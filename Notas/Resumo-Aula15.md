# Resumo — Aula 15: Tri-State e Memória

## 1. Tri-State — O Terceiro Estado

Circuitos digitais normais têm dois estados de saída: **0** (baixo) e **1** (alto). Circuitos **tri-state** acrescentam um terceiro estado: **Alta Impedância (High-Z)**, onde a saída fica eletricamente desconectada do barramento.

```
Estados possíveis:
  0  → nível lógico baixo (0 V)
  1  → nível lógico alto (5 V)
  Z  → alta impedância (desconectado — "invisível" no barramento)
```

> **Por que isso é necessário?** Em um barramento compartilhado, múltiplos dispositivos têm suas saídas ligadas ao mesmo fio. Se dois dispositivos tentarem colocar valores diferentes ao mesmo tempo → **curto-circuito lógico**. Com tri-state, apenas um fala; os outros ficam em High-Z.

O controle é feito por um pino **Enable (E)** ou **Output Enable (OE)**:

| A | E (enable) | Saída |
|---|-----------|-------|
| 0 | 0 | High Z |
| 1 | 0 | High Z |
| 0 | 1 | 1 (buffer inversor) |
| 1 | 1 | 0 (buffer inversor) |

---

## 2. Buffers Tri-State Unidirecionais

Três variantes da família 74LS com 8 canais cada (2 grupos de 4):

| CI | Inversão | Enable |
|----|----------|--------|
| **74LS240** | Inversor (saída = Ā) | Ativo em LOW (G̅) |
| **74LS241** | Sem inversão (saída = A) — grupo 1 tem G̅, grupo 2 tem G | Misto |
| **74LS244** | Sem inversão (saída = A) | Ativo em LOW (G̅) |

Todos possuem **dois grupos de 4 buffers** com enable independente, permitindo controlar metade de um barramento de 8 bits de cada vez.

---

## 3. Buffer Tri-State Bidirecional — 74LS245

O **74LS245** é um transceptor de 8 bits: permite que o dado flua nos **dois sentidos** (A→B ou B→A) dependendo do sinal **DIR**, e pode ser isolado com **OE̅**.

**Pinos de controle:**
- **OE̅** (Output Enable, ativo em LOW): habilita o buffer
- **DIR** (Direction): controla o sentido do fluxo

**Tabela de Operação:**

| OE̅ | DIR | Operação |
|-----|-----|----------|
| L | L | Dado de **B** para **A** |
| L | H | Dado de **A** para **B** |
| H | X | **Isolamento** (High-Z em ambos os lados) |

**Aplicação típica:** conectar CPU (master) a periférico (slave) via cabo flat ou backplane. Cada lado tem seu próprio 74LS245; o sinal DIR determina quem está enviando.

```
CPU ──[74LS245]── Barramento ──[74LS245]── Periférico
      DIR=H→               ←DIR=L
```

---

## 4. Memória Semicondutora — Classificação

```
Memória
├── Não Volátil (mantém dado sem alimentação)
│   ├── ROM     — gravada na fabricação, só leitura
│   ├── PROM    — programável uma vez pelo usuário (fusíveis)
│   └── EPROM   — apagável e reprogramável
│       ├── UvEPROM  — apaga com luz ultravioleta (janela de quartzo)
│       ├── EEPROM   — apaga eletricamente, byte a byte
│       └── FLASH    — apaga eletricamente, em blocos (mais rápido)
│
└── Volátil (perde dado sem alimentação)
    └── RAM     — Random Access Memory (leitura e escrita)
        ├── SRAM — dado armazenado em latch (FF)
        │         → rápida, cara, consome mais área
        └── DRAM — dado armazenado como carga em capacitor
                  → necessita refresh periódico, densa, barata
```

---

## 5. Sinais de Controle de Memória

Todo chip de memória possui (além das linhas de endereço e dados):

| Sinal | Nome | Função |
|-------|------|--------|
| **WE̅** | Write Enable | LOW = escreve dado; HIGH = leitura |
| **CS̅** | Chip Select | LOW = chip habilitado; HIGH = chip em High-Z |
| **OE̅** | Output Enable | LOW = coloca dado no barramento; HIGH = saída em Hi-Z |

O pino **I/O** das memórias RAM é bidirecional (tri-state): durante escrita recebe dados do barramento; durante leitura os envia.

---

## 6. Diagrama Interno de uma Memória

```
          Linhas de Endereço
                 ↓
        ┌─────────────────┐
        │  Decodificador  │ ← seleciona UMA linha (word line)
        │   de Linha      │
        └────────┬────────┘
                 ↓
        ┌─────────────────────────────┐
        │   Arranjo de Memória        │
        │   256 linhas × 128 colunas  │
        │         × 8 bits            │
        └────────────┬────────────────┘
                     ↓
             I/O de Coluna ← seleciona qual coluna ler
                     ↓
             Buffers Tri-State de Saída → Dados (D0–D7)
                              ↑
                         CS̅, WE̅, OE̅
```

---

## 7. Organização de Memória — Notação N × M

A notação **N × M** indica:
- **N** = número de posições (endereços)
- **M** = largura de cada posição (bits por endereço)
- Total de bits = N × M

**Regra para linhas de endereço:** N posições → **log₂(N)** bits de endereço

| Memória | Posições | Bits/posição | Total bits | Endereços | Dados |
|---------|----------|-------------|-----------|-----------|-------|
| **1K × 8** | 1024 | 8 | 8192 bits | A0–A9 (10 bits) | I/O0–I/O7 (8 bits) |
| **8K × 1** | 8192 | 1 | 8192 bits | A0–A12 (13 bits) | I/O0 (1 bit) |
| **4K × 8** | 4096 | 8 | 32768 bits | A0–A11 (12 bits) | I/O0–I/O7 (8 bits) |

> As duas primeiras armazenam o mesmo total (8k bits) com organizações diferentes!

---

## 8. Banco de Memória — Expandindo Capacidade

### Caso 1: Expandir o número de endereços (mais linhas)

**Problema:** construir 4K×8 com chips de 1K×8.

- Precisa de 4 chips (4 × 1K = 4K posições)
- Os 10 bits baixos de endereço (A0–A9) vão para **todos os chips** igualmente
- Os 2 bits altos (A10–A11) selecionam **qual chip** via **decodificador 74LS139** → CS̅ de cada chip
- As linhas de dados (D0–D7) de todos os chips são conectadas ao mesmo barramento (saídas tri-state — apenas o chip selecionado fala)

```
A0–A9  → todos os chips (1K×8) em paralelo
A10–A11 → 74LS139 → CS̅ chip0 (A10A11=00)
                   → CS̅ chip1 (A10A11=01)
                   → CS̅ chip2 (A10A11=10)
                   → CS̅ chip3 (A10A11=11)
D0–D7  ← barramento compartilhado (tri-state)
```

### Caso 2: Expandir a largura de dados (mais bits por posição)

**Problema:** construir 4K×8 com chips de 4K×1.

- Precisa de **8 chips** (8 × 1 bit = 8 bits por posição)
- **Todos os chips** recebem os mesmos endereços (A0–A11) e o mesmo CS̅
- Cada chip fornece **1 bit** do dado: chip0 → D0, chip1 → D1, ..., chip7 → D7

```
A0–A11 → 8 chips (4K×1) em paralelo (mesmo endereço)
           chip0.IO → D0
           chip1.IO → D1
           ...
           chip7.IO → D7
```

### Caso 3: Com buffer 74LS245 no barramento de dados

Adiciona um **74LS245** entre a memória e o barramento externo para:
- **Isolamento** elétrico (proteção contra corrente excessiva)
- **Direção controlável**: DIR=H (leitura: memória→CPU), DIR=L (escrita: CPU→memória)
- **Enable via OE̅** do 74LS245, sincronizado com o acesso à memória

---

## 9. Mapeamento de Endereços (Address Mapping)

Um banco de memória pode ocupar diferentes regiões do espaço de endereçamento de um sistema. Chaves físicas (CH0, CH1) selecionam qual região:

**Exemplo: Banco 4K×8 em sistema com 16K (A0–A13)**

Os bits **A12, A13** determinam a região; A0–A11 são o endereço interno:

| CH1 CH0 | A13 A12 | Região |
|---------|---------|--------|
| 0 0 | 0 0 | 0000h – 0FFFh |
| 0 1 | 0 1 | 1000h – 1FFFh |
| 1 0 | 1 0 | 2000h – 2FFFh |
| 1 1 | 1 1 | 3000h – 3FFFh |

As chaves CH0 e CH1 são comparadas com A12 e A13: CS̅ é ativado apenas quando há coincidência.

---

## 10. Conexão com Outras Aulas

- **Aula 6 (Decodificadores 74LS139):** É o elemento central da seleção de chips no banco de memória — cada saída ativa o CS̅ de um chip diferente
- **Aula 5 (MUX):** O I/O bidirecional da memória funciona como um MUX controlado por WE̅
- **Aula 7 (FF's/Latch):** A célula SRAM é implementada com um latch SR — o FF mais básico do curso
- **Aula 14 (Tri-State/74LS245):** O buffer bidirecional é usado aqui para isolar o barramento de dados da memória

---

## Resumo Visual

```
Tri-State: 0 | 1 | Z (High-Z)
                          ↑
             Permite barramento compartilhado

74LS244 → unidirecional, sem inversão, G̅ habilita
74LS245 → bidirecional, DIR controla sentido, OE̅ habilita

Memória:
  NxM → N endereços, M bits/endereço
  log₂(N) bits de endereço necessários

Banco:
  Mais endereços → chips em série (CS diferente, dados ligados)
  Mais bits      → chips em paralelo (CS igual, dados separados)
```
