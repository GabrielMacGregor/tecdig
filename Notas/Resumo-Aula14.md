# Resumo — Aula 14: Monoestável e Teclado

## 1. Monoestável — Conceito

Um **monoestável** é um dispositivo com **apenas um estado estável** (Q=0). Ao receber um disparo (trigger), passa ao estado quase-estável (Q=1) por um período **tw** predeterminado, e então retorna ao estado estável sozinho, sem estímulo externo.

```
Trigger ─┐
         ↓
Q: 0 ──────┌──────┐──── 0
           |  tw  |
           └──────┘
```

**tw** é controlado por um resistor externo (R) e um capacitor externo (C):

> **tw = 0,7 × Cext × RT**  (para o 74121)

Principal aplicação: **circuitos temporizadores**.

---

## 2. Tipos de Monoestável

### Não Retrigável — 74121

- Uma vez disparado, **ignora novos triggers** enquanto tw está contando
- O pulso de saída tem sempre duração fixa tw a partir do primeiro disparo

```
Disparo: __|‾|___|‾|___|‾|___
Q:       ____|‾‾‾‾‾|__|‾‾‾‾‾|__
              ←tw→  (2º disparo ignorado se cair dentro de tw)
```

### Retrigável — 74123

- Cada novo trigger **reinicia a contagem** tw do zero
- O pulso de saída pode ser **estendido** enquanto chegarem triggers

```
Trigger: __|‾|__|‾|______________|‾|__
Q:       ____|‾‾‾‾‾‾‾‾‾‾‾‾‾‾‾‾‾|___
              ←    tw + tw    →
              (retrigado no meio → reinicia tw)
```

| Característica | Não Retrigável (74121) | Retrigável (74123) |
|----------------|------------------------|---------------------|
| Novo trigger durante tw | **Ignorado** | **Reinicia** tw |
| Pulso máximo | tw fixo | tw acumulado |
| Uso típico | Pulso preciso único | Extensão dinâmica de pulso |

---

## 3. CI 74121 — Não Retrigável

**Fórmula do tempo de saída:**

```
tw = 0,7 × Cext × RT
```
- RT: resistor de temporização (1,4 kΩ – 40 kΩ)
- Cext: capacitor externo (até 1000 µF)
- Possui também resistor interno (Rint ≈ 2 kΩ) — pode ser usado sem Rext

**Pinos principais:**
- A1, A2 (entradas ativas em LOW), B (entrada ativa em HIGH)
- Q, Q̄ (saídas complementares)
- Rext/Cext, Cext, Rint (componentes de temporização)

**Tabela de Função (resumo):**

| A1 | A2 | B | Q |
|----|----|---|---|
| L  | X  | H | L (não dispara) |
| X  | L  | H | L (não dispara) |
| H  | H  | X | L (não dispara) |
| H  | ↓  | H | **Dispara** (borda de descida em A) |
| ↓  | H  | H | **Dispara** (borda de descida em A1) |
| L  | X  | ↑ | **Dispara** (borda de subida em B) |

> Disparo ocorre na **borda de descida** de A1 ou A2, ou na **borda de subida** de B.

---

## 4. CI 74123 — Retrigável

**Pinos:** A (ativo em LOW), B (ativo em HIGH), CLR̄ (clear assíncrono), Q, Q̄

**Tabela de Função:**

| CLR̄ | A | B | Q |
|------|---|---|---|
| L   | X | X | L (clear imediato) |
| H   | H | X | L (sem disparo) |
| H   | X | L | L (sem disparo) |
| H   | L | ↑ | **Dispara** |
| H   | ↓ | H | **Dispara** |
| ↑   | L | H | **Dispara** |

**Funcionalidade extra — CLEAR:**
- CLR̄=0 a qualquer momento cancela o pulso e força Q=0 imediatamente
- Útil para **cancelar** o temporizador antes de tw terminar

---

## 5. Aplicações do Monoestável

| Aplicação | Como funciona |
|-----------|--------------|
| **Temporizador** | Gera um pulso de duração precisa tw após um evento |
| **Oscilador** | Saída Q̄ realimentada no trigger → oscilação livre com período ≈ 2·tw |
| **Anti-bounce (debounce)** | Dispara na 1ª borda do bounce; não retrigável ignora os bounces seguintes durante tw |
| **Watchdog Timer (HW)** | Sistema saudável "peta" o mono periodicamente; se parar, o mono expira e gera reset |
| **Verificação de falha** | Detecta ausência de pulsos (sinal que deveria chegar dentro de tw não chega → alarme) |

### Debounce com monoestável (aplicação importante):

```
Tecla pressionada → bounces elétricos → trigger dispara mono
Mono (não retrigável): ignora os bounces durante tw
Saída Q: pulso limpo e único, sem ruído
```

---

## 6. Teclado Matricial

### Estrutura 4×4

Um teclado matricial organiza N teclas em uma **grade de linhas × colunas**, reduzindo o número de fios. Para 16 teclas (0–F):

```
     Col0  Col1  Col2  Col3
Lin0: [0]   [1]   [2]   [3]
Lin1: [4]   [5]   [6]   [7]
Lin2: [8]   [9]   [A]   [B]
Lin3: [C]   [D]   [E]   [F]
```

- 4 linhas + 4 colunas = **8 fios** (em vez de 16 fios individuais)
- Cada tecla é um chave que conecta uma linha a uma coluna quando pressionada
- Resistores de pull-up (1 kΩ) em cada linha mantêm nível alto quando nenhuma tecla está pressionada

### Princípio de Varredura

O circuito coloca um nível LOW em **uma coluna por vez** (varrendo 0111 → 1011 → 1101 → 1110) e lê as **quatro linhas** a cada passo:

```
Passo 1: Col = 0111 → lê Lin: se Lin0=0 → tecla 0 pressionada
Passo 2: Col = 1011 → lê Lin: se Lin1=0 → tecla 5 pressionada
Passo 3: Col = 1101 → lê Lin: ...
Passo 4: Col = 1110 → lê Lin: ...
```

A leitura das linhas vai para um **MUX** que, controlado pelo mesmo contador que gera a varredura, seleciona a linha correta.

---

## 7. Circuito Completo de Varredura e Decodificação

**Componentes:**

| Bloco | CI | Função |
|-------|----|--------|
| Gerador de clock | Oscilador / monoestável | Clock para o contador |
| Contador de varredura | **74LS293** (módulo 4) | Gera endereços de coluna (00→01→10→11) |
| Decodificador de coluna | 74LS138 ou lógica | Converte 2 bits → sinal ativo baixo numa coluna |
| Leitura das linhas | Resistores pull-up + fios | Detecta tecla fechada |
| Multiplexador | **MUX 4:1** | Seleciona qual linha ler conforme a coluna ativa |
| Decodificador de saída | 74LS153 ou similar | Converte posição (linha+coluna) em código da tecla |
| Display | 7 segmentos | Exibe o valor da tecla |

**Fluxo de funcionamento:**

```
Clock → Contador (74LS293) → código 2 bits (Q1Q0)
                ↓                          ↓
         Varredura de colunas      Seleção do MUX
         (0111→1011→1101→1110)    (lê linha correta)
                          ↓
                 Linha pressionada? → MUX detecta nível 0
                          ↓
              (col, lin) → Endereço da tecla
                          ↓
                  Decodificador → Saída binária
                          ↓
                    Display 7 segmentos
```

**Por que MUX?** (conexão com Aula 5)
- O MUX recebe as 4 linhas como entradas de dados
- O endereço do MUX = código da coluna atual
- Saída = estado da linha para aquela coluna → detecta a tecla

---

## 8. Conexão com Outras Aulas

- **Aula 4 (Don't Care):** Os don't cares do debounce — estados intermediários do bounce são "don't cares" funcionais
- **Aula 5 (MUX):** O MUX é o elemento central da leitura do teclado matricial
- **Aula 8 (Contadores 74293):** O contador 74LS293 gera o endereço de varredura das colunas
- **Aula 6 (Decodificadores):** Pode-se usar um decodificador 74138 para ativar uma coluna por vez (saída ativa em LOW)
- **Aula 7 (FF's):** O monoestável pode ser visto como um "FF com temporização" — um estado quasi-estável controlado por RC
