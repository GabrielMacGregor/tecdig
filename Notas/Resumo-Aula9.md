# Resumo — Aula 9: Registradores de Deslocamento (Shift Registers)

## 1. Registrador Paralelo

Um **registrador paralelo de N bits** é simplesmente N FF D com o mesmo clock:
- Entradas: P_A, P_B, P_C, P_D (dados paralelos)
- Saídas: Q_A, Q_B, Q_C, Q_D
- No pulso de clock: todos os bits são capturados **simultaneamente**

> Usado como "buffer" — captura o estado de um barramento de dados num instante preciso.

---

## 2. Registrador de Deslocamento (Shift Register)

Um **shift register** é uma cadeia de FF D em série: a saída Q de cada FF alimenta a entrada D do próximo. No pulso de clock, a informação avança um estágio.

### 4 tipos e suas aplicações

| Tipo | Entrada | Saída | Aplicação típica |
|------|---------|-------|-----------------|
| **Série/Paralelo** | 1 bit serial | N bits paralelos | Recepção de dados seriais (ex. UART) |
| **Série/Série** | 1 bit serial | 1 bit serial | Linha de atraso (delay line) |
| **Paralelo/Série** | N bits paralelos | 1 bit serial | Transmissão de dados seriais |
| **Paralelo/Paralelo** | N bits paralelos | N bits paralelos | Armazenamento / buffer |

### Como funciona o deslocamento (série/série)

Com 4 FF D em cascata e entrada serial:

```
Data In → [FF1] → [FF2] → [FF3] → [FF4] → saída
           Q1       Q2       Q3       Q4
Clock comum a todos
```

- A cada pulso de clock: bit em Q1 vai para Q2, Q2 para Q3, Q3 para Q4
- O bit da entrada serial entra em Q1
- Após 4 pulsos: o bit que entrou está em Q4 (atraso de 4 ciclos)

### Conversão Paralelo → Série (Paralelo/Série)

1. **Fase de carga (Load):** usar as entradas PR (Preset) dos FFs para forçar os valores paralelos em Q1–Q4 simultaneamente (sem clock)
2. **Fase de deslocamento (Shift):** aplicar N pulsos de clock — os bits saem em sequência pela saída do último FF

**Sinais necessários:**
- **Enable** (ou SH/LD̄): controla se está em modo carga ou modo shift
- **Clear:** zera todos os FFs antes de carregar novo dado
- **Clock:** move os bits a cada pulso

---

## 3. CI 74165 — Registrador Paralelo/Série de 8 bits

O **74165** converte 8 bits paralelos em série (Parallel-In / Serial-Out).

### Pinos principais

| Pino | Função |
|------|--------|
| A–H | Entradas de dados paralelos (8 bits) |
| SH/LD̄ | Shift/Load̄ — controla o modo |
| CLK | Clock |
| CLK INH | Clock Inhibit — inibe o clock quando H |
| SER | Entrada serial (para cascatear 74165) |
| QH | Saída serial (bit mais significativo sai primeiro) |
| Q̄H | Saída serial complementada |

### Tabela de funcionamento

| SH/LD̄ | CLK INH | CLK | SER | Ação |
|--------|---------|-----|-----|------|
| L | X | X | X | **Carga paralela** (A–H carregados nos FFs) |
| H | L | ↑ | H | Shift — entra 1 serial em QA, desloca tudo |
| H | L | ↑ | L | Shift — entra 0 serial em QA, desloca tudo |
| H | H | X | X | Saída congelada (clock inibido) |

### Sequência de operação (Paralelo → Série)

1. SH/LD̄ = 0 → dado paralelo (A–H) é carregado nos FFs
2. SH/LD̄ = 1, CLK INH = 0 → aplicar 8 pulsos de clock
3. QH emite os bits em sequência: A primeiro, H por último

### Cascateamento de 74165

Para processar mais de 8 bits: ligar **QH do primeiro** à entrada **SER do segundo**, com o mesmo CLK e SH/LD̄.

---

## 4. Conversão Série → Paralelo

O processo inverso — receber bits um a um e disponibilizá-los todos ao mesmo tempo:

```
Data In → [FF1] → [FF2] → [FF3] → [FF4]
           Q1       Q2       Q3       Q4
```

Após N pulsos de clock com os N bits seriais chegando em Data In:
- Q1 contém o último bit recebido
- Q4 contém o primeiro bit recebido
- Ler Q1–Q4 em paralelo = dado completo reconstruído

---

## 5. Resumo visual dos 4 tipos

```
SÉRIE/PARALELO:
  bit  bit  bit  bit        → Q1 Q2 Q3 Q4 (lidos juntos após N pulsos)

SÉRIE/SÉRIE:
  bit  bit  bit  bit  bit   → delay de N ciclos na saída

PARALELO/SÉRIE:
  [D3 D2 D1 D0] carregados  → saem bit a bit em N pulsos

PARALELO/PARALELO:
  [D3 D2 D1 D0] → [Q3 Q2 Q1 Q0] (captura simultânea no clock)
```

---

## 6. Pontos-chave para a prova

1. **Shift register** = FFs D em cascata com clock comum
2. **Paralelo/Série:** Load primeiro (via PR/CLR), depois Shift com N pulsos — usado para **transmitir** dados serialmente
3. **Série/Paralelo:** dados entram um a um e são lidos todos juntos ao final — usado para **receber** dados seriais
4. **Série/Série:** atrasa o sinal em N ciclos de clock (N = número de FFs)
5. **74165:** pino SH/LD̄ — 0 = carga paralela, 1 = shift; CLK INH = 1 congela a saída
6. A saída serial do 74165 emite **8 bits** após **8 pulsos** de clock (MSB primeiro)
7. Para cascatear 74165: QH → SER do próximo, CLK e SH/LD̄ compartilhados

---

## Conexão com as próximas aulas

- **Aulas 10–11**: Projeto sistemático de circuitos sequenciais — aplica o conceito de "estado" que os FFs armazenam
- **Aulas 14**: Registradores e teclados matriciais
- **Aula 16**: Pilhas e filas — estruturas que usam registradores e endereçamento
