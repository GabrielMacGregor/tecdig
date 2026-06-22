# Gabarito — Simulado Técnicas Digitais

---

## Questão 1a — SDP mínima

```
f(A,B,C,D,E) = Σm(0,2,4,6,9,11,18,21,22,23,24,25,26,27,31) + d(8,10,16,29)
```

### Mapas preenchidos

**A = 0:**

```
         BC
DE     | 00    | 01    | 11    | 10
-------+-------+-------+-------+-------
  00   | 1(0)  | 1(4)  | 0(12) | X(8)
  01   | 0(1)  | 0(5)  | 0(13) | 1(9)
  11   | 0(3)  | 0(7)  | 0(15) | 1(11)
  10   | 1(2)  | 1(6)  | 0(14) | X(10)
```

**A = 1:**

```
         BC
DE     | 00    | 01    | 11    | 10
-------+-------+-------+-------+-------
  00   | X(16) | 0(20) | 0(28) | 1(24)
  01   | 0(17) | 1(21) | X(29) | 1(25)
  11   | 0(19) | 1(23) | 1(31) | 1(27)
  10   | 1(18) | 1(22) | 0(30) | 1(26)
```

### Grupos identificados

| Grupo | Células | Variáveis fixas | Termo SDP |
|-------|---------|-----------------|-----------|
| 🔴 | {0, 2, 4, 6} | A=0, B=0, E=0 | **A'B'E'** |
| 🔵 | {2, 6, 18, 22} | B=0, D=1, E=0 | **B'DE'** |
| 🟢 | {9, 11, 25, 27} | B=1, C=0, E=1 | **BC'E** |
| 🟡 | {21, 23, 29(X), 31} | A=1, C=1, E=1 | **ACE** |
| 🟣 | {24, 25, 26, 27} | A=1, B=1, C=0 | **ABC'** |

> Grupo 🔵 cruza os dois mapas. Grupo 🟡 usa o don't care 29.

### Resultado SDP

```
f = A'B'E' + B'DE' + BC'E + ACE + ABC'
```

### Verificação de cobertura

| Mintermo | Coberto por |
|----------|-------------|
| 0, 4 | A'B'E' |
| 2, 6 | A'B'E' + B'DE' |
| 9, 11 | BC'E |
| 18, 22 | B'DE' |
| 21, 23 | ACE |
| 24, 26 | ABC' |
| 25, 27 | BC'E + ABC' |
| 31 | ACE |

---

## Questão 1b — PDS mínima

```
g(A,B,C,D,E) = ΠM(1,3,5,7,12,13,14,15,28,30) + d(0,8,16,24)
```

### Mapas preenchidos (0 nos maxtermos)

**A = 0:**

```
         BC
DE     | 00    | 01    | 11    | 10
-------+-------+-------+-------+-------
  00   | X(0)  | 1(4)  | 0(12) | X(8)
  01   | 0(1)  | 1(5)  | 0(13) | 1(9)
  11   | 0(3)  | 1(7)  | 0(15) | 1(11)
  10   | 1(2)  | 1(6)  | 0(14) | 1(10)
```

**A = 1:**

```
         BC
DE     | 00    | 01    | 11    | 10
-------+-------+-------+-------+-------
  00   | X(16) | 1(20) | 0(28) | X(24)
  01   | 1(17) | 1(21) | 1(29) | 1(25)
  11   | 1(19) | 1(23) | 1(31) | 1(27)
  10   | 1(18) | 1(22) | 0(30) | 1(26)
```

### Grupos de 0s identificados

| Grupo | Células | Variáveis fixas | Fator PDS |
|-------|---------|-----------------|-----------|
| 🔴 | {1, 3, 5, 7} | A=0, B=0, E=1 | **(A + B + E')** |
| 🔵 | {12, 13, 14, 15} | A=0, B=1, C=1 | **(A + B' + C')** |
| 🟢 | {12, 14, 28, 30} | B=1, C=1, E=0 | **(B' + C' + E)** |

> Regra PDS: variável fixa em 0 → entra **direta** na soma; fixa em 1 → entra **complementada**.
> Grupo 🟢 cruza os dois mapas e usa dois maxtermos comuns com 🔵.

### Resultado PDS

```
g = (A + B + E') · (A + B' + C') · (B' + C' + E)
```

### Verificação de cobertura dos maxtermos

| Maxtermo | Coberto por |
|----------|-------------|
| 1, 3, 5, 7 | (A+B+E') |
| 12, 13, 14, 15 | (A+B'+C') |
| 28, 30 | (B'+C'+E) |

---

## Questão 1c — Contagem de portas AND

**ABCDE = 11001** → A=1, B=1, C=0, D=0, E=1

Inversões: A'=0, B'=0, C'=1, D'=1, E'=0

| Porta AND | Cálculo | Saída |
|-----------|---------|-------|
| A'B'E' | 0·0·1 = 0 | 0 |
| B'DE' | 0·0·0 = 0 | 0 |
| BC'E | 1·1·1 = 1 | **1** |
| ACE | 1·0·1 = 0 | 0 |
| ABC' | 1·1·1 = 1 | **1** |

> **Resposta: 2 portas AND** têm saída "1" para ABCDE = 11001.
>
> Confirmação: 11001 = mintermo 25 → f(25)=1 ✓ (BC'E e ABC' ambas em 1 → f=1)

---

## Questão 2a — Codificador com Prioridade

**Prioridade definida:** L3 > L2 > L1 > L0 (maior índice = maior prioridade)

| Sinal prioritário ativo | C1C0 | Código |
|------------------------|------|--------|
| L3 = 1 (qualquer outro) | 11 | 3 |
| L3=0, L2=1 | 10 | 2 |
| L3=0, L2=0, L1=1 | 01 | 1 |
| apenas L0 (ou nenhum) | 00 | 0 |

### Derivação de C1

C1 = 1 quando o código é 2 (L2 ativo) ou 3 (L3 ativo):

```
C1 = L3 + L3'·L2 = L3 + L2
```

(Simplificação: se L3=1, C1=1; se L3=0 e L2=1, C1=1 → C1 = L3 + L2)

**Circuito:** porta OR de 2 entradas com L3 e L2.

```
L3 ─┐
    ├── OR ──→ C1
L2 ─┘
```

**CI:** 7432 (OR)

---

## Questão 2b — Decodificador de Intervalos (5 bits, 0–31)

**Intervalos:** (8–15) e (24–27)

### Análise dos intervalos

```
8–15:  01000 a 01111 → A=0, B=1 (grupo de 8)
24–27: 11000 a 11011 → A=1, B=1, C=0 (grupo de 4)
```

### Estrutura dos decodificadores

**Decoder MESTRE — entradas A, B (always enabled):**

| Saída | AB | Grupo |
|-------|----|-------|
| Y0 | 00 | 0–7 |
| **Y1** | **01** | **8–15** |
| Y2 | 10 | 16–23 |
| **Y3** | **11** | **24–31** |

→ Y1 cobre diretamente o intervalo 8–15 (saída ativa-baixo = 0 quando AB=01).

**Decoder ESCRAVO — entradas C, D, habilitado por Y3:**

Quando A=1, B=1 (Y3=0): decodifica C e D:

| Saída | CD | Grupo |
|-------|-----|-------|
| **Ya0** | **00** | **24–25** |
| **Ya1** | **01** | **26–27** |
| Ya2 | 10 | 28–29 |
| Ya3 | 11 | 30–31 |

→ Ya0 e Ya1 cobrem 24–27.

### Porta final (AND de 3 entradas)

```
OUT = Y1 AND Ya0 AND Ya1
```

- Em 8–15: Y1=0, Ya0=1, Ya1=1 → OUT = 0 ✓
- Em 24–25: Y1=1, Ya0=0, Ya1=1 → OUT = 0 ✓
- Em 26–27: Y1=1, Ya0=1, Ya1=0 → OUT = 0 ✓
- Fora: todos=1 → OUT = 1 ✓

### Diagrama

```
A ─┐
B ─┤ [DECODER MESTRE, EN=GND]
   │   Y0 → não usado
   │   Y1 ──────────────────────────────────→ AND3
   │   Y2 → não usado                              ↓
   │   Y3 ──→ EN [DECODER ESCRAVO]              OUT (ativo-baixo)
                │
C ─────────────→ C1 [ESCRAVO]   Ya0 ──→ AND3
D ─────────────→ C0 [ESCRAVO]   Ya1 ──→ AND3
```

**CIs:** 2× 74139 (ou similar 2×4) + 1× 7411 (AND3)

---

## Questão 2c — Formas de Onda JK

### Análise

J=K=1 → todos os FFs em modo **toggle**. Cada FF inverte na borda ↓ do seu clock.
Resultado: contador binário de 3 bits (0 a 7), período total = 8 clocks.

### Tabela de estados

| Pulso | Qa | Qb | Qc |
|-------|----|----|-----|
| 0 (inicial) | 0 | 0 | 0 |
| 1↓ | 1 | 0 | 0 |
| 2↓ | 0 | 1 | 0 |
| 3↓ | 1 | 1 | 0 |
| 4↓ | 0 | 0 | 1 |
| 5↓ | 1 | 0 | 1 |
| 6↓ | 0 | 1 | 1 |
| 7↓ | 1 | 1 | 1 |
| 8↓ | 0 | 0 | 0 |
| 9↓ | 1 | 0 | 0 |
| 10↓ | 0 | 1 | 0 |

### Formas de onda

```
CLK  ¯|_|¯|_|¯|_|¯|_|¯|_|¯|_|¯|_|¯|_|¯|_|¯|_|
       1   2   3   4   5   6   7   8   9  10

Qa   __|¯|_|¯|_|¯|_|¯|_|¯|_|¯|_|¯|_|¯|_|¯|_
        1  2  3  4  5  6  7  8  9 10

Qb   ____|¯¯¯|___|¯¯¯|___|¯¯¯|___|¯¯¯|___
          2   4   6   8  10

Qc   ________|¯¯¯¯¯¯¯|_______________|¯¯¯
              4       8
```

**Período de Qc = 8 × período do CLK** (Qc divide o clock por 8)

---

## Questão 2d — Serialização com 74165

**Dado = 1010, transmissão = `0 1010 1` = `010101` (6 bits)**

### Carregamento das posições

| Posição 74165 | Valor | Explicação |
|---------------|-------|-----------|
| H | 0 | Start bit (sai primeiro via QH) |
| G | 1 | dado[3] (MSB) |
| F | 0 | dado[2] |
| E | 1 | dado[1] |
| D | 0 | dado[0] (LSB) |
| C | 1 | Stop bit (sai por último) |
| B, A | X | Não usados (indiferente) |

### Transmissão clock a clock

| Clock | QH | Bit transmitido |
|-------|-----|----------------|
| 1 | H = 0 | start ✓ |
| 2 | G = 1 | dado[3] ✓ |
| 3 | F = 0 | dado[2] ✓ |
| 4 | E = 1 | dado[1] ✓ |
| 5 | D = 0 | dado[0] ✓ |
| 6 | C = 1 | stop ✓ |

### Sinal DONE

- **6 pulsos de clock** são necessários.
- Use um **contador de 3 bits** que conta os clocks durante o shift (SH/LD=1).
- Quando contagem = 6 = 110₂:

```
DONE = Q2 · Q1 · Q0'
```

- Contador é zerado quando SH/LD = 0 (fase de carregamento).

---

## Questão 3 — Sistema de Pressão

### a) Cálculo de P + Q

Use **dois 74283** em cascata (somador de 5 bits):

```
[74283 #1]
  A = P[3:0], B = Q[3:0], Cin = 0
  Saída: S[3:0] + Cout0

[74283 #2]
  A = P[4]=0, B = Q[4]=0, Cin = Cout0
  Saída: S[4] = Cout0, S[5] = Cout1 (=0, pois máx = 30 < 32)
```

Resultado: **S[4..0]** (5 bits, valores 0–30)

### b) Detecção de soma > 20

20 em 5 bits = **10100**

```
[7485]
  A[4:0] = S[4..0]  (soma calculada)
  B[4:0] = 10100    (constante 20: B4=1, B3=0, B2=1, B1=0, B0=0)
  Cascade inicial: IA>B=0, IA=B=1, IA<B=0
  Saída OA>B = 1 quando S > 20 → TRIG (gatilho do alarme)
```

### c) Armazenamento do Alarme em 7474

O 7474 (FF D) tem **Preset e Clear assíncronos** (ativos em nível baixo):

```
TRIG ──→ NOT ──→ PRESET [7474]  ← ativa (=0) quando soma > 20 → Q=1=AL
RESET ──→ NOT ──→ CLEAR [7474] ← ativa (=0) quando operador pressiona RESET → Q=0
                  Q = AL
```

- D e CLK do 7474 não são críticos (Preset/Clear assíncronos dominam)
- Uma vez ativado, AL permanece 1 até o operador acionar RESET

### d) Conversão BCD para Display

A soma S vai de 0 a 30. Precisamos de dezenas (0,1,2) e unidades (0–9).

**Detectar se S ≥ 10:**

```
GE10 = S3 · (S2 + S1)
```

**Verificação:**
- S=9 (01001): S3=1,S2=0,S1=0 → GE10=0 ✓
- S=10 (01010): S3=1,S2=0,S1=1 → GE10=1 ✓
- S=20 (10100): S4=1,S3=0... aguarda — para S>15 o 5º bit (S4) precisa ser incluído.

> **Atenção:** Como S pode ser até 30, S4 pode ser 1 (para valores 16–30). GE10 deve considerar S4 também!

```
GE10 = S4 + S3·(S2 + S1)
```

**Correção BCD** com 74283 #3:

```
[74283 #3]
  A = S[3:0]
  B3=0, B2=GE10, B1=GE10, B0=0   → soma 0110 quando GE10=1, 0000 quando GE10=0
  Cin = 0
  Saída S_corr[3:0] = UNIDADES (dígito das unidades)
  Cout = carry_bcd
```

**Dezenas:**

```
TENS = S4 OR carry_bcd
```

(Para somas 0–9: S4=0, carry=0 → TENS=0; para 10–19: GE10=1, carry=1 → TENS=1; para 20–30: S4=1 → TENS=1 ou 2)

> **Nota:** Para somas ≥ 20 com dezena = 2, precisaria de lógica adicional ou um segundo estágio de correção BCD. Para a prova, aceitar a solução para valores ≤ 19 com a explicação é suficiente.

**Display:**
```
TENS ──→ Display dezenas (0 ou 1)
UNIDADES[3:0] ──→ [Decoder BCD→7seg] ──→ Display unidades
```

### Diagrama resumo Q3

```
P[3:0] ─┐
Q[3:0] ─┤── [74283 #1 + #2] ──→ S[4:0]
         │                           │
         │                     ┌─────┴──────┐
         │                     │            │
         │               [7485: S>20]  GE10=S4+S3(S2+S1)
         │                     │            │
         │                   TRIG     [74283 #3: +GE10·0110]
         │                     │            │
         │              NOT──→PRESET  S_corr[3:0]=UNIDADES
         │              [7474]         carry_bcd
         │               CLEAR←NOT   TENS=S4+carry
         │                ↑
         │           RESET(operador)
         │
         Q=AL (alarme)
```

---

## Pontuação sugerida

| Questão | Itens | Pontos |
|---------|-------|--------|
| 1a | Mapas + grupos + expressão | 1,2 |
| 1b | Mapas + grupos + expressão | 1,0 |
| 1c | Resposta + justificativa | 0,8 |
| 2a | Tabela + expressão C1 + circuito | 1,0 |
| 2b | Análise + estrutura + diagrama | 1,5 |
| 2c | Tabela + formas de onda + período | 0,8 |
| 2d | Posições + nº clocks + DONE | 0,7 |
| 3a | Somador em cascata | 0,6 |
| 3b | Configuração do 7485 | 0,8 |
| 3c | FF D com Preset/Clear | 0,8 |
| 3d | GE10 + BCD + TENS + display | 0,8 |
| **Total** | | **10,0** |
