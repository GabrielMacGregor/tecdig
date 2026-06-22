# Lab 3 — MUX 8x1, Decoder 4x16 e Somador com Conversão BCD

---

## Parte 1 — MUX 8×1 usando 74157

### O que é o 74157

O **74157** é um CI com **4 MUX 2×1** dentro, todos compartilhando **1 linha de seleção (S)**:

```
Se S=0: saída Y = entrada A
Se S=1: saída Y = entrada B
```

### Ideia: árvore de 3 níveis

Para selecionar 1 de 8 entradas precisamos de **3 bits de seleção** (S2, S1, S0):

```
8 entradas ──[Nível 1: S0]──→ 4 saídas
4 saídas   ──[Nível 2: S1]──→ 2 saídas
2 saídas   ──[Nível 3: S2]──→ 1 saída (resultado)
```

Total: **7 MUX 2×1** → **3 chips 74157**

### Como conectar os 3 chips

**Chip 1 — Nível 1 (select = S0):**
- MUX A: I0 (A) e I1 (B) → Y_A
- MUX B: I2 (A) e I3 (B) → Y_B
- MUX C: I4 (A) e I5 (B) → Y_C
- MUX D: I6 (A) e I7 (B) → Y_D

**Chip 2 — Nível 2 (select = S1):**
- MUX E: Y_A (A) e Y_B (B) → Y_E
- MUX F: Y_C (A) e Y_D (B) → Y_F
- (2 MUX sobrando)

**Chip 3 — Nível 3 (select = S2):**
- MUX G: Y_E (A) e Y_F (B) → SAÍDA FINAL
- (3 MUX sobrando)

### Diagrama

```
I0 ─┐
I1 ─┤[MUX]─ Y_A ─┐
                  ├[MUX]─ Y_E ─┐
I2 ─┐             │             ├[MUX]──→ SAÍDA
I3 ─┤[MUX]─ Y_B ─┘             │
                                │
I4 ─┐             ┌[MUX]─ Y_F ─┘
I5 ─┤[MUX]─ Y_C ─┘
                  │
I6 ─┐             │
I7 ─┤[MUX]─ Y_D ─┘

     ↑               ↑            ↑
    S0              S1           S2
  (Chip 1)        (Chip 2)     (Chip 3)
```

### Tabela de seleção

| S2 S1 S0 | Entrada selecionada |
|----------|-------------------|
| 0  0  0  | I0 |
| 0  0  1  | I1 |
| 0  1  0  | I2 |
| 0  1  1  | I3 |
| 1  0  0  | I4 |
| 1  0  1  | I5 |
| 1  1  0  | I6 |
| 1  1  1  | I7 |

---

## Parte 2 — Decoder 4×16 usando 74139

### O que é o 74139

O **74139** contém **2 decodificadores 2×4** independentes. Cada um:
- 2 entradas de seleção (A, B)
- 1 enable (G, ativo em **nível baixo**)
- 4 saídas ativas em **nível baixo** (Y0, Y1, Y2, Y3)

### Ideia: hierarquia mestre-escravo

```
A3, A2 → Decoder MESTRE → habilita 1 de 4 grupos
A1, A0 → Decoders ESCRAVOS → ativa 1 saída dentro do grupo
```

### Estrutura com 3 chips 74139

**Chip 1 — Mestre (usa 1 dos 2 decoders):**
- Entradas: A3, A2
- Enable G = GND (sempre ativo)
- Saídas: Y0 → escravo 0 | Y1 → escravo 1 | Y2 → escravo 2 | Y3 → escravo 3

**Chip 2 — Escravos 0 e 1:**
- Escravo 0: G = Y0 do mestre → saídas 0, 1, 2, 3
- Escravo 1: G = Y1 do mestre → saídas 4, 5, 6, 7

**Chip 3 — Escravos 2 e 3:**
- Escravo 2: G = Y2 do mestre → saídas 8, 9, 10, 11
- Escravo 3: G = Y3 do mestre → saídas 12, 13, 14, 15

Todos os escravos recebem **A1 e A0** nas entradas.

### Diagrama

```
A3 ─┐
A2 ─┤ [MESTRE] ── Y0 ──→ G do Escravo 0 ──[ESC0]─→ saídas 0,1,2,3
    │           ── Y1 ──→ G do Escravo 1 ──[ESC1]─→ saídas 4,5,6,7
    │           ── Y2 ──→ G do Escravo 2 ──[ESC2]─→ saídas 8,9,10,11
    │           ── Y3 ──→ G do Escravo 3 ──[ESC3]─→ saídas 12,13,14,15
A1 ──────────────────────────────────────────────→ entrada A (todos escravos)
A0 ──────────────────────────────────────────────→ entrada B (todos escravos)
```

> **Atenção:** saídas do 74139 são **ativas em nível baixo**. O enable G também é ativo em baixo.
> Por isso as saídas do mestre conectam diretamente nos enables dos escravos.

### Resumo de chips

| Chip | Uso |
|------|-----|
| 74139 #1 | 1 decoder = mestre (1 sobrando) |
| 74139 #2 | 2 decoders = escravos 0 e 1 |
| 74139 #3 | 2 decoders = escravos 2 e 3 |

---

## Parte 3 — Somador 5 bits com conversão BCD

### O que o circuito faz

```
A (5 bits) + B (5 bits) → Soma S (6 bits, máx 62)

Se S < 17: CONV=1 → mostra em BCD nos displays
Se S ≥ 17: CONV=0 → mostra em binário nos displays
```

### Passo 1 — Calcular a soma (dois 74283)

```
[74283 #1]: A3..A0 + B3..B0, Cin=0  → S3..S0 + Cout0
[74283 #2]: A4 + B4 + Cout0         → S4 + S5(=Cout1)

Resultado: S5 S4 S3 S2 S1 S0  (6 bits)
```

### Passo 2 — Detectar CONV (soma < 17)

Use dois **7485** comparando a soma com 16 (= `010000`):

```
7485 #1: S3S2S1S0 vs 0000  (bits baixos de 16)
         cascade inicial: IA>B=0, IA=B=1, IA<B=0

7485 #2: 0,0,S5,S4 vs 0,0,0,1  (bits altos de 16)
         cascade: saídas do 7485 #1

CONV = NOT(OA>B do 7485 #2)  =  OA<B  OU  OA=B  do 7485 #2
```

### Passo 3 — Correção BCD (somar 6)

**Quando somar 6?** Quando nibble inferior > 9 OU quando S4=1:

```
ADD6 = S4 OR (S3 AND (S2 OR S1))
```

**Exemplos:**

| Soma | S4 | S3S2S1S0 | ADD6 | Resultado BCD | Display |
|------|----|----------|------|--------------|---------|
| 5    | 0  | 0101     | 0    | 0 \| 5        | "05" ✓  |
| 10   | 0  | 1010     | 1    | 1 \| 0        | "10" ✓  |
| 15   | 0  | 1111     | 1    | 1 \| 5        | "15" ✓  |
| 16   | 1  | 0000     | 1    | 1 \| 6        | "16" ✓  |

**3º 74283 para fazer a correção:**

```
Entradas A: S3, S2, S1, S0
Entradas B: B3=0, B2=ADD6, B1=ADD6, B0=0
Cin = 0

Saídas: nibble corrigido (D3..D0) + carry_bcd

TENS = S4 OR carry_bcd
```

- ADD6=0 → soma 0000 → saída = entrada (sem mudança)
- ADD6=1 → soma 0110 → aplica correção BCD

### Passo 4 — Selecionar o que mostrar (74157 como MUX)

Use **74157** com CONV como seleção (CONV=0 → binário, CONV=1 → BCD):

| Bit display | CONV=0 (binário) | CONV=1 (BCD) |
|-------------|-----------------|--------------|
| D7          | 0               | 0            |
| D6          | 0               | 0            |
| D5          | S5              | 0            |
| D4          | S4              | TENS         |
| D3          | S3              | D3 corrigido |
| D2          | S2              | D2 corrigido |
| D1          | S1              | D1 corrigido |
| D0          | S0              | D0 corrigido |

### Diagrama completo

```
A5..A0   B5..B0
   │         │
[74283 #1]──→ S3..S0 + Cout0
[74283 #2]──→ S4 + S5
        │
        ├──→ [7485 x2] ──→ CONV
        │
        ├──→ ADD6 = S4 OR (S3 AND (S2 OR S1))
        │
        ├──→ [74283 #3] ──→ D3..D0 corrigido + carry_bcd
        │         B = 0, ADD6, ADD6, 0
        │
        └──→ TENS = S4 OR carry_bcd
                │
               [74157 MUX] ──→ D7..D0 ──→ displays 7-seg
               select = CONV
```

### CIs usados no Lab 3

| CI | Qtd | Função |
|----|-----|--------|
| 74157 | 3 | MUX 8x1 (parte 1) + seleção display (parte 3) |
| 74139 | 3 | Decoder 4x16 (parte 2) |
| 74283 | 3 | Soma 5 bits + correção BCD (parte 3) |
| 7485  | 2 | Comparador para CONV (parte 3) |
| 7408/7432 | 1 | AND/OR para ADD6 e TENS |
