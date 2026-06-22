# Lab 2 — Conversor de Teclado + Somador de 5 Bits

## Parte 1 — Conversor de Código

### O problema

O teclado gera um código errado. Projetar um circuito que converte para o binário correto.

| Tecla | Cód. Gerado (D3D2D1D0) | Binário (B3B2B1B0) |
|-------|----------------------|-------------------|
| 1     | 0000                 | 0001              |
| 2     | 0001                 | 0010              |
| 3     | 0010                 | 0011              |
| 4     | 0100                 | 0100              |
| 5     | 0101                 | 0101              |
| 6     | 0110                 | 0110              |
| 7     | 1000                 | 0111              |
| 8     | 1001                 | 1000              |
| 9     | 1010                 | 1001              |
| 0     | 1101                 | 0000              |

**Don't Cares (nunca ocorrem):** 0011, 0111, 1011, 1100, 1110, 1111

---

### Tabela verdade completa

| D3D2D1D0 | B3 | B2 | B1 | B0 | Obs |
|----------|----|----|----|----|-----|
| 0000     | 0  | 0  | 0  | 1  | Tecla 1 |
| 0001     | 0  | 0  | 1  | 0  | Tecla 2 |
| 0010     | 0  | 0  | 1  | 1  | Tecla 3 |
| 0011     | X  | X  | X  | X  | DC |
| 0100     | 0  | 1  | 0  | 0  | Tecla 4 |
| 0101     | 0  | 1  | 0  | 1  | Tecla 5 |
| 0110     | 0  | 1  | 1  | 0  | Tecla 6 |
| 0111     | X  | X  | X  | X  | DC |
| 1000     | 0  | 1  | 1  | 1  | Tecla 7 |
| 1001     | 1  | 0  | 0  | 0  | Tecla 8 |
| 1010     | 1  | 0  | 0  | 1  | Tecla 9 |
| 1011     | X  | X  | X  | X  | DC |
| 1100     | X  | X  | X  | X  | DC |
| 1101     | 0  | 0  | 0  | 0  | Tecla 0 |
| 1110     | X  | X  | X  | X  | DC |
| 1111     | X  | X  | X  | X  | DC |

---

### K-map para B3

```
        D1D0
D3D2  | 00 | 01 | 11 | 10
------+----+----+----+----
  00  |  0 |  0 |  X |  0
  01  |  0 |  0 |  X |  0
  11  |  X |  0 |  X |  X
  10  |  0 |  1 |  X |  1
```

Grupos:
- {9, 11(DC)} → D3=1, D2=0, D0=1 → **D3.D2'.D0**
- {10, 11(DC), 14(DC), 15(DC)} → D3=1, D1=1 → **D3.D1**

```
B3 = D3.D1 + D3.D2'.D0
```

---

### K-map para B2

```
        D1D0
D3D2  | 00 | 01 | 11 | 10
------+----+----+----+----
  00  |  0 |  0 |  X |  0
  01  |  1 |  1 |  X |  1
  11  |  X |  0 |  X |  X
  10  |  1 |  0 |  X |  0
```

Grupos:
- {4, 5, 6, 7(DC)} → D3=0, D2=1 → **D3'.D2**
- {8, 12(DC)} → D3=1, D1=0, D0=0 → **D3.D1'.D0'**

```
B2 = D3'.D2 + D3.D1'.D0'
```

---

### K-map para B1

```
        D1D0
D3D2  | 00 | 01 | 11 | 10
------+----+----+----+----
  00  |  0 |  1 |  X |  1
  01  |  0 |  0 |  X |  1
  11  |  X |  0 |  X |  X
  10  |  1 |  0 |  X |  0
```

Grupos:
- {2, 3(DC), 6, 7(DC)} → D3=0, D1=1 → **D3'.D1**
- {1, 3(DC)} → D3=0, D2=0, D0=1 → **D3'.D2'.D0**
- {8, 12(DC)} → D3=1, D1=0, D0=0 → **D3.D1'.D0'**

```
B1 = D3'.D1 + D3'.D2'.D0 + D3.D1'.D0'
```

---

### K-map para B0

```
        D1D0
D3D2  | 00 | 01 | 11 | 10
------+----+----+----+----
  00  |  1 |  0 |  X |  1
  01  |  0 |  1 |  X |  0
  11  |  X |  0 |  X |  X
  10  |  1 |  0 |  X |  1
```

Grupos:
- {0, 2, 8, 10} → D2=0, D0=0 → **D2'.D0'**
- {5, 7(DC)} → D3=0, D2=1, D0=1 → **D3'.D2.D0**

```
B0 = D2'.D0' + D3'.D2.D0
```

---

### Verificação

| Entrada | Tecla | Saída esperada | B3 | B2 | B1 | B0 |
|---------|-------|---------------|----|----|----|----|
| 1000    | 7     | 0111          | 0  | 1  | 1  | 1  |
| 1010    | 9     | 1001          | 1  | 0  | 0  | 1  |
| 1101    | 0     | 0000          | 0  | 0  | 0  | 0  |

### CIs para a Parte 1

| CI | Função |
|----|--------|
| 7404 | NOT |
| 7408 | AND2 |
| 7411 | AND3 |
| 7432 | OR2 |

---

## Parte 2 — Somador de 5 bits com flag M

### Estrutura geral

```
A4..A0  B4..B0
  │       │
[74283 #1: bits 0–3, Cin=0] → S3..S0 + Cout0
           │
[74283 #2: bit 4, Cin=Cout0] → S4 + S5(=Cout1)

Resultado: S5 S4 S3 S2 S1 S0  (6 bits, máx = 62)
           │
[7485 x2] → compara com 35 → flag M
```

---

### 74283 em cascata para 5 bits

**74283 #1:**
- Entradas A: A3, A2, A1, A0
- Entradas B: B3, B2, B1, B0
- Cin = GND
- Saídas: S3, S2, S1, S0 e Cout0

**74283 #2:**
- Entradas A: A4 no pino A1, demais = GND
- Entradas B: B4 no pino B1, demais = GND
- Cin = Cout0
- Saídas: S4 no pino S1, Cout1 = S5

---

### Flag M (resultado > 35)

35 em binário (6 bits) = **100011**

| Bit | S5 | S4 | S3 | S2 | S1 | S0 |
|-----|----|----|----|----|----|----|
| 35  | 1  | 0  | 0  | 0  | 1  | 1  |

**7485 #1 — bits baixos (S3S2S1S0 vs 0011):**
- Entradas B fixas: 0, 0, 1, 1
- Cascade inicial: IA>B=0, IA=B=1, IA<B=0

**7485 #2 — bits altos (0,0,S5,S4 vs 0,0,1,0):**
- Entradas B fixas: 0, 0, 1, 0
- Cascade: saídas do 7485 #1
- **M = saída OA>B do 7485 #2**

---

### Diagrama completo

```
A4..A0   B4..B0
   │         │
[74283 #1]──→ S3..S0 + Cout0
[74283 #2]──→ S4 + S5

S3S2S1S0 ──→ [7485 #1] → cascade → [7485 #2] → OA>B = M
              B=0011                  B=0010
```

### CIs para a Parte 2

| CI | Qtd | Função |
|----|-----|--------|
| 74283 | 2 | Somador 4 bits em cascata |
| 7485  | 2 | Comparador em cascata para flag M |
