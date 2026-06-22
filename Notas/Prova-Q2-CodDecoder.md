# Questão 2 — Codificador com Prioridade + Decodificador de Intervalos

> Prova 2015.1 — 4 pontos

---

## Parte a — Codificador com Prioridade para 5 Sinais

### Enunciado

5 sinais: **L0, L1, L2, L3, L4**
Saída: código de 3 bits **C2C1C0**
Implementar apenas **C0**.
Você define qual sinal tem maior prioridade.

### Definindo a prioridade

Convenção natural: **L4 tem prioridade máxima**, depois L3, L2, L1, L0.

| Sinal ativo (maior prioridade) | C2C1C0 | Significado |
|-------------------------------|--------|-------------|
| L4 = 1 (qualquer outro)       | 100    | código 4    |
| L4=0, L3=1                    | 011    | código 3    |
| L4=0, L3=0, L2=1              | 010    | código 2    |
| L4=0, L3=0, L2=0, L1=1        | 001    | código 1    |
| apenas L0 (ou nenhum)          | 000    | código 0    |

### Implementando apenas C0 (LSB)

C0 = 1 quando o código é **ímpar**: 1 (L1 ativo) ou 3 (L3 ativo).

```
C0 = 1  quando sinal ativo é L1 (código 1) ou L3 (código 3)
```

**Expressão:**

```
C0 = L4' · L3 + L4' · L3' · L2' · L1
```

Ou simplificando com o fato de que L3 já elimina L2,L1 pela prioridade:

```
C0 = L4' · (L3 + L3' · L2' · L1)
C0 = L4' · (L3 + L2' · L1)
```

### Tabela verdade de C0 (casos relevantes)

| L4 | L3 | L2 | L1 | L0 | C0 |
|----|----|----|----|----|-----|
|  1 |  x |  x |  x |  x |  0  |
|  0 |  1 |  x |  x |  x |  1  |
|  0 |  0 |  1 |  x |  x |  0  |
|  0 |  0 |  0 |  1 |  x |  1  |
|  0 |  0 |  0 |  0 |  x |  0  |

### Circuito para C0

```
L4 ──→ NOT ──┐
             AND3 ──→ NAND ou AND → C0
L3 ──────────┘─────────────────────────→ parte 1: L4'.L3

L4 ──→ NOT ──┐
L3 ──→ NOT ──┤
L2 ──→ NOT ──┤── AND4 ─────────────────→ parte 2: L4'.L3'.L2'.L1
L1 ──────────┘

[parte 1] OR [parte 2] ──→ C0
```

**CIs necessários:** 7404 (NOT) + 7408 (AND) + 7432 (OR)

---

## Parte b — Decodificador 6 bits para intervalos 8–15 e 40–43

### Enunciado

Sistema de 6 bits → códigos de 0 a 63 (**B5B4B3B2B1B0**)
Gerar **sinal em nível "0"** quando o código estiver nos intervalos:
- **8–15:** `001000` a `001111`
- **40–43:** `101000` a `101011`

Usar **decodificadores 2×4** (2 entradas de seleção, 1 enable, 4 saídas ativas em nível baixo).

### Análise dos intervalos em binário

**Intervalo 8–15:**
```
8  = 001000
15 = 001111
→ B5=0, B4=0, B3=1, B2B1B0 = qualquer (8 códigos)
→ Resumo: B5=0, B4=0, B3=1
```

**Intervalo 40–43:**
```
40 = 101000
41 = 101001
42 = 101010
43 = 101011
→ B5=1, B4=0, B3=1, B2=0, B1B0 = qualquer (4 códigos)
→ Resumo: B5=1, B4=0, B3=1, B2=0
```

### Estratégia: decodificação hierárquica

Usamos 3 decodificadores 2×4, em dois níveis:

#### Nível 1 — Decoder MESTRE (entradas B5B4)

Divide os 64 códigos em 4 grupos de 16:

| Saída | B5B4 | Grupo |
|-------|------|-------|
| Y0    | 00   | 0–15  |
| Y1    | 01   | 16–31 |
| Y2    | 10   | 32–47 |
| Y3    | 11   | 48–63 |

- Enable permanentemente ativo (EN = GND)
- Saídas ativas em nível baixo: Y0=0 quando B5B4=00, etc.

#### Nível 2 — Decoder ESCRAVO para grupo 0–15 (habilitado por Y0)

- Entradas: B3B2
- Enable: Y0 (ativo quando B5B4=00)

| Saída | B3B2 | Códigos |
|-------|------|---------|
| Ya0   | 00   | 0–3     |
| Ya1   | 01   | 4–7     |
| **Ya2** | **10** | **8–11** |
| **Ya3** | **11** | **12–15** |

Intervalo 8–15 = Ya2 OU Ya3 ativos

#### Nível 2 — Decoder ESCRAVO para grupo 32–47 (habilitado por Y2)

- Entradas: B3B2
- Enable: Y2 (ativo quando B5B4=10)

| Saída | B3B2 | Códigos |
|-------|------|---------|
| Yb0   | 00   | 32–35   |
| Yb1   | 01   | 36–39   |
| **Yb2** | **10** | **40–43** |
| Yb3   | 11   | 44–47   |

Intervalo 40–43 = Yb2 ativo

### Saída final (ativa em nível "0")

Todas as saídas dos decoders já são ativas em nível baixo (=0 quando ativas).

```
OUT = Ya2 AND Ya3 AND Yb2
```

Verificação:
- Código 8–11:  Ya2=0, Ya3=1, Yb2=1 → OUT = 0·1·1 = 0 ✓
- Código 12–15: Ya2=1, Ya3=0, Yb2=1 → OUT = 1·0·1 = 0 ✓
- Código 40–43: Ya2=1, Ya3=1, Yb2=0 → OUT = 1·1·0 = 0 ✓
- Código 5 (fora): Ya2=1, Ya3=1, Yb2=1 → OUT = 1 ✓

### Diagrama do circuito

```
B5 ─┐
B4 ─┤ [DECODER MESTRE]  EN=GND
    │   entradas: B5, B4
    │   Y0 (0–15)  ──────────→ EN [DECODER A] ──── entradas: B3,B2
    │   Y1 (16–31) → não usado           │
    │   Y2 (32–47) ──────────→ EN [DECODER B] ──── entradas: B3,B2
    │   Y3 (48–63) → não usado           │
    │                                     │
B3 ──────────────────────────────────────┘ (para ambos os escravos)
B2 ──────────────────────────────────────┘

DECODER A saídas:  Ya0, Ya1, Ya2, Ya3
DECODER B saídas:  Yb0, Yb1, Yb2, Yb3

Ya2 ─┐
Ya3 ─┤── AND3 ──→ OUT (ativo em "0" nos intervalos 8–15 e 40–43)
Yb2 ─┘
```

### CIs necessários

| CI | Quantidade | Uso |
|----|-----------|-----|
| 74139 | 2 chips | 2 decoders 2×4 por chip → total 4 decoders (1 mestre + 2 escravos + 1 sobra) |
| 7411 (AND3) | 1 porta | porta AND de 3 entradas para OUT |

> **Nota:** O 74139 tem **dois** decoders 2×4 por chip. Portanto, 2 chips 74139 são suficientes para mestre + 2 escravos (1 decoder sobra no 2º chip).

### Resumo visual do mapeamento

```
B5 B4 | Mestre | B3 B2 | Escravo A (0-15) | Escravo B (32-47) | OUT
------+--------+-------+------------------+-------------------+----
 0  0 |  Y0=0  |  1  0 |   Ya2=0          |    (inativo)      |  0  ← 8–11
 0  0 |  Y0=0  |  1  1 |   Ya3=0          |    (inativo)      |  0  ← 12–15
 1  0 |  Y2=0  |  1  0 |   (inativo)      |    Yb2=0          |  0  ← 40–43
outros|        |       |   ambos=1        |    ambos=1        |  1  ← fora
```
