# Questão 1 — K-map de 5 Variáveis com Don't Care

> Aparece em TODAS as provas. Dominar isso é essencial.

---

## Como funcionar o K-map de 5 variáveis

Com 5 variáveis (A, B, C, D, E), usamos **dois K-maps de 4 variáveis** lado a lado:
- Mapa da esquerda: **A = 0**
- Mapa da direita: **A = 1**

O layout padrão é:
- Colunas: **BC** em código Gray: 00 → 01 → 11 → 10
- Linhas: **DE** em código Gray: 00 → 01 → 11 → 10

> **Regra de ouro:** grupos podem cruzar de um mapa para o outro!

Como calcular o valor de cada célula:
```
Valor = A×16 + B×8 + C×4 + D×2 + E×1
```

---

## Regras dos grupos

| SDP (agrupando 1s) | PDS (agrupando 0s) |
|--------------------|--------------------|
| Grupos de 1s e X's | Grupos de 0s e X's |
| Variável fixa em 0 → aparece **complementada** no produto | Variável fixa em 0 → aparece **direta** na soma |
| Variável fixa em 1 → aparece **direta** no produto | Variável fixa em 1 → aparece **complementada** na soma |
| Variável que varia → eliminada | Variável que varia → eliminada |
| Maior grupo = menor expressão | Maior grupo = menor expressão |

---

## EXERCÍCIO 1 (2020.2) — Parte a: SDP mínima

```
f(A,B,C,D,E) = Σm(0,2,4,6,8,10,15,18,21,22,23,26,29,31) + d(5,7,17,19,24,27)
```

### Passo 1 — Preencher os mapas

**A = 0 (valores 0–15):**

```
         BC
DE     | 00    | 01    | 11    | 10
-------+-------+-------+-------+-------
  00   | 1(0)  | 1(4)  | 0(12) | 1(8)
  01   | 0(1)  | X(5)  | 0(13) | 0(9)
  11   | 0(3)  | X(7)  | 1(15) | 0(11)
  10   | 1(2)  | 1(6)  | 0(14) | 1(10)
```

**A = 1 (valores 16–31):**

```
         BC
DE     | 00    | 01    | 11    | 10
-------+-------+-------+-------+-------
  00   | 0(16) | 0(20) | 0(28) | X(24)
  01   | X(17) | 1(21) | 1(29) | 0(25)
  11   | X(19) | 1(23) | 1(31) | X(27)
  10   | 1(18) | 1(22) | 0(30) | 1(26)
```

### Passo 2 — Identificar grupos (agrupando 1s)

| Grupo | Células | Fixas | Termo SDP |
|-------|---------|-------|-----------|
| 🔴 | {0, 2, 4, 6} | A=0, B=0, E=0 | **A'.B'.E'** |
| 🔵 | {8, 10, 24(X), 26} | B=1, C=0, E=0 | **B.C'.E'** |
| 🟢 | {2, 6, 18, 22} | B=0, D=1, E=0 | **B'.D.E'** |
| 🟡 | {21, 23, 29, 31} | A=1, C=1, E=1 | **A.C.E** |
| 🟣 | {7(X), 15, 23, 31} | C=1, D=1, E=1 | **C.D.E** |

> Grupos 🟡 e 🟣 cruzam os dois mapas!

### Resultado SDP:

```
f = A'.B'.E' + B.C'.E' + B'.D.E' + A.C.E + C.D.E
```

### Verificação de cobertura:

| Mintermo | Coberto por |
|----------|-------------|
| 0, 4 | A'.B'.E' |
| 2, 6 | A'.B'.E' + B'.D.E' |
| 8, 10 | B.C'.E' |
| 15 | C.D.E |
| 18, 22 | B'.D.E' |
| 21, 29 | A.C.E |
| 23, 31 | A.C.E + C.D.E |
| 26 | B.C'.E' |

---

## EXERCÍCIO 1 (2020.2) — Parte b: PDS mínima

```
f(A,B,C,D,E) = ΠM(1,3,9,11,12,13,14,16,19,20,25,28,30) + d(5,7,17,22,24,27)
```

### Passo 1 — Preencher os mapas (0 nas posições dos maxtermos)

**A = 0:**

```
         BC
DE     | 00    | 01    | 11    | 10
-------+-------+-------+-------+-------
  00   | 1(0)  | 1(4)  | 0(12) | 1(8)
  01   | 0(1)  | X(5)  | 0(13) | 0(9)
  11   | 0(3)  | X(7)  | 1(15) | 0(11)
  10   | 1(2)  | 1(6)  | 0(14) | 1(10)
```

**A = 1:**

```
         BC
DE     | 00    | 01    | 11    | 10
-------+-------+-------+-------+-------
  00   | 0(16) | 0(20) | 0(28) | X(24)
  01   | X(17) | 1(21) | 1(29) | 0(25)
  11   | X(19) | 1(23) | 1(31) | X(27)
  10   | 1(18) | X(22) | 0(30) | 1(26)
```

### Passo 2 — Identificar grupos (agrupando 0s)

| Grupo | Células | Fixas | Fator PDS |
|-------|---------|-------|-----------|
| 🔴 | {1, 3, 9, 11} | A=0, C=0, E=1 | **(A + C + E')** |
| 🔵 | {12, 14, 28, 30} | B=1, C=1, E=0 | **(B' + C' + E)** |
| 🟢 | {16, 20, 24(X), 28} | A=1, D=0, E=0 | **(A' + D + E)** |
| 🟡 | {1, 5(X), 9, 13} | A=0, D=0, E=1 | **(A + D + E')** |
| 🟣 | {17(X), 19, 25, 27(X)} | A=1, C=0, E=1 | **(A' + C + E')** |

> Como encontrar o fator PDS:
> - Variável fixa em 0 → entra **direta** na soma
> - Variável fixa em 1 → entra **complementada** na soma

### Resultado PDS:

```
f = (A+C+E') . (B'+C'+E) . (A'+D+E) . (A+D+E') . (A'+C+E')
```

### Verificação de cobertura dos maxtermos:

| Maxtermo | Coberto por |
|----------|-------------|
| 1, 3 | (A+C+E') |
| 9, 11 | (A+C+E') + (A+D+E') |
| 12, 14 | (B'+C'+E) |
| 13 | (A+D+E') |
| 16, 20 | (A'+D+E) |
| 19 | (A'+C+E') |
| 25 | (A'+C+E') |
| 28, 30 | (B'+C'+E) + (A'+D+E) |

---

## PROVA 2015.1 — Questão 1a: SDP mínima

```
f(A,B,C,D,E) = Σm(1,3,5,6,7,9,10,13,16,18,21,22,23,24) + d(0,2,26,29)
```

### Mapas preenchidos:

**A = 0:**

```
         BC
DE     | 00    | 01    | 11    | 10
-------+-------+-------+-------+-------
  00   | X(0)  | 0(4)  | 0(12) | 0(8)
  01   | 1(1)  | 1(5)  | 1(13) | 1(9)
  11   | 1(3)  | 1(7)  | 0(15) | 0(11)
  10   | X(2)  | 1(6)  | 0(14) | 1(10)
```

**A = 1:**

```
         BC
DE     | 00    | 01    | 11    | 10
-------+-------+-------+-------+-------
  00   | 1(16) | 0(20) | 0(28) | 1(24)
  01   | 0(17) | 1(21) | X(29) | 0(25)
  11   | 0(19) | 1(23) | 0(31) | 0(27)
  10   | 1(18) | 1(22) | 0(30) | X(26)
```

### Agrupamentos:

| Grupo | Células | Fixas | Termo |
|-------|---------|-------|-------|
| 🔴 | {1, 3, 5, 7} | A=0, B=0, E=1 | **A'.B'.E** |
| 🔵 | {1, 5, 9, 13} | A=0, D=0, E=1 | **A'.D'.E** |
| 🟢 | {5, 7, 21, 23} | B=0, C=1, E=1 | **B'.C.E** |
| 🟡 | {6, 7, 22, 23} | B=0, C=1, D=1 | **B'.C.D** |
| 🟤 | {16, 18, 24, 26(X)} | A=1, C=0, E=0 | **A.C'.E'** |
| 🟣 | {2(X), 10, 18, 26(X)} | C=0, D=1, E=0 | **C'.D.E'** |

### Resultado SDP 2015:

```
f = A'.B'.E + A'.D'.E + B'.C.E + B'.C.D + A.C'.E' + C'.D.E'
```

---

## PROVA 2015.1 — Questão 1b: PDS mínima

```
f(A,B,C,D,E) = ΠM(3,4,6,13,22,23,27,29,31) + d(9,16,20)
```

### Mapas preenchidos (0 nos maxtermos):

**A = 0:**

```
         BC
DE     | 00    | 01    | 11    | 10
-------+-------+-------+-------+-------
  00   | 1(0)  | 0(4)  | 1(12) | 1(8)
  01   | 1(1)  | 1(5)  | 0(13) | X(9)
  11   | 0(3)  | 1(7)  | 1(15) | 1(11)
  10   | 1(2)  | 0(6)  | 1(14) | 1(10)
```

**A = 1:**

```
         BC
DE     | 00    | 01    | 11    | 10
-------+-------+-------+-------+-------
  00   | X(16) | X(20) | 1(28) | 1(24)
  01   | 1(17) | 1(21) | 0(29) | 1(25)
  11   | 1(19) | 0(23) | 0(31) | 0(27)
  10   | 1(18) | 0(22) | 1(30) | 1(26)
```

### Agrupamentos dos 0s:

| Grupo | Células | Fixas | Fator |
|-------|---------|-------|-------|
| 🔴 | {4, 6, 20(X), 22} | B=0, C=1, E=0 | **(B + C' + E)** |
| 🔵 | {22, 23} | A=1, B=0, C=1 | **(A' + B + C')** |
| 🟢 | {27, 31} | A=1, B=1, D=1, E=1 | **(A' + B' + D' + E')** |
| 🟡 | {29, 31} | A=1, B=1, C=1, E=1 | **(A' + B' + C' + E')** |
| 🟤 | {13, 9(X)} | A=0, B=1, D=0, E=1 | **(A + B' + D + E')** |
| 🟣 | {3} | A=0,B=0,C=0,D=1,E=1 | **(A+B+C+D'+E')** |

### Resultado PDS 2015:

```
f = (B+C'+E) . (A'+B+C') . (A'+B'+D'+E') . (A'+B'+C'+E') . (A+B'+D+E') . (A+B+C+D'+E')
```

---

## PROVA 2015.1 — Questão 1c: Contagem de portas AND

```
Combinação ABCDE = 00101 → em quantas portas AND a saída vale "1"?
```

A = 0, B = 0, C = 1, D = 0, E = 1

A SDP era: `f = A'.B'.E + A'.D'.E + B'.C.E + B'.C.D + A.C'.E' + C'.D.E'`

Calcule as inversões primeiro: A'=1, B'=1, C'=0, D'=1, E'=0

| Porta AND | Cálculo | Saída |
|-----------|---------|-------|
| A'.B'.E   | 1·1·1   | **1** |
| A'.D'.E   | 1·1·1   | **1** |
| B'.C.E    | 1·1·1   | **1** |
| B'.C.D    | 1·1·0   | 0     |
| A.C'.E'   | 0·0·0   | 0     |
| C'.D.E'   | 0·0·0   | 0     |

> **Resposta: 3 portas AND têm saída "1"** para a combinação 00101.
>
> Confirmação: mintermo 5 (00101) está na lista de mintermos ✓ → f=1 ✓

---

## Resumo do método — Checklist para a prova

### Para SDP:
1. Marque 1s nos mintermos e X nos don't cares
2. Forme grupos de 1s (e X's), sempre potência de 2 (2,4,8...)
3. Grupos podem cruzar os dois mapas (A=0 ↔ A=1)
4. Grupos podem "dar a volta" nas bordas do mapa
5. Use Don't Care como 1 se ajudar a formar grupo maior
6. Para cada grupo: variável fixa em 0 → complementada; fixa em 1 → direta
7. Verifique que todo mintermo está coberto

### Para PDS:
1. Marque 0s nos maxtermos e X nos don't cares
2. Forme grupos de 0s (e X's), sempre potência de 2
3. Para cada grupo: variável fixa em 0 → direta na soma; fixa em 1 → complementada
4. Verifique que todo maxtermo está coberto

### Armadilhas comuns:
- Não esqueça que o K-map usa **código Gray** (00→01→**11**→10) — não binário!
- Grupos de borda podem "dar a volta": col 00 é adjacente à col 10!
- Don't Care pode ser tratado como 0 ou 1 — escolha o que forma o maior grupo
- No PDS: a lógica das variáveis é **oposta** ao SDP
