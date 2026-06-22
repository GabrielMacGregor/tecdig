# Resumo — Aula 3: Mintermos, Maxtermos, SDP e PDS

## Contexto

Na Aula 2, aprendemos a escrever expressões booleanas a partir de especificações em linguagem natural. A Aula 3 formaliza **duas formas canônicas** para representar qualquer função lógica: **SDP** (via Mintermos) e **PDS** (via Maxtermos).

---

## 1. Mintermos (mₖ) — para saída = 1

Um **mintermo** é um produto (AND) de todas as variáveis da função, onde cada variável aparece **normal** se seu valor na linha for **1**, e **complementada** se for **0**.

Para 3 variáveis A, B, C:

| ABC | Mintermo | Notação |
|-----|----------|---------|
| 000 | Ā.B̄.C̄ | m₀ |
| 001 | Ā.B̄.C | m₁ |
| 010 | Ā.B.C̄ | m₂ |
| 011 | Ā.B.C | m₃ |
| 100 | A.B̄.C̄ | m₄ |
| 101 | A.B̄.C | m₅ |
| 110 | A.B.C̄ | m₆ |
| 111 | A.B.C | m₇ |

**Regra:** o índice k do mintermo mₖ corresponde ao **valor decimal** da combinação de entrada.

---

## 2. Maxtermos (Mₖ) — para saída = 0

Um **maxtermo** é uma soma (OR) de todas as variáveis, onde cada variável aparece **complementada** se seu valor na linha for **1**, e **normal** se for **0**.

Para 3 variáveis A, B, C:

| ABC | Maxtermo | Notação |
|-----|----------|---------|
| 000 | (A+B+C) | M₀ |
| 001 | (A+B+C̄) | M₁ |
| 010 | (A+B̄+C) | M₂ |
| 011 | (A+B̄+C̄) | M₃ |
| 100 | (Ā+B+C) | M₄ |
| ... | ... | ... |
| 111 | (Ā+B̄+C̄) | M₇ |

> **Lembrete:** Mintermo e Maxtermo do mesmo índice são **complementos entre si**: mₖ = M̄ₖ

---

## 3. SDP — Soma de Produtos

A **SDP** expressa a função como OR dos mintermos onde a saída é **1**.

**Notação:** f = Σm(linhas com saída 1)

**Exemplo:**
Se a saída é 1 para as linhas 1, 3, 5 e 7 de uma função de 3 variáveis:
```
f(A,B,C) = Σm(1,3,5,7) = m₁ + m₃ + m₅ + m₇
         = Ā.B̄.C + Ā.B.C + A.B̄.C + A.B.C
```
Isso simplifica para:
```
f = C  (basta C=1)
```

---

## 4. PDS — Produto de Somas

A **PDS** expressa a função como AND dos maxtermos onde a saída é **0**.

**Notação:** f = ΠM(linhas com saída 0)

**Exemplo da aula:** minimizar f(A,B,C) = ΠM(2,6,7)

As posições com saída 0 são: 2, 6 e 7.
No Mapa de Karnaugh (3 variáveis), os zeros ficam nas células 2, 6 e 7.

Agrupando os zeros no mapa:
- Grupo de 2: células 6 e 7 → elimina C → resulta em (Ā+B̄) = fator (A+B) complementado → **fator (A+B)**

Mas no PDS trabalhamos diretamente com os maxtermos:
```
f(A,B,C) = ΠM(2,6,7) = M₂.M₆.M₇
```

Após minimização com K-map:
```
f(A,B,C) = (A+B) . (B+C)
```

---

## 5. Mapa de Karnaugh para PDS

No Mapa de Karnaugh para minimização por **PDS**:
- Preencha o mapa com os **0s** nas posições dos Maxtermos
- Agrupe os **0s** em grupos de 2, 4 ou 8
- Para cada grupo, elimine as variáveis que mudam dentro do grupo
- A variável que fica entra no fator de soma de forma **direta** (se foi 0) ou **complementada** (se foi 1)

---

## 6. Exemplo com 4 variáveis — PDS

**f(A,B,C,D) = ΠM(1,3,6,8,9,10,11,13,15)**

Coloca os 0s nas posições listadas no mapa 4×4.
Agrupando os 0s:
- Grupo dos 0s em linha 8,9,10,11 → A=1, B=0 → fator **(Ā+B)**
- Grupo vertical 1,3,9,11 → B=0, D=1 → fator **(B+D̄)**
- Grupo 1,3 → A=0, B=0, D=1 → coberto pelos grupos acima
- Grupo 6 → A=0, B=1, C=1, D=0 → fator **(A+B̄+C̄+D)**
- Grupo 13,15 → A=1, B=1, D=1 → fator **(Ā+B̄+D̄)**

**Resultado:**
```
f(A,B,C,D) = (A+B+C+D) . (Ā+B) . (Ā+D̄) . (B̄+D̄)
```

---

## 7. Implementação OR-AND com portas NOR-NOR

Uma expressão PDS (Produto de Somas) pode ser implementada diretamente com **portas NOR em dois níveis**:

- Primeiro nível: NOR em cada soma (= OR + inversão)
- Segundo nível: NOR de todas as saídas (= OR + inversão = AND duplo negado = AND)

Isso é análogo ao que a implementação AND-OR com NAND-NAND faz para SDP.

---

## Resumo Visual

```
SDP → usa Mintermos (onde saída = 1) → Σm(...) → implementa com AND-OR ou NAND-NAND
PDS → usa Maxtermos (onde saída = 0) → ΠM(...) → implementa com OR-AND ou NOR-NOR
```

---

## Conexão com a próxima aula

A Aula 4 apresenta o **Don't Care** — o que fazer com entradas que nunca ocorrem — e aplica o Mapa de Karnaugh ao projeto de somadores BCD.
