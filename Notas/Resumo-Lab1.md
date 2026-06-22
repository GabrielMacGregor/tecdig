# Lab 1 — Circuito Combinacional: Salas com Projetor

## O que o circuito faz

- Entrada: número da sala em binário (5 bits: A4 A3 A2 A1 A0)
- Saída: LED acende se a sala tem projetor

Salas com projetor: **1, 3, 10, 11, 12, 13, 14, 15, 16, 17, 19, 25, 26, 27**

---

## Passo 1 — Tabela verdade

| Sala | A4 | A3 | A2 | A1 | A0 |
|------|----|----|----|----|-----|
| 1    | 0  | 0  | 0  | 0  | 1  |
| 3    | 0  | 0  | 0  | 1  | 1  |
| 10   | 0  | 1  | 0  | 1  | 0  |
| 11   | 0  | 1  | 0  | 1  | 1  |
| 12   | 0  | 1  | 1  | 0  | 0  |
| 13   | 0  | 1  | 1  | 0  | 1  |
| 14   | 0  | 1  | 1  | 1  | 0  |
| 15   | 0  | 1  | 1  | 1  | 1  |
| 16   | 1  | 0  | 0  | 0  | 0  |
| 17   | 1  | 0  | 0  | 0  | 1  |
| 19   | 1  | 0  | 0  | 1  | 1  |
| 25   | 1  | 1  | 0  | 0  | 1  |
| 26   | 1  | 1  | 0  | 1  | 0  |
| 27   | 1  | 1  | 0  | 1  | 1  |

---

## Passo 2 — K-map de 5 variáveis (dois mapas 4×4)

**K-map A4 = 0 (salas 0–15):**

```
        A1A0
A3A2  | 00 | 01 | 11 | 10
------+----+----+----+----
  00  |  0 |  1 |  1 |  0   (salas 0,1,3,2)
  01  |  0 |  0 |  0 |  0   (salas 4,5,7,6)
  11  |  1 |  1 |  1 |  1   (salas 12,13,15,14)
  10  |  0 |  0 |  1 |  1   (salas 8,9,11,10)
```

**K-map A4 = 1 (salas 16–31):**

```
        A1A0
A3A2  | 00 | 01 | 11 | 10
------+----+----+----+----
  00  |  1 |  1 |  1 |  0   (salas 16,17,19,18)
  01  |  0 |  0 |  0 |  0   (salas 20,21,23,22)
  11  |  0 |  1 |  1 |  1   (salas 24,25,27,26)
  10  |  0 |  0 |  0 |  0   (salas 28,29,31,30)
```

> Grupos podem atravessar de um mapa para o outro!

---

## Passo 3 — Agrupamentos

| Grupo | Células (salas) | Variáveis fixas | Termo |
|-------|----------------|-----------------|-------|
| Vermelho | 12,13,14,15 | A4=0, A3=1, A2=1 | **A4'.A3.A2** |
| Azul | 10,11,26,27 | A3=1, A2=0, A1=1 | **A3.A2'.A1** |
| Verde | 1,3,17,19 | A3=0, A2=0, A0=1 | **A3'.A2'.A0** |
| Amarelo | 17,19,25,27 | A4=1, A2=0, A0=1 | **A4.A2'.A0** |
| Roxo | 16,17 | A4=1, A3=0, A2=0, A1=0 | **A4.A3'.A2'.A1'** |

---

## Passo 4 — Expressão minimizada

```
f = A4'.A3.A2  +  A3.A2'.A1  +  A3'.A2'.A0  +  A4.A2'.A0  +  A4.A3'.A2'.A1'
```

---

## Passo 5 — Estrutura do circuito

```
A4'─┐
A3 ─┤── AND3 ──┐
A2 ─┘          │
               │
A3 ─┐          │
A2'─┤── AND3 ──┤
A1 ─┘          │
               ├── OR5 ──→ f (LED)
A3'─┐          │
A2'─┤── AND3 ──┤
A0 ─┘          │
               │
A4 ─┐          │
A2'─┤── AND3 ──┤
A0 ─┘          │
               │
A4 ─┐          │
A3'─┤          │
A2'─┤── AND4 ──┘
A1'─┘
```

Inversões necessárias (7404): A4', A2', A3', A1'

O OR5 se monta em cascata com OR2: OR(t1,t2) → OR(res,t3) → OR(res,t4) → OR(res,t5)

---

## Passo 6 — CIs TTL necessários

| CI | Função | Qtd |
|----|--------|-----|
| 7404 | 6x NOT | 1 |
| 7411 | 3x AND3 | 2 |
| 7421 | 2x AND4 | 1 |
| 7432 | 4x OR2 | 1 |

---

## Como montar no CircuitMaker

1. 5 chaves digitais → A4, A3, A2, A1, A0
2. Inversores 7404 → gerar A4', A2', A3', A1'
3. Portas AND para cada termo da expressão
4. OR em cascata juntando os 5 termos
5. LED na saída f

## Testes

| Sala | A4A3A2A1A0 | LED esperado |
|------|-----------|-------------|
| 12   | 01100     | Aceso ✓ |
| 4    | 00100     | Apagado ✓ |
| 27   | 11011     | Aceso ✓ |
| 18   | 10010     | Apagado ✓ |
