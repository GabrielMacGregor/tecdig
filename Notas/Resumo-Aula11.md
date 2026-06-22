# Resumo — Aula 11: Projeto de Circuitos Sequenciais (Parte 2)

## Visão Geral

A Aula 11 pega a Tabela de Transição (obtida na Aula 10) e conclui o projeto completo: deriva as equações dos flip-flops e monta o circuito. São usados dois tipos de FF: **JK** e **D**, sobre o mesmo exemplo.

---

## Problema-base da Aula

Tabela Mínima (modelo Mealy, 4 estados):

| q | x=0 | x=1 |
|---|-----|-----|
| a | a,0 | c,0 |
| b | a,0 | d,1 |
| c | a,0 | b,0 |
| d | d,0 | d,0 |

**Designação de estados** (atribuição de bits):

| Estado | y1 y0 |
|--------|-------|
| a      | 0 0   |
| b      | 0 1   |
| c      | 1 1   |
| d      | 1 0   |

> A escolha do código Gray (00→01→11→10) minimiza transições simultâneas de bits.

---

## Tabela de Transição (y1y0 atual → y1y0 próximo)

| y1 y0 (atual) | x=0 | x=1 |
|---------------|-----|-----|
| 0 0 (= a)     | 0 0 | 1 1 |
| 0 1 (= b)     | 0 0 | 1 0 |
| 1 1 (= c)     | 0 0 | 0 1 |
| 1 0 (= d)     | 1 0 | 1 0 |

---

## Parte 1 — Projeto com FF JK

### Lista de Transição do FF JK (tabela de excitação)

Esta tabela responde: "Para fazer o FF ir de Qt para Qt+1, que valores de J e K são necessários?"

| Qt → Qt+1 | J | K |
|-----------|---|---|
| 0 → 0     | 0 | X |
| 0 → 1     | 1 | X |
| 1 → 0     | X | 1 |
| 1 → 1     | X | 0 |

> **X = don't care** — quando o FF mantém o estado, o outro input é irrelevante.

### Mapas de Karnaugh e Equações

Para cada FF (y1 e y0), são gerados 4 mapas K (J_y1, K_y1, J_y0, K_y0):

**Mapa J_y1** (eixos: x vs y1y0 em Gray):

| x \ y1y0 | 00 | 01 | 11 | 10 |
|----------|----|----|----|----|
| 0        | 0  | 0  | X  | X  |
| 1        | 1  | 1  | X  | X  |

**→ J_y1 = x**

**Mapa K_y1:**

| x \ y1y0 | 00 | 01 | 11 | 10 |
|----------|----|----|----|----|
| 0        | X  | X  | 1  | 0  |
| 1        | X  | X  | 1  | 0  |

**→ K_y1 = y0**

**Mapa J_y0:**

| x \ y1y0 | 00 | 01 | 11 | 10 |
|----------|----|----|----|----|
| 0        | 0  | X  | X  | 0  |
| 1        | 1  | X  | X  | 0  |

**→ J_y0 = ȳ1·x**

**Mapa K_y0:**

| x \ y1y0 | 00 | 01 | 11 | 10 |
|----------|----|----|----|----|
| 0        | X  | 1  | 1  | X  |
| 1        | X  | 1  | 0  | X  |

**→ K_y0 = x̄ + ȳ1**

### Equação de Saída

A saída Z=1 só ocorre quando q=b e x=1 (transição b→d com z=1).
Estado b = (y1=0, y0=1). Entrada x=1:

**Z = x · ȳ1 · y0**

### Circuito Final (FF JK)

```
x ──────────────────────────────── J(y1) → FF_y1 → y1
                                   K(y1) ← y0
x, ȳ1 → AND ────────────────────── J(y0) → FF_y0 → y0
x̄, ȳ1 → OR  ────────────────────── K(y0)
x, ȳ1, y0 → AND ──────────────────────────────────── Z
```

---

## Parte 2 — Mesmo Projeto com FF D

### Lista de Transição do FF D

O FF D é simples: **D = Qt+1** (o que você quer que o FF seja no próximo ciclo, você coloca em D).

| Qt → Qt+1 | D |
|-----------|---|
| 0 → 0     | 0 |
| 0 → 1     | 1 |
| 1 → 0     | 0 |
| 1 → 1     | 1 |

> Não há don't care no FF D — D simplesmente copia o próximo estado.

### Mapas de Karnaugh e Equações

**Mapa D_y1** (lê direto o bit y1 do próximo estado na tabela de transição):

| x \ y1y0 | 00 | 01 | 11 | 10 |
|----------|----|----|----|----|
| 0        | 0  | 0  | 0  | 1  |
| 1        | 1  | 1  | 0  | 1  |

Grupos: coluna 10 (ambas as linhas) → **y1·ȳ0**; linha x=1 colunas 00 e 01 → **x·ȳ1**

**→ D_y1 = y1·ȳ0 + x·ȳ1**

**Mapa D_y0** (bit y0 do próximo estado):

| x \ y1y0 | 00 | 01 | 11 | 10 |
|----------|----|----|----|----|
| 0        | 0  | 0  | 0  | 0  |
| 1        | 1  | 0  | 1  | 0  |

Grupos: (00,x=1) e (11,x=1) — não adjacentes → dois mintermos separados

**→ D_y0 = ȳ1·ȳ0·x + y1·y0·x**

Que pode ser fatorado: **D_y0 = x · (ȳ1·ȳ0 + y1·y0)**

### Comparação JK vs D

| Aspecto | FF JK | FF D |
|---------|-------|------|
| Don't care (X) | Sim (aproveitado nos mapas K) | Não (D = Qt+1 direto) |
| Equações | Mais compactas (X ajuda na minimização) | Geralmente mais complexas |
| Quantidade de FF's | Igual | Igual |
| Facilidade de projeto | Mais etapas | Mais simples conceitualmente |

---

## Exemplo Extra — Contador Módulo 8 Bidirecional

**Especificação:**
- 3 bits de saída: Z = (Z2, Z1, Z0)
- x=0 → conta crescente (0→1→2→...→7→0)
- x=1 → conta decrescente (7→6→5→...→0→7)

**Tabela de Transição:**

| Estado atual (q2q1q0) | x=0 (próximo) | x=1 (próximo) |
|-----------------------|---------------|---------------|
| 0 0 0                 | 0 0 1         | 1 1 1         |
| 0 0 1                 | 0 1 0         | 0 0 0         |
| 0 1 0                 | 0 1 1         | 0 0 1         |
| 0 1 1                 | 1 0 0         | 0 1 0         |
| 1 0 0                 | 1 0 1         | 0 1 1         |
| 1 0 1                 | 1 1 0         | 1 0 0         |
| 1 1 0                 | 1 1 1         | 1 0 1         |
| 1 1 1                 | 0 0 0         | 1 1 0         |

> A partir desta tabela, o procedimento é o mesmo: aplicar a lista de excitação do FF escolhido, montar os mapas K para cada entrada de FF, minimizar e obter as equações.

---

## Resumo do Procedimento Completo de Projeto

```
1. Tabela Mínima de Estados
        ↓
2. Designação de Estados (atribuir bits binários)
        ↓
3. Tabela de Transição (substitui nomes por bits)
        ↓
4. Lista de Excitação do FF (JK ou D)
        ↓
5. Mapas de Karnaugh (um por entrada de FF)
        ↓
6. Equações minimizadas (J, K, D para cada FF)
        ↓
7. Equação da Saída Z (lida da tabela de transição)
        ↓
8. Circuito
```

---

## Conexão com Outras Aulas

- **Aula 4 (Karnaugh):** Os X (don't care) do FF JK são aproveitados nos mapas K para obter equações menores
- **Aula 7 (FF's JK e D):** As tabelas de excitação vêm diretamente da tabela verdade de cada FF
- **Aula 10:** Esta aula completa o fluxo iniciado na Aula 10 — aqui saímos da Tabela de Transição e chegamos ao Circuito
- **Aula 12:** A "Tabela Mínima" usada como ponto de partida aqui será gerada formalmente pela minimização de estados
