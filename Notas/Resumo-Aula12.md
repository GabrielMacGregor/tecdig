# Resumo — Aula 12: Minimização de Tabela de Estados

## 1. Objetivo

Reduzir o número de estados de uma máquina sequencial **sem alterar seu comportamento externo**. Menos estados → menos flip-flops → circuito menor e mais barato.

---

## 2. Condição de Equivalência entre Estados

Dois estados **p** e **q** são equivalentes se, para **toda** entrada x:
1. **Saídas iguais** (z idêntico para x=0, x=1, ...)
2. **Próximos estados equivalentes** (não precisam ser idênticos, apenas equivalentes entre si)

Se p ≡ q, pode-se eliminar um deles substituindo todas as ocorrências pelo outro.

---

## 3. Procedimento de Minimização

### Etapa 1 — Inspeção (estados idênticos)

Varrer a tabela à procura de linhas com **exatamente** os mesmos próximos estados E as mesmas saídas. São os casos mais fáceis: identidade direta.

> **Regra:** Se a linha de p e a linha de q são idênticas → p ≡ q → elimina um deles e substitui todas as referências ao eliminado pelo sobrevivente.

Repetir até não encontrar mais pares idênticos.

### Etapa 2 — Tabela de Implicação

Quando a inspeção não encontra mais pares óbvios, usa-se a **Tabela de Implicação**: uma grade triangular onde cada célula (i, j) representa o par de estados (i < j).

**Preenchimento:**
- Se as **saídas diferem** para qualquer entrada → marca **X** (incompatível)
- Se as saídas são iguais → escreve na célula os **pares implicados** (próximos estados que também precisam ser equivalentes)

**Propagação dos X:**
- Se a célula (i,j) tem como implicação o par (p,q), e (p,q) for marcado X → (i,j) também vira **X**
- Repetir até estabilizar (nenhum novo X surgir)

**Leitura do resultado:**
- Células sem X → estados equivalentes
- Agrupa-se em **classes de equivalência** por transitividade

---

## 4. Exemplo Completo — 12 → 5 estados

### Tabela original (12 estados):

| q  | x=0  | x=1  |
|----|------|------|
| 1  | 2,0  | 3,0  |
| 2  | 4,0  | 5,0  |
| 3  | 6,0  | 7,0  |
| 4  | 8,0  | 9,0  |
| 5  | 10,0 | 11,0 |
| 6  | 4,0  | 12,0 |
| 7  | 10,0 | 12,0 |
| 8  | 8,0  | 1,0  |
| 9  | 10,1 | 1,0  |
| 10 | 4,0  | 1,0  |
| 11 | 2,0  | 1,0  |
| 12 | 2,0  | 1,0  |

### Inspeção — 1ª rodada

- **Estado 12 = Estado 11** (mesmas saídas, mesmos próximos estados)
  → Elimina 12, substitui por 11 em toda a tabela

Após substituição, estados 6 e 7:
- Estado 6: (4,0) | (11,0)
- Estado 7: (10,0) | (11,0) → ainda diferentes (x=0: 4 vs 10)

Nova rodada após substituição:
- **Estado 7 = Estado 5** (ambos: (10,0) | (11,0))
  → Elimina 7, substitui por 5

### Tabela após inspeção (10 estados):

| q  | x=0  | x=1  |
|----|------|------|
| 1  | 2,0  | 3,0  |
| 2  | 4,0  | 5,0  |
| 3  | 6,0  | 5,0  |
| 4  | 8,0  | 9,0  |
| 5  | 10,0 | 11,0 |
| 6  | 4,0  | 11,0 |
| 8  | 8,0  | 1,0  |
| 9  | 10,1 | 1,0  |
| 10 | 4,0  | 1,0  |
| 11 | 2,0  | 1,0  |

### Tabela de Implicação

A célula da linha i e coluna j contém os **pares implicados** (o que mais precisa ser verdade para i≡j).

**Estado 9 é eliminado imediatamente** de todos os pares: ele tem saída z=1 para x=0, enquanto todos os outros têm z=0 → **X** em toda a linha do estado 9.

Após propagar todos os X (pares que dependem de (9,X) também viram X), as células sobreviventes revelam as classes de equivalência:

### Classes de Equivalência

```
(1, 3, 5, 11) → a
(2, 6, 10)    → b
(9)           → c
(8)           → d
(4)           → e
```

**De 12 estados → 5 estados!**

### Tabela Minimizada Final

| q | x=0 | x=1 |
|---|-----|-----|
| a | b,0 | a,0 |
| b | e,0 | a,0 |
| c | b,1 | a,0 |
| d | d,0 | a,0 |
| e | d,0 | c,0 |

**Verificação da lógica:**
- Estado `c` (antes: estado 9) é o único com saída 1 → para x=0 gera z=1
- `a` é o estado "raiz" (estados 1,3,5,11 tinham comportamento equivalente)
- `b` e `e` são intermediários
- `d` (estado 8) auto-loop para x=0

---

## 5. Segundo Exercício — 4 entradas

Para máquinas com múltiplas entradas (x = 0,1,2,3), o procedimento é o mesmo, mas os pares de saída E próximos estados devem ser verificados para **todas as 4 entradas**.

Exemplo dado na aula (11 estados, 4 entradas) → reduz para 6 estados (a,b,c,d,e,f):

| Classe | Estados originais |
|--------|------------------|
| a      | (4, 5)           |
| b      | (6, 9)           |
| c      | (1, 8)           |
| d      | (2)              |
| e      | (3)              |
| f      | (7)              |

---

## 6. Resumo do Método

```
Tabela original
      ↓
1. INSPEÇÃO → remove estados idênticos (repete até estabilizar)
      ↓
2. TABELA DE IMPLICAÇÃO
   a) Marcar X onde saídas diferem
   b) Preencher implicações onde saídas são iguais
   c) Propagar X pelas implicações
   d) Repetir até estabilizar
      ↓
3. CLASSES DE EQUIVALÊNCIA
   (agrupar pares sobreviventes por transitividade)
      ↓
4. TABELA MÍNIMA
   (substituir grupos por um único estado novo)
```

---

## 7. Conexão com Outras Aulas

- **Aula 10:** Esta tabela mínima é o **ponto de partida** do projeto — a Aula 10 assume que ela já existe; a Aula 12 explica como gerá-la
- **Aula 11:** Menos estados na tabela mínima = menos FF's no circuito final = equações mais simples
- **Aula 13:** Extensão deste método para **máquinas incompletamente especificadas** (com don't cares na tabela de estados)
