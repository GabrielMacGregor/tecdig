# Resumo — Aula 2: Funções Verdade e Projeto de Sistemas Combinacionais

## Recapitulando da Aula 1

O projeto de um sistema combinacional segue três etapas:
1. Encontrar a **Função Verdade**
2. **Minimizar** a função
3. **Implementar** o circuito com portas lógicas

A Aula 2 consolida esse processo com mais exemplos e introduz os **sistemas numéricos** necessários para os projetos das próximas aulas.

---

## 1. Reforço: Expressão Booleana a partir da especificação

O segredo está em traduzir linguagem natural para operações lógicas:

| Palavra | Operação |
|---------|----------|
| "e", "ambos", "quando ... e ..." | AND (.) |
| "ou", "pelo menos um" | OR (+) |
| "não", "desligada", "invertido" | NOT (barra) |
| "se e somente se" | Equivalência |

### Exemplo do Alarme Bancário (revisão)

**Especificação:**
> O alarme toca se C=1 **e** (S=1 **ou** (B=1 **e** E=0))

**Tradução direta:**
```
A = C . (S + (B . Ē))
```

**Tabela verdade completa (C=1, variando S, B, E):**

| C | S | B | E | B.Ē | S+(B.Ē) | A |
|---|---|---|---|-----|---------|---|
| 1 | 0 | 0 | 0 |  0  |    0    | 0 |
| 1 | 0 | 0 | 1 |  0  |    0    | 0 |
| 1 | 0 | 1 | 0 |  1  |    1    | 1 |
| 1 | 0 | 1 | 1 |  0  |    0    | 0 |
| 1 | 1 | 0 | 0 |  0  |    1    | 1 |
| 1 | 1 | 0 | 1 |  0  |    1    | 1 |
| 1 | 1 | 1 | 0 |  1  |    1    | 1 |
| 1 | 1 | 1 | 1 |  0  |    1    | 1 |

---

## 2. Exemplo: Controle de Refrigeração

**Especificação:**
> O A/C liga se:
> 1. P=0, H=1, T=1 (leve + úmido + quente)
> 2. P=1, T=1 (pesado + quente)
> 3. P=1, B=1 (pesado + alta pressão)

**Expressão booleana:**
```
A = (P̄.H.T) + (P.T) + (P.B)
```

**Dica de minimização:** perceba que P.T e P.B podem ser fatorados:
```
A = P̄.H.T + P.(T + B)
```
Isso reduz o número de portas necessárias na implementação.

---

## 3. Implementação com Portas Lógicas

Depois de obter a expressão, o circuito é construído mapeando cada operação para uma porta:

- Multiplicação (.) → AND
- Soma (+) → OR
- Barra (complemento) → NOT / Inversor

### Circuito do Alarme Bancário:
```
Entradas: B, E, S, C
Saídas: A

Passo 1: Ē = NOT(E)
Passo 2: B.Ē = AND(B, Ē)
Passo 3: S + (B.Ē) = OR(S, resultado anterior)
Passo 4: A = C . resultado = AND(C, resultado)
```

---

## 4. Sistemas Numéricos — Referência

### Base 2 (Binário)
Converta dividindo por 2 e lendo os restos de baixo para cima:

```
67 ÷ 2 = 33 resto 1
33 ÷ 2 = 16 resto 1
16 ÷ 2 =  8 resto 0
 8 ÷ 2 =  4 resto 0
 4 ÷ 2 =  2 resto 0
 2 ÷ 2 =  1 resto 0
 1 ÷ 2 =  0 resto 1
→ (67)₁₀ = (1000011)₂
```

### Base 16 (Hexadecimal)
Agrupe o binário de 4 em 4 bits (da direita para esquerda) e converta cada grupo:

```
1000011 → 0100 0011 → 4  3 → (43)₁₆
```

### BCD — Binary Coded Decimal
Cada dígito decimal é representado por 4 bits separadamente:

```
1394 → 0001 | 0011 | 1001 | 0100
```

**Atenção:** No BCD os códigos de 1010 (10) a 1111 (15) são **inválidos** — isso será fundamental na Aula 4 (Don't Care).

| Decimal | Binário | Hex | BCD |
|---------|---------|-----|-----|
| 0–9 | 0000–1001 | 0–9 | igual ao binário |
| 10 | 1010 | A | 0001 0000 |
| 11 | 1011 | B | 0001 0001 |
| ... | ... | ... | ... |

---

## Conexão com as próximas aulas

- **Aula 3:** formaliza as formas canônicas SDP e PDS (Mintermos e Maxtermos), que são uma maneira sistemática de escrever qualquer função booleana
- **Aula 4:** apresenta o Mapa de Karnaugh como ferramenta visual para minimizar essas funções, junto com o conceito de Don't Care que usa as combinações inválidas do BCD
