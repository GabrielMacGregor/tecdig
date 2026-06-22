# Resumo — Aula 13: Máquina Incompletamente Especificada + Minimização

## 1. O que é uma Máquina Incompletamente Especificada?

Em alguns sistemas, certas sequências de entrada **nunca ocorrem** na prática. Nesses casos, o próximo estado ou a saída para aquela combinação simplesmente não importa — são **don't cares**, representados por traço `–` na tabela de estados.

> Analogia direta com os don't cares dos Mapas de Karnaugh (Aula 4), mas agora nas linhas da tabela de estados.

---

## 2. Exemplo — Testador de Sequências

**Especificação:**
- Entradas: A, B, C, K (4 entradas, uma de cada vez)
- Saídas: Z1, Z2
- Fluxo: K inicia/encerra; A, B, C chegam uma única vez cada
- Sequência **A→B→C**: saída Z2=1 no K final
- Sequência **C→B→A**: saída Z1=1 no K final
- Qualquer outra sequência: saídas 00

**Tabela de Estados (com don't cares):**

| q | A    | B    | C    | K     |
|---|------|------|------|-------|
| 1 | 1,–  | 1,–  | 1,–  | 3,00  |
| 3 | 4,–  | 5,–  | 6,–  | –,–   |
| 4 | –,–  | 7,–  | 9,–  | –,–   |
| 5 | 9,–  | –,–  | 9,–  | –,–   |
| 6 | 9,–  | 8,–  | –,–  | –,–   |
| 7 | –,–  | –,–  | 7,–  | 1,01  |
| 8 | 8,–  | –,–  | –,–  | 1,10  |
| 9 | 9,–  | 9,–  | 9,–  | 1,00  |

> **Por que tantos `–`?** Porque A, B e C chegam **uma única vez cada**. Se estou no estado "recebi A", nunca vou receber A novamente nessa sequência. Os `–` refletem entradas que fisicamente não podem ocorrer naquele ponto.

---

## 3. Diferença entre Equivalência e Compatibilidade

Na **máquina completamente especificada** (Aula 12):
- Procura-se **estados equivalentes** (saídas iguais E próximos estados equivalentes, para TODAS as entradas)
- Equivalência é **transitiva**: se A≡B e B≡C, então A≡C

Na **máquina incompletamente especificada**:
- Procura-se **estados compatíveis**
- **Compatibilidade NÃO é transitiva**: A compatível com B e B compatível com C **não implica** A compatível com C

### Condição de Compatibilidade entre estados p e q

Para cada entrada x:
1. Se **ambas as saídas** estiverem especificadas → devem ser **iguais**
2. Se **ambos os próximos estados** estiverem especificados → devem ser **compatíveis**
3. Se um ou ambos forem `–` → a condição é satisfeita automaticamente (don't care)

---

## 4. Procedimento de Minimização

### Etapa 1 — Tabela de Implicação

Mesma estrutura da Aula 12 (grade triangular), mas com regras adaptadas:

- Se saídas **especificadas** conflitam → **X** (incompatíveis)
- Se saídas são compatíveis → escreve pares implicados (próximos estados especificados que também precisam ser compatíveis)
- Propaga X: se implicação aponta para par com X → esse par também vira X
- Células sem X → pares **compatíveis**

### Etapa 2 — Classes de Compatíveis Maximais

Como compatibilidade não é transitiva, agrupa-se em **compatíveis maximais**: o maior subconjunto de estados onde **todo par** do subconjunto é compatível.

> Diferentemente da Aula 12, onde as classes de equivalência são únicas e disjuntas, aqui um estado pode aparecer em **múltiplas** classes compatíveis.

### Etapa 3 — Escolha do Conjunto Completo e Fechado

Escolher um **subconjunto das classes maximais** que seja:

- **Completo**: todo estado original aparece em pelo menos uma classe escolhida
- **Fechado**: para cada classe escolhida, os próximos estados (especificados) de seus membros pertencem todos a **alguma classe escolhida**

O objetivo é escolher o **menor número** de classes que satisfaça as condições acima.

---

## 5. Exemplo Completo — 8 estados → 3 estados

### Tabela original (com don't cares):

| q | x=00 | x=01 | x=11 | x=10 |
|---|------|------|------|------|
| 1 | 2,0  | –,1  | 3,–  | 2,0  |
| 2 | 8,0  | 5,1  | 2,0  | –,–  |
| 3 | 3,0  | 6,1  | –,–  | 5,0  |
| 4 | –,–  | 7,1  | 2,–  | –,–  |
| 5 | –,–  | 1,1  | 1,1  | –,–  |
| 6 | –,–  | 1,1  | 2,–  | –,–  |
| 7 | 2,0  | –,1  | 8,–  | 2,0  |
| 8 | 3,0  | 4,1  | –,–  | 5,0  |

### Tabela de Implicação

Apenas o par (1,4) é imediatamente **X** na primeira passagem (saídas conflitantes para x=00: z=0 vs z não especificado não conflita, mas para outros estados...). A propagação de X elimina vários outros pares.

### Classes de Compatíveis Maximais encontradas:

```
(3, 4, 5, 6, 8)
(2, 3, 8)
(1, 4, 6, 7)
(1, 2, 7)
```

### Escolha do conjunto completo e fechado

Analisando as implicações de cada classe, escolhe-se:

| Classe | Estados | Nome |
|--------|---------|------|
| (1, 2, 7) | 1, 2, 7 | a |
| (4, 5, 6) | 4, 5, 6 | b |
| (2, 3, 8) | 2, 3, 8 | c |

- **Completo**: todos os estados 1-8 aparecem em pelo menos uma classe ✓
- **Fechado**: os próximos estados de cada classe pertencem a uma das três classes ✓

### Tabela Minimizada Final (3 estados):

| q | x=00 | x=01 | x=11 | x=10 |
|---|------|------|------|------|
| a | c,0  | b,1  | c,0  | a,0  |
| b | –,–  | a,1  | a,1  | –,–  |
| c | c,0  | b,1  | a,0  | b,0  |

**De 8 estados → 3 estados!**

---

## 6. Comparação: Completa vs Incompletamente Especificada

| Aspecto | Máq. Completa (Aula 12) | Máq. Incompleta (Aula 13) |
|---------|------------------------|--------------------------|
| Busca por | Estados **equivalentes** | Estados **compatíveis** |
| Condição saída | Igual para TODAS entradas | Igual onde **ambas** especificadas |
| Condição próx. estado | Equivalentes para TODAS | Compatíveis onde **ambos** especificados |
| Transitividade | Sim (relação de equivalência) | **Não** |
| Resultado | Classes de equivalência únicas | Classes maximais (podem se sobrepor) |
| Restrição extra | Não | Conjunto deve ser **completo e fechado** |
| Don't care (`–`) | Não existe | Sim, em saídas e próximos estados |

---

## 7. Resumo do Procedimento

```
Tabela com don't cares (–)
         ↓
1. TABELA DE IMPLICAÇÃO
   - X onde saídas especificadas conflitam
   - Pares implicados onde são compatíveis
   - Propagar X
         ↓
2. CLASSES DE COMPATÍVEIS MAXIMAIS
   (maior subconjunto onde todo par é compatível)
         ↓
3. ESCOLHER SUBCONJUNTO
   - Completo (cobre todos os estados)
   - Fechado (próximos estados cobertos)
   - Mínimo (menor número de classes)
         ↓
4. TABELA MINIMIZADA
   (substituir classes por estados únicos a, b, c...)
```

---

## Conexão com Outras Aulas

- **Aula 4 (Don't Care):** O mesmo conceito aplicado em tabelas de estados em vez de mapas de Karnaugh
- **Aula 12:** O procedimento é análogo (tabela de implicação), mas a condição de compatibilidade é mais flexível que equivalência, e o resultado não são classes disjuntas
- **Aula 10/11:** A tabela mínima resultante alimenta o projeto completo (designação de estados → tabela de transição → equações → circuito)
