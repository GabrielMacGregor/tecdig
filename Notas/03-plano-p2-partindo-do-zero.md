# Plano para aprender toda a P2 partindo do zero

## Objetivo

Aprender, em ordem, os conhecimentos necessários para resolver as provas enviadas:

1. entender estados, clock e flip-flops;
2. ler e criar máquinas Mealy e Moore;
3. transformar uma máquina em circuito;
4. minimizar tabelas de estados;
5. projetar contadores síncronos;
6. usar contadores integrados 74190, 74192, 74193 e 74293;
7. resolver formas de onda e projetos com teclado.

Este plano foi pensado para a prova de **quarta-feira, 24/06/2026**.

## Como estudar

Cada bloco dura **50 minutos**, seguido por 10 minutos de intervalo:

- **15 min:** aprender o conceito pelo resumo;
- **15 min:** copiar e explicar um exemplo;
- **20 min:** resolver algo sem consultar.

Ao terminar, escreva em uma folha:

```text
O que aprendi?
O que ainda confundo?
Qual erro não posso repetir?
```

Não avance se você não conseguir explicar o assunto com suas próprias palavras.

---

# Etapa 1 — Fundamentos indispensáveis

## Bloco 1 — Binário e lógica mínima

### Aprender

- Converter decimal para binário e binário para decimal.
- Identificar bits mais e menos significativos.
- Operações AND, OR e NOT.
- Ler uma tabela verdade.
- Entender código Gray no K-map: `00, 01, 11, 10`.

### Você deve conseguir

- converter rapidamente números de 0 a 31;
- reconhecer 7, 15, 17, 18, 21 e 24 em binário;
- identificar quais bits estão em 1 num determinado estado.

### Material

- Resumo-Aula1.
- Resumo-Aula2.
- Início do Resumo-Aula4.

### Exercício

Monte uma tabela decimal/binário de 0 a 24.

---

## Bloco 2 — O que é um circuito sequencial

### Aprender

- Combinacional: saída depende somente das entradas atuais.
- Sequencial: saída depende das entradas e do estado armazenado.
- Estado atual `Q(t)`.
- Próximo estado `Q(t+1)`.
- Clock e borda ativa.
- Entradas assíncronas preset e clear.

### Modelo mental

```text
entradas + estado atual
        ↓
lógica de próximo estado
        ↓
flip-flops — recebem o clock
        ↓
novo estado
```

### Material

- Resumo-Aula7, seções 1 e 3.

### Você deve conseguir

Explicar por que um circuito combinacional não consegue lembrar quantos dígitos de uma
senha já foram digitados.

---

## Bloco 3 — Flip-flops D e JK

### FF D

```text
Q(t+1) = D
```

O valor colocado em D será armazenado no próximo clock.

### FF JK

| J | K | Próximo estado |
|---|---|---|
| 0 | 0 | mantém |
| 0 | 1 | 0 |
| 1 | 0 | 1 |
| 1 | 1 | inverte |

### Tabela de excitação do JK

| `Q → Q+` | J | K |
|---|---|---|
| 0 → 0 | 0 | X |
| 0 → 1 | 1 | X |
| 1 → 0 | X | 1 |
| 1 → 1 | X | 0 |

### Material

- Resumo-Aula7.
- Resumo-Aula11.

### Exercício

Para cada transição abaixo, determine D, J e K:

```text
0→0
0→1
1→0
1→1
```

### Critério de avanço

Responder às quatro transições sem consultar.

---

# Etapa 2 — Aprender máquinas de estado

## Bloco 4 — Estados e transições

### Aprender

Um estado representa a informação do passado que ainda é necessária.

Exemplo: detector da sequência `101`.

```text
q0: ainda não reconheceu nada
q1: recebeu 1
q2: recebeu 10
```

### Procedimento

1. Identificar a sequência ou comportamento.
2. Criar o estado inicial.
3. Criar estados para os progressos parciais.
4. Desenhar as transições corretas.
5. Completar as transições de erro.

### Material

- Resumo-Aula10.

### Exercício

Criar o diagrama de um detector de `101`, primeiro sem interseção.

---

## Bloco 5 — Mealy e Moore

### Mealy

- Saída depende do estado e da entrada.
- Saída fica nas setas.
- Rótulo: `entrada/saída`.

### Moore

- Saída depende somente do estado.
- Saída fica dentro do estado.
- Rótulo: `estado/saída`.

### Regra para memorizar

```text
Mealy → saída na seta
Moore → saída no círculo
```

### Material

- Resumo-Aula10, seções 2–4.

### Exercício

Fazer o detector de `101` em Mealy e Moore e comparar a quantidade de estados.

### Critério de avanço

Conseguir olhar um diagrama e identificar imediatamente se é Mealy ou Moore.

---

## Bloco 6 — Criar máquina a partir de texto

### Método

```text
1. Sublinhar entradas.
2. Circular saídas.
3. Listar comportamentos válidos.
4. Definir o que precisa ser lembrado.
5. Criar estados.
6. Desenhar o caminho de sucesso.
7. Completar erro, reset e retorno.
8. Testar uma sequência completa.
```

### Primeiro exercício

Resolver a máquina dos três sensores da P2 de 2012:

- seguir inicialmente apenas esquerda→direita;
- depois construir o caminho oposto;
- usar modelo de Mealy;
- deixar a tabela para depois.

### Segundo exercício

Máquina Moore das câmeras, começando apenas pelo código 422. Acrescentar 131, CLR e
tentativas somente após o caminho básico funcionar.

---

# Etapa 3 — Da máquina ao circuito

## Bloco 7 — Diagrama para tabela de estados

### Aprender

Cada linha representa um estado atual. Cada coluna representa uma entrada ou combinação de
entradas. A célula informa:

```text
próximo estado, saída
```

### Exercício

Converter o detector de `101` e a máquina dos sensores em tabelas.

### Checklist

- Toda entrada possível possui uma transição?
- A saída foi colocada no lugar correto?
- O estado inicial está identificado?
- Existe retorno após concluir a operação?

---

## Bloco 8 — Designação e tabela de transição

### Aprender

Estados com nomes precisam receber códigos binários.

```text
4 estados → 2 FFs
5 a 8 estados → 3 FFs
```

Fórmula:

```text
número de FFs = teto(log2(número de estados))
```

### Procedimento

1. Criar uma tabela estado → código.
2. Substituir os nomes pelos códigos.
3. Separar estado atual e próximo estado.
4. Ler cada bit do próximo estado.

### Material

- Resumo-Aula11 até “Tabela de Transição”.

### Exercício

Refazer a designação obrigatória da Questão 2 da P2 de 2012.

---

## Bloco 9 — Tabela de transição para entradas dos FFs

### Com FF D

O bit seguinte é a própria entrada:

```text
D2 = y2+
D1 = y1+
D0 = y0+
```

### Com FF JK

Para cada bit:

1. observar `Q atual`;
2. observar `Q seguinte`;
3. consultar a tabela de excitação;
4. preencher J e K.

### Exercício principal

Resolver completamente a Questão 2 da P2 de 2012:

- tabela de transição;
- J e K de `y1`;
- D de `y2`;
- saída Z.

Não copie os mapas já preenchidos.

---

## Bloco 10 — K-map para equações dos flip-flops

### Aprender

- Cada entrada de FF ganha seu próprio mapa.
- Os X da tabela de excitação são don't cares.
- Estados binários não usados também podem ser X, se o enunciado permitir.
- A saída Z possui um mapa separado.

### Material

- Resumo-Aula4 para regras do K-map.
- Resumo-Aula11 para aplicação nos FFs.

### Critério de avanço

Conseguir explicar de qual coluna da tabela veio cada célula do mapa.

---

# Etapa 4 — Contadores projetados com flip-flops

## Bloco 11 — Contador síncrono de sequência arbitrária

### Aprender

Todos os FFs recebem o mesmo clock. A lógica combinacional determina o próximo valor.

### Procedimento

```text
sequência decimal
→ sequência binária
→ tabela atual/próximo
→ excitação
→ K-maps
→ equações
→ circuito
```

### Exercício principal

Projetar com JK:

```text
0, 2, 4, 5, 6, 8, 9, 0...
```

### Depois

Resolver o contador controlado por S da questão com FF WZ.

---

## Bloco 12 — Como lidar com um FF desconhecido

### Ideia

Se a prova fornecer a tabela característica, você consegue criar a tabela de excitação.

Para cada transição:

```text
Q atual → Q seguinte
```

procure quais entradas produzem o resultado desejado.

### Exercício

Derivar a tabela de excitação do FF WZ e calcular somente W2 e Z2.

### Critério de avanço

Conseguir resolver sem tentar transformar o WZ em JK.

---

# Etapa 5 — Minimização de estados

## Bloco 13 — Estados equivalentes

Dois estados são equivalentes quando:

1. produzem as mesmas saídas para todas as entradas;
2. seus próximos estados também são equivalentes.

### Método da tabela de implicação

```text
1. Criar a tabela triangular.
2. Marcar X quando as saídas diferirem.
3. Anotar os pares implicados.
4. Propagar os X.
5. Identificar os pares restantes.
6. Formar classes.
7. Construir a tabela reduzida.
```

### Material

- Resumo-Aula12.

### Exercício

Resolver a tabela A–H de `G2_202EXE2.PDF` e somente depois comparar com `G2_Q2_1.pdf`.

---

## Bloco 14 — Máquina incompletamente especificada

### Aprender apenas depois da minimização comum

- Traço significa don't care.
- Procura-se compatibilidade, não equivalência.
- Compatibilidade não é transitiva.
- O conjunto final deve ser completo e fechado.

### Material

- Resumo-Aula13.

### Prioridade

Média. Estude se os blocos anteriores já estiverem seguros.

---

# Etapa 6 — Contadores integrados

## Bloco 15 — Conceito de contador integrado

### Aprender

- Contador binário versus contador BCD.
- Contar para cima e para baixo.
- Carga paralela.
- Reset.
- Carry e borrow.
- Cascata de unidades e dezenas.

### CIs da prova

| CI | Função |
|---|---|
| 74190 | BCD up/down, um clock e entrada de direção |
| 74192 | BCD up/down, clocks separados |
| 74193 | binário up/down, clocks separados |
| 74293 | contador binário assíncrono |
| 7490/74LS90 | contador decimal assíncrono |

### Material

- Resumo-Aula8.
- Lab 6.
- Datasheets fornecidos.

---

## Bloco 16 — 74192 com um clock externo

### Problema recorrente

O 74192 possui clock UP e clock DOWN, mas o enunciado fornece uma única entrada de clock.

### Solução conceitual

- Modo crescente: encaminhar clock para UP.
- Modo decrescente: encaminhar clock para DOWN.
- Manter o outro clock no nível inativo exigido.
- Usar a chave M para controlar as portas.

### Atenção

Não conecte simplesmente o clock aos dois pinos.

### Exercício

Desenhar somente o bloco de seleção de clock, sem ainda fazer os limites.

---

## Bloco 17 — Contador com limites

### Exemplo

Contar de 7 a 17:

```text
crescente: 7, 8, ..., 17, 7
decrescente: 17, 16, ..., 7, 17
```

### Método

1. Dois 74192 para unidades e dezenas.
2. Programar as entradas paralelas.
3. Detectar limite superior no crescente.
4. Detectar limite inferior no decrescente.
5. Acionar LOAD para carregar o outro extremo.
6. Usar T para selecionar o limite superior.
7. Usar M para selecionar direção e condição de recarga.

### Exercícios

- 7–17 ou 7–15.
- 6–18 ou 6–21.
- Lab 6: 5–24.

### Testes

- máximo → mínimo;
- mínimo → máximo;
- crescente e decrescente;
- cada posição da chave T.

---

## Bloco 18 — Contadores assíncronos e formas de onda

### Aprender

- Cada estágio recebe o clock do anterior.
- As mudanças não são simultâneas.
- É necessário conhecer a borda ativa.
- O contador seguinte muda somente no carry/borda correspondente.

### Exercícios

- Contador 0–39 com 74293.
- Setar FF D em 9 e resetar em 17.
- Diagrama temporal dos dois 74LS90 da P2 de 2012.

### Método para ondas

Monte primeiro:

| Pulso | Contador 1 | Contador 2 | Saída pedida |
|---|---|---|---|

Depois desenhe a onda.

---

# Etapa 7 — Teclado e projeto em blocos

## Bloco 19 — Teclado matricial

### Aprender

- Linhas e colunas.
- Varredura de uma coluna por vez.
- Pull-up.
- Detecção de tecla.
- Debounce.
- Conversão linha+coluna para código.

### Material

- Resumo-Aula14.

### Exercício

Desenhar os blocos do teclado 3×3:

```text
clock
→ contador módulo 3
→ decoder de coluna
→ teclado
→ leitura das linhas
→ codificador
→ código 0–8 + ST
```

---

## Bloco 20 — Projeto do monitor P0–P8

### Dividir em blocos

```text
CONFIG
→ registrador do limite

teclado ou teclas ↑/↓
→ seleção direta ou contador
→ número da variável

número da variável
→ MUX P0–P8
→ valor da variável

número + valor
→ displays
```

### Aprender

- Guardar configuração em registrador.
- Selecionar modo direto ou sequencial.
- Contagem circular.
- MUX de várias entradas.
- Exibição BCD.

### Prioridade

Faça depois de dominar máquinas e contadores.

---

# Cronograma até quarta-feira

## Domingo, 21/06 — Base e implementação

- [ ] Bloco 1 — binário.
- [ ] Bloco 2 — circuito sequencial.
- [ ] Bloco 3 — FF D e JK.
- [ ] Bloco 4 — estados.
- [ ] Bloco 5 — Mealy/Moore.
- [ ] Blocos 7–9 — tabela, designação e excitação.

### Entrega do dia

Resolver a Questão 2 da P2 de 2012, ainda que consultando os resumos.

## Segunda-feira, 22/06 — Máquinas e minimização

- [ ] Bloco 6 — máquina a partir de texto.
- [ ] Máquina dos sensores.
- [ ] Máquina das câmeras.
- [ ] Bloco 10 — K-map dos FFs.
- [ ] Bloco 11 — contador síncrono.
- [ ] Bloco 12 — FF WZ.
- [ ] Bloco 13 — minimização.

### Entrega do dia

Fazer um diagrama Mealy, um Moore e uma tabela de implicação sem consultar o procedimento.

## Terça-feira, 23/06 — Contadores e prova

- [ ] Blocos 15–17 — 74190/74192/74193 e limites.
- [ ] Bloco 18 — 74293/7490 e formas de onda.
- [ ] Blocos 19–20 — teclado e monitor, se houver tempo.
- [ ] Simulado de duas horas.

### Simulado

1. Máquina de estados — 45 minutos.
2. Implementação JK/D ou contador síncrono — 40 minutos.
3. Contador 74192 com limites — 35 minutos.

### Entrega do dia

Lista final contendo somente erros que ainda precisam ser revisados.

## Quarta-feira, 24/06 — Revisão

### Revisar

- tabela de excitação JK;
- `D = Q+`;
- Mealy versus Moore;
- fluxo completo de projeto;
- tabela de implicação;
- funções e polaridades do 74192;
- sua lista de erros.

### Não fazer

- começar matéria nova;
- estudar detalhes de baixa prioridade;
- refazer todas as provas;
- decorar circuitos sem entender o fluxo.

---

# Plano mínimo caso o tempo fique curto

Se não for possível completar tudo, siga esta ordem:

1. FF D e JK.
2. Mealy e Moore.
3. Tabela de transição e excitação.
4. Contador síncrono.
5. 74192 com limites.
6. Minimização de estados.
7. Formas de onda.
8. Teclado.

# Checklist final

- [ ] Sei converter números importantes para binário.
- [ ] Sei explicar estado atual e próximo estado.
- [ ] Sei usar FF D e a excitação do JK.
- [ ] Sei distinguir Mealy de Moore.
- [ ] Sei criar estados a partir de um enunciado.
- [ ] Sei converter diagrama em tabela.
- [ ] Sei criar a tabela de transição.
- [ ] Sei montar mapas para as entradas dos FFs.
- [ ] Sei projetar um contador síncrono.
- [ ] Sei minimizar uma tabela por implicação.
- [ ] Sei explicar LOAD, UP, DOWN, carry e borrow do 74192.
- [ ] Sei projetar contagem entre dois limites.
- [ ] Sei calcular formas de onda pulso a pulso.
- [ ] Sei dividir o monitor e o teclado em blocos.

