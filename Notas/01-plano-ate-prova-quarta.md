# Plano intensivo até a prova — quarta-feira, 24/06/2026

## Objetivo

Chegar à prova sabendo resolver os procedimentos principais sem depender dos resumos.
Até receber a P2 do ano passado, o plano usa as aulas, laboratórios e padrões já presentes
no material.

## Regra destes três dias

- Estudar em blocos de **50 minutos**, com **10 minutos de intervalo**.
- Usar aproximadamente **20% do tempo para revisão** e **80% para exercícios**.
- Tentar cada exercício sem consulta.
- Consultar o resumo somente depois de travar ou terminar.
- Anotar os erros em uma folha separada chamada **Lista de Erros**.
- Não estudar assunto novo na manhã da prova.

## Prioridades atualizadas após análise da P2

### Prioridade A — precisa dominar

1. Projeto de máquinas de estado Mealy e Moore.
2. Tabela de transição e excitação dos FFs JK e D.
3. Contadores síncronos de sequência arbitrária.
4. Minimização de tabela de estados.
5. Contadores 74190, 74192, 74193 e 74293.

### Prioridade B — estudar depois da A

1. Formas de onda de contadores em cascata.
2. Teclado matricial e varredura.
3. MUX, decoder, registradores e displays em projetos por blocos.
4. K-map usado para minimizar as entradas dos flip-flops.

### Prioridade C — baixa incidência nas provas enviadas

1. K-map de 5 variáveis de funções combinacionais.
2. Somador BCD e comparadores.
3. Tri-state, memória, pilha e fila.

---

## Domingo — 21/06

### Bloco 1 — Diagnóstico rápido

Sem consultar, responda:

- Qual a diferença entre SDP e PDS?
- Como organizar um K-map de 5 variáveis?
- Como obter um divisor por N?
- Qual a diferença entre MUX, DEMUX, encoder e decoder?
- Como funcionam FF D e JK?
- Qual a função do 74165?

Marque cada assunto:

- **Verde:** consigo resolver.
- **Amarelo:** lembro parcialmente.
- **Vermelho:** não consigo começar.

### Blocos 2 e 3 — FFs e implementação de uma tabela

Revisar Aulas 7 e 11 e depois resolver a Questão 2 da P2 de 2012:

- tabela de transição;
- entradas J e K;
- entrada D;
- saída Z.

### Bloco 4 — Contador síncrono

Resolver o contador `0, 2, 4, 5, 6, 8, 9, 0...` do G2 Exercício 1.

### Meta de domingo

Conseguir transformar transições em excitações JK/D e preencher os mapas sem consultar.

---

## Segunda-feira — 22/06

### Bloco 1 — Projeto de máquinas

Revisar Aula 10 e resolver o início da máquina Mealy dos sensores da P2 de 2012.

### Bloco 2 — Máquina Moore

Montar o diagrama da máquina das câmeras:

- códigos 422 e 131;
- CLR;
- duas tentativas;
- bloqueio.

### Bloco 3 — Minimização

Refazer a tabela de implicação de `G2_202EXE2.PDF` sem olhar a solução manuscrita.

### Bloco 4 — FF desconhecido

Resolver a questão do contador com FF WZ:

- derivar primeiro a tabela de excitação;
- montar apenas W2 e Z2.

### Meta de segunda

Conseguir criar estados a partir de texto e minimizar uma tabela completamente especificada.

---

## Terça-feira — 23/06

### Bloco 1 — 74190/74192/74193

Revisar o Lab 6 e os datasheets:

- LOAD;
- entradas paralelas;
- clock UP/DOWN;
- carry/borrow;
- cascata;
- níveis ativos.

### Bloco 2 — Contador com limites

Projetar uma das questões:

- 7–17 ou 7–15;
- 6–18 ou 6–21;
- modo crescente/decrescente;
- uma entrada externa de clock.

### Bloco 3 — Contadores assíncronos e ondas

Resolver:

- contador 0–39 com 74293;
- set em 9 e reset em 17;
- forma de onda dos dois 74LS90.

### Bloco 4 — Teclado e sistema

Estudar a questão do monitor P0–P8 e desenhar somente o diagrama em blocos. Depois revisar
a varredura do teclado 3×3 da Aula 14.

### Bloco 5 — Simulado da P2

Em 2 horas, resolver:

1. uma máquina Mealy ou Moore;
2. uma implementação com JK/D;
3. um contador 74192 com limites.

### Meta de terça

Resolver as três famílias principais de questões dentro do tempo, sem consultar o método.

---

## Quarta-feira — 24/06, antes da prova

Tempo recomendado: **60 a 90 minutos**, encerrando o estudo pesado com antecedência.

### Revisão de 30 minutos

Repassar somente:

- checklist de K-map;
- tabelas de FF D e JK;
- bordas e sinais ativos em LOW;
- sequência de projeto em blocos;
- lista pessoal de erros.

### Exercícios de aquecimento

Faça apenas exercícios curtos:

1. localizar quatro células em um K-map de 5 variáveis;
2. desenhar dois ciclos de um contador JK;
3. identificar os bits fixos de um intervalo;
4. dizer quantos bits, FFs ou linhas de seleção são necessários.

### O que não fazer

- Não iniciar conteúdo novo.
- Não resolver uma prova inteira.
- Não decorar circuitos sem entender os blocos.
- Não continuar estudando se estiver apenas repetindo erros por cansaço.

---

## Se houver menos tempo disponível

Com apenas **2 horas por dia**, faça nesta ordem:

1. Máquina de estados.
2. Excitação JK/D e K-map das entradas.
3. Contador síncrono de sequência arbitrária.
4. Minimização de estados.
5. 74192/74190/74193 e formas de onda.

Com apenas **1 hora por dia**, estude um assunto e resolva uma questão completa, em vez de
ler vários resumos superficialmente.

## Lista de erros

Preencha durante o estudo:

| Questão | Meu erro | Regra correta | Como evitar |
|---|---|---|---|
|  |  |  |  |
|  |  |  |  |
|  |  |  |  |

## Ajuste quando chegar a P2 anterior

Quando a prova for adicionada:

1. identificar quais aulas realmente aparecem;
2. comparar o peso das questões;
3. substituir conteúdos de Prioridade C pelos temas mais recorrentes;
4. montar um simulado final no mesmo formato;
5. atualizar o plano de terça-feira e a revisão de quarta.
