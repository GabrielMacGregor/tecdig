# Flashcards — Técnicas Digitais (20261)

> Leia a pergunta, pense na resposta, depois veja o gabarito.

---

## Aula 1 — Sistemas Digitais

**Q: O que é um sistema digital?**
A: Um sistema onde a informação é representada por sinais que só assumem valores discretos (ex: 0 e 1 no sistema binário).

**Q: Qual a diferença entre circuito combinacional e sequencial?**
A: Combinacional: a saída depende **apenas** das entradas atuais. Sequencial: a saída depende das entradas **e do estado anterior** (memória).

---

## Aula 2 — Funções Verdade

**Q: O que é uma Função Verdade?**
A: Uma tabela que define a saída de um sistema combinacional para todas as combinações possíveis de entrada.

**Q: Quais são as etapas para projetar um sistema combinacional?**
A: 1) Encontrar a Função Verdade; 2) Minimizar a função; 3) Implementar o circuito.

---

## Aula 3 — Mintermos e Maxtermos

**Q: O que são Mintermos?**
A: Combinações de entrada para as quais a saída da função é **1**. Usados na forma SDP (Soma de Produtos).

**Q: O que são Maxtermos?**
A: Combinações de entrada para as quais a saída da função é **0**. Usados na forma PDS (Produto de Somas).

**Q: O que é SDP (Soma de Produtos)?**
A: Forma canônica onde a função é expressa como uma soma (OR) de produtos (AND) dos mintermos.

**Q: O que é PDS (Produto de Somas)?**
A: Forma canônica onde a função é expressa como um produto (AND) de somas (OR) dos maxtermos.

---

## Aula 4 — Don't Care e Karnaugh

**Q: O que significa "Don't Care" (X) em um mapa de Karnaugh?**
A: Condições de entrada que nunca ocorrem ou cujo valor de saída é irrelevante. Podem ser tratadas como 0 ou 1 para obter melhor minimização.

**Q: Quando usar Don't Care?**
A: Quando existem combinações de entrada impossíveis (ex: dígitos BCD — só existem de 0 a 9, combinações 10–15 são Don't Care).

---

## Aula 5 — Multiplexador (MUX)

**Q: O que é um Multiplexador?**
A: Dispositivo que seleciona qual sinal de entrada será conectado à saída, controlado por entradas de seleção.

**Q: Quantas entradas de seleção um MUX 8x1 precisa?**
A: 3 entradas de seleção (2³ = 8 combinações).

**Q: O que é um Decodificador?**
A: Dispositivo que ativa uma única saída correspondente ao código binário presente na entrada.

---

## Aula 6 — Demultiplexador e Codificador

**Q: O que é um Demultiplexador?**
A: Dispositivo que direciona o sinal da entrada para uma das saídas selecionadas pelas entradas de controle.

**Q: Qual a relação entre Decodificador e Demultiplexador?**
A: O mesmo circuito implementa ambos — no DEMUX, o ENABLE é usado como entrada de dado.

**Q: O que é um Codificador?**
A: Dispositivo que indica na saída (código binário) qual linha de entrada está ativa.

---

## Aula 7 — Flip-Flops

**Q: Qual é a entrada proibida do Flip-Flop RS com gates NOR?**
A: R=1 e S=1 (ambos ativos), pois gera estado indeterminado na saída.

**Q: O que é o Flip-Flop D?**
A: FF com uma única entrada de dados D. Na borda ativa do clock, a saída Q assume o valor de D.

**Q: Qual a diferença entre FF RS e FF JK?**
A: O FF JK elimina o estado proibido: quando J=1 e K=1, a saída **togles** (complementa o estado atual).

**Q: O que é "debounce" de chave?**
A: Técnica usando FF RS para eliminar os múltiplos pulsos causados pelo bouncing mecânico de uma chave.

---

## Aula 8 — Contadores Assíncronos

**Q: O que é um contador assíncrono (ripple counter)?**
A: Contador onde as entradas de clock dos FFs não estão conectadas ao mesmo sinal — cada FF é disparado pela saída do anterior.

**Q: Qual o problema dos contadores assíncronos?**
A: Podem gerar "spikes" (pulsos espúrios) ao decodificar, devido ao atraso de propagação entre os FFs.

**Q: O que faz o CI 7493?**
A: Contador binário de 4 bits (divide por 16) — composto por um divisor por 2 e um por 8.

**Q: O que faz o CI 7490?**
A: Contador decimal de 4 bits (divide por 10).

---

## Aula 9 — Registradores de Deslocamento

**Q: O que é um Registrador de Deslocamento (Shift Register)?**
A: Associação de FFs tipo D que permite deslocar a informação armazenada a cada pulso de clock.

**Q: Quais os 4 tipos de Registrador de Deslocamento?**
A: Série/Paralelo, Série/Série, Paralelo/Série, Paralelo/Paralelo.

**Q: Para que serve o tipo Paralelo/Série?**
A: Converter um dado paralelo em serial — usado em transmissão de dados.

**Q: Para que serve o tipo Série/Paralelo?**
A: Converter um dado serial em paralelo — usado na recepção de dados seriais.

**Q: O que é o CI 74165?**
A: Registrador de deslocamento paralelo/série de 8 bits.
