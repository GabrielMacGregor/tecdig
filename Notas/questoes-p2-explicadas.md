# Questões da P2 e dos G2 explicadas

Este arquivo junta as questões que aparecem na P2 e nos G2 usados no estudo.

Objetivo:

- deixar cada enunciado mais fácil de ler;
- explicar o que a questão quer de verdade;
- apontar o caminho de solução sem precisar abrir outro resumo a toda hora.

Se você quiser a versão mais direta, com aulas para revisar antes de responder, veja também `questoes-p2-gabarito-comentado.md`.

## Como usar

- Leia primeiro a parte "o que a questão quer".
- Depois veja "como atacar".
- Só então volte para os resumos, se quiser conferir detalhes de fórmula.

---

## G2 2020.2, Exercício 1

### Questão 1 - Câmeras com código e bloqueio

### O enunciado, em palavras simples

O sistema tem:

- um teclado com `1`, `2`, `3`, `4` e `CLR`;
- duas saídas, `A` e `D`;
- um código para ativar as câmeras: `422`;
- um código para desativar: `131`;
- duas tentativas antes de bloquear;
- `CLR` que apaga o último dígito, mas só depois que pelo menos dois dígitos já foram digitados.

### O que a questão quer

- desenhar o diagrama de estados;
- montar a tabela de estados;
- usar o modelo de Moore;
- não passar de 22 estados.

### Como pensar

- Cada estado precisa guardar quanto do código já foi digitado.
- Além disso, a máquina precisa lembrar se está na primeira tentativa, na segunda tentativa ou bloqueada.
- `CLR` não é uma tecla comum: ela desfaz progresso parcial.
- Se o código correto terminar, a saída correspondente sobe e a máquina volta a ficar pronta para outro comando.

### Palavras-chave

- memória do prefixo;
- tentativa;
- bloqueio;
- retorno ao início;
- saída associada ao estado.

---

### Questão 2 - Contador síncrono com JK

### O enunciado, em palavras simples

O contador deve repetir a sequência:

`0, 2, 4, 5, 6, 8, 9, 0, ...`

e a implementação deve usar flip-flops JK.

### O que a questão quer

- tabela de estados;
- tabela de excitação do JK;
- mapas de Karnaugh;
- equações das entradas dos flip-flops;
- simulação do circuito.

### Como pensar

- Isso é um contador síncrono, então todos os FFs recebem o mesmo clock.
- A sequência de saída é fixa, então você precisa montar o ciclo atual -> próximo estado.
- Depois transforma cada transição em excitação JK.
- Se a sequência tiver 7 estados, você precisa de pelo menos 3 bits de estado.

### Passo a passo

1. Escreva os estados em binário com a mesma largura.
2. Monte a tabela atual/próximo.
3. Aplique a tabela de excitação do JK bit a bit.
4. Preencha os mapas.
5. Simplifique as equações.

---

## G2 2020.2, Exercício 2

### Questão 1 - Minimização de estados

### O enunciado, em palavras simples

Você recebe uma tabela de máquina sequencial com:

- 8 estados, `A` a `H`;
- 4 combinações de entrada, `00`, `01`, `10` e `11`;
- saídas associadas a cada transição.

### O que a questão quer

- reduzir a máquina para o menor número possível de estados;
- construir a tabela reduzida;
- manter o comportamento externo igual.

### Como pensar

- Compare as saídas primeiro.
- Se duas linhas já diferem em alguma saída, o par é incompatível.
- Se as saídas batem, veja para quais estados cada transição aponta.
- Se os próximos estados dependem de um par incompatível, esse par também cai.
- No final, os estados que sobreviverem formam as classes equivalentes.

### Palavra-chave

- tabela de implicação.

---

## P2 2012.2

### Questão 1 - Sensores ópticos e direção do cartão

### O enunciado, em palavras simples

Há três sensores ópticos, `A`, `B` e `C`.

O cartão pode passar:

- da esquerda para a direita;
- da direita para a esquerda.

O sistema deve gerar:

- um pulso na linha 2 quando o cartão vai da esquerda para a direita;
- um pulso na linha 1 quando o cartão vai da direita para a esquerda.

### O que a questão quer

- diagrama de estados;
- tabela de estados;
- modelo de Mealy.

### Como pensar

- A saída depende da trajetória e da entrada atual, então Mealy faz mais sentido.
- Os estados não representam só "qual sensor está ligado", mas sim o progresso parcial da passagem.
- Como o cartão tem comprimento maior que a distância entre `A` e `C`, os sensores podem ficar ativos ao mesmo tempo.
- A máquina precisa reconhecer a ordem dos sinais, não só o valor instantâneo.

### Passo mental

1. Leia as formas de onda.
2. Escreva a sequência de combinações `ABC`.
3. Crie um estado para cada prefixo útil.
4. Marque o instante em que a direção ficou clara.
5. Gere o pulso só no fim da passagem.

---

### Questão 2 - Máquina sequencial com tabela mínima

### O enunciado, em palavras simples

A prova fornece uma tabela mínima com:

- estados `S0` a `S4`;
- entradas `a` e `b` na ordem `00`, `01`, `11`, `10`;
- uma saída `Z`;
- uma codificação obrigatória:
  - `S0 = 000`
  - `S1 = 001`
  - `S2 = 011`
  - `S3 = 111`
  - `S4 = 110`

### O que a questão quer

- montar a tabela de transição com estados binários;
- encontrar as equações mínimas de `J` e `K` do flip-flop `Y1`;
- encontrar a equação mínima de `D` para o mesmo bit;
- encontrar a equação mínima da saída `Z`.

### Como pensar

- Primeiro substitua os nomes dos estados pelos códigos dados.
- Depois leia bit a bit o próximo estado.
- Para o JK, compare o bit atual com o próximo e use a tabela de excitação.
- Para o D, basta copiar o próximo valor do bit.
- A saída `Z` costuma ficar simples quando você olha a tabela de estados inteira.

### O que revisar aqui

- codificação de estados;
- tabela de excitação do JK;
- diferença entre estado atual e próximo estado;
- mapa de Karnaugh com entradas de estado e de entrada.

---

### Questão 3a - Contador binário de 0 a 39 com 74293

### O enunciado, em palavras simples

Você deve projetar um contador binário de `0` a `39` usando `74293`.

Além disso:

- quando o contador chegar em `9`, um flip-flop D deve ser setado;
- quando chegar em `17`, esse flip-flop D deve ser resetado.

### O que a questão quer

- entender como formar o contador com os CIs dados;
- detectar os valores especiais `9` e `17`;
- usar o FF D como sinal de controle ou memória auxiliar.

### Como pensar

- O `74293` é um contador assíncrono em ripple.
- Para contar até `39`, você precisa de bits suficientes para representar esse intervalo.
- O FF D funciona como uma memória simples: ele sobe em `9` e desce em `17`.
- A ideia não é só contar, mas também gerar um sinal que "marca" um intervalo da contagem.

---

### Questão 3b - Contador com 74192, uma entrada de clock e seleção de faixa

### O enunciado, em palavras simples

O circuito deve contar:

- de `7` a `17` quando `T = 0`;
- de `7` a `15` quando `T = 1`.

Além disso:

- `M = 0` significa contagem crescente;
- `M = 1` significa contagem decrescente;
- o circuito deve usar `74192`;
- o circuito deve ter apenas uma entrada de relógio.

### O que a questão quer

- um contador com limites configuráveis;
- controle de direção;
- roteamento correto do clock;
- lógica de carga/recarregamento dos limites.

### Como pensar

- O `74192` tem clocks separados para subir e descer.
- Como a questão quer só um clock externo, você precisa distribuir esse clock para o pino certo dependendo de `M`.
- `T` escolhe qual limite superior será usado.
- Quando o contador atinge o limite, ele precisa carregar o outro extremo.

### Pontos que não podem passar batido

- clock UP e clock DOWN não podem ficar ligados do mesmo jeito;
- a polaridade das entradas de carga precisa ser respeitada;
- os limites são parte central da resposta, não detalhe.

---

### Questão 3c - Diagrama temporal de dois 74LS90

### O enunciado, em palavras simples

Você recebe um circuito com dois contadores `74LS90`.

O estado inicial é:

- `01011010`

com o bit mais significativo ligado ao `Q3` do `U2`.

### O que a questão quer

- desenhar o comportamento temporal;
- acompanhar a contagem pulso a pulso;
- respeitar o ripple de um contador assíncrono.

### Como pensar

- `74LS90` não troca todos os bits ao mesmo tempo.
- Primeiro atualize o estágio que recebe o clock direto.
- Depois veja como o próximo estágio responde.
- Monte uma tabela por pulso antes de desenhar as ondas.

### Passo seguro

1. Anote o estado inicial.
2. Atualize o contador de menor peso.
3. Verifique o carry ou a conexão de cascata.
4. Atualize o próximo contador.
5. Só depois desenhe a forma de onda pedida.

---

## Resumo final por tipo de questão

### Máquina de estados

- sensores;
- câmeras;
- tabela reduzida;
- código com prefixos.

### Contadores

- contador JK de sequência fixa;
- contador binário com 74293;
- contador com limites usando 74192;
- forma de onda com 74LS90.

### O que sempre aparece

- estado atual;
- próximo estado;
- entrada;
- saída;
- tabela;
- mapa de Karnaugh;
- codificação dos estados.
