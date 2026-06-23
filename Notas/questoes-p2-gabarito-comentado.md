# Gabarito comentado da P2 e dos G2

Este guia serve para você saber:

- o que cada questão pede;
- quais aulas rever antes de tentar;
- qual é a ideia central da resposta.

Ele não substitui os resumos. Ele te diz quais ver primeiro.

## Ordem de estudo mais útil

1. `Aula 10` - Mealy, Moore, diagrama e tabela de estados.
2. `Aula 11` - designação de estados, tabela de transição, JK, D e Karnaugh.
3. `Aula 7` - flip-flops D e JK.
4. `Aula 8` - contadores, 74293, 7490, 74192 e ondas.
5. `Aula 12` - minimização de estados.
6. `Lab 6` - projetos com 74190, 74192 e 74193.
7. `Aula 14` - teclado matricial e varredura, se sobrar tempo.

---

## Mapa rápido

| Questão | Aulas principais | Ideia da resposta |
|---|---|---|
| G2 2020.2 Ex. 1, Q1 | 10, 11 | Moore, prefixos, tentativas, CLR, bloqueio |
| G2 2020.2 Ex. 1, Q2 | 7, 11, 4 | contador síncrono em 4 bits, JK, Karnaugh |
| G2 2020.2 Ex. 2, Q1 | 12 | tabela de implicação e classes equivalentes |
| P2 2012, Q1 | 10 | Mealy com sensores A, B, C |
| P2 2012, Q2 | 11, 7, 4 | designação, JK/D e saída mínima |
| P2 2012, Q3a | 8, 7 | 74293 com FF D set/reset |
| P2 2012, Q3b | 8, Lab 6 | 74192 com um clock externo e limites |
| P2 2012, Q3c | 8 | forma de onda com dois 74LS90 |

---

## G2 2020.2 - Exercício 1

### Q1 - Câmeras com código e bloqueio

### O que a questão quer

- diagrama de estados;
- tabela de estados;
- modelo de Moore;
- no máximo 22 estados.

### O que revisar

- `Aula 10`: Moore, diagrama, tabela de estados;
- `Aula 11`: se quiser pensar na implementação depois;
- `Aula 7`: memória por flip-flops, como apoio mental.

### Gabarito comentado

- O modelo certo é **Moore**, porque a saída `A` ou `D` deve ficar associada ao estado final reconhecido.
- A máquina precisa memorizar:
  - o prefixo já digitado de `422` ou `131`;
  - a quantidade de tentativas;
  - se está bloqueada;
  - o efeito especial de `CLR`.
- O caminho de sucesso deve vir antes dos erros.
- `CLR` só passa a valer depois de os dois primeiros dígitos já existirem na memória parcial.
- Se o código certo termina, a máquina volta a ficar pronta para um novo comando.

### Como atacar

1. Desenhe primeiro os prefixos de `422` e `131`.
2. Depois acrescente a primeira tentativa, a segunda tentativa e o bloqueio.
3. Por último, adicione `CLR` e os retornos.

---

### Q2 - Contador síncrono com JK

### O que a questão quer

- tabela de estados;
- tabela de excitação do JK;
- mapas de Karnaugh;
- equações das entradas dos FFs;
- simulação do circuito.

### O que revisar

- `Aula 7`: tabela do JK e funcionamento do FF D;
- `Aula 11`: tabela de transição e mapas para JK/D;
- `Aula 4`: se quiser relembrar Karnaugh.

### Gabarito comentado

- A sequência é:

  `0 -> 2 -> 4 -> 5 -> 6 -> 8 -> 9 -> 0`

- Como aparece o valor `9`, a representação natural fica em **4 bits**.
- Você monta a tabela atual -> próximo em binário.
- Depois aplica a tabela de excitação do JK bit a bit.
- Cada entrada `J` e `K` é simplificada em mapa separado.

### Como atacar

1. Escreva os valores em binário de 4 bits.
2. Monte a sequência atual/próximo.
3. Tire as entradas de cada flip-flop pela tabela do JK.
4. Simplifique com Karnaugh.

---

## G2 2020.2 - Exercício 2

### Q1 - Minimização de estados

### O que a questão quer

- reduzir uma máquina de 8 estados para o menor número possível de classes;
- construir a tabela reduzida.

### O que revisar

- `Aula 12`: minimização de estados;
- `Aula 10`: leitura da tabela de estados;
- `Aula 13`: só se aparecer máquina incompletamente especificada, o que não é o caso principal aqui.

### Gabarito comentado

Pelo gabarito que você tem no material, a máquina reduz para **5 classes**:

- `A`
- `B, D, E`
- `F`
- `C, G`
- `H`

### Como pensar

- Compare saídas primeiro.
- Marque os pares incompatíveis.
- Propague os `X` da tabela de implicação.
- Os pares que sobram viram classes equivalentes.

---

## P2 2012.2

### Q1 - Sensores ópticos e direção do cartão

### O que a questão quer

- diagrama de estados;
- tabela de estados;
- modelo de Mealy.

### O que revisar

- `Aula 10`: Mealy, diagrama e tabela de estados.

### Gabarito comentado

- O modelo certo é **Mealy**, porque a saída depende da entrada atual e do estado.
- Os estados precisam guardar o progresso da passagem do cartão.
- O que importa é a **ordem** em que `A`, `B` e `C` aparecem.
- A saída `L1` ou `L2` deve aparecer só quando a direção ficar clara.

### Como atacar

1. Escreva as sequências possíveis dos sensores.
2. Transforme cada prefixo útil em um estado.
3. Gere a saída na transição correta.

---

### Q2 - Máquina sequencial com tabela mínima

### O que a questão quer

- tabela de transição com designação obrigatória;
- equações `J` e `K` de `Y1`;
- equação `D` do mesmo bit;
- equação mínima de saída.

### O que revisar

- `Aula 11`: designação, tabela de transição, JK, D e Karnaugh;
- `Aula 7`: tabela de excitação do JK;
- `Aula 4`: mapas de Karnaugh.

### Gabarito comentado

A designação obrigatória é:

- `S0 = 000`
- `S1 = 001`
- `S2 = 011`
- `S3 = 111`
- `S4 = 110`

O que você precisa fazer:

- substituir nomes por bits;
- separar estado atual e próximo estado;
- tirar `J` e `K` de `Y1` pela tabela de excitação;
- tirar `D` do mesmo bit copiando o próximo estado;
- minimizar a saída com a tabela.

### Como atacar

1. Monte a tabela em binário.
2. Trabalhe um bit por vez.
3. Faça um mapa para cada entrada de FF.
4. Minimize a saída no fim.

---

### Q3a - Contador binário de 0 a 39 com 74293

### O que a questão quer

- contador binário até 39;
- FF D setado em 9;
- FF D resetado em 17.

### O que revisar

- `Aula 8`: 74293 e contadores assíncronos;
- `Aula 7`: FF D;
- `Aula 4`: se quiser lembrar detecção por portas.

### Gabarito comentado

- O `74293` é um contador em ripple.
- Para contar até `39`, você precisa de bits suficientes para o número máximo.
- O FF D funciona como memória auxiliar:
  - seta quando a contagem chega em `9`;
  - reseta quando chega em `17`.

### Como atacar

1. Identifique como montar o contador de 0 a 39.
2. Detecte `9` e `17`.
3. Use essas detecções para comandar o FF D.

---

### Q3b - Contador com 74192, um clock e faixa selecionável

### O que a questão quer

- contar de `7` a `17` ou de `7` a `15`;
- contar crescente ou decrescente;
- usar um único clock externo;
- usar o `74192`.

### O que revisar

- `Aula 8`: 74192, contagem up/down, seleção de clock;
- `Lab 6`: limites e cascata de contadores.

### Gabarito comentado

- O `74192` tem clocks separados para subir e descer.
- Como a questão só dá um clock externo, você precisa roteá-lo para `UP` ou `DOWN`.
- `M` escolhe a direção.
- `T` escolhe o limite superior.
- Quando chega no limite, o circuito carrega o outro extremo.

### Como atacar

1. Decida qual clock fica ativo em cada modo.
2. Configure os limites `7`, `15` ou `17`.
3. Faça a recarga no momento certo.

---

### Q3c - Diagrama temporal com dois 74LS90

### O que a questão quer

- acompanhar o comportamento temporal;
- respeitar a cascata dos contadores;
- partir do estado `01011010`.

### O que revisar

- `Aula 8`: contadores assíncronos e formas de onda.

### Gabarito comentado

- O `74LS90` não muda todos os bits ao mesmo tempo.
- Primeiro muda o estágio que recebe o clock.
- Depois o próximo estágio responde.
- O jeito seguro é montar uma tabela pulso a pulso antes de desenhar a onda.

### Como atacar

1. Anote o estado inicial.
2. Atualize o primeiro contador.
3. Veja se há carry para o segundo.
4. Só então desenhe a forma de onda.

---

## Resumo final

Se você só puder rever poucas aulas, fique com esta ordem:

1. `Aula 10` - máquinas de estado.
2. `Aula 11` - transição, JK, D e Karnaugh.
3. `Aula 7` - FFs.
4. `Aula 8` - contadores e formas de onda.
5. `Aula 12` - minimização.
6. `Lab 6` - 74190/74192/74193 com limites.

