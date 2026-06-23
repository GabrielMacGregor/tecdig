# Guia do Lab 10 e 11: como montar a copia de memoria

Este arquivo e para voce entender o lab do zero, sem precisar ficar pulando de resumo em resumo.

A ideia central e simples:

1. voce aperta `INIC`;
2. o circuito guarda os parametros `EFI`, `EDI` e `NP`;
3. ele le uma palavra da memoria fonte;
4. ele escreve essa palavra na memoria destino;
5. ele anda com os enderecos;
6. ele repete isso ate terminar.

O Lab 11 faz quase a mesma coisa, mas com uma regra extra: se o dado lido for `F` em hexadecimal, o processo para e esse `F` nao deve ser copiado.

---

## 1. O que o lab quer, em portugues bem direto

### Lab 10

Voce tem:

- um endereco inicial da memoria fonte (`EFI`);
- um endereco inicial da memoria destino (`EDI`);
- um numero de palavras a copiar (`NP`);
- um pulso de partida (`INIC`);
- um sinal de fim (`FIM`).

O circuito precisa fazer isso:

```text
para i = 0 ate NP - 1:
    ler dado da memoria fonte em EFI + i
    escrever esse dado na memoria destino em EDI + i

quando acabar:
    ativar FIM
```

### Lab 11

O Lab 11 e igual, mas com uma trava extra:

```text
se o dado lido for F:
    parar na hora
    nao escrever esse dado
    ativar FIM
```

Ou seja, o Lab 11 tem um "valor sentinela": o `F` diz "acabou".

---

## 2. As aulas que voce precisa rever

Nao precisa decorar tudo. Precisa saber o papel de cada aula.

| Aula | O que voce precisa pegar dela |
|------|-------------------------------|
| Aula 7 | O que e um flip-flop, porque ele guarda estado e porque `FIM` precisa vir de um FF |
| Aula 8 | Como funcionam contadores e como usar a ideia de somar 1 a cada copia |
| Aula 9 | Registradores, carga paralela e como segurar `EFI`, `EDI` e `NP` depois do pulso `INIC` |
| Aula 10 | Como pensar um circuito sequencial como uma maquina de estados |
| Aula 11 | Como transformar a tabela/ideia da maquina de estados em circuito |
| Aula 12 | Opcional, se voce quiser reduzir estados da maquina |
| Aula 15 | Tri-state, barramento, memoria RAM, `CS`, `OE` e `WE` |

Se voce for estudar por prioridade:

1. Aula 15
2. Aula 9
3. Aula 8
4. Aula 10
5. Aula 11
6. Aula 7

Isso porque o lab e, antes de tudo, um problema de memoria + controle.

---

## 3. Como pensar o circuito sem se perder

Pensa no circuito como um robo com tres blocos:

1. um bloco que segura os parametros;
2. um bloco que faz a copia;
3. um bloco que decide quando acabou.

O jeito mais facil de visualizar e separar em **datapath** e **controle**:

```text
        +-------------------+
EFI --->| registro/endereco |----+
        +-------------------+    |
                                    v
                                 RAM fonte ---> dado --->+
                                                        |
        +-------------------+                            v
EDI --->| registro/endereco |----+                   barramento ---> RAM destino
        +-------------------+    |                            ^
                                    v                            |
        +-------------------+   contador NP                      |
NP  --->| contador/registro |-----------------------------+      |
        +-------------------+                             |      |
                                                          controle
                                                             |
INIC ---------------------------------------------------------+
                                                             |
FIM <-------------------------------------------------------- FF
```

### O que cada bloco faz

- **bloco de entrada**: guarda `EFI`, `EDI` e `NP` quando `INIC` acontece;
- **bloco de memoria fonte**: le a palavra na memoria de origem;
- **bloco de memoria destino**: escreve a palavra na memoria de destino;
- **bloco de controle**: liga e desliga leitura, escrita, incremento de endereco e fim;
- **bloco FIM**: lembra que o processo acabou.

---

## 4. Um detalhe importante sobre os tamanhos

O enunciado fala em:

- `EFI` com 8 bits;
- `EDI` com 8 bits;
- `NP` com 4 bits;
- palavra de 4 bits;
- RAM `1K x 8`.

Isso assusta no comeco, mas a ideia e esta:

- a RAM tem barramento de dados de 8 bits;
- o lab fala em palavra de 4 bits;
- entao, na pratica, voce deve trabalhar com o **nibble baixo** do dado, isto e, os 4 bits menos significativos.

### Como tratar isso sem drama

- os 4 bits baixos do dado sao a palavra que interessa;
- os 4 bits altos podem ficar em zero ou ser ignorados, dependendo da montagem pedida pelo professor;
- o importante e a logica da copia.

### E o endereco?

Como a RAM e `1K x 8`, ela pede 10 bits de endereco.
Mas o enunciado te entrega `EFI` e `EDI` com 8 bits.

Entao o jeito mais comum e:

- usar `EFI[7:0]` e `EDI[7:0]` como a parte baixa do endereco;
- prender os dois bits mais altos em zero, se o componente pedir 10 bits completos.

Se o professor tiver uma regra diferente, siga a regra dele.
Mas para entender o circuito, pense assim:

```text
endereco real da RAM = 00 + EFI
endereco real da RAM = 00 + EDI
```

---

## 5. O que cada aula entra no lab

### Aula 7: flip-flops

Voce precisa dela para entender:

- porque o circuito tem memoria;
- porque `FIM` nao pode ser so um fio combinacional;
- como o estado do circuito fica guardado entre pulsos.

Em outras palavras:

- um FF guarda 1 bit de informacao;
- o sinal `FIM` e justamente uma informacao que precisa ficar presa ate o usuario reiniciar o circuito.

### Aula 8: contadores

Voce precisa dela para entender:

- como somar 1 a cada palavra copiada;
- como fazer a contagem de quantas palavras faltam;
- como andar com os enderecos da memoria.

Pensa assim:

- `EFI` vai andando para a frente;
- `EDI` vai andando para a frente;
- `NP` vai diminuindo ate zerar.

### Aula 9: registradores

Voce precisa dela para guardar os valores de entrada no momento do `INIC`.

Isso e muito importante porque o enunciado diz que:

> os parametros da copia so sao validos durante o pulso INIC.

Traduzindo:

- voce mexe nas chaves;
- aperta `INIC`;
- o circuito copia esses valores para registradores;
- depois disso, as chaves podem mudar e nao devem mais afetar a operacao.

### Aula 10: maquina de estados

Voce precisa dela para pensar a sequencia do lab.

O lab nao e um circuito combinacional.
Ele precisa fazer uma sequencia:

1. esperar;
2. carregar parametros;
3. ler;
4. escrever;
5. atualizar;
6. repetir;
7. terminar.

Isso e exatamente pensamento de maquina de estados.

### Aula 11: transformar a maquina em circuito

Voce precisa dela se o professor quiser ver a implementacao em FFs e portas.

A aula mostra como sair da tabela de estados e chegar nas equacoes que controlam:

- leitura;
- escrita;
- incremento;
- fim.

### Aula 15: memoria e tri-state

Essa e a aula mais importante para a parte fisica do lab.

Voce precisa dela para entender:

- o que e RAM;
- o que sao `CS`, `OE` e `WE`;
- por que o barramento precisa de tri-state;
- por que nao pode deixar todo mundo falando no mesmo fio ao mesmo tempo.

---

## 6. A logica do problema em forma de pseudocodigo

Se voce quiser entender o lab sem pensar em CI ainda, pense assim:

```text
ao apertar INIC:
    guardar EFI, EDI e NP
    zerar o sinal FIM

enquanto NP != 0:
    ler dado da RAM fonte em EFI

    se for Lab 11 e dado == F:
        parar
        ativar FIM
        sair

    escrever dado na RAM destino em EDI

    EFI = EFI + 1
    EDI = EDI + 1
    NP  = NP  - 1

ativar FIM
```

Essa e a espinha dorsal do circuito.

Se voce conseguir traduzir isso para blocos digitais, o lab esta praticamente resolvido.

---

## 7. Como montar o circuito por blocos

### 7.1 Bloco de entrada

Voce precisa de:

- `EFI` em 8 bits;
- `EDI` em 8 bits;
- `NP` em 4 bits;
- `INIC`.

O que esse bloco faz:

- recebe os valores das chaves;
- no instante do `INIC`, guarda esses valores em registradores.

#### Por que guardar?

Porque o enunciado diz que os parametros so valem durante o pulso.

Se voce nao guardar, as chaves vao continuar influenciando o circuito o tempo todo.

#### Aula relacionada

- Aula 9.

---

### 7.2 Bloco da memoria fonte

Esse bloco faz:

- receber o endereco fonte;
- colocar a palavra lida em um barramento;
- liberar o barramento quando nao estiver lendo.

#### O que nao fazer

- nao deixe a saida da RAM sempre ativa;
- nao ligue duas saidas diferentes no mesmo fio sem tri-state;
- nao deixe `CS` preso em um nivel fixo sem necessidade.

#### Aula relacionada

- Aula 15.

---

### 7.3 Bloco da memoria destino

Esse bloco faz:

- receber o endereco de destino;
- receber o dado vindo da memoria fonte;
- escrever esse dado na posicao correta.

#### Ideia importante

Na pratica, o dado que sai da RAM fonte vira o dado que entra na RAM destino.

Ou seja:

```text
RAM fonte -> barramento -> RAM destino
```

#### Aula relacionada

- Aula 15.

---

### 7.4 Bloco de contagem

Voce tem tres "contadores" logicos:

1. contador do endereco fonte;
2. contador do endereco destino;
3. contador de palavras restantes.

#### Endereco fonte

- comeca em `EFI`;
- a cada copia bem-sucedida, soma 1.

#### Endereco destino

- comeca em `EDI`;
- a cada copia bem-sucedida, soma 1.

#### NP

- comeca com o valor digitado em `NP`;
- a cada copia bem-sucedida, subtrai 1;
- quando chega em zero, acabou.

#### Aula relacionada

- Aula 8.

---

### 7.5 Bloco de controle

Esse e o cerebro do circuito.

Ele decide:

- quando ler;
- quando escrever;
- quando incrementar;
- quando ativar `FIM`.

Na pratica, ele e uma **maquina de estados**.

#### Estados sugeridos

Voce pode pensar nestes estados:

| Estado | O que ele faz |
|--------|---------------|
| `ESPERA` | espera o `INIC` |
| `CARGA` | guarda `EFI`, `EDI` e `NP` |
| `LEITURA` | habilita a RAM fonte para colocar o dado no barramento |
| `CHECA` | no Lab 11, compara o dado com `F` |
| `ESCRITA` | habilita a RAM destino para gravar o dado |
| `ATUALIZA` | soma 1 nos enderecos e subtrai 1 de `NP` |
| `FIM` | ativa o FF de fim e desliga o resto |

#### Aula relacionada

- Aula 10 para pensar a sequencia;
- Aula 11 para transformar isso em circuito.

---

## 8. Como fica o Lab 10, passo a passo

No Lab 10, a ideia e copiar exatamente `NP` palavras.

### Fluxo simples

1. `INIC` sobe.
2. O circuito salva `EFI`, `EDI` e `NP`.
3. O controlador coloca o endereco fonte na RAM fonte.
4. A RAM fonte libera o dado.
5. O controlador coloca o endereco destino na RAM destino.
6. A RAM destino grava o dado.
7. Os enderecos andam.
8. `NP` diminui.
9. Se `NP` nao zerou, repete.
10. Se `NP` zerou, `FIM` liga.

### O que voce precisa verificar na simulacao

- o endereco fonte anda de 1 em 1;
- o endereco destino anda de 1 em 1;
- o dado que aparece na saida da fonte e o mesmo que entrou na destino;
- `FIM` so liga no final.

### Erro comum no Lab 10

Muita gente tenta fazer tudo em um unico estado.
Isso costuma ficar confuso.

Melhor pensar em pelo menos tres momentos:

1. ler;
2. escrever;
3. atualizar.

---

## 9. Como fica o Lab 11, passo a passo

O Lab 11 e o Lab 10 com uma regra a mais.

### A regra nova

Se o dado lido for `F`:

- o processo para;
- esse dado nao e escrito;
- `FIM` ativa.

### Como detectar `F`

Como o dado e de 4 bits, `F` significa:

```text
1111
```

Entao a comparacao pode ser feita assim:

```text
eh_F = dado3 AND dado2 AND dado1 AND dado0
```

Se todos os bits forem 1, o dado e `F`.

### Onde essa comparacao entra

O ideal e comparar **antes** de escrever.

Ou seja:

1. le o dado;
2. verifica se e `F`;
3. se for `F`, vai para `FIM`;
4. se nao for `F`, escreve na memoria destino.

### O que nao pode acontecer

- nao pode escrever o `F` na memoria destino;
- nao pode continuar copiando depois do `F`.

### Aula relacionada

- Aula 10 para a sequencia;
- Aula 11 para colocar a decisao no circuito;
- Aula 15 para o barramento e a RAM.

---

## 10. Como pensar a maquina de estados

Se voce estiver travado, pensa assim:

### Estado 1: espera

- tudo parado;
- `FIM` desligado;
- espera `INIC`.

### Estado 2: carrega parametros

- guarda `EFI`;
- guarda `EDI`;
- guarda `NP`.

### Estado 3: leitura

- coloca o endereco fonte;
- habilita a RAM fonte;
- pega o dado.

### Estado 4: decisao

- no Lab 11, olha o dado;
- se for `F`, vai para fim;
- se nao for, continua.

### Estado 5: escrita

- coloca o endereco destino;
- habilita a RAM destino;
- escreve o dado.

### Estado 6: atualizacao

- incrementa endereco fonte;
- incrementa endereco destino;
- decrementa `NP`.

### Estado 7: fim

- desliga tudo;
- trava `FIM` num FF.

### Por que isso e bom?

Porque cada estado faz pouca coisa.
Assim o circuito fica mais facil de entender e de depurar.

---

## 11. Como o tri-state entra de verdade

O tri-state e o que evita briga no barramento.

Pensa no barramento como uma rua de mao unica:

- se a RAM fonte esta dirigindo o barramento, so ela pode falar;
- a RAM destino deve ficar ouvindo;
- quando nao estiver lendo, a saida da fonte precisa ficar em `Z`.

### Em linguagem pratica

- durante a leitura: habilita a saida da RAM fonte;
- fora da leitura: libera a saida;
- durante a escrita: a RAM destino recebe o dado.

### Aula relacionada

- Aula 15.

Se voce esquecer o tri-state, o circuito pode parecer funcionar no papel, mas na simulacao o barramento fica brigando e o resultado vira bagunca.

---

## 12. Como fazer o sinal FIM do jeito certo

O enunciado diz que `FIM` precisa ser proveniente de um FF.

Isso significa:

- nao basta um comparador gerar `FIM` diretamente;
- o sinal precisa ficar armazenado.

### Jeito simples de pensar

1. o controlador detecta a condicao de parada;
2. essa condicao coloca `1` na entrada de um flip-flop;
3. o flip-flop segura esse `1`;
4. a saida do flip-flop e o `FIM`.

### Por que isso importa?

Porque, sem FF, o sinal pode sumir quando o circuito mudar de estado.
Com FF, ele fica preso ate um reset.

### Aula relacionada

- Aula 7.

---

## 13. Roteiro pratico para montar no CircuitMaker

Se voce quiser fazer sem ficar pensando demais, siga esta ordem.

### Passo 1

Coloque as duas RAMs:

- uma como fonte;
- uma como destino.

### Passo 2

Coloque as entradas:

- `EFI`;
- `EDI`;
- `NP`;
- `INIC`.

### Passo 3

Coloque os registradores de carga:

- um para o endereco fonte;
- um para o endereco destino;
- um para `NP`.

### Passo 4

Coloque os contadores ou registradores de incremento:

- endereco fonte +1;
- endereco destino +1;
- `NP - 1`.

### Passo 5

Coloque o barramento de dados com tri-state.

### Passo 6

Coloque o comparador de `F` para o Lab 11.

### Passo 7

Monte a maquina de estados que gera:

- leitura;
- escrita;
- atualizacao;
- fim.

### Passo 8

Monte o FF de `FIM`.

### Passo 9

Coloque os displays.

### Passo 10

Teste primeiro com `NP = 1`.

Depois teste com `NP = 2`.

So depois aumente a complexidade.

---

## 14. Como testar sem se confundir

O jeito mais seguro e testar em partes.

### Teste 1: apenas leitura

- verifique se a RAM fonte entrega o dado certo.

### Teste 2: apenas escrita

- verifique se a RAM destino grava o dado correto.

### Teste 3: endereco andando

- veja se os enderecos mudam de 1 em 1.

### Teste 4: NP zerando

- confira se o contador de palavras chega em zero.

### Teste 5: FIM

- confira se `FIM` ativa so no final.

### Teste 6: Lab 11 com `F`

- coloque um `F` na memoria fonte;
- confirme que o circuito para antes de copiar esse valor.

### Exemplo de teste bom

- `EFI = 10`
- `EDI = 20`
- `NP = 3`
- memoria fonte = `3, 7, F`

No Lab 10:

- ele copia `3`, `7` e `F` se o dado estiver dentro das `NP` palavras.

No Lab 11:

- ele copia `3` e `7`;
- ao ler `F`, para;
- `F` nao vai para o destino.

---

## 15. Erros que mais derrubam esse lab

### 1. Esquecer de guardar os parametros no `INIC`

Se voce nao guardar, mudar a chave depois altera o circuito inteiro.

### 2. Deixar a RAM sempre habilitada

Isso pode causar conflito no barramento.

### 3. Copiar `F` no Lab 11

No Lab 11, `F` e parada, nao dado valido.

### 4. Fazer `FIM` so com combinacao logica

O enunciado pede FF, entao o fim precisa ser armazenado.

### 5. Misturar endereco e dado

Endereco e onde eu leio/escrevo.
Dado e o que eu copio.

### 6. Esquecer que a palavra do lab e de 4 bits

Na simulacao, fique atento para nao tratar o dado como 8 bits sem necessidade.

---

## 16. Resumo bem curto para memorizar

Se voce quiser decorar so a essencia, guarda isto:

```text
INIC -> guarda EFI, EDI e NP
LE -> pega o dado da RAM fonte
SE Lab 11 e dado == F -> FIM
SENAO -> escreve na RAM destino
        -> soma 1 nos enderecos
        -> subtrai 1 de NP
        -> repete
quando NP chegar em zero -> FIM
```

E a ligacao com as aulas e esta:

- Aula 9: guardar os parametros;
- Aula 8: contar e andar com os enderecos;
- Aula 10 e 11: organizar o controle;
- Aula 15: RAM, barramento e tri-state;
- Aula 7: `FIM` vindo de um FF.

---

## 17. Se voce quiser pensar como uma maquina de estados Moore

Para iniciante, esse e o jeito mais tranquilo.

Em uma maquina **Moore**:

- as saidas dependem do estado atual;
- isso facilita muito os sinais de controle.

Entao voce pode fazer assim:

- estado `LEITURA` -> habilita leitura;
- estado `ESCRITA` -> habilita escrita;
- estado `FIM` -> ativa `FIM`;
- estado `ESPERA` -> tudo desligado.

Isso combina bem com o que a Aula 10 mostra.

---

## 18. O que eu revisaria antes de abrir a simulacao

Antes de mexer nos fios, eu revisaria esta lista:

- o que e FF;
- o que e registrador;
- o que e contador;
- o que e maquina de estados;
- o que e tri-state;
- o que cada sinal da RAM faz (`CS`, `OE`, `WE`);
- como detectar `F` com 4 bits;
- como fazer `FIM` ficar preso num FF.

Se voce entender isso, o lab deixa de parecer um bicho de sete cabecas.

---

## 19. Fechamento

Se eu tivesse que resumir o lab em uma unica frase, seria esta:

> montar uma pequena maquina sequencial que le uma palavra, decide se pode copiar, escreve no destino, atualiza os ponteiros e para no final.

O Lab 10 e a copia normal.
O Lab 11 e a copia com parada em `F`.

Se voce seguir a ordem:

1. Aula 15;
2. Aula 9;
3. Aula 8;
4. Aula 10;
5. Aula 11;
6. Aula 7;

voce ja tem praticamente todo o raciocinio do circuito.
