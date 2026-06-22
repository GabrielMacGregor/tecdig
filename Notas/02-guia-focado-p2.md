# Guia focado para a P2 — o que é necessário saber

## Conclusão da análise das provas

Foram comparados:

- `p2_2012.2.pdf`;
- `G2_202EX1.PDF`;
- `G2_202EXE2.PDF`;
- `G2_Q2_1.pdf`;
- as quatro imagens de questões;
- Aulas 7–16;
- Labs 4–6;
- datasheets dos contadores 74190 e 74192/74193.

A P2 está concentrada em **circuitos sequenciais**. A prioridade anterior dada a K-map de
5 variáveis era adequada para P1, mas não para esta prova.

## Prioridade real

| Prioridade | Conteúdo | Onde aparece |
|---|---|---|
| 1 | Criar diagrama e tabela de estados a partir de um enunciado | sensores, códigos, ASCII |
| 2 | Implementar uma tabela com FF JK, D ou outro FF fornecido | P2 2012 e contador WZ |
| 3 | Contadores síncronos com sequência arbitrária | sequência 0,2,4,5,6,8,9 e outras |
| 4 | Contadores integrados 74190/74192/74193 | limites variáveis e modo crescente/decrescente |
| 5 | Minimização de tabelas de estados | exercício G2 e solução manuscrita |
| 6 | Contadores assíncronos, cascata e formas de onda | 74293 e 74LS90 |
| 7 | Teclado matricial e projeto em blocos | monitor de variáveis e varredura 3×3 |

## Aulas que realmente importam

### Estudar profundamente

- **Aula 7:** FF D, JK, tabelas características, excitação, preset e clear.
- **Aula 8:** contadores, cascata, módulo e detecção de estados.
- **Aula 10:** Mealy, Moore, diagrama e tabela de estados.
- **Aula 11:** designação, tabela de transição, excitação, K-map e equações.
- **Aula 12:** minimização de máquina completamente especificada.
- **Aula 14:** teclado matricial e varredura.
- **Lab 6:** contagem crescente/decrescente com limites usando 74190/74193.

### Revisar como apoio

- **Aula 4:** K-map e don't care, usados para minimizar entradas dos FFs.
- **Aula 5:** MUX e decoder, usados em seleção e projeto em blocos.
- **Aula 9:** registradores, apenas como apoio à compreensão de dados seriais.
- **Aula 13:** compatibilidade de estados, caso apareça máquina incompletamente especificada.

### Baixa prioridade para estas provas

- Aulas 1–3 e 6.
- Aulas 15–16.
- Conversão BCD detalhada, exceto para ligar contadores BCD ao display.

---

## Tipo 1 — Projetar máquina de estados a partir de texto

### Questões encontradas

- Detectar a direção de um cartão com sensores A, B e C — modelo de Mealy.
- Reconhecer caracteres ASCII maiúsculos entre H e S — modelo de Moore.
- Reconhecer códigos 422 e 131, tratar CLR e bloquear após dois erros — Moore.

### O que você precisa saber

1. Definir exatamente o que cada estado “lembra”.
2. Escolher Mealy ou Moore conforme o enunciado.
3. Construir os caminhos válidos antes de preencher os casos restantes.
4. Determinar o retorno correto após entrada errada.
5. Representar saídas:
   - Mealy: `entrada/saída` na transição;
   - Moore: saída associada ao estado.
6. Converter o diagrama em tabela sem perder transições.
7. Usar estados de erro/bloqueio quando necessário.
8. Aproveitar prefixos já recebidos quando houver interseção.

### Procedimento que deve ser seguido

```text
1. Liste entradas, saídas e evento de clock.
2. Escreva as sequências que devem ser reconhecidas.
3. Crie um estado inicial.
4. Crie um estado para cada informação parcial que precisa ser lembrada.
5. Desenhe primeiro o caminho de sucesso.
6. Adicione erro, CLR, nova tentativa, bloqueio ou retorno.
7. Confira todas as entradas possíveis em cada estado.
8. Transfira o diagrama para a tabela.
9. Simule manualmente pelo menos um caso correto e um incorreto.
```

### Para a questão dos sensores

É necessário:

- ler as formas de onda na ordem temporal;
- identificar as sequências de combinações `ABC`;
- criar um caminho para esquerda→direita e outro para direita→esquerda;
- gerar um pulso em L1 ou L2 somente quando a passagem terminar;
- usar don't care apenas para combinações fisicamente impossíveis.

### Para a questão do ASCII

É necessário:

- ler os sete bits MSB primeiro;
- reconhecer o prefixo comum das letras maiúsculas;
- distinguir os códigos de H até S;
- manter Z3 durante a recepção;
- ao completar sete bits, selecionar Z1 ou Z2;
- codificar as três condições de saída com dois bits e decodificá-las.

### Para a questão das câmeras

É necessário:

- armazenar o progresso dos códigos 422 e 131;
- fazer CLR apagar o progresso parcial, mas não agir no início;
- contar tentativas erradas;
- entrar em bloqueio após o segundo erro;
- voltar ao início após código correto;
- criar estados Moore específicos para A=1, D=1 e bloqueio.

### Erros comuns

- Criar estado para o símbolo recebido, e não para a memória necessária.
- Esquecer transições de entradas “erradas”.
- Colocar a saída no lugar errado para Mealy/Moore.
- Não voltar ao estado inicial após completar uma palavra.
- Usar menos estados apagando uma informação que ainda será necessária.

---

## Tipo 2 — Implementar uma tabela de estados com flip-flops

### Questão encontrada

A P2 de 2012 fornece uma tabela mínima, uma designação obrigatória e pede:

- tabela de transição;
- equações J e K de um FF;
- equação D de outro FF;
- equação mínima da saída.

### O que você precisa saber

#### Tabela de excitação do JK

| `Q → Q+` | J | K |
|---|---|---|
| 0 → 0 | 0 | X |
| 0 → 1 | 1 | X |
| 1 → 0 | X | 1 |
| 1 → 1 | X | 0 |

#### FF D

```text
D = Q+
```

### Procedimento

```text
1. Substituir os nomes dos estados pelos códigos fornecidos.
2. Montar a tabela: estado atual + entrada → próximo código.
3. Separar cada bit do próximo estado.
4. Para FF D, copiar diretamente o bit seguinte para D.
5. Para FF JK, aplicar a tabela de excitação linha a linha.
6. Colocar J e K em mapas separados.
7. Tratar X como don't care.
8. Minimizar uma equação por entrada de FF.
9. Obter a saída a partir da coluna Z e dos bits do estado.
```

### Habilidade indispensável

Você deve conseguir pegar uma transição como:

```text
y1 atual = 0 → y1 seguinte = 1
```

e concluir imediatamente:

```text
J1 = 1 e K1 = X
```

### Erros comuns

- Usar a tabela característica quando a questão exige a tabela de excitação.
- Misturar estado atual com próximo estado.
- Não colocar os estados não utilizados como don't care no K-map.
- Trocar a ordem das entradas no mapa.
- Derivar Z do próximo estado em vez do estado/entrada definidos na tabela.

---

## Tipo 3 — Flip-flop não convencional, como o WZ

### Questão encontrada

Um contador tem sequências diferentes para `S=0` e `S=1` e pede somente as entradas do
FF `y2`, usando um FF WZ cuja tabela é fornecida.

### O que você precisa saber

Não é necessário decorar o FF WZ. É necessário saber **derivar sua tabela de excitação**.

Tabela característica fornecida:

| W | Z | `Q+` |
|---|---|---|
| 0 | 0 | 0 |
| 0 | 1 | 1 |
| 1 | 0 | Q |
| 1 | 1 | Q̅ |

### Procedimento

1. Para cada combinação `Q atual → Q seguinte`, listar todos os pares WZ permitidos.
2. Transformar isso em uma tabela de excitação com don't cares quando possível.
3. Montar a tabela de transição do contador para `S=0` e `S=1`.
4. Observar apenas o bit `y2`.
5. Preencher um mapa para W2 e outro para Z2.
6. Usar estados binários não pertencentes à sequência como don't care, se permitido.

Essa mesma técnica funciona para qualquer flip-flop inventado na prova.

---

## Tipo 4 — Projetar contador síncrono de sequência arbitrária

### Questões encontradas

- Contagem `0, 2, 4, 5, 6, 8, 9, 0...` usando JK.
- Contagem em uma ordem para `S=0` e na ordem inversa para `S=1`.

### Procedimento

```text
1. Converter todos os números para binário com a mesma largura.
2. Montar estado atual → próximo estado.
3. Acrescentar a entrada de modo, se existir.
4. Definir o comportamento dos estados não usados:
   - don't care, se permitido;
   - ou retorno seguro ao estado inicial.
5. Aplicar a excitação do FF escolhido.
6. Criar os mapas de cada entrada de FF.
7. Minimizar e desenhar o circuito com clock comum.
```

### Distinção importante

- **Contador síncrono:** todos os FFs recebem o mesmo clock.
- **Contador assíncrono:** a saída de um estágio alimenta o clock do próximo.

Se a questão pedir contador síncrono, não desenhe ripple counter.

---

## Tipo 5 — Minimização de tabela de estados

### Questões encontradas

- Tabela de oito estados A–H com quatro combinações de entrada.
- Solução manuscrita usando tabela de implicação.

### O que você precisa saber

1. Marcar imediatamente pares com saídas diferentes.
2. Anotar os pares de próximos estados implicados.
3. Propagar incompatibilidades até estabilizar.
4. Identificar os pares equivalentes restantes.
5. Formar classes de equivalência.
6. Substituir cada classe por um estado na tabela reduzida.

### Procedimento de prova

```text
1. Compare as saídas de cada par.
2. Se diferirem em qualquer entrada: X.
3. Se forem iguais: anote os pares implicados.
4. Propague os X.
5. Leia os pares não marcados.
6. Monte as classes.
7. Construa e confira a tabela mínima.
```

### Resultado da questão usada como referência

A solução enviada reduz os oito estados para cinco classes. Use-a para conferir seu método,
mas refaça a tabela de implicação sem copiar.

---

## Tipo 6 — Contadores 74190, 74192 e 74193

### Questões encontradas

- Contar de 7 a 17 ou de 7 a 15, crescente/decrescente, com 74192.
- Contar de 6 a 18 ou de 6 a 21, crescente/decrescente, com 74192.
- Lab 6: contar de 5 a 24 com 74193 e 74190.
- Uma única entrada de clock, apesar de o 74192 possuir clocks UP e DOWN.

### Diferenças fundamentais

| CI | Tipo de contagem | Controle de direção |
|---|---|---|
| 74190 | BCD 0–9 | um clock + entrada de modo |
| 74191 | binário | um clock + entrada de modo |
| 74192 | BCD 0–9 | clocks UP e DOWN separados |
| 74193 | binário 0–15 | clocks UP e DOWN separados |

### Pinos/funções que precisa dominar

- Entradas paralelas de preset.
- `LOAD`/`PL` ativo em nível baixo.
- Clear/reset e sua polaridade.
- Clock UP e clock DOWN.
- Carry/borrow para cascata.
- Como manter o clock não utilizado no nível inativo correto.

### Estrutura de um contador com limite inferior e superior

Para contar de `MIN` até `MAX`:

- crescente: ao detectar `MAX`, o próximo pulso deve carregar `MIN`;
- decrescente: ao detectar `MIN`, o próximo pulso deve carregar `MAX`;
- a chave de modo seleciona a direção;
- a chave T seleciona qual valor de MAX será usado;
- as entradas paralelas recebem MIN ou MAX conforme a operação.

### Uma única entrada de clock no 74192

É necessário criar um roteamento:

- modo crescente: clock externo vai para UP e DOWN fica inativo;
- modo decrescente: clock externo vai para DOWN e UP fica inativo.

Isso pode ser feito com portas controladas por M e respeitando o nível inativo exigido pelo
datasheet. Não basta ligar o mesmo clock diretamente aos dois pinos.

### Para dois dígitos BCD

- um 74192 representa unidades;
- outro representa dezenas;
- carry/borrow do estágio de unidades aciona o estágio de dezenas;
- a detecção do limite deve considerar os dois dígitos;
- displays recebem os nibbles BCD por decodificadores BCD→7 segmentos.

### Testes obrigatórios

Para cada circuito, simule:

- `MIN`, `MIN+1`;
- `MAX-1`, `MAX`;
- transição `MAX → MIN` no crescente;
- transição `MIN → MAX` no decrescente;
- mudança da chave T;
- mudança da chave M.

---

## Tipo 7 — 74293, 7490 e formas de onda

### Questões encontradas

- Contador binário de 0 a 39 com 74293.
- Setar FF D em 9 e resetar em 17.
- Interpretar dois 74LS90 interligados e desenhar o diagrama temporal.

### O que você precisa saber

- Quantos bits ou CIs são necessários para alcançar o valor máximo.
- Como cascatear contadores.
- Como detectar um estado com portas.
- Polaridade do reset/preset.
- Em qual borda o estágio seguinte conta.
- Diferença entre valor BCD e valor binário.

### Para desenhar formas de onda

1. Anote o estado inicial completo.
2. Identifique a borda ativa do primeiro CI.
3. Atualize primeiro o contador de unidades.
4. Verifique se ocorreu carry/borrow para o segundo.
5. Registre a palavra completa após cada borda.
6. Só então desenhe Q pedido.

Não tente desenhar a onda “de cabeça” sem a tabela pulso a pulso.

---

## Tipo 8 — Monitor de variáveis e teclado 3×3

### Questões encontradas

- Monitorar P0–P8 de forma direta ou circular crescente/decrescente.
- Configurar quantas variáveis serão monitoradas.
- Projetar a varredura e decodificação do teclado 3×3.

### Blocos necessários

```text
teclado/código direto
→ registrador do número selecionado
→ contador up/down para modo sequencial
→ lógica de limites configuráveis
→ MUX para selecionar P0–P8
→ decodificadores para displays
```

### Para o teclado 3×3

- contador módulo 3 para varrer colunas;
- decoder para ativar uma coluna;
- pull-ups nas linhas;
- leitura das três linhas;
- debounce/monoestável para gerar um pulso limpo;
- combinação linha+coluna → código 0–8;
- sinal ST indicando código válido.

### Conhecimentos associados

- Aula 5: MUX e decoder.
- Aula 8/Lab 6: contador e limites.
- Aula 14: varredura do teclado.
- Aula 7: registradores/FF para armazenar CONFIG e seleção.

---

## Folha de fórmulas que deve estar na memória

### Quantidade de flip-flops

```text
número de FFs = teto(log2(número de estados))
```

### JK

```text
0→0: J=0, K=X
0→1: J=1, K=X
1→0: J=X, K=1
1→1: J=X, K=0
```

### D

```text
D = Q+
```

### Moore e Mealy

```text
Moore: Z = f(estado)
Mealy: Z = f(estado, entrada)
```

### Método geral de projeto

```text
especificação
→ diagrama/tabela
→ minimização
→ designação
→ tabela de transição
→ excitação
→ K-map
→ equações
→ circuito
```

---

## Ordem de exercícios recomendada

1. P2 2012 — Questão 2: implementação com JK e D.
2. G2 Exercício 1 — Questão 2: contador síncrono de sequência arbitrária.
3. Questão WZ: derivar excitação de FF desconhecido.
4. G2 Exercício 2 — Questão 1: minimização de estados.
5. P2 2012 — Questão 1: máquina Mealy dos sensores.
6. G2 Exercício 1 — Questão 1: máquina Moore das câmeras.
7. Questão ASCII: máquina Moore para dados seriais.
8. P2 2012 — Questão 3b e questão da imagem: 74192 com limites.
9. Lab 6: repetir o projeto com 74190 e 74193.
10. G2 Exercício 2 — monitor e teclado.

## Critério de prontidão

Você está pronto quando consegue, sem consultar:

- transformar uma descrição em estados;
- diferenciar corretamente Mealy de Moore;
- montar tabela de transição com designação fornecida;
- derivar excitação de JK, D e de um FF cuja tabela seja dada;
- minimizar uma tabela por implicação;
- projetar contador síncrono de sequência arbitrária;
- explicar como o 74192 conta para cima e para baixo com um clock externo;
- desenhar uma forma de onda usando tabela pulso a pulso;
- dividir um sistema complexo em contador, registrador, MUX, decoder e display.

