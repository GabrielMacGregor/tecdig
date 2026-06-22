# Plano de Estudos — Técnicas Digitais (20261)

## Diagnóstico do material

O conteúdo está bem dividido em 16 aulas, 5 laboratórios, questões de provas anteriores,
flashcards e um simulado. A melhor estratégia não é apenas reler tudo em ordem: é dominar
os procedimentos que se repetem nas provas e usar os resumos como consulta para corrigir
erros.

### Prioridade por retorno na prova

| Prioridade | Conteúdo | Motivo |
|---|---|---|
| Muito alta | K-map de 4 e 5 variáveis, SDP, PDS e don't care | Questão recorrente e de alto peso |
| Muito alta | Projeto em blocos: somador, comparador, MUX, decoder e FF | Exige integrar vários assuntos |
| Alta | Flip-flops, formas de onda, contadores e divisores | Procedimento previsível, mas sujeito a erros de borda e nível ativo |
| Alta | MUX, decoder e codificador com prioridade | Aparece em projeto e questões específicas |
| Alta | Registradores 74165/74164 e sinal DONE | Tema frequente de serialização |
| Média | Máquinas de estado e projeto com FF D/JK | Conteúdo longo; precisa de exercícios completos |
| Média | Minimização de estados | Método mecânico, aprendido melhor resolvendo tabelas |
| Menor | Monoestável, memória, pilha e fila | Estudar depois de consolidar a base, salvo indicação do professor |

## Método para cada sessão

Use sessões de 60 a 90 minutos:

1. **10 min — recuperação ativa:** sem consultar, escreva regras, fórmulas e o fluxo do tema.
2. **20 min — revisão dirigida:** leia apenas os pontos que não conseguiu recordar.
3. **30 a 45 min — exercício sem resposta:** resolva no papel, mostrando todas as etapas.
4. **10 min — correção:** compare com o resumo ou gabarito e marque a causa de cada erro.
5. **5 min — fechamento:** crie de 3 a 5 perguntas curtas para revisar no dia seguinte.

Regra principal: no máximo 30% do tempo lendo e no mínimo 70% resolvendo, desenhando
circuitos ou explicando em voz alta.

## Sequência recomendada — 14 sessões

### Fase 1 — Base combinacional

- [ ] **Sessão 1:** Aulas 1–3 — tabela verdade, expressão booleana, mintermos, maxtermos, SDP e PDS.
- [ ] **Sessão 2:** Aula 4 — K-map de 3 e 4 variáveis e don't care.
- [ ] **Sessão 3:** K-map de 5 variáveis — refazer uma SDP e uma PDS de `Prova-Q1-Kmap5variaveis.md`.
- [ ] **Sessão 4:** Labs 1 e 2 — transformar especificação em tabela, minimizar e selecionar portas/CIs.

Meta da fase: montar um K-map de 5 variáveis sem consultar o código Gray e justificar cada
termo da expressão.

### Fase 2 — Blocos combinacionais

- [ ] **Sessão 5:** Aulas 5–6 — MUX, DEMUX, decoder, encoder com prioridade e níveis ativos.
- [ ] **Sessão 6:** Labs 2–3 — 74283, 7485, cascata, conversão BCD e expansão de MUX/decoder.
- [ ] **Sessão 7:** Resolver `Prova-Q2-CodDecoder.md` sem olhar a solução.

Meta da fase: receber uma especificação e desenhar primeiro os blocos, depois as ligações e
só por último os CIs.

### Fase 3 — Circuitos sequenciais

- [ ] **Sessão 8:** Aula 7 — tabelas de RS, D, JK e T; preset, clear e borda de clock.
- [ ] **Sessão 9:** Aula 8 + Lab 4 — formas de onda, contadores assíncronos e divisor por N.
- [ ] **Sessão 10:** Aula 9 + Lab 5 — PISO, SIPO, 74165, 74164 e geração de DONE.
- [ ] **Sessão 11:** Resolver `Prova-Q2cd-FlipFlopsSer.md` sem consultar.

Meta da fase: produzir a tabela de estados pulso a pulso antes de desenhar qualquer forma
de onda.

### Fase 4 — Projeto e máquinas de estado

- [ ] **Sessão 12:** Aulas 10–11 — Mealy/Moore e fluxo completo até as equações de FF.
- [ ] **Sessão 13:** Aulas 12–13 — equivalência, compatibilidade e tabela de implicação.

Meta da fase: executar sem pular etapas:
descrição → estados → tabela → codificação → transição → excitação → K-map → circuito.

### Fase 5 — Integração e prova

- [ ] **Sessão 14:** Resolver `Simulado.md` em 2h30, sem consulta, e corrigir por categoria de erro.
- [ ] Revisar Aulas 14–16 depois do núcleo acima ou antes, caso estejam confirmadas na prova.

## Ordem para revisar o simulado

Não registre apenas “acertei” ou “errei”. Classifique cada falha:

- **Conceito:** não sabia qual componente ou regra usar.
- **Representação:** errou binário, BCD, código Gray ou nível ativo.
- **Procedimento:** pulou uma etapa do projeto.
- **Álgebra:** agrupamento ou simplificação incorreta.
- **Atenção:** trocou bit, borda, entrada ou polaridade.
- **Tempo:** sabia fazer, mas demorou demais.

Refaça no dia seguinte apenas as questões com erro, começando de uma folha em branco.

## Ciclo de revisão espaçada

Para cada tema estudado:

- **D+1:** responder aos flashcards sem consultar.
- **D+3:** resolver um exercício curto.
- **D+7:** resolver uma questão de prova ou parte do simulado.
- **D+14:** explicar o procedimento inteiro em voz alta e refazer o ponto mais difícil.

Os flashcards existentes cobrem principalmente as Aulas 1–9. Crie cartões adicionais para:

- Fluxo de projeto de máquinas de estado.
- Tabela de excitação do JK.
- Diferença entre equivalência e compatibilidade.
- Expansão de memória por endereços e por largura.
- Condições de pilha/fila vazia e cheia.

## Checklists de prova

### K-map

- [ ] Usei Gray: 00, 01, 11, 10?
- [ ] Considerei bordas e grupos entre os dois mapas?
- [ ] Usei X somente quando ajudou?
- [ ] Cobri todos os 1s na SDP ou todos os 0s na PDS?
- [ ] Traduzi corretamente as variáveis fixas?

### Projeto com CIs

- [ ] Separei o sistema em blocos?
- [ ] Calculei a largura necessária de dados e endereços?
- [ ] Conferi pinos ativos em LOW e HIGH?
- [ ] Incluí carry, enable, preset, clear e clock?
- [ ] Testei valores de fronteira: mínimo, limite e máximo?

### Circuitos sequenciais

- [ ] Identifiquei a borda ativa?
- [ ] Anotei o estado inicial?
- [ ] Atualizei os FFs na ordem correta?
- [ ] Diferenciei entrada síncrona de preset/clear assíncronos?
- [ ] Para divisor por N, verifiquei o primeiro estado que ativa a detecção?

## Pontos do material que exigem conferência

Antes de memorizar uma solução, valide estes pontos com datasheet, professor ou simulação:

1. Alguns exemplos misturam reset ativo em HIGH com portas NAND/AND.
2. O 7473 não deve ser tratado como se tivesse preset sem conferir a variante usada.
3. Comparações maiores que 4 bits exigem cascata correta de 7485.
4. A conversão binário→BCD de 0 a 30 não é resolvida por um único ajuste de +6.
5. No 74165, confira a ordem real dos bits e se a carga paralela é assíncrona.
6. Detectores simplificados de estados podem ativar novamente em outros estados; teste a
   sequência completa, não somente o número desejado.

## Critério para considerar um bloco dominado

Avance apenas quando conseguir:

- obter pelo menos **80%** em exercícios sem consulta;
- explicar o método sem ler o resumo;
- resolver novamente os erros após 24 horas;
- completar a questão dentro do tempo proporcional à pontuação.

## Progresso geral

- [ ] Base combinacional dominada
- [ ] K-map de 5 variáveis dominado
- [ ] Blocos combinacionais dominados
- [ ] Flip-flops e contadores dominados
- [ ] Serialização dominada
- [ ] Máquinas de estado dominadas
- [ ] Simulado feito e corrigido
- [ ] Revisão final concluída
