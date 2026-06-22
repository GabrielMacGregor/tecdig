# Simulado — Técnicas Digitais (20261)

> Tempo sugerido: 2h30 | Consulta: não | Total: 10 pontos

---

## Questão 1 (3,0 pontos) — K-map de 5 Variáveis

Encontre as formas mínimas para as funções incompletamente especificadas abaixo.

### a) Soma de Produtos (SDP) mínima

```
f(A,B,C,D,E) = Σm(0, 2, 4, 6, 9, 11, 18, 21, 22, 23, 24, 25, 26, 27, 31) + d(8, 10, 16, 29)
```

**Fórmula:** Valor da célula = A×16 + B×8 + C×4 + D×2 + E×1

Preencha os K-maps abaixo e identifique os grupos:

```
               A = 0                              A = 1
         BC                               BC
DE     | 00 | 01 | 11 | 10          DE | 00 | 01 | 11 | 10
-------+----+----+----+----         ---+----+----+----+----
  00   |    |    |    |              00 |    |    |    |
  01   |    |    |    |              01 |    |    |    |
  11   |    |    |    |              11 |    |    |    |
  10   |    |    |    |              10 |    |    |    |
```

f(A,B,C,D,E) = _______________________________________________

---

### b) Produto de Somas (PDS) mínima

```
g(A,B,C,D,E) = ΠM(1, 3, 5, 7, 12, 13, 14, 15, 28, 30) + d(0, 8, 16, 24)
```

```
               A = 0                              A = 1
         BC                               BC
DE     | 00 | 01 | 11 | 10          DE | 00 | 01 | 11 | 10
-------+----+----+----+----         ---+----+----+----+----
  00   |    |    |    |              00 |    |    |    |
  01   |    |    |    |              01 |    |    |    |
  11   |    |    |    |              11 |    |    |    |
  10   |    |    |    |              10 |    |    |    |
```

g(A,B,C,D,E) = _______________________________________________

---

### c) Portas AND

No item **a**, para a combinação **ABCDE = 11001**, em quantas portas AND do circuito SDP a saída vale **"1"**? Justifique.

Resposta: _______

---

## Questão 2 (4,0 pontos) — Combinacional e Sequencial

### a) Codificador com Prioridade

Projete um codificador com prioridade para **4 sinais** (L3, L2, L1, L0), gerando um código de **2 bits** (C1C0). Você define a prioridade. Implemente apenas a saída **C1**.

```
L0 ─┐
L1 ─┤  Codificador   ├── C1
L2 ─┤  com Prioridade ├── C0
L3 ─┘
```

Tabela de prioridade (preencha):

| Sinal prioritário | C1C0 |
|-------------------|------|
|                   |      |
|                   |      |
|                   |      |
|                   |      |

Expressão de C1: _______________________

Circuito com portas lógicas:

---

### b) Decodificador de Intervalos

Em um sistema de **5 bits** (códigos de 0 a 31), projete um circuito que gere um sinal em nível lógico **"0"** quando o código estiver nos intervalos **(8–15)** ou **(24–27)**.

Use obrigatoriamente **decodificadores 2×4** (com enable) e, se necessário, portas lógicas.

```
Decodificador 2×4:
         ┌──────┐
  C1 ───→│      ├──→ Y0
  C0 ───→│      ├──→ Y1
         │      ├──→ Y2
  EN ───→│      ├──→ Y3
         └──────┘
  (saídas ativas em nível baixo; EN ativo em nível baixo)
```

Diagrama do circuito:

Sinal de saída OUT = _______________________

---

### c) Formas de Onda — Flip-Flops JK

Considere o circuito abaixo com **três FFs JK (a, b, c)** em cascata. Todas as entradas em aberto equivalem a nível lógico "1". Os FFs mudam na **transição negativa** do clock. Estado inicial: **Qa = Qb = Qc = 0**.

```
              J=1             J=1             J=1
CLK ──→ [FF_a] ──Qa──→ [FF_b] ──Qb──→ [FF_c] ──Qc
              K=1             K=1             K=1
         borda ↓ do CLK   borda ↓ de Qa   borda ↓ de Qb
```

Trace as formas de onda de Qa, Qb e Qc para **10 pulsos de clock**:

```
CLK  ¯|_|¯|_|¯|_|¯|_|¯|_|¯|_|¯|_|¯|_|¯|_|¯|_|

Qa   ____________________________________________

Qb   ____________________________________________

Qc   ____________________________________________
      1   2   3   4   5   6   7   8   9  10
```

Qual é o período de Qc em termos do período do CLK? ________________

---

### d) Serialização com 74165

Projete um circuito que serializa um dado de **4 bits** com o seguinte envelope:
- **bit 0** no início (start bit)
- **bit 1** no final (stop bit)

Exemplo: dado = **1010** → transmitir: **`0 1010 1`** = `010101`

Use o registrador **74165** (PISO — Paralelo → Série, 8 bits, saída QH).

Responda:
1. O que você carrega nas posições **H, G, F, E, D, C** do 74165?
2. Quantos pulsos de clock são necessários para completar a transmissão?
3. Como você gera o sinal **DONE** (fim da transmissão)?

---

## Questão 3 (3,0 pontos) — Projeto de Sistema

Uma linha de produção monitora dois sensores de pressão: **P** e **Q**, cada um fornecendo um valor de **4 bits** (0 a 15).

O sistema deve:

1. Calcular **P + Q** (resultado com até 5 bits, máximo 30)
2. Acionar um **sinal de ALERTA (AL)** quando a soma **for maior que 20**
3. O sinal AL deve ser **armazenado em um FF D** e só pode ser zerado por uma chave de **RESET** manual
4. Exibir a soma em **decimal** em dois displays de 7 segmentos (dezenas e unidades)

**Componentes disponíveis:** 74283 (somador), 7485 (comparador), 74157 (MUX), 7474 (FF D), portas lógicas, decodificadores BCD→7 segmentos.

**a)** Qual(is) CI(s) você usa para calcular P + Q? Como conecta?

**b)** Como você detecta se a soma > 20? Especifique as entradas do 7485.

**c)** Como você projeta o armazenamento do sinal AL no FF D? (Desenhe as conexões do 7474.)

**d)** Como você converte a soma binária (0–30) para BCD (dezenas e unidades)?

---

*Boa sorte! Lembre-se: K-map usa código Gray (00→01→11→10), e grupos podem cruzar os dois mapas e "dar a volta" nas bordas.*
