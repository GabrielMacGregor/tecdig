# Questão 2c e 2d — Flip-Flops JK em Cascata + Serialização com 74165

> Prova 2015.1

---

## Parte c — Formas de Onda de 3 FFs JK em Cascata

### Enunciado

Circuito com **3 FFs JK (a, b, c)** em cascata (contador assíncrono):
- Entradas em aberto = **nível lógico 1** → J=K=1 (modo **toggle**)
- FFs mudam de estado nas **transições negativas** do clock
- Estado inicial: **Qa=Qb=Qc=0**
- Sinal periódico (relógio) injetado na **entrada do FF a**

### Diagrama do circuito

```
             J=1              J=1              J=1
CLK ──→ [FF_a] ──Qa──→ [FF_b] ──Qb──→ [FF_c] ──Qc
             K=1              K=1              K=1
             ↓CK              ↓CK              ↓CK
         (borda ↓)        (borda ↓)        (borda ↓)
         CLK externo       borda ↓ de Qa    borda ↓ de Qb
```

> J=K=1 → FF sempre **inverte** (toggle) na borda negativa do seu clock.

### Regra de cada FF

| FF | Quando inverte? |
|----|----------------|
| FF_a | A cada borda ↓ do CLK externo |
| FF_b | A cada borda ↓ de Qa |
| FF_c | A cada borda ↓ de Qb |

### Tabela de estados

| Pulso CLK | Qa | Qb | Qc | Contador (Qa=LSB) |
|-----------|----|----|-----|-------------------|
| inicial   |  0 |  0 |  0  | 0 |
| 1 ↓       |  1 |  0 |  0  | 1 |
| 2 ↓       |  0 |  1 |  0  | 2 (Qa↓ → Qb inverte) |
| 3 ↓       |  1 |  1 |  0  | 3 |
| 4 ↓       |  0 |  0 |  1  | 4 (Qa↓→Qb↓→Qc inverte) |
| 5 ↓       |  1 |  0 |  1  | 5 |
| 6 ↓       |  0 |  1 |  1  | 6 |
| 7 ↓       |  1 |  1 |  1  | 7 |
| 8 ↓       |  0 |  0 |  0  | 0 (ciclo completo) |
| 9 ↓       |  1 |  0 |  0  | 1 |
| 10 ↓      |  0 |  1 |  0  | 2 |
| 11 ↓      |  1 |  1 |  0  | 3 |
| 12 ↓      |  0 |  0 |  1  | 4 |

### Formas de onda (texto)

```
CLK  ¯|_|¯|_|¯|_|¯|_|¯|_|¯|_|¯|_|¯|_|¯|_|¯|_|¯|_|¯|_|
       1   2   3   4   5   6   7   8   9  10  11  12

Qa   ____|¯¯|__|¯¯|__|¯¯|__|¯¯|__|¯¯|__|¯¯|__|¯¯|__
          1   2   3   4   5   6   7   8   9  10  11 12

Qb   ________|¯¯¯¯|________|¯¯¯¯|________|¯¯¯¯|_____
              2    4         6    8        10   12

Qc   ________________|¯¯¯¯¯¯¯¯|________________|¯¯¯
                      4        8                12
```

> **Resumo:** É um contador binário de 3 bits (0 a 7), que reseta no 8 e recomeça.
> - Qa: período = 2 clocks (divide por 2)
> - Qb: período = 4 clocks (divide por 4)
> - Qc: período = 8 clocks (divide por 8)

### Pontos de atenção para a prova

1. Qa inverte **sempre** na borda ↓ do CLK
2. Qb inverte apenas quando **Qa vai de 1→0** (borda ↓ de Qa)
3. Qc inverte apenas quando **Qb vai de 1→0** (borda ↓ de Qb)
4. Não confundir com borda de subida!

---

## Parte d — Serialização de 4 bits com Framing usando 74165

### Enunciado

Projetar um circuito que serializa um dado de **4 bits**, envelopado com:
- **bit 0** (início) e **bit 1** (fim)

Exemplo: dado = **1011** → transmitir: **`0 1011 1`** = `010111`

Sinais:
- **START**: indica início da transmissão (você gera)
- **DONE**: indica fim da transmissão (você deve gerar)

Usar o registrador **74165**.

---

### Revisão do 74165 (PISO — Paralelo→Série)

```
Pinos principais:
  SH/LD: 0 = carrega paralelo | 1 = desloca (shift)
  CLK:   pulso de clock
  A..H:  8 entradas paralelas (H = primeiro a sair!)
  QH:    saída serial (sai H na 1ª borda, G na 2ª, F na 3ª...)
  SER:   entrada serial (entra pelo lado A durante shift)
```

> A saída QH emite primeiro o bit da posição **H**, depois G, F, E, D, C, B, A.

---

### Mapeamento das posições

Para transmitir `010111` (6 bits) com dado `1011`:

| Posição 74165 | Valor | Descrição |
|---------------|-------|-----------|
| H | 0 | start bit (sai primeiro) |
| G | 1 | dado[3] (bit mais significativo) |
| F | 0 | dado[2] |
| E | 1 | dado[1] |
| D | 1 | dado[0] |
| C | 1 | stop bit (sai por último) |
| B | X | não usado |
| A | X | não usado |

Após 6 pulsos de clock: QH emite `0, 1, 0, 1, 1, 1` = `010111` ✓

### Carregamento das entradas

```
H = 0         (start bit fixo)
G = dado[3]   (MSB dos dados)
F = dado[2]
E = dado[1]
D = dado[0]   (LSB dos dados)
C = 1         (stop bit fixo)
B = X (pode ser 0)
A = X (pode ser 0)
```

---

### Sinal de início (START) e fim (DONE)

**START:** Controlado pela chave SH/LD:
- SH/LD = 0 → carrega os dados paralelos (1 pulso de clock)
- SH/LD = 1 → começa a transmissão (shift) → este é o sinal de START

**DONE:** Gerado após **6 pulsos de clock** desde o início do shift.

Use um **contador de 3 bits** para contar os 6 pulsos:

```
DONE = Q2 AND Q1 AND NOT(Q0)   (quando contagem = 6 = 110 em binário)
```

O contador:
- É zerado quando SH/LD = 0 (carregando)
- Conta quando SH/LD = 1 (shiftando)
- Quando chega em 6: DONE = 1

---

### Diagrama completo do circuito

```
DADO[3:0] ──────────────────────────→ G, F, E, D [74165]
                                        H = 0 (fixo)
                                        C = 1 (fixo)
                                        A, B = 0

START ──→ SH/LD [74165] ──→ também ──→ RESET [Contador 3 bits]
                                          │
CLK ──→ [74165] CLK                  CLK [Contador]
     └──→ [Contador 3 bits] CLK          │
                                          ↓
                               Q2 · Q1 · Q0' = DONE

74165 QH ──→ Serial Data Out (fio de transmissão)
```

### Sequência de operação passo a passo

```
1. Operador aplica dado nos pinos G,F,E,D do 74165
2. Ativa START: SH/LD = 0 → 1 pulso CLK → dado carregado
3. SH/LD = 1 → transmissão inicia (contador zera)
4. Cada pulso CLK:
     - 74165 envia 1 bit pelo QH
     - contador incrementa
5. Após 6 pulsos: contador = 6 → DONE = 1
6. Transmissão concluída: QH emitiu 0,1,0,1,1,1 ✓
```

### Temporização

```
CLK   _|¯|_|¯|_|¯|_|¯|_|¯|_|¯|_

SH/LD ¯¯¯¯|___|¯¯¯¯¯¯¯¯¯¯¯¯¯¯¯¯
           ↑load ↑shift_start

QH    xxxxxxx|0|1|0|1|1|1|xxxxx
              H  G  F  E  D  C

Count         0  1  2  3  4  5  6
DONE                              ¯|___
```

### CIs necessários para a parte d

| CI | Quantidade | Função |
|----|-----------|--------|
| 74165 | 1 | Registrador PISO 8 bits |
| 7493 ou similar | 1 | Contador 3 bits para DONE |
| 7404 | 1 | NOT para Q0' |
| 7408/7411 | 1 | AND para detecção de DONE |

---

## Resumo rápido para a prova

| Item | Resposta |
|------|----------|
| 2c: O que fazem os 3 FFs? | Contador binário 0–7 (divide clock por 8) |
| 2c: Período de Qc? | 8 × período do CLK |
| 2c: Quando Qb inverte? | Na borda ↓ de Qa (= quando Qa vai de 1 para 0) |
| 2d: CI usado | 74165 (PISO) |
| 2d: O que fica em H? | Start bit = 0 (sai primeiro) |
| 2d: Quantos pulsos para DONE? | 6 pulsos de clock |
| 2d: Como detectar DONE? | Contador 3 bits = 6 → DONE=1 |
