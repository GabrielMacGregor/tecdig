# Questão 3 — Sistema de Temperatura Industrial

> Prova 2015.1 — 3 pontos

---

## Lendo o enunciado com calma

A questão descreve um sistema com 4 sensores de temperatura: **TA, TB, TC, TD**.

Cada sensor envia sua leitura em **binário de 4 bits** (valores de 0 a 12).

O operador escolhe **qual sensor visualizar** usando duas chaves (Ch1, Ch2), e o valor aparece em **dois displays de 7 segmentos** mostrando decimal (ex: "08", "12").

Mas há dois detalhes importantes:
- **TB precisa ser ajustado: TB + 2**
- **TC precisa ser ajustado: TC + 3**
- Se o valor ajustado for **maior que 12**, um **alarme (AL)** deve acender

---

## Pensando no sistema em blocos

Antes de desenhar qualquer circuito, quebre o problema em partes:

```
[Bloco 1] Ajustar TB e TC  →  soma com constante
[Bloco 2] Selecionar qual temperatura mostrar  →  MUX
[Bloco 3] Detectar alarme (valor > 12)  →  comparador
[Bloco 4] Armazenar o alarme  →  flip-flop D
[Bloco 5] Converter para decimal e mostrar no display  →  BCD
```

---

## Bloco 1 — Ajuste de TB e TC

**Por que precisamos ajustar?**
O enunciado diz que os pontos B e C precisam ser corrigidos antes de exibir.
TB_real = TB + 2 e TC_real = TC + 3.

**Como fazer?**
O **74283** é um somador de 4 bits. Para somar uma constante, fixamos as entradas B nos valores da constante (ligadas no VCC ou GND).

```
TB + 2:
  [74283 #1]
  Entradas A = TB[3:0]        (4 bits do sensor B)
  Entradas B = 0010           (constante +2, fixas no hardware)
            B3=GND, B2=GND, B1=VCC, B0=GND
  Cin = GND (sem carry inicial)
  Saída = TB_ajustado[3:0]
```

```
TC + 3:
  [74283 #2]
  Entradas A = TC[3:0]
  Entradas B = 0011           (constante +3)
            B3=GND, B2=GND, B1=VCC, B0=VCC
  Cin = GND
  Saída = TC_ajustado[3:0]
```

> Não precisamos do Cout — TB máx = 12+2 = 14, TC máx = 12+3 = 15, ambos cabem em 4 bits.

---

## Bloco 2 — Seleção com MUX (as chaves Ch1 e Ch2)

**Por que precisamos de MUX?**
Temos 4 fontes de temperatura (TA, TB_aj, TC_aj, TD) mas só 1 display.
O MUX escolhe qual das 4 passa adiante baseado em Ch1 e Ch2.

**Tabela de seleção:**

| Ch1 | Ch2 | Temperatura exibida |
|-----|-----|---------------------|
|  0  |  0  | TA                  |
|  0  |  1  | TB_ajustado         |
|  1  |  0  | TC_ajustado         |
|  1  |  1  | TD                  |

**Como montar com 74157?**
O 74157 tem **4 MUX 2:1** em um chip, todos com 1 bit de seleção.
Para fazer um MUX 4:1 de 4 bits precisamos de **2 níveis**.

**Nível 1 — Chip A (select = Ch2):**
- Quando Ch2=0 → saída = TA
- Quando Ch2=1 → saída = TB_ajustado
- Resultado: T_grupo0[3:0]

**Nível 1 — Chip B (select = Ch2):**
- Quando Ch2=0 → saída = TC_ajustado
- Quando Ch2=1 → saída = TD
- Resultado: T_grupo1[3:0]

**Nível 2 — Chip C (select = Ch1):**
- Quando Ch1=0 → saída = T_grupo0 (TA ou TB_aj)
- Quando Ch1=1 → saída = T_grupo1 (TC_aj ou TD)
- Resultado final: **T[3:0]** (a temperatura selecionada)

```
TA[3:0]      ──→ A [Chip A, sel=Ch2] ──→ T_grupo0 ──→ A [Chip C, sel=Ch1] ──→ T[3:0]
TB_aj[3:0]   ──→ B [Chip A]                                                       ↑
                                                                                   │
TC_aj[3:0]   ──→ A [Chip B, sel=Ch2] ──→ T_grupo1 ──→ B [Chip C]
TD[3:0]      ──→ B [Chip B]
```

---

## Bloco 3 — Detectar Alarme (T > 12)

**Por que T > 12?**
Somente TB_aj e TC_aj podem superar 12 (máx 14 e 15 respectivamente).
Se o operador selecionar TB_aj=13 por exemplo, o alarme deve acender.

**Como detectar?**
O **7485** é um comparador de 4 bits. Conectamos:
- Entrada A = T[3:0] (a temperatura selecionada já ajustada)
- Entrada B = 1100 (constante 12, fixada no hardware)
  - B3=VCC, B2=VCC, B1=GND, B0=GND
- Entradas de cascade = IA>B=0, IA=B=1, IA<B=0 (padrão início de cascata)

```
Saída OA>B = 1  →  T > 12  →  TRIG (dispara o alarme)
Saída OA=B = 1  →  T = 12  →  sem alarme
Saída OA<B = 1  →  T < 12  →  sem alarme
```

---

## Bloco 4 — Armazenar o Alarme no FF D (7474)

**Por que precisamos de FF?**
O enunciado diz: "o sinal de alarme deve ser armazenado em flip-flop e só pode ser zerado pelo operador".

Isso significa: **mesmo que a temperatura volte ao normal, o alarme continua aceso** até o operador pressionar R-AL.

**Como usar o 7474?**
O 7474 tem entradas assíncronas de **Preset** (coloca Q=1) e **Clear** (coloca Q=0), ambas ativas em nível baixo.

- Quando TRIG=1 (alarme disparado): queremos Q=1 → ativamos **Preset**
- Quando operador aperta R-AL: queremos Q=0 → ativamos **Clear**

```
TRIG ──→ [NOT] ──→ Preset (ativo baixo) [7474]
R-AL ──→ [NOT] ──→ Clear  (ativo baixo) [7474]
                            Q = AL (saída do alarme)
```

> D e CLK não importam aqui — Preset e Clear são assíncronos e têm prioridade sobre D.

**Comportamento:**
- TRIG vai a 1 (temp > 12) → Preset ativa → Q=AL=1 (alarme acende)
- TRIG volta a 0 (temp ≤ 12) → Preset desativa → **Q continua 1** (alarme memorizado!)
- Operador aperta R-AL → Clear ativa → Q=AL=0 (alarme apaga)

---

## Bloco 5 — Conversão BCD para o Display

**O problema:**
A temperatura T[3:0] é um número binário de 0 a 15.
Os displays de 7 segmentos esperam **BCD** (Binary Coded Decimal).
Em BCD, dezenas e unidades são separadas.

Exemplos:
- T=9 → mostrar "09" → dezenas=0, unidades=9
- T=10 → mostrar "10" → dezenas=1, unidades=0
- T=14 → mostrar "14" → dezenas=1, unidades=4

**Quando o dígito das dezenas vira 1?**
Quando T ≥ 10. Precisamos detectar isso.

**Detectando T ≥ 10:**

Olhando os valores em binário:
- T=8 (1000): NÃO é ≥ 10
- T=9 (1001): NÃO é ≥ 10
- T=10 (1010): SIM
- T=11 (1011): SIM
- T=12 (1100): SIM
- T=13 (1101): SIM
- T=14 (1110): SIM
- T=15 (1111): SIM

Padrão: T ≥ 10 quando **T3=1 E (T2=1 OU T1=1)**

```
GE10 = T3 · (T2 + T1)
```

Verificação rápida:
- T=8 (1000): T3=1, T2=0, T1=0 → GE10=1·(0+0)=0 ✓
- T=10 (1010): T3=1, T2=0, T1=1 → GE10=1·(0+1)=1 ✓
- T=12 (1100): T3=1, T2=1, T1=0 → GE10=1·(1+0)=1 ✓

**Calculando as unidades (subtraindo 10 quando GE10=1):**

Truque: em vez de subtrair 10, **somamos 6** (pois 10+6=16, que em 4 bits = 0000 com carry 1).

Usando **74283 #3**:
- Entradas A = T[3:0]
- Entradas B = 0110 quando GE10=1, ou 0000 quando GE10=0
  - B3=GND, B2=GE10, B1=GE10, B0=GND
- Cin = GND
- Saída S[3:0] = **UNIDADES** (dígito das unidades em BCD)
- Cout = **carry_bcd**

**Calculando as dezenas:**
```
TENS = carry_bcd
```

Por que funciona:
- T=9: 9+0=9, carry=0 → TENS=0, unidades=9 → "09" ✓
- T=10: 10+6=16=10000, carry=1, 4 bits=0000 → TENS=1, unidades=0 → "10" ✓
- T=12: 12+6=18=10010, carry=1, 4 bits=0010 → TENS=1, unidades=2 → "12" ✓
- T=15: 15+6=21=10101, carry=1, 4 bits=0101 → TENS=1, unidades=5 → "15" ✓

---

## Diagrama completo do sistema

```
TA[3:0] ──────────────────────────────────────────────────────┐
                                                               │
TB[3:0] ──→ [74283 #1: +2] ──→ TB_aj[3:0] ──────────────────→│
                                                               │
TC[3:0] ──→ [74283 #2: +3] ──→ TC_aj[3:0] ──────────────────→│
                                                               │
TD[3:0] ──────────────────────────────────────────────────────┘
                                        │
                                  [MUX 4:1]
                                  3×74157
                                  sel: Ch1,Ch2
                                        │
                                     T[3:0]
                                        │
                   ┌────────────────────┼──────────────────┐
                   │                    │                  │
            [7485: A=T,           GE10=T3·(T2+T1)         │
             B=1100(12)]               │                   │
                   │           [74283 #3: A=T,             │
                TRIG=OA>B        B=0,GE10,GE10,0]         │
                   │                   │                   │
            [NOT]──→PRESET          UNIDADES[3:0]      carry_bcd=TENS
            [7474]                     │                   │
            [NOT]──→CLEAR      [Dec BCD→7seg]         [Display dezenas]
               ↑                       │
             R-AL               [Display unidades]
               │
               Q = AL (alarme)
```

---

## Lista de CIs e funções

| CI | Qtd | Para que serve |
|----|-----|---------------|
| 74283 | 3 | #1: TB+2 / #2: TC+3 / #3: correção BCD |
| 74157 | 3 | MUX 4:1 para seleção de temperatura |
| 7485 | 1 | Comparar T com 12 (detectar alarme) |
| 7474 | 1 | Armazenar o alarme |
| 7404 | 1 | NOT para Preset e Clear do 7474 |
| 7432 | 1 | OR para T2+T1 (parte do GE10) |
| 7408 | 1 | AND para T3·(T2+T1) = GE10 |
| 7448/7447 | 1 | Decoder BCD→7 segmentos |

---

## Resumo do raciocínio (para memorizar)

1. **Ajuste** → 74283 com entradas B fixas (hardwired)
2. **Seleção** → MUX 4:1 em 2 níveis com 74157
3. **Alarme** → 7485 compara com 12, resultado vai ao Preset do 7474
4. **Reset do alarme** → Clear do 7474 via chave R-AL
5. **BCD** → detectar ≥10 com GE10, somar 6 com 74283, carry = dezena
