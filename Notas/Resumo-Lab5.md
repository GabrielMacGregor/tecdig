# Lab 5 — Divisores por 24, Contador 0–23 e Shift Registers

---

## Parte 1a — Divisor por 24 usando 7493

### O que é o 7493

O **7493** é um contador binário de 4 bits composto por:
- 1 FF independente: divisor por 2 (entrada CKA, saída QA)
- 3 FFs em cascata: divisor por 8 (entrada CKB, saídas QB, QC, QD)

Para obter **divisor por 16**: conecte QA → CKB.

### Divisor por 24 com 7493

24 em binário = **11000** → precisamos de 5 bits → use **dois 7493**.

**Montagem:**
- 7493 #1: QA → CKB (divisor por 16) → QD_1, QC_1, QB_1, QA_1 = bits 3–0 da contagem
- 7493 #2: CKA recebe QD_1 (overflow do #1) → QA_2 = bit 4 da contagem

**Detecção do estado 24 = 11000:**
- Q4=1 (QA_2=1) E Q3=1 (QD_1=1) → os demais já são 0

```
QA_2 ─┐
QD_1 ─┤── AND ──→ MR1 e MR2 (de ambos os 7493)
```

Quando o contador chega em 24, o AND zera tudo → conta de 0 a 23.

### Diagrama

```
CLK ─→ [7493 #1] ─→ QA_1, QB_1, QC_1, QD_1 (bits 0–3)
         QA→CKB           │
                          QD_1 ─→ CKA [7493 #2] → QA_2 (bit 4)

QD_1 ─┐
QA_2 ─┤── AND ──→ MR1=MR2 (ambos os CIs) → reset quando chega em 24
```

> Ligar o clock na entrada **A** (CKA) conforme pedido no lab.

---

## Parte 1b — Divisor por 24 usando 7490

### O que é o 7490

O **7490** é um contador decimal composto por:
- 1 FF: divisor por 2 (entrada A, saída QA)
- 3 FFs: divisor por 5 (entrada B, saídas QB, QC, QD)

Para obter **divisor por 10**: conecte QA → B.

### Divisor por 24 com 7490

Use dois 7490 em cascata:

**7490 #1:** conta de 0 a 9 (unidades)
**7490 #2:** recebe carry do #1 e conta as dezenas

**Detecção do estado 24:**
- Dezenas = 2 = **0010** em BCD → QB_2=1
- Unidades = 4 = **0100** em BCD → QC_1=1

```
QB_2 ─┐
QC_1 ─┤── AND ──→ MR1=MR2 (ambos os 7490) → reset em 24
```

### Diagrama

```
CLK ─→ [7490 #1] ─→ QA,QB,QC,QD (unidades 0–9)
         QA→B              │
                           QD_1 ─→ A [7490 #2] → dezenas

QB_2 ─┐
QC_1 ─┤── AND ──→ MR1=MR2 (ambos) → reset quando conta 24
```

---

## Parte 2 — Contador 0–23 com FF D (set em 10, reset em 18)

Usando o circuito da parte 1a (contador 0–23 com 7493):

**Detectar estado 10 = 01010:**
- Q3=0, Q2=1, Q1=0... mais simples: Q1=1 E Q3=1? Não.
- 10 = 01010: Q4=0, Q3=1, Q2=0, Q1=1, Q0=0
- Detectar: QB_1=1 E QD_1=1 (e os outros 0, mas simplificado)
- → AND(QB_1, QD_1) → **Set do FF D** (preset assíncrono)

**Detectar estado 18 = 10010:**
- 18: Q4=1, Q3=0, Q2=0, Q1=1, Q0=0
- Detectar: QA_2=1 E QB_1=1
- → AND(QA_2, QB_1) → **Reset do FF D** (clear assíncrono)

```
QB_1 ─┐
QD_1 ─┤── AND ──→ Preset (Set) do FF D   ← estado 10

QA_2 ─┐
QB_1 ─┤── AND ──→ Clear (Reset) do FF D  ← estado 18
```

---

## Parte 3 — Serialização com 74165 e 74164

### O que é o 74165 (PISO — Paralelo para Série)

- 8 entradas paralelas (H, G, F, E, D, C, B, A)
- Carrega com pino **SH/LD = 0** (um pulso de clock)
- Serializa com **SH/LD = 1** e pulsos de clock
- Saída serial: pino **QH**
- Entrada serial (para cascata): SER

### O que é o 74164 (SIPO — Série para Paralelo)

- Entradas de dado serial: **A e B** (ligadas juntas)
- Acumula 1 bit por pulso de clock
- Após 8 clocks: saídas paralelas QA–QH contêm os 8 bits
- Reset assíncrono: pino CLR (ativo baixo)

### Interligação dos dois CIs

```
[74165] QH (saída série) ──────→ A e B (entradas série) [74164]
              ↑                              ↑
         Clock comum ────────────────────────
```

### Funcionamento passo a passo

1. **Carregar:** SH/LD = 0 → 1 pulso de clock → 8 bits entram no 74165
2. **Serializar:** SH/LD = 1 → 8 pulsos de clock
   - A cada clock: 74165 envia 1 bit pelo QH
   - A cada clock: 74164 recebe 1 bit e desloca internamente
3. **Resultado:** após 8 clocks, QA–QH do 74164 contêm os 8 bits recebidos

### Diagrama completo

```
[Ch. Hexa 1] ──→ H..A [74165]
[Ch. Hexa 2] ──→ H..A [74165 em cascata, se necessário]

SH/LD ──→ 74165 (chave digital: 0=carrega, 1=desloca)
CLK   ──→ 74165 e 74164 (chave digital, pulso manual)

74165 QH ──→ AB [74164]

74164 QA..QD ──→ [Display 7-seg 1]
74164 QE..QH ──→ [Display 7-seg 2]
```

---

## Parte 4 — Sinal de transmissão concluída

Após 8 pulsos de clock, todos os 8 bits foram transmitidos/recebidos.

**Estratégia:** use um contador de 3 bits (conta até 8):

```
CLK ──→ [contador 3 bits] ──→ quando chega em 8: seta FF D → sinal DONE=1
```

Ou mais simples: use um **registrador de deslocamento de 8 bits** com 1 na entrada:
- Após 8 clocks, o 1 chega na última posição → indica conclusão

---

## Resumo dos CIs por parte

| Parte | CI | Função |
|-------|-----|--------|
| 1a    | 7493 × 2 | Divisor por 24 (binário) |
| 1b    | 7490 × 2 | Divisor por 24 (decimal) |
| 2     | 7493 × 2 + 7474 | Contador 0–23 com FF D |
| 3     | **74165** | Conversor paralelo → série (PISO) |
| 3     | **74164** | Conversor série → paralelo (SIPO) |
| 4     | 7474 ou contador | Sinal de conclusão |

---

## O que montar na protoboard (físico)

Apenas o **item 1a e 1b** (divisores por 24 com 7493 e 7490).
Todos os outros itens são apenas simulação no CircuitMaker.
