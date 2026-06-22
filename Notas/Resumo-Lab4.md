# Lab 4 — Decodificador 74138 + Flip-Flops + Divisor por 21

---

## Parte 1 — Controle de dispositivos por endereço de 5 bits

### O que o circuito faz

Um celular envia um endereço de 5 bits e um pulso **Exec**.
O circuito liga/desliga dispositivos conforme a tabela:

| Endereço | Decimal | Binário (A4..A0) | Comando |
|----------|---------|-----------------|---------|
| 0        | 00000   | Desliga todos   |         |
| 8        | 01000   | Liga A          |         |
| 9        | 01001   | Desliga A       |         |
| 10       | 01010   | Liga B          |         |
| 11       | 01011   | Desliga B       |         |

- Dispositivo A → saída Q do **FF 7473 (JK)**
- Dispositivo B → saída Q do **FF 7474 (D)**

---

### O que é o 74138

O **74138** é um decodificador **3×8** (3 entradas → 8 saídas ativas em nível baixo).

Possui 3 enables: G1 (ativo alto), G2A e G2B (ativos baixo).
Só funciona quando: G1=1, G2A=0, G2B=0.

---

### Estratégia: dois 74138 para cobrir 32 endereços

Com 5 bits de endereço (A4..A0), precisamos decodificar até o endereço 11.
Os endereços relevantes são todos menores que 16 (A4=0).

**74138 #1 — endereços 0–7 (A4=0, A3=0):**
- Entradas A, B, C = A2, A1, A0
- Enable: G1=1, G2A=A3, G2B=A4 → ativo apenas quando A4=0 E A3=0

**74138 #2 — endereços 8–15 (A4=0, A3=1):**
- Entradas A, B, C = A2, A1, A0
- Enable: G1=A3, G2A=0, G2B=A4 → ativo apenas quando A4=0 E A3=1

| Endereço | Saída ativa | CI |
|----------|------------|-----|
| 0        | Y0 do #1   | 74138 #1 |
| 8        | Y0 do #2   | 74138 #2 |
| 9        | Y1 do #2   | 74138 #2 |
| 10       | Y2 do #2   | 74138 #2 |
| 11       | Y3 do #2   | 74138 #2 |

> As saídas dos 74138 são ativas em **nível baixo**.

---

### Conexão com os Flip-Flops

**FF A — 7473 (tipo JK):**
- **Set (Liga A):** endereço 8 → Y0 do 74138 #2 vai ao CLR? Não — use as entradas J e K
  - J=1, K=0 na borda do clock (Exec) → Q=1 (liga)
  - J=0, K=1 na borda do clock (Exec) → Q=0 (desliga)
- Solução prática: Y0_#2 (ativo baixo) conecta ao **Preset** do JK, Y1_#2 ao **Clear**

**FF B — 7474 (tipo D):**
- Possui entradas de **Preset** e **Clear** assíncronos (ativos baixo)
- Y2_#2 (endereço 10) → **Preset** (liga B)
- Y3_#2 (endereço 11) → **Clear** (desliga B)

**Desliga todos (endereço 0):**
- Y0_#1 (ativo baixo) → conecta ao **Clear** de ambos os FFs

**Pulso Exec:**
- Gerado por uma chave digital
- Serve como clock dos FFs JK (7473 é disparado por borda de descida)

### Diagrama

```
A4..A0 ─┬──→ [74138 #1, A4=0,A3=0] ──→ Y0 (end.0) → Clear FF_A e FF_B
         │
         └──→ [74138 #2, A4=0,A3=1] ──→ Y0 (end.8)  → Preset FF_A (Liga A)
                                       → Y1 (end.9)  → Clear FF_A (Desliga A)
                                       → Y2 (end.10) → Preset FF_B (Liga B)
                                       → Y3 (end.11) → Clear FF_B (Desliga B)

Exec ──→ Clock do FF_A (7473) e FF_B (7474)

FF_A (7473): Q_A = Dispositivo A
FF_B (7474): Q_B = Dispositivo B
```

---

## Parte 2 — Divisor de frequência por 21 com FFs JK

### Ideia

Construir um contador que conta de 0 a 20 e reseta quando chega em 21.
O período de saída = 21 × período do clock de entrada → divide por 21.

### Quantos FFs?

21 em binário = **10101** → precisamos de **5 FFs JK** (conta até pelo menos 21)

### Conexão dos FFs (contador assíncrono)

```
CLK ──→ FF0 (Q0) ──→ FF1 (Q1) ──→ FF2 (Q2) ──→ FF3 (Q3) ──→ FF4 (Q4)
        (CLK)        (Q0)         (Q1)         (Q2)         (Q3)
```

Cada FF é disparado pela saída do anterior (contador assíncrono / ripple).
Todos os FFs em modo toggle: J=1, K=1.

### Detecção do estado 21

21 = **10101** → Q4=1, Q3=0, Q2=1, Q1=0, Q0=1

Detectar com porta AND: `Q4 AND Q2 AND Q0`
- Q3 e Q1 já são 0 quando os outros são 1 → não precisa detectá-los

### Reset

A saída da porta AND conecta ao **Clear assíncrono** de **todos os FFs** simultaneamente:

```
Q4 ─┐
Q2 ─┤── AND ──→ Clear (todos os FFs)
Q0 ─┘
```

Quando o contador chega em 21, o AND vai para 1, zera tudo instantaneamente, e o ciclo recomeça.

### Diagrama

```
         J=1            J=1             J=1            J=1            J=1
CLK─→[FF0]─Q0─→[FF1]─Q1─→[FF2]─Q2─→[FF3]─Q3─→[FF4]─Q4
         K=1            K=1             K=1            K=1            K=1
          │                              │                             │
          └──────────────────────────────┴─────── AND ──→ CLR (todos)
                                        Q0        Q2      Q4
```

### CIs necessários

| CI | Função |
|----|--------|
| 74138 × 2 | Decodificador de endereços |
| 7473 | FF JK (dispositivo A) |
| 7474 | FF D (dispositivo B) |
| 7410 ou 7411 | AND3 (detecção do estado 21) |
