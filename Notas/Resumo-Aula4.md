# Resumo — Aula 4: Don't Care, Mapa de Karnaugh e Somadores

## Contexto

As Aulas 1–3 ensinaram a construir e representar funções booleanas. A Aula 4 traz duas contribuições: o conceito de **Don't Care** (que melhora a minimização) e o projeto de **somadores** como aplicação prática.

---

## 1. Don't Care (X) — Entradas Inexistentes

### O que é?

Em muitos sistemas existem combinações de entrada que **nunca ocorrem** ou cujo resultado é **irrelevante**. Chamamos essas posições de **Don't Care** e as marcamos com **X** no Mapa de Karnaugh.

### Por que usar?

Ao preencher o K-map, os X's podem ser tratados como **0 ou 1**, conforme o que permitir **agrupar mais células**. Isso resulta em uma expressão mais simples.

> **Regra de ouro:** use o X se ele ajudar a formar um grupo maior. Ignore-o se não ajudar.

---

## 2. Exemplo com BCD

**Problema:** projetar um circuito com entrada BCD (0–9) cuja saída seja 1 se o dígito for 2, 4 ou 5.

**Observação:** dígitos BCD válidos são de 0 a 9. As combinações 10–15 (1010 a 1111) nunca ocorrem → são **Don't Care**.

### Mapa de Karnaugh para SDP (saída = 1 em 2, 4, 5):

```
         AB
    CD   00  01  11  10
    00    0   1   X   1
    01    0   1   X   0
    11    0   0   X   X
    10    1   0   X   X
```
(1 nas posições 2, 4, 5; X nas posições 10–15)

**Agrupamentos:**
- 4 e 5 (+ X's adjacentes) → agrupamento de 4 → elimina C e D → **B.C̄** = B.C̄... 

Resultado minimizado:
```
S = B.C̄ + B̄.C.D̄
```
(forma simplificada com uso dos Don't Cares como 1)

### Minimização por PDS (agrupando os 0s):

Quando os Don't Cares não cobertos são assumidos como 1, e os cobertos como 0:
```
S = B
```
(muito mais simples! O K-map para PDS pode dar resultado diferente do SDP)

---

## 3. Somador de 1 Bit (Full Adder)

### O problema

Projetar um circuito que soma dois bits **Ai** e **Bi** com um **Carry de entrada (Cin)**, gerando a **soma (Si)** e o **carry de saída (Cout)**.

### Tabela Verdade

| Ai | Bi | Cin | Cout | Si |
|----|----|----|------|----|
| 0  | 0  | 0  |  0   | 0  |
| 0  | 0  | 1  |  0   | 1  |
| 0  | 1  | 0  |  0   | 1  |
| 0  | 1  | 1  |  1   | 0  |
| 1  | 0  | 0  |  0   | 1  |
| 1  | 0  | 1  |  1   | 0  |
| 1  | 1  | 0  |  1   | 0  |
| 1  | 1  | 1  |  1   | 1  |

### K-map para Cout

```
      AiBi
Cin   00  01  11  10
  0    0   0   1   0
  1    0   1   1   1
```

Agrupamentos: {AiBi=11, Cin=0}, {AiBi=11, Cin=1}, {AiBi=01, Cin=1}, {AiBi=10, Cin=1}

```
Cout = Bi.Cin + Ai.Cin + Ai.Bi
```

### K-map para Si

Si = 1 apenas quando o número de 1s nas entradas é ímpar → é a **função XOR tripla**:

```
Si = Ai ⊕ Bi ⊕ Cin
```

ou em forma canônica:
```
Si = Ā.B̄.C + Ā.B.C̄ + A.B̄.C̄ + A.B.C
```

---

## 4. Somador de 2 Bits em Cascata

Para somar dois números de 2 bits (A1A0 + B1B0):
- Use dois Full Adders
- Conecte o **Cout do bit 0** ao **Cin do bit 1**

```
[FA bit 0]: A0, B0, Cin=0  →  S0, Cout0
[FA bit 1]: A1, B1, Cin=Cout0  →  S1, Cout1 (carry final)
```

---

## 5. Somador de 4 Bits — CI 74LS283

Para somar números de 4 bits, cascateiam-se 4 Full Adders. Isso é implementado no CI **74LS283** (ou 74283), que possui:

- Entradas: A3–A0, B3–B0, C0 (carry de entrada)
- Saídas: S3–S0, C4 (carry de saída)

Para números maiores (8, 16 bits...), basta cascatear múltiplos 74LS283.

---

## 6. Comparador de Magnitude — CI 7485

O **7485** compara dois números binários de 4 bits e possui saídas para:
- A > B
- A = B
- A < B

Também pode ser cascateado para comparar números de 8, 12, 16 bits.

---

## Resumo dos conceitos

| Conceito | Para que serve |
|----------|---------------|
| Don't Care (X) | Aproveitar entradas impossíveis para simplificar mais o K-map |
| Full Adder | Soma de 1 bit com propagação de carry |
| Cascateamento | Expandir para N bits usando módulos de 4 bits |
| 74LS283 | CI somador de 4 bits |
| 7485 | CI comparador de magnitude de 4 bits |

---

## Conexão com as próximas aulas

A Aula 5 sai dos circuitos puramente lógicos e apresenta **componentes combinacionais prontos**: Multiplexadores e Decodificadores, que permitem construir sistemas mais complexos reutilizando blocos padrão.
