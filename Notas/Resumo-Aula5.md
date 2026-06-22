# Resumo — Aula 5: Multiplexadores (MUX) e Decodificadores

## 1. Multiplexador (MUX)

Um **multiplexador** é um dispositivo que **seleciona qual entrada será conectada à saída** com base em sinais de controle (seleção).

Analogia: pense no MUX como um comutador de trilhos — várias entradas chegam, mas apenas uma é deixada passar.

### Estrutura geral

Um MUX com **N entradas de dados** precisa de **log₂(N) bits de seleção**.

| Tipo    | Entradas de dados | Bits de seleção | Saídas |
|---------|-------------------|-----------------|--------|
| 2 × 1   | 2                 | 1 (C₀)          | 1      |
| 4 × 1   | 4                 | 2 (C₁, C₀)      | 1      |
| 8 × 1   | 8                 | 3 (C₂, C₁, C₀)  | 1      |

### MUX 2 × 1 — como funciona internamente

A expressão lógica da saída é:
```
S = (C̄₀ · E₀) + (C₀ · E₁)
```
- C₀ = 0 → S recebe E₀
- C₀ = 1 → S recebe E₁

O circuito usa: 1 inversor (NOT), 2 portas AND, 1 porta OR.

---

## 2. Associação de MUX (Expansão)

Se precisar de um MUX maior e só tiver os menores, associe em dois níveis:

**MUX 8 × 1 com dois MUX 4 × 1 e um MUX 2 × 1:**
- MUX A (4×1) recebe entradas E0–E3, controlado por S₁S₀
- MUX B (4×1) recebe entradas E4–E7, controlado por S₁S₀
- MUX final (2×1) escolhe entre saída de A e B usando S₂

> **Regra prática:** as entradas de seleção dos MUX de primeiro nível são compartilhadas (mesmas linhas S₁ e S₀).

---

## 3. MUX com Variáveis Multi-bit

Para multiplexar um sinal com mais de 1 bit (ex: dígito BCD = 4 bits):

- Usa-se **um MUX para cada bit** do dado
- Todas as entradas de seleção são **interligadas** (mesma linha de controle seleciona o mesmo canal em todos os MUX)

**Exemplo prático:** Monitorar 4 elevadores (9 andares, 1 display de 7 segmentos):
- Cada elevador fornece um dígito BCD de 4 bits (posição do andar)
- 4 MUX 4×1 (um por bit do BCD) com as mesmas 2 chaves de seleção
- A saída dos MUX vai para um decodificador BCD → 7 segmentos → display

---

## 4. Decodificador

Um **decodificador** identifica o código presente na entrada e ativa **apenas a saída correspondente**.

### Decodificador 2 × 4

2 entradas (C₁, C₀), 4 saídas (S0–S3). Apenas **uma saída ativa por vez**.

| C₁ | C₀ | S0 | S1 | S2 | S3 |
|----|----|----|----|----|-----|
|  0 |  0 |  1 |  0 |  0 |  0 |
|  0 |  1 |  0 |  1 |  0 |  0 |
|  1 |  0 |  0 |  0 |  1 |  0 |
|  1 |  1 |  0 |  0 |  0 |  1 |

Expressões lógicas das saídas (ativo em nível 1):
```
S0 = C̄₁ · C̄₀      S2 = C₁ · C̄₀
S1 = C̄₁ · C₀       S3 = C₁ · C₀
```

### Saída ativa em nível lógico "0"

Algumas implementações ativam a saída selecionada com nível **0** (as demais ficam em 1). A tabela é simplesmente o complemento da anterior — observe o círculo (bolinha) no símbolo de saída.

---

## 5. Entrada de ENABLE

O decodificador pode ter um pino **ENABLE (EN)**:
- EN ativo → decodificador funciona normalmente
- EN inativo → **todas as saídas ficam desativadas**, independente das entradas

> O EN pode ser ativo em "0" ou em "1" — verifique o símbolo (bolinha = ativo em "0").

---

## 6. Expansão de Decodificadores

Assim como MUX, decodificadores menores podem ser cascateados para formar decodificadores maiores.

### DEC 3 × 8 com dois DEC 2 × 4 (com ENABLE)

- Ambos os DEC 2×4 recebem as mesmas entradas E₁ e E₀ (bits menos significativos)
- E₂ (bit mais significativo) controla o ENABLE:
  - E₂ = 0 → apenas o primeiro DEC (saídas S0–S3) está habilitado
  - E₂ = 1 → apenas o segundo DEC (saídas S4–S7) está habilitado (via inversor)

### DEC 4 × 16 com DEC 2 × 4

- Um DEC 2×4 "principal" decodifica os 2 bits mais significativos (E₃E₂)
- Suas 4 saídas ligam ao EN dos 4 DEC 2×4 "secundários"
- Os DEC secundários recebem os 2 bits menos significativos (E₁E₀)
- Cada DEC secundário gera 4 saídas → total = 16 saídas

### DEC 5 × 32

Mesmo princípio, com dois blocos DEC 4×16 habilitados pelo bit mais significativo.

---

## 7. Exemplo de Projeto com Decodificador

**Problema:** sistema de 6 bits (0 a 63). Detectar em nível lógico "0" os intervalos 16–31 e 48–51.

Análise binária:
- 16 = 010000, 31 = 011111 → bits 5,4,3 = 010 (fixo), bits 2,1,0 qualquer → detectar quando E₅=0, E₄=1, E₃=0
- 48 = 110000, 51 = 110011 → bits 5,4 = 11 (fixo), bits 3,2 = 00, bits 1,0 = 00 a 11

Solução: decodificar os bits mais significativos com um DEC e usar a saída correspondente, complementada se necessário.

---

## 8. Pontos-chave para a prova

1. **MUX** = seleciona uma entrada para a saída; **N entradas** exigem **log₂(N) bits de seleção**
2. **Expressão do MUX 2×1:** S = C̄₀·E₀ + C₀·E₁
3. Para variável multi-bit: **um MUX por bit**, **mesmas linhas de seleção**
4. **DECODER** = ativa UMA saída correspondente ao código de entrada
5. Saída pode ser ativa em "0" ou em "1" — atenção ao símbolo!
6. **ENABLE** desabilita todas as saídas quando inativo — usado para expandir decodificadores
7. Para montar DEC 3×8 com dois 2×4: o bit extra vai para o EN (com inversor em um deles)

---

## Conexão com as próximas aulas

- **Aula 6**: DEMUX (operação inversa ao MUX) e Codificadores (operação inversa ao Decoder)
- **Aulas 7–9**: circuitos sequenciais (flip-flops) onde os conceitos de seleção e habilitação voltam a aparecer
