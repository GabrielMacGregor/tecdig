# Resumo — Aula 6: Demultiplexadores, Codificadores e Somador BCD

## 1. Demultiplexador (DEMUX)

O **DEMUX** é o oposto do MUX: recebe **uma entrada** e direciona seu conteúdo para **uma das saídas**, selecionada pelos bits de controle.

> MUX = muitas entradas, 1 saída. **DEMUX = 1 entrada, muitas saídas.**

### DEMUX 1 × 4

1 entrada de dados (Dᵢₙ), 4 saídas (S0–S3), 2 bits de controle (C₁, C₀):

| C₁ | C₀ | S0   | S1   | S2   | S3   |
|----|-----|------|------|------|------|
|  0 |  0  | Dᵢₙ  |  0   |  0   |  0   |
|  0 |  1  |  0   | Dᵢₙ  |  0   |  0   |
|  1 |  0  |  0   |  0   | Dᵢₙ  |  0   |
|  1 |  1  |  0   |  0   |  0   | Dᵢₙ  |

### Relação com o Decodificador

> **O mesmo circuito que implementa o DEC 2×4 implementa o DEMUX 1×4.**
>
> A diferença é funcional: no DEMUX, a entrada de **ENABLE passa a ser tratada como o dado de entrada** (Dᵢₙ), enquanto C₁ e C₀ selecionam qual saída recebe esse dado.

---

## 2. Codificador (Encoder)

O **Codificador** é o oposto do Decodificador:
- Decoder: recebe código → ativa a saída correspondente
- **Encoder: recebe uma linha ativa → gera na saída o código que identifica essa linha**

### Codificador 4 × 2

4 entradas (E0–E3), 2 saídas (C₁, C₀). Apenas **uma entrada** deve estar ativa por vez:

| E0 | E1 | E2 | E3 | C₁ | C₀ |
|----|----|----|-----|-----|-----|
|  1 |  0 |  0 |  0 |  0  |  0  |
|  0 |  1 |  0 |  0 |  0  |  1  |
|  0 |  0 |  1 |  0 |  1  |  0  |
|  0 |  0 |  0 |  1 |  1  |  1  |

### Problema do Codificador Simples

Quando **nenhuma entrada está ativa** (todas em 0), a saída também é 00 — idêntica ao caso em que E0 está ativa. Não é possível distinguir "sem entrada ativa" de "E0 ativa".

**Solução:** acrescentar uma saída de **VALID (V)**:
- V = 1 → pelo menos uma entrada está ativa (saída C₁C₀ é válida)
- V = 0 → nenhuma entrada ativa (saída C₁C₀ não tem significado)

Expressões lógicas:
```
C₁ = E2 + E3
C₀ = E1 + E3
V  = E0 + E1 + E2 + E3
```

---

## 3. Somador BCD

O Somador BCD soma dois dígitos decimais representados em BCD (4 bits cada) e produz o resultado em BCD.

### Por que não basta somar em binário?

A soma binária de dois dígitos BCD pode produzir resultados inválidos para o BCD (combinações 1010 a 1111) ou um carry que não está alinhado com a base 10.

### Regra de correção

Após a soma binária de 4 bits:
- Se resultado > 9 **(1001)** OU houve carry de saída → somar **6 (0110)** ao resultado (ajuste BCD)
- O carry do ajuste indica que há um "vai-um" para o dígito BCD mais significativo

### Tabela de mapeamento

| Soma Binária | Carry + BCD correto |
|--------------|---------------------|
| 0000–1001    | 0 + resultado (sem ajuste) |
| 1010–1111 ou carry | 1 + (resultado − 10) via +6 |

### Exemplos

**Exemplo 1: 9 + 3 = 12**
```
  1001   (9 em BCD)
+ 0011   (3 em BCD)
------
  1100   (soma binária = 12, inválido em BCD)
+ 0110   (ajuste +6)
------
1 0010   → carry=1, dígito=0010(2) → resultado: 12 ✓
```

**Exemplo 2: 9 + 9 = 18**
```
  1001   (9)
+ 1001   (9)
------
1 0010   (soma binária = 18, já gerou carry)
+  110   (ajuste +6 apenas no nibble baixo)
-------
1 1000   → carry=1 propagado, dígito=1000(8) → resultado: 18 ✓
```

**Exemplo 3: 99 + 93 = 192 (2 dígitos BCD)**
```
  1001 1001   (99)
+ 1001 0011   (93)
-----------
1 0010 1100   (soma binária)
         110  (ajuste no dígito das unidades: 1100 > 9)
-----------
1 0011 0010   (ajuste parcial)
   110         (ajuste no dígito das dezenas: 0011 + carry = 1 0011 → ainda > 9? Não... ajuste necessário se > 9)
```
> Em somas multi-dígito o ajuste é aplicado em **cada dígito BCD individualmente**, da direita para a esquerda, propagando carries.

### Implementação em hardware

O Somador BCD usa:
1. Um **somador binário de 4 bits** para a soma inicial
2. Um circuito detector de "necessidade de ajuste" (resultado > 9 ou carry)
3. Um segundo somador que adiciona 0110 quando necessário
4. O carry de saída do segundo somador é o carry BCD para o próximo dígito

---

## 4. Comparativo dos 4 dispositivos da Aula 5 e 6

| Dispositivo | Entradas | Saídas | Função |
|-------------|----------|--------|--------|
| **MUX N×1** | N dados + log₂N seleção | 1 | Seleciona qual entrada vai para a saída |
| **DEMUX 1×N** | 1 dado + log₂N seleção | N | Envia entrada para a saída selecionada |
| **Decoder n×2ⁿ** | n código | 2ⁿ | Ativa a saída correspondente ao código |
| **Encoder 2ⁿ×n** | 2ⁿ linhas | n código | Gera o código da linha ativa |

> **Relação fundamental:** Decodificador com ENABLE = Demultiplexador (mesmos circuitos, uso diferente do pino EN).

---

## 5. Pontos-chave para a prova

1. **DEMUX** = oposto do MUX; direciona a entrada para a saída selecionada
2. DEC 2×4 com ENABLE funciona como **DEMUX 1×4** — o ENABLE vira a entrada de dados
3. **Encoder** gera código a partir da linha ativa; precisa de saída VALID para distinguir "nenhuma ativa" de E0 ativa
4. Expressões do Encoder 4×2: `C₁ = E2 + E3`, `C₀ = E1 + E3`
5. **Somador BCD:** soma binária → se resultado > 9 ou carry → adicionar 6 (ajuste BCD)
6. Ajuste BCD (+6) é necessário porque o BCD "pula" 6 combinações (1010–1111) em relação ao binário

---

## Conexão com as próximas aulas

- **Aulas 7–9**: Flip-flops, contadores e registradores (circuitos sequenciais — com memória)
- O conceito de "dado que trafega por um barramento selecionado" (MUX/DEMUX) reaparece na Aula 15 (Tri-State e Memória)
