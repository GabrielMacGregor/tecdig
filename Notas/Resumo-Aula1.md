# Resumo — Aula 1: Sistemas Digitais e Introdução aos Circuitos Combinacionais

## 1. Sistema Digital

Um **sistema digital** representa informação por sinais que só assumem valores **discretos** — ao contrário dos sistemas analógicos, onde os valores variam de forma contínua.

**Exemplo clássico:** um medidor de combustível analógico mostra um ponteiro em qualquer posição; o digital mostra apenas valores fixos (cheio, ¾, ½, etc.).

No caso mais comum usamos o **sistema binário**: dois valores discretos, convencionalmente chamados de **0** e **1** (ou FALSO/VERDADEIRO, LOW/HIGH, desligado/ligado).

---

## 2. Tipos de Circuitos Digitais

| Tipo | Comportamento |
|------|--------------|
| **Combinacional** | A saída depende **somente** das entradas atuais — não tem memória |
| **Sequencial** | A saída depende das entradas atuais **e do estado anterior** — tem memória |

---

## 3. Projetando um Sistema Combinacional

O processo tem três etapas:

1. **Encontrar a Função Verdade** que atende à especificação do sistema
2. **Minimizar** a função encontrada (simplificar ao máximo)
3. **Implementar** o circuito com as portas lógicas disponíveis

---

## 4. Função Verdade na Prática — Exemplos

### Exemplo 1: Sistema de Alarme Bancário

**Especificação:** O alarme toca se a chave na delegacia estiver ligada (C) **e** caso a porta do cofre for mexida (S) **ou** se a porta do banco estiver aberta (B) com a chave do guarda desligada (Ē).

**Variáveis:**
- A = alarme toca
- C = chave na delegacia ligada
- S = porta do cofre mexida
- B = porta do banco aberta
- E = chave especial ligada

**Expressão lógica:**
```
A = C . (S + (B . Ē))
```

**Tabela verdade (simplificada):**

| C | S | B | E | A |
|---|---|---|---|---|
| 1 | 0 | 0 | 0 | 0 |
| 1 | 0 | 1 | 0 | 1 |
| 1 | 1 | 0 | 0 | 1 |
| 1 | 1 | 1 | 1 | 1 |
| 0 | X | X | X | 0 |

### Exemplo 2: Controle de Ar Condicionado

**Especificação:** O A/C é acionado se:
1. Peso < 100t, umidade ≥ 60% e temperatura > 20°C
2. Peso ≥ 100t e temperatura > 20°C
3. Peso ≥ 100t e pressão > 30

**Variáveis:** A (A/C), P (peso ≥ 100t), H (umidade ≥ 60%), T (temp > 20°C), B (pressão > 30)

**Expressão lógica:**
```
A = (P̄ . H . T) + (P . T) + (P . B)
```

---

## 5. Conectivos Lógicos (Portas)

| Símbolo | Nome | Operação | Saída = 1 quando... |
|---------|------|----------|---------------------|
| AND | E | A . B | **ambas** entradas são 1 |
| OR | OU | A + B | **pelo menos uma** entrada é 1 |
| NOT | Inversor | Ā | entrada é 0 |
| NAND | NE | ̄(A.B) | NOT AND (não são ambas 1) |
| NOR | NOU | ̄(A+B) | NOT OR (nenhuma é 1) |
| XOR | OU Exclusivo | A ⊕ B | entradas são **diferentes** |
| XNOR | NOR Exclusivo | ̄(A⊕B) | entradas são **iguais** |

### Tabela verdade completa das portas principais

| A | B | AND | OR | XOR | NAND | NOR | XNOR |
|---|---|-----|----|-----|------|-----|------|
| 0 | 0 |  0  |  0 |  0  |  1   |  1  |  1   |
| 0 | 1 |  0  |  1 |  1  |  1   |  0  |  0   |
| 1 | 0 |  0  |  1 |  1  |  1   |  0  |  0   |
| 1 | 1 |  1  |  1 |  0  |  0   |  0  |  1   |

> **Dica:** Para 2 entradas existem 2^(2^2) = **16 funções possíveis** ao total. As acima são as mais usadas na prática.

> **NAND e NOR são universais** — qualquer circuito lógico pode ser construído usando só NANDs ou só NORs.

---

## 6. Sistemas Numéricos

**Representação geral na base b:**
```
N = aₙ₋₁ × bⁿ⁻¹ + aₙ₋₂ × bⁿ⁻² + ... + a₁ × b¹ + a₀ × b⁰
```

### Conversões importantes

| Decimal | Binário | Hexadecimal | BCD |
|---------|---------|-------------|-----|
| 67 | 1000011 | 43 | 0110 0111 |
| 154 | 10011010 | 9A | 0001 0101 0100 |

### BCD (Binary Coded Decimal)
Cada dígito decimal é codificado individualmente em 4 bits binários.
- Ex: (1394)₁₀ = 0001 0011 1001 0100 em BCD
- Dígitos válidos: 0 a 9 → combinações 1010 a 1111 são **inválidas** em BCD

---

## 7. Pontos-chave para a prova

1. **Sistema digital** usa valores discretos (0 e 1), diferente do analógico (contínuo)
2. **Combinacional** = sem memória → saída depende SÓ das entradas atuais
3. **Sequencial** = com memória → saída depende das entradas + estado anterior
4. **Método de projeto**: Especificação em texto → Função Verdade → Minimizar → Implementar
5. **XOR = 1** quando as entradas são **diferentes**; **XNOR = 1** quando são **iguais**
6. **NAND e NOR** são as portas universais
7. Saber ler e montar a **tabela verdade** a partir de uma especificação em linguagem natural

---

## Conexão com as próximas aulas

- **Aula 2**: como extrair sistematicamente a Função Verdade e usar Álgebra de Boole
- **Aulas 3–4**: mintermos, maxtermos e Mapa de Karnaugh para minimização
- **Aulas 7–9**: circuitos sequenciais (flip-flops, contadores, registradores)

---

## Fontes complementares

- [Circuitos Combinacionais — Embarcados](https://embarcados.com.br/circuitos-combinacionais/)
- [Portas Lógicas e Álgebra Booleana — UFF (PDF)](https://www.professores.uff.br/lbertini/wp-content/uploads/sites/108/2017/08/Capitulo-3-Portas-Logicas-e-Algebra-Booleana.pdf)
- [Apostila Sistemas Digitais — IFC (PDF)](https://professor.luzerna.ifc.edu.br/ricardo-kerschbaumer/wp-content/uploads/sites/43/2020/12/Apostila-Sistemas-Digitais-Kerschbaumer-1.pdf)
