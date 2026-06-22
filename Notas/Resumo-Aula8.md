# Resumo — Aula 8: Contadores Assíncronos (7493 e 7490)

## 1. Contador Assíncrono (Ripple Counter)

Um **contador assíncrono** é aquele em que os FF's não compartilham o mesmo clock — cada FF recebe o clock da **saída do FF anterior** (em cascata).

**Também chamado de:** Ripple Counter (contador em cascata/ondulação).

**Vantagem:** implementação simples — poucos componentes.  
**Desvantagem:** pode gerar **spikes** (pulsos espúrios) ao decodificar as saídas, porque os bits não mudam todos ao mesmo tempo.

> Comparação: no contador **síncrono** todos os FFs são disparados pelo mesmo CLK (sem glitches, mais rápido, porém mais complexo).

---

## 2. CI 7493 — Contador Binário de 4 bits (divisor por 16)

### Estrutura interna

O 7493 é composto de **dois contadores internos independentes**:
- **FF A** (CKA → QA): divisor por 2
- **FFs B, C, D** (CKB → QB, QC, QD): divisor por 8

Para obter **contador/divisor por 16**: conectar **QA → CKB** externamente.

### Sequência de contagem (modo ÷16)

| Count | QD | QC | QB | QA |
|-------|----|----|----|----|
| 0     | L  | L  | L  | L  |
| 1     | L  | L  | L  | H  |
| 2     | L  | L  | H  | L  |
| ...   | ...| ...| ...| ...|
| 15    | H  | H  | H  | H  |

(L = 0, H = 1)

### Reset (Clear)

- Pinos **R0(1) e R0(2)** — quando ambos em H → saídas vão para **0000** (reset assíncrono)
- Se qualquer um estiver em L → contador opera normalmente (conta)

### Como fazer um divisor por N qualquer (com 7493)

1. Determine N em binário: ex. N=13 → 1101
2. Identifique quais bits de saída são "1" em N: QD=1, QC=1, QB=0, QA=1
3. Conecte essas saídas a uma porta NAND
4. Ligue a saída da NAND ao R0(1) e R0(2)
5. O contador vai de 0 até N−1, ao atingir N momentaneamente o NAND reseta tudo para 0

**Exemplo — Divisor por 13:**
- 13 = 1101 → usar QD, QC, QA (são os bits 1)
- NAND(QD, QC, QA) → R0

**Exemplo — Divisor por 12 (para gerar 1kHz a partir de 12kHz):**
- 12 = 1100 → usar QD e QC
- NAND(QD, QC) → R0

### Divisor vs Contador

| | Divisor | Contador |
|---|---------|---------|
| **O que faz** | Gera uma saída com frequência f/N | Apresenta nas saídas o número de pulsos recebidos |
| **Saída de interesse** | Apenas QD (ou outra saída específica) | Todas as saídas QA–QD (valor binário da contagem) |
| **Uso típico** | Geração de clock | Contagem de eventos, timer |

---

## 3. CI 7490 — Contador Decimal de 4 bits (divisor por 10)

### Estrutura interna

O 7490 também é composto de dois divisores:
- **FF A** (CKA → QA): divisor por 2
- **FFs B, C, D** (CKB → QB, QC, QD): divisor por 5

### Dois modos de operação

#### Modo BCD (Note A) — divisor por 10 para contar 0 a 9
- Conectar **QA → CKB**
- Sequência: 0000 → 0001 → ... → 1001 (0 a 9), depois retorna a 0000
- Cada grupo de 4 bits representa um dígito decimal em BCD

#### Modo Bi-Quinário (Note B) — divisor por 10 em formato 5-2
- Conectar **QD → CKA**
- Formato diferente de codificação (menos usado)

### Reset do 7490

| R0(1) | R0(2) | R9(1) | R9(2) | Resultado |
|-------|-------|-------|-------|-----------|
| H | H | L | X | Reset para **0000** |
| H | H | X | L | Reset para **0000** |
| X | X | H | H | Preset para **1001** (= 9) |
| qualquer L | qualquer L | — | — | CONTA normalmente |

> O **Preset para 9** (R9) é usado quando se quer iniciar a contagem do 9 para baixo.

---

## 4. Projetos com 7493 e 7490

### Problema: Contador de 0 a 45 com reset automático no 46º pulso

**Análise:** 46 estados → precisamos de 2 CIs de 4 bits em cascata (máximo = 99 com dois 7490 em BCD, ou 255 com dois 7493).

**Com 7493:**
- 46 = 00101110 (8 bits) → usar dois 7493 em cascata: primeiro para bits 0–3, segundo para bits 4–7
- Ligar QD do primeiro ao CKB do segundo (ou CKA do segundo)
- Detectar 46 = 0010 1110: segundo CI tem QB=1 (bit 5), primeiro tem QD=1,QC=1,QB=1 (bits 3,2,1)
- NAND dessas saídas → R0 de ambos os CIs

**Com 7490 (modo BCD):**
- Usar dois 7490 em cascata: primeiro conta unidades (0–9), segundo conta dezenas
- QD do primeiro → CKA do segundo (quando unidades chegam a 9 e reiniciam, gera pulso para dezenas)
- Detectar 46: dezenas = 4 (0100) → QC=1; unidades = 6 (0110) → QC=1, QB=1
- NAND(QC_dezenas, QC_unidades, QB_unidades) → R0 de ambos

### Bloqueio no 45º pulso (só sai com reset externo)

Em vez de conectar o sinal de detecção ao **R0** (que reseta automaticamente), conectá-lo ao **controle do clock** (inibir o clock via AND gate com um sinal externo de habilitação). O contador para em 45 e só reinicia quando o sinal de reset externo for ativado manualmente.

---

## 5. Cascateamento de 7490 para contagem multi-dígito

Para criar um contador de 0 a 99 (dois dígitos BCD):
```
CLK → CKA₁ → QA₁ → CKB₁ (QD₁ = carry das unidades)
QD₁ → CKA₂ → QA₂ → CKB₂
```
- Saídas QD–QA do CI 1 = dígito das unidades (BCD)
- Saídas QD–QA do CI 2 = dígito das dezenas (BCD)

Para N dígitos: N CIs 7490 em cascata, cada um controlado pelo carry do anterior.

---

## 6. Pontos-chave para a prova

1. **Assíncrono** = FFs em cascata (cada CLK vem da saída anterior) — simples mas gera glitches
2. **7493** = divisor por 16 (÷2 interno + ÷8); para ÷16 ligar **QA→CKB**
3. **7490** = divisor por 10 (÷2 + ÷5); para BCD ligar **QA→CKB**
4. **Reset 7493:** R0(1) E R0(2) = H → 0000
5. **Reset 7490:** R0(1) E R0(2) = H → 0000; R9(1) E R9(2) = H → 1001
6. **Divisor por N arbitrário:** detectar N em binário com NAND nas saídas = 1, ligar ao pino de reset
7. Para contar de 0 até M (M+1 estados): detectar M+1 e resetar

---

## Conexão com as próximas aulas

- **Aula 9**: Registradores de deslocamento (shift registers) — outro uso de FFs em cascata
- **Aulas 10–11**: Projeto de circuitos sequenciais genéricos — onde a lógica de próximo estado é projetada sistematicamente
