# Resumo — Aula 10: Projeto de Circuitos Sequenciais (Parte 1)

## 1. Procedimento de Projeto

O projeto de um circuito sequencial síncrono segue um fluxo bem definido:

```
Descrição Funcional
       ↓
Diagrama de Estados  ←→  Tabela de Estados
       ↓
  Tabela Mínima
       ↓
 Tabela de Transição
       ↓
  Equações dos FF's
       ↓
    Circuito
```

Cada etapa alimenta a próxima. A minimização (Tabela Mínima) é opcional mas reduz o número de flip-flops necessários.

---

## 2. Modelos de Máquinas de Estado

### Modelo de Mealy
- A **saída depende do estado atual E da entrada** (x/z nas setas do diagrama)
- Geralmente resulta em **menos estados** do que Moore
- Notação nas transições: `entrada/saída`

### Modelo de Moore
- A **saída depende apenas do estado** (z está dentro do círculo do estado: `qN/z`)
- Pode precisar de **mais estados** que Mealy
- Notação nos estados: `qN/saída`, e nas setas apenas a entrada

> **Regra prática:** Mealy = saída nas setas | Moore = saída nos estados

---

## 3. Exemplo 1 — Detector de Três "1"s Consecutivos (Mealy)

**Problema:** Detectar três bits `1` consecutivos no início de uma mensagem serial. Reset ao fim de cada mensagem.

### Diagrama de Estados (Mealy) — raciocínio:
- `q0` → estado inicial (nenhum "1" visto)
- `q1` → viu 1 "1"
- `q2` → viu 2 "1"s
- `q3` → viu 3 "1"s → saída Z=1 enquanto continua recebendo "1"
- `q4` → estado de "erro" (recebeu "0") — depois percebe-se que `q4 = q0`

### Tabela de Estados (Mealy) — formato: `(próximo estado, saída)`

| q  | x=0    | x=1    |
|----|--------|--------|
| q0 | q4, 0  | q1, 0  |
| q1 | q4, 0  | q2, 0  |
| q2 | q4, 0  | q3, 1  |
| q3 | q3, 0  | q3, 0  |
| q4 | q4, 0  | q1, 0  |

> **Observação:** `q4` tem exatamente o mesmo comportamento que `q0` → podem ser fundidos!

### Tabela Mínima (4 estados):

| q  | x=0    | x=1    |
|----|--------|--------|
| q0 | q0, 0  | q1, 0  |
| q1 | q0, 0  | q2, 0  |
| q2 | q0, 0  | q3, 1  |
| q3 | q3, 0  | q3, 0  |

---

## 4. Exemplo 1 — Mesmo Detector usando Modelo de Moore

No modelo de Moore, a saída Z fica associada ao **estado**, não à transição. Por isso é necessário um estado extra para representar "acabou de detectar os 3 '1's" com Z=1.

### Tabela de Estados (Moore):

| q  | x=0 | x=1 | Z |
|----|-----|-----|---|
| q0 | q0  | q1  | 0 |
| q1 | q0  | q2  | 0 |
| q2 | q0  | q3  | 0 |
| q3 | q4  | q4  | 1 |
| q4 | q4  | q4  | 0 |

- `q3` é o estado de saída Z=1 (os 3 "1"s foram detectados)
- `q4` é o estado pós-detecção (Z volta a 0)
- Moore requer **5 estados** vs **4 estados** em Mealy para o mesmo problema

---

## 5. Exemplo 2 — Detector de Sequência 0101

**Problema:** Gerar saída Z=1 sempre que a sequência `0101` ocorrer.

Existem dois modos de detecção:

| Modo | Significado | Exemplo Z para X=001010101 |
|------|-------------|----------------------------|
| **Sem interseção** | Após detectar 0101, reinicia do zero | Z = 000010001 |
| **Com interseção** | Após detectar 0101, os últimos bits podem iniciar nova detecção | Z = 000010101 |

### Diagrama de Estados (Mealy, sem interseção):

```
q0 --0/0--> q1 --1/0--> q2 --0/0--> q3
 ^           |                        |
 |           | 0/0                   1/1 (detectou! → volta q0)
 |           v                        |
 +---------- q0 <--------------------+
(1/0 volta para q0)
```

Transições resumidas:
- `q0`: recebeu 0 → q1 / recebeu 1 → q0 (1 não começa a sequência 0101)
- `q1`: recebeu 1 → q2 / recebeu 0 → q1 (novo 0 pode ser início)
- `q2`: recebeu 0 → q3 / recebeu 1 → q0
- `q3`: recebeu 1 → q0 com Z=1 / recebeu 0 → q1

---

## 6. Exemplo 3 — Fechadura Eletrônica (exercício proposto)

**Especificação:**
- Código de 3 dígitos (teclado de 10 teclas, código binário 0000–1001)
- 2 tentativas para acertar
- Código correto → Z=1 (abre a porta)
- 2º erro → trava (só reset desbloqueia)

Este é um exercício para o aluno desenvolver o diagrama de estados com base nas regras da máquina.

---

## 7. Comparação Mealy vs Moore — Resumo

| Característica | Mealy | Moore |
|---------------|-------|-------|
| Saída depende de | Estado + Entrada | Só do Estado |
| Número de estados | Geralmente menor | Geralmente maior |
| Onde fica Z no diagrama | Nas setas (entrada/saída) | Nos círculos (estado/saída) |
| Reatividade | Imediata à entrada | Com um ciclo de atraso |

---

## Conexão com Aulas Anteriores

- **Aula 7 (Flip-Flops):** Os estados são armazenados em FF's — o número de estados determina quantos FF's são necessários (n estados → ceil(log2(n)) FF's)
- **Aula 12:** A minimização da tabela de estados (que aparece brevemente aqui com q4=q0) será estudada em profundidade na Aula 12
