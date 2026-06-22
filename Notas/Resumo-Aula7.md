# Resumo — Aula 7: Flip-Flops RS, D e JK

## 1. O que é um Flip-Flop?

Um **Flip-Flop (FF)** é um circuito biestável — tem **memória**: sua saída depende das entradas atuais **e do estado anterior**. É o elemento fundamental dos **circuitos sequenciais**.

Diferença chave em relação aos circuitos combinacionais da Aula 2–6:
- Combinacional: saída = f(entradas atuais)
- **Sequencial: saída = f(entradas atuais + estado atual)**

Notação: Qt = estado atual da saída; Qt+1 = próximo estado após o disparo.

---

## 2. Flip-Flop RS (Set-Reset)

### Implementação com portas NOR (entradas ativas em nível 1)

Duas portas NOR em realimentação cruzada (cross-coupled):

```
Tabela do FF RS (NOR):

R  | S  | Q^(t+1)
---|----|---------
0  | 0  | Qt     (memória — mantém)
0  | 1  | 1      (Set — Q vai para 1)
1  | 0  | 0      (Reset — Q vai para 0)
1  | 1  | X      (PROIBIDO)
```

**Por que R=S=1 é proibido?** Porque forçaria Q = Q̄ = 0 simultaneamente, o que é uma contradição. Ao voltar para R=S=0 o estado final é imprevisível.

### Implementação com portas NAND (entradas ativas em nível 0)

Com NANDs, os pinos ficam com lógica invertida (S̄ e R̄ nas entradas):

```
Tabela do FF RS (NAND — entradas ativas em 0):

R  | S  | Q^(t+1)
---|----|---------
1  | 0  | 0      (Set — ativo em 0 no S)
1  | 1  | Qt     (memória)
0  | 1  | 1      (Reset — ativo em 0 no R)
0  | 0  | X      (PROIBIDO — ambos em 0)
```

### FF RS com Clock (Enable)

Adicionando portas AND antes do latch, as entradas só são amostradas quando o **Clock (Ck) / Enable** estiver ativo:
- Ck ativo em 1 → FF responde a R e S
- Ck em 0 → saída **congela** no estado atual

Símbolos:
- `>` ou triângulo no pino CLK → disparado por transição (edge-triggered)
- Bolinha no CLK → ativo em nível baixo

### Aplicação prática: Debounce de chave

Chaves mecânicas geram múltiplos pulsos (bouncing) ao comutar. Um FF RS com duas resistências de pull-up elimina esse efeito: o FF "trava" no primeiro nível estável e ignora os bounces seguintes.

---

## 3. Flip-Flop tipo D (Delay / Data)

O **FF D** é o FF mais simples para armazenamento: copia o estado da entrada D para a saída Q quando o clock dispara.

```
Tabela do FF D:

D  | Q^(t+1)
---|--------
0  | 0
1  | 1
```

### Expressão: `Q^(t+1) = D`

O FF D é construído internamente ligando S = D e R = D̄ em um FF RS com clock.

### Tipos de disparo (Trigger)

| Tipo | Funcionamento |
|------|--------------|
| Nível 0 | Amostra entrada enquanto CLK = 0 |
| Nível 1 | Amostra entrada enquanto CLK = 1 |
| Transição positiva (↑) | Amostra na borda de subida do CLK |
| Transição negativa (↓) | Amostra na borda de descida do CLK |

> **Símbolo:** triângulo no CLK = transição positiva; triângulo + bolinha = transição negativa.

### Entradas assíncronas: Clear (CLR) e Preset (PR)

- **CLR (Clear):** força Q = 0 imediatamente, independente do clock
- **PR (Preset):** força Q = 1 imediatamente, independente do clock
- São usadas para colocar o FF num estado inicial determinado na inicialização do sistema

---

## 4. Flip-Flop tipo JK

O **FF JK** é o FF mais completo e versátil. Resolve o estado proibido do RS:
- J equivale ao S (Set)
- K equivale ao R (Reset)
- J=K=1 é **permitido** e causa **Toggle** (inversão da saída)

```
Tabela do FF JK:

J  | K  | Q^(t+1)
---|----|---------
0  | 0  | Qt      (memória)
0  | 1  | 0       (Reset)
1  | 0  | 1       (Set)
1  | 1  | Q̄t     (Toggle — inverte)
```

### Como funciona o Toggle

Quando J=K=1, cada pulso de clock inverte Q. Isso faz o FF JK funcionar como **divisor de frequência por 2** — muito útil em contadores.

### FF JK Master-Slave

Dois FFs RS em cascata, com clocks complementares (mestre ativo na borda positiva, escravo na borda negativa):
1. **Mestre** amostra J e K durante CLK=1 e armazena internamente
2. **Escravo** copia o estado do mestre apenas quando CLK cai para 0

Isso elimina o problema de "race condition" (onde a saída retroalimentada poderia re-disparar o mestre no mesmo pulso).

### Contador binário com 4 FFs JK em cascata

Quando todos J=K=1 (fixos em 1) e o CLK de cada FF é conectado à saída Q̄ do anterior:
- Q0 inverte a cada borda de descida do CLK externo → período = 2×CLK
- Q1 inverte a cada 2 pulsos → período = 4×CLK
- Q2 → período = 8×CLK
- Q3 → período = 16×CLK

Resultado: Q3 Q2 Q1 Q0 conta de 0000 a 1111 (0 a 15) em binário, voltando a 0000 — este é o **contador assíncrono de 4 bits** (tema da Aula 8).

---

## 5. Flip-Flop tipo T (Toggle / Trigger)

O **FF T** é derivado do JK com J=K=T:

```
Tabela do FF T:

T  | Q^(t+1)
---|--------
0  | Qt     (mantém)
1  | Q̄t    (inverte)
```

Implementação: FF JK com J e K ligados juntos à entrada T.

---

## 6. Comparativo dos 4 tipos de FF

| FF | Entradas | Função especial | Estado problemático |
|----|----------|-----------------|---------------------|
| **RS** | R, S | Set/Reset | R=S=1 proibido |
| **D** | D | Armazena dado | Nenhum |
| **JK** | J, K | Toggle (J=K=1) | Nenhum |
| **T** | T | Divide frequência | Nenhum |

---

## 7. Pontos-chave para a prova

1. FF RS (NOR): **R=S=0 → memória**, S=1 → Set, R=1 → Reset, **R=S=1 → PROIBIDO**
2. FF RS com NAND: lógica invertida — as entradas são ativas em **nível 0**
3. FF D: Q^(t+1) = D — simplesmente copia a entrada
4. Saber identificar o tipo de disparo pelo símbolo: triângulo = transição, bolinha = negativo
5. FF JK: igual ao RS mas **J=K=1 → Toggle** (inverte Q) — estado proibido eliminado
6. FF T = FF JK com J=K=T → divide frequência por 2 quando T=1
7. **CLR e PR são assíncronos** — atuam imediatamente, sem esperar o clock
8. Cascata de JK com J=K=1 → **contador binário assíncrono**

---

## Conexão com as próximas aulas

- **Aula 8**: Contadores assíncronos (7493 e 7490) — construídos com FF JK em cascata
- **Aula 9**: Registradores de deslocamento (shift registers) — construídos com FF D em cascata
- **Aulas 10–13**: Projeto sistemático de circuitos sequenciais usando tabelas de estado e flip-flops
