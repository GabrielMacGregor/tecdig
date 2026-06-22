# Resumo — Aula 16: Pilha e Fila

## 1. PILHA — Estrutura LIFO (Last In, First Out)

Uma **pilha** é uma estrutura de dados onde o **último elemento inserido é o primeiro a ser retirado** — como uma pilha de pratos: coloca-se em cima, retira-se de cima.

**Ponteiro único:** um único **Stack Pointer (SP)** aponta para a próxima posição livre (o topo da pilha).

### Operação PUSH (inserir)

1. Escreve o dado na posição atual do SP
2. Incrementa o SP (aponta para a próxima posição livre)

```
Estado inicial (pilha vazia): SP → posição 1

PUSH(X): [1]=X → SP avança para posição 2
PUSH(Y): [2]=Y → SP avança para posição 3
PUSH(Z): [3]=Z → SP avança para posição 4
```

### Operação POP (retirar)

1. Decrementa o SP (volta à última posição ocupada)
2. Lê o dado dessa posição

```
POP: SP volta para 3 → lê [3]=Z  (último que entrou, primeiro a sair!)
POP: SP volta para 2 → lê [2]=Y
POP: SP volta para 1 → lê [1]=X  → Pilha Vazia
```

### Exemplo completo

| Operação | Conteúdo da pilha (pos 1,2,3) | SP aponta para |
|----------|-------------------------------|----------------|
| Início (vazia) | [ ][ ][ ] | 1 |
| PUSH(X) | [X][ ][ ] | 2 |
| PUSH(Y) | [X][Y][ ] | 3 |
| PUSH(Z) | [X][Y][Z] | 4 |
| POP → Z | [X][Y][Z] | 3 |
| POP → Y | [X][Y][Z] | 2 |
| POP → X | [X][Y][Z] | 1 (vazia) |

> **Observação:** O dado não é apagado da memória — apenas o SP recua. A próxima escrita sobrescreverá.

---

## 2. FILA — Estrutura FIFO (First In, First Out)

Uma **fila** é uma estrutura onde o **primeiro elemento inserido é o primeiro a ser retirado** — como uma fila de banco: quem chega primeiro, sai primeiro.

**Dois ponteiros independentes:**
- **Ponteiro de Entrada** (write pointer): aponta para onde o próximo dado será escrito
- **Ponteiro de Saída** (read pointer): aponta para onde o próximo dado será lido

Fila vazia: ambos os ponteiros apontam para a mesma posição.

### Operação ENQUEUE (inserir)

1. Escreve o dado na posição do Ponteiro de Entrada
2. Incrementa o Ponteiro de Entrada

```
ENQUEUE(X): [1]=X → P.Entrada avança para 2
ENQUEUE(Y): [2]=Y → P.Entrada avança para 3
ENQUEUE(Z): [3]=Z → P.Entrada avança para 4
```

### Operação DEQUEUE (retirar)

1. Lê o dado na posição do Ponteiro de Saída
2. Incrementa o Ponteiro de Saída

```
DEQUEUE: lê [1]=X → P.Saída avança para 2  (X era o primeiro que entrou!)
DEQUEUE: lê [2]=Y → P.Saída avança para 3
DEQUEUE: lê [3]=Z → P.Saída avança para 4 = P.Entrada → Fila Vazia
```

### Exemplo completo

| Operação | [1] [2] [3] | P.Entrada | P.Saída |
|----------|-------------|-----------|---------|
| Início (vazia) | [ ][ ][ ] | 1 | 1 |
| ENQUEUE(X) | [X][ ][ ] | 2 | 1 |
| ENQUEUE(Y) | [X][Y][ ] | 3 | 1 |
| ENQUEUE(Z) | [X][Y][Z] | 4 | 1 |
| DEQUEUE → X | [X][Y][Z] | 4 | 2 |
| DEQUEUE → Y | [X][Y][Z] | 4 | 3 |
| DEQUEUE → Z | [X][Y][Z] | 4 | 4 (vazia) |

---

## 3. Comparação LIFO vs FIFO

| Aspecto | PILHA (LIFO) | FILA (FIFO) |
|---------|-------------|-------------|
| Regra | Último a entrar, primeiro a sair | Primeiro a entrar, primeiro a sair |
| Ponteiros | 1 (Stack Pointer) | 2 (Entrada e Saída) |
| Inserção | PUSH | ENQUEUE |
| Retirada | POP | DEQUEUE |
| Analogia | Pilha de pratos | Fila de banco |
| Uso em HW | Chamada de subrotinas, interrupções | Buffers de comunicação, pipelines |

---

## 4. Diagrama em Bloco

```
          PUSH / ENQUEUE ──→┐
                 (clock ↑)   │
                             │  PILHA/FILA  ←→  I/O (dados)
          POP / DEQUEUE ──→┘
                 (clock ↑)
```

- As operações são sincronizadas por **borda de subida de clock**
- O barramento **I/O é bidirecional**: fornece dados no PUSH/ENQUEUE, recebe dados no POP/DEQUEUE

---

## 5. Implementação em Hardware

### Componentes

| CI | Função na Pilha/Fila |
|----|----------------------|
| **74LS191** | Contador up/down de 4 bits — implementa o Stack Pointer ou Ponteiro de Entrada/Saída |
| **74LS138** | Decodificador 3:8 — converte o endereço do contador em sinal de seleção de memória (CS̅) |
| Memória RAM | Armazena os dados da pilha/fila |

### 74LS191 — Contador Up/Down

| Pino | Função |
|------|--------|
| **CP** | Clock (dispara na borda de subida) |
| **U/D** | 1 = conta para cima (PUSH/ENQUEUE); 0 = conta para baixo (POP) |
| **PL̅** | Pre-Load: carrega valor inicial (zera o ponteiro) |
| **CE̅** | Chip Enable: habilita a contagem |
| **TC** | Terminal Count: indica overflow (pilha cheia) ou underflow |
| **Q0–Q3** | Saída do contador → endereço atual do ponteiro |

### 74LS138 — Decodificador 3:8

Recebe os 3 bits menos significativos do contador (Q0–Q2) e ativa **uma única saída** de 8 possíveis (Q0–Q7), cada uma correspondendo a uma posição da pilha/fila.

Cada saída Q do 74LS138 é conectada ao **CS̅** de uma posição de memória → apenas a posição endereçada pelo ponteiro é acessada.

### Fluxo do circuito

```
Clock PUSH/ENQUEUE
        ↓
    74LS191 (U/D=1, incrementa)
        ↓
    Q0–Q2 → 74LS138
        ↓
   CS̅ da posição i → ativa a célula de memória i
        ↓
   Dado em I/O é escrito (WE̅=0)

Clock POP/DEQUEUE
        ↓
    74LS191 (U/D=0, decrementa primeiro)
        ↓
    Q0–Q2 → 74LS138
        ↓
   CS̅ da posição i → ativa a célula de memória i
        ↓
   Dado em I/O é lido (WE̅=1, OE̅=0)
```

---

## 6. Condições de Status

| Condição | Como detectar |
|----------|--------------|
| **Pilha Vazia** | SP = valor inicial (ex: 0) — TC do 74LS191 ao contar abaixo de 0 |
| **Pilha Cheia** | SP = tamanho máximo — TC do 74LS191 ao atingir o limite superior |
| **Fila Vazia** | Ponteiro de Entrada = Ponteiro de Saída |
| **Fila Cheia** | Ponteiro de Entrada = Ponteiro de Saída − 1 (mod N) |

---

## 7. Conexão com Outras Aulas

- **Aula 6 (Decodificadores 74LS138):** O mesmo decodificador do banco de memória (Aula 15) aqui seleciona a posição da pilha/fila — a diferença é que o endereço vem de um contador, não de um endereço fixo
- **Aula 8 (Contadores 74LS191):** O contador up/down é a espinha dorsal do ponteiro — conta para cima no PUSH, para baixo no POP
- **Aula 15 (Memória):** A pilha/fila é uma memória RAM com acesso controlado por ponteiro — CS̅, WE̅, OE̅ operam exatamente como na Aula 15
- **Aula 9 (Shift Register):** O shift register é uma implementação hardware de uma fila de 1 bit — cada clock desloca o dado uma posição

---

## Resumo Visual

```
PILHA (LIFO)               FILA (FIFO)
┌───────────┐              ┌───────────┐
│           │ ← SP         │           │ ← P.Saída (leitura)
│    [Z]    │              │    [X]    │
│    [Y]    │              │    [Y]    │
│    [X]    │              │    [Z]    │
│           │              │           │ ← P.Entrada (escrita)
└───────────┘              └───────────┘

1 ponteiro (E/S)           2 ponteiros separados
SP sobe no PUSH            P.Entrada sobe no ENQUEUE
SP desce no POP            P.Saída sobe no DEQUEUE
```
