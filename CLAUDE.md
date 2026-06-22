# Projeto de Estudos — Técnicas Digitais (20261)

## Sobre este projeto

Este projeto contém o material completo da disciplina **Técnicas Digitais** (código 20261).
Use este arquivo para orientar o agente Claude nos seus estudos.

## Estrutura de pastas

```
TECDIG/
├── Aulas/          → PDFs das 16 aulas teóricas
├── Laboratorios/   → PDFs dos 6 laboratórios + datasheets
└── Notas/          → Resumos, plano de estudos e flashcards gerados
```

## Conteúdo do curso

| Aula | Tema                                                      |
|------|-----------------------------------------------------------|
| 1    | Sistemas Digitais, binário, combinacional vs sequencial   |
| 2    | Funções Verdade e projeto de sistemas combinacionais      |
| 3    | Mintermos, Maxtermos, SDP e PDS                          |
| 4    | Don't Care, Mapa de Karnaugh, Somadores BCD               |
| 5    | Multiplexadores (MUX) e Decodificadores                   |
| 6    | Demultiplexadores e Codificadores                         |
| 7    | Flip-Flops RS, D e JK                                     |
| 8    | Contadores Assíncronos (7493, 7490)                       |
| 9    | Registradores de Deslocamento (Shift Registers)           |
| 10   | Projeto de Circuitos Sequenciais (parte 1)                |
| 11   | Projeto de Circuitos Sequenciais (parte 2)                |
| 12   | Minimização de Tabela de Estados                          |
| 13   | Máquina Incompletamente Especificada + Minimização        |
| 14   | Monoestável e Teclado                                     |
| 15   | Tri-State e Memória                                       |
| 16   | Pilha e Fila                                              |

## Como usar o agente

Você pode pedir ao Claude para:
- "Explique o conceito da Aula X" → Claude lê o PDF e explica
- "Me faça um quiz sobre Flip-Flops" → Claude usa os flashcards e cria perguntas
- "Resuma a Aula 7" → Claude gera um resumo detalhado
- "Ajuda para o Lab 3" → Claude lê o enunciado do laboratório e orienta
- "Crie notas da Aula 5 em Notas/" → Claude salva uma nota .md

## Instruções para o agente

Quando o usuário pedir ajuda para estudar:
1. Leia o PDF relevante em `Aulas/` ou `Laboratorios/`
2. Explique de forma clara e didática, com exemplos práticos
3. Ao criar quiz/flashcards, salve em `Notas/flashcards.md`
4. Ao criar resumos, salve em `Notas/Resumo-AulaX.md`
5. Sempre relacione o novo conteúdo com o que já foi visto antes
