# Guia de Estudo — Arquitetura de Computadores
### Baseado em Harris & Harris, *Projeto Digital e Arquitetura de Computadores* (Cap. 6–7 — arquitetura MIPS)

---

## 1. Operandos em instruções assembly
*(Livro: Cap. 6.2 — Linguagem Assembly)*

Uma instrução assembly opera sobre **operandos**, que podem vir de três lugares:

- **Registradores:** pequenas memórias muito rápidas dentro do processador. No MIPS existem 32 registradores de 32 bits cada (ex.: `$s0`–`$s7` para variáveis, `$t0`–`$t9` temporários, `$a0`–`$a3` argumentos de função, `$v0`–`$v1` valores de retorno, `$ra` endereço de retorno, `$sp` ponteiro de pilha, `$zero` sempre vale 0). A maioria das instruções aritméticas/lógicas só opera com registradores — é a filosofia **load/store** do MIPS (RISC): só `lw`/`sw` acessam a memória.
- **Memória:** dados maiores (arrays, structs) ficam na memória principal. Para usá-los em uma operação, é preciso primeiro **carregar** (`lw` — load word) para um registrador, operar, e depois **armazenar** (`sw` — store word) de volta.
- **Constantes/imediatos:** valores literais embutidos na própria instrução (ex.: `addi $t0, $t1, 5` soma 5 diretamente, sem precisar de outro registrador).

**Ponto-chave de prova:** entender por que uma arquitetura RISC como MIPS restringe o acesso à memória apenas a `lw`/`sw`, deixando toda a aritmética restrita a registradores — isso simplifica o hardware e permite pipeline eficiente (ligação direta com o tópico 5).

---

## 2. Formatos de instruções assembly
*(Livro: Cap. 6.3 — Linguagem de Máquina)*

Cada instrução assembly é traduzida para uma palavra binária de 32 bits (**linguagem de máquina**), seguindo um formato fixo. O MIPS usa três formatos:

| Formato | Uso típico | Campos |
|---|---|---|
| **Tipo-R** (Register) | Instruções aritmético-lógicas reg-reg (ex.: `add`, `sub`, `and`, `or`, `slt`) | `opcode`(6) \| `rs`(5) \| `rt`(5) \| `rd`(5) \| `shamt`(5) \| `funct`(6) |
| **Tipo-I** (Immediate) | Instruções com constante/imediato ou acesso à memória (ex.: `addi`, `lw`, `sw`, `beq`) | `opcode`(6) \| `rs`(5) \| `rt`(5) \| `imediato`(16) |
| **Tipo-J** (Jump) | Desvios incondicionais de longo alcance (ex.: `j`, `jal`) | `opcode`(6) \| `endereço`(26) |

**Detalhes importantes de prova:**
- No **tipo-R**, o `opcode` é sempre 0 (000000); é o campo **funct** que diferencia qual operação (add, sub, and...) — por isso é chamado de formato "genérico" das operações internas da ULA.
- No **tipo-I**, o campo imediato de 16 bits é normalmente **estendido com sinal** (sign-extend) para 32 bits antes de ser usado.
- O formato usado depende diretamente do **tipo de instrução** (ligação com o tópico 3) — decorar qual instrução usa qual formato é frequentemente cobrado.

---

## 3. Tipos de instruções: aritméticas/lógicas, condicionais, de desvios, chamadas de funções
*(Livro: Cap. 6.4 — Programando)*

- **Aritméticas/lógicas:** `add`, `sub`, `and`, `or`, `slt` (set-less-than), `sll`/`srl` (deslocamento), suas versões imediatas (`addi`, `andi`, `ori`) — operam sobre registradores/imediatos, formato R ou I.
- **Acesso à memória:** `lw` (load word), `sw` (store word) — tecnicamente tipo-I, servem de "ponte" entre registradores e memória (ligação com tópico 1).
- **Condicionais (desvio condicional):** `beq` (branch if equal), `bne` (branch if not equal) — comparam dois registradores e, se a condição for satisfeita, **desviam** o fluxo somando um deslocamento (offset) ao PC (Program Counter); usadas para implementar `if`/`while`/`for`.
- **Desvios incondicionais:** `j` (jump) — pula sempre para um endereço fixo (tipo-J); `jr` (jump register) — pula para o endereço contido em um registrador (usado no retorno de função, `jr $ra`).
- **Chamadas de função:** `jal` (jump and link) — salta para o endereço da função **e** salva o endereço de retorno em `$ra` automaticamente. O retorno é feito com `jr $ra`. A convenção de chamada também envolve o uso da **pilha (stack)** via `$sp`, para salvar registradores e passar parâmetros que não cabem em `$a0`–`$a3`.

**Ponto de prova comum:** montar a sequência de instruções assembly equivalente a um trecho de código em C/pseudocódigo com `if`, laços e chamadas de função — e saber calcular o deslocamento (offset) de um `beq`/`j`.

---

## 4. Modos de endereçamento
*(Livro: Cap. 6.5)*

Definem **como o operando é localizado** a partir da instrução:

- **Imediato:** o próprio valor está embutido na instrução (`addi $t0, $t1, 5` → o 5 é o operando direto).
- **Registrador (direto):** o operando é o conteúdo de um registrador (`add $t0, $t1, $t2`).
- **Base + deslocamento (base+offset):** usado em `lw`/`sw` — o endereço de memória é calculado somando um registrador-base a uma constante (`lw $t0, 4($s0)` → endereço = `$s0 + 4`). É o modo mais usado para acessar arrays e campos de structs.
- **PC-relativo:** usado nos desvios condicionais (`beq`/`bne`) — o novo endereço é calculado somando um deslocamento ao **PC** (na prática, ao PC+4). Permite código **relocável** (independe do endereço absoluto onde o programa foi carregado).
- **Pseudo-direto:** usado no `j`/`jal` — os 26 bits de endereço da instrução são combinados com os bits mais altos do PC atual para formar o endereço de destino completo (não é totalmente relativo, mas também não é um endereço absoluto completo de 32 bits).

**Ponto-chave de prova:** associar cada instrução ao seu modo de endereçamento correspondente, e entender **por que** cada modo existe (ex.: PC-relativo → código relocável; base+offset → acesso eficiente a vetores).

---

## 5. Implementação da microarquitetura: controle e datapath
*(Livro: Cap. 7.3–7.5 — Ciclo Único, Multiciclo, Pipeline)*

A **microarquitetura** é a implementação em hardware (circuito) que executa o conjunto de instruções (ISA) definido nos tópicos 1–4. Ela é dividida em dois blocos que trabalham juntos:

- **Datapath (caminho de dados):** todos os elementos que armazenam/movem/processam dados — banco de registradores, ULA, memórias de instrução/dados, somadores de PC, multiplexadores, unidade de extensão de sinal, etc. É por onde os **dados** fluem.
- **Unidade de controle:** lógica combinacional (às vezes uma FSM — ligação direta com o assunto de Máquinas de Estado!) que, a partir do `opcode`/`funct` da instrução, gera os **sinais de controle** que comandam o datapath: quais multiplexadores selecionam qual entrada, se a memória será lida/escrita, se um registrador será escrito, qual operação a ULA realiza, etc.

O livro apresenta **três estilos de microarquitetura** para o mesmo conjunto de instruções MIPS, em ordem crescente de desempenho e complexidade:

1. **Ciclo único (single-cycle):** cada instrução é executada por completo em **um único ciclo de clock**. Simples de entender e projetar (controle é lógica puramente combinacional), mas o ciclo de clock precisa ser tão longo quanto a instrução mais lenta (geralmente `lw`), desperdiçando tempo nas instruções mais rápidas.
2. **Multiciclo (multicycle):** cada instrução é dividida em **vários passos menores** (busca, decodificação, execução, acesso à memória, escrita), cada um levando um ciclo de clock — instruções diferentes usam números diferentes de ciclos. Reaproveita um único somador/ULA em vários passos (menos hardware duplicado), mas exige uma **unidade de controle mais complexa**, implementada como uma **FSM** (Moore) que sequencia os passos — aqui há uma conexão direta com o assunto de Máquinas de Estados Finitos que você já estudou.
3. **Pipeline:** divide a execução em **estágios sobrepostos** (tipicamente 5: Busca, Decodificação, Execução, Memória, Escrita — Fetch, Decode, Execute, Memory, Writeback), permitindo que múltiplas instruções estejam em processamento simultaneamente, uma em cada estágio, aumentando bastante a taxa de instruções executadas por segundo (throughput). Introduz complicações que costumam ser cobradas em prova:
 - **Hazards (riscos) estruturais:** dois estágios precisando do mesmo recurso de hardware ao mesmo tempo.
 - **Hazards de dados:** uma instrução precisa de um resultado que a instrução anterior ainda não terminou de calcular/escrever — resolvido com **forwarding (encaminhamento)** ou, quando não é possível, com **stall (bolha)**.
 - **Hazards de controle:** desvios (`beq`, `j`) só são resolvidos em um estágio posterior, mas o processador já pode ter buscado instruções erradas em sequência — resolvido com técnicas de **predição de desvio** ou flush do pipeline.

**Ponto-chave de prova:** saber comparar os três estilos em termos de **CPI (ciclos por instrução)**, período de clock e desempenho total, além de identificar/resolver hazards em um pipeline de 5 estágios dado um trecho de código.

---

## Como usar este guia
Segue a mesma lógica do guia anterior (Matemática e Fundamentos da Computação): um bloco por tópico, na ordem da sua lista. Posso montar também uma lista de exercícios em PDF para esses 5 tópicos, no mesmo estilo (complexidade moderada + gabarito) — é só pedir.
