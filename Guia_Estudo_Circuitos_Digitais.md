# Guia de Estudo — Circuitos Digitais
### Baseado em Harris & Harris, *Projeto Digital e Arquitetura de Computadores* (Cap. 1, 2, 3 e 5)

---

## 1. Lógica CMOS combinacional: transistores MOS p e n como chaves
*(Livro: Cap. 1.7 — Transistores CMOS)*

CMOS (Complementary MOS) constrói portas lógicas usando dois tipos de transistor como **chaves controladas por tensão**:

- **nMOS (transistor tipo n):** conduz (liga) quando o *gate* está em **1** (tensão alta); é um bom condutor de **0** (passa 0 "forte").
- **pMOS (transistor tipo p):** conduz quando o *gate* está em **0**; é um bom condutor de **1** (passa 1 "forte").

**Regra de montagem CMOS:** toda porta lógica tem uma **rede pMOS** ligada entre a saída e $V_{DD}$ (alimentação) e uma **rede nMOS** ligada entre a saída e GND. As duas redes são **complementares**: nunca conduzem ao mesmo tempo (evita curto-circuito) e sempre uma delas conduz (evita saída flutuante).

- Um **inversor CMOS** é o caso mais simples: 1 pMOS + 1 nMOS.
- Entradas em **série** na rede nMOS correspondem a uma função **AND**; entradas em **paralelo** correspondem a **OR**. Na rede pMOS a lógica é invertida (série↔paralelo trocados), pela dualidade — por isso portas CMOS naturalmente implementam **NAND/NOR** (com inversão), sendo mais "baratas" que AND/OR puras (que exigem um inversor extra).

---

## 2. Comportamentos elétricos: estático, dinâmico e de consumo de potência
*(Livro: Cap. 1.6 e 1.8)*

- **Comportamento estático (disciplina estática):** define as faixas de tensão válidas para representar 0 e 1 lógicos ($V_{OL}$, $V_{OH}$, $V_{IL}$, $V_{IH}$) e a **margem de ruído (noise margin)** — a folga entre o que uma porta garante emitir e o que a próxima porta garante reconhecer. Garantir essa disciplina é o que permite abstrair um circuito elétrico analógico como um circuito puramente lógico (0/1).
- **Comportamento dinâmico:** descreve como a tensão de saída **varia no tempo** durante uma transição (0→1 ou 1→0), já que nenhuma porta troca de estado instantaneamente — envolve os tempos de subida/descida e o atraso de propagação (ligação com o tópico 3).
- **Consumo de potência:** em CMOS há dois componentes principais:
 - **Potência dinâmica:** consumida ao **carregar/descarregar capacitâncias** a cada transição de sinal — proporcional a $C \cdot V^2 \cdot f$ (capacitância, tensão ao quadrado, frequência de chaveamento). É o termo dominante em circuitos que chaveiam bastante.
 - **Potência estática:** consumida mesmo sem chaveamento, devido a correntes de fuga (leakage) pelos transistores "desligados" — idealmente próxima de zero em CMOS ideal, mas relevante em tecnologias modernas com transistores muito pequenos.

---

## 3. Propagação de atrasos em blocos lógicos e caminho crítico
*(Livro: Cap. 2.9 — Temporização)*

Toda porta lógica introduz um **atraso de propagação** ($t_{pd}$) entre a mudança da entrada e a estabilização da saída, e um **atraso de contaminação** ($t_{cd}$, o tempo mínimo até a saída começar a mudar).

- Em um circuito com várias portas em cascata, o atraso total de um caminho é a **soma dos atrasos de cada porta** ao longo desse caminho.
- **Caminho crítico:** o caminho **de maior atraso total** entre uma entrada e uma saída do circuito — é ele que determina a velocidade máxima (frequência máxima de operação) do circuito inteiro, pois todos os sinais precisam estar estáveis antes de serem usados.
- Reduzir o atraso do caminho crítico (redesenhando a lógica, reduzindo o número de níveis de porta, etc.) é um dos principais objetivos de otimização em projeto digital — ligação direta com o assunto de minimização lógica (Mapas de Karnaugh, tópicos já estudados).

---

## 4. Glitches, riscos temporais (hazards) estáticos e dinâmicos
*(Livro: Cap. 2.9)*

**Glitch (risco/hazard):** uma mudança **momentânea e indesejada** na saída de um circuito combinacional, causada por caminhos com atrasos diferentes chegando em momentos diferentes a uma mesma porta, mesmo quando o valor final da saída não deveria mudar.

- **Hazard estático:** a saída deveria **permanecer constante**, mas oscila momentaneamente antes de se estabilizar (ex.: deveria continuar em 1, mas passa rapidamente por 0 antes de voltar a 1).
- **Hazard dinâmico:** a saída deveria mudar **uma única vez** (ex.: de 0 para 1), mas oscila várias vezes antes de se estabilizar no valor final.

**Causa raiz:** múltiplos caminhos com número de portas/atrasos diferentes levando à mesma saída — típico quando a mesma variável de entrada aparece mais de uma vez na expressão lógica (ex.: quando um termo do K-map "salta" de um grupo para outro sem termo de cobertura comum).

**Mitigação:** adicionar termos redundantes (ex.: o termo de "consenso" na álgebra booleana) para cobrir a transição, eliminando o hazard estático — outra ligação direta com a minimização por Mapas de Karnaugh.

---

## 5. Lógica Sequencial: estabilidade, latches e flip-flops
*(Livro: Cap. 3.2)*

Diferente da lógica combinacional (saída = função só das entradas atuais), a **lógica sequencial** tem memória: a saída depende também do **estado armazenado**.

- **Elemento biestável:** circuito realimentado (ex.: dois inversores em loop) que tem dois pontos de operação estáveis — a base de armazenamento de 1 bit.
- **Báscula/Latch SR:** elemento de memória mais simples, controlado por entradas Set/Reset, mas **sensível a nível** (transparente) — a saída acompanha a entrada enquanto o sinal de habilitação estiver ativo, o que dificulta o controle preciso do momento de captura.
- **Latch D:** versão mais usada — enquanto o clock/enable está em nível ativo, a saída "segue" a entrada D; quando o nível muda, o valor fica "trancado" (latched).
- **Flip-flop D:** diferente do latch, é **sensível à borda** (edge-triggered) — só captura o valor de D no instante exato da borda de subida (ou descida) do clock, permanecendo estável no resto do ciclo. É o elemento de memória padrão usado em registradores e no registrador de estado de FSMs.
- Internamente, um flip-flop **mestre-escravo** é construído com dois latches em cascata, com clocks complementares.

**Ponto-chave de prova:** diferença latch (sensível a nível) vs. flip-flop (sensível a borda), e por que projetos síncronos usam flip-flops para evitar comportamento "transparente" indesejado.

---

## 6. Temporização de lógica sequencial: tempos de hold, de setup e de propagação lógica
*(Livro: Cap. 3.5)*

Para um flip-flop capturar corretamente o dado na borda de clock, a entrada D precisa respeitar uma "janela" de estabilidade:

- **Tempo de setup ($t_{setup}$):** tempo **antes** da borda de clock em que D precisa estar estável.
- **Tempo de hold ($t_{hold}$):** tempo **depois** da borda de clock em que D ainda precisa permanecer estável.
- **Tempo de propagação clock-para-Q ($t_{pcq}$):** atraso entre a borda de clock e a saída Q realmente mudar.

**Cálculo do período mínimo de clock** (entre dois flip-flops ligados por lógica combinacional com atraso $t_{pd}$):

$$T_c \geq t_{pcq} + t_{pd} + t_{setup}$$

- Se esse período mínimo não for respeitado → **violação de setup**, e o circuito pode falhar mesmo em baixa frequência sendo mal projetado.
- **Violação de hold:** ocorre quando o caminho é rápido demais (atraso de contaminação $t_{ccq}+t_{cd}$ menor que $t_{hold}$) — mais grave, pois **não pode ser corrigida aumentando o período de clock**; exige redesenho do circuito (ex.: adicionar atraso no caminho).

---

## 7. Somadores: half-adder, full-adder, ripple-carry, look-ahead
*(Livro: Cap. 5.2.1 — Adição)*

- **Meio-somador (half-adder):** soma 2 bits, sem considerar carry de entrada. $S = A \oplus B$; $Cout = A\cdot B$.
- **Somador completo (full-adder):** soma 2 bits **mais** um carry-in. $S = A \oplus B \oplus Cin$; $Cout = AB + A\cdot Cin + B\cdot Cin$ (já visto no exercício de síntese anterior).
- **Somador ripple-carry (propagação em cadeia):** encadeia $n$ somadores completos, onde o carry-out de um bit alimenta o carry-in do próximo. Simples e com pouco hardware, mas **lento** para números grandes, pois o carry precisa "se propagar" por todos os bits em sequência — o atraso cresce linearmente com $n$ (ligação direta com o tópico 3: caminho crítico).
- **Somador com carry look-ahead (antecipação de carry):** calcula os carries de cada posição **antecipadamente**, usando lógica adicional (sinais de *generate* $G_i=A_iB_i$ e *propagate* $P_i=A_i\oplus B_i$) em paralelo, ao invés de esperar a propagação em cadeia. Bem mais rápido para números grandes, ao custo de mais área/hardware.

---

## 8. Outras unidades funcionais aritméticas e lógicas: subtrator, comparador, shifter, ULA
*(Livro: Cap. 5.2.2–5.2.5)*

- **Subtrator:** $A - B$ é implementado como $A + \overline{B} + 1$ (soma do complemento de 2 de B), reaproveitando o mesmo hardware do somador — basta inverter os bits de B e colocar o carry-in inicial em 1.
- **Comparador:** verifica relações como igualdade ($A=B$, geralmente via portas XNOR + AND) ou grandeza ($A<B$, $A>B$), frequentemente reaproveitando um subtrator (o sinal do resultado de $A-B$ indica a relação).
- **Shifter (deslocador):** desloca os bits de um número para a esquerda/direita — usado para multiplicação/divisão por potências de 2 e para manipulação de campos de bits. Pode ser lógico (preenche com 0), aritmético (preserva o bit de sinal) ou rotacional (bits "saem" de um lado e "entram" do outro).
- **ULA (Unidade Lógica e Aritmética):** bloco que combina somador/subtrator + operações lógicas (AND, OR, etc.) em um único circuito, selecionando a operação desejada através de um multiplexador/sinal de controle (`ALUop`) — é o "coração" do datapath de um processador (ligação direta com o assunto de microarquitetura já estudado).

---

## 9. Contadores
*(Livro: Cap. 5.4 — Blocos de Construção Sequenciais)*

Um **contador** é um registrador sequencial que avança por uma sequência de valores (tipicamente incrementando em 1) a cada pulso de clock.

- **Contador binário simples:** soma 1 ao valor armazenado a cada ciclo, usando um somador + registrador realimentado.
- Pode ter entrada de **reset** (volta a 0) e/ou **enable** (só conta quando habilitado).
- Variações: contador **decrescente**, contador **módulo-N** (volta a 0 após atingir um valor máximo, útil para gerar divisores de frequência/temporizadores), e contadores que usam código Gray internamente (ligação com o tópico de códigos binários já estudado, útil para reduzir hazards ao contar).
- Do ponto de vista de projeto, um contador nada mais é do que uma **FSM particular**, onde o "próximo estado" é sempre "estado atual + 1" — ligação direta com o assunto de máquinas de estado.

---

## 10. Representação numérica em ponto fixo e ponto flutuante
*(Livro: Cap. 5.3 — Sistemas Numéricos)*

- **Ponto fixo:** a posição do "ponto binário" (equivalente à vírgula decimal) é **fixa e implícita** — por exemplo, um formato com 8 bits inteiros e 8 bits fracionários sempre reserva essa mesma divisão. Simples de implementar (usa o mesmo hardware de inteiros), mas tem faixa dinâmica limitada — bom para valores de magnitude previsível.
- **Ponto flutuante:** representa números na forma $(-1)^S \times 1{,}Mantissa \times 2^{Expoente}$, dividindo os bits em três campos:
 - **Sinal (S):** 1 bit.
 - **Expoente:** representado com um "viés" (bias) para permitir expoentes negativos sem precisar de bit de sinal separado.
 - **Mantissa (fração):** os bits significativos do número, com um "1 implícito" antes do ponto (normalização), para aproveitar melhor a precisão disponível.
 - O padrão mais usado é o **IEEE 754** (ex.: precisão simples de 32 bits: 1 bit sinal + 8 bits expoente + 23 bits mantissa).
- **Trade-off central de prova:** ponto fixo tem precisão constante mas faixa limitada; ponto flutuante tem faixa dinâmica muito maior (representa números muito grandes e muito pequenos), mas com precisão relativa (não absoluta) e hardware mais complexo.

---

## 11. Memórias: banco de registradores, ROM, SRAM e DRAM
*(Livro: Cap. 5.5 — Matrizes de Memória)*

- **Banco de registradores (register file):** conjunto de registradores acessados por endereço, com portas de leitura e escrita — é a "memória" mais próxima da ULA em um processador (ex.: os 32 registradores do MIPS).
- **ROM (Read-Only Memory):** memória **não volátil** (mantém dados sem energia), projetada para ser majoritariamente lida; a "escrita" é feita na fabricação ou por processos especiais (variações: PROM, EPROM, EEPROM — programáveis eletricamente).
- **SRAM (Static RAM):** memória **volátil**, cada bit armazenado em uma célula biestável (tipicamente 6 transistores, usando o mesmo princípio de realimentação dos latches/flip-flops do tópico 5). Rápida, mas cada célula ocupa mais área — usada em memórias **cache**.
- **DRAM (Dynamic RAM):** memória **volátil**, cada bit armazenado como carga em um **capacitor** (1 transistor + 1 capacitor por bit) — célula muito menor/mais barata que a SRAM, mas a carga vaza com o tempo, exigindo **refresh periódico**. Usada como memória principal (RAM do sistema), mais lenta que SRAM mas com muito maior densidade.

**Ponto-chave de prova:** comparar SRAM x DRAM (velocidade, custo, densidade, necessidade de refresh) e volátil x não-volátil.

---

## 12. Arranjos Lógicos: PLA, PAL e PROM
*(Livro: Cap. 5.6 — Matrizes Lógicas)*

Estruturas regulares que implementam **qualquer função lógica em forma de soma de produtos (SOP)**, usando duas matrizes: uma de portas **AND** (gera os produtos/mintermos) seguida de uma de portas **OR** (soma os produtos selecionados).

- **PROM (Programmable ROM)** usada como arranjo lógico: matriz **AND fixa** (todos os mintermos possíveis, como um decodificador) + matriz **OR programável** (escolhe quais mintermos somar). Boa para funções com muitos termos, mas "desperdiça" AND para poucas variáveis.
- **PLA (Programmable Logic Array):** matriz **AND programável** + matriz **OR programável** — mais flexível, pois só implementa os produtos (mintermos) realmente necessários, geralmente resultando numa "pegada" (área) menor.
- **PAL (Programmable Array Logic):** matriz **AND programável** + matriz **OR fixa** — mais simples/barata de fabricar que a PLA, ao custo de um pouco menos de flexibilidade.

**Ponto-chave de prova:** lembrar qual matriz (AND/OR) é fixa e qual é programável em cada uma das três siglas — é o detalhe mais cobrado.

---

## 13. CPLDs e FPGAs
*(Livro: Cap. 5.6, extensão dos arranjos lógicos)*

- **CPLD (Complex Programmable Logic Device):** evolução dos PLAs/PALs — contém **vários blocos** do tipo PAL/PLA interligados por uma matriz de interconexão programável, permitindo implementar circuitos maiores que um único PLA/PAL isolado.
- **FPGA (Field-Programmable Gate Array):** dispositivo muito mais flexível e de granularidade fina, composto por um grande número de **blocos lógicos configuráveis (CLBs)** — tipicamente baseados em **LUTs (look-up tables)**, que podem implementar qualquer função de poucas entradas guardando a tabela verdade em memória — além de flip-flops e uma malha de interconexão programável extensa, permitindo tanto lógica combinacional quanto sequencial complexa.
- **Diferença central de prova:** CPLDs são mais simples, não-voláteis (mantêm a configuração sem energia externa) e adequados a lógica menor/mais previsível (baseada em somas de produtos); FPGAs são muito mais densos e flexíveis (baseados em LUTs), permitem projetos muito maiores (inclusive processadores inteiros), mas geralmente são **voláteis** — perdem a configuração ao desligar e precisam recarregá-la (de uma memória externa) a cada inicialização.

---

## Como usar este guia
Mesmo formato dos anteriores: um bloco por tópico, na ordem da sua lista, cada um referenciando a seção correspondente do livro. Vários tópicos aqui se conectam entre si (CMOS → potência → temporização → hazards; latches/flip-flops → temporização sequencial → contadores) — vale a pena revisar essas conexões, pois costumam aparecer combinadas em uma mesma questão. Posso montar a lista de exercícios em PDF para esses 13 tópicos também, no mesmo estilo com gabarito, se quiser.
