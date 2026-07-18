# Guia de Estudo — Matemática e Fundamentos da Computação
### Baseado em Harris & Harris, *Projeto Digital e Arquitetura de Computadores* (Cap. 1–3)

---

## 1. Sistema numérico binário — operações aritméticas e conversão para decimal
*(Livro: Cap. 1.4)*

**Representação posicional.** Em qualquer base *b*, um número é:

N = Σ dᵢ · bⁱ

Em binário (base 2), cada dígito (bit) vale 0 ou 1, e cada posição vale uma potência de 2.

**Binário → Decimal:** some as potências de 2 correspondentes aos bits em 1.
Ex.: 1011₂ = 1·2³ + 0·2² + 1·2¹ + 1·2⁰ = 8+0+2+1 = 11₁₀

**Decimal → Binário:** divisões sucessivas por 2 (pegando os restos de baixo para cima) ou subtração de potências de 2 (método das potências).

**Hexadecimal (base 16):** cada dígito hex representa exatamente 4 bits (um "nibble") — por isso é usado como forma compacta de escrever binário. Conversão bin↔hex é feita agrupando de 4 em 4 bits a partir do bit menos significativo.

**Operações aritméticas em binário:**
- **Adição:** igual à decimal, com "vai um" (carry) quando 1+1=10.
- **Subtração:** pode ser feita com "empresta um" (borrow), mas em hardware normalmente se usa **complemento de 2**: A − B = A + (complemento de 2 de B).
- **Complemento de 2** de um número: inverte todos os bits e soma 1. É a forma padrão de representar negativos em sistemas digitais, porque permite somar/subtrair com o mesmo circuito somador.
- **Overflow (transbordo):** ocorre em soma com sinal quando o resultado excede a faixa representável pelo número de bits — detectado quando o carry de entrada do bit de sinal é diferente do carry de saída.
- **Multiplicação/divisão binária:** seguem o mesmo raciocínio da decimal (somas deslocadas / subtrações sucessivas), mas em hardware geralmente são implementadas com somadores e deslocadores (shift-and-add).

**Faixas de representação (n bits):**
- Sem sinal: 0 a 2ⁿ−1
- Com sinal (complemento de 2): −2ⁿ⁻¹ a 2ⁿ⁻¹−1

---

## 2. Códigos binários: BCD, Gray, ASCII, checksum, etc.
*(Livro: Cap. 1.4, com Gray code frequentemente ligado ao Cap. 3 — máquinas de estado)*

- **BCD (Binary Coded Decimal):** cada dígito decimal (0–9) é representado por 4 bits separadamente (ex.: 47 → 0100 0111). Facilita exibir números em displays, mas "desperdiça" combinações (1010–1111 não são usadas).
- **Código Gray:** código onde **apenas 1 bit muda** entre valores consecutivos. Muito usado em codificadores de posição (encoders) e em codificação de estados de FSM para reduzir glitches/consumo. Regra de conversão binário→Gray: Gᵢ = Bᵢ ⊕ Bᵢ₊₁ (XOR de cada bit com o bit mais significativo vizinho, mantendo o MSB igual).
- **ASCII:** código de 7 (ou 8) bits que representa caracteres (letras, números, símbolos) — cada caractere = um padrão binário fixo, usado para representar texto.
- **Checksum / bit de paridade:** bits extras usados para **detecção de erros** em transmissão/armazenamento.
 - Paridade simples: soma (XOR) de todos os bits de dados; detecta erro de 1 bit.
 - Checksum: soma dos blocos de dados, cujo resultado é enviado junto para conferência no receptor.
- Outros exemplos citados no livro: código de excesso-3, código de Hamming (para correção de erros), mencionados como extensões do tema de códigos.

**Ponto de prova comum:** saber converter entre esses códigos e explicar *por que* cada um é usado (Gray → evitar múltiplas transições simultâneas; BCD → interface humana/displays; paridade/checksum → confiabilidade).

---

## 3. Portas lógicas: inversor, NAND, NOR, etc.
*(Livro: Cap. 1.5)*

Porta lógica = dispositivo que implementa uma função booleana em hardware.

| Porta | Símbolo/Operação | Saída = 1 quando |
|---|---|---|
| **AND** | Y = A·B | todas as entradas = 1 |
| **OR** | Y = A+B | pelo menos uma entrada = 1 |
| **NOT (inversor)** | Y = A' (ou Ā) | entrada = 0 |
| **NAND** | Y = (A·B)' | pelo menos uma entrada = 0 |
| **NOR** | Y = (A+B)' | todas as entradas = 0 |
| **XOR** | Y = A⊕B | número ímpar de entradas em 1 |
| **XNOR** | Y = (A⊕B)' | número par de entradas em 1 (entradas iguais, no caso de 2 entradas) |

**Ponto-chave de prova:** **NAND e NOR são "portas universais"** — qualquer função booleana pode ser construída usando *apenas* NANDs (ou apenas NORs). Isso é muito cobrado porque simplifica a fabricação (um único tipo de célula lógica).

- Inversor pode ser feito com NAND ou NOR amarrando as entradas juntas.
- AND = NAND seguido de inversor; OR = NOR seguido de inversor.

---

## 4. Álgebra Booleana (de chaveamento): axiomas, funções e teoremas
*(Livro: Cap. 2.2–2.3)*

**Axiomas básicos** (definem AND, OR, NOT sobre {0,1}):
- 0·0=0, 1·1=1, 0·1=1·0=0
- 0+0=0, 1+1=1+0=0+1=1
- 0'=1, 1'=0

**Teoremas de uma variável:**
- Identidade: A+0=A, A·1=A
- Nulo (dominância): A+1=1, A·0=0
- Idempotência: A+A=A, A·A=A
- Involução: (A')' = A
- Complemento: A+A'=1, A·A'=0

**Teoremas multivariáveis:**
- Comutatividade: A+B=B+A, A·B=B·A
- Associatividade: (A+B)+C=A+(B+C)
- Distributividade: A·(B+C)=A·B+A·C **e** A+(B·C)=(A+B)·(A+C) (esta segunda forma é a "estranha" que não existe na álgebra comum — cai bastante em prova)
- **Absorção:** A+A·B=A ; A·(A+B)=A
- **Combinação (Uniting):** A·B + A·B' = A
- **Consenso:** A·B + B·C + A'·C = A·B + A'·C (o termo B·C é redundante)

**Uso prático:** essas leis são a base para **simplificar equações lógicas** antes de desenhar o circuito — reduzir portas = reduzir custo, área e atraso.

---

## 5. Teorema de DeMorgan e dualidade
*(Livro: Cap. 2.3)*

**Teorema de DeMorgan:**

(A·B)' = A' + B'\
(A+B)' = A'·B'

Generalizando: para "quebrar" uma barra sobre uma expressão, troca-se AND↔OR e nega-se cada variável individualmente (a barra "se distribui" trocando o operador).

**Aplicação prática mais cobrada:** converter uma expressão qualquer para usar **só NAND** ou **só NOR**, já que essas são as portas universais (ligação direta com o tópico 3).

**Princípio da dualidade:** toda identidade booleana continua válida se trocarmos simultaneamente:
- AND ↔ OR
- 0 ↔ 1

Ex.: o dual de A+0=A é A·1=A. Isso explica por que muitas leis "vêm em pares" (visto no tópico 4).

---

## 6. Valores lógicos H, L, Z e don't care
*(Livro: Cap. 2.6 — "X's e Z's")*

Além de 0 e 1, o projeto digital usa símbolos adicionais:

- **H / L:** usados às vezes para indicar nível "alto"/"baixo" de tensão sem comprometer se representa logicamente 1 ou 0 (mais comum em contexto de família lógica/tensões).
- **Z (alta impedância):** saída **desconectada eletricamente** — não é nem 0 nem 1, o pino "flutua". Aparece em barramentos (buses) com múltiplos dispositivos (**buffers tri-state**), onde só um dispositivo pode "dirigir" a linha por vez; os demais ficam em Z.
- **X (don't care):** usado no **projeto/tabela verdade**, não no hardware físico. Indica que o valor daquela entrada/saída **não importa** para aquele caso — pode ser 0 ou 1 livremente. É uma ferramenta de simplificação: permite ao projetista escolher o valor de X que resulte na equação mais simples (muito usado junto com Mapas de Karnaugh — tópico 10).

**Diferença importante de prova:** Z é um estado **elétrico real** do circuito; X é uma **conveniência de projeto** que nunca aparece fisicamente no circuito final — o circuito real sempre produz 0 ou 1, o X só existe na tabela verdade/K-map.

---

## 7. Multiplexador, codificador, decodificador e outros blocos lógicos básicos
*(Livro: Cap. 2.8, aprofundado no Cap. 5)*

- **Multiplexador (MUX):** seleciona uma entre várias entradas de dados para a saída, conforme bits de seleção. Um MUX 2ⁿ:1 tem n linhas de seleção. É extremamente versátil: **qualquer função booleana pode ser implementada com um MUX** cujas entradas de seleção são as variáveis da função (aplicação clássica de prova).
- **Demultiplexador (DEMUX):** função inversa — uma entrada é direcionada para uma dentre várias saídas.
- **Decodificador:** converte um código de entrada de n bits em até 2ⁿ saídas, ativando **apenas uma** saída por vez (one-hot). Muito usado para seleção de memória/endereçamento e para implementar funções lógicas (cada saída = um mintermo).
- **Codificador (encoder):** função inversa do decodificador — converte "uma linha ativa entre várias" em um código binário de saída.
 - **Codificador de prioridade:** variação onde, se mais de uma entrada estiver ativa, prevalece a de maior prioridade (evita ambiguidade).
- Outros blocos citados como básicos: portas de transmissão (transmission gates), buffers tri-state.

**Dica de prova:** entender a relação **decodificador ↔ mintermos** e **MUX ↔ implementação universal de função** costuma ser cobrada em questões de síntese.

---

## 8. Combinação de portas lógicas e avaliação de expressões lógicas
*(Livro: Cap. 2.4–2.5)*

- **Da lógica às portas:** qualquer equação booleana em soma de produtos (SOP) ou produto de somas (POS) pode ser desenhada diretamente como um circuito de duas camadas (AND-OR ou OR-AND).
- **Lógica combinatória multinível:** ao invés de duas camadas, portas são encadeadas em vários níveis — geralmente reduz o número total de portas/entradas, mas pode aumentar o atraso (delay) do circuito, pois o sinal passa por mais portas em série.
- **Avaliação de expressões:** para calcular a saída de um circuito dado um conjunto de entradas, segue-se a ordem de precedência: NOT primeiro, depois AND, depois OR (parênteses têm prioridade máxima) — análogo à precedência de operadores em matemática comum.
- Circuitos podem ser convertidos: **equação → tabela verdade → circuito**, e o caminho inverso também (circuito → equação, "lendo" porta por porta a partir das entradas).

---

## 9. Minimização lógica, mintermos e maxtermos
*(Livro: Cap. 2.7, junto com K-maps)*

- **Mintermo:** produto (AND) que contém **todas** as variáveis da função, cada uma em sua forma normal ou complementada, e que vale 1 para **exatamente uma** linha da tabela verdade. Uma função pode ser escrita como soma de mintermos (**soma de produtos canônica / soma dos mintermos onde f=1**), notação: Σm(...).
- **Maxtermo:** soma (OR) de todas as variáveis (normais/complementadas) que vale 0 para exatamente uma linha. Uma função pode ser escrita como produto de maxtermos (**produto de somas canônico**), notação: ΠM(...).
- Mintermos e maxtermos são **complementares**: o conjunto de índices dos maxtermos de f é o complemento do conjunto de índices dos mintermos de f.
- **Minimização lógica:** processo de reduzir uma expressão canônica (com muitos termos) para uma equivalente com **menos termos e menos literais**, usando os teoremas booleanos (tópico 4) ou, de forma mais sistemática, Mapas de Karnaugh (tópico 10). Objetivo prático: menos portas → circuito mais barato, rápido e com menos consumo.

---

## 10. Mapas de Karnaugh
*(Livro: Cap. 2.7)*

Ferramenta gráfica para minimizar funções booleanas de até ~4–6 variáveis.

**Como funciona:**
1. Desenha-se uma grade onde cada célula representa um mintermo, organizada de forma que **células adjacentes (inclusive nas bordas, que "dão a volta") difiram em apenas 1 bit** — por isso as linhas/colunas são ordenadas em **código Gray** (conexão direta com o tópico 2!).
2. Preenche-se cada célula com o valor da função (0, 1 ou X de don't care) para aquela combinação de entradas.
3. Agrupam-se os 1's (e X's convenientes) em **retângulos de tamanho potência de 2** (1, 2, 4, 8...) — os maiores grupos possíveis.
4. Cada grupo gera um **termo produto simplificado**: variáveis que não mudam de valor dentro do grupo permanecem na equação; as que mudam são eliminadas.
5. A soma de todos os termos dos grupos escolhidos (cobrindo todos os 1's, com o menor número de grupos/maior tamanho possível) dá a **equação SOP mínima**.

**Regras importantes de prova:**
- Todo 1 deve ser coberto por pelo menos um grupo.
- Grupos podem se sobrepor.
- Don't cares (X) podem ser incluídos em um grupo **somente se ajudarem a simplificar** — não é obrigatório cobri-los.
- K-map também pode ser feito "pelos zeros" para achar a forma POS mínima.

---

## 11. Síntese de circuito lógico (da equação lógica a esquema de portas lógicas)
*(Livro: Cap. 2, uso combinado de 2.2 a 2.7)*

Fluxo típico de síntese cobrado em prova:

1. **Especificação do problema** → descrever em palavras o que o circuito deve fazer.
2. **Tabela verdade** → listar todas as combinações de entrada e a saída correspondente.
3. **Equação booleana** → extrair a expressão canônica (soma de mintermos ou produto de maxtermos) diretamente da tabela verdade.
4. **Minimização** → aplicar teoremas booleanos (tópico 4) ou Mapa de Karnaugh (tópico 10) para obter a equação mais simples possível.
5. **Esquema de portas** → desenhar o circuito com as portas lógicas correspondentes à equação minimizada (podendo ainda converter para NAND/NOR universais usando DeMorgan — tópicos 3 e 5).

Esse fluxo (problema → tabela → equação → minimização → circuito) é essencialmente o "fio condutor" que liga todos os tópicos anteriores — é comum a prova pedir esse processo completo em uma única questão.

---

## 12. Máquina de Estados Finitos (Moore e Mealy)
*(Livro: Cap. 3.4)*

Uma **FSM (Finite State Machine)** modela lógica sequencial: a saída depende não só das entradas atuais, mas também do **estado presente** (memória do sistema), armazenado em flip-flops.

Componentes: registrador de estado (flip-flops) + lógica combinatória de **próximo estado** + lógica combinatória de **saída**.

**Máquina de Moore:**
- A saída depende **apenas do estado atual** (não depende diretamente das entradas).
- Saída é "mais estável" (muda só na borda de clock), mas geralmente precisa de mais estados para o mesmo comportamento.

**Máquina de Mealy:**
- A saída depende do **estado atual E das entradas atuais**.
- Pode reagir mais rápido a mudanças de entrada (a saída pode mudar entre bordas de clock), geralmente usa menos estados, mas é mais suscetível a glitches na saída se a entrada for ruidosa.

**Diferença central cobrada em prova:** na Moore, a saída "sai" só do bloco de estado; na Mealy, a saída "sai" combinando estado + entrada — isso muda tanto o diagrama de blocos quanto o diagrama de estados (na Mealy, as saídas ficam escritas nas **transições/arestas**; na Moore, ficam escritas **dentro dos estados**).

---

## 13. Síntese de máquina de estados (tabela e grafo de transição de estados, codificação de estados e extração de equações lógicas)
*(Livro: Cap. 3.4)*

Fluxo de projeto de uma FSM, também muito cobrado como questão completa:

1. **Diagrama/grafo de estados:** desenha-se cada estado como um círculo/bolha; setas (arestas) indicam transições, rotuladas com a condição de entrada que causa a transição (e, na Mealy, também com a saída correspondente).
2. **Tabela de transição de estados:** versão tabular do grafo — para cada (estado atual, entrada), lista-se o próximo estado (e a saída, se Mealy).
3. **Codificação de estados:** atribuir um código binário a cada estado simbólico (ex.: S0, S1, S2 → 00, 01, 10). A escolha do código impacta a complexidade da lógica resultante — codificações tipo Gray (tópico 2) podem reduzir consumo/glitches; codificação **one-hot** (1 flip-flop por estado) simplifica a lógica em troca de mais flip-flops.
4. **Extração das equações lógicas:**
 - Reescreve-se a tabela de transição usando os códigos binários dos estados → obtém-se uma tabela verdade convencional (entradas = estado atual codificado + entradas externas; saídas = próximo estado codificado + saídas da FSM).
 - Aplica-se minimização lógica / K-maps (tópicos 9–10) sobre essa tabela para obter as equações de **próximo estado** (que alimentam as entradas D dos flip-flops) e de **saída**.
5. **Circuito final:** flip-flops (um por bit de estado) + lógica combinatória de próximo estado + lógica combinatória de saída, exatamente como na estrutura Moore/Mealy do tópico 12.

**Observação de prova:** essa sequência (grafo → tabela → codificação → equações → circuito) é a versão "sequencial" do fluxo de síntese combinatória do tópico 11 — vale a pena estudar os dois lado a lado, pois costumam aparecer como questões espelhadas.

---

## Como usar este guia
Cada seção corresponde a um tópico da sua lista, na mesma ordem. Recomendo, para cada tópico, resolver 2–3 exercícios do próprio livro (final dos Cap. 1–3) logo depois de revisar o resumo, para fixar. Posso gerar questões de prática tópico a tópico, ou aprofundar qualquer seção específica com mais exemplos resolvidos — é só pedir.
