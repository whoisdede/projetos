# 🏆 Resolução Integral & Guia Passo a Passo da Prova P2 (`p2.jpeg`)

**Arquivo da Prova:** [`p2.jpeg`](file:///C:/Users/andre/Downloads/PROVA2_PIM/p2.jpeg)  
**Instituição / Disciplina:** DCC / CCT / UDESC — Processamento de Imagens (PIM) — Prof. Gilmário Barbosa dos Santos  
**Pontuação Total:** $4{,}0 + 3{,}0 + 3{,}0 = 10{,}0\text{ pontos}$

---

## 📋 Visão Geral Rápida do Gabarito

| Questão | Peso | Tema Central | Resumo da Resposta / Gabarito |
| :--- | :---: | :--- | :--- |
| **Questão 1** | **4,0 pts** | Transformada **Top-Hat** + Iluminação Não Uniforme + Limiarização | $\text{WTH}(f) = f - (f \circ b)$ com EE $b$ maior que os objetos claros: a abertura estima o fundo desuniforme, a subtração nivela o fundo em $\approx 0$ e viabiliza um limiar global único $T$. |
| **Questão 2** | **3,0 pts** | Fundamentos e Aplicação do **Operador Gradiente (Tarefa #2)** | Derivadas parciais $\nabla f = [G_x, G_y]^T$, magnitude $\sqrt{G_x^2+G_y^2}$ e direção $\arctan(G_y/G_x)$, máscaras discretas (Sobel/Prewitt/Roberts), convolução + limiarização (e paralelo com Gradiente Morfológico $(f\oplus b)-(f\ominus b)$). |
| **Questão 3a** | **0,5 pt** | Erosão/Dilatação como inversas | **`( F )` FALSA** — Não são inversas (são duais); geram fechamento $A\bullet B$ e abertura $A\circ B$. |
| **Questão 3b** | **0,5 pt** | Exclusividade em imagens binárias | **`( F )` FALSA** — Aplicam-se também a imagens em tons de cinza (e multiespectrais). |
| **Questão 3c** | **0,5 pt** | Abertura `dil(ero(X,E))` removendo "bico" $B$ | **`( V )` VERDADEIRA** — Diâmetro de $E$ ($4$) $>$ espessura do bico ($1$), remove $B$ e restaura circunferência $A$ ($R=4$). |
| **Questão 3d** | **0,5 pt** | Fechamento `ero(dil(X,E))` preenchendo reentrância $B$ | **`( V )` VERDADEIRA** — Diâmetro de $E$ ($4$) $>$ largura da fenda ($1$), preenche $B$ sem expandir $A$ ($R=4$). |
| **Questão 3e** | **0,5 pt** | Dilatação em tons de cinza ($\max$) | **`( F )` FALSA** — Dilatação ($\max$) **clareia**: espalha regiões **claras sobre escuras** (o enunciado inverteu!). |
| **Questão 3f** | **0,5 pt** | Erosão em tons de cinza ($\min$) | **`( F )` FALSA** — Erosão ($\min$) **escurece**: espalha regiões **escuras sobre claras** (o enunciado inverteu!). |

---

# 🟢 QUESTÃO 1 (4,0 pontos)

### Enunciado Exato (`p2.jpeg`)
> *"1) (4,0) Inclua a questão adiantada ou resolva em MANUSCRITO a seguinte questão (baseada na 13ª questão da lista de exercícios sobre processamento morfológico):  
> Explique com o apoio em figuras a aplicação do método morfológico 'top-hat' no pré-processamento de imagens tons de cinza que apresentam iluminação não uniforme e serão submetidas a processo de 'limiarização'."*

---

### 🧠 1. Como Pensar e Estruturar a Resolução (Passo a Passo)

Para garantir **4,0 / 4,0**, sua resposta manuscrita precisa conectar **4 pilares lógicos** apoiados por **figuras claras**:

1. **O Problema da Iluminação Não Uniforme na Limiarização Global:**
   - Uma imagem real $f(x,y)$ pode ser vista como a soma de um **fundo de iluminação variável lentamente** $i_{\text{fundo}}(x,y)$ (sombras, rampa luminosa, vinhetagem) com os **objetos claros de interesse** $o(x,y)$ (ex.: grãos de arroz da Figura 9.40 de Gonzalez & Woods / Questão 13 da lista).
   - Na **limiarização global** ($g(x,y) = 1 \text{ se } f(x,y) > T$, senão $0$), escolhe-se um único valor de corte $T$ para toda a imagem (mesmo com métodos ótimos como Otsu).
   - Quando o fundo é inclinado/desuniforme, **pixels de fundo na zona superiluminada têm intensidade maior que objetos legítimos situados na zona escura/sombreada**. Resultado: qualquer limiar $T$ falha — na região clara o fundo satura/funde objetos (falsos positivos) e na região escura objetos inteiros desaparecem (falsos negativos).

2. **O Papel da Abertura em Tons de Cinza ($f \circ b$) e a Escolha Crítica do Elemento Estruturante $b$:**
   - A abertura morfológica é dada por $f \circ b = (f \ominus b) \oplus b$ (erosão seguida de dilatação pelo mesmo elemento estruturante plano $b$).
   - **Escolha do EE $b$:** Deve ter geometria compatível (ex.: disco plano) e **tamanho estritamente MAIOR que os objetos claros** que desejamos segmentar (por exemplo, disco de raio $40$ pixels para os grãos de arroz em $600\times 600$), porém menor que a escala de variação suave da iluminação.
   - **O que acontece:**
     1. Na **erosão ($f \ominus b$, operador $\min$)**, como $b$ é maior que qualquer objeto claro, sempre há pelo menos um pixel de fundo escuro dentro da janela de $b$; logo, **todos os picos claros (objetos) são completamente eliminados**, restando o envelope inferior escuro.
     2. Na **dilatação seguinte ($(f \ominus b) \oplus b$, operador $\max$)**, restaura-se o nível médio local das estruturas largas onde $b$ coube, sem que os objetos apagados voltem a existir!
   - Geometricamente, equivale a "empurrar/rolar a sonda $b$ por baixo da superfície topográfica $f(x,y)$": $b$ não entra nos picos estreitos (objetos), mas acompanha perfeitamente a rampa suave da iluminação. Assim, **$f \circ b$ é uma estimativa direta do fundo de iluminação não uniforme!**

3. **A Transformada *White Top-Hat* ($\text{WTH}$):**
   $$\text{WTH}(f) = T_{\text{hat}}(f) = f - (f \circ b)$$
   - Como a abertura é **anti-extensiva** ($f \circ b \le f$ em todos os pixels), a diferença $f - (f \circ b)$ é sempre $\ge 0$.
   - Subtraindo o fundo estimado $(f \circ b)$ da imagem original $f$, **cancela-se a rampa de iluminação**: o fundo inteiro passa a ter valores próximos de $0$ (plano/uniforme) e sobram apenas os objetos claros com contraste homogêneo em toda a extensão da imagem!

4. **Sucesso da Limiarização Pós-Top-Hat (e observação sobre o *Bottom-Hat*):**
   - O histograma de $\text{WTH}(f)$ torna-se nitidamente bimodal (pico estreito do fundo próximo a $0$ + distribuição dos objetos claros), permitindo aplicar um **único limiar global $T'$** (ex.: Otsu) que isola 100% dos objetos sem falsos positivos nem perdas na sombra.
   - *(Bônus que impressiona o professor)*: Mencione que, caso os objetos fossem **escuros sobre fundo claro desuniforme** (ex.: texto escuro em papel sombreado), aplicar-se-ia de forma dual a transformada ***Black Top-Hat* (*Bottom-Hat*)**: $\text{BTH}(f) = (f \bullet b) - f$, onde o fechamento $f \bullet b = (f \oplus b) \ominus b$ remove os vales escuros estreitos e estima o fundo claro!

---

### 🖼️ 2. Visualização Completa (2D + Perfil 1D para Desenhar na Prova)

![Ilustração Completa Top-Hat 2D e 1D](./figuras/fig01_tophat_iluminacao_1d_2d.png)

> [!TIP]
> **O QUE DESENHAR À MÃO NA FOLHA DE PROVA?**  
> Desenhar grãos de arroz em 2D à caneta é difícil de sombrear, mas desenhar **OS DOIS GRÁFICOS DE PERFIL 1D** da metade inferior da figura acima (junto com um pequeno esquema de blocos 2D) é **rápido, matematicamente rigoroso e idêntico ao Slide 39 do Prof. Gilmário e à Fig. 9.36 de Gonzalez & Woods**!  
> Faça **3 desenhos simples** no seu manuscrito:
> 1. **Gráfico 1D (Antes):** Eixo vertical $I(x)$ vs. posição $x$. Desenhe uma reta inclinada descendente (iluminação de fundo) com 3 ou 4 "morrinhos" estreitos em cima (objetos claros). Trace uma linha horizontal `Limiar T` cortando a rampa ao meio — mostre com uma seta que à esquerda ela pega fundo como objeto (erro!) e à direita perde os últimos objetos (erro!). Desenhe por baixo dos morrinhos uma linha tracejada acompanhando só a rampa e escreva: `Abertura (f ∘ b): estima o fundo pois o EE b não cabe nos picos estreitos`.
> 2. **Gráfico 1D (Depois — Top-Hat):** Eixo vertical $\text{WTH}(x) = f(x) - (f \circ b)(x)$ vs. $x$. Agora a base é reta em $0$ e os 3 ou 4 picos têm a mesma altura! Trace uma linha horizontal `Novo Limiar T'` cortando todos os picos perfeitamente.
> 3. **Mini-Exemplo Numérico 1D (Opcional, leva 30 segundos e blinda a nota!):**
>    - Linha com rampa + 2 objetos claros de largura 1 pixel:  
>      $f = [100, \; 100, \; \mathbf{140}, \; 90, \; 80, \; 70, \; \mathbf{110}, \; 60, \; 60]$  
>      *(Note que o objeto `110` à direita é mais escuro que... quase igual ao fundo `100` da esquerda! Se o fundo esquerdo fosse `120`, `110 < 120` — impossível separar com limiar único!).*
>    - Com EE plano $b = [0, \underline{0}, 0]$ de tamanho 3 (maior que o objeto de 1 pixel):
>      - Erosão $f \ominus b$: $[100, 100, 90, 80, 70, 70, 60, 60, 60]$
>      - Abertura $f \circ b = (f\ominus b)\oplus b$: $[100, 100, 100, 90, 80, 70, 70, 60, 60]$ *(picos sumiram, rampa ficou!)*
>      - Top-Hat $f - (f \circ b)$: $[0, \; 0, \; \mathbf{40}, \; 0, \; 0, \; 0, \; \mathbf{40}, \; 0, \; 0]$ $\implies$ Qualquer limiar $T' \in (0, 40)$, como $T'=20$, segmenta $[0, 0, \mathbf{1}, 0, 0, 0, \mathbf{1}, 0, 0]$ com 100% de precisão!

---

### ✍️ 3. Modelo de Texto Pronto para Escrever em Manuscrito (Questão 1)

> **Resposta Sugerida (Questão 1):**
>
> Em imagens em tons de cinza com iluminação não uniforme (como o clássico problema dos grãos de arroz), a intensidade observada $f(x,y)$ combina variações lentas de iluminação do fundo com variações abruptas e localizadas correspondentes aos objetos claros de interesse. Se aplicarmos diretamente uma **limiarização global** com limiar único $T$, o processo falha: regiões de fundo fortemente iluminadas apresentam níveis de cinza superiores a $T$ (gerando falsos positivos e fusão de objetos), enquanto objetos situados em regiões sombreadas apresentam níveis inferiores a $T$, desaparecendo na imagem binarizada *(ver Figura 1 — gráfico à esquerda)*.
>
> Para corrigir esse problema no pré-processamento, aplica-se a transformada morfológica **White Top-Hat ($\text{WTH}$)**, definida por:
> $$\text{WTH}(f) = f - (f \circ b), \quad \text{onde } f \circ b = (f \ominus b) \oplus b$$
>
> **Etapas de funcionamento:**
> 1. **Escolha do Elemento Estruturante ($b$):** Seleciona-se um elemento estruturante plano (tipicamente um disco simétrico) com dimensões **ligeiramente maiores que os objetos claros** a serem extraídos (de modo que $b$ não caiba no interior dos objetos).
> 2. **Estimativa do Fundo via Abertura ($f \circ b$):** Na etapa de erosão ($f \ominus b$, mínimo local), todos os picos brilhantes menores que $b$ (os objetos) são eliminados. Na dilatação subsequente ($\oplus b$, máximo local), restaura-se a intensidade das regiões amplas do fundo sem recriar os objetos suprimidos. Geometricamente, equivale a deslizar $b$ por baixo do perfil de intensidade $f$: $b$ não penetra nos picos estreitos dos objetos, gerando em $f \circ b$ uma aproximação suave apenas da iluminação não uniforme do fundo.
> 3. **Subtração Top-Hat ($f - (f \circ b)$):** Como $f \circ b \le f$, subtrair a abertura da imagem original remove a componente de iluminação variável, nivelando todo o fundo próximo a zero e preservando apenas os objetos mais claros e menores que $b$ com contraste uniforme em toda a imagem *(ver Figura 1 — gráfico à direita)*.
> 4. **Limiarização Global Eficaz:** Com o fundo corrigido e histograma bem separado, um único limiar global $T'$ (por exemplo, obtido pelo método de Otsu) segmenta com precisão todos os objetos, tanto nas regiões originalmente claras quanto nas sombreadas. *(Analogamente, para objetos escuros em fundo claro desuniforme, utiliza-se o Black Top-Hat / Bottom-Hat: $\text{BTH}(f) = (f \bullet b) - f$).*

---
---

# 🟢 QUESTÃO 2 (3,0 pontos)

### Enunciado Exato (`p2.jpeg`)
> *"2) (3,0) Explique os fundamentos conceituais e sua aplicação na implementação do operador gradiente (tarefa #2)."*

---

### 🧠 1. Entendendo o Contexto Completo (Gradiente Diferencial + Gradiente Morfológico)

Na disciplina de PIM, o **Operador Gradiente** aparece em duas frentes complementares que visam o **mesmo objetivo físico (detecção e realce de bordas / transições de intensidade)**:
1. **Formulação Diferencial / Espacial Clássica (Convolução com Máscaras de 1ª Ordem — Sobel, Prewitt, Roberts):** É a base matemática clássica da implementação de um detector de bordas por operador gradiente no domínio espacial (conforme visto também na etapa `sobel` do Slide 19 do Prof. Gilmário).
2. **Formulação Morfológica (Gradiente Morfológico):** No contexto da Morfologia Matemática (Slide 20 do Prof. Gilmário, Slides 44–46 de Pedrini e Slide 43 de Gonzalez & Woods), define-se o gradiente não-linear via diferença entre dilatação e erosão: $g = (f \oplus b) - (f \ominus b)$.

Apresentar os **fundamentos conceituais do vetor gradiente diferencial e os passos práticos de sua implementação computacional**, fechando com a **conexão direta com o gradiente morfológico**, cobre 100% de qualquer critério da banca!

![Operador Gradiente Diferencial vs Morfológico](./figuras/fig03_operador_gradiente_comparativo.png)

---

### 📐 2. Fundamentos Conceituais Detalhados

#### A) Conceito Físico de Borda e Derivada de 1ª Ordem
- Uma imagem monocromática é uma função discreta 2D de intensidade $f(x,y)$.
- **Regiões homogêneas** possuem intensidade aproximadamente constante $\implies$ derivada próxima de **zero**.
- **Bordas (contornos de objetos)** caracterizam-se por **transições abruptas de intensidade** (degraus ou rampas íngremes entre claro e escuro) $\implies$ a derivada de 1ª ordem assume **valores elevados (picos de magnitude)** exatamente sobre a transição!

#### B) O Vetor Gradiente Bidimensional ($\nabla f$)
Para uma função contínua $f(x,y)$, o operador gradiente no ponto $(x,y)$ é o vetor coluna das derivadas parciais de primeira ordem:
$$\nabla f(x,y) = \begin{bmatrix} G_x \\ G_y \end{bmatrix} = \begin{bmatrix} \dfrac{\partial f}{\partial x} \\[8pt] \dfrac{\partial f}{\partial y} \end{bmatrix}$$
Duas grandezas fundamentais extraem-se desse vetor:
1. **Magnitude (Módulo) do Gradiente — Força da Borda:**
   $$M(x,y) = \|\nabla f(x,y)\| = \sqrt{G_x^2 + G_y^2} \approx |G_x| + |G_y|$$
   - Mede a taxa máxima de variação da intensidade por unidade de distância no ponto $(x,y)$. É **isotrópica** (invariante à rotação). Na implementação computacional, a norma $L_1$ ($|G_x| + |G_y|$) é frequentemente usada para evitar o custo de raiz quadrada.
2. **Direção (Ângulo) do Gradiente — Orientação:**
   $$\alpha(x,y) = \arctan\!\left(\frac{G_y}{G_x}\right) \quad (\text{implementada via } \texttt{atan2}(G_y, G_x))$$
   - O vetor $\nabla f$ aponta na direção de **máximo crescimento de brilho** (do escuro para o claro), sendo **perpendicular (ortogonal)** à direção tangente da borda!

#### C) Discretização por Diferenças Finitas e Máscaras de Convolução 2D
Como imagens digitais são grades discretas, aproximam-se $\partial f/\partial x$ e $\partial f/\partial y$ por diferenças finitas entre pixels vizinhos usando máscaras (kernels) de correlação/convolução:

1. **Operador Cruzado de Roberts ($2\times 2$):** Diferenças diagonais rápidas, porém muito sensíveis a ruído e sem pixel central simétrico:
   $$G_x = \begin{bmatrix} -1 & 0 \\ 0 & 1 \end{bmatrix}, \qquad G_y = \begin{bmatrix} 0 & -1 \\ 1 & 0 \end{bmatrix}$$
2. **Operador de Prewitt ($3\times 3$):** Calcula a diferença central num eixo ($f(x+1)-f(x-1)$) combinada com média aritmética simples $[1,1,1]$ no eixo perpendicular para suavizar ruído:
   $$G_x = \begin{bmatrix} -1 & 0 & 1 \\ -1 & 0 & 1 \\ -1 & 0 & 1 \end{bmatrix}, \qquad G_y = \begin{bmatrix} -1 & -1 & -1 \\ 0 & 0 & 0 \\ 1 & 1 & 1 \end{bmatrix}$$
3. **Operador de Sobel ($3\times 3$ — O mais utilizado na prática!):** Atribui **peso 2** à linha/coluna central ($[1, 2, 1]$, aproximação binomial/gaussiana), proporcionando maior imunidade a ruído e melhor isotropia angular:
   $$G_x = \begin{bmatrix} -1 & 0 & 1 \\ -2 & 0 & 2 \\ -1 & 0 & 1 \end{bmatrix} \text{ (detecta bordas verticais)}, \qquad G_y = \begin{bmatrix} -1 & -2 & -1 \\ 0 & 0 & 0 \\ 1 & 2 & 1 \end{bmatrix} \text{ (detecta bordas horizontais)}$$
   *(Propriedade fundamental: a soma de todos os coeficientes de qualquer máscara derivativa é sempre **zero**, garantindo resposta nula $G_x = G_y = 0$ em regiões de tom constante!)*

#### D) Pipeline Prático de Implementação Computacional (Aplicação na Tarefa #2)
Para implementar corretamente o operador gradiente em código (ex.: Python/NumPy/OpenCV):
1. **Conversão para Tons de Cinza e Suavização Prévia:** Converte-se a imagem RGB para luminância monocromática e, opcionalmente, aplica-se um filtro passa-baixas (Gaussiano/média), pois operadores derivativos amplificam ruído de alta frequência.
2. **Cuidado com Tipos Numéricos com Sinal (`float32` / `int16`):** Ao convoluir a imagem `uint8` ($[0,255]$) com $G_x$ e $G_y$, transições do claro para o escuro geram **valores negativos** (e no Sobel os valores vão de $-1020$ a $+1020$). Se armazenados diretamente em `uint8`, ocorre truncamento/*underflow* e perde-se metade das bordas!
3. **Tratamento das Bordas da Imagem (*Padding*):** Como uma máscara $3\times 3$ precisa de 1 pixel de vizinhança, tratam-se as margens por replicação de borda (*border replicate*), espelhamento ou *zero-padding* (ou restringindo aos pixels internos $1 \le x \le W-2$).
4. **Combinação na Magnitude e Normalização:** Calcula-se $M(x,y) = \sqrt{G_x^2 + G_y^2}$ (ou $|G_x|+|G_y|$) e reescala-se/satura-se o resultado para $[0, 255]$.
5. **Limiarização (*Thresholding*) para Extração de Bordas:** Aplica-se um limiar $T$ sobre $M(x,y)$ ($B(x,y) = 255 \text{ se } M(x,y) \ge T$, senão $0$) para descartar variações suaves/ruído residual e produzir o **mapa binário de bordas** (que pode ser fechado/refinado morfologicamente).

#### E) Equivalente na Morfologia Matemática (Gradiente Morfológico)
Na morfologia matemática, o operador gradiente substitui derivadas lineares por operações não-lineares de ordem sobre um elemento estruturante plano simétrico $b$ (ex.: $3\times 3$):
- **Gradiente Morfológico Simétrico:** $g = (f \oplus b) - (f \ominus b) = \max_{(m,n)\in b} f - \min_{(m,n)\in b} f$
- **Gradiente Interno:** $g_{\text{int}} = f - (f \ominus b)$ | **Gradiente Externo:** $g_{\text{ext}} = (f \oplus b) - f$
- Em regiões constantes $\max = \min \implies g = 0$; nas bordas, $\max - \min$ salta abruptamente, extraindo contornos em todas as direções simultaneamente sem precisar de duas máscaras direcionais.

---

### ✍️ 3. Modelo de Resposta Manuscrita Pronta para a Prova (Questão 2)

> **Resposta Sugerida (Questão 2):**
>
> **1. Fundamentos Conceituais:**  
> Em Processamento de Imagens, bordas correspondem a descontinuidades locais ou transições abruptas nos níveis de cinza $f(x,y)$. O **operador gradiente** fundamenta-se na derivada de primeira ordem bidimensional, definida pelo vetor:
> $$\nabla f(x,y) = \begin{bmatrix} G_x \\ G_y \end{bmatrix} = \begin{bmatrix} \partial f / \partial x \\ \partial f / \partial y \end{bmatrix}$$
> O vetor $\nabla f$ aponta na direção da máxima taxa de variação (crescimento) da intensidade, perpendicular à orientação da borda. Sua **magnitude** $M(x,y) = \|\nabla f\| = \sqrt{G_x^2 + G_y^2}$ (frequentemente aproximada por $|G_x| + |G_y|$) quantifica a intensidade/força da borda (sendo nula em regiões homogêneas e máxima sobre transições), enquanto $\alpha(x,y) = \arctan(G_y/G_x)$ fornece sua direção. *(De forma análoga não-linear, na morfologia matemática define-se o gradiente morfológico por $g = (f \oplus b) - (f \ominus b)$, medindo o salto local entre máximo e mínimo).*
>
> **2. Discretização e Aplicação na Implementação (Detecção de Bordas):**  
> No domínio discreto, as derivadas parciais $G_x$ e $G_y$ são aproximadas por diferenças finitas implementadas via **convolução espacial 2D** com pares de máscaras ortogonais (cuja soma dos pesos é zero), destacando-se Prewitt e **Sobel** ($3\times 3$):
> $$G_x = \begin{bmatrix} -1 & 0 & 1 \\ -2 & 0 & 2 \\ -1 & 0 & 1 \end{bmatrix} \text{ (bordas verticais)}, \qquad G_y = \begin{bmatrix} -1 & -2 & -1 \\ 0 & 0 & 0 \\ 1 & 2 & 1 \end{bmatrix} \text{ (bordas horizontais)}$$
> *(No Sobel, a ponderação $[1,2,1]$ introduz suavização perpendicular à derivada, reduzindo a sensibilidade ao ruído).*
>
> **Etapas práticas da implementação:**
> 1. **Pré-processamento:** Conversão da imagem de entrada para tons de cinza e eventual filtragem passa-baixas (ex.: Gaussiana) para atenuar ruídos de alta frequência que seriam amplificados pela derivação.
> 2. **Cálculo de $G_x$ e $G_y$:** Varredura da imagem aplicando as máscaras horizontais e verticais (com tratamento de fronteira por replicação/*padding*), armazenando os resultados em variáveis com sinal (`float` ou `int16`) para preservar derivadas negativas em transições claro$\to$escuro.
> 3. **Cálculo da Magnitude e Normalização:** Combinação pixel a pixel via $M(x,y) = \sqrt{G_x^2 + G_y^2}$ e reescalonamento para $[0, 255]$.
> 4. **Segmentação de Bordas (Limiarização):** Aplicação de um limiar $T$ ($M(x,y) \ge T$) para suprimir respostas fracas de ruído e gerar o mapa binário final dos contornos dos objetos.

---
---

# 🟢 QUESTÃO 3 (3,0 pontos — 6 itens de 0,5 pt cada)

### Enunciado e Regra Crítica (`p2.jpeg`)
> *"3) (3,0) Determine como Verdadeira (V) ou Falsa (F) cada uma das afirmações abaixo. **Uma marcação errada anula uma certa, responda apenas se estiver convicto**."*

Vamos demonstrar matematicamente e visualmente cada um dos **6 itens (`a` até `f`)**, para que você tenha **100% de convicção** em todos!

---

## ❌ Item 3(a) — GABARITO: `( F )` FALSA

> **Afirmação:** *"a) Considerando o mesmo elemento estruturante, uma erosão desfaz totalmente uma dilatação: A=ero(dil(A,B),B). Bem como, uma dilatação desfaz totalmente uma erosão A=dil(ero(A,B),B)."*

### Por que é FALSA?
1. **Erosão e dilatação NÃO são operações inversas uma da outra!** Conforme enfatizado literalmente no **Slide 18 de `introdProcMorfologico_2026_2.pdf`**, elas são operações **mutuamente duais** em relação à complementação e reflexão ($(A \ominus B)^c = A^c \oplus \hat{B}$), mas **perdem informação geométrica irreversivelmente**.
2. Na verdade, as duas composições citadas definem dois novos operadores morfológicos (filtros não-lineares idempotentes):
   - $\text{ero}(\text{dil}(A,B), B) = A \bullet B$ é o **FECHAMENTO** de $A$ por $B$, que é **extensivo** ($A \subseteq A \bullet B$): ele preenche buracos internos, fendas e concavidades menores que $B$, logo em geral **$A \bullet B \neq A$**!
   - $\text{dil}(\text{ero}(A,B), B) = A \circ B$ é a **ABERTURA** de $A$ por $B$, que é **anti-extensiva** ($A \circ B \subseteq A$): qualquer objeto isolado ou "bico" menor que $B$ é **destruído (vira conjunto vazio $\varnothing$)** na erosão, e dilatar o vazio continua dando vazio ($\varnothing \oplus B = \varnothing \neq A$)!
3. **Contraexemplo imediato:** Se $A = \{(0,0)\}$ (1 pixel isolado) e $B$ é um quadrado $3\times 3$ centrado na origem: $\text{ero}(A,B) = \varnothing \implies \text{dil}(\text{ero}(A,B), B) = \varnothing \neq A$.

---

## ❌ Item 3(b) — GABARITO: `( F )` FALSA

> **Afirmação:** *"b) Os operadores morfológicos são utilizados exclusivamente em imagens binárias."*

### Por que é FALSA?
1. Embora a morfologia matemática costume ser *introduzida* didaticamente em imagens binárias via teoria de conjuntos em $\mathbb{Z}^2$ (Slide 3 de `introdProcMorfologico_2026_2.pdf`), ela é **amplamente estendida e aplicada a imagens em tons de cinza (monocromáticas)** (substituindo união/interseção por $\max$/$\min$ sobre funções em $\mathbb{Z}^3$) e até a imagens coloridas/multiespectrais e grafos!
2. Prova dentro da própria prova: a **Questão 1** (Top-Hat em tons de cinza) e os próprios **itens 3(e) e 3(f)** tratam exatamente de operadores morfológicos em **tons de cinza** (`dil_gray` e `ero_gray`)!

---

## ✅ Item 3(c) — GABARITO: `( V )` VERDADEIRA

> **Afirmação:** *"c) Seja um elemento estruturante $E$ na forma de um disco (centro na origem) e o objeto $X$ composto pela circunferência $A$ e o 'bico' $B$. A $dil(ero(X,E))$ praticamente remove o 'bico' existente no objeto $X$ sem grandes perdas na área da circunferência $A$."*

![Simulação Geométrica Exata 3c e 3d](./figuras/fig02_questao3_cd_geometria_exata.png)

### Demonstração Geométrica Exata (Por que é VERDADEIRA):
1. **Leitura das cotas na figura de 3(c):**
   - Circunferência (disco) $A$: cota entre o centro e a borda esquerda $= 4 \implies$ **Raio $R_A = 4$** (diâmetro $D_A = 8$).
   - "Bico" (protrusão retangular) $B$: cota vertical entre as bordas superior e inferior $= 1 \implies$ **Espessura $h_B = 1$**.
   - Elemento estruturante $E$: disco com origem no centro e cota do centro até a borda direita $= 2 \implies$ **Raio $R_E = 2$** (**diâmetro $D_E = 4$**). *(Nota: mesmo que alguém lesse $2$ como diâmetro, $D_E = 2 > 1$ daria exatamente o mesmo resultado qualitativo!).*
2. **Operação executada:** $\text{dil}(\text{ero}(X, E), E) = X \circ E$ é a **ABERTURA** de $X$ por $E$:
   - **Passo 1 — Erosão $\text{ero}(X, E)$:** Para um ponto sobreviver à erosão, o disco $E$ (diâmetro $4$) precisa caber inteiramente dentro de $X$.
     - No "bico" $B$ (espessura $1$): como $4 > 1$, o disco $E$ **não cabe em nenhum ponto do bico**, eliminando-o totalmente!
     - Na circunferência $A$ (raio $4$): o disco $E$ de raio $2$ cabe perfeitamente dentro de $A$, gerando um disco concêntrico de raio $R_A - R_E = 4 - 2 = 2$ *(com uma minúscula saliência de $\approx 0{,}03$ unidades apenas na boca do bico)*.
   - **Passo 2 — Dilatação $\text{dil}(\text{ero}(X, E), E)$:** Dilatando o disco erodido de raio $2$ novamente pelo disco $E$ de raio $2$, o raio retorna exatamente a $2 + 2 = 4$!
3. **Conclusão:** O bico $B$ desaparece quase por completo (restando apenas uma micro-suavização de filete de $0{,}03$ na raiz onde $B$ tocava $A$ — daí o uso rigoroso da palavra **"praticamente"** pelo professor!) e a circunferência $A$ recupera 100% de seu raio $4$, **sem perdas na área de $A$**! Portanto, **VERDADEIRA (V)**.

---

## ✅ Item 3(d) — GABARITO: `( V )` VERDADEIRA

> **Afirmação:** *"d) Seja um elemento estruturante $E$ na forma de um disco (centro na origem) e o objeto $X$ composto pela circunferência $A$ e uma reentrância $B$. A $ero(dil(X,E))$ praticamente preenche a reentrância existente no objeto $X$ sem grandes aumentos na área da circunferência $A$."*

### Demonstração Geométrica Exata (Por que é VERDADEIRA):
1. **Leitura das cotas na figura de 3(d):** *(Figura adaptada pelo professor a partir do Exercício 2d/2f da lista, substituindo $R=4 \implies R/4 = 1$ e $R/2 = 2$)*:
   - Circunferência $A$: **Raio $= 4$**.
   - Reentrância (fenda horizontal estreita): abertura vertical $= 1$ (menor que o diâmetro do elemento estruturante).
   - Elemento estruturante $E$ (indicado por `B:` na gravura reaproveitada): disco centrado na origem de **Raio $R_E = 2$** (**diâmetro $D_E = 4$**).
2. **Operação executada:** $\text{ero}(\text{dil}(X, E), E) = X \bullet E$ é o **FECHAMENTO** de $X$ por $E$ (rolagem externa do disco $E$):
   - **Passo 1 — Dilatação $\text{dil}(X, E)$:**
     - A parede superior da fenda avança $R_E = 2$ unidades para baixo e a parede inferior avança $R_E = 2$ unidades para cima (capacidade de fechamento $= 2 + 2 = 4 > 1$). Logo, **toda a reentrância é preenchida**!
     - O contorno externo da circunferência $A$ expande-se temporariamente de raio $4$ para raio $4 + 2 = 6$.
   - **Passo 2 — Erosão $\text{ero}(\text{dil}(X, E), E)$:**
     - O contorno externo de raio $6$ é erodido por $R_E = 2$, retornando **exatamente ao raio original $6 - 2 = 4$** (logo, sem aumento na área externa da circunferência $A$!).
     - Já a reentrância, como foi totalmente soldada na dilatação, **permanece preenchida** no interior, restando apenas uma pequeníssima concavidade rasa exatamente na entrada (boca) da fenda onde o disco externo $E$ de raio $2$ encosta nas duas quinas sem conseguir entrar — razão exata pela qual o professor escreve **"praticamente preenche a reentrância... sem grandes aumentos na área da circunferência $A$"**! Portanto, **VERDADEIRA (V)**.

---

## ❌ Item 3(e) — GABARITO: `( F )` FALSA

> **Afirmação:** *"e) A imagem C resultante da dilatação em tons de cinza sobre uma imagem A utilizando o elemento B, corresponde a $C = [dil_{gray}(A,B)](l,c) = max\{A(l-m,c-n)\}$. Podemos afirmar que a dilatação em tons de cinza corresponde a um espalhamento de regiões escuras sobre regiões claras."*

![Perfil 1D Dilatação vs Erosão em Tons de Cinza](./figuras/fig04_cinza_dilatacao_erosao_abertura_fechamento.png)

### Por que é FALSA? (A Pegadinha da Inversão Claro/Escuro!)
1. Na representação padrão de imagens em tons de cinza ($[0, 255]$), **valores menores representam tons escuros** (próximos de $0$, preto) e **valores maiores representam tons claros/brilhantes** (próximos de $255$, branco).
2. A fórmula da dilatação plana $C(l,c) = \max\{A(l-m, c-n)\}$ está correta para um elemento estruturante plano, pois escolhe o **MAIOR valor** na vizinhança.
3. Porém, escolher o **máximo** significa que sempre que a janela de $B$ tocar uma região clara (valor alto) ao lado de uma região escura (valor baixo), o pixel escuro receberá o valor alto! Conforme o **Slide 31 de `introdProcMorfologico_2026_2.pdf`**: *"A dilatação 'clareia' a imagem em tons de cinza"*.
4. Portanto, a dilatação em tons de cinza corresponde a um **espalhamento de regiões CLARAS sobre regiões ESCURAS** (e **NÃO** de escuras sobre claras, como afirmou falsamente o item!).

---

## ❌ Item 3(f) — GABARITO: `( F )` FALSA

> **Afirmação:** *"f) A imagem D resultante da erosão em tons de cinza sobre uma imagem A utilizando o elemento B, corresponde a $D = [ero_{gray}(A,B)](l,c) = min\{A(l-m,c-n)\}$. Podemos afirmar que a erosão em tons de cinza corresponde a um espalhamento de regiões claras sobre regiões escuras."*

### Por que é FALSA? (A Mesma Pegadinha Espelhada!)
1. A erosão em tons de cinza com elemento estruturante plano calcula o **MENOR valor** ($\min$) na vizinhança definida por $B$.
2. Como valores menores são mais **escuros**, qualquer pixel claro vizinho a uma região escura terá seu valor substituído pelo mínimo (escuro). Conforme o **Slide 31 de `introdProcMorfologico_2026_2.pdf`**: *"A erosão 'escurece' a imagem em tons de cinza"*.
3. Portanto, a erosão em tons de cinza corresponde a um **espalhamento de regiões ESCURAS sobre regiões CLARAS** (encolhendo objetos claros e expandindo sombras/vales escuros — exatamente o inverso do que afirma a frase!).

---

## 🎯 Resumo Final do Cartão de Respostas da Questão 3

```text
a) ( F )
b) ( F )
c) ( V )
d) ( V )
e) ( F )
f) ( F )
```
*(Com 6 acertos convictos e 0 erros $\implies$ **3,0 / 3,0 pontos garantidos**!)*
