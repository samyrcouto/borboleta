# Dream of Butterfly

Uma animação 3D de borboletas em estilo **holograma wireframe roxo**, programada do zero no navegador, com o texto original do **Sonho da Borboleta de Zhuangzi** sendo digitado em loop.

> *"Não sei se era Zhou sonhando ser uma borboleta, ou uma borboleta sonhando ser Zhou."*

Tudo está em **um único arquivo** (`index.html`), sem instalação, sem build e sem servidor: é só abrir no navegador.

---

# Vizualização
link:https://samyrcouto.github.io/borboleta/

##O que tem no projeto

- **5 borboletas em 3D** (uma grande na frente e 4 menores seguindo o rastro) voando em uma trajetória curva.
- **Visual de esqueleto/holograma:** asas feitas só de veias, anéis, nós brilhantes e um preenchimento translúcido. O corpo tem anéis segmentados, antenas e pernas.
- **Neon suave** com bloom leve, no tom roxo da arte de *Persona: Dream of Butterfly*.
- **Asas que se curvam** ao bater: a ponta atrasa em relação à base, como numa borboleta real.
- **Rastro de partículas** que vai sumindo atrás de cada borboleta, piso em grade e poeira flutuando.
- **Texto filosófico digitado em loop:** o original em chinês, depois a tradução em português. Quando termina, some e recomeça.
- **Interativo:** mexer o mouse (ou o dedo no celular) desloca a câmera de leve.

---

##O texto

O trecho vem do **Zhuangzi** (莊子), capítulo 2, *"Qi Wu Lun"* (齊物論, "Sobre a igualdade das coisas"), texto do filósofo taoísta **Zhuang Zhou**, escrito por volta do século IV a.C. É uma obra de domínio público.

### Original em chinês

> 昔者莊周夢為胡蝶，栩栩然胡蝶也，自喻適志與！不知周也。俄然覺，則蘧蘧然周也。不知周之夢為胡蝶與？胡蝶之夢為周與？周與胡蝶，則必有分矣。此之謂物化。

### Tradução em português

> Certa vez, Zhuang Zhou sonhou que era uma borboleta, voando alegre e satisfeita, sem saber que era Zhou. De repente acordou, e ali estava ele, Zhou em pessoa. Não sabia mais se era Zhou que havia sonhado ser uma borboleta, ou uma borboleta que agora sonhava ser Zhou. Entre Zhou e a borboleta deve haver alguma distinção. A isso se chama a transformação das coisas.

A tradução é livre, feita para manter o sentido e ser fácil de ler. Existem várias traduções com escolhas diferentes.

### Por que borboleta?

A história levanta uma pergunta clássica: como ter certeza do que é real e do que é sonho? O próprio nome do capítulo, *物化* (*wù huà*), "a transformação das coisas", sugere que a fronteira entre as coisas é mais fluida do que parece. A ideia também aparece na cultura pop, por exemplo no subtítulo de *Persona: Dream of Butterfly*, que inspirou a estética roxa e preta deste projeto.

---

## 🛠️ Ferramentas utilizadas

| Ferramenta | Para que serve aqui |
|---|---|
| **HTML5 + CSS3** | Estrutura da página, texto, efeito de scanline e vinheta |
| **JavaScript (vanilla)** | Lógica da animação, trajetórias e o efeito de digitação |
| **[three.js](https://threejs.org/) r128** | Biblioteca 3D sobre WebGL: cena, câmera, geometrias e render |
| **WebGL / GLSL** | Shaders próprios (vertex e fragment) para linhas, pontos e preenchimento das asas |
| **EffectComposer + UnrealBloomPass** | Pós-processamento que cria o brilho neon (bloom) |
| **CDNs (cdnjs e jsDelivr)** | Carregam o three.js e os módulos de pós-processamento sem baixar nada |
| **GitHub Pages** | Hospedagem gratuita da página |

Se o bloom não carregar por algum motivo, a página usa um render simples e continua funcionando.

---

## Como a borboleta é "programada"

A ideia central é trabalhar com **vetores 3D** (`x, y, z`). Cada ponto da borboleta e da trajetória é um vetor, e a animação muda esses vetores a cada frame.

1. **Contorno da asa:** curvas de Bézier cúbicas definem a asa anterior (grande, pontuda) e a posterior (arredondada, com uma "cauda"). Elas são amostradas em vários pontos.
2. **Veias e malha:** do ponto de origem da asa saem linhas (veias) até cada ponto do contorno. Anéis concêntricos ligam as veias entre si, e sub-veias preenchem os vãos.
3. **Cor por posição:** cada vértice guarda um valor de 0 (base) a 1 (borda), e o shader usa isso para fazer o degradê de violeta escuro até lilás claro.
4. **Batimento:** as duas asas giram em torno do corpo com uma senoide (`Math.sin`). A asa esquerda é a direita espelhada.
5. **Curvatura da asa:** no vertex shader, a posição vertical de cada ponto recebe um deslocamento proporcional ao quadrado da distância até o corpo, ligado à velocidade do batimento (`-cos`). Assim a ponta atrasa.
6. **Corpo:** um "tubo" feito de anéis com raios variáveis (cabeça, tórax, abdômen), mais antenas e pernas.
7. **Voo:** uma curva paramétrica (tipo Lissajous) dá a posição, e a borboleta olha sempre para o ponto seguinte da curva. As menores seguem a mesma curva com atraso.
8. **Rastro:** cada borboleta guarda as últimas 70 posições, desenhadas como pontos que vão desaparecendo.
9. **Texto:** uma função assíncrona digita caractere por caractere, com pausas maiores na pontuação. O trecho que ainda não foi digitado fica invisível para reservar o espaço, e por isso nada pula na tela.

---

## Como usar

### Abrir localmente
Baixe o `index.html` e dê dois cliques. Precisa de internet apenas para carregar o three.js pelos CDNs.

### Publicar no GitHub Pages
1. Crie um repositório público no GitHub.
2. Envie o `index.html` (o nome precisa ser exatamente esse).
3. Vá em **Settings → Pages**, escolha **Deploy from a branch**, branch `main` e pasta `/ (root)`, e clique em **Save**.
4. Em 1 ou 2 minutos o link aparece na mesma tela: `https://SEUUSUARIO.github.io/NOMEDOREPOSITORIO/`.

---

##Personalizando

Tudo fica no próprio `index.html`. Alguns pontos para mexer:

| O que mudar | Onde procurar |
|---|---|
| Cores do holograma | função `col` dentro da string `COL` (três cores: base, meio, borda) |
| Força do brilho neon | `new THREE.UnrealBloomPass(..., 0.5, 0.4, 0.25)` (força, raio, limiar). Coloque `0` na força para desligar |
| Quantidade de borboletas | constante `N` |
| Velocidade do batimento | `speed` de cada borboleta |
| Trajetória do voo | função `path` |
| Velocidade da digitação | `typeIn(zhEl, ZH, 170)` e `typeIn(ptEl, PT, 38)`, em milissegundos por caractere |
| Texto exibido | constantes `ZH` (chinês) e `PT` (português) |
| Distância da câmera | `camera.baseZ` na função `resize` |

---

##Observações

- **Desempenho:** em celulares mais antigos o bloom e as partículas podem pesar. Reduzir `N` e o bloom ajuda.
- **Fontes:** o chinês usa as fontes do próprio sistema. Se não aparecer corretamente em algum aparelho, falta uma fonte CJK instalada.
- **Inspiração visual:** o projeto é uma homenagem feita por fã e não tem vínculo com a Atlus nem com a franquia *Persona*. Todos os visuais foram desenhados por código, sem usar nenhum asset do jogo.

---

##Créditos

- Texto: **Zhuang Zhou (莊子)**, *Zhuangzi*, domínio público.
- Inspiração: *Persona: Dream of Butterfly* e a lenda do sonho da borboleta.
- 3D: [three.js](https://threejs.org/).
