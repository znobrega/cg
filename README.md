# Rasterização de pontos e linhas 

## Introdução

<p>O objetivo deste trabalho é desenvolver um maior entendimento sobre algoritmos de rasterização de pontos e linhas. O foco foi entender e desenvolver funções implementando os conceitos aprendidos na disciplina Introdução à computação gráfica, utilizando o framework cedido pelo professor Christian Azambuja.</p>
<p>A seguir, serão discutidas as funções PutPixel, DrawLine e DrawTriangle.</p>

### PutPixel(tPixel pixel, tColor color)

<p>Uma imagem é formada por um conjunto de pixels, então o primeiro passo era conseguir rasterizar um pixel na tela. Como alterações à memória de vídeo são restritas pelo sistema operacional, o acesso se deu por uma simulação pelo ponteiro FBptr, através do framework desenvolvido pelo Professor.</p>

```c
typedef struct {
	int red;
	int green;
	int blue;
	int alpha;
} tColor;

typedef struct {
	int x;
	int y;
} tPixel;

```

```c
void PutPixel(tPixel pixel, tColor color) {
	int c = 4*pixel.x + 4*pixel.y*IMAGE_WIDTH;

	FBptr[c] = color.red;
	FBptr[c + 1] = color.green;
	FBptr[c + 2] = color.blue;
	FBptr[c + 3] = color.alpha;
};
```

<h5>PutPixel({250, 250}, {255, 0, 0, 255});</h5>
<p align="center">
<img alt="sing" src="./prints/single.png"/><br>
</p>

<h5>({250, 250}, {255, 0, 0, 255}), ({50, 250}, {255, 0, 0, 255}), ({10, 150}, {255, 0, 0, 255})</h5>
<p align="center">
<img alt="tres" src="./prints/three.png"/>	
</p>
	
### DrawLine(tPixel init, tPixel end, tColor color1, tColor2)

</p>Para a rasterização de linhas foi usado o algoritmo de Bresenham. Famoso por sua eficiência em desenhar linhas finas, já que o mesmo utiliza somente operações aritméticas simples, o algoritmo de Bresenham escolhe o pixel a ser desenhado por aproximação de uma linha que passa entre dois pontos, com isso, ele consegue desenhar apenas 1 pixel por coluna. É bastante empregado para desenhar primitivas.</p>
<p>A função foi desenvolvida a partir do algoritmo de ponto médio, apresentado nas notas de aula do professor Christian.</p>

<p align="center">
<img alt="midpoint" src="./prints/algoritmo.png" />
</p>

<p>Dividindo o plano cartesiano em 8 fatias, cada uma com 45 graus de variação, é possível encontrar padrões para as linhas desenhadas em cada fatia. As fatias são chamadas de octantes.
</p>
<p>O código acima é referente a primeira octante, então uma implementação para as demais octantes se fez necessária. 
O desenvolvimento foi feito seguindo os próximos conceitos.</p>
	
<p align="center">
<img alt="octantes" src="./prints/cords.png"/>
</p>
<p align="center" style="color:gray;">Octantes e padrão para as linhas em cada octante</p>
<p>
x0 = x inicial<br>
x1 = x final<br>
y0 = y inicial<br>
y1 = y final </p>

>primeiro octante: o coeficiente angular varia entre 0 e 1 (0 <= m <= 1) e x0 < x1<br>
segundo octante: o coeficiente angular é maior do que 1 (m > 1) e y0 < y1<br>
terceiro octante:  o coeficiente angular é menor do que -1 (m < -1) e y0 < y1<br>
quarto octante: o coeficiente angular varia entre 0 e -1 (0 >= m >= -1) e x1 < x0<br>
quinto octante: o coeficiente angular varia entre 0 e 1 (0 <= m <= 1)  e x1 < x0<br>
sexto octante: o coeficiente angular é maior do que 1 (m > 1) e y1 < y0<br>
sétimo octante: o coeficiente angular é menor do que -1 (m < -1) e y1 < y0<br>
oitavo octante:  o coeficiente angular varia entre 0 e -1 (0 >= m >= -1) e x0 < x1<br>

<h5>({0, 512}, {512, 0}, {255, 0, 0, 255}, {0, 255, 0, 255})</h5>
<p align="center">
<img alt="lineinter" src="./prints/line.png"/>
</p>


### DrawTriangle(tPixel v1, tPixel v2, tPixel v3, tColor c1, tColor c2, tColor c3)

<p>Após o processo de criação da função para rasterização de linhas, o desenvolvimento de um triângulo torna-se bastante simples. É necessário apenas a passagem de coordenadas para o desenho de três linhas com alguns vértices coincidentes.</p>

```c
void DrawTriangle(tPixel v1, tPixel v2, tPixel, v3, tColor c1, tColor, c2, tColor c3) {
        DrawLine({100, 390}, {256, 150}, {255, 0, 0, 255}, {0, 0, 255, 255});	
	DrawLine({256, 150}, {350, 390}, {0, 0, 255, 255}, {0, 255, 0, 255});
	DrawLine({100, 390}, {350, 390}, {255, 0, 0, 255}, {0, 255, 0, 255});
}

```

<p align="center">
<img alt="triang" src="./prints/triangle.png"/>
</p>

<br>

### Dificuldades
<p>Certamente, a maior dificuldade do trabalho foi concluir a função DrawLine. Como o algoritmo funciona por octantes, ou seja, existem 8 variações para a implementá-lo. Encontrar todas as relações matemáticas para todas as octantes, além de implementá-las foi algo bastante trabalhoso, mas foi um ótimo exercício.</p>

### Possíveis melhoras
<p>
O algoritmo está desenhando pixels e linhas, com isso é possível desenhar as primitivas mais básicas, que possuam vértices. Porém, o desenho de um circulo é um pouco limitado, talvez seja necessário a implementação de um algoritmo separadamente para criar um. 
Tentei criar um círculo preenchido fazendo linhas de tamanho igual a partir de um vértice, e mudando o ângulo das linhas para que criasse a primitiva de 360º, mas o efeito não ficou fluído.
</p>
<p>
Outra melhoria é a suavização das linhas que possuem uma angulação, já que elas sempre ficam com serrilhamento. Como as linhas são bem finas, o efeito visual não fica muito agradável.  Gostaria de estudar como posso amenizar esse efeito.
</p>
<p>
Além disso, no exemplo que o Prof. Christian mostrou em sala, usando openGL, era possível fazer um preenchimento da primitiva com as cores que se encontravam nos vértices. Não implementei o algoritmo para tal, mas com certeza é algo interessante para ser feito.
</p>

### Referências 

<ul>
	<li>http://matheuspraxedescg.blogspot.com/2016/08/trabalho-1-rasterizacao-de-ponto-e-linha.html</li>
	<li>https://medium.com/@biancaamoriim/rasteriza%C3%A7%C3%A3o-de-primitivas-em-opengl-8680a76fdda5</li>
	<li>https://en.wikipedia.org/wiki/Rasterisation</li>
	<li>Slides do professor Christian Azambuja</li>
</ul>

### Autor
<p>José Carlos Alves da Nóbrega Júnior</p>

