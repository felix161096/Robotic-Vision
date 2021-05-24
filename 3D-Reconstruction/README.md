# 3D Reconstruction
## Introducción
En esta práctica se tiene como objetivo obtener la reconstruccion 3D de un par de imaganes estereo captadas por una cámara. El objetivo es conseguirlo solo mediante el uso de algoritmos de visión computacionla.

## Materiales y métodos.

Para tal fin se ha hecho uso de la herramienta Unibotics, la cual puede encontrarse en https://unibotics.org/. Además, se hizo usó de referencia la guía dispuesta en el siguiente enlace: https://jderobot.github.io/RoboticsAcademy/exercises/ComputerVision/3d_reconstruction. En este último enlace puede obtenerse información sobre las funciones implementadas en el simulador, así como una breve guía de los procedimientos a seguir para obtener un resultado exitoso. En la Figura 1 se muestra una captura de pantalla del simulador, así como de las partes que lo forma. En la parte superior se encuentra el entorno 3D donde se ploteará cada uno de los puntos 3D obtenidos, mientras que en la parte inferior se muestran los elementos a reconstruir.

<figure>
    <img src= 'https://user-images.githubusercontent.com/37750255/119400475-8a0e0880-bcda-11eb-97df-57e0203120fc.png' />
    <font size="2">
    <figcaption> Figura 1. Entorno de simulación y sus componentes. En la parte superior se encuentra el entorno 3D donde se ploteará cada uno de los puntos 3D obtenidos, mientras que en la parte inferior se muestran los elementos a reconstruir.
    </figcaption>
    </font>
    
&nbsp; 

Se dispone de dos imagenes estereo que devuelve la aplicación, una obtenida con la camara izquierda y otra con la cámara derecha. Estas imagenes se encuentran en formato BGR de color. El primer paso a tener en cuenta es saber que es lo que se desea reconstruir. En este caso, serían los monigores visulizados en las imágenes. Sin embargo, una reconstrucción densa de todos los píxeles de la imagen supondría un alto coste computacional, por lo que se a optado por optener unicamente la reconstrucción de los bordes de la imagen. Para ello se ha usado el método de Canny, implementado en  openCV, y se obtuvo lo mostrado en la Figura 2.
    
<figure>
    <img src= 'https://user-images.githubusercontent.com/37750255/119400475-8a0e0880-bcda-11eb-97df-57e0203120fc.png' />
    <font size="2">
    <figcaption> Figura 2. Imagen de bordes obtenido mediante el método de Canny de openCV apricado a las imagenes estereo izquierda y derecha.
    </figcaption>
    </font>
    
&nbsp; 
    
El siguiente punto es la busqueda de las correspondencias entre la camara izquierda y la cámara derecha. Para ello se utilizará de referencia la imagen izquierda. Se buscará un píxel de borde y se guardará sus coordenadas (x, y) en la imagen. Después, mediante el uso de las funciones implementadas en el simulador, se obtiene uno de los puntos pertenecientes a la linea de retroproyeccion de ese punto en la imagen izquierda. Este ultimo es un punto 3D. Para obtener la recta de retropoyeccion de dicho punto hace falta otro, para ello se utiliza la posición 3D de la cámara izquierda. Una vez obtenida la recta de retroproyección, otra vez mediante el uso de las funciones del entorno de simulación, se puede obtener la proyección de los puntos de dicha recta en la imagen derecha. Se consigue así la linea epipolar, cuya intuición se representa en la Figura 3. En esta linea está contenido el punto que se tomó de la imagne izquierda.

<figure>
    <img src= 'https://user-images.githubusercontent.com/37750255/119273807-eb67a600-bc0c-11eb-8458-1c35cd75dddb.png' />
    <font size="2">
    <figcaption> Figura 3. Restricción epipolar. Imagen obtenida de los apuntes de la asignatura de Visión Robótica, J.M. Cañas, Universidad Rey Juan Carlos.
    </figcaption>
    </font>
    
&nbsp; 
    
Una vez se sabe la localización de los puntos del espacio donde un punto de la imagen izquierda puede estar en la imagen derecha, es necesario conocer su localización exacta en la imagen derecha. El objetivo es buscar la correspondencia de los puntos entre ambas imagenes. Los pasos ha seguir para conseguirlo han sido:
- Pasar la imagen de formado BGR a HSV, para mejorar la busqueda de correspondencias se usará el Hue.
- Fijar un umbral en píxeles alrededor de la línea epipolar poryectada en la imagen derecha. 
- Obtencion de un parche de kxk píxeles alrededor del punto de la imagen izquierda.
- Recorrer el umbral de la imagen derecha, siguiendo la epipolar proyectada, obtenindo una medida de "similaritud" entre parches.
- Obtener la posición en que es máxima la similaritud entre píxeles.
Como medida de similaritud se ha utilizado la suma de diferencias al cuadrado entre píxeles (SSD):
    
<p align="center">
<img src="https://render.githubusercontent.com/render/math?math=SSD = \sum (pixel_{der} - pixel_{izq})^{2}">
</p>

En la Figura 4 se ve un ejemplo de un punto al que se le ha obtenido su correspondencia. Se ha representado la proyección de la linea epipolar. 
    
<figure>
<img src= 'https://user-images.githubusercontent.com/37750255/119274637-3b486c00-bc11-11eb-8995-4c2b7fcceab6.png' />
<font size="2">
<figcaption> Figura 4. Correspondencia entre puntos en las imagenes estereo. Se ha representado a su vez la linea epipolar sobre la imagen derecha.
</figcaption>
</font>
    
&nbsp; 

Una vez obtenida las correspondencias, el siguiente paso es obtener el punto de mínima distancia en el que las lineas se cortan o se cruzan en el espacio tridimensional. Las lineas son aquellas formadas por el centro de la imagen y el obtenido con el píxel (usando las funciones del simulador) para ambas imagenes. Existen diferentes maneras de encontrar una solucion, las cuales van desde una solución geométrica hasta una optimización por mínimos cuadrados. Para este caso se ha implementado la solución de optimización. El punto buscado es aquel representado en la Figura 5. Se observa que es el punto medio donde la distancia entre ambas curvas se hace mínima.
    
<figure>
    <img src= 'https://user-images.githubusercontent.com/37750255/119274417-08ea3f00-bc10-11eb-8c3b-659081288969.png' />
    <font size="2">
    <figcaption> Figura 5. Punto medio de la recta que uno los puntos de mínima distancia de dos rectas que se cruzan en el espacio. Imagen obtenida de los apuntes de la asignatura de Visión Robótica, J.M. Cañas, Universidad Rey Juan Carlos.
    </figcaption>
    </font>

&nbsp; 

El último paso es dibujar los puntos obtenidos en el simulador. Para ello se usaron de nuevo las funciones que incorpora el entorno. Para dotar de color a los puntos y mejorar su visualización, a cada punto se le asignó el mismo color el cual tenia la imagen izquierda de partida.
    
    
## Resultados
**24/05/2021 10:27** Reconstrucción 3D con k = 11 (tamaño del umbral entre parches). En esta simulación se ha recorrido toda la linea de proyeccion epipolar para buscar correspondencias en la imagen de la derecha. Además, no se ha restringido la distancia máxima a la cual las dos rectas de retroproyección pueden cortarse. Puede observarse puntos que no han logrado una buena correspondencia, aunque se ha conseguido la reconstrucción exitosa de todo el par estereo.

<figure>
    <img src= 'https://user-images.githubusercontent.com/37750255/119319937-4d5ef480-bc7b-11eb-885a-58c7c9b42049.png' />
    <font size="2">
    <figcaption> Resultado 24/05/2021 10:27.
    </figcaption>
    </font>

&nbsp;     

[![IMAGE ALT TEXT HERE](https://user-images.githubusercontent.com/37750255/119319937-4d5ef480-bc7b-11eb-885a-58c7c9b42049.png)](https://user-images.githubusercontent.com/37750255/119325521-3cb17d00-bc81-11eb-8d06-a72ae35a868c.mp4)

**24/05/2021 XXX** Reconstrucción 3D con k = 21 (tamaño del umbral entre parches). En esta simulación se ha recorrido toda la linea de proyeccion epipolar para buscar correspondencias en la imagen de la derecha. **MEJORAS:** En esta versión se ha restringido la distancia máxima en el punto de de cruce entre las rectas de retroproyección de los puntos correspondientes de la imagen izquierda y derecha. Se rechazan y no se representan todos aquellas dintacias en el cruce que superan cierto umbral. Esto último, unido al aumento de k, han sido los potenciadores de una mejor actución del sistema de recontrucción 3D.
    


    
    
    
