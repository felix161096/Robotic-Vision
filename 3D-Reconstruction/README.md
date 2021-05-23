# 3D Reconstruction
## Introducción
En esta práctica se tiene como objetivo obtener la reconstruccion 3D de un par de imaganes estereo captadas por una cámara. El objetivo es conseguirlo solo mediante el uso de algoritmos de visión computacionla.

## Materiales y métodos.

Para tal fin se ha hecho uso de la herramienta Unibotics, la cual puede encontrarse en https://unibotics.org/. Además, se hizo usó de referencia la guía dispuesta en el siguiente enlace: https://jderobot.github.io/RoboticsAcademy/exercises/ComputerVision/3d_reconstruction. En este último enlace puede obtenerse información sobre las funciones implementadas en el simulador, así como una breve guía de los procedimientos a seguir para obtener un resultado exitoso. En la Figura 1 se muestra una captura de pantalla del simulador, así como de las partes que lo forma. En la parte superior se encuentra el entorno 3D donde se ploteará cada uno de los puntos 3D obtenidos, mientras que en la parte inferior se muestran los elementos a reconstruir.

FIGURA 1: Entorno de simulación y sus componentes. En la parte superior se encuentra el entorno 3D donde se ploteará cada uno de los puntos 3D obtenidos, mientras que en la parte inferior se muestran los elementos a reconstruir.

Se dispone de dos imagenes estereo que devuelve la aplicación, una obtenida con la camara izquierda y otra con la cámara derecha. Estas imagenes se encuentran en formato BGR de color. El primer paso a tener en cuenta es saber que es lo que se desea reconstruir. En este caso, serían los monigores visulizados en las imágenes. Sin embargo, una reconstrucción densa de todos los píxeles de la imagen supondría un alto coste computacional, por lo que se a optado por optener unicamente la reconstrucción de los bordes de la imagen. Para ello se ha usado el método de Canny, implementado en  openCV, y se obtuvo lo mostrado en la Figura 2.

Figura 2. Imagen de bordes obtenido mediante el método de Canny de openCV apricado a las imagenes estereo izquierda y derecha.

El siguiente punto es la busqueda de las correspondencias entre la camara izquierda y la cámara derecha. Para ello se utilizará de referencia la imagen izquierda. Se buscará un píxel de borde y se guardará sus coordenadas (x, y) en la imagen. Después, mediante el uso de las funciones implementadas en el simulador, se obtiene uno de los puntos pertenecientes a la linea de retroproyeccion de ese punto en la imagen izquierda. Este ultimo es un punto 3D. Para obtener la recta de retropoyeccion de dicho punto hace falta otro, para ello se utiliza la posición 3D de la cámara izquierda. Una vez obtenida la recta de retroproyección, otra vez mediante el uso de las funciones del entorno de simulación, se puede obtener la proyección de los puntos de dicha recta en la imagen derecha. Se consigue así la linea epipolar, cuya intuición se representa en la Figura 3. En esta linea está contenido el punto que se tomó de la imagne izquierda.

<figure>
    <img src= 'https://user-images.githubusercontent.com/37750255/119273807-eb67a600-bc0c-11eb-8458-1c35cd75dddb.png' />
    <font size="2">
    <figcaption> Figure 3. Restricción epipolar. Imagen obtenida de los apuntes de la asignatura de Visión Robótica, J.M. Cañas, Universidad Rey Juan Carlos.
    </figcaption>
    </font>

&nbsp; 
  
Una vez se sabe la localización de los puntos del espacio donde un punto de la imagen izquierda puede estar en la imagen derecha, es necesario conocer su localización exacta en la imagen derecha. El objetivo es buscar la correspondencia de los puntos entre ambas imagenes. Para ello se ha usado un umbral



