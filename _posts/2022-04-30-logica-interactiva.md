---
layout: post
title: Lógica interactiva
subtitle: Opciones, eventos, disparadores y estados
thumbnail-img: /assets/img/decisiones.jpg
tags: [código, javascript, dom]
---
![Decisiones](/assets/img/decisiones.jpg){: .mx-auto.d-block :}

Programar es una diversión difícil. En ocasiones las interacciones entre los disparadores de eventos y los estados de las variables y los objetos interelacionados multiplican sus opciones, haciendo complicado establecer de memoria todas las permutaciones posibles para no dejar ninguna de ellas sin implementar en nuestro código.

En esta [página web de ejemplo](https://javguerra.github.io/02-bootcamp-fs-javascript/12-nodos.html) de ejemplo que he preparado tenemos cinco botones de un menú de opciones. Cada opción interactúa con los estados de los otros botones mientras se van añadiendo, quitando o cambiando elementos del DOM, y activando o desactivando los botones según sea necesario en cada momento.

Los disparadores son los botones, y algunos estados del propio programa, como cuando hemos borrado todos los elementos del DOM o cuando añadimos el primer elemento tras ello. Los estados corresponden a los botones ON/OFF, y al valor de las variables que se emplean para llevar a cabo las acciones asignadas a cada botón.

![Decisiones](/assets/img/opciones.png){: .mx-auto.d-block :}

Imaginemos que con el botón «Cambiar» alteramos el contenido del último elemento de la lista de elementos que hemos añadido. Esto hará que el botón «cambiar» se desactive, porque ya no tenemos que cambiar el elemento, y el botón «Limpiar» se active, para limpiar las líneas cambiadas (una hasta ahora). Si borramos seguidamente esta última línea con «Borrar», el botón «Limpiar» deberá desactivarse, y el botón «Cambiar» activarse de nuevo (Si la que ahora es la última línea no fue cambiada anteriormente, o mantenerse desactivada en tal caso).

Tabla «disparadores/estados»
----------------------------
Mediante una tabla en una sencilla hoja de cálculo podemos contabilizar en el eje de las **Y** todos aquellos disparadores (eventos y condiciones), y en el eje de las **X** todos los estados, tanto de las variables como de los objetos (botones u otros), y ver cómo se interelacionan en tiempo de ejecución. Lo dificil entonces será determinar con claridad cuales son los disparadores y los estados. Si lo hacemos bien, con esta herramienta podemos clarificar, sin olvidar nada, cada interacción lógica derivada de las opciones de la aplicación, de forma similar a como se ve en la siguiente imagen.

![Decisiones](/assets/img/tabla-disparadores-estados.png){: .mx-auto.d-block :}

Esta tabla resumida incluye sólo la interacción de estados de los botones, anotando en cada celda el estado de cada botón según los disparadores indicados al principio de cada fila. A la hora de pasar a la fase de codificación, nos será fácil determinar si hemos implementado todas y cada una de las interacciones posibles.

Cuando tengas ante ti un abanico de opciones inabarcables, esta puede ser una buena opción para salir del atolladero.

* Ver el [ejercicio interactivo on-line](https://javguerra.github.io/02-bootcamp-fs-javascript/12-nodos.html).
* Descargar la tabla de decisiones completa del ejercicio en formato [.ods (Open Document)](https://javguerra.github.io/02-bootcamp-fs-javascript/entregas/12-nodos-tabla.ods) o [.xls (Excel)](https://javguerra.github.io/02-bootcamp-fs-javascript/entregas/12-nodos-tabla.xls).

De paso, este ejercicio sirve para mostrar el uso de JavaScript en el manejo de nodos del DOM.