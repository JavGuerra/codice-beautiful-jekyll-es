---
layout: post
title: Un simple efecto Parallax
subtitle: Efectos con CSS
tags: [código, HTML, CSS]
---

Parallax es un efecto que se aplica generalmente a las imágenes de fondo de una página web, y que consiste en mantener estático, o mover a una velocidad distinta, el resto de los elementos de la página al hacer scroll. Pareceŕa de esa forma que la página tiene profundidad. En la [página de ejemplo](https://javguerra.github.io/ramp-up-fswd/index.html) a la que hacía referencia en este [post]({% post_url 2022-04-23-pagina-de-ejemplo %}), puede verse aplicado en la portada o zona «Hero».

esto se consigue de una forma muy sencilla. Sólo tienes que crear un div en el html y ponerle una clase, p. ej. «.parallax», y luego, en el CSS, en .parallax, incluir la altura que le darás al div y la imagen de fondo que le vas a poner. Por supuesto, en el HTML de ese div puedes incluir texto, imágenes... En el caso del ejemplo mencionado puse el título de la página.

```css
min-height: 100vh;
background: url("fondo.jpg") center/cover no-repeat fixed;
```

Estas son las dos líneas que he incluido en mi código para lograrlo. La primera le dice al navegador que el div va a tener la altura de la ventana del navegador, sea esta cual sea. Pero también se puede poner un valor fijo, por ejemplo 'height: 200px'.

La segunda le dice al navegador que el div va a tener una imagen de fondo, en este caso fondo.jpg, y que la tiene que poner centrada vertical y horizontalmente, ocupando todo el ancho del div, y poniéndola fija (fixed) para que no se mueva. Este es el efecto que buscamos. El fixed lo hace todo.

Al hacer scroll, el fondo permanecerá estático, y la página lo irá tapando a medida que hacemos scroll hacia abajo.

En esta otra [página _landing_](https://badared.com/conectiva/evento/THTC2021/) que realicé para un evento, puede verse un uso parecido de este efecto a modo de cortinilla o separador de las distintas secciones.

Este efecto tiene un inconveniente, y es que no funciona bien en el navegador Safari en iOS (iPhone), como puede verse [aquí](https://css-tricks.com/ios-13-broke-the-classic-pure-css-parallax-technique/), aunque es posible encontrar soluciones más o menos imaginativas.

Mis páginas de ejemplo de este efecto Parallax:

* [página de ejemplo](https://javguerra.github.io/ramp-up-fswd/index.html) - [post]({% post_url 2022-04-23-pagina-de-ejemplo %})
* [página _landing_](https://badared.com/conectiva/evento/THTC2021/)