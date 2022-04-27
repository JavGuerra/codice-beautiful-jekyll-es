---
layout: post
title: wysiwyg.css
subtitle: Un pequeño CSS para el contenido HTML o Markdown con una única clase
thumbnail-img: https://raw.githubusercontent.com/jgthms/wysiwyg.css/master/docs/wysiwyg.png
tags: [css, html, marco]
---

![WYSIWYG](https://raw.githubusercontent.com/jgthms/wysiwyg.css/master/docs/wysiwyg.png){: .mx-auto.d-block :}

En ocasiones debemos presentar pequeñas páginas web a las que no merece la pena dedicarles tiempo de maquetación con CSS, como páginas para mostrar ejemplos, ejercicios, etc. pero a las que queremos darle una presentación básica que nos muestre el contenido algo más pulido que con los estilos por defecto del navegador.

[wysiwyg.css](https://jgthms.com/wysiwyg.css/) de **Jeremy Thomas** es un fichero **.css** con una sola clase llamada «**.wysiwyg**» que lo hace por nosotros. Su uso es sencillo, descargamos el fichero **wysiwyg.css** de la página del proyecto, lo enlazamos en el head de nuestra página, y por último creamos un div que envuelva todo aquel contenido de la página que queramos que se vea afectado por los cambios de estilo de la clase «.wysiwyg», o incluso toda la página, de principio a fin.

```html
<body>
    <div class="wysiwyg">
        <!-- Mi HTML -->
    </div>
</body>
```
La clase .wysiwyg define estilos para cabeceras, párrafos, listas, tablas, bloques de código... Al aplicar esta clase, todo el contenido dentro del div se mostrará con los estilos definidos en la clase «.wysiwyg». La página de este proyecto es un ejemplo de uso de esta clase.

el [proyecto en Github](https://github.com/jgthms/wysiwyg.css) también permite descargarnos un fichero **.scss** sobre el que realizar cambios en los valores por defecto de la clase .wysiwyg antes de transpilarlo a .css.

Algunas cosas que podemos cambiar en el .scss son:
* Variables que guardan los colores por defecto
* Espaciado
* Tipografía
* Otros (Todas las opciones de personalización están deshabilitadas inicialmente).

Ir a la página de [wysiwyg.css](https://jgthms.com/wysiwyg.css/) para más información.