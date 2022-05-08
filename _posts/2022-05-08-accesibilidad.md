---
layout: post
title: Accesibilidad Web
subtitle: Buenas prácticas explicadas a través de un ejemplo
tags: [código, HTML, CSS, javascript, accesibilidad, usabilidad]
---

>«El poder de la Web está en su universalidad. El acceso por cualquier persona, independientemente de la discapacidad que presente es un aspecto esencial.»
>  
>_Fuente: [World Wide Web Consortium Launches International Program Office for Web Accessibility Initiative](https://www.w3.org/Press/IPO-announce). [Tim Berners-Lee](https://www.w3.org/People/Berners-Lee/), Director del W3C e inventor de la World Wide Web._

Todos presentamos o presentaremos en algún momento de nuestra vida alguna discapacidad. El envejecimiento de la población acentúa esto, sumando un número destacable de personas que requieren algún tipo de adaptación (visual, auditiva, motriz...) a la hora de acceder a la información, por eso es importante que nuestras web tengan esto en cuenta para poder llegar a cualquier tipo de público.

Con ayuda de la página de ejemplo que preparé y reseñe en [esta entrada]({% post_url 2022-04-23-pagina-de-ejemplo %}), iré describiendo algunas buenas prácticas que suelo emplear en las páginas web que desarrollo.

[<button>Acceder a la página web</button>](https://javguerra.github.io/ramp-up-fswd/index.html)

Comprensión del contexto
------------------------
La navegación web no siempre toma como referencia el diseño visual de la página ni emplea el ratón para ello. Para algunas personas el acceso a la web es posible mediante el uso de programas de lectura de pantalla que traducen la página de arriba a abajo, secuencialmente, a mensajes de voz o a líneas de lectura Braille.

Mi primer objetivo es pues dar a conocer adecuadamente el contexto en el que cada elemento de la página se presenta y describir cada cosa que lo necesite, como la misma página, a través de un ```<title>``` o las imágenes con el atributo ```alt``` (Los iconos, a no ser que tengan alguna función informativa, no requieren de este atributo). Esta es una práctica comúnmente usada por los desarrolladores, afortunadamente. Pero hay más.

**Redes Sociales**  
Cuando compartimos nuestra web en redes sociales, queremos que esto ayude a generar visitas, por lo que es bueno contar con una descripción y una imagen que nos ayude a referenciar adecuadamente qué encontraremos al hacer clic. mediante le protocolo [Open Graph](https://ogp.me/) conseguimos que las redes sociales descubran toda esta información de manera automática al compartir nuestro enlace. Se trata de una serie de indicaciones en el ```head``` de la página con la información necesaria. Pues bien, esto que nace como un recurso para SEO y para conseguir más clics también ayuda las personas con alguna discapacidad a realizar una mejor navegación.

**El lenguaje**  
Hay al menos dos cuestiones de importancia en este apartado:  
1. El contenido debe ser claro, orientado a todo tipo de público, y bien estructurado para que cualquier persona, incluso aquella que presente problemas de comprensión lectora,pueda entenderlo.
2. La página debe contar con el atributo ```lang``` en la etiqueta ```html``` (```lang="es"```) que describe el idioma principal de la página, y todos aquellos textos y enlaces que se presenten en otro idioma deben ir debidamente señalizados con la esta etiqueta también. Ver este ejemplo: ```<span lang="en">front end</span>```. Cuando se escucha una página a través de un sintetizador de voz, es importante que las palabras en otro idioma no generen confusión.

**El color**
Para las personas con baja visión o con afecciones en la percepción de los colores, es importante que los textos y el fondo presenten un contraste adecuado para que sean legibles.

**Ir al contenido**  
Cuando usamos lectores de pantalla se hace tedioso escuchar todo el contenido del ```header```, por lo que ayuda mucho contar con un enlace al inicio de la página que nos lleve directamente al contenido. Algo como esto: ```<a href="#contenido" class="sr">Ir al contenido</a>```. Al hacer clic saltaremos directamente al ```<main>``` de la página, ahorrándonos un tiempo valioso, pero no querremos que este enlace aparezca en nuestro diseño web visual. mediante la clase ```sr``` conseguimos esto. en mi caso, la clase que aplicamos a este enlace contiene lo siguiente: 

```css
.sr {
  position: absolute;
  overflow: hidden;
  text-indent: -9999px;
}
```
Esto hará que la etiqueta exista y pueda ser leída por un lector de pantalla, que no toma en cuenta dónde se ubican visualmente las etiquetas, pero no se mostrará en nuestro diseño, porque estará oculta en ```-9999px```, fuera de la pantalla.

Esta técnica es útil también para incluir comentarios de navegación, información complementaria, incluso, como en el caso del formulario que incluye la web, para mostrar las etiquetas de los campos del formulario que han sido eliminadas del diseño por una cuestión estética.

Por supuesto, la página debe estar adecuadamente estructurada con las etiquetas semánticas correspondientes (```section```, ```article```, etc.) para poder realizar una navegación secuencial correcta.

**Interactividad**  
Al interactuar con la página, botones, enlaces y campos de formulario deben estar adecuadamente identificados, y dotados de un contexto semántico (p.ej. ```label```). Los enlaces deben ser auto-descriptivos, de tal forma que al leer el texto del enlace sepamos que nos depara el hacer clic en él.

No es muy complicado cuando tomamos conciencia de ello, pero existe una traba mayor, los cambios en la página. Cuando hacemos clic en un menú desplegable, la página cambia para mostrarnos una información que hasta ahora no estaba disponible. Cuando introducimos un valor erróneo en un campo de formulario, el mensaje de error que se muestra es una aparición que debe ser notificada, y el foco de donde comenzar a escribir para corregir ese dato debe apuntar al campo erróneo.

Mediante el uso de etiquetas [WAI-ARIA](https://en.wikipedia.org/wiki/WAI-ARIA) podemos describir esos eventos y mostrar sus estados correctamente. Veamos el siguiente ejemplo.

```css
<button id="hamburguesa" aria-controls="menu" aria-label="Botón de menú" aria-expanded="false">
    <span class="barra1"></span>
    <span class="barra2"></span>
    <span class="barra3"></span>
</button>

<nav id="menu">
    <ul>
        <li><a href="#descripcion">Descripción</a></li>
        <li><a href="#perfiles">Perfiles</a></li>
        <li><a href="#tecnologias">Tecnologías</a></li>
        <li><a href="#contactar">Contactar</a></li>
    </ul>
</nav>
```
Esta pieza de código corresponde al menú de la página. El ```<button>``` permite que se despliegue y se repliegue (ON/OFF) el menú del ```<nav>``` cuando nuestro dispositivo tiene una pantalla pequeña (es _responsive_). Así pues, con ```aria-controls="menu"```, establecemos esa relación. Con ```aria-label="Botón de menú"``` aportamos una descripción de la función del botón. Y con ```aria-expanded="false"``` indicamos si el botón esta activado o no. En este caso aparece como ```false```, desactivado, pero esto puede cambiar la hacer clic, y es nuestra responsabilidad actualizar esta etiqueta mediante JavaScript, como se hace en el fichero ```script.js```:

```javascript
const h = document.getElementById("hamburguesa");
h.onclick = hamburguesa;
...
h.setAttribute(
    "aria-expanded",
    h.getAttribute("aria-expanded") === true ? false : true;
);
```

Al hacer click en el botón del menú ('_hamburguesa_' llamado así porque su icono de tres líneas se asemeja a un bocadillo), la etiqueta ```aria-expanded``` cambia su valor de ```false``` a ```true``` o viceversa.

Con todo esto estaremos informando al usuario de un lector de pantalla si el botón está pulsado o no.


**Tablas**  
Las páginas pensadas para su uso por terminales móviles primero (_responsive_) ayudan mucho a clarificar el flujo del contenido de la navegación, pero si hay algo que rompe con todo ello son las tablas. ¿Cuál es el orden correcto para leer una tabla? ¿Fila a fila? ¿Y qué hay de las columnas o filas agrupadas?

Las etiquetas semánticas nuevamente son de ayuda aquí (```<thead>```, ```<tbody>```, ```<tfoot>```, y ```<tr>``` para los encabezados) y algunos atributos de HTML ayudan a paliar esto, como ```scope="colgroup"``` (columnas) y ```scope="rowgroup"``` (filas) que indican que las siguientes filas o columnas forman parte de un grupo.

Como se puede ver en la [tabla](https://javguerra.github.io/ramp-up-fswd/index.html#perfiles) de la web de ejemplo, todas las celdas debajo de la celda de encabezado «_Front end_» o del encabezado «_Back end_» estarían dentro de un _colgroup_ y las celdas al lado de las celdas de encabezado «Desarrollo», «Diseño», «Sistemas», etc. estarían dentro de un ```rowgroup```. Definiendo estos bloques, mejoramos un poco la navegación. Estas etiquetas se suelen aplicar a los elementos ```<th>```.

Conclusiones
------------
Hacer desarrollo web accesible no es más complicado que hacer diseño _responsive_, aunque implica más tiempo de desarrollo.

Contempla lo siguiente:
- Hacer contenido claro, bien estructurado, y definir adecuadamente el contexto de cada cosa que mostremos en la web. Desarrollamos para personas.
- Aportar atajos y ayudas a la navegación si es posible, y sin exagerar.
- Vigilar los eventos interactivos, y señala adecuadamente sus estados.
- Revisar el código con herramientas que nos ayudan a tener una saber si hemos pasado algo por alto, como el [complemento WAVE](https://wave.webaim.org/) para el navegador.

[<button>Acceder a la página web</button>](https://javguerra.github.io/ramp-up-fswd/index.html)

**Enlaces de interés**:
- [Introducción a la accesibilidad web](https://www.w3.org/WAI/fundamentals/accessibility-intro/es) - W3C
- [Accesibilidad Web](https://es.wikipedia.org/wiki/Accesibilidad_web) - Wikipedia
- [Normas de accesibilidad](https://administracionelectronica.gob.es/pae_Home/pae_Estrategias/pae_Accesibilidad/pae_normativa/pae_eInclusion_Normas_Accesibilidad.html) - PAE
- [Unidad de accesibilidad digital](https://web.ua.es/es/accesibilidad/) - Univesidad de Alicante
- [Sergio Luján Mora](https://twitter.com/sergiolujanmora) - Profesor universitario experto
- [Usable y accesible](https://olgacarreras.blogspot.com/) - Consultora freelance 
- [complemento WAVE](https://wave.webaim.org/) - Herramienta
- [WAI-ARIA](https://en.wikipedia.org/wiki/WAI-ARIA) - Estándar