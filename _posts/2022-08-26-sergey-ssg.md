---
layout: post
title: Usando Sergey SSG
subtitle: La página de las tarjetas de emergencia de Conectiva
thumbnail-img: /assets/img/sergey-ssg.svg
tags: [html, node.js, ssg]
---

«Si alguna has hecho un cambio en cada encabezado en un sitio web totalmente estático, sabrás lo engorroso y propenso a errores que es copiar y pegar los cambios en todos los archivos. Ahí es donde entra en juego Sergey. Sergey le permite mover ese encabezado a un único archivo que se puede importar y te ayuda a incluirlo donde lo necesites.»

Así describe el autor de esta herramienta la funcionalidad de su Generador de Sitios Estáticos. Se trata de una utilidad simple que permite hacer importaciones de código en nuestro HTML empleando node.js para renderizar la página resultante.

![Sergey SSG](/assets/img/sergey-ssg.svg){: .mx-auto.d-block :}

# La página de ejemplo

En esta entrada voy a mostrar un ejemplo práctico. Cómo usar [Sergey SSG](https://sergey.cool/) en la web de una iniciativa sobre tarjetas de emergencia para [enfermedades raras o poco frecuentes](https://es.wikipedia.org/wiki/Enfermedad_rara).

[<button>Ir a la página de las tarjetas de emergencia</button>](https://conectiva.lat/emergencias)

Hay una serie de requisitos funcionales y operativos:

1. Rápida. La página debe cargar lo más rápido posible para que pueda ser accedida incluso en entornos donde hay poco ancho de banda, ya que la información médica que contiene debe estar disponible en cualquier lugar de Latinoamérica.
2. Liviana. Por el mismo motivo, debe pesar poco para un consumo bajo de ancho de banda.
3. Mantenible. La página debe poder actualizarse con rapidez y sin gran esfuerzo.

Se optó por desarrollar la página sin usar ningún CMS ni base de datos de respaldo, empleando sólo HTML + CSS. Para la maquetación se usó Bootstrap, simplificando el desarrollo y su posterior mantenimiento, y las zonas comunes de las páginas web fueron extraídas para ser insertadas como _slots_, en cada página HTML. Esto permite que para hacer cambios en estos contenidos repetidos sólo sea necesario modificar un archivo, y el resto de páginas mostrarán los cambios.

# Sergey SSG

Ya que el propósito de esta entrada no es hacer una revisión de la herramienta, te dejo enlace a un video de [Jonathan MirCha](https://jonmircha.com/) sobre sus características y uso básico paso a paso.

* [LiveCoding Primeros pasos con Sergey un SSG minimalista](https://youtu.be/r5pfaxVCatU)

La instalación y uso de **Sergey SSG** requiere tener instalado **Node.js**.

Ver las indicaciones de instalación en: [https://sergey.cool/](https://sergey.cool/)

# Importaciones

La [página de ejemplo](https://conectiva.lat/emergencias) consta de una página principal o portada y de ella derivan todas las otras páginas estáticas de la web. Todas ellas tienen la misma estructura:

- Inicio, con la información del _head_ de la página.
- Cabecera de la página, con el logo y el título de la página. (En algunos casos contiene más de un logo)
- Cuerpo de la página. Este es el contenido que cambia en cada página.
- Fin, o pie de página.

Inicio, cabecera y fin son los contenidos que se repiten, si bien en ellos, como por ejemplo en el inicio o en la cabecera, hay información específica de cada página, como el título.

Lo que necesito es que cada página web estática contenga el cuerpo de la página, es decir la información propia y específica de esa página, pero que las otras partes de la página que tienen una estructura repetida se inserten en la página estática desde otro archivo. Por alguna razón, HTML no implementa esta característica que si tienen CSS y JavaScript, y aunque en su momento se intentó llevar a cabo, no cuajó. Ver info sobre [Imports HTML](https://www.todojs.com/que-pasa-con-import-y-los-web-components/).

Según parece, hay un estándar en ciernes llamado **HTML módulos** que permitiría importar HTML como si fuesen módulos de Javascript mediante `import`. Para saber más: [HTML Modules Explainer](https://github.com/WICG/webcomponents/blob/gh-pages/proposals/html-modules-explainer.md)

Hasta que se estandarize, Sergey SSG es una razonable alternativa. Otro posible SSG sencillo puede ser [PostHTML Static Site Starter](https://www.telagraphic.com/posthtml).

# Vamos por partes

...dijo el descuartizador de Boston. Este mal chiste da pie a entender cómo vamos a trabajar.

Cada parte susceptible de repetirse en las distintas páginas se sacará a parte, en una carpeta llamada `_imports`, y estos documentos «importables» serán referenciados en cada una de las páginas donde se utilicen.

Para conocer las etiquetas de importación de Sergey SSG, puedes visitar la sección [Slots](https://sergey.cool/slots/) de su página.

Las partes que vamos a reutilizar son:

1. inicio.html
2. cabecera.html
3. cabecera-multiple.html
4. fin.html

![Tarjetas de emergencia](https://conectiva.lat/emergencias/assets/portada1.png){: .mx-auto.d-block :}

## 1. Inicio

Este archivo, como dije, contiene el `head` de la página, y el comienzo del `body`:

```html
<!DOCTYPE html>
<html lang="es" prefix="og: http://ogp.me/ns# website: http://ogp.me/ns/website#">

<head>
    <meta charset="UTF-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1" />
    <title><sergey-slot name="title" /></title>
    <meta name="description" content="<sergey-slot name="description" />" />
    <meta name="author" content="Javier Guerra" />
    <meta name="license" content="CC-BY Conectiva.Lat" />
    <meta name="twitter:card" content="summary_large_image" />
    <meta name="twitter:site" content="@conectiva_lat" />
    <meta property="og:url" content="https://conectiva.lat/emergencias/index.html" />
    <meta property="og:type" content="website" />
    <meta property="og:title" content="Tarjetas de emergencia" />
    <meta property="og:description" content="Descargar las tarjetas de emergencia para Enfermedades Poco Frecuentes." />
    <meta property="og:image" content="https://conectiva.lat/emergencias/assets/banner.png" />
    <meta property="og:image:type" content="image/png" />
    <meta property="og:image:width" content="1024" />
    <meta property="og:image:height" content="512" />
    <meta property="og:image:alt" content="Banner" />
    <meta property="og:locale" content="es_ES" />
    <link rel="manifest" href="https://conectiva.lat/emergencias/manifest.json" />
    <link rel="shortcut icon" href="<sergey-slot name="assets" />assets/favicon.png" />
    <link rel="stylesheet" href="<sergey-slot name="assets" />assets/estilos.css" />
    <sergey-slot />
</head>

<body>
    <div class="container">
```
Como se aprecia, hay una serie de etiquetas `sergey-slot` en este archivo. Para entenderlo mejor, tenemos que ver cómo se inserta este archivo en las páginas.

```html
<sergey-import src="inicio">
    <sergey-template name="title">Tarjetas de Emergencia</sergey-template>
    <sergey-template name="description">Descargar las tarjetas de emergencia para Enfermedades Poco Frecuentes.</sergey-template>
    <script data-goatcounter="https://emergencias.goatcounter.com/count" async src="//gc.zgo.at/count.js"></script>
</sergey-import>
```

Estas son las líneas que uso en, por ejemplo, el fichero index.html para insertar la cabecera de la página.

Vemos un `<sergey-import src="inicio">` que indica cual es el `import` que vamos a insertar, que en este caso se llama `inicio`, y una serie de etiquetas `sergey-template` con información de la cabecera. Esta información será distinta en cada HTML, pero no así el resto de contenido de la cabecera. Con estos `sergey-template` indicamos que datos debe incluir en el `import` genérico donde aparezca una etiqueta `sergey-slot`, consiguiendo de esta forma tener un fichero externo donde podremos hacer cambios de forma sencilla que afectarán a todas las páginas que lo use, pero sin perder la funcionalidad de la personalización de cada página.

Con este _import_ definimos el `title` de la página, `description` y podemos añadir contenido en este caso al final de `head` si lo deseamos, como he hecho yo con el `script`, ya que en el fichero `inicio.html` he colocado un `<sergey-slot />` justo antes de la etiqueta de cierre de la cabecera.

En este caso no incluyo _templates_ para los `"<sergey-slot name="assets" />` de inicio.html porque no los necesito, pero cuando uso este _import_ en archivos en subcarpetas, si debo indicar sus ruta,s pudiendo añadir en este caso:

```html
<sergey-template name="assets">../</sergey-template>
```
para indicar que estamos en una subcarpeta, añadiendo de esta forma `../` al inicio de la ruta de `assets/favicon.png` y `assets/estilos.css`.

Cuando en el fichero de importación incluyo un `<sergey-slot />` pero no le envío contenido, Sergey SSG crea un salto de línea no deseado. Cuando hacemos un import de contenido que está tabulado, Sergey tabula la primera línea del contenido que incluye en `<sergey-slot />` correctamente, pero no hace lo mismo con el resto de líneas, que no conservan el tabulado original. Si eres un obseso de la tabulación de tus líneas de código tienes un problema. He abierto una [_Issue_](https://github.com/trys/sergey/issues/69) para que estas cuestiones puedan ser corregidas en la siguiente versión, si esta llega a verla luz. Por lo demás, e software funciona correctamente y hace su función.

## 2. Cabecera

Como recordamos, la cabecera contiene el logo y el título y subtítulo. El código:

```html
        <header>
            <div class="row">
                <div class="col-sm order-sm-last text-end">
                    <a href="https://conectiva.lat" target="_blank">
                        <img src="<sergey-slot name="assets" />assets/conectiva-logo.svg" width="148" alt="Logo de Conectiva" />
                    </a>
                </div>
                <div class="col-sm">
                    <h1><sergey-slot name="h1" /></h1>
                    <h2><sergey-slot name="h2" /></h2>
                </div>
            </div>
        </header>

        <a href="#contenido" class="visually-hidden-focusable">Ir a los recursos</a>
```

que es llamado desde el HTML con el código:

```html
<sergey-import src="cabecera">
    <sergey-template name="h1">Tarjetas de emergencia</sergey-template>
    <sergey-template name="h2">Enfermedades Poco&nbsp;Frecuentes</sergey-template>
</sergey-import>
```
En este caso se trata del código incluido en el archivo `index.html`. si se tratase de un archivo en una subcarpeta, deberíamos incluir los `_template_` de la ruta del logo. Por ejemplo:

```html
<sergey-import src="cabecera">
    <sergey-template name="h1">Tarjeta de emergencias</sergey-template>
    <sergey-template name="h2">Síndrome de Loeys-Dietz</sergey-template>
    <sergey-template name="assets">../</sergey-template>
</sergey-import>
```
Como en el caso anterior, el _import_ trae el fichero, en este caso `cabecera.html`, y lo inserta en el html rellenando los _slots_ con el contenido especificado en los `template`.

## 3. Cabecera múltiple

En algunas páginas, como ocurre [aquí](https://conectiva.lat/emergencias/acoed/eds/tarjeta.html), la cabeceras incluye dos logos. El código del _import en este caso es:

```html
        <header>
            <div class="row">
                <div class="col-sm order-sm-last text-end">
					<ul class="enLinea">
                        <sergey-slot />
                        <li>
							<a href="https://conectiva.lat" target="_blank">
								<img src="<sergey-slot name="assets" />assets/conectiva-logo.svg" width="148" alt="Logo de Conectiva" />
							</a>
						</li>
					</ul>
                </div>
                <div class="col-sm">
                    <h1><sergey-slot name="h1" /></h1>
                    <h2><sergey-slot name="h2" /></h2>
                </div>
            </div>
        </header>

        <a href="#contenido" class="visually-hidden-focusable">Ir a los recursos</a>
```
que no se diferencia mucho de la cabecera simple, si no es por la inclusión de un `<sergey-slot />` y dela lista ordenada que va a incluir los distintos logos. Este _import_ será llamado desde las páginas con el código:

```html
<sergey-import src="cabecera-multiple">
    <sergey-template name="h1">Tarjeta de emergencias</sergey-template>
    <sergey-template name="h2">Síndrome Ehlers-Danlos</sergey-template>
    <sergey-template name="assets">../../</sergey-template>
    <li>
        <a href="https://www.facebook.com/ACOEDCR" target="_blank">
            <img src="../../assets/acoed.svg" width="150" alt="Logo ACOED" />
        </a>
    </li>
</sergey-import>
```
Como se aprecia, no es necesario seguir el mismo orden de los _slots_ para definir los _template_.

## 4. Fin

En el fichero fin.html defino el pie y el cierre de página:

```html
        <footer>
            <div class="row">
                <div class="nota">
                    <div class="d-flex">
                        <div class="ms-3 me-3">
                            <i class="bi bi-info-circle"></i>
                        </div>
                        <div>
                            <p>
                                <small>Las <a href="https://conectiva.lat/emergencias/index.html">tarjetas de
                                    emergencia</a> son una iniciativa abierta y gratuita del equipo de trabajo de
                                    Emergencias de <a href="https://conectiva.lat" target="_blank">Conectiva</a> para
                                    las personas afectadas por Enfermedades Raras, Huérfanas o Poco Frecuentes.</small>
                            </p>
                        </div>
                    </div>
                </div>
            </div>
        </footer>

    </div>
</body>

</html>
```

En este caso no uso ningún _slot_ por lo que el código que uso para importarlo es muy simple:

```html
<sergey-import src="fin" />
```
# Una página completa

Una vez vista cada importación de cada parte de la página, veamos cómo sería el index.html:

```html
<sergey-import src="inicio">
    <sergey-template name="title">Tarjetas de Emergencia</sergey-template>
    <sergey-template name="description">Descargar las tarjetas de emergencia para Enfermedades Poco Frecuentes.</sergey-template>
    <script data-goatcounter="https://emergencias.goatcounter.com/count" async src="//gc.zgo.at/count.js"></script>
</sergey-import>

<sergey-import src="cabecera">
    <sergey-template name="h1">Tarjetas de emergencia</sergey-template>
    <sergey-template name="h2">Enfermedades Poco&nbsp;Frecuentes</sergey-template>
</sergey-import>


<!-- Aquí el código HTML de nuestra página -->


<sergey-import src="fin" />
```
Este código, al renderizarlo con `npm start`, mostraría lo siguiente:

```html
<!DOCTYPE html>
<html lang="es" prefix="og: http://ogp.me/ns# website: http://ogp.me/ns/website#">

<head>
    <meta charset="UTF-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1" />
    <title>Tarjetas de Emergencia</title>
    <meta name="description" content="Descargar las tarjetas de emergencia para Enfermedades Poco Frecuentes." />
    <meta name="author" content="Javier Guerra" />
    <meta name="license" content="CC-BY Conectiva.Lat" />
    <meta name="twitter:card" content="summary_large_image" />
    <meta name="twitter:site" content="@conectiva_lat" />
    <meta property="og:url" content="https://conectiva.lat/emergencias/index.html" />
    <meta property="og:type" content="website" />
    <meta property="og:title" content="Tarjetas de emergencia" />
    <meta property="og:description" content="Descargar las tarjetas de emergencia para Enfermedades Poco Frecuentes." />
    <meta property="og:image" content="https://conectiva.lat/emergencias/assets/banner.png" />
    <meta property="og:image:type" content="image/png" />
    <meta property="og:image:width" content="1024" />
    <meta property="og:image:height" content="512" />
    <meta property="og:image:alt" content="Banner" />
    <meta property="og:locale" content="es_ES" />
    <link rel="manifest" href="https://conectiva.lat/emergencias/manifest.json" />
    <link rel="shortcut icon" href="assets/favicon.png" />
    <link rel="stylesheet" href="assets/estilos.css" />
    <script data-goatcounter="https://emergencias.goatcounter.com/count" async src="//gc.zgo.at/count.js"></script>
</head>

<body>
    <div class="container">

        <header>
            <div class="row">
                <div class="col-sm order-sm-last text-end">
                    <a href="https://conectiva.lat" target="_blank">
                        <img src="assets/conectiva-logo.svg" width="148" alt="Logo de Conectiva" />
                    </a>
                </div>
                <div class="col-sm">
                    <h1>Tarjetas de emergencia</h1>
                    <h2>Enfermedades Poco&nbsp;Frecuentes</h2>
                </div>
            </div>
        </header>

        <a href="#contenido" class="visually-hidden-focusable">Ir a los recursos</a>


<!-- Aquí el código HTML de nuestra página -->


        <footer>
            <div class="row">
                <div class="nota">
                    <div class="d-flex">
                        <div class="ms-3 me-3">
                            <i class="bi bi-info-circle"></i>
                        </div>
                        <div>
                            <p>
                                <small>Las <a href="https://conectiva.lat/emergencias/index.html">tarjetas de
                                    emergencia</a> son una iniciativa abierta y gratuita del equipo de trabajo de
                                    Emergencias de <a href="https://conectiva.lat" target="_blank">Conectiva</a> para
                                    las personas afectadas por Enfermedades Raras, Huérfanas o Poco Frecuentes.</small>
                            </p>
                        </div>
                    </div>
                </div>
            </div>
        </footer>

    </div>
</body>

</html>
```
Los ficheros renderizados resultantes estarán en la carpeta `public`, y será esta carpeta la que podremos subir a nuestro servidor ya con las páginas estáticas con todo su contenido.

# Más opciones

Sergey SSG tiene un par de opciones más interesantes.

Puede gestionar enlaces con [sergey-link](https://sergey.cool/links/), algo muy útil cuando queremos mostrar a los usuarios dónde se encuentran.

Sergey SSG también puede importar texto en formato Markdown como se indica [aquí](https://sergey.cool/markdown/).

Revisa las distintas [opciones](https://sergey.cool/options/) para ejecutar Sergey SSG.

# Enlaces

* [Ir a la página de las tarjetas de emergencia](https://conectiva.lat/emergencias)
* [Sergey SSG](https://sergey.cool/)
* [Repositorio de Sergey SSG en GitHub](https://github.com/trys/sergey)
* [LiveCoding Primeros pasos con Sergey un SSG minimalista](https://youtu.be/r5pfaxVCatU) - Video de Jonathan MirCha
*
* [PostHTML Static Site Starter](https://www.telagraphic.com/posthtml) - Alternativa




