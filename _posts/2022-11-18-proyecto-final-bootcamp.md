---
layout: post
title: Tienda 8 bits
subtitle: Proyecto de graduación del Bootcamp Full Stack Web Developer
thumbnail-img: https://raw.githubusercontent.com/JavGuerra/final-fswd/main/frontend/public/assets/img/banner.png
tags: [formación, beca, node.js, express, react, mongodb, javascript]
---

Durante este año 2022 he cursado el [Bootcamp de Desarrollo Web Full Stack]({% post_url 2022-04-23-beca-santander-fswd %}) que [Becas Santander](https://www.becas-santander.com/es/index.html) llevó a cabo en colaboración con [Fundación Universia](https://jobs.universia.net/), [Indra](https://www.indracompany.com/) y [The Bridge](https://www.thebridge.tech/).

![Tienda 8 bits](https://raw.githubusercontent.com/JavGuerra/final-fswd/main/frontend/public/assets/img/banner.png){: .mx-auto.d-block :}

[Página del proyecto en GitHub](https://github.com/JavGuerra/final-fswd)

El pasado día 17 de noviembre finalizó la formación con un acto de entrega de diplomas on-line en el que tuve ocasión de mostrar mi proyecto final «Tienda 8 bits» junto con Alex, otro de mis compañeros, que también expuso su proyecto.

A parte de los compañeros y los profesores, asistieron al evento Alejandra Cuenca y Diego Díaz por parte de The Bridge, Cristina Soriano por Minsait/Indra, y Beatriz Arribas y Carlos Sánchez por Universia.

# Video

El evento se llevó a cabo de forma híbrida, presencial y on-line, y de él he rescatado el video de mi presentación que comparto a continuación.

![Video de presentación](/assets/img/tienda.png){: .mx-auto.d-block :}

[<button>Ver el video en YouTube</button>](https://youtu.be/XlswjqhxJZ8)

# Descripción técnica

## Back end

__Tecnologías empleadas__: Node + Express + Mongodb + Mongoose  
__Otros módulos__: compression, cors, dotenv, nodemon, mongoose-paginator-v2

__Características destacadas__:
* Emplea módulos ESM. 
* Está preparada para el versionado de la API.  
* Está preparada para el uso de BBDD MongoDB local o en remoto (config.js).
* Devuelve las rutas de los enlaces a las fotos y logos dinámicamente.  
* Cuenta con documentación de la API.  

## Front end

__Tecnologías empleadas__: React + Vite, axios, react-router-dom, react-hook-form  
__Otros módulos propios__: setSpin, inactiveBtn

__Características destacadas__:
* El diseño es adaptable (responsive) según el dispositivo.  
* CSS propio. No usa frameworks.  
* Es accesible (revisado con el complemento [WAVE](https://wave.webaim.org/)).   
* Utiliza fuentes de Google Font en los títulos para preservar la accesibilidad.  
* Implementa el protocolo [Open Graph](https://ogp.me/) para la correcta inserción de la web en RRSS.  
* Es compatible con Progessive Web Aplication ([PWA](https://developer.mozilla.org/es/docs/Web/Progressive_web_apps)) a través de fichero [manifest.json](https://developer.mozilla.org/es/docs/Web/Manifest), por lo que la página puede ser instalada en dispositivos móviles como una web app.  
* Ha sido probada con los navegadores web Firefox y Chrome.  
* Tratamiento de imágenes con software libre: Gimp e InkScape.  

## Fuentes

* Wikipedia  
* Enciclopedia «Mi Computer», editorial Delta, 1984.  

## Licencias

Sobre el código fuente: GNU GENERAL PUBLIC LICENSE Version 3  
Sobre el contenido de la web:(CC) BY-SA 3.0  
Sobre las fotos: Son propiedad de sus respectivos autores.  
Material usado sin ánimo de lucro bajo criterios didácticos y formativos.  

## Enlaces

- [Página del proyecto en GitHub](https://github.com/JavGuerra/final-fswd). Ver enunciado y presentación en pdf.  
- [Video de la presentación en YouTube](https://youtu.be/XlswjqhxJZ8)  

Artículos informativos relacionados con este proyecto:  

- [Usando populate con paginate y filtrando los resultados]({% post_url 2022-10-29-populate-paginate-fitrado %})  
- [Generar enlaces dinámicos de imágenes almacenadas en MongoDB]({% post_url 2022-11-06-rutas-dinamicas-imagenes-mongodb %})  
