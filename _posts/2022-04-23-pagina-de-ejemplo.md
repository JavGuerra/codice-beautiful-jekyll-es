---
layout: post
title: Una página de ejemplo
subtitle: Conceptos Full Stack Web Developer
cover-img: https://javguerra.github.io/ramp-up-fswd/img/fondo.jpg
gh-repo:  JavGuerra/ramp-up-fswd 
gh-badge: [star, fork, follow]
tags: [web, código, html, css, javascript]
comments: true
---
# Conceptos FSWD

__Práctica final del Ramp-up del Bootcamp de FSWD__  
Repositorio en GitHub: [ JavGuerra / ramp-up-fswd](https://github.com/JavGuerra/ramp-up-fswd)  
Por: __Javier Guerra__ 

## La página


[<button>Acceder a la página web</button>](https://javguerra.github.io/ramp-up-fswd/index.html)

Esta página corresponde a la práctica final del ramp-up del [bootcamp sobre Full Stack Web Developer]({% post_url 2022-04-23-beca-santander-fswd %}) que estoy realizando en este momento.

En ella se muestran algunos conceptos aprendidos en esta primera fase, e incluye «algo más» de mi propia cosecha. A continuación algunas de estas mejoras.

## 14 características destacables de esta página web

- CSS transpilado con [Sass](https://es.wikipedia.org/wiki/Sass).
- Usa JavaScript puro ([Vanilla JS](https://www.ardepizando.com/que-es-vanilla-js/)).
- La web implementa los criterios del estándar XHTML (revisado con [Nu Html Checker](https://html5.validator.nu/)).
- El diseño es adaptable ([_responsive_](https://es.wikipedia.org/wiki/Dise%C3%B1o_web_adaptable)) según el dispositivo.
- Usa la librería y los iconos de [Bootstrap Icons](https://icons.getbootstrap.com/) y las fuentes de letra de [Google Fonts](https://fonts.google.com/).
- Aplica un [efecto Paralax](https://blog.hubspot.es/marketing/efecto-parallax) a la portada (_hero_).
- Implementa el protocolo [Open Graph](https://ogp.me/) (_head_) para la correcta inserción de la web en redes sociales.
- Es compatible con _Progessive Web Aplication_ ([PWA](https://developer.mozilla.org/es/docs/Web/Progressive_web_apps)) a través de fichero [manifest.json](https://developer.mozilla.org/es/docs/Web/Manifest), por lo que la página puede ser instalada en dispositivos móviles como una web app.
- Implementa técnicas de seguridad en el formulario:
    - Hace uso de un [_Honeypod_](https://es.wikipedia.org/wiki/Honeypot), mediante el campo "hp", para evitar el SPAM.
    - Hace uso de un campo oculto "dato" para evitar ataques por [XSS](https://es.wikipedia.org/wiki/Cross-site_scripting) y [CRSF](https://es.wikipedia.org/wiki/Cross-site_request_forgery).
- Es accesible (revisado con el complemento [WAVE](https://wave.webaim.org/)):
    - Emplea párrafos y etiquetas contextuales sólo visibles para lectores de pantalla.
    - Hace uso de etiquetas [WAI-ARIA](https://en.wikipedia.org/wiki/WAI-ARIA) para describir eventos interactivos.
- El fichero README.md tiene sintáxis de marcado ligero [Markdown](https://es.wikipedia.org/wiki/Markdown).
- Dispone de un código QR para acceso rápido a la web a través de dispositivos móviles.
- Ha sido probado en los navegadores web __Firefox__ y __Chrome__.
- Web alojada en [Github](https://github.com/JavGuerra/ramp-up-fswd) pages.

## Acceder a la página web

[<button>Acceder a la página web</button>](https://javguerra.github.io/ramp-up-fswd/index.html) de la práctica.

![icono](https://javguerra.github.io/ramp-up-fswd/img/qrcode.svg)
