---
layout: post
title: Cartelera de cine
subtitle: Práctica final del curso de Java + Spring
thumbnail-img: https://raw.githubusercontent.com/JavGuerra/cartelera-java-spring/a77e7c8dded2eaa8235d92814427e56a628ca465/src/main/resources/static/img/logo.svg
tags: [java, spring, bootstrap, tymeleaft, security]
---

Durante los meses de enero y febrero, y hasta el 9 de marzo de 2023, he participado en un curso de especialización en Java + Spring de la Fundación Adecco dentro del Proyecto Digital School Erasmus+

![Logo cartelera](https://raw.githubusercontent.com/JavGuerra/cartelera-java-spring/a77e7c8dded2eaa8235d92814427e56a628ca465/src/main/resources/static/img/logo.svg){: .mx-auto.d-block :}

## La práctica

Empleando **Java** y el framework **Spring**, se ha llevado a cabo la planificación e implementación de una aplicación web que permite a los usuarios listar cines, salas de cine y sus películas asociadas, y capacita a los usuarios registrados para modificar, mediante formularios, cada una de las entidades mencionadas.

En su desarrollo se ha empleado **Spring security**, **Tymeleaft** y **Bootstrap**. Se ha codificado usando **IntelliJ IDEA**.

La planificación y el desarrollo se han llevado a cabo usando **Trello** y **GitHub**.

Además de los requisitos solicitados para el MVP, en la app se ha implementado:

- Securización de rutas
- Registro de usuario
- Refactorización de métodos
- Documentación con JavaDoc
- Implementación de Logs

[<button class="btn btn-info" style="font-family:Arial, Helvetica, sans-serif;">Ir al repositorio de la práctica</button>](https://github.com/JavGuerra/cartelera-java-spring)

### Estado de la aplicación

![Cartelera planificación](/assets/img/cartelera-planificacion.png){: .mx-auto.d-block :}

La aplicación ha sido realizada en un plazo de cuatro semanas en las que íbamos aplicando lo que aprendíamos por la mañana.

Si bien su estado es inconcluso, habida cuenta de que la planificación inicial preveía implementar relaciones entre las distintas pantallas de la App, el código resultante ha sido revisado y cumple con los contenidos impartidos en el curso.

### Opciones de mejora

- Portada dinámica de la App  
- Lógica de negocio y estadísticas
- Gestión de excepciones  
- Testing  
- Diseño claro / oscuro
- Diseño responsive  
- Diseño accesible   
- Aviso legal y privacidad
- Despliegue  

## DEMO

![Cartelera demo](/assets/img/cartelera-demo.png){: .mx-auto.d-block :}

Puede ver un video sobre la aplicación.

[<button class="btn btn-info" style="font-family:Arial, Helvetica, sans-serif;">Ver video (*3 min.*)</button>](https://youtu.be/DfS8oC7WmDk)  

## Indicaciones de instalación

Tras clonar el repositorio, es necesario crear un usuario en la BBDD relacional llamado «**cinefilo**» con el password: «**ElPadrino2**» (sin comillas). Estos datos, así como el puerto por defecto de la app (**8082**), pueden ser alterados en el fichero «aplication.propieties», en resources.

Una vez iniciada la aplicación en local, se puede acceder a ella con la dirección: http://localhost:8082

Para hacer login, todos los usuarios registrados cuentan con la contraseña: **632541**

## Implicación de los autores

* **Javier Guerra**: Se ha encargado de la coordinación del equipo mediante Trello y de la planificación de las funcionalidades de la aplicación, corrección de errores detectados, securización de la app, documentación JavaDoc, funcionalidades de las entidades «user» y «address» y las propuesta de mejora de la app.


* **Dawid Lempicki**: Ha realizado la estructura de la entidad «room», el listado de películas y la presentación de la app.


* **Pedro Rivas**: Se ha encargado de la gestión del repositorio en GitHub, y las funcionalidades de las entidades de «cinema» y «room».


* **Eduardo González**: Ha realizado el diseño CSS y las funcionalidades de la entidad de «film», la estructura de la entidad «user», y el listado de películas.

## Licencia

Sobre el código fuente: [GNU GENERAL PUBLIC LICENSE Version 3](LICENSE)

## Calificación

En el curso, así como en la práctica, he obtenido una calificación de 10/10.

## Enlaces

- [Repositorio](https://github.com/JavGuerra/cartelera-java-spring)  
- [Video de la presentación](https://youtu.be/DfS8oC7WmDk)  
- [Certificado de aprovechamiento](/assets/img/certificados/java-adecco.png)  
