---
layout: post
title: Generar enlaces dinámicos de imágenes almacenadas en MongoDB
subtitle: Conseguir que nuestros datos no dependan de una ubicación estática.
thumbnail-img: /assets/img/dynamic_links.png
tags: [código, javascript, node.js, mongodb, imágenes]
---
Las bases de datos a menudo contienen referencias a recursos que no son almacenados en su misma estructura. Un claro ejemplo de ello son las fotografías o imágenes que acompañan a los productos de un catálogo. En tal caso, guardar la información es sencillo, es suficiente con almacenar la ruta completa al recurso en un campo de tipo _String_, pero, ¿que ocurre si, presumiblemente, nuestros datos van a cambiar de ubicación, o va a correr distintas instancias de si misma en diferentes hospedajes? En ese caso, lo recomendable es almacenar la información del nombre del recurso en la BBDD, y entonces tendremos dos alternativas para generar el enlace completo: modificar los datos de la BBDD para que la url al recurso sea la adecuada, o bien generar la ruta al recurso de forma dinámica cuando se realizan las consultas. Vamos a ver aquí la segunda.

![enlaces dinámicos](/assets/img/dynamic_links.png){: .mx-auto.d-block :}

# El ejemplo

Partimos de la siguiente premisa. En las variables globales del sistema tenemos almacenadas la ruta y el puerto del servidor donde están almacenadas las imágenes, que es la información susceptible de cambiar de lugar. Tal vez estos datos estén disponibles mediante [dotenv](https://www.npmjs.com/package/dotenv).

Tomemos como referencia el ejemplo de la [entrada anterior]({% post_url 2022-10-29-populate-paginate-fitrado %}). Dos colecciones relacionadas, `libros` y `autores`, y en las que queremos incluir un dato en cada documento de la colección libros con la foto de portada del libro, y un dato con la foto del autor en cada documento de la colección autores. Algo como:

```javascript
libro: {
    portada: 'don-quijote.png',
}

autor: {
    foto: 'cervantes.jpg',
}
```

# La ruta

Para poder acceder a la portada del libro, necesitamos completar la ruta a la imagen. En este ejemplo usaremos el dominio `lalibreria2022.com` al que se accede por el porto `80`. Esta información la tenemos almacenada en un fichero `.env` de nuestra aplicación de la siguiente forma:

```text
HOST='https://lalibreria2022.com'
PORT=80
```
Y asumo que las portadas se almacenan en una carpeta llamada `portadas`, y las fotos en otra llamada `fotos`. 
De esta forma, para acceder a la imagen `don-quijote.png` debemos completar la siguiente ruta:

```text
HOST + ':' + PORT + '/' + 'portadas' + '/' + 'don-quijote.png'
```
# Alterando los resultados

Cuando llevamos a cabo las búsquedas de información sobre la BBDD, esta nos devolverá, como ya sabemos, el nombre del recurso ('don-quijote.png'), pero queremos que la BBDD devuelva dinámicamente la ruta antes mencionada. Para ello hay que recorrer el resultado obtenido y hacer cambios sobre los datos. Un `forEach()` es suficiente.

```javascript
const insertarRutas = datos => {
    const url = process.env.HOST + ':' + process.env.PORT + '/';
    datos.forEach( elemento => elemento.portada = url + 'portadas/' + elemento.portada );
    return data;
}
```
Tras cada búsqueda, aplicando esta función al resultado, recorro todos los documentos de la colección (`datos`) y cambio el valor del dato `portada` en cada documento.

# También con resultados agregados

Cuando llevamos a cabo consultas agregadas, el proceso es similar. En una consulta donde obtenemos los libros y las referencias a sus respectivos autores, debemos repetir el proceso también para el dato `foto` de la colección `autores`, pero hay que tener en cuenta lo siguiente:

{: .box-note}
**Nota:** Cuando recorremos una colección agregada lo hacemos sobre una colección y sobre referencias a otra colección, es decir, si hay varios libros de un mismo autor, aunque aparezca en varios libros agregados, serán los mismos datos del autor en cada libro.

Esto tiene importancia porque, al realizar el cambio de la ruta de la foto del autor, estaríamos cambiándola tantas veces como libros existan, y generando entonces una url errónea con rutas repetidas. Si tenemos dos libros de Cervantes, la ruta a la foto podría verse así:

```text
https://lalibreria2022.com:80/fotos/https://lalibreria2022.com:80/fotos/cervantes.jpg
```
Para evitar esto, debemos implementar el siguiente código, en el que se incluyen ambos casos, el de la portada del libro y el de la foto del autor:

```javascript
const insertarRutas = datos => {
    const url = process.env.HOST + ':' + process.env.PORT + '/';
    datos.forEach(elemento => {
        elemento.portada = url + 'portadas/' + elemento.portada;
        if (!elemento.autor.ref.foto.includes(url)) 
             elemento.autor.ref.foto = url + 'fotos/' + elemento.autor.ref.foto;
    });
    return data;
}
```
Con el `if` me aseguro que la foto no contiene la ruta completa, y si es así, la completa.

# Enlaces

* [Usando populate con paginate y filtrando los resultados]({% post_url 2022-10-29-populate-paginate-fitrado %})  