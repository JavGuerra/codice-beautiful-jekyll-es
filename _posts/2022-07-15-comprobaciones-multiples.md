---
layout: post
title: Comprobaciones múltiples
subtitle: Como simplificar el filtrado de datos. 
thumbnail-img: /assets/img/filtrado.jpg
tags: [código, javascript, node, express]
---

En programación es muy frecuente encontrarnos con valores de entrada que pueden o no estar presentes y con los que pretendemos llevar a cabo una tarea, por ejemplo, al trabajar con los campos (obligatorios o no) de un formulario con los que luego llevaremos a cabo búsquedas en una base de datos para dar de alta información, devolver datos al usuario... En esta entrada veremos como simplificar el filtrado cuando hacemos las comprobaciones necesarias con los datos recibidos.

![Filtrado](/assets/img/filtrado.jpg){: .mx-auto.d-block :}

# Supuesto práctico

Tenemos una lista de **productos** almacenados en un arreglo o lista de objetos. Cada objeto tiene las propiedades **nombre**, **tipo** y **precio**, y queremos hacer búsquedas sobre esta lista, devolviendo los objetos que cumplan con los datos recibidos desde un formulario. Los datos recibidos pueden ser todos ellos, es decir, valores para cada una de las tres propiedades, o sólo el dato de búsqueda de uno de ellos o dos de ellos, faltando un dato en este último caso. Veamos la estructura de datos:

```javascript
let productos = [
    {
        "nombre": "LAVADORA SECAMAS",
        "tipo": "hogar",
        "precio": 700
    },
        {
        "nombre": "TELEVISOR TV",
        "tipo": "video",
        "precio": 1200
    },
        {
        "nombre": "ARMARIO RIO",
        "tipo": "hogar",
        "precio": 300
    },
];
```
Una búsqueda ```nombre 'LAVADORA'``` devolvería el primer objeto. Si buscamos por ```tipo 'hogar'``` nos devolverá el primer y el tercer objeto... Y si buscamos por ```tipo 'video' y precio menor que 1500``` nos devolverá el segundo objeto.

## Así sería

Para hacer una búsqueda sobre esta lista, tendríamos que comprobar si hemos recibido un valor para cada uno de las propiedades, si es así, realizar el filtrado para obtener el primer resultado, y pasar al siguiente valor si lo hubiese, para obtener el siguiente resultado filtrado. Algo como esto:

```javascript
let resultados = productos;

if (nombre || tipo || precio) {
    if (nombre) resultados = resultados.filter(elemento => elemento.nombre.includes(nombre));
    if (tipo) resultados = resultados.filter(elemento => elemento.tipo.includes(tipo));
    if (precio) resultados = resultados.filter(elemento => elemento.precio < precio);
} else {
    resultados = [];
}

console.log(resultados);
```
Este encadenado de filtrados me obliga a realizar tres filtros e ir guardando el resultado para el siguiente filtro, si la variable existe.

Como se aprecia en el código, primeramente copio el contenido de ```productos``` a ```resultados```, para ir filtrando el resultado. Esto lo hago porque no sé qué valores recibiremos a través de las variables, por lo tanto no sabré donde empezará ```productos.filter```, si en el primer, segundo o tercer if.

Seguidamente comprobamos si tenemos valores en las variables nombre, tipo y precio. Si alguna de ellas existe, realizamos la búsqueda, sino devuelvo un arreglo o lista vacía. luego, si existe nombre, filtro por nombre, si existe tipo, filtro por tipo y si existe precio filtro por precio.

## Otra forma de hacerlo

Es posible hacer un sólo filtrado que incluya las tres condiciones. Para ello usaré el operador condicional ternario (```?```):

```javascript
const resultados = (nombre || tipo || precio)
? productos.filter(elemento =>
    (nombre ? elemento.nombre.includes(nombre) : true) &&
    (tipo   ? elemento.tipo.includes(tipo) : true) &&
    (precio ? elemento.precio < precio : true))
: [];
```
La lista ```resultados``` tendrá el valor del filtro o una lista vacía según reciba o no alguno de los valores para filtrar.

Dentro de ```filter``` incluimos ahora los tres criterios de filtrado de una vez, pero igualmente debemos comprobar si cada uno de las variables que recibimos tiene o no valores para filtrar.

Si una variable tiene valor, es decir, hemos recibido sus datos del formulario, entonces se incluirá la condición de filtrado, sino se incluirá un ```true```. Nos fijamos en una de las líneas:

```javascript
    (nombre ? elemento.nombre.includes(nombre) : true) &&
```
Si la variable ```nombre``` tiene datos, entonces se incluirá la comprobación ```elemento.nombre.includes(nombre)```, de otra forma se incluirá un ```true``` en el filtrado.

Como estamos incluyendo tres comprobaciones que tienen que ser verdaderas (```&&```) en el filter, si alguna de ellas no se cumple, el filtro pasará al siguiente elemento de la lista, y no devolverá este elemento.

Imaginemos que sólo hemos recibido el ```nombre``` del producto. ¿Qué pasa con las otras dos condiciones derivadas de ```tipo``` y ```precio```?, que devolverán ```true```, condicionando la respuesta de filtrado a lo que devuelva la condición de ```nombre```. Por ello los ```true``` que no sean resultado de la condición de una o varias de las variables no tendrán ninguna influencia en el valor de la comprobación.

Si tenemos dos variables, la tercera será ```true``` y la condición de filtrado funcionará sólo si las otras dos condiciones son ```true``` también después de haber pasado la comprobación asociada a esa variable.

# Un poco más allá

La siguiente es una función que incluye la codificación anterior y devuelve un objeto que incluye un código de respuesta y el listado de objetos resultante.

```javascript
function respuesta(nombre = undefined, tipo = undefined, precio = undefined) {
    let codigo = (nombre || tipo || precio) ? 0 : 2;
    const resultado = (codigo == 0)
        ? productos.filter(elemento =>
            (nombre ? elemento.nombre.includes(nombre) : true) &&
            (tipo   ? elemento.tipo.includes(tipo) : true) &&
            (precio ? elemento.precio < precio : true))
        : [];
    if (codigo == 0) codigo = (resultado.length) ? 0 : 1;
    return { codigo, resultado };
}
```
La variable código va a contener tres posibles valores:

| Valor | Significado |
| :------ |:--- |
| 0 | Datos encontrados. |
| 1 | No se han encontrado datos. |
| 2 | no se han recibido datos de entrada. |

De tal forma que en:

```javascript
    let codigo = (nombre || tipo || precio) ? 0 : 2;
```
se determina si se ha recibido alguna de las variables, y en la línea:

```javascript
    if (codigo == 0) codigo = (resultado.length) ? 0 : 1;
```
se determina si hemos tenido algún resultado tras la búsqueda o no.

por último en la línea:

```javascript
    return { codigo, resultado };
```
Devolvemos el objeto con la información de lo sucedido y el resultado de la búsqueda.

Recordar que la línea anterior es equivalente a:

```javascript
    return { codigo: codigo, resultado: resultado };
```

Esta función nos puede ser útil para, por ejemplo, trabajar con rutas en node.js/express. un ejemplo:

```javascript
router.get('/', (req, res) => {
    const { nombre, tipo, precio } = req.query;
    res.json(response(nombre, tipo, precio));
});
```

