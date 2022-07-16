---
layout: post
title: Comprobaciones múltiples
subtitle: Como simplificar el filtrado de datos. 
thumbnail-img: /assets/img/filtrado.jpg
tags: [código, javascript, node, express]
---

En programación es habitual trabajar con datos de entrada. Un caso común son los campos (obligatorios o no) que recibimos de un formulario para realizar búsquedas en una base de datos y devolver datos al usuario, o para dar de alta nueva información... pero estos datos de entrada, según las circunstancias, pueden variar. Para un mismo formulario, podemos recibir uno o varios campos, lo que complica la forma en que accedemos a la información. En esta entrada veremos como simplificar el filtrado para hacer consultas a los datos.

![Filtrado](/assets/img/filtrado.jpg){: .mx-auto.d-block :}

# Supuesto práctico

Tenemos una lista de **productos** almacenados en un arreglo o lista de objetos. Cada objeto tiene las propiedades **nombre**, **tipo** y **precio**, y deseamos hacer búsquedas sobre esta lista, devolviendo los objetos que cumplan con los datos recibidos desde un formulario. Los datos recibidos pueden ser todos ellos, es decir, valores para cada una de las tres propiedades, o sólo el dato de búsqueda de uno de ellos o dos de ellos (faltando un dato en este último caso). Veamos primeramente la estructura de datos:

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
Una búsqueda ```nombre: 'LAVADORA'``` devolvería el primer objeto. Si buscamos por ```tipo: 'hogar'``` nos devolverá el primer y el tercer objeto... Y si buscamos por ```tipo: 'video' y precio: menor que 1500``` nos devolverá el segundo objeto.

## Así sería

Para hacer una búsqueda sobre esta lista, tendríamos que comprobar si hemos recibido un valor para cada uno de las propiedades, y si es así, realizar el filtrado para obtener el primer resultado, pasar al siguiente valor si lo hubiese, para obtener el siguiente resultado filtrado, y así con tantos campos como hayamos recibido. Algo como esto:

```javascript
let resultados = productos;

if (nombre || tipo || precio) {
    if (nombre) resultados = resultados.filter(producto => producto.nombre.includes(nombre));
    if (tipo) resultados = resultados.filter(producto => producto.tipo.includes(tipo));
    if (precio) resultados = resultados.filter(producto => producto.precio < precio);
} else {
    resultados = [];
}

console.log(resultados);
```
Este encadenado de filtrados me obliga a realizar tres filtrados e ir guardando el resultado para el siguiente filtro, si la entrada de formulario, por tanto la variable que guarda su valor, existe.

Como se aprecia en el código, primeramente copio el contenido de ```productos``` a ```resultados```, para ir filtrando el resultado. Esto lo hago porque no sé qué valores recibiré, por lo tanto no sabré donde empezará ```productos.filter```, si en el primer, segundo o tercer ```if```.

Seguidamente compruebo si tengo valores en las variables ```nombre```, ```tipo``` y ```precio```. Si alguna de ellas existe, realizo el filtrado sobre los datos, sino, devuelvo un arreglo o lista vacía. Así pues, si existe nombre, filtro por nombre, si existe tipo, filtro por tipo y si existe precio filtro por precio, consecutivamente.

## Otra forma de hacerlo

Es posible hacer un sólo filtrado que incluya las tres condiciones. Para ello usaré el operador condicional ternario (```?```):

```javascript
const resultados = (nombre || tipo || precio)
? productos.filter(producto =>
    (nombre ? producto.nombre.includes(nombre) : true) &&
    (tipo   ? producto.tipo.includes(tipo) : true) &&
    (precio ? producto.precio < precio : true))
: [];
```
La lista ```resultados``` tendrá el valor del filtro o una lista vacía según reciba o no reciba alguno de los valores para filtrar.

Dentro de ```filter``` incluimos ahora los tres criterios de filtrado de una vez, pero igualmente debemos comprobar si cada uno de las variables que recibimos tiene o no valores para filtrar, es decir, si el dato de entrada existe.

Si una variable tiene valor, es decir, hemos recibido sus datos del formulario, entonces se incluirá la condición de filtrado, sino se incluirá un ```true```. Nos fijamos en una de las líneas:

```javascript
    (nombre ? producto.nombre.includes(nombre) : true) &&
```
Si la variable ```nombre``` tiene datos, entonces se incluirá la comprobación ```producto.nombre.includes(nombre)```, de otra forma se incluirá un ```true``` en el filtrado.

Como estamos filtrando por un total de hasta tres comprobaciones que tienen que ser verdaderas (```&&```), si alguna de ellas no se cumple, el ```filter``` pasará al siguiente elemento de la lista, y no devolverá este elemento.

Imaginemos que sólo hemos recibido el ```nombre``` del producto. ¿Qué pasa con las otras dos condiciones derivadas de ```tipo``` y ```precio```?. Devolverán ```true```, condicionando la respuesta de filtrado a lo que devuelva la condición de ```nombre```. Por ello los ```true``` que no sean resultado de la condición de una o varias de las variables no tendrán ninguna influencia en el valor de la comprobación.

Si tenemos dos variables, la tercera será ```true``` y la condición de filtrado funcionará sólo si las dos condiciones con valores de entrada son ```true``` si han pasado la comprobación asociada a esa variable.

# Otra manera más

En programación no hay un único camino para hacer las cosas. Tras la lectura de esta entrada, **Marcos**, de un foro de Discord, me propuso esta alternativa aún más simple. Vamos a ver cómo funciona:

```javascript
const resultados = productos.filter(
    (producto) => 
      (!nombre || producto.nombre.includes(nombre)) &&
      (!tipo || producto.tipo.includes(tipo)) &&
      (!precio || producto.precio < precio)
  )
```
La lógica de fondo no deja de ser la misma, se basa en un booleano (```true```) que nos dice si se va a procesar el filtrado o se va a incluir un ```true```, pero esta refinada forma de hacerlo consiste en «jugar» con el valor inverso del contenido de una variable, de tal forma que si la variable existe, su valor será ```false``` (Ej. ```!nombre```) debiendo aplicar entonces la condición de filtrado (```||```), y si no existe, el valor de la variable será ```true```. Elegante.


# Un poco más allá

La siguiente es una función basada en la codificación anterior y devuelve un objeto que incluye un código de respuesta y el listado de objetos resultante.

```javascript
function respuesta(nombre = undefined, tipo = undefined, precio = undefined) {
    let codigo = (nombre || tipo || precio) ? 0 : 2;
    const resultado = (codigo == 0)
        ? productos.filter(producto =>
            (nombre ? producto.nombre.includes(nombre) : true) &&
            (tipo   ? producto.tipo.includes(tipo) : true) &&
            (precio ? producto.precio < precio : true))
        : [];
    if (codigo == 0) codigo = (resultado.length) ? 0 : 1;
    return { codigo, resultado };
}
```
La variable código va a contener tres posibles valores:

| Valor | Significado |
| :---: |:--- |
| 0 | Datos encontrados. |
| 1 | No se han encontrado datos. |
| 2 | No se han recibido datos de entrada. |

De tal forma que en:

```javascript
    let codigo = (nombre || tipo || precio) ? 0 : 2;
```
se determina si se ha recibido alguna de las variables, asignándole el código correspondiente, y en la línea:

```javascript
    if (codigo == 0) codigo = (resultado.length) ? 0 : 1;
```
se determina si se ha obtenido algún resultado tras la búsqueda, si código era 0, es decir, si había datos de entrada.

por último en la línea:

```javascript
    return { codigo, resultado };
```
Devolvemos el objeto con la información de lo sucedido y el resultado de la búsqueda.

La línea anterior es equivalente a:

```javascript
    return { codigo: codigo, resultado: resultado };
```

Esta función puede ser útil para, por ejemplo, trabajar con rutas en node.js/express. un ejemplo:

```javascript
router.get('/', (req, res) => {
    const { nombre, tipo, precio } = req.query;
    res.json(response(nombre, tipo, precio));
});
```

