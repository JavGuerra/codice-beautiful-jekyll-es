---
layout: post
title: Ordenando resultados en React
subtitle: Listando alternativamente, por distintos criterios, la información de menor a mayor y viceversa
thumbnail-img: https://lamardelibros.files.wordpress.com/2015/08/dalton.jpg
tags: [react, javascript]
---

Cuando mostramos al usuario un listado con muchos resultados totales, le estamos dando también un problema. Buscar la información que necesita entre tantos resultados requiere tiempo y esfuerzo. Para atajar este problema podemos incluir un campo de búsqueda, pero aún así, la información aparecerá desordenada. En esta entrada veremos como ordenar los resultados de menor a mayor y viceversa, y cómo hacerlo por varios criterios alternativamente en React. 

![Ordenación](https://lamardelibros.files.wordpress.com/2015/08/dalton.jpg){: .mx-auto.d-block :}

# El ejemplo

Esta entrada está basada en el [proyecto «Tienda 8 bits»]({% 2022-11-18-proyecto-final-bootcamp %}), y aquí puedes ver su funcionamiento en esta [página de ejemplo](https://javguerra.badared.com/proyecto/tienda8bits/).

Queremos listar una serie de fichas de productos por tres criterios, modelo de producto, precio y año de fabricación, y lo haremos alternativamente, es decir, por uno de entre esos tres criterios de ordenación a la vez.

La ordenación se hará de menor a mayor, osea, en orden ascendente, y de mayor a menor, en orden descendente. La API de este ejemplo debe estar preparada para ello, aunque no entraré a mostrar eso en esta entrada.

Los filtros, en la página, tendrán un aspecto parecido a este:

![Filtros](/assets/img/filtros.png){: .mx-auto.d-block :}

Voy a usar React + Vite para este ejercicio.

# La App

En nuestra página App.jsx, para este ejercicio, voy a usar dos _useState_ uno que contendrá los filtros de ordenación en un objeto y otro que contendrá el valor de la página actual de los resultados, paginados, que vamos a mostrar.

```javascript
const [sortData, setSortData] = useState({ sortmodel: 1 });
const [currentPage, setCurrentPage] = useState(1);
```
En una aplicación completa deberemos incluir otros useState para, por ejemplo, guardar los datos obtenidos de la consulta a la API, o los criterios de búsqueda de datos...

Dentro del objeto que tendremos en `sortData` iremos incluyendo, alternativamente, `sortmodel` para filtrar por el modelo, `sortprice` para filtrar por el precio, y `sortyear` para filtrar por el año. Los valores posibles para cada una de estas propiedades de filtrado serán `1` y `-1`, siendo 1 el valor que indica ordenación ascendente, y -1 el valor que indica ordenación descendente, que son los valores que acepta nuestra API.

```javascript
useEffect(() => {
    const { sortmodel, sortprice, sortyear } = sortData;

    // Preparar los datos para la consulta a la API
    // Realizar la consulta a la API y obtener (set) los resultados
    
}, [sortData, currentPage]);
```
Cuando `sortData` cambie por la interacción con el usuario mediante el uso de la función `setSortData`, el contenido de `useEffect` se ejecutará, pasando los datos de filtrado a la consulta a la API que se puede hacer mediante fetch, axios... Los datos deben ser preparados para hacer la consulta, para ello, deberemos comprobar cual de los tres filtros (sortmodel, sortprice, sortyear) estamos usando y cuales no (_undefined_), e incluir el filtro indicado en la consulta.

El componente que va a llevar a cabo la ordenación se llama `Sort`, y esta sería su forma para renderizarlo en la página:

```javascript
return (
    <Sort
        sortData={sortData}
        setSortData={setSortData}
        setCurrentPage={setCurrentPage}
    />
);
```
Por props le hacemos llegar al componente Sort los datos de filtrado (sortData), la función que establece (set) el filtrado (setSortData) y la función que establece la página actual (setCurrentPage). Esto último es necesario porque cada vez que cambie el filtrado estableceremos la página 1 como la página actual, de otra los datos obtenidos con el nuevo filtrado se mostrarían confusos para el usuario.

## El componente Sort

El código será el siguiente, e iré explicando cada parte a continuación:

```javascript
import SortIcon from "./SortIcon";

const Sort = ({sortData, setSortData, setCurrentPage}) => {

  const { sortmodel, sortprice, sortyear } = sortData;

  const handleIcon = (sortName, order) => {
    setSortData({ [sortName]: order ? -order : 1 });
    setCurrentPage(1);
  };

  return (
    <ul>
      <li>
        <SortIcon
          name="Modelo"
          sortName="sortmodel"
          order={sortmodel}
          handleIcon={handleIcon}
        />
      </li>
      <li>
        <SortIcon
          name="Precio"
          sortName="sortprice"
          order={sortprice}
          handleIcon={handleIcon}
        />
      </li>
      <li>
        <SortIcon
          name="Año"
          sortName="sortyear"
          order={sortyear}
          handleIcon={handleIcon}
        />
      </li>
    </ul>
  );
}

export default Sort;
```

Primeramente importo el componente `SortIcon` que luego veremos. Éste componente se encargará de mostrar el nombre y el icono de ordenación por cada uno de los tres criterios y hacer los cambios necesarios al hacer clic en él.

Como dije, el componente `Sort` recibe por props tres parámetros `{sortData, setSortData, setCurrentPage}` con el dato de ordenación, la función para fijar la nueva ordenación y la función para fijar la página actual de la paginación de resultados.

Lo primero que hago es obtener los datos del objeto `sortData`recibido por props.

La función `handleIcon` será la encargada de efectuar el cambio de filtro de ordenación.

A cada `sortIcon`, en el renderizado, le paso cuatro valores: 

* __name__: con el nombre que aparecerá en la página identificando al filtro.  
* __sortName__: el nombre del filtro que vamos a fijar al hacer clic en este icono mediante la función handleIcon.  
* __order__: que contiene el valor de ordenación del filtro (1 ó -1).  
* __handleIcon__: la función que gestionará el cambio de filtro y el valor de ordenación, y fijará la página actual a 1.  

## La función handleIcon

Aquí es donde se lleva a cabo todo.

```javascript
const handleIcon = (sortName, order) => {
    setSortData({ [sortName]: order ? -order : 1 });
    setCurrentPage(1);
};
```
La función recibe dos parámetros, `sortName`, con el nombre del filtro seleccionado en función del icono (SortIcon) en el que hagamos clic, y `order`, con el valor del ordenación de ese filtro (1 ó -1).

Con `setSortData`, que recibí por props, voy a fijar el criterio de ordenación y su valor.

Con `[sortName]:` dentro del objeto que voy a pasar a `setSortData` indico que la propiedad se tiene que llamar como el nombre del filtro recibido en `sortName` (que pueden ser tres: "sortmodel", "sortprice" o "sortyear").

Con `order ? -order : 1` determino el valor de la propiedad del objeto que voy a pasar a `setSortData`. Si el valor de order existe, es decir no es `undefined` su valor ahora será el valor alternativo (positivo o negativo) al que ya tiene. Si el valor era 1, ahora será -1. Si era -1, pasará a ser 1. y en caso de que el valor de order fuera `undefined`, es decir, que no fuese este el filtro por el que estábamos filtrando antes, establezco su valor inicial a 1.

Con `setCurrentPage(1)` fijo la página de la paginación de resultados a 1.

Con esta simple función podemos entonces, como dije, seleccionar el filtrado entre uno de los tres filtros posibles, y establecer su criterio de ordenación.

## El componente SortIcon

Este componente es el encargado de mostrar el nombre y el icono de cada uno de los tres criterios de ordenación en la página.

```javascript
const SortIcon = ({name, sortName, order, handleIcon}) => {

  const d = !order
    ? "M8.25 15L12 18.75 15.75 15m-7.5-6L12 5.25 15.75 9"
    : order === 1
      ? "M3 4.5h14.25M3 9h9.75M3 13.5h5.25m5.25-.75L17.25 9m0 0L21 12.75M17.25 9v12"
      : "M3 4.5h14.25M3 9h9.75M3 13.5h9.75m4.5-4.5v12m0 0l-3.75-3.75M17.25 21L21 17.25";

  return (
    <span onClick={() => handleIcon(sortName, order)}>
      {name}&nbsp;
      <svg xmlns="http://www.w3.org/2000/svg" className="icon" fill="none"
          viewBox="0 0 24 24" strokeWidth={1.5} stroke="currentColor">
          <path strokeLinecap="round" strokeLinejoin="round" d={d} />
      </svg>
    </span>
  );
}

export default SortIcon;
```

Por props, `SortIcon`recibe cuatro parámetros: `name, sortName, order, handleIcon`.

En el renderizado lo que hago es poner el nombre `name` y dibujar el icono en función del valor de `order`, que puede ser: 1, -1 y _undefined_. Para elegir que icono mostrar, cambio el valor de la propiedad `d` de la imagen SVG que pinta el icono. La propiedad `d` es la única que cambia en los tres iconos elegidos, el de ordenación ascendente (1), descendente (-1) y no seleccionado (_undefined_), así que utilizo el mismo SVG para los tres iconos, y cambio el valor de d en función de su estátus (order).

Con el operador condicional ternario compruebo si order es `undefined` (!order) y si es así, asigno a `d` los datos para pintar el icono de "no seleccionado". En caso contrario, hago otra comprobación con el operador condicional ternario, esta vez para indicar si el icono a dibujar es el de  ordenación ascendente o descendente.

Los iconos corresponden a la librería de iconos de [Heroicons](https://heroicons.com/) en su versión JSX.

Para terminar, lo más importante. Al hacer click en cualquiera de los tres iconos, la función `handleIcon` dentro de  `onClick={() => handleIcon(sortName, order)}` se ejecuta, con los parámetros `sortName`y `order`, lo que permite hacer cambios en la ordenación, disparando el `useEffect` de App.jsx, y haciendo que los productos listados se actualicen.

# Enlaces

* [Página de ejemplo «Tienda 8 bits»](https://javguerra.badared.com/proyecto/tienda8bits/)  
* [Información sobre el proyecto]({% post_url 2022-11-18-proyecto-final-bootcamp %})  
* [Heroicons](https://heroicons.com/)  
