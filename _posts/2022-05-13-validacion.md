---
layout: post
title: Validación de formularios
subtitle: Combinando validaciones propias con las de HTML5
thumbnail-img: /assets/img/validacion.png
tags: [código, HTML, CSS, javascript, usabilidad]
---
¿Cómo procesar un formulario antes de enviarlo, realizando validaciones propias en sus campos y aprovechando simultáneamente la validación de restricciones implícita en HTML5?

Que ambos tipos de validaciones se den al mismo tiempo es importante, ya que si esto se hiciera consecutivamente, es decir, una a continuación de la otra, correríamos el riesgo de que los campos ya validados pudieran ser alterados por el usuario una vez pasada la primera validación, si en la segunda validación hay campos erróneos, y por tanto el formulario se enviaría errores.

![Validación](/assets/img/validacion.png){: .mx-auto.d-block :}

Veámoslo con este [<button>Formulario de ejemplo</button>](https://javguerra.github.io/02-bootcamp-fs-javascript/validacion.html)

Tenemos un formulario con tres campos requeridos (nombre, edad, correo), y al campo edad le añado una restricción propia, si su valor es mayor o igual a 16, que deberé validar y mostrar con un mensaje propio en el mismo campo, si este es erróneo. El formulario cuenta con dos botones: «Borrar» y «Enviar».

Si no tuviésemos que realizar nuestra validación, el proceso sería sencillo, ya que HTML se ocupa de ello por nosotros. Al hacer click en el botón enviar, que es de tipo submit (```<button type="submit">Enviar</button>```), el formulario lanza el evento ```submit()``` para procesarlo, y se envía si no hay errores, o nos muestra los errores de validación de campos implícita en HTML5. Pero para poder realizar nuestras propias validaciones debemos romper este flujo, y tomar el control de la validación.

## Tomando el control

Esto podemos hacerlo de dos formas. O bien capturamos el evento submit() asociado al botón «Enviar» con ```form.onsubmit = validaForm;``` en JavaScript, y en ```function validaForm(evento)``` incluimos un ```evento.preventDefault();``` evitando de esta forma que el ```action``` del formulario se procese, y realizando por nuestra cuenta todas las validaciones, las propias y las de HTML5 usando su ([API](https://www.w3.org/TR/html5/forms.html#the-constraint-validation-api)), y lanzando al terminar el evento ```form.submit()``` para el envío de formulario, o simplemente podemos usar un botón de formulario para enviar que no incluya el ```type="submit"```, y asociar el botón a la función de validación ```validaForm()``` en JavaScript desde la cual lanzaremos el ```form.submit()``` cuando se cumplan las dos condiciones: que la edad sea la correcta y que el formulario sea válido.

La opción elegida para este ejemplo es la segunda por ser la más simple. Desde la función ```validaForm()``` comprobamos la edad con la función ```validaEdad()``` que debe devolver ```True``` o ```False``` y comprobamos si el formulario tiene errores con ```form.checkValidity()```, que también devuelve ```True``` o ```False```. Si ambas comprobaciones son ```True```, el formulario se envía. Veamos el código JavaScript:

{% highlight javascript linenos %}
const enviar = document.querySelector('#enviar');
const form   = document.formulario;

enviar.onclick = validaForm;

function validaForm() {
  if ( validaEdad() && form.checkValidity() ) {
    alert('Ambas validaciones son correctas.');
    form.submit();
  } else {
    alert( 'El formulario contiene errores.' );
  }
}

/* Comprueba que el usuario tenga más de 16 años */
function validaEdad() {
  let valida = true;
  let error  = '';
  if (form.edad.value < 16) {
      valida = false;
      error  = 'Demasiado joven.';
  }
  form.edad.setCustomValidity(error);
  return valida;
}
{% endhighlight %}

Para llegar a esta solución me inspiré en [la información](https://stackoverflow.com/questions/45789010/how-to-use-html-form-checkvalidity) disponible en **StackOverflow** y en el [enlace](https://www.w3schools.com/js/js_validation_api.asp) de **W3Schools** que aparece en la mejor respuesta.

En caso de tener que llevar a cabo más validaciones propias, bastaría con encadenarlas en el ```ìf()``` de la línea **7**, o crear una función a parte que incluya todas nuestras validaciones, y sustituya a ```validaEdad()``` devolviendo ```True``` o ```False```.

## La función validaEdad()

En la línea 18 inicializamos la variable ```error``` con ```''``` que luego usaremos en la línea **23** con ```.setCustomValidity()```. Cuando se ejecuta la línea **23** se llevan a cabo dos cosas: se marca un error (o no) en el campo edad, y se asigna un mensaje de error personalizado que le pasamos a ```.setCustomValidity()```. Si el mensaje de error es ```''``` (cadena vacía) estaremos indicando que no hay ningún error en el campo edad, por tanto la validación de HTML5 sabrá que el formulario es correcto. En caso contrario, estaríamos enviando el mensaje de error personalizado (que definimos en la línea **21**) y marcando un error en ese campo. 

Es importante que, una vez el contenido del campo edad sea el correcto, es decir, la edad introducida sea **mayor o igual a 16**, pasemos ```''``` a ```.setCustomValidity()```, de lo contrario el formulario nunca se procesará. Por defecto, al llamar a la función ```validaEdad()``` pasará ```''``` si se comprueba que no hay error.

Este formato deberemos usarlo en cada una de las comprobaciones propias que llevemos a cabo.

## Valida / NO valida

¿Qué ocurre si la edad no es correcta o si uno de los campos obligatorios está vacío? Se mostrará la alerta de la línea **11**. Podemos sustituir esta alerta por una serie de acciones de aviso al usuario u otras para informar de que algo no ha ido bien. En caso contrario, se llevará a cabo lo estipulado en las líneas **8** y **9**.

La alerta de la línea **8** puede sustituirse por cualquier cosa que queramos llevar a cabo antes de enviar el formulario en la línea **9**, por ejemplo, mostrar los valores de los campos para que el usuario lo revise antes de enviar, si estamos realizando una compra o un envío de dinero...

Por último, en la línea **9**, enviamos el formulario con ```form.submit()```.

Eso es todo. Puedes ver el ejemplo y más información en los siguientes enlaces:

* [<button>Formulario de ejemplo</button>](https://javguerra.github.io/02-bootcamp-fs-javascript/validacion.html)
* [API de formularios](https://www.w3.org/TR/html5/forms.html#the-constraint-validation-api)
* [Información y ejemplos en W3Schools](https://www.w3schools.com/js/js_validation_api.asp)
* [Información en StackOverflow](https://stackoverflow.com/questions/45789010/how-to-use-html-form-checkvalidity)




