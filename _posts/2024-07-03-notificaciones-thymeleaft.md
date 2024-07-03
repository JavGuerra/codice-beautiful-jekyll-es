---
layout: post
title: Notificaciones con Thymeleaft
subtitle: Cómo incluir avisos en Spring Boot
thumbnail-img: /assets/img/notificaciones.jpg
tags: [código, java, spring, bootstrap, thymeleaft]
---

Cuando una aplicación realiza operaciones potencialmente críticas como editar, guardar o borrar datos de una base de datos, es conveniente notificar al usuario el éxito o fracaso de las operaciones. Esta entrada presenta una solución básica de cómo mostrar notificaciones en la plantilla Thymeleaft.

Se asume que el lector tiene conocimientos generales de Spring Boot, Thymeleaft y Bootstrap para aplicar el contenido.

![Notificaciones](/assets/img/notificaciones.jpg){: .mx-auto.d-block :}

# Planteamiento

La idea es destinar una área de la visualización en la aplicación a las notificaciones, y activar este área sólo cuando se deban mostrar, dando la opción al usuario de cerrarlas una vez leídas. Este área puede crearse como un fragmento (fragment) e incluirse en las plantillas que se necesiten, o en todas las plantillas asociadas a las vistas de la aplicación.

Será necesaria una variable que sirva de «llave» para mostrar estas notificaciones o no. Como enviar el mensaje desde cada gestor de ruta de nuestros controladores es algo trabajoso, voy a usar un filtro de sesión http donde guardaré el valor del mensaje y el tipo de mensaje a mostrar.

Por último, mediante un controlador de mensajes, gestionaré el borrado del mensaje para que deje de mostrarse.

## El fragmento

He creado un fragmento de código llamado `messageAlert.html` que he incluido en mi carpeta `fragments` dentro de templates en resources.

```html
<div th:fragment="messageAlert" th:if="${session.message != ''}" class="container">
    <form th:action="@{/message}" method="POST">

        <input type="hidden" th:name="returnUrl" th:value="${returnUrl}">

        <div class="alert alert-dismissible fade show" th:classappend="${session.messageType} == 'danger'? 'alert-danger' : 'alert-info'" role="alert">
            <div>
                <svg xmlns="http://www.w3.org/2000/svg" fill="none" viewBox="0 0 24 24" stroke-width="1.5" stroke="currentColor" width="24" height="24">
                    <path stroke-linecap="round" stroke-linejoin="round" d="M14.857 17.082a23.848 23.848 0 0 0 5.454-1.31A8.967 8.967 0 0 1 18 9.75V9A6 6 0 0 0 6 9v.75a8.967 8.967 0 0 1-2.312 6.022c1.733.64 3.56 1.085 5.455 1.31m5.714 0a24.255 24.255 0 0 1-5.714 0m5.714 0a3 3 0 1 1-5.714 0M3.124 7.5A8.969 8.969 0 0 1 5.292 3m13.416 0a8.969 8.969 0 0 1 2.168 4.5" />
                </svg>&nbsp;&nbsp;
                <span class="align-middle" th:text="${session.message}"></span>
            </div>

            <button type="submit" class="btn-close" data-bs-dismiss="alert" aria-label="Cerrar"></button>
        </div>
    </form>
</div>
```

Para incluir este fragmento en la vista donde deseo que se muestre la notificación, uso:

```html
<div th:replace="~{fragments/messageAlert :: messageAlert}"></div>
```

Con `th:if="${session.message != ''}"`determino si este fragmento se mostrará o no. Si el valor de la variable de sesión llamada `message` está vacío, es que no hay nada que mostrar, de otra forma, el fragmento se mostrará.

Mediante `<input type="hidden" th:name="returnUrl" th:value="${returnUrl}">`indico a través de la respuesta de formulario al controlador, a qué página debo dirigirme después de realizar la operación. Esto me permitirá volver a la página donde estaba una vez se procese el borrado del mensaje. Uso de forma genérica `returnUrl` para indicar la ruta porque es útil para, por ejemplo, cuando llamo a un formulario y quiero saber dónde debo ir una vez enviado el contenido del formulario, como en este caso. Por ejemplo, si nuestra aplicación maneja cines, y añadimos, editamos o borramos un nuevo cine, mediante returnUrl puedo indicar al método @PostMapping qué vista mostrar tras realizar estas operaciones. Para poder hacerlo, cuando usé las vistas de crear, editar o borrar, añadí esta variable al modelo con:

``` java
model.addAttribute("returnUrl", "cinemas");
```
donde cinemas es la ruta a la que se debe llegar una vez el formulario de la ruta a la que vamos se procese.

Sigamos. Con `th:classappend="${session.messageType} == 'danger'? 'alert-danger' : 'alert-info'" role="alert` determino qué tipo de alerta de Boostrap mostrar, si una destinada a informar (recuadro con fondo azul) o una destinada a alertar de un fallo (recuadro con fondo rojo). Esto lo consigo mediante la variable de sesión `messageType`.

Tras el icono SVG que se mostrará en el aviso, se incluye el mensaje que se mostrará con `th:text="${session.message}"` dentro de la etiqueta `span`.

Con el botón `button type="submit"` envío el formulario del mensaje al controlador que va a borrar el mensaje en la ruta indicada en el formulario: `<form th:action="@{/message}" method="POST">`.

## Inicializando las variables message y messageType

Como he comentado, voy a usar variables de sesión http para guardar el contenido del mensaje y el tipo de mensaje. Para ello creo un filtro de sesión que compruebe, por cada petición http, si existe una sesión y si las variables de sesión están creadas.

He incluido el siguiente componente en `/Utils/SessionFilter.java`:

```java
@Component
public class SessionFilter implements Filter {

    @Override
    public void init(FilterConfig filterConfig) throws ServletException {}

    @Override
    public void doFilter(ServletRequest request, ServletResponse response, FilterChain chain)
            throws IOException, ServletException {

        HttpServletRequest httpRequest = (HttpServletRequest) request;
        HttpServletResponse httpResponse = (HttpServletResponse) response;

        HttpSession session = httpRequest.getSession(true);

        if (session!= null) {
            if (session.getAttribute("message") == null)
                session.setAttribute("message", "");

            if (session.getAttribute("messageType") == null)
                session.setAttribute("messageType", ""); 
                // Valores: "danger" u otro cualquiera = "info".
        }

        chain.doFilter(request, response);
    }

    @Override
    public void destroy() {}
}
```

Con `HttpSession session = httpRequest.getSession(true);` Obtengo la sesión, y si no existe, la crea, porque he incluido el valor `true` en `.getSession(true)`.

Si existe la sesión (que, salvo error, ya debería existir), compruebo que las variables `message` y `messageType` estén inicializadas, y si no lo están las inicializo a "".

Tras esto, ya podremos usar `session.getAttribute()` y `session.setAttribute()` para leer y para actualizar el valor de las variables desde nuestros servicios o controladores, como veremos.

## Usando mensajes

Pensemos en un servicio que guarda el contenido de un formulario para crear un cine. Sería algo similar a esto:

``` java
@Slf4j
@RequiredArgsConstructor
@Service
public class CinemaServiceImpl implements ICinemaService {

    private final CinemaRepository cinemaRepo;

    // Otras propiedades y métodos de la clase aquí

    @Override
    @Transactional
    public Cinema save(Cinema cinema) {
        log.info("save {}", cinema);

        try {
            return cinemaRepo.save(cinema);

        } catch (DataIntegrityViolationException e) {
            log.error("Error al guardar el cine: ", e);

            return null;
        }
    }

}
```

Como se ve, la clase `CinemaServiceImpl` implementa la interfaz `ICinemaService`, inyecta el repositorio `CinemaRepository`y tiene un método que guarda un cine. `cinema` es enviado al método `save`desde el controlador, y el método devuelve el cine guardado o null si se produjo un error.  

A continuación se vé como cambia el método para incluir los mensajes de éxito o error:

``` java
@Slf4j
@RequiredArgsConstructor
@Service
public class CinemaServiceImpl implements ICinemaService {

    private final HttpSession session;
    private final CinemaRepository cinemaRepo;

    // Otras propiedades y métodos de la clase aquí

    @Override
    @Transactional
    public Cinema save(Cinema cinema) {
        log.info("save {}", cinema);

        try {
            Cinema newCinema = cinemaRepo.save(cinema);

            String message = "Cine " + newCinema + " guardado correctamente.";

            session.setAttribute("message", message);
            session.setAttribute("messageType", "info");

            return newCinema;

        } catch (DataIntegrityViolationException e) {
            log.error("Error al guardar el cine: ", e);

            session.setAttribute("message", "El cine no ha podido guardarse.");
            session.setAttribute("messageType", "danger");

            return null;
        }
    }
```

Con `session.setAttribute("message", message);` se cambia el valor de la variable `message`, y con `session.setAttribute("messageType", "info");` se hace lo propio con la variable `messageType`. Mismo ocurre en caso de error.

A tener en cuenta: en `String message = "Cine " + newCinema + " guardado correctamente.";` hay que estar seguro de que la entidad `Cinema`sobreescribe (@Override) el método `toString()`.

El resultado es algo como lo que puede verse a continuación.

![Notificación activa](/assets/img/notificaciones.png){: .mx-auto.d-block :}

lo que se ve es lo que hemos definido en el fragmento `messageAlert.html` que hemos incluido en la plantilla de la vista.

# Cerrando la notificación

La notificación anterior estará disponible en la vista actual y en todas aquellas vistas que tengan el fragmento `messageAlert.html` incluido en la plantilla hasta que el mensaje deje de existir, es decir, el mensaje sea "". Recordar que, para que el fragmento se muestre, el contenido de `session.message` debe ser distinto de "".

Como la sesión se maneja desde el *backend*, es necesario gestionarla mediante una ruta que nos permita cambiar el valor de la variable `message`.

En la carpeta `controllers` creo el controlador `MessageController` siguiente:

```java
@Controller
public class MessageController {


    @PostMapping("/message")
    public String messageAlert(@RequestParam(value = "returnUrl", required = false) String returnUrl,
                                    HttpSession session) {

        session.setAttribute("message", "");

        if (!stringIsEmpty(returnUrl)) {
            return "redirect:" + returnUrl;
        } else {
            return "redirect:/";
        }
    }

}
```

A través de la ruta `/message` puedo cambiar el valor de `message` con: `session.setAttribute("message", "");` y luego volver a la ruta deseada si hemos pasado el parámetro `returnUrl`.

Recordemos que, cuando creamos el fragmento, definimos la ruta a la que apuntaría el formulario de la notificación `<form th:action="@{/message}" method="POST">`, y el formulario se envía al hacer clic sobre la `X` de cerrar la notificación.

Es importante que la ruta `/message` esté definida en nuestra configuración de seguridad. Si estamos usando Spring Security, en `SecurityConfig.class` debe haber algo como esto:

```java
    @Bean
    public SecurityFilterChain securityFilterChain(HttpSecurity http)  throws Exception {
        http
            .authorizeHttpRequests(authRequest -> authRequest

                // otras configuraciones

                .requestMatchers(HttpMethod.POST, "/message").permitAll()

                // otras configuraciones

                .anyRequest().authenticated()
            )

            // otras configuraciones

            return http.build();
    }
```

# Resumiendo

Para crear las notificaciones he detallado:

1. Crear un fragmento donde incluir el texto de los mensajes en nuestras vistas.
2. Inicializar las variables de sesión que contendrán el mensaje y el tipo de mensaje.
3. Actualizar el valor de las variables de mensaje para que estos se muestren.
4. Eliminar el mensaje desde el backend una vez mostrado.

# Mensajes encadenados

Si desde el controlador realizamos más de una gestión crítica, como por ejemplo actualizar un cine, y si este no está activo desactivar las salas de ese cine, podemos encadenar los mensajes. Para ello deberíamos leer el valor de la variable `message` en el segundo servicio, el que se ocupe de desactivar las salas, y añadir el aviso al nuevo mensaje, ya que sólo disponemos de un mensaje que puede ser mostrado a la vez:

``` java
String message = (String) session.getAttribute("message");
session.setAttribute("message", message + " Salas desactivadas correctamente.");
```

# Otros usos

Aplicando el método aquí descrito, es fácil realizar los cambios para gestionar, por ejemplo, un aviso de cookies con un botón de aceptar.

También es posible generar notificaciones que permitan consultar al usuario si quiere llevar a cabo una tarea o no: `"¿Desea borrar el cine? [Sí] [No]"`.

# Un ejemplo

Puedes ver una aplicación que implementa esté método en el repositorio de la aplicación [Cartelera DAW]({% post_url 2024-06-20-cartelera-daw %}).

- [Repositorio de Cartelera DAW](https://github.com/JavGuerra/cartelera-daw) 



