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

He creado un fragmento de código llamado `messageAlert.html` que he incluido en mi carpeta `fragments` dentro de templates en resources. Este es un ejemplo que puede modiicarse a voluntad para adaptar su diseño a la aplicación donde se usará.

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

Mediante `<input type="hidden" th:name="returnUrl" th:value="${returnUrl}">` indico a través de la respuesta de formulario al controlador, a qué página debo dirigirme después de realizar la operación. Esto me permitirá volver a la página donde estaba una vez se procese el borrado del mensaje. Uso de forma genérica `returnUrl` para indicar la ruta porque es útil para, por ejemplo, cuando llamo a un formulario y quiero saber dónde debo ir una vez enviado el contenido del formulario, como en este caso. Por ejemplo, si nuestra aplicación maneja cines, y añadimos, editamos o borramos un cine, mediante `returnUrl` puedo indicar al método `@PostMapping` qué vista mostrar tras realizar estas operaciones. Para poder hacerlo, cuando usé las vistas de crear, editar o borrar, añadí esta variable al modelo con:

``` java
model.addAttribute("returnUrl", "cinemas");
```
donde cinemas es la ruta a la que se debe llegar una vez el formulario de la ruta a la que vamos se procese. Un ejemplo:

```java
    @GetMapping("/create")
    public String createForm(Model model) {

        model.addAttribute("cinema", new Cinema());
        
        model.addAttribute("returnUrl", "cinemas");

        return "cinema/cinema-form";
    }
```

Sigamos. Con `th:classappend="${session.messageType} == 'danger'? 'alert-danger' : 'alert-info'" role="alert` determino qué tipo de alerta de Boostrap mostrar, si una destinada a informar (recuadro con fondo azul) o una destinada a alertar de un fallo (recuadro con fondo rojo). Esto lo consigo mediante la variable de sesión `messageType`.

Tras el icono SVG que se mostrará en el aviso, se incluye el mensaje de la notificación con `th:text="${session.message}"` dentro de la etiqueta `span`.

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

Si existe la sesión (que, salvo error, ya debería existir), compruebo que las variables `message` y `messageType` estén inicializadas, y si no lo están las inicializo a `""`.

Tras esto, ya podremos usar `session.getAttribute()` y `session.setAttribute()` para leer y para actualizar el valor de las variables de sesión desde nuestros servicios o controladores, como veremos.

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

Como se ve, la clase `CinemaServiceImpl` implementa la interfaz `ICinemaService`, inyecta el repositorio `CinemaRepository` y tiene un método que guarda un cine. `cinema` es enviado al método `save` desde el controlador, y el método devuelve el cine guardado o null si se produjo un error.  

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
}
```

Con `session.setAttribute("message", message);` se cambia el valor de la variable `message`, y con `session.setAttribute("messageType", "info");` se hace lo propio con la variable `messageType`. Mismo ocurre en caso de error.

A tener en cuenta: en `String message = "Cine " + newCinema + " guardado correctamente.";` hay que estar seguro de que la entidad `Cinema` sobreescribe (@Override) el método `toString()` para poder usarlo para crear el String.

El resultado es algo como lo que puede verse a continuación.

![Notificación activa](/assets/img/notificaciones.png){: .mx-auto.d-block :}

lo que se ve es lo que hemos definido en el fragmento `messageAlert.html` que hemos incluido en la plantilla de la vista.

# Cerrando la notificación

La notificación anterior estará disponible en la vista actual y en todas aquellas vistas que tengan el fragmento `messageAlert.html` incluido en la plantilla hasta que el texto de la notificación deje de existir. Hay que recordar que, para que el fragmento se muestre, el contenido de `session.message` debe ser distinto de `""`.

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

Con `.requestMatchers(HttpMethod.POST, "/message").permitAll()` la ruta `/message` es ahora visible.

Si las notificaciones las llevamos a cabo al realizar procesos que sólo están disponibles una vez el usuario está autenticado, es buena idea que, al hacer *logout* se borre también la notificación con algo parecido a:

```java
    @RequestMapping("/logout")
    public String logout(HttpSession session, Model model) {

        SecurityContextHolder.getContext().setAuthentication(null);

        model.addAttribute("returnUrl", "/");

        session.setAttribute("message", "");

        return "logout";
    }
```

# Resumiendo

Para crear las notificaciones he detallado:

1. Crear un fragmento donde incluir el texto de la notificación en nuestras vistas.
2. Inicializar las variables de sesión que contendrán la notificación y el tipo de notificación.
3. Actualizar el valor de las variables de notificación para que estos se muestren.
4. Eliminar la notificación desde el backend una vez mostrado.

# Otros usos

Aplicando el método aquí descrito, es fácil realizar los cambios para gestionar, por ejemplo, un aviso de cookies con un botón de aceptar.

También es posible generar notificaciones que permitan consultar al usuario si quiere llevar a cabo una tarea o no: `"¿Desea borrar el cine? [Sí] [No]"`.

# Bonus: Cierre de mensajes

Mediante el método descrito, la notificación nos acompañará por toda la aplicación hasta que la cerremos. Lo ideal sería que, al cambiar de página, la notificación desaparezca. Te muestro cómo hacerlo añadiendo una nueva variable `messageActivated` a la configuración en `SessionFilter` que indique el estado del mensaje.

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

            // Cierre de notificaciones al cambiar de página

            if (session.getAttribute("messageActivated") == null)
                session.setAttribute("messageActivated", false);

            if ((boolean) session.getAttribute("messageActivated")) {
                session.setAttribute("message", "");
                session.setAttribute("messageActivated", false);
            }

            if (!Objects.equals((String) session.getAttribute("message"), ""))
                session.setAttribute("messageActivated", true);
            
        }

        chain.doFilter(request, response);
    }

    @Override
    public void destroy() {}
}
```

En estos tres primeros bloques de sentencias, primero compruebo que la variable de sesión `messageActivated` está inicializada, como hacía con las otras variables de sesión, y si no lo está, la inicializo a false.

Como cada vez que se muestra una página en la aplicación se lanza `SessionFilter`, en el siguiente bloque de sentencias compruebo si `messageActivated`, y si lo está, la desactivo, y vacío `message` para que no vuelva a mostrarse. Recordemos que el fragment `messageAlert.html` sólo muestra la notificación si su contenido no es `""`.

En el último bloque, si `message` contiene texto y es la primera vez que se va a mostrar la notificación, el bloque anterior no la habrá borrado. Entonces activo `messageActivated` para que pueda la notificación pueda ser eliminada la siguiente vez que se muestre una página.

Como se aprecia, `messageActivated` actúa como indicador de borrado, y en este proceso es determinante el orden en el que se hacen las comprobaciones.

Y esto tiene una serie de ventajas más. No necesitas cerrar la notificación en el backend, por lo que no es necesario crear `MessageController` ni dar de alta la ruta en `SecurityConfig.class`. Cuando le das a la `X` de cerrar, la notificación deja de mostrarse, y como ya se mostró, no volverá a aparecer en otras páginas gracias a `SessionFilter`. Tampoco necesitas poner el fragmento en todas las páginas, sino sólo en aquellas en las que es posible que se muestren notificaciones.

Este es el fragmento `messageAlert.html` actualizado sin el formulario:

```html
<div th:fragment="messageAlert" th:if="${session.message != ''}" class="container">
    <div class="alert alert-dismissible fade show" th:classappend="${session.messageType} == 'danger'? 'alert-danger' : 'alert-info'" role="alert">
        <div>
            <svg xmlns="http://www.w3.org/2000/svg" fill="none" viewBox="0 0 24 24" stroke-width="1.5" stroke="currentColor" width="24" height="24">
                <path stroke-linecap="round" stroke-linejoin="round" d="M14.857 17.082a23.848 23.848 0 0 0 5.454-1.31A8.967 8.967 0 0 1 18 9.75V9A6 6 0 0 0 6 9v.75a8.967 8.967 0 0 1-2.312 6.022c1.733.64 3.56 1.085 5.455 1.31m5.714 0a24.255 24.255 0 0 1-5.714 0m5.714 0a3 3 0 1 1-5.714 0M3.124 7.5A8.969 8.969 0 0 1 5.292 3m13.416 0a8.969 8.969 0 0 1 2.168 4.5" />
            </svg>&nbsp;&nbsp;
            <span class="align-middle" th:text="${session.message}"></span>
        </div>
        <button class="btn-close" data-bs-dismiss="alert" aria-label="Cerrar"></button>
    </div>
</div>
```

# Mensajes encadenados

Si desde el controlador realizamos más de una gestión crítica, como por ejemplo actualizar un cine, y si este no está activo desactivar las salas de ese cine, podemos encadenar los mensajes. Para ello deberíamos leer el valor de la variable `message` en el segundo servicio, el que se ocupe de desactivar las salas, y añadir el aviso al nuevo mensaje, ya que sólo disponemos de un mensaje que puede ser mostrado a la vez:

``` java
String message = (String) session.getAttribute("message");
session.setAttribute("message", message + " Salas desactivadas correctamente.");
```

# Un ejemplo

Puedes ver una aplicación que implementa esté método en el repositorio de la aplicación [Cartelera DAW]({% post_url 2024-06-20-cartelera-daw %}).

- [Repositorio de Cartelera DAW](https://github.com/JavGuerra/cartelera-daw) 
