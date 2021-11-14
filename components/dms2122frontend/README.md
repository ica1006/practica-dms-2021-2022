# DMS 2021-2022 Frontend service

This frontend serves as the human interface for the other services of the appliance.

## Installation

Run `./install.sh` for an automated installation.

To manually install the service:

```bash
# Install the service itself.
./setup.py install
```

## Configuration

Configuration will be loaded from the default user configuration directory, subpath `dms2122frontend/config.yml`. This path is thus usually `${HOME}/.config/dms2122frontend/config.yml` in most Linux distros.

The configuration file is a YAML dictionary with the following configurable parameters:

- `service_host` (mandatory): The service host.
- `service_port` (mandatory): The service port.
- `debug`: If set to true, the service will run in debug mode.
- `app_secret_key`: A secret used to sign the session cookies.
- `auth_service`: A dictionary with the configuration needed to connect to the authentication service.
  - `host` and `port`: Host and port used to connect to the service.
- `backend_service`: A dictionary with the configuration needed to connect to the backend service.
  - `host` and `port`: Host and port used to connect to the service.

## Running the service

Just run `dms2122frontend` as any other program.

## Services integration

The frontend service is integrated with both the backend and the authentication services. To do so it uses two different API keys (each must be whitelisted in its corresponding service); it is a bad practice to use the same key for different services, as those with access to the whitelist in one can create impostor clients to operate on the other.

## Authentication workflow

Most, if not all operations, require a user session as an authorization mechanism.

Users through this frontend must first log in with their credentials. If they are accepted by the authorization service, a user session token will be generated and returned to the frontend. The frontend will then store the token, encrypted and signed, as a session cookie.

Most of the interactions with the frontend check and refresh this token, so as long as the service is used, the session will be kept open.

If the frontend is kept idle for a long period of time, the session is closed (via a logout), or the token is lost with the cookie (e.g., closing the web browser) the session will be lost and the cycle must start again with a login.

## UI pages and components

The UI has the following templates hierarchy and structure:

- `base.html`: Base page. Blocks defined: `title`, `pagecontent`, `footer`.
  - `login.html`: Login form page. Macros used: `flashedmessages`, `submit_button`.
  - `base_logged_in.html`: Base page when a user is logged in. Blocks used: `pagecontent`. Blocks defined: `contentheading`, `contentsubheading`, `maincontent`. Macros used: `flashedmessages`, `navbar`.
    - `home.html`: Home page/dashboard. Blocks used: `title`, `contentheading`, `maincontent`.
    - `student.html`: Main student panel. Blocks used: `title`, `contentheading`, `maincontent`. Blocks defined: `subtitle`, `studentcontent`.
    - `teacher.html`: Main teacher panel. Blocks used: `title`, `contentheading`, `maincontent`. Blocks defined: `subtitle`, `teachercontent`.
    - `admin.html`: Main administration panel. Blocks used: `title`, `contentheading`, `maincontent`. Blocks defined: `subtitle`, `administrationcontent`.
    - `admin/users.html`: Users administration listing. Blocks used: `contentsubheading`, `administrationcontent`. Macros used: `button`.
    - `admin/users/new.html`: User creation form page. Blocks used: `contentsubheading`, `administrationcontent`. Macros used: `button`, `submit_button`.
    - `admin/users/edit.html`: User editing form page. Blocks used: `contentsubheading`, `administrationcontent`. Macros used: `button`, `submit_button`.

The following macros/components are provided:

- `macros/navbar.html`
  - `navbar(roles)`: The top navigation bar.
    - Parameters:
      - `roles`: A list of role names.
- `macros/flashedmessages.html`
  - `flashedmessages()`: The bottom panel with the flashed messages.
- `macros/buttons.html`
  - `button(color_class, href, content, onclick=None)`: A link with the appearance of a button.
    - Parameters:
      - `color_class`: A string with a CSS class to use to colorize it (e.g., `bluebg`, `redbg`, `yellowbg`)
      - `href`: The link to follow when clicked.
      - `content`: The contents (usually a string) to display inside the button.
      - `onclick`: If provided, a JavaScript snippet to be run when clicked.
  - `submit_button(color_class, content)`: Special button that submits the containing form. Uses the `button` macro providing a blank link `'#'` as the `href` and a form-submitting statement as the `onclick` argument.
    - Parameters:
      - `color_class` (See `button`)
      - `content` (See `button`)

## Arquitectura del frontend

Trataremos el diseño del frontend y sus actualizaciones necesarias para implementar determinadas funciones

  - Archivo dms2122frontend

Necesitamos crear los metodos necesarios para tareas especificas como por ejemplo:
      Funcion de crear preguntas para el profesor
      Funcion de editar preguntas para el profesor
      Funcion de contestar preguntas para el alumno
      Funcion de revision de las preguntas para el alumno
Y mas funciones necesarias para que sea lo mas completa posible.

  - Web

Para poder implementar nuestros metodos creados en el fichero anterior del frontend, deberemos actualizar nuestros ficheros           studentendpoints.py y teacherendpoints.py de la parte web. Los cuales se actualizan con otros metodos necesarios para que haga determinadas  funciones dentro de la misma web de nuestra practica, como pueden ser navegar entre los diferentes htmls dependiendo de las rutas creadas en  el frontend.

  - Templates

Por ultimo necesitamos que los html, los cuales seran los ficheros que se muestren en la web y seran modificados para mejoras o arreglos de la misma, puedan estar organizados. Por ello, hemos creado dos carpetas, una de student y otra de teacher en las cuales se crearan otros ficheros para cada seccion que necesite nuestros metodos creados en el frontend (organizados en los ficheros web mencionados anteriormente).
Ficheros creados para algunos metodos, por ejemplo:
      Fichero check para alumnos (funcion de revision)
      Fichero answer para alumnos (funcion de contestar)
      Fichero student para alumnos (interfaz de alumnos)
      Fichero create para profesor (funcion crear preguntas)
      Fichero edit para profesor (funcion editar preguntas)
