# Spring MVC - Introducción
Spring MVC es un framework web basado en Servlets que viene incluido en Spring Framework (spring-webmvc). Esta diseñado siguiendo el patrón de diseño de Front Controller, que en Spring MVC es conocido como DispatcherServlet y sus funciones son:
- Enviar las peticiones (requests) a los manejadores (handlers) para que sean procesadas.
  - El default handler son los controladores (@Controller, @RequestMapping).
- Encargado de resolver las vistas (views).
A partir de Spring 3.0 se pueden crear RESTFul Web Services utilizando la anotación @RestController y @PathVariable. Además, esta basado en Spring IOC container (Inyección de Dependecias). Y Se integra muy fácil con otros proyectos de Spring como Spring Boot, Spring Data JPA, Spring Security, Spring REST, etc.

### Controlador en Spring MVC
Un controlador (Controller) en Spring MVC es una clase normal a la cual se le agrega la anotación **@Controller** a nivel de clase. En una aplicación web estos métodos principalmente están marcados con las anotaciones @GetMapping, @PostMapping, y @RequestMapping (Action Controller). Los métodos pueden tener cualquier nombre y deben regresar un String (nombre de la vista). Los métodos son ejecutados al ser invocados por medio de URL especificada como parámetro en las anotaciones @GetMapping, @PostMapping, etc.
```java
@Controller
public class HomeController {

  //localhost:8080/miUrl
  @GetMapping("/miUrl")
  public String mostrarHome() {
    //Mi lógica de negocio
    return "home";
  }
}
```
### Thymeleaf
Thymeleaf es un motor de plantillas para aplicaciones web desarrolladas con Java. Es algo similar a los JSPs, con algunas diferencias. Comúnmente es utilizado con Spring Boot para generar vistas con código HTML para aplicaciones web.
Agrega la siguiente dependecia al archivo **pom.xml**:
```xml
<dependency>
  <groupId>org.springframework.boot</groupId>
  <artifactIf>spring-boot-starter-thymeleaf</artifactIf>
</dependency>
```
Para utlizar **Thymeleaf** en un archivo HTML se debe agregar el namespace.
```html
<!DOCTYPE html>
<html xmlns:th="http://www.thymeleaf.org">
<head>
```
Enviando un atributo desde el controlador hacia la vista.
```java
...
  @GetMapping("/")
  public String mostrarHome(Model model) {
    model.addAttribute("mensaje", "Bienvenidos a la página!");
    return "home";
  }
```
```html
<!DOCTYPE html>
<html xmlns:th="http://www.thymeleaf.org">
<head>
  <meta charset="ISO-8859-1">
  <title></title>
</head>
<body>
  <h1 th:text="${mensaje} + ':D'"></h1>  
</body>
```
#### Iteraciones en thymeleaf
Las iteraciones se pueden realizar con la expresión **th:each**, similar a un for en Java. Y se puede iterar sobre diferentes tipos de datos como:
- List
- Map
- Iterable
Ejemplo:

Vista (detalle.html)
```html
<tr th:each="tmpEmp : ${empleos}">
  <td th:text="${tmpEmp}"/>
</tr>
```
Controlador
```java
  @GetMapping("/detalle")
  public String mostrarDetalle(Model model) {
    List<String> lista = new LinkedList<>();
    
    lista.add("Ingeniero de Sistemas");
    lista.add("Contador público");
    model.addAttribute("empleos", lista);
    
    return "home";
  }
```
#### Condicionales en Thymeleaf
- Operador Elvis (?:)
El operador Elvis permite renderizar texto dentro de un elemento HTML, dependiendo de una expresión Booleana. Es muy similar al operador ternario en otros lenguajes de programación.
Ejemplo:
```html
<td th:text="${usuario.estatus == 1} ? 'ACTIVO' : 'BLOQUEADO'"></td>
```
- if-unless
La expresión **if-unless** permite renderizar un elemento HTML, dependiendo de una expresión Booleana. Es muy similar a un if-else en otros lenguajes de programación.
Ejemplo
```html
<td>
  <!-- Se repite la misma condición -->
  <span th:if="${alumno.genero == 'F'}"> Femenino </span>
  <span th:unless="${alumno.genero == 'F'}"> Masculino </span>
</td>
```
#### Urls relativas al ContextPath en Thymeleaf
Las Urls relativas al ContextPath son las que son relativas al directorio (ROOT) de una aplicación web, una vez que están publicadas en el servidor. En un proyecto web cuando se utiliza Thymeleaf como motor de plantillas, los recursos estáticos deben guardarse en el directorio **src/resources/static**
Ejemplo:
- Para incluir el archivo CSS style.css en una vista se utilizaria la siguiente expresion:
```html
<link th:href="@{/css/style.css}" rel="stylesheet">
```
- Para incluir el archivo javascript funciones.js en una vista se utilizaria la siguiente expresion:
```html
<script th:src="@{/js/funciones.js}"></script>
```
- Para incluir la imagen foto.png en una vista se utilizaria la siguiente expresion:
```html
<img th:src="@{/images/foto.png}" width="136" height="136">
<img th:src="@{/images/{img} ( img=${vacante.imagen} ) }" width="136" height="136">
```
### Anotación @RequestMapping
La anotación @RequestMapping cuando se incluye antes de la declaración de un método en un controlador sirve para especificar la URL y el Método HTTP (POST, GET, DELETE, PUT, etc) al que estará mapeado el método.
En versiones anteriores se Spring 4.3 se utilizaba esta anotación para mapear los métodos de los controladores a las URLs. A partir de la versión **Spring 4.3** se agregaron las siguientes variaciones de la anotación **@RequestMapping**.
- **@GetMapping("/lista")** | @RequestMapping(value="/lista", method=RequestMethod.GET)
- **@PostMapping("/guardar")** | @RequestMapping(value="/guardar", method=RequestMethod.POST)
- **@DeleteMapping("/borrar")** | @RequestMapping(value="/borrar", method=RequestMethod.DELETE)
- **@GetMapping("/actualizar")** | @RequestMapping(value="/actualizar", method=RequestMethod.PUT)

### URL dinámicas
Las URLs dinámicas son usadas para obtener parte de ellas en un método de un controlador (como parámetro). Una URL dinámica puede contener 1 o varias PathVariable (parámetros entre llaves {}).
Para vincular (binding) un parámetro de una URL dinámica a un parámetro en el controlador se utiliza la anotación @PathVariable:
```java
//localhost:8080/detalle/07
@GetMapping("/detalle/{id}")
public String mostrarDetalle(@PathVariable("id") int idVacante) {
  System.out.println("PathVariable: " + idVacante);
  return "detalle";
}
```
Un método puede tener cualquier número de anotaciones @PathVariable
```java
public String mostrarDetalle(@PathVariable("id") int idVacante, @PathVariable("fecha") Date fecha){}
```
### Binding (vincular) Request Parameters
Los parámetros de una petición HTTP pueden ser vinculados a un parámetro en un método en el controlador. Para vincular (binding) un parámetro de una petición HTTP se utiliza la anotación **@RequestParam** de Spring MVC. Se pueden vincular parámetros de una petición tipo GET y POST.
```java 
@GetMapping("/detalle")
public String verDetalle(@RequestParam("idCliente") int idCliente) {
  //Procesamiento del parámetro.
  System.out.println("RequestParam: " + idCliente);
  return "detalle";
}
```
Los parámetros usados con esta anotación son REQUERIDOS por default.
Si se requiere que el parámetro sea opcional, se tiene que agregar el atributo "required" con el valor "false". Ejemplo: (@RequestParam(name="id", required=false))

Ejemplo 1

**Controlador**
```java
@GetMapping("/detalle")
public String verDetalle(@RequestParam("idCliente") int idCliente) {
  //Procesamiento del parámetro.
  System.out.println("RequestParam: " + idCliente);
  return "detalle";
}
```
**Link HTML (GET)**
```html
<!-- localhost:8080/detalle?idCliente=07 -->
<a th:href="@{/detalle(idCliente=${cliente.id}) }"> Ver detalles </a>
```

Ejemplo 2

**FORM HTML(POST)**
```html
<form action="save" method="POST">
  Titulo
  <input type="text" name="titulo"/>
  <button type="submit"> Guardar </button>
</form>
```
**Controlador**
```java
@PostMapping("/save")
public String guardar( @RequestParam("titulo") String tituloTmp ) {
  System.out.println("Titulo: " + tituloTmp);
  return "detalle";
}
```
### Inyección de Dependencias
Procedimiento para inyectar (@Autowired) una clase de servicio en un controlador:
1. Anotar la clase de servicio (la implementación) con la anotación **@Service** de Spring Framework.
```java
@Service
public class clientesServiceImpl implements IClientesService {
  //Métodos de lógica de negocio
  ...
}
```
Por defecto, las clases de servicio tienen alcance Singleton (una sola instancia de la clase para toda la aplicación).
2. Utilizar los métodos de la clase de servicio en el controlador.
```java
@Controller
public class HomeController {

  @Autowired
  private IClientesService serviceClientes;
  
  ...
}
```
### Thymeleaf Fragments - Layouts (Aplicando plantillas HTML)
Normalmente las aplicaciones web comparten componentes (fragmentos de código HTML) que se repiten en cada vista, como cabecera, menu, pie de página, etc.

Para este tipo de diseño de plantillas, Thymeleaf incluye las siguientes expresiones:
**templates/fragments/menu.html** Estamos definiendo un Fragmento de código HTML con el nombre de "menu-principal"
```html
<nav th:fragment="menu-principal">
  <li>
    <a>Home</a>
  </li>
</nav>
```
Una vez definidos los fragmentos, solo los insertamos. Los fragmentos se insertarán dentro del elemento HTML donde se declaren (th:insert) en este caso: <header>
```html
...
  <body>
    <header th:insert="fragments/menu.html :: menu-principal" >
    </header>
    ...
```
    
### Data Binding en Spring MVC
Data Binding es el mecanismo mediante el cual Spring MVC extrae dinámicamente los datos de entrada del usuario y los asigna a objetos de Modelo de nuestra aplicación. Cuando utilizamos Data Binding en Spring MVC, la conversión de los tipos de datos se hace automáticamente. Spring MVC convierte los parámetros de la aplicación HTTP de tipo String, al tipo de dato según nuestra clase de modelo (int, Date, double, etc). La validación de los datos de entrada también se hace automáticamente.

```html
<form th:action="@{/guardar}" method="post">
  
  <input type="text" name="nombre" />
  
  <select name="estado" >
    <option value="creado"> Creado </option>
    <option value="aprobado"> Aprobado </option>
  </select>
  
  <textarea name="detalle"></textarea>
  
  <button type="submit"> Guardar </button>
</form>
```

Data Binding asignará cada parámetro de la petición a cada propiedad del Modelo, pero solo si el nombre de un parámetro de la petición (input del formulario) coincide con el de una propiedad de nuestro objeto de modelo.

```java
public class Cliente {
  private int id;
  private String nombre;
  private String estado;
  private String detalle;
  
  //getters y setters
}
```

Controller
```java
@PostMapping("guardar")
public String guardar(Cliente cliente) {
  serviceClientes.guardar(cliente);
  return "listado";
}
```
### Anotación @InitBinder
**Error
Failed to convert from type [java.lang.String] to type [java.util.Date] for value '09-04-2019' ; nested exception is java.lang.IllegalArgumentException**

El error anterior significa que Spring no puede convertir el valor del parámetro de la petición "09-04-2019" a un tipo Date. Y la causa es que por defecto Spring espera la fecha en el formato según este configurada en el sistema operativo donde este la aplicación.

La anotación **@InitBinder** permite crear métodos para configurar el Data Binding directamente en el ordenador.
Los métodos marcados con la anotación @InitBinder **no regresa ningún valor**, normalmente son declarados como void. Comúnmente reciben un parámetro de tipo WebDataBinder.
El siguiente ejemplo muestra como utilizar @InitBinder para configurar CustomDateEditor para todas las propiedades de tipo java.util.Date

```java
@InitBinder
public void initBinder(WebDataBinder webDataBinder) {
  SimpleDateFormat dateFormat = new SimpleDateFormat("dd-MM-yyyy");
  webDataBinder.registerCustomEditor(Date.class, new CustomDateEditor(dateFormat, false));
}
```
(Utilizar dentro del controlador).

### Control de errores en Data Binding
Cuando Spring MVC realiza Data Binding pueden surgir errores. Por ejemplo:

- Errores de formato. Por ejemplo el usuario ingresa la fecha 32-02-2020.
- Errores de conversión. Por ejemplo en el atributo salario (private Double salario) el usuario ingresa un valor alfanumérico.
- Omitir campos requeridos.

Para revisar posibles errores despúes del Data Binding debemos agregar un parámetro de tipo **BindingResult** INMEDIATAMENTE despúes de la clase de modelo. Ejemplo:
```java
@PostMapping("/guardar")
public String guardar(Cliente cliente, BindingResult result) {
  if (result.hasErrors()) {
    // si hay errores vuelve al formulario
    return "formulario";
  }
  serviceClientes.guardar(cliente)
  return "listado";
}
```

Despúes de agregar el parámetro BindigResult, podríamos verificar si hay errores y en case de haber, lo correcto sería mostrarselos al usuario en la vista para su corrección.
**Desplegar errores por consola**
```java
for (ObjectError error: result.getAllErrors()) {
  System.out.println("Ocurrio un error: " + error.getDefaultMessage());
}
```

### BindingResult - Desplegar errores en la vista
Para vincular los errores en la vista, también debemos enviar al formulario un objeto de nuestra clase de modelo (se declara como parametro):
```java
@GetMapping("/crear")
public String crear(Cliente cliente) {
  return "formulario";
}
```
Vincular nuestro HTML FORM con el objeto de modelo (th:object)
```html
<form th:action="@{/guardar}" method="post" th:object="${cliente}" >
```
Utilizar la expresión **${#fields.hasErrors('*')}** es un* condicional para preguntar si existieron errores al realizarse el Data Binding.
En caso de Existir errores, recorrer (th:each) la colección de errores existente: **${#fields.errors('*')}** .* 

```html
<div th:if="${#fields.hasErrors('*')}"  class='alert alert-danger' role='alert'>
  Por favor corrija los siguientes errores:
  <ul>
    <li th:each="err : ${#fields.errors('*')}" th:text="${err}" ></li>
  </ul>
</div>
```

### Redirect en Spring MVC
```java
@PostMapping("/guardar")
public String guardar(Cliente cliente, BindingResult result) {
  ...
  // Petición HTTP tipo GET -> localhost:8080/clientes/listado
  return "redirect:/clientes/listado";
}
```

### Flash Atributtes
Lo utilizamos para enviar atributos implicitamente dentro de un Redirect. 
Los **atributos flash** proporcionan una forma de almacenar atributos para poder ser usados en otra petición diferente. Comúnmente son usados cuando hacemos una redirección utilizando el patrón Post/Redirect/Get.
Los atributos flash son almacenados temporalmente antes de hacer el redirect (tipicamente en la sesión) para tenerlos disponibles después del redirect. Después del redirect son eliminados de la sesión automáticamente.

Ejemplo **controlador**
```java
@PostMapping("/guardar")
public String guardar(Cliente cliente, BindingResult result, RedirectAttributes attributes) {
  ...
  attributes.addFlashAttribute("msg", "Registro guadado");
  return "redirect:/clientes/listado";
}
```
Ejemplo **vista**
```html
<div th:if="${msg != null}" class='alert alert-success' th:text="${msg}" role='alert'></div>
```
### Vincular inputs apropiedades de clases de modulo
Lo utilizaremos para que al momento llenar un formulario y cometer un error se vuelva a cargar el formulario pero con los demás campos llenos.
```html
<input type="text" ... th:field="*{nombre}">
```

### Generar input de tipo select de forma dinámica
Ejemplo en la vista
```html
<select class="form-control" name="" >
  <option th:each="categoria : ${categorias}" th:value="${categoria.id}" th:text="${categoria.nombre}" ></option>
</select>
```
### Configuración de Spring Boot para subir archivos
Agregar las siguientes propiedades en el archivo **application.properties**

```
# CONFIGURACIÓN MULTIPART (SUBIDA DE ARCHIVOS)
# ¿Habilitamos subida de archivos?
spring.servlet.multipart.enabled=true
# Directorio intermedio para subir archivos (Linux/MAC)
# spring.servlet.multipart.location=/tmp
# Directorio temporal para subir archivos (Windows)
spring.servlet.multipart.location=c:/tmp
# Maximo tamaño de archivos que se pueden subir
spring.servlet.multipart.max-file-size=2MB
```
Crear un directorio para guardar los archivos que se subirán. Por ejemplo:

- Windows
  - C:\clientes\img-clientes
- Linux/MAC
  - El directorio DEBE TENER PERMISOS DE ESCRITURA/LECTURA para el usuario que ejecuta la aplicación de Spring Boot.
  
```
$ sudo mkdir -p /clientes/img-clientes
$ sudo chown -R juan:juan /clientes/img-clientes
$ ls -l /clientes
drwxr-xr-x 2 juan juan 4096 feb 16 09:12 img-clientes
```

### Clase Helper para guardar el archivo a disco duro
**Controlador**
```java
@PostMapping(value="/save")
public String guardar(... @RequestParam("archivoImagen") MultipartFile multiPart ...) {
  ...
  if (!multiPart.isEmpty()) {
    //String ruta = "/clientes/img-clientes/"; //Linux/MAC
    String ruta = "c:/clientes/img-clientes/"; //Windows
    String nombreImagen = Utileria.guardarArchivo(multiPart, ruta);
    if (nombreImagen != null) { //La imagen si se subio
      // Procesamos la variable nombreImagen
      cliente.setImagen(nombreImagen);
    }
  }
  ...
}
```
**Formulario HTML**
```html
<form action="#" enctype="multipart/form-data" >
  <input type="file" name="archivoImagen" />
  <input type="submit"/>
</form>
```
*Nota: En este caso el valor del atributo name del input de tipo file (archivoImagen) no es el nombre de la propiedad de la clase de modelo, porque no se puede convertir un tipo File a String.*

**Clase Utileria**
```java
public class Utileria {
  public static String guardarArchivo(MultipartFile multiPart, String ruta) {
    // Obtenemos el nombre original del archivo
    String nombreOriginal = multiPart.getOriginalFilename();
    try {
      // Formamos el nombre del archivo para guardarlo en el disco duro.
      File imageFile = new File(ruta + nombreOriginal);
      System.out.println("Archivo: " + imageFile.getAbsolutePath());
      // Guardamos fisicamente el archivo en HD
      multiPart.transferTo(imageFile);
      return nombreOriginal;
    } catch (IOException e) {
      System.out.println("Error " + e.getMessage());
      return null;
    }
  }
}
```
