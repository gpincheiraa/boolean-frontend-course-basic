# Boolean curso profesional de frontend nivel básico

Bienvenido al curso gratuito de desarrollo frontend profesional nivel básico. 

El objetivo de este curso es dar una mirada completa sobre el proceso de desarollar una aplicación frontend utilizando el lenguaje *javascript*. 

Las librerías utilizadas en este curso fueron seleccionadas no por ser las mejores ni las más populares, sino que por ser las que permitían ilustrar de mejor manera los conceptos escenciales de un proceso de desarollo orientado a la integración continua y los lanzamientos a producción tempranos.

El proceso de desarrollo utilizado en este curso permite a quienes lo utilicen de la manera correcta, medir la calidad del código que se produce, la promoción de buenas prácticas e implementación de patrones de diseño, las pruebas automatizadas, la integración del código desarrollado en equipos distribuidos, el versionado del código y el proceso de gestión de tickets de incidencias o mejoras; todos estas ganancias traducidas finalmente en código más fácil de escalar y preparado para ser modificado en un futuro.

Revisaremos de manera breve los conceptos importantes para seguir este proceso integrando herramientas simples pero poderosas de manera de lograr construir un proceso de desarrollo que asegure que el comportamiento que definimos que nuestro codigo debía tener, perdure en el tiempo sin importar las modificaciones que se hagan a futuro, y que de ser el caso en que algunos de estos comportamientos se vean corrompidos por la introducción de nuevo código, nos avise de manera de poder tomar acciones frente a ello.


------ Imagen que logre describir de manera gráfica el proceso de desarrollo ---------


Indice de capítulos

 - Cápitulo 1: Ambiente de desarollo, TDD , AngularJS y Snapshot Testing.


## Cápitulo 1: Ambiente de desarollo, TDD , AngularJS y Snapshot Testing.

En este primer capítulo discutiremos como desarrollar una simple funcionalidad usando la metodología de desarrollo liderado por pruebas (TDD por sus siglas en inglés). 

En concreto revisaremos:
 - Configurar un entorno de desarrollo mínimo.
 - Utilizar git para versionar las aplicaciones.
 - Comprender los conceptos básicos de TDD y construir una funcionalidad simple.
 - Introducción a AngularJS.
 - Snapshot testing de templates compilados por AngularJS.

### Configurar un entorno de desarrollo mínimo.

Para nuestro entorno y proceso de desarollo necesitaremos cumplir los siguientes requisitos:

- Una terminal capaz de comprender comandos unix. (Para windows recomendamos

Requerimientos
NodeJS ≥ 8.4.0 usando NVM
Git

## Control de versiones: Git
Usaremos Git y Github para la posterior integración de un proceso que nos permitirá hacer deploy continuo y generar versiones de la aplicación.
Partiremos creando la carpeta vacía e iniciando git en ella.

```unix
git init 
git remote add origin <url_de_mi_repositorio_en_github>
```

Creemos los siguientes 3 archivos en la raíz del repositorio:

**package.json**

```json
{
  "name": "angularjs-tdd-jest",
  "version": "0.1.0",
  "scripts": {
    "test": "jest --coverage --verbose",
    "tdd": "jest --watch --verbose",
    "check-coverage": "npm test | http-server -so -p 9000 coverage/lcov-report"
  },
  "devDependencies": {
    "babel-preset-es2015": "^6.9.0",
    "babel-preset-stage-3": "^6.24.1",
    "http-server": "^0.10.0",
    "jest-cli": "^20.0.1"
  },
  "engines": {
    "node": ">=8.4.0"
  }
}
```
Hay 2 cosas importantes que notar en el archivo `package.json`:

  - scripts: Son los scripts de npm que se corren a través del comando `npm run nombre_script`. El script test también puede correrse utilizando `npm test`. Esta diferencia tiene que ver porque existen [scripts por defecto](https://docs.npmjs.com/misc/scripts#description) dentro de los cuales test es uno de ellos. Los comandos ejecutados a través de los npm scripts por ejemplo `jest` son archivos de linea de comandos instalados a través de los paquetes de dependencias. Si queremos ver estos archivos, una vez instaladas las dependencias, podemos encontrarlos en la ruta `node_modules/.bin/`.

  - devDependencies: Son las dependencias de desarrollo de nuestro proyecto. Estos paquetes nos serviran para ejecutar comandos que nos serán de utilidad por ejemplo `http-server` que nos servirá para correr un servidor local donde veremos en el navegador el informe de cobertura de los test.


**.gitignore**

```git
node_modules
coverage
npm-debug.log
dist
```

.babelrc

```json
{
  "presets": [ "es2015", "stage-3"]
}
```

##CAMBIAR EXPLICACIÓN DE BABEL EXPLICANDO POR EJEMPLO EL STAGE-3 Y SOLAMENTE MENCIONAR CUALES SON LAS FUNCIONALIDADES QUE USAREMOS EN ESTE PRIMER ARCHIVO

Ahora agregamos esos archivos y creamos el commit correspondiente:

```
git add package.json .gitignore .babelrc
git commit -m "Adding development environment configuration"
```

Finalmente instalaremos estas dependencias corriendo el comando:
```
npm install
```

## Explicando la configuración básica de Jest

Jest es cero-configuración, solo necesitamos seguir 2 convenciones: la carpeta que contendrá la suite de test se debe llamar **__test__** y los archivos con los test deben tener la extensión **.spec.js**, ¡¡¡Super simple!!!

## Crear una aplicación con TDD

Crearemos la clásica lista “to-do list”, pero esta vez usando la metodología TDD para escribir nuestro código y enfocarnos en código orientado a componentes.
Lo primero es simplemente escribir nuestra primera prueba basado en el ciclo “red-green-refactor”.
Escribiremos una prueba unitaria en el archivo `todoList.component.spec.js` dentro del directorio `./__test__/`.

```
import { TodoListController } from "../src/components/todoList.component"

describe("TodoListController", () => {
    let controller;

    beforeEach(() => {
        controller = new TodoListController()
    });

    it("Should have a defined controller", () => {
        expect(controller).toBeInstanceOf(TodoListController)
    });
});
```

Se ve simple verdad?, lo primero es importar el código que vamos a probar. En este punto estamos diseñando nuestro código con código. Pensar en el proceso mental de diseñar/modelar y escribir esas ideas en nuestros test.

En este pequeño ejemplo nuestro código ya está siendo diseñado e incluye:

- El nombre del archivo y la clase que se debiese exportar en el componente (todoList.component) en la primera línea
- Un escenario común (línea 6) que se correra por cada uno de nuestros test donde se crea una nueva instancia de nuestro controlador.
- Un test básico que comprueba si el constructor de la clase funciona correctamente y nos retorna una instancia de esa clase.

Claramente este test fallará y podemos corroborarlo corriendo el comando `npm test`.

![Failing Test](https://cdn-images-1.medium.com/max/800/1*RJLkbAApKyEwzs-TBzGbFg.png)

Entonces, el próximo paso en el ciclo “red-green-refactor” es escribir solo el código necesario para pasar la prueba. Para lograr esto necesitamos crear el archivo con el nombre y la ruta que definimos en nuestros test: `src/components/todoList.component`, exportar la clase con el nombre también definido en los test: `TodoListController`.

```
export class TodoListController {}
```

Ya escribimos nuestra primera clase que en un futuro, cuando integremos el framework AngularJS, servirá como [controlador de un componente](https://docs.angularjs.org/guide/component).

Ahora veamos que la prueba pasa corriendo nuevamente:  `npm test`.

En este caso no aplicaría refactorizar, ya que solo hemos creado la definición del controlador y nada más.

A esta altura tenemos la siguiente estructura en nuestro directorio:

```
+-- src
|   +-- components
|   |   +-- todoList.component.js
+-- test
|   +-- todoList.component.spec.js
+-- .gitignore
+-- .babelrc
+-- package.json
```

Antes de escribir la siguiente prueba, veamos un script muy útil incluido en el package.json  para trabajar con TDD:  `npm run tdd`. Al ejecutarlo vemos que una consola interactiva entra en acción. Desde ahora escribiremos las nuevas pruebas y estas se ejecutaran automáticamente cuando nuestros archivos se actualizan.
Continuemos con nuestro proceso de diseño...
Ahora diseñaremos la funcionalidad para agregar un nuevo item al todoList.
El siguiente test que construiremos está en la linea 14:

```
import { TodoListController } from "../src/components/todoList.component"

describe("TodoListController", () => {
    let controller

    beforeEach(() => {
        controller = new TodoListController()
    })

    it("Should have a defined controller", () => {
        expect(controller).toBeInstanceOf(TodoListController)
    })

    it("Should add a todo item", () => {
        const todo = {
            name: "Learn Programming using component based approach",
            completed: false
        }
        controller.todosList = []

        controller.addTodo(todo)

        expect(controller.todosList.length).toBe(1)
        expect(controller.todosList[0]).toEqual(todo)
    })
```

Al actualizar nuestros archivos veremos que nuestro nuevo test está efectivamente fallando.
La funcionalidad que considera agregar un array al controlador como propiedad llamada todosList y usar un método addTodo. Notemos cómo seguimos diseñando nuestro código al definir los nombres de las propiedades, la estructura y métodos. 
Agregamos dos expectativas, después de agregar un todo, esperamos que la longitud de la propiedad aumente en uno y que el todo agregado esté presente en el array.

```
export class TodoListController {
    addTodo(todo){
        this.todosList.push(todo)
    }
}
```

Ahora que tenemos pasando 2/2 pruebas, la siguiente funcionalidad que diseñaremos es la posibilidad de marcar un cierto to-do como completado. El test relacionado está en la línea 27:

```
import { TodoListController } from "../src/components/todoList.component"

describe("TodoListController", () => {
    let controller

    beforeEach(() => {
        controller = new TodoListController()
    })

    it("Should have a defined controller", () => {
        expect(controller).toBeInstanceOf(TodoListController)
    })

    it("Should add a todo item", () => {
        const todo = {
            name: "Learn Programming using component based approach",
            completed: false
        }
        controller.todosList = []

        controller.addTodo(todo)

        expect(controller.todosList.length).toBe(1)
        expect(controller.todosList[0]).toEqual(todo)
    })

    it("Should be mark/unmark as completed a certain todo", () => {
        const index = 1
        controller.todosList = [
            {
                name: "Learn Programming using component based approach",
                completed: false
            },
            {
                name: "Learn Machine Learning",
                completed: false
            },
            {
                name: "Finish Medium article",
                completed: false
            }
        ]

        controller.toggleCheckTodo(index)
        expect(controller.todosList[index].completed).toBe(true)

        controller.toggleCheckTodo(index)
        expect(controller.todosList[index].completed).toBe(false)
    })
});
```

Podemos identificar tres secciones en el código de la prueba. Este concepto viene de las siglas AAA o Arrange-Act-Assert, por sus siglas en inglés. En resumen quiere decir:
 - Preparar las pre-condiciones y entradas.
 - Ejecutar el método que provocará un comportamiento que vamos a probar, y
 - Comprobar que el comportamiento esperado haya ocurrido. 

En las líneas 28–43 podemos ver la sección de preparación. Podemos ver que se establecen las variables sobre las cuales luego ejecutaremos y comprobaremos su comportamiento en las secciones siguientes.
En las líneas 44 y 47 podemos ver la ejecución.
En las líneas 45 y 48 vemos la sección de comprobación.
Con nuestra prueba fallando, escribamos el código para pasar.

```
export class TodoListController {
    addTodo(todo){
        this.todosList.push(todo)
    }
    toggleCheckTodo(index){
        this.todosList[index].completed = !this.todosList[index].completed
    }
}
```

La última funcionalidad será la posibilidad de eliminar un item todo. Escribamos este test en la línea 51:

## -------------------> snippet de test para borrar todo

Esta es una prueba más elaborada. Podemos identificar la sección de preparación en las líneas 52–69, pero mira especialmente las líneas 67–68.
todosListOriginalSize  fue creada para comparar en las expectativas que el largo del todoList disminuyó.

todoTargetName  fue creada para obtener el nombre del todo objetivo, el que será usado para revisar en las expectativas que el item no está presente en todosList.

todoFinderen la línea 69 es una función que revisa si cierto todo existe comparando con todoTargetName.

El código para pasar las pruebas es:

## ------------------> snippet con componente que elimina todo

Felicitaciones! 4 de 4 pruebas pasando. Nuestras funcionalidades están completas. Como conclusión podemos decir que ganamos dos cosas:
Las pruebas actúan como un “Contrato para los desarrolladores” Esto es, que en el futuro, cuando otros desarrolladores o nosotros mismos modifiquemos el código sin usar TDD e introducimos cambios en el comportamiento correcto, las pruebas que se aseguran de la calidad del comportamiento fallarán y podremos detectarlo.
Si encontramos manualmente un caso que no está considerado en nuestras pruebas, o en el caso que el cliente descubre un caso en el que nuestra aplicación falla, siempre podemos agregar un nuevo test y asegurarnos que el código para la nuevo caso no rompe otras funcionalidades probadas y aceptadas.

Entonces, el siguiente paso es crear la Interfaz de usuario que use las funcionalidades diseñadas y probadas.
Agregando UI, registrando componentes y rutas
Ya tenemos la funcionalidad, pero no tenemos la interfaz. Lo primero es crear una plantilla html para el todoList.component  llamada todoList.component.html  bajo el directorio ./src/components  .



Necesitaremos un index.html donde mostrar nuestra aplicación. Agrega este archivo en la carpeta ./src/ 



Fíjate en la línea 15 donde definimos un script llamado index.bundle.js , por ahora, como adelanto podemos decir que este archivo será usado como punto de entrada para la aplicación generada por webpack y servida por el paquetewebpack-dev-server .
Ahora vamos a escribir una función para el ruteo llamada routes  en el archivo routes.js en el directorio ./src/ . Esta función define la ruta de entrada / para la aplicación y establece la configuración básica para html5mode usando el módulo angular-ui-routrer .



Vemos la plantilla definida para la ruta en la línea 12. Esto es que el componente registrado en el sistema de módulos de angular debe ser nombrado todoList basado en las convenciones de directivas/componentes de AngularJS. ****nota: Buscar link de domunetación
Finalmente, también en el ./src/ vamos a crear un index.js . En este archivo vamos a registrar la función de configuración usando la función routes , registraremos nuestro componente y generamos el documento html bajo la directiva ng-app App



Vemos que estos archivos no fueron creados con TDD ya que en las pruebas unitarias no probamos configuraciones o código sin lógica.****Igual se podría testear la ruta
También necesitamos algo de datos para probar la aplicación. Por ahora vamos a agregar una lista directamente en el todosList.component.js en la propiedad todoList.  De paso con esto comprobaremos el constructor de la clase. 
Agregando webpack-dev-server:
Ahora necesitamos un servidor para levantar la aplicación. Para esto vamos a agregar el archivo webpack.config.js en la raíz del directorio
Nuestro directorio ahora luce así:
Ahora sí, ejecuta npm start y el navegador debiese abrir nuestra apliación

############################################################################################################################
##Stubby4node para simular una API REST,  Testing After Development (TAD), Tests de integración e Integración continua con TravisCI

En esta oportunidad dejaremos de lado la práctica de TDD, partiremos escribiendo primero la funcionalidad y luego el test asociado. A esto se le llama Testing After Development (TAD por sus siglas en inglés). 
Siguiendo con nuestra to-do list, ahora agregaremos otro nivel de funcionalidad: Servicios de AngularJS para comunicarnos con una API REST . La definición de clase para crear un servicio es la siguiente:



A diferencia de la clase TodoList que desarrollamos en el artículo anterior, esta vez definimos un constructor. AngularJS nos da la posibilidad de usar el patrón de inyección de dependencias para inyectar a nuestra instancia algún servicio del core y otro tipo de cosas como factories, filtros, constantes y/o values a través del constructor de la clase. 
En este caso usaremos el servicio core$http y  una constante llamada BASE_URL que contendrá la url donde publicaremos nuestra API REST.
Registrar Servicio en Angular, Resolviendo Promesas en el Router y Component Bindings
En el archivo index.js registramos nuestro servicio y la constante BASE_URL



Ahora podemos hacer uso de este servicio y podremos inyectarlo cuando lo necesitemos. 
En este caso necesitamos este servicio para proveer al componente TodoListComponent de la lista de todos proveniente desde el servidor 



Nótese como es utilizado el atributo resolve en la definición de la ruta. Cada una de las key de este objeto debe ser un callback que retorne el valor que será inyectado en el componente. A través de la propiedad bindings en la definición del componente, agregaremos el resultado de este callback utilizando el mismo nombre de la key acá definida, en este caso todosList 



El  callback definido en el resolvede la ruta, tiene la posibilidad de usar el patrón de inyección de dependencias, por lo tanto podemos usar nuestro Servicio TodoService y a través de se su método getTodos retornar una Promesa. Algo importante a considerar es que si el valor retornado por el callback es una Promesa, el router no hará render del template del componente hasta que esta promesa sea resuelta.
Si corremos nuestro código utlizando npm start, como es de esperarse obtendremos un error debido a que no tenemos una API REST que nos responda un JSON. 


Stubby4node para simular una API REST 
Cuando estamos desarrollando Front End llegamos siempre al punto en que tenemos que comenzar a modelar cuáles son los datos que serán enviados desde una API REST y como vendrán estructurados. Este proceso es clave ya que el éxito de un buen modelado nos permitirá tener un FrontEnd más preparado para el siempre cambiante modelamiento del negocio. Para comenzar a comunicarnos con una API REST necesitamos tener una API publicada en la web o bien tener un proyecto Backend corriendo en local para poder obtener datos. 
Aquí es donde entra la librería stubby4node que nos permitirá simular el comportamiento de una API REST y entregar respuestas predeterminadas a los endpoints que nosotros registremos, además de soportar comportamientos más avanzados como recibir tokens de autenticación, agregar retardo a las respuestas (muy útil para simular y probar loaders) y más.
npm install --save-dev stubby
Ahora modificamos nuestro package.json para agregar un script para correr stubby a través de su línea de comandos:
"stubs": "stubby -w -d stubs/fakeserver.yml -s 5000" 
La opción -w de watch, sirve para que cuando editemos los archivos de respuestas, stubby se actualice automáticamente.
La opción -d sirve para indicar el path en el cual se encuentra el archivo de configuración de stubby.
La opción -s sirve para indicar el puerto http en el cual correrá stubby. 

Ahora crearemos 2 archivos bajo un directorio al que llamaremos ./stubs uno de configuración llamado fakeserver.yml y otro llamado todos_get.json que contendrá la respuesta cuando se llame al endpoint http://localhost:5000/api/todos . 






Nuestra estructura de directorio ahora debería verse así:
.
+-- src
|   +-- components
    |   +-- todoList.component.html
    |   +-- todoList.component.js
|   +-- services
    |   +-- todo.service.js    
    +-- index.html
    +-- index.js
    +-- routes.js
+-- test
|   +-- todoList.component_spec.js
|   +-- jest.init.js
+-- .babelrc
+-- package.json
+-- webpack.config.js
Necesitamos decirle a nuestro script principal que corra stubby y el server de desarrollo en paralelo. Si bien podemos hacer esto de forma fácil con comandos soportados por sistemas operativos unix, existe un paquete de npm que nos permite hacer esto pero con soporte para otros sistemas como windows. Este paquete se llama npm-run-all.
Primero lo instalaremos:
npm install --save-dev npm-run-all
y lo utilizaremos de la siguiente manera:
"start": "npm-run-all -p stubs server" 
Finalmente nuestro package.json debe lucir de la siguiente manera:



Test después del desarrollo
Mi opinión personal es que TDD no es una práctica que podamos aplicar el 100% de las veces y tarde o temprano terminamos escribiendo primero la funcionalidad y luego el test, sin embargo sigo afirmando que hacer TDD nos hará pensar con mayor certeza en los casos bordes e impedir que se introduzcan defectos en el código.
Para escribir este test primero analizaremos a qué desafíos nos enfrentamos al momento de escribir este test. Existen 2 importantes puntos que debemos considerar:
1.- Para hacer testing unitario de este servicio tendremos que crear una instancia de la clase en una función beforeEach para proveer a cada test. Debemos considerar que para construir esta instancia tenemos 2 dependencias. $http y BASE_URL 
2 .- El método getTodos retorna una Promesa que interactúa con el servidor

Los test unitarios tienen un cierto alcance: “Cuando estamos haciendo testing sobre X, y este tiene dependencias Y, Z, si estamos haciendo testing sobre un método que utilice Z, el alcance del test unitario será hasta que Z entre en acción”
Analicemos nuestro servicio para ilustrar este concepto. 
TodoService y sus 2 dependenciasPara que el test realmente sea unitario y nuestro alcance sólo llegue hasta TodoService debemos romper esa dependencia y reemplazarla por “algo” que simule su comportamiento. Ese algo se llama “mock”.
Mocks
Un mock es un objeto que simula el comportamiento de una dependencia para que el método bajo test quede aislado y se pueda así hacer testing unitario. Generalmente se utilizan para poder testear la colaboración que hay entre el método que le estamos haciendo testing y la dependencia. 
Esto traducido a código luce de la siguiente manera:



Nótese el código en las líneas 6 y 9. A esto se le llama mocks. Si se fijan para hacer mock del servicio $http solamente definimos el método get que es el único que utilizamos hasta el momento. Si más adelante extendemos nuestro servicio para que haga otras operaciones como post o put debemos tambien agregar las implementaciones en este mock.
Tests de integración y la API de Javascript para manejar el DOM




TravisCI


Conclusión
Doing TDD Well
Test-driven development (TDD) is a simple concept. The TDD cycle is: Write a little bit of test, ensure it fails, write…www.developer.com
Considering Test-After Development
In this article, I'll build a solution twice. First, I'll write the code for a "reverser" method, a simple bit of code…www.developer.com
Mocks Aren't Stubs
I first came across the term "mock object" a few years ago in the Extreme Programming (XP) community. Since then I've…martinfowler.com
