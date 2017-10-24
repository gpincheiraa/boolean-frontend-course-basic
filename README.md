# Boolean curso profesional de Frontend nivel básico

Bienvenido al curso gratuito de desarrollo frontend profesional nivel básico.

El objetivo de este curso es dar una mirada completa sobre el proceso de desarollar una aplicación frontend utilizando el lenguaje *javascript*.

Las librerías utilizadas en este curso fueron seleccionadas no por ser las mejores ni las más populares, sino que por ser las que permitían ilustrar de mejor manera los conceptos escenciales de un proceso de desarollo orientado a la integración continua y los lanzamientos a producción tempranos.

El proceso de desarrollo utilizado en este curso permite a quienes lo utilicen de la manera correcta, medir la calidad del código que se produce, la promoción de buenas prácticas e implementación de patrones de diseño, las pruebas automatizadas, la integración del código desarrollado en equipos distribuidos, el versionado del código y el proceso de gestión de tickets de incidencias o mejoras; todos estas ganancias traducidas finalmente en código más fácil de escalar y preparado para ser modificado en un futuro.

Revisaremos de manera breve los conceptos importantes para seguir este proceso integrando herramientas simples pero poderosas de manera de lograr construir un proceso de desarrollo que asegure que el comportamiento que definimos que nuestro codigo debía tener, perdure en el tiempo sin importar las modificaciones que se hagan a futuro, y que de ser el caso en que algunos de estos comportamientos se vean corrompidos por la introducción de nuevo código, nos avise de manera de poder tomar acciones frente a ello.


------ Imagen que logre describir de manera gráfica el proceso de desarrollo ---------



Indice de capítulos

 - [Capítulo 1](#capítulo-1-ambiente-de-desarollo-tdd-angularjs-y-snapshot-testing): Ambiente de desarollo, TDD, AngularJS y Snapshot Testing.

 - [Capítulo 2](#capítulo-2-stubby4node-para-simular-una-api-rest-pruebas-después-del-desarrollo-y-tests-de-integración): Stubby4node para simular una API REST, Pruebas después del desarrollo y Tests de Integración.


## Capítulo 1: Ambiente de desarollo, TDD, AngularJS y Snapshot Testing.

En este primer capítulo discutiremos como desarrollar una simple funcionalidad usando la metodología de desarrollo liderado por pruebas (TDD por sus siglas en inglés).

En concreto revisaremos:
 - Configurar un entorno de desarrollo mínimo.
 - Utilizar git para versionar las aplicaciones.
 - Comprender los conceptos básicos de TDD y construir una funcionalidad simple.
 - Introducción a AngularJS.
 - Snapshot testing de templates compilados por AngularJS.

### Configurar un entorno de desarrollo mínimo.

Para nuestro entorno y proceso de desarollo necesitaremos cumplir los siguientes requisitos:

- Una terminal. (Para windows recomendamos [cmder](http://cmder.net/))
- NodeJS >= 8.4 instalado a través de NVM. [¿por qué a través de nvm?]
- Git

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

  - `scripts`: Son los scripts de npm que se corren a través del comando `npm run nombre_script`. El script test también puede correrse utilizando `npm test`. Esta diferencia tiene que ver porque existen [scripts por defecto](https://docs.npmjs.com/misc/scripts#description) dentro de los cuales test es uno de ellos. Los comandos ejecutados a través de los npm scripts (por ejemplo `jest`) son archivos de linea de comandos instalados a través de los paquetes de dependencias. Si queremos ver estos archivos, una vez instaladas las dependencias, podemos encontrarlos en la ruta `node_modules/.bin/`.

  - `devDependencies`: Son las dependencias de desarrollo de nuestro proyecto. Estos paquetes nos serviran para ejecutar comandos que nos serán de utilidad por ejemplo `http-server` que nos servirá para correr un servidor local donde veremos en el navegador el informe de cobertura de los test.


**.gitignore**

```git
node_modules
coverage
npm-debug.log
dist
```

**.babelrc**

```json
{
  "presets": [ "es2015", "stage-3"]
}
```

## Del porque usar Babel y entendiendo como Ecmascript es un estándar en constante desarrollo  
Primero sería bueno hacer una aclaración: ¿Ecmascript?, ¿Javascript?. ¿Qué son? ¿Cómo se relacionan?.
La respuesta a estas preguntas se puede resumir en la definición de que es Javascript.

> Javascript: Es un lenguaje de scripting multi-paradigma especificado a través del estándar Ecmascript.

Entonces Ecmascript es el estándar que define cuales serán las funcionalidades y el comportamiento del lenguaje y Javascript es la implementación de este. Cabe destacar que quien se encarga de darle el carácter de estándar es la asociación internacional de estándares [Ecma International](http://www.ecma-international.org/).

Para llevar a cabo esta tarea de estandarización es necesario un proceso que garantice la calidad de las nuevas versiones, la discusión sobre que nuevas funcionalidades deben ser parte de una nueva versión y cuáles no. La historia se remonta a 1997 cuando se publica la primera versión y continúa hasta nuestros días donde el estándar sigue evolucionando. Si revisamos [el historial de publicaciones del estándar](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Language_Resources) podemos hacer algunas observaciones como que no existe la versión 4; y que entre la versión 3 y la 5 hay 10 años de diferencia.
Lo más llamativo es lo rápido que ha evolucionado el lenguaje a partir de la quinta edición y esto se debe a las mejoras que se introdujeron al proceso: Se pasó de un proceso basado en e-mails y "conversaciones" a uno [totalmente abierto y democrático a través de Github](https://github.com/tc39/ecma262/blob/master/CONTRIBUTING.md).
De este proceso se desprenden distintas [etapas de "maduración"](https://tc39.github.io/process-document/) desde que una nueva versión es propuesta hasta que esta es publicada oficialmente.

Estas etapas las podemos resumir en:

- `stage-0`:
- `stage-1`:
- `stage-2`:
- `stage-3`:
- `stage-4`:

El papel que juega [Babel](https://babeljs.io/) en el desarrolo moderno de software utilizando javascipt es el de servir como herramienta de compilación para dar compatibilidad a los interpretes de javascript (principalmente los navegadores) sobre nuevas funcionalidades que aún no han sido lanzadas oficialmente. Por lo tanto a través de Babel podemos agregar a nuestro código funcionalidades que aún se encuentran trabajándose entre el `stage-0` hasta el `stage-3`. La posibilidad de hacer esto a logrado que el lenguaje avance a pasos agigantados siendo cada vez mejor refinado ya que permite que cualquier persona en el mundo pueda probar las funcionalidades que aún no han sido lanzadas y entregar retroalimentación al estándar para descartar o seguir adelante con alguna X funcionalidad.



## Control de versiones

Ahora agregamos esos archivos y creamos el commit correspondiente:

```
git add package.json .gitignore .babelrc
git commit -m "Adding development environment configuration"
```

Finalmente instalaremos estas dependencias corriendo el comando:
```
npm install
```

## Crear una aplicación con TDD

Escribiremos pruebas unitarias a una unidad de código que resuelve el siguiente requerimiento:
```
Construir un código capaz de:
- agregar una nueva tarea a una lista de tareas.
- cambiar el estado de una tarea de completed a incomplete o viceversa.
- eliminar una tarea de la lista
```
La lista de tareas consiste en una o más tareas, donde cada tarea tiene 2 propiedades: name y completed; name es el nombre dado a la tarea
y completed representa si la tarea ya fue realizada o no.

Para poder escribir y correr pruebas unitarias utilizaremos **Jest**, un completo framework para hacer tests. Una de sus particularidades es que tiene cero-configuración, solo necesitamos seguir 2 convenciones: la carpeta que contendrá la suite de test se debe llamar **__test__** y los archivos con los test deben tener la extensión **.spec.js**, ¡¡¡Super simple!!!
Si en un futuro queremos utilizar jest para agregar comportamiento más avanzado a nuestra suite de tests, este provee [muchas opciones de configuración](https://jest-bot.github.io/jest/docs/configuration.html). Más adelante conversaremos acerca de algunas de estas.


Lo primero es simplemente escribir nuestra primera prueba considerando el ciclo [“red-green-refactor”](http://blog.cleancoder.com/uncle-bob/2014/12/17/TheCyclesOfTDD.html).
Escribiremos una prueba unitaria en el archivo `todoList.component.spec.js` dentro del directorio `./__test__/`.

```javascript
import { TodoListController } from "../src/components/todoList.component"

describe("TodoListController", () => {
    let controller;

    beforeEach(() => {
        controller = new TodoListController();
    });

    it("Should have a defined controller", () => {
        expect(controller).toBeInstanceOf(TodoListController);
    });
});
```

Se ve simple verdad?
En este punto estamos **diseñando nuestro código con código**.

Hablemos un poco sobre esta idea:
A menudo cuando diseñamos una funcionalidad estamos acostumbrados a pensar en cuál debería ser el código más correcto para solucionar X problema basados en nuestra experiencia y en los algoritmos que conocemos. Esto no pasa más allá de procesar en nuestros cerebros esa idea o escribirla en un papel y luego escribimos el que será a futuro nuestro código de producción.

El gran problema es que el proceso de probar si la idea que pensé va a funcionar requiere un trabajo extra: Integrar el código en algún framework (o sin framework) para luego integrarlo en una página HTML, correr esta página en un servidor local, verla en un navegador y empezar a interactuar con ella. Una vez superados todos estos obstáculos, recién podemos comenzar a hacer pruebas y en general tendemos a hacer sólo pruebas de "camino feliz".
La idea de diseñar código con código es poder probarlo sin necesidad de todo ese proceso sumado a poder simular distintos comportamientos y corroborar si nuestro código está preparado para ser integrado en una aplicación que finalmente será la que verá el cliente final para quien estamos desarrollando.

En este pequeño ejemplo podemos ver que nuestro código ya está siendo diseñado e incluye:

- El nombre del archivo y la clase que se debiese exportar en el componente (todoList.component) en la primera línea
- Un escenario común (línea 6) que se correra por cada uno de nuestros test donde se crea una nueva instancia de nuestro controlador.
- Un test básico que comprueba si el constructor de la clase funciona correctamente y nos retorna una instancia de esa clase.

Claramente este test fallará y podemos corroborarlo corriendo el comando `npm test`.

![Failing Test](https://cdn-images-1.medium.com/max/800/1*RJLkbAApKyEwzs-TBzGbFg.png)

Entonces, el próximo paso en el ciclo “red-green-refactor” es escribir solo el código necesario para pasar la prueba. Para lograr esto necesitamos crear el archivo con el nombre y ruta que definimos en nuestro test: `src/components/todoList.component`, exportar la clase con el nombre también definido en los test: `TodoListController`.

```javascript
export class TodoListController {}
```

Ya escribimos nuestra primera clase que en un futuro, cuando integremos el framework AngularJS a nuestro stack de desarrollo, servirá como [controlador de un componente](https://docs.angularjs.org/guide/component).

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

```javascript
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

```javascript
export class TodoListController {
    addTodo(todo){
        this.todosList.push(todo)
    }
}
```

Ahora que tenemos pasando 2/2 pruebas, la siguiente funcionalidad que diseñaremos es la posibilidad de marcar un cierto to-do como completado. El test relacionado está en la línea 27:

```javascript
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

```javascript
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

```javascript
import { TodoListController } from "../../src/components/todoList.component";

describe("TodoListController", () => {
    let controller;

    beforeEach(() => {
        controller = new TodoListController();
    })

    it("Should have a defined controller", () => {
        expect(controller).toBeInstanceOf(TodoListController);
    })

    it("Should add a todo item", () => {
        const todo = {
            name: "Learn Programming using component based approach",
            completed: false
        };
        controller.todosList = [];

        controller.addTodo(todo);

        expect(controller.todosList.length).toBe(1);
        expect(controller.todosList[0]).toEqual(todo);
    });

    it("Should be mark/unmark as completed a certain todo", () => {
        const index = 1;
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
        ];

        controller.toggleCheckTodo(index);
        expect(controller.todosList[index].completed).toBe(true);

        controller.toggleCheckTodo(index);
        expect(controller.todosList[index].completed).toBe(false);
    });

    it("Should be delete a todo", () => {
        const index = 0;
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
        ];
        const todosListOriginalSize = controller.todosList.length;
        const todoTargetName = controller.todosList[index].name;
        const todoFinder = (todo) => todo.name === todoTargetName;

        expect(controller.todosList.some(todoFinder)).toBe(true);

        controller.deleteTodo(index);

        expect(controller.todosList.length).toEqual(todosListOriginalSize - 1);
        expect(controller.todosList.some(todoFinder)).toBe(false);
    });
});
```

Esta es una prueba más elaborada. Podemos identificar la sección de preparación en las líneas 52–69, pero mira especialmente las líneas 67–69:

`todosListOriginalSize` El largo de la lista de tareas antes de ser modificada. Nos servirá para luego comparar una vez eliminada una tarea.

`todoTargetName` fue creada para obtener el nombre la tarea objetivo, el que será usado para revisar en las pruebas que este ya no está presente en la lista.

`todoFinder` función genérica que será utilizada en conjunto con `Array.some` para revisar si la tarea está presente o no en la lista.

El código para pasar estas pruebas es:

```javascript
export class TodoListController {
    constructor() {
        this.todosList = [];
    }
    addTodo(todo){
        this.todosList.push(todo);
    }
    toggleCheckTodo(index){
        this.todosList[index].completed = !this.todosList[index].completed;
    }
    deleteTodo(index){
        this.todosList.splice(index, 1);
    }
}
```
Lo logramos! 4/4 pruebas correctas. Nuestras funcionalidades están completas.
Como conclusión a este proceso podemos decir que hemos ganado dos cosas:

1.- Las pruebas actúan como un “Contrato para los desarrolladores”, osea que en el futuro, cuando otros desarrolladores o nosotros mismos modifiquemos el código y en el peor de los casos introducimos un comportamiento no adecuado, las pruebas fallarán y nos daremos por enterados para refactorizar nuestro código.
2.- Si encontramos manualmente un caso que no está considerado en nuestras pruebas, o en el caso que el cliente descubre un caso en el que nuestra aplicación falla, siempre podemos agregar un nuevo test y asegurarnos que el código para el nuevo caso no corrompe otras funcionalidades ya probadas y aceptadas.


### Integrando nuestro código a AngularJS





## Capítulo 2: Stubby4node para simular una API REST, Pruebas después del desarrollo y Tests de Integración

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


## Capítulo 3: Escribiendo una nueva funcionalidad desde las pruebas unitaras hasta las de integración

Escribiremos pruebas unitarias a una unidad de código que resuelve el siguiente requerimiento:
```
- Crear una nueva tarea que tenga los campos `nombre` y `descripción`.
- Existe sólo un campo requerido: `nombre`.
- Botón guardar debe estar deshabilitado si el campo requerido no ha sido ingresado.
- Al sacar el foco sobre el input requerido debe desplegarse un mensaje que diga al usuario que no olvide escribir sobre el input obligatorio.
- La descripción debe tener como máximo 300 caracteres.
```
Escribiremos un test unitario para definir el componente que cumplirá con los requerimientos dados:

```javascript
import { TodoFormController } from "../../src/components/todoForm.component";

describe("TodoFormController", () => {
    let controller;

    beforeEach(() => {
        controller = new TodoFormController();
    });

    it("Should have a defined controller and default values for new todo", () => {
        expect(controller).toBeInstanceOf(TodoFormController);
        expect(controller.newTodo.name).toEqual("");
        expect(controller.newTodo.description).toEqual("")
    });
});
```

```javascript
export class TodoFormController {
    constructor() {
        this.newTodo = {
            name: "",
            description: ""
        };
    }
}
```
Con este código ya estamos pasando la primera prueba unitaria. Ahora escribiremos un test de integración:

```javascript
import "../../src/index";
import "angular-mocks";
import { uirouterScenario } from "angularjs-uirouter-integration-scenarios";

describe("New Todo rendering and interaction on '/newtodo' path", () => {
    const treeDOMBody = document.querySelectorAll("body")[0];
    const stateOptions = { stateName: "newtodo" };

    beforeEach(angular.mock.module("App"));
    beforeEach(uirouterScenario.build());
    beforeEach(uirouterScenario.loadState(stateOptions));

    afterEach(uirouterScenario.clean());

    it("should disable by default the submit button", () => {
        const submitButton = treeDOMBody.querySelector("#todo-submit");
        expect(submitButton.disabled).toBe(true);
    });

    it("should enable the submit button only if the required fields was filled", () => {
        const formElement = uirouterScenario.getFormFromState("newtodoForm");
        const submitButton = treeDOMBody.querySelector("#todo-submit");
        const requiredElements = treeDOMBody.querySelectorAll("[required]");

        Array.from(requiredElements).forEach((field) => {
            formElement[field.name].$setViewValue("somevalue");
        });

        expect(submitButton.disabled).toBe(false);
    });
});
```
Necesitamos un estado registrado en el `$stateProvider` llamado "newtodo", necesitamos que exista en un elemento html con el id `todo-submit` y finalemente que esté deshabilitado. Escribamos el código necesario:

Registramos la nueva ruta:
```javascript
import "@uirouter/angularjs";

export function routes($stateProvider, $locationProvider) {
    $locationProvider.html5Mode({
        enabled: true,
        requireBase: false
    })
    $stateProvider
        .state("home", {
            url: "/",
            component: "todoList",
            resolve: {
                todosList: TodoService => TodoService.getTodos()
            }
        })
        .state("newtodo", {
            url: "/newtodo",
            component: "todoForm"
        })
}
```

Creamos la definición de componente para "todoForm" junto a su template y lo registramos en angular

```javascript
import template from "./todoForm.component.html";

export class TodoFormController {
    constructor() {
        this.newTodo = {
            name: "",
            description: ""
        };
    }
}
export const TodoFormComponent = {
    template,
    controller: TodoFormController
}
```

```html
<form name="$ctrl.newtodoForm">
    <button id="todo-submit" disabled>Save</button>
</form>
```

```javascript
import angular from "angular";
import { routes } from "./routes";
import { TodoListComponent } from "./components/todoList.component";
import { TodoFormComponent } from "./components/todoForm.component";
import { TodoService } from "./services/todo.service";

angular
    .module("App", [
        "ui.router"
    ])
    .config(routes);
    .constant("BASE_URL", "http://localhost:5000/api");
    .component("todoList", TodoListComponent);
    .component("todoForm", TodoFormComponent);
    .service("TodoService", TodoService);

angular.bootstrap(document, ["App"]);
```
Haciendo estos pasos ya pasaemos la primera prueba de integración. Ahora debemos refactorizar en este caso el template del componente que es el que tendrá la lógica de un formulario de angularjs. Una explicación detallada sobre como funcionan los forms en angularjs puede ser encontrada [acá](https://docs.angularjs.org/guide/forms).

```html
<form name="$ctrl.newtodoForm">
    <p>
        <label for="todo-name">Name</label>
        <input id="todo-name" name="todoName" ng-model="$ctrl.newTodo.name" required>
    </p>
    <p>
        <label for="todo-description">Name</label>
        <input id="todo-description" name="todoDescription" ng-model="$ctrl.newTodo.description">
    </p>
    <p>
        <button id="todo-submit" ng-disabled="$ctrl.newtodoForm.$invalid">Save</button>
    </p>
</form>
```
Los puntos clave en este código son el atributo `name` en el formulario que está asociado a una variable del
controlador. Esto creará un objeto que contendrá una serie de atributos y métodos que nos servirán para realizar validaciones, crear mensajes de error y generar clases que nos servirán para el manejo de formularios.

Usaremos la directiva `ng-disabled` para cumplir el requerimiento de que el botón esté deshabilitado
Ya tenemos el código suficiente como para crear un snapshot.

```javascript
import template from "../../src/components/todoForm.component.html";

describe("Render todoForm", () => {
    it("newTodo form with name data filled and submit enabled", () => {
        const componentDefinition = {
            template,
            $ctrl: {
                newTodo: {
                    name: "data"
                }
            }
        };
        expect(componentDefinition).toMatchSnapshot();
    });
    it("newTodo form with name data empty and submit disabled", () => {
        const componentDefinition = {
            template,
            $ctrl: {
                newTodo: {
                    name: ""
                }
            }
        };
        expect(componentDefinition).toMatchSnapshot();
    });
});
```
Nos falta escribir un nuevo test de integración que corrobore que al quitar el foco sobre el input obligatorio aparezca un mensaje.



## Capítulo 3: Deploy automatizado para salida tempranas a producción a través del sistema de Integración Continua

  Mostrar como lograr salir a producción de manera automatizada a través de scripts que corren en el sistema
  de integración continua sólo cuando el código es mezclado en la rama master a través de PR's. Mostrar  

## Capítulo 4: Test funcionales


## Capítulo 5: Cambiar o remover funcionalidades con confianza gracias a las pruebas escritas

  Mostrar con un ejemplo como removemos funcionalidad que cambió ya sea por requerimiento o porque
  estamos refactorizando o derechamente encontramos código que ya no aplica.


## Capítulo 6: Conceptos más avanzados de testing y la generación de recetas de testing.
