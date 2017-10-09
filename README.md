Desarrollo Web Profesional parte I: Aplicación AngularJS con Testing y Javascript del futuro.

Bienvenido a la serie de Desarrollo Web Profesional parte I: El Front End. En esta serie de 4 artículos revisaremos brevemente conceptos importantes para desarrollar aplicaciones Front End de forma profesional. Lo haremos integrando herramientas simples, pero poderosas para construir un proceso de desarrollo que asegura comportamiento (muy útil para metodologías ágiles), calidad de código, escalabilidad, rendimiento e integración continua.
En este primer artículo discutiremos acerca del enfoque orientado a componentes para desarrollar una app usando la metodología de desarrollo liderado por pruebas (TDD por sus siglas en inglés). También discutiremos el por qué no basta con estar en permanente actualización de los frameworks y librerías más populares, sino que es de suma importancia entender los conceptos transversales que aplican al desarrollo Front End profesional, los cuales muchas veces son consecuencia de nuevas funcionalidades y evolución del lenguaje JavaScript.
En concreto revisaremos:
Configurar un entorno de desarrollo.
Utilizar git para versionar las aplicaciones.
Entender la configuración básica de Babel para usar funcionalidades futurísticas de Javascript que aún no soporta la mayoría de los navegadores, como async/await, import/export y Classes. Esto nos permitirá tener código conciso y sintáctico.
Entender las principales funcionalidades y configuración del framework Jest, una suit de pruebas encantadora según su sitio web.
Diseñar el código y probarlo usando TDD y un enfoque orientado a componentes.
Usar la configuración básica de webpack-dev-server e implementar los cargadores (loaders) de webpack.

De una buena vez, comencemos.
Requerimientos
NodeJS ≥ 8.4.0 usando NVM
Git

**Control de versiones: Git**
Usaremos Git y Github para la posterior integración de un proceso que nos permitirá hacer deploy continuo y generar versiones de la aplicación.
Partiremos creando la carpeta vacía e iniciando git en ella.

```
git init 
git remote add origin <url_de_mi_repositorio_en_github> 
```

Creemos los siguientes 3 archivos en la raíz del repositorio:

**package.json**

```
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

```
node_modules
coverage
npm-debug.log
dist
```

.babelrc

```
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


Explicando la configuración básica de Jest
Las opciones de testing son muy sencillas. Se necesitan configurar 3 cosas: Configurar la url por defecto para los test, un archivo de configuración y el patrón donde Jest podrá encontrar el código bajo los test (conocido como SUT).
testURL : La URL por defecto cuando corren los test. Por ejemplo, esto podría reflejarse en el location.href .
testMatch : Un arreglo con las convenciones de nombre de los archivos donde están los test. Por ejemplo, todos los archivos terminados en “_spec.js”.

Opciones para cobertura de código son muy sencillos. Podemos configurar las métricas asociadas.
collectCoverageFrom : El código que será medido e incluido en el reporte a cubrir.
coverageThreshold : La métrica mínima que los test serán considerados aceptables.
Crear una aplicación con TDD
Crearemos la clásica lista “to-do list”, pero esta vez usando la metodología TDD para escribir nuestro código y enfocarnos en código orientado a componentes.
Lo primero es simplemente escribir nuestra primera prueba basado en el ciclo “red-green-refactor”, escribiremos una prueba unitaria en el archivo todoList.component_spec.js dentro del directorio ./test/ . Claramente este test fallará, y que suerte qué así sea, ya que no hemos escrito ningún código.



Archivo todoList.component_spec.jsSe ve simple verdad?, lo primero es importar el código que vamos a probar. En este punto estamos diseñando nuestro código con código. Pensar en el proceso mental de diseñar/modelar y escribir esas ideas en nuestros test.
En este pequeño ejemplo nuestro código ya está siendo diseñado e incluye:
El nombre del archivo y la clase que se debiese exportar en él (todoList.component) en la primera línea
Un escenario común (línea 6) para todos nuestros test donde se crea una nueva instancia de nuestro controlador.
Un test básico que comprueba si la instancia creada está definida y es de su clase correspondiente.

Ahora veamos fallar esta prueba, corramos npm test  :
Entonces, el próximo paso en el ciclo “red-green-refactor” es escribir solo el código necesario para pasar la prueba. Para lograr esto necesitamos crear el archivo, exportar el objeto que define nuestra constante e incluir la definición del controlador. Creamos el archivo todoList.component.js en el siguiente directorio ./src/components con el código justo.



En este punto, solo definimos una contante con la mínima definición de un componente de AngularJS. Para más detalles sobre esto revisa esto. Hay que notar que no hemos resgistrado el componente en el sistema de módulos de Angular, solamente creamos la definición y la exportamos, pero en el test registramos el componente usando su definición.
Ahora veamos que la prueba pasa usando npm test  
En este caso no aplicaría refactorizar, ya que solo hemos creado la definición del controlador y nada más.
A esta altura tenemos la siguiente estructura en nuestro directorio:
+-- src
|   +-- components
|   +-- todoList.component.js
+-- test
|   +-- todoList.component_spec.js
|   +-- jest.init.js
+-- .babelrc
+-- package.json
Antes de escribir la siguiente prueba, veamos un código muy útil incluido en el package.json  para trabajar con TDD npm run tdd  . Al ejecutarlo vemos que una consola interactiva entra en acción junto con al modo observador para Jest. Desde ahora escribiremos las nuevas pruebas y estas se ejecutaran automáticamente cuando nuestros archivos se actualizan.
Continuemos con nuestro proceso de diseño…
Ahora diseñaremos la funcionalidad para agregar un nuevo item al todoList. El siguiente test que construiremos está en la linea 14:



Al actualizar nuestros archivos veremos que nuestro nuevo test está efectivamente fallando.
La funcionalidad que considera agregar un array al controlador como propiedad llamada todosList y crea un addTodo. Notemos cómo seguimos diseñando nuestro código al definir los nombres de las propiedades, la estructura y métodos. 
Agregamos dos expectativas, después de agregar un todo, esperamos que la longitud de la propiedad de cambie y que el todo agregado sea agregado al array.
Nuevamente, agregamos el mínimo código requerido:



Ahora que tenemos pasando 2/2 pruebas, la siguiente funcionalidad a diseñar es la posibilidad de marcar un cierto todo como completado. El test relacionado está en la línea 27:



Podemos identificar tres secciones en el código de la prueba. Este concepto viene de las siglas AAA o Arrange-Act-Assert, por sus siglas en inglés. En resumen quiere decir:
Preparar las pre-condiciones y entradas.
Ejecutar el método que provocará un comportamiento que vamos a probar, y
Comprobar que la expectativa esperada haya ocurrido. 

En las líneas 28–43 podemos ver la sección de preparación. Podemos ver que se establecen las variables sobre las cuales luego ejecutaremos y comprobaremos su comportamiento en las secciones siguientes.
En las líneas 44 y 47 podemos ver la ejecución.
En las líneas 45 y 48 vemos la sección de comprobación.
Con nuestra prueba fallando, escribamos el código para pasar.



La última funcionalidad será la posibilidad de eliminar un item todo. Escribamos este test en la línea 51:



Esta es una prueba más elaborada. Podemos identificar la sección de preparación en las líneas 52–69, pero mira especialmente las líneas 67–68.
todosListOriginalSize  fue creada para comparar en las expectativas que el largo del todoList disminuyó.
todoTargetName  fue creada para obtener el nombre del todo objetivo, el que será usado para revisar en las expectativas que el item no está presente en todosList.
todoFinderen la línea 69 es una función que revisa si cierto todo existe comparando con todoTargetName  .
El código para pasar las pruebas es:



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
