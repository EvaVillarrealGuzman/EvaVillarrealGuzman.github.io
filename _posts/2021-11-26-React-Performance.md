---
title: Técnicas de optimización para aplicaciones React
published: true
---

La *performance* es un factor importante en la calidad de una aplicación. Depende mucho de cómo codificamos y cómo está configurada la infraestructura. Es importante, ya que impacta en la experiencia del usuario.

Por su parte React ofrece mucha optimización para diseñar aplicaciones de reacción de alto rendimiento, sin embargo al agregar una librería como React, no se mejora la *performance* de la aplicación al instante. Para ello, debe configurar correctamente el React.

# [](#header-1)React.Fragment


Muchas veces pasa que queremos renderizar un conjunto de elementos y para agruparlos se suelen poner dentro de un elemento padre ```div```: 

```react
<div>
    <h1>Title</h1>
    <p>Content</p>
</div>
```

Esto agrega un nodo extra al DOM, el cual es innecesario ya que solo tiene la función de agrupar elementos. Esto puede afectar a la *performance*.

Para agrupar elementos debemos usar ```<React.Fragment>```, el cuál no agrega ningún nodo extra:

```react
<React.Fragment>
    <h1>Title</h1>
    <p>Content</p>
</React.Fragment>
```

React.Fragment tiene una sintaxis corta que es  <></>. Por lo que el código de arriba también se puede escribir:

```react
<>
    <h1>Title</h1>
    <p>Content</p>
</>
``` 

# [](#header-1)Usar el empaquetado en modo  Producción

Cuando se desarrolla una aplicación con React, se presentan advertencias y mensajes de errores muy útiles. Estos hacen la identificación de bugs y problemas durante el desarrollo muy fácil. Pero también cuestan parte del desempeño.

Si observas el código fuente de React, verás muchas marcas de ```if (process.env.NODE_ENV != 'production')```. Estos trozos de código que React está ejecutando en tu ambiente de desarrollo no es algo que el usuario necesite. Para ambientes de producción, todo este código innecesario se puede descartar.

Si impulsaste tu proyecto usando create-react-app, entonces puedes ejecutar ``npm run build`` para producir la construcción de producción sin este código extra. Si estás usando *webpack* puedes ejecutar ```webpack -p``` (el cual es el equivalente de ```webpack --optimize-minimize --define process.env.NODE_ENV="'production'"```).


# [](#header-1)Implemente el método shouldComponentUpdate()


Cuando una propiedad (*props*) o estado de un componente cambia, React decide si es necesario actualizar el DOM comparando el elemento recién retornado con el previamente renderizado. Si no son iguales, React actualizará el DOM.

Aunque React solo actualiza los nodos DOM modificados, el re-renderizado aún lleva algo de tiempo. En muchos casos no es un problema, pero si la desaceleración es notable puedes acelerar el proceso anulando la función del ciclo de vida shouldComponentUpdate, el cual se ejecuta antes de que el proceso de re-renderizado comience, es decir, cuando nuevos *props* son recibidos o algún estado cambia. La implementación por defecto de esta función retorna true, permitiendo a React hacer la actualización, incluso si ningún *props* o estado ha cambiado (los valores actuales y previos son los mismos).

Si sabes que en algunas situaciones tu componente no necesita actualizarse, puedes retornar false desde shouldComponentUpdate para omitir todo el proceso de renderización, incluida la invocación de render() en este componente y debajo de él.


```react
import React from "react";

export default class TestComponent extends React.Component {
  constructor(props) {
    super(props)
    this.state = {
      data: ""
    }
  }

  handleClick = () => {
    let value = document.getElementById("textvalue").value;
    this.setState({ data: value })
  }

  shouldComponentUpdate(nextProps, nextState) {
    if (nextState.data === this.state.data)
      return false
    return true
  }

  render() {
    console.log("Rendering");
    return (
      <div>
        <input id="textvalue" type="text" />
        <button onClick={this.handleClick}>Click</button>
        {this.state.data}
      </div>
    )
  }
}
``` 

Si el código de arriba no tuviera el método shouldComponentUpdate implementado, el componente se vuelve a renderizar cada vez que se hace click en un botón (incluso si el *input* de texto tiene el mismo valor).


# [](#header-1)React.PureComponent

En lugar de implementar shouldComponentUpdate, podemos usar React.PureComponent.

Un React.PureComponent es lo mismo que un React.Component pero que implementa una función shouldComponentUpdate() con una comparación superficial. Es decir, chequea los *props* y los valores de los estados superficiales y decide si el componente necesita renderizar o no. De esta manera React.PureComponent optimiza los componentes de la clase reduciendo el número de renderizados no deseados.

Debido a que sólo hace una comparación superficial, no puede usarlo si las propiedades o el estado han sido mutados de una manera que una comparación superficial pasaría por alto. Esto puede ser un problema con estructuras de datos más complejas.

Por ejemplo, en el siguiente código el problema es que PureComponent hará una comparación simple entre los valores antiguos y nuevos de this.props.words. Dado que este código muta la matriz wordsen el método handleClick de WordAdder, los valores antiguos y nuevos de this.props.words se compararán como iguales, aunque las palabras actuales de la matriz hayan cambiado. La ListOfWords no se actualizará a pesar de que tiene nuevas palabras que se deben renderizar.

```react
class CounterButton extends React.PureComponent {
  constructor(props) {
    super(props);
    this.state = {count: 1};
  }

  render() {
    return (
      <button
        color={this.props.color}
        onClick={() => this.setState(state => ({count: state.count + 1}))}>
        Count: {this.state.count}
      </button>
    );
  }
}
``` 

Por lo tanto, React.PureComponent es útil solo cuando:
- *props* y estados contienen datos primitivos
- *props* y estados tienen datos complejos pero saben cuándo llamar a forceUpdate() para actualizar el componente


La implementación de shouldComponentUpdate en el ejemplo anterior de TestComponent se podría implementar como React.PureComponent de la siguiente manera:

```react
import React from "react";

export default class TestComponent extends React.PureComponent {
  constructor(props) {
    super(props)
    this.state = {
      data: ""
    }
  }
  handleClick = () => {
    let value = document.getElementById("textvalue").value;
    this.setState({ data: value })
  }
  render() {
    console.log("Rendering");
    return (
      <div>
        <input id="textvalue" type="text" />
        <button onClick={this.handleClick}>Click</button>
        {this.state.data}
      </div>
    )
  }
}
```

# [](#header-1)Usar memorización

Memorización es una técnica de optimización para incrementar la *performance* de la aplicación guardando los resultados y devolviendo el resultado en caché cuando se repiten las mismas entradas.

React provee React.memo y useMemo para memorización, los cuáles guardan en caché componentes.

React.memo es un componente de alto nivel para usar en componentes funcionales. Cuando el componente funcional se procesa usando React.Memo o useMemo, entonces su resultado se ha guardado en la memoria y la próxima vez que se llame al componente con los mismos *props*, el resultado en caché regresará sin ninguna ejecución. 


```react
const UserDisplay = (userDetails) =>{
    const {name, age, address} = userDetails;

    return (
        <>
            <h4>{name}</h4>
            <p>{age} , {address}</p>
        </>
    )
}
export default React.memo(UserDisplay);
// Primero - UserDisplay se llama y se ejecuta, luego se procesa.
<UserDisplay
  name="Test"
  age="30"
  address="Test address"
/>
// Segundo - El resultado almacenado en caché se procesará sin ninguna ejecución.
<UserDisplay
  name="Test"
  age="30"
  address="Test address"
/>
// Tercero - UserDisplay se llama y se ejecuta, luego se procesa.
// Porque aquí el valor *name* es diferente.
<UserDisplay
  name="New Test"
  age="30"
  address="Test address"
/>
``` 

# [](#header-1)*Code Splitting*

Primero es importante entender el concepto de *Lazy loading*, el cuál indica que los recursos (imaǵenes, *scripts*, etc.) deberían ser cargados cuando realmente se necesitan. En lugar de cargar una página web entera y renderizarla en el navegador todo de una vez, solo renderiza los componente críticos primero y luego aquellos componentes restantes o cuando se vayan requiriendo. De esta manera no perdemos tiempo ni memoria en procesos que no beneficiarán la experiencia del usuario del producto.

*Code Splitting* es una técnica en la cual podemos tratar costos de cargar Javascript y cumplir con el concepto de *lazy loading* al mismo tiempo.  En lugar de entregar tu aplicación en un único archivo, se divide en varios archivos.

## [](#header-2)Webpack

La forma en que le indicamos a *webpack* que queremos dividir un código en paquetes separados es mediante *dynamic imports*. La palabra clave *import* se puede usar como una función que toma la ruta del módulo que queremos dividir en un paquete separado y devuelve una *promise*:

```js
import('/module/name/here').then(module => ...)
``` 

Cuando el módulo es cargado y la promesa resulta, podemos acceder a lo que exporta.

## [](#header-2)React

React provee dos elementos para implementarlo:

- React.lazy() - React.lazy() es la función que permite implementar la importación dinámica para componentes regulares en React.
- React.Suspense - React.suspense tiene una propiedad *fallback* que toma el elemento react que quiere renderizar mientras el componente se carga usando React.lazy.

## [](#header-2)Niveles


Existen diferentes paradigmas de *code splitting*. Algunos de los más comunes y que se pueden combinar son:

### [](#header-3)Dividir por rutas

Es uno de los enfoques más comunes. Esto nos dejará con un paquete separado para cada ruta de nivel superior. Por ejemplo:

```react
import React, {Suspense} from 'react'
import {Switch, BrowserRouter as Router, Route} from 'react-router-dom';
import importedComponent from 'react-imported-component';

import Home from './Home';
import Loading from './Loading';

const AsyncDynamicPAge = React.lazy(() => import(/* webpackChunkName:'DynamicPage' */ './DynamicPage'));
const AsyncNoMatch = React.lazy(() => import(/* webpackChunkName:'NoMatch' */ './NoMatch'));

const App = () => {
    return (
        <Router>
            <Suspense fallback={Loading}>
                <Switch>
                    <Route exact path="/" component={Home}/>
                    <Route exact path="/dynamic" component={AsyncDynamicPAge}/>
                    <Route component={AsyncNoMatch}/>
                </Switch>
            </Suspense>
        </Router>
    )
        ;
};

export default App;
``` 

Esto creará tres paquetes, uno para el componente DynamicPage, uno para el componente NoMatch y otro para la aplicación principal.

Cambiemos también el nombre del archivo del paquete. En el archivo webpack.config.js se debería cambiar de la siguiente manera: 

```react
...
module.exports = {
  ...
  output: {
    filename: '[name].[hash].js',
    ...
  },
}
``` 

### [](#header-3)Dividir por *Vendor*

Dividamos la aplicación por *vendor*. En el archivo webpack.config.js se debe agregar lo siguiente: 

```react
...
module.exports = {
  entry: {
    vendor: ['semantic-ui-react'],
    app: './src/index.js'
  },
  ...
  optimization: {
    splitChunks: {
      cacheGroups: {
        styles: {
          name: 'styles',
          test: /\.css$/,
          chunks: 'all',
          enforce: true
        },
        vendor: {
          chunks: 'initial',
          test: 'vendor',
          name: 'vendor',
          enforce: true
        }
      }
    }
  },
  ...
};
``` 

- entry.vendor: [‘semantic-ui-react’]: especifica qué biblioteca queremos extraer de nuestra aplicación principal.
- optimization: si omite esta entrada, *webpack* aún dividirá su aplicación por *vendor*, sin embargo, los tamaños de los paquetes serán grandes. En cambio, agregando esta entrada, los tamaños de los paquetes se reducirán significativamente.


Este tipo de *code splitting* te ayudará de dos maneras. Ayuda al buscador a almacenar aquellos recursos que cambian con menos frecuencia. También ayudará al buscador a tomar ventaja de la descarga paralela para reducir, potencialmente, el tiempo de carga.


### [](#header-3)Extraer estilos Css

Al usar ExtractTextWebpackPlugin, puedes extraer todo código CSS a un archivo CSS separado. También se obtienen en general los mismos beneficios que el nivel anterior.

# [](#header-1)Usar Componentes Functionales/*Stateless* 

El componente funcional evita la construcción de la instancia de clase. Reduce el tamaño general del paquete mejor que las clases. 


# [](#header-1)Virtualizar listas largas

Si su aplicación renderiza largas listas de datos (cientos o miles de filas), recomendamos que uses una técnica conocida como *windowing*. Esta técnica solo renderiza un pequeño subconjunto de tus filas en un momento dado, y puede reducir dramáticamente el tiempo que demora en re-renderizar los componentes, así como el número de nodos creados en el DOM.

[react-window](https://react-window.vercel.app/#/examples/list/fixed-size) y [react-virtualized](https://bvaughn.github.io/react-virtualized/#/components/List) son bibliotecas de *windowing* populares. Estas proveen varios componentes reusables para mostrar listas, grillas y datos tabulares.

# [](#header-1)*Throttling* y *Debouncing*

*Throttling* y *Debouncing* son conceptos de optimización importantes para evitar hacer múltiples llamadas a APIs. 

## [](#header-2)Throttling

*Throttling* significa ejecutar la función a intervalos regulares, es decir que una vez que se ejecuta la función, comenzará de nuevo sólo después de que haya transcurrido un período de tiempo específico. 

### [](#header-3)Implementar con *lodash*

Primero instalar lodash:

```js
npm i lodash
```

La implementación de *throttling* para un componente que se encarga de autocompletar:

```react
// ...
import { throttle } from lodash;class autocomp extends React.Component {
    constructor(props) {
        super(props);
        this.state = {
            results: []
        }
       this.handleInputThrottled = throttle(this.handleInput, 100)
    }    handleInput = evt => {
        const value = evt.target.value
        const filteredRes = data.filter((item)=> {
            // algoritmo para buscar a través del array `data`
        })
        this.setState({ results: filteredRes })
    }    render() {
        let { results } = this.state;
        return (
            <div className='autocomp_wrapper'>
                <input placeholder="Enter your search.." onChange={this.handleInputThrottled} />
                <div>
                    {results.map(result=>{result})}
                </div>
            </div>
        );
    }
}
```

La función *throttle* se le pasa una función que quiere ser *throttled* y la cantidad de tiempo que debe ser *throttled*, esto devuelve una función que está *throttled*. En nuestro caso, el método handleInput se pasa a la función *throttle* y queremos que se acelere a 100 ms.

Ahora, si escribimos fidudusola a una velocidad normal de 1 letra por 200ms. En nuestra composición *throttled*, se necesitarían 10 * 200ms = (2000ms) 2s para escribir fidudusola, donde el método handleInput se llamaría 2 (2000ms / 100ms = 2) veces en lugar de 10 veces si nuestro componente de autocomp no se le hubiera aplicado *throttled*.

## [](#header-2)Debouncing

*Debouncing* significa que la función se ejecutará cuando un usuario no haya realizado un evento en un período de tiempo específico. Ignora todas las llamadas a una función y espera hasta que la función deje de llamarse durante un período de tiempo específico.

### [](#header-3)Implementar con *lodash*

Primero instalar lodash:

```react
npm i lodash
```

La implementación de *debouncing* para un componente que se encarga de autocompletar:

```react
// ...
import { debounce } from 'lodash';class autocomp extends React.Component {
    constructor(props) {
        super(props);
        this.state = {
            results: []
        }
       this.handleInputThrottled = debounce(this.handleInput, 100)
    }    handleInput = evt => {
        const value = evt.target.value
        const filteredRes = data.filter((item)=> {
             // algoritmo para buscar a través del array `data`
        })
        this.setState({ results: filteredRes })
    }    render() {
        let { results } = this.state;
        return (
            <div className='autocomp_wrapper'>
                <input placeholder="Enter your search.." onChange={this.handleInputThrottled} />
                <div>
                    {results.map(result=>{result})}
                </div>
            </div>
        );
    }
}
```

# [](#header-1)Activar Gzip en tu Servidor Web

El grupo de archivos JS de la aplicación React son, comúnmente, muy grandes así que para hacer que la página web cargue más rápido, podemos activar Gzip en el servidor web (Apache, Nginx, etc.)

Todos los buscadores modernos apoyan y negocian automáticamente la compresión de Gzip para las peticiones HTTP. Activar la compresión Gzip puede reducir el tamaño de la respuesta transferida en un 90%, lo cual puede reducir la cantidad de tiempo para descargar un recurso, reducir el uso de data para el cliente y mejorar el tiempo para la renderización de tus páginas.

Verifica la documentación para tu servidor web para ver cómo activar la compresión:

- Apache: Usa [mod_deflate](http://httpd.apache.org/docs/current/mod/mod_deflate.html)
- Nginx: Usa [ngx_http_gzip_module](https://nginx.org/en/docs/http/ngx_http_gzip_module.html)


# [](#header-1)Herramientas Útiles

Realmente no podemos hablar de *performance* a menos que haya algo que podamos medir. Para saber dónde mejorar, necesitamos tener algunas ideas. No olvide ejecutar evaluaciones comparativas antes y después de realizar cambios en su código para realizar un seguimiento de su progreso. 

Algunas herramientas que te pueden ayudar a medir son:

## [](#header-2)Why Did You Render

Al trabajar con React, a menudo nos encontramos con situaciones en las cuales una página se ralentiza debido a que ciertos componentes se están volviendo a renderizar cuando no deberían. [Why Did You Render](https://www.npmjs.com/package/@welldone-software/why-did-you-render) es una libería que ayuda a detectar por qué un componente se está volviendo a renderizar; lo consigue al modificar el comportamiento del código en tiempo de ejecución mediante *monkey patching* React. Esta herramienta nos ayuda a investigar problemas de rendimiento.

## [](#header-2)react-addons-perf

react-addons-perf te permite obtener un resumen del desempeño general de tu aplicación.

El uso es el siguiente:

```js
Import Perf from 'react-addons-perf'
Perf.start();
// use the app
Perf.stop();
Perf.printWasted();
```

Esto imprimirá una tabla con la cantidad de componentes de tiempo perdido al renderizar.

La librería proporciona otras funciones que te permiten imprimir diferentes aspectos del tiempo perdido por separado (por ejemplo al usar las funciones printInclusive() o printExclusive()), o hasta imprimir las operaciones de manipulación DOM (usando la función printOperations()).

Para más detalle ver [aquí](https://reactjs.org/docs/perf.html)

## [](#header-2)Webpack Bundle Analyzer

En el contexto del *code splitting*, debemos ser conscientes de qué tan grandes son nuestros paquetes y qué hay realmente en ellos.

*Webpack Bundle Analyzer* proporciona una representación visual de los paquetes de tu aplicación para que pueda ver qué va en cada uno. 

Para más detalle ver [aquí](https://github.com/webpack-contrib/webpack-bundle-analyzer)


## [](#header-2)React Developer Tools

[React Developer Tools](https://chrome.google.com/webstore/detail/react-developer-tools/fmkadmapgofadopljbjfkapdkoienihi?hl=en) es una extensión de *Chrome DevTools* para React. Esta extensión agrega herramientas de depuración React que nos puede ayudar a inspeccionar y editar el árbol de componentes de React que construye, y para cada componente, uno puede verificar los *props*, el estado, los *hooks*, etc. y también nos ayuda a saber si en una aplicación particular React.js ha sido usado o no. 


# [](#header-1)Fuentes

- [Lessons Learned: Code Splitting with Webpack and React](https://hackernoon.com/lessons-learned-code-splitting-with-webpack-and-react-f012a989113)
- [How to use Webpack with React: an in-depth tutorial](https://www.freecodecamp.org/news/learn-webpack-for-react-a36d4cac5060/)
- [Optimizando el Rendimiento](https://es.reactjs.org/docs/optimizing-performance.html)
- [Performance Optimization Techniques For React Applications](https://www.c-sharpcorner.com/article/performance-optimization-techniques-for-react-applications/)
- [Improve Your React App Performance by Using Throttling and Debouncing](https://blog.bitsrc.io/improve-your-react-app-performance-by-using-throttling-and-debouncing-101afbe9055)
- [9 Performance Optimization Strategies for ReactJS Development](https://medium.com/swlh/9-performance-optimization-strategies-for-reactjs-development-36f6d03a0912)
- [Componentes React Eficientes: Una Guía para Optimizar el Desempeño de React](https://www.toptal.com/react/componentes-react-eficientes-una-guia-para-optimizar-el-desempeno-de-react)
- [Top 9: Las Mejores técnicas de optimización para aplicaciones React](https://ourcodeworld.co/articulos/leer/934/top-9-las-mejores-tecnicas-de-optimizacion-para-aplicaciones-react)