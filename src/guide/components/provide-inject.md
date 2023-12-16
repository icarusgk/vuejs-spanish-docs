# Provide / Inject {#provide-inject}

> Esta página supone que ya has leído los [Fundamentos de los Componentes](/guide/essentials/component-basics). Léelo primero si eres nuevo en el tema de componentes.

## Profundización de Prop {#prop-drilling}

Normalmente, cuando necesitamos pasar datos del componente padre a un componente hijo, utilizamos [props](/guide/components/props). Sin embargo, imaginemos el caso en el que tenemos un gran árbol de componentes, y un componente profundamente anidado necesita algo de un componente ancestral lejano. Con sólo props, tendríamos que pasar la misma prop a través de toda la cadena de padres:

![diagrama de profundización de prop](./images/prop-drilling.png)

<!-- https://www.figma.com/file/yNDTtReM2xVgjcGVRzChss/prop-drilling -->

Fíjate en que, aunque al componente `<Footer>` no le importen en absoluto estas props, tiene que declararlas y pasarlas para que `<DeepChild>` pueda acceder a ellas. Si hay una cadena de padres más larga, más componentes se verán afectados en el camino. Esto se llama "profundización de props" y definitivamente no es divertido de tratar.

Podemos resolver la profundización de props con `provide` e `inject`. Un componente padre puede servir como **proveedor de dependencia** para todos sus descendientes. Cualquier componente en el árbol descendiente, independientemente de su profundidad, puede **inyectar** dependencias proporcionadas por los componentes de su cadena padre.

![esquema de Provide/inject](./images/provide-inject.png)

<!-- https://www.figma.com/file/PbTJ9oXis5KUawEOWdy2cE/provide-inject -->

## Provide {#provide}

<div class="composition-api">

Para proporcionar datos a los descendientes de un componente, utiliza la función [`provide()`](/api/composition-api-dependency-injection#provide):

```vue
<script setup>
import { provide } from 'vue'

provide(/* key */ 'message', /* value */ 'hola!')
</script>
```

Si no usas `<script setup>`, asegúrate de que `provide()` sea sincrónicamente llamado dentro de `setup()`:

```js
import { provide } from 'vue'

export default {
  setup() {
    provide(/* key */ 'message', /* value */ 'hola!')
  }
}
```

La función `provide()` acepta dos argumentos. El primer argumento se llama **key de inyección**, que puede ser un string o un `Symbol`. La key de inyección es utilizada por los componentes descendientes para buscar el valor deseado a inyectar. Un mismo componente puede llamar a `provide()` varias veces con diferentes claves de inyección para proporcionar diferentes valores.

El segundo argumento es el valor proporcionado. El valor puede ser de cualquier tipo, incluyendo el estado reactivo como las refs:

```js
import { ref, provide } from 'vue'

const count = ref(0)
provide('key', count)
```

Proporcionar valores reactivos permite a los componentes descendientes que utilizan el valor proporcionado establecer una conexión reactiva con el componente proveedor.

</div>

<div class="options-api">

Para proporcionar datos a los descendientes de un componente, utiliza la opción [`provide`](/api/options-composition#provide):

```js
export default {
  provide: {
    message: 'hola!'
  }
}
```

Para cada propiedad en el objeto `provide`, la key es utilizada por los componentes hijos para localizar el valor correcto a inyectar, mientras que el valor es lo que termina siendo inyectado.

Si necesitamos proporcionar un estado por instancia, por ejemplo datos declarados a través de `data()`, entonces `provide` debe utilizar un valor de función:

```js{7-12}
export default {
  data() {
    return {
      message: 'hola!'
    }
  },
  provide() {
    // usar la sintaxis de función para poder acceder a `this`.
    return {
      message: this.message
    }
  }
}
```

Sin embargo, tenga en cuenta que esto **no** hace que la inyección sea reactiva. Hablaremos de [hacer reactivas las inyecciones](#trabajando-con-reactividad) más adelante.

</div>

## Nivel de Aplicación de Provide {#app-level-provide}

Además de proporcionar datos en un componente, también podemos hacerlo a nivel de la aplicación:

```js
import { createApp } from 'vue'

const app = createApp({})

app.provide(/* key */ 'message', /* value */ 'hola!')
```

Los valores del nivel de la aplicación están disponibles para todos los componentes renderizados en la aplicación. Esto es especialmente útil cuando se escriben [plugins](/guide/reusability/plugins), ya que los plugins normalmente no podrían proporcionar valores usando componentes.

## Inject {#inject}

<div class="composition-api">

Para inyectar datos proporcionados por un componente ancestro, utiliza la función [`inject()`](/api/composition-api-dependency-injection#inject):

```vue
<script setup>
import { inject } from 'vue'

const message = inject('message')
</script>
```

Si el valor proporcionado es una ref, se inyectará tal cual y **no** se desempaquetará automáticamente. Esto permite que el componente inyector conserve la conexión de reactividad con el componente proveedor.

[Ejemplo de provide + inject Total con Reactividad](https://play.vuejs.org/#eNqFUUFugzAQ/MrKF1IpxfeIVKp66Kk/8MWFDXYFtmUbpArx967BhURRU9/WOzO7MzuxV+fKcUB2YlWovXYRAsbBvQije2d9hAk8Xo7gvB11gzDDxdseCuIUG+ZN6a7JjZIvVRIlgDCcw+d3pmvTglz1okJ499I0C3qB1dJQT9YRooVaSdNiACWdQ5OICj2WwtTWhAg9hiBbhHNSOxQKu84WT8LkNQ9FBhTHXyg1K75aJHNUROxdJyNSBVBp44YI43NvG+zOgmWWYGt7dcipqPhGZEe2ef07wN3lltD+lWN6tNkV/37+rdKjK2rzhRTt7f3u41xhe37/xJZGAL2PLECXa9NKdD/a6QTTtGnP88LgiXJtYv4BaLHhvg==)

De nuevo, si no se utiliza `<script setup>`, `inject()` sólo debe llamarse de forma sincrónica dentro de `setup()`:

```js
import { inject } from 'vue'

export default {
  setup() {
    const message = inject('message')
    return { message }
  }
}
```

</div>

<div class="options-api">

Para inyectar los datos proporcionados por un componente ancestro, utiliza la opción [`inject`](/api/options-composition#inject):

```js
export default {
  inject: ['message'],
  created() {
    console.log(this.message) // valor inyectado
  }
}
```

Las inyecciones se resuelven **antes** del propio estado del componente, por lo que se puede acceder a las propiedades inyectadas en `data()`:

```js
export default {
  inject: ['message'],
  data() {
    return {
      // datos iniciales basados en el valor inyectado
      fullMessage: this.message
    }
  }
}
```

[Ejemplo de provide + inject total](https://play.vuejs.org/#eNqNkcFqwzAQRH9l0EUthOhuRKH00FO/oO7B2JtERZaEvA4F43+vZCdOTAIJCImRdpi32kG8h7A99iQKobs6msBvpTNt8JHxcTC2wS76FnKrJpVLZelKR39TSUO7qreMoXRA7ZPPkeOuwHByj5v8EqI/moZeXudCIBL30Z0V0FLXVXsqIA9krU8R+XbMR9rS0mqhS4KpDbZiSgrQc5JKQqvlRWzEQnyvuc9YuWbd4eXq+TZn0IvzOeKr8FvsNcaK/R6Ocb9Uc4FvefpE+fMwP0wH8DU7wB77nIo6x6a2hvNEME5D0CpbrjnHf+8excI=)

### Asignación de Injection \* {#injection-aliasing}

Cuando se utiliza la sintaxis de array para `inject`, las propiedades inyectadas se exponen en la instancia del componente utilizando la misma key. En el ejemplo anterior, la propiedad se ha proporcionado bajo la key `"message"`, y se ha inyectado como `this.message`. La key local es la misma que la key de inyección.

Si queremos inyectar la propiedad usando una key local diferente, necesitamos usar la sintaxis del objeto para la opción `inject`:

```js
export default {
  inject: {
    /* key local */ localMessage: {
      from: /* key de injection */ 'message'
    }
  }
}
```

Aquí, el componente localizará una propiedad proporcionada con la key `"message"`, y luego la expondrá como `this.localMessage`.

</div>

### Valores Default de Injection {#injection-default-values}

Por defecto, `inject` supone que la key inyectada se proporciona en algún lugar de la cadena padre. En el caso de que no se proporcione la key, habrá una advertencia en tiempo de ejecución.

Si queremos que una propiedad inyectada funcione con proveedores opcionales, debemos declarar un valor por defecto, similar a las props:

<div class="composition-api">

```js
// `value` será el "valor default"
// si no se proporcionan datos que coincidan con "message"
const value = inject('message', 'default value')
```

En algunos casos, puede ser necesario crear el valor por defecto llamando a una función o instanciando una nueva clase. Para evitar cálculos innecesarios o efectos secundarios en caso de que no se utilice el valor opcional, podemos utilizar una factory function para crear el valor por defecto:

```js
const value = inject('key', () => new ExpensiveClass())
```

</div>

<div class="options-api">

```js
export default {
  // la sintaxis del objeto es necesaria cuando se
  // declaran valores por defecto para las inyecciones
  inject: {
    message: {
      from: 'message', // esto es opcional si se utiliza la misma key para la inyección
      default: 'default value'
    },
    user: {
      // utiliza una factory function para los valores no
      // primitivos que son difíciles de crear o para los que
      // deben ser únicos por cada instancia del componente.
      default: () => ({ name: 'John' })
    }
  }
}
```

</div>

## Trabajando con Reactividad {#working-with-reactivity}

<div class="composition-api">

Cuando se utilizan valores reactivos de provide / inject, **se recomienda mantener las mutaciones del estado reactivo dentro del _provider_ siempre que sea posible**. Esto asegura que el estado proporcionado y sus posibles mutaciones están co-localizados en el mismo componente, haciendo más fácil su mantenimiento en el futuro.

Puede haber ocasiones en las que necesitemos actualizar los datos de un componente inyector. En estos casos, recomendamos proporcionar una función que se encargue de mutar el estado:

```vue{7-9,13}
<!-- dentro del componente proveedor -->
<script setup>
import { provide, ref } from 'vue'

const location = ref('Polo Norte')

function updateLocation() {
  location.value = 'Polo Sur'
}

provide('location', {
  location,
  updateLocation
})
</script>
```

```vue{5}
<!-- en el componente inyector -->
<script setup>
import { inject } from 'vue'

const { location, updateLocation } = inject('location')
</script>

<template>
  <button @click="updateLocation">{{ location }}</button>
</template>
```

Finalmente, puedes envolver el valor proporcionado con [`readonly()`](/api/reactivity-core#readonly) si quieres asegurarte de que los datos pasados a través de `provide` no pueden ser mutados por el componente inyector.

```vue
<script setup>
import { ref, provide, readonly } from 'vue'

const count = ref(0)
provide('read-only-count', readonly(count))
</script>
```

</div>

<div class="options-api">

Para que las inyecciones estén vinculadas de forma reactiva al proveedor, debemos proporcionar una propiedad computada utilizando la función [computed()](/api/reactivity-core#computed):

```js{10}
import { computed } from 'vue'

export default {
  data() {
    return {
      message: 'hola!'
    }
  },
  provide() {
    return {
      // proporcionar explícitamente una propiedad computada
      message: computed(() => this.message)
    }
  }
}
```

[Ejemplo de provide + inject Total con Reactividad](https://play.vuejs.org/#eNqNUctqwzAQ/JVFFyeQxnfjBEoPPfULqh6EtYlV9EKWTcH43ytZtmPTQA0CsdqZ2dlRT16tPXctkoKUTeWE9VeqhbLGeXirheRwc0ZBds7HKkKzBdBDZZRtPXIYJlzqU40/I4LjjbUyIKmGEWw0at8UgZrUh1PscObZ4ZhQAA596/RcAShsGnbHArIapTRBP74O8Up060wnOO5QmP0eAvZyBV+L5jw1j2tZqsMp8yWRUHhUVjKPoQIohQ460L0ow1FeKJlEKEnttFweijJfiORElhCf5f3umObb0B9PU/I7kk17PJj7FloN/2t7a2Pj/Zkdob+x8gV8ZlMs2de/8+14AXwkBngD9zgVqjg2rNXPvwjD+EdlHilrn8MvtvD1+Q==)

La función `computed()` se utiliza normalmente en los componentes de la Composition API, pero también puede utilizarse para complementar ciertos casos de uso en la Options API. Puedes aprender más sobre su uso leyendo los [Fundamentos de Reactividad](/guide/essentials/reactivity-fundamentals) y [Propiedades Computadas](/guide/essentials/computed) con la Preferencia de API establecida en Composition API.

:::warning Se Requiere Configuración Temporal
El uso anterior requiere establecer `app.config.unwrapInjectedRef = true` para que las inyecciones desempaqueten automáticamente las refs computadas. Esto se convertirá en el comportamiento por defecto en Vue 3.3 y esta configuración se introduce temporalmente para evitar roturas. Ya no será necesario después de la versión 3.3.
:::

</div>

## Trabajando con Symbol Keys {#working-with-symbol-keys}

Hasta ahora, hemos estado utilizando key de inyección de cadenas en los ejemplos. Si estás trabajando en una aplicación grande con muchos proveedores de dependencias, o estás creando componentes que van a ser utilizados por otros desarrolladores, es mejor utilizar keys de inyección de Symbol para evitar potenciales conflictos.

Se recomienda exportar los Symbols en un archivo dedicado:

```js
// keys.js
export const myInjectionKey = Symbol()
```

<div class="composition-api">

```js
// en el componente proveedor
import { provide } from 'vue'
import { myInjectionKey } from './keys.js'

provide(myInjectionKey, {
  /* datos para proveer */
})
```

```js
// en el componente inyector
import { inject } from 'vue'
import { myInjectionKey } from './keys.js'

const injected = inject(myInjectionKey)
```

Mira también: [Escritura de Provide / Inject](/guide/typescript/composition-api#typing-provide-inject) <sup class="vt-badge ts" />

</div>

<div class="options-api">

```js
// en el componente proveedor
import { myInjectionKey } from './keys.js'

export default {
  provide() {
    return {
      [myInjectionKey]: {
        /* datos para proveer */
      }
    }
  }
}
```

```js
// en el componente inyector
import { myInjectionKey } from './keys.js'

export default {
  inject: {
    injected: { from: myInjectionKey }
  }
}
```

</div>
