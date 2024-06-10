# Composables {#composables}

<script setup>
import { useMouse } from './mouse'
const { x, y } = useMouse()
</script>

:::tip
Esta sección supone un conocimiento básico de la Composition API. Si has estado aprendiendo Vue sólo con la Options API, puedes establecer la Preferencia de la API en la Composition API (utilizando el conmutador en la parte superior de la barra lateral izquierda) y volver a leer los capítulos [Fundamentos de Reactividad](/guide/essentials/reactivity-fundamentals) y [Hooks del Ciclo de Vida](/guide/essentials/lifecycle).
:::

## ¿Qué es un "Composable"? {#what-is-a-composable}

En el contexto de las aplicaciones Vue, un " composable " es una función que aprovecha la Composition API de Vue para encapsular y reutilizar la **lógica de estado**.

Al construir aplicaciones de frontend, a menudo necesitamos reutilizar la lógica para tareas comunes. Por ejemplo, podemos necesitar formatear fechas en muchos lugares, así que extraemos una función reutilizable para ello. Esta función de formato encapsula **lógica sin estado**: toma una entrada y devuelve inmediatamente la salida esperada. Existen muchas librerías para reutilizar la lógica sin estado; por ejemplo [lodash](https://lodash.com/) y [date-fns](https://date-fns.org/), de las que quizás hayas oído hablar.

Por el contrario, la lógica con estado implica la gestión del estado que cambia con el tiempo. Un ejemplo sencillo sería el seguimiento de la posición actual del ratón en una página. En escenarios del mundo real, también podría ser una lógica más compleja, como los gestos táctiles o el estado de conexión a una base de datos.

## Ejemplo de Rastreador de Ratón {#mouse-tracker-example}

Si tuviéramos que implementar la funcionalidad de seguimiento del ratón utilizando la Composition API directamente dentro de un componente, se vería así:

```vue
<script setup>
import { ref, onMounted, onUnmounted } from 'vue'

const x = ref(0)
const y = ref(0)

function update(event) {
  x.value = event.pageX
  y.value = event.pageY
}

onMounted(() => window.addEventListener('mousemove', update))
onUnmounted(() => window.removeEventListener('mousemove', update))
</script>

<template>El ratón se encuentra en la posición: {{ x }}, {{ y }}</template>
```

¿Pero qué pasa si queremos reutilizar la misma lógica en varios componentes? Podemos extraer la lógica en un archivo externo, como una función composable:

```js
// mouse.js
import { ref, onMounted, onUnmounted } from 'vue'

// por convención, los nombres de las funciones composables comienzan con "use"
export function useMouse() {
  // estado encapsulado y gestionado por el composable
  const x = ref(0)
  const y = ref(0)

  // un composable puede actualizar su estado gestionado a lo largo del tiempo.
  function update(event) {
    x.value = event.pageX
    y.value = event.pageY
  }

  // un composable también puede engancharse al ciclo de vida de su componente
  // propietario para configurar y deshacer los efectos secundarios.
  onMounted(() => window.addEventListener('mousemove', update))
  onUnmounted(() => window.removeEventListener('mousemove', update))

  // exponer el estado gestionado como valor de retorno
  return { x, y }
}
```

Y así es como se puede utilizar en los componentes:

```vue
<script setup>
import { useMouse } from './mouse.js'

const { x, y } = useMouse()
</script>

<template>El ratón se encuentra en la posición: {{ x }}, {{ y }}</template>
```

<div class="demo">
  El ratón se encuentra en la posición: {{ x }}, {{ y }}
</div>

[Pruébalo en la Zona de Práctica](https://play.vuejs.org/#eNqNkj1rwzAQhv/KocUOGKVzSAIdurVjoQUvJj4XlfgkJNmxMfrvPcmJkkKHLrbu69H7SlrEszFyHFDsxN6drDIeHPrBHGtSvdHWwwKDwzfNHwjQWd1DIbd9jOW3K2qq6aTJxb6pgpl7Dnmg3NS0365YBnLgsTfnxiNHACvUaKe80gTKQeN3sDAIQqjignEhIvKYqMRta1acFVrsKtDEQPLYxuU7cV8Msmg2mdTilIa6gU5p27tYWKKq1c3ENphaPrGFW25+yMXsHWFaFlfiiOSvFIBJjs15QJ5JeWmaL/xYS/Mfpc9YYrPxl52ULOpwhIuiVl9k07Yvsf9VOY+EtizSWfR6xKK6itgkvQ/+fyNs6v4XJXIsPwVL+WprCiL8AEUxw5s=)

Como podemos ver, el núcleo de la lógica sigue siendo el mismo; todo lo que teníamos que hacer era trasladarlo a una función externa y devolver el estado que debe ser expuesto. Al igual que dentro de un componente, se puede utilizar toda la gama de funciones de la [Composition API](/api/#composition-api) en los composables. La misma funcionalidad `useMouse()` se puede utilizar ahora en cualquier componente.

Lo mejor de los composables es que también se pueden anidar: una función composable puede llamar a otra u otras funciones composables. Esto nos permite componer una lógica compleja utilizando unidades pequeñas y aisladas, de forma similar a como componemos una aplicación completa utilizando componentes. De hecho, esta es la razón por la que hemos decidido llamar Composition API al conjunto de APIs que hacen posible este patrón.

Por ejemplo, podemos extraer la lógica de añadir y eliminar un escuchador de eventos del DOM en su propio composable:

```js
// event.js
import { onMounted, onUnmounted } from 'vue'

export function useEventListener(target, event, callback) {
  // si lo deseas, también puedes hacer que este
  // soporte selectores string como objetivo
  onMounted(() => target.addEventListener(event, callback))
  onUnmounted(() => target.removeEventListener(event, callback))
}
```

Y ahora nuestro composable `useMouse()` puede ser simplificado a:

```js{3,9-12}
// mouse.js
import { ref } from 'vue'
import { useEventListener } from './event'

export function useMouse() {
  const x = ref(0)
  const y = ref(0)

  useEventListener(window, 'mousemove', (event) => {
    x.value = event.pageX
    y.value = event.pageY
  })

  return { x, y }
}
```

:::tip
Cada instancia del componente que llame a `useMouse()` creará sus propias copias del estado `x` e `y` para que no interfieran entre sí. Si quieres gestionar el estado compartido entre componentes, lee el capítulo [Manejo del Estado](/guide/scaling-up/state-management).
:::

## Ejemplo de Estado Asíncrono {#async-state-example}

El composable `useMouse()` no toma ningún argumento, así que veamos otro ejemplo que hace uso de uno. Cuando se hace una recuperación de datos asíncrona, a menudo necesitamos manejar diferentes estados: carga, éxito y error:

```vue
<script setup>
import { ref } from 'vue'

const data = ref(null)
const error = ref(null)

fetch('...')
  .then((res) => res.json())
  .then((json) => (data.value = json))
  .catch((err) => (error.value = err))
</script>

<template>
  <div v-if="error">¡Oops! Error encontrado: {{ error.message }}</div>
  <div v-else-if="data">
    Datos cargados:
    <pre>{{ data }}</pre>
  </div>
  <div v-else>Cargando...</div>
</template>
```

Sería tedioso tener que repetir este patrón en cada componente que necesite obtener datos. Vamos a extraerlos en un composable:

```js
// fetch.js
import { ref } from 'vue'

export function useFetch(url) {
  const data = ref(null)
  const error = ref(null)

  fetch(url)
    .then((res) => res.json())
    .then((json) => (data.value = json))
    .catch((err) => (error.value = err))

  return { data, error }
}
```

Ahora en nuestro componente podemos simplemente hacer:

```vue
<script setup>
import { useFetch } from './fetch.js'

const { data, error } = useFetch('...')
</script>
```

### Aceptando Estado Reactivo {#accepting-reactive-state}

`useFetch()` acepta un argumento de tipo string, que representa una URL estática, por lo que realiza la búsqueda sólo una vez y termina. ¿Qué pasa si queremos que vuelva a correr un nuevo fetch cada vez que la URL cambie? Para lograr esto, necesitamos pasar estado reactivo a la función composable, y dejar a la composable crear watchers que realicen acciones usando el estado dado.

Por ejemplo, `useFetch()` debería ser capaz de aceptar una ref:

```js
const url = ref('/initial-url')

const { data, error } = useFetch(url)

// esto debería activar un nuevo fetch
url.value = '/new-url'
```

O, aceptar una [función getter](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Functions/get#description):

```js
// nuevo fetch cuando cambie props.id
const { data, error } = useFetch(() => `/posts/${props.id}`)
```

Podemos refactorizar nuestra implementacion existente con las APIs [`watchEffect()`](/api/reactivity-core.html#watcheffect) y [`toValue()`](/api/reactivity-utilities.html#tovalue):

```js{8, 13}
// fetch.js
import { ref, watchEffect, toValue } from 'vue'

export function useFetch(url) {
  const data = ref(null)
  const error = ref(null)

  const fetchData = () => {
    // restablecer el estado antes de la recuperación..
    data.value = null
    error.value = null

    fetch(toValue(url))
      .then((res) => res.json())
      .then((json) => (data.value = json))
      .catch((err) => (error.value = err))
  }

  watchEffect(() => {
    fetchData()
  })

  return { data, error }
}
```

`toValue()` es una API añadida en la versión 3.3. Está diseñada para normalizar refs o getters en valores. Si el argumento es una ref, el retorno será el valor de la ref; si el argumento es una función, llamará la función y dará su valor de retorno. De lo contrario, devolverá el argumento como tal. Funciona de manera similar a [`unref()`](/api/reactivity-utilities.html#unref), pero con un tratamiento especial para funciones.

Nota como `toValue(url)` es llamado **dentro** de la llamada de retorno de `watchEffect`. Esto asegura que cualquier dependencia reactiva accedida durante la normalización de `toValue()` sea rastreada por el watcher.

Esta versión de `useFetch()` acepta ahora tanto cadenas de URL estáticas, refs y getters, haciéndola mucho más flexible. El efecto observador se ejecutará inmediatamente, y rastreará cualquier dependencia accedida durante `toValue()`. Si ninguna dependencia es rastreada (por ejemplo, url ya es una cadena de texto), el efecto se ejecuta solo una vez; de lo contrario, volverá a ser ejecutado cuando una dependencia rastreada cambie.

Aquí está [la versión actualizada de `useFetch()`](https://play.vuejs.org/#eNp9Vdtu40YM/RVWL1ZQr5RF0JfAMXpLgRZtd5Fu90kvY4mKJ5FnhLnYMQz/+5IcSZF3g30IbPNyyHPIYU7ZL31f7CNmt9nK1073ATyG2K8ro3e9dQFO4LBdQm13fQzYwBlaZ3ewoKTFLCh6/ANDvZ38RTmaiidPkZWprfEBNsrj/66DO1hsQ+j9bVk+eWv6TtW4tV2DrgjHXte2wYKKlsE21pcEkNJ1Q5nUUb54v7gajVHwxhbz/Aru1lOhHymn2KsuIsWPGSdoVFBLQOeso57vJgI5gc0CHQZ3JHfCPFUGJjimQH1dGt6T5VyZVZnUJB3pR8Ad8QtIvwD+tqqB3gqXWzasNjEEa2D/rrXurso0aAM/VRn8XHe6fmYLk9ZVtj6dQMP5vCpTiqBXYdXoPWXrlkKFEEUyMEH36w+29z/AvfBEIhVNQIfNLRCWBBc79F49ouDy4CVx6GlqQXQg3Af+nNWn0JLKp2+pD+w8pmZYY8r5nT6gI9pcdtU7ZB7sSyXp95sYa1ZKm8eiKEb/qpykzJbZbMFofy/39aDIcd+2WIclBPtZ5nO5u5XBF0lpo6mDJrYXO5CGnbZAmk17Z2LH+zF60gJ95eK/WQO58kdTz1cIoCwphZ4a+EBsYIM0e4SWqwvlFMV1p91i+GROc/vWPoe23R4hbFEeRwrlLrbOGht9dwRvQYeFh+BU/UwPW3lQE0CDPZqG9uUIm+MFFyE4sifspOzdqPHwfF674eczcBZk5YJuda1VR0U6dQTqVpmGxpJWl+ULAOqgdICgd2jjUJTNBBANa30FB911/DyjM8KTrANP3SZmim38QIbSlsLcQfukS4oVlA1nM5DI8E77gUAYb4AngqkjmRCTFLZ8KAT9YlApkrJoMa0ZFTtDzTJCjsNqfTtJHCL54yxHCEaGXx0sOTKVeUPPykzrPKmX6g1IBg/wkZ4B6ZDnw6IsyflE051vKC3npwHgYnPp3rWQ/6PCtkiDI+8aroubGS0uJsAjeabPb/oyhEvm3I+cp3zxkBZBfi2uXlMHWZZwc30tVhbnTBcgeJpQqx9FaLoBgl5l/J9Ad+g+9KyDrzK6dsNIM9V19vCX2IKLuBzt9Rbr5zfsT/6FbVX2kd+r22OVTb6g3COG5L7/7198oe+Tc2eb2FH0d5wPLFLkHlPYr9E01PYsTrr9Uy4bnYVP/v4loPEjKW5U5JD4KqO79tt3qL+2e1PcSB6reP4CbzCltA==), con un retardo artificial y un error aleatorio para fines de demostración.

## Convenciones y Mejores Prácticas {#conventions-and-best-practices}

### Nomenclatura {#naming}

Es una convención nombrar las funciones composables con nombres camelCase que comienzan con "use".

### Argumentos de Entrada {#input-arguments}

Un composable puede aceptar argumentos de referencia o getters incluso si no depende de ellos para la reactividad. Si estás escribiendo un composable que puede ser utilizado por otros desarrolladores, es una buena idea manejar el caso de que los argumentos de entrada sean refs o getters en lugar de valores crudos. La función de ayuda [`toValue()`](/api/reactivity-utilities#tovalue) será muy útil para este propósito:

```js
import { toValue } from 'vue'

function useFeature(maybeRefOrGetter) {
  // si maybeRefOrGetter es una ref o un getter
  // su valor normalizado será retornado
  // de lo contrario, retorna el valor como tal
  const value = toValue(maybeRefOrGetter)
}
```

Si tu composable crea efectos reactivos cuando la entrada es una ref o un getter,asegúrate de vigilar explícitamente la ref / getter con `watch()`, o llama a `toValue()` dentro de un `watchEffect()` para que sea rastreado correctamente.

La [implementación de useFetch() discutida anteriormente](#accepting-reactive-state) da un ejemplo concreto de una composable que acepta refs, getters y valores simples como argumento de entrada.

### Valores de Retorno {#return-values}

Probablemente has notado que hemos estado usando exclusivamente `ref()` en lugar de `reactive()` en los composables. La convención recomendada es que los composables devuelvan siempre un objeto simple, no reactivo, que contenga múltiples refs. Esto permite que se desestructure en los componentes mientras se mantiene la reactividad:

```js
// x e y son refs
const { x, y } = useMouse()
```

Devolver un objeto reactivo desde un composable hará que dichas desestructuras pierdan la conexión de reactividad con el estado dentro del composable, mientras que las refs mantendrán esa conexión.

Si prefieres utilizar el estado devuelto de los composables como propiedades de los objetos, puedes encerrar el objeto devuelto en `reactive()` para que las refs se desplieguen. Por ejemplo:

```js
const mouse = reactive(useMouse())
// mouse.x está vinculado a la ref original
console.log(mouse.x)
```

```vue-html
El ratón se encuentra en la posición: {{ mouse.x }}, {{ mouse.y }}
```

### Efectos Secundarios {#side-effects}

Está bien utilizar efectos secundarios (por ejemplo, añadir escuchadores de eventos del DOM o buscar datos) en composables, pero presta atención a las siguientes reglas:

- Si estás trabajando en una aplicación que utiliza [Renderizado del Lado del Servidor](/guide/scaling-up/ssr) (SSR), asegúrate de ejecutar los efectos secundarios específicos del DOM en los hooks del ciclo de vida posterior al montaje, por ejemplo `onMounted()`. Estos hooks sólo se llaman en el navegador, por lo que puedes estar seguro de que el código dentro de ellos tiene acceso al DOM.

- Recuerda limpiar los efectos secundarios en `onUnmounted()`. Por ejemplo, si un composable establece un escuchador de eventos del DOM, deberías eliminar ese escuchador en `onUnmounted()` como hemos visto en el ejemplo de `useMouse()`. Puede ser una buena idea utilizar un composable que haga esto automáticamente por ti, como el ejemplo de `useEventListener()`.

### Restricciones de Uso {#usage-restrictions}

Los composables solo deben ser llamados solo en `<script setup>` o en el hook `setup()`. También deben ser llamados de manera **sincrónica** en estos contextos. En algunos casos, también puedes llamarlos en hooks del ciclo de vida como `onMounted()`.

Estas restricciones son importantes porque estos son los contextos donde Vue puede determinar la instancia activa actual del componente. El acceso a una instancia activa del componente es necesario para que:

1. Los hooks del ciclo de vida puedan ser registrados en ella.

2. Las propiedades computadas y los watchers puedan ser vinculados a ella, de manera que puedan ser eliminados cuando la instancia sea desmontada para evitar pérdidas de memoria.

:::tip
`<script setup>` es el único lugar donde puedes llamar a composables **después** de usar `await`. El compilador restaura automáticamente el contexto de la instancia activa por ti después de la operación asíncrona.
:::

## Extracción de Composables para la Organización del Código {#extracting-composables-for-code-organization}

Los composables pueden ser extraídos no sólo para su reutilización, sino también para la organización del código. A medida que la complejidad de sus componentes crece, puede terminar con componentes que son demasiado grandes para navegar y razonar. La Composition API te da toda la flexibilidad para organizar el código de tus componentes en funciones más pequeñas basadas en aspectos lógicos:

```vue
<script setup>
import { useFeatureA } from './featureA.js'
import { useFeatureB } from './featureB.js'
import { useFeatureC } from './featureC.js'

const { foo, bar } = useFeatureA()
const { baz } = useFeatureB(foo)
const { qux } = useFeatureC(baz)
</script>
```

Hasta cierto punto, se puede pensar en estos composables extraídos como servicios con alcance de componente que pueden hablar entre sí.

## Uso de los Composables en la Options API {#using-composables-in-options-api}

Si estás usando la Options API, los composables deben ser llamados dentro de `setup()`, y los bindings retornados deben ser devueltos desde `setup()` para que sean expuestos a `this` y a la plantilla:

```js
import { useMouse } from './mouse.js'
import { useFetch } from './fetch.js'

export default {
  setup() {
    const { x, y } = useMouse()
    const { data, error } = useFetch('...')
    return { x, y, data, error }
  },
  mounted() {
    // Las propiedades exhibidas por setup() pueden ser accedidas por `this`.
    console.log(this.x)
  }
  // ...otras opciones
}
```

## Comparación con Otras Técnicas {#comparisons-with-other-techniques}

### vs. Mixins {#vs-mixins}

Los usuarios que vienen de Vue 2 pueden estar familiarizados con la opción [mixins](/api/options-composition#mixins), que también nos permite extraer la lógica del componente en unidades reutilizables. Los mixins tienen tres inconvenientes principales:

1. **Origen de las propiedades poco claro**: cuando se utilizan muchos mixins, no queda claro qué propiedad de instancia es inyectada por cada mixin, lo que dificulta el seguimiento de la implementación y la comprensión del comportamiento del componente. Esta es también la razón por la que recomendamos utilizar el patrón refs + desestructuración para los composables: hace que la fuente de la propiedad sea clara en los componentes consumidores.

2. **Colisiones de nombre de espacio**: múltiples mixins de diferentes autores pueden potencialmente registrar las mismas keys de propiedades, causando colisiones de nombre de espacio. Con los composables, puedes renombrar las variables desestructuradas si hay keys conflictivas de diferentes composables.

3. **Comunicación implícita entre mixins**: varios mixins que necesitan interactuar entre sí tienen que depender de keys de propiedades compartidas, haciéndolos implícitamente acoplados. Con los composables, los valores devueltos por un composable pueden ser pasados a otro como argumentos, al igual que las funciones normales.

Por las razones anteriores, ya no recomendamos el uso de mixins en Vue 3. La función se mantiene sólo por razones de migración y familiaridad.

### vs. Componentes sin Renderizado {#vs-renderless-components}

En el capítulo sobre slots de los componentes, discutimos el patrón [Componentes sin Renderizado](/guide/components/slots#renderless-components) basado en slots de ámbito. Incluso implementamos la misma demostración de seguimiento del ratón utilizando componentes sin renderizado.

La principal ventaja de los composables sobre los componentes sin renderizado es que los composables no implican la sobrecarga adicional de las instancias de los componentes. Cuando se utilizan en toda una aplicación, la cantidad de instancias de componentes adicionales creadas por el patrón de componentes sin renderizado puede convertirse en una sobrecarga notable para el rendimiento.

La recomendación es utilizar composables cuando se reutiliza la lógica pura, y utilizar componentes cuando se reutiliza tanto la lógica como el diseño visual.

### vs. Hooks de React {#vs-react-hooks}

Si tienes experiencia con React, puedes notar que esto se parece mucho a los hooks personalizados de React. La Composition API se inspiró en parte en los hooks de React, y los composables de Vue son de hecho similares a los hooks de React en cuanto a las capacidades de composición de la lógica. Sin embargo, los composables de Vue se basan en el sistema de reactividad de precisión de Vue, que es fundamentalmente diferente del modelo de ejecución de los hooks de React. Esto se discute con más detalle en las [FAQ de la Composition API](/guide/extras/composition-api-faq#comparacion-con-los-hooks-de-react).

## Lecturas Adicionales {#further-reading}

- [Reactividad en profundidad](/guide/extras/reactivity-in-depth): para una comprensión de bajo nivel de cómo funciona el sistema de reactividad de Vue.
- [Manejo del Estado](/guide/scaling-up/state-management): para conocer los patrones de gestión del estado compartido por varios componentes.
- [Pruebas de Composables](/guide/scaling-up/testing#testing-composables): tips para realizar pruebas unitarias en composables.
- [VueUse](https://vueuse.org/): una colección cada vez mayor de composables de Vue. El código fuente es también un gran recurso de aprendizaje.
