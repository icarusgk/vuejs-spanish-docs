# Watchers {#watchers}

## Ejemplo Básico {#basic-example}

Las propiedades computadas nos permiten calcular declarativamente valores derivados. Sin embargo, hay casos en los que necesitamos producir "efectos secundarios" como reacción a los cambios de estado; por ejemplo, mutando el DOM o cambiando otra parte del estado en función del resultado de una operación asíncrona.

<div class="options-api">

Con la Options API, podemos utilizar la [opción `watch`](/api/options-state#watch) para disparar una función cada vez que cambie una propiedad reactiva:

```js
export default {
  data() {
    return {
      question: '',
      answer:
        'Las preguntas suelen contener un signo de interrogación. ;-)'
    }
  },
  watch: {
    // cada vez que la pregunta cambie, esta función se ejecutará
    question(newQuestion, oldQuestion) {
      if (newQuestion.includes('?')) {
        this.getAnswer()
      }
    }
  },
  methods: {
    async getAnswer() {
      this.answer = 'Pensando...'
      try {
        const res = await fetch('https://yesno.wtf/api')
        this.answer = (await res.json()).answer
      } catch (error) {
        this.answer = '¡Error! No se ha podido acceder a la API. ' + error
      }
    }
  }
}
```

```vue-html
<p>
  Hacer una pregunta de sí/no:
  <input v-model="question" />
</p>
<p>{{ answer }}</p>
```

[Pruébalo en la Zona de Práctica](https://play.vuejs.org/#eNptUk2PmzAQ/SuvXAA1sdVrmt0qqnroqa3UIxcLhuCGjKk/wkYR/70OBJLuroRkPDPvzbznuSS7rhOnQMkm2brS6s4/F0wvnbEeFdUqtB6XgoFKeZXl0z9gyQfL8w34G8h5bXiDNF3NQcWuJxtDv25Zh+CCatszSsNeaYZakDgqexD4vM7TCT9cj2Ek65Uvm83cTUr0DTGdyN7RZaN4T24F32iHOnA5hnvdtrCBJ+RcnTH180wrmLaaL4s+QNd4LBOaK3r5UWfplzTHM9afHmoxdhV78rtRcpbPmVHEf1qO5BtTuUWNcmcu8QC9046kk4l4Qvq70XzQvBdC3CyKJfb8OEa01fn4OC7Wq15pj5qidVnaeN+5jZRncmxE72upOp0uY77ulU3gSCT+uOhXnt9yiy6U1zdBRtYa+9aK+9TfrgUf8NWEtgKbK6mKQN8Qdj+/C6T4iJHkXcsKjt9WLpsZL56OXas8xRuw7cYD2LlDXKYoT7K5b+OU22rugsdpfTQVtU9FMueLBHKikRNPpLtcbnuLYZjCW7m0TIZ/92UFiQ==)

La opción `watch` también admite una ruta definida por puntos como clave:

```js
export default {
  watch: {
    // Nota: sólo rutas simples. Las expresiones no son soportadas.
    'some.nested.key'(newValue) {
      // ...
    }
  }
}
```

</div>

<div class="composition-api">

Con la Composition API, podemos usar la [función `watch`](/api/reactivity-core#watch) para accionar un callback cada vez que cambie un trozo de estado reactivo:

```vue
<script setup>
import { ref, watch } from 'vue'

const question = ref('')
const answer = ref(
  'Las preguntas suelen contener un signo de interrogación. ;-)'
)

// watch funciona directamente sobre una ref
watch(question, async (newQuestion, oldQuestion) => {
  if (newQuestion.indexOf('?') > -1) {
    answer.value = 'Pensando...'
    try {
      const res = await fetch('https://yesno.wtf/api')
      answer.value = (await res.json()).answer
    } catch (error) {
      answer.value = 'Error! No se ha podido acceder a la API. ' + error
    }
  }
})
</script>

<template>
  <p>
    Hacer una pregunta de sí/no:
    <input v-model="question" />
  </p>
  <p>{{ answer }}</p>
</template>
```

[Pruébalo en la Zona de Práctica](https://play.vuejs.org/#eNplkkGPmzAQhf/KKxdA3Rj1mpJUUdVDT22lHrlYxDRuYOzaJjRC/PcdxyGr3b2A7PfmmzcMc3awVlxGlW2z2rdO2wCvwmj3DenBGhcww6nuCZMM7QkLOmcG5FyRN9RQa8gH/BuVD9oQdtFb5Hm5KpL8pNx6/+vu8xj9KPv+CnYFqQnyhTFIdxb4vCkjpaFb32JVnyD9lVoUpKaVVmK3x9wQoLtXgtB0VP9/cOMveYk9Np/K5MM9l7jIflScLv990nTW9EcIwXNFR3DX1YwYk4dxyrNXTlIHdCrGyk8hWL+tqqvyZMQUukpaHYOnujdtilTLHPHXGyrKUiRH8i9obx+5UM4Z98j6Pu23qH/AVzP2R5CJRMl14aRw+PldIMdH3Bh3bnzxY+FcdZW2zPvlQ1CD7WVQfALquPToP/gzL4RHqsg89rJNWq3JjgGXzWCOqt812ao3GaqEqRKHcfO8/gDLkq7r6tEyW54Bf5TTlg==)

### Observar Tipos de Fuentes {#watch-source-types}

El primer argumento de `watch` pueden ser diferentes tipos de "fuentes" reactivas: puede ser una ref (incluyendo refs computadas), un objeto reactivo, una función getter, o un array de múltiples fuentes:

```js
const x = ref(0)
const y = ref(0)

// ref simple
watch(x, (newX) => {
  console.log(`x es ${newX}`)
})

// getter
watch(
  () => x.value + y.value,
  (sum) => {
    console.log(`la suma de x + y es: ${sum}`)
  }
)

// array de múltiples fuentes
watch([x, () => y.value], ([newX, newY]) => {
  console.log(`x es ${newX} y y es ${newY}`)
})
```

Ten en cuenta que no puedes observar una propiedad de un objeto reactivo de esta manera:

```js
const obj = reactive({ count: 0 })

// esto no funcionará porque estamos pasando un número a watch()
watch(obj.count, (count) => {
  console.log(`El contador está en: ${count}`)
})
```

En su lugar, utiliza un getter:

```js
// en su lugar, utiliza un getter:
watch(
  () => obj.count,
  (count) => {
    console.log(`El contador está en: ${count}`)
  }
)
```

</div>

## Watchers Profundos {#deep-watchers}

<div class="options-api">

`watch` es superficial por defecto: el callback sólo se activará cuando a la propiedad observada se le asigne un nuevo valor; no se activará en los cambios de las propiedades anidadas. Si quieres que el callback se dispare en todas las mutaciones anidadas, necesitas usar un watcher profundo:

```js
export default {
  watch: {
    someObject: {
      handler(newValue, oldValue) {
        // Nota: `newValue` será igual a `oldValue` aquí
        // en las mutaciones anidadas siempre que el propio
        // objeto no haya sido reemplazado.
      },
      deep: true
    }
  }
}
```

</div>

<div class="composition-api">

Cuando llamas a `watch()` directamente en un objeto reactivo, se creará implícitamente un watcher profundo; el callback se disparará en todas las mutaciones anidadas:

```js
const obj = reactive({ count: 0 })

watch(obj, (newValue, oldValue) => {
  // se dispara en las mutaciones de propiedades anidadas
  // Nota: `newValue` será igual a `oldValue` aquí
  // porque ¡ambos apuntan al mismo objeto!
})

obj.count++
```

Esto debe diferenciarse de un getter que devuelve un objeto reactivo; en este último caso, el callback sólo se disparará si el getter devuelve un objeto diferente:

```js
watch(
  () => state.someObject,
  () => {
    // se dispara sólo cuando state.someObject es reemplazado
  }
)
```

Sin embargo, puedes forzar el segundo caso en un watcher profundo utilizando explícitamente la opción `deep`:

```js
watch(
  () => state.someObject,
  (newValue, oldValue) => {
    // Nota: `newValue` será igual a `oldValue` aquí
    // *salvo* que state.someObject haya sido reemplazado
  },
  { deep: true }
)
```

</div>

:::warning Usar con precaución
La vigilancia profunda requiere recorrer todas las propiedades anidadas en el objeto vigilado, y puede ser costosa cuando se utiliza en estructuras de datos grandes. Utilízala sólo cuando sea necesario y ten cuidado con las implicaciones de rendimiento.
:::

## Watchers "Entusiastas" (Eager Watchers) {#eager-watchers}

La función `watch` es perezosa por defecto: el callback no será llamado hasta que la fuente observada haya cambiado. Pero en algunos casos podemos querer que la misma lógica de callback se ejecute con urgencia; por ejemplo, podemos querer obtener algunos datos iniciales, y luego volver a obtener los datos cada vez que el estado relevante cambie.

<div class="options-api">

Podemos forzar que el callback de un watcher se ejecute inmediatamente declarándolo mediante un objeto con una función `handler` y la opción `immediate: true`:

```js
export default {
  // ...
  watch: {
    question: {
      handler(newQuestion) {
        // esto se ejecutará inmediatamente al crear el componente.
      },
      // forzar la ejecución del callback
      immediate: true
    }
  }
  // ...
}
```

La ejecución inicial de la función handler tendrá lugar justo antes del hook `created`. Vue ya habrá procesado las opciones `data`, `computed` y `methods`, por lo que esas propiedades estarán disponibles en la primera invocación.

</div>

<div class="composition-api">

Podemos forzar que la llamada de retorno de un watcher se ejecute inmediatamente pasando la opción `immediate: true`:

```js
watch(
  source,
  (newValue, oldValue) => {
    // ...
  },
  { immediate: true }
)
```

</div>

<div class="composition-api">

## `watchEffect()` \*\* {#watcheffect}

Es habitual que la llamada de retorno del watcher utilice exactamente el mismo estado reactivo que la fuente. Por ejemplo, considera el siguiente código, que utiliza un watcher para cargar un recurso remoto cada vez que la ref `todoId` cambia:

```js
const todoId = ref(1)
const data = ref(null)

watch(todoId, async, () => {
  const response = await fetch(
    `https://jsonplaceholder.typicode.com/todos/${todoId.value}`
  )
  data.value = await response.json()
}, { immediate: true })
```

En particular, observa cómo el watcher utiliza `todoId` dos veces, una como fuente y otra dentro del callback.

Esto puede simplificarse con [`watchEffect()`](/api/reactivity-core#watcheffect). `watchEffect()` nos permite rastrear automáticamente las dependencias reactivas del callback. El watcher anterior puede reescribirse como:

```js
watchEffect(async () => {
  const response = await fetch(
    `https://jsonplaceholder.typicode.com/todos/${todoId.value}`
  )
  data.value = await response.json()
})
```

Aquí, el callback se ejecutará inmediatamente. Durante su ejecución, también hará un seguimiento automático de `url.value` como dependencia (similar a las propiedades computadas). Cada vez que `url.value` cambie, el callback se ejecutará de nuevo.

Puedes ver [este ejemplo](/examples/#fetching-data) con `watchEffect` y la obtención reactiva de datos en acción.

Para ejemplos como estos, con una sola dependencia, el beneficio de `watchEffect()` es relativamente pequeño. Pero para watchers que tienen múltiples dependencias, el uso de `watchEffect()` elimina la carga de tener que mantener la lista de dependencias manualmente. Además, si necesitas vigilar varias propiedades en una estructura de datos anidada, `watchEffect()` puede resultar más eficiente que un watcher profundo, ya que sólo realizará un seguimiento de las propiedades que se utilizan en la llamada de retorno, en lugar de realizar un seguimiento recursivo de todas ellas.

:::tip
`watchEffect` sólo rastrea las dependencias durante su ejecución **sincrónica**. Cuando se utiliza con un callback asíncrono, sólo se rastrean las propiedades a las que se accede antes del primer paso del `await`.
:::

### `watch` vs. `watchEffect` {#watch-vs-watcheffect}

Tanto `watch` como `watchEffect` nos permiten producir efectos secundarios de forma reactiva. Su principal diferencia es la forma en que rastrean sus dependencias reactivas:

- `watch` sólo rastrea la fuente explícitamente observada. No rastreará nada de lo que se acceda dentro del callback. Además, el callback sólo se activa cuando la fuente ha cambiado realmente. El sistema `watch` separa el seguimiento de las dependencias del efecto secundario, lo que nos da un control más preciso sobre el momento en el que debe dispararse la llamada de retorno.

- Por otro lado, `watchEffect` combina el seguimiento de la dependencia y el efecto secundario en una sola fase. Rastrea automáticamente cada propiedad reactiva a la que se accede durante su ejecución sincrónica. Esto es más conveniente y típicamente resulta en un código más breve, pero hace que sus dependencias reactivas sean menos explícitas.

</div>

## Temporización del Flujo del Callback {#callback-flush-timing}

Cuando mutas el estado reactivo, puede desencadenar tanto las actualizaciones de los componentes de Vue como los callbacks de los watchers creados por ti.

De forma predeterminada, los callbacks del watcher creados por el usuario son llamados **antes** de las actualizaciones del componente Vue. Esto significa que si intentas acceder al DOM dentro de un callback de vigilancia, el DOM estará en el estado antes de que Vue haya aplicado cualquier actualización.

Si quieres acceder al DOM en un callback de vigilancia **después** de que Vue lo haya actualizado, debes especificar la opción `flush: 'post'`:

<div class="options-api">

```js
export default {
  // ...
  watch: {
    key: {
      handler() {},
      flush: 'post'
    }
  }
}
```

</div>

<div class="composition-api">

```js
watch(source, callback, {
  flush: 'post'
})

watchEffect(callback, {
  flush: 'post'
})
```

La función `watchEffect()` post-flujo también tiene un alias conveniente, `watchPostEffect()`:

```js
import { watchPostEffect } from 'vue'

watchPostEffect(() => {
  /* ejecutado después de las actualizaciones de Vue */
})
```

</div>

<div class="options-api">

## `this.$watch()` \* {#this-watch}

También es posible crear imperativamente watchers utilizando el [método de instancia `$watch()`](/api/component-instance#watch):

```js
export default {
  created() {
    this.$watch('question', (newQuestion) => {
      // ...
    })
  }
}
```

Esto resulta útil cuando se necesita configurar condicionalmente un watcher, o sólo vigilar algo en respuesta a la interacción del usuario. También te permite detener el watcher antes de tiempo.

</div>

## Detención de un Watcher {#stopping-a-watcher}

<div class="options-api">

Los watchers declarados usando la opción `watch` o el método de instancia `$watch()` se detienen automáticamente cuando el componente propietario es desmontado, por lo que en la mayoría de los casos no necesitas preocuparte por detener el watcher tú mismo.

En el extraño caso de que necesites detener un watcher antes de que el componente propietario se desmonte, la API `$watch()` devuelve una función para ello:

```js
const unwatch = this.$watch('foo', callback)

// ...cuando el observador ya no es necesario:
unwatch()
```

</div>

<div class="composition-api">

Los watchers declarados sincrónicamente dentro de `setup()` o `<script setup>` están vinculados a la instancia del componente propietario, y se detendrán automáticamente cuando el componente propietario sea desmontado. En la mayoría de los casos, no es necesario preocuparse por detener el watcher.

La clave aquí es que el watcher debe ser creado **sincrónicamente**: si el watcher es creado en un callback asíncrono, no estará ligado al componente propietario y debe ser detenido manualmente para evitar fugas de memoria. Este es un ejemplo:

```vue
<script setup>
import { watchEffect } from 'vue'

// este se detendrá automáticamente
watchEffect(() => {})

// ...¡éste no lo hará!
setTimeout(() => {
  watchEffect(() => {})
}, 100)
</script>
```

Para detener manualmente un watcher, utiliza la función de devolución del control. Esto funciona tanto para `watch` como para `watchEffect`:

```js
const unwatch = watchEffect(() => {})

// ...más tarde, cuando ya no sea necesario
unwatch()
```

Ten en cuenta que debería haber muy pocos casos en los que necesites crear watchers de forma asíncrona, y se debería preferir la creación síncrona siempre que sea posible. Si necesitas esperar algún dato asíncrono, puedes hacer que tu lógica de observación sea condicional:

```js
// datos a ser cargados de manera asincrónica
const data = ref(null)

watchEffect(() => {
  if (data.value) {
    // hacer algo cuando se cargan los datos
  }
})
```

</div>
