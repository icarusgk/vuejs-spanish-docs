---
outline: deep
---

# Fundamentos de Reactividad {#reactivity-fundamentals}

:::tip Preferencias de API
Esta página y muchos otros capítulos posteriores en la guía tienen contenido diferente para la Options API y la Composition API. Tu preferencia actual es <span class="options-api">Options API</span><span class="composition-api">Composition API</span>. Puedes alternar entre los estilos de API usando el interruptor de "Preferencia de API" en la parte superior de la barra lateral izquierda.
:::

<div class="options-api">

## Declarando el Estado Reactivo {#declaring-reactive-state}

Con la Options API, usamos la opción `data` para declarar el estado reactivo de un componente. El valor de la opción debe ser una función que devuelva un objeto. Vue llamará a la función al crear una nueva instancia del componente y empaquetará el objeto devuelto en su sistema de reactividad. Cualquier propiedad de nivel superior de este objeto se representa en la instancia del componente (`this` en los métodos y hooks del ciclo de vida):

```js{2-6}
export default {
  data() {
    return {
      count: 1
    }
  },

  // `mounted` es un hook del ciclo de vida que explicaremos luego
  mounted() {
    // `this` se refiere a la instancia del componente.
    console.log(this.count) // => 1

    // los datos también pueden ser mutados
    this.count = 2
  }
}
```

[Pruébalo en la Zona de Práctica](https://play.vuejs.org/#eNpFUNFqhDAQ/JXBpzsoHu2j3B2U/oYPpnGtoetGkrW2iP/eRFsPApthd2Zndilex7H8mqioimu0wY16r4W+Rx8ULXVmYsVSC9AaNafz/gcC6RTkHwHWT6IVnne85rI+1ZLr5YJmyG1qG7gIA3Yd2R/LhN77T8y9sz1mwuyYkXazcQI2SiHz/7iP3VlQexeb5KKjEKEe2lPyMIxeSBROohqxVO4E6yV6ppL9xykTy83tOQvd7tnzoZtDwhrBO2GYNFloYWLyxrzPPOi44WWLWUt618txvASUhhRCKSHgbZt2scKy7HfCujGOqWL9BVfOgyI=)

Estas propiedades de la instancia solo se agregan cuando la instancia se crea por primera vez, por lo que debes asegurarte que estén todas presentes en el objeto devuelto por la función `data`. Cuando sea necesario, usa `null`, `undefined` o algún otro valor de marcador de posición para las propiedades donde el valor deseado aún no está disponible.

Es posible agregar una nueva propiedad directamente al `this` sin incluirla en `data`. Sin embargo, las propiedades agregadas de esta manera no podrán desencadenar actualizaciones reactivas.

Vue usa el prefijo `$` cuando expone sus propias API integradas a través de la instancia del componente. También reserva el prefijo `_` para propiedades internas. Debes evitar el uso de nombres para las propiedades de nivel superior de `data` que comiencen con cualquiera de estos caracteres.

### Proxy Reactivo vs. Original \* {#reactive-proxy-vs-original}

En Vue 3, los datos se vuelven reactivos al aprovechar los [Proxies de JavaScript](https://developer.mozilla.org/es/docs/Web/JavaScript/Reference/Global_Objects/Proxy). Los usuarios que vienen de Vue 2 deben tener en cuenta el siguiente caso extremo:

```js
export default {
  data() {
    return {
      someObject: {}
    }
  },
  mounted() {
    const newObject = {}
    this.someObject = newObject

    console.log(newObject === this.someObject) // false
  }
}
```

Cuando accedes a `this.someObject` después de asignarlo, el valor es un proxy reactivo del `newObject` original. **A diferencia de Vue 2, el `newObject` original se deja intacto y no se volverá reactivo: asegúrate de acceder siempre al estado reactivo como una propiedad de `this`.**

</div>

<div class="composition-api">

## Declarando Estado Reactivo \*\* {#declaring-reactive-state-1}

### `ref()` \*\* {#ref}

En la Composition API, la manera recomendada de declarar estado reactivo es usando la función [`ref()`](/api/reactivity-core#ref):

```js
import { ref } from 'vue'

const count = ref(0)
```

`ref()` toma el argumento y lo devuelve empaquetado en un objeto ref con la propiedad `.value`:

```js
const count = ref(0)

console.log(count) // { value : 0 }
console.log(count.value) // 0

count.value++
console.log(count.value) // 1
```

> Consulta también: [Escritura de Refs](/guide/typescript/composition-api#typing-ref) <sup class="vt-badge ts" />

Para usar refs en la plantilla de un componente, decláralos y devuélvelos desde la función `setup()` del componente:

```js{5,9-11}
import { ref } from 'vue'

export default {
  // `setup` es un hook especial dedicado para la Composition API.
  setup() {
    const count = ref(0})

    // expone la ref a la plantilla
    return {
      count
    }
  }
}
```

```vue-html
<div>{{ count }}</div>
```

Nota que **_no_** necesitamos agregar `.value` al usar la ref en la plantilla. Por conveniencia, las refs son desenvueltos automáticamente al ser usadas dentro de plantillas (con algunas [advertencias](#caveat-when-unwrapping-in-templates)).

También puedes mutar una ref directamente en manejadores de eventos:

```vue-html{1}
<button @click="count++">
  {{ count }}
</button>
```

Para lógica más compleja, podemos declarar funciones que mutan refs en el mismo ámbito y exponerlas como métodos junto con el estado:

```js{7-10,15}
import { ref } from 'vue'

export default {
  setup() {
    const count = ref(0)

    function increment() {
      // .value es necesario en JavaScript
      count.count++
    }

    // no te olvides de exponer la función también.
    return {
      count,
      increment
    }
  }
}
```

Los métodos expuestos pueden después ser usados como manejadores de eventos:

```vue-html{1}
<button @click="increment">
  {{ count }}
</button>
```

Acá hay un ejemplo en [CodePen](https://codepen.io/vuejs-examples/pen/WNYbaqo), sin usar herramientas de compilación.

### `<script setup>` \*\* {#script-setup}

Exponer manualmente el estado y los métodos a través de `setup()` puede ser verboso. Por suerte, se puede evitar cuando se utilizan [Componentes de un Solo Archivo (SFC)](/guide/scaling-up/sfc). Podemos simplificar el uso con `<script setup>`:

```vue{1}
<script setup>
import { ref } from 'vue'

const count = ref(0)

function increment() {
  count.value++
}
</script>

<template>
  <button @click="increment">
    {{ count }}
  </button>
</template>
```

[Pruébalo en la Zona de Práctica](https://play.vuejs.org/#eNo9jUEKgzAQRa8yZKMiaNcllvYe2dgwQqiZhDhxE3L3jrW4/DPvv1/UK8Zhz6juSm82uciwIef4MOR8DImhQMIFKiwpeGgEbQwZsoE2BhsyMUwH0d66475ksuwCgSOb0CNx20ExBCc77POase8NVUN6PBdlSwKjj+vMKAlAvzOzWJ52dfYzGXXpjPoBAKX856uopDGeFfnq8XKp+gWq4FAi)

Las importaciones de nivel superior, variables y funciones declaradas en el `<script setup>` se pueden usar automáticamente en la plantilla del mismo componente. Piensa la plantilla como una funcion de JavaScript declarada en el mismo ámbito, naturalmente tiene acceso a todo lo declarado en conjunto.

:::tip
Para el resto de la guía, utilizaremos principalmente la sintaxis SFC + `<script setup>` para los ejemplos de código de la Composition API, ya que ese es el uso más común para los desarrolladores de Vue.

Si no estas usando SFC, puedes seguir usando la Composition API con la opción [`setup()`](/api/composition-api-setup).
:::

### ¿Por qué Refs? \*\* {#why-refs}

Te puedes estar preguntando la razón de necesitar refs con el `.value` en vez de variables simples. Para explicar eso, necesitamos discutir brevemente cómo funciona el sistema de reactividad de Vue.

Cuando usas una ref en la plantilla, y después cambias el valor de la ref, Vue detecta el cambio automáticamente y actualiza el DOM como corresponda. Esto es posible con un sistema de reactividad basado en el seguimiento de dependencias. Cuando un componente es renderizado por primera vez, Vue **rastrea** cada ref que fue usada durante la renderización. Luego, cuando alguna ref mute, **activará** una nueva renderización de los componentes que la están rastreando.

En JavaScript estándar, no hay una manera de detectar el acceso o mutación de variables simples. Pero podemos interceptar las operaciones get y set de una propiedad.

La propiedad `.value` le da a Vue la oportunidad de detectar cuando una ref ha sido accedida o mutada. Bajo la superficie, Vue realiza el seguimiento en su getter, y activa en su setter. Conceptualmente, puedes pensar en una ref como un objeto que se ve así:

```js
// pseudocódigo, no la implementación real
const myRef = {
  _value: 0,
  get value() {
    track()
    return this._value
  },
  set value(newValue) {
    this._value = newValue
    trigger()
  }
}
```

Otra buena característica de las refs es que a diferencia de variables simples, puedes pasar las refs a funciones reteniendo el acceso a su último valor y la conexión de reactividad. Esto es particularmente útil al refactorizar lógica compleja en código reutilizable.

El sistema de reactividad es discutido en más detalle en la sección [Reactividad en Profundidad](/guide/extras/reactivity-in-depth).

</div>

<div class="options-api">

## Declarando Métodos \* {#declaring-methods}

<VueSchoolLink href="https://vueschool.io/lessons/methods-in-vue-3" title="Lección gratuita de Métodos de Vue.js"/>

Para agregar métodos a una instancia del componente, usamos la opción `methods`. Este debería ser un objeto que contenga los métodos deseados:

```js{7-11}
export default {
  data() {
    return {
      count: 0
    }
  },
  methods: {
    increment() {
      this.count++
    }
  },
  mounted() {
    // los métodos pueden ser llamados en hooks del ciclo de vida u otros métodos!
    this.increment()
  }
}
```

Vue vincula automáticamente el valor `this` para el `methods` para que siempre se refiera a la instancia del componente. Esto asegura que un método conserve el valor `this` correcto si se usa como escuchador de eventos o callback. Debes evitar el uso de funciones de flecha al definir `methods`, ya que eso evita que Vue vincule el valor `this` apropiado:

```js
export default {
  methods: {
    increment: () => {
      // MAL: ¡no hay acceso a `this` aquí!
    }
  }
}
```

Al igual que todas las demás propiedades de la instancia del componente, se puede acceder a los `methods` desde la plantilla del componente. Dentro de una plantilla, se usan más comúnmente como escuchadores de eventos:

```vue-html
<button @click="increment">{{ count }}</button>
```

[Pruébalo en la Zona de Práctica](https://play.vuejs.org/#eNplj9EKwyAMRX8l+LSx0e65uLL9hy+dZlTWqtg4BuK/z1baDgZicsPJgUR2d656B2QN45P02lErDH6c9QQKn10YCKIwAKqj7nAsPYBHCt6sCUDaYKiBS8lpLuk8/yNSb9XUrKg20uOIhnYXAPV6qhbF6fRvmOeodn6hfzwLKkx+vN5OyIFwdENHmBMAfwQia+AmBy1fV8E2gWBtjOUASInXBcxLvN4MLH0BCe1i4Q==)

En el ejemplo anterior, se llamará al método `increment` cuando se haga clic en `<button>`.

</div>

### Reactividad profunda {#deep-reactivity}

<div class="options-api">

En Vue, el estado es profundamente reactivo por defecto. Esto significa que puedes esperar que se detecten cambios incluso cuando mutas objetos o arrays anidados:

```js
export default {
  data() {
    return {
      obj: {
        nested: { count: 0 },
        arr: ['foo', 'bar']
      }
    }
  },
  methods: {
    mutateDeeply() {
      // estos funcionarán como se esperaba.
      this.obj.nested.count++
      this.obj.arr.push('baz')
    }
  }
}
```

</div>

<div class="composition-api">

Las refs pueden tener cualquier tipo de valor, incluyendo objetos profundamente anidados, arrays, o estructuras que son parte de JavaScript como `Map`.

Una ref hará que su valor sea profundamente reactivo. Esto significa que puedes esperar que se detecten cambios aún cuando mutas objetos o arrays anidados.

```js
import { ref } from 'vue'

const obj = ref({
  nested: { count: 0 },
  arr: ['foo', 'bar']
})

function mutateDeeply() {
  // estos funcionarán como se esperaba.
  obj.value.nested.count++
  obj.value.arr.push('baz')
}
```

Valores no primitivos son convertidos en proxies reactivos mediante [`reactive()`](#reactive), lo que es discutido más abajo.

También es posible no optar por reactividad profunda con [refs superficiales](/api/reactivity-advanced#shallowref). Para refs superficiales, sólo se registra el acceso a .value para la reactividad. Las refs superficiales pueden ser usadas para optimizar rendimiento al evitar los costos de observación de objetos grandes, o en casos donde el estado interno es manejado por una librería externa.

Véase también

- [Reducción de la Sobrecarga de Reactividad en Estructuras Inmutables de Gran Tamaño](/guide/best-practices/performance#reduce-reactivity-overhead-for-large-immutable-structures)
- [Integración con los Sistemas de Estado Externos](/guide/extras/reactivity-in-depth#integration-with-external-state-systems)

</div>

### Tiempo de Actualización del DOM {#dom-update-timing}

Cuando mutas el estado reactivo, el DOM se actualiza automáticamente. Sin embargo, hay que tener en cuenta que las actualizaciones del DOM no se aplican de forma sincrónica. En su lugar, Vue las almacena en búfer hasta la "siguiente marca" (next tick) del ciclo de actualización para garantizar que cada componente tenga que actualizarse sólo una vez, independientemente de cuántos cambios de estado hayas realizado.

Para esperar a que se complete la actualización del DOM después de un cambio de estado, puedes usar la API global [nextTick()](/api/general#nexttick):

<div class="composition-api">

```js
import { nextTick } from 'vue'

function increment() {
  count.value++
  nextTick(() => {
    // acceder al DOM actualizado
  })
}
```

</div>
<div class="options-api">

```js
import { nextTick } from 'vue'

export default {
  methods: {
    increment() {
      this.count++
      nextTick(() => {
        // acceder al DOM actualizado
      })
    }
  }
}
```

</div>

<div class="composition-api">

## `reactive()` \*\* {#reactive}

Existe otra forma de declarar estado reactivo, con la API `reactive()`. A diferencia de una ref, que envuelve el valor interno en un objeto especial, `reactive()` hace reactivo al objeto mismo:

```js
import { reactive } from 'vue'

const state = reactive({ count: 0 })
```

> Consulta también: [Escritura de `reactive()`](/guide/typescript/composition-api#typing-reactive) <sup class="vt-badge ts"/>

Uso en plantillas:

```vue-html
<button @click="state.count++">
  {{ state.count }}
</button>
```

Los objetos reactivos son [proxies de JavaScript](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Proxy) y se comportan como objetos normales. La diferencia es que Vue es capaz de interceptar el acceso y la mutación de todas las propiedades de un objeto reactivo para el seguimiento y activación de la reactividad.

`reactive()` convierte profundamente los objetos: objetos anidados también son envueltos con `reactive()`al ser accedidos. También es llamado por `ref()` internamente cuando el valor de la ref es un objeto. Similar a las refs superficiales, también está la API [`shallowReactive()`](/api/reactivity-advanced#shallowreactive) para no optar por la reactividad profunda.

### Proxy Reactivo vs. Original \*\* {#reactive-proxy-vs-original-1}

Es importante tener en cuenta que el valor devuelto por `reactive()` es un [Proxy](https://developer.mozilla.org/es/docs/Web/JavaScript/Reference/Global_Objects/Proxy) del objeto original, que no es igual al objeto original:

```js
const raw = {}
const proxy = reactive(raw)

// el proxy NO es igual al original.
console.log(proxy === raw) // false
```

Solo el proxy es reactivo: la mutación del objeto original no activará las actualizaciones. Por lo tanto, la mejor práctica cuando se trabaja con el sistema de reactividad de Vue es **utilizar exclusivamente las versiones proxy de tu estado**.

Para asegurar un acceso consistente al proxy, llamar a `reactive()` en el mismo objeto siempre devuelve el mismo proxy, y llamar a `reactive()` en un proxy existente también devuelve ese mismo proxy:

```js
// llamar a reactive () en el mismo objeto devuelve el mismo proxy
console.log(reactive(raw) === proxy) // true

// llamar a reactive() en un proxy devuelve ese mismo proxy
console.log(reactive(proxy) === proxy) // true
```

Esta regla también se aplica a los objetos anidados. Debido a la reactividad profunda, los objetos anidados dentro de un objeto reactivo también son proxies:

```js
const proxy = reactive({})

const raw = {}
proxy.nested = raw

console.log(proxy.nested === raw) // false
```

### Limitaciones de `reactive()` \*\* {#limitations-of-reactive}

La API `reactive()` tiene algunas limitaciones:

1. **Tipos de valores limitados:** esta solo funciona para tipos de objetos (objetos, arrays y [objetos globales](https://developer.mozilla.org/es/docs/Web/JavaScript/Reference/Global_Objects#keyed_collections) como `Map` y `Set`). No puede contener [tipos primitivos](https://developer.mozilla.org/es/docs/Glossary/Primitive) como `string`, `number` o `boolean`.

2. **No puede reemplazar un objeto entero:** dado que el seguimiento de la reactividad de Vue funciona sobre el acceso a la propiedad, siempre debemos mantener la misma referencia al objeto reactivo. Esto significa que no podemos " reemplazar" fácilmente un objeto reactivo porque se pierde la conexión de la reactividad con la primera referencia:

   ```js
   let state = reactive({ count: 0 })

   // la referencia de arriba ({ count: 0 }) ya no está siendo seguida
   // (se pierde la conexión de reactividad)
   state = reactive({ count: 1 })
   ```

3. **No es amigable con la desestructuración:** cuando desestructuramos la propiedad de un objeto reactivo en variables locales, o cuando pasamos esa propiedad a una función, perderemos la conexión de reactividad:

   ```js
   const state = reactive({ count: 0 })

   // count está desconectado de state.count cuando es desestructurado
   let { count } = state
   // no afecta el estado original
   count++

   // la función recibe un número simple y no será capaz de
   // realizar un seguimiento de los cambios en state.count
   // tenemos que pasar el objeto completo para retener reactividad
   callSomeFunction(state.count)
   ```

Debido a estas limitaciones, recomendamos usar `ref()`como la API principal para declarar estado reactivo.

## Detalles adicionales sobre desempaquetado de refs \*\* {#additional-ref-unwrapping-details}

### Como propiedad de un objeto reactivo \*\* {#ref-unwrapping-as-reactive-object-property}

Una ref es automaticamente desempaquetada cuando es accedida o mutada como propiedad de un objeto reactivo. En otras palabras, se comporta como una propiedad normal:

```js
const count = ref(0)
const state = reactive({
  count
})

console.log(state.count) // 0

state.count = 1
console.log(count.value) // 1
```

Si se asigna una nueva ref a una propiedad vinculada a una ref existente, ésta sustituirá a la antigua ref:

```js
const otherCount = ref(2)

state.count = otherCount
console.log(state.count) // 2
// la ref original ahora está desconectada de state.count
console.log(count.value) // 1
```

El desempaquetado de la ref sólo ocurre cuando se anida dentro de un objeto reactivo profundo. No se aplica cuando se accede como una propiedad de un [objeto reactivo superficial](/api/reactivity-advanced#shallowreactive).

### Advertencias en Arrays y Collecciones \*\* {#caveat-in-arrays-and-collections}

A diferencia de los objetos reactivos, **no** se realiza ningún desempaquetado cuando se accede a la ref como elemento de un array reactivo o de un tipo de colección nativa como `Map`

```js
const books = reactive([ref('Vue 3 Guide')])
// necesita .value aquí
console.log(books[0].value)

const map = reactive(new Map([['count', ref(0)]]))
// necesita .value aquí
console.log(map.get('count').value)
```

### Advertencia al Desempaquetar en Plantillas \*\* {#caveat-when-unwrapping-in-templates}

Desempaquetar a refs en plantillas solo aplica si la ref es una propiedad de nivel superior en el contexto del renderizado de la plantilla.

En el siguiente ejemplo, `count` y `object` son propiedades de nivel superior, pero `object.id` no lo es:

```js
const count = ref(0)
const object = { id: ref(0) }
```

Por lo tanto, esta expresión funciona como es esperado:

```vue-html
{{ count + 1 }}
```

...mientras que esto **NO**:

```vue-html
{{ object.id + 1}}
```

El resultado de la renderización será `[object Object]1` porque `object.id` no está desempaquetado al evaluar la expresión y sigue siendo un objeto ref. Para arreglar podemos desestructurar `id` para que sea una propiedad de nivel superior:

```js
const { id } = object
```

```vue-html
{{ id + 1 }}
```

Ahora el resultado del renderizado será `2`.

Otra cosa que hay que tener en cuenta es que una ref se desempaquetará si es el valor final evaluado de una interpolación de texto (es decir, una etiqueta <code v-pre>{{ }}</code>), por lo que lo siguiente renderizará `1`:

```vue-html
{{ object.id }}
```

Esto es una característica conveniente de interpolación de texto y es equivalente a <code v-pre>{{ object.id.value }}</code>.

</div>

<div class="options-api">

### Métodos con Estado \* {#stateful-methods}

En algunos casos, es posible que necesitemos crear dinámicamente un método, por ejemplo, creando un manejador de eventos desbordado:

```js
import { debounce } from 'lodash-es'

export default {
  methods: {
    // Rebote con Lodash
    click: debounce(function () {
      // ... responder al clic ...
    }, 500)
  }
}
```

Sin embargo, este enfoque es problemático para los componentes que se reutilizan porque una función desbordada **tiene estado**: mantiene algún estado interno sobre el tiempo transcurrido. Si varias instancias de un componente comparten la misma función de desbordamiento, interferirán entre sí.

Para que la función de cada instancia de componente sea independiente de las demás, podemos crear la versión desbordada en el hook del ciclo de vida `created`:

```js
export default {
  created() {
    // cada instancia tiene ahora su propia copia del manejador de desbordamiento
    this.debouncedClick = _.debounce(this.click, 500)
  },
  unmounted() {
    // también es una buena idea cancelar el temporizador
    // cuando el componente es removido
    this.debouncedClick.cancel()
  },
  methods: {
    click() {
      // ... responder al clic ...
    }
  }
}
```

</div>
