---
outline: deep
---

<script setup>
import SpreadSheet from './demos/SpreadSheet.vue'
</script>

# Reactividad en Profundidad {#reactivity-in-depth}

Una de las características más distintivas de Vue es el sistema de reactividad no intrusiva. El estado de los componentes consiste en objetos JavaScript reactivos. Cuando los modificas, la vista se actualiza. Esto hace que la gestión del estado sea sencilla e intuitiva, pero también es importante entender cómo funciona para evitar algunos problemas comunes. En esta sección, vamos a profundizar en algunos de los detalles de bajo nivel del sistema de reactividad de Vue.

## ¿Qué es la Reactividad? {#what-is-reactivity}

Este término aparece bastante en la programación hoy en día, pero ¿a qué se refiere la gente cuando lo dice? La reactividad es un paradigma de programación que nos permite ajustarnos a los cambios de forma declarativa. El ejemplo canónico que la gente suele mostrar, porque es muy bueno, es una hoja de cálculo de Excel:

<SpreadSheet />

Aquí la celda A2 está definida mediante una fórmula de `= A0 + A1` (se puede pulsar sobre A2 para ver o editar la fórmula), por lo que la hoja de cálculo nos da 3. No hay sorpresas. Pero si actualizas A0 o A1, notarás que A2 también se actualiza automáticamente.

JavaScript no suele funcionar así. Si escribiéramos algo comparable en JavaScript:

```js
let A0 = 1
let A1 = 2
let A2 = A0 + A1

console.log(A2) // 3

A0 = 2
console.log(A2) // Aún es 3
```

Cuando mutamos `A0`, `A2` no cambia automáticamente.

Entonces, ¿cómo podríamos hacer esto en JavaScript? En primer lugar, para volver a ejecutar el código que actualiza `A2`, vamos a envolverlo en una función:

```js
let A2

function update() {
  A2 = A0 + A1
}
```

Entonces, necesitamos definir algunos términos:

- La función `update()` produce un **efecto secundario**, o **efecto** para abreviar, porque modifica el estado del programa.

- Los valores `A0` y `A1` se consideran **dependencias** del efecto, ya que sus valores se utilizan para realizar el efecto. Se dice que el efecto es un **suscriptor** de sus dependencias.

Lo que necesitamos es una función mágica que pueda invocar `update()` (el **efecto**) cada vez que `A0` o `A1` (las **dependencias**) cambien:

```js
whenDepsChange(update)
```

Esta función `whenDepsChange()` tiene las siguientes tareas:

1. Rastrear cuando una variable es leída. Por ejemplo, cuando se evalúa la expresión `A0 + A1`, se leen tanto `A0` como `A1`.

2. Si una variable es leída cuando hay un efecto en ejecución, hacer que ese efecto sea un suscriptor de esa variable. Por ejemplo, como `A0` y `A1` se leen cuando se ejecuta `update()`, `update()` se convierte en suscriptor de `A0` y `A1` después de la primera llamada.

3. Detectar cuando una variable es mutada. Por ejemplo, cuando a `A0` se le asigna un nuevo valor, notifica a todos sus efectos suscriptores para que se vuelvan a ejecutar.

## Cómo Funciona la Reactividad en Vue {#how-reactivity-works-in-vue}

Realmente no podemos hacer un seguimiento de la lectura y escritura de variables locales como en el ejemplo. No hay ningún mecanismo para hacerlo en JavaScript. Lo que sí **podemos** hacer es interceptar la lectura y escritura de las **propiedades de los objetos**.

Hay dos maneras de interceptar el acceso a las propiedades en JavaScript: [getter](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Functions/get) / [setters](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Functions/set) y [Proxies](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Proxy). Vue 2 utilizaba exclusivamente getter / setters debido a las limitaciones de soporte del navegador. En Vue 3, se utilizan Proxies para los objetos reactive y getter / setters para los refs. Aquí hay un pseudocódigo que ilustra cómo funcionan:

```js{4,9,17,22}
function reactive(obj) {
  return new Proxy(obj, {
    get(target, key) {
      track(target, key)
      return target[key]
    },
    set(target, key, value) {
      target[key] = value
      trigger(target, key)
    }
  })
}

function ref(value) {
  const refObject = {
    get value() {
      track(refObject, 'value')
      return value
    },
    set value(newValue) {
      value = newValue
      trigger(refObject, 'value')
    }
  }
  return refObject
}
```

:::tip
Los snippets de código aquí y más abajo pretenden explicar los conceptos básicos de la forma más sencilla posible, por lo que se omiten muchos detalles y se ignoran los casos extremos.
:::

Esto explica algunas [limitaciones de los objetos reactivos](/guide/essentials/reactivity-fundamentals.html#limitaciones-de-reactive) que hemos discutido en la sección de fundamentos:

- Cuando asignas o desestructuras una propiedad de un objeto reactivo a una variable local, la reactividad se "desconecta" porque el acceso a la variable local ya no acciona las capturas del proxy get / set.

- El proxy devuelto por `reactive()`, aunque se comporta igual que el original, tiene una identidad diferente si lo comparamos con el original usando el operador `===`.

Dentro de `track()`, comprobamos si hay un efecto en ejecución. Si lo hay, buscamos los efectos suscriptores (almacenados en un conjunto) para la propiedad que se está siguiendo, y añadimos el efecto al conjunto:

```js
// Esto se establecerá justo antes de que un efecto esté a punto
// de ser ejecutado. Nos ocuparemos de esto más adelante.
let activeEffect

function track(target, key) {
  if (activeEffect) {
    const effects = getSubscribersForProperty(target, key)
    effects.add(activeEffect)
  }
}
```

Las suscripciones de efectos se almacenan en una estructura de datos global `WeakMap<target, Map<key, Set<effect>>>`. Si no se ha encontrado ningún conjunto de efectos suscritos para una propiedad (rastreada por primera vez), se creará. Esto es lo que hace la función `getSubscribersForProperty()`, en resumen. Para simplificar, omitiremos sus detalles.

Dentro de `trigger()`, volvemos a buscar los efectos del suscriptor para la propiedad. Pero esta vez los invocamos:

```js
function trigger(target, key) {
  const effects = getSubscribersForProperty(target, key)
  effects.forEach((effect) => effect())
}
```

Volvamos ahora a la función `whenDepsChange()`:

```js
function whenDepsChange(update) {
  const effect = () => {
    activeEffect = effect
    update()
    activeEffect = null
  }
  effect()
}
```

Esto encierra la función cruda `update` en un efecto que se establece como el efecto activo actual antes de ejecutar la actualización real. Esto permite las llamadas a `track()` durante la actualización para localizar el efecto activo actual.

En este punto, hemos creado un efecto que rastrea automáticamente sus dependencias, y se vuelve a ejecutar cada vez que una dependencia cambia. A esto lo llamamos un **Efecto Reactivo**.

Vue proporciona una API que permite crear efectos reactivos: [`watchEffect()`](/api/reactivity-core.html#watcheffect). De hecho, te habrás dado cuenta de que funciona de forma bastante similar al mágico `whenDepsChange()` del ejemplo. Ahora podemos rehacer el ejemplo original utilizando las APIs reales de Vue:

```js
import { ref, watchEffect } from 'vue'

const A0 = ref(0)
const A1 = ref(1)
const A2 = ref()

watchEffect(() => {
  // rastrea A0 y A1
  A2.value = A0.value + A1.value
})

// dispara el efecto
A0.value = 2
```

Usar un efecto reactivo para mutar una ref no es el caso de uso más interesante; de hecho, usar una propiedad computada lo hace más declarativo:

```js
import { ref, computed } from 'vue'

const A0 = ref(0)
const A1 = ref(1)
const A2 = computed(() => A0.value + A1.value)

A0.value = 2
```

Internamente, `computed` gestiona su invalidación y recálculo utilizando un efecto reactivo.

Así que, ¿cuál es un ejemplo de un efecto reactivo común y útil? Bueno, ¡actualizar el DOM! Podemos implementar una simple "renderización reactiva" como esta:

```js
import { ref, watchEffect } from 'vue'

const count = ref(0)

watchEffect(() => {
  document.body.innerHTML = `La cuenta es: ${count.value}`
})

// actualiza el DOM
count.value++
```

De hecho, esto es bastante parecido a cómo un componente de Vue mantiene el estado y el DOM en sincronía; cada instancia del componente crea un efecto reactivo para renderizar y actualizar el DOM. Por supuesto, los componentes de Vue utilizan formas mucho más eficientes de actualizar el DOM que `innerHTML`. Esto se trata en [Mecanismo de Renderizado](./rendering-mechanism).

<div class="options-api">

Las APIs `ref()`, `computed()` y `watchEffect()` forman parte de la API de composición. Si hasta ahora sólo has utilizado la Options API con Vue, te darás cuenta de que la Composition API está más cerca de cómo funciona el sistema de reactividad de Vue. De hecho, en Vue 3 la Options API está implementada sobre la Composition API. Todo el acceso a las propiedades en la instancia del componente (`this`) activa getter / setters para el seguimiento de la reactividad, y las opciones como `watch` y `computed` invocan sus equivalentes de la Composition API internamente.

</div>

## Reactividad en Tiempo de Ejecución vs. Tiempo de Compilación {#runtime-vs-compile-time-reactivity}

El sistema de reactividad de Vue se basa principalmente en el tiempo de ejecución: el seguimiento y la activación se realizan mientras el código se ejecuta directamente en el navegador. Las ventajas de la reactividad en tiempo de ejecución es que puede funcionar sin un paso de compilación, y hay menos casos límite. Por otro lado, esto hace que esté restringida por las limitaciones de sintaxis de JavaScript, lo que lleva a la necesidad el uso de contenedores de valores como Vue refs.

Algunos frameworks, como [Svelte](https://svelte.dev/), optan por superar estas limitaciones implementando la reactividad durante la compilación. Este analiza y transforma el código para simular la reactividad. El paso de compilación permite a los frameworks alterar la semántica del propio JavaScript, por ejemplo, inyectando implícitamente código que realiza análisis de dependencias y activación de efectos en torno al acceso a variables definidas localmente. El inconveniente es que tales transformaciones requieren un paso de compilación, y al alterar la semántica de JavaScript es esencialmente crear un lenguaje que parece JavaScript pero que se compila en otra cosa.

El equipo de Vue exploró esta dirección a través de una característica experimental llamada [Reactivity Transform](/guide/extras/reactivity-transform), pero al final hemos decidido que no sería un buen ajuste para el proyecto debido al [razonamiento explicado aquí](https://github.com/vuejs/rfcs/discussions/369#discussioncomment-5059028).

## Depuración de la Reactividad {#reactivity-debugging}

Es estupendo que el sistema de reactividad de Vue rastree automáticamente las dependencias, pero en algunos casos podemos querer averiguar exactamente qué se está rastreando, o qué está causando que un componente se vuelva a renderizar.

### Hooks de Depuración de Componentes {#component-debugging-hooks}

Podemos depurar qué dependencias se utilizan durante el renderizado de un componente y cuáles son las que desencadenan una actualización utilizando las funciones <span class="options-api">`renderTracked`</span><span class="composition- api">`onRenderTracked`</span> y <span class="options-api">`renderTriggered`</span><span class="composition-api">`onRenderTriggered`</span> del hooks del ciclo de vida. Ambos hooks recibirán un evento de depuración que contiene información sobre la dependencia en cuestión. Se recomienda colocar una sentencia `debugger` en los callbacks para inspeccionar interactivamente la dependencia:

<div class="composition-api">

```vue
<script setup>
import { onRenderTracked, onRenderTriggered } from 'vue'

onRenderTracked((event) => {
  debugger
})

onRenderTriggered((event) => {
  debugger
})
</script>
```

</div>
<div class="options-api">

```js
export default {
  renderTracked(event) {
    debugger
  },
  renderTriggered(event) {
    debugger
  }
}
```

</div>

:::tip
Los hooks de depuración de componentes sólo funcionan en modo de desarrollo.
:::

Los objetos de eventos de depuración tienen el siguiente tipo:

<span id="debugger-event"></span>

```ts
type DebuggerEvent = {
  effect: ReactiveEffect
  target: object
  type:
    | TrackOpTypes /* 'get' | 'has' | 'iterate' */
    | TriggerOpTypes /* 'set' | 'add' | 'delete' | 'clear' */
  key: any
  newValue?: any
  oldValue?: any
  oldTarget?: Map<any, any> | Set<any>
}
```

### Depuración Computada {#computed-debugging}

<!-- TODO equivalente de la Options API -->

Podemos depurar las propiedades computadas pasando a `computed()` un segundo objeto de opciones con los callbacks `onTrack` y `onTrigger`:

- `onTrack` será llamado cuando una propiedad reactive o ref sea rastreada como una dependencia.
- `onTrigger` se llamará cuando la llamada de retorno del watcher sea activada por la mutación de una dependencia.

Ambos callbacks recibirán eventos del depurador en el [mismo formato](#component-debugging-hooks) que los hooks de depuración del componente:

```js
const plusOne = computed(() => count.value + 1, {
  onTrack(e) {
    // activado cuando count.value es rastreado como una dependencia
    debugger
  },
  onTrigger(e) {
    // activado cuando count.value es mutado
    debugger
  }
})

// acceder a plusOne, debería activar a onTrack
console.log(plusOne.value)

// mutar count.value, debería activar a onTrigger
count.value++
```

:::tip
Las opciones computadas `onTrack` y `onTrigger` sólo funcionan en modo de desarrollo.
:::

### Depuración del Watcher {#watcher-debugging}

<!-- TODO equivalente de la Options API -->

Al igual que `computed()`, los watchers también soportan las opciones `onTrack` y `onTrigger`:

```js
watch(source, callback, {
  onTrack(e) {
    debugger
  },
  onTrigger(e) {
    debugger
  }
})

watchEffect(callback, {
  onTrack(e) {
    debugger
  },
  onTrigger(e) {
    debugger
  }
})
```

:::tip
Las opciones de seguimiento `onTrack` y `onTrigger` sólo funcionan en modo de desarrollo.
:::

## Integración con los Sistemas de Estado Externos {#integration-with-external-state-systems}

El sistema de reactividad de Vue funciona convirtiendo profundamente los objetos JavaScript planos en proxies reactivos. La conversión profunda puede ser innecesaria o a veces no deseada cuando se integra con sistemas de gestión de estado externos (por ejemplo, si una solución externa también utiliza Proxies).

La idea general de integrar el sistema de reactividad de Vue con una solución externa de gestión de estado es mantener el estado externo en un [`shallowRef`](/api/reactivity-advanced.html#shallowref). Una ref superficial sólo es reactiva cuando se accede a su propiedad `.value`; el valor interno se deja intacto. Cuando el estado externo cambia, reemplaza el valor de la ref para activar las actualizaciones.

### Datos Inmutables {#immutable-data}

Si estás implementando una función de deshacer / rehacer, es probable que quieras tomar una instantánea del estado de la aplicación en cada edición del usuario. Sin embargo, el sistema de reactividad mutable de Vue no es el más adecuado para esto si el árbol de estado es grande, porque serializar todo el objeto del estado en cada actualización puede ser costoso en términos de costes de CPU y memoria.

Las [estructuras de datos inmutables](https://en.wikipedia.org/wiki/Persistent_data_structure) solucionan esto al no mutar nunca los objetos de estado; en su lugar, crean nuevos objetos que comparten las mismas partes inalteradas con los antiguos. Hay diferentes formas de utilizar datos inmutables en JavaScript, pero recomendamos utilizar [Immer](https://immerjs.github.io/immer/) con Vue porque permite el uso de datos inmutables manteniendo la sintaxis mutable más ergonómica.

Podemos integrar Immer con Vue a través de un simple composable:

```js
import produce from 'immer'
import { shallowRef } from 'vue'

export function useImmer(baseState) {
  const state = shallowRef(baseState)
  const update = (updater) => {
    state.value = produce(state.value, updater)
  }

  return [state, update]
}
```

[Pruébalo en la Zona de Práctica](https://play.vuejs.org/#eNplU8Fu2zAM/RXOlzpAYu82zEu67lhgpw3bJcrBs5VYqywJkpxmMPzvoyjZNRodbJF84iOppzH7ZkxxHXhWZXvXWGE8OO4H88iU6I22HkYYHH/ue25hgrPVPTwUpQh28dc9MAXAVKOV83AUnvduC4Npa8+fg3GCw3I8PwbwGD64vPCSV8Cy77y2Cn4PnGXbFGu1wpC36EPHRO67c78cD6fgVfgOiOB9gnMtXczA1GnDFFPnQTVeaAVeXy6SSsyFavltE/OvKs+pGTg8zsxkHwl9KgIBtvbhzkl0yIWU+zIOFEeJBgKNxORoAewHSX/cSQHX3VnbA8vyMXa3pfqxb0i1CRXZWZb6w1U1snYOT40JvQ4+NVI0Lxi865NliTisMRHChOVSNaUUscCSKtyXq7LRdP6fDNvYPw3G85vftbzRtg6TrUAKxXe+s3q4dF/mQdC5bJtFTe362qB4tELVURKWAthhNc87+OhSw2V33htXleWgzMulaHQfFfj0ufhYfCpb4XySJHc9Zv7a63aQqKh0+xNRR8kiZ1K2sYhqeBI1xVHPi+xdV0upX3/w8yJ8fCiIYIrfCLPIaZH4n9rxnx7nlQQVH4YLHpTLW8YV8A0W1Ye4PO7sZiU/ylFca4mSP8yl5yvv/O4sZcSmw8/iW8bXdSTcjDiFgUz/AcH6WZQ=)

### Máquinas de Estado {#state-machines}

Las [Máquinas de Estado](https://en.wikipedia.org/wiki/Finite-state_machine) son un modelo para describir todos los estados posibles en los que puede estar una aplicación y todas las formas posibles de transición de un estado a otro. Aunque puede ser exagerado para componentes simples, puede ayudar a que los flujos de estado complejos sean más robustos y manejables.

Una de las implementaciones de máquinas de estado más populares en JavaScript es [XState](https://xstate.js.org/). Aquí hay un composable que se integra con ella:

```js
import { createMachine, interpret } from 'xstate'
import { shallowRef } from 'vue'

export function useMachine(options) {
  const machine = createMachine(options)
  const state = shallowRef(machine.initialState)
  const service = interpret(machine)
    .onTransition((newState) => (state.value = newState))
    .start()
  const send = (event) => service.send(event)

  return [state, send]
}
```

[Pruébalo en la Zona de Práctica](https://play.vuejs.org/#eNp1U81unDAQfpWRL7DSFqqqUiXEJumhyqVVpDa3ugcKZtcJjC1syEqId8/YBu/uIRcEM9/P/DGz71pn0yhYwUpTD1JbMMKO+o6j7LUaLMwwGvGrqk8SBSzQDqqHJMv7EMleTMIRgGOt0Fj4a2xlxZ5EsPkHhytuOjucbApIrDoeO5HsfQCllVVHUYlVbeW0xr2OKcCzHCwkKQAK3fP56fHx5w/irSyqbfFMgA+h0cKBHZYey45jmYfeqWv6sKLXHbnTF0D5f7RWITzUnaxfD5y5ztIkSCY7zjwKYJ5DyVlf2fokTMrZ5sbZDu6Bs6e25QwK94b0svgKyjwYkEyZR2e2Z2H8n/pK04wV0oL8KEjWJwxncTicnb23C3F2slabIs9H1K/HrFZ9HrIPX7Mv37LPuTC5xEacSfa+V83YEW+bBfleFkuW8QbqQZDEuso9rcOKQQ/CxosIHnQLkWJOVdept9+ijSA6NEJwFGePaUekAdFwr65EaRcxu9BbOKq1JDqnmzIi9oL0RRDu4p1u/ayH9schrhlimGTtOLGnjeJRAJnC56FCQ3SFaYriLWjA4Q7SsPOp6kYnEXMbldKDTW/ssCFgKiaB1kusBWT+rkLYjQiAKhkHvP2j3IqWd5iMQ+M=)

### RxJS {#rxjs}

[RxJS](https://rxjs.dev/) es una biblioteca para trabajar con flujos de eventos asíncronos. La librería [VueUse](https://vueuse.org/) proporciona el complemento [`@vueuse/rxjs`](https://vueuse.org/rxjs/readme.html) para conectar los flujos RxJS con el sistema de reactividad de Vue.

## Conexión con Signals {#connection-to-signals}

Muchos otros frameworks han introducido tipos primitivos de reactividad similares a los refs de la Composition API de Vue, bajo el término "signals":

- [Solid Signals](https://www.solidjs.com/docs/latest/api#createsignal)
- [Angular Signals](https://github.com/angular/angular/discussions/49090)
- [Preact Signals](https://preactjs.com/guide/v10/signals/)
- [Qwik Signals](https://qwik.builder.io/docs/components/state/#usesignal)

Fundamentalmente, las signals son el mismo tipo de primitivo de reactividad que las refs de Vue. Es un contenedor de valores que proporciona seguimiento de dependencias en el acceso y activación de efectos secundarios en la mutación. Este paradigma basado en primitivos de reactividad no es un concepto particularmente nuevo en el mundo del frontend: se remonta a implementaciones como [Knockout observables](https://knockoutjs.com/documentation/observables.html) y [Meteor Tracker](https://docs.meteor.com/api/tracker.html) de hace más de una década. La Options API de Vue y la librería de gestión de estados de React [MobX](https://mobx.js.org/) también se basan en los mismos principios, pero ocultan las primitivas tras las propiedades de los objetos.

Aunque no es un rasgo necesario para que algo pueda calificarse como signal, hoy en día el concepto se discute a menudo junto con el modelo de renderizado en el que las actualizaciones se realizan a través de suscripciones de grano fino. Debido al uso de Virtual DOM, Vue actualmente [depende de compiladores para lograr optimizaciones similares](https://vuejs.org/guide/extras/rendering-mechanism.html#compiler-informed-virtual-dom). Sin embargo, también estamos explorando una nueva estrategia de compilación inspirada en Solid (Vapor Mode) que no depende de Virtual DOM y aprovecha más el sistema de reactividad integrado de Vue.

### Aspectos del diseño de API {#api-design-trade-offs}

El diseño de las signals de Preact y Qwik son muy similares al [shallowRef](/api/reactivity-advanced.html#shallowref) de Vue: las tres proporcionan una interfaz mutable a través de la propiedad `.value`. Centraremos la discusión en las signals de Solid y Angular.

#### Signals de Solid {#solid-signals}

El diseño de la API `createSignal()` de Solid enfatiza la segregación de lectura/escritura. Las signals se exponen como un getter de sólo lectura y un setter separado:

```js
const [count, setCount] = createSignal(0)

count() // Acceder al valor
setCount(1) // Actualizar el valor
```

Observa cómo la signal `count` puede ser pasada sin el setter. Esto asegura que el estado nunca puede ser mutado a menos que el setter también sea explícitamente expuesto. Si esta garantía de seguridad justifica la sintaxis más verbosa podría estar sujeto a los requisitos del proyecto y el gusto personal - pero en caso de que prefiera este estilo de API, se puede replicar fácilmente en Vue:

```js
import { shallowRef, triggerRef } from 'vue'

export function createSignal(value, options) {
  const r = shallowRef(value)
  const get = () => r.value
  const set = (v) => {
    r.value = typeof v === 'function' ? v(r.value) : v
    if (options?.equals === false) triggerRef(r)
  }
  return [get, set]
}
```

[Pruébalo en la Zona de Práctica](https://play.vuejs.org/#eNpdUk1TgzAQ/Ss7uQAjgr12oNXxH+ix9IAYaDQkMV/qMPx3N6G0Uy9Msu/tvn2PTORJqcI7SrakMp1myoKh1qldI9iopLYwQadpa+krG0TLYYZeyxGSojSSs/d7E8vFh0ka0YhOCmPh0EknbB4mPYfTEeqbIelD1oiqXPRQCS+WjoojAW8A1Wmzm1A39KYZzHNVYiUib85aKeCx46z7rBuySqQe6h14uINN1pDIBWACVUcqbGwtl17EqvIiR3LyzwcmcXFuTi3n8vuF9jlYzYaBajxfMsDcomv6E/m9E51luN2NV99yR3OQKkAmgykss+SkMZerxMLEZFZ4oBYJGAA600VEryAaD6CPaJwJKwnr9ldR2WMedV1Dsi6WwB58emZlsAV/zqmH9LzfvqBfruUmNvZ4QN7VearjenP4aHwmWsABt4x/+tiImcx/z27Jqw==)

#### Signals de Angular {#angular-signals}

Angular está experimentando algunos cambios fundamentales al renunciar a la comprobación sucia e introducir su propia implementación de un primitivo de reactividad. La API de las signals de Angular tiene este aspecto:

```js
const count = signal(0)

count() // access the value
count.set(1) // set new value
count.update((v) => v + 1) // update based on previous value

// mutate deep objects with same identity
const state = signal({ count: 0 })
state.mutate((o) => {
  o.count++
})
```

De nuevo, podemos replicar fácilmente la API en Vue:

```js
import { shallowRef, triggerRef } from 'vue'

export function signal(initialValue) {
  const r = shallowRef(initialValue)
  const s = () => r.value
  s.set = (value) => {
    r.value = value
  }
  s.update = (updater) => {
    r.value = updater(r.value)
  }
  s.mutate = (mutator) => {
    mutator(r.value)
    triggerRef(r)
  }
  return s
}
```

[Pruébalo en la Zona de Práctica](https://play.vuejs.org/#eNp9UslOwzAQ/ZVRLiRQEsqxpBUIvoADp0goTd3U4DiWl4AU5d8ZL3E3iZtn5r1Z3vOYvAiRD4Ykq6RUjaRCgyLaiE3FaSd6qWEERVteswU0fSeMJjuYYC/7Dm7youatYbW895D8S91UvOJNz5VGuOEa1oGePmRzYdebLSNYmRumaQbrjSfg8xYeEVsWfh/cBANNOsFqTTACKA/LzavrTtUKxjEyp6kssDZj3vygAPJjL1Bbo3XP4blhtPleV4nrlBuxw1npYLca4A6WWZU4PADljSQd4drRC8//rxfKaW+f+ZJg4oJbFvG8ZJFcaYreHL041Iz1P+9kvwAtadsS6d7Rm1rB55VRaLAzhvy6NnvDG01x1WAN5VTTmn3UzJAMRrudd0pa++LEc9wRpRDlHZT5YGu2pOzhWHAJWxvnakxOHufFxqx/4MxzcEinIYP+eV5ntOe5Rx94IYjopxOZUhnIEr+4xPMrjuG1LPFftkTj5DNJGhwYBZ4BJz3DV56FmJLpD1DrKXU=)

En comparación con las refs de Vue, el estilo de API basado en getters de Solid y Angular proporciona algunas compensaciones interesantes cuando se utiliza en componentes Vue:

- `()` es ligeramente menos verboso que `.value`, pero actualizar el valor es más verboso.
- No hay desenvolvimiento de refs: el acceso a los valores siempre requiere `()`. Esto hace que el acceso a valores sea consistente en todas partes. Esto también significa que puedes pasar las signals como componentes.
Si estos estilos de API te convienen o no es hasta cierto punto subjetivo. Nuestro objetivo aquí es demostrar la similitud fundamental y las ventajas y desventajas entre estos diferentes diseños de API. También queremos mostrar que Vue es flexible: no estás realmente encerrado en las APIs existentes. Si es necesario, puedes crear tu propia API primitiva de reactividad para satisfacer necesidades más específicas.
