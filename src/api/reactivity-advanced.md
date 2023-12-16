# Reactividad: Avanzado {#reactivity-api-advanced}

## shallowRef() {#shallowref}

Versión poco profunda de [`ref()`](./reactivity-core#ref).

- **Tipo**

  ```ts
  function shallowRef<T>(value: T): ShallowRef<T>

  interface ShallowRef<T> {
    value: T
  }
  ```

- **Detalles**

  A diferencia de `ref()`, el valor interno de una ref poco profunda se almacena y se expone tal cual, y no se volverá profundamente reactivo. Solo el acceso a `.value` es reactivo.

  `shallowRef()` se usa normalmente para optimizar el rendimiento de grandes estructuras de datos, o para la integración con sistemas externos de gestión de estados.

- **Ejemplo**

  ```js
  const state = shallowRef({ count: 1 })

  // NO activa el cambio
  state.value.count = 2

  // activa el cambio
  state.value = { count: 2 }
  ```

- **Véase también**
  - [Guía - Reducción de la Sobrecarga de Reactividad en Estructuras Inmutables de Gran Tamaño](/guide/best-practices/performance#reduce-reactivity-overhead-for-large-immutable-structures)
  - [Guía - Integración con los Sistemas de Estado Externos](/guide/extras/reactivity-in-depth#integration-with-external-state-systems)

## triggerRef() {#triggerref}

Forzar la activación de los efectos que depende de una [ref poco profunda](#shallowref). Esto se usa típicamente después de hacer mutaciones profundas en el valor interno de una ref poco profunda.

- **Tipo**

  ```ts
  function triggerRef(ref: ShallowRef): void
  ```

- **Ejemplo**

  ```js
  const shallow = shallowRef({
    greet: 'Hola, mundo'
  })

  // Muestra "Hola, mundo" una vez para la primera ejecución
  watchEffect(() => {
    console.log(shallow.value.greet)
  })

  // Esto no activará el efecto porque la ref es poco profunda
  shallow.value.greet = 'Hola, universo'

  // Muestra "Hola, universo"
  triggerRef(shallow)
  ```

## customRef() {#customref}

Crea una ref personalizada con control explícito sobre el seguimiento de sus dependencias y la activación de actualizaciones.

- **Tipo**

  ```ts
  function customRef<T>(factory: CustomRefFactory<T>): Ref<T>

  type CustomRefFactory<T> = (
    track: () => void,
    trigger: () => void
  ) => {
    get: () => T
    set: (value: T) => void
  }
  ```

- **Detalles**

  `customRef()` espera una función de fábrica, que recibe las funciones `track` y `trigger` como argumentos y debe devolver un objeto con los métodos `get` y `set`.

  En general, `track()` debe llamarse dentro de `get()`, y `trigger()` debe llamarse dentro de `set()`. Sin embargo, tú tienes el control total sobre cuándo deben llamarse, o si deben llamarse en definitiva.

- **Ejemplo**

  Creando una ref que solo actualiza el valor después de un determinado tiempo de espera tras la última llamada establecida:

  ```js
  import { customRef } from 'vue'

  export function useDebouncedRef(value, delay = 200) {
    let timeout
    return customRef((track, trigger) => {
      return {
        get() {
          track()
          return value
        },
        set(newValue) {
          clearTimeout(timeout)
          timeout = setTimeout(() => {
            value = newValue
            trigger()
          }, delay)
        }
      }
    })
  }
  ```

  Uso en el componente:

  ```vue
  <script setup>
  import { useDebouncedRef } from './debouncedRef'
  const text = useDebouncedRef('hola')
  </script>

  <template>
    <input v-model="text" />
  </template>
  ```

  [Pruébalo en la Zona de Práctica](https://play.vuejs.org/#eNplUkFugzAQ/MqKC1SiIekxIpEq9QVV1BMXCguhBdsyaxqE/PcuGAhNfYGd3Z0ZDwzeq1K7zqB39OI205UiaJGMOieiapTUBAOYFt/wUxqRYf6OBVgotGzA30X5Bt59tX4iMilaAsIbwelxMfCvWNfSD+Gw3++fEhFHTpLFuCBsVJ0ScgUQjw6Az+VatY5PiroHo3IeaeHANlkrh7Qg1NBL43cILUmlMAfqVSXK40QUOSYmHAZHZO0KVkIZgu65kTnWp8Qb+4kHEXfjaDXkhd7DTTmuNZ7MsGyzDYbz5CgSgbdppOBFqqT4l0eX1gZDYOm057heOBQYRl81coZVg9LQWGr+IlrchYKAdJp9h0C6KkvUT3A6u8V1dq4ASqRgZnVnWg04/QWYNyYzC2rD5Y3/hkDgz8fY/cOT1ZjqizMZzGY3rDPC12KGZYyd3J26M8ny1KKx7c3X25q1c1wrZN3L9LCMWs/+AmeG6xI=)

## shallowReactive() {#shallowreactive}

Versión poco profunda de [`reactive()`](./reactivity-core#reactive).

- **Tipo**

  ```ts
  function shallowReactive<T extends object>(target: T): T
  ```

- **Detalles**

  A diferencia de `reactive()`, no hay una conversión profunda: solo las propiedades del nivel raíz son reactivas para un objeto reactivo poco profundo. Los valores de propiedad se almacenan y exponen tal cual; esto también significa que las propiedades con valores ref **no** se desenvolverán automáticamente.

  :::warning Úsalo con precaución
  Las estructuras de datos poco profundas solo deben usarse para el estado del nivel raíz en un componente. Evita anidarlos dentro de un objeto reactivo profundo, ya que crea un árbol con un comportamiento de reactividad inconsistente que puede ser difícil de entender y depurar.
  :::

- **Ejemplo**

  ```js
  const state = shallowReactive({
    foo: 1,
    nested: {
      bar: 2
    }
  })

  // La mutación de las propiedades del estado es reactiva
  state.foo++

  // ...pero no convierte los objetos anidados
  isReactive(state.nested) // false

  // NO reactivo
  state.nested.bar++
  ```

## shallowReadonly() {#shallowreadonly}

Versión poco profunda de [`readonly()`](./reactivity-core#readonly).

- **Tipo**

  ```ts
  function shallowReadonly<T extends object>(target: T): Readonly<T>
  ```

- **Detalles**

  A diferencia de `readonly()`, no hay una conversión profunda: solo las propiedades del nivel raíz se hacen de solo lectura. Los valores de propiedad se almacenan y exponen tal cual; esto también significa que las propiedades con valores de ref **no** se desenvolverán automáticamente.

  :::warning Úsalo con precaución
  Las estructuras de datos poco profundas solo deben usarse para el estado del nivel raíz en un componente. Evita anidarlos dentro de un objeto reactivo profundo, ya que crea un árbol con un comportamiento de reactividad inconsistente que puede ser difícil de entender y depurar.
  :::

- **Ejemplo**

  ```js
  const state = shallowReadonly({
    foo: 1,
    nested: {
      bar: 2
    }
  })

  // La mutación de las propiedades propias del estado fallará
  state.foo++

  // ...pero funciona con objetos anidados
  isReadonly(state.nested) // false

  // funciona
  state.nested.bar++
  ```

## toRaw() {#toraw}

Devuelve el objeto original de un proxy creado por Vue.

- **Tipo**

  ```ts
  function toRaw<T>(proxy: T): T
  ```

- **Detalles**

  `toRaw()` puede devolver el objeto original de los proxies creados por [`reactive()`](./reactivity-core#reactive), [`readonly()`](./reactivity-core#readonly), [`shallowReactive()`](#shallowreactive) o [`shallowReadonly()`](#shallowreadonly).

  Esta es una vía de escape que puede utilizarse para leer temporalmente sin incurrir en la sobrecarga de acceso/seguimiento del proxy, o escribir sin desencadenar cambios. **No** se recomienda mantener una referencia persistente al objeto original. Utilízalo con precaución.

- **Ejemplo**

  ```js
  const foo = {}
  const reactiveFoo = reactive(foo)

  console.log(toRaw(reactiveFoo) === foo) // true
  ```

## markRaw() {#markraw}

Marca un objeto para que nunca se convierta en un proxy. Devuelve el objeto mismo.

- **Tipo**

  ```ts
  function markRaw<T extends object>(value: T): T
  ```

- **Ejemplo**

  ```js
  const foo = markRaw({})
  console.log(isReactive(reactive(foo))) // false

  // también funciona cuando se anida dentro de otros objetos reactivos
  const bar = reactive({ foo })
  console.log(isReactive(bar.foo)) // false
  ```

  :::warning Úsalo con precaución
  `markRaw()` y las API poco profundas como `shallowReactive()` te permiten optar por la conversión profunda reactiva/solo lectura por defecto e incrustar objetos sin procesar, sin proxy, en tu gráfico de estado. Se pueden utilizar por varias razones:

  - Algunos valores simplemente no deben volverse reactivos, por ejemplo, una instancia de clase compleja de terceros, o un objeto de componente de Vue.

  - Omitir la conversión de proxy puede proporcionar mejoras de rendimiento al representar listas grandes con fuentes de datos inmutables.

  Se consideran avanzados porque la omisión del proxy es sólo a nivel de raíz, por lo que si se establece un objeto raw anidado y no marcado en un objeto reactivo y luego accedes a él nuevamente, obtienes la versión de proxy. Esto puede conducir a **riesgos de identidad**, es decir, realizar una operación que depende de la identidad del objeto, pero utilizando tanto la versión raw como la versión proxy del mismo objeto:

  ```js
  const foo = markRaw({
    nested: {}
  })

  const bar = reactive({
    // aunque `foo` esté marcado como raw, foo.nested no lo está.
    nested: foo.nested
  })

  console.log(foo.nested === bar.nested) // false
  ```

  Los riesgos de identidad son generalmente raros. Sin embargo, para utilizar correctamente estas API y evitar riesgos de identidad de manera segura, se requiere una comprensión sólida de cómo funciona el sistema de reactividad.

  :::

## effectScope() {#effectscope}

Crea un objeto de ámbito de efecto que puede capturar los efectos reactivos (es decir, computed y watchers) creados dentro de él para que estos efectos puedan eliminarse juntos. Para conocer los casos de uso detallados de esta API, consulta su correspondiente [RFC](https://github.com/vuejs/rfcs/blob/master/active-rfcs/0041-reactivity-effect-scope.md).

- **Tipo**

  ```ts
  function effectScope(detached?: boolean): EffectScope

  interface EffectScope {
    run<T>(fn: () => T): T | undefined // undefined si el ámbito está inactivo
    stop(): void
  }
  ```

- **Ejemplo**

  ```js
  const scope = effectScope()

  scope.run(() => {
    const doubled = computed(() => counter.value * 2)

    watch(doubled, () => console.log(doubled.value))

    watchEffect(() => console.log('Count: ', doubled.value))
  })

  // para eliminar todos los efectos en el ámbito
  scope.stop()
  ```

## getCurrentScope() {#getcurrentscope}

Devuelve el [ámbito de efectos](#effectscope) activo actual, si lo hay.

- **Tipo**

  ```ts
  function getCurrentScope(): EffectScope | undefined
  ```

## onScopeDispose() {#onscopedispose}

Registra una devolución de llamada de eliminación en el [ámbito de efectos](#effectscope) activo actual. La devolución de llamada se invocará cuando se detenga el ámbito de efectos asociado.

Este método se puede usar como un reemplazo no acoplado a componentes de `onUnmounted` en funciones de composición reutilizables, ya que la función `setup()` de cada componente de Vue también se invoca en un ámbito de efectos.

- **Tipo**

  ```ts
  function onScopeDispose(fn: () => void): void
  ```
