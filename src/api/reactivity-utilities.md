# Reactividad: Utilidades {#reactivity-api-utilities}

## isRef() {#isref}

Comprueba si un valor es un objeto ref.

- **Tipo**

  ```ts
  function isRef<T>(r: Ref<T> | unknown): r is Ref<T>
  ```

  Ten en cuenta que el tipo de retorno es un [predicado de tipo](https://www.typescriptlang.org/docs/handbook/2/narrowing.html#using-type-predicates), lo que significa que `isRef` puede ser utilizado como un protector de tipo:

  ```ts
  let foo: unknown
  if (isRef(foo)) {
    // El tipo de foo se reduce a Ref<unknown>
    foo.value
  }
  ```

## unref() {#unref}

Devuelve el valor interno si el argumento es una ref, de lo contrario devuelve el argumento en sí. Esta es una función de azúcar sintáctica para `val = isRef(val) ? val.value : val`.

- **Tipo**

  ```ts
  function unref<T>(ref: T | Ref<T>): T
  ```

- **Ejemplo**

  ```ts
  function useFoo(x: number | Ref<number>) {
    const unwrapped = unref(x)
    // unwrapped se garantiza que será ahora number
  }
  ```

## toRef() {#toref}

Se puede utilizar para normalizar valores / refs / getters convirtiéndolos en refs (3.3+).

También se puede utilizar para crear una referencia para una propiedad en un objeto fuente reactivo. La referencia creada se sincroniza con su propiedad fuente: el mutar la propiedad fuente actualizará la referencia y viceversa.

- **Tipo**

  ```ts
  // signatura de normalización (3.3+)
  function toRef<T>(
    value: T
  ): T extends () => infer R
    ? Readonly<Ref<R>>
    : T extends Ref
    ? T
    : Ref<UnwrapRef<T>>

  // signatura de propiedad del objeto
  function toRef<T extends object, K extends keyof T>(
    object: T,
    key: K,
    defaultValue?: T[K]
  ): ToRef<T[K]>

  type ToRef<T> = T extends Ref ? T : Ref<T>
  ```

- **Ejemplo**

  Signatura de normalización (3.3+)

  ```js
  // devuelve los refs existentes tal cual
  toRef(existingRef)

  // crea un ref de sólo lectura que llama al getter en el acceso a .value
  toRef(() => props.foo)

  // crea refs normales a partir de valores no funcionales
  // equivalente a ref(1)
  toRef(1)
  ```

  Signatura de propiedad del objeto

  ```js
  const state = reactive({
    foo: 1,
    bar: 2
  })

  // un ref bidireccional que se sincroniza con la propiedad original
  const fooRef = toRef(state, 'foo')

  // la mutación de la ref actualiza el original
  fooRef.value++
  console.log(state.foo) // 2

  // al mutar el original también se actualiza la ref
  state.foo++
  console.log(fooRef.value) // 3
  ```

  Ten en cuenta que esto es diferente de:

  ```js
  const fooRef = ref(state.foo)
  ```

  La ref anterior **no** está sincronizada con `state.foo`, porque `ref()` recibe un valor numérico simple.

  `toRef()` es útil cuando deseas pasar la ref de una prop a una función composable:

  ```vue
  <script setup>
  import { toRef } from 'vue'

  const props = defineProps(/* ... */)

  // convertir `props.foo` en una ref, y luego pasarla a
  // una composable
  useSomeFeature(toRef(props, 'foo'))

  // sintaxis getter - recomendada en 3.3+
  useSomeFeature(toRef(() => props.foo))
  </script>
  ```

  Cuando `toRef` se usa con props de componentes, se siguen aplicando las restricciones habituales sobre la mutación de las props. Intentar asignar un nuevo valor a la ref es equivalente a intentar modificar la prop directamente y no está permitido. En ese escenario, puedes considerar el uso de [`computed`](./reactivity-core#computed) con `get` y `set` en su lugar. Consulta la guía para [usar `v-model` con componentes](/guide/components/v-model) para más información.

  Cuando se utiliza la signatura de propiedad del objeto, `toRef()` devolverá una ref utilizable incluso si la propiedad de origen no existe actualmente. Esto hace posible trabajar con propiedades opcionales, que no serían recogidas por [`toRefs`](#torefs).

## toValue() <sup class="vt-badge" data-text="3.3+" /> {#tovalue}

Normaliza valores / refs / getters a valores. Esto es similar a [unref()](#unref), excepto que también normaliza getters. Si el argumento es un getter, se invocará y se devolverá su valor de retorno.

Se puede utilizar en [Composables](/guide/reusability/composables.html) para normalizar un argumento que puede ser un valor, una ref o un getter.

- **Tipo**

  ```ts
  function toValue<T>(source: T | Ref<T> | (() => T)): T
  ```

- **Ejemplo**

  ```js
  toValue(1) //       --> 1
  toValue(ref(1)) //  --> 1
  toValue(() => 1) // --> 1
  ```

  Normalización de argumentos en composables:

  ```ts
  import type { MaybeRefOrGetter } from 'vue'

  function useFeature(id: MaybeRefOrGetter<number>) {
    watch(
      () => toValue(id),
      (id) => {
        // Reaccionar a cambios del id
      }
    )
  }

  // este composable admite cualquiera de los siguientes:
  useFeature(1)
  useFeature(ref(1))
  useFeature(() => 1)
  ```

## toRefs() {#torefs}

Convierte un objeto reactivo en un objeto simple donde cada propiedad del objeto resultante es una ref que apunta a la propiedad correspondiente del objeto original. Cada ref individual se crea utilizando [`toRef()`](#toref).

- **Tipo**

  ```ts
  function toRefs<T extends object>(
    object: T
  ): {
    [K in keyof T]: ToRef<T[K]>
  }

  type ToRef = T extends Ref ? T : Ref<T>
  ```

- **Ejemplo**

  ```js
  const state = reactive({
    foo: 1,
    bar: 2
  })

  const stateAsRefs = toRefs(state)
  /*
  Tipos de stateAsRefs: {
    foo: Ref<number>,
    bar: Ref<number>
  }
  */

  // La ref y la propiedad original están "vinculadas"
  state.foo++
  console.log(stateAsRefs.foo.value) // 2

  stateAsRefs.foo.value++
  console.log(state.foo) // 3
  ```

  `toRefs` es útil cuando se devuelve un objeto reactivo de una función composable para que el componente consumidor pueda desestructurar/difundir el objeto devuelto sin perder la reactividad:

  ```js
  function useFeatureX() {
    const state = reactive({
      foo: 1,
      bar: 2
    })

    // ...lógica que opera sobre el estado

    // convierte en refs cuando hace el return
    return toRefs(state)
  }

  // puede desestructurarse sin perder la reactividad
  const { foo, bar } = useFeatureX()
  ```

  `toRefs` solo generará refs para las propiedades que se pueden enumerar en el objeto de origen en el momento de la llamada. Para crear una ref para una propiedad que quizás aún no exista, utiliza [`toRef`](#toref) en su lugar.

## isProxy() {#isproxy}

Comprueba si un objeto es un proxy creado por [`reactive()`](./reactivity-core#reactive), [`readonly()`](./reactivity-core#readonly), [`shallowReactive()`](./reactivity-advanced#shallowreactive) o [`shallowReadonly()`](./reactivity-advanced#shallowreadonly).

- **Tipo**

  ```ts
  function isProxy(value: any): boolean
  ```

## isReactive() {#isreactive}

Comprueba si un objeto es un proxy creado por [`reactive()`](./reactivity-core#reactive) o [`shallowReactive()`](./reactivity-advanced#shallowreactive).

- **Tipo**

  ```ts
  function isReactive(value: unknown): boolean
  ```

## isReadonly() {#isreadonly}

Comprueba si un objeto es un proxy creado por [`readonly()`](./reactivity-core#readonly) o [`shallowReadonly()`](./reactivity-advanced#shallowreadonly).

- **Tipo**

  ```ts
  function isReadonly(value: unknown): boolean
  ```
