# \<script setup> {#script-setup}

`<script setup>` es un azúcar sintáctico en tiempo de compilación para usar la Composition API dentro de los componentes de un solo archivo (SFC). Es la sintaxis recomendada si estás utilizando tanto SFC como Composition API. Proporciona una serie de ventajas sobre la sintaxis normal de `<script>`:

- Código más breve y con menos repeticiones
- Capacidad para declarar props y eventos emitidos usando TypeScript puro
- Mejor rendimiento en tiempo de ejecución (la plantilla se compila en una función de renderizado en el mismo ámbito, sin un proxy intermedio)
- Mejor rendimiento de inferencia de tipos IDE (menos trabajo para que el servidor de lenguaje extraiga los tipos del código)

## Sintaxis Básica {#basic-syntax}

Para optar por la sintaxis, agregue el atributo `setup` al bloque `<script>`:

```vue
<script setup>
console.log('hola script setup')
</script>
```

El código interno se compila como el contenido de la función `setup()` del componente. Esto significa que, a diferencia del `<script>` normal, que solo se ejecuta una vez cuando el componente se importa por primera vez, el código dentro de `<script setup>` **se ejecutará cada vez que se cree una instancia del componente**.

### Los enlaces de nivel superior se exponen a la plantilla {#top-level-bindings-are-exposed-to-template}

Al usar `<script setup>`, cualquier enlace de nivel superior (incluyendo variables, declaraciones de funciones e importaciones) declarado dentro de `<script setup>` se puede usar directamente en la plantilla:

```vue
<script setup>
// variable
const msg = 'Hola!'

// funciones
function log() {
  console.log(msg)
}
</script>

<template>
  <button @click="log">{{ msg }}</button>
</template>
```

Las importaciones se exponen de la misma manera. Esto significa que puedes usar directamente una función auxiliar importada en expresiones de plantilla sin tener que exponerla mediante la opción `methods`:

```vue
<script setup>
import { capitalize } from './helpers'
</script>

<template>
  <div>{{ capitalize('hola') }}</div>
</template>
```

## Reactividad {#reactivity}

El estado reactivo debe crearse explícitamente mediante las [API de reactividad](./reactivity-core). Al igual que los valores devueltos por una función `setup()`, las refs se desenvuelven automáticamente cuando se hace referencia a ellas en las plantillas:

```vue
<script setup>
import { ref } from 'vue'

const count = ref(0)
</script>

<template>
  <button @click="count++">{{ count }}</button>
</template>
```

## Usando Componentes {#using-components}

Los valores en el ámbito de `<script setup>` también pueden utilizarse directamente como nombres de etiquetas de componentes personalizados:

```vue
<script setup>
import MyComponent from './MyComponent.vue'
</script>

<template>
  <MyComponent />
</template>
```

Piensa en `MyComponent` como si fuera una variable. Si has utilizado JSX, el modelo mental es similar aquí. El equivalente en kebab-case `<my-component>` también funciona en la plantilla; sin embargo, se recomiendan encarecidamente las etiquetas de componentes en PascalCase para mantener la coherencia. También ayuda a diferenciarse de los elementos personalizados nativos.

### Componentes Dinámicos {#dynamic-components}

Dado que los componentes se referencian como variables en lugar de registrarse bajo claves de tipo cadena, deberíamos utilizar el enlace dinámico `:is` cuando usamos componentes dinámicos dentro de `<script setup>`:

```vue
<script setup>
import Foo from './Foo.vue'
import Bar from './Bar.vue'
</script>

<template>
  <component :is="Foo" />
  <component :is="someCondition ? Foo : Bar" />
</template>
```

Nota cómo los componentes pueden utilizarse como variables en una expresión ternaria.

### Componentes Recursivos {#recursive-components}

Un SFC puede referirse implícitamente a sí mismo a través de su nombre de archivo. Por ejemplo, un archivo llamado `FooBar.vue` puede referirse a sí mismo como `<FooBar/>` en su plantilla.

Ten en cuenta que esto tiene menor prioridad que los componentes importados. Si tienes una importación con nombre que entra en conflicto con el nombre inferido del componente, puedes crear un alias para la importación:

```js
import { FooBar as FooBarChild } from './components'
```

### Componentes con Espacio de Nombres {#namespaced-components}

Puedes utilizar etiquetas de componentes con puntos como `<Foo.Bar>` para referirte a los componentes anidados en las propiedades del objeto. Esto es útil cuando importas múltiples componentes desde un solo archivo:

```vue
<script setup>
import * as Form from './form-components'
</script>

<template>
  <Form.Input>
    <Form.Label>label</Form.Label>
  </Form.Input>
</template>
```

## Usando Directivas Personalizadas {#using-custom-directives}

Las directivas personalizadas registradas globalmente funcionan normalmente. Las directivas personalizadas locales no necesitan registrarse explícitamente con `<script setup>`, pero deben seguir el esquema de nombres `vNameOfDirective`:

```vue
<script setup>
const vMyDirective = {
  beforeMount: (el) => {
    // haz algo con el elemento
  }
}
</script>
<template>
  <h1 v-my-directive>Esto es un Título</h1>
</template>
```

Si estás importando una directiva desde otro lugar, se le puede cambiar el nombre para que se ajuste al esquema de nombres requerido:

```vue
<script setup>
import { myDirective as vMyDirective } from './MyDirective.js'
</script>
```

## defineProps() y defineEmits() {#defineprops-defineemits}

Para declarar opciones como `props` y `emits` con soporte completo de inferencia de tipo, podemos usar las API `defineProps` y `defineEmits`, que están disponibles automáticamente dentro de `<script setup>`:

```vue
<script setup>
const props = defineProps({
  foo: String
})

const emit = defineEmits(['change', 'delete'])
// código
</script>
```

- `defineProps` y `defineEmits` son **macros del compilador** que solo se pueden usar dentro de `<script setup>`. No es necesario importarlos y se compilan cuando se procesa `<script setup>`.

- `defineProps` acepta el mismo valor que la opción `props`, mientras que `defineEmits` acepta el mismo valor que la opción `emits`.

- `defineProps` y `defineEmits` proporcionan una inferencia de tipo apropiada basada en las opciones pasadas.

- Las opciones pasadas a `defineProps` y `defineEmits` se sacarán del setup al ámbito del módulo. Por lo tanto, las opciones no pueden hacer referencia a las variables locales declaradas en el ámbito de setup. Si lo hacen, se producirá un error de compilación. Sin embargo, _puede_ hacer referencia a enlaces importados ya que también están en el ámbito del módulo.

### Declaraciones de props/emit de sólo tipo {#type-only-props-emit-declarations}

Los props y los emits también pueden declararse utilizando la sintaxis de tipo puro, pasando un argumento de tipo literal a `defineProps` o `defineEmits`:

```ts
const props = defineProps<{
  foo: string
  bar?: number
}>()

const emit = defineEmits<{
  (e: 'change', id: number): void
  (e: 'update', value: string): void
}>()

// 3.3+: sintaxis alternativa más sucinta
const emit = defineEmits<{
  change: [id: number] // sintaxis de tupla nombrada
  update: [value: string]
}>()
```

- `defineProps` o `defineEmits` sólo pueden utilizar la declaración en tiempo de ejecución O una declaración de tipo. El uso de ambos al mismo tiempo dará como resultado un error de compilación.

- Cuando se utiliza la declaración de tipo, la declaración equivalente en tiempo de ejecución se genera automáticamente a partir del análisis estático para eliminar la necesidad de una doble declaración y seguir garantizando un comportamiento correcto en tiempo de ejecución.

  - En el modo de desarrollo, el compilador intentará inferir la validación correspondiente en tiempo de ejecución a partir de los tipos. Por ejemplo, aquí `foo: String` se deduce del tipo `foo: string`. Si el tipo es una referencia a un tipo importado, el resultado inferido será `foo: null` (equivalente al tipo `any`) ya que el compilador no tiene información de archivos externos.

  - En el modo de producción, el compilador generará la declaración de formato del array para reducir el tamaño del paquete (los props aquí serán compilados en `['foo', 'bar']`)

  - En la versión 3.2 e inferiores, el parámetro de tipo genérico para `defineProps()` estaba limitado a un literal de tipo o a una referencia a una interfaz local.

  Esta limitación se ha resuelto en la versión 3.3. La última versión de Vue soporta la referencia a tipos importados y a un conjunto limitado de tipos complejos en la posición del parámetro de tipo. Sin embargo, debido a que la conversión de tipo a tiempo de ejecución sigue basándose en AST, algunos tipos complejos que requieren un análisis de tipo real, por ejemplo, los tipos condicionales, no son compatibles. Puedes utilizar tipos condicionales para el tipo de una única prop, pero no para todo el objeto props.

### Valores por defecto de props cuando se usa declaración de tipo {#default-props-values-when-using-type-declaration}

Uno de los inconvenientes de la declaración de tipo con `defineProps` es que no tiene una forma de proporcionar valores por defecto para los props. Para resolver este problema, también se proporciona una macro del compilador `withDefaults`:

```ts
export interface Props {
  msg?: string
  labels?: string[]
}

const props = withDefaults(defineProps<Props>(), {
  msg: 'hola',
  labels: () => ['uno', 'dos']
})
```

Esto se compilará con las opciones equivalentes de props `default` en tiempo de ejecución. Además, el ayudante `withDefaults` proporciona comprobaciones de tipo para los valores por defecto, y garantiza que el tipo `props` devuelto tenga los indicadores opcionales eliminados para las propiedades que sí tienen valores por defecto declarados.

## defineModel() <sup class="vt-badge" data-text="3.4+" /> {#definemodel}

Esta macro se puede usar para declarar una propiedad de enlace bidireccional que puede ser consumida a través de `v-model` desde el componente padre. El ejemplo de su uso también se discute en la guía [Componente `v-model`](/guide/components/v-model).

Detrás de escena, esta macro declara una propiedad model y un evento de actualización de valor correspondiente. Si el primer argumento es una cadena literal, se usará como el nombre de la prop; de lo contrario, el nombre de la prop será `"modelValue"` por defecto. En ambos casos, también se puede pasar un objeto adicional que puede incluir las opciones de la prop y las opciones de transformación del valor de la ref del model.

```js
// declara la prop "modelValue", consumida por el padre mediante v-model
const model = defineModel()
// O: declara la prop "modelValue" con opciones
const model = defineModel({ type: String })

// emite "update:modelValue" cuando se muta
model.value = 'hello'

// declara la prop "count", consumida por el padre mediante v-model:count
const count = defineModel('count')
// O: declara la prop "count" con opciones
const count = defineModel('count', { type: Number, default: 0 })

function inc() {
  // emite "update:count" cuando se muta
  count.value++
}
```

:::warning
Si tienes un valor `default` para la prop `defineModel` y no proporcionas ningún valor para esta prop desde el componente padre, puede causar una desincronización entre el componente padre y el componente hijo. En el ejemplo siguiente, el `myRef` del padre es indefinido, pero el `model` del hijo es 1:

```js
// componente hijo:
const model = defineModel({ default: 1 })

// componente padre:
const myRef = ref()
```

```html
<Child v-model="myRef"></Child>
```

:::

### Modificadores y Transformadores {#modifiers-and-transformers}

Para acceder a los modificadores usados con la directiva `v-model`, podemos desestructurar el valor de retorno de `defineModel()` de esta manera:

```js
const [modelValue, modelModifiers] = defineModel()

// corresponde a v-model.trim
if (modelModifiers.trim) {
  // ...
}
```

Cuando está presente un modificador, probablemente necesitemos transformar el valor al leerlo o sincronizarlo de vuelta con el padre. Podemos lograr esto utilizando las opciones de transformación `get` y `set`:

```js
const [modelValue, modelModifiers] = defineModel({
  // get() omitido ya que no es necesario aquí
  set(value) {
    // si se utiliza el modificador .trim, devuelve el valor recortado
    if (modelModifiers.trim) {
      return value.trim()
    }
    // de lo contrario, devuelve el valor tal como está
    return value
  }
})
```

### Uso con TypeScript <sup class="vt-badge ts" /> {#usage-with-typescript}

Al igual que `defineProps` y `defineEmits`, `defineModel` también puede recibir argumentos de tipo para especificar los tipos del valor del modelo y los modificadores:

```ts
const modelValue = defineModel<string>()
//    ^? Ref<string | undefined>
// model por defecto con opciones, required elimina posibles valores indefinidos
const modelValue = defineModel<string>({ required: true })
//    ^? Ref<string>

const [modelValue, modifiers] = defineModel<string, 'trim' | 'uppercase'>()
//                 ^? Record<'trim' | 'uppercase', true | undefined>
```

## defineExpose() {#defineexpose}

Los componentes que utilizan `<script setup>` **están cerrados por defecto**, es decir, la instancia pública del componente, que se recupera a través de las refs de plantilla o de las cadenas `$parent`, **no** expondrá ninguno de los enlaces declarados dentro de `<script setup>`.

Para exponer explícitamente las propiedades en un componente `<script setup>`, utilice la macro del compilador `defineExpose`:

```vue
<script setup>
import { ref } from 'vue'

const a = 1
const b = ref(2)

defineExpose({
  a,
  b
})
</script>
```

Cuando un padre obtiene una instancia de este componente a través de refs de plantilla, la instancia recuperada tendrá la forma `{ a: number, b: number }` (las refs se desenvuelven automáticamente como en las instancias normales).

## defineOptions() <sup class="vt-badge" data-text="3.3+" /> {#defineoptions}

Esta macro puede usarse para declarar opciones de componentes directamente dentro de `<script setup>` sin tener que usar un bloque `<script>` separado:

```vue
<script setup>
defineOptions({
  inheritAttrs: false,
  customOptions: {
    /* ... */
  }
})
</script>
```

- Sólo se admite en 3.3+.
- Se trata de una macro. Las opciones serán elevadas al ámbito del módulo y no podrán acceder a variables locales en `<script setup>` que no sean constantes literales.

## defineSlots()<sup class="vt-badge ts"/> {#defineslots}

Esta macro puede usarse para proporcionar sugerencias de tipo a los IDEs para la comprobación de tipo de nombre de slot y props.

`defineSlots()` sólo acepta un parámetro de tipo y ningún argumento de ejecución. El parámetro de tipo debe ser un literal de tipo donde la clave de propiedad es el nombre del slot, y el tipo de valor es la función del slot. El primer argumento de la función son las props que el slot espera recibir, y su tipo se utilizará para las props del slot en la plantilla. El tipo de retorno se ignora actualmente y puede ser `any`, pero puede que lo utilicemos para comprobar el contenido del slot en el futuro.

También retorna el objeto `slots`, que es equivalente al objeto `slots` expuesto en el contexto de setup o retornado por `useSlots()`.

```vue
<script setup lang="ts">
const slots = defineSlots<{
  default(props: { msg: string }): any
}>()
</script>
```

- Sólo se admite en 3.3+.

```vue
<script setup lang="ts">
const slots = defineSlots<{
  default: { msg: string }
}>()
</script>
```

- Sólo se admite en 3.3+.

## `useSlots()` y `useAttrs()` {#useslots-useattrs}

El uso de `slots` y `attrs` dentro de `<script setup>` debería ser relativamente raro, ya que puedes acceder a ellos directamente como `$slots` y `$attrs` en la plantilla. En el raro caso de que los necesites, utiliza los ayudantes `useSlots` y `useAttrs` respectivamente:

```vue
<script setup>
import { useSlots, useAttrs } from 'vue'

const slots = useSlots()
const attrs = useAttrs()
</script>
```

`useSlots` y `useAttrs` son funciones reales en tiempo de ejecución que devuelven el equivalente de `setupContext.slots` y `setupContext.attrs`. También se pueden utilizar en las funciones normales de la Composition API.

## Usando junto a un `<script>` normal {#usage-alongside-normal-script}

`<script setup>` se puede usar junto a un `<script>` normal. Es posible que se necesite un `<script>` normal en los casos en que necesitemos:

- Declarar opciones que no se pueden expresar en `<script setup>`, por ejemplo, `inheritAttrs` u opciones personalizadas habilitadas a través de plugins (puede ser reemplazado utilizando [`defineOptions`](/api/sfc-script-setup#defineoptions) desde la versión 3.3+).
- Declarar exportaciones con nombre.
- Ejecutar efectos secundarios o crear objetos que sólo deben ejecutarse una vez.

```vue
<script>
// un <script> normal, ejecutado en el ámbito del módulo (sólo una vez)
runSideEffectOnce()

// declarar opciones adicionales
export default {
  inheritAttrs: false,
  customOptions: {}
}
</script-normal>

<script setup>
// ejecutado en el ámbito de setup() (para cada instancia)
</script>
```

El soporte para combinar `<script setup>` y `<script>` en el mismo componente está limitado a los escenarios descritos anteriormente. En concreto:

- **NO** utilice una sección `<script>` separada para opciones que ya pueden definirse utilizando `<script setup>`, tales como `props` y `emits`.
- Las variables creadas dentro de `<script setup>` no se añaden como propiedades a la instancia del componente, haciéndolas inaccesibles desde la Options API. Se desaconseja encarecidamente mezclar APIs de este modo.

Si se encuentra en uno de los escenarios no soportados, debería considerar cambiar a una función [`setup()`](/api/composition-api-setup) explícita, en lugar de utilizar `<script setup>`.

## Nivel Superior de `await` {#top-level-await}

El nivel superior de `await` se puede usar dentro de `<script setup>`. El código resultante se compilará como `async setup()`:

```vue
<script setup>
const post = await fetch(`/api/post/1`).then((r) => r.json())
</script>
```

Además, la expresión esperada se compilará automáticamente en un formato que conserva el contexto de la instancia del componente actual después del `await`.

:::warning Nota
`async setup()` debe usarse en combinación con `Suspense`, que actualmente sigue siendo una característica experimental. Planeamos finalizarla y documentarla en una versión futura, pero si tienes curiosidad ahora, puedes consultar sus [pruebas](https://github.com/vuejs/core/blob/main/packages/runtime-core/__tests__/components/Suspense.spec.ts) para ver cómo funciona.
:::

### Genéricos <sup class="vt-badge ts" /> {#generics}

Los parámetros de tipo genérico pueden declararse utilizando el atributo `generic` de la etiqueta `<script>`:

```vue
<script setup lang="ts" generic="T">
defineProps<{
  items: T[]
  selected: T
}>()
</script>
```

El valor de `generic` funciona exactamente igual que la lista de parámetros entre `<...>` en TypeScript. Por ejemplo, puedes usar múltiples parámetros, restricciones `extends`, tipos por defecto y tipos importados de referencia:

```vue
<script
  setup
  lang="ts"
  generic="T extends string | number, U extends Item"
>
import type { Item } from './types'
defineProps<{
  id: T
  list: U[]
}>()
</script>
```

## Restricciones {#restrictions}

- Debido a la diferencia en la semántica de ejecución del módulo, el código dentro de `<script setup>` depende del contexto de un SFC. Cuando se mueve a archivos externos `.js` o `.ts`, puede generar confusión tanto para los desarrolladores como para las herramientas. Por lo tanto, **`<script setup>`** no se puede utilizar con el atributo `src`.
- `<script setup>` no soporta Plantilla de Componente Raíz en el DOM.([Discusión relacionada](https://github.com/vuejs/core/issues/8391))
