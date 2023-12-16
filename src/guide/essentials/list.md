# Renderizado de Listas {#list-rendering}

<div class="options-api">
  <VueSchoolLink href="https://vueschool.io/lessons/list-rendering-in-vue-3" title="Lección gratuita de Renderizado de Listas en Vue.js"/>
</div>

<div class="composition-api">
  <VueSchoolLink href="https://vueschool.io/lessons/vue-fundamentals-capi-list-rendering-in-vue" title="Lección gratuita de Renderizado de Listas en Vue.js"/>
</div>

## `v-for` {#v-for}

Podemos utilizar la directiva `v-for` para representar una lista de elementos basada en un array. La directiva `v-for` requiere una sintaxis especial en forma de `item in items`, donde `items` es el array de datos fuente y `item` es un **alias** para el elemento del array sobre el que se está iterando:

<div class="composition-api">

```js
const items = ref([{ message: 'Foo' }, { message: 'Bar' }])
```

</div>

<div class="options-api">

```js
data() {
  return {
    items: [{ message: 'Foo' }, { message: 'Bar' }]
  }
}
```

</div>

```vue-html
<li v-for="item in items">
  {{ item.message }}
</li>
```

Dentro del ámbito `v-for`, las expresiones de la plantilla tienen acceso a todas las propiedades del ámbito padre. Además, `v-for` también admite un segundo alias opcional para el índice del elemento actual:

<div class="composition-api">

```js
const parentMessage = ref('Parent')
const items = ref([{ message: 'Foo' }, { message: 'Bar' }])
```

</div>
<div class="options-api">

```js
data() {
  return {
    parentMessage: 'Parent',
    items: [{ message: 'Foo' }, { message: 'Bar' }]
  }
}
```

</div>

```vue-html
<li v-for="(item, index) in items">
  {{ parentMessage }} - {{ index }} - {{ item.message }}
</li>
```

<script setup>
const parentMessage = 'Parent'
const items = [{ message: 'Foo' }, { message: 'Bar' }]
</script>
<div class="demo">
  <li v-for="(item, index) in items">
    {{ parentMessage }} - {{ index }} - {{ item.message }}
  </li>
</div>

<div class="composition-api">

[Pruébalo en la Zona de Práctica](https://play.vuejs.org/#eNpdTsuqwjAQ/ZVDNlFQu5d64bpwJ7g3LopOJdAmIRlFCPl3p60PcDWcM+eV1X8Iq/uN1FrV6RxtYCTiW/gzzvbBR0ZGpBYFbfQ9tEi1ccadvUuM0ERyvKeUmithMyhn+jCSev4WWaY+vZ7HjH5Sr6F33muUhTR8uW0ThTuJua6mPbJEgGSErmEaENedxX3Z+rgxajbEL2DdhR5zOVOdUSIEDOf8M7IULCHsaPgiMa1eK4QcS6rOSkhdfapVeQLQEWnH)

</div>
<div class="options-api">

[Pruébalo en la Zona de Práctica](https://play.vuejs.org/#eNpVTssKwjAQ/JUllyr0cS9V0IM3wbvxEOxWAm0a0m0phPy7m1aqhpDsDLMz48XJ2nwaUZSiGp5OWzpKg7PtHUGNjRpbAi8NQK1I7fbrLMkhjc5EJAn4WOXQ0BWHQb2whOS24CSN6qjXhN1Qwt1Dt2kufZ9ASOGXOyvH3GMNCdGdH75VsZVjwGa2VYQRUdVqmLKmdwcpdjEnBW1qnPf8wZIrBQujoff/RSEEyIDZZeGLeCn/dGJyCSlazSZVsUWL8AYme21i)

</div>

El ámbito de las variables de `v-for` es similar al siguiente JavaScript:

```js
const parentMessage = 'Parent'
const items = [
  /* ... */
]

items.forEach((item, index) => {
  // tiene acceso al ámbito externo `parentMessage`
  // pero `item` e `index` sólo están disponibles aquí
  console.log(parentMessage, item.message, index)
})
```

Observa en que el valor `v-for` coincide con la firma de la función del callback `forEach`. De hecho, puedes utilizar la desestructuración en el alias del elemento `v-for` de forma similar a la desestructuración de los argumentos de la función:

```vue-html
<li v-for="{ message } in items">
  {{ message }}
</li>

<!-- con alias de índice -->
<li v-for="({ message }, index) in items">
  {{ message }} {{ index }}
</li>
```

En el caso de los `v-for` anidados, el ámbito también funciona de forma similar a las funciones anidadas. Cada ámbito `v-for` tiene acceso a los ámbitos padre:

```vue-html
<li v-for="item in items">
  <span v-for="childItem in item.children">
    {{ item.message }} {{ childItem }}
  </span>
</li>
```

También puedes utilizar `of` como delimitador en lugar de `in`, para que se acerque más a la sintaxis de JavaScript para los iteradores:

```vue-html
<div v-for="item of items"></div>
```

## `v-for` con un Objeto {#v-for-with-an-object}

También puedes utilizar `v-for` para iterar a través de las propiedades de un objeto. El orden de iteración se basará en el resultado de llamar a `Object.keys()` sobre el objeto:

<div class="composition-api">

```js
const myObject = reactive({
  title: 'Cómo hacer listas en Vue',
  author: 'Jane Doe',
  publishedAt: '2016-04-10'
})
```

</div>
<div class="options-api">

```js
data() {
  return {
    myObject: {
      title: 'Cómo hacer listas en Vue',
      author: 'Jane Doe',
      publishedAt: '2016-04-10'
    }
  }
}
```

</div>

```vue-html
<ul>
  <li v-for="value in myObject">
    {{ value }}
  </li>
</ul>
```

También puedes proporcionar un segundo alias para el nombre de la propiedad (también conocido como key o clave):

```vue-html
<li v-for="(value, key) in myObject">
  {{ key }}: {{ value }}
</li>
```

Y otra para el índice:

```vue-html
<li v-for="(value, key, index) in myObject">
  {{ index }}. {{ key }}: {{ value }}
</li>
```

<div class="composition-api">

[Pruébalo en la Zona de Práctica](https://play.vuejs.org/#eNo9jjFvgzAQhf/KE0sSCQKpqg7IqRSpQ9WlWycvBC6KW2NbcKaNEP+9B7Tx4nt33917Y3IKYT9ESspE9XVnAqMnjuFZO9MG3zFGdFTVbAbChEvnW2yE32inXe1dz2hv7+dPqhnHO7kdtQPYsKUSm1f/DfZoPKzpuYdx+JAL6cxUka++E+itcoQX/9cO8SzslZoTy+yhODxlxWN2KMR22mmn8jWrpBTB1AZbMc2KVbTyQ56yBkN28d1RJ9uhspFSfNEtFf+GfnZzjP/oOll2NQPjuM4xTftZyIaU5VwuN0SsqMqtWZxUvliq/J4jmX4BTCp08A==)

</div>
<div class="options-api">

[Pruébalo en la Zona de Práctica](https://play.vuejs.org/#eNo9T8FqwzAM/RWRS1pImnSMHYI3KOwwdtltJ1/cRqXe3Ng4ctYS8u+TbVJjLD3rPelpLg7O7aaARVeI8eS1ozc54M1ZT9DjWQVDMMsBoFekNtucS/JIwQ8RSQI+1/vX8QdP1K2E+EmaDHZQftg/IAu9BaNHGkEP8B2wrFYxgAp0sZ6pn2pAeLepmEuSXDiy7oL9gduXT+3+pW6f631bZoqkJY/kkB6+onnswoDw6owijIhEMByjUBgNU322/lUWm0mZgBX84r1ifz3ettHmupYskjbanedch2XZRcAKTnnvGVIPBpkqGqPTJNGkkaJ5+CiWf4KkfBs=)

</div>

## `v-for` con un Rango {#v-for-with-a-range}

`v-for` también puede tomar un entero. En este caso se repetirá la plantilla tantas veces, basado en un rango de `1...n`.

```vue-html
<span v-for="n in 10">{{ n }}</span>
```

Observa que aquí `n` comienza con un valor inicial de `1` en lugar de `0`.

## `v-for` en `<template>` {#v-for-on-template}

De forma similar a la etiqueta `v-if`, también puedes utilizar una etiqueta `<template>` con `v-for` para representar un bloque de múltiples elementos. Por ejemplo:

```vue-html
<ul>
  <template v-for="item in items">
    <li>{{ item.msg }}</li>
    <li class="divider" role="presentation"></li>
  </template>
</ul>
```

## `v-for` con `v-if` {#v-for-with-v-if}

:::warning Nota
**No** es recomendado usar `v-if` y `v-for` en el mismo elemento debido a la precedencia implícita. Consulta la [guía de estilo](/style-guide/rules-essential#avoid-v-if-with-v-for) para más detalles.
:::

Cuando existen en el mismo nodo, `v-if` tiene mayor prioridad que `v-for`. Esto significa que la condición `v-if` no tendrá acceso a las variables del ámbito de `v-for`:

```vue-html
<!--
Esto arrojará un error porque la propiedad "todo"
no está definida en la instancia.
-->
<li v-for="todo in todos" v-if="!todo.isComplete">
  {{ todo.name }}
</li>
```

Esto puede arreglarse moviendo `v-for` a una etiqueta envolvente `<template>` (que también es más explícita):

```vue-html
<template v-for="todo in todos">
  <li v-if="!todo.isComplete">
    {{ todo.name }}
  </li>
</template>
```

## Manteniendo el Estado con `key` {#maintaining-state-with-key}

Cuando Vue está actualizando una lista de elementos renderizados con `v-for`, por defecto utiliza una estrategia de "parche en el lugar". Si el orden de los elementos de datos ha cambiado, en lugar de mover los elementos del DOM para que coincidan con el orden de los elementos, Vue parchará cada elemento en su lugar y se asegurará de que refleje lo que debe ser renderizado en ese índice en particular.

Este modo por defecto es eficiente, pero **sólo es adecuado cuando la salida de la lista no depende del estado de los componentes hijos o del estado temporal del DOM (por ejemplo, los valores de entrada del formulario)**.

Para darle a Vue una pista para que pueda rastrear la identidad de cada nodo, y así reutilizar y reordenar los elementos existentes, necesitas proporcionar un atributo `key` único para cada elemento:

```vue-html
<div v-for="item in items" :key="item.id">
  <!-- contenido -->
</div>
```

Cuando se usa `<template v-for>`, la `key` debe colocarse en el contenedor `<template>`:

```vue-html
<template v-for="todo in todos" :key="todo.name">
  <li>{{ todo.name }}</li>
</template>
```

:::tip Nota
`key` aquí es un atributo especial que se vincula con `v-bind`. No debe confundirse con la variable clave de la propiedad cuando [se utiliza `v-for` con un objeto](#v-for-con-un-objeto).
:::

[Se recomienda](/style-guide/rules-essential#use-keyed-v-for) proporcionar un atributo `key` con `v-for` siempre que sea posible, a menos que el contenido del DOM iterado sea simple (es decir, que no contenga componentes o elementos del DOM con estado), o que se confíe intencionadamente en el comportamiento por defecto para mejorar el rendimiento.

El enlace `key` espera valores primitivos, es decir, cadenas y números. No utilice objetos como claves `v-for`. Para un uso detallado del atributo `key`, por favor consulta la [documentación de la API `key`](/api/built-in-special-attributes#key).

## `v-for` con un Componente {#v-for-with-a-component}

> Esta sección supone el conocimiento de [Componentes](/guide/essentials/component-basics). Siéntete libre de saltarla y volver más tarde.

Puedes usar directamente `v-for` en un componente, como cualquier elemento normal (no olvides proporcionar una `clave`):

```vue-html
<MyComponent v-for="item in items" :key="item.id" />
```

Sin embargo, esto no pasará automáticamente ningún dato al componente, porque los componentes tienen ámbitos aislados propios. Para pasar los datos iterados al componente, debemos usar también props:

```vue-html
<MyComponent
  v-for="(item, index) in items"
  :item="item"
  :index=" index"
  :key="item.id"
/>
```

La razón por la que no se inyecta automáticamente `item` en el componente es porque eso hace que el componente esté estrechamente acoplado a cómo funciona `v-for`. Ser explícito sobre la procedencia de sus datos hace que el componente sea reutilizable en otras situaciones.

<div class="composition-api">

Mira [este ejemplo de una simple lista de tareas](https://play.vuejs.org/#eNp1U8Fu2zAM/RXCGGAHTWx02ylwgxZYB+ywYRhyq3dwLGYRYkuCJTsZjPz7KMmK3ay9JBQfH/meKA/Rk1Jp32G0jnJdtVwZ0Gg6tSkEb5RsDQzQ4h4usG9lAzGVxldoK5n8ZrAZsTQLCduRygAKUUmhDQg8WWyLZwMPtmESx4sAGkL0mH6xrMH+AHC2hvuljw03Na4h/iLBHBAY1wfUbsTFVcwoH28o2/KIIDuaQ0TTlvrwNu/TDe+7PDlKXZ6EZxTiN4kuRI3W0dk4u4yUf7bZfScqw6WAkrEf3m+y8AOcw7Qv6w5T1elDMhs7Nbq7e61gdmme60SQAvgfIhExiSSJeeb3SBukAy1D1aVBezL5XrYN9Csp1rrbNdykqsUehXkookl0EVGxlZHX5Q5rIBLhNHFlbRD6xBiUzlOeuZJQz4XqjI+BxjSSYe2pQWwRBZizV01DmsRWeJA1Qzv0Of2TwldE5hZRlVd+FkbuOmOksJLybIwtkmfWqg+7qz47asXpSiaN3lxikSVwwfC8oD+/sEnV+oh/qcxmU85mebepgLjDBD622Mg+oDrVquYVJm7IEu4XoXKTZ1dho3gnmdJhedEymn9ab3ysDPdc4M9WKp28xE5JbB+rzz/Trm3eK3LAu8/E7p2PNzYM/i3ChR7W7L7hsSIvR7L2Aal1EhqTp80vF95sw3WcG7r8A0XaeME=) para ver cómo renderizar una lista de componentes usando `v-for`, pasando diferentes datos a cada instancia.

</div>
<div class="options-api">

Mira [este ejemplo de una simple lista de tareas](https://play.vuejs.org/#eNqNVE2PmzAQ/SsjVIlEm4C27Qmx0a7UVuqhPVS5lT04eFKsgG2BSVJF+e8d2xhIu10tihR75s2bNx9wiZ60To49RlmUd2UrtNkUUjRatQa2iquvBhvYt6qBOEmDwQbEhQQoJJ4dlOOe9bWBi7WWiuIlStNlcJlYrivr5MywxdIDAVo0fSvDDUDiyeK3eDYZxLGLsI8hI7H9DHeYQuwjeAb3I9gFCFMjUXxSYCoELroKO6fZP17Mf6jev0i1ZQcE1RtHaFrWVW/l+/Ai3zd1clQ1O8k5Uzg+j1HUZePaSFwfvdGhfNIGTaW47bV3Mc6/+zZOfaaslegS18ZE9121mIm0Ep17ynN3N5M8CB4g44AC4Lq8yTFDwAPNcK63kPTL03HR6EKboWtm0N5MvldtA8e1klnX7xphEt3ikTbpoYimsoqIwJY0r9kOa6Ag8lPeta2PvE+cA3M7k6cOEvBC6n7UfVw3imPtQ8eiouAW/IY0mElsiZWqOdqkn5NfCXxB5G6SJRvj05By1xujpJWUp8PZevLUluqP/ajPploLasmk0Re3sJ4VCMnxvKQ//0JMqrID/iaYtSaCz+xudsHjLpPzscVGHYO3SzpdixIXLskK7pcBucnTUdgg3kkmcxhetIrmH4ebr8m/n4jC6FZp+z7HTlLsVx1p4M7odcXPr6+Lnb8YOne5+C2F6/D6DH2Hx5JqOlCJ7yz7IlBTbZsf7vjXVBzjvLDrH5T0lgo=) para ver cómo renderizar una lista de componentes usando `v-for`, pasando diferentes datos a cada instancia.

</div>

## Detección de Cambios en Arrays {#array-change-detection}

### Métodos de Mutación {#mutation-methods}

Vue envuelve los métodos de mutación de un array observado para que también activen las actualizaciones de la vista. Los métodos envueltos son:

- `push()`
- `pop()`
- `shift()`
- `unshift()`
- `splice()`
- `sort()`
- `reverse()`

### Reemplazo de un Array {#replacing-an-array}

Los métodos de mutación, como su nombre indica, mutan el array original sobre el que son llamados. En comparación, también hay métodos que no mutan, como `filter()`, `concat()` y `slice()`, que no mutan el array original sino que **siempre devuelven un nuevo array**. Cuando se trabaja con métodos que no mutan, debemos sustituir el array antiguo por el nuevo:

<div class="composition-api">

```js
// `item` es una ref con valor de array
items.value = items.value.filter((item) => item.message.match(/Foo/))
```

</div>
<div class="options-api">

```js
this.items = this.items.filter((item) => item.message.match(/Foo/))
```

</div>

Podrías pensar que esto hará que Vue tire el DOM existente y vuelva a renderizar toda la lista; afortunadamente, no es el caso. Vue implementa algunas heurísticas inteligentes para maximizar la reutilización de elementos del DOM, por lo que reemplazar un array con otro array que contenga objetos superpuestos es una operación muy eficiente.

## Visualización de Resultados Filtrados/Ordenados {#displaying-filtered-sorted-results}

A veces queremos mostrar una versión filtrada u ordenada de un array sin mutar o reiniciar los datos originales. En este caso, puedes crear una propiedad computada que devuelva el array filtrado u ordenado.

Por ejemplo:

<div class="composition-api">

```js
const numbers = ref([1, 2, 3, 4, 5])

const evenNumbers = computed(() => {
  return numbers.value.filter((n) => n % 2 === 0)
})
```

</div>
<div class="options-api">

```js
data() {
  return {
    numbers: [1, 2, 3, 4, 5]
  }
},
computed: {
  evenNumbers() {
    return this.numbers.filter(n => n % 2 === 0)
  }
}
```

</div>

```vue-html
<li v-for="n in evenNumbers">{{ n }}</li>
```

En situaciones en las que las propiedades computadas no son factibles (por ejemplo, dentro de bucles `v-for` anidados), puede utilizar un método:

<div class="composition-api">

```js
const sets = ref([
  [1, 2, 3, 4, 5],
  [6, 7, 8, 9, 10]
])

function even(numbers) {
  return numbers.filter((number) => number % 2 === 0)
}
```

</div>
<div class="options-api">

```js
data() {
  return {
    sets: [[ 1, 2, 3, 4, 5 ], [6, 7, 8, 9, 10]]
  }
},
methods: {
  even(numbers) {
    return numbers.filter(number => number % 2 === 0)
  }
}
```

</div>

```vue-html
<ul v-for="numbers in sets">
  <li v-for="n in even(numbers)">{{ n }}</li>
</ul>
```

¡Ten cuidado con `reverse()` y `sort()` en una propiedad computada! Estos dos métodos mutarán el array original, lo que debería evitarse en los getters computados. Crea una copia del array original antes de llamar a estos métodos:

```diff
- return numbers.reverse()
+ return [...numbers].reverse()
```
