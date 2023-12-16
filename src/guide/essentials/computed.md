# Propiedades Computadas {#computed-properties}

<div class="options-api">
  <VueSchoolLink href="https://vueschool.io/lessons/computed-properties-in-vue-3" title="Lección gratuita de Propiedades Computadas de Vue.js"/>
</div>

<div class="composition-api">
  <VueSchoolLink href="https://vueschool.io/lessons/vue-fundamentals-capi-computed-properties-in-vue-with-the-composition-api" title="Lección gratuita de Propiedades Computadas de Vue.js"/>
</div>

## Ejemplo Básico {#basic-example}

Las expresiones en las plantillas son muy convenientes, pero están pensadas para operaciones simples. Poner demasiada lógica en tus plantillas puede hacerlas infladas y difíciles de mantener. Por ejemplo, si tenemos un objeto con un array anidado:

<div class="options-api">

```js
export default {
  data() {
    return {
      author: {
        name: 'John Doe',
        books: [
          'Vue 2 - Guía Avanzada',
          'Vue 3 - Guía Básica',
          'Vue 4 - El Misterio'
        ]
      }
    }
  }
}
```

</div>
<div class="composition-api">

```js
const author = reactive({
  name: 'John Doe',
  books: [
    'Vue 2 - Guía Avanzada',
    'Vue 3 - Guía Básica',
    'Vue 4 - El Misterio'
  ]
})
```

</div>

Y queremos mostrar diferentes mensajes dependiendo de si `author` ya tiene algunos libros o no:

```vue-html
<p>Ha publicado libros:</p>
<span>{{ author.books.length > 0 ? 'Sí' : 'No' }}</span>
```

Llegados a este punto, la plantilla se está volviendo un poco desordenada. Tenemos que mirarla un segundo antes de darnos cuenta de que realiza un cálculo en función de `author.books`. Y lo que es más importante, probablemente no queramos repetirnos si necesitamos incluir este cálculo en la plantilla más de una vez.

Por eso, para una lógica compleja que incluya datos reactivos, se recomienda utilizar una **propiedad computada**. Aquí está el mismo ejemplo, refactorizado:

<div class="options-api">

```js
export default {
  data() {
    return {
      author: {
        name: 'John Doe',
        books: [
          'Vue 2 - Guía Avanzada',
          'Vue 3 - Guía Básica',
          'Vue 4 - El Misterio'
        ]
      }
    }
  },
  computed: {
    // un getter computado
    publishedBooksMessage() {
      // `this` apunta a la instancia del componente
      return this.author.books.length > 0 ? 'Sí' : 'No'
    }
  }
}
```

```vue-html
<p>Ha publicado libros:</p>
<span>{{ publishedBooksMessage }}</span>
```

[Pruébalo en la Zona de Práctica](https://play.vuejs.org/#eNqFkN1KxDAQhV/l0JsqaFfUq1IquwiKsF6JINaLbDNui20S8rO4lL676c82eCFCIDOZMzkzXxetlUoOjqI0ykypa2XzQtC3ktqC0ydzjUVXCIAzy87OpxjQZJ0WpwxgzlZSp+EBEKylFPGTrATuJcUXobST8sukeA8vQPzqCNe4xJofmCiJ48HV/FfbLLrxog0zdfmn4tYrXirC9mgs6WMcBB+nsJ+C8erHH0rZKmeJL0sot2tqUxHfDONuyRi2p4BggWCr2iQTgGTcLGlI7G2FHFe4Q/xGJoYn8SznQSbTQviTrRboPrHUqoZZ8hmQqfyRmTDFTC1bqalsFBN5183o/3NG33uvoWUwXYyi/gdTEpwK)

Aquí hemos declarado una propiedad computada `publishedBooksMessage`.

Intenta cambiar el valor del array `books` en la aplicación `data` y verás cómo `publishedBooksMessage` cambia en consecuencia.

Puedes enlazar datos a las propiedades computadas en las plantillas como si se tratara de una propiedad normal. Vue es consciente de que `this.publishedBooksMessage` depende de `this.author.books`, por lo que actualizará cualquier enlace que dependa de `this.publishedBooksMessage` cuando cambie `this.author.books`.

Mira también: [Escritura de Propiedades Computadas](/guide/typescript/options-api#typing-computed-properties) <sup class="vt-badge ts" />

</div>

<div class="composition-api">

```vue
<script setup>
import { reactive, computed } from 'vue'

const author = reactive({
  name: 'John Doe',
  books: [
    'Vue 2 - Guía Avanzada',
    'Vue 3 - Guía Básica',
    'Vue 4 - El Misterio'
  ]
})

// una ref computada
const publishedBooksMessage = computed(() => {
  return author.books.length > 0 ? 'Sí' : 'No'
})
</script>

<template>
  <p>Ha publicado libros:</p>
  <span>{{ publishedBooksMessage }}</span>
</template>
```

[Pruébalo en la Zona de Práctica](https://play.vuejs.org/#eNp1kE9Lw0AQxb/KI5dtoTainkoaaREUoZ5EEONhm0ybYLO77J9CCfnuzta0vdjbzr6Zeb95XbIwZroPlMySzJW2MR6OfDB5oZrWaOvRwZIsfbOnCUrdmuCpQo+N1S0ET4pCFarUynnI4GttMT9PjLpCAUq2NIN41bXCkyYxiZ9rrX/cDF/xDYiPQLjDDRbVXqqSHZ5DUw2tg3zP8lK6pvxHe2DtvSasDs6TPTAT8F2ofhzh0hTygm5pc+I1Yb1rXE3VMsKsyDm5JcY/9Y5GY8xzHI+wnIpVw4nTI/10R2rra+S4xSPEJzkBvvNNs310ztK/RDlLLjy1Zic9cQVkJn+R7gIwxJGlMXiWnZEq77orhH3Pq2NH9DjvTfpfSBSbmA==)

Aquí hemos declarado la propiedad computada `publishedBooksMessage`. La función `computed()` espera que se le pase una función getter, y el valor devuelto es una **ref computada**. Al igual que con las refs normales, se puede acceder al resultado calculado como `publishedBooksMessage.value`. Las refs computadas también se desenvuelven automáticamente en las plantillas, por lo que se puede hacer referencia a ellas sin `.value` en las expresiones de las plantillas.

Una propiedad computada rastrea automáticamente sus dependencias reactivas. Vue es consciente de que el cálculo de `publishedBooksMessage` depende de `author.books`, por lo que actualizará cualquier enlace que dependa de `publishedBooksMessage` cuando cambie `author.books`.

Véase también: [Escritura de computed()](/guide/typescript/composition-api#typing-computed) <sup class="vt-badge ts" />

</div>

## Almacenamiento en Caché Computado vs. Métodos {#computed-caching-vs-methods}

Te habrás dado cuenta de que podemos conseguir el mismo resultado invocando un método en la expresión:

```vue-html
<p>{{ calculateBooksMessage() }}</p>
```

<div class="options-api">

```js
// en un componente
methods: {
  calculateBooksMessage() {
    return this.author.books.length > 0 ? 'Sí' : 'No'
  }
}
```

</div>

<div class="composition-api">

```js
// en un componente
function calculateBooksMessage() {
  return author.books.length > 0 ? 'Sí' : 'No'
}
```

</div>

En lugar de una propiedad computada, podemos definir la misma función como un método. Para el resultado final, los dos enfoques son de hecho exactamente lo mismo. Sin embargo, la diferencia es que **las propiedades computadas se almacenan en caché en función de sus dependencias reactivas.** Una propiedad computada sólo se reevaluará cuando alguna de sus dependencias reactivas haya cambiado. Esto significa que mientras `author.books` no haya cambiado, un acceso múltiple a `publishedBooksMessage` devolverá inmediatamente el resultado calculado anteriormente sin tener que volver a ejecutar la función getter.

Esto también significa que la siguiente propiedad calculada nunca se actualizará, porque `Date.now()` no es una dependencia reactiva:

<div class="options-api">

```js
computed: {
  now() {
    return Date.now()
  }
}
```

</div>

<div class="composition-api">

```js
const now = computed(() => Date.now())
```

</div>

En comparación, una invocación a un método ejecutará **siempre** la función cada vez que se produzca una nueva renderización.

¿Por qué necesitamos el almacenamiento en caché? Imagina que tenemos una costosa propiedad computada `lista`, que requiere recorrer un enorme array y hacer muchos cálculos. Luego podemos tener otras propiedades computadas que a su vez dependen de `list`. Sin la caché, estaríamos ejecutando el getter de `list` muchas más veces de las necesarias. En los casos en los que no quieras almacenar en caché, en su lugar utiliza una llamada a un método.

## "Escribible" Computado {#writable-computed}

Las propiedades computadas son, por defecto, de tipo getter. Si intentas asignar un nuevo valor a una propiedad computada, recibirás una advertencia en tiempo de ejecución. En los raros casos en los que necesites una propiedad computada "escribible", puedes crear una proporcionando tanto un getter como un setter:

<div class="options-api">

```js
export default {
  data() {
    return {
      firstName: 'John',
      lastName: 'Doe'
    }
  },
  computed: {
    fullName: {
      // getter
      get() {
        return this.firstName + ' ' + this.lastName
      },
      // setter
      set(newValue) {
        // Nota: aquí utilizamos la sintaxis de asignación de desestructuración.
        ;[this.firstName, this.lastName] = newValue.split(' ')
      }
    }
  }
}
```

Ahora cuando ejecutes `this.fullName = 'John Doe'`, se invocará el setter y se actualizarán `this.firstName` y `this.lastName` en consecuencia.

</div>

<div class="composition-api">

```vue
<script setup>
import { ref, computed } from 'vue'

const firstName = ref('John')
const lastName = ref('Doe')

const fullName = computed({
  // getter
  get() {
    return firstName.value + ' ' + lastName.value
  },
  // setter
  set(newValue) {
    // Nota: aquí utilizamos la sintaxis de asignación de desestructuración.
    ;[firstName.value, lastName.value] = newValue.split(' ')
  }
})
</script>
```

Ahora cuando ejecutes `fullName.value = 'John Doe'`, el setter será invocado y `firstName` y `lastName` serán actualizados en consecuencia.

</div>

## Mejores Prácticas {#best-practices}

### Los getters deben estar libres de efectos secundarios {#getters-should-be-side-effect-free}

Es importante recordar que las funciones getter computadas sólo deben realizar cálculos puros y estar libres de efectos secundarios. Por ejemplo, no hagas peticiones asíncronas ni mutes el DOM dentro de un getter computado. Piensa en una propiedad computada como una descripción declarativa de cómo derivar un valor basado en otros valores: su única responsabilidad debe ser calcular y devolver ese valor. Más adelante en la guía discutiremos cómo podemos realizar efectos secundarios en reacción a los cambios de estado con los [watchers](./watchers).

### Evitar la mutación del valor computado {#avoid-mutating-computed-value}

El valor devuelto de una propiedad computada es un estado derivado. Piensa en ello como una instantánea temporal: cada vez que el estado fuente cambia, se crea una nueva instantánea. No tiene sentido mutar una instantánea, por lo que un valor de retorno computado debe ser tratado como de sólo lectura y nunca ser mutado; en su lugar, actualizar el estado fuente del que depende para desencadenar nuevos cálculos.
