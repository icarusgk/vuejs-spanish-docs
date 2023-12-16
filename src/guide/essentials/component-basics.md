# Fundamentos de los Componentes {#components-basics}

Los componentes nos permiten dividir la interfaz de usuario en piezas independientes y reutilizables, y pensar en cada pieza de forma aislada. Es común que una aplicación se organice en un árbol de componentes anidados:

![Árbol de Componentes](./images/components.png)

<!-- https://www.figma.com/file/qa7WHDQRWuEZNRs7iZRZSI/components -->

Esto es muy similar a cómo anidamos los elementos HTML nativos, pero Vue implementa su propio modelo de componentes que nos permite encapsular contenido y lógica personalizados en cada componente. Vue también juega muy bien con los Componentes Web nativos. Si tienes curiosidad por saber la relación entre los Componentes de Vue y los Componentes Web nativos, [lee más aquí](/guide/extras/web-components).

## Definiendo un Componente {#defining-a-component}

Cuando usamos un paso de compilación, normalmente definimos cada componente de Vue en un archivo dedicado usando la extensión `.vue` conocido como [Componente de un Solo Archivo](/guide/scaling-up/sfc) (abreviado SFC):

<div class="options-api">

```vue
<script>
export default {
  data() {
    return {
      count: 0
    }
  }
}
</script>

<template>
  <button @click="count++">Me has hecho clic {{ count }} veces.</button>
</template>
```

</div>
<div class="composition-api">

```vue
<script setup>
import { ref } from 'vue'

const count = ref(0)
</script>

<template>
  <button @click="count++">Me has hecho clic {{ count }} veces.</button>
</template>
```

</div>

Cuando no se utiliza un paso de compilación, un componente de Vue puede definirse como un objeto JavaScript simple que contiene opciones específicas de Vue:

<div class="options-api">

```js
export default {
  data() {
    return {
      count: 0
    }
  },
  template: `
    <button @click="count++">
      Me has hecho clic {{ count }} veces.
    </button>`
}
```

</div>
<div class="composition-api">

```js
import { ref } from 'vue'

export default {
  setup() {
    const count = ref(0)
    return { count }
  },
  template: `
    <button @click="count++">
      Me has hecho clic {{ count }} veces.
    </button>`
  // o `template: '#my-template-element'`
}
```

</div>

La plantilla se alinea aquí como una cadena de JavaScript, que Vue compilará sobre la marcha. También puedes utilizar un selector de ID que apunte a un elemento (normalmente elementos nativos `<template>`). Vue utilizará su contenido como fuente de la plantilla.

El ejemplo anterior define un único componente y lo exporta como la exportación por defecto de un archivo `.js`, pero puedes usar exportaciones nominales para exportar múltiples componentes desde el mismo archivo.

## Usando un Componente {#using-a-component}

:::tip
Utilizaremos la sintaxis SFC para el resto de esta guía. Los conceptos en torno a los componentes son los mismos independientemente de si está utilizando un paso de compilación o no. La sección [Ejemplos](/examples/) muestra el uso de componentes en ambos escenarios.
:::

Para usar un componente hijo, necesitamos importarlo en el componente padre. Asumiendo que colocamos nuestro componente contador dentro de un archivo llamado `ButtonCounter.vue`, el componente será expuesto como la exportación por defecto del archivo:

<div class="options-api">

```vue
<script>
import ButtonCounter from './ButtonCounter.vue'

export default {
  components: {
    ButtonCounter
  }
}
</script>

<template>
  <h1>¡Aquí hay un componente hijo!</h1>
  <ButtonCounter />
</template>
```

Para exponer el componente importado a nuestra plantilla, necesitamos [registrarlo](/guide/components/registration) con la opción `components`. El componente estará entonces disponible como una etiqueta usando la clave con la que está registrado.

</div>

<div class="composition-api">

```vue
<script setup>
import ButtonCounter from './ButtonCounter.vue'
</script>

<template>
  <h1>¡Aquí hay un componente hijo!</h1>
  <ButtonCounter />
</template>
```

Gracias a `<script setup>`, los componentes importados se ponen automáticamente a disposición de la plantilla.

</div>

También es posible registrar globalmente un componente, haciéndolo disponible para todos los componentes de una determinada aplicación sin tener que importarlo. Las ventajas y desventajas del registro global frente al local se discuten en la sección dedicada al [Registro de Componentes](/guide/components/registration).

Los componentes pueden ser reutilizados tantas veces como se quieras:

```vue-html
<h1>¡Aquí hay muchos componentes hijos!</h1>
<ButtonCounter />
<ButtonCounter />
<ButtonCounter />
```

<div class="options-api">

[Pruébalo en la Zona de Práctica](https://play.vuejs.org/#eNqVUE1LxDAQ/StjLqusNHotcfHj4l8QcontLBtsJiGdiFL6301SdrEqyEJyeG9m3ps3k3gIoXlPKFqhxi7awDtN1gUfGR4Ts6cnn4gxwj56B5tGrtgyutEEoAk/6lCPe5MGhqmwnc9KhMRjuxCwFi3UrCk/JU/uGTC6MBjGglgdbnfPGBFM/s7QJ3QHO/TfxC+UzD21d72zPItU8uQrrsWvnKsT/ZW2N2wur45BI3KKdETlFlmphZsF58j/RgdQr3UJuO8G273daVFFtlstahngxSeoNezBIUzTYgPzDGwdjk1VkYvMj4jzF0nwsyQ=)

</div>
<div class="composition-api">

[Pruébalo en la Zona de Práctica](https://play.vuejs.org/#eNqVj91KAzEQhV/lmJsqlY3eSlr8ufEVhNys6ZQGNz8kE0GWfXez2SJUsdCLuZiZM9+ZM4qnGLvPQuJBqGySjYxMXOJWe+tiSIznwhz8SyieKWGfgsOqkyfTGbDSXsmFUG9rw+Ti0DPNHavD/faVEqGv5Xr/BXOwww4mVBNPnvOVklXTtKeO8qKhkj++4lb8+fL/mCMS7TEdAy6BtDfBZ65fVgA2s+L67uZMUEC9N0s8msGaj40W7Xa91qKtgbdQ0Ha0gyOM45E+TWDrKHeNIhfMr0DTN4U0me8=)

</div>

Observa que al hacer clic en los botones, cada uno mantiene su propio "recuento" por separado. Esto se debe a que cada vez que se utiliza un componente, se crea una nueva **instancia** del mismo.

En los SFCs, se recomienda utilizar nombres de etiquetas `PascalCase` para los componentes hijos para diferenciarlos de los elementos HTML nativos. Aunque los nombres de las etiquetas HTML nativas no distinguen entre mayúsculas y minúsculas, el SFC de Vue es un formato compilado, por lo que podemos utilizar nombres de etiquetas que distinguen entre mayúsculas y minúsculas. También podemos utilizar `/>` para cerrar una etiqueta.

Si estás creando tus plantillas directamente en un DOM (por ejemplo, como el contenido de un elemento nativo `<template>`), la plantilla estará sujeto al comportamiento de análisis nativo de HTML del navegador. En estos casos, tendrá que utilizar `kebab-case` y etiquetas de cierre explícitas para los componentes:

```vue-html
<!-- si esta plantilla está escrita en el DOM -->
<button-counter></button-counter>
<button-counter></button-counter>
<button-counter></button-counter>
```

Consulta [análisis de advertencias de la plantilla del DOM](#advertencias-sobre-el-procesamiento-de-las-plantillas-del-dom) para más detalles.

## Pasando Props {#passing-props}

Si estamos construyendo un blog, probablemente necesitaremos un componente que represente una entrada del blog. Queremos que todas las entradas del blog compartan el mismo diseño visual, pero con diferente contenido. Dicho componente no será útil a menos que puedas pasarle datos, como el título y el contenido de la entrada específica que queremos mostrar. Es ahí donde entran los props.

Las Props son atributos personalizados que puedes registrar en un componente. Para pasar un título a nuestro componente de entrada de blog, debemos declararlo en la lista de props que este componente acepta, utilizando la opción de la macro <span class="options-api">[`props`](/api/options-state#props)</span><span class="composition-api">[`defineProps`](/api/sfc-script-setup#defineprops-defineemits)</span>:

<div class="options-api">

```vue
<!-- BlogPost.vue -->
<script>
export default {
  props: ['title']
}
</script>

<template>
  <h4>{{ title }}</h4>
</template>
```

Cuando se pasa un valor a un atributo prop, se convierte en una propiedad de esa instancia del componente. El valor de esa propiedad es accesible dentro de la plantilla y en el contexto `this` del componente, como cualquier otra propiedad del componente.

</div>
<div class="composition-api">

```vue
<!-- BlogPost.vue -->
<script setup>
defineProps(['title'])
</script>

<template>
  <h4>{{ title }}</h4>
</template>
```

`defineProps` es una macro en tiempo de compilación que sólo está disponible dentro de `<script setup>` y no necesita ser importada explícitamente. Las props declaradas se exponen automáticamente a la plantilla. `defineProps` también devuelve un objeto que contiene todos las props pasadas al componente, para que podamos acceder a ellas en JavaScript si es necesario:

```js
const props = defineProps(['title'])
console.log(props.title)
```

Mira también: [Escritura de las Props de Componentes](/guide/typescript/composition-api#typing-component-props) <sup class="vt-badge ts" />

Si no está usando `<script setup>`, las props deben ser declaradas usando la opción `props`, y el objeto props será pasado a `setup()` como primer argumento:

```js
export default {
  props: ['title'],
  setup(props) {
    console.log(props.title)
  }
}
```

</div>

Un componente puede tener tantas props como se quieras y, por defecto, se puede pasar cualquier valor a cualquier prop.

Una vez registrada una prop, puedes pasarle datos como un atributo personalizado, así:

```vue-html
<BlogPost title="Mi viaje con Vue" />
<BlogPost title="Blogueando con Vue" />
<BlogPost title="Por qué Vue es tan divertido" />
```

En una aplicación típica, sin embargo, es probable que tengas un array de posts en tu componente padre:

<div class="options-api">

```js
export default {
  // ...
  data() {
    return {
      posts: [
        { id: 1, title: 'Mi viaje con Vue' },
        { id: 2, title: 'Blogueando con Vue' },
        { id: 3, title: 'Por qué Vue es tan divertido' }
      ]
    }
  }
}
```

</div>
<div class="composition-api">

```js
const posts = ref([
  { id: 1, title: 'Mi viaje con Vue' },
  { id: 2, title: 'Blogueando con Vue' },
  { id: 3, title: 'Por qué Vue es tan divertido' }
])
```

</div>

Después queremos renderizar un componente para cada uno, usando `v-for`:

```vue-html
<BlogPost
  v-for="post in posts"
  :key="post.id"
  :title="post.title"
 />
```

<div class="options-api">

[Pruébalo en la Zona de Práctica](https://play.vuejs.org/#eNp9UU1rhDAU/CtDLrawVfpxklRo74We2kPtQdaoaTUJ8bmtiP+9ia6uC2VBgjOZeXnz3sCejAkPnWAx4+3eSkNJqmRjtCU817p81S2hsLpBEEYL4Q1BqoBUid9Jmosi62rC4Nm9dn4lFLXxTGAt5dG482eeUXZ1vdxbQZ1VCwKM0zr3x4KBATKPcbsDSapFjOClx5d2JtHjR1KFN9fTsfbWcXdy+CZKqcqL+vuT/r3qvQqyRatRdMrpF/nn/DNhd7iPR+v8HCDRmDoj4RHxbfyUDjeFto8p8yEh1Rw2ZV4JxN+iP96FMvest8RTTws/gdmQ8HUr7ikere+yHduu62y//y3NWG38xIOpeODyXcoE8OohGYZ5VhhHHjl83sD4B3XgyGI=)

</div>
<div class="composition-api">

[Pruébalo en la Zona de Práctica](https://play.vuejs.org/#eNp9kU9PhDAUxL/KpBfWBCH+OZEuid5N9qSHrQezFKhC27RlDSF8d1tYQBP1+N78OpN5HciD1sm54yQj1J6M0A6Wu07nTIpWK+MwwPASI0qjWkQejVbpsVHVQVl30ZJ0WQRHjwFMnpT0gPZLi32w2h2DMEAUGW5iOOEaniF66vGuOiN5j0/hajx7B4zxxt5ubIiphKz+IO828qXugw5hYRXKTnqSydcrJmk61/VF/eB4q5s3x8Pk6FJjauDO16Uye0ZCBwg5d2EkkED2wfuLlogibMOTbMpf9tMwP8jpeiMfRdM1l8Tk+/F++Y6Cl0Lyg1Ha7o7R5Bn9WwSg9X0+DPMxMI409fPP1PELlVmwdQ==)

</div>

Observa cómo podemos usar `v-bind` para pasar props dinámicos. Esto resulta especialmente útil cuando no conoces el contenido exacto que vas a renderizar con antelación.

Eso es todo lo que necesitas saber sobre los props por ahora, pero una vez que hayas terminado de leer esta página y te sientas cómodo con su contenido, te recomendamos que vuelvas más tarde para leer la guía completa sobre [Props](/guide/components/props).

## Escuchando los Eventos {#listening-to-events}

Según vayamos desarrollando nuestro componente `<BlogPost>`, algunas características pueden requerir comunicarse con el padre. Por ejemplo, podemos decidir incluir una función de accesibilidad para ampliar el texto de las entradas del blog, dejando el resto de la página en su tamaño por defecto.

En el padre, podemos soportar esta característica añadiendo una <span class="options-api">propiedad de data</span><span class="composition-api">ref</span> `postFontSize`:

<div class="options-api">

```js{6}
data() {
  return {
    posts: [
      /* ... */
    ],
    postFontSize: 1
  }
}
```

</div>
<div class="composition-api">

```js{5}
const posts = ref([
  /* ... */
])

const postFontSize = ref(1)
```

</div>

La cual se puede utilizar en la plantilla para controlar el tamaño de la fuente de todas las entradas del blog:

```vue-html{1,7}
<div :style="{ fontSize: postFontSize + 'em' }">
  <BlogPost
    v-for="post in posts"
    :key="post.id"
    :title="post.title"
   />
</div>
```

Ahora vamos a añadir un botón a la plantilla del componente `<BlogPost>`:

```vue{5}
<!-- BlogPost.vue, omitiendo <script> -->
<template>
  <div class="blog-post">
    <h4>{{ title }}</h4>
    <button>Ampliar el texto</button>
  </div>
</template>
```

El botón de momento no hace nada; queremos que al hacer clic en el botón comunique al padre que debe ampliar el texto de todas las entradas. Para resolver este problema, las instancias de componentes proporcionan un sistema de eventos personalizado. El padre puede elegir escuchar cualquier evento en la instancia del componente hijo con `v-on` o `@`, tal como lo haríamos con un evento DOM nativo:

```vue-html{3}
<BlogPost
  ...
  @enlarge-text="postFontSize += 0.1"
 />
```

Entonces el componente hijo puede emitir un evento sobre sí mismo llamando al [método **`emit`**](/api/component-instance#emit) integrado, pasando el nombre del evento:

```vue{5}
<!-- BlogPost.vue, omitiendo <script> -->
<template>
  <div class="blog-post">
    <h4>{{ title }}</h4>
    <button @click="$emit('enlarge-text')">Ampliar el texto</button>
  </div>
</template>
```

Gracias al escuchador `@enlarge-text="postFontSize += 0.1"`, el padre recibirá el evento y actualizará el valor de `postFontSize`.

<div class="options-api">

[Pruébalo en la Zona de Práctica](https://play.vuejs.org/#eNqNUsFOg0AQ/ZUJMaGNbbHqidCmmujNxMRED9IDhYWuhV0CQy0S/t1ZYIEmaiRkw8y8N/vmMZVxl6aLY8EM23ByP+Mprl3Bk1RmCPexjJ5ljhBmMgFzYemEIpiuAHAFOzXQgIVeESNUKutL4gsmMLfbBPStVFTP1Bl46E2mup4xLDKhI4CUsMR+1zFABTywYTkD5BgzG8ynEj4kkVgJnxz38Eqaut5jxvXAUCIiLqI/8TcD/m1fKhTwHHIJYSEIr+HbnqikPkqBL/yLSMs23eDooNexel8pQJaksYeMIgAn4EewcyxjtnKNCsK+zbgpXILJEnW30bCIN7ZTPcd5KDNqoWjARWufa+iyfWBlV13wYJRvJtWVJhiKGyZiL4vYHNkJO8wgaQVXi6UGr51+Ndq5LBqMvhyrH9eYGePtOVu3n3YozWSqFsBsVJmt3SzhzVaYY2nm9l82+7GX5zTGjlTM1SyNmy5SeX+7rqr2r0NdOxbFXWVXIEoBGz/m/oHIF0rB5Pz6KTV6aBOgEo7Vsn51ov4GgAAf2A==)

</div>
<div class="composition-api">

[Pruébalo en la Zona de Práctica](https://play.vuejs.org/#eNp1Uk1PwkAQ/SuTxqQYgYp6ahaiJngzITHRA/UAZQor7W7TnaK16X93th8UEuHEvPdm5s3bls5Tmo4POTq+I0yYyZTAIOXpLFAySXVGUEKGEVQQZToBl6XukXqO9XahDbXc2OsAO5FlAIEKtWJByqCBqR01WFqiBLnxYTIEkhSjD+5rAV86zxQW8C1pB+88Aaphr73rtXbNVqrtBeV9r/zYFZYHacBoiHLFykB9Xgfq1NmLVvQmf7E1OGFaeE0anAMXhEkarwhtRWIjD+AbKmKcBk4JUdvtn8+6ARcTu87hLuCf6NJpSoDDKNIZj7BtIFUTUuB0tL/HomXHcnOC18d1TF305COqeJVtcUT4Q62mtzSF2/GkE8/E8b1qh8Ljw/if8I7nOkPn9En/+Ug2GEmFi0ynZrB0azOujbfB54kki5+aqumL8bING28Yr4xh+2vePrI39CnuHmZl2TwwVJXwuG6ZdU6kFTyGsQz33HyFvH5wvvyaB80bACwgvKbrYgLVH979DQc=)

</div>

Opcionalmente podemos declarar eventos emitidos utilizando la opción macro <span class="options-api">[`emits`](/api/options-state#emits)</span><span class="composition-api">[`defineEmits`](/api/sfc-script-setup#defineprops-defineemits)</span>:

<div class="options-api">

```vue{5}
<!-- BlogPost.vue -->
<script>
export default {
  props: ['title'],
  emits: ['enlarge-text']
}
</script>
```

</div>
<div class="composition-api">

```vue{4}
<!-- BlogPost.vue -->
<script setup>
defineProps(['title'])
defineEmits(['enlarge-text'])
</script>
```

</div>

Esto documenta todos los eventos que emite un componente y opcionalmente [los valida](/guide/components/events#events-validation). También permite a Vue evitar aplicarlos implícitamente como oyentes nativos al elemento raíz del componente hijo.

<div class="composition-api">

Al igual que `defineProps`, `defineEmits` también se puede utilizar sólo en `<script setup>` y no necesita ser importado. Devuelve una función `emit` que se puede utilizar para emitir eventos en el código JavaScript:

```js
const emit = defineEmits(['enlarge-text'])

emit('enlarge-text')
```

Mira también: [Escritura de Emits del Componente](/guide/typescript/composition-api#typing-component-emits) <sup class="vt-badge ts" />

Si no estás usando `<script setup>`, puedes declarar eventos emitidos usando la opción `emits`. Puedes acceder a la función `emit` como una propiedad del contexto de configuración (pasada a `setup()` como segundo argumento):

```js
export default {
  emits: ['enlarge-text'],
  setup(props, ctx) {
    ctx.emit('enlarge-text')
  }
}
```

</div>

Eso es todo lo que necesitas saber sobre los eventos de componentes personalizados por ahora, pero una vez que hayas terminado de leer esta página y te sientas cómodo con su contenido, te recomendamos que vuelvas más tarde para leer la guía completa sobre [Eventos Personalizados](/guide/components/events).

## Distribución de Contenidos con Slots {#content-distribution-with-slots}

Al igual que con los elementos HTML, a menudo es útil poder pasar contenido a un componente, de esta manera:

```vue-html
<AlertBox>
  Ha ocurrido algo malo.
</AlertBox>
```

Lo que podría renderizar algo como:

:::danger Esto es un error para fines de demostración
Algo malo ha ocurrido.
:::

Esto puede lograrse utilizando el elemento personalizado `<slot>` de Vue:

```vue{4}
<template>
  <div class="alert-box">
    <strong>Esto es un error para fines de demostración</strong>
    <slot />
  </div>
</template>

<style scoped>
.alert-box {
  /* ... */
}
</style>
```

Como verás arriba, usamos el `<slot>` como marcador de posición donde queremos que vaya el contenido, y eso es todo. ¡Ya hemos terminado!

<div class="options-api">

[Pruébalo en la Zona de Práctica](https://play.vuejs.org/#eNpVUcFOwzAM/RUTDruwFhCaUCmThsQXcO0lbbKtIo0jx52Kpv07TreWouTynl+en52z2oWQnXqrClXGhtrA28q3XUBi2DlL/IED7Ak7WGX5RKQHq8oDVN4Oo9TYve4dwzmxDcp7bz3HAs5/LpfKyy3zuY0Atl1wmm1CXE5SQeLNX9hZPrb+ALU2cNQhWG9NNkrnLKIt89lGPahlyDTVogVAadoTNE7H+F4pnZTrGodKjUUpRyb0h+0nEdKdRL3CW7GmfNY5ZLiiMhfP/ynG0SL/OAuxwWCNMNncbVqSQyrgfrPZvCVcIxkrxFMYIKJrDZA1i8qatGl72ehLGEY6aGNkNwU8P96YWjffB8Lem/Xkvn9NR6qy+fRd14FSgopvmtQmzTT9Toq9VZdfIpa5jQ==)

</div>
<div class="composition-api">

[Pruébalo en la Zona de Práctica](https://play.vuejs.org/#eNpVUEtOwzAQvcpgFt3QBBCqUAiRisQJ2GbjxG4a4Xis8aQKqnp37PyUyqv3mZn3fBVH55JLr0Umcl9T6xi85t4VpW07h8RwNJr4Cwc4EXawS9KFiGO70ubpNBcmAmDdOSNZR8T5Yg0IoOQf7DSfW9tAJRWcpXPaapWM1nVt8ObpukY8ie29GHNzAiBX7QVqI73/LIWMzn2FQylGMcieCW1TfBMhPYSoE5zFitLVZ5BhQnkadt6nGKt5/jMafI1Oq8Ak6zW4xrEaDVIGj4fD4SPiCknpQLy4ATyaVgFptVH2JFXb+wze3DDSTioV/iaD1+eZqWT92xD2Vu2X7af3+IJ6G7/UToVigpJnTzwTO42eWDnELsTtH/wUqH4=)

</div>

Esto es todo lo que necesitas saber sobre las ranuras por ahora, pero una vez que hayas terminado de leer esta página y te sientas cómodo con su contenido, te recomendamos que vuelvas más tarde para leer la guía completa sobre [Slots](/guide/components/slots).

## Componentes Dinámicos {#dynamic-components}

A veces, es útil cambiar dinámicamente entre componentes, como en una interfaz con pestañas:

<div class="options-api">

[Abrir ejemplo en la Zona de Práctica](https://play.vuejs.org/#eNqNVE2PmzAQ/Ssj9kArLSHbrXpwk1X31mMPvS17cIxJrICNbJMmivLfO/7AEG2jRiDkefP85sNmztlr3y8OA89ItjJMi96+VFJ0vdIWfqqOQ6NVB/midIYj5sn9Sxlrkt9b14RXzXbiMElEO5IAKsmPnljzhg6thbNDmcLdkktrSADAJ/IYlj5MXEc9Z1w8VFNLP30ed2luBy1HC4UHrVH2N90QyJ1kHnUALN1gtLeIQu6juEUMkb8H5sXHqiS+qzK1Cw3Lu76llqMFsKrFAVhLjVlXWc07VWUeR89msFbhhhAWDkWjNJIwPgjp06iy5CV7fgrOOTgKv+XoKIIgpnoGyiymSmZ1wnq9dqJweZ8p/GCtYHtUmBMdLXFitgDnc9ju68b0yxDO1WzRTEcFRLiUJsEqSw3wwi+rMpFDj0psEq5W5ax1aBp7at1y4foWzq5R0hYN7UR7ImCoNIXhWjTfnW+jdM01gaf+CEa1ooYHzvnMVWhaiwEP90t/9HBP61rILQJL3POMHw93VG+FLKzqUYx3c2yjsOaOwNeRO2B8zKHlzBKQWJNH1YHrplV/iiMBOliFILYNK5mOKdSTMviGCTyNojFdTKBoeWNT3s8f/Vpsd7cIV61gjHkXnotR6OqVkJbrQKdsv9VqkDWBh2bpnn8VXaDcHPexE4wFzsojO9eDUOSVPF+65wN/EW7sHRsi5XaFqaexn+EH9Xcpe8zG2eWG3O0/NVzUaeJMk+jGhUXlNPXulw5j8w7t2bi8X32cuf/Vv/wF/SL98A==)

</div>
<div class="composition-api">

[Abrir ejemplo en la Zona de Práctica](https://play.vuejs.org/#eNqNVMGOmzAQ/ZURe2BXCiHbrXpwk1X31mMPvS1V5RiTWAEb2SZNhPLvHdvggLZRE6TIM/P8/N5gpk/e2nZ57HhCkrVhWrQWDLdd+1pI0bRKW/iuGg6VVg2ky9wFDp7G8g9lrIl1H80Bb5rtxfFKMcRzUA+aV3AZQKEEhWRKGgus05pL+5NuYeNwj6mTkT4VckRYujVY63GT17twC6/Fr4YjC3kp5DoPNtEgBpY3bU0txwhgXYojsJoasymSkjeqSHweK9vOWoUbXIC/Y1YpjaDH3wt39hMI6TUUSYSQAz8jArPT5Mj+nmIhC6zpAu1TZlEhmXndbBwpXH5NGL6xWrADMsyaMj1lkAzQ92E7mvYe8nCcM24xZApbL5ECiHCSnP73KyseGnvh6V/XedwS2pVjv3C1ziddxNDYc+2WS9fC8E4qJW1W0UbUZwKGSpMZrkX11dW2SpdcE3huT2BULUp44JxPSpmmpegMgU/tyadbWpZC7jCxwj0v+OfTDdU7ITOrWiTjzTS3Vei8IfB5xHZ4PmqoObMEJHryWXXkuqrVn+xEgHZWYRKbh06uLyv4iQq+oIDnkXSQiwKymlc26n75WNdit78FmLWCMeZL+GKMwlKrhLRcBzhlh51WnSwJPFQr9/zLdIZ007w/O6bR4MQe2bseBJMzer5yzwf8MtzbOzYMkNsOY0+HfoZv1d+lZJGMg8fNqdsfbbio4b77uRVv7I0Li8xxZN1PHWbeHdyTWXc/+zgw/8t/+QsROe9h)

</div>

Lo anterior es posible gracias al elemento `<component>` de Vue con el atributo especial `is`:

<div class="options-api">

```vue-html
<!-- El componente cambia cuando cambia el currentTab -->
<component :is="currentTab"></component>
```

</div>
<div class="composition-api">

```vue-html
<!-- El componente cambia cuando cambia el currentTab -->
<component :is="tabs[currentTab]"></component>
```

</div>

En el ejemplo anterior, el valor pasado a `:is` puede contener

- la cadena de nombre de un componente registrado, O
- el objeto componente actual importado

También puedes utilizar el atributo "is" para crear elementos HTML normales.

Cuando se cambia entre varios componentes con `<component :is="...">`, un componente será desmontado cuando se cambie de lugar. Podemos forzar que los componentes inactivos permanezcan "vivos" con el componente integrado [`<KeepAlive>`](/guide/built-ins/keep-alive).

## Advertencias sobre el Procesamiento de las Plantillas del DOM {#dom-template-parsing-caveats}

Si estás escribiendo tus plantillas de Vue directamente en el DOM, Vue tendrá que recuperar la cadena de la plantilla desde el DOM. Esto conduce a algunas advertencias debido al comportamiento de análisis nativo de HTML de los navegadores.

:::tip
Debe tenerse en cuenta que las limitaciones discutidas a continuación sólo se aplican si estás escribiendo tus plantillas directamente en el DOM. NO se aplican si está usando plantillas de cadena de las siguientes fuentes:

- Componentes de un Solo Archivo
- Cadenas de plantillas alineadas (por ejemplo, `template: '...'`)
- `<script type="text/x-template">`
  :::

### Insensibilidad a las Mayúsculas y Minúsculas {#case-insensitivity}

Las etiquetas HTML y los nombres de atributos no distinguen entre mayúsculas y minúsculas, por lo que los navegadores interpretarán cualquier carácter en mayúscula como en minúscula. Esto significa que cuando se utilizan plantillas en el DOM, los nombres de componentes en PascalCase y los nombres de props en camelCase o los nombres de eventos `v-on` deben utilizar sus equivalentes en kebab-cased (delimitados por guiones):

```js
// camelCase en JavaScript
const BlogPost = {
  props: ['postTitle'],
  emits: ['updatePost'],
  template: `
    <h3>{{ postTitle }}</h3>
  `
}
```

```vue-html
<!-- kebab-case en el HTML -->
<blog-post post-title="¡hola!" @update-post="onUpdatePost"></blog-post>
```

### Etiquetas de Autocierre {#self-closing-tags}

Ya hemos utilizado etiquetas de autocierre para los componentes en los ejemplos de código anteriores:

```vue-html
<MyComponent />
```

Esto es debido a que el parser de plantillas de Vue respeta `/>` como una indicación para terminar cualquier etiqueta, independientemente de su tipo.

Sin embargo, en las plantillas del DOM, debemos incluir siempre etiquetas de cierre explícitas:

```vue-html
<my-component></my-component>
```

Esto es debido a que la especificación HTML sólo permite omitir las etiquetas de cierre en [unos pocos elementos específicos](https://html.spec.whatwg.org/multipage/syntax.html#void-elements), siendo los más comunes `<input>` y `<img>`. Para los demás elementos, si omites la etiqueta de cierre, el analizador nativo de HTML pensará que nunca has terminado la etiqueta de apertura. Por ejemplo, el siguiente fragmento:

```vue-html
<my-component /> <!-- pretendemos cerrar la etiqueta aquí... -->
<span>hola</span>
```

se interpretará como:

```vue-html
<my-component>
  <span>hola</span>
</my-component> <!-- pero el navegador lo cerrará aquí. -->
```

### Restricciones para la Colocación de Elementos {#element-placement-restrictions}

Algunos elementos HTML, como `<ul>`, `<ol>`, `<table>` y `<select>` tienen restricciones sobre qué elementos pueden aparecer dentro de ellos, y algunos elementos como `<li>`, `<tr>` y `<option>` solo pueden aparecer dentro de ciertos otros elementos.

Esto dará lugar a problemas cuando se utilicen componentes con elementos que tengan dichas restricciones. Por ejemplo:

```vue-html
<table>
  <blog-post-row></blog-post-row>
</table>
```

El componente personalizado `<blog-post-row>` será captado como contenido inválido, causando errores en la eventual salida renderizada. Podemos utilizar el [atributo especial `is`](/api/built-in-special-attributes#is) como solución:

```vue-html
<table>
  <tr is="vue:blog-post-row"></tr>
</table>
```

:::tip
Cuando se utiliza en elementos HTML nativos, el valor de `is` debe llevar el prefijo `vue:` para ser interpretado como un componente Vue. Esto es necesario para evitar la confusión con los [elementos integrados personalizados](https://html.spec.whatwg.org/multipage/custom-elements#custom-elements-customized-builtin-example) nativos.
:::

¡Enhorabuena! Por ahora, eso es todo lo que necesitas saber sobre el análisis de advertencias de la plantilla del DOM y, en definitiva, el final de los _Esenciales_ de Vue. Todavía hay más que aprender, pero primero, recomendamos tomar un descanso para que juegues con Vue tú mismo, construyas algo divertido o revises algunos de los [Ejemplos](/examples/) si aún no lo has hecho.

Una vez que te sientas cómodo con los conocimientos que acabas de digerir, sigue con la guía para aprender más sobre los componentes en profundidad.
