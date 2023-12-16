<script setup>
import SwitchComponent from './keep-alive-demos/SwitchComponent.vue'
</script>

# KeepAlive {#keepalive}

`<KeepAlive>` es un componente integrado que nos permite cachear condicionalmente las instancias de los componentes cuando dinámicamente intercambiamos entre varios componentes.

## Uso Básico {#basic-usage}

En el capítulo de Componentes Básicos, introdujimos la sintaxis de los [Componentes Dinámicos](/guide/essentials/component-basics#dynamic-components), utilizando el elemento especial `<component>`:

```vue-html
<componente :is="activeComponent" />
```

Por defecto, una instancia de un componente activo se desmontará cuando se abandone. Esto hará que se pierda cualquier estado modificado que tenga. Cuando se vuelva a mostrar este componente, se creará una nueva instancia con sólo el estado inicial.

En el ejemplo siguiente, tenemos dos componentes con estado; A contiene un contador, mientras que B contiene un mensaje sincronizado con una entrada a través de `v-model`. Prueba a actualizar el estado de uno de ellos, cámbialo y luego vuelve a él:

<SwitchComponent />

Verás que cuando vuelvas a cambiar, el estado anterior cambiado se habrá restablecido.

La creación de una nueva instancia del componente al cambiar es un comportamiento normalmente útil, pero en este caso, nos gustaría que las dos instancias del componente se conservaran incluso cuando están inactivas. Para resolver este problema, podemos envolver nuestro componente dinámico con el componente integrado `<KeepAlive>`:

```vue-html
¡<!-- ¡Los componentes inactivos se guardarán en la caché! -->
<KeepAlive>
  <componente :is="activeComponent" />
</KeepAlive>
```

Ahora, el estado será persistente a través de los cambios de componentes:

<SwitchComponent use-KeepAlive />

<div class="composition-api">

[Pruébalo en la Zona de Práctica](https://play.vuejs.org/#eNqtUsFOwzAM/RWrl4IGC+cqq2h3RFw495K12YhIk6hJi1DVf8dJSllBaAJxi+2XZz8/j0lhzHboeZIl1NadMA4sd73JKyVaozsHI9hnJqV+feJHmODY6RZS/JEuiL1uTTEXtiREnnINKFeAcgZUqtbKOqj7ruPKwe6s2VVguq4UJXEynAkDx1sjmeMYAdBGDFBLZu2uShre6ioJeaxIduAyp0KZ3oF7MxwRHWsEQmC4bXXDJWbmxpjLBiZ7DwptMUFyKCiJNP/BWUbO8gvnA+emkGKIgkKqRrRWfh+Z8MIWwpySpfbxn6wJKMGV4IuSs0UlN1HVJae7bxYvBuk+2IOIq7sLnph8P9u5DJv5VfpWWLaGqTzwZTCOM/M0IaMvBMihd04ruK+lqF/8Ajxms8EFbCiJxR8khsP6ncQosLWnWV6a/kUf2nqu75Fby04chA0iPftaYryhz6NBRLjdtajpHZTWPio=)

</div>
<div class="options-api">

[Pruébalo en la Zona de Práctica](https://play.vuejs.org/#eNqtU8tugzAQ/JUVl7RKWveMXFTIseofcHHAiawasPxArRD/3rVNSEhbpVUrIWB3x7PM7jAkuVL3veNJmlBTaaFsVraiUZ22sO0alcNedw2s7kmIPHS1ABQLQDEBAMqWvwVQzffMSQuDz1aI6VreWpPCEBtsJppx4wE1s+zmNoIBNLdOt8cIjzut8XAKq3A0NAIY/QNveFEyi8DA8kZJZjlGALQWPVSSGfNYJjVvujIJeaxItuMyo6JVzoJ9VxwRmtUCIdDfNV3NJWam5j7HpPOY8BEYkwxySiLLP1AWkbK4oHzmXOVS9FFOSM3jhFR4WTNfRslcO54nSwJKcCD4RsnZmJJNFPXJEl8t88quOuc39fCrHalsGyWcnJL62apYNoq12UQ8DLEFjCMy+kKA7Jy1XQtPlRTVqx+Jx6zXOJI1JbH4jejg3T+KbswBzXnFlz9Tjes/V/3CjWEHDsL/OYNvdCE8Wu3kLUQEhy+ljh+brFFu)

</div>

:::tip
Cuando se utiliza en [plantillas del DOM](/guide/essentials/component-basics#dom-template-parsing-caveats), debería ser referenciado como `<keep-alive>`.
:::

## Include / Exclude {#include-exclude}

Por defecto, `<KeepAlive>` almacenará en caché cualquier instancia que se encuentre dentro del componente. Podemos personalizar este comportamiento a través de las props `include` y `exclude`. Ambas props pueden ser un string delimitado por comas, una `RegExp` o un array que contenga cualquiera de los dos tipos:

```vue-html
<!-- string delimitado por comas -->
<KeepAlive include="a,b">
  <componente :is="view" />
</KeepAlive>

<!-- regex (utiliza `v-bind`) -->
<KeepAlive :include="/a|b/">
  <componente :is="view" />
</KeepAlive>

<!-- Array (utiliza `v-bind`) -->
<KeepAlive :include="['a', 'b']">
  <componente :is="view" />
</KeepAlive>
```

La verificación de la coincidencia se realiza con la opción [`name`](/api/options-misc#name) del componente, por lo que los componentes que necesiten ser cacheados condicionalmente por `KeepAlive` deben declarar explícitamente una opción `name`.

:::tip
Desde la versión 3.2.34, un componente de un solo archivo que utilice `<script setup>` inferirá automáticamente su opción `name` basándose en el nombre del archivo, eliminando la necesidad de declarar manualmente el nombre.
:::

## Instancias Máximas en Caché {#max-cached-instances}

Podemos limitar el número máximo de instancias del componente que pueden ser almacenadas en caché a través de la proposición `max`. Cuando se especifica `max`, `<KeepAlive>` se comporta como una [caché LRU](<https://en.wikipedia.org/wiki/Cache_replacement_policies#Least_recently_used_(LRU)>): si el número de instancias en caché está a punto de exceder el número máximo especificado, la instancia en caché a la que se haya accedido menos recientemente será destruida para hacer sitio a la nueva.

```vue-html
<KeepAlive :max="10">
  <component :is="activeComponent" />
</KeepAlive>
```

## Ciclo de Vida de la Instancia en Caché {#lifecycle-of-cached-instance}

Cuando se elimina una instancia de un componente del DOM pero esta forma parte de un árbol de componentes almacenado en caché por `<KeepAlive>`, pasa a un estado **desactivado** en lugar de ser desmontado. Cuando se inserta una instancia de componente en el DOM como parte de un árbol en caché, esta es **activada**.

<div class="composition-api">

Un componente kept-alive puede registrar hooks del ciclo de vida para estos dos estados utilizando [`onActivated()`](/api/composition-api-lifecycle#onactivated) y [`onDeactivated()`](/api/composition-api-lifecycle#ondeactivated):

```vue
<script setup>
import { onActivated, onDeactivated } from 'vue'

onActivated(() => {
  // llamado en el montaje inicial
  // y cada vez que se reinserta desde la caché
})

onDeactivated(() => {
  // llamado cuando se retira desde el DOM a la caché
  // y también cuando se desmonta
})
</script>
```

</div>
<div class="options-api">

El componente kept-alive puede registrar hooks del ciclo de vida para estos dos estados utilizando los hooks [`activated`](/api/options-lifecycle#activated) y [`deactivated`](/api/options-lifecycle#deactivated):

```js
export default {
  activated() {
    // llamado en el montaje inicial
    // y cada vez que se reinserta desde la caché
  },
  deactivated() {
    // llamado cuando se retira desde el DOM a la caché
    // y también cuando se desmonta
  }
}
```

</div>

Observa que:

<span class="composition-api">`onActivated`</span><span class="options-api">`activated`</span> también es llamado al momento del montaje, y <span class="composition-api">`onDeactivated`</span><span class="options-api">`deactivated`</span> al momento del desmontaje.

- Los dos hooks funcionan no sólo para el componente raíz cacheado por `<KeepAlive>`, sino también para los componentes descendientes en el árbol cacheado.

---

**Relacionado**

- [Referencia de la Api sobre `<KeepAlive>`](/api/built-in-components#keepalive)
