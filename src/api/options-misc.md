# Opciones: Misceláneas {#options-misc}

## name {#name}

Declara explícitamente un nombre de visualización para el componente.

- **Tipo**

  ```ts
  interface ComponentOptions {
    name?: string
  }
  ```

- **Detalles**

  El nombre de un componente se utiliza para lo siguiente

  - Autorreferencia recursiva en la propia plantilla del componente
  - Visualización en el árbol de inspección de componentes de Vue DevTools
  - Visualización en las trazas de advertencia del componente

  Cuando se utilizan componentes de un solo archivo, el componente ya infiere su propio nombre a partir del nombre del archivo. Por ejemplo, un archivo llamado `MiComponente.vue` tendrá el nombre de visualización inferido "MiComponente".

  Otro caso es que cuando un componente se registra globalmente con [`app.component`](/api/application#app-component), el ID global se establece automáticamente como su nombre.

  La opción `name` le permite anular el nombre inferido, o proporcionar explícitamente un nombre cuando no se puede inferir ningún nombre (por ejemplo, cuando no se utilizan herramientas de compilación, o un componente no SFC).

  Hay un caso en el que `nombre` es explícitamente necesario: cuando se compara con componentes almacenables en caché en [`<KeepAlive>`](/guide/built-ins/keep-alive) a través de sus propiedades `include / exclude`.

  :::consejo
  Desde la versión 3.2.34, un componente de un solo archivo que utilice `<script setup>` inferirá automáticamente su opción `name` basándose en el nombre del archivo, eliminando la necesidad de declarar manualmente el nombre incluso cuando se utilice con `<KeepAlive>`.
  :::

## inheritAttrs {#inheritattrs}

Controla si el comportamiento por defecto de los atributos de los componentes debe ser activado.

- **Tipo**

  ```ts
  interface ComponentOptions {
    inheritAttrs?: boolean // valor por defecto: true
  }
  ```

- **Detalles**

  Por defecto, las vinculaciones de atributos de ámbito padre que no se reconocen como props se "traspasarán". Esto significa que cuando tenemos un componente de una sola raíz, estos enlaces se aplicarán al elemento raíz del componente hijo como atributos HTML normales. Cuando se crea un componente que envuelve a un elemento de destino o a otro componente, puede que este no sea siempre el comportamiento deseado. Estableciendo `inheritAttrs` a `false`, este comportamiento por defecto puede ser desactivado. Los atributos están disponibles a través de la propiedad de instancia `$attrs` y pueden ser vinculados explícitamente a un elemento no raíz utilizando `v-bind`.

- **Ejemplo**

  <div class="options-api">

  ```vue
  <script>
  export default {
    inheritAttrs: false,
    props: ['label', 'value'],
    emits: ['input']
  }
  </script>

  <template>
    <label>
      {{ label }}
      <input
        v-bind="$attrs"
        v-bind:value="value"
        v-on:input="$emit('input', $event.target.value)"
      />
    </label>
  </template>
  ```

  </div>
  <div class="composition-api">

  Cuando se declara esta opción en un componente que utiliza `<script setup>`, puedes utilizar el macro [`defineOptions`](/api/sfc-script-setup#defineoptions):

  ```vue
  <script setup>
  defineProps(['label', 'value'])
  defineEmits(['input'])
  defineOptions({
    inheritAttrs: false
  })
  </script>

  <template>
    <label>
      {{ label }}
      <input
        v-bind="$attrs"
        v-bind:value="value"
        v-on:input="$emit('input', $event.target.value)"
      />
    </label>
  </template>
  ```

  Desde la versión 3.3 puedes usar también `defineOptions` directamente en `<script setup>`:

  ```vue
  <script setup>
  defineProps(['label', 'value'])
  defineEmits(['input'])
  defineOptions({ inheritAttrs: false })
  </script>

  <template>
    <label>
      {{ label }}
      <input
        v-bind="$attrs"
        v-bind:value="value"
        v-on:input="$emit('input', $event.target.value)"
      />
    </label>
  </template>
  ```

  </div>

- **Véase también** [Attributos Fallthrough](/guide/components/attrs)

## components {#components}

Un objeto que registra los componentes que se pondrán a disposición de la instancia del componente.

- **Tipo**

  ```ts
  interface ComponentOptions {
    components?: { [key: string]: Component }
  }
  ```

- **Ejemplo**

  ```js
  import Foo from './Foo.vue'
  import Bar from './Bar.vue'

  export default {
    components: {
      // abreviatura
      Foo,
      // registra bajo un nombre distinto
      RenamedBar: Bar
    }
  }
  ```

- **Véase también** [Registro de Componentes](/guide/components/registration)

## directives {#directives}

Un objeto que registra las directivas que se pondrán a disposición de la instancia del componente.

- **Tipo**

  ```ts
  interface ComponentOptions {
    directives?: { [key: string]: Directive }
  }
  ```

- **Ejemplo**

  ```js
  export default {
    directives: {
      // activa v-focus en la plantilla
      focus: {
        mounted(el) {
          el.focus()
        }
      }
    }
  }
  ```

  ```vue-html
  <input v-focus>
  ```

  Un hash de directivas que se pondrán a disposición de la instancia del componente.

- **Véase también** [Directivas personalizadas](/guide/reusability/custom-directives)
