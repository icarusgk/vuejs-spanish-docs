# Tipos de utilidades {#utility-types}

:::info Información
En esta página sólo se enumeran algunos tipos de utilidad comúnmente utilizados que pueden necesitar una explicación para su uso. Para obtener una lista completa de los tipos exportados, consulta el [código fuente](https://github.com/vuejs/core/blob/main/packages/runtime-core/src/index.ts#L131).
:::

## PropType\<T> {#proptype-t}

Se usa para anotar una prop con tipos más avanzados cuando se usan declaraciones de props en tiempo de ejecución.

- **Ejemplo**

  ```ts
  import { PropType } from 'vue'

  interface Book {
    title: string
    author: string
    year: number
  }

  export default {
    props: {
      book: {
        // provee un tipo más específico a `Object`.
        type: Object as PropType<Book>,
        required: true
      }
    }
  }
  ```

- **Véase también** [Guía - Escritura de las Props de Componentes](/guide/typescript/options-api#typing-component-props)

## MaybeRef\<T> {#mayberef}

Alias para `T | Ref<T>`. Útil para anotar argumentos de [Composables](/guide/reusability/composables.html).

- Sólo se admite en 3.3+.

## MaybeRefOrGetter\<T> {#maybereforgetter}

Alias para `T | Ref<T> | (() => T)`. Útil para anotar argumentos de [Composables](/guide/reusability/composables.html).

- Sólo se admite en 3.3+.

## ExtractPropTypes\<T> {#extractproptypes}

Extrae tipos de props de un objeto de opciones de props en tiempo de ejecución. Los tipos extraídos son de cara interna - es decir, las props resueltas recibidas por el componente. Esto significa que las props booleanas y las props con valores por defecto siempre se definen, incluso si no son necesarias.

Para extraer props públicas, es decir, props que el padre puede pasar, utiliza [`ExtractPublicPropTypes`](#extractpublicproptypes).

- **Ejemplo**

  ```ts
  const propsOptions = {
    foo: String,
    bar: Boolean,
    baz: {
      type: Number,
      required: true
    },
    qux: {
      type: Number,
      default: 1
    }
  } as const

  type Props = ExtractPropTypes<typeof propsOptions>
  // {
  //   foo?: string,
  //   bar: boolean,
  //   baz: number,
  //   qux: number
  // }
  ```

## ExtractPublicPropTypes\<T> {#extractpublicproptypes}

Extrae tipos de props de un objeto de opciones de props en tiempo de ejecución. Los tipos extraídos son de cara pública, es decir, las props que el padre tiene permitido pasar.

- **Ejemplo**

  ```ts
  const propsOptions = {
    foo: String,
    bar: Boolean,
    baz: {
      type: Number,
      required: true
    },
    qux: {
      type: Number,
      default: 1
    }
  } as const

  type Props = ExtractPublicPropTypes<typeof propsOptions>
  // {
  //   foo?: string,
  //   bar?: boolean,
  //   baz: number,
  //   qux?: number
  // }
  ```

## ComponentCustomProperties {#componentcustomproperties}

Se utiliza para aumentar el tipo de instancia del componente para admitir propiedades globales personalizadas.

- **Ejemplo**

  ```ts
  import axios from 'axios'

  declare module 'vue' {
    interface ComponentCustomProperties {
      $http: typeof axios
      $translate: (key: string) => string
    }
  }
  ```

  :::tip
  Los aumentos deben colocarse en un archivo de módulo `.ts` o `.d.ts`. Consulta [Ubicación del Aumento de Tipo](/guide/typescript/options-api#augmenting-global-properties) para obtener más detalles.
  :::

- **Véase también** [Guía - Aumento de las Propiedades Globales](/guide/typescript/options-api#augmenting-global-properties)

## ComponentCustomOptions {#componentcustomoptions}

Se utiliza para aumentar el tipo de opciones del componente para admitir opciones personalizadas.

- **Ejemplo**

  ```ts
  import { Route } from 'vue-router'

  declare module 'vue' {
    interface ComponentCustomOptions {
      beforeRouteEnter?(to: any, from: any, next: () => void): void
    }
  }
  ```

  :::tip
  Los aumentos deben colocarse en un archivo de módulo `.ts` o `.d.ts`. Consulta [Ubicación del Aumento de Tipo](/guide/typescript/options-api#augmenting-global-properties) para obtener más detalles.
  :::

- **Véase también** [Guía - Aumento de las Opciones Personalizadas](/guide/typescript/options-api#augmenting-custom-options)

## ComponentCustomProps {#componentcustomprops}

Se utiliza para aumentar las props TSX permitidas para usar props no declaradas en elementos TSX.

- **Ejemplo**

  ```ts
  declare module 'vue' {
    interface ComponentCustomProps {
      hello?: string
    }
  }

  export {}
  ```

  ```tsx
  // ahora funciona incluso si hello no es una prop declarada
  <MyComponent hello="world" />
  ```

  :::tip
  Los aumentos deben colocarse en un archivo de módulo `.ts` o `.d.ts`. Consulta [Ubicación del Aumento de Tipo](/guide/typescript/options-api#augmenting-global-properties) para obtener más detalles.
  :::

## CSSProperties {#cssproperties}

Se utiliza para aumentar los valores permitidos en los enlaces de propiedades de estilo.

- **Ejemplo**

  Permitir cualquier propiedad CSS personalizada

  ```ts
  declare module 'vue' {
    interface CSSProperties {
      [key: `--${string}`]: string
    }
  }
  ```

  ```tsx
  <div style={ { '--bg-color': 'blue' } }>
  ```

  ```html
  <div :style="{ '--bg-color': 'blue' }"></div>
  ```

:::tip
Los aumentos deben colocarse en un archivo de módulo `.ts` o `.d.ts`. Consulta [Ubicación del Aumento de Tipo](/guide/typescript/options-api#augmenting-global-properties) para obtener más detalles.
:::

:::info Véase también
Las etiquetas `<style>` de SFC admiten la vinculación de valores CSS al estado de los componentes dinámicos mediante la función `v-bind CSS`. Esto permite propiedades personalizadas sin aumento de tipo.

- [v-bind() en CSS](/api/sfc-css-features#v-bind-in-css)
  :::
