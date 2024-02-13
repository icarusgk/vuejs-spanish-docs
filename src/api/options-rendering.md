# Opciones: Renderizado {#options-rendering}

## template {#template}

Un modelo de cadena para el componente.

- **Tipo**

  ```ts
  interface ComponentOptions {
    template?: string
  }
  ```

- **Detalles**

  Una plantilla proporcionada a través de la opción `template` será compilada sobre la marcha en tiempo de ejecución. Sólo se admite cuando se utiliza una compilación de Vue que incluya el compilador de plantillas. El compilador de plantillas **NO** está incluido en las versiones de Vue que tienen la palabra `runtime` en sus nombres, por ejemplo `vue.runtime.esm-bundler.js`. Consulta la [guía de distribución de archivos](https://github.com/vuejs/core/tree/main/packages/vue#which-dist-file-to-use) para obtener más detalles sobre las diferentes compilaciones.

  Si la cadena empieza por `#` se utilizará como `querySelector` y utilizará el `innerHTML` del elemento seleccionado como cadena de la plantilla. Esto permite que la plantilla fuente sea creada usando elementos nativos `<template>`.

  Si la opción `render` también está presente en el mismo componente, `template` será ignorada.

  Si el componente raíz de tu aplicación no tiene una opción `template` o `render` especificada, Vue intentará utilizar el `innerHTML` del elemento montado como plantilla en su lugar.

  :::warning Nota de seguridad
  Utilice únicamente fuentes de plantillas en las que pueda confiar. No utilice contenido proporcionado por otros usuarios como tu plantilla. Consulte la [Guía de Seguridad](/guide/best-practices/security#rule-no-1-never-use-non-trusted-templates) para más detalles.
  :::

## render {#render}

Una función que devuelve mediante programación el árbol DOM virtual del componente.

- **Tipo**

  ```ts
  interface ComponentOptions {
    render?(this: ComponentPublicInstance) => VNodeChild
  }

  type VNodeChild = VNodeChildAtom | VNodeArrayChildren

  type VNodeChildAtom =
    | VNode
    | string
    | number
    | boolean
    | null
    | undefined
    | void

  type VNodeArrayChildren = (VNodeArrayChildren | VNodeChildAtom)[]
  ```

- **Detalles**

  `render` es una alternativa a las plantillas de cadena que permite aprovechar toda la potencia programática de JavaScript para declarar la salida de renderización del componente.

  Las plantillas precompiladas, por ejemplo las de los componentes de un solo archivo, se compilan en la opción `render` en el momento de la compilación. Si tanto `render` como `template` están presentes en un componente, `render` tendrá mayor prioridad.

- **Ver también**
  - [Mecanismo de Renderizado](/guide/extras/rendering-mechanism)
  - [Funciones de Renderizado y JSX](/guide/extras/render-function)

## compilerOptions {#compileroptions}

Configurar las opciones del compilador en tiempo de ejecución para la plantilla del componente.

- **Tipo**

  ```ts
  interface ComponentOptions {
    compilerOptions?: {
      isCustomElement?: (tag: string) => boolean
      whitespace?: 'condense' | 'preserve' // valor por defecto: 'condense'
      delimiters?: [string, string] // valor por defecto: ['{{', '}}']
      comments?: boolean // default: false
    }
  }
  ```

- **Detalles**

  Esta opción de configuración sólo se respeta cuando se utiliza la compilación completa (es decir, el `vue.js` independiente que puede compilar plantillas en el navegador). Éste soporta las mismas opciones que aquellas en el nivel de la aplicación [app.config.compilerOptions](/api/application#app-config-compileroptions), y tiene mayor prioridad para el componente actual.

- **Ver también** [app.config.compilerOptions](/api/application#app-config-compileroptions)

## slots<sup class="vt-badge ts"/> {#slots}

Una opción para ayudar con la inferencia de tipo cuando se utilizan slots de forma programática en funciones de renderizado. Sólo se admite en 3.3+.

- **Detalles**

  El valor en tiempo de ejecución de esta opción no se utiliza. Los tipos reales deben ser declarados a través de type casting utilizando el ayudante de tipo `SlotsType`:

  ```ts
  import { SlotsType } from 'vue'

  defineComponent({
    slots: Object as SlotsType<{
      default: { foo: string; bar: number }
      item: { data: number }
    }>,
    setup(props, { slots }) {
      expectType<
        undefined | ((scope: { foo: string; bar: number }) => any)
      >(slots.default)
      expectType<undefined | ((scope: { data: number }) => any)>(
        slots.item
      )
    }
  })
  ```
