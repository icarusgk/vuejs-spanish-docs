# Composition API: <br>Inyección de Dependencias {#composition-api-dependency-injection}

## provide() {#provide}

Proporciona un valor que puede ser inyectado por componentes descendientes.

- **Tipo**

  ```ts
  function provide<T>(key: InjectionKey<T> | string, value: T): void
  ```

- **Detalles**

  `provide()` toma dos argumentos: la clave, que puede ser una cadena de texto o un symbol, y el valor a inyectar.

  Cuando se utiliza TypeScript, la clave puede ser un symbol convertido como `InjectionKey` - un tipo de utilidad proporcionado por Vue que extiende `Symbol`, que se puede utilizar para sincronizar el tipo de valor entre `provide()` e `inject()`.

  Al igual que las APIs de registro de los hooks de ciclo de vida, `provide()` debe ser llamado de forma sincrónica durante la fase `setup()` de un componente.

- **Ejemplo**

  ```vue
  <script setup>
  import { ref, provide } from 'vue'
  import { countSymbol } from './injectionSymbols'

  // proporcionar un valor estático
  provide('path', '/project/')

  // proporcionar un valor reactivo
  const count = ref(0)
  provide('count', count)

  // proporcionar con claves Symbol
  provide(countSymbol, count)
  </script>
  ```

- **Véase también**:
  - [Guía - Provide / Inject](/guide/components/provide-inject)
  - [Guía - Escritura de Provide / Inject](/guide/typescript/composition-api#typing-provide-inject)

## inject() {#inject}

Inyecta un valor proporcionado por un componente de nivel superior o por la aplicación (a través de `app.provide()`).

- **Tipo**

  ```ts
  // sin valor predeterminado
  function inject<T>(key: InjectionKey<T> | string): T | undefined

  // con valor predeterminado
  function inject<T>(key: InjectionKey<T> | string, defaultValue: T): T

  // con fábrica
  function inject<T>(
    key: InjectionKey<T> | string,
    defaultValue: () => T,
    treatDefaultAsFactory: true
  ): T
  ```

- **Detalles**

  El primer argumento es la clave de inyección. Vue recorrerá la estructura padre para ubicar un valor proporcionado con una clave que coincida. Si varios componentes de la estructura padre proporcionan la misma clave, el más cercano al componente que inyecta reemplazará a los que están más arriba en la cadena. Si no se encuentra un valor con una clave que coincida, `inject()` devuelve `undefined` a menos que se proporcione un valor predeterminado.

  El segundo argumento es opcional y es el valor predeterminado que se utilizará cuando no se encuentre ningún valor que coincida. También puede ser una función de fábrica para devolver valores que son costosos de crear. Si el valor predeterminado es una función, entonces se debe pasar `false` como tercer argumento para indicar que la función debe utilizarse como el valor en lugar de la función de fábrica.

  Al igual que las APIs de registro de los hooks de ciclo de vida, `inject()` debe ser llamado de manera sincrónica durante la fase `setup()` de un componente.

  Cuando se utiliza TypeScript, la clave puede ser de tipo `InjectionKey` - un tipo de utilidad proporcionado por Vue que extiende `Symbol`, que se puede utilizar para sincronizar el tipo de valor entre `provide()` e `inject()`.

- **Ejemplo**

  Asumiendo que un componente padre ha proporcionado valores como se muestra en el ejemplo anterior de `provide()`:

  ```vue
  <script setup>
  import { inject } from 'vue'
  import { countSymbol } from './injectionSymbols'

  // inyectar valor estático sin valor predeterminado
  const path = inject('path')

  // inyectar valor reactivo
  const count = inject('count')

  // inyectar con claves Symbol
  const count2 = inject(countSymbol)

  // inyectar con valor predeterminado
  const bar = inject('path', '/default-path')

  // inyectar función como valor predeterminado
  const fn = inject('function', () => {})

  // inyectar con valor fábrica por defecto
  const baz = inject('factory', () => newExpensiveObject(), true)
  </script>
  ```

- **Véase también**:
  - [Guía - Provide / Inject](/guide/components/provide-inject)
  - [Guía - Escritura de Provide / Inject](/guide/typescript/composition-api#typing-provide-inject)

## hasInjectionContext() <sup class="vt-badge" data-text="3.3+" /> {#has-injection-context}

Devuelve true si [inject()](#inject) puede usarse sin advertencia por ser llamado en el lugar equivocado (por ejemplo, fuera de `setup()`). Este método está diseñado para ser usado por librerías que quieran usar `inject()` internamente sin lanzar una advertencia al usuario final.

- **Tipo**

  ```ts
  function hasInjectionContext(): boolean
  ```
