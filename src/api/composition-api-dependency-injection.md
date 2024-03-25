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
  import { fooSymbol } from './injectionSymbols'

  // proporcionar un valor estático
  provide('foo', 'bar')

  // proporcionar un valor reactivo
  const count = ref(0)
  provide('count', count)

  // proporcionar con claves Symbol
  provide(fooSymbol, count)
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

  El primer argumento es la clave de inyección. Vue recorrerá la estructura padre para ubicar un valor proporcionado con una clave coincidente. Si varios componentes de la estructura padre proporcionan la misma clave, el más cercano al componente que inyecta reemplazará a los que están más arriba en la cadena. Si no se encuentra un valor con una clave coincidente, `inject()` devuelve `undefined` a menos que se proporcione un valor predeterminado.

  El segundo argumento es opcional y es el valor predeterminado que se utilizará cuando no se encuentre ningún valor que corresponda.

  El segundo argumento también puede ser una función de fábrica que devuelva valores que son costosos de crear. En este caso, se debe pasar `true` como tercer argumento para indicar que se debe utilizar la función como fábrica en lugar del propio valor.

  Al igual que las APIs de registro de los hooks de ciclo de vida, `inject()` debe ser llamado de forma sincrónica durante la fase `setup()` de un componente.

  Cuando se utiliza TypeScript, la clave puede ser de tipo `InjectionKey` - un tipo de utilidad proporcionado por Vue que extiende `Symbol`, que se puede utilizar para sincronizar el tipo de valor entre `provide()` e `inject()`.

- **Ejemplo**

  Asumiendo que un componente padre ha proporcionado valores como se muestra en el ejemplo anterior de `provide()`:

  ```vue
  <script setup>
  import { inject } from 'vue'
  import { fooSymbol } from './injectionSymbols'

  // inyectar valor estático con valor predeterminado
  const foo = inject('foo')

  // inyectar valor reactivo
  const count = inject('count')

  // inyectar con claves Symbol
  const foo2 = inject(fooSymbol)

  // inyectar con valor predeterminado
  const bar = inject('foo', 'default value')

  // inyectar fábrica como valor predeterminado
  const baz = inject('foo', () => new Map())

  // inyectar con el valor predeterminado de la función, pasando el 3er argumento
  const fn = inject('function', () => {}, false)
  </script>
  ```

- **Véase también**:
  - [Guía - Provide / Inject](/guide/components/provide-inject)
  - [Guía - Escritura de Provide / Inject](/guide/typescript/composition-api#typing-provide-inject)
