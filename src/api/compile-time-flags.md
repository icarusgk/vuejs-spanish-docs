---
outline: deep
---

# Banderas de tiempo de compilación {#compile-time-flags}

:::tip
Las banderas de tiempo de compilación solo se aplican cuando se utiliza la versión `esm-bundler` de Vue (es decir, `vue/dist/vue.esm-bundler.js`).
:::

Cuando se utiliza Vue con un paso de compilación, es posible configurar varias banderas de tiempo de compilación para habilitar/deshabilitar ciertas características. El beneficio de usar banderas de tiempo de compilación es que las características deshabilitadas de esta manera pueden eliminarse del paquete final mediante tree-shaking.

Vue funcionará incluso si estas banderas no están configuradas explícitamente. Sin embargo, se recomienda configurarlas siempre para que las características relevantes puedan eliminarse correctamente cuando sea posible.

Consulta las [Guías de configuración](#configuration-guides) sobre cómo configurarlas según la herramienta de compilación.

## `__VUE_OPTIONS_API__` {#VUE_OPTIONS_API}

- **Por defecto:** `true`

  Habilita/deshabilita el soporte de Options API. Desactivar esto resultará en paquetes más pequeños, pero puede afectar la compatibilidad con bibliotecas de terceros si dependen del Options API.

## `__VUE_PROD_DEVTOOLS__` {#VUE_PROD_DEVTOOLS}

- **Por defecto:** `false`

  Habilita/deshabilita el soporte de herramientas de desarrollo (devtools) en compilaciones de producción. Esto resultará en más código incluido en el paquete, por lo que se recomienda habilitarlo solo con fines de depuración.

## `__VUE_PROD_HYDRATION_MISMATCH_DETAILS__` <sup class="vt-badge" data-text="3.4+" /> {#VUE_PROD_HYDRATION_MISMATCH_DETAILS}

- **Por defecto:** `false`

  Habilita/deshabilita advertencias detalladas para discrepancias de hidratación en compilaciones de producción. Esto resultará en más código incluido en el paquete, por lo que se recomienda habilitarlo solo con fines de depuración.

## Guías de configuración {#configuration-guides}

### Vite {#vite}

`@vitejs/plugin-vue` proporciona automáticamente valores por defecto para estas banderas. Para cambiar los valores por defecto, utiliza la opción de configuración `define` de Vite. Puedes encontrar más información sobre cómo hacerlo en la [documentación de opciones compartidas de Vite](https://es.vitejs.dev/config/shared-options.html#define).

```js
// vite.config.js
import { defineConfig } from 'vite'

export default defineConfig({
  define: {
    // Habilitar detalles de discrepancias de hidratación en compilación de producción.
    __VUE_PROD_HYDRATION_MISMATCH_DETAILS__: 'true'
  }
})
```

### vue-cli {#vue-cli}

El servicio `@vue/cli-service` proporciona automáticamente valores por defecto para algunas de estas banderas. Para configurar/cambiar los valores:

```js
// vue.config.js
module.exports = {
  chainWebpack: (config) => {
    config.plugin('define').tap((definitions) => {
      Object.assign(definitions[0], {
        __VUE_OPTIONS_API__: 'true',
        __VUE_PROD_DEVTOOLS__: 'false',
        __VUE_PROD_HYDRATION_MISMATCH_DETAILS__: 'false'
      })
      return definitions
    })
  }
}
```

### webpack {#webpack}

Las banderas deben definirse utilizando el [DefinePlugin](https://webpack.js.org/plugins/define-plugin/) de webpack.

```js
// webpack.config.js
module.exports = {
  // ...
  plugins: [
    new webpack.DefinePlugin({
      __VUE_OPTIONS_API__: 'true',
      __VUE_PROD_DEVTOOLS__: 'false',
      __VUE_PROD_HYDRATION_MISMATCH_DETAILS__: 'false'
    })
  ]
}
```

### Rollup {#rollup}

Las banderas deben definirse utilizando [@rollup/plugin-replace](https://github.com/rollup/plugins/tree/master/packages/replace):

```js
// rollup.config.js
import replace from '@rollup/plugin-replace'

export default {
  plugins: [
    replace({
      __VUE_OPTIONS_API__: 'true',
      __VUE_PROD_DEVTOOLS__: 'false',
      __VUE_PROD_HYDRATION_MISMATCH_DETAILS__: 'false'
    })
  ]
}
```
