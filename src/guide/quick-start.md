---
footer: false
---

# Inicio Rápido {#quick-start}

## Prueba Vue Online {#try-vue-online}

- Para probar rápidamente Vue, puedes hacerlo directamente en nuestro [Playground](https://play.vuejs.org/#eNo9jcEKwjAMhl/lt5fpQYfXUQfefAMvvRQbddC1pUuHUPrudg4HIcmXjyRZXEM4zYlEJ+T0iEPgXjn6BB8Zhp46WUZWDjCa9f6w9kAkTtH9CRinV4fmRtZ63H20Ztesqiylphqy3R5UYBqD1UyVAPk+9zkvV1CKbCv9poMLiTEfR2/IXpSoXomqZLtti/IFwVtA9A==).

- Si prefieres una configuración HTML sencilla sin pasos de compilación, puedes utilizar este [JSFiddle](https://jsfiddle.net/yyx990803/2ke1ab0z/) como punto de partida.

- Si ya estás familiarizado con Node.js y el concepto de herramientas de compilación, también puedes probar una configuración de compilación completa directamente desde tu navegador en [StackBlitz](https://vite.new/vue).

## Creación de una Aplicación de Vue {#creating-a-vue-application}

:::tip Prerequisitos

- Familiaridad con la línea de comandos
- Instalar [Node.js](https://nodejs.org/) versión 16.0 o superior
  :::

En esta sección presentaremos cómo crear una [Aplicación de una Sola Página (SPA)](/guide/extras/ways-of-using-vue#single-page-application-spa) de Vue en tu máquina local. El proyecto creado utilizará una configuración de compilación basada en [Vite](https://vitejs.dev) y nos permitirá utilizar los [Componentes de un Solo Archivo (SFCs)](/guide/scaling-up/sfc) de Vue.

Asegúrate de tener instalada una versión actualizada de [Node.js](https://nodejs.org/), luego ejecuta el siguiente comando en tu línea de comandos (sin el signo `>`):

<div class="language-sh"><pre><code><span class="line"><span style="color:var(--vt-c-green);">&gt;</span> <span style="color:#A6ACCD;">npm init vue@latest</span></span></code></pre></div>

Este comando instalará y ejecutará [create-vue](https://github.com/vuejs/create-vue), la herramienta oficial de estructuración de proyectos de Vue. Se te presentarán solicitudes para varias características opcionales como TypeScript y soporte de pruebas:

<div class="language-sh"><pre><code><span style="color:var(--vt-c-green);">✔</span> <span style="color:#A6ACCD;">Project name: <span style="color:#888;">… <span style="color:#89DDFF;">&lt;</span><span style="color:#888;">your-project-name</span><span style="color:#89DDFF;">&gt;</span></span></span>
<span style="color:var(--vt-c-green);">✔</span> <span style="color:#A6ACCD;">Add TypeScript? <span style="color:#888;">… <span style="color:#89DDFF;text-decoration:underline">No</span> / Yes</span></span>
<span style="color:var(--vt-c-green);">✔</span> <span style="color:#A6ACCD;">Add JSX Support? <span style="color:#888;">… <span style="color:#89DDFF;text-decoration:underline">No</span> / Yes</span></span>
<span style="color:var(--vt-c-green);">✔</span> <span style="color:#A6ACCD;">Add Vue Router for Single Page Application development? <span style="color:#888;">… <span style="color:#89DDFF;text-decoration:underline">No</span> / Yes</span></span>
<span style="color:var(--vt-c-green);">✔</span> <span style="color:#A6ACCD;">Add Pinia for state management? <span style="color:#888;">… <span style="color:#89DDFF;text-decoration:underline">No</span> / Yes</span></span>
<span style="color:var(--vt-c-green);">✔</span> <span style="color:#A6ACCD;">Add Vitest for Unit testing? <span style="color:#888;">… <span style="color:#89DDFF;text-decoration:underline">No</span> / Yes</span></span>
<span style="color:var(--vt-c-green);">✔</span> <span style="color:#A6ACCD;">Add Cypress for both Unit and End-to-End testing? <span style="color:#888;">… <span style="color:#89DDFF;text-decoration:underline">No</span> / Yes</span></span>
<span style="color:var(--vt-c-green);">✔</span> <span style="color:#A6ACCD;">Add ESLint for code quality? <span style="color:#888;">… <span style="color:#89DDFF;text-decoration:underline">No</span> / Yes</span></span>
<span style="color:var(--vt-c-green);">✔</span> <span style="color:#A6ACCD;">Add Prettier for code formatting? <span style="color:#888;">… <span style="color:#89DDFF;text-decoration:underline">No</span> / Yes</span></span>
<span></span>
<span style="color:#A6ACCD;">Scaffolding project in ./<span style="color:#89DDFF;">&lt;</span><span style="color:#888;">your-project-name</span><span style="color:#89DDFF;">&gt;</span>...</span>
<span style="color:#A6ACCD;">Done.</span></code></pre></div>

Si no estás seguro de alguna opción, simplemente elige `No` pulsando enter por ahora. Una vez creado el proyecto, sigue las instrucciones para instalar las dependencias e iniciar el servidor de desarrollo:

<div class="language-sh"><pre><code><span class="line"><span style="color:var(--vt-c-green);">&gt; </span><span style="color:#A6ACCD;">cd</span><span style="color:#A6ACCD;"> </span><span style="color:#89DDFF;">&lt;</span><span style="color:#888;">your-project-name</span><span style="color:#89DDFF;">&gt;</span></span>
<span class="line"><span style="color:var(--vt-c-green);">&gt; </span><span style="color:#A6ACCD;">npm install</span></span>
<span class="line"><span style="color:var(--vt-c-green);">&gt; </span><span style="color:#A6ACCD;">npm run dev</span></span>
<span class="line"></span></code></pre></div>

Ahora deberías tener tu primer proyecto Vue funcionando. Ten en cuenta que los componentes de ejemplo del proyecto generado están escritos utilizando la [Composition API](/guide/introduction#composition-api) y `<script setup>`, en lugar de la [Options API](/guide/introduction#options-api). He aquí algunos consejos adicionales:

- La configuración de IDE recomendada es [Visual Studio Code](https://code.visualstudio.com/) + la [extensión Volar](https://marketplace.visualstudio.com/items?itemName=Vue.volar). Si utilizas otros editores, consulta la sección [Soporte para IDE](/guide/scaling-up/tooling#ide-support).
- En la [Guía de Herramientas](/guide/scaling-up/tooling) se discuten más detalles sobre las herramientas, incluyendo la integración con frameworks backend.
- Para obtener más información sobre la herramienta de compilación subyacente Vite, consulta la [documentación de Vite](https://vitejs.dev).
- Si decides utilizar TypeScript, consulta la guía [Usando Vue con TypeScript](typescript/overview.html).

Cuando estés listo para enviar tu aplicación a producción, ejecuta lo siguiente:

<div class="language-sh"><pre><code><span class="line"><span style="color:var(--vt-c-green);">&gt; </span><span style="color:#A6ACCD;">npm run build</span></span>
<span class="line"></span></code></pre></div>

Esto creará una compilación de tu aplicación lista para producción en el directorio `./dist` del proyecto. Consulta la guía de [Implementación de Producción (Deployment)](/guide/best-practices/production-deployment) para aprender más sobre cómo enviar tu aplicación a producción.

[Siguientes Pasos >](#siguientes-pasos)

## Usando Vue desde un CDN {#using-vue-from-cdn}

Puedes usar Vue directamente desde un CDN a través de una etiqueta script:

```html
<script src="https://unpkg.com/vue@3/dist/vue.global.js"></script>
```

Aquí estamos usando [unpkg](https://unpkg.com/), pero también puedes usar cualquier CDN que sirva paquetes npm, por ejemplo [jsdelivr](https://www.jsdelivr.com/package/npm/vue) o [cdnjs](https://cdnjs.com/libraries/vue). Por supuesto, también puedes descargar este archivo y servirlo tú mismo.

Cuando se utiliza Vue desde un CDN, no hay ningún "paso de compilación" involucrado. Esto hace que la configuración sea mucho más simple, y es adecuado para mejorar el HTML estático o la integración con un marco de backend. Sin embargo, no podrás usar la sintaxis de Componentes de un Solo Archivo (SFC).

### Usando la Compilación Global {#using-the-global-build}

El enlace anterior carga la _compilación global_ de Vue, donde todas las APIs de alto nivel están expuestas como propiedades en el objeto `Vue` global. Aquí hay un ejemplo completo usando la compilación global:

```html
<script src="https://unpkg.com/vue@3/dist/vue.global.js"></script>

<div id="app">{{ message }}</div>

<script>
  const { createApp } = Vue

  createApp({
    data() {
      return {
        message: '¡Hola Vue!'
      }
    }
  }).mount('#app')
</script>
```

[JSFiddle demo](https://jsfiddle.net/yyx990803/nw1xg8Lj/)

### Usando el Módulo de Construcción ES {#using-the-es-module-build}

En el resto de la documentación, utilizaremos principalmente la sintaxis de [módulos ES](https://developer.mozilla.org/es/docs/Web/JavaScript/Guide/Modules). La mayoría de los navegadores modernos soportan ahora módulos ES de forma nativa, por lo que podemos usar Vue desde un CDN a través de módulos ES nativos como este:

```html{3,4}
<div id="app">{{ message }}</div>

<script type="module">
  import { createApp } from 'https://unpkg.com/vue@3/dist/vue.esm-browser.js'

  createApp({
    data() {
      return {
        message: '¡Hola Vue!'
      }
    }
  }).mount('#app')
</script>
```

Observa que estamos usando `<script type="module">`, y que la URL importada del CDN apunta a la **compilación de módulos ES** de Vue.

[JSFiddle demo](https://jsfiddle.net/yyx990803/vo23c470/)

### Habilitar mapas de importación {#enabling-import-maps}

En el ejemplo anterior, estamos importando desde la URL del CDN, pero en el resto de la documentación verás código como este:

```js
import { createApp } from 'vue'
```

Podemos enseñarle al navegador dónde localizar la importación de `vue` usando [Import Maps](https://caniuse.com/import-maps):

```html{1-7,12}
<script type="importmap">
  {
    "imports": {
      "vue": "https://unpkg.com/vue@3/dist/vue.esm-browser.js"
    }
  }
</script>

<div id="app">{{ message }}</div>

<script type="module">
  import { createApp } from 'vue'

  createApp({
    data() {
      return {
        message: '¡Hola Vue!'
      }
    }
  }).mount('#app')
</script>
```

[JSFiddle demo](https://jsfiddle.net/yyx990803/2ke1ab0z/)

También puede añadir entradas para otras dependencias al mapa de importación, pero asegúrate de que apuntan a la versión de los módulos ES de la biblioteca que pretendes utilizar.

:::tip Soporte para Navegadores de Mapas de Importación
Los mapas de importación son compatibles por defecto con los navegadores basados en Chromium, por lo que recomendamos utilizar Chrome o Edge durante el proceso de aprendizaje.

Si utilizas Firefox, sólo está soportado por defecto en la versión 108+ o estableciendo la opción `dom.importMaps.enabled` a true en `about:config` para las versiones 102+.

Si tu navegador preferido aún no soporta la importación de mapas, puedes rellenarlo con [es-module-shims](https://github.com/guybedford/es-module-shims).
:::

:::warning Notas sobre el Uso en Producción
Los ejemplos mostrados hasta ahora usan la versión de desarrollo de Vue. Si quieres usar Vue desde un CDN en producción, asegúrate de consultar la guía de [Implementación en Producción (Deployment)](/guide/best-practices/production-deployment#without-build-tools).
:::

### Distribución de los Módulos {#splitting-up-the-modules}

A medida que profundizamos en la guía, puede que necesitemos dividir nuestro código en archivos JavaScript separados para que sean más fáciles de gestionar. Por ejemplo:

```html
<!-- index.html -->
<div id="app"></div>

<script type="module">
  import { createApp } from 'vue'
  import MyComponent from './my-component.js'

  createApp(MyComponent).mount('#app')
</script>
```

```js
// my-component.js
export default {
  data() {
    return { count: 0 }
  },
  template: `<div>El contador está en {{ count }}</div>`
}
```

Si abres el `index.html` de arriba directamente en tu navegador, verás que arroja un error porque los módulos ES no pueden trabajar sobre el protocolo `file://`. Para que esto funcione, necesitas servir tu `index.html` sobre el protocolo `http://`, con un servidor HTTP local.

Para iniciar un servidor HTTP local, primero instala [Node.js](https://nodejs.org/es/) y luego ejecuta `npx serve` desde la línea de comandos en el mismo directorio donde está tu archivo HTML. También puedes utilizar cualquier otro servidor HTTP que pueda servir archivos estáticos con los tipos MIME correctos.

Puede que hayas notado que la plantilla del componente importado está en línea como una cadena JavaScript. Si estás usando VSCode, puedes instalar la extensión [es6-string-html](https://marketplace.visualstudio.com/items?itemName=Tobermory.es6-string-html) y prefijar las cadenas con un comentario `/*html*/` para obtener resaltado de la sintaxis para ellas.

### Usando la Composition API sin un Paso de Compilación {#using-composition-api-without-a-build-step}

Muchos de los ejemplos para la Composition API utilizarán la sintaxis `<script setup>`. Si piensas utilizar la Composition API sin un paso de compilación, consulta el uso de la [opción `setup()`](/api/composition-api-setup).

## Siguientes pasos {#next-steps}

Si te has saltado la [Introducción](/guide/introduction), te recomendamos encarecidamente que la leas antes de pasar al resto de la documentación.

<div class="vt-box-container next-steps">
  <a class="vt-box" href="/guide/essentials/application.html">
    <p class="next-steps-link">Continúa con la Guía</p>
    <p class="next-steps-caption">La guía te lleva a través de cada aspecto del framework con todos sus detalles.</p>
  </a>
  <a class="vt-box" href="/tutorial/">
    <p class="next-steps-link">Prueba el Tutorial</p>
    <p class="next-steps-caption">Para aquellos que prefieren aprender las cosas de forma práctica.</p>
  </a>
  <a class="vt-box" href="/examples/">
    <p class="next-steps-link">Mira los Ejemplos</p>
    <p class="next-steps-caption">Explora ejemplos de las principales características y las tareas comunes de la UI.</p>
  </a>
</div>
