# Enrutamiento {#routing}

## Enrutamiento del Lado del Cliente vs. del Lado del Servidor {#client-side-vs-server-side-routing}

El enrutamiento en el lado del servidor significa que el servidor envía una respuesta basada en la ruta de la URL que está visitando el usuario. Cuando hacemos clic en un enlace de una aplicación web tradicional renderizada por un servidor, el navegador recibe una respuesta HTML del servidor y vuelve a cargar toda la página con el nuevo HTML.

Sin embargo, en una [Aplicación de una Sola página](https://developer.mozilla.org/en-US/docs/Glossary/SPA) (SPA), el JavaScript del lado del cliente puede interceptar la navegación, obtener dinámicamente nuevos datos y actualizar la página actual sin necesidad de recargarla por completo. Esto generalmente da como resultado una experiencia de usuario más ágil, especialmente para casos de uso que se parecen más a "aplicaciones" reales, donde se espera que el usuario realice muchas interacciones durante un largo período de tiempo.

En estas SPA, el "enrutamiento" se realiza en el lado del cliente, en el navegador. Un enrutador del lado del cliente se encarga de administrar la vista renderizada de la aplicación utilizando las API del navegador, como la [API del historial](https://developer.mozilla.org/en-US/docs/Web/API/History) o el [evento `hashchange`](https://developer.mozilla.org/en-US/docs/Web/API/Window/hashchange_event).

## Router Oficial {#official-router}

<!-- TODO update links -->
<div>
  <VueSchoolLink href="https://vueschool.io/courses/vue-router-4-for-everyone" title="Curso gratis de Vue Router">
    Ver un curso de video gratuito en Vue School
  </VueSchoolLink>
</div>

Vue es muy adecuado para construir SPA. Para la mayoría de las SPA, se recomienda utilizar la [librería Vue Router](https://github.com/vuejs/router), que cuenta con soporte oficial. Para más detalles, consulta la [documentación](https://router.vuejs.org/) de Vue Router.

## Enrutamiento simple desde cero {#simple-routing-from-scratch}

Si solo necesitas un enrutamiento muy simple y no deseas involucrar una librería de enrutamiento completa, puedes hacerlo con [Componentes Dinámicos](/guide/essentials/component-basics#dynamic-components) y actualizar el estado del componente escuchando los [eventos de `hashchange`](https://developer.mozilla.org/en-US/docs/Web/API/Window/hashchange_event) del navegador o usando la [API de historial](https://developer.mozilla.org/en-US/docs/Web/API/History).

Este es un ejemplo de lo que se puede hacer:

<div class="composition-api">

```vue
<script setup>
import { ref, computed } from 'vue'
import Home from './Home.vue'
import About from './About.vue'
import NotFound from './NotFound.vue'

const routes = {
  '/': Home,
  '/about': About
}

const currentPath = ref(window.location.hash)

window.addEventListener('hashchange', () => {
  currentPath.value = window.location.hash
})

const currentView = computed(() => {
  return routes[currentPath.value.slice(1) || '/'] || NotFound
})
</script>

<template>
  <a href="#/">Home</a> | <a href="#/about">Acerca de</a> |
  <a href="#/non-existent-path">Enlace Roto</a>
  <component :is="currentView" />
</template>
```

[Pruébalo en la Zona de Práctica](https://play.vuejs.org/#eNptUk1vgkAQ/SsTegAThZp4MmhikzY9mKanXkoPWxjLRpgly6JN1P/eWb5Eywlm572ZN2/m5GyKwj9U6CydsIy1LAyUaKpiHZHMC6UNnEDjbgqxyovKYAIX2GmVg8sktwe9qhzbdz+wga15TW++VWX6fB3dAt6UeVEVJT2me2hhEcWKSgOamVjCCk4RAbiBu6xbT5tI2ML8VDeI6HLlxZXWSOZdmJTJPJB3lJSoo5+pWBipyE9FmU4soU2IJHk+MGUrS4OE2nMtIk4F/aA7BW8Cq3WjYlDbP4isQu4wVp0F1Q1uFH1IPDK+c9cb1NW8B03tyJ//uvhlJmP05hM4n60TX/bb2db0CoNmpbxMDgzmRSYMcgQQCkjZhlXkPASRs7YmhoFYw/k+WXvKiNrTcQgpmuFv7ZOZFSyQ4U9a7ZFgK2lvSTXFDqmIQbCUJTMHFkQOBAwKg16kM3W6O7K3eSs+nbeK+eee1V/XKK0dY4Q3vLhR6uJxMUK8/AFKaB6k)

</div>

<div class="options-api">

```vue
<script>
import Home from './Home.vue'
import About from './About.vue'
import NotFound from './NotFound.vue'

const routes = {
  '/': Home,
  '/about': About
}

export default {
  data() {
    return {
      currentPath: window.location.hash
    }
  },
  computed: {
    currentView() {
      return routes[this.currentPath.slice(1) || '/'] || NotFound
    }
  },
  mounted() {
    window.addEventListener('hashchange', () => {
      this.currentPath = window.location.hash
    })
  }
}
</script>

<template>
  <a href="#/">Home</a> | <a href="#/about">Acerca de</a> |
  <a href="#/non-existent-path">Enlace Roto</a>
  <component :is="currentView" />
</template>
```

[Pruébalo en la Zona de Práctica](https://play.vuejs.org/#eNptUstO6zAQ/ZVR7iKtVJKLxCpKK3Gli1ggxIoNZmGSKbFoxpEzoUi0/87YeVBKNonHPmfOmcdndN00yXuHURblbeFMwxtFpm6sY7i1NcLW2RriJPWBB8bT8/WL7Xh6D9FPwL3lG9tROWHGiwGmqLDUMjhhYgtr+FQEEKdxFqRXfaR9YrkKAoqOnocfQaDEre523PNKzXqx7M8ADrlzNEYAReccEj9orjLYGyrtPtnZQrOxlFS6rXqgZJdPUC5s3YivMhuTDCkeDe6/dSalvognrkybnIgl7c4UuLhcwuHgS3v2/7EPvzRruRXJ7/SDU12W/98l451pGQndIvaWi0rTK8YrEPx64ymKFQOce5DOzlfs4cdlkA+NzdNpBSRgrJudZpQIINdQOdyuVfQnVdHGzydP9QYO549hXIII45qHkKUL/Ail8EUjBgX+z9k3JLgz9OZJgeInYElAkJlWmCcDUBGkAsrTyWS0isYV9bv803x1OTiWwzlrWtxZ2lDGDO90mWepV3+vZojHL3QQKQE=)

</div>
