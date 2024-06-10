# Glosario {#glossary}

Este glosario tiene la intención de proporcionar orientación sobre los significados de los términos técnicos que se utilizan comúnmente al hablar de Vue. Está destinado a ser _descriptivo_ de cómo se usan comúnmente los términos, no una especificación _prescriptiva_ de cómo deben usarse. Algunos términos pueden tener significados o matices ligeramente diferentes según el contexto circundante.

[[TOC]]

## Componente asíncrono {#async-component}

Un _componente asíncrono_ es un envoltorio alrededor de otro componente que permite cargar de forma diferida el componente envuelto. Esto se utiliza típicamente como una forma de reducir el tamaño de los archivos `.js` generados, permitiendo dividirlos en fragmentos más pequeños que se cargan solo cuando se necesitan.

Vue Router tiene una característica similar para la [carga perezosa de componentes de ruta](https://router.vuejs.org/guide/advanced/lazy-loading.html), aunque esto no utiliza la característica de componentes asíncronos de Vue.

Para obtener más detalles, consulta:

- [Guía - Componentes asíncronos](/guide/components/async.html)

## Macro de compilador {#compiler-macro}

Un _macro de compilador_ es un código especial que es procesado por un compilador y convertido en algo diferente. Son efectivamente una forma ingeniosa de reemplazo de cadenas.

El compilador de [SFC](#single-file-component) de Vue admite varios macros, como `defineProps()`, `defineEmits()` y `defineExpose()`. Estos macros están diseñados intencionalmente para parecerse a funciones JavaScript normales, de modo que puedan aprovechar la misma herramienta de análisis y deducción de tipos alrededor de JavaScript / TypeScript. Sin embargo, no son funciones reales que se ejecutan en el navegador. Son cadenas especiales que el compilador detecta y reemplaza con el código JavaScript real que se ejecutará.

Los macros tienen limitaciones en su uso que no se aplican al código JavaScript normal. Por ejemplo, podrías pensar que `const dp = defineProps` te permitiría crear un alias para `defineProps`, pero en realidad resultará en un error. También hay limitaciones en los valores que se pueden pasar a `defineProps()`, ya que los 'argumentos' deben ser procesados por el compilador y no en tiempo de ejecución.

Para más detalles, consulta:

- [`<script setup>` - `defineProps()` y `defineEmits()`](/api/sfc-script-setup.html#defineprops-defineemits)
- [`<script setup>` - `defineExpose()`](/api/sfc-script-setup.html#defineexpose)

## Componente {#component}

El término _componente_ no es único de Vue. Es común en muchos marcos de interfaz de usuario. Describe un fragmento de la interfaz de usuario, como un botón o una casilla de verificación. Los componentes también se pueden combinar para formar componentes más grandes.

Los componentes son el mecanismo principal proporcionado por Vue para dividir una interfaz de usuario en partes más pequeñas, tanto para mejorar la mantenibilidad como para permitir la reutilización de código.

Un componente de Vue es un objeto. Todas las propiedades son opcionales, pero se requiere un template o una función de renderizado para que el componente se renderice. Por ejemplo, el siguiente objeto sería un componente válido:

```js
const HelloWorldComponent = {
  render() {
    return '¡Hola mundo!'
  }
}
```

En la práctica, la mayoría de las aplicaciones de Vue se escriben utilizando [Componentes de un solo archivo](#single-file-component) (archivos `.vue`). Aunque estos componentes pueden no parecer objetos a primera vista, el compilador de SFC los convertirá en un objeto, que se utiliza como la exportación predeterminada del archivo. Desde una perspectiva externa, un archivo `.vue` es simplemente un módulo ES que exporta un objeto de componente.

Las propiedades de un objeto de componente generalmente se denominan _opciones_. Aquí es donde la [Options API](#options-api) obtiene su nombre.

Las opciones de un componente definen cómo se deben crear las instancias de ese componente. Los componentes son conceptualmente similares a las clases, aunque Vue no utiliza clases JavaScript reales para definirlos.

El término componente también se puede usar de manera más amplia para referirse a instancias de componentes.

Para obtener más detalles, consulta:

- [Guía - Conceptos básicos de componentes](/guide/essentials/component-basics.html)

La palabra 'componente' también aparece en varios otros términos:

- [componente asíncrono](#async-component)
- [componente dinámico](#dynamic-component)
- [componente funcional](#functional-component)
- [Componente Web](#web-component)

## Composable {#composable}

El término _composable_ describe un patrón de uso común en Vue. No es una característica separada de Vue, es simplemente una forma de usar la [Composition API](#composition-api) del framework.

- Un composable es una función.
- Los composables se utilizan para encapsular y reutilizar la lógica con estado.
- El nombre de la función generalmente comienza con `use`, para que otros desarrolladores sepan que es un composable.
- Se espera que la función se llame durante la ejecución síncrona de la función `setup()` de un componente (o, de manera equivalente, durante la ejecución de un bloque `<script setup>`). Esto vincula la invocación del composable al contexto actual del componente, por ejemplo, mediante llamadas a `provide()`, `inject()` o `onMounted()`.
- Los composables suelen devolver un objeto plano, no un objeto reactivo. Este objeto suele contener refs y funciones y se espera que se destructure dentro del código que lo llama.

Al igual que con muchos patrones, puede haber cierta discrepancia sobre si un código específico califica para la etiqueta. No todas las funciones de utilidad de JavaScript son composables. Si una función no utiliza la Composition API, probablemente no sea un composable. Si no espera ser llamado durante la ejecución síncrona de `setup()`, probablemente no sea un composable. Los composables se utilizan específicamente para encapsular la lógica con estado, no son solo una convención de nombres para funciones.

Consulta la [Guía - Composables](/guide/reusability/composables.html) para obtener más detalles sobre cómo escribir composables.

## Composition API {#composition-api}

La _Composition API_ es una colección de funciones utilizadas para escribir componentes y composables en Vue.

El término también se usa para describir uno de los dos estilos principales utilizados para escribir componentes, siendo el otro la [Options API](#options-api). Los componentes escritos utilizando la Composition API utilizan ya sea `<script setup>` o una función `setup()` explícita.

Consulta las [preguntas frecuentes sobre la Composition API](/guide/extras/composition-api-faq) para obtener más detalles.

## Elementos personalizados {#custom-element}

Un _elemento personalizado_ es una característica del estándar [Web Components](#web-component), que está implementado en navegadores web modernos. Se refiere a la capacidad de utilizar un elemento HTML personalizado en tu marcado HTML para incluir un componente web en ese punto de la página.

Vue tiene soporte incorporado para representar elementos personalizados y permite su uso directo en las plantillas de componentes de Vue. No se debe confundir los elementos personalizados con la capacidad de incluir componentes Vue como etiquetas dentro de la plantilla de otro componente Vue.

Los elementos personalizados se utilizan para crear componentes web. Para obtener más detalles, consulta:

- [Guía - Vue y Web Components](/guide/extras/web-components.html)

## Directiva {#directive}

El término _directiva_ se refiere a atributos de plantilla que comienzan con el prefijo `v-`, o sus abreviaturas equivalentes.

Las directivas integradas incluyen `v-if`, `v-for`, `v-bind`, `v-on` y `v-slot`.

Vue también admite la creación de directivas personalizadas, aunque generalmente solo se utilizan como una "salida de emergencia" para manipular nodos DOM directamente. Las directivas personalizadas generalmente no se pueden utilizar para recrear la funcionalidad de las directivas integradas.

Para obtener más detalles, consulta:

- [Guía - Sintaxis de Plantilla - Directivas](/guide/essentials/template-syntax.html#directives)
- [Guía - Directivas Personalizadas](/guide/reusability/custom-directives.html)

## Componente dinámico {#dynamic-component}

El término componente dinámico se utiliza para describir casos en los que la elección del componente hijo que se va a renderizar debe hacerse de forma dinámica. Normalmente esto se logra utilizando `<component :is="tipo">`.

Un componente dinámico no es un tipo especial de componente. Cualquier componente puede usarse como un componente dinámico. Es la elección del componente lo que es dinámico, en lugar del componente en sí.

Para obtener más detalles, consulta:

- [Guía - Conceptos Básicos de Componentes - Componentes Dinámicos](/guide/essentials/component-basics.html#dynamic-components)

## Efecto {#effect}

Consulta [efecto reactivo](#reactive-effect) y [efecto secundario](#side-effect).

## Evento {#event}

El uso de eventos para comunicarse entre diferentes partes de un programa es común en muchas áreas diferentes de la programación. Dentro de Vue, el término se aplica comúnmente tanto a eventos nativos de elementos HTML como a eventos de componentes Vue. La directiva `v-on` se utiliza en plantillas para escuchar ambos tipos de eventos.

Para obtener más detalles, consulta:

- [Guía - Manejo de Eventos](/guide/essentials/event-handling.html)
- [Guía - Eventos de Componentes](/guide/components/events.html)

## Fragmento {#fragment}

El término _fragmento_ se refiere a un tipo especial de [VNode](#vnode) que se utiliza como padre para otros VNodes, pero que no renderiza ningún elemento en sí mismo.

El nombre proviene del concepto similar de un [`DocumentFragment`](https://developer.mozilla.org/en-US/docs/Web/API/DocumentFragment) en la API nativa del DOM.

Los fragmentos se utilizan para admitir componentes con múltiples nodos raíz. Aunque estos componentes pueden parecer tener múltiples raíces, en realidad utilizan un nodo de fragmento como una sola raíz, como padre de los nodos 'raíz'.

Los fragmentos también son utilizados por el compilador de plantillas como una forma de envolver múltiples nodos dinámicos, como los creados mediante `v-for` o `v-if`. Esto permite pasar pistas adicionales al algoritmo de parcheo del [VDOM](#virtual-dom). Gran parte de esto se maneja internamente, pero un lugar donde puedes encontrarlo directamente es usando una `key` en una etiqueta `<template>` con `v-for`. En ese escenario, la `key` se agrega como una [propiedad](#prop) al VNode del fragmento.

Actualmente, los nodos de fragmento se representan en el DOM como nodos de texto vacíos, aunque eso es un detalle de implementación. Puedes encontrarte con esos nodos de texto si usas `$el` o intentas recorrer el DOM con las API integradas del navegador.

## Componente funcional {#functional-component}

Una definición de componente suele ser un objeto que contiene opciones. Puede que no parezca así si estás usando `<script setup>`, pero el componente exportado desde el archivo `.vue` seguirá siendo un objeto.

Un _componente funcional_ es una forma alternativa de componente que se declara mediante una función. Esa función actúa como la [función de renderizado](#render-function) para el componente.

Un componente funcional no puede tener su propio estado. Tampoco pasa por el ciclo de vida normal del componente, por lo que no se pueden usar hooks de ciclo de vida. Esto los hace ligeramente más livianos que los componentes normales que contienen estado.

Para más detalles, consulta:

- [Guía - Funciones de Renderizado y JSX - Componentes Funcionales](/guide/extras/render-function.html#functional-components)

## Hoisting {#hoisting}

El término _hoisting_ se utiliza para describir la ejecución de una sección de código antes de que se alcance, adelantándose a otro código. La ejecución se "eleva" a un punto anterior.

JavaScript utiliza el hoisting para algunas construcciones, como `var`, `import` y declaraciones de funciones.

En un contexto de Vue, el compilador de plantillas aplica el _hoisting estático_ para mejorar el rendimiento. Al convertir una plantilla a una función de renderizado, los VNodes que corresponden a contenido estático pueden crearse una vez y luego reutilizarse. Estos VNodes estáticos se describen como hoisteados porque se crean fuera de la función de renderizado, antes de que se ejecute. Una forma similar de hoisting se aplica a objetos o matrices estáticas generadas por el compilador de plantillas.

Para más detalles, consulta:

- [Guía - Mecanismo de Renderizado - Hoisting Estático](/guide/extras/rendering-mechanism.html#static-hoisting)

## Plantilla en el DOM {#in-dom-template}

Hay varias formas de especificar una plantilla para un componente. En la mayoría de los casos, la plantilla se proporciona como una cadena de texto.

El término _plantilla en el DOM_ se refiere al escenario en el que la plantilla se proporciona en forma de nodos DOM, en lugar de una cadena. Vue luego convierte los nodos DOM en una cadena de plantilla usando `innerHTML`.

Normalmente, una plantilla en el DOM comienza como un marcado HTML escrito directamente en el HTML de la página. El navegador luego analiza esto en nodos DOM, que Vue luego utiliza para extraer `innerHTML`.

Para más detalles, consulta:

- [Guía - Creando una Aplicación - Plantilla de Componente Raíz en el DOM](/guide/essentials/application.html#in-dom-root-component-template)
- [Guía - Conceptos Básicos de Componentes - Advertencias sobre el procesamiento de las plantillas en el DOM](/guide/essentials/component-basics.html#in-dom-template-parsing-caveats)
- [Opciones: Renderizado - template](/api/options-rendering.html#template)

## inject {#inject}

Consulta [provide / inject](#provide-inject).

## Hooks de ciclo de vida {#lifecycle-hooks}

Una instancia de componente Vue atraviesa un ciclo de vida. Por ejemplo, se crea, se monta, se actualiza y se desmonta.

Los _hooks de ciclo de vida_ son una forma de escuchar estos eventos del ciclo de vida.

Con la Options API, cada hook se proporciona como una opción separada, por ejemplo, `mounted`. La Composition API utiliza funciones en su lugar, como `onMounted()`.

Para más detalles, consulta:

- [Guía - Hooks de Ciclo de Vida](/guide/essentials/lifecycle.html)

## Macro {#macro}

Consulta [macro de compilador](#compiler-macro).

## Slot con nombre {#named-slot}

Un componente puede tener múltiples slots, diferenciados por nombre. Los slots que no son el slot predeterminado se conocen como _slots con nombre_.

Para más detalles, consulta:

- [Guía - Slots - Slots con Nombre](/guide/components/slots.html#named-slots)

## Options API {#options-api}

Los componentes Vue se definen utilizando objetos. Las propiedades de estos objetos de componente se conocen como _opciones_.

Los componentes pueden escribirse de dos estilos. Un estilo utiliza la [Composition API](#composition-api) en conjunto con `setup` (ya sea a través de una opción `setup()` o `<script setup>`). El otro estilo hace muy poco uso directo de la Composition API, en su lugar utiliza varias opciones del componente para lograr un resultado similar. Las opciones del componente que se utilizan de esta manera se denominan _Options API_.

La Options API incluye opciones como `data()`, `computed`, `methods` y `created()`.

Algunas opciones, como `props`, `emits` e `inheritAttrs`, se pueden utilizar al escribir componentes con cualquiera de las API. Dado que son opciones del componente, podrían considerarse parte de la Options API. Sin embargo, como estas opciones también se utilizan junto con `setup()`, suele ser más útil pensar en ellas como compartidas entre los dos estilos de componente.

La función `setup()` en sí es una opción del componente, por lo que _podría_ describirse como parte de la Options API. Sin embargo, esto no es lo que normalmente se entiende por el término 'Options API'. En cambio, la función `setup()` se considera parte de la Composition API.

## Plugin {#plugin}

Si bien el término _plugin_ se puede utilizar en una amplia variedad de contextos, Vue tiene un concepto específico de plugin como una forma de agregar funcionalidad a una aplicación.

Los plugins se agregan a una aplicación llamando a `app.use(plugin)`. Un plugin en sí es una función o un objeto con una función `install`. Esa función recibirá la instancia de la aplicación y luego puede hacer lo que necesite hacer.

Para más detalles, consulta:

- [Guía - Plugins](/guide/reusability/plugins.html)

## Prop {#prop}

Hay tres usos comunes del término _prop_ en Vue:

- Props de componente
- Props de VNode
- Props de slot

_Los props del componente_ son a lo que la mayoría de las personas se refieren como props. Estos se definen explícitamente por un componente utilizando `defineProps()` o la opción `props`.

El término _props de VNode_ se refiere a las propiedades del objeto pasado como segundo argumento a `h()`. Estos pueden incluir props de componente, pero también pueden incluir eventos de componente, eventos DOM, atributos DOM y propiedades DOM. Normalmente solo encontrarías props de VNode si estás trabajando con funciones de renderizado para manipular VNodes directamente.

_Los props de slot_ son las propiedades pasadas a un slot con ámbito.

En todos los casos, los props son propiedades que se pasan desde otro lugar.

Aunque la palabra props se deriva de la palabra _propiedades_, el término props tiene un significado mucho más específico en el contexto de Vue. Deberías evitar usarlo como una abreviatura de propiedades.

Para más detalles, consulta:

- [Guía - Propiedades](/guide/components/props.html)
- [Guía - Funciones de Renderizado y JSX](/guide/extras/render-function.html)
- [Guía - Slots - Slots con Ámbito](/guide/components/slots.html#scoped-slots)

## provide / inject {#provide-inject}

`provide` e `inject` son una forma de comunicación entre componentes.

Cuando un componente _proporciona_ un valor, todos los descendientes de ese componente pueden elegir tomar ese valor, usando `inject`. A diferencia de los props, el componente que proporciona no sabe exactamente qué componente está recibiendo el valor.

`provide` e `inject` se usan a veces para evitar la _inyección de props_. También se pueden usar como una forma implícita de que un componente se comunique con el contenido de su slot.

`provide` también se puede usar a nivel de aplicación, haciendo que un valor esté disponible para todos los componentes dentro de esa aplicación.

Para más detalles, consulta:

- [Guía - provide / inject](/guide/components/provide-inject.html)

## Efecto reactivo {#reactive-effect}

Un _efecto reactivo_ es parte del sistema de reactividad de Vue. Se refiere al proceso de realizar un seguimiento de las dependencias de una función y volver a ejecutar esa función cuando los valores de esas dependencias cambien.

`watchEffect()` es la forma más directa de crear un efecto. Varias otras partes de Vue utilizan efectos internamente, como las actualizaciones de renderizado de componentes, `computed()` y `watch()`.

Vue solo puede realizar un seguimiento de las dependencias reactivas dentro de un efecto reactivo. Si el valor de una propiedad se lee fuera de un efecto reactivo, perderá la _reactividad_, en el sentido de que Vue no sabrá qué hacer si esa propiedad cambia posteriormente.

El término se deriva de 'efecto secundario'. Llamar a la función de efecto es un efecto secundario del valor de la propiedad que se está cambiando.

Para más detalles, consulta:

- [Guía - Reactividad en Profundidad](/guide/extras/reactivity-in-depth.html)

## Reactividad {#reactivity}

En general, _reactividad_ se refiere a la capacidad de realizar automáticamente acciones en respuesta a cambios en los datos. Por ejemplo, actualizar el DOM o realizar una solicitud de red cuando cambia el valor de un dato.

En un contexto de Vue, la reactividad se utiliza para describir una colección de características. Esas características se combinan para formar un _sistema de reactividad_, que se expone a través de la [API de Reactividad](#reactivity-api).

Hay varias formas diferentes en que se podría implementar un sistema de reactividad. Por ejemplo, podría hacerse mediante el análisis estático del código para determinar sus dependencias. Sin embargo, Vue no emplea ese tipo de sistema de reactividad.

En cambio, el sistema de reactividad de Vue realiza un seguimiento del acceso a las propiedades en tiempo de ejecución. Esto se hace utilizando envolturas Proxy y funciones [getter](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Functions/set#description)/[setter](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Functions/set#description) para las propiedades.

Para más detalles, consulta:

- [Guía - Fundamentos de la Reactividad](/guide/essentials/reactivity-fundamentals.html)
- [Guía - Reactividad en Profundidad](/guide/extras/reactivity-in-depth.html)

## API de Reactividad {#reactivity-api}

La _API de Reactividad_ es una colección de funciones principales de Vue relacionadas con la [reactividad](#reactividad). Estas funciones se pueden utilizar de manera independiente de los componentes. Incluye funciones como `ref()`, `reactive()`, `computed()`, `watch()` y `watchEffect()`.

La API de Reactividad es un subconjunto de la Composition API.

Para obtener más detalles, consulta:

- [API de Reactividad: Core](/api/reactivity-core.html)
- [API de Reactividad: Utilidades](/api/reactivity-utilities.html)
- [API de Reactividad: Avanzada](/api/reactivity-advanced.html)

## ref {#ref}

> Esta entrada se refiere al uso de `ref` para la reactividad. Para el atributo `ref` utilizado en las plantillas, consulta [referencia de plantilla](#template-ref) en su lugar.

Un `ref` es parte del sistema de reactividad de Vue. Es un objeto con una única propiedad reactiva llamada `value`.

Existen diferentes tipos de refs. Por ejemplo, los refs se pueden crear utilizando `ref()`, `shallowRef()`, `computed()` y `customRef()`. La función `isRef()` se puede utilizar para verificar si un objeto es un ref, y `isReadonly()` se puede utilizar para verificar si el ref permite la reasignación directa de su valor.

Para obtener más detalles, consulta:

- [Guía - Fundamentos de la Reactividad](/guide/essentials/reactivity-fundamentals.html)
- [API de Reactividad: Core](/api/reactivity-core.html)
- [API de Reactividad: Utilidades](/api/reactivity-utilities.html)
- [API de Reactividad: Avanzada](/api/reactivity-advanced.html)

## Función de renderizado {#render-function}

Una _función de renderizado_ es la parte de un componente que genera los VNodes utilizados durante el renderizado. Las plantillas se compilan en funciones de renderizado.

Para obtener más detalles, consulta:

- [Guía - Funciones de Renderizado y JSX](/guide/extras/render-function.html)

## Planificador {#scheduler}

El _planificador_ es la parte interna de Vue que controla el momento en que se ejecutan los [efectos reactivos](#reactive-effect).

Cuando cambia el estado reactivo, Vue no activa inmediatamente las actualizaciones de renderizado. En su lugar, las agrupa utilizando una cola. Esto asegura que un componente solo se vuelva a renderizar una vez, incluso si se realizan múltiples cambios en los datos subyacentes.

[Los watchers](/guide/essentials/watchers.html) también se agrupan mediante la cola del planificador. Los watchers con `flush: 'pre'` (el valor predeterminado) se ejecutarán antes del renderizado del componente, mientras que aquellos con `flush: 'post'` se ejecutarán después del renderizado del componente.

Los trabajos en la cola del planificador también se utilizan para realizar varias otras tareas internas, como activar algunos [hooks de ciclo de vida](#lifecycle-hooks) y actualizar [referencias de plantillas](#template-ref).

## Slot con ámbito {#scoped-slot}

El término _slot con ámbito_ se utiliza para referirse a un [slot](#slot) que recibe [props](#prop).

Históricamente, Vue hacía una distinción mucho mayor entre slots con ámbito y sin ámbito. En cierto modo, se podrían considerar como dos características separadas, unificadas detrás de una sintaxis de plantilla común.

En Vue 3, las API de slots se simplificaron para que todos los slots se comporten como slots con ámbito. Sin embargo, los casos de uso para slots con ámbito y sin ámbito a menudo difieren, por lo que el término sigue siendo útil para referirse a los slots con props.

Los props pasados a un slot solo se pueden utilizar dentro de una región específica de la plantilla principal, que es responsable de definir el contenido del slot. Esta región de la plantilla se comporta como un ámbito de variables para los props, de ahí el nombre 'slot con ámbito'.

Para obtener más detalles, consulta:

- [Guía - Slots - Slots con Ámbito](/guide/components/slots.html#scoped-slots)

## SFC {#sfc}

Ver [Componente de Archivo Único](#single-file-component).

## Efecto secundario {#side-effect}

El término _efecto secundario_ no es específico de Vue. Se utiliza para describir operaciones o funciones que hacen algo más allá de su ámbito local.

Por ejemplo, en el contexto de establecer una propiedad como `user.name = null`, se espera que esto cambie el valor de `user.name`. Si también hace algo más, como activar el sistema de reactividad de Vue, entonces se describiría como un efecto secundario. Este es el origen del término [efecto reactivo](#reactive-effect) dentro de Vue.

Cuando se dice que una función tiene efectos secundarios, significa que la función realiza algún tipo de acción que es observable fuera de la función, además de simplemente devolver un valor. Esto podría significar que actualiza un valor en el estado o activa una solicitud de red.

El término se usa con frecuencia al describir la representación o las propiedades computadas. Se considera una buena práctica que el renderizado no tenga efectos secundarios. De manera similar, la función getter para una propiedad computada no debe tener efectos secundarios.

## Componente de Archivo Único {#single-file-component}

El término _Componente de Archivo Único_, o SFC, se refiere al formato de archivo `.vue` que se utiliza comúnmente para los componentes de Vue.

Consulta también:

- [Guía - Componentes de Archivo Único](/guide/scaling-up/sfc.html)
- [Especificación de Sintaxis de SFC](/api/sfc-spec.html)

## Slot {#slot}

Los slots se utilizan para pasar contenido a los componentes secundarios. Mientras que las props se utilizan para pasar valores de datos, los slots se utilizan para pasar contenido más rico que consiste en elementos HTML y otros componentes de Vue.

Para obtener más detalles, consulta:

- [Guía - Slots](/guide/components/slots.html)

## Referencia de plantilla {#template-ref}

El término _referencia de plantilla_ se refiere al uso de un atributo `ref` en una etiqueta dentro de una plantilla. Después de que el componente se renderiza, este atributo se utiliza para llenar una propiedad correspondiente con el elemento HTML o la instancia del componente que corresponde a la etiqueta en la plantilla.

Si estás utilizando la Options API, entonces las referencias de plantilla se exponen a través de las propiedades del objeto `$refs`.

Con la Composition API, las referencias de plantilla llenan un [ref](#ref) reactivo con el mismo nombre.

Las referencias de plantilla no deben confundirse con los refs reactivos que se encuentran en el sistema de reactividad de Vue.

Para obtener más detalles, consulta:

- [Guía - Referencias de Plantilla](/guide/essentials/template-refs.html)

## VDOM {#vdom}

Ver [DOM virtual](#virtual-dom).

## DOM virtual {#virtual-dom}

El término _DOM virtual_ (VDOM) no es exclusivo de Vue. Es un enfoque común utilizado por varios marcos web para gestionar las actualizaciones en la interfaz de usuario.

Los navegadores utilizan un árbol de nodos para representar el estado actual de la página. Ese árbol y las API de JavaScript utilizadas para interactuar con él se denominan _modelo de objetos del documento_, o _DOM_.

Manipular el DOM es un gran obstáculo para el rendimiento. El DOM virtual proporciona una estrategia para gestionar eso.

En lugar de crear nodos DOM directamente, los componentes de Vue generan una descripción de los nodos DOM que desean. Estos descriptores son objetos JavaScript simples, conocidos como VNodes (nodos DOM virtuales). La creación de VNodes es relativamente económica.

Cada vez que un componente se vuelve a renderizar, el nuevo árbol de VNodes se compara con el árbol anterior de VNodes y se aplican las diferencias al DOM real. Si no ha cambiado nada, el DOM no necesita modificarse.

Vue utiliza un enfoque híbrido que llamamos [DOM Virtual informado por el compilador](/guide/extras/rendering-mechanism.html#compiler-informed-virtual-dom). El compilador de plantillas de Vue puede aplicar optimizaciones de rendimiento basadas en el análisis estático de la plantilla. En lugar de realizar una comparación completa entre los antiguos y nuevos árboles de VNodes de un componente en tiempo de ejecución, Vue puede utilizar información extraída por el compilador para reducir la comparación solo a las partes del árbol que realmente pueden cambiar.

Para obtener más detalles, consulta:

- [Guía - Mecanismo de Renderizado](/guide/extras/rendering-mechanism.html)
- [Guía - Funciones de Renderizado y JSX](/guide/extras/render-function.html)

## VNode {#vnode}

Un _VNode_ es un _nodo del DOM virtual_. Se pueden crear utilizando la función [`h()`](/api/render-function.html#h).

Consulta [DOM virtual](#virtual-dom) para obtener más información.

## Componente Web {#web-component}

El estándar _Componentes Web_ es una colección de funciones implementadas en los navegadores web modernos.

Los componentes de Vue no son Componentes Web, pero `defineCustomElement()` se puede utilizar para crear un [elemento personalizado](#custom-element) a partir de un componente de Vue. Vue también admite el uso de elementos personalizados dentro de los componentes de Vue.

Para obtener más detalles, consulta:

- [Guía - Vue y Componentes Web](/guide/extras/web-components.html)
