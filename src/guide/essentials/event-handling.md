# Manejando Eventos {#event-handling}

<div class="options-api">
  <VueSchoolLink href="https://vueschool.io/lessons/user-events-in-vue-3" title="Lección gratuita de Eventos de Vue.js"/>
</div>

<div class="composition-api">
  <VueSchoolLink href="https://vueschool.io/lessons/vue-fundamentals-capi-user-events-in-vue-3" title="Lección gratuita de Eventos de Vue.js "/>
</div>

## Escuchando Eventos {#listening-to-events}

Podemos utilizar la directiva `v-on`, que normalmente acortamos con el símbolo `@`, para escuchar los eventos del DOM y ejecutar JavaScript cuando se activen. El uso sería `v-on:click="handler"` o con el atajo, `@click="handler"`.

El valor del manejador puede ser uno de los siguientes:

1. **Manejadores en línea:** JavaScript en línea que se ejecutará cuando se active el evento (similar al atributo nativo `onclick`).

2. **Manejadores de métodos:** Un nombre de propiedad o ruta que apunta a un método definido en el componente.

## Manejadores en Línea {#inline-handlers}

Los manejadores en línea suelen utilizarse en casos sencillos, por ejemplo:

<div class="composition-api">

```js
const count = ref(0)
```

</div>
<div class="options-api">

```js
data() {
  return {
    count: 0
  }
}
```

</div>

```vue-html
<button @click="count++">Añadir 1</button>
<p>El Contador está en: {{ count }}</p>
```

<div class="composition-api">

[Pruébalo en la Zona de Práctica](https://play.vuejs.org/#eNo9jssKgzAURH/lko0tgrbbEqX+Q5fZaLxiqHmQ3LgJ+fdqFZcD58xMYp1z1RqRvRgP0itHEJCia4VR2llPkMDjBBkmbzUUG1oII4y0JhBIGw2hh2Znbo+7MLw+WjZ/C4TaLT3hnogPkcgaeMtFyW8j2GmXpWBtN47w5PWBHLhrPzPCKfWDXRHmPsCAaOBfgSOkdH3IGUhpDBWv9/e8vsZZ/gFFhFJN)

</div>
<div class="options-api">

[Pruébalo en la Zona de Práctica](https://play.vuejs.org/#eNo9jcEKgzAQRH9lyKlF0PYqqdR/6DGXaLYo1RjiRgrivzepIizLzu7sm1XUzuVLIFEKObe+d1wpS183eYahtw4DY1UWMJr15ZpmxYAnDt7uF0BxOwXL5Evc0kbxlmyxxZLFyY2CaXSDZkqKZROYJ4tnO/Tt56HEgckyJaraGNxlsVt2u6teHeF40s20EDo9oyGy+CPIYF1xULBt4H6kOZeFiwBZnOFi+wH0B1hk)

</div>

## Manejadores de Métodos {#method-handlers}

La lógica de muchos manejadores de eventos será más compleja aún, y probablemente no sea factible con manejadores en línea. Por eso, `v-on` también puede aceptar el nombre o la ruta de un método del componente que quieras llamar.

Por ejemplo:

<div class="composition-api">

```js
const name = ref('Vue.js')

function greet(event) {
  alert(`Hola ${name.value}!`)
  // `event` es el evento nativo del DOM
  if (event) {
    alert(event.target.tagName)
  }
}
```

</div>
<div class="options-api">

```js
data() {
  return {
    name: 'Vue.js'
  }
},
methods: {
  greet(event) {
    // `this` dentro de los métodos apunta a la instancia activa actual
    alert(`Hola ${this.name}!`)
    // `event` es el evento nativo del DOM
    if (event) {
      alert(event.target.tagName)
    }
  }
}
```

</div>

```vue-html
<!-- `greet` es el nombre del método definido anteriormente -->
<button @click="greet">Saluda</button>
```

<div class="composition-api">

[Pruébalo en la Zona de Práctica](https://play.vuejs.org/#eNpVj0FLxDAQhf/KMwjtXtq7dBcFQS/qzVMOrWFao2kSkkkvpf/dJIuCEBgm771vZnbx4H23JRJ3YogqaM+IxMlfpNWrd4GxI9CMA3NwK5psbaSVVjkbGXZaCediaJv3RN1XbE5FnZNVrJ3FEoi4pY0sn7BLC0yGArfjMxnjcLsXQrdNJtFxM+Ys0PcYa2CEjuBPylNYb4THtxdUobj0jH/YX3D963gKC5WyvGZ+xR7S5jf01yPzeblhWr2ZmErHw0dizivfK6PV91mKursUl6dSh/4qZ+vQ/+XE8QODonDi)

</div>
<div class="options-api">

[Pruébalo en la Zona de Práctica](https://play.vuejs.org/#eNplUE1LxDAQ/StjEbYL0t5LXRQEvag3Tz00prNtNE1CMilC6X83SUkRhJDJfLz3Jm8tHo2pFo9FU7SOW2Ho0in8MdoSDHhlXhKsnQIYGLHyvL8BLJK3KmcAis3YwOnDY/XlTnt1i2G7i/eMNOnBNRkwWkQqcUFFByVAXUNPk3A9COXEgBkGRgtFDkgDTQjcWxuAwDiJBeMsMcUxszCJlsr+BaXUcLtGwiqut930579KST1IBd5Aqlgie3p/hdTIk+IK//bMGqleEbMjxjC+BZVDIv0+m9CpcNr6MDgkhLORjDBm1H56Iq3ggUvBv++7IhnUFZfnGNt6b4fRtj5wxfYL9p+Sjw==)

</div>

Un manejador de método recibe automáticamente el objeto Evento nativo del DOM que lo desencadena. En el ejemplo anterior, podemos acceder al elemento que envía el evento a través de `event.target.tagName`.

<div class="composition-api">

Mira también: [Manejadores de Eventos de Escritura](/guide/typescript/composition-api#typing-event-handlers) <sup class="vt-badge ts" />

</div>
<div class="options-api">

Mira también: [Manejadores de Eventos de Escritura](/guide/typescript/options-api#typing-event-handlers) <sup class="vt-badge ts" />

</div>

### Método vs. la Detección en Línea {#method-vs-inline-detection}

El compilador de plantillas detecta los manejadores de métodos comprobando si la cadena de valores `v-on` es un identificador JavaScript válido o una ruta de acceso a una propiedad. Por ejemplo, `foo`, `foo.bar` y `foo['bar']` se tratan como manejadores de métodos, mientras que `foo()` y `count++` se tratan como manejadores en línea.

## Llamando Métodos en Manejadores en Línea {#calling-methods-in-inline-handlers}

En lugar de vincularlos directamente a un nombre de un método, también podemos llamar a los métodos en un manejador en línea. Esto nos permite pasar los argumentos personalizados del método en lugar del evento nativo:

<div class="composition-api">

```js
function say(message) {
  alert(message)
}
```

</div>
<div class="options-api">

```js
methods: {
  say(message) {
    alert(message)
  }
}
```

</div>

```vue-html
<button @click="say('hello')">Di hola</button>
<button @click="say('bye')">Di adiós</button>
```

<div class="composition-api">

[Pruébalo en la Zona de Práctica](https://play.vuejs.org/#eNp9jTEOwjAMRa8SeSld6I5CBWdg9ZJGBiJSN2ocpKjq3UmpFDGx+Vn//b/ANYTjOxGcQEc7uyAqkqTQI98TW3ETq2jyYaQYzYNatSArZTzNUn/IK7Ludr2IBYTG4I3QRqKHJFJ6LtY7+zojbIXNk7yfmhahv5msvqS7PfnHGjJVp9w/hu7qKKwfEd1NSg==)

</div>
<div class="options-api">

[Pruébalo en la Zona de Práctica](https://play.vuejs.org/#eNptjUEKwjAQRa8yZFO7sfsSi57B7WzGdjTBtA3NVC2ldzehEFwIw8D7vM9f1cX742tmVSsd2sl6aXDgjx8ngY7vNDuBFQeAnsWMXagToQAEWg49h0APLncDAIUcT5LzlKJsqRBfPF3ljQjCvXcknEj0bRYZBzi3zrbPE6o0UBhblKiaKy1grK52J/oA//23IcmNBD8dXeVBtX0BF0pXsg==)

</div>

## Acceso al Argumento del Evento en los Manejadores en Línea {#accessing-event-argument-in-inline-handlers}

A veces también necesitamos acceder al evento original del DOM en un manejador en línea. Puedes pasarlo a un método usando la variable especial `$event`, o usar una función de flecha en línea:

```vue-html
<!-- utilizando la variable especial $event -->
<button @click="warn('Aún no se puede enviar el formulario.', $event)">
  Enviar
</button>

<!-- utilizando la función de flecha en línea -->
<button @click="(event) => warn('Aún no se puede enviar el formulario.', event)">
  Enviar
</button>
```

<div class="composition-api">

```js
function warn(message, event) {
  // ahora tenemos acceso al evento nativo
  if (event) {
    event.preventDefault()
  }
  alert(message)
}
```

</div>
<div class="options-api">

```js
methods: {
  warn(message, event) {
    // ahora tenemos acceso al evento nativo
    if (event) {
      event.preventDefault()
    }
    alert(message)
  }
}
```

</div>

## Modificadores de Eventos {#event-modifiers}

Es una necesidad muy común llamar a `event.preventDefault()` o `event.stopPropagation()` dentro de los manejadores de eventos. Aunque podemos hacer esto fácilmente dentro de los métodos, sería mejor si los métodos pueden ser puramente sobre la lógica de los datos en lugar de tener que lidiar con los detalles de los eventos del DOM.

Para solucionar este problema, Vue proporciona **modificadores de eventos** para `v-on`. Recordemos que los modificadores son directivas postfijas denotadas por un punto.

- `.stop`
- `.prevent`
- `.self`
- `.capture`
- `.once`
- `.passive`

```vue-html
<!-- se detendrá la propagación del evento clic -->
<a @click.stop="doThis"></a>

<!-- el evento de envío ya no recargará la página -->
<form @submit.prevent="onSubmit"></form>

<!-- los modificadores se pueden encadenar -->
<a @click.stop.prevent="doThat"></a>

<!-- sólo el modificador -->
<form @submit.prevent></form>

<!-- sólo activar el manejador si event.target es del propio elemento -->
<!-- es decir, no de un elemento hijo -->
<div @click.self="doThat">...</div>
```

::: tip
El orden importa cuando se utilizan modificadores porque el código relevante se genera en el mismo orden. Por lo tanto, el uso de `@click.prevent.self` impedirá **la acción por defecto de los clics en el propio elemento y sus hijos** mientras que `@click.self.prevent` sólo impedirá la acción por defecto de los clics en el propio elemento.
:::

Los modificadores `.capture`, `.once` y `.passive` reflejan las [opciones del método nativo `addEventListener`](https://developer.mozilla.org/en-US/docs/Web/API/EventTarget/addEventListener#Parameters):

```vue-html
<!-- utiliza el modo de captura al añadir el receptor de eventos -->
<!-- es decir, un evento dirigido a un elemento interior es manejado aquí antes de ser manejado por ese elemento -->
<div @click.capture="doThis">...</div>

<!-- el evento clic se activará como máximo una vez -->
<a @click.once="doThis"></a>

<!-- el comportamiento por defecto del evento scroll (desplazamiento)  -->
<!-- se producirá inmediatamente, en lugar de esperar a que `onScroll` -->
<!-- se complete en caso de que contenga `event.preventDefault()`.     -->
<div @scroll.passive="onScroll">...</div>
```

El modificador `.passive` se suele utilizar con los escuchadores de eventos táctiles para [mejorar el rendimiento en los dispositivos móviles](https://developer.mozilla.org/en-US/docs/Web/API/EventTarget/addEventListener#improving_scrolling_performance_with_passive_listeners).

::: tip
No utilice `.passive` y `.prevent` juntos, porque `.passive` ya le indica al navegador que _no_ pretendes impedir el comportamiento por defecto del evento, y es probable que veas una advertencia del navegador si lo haces.
:::

## Modificadores Clave {#key-modifiers}

Cuando escuchamos eventos de teclado, a menudo necesitamos comprobar teclas específicas. Vue permite añadir modificadores de tecla para `v-on` o `@` al escuchar eventos de teclado:

```vue-html
<!-- sólo llama a `submit` cuando la `key` es `Enter` -->
<input @keyup.enter="submit" />
```

Puedes utilizar directamente cualquier nombre de tecla válido expuesto a través de [`KeyboardEvent.key`](https://developer.mozilla.org/en-US/docs/Web/API/KeyboardEvent/key/Key_Values) como modificadores convirtiéndolos en kebab-case.

```vue-html
<input @keyup.page-down="onPageDown" />
```

En el ejemplo anterior, el controlador sólo será llamado si `$event.key` es igual a `'PageDown'`.

### Alias de las Teclas {#key-aliases}

Vue proporciona alias para las teclas más utilizadas:

- `.enter`
- `.tab`
- `.delete` (captura las teclas "Borrar" y "Retroceso")
- `.esc`
- `.space`
- `.up`
- `.down`
- `.left`
- `.right`

### Teclas Modificadoras del Sistema {#system-modifier-keys}

Puedes utilizar los siguientes modificadores para activar los receptores de eventos del ratón o del teclado sólo cuando se pulse la tecla modificadora correspondiente:

- `.ctrl`
- `.alt`
- `.shift`
- `.meta`

::: tip Note
En los teclados Macintosh, meta es la tecla de comando (⌘). En los teclados Windows, meta es la tecla Windows (⊞). En los teclados de Sun Microsystems, meta está marcada como un diamante sólido (◆). En ciertos teclados, específicamente en los teclados de las máquinas MIT, Lisp y sus sucesores, como el teclado Knight y el teclado space-cadet, meta está etiquetado como "META". En los teclados Symbolics, meta lleva la etiqueta "META" o "Meta"
:::

Por ejemplo:

```vue-html
<!-- Alt + Enter -->
<input @keyup.alt.enter="clear" />

<!-- Ctrl + Click -->
<div @click.ctrl="doSomething">Haz algo</div>
```

::: tip
Ten en cuenta que las teclas modificadoras son diferentes de las teclas normales y cuando se usan con eventos `keyup`, tienen que estar pulsadas cuando se emite el evento. En otras palabras, `keyup.ctrl` sólo se activará si sueltas una tecla mientras mantienes pulsada `ctrl`. No se activará si sueltas la tecla "ctrl" sola.
:::

### Modificador `.exact` {#exact-modifier}

El modificador `.exact` permite controlar la combinación exacta de modificadores del sistema necesarios para activar un evento.

```vue-html
<!-- esto se disparará incluso si se pulsa también Alt o Shift -->
<button @click.ctrl="onClick">A</button>

<!-- esto sólo se disparará cuando se pulse Ctrl y ninguna otra tecla -->
<button @click.ctrl.exact="onCtrlClick">A</button>

<!-- esto sólo se disparará cuando no se pulsen modificadores del sistema -->
<button @click.exact="onClick">A</button>
```

## Modificadores del Botón del Ratón {#mouse-button-modifiers}

- `.left`
- `.right`
- `.middle`

Estos modificadores restringen el manejador a los eventos desencadenados por un botón específico del ratón.
