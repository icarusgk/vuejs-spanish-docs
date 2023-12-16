---
outline: deep
---

<script setup>
import { ref } from 'vue'
const message = ref('')
const multilineText = ref('')
const checked = ref(false)
const checkedNames = ref([])
const picked = ref('')
const selected = ref('')
const multiSelected = ref([])
</script>

# Vinculación de Entradas de Formularios {#form-input-bindings}

<div class="options-api">
<VueSchoolLink href="https://vueschool.io/lessons/user-inputs-vue-devtools-in-vue-3" title=" Lección gratuita sobre Entradas del Usuario con Vue.js"/>
</div>

<div class="composition-api">
<VueSchoolLink href="https://vueschool.io/lessons/vue-fundamentals-capi-user-inputs-in-vue" title=" Lección gratuita sobre Entradas del Usuario con Vue.js"/>
</div>

Cuando tratamos con formularios en el frontend, a menudo necesitamos sincronizar el estado de los elementos de entrada del formulario con el estado correspondiente en JavaScript. Puede ser incómodo configurar manualmente los enlaces de valores y cambiar los escuchadores de eventos:

```vue-html
<input
  :value="text"
  @input="event => text = event.target.value">
```

La directiva `v-model` nos ayuda a simplificar lo anterior a:

```vue-html
<input v-model="text">
```

Además, `v-model` se puede utilizar en entradas de diferentes tipos, `<textarea>`, y elementos `<select>`. Esto se propaga automáticamente a diferentes propiedades del DOM y pares de eventos basados en el elemento en el que se utiliza:

- `<input>` con tipos de texto y elementos `<textarea>` utilizan la propiedad `value` y el evento `input`;
- `<input type="checkbox">` al igual que `<input type="radio">` utilizan la propiedad `checked` y el evento `change`;
- `<select>` utiliza la propiedad `value` y `change` como un evento.

::: tip Nota
`v-model` ignorará los atributos iniciales `value`, `checked` o `selected` encontrados en cualquier elemento del formulario. Este siempre tratará el estado del JavaScript enlazado actual como la fuente de la verdad. Debes declarar el valor inicial en el lado de JavaScript, utilizando <span class="options-api">la opción `data`</span><span class="composition-api">las APIs de reactividad</span>.
:::

## Uso Básico {#basic-usage}

### Texto {#text}

```vue-html
<p>El mensaje es: {{ message }}</p>
<input v-model="message" placeholder="edítame" />
```

<div class="demo">
  <p>El mensaje es: {{ message }}</p>
<input v-model="message" placeholder="edítame" />
</div>

<div class="composition-api">

[Pruébalo en la Zona de Práctica](https://play.vuejs.org/#eNo9jUEOgyAQRa8yYUO7aNkbNOkBegM2RseWRGACoxvC3TumxuX/+f+9ql5Ez31D1SlbpuyJoSBvNLjoA6XMUCHjAg2WnAJomWoXXZxSLAwBSxk/CP2xuWl9d9GaP0YAEhgDrSOjJABLw/s8+NJBrde/NWsOpWPrI20M+yOkGdfeqXPiFAhowm9aZ8zS4+wPv/RGjtZcJtV+YpNK1g==)

</div>
<div class="options-api">

[Pruébalo en la Zona de Práctica](https://play.vuejs.org/#eNo9jdEKwjAMRX8l9EV90L2POvAD/IO+lDVqoetCmw6h9N/NmBuEJPeSc1PVg+i2FFS90nlMnngwEb80JwaHL1sCQzURwFm258u2AyTkkuKuACbM2b6xh9Nps9o6pEnp7ggWwThRsIyiADQNz40En3uodQ+C1nRHK8HaRyoMy3WaHYa7Uf8To0CCRvzMwWESH51n4cXvBNTd8Um1H0FuTq0=)

</div>

<span id="vmodel-ime-tip"></span>
::: tip Nota
Para los lenguajes que requieren un [IME](https://en.wikipedia.org/wiki/Input_method) (Chino, Japonés, Coreano, etc.), notarás que el `v-model` no se actualiza durante la composición del IME. Si quieres responder también a estas actualizaciones, utiliza un receptor de eventos `input` y un enlace `value` en lugar de utilizar `v-model`.
:::

### Texto Multilínea {#multiline-text}

```vue-html
<span>El mensaje multilínea es:</span>
<p style="white-space: pre-line;">{{ message }}</p>
<textarea v-model="message" placeholder="agrega múltiples líneas"></textarea>
```

<div class="demo">
  <span>El mensaje multilínea es:</span>
  <p style="white-space: pre-line;">{{ message }}</p>
  <textarea v-model="message" placeholder="agrega múltiples líneas"></textarea>
</div>

<div class="composition-api">

[Pruébalo en la Zona de Práctica](https://play.vuejs.org/#eNo9jktuwzAMRK9CaON24XrvKgZ6gN5AG8FmGgH6ECKdJjB891D5LYec9zCb+SH6Oq9oRmN5roEEGGWlyeWQqFSBDSoeYYdjLQk6rXYuuzyXzAIJmf0fwqF1Prru02U7PDQq0CCYKHrBlsQy+Tz9rlFCDBnfdOBRqfa7twhYrhEPzvyfgmCvnxlHoIp9w76dmbbtDe+7HdpaBQUv4it6OPepLBjV8Gw5AzpjxlOJC1a9+2WB1IZQRGhWVqsdXgb1tfDcbvYbJDRqLQ==)

</div>
<div class="options-api">

[Pruébalo en la Zona de Práctica](https://play.vuejs.org/#eNo9jk2OwyAMha9isenMIpN9hok0B+gN2FjBbZEIscDpj6LcvaZpKiHg2X6f32L+mX+uM5nO2DLkwNK7RHeesoCnE85RYHEJwKPg1/f2B8gkc067AhipFDxTB4fDVlrro5ce237AKoRGjihUldjCmPqjLgkxJNoxEEqnrtp7TTEUeUT6c+Z2CUKNdgbdxZmaavt1pl+Wj3ldbcubUegumAnh2oyTp6iE95QzoDEGukzRU9Y6eg9jDcKRoFKLUm27E5RXxTu7WZ89/G4E)

</div>

Observa que la interpolación dentro del `<textarea>` no funcionará. En su lugar, utiliza `v-model`.

```vue-html
<!-- erróneo -->
<textarea>{{ text }}</textarea>

<!-- correcto -->
<textarea v-model="text"></textarea>
```

### Checkbox {#checkbox-1}

Un único checkbox, con valor booleano:

```vue-html
<input type="checkbox" id="checkbox" v-model="checked" />
<label for="checkbox">{{ checked }}</label>
```

<div class="demo">
  <input type="checkbox" id="checkbox-demo" v-model="checked" />
  <label for="checkbox-demo">{{ checked }}</label>
</div>

<div class="composition-api">

[Pruébalo en la Zona de Práctica](https://play.vuejs.org/#eNpVjssKgzAURH/lko3tonVfotD/yEaTKw3Ni3gjLSH/3qhUcDnDnMNk9gzhviRkD8ZnGXUgmJFS6IXTNvhIkCHiBAWm6C00ddoIJ5z0biaQL5RvVNCtmwvFhFfheLuLqqIGQhvMQLgm4tqFREDfgJ1gGz36j2Cg1TkvN+sVmn+JqnbtrjDDiAYmH09En/PxphTebqsK8PY4wMoPslBUxQ==)

</div>
<div class="options-api">

[Pruébalo en la Zona de Práctica](https://play.vuejs.org/#eNpVjtEKgzAMRX8l9Gl72Po+OmH/0ZdqI5PVNnSpOEr/fVVREEKSc0kuN4sX0X1KKB5Cfbs4EDfa40whMljsTXIMWXsAa9hcrtsOEJFT9DsBdG/sPmgfwDHhJpZl1FZLycO6AuNIzjAuxGrwlBj4R/jUYrVpw6wFDPbM020MFt0uoq2a3CycadFBH+Lpo8l5jwWlKLle1QcljwCi/AH7gFic)

</div>

También podemos vincular múltiples checkboxes al mismo array o valor [Set](https://developer.mozilla.org/es/docs/Web/JavaScript/Reference/Global_Objects/Set):

<div class="composition-api">

```js
const checkedNames = ref([])
```

</div>
<div class="options-api">

```js
export default {
  data() {
    return {
      checkedNames: []
    }
  }
}
```

</div>

```vue-html
<div>Nombres verificados: {{ checkedNames }}</div>

<input type="checkbox" id="jack" value="Jack" v-model="checkedNames">
<label for="jack">Jack</label>

<input type="checkbox" id="john" value="John" v-model="checkedNames">
<label for="john">John</label>

<input type="checkbox" id="mike" value="Mike" v-model="checkedNames">
<label for="mike">Mike</label>
```

<div class="demo">
  <div>Nombres verificados: {{ checkedNames }}</div>

  <input type="checkbox" id="demo-jack" value="Jack" v-model="checkedNames">
  <label for="demo-jack">Jack</label>

  <input type="checkbox" id="demo-john" value="John" v-model="checkedNames">
  <label for="demo-john">John</label>

  <input type="checkbox" id="demo-mike" value="Mike" v-model="checkedNames">
  <label for="demo-mike">Mike</label>
</div>

En este caso, el array `checkedNames` siempre contendrá los valores de las casillas seleccionadas en ese momento.

<div class="composition-api">

[Pruébalo en la Zona de Práctica](https://play.vuejs.org/#eNqVkUtqwzAURbfy0CTtoNU8KILSWaHdQNWBIj8T1fohyybBeO+RbOc3i2e+vHvuMWggHyG89x2SLWGtijokaDF1gQunbfAxwQARaxihjt7CJlc3wgmnvGsTqAOqBqsfabGFXSm+/P69CsfovJVXckhog5EJcwJgle7558yBK+AWhuFxaRwZLbVCZ0K70CVIp4A7Qabi3h8FAV3l/C9Vk797abpy/lrim/UVmkt/Gc4HOv+EkXs0UPt4XeCFZHQ6lM4TZn9w9+YlrjFPCC/kKrPVDd6Zv5e4wjwv8ELezIxeX4qMZwHduAs=)

</div>
<div class="options-api">

[Pruébalo en la Zona de Práctica](https://play.vuejs.org/#eNqVUc1qxCAQfpXBU3tovS9WKL0V2hdoenDjLGtjVNwxbAl592rMpru3DYjO5/cnOLLXEJ6HhGzHxKmNJpBsHJ6DjwQaDypZgrFxAFqRenisM0BEStFdEEB7xLZD/al6PO3g67veT+XIW16Cr+kZEPbBKsKMAIQ2g3yrAeBqwjjeRMI0CV5kxZ0dxoVEQL8BXxo2C/f+3DAwOuMf1XZ5HpRNhX5f4FPvNdqLfgnOBK+PsGqPFg4+rgmyOAWfiaK5o9kf3XXzArc0zxZZnJuae9PhVfPHAjc01wRZnP/Ngq8/xaY/yMW74g==)

</div>

### Radio {#radio-1}

```vue-html
<div>Seleccionado: {{ picked }}</div>

<input type="radio" id="one" value="Uno" v-model="picked" />
<label for="one">Uno</label>

<input type="radio" id="two" value="Dos" v-model="picked" />
<label for="two">Dos</label>
```

<div class="demo">
  <div>Seleccionado: {{ picked }}</div>

  <input type="radio" id="one" value="Uno" v-model="picked" />
  <label for="one">Uno</label>

  <input type="radio" id="two" value="Dos" v-model="picked" />
  <label for="two">Dos</label>
</div>

<div class="composition-api">

[Pruébalo en la Zona de Práctica](https://play.vuejs.org/#eNqFkDFuwzAMRa9CaHE7tNoDxUBP0A4dtTgWDQiRJUKmHQSG7x7KhpMMAbLxk3z/g5zVD9H3NKI6KDO02RPDgDxSbaPvKWWGGTJ2sECXUw+VrFY22timODCQb8/o4FhWPqrfiNWnjUZvRmIhgrGn0DCKAjDOT/XfCh1gnnd+WYwukwJYNj7SyMBXwqNVuXE+WQXeiUgRpZyaMJaR5BX11SeHQfTmJi1dnNiE5oQBupR3shbC6LX9Posvpdyz/jf1OksOe85ayVqIR5bR9z+o5Qbc6oCk)

</div>
<div class="options-api">

[Pruébalo en la Zona de Práctica](https://play.vuejs.org/#eNqNkEEOAiEMRa/SsFEXyt7gJJ5AFy5ng1ITIgLBMmomc3eLOONSEwJ9Lf//pL3YxrjqMoq1ULdTspGa1uMjhkRg8KyzI+hbD2A06fmi1gAJKSc/EkC0pwuaNcx2Hme1OZSHLz5KTtYMhNfoNGEhUsZ2zf6j7vuPEQyDkmVSBPzJ+pgJ6Blx04qkjQ2tAGsYgkcuO+1yGXF6oeU1GHTM1Y1bsoY5fUQH55BGZcMKJd/t31l0L+WYdaj0V9Zb2bDim6XktAcxvADR+YWb)

</div>

### Select {#select}

Select Simple:

```vue-html
<div>Selección: {{ selected }}</div>

<select v-model="selected">
  <option disabled value="">Por favor, selecciona uno</option>
  <option>A</option>
  <option>B</option>
  <option>C</option>
</select>
```

<div class="demo">
  <div>Selección: {{ selected }}</div>
  <select v-model="selected">
    <option disabled value="">Por favor, selecciona uno</option>
    <option>A</option>
    <option>B</option>
    <option>C</option>
  </select>
</div>

<div class="composition-api">

[Pruébalo en la Zona de Práctica](https://play.vuejs.org/#eNp1j7EOgyAQhl/lwmI7tO4Nmti+QJOuLFTPxASBALoQ3r2H2jYOjvff939wkTXWXucJ2Y1x37rBBvAYJlsLPYzWuAARHPaQoHdmhILQQmihW6N9RhW2ATuoMnQqirPQvFw9ZKAh4GiVDEgTAPdW6hpeW+sGMf4VKVEz73Mvs8sC5stoOlSVYF9SsEVGiLFhMBq6wcu3IsUs1YREEvFUKD1udjAaebnS+27dHOT3g/yxy+nHywM08PJ3KksfXwJ2dA==)

</div>
<div class="options-api">

[Pruébalo en la Zona de Práctica](https://play.vuejs.org/#eNp1j1ELgyAUhf/KxZe2h633cEHbHxjstReXdxCYSt5iEP333XIJPQSinuN3jjqJyvvrOKAohAxN33oqa4tf73oCjR81GIKptgBakTqd4x6gRxp6uymAgAYbQl1AlkVvXhaeeMg8NbMg7LxRhKwAZPDKlvBK8WlKXTDPnFzOI7naMF46p9HcarFxtVgBRpyn1lnQbVBvwwWjMgMyycTToAr47wZnUeaR3mfL6sC/H/iPnc/vXS9gIfP0UTH/ACgWeYE=)

</div>

:::tip Nota
Si el valor inicial de tu expresión `v-model` no coincide con ninguna de las opciones, el elemento `<select>` se mostrará en un estado "no seleccionado". En iOS esto hará que el usuario no pueda seleccionar el primer elemento porque iOS no dispara un evento de cambio en este caso. Por lo tanto, se recomienda proporcionar una opción deshabilitada con un valor vacío, como se demuestra en el ejemplo anterior.
:::

Selección múltiple (vinculada a un array):

```vue-html
<div>Seleccionado: {{ selected }}</div>

<select v-model="selected" multiple>
  <option>A</option>
  <option>B</option>
  <option>C</option>
</select>
```

<div class="demo">
  <div>Seleccionado: {{ multiSelected }}</div>

  <select v-model="multiSelected" multiple>
    <option>A</option>
    <option>B</option>
    <option>C</option>
  </select>
</div>

<div class="composition-api">

[Pruébalo en la Zona de Práctica](https://play.vuejs.org/#eNp1kL2OwjAQhF9l5Ya74i7QBhMJeARKTIESIyz5Z5VsAsjyu7NOQEBB5xl/M7vaKNaI/0OvRSlkV7cGCTpNPVbKG4ehJYjQ6hMkOLXBwYzRmfLK18F3GbW6Jt3AKkM/+8Ov8rKYeriBBWmH9kiaFYBszFDtHpkSYnwVpCSL/JtDDE4+DH8uNNqulHiCSoDrLRm0UyWzAckEX61l8Xh9+psv/vbD563HCSxk8bY0y45u47AJ2D/HHyDm4MU0dC5hMZ/jdal8Gg8wJkS6A3nRew4=)

</div>
<div class="options-api">

[Pruébalo en la Zona de Práctica](https://play.vuejs.org/#eNp1UEEOgjAQ/MqmJz0oeMVKgj7BI3AgdI1NCjSwIIbwdxcqRA4mTbsznd2Z7CAia49diyIQsslrbSlMSuxtVRMofGStIRiSEkBllO32rgaokdq6XBBAgwZzQhVAnDpunB6++EhvncyAsLAmI2QEIJXuwvvaPAzrJBhH6U2/UxMLHQ/doagUmksiFmEioOCU2ho3krWVJV2VYSS9b7Xlr3/424bn1LMDA+n9hGbY0Hs2c4J4sU/dPl5a0TOAk+/b/rwsYO4Q4wdtRX7l)

</div>

Las opciones de selección se pueden representar dinámicamente con `v-for`:

<div class="composition-api">

```js
const selected = ref('A')

const options = ref([
  { text: 'Uno', value: 'A' },
  { text: 'Dos', value: 'B' },
  { text: 'Tres', value: 'C' }
])
```

</div>
<div class="options-api">

```js
export default {
  data() {
    return {
      selected: 'A',
      options: [
        { text: 'Uno', value: 'A' },
        { text: 'Dos', value: 'B' },
        { text: 'Tres', value: 'C' }
      ]
    }
  }
}
```

</div>

```vue-html
<select v-model="selected">
  <option v-for="option in options" :value="option.value">
    {{ option.text }}
  </option>
</select>

<div>Seleccionado: {{ selected }}</div>
```

<div class="composition-api">

[Pruébalo en la Zona de Práctica](https://play.vuejs.org/#eNplkMFugzAQRH9l5YtbKYU7IpFoP6CH9lb3EMGiWgLbMguthPzvXduEJMqNYUazb7yKxrlimVFUop5arx3BhDS7kzJ6dNYTrOCxhwC9tyNIjkpllGmtmWJ0wJawg2MMPclGPl9N60jzx+Z9KQPcRfhHFch3g/IAy3mYkVUjIRzu/M9fe+O/Pvo/Hm8b3jihzDdfr8s8gwewIBzdcCZkBVBnXFheRtvhcFTiwq9ECnAkQ3Okt54Dm9TmskYJqNLR3SyS3BsYct3CRYSFwGCpusx/M0qZTydKRXWnl9PHBlPFhv1lQ6jL6MZl+xoR/gFjPZTD)

</div>
<div class="options-api">

[Pruébalo en la Zona de Práctica](https://play.vuejs.org/#eNp1kMFqxCAQhl9l8JIWtsk92IVtH6CH9lZ7COssDbgqZpJdCHn3nWiUXBZE/Mdvxv93Fifv62lE0Qo5nEPv6ags3r0LBBov3WgIZmUBdEfdy2s6AwSkMdisAAY0eCbULVSn6pCrzlPv7NDCb64AzEB4J+a+LFYHmDozYuyCpfTtqJ+b21Efz6j/gPtpn8xl7C8douaNl2xKUhaEV286QlYAMgWB6e3qNJp3JXIyJSLASErFyMUFBjbZ2xxXCWijkXJZR1kmsPF5g+s1ACybWdmkarLSpKejS0VS99Pxu3wzT8jOuF026+2arKQRywOBGJfE)

</div>

## Vinculación de Valores {#value-bindings}

En las opciones radio, checkbox y select, los valores de enlace del `v-model` suelen ser cadenas estáticas (o booleanas en el caso de los checkbox):

```vue-html
<!-- `picked` es una cadena "a" cuando se selecciona -->
<input type="radio" v-model="picked" value="a" />

<!-- `toggle` puede ser verdadero o falso -->
<input type="checkbox" v-model="toggle" />

<!-- `selected` es una cadena "abc" cuando se selecciona la primera opción -->
<select v-model="selected">
  <option value="abc">ABC</option>
</select>
```

Pero en ocasiones podemos querer vincular el valor a una propiedad dinámica de la instancia activa actual. Para ello podemos utilizar `v-bind`. Además, el uso de `v-bind` nos permite vincular el valor de entrada a valores que no son de cadena.

### Checkbox {#select-options}

```vue-html
<input
  type="checkbox"
  v-model="toggle"
  true-value="sí"
  false-value="no" />
```

`true-value` y `false-value` son atributos específicos de Vue que sólo funcionan con `v-model`. En este caso, el valor de la propiedad `toggle` se establecerá en `'sí'` cuando la casilla esté marcada, y en `'no'` cuando esté desmarcada. También puedes vincularlos a valores dinámicos usando `v-bind`:

```vue-html
<input
  type="checkbox"
  v-model="toggle"
  :true-value="dynamicTrueValue"
  :false-value="dynamicFalseValue" />
```

:::tip Tip
Los atributos `true-value` y `false-value` no afectan al atributo `value` de la entrada, porque los navegadores no incluyen las casillas sin marcar en los envíos de formularios. Para garantizar que uno de los dos valores se envíe en un formulario (por ejemplo, "sí" o "no"), utiliza entradas de radio en su lugar.
:::

### Radio {#modifiers}

```vue-html
<input type="radio" v-model="pick" :value="primero" />
<input type="radio" v-model="pick" :value="segundo" />
```

`pick` se establecerá con el valor de `primero` cuando se marque la primera entrada de radio, y se establecerá con el valor de `segundo` cuando se marque la segunda.

### Seleccionar Opciones {#lazy}

```vue-html
<select v-model="selected">
  <!-- objeto literal en línea -->
  <option :value="{ number: 123 }">123</option>
</select>
```

`v-model` admite también la vinculación de valores que no sean cadenas. En el ejemplo anterior, cuando se selecciona la opción, `selected` se establecerá en el valor literal del objeto de `{ number: 123 }`.

## Modificadores {#number}

### `.lazy` {#trim}

Por defecto, `v-model` sincroniza la entrada con los datos después de cada evento `input` (con la excepción de la composición IME, como se ha indicado [anteriormente](#vmodel-ime-tip)). Puedes añadir el modificador `lazy` para sincronizar después de los eventos `change`:

```vue-html
<!-- sincronizado después de "change" en lugar de "input" -->
<input v-model.lazy="msg" />
```

### `.number` {#v-model-with-components}

Si quieres que la entrada del usuario sea automáticamente tipificada como un número, puedes añadir el modificador `number` a tus entradas gestionadas por `v-model`:

```vue-html
<input v-model.number="age" />
```

Si el valor no puede ser procesado con `parseFloat()`, entonces se utiliza el valor original.

El modificador `number` se aplica automáticamente si la entrada tiene `type="number"`.

### `.trim` 

Si quieres que los espacios en blanco de la entrada del usuario se recorten automáticamente, puedes añadir el modificador `trim` a tus entradas gestionadas por `v-model`:

```vue-html
<input v-model.trim="msg" />
```

## `v-model` con Componentes 

> Si aún no estás familiarizado con los componentes de Vue, puedes saltarte esto por ahora.

Los tipos de entrada incorporados en HTML no siempre satisfacen tus necesidades. Afortunadamente, los componentes de Vue te permiten construir entradas reutilizables con un comportamiento completamente personalizado. ¡Estas entradas incluso funcionan con `v-model`! Para aprender más, lee sobre [Uso con `v-model`](/guide/components/v-model) en la guía de Componentes.
