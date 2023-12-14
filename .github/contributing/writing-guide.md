# Guía de Escritura de Documentación de Vue

Escribir documentación es un ejercicio de empatía. No estamos describiendo una realidad objetiva: el código fuente ya hace eso. Nuestro trabajo es ayudar a dar forma a la relación entre los usuarios y el ecosistema Vue. Esta guía, que está en constante evolución, proporciona reglas y recomendaciones sobre cómo hacerlo de manera consistente dentro del ecosistema de Vue.

## Principios

- **Una característica no existe hasta que está bien documentada.**
- **Respeta la capacidad cognitiva de los usuarios (es decir, la energía cerebral).** Cuando un usuario comienza a leer, lo hace con una cierta cantidad de energía cerebral limitada, y cuando se agota, deja de aprender.
- La capacidad cognitiva **se agota más rápido** con oraciones complejas, tener que aprender más de un concepto a la vez y ejemplos abstractos que no se relacionan directamente con el trabajo del usuario.
- La capacidad cognitiva **se agota más lentamente** cuando los ayudamos a sentirse consistentemente inteligentes, poderosos y curiosos. Desglosar las cosas en piezas digeribles y prestar atención al flujo del documento puede ayudar a mantenerlos en este estado.
- **Siempre intenta ver desde la perspectiva del usuario.** Cuando entendemos algo a fondo, nos resulta obvio. Esto se llama la _maldición del conocimiento_. Para escribir una buena documentación, intenta recordar lo que necesitabas saber al principio al aprender este concepto. ¿Qué jerga necesitabas aprender? ¿Qué malentendiste? ¿Qué te llevó mucho tiempo entender realmente? Una buena documentación les permite saber a los usuarios donde están. Puede ser útil el practicar explicar el concepto a gente en persona antes.
- **Describe el _problema_ primero, luego la solución.** Antes de mostrar cómo funciona una característica, es importante explicar por qué existe. De lo contrario, los usuarios no tendrán el contexto para saber si esta información es importante para ellos (¿es un problema que experimentan?) o qué conocimientos/experiencia previa conectar.
- **Mientras escribes, no tengas miedo de hacer preguntas**, _especialmente_ si temes que tus preguntas puedan ser "tontas". Ser vulnerable es difícil, pero es la única manera de entender más completamente lo que necesitamos explicar.
- **Participa en las discusiones sobre características.** Las mejores APIs provienen del desarrollo impulsado por la documentación, donde construimos características fáciles de explicar en lugar de tratar de descubrir cómo explicarlas más tarde. Hacer preguntas (especialmente preguntas "tontas") desde antes a menudo ayuda a revelar confusiones, inconsistencias y comportamientos problemáticos antes de que sea necesario un cambio que quiebre la compatibilidad para corregirlos.

## Organización

- **Instalación/Integración:** Proporciona una descripción general exhaustiva de cómo integrar el software en tantos proyectos diferentes como sea necesario.
- **Introducción/Primeros Pasos:**
  - Proporciona una descripción general de menos de 10 minutos de los problemas que resuelve el proyecto y por qué existe.
  - Proporciona una descripción general de menos de 30 minutos de los problemas que resuelve el proyecto y cómo, incluyendo cuándo y por qué usar el proyecto y algunos ejemplos de código simples. Al final, vincula tanto a la página de Instalación como al comienzo de la Guía de Elementos Esenciales.
- **Guía:** Haz que los usuarios se sientan inteligentes, poderosos y curiosos, luego mantén este estado para que conserven la motivación y la capacidad cognitiva para seguir aprendiendo más. Las páginas de la guía están destinadas a leerse de manera secuencial, por lo que generalmente deben ordenarse de mayor a menor proporción de potencia/esfuerzo.
  - **Elementos Esenciales:** No debería llevar más de 5 horas leer los Elementos Esenciales, aunque menos es mejor. Su objetivo es proporcionar el 20% del conocimiento que ayudará a los usuarios a manejar el 80% de los casos de uso. Los Elementos Esenciales pueden vincular a guías más avanzadas y a la API, aunque, en la mayoría de los casos, debes evitar tales enlaces. Cuando se proporcionan, también debes proporcionar un contexto para que los usuarios sepan si deben seguir este enlace en su primera lectura. De lo contrario, muchos usuarios terminan agotando su capacidad cognitiva saltando de enlace en enlace, tratando de aprender completamente cada aspecto de una característica antes de avanzar y, como resultado, nunca terminan esa primera lectura de los Elementos Esenciales. Recuerda que una lectura fluida es más importante que ser exhaustivo. Queremos dar a las personas la información que necesitan para evitar una experiencia frustrante, pero siempre pueden volver y leer más, o buscar en Google un problema menos común cuando lo encuentren.
  - **Avanzado:** Mientras que los Elementos Esenciales ayudan a las personas a manejar ~80% de los casos de uso, las guías posteriores ayudan a los usuarios a llegar al 95% de los casos de uso, además de proporcionar información más detallada sobre características no esenciales (por ejemplo, transiciones, animaciones), características de conveniencia más complejas (por ejemplo, mixins, directivas personalizadas) y mejoras en la experiencia del desarrollador (por ejemplo, JSX, plugins). El último 5% de los casos de uso que son más especializados, complejos y/o propensos a abusos se dejarán para el recetario y la referencia de la API, que se pueden vincular desde estas guías avanzadas.
- **Referencia/API:** Proporciona una lista completa de características, incluida información de tipo, descripciones del problema que resuelve cada una, ejemplos de cada combinación de opciones y enlaces a guías, recetas del recetario y otros recursos internos que brinden más detalles. A diferencia de otras páginas, esta no está destinada a leerse de arriba a abajo, por lo que se puede proporcionar bastante detalle. Estas referencias también deben ser más fáciles de revisar que las guías, por lo que el formato debe parecerse más a las entradas de un diccionario que al formato narrativo de las guías.
- **Migraciones:**
  - **Versiones:** Cuando se realizan cambios importantes, es útil incluir una lista completa de cambios, incluida una explicación detallada de por qué se hizo el cambio y cómo migrar sus proyectos.
  - **Desde otros proyectos:** ¿Cómo se compara este software con software similar? Esto es importante para ayudar a los usuarios a comprender qué problemas adicionales podríamos resolver o crear para ellos, y en qué medida pueden transferir conocimientos que ya tienen.
- **Guía de Estilo:** Hay algunas piezas clave en desarrollo que necesitan una decisión, pero no son fundamentales para la API. La guía de estilo proporciona recomendaciones educadas y con opiniones para ayudar a guiar estas decisiones. No deben seguirse ciegamente, pero pueden ayudar a los equipos a ahorrar tiempo al estar alineados en detalles más pequeños.
- **Recetario:** Las recetas en el recetario están escritas con cierta asunción de familiaridad con Vue y su ecosistema. Cada una es un documento altamente estructurado que explora algunos detalles de implementación comunes que un desarrollador de Vue podría encontrar.

## Escritura y Gramática

### Estilo

- **Los encabezados deben describir problemas**, no soluciones. Por ejemplo, un encabezado menos efectivo podría ser "Usando props", porque describe una solución. Un encabezado mejor podría ser "Pasando datos a componentes hijos con Props", porque proporciona el contexto del problema que resuelven los props. Los usuarios no empezarán a prestar atención a la explicación de una característica hasta que tengan alguna idea de por qué/cuándo la usarían.
- **Cuando asumas conocimiento, decláralo** al principio y vincula a recursos para conocimientos menos comunes que estás esperando.
- **Introduce solo un nuevo concepto a la vez siempre que sea posible** (incluyendo textos y ejemplos de código). Incluso si muchas personas pueden entender cuando introduces más de uno, también hay muchas que se perderán, e incluso aquellos que no se pierdan habrán agotado más de su capacidad cognitiva.
- **Evita bloques de contenido especiales para consejos y advertencias cuando sea posible.** En general, es preferible integrarlos de manera más natural en el contenido principal, por ejemplo, desarrollando ejemplos para demostrar un caso especial.
- **No incluyas más de dos consejos y advertencias entrelazados por página.** Si descubres que se necesitan más de dos consejos en una página, considera agregar una sección de advertencias para abordar estos problemas. La guía está destinada a leerse de corrido, y los consejos y advertencias pueden ser abrumadores o distraer a alguien que intenta entender los conceptos básicos.
- **Evita apelaciones a la autoridad** (por ejemplo, "deberías hacer X, porque es una mejor práctica" o "X es mejor porque te da una separación completa de las preocupaciones"). En cambio, demuestra con ejemplos los problemas humanos específicos causados y/o resueltos por un patrón.
- **Al decidir qué enseñar primero, piensa en qué conocimiento proporcionará la mejor relación potencia/esfuerzo.** Eso significa enseñar lo que ayudará a los usuarios a resolver los mayores problemas o el mayor número de problemas, con relativamente menos esfuerzo para aprender. Esto ayuda a los aprendices a sentirse inteligentes, poderosos y curiosos, para que su capacidad cognitiva se agote más lentamente.
- **A menos que el contexto asuma una plantilla de cadenas de texto o un sistema de compilación, solo escribe código que funcione en cualquier entorno por el software (por ejemplo, Vue, Vuex, etc.)**.
- **Muestra, no cuentes.** Por ejemplo, "Para usar Vue en una página, puedes agregar esto a tu HTML" (luego muestra la etiqueta de script), en lugar de "Para usar Vue en una página, puedes agregar un elemento de script con un atributo src, cuyo valor debe ser un enlace al código fuente compilado de Vue".
- **Casi siempre evita el humor**, especialmente el sarcasmo y las referencias a la cultura pop, ya que no se traduce bien entre las culturas.
- **Nunca supongas un contexto más avanzado del necesario.**
- **En la mayoría de los casos, prefiere los enlaces entre secciones de la documentación en lugar de repetir el mismo contenido en varias secciones.** Algo de repetición en el contenido es inevitable e incluso esencial para el aprendizaje. Sin embargo, demasiada repetición también hace que la documentación sea más difícil de mantener, porque un cambio en la API requerirá cambios en muchos lugares y es fácil pasar por alto algo. Este es un equilibrio difícil de alcanzar.
- **Lo específico es mejor que lo genérico.** Por ejemplo, un ejemplo de componente `<BlogPost>` es mejor que `<ComponentA>`.
- **Lo relacionable es mejor que lo oscuro.** Por ejemplo, un ejemplo de componente `<BlogPost>` es mejor que `<CurrencyExchangeSettings>`.
- **Sé emocionalmente relevante.** Las explicaciones y ejemplos que se relacionan con algo con lo que las personas tienen experiencia y se preocupan siempre serán más efectivas.
- **Siempre prefiere un lenguaje más simple y sencillo sobre un lenguaje complejo o jergoso**. Por ejemplo:
  - "puedes usar Vue con un elemento de script" en lugar de "para iniciar el uso de Vue, una opción posible es inyectarlo a través de un elemento de script HTML"
  - "función que devuelve una función" en lugar de "función de orden superior"
- **Evita el lenguaje que invalida un problema**, como "fácil", "solo", "obviamente", etc. Para referencia, consulta [Palabras a evitar en la escritura educativa (en inglés)](https://css-tricks.com/words-avoid-educational-writing/).

### Gramática

- **Evita abreviaturas** en la escritura y ejemplos de código (por ejemplo, `attribute` es mejor que `attr`, `message` es mejor que `msg`), a menos que estés haciendo referencia específica a una abreviatura en una API (por ejemplo, `$attrs`). Los símbolos de abreviaturas incluidos en los teclados estándar (por ejemplo, `@`, `#`, `&`) son permitidos.
- **Cuando hagas referencia a un ejemplo que sigue directamente, usa dos puntos (`:`) para terminar una oración**, en lugar de un período (`.`).
- **Usa la coma de Oxford** (por ejemplo, "a, b, y c" en lugar de "a, b y c"). ![Por qué la coma de Oxford es importante](./oxford-comma.jpg)
  - Fuente: [The Serial (Oxford) Comma: When and Why To Use It](https://www.inkonhand.com/2015/10/the-serial-oxford-comma-when-and-why-to-use-it/)
- **Al referirse al nombre de un proyecto, utiliza el nombre que el proyecto se refiere a sí mismo.** Por ejemplo, "webpack" y "npm" deberían escribirse en minúsculas, ya que así es como se hace referencia a ellos en su documentación.
- **Utiliza Capitalización en los encabezados** - al menos por ahora, ya que es lo que usamos en el resto de la documentación. Hay investigaciones que sugieren que el caso de frase (donde solo la primera palabra del encabezado comienza con mayúscula) es realmente superior en legibilidad y también reduce la carga cognitiva para los escritores de documentación, ya que no tienen que recordar si deben capitalizar palabras como "y", "con" y "acerca de".
- **No uses emojis (excepto en discusiones).** Los emojis son lindos y amigables, pero pueden ser una distracción en la documentación y algunos emojis incluso transmiten diferentes significados en diferentes culturas.

## Repetición y Comunicación

- **La excelencia proviene de la repetición.** Los primeros borradores siempre son malos, pero escribirlos es una parte vital del proceso. Es extremadamente difícil evitar la lenta progresión de Malo -> Aceptable -> Bueno -> Excelente -> Inspirador -> Trascendental.
- **Solo espera hasta que algo sea "bueno" antes de publicarlo.** La comunidad te ayudará a llevarlo más abajo en la cadena.
- **Trata de no ponerte a la defensiva al recibir retroalimentación.** Nuestra escritura puede ser muy personal para nosotros, pero si nos enfadamos con las personas que nos ayudan a mejorarla, dejarán de dar retroalimentación o empezarán a limitar el tipo de retroalimentación que dan.
- **Revisa tu propio trabajo antes de mostrárselo a los demás.** Si muestras a alguien un trabajo con muchos errores de ortografía/gramática, recibirás retroalimentación sobre errores de ortografía/gramática en lugar de notas más valiosas sobre si la escritura está alcanzando tus objetivos.
- **Cuando le pides a las personas retroalimentación, dile a los revisores:**
  - **qué estás tratando de lograr**
  - **cuales son tus temores**
  - **qué balance estás tratando de alcanzar**
- **Cuando alguien reporta un problema, casi siempre hay un problema**, incluso si la solución que propusieron no es del todo correcta. Sigue haciendo preguntas de seguimiento para aprender más.
- Las personas necesitan sentirse seguras al hacer preguntas al contribuir/revisar contenido. Así es cómo puedes lograrlo:
  - **Agradece a las personas por sus contribuciones/retroalimentaciones, incluso si te sientes fastidiado.** Por ejemplo:
    - "¡Gran pregunta!"
    - "Gracias por tomarte el tiempo para explicar. 🙂"
    - "Esto es intencional, pero gracias por tomarte el tiempo para contribuir. 😊"
  - **Escucha lo que las personas dicen y refleja si no estás seguro de entender correctamente.** Esto puede ayudar a validar los sentimientos y experiencias de las personas, al mismo tiempo que entiendes si _tú_ los estás entendiendo a _ellos_ de forma adecuada.
  - **Usa muchos emojis positivos y empáticos.** Siempre es mejor parecer un poco extraño que ser malo o impaciente.
  - **Comunica amablemente reglas/límites.** Si alguien se comporta de manera abusiva/inapropiada, responde solo con amabilidad y madurez, pero también deja claro que este comportamiento no es aceptable y qué sucederá (según el código de conducta) si continúan comportándose mal.

### Consejos, Llamadas, Alertas y Destacados de Línea

Tenemos algunos estilos dedicados para denotar algo que vale la pena resaltar de una manera particular. Estos están agrupados [en esta página](https://vitepress.vuejs.org/guide/markdown.html#custom-containers). **Deben usarse con moderación.**

Existe una cierta tentación de abusar de estos estilos, ya que uno puede simplemente agregar un cambio dentro de una llamada. Sin embargo, esto rompe el flujo de lectura para el usuario y solo debe usarse en circunstancias especiales. Siempre que sea posible, debemos intentar crear una narrativa y flujo dentro de la página para respetar la carga cognitiva del lector.

En ninguna circunstancia deben usarse dos alertas una al lado de la otra, es una señal de que no podemos explicar adecuadamente el contexto.

### Contribuciones

Apreciamos las solicitudes de cambios pequeñas y enfocadas. Si deseas realizar un cambio extremadamente grande, comunícate con los miembros del equipo antes de hacer una solicitud de cambio. Aquí hay un [artículo que detalla por qué esto es tan crítico](https://www.netlify.com/blog/2020/03/31/how-to-scope-down-prs/) para que funcionemos bien en este equipo. Por favor, comprende que aunque siempre apreciamos las contribuciones, en última instancia debemos priorizar lo que funciona mejor para el proyecto en su conjunto.

## Recursos

### Software

- [Grammarly](https://www.grammarly.com/): Aplicación de escritorio y extensión del navegador para verificar ortografía y gramática (aunque la verificación de gramática no captura todo y ocasionalmente muestra falsos positivos).
- [Code Spell Checker](https://marketplace.visualstudio.com/items?itemName=streetsidesoftware.code-spell-checker): Una extensión para VS Code que te ayuda a verificar la ortografía en markdown y ejemplos de código.

### Libros

- [On Writing Well](https://www.amazon.com/Writing-Well-30th-Anniversary-Nonfiction-ebook/dp/B0090RVGW0) (ver [citas populares](https://www.goodreads.com/work/quotes/1139032-on-writing-well-the-classic-guide-to-writing-nonfiction))
- [Bird by Bird](https://www.amazon.com/Bird-Some-Instructions-Writing-Life/dp/0385480016) (ver [citas populares](https://www.goodreads.com/work/quotes/841198-bird-by-bird-some-instructions-on-writing-and-life))
- [Cognitive Load Theory](https://www.amazon.com/Cognitive-Exploproporciónns-Instructional-Performance-Technologies/dp/144198125X/)
