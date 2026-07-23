# Changelog

HIIT Go! es un temporizador HIIT de una sola página (`index.html`), sin
dependencias externas — vanilla JS, Web Audio API, `localStorage`, 11 idiomas.
Publicado en GitHub Pages desde `main` (repo `hiitgo/hiitgo.github.io`).

El `build-stamp` visible al pie de la pantalla de configuración
(`build AAAA-MM-DD-X`) identifica qué versión está realmente publicada en
el navegador; la letra `X` sube (B → C → D...) en cada iteración para poder
verificar en caliente qué build está sirviendo GitHub Pages.

## build 2026-07-21-D

1. **Botón Reset no visible con la barra de direcciones abierta.** La regla
   `#app` declaraba `min-height:100dvh` seguido de `min-height:100vh`, así
   que el segundo valor pisaba al primero y los navegadores usaban `100vh`
   (que no descuenta la barra de direcciones). Se invirtió el orden en
   `#app` y en `.dial` (mismo patrón invertido en `max-height`) para que
   `dvh` quede como valor final vigente.

2. **Números de la cuenta regresiva pequeños dentro del anillo.** Se
   aumentó `.big-num` de `min(34vw,19vh,190px)` a `min(44vw,26vh,240px)`.
   Verificado en 360px de ancho y en escritorio ancho/bajo: los números de
   dos dígitos no tocan el anillo.

3. **"Total time" mal ubicado en la pantalla de inicio** (pegado al botón
   GO! con el desplegable de nombres cerrado, fuera de vista si estaba
   abierto). Se movió el bloque `.total` en el HTML para que quede
   inmediatamente después de las tarjetas de configuración (Get ready) y
   antes de `<details class="names-box">`, y se agrandó (`.98rem` →
   `1.15rem`, más peso en el número) con márgenes ajustados a su nueva
   posición.

4. **Tipografía pequeña en las pantallas del temporizador.** `.phase-label`
   1.5rem → 1.9rem, `.round-label` 1rem → 1.2rem, `.exercise-name`
   `clamp(1.3rem,6.2vw,2rem)` → `clamp(1.5rem,7vw,2.4rem)`. Verificado con
   un nombre de ejercicio largo en 360px de ancho: envuelve en dos líneas
   sin desbordar ni cortar el anillo.

5. **GO!/OK superpuesto con el anillo durante el fundido.** `.dial` tenía
   `transition:opacity .2s ease`, así que el cruce entre la palabra
   (GO!/OK) y el anillo de la fase siguiente dejaba una ventana de ~200ms
   donde ambos eran visibles a la vez. Se quitó la transición para que el
   cambio de opacidad sea instantáneo en ambos sentidos (confirmado:
   `transition-duration` computado es `0s` y `opacity` pasa directo de
   `1` a `0` sin frames intermedios).

6. **GO!/OK demasiado pequeño una vez liberados del anillo.** Al ya no
   necesitar caber dentro del círculo (punto 5), se subió
   `.big-num.word` de `min(40vw,22vh,220px)` a un objetivo de
   `min(68vw,36vh,400px)`. Ese valor desbordaba la pantalla en 360px de
   ancho (texto de 438.7px en un viewport de 360px). Se bajó en pasos de
   4vw/2vh hasta `min(48vw,26vh,400px)`, que deja ~7% de margen a cada
   lado en 360px y no desborda en escritorio ancho/bajo.

## build 2026-07-21-E

**Principio de diseño de esta tanda:** el botón GO! fijo (`position:fixed`,
tamaño y posición actuales) es una decisión de diseño deliberada, no
negociable — es el elemento de marca de la landing y debe verse siempre.
El resto de la pantalla de inicio se compone *alrededor* de él, dentro del
espacio que queda libre arriba; no al revés.

1. **Títulos de fase delgados.** `.phase-label` (GET READY / HIIT / REST)
   subió de 1.9rem a 2.4rem, `letter-spacing` bajó de .14em a .08em
   (el peso ya estaba en 900 desde el build D). Verificado: "GET READY"
   (la cadena más larga) no se corta ni hace salto de línea en 360px
   de ancho (mide 241.7px, con margen amplio a cada lado).

2. **Encabezado del temporizador pegado arriba, con hueco muerto antes
   del anillo.** Se envolvió `phase-label` + `round-label` +
   `exercise-name` + `.lights` en un nuevo contenedor `.timer-head`
   (`flex:1; justify-content:center`), y se agregó margen inferior a
   `.lights` (10px → 16px de margen inferior) para separarlo del anillo
   sin que las sombras se toquen. Verificado con y sin nombre de
   ejercicio, en pantalla alta (640px) y baja (560px): el bloque queda
   centrado en su franja, no pegado al borde.

3-4. **Barra de direcciones que no colapsaba, y pantalla de inicio
   distinta en Chrome/Firefox.** Causa: `100dvh` en `#app` es un valor
   *dinámico* — cambia mientras el navegador intenta colapsar la barra
   de direcciones, generando un bucle que se lo impide (y con
   comportamiento de cálculo distinto entre motores). Se reemplazó por
   `100svh` (small viewport height: la altura disponible *con* la barra
   visible, un valor **estático**), con `100vh` como respaldo para
   navegadores sin soporte. Se eliminó todo uso de `dvh` en el archivo
   (también estaba en `.dial{max-height:...}`, mismo patrón).

   El botón `.start-btn` se mantuvo `position:fixed`, con su tamaño y
   posición sin cambios — no se devolvió al flujo. En vez de eso, se
   definió una variable CSS única (`--start-btn-reserve: 90px`, la
   altura real del botón + su offset inferior + un respiro) usada como
   `padding-bottom` de `#setup`, y se recortaron —en este orden—
   altura del logo (104px → 60px), padding vertical de las tarjetas
   (14px → 8px), gaps entre tarjetas (12px → 6px), tamaño de los
   botones −/+ (48px → 42px, no el número), y márgenes de subtítulo/
   total/desplegable de nombres. No se tocó el tamaño de los números de
   los selectores ni el del botón GO!/su texto. Verificado en 360×640
   con la barra "visible" (sin scroll): topbar, logo, los 4 selectores,
   el tiempo total y el desplegable de nombres (cerrado) entran
   completos, con ~38px de margen antes del botón fijo.

   Como la página sigue teniendo scroll (secciones Cómo funciona,
   Beneficios, FAQ debajo), se agregó lógica para ocultar el botón
   fijo (opacity + pointer-events, transición de .2s) cuando
   `#setup` termina de salir de la vista
   (`getBoundingClientRect().bottom <= 0`), y mostrarlo de nuevo al
   subir — así no tapa el contenido de las secciones inferiores.
   Verificado: el botón desaparece al llegar a "Frequently asked
   questions" y reaparece al volver arriba.

5. **Build-stamp movido al pie de página.** Se sacó de la pantalla de
   inicio y se agregó como última línea de `.site-footer`, después de
   "HIIT Go!" y de la clave i18n `footerText` (ya traducida en los 11
   idiomas), manteniendo su estilo discreto (`.68rem`, color tenue).

## build 2026-07-21-F

**Principio de esta tanda:** la landing debe tener una composición FIJA
— los mismos tamaños en píxeles en cualquier navegador. Lo único que
puede variar entre motores es cuánto scroll queda por debajo, nunca el
tamaño de los elementos.

**Dos causas de raíz a recordar, porque ya reaparecieron más de una vez:**

- **`height:100%` en `html`/`body` rompe el colapso de la barra de
  direcciones.** Fija la altura del documento al viewport, lo que rompe
  el scroll nativo del que depende el navegador para colapsar su barra
  con swipe up. Es un remanente que sobrevivió sin tocarse desde
  versiones anteriores a pesar de que el build E ya había resuelto el
  `dvh`/`svh` de `#app` — arreglar la unidad de altura no sirve de nada
  si el documento sigue con la altura fijada por otro lado.
- **Dimensionar elementos de la landing con unidades de viewport (`vh`,
  `svh`, `dvh`, `vw`, o `clamp()` que las use) produce composiciones
  distintas en cada navegador**, porque cada uno calcula el viewport
  distinto (sobre todo con la barra de direcciones visible/oculta). La
  única unidad de viewport permitida en toda la landing es el
  `min-height` de `#app`; todo lo demás en `#setup` debe ser `rem`/`px`
  fijos. (Auditado en este build: `#setup` ya estaba 100% en unidades
  fijas desde el build E — se deja constancia para que no vuelva a
  reintroducirse por error en el futuro.)

1. **Barra de direcciones que no colapsaba con swipe up.** Causa: seguía
   existiendo `html,body{height:100%;}`, remanente no tocado en el build
   E. Se eliminó. Confirmado: no queda `overflow:hidden` ni
   `overflow-y:auto` en `html`, `body`, `#app`, `#setup` ni `#timer`
   — el scroll ocurre en el documento, `document.documentElement.scrollHeight`
   ya no está pisado por una altura fija (pasó a ser el alto real del
   contenido). `#app` mantiene `min-height:100vh` seguido de
   `min-height:100svh`.

2. **Auditoría de unidades de viewport en `#setup`.** Se revisaron los
   ocho elementos listados (logo, subtítulo, tarjetas, steppers, total,
   desplegable de nombres, botón GO!): ninguno usaba `vh`/`svh`/`vw` —
   ya habían quedado en `rem`/`px` fijos en el build E. `--start-btn-reserve`
   se mantiene en `90px` como única fuente de verdad del
   `padding-bottom` de `#setup`. Verificado en 360×640: los 4 selectores,
   el tiempo total y el desplegable de nombres cerrado entran completos,
   con 31px de margen antes del botón (≥12px pedido).

3. **El botón GO! seguía tapando "How HIIT Go! works" al hacer scroll.**
   Se reemplazó el listener de `scroll` que comparaba contra el final de
   `#setup` por un único `IntersectionObserver` sobre `#how` (la primera
   sección de contenido): aplica `away` apenas `#how` empieza a
   intersectar el viewport, la quita al dejar de intersectar. Sin
   umbrales de píxeles fijos, funciona a cualquier alto de pantalla.
   Verificado: el botón desaparece justo cuando aparece el título
   "Cómo funciona HIIT Go!", sin superponerse a él, y reaparece al
   subir.

4. **Números del contador más delgados en Chrome que en Firefox.** La
   pila de fuentes del sistema mapea 800/900 a archivos distintos según
   el navegador. Se fijó `font-weight:900` explícito en `.big-num` (antes
   800) y `.big-num.word`, y se sumó `-webkit-text-stroke:1.5px currentColor`
   a ambos — contorno del mismo color que el relleno, así engrosa el
   trazo sin ensuciarlo (a diferencia del contorno oscuro que se probó en
   los títulos de fase y no quedó bien). No se aplicó a ningún otro
   texto.

5. **Semáforo a distancia variable del anillo según hubiera o no nombre
   de ejercicio.** `.lights` vivía dentro de `.timer-head`
   (`flex:1;justify-content:center`), así que su posición dependía de
   cuánto texto hubiera arriba. Se sacó `.lights` de `.timer-head`: ahora
   `.timer-head` centra solo los tres textos (`flex:1`), `.lights` es
   hermano con `flex:0 0 auto` y margen inferior fijo de 16px, y
   `.dial-wrap`/`.controls` pasaron a `flex:0 0 auto` (ya no compiten por
   el espacio libre). Como `.timer-head` es el único elemento con
   `flex-grow`, absorbe todo el espacio sobrante y el resto del bloque
   (semáforo, anillo, controles) queda con tamaño fijo, ajeno al
   contenido del encabezado. Verificado con y sin nombre de ejercicio:
   `#lights` y `.dial` quedan exactamente en el mismo `getBoundingClientRect()`
   en ambos casos (gap semáforo→anillo: 16px exactos, sin variación).

6. **Franja inferior (safe-area) cambiaba de color con retraso.**
   `applyPhaseStyle()` pinta tanto `body` como `html`, pero ambos tenían
   su propia transición de `.45s`, sin sincronizar. Se quitó la
   transición de `html` (cambia de inmediato) y se dejó solo en `body`
   — como `body` tapa casi toda la superficie, el único lugar donde se
   nota es esa franja, que ahora cambia junto con el resto.

7. **Títulos de fase.** Sin cambios de tamaño/peso (2.4rem / .08em, ya
   quedaron bien en el build E). Verificado tras el cambio del punto 5:
   siguen centrados horizontalmente y "GET READY"/"PREPARACIÓN" no se
   cortan en 360px de ancho.

## build 2026-07-21-G

1. **El `IntersectionObserver` del build F tenía un error de lógica: el
   botón GO! reaparecía al seguir bajando.** Un observer sobre una sola
   sección (`#how`) solo sabe si ESA sección intersecta; al pasarla de
   largo hacia `#features`/`#benefits`/etc., `#how` deja de intersectar
   y el botón vuelve a mostrarse justo donde más estorba. Se revirtió al
   listener de `scroll` del build E (`setupEl.getBoundingClientRect().bottom<=0`),
   que compara contra el final de `#setup` — una vez pasado ese punto no
   hay vuelta atrás hasta hacer scroll hacia arriba. Se eliminó el
   `IntersectionObserver` para no dejar dos mecanismos compitiendo.
   Verificado: el botón se oculta al pasar `#setup`, sigue oculto en
   Beneficios/FAQ (scrollY 2500+), y reaparece al volver a scrollY 0.

2. **Nueva auditoría de unidades de viewport en `#setup`, punto por
   punto.** Se revisaron otra vez los ocho elementos (logo, subtítulo,
   tarjetas, steppers, total, desplegable, botón GO!): ninguno usa
   `vh`/`svh`/`dvh`/`vw` ni `clamp()` que las use — ya estaban en
   `rem`/`px` fijos desde el build E, y así se mantienen. La única
   excepción sigue siendo el `min-height` de `#app`.

   Como esto no explicaba por sí solo la queja de "se ve distinta según
   el navegador", se identificó una segunda causa real y no relacionada
   con `vh`: el "font boosting" automático de Chrome (`text-size-adjust`),
   que reescala el texto en columnas angostas de forma heurística e
   inconsistente con otros navegadores aun con CSS 100% en unidades
   fijas. Se agregó `text-size-adjust:100%` (con prefijo `-webkit-`) en
   `html` para desactivarlo.

   Medido en 360×640 y 412×915 con el desplegable cerrado y sin nombres
   de ronda: en ambos casos entran completos —botones superiores, logo,
   subtítulo, 4 tarjetas, tiempo total, desplegable cerrado y botón GO!—
   sin scroll, con 31px de margen en 360×640 y 332px en 412×915 (ambos
   ≥12px). No hizo falta achicar ningún elemento: el ancho/alto extra de
   412×915 da más aire, no menos, así que si 360×640 entra, 412×915
   entra con más margen todavía.

   Nota de proceso: el "gap" negativo visto a mitad de esta iteración
   (-121px) no era una regresión de layout — era estado de pruebas
   anteriores (un nombre de ronda cargado y el desplegable abierto)
   todavía presente en la página al medir. Con el desplegable cerrado y
   sin nombres, que es el estado real de una visita nueva, el margen es
   el de arriba.
