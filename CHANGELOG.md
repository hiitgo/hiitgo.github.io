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
