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
