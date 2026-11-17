# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Proyecto

Clon de **Asteroids** en canvas HTML5 puro. Sin dependencias, sin bundler, sin package.json, sin tests. Tres archivos: `index.html` (canvas 800×600 + estilos inline), `game.js` (todo el juego), `favicon.svg`.

Comentarios y texto de UI están en español — mantener ese idioma al añadir código.

## Correr

Abrir `index.html` en el navegador (doble clic), o servirlo:

```bash
npx serve .
```

No hay build, lint ni suite de tests: la verificación es manual en el navegador (recargar la página).

## Arquitectura de `game.js`

Un solo archivo, sin módulos, cargado con `<script src>` clásico (no ES modules) — todo vive en el scope global y el orden de declaración importa. Secciones separadas por comentarios `// ── Nombre ──`.

**Bucle de juego** (al final del archivo): `loop(ts)` → `update(dt)` → `draw()` → `requestAnimationFrame`. `dt` está en **segundos** y se limita a `0.05` para evitar saltos tras un tab inactivo. Toda velocidad/aceleración se expresa por segundo y se multiplica por `dt`; nunca asumir movimiento por frame.

**Entidades** (`Bullet`, `Asteroid`, `Ship`, `Particle`): clases con el mismo contrato informal `update(dt)` / `draw()` y una bandera `dead`. Dibujan directo sobre el `ctx` global con `ctx.save()/restore()`, en coordenadas locales centradas en la entidad. Para añadir una entidad nueva, seguir ese contrato y añadir su array al estado global, al `update` y al `draw` (el orden en `draw()` define la profundidad: partículas → asteroides → balas → nave → HUD → overlay).

**Estado global**: `ship, bullets, asteroids, particles, score, lives, level, state, deadTimer`. `state` es `'playing' | 'dead' | 'gameover'` y `update()` hace early-return en los dos últimos (en `'dead'` los asteroides siguen moviéndose, en `'gameover'` solo las partículas). `initGame()` resetea todo; `nextLevel()` limpia balas/partículas y repuebla.

**Ciclo de vida de entidades**: nada se borra dentro de los bucles de colisión. Se marca `dead = true`, los fragmentos nuevos se acumulan en un array temporal, y al final se reemplazan los arrays con `.filter(x => !x.dead).concat(nuevos)`. Mantener este patrón al tocar colisiones.

**Espacio toroidal**: toda posición pasa por `wrap(v, max)`. Los asteroides y la nave envuelven; las partículas **no** (viven poco). Las colisiones usan `dist()` euclídea sin tener en cuenta el envolvimiento, así que un impacto justo en el borde puede fallar — es aceptable por diseño actual.

**Tablas por tamaño de asteroide**: `RADII`, `SPEEDS`, `POINTS` son arrays indexados por `size` (1 = pequeño, 3 = grande), con un `0` de relleno en el índice 0. Añadir un tamaño implica extender las tres tablas a la vez.

**Input**: `keys` (estado mantenido) para rotación y propulsión; `pressed(code)` consume un flanco de `justPressed` y debe llamarse **una sola vez por frame** por tecla, porque la lectura lo limpia. Disparo y reinicio usan `pressed('Space')`.

**Constantes de tuning** viven como `const` locales dentro del método que las usa (`ROT`, `THRUST`, `DRAG` en `Ship.update`; `SPEED` en `Bullet`; cooldown de disparo `0.2`s en `tryShoot`). Ajustar la jugabilidad es editar ahí, no un bloque de config central.

## Discrepancia conocida

El `README.md` describe power-ups y tipos especiales de asteroide (p. ej. "estrella fugaz") que **no existen** en `game.js`. Si se implementan, actualizar el README; si no, no asumir que ese código está en algún sitio.
