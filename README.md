# Asteroids

Clon del clásico arcade **Asteroids** implementado en canvas HTML5 puro, sin dependencias ni bundler.

## Demo:

[Asteroids demo](https://klerith.github.io/claude-asteroids/)

## Descripción del juego

Nave espacial en un campo de asteroides con envolvimiento de bordes (el espacio es toroidal). Destruye asteroides para sumar puntos: los grandes se parten en medianos, los medianos en pequeños. Algunos asteroides sueltan un power-up de **disparo triple**.

## Tecnologías

- **HTML5 Canvas** — renderizado 2D
- **JavaScript (ES6+)** — lógica del juego en un solo archivo `game.js`
- Sin frameworks, sin bundler, sin dependencias

## Cómo correr

Abre `index.html` directamente en el navegador (doble clic), o usa un servidor local:

```bash
npx serve .
```

Luego visita `http://localhost:3000`.

## Controles

| Tecla     | Acción     |
| --------- | ---------- |
| `←` `→`   | Rotar nave |
| `↑`       | Propulsar  |
| `Espacio` | Disparar   |

## Puntuación

| Asteroide | Puntos |
| --------- | ------ |
| Grande    | 20     |
| Mediano   | 50     |
| Pequeño   | 100    |

## Power-ups

| Power-up           | Efecto                                                    | Duración |
| ------------------ | --------------------------------------------------------- | -------- |
| **Disparo triple** | La nave dispara 3 balas en abanico en lugar de una         | 10 s     |

Cada nivel garantiza al menos una aparición: uno de los asteroides grandes del nivel,
elegido al azar, lo lleva siempre y lo suelta al ser destruido. Además, cualquier otro
asteroide lo suelta con un 14% de probabilidad. Aparece como una cápsula
hexagonal cian y se recoge pasando por encima con la nave. Permanece 9 segundos en
pantalla (parpadea antes de desaparecer) y se pierde si la nave es destruida. El HUD
muestra una barra con el tiempo restante del efecto.

## Características

- 3 vidas con invencibilidad temporal al reaparecer (parpadeo)
- Asteroides se parten en fragmentos más pequeños al ser destruidos
- Partículas de explosión al destruir asteroides
- Power-up de disparo triple con indicador de duración en el HUD
