# Práctica 1: Vacuum Cleaner

## Objetivo

El objetivo de esta práctica es programar un robot aspiradora autónomo capaz de cubrir el máximo área posible de una casa. El evaluador mide el porcentaje de área recorrida, así que cuanto más cubra el robot, mejor es la solución.

## Herramientas

Para esta práctica he usado:
- **HAL**: para leer el sensor láser y enviar comandos de velocidad.
- **Frequency**: para regular el bucle a 50 Hz.
- **WebGUI**: para depurar.
- **Láser**: el sensor principal. Devuelve 180 distancias (0 = derecha, 90 = frente, 180 = izquierda).

## Estrategia

El enunciado propone un algoritmo de random exploration: avanzar hasta chocar, girar un tiempo aleatorio, y repetir. Es el algoritmo más simple de cobertura, y es el que he usado como base.

A partir de ahí, le he añadido varias mejoras para que no se quede atascado y cubra más área.

## Diseño: máquina de estados

En lugar de tener todo el comportamiento en un solo bloque, lo dividí en tres estados que se van alternando:

### AVANZAR
El robot va recto hacia delante. Mientras avanza, comprueba el láser en varios ángulos (frente y laterales). Si detecta un obstáculo cerca, pasa al estado PARAR.

### PARAR
El robot se detiene durante unos frames. Esto es importante porque si pasa directamente de avanzar a girar, el comando de avance sigue activo y el robot gira mientras avanza, lo que desestabiliza el movimiento. Parar un momento le da tiempo a "olvidar" el comando anterior.

### GIRAR
El robot gira en el sitio durante un tiempo aleatorio. Al terminar, vuelve a AVANZAR. El tiempo aleatorio es lo que da variedad al recorrido, para no repetir siempre el mismo camino.




```
┌─────────────┐
│   AVANZAR   │ ◄────────────┐
└──────┬──────┘              │
       │ obstáculo           │
       ▼                     │
┌─────────────┐              │
│    PARAR    │              │
└──────┬──────┘              │
       │ espera              │
       ▼                     │
┌─────────────┐              │
│    GIRAR    │              │
└──────┬──────┘              │
       │ fin giro            │
       └─────────────────────┘
```

## Desarrollo

### Primer intento: random puro

Empecé implementando el algoritmo tal cual: avanzar recto, y cuando el láser detectaba un obstáculo de frente, girar un tiempo aleatorio. El problema es que el robot se quedaba atascado contra las paredes.

### Segundo intento: mirar también los laterales

Me di cuenta de que el robot chocaba de lado y no lo detectaba, porque solo miraba el frente del láser. Añadí mirar también los laterales (ángulos 40 y 140 aproximadamente). Mejoró, pero a veces giraba hacia el obstáculo en lugar de alejarse.

### Tercer intento: girar hacia el lado contrario

Decidí que el robot girara siempre hacia el lado contrario al obstáculo. Si el obstáculo estaba a la izquierda, giraba a la derecha. Si estaba a la derecha, giraba a la izquierda. Si estaba de frente, elegía un lado al azar. Esto funcionó mucho mejor.

### Cuarto intento: retroceder antes de girar

Aun así, a veces se quedaba clavado contra la pared. Añadí un paso de retroceso antes de girar, para separarme de la pared y poder girar sin empujar contra ella. Finalmente quité la deteccion del laser de frente ya que a veces se quedaba pillado girando de lado a lado 

## Resultado

Con todas las mejoras, el robot cubre aproximadamente el XX% del área (si lo sabes), y ya no se queda atascado. El comportamiento es estable y cubre bien la casa.

### Video

https://github.com/user-attachments/assets/5e4554bc-6059-45a7-a612-1f591fce3f09


## Reflexión

Lo más difícil ha sido evitar que el robot se quedara atascado. Al principio pensaba que con el random puro era suficiente, pero me di cuenta de que el robot se quedaba en bucles limpiando la misma zona. 
Las mejoras que añadí (mirar los laterales, girar hacia el lado contrario, retroceder) resolvieron el problema.

