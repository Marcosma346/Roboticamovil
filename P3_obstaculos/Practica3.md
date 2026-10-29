# Práctica 3: Local Navigation with VFF

## Objetivo

El objetivo de esta práctica es implementar el algoritmo de Navegación Local conocido como VFF (Virtual Force Field). El coche (un F1) debe ir desde su posición actual hasta un waypoint (objetivo) esquivando a los coches rivales que se encuentran en el circuito.

La idea principal es que nuestro F1 se mueva por la suma de dos fuerzas: una de atracción hacia el waypoint y otra de repulsión generada por los coches rivales.

## Herramientas

Para esta práctica he usado:
- **HAL**: para leer la posición de nuestro F1 (`getPose3d`), el láser (`getLaserData`) y enviar comandos de velocidad (`setV`, `setW`).
- **WebGUI**: para obtener el waypoint actual (`getNextTarget`) y para depurar dibujando las fuerzas en pantalla (`showForces`).
- **Frequency**: para regular el bucle a 50 Hz.
- **Láser**: el sensor principal para detectar a los otros F1 (180 rayos).

## Teoría: ¿Cómo funciona el VFF?

El algoritmo se basa en tratar a nuestro F1 como si fuera una partícula en un campo de fuerzas. En cada iteración del programa se calculan dos vectores:

1. **Vector de Atracción (Target Vector)**: Es la flecha que tira de nuestro coche hacia el waypoint. Para calcularlo, resto la posición de nuestro F1 a la del objetivo    y aplico una función de rotación (`absolute2relative`) para que el vector sea relativo al morro de nuestro coche y no al mapa.

2. **Vector de Repulsión (Obstacle Vector)**: Es la suma de todas las flechas  que empujan a nuestro F1 para alejarlo de los coches rivales. Cuanto más cerca está un rival (detectado por el láser), más fuerte es la repulsión.

Finalmente, se suman ambos vectores para obtener un **Vector Resultante**. 
Como nuestro F1 siempre avanza hacia adelante, uso este vector resultante únicamente para decidir el ángulo de giro (`setW`).

## Desarrollo: Mi proceso

### Primer intento: Solo atracción
Empecé calculando únicamente el vector de atracción. El F1 iba directo hacia el waypoint, pero chocaba con los rivales que se ponían en medio porque no tenía en cuenta los obstáculos. Usé la función `absolute2relative` para que el coche supiera hacia dónde girar.

### Segundo intento: Añadir la repulsión del láser
Añadí el cálculo del vector de repulsión. Para cada uno de los 180 rayos del 
láser, calculé una fuerza inversamente proporcional a la distancia (a menor 
distancia, mayor fuerza). Luego usé senos y cosenos para descomponer cada 
rayo en X e Y, y los sumé todos.

### Tercer intento: Ajustar el comportamiento
Al principio el F1 se quedaba bloqueado o frenaba demasiado cuando se acercaba a otro coche. Para solucionarlo:
- **Ignorar la componente de avance**: Como sugiere el enunciado, la fuerza de repulsión suele apuntar hacia atrás, así que usé la suma de vectores solo para calcular el giro, no para frenar el avance.
- **Velocidad adaptativa**: Hice que el F1 redujera la velocidad si el ángulo de giro era muy brusco, para que pudiera tomar mejor las curvas.
- **Lógica de llegada**: Cuando el F1 está a menos de 1 metro del waypoint, llamo a `target.setReached(True)` para que el simulador le dé el siguiente punto.

## Depuración con WebGUI

La función `WebGUI.showForces()` fue clave para entender qué estaba pasando. 
Me permitía ver en pantalla:
- La flecha de atracción (hacia el objetivo).
- La flecha de repulsión (desde los rivales).
- La flecha resultante (la suma de ambas, que indica hacia dónde gira nuestro F1).

<img width="1850" height="951" alt="flechas" src="https://github.com/user-attachments/assets/91bfde02-0936-4cc0-adaf-0b16769a502b" />

## Resultado

El F1 es capaz de navegar de waypoint en waypoint de forma autónoma. 
Esquiva a los coches rivales usando el láser y llega a los objetivos. 
El comportamiento general es estable y las fuerzas se calculan correctamente.

## Vídeo

https://github.com/user-attachments/assets/cb036bbf-148b-49e1-a58c-369554b16083

Duración aproximada: 1.45 minutos 

## Reflexión

Lo más difícil de esta práctica ha sido entender cómo combinar los vectores. 
Al principio intentaba sumar la fuerza de repulsión directamente a la velocidad del coche, pero eso hacía que frenara o fuera hacia atrás. Entender que había que usar el vector resultante solo para el giro fue el clic que hizo que todo funcionara.

También me di cuenta de la importancia de la depuración visual. Ver las flechas en el WebGUI me ayudó a entender por qué el F1 a veces se comportaba de forma extraña.
