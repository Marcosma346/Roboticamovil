# Práctica 2: Visual Follow Line

## Objetivo

El objetivo de esta práctica es programar un coche de F1 para que siga una línea roja en un circuito, usando un controlador PID. El evaluador mide si sigue la línea y el tiempo que tarda en completar el circuito.

## Herramientas

Para esta práctica he usado:
- **HAL**: para leer la cámara y enviar comandos de velocidad.
- **Frequency**: para regular el bucle.
- **OpenCV**: para procesar la imagen (filtrar el rojo, calcular el centroide).
- **WebGUI**: para depurar (ver la máscara, las líneas, etc.).

## Estrategia

El enfoque general tiene tres partes:

1. **Detección de la línea**: filtrar el rojo en HSV y calcular el    centroide con `cv2.moments`.
2. **Cálculo del error**: diferencia entre dónde está la línea y dónde    quiero que esté.
3. **Controlador PID**: convierte el error en un giro para el coche.

Además, la velocidad se adapta: rápida en recta, lenta en curva.

## Diseño: controlador PID

El PID calcula el giro a partir del error:

| Término | Qué hace | En mi caso |
|---|---|---|
| P (Kp) | Gira proporcional al error | Sí, principal |
| I (Ki) | Corrige error constante | No lo usé |
| D (Kd) | Amortigua el giro | Sí, para evitar oscilación |

## Desarrollo
### Centrar camara

Lo primero que tuve que tuve que hacer era centrar la camara ya que no est en centro del vehiculo , el vehiculo al principio no estba centrado en la linea, estaba centrada la camara, tuve que movel el vehiculo y ver ese centro de la imagen, el cual era 380, a partir de ahi utilice ese numero para que el coche fuese recto 

### Primer intento: detección de la línea

Empecé filtrando el rojo en HSV. El problema es que el rojo está en dos rangos del círculo de tonos, así que tuve que sumar dos máscaras. Luego calculé el centroide con `cv2.moments` para saber dónde estaba la línea en la imagen.

### Segundo intento: PID con solo P

Con el error calculado, probé un controlador P: el giro era proporcional al error. El coche seguía la línea en recta, pero se salía en las curvas.

### Tercer intento: añadir D

Añadí el término D para amortiguar. Mejoró, pero el coche oscilaba al entrar en curva.

### Cuarto intento: ajustar Kp y Kd

Probé muchos valores. Descubrí que hay un compromiso: si Kp es alto, corrige rápido pero oscila; si Kd es alto, amortigua pero amplifica el ruido.

### Quinto intento: velocidad adaptativa

Hice que la velocidad bajara según el error: rápida en recta, lenta en curva. Eso mejoró el tiempo y redujo las salidas de pista.

### Resultado final

Con todas las mejoras, el coche completa el circuito en 42-43 segundos. Oscila un poco al entrar en curva, pero se estabiliza en 1-2 segundos.

## Resultado

- **Tiempo**: 42-43 segundos.
- **Comportamiento**: sigue la línea, oscila ligeramente al entrar 
  en curva, se estabiliza.
- **Oscilación**: transitoria, de 1-2 segundos.

### Vídeos
#### Circuito Simple ( 43,23 segundos )
https://github.com/user-attachments/assets/dafa74ec-09f0-4782-94a8-b88cb5dfc102

#### Circuito Montreal ( 97,31 segundos )

https://github.com/user-attachments/assets/cb6f08f3-c044-416e-9af8-59b10ff08a89

#### Circuito Montmelo ( 77,70 segundos )

https://github.com/user-attachments/assets/df524d30-7ecf-4b2c-8d0d-12f251198eb7

#### Circuito Nurburgring ( 58,76 segundos )

https://github.com/user-attachments/assets/d4019fb2-79a5-47db-bafd-129f2b84284a




## Reflexión

Lo más difícil ha sido el ajuste del PID. Al principio pensaba que subir Kp era la solución, pero me di cuenta de que hay un compromiso entre velocidad y estabilidad. Después de mucho probar conseguí una balance entre que hiciese rapido el circuito y oscilase lo menos posible 
