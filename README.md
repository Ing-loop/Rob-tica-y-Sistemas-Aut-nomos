# Laboratorio 1: Simulación de Robot Móvil Diferencial en Webots

## Asignatura
Robótica y Sistemas Autónomos  
Código: ICI 4150  

## 1. Descripción del laboratorio

El presente laboratorio tiene como objetivo comprender el comportamiento cinemático de un robot móvil diferencial mediante simulación en Webots. Se analiza cómo las velocidades de las ruedas influyen directamente en la trayectoria del robot.

Se utilizó un robot tipo **e-puck**, el cual cuenta con dos ruedas motrices independientes (izquierda y derecha), permitiendo generar distintos tipos de movimiento dependiendo de sus velocidades.

## 2. Objetivos

### Objetivo general
Comprender el modelo cinemático de un robot diferencial mediante simulación.

### Objetivos específicos
- Implementar control de motores en Webots.
- Analizar trayectorias en función de velocidades.
- Validar comportamiento teórico mediante simulación.
- Interpretar resultados desde un enfoque cinemático.

## 3. Modelo cinemático del robot diferencial

Un robot diferencial se modela mediante las siguientes ecuaciones:

$$
v = \frac{v_r + v_l}{2}
$$

$$
\omega = \frac{v_r - v_l}{L}
$$

Donde:

- \( v \): velocidad lineal del robot
- \( w \): velocidad angular
- \( v_r \): velocidad rueda derecha
- \( v_l \): velocidad rueda izquierda
- \( L \): distancia entre ruedas

### Interpretación física

| Condición        | Movimiento            |
|------------------|----------------------|
| v_r = v_l        | Movimiento recto     |
| v_r ≠ v_l        | Trayectoria curva    |
| v_r = -v_l       | Rotación en el lugar |

---

## 4. Descripción del experimento

Se programó el robot para ejecutar diferentes movimientos en función del tiempo:

### Secuencia de movimientos

| Intervalo (s) | Movimiento |
|--------------|-----------|
| 0 – 3 | Avance recto |
| 3 – 4 | Pausa |
| 4 – 7 | Retroceso |
| 7 – 8 | Pausa |
| 8 – 18 | Curva derecha |
| 18 – 19 | Pausa |
| 19 – 29 | Curva izquierda |
| 29 – 30 | Pausa |
| 30 – 40 | Círculo izquierda |
| 40 – 41 | Pausa |
| 41 – 51 | Círculo derecha |
| 51 – 52 | Pausa |
| 52 – 62 | Giro sobre su eje (derecha) |
| 63 – 73 | Giro sobre su eje (izquierda) |

## 5. Código del controlador (C)
El siguiente código fue implementado en Webots:

```c
#include <webots/robot.h>
#include <webots/motor.h>

#define TIME_STEP 64

WbDeviceTag left_motor;
WbDeviceTag right_motor;

double left_speed = 0.0;
double right_speed = 0.0;

void setup() {
  wb_robot_init();

  left_motor = wb_robot_get_device("left wheel motor");
  right_motor = wb_robot_get_device("right wheel motor");

  wb_motor_set_position(left_motor, INFINITY);
  wb_motor_set_position(right_motor, INFINITY);
}

void loop() {

  double t = wb_robot_get_time();

  // 0 - 3 → adelante
  if (t < 3.0) {
    left_speed = 5.0;
    right_speed = 5.0;
  }

  // 3 - 4 → pausa
  else if (t < 4.0) {
    left_speed = 0.0;
    right_speed = 0.0;
  }

  // 4 - 7 → atrás
  else if (t < 7.0) {
    left_speed = -5.0;
    right_speed = -5.0;
  }

  // 7 - 8 → pausa
  else if (t < 8.0) {
    left_speed = 0.0;
    right_speed = 0.0;
  }

  // 8 - 18 → curva derecha
  else if (t < 18.0) {
    left_speed = 6.0;
    right_speed = 5.0;
  }

  // 18 - 19 → pausa
  else if (t < 19.0) {
    left_speed = 0.0;
    right_speed = 0.0;
  }

  // 19 - 29 → curva izquierda
  else if (t < 29.0) {
    left_speed = 5.0;
    right_speed = 6.0;
  }

  // 29 - 30 → pausa
  else if (t < 30.0) {
    left_speed = 0.0;
    right_speed = 0.0;
  }

  // 30 - 40 → círculo izquierda
  else if (t < 40.0) {
    left_speed = 6.20;
    right_speed = 0.5;
  }

  // 40 - 41 → pausa
  else if (t < 41.0) {
    left_speed = 0.0;
    right_speed = 0.0;
  }

  // 41 - 51 → círculo derecha
  else if (t < 51.0) {
    left_speed = 0.5;
    right_speed = 6.20;
  }

  // 51 - 52 → pausa
  else if (t < 52.0) {
    left_speed = 0.0;
    right_speed = 0.0;
  }

  // 52 - 62 → giro en el sitio hacia la derecha
  else if (t < 62.0) {
    left_speed = 6.0;
    right_speed = -6.0;
  }
  
    // 62 - 63 → pausa
  else if (t < 55.0) {
    left_speed = 0.0;
    right_speed = 0.0;
  }
  
  // 63 - 73 → giro en el sitio hacia la izquierda
  else if (t < 73.0) {
    left_speed = -6.0;
    right_speed = 6.0;
  }

  else {
    left_speed = 0.0;
    right_speed = 0.0;
  }

  wb_motor_set_velocity(left_motor, left_speed);
  wb_motor_set_velocity(right_motor, right_speed);
}

int main(int argc, char **argv) {

  setup();

  while (wb_robot_step(TIME_STEP) != -1) {
    loop();
  }

  wb_robot_cleanup();
  return 0;
}

```

## 6. Análisis de resultados
### 6.1. ¿Qué ocurre cuando ambas ruedas tienen la misma velocidad?

Cuando `v_r = v_l`, la velocidad angular es cero:

$$
\omega = 0
$$

Por lo tanto, el robot se desplaza en línea recta sin rotación.  
Esto se observó claramente en los intervalos de avance y retroceso.

### 6.2. ¿Cómo cambia la trayectoria cuando las velocidades son diferentes?

Cuando `v_r ≠ v_l`, aparece una velocidad angular distinta de cero:

$$
\omega \neq 0
$$

Esto genera una trayectoria curva. Mientras mayor sea la diferencia entre velocidades, menor será el radio de curvatura.

- Diferencia pequeña → curva suave  
- Diferencia grande → curva cerrada  

### 6.3. ¿Qué ocurre cuando una rueda gira en sentido opuesto a la otra?

Cuando `v_r = -v_l`;

Se obtiene:

$$
v = 0
$$

El robot no avanza, pero sí rota sobre su propio eje.  
Este comportamiento corresponde a una rotación pura, observada en los giros en el lugar.

### 6.4. ¿Qué tipo de movimiento permite dibujar un círculo?

Un movimiento circular se obtiene cuando `v_r ≠ v_l y ambos > 0`.

En este caso, el robot describe una circunferencia cuyo radio depende de:

$$
R = \frac{L}{2} \cdot \frac{v_r + v_l}{v_r - v_l}
$$

En la simulación, esto se evidenció cuando una rueda tenía velocidad alta y la otra baja.

### 6.5 Video de la simulacion

[Ver video de la simulacion](https://github.com/Ing-loop/Rob-tica-y-Sistemas-Aut-nomos/raw/refs/heads/main/Video_lab_1.mp4)

## 7. Análisis cinemático basado en el código

En el código implementado, las variables `left_speed` y `right_speed` representan directamente las velocidades de las ruedas del robot, equivalentes a:

- `left_speed ≡ v_l`
- `right_speed ≡ v_r`

A partir de estas variables, se puede analizar el comportamiento del robot en cada intervalo de tiempo utilizando el modelo cinemático:

$$
v = \frac{v_r + v_l}{2}, \quad \omega = \frac{v_r - v_l}{L}
$$

### Casos observados en la simulación

- **Movimiento recto (t < 3 s y 4–7 s)**  
  `left_speed = right_speed`  
  ⇒ \( w = 0 \), el robot se desplaza en línea recta.

- **Curva derecha (8–18 s)**  
  `left_speed > right_speed`  
  ⇒ \( w > 0 \), el robot gira hacia la derecha.

- **Curva izquierda (19–29 s)**  
  `right_speed > left_speed`  
  ⇒ \( w < 0 \), el robot gira hacia la izquierda.

- **Movimiento circular (30–40 s y 41–51 s)**  
  Existe una diferencia significativa entre velocidades  
  ⇒ Se obtiene una trayectoria circular con radio constante.

- **Rotación en el lugar (52–62 s y 63–73 s)**  
  `left_speed = -right_speed`  
  ⇒ \( v = 0 \), el robot gira sobre su propio eje sin desplazamiento lineal.

- **Pausas**  
  `left_speed = right_speed = 0`  
  ⇒ \( v = 0 \) y \( w = 0 \), el robot permanece detenido.

Este análisis confirma que el comportamiento del robot está completamente determinado por las velocidades asignadas en el código.

En la simulación, esto se evidenció cuando una rueda tenía velocidad alta y la otra baja.

## 9. Conclusiones

- El comportamiento del robot diferencial está completamente determinado por las velocidades de sus ruedas.  
- El modelo cinemático describe correctamente las trayectorias observadas.  
- La simulación en Webots permite validar conceptos teóricos de forma visual.  
- Se comprobó la relación directa entre diferencia de velocidades y tipo de trayectoria.  
- Este modelo es fundamental en robótica móvil y navegación autónoma.

