# CUBLI - Cubo Autoequilibrable

Proyecto desarrollado para el curso de Métodos Numéricos de la Facultad de Ingeniería Electrónica y Electrónica (FIEE) de la Universidad Nacional Mayor de San Marcos (UNMSM)[cite: 1, 2].

## 👥 Integrantes
* **Espinoza Quispe, Anthony**[cite: 1]
* **Albornoz Tamayo, Gian Carlos**[cite: 1]
* **Palomino Ccopa, Khevyn Jahir**[cite: 1]
* **Mendoza Sanchez, Jose Fabian**[cite: 1]

**Docente:** Josué Miranda[cite: 1]  
**Código FIEE**[cite: 1]

---

## 🔬 1. Prototipo Experimental (Hardware y Física del Sistema)

### Descripción y Justificación
El prototipo físico se implementa para resolver un problema clásico de control automático donde el sistema posee más grados de libertad que actuadores independientes[cite: 4]. El cubo se apoya sobre uno de sus vértices, lo que lo vuelve intrínsecamente inestable[cite: 4].

**Propósito del Prototipo:**
* Mantener el cubo estático sobre su vértice de forma autónoma, corrigiendo instantáneamente cualquier inclinación o perturbación externa[cite: 4].
* Comprender a profundidad el modelado de sistemas físicos inestables y diseñar leyes de control robustas capaces de responder en escala de milisegundos[cite: 4].

### Vista del Prototipo Físico
![Prototipo Experimental Cubli](prototitpo_exp.png)[cite: 31]

### Lista de Materiales y Componentes
* **Microcontrolador:** ESP32 DevKit V1 (actúa como el cerebro del sistema)[cite: 5, 6].
* **Unidad de Medición Inercial (IMU):** Sensor GY-521 (MPU6050)[cite: 5].
* **Actuadores:** Motores *Brushless* / Nidec[cite: 5, 10].
* **Mecánica Inercial:** Volantes de inercia de metal pesadas[cite: 5, 6].
* **Alimentación y Energía:** Batería de LiPo 500 mAh (3.7V), Condensador Electrolítico ($35\,\text{V}, 100\,\mu\text{F}$), Condensador ($35\,\text{V}, 100\,\text{nF}$) y reguladores[cite: 5].
* **Resistencias:** $10\,\text{k}\Omega$, $33\,\text{k}\Omega$, $6.8\,\text{k}\Omega$[cite: 5].
* **Alerta Auditiva:** Buzzer[cite: 5].
* **Chasis:** Estructura cúbica con variados adhesivos y soportes[cite: 5].

### Principio de Funcionamiento Físico
1. **Detección Inercial:** El sensor IMU (MPU6050) lee constantemente la inclinación y velocidad angular espacial del cubo[cite: 6]. Cuando detecta que el sistema pierde su centro de gravedad y comienza a caer, envía dicha lectura al ESP32[cite: 6].
2. **Cálculo y Respuesta:** El microcontrolador calcula en milisegundos la fuerza exacta requerida para contrarrestar la caída y activa los motores internos[cite: 6].
3. **Generación de Contra-Torque:** Los motores hacen girar bruscamente las ruedas de inercia en la misma dirección de la caída[cite: 6]. Por el principio físico de acción y reacción, acelerar este volante pesado genera un contra-torque invisible en sentido contrario que empuja el chasis del cubo de regreso a su posición original de equilibrio[cite: 6].

### Adquisición y Depuración de la Posición Espacial (IMU)
En la práctica experimental, el procesamiento de la posición espacial del cubo mediante el MPU6050 se realiza a través de cuatro etapas críticas[cite: 7]:
* **Lecturas Brutas (*Raw Data*):** Valores nativos directos entregados por el acelerómetro y giroscopio que no pueden usarse directamente debido a imperfecciones del entorno[cite: 7].
* **Ruido de Alta Frecuencia:** Fluctuaciones e imprecisiones puntuales en el acelerómetro provocadas por las vibraciones mecánicas de los motores y el contacto con la superficie[cite: 7].
* **Deriva (*Drift*) Acumulativa:** Error progresivo en el giroscopio al integrar numéricamente la velocidad angular en el tiempo, lo que desvía la posición real segundo a segundo[cite: 7].
* **Fusión Sensorial:** Algoritmos (Filtro Complementario / EKF) que combinan y compensan ambas señales, logrando estabilizar el sistema y reducir el error de posición a un margen menor a $0.35^\circ$[cite: 7].

---

## 🖼️ Diagrama del Circuito Electrónico

![Diagrama Electrónico del Cubli](Diagrama_Cubli.png)[cite: 30]

---

## 💻 2. Prototipo Web / Computacional (Métodos Numéricos y Modelado en MATLAB)

El prototipo computacional comprende la modelación matemática, simulación y validación rigurosa de 6 métodos avanzados de control automático, procesamiento de señales y estimación estocástica desarrollados en scripts de MATLAB:

### Método 1: Espacio de Estados y Control Óptimo LQR (Regulador Lineal Cuadrático)
* **Fundamento Matemático:** El sistema dinámico linealizado se modela en espacio de estados mediante la ecuación vectorial $\dot{x} = Ax + Bu$, donde los vectores y matrices describen las inercias, masas y los acoplamientos mecánicos del cubo[cite: 11]. Para estabilizar este sistema inestable de forma óptima, se implementa un Regulador Lineal Cuadrático (LQR), el cual resuelve numéricamente la Ecuación Algebraica de Riccati (ARE)[cite: 11]:
  $$A^P + PA - PBR^{-1}B^T + Q = 0$$
* **Aporte al Prototipo:** Permite calcular de forma óptima la matriz de ganancias de control $K = R^{-1}B^T P$, minimizando simultáneamente dos objetivos opuestos: el error de orientación/estado del cubo y el consumo de energía eléctrica de los motores[cite: 11]. Asimismo, calcula los polos del sistema en lazo cerrado ($A - BK$), demostrando matemáticamente que se ubican en el semiplano izquierdo del plano complejo, garantizando la estabilidad asintótica del Cubli[cite: 11].

### Método 2: Caracterización No Lineal de Actuadores y Saturación de Voltaje
* **Fundamento Matemático:** En la teoría ideal, ante una señal de control, el motor responde de forma lineal e instantánea. Sin embargo, en el mundo real, los motores DC poseen no linealidades severas (zonas muertas, fricción magnética) y restricciones físicas de alimentación[cite: 13]. Se modela la curva de torque real mediante una función tangente hiperbólica[cite: 13]:
  $$\tau_{\text{real}} = k_v \cdot \tanh\left(\frac{\text{PWM}}{v_{\text{scale}}}\right)$$
* **Aporte al Prototipo:** Introduce una función de saturación estricta ligada a la fuente de alimentación real (una batería LiPo de $12\,\text{V}$)[cite: 13]:
  $$V_{\text{aplicado}} = \min(\max(V_{\text{exigido}}, -V_{\text{max}}), V_{\text{max}})$$
  Esto obliga al algoritmo de simulación a trabajar bajo restricciones físicas reales, evitando fallas críticas al momento de transferir el código al microcontrolador ESP32[cite: 13].

### Método 3: Estrategia de Salto y Transferencia de Momento Angular (*Flat-to-Edge*)
* **Fundamento Matemático:** El Cubli es capaz de levantarse por sí mismo sin ayuda externa utilizando la conservación de momento angular[cite: 15]. Al frenar bruscamente una rueda de reacción de alta inercia, transfiere todo su momento angular al cuerpo principal del cubo, permitiéndole realizar la transición desde una posición plana (*flat*) hasta apoyarse sobre una arista o vértice (*edge*)[cite: 15]. Basado en las ecuaciones del paper oficial de la ETH Zürich, se calcula analíticamente la velocidad angular de la rueda requerida[cite: 15]:
  $$\omega_{w,\text{req}} = \sqrt{(2 - \sqrt{2})\left(\frac{I_w + I_b + m_w L^2}{I_w^2}\right) \text{Momento Estático} \cdot g}$$
* **Aporte al Prototipo:** Proporciona la referencia analítica inicial del impulso transitorio de impacto (simulado en intervalos de milisegundos mediante funciones senoidales amortiguadas) para comprender cómo la energía cinética de las ruedas se transforma en energía potencial gravitacional en la etapa de levantamiento[cite: 15].

### Método 4: Ecuaciones de Euler-Lagrange (Modelado Dinámico No Lineal)
* **Fundamento Matemático:** El comportamiento mecánico real y no lineal del Cubli se rige mediante las ecuaciones diferenciales de Euler-Lagrange que relacionan la cinemática del cuerpo ($\theta_b$, $\dot{\theta}_b$) y de la rueda ($\dot{\theta}_w$)[cite: 17]. Mediante MATLAB y el integrador numérico de Runge-Kutta de orden superior `ode45`, se resuelve el sistema de ecuaciones diferenciales ordinarias (EDO) en lazo abierto[cite: 17]:
  $$\frac{d}{dt}\left(\frac{\partial L}{\partial \dot{q}}\right) - \frac{\partial L}{\partial q} = \tau$$
* **Aporte al Prototipo:** Permite simular y observar el comportamiento inestable natural del péndulo invertido ante una perturbación leve sin el controlador activo (caída libre del sistema), sirviendo como base matemática fundamental antes de aplicar las leyes de control retroalimentado[cite: 17].

### Método 5: Filtro de Kalman Extendido (EKF) para Estimación Cinemática
* **Fundamento Matemático:** Ante la presencia de ruidos estocásticos y modelos de medición no lineales (como la distancia euclidiana de un radar o sensor de posición $z = \sqrt{p^2 + h^2} + v$), el filtro de Kalman Extendido (EKF) linealiza el sistema localmente utilizando la matriz Jacobiana de derivadas parciales $H = \frac{\partial h}{\partial x}$[cite: 19, 20]. El algoritmo opera en dos ciclos recursivos[cite: 20]:
  1. **Predicción:** Proyecta el estado y la covarianza del error hacia adelante[cite: 20]:
     $$\hat{x}_k(-) = F \hat{x}_{k-1}, \quad P_k(-) = FP_{k-1}F^T + Q$$
  2. **Actualización:** Calcula la Ganancia de Kalman $K_k$ ponderando la incertidumbre del sensor ($R$) frente al modelo ($P$), corrigiendo el estado final estimado[cite: 20]:
     $$K_k = P_k(-)H^T (HP_k(-)H^T + R)^{-1}, \quad \hat{x}_k(+) = \hat{x}_k(-) + K_k(z_k - h(\hat{x}_k(-)))$$
* **Aporte al Prototipo:** Esencial para filtrar de forma óptima el ruido de los sensores y estimar con precisión milimétrica la posición y velocidad en tiempo real[cite: 20].

### Método 6: Estimación de Orientación Tridimensional mediante Cuaterniones
* **Fundamento Matemático:** Los ángulos de Euler tradicionales (Roll, Pitch, Yaw) sufren del fenómeno matemático de bloqueo de cardán (*gimbal lock*). Para evitarlo, el sistema implementa un algoritmo de orientación basado en un cuaternión de 4 parámetros $q = [q_w, q_x, q_y, q_z]^T$ normalizado a magnitud unitaria ($\|q\| = 1$)[cite: 24, 25]:
  * Se estima la dirección de la gravedad teórica a partir del cuaternión actual ($v$)[cite: 25]:
    $$v = \begin{bmatrix} 2(q_x q_z - q_w q_y) \\ 2(q_w q_x + q_y q_z) \\ q_w^2 - q_x^2 - q_y^2 + q_z^2 \end{bmatrix}$$
  * Se calcula el error vectorial cruzado respecto a la lectura real del acelerómetro ($a$) para corregir la deriva acumulada del giróscopo mediante una ganancia proporcional $K_p$ (filtro tipo Mahony/Madgwick) y actualizar el cuaternión mediante integración numérica cinemática[cite: 24, 25]:
    $$\dot{q} = \frac{1}{2} q \otimes \omega_{\text{corregida}}$$

* **Aporte al Prototipo:** Garantiza una representación matemática sólida, continua y libre de singularidades de la orientación tridimensional del Cubli en el espacio físico[cite: 24, 25].

---

## 📁 Documentación
El informe, la monografía técnica y la base teórica completa del proyecto se encuentran adjuntos en este repositorio en el archivo **`CUBLI.pdf`**[cite: 1].
