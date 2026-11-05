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

## 🔬 1. Prototipo Experimental

### Descripción General
El sistema se implementa para resolver un problema clásico de control automático donde el sistema posee más grados de libertad que actuadores independientes[cite: 4]. El cubo se apoya sobre uno de sus vértices, lo que lo vuelve intrínsecamente inestable[cite: 4].

* **Objetivo principal:** Mantener el cubo estático sobre su vértice de forma autónoma, corrigiendo instantáneamente cualquier inclinación o perturbación externa[cite: 4].
* **Propósito académico:** Comprender a profundidad el modelado de sistemas físicos inestables y diseñar leyes de control robustas capaces de responder en milisegundos[cite: 4].

### Vista del Prototipo
![Prototipo Experimental Cubli](prototitpo_exp.png)[cite: 31]

### Lista de Componentes y Hardware
* **Microcontrolador:** ESP32 DevKit V1 (cerebro del sistema)[cite: 5, 6].
* **Sensor Inercial:** Módulo GY-521 (IMU MPU6050)[cite: 5].
* **Actuadores:** Motores *Brushless* / Nidec[cite: 5, 10].
* **Mecánica:** Volantes de inercia de metal pesado[cite: 5, 6].
* **Alimentación y Energía:** Batería LiPo 500 mAh (3.7V), condensadores y reguladores[cite: 5].
* **Componentes Pasivos:** Resistencias ($10\,\text{k}\Omega$, $33\,\text{k}\Omega$, $6.8\,\text{k}\Omega$) y Buzzer[cite: 5].

### Principio de Funcionamiento Físico
1. **Detección:** El sensor MPU6050 lee constantemente la inclinación del cubo[cite: 6].
2. **Cálculo:** El ESP32 procesa la información y determina la fuerza exacta para contrarrestar la caída[cite: 6].
3. **Contra-Torque:** Los motores aceleran bruscamente los volantes de inercia. Por acción y reacción, esto genera una fuerza en sentido contrario que devuelve el chasis a su posición de equilibrio[cite: 6].

### Adquisición y Depuración de la Posición Espacial (IMU)
El procesamiento de la señal del MPU6050 se divide en cuatro etapas críticas[cite: 7]:
* **Lecturas Brutas:** Valores nativos directos de aceleración y velocidad angular[cite: 7].
* **Ruido de Alta Frecuencia:** Filtrado de fluctuaciones causadas por vibraciones mecánicas[cite: 7].
* **Deriva del Giroscopio:** Corrección del error progresivo acumulado en el tiempo[cite: 7].
* **Fusión Sensorial:** Algoritmos de combinación de señales para reducir el error de posición por debajo de $0.35^\circ$[cite: 7].

---

## 🖼️ Diagrama del Circuito Electrónico

![Diagrama Electrónico del Cubli](Diagrama_Cubli.png)[cite: 30]

---

## ⚙️ 2. Métodos Utilizados (Simulaciones y Modelado)

El desarrollo computacional incluye la implementación y validación de 6 métodos clave para el control y estimación del sistema:

### Método 1: Control Óptimo LQR (Regulador Lineal Cuadrático)
* **Descripción:** Modela el sistema linealizado en espacio de estados para encontrar de forma óptima la matriz de ganancias de control.
* **Aporte:** Minimiza simultáneamente el error de orientación del cubo y el consumo de energía de los motores, garantizando una estabilidad asintótica en lazo cerrado[cite: 11].

### Método 2: Caracterización de Actuadores y Saturación de Voltaje
* **Descripción:** Simula el comportamiento real de los motores DC incorporando zonas muertas y restricciones físicas.
* **Aporte:** Aplica una limitación estricta basada en el voltaje real de la batería LiPo ($12\,\text{V}$), asegurando que el algoritmo sea completamente viable al llevarlo al microcontrolador físico[cite: 13].

### Método 3: Estrategia de Salto (*Flat-to-Edge*)
* **Descripción:** Analiza la transferencia de momento angular para permitir que el cubo se levante por sí mismo sin ayuda externa.
* **Aporte:** Calcula analíticamente el impulso transitorio necesario para que la energía cinética de las ruedas se convierta en la inclinación requerida para apoyarse sobre una arista[cite: 15].

### Método 4: Ecuaciones de Euler-Lagrange (Dinámica No Lineal)
* **Descripción:** Modela el comportamiento mecánico completo del sistema mediante ecuaciones diferenciales ordinarias resueltas con el integrador numérico `ode45`[cite: 17].
* **Aporte:** Permite estudiar la respuesta libre e inestable del sistema (caída estilo péndulo invertido) antes de activar los controladores[cite: 17].

### Método 5: Filtro de Kalman Extendido (EKF)
* **Descripción:** Algoritmo recursivo de estimación estructurado en dos fases: predicción y actualización mediante matrices Jacobianas[cite: 19, 20].
* **Aporte:** Filtra eficientemente el ruido estocástico de los sensores para estimar con máxima precisión la posición y velocidad del sistema[cite: 20].

### Método 6: Estimación de Orientación con Cuaterniones
* **Descripción:** Implementa un sistema de 4 parámetros para el seguimiento de la orientación tridimensional[cite: 24, 25].
* **Aporte:** Evita por completo el problema del bloqueo de cardán (*gimbal lock*) propio de los ángulos de Euler tradicionales, logrando un cálculo de Roll, Pitch y Yaw robusto y continuo[cite: 24, 25, 26].

---

## 📁 Documentación
El informe y la monografía académica completa del proyecto se encuentran adjuntos en este repositorio en el archivo **`CUBLI.pdf`**[cite: 1].
