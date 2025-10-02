# **Informe de Proyecto (TB1): Modelado y Solución del Problema de Asignación de Tripulaciones Aéreas mediante Programación por Restricciones**

**Tópicos en Ciencias de Computación - 2025-2**

- **Integrantes:**
Ibrahim Imanol Jordi Arquiñigo Jacinto
    "  - Ian Joaquin Sanchez Alva
    "  - Eduardo Jose Rivas Siesquen
    "  - Daniel Orlando Luis Lázaro 
- **Profesor:** Luis Martin Canaval Sanchez
- **Fecha:** 27 de setiembre de 2025

---

## **1. Resumen Ejecutivo**

El presente informe detalla la solución al problema de asignación de tripulaciones aéreas, un desafío combinatorio clásico en la investigación de operaciones y la inteligencia artificial. El objetivo es asignar 20 empleados a 10 vuelos, respetando un conjunto complejo de reglas que incluyen tamaño de la tripulación, cuotas de roles, requisitos de idiomas y un período de descanso obligatorio.

Para abordar este problema, se ha optado por modelarlo como un **Problema de Satisfacción de Restricciones (CSP)**. Esta metodología permite definir el problema de manera declarativa, especificando las variables, sus dominios y las restricciones que deben cumplirse. La solución fue implementada en Python utilizando la biblioteca **Google OR-Tools**, específicamente su solver CP-SAT. El resultado es un modelo robusto que genera un cronograma de asignación factible, demostrando la idoneidad del paradigma CSP para resolver problemas de planificación del mundo real y cumpliendo con el logro principal del curso.

---

## **2. Análisis del Problema (Competencia: Análisis de Problemas - Nivel 3)**

Se realizó un análisis estructurado para definir el problema, sus variables y las relaciones causa-efecto que lo gobiernan.

### **2.1. Definición y Alcance del Problema**

El problema consiste en encontrar una **asignación factible** de empleados a vuelos que satisfaga todas las condiciones estipuladas. El alcance se limita a la factibilidad; no se busca optimizar un objetivo (como minimizar costos), lo que lo define como un CSP puro. El contexto es el de la planificación de recursos humanos en una aerolínea, donde la eficiencia operativa y el cumplimiento de normativas son críticos.

### **2.2. Identificación de Variables del Problema**

-   **Variables Externas (Datos de Entrada):** Definen el entorno del problema.
    -   **Entidades Empleado:** Conjunto de 20 personas con atributos fijos: `Nombre`, `Rol` (Steward/Hostess), `Idiomas`.
    -   **Entidades Vuelo:** Conjunto de 10 eventos con requisitos fijos: `Tamaño de Tripulación`, `Mínimo de Hostesses`, `Mínimo de Stewards`, `Requisito de Idiomas`.

-   **Variables Internas (Variables de Decisión):** Definen el espacio de soluciones del problema.
    -   **Matriz de Asignación `A[e][v]`:** Variable booleana que toma el valor `1` si el empleado `e` es asignado al vuelo `v`, y `0` en caso contrario. Estas son las variables que el solver debe determinar.

### **2.3. Análisis de Relaciones Causa-Efecto**

Las restricciones definen las complejas interdependencias del sistema. La decisión de asignar un empleado a un vuelo (`A[e][v] = 1`) origina los siguientes efectos:
1.  **Efecto en el Vuelo:** Se consume un cupo en el vuelo `v`, contribuyendo a satisfacer sus requisitos de tamaño, rol e idioma.
2.  **Efecto en el Empleado:** La disponibilidad del empleado `e` se ve afectada, ya que no podrá ser asignado a los vuelos `v+1` y `v+2`.

**Priorización de Variables/Restricciones:** La restricción del **período de descanso** es la de mayor impacto en la complejidad del problema. Mientras las otras restricciones son "locales" (se aplican a cada vuelo de forma independiente), esta es "global" a lo largo del eje temporal para cada empleado. Conecta las decisiones de un vuelo con las de los siguientes, reduciendo drásticamente el espacio de búsqueda y siendo el principal desafío para encontrar una solución.

### **2.4. Conclusiones sobre el Impacto y Necesidades a Cubrir**

-   **Impacto del Problema:** Una mala asignación puede llevar a vuelos sin personal suficiente, incumplimiento de normativas de seguridad (idiomas, roles) y agotamiento del personal, afectando la operatividad y reputación de la aerolínea.
-   **Necesidades Técnicas y de Negocio:**
    -   **Técnica:** Se necesita una solución automatizada, rápida y garantizada para encontrar un cronograma válido, dado que una búsqueda manual es inviable y propensa a errores.
    -   **Negocio:** Se requiere un sistema que asegure la continuidad operativa, el cumplimiento normativo y el bienestar de los empleados. La solución propuesta atiende directamente estas necesidades.

---

## **3. Modelado como CSP (Competencia: Manejo de la Información - Nivel 2)**

Se seleccionó el paradigma CSP por su capacidad para representar declarativamente las relaciones entre las variables.

### **3.1. Componentes Formales del Modelo**

-   **Variables (X):** Un conjunto de 200 variables booleanas `A_ev` donde `e` es el índice del empleado (0-19) y `v` es el índice del vuelo (0-9).
-   **Dominios (D):** Cada variable `A_ev` tiene el dominio `{0, 1}`.
-   **Restricciones (C):**
    1.  **Restricción de Tamaño de Tripulación:** `∀v: Σ_e A_ev = TamañoRequerido_v`
    2.  **Restricción de Roles Mínimos:** `∀v: Σ_{e∈Hostesses} A_ev ≥ MinHostesses_v` y `∀v: Σ_{e∈Stewards} A_ev ≥ MinStewards_v`
    3.  **Restricción de Idiomas:** `∀v, ∀l: Σ_{e∈Hablan(l)} A_ev ≥ 1`
    4.  **Restricción de Período de Descanso:** `∀e, ∀v<8: A_ev = 1 ⇒ (A_{e,v+1} = 0 ∧ A_{e,v+2} = 0)`
### **3.2. Uso de Restricciones Globales**

El modelo utiliza restricciones que, aunque expresadas como sumas o implicaciones, son manejadas por el solver como potentes **restricciones globales**:

-   **Restricción de Cardinalidad:** Las restricciones de tamaño y requisitos mínimos son, en esencia, `Global Cardinality Constraints (GCC)`. El solver las utiliza para propagar información de manera muy eficiente.
-   **Restricción de Secuencia:** La regla de descanso prohíbe el patrón `1, 1` o `1, 0, 1` en las asignaciones de un empleado. Esta es una forma de **restricción de secuencia** que el solver puede analizar para podar ramas enteras del árbol de búsqueda.

---

## **4. Implementación y Herramientas**

### **4.1. Elección del Solver: Google OR-Tools (CP-SAT)**

Se eligió el solver CP-SAT de Google OR-Tools por su enfoque declarativo, alto rendimiento y especialización en problemas de satisfacción de restricciones.

### **4.2. Estructura del Proyecto**

La implementación completa, incluyendo el código, los comentarios explicativos, la ejecución y los resultados, se encuentra consolidada en un único **Jupyter Notebook** llamado `TB1_Asignacion_Tripulacion.ipynb`. Este formato permite una presentación interactiva y reproducible del proyecto, donde cada paso del proceso está claramente documentado.

---

## **5. Resultados y Discusión**

### **5.1. Solución Encontrada**

El solver fue capaz de encontrar una solución factible que satisface todas las restricciones impuestas. **La asignación completa y detallada para los 10 vuelos se puede consultar directamente en la celda de resultados del notebook `TB1_Asignacion_Tripulacion.ipynb`**. La validez de la solución se puede verificar contrastando la salida con las restricciones descritas en la sección de análisis. La presentación interactiva del notebook permite no solo ver el resultado, sino también re-ejecutar el modelo para comprobar su funcionamiento.

---

## **6. Conclusiones y Reflexión Final**

### **6.1. Conclusiones Técnicas**

El modelado del problema como un CSP fue un éxito. Demostró ser una técnica poderosa y flexible para resolver problemas de planificación complejos. La naturaleza declarativa del solver CP-SAT permitió una implementación rápida y fiable, donde la principal tarea fue traducir correctamente las reglas del negocio a restricciones lógicas y matemáticas.

### **6.2. Cumplimiento del Logro del Curso**

Este trabajo evidencia el logro del curso al:
-   **Modelar una solución a una tarea real y compleja:** Se abordó un problema logístico no trivial de una aerolínea.
-   **Aplicar técnicas de IA avanzada:** Se utilizó la Programación por Restricciones, un pilar de la IA simbólica.
-   **Evidenciar responsabilidad ética y profesional:** La solución garantiza que se cumplan las reglas de descanso, promoviendo el bienestar de los empleados.
-   **Aprendizaje continuo y pensamiento crítico:** El proceso requirió analizar el problema desde múltiples ángulos (variables, restricciones, herramientas) y elegir la mejor solución técnica, justificando cada decisión de diseño.

---

## **7. Declaración Detallada de Uso de IA con Ingeniería de Prompts**

En este proyecto, la Inteligencia Artificial Generativa fue empleada como una herramienta de desarrollo avanzado, dirigida mediante una estrategia de **Ingeniería de Prompts** precisa y por fases. Este enfoque nos permitió acelerar la implementación mientras manteníamos el control total sobre la arquitectura y la lógica del modelo.

-   **Servicio Utilizado:** Gemini, de Google.

A continuación, se detallan los prompts exactos utilizados para construir el modelo CSP paso a paso.

### **Fase 1: Modelado Asistido del Problema**

El objetivo en esta fase fue traducir las reglas del negocio a un modelo CSP formal, guiando a la IA en cada paso.

1.  **Establecimiento del Contexto y las Variables:**
    ```
    "Estoy modelando un problema de asignación de tripulaciones como un CSP usando Python y Google OR-Tools CP-SAT. La variable de decisión principal será una matriz booleana llamada `assignments[employee_idx][flight_idx]`. Ayúdame a agregar las restricciones una por una."
    ```

2.  **Restricción de Tamaño de Tripulación:**
    ```
    "Para la primera regla: cada vuelo `v` debe tener un número exacto de tripulantes, definido en la lista `crew_size`. Para cada vuelo, agrega una restricción que asegure que la suma de `assignments[e][v]` sobre todos los empleados `e` sea exactamente igual al valor correspondiente en `crew_size[v]`."
    ```

3.  **Restricción de Roles Mínimos (Azafatas y Stewards):**
    ```
    "Ahora, para los roles. Para cada vuelo `v`, la suma de `assignments[e][v]` donde `e` pertenece al grupo de azafatas debe ser mayor o igual a `min_hostesses[v]`. Agrega una restricción similar para los stewards usando la lista `min_stewards[v]`."
    ```

4.  **Restricción de Idiomas:**
    ```
    "Para los requisitos de idioma: por cada vuelo `v`, debe haber al menos un tripulante que hable francés, uno que hable español y uno que hable alemán. Agrega tres restricciones separadas por vuelo para asegurar que la suma de `assignments[e][v]` sobre los empleados que hablan cada idioma sea mayor o igual a 1."
    ```

5.  **Restricción de Período de Descanso:**
    ```
    "Finalmente, la restricción de descanso, que es secuencial. Si un empleado `e` es asignado al vuelo `v`, no puede ser asignado a `v+1` ni a `v+2`. Para cada empleado y cada vuelo, implementa esto usando `AddImplication`. El prompt sería: 'Si `assignments[e][v]` es verdadero, entonces `assignments[e][v+1]` debe ser falso' y 'Si `assignments[e][v]` es verdadero, entonces `assignments[e][v+2]` debe ser falso'."
    ```

### **Fase 2: Generación de Código y Presentación**

Con el modelo lógicamente definido, se solicitó a la IA que generara el código ejecutable.

```
"Excelente. Ahora, empaqueta todo el modelo que hemos definido en una única función de Python. Esta función debe crear el `CpModel`, agregar todas las restricciones que especificamos, invocar el solver CP-SAT y, si encuentra una solución, imprimir los resultados de forma clara, mostrando la lista de tripulantes asignados a cada vuelo."
```

### **Fase 3: Documentación Técnica Asistida**

Para el informe, se utilizó un prompt final para generar un borrador.

```
"Basado en el problema de asignación de tripulaciones y el modelo CSP que construimos, redacta un borrador para un informe técnico. Estructúralo con secciones para 'Análisis del Problema', 'Modelado CSP', 'Implementación' y 'Resultados'. Asegúrate de justificar por qué CSP es un enfoque adecuado para este tipo de problema de planificación."
```

Este borrador fue posteriormente revisado, editado y completado por el equipo para asegurar su calidad y reflejar nuestras conclusiones finales. Este método de prompting específico nos permitió utilizar la IA de manera eficiente, como una herramienta precisa bajo nuestro control directo.
