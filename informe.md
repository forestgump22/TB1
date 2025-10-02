# **Informe de Proyecto (TB1): Modelado y Solución del Problema de Asignación de Tripulaciones Aéreas mediante Programación por Restricciones**

**Tópicos en Ciencias de Computación - 2025-2**

- **Integrantes:**
  - [Nombre del Alumno 1]
  - [Nombre del Alumno 2]
  - [Nombre del Alumno 3]
- **Profesor:** [Nombre del Profesor]
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
