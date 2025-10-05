
**Tópicos en Ciencias de Computación - 2025-2**

- **Integrantes:**
  - Ibrahim Imanol Jordi, Arquiñigo Jacinto
  - Ian Joaquin Sanchez Alva
  - Eduardo Jose Rivas Siesquen
  - Daniel Orlando Luis Lazaro
- **Profesor:** Luis Martin Canaval Sanchez
- **Fecha:** 05 de octubre de 2025

---

## **1. Resumen Ejecutivo**

En este informe presentamos la solución a un problema de planificación que consiste en organizar el horario de un ensayo de orquesta de manera óptima. El objetivo es encontrar el orden ideal de 9 composiciones musicales que minimice el tiempo total de espera de 5 músicos, respetando ciertas reglas de precedencia y duración.

El problema se resolvió mediante un **algoritmo de búsqueda exhaustiva sistemática**, implementado íntegramente en **Python puro**, sin recurrir a librerías externas de optimización. El enfoque se centró en generar todas las secuencias posibles y evaluarlas de forma inteligente, utilizando técnicas de **filtrado por restricciones** y **ruptura de simetría** para reducir drásticamente el espacio de búsqueda. Este método garantizó encontrar la solución óptima global de manera eficiente y determinista.

---

## **2. Análisis del Problema (Competencia: Análisis de Problemas - Nivel 3)**

### **2.1. Definición y Alcance del Problema**

El reto consiste en determinar la mejor secuencia de las 9 composiciones del ensayo, de manera que el tiempo total de espera de los músicos sea el menor posible. No basta con que el horario sea válido: buscamos la opción **más eficiente**. Este tipo de problema pertenece a la categoría de planificación combinatoria y tiene aplicaciones directas en logística y gestión de recursos.

### **2.2. Identificación de Variables**

- **Datos de entrada (Constantes):**
  - **Composiciones:** Un conjunto de 9 piezas, cada una con una duración fija.
  - **Músicos:** Un grupo de 5 músicos, donde cada uno participa solo en ciertas composiciones (según una matriz de participación fija).
  - **Reglas (Restricciones):** Restricciones de precedencia y adyacencia inalterables, como: *C2 antes de C8*, y *C6 debe ir justo después de C5*.

- **Variable de Decisión Principal:**
  - `Orden`: Una permutación de las 9 composiciones que define la secuencia del ensayo.

- **Variables Derivadas (Calculadas a partir del `Orden`):**
  - `Timeline`: El horario exacto de inicio y fin de cada composición.
  - `Llegada_m` y `Salida_m`: El momento en que cada músico debe llegar y puede irse.
  - `Span_m`: El tiempo total que cada músico permanece en el ensayo (`Salida_m - Llegada_m`).

### **2.3. Relaciones Causa-Efecto**

La única decisión que controlamos es el **`Orden` de las composiciones**. Esta única variable actúa como la "causa" principal que desencadena una cascada de "efectos":
1.  Un `Orden` específico define un `Timeline` único.
2.  El `Timeline` determina los tiempos de `Llegada` y `Salida` de cada músico.
3.  Estos tiempos definen el `Span` de cada músico.
4.  La suma de los `Span` (o de los tiempos de espera) determina la **calidad (costo)** de ese `Orden`.

El desafío radica en que el espacio de posibles "causas" (órdenes) es factorial (`9! = 362,880`), y una pequeña alteración en el orden puede tener un gran impacto en el costo final, haciendo inviable una búsqueda manual.

### **2.4. Conclusiones del Análisis**

- **Impacto:** Un horario ineficiente genera tiempos muertos, reduce la productividad y puede implicar costos operativos si los músicos son remunerados por tiempo de permanencia.
- **Necesidades:**
  - **Técnica:** Se requiere un algoritmo que explore sistemáticamente el espacio de `362,880` permutaciones, pero que sea lo suficientemente inteligente para no evaluar combinaciones inválidas o redundantes.
  - **Práctica:** La solución debe proporcionar un cronograma claro y accionable que minimice el tiempo no productivo del personal.

---

## **3. Modelado Algorítmico (Competencia: Manejo de la Información - Nivel 2)**

### **3.1. Componentes del Algoritmo**

- **Generador de Candidatos:** Se utiliza la función `itertools.permutations` de Python para generar sistemáticamente cada uno de los `9!` órdenes posibles. Este es el punto de partida de nuestra búsqueda exhaustiva.

- **Filtros de Poda (Pruning):** Antes de calcular el costo de un candidato, se aplican filtros para descartarlo si es inviable o redundante:
  1.  **Filtro por Restricciones:** Se verifica si el orden cumple las reglas fijas.
      -   `Posición(C2) < Posición(C8)`
      -   `Posición(C6) == Posición(C5) + 1`
      Si un orden no cumple estas reglas, se descarta inmediatamente.
  2.  **Filtro de Ruptura de Simetría:** Se introduce una regla artificial para eliminar soluciones "espejo". Dado que el costo de un orden y su reverso es idéntico, se impone una restricción arbitraria (ej: `Posición(C1) < Posición(C2)`) para asegurar que solo una de cada par de soluciones simétricas sea evaluada. Esto reduce el trabajo computacional a la mitad sin riesgo de perder la solución óptima.

- **Función de Costo (Objetivo):**
  Para cada orden que pasa los filtros, se calcula el **tiempo total de espera**. La función `calcular_espera_total` implementa esta lógica:
  1.  Construye el `Timeline` a partir del orden y las duraciones.
  2.  Para cada músico, calcula su `Span` y su `tiempo de actividad`.
  3.  El costo del músico es `Span - Actividad`.
  4.  El costo total es la suma de los costos de todos los músicos.

El algoritmo mantiene en memoria el mejor orden y el menor costo encontrados hasta el momento, garantizando que al finalizar la exploración de todos los candidatos válidos, el resultado sea el óptimo global.

---

### **Comentario final**

El modelo desarrollado demuestra que los **problemas de planificación combinatoria** pueden resolverse de manera efectiva y garantizada mediante **búsqueda exhaustiva inteligente** en Python. La clave del éxito no radica en la complejidad del código, sino en el análisis lógico del problema para implementar técnicas de poda y reducción de simetrías que hacen manejable un espacio de búsqueda factorial. Este enfoque, aunque computacionalmente intensivo, es riguroso y proporciona una certeza del 100% en la optimalidad del resultado para problemas de esta escala.