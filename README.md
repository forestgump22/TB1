# 🧠 Proyecto TB1: Optimización de Ensayo de Orquesta

Este repositorio contiene la solución al problema de **Optimización del Horario de un Ensayo de Orquesta**, desarrollado como parte del curso *Tópicos en Ciencias de Computación (2025-2)*.

El trabajo fue implementado íntegramente en **Python puro**, sin dependencias externas, enfocándose en un algoritmo de búsqueda eficiente y técnicas de reducción del espacio de soluciones.

---

## 🧩 Descripción del Problema

El objetivo es determinar el orden óptimo de 9 composiciones musicales para **minimizar el tiempo total de espera de 5 músicos**, cumpliendo con restricciones de precedencia y adyacencia entre las piezas.

El problema se resolvió mediante un algoritmo de **búsqueda exhaustiva sistemática**. En lugar de usar un solver externo, se generaron todas las permutaciones posibles del horario y se evaluaron de forma inteligente, aplicando dos técnicas clave:
1.  **Filtrado por Restricciones:** Descarte inmediato de secuencias que no cumplen las reglas del problema.
2.  **Ruptura de Simetría:** Implementación de una regla lógica para eliminar soluciones "espejo" redundantes, reduciendo el espacio de búsqueda a la mitad y garantizando la optimalidad sin cálculos innecesarios.

---

## 📁 Contenido del Repositorio

-   **`TB1_Proyecto3.ipynb`** → Notebook de Jupyter con el código principal. Contiene la definición del problema, las funciones de cálculo, el algoritmo de optimización y la visualización de resultados.
-   **`informe.md`** → Informe técnico que explica el análisis del problema, la lógica del algoritmo, las técnicas de optimización aplicadas y los resultados obtenidos.
-   **`README.md`** → Este archivo, con la documentación general del proyecto.

---

## ⚙️ Instrucciones de Ejecución

1.  **Clonar el repositorio:**
    ```bash
    git clone [URL_DEL_REPOSITORIO]
    cd [NOMBRE_DEL_DIRECTORIO]
    ```

2.  **Instalar dependencias:**  
    El proyecto **no requiere ninguna librería externa**. Solo necesitas tener Python instalado (versión 3.6 o superior).

3.  **Ejecutar el Notebook:**  
    Abre el archivo `TB1_Proyecto3.ipynb` en un entorno como Jupyter Notebook, JupyterLab o Google Colab y ejecuta las celdas en orden.
    
    El programa mostrará en la salida la secuencia óptima del ensayo, el tiempo total de espera minimizado y un resumen detallado por músico.

---

## 👥 Autores

-   Ian Joaquin Sanchez Alva
-   [Nombre del Alumno 2]

---

## 📚 Notas

-   Este proyecto fue desarrollado con fines académicos dentro del curso Tópicos en Ciencias de Computación.
-   Es un excelente ejemplo de cómo resolver problemas de optimización combinatoria utilizando algoritmos fundamentales de Python y pensamiento lógico para gestionar la complejidad computacional.
