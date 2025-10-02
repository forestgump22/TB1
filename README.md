# Proyecto TB1: Asignación de Tripulaciones Aéreas con CSP

Este repositorio contiene la solución al problema de **Asignación de Tripulaciones Aéreas**, desarrollado para el curso "Tópicos en Ciencias de Computación" (2025-2). La implementación completa se encuentra en un único Jupyter Notebook.

## Descripción del Problema

El objetivo es asignar una tripulación de 20 empleados a 10 vuelos diferentes, cumpliendo con un conjunto de restricciones complejas (tamaño de la tripulación, roles, idiomas y descanso). El problema fue modelado como un **Problema de Satisfacción de Restricciones (CSP)** y resuelto con Google OR-Tools.

## Contenido del Repositorio

- **`TB1_Asignacion_Tripulacion.ipynb`**: El Jupyter Notebook que contiene el análisis, el código, la ejecución y los resultados del proyecto. **Este es el archivo principal.**
- **`informe.md`**: El informe formal del proyecto, que resume el enfoque y los hallazgos.
- **`README.md`**: Este archivo.

## Instrucciones para la Ejecución

1.  **Clonar el repositorio:**
    ```bash
    git clone [URL-de-tu-repositorio]
    cd [nombre-del-repositorio]
    ```

2.  **Abrir el Notebook:**
    Abre el archivo `TB1_Asignacion_Tripulacion.ipynb` en un entorno de Jupyter, como **Jupyter Lab, VS Code, o Google Colab**.

3.  **Ejecutar las celdas:**
    Puedes ejecutar todas las celdas en orden ("Run All").
    - La primera celda de código (`!pip install ortools`) instalará la dependencia necesaria.
    - La última celda ejecutará el solver y mostrará la asignación de tripulación directamente en el notebook.

## Autores

- Ibrahim Imanol Jordi Arquiñigo Jacinto
- Ian Joaquin Sanchez Alva
- [Nombre del Alumno 3]
