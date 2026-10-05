# Preparación y Validación de Datos Industriales L-DED

**Grupo 5: Junior Anchundia, Jeremy Garzon, Noelia Quishpe**

Este proyecto trabaja con datos obtenidos de un proceso de manufactura aditiva por deposición láser (**L-DED**). El conjunto de datos incluye imágenes TIFF de 16 bits y registros de telemetría almacenados en un archivo CSV.

El objetivo es organizar, validar y sincronizar la información para que pueda ser utilizada posteriormente en el análisis del comportamiento de la piscina fundida.

Durante el procesamiento se realizan tareas como:

- Lectura y validación de imágenes TIFF.
- Extracción de timestamps desde los nombres de archivo.
- Análisis básico de intensidades y metadatos.
- Sincronización entre imágenes y telemetría.
- Detección de posibles inconsistencias, duplicados o faltantes.
- Procesamiento paralelo para reducir el tiempo de ejecución.
- Generación de archivos procesados para análisis posteriores.

La estructura principal del proyecto incluye:

```text
Data/
├── file.csv              # Datos principales
├── images/               # Imágenes originales
├── images_trabajo/       # Imágenes muestra para trabajar
└── outputs/              # Resultados procesados

utils/
└── requirements.txt      # Dependencias del entorno

Code/
├── Reto_Taller1_Grupo5.ipynb  # Resolución Taller 1
└── Reto_Taller2_Grupo5.ipynb  # Resolución Taller 2

