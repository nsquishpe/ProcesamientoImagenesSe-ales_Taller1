# Reto: Preparación y Validación Reproducible de Datos Industrial (L-DED)

**Grupo 5: Junior Anchundia, Jeremy Garzon, Noelia Quishpe ** | Taller 1 - Análisis, Sincronización y Reducción de Datos

---

## Resumen Ejecutivo

Este proyecto procesa y valida un conjunto de datos industriales proveniente de un sistema de **deposición láser (L-DED)**: sincroniza 41,645 imágenes TIFF (16 bits) capturadas por un sensor de alta resolución con telemetría en CSV que registra posiciones 3D, orientación, estado del láser y temperaturas del proceso.

**Objetivo Principal**: Garantizar calidad, integridad y sincronización de datos para análisis posterior del comportamiento de la piscina fundida durante manufactura aditiva.

---

## Contexto Técnico

### Tecnología L-DED (Laser Directed Energy Deposition)

Un láser de alta potencia funde material metálico depositándolo progresivamente, capa por capa. La variación no controlada de la piscina fundida puede causar:
- Deformaciones y porosidad
- Falta de fusión
- Discontinuidades estructurales

**Solución**: Análisis simultáneo de imágenes del proceso + datos de telemetría para caracterizar y controlar el comportamiento de la piscina.

---

## 📁 Estructura del Proyecto

```
proyecto/
├── Data/
│   ├── file.csv                 # Telemetría del sistema
│   ├── images/                  # 41,645 archivos TIFF (16 bits)
│   └── outputs/                 # Resultados procesados
├── utils/
│   ├── requirements.txt          # Dependencias
│   └── [funciones reutilizables]
└── Reto_Taller1_Grupo5.ipynb    # Notebook principal
```

### Datos de Entrada

| Componente | Formato | Características |
|-----------|---------|-----------------|
| **Imágenes** | TIFF (16 bits) | ~41,645 archivos, sensor de alta resolución |
| **Telemetría** | CSV | Posición XYZ, orientación, estado láser, temperaturas |
| **Timestamp** | Unix | Embebido en nombre de archivo TIFF (10-13 dígitos) |

---

## Flujo de Trabajo (CRISP-DM)

El proyecto aplica la metodología **CRISP-DM** de forma estructurada:

### 1. **Comprensión del Problema**
- Contexto de manufactura aditiva (L-DED)
- Identificación de variables críticas (piscina fundida)
- Necesidad de sincronización imagen-telemetría

### 2. **Comprensión de Datos**
- Exploración de archivos TIFF
- Lectura de telemetría CSV
- Identificación de timestamps

### 3. **Preparación de Datos**
- Extracción de metadatos de imágenes
- Lectura e indexación de telemetría
- **Procesamiento paralelo** para optimización
- Sincronización frame-registro

### 4. **Modelado**
- Validación de consistencia
- Análisis estadístico de intensidades
- Reducción de datos sin pérdida de información

### 5. **Evaluación**
- Verificación de calidad e integridad
- Métricas de sincronización
- Reportes de validación

---

## Componentes Clave

### 1. **Lectura de Imágenes TIFF**

```python
def leer_imagen_tiff(ruta_archivo):
    """
    Lee archivo TIFF preservando tipo de dato original (16 bits).
    Retorna: np.ndarray sin normalizar
    """
```

**Características**:
- Mantiene precisión de 16 bits (dato original del sensor)
- No normaliza valores (evita pérdida de información)
- Compatible con múltiples formatos (.tiff, .tif)

### 2. **Extracción de Timestamp**

```python
def extraer_timestamp_archivo(nombre_archivo):
    """
    Extrae timestamp Unix del nombre del archivo.
    Soporta: 10 dígitos (segundos) y 13 dígitos (milisegundos)
    """
```

**Importancia**:
- Vincula cada frame con su registro de telemetría
- Permite sincronización temporal precisa
- Detecta gaps y duplicados en la secuencia

### 3. **Procesamiento Paralelo (Optimización Crítica)**

**Mejora de Rendimiento**: 
- Lectura secuencial: **9 minutos** ⏱️
- Lectura paralela (8 workers): **3 minutos** ⚡
- **Reducción: 67% del tiempo**

```python
num_workers = 8  # Ajustable según recursos disponibles
# Procesa múltiples archivos simultáneamente
```

### 4. **Extracción de Metadatos**

Para cada imagen se captura:
- **Timestamp**: Sincronización temporal
- **Shape & dtype**: Validación de consistencia
- **Percentiles (p5, p25, p50, p75, p95)**: Distribución de intensidades
- **Min/Max/Media**: Estadísticas básicas
- **Pixels_cero**: Cantidad de píxeles con valor 0

**Nota sobre Muestreo**: Debido a restricciones de memoria, los percentiles se calculan sobre una muestra controlada de píxeles no-cero como estimación de la distribución global.

### 5. **Validación de Consistencia**

```python
# Verificar dimensiones uniformes
shapes_unicos = set([f['shape'] for f in frames])
# Verificar tipos de dato
dtypes_unicos = set([f['dtype'] for f in frames])
```

Garantiza que todas las imágenes tengan:
- Mismas dimensiones
- Mismo tipo de dato (uint16)
- Estructura coherente

---

## Análisis Implementado

### ACTIVIDAD 1: Describir las Imágenes

Caracterización completa sin modificar datos originales:

1. **Validación estructural**: Dimensiones uniformes, dtype consistente
2. **Análisis estadístico**: Min, max, media, percentiles
3. **Distribución de intensidades**: Histogramas, coeficiente de variación
4. **Detección de anomalías**: Imágenes completamente negras, píxeles saturados

### ACTIVIDAD 2-5: (Estructura continuada en notebook)

- Sincronización frame-telemetría
- Reducción de datos
- Validación de integridad
- Generación de reportes

---

## 🛠️ Instalación y Uso

### Requisitos Previos

- Python 3.8+
- 8+ GB RAM recomendado
- Estructura de carpetas: `Data/file.csv` y `Data/images/`

### Instalación

```bash
# 1. Navegar al directorio del proyecto
cd ruta/del/proyecto

# 2. Instalar dependencias
pip install -r utils/requirements.txt
```

### Ejecución

```bash
# Abrir Jupyter Notebook
jupyter notebook Reto_Taller1_Grupo5.ipynb

# Ejecutar celdas secuencialmente (Shift + Enter)
# El notebook guía paso a paso el análisis
```

### Outputs Generados

Los resultados se guardan en `Data/outputs/`:
- CSV mejorado con frames sincronizados
- Registros JSON de metadatos
- Figuras de análisis y validación
- Reportes de integridad de datos

---

## 📈 Resultados y Métricas

| Métrica | Valor |
|---------|-------|
| Archivos TIFF procesados | 41,645 |
| Resolución temporal | 10-13 dígitos Unix |
| Tiempo procesamiento secuencial | ~9 min |
| Tiempo procesamiento paralelo | ~3 min |
| Mejora de rendimiento | 67% reducción |
| Dimensiones de imagen | Uniforme (validado) |
| Tipo de dato | uint16 (16 bits) |
| Consistencia datos | ✅ Verificada |

---

## 🔍 Detalles Técnicos Clave

### Manejo de Memoria

**Desafío**: 41,645 imágenes × resolucionalta = consumo excesivo

**Solución**: 
- Muestreo controlado de píxeles no-cero
- Cálculo de percentiles sobre muestra representativa
- Iteración sin cargar dataset completo en memoria

### Sincronización Temporal

**Método**:
1. Extraer timestamp de nombre del archivo TIFF
2. Buscar registro en CSV con timestamp más cercano
3. Validar diferencia temporal (tolerancia configurable)
4. Crear índices de correspondencia

### Validación de Integridad

- Verificar gaps en secuencia temporal
- Detectar registros duplicados
- Confirmar rangos de valores esperados
- Validar coherencia posición 3D - orientación

---

## Metodología y Buenas Prácticas

### CRISP-DM Adaptado

Se aplicaron explícitamente los 5 pasos:
1. Business/Problem Understanding
2. Data Understanding
3. Data Preparation
4. Modeling (análisis y validación)
5. Evaluation

### Principios de Programación

✅ **Funciones reutilizables**: Código modular y mantenible  
✅ **Documentación inline**: Docstrings descriptivos  
✅ **Manejo de rutas**: `pathlib` para compatibilidad multiplataforma  
✅ **Procesamiento paralelo**: `concurrent.futures` para eficiencia  
✅ **Validación continua**: Chequeos en cada etapa  

---

## Transferencia de Conocimiento

### Para Replicar Este Análisis

1. **Preparar datos**: 
   - CSV con telemetría en `Data/file.csv`
   - Imágenes TIFF en `Data/images/`

2. **Ejecutar notebook**:
   - Sigue las celdas secuencialmente
   - Lee las notas explicativas

3. **Adaptar parámetros**:
   - `num_workers`: Ajustar según CPU disponibles
   - `sample_size`: Muestreo para percentiles
   - `tolerance`: Tolerancia en sincronización

### Para Entender el Código

- Cada función tiene **docstring detallado**
- Las celdas **markdown explican el "por qué"**
- Los **comentarios** justifican decisiones técnicas
- Los **prints intermedios** muestran progreso y validación

---

## Consideraciones Importantes

### Limitaciones Conocidas

- **Memoria**: Muestreo de píxeles para percentiles (estimación)
- **Tiempo**: Procesamiento largo incluso con paralelización
- **Timestamp**: Asume formato específico en nombre de archivo

### Recomendaciones

1. Usar máquina con **8+ GB RAM**
2. Ejecutar en **Linux/Mac** para mejor rendimiento paralelo
3. Documentar cualquier cambio en estructura de datos
4. Validar resultados antes de análisis downstream

---

## Próximas Etapas

Con datos preparados y validados, los siguientes pasos podrían ser:

- Análisis de comportamiento de piscina fundida
- Modelado de defectos (porosidad, falta de fusión)
- Predicción de calidad con ML
- Optimización de parámetros de proceso
- Visualización 3D de trayectoria vs. imágenes

---
