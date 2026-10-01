# Whisper Transcriptor

Herramienta local de transcripción automática de archivos de audio mediante **OpenAI Whisper**, ejecutada en Windows y acelerada mediante GPU AMD.

El proyecto nace como una alternativa local a un flujo anterior basado en **Google Colab + Whisper**, con el objetivo de poder transcribir archivos de audio directamente desde el ordenador, automatizar el procesamiento de varios audios y guardar las transcripciones en archivos `.txt`.

Actualmente el proyecto permite:

* Cargar el modelo `medium` de Whisper.
* Utilizar la GPU AMD Radeon RX 7900 XT mediante PyTorch/ROCm.
* Detectar automáticamente los archivos de audio almacenados en una carpeta.
* Procesar varios audios.
* Transcribir los audios en español.
* Guardar automáticamente cada transcripción como archivo `.txt`.
* Mantener separados los archivos de entrada (`audios/`) y las transcripciones generadas (`transcripciones/`).
* Medir el tiempo empleado en la transcripción.
* Trabajar completamente de forma local, sin necesidad de subir los audios a servicios externos.

---

## 1. Objetivo del proyecto

El objetivo principal es construir un **transcriptor local y reutilizable de archivos de audio** basado en Whisper.

El proyecto comenzó a partir de un flujo de trabajo utilizado en Google Colab.

Una de las ventajas principales es que el procesamiento se realiza en el propio ordenador, evitando tener que cargar cada audio manualmente en Google Colab.

---

# 2. Tecnologías utilizadas

## Software principal

* Python 3.11
* OpenAI Whisper
* PyTorch
* ROCm
* FFmpeg
* VS Code
* Jupyter Notebook
* Conda

## Hardware utilizado

* **GPU:** AMD Radeon RX 7900 XT
* **VRAM:** 20 GB
* **Sistema operativo:** Windows 11

La GPU es utilizada mediante la compatibilidad de PyTorch con ROCm.

---

# 3. Estructura del proyecto

### `transcriptor.ipynb`

Es el notebook principal del proyecto.

Contiene las diferentes fases del programa:

1. Importación de librerías.
2. Comprobación del entorno.
3. Carga del modelo Whisper.
4. Transcripción de archivos.
5. Guardado de resultados.
6. Procesamiento de múltiples audios.

### `audios/`

Contiene los archivos de audio que se quieren transcribir.

El programa obtiene los archivos directamente de esta carpeta, por lo que no es necesario modificar manualmente el código cada vez que se añade un nuevo audio.

### `transcripciones/`

Contiene las transcripciones generadas automáticamente.

La carpeta se crea automáticamente cuando es necesaria.

---

# 4. Entorno de Python

El proyecto utiliza un entorno Conda independiente:

```text
transcriptor_proj
```

La versión de Python utilizada en el entorno es:

```text
Python 3.11.16
```

Esto permite mantener separadas las dependencias del proyecto respecto al Python global del sistema.

La estructura conceptual es:

```text
Windows
   │
   └── Miniconda
         │
         └── transcriptor_proj
                │
                ├── Python 3.11
                ├── Whisper
                ├── PyTorch
                ├── FFmpeg
                └── Jupyter
```

---

# 5. PyTorch y GPU AMD

Una parte importante del proyecto es utilizar la GPU AMD en lugar de realizar toda la transcripción mediante CPU.

Aunque el hardware es AMD, PyTorch utiliza la interfaz compatible denominada `cuda` para acceder al dispositivo.

---

# 6. FFmpeg

Whisper necesita FFmpeg para trabajar con los diferentes formatos de audio.

Durante la configuración se utilizó:

```text
imageio-ffmpeg
```

Esta instalación proporciona un ejecutable de FFmpeg para Windows:

```text
ffmpeg-win-x86_64-v7.1.exe
```

---

## 6.1. Problema encontrado con FFmpeg

Durante las primeras pruebas apareció un problema relacionado con la localización de FFmpeg.

Aunque el ejecutable estaba instalado dentro del entorno de Conda, el kernel utilizado por Jupyter no lo estaba encontrando correctamente mediante el `PATH`.

Esto provocó errores relacionados con la localización de FFmpeg.

Finalmente se comprobó que el ejecutable estaba disponible dentro de:

```text
transcriptor_proj\Scripts
```

y se consiguió que el notebook pudiera utilizarlo.

La solución utilizada durante las pruebas fue modificar temporalmente el `PATH` desde Python.

Por tanto, al quedar correctamente configurado `ffmpeg.exe` dentro del entorno,  el proyecto **no depende de mantener una copia manual de FFmpeg dentro de la carpeta del proyecto**.

---

# 7. Carga del modelo

El modelo utilizado actualmente es:

```text
medium
```

Se escogió `medium` como equilibrio entre:

* calidad de transcripción;
* consumo de recursos;
* velocidad;
* capacidad de procesamiento mediante la GPU disponible.

La carga se realiza mediante:

```python
modelo = whisper.load_model("medium", device="cuda")
```

La primera carga del modelo requiere descargar aproximadamente 1,4 GB de datos.

Una vez descargado, el modelo queda disponible para posteriores ejecuciones.

---


# 8. Transcripción en español

Los audios utilizados en el proyecto son principalmente en español.

Por este motivo, la transcripción se realiza especificando:

```python
language="es"
```
---

# 9. Procesamiento de múltiples audios

Una de las mejoras importantes respecto al flujo original en Colab fue pasar de procesar un archivo cada vez a procesar automáticamente todos los archivos disponibles.

El programa puede recorrer los archivos y aplicar el mismo proceso de transcripción a cada uno.

Esto permite añadir nuevos audios a `audios/` sin tener que escribir una nueva instrucción de transcripción para cada archivo.

---

# 10. Privacidad

Una de las ventajas del enfoque local es que los archivos de audio pueden procesarse directamente en el ordenador.

El flujo principal no requiere subir los audios a Google Colab.

El proyecto, por tanto, permite separar el procesamiento de audio de servicios externos.

---


# 11. Estado actual del proyecto

### Implementado

* [x] Entorno Conda independiente.
* [x] Python 3.11.
* [x] VS Code + Jupyter.
* [x] Instalación de Whisper.
* [x] Instalación de PyTorch.
* [x] Detección de GPU AMD.
* [x] Ejecución mediante ROCm.
* [x] Instalación de FFmpeg.
* [x] Transcripción individual.
* [x] Transcripción en español.
* [x] Guardado en `.txt`.
* [x] Carpeta `audios/`.
* [x] Carpeta `transcripciones/`.
* [x] Procesamiento de múltiples audios.
* [x] Medición del tiempo de procesamiento.
* [x] Pruebas con audios de diferente duración.
* [x] Pruebas de rendimiento con GPU.

### Pendiente / posible evolución

* [ ] Filtrado más estricto de extensiones.
* [ ] Indicador de progreso más elaborado.
* [ ] Evitar reprocesamiento automático.
* [ ] Gestión individual de errores.
* [ ] División automática de audios muy largos.
* [ ] Diarización de hablantes.
* [ ] Interfaz gráfica.

---
