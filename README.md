# 🔎 OCR → Identificación IA → Web Scraping → EDA → Feature Engineering

Plataforma en Streamlit que permite subir una imagen, extraer su texto con OCR,
identificar de qué se trata usando un modelo de **Groq**, buscar información
relacionada en la web (sin necesidad de APIs de pago) y analizar esos datos con
un EDA y variables de feature engineering.

---

## 📋 Requisitos previos

### 1. Python
Python 3.9 o superior.

### 2. Tesseract OCR (motor de OCR del sistema operativo)
El paquete `pytesseract` es solo un "puente" en Python: necesita el programa
**Tesseract** instalado en tu computador.

- **Ubuntu / Debian**
  ```bash
  sudo apt-get install tesseract-ocr tesseract-ocr-spa
  ```
- **macOS** (con Homebrew)
  ```bash
  brew install tesseract tesseract-lang
  ```
- **Windows**
  Descarga el instalador desde:
  https://github.com/UB-Mannheim/tesseract/wiki
  y agrega la ruta de instalación (ej. `C:\Program Files\Tesseract-OCR`) a la
  variable de entorno `PATH`.

Si este paso no se hace, el botón "Ejecutar OCR" mostrará un error indicando
que no se encontró el binario `tesseract`.

### 3. Una API Key de Groq
Necesitas una cuenta gratuita en [console.groq.com](https://console.groq.com)
y generar una API Key allí. Esta clave **no se guarda en ningún lado**: solo
la ingresas en la barra lateral de la app y se usa en memoria durante tu
sesión.

---

## ⚙️ Instalación

1. Descarga/clona los archivos `app.py` y `requirements.txt` en una misma
   carpeta.
2. (Opcional pero recomendado) crea un entorno virtual:
   ```bash
   python3 -m venv venv
   source venv/bin/activate      # En Windows: venv\Scripts\activate
   ```
3. Instala las dependencias de Python:
   ```bash
   pip install -r requirements.txt
   ```

---

## ▶️ Cómo ejecutar la plataforma

Desde la carpeta donde están `app.py` y `requirements.txt`:

```bash
streamlit run app.py
```

Esto abrirá automáticamente una pestaña en tu navegador (normalmente en
`http://localhost:8501`).

---

## 🖥️ Cómo usar la plataforma (paso a paso)

La app está organizada en 4 pestañas, que se usan **en orden**.

### 🔧 Antes de empezar: barra lateral (sidebar)
En la barra lateral izquierda debes configurar:

| Campo | Qué hacer |
|---|---|
| **Groq API Key** | Pega tu API Key de Groq (queda oculta, tipo contraseña). |
| **Modelo de Groq** | Elige el modelo con el que quieres identificar la imagen (por defecto `llama-3.3-70b-versatile`, buena opción general). |
| **Idioma OCR** | Deja `spa+eng` si tus imágenes tienen texto en español y/o inglés. Puedes cambiarlo si necesitas otro idioma soportado por Tesseract. |
| **Resultados por consulta** | Cuántos resultados quieres traer por cada búsqueda al hacer scraping (por defecto 6). |

Si ves mensajes en rojo en la barra lateral avisando que falta `tesseract` o
el paquete `groq`, revisa la sección de **Requisitos previos**.

---

### 1️⃣ Pestaña "OCR + Identificación"

1. Haz clic en **"Sube la imagen de la figura/objeto"** y selecciona tu
   archivo (`.png`, `.jpg`, `.jpeg`, `.bmp`, `.webp`).
2. (Opcional) escribe un **contexto adicional** si ya sabes algo de la imagen,
   por ejemplo: *"es una pieza mecánica"*, *"es una etiqueta de producto"*,
   *"es una planta"*. Esto ayuda a la IA a identificar mejor.
3. Presiona **"🔍 Ejecutar OCR"**. El texto que la imagen contiene aparecerá
   en el cuadro de la derecha.
4. Revisa el texto extraído: como todo OCR, puede tener errores. **Puedes
   editarlo manualmente** antes de continuar.
5. Presiona **"🤖 Identificar con IA (Groq)"**. La app le pedirá al modelo de
   Groq que identifique de qué se trata a partir del texto (y del contexto,
   si lo diste). Verás:
   - La identificación general.
   - La categoría.
   - Una descripción breve.
   - Palabras clave.
   - Consultas de búsqueda sugeridas (estas se usarán automáticamente en la
     siguiente pestaña).

> 💡 Si no subiste ninguna imagen o el OCR no encontró texto, igual puedes
> escribir directamente en el cuadro de texto y/o usar el campo de contexto
> para identificar algo.

---

### 2️⃣ Pestaña "Web Scraping"

1. Verás una caja de texto con las **consultas de búsqueda**, precargada
   automáticamente con las sugerencias que dio la IA en el paso anterior.
   Puedes editarlas, borrar alguna o agregar las tuyas (una consulta por
   línea).
2. Presiona **"🌐 Ejecutar Web Scraping"**.
3. La app buscará en:
   - **DuckDuckGo** (buscador libre, sin necesidad de API key).
   - **Wikipedia** (API pública y gratuita).
4. Verás una tabla con los resultados (título, snippet/resumen, URL, fuente,
   dominio).
5. Puedes descargar estos resultados en CSV con el botón **"⬇️ Descargar
   CSV"**.

---

### 3️⃣ Pestaña "EDA" (Análisis Exploratorio de Datos)

Una vez tengas resultados de scraping, esta pestaña se llena automáticamente
con:

- Métricas generales (total de resultados, dominios únicos, consultas
  distintas, longitud media de los textos).
- Gráfico de resultados por consulta.
- Gráfico de los dominios/fuentes más frecuentes.
- Histograma de la longitud de los textos encontrados.
- Nube de palabras con los términos más repetidos.
- Ranking de las 20 palabras clave más frecuentes.

No necesitas hacer nada más que revisar los gráficos; se generan solos con
los datos scrapeados.

---

### 4️⃣ Pestaña "Feature Engineering"

1. Presiona **"🛠️ Generar features"**. Esto crea variables nuevas a partir de
   los textos scrapeados, por ejemplo:
   - Longitud del snippet y del título.
   - Número de palabras.
   - Cantidad de dígitos.
   - Si el texto menciona un precio (`$`, `USD`, `EUR`, etc.).
   - Valores numéricos detectados (máximo, cantidad de valores).
2. Podrás ver:
   - La tabla completa con las nuevas columnas.
   - Estadística descriptiva (promedios, mínimos, máximos, etc.).
   - Una matriz de correlación entre las variables numéricas.
   - Un gráfico de proporción de resultados que mencionan precio.
3. Puedes descargar esta tabla enriquecida en CSV.
4. **Feature adicional con IA (opcional):** puedes elegir cuántos textos
   analizar (para no gastar de más en la API de Groq) y presionar el botón
   correspondiente para que el modelo clasifique el tono de cada snippet como
   *positivo*, *negativo* o *neutral*, agregando esa columna a la tabla.

---

## 🔁 Flujo recomendado (resumen rápido)

```
Subir imagen → Ejecutar OCR → (editar texto si hace falta) →
Identificar con IA → Revisar/ajustar consultas → Ejecutar Web Scraping →
Ver EDA → Generar Features → (opcional) Feature de tono con IA →
Descargar CSVs
```

---

## ❓ Problemas comunes

| Problema | Solución |
|---|---|
| "No se detectó el binario 'tesseract'" | Instala Tesseract en tu sistema operativo (ver Requisitos previos). No basta con `pip install pytesseract`. |
| El botón de identificar está deshabilitado | Verifica que hayas escrito tu API Key de Groq en la barra lateral. |
| El scraping trae pocos o ningún resultado | Prueba con consultas más generales o distintas; a veces DuckDuckGo limita resultados si detecta demasiadas peticiones seguidas. |
| Error al llamar a Groq | Revisa que la API Key sea correcta y que el modelo elegido esté disponible en tu cuenta de Groq. |
| El OCR extrae texto con errores | Es normal en imágenes de baja calidad; puedes corregir el texto manualmente antes de identificar. |

---

## 🔒 Privacidad

- La API Key de Groq solo se usa en memoria durante tu sesión de Streamlit;
  no se almacena en ningún archivo ni se envía a otro lugar que no sea la
  API oficial de Groq.
- El scraping se hace únicamente contra DuckDuckGo y Wikipedia, ambas fuentes
  de acceso público y gratuito.
