# Proyecto PC0: Instrucciones

Este proyecto tiene como objetivo configurar un entorno de trabajo en Python, descargar un dataset, realizar consultas y almacenar datos, y realizar un análisis exploratorio básico. Sigue las instrucciones detalladas a continuación.

---

## 1. Creación del entorno Python 3.10 llamado `MBIT`

### Opción 1: Usando Conda
```bash
conda create --name MBIT python=3.10 -y
conda activate MBIT
```

### Opción 2: Usando Pipenv
```bash
pip install pipenv
pipenv --python 3.10
pipenv shell
```

### Opción 3: Usando Virtualenv
```bash
python3 -m venv MBIT
source MBIT/bin/activate  # En macOS/Linux
MBIT\Scripts\activate  # En Windows
```

---

## 2. Instalación de dependencias

Asegúrate de que el entorno esté activado y ejecuta:
```bash
pip install -r requirements.txt
```
Esto instalará todos los paquetes necesarios para el proyecto.

---

## 3. Creación de carpetas

En el directorio raíz del proyecto, crea las siguientes carpetas:
```bash
mkdir src notebooks
```
La estructura del proyecto será la siguiente:

```markdown
Proyecto/
├── src/
├── notebooks/
├── requirements.txt
├── README.md
```

---

## 4. Crear el archivo `utils.py` en la carpeta `src`

Crea un archivo llamado `utils.py` dentro de la carpeta `src/` con una función capaz de descargar el [dataset de hoteles de Kaggle](https://www.kaggle.com/datasets/raj713335/tbo-hotels-dataset) y crear la carpeta `data` al mismo nivel que `src`y `notebooks`.

```markdown
Proyecto/
├── src/
├── notebooks/
├── data/
├── requirements.txt
├── README.md
```

En el desplegable tienes parte de la respuesta, y la otra en este link de [Stack Overflow](https://stackoverflow.com/questions/49386920/download-kaggle-dataset-by-using-python). 

Si te bloqueas en este paso, siempre puedes descargarlo manualmente y guardarlo en la carpeta `/data`

<details>
  <summary>Mostrar solución</summary>
  
```python
import pandas as pd
import os

def get_dataset():
    url = "https://www.kaggle.com/datasets/raj713335/tbo-hotels-dataset"
    dataset_path = "data/hotels.csv"

    if not os.path.exists("data"):
        os.makedirs("data")

    # Descarga del dataset (simulación, ya que Kaggle requiere autenticación)
    print(f"Por favor, descarga manualmente el dataset de {url} y colócalo en {dataset_path}")

    df = pd.read_csv(dataset_path)
    return df
```

</details>

---

## 5. Crear el notebook `01_PC0_companion` en la carpeta `notebooks`

1. Crea un archivo llamado `01_PC0_companion.ipynb` en la carpeta `notebooks/`.
2. El contenido del notebook debe incluir los siguientes pasos:

### Paso 1: Importar la función `get_dataset`

<details>
  <summary>Mostrar solución</summary>
  

```python
from src.utils import get_dataset
```

</details>

### Paso 2: Crear la carpeta `data` en el nivel raíz del proyecto

<details>
  <summary>Mostrar solución</summary>
  
```python
import os

if not os.path.exists("data"):
    os.makedirs("data")
```

</details>

### Paso 3: Descargar y cargar el dataset en memoria

<details>
  <summary>Mostrar solución</summary>

```python
df = get_dataset()
```

</details>

### Paso 4: Guardar el dataframe en dos formatos

1. Como un archivo CSV llamado `raw.csv` en la carpeta `data`:
```python
df.to_csv("data/raw.csv", index=False)
```
2. Como una base de datos SQLite llamada `db` en la carpeta `data`:
```python
import sqlite3

conn = sqlite3.connect("data/db")
df.to_sql("hotels", conn, if_exists="replace", index=False)
conn.close()
```

### Paso 5: Realizar consultas
1. Usando SQL y Pandas para contar los hoteles por categoría (`HotelRatings`) en cada país:
```python
conn = sqlite3.connect("data/db")
query = """
    SELECT HotelRatings, Country, COUNT(*) AS HotelCount 
    FROM hotels 
    GROUP BY HotelRatings, Country
"""
sql_results = pd.read_sql_query(query, conn)
print(sql_results)
conn.close()
```
2. Usando únicamente Pandas:
```python
pandas_results = df.groupby(["HotelRatings", "Country"]).size().reset_index(name="HotelCount")
print(pandas_results)
```

### Paso 6: Comparar resultados
```python
assert sql_results.equals(pandas_results), "Los resultados no coinciden."
print("Los resultados coinciden.")
```

### Paso 7: Análisis exploratorio de datos (EDA)
Incluye gráficos o resúmenes descriptivos que permitan entender mejor el dataset:
```python
import matplotlib.pyplot as plt

df["HotelRatings"].value_counts().plot(kind="bar")
plt.title("Distribución de HotelRatings")
plt.show()

print(df.describe())
```

---

## Notas adicionales
- Asegúrate de seguir las instrucciones detalladas en cada paso.
- Si tienes problemas para descargar el dataset directamente desde Kaggle, consulta las guías de autenticación de Kaggle para configurar tu entorno.

---

¡Buena suerte con el proyecto!
