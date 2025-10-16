Claro, aquí tienes una propuesta de `README.md` para tu repositorio de GitHub. Este archivo detalla el propósito, funcionamiento y uso de tu algoritmo en un formato claro y profesional.

-----

# Generador de Reportes de Asistencia para Semilleros

Este repositorio contiene un script de Python diseñado para automatizar la creación de reportes de asistencia en formato Excel. A partir de un archivo consolidado de participantes, el script genera un informe individual para cada "semillero" o grupo por período académico, simulando y formateando los datos de asistencia para 12 encuentros.

## ✨ Características Principales

  * **Automatización Completa**: Lee un único archivo Excel y genera múltiples reportes de asistencia, uno por cada grupo.
  * **Generación Dinámica de Fechas**: Calcula automáticamente 12 fechas de encuentro semanales (los viernes) a partir del período académico (ej. `2023-1`).
  * **Simulación de Asistencia**: Rellena los reportes con datos de asistencia (1) e inasistencia (0), distribuyendo un número aleatorio de faltas en cada grupo.
  * **Cálculo de Porcentaje**: Añade una columna final con el porcentaje de asistencia total para cada estudiante.
  * **Formato Profesional en Excel**: Aplica estilos al archivo de salida para una mejor visualización:
      * **Bordes** en todas las celdas.
      * **Colores condicionales**: Verde (`✅`) para asistencias y Rojo (`❌`) para inasistencias.
      * **Formato numérico** para los porcentajes.
  * **Manejo de Errores**: Incluye un control para verificar la existencia del archivo de entrada.

-----

## 📋 Requisitos

Para ejecutar este script, necesitas tener instalado Python 3 y las siguientes bibliotecas:

  * **Pandas**: Para la manipulación y análisis de datos.
  * **NumPy**: Para operaciones numéricas eficientes.
  * **Openpyxl**: Para leer y escribir archivos Excel (`.xlsx`) y aplicarles formato.

Puedes instalar todas las dependencias ejecutando:

```bash
pip install pandas numpy openpyxl
```

-----

## 🚀 Cómo Usar

1.  **Prepara tu archivo de entrada**: Asegúrate de tener un archivo llamado `datos_consolidados.xlsx` en la misma carpeta que el script. La estructura de este archivo es crucial y debe contener al menos las siguientes columnas:

      * `Periodo`: El período académico en formato `AÑO-SEMESTRE` (ej. `2024-1`, `2024-2`).
      * `Semillero`: El nombre del grupo o semillero.
      * Otras columnas con información del estudiante (ej. `ID`, `Nombre Completo`, `Programa`, etc.).

    **Ejemplo de `datos_consolidados.xlsx`:**

| ID | Nombre Completo | Programa | Periodo | Semillero |
| :--- | :--- | :--- | :--- | :--- |
| 101 | Ana García | Ing. de Sistemas | 2024-2 | Python Avanzado |
| 102 | Carlos Ruiz | Ing. Industrial | 2024-2 | Python Avanzado |
| 201 | Laura Mendez | Ing. de Sistemas | 2024-2 | Ciencia de Datos |
| 301 | Pedro Diaz | Ing. Industrial | 2023-1 | Optimización |

2.  **Ejecuta el script**: Abre una terminal en la carpeta del proyecto y ejecuta el siguiente comando:

    ```bash
    python nombre_del_script.py
    ```

    *(Reemplaza `nombre_del_script.py` con el nombre real de tu archivo)*.

3.  **Revisa los resultados**: El script generará un archivo Excel por cada combinación de `Periodo` y `Semillero` en la misma carpeta. Por ejemplo, basado en el archivo anterior, se crearían:

      * `asistencia_2024-2_Python_Avanzado.xlsx`
      * `asistencia_2024-2_Ciencia_de_Datos.xlsx`
      * `asistencia_2023-1_Optimización.xlsx`

-----

## ⚙️ Flujo de Trabajo del Algoritmo

El script sigue un proceso lógico para transformar los datos consolidados en reportes de asistencia individuales y formateados.

### 1\. Carga y Agrupación de Datos

El script comienza cargando el archivo `datos_consolidados.xlsx` en un DataFrame de pandas. Luego, itera sobre cada grupo único definido por la combinación de las columnas `Periodo` y `Semillero`.

```python
df = pd.read_excel("datos_consolidados.xlsx")

for (periodo, semillero), df_grupo in df.groupby(['Periodo', 'Semillero']):
    # ... el resto de la lógica se aplica a cada df_grupo
```

### 2\. Generación de Fechas de Encuentro

Para cada grupo, el algoritmo determina las 12 fechas de los encuentros:

  * **Identifica el mes de inicio**: Marzo (`3`) para el primer semestre (`-1`) y Septiembre (`9`) para el segundo (`-2`).
  * **Encuentra el primer viernes**: Calcula la fecha del primer viernes del mes de inicio correspondiente al año del período.
  * **Genera la secuencia**: A partir de esa fecha inicial, genera una lista de 12 fechas, una por cada 7 días. Estas fechas se usarán como encabezados de las columnas de asistencia.

### 3\. Simulación de Asistencia Aleatoria

Para dar realismo a los datos, el script simula la asistencia de la siguiente manera:

  * Crea una matriz donde inicialmente todos los estudiantes **asistieron a todas las sesiones** (valores en `1`).
  * Determina un **número total de inasistencias** aleatorio (entre 5 y 10) para todo el grupo.
  * **Distribuye las inasistencias** de forma aleatoria entre todos los estudiantes y todas las fechas, cambiando los valores correspondientes a `0`.

### 4\. Consolidación de Datos

La matriz de asistencia generada se convierte en un DataFrame y se une con los datos originales del grupo. Posteriormente, se calcula el porcentaje de asistencia para cada estudiante y se añade como una nueva columna (`% Asistencia`).

```python
# Unir el DataFrame original del grupo con el de asistencias
df_grupo_con_asistencia = pd.concat([df_grupo, attendance_df], axis=1)

# Calcular y agregar la columna de porcentaje de asistencia
df_grupo_con_asistencia['% Asistencia'] = (attendance_df.sum(axis=1) / 12) * 100
```

### 5\. Exportación y Formato del Excel

Finalmente, el DataFrame resultante de cada grupo se guarda en un nuevo archivo Excel. Usando `openpyxl`, se accede a la hoja de cálculo para aplicar los estilos finales que mejoran la legibilidad del reporte.

  * Se crea un nombre de archivo único, por ejemplo: `asistencia_2024-2_Python_Avanzado.xlsx`.
  * Se itera sobre las celdas para aplicar **bordes** a toda la tabla.
  * Se aplica un **relleno de color** verde a las celdas con `1` (asistencia) y rojo a las celdas con `0` (inasistencia).
  * Se ajusta el **formato numérico** de la columna `% Asistencia` para que muestre valores enteros.
