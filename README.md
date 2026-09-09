# powerbi

# Conectividad y Transformación de Datos en Power BI

## Preparación de un set de ventas para el análisis

### Objetivo

El objetivo de esta práctica fue preparar un archivo de ventas proveniente de un sistema legacy para su posterior análisis en Power BI. Para ello se utilizó Power Query para realizar tareas de limpieza, transformación y normalización de los datos antes de incorporarlos al modelo.

El archivo original contenía nombres técnicos de columnas, datos de clientes mezclados con información de ventas, registros duplicados, filas vacías y valores nulos.

---

## 1. Carga de los datos

El archivo de ventas fue importado en Power BI Desktop utilizando el conector de Excel.

En lugar de cargar directamente los datos al modelo, se seleccionó la opción **Transform Data** para ingresar al Editor de Power Query y realizar primero las tareas de limpieza y transformación.

La consulta original fue duplicada para separar la información en dos tablas:

* `D_Clientes`: dimensión que contiene la información descriptiva de los clientes.
* `F_Ventas`: tabla de hechos que contiene las transacciones de ventas.

---

## 2. Renombrado de columnas

Los nombres técnicos provenientes del sistema legacy fueron reemplazados por nombres descriptivos utilizando `snake_case`.

### Tabla D_Clientes

| Nombre original | Nombre final   |
| --------------- | -------------- |
| COD_CLI         | id_cliente     |
| NOM_CLI         | nombre_cliente |
| MAIL_CLI        | email          |
| TEL_CLI         | telefono       |
| CIU_CLI         | ciudad         |
| PROV_CLI        | provincia      |
| SEG_CLI         | segmento       |
| FLG_ACT         | activo         |
| F_ALTA_CLI      | fecha_alta     |

### Tabla F_Ventas

| Nombre original | Nombre final       |
| --------------- | ------------------ |
| COD_OP          | id_venta           |
| COD_CLI         | id_cliente         |
| F_VTA           | fecha_venta        |
| COD_PROD        | id_producto        |
| DESC_PROD       | nombre_producto    |
| RUBRO_PROD      | categoria_producto |
| CANT            | cantidad           |
| PU_VTA          | precio_unitario    |
| DTO_PCT         | descuento_pct      |
| TOT_VTA         | total_venta        |
| COD_MON         | moneda             |
| CANAL_VTA       | canal_venta        |

Este cambio permite que las columnas sean más fáciles de interpretar y mantener dentro del modelo de Power BI.

---

## 3. Corrección de tipos de datos

Se revisaron los tipos de datos para asegurar que cada columna pudiera utilizarse correctamente durante el análisis.

Los identificadores alfanuméricos, como `id_cliente`, se configuraron como **Text**, ya que contienen valores como `COD_CLI_005` y no representan cantidades sobre las que deban realizarse operaciones matemáticas.

Las fechas (`fecha_alta` y `fecha_venta`) se configuraron como **Date**, permitiendo posteriormente realizar filtros y análisis temporales.

Las columnas monetarias, como `precio_unitario` y `total_venta`, se configuraron como **Decimal Number**.

La columna `cantidad` se configuró como **Whole Number**.

La columna `descuento_pct` se configuró como **Percentage**, ya que los valores originales estaban expresados como proporciones decimales. Por ejemplo, `0.05` representa un descuento del 5% y `0.20` un descuento del 20%.

Las columnas descriptivas se configuraron como **Text**. Esto incluye `telefono`, ya que, aunque contiene números, representa un dato identificativo y no una cantidad matemática.

La columna `activo` también se configuró como **Text**, dado que sus valores originales son `S` y `N`.

---

## 4. Tratamiento de duplicados, filas vacías y valores nulos

Se eliminaron los registros duplicados utilizando los identificadores correspondientes de cada tabla.

En `D_Clientes`, se utilizó `id_cliente` como criterio para asegurar que cada cliente aparezca una única vez.

En `F_Ventas`, se utilizó `id_venta` como identificador de cada transacción.

También se eliminaron las filas completamente vacías, ya que no contenían información útil para el análisis.

En `D_Clientes` se encontraron valores nulos en las columnas `email` y `telefono`. Estos registros se conservaron porque la ausencia de información de contacto no invalida la existencia del cliente ni sus transacciones. Los valores faltantes fueron identificados como `no_informado`.

En `F_Ventas` se encontraron valores nulos en `descuento_pct` y `total_venta`.

Los valores nulos de `descuento_pct` fueron reemplazados por `0`, interpretándolos como operaciones sin descuento.

En cambio, los registros con `total_venta` nulo fueron eliminados. Se tomó esta decisión porque el total de venta es una variable fundamental para calcular facturación, promedios y otros indicadores. Reemplazar un total desconocido por cero podría generar resultados incorrectos y distorsionar el análisis.

---

## 5. Normalización de la estructura

El archivo original contenía en una misma tabla información descriptiva de clientes e información correspondiente a las transacciones.

Para mejorar la estructura se separaron los datos en dos consultas.

### D_Clientes

Contiene exclusivamente información descriptiva del cliente y posee un único registro por `id_cliente`.

### F_Ventas

Contiene la información correspondiente a cada transacción, como fecha, producto, cantidad, precio, descuento, total, moneda y canal de venta.

La columna `id_cliente` se mantiene en ambas tablas para permitir posteriormente establecer una relación entre ellas:

`D_Clientes[id_cliente] (1) → (*) F_Ventas[id_cliente]`

Esta estructura reduce la repetición innecesaria de información del cliente en cada transacción y facilita la construcción posterior del modelo de datos.

---

## 6. Resultado final

Luego de las transformaciones, se obtuvieron dos tablas limpias y estructuradas para continuar con el análisis en Power BI:

* `D_Clientes`: una fila por cliente y sus atributos descriptivos.
* `F_Ventas`: una fila por transacción con la información necesaria para analizar las ventas.

El proceso permitió corregir nombres técnicos, establecer tipos de datos adecuados, eliminar duplicados y filas vacías, gestionar valores nulos y separar las entidades principales del dataset.

De esta manera, los datos quedan preparados para las siguientes etapas del proceso analítico: modelado, creación de medidas y construcción de visualizaciones en Power BI.
