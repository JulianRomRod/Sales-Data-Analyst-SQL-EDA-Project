# Sales-Data-Analyst-SQL-EDA-Project

## Acerca del proyecto
Este proyecto analiza los datos de ventas de **PedalPro Cycles**, una empresa ficticia dedicada a la venta de bicicletas, componentes, ropa y accesorios de ciclismo a nivel internacional. El objetivo es identificar los productos y categorías con mejor desempeño, analizar la evolución de las ventas a lo largo del tiempo y comprender el comportamiento de los distintos segmentos de clientes. El propósito final es aportar información que permita optimizar las estrategias comerciales de la empresa.

## Objetivos del Proyecto
El objetivo principal es extraer conocimiento a partir de los datos de ventas de PedalPro Cycles, explorando los distintos factores (producto, cliente, tiempo y geografía) que influyen en las ventas de la compañía.

## Acerca de los Datos
Los datos utilizados corresponden a un modelo en esquema estrella compuesto por una tabla de hechos y dos tablas de dimensiones, con un total de **60,398 líneas de venta**, **18,484 clientes** y **295 productos**, cubriendo el periodo comprendido entre **diciembre de 2010 y enero de 2014**.

### `fact_sales`
Contiene 9 columnas y 60,398 registros, cada uno correspondiente a una línea de un pedido de venta:

| Columna        | Descripción                                                | Tipo de dato   |
|----------------|-------------------------------------------------------------|----------------|
| order_number   | Número de pedido de la venta                                | VARCHAR(20)    |
| product_key    | Clave del producto vendido (relaciona con dim_products)     | INT            |
| customer_key   | Clave del cliente que realizó la compra (relaciona con dim_customers) | INT     |
| order_date     | Fecha en la que se realizó el pedido                         | DATE           |
| shipping_date  | Fecha en la que el pedido fue enviado                        | DATE           |
| due_date       | Fecha de entrega prevista del pedido                          | DATE           |
| sales_amount   | Importe total de la venta de la línea de pedido               | DECIMAL(10,2)  |
| quantity       | Cantidad de unidades vendidas                                  | INT            |
| price          | Precio unitario del producto en el momento de la venta         | DECIMAL(10,2)  |

### `dim_customers`
Contiene 10 columnas y 18,484 registros:

| Columna         | Descripción                                              | Tipo de dato   |
|-----------------|-----------------------------------------------------------|----------------|
| customer_key    | Clave sustituta del cliente (clave primaria)               | INT            |
| customer_id     | Identificador de negocio del cliente                        | INT            |
| customer_number | Código alfanumérico del cliente                             | VARCHAR(15)    |
| first_name      | Nombre del cliente                                          | VARCHAR(50)    |
| last_name       | Apellido del cliente                                         | VARCHAR(50)    |
| country         | País de residencia del cliente                               | VARCHAR(50)    |
| marital_status  | Estado civil del cliente (Married / Single)                  | VARCHAR(20)    |
| gender          | Género del cliente                                            | VARCHAR(10)    |
| birthdate       | Fecha de nacimiento del cliente                                | DATE           |
| create_date     | Fecha de alta del cliente en el sistema                        | DATE           |

### `dim_products`
Contiene 11 columnas y 295 registros:

| Columna       | Descripción                                                    | Tipo de dato   |
|---------------|-------------------------------------------------------------------|----------------|
| product_key   | Clave sustituta del producto (clave primaria)                     | INT            |
| product_id    | Identificador de negocio del producto                              | INT            |
| product_number| Código alfanumérico del producto                                  | VARCHAR(20)    |
| product_name  | Nombre descriptivo del producto                                    | VARCHAR(100)   |
| category_id   | Identificador de la categoría del producto                         | VARCHAR(10)    |
| category      | Categoría del producto (Bikes, Components, Clothing, Accessories)  | VARCHAR(30)    |
| subcategory   | Subcategoría del producto (37 subcategorías distintas)             | VARCHAR(50)    |
| maintenance   | Indica si el producto requiere mantenimiento (Yes/No)               | VARCHAR(5)     |
| cost          | Coste de fabricación/adquisición del producto                      | DECIMAL(10,2)  |
| product_line  | Línea de producto (Mountain, Road, Touring, Other Sales)             | VARCHAR(20)    |
| start_date    | Fecha desde la que el producto está disponible a la venta            | DATE           |

## Lista de Análisis

1. **Análisis de Productos**

> Analizar el catálogo de productos para conocer las categorías, subcategorías y líneas de producto con mejor desempeño, e identificar oportunidades de mejora en aquellas con menor rendimiento.

2. **Análisis de Ventas**

> Estudiar la tendencia de las ventas a lo largo del tiempo (año, mes, día) para evaluar la eficacia de las estrategias comerciales aplicadas y detectar posibles estacionalidades.

3. **Análisis de Clientes**

> Identificar los distintos segmentos de clientes (país, género, estado civil, edad), entender sus patrones de compra y evaluar la rentabilidad asociada a cada segmento.

## Enfoque Utilizado de Análisis Exploratorio de Datos (EDA)

**1. Preparación y entendimiento de la base de datos**

En esta fase inicial se examinan los datos para detectar valores nulos, duplicados o inconsistentes, y se definen las estrategias necesarias para tratarlos.
- Construcción del modelo relacional (esquema estrella) a partir de las tres tablas.
- Creación de las tablas e importación de los datos.
- Revisión de los esquemas principales de tablas y columnas.
- Revisión de valores nulos o marcados como `n/a` y definición de reglas de tratamiento.

**2. Exploración de dimensiones principales y rango temporal**

En esta fase se examinan aquellas dimensiones principales que más tarde servirán como guía para agrupar y segmentar datos, así como se examinan aquellas columnas que nos aportan información sobre fechas o edades con el objetivo de entender la temporalidad de los datos.
- Estudio de las dimensiones "country", "gender" o "marital_status" pertenecientes a "dim_customers", utilizando DISTINCT para observar sus valores únicos.
- Estudio de las dimensiones "category", "subcategory", "product_line" y "product_name" pertenecientes a "dim_products", utilizando DISTINCT para observar sus valores únicos.
- Estudio de las columnas "order_date" y "edad", entendiendo así la edad de los diferentes segmentos de clientes y el momento en el que ocurrió la primera y la última venta.

**3. Análisis inicial de datos cuantitativos**

En esta fase entramos de manera más profunda en aquellas dimensiones numéricas de valor que determinan el rendimiento de la empresa. Hablamos principalmente de datos como ventas, cantidades vendidas, costes, beneficio asegurado, precios máximos, mínimos y medios, cantidad de productos vendidos, número de categorías...
- Entendimiento del total de ventas y cantidad de ordenes realizadas.
- Estudio de la escala de precios empresarial (precio máximo, mínimo y medio).
- Número de productos y clientes.

**4. Preparación al análisis descriptivo**

En esta última fase del análisis exploratorio se produce el comienzo del cruzado de datos, estableciendo relación entre dimensiones como el país, el género o la categoría de producto con columnas de valores numéricos como las ventas, costes o precios.
- Análisis cruzados: ventas según país, clientes según género, ventas según producto, precio medio según categoría...
- Introducción al análisis descriptivo estableciendo rankings de dimensiones como clientes, productos o países según variables métricas.

## Preguntas de Negocio a Responder
### 1. Exploración de Dimensiones
1. ¿Qué países distintos existen en la tabla de clientes (`dim_customers`)?
2. ¿Qué combinaciones de categoría, subcategoría y línea de producto (`product_line`) existen en la tabla de productos (`dim_products`)?

### 2. Exploración de Fechas
1. ¿Cuál es la fecha del primer y del último pedido registrado, y cuántos años de ventas abarca el conjunto de datos (`fact_sales`)?
2. ¿Cuál es la fecha de nacimiento del cliente más joven y del más viejo, y qué edad tienen a día de hoy (`dim_customers`)?

### 3. Exploración de Medidas
1. ¿Cuál es el importe total vendido (`sales_amount`) y cuántos artículos (`quantity`) se han vendido en total?
2. ¿Cuál es el precio máximo, mínimo y medio de los productos vendidos?
3. ¿Cuántos productos distintos (`product_name`) hay en el catálogo?
4. ¿Cuántos clientes distintos (`customer_id`) están registrados?
5. ¿Cómo se resumen todas estas medidas (cantidad vendida, ítems, precio máximo/mínimo/medio, nº de productos y nº de clientes) en un único informe exploratorio?

### 4. Análisis de Magnitudes
1. ¿Cuántos clientes hay por país, ordenados de mayor a menor?
2. ¿Cuántos clientes hay por género, ordenados de mayor a menor?
3. ¿Cuántos productos hay por categoría (`category`), ordenados de mayor a menor?
4. ¿Cuál es el coste medio (`cost`) de los productos por categoría?
5. ¿Cuál es el total de ventas (`sales_amount`) por categoría de producto?
6. ¿Cuál es el total de ventas por país?
7. ¿Cuál es el total de artículos vendidos (`quantity`) por país?
8. ¿Cuál es el total de ventas por género del cliente?

### 5. Análisis de Ranking (Top N)
1. ¿Cuáles son los 5 productos que más ventas han generado?
2. ¿Cuáles son los 5 productos que menos ventas han generado?
3. ¿Cuáles son los 10 clientes (por nombre) que más ventas han generado?
4. ¿Cuáles son los 3 clientes (por nombre) con el menor número de pedidos (`order_number`) distintos realizados?
 
