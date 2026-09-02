## Exploración de la Base de Datos
 
**Objetos de la base de datos**
```sql
SELECT * FROM INFORMATION_SCHEMA.TABLES
```
 
**Exploración de columnas de cada tabla**
```sql
SELECT * FROM INFORMATION_SCHEMA.COLUMNS
WHERE TABLE_NAME = 'dim_customers'
```
```sql
SELECT * FROM INFORMATION_SCHEMA.COLUMNS
WHERE TABLE_NAME = 'dim_products'
```
```sql
SELECT * FROM INFORMATION_SCHEMA.COLUMNS
WHERE TABLE_NAME = 'fact_sales'
```
---
 
## 1. Exploración de Dimensiones
 
**1. ¿Qué países distintos existen en la tabla de clientes (`dim_customers`)?**
```sql
SELECT
	DISTINCT(country)
FROM dim_customers
```
 
**2. ¿Qué combinaciones de categoría, subcategoría y línea de producto (`product_line`) existen en la tabla de productos (`dim_products`)?**
```sql
SELECT DISTINCT Category, subcategory, product_line
FROM dim_products
ORDER BY 1,2,3
```
 
---
 
## 2. Exploración de Fechas
 
**1. ¿Cuál es la fecha del primer y del último pedido registrado, y cuántos años de ventas abarca el conjunto de datos (`fact_sales`)?**
```sql
SELECT
	Primera_Orden,
	Última_Orden,
	DATEDIFF(YEAR, Primera_Orden, Última_Orden) as Años_Comprendidos
FROM(
SELECT
	MIN(order_date) as Primera_Orden,
	MAX(order_date) Última_Orden
FROM fact_sales
)tiempo
```
 
**2. ¿Cuál es la fecha de nacimiento del cliente más joven y del más viejo, y qué edad tienen a día de hoy (`dim_customers`)?**
```sql
SELECT
	MIN(Birthdate) as Viejo,
	DATEDIFF(year, MIN(Birthdate), GETDATE()) as EDADviejo,
	MAX(Birthdate) as Joven,
	DATEDIFF(year, MAX(Birthdate), GETDATE()) as EDADjoven
FROM dim_customers
```
 
---
 
## 3. Exploración de Medidas
 
**1. ¿Cuál es el importe total vendido (`sales_amount`), cuántos artículos (`quantity`) se han vendido en total, y cuál es el precio máximo, mínimo y medio de los productos vendidos?**
```sql
SELECT
	SUM(Sales_amount) as Cantidad_vendida,
	SUM(quantity) as Items_vendidos,
	MAX(Price) as Precio_Máximo,
	MIN(Price) as Precio_Mínimo,
	AVG(Price) as Precio_Medio
FROM fact_sales
```
 
**2. ¿Cuántos productos distintos (`product_name`) hay en el catálogo?**
```sql
SELECT
	COUNT(DISTINCT product_name) as Número_Productos
FROM dim_products
```
 
**3. ¿Cuántos clientes distintos (`customer_id`) están registrados?**
```sql
SELECT
	COUNT(DISTINCT customer_id) as Número_Clientes
FROM dim_customers
```
 
**4. ¿Cómo se resumen todas estas medidas (cantidad vendida, ítems, precio máximo/mínimo/medio, nº de productos y nº de clientes) en un único informe exploratorio?**
```sql
SELECT
	'Cantidad Vendida' as Medida, SUM(Sales_amount) as Valor
FROM fact_sales
UNION ALL
SELECT
	'Cantidad Items' as Medida, SUM(quantity) as Valor
FROM fact_sales
UNION ALL
SELECT
	'Precio Máximo' as Medida, MAX(Price)  as Valor
FROM fact_sales
UNION ALL
SELECT
	'Precio Mínimo' as Medida, MIN(Price)  as Valor
FROM fact_sales
UNION ALL
SELECT
	'Precio Medio' as Medida, AVG(Price)  as Valor
FROM fact_sales
UNION ALL
SELECT
	'Número Productos' as Medida,
	COUNT(DISTINCT product_name) as Valor
FROM dim_products
UNION ALL
SELECT
	'Número Clientes' as Medida,
	COUNT(DISTINCT customer_id) as Valor
FROM dim_customers
```
 
---
 
## 4. Análisis de Magnitudes
 
```sql
CREATE VIEW Gender_Country_Category_ProductLine as(
SELECT
	s.*,
	c.country,
	c.gender,
	p.category,
	p.product_line
FROM fact_sales as s
LEFT JOIN dim_customers as c
ON s.customer_key = c.customer_key
LEFT JOIN dim_products as p
ON s.product_key = p.product_key
)
```
 
**1. ¿Cuántos clientes hay por país, ordenados de mayor a menor?**
```sql
SELECT
	country,
	COUNT(*) as Total_Clientes
FROM dim_customers
GROUP BY country
ORDER BY Total_Clientes DESC
```
 
**2. ¿Cuántos clientes hay por género, ordenados de mayor a menor?**
```sql
SELECT
	gender,
	COUNT(*) as Total_Clientes
FROM dim_customers
GROUP BY gender
ORDER BY Total_Clientes DESC
```
 
**3. ¿Cuántos productos hay por categoría (`category`), ordenados de mayor a menor?**
```sql
SELECT
	category,
	COUNT(*) as Total_Productos
FROM dim_products
GROUP BY category
ORDER BY Total_Productos DESC
```
 
**4. ¿Cuál es el coste medio (`cost`) de los productos por categoría?**
```sql
SELECT
	category,
	AVG(cost) as Coste_Medio
FROM dim_products
GROUP BY category
ORDER BY Coste_Medio DESC
```
 
**5. ¿Cuál es el total de ventas (`sales_amount`) por categoría de producto?**
```sql
SELECT
	Category,
	SUM(sales_amount) as Total_Ventas
FROM Gender_Country_Category_ProductLine
GROUP BY Category
ORDER BY Total_Ventas DESC
```
 
**6. ¿Cuál es el total de ventas por país?**
```sql
SELECT
	country,
	SUM(sales_amount) as Total_Ventas
FROM Gender_Country_Category_ProductLine
GROUP BY country
ORDER BY Total_Ventas DESC
```
 
**7. ¿Cuál es el total de artículos vendidos (`quantity`) por país?**
```sql
SELECT
	country,
	SUM(quantity) as Total_Items
FROM Gender_Country_Category_ProductLine
GROUP BY country
ORDER BY Total_Items DESC
```
 
**8. ¿Cuál es el total de ventas por género del cliente?**
```sql
SELECT
	gender,
	SUM(sales_amount) as Total_Ventas
FROM Gender_Country_Category_ProductLine
GROUP BY gender
ORDER BY Total_Ventas DESC
```
 
---
 
## 5. Análisis de Ranking (Top N)
 
**1. ¿Cuáles son los 5 productos que más ventas han generado?**
```sql
SELECT TOP 5
	SUM(sales_amount) as Total_Ventas,
	p.product_name,
	ROW_NUMBER() OVER(ORDER BY SUM(sales_amount) DESC) as Mejores_Productos
FROM fact_sales as s
LEFT JOIN dim_products as p
ON s.product_key = p.product_key
GROUP BY product_name
ORDER BY Total_Ventas DESC
```
 
**2. ¿Cuáles son los 5 productos que menos ventas han generado?**
```sql
SELECT TOP 5
	SUM(sales_amount) as Total_Ventas,
	p.product_name,
	ROW_NUMBER() OVER(ORDER BY SUM(sales_amount) DESC) as Peores_Productos
FROM fact_sales as s
LEFT JOIN dim_products as p
ON s.product_key = p.product_key
GROUP BY product_name
ORDER BY Total_Ventas ASC
```
 
**3. ¿Cuáles son los 10 clientes (por nombre) que más ventas han generado?**
```sql
SELECT TOP 10
	SUM(s.Sales_amount) as Total_Ventas,
	c.first_name
FROM fact_sales as s
LEFT JOIN dim_customers as c
ON s.customer_key = c.customer_key
GROUP BY c.first_name
ORDER BY Total_Ventas DESC
```
 
**4. ¿Cuáles son los 3 clientes (por nombre) con el menor número de pedidos (`order_number`) distintos realizados?**
```sql
SELECT TOP 3
	COUNT(DISTINCT order_number) as Total_Órdenes,
	c.first_name
FROM fact_sales as s
LEFT JOIN dim_customers as c
ON s.customer_key = c.customer_key
GROUP BY c.first_name
ORDER BY Total_Órdenes ASC
```
 
