---
layout: default
---

# Introducción de la ingenieria de sistemas 

[Dashboard](https://lookerstudio.google.com/s/kiKmy1KN4Lc).

Hecho por: Alexy De Jesus Salcedo Tete

# 1. ¿Cuál es el producto más vendido en una ubicación geográfica específica?

Para este punto lo primero que se hizo fue delimitar la zona, en este caso fue el departamento. 



```ruby
WITH clientes_Atlantico AS (
SELECT Departamento, numero AS Codigo_Cliente 
FROM `Introduccion.clientes`
WHERE Departamento= 'Atlántico'
),

compras_Atlantico AS (
SELECT clin.Codigo_Cliente, producto AS Codigo_producto   
FROM clientes_Atlantico clin
INNER JOIN `Introduccion.compras` comp ON clin.Codigo_Cliente= comp.cliente

)

SELECT COUNT(*) AS cantidad_compras, produ.Codigo, Producto
FROM compras_Atlantico comp
INNER JOIN `Introduccion.productos` produ ON produ.Codigo = comp.Codigo_producto
GROUP BY Producto, produ.Codigo
ORDER BY cantidad_compras desc
```
Usamos dos subconsultas, la primera fue usada para escoger a los clientes del atlántico, otra para ver las compras de los clientes del atlántico por medio de un INNER JOIN.

Y en la consulta final se usa un COUNT para obtener el número de compras de los productos y se organiza de mayor a menor, el primero será el más vendido.



# 2. ¿Cuántos clientes han comprado productos de un rango de precios específico?

En primer lugar delimitamos un rango de 2000 a 8000 

```ruby
WITH
  personas AS (
  SELECT
    Codigo,
    precio
  FROM
    `Introduccion.productos`
  WHERE
    precio > 2000
    AND precio <8000 ),
  Comprados AS (
  SELECT
    cliente,
    producto
  FROM
    `Introduccion.compras`
  INNER JOIN
    personas
  ON
    codigo = producto ),
  Agrupados AS (
  SELECT
    CLIENTE
  FROM
    Comprados
  GROUP BY
    cliente )
SELECT
  COUNT(*) AS Compra_Rango
FROM
  Agrupados
```
Se hacen 3 subconsultas:

En la primera subconsulta se buscan los productos dentro del intervalo de esos precios, en la segunda se buscan las compras dentro de ese intervalo, en la tercera se agregan los datos de los clientes 

En la consulta final se usa el COUNT para ver contar todas los clientes que hicieron una compra en el intervalo



# 3. ¿Cuáles son los clientes más frecuentes en realizar compras y cuánto han gastado en total?

```ruby
WITH Productos_Compras As (
SELECT p.*, c .Precio
FROM `Introduccion.productos` c
INNER JOIN `Introduccion.compras` p  ON c.Codigo=p.producto

),

clientes_Compras as (
SELECT Numero, Nombre_1 AS Primer_Nombre, Nombre_2 AS Segundo_Nombre, Apellidos, Count (*) AS Las_Compras, Sum (precio) AS gastos 
FROM `Introduccion.clientes` 
INNER JOIN Productos_Compras ON cliente=Numero
Group by Numero, Primer_Nombre,Segundo_Nombre, Apellidos
)

SELECT *
FROM clientes_Compras
order by clientes_Compras.Las_Compras DESC

```
Acá solo se hace una  subconsulta, en las cuales se obtienen las compras. Posteriormente se hace una consulta en donde se agrupan los datos para mostrar los clientes más frecuentes y cuanto han gastado 

# 4. ¿Cuál es el producto más vendido en cada ubicación geográfica?

En esta pregunta se usa el departamento como delimitación geográfica En esta pregunta se usa el departamento como delimitación geográfica 


```ruby
WITH Agru_ProduCity AS (

SELECT Departamento,producto, COUNT (producto) AS Compras_Por_Producto
FROM `Introduccion.compras`
INNER JOIN `Introduccion.clientes` ON Cliente= Numero
GROUP BY Departamento, producto

),

top_produ AS (
SELECT MAX(Compras_Por_Producto) AS Top_Producto, Departamento
FROM Agru_ProduCity
GROUP BY Departamento
),


agru As (
  SELECT top_produ.Departamento, producto AS Clave_Producto, Compras_por_producto
  FROM top_produ
  INNER JOIN Agru_ProduCity ON top_produ.Top_Producto= Agru_ProduCity.Compras_Por_Producto AND top_produ.Departamento = Agru_ProduCity.Departamento 
)

SELECT Departamento, Producto, Compras_Por_Producto,Clave_Producto
FROM `Introduccion.productos`
INNER JOIN agru ON Codigo=Clave_Producto
ORDER BY  Compras_Por_Producto DESC 
 
```
Se usan 3 subconsultas para este, una donde vemos una lista de compras por departamento, otra donde se ve el producto top ventas en dicho departamento y la última subconsulta se usa para obtener cual es el código. Con la consulta general se dan los datos al final.


# 5. ¿Cuál es el producto más vendido en cada ubicación geográfica?

Primero hacemos una consulta para comprobar que productos no han sido comprados y restarlos con los comprados, así nos vemos los cuales son 11 y se le resta a los 21 que son los totales, nos da 10. 

```ruby
WITH pc AS(
SELECT DISTINCT pro.Producto, com.cliente
FROM `Introduccion.productos` pro
LEFT JOIN `Introduccion.compras` com ON pro.codigo=com.producto
)


SELECT Count (*) AS Productos_Sin_Comprar
FROM pc
WHERE cliente IS NULL

 
```
Entonces tomamos a los clientes que han comprado todos los productos que se han vendido, usando en el WHERE en 10 después de usar las subconsultas para tener solo los clientes que han comprado esa cantidad de productos 


```ruby
WITH compras_de_Clientes AS (
SELECT DISTINCT cliente, Codigo, COUNT(p.producto) AS Productos_Comprados
FROM `Introduccion.productos` p
INNER JOIN `Introduccion.compras` c ON  c.producto = Codigo
GROUP BY Cliente, codigo 
ORDER BY cliente 

), 

clipro AS (
SELECT cliente, COUNT(codigo) AS Comprados
FROM compras_de_Clientes
GROUP BY cliente
)

SELECT DISTINCT cliente, Nombre_1 AS Primer_Nombre, Nombre_2 AS Segundo_Nombre, Apellidos, c.Comprados
FROM `Introduccion.clientes`
INNER JOIN clipro c on cliente = Numero 
WHERE c.Comprados= 10
ORDER BY cliente 
```
#

# REFERENCIAS DE IMAGENES DASHBOARD

https://www.freepik.es/vector-gratis/descuentos-venta-temporada-presenta-compra-visita-boutiques-compras-lujo-cupones-promocionales-reduccion-precio-ofertas-especiales-vacaciones-ilustracion-metafora-concepto-aislado-vector_12083346.htm#query=bolsa%20de%20compras&position=1&from_view=keyword&track=ais
