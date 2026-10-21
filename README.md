# ​smart-inventory-replenishment-ml


                    ESTRUCTURA Y DICCIONARIO DE DATOS


1. TABLA: dim_products (Catálogo y Parámetros de Inventario)
Contiene los metadatos y parámetros financieros/logísticos calculados para cada 
familia de productos.

* product_id      (TEXT)   : Identificador único de la familia de productos.
* product_name    (TEXT)   : Nombre descriptivo de la familia de productos.
* category        (TEXT)   : Categoría general del producto (ej. 'Retail').
* unit_cost       (FLOAT)  : Costo unitario de compra estimado ($).
* lead_time_days  (INTEGER): Tiempo de entrega del proveedor en días (3, 5, 7 o 10 días).
* selling_price   (FLOAT)  : Precio de venta al público (calculado con un margen del +30%).


2. TABLA: dim_stores (Información de Tiendas)
Contiene los datos geográficos y la clasificación de los puntos de venta.

* store_nbr (INTEGER): Número identificador único de la tienda (1 a 54).
* city      (TEXT)   : Ciudad de Ecuador donde está ubicada la tienda.
* state     (TEXT)   : Provincia/Estado donde se ubica la tienda.
* type      (TEXT)   : Tipo/Tipo de formato de tienda (A, B, C, D, E).
* cluster   (INTEGER): Grupo o 'cluster' de tiendas con comportamiento de ventas similar.


3. TABLA: fact_oil_prices (Precios del Petróleo)
Registra la cotización del crudo WTI (West Texas Intermediate), indicador socioeconómico clave.

* date       (DATE) : Fecha del registro.
* dcoilwtico (FLOAT): Precio diario del barril de petróleo en USD (con imputación lineal para días faltantes).


4. TABLA: fact_daily_sales (Historial de Ventas Diarias)
--------------------------------------------------------------------------------
Tabla transaccional principal con los registros diarios por tienda y producto.

* id          (INTEGER): Identificador único del registro de venta.
* date        (DATE)   : Fecha de la transacción.
* store_nbr   (INTEGER): Clave foránea referenciando a 'dim_stores' (tienda).
* family      (TEXT)   : Clave foránea referenciando a 'dim_products' (familia de producto).
* sales       (FLOAT)  : Volumen total de ventas en unidades para esa combinación en el día.
* onpromotion (INTEGER): Número de artículos de esa familia que estaban en promoción ese día.


5. TABLA/CARACTERÍSTICAS: Time Series Features (Ingeniería de Variables)

Variables derivadas creadas dinámicamente para el entrenamiento del modelo de LightGBM.

* dayofweek        (INTEGER): Día de la semana (0 = Lunes, 6 = Domingo).
* month            (INTEGER): Mes del año (1 a 12).
* is_weekend       (INTEGER): Flag binario (1 si es fin de semana, 0 en otro caso).
* sales_lag_X      (FLOAT)  : Venta real histórica desfasada X días (lags: 7, 14, 21, 28, 30).
* rolling_mean_7   (FLOAT)  : Promedio móvil de ventas de los últimos 7 días.
* rolling_std_7    (FLOAT)  : Desviación estándar móvil de ventas de los últimos 7 días.
* rolling_mean_30  (FLOAT)  : Promedio móvil de ventas de los últimos 30 días.


6. ARCHIVO DE SALIDA: inventory_policy.csv (Políticas de Inventario)
Resultados generados tras la inferencia del modelo y aplicación de fórmulas de Supply Chain.

* family             (TEXT) : Familia de producto evaluada.
* daily_demand_pred  (FLOAT): Demanda promedio diaria predicha por el modelo (unidades/día).
* safety_stock       (FLOAT): Inventario de seguridad calculado para un nivel de servicio del 95% (unidades).
* reorder_point (ROP)(FLOAT): Punto de reorden que activa la orden de compra al proveedor (unidades).
================================================================================
