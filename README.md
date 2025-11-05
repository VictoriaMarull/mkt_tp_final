# Trabajo Práctico Final  "Introducción al Marketing Online y los Negocios Digitales"
# E-Commerce Data Warehouse & Analytics

Este es un trabajo de la materia 'Intro. al Marketing Online y los Negocios Digitales' del 2º año de la lic. en Ciencia de Datos. 
Este mismo esta orientado a a una práctica real de ingeniería de datos: construcción de un data warehouse en un modelo estrella, procesamiento ETL y preparación de datasets para análisis y dashboarding.



## ⭐ Modelos Estrella del Data Warehouse

### FactOrderItem
- **Grano:** Una fila representa una unidad de producto vendida dentro de una transacción comercial, adquirida por un cliente específico en un momento determinado, detallando la cantidad, precio aplicado y descuentos correspondientes, y asociada a un canal y punto de venta. 
- **Medidas:** quantity, unit_price, discount_amount, net_amount *(importe neto por ítem)*
- **Dimensiones:** Customer, Product, Channel, Store, Address, Date  

### FactSalesOrder 
- **Grano:** Una fila representa una compra completa realizada por un cliente, incluyendo el monto total pagado, impuestos, envío y canal de venta, registrada en una fecha específica.
- **Medidas:** subtotal, tax_amount, shipping_fee, total_amount *(monto final de la orden)*
- **Dimensiones:** Customer, Channel, Store, Address, Date  

### FactNpsResponse 
- **Grano:** Una fila representa una respuesta de NPS enviada por un cliente, con su puntuación y comentario, registrada en una fecha y canal específicos.
- **Medidas:** score *(valor de satisfacción de 0 a 10)*
- **Dimensiones:** Customer, Channel, Date  

### FactWebSession 
- **Grano:** Una fila representa una interacción digital completa de un cliente con la plataforma, iniciada en un momento específico, que registra el canal de origen, el dispositivo utilizado y la duración total de la sesión.
- **Medidas:** duration_seconds *(tiempo total de sesión en segundos)*
- **Dimensiones:** Customer, Date  



## 📚 Documentación: Diccionario de datos, claves, dominios y supuestos

### 1) Diccionario de datos 

#### Tablas de hechos

**FactOrderItem**  
Campos:  
date_id, customer_id, product_id, channel_id, store_id, billing_address_id, shipping_address_id,  
order_number, quantity, unit_price, discount_amount, net_amount  

**FactSalesOrder**  
Campos:  
date_id, customer_id, channel_id, store_id, billing_address_id, shipping_address_id,  
order_number, status, currency_code, subtotal, tax_amount, shipping_fee, total_amount  

**FactNpsResponse**  
Campos:  
date_id, customer_id, channel_id, nps_id, score, comment  

**FactWebSession**  
Campos:  
date_id, customer_id, session_id, source, device, duration_seconds  

---

#### Tablas dimensión

**DimCustomer**  
customer_id *(PK)*, first_name, last_name, email, status, created_at  

**DimProduct**  
product_id *(PK)*, sku, product_name, category_name, parent_category_name, category_level, status, created_at  

**DimStore**  
store_id *(PK)*, store_name  

**DimChannel**  
channel_id *(PK)*, channel_name  

**DimAddress**  
address_id *(PK)*, line1, line2, city, postal_code, country_code, province_id, province_name, created_at  

**DimDate**  
date_id *(PK)*, full_date, year, quarter, month, month_name, day, weekday  

---

### 2) Claves

#### PK (claves primarias)
- DimCustomer: customer_id  
- DimProduct: product_id  
- DimStore: store_id  
- DimChannel: channel_id  
- DimAddress: address_id  
- DimDate: date_id  


#### Grano de las tablas de hechos
- FactOrderItem → **1 fila por producto por orden**
- FactSalesOrder → **1 fila por orden**
- FactNpsResponse → **1 fila por respuesta NPS**
- FactWebSession → **1 fila por sesión**

#### FKs (relaciones de hechos → dimensiones)

##### FactOrderItem
- date_id → DimDate  
- customer_id → DimCustomer  
- product_id → DimProduct  
- channel_id → DimChannel  
- store_id → DimStore  
- billing_address_id → DimAddress  
- shipping_address_id → DimAddress

##### FactSalesOrder
- date_id → DimDate  
- customer_id → DimCustomer  
- channel_id → DimChannel  
- store_id → DimStore  
- billing_address_id → DimAddress  
- shipping_address_id → DimAddress

##### FactNpsResponse
- date_id → DimDate  
- customer_id → DimCustomer  
- channel_id → DimChannel

##### FactWebSession
- date_id → DimDate  
- customer_id → DimCustomer  

---

### 3) Dominios (tipos/valores esperados)

- **IDs:** claves naturales del sistema transaccional  
- **date_id**: `YYYYMMDD` (8 dígitos)  
- **Fechas:** `YYYY-MM-DD`  
- **Textos:** `varchar`  
- **Decimal / importes:** ≥ 0, dos decimales  
- **quantity:** entero ≥ 0  
- **score (NPS):** 0 a 10  
- **duration_seconds:** entero ≥ 0  
- **status** (cliente/producto): {`active`, `inactive`}  
- **canales** (ejemplos): {`online`, `store`, `app`, `ads`, `email`, `social`, `direct`}  

> Nota: `weekday` puede ser 0–6 o 1–7 según implementación; mantener consistente con el ETL.

---

### 4) Supuestos

- Se usan **claves naturales**, no surrogate keys.  
- Dimensiones **conformadas** compartidas entre todas las facts.  
- `date_id` se deriva para normalizar fechas y facilitar joins.  
- Duración web calculada como `ended_at - started_at`.  
- Envío y facturación se modelan como dos FK separadas a DimAddress.  
- Datos sintéticos pero representativos de e-commerce real.




## 🧠 Consultas SQL Clave para Análisis

### 1) Revenue diario
```sql
SELECT 
    d.full_date AS fecha,
    SUM(f.total_amount) AS revenue
FROM FactSalesOrder f
JOIN DimDate d ON d.date_id = f.date_id
GROUP BY d.full_date
ORDER BY d.full_date;
```

### 2) Ticket promedio (AOV)
```sql
SELECT 
    AVG(total_amount) AS average_order_value
FROM FactSalesOrder;
```

### 3) Revenue por canal
```sql
SELECT
    c.channel_name,
    SUM(f.total_amount) AS revenue
FROM FactSalesOrder f
JOIN DimChannel c ON c.channel_id = f.channel_id
GROUP BY c.channel_name
ORDER BY revenue DESC;
```

### 4) Top 10 productos más vendidos
```sql
SELECT
    p.product_name,
    SUM(i.quantity) AS unidades_vendidas
FROM FactOrderItem i
JOIN DimProduct p ON p.product_id = i.product_id
GROUP BY p.product_name
ORDER BY unidades_vendidas DESC
LIMIT 10;
```

### 5) Margen neto por día
```sql
SELECT
    d.full_date,
    SUM(i.net_amount) AS net_revenue
FROM FactOrderItem i
JOIN DimDate d ON d.date_id = i.date_id
GROUP BY d.full_date
ORDER BY d.full_date;
```

### 6) NPS global
```sql
WITH base AS (
    SELECT score FROM FactNpsResponse
),
stats AS (
    SELECT
        COUNT(*) AS total,
        SUM(CASE WHEN score >= 9 THEN 1 END) AS promotores,
        SUM(CASE WHEN score <= 6 THEN 1 END) AS detractores
    FROM base
)
SELECT
    ROUND(100.0 * promotores/total - 100.0 * detractores/total, 2) AS nps;
```

### 7) Sesiones por dispositivo y duración media
```sql
SELECT
    device,
    COUNT(*) AS sesiones,
    ROUND(AVG(duration_seconds), 2) AS avg_duration_seconds
FROM FactWebSession
GROUP BY device
ORDER BY sesiones DESC;
```

### 8) Clientes nuevos por mes
```sql
WITH first_order AS (
    SELECT customer_id, MIN(date_id) AS first_date
    FROM FactSalesOrder
    GROUP BY customer_id
)
SELECT
    SUBSTRING(first_date, 1, 6) AS periodo_yyyy_mm,
    COUNT(*) AS clientes_nuevos
FROM first_order
GROUP BY SUBSTRING(first_date, 1, 6)
ORDER BY periodo_yyyy_mm;
```

### 9) Recompra
```sql
WITH dates AS (
    SELECT DISTINCT customer_id, date_id
    FROM FactSalesOrder
)
SELECT
    COUNT(*) AS clientes_recompra
FROM (
    SELECT customer_id, COUNT(*) AS dias_con_compra
    FROM dates
    GROUP BY customer_id
) t
WHERE dias_con_compra > 1;
```

### 10) Engagement digital
```sql
SELECT
    source,
    device,
    COUNT(*) AS sesiones,
    AVG(duration_seconds) AS avg_duration
FROM FactWebSession
GROUP BY source, device
ORDER BY sesiones DESC;
```


## 🛠️ Buenas prácticas aplicadas

En este proyecto se aplicaron buenas prácticas básicas de ingeniería de datos:

- Uso de entorno virtual (`.venv`) para aislar dependencias
- Instalación de dependencias mediante `pip` (`pandas`)
- Script ETL ejecutable desde terminal (`python3 build_dw.py`)
- Organización del proyecto en carpetas:
  - `raw/` → datos fuente
  - `warehouse/` → tablas generadas del DW
- Construcción de un **modelo estrella** con **dimensiones conformadas**
- Código en Python modular y documentado para reproducibilidad
- Lectura/escritura estructurada de datos (csv → warehouse)


## 🚀 Instrucciones de ejecución

### 1. Ubicarse en el proyecto
Abrir la terminal y situarse en la carpeta principal del repositorio:
 cd MARKETING

### 2. Crear y activar el entorno virtual
python3 -m venv .venv
source .venv/bin/activate       # Windows: .venv\Scripts\activate


### 3. Instalar dependencias necesarias
pip install pandas


### 4. Ejecutar el ETL para construir el Data Warehouse
python3 build_dw.py
