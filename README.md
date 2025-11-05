# Trabajo Práctico Final — Introducción al Marketing Online y los Negocios Digitales
# E-Commerce Data Warehouse & Analytics

Este es un trabajo de la materia 'Intro. al Marketing Online y los Negocios Digitales' del 2º año de la lic. en Ciencia de Datos. 
Este mismo esta orientado a a una práctica real de ingeniería de datos: construcción de un data warehouse en un modelo estrella, procesamiento ETL y preparación de datasets para análisis y dashboarding.

## 🚀 Instrucciones de ejecución

### 1 — Ubicarse en el proyecto
Abrir la terminal y situarse en la carpeta principal del repositorio:
 cd MARKETING

### 2 - Crear y activar el entorno virtual
python3 -m venv .venv
source .venv/bin/activate       # Windows: .venv\Scripts\activate


### 3 — Instalar dependencias necesarias
pip install pandas


### 4 — Ejecutar el ETL para construir el Data Warehouse
python3 build_dw.py

## ⭐ Modelos Estrella del Data Warehouse

### FactOrderItem — Ventas por producto
- **Grano:** Una fila representa una unidad de producto vendida dentro de una transacción comercial, adquirida por un cliente específico en un momento determinado, detallando la cantidad, precio aplicado y descuentos correspondientes, y asociada a un canal y punto de venta. 
- **Medidas:** quantity, unit_price, discount_amount, net_amount *(importe neto por ítem)*
- **Dimensiones:** Customer, Product, Channel, Store, Address, Date  

### FactSalesOrder — Órdenes completas
- **Grano:** Una fila representa una compra completa realizada por un cliente, incluyendo el monto total pagado, impuestos, envío y canal de venta, registrada en una fecha específica.
- **Medidas:** subtotal, tax_amount, shipping_fee, total_amount *(monto final de la orden)*
- **Dimensiones:** Customer, Channel, Store, Address, Date  

### FactNpsResponse — Experiencia del cliente
- **Grano:** Una fila representa una respuesta de NPS enviada por un cliente, con su puntuación y comentario, registrada en una fecha y canal específicos.
- **Medidas:** score *(valor de satisfacción de 0 a 10)*
- **Dimensiones:** Customer, Channel, Date  

### FactWebSession — Navegación digital
- **Grano:** Una fila representa una interacción digital completa de un cliente con la plataforma, iniciada en un momento específico, que registra el canal de origen, el dispositivo utilizado y la duración total de la sesión.
- **Medidas:** duration_seconds *(tiempo total de sesión en segundos)*
- **Dimensiones:** Customer, Date  



## Documentación: Diccionario de datos, claves, dominios y supuestos

### 1) Diccionario de datos (resumen)

#### Tablas de hechos
- **FactOrderItem:** date_id, customer_id, product_id, channel_id, store_id, billing_address_id, shipping_address_id, order_number, quantity, unit_price, discount_amount, net_amount.
- **FactSalesOrder:** date_id, customer_id, channel_id, store_id, billing_address_id, shipping_address_id, order_number, status, currency_code, subtotal, tax_amount, shipping_fee, total_amount.
- **FactNpsResponse:** date_id, customer_id, channel_id, nps_id, score, comment.
- **FactWebSession:** date_id, customer_id, session_id, source, device, duration_seconds.

#### Tablas dimensión
- **DimCustomer:** customer_id, first_name, last_name, email, status, created_at.
- **DimProduct:** product_id, sku, product_name, category_name, parent_category_name, category_level, status, created_at.
- **DimStore:** store_id, store_name.
- **DimChannel:** channel_id, channel_name.
- **DimAddress:** address_id, line1, line2, city, postal_code, country_code, province_id, province_name, created_at.
- **DimDate:** date_id, full_date, year, quarter, month, month_name, day, weekday.

---

### 2) Claves

#### PK (claves primarias)
- DimCustomer: customer_id  
- DimProduct: product_id  
- DimStore: store_id  
- DimChannel: channel_id  
- DimAddress: address_id  
- DimDate: date_id  

> Las tablas hechos usan **claves naturales del OLTP** y definen su unicidad por grano, no por PK artificial.

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

- **IDs:** claves naturales del sistema transaccional.
- **date_id**: `YYYYMMDD` (8 dígitos)
- **Fechas:** `YYYY-MM-DD`
- **Textos:** `varchar`
- **Decimal / importes:** >= 0, dos decimales
- **quantity:** entero >= 0
- **score (NPS):** 0 a 10
- **duration_seconds:** entero >= 0
- **status** (cliente/producto): {`active`, `inactive`}
- **canales** (ejemplos reales): {`online`, `store`, `app`, `ads`, `email`, `social`, `direct`}

> Notas: `weekday` puede ser 0–6 o 1–7 según implementación; mantener consistente.

---

### 4) Supuestos

- Se usan **claves naturales**, no surrogate keys.
- Dimensiones son **conformadas** y compartidas entre todas las facts.
- `date_id` se deriva para normalizar fechas y facilitar joins.
- Duración web calculada como `ended_at - started_at`.
- Envío y facturación se modelan como dos FK separadas a DimAddress.
- Los datos son simulados pero representan un entorno de e-commerce realista.




