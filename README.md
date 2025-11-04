# Trabajo Práctico Final — Introducción al Marketing Online y los Negocios Digitales
# E-Commerce Data Warehouse & Analytics

Este es un trabajo de la materia 'Intro. al Marketing Online y los Negocios Digitales' del 2º año de la lic. en Ciencia de Datos. 
Este mismo esta orientado a a una práctica real de ingeniería de datos: construcción de un data warehouse en un modelo estrella, procesamiento ETL y preparación de datasets para análisis y dashboarding.

## Documentación: Diccionario de datos, claves, dominios y supuestos

### 1) Diccionario de datos (resumen)
- **FactOrderItem**: date_id, customer_id, product_id, channel_id, store_id, billing_address_id, shipping_address_id, order_number, quantity, unit_price, discount_amount, net_amount.
- **DimCustomer**: customer_id, first_name, last_name, email, status, created_at.
- **DimProduct**: product_id, sku, product_name, category_name, parent_category_name, category_level, status, created_at.
- **DimStore**: store_id, store_name.
- **DimChannel**: channel_id, channel_name.
- **DimAddress**: address_id, line1, line2, city, postal_code, province_name, country_code, province_id, created_at.
- **DimDate**: date_id, full_date, year, quarter, month, month_name, day, weekday.

### 2) Claves
- **PK (claves primarias)**
  - DimCustomer: customer_id
  - DimProduct: product_id
  - DimStore: store_id
  - DimChannel: channel_id
  - DimAddress: address_id
  - DimDate: date_id
  - FactOrderItem: *(sin PK propia; grano definido abajo)*
- **FK (claves foráneas de la fact)**
  - FactOrderItem.date_id → DimDate.date_id
  - FactOrderItem.customer_id → DimCustomer.customer_id
  - FactOrderItem.product_id → DimProduct.product_id
  - FactOrderItem.channel_id → DimChannel.channel_id
  - FactOrderItem.store_id → DimStore.store_id
  - FactOrderItem.billing_address_id → DimAddress.address_id
  - FactOrderItem.shipping_address_id → DimAddress.address_id

### 3) Dominios (tipos/valores esperados)
- **IDs**: enteros positivos (claves naturales del OLTP).
- **Fechas**: `YYYY-MM-DD`; **date_id**: `YYYYMMDD` (8 dígitos).
- **Textos**: `varchar` (sin longitud fija para el TP).
- **quantity**: entero ≥ 0.
- **unit_price, discount_amount, net_amount**: decimal ≥ 0 (2 decimales).
- **status** (cliente/producto): {`active`, `inactive`}.
- **channel_name**: {`online`, `store`, `app`, `marketplace`} (ajustable a datos reales).
- **weekday**: 1–7 (1=Lunes … 7=Domingo) *o* 0–6 según implementación; mantener consistente con el ETL.

### 4) Supuestos
- **Grano de la fact**: COMPLETAR
- **Producto + categoría** se consolidan en **DimProduct** (con `parent_category_name` y `category_level`).
- **Provincia** se integra en **DimAddress** (no hay DimProvince separada).
- **DimDate** se genera a partir de las fechas presentes en `sales_order` (no calendario completo).
- **Integridad referencial**: se asume válida en los datos fuente.
- **Valores faltantes**: nulos o defaults razonables (p.ej., `discount_amount=0`; `parent_category_name=''`).
- **net_amount** se valida o calcula como `quantity*unit_price - discount_amount` cuando no está en el origen.


