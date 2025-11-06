## 🧠 Trabajo Práctico Final  
### *“Introducción al Marketing Online y los Negocios Digitales”*  
### **E-Commerce Data Warehouse & Analytics**

Este proyecto corresponde a la materia **Introducción al Marketing Online y los Negocios Digitales**, del **segundo año de la Licenciatura en Ciencia de Datos**.  
El trabajo aborda una práctica aplicada de **ingeniería de datos**, que incluye el **diseño y construcción de un Data Warehouse** bajo un **modelo estrella**, el **proceso ETL completo** y la **preparación de datasets analíticos** para su posterior exploración y visualización en dashboards.

---

### 🧩 Tecnologías utilizadas
- 🐍 **Python 3.12+** Lenguaje principal del proyecto.  
- 🧮 **pandas** Transformaciones, limpieza y modelado ETL.  
- 🌿 **Git / GitHub** Control de versiones y gestión del proyecto.  
- 🧱 **Virtual Environment (venv)** Aislamiento del entorno de desarrollo.  
- 📊 **Power BI** Visualización y desarrollo del dashboard final.  

---

### 🗃️ Modelo OLTP de origen

Partimos del siguiente modelo **OLTP** (base transaccional), el cual representaba el flujo operativo del e-commerce: clientes, pedidos, productos, envíos, pagos, sesiones web, entre otros.  
Sobre esta estructura se aplicó un proceso **ETL (Extract, Transform, Load)** desarrollado en **Python**, que permitió:

- Limpiar, normalizar y transformar los datos de las tablas transaccionales.  
- Integrar y relacionar las distintas entidades.  
- Generar **múltiples esquemas estrella (star schemas)** orientados a análisis específicos dentro del Data Warehouse (ventas, envíos, pagos, satisfacción del cliente, etc.).

<p align="center">
  <img src="DER.png" alt="Modelo OLTP original" width="850"/>
</p>

---

## 🌟 Modelos estrella del Data Warehouse

Tras el proceso **ETL**, el modelo OLTP fue transformado en distintos **schemas estrella**, cada uno diseñado para responder a un aspecto específico del negocio.  
Estos modelos conforman el nuevo **Data Warehouse del e-commerce**, optimizado para análisis y reporting.

---

### 📦 FactSalesOrder — Ventas y Pedidos

| Elemento | Descripción |
|-----------|-------------|
| **Tabla de Hechos** | FactSalesOrder |
| **Grano** | Una fila por pedido realizado |
| **Dimensiones** | DimDate, DimCustomer, DimChannel, DimStore, DimAddress |
| **Métricas principales** | Subtotal, Impuestos (tax_amount), Gastos de envío (shipping_fee), Total de pedido (total_amount) |
| **Objetivo analítico** | Analizar la performance de ventas, pedidos completados, medios de venta y localización. |

---

### 🧾 FactOrderItem — Detalle de Ítems Vendidos

| Elemento | Descripción |
|-----------|-------------|
| **Tabla de Hechos** | FactOrderItem |
| **Grano** | Una fila por producto vendido dentro de un pedido |
| **Dimensiones** | DimDate, DimCustomer, DimChannel, DimStore, DimProduct, DimAddress |
| **Métricas principales** | Cantidad (quantity), Precio unitario (unit_price), Descuento (discount_amount), Importe neto (net_amount) |
| **Objetivo analítico** | Evaluar el rendimiento por producto, categoría, cliente y canal de venta. |

---

### 💳 FactPayment — Pagos y Métodos de Cobro

| Elemento | Descripción |
|-----------|-------------|
| **Tabla de Hechos** | FactPayment |
| **Grano** | Una fila por transacción de pago registrada |
| **Dimensiones** | DimDate, DimCustomer, DimChannel, DimStore, DimAddress |
| **Métricas principales** | Monto del pago (amount) |
| **Atributos clave** | Método de pago (method), Estado (status), Moneda (currency_code) |
| **Objetivo analítico** | Analizar medios de pago, conversiones efectivas y comportamiento financiero del cliente. |

---

### 🚚 FactShipment — Envíos y Logística

| Elemento | Descripción |
|-----------|-------------|
| **Tabla de Hechos** | FactShipment |
| **Grano** | Una fila por envío efectuado |
| **Dimensiones** | DimDate, DimCustomer, DimChannel, DimStore, DimAddress |
| **Métricas principales** | Costo de envío (shipping_fee), Días de entrega (delivery_days) |
| **Atributos clave** | Transportista (carrier), Estado (status), Número de seguimiento (tracking_number) |
| **Objetivo analítico** | Medir la eficiencia logística, los tiempos de entrega y costos asociados. |

---

### ⭐ FactNpsResponse — Satisfacción del Cliente (NPS)

| Elemento | Descripción |
|-----------|-------------|
| **Tabla de Hechos** | FactNpsResponse |
| **Grano** | Una fila por respuesta al cuestionario NPS |
| **Dimensiones** | DimDate, DimCustomer, DimChannel |
| **Métricas principales** | Puntuación NPS (score) |
| **Atributos clave** | Comentario del cliente (comment) |
| **Objetivo analítico** | Medir la satisfacción y lealtad del cliente en función del canal de interacción. |

---

### 🌐 FactWebSession — Sesiones Web y Comportamiento Digital

| Elemento | Descripción |
|-----------|-------------|
| **Tabla de Hechos** | FactWebSession |
| **Grano** | Una fila por sesión web registrada |
| **Dimensiones** | DimDate, DimCustomer |
| **Métricas principales** | Duración de la sesión (duration_seconds) |
| **Atributos clave** | Fuente (source), Dispositivo (device) |
| **Objetivo analítico** | Analizar el comportamiento del usuario en el sitio y su relación con las ventas y engagement. |

---

Cada modelo se diseñó con la intención de ofrecer una **visión analítica integral** del negocio, facilitando la trazabilidad desde la interacción digital hasta la conversión y la satisfacción del cliente.

---

