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
---

## 🚀 Instrucciones de ejecución

### 1. Clonar el repositorio
Primero, clonar este repositorio en tu máquina local:
```bash
git clone https://github.com/VictoriaMarull/mkt_tp_final.git
```

### 2. Ubicarse en el proyecto
Abrir la terminal y situarse en la carpeta principal del repositorio:
```bash
cd MARKETING
```

### 3. Crear y activar el entorno virtual
```bash
python3 -m venv .venv
source .venv/bin/activate       # En Windows: .venv\Scripts\activate
```

### 4. Instalar dependencias necesarias
```bash
pip install pandas
```

### 5. Ejecutar el ETL principal
Ejecutar el script que construye las dimensiones y tablas de hechos:
```bash
python3 build_dw.py
```
> ✅ Esto generará todos los archivos CSV correspondientes al **Data Warehouse** dentro de la carpeta `warehouse/`.

---


### 🗃️ Modelo OLTP de origen

Partimos del siguiente modelo **OLTP** (base transaccional), el cual representaba el flujo operativo del e-commerce: clientes, pedidos, productos, envíos, pagos, sesiones web, entre otros.  
Sobre esta estructura se aplicó un proceso **ETL (Extract, Transform, Load)** desarrollado en **Python**, que permitió:

- Limpiar, normalizar y transformar los datos de las tablas transaccionales.  
- Integrar y relacionar las distintas entidades.  
- Generar **múltiples esquemas estrella (star schemas)** orientados a análisis específicos dentro del Data Warehouse (ventas, envíos, pagos, satisfacción del cliente, etc.).

<p align="center">
  <img src="2DER.png" alt="Modelo OLTP original" width="850"/>
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
| **Grano** | Una fila representa una compra completa realizada por un cliente, incluyendo el monto total pagado, impuestos, envío y canal de venta, registrada en una fecha específica. |
| **Dimensiones** | DimDate, DimCustomer, DimChannel, DimStore, DimAddress |
| **Métricas principales** | Subtotal, Impuestos (tax_amount), Gastos de envío (shipping_fee), Total de pedido (total_amount) |
| **Objetivo analítico** | Analizar la performance de ventas, pedidos completados, medios de venta y localización. |

---

### 🧾 FactOrderItem — Detalle de Ítems Vendidos

| Elemento | Descripción |
|-----------|-------------|
| **Tabla de Hechos** | FactOrderItem |
| **Grano** | Una fila representa una unidad de producto vendida dentro de una transacción comercial, adquirida por un cliente específico en un momento determinado, detallando la cantidad, precio aplicado y descuentos correspondientes, y asociada a un canal y punto de venta. |
| **Dimensiones** | DimDate, DimCustomer, DimChannel, DimStore, DimProduct, DimAddress |
| **Métricas principales** | Cantidad (quantity), Precio unitario (unit_price), Descuento (discount_amount), Importe neto (net_amount) |
| **Objetivo analítico** | Evaluar el rendimiento por producto, categoría, cliente y canal de venta. |

---

### 💳 FactPayment — Pagos y Métodos de Cobro

| Elemento | Descripción |
|-----------|-------------|
| **Tabla de Hechos** | FactPayment |
| **Grano** | Cada fila representa el evento de pago correspondiente a un pedido efectuado por un cliente, registrado en una fecha determinada. |
| **Dimensiones** | DimDate, DimCustomer, DimChannel, DimStore, DimAddress |
| **Métricas principales** | Monto del pago (amount) |
| **Atributos clave** | Método de pago (method), Estado (status), Moneda (currency_code) |
| **Objetivo analítico** | Analizar medios de pago, conversiones efectivas y comportamiento financiero del cliente. |

---

### 🚚 FactShipment — Envíos y Logística

| Elemento | Descripción |
|-----------|-------------|
| **Tabla de Hechos** | FactShipment |
| **Grano** | Una fila representa el evento de envío de un pedido realizado por un cliente, registrado en una fecha determinada. |
| **Dimensiones** | DimDate, DimCustomer, DimChannel, DimStore, DimAddress |
| **Métricas principales** | Costo de envío (shipping_fee), Días de entrega (delivery_days) |
| **Atributos clave** | Transportista (carrier), Estado (status), Número de seguimiento (tracking_number) |
| **Objetivo analítico** | Medir la eficiencia logística, los tiempos de entrega y costos asociados. |

---

### ⭐ FactNpsResponse — Satisfacción del Cliente (NPS)

| Elemento | Descripción |
|-----------|-------------|
| **Tabla de Hechos** | FactNpsResponse |
| **Grano** | Una fila representa una respuesta de NPS enviada por un cliente, con su puntuación y comentario, registrada en una fecha y canal específicos. |
| **Dimensiones** | DimDate, DimCustomer, DimChannel |
| **Métricas principales** | Puntuación NPS (score) |
| **Atributos clave** | Comentario del cliente (comment) |
| **Objetivo analítico** | Medir la satisfacción y lealtad del cliente en función del canal de interacción. |

---

### 🌐 FactWebSession — Sesiones Web y Comportamiento Digital

| Elemento | Descripción |
|-----------|-------------|
| **Tabla de Hechos** | FactWebSession |
| **Grano** | Una fila representa una interacción digital completa de un cliente con la plataforma, iniciada en un momento específico, que registra el canal de origen, el dispositivo utilizado y la duración total de la sesión. |
| **Dimensiones** | DimDate, DimCustomer |
| **Métricas principales** | Duración de la sesión (duration_seconds) |
| **Atributos clave** | Fuente (source), Dispositivo (device) |
| **Objetivo analítico** | Analizar el comportamiento del usuario en el sitio y su relación con las ventas y engagement. |

---

Cada modelo se diseñó con la intención de ofrecer una **visión analítica integral** del negocio, facilitando la trazabilidad desde la interacción digital hasta la conversión y la satisfacción del cliente.

---

---

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
---

## 📊 Próximos pasos

En una próxima etapa, se desarrollará un **dashboard interactivo en Power BI** que integrará todas las tablas de hechos del Data Warehouse.  
Este dashboard permitirá visualizar **métricas clave y KPIs** derivados de los distintos modelos estrella, tales como:

> 💡 El objetivo es brindar una visión 360° del negocio, unificando información operativa, comercial y de experiencia del cliente.

---