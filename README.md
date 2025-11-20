## Análisis, Diseño e Implementación de una Solución Analítica para la Gestión de Inventarios

Empresa: Textiles Increíble, S.A. de C.V.\
ERP Fuente: Odoo (PostgreSQL)\
DW: SQL Server (Snapshot Fact)

Autores:
- Diana Carolina Meléndez López
- Luis Alonso Mendoza González
- Erika Yasmín Navas Álvarez

------------------------------------------------------------------------

# 1. Descripción General del Proyecto

Este proyecto desarrolla una solución analítica integral para la gestión
de inventarios de Textiles Increíble, S.A. de C.V., utilizando como
sistema transaccional Odoo y como plataforma analítica un Data Warehouse
en SQL Server.

La solución aborda problemas del negocio textil como diferencias entre
inventarios, falta de trazabilidad en movimientos, disponibilidad real
del stock y ausencia de métricas consolidadas. La arquitectura
implementa un modelo dimensional basado en snapshot diario.

------------------------------------------------------------------------

# 2. Arquitectura General

## Componentes principales

1.  **Droplet 1 -- ERP Odoo**
    -   Contenedores Docker con Odoo y PostgreSQL.
    -   Fuente de datos para los movimientos e inventarios.
    -   Exposición de PostgreSQL para extracción desde SSIS.
2.  **Droplet 2 -- Data Warehouse**
    -   SQL Server Developer Edition.
    -   Data Warehouse dimensional.
    -   Procesos ETL desarrollados en SQL Server Integration Services
        (SSIS).
3.  **Proceso ETL**
    -   Extracción desde PostgreSQL.
    -   Transformación y normalización de datos.
    -   Carga en un modelo dimensional con fact table snapshot.

------------------------------------------------------------------------

# 3. Modelo de Datos Implementado

## 3.1 Modelo Dimensional

### Dimensiones

  Dimensión       Tipo       Descripción
  --------------- ---------- -------------------------------------------
  DimProducto     SCD2       Nombre, categoría, UOM, estado, vigencias
  DimAlmacén      SCD1       Ubicaciones y almacenes
  DimFecha        Estática   Jerarquía día/mes/año
  DimMovimiento   SCD1       Tipo de movimiento según Odoo

### Tabla de Hechos

  ------------------------------------------------------------------------
  Tabla               Tipo             Descripción
  ------------------- ---------------- -----------------------------------
  FT_Inventario       Snapshot diario  Fotografía del inventario por
                                       producto-almacén

  ------------------------------------------------------------------------

------------------------------------------------------------------------

# 4. Fact Table -- Lógica Snapshot

La tabla FT_Inventario captura diariamente el estado del inventario
aunque no existan movimientos ese día. Cada ejecución del ETL genera un
nuevo registro por producto y almacén.

### Columnas principales

-   FechaKey
-   ProductoKey
-   AlmacénKey
-   Entrada_Producto
-   Costo_Producto_Entrante
-   Salida_Producto
-   Costo_Producto_Saliente
-   Inventario_Fisico
-   Inventario_Logico
-   Diferencia_Inventarios
-   Estado_Stock
-   CreatedAt

------------------------------------------------------------------------

# 7. Componentes de Nube Utilizados

## DigitalOcean -- Droplets

### Droplet 1: ERP Odoo

-   Servicios: Odoo + PostgreSQL
-   Conexiones:
    -   Host: 167.99.233.226
    -   Base: odoo-db
    -   Usuario: odoo
    -   Contraseña: odoo
    -   Puerto: 5432

### Droplet 2: Data Warehouse

-   Servicios: SQL Server Developer
-   Conexiones:
    -   Server: 165.227.22.226
    -   Usuario: sa
    -   Contraseña: Sql2024@DW
    -   Base: DWOdooInventarios
    -   AuthMode: SQL
    -   Encrypt: Mandatory
    -   TrustCert: True

------------------------------------------------------------------------

# 8. Parámetros de Conexión del ETL

| Parámetro    | Valor             | Descripción          |
|--------------|-------------------|----------------------|
| DW_Server    | 165.227.22.226    | Servidor SQL Server  |
| DW_User      | sa                | Usuario              |
| DW_Database  | DWOdooInventarios | Base del DW          |
| DW_Password  | Sql2024@DW        | Contraseña del DW    |
| Src_Host     | 167.99.233.226    | Host PostgreSQL      |
| Src_DB       | odoo-db           | Base Odoo            |
| Src_User     | odoo              | Usuario              |
| Src_Password | odoo              | Contraseña Postgres  |
| Src_Port     | 5432              | Puerto               |

------------------------------------------------------------------------

# 9. Métricas generadas

-   Entradas y salidas diarias
-   Existencias físicas y lógicas
-   Costo total de entradas y salidas
-   Diferencias entre inventario físico y lógico
-   Estados de stock por producto

------------------------------------------------------------------------
