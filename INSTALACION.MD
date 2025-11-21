# Guía de Instalación del proyecto

Este documento describe el proceso de instalación, configuración y
ejecución del entorno necesario para operar la solución analítica basada
en SSIS, SQL Server y Odoo en DigitalOcean.

Repositorio del proyecto:
https://github.com/Luis318/DWGrupo5-odoo/tree/main-v2

# 1. Requisitos Previos

## 1.1. Sistemas utilizados

-   Windows 10/11 para desarrollo ETL con SSIS.
-   Servidor Linux (Ubuntu) en DigitalOcean para Odoo.
-   Servidor Linux (Ubuntu) en DigitalOcean para SQL Server Developer
    Edition.

## 1.2. Herramientas necesarias en Windows

-   Visual Studio 2022
-   Extensión SSIS (SQL Server Integration Services Projects)
-   SQL Server Management Studio (SSMS)
-   Npgsql (conector PostgreSQL para .NET)
-   Git

------------------------------------------------------------------------

# 2. Instalación del ERP Odoo en DigitalOcean

Odoo y PostgreSQL se instalaron utilizando Docker Compose en un droplet
separado.
El entorno contiene: - Contenedor de Odoo (versión 16/17 según
despliegue) - Contenedor de PostgreSQL - Persistencia por volúmenes

Los servicios expuestos: - Host PostgreSQL: 167.99.233.226
- Puerto: 5432
- Base de datos: odoo-db
- Usuario: odoo
- Contraseña: odoo

------------------------------------------------------------------------

# 3. Instalación del Data Warehouse en SQL Server

El Data Warehouse se aloja en un droplet independiente ejecutando SQL
Server Developer Edition.
Conexión para el proyecto:

-   Servidor SQL: 165.227.22.226
-   Usuario: sa
-   Contraseña: Sql2024@DW
-   Base: DWOdooInventarios
-   Encryption: Mandatory
-   TrustCert: True

No se requiere creación manual preliminar, ya que la base está
provisionada en la nube.

------------------------------------------------------------------------

# 4. Instalación de Visual Studio y SSIS

## 4.1. Visual Studio 2022

1.  Descargar desde: https://visualstudio.microsoft.com/es/
2.  Instalar con la carga de trabajo Desarrollo de escritorio .NET.

## 4.2. Instalar Integration Services (SSIS)

1.  Abrir Visual Studio Installer.
2.  Instalar la extensión: SQL Server Integration Services Projects.

## 4.3. Instalar Npgsql

Requerido para conectar SSIS con PostgreSQL.

1.  Descargar desde el repositorio de la solución el instalador Npgsql.msi

------------------------------------------------------------------------

# 5. Clonación del Repositorio

    git clone https://github.com/Luis318/DWGrupo5-odoo.git
    cd DWGrupo5-odoo
    git checkout main-v2

------------------------------------------------------------------------

# 6. Configuración del Proyecto SSIS

Abrir la solución en Visual Studio.

Las conexiones del paquete utilizan parámetros centralizados:

## 6.1 Parámetros del Data Warehouse

  Parámetro     Valor
  ------------- -------------------
  DW_Server     165.227.22.226
  DW_User       sa
  DW_Database   DWOdooInventarios
  DW_Password   Sql2024@DW

## 6.2 Parámetros del ERP Odoo (PostgreSQL)

  Parámetro      Valor
  -------------- ----------------
  Src_Host       167.99.233.226
  Src_DB         odoo-db
  Src_User       odoo
  Src_Password   odoo
  Src_Port       5432

Verificar que las conexiones OLE DB y .NET Odbc/Npgsql estén
configuradas correctamente.

------------------------------------------------------------------------

# 7. Ejecución del ETL

## 7.1 Validar conexiones

Desde la pestaña Connection Managers: - Probar conexión a PostgreSQL. -
Probar conexión a SQL Server.

## 7.2 Ejecutar paquete principal

    Orquestador.dtsx

Realiza: 1. Extracción desde Odoo. 2. Transformación de movimientos. 3.
Cálculo de snapshot diario. 4. Carga de dimensiones y tabla de hechos.