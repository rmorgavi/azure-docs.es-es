---
title: "Administración de bases de datos en Azure SQL Data Warehouse | Microsoft Docs"
description: "Información general de administración de bases de datos de Almacenamiento de datos SQL Incluye herramientas de administración, DWU y rendimiento de escalado horizontal, solución de problemas de rendimiento de las consultas, el establecimiento de directivas de seguridad y la restauración una base de datos de daños en los datos o de un apagón regional."
services: sql-data-warehouse
documentationcenter: NA
author: barbkess
manager: jhubbard
editor: 
ms.assetid: 8576fdb3-71fe-4b3b-a4e0-5e8a7f148acf
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.date: 10/31/2016
ms.author: barbkess
translationtype: Human Translation
ms.sourcegitcommit: 5a101aa78dbac4f1a0edb7f414b44c14db392652
ms.openlocfilehash: f421f2e333b0725fe4561997d44ed61b20b3f1d6


---
# <a name="manage-databases-in-azure-sql-data-warehouse"></a>Administración de base datos en Almacenamiento de datos SQL de Azure
Almacenamiento de datos SQL automatiza muchos aspectos de la administración de las bases de datos. Por ejemplo, para escalar el rendimiento solo necesita ajustar y pagar por el nivel adecuado de recursos de proceso y, a continuación, dejar que Almacenamiento de datos SQL haga todo el trabajo de escalado horizontal y escalado de nuevo.

Sin duda deseará supervisar la carga de trabajo para identificar sus necesidades de rendimiento, así como solucionar problemas de consultas de larga ejecución. También debe realizar algunas tareas de seguridad para administrar los permisos de los usuarios e inicios de sesión.

Esta información general describe estos aspectos de Almacenamiento de datos SQL.

* Herramientas de administración
* Escalado de proceso
* Pausa y reanudación
* Prácticas recomendadas de rendimiento
* Supervisión de consulta
* Seguridad
* Copia de seguridad y restauración

## <a name="management-tools"></a>Herramientas de administración
Puede usar una variedad de herramientas para administrar bases de datos en Almacenamiento de datos SQL. A medida que administra bases de datos, desarrollará las preferencias de la herramienta para cada tipo de tarea que necesite realizar.

### <a name="azure-portal"></a>Portal de Azure
[Portal de Azure][Portal de Azure] es un portal basado en web donde se pueden crear, actualizar y eliminar bases de datos, así como supervisar recursos de bases de datos. Es una herramienta muy útil si se está empezando a trabajar con Azure, se administran pocas bases de datos de almacenamiento de datos o hay que hacer algo rápidamente.

Para empezar a usar Azure Portal, consulte [Creación de SQL Data Warehouse (Azure Portal)][Creación de SQL Data Warehouse (Azure Portal)].

### <a name="sql-server-data-tools-in-visual-studio"></a>SQL Server Data Tools en Visual Studio
[SQL Server Data Tools][SQL Server Data Tools] (SSDT) de Visual Studio le permite conectar, administrar y desarrollar las bases de datos. Si es un programador familiarizado con Visual Studio o con otros entornos de desarrollo integrado (IDE), pruebe a usar SSDT en Visual Studio.

SSDT incluye el Explorador de objetos de SQL Server, que permite visualizar, conectar y ejecutar scripts en bases de datos de Almacenamiento de datos SQL. Para conectarse rápidamente a Almacenamiento de datos SQL, simplemente haga clic en el botón **Abrir en Visual Studio** de la barra de comandos al visualizar los detalles de la base de datos en el Portal de Azure clásico.  

Para una introducción a SSDT en Visual Studio, consulte [Consultas en Almacenamiento de datos SQL de Azure con Visual Studio][Consultas en Almacenamiento de datos SQL de Azure con Visual Studio].

### <a name="command-line-tools"></a>Herramientas de línea de comandos
Las herramientas de línea de comandos son ideales para automatizar las cargas de trabajo.  PowerShell y sqlcmd son dos excelentes formas de automatizar los procesos.  Se recomienda el uso de estas herramientas para administrar un gran número de servidores lógicos e implementar cambios en los recursos en un entorno de producción, ya que las tareas necesarias se pueden realizar mediante scripts y automatizar.

### <a name="dynamic-management-views"></a>Vistas de administración dinámica
Las DMV son las herramientas esenciales de la administración de Almacenamiento de datos SQL. Casi toda la información que se presenta en el portal se basa en DMV. Para ver una lista de las DMV de SQL Data Warehouse, consulte [Vistas del sistema de Almacenamiento de datos SQL][Vistas del sistema de Almacenamiento de datos SQL].

Para empezar, consulte [Conexión y consultas con sqlcmd][Conexión y consultas con sqlcmd], y [Creación de una base de datos (PowerShell)][Creación de una base de datos (PowerShell)].

## <a name="scale-compute"></a>Escalado de proceso
En Almacenamiento de datos SQL, puede escalar horizontalmente o de nuevo de forma rápida el rendimiento mediante el aumento o disminución de los recursos de proceso de la CPU, la memoria y el ancho de banda de E/S. Para escalar el rendimiento, todo lo que necesita hacer es ajustar el número de unidades de almacenamiento de datos (DWU) que asigna Almacenamiento de datos SQL. Almacenamiento de datos SQL realiza el cambio y controla todos los cambios subyacentes al hardware o software rápidamente.

Para más información sobre el escalado de DWU, consulte [Scale performance][Scale performance](Rendimiento del escalado).

## <a name="pause-and-resume"></a>Pausa y reanudación
Para ahorrar costos, puede pausar y reanudar recursos de proceso a petición. Por ejemplo, si no va a usar la base de datos durante la noche y los fines de semana, puede pausarla durante esas horas y reanudarla durante el día. No se le cobrará por DWU mientras la base de datos se encuentre en pausa.

Para más información, consulte [Pausa del proceso][Pausa del proceso] y [Reanudación del proceso][Reanudación del proceso].

## <a name="performance-best-practices"></a>Prácticas recomendadas de rendimiento
Cuando comience a trabajar con una nueva tecnología, descubrir las sugerencias y trucos que funcionan mejor directamente desde el inicio puede ahorrarle mucho tiempo.  Encontrará procedimientos recomendados a lo largo de muchos de nuestros temas.

Para ver un resumen de las consideraciones más importantes al desarrollar la carga de trabajo, consulte [Prácticas recomendadas de Almacenamiento de datos SQL][Prácticas recomendadas de Almacenamiento de datos SQL].

## <a name="query-monitoring"></a>Supervisión de consulta
A veces se ejecuta una consulta durante demasiado tiempo, pero no está seguro de cuál es la culpable. Almacenamiento de datos de SQL tiene vistas de administración dinámica (DMV) que puede utilizar para averiguar qué consulta está tardando demasiado.

Para buscar las consultas de larga ejecución, consulte [Supervisión de la carga de trabajo mediante DMV][Supervisión de la carga de trabajo mediante DMV].

## <a name="security"></a>Seguridad
Para mantener un sistema seguro, debe estar en la alerta y protegerse contra cualquier tipo de acceso no autorizado. Un sistema de seguridad debe asegurarse de que las reglas de firewall están en su lugar para que solo puedan conectarse las direcciones IP autorizadas. Necesita la autenticación correcta de las credenciales de usuario. Después de que un usuario se conecte a la base de datos, el usuario solo debe tener permisos para realizar un número mínimo de acciones. Para proteger los datos, puede usar el cifrado. También es importante realizar una auditoría y un seguimiento para poder volver a realizar un seguimiento de los eventos si existe una actividad sospechosa.

Para más información sobre cómo administrar la seguridad, consulte el artículo de [Introducción a la seguridad][Introducción a la seguridad].

## <a name="backup-and-restore"></a>Copia de seguridad y restauración
Una parte esencial de cualquier base de datos de producción es contar con copias de seguridad confiables de los datos. Almacenamiento de datos SQL mantiene los datos seguros al realizar automáticamente una copia de seguridad de las bases de datos activas a intervalos regulares. Estas copias de seguridad le permiten recuperarse de situaciones en las que los datos han quedado dañados o los datos o la base de datos se han eliminado por accidente.  Para información sobre la programación de copias de seguridad de datos, la directiva de retención y la restauración de bases de datos, consulte el artículo sobre la [Restauración desde una instantánea][Restauración desde una instantánea].

## <a name="next-steps"></a>Pasos siguientes
Unos buenos principios de diseño de base de datos le facilitarán la administración de las bases de datos en Almacenamiento de datos SQL. Para más información, consulte el artículo de [Información general sobre desarrollo][Información general sobre desarrollo].

<!--Image references-->

<!--Article references-->
[Creación de SQL Data Warehouse (Azure Portal)]: sql-data-warehouse-get-started-provision.md
[Creación de una base de datos (PowerShell)]: sql-data-warehouse-get-started-provision-powershell.md
[conexión]: sql-data-warehouse-develop-connections.md
[Consultas en Almacenamiento de datos SQL de Azure con Visual Studio]: sql-data-warehouse-query-visual-studio.md
[Conexión y consultas con sqlcmd]: sql-data-warehouse-get-started-connect-sqlcmd.md
[Información general sobre desarrollo]: sql-data-warehouse-overview-develop.md
[Supervisión de la carga de trabajo mediante DMV]: sql-data-warehouse-manage-monitor.md
[Pausa del proceso]: sql-data-warehouse-manage-compute-overview.md#pause-compute-bk
[Restauración desde una instantánea]: sql-data-warehouse-restore-database-overview.md
[Reanudación del proceso]: sql-data-warehouse-manage-compute-overview.md#resume-compute-bk
[Scale performance]: sql-data-warehouse-manage-compute-overview.md#scale-performance-bk
[Introducción a la seguridad]: sql-data-warehouse-overview-manage-security.md
[Prácticas recomendadas de Almacenamiento de datos SQL]: sql-data-warehouse-best-practices.md
[Vistas del sistema de Almacenamiento de datos SQL]: sql-data-warehouse-reference-tsql-system-views.md

<!--MSDN references-->
[SQL Server Data Tools]: https://msdn.microsoft.com/library/mt204009.aspx

<!--Other web references-->
[Portal de Azure]: http://portal.azure.com/



<!--HONumber=Nov16_HO3-->


