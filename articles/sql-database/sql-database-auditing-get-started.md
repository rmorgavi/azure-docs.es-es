---
title: "Introducción a la auditoría de bases de datos SQL | Microsoft Docs"
description: "Introducción a la auditoría de bases de datos SQL"
services: sql-database
documentationcenter: 
author: ronitr
manager: jhubbard
editor: 
ms.assetid: 89c2a155-c2fb-4b67-bc19-9b4e03c6d3bc
ms.service: sql-database
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/05/2016
ms.author: CarlRabeler; ronitr; giladm
translationtype: Human Translation
ms.sourcegitcommit: 80680647559fe8b42cfa09985b9da3713b9c9305
ms.openlocfilehash: cf0152b0cbcc85befa1b753b92f1593af958dd89


---
# <a name="get-started-with-sql-database--auditing"></a>Introducción a la auditoría de bases de datos SQL
Auditoría de Azure SQL Database realiza un seguimiento de eventos de bases de datos y registra los eventos en un registro de auditoría de la cuenta de Azure Storage.

La auditoría puede ayudarle a mantener el cumplimiento de normativas, comprender la actividad de las bases de datos y conocer las discrepancias y anomalías que pueden indicar problemas en el negocio o infracciones de seguridad sospechosas.

La auditoría posibilita y facilita la observancia de estándares reguladores pero no garantiza el cumplimiento. Para obtener más información acerca de los programas de Azure compatibles con la observancia de estándares, consulte el [Centro de confianza de Azure](https://azure.microsoft.com/support/trust-center/compliance/).

* [Información general de Auditoría de Azure SQL Database]
* [Configuración de la auditoría para su base de datos]
* [Análisis de registros e informes de auditoría]

## <a name="a-idsubheading-1aazure-sql-database-auditing-overview"></a><a id="subheading-1"></a>Información general de Auditoría de Azure SQL Database
La auditoría de Base de datos SQL le permite:

* **Conservar** una traza de auditoría de eventos seleccionados. Puede definir categorías de acciones de base de datos para auditar.
* **Informar** sobre la actividad de la base de datos. Puede usar informes preconfigurados y un panel para empezar rápidamente con el informe de actividades y eventos.
* **Analizar** informes. Puede buscar eventos sospechosos, actividades inusuales y tendencias.

Existen dos **métodos de auditoría**:

* **Auditoría de blobs**: los registros se escriben en Azure Blob Storage. Es el método de auditoría más reciente, que proporciona **mayor rendimiento**, admite **auditoría de nivel de objeto de mayor granularidad** y es **más rentable**.
* **Auditoría de tablas**: los registros se escriben en Azure Table Storage.

Puede configurar la auditoría para diferentes tipos de categorías de eventos, como se explica en la sección [Configuración de la auditoría para su base de datos](#subheading-2).

<!--For each Event Category, auditing of **Success** and **Failure** operations are configured separately.-->

Puede definirse una directiva de auditoría para una base de datos específica o como directiva de servidor predeterminada. Una directiva de auditoría de servidor predeterminada se aplica a todas las bases de datos recién creadas en un servidor.

## <a name="a-idsubheading-2aset-up-auditing-for-your-database"></a><a id="subheading-2"></a>Configuración de la auditoría para su base de datos
Las secciones siguientes describen la configuración de auditoría mediante el Portal de Azure.

### <a name="a-idsubheading-2-1blob-auditinga"></a><a id="subheading-2-1">Auditoría de blobs</a>
1. Inicie [Azure Portal](https://portal.azure.com) en https://portal.azure.com.
2. Vaya a la hoja de configuración de la base de datos SQL o el servidor SQL Server que desea auditar. En la hoja Configuración, seleccione **Auditoría y detección de amenazas**.
   
    <a id="auditing-screenshot"></a>
    ![Panel de navegación][1]
3. En la hoja de configuración de la auditoría de base de datos, puede activar la casilla **Heredar la configuración del servidor** para indicar que esta base de datos se auditará según la configuración de su servidor. Si esta opción está activada, verá un vínculo **Ver configuración de auditoría de servidor** que le permite ver o modificar la configuración de auditoría del servidor desde este contexto.
   
    ![Panel de navegación][2]
4. Si prefiere habilitar la auditoría de blobs en el nivel de base de datos (además, o en lugar, del nivel de servidor) **desactive** la opción **Heredar la configuración de auditoría del servidor**, **active** Auditoría y elija el tipo **Blob**.
   
   > Si está habilitada la auditoría de blobs del servidor, la auditoría configurada de base de datos se producirá de forma paralela a la auditoría de blobs del servidor.  
   > 
   > 
   
    ![Panel de navegación][3]
5. Seleccione **Detalles de almacenamiento** para abrir la hoja de almacenamiento de registros de auditoría. Seleccione la cuenta de almacenamiento de Azure donde se guardarán los registros, y el período de retención, después del cual se eliminarán los registros antiguos. A continuación, haga clic en **Aceptar** en la parte inferior. **Sugerencia:** use la misma cuenta de almacenamiento para todas las bases de datos auditadas con el fin de obtener el máximo rendimiento de las plantillas de informes de auditorías.
   
    <a id="storage-screenshot"></a>
    ![Panel de navegación][4]
6. Si quiere personalizar los eventos auditados, puede hacerlo mediante PowerShell o la API de REST (consulte la sección [Automation (PowerShell/API de REST)](#subheading-7) para más información.
7. Haga clic en **Save**.

### <a name="a-idsubheading-2-2table-auditinga"></a><a id="subheading-2-2">Auditoría de tablas</a>
> [!NOTE]
> Antes de configurar la **auditoría de tablas**, compruebe si usa un ["Cliente de nivel inferior"](sql-database-auditing-and-dynamic-data-masking-downlevel-clients.md). Además, si tiene una configuración de firewall estricta, tenga en cuenta que el [punto de conexión de IP de la base de datos cambiará](sql-database-auditing-and-dynamic-data-masking-downlevel-clients.md) al habilitar la auditoría de tablas.
> 
> 

1. Inicie [Azure Portal](https://portal.azure.com) en https://portal.azure.com.
2. Vaya a la hoja de configuración de la base de datos SQL o el servidor SQL Server que desea auditar. En la hoja de configuración, seleccione **Auditoría y detección de amenazas** (*[consulte la captura de pantalla en la sección Auditoría de blobs](#auditing-screenshot)*).
3. En la hoja de configuración de la auditoría de base de datos, puede activar la casilla **Heredar la configuración del servidor** para indicar que esta base de datos se auditará según la configuración de su servidor. Si esta opción está activada, verá un vínculo **Ver configuración de auditoría de servidor** que le permite ver o modificar la configuración de auditoría del servidor desde este contexto.
   
    ![Panel de navegación][2]
4. Si prefiere no heredar la configuración de auditoría del servidor, **desactive** la opción **eredar la configuración de auditoría del servidor**, **active** Auditoría y elija el tipo **abla**.
   
    ![Panel de navegación][3-tbl]
5. Seleccione **Detalles de almacenamiento** para abrir la hoja de almacenamiento de registros de auditoría. Seleccione la cuenta de almacenamiento de Azure donde se guardan los registros, y el período de retención, después del cual los registros antiguos se eliminarán. **Sugerencia:** use la misma cuenta de almacenamiento para todas las bases de datos auditadas a fin de aprovechar al máximo las plantillas de informes de auditoría (*[consulte la captura de pantalla en la sección Auditoría de blobs](#storage-screenshot)*).
6. Haga clic en **Eventos auditados** para personalizar los eventos que se van a auditar. En la hoja **Registro por evento** haga clic en **Acierto** y **Error** para registrar todos los eventos, o elija categorías individuales de eventos.
   
   > La personalización de los eventos auditados se puede realizar también mediante PowerShell o la API de REST (consulte la sección [Automation (PowerShell/API de REST)](#subheading-7) para más información.
   > 
   > 
   
    ![Panel de navegación][5]
7. Cuando haya configurado los valores de auditoría, puede activar la nueva **detección de amenazas** (versión preliminar) y configurar los mensajes de correo electrónico para recibir alertas de seguridad. La detección de amenazas le permite recibir alertas proactivas sobre actividades anómalas de la base de datos que pueden indicar posibles amenazas de seguridad. Vea [Introducción a la detección de amenazas](sql-database-threat-detection-get-started.md) para obtener más detalles.
8. Haga clic en **Guardar**.

## <a name="a-idsubheading-3aanalyze-audit-logs-and-reports"></a><a id="subheading-3"></a>Análisis de registros e informes de auditoría
Los registros de auditoría se agregan a la cuenta de almacenamiento de Azure que eligió durante la instalación.

Puede explorar los registros de auditoría con una herramienta como el [Explorador de Azure Storage](http://storageexplorer.com/).

Consulte a continuación los aspectos específicos del análisis de los registros de auditoría de **Tabla** y **Blob**.

### <a name="a-idsubheading-3-1blob-auditinga"></a><a id="subheading-3-1">Auditoría de blobs</a>
Los registros de auditoría de blobs se guatdan como una colección de archivos de blob dentro de un contenedor llamado "**sqldbauditlogs**".

Para más información sobre la jerarquía de carpetas de almacenamiento de registros de auditoría de blobs, y el formato del registro, consulte la [referencia del formato de registro de auditoría de blobs (descarga del archivo de documento)](https://go.microsoft.com/fwlink/?linkid=829599).

Existen varios métodos para ver los registros de auditoría de blobs:

1. Mediante Azure Portal: **consulte el método (1) en la [sección Auditoría de tablas](#activity-ui) a continuación** (no se admite la descarga de Excel).
2. Descargue los archivos de registro del contenedor de Azure Storage Blob mediante el portal o con una herramienta como el [Explorador de almacenamiento de Azure](http://storageexplorer.com/).
   
    Cuando haya descargado localmente el archivo de registro, haga doble clic en él para abrir, ver y analizar los registros en SSMS.
   
    Otros métodos:
   
   * Puede **descargar varios archivos a la vez** mediante el Explorador de Azure Storage: haga clic con el botón derecho en una subcarpeta específica (por ejemplo, una que incluya todos los archivos de registro de una fecha determinada) y elija "Guardar como" para guardarla en una carpeta local.
     
       Después de descargar varios archivos (o un día completo, como se ha descrito anteriormente), puede combinarlos localmente de la manera siguiente:
     
       **Abra SSMS -> Archivo -> Abrir -> Merge Extended Events (Combinar eventos extendidos) -> Choose all files to merge** (Seleccionar todos los archivos para combinar).
   * Mediante programación:
     
     * Lector de eventos extendidos **biblioteca de C#** ([más información aquí](https://blogs.msdn.microsoft.com/extended_events/2011/07/20/introducing-the-extended-events-reader/))
     * Consulta de archivos de eventos extendidos mediante **PowerShell** ([más información aquí](https://sqlscope.wordpress.com/2014/11/15/reading-extended-event-files-using-client-side-tools-only/))

### <a name="a-idsubheading-3-2table-auditinga"></a><a id="subheading-3-2">Auditoría de tablas</a>
Los registros de auditoría de tablas se guardan como una colección de instancias de Azure Storage Table con un prefijo **SQLDBAuditLogs**.

Para más información sobre el formato de registro de auditoría de tablas, consulte la [referencia del formato de registro de auditoría de tablas (descarga del archivo de documento)](http://go.microsoft.com/fwlink/?LinkId=506733).

Existen varios métodos para ver los registros de auditoría de tablas:

<a id="activity-ui"></a>

1. Mediante [Azure Portal](https://portal.azure.com): en la parte superior de la hoja **Auditoría y detección de amenazas**, haga clic en **Más** y luego en **Explorar**. Se abre una hoja **Registros de auditoría**, donde podrá ver los registros.
   
   * Puede elegir ver fechas específicas si hace clic en **Filtrar** en la parte superior de la hoja Registros de auditoría.
   * Puede descargar y ver los registros de auditoría en formato Excel si hace clic en **Abrir en Excel** en la parte superior de la hoja Registros de auditoría
     
     ![Panel de navegación][7]
2. Como alternativa, hay una plantilla de informe preconfigurada disponible como [hoja de cálculo de Excel descargable](http://go.microsoft.com/fwlink/?LinkId=403540) para ayudarle a analizar rápidamente los datos de registro. Para utilizar la plantilla en los registros de auditoría, necesita Excel 2013 o posterior y Power Query, que puede descargar [aquí](http://www.microsoft.com/download/details.aspx?id=39379).
   
    También puede importar los registros de auditoría en la plantilla de Excel directamente desde su cuenta de almacenamiento de Azure con Power Query. A continuación, puede explorar los registros de auditoría y crear paneles e informes sobre los datos del registro.
   
    ![Panel de navegación][9]

## <a name="a-idsubheading-5apractices-for-usage-in-production"></a><a id="subheading-5"></a>Procedimientos de uso en producción
<!--The description in this section refers to screen captures above.-->

### <a name="a-idsubheading-6auditing-geo-replicated-databasesa"></a><a id="subheading-6">Auditoría de bases de datos con replicación geográfica</a>
Al usar bases de datos con replicación geográfica, es posible configurar la auditoría en la base de datos principal, la base de datos secundaria, o ambas, según el tipo de auditoría.

**Auditoría de tablas**: puede configurar una directiva aparte, en el nivel de base de datos o de servidor, para cada una de las dos bases de datos (principal y secundaria) como se describe en la sección [Configuración de la auditoría para su base de datos](#subheading-2-2).

**Auditoría de blobs**: siga estas instrucciones:

1. **Base de datos principal**: active **Auditoría de blobs** en el servidor o en la propia base de datos, como se describe en la sección [Configuración de la auditoría para su base de datos](#subheading-2-1).
2. **Base de datos secundaria**: la auditoría de blobs solo se puede activar o desactivar desde la configuración de auditoría de la base de datos principal.
   
   * Active **Auditoría de blobs** en la **base de datos principal**, como se describe en la sección [Configuración de la auditoría para su base de datos](#subheading-2-1). (**Nota:** La auditoría de blobs debe estar habilitada en la propia base de datos, no en el servidor).
   * Después de habilitar la auditoría de blobs en la base de datos principal, también se habilitará en la base de datos secundaria.
   * **Importante:** De forma predeterminada, la **configuración de almacenamiento** de la base de datos secundaria será idéntica a la de la base de datos principal, lo que provocará **tráfico interregional**. Para evitarlo, habilite la auditoría de blobs en el **servidor secundario** y configure un **almacenamiento local** en su configuración de almacenamiento, así se invalidará la ubicación de almacenamiento de la base de datos secundaria y se guardarán los registros de auditoría de cada base de datos en un almacenamiento local.  

<br>

### <a name="a-idsubheading-6storage-key-regenerationa"></a><a id="subheading-6">Regeneración de clave de almacenamiento</a>
En el entorno de producción, es probable que actualice periódicamente las claves de almacenamiento. Al actualizar las claves se debe volver a guardar la directiva de auditoría. El proceso es el siguiente:

1. En la hoja de detalles de almacenamiento cambie el valor de **Clave de acceso de almacenamiento** de *rincipal* a *ecundaria* y luego haga clic en **Aceptar** en la parte inferior. A continuación, haga clic en **GUARDAR** en la parte superior de la hoja de configuración de auditoría.
   
    ![Panel de navegación][6]
2. Vaya a la hoja de configuración de almacenamiento y **regenere** la *Clave de acceso primaria*.
   
    ![Panel de navegación][8]
3. Vuelva a la hoja de configuración de auditoría, cambie el valor de **Clave de acceso de almacenamiento** de *Secundaria* a *Principal* y haga clic en **Aceptar** en la parte inferior. A continuación, haga clic en **GUARDAR** en la parte superior de la hoja de configuración de auditoría.
4. Vuelva a la hoja de configuración de almacenamiento y **vuelva a generar** la *lave de acceso secundaria* (como preparación para el siguiente ciclo de actualización de claves).

## <a name="a-idsubheading-7aautomation-powershell--rest-api"></a><a id="subheading-7"></a>Automation (PowerShell/API de REST)
También puede configurar la auditoría en Azure SQL Database mediante las siguientes herramientas de automatización:

1. **Cmdlets de PowerShell**
   
   * [Get-AzureRMSqlDatabaseAuditingPolicy][101]
   * [Get-AzureRMSqlServerAuditingPolicy][102]
   * [Remove-AzureRMSqlDatabaseAuditing][103]
   * [Remove-AzureRMSqlServerAuditing][104]
   * [Set-AzureRMSqlDatabaseAuditingPolicy][105]
   * [Set-AzureRMSqlServerAuditingPolicy][106]
   * [Use-AzureRMSqlServerAuditingPolicy][107]
2. **API de REST, auditoría de blobs**
   
   * [Create or Update Database Blob Auditing Policy](https://msdn.microsoft.com/en-us/library/azure/mt695939.aspx) (Creación o actualización de la directiva de auditoría de blobs de la base de datos)
   * [Create or Update Server Blob Auditing Policy](https://msdn.microsoft.com/en-us/library/azure/mt771861.aspx) (Creación o actualización de la directiva de audioría de blobs del servidor)
   * [Get Database Blob Auditing Policy](https://msdn.microsoft.com/en-us/library/azure/mt695938.aspx) (Obtención de la directiva de auditoría de bobs de la base de datos)
   * [Get Server Blob Auditing Policy](https://msdn.microsoft.com/en-us/library/azure/mt771860.aspx) (Obtención de la directiva de auditoría de blobs del servidor)
   * [Get Server Blob Auditing Operation Result](https://msdn.microsoft.com/en-us/library/azure/mt771862.aspx) (Obtención de resultados de funcionamiento de la auditoría de blobs del servidor)
3. **API de REST, auditoría de tablas**
   
   * [Create or Update Database Auditing Policy](https://msdn.microsoft.com/en-us/library/azure/mt604471.aspx) (Creación o actualización de la directiva de auditoría de la base de datos)
   * [Create or Update Server Auditing Policy](https://msdn.microsoft.com/en-us/library/azure/mt604383.aspx) (Creación o actualización de la directiva de auditoría del servidor)
   * [Get Database Auditing Policy](https://msdn.microsoft.com/en-us/library/azure/mt604381.aspx) (Obtención de la directiva de auditoría de la base de datos)
   * [Get Server Auditing Policy](https://msdn.microsoft.com/en-us/library/azure/mt604382.aspx) (Obtención de la directiva de auditoría del servidor)

<!--Anchors-->
[Información general de Auditoría de Azure SQL Database]: #subheading-1
[Configuración de la auditoría para su base de datos]: #subheading-2
[Análisis de registros e informes de auditoría]: #subheading-3
[Prácticas para el uso en producción]: #subheading-5
[Regeneración de clave de almacenamiento]: #subheading-6
[Automation (PowerShell/API de REST)]: #subheading-7


<!--Image references-->
[1]: ./media/sql-database-auditing-get-started/1_auditing_get_started_settings.png
[2]: ./media/sql-database-auditing-get-started/2_auditing_get_started_server_inherit.png
[3]: ./media/sql-database-auditing-get-started/3_auditing_get_started_turn_on.png
[3-tbl]: ./media/sql-database-auditing-get-started/3_auditing_get_started_turn_on_table.png
[4]: ./media/sql-database-auditing-get-started/4_auditing_get_started_storage_details.png
[5]: ./media/sql-database-auditing-get-started/5_auditing_get_started_audited_events.png
[6]: ./media/sql-database-auditing-get-started/6_auditing_get_started_storage_key_regeneration.png
[7]: ./media/sql-database-auditing-get-started/7_auditing_get_started_activity_log.png
[8]: ./media/sql-database-auditing-get-started/8_auditing_get_started_regenerate_key.png
[9]: ./media/sql-database-auditing-get-started/9_auditing_get_started_report_template.png


[101]: https://msdn.microsoft.com/en-us/library/azure/mt603731(v=azure.200).aspx
[102]: https://msdn.microsoft.com/en-us/library/azure/mt619329(v=azure.200).aspx
[103]: https://msdn.microsoft.com/en-us/library/azure/mt603796(v=azure.200).aspx
[104]: https://msdn.microsoft.com/en-us/library/azure/mt603574(v=azure.200).aspx
[105]: https://msdn.microsoft.com/en-us/library/azure/mt603531(v=azure.200).aspx
[106]: https://msdn.microsoft.com/en-us/library/azure/mt603794(v=azure.200).aspx
[107]: https://msdn.microsoft.com/en-us/library/azure/mt619353(v=azure.200).aspx



<!--HONumber=Nov16_HO3-->


