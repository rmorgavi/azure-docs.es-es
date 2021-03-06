---
title: Traslado de los datos de MySQL | Microsoft Docs
description: "Obtenga información acerca de cómo mover los datos de la base de datos de MySQL mediante Factoría de datos de Azure."
services: data-factory
documentationcenter: 
author: linda33wj
manager: jhubbard
editor: monicar
ms.assetid: 452f4fce-9eb5-40a0-92f8-1e98691bea4c
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/01/2016
ms.author: jingwang
translationtype: Human Translation
ms.sourcegitcommit: 6ec8ac288a4daf6fddd6d135655e62fad7ae17c2
ms.openlocfilehash: 76b7469260a88d67c8a14e4286b62d0019f99f18


---
# <a name="move-data-from-mysql-using-azure-data-factory"></a>Movimiento de datos de MySQL mediante Factoría de datos de Azure
En este artículo se describe cómo puede usar la actividad de copia en Factoría de datos de Azure para mover datos de MySQL a otro almacén de datos. Este artículo se basa en el artículo sobre [actividades de movimiento de datos](data-factory-data-movement-activities.md) que presenta una introducción general del movimiento de datos con la actividad de copia y las combinaciones del almacén de datos admitidas.

El servicio Factoría de datos admite la conexión a orígenes de MySQL locales mediante Data Management Gateway. Consulte el artículo sobre cómo [mover datos entre ubicaciones locales y la nube](data-factory-move-data-between-onprem-and-cloud.md) para obtener información acerca de Data Management Gateway, así como instrucciones paso a paso sobre cómo configurar la puerta de enlace.

> [!NOTE]
> La puerta de enlace es necesaria incluso si la base de datos MySQL está hospedada en una máquina virtual (VM) de IaaS de Azure. Puede instalar la puerta de enlace en la misma máquina virtual como almacén de datos o en una máquina virtual diferente, siempre y cuando la puerta de enlace se pueda conectar a la base de datos.  
>
>

Factoría de datos solo admite actualmente el movimiento de datos desde MySQL a otros almacenes de datos, pero no de otros almacenes de datos a MySQL.

## <a name="supported-versions-and-installation"></a>Versiones compatibles e instalación
Para que la puerta de enlace de administración de datos se conecte a la base de datos de MySQL, deberá instalar el [conector MySQL/Net 6.6.5 para Microsoft Windows](http://go.microsoft.com/fwlink/?LinkId=278885) en el mismo sistema que la puerta de enlace de administración de datos. Se admite la versión 5.1 de MySQL o versiones posteriores.

> [!NOTE]
> Consulte [Solución de problemas de la puerta de enlace](data-factory-data-management-gateway.md#troubleshooting-gateway-issues) para obtener sugerencias para solucionar problemas de conexión o puerta de enlace.
>
>

## <a name="copy-data-wizard"></a>Asistente para copia de datos
La manera más fácil de crear una canalización que copie datos de una base de datos de MySQL en cualquiera de los almacenes de datos receptores admitidos es usar el Asistente para copiar datos. Consulte [Tutorial: crear una canalización con la actividad de copia mediante el Asistente para copia de Data Factory](data-factory-copy-data-wizard-tutorial.md) para ver un tutorial rápido sobre la creación de una canalización mediante el Asistente para copiar datos.

En el ejemplo siguiente se proporcionan definiciones JSON de ejemplo que puede usar para crear una canalización. Puede encontrar un tutorial completo con instrucciones paso a paso en el artículo sobre el [movimiento de datos entre ubicaciones locales y la nube](data-factory-move-data-between-onprem-and-cloud.md) . Los datos se pueden copiar en cualquiera de los receptores indicados [aquí](data-factory-data-movement-activities.md#supported-data-stores-and-formats) mediante la actividad de copia en Data Factory de Azure.   

## <a name="sample-copy-data-from-mysql-to-azure-blob"></a>Ejemplo: Copiar datos de MySQL a un blob de Azure
En este ejemplo, se muestra cómo copiar datos de una base de datos MySQL local a un Almacenamiento de blobs de Azure. Sin embargo, se pueden copiar datos **directamente** a cualquiera de los receptores indicados [aquí](data-factory-data-movement-activities.md#supported-data-stores-and-formats) mediante la actividad de copia en Data Factory de Azure.  

> [!IMPORTANT]
> Este ejemplo proporciona fragmentos JSON. No incluye instrucciones paso a paso para crear la factoría de datos. Las instrucciones paso a paso se encuentran en el artículo sobre cómo [mover datos entre ubicaciones locales y en la nube](data-factory-move-data-between-onprem-and-cloud.md) .
>
>

El ejemplo consta de las siguientes entidades de factoría de datos:

1. Un servicio vinculado de tipo [OnPremisesMySql](data-factory-onprem-mysql-connector.md#mysql-linked-service-properties).
2. Un servicio vinculado de tipo [AzureStorage](data-factory-azure-blob-connector.md#azure-storage-linked-service)
3. Un [conjunto de datos](data-factory-create-datasets.md) de entrada de tipo [RelationalTable](data-factory-onprem-mysql-connector.md#mysql-dataset-type-properties).
4. Un [conjunto de datos](data-factory-create-datasets.md) de salida de tipo [AzureBlob](data-factory-azure-blob-connector.md#azure-blob-dataset-type-properties).
5. Una [canalización](data-factory-create-pipelines.md) con la actividad de copia que usa [RelationalSource](data-factory-onprem-mysql-connector.md#mysql-copy-activity-type-properties) y [BlobSink](data-factory-azure-blob-connector.md#azure-blob-copy-activity-type-properties).

En el ejemplo se copian datos de un resultado de consulta de una base de datos MySQL en un blob cada hora. Las propiedades JSON usadas en estos ejemplos se describen en las secciones que aparecen después de los ejemplos.

En primer lugar, configure Data Management Gateway. Las instrucciones se encuentran en el artículo sobre cómo [mover datos entre ubicaciones locales y en la nube](data-factory-move-data-between-onprem-and-cloud.md) .

**Servicio vinculado de MySQL**

    {
      "name": "OnPremMySqlLinkedService",
      "properties": {
        "type": "OnPremisesMySql",
        "typeProperties": {
          "server": "<server name>",
          "database": "<database name>",
          "schema": "<schema name>",
          "authenticationType": "<authentication type>",
          "userName": "<user name>",
          "password": "<password>",
          "gatewayName": "<gateway>"
        }
      }
    }

**Servicio vinculado de Almacenamiento de Azure**

    {
      "name": "AzureStorageLinkedService",
      "properties": {
        "type": "AzureStorage",
        "typeProperties": {
          "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
        }
      }
    }

**Conjunto de datos de entrada de MySQL**

El ejemplo supone que ha creado una tabla "MyTable" en MySQL y que contiene una columna denominada "timestampcolumn" para los datos de serie temporal.

Si se establece "external": true, se informa al servicio Data Factory de que la tabla es externa a la factoría de datos y no la produce ninguna actividad de dicha factoría.

    {
        "name": "MySqlDataSet",
        "properties": {
            "published": false,
            "type": "RelationalTable",
            "linkedServiceName": "OnPremMySqlLinkedService",
            "typeProperties": {},
            "availability": {
                "frequency": "Hour",
                "interval": 1
            },
            "external": true,
            "policy": {
                "externalData": {
                    "retryInterval": "00:01:00",
                    "retryTimeout": "00:10:00",
                    "maximumRetry": 3
                }
            }
        }
    }



**Conjunto de datos de salida de blob de Azure**

Los datos se escriben en un nuevo blob cada hora (frecuencia: hora, intervalo: 1). La ruta de acceso de la carpeta para el blob se evalúa dinámicamente según la hora de inicio del segmento que se está procesando. La ruta de acceso de la carpeta usa las partes year, month, day y hours de la hora de inicio.

    {
        "name": "AzureBlobMySqlDataSet",
        "properties": {
            "type": "AzureBlob",
            "linkedServiceName": "AzureStorageLinkedService",
            "typeProperties": {
                "folderPath": "mycontainer/mysql/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
                "format": {
                    "type": "TextFormat",
                    "rowDelimiter": "\n",
                    "columnDelimiter": "\t"
                },
                "partitionedBy": [
                    {
                        "name": "Year",
                        "value": {
                            "type": "DateTime",
                            "date": "SliceStart",
                            "format": "yyyy"
                        }
                    },
                    {
                        "name": "Month",
                        "value": {
                            "type": "DateTime",
                            "date": "SliceStart",
                            "format": "MM"
                        }
                    },
                    {
                        "name": "Day",
                        "value": {
                            "type": "DateTime",
                            "date": "SliceStart",
                            "format": "dd"
                        }
                    },
                    {
                        "name": "Hour",
                        "value": {
                            "type": "DateTime",
                            "date": "SliceStart",
                            "format": "HH"
                        }
                    }
                ]
            },
            "availability": {
                "frequency": "Hour",
                "interval": 1
            }
        }
    }



**Canalización con actividad de copia**

La canalización contiene una actividad de copia que está configurada para usar los conjuntos de datos de entrada y de salida y está programada para ejecutarse cada hora. En la definición de la canalización JSON, el tipo **source** se establece en **RelationalSource** y el tipo **sink** se establece en **BlobSink**. La consulta SQL especificada para la propiedad **query** selecciona los datos de la última hora que se van a copiar.

    {
        "name": "CopyMySqlToBlob",
        "properties": {
            "description": "pipeline for copy activity",
            "activities": [
                {
                    "type": "Copy",
                    "typeProperties": {
                        "source": {
                            "type": "RelationalSource",
                            "query": "$$Text.Format('select * from MyTable where timestamp >= \\'{0:yyyy-MM-ddTHH:mm:ss}\\' AND timestamp < \\'{1:yyyy-MM-ddTHH:mm:ss}\\'', WindowStart, WindowEnd)"
                        },
                        "sink": {
                            "type": "BlobSink",
                            "writeBatchSize": 0,
                            "writeBatchTimeout": "00:00:00"
                        }
                    },
                    "inputs": [
                        {
                            "name": "MySqlDataSet"
                        }
                    ],
                    "outputs": [
                        {
                            "name": "AzureBlobMySqlDataSet"
                        }
                    ],
                    "policy": {
                        "timeout": "01:00:00",
                        "concurrency": 1
                    },
                    "scheduler": {
                        "frequency": "Hour",
                        "interval": 1
                    },
                    "name": "MySqlToBlob"
                }
            ],
            "start": "2014-06-01T18:00:00Z",
            "end": "2014-06-01T19:00:00Z"
        }
    }



## <a name="mysql-linked-service-properties"></a>Propiedades del servicio vinculado de MySQL
En la tabla siguiente se proporciona la descripción de los elementos JSON específicos al servicio vinculado de MySQL.

| Propiedad | Descripción | Obligatorio |
| --- | --- | --- |
| type |La propiedad type debe establecerse en: **OnPremisesMySql** |Sí |
| server |Nombre del servidor MySQL. |Sí |
| database |Nombre de la base de datos MySQL. |Sí |
| schema |Nombre del esquema de la base de datos. |No |
| authenticationType |Tipo de autenticación usado para conectarse a la Base de datos MySQL. Los valores posibles son: Anonymous, Basic y Windows. |Sí |
| nombre de usuario |Especifique el nombre de usuario si usa la autenticación Basic o Windows. |No |
| contraseña |Especifique la contraseña de la cuenta de usuario especificada para el nombre de usuario. |No |
| gatewayName |Nombre de la puerta de enlace que debe usar el servicio Factoría de datos para conectarse a la Base de datos MySQL local. |Sí |

Consulte [Movimiento de datos entre orígenes locales y la nube con Data Management Gateway](data-factory-move-data-between-onprem-and-cloud.md) para obtener más información acerca de cómo establecer las credenciales para un origen de datos de MySQL local.

## <a name="mysql-dataset-type-properties"></a>Propiedades de tipo de conjunto de datos de MySQL
Para una lista completa de las secciones y propiedades disponibles para definir conjuntos de datos, vea el artículo [Creación de conjuntos de datos](data-factory-create-datasets.md). Las secciones como structure, availability y policy del código JSON del conjunto de datos son similares para todos los tipos de conjunto de datos (SQL Azure, blob de Azure, tabla de Azure, etc.).

La sección **typeProperties** es diferente en cada tipo de conjunto de datos y proporciona información acerca de la ubicación de los datos en el almacén de datos. La sección typeProperties del conjunto de datos de tipo **RelationalTable** (que incluye el conjunto de datos de MySQL) tiene las propiedades siguientes:

| Propiedad | Descripción | Obligatorio |
| --- | --- | --- |
| tableName |Nombre de la tabla en la instancia de la Base de datos MySQL a la que hace referencia el servicio vinculado. |No (si se especifica **query** de **RelationalSource**) |

## <a name="mysql-copy-activity-type-properties"></a>Propiedades de tipo de actividad de copia de MySQL
Para ver una lista completa de las secciones y propiedades disponibles para definir actividades, consulte el artículo [Creación de canalizaciones](data-factory-create-pipelines.md). Las propiedades (como nombre, descripción, tablas de entrada y salida, y directivas) están disponibles para todos los tipos de actividades.

Por otra parte, las propiedades disponibles en la sección **typeProperties** de la actividad varían con cada tipo de actividad. Para la actividad de copia, varían en función de los tipos de orígenes y receptores.

Si el origen es de tipo **RelationalSource** (que incluye MySQL), están disponibles las propiedades siguientes en la sección typeProperties:

| Propiedad | Descripción | Valores permitidos | Obligatorio |
| --- | --- | --- | --- |
| query |Utilice la consulta personalizada para leer los datos. |Cadena de consulta SQL. Por ejemplo: select * from MyTable. |No (si se especifica **tableName** de **dataset**) |

[!INCLUDE [data-factory-structure-for-rectangualr-datasets](../../includes/data-factory-structure-for-rectangualr-datasets.md)]

### <a name="type-mapping-for-mysql"></a>Asignación de tipos para MySQL
Como se mencionó en el artículo sobre [actividades del movimiento de datos](data-factory-data-movement-activities.md) , la actividad de copia realiza conversiones automáticas de los tipos de origen a los tipos de receptor con el siguiente enfoque de dos pasos:

1. Conversión de tipos de origen nativos al tipo .NET
2. Conversión de tipo .NET al tipo del receptor nativo

Al mover datos a MySQL, se usarán las asignaciones siguientes de tipos MySQL a tipos .NET.

| Tipo de Base de datos MySQL | Tipo .NET Framework |
| --- | --- |
| bigint unsigned |Decimal |
| bigint |Int64 |
| bit |Decimal |
| blob |Byte[] |
| booleano |Booleano |
| char |String |
| fecha |Datetime |
| Datetime |Datetime |
| Decimal |Decimal |
| double precision |Doble |
| Doble |Doble |
| enum |String |
| float |Single |
| int unsigned |Int64 |
| int |Int32 |
| integer unsigned |Int64 |
| integer |Int32 |
| long varbinary |Byte[] |
| long varchar |String |
| longblob |Byte[] |
| longtext |String |
| mediumblob |Byte[] |
| mediumint unsigned |Int64 |
| mediumint |Int32 |
| mediumtext |String |
| numeric |Decimal |
| real |Doble |
| set |String |
| smallint unsigned |Int32 |
| smallint |Int16 |
| text |String |
| Twitter en tiempo |timespan |
| timestamp |Datetime |
| tinyblob |Byte[] |
| tinyint unsigned |Int16 |
| tinyint |Int16 |
| tinytext |String |
| varchar |String |
| year |int |

[!INCLUDE [data-factory-column-mapping](../../includes/data-factory-column-mapping.md)]

[!INCLUDE [data-factory-type-repeatability-for-relational-sources](../../includes/data-factory-type-repeatability-for-relational-sources.md)]

## <a name="performance-and-tuning"></a>Rendimiento y optimización
Consulte [Guía de optimización y rendimiento de la actividad de copia](data-factory-copy-activity-performance.md) para más información sobre los factores clave que afectan al rendimiento del movimiento de datos (actividad de copia) en Azure Data Factory y las diversas formas de optimizarlo.



<!--HONumber=Nov16_HO3-->


