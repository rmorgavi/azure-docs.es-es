---
title: "Supervisión de operaciones, eventos y contadores para grupos de seguridad de red | Microsoft Docs"
description: Aprenda a habilitar contadores, eventos y registros operativos para grupos de seguridad de red
services: virtual-network
documentationcenter: na
author: jimdial
manager: carmonm
editor: tysonn
tags: azure-resource-manager
ms.assetid: 2e699078-043f-48bd-8aa8-b011a32d98ca
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/14/2016
ms.author: jdial
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: 30542a5166dffda4a99fe2fccd9e1c5d6127cabd


---
# <a name="log-analytics-for-network-security-groups-nsgs"></a>Análisis del registro para grupos de seguridad de red (NSG)
Puede usar diferentes tipos de registros en Azure para administrar y solucionar problemas de grupos de seguridad de red. Se puede acceder a algunos de estos registros a través del portal y se pueden extraer todos los registros desde un almacenamiento de blobs de Azure y verse en distintas herramientas, como [Log Analytics](../log-analytics/log-analytics-azure-networking-analytics.md), Excel y PowerBI. Puede obtener más información acerca de los diferentes tipos de registros en la lista siguiente.

* **Registros de auditoría:** puede usar los [registros de auditoría de Azure](../monitoring-and-diagnostics/insights-debugging-with-events.md) (anteriormente conocido como registros operativos) para ver todas las operaciones enviadas a sus suscripciones de Azure, así como su estado. Los registros de auditoría están habilitados de forma predeterminada y se pueden ver en el Portal de vista previa de Azure.
* **Registros de eventos:** puede usar este registro para ver qué reglas de grupos de seguridad de red se aplican a las máquinas virtuales y a los roles de instancia en función de la dirección MAC. El estado de estas reglas se recopila cada 60 segundos.
* **Registros de contador:** puede usar este registro para ver cuántas veces se aplica cada regla de grupos de seguridad de red para denegar o permitir el tráfico.

> [!WARNING]
> Los registros solo están disponibles para los recursos implementados en el modelo de implementación del Administrador de recursos. No puede usar los registros de recursos del modelo de implementación clásica. Para entender mejor los dos modelos, consulte el artículo [Descripción de la implementación del Administrador de recursos y la implementación clásica](../resource-manager-deployment-model.md) .
> 
> 

## <a name="enable-logging"></a>Habilitación del registro
El registro de auditoría se habilita automáticamente siempre para todos los recursos del Administrador de recursos. Debe habilitar el registro de eventos y de contadores para iniciar la recopilación de los datos disponibles a través de esos registros. Para habilitar el registro, siga estos pasos.

1. Inicie sesión en el [Portal de Azure](https://portal.azure.com). Si aún no tiene un grupo de seguridad de red existente, [cree un grupo de seguridad de red](virtual-networks-create-nsg-arm-ps.md) antes de continuar.
2. En el Portal de vista previa, haga clic en **Examinar** >> **Grupos de seguridad de red**.
   
   ![Portal de vista previa: Grupos de seguridad de red](./media/virtual-network-nsg-manage-log/portal-enable1.png)
3. Seleccione un grupo de seguridad de red existente.
   
    ![Portal de vista previa: Configuración de los grupos de seguridad de red](./media/virtual-network-nsg-manage-log/portal-enable2.png)
4. En la hoja **Configuración**, haga clic en **Diagnósticos** y después, en el panel **Diagnósticos**, junto a **Estado**, haga clic en **Activar**.
5. En la hoja **Configuración**, haga clic en **Cuenta de almacenamiento** y seleccione una cuenta de almacenamiento existente o cree una nueva.  

> [AZURE.INFORMATION] Los registros de auditoría no requieren una cuenta de almacenamiento independiente. El uso del almacenamiento para el registro de eventos y de reglas supondrán un costo adicional de servicio.
> 
> 

1. En la lista desplegable situada debajo de **Cuenta de almacenamiento**, seleccione si desea registrar los eventos, los contadores o ambos y, después, haga clic en **Guardar**.
   
    ![Portal de vista previa: Registros de diagnóstico](./media/virtual-network-nsg-manage-log/portal-enable3.png)

## <a name="audit-log"></a>Registro de auditoría
Azure genera este registro (anteriormente conocido como "registro operativo") de forma predeterminada.  Los registros se conservan durante 90 días en el almacén de registros de eventos de Azure. Para obtener más información sobre estos registros, consulte el artículo [Visualización de eventos y registros de auditoría](../monitoring-and-diagnostics/insights-debugging-with-events.md) .

## <a name="counter-log"></a>Registro de contadores
Este registro solo se genera si lo habilitó en un grupo de seguridad de red, tal como se indicó anteriormente. Los datos se almacenan en la cuenta de almacenamiento que especificó cuando habilitó el registro. Cada regla que se aplica a los recursos se registra en formato JSON, tal como se muestra a continuación.

    {
        "time": "2015-09-11T23:14:22.6940000Z",
        "systemId": "e22a0996-e5a7-4952-8e28-4357a6e8f0c5",
        "category": "NetworkSecurityGroupRuleCounter",
        "resourceId": "/SUBSCRIPTIONS/D763EE4A-9131-455F-8C5E-876035455EC4/RESOURCEGROUPS/INSIGHTOBONRP/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/NSGINSIGHTOBONRP",
        "operationName": "NetworkSecurityGroupCounters",
        "properties": {
            "vnetResourceGuid":"{DD0074B1-4CB3-49FA-BF10-8719DFBA3568}",
            "subnetPrefix":"10.0.0.0/24",
            "macAddress":"001517D9C43C",
            "ruleName":"DenyAllOutBound",
            "direction":"Out",
            "type":"block",
            "matchedConnections":0
            }
    }

## <a name="event-log"></a>Registro de eventos
Este registro solo se genera si lo habilitó en un grupo de seguridad de red, tal como se indicó anteriormente. Los datos se almacenan en la cuenta de almacenamiento que especificó cuando habilitó el registro. Se registran los datos siguientes:

    {
        "time": "2015-09-11T23:05:22.6860000Z",
        "systemId": "e22a0996-e5a7-4952-8e28-4357a6e8f0c5",
        "category": "NetworkSecurityGroupEvent",
        "resourceId": "/SUBSCRIPTIONS/D763EE4A-9131-455F-8C5E-876035455EC4/RESOURCEGROUPS/INSIGHTOBONRP/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/NSGINSIGHTOBONRP",
        "operationName": "NetworkSecurityGroupEvents",
        "properties": {
            "vnetResourceGuid":"{DD0074B1-4CB3-49FA-BF10-8719DFBA3568}",
            "subnetPrefix":"10.0.0.0/24",
            "macAddress":"001517D9C43C",
            "ruleName":"AllowVnetOutBound",
            "direction":"Out",
            "priority":65000,
            "type":"allow",
            "conditions":{
                "destinationPortRange":"0-65535",
                "sourcePortRange":"0-65535",
                "destinationIP":"10.0.0.0/8,172.16.0.0/12,169.254.0.0/16,192.168.0.0/16,168.63.129.16/32",
                "sourceIP":"10.0.0.0/8,172.16.0.0/12,169.254.0.0/16,192.168.0.0/16,168.63.129.16/32"
            }
        }
    }

## <a name="view-and-analyze-the-audit-log"></a>Visualización y análisis del registro de auditoría
Puede ver y analizar los datos del registro de auditoría mediante el uso de cualquiera de los métodos siguientes:

* **Herramientas de Azure:** puede recuperar información de los registros de auditoría a través de Azure PowerShell, de la interfaz de la línea de comandos (CLI) de Azure, la API de REST de Azure o el Portal de vista previa de Azure.  En el artículo [Operaciones de auditoría con el Administrador de recursos](../resource-group-audit.md) se detallan instrucciones paso a paso de cada método.
* **Power BI:** si todavía no tiene una cuenta de [Power BI](https://powerbi.microsoft.com/pricing) , puede probarlo gratis. Con el [paquete de contenido de los registros de auditoría de Azure para Power BI](https://powerbi.microsoft.com/documentation/powerbi-content-pack-azure-audit-logs/) puede analizar los datos con los paneles preconfigurados que puede usar tal cual o personalizarlos.

## <a name="view-and-analyze-the-counter-and-event-log"></a>Visualización y análisis del registro de eventos y de contadores
[Log Analytics](../log-analytics/log-analytics-azure-networking-analytics.md) de Azure puede recopilar el contador de registro de eventos desde la cuenta de Almacenamiento de blobs e incluye visualizaciones y eficaces funcionalidades de búsqueda para analizar los registros.

También puede conectarse a la cuenta de almacenamiento y recuperar las entradas del registro de JSON para los registros de eventos y de contadores. Cuando descargue los archivos JSON, se pueden convertir a CSV y consultarlos en Excel, PowerBI o cualquier otra herramienta de visualización de datos.

> [!TIP]
> Si está familiarizado con Visual Studio y con los conceptos básicos de cambiar los valores de constantes y variables de C#, puede usar las [herramientas convertidoras de registros](https://github.com/Azure-Samples/networking-dotnet-log-converter) , disponibles en Github.
> 
> 

## <a name="next-steps"></a>Pasos siguientes
* Visualización del contador y de registros de eventos con [Log Analytics](../log-analytics/log-analytics-azure-networking-analytics.md)
* [Visualize your Azure Audit Logs with Power BI](http://blogs.msdn.com/b/powerbi/archive/2015/09/30/monitor-azure-audit-logs-with-power-bi.aspx) (Visualizar los registros de auditoría de Azure con Power BI).
* [View and analyze Azure Audit Logs in Power BI and more](https://azure.microsoft.com/blog/analyze-azure-audit-logs-in-powerbi-more/) (Visualizar los registros de auditoría de Azure con Power BI).




<!--HONumber=Nov16_HO3-->


