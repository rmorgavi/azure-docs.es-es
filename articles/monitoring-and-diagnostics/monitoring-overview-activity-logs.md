---
title: "Información general sobre el registro de actividades de Azure | Microsoft Docs"
description: "Aprenda qué es el registro de actividad de Azure y cómo puede usarlo para comprender los eventos que se producen en su suscripción de Azure."
author: johnkemnetz
manager: rboucher
editor: 
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: c274782f-039d-4c28-9ddb-f89ce21052c7
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/09/2016
ms.author: johnkem
translationtype: Human Translation
ms.sourcegitcommit: c6190a5a5aba325b15aef97610c804f5441ef7ad
ms.openlocfilehash: b9c2308a85fb9a65e6e18b8c3b4373876c8d1f25


---
# <a name="overview-of-the-azure-activity-log"></a>Información general sobre el registro de actividad de Azure
El **registro de actividad de Azure** es un registro que proporciona información sobre las operaciones realizadas en recursos de su suscripción. El registro de actividad se conocía antes como "Registros de auditoría" o "Registros operativos", ya que notifica eventos del plano de control para las suscripciones. Con el Registro de actividades, se pueden determinar los interrogantes “qué, quién y cuándo” de las operaciones de escritura (PUT, POST, DELETE) en los recursos de la suscripción. También puede conocer el estado de la operación y otras propiedades relevantes. El registro de actividad no incluye las operaciones de lectura (GET).

El registro de actividad se diferencia de los [registros de diagnóstico](monitoring-overview-of-diagnostic-logs.md)en que estos últimos son emitidos por un solo recurso. Estos registros proporcionan datos sobre el funcionamiento de ese recurso, en lugar de las operaciones en ese recurso.

Puede recuperar los eventos del registro de actividad mediante Azure Portal, la CLI, los cmdlets de PowerShell y la API de REST de Azure Monitor.

Vea este vídeo [sobre la introducción del registro de actividad](https://channel9.msdn.com/Blogs/Seth-Juarez/Logs-John-Kemnetz).  

## <a name="what-you-can-do-with-the-activity-log"></a>Qué se puede hacer con el registro de actividad
Estas son algunas de las cosas que puede hacer con el registro de actividad:

* Verlo y realizar consultas en él en el **Portal de Azure**.
* Consultarlo por medio de la API de REST, un cmdlet de PowerShell o la CLI.
* [Crear una alerta de correo electrónico o webhook que se desencadene con un evento de registro de actividad.](insights-auditlog-to-webhook-email.md)
* [Guardarlo en una **cuenta de almacenamiento** para archivarlo o inspeccionarlo manualmente](monitoring-archive-activity-log.md). Puede especificar el tiempo de retención (en días) mediante **perfiles de registro**.
* Analizarlo en PowerBI con el [**paquete de contenido de PowerBI**](https://powerbi.microsoft.com/en-us/documentation/powerbi-content-pack-azure-audit-logs/).
* [Transmitirlo al **Centro de eventos**](monitoring-stream-activity-logs-event-hubs.md) para la ingestión en un servicio de terceros o una solución de análisis personalizado como PowerBI.

El espacio de nombres del centro de eventos o la cuenta de almacenamiento no tiene que estar en la misma suscripción que la que emite los registros, siempre que el usuario que configura la configuración tenga acceso RBAC adecuado a ambas suscripciones.

## <a name="export-the-activity-log-with-log-profiles"></a>Exportación del registro de actividad con perfiles de registro
Un **perfil de registro** controla cómo se exporta el registro de actividad. Con un perfil de registro, puede configurar:

* Dónde se debería enviar el registro de actividad (cuenta de almacenamiento o centros de eventos)
* Qué categorías de eventos (Escritura, Eliminación, Acción) se deberían enviar
* Qué regiones (ubicaciones) se deben exportar
* Cuánto tiempo debe retenerse el registro de actividad en una cuenta de almacenamiento: con una retención de cero días los registros se mantienen indefinidamente. De lo contrario, el valor puede ser cualquier número de días comprendido entre 1 y 2147483647. Si se establecen directivas de retención, pero el almacenamiento de registros en una cuenta de almacenamiento está deshabilitado (por ejemplo, si solo se han seleccionado las opciones de Event Hubs u OMS), las directivas de retención no surten ningún efecto. Las directivas de retención se aplican a diario, por lo que al final de un día (UTC) se eliminan los registros del día que quede fuera de la directiva de retención. Por ejemplo, si tuviera una directiva de retención de un día, se eliminarían los registros de anteayer al principio del día de hoy.

Estas opciones se pueden configurar a través de la opción de exportación en la hoja de registro de actividad en el portal. También pueden configurarse mediante programación [con la API de REST de Azure Monitor](https://msdn.microsoft.com/library/azure/dn931927.aspx), los cmdlets de PowerShell o con la CLI. Una suscripción solo puede tener un perfil de registro.

### <a name="configure-log-profiles-using-the-azure-portal"></a>Configuración de perfiles de registro mediante el Portal de Azure
Puede transmitir el registro de actividad a un centro de eventos o almacenarlo en una cuenta de almacenamiento mediante la opción "Exportar" en el Portal de Azure.

1. Vaya a la hoja de **registro de actividad** mediante el menú en el lado izquierdo del portal.
   
    ![Ir al registro de actividad en el portal](./media/monitoring-overview-activity-logs/activity-logs-portal-navigate.png)
2. Haga clic en el botón **Exportar** en la parte superior de la hoja.
   
    ![Botón Exportar en el portal](./media/monitoring-overview-activity-logs/activity-logs-portal-export.png)
3. En la hoja que aparece, puede seleccionar:  
   
   * Las regiones para las que le gustaría exportar eventos
   * La cuenta de almacenamiento en la que desea guardar los eventos
   * El número de días que desea conservar estos eventos en el almacenamiento. Un valor de 0 días conserva los registros para siempre.
   * El espacio de nombres de Service Bus en el que quiere que se cree un centro de eventos para transmitir estos eventos.
     
     ![Exportar en hoja de registro de actividad](./media/monitoring-overview-activity-logs/activity-logs-portal-export-blade.png)
4. Haga clic en **Guardar** para guardar la configuración. La configuración se aplica inmediatamente a la suscripción.

### <a name="configure-log-profiles-using-the-azure-powershell-cmdlets"></a>Configuración de perfiles de registro mediante cmdlets de Azure PowerShell
#### <a name="get-existing-log-profile"></a>Obtención del perfil de registro existente
```
Get-AzureRmLogProfile
```

#### <a name="add-a-log-profile"></a>Incorporación de un perfil de registro
```
Add-AzureRmLogProfile -Name my_log_profile -StorageAccountId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.Storage/storageAccounts/my_storage -serviceBusRuleId /subscriptions/s1/resourceGroups/Default-ServiceBus-EastUS/providers/Microsoft.ServiceBus/namespaces/mytestSB/authorizationrules/RootManageSharedAccessKey -Locations global,westus,eastus -RetentionInDays 90 -Categories Write,Delete,Action
```

| Propiedad | Obligatorio | Description |
| --- | --- | --- |
| Nombre |Sí |Nombre de su perfil de registro. |
| StorageAccountId |No |Identificador de recurso de la cuenta de almacenamiento donde se debe guardar el registro de actividad. |
| serviceBusRuleId |No |Identificador de regla del Bus de servicio para el espacio de nombres del Bus de servicio donde desea que se creen centros de eventos. Es una cadena con este formato: `{service bus resource ID}/authorizationrules/{key name}`. |
| Ubicaciones |Sí |Lista separada por comas de las regiones para las que desea recopilar eventos del registro de actividad. |
| RetentionInDays |Sí |Número de días que deben retenerse los eventos, entre 1 y 2147483647. Con el valor cero, se almacenan los registros indefinidamente. |
| Categorías |No |Lista separada por comas de las categorías de eventos que deben recopilarse. Los valores posibles son Write, Delete y Action. |

#### <a name="remove-a-log-profile"></a>Eliminación de perfil de registro
```
Remove-AzureRmLogProfile -name my_log_profile
```

### <a name="configure-log-profiles-using-the-azure-cross-platform-cli"></a>Configuración de perfiles de registro mediante la CLI multiplataforma de Azure
#### <a name="get-existing-log-profile"></a>Obtención del perfil de registro existente
```
azure insights logprofile list
```
```
azure insights logprofile get --name my_log_profile
```
La propiedad `name` debe ser el nombre del perfil de registro.

#### <a name="add-a-log-profile"></a>Incorporación de un perfil de registro
```
azure insights logprofile add --name my_log_profile --storageId /subscriptions/s1/resourceGroups/insights-integration/providers/Microsoft.Storage/storageAccounts/my_storage --serviceBusRuleId /subscriptions/s1/resourceGroups/Default-ServiceBus-EastUS/providers/Microsoft.ServiceBus/namespaces/mytestSB/authorizationrules/RootManageSharedAccessKey --locations global,westus,eastus,northeurope --retentionInDays 90 –categories Write,Delete,Action
```

| Propiedad | Obligatorio | Description |
| --- | --- | --- |
| name |Sí |Nombre de su perfil de registro. |
| storageId |No |Identificador de recurso de la cuenta de almacenamiento donde se debe guardar el registro de actividad. |
| serviceBusRuleId |No |Identificador de regla del Bus de servicio para el espacio de nombres del Bus de servicio donde desea que se creen centros de eventos. Es una cadena con este formato: `{service bus resource ID}/authorizationrules/{key name}`. |
| Ubicaciones |Sí |Lista separada por comas de las regiones para las que desea recopilar eventos del registro de actividad. |
| RetentionInDays |Sí |Número de días que deben retenerse los eventos, entre 1 y 2147483647. Con el valor cero, se almacenan los registros indefinidamente. |
| Categorías |No |Lista separada por comas de las categorías de eventos que deben recopilarse. Los valores posibles son Write, Delete y Action. |

#### <a name="remove-a-log-profile"></a>Eliminación de perfil de registro
```
azure insights logprofile delete --name my_log_profile
```

## <a name="event-schema"></a>Esquema del evento
Cada evento del registro de actividad tiene un blob JSON similar al siguiente:

```
{
  "value": [ {
    "authorization": {
      "action": "microsoft.support/supporttickets/write",
      "role": "Subscription Admin",
      "scope": "/subscriptions/s1/resourceGroups/MSSupportGroup/providers/microsoft.support/supporttickets/115012112305841"
    },
    "caller": "admin@contoso.com",
    "channels": "Operation",
    "claims": {
      "aud": "https://management.core.windows.net/",
      "iss": "https://sts.windows.net/72f988bf-86f1-41af-91ab-2d7cd011db47/",
      "iat": "1421876371",
      "nbf": "1421876371",
      "exp": "1421880271",
      "ver": "1.0",
      "http://schemas.microsoft.com/identity/claims/tenantid": "1e8d8218-c5e7-4578-9acc-9abbd5d23315 ",
      "http://schemas.microsoft.com/claims/authnmethodsreferences": "pwd",
      "http://schemas.microsoft.com/identity/claims/objectidentifier": "2468adf0-8211-44e3-95xq-85137af64708",
      "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/upn": "admin@contoso.com",
      "puid": "20030000801A118C",
      "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier": "9vckmEGF7zDKk1YzIY8k0t1_EAPaXoeHyPRn6f413zM",
      "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname": "John",
      "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname": "Smith",
      "name": "John Smith",
      "groups": "cacfe77c-e058-4712-83qw-f9b08849fd60,7f71d11d-4c41-4b23-99d2-d32ce7aa621c,31522864-0578-4ea0-9gdc-e66cc564d18c",
      "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name": " admin@contoso.com",
      "appid": "c44b4083-3bq0-49c1-b47d-974e53cbdf3c",
      "appidacr": "2",
      "http://schemas.microsoft.com/identity/claims/scope": "user_impersonation",
      "http://schemas.microsoft.com/claims/authnclassreference": "1"
    },
    "correlationId": "1e121103-0ba6-4300-ac9d-952bb5d0c80f",
    "description": "",
    "eventDataId": "44ade6b4-3813-45e6-ae27-7420a95fa2f8",
    "eventName": {
      "value": "EndRequest",
      "localizedValue": "End request"
    },
    "eventSource": {
      "value": "Microsoft.Resources",
      "localizedValue": "Microsoft Resources"
    },
    "httpRequest": {
      "clientRequestId": "27003b25-91d3-418f-8eb1-29e537dcb249",
      "clientIpAddress": "192.168.35.115",
      "method": "PUT"
    },
    "id": "/subscriptions/s1/resourceGroups/MSSupportGroup/providers/microsoft.support/supporttickets/115012112305841/events/44ade6b4-3813-45e6-ae27-7420a95fa2f8/ticks/635574752669792776",
    "level": "Informational",
    "resourceGroupName": "MSSupportGroup",
    "resourceProviderName": {
      "value": "microsoft.support",
      "localizedValue": "microsoft.support"
    },
    "resourceUri": "/subscriptions/s1/resourceGroups/MSSupportGroup/providers/microsoft.support/supporttickets/115012112305841",
    "operationId": "1e121103-0ba6-4300-ac9d-952bb5d0c80f",
    "operationName": {
      "value": "microsoft.support/supporttickets/write",
      "localizedValue": "microsoft.support/supporttickets/write"
    },
    "properties": {
      "statusCode": "Created"
    },
    "status": {
      "value": "Succeeded",
      "localizedValue": "Succeeded"
    },
    "subStatus": {
      "value": "Created",
      "localizedValue": "Created (HTTP Status Code: 201)"
    },
    "eventTimestamp": "2015-01-21T22:14:26.9792776Z",
    "submissionTimestamp": "2015-01-21T22:14:39.9936304Z",
    "subscriptionId": "s1"
  } ],
"nextLink": "https://management.azure.com/########-####-####-####-############$skiptoken=######"
}
```

| Nombre del elemento | Description |
| --- | --- |
| authorization |Blob de propiedades RBAC del evento. Normalmente incluye las propiedades "action", "role" y "scope". |
| caller |Dirección de correo electrónico del usuario que ha realizado la operación, la notificación de UPN o la notificación de SPN basada en la disponibilidad. |
| canales nueva |Uno de los siguientes valores: "Admin", "Operation" |
| correlationId |Normalmente, un GUID en formato de cadena. Los eventos que comparten correlationId pertenecen a la misma acción general. |
| Description |Descripción de texto estático de un evento. |
| eventDataId |Identificador único de un evento. |
| eventSource |Nombre de la infraestructura o el servicio de Azure que generó el evento. |
| httpRequest |Blob que describe la solicitud HTTP. Normalmente incluye "clientRequestId", "clientIpAddress" y "method" (método HTTP). Por ejemplo, PUT). |
| level |Nivel del evento. Uno de los siguientes valores: "Critical", "Error", "Warning", "Informational" y "Verbose" |
| resourceGroupName |Nombre del grupo de recursos del recurso afectado. |
| resourceProviderName |Nombre del proveedor de recursos del recurso afectado. |
| resourceUri |Identificador de recurso del recurso afectado. |
| operationId |GUID compartido entre los eventos correspondientes a una sola operación. |
| operationName |Nombre de la operación. |
| propiedades |Conjunto de pares `<Key, Value>` (es decir, diccionario) que describen los detalles del evento. |
| status |Cadena que describe el estado de la operación. Algunos valores habituales son: Started, In Progress, Succeeded, Failed, Active y Resolved. |
| subStatus |Por lo general, el código de estado HTTP de la llamada de REST correspondiente, pero también puede incluir otras cadenas que describen un subestado, como estos valores habituales: OK (código de estado HTTP: 200), Created (código de estado HTTP: 201), Accepted (código de estado HTTP: 202), No Content (código de estado HTTP: 204), Bad Request (código de estado HTTP: 400), Not Found (código de estado HTTP: 404), Conflict (código de estado HTTP: 409), Internal Server Error (código de estado HTTP: 500), Service Unavailable (código de estado HTTP: 503) y Gateway Timeout (código de estado HTTP: 504). |
| eventTimestamp |Marca de tiempo de cuándo el servicio de Azure generó el evento que procesó la solicitud correspondiente al evento. |
| submissionTimestamp |Marca de tiempo de cuándo el evento empezó a estar disponible para las consultas. |
| subscriptionId |Identificador de la suscripción de Azure. |
| nextLink |Token de continuación para recuperar el siguiente conjunto de resultados cuando se dividen en varias respuestas. Suele ser necesario cuando hay más de 200 registros. |

## <a name="next-steps"></a>Pasos siguientes
* [Más información sobre el registro de actividad (antes, Registros de auditoría)](../azure-resource-manager/resource-group-audit.md)
* [Transmisión del registro de actividad de Azure a centros de eventos](monitoring-stream-activity-logs-event-hubs.md)




<!--HONumber=Dec16_HO4-->


