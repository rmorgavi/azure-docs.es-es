---
title: "Límites de solicitudes de Azure Resource Manager| Microsoft Docs"
description: "En este artículo se describe cómo usar la limitación con las solicitudes de Azure Resource Manager cuando se han alcanzado los límites de suscripción."
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: e1047233-b8e4-4232-8919-3268d93a3824
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/07/2016
ms.author: tomfitz
translationtype: Human Translation
ms.sourcegitcommit: f6e684b08ed481cdf84faf2b8426da72f98fc58c
ms.openlocfilehash: 39678df7c52781d2f7e8fec49d97c96bee58ce8c


---
# <a name="throttling-resource-manager-requests"></a>Limitación de solicitudes de Resource Manager
Para cada suscripción e inquilino, los límites de Resource Manager leen 15 000 solicitudes y escriben 1200 cada hora. Si la aplicación o el script alcanza estos límites, debe limitar las solicitudes. En este tema se muestra cómo determinar las solicitudes restantes que quedan antes de alcanzar el límite y cómo responder cuando se ha alcanzado dicho límite.

Cuando se alcanza este límite, recibirá el código de estado HTTP **429 Demasiadas solicitudes**.

El número de solicitudes se limita a su suscripción o inquilino. Si tiene varias aplicaciones simultáneas realizando solicitudes en su suscripción, estas se agregan conjuntamente para determinar el número de solicitudes restantes.

Las solicitudes del ámbito de la suscripción son aquellas en las que se pasan el identificador de suscripción, por ejemplo, al recuperar el recurso de la suscripción. Las solicitudes del ámbito del inquilino no incluyen el identificador de suscripción, por ejemplo, al recuperar ubicaciones válidas de Azure.

## <a name="remaining-requests"></a>Solicitudes restantes
Puede determinar el número de solicitudes restantes examinando los encabezados de respuesta. Cada solicitud incluye valores para el número de las demás solicitudes de lectura y escritura. En la tabla siguiente se describen los encabezados de respuesta que puede examinar para esos valores:

| Encabezado de respuesta | Descripción |
| --- | --- |
| x-ms-ratelimit-Remaining-Subscription-Reads |Lecturas restantes del ámbito de la suscripción |
| x-ms-ratelimit-Remaining-Subscription-Writes |Escrituras restantes del ámbito de la suscripción |
| x-ms-ratelimit-Remaining-tenant-Reads |Lecturas restantes del ámbito del inquilino |
| x-ms-ratelimit-Remaining-tenant-Writes |Escrituras restantes del ámbito del inquilino |
| x-ms-ratelimit-Remaining-Subscription-Resource-Requests |Solicitudes de tipos de recursos restantes del ámbito de la suscripción<br /><br />Este valor de encabezado solo se devuelve si un servicio ha invalidado el límite predeterminado. Resource Manager agrega este valor en lugar de las lecturas o escrituras de la suscripción. |
| x-ms-ratelimit-Remaining-Subscription-Resource-Entities-Read |Solicitudes de colección de tipos de recursos restantes del ámbito de la suscripción<br /><br />Este valor de encabezado solo se devuelve si un servicio ha invalidado el límite predeterminado. Este valor proporciona el número de solicitudes de colección restantes (recursos de lista). |
| x-ms-ratelimit-Remaining-tenant-Resource-Requests |Solicitudes de tipos de recurso restantes del ámbito del inquilino<br /><br />Este encabezado solo se agrega para las solicitudes en el nivel del inquilino, y solo si un servicio ha invalidado el límite predeterminado. Resource Manager agrega este valor en lugar de las lecturas o escrituras del inquilino. |
| x-ms-ratelimit-Remaining-tenant-Resource-Entities-Read |Solicitudes de colección de tipos de recursos restantes del ámbito del inquilino<br /><br />Este encabezado solo se agrega para las solicitudes en el nivel del inquilino, y solo si un servicio ha invalidado el límite predeterminado. |

## <a name="retrieving-the-header-values"></a>Recuperación de los valores de encabezado
El proceso de recuperación de estos valores de encabezado del código o el script es igual que el de cualquier valor de encabezado. 

Por ejemplo, en **C#**, recupere el valor del encabezado de un objeto **HttpWebResponse** denominado "**response**"con el código siguiente:

    response.Headers.GetValues("x-ms-ratelimit-remaining-subscription-reads").GetValue(0)

En **PowerShell**, recupere el valor del encabezado de una operación Invoke-WebRequest.

    $r = Invoke-WebRequest -Uri https://management.azure.com/subscriptions/{guid}/resourcegroups?api-version=2016-09-01 -Method GET -Headers $authHeaders
    $r.Headers["x-ms-ratelimit-remaining-subscription-reads"]

O bien, si quiere ver las solicitudes restantes de depuración, puede proporcionar el parámetro **-Debug** en el cmdlet **PowerShell**.

    Get-AzureRmResourceGroup -Debug

Esta operación devuelve una gran cantidad de información, incluido el valor de la respuesta siguiente:

    ...
    DEBUG: ============================ HTTP RESPONSE ============================

    Status Code:
    OK

    Headers:
    Pragma                        : no-cache
    x-ms-ratelimit-remaining-subscription-reads: 14999
    ...

En la **CLI de Azure**, recupere el valor del encabezado mediante la opción más detallada.

    azure group list -vv --json

Esta operación devuelve una gran cantidad de información, incluido el valor del objeto siguiente:

    ...
    silly: returnObject
    {
      "statusCode": 200,
      "header": {
        "cache-control": "no-cache",
        "pragma": "no-cache",
        "content-type": "application/json; charset=utf-8",
        "expires": "-1",
        "x-ms-ratelimit-remaining-subscription-reads": "14998",
        ...

## <a name="waiting-before-sending-next-request"></a>Espera antes de enviar la solicitud siguiente
Cuando alcance el límite de solicitudes, Azure Resource Manager devuelve el código de estado HTTP **429** y un valor **Retry-After** del encabezado. El valor **Retry-After** valor especifica el número de segundos que debe esperar (o estar en estado de suspensión) la aplicación antes de enviar la solicitud siguiente. Si envía una solicitud antes de que haya transcurrido el valor de reintento, no se procesa la solicitud y se devuelve un nuevo valor de reintento.




<!--HONumber=Nov16_HO3-->


