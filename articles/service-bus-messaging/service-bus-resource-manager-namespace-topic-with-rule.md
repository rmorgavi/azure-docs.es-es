---
title: "Creación de un espacio de nombres de Service Bus con un tema, una suscripción y una regla mediante una plantilla de Azure Resource Manager | Microsoft Docs"
description: "Creación de un espacio de nombres de Bus de servicio con un tema, una suscripción y una regla mediante una plantilla de Azure Resource Manager"
services: service-bus-messaging
documentationcenter: .net
author: sethmanheim
manager: timlt
editor: 
ms.assetid: 9e0aaf58-0214-4bca-bd00-d29c08f9b1bc
ms.service: service-bus-messaging
ms.devlang: tbd
ms.topic: article
ms.tgt_pltfrm: dotnet
ms.workload: na
ms.date: 10/25/2016
ms.author: sethm;shvija
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: a1a5d9d6fa62bee7b2b463ddc89fe6c99740e03d


---
# <a name="create-a-service-bus-namespace-with-topic-subscription-and-rule-using-an-azure-resource-manager-template"></a>Creación de un espacio de nombres de Bus de servicio con un tema, una suscripción y una regla mediante una plantilla de Azure Resource Manager
En este artículo se muestra cómo utilizar una plantilla de Azure Resource Manager que crea un espacio de nombres de Bus de servicio con un tema, una suscripción y una regla (filtro). Aprenderá a definir los recursos que se implementan y los parámetros que se especifican cuando se ejecuta la implementación. Puede usar esta plantilla para sus propias implementaciones o personalizarla para satisfacer sus necesidades.

Para más información sobre la creación de plantillas, vea [Creación de plantillas de Azure Resource Manager][Creación de plantillas de Azure Resource Manager].

Para más información sobre prácticas y patrones de convenciones de nomenclatura de recursos de Azure, vea [Convenciones de nomenclatura de recursos de Azure][Convenciones de nomenclatura de recursos de Azure].

Vea la plantilla completa en [espacio de nombres de Bus de servicio con un tema, suscripción y una regla][espacio de nombres de Bus de servicio con un tema, suscripción y una regla].

> [!NOTE]
> Las siguientes plantillas de Azure Resource Manager están disponibles para su descarga e implementación.
> 
> * [Creación de un espacio de nombres de Bus de servicio con regla de autorización y cola](service-bus-resource-manager-namespace-auth-rule.md)
> * [Creación de un espacio de nombres de Bus de servicio con cola](service-bus-resource-manager-namespace-queue.md)
> * [Creación de un espacio de nombres de bus de servicio](service-bus-resource-manager-namespace.md)
> * [Creación de un espacio de nombres de Bus de servicio con un tema y una suscripción](service-bus-resource-manager-namespace-topic.md)
> 
> Para buscar las últimas plantillas, visite la galería de [Plantillas de inicio rápido de Azure][Plantillas de inicio rápido de Azure] y busque Service Bus.
> 
> 

## <a name="what-will-you-deploy"></a>¿Qué va a implementar?
Con esta plantilla, implementará un espacio de nombres de Bus de servicio con un tema, una suscripción y una regla (filtro).

Los [temas y suscripciones de Service Bus](service-bus-queues-topics-subscriptions.md#topics-and-subscriptions) proporcionan una o varias formas de comunicación en un patrón *publicación/suscripción*. Al usar temas y suscripciones, los componentes de una aplicación distribuida no se comunican directamente entre ellos, sino que intercambian mensajes a través de un tema que actúa como un intermediario. Una suscripción a un tema es similar a una cola virtual que recibe copias de mensajes que se enviaron al tema. Un filtro de una suscripción permite especificar qué mensajes enviados a un tema deben aparecer dentro de una suscripción a un tema determinado.

## <a name="what-are-rules-filters"></a>¿Cuáles son las reglas (filtros)?
En muchos escenarios, los mensajes que tienen características específicas deben procesarse de maneras diferentes. Para permitirlo, puede configurar suscripciones para buscar los mensajes que tengan las propiedades deseadas y, después, realizar determinadas modificaciones en dichas propiedades. Mientras que las suscripciones del Service Bus ven todos los mensajes enviados al tema, solo se puede copiar un subconjunto de dichos mensajes en la cola de suscripción virtual. Esto se consigue mediante los filtros de suscripción. Para más información sobre las reglas (filtros), vea [Colas, temas y suscripciones de Service Bus][Colas, temas y suscripciones de Service Bus].

Para ejecutar automáticamente la implementación, haga clic en el botón siguiente:

[![Implementación en Azure](./media/service-bus-resource-manager-namespace-topic/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-servicebus-create-topic-subscription-rule%2Fazuredeploy.json)

## <a name="parameters"></a>Parámetros
Con Azure Resource Manager, se definen los parámetros de los valores que desea especificar al implementar la plantilla. La plantilla incluye una sección denominada `Parameters` que contiene todos los valores de los parámetros. Debe definir un parámetro para esos valores que variarán según el proyecto que vaya a implementar o según el entorno en el que vaya a realizar la implementación. No defina parámetros para valores que siempre permanezcan igual. Cada valor de parámetro se usa en la plantilla para definir los recursos que se implementan.

La plantilla define los parámetros siguientes:

### <a name="servicebusnamespacename"></a>serviceBusNamespaceName
El nombre del espacio de nombres de Bus de servicio que crear.

```
"serviceBusNamespaceName": {
"type": "string"
}
```

### <a name="servicebustopicname"></a>serviceBusTopicName
El nombre del tema creado en el espacio de nombres de Bus de servicio.

```
"serviceBusTopicName": {
"type": "string"
}
```

### <a name="servicebussubscriptionname"></a>serviceBusSubscriptionName
El nombre de la suscripción creada en el espacio de nombres de Bus de servicio.

```
"serviceBusSubscriptionName": {
"type": "string"
}
```
### <a name="servicebusrulename"></a>serviceBusRuleName
El nombre de la regla (filtro) creada en el espacio de nombres de Bus de servicio.

```
   "serviceBusRuleName": {
   "type": "string",
  }
```
### <a name="servicebusapiversion"></a>serviceBusApiVersion
La versión de la API de Bus de servicio de la plantilla.

```
"serviceBusApiVersion": {
"type": "string"
}
```
## <a name="resources-to-deploy"></a>Recursos para implementar
Crea un espacio de nombres de Bus de servicio estándar de tipo **Mensajería**con tema, suscripción y reglas.

```
 "resources": [{
        "apiVersion": "[variables('sbVersion')]",
        "name": "[parameters('serviceBusNamespaceName')]",
        "type": "Microsoft.ServiceBus/Namespaces",
        "location": "[variables('location')]",
        "sku": {
            "name": "Standard",
            "tier": "Standard"
        },
        "resources": [{
            "apiVersion": "[variables('sbVersion')]",
            "name": "[parameters('serviceBusTopicName')]",
            "type": "Topics",
            "dependsOn": [
                "[concat('Microsoft.ServiceBus/namespaces/', parameters('serviceBusNamespaceName'))]"
            ],
            "properties": {
                "path": "[parameters('serviceBusTopicName')]"
            },
            "resources": [{
                "apiVersion": "[variables('sbVersion')]",
                "name": "[parameters('serviceBusSubscriptionName')]",
                "type": "Subscriptions",
                "dependsOn": [
                    "[parameters('serviceBusTopicName')]"
                ],
                "properties": {},
                "resources": [{
                    "apiVersion": "[variables('sbVersion')]",
                    "name": "[parameters('serviceBusRuleName')]",
                    "type": "Rules",
                    "dependsOn": [
                        "[parameters('serviceBusSubscriptionName')]"
                    ],
                    "properties": {
                        "filter": {
                            "sqlExpression": "StoreName = 'Store1'"
                        },
                        "action": {
                            "sqlExpression": "set FilterTag = 'true'"
                        }
                    }
                }]
            }]
        }]
    }]
```

## <a name="commands-to-run-deployment"></a>Comandos para ejecutar la implementación
[!INCLUDE [app-service-deploy-commands](../../includes/app-service-deploy-commands.md)]

## <a name="powershell"></a>PowerShell
```
New-AzureResourceGroupDeployment -Name \<deployment-name\> -ResourceGroupName \<resource-group-name\> -TemplateUri <https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/201-servicebus-create-topic-subscription-rule/azuredeploy.json>
```

## <a name="azure-cli"></a>Azure CLI
```
azure config mode arm

azure group deployment create \<my-resource-group\> \<my-deployment-name\> --template-uri <https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/201-servicebus-create-topic-subscription-rule/azuredeploy.json>
```

## <a name="next-steps"></a>Pasos siguientes
Ahora que ha creado e implementado recursos con Azure Resource Manager, estos artículos le enseñarán como administrarlos:

* [Administración de Bus de servicio de Azure con Automatización de Azure](service-bus-automation-manage.md)
* [Administración de Service Bus con PowerShell](service-bus-powershell-how-to-provision.md)
* [Administración de recursos de Bus de servicio con el Explorador de Bus de servicio](https://code.msdn.microsoft.com/Service-Bus-Explorer-f2abca5a)

[Creación de plantillas de Azure Resource Manager]: ../resource-group-authoring-templates.md
[Plantillas de inicio rápido de Azure]: https://azure.microsoft.com/documentation/templates/?term=service+bus
[Obtenga más información sobre los temas y suscripciones de Bus de servicio]: service-bus-queues-topics-subscriptions.md
[Uso de Azure PowerShell con Azure Resource Manager]: ../powershell-azure-resource-manager.md
[Uso de la CLI de Azure para Mac, Linux y Windows mediante la administración de recursos de Azure]: ../xplat-cli-azure-resource-manager.md
[Convenciones de nomenclatura de recursos de Azure]: https://azure.microsoft.com/en-us/documentation/articles/guidance-naming-conventions/
[espacio de nombres de Bus de servicio con un tema, suscripción y una regla]: https://github.com/Azure/azure-quickstart-templates/blob/master/201-servicebus-create-topic-subscription-rule/
[Colas, temas y suscripciones de Service Bus]:service-bus-queues-topics-subscriptions.md




<!--HONumber=Nov16_HO3-->


