---
title: "Solución de errores comunes de implementación de Azure | Microsoft Docs"
description: "Describe cómo solucionar errores comunes al implementar recursos en Azure con Azure Resource Manager."
services: azure-resource-manager
documentationcenter: 
tags: top-support-issue
author: tfitzmac
manager: timlt
editor: tysonn
keywords: "error de implementación, implementación de Azure, implementar en Azure"
ms.assetid: c002a9be-4de5-4963-bd14-b54aa3d8fa59
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/12/2016
ms.author: tomfitz
translationtype: Human Translation
ms.sourcegitcommit: e2e59da29897a40f0fe538d6fe8063ae5edbaccd
ms.openlocfilehash: 4dd4e54f3e2514570ff5cbffcb926f274491cb65


---
# <a name="troubleshoot-common-azure-deployment-errors-with-azure-resource-manager"></a>Solución de errores comunes de implementación de Azure con Azure Resource Manager
En este tema se describe cómo resolver algunos errores comunes con los que puede encontrarse al realizar una implementación de Azure.

## <a name="two-types-of-errors"></a>Dos tipos de errores
Hay dos tipos de errores que puede recibir:

* Errores de validación
* Errores de implementación

La siguiente imagen muestra el registro de actividad de una suscripción. Hay tres operaciones que se produjeron en dos implementaciones. En la primera implementación, la plantilla pasó la validación, pero se produjo un error al crear los recursos (paso de **escritura implementaciones**). En la segunda implementación, la plantilla de un error de validación y no continúa con el paso de **escritura de implementaciones**.

![Visualización del código de error](./media/resource-manager-common-deployment-errors/show-activity-log.png)

Errores de validación que provengan de escenarios que se pueden determinar previamente para que se produzca un problema. Entre ellos se encuentran los errores de sintaxis en la plantilla o tratar de implementar los recursos que se superen las cuotas de suscripción. Errores de implementación que provengan de condiciones que se producen durante el proceso de implementación. Por ejemplo, un error de implementación podría surgir por tratar de obtener acceso a un recurso que se va a implementar en paralelo.

Los dos tipos de errores devuelven un código de error que utiliza para solucionar problemas de implementación. Ambos aparecen en el registro de actividad. Sin embargo, los errores de validación no aparecen en el historial de implementación porque la implementación nunca se inicia.


## <a name="error-codes"></a>Códigos de error
Los errores de implementación devuelven el código **DeploymentFailed**. Sin embargo, este código de error es un error de implementación general. El código de error que realmente lo ayuda a resolver el problema suele estar a un nivel por debajo de ese error. La siguiente imagen muestra el código de error **RequestDisallowedByPolicy** que se encuentra en el error de implementación.

![Visualización del código de error](./media/resource-manager-common-deployment-errors/error-code.png)

En este tema se describen los códigos de error siguientes:

* [AccountNameInvalid](#accountnameinvalid)
* [Error de autorización](#authorization-failed)
* [BadRequest](#badrequest)
* [InvalidContentLink](#invalidcontentlink)
* [InvalidTemplate](#invalidtemplate)
* [MissingSubscriptionRegistration](#noregisteredproviderfound)
* [NotFound](#notfound)
* [NoRegisteredProviderFound](#noregisteredproviderfound)
* [OperationNotAllowed](#quotaexceeded)
* [ParentResourceNotFound](#parentresourcenotfound)
* [QuotaExceeded](#quotaexceeded)
* [RequestDisallowedByPolicy](#requestdisallowedbypolicy)
* [ResourceNotFound](#notfound)
* [SkuNotAvailable](#skunotavailable)
* [StorageAccountAlreadyExists](#storagenamenotunique)
* [StorageAccountAlreadyTaken](#storagenamenotunique)

### <a name="invalidtemplate"></a>InvalidTemplate
Este error puede tener distintos orígenes.

- Error de sintaxis

   Si recibe un mensaje de error que indica que la plantilla no superó la validación, puede que esta tenga un problema de sintaxis.

       Code=InvalidTemplate
       Message=Deployment template validation failed

   Es fácil cometer este error porque las expresiones de plantilla pueden ser complejas. Por ejemplo, la siguiente asignación de nombre de una cuenta de almacenamiento contiene un conjunto de corchetes, tres funciones, tres conjuntos de paréntesis, un conjunto de comillas simples y una propiedad:

       "name": "[concat('storage', uniqueString(resourceGroup().id))]",

   Si no proporciona toda la sintaxis de coincidencia, la plantilla generará un valor que será diferente del que esperaba.

   Si recibe este tipo de error, revise cuidadosamente la sintaxis de las expresiones. Considere la posibilidad de utilizar un editor JSON como [Visual Studio](vs-azure-tools-resource-groups-deployment-projects-create-deploy.md) o [Visual Studio Code](resource-manager-vs-code.md) que puede avisarlo de los errores de sintaxis.

- Longitudes de segmentos incorrectas

   Cuando el nombre del recurso no tiene el formato correcto, se produce otro error de plantilla no válida.

       Code=InvalidTemplate
       Message=Deployment template validation failed: 'The template resource {resource-name}'
       for type {resource-type} has incorrect segment lengths.

   Un recurso de nivel de raíz debe tener un segmento menos en el nombre que en el tipo de recurso. Cada segmento se distingue por una barra diagonal. En el ejemplo siguiente, el tipo tiene&2; segmentos y el nombre,&1;, por lo que es un **nombre válido**.

       {
         "type": "Microsoft.Web/serverfarms",
         "name": "myHostingPlanName",
         ...
       }

   Pero el ejemplo siguiente **no es un nombre válido** porque el nombre tiene el mismo número de segmentos que el tipo.

       {
         "type": "Microsoft.Web/serverfarms",
         "name": "appPlan/myHostingPlanName",
         ...
       }

   Para los recursos secundarios, el tipo y el nombre deben tener el mismo número de segmentos. Este número de segmentos tiene sentido, ya que el tipo y el nombre completo del elemento secundario incluyen el tipo y el nombre del elemento primario. Por lo tanto, el nombre completo sigue teniendo un segmento menos que el tipo completo.

       "resources": [
           {
               "type": "Microsoft.KeyVault/vaults",
               "name": "contosokeyvault",
               ...
               "resources": [
                   {
                       "type": "secrets",
                       "name": "appPassword",
                       ...
                   }
               ]
           }
       ]

   Obtener los segmentos correctos puede resultar complicado con los tipos de Resource Manager que se aplican en los proveedores de recursos. Por ejemplo, para aplicar un bloqueo de recurso a un sitio web, el tipo debe tener&4; segmentos. Por lo tanto, el nombre tiene&3; segmentos:

       {
           "type": "Microsoft.Web/sites/providers/locks",
           "name": "[concat(variables('siteName'),'/Microsoft.Authorization/MySiteLock')]",
           ...
       }

- Copy index is not expected (Índice de copia no esperado)

   Este error **InvalidTemplate** se produce cuando se ha aplicado el elemento **copy** a una parte de la plantilla que no lo admite. Solo se puede aplicar el elemento copy a un tipo de recurso. No se puede aplicar el elemento copy a una propiedad de un tipo de recurso. Por ejemplo, puede aplicar el elemento copy a una máquina virtual, pero no puede aplicarlo a los discos del sistema operativo de una máquina virtual. En algunos casos, puede convertir un recurso secundario en un recurso primario para crear un bucle del elemento copy. Para más información sobre cómo usar este elemento, consulte [Creación de varias instancias de recursos en Azure Resource Manager](resource-group-create-multiple.md).

- El parámetro no es válido

   Si la plantilla especifica los valores permitidos de un parámetro y proporciona un valor que no es uno de esos, recibirá un mensaje similar al siguiente error:

       Code=InvalidTemplate;
       Message=Deployment template validation failed: 'The provided value {parameter value}
       for the template parameter {parameter name} is not valid. The parameter value is not
       part of the allowed values

   Compruebe los valores permitidos de la plantilla y proporcione uno durante la implementación.

<a id="notfound" />
### <a name="notfound-and-resourcenotfound"></a>NotFound y ResourceNotFound
Cuando la plantilla incluye el nombre de un recurso que no se puede resolver, recibirá un error similar al siguiente:

    Code=NotFound;
    Message=Cannot find ServerFarm with name exampleplan.

Si está tratando de implementar el recurso que falta en la plantilla, compruebe si tiene que agregar una dependencia. Cuando es posible, Resource Manager optimiza la implementación mediante la creación de recursos en paralelo. Si un recurso se debe implementar después de otro recurso, debe usar el elemento **dependsOn** en la plantilla para crear una dependencia en el otro recurso. Por ejemplo, al implementar una aplicación web, debe existir el plan de Servicio de aplicaciones. Si no ha especificado que la aplicación web dependa del plan de App Service, Resource Manager creará ambos recursos al mismo tiempo. Recibirá un error que indica que no se encuentra el recurso del plan de App Service, porque aún no existe cuando se trata de establecer una propiedad en la aplicación web. Este error se puede evitar estableciendo la dependencia en la aplicación web.

    {
      "apiVersion": "2015-08-01",
      "type": "Microsoft.Web/sites",
      ...
      "dependsOn": [
        "[variables('hostingPlanName')]"
      ],
      ...
    }

También verá este error cuando el recurso se encuentra en otro grupo de recursos que donde se está implementando. En ese caso, use la [función resourceId](resource-group-template-functions.md#resourceid) para obtener el nombre completo del recurso.

    "properties": {
        "name": "[parameters('siteName')]",
        "serverFarmId": "[resourceId('plangroup', 'Microsoft.Web/serverfarms', parameters('hostingPlanName'))]"
    }

Si trata de usar las funciones [reference](resource-group-template-functions.md#reference) o [listKeys](resource-group-template-functions.md#listkeys) con un recurso que no se puede resolver, recibirá el error siguiente:

    Code=ResourceNotFound;
    Message=The Resource 'Microsoft.Storage/storageAccounts/{storage name}' under resource
    group {resource group name} was not found.

Busque una expresión que incluya la función **reference**. Compruebe que los valores de parámetro son correctos.

### <a name="parentresourcenotfound"></a>ParentResourceNotFound

Cuando un recurso es un elemento primario de otro recurso, el primario debe existir antes de crear el secundario. Si aún no existe, recibirá el error siguiente:

     Code=ParentResourceNotFound;
     Message=Can not perform requested operation on nested resource. Parent resource 'exampleserver' not found."

El nombre del recurso secundario incluye el nombre primario. Por ejemplo, se podría definir una base de datos SQL como:

    {
      "type": "Microsoft.Sql/servers/databases",
      "name": "[concat(variables('databaseServerName'), '/', parameters('databaseName'))]",
      ...

Sin embargo, si no especifica una dependencia del recurso primario, el secundario puede implementarse antes del primario. Para resolver este error, incluya una dependencia.

    "dependsOn": [
        "[variables('databaseServerName')]"
    ]

<a id="storagenamenotunique" />
### <a name="storageaccountalreadyexists-and-storageaccountalreadytaken"></a>StorageAccountAlreadyExists y StorageAccountAlreadyTaken
Para las cuentas de almacenamiento, debe proporcionar un nombre al recurso que sea único en Azure. Si no lo hace, recibirá un error como el siguiente:

    Code=StorageAccountAlreadyTaken
    Message=The storage account named mystorage is already taken.

Puede crear un nombre único concatenando la convención de nomenclatura con el resultado de la función [uniqueString](resource-group-template-functions.md#uniquestring) .

    "name": "[concat('contosostorage', uniqueString(resourceGroup().id))]",
    "type": "Microsoft.Storage/storageAccounts",

Si implementa una cuenta de almacenamiento con el mismo nombre que una que ya hay en la suscripción, pero proporciona una ubicación diferente, recibirá un error que indica que la cuenta de almacenamiento ya se encuentra en otra ubicación. Elimine la cuenta de almacenamiento que ya existe o proporcione la misma ubicación que la de la cuenta de almacenamiento.

### <a name="accountnameinvalid"></a>AccountNameInvalid
Verá el error **AccountNameInvalid** al tratar de proporcionar a una cuenta de almacenamiento un nombre que incluya caracteres prohibidos. Los nombres de cuentas de almacenamiento deben tener entre 3 y 24 caracteres, y usar solo números y letras minúsculas.

### <a name="badrequest"></a>BadRequest

Puede encontrar un estado BadRequest al proporcionar un valor no válido para una propiedad. Por ejemplo, si proporciona un valor incorrecto de SKU para una cuenta de almacenamiento, se produce un error en la implementación. 

<a id="noregisteredproviderfound" />
### <a name="noregisteredproviderfound-and-missingsubscriptionregistration"></a>NoRegisteredProviderFound y MissingSubscriptionRegistration
Al implementar recursos, puede recibir el siguiente código y mensaje de error:

    Code: NoRegisteredProviderFound
    Message: No registered resource provider found for location {ocation}
    and API version {api-version} for type {resource-type}.

O bien, puede recibir un mensaje similar que indica:

    Code: MissingSubscriptionRegistration
    Message: The subscription is not registered to use namespace {resource-provider-namespace}

Recibirá estos errores por uno de estos tres motivos:

1. El proveedor de recursos no se ha registrado para la suscripción
2. No se permite esta versión de API para el tipo de recurso
3. No se permite esta ubicación para el tipo de recurso

El mensaje de error debería proporcionarle sugerencias con respecto a las versiones de API y a las ubicaciones admitidas. Puede cambiar la plantilla a uno de los valores sugeridos. La mayoría de los proveedores se registran automáticamente mediante el Portal de Azure o la interfaz de línea de comandos que esté utilizando; pero no ocurre con todos. Si no ha utilizado un proveedor de recursos determinado antes, debe registrar dicho proveedor. Puede obtener más información sobre los proveedores de recursos a través de PowerShell o la CLI de Azure.

**Portal**

Puede ver el estado de registro y registrar un espacio de nombres de proveedor de recursos a través del portal.

1. Para la suscripción, seleccione **Proveedores de recursos**.

   ![seleccionar proveedores de recursos](./media/resource-manager-common-deployment-errors/select-resource-provider.png)

2. Examine la lista de proveedores de recursos y, si es necesario, seleccione el vínculo **Registrar** para registrar el proveedor de recursos del tipo que está intentando implementar.

   ![Enumeración de proveedores de recursos](./media/resource-manager-common-deployment-errors/list-resource-providers.png)

**PowerShell**

Para ver el estado de su registro, use **Get-AzureRmResourceProvider**.

    Get-AzureRmResourceProvider -ListAvailable

Para registrar un proveedor, use **Register-AzureRmResourceProvider** e indique el nombre del proveedor de recursos que desea registrar.

    Register-AzureRmResourceProvider -ProviderNamespace Microsoft.Cdn

Para conocer las ubicaciones admitidas para un tipo determinado de recurso, use:

    ((Get-AzureRmResourceProvider -ProviderNamespace Microsoft.Web).ResourceTypes | Where-Object ResourceTypeName -eq sites).Locations

Para conocer las versiones de API admitidas para un tipo determinado de recurso, use:

    ((Get-AzureRmResourceProvider -ProviderNamespace Microsoft.Web).ResourceTypes | Where-Object ResourceTypeName -eq sites).ApiVersions

**CLI de Azure**

Para ver si el proveedor está registrado, utilice el comando `azure provider list` .

    azure provider list

Para registrar un proveedor de recursos, use el comando `azure provider register` y especifique el *espacio de nombres* que desea registrar.

    azure provider register Microsoft.Cdn

Para ver las ubicaciones y las versiones de API admitidas por un proveedor de recursos, use:

    azure provider show -n Microsoft.Compute --json > compute.json

<a id="quotaexceeded" />
### <a name="quotaexceeded-and-operationnotallowed"></a>QuotaExceeded y OperationNotAllowed
Podría tener problemas cuando una implementación supera una cuota, lo que podría suceder por grupo de recursos, suscripciones, cuentas y otros ámbitos. Por ejemplo, la suscripción puede configurarse para limitar el número de núcleos para una región. Si trata de implementar una máquina virtual con más núcleos que los permitidos, recibirá un error que indica que se ha superado la cuota.
Para obtener información completa de las cuotas, consulte [Límites, cuotas y restricciones de suscripción y servicios de Microsoft Azure](../azure-subscription-service-limits.md).

Para examinar las cuotas de núcleos de su suscripción, puede usar el comando `azure vm list-usage` en la CLI de Azure. En el siguiente ejemplo se muestra que la cuota de núcleos para una cuenta de evaluación gratuita es 4:

    azure vm list-usage

Que devuelve:

    info:    Executing command vm list-usage
    Location: westus
    data:    Name   Unit   CurrentValue  Limit
    data:    -----  -----  ------------  -----
    data:    Cores  Count  0             4
    info:    vm list-usage command OK

Si implementa una plantilla que crea más de&4; núcleos en la región oeste de EE. UU., obtendrá un error de implementación similar al siguiente:

    Code=OperationNotAllowed
    Message=Operation results in exceeding quota limits of Core.
    Maximum allowed: 4, Current in use: 4, Additional requested: 2.

O bien en PowerShell, puede emplear el cmdlet **Get-AzureRmVMUsage** .

    Get-AzureRmVMUsage

Que devuelve:

    ...
    CurrentValue : 0
    Limit        : 4
    Name         : {
                     "value": "cores",
                     "localizedValue": "Total Regional Cores"
                   }
    Unit         : null
    ...

En estos casos, debe ir al portal y archivar un problema de soporte técnico para aumentar su cuota para la región en la que desea realizar la implementación.

> [!NOTE]
> Recuerde que para los grupos de recursos, la cuota para cada región individual, no para toda la suscripción. Si necesita implementar 30 núcleos en el oeste de Estados Unidos, debe pedir 30 núcleos de administrador de recursos en el oeste de Estados Unidos. Si necesita implementar 30 núcleos en cualquiera de las regiones para las que tiene acceso, debe pedir 30 núcleos de Resource Manager en todas las regiones.
>
>

### <a name="invalidcontentlink"></a>InvalidContentLink
Si recibe el mensaje de error:

    Code=InvalidContentLink
    Message=Unable to download deployment content from ...

Probablemente ha tratado de agregar un vínculo a una plantilla anidada que no está disponible. Compruebe el URI proporcionado para la plantilla anidada. Si la plantilla se encuentra en una cuenta de almacenamiento, asegúrese de que puede accederse al URI. Debe transmitir un token SAS. Para más información, consulte [Uso de plantillas vinculadas con el Administrador de recursos de Azure](resource-group-linked-templates.md).

### <a name="requestdisallowedbypolicy"></a>RequestDisallowedByPolicy
Recibirá este error cuando la suscripción incluye una directiva de recursos que impide una acción que está tratando de realizar durante la implementación. En el mensaje de error, busque el identificador de directiva.

    Policy identifier(s): '/subscriptions/{guid}/providers/Microsoft.Authorization/policyDefinitions/regionPolicyDefinition'

En **PowerShell**, proporcione ese identificador de directiva como el parámetro **Id** para recuperar los detalles de la directiva que bloqueó la implementación.

    (Get-AzureRmPolicyAssignment -Id "/subscriptions/{guid}/providers/Microsoft.Authorization/policyDefinitions/regionPolicyDefinition").Properties.policyRule | ConvertTo-Json

En la **CLI de Azure**, proporcione el nombre de la definición de directiva:

    azure policy definition show regionPolicyDefinition --json

Para más información sobre las directivas, consulte [Uso de directivas para administrar los recursos y controlar el acceso](resource-manager-policy.md).

### <a name="authorization-failed"></a>Error de autorización
Puede recibir un error durante la implementación porque la cuenta o la entidad de servicio que intenta implementar los recursos no tiene acceso para realizar esas acciones. Azure Active Directory permite al usuario o al administrador controlar qué identidades pueden acceder a qué recursos con un alto grado de precisión. Por ejemplo, si su cuenta se asigna al rol Lector, no podrá crear recursos. En ese caso, debería ver un mensaje de error que indica que hubo un error de autorización.

Para más información sobre el control de acceso basado en roles, consulte [Control de acceso basado en roles de Azure](../active-directory/role-based-access-control-configure.md).

### <a name="skunotavailable"></a>SkuNotAvailable

Al implementar un recurso (normalmente una máquina virtual), puede recibir el siguiente código y mensaje de error:

```
Code: SkuNotAvailable
Message: The requested tier for resource '<resource>' is currently not available in location '<location>' for subscription '<subscriptionID>'. Please try another tier or deploy to a different location.
```

Recibirá este error si la SKU del recurso que ha seleccionado (como, por ejemplo, el tamaño de máquina virtual) no está disponible para la ubicación seleccionada. Tiene dos opciones para resolver este problema:

- Inicie sesión en el portal y agregue un nuevo recurso a través de la interfaz de usuario. A medida que establezca los valores, verá las SKU disponibles para ese recurso. No tiene que completar la implementación.

    ![sku disponibles](./media/resource-manager-common-deployment-errors/view-sku.png)

- Si no puede encontrar una SKU adecuada en esa región ni en ninguna región alternativa que satisfaga las necesidades de su negocio, póngase en contacto con el [soporte técnico de Azure](https://portal.azure.com/#create/Microsoft.Support).

## <a name="troubleshooting-tricks-and-tips"></a>Sugerencias y trucos para solucionar problemas

### <a name="enable-debug-logging"></a>Habilitación del registro de depuración
Puede detectar información valiosa sobre cómo se procesa la implementación registrando la solicitud, la respuesta o ambas.

- PowerShell

   En PowerShell, establezca el parámetro **DeploymentDebugLogLevel** en Todos, ResponseContent o RequestContent.

       New-AzureRmResourceGroupDeployment -ResourceGroupName examplegroup -TemplateFile c:\Azure\Templates\storage.json -DeploymentDebugLogLevel All

   Examine el contenido de la solicitud con el siguiente cmdlet:

       (Get-AzureRmResourceGroupDeploymentOperation -DeploymentName storageonly -ResourceGroupName startgroup).Properties.request | ConvertTo-Json

   O bien, la respuesta de contenido con:

       (Get-AzureRmResourceGroupDeploymentOperation -DeploymentName storageonly -ResourceGroupName startgroup).Properties.response | ConvertTo-Json

   Esta información puede ayudarlo a determinar si un valor en la plantilla se está estableciendo de forma incorrecta.

- Azure CLI

   En la CLI de Azure, establezca el parámetro **--debug-setting** en Todos, ResponseContent o RequestContent.

       azure group deployment create --debug-setting All -f c:\Azure\Templates\storage.json -g examplegroup -n ExampleDeployment

   Examine el contenido de la respuesta y la solicitud registrada con lo siguiente:

       azure group deployment operation list --resource-group examplegroup --name ExampleDeployment --json

   Esta información puede ayudarlo a determinar si un valor en la plantilla se está estableciendo de forma incorrecta.

- Plantilla anidada

   Para registrar la información de depuración de una plantilla anidada, use el elemento **debugSetting**.

       {
           "apiVersion": "2016-09-01",
           "name": "nestedTemplate",
           "type": "Microsoft.Resources/deployments",
           "properties": {
               "mode": "Incremental",
               "templateLink": {
                   "uri": "{template-uri}",
                   "contentVersion": "1.0.0.0"
               },
               "debugSetting": {
                  "detailLevel": "requestContent, responseContent"
               }
           }
       }


### <a name="create-a-troubleshooting-template"></a>Creación de una plantilla de solución de problemas
En algunos casos, la manera más fácil de solucionar problemas de plantillas es comprobando sus elementos. Puede crear una plantilla simplificada que le permita centrarse en lo que crea que está provocando el error. Por ejemplo, supongamos que se recibe un error al hacer referencia a un recurso. En lugar de trabajar directamente con una plantilla de toda, cree una plantilla que devuelva la parte que pueda estar causando el problema. Esto puede ayudarlo a determinar si está pasando los parámetros correctos con las funciones de plantilla correctamente y obteniendo el recurso esperado.

    {
      "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
        "storageName": {
            "type": "string"
        },
        "storageResourceGroup": {
            "type": "string"
        }
      },
      "variables": {},
      "resources": [],
      "outputs": {
        "exampleOutput": {
            "value": "[reference(resourceId(parameters('storageResourceGroup'), 'Microsoft.Storage/storageAccounts', parameters('storageName')), '2016-05-01')]",
            "type" : "object"
        }
      }
    }

Supongamos que se están produciendo errores de implementación que cree que están relacionados con dependencias establecidas incorrectamente. Pruebe la plantilla dividiéndola en plantillas simplificadas. En primer lugar, cree una plantilla que implemente solo un único recurso (por ejemplo, un servidor SQL Server). Cuando esté seguro de que tiene dicho recurso definido correctamente, agregue un recurso que dependa de él (por ejemplo, una base de datos SQL). Cuando esos dos recursos se definan correctamente, agregue otros recursos dependientes (por ejemplo, las directivas de auditoría). Entre cada implementación de prueba, elimine el grupo de recursos para asegurarse de que se prueban adecuadamente las dependencias. 

### <a name="check-deployment-sequence"></a>Comprobación de la secuencia de implementación

Muchos errores de implementación se producen cuando los recursos se implementan en una secuencia inesperada. Estos errores se producen cuando las dependencias no se han establecido correctamente. Un recurso intenta usar un valor para otro recurso pero este último todavía no existe. Para ver el orden de las operaciones de implementación:

1. Seleccione el historial de implementaciones para el grupo de recursos.

   ![seleccionar el historial de implementaciones](./media/resource-manager-common-deployment-errors/select-deployment.png)

2. Seleccione una implementación del historial y seleccione **Eventos**.

   ![seleccionar eventos de implementación](./media/resource-manager-common-deployment-errors/select-deployment-events.png)

3. Examine la secuencia de eventos para cada recurso. Preste atención para el estado de cada operación. Por ejemplo, la siguiente imagen muestra tres cuentas de almacenamiento que se implementan en paralelo. Tenga en cuenta que las tres cuentas de almacenamiento se inician al mismo tiempo.

   ![implementación paralela](./media/resource-manager-common-deployment-errors/deployment-events-parallel.png)

   La siguiente imagen muestra tres cuentas de almacenamiento que no se implementan en paralelo. La segunda cuenta de almacenamiento se marca como dependiente de la primera cuenta de almacenamiento y la tercera cuenta de almacenamiento depende de la segunda cuenta de almacenamiento. Por lo tanto, la primera cuenta de almacenamiento se inicia, acepta y completa antes de que se inicie la siguiente.

   ![implementación secuencial](./media/resource-manager-common-deployment-errors/deployment-events-sequence.png)

Examine los eventos de implementación para ver si un recurso se ha iniciado antes de lo que cabría esperar. Si es así, compruebe las dependencias para este recurso.

## <a name="troubleshooting-other-services"></a>Solución de problemas con otros servicios
Si los códigos de error de implementación anterior no le han ayudado solucionar el problema, puede buscar instrucciones más detalladas de solución de problemas del servicio de Azure que tiene el error.

En la tabla siguiente se enumeran los temas de solución de problemas para máquinas virtuales.

| Error | Artículos |
| --- | --- |
| Errores de extensión de script personalizado |[Errores de extensión de máquina virtual Linux](../virtual-machines/virtual-machines-windows-extensions-troubleshoot.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)<br />o<br />[Errores de extensión de máquina virtual Linux](../virtual-machines/virtual-machines-linux-extensions-troubleshoot.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) |
| Errores de aprovisionamiento de imágenes de sistema operativo |[Nuevos errores de máquina virtual Windows](../virtual-machines/virtual-machines-windows-troubleshoot-deployment-new-vm.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)<br />o<br />[Nuevos errores de máquina virtual Linux](../virtual-machines/virtual-machines-linux-troubleshoot-deployment-new-vm.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) |
| Errores de asignación |[Errores de asignación de máquina virtual Linux](../virtual-machines/virtual-machines-windows-allocation-failure.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)<br />o<br />[Errores de asignación de máquina virtual Linux](../virtual-machines/virtual-machines-linux-allocation-failure.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) |
| Errores de Secure Shell (SSH) al intentar conectarse |[Conexiones de Secure Shell a máquina virtual Linux](../virtual-machines/virtual-machines-linux-troubleshoot-ssh-connection.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) |
| Errores de conexión a una aplicación que se ejecuta en una máquina virtual |[Aplicación que se ejecuta en una máquina virtual Linux](../virtual-machines/virtual-machines-windows-troubleshoot-app-connection.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)<br />o<br />[Aplicación que se ejecuta en una máquina virtual Linux](../virtual-machines/virtual-machines-linux-troubleshoot-app-connection.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) |
| Errores de conexión del Escritorio remoto |[Conexiones del Escritorio remoto a máquinas virtuales Windows](../virtual-machines/virtual-machines-windows-troubleshoot-rdp-connection.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) |
| Errores de conexión resueltos con una nueva implementación |[Nueva implementación de la máquina virtual en un nuevo nodo de Azure](../virtual-machines/virtual-machines-windows-redeploy-to-new-node.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) |
| Errores de servicio en la nube |[Problemas de implementación de servicio en la nube](../cloud-services/cloud-services-troubleshoot-deployment-problems.md) |

En la tabla siguiente se enumeran los temas de solución de problemas para otros servicios de Azure. Se centra en los problemas relacionados con la implementación o la configuración de recursos. Si necesita ayuda para solucionar problemas de tiempo de ejecución con un recurso, consulte la documentación de ese servicio de Azure.

| Servicio | Artículo |
| --- | --- |
| Automatización |[Sugerencias para la solución de problemas para errores comunes de Automatización de Azure](../automation/automation-troubleshooting-automation-errors.md) |
| Azure Stack |[Solución de problemas de Microsoft Azure Stack](../azure-stack/azure-stack-troubleshooting.md) |
| Data Factory |[Solución de problemas de la factoría de datos](../data-factory/data-factory-troubleshoot.md) |
| Service Fabric |[Solución de problemas comunes al implementar servicios en Azure Service Fabric](../service-fabric/service-fabric-diagnostics-troubleshoot-common-scenarios.md) |
| Recuperación de sitios |[Protección de supervisión y solución de problemas para las máquinas virtuales y los servidores físicos](../site-recovery/site-recovery-monitoring-and-troubleshooting.md) |
| Almacenamiento |[Supervisión, diagnóstico y solución de problemas de Almacenamiento de Microsoft Azure](../storage/storage-monitoring-diagnosing-troubleshooting.md) |
| StorSimple |[Solución de problemas de implementación de dispositivos de StorSimple](../storsimple/storsimple-troubleshoot-deployment.md) |
| Base de datos SQL |[Solución de problemas de conexión comunes relacionados con la base de datos SQL de Azure](../sql-database/sql-database-troubleshoot-common-connection-issues.md) |
| Almacenamiento de datos SQL |[Solución de problemas de Almacenamiento de datos SQL de Azure](../sql-data-warehouse/sql-data-warehouse-troubleshoot.md) |

## <a name="next-steps"></a>Pasos siguientes
* Para más información sobre las acciones de auditoría, consulte [Operaciones de auditoría con Resource Manager](resource-group-audit.md).
* Si desea conocer más detalles sobre las acciones que permiten determinar los errores durante la implementación, consulte [Visualización de operaciones de implementación con el Portal de Azure](resource-manager-troubleshoot-deployments-portal.md).



<!--HONumber=Dec16_HO3-->


