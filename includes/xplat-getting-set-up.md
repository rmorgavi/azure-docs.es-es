---
services: virtual-machines
title: "Configuración de la CLI de Azure CLI para la administración de servicios"
author: squillace
solutions: 
manager: timlt
editor: tysonn
ms.service: virtual-machine
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: linux
ms.workload: infrastructure
ms.date: 04/13/2015
ms.author: rasquill
translationtype: Human Translation
ms.sourcegitcommit: e664ce9426a2852a35dfdade5d41a9ce8b37a3b7
ms.openlocfilehash: f18a4705c9fd57790bff9cfdd23c19fd30fe0d32


---
## <a name="using-azure-cli"></a>Uso de la CLI de Azure
Los pasos siguientes le ayudarán a usar la CLI de Azure fácilmente con la versión más reciente y la suscripción adecuada. Si necesita instalar la CLI de Azure y conectarla primero a su cuenta, consulte la [interfaz de la línea de comandos de Azure (CLI de Azure)](../articles/xplat-cli-install.md).

### <a name="step-1-update-azure-cli-version"></a>Paso 1: Actualización de la CLI de Azure
Para usar la CLI de Azure para comandos imperativos con el modo Administración de servicios, debe tener una versión reciente, si es posible. Para comprobar su versión, escriba `azure --version`. Debería ver algo parecido a lo siguiente:

    $ azure --version
    0.8.17 (node: 0.10.25)

Si desea actualizar la versión de la CLI de Azure, consulte [CLI de Azure](https://github.com/Azure/azure-xplat-cli).

### <a name="step-2-set-the-azure-account-and-subscription"></a>Paso 2: establecimiento de la cuenta y suscripción de Azure
Una vez que haya conectado la CLI de Azure a la cuenta que desee utilizar, puede tener más de una suscripción. En ese caso, debe revisar las suscripciones disponibles para la cuenta, para lo que debe escribir `azure account list`, y, después, seleccionar la suscripción que desea usar y escriba `azure account set <subscription id or name> true`, donde *subscription id or name* es el identificador de la suscripción o el nombre de la suscripción con la que desea trabajar en la sesión actual. Debe ver algo parecido a lo siguiente:

    $ azure account set "Visual Studio Ultimate with MSDN" true
    info:    Executing command account set
    info:    Setting subscription to "Visual Studio Ultimate with MSDN" with id "0e220bf6-5caa-4e9f-8383-51f16b6c109f".
    info:    Changes saved
    info:    account set command OK

> [!NOTE]
> Si todavía no tiene una cuenta de Azure, pero dispone de una suscripción a MSDN, puede obtener créditos gratuitos de Azure mediante la activación de sus [ventajas de suscriptor de MSDN aquí](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) , o puede usar la cuenta gratuita. Con cualquiera de estas opciones podrá obtener acceso a Azure.
> 
> 




<!--HONumber=Jan17_HO3-->


