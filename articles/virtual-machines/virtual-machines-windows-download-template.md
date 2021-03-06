---
title: "Creación de una imagen de máquina virtual desde una máquina virtual de Azure | Microsoft Docs"
description: "Obtenga información sobre cómo crear una imagen de máquina virtual generalizada a partir de una máquina virtual de Azure existente creada en el modelo de implementación de Resource Manager"
services: virtual-machines-windows
documentationcenter: 
author: cynthn
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 51ef4f51-0942-4249-afea-4a3f87ce1ff8
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 10/10/2016
ms.author: cynthn
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: b6dffec166ffe8e04c5d7b701aef009bf7b72d45


---
# <a name="download-the-template-for-a-vm"></a>Descargar la plantilla para una máquina virtual
Cuando se crea una máquina virtual en Azure mediante el portal o PowerShell, se crea automáticamente una plantilla de Resource Manager. Puede usar esta plantilla para duplicar rápidamente una implementación. La plantilla contiene información acerca de todos los recursos de un grupo de recursos. En el caso de una máquina virtual, esto significa que la plantilla contiene todo lo que se crea para ayudar a la máquina virtual en ese grupo de recursos, incluidos los recursos de red.

## <a name="download-the-template-using-the-portal"></a>Descarga de la plantilla mediante el portal
1. Inicie sesión en el [Portal de Azure](https://portal.azure.com/).
2. En el menú del concentrador, haga clic en **Virtual Machines**.
3. Seleccione la máquina virtual en la lista.
4. Seleccione **Script de Automation**.
5. Seleccione **Descargar** y guarde el archivo .zip en el equipo local.
6. Abra el archivo .zip y extraiga los archivos en una carpeta. El archivo .zip contendrá:
   
   * deploy.ps1
   * deploy.sh 
   * deployer.rb
   * DeploymentHelper.cs
   * parameters.json
   * template.json

El archivo .json es la plantilla.

## <a name="download-the-template-using-powershell"></a>Descarga de la plantilla mediante PowerShell
También puede descargar el archivo de plantilla .json mediante el cmdlet [Export-AzureRMResourceGroup](https://msdn.microsoft.com/library/mt715427.aspx). Puede usar el parámetro `-path` para proporcionar el nombre de archivo y la ruta de acceso del archivo .json. En este ejemplo se muestra cómo descargar la plantilla para el grupo de recursos denominado **myResourceGroup** en la carpeta **C:\users\public\downloads** del equipo local.

```powershell
    Export-AzureRmResourceGroup -ResourceGroupName "myResourceGroup" -Path "C:\users\public\downloads"
```

## <a name="next-steps"></a>Pasos siguientes
Para más información sobre la implementación de recursos mediante plantillas, consulte el [tutorial de plantillas de Resource Manager](../resource-manager-template-walkthrough.md).




<!--HONumber=Nov16_HO3-->


