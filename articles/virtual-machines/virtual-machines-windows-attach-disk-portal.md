---
title: "Conexión de un disco de datos a una máquina virtual Windows | Microsoft Docs"
description: "Cómo conectar un disco de datos nuevo o existente a una máquina virtual Windows en el Portal de Azure con el modelo de implementación del Administrador de recursos."
services: virtual-machines-windows
documentationcenter: 
author: cynthn
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 3790fc59-7264-41df-b7a3-8d1226799885
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 11/02/2016
ms.author: cynthn
translationtype: Human Translation
ms.sourcegitcommit: ee34a7ebd48879448e126c1c9c46c751e477c406
ms.openlocfilehash: e7ec8715087abaf5c96a6327d4bcde0ad3fd547a


---
# <a name="how-to-attach-a-data-disk-to-a-windows-vm-in-the-azure-portal"></a>Cómo adjuntar un disco de datos a una máquina virtual Windows en el Portal de Azure
En este artículo se muestra cómo adjuntar discos nuevos y existentes a una máquina virtual Windows a través del Portal de Azure. También puede [adjuntar un disco de datos a una máquina virtual Linux en el Portal de Azure](virtual-machines-linux-attach-disk-portal.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Antes de hacerlo, revise estas sugerencias:

* El tamaño de la máquina virtual controla cuántos discos de datos puede conectar. Para obtener más información, consulte [Tamaños de máquinas virtuales](virtual-machines-windows-sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
* Para un disco nuevo, no es necesario crearlo en primer lugar porque Azure lo crea cuando lo conecta.
* Para un disco existente, el archivo .vhd debe estar disponible en una cuenta de almacenamiento de Azure. Puede usar uno que ya esté disponible allí, si no está conectado a otra máquina virtual, o cargar su propio archivo .vhd en la cuenta de almacenamiento.

También puede [conectar un disco de datos mediante Powershell](virtual-machines-windows-ps-manage.md#add-a-data-disk-to-a-virtual-machine).

[!INCLUDE [virtual-machines-common-attach-disk-portal](../../includes/virtual-machines-common-attach-disk-portal.md)]

## <a name="a-idinitializeinwsahow-to-initialize-a-new-data-disk-in-windows-server"></a><a id="initializeinWS"></a>Inicialización de un nuevo disco de datos en Windows Server
1. Conexión a una máquina virtual. Para obtener instrucciones, consulte [Conexión a una máquina virtual de Azure donde se ejecuta Windows Server e inicio de sesión en ella](virtual-machines-windows-connect-logon.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
2. Después de iniciar sesión en la máquina virtual, abra el **Administrador del servidor**. En el panel izquierdo, seleccione **Servicios de archivos y almacenamiento**.
   
    ![Abrir Administrador de servidores](./media/virtual-machines-windows-classic-attach-disk/fileandstorageservices.png)
3. Expanda el menú y seleccione **Discos**.
4. En la sección **Discos** aparecen todos los discos. En la mayoría de los casos, habrá un disco 0, un disco 1 y un disco 2. El disco 0 es el disco del sistema operativo, el disco 1 es el disco temporal y el disco 2 es el disco de datos que acaba de conectar a la VM. El nuevo disco de datos mostrará la partición como **Desconocida**. Haga clic con el botón derecho en el disco y seleccione **Inicializar**.
5. Se le notificará que se borrarán todos los datos cuando se inicializa el disco. Haga clic en **Sí** para confirmar la advertencia e inicializar el disco. Una vez que se complete el proceso, la partición aparecerá como **GPT**. Vuelva a hacer clic con el botón derecho en el disco y seleccione **Nuevo volumen**.
6. Complete el asistente usando los valores predeterminados que se proporcionan. Cuando haya finalizado el asistente, la sección **Volúmenes** mostrará el nuevo volumen. El disco está ahora conectado y listo para almacenar los datos.

    ![Volumen inicializado correctamente](./media/virtual-machines-windows-classic-attach-disk/newvolumecreated.png)

> [!NOTE]
> El tamaño de la VM determina el número de discos que le puede asociar. Para obtener más información, consulte [Tamaños de máquinas virtuales](virtual-machines-linux-sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
> 
> 

## <a name="next-steps"></a>Pasos siguientes
Si la aplicación debe usar la unidad D: para almacenar datos, puede [cambiar la letra de la unidad del disco temporal de Windows](virtual-machines-windows-classic-change-drive-letter.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).




<!--HONumber=Nov16_HO3-->


