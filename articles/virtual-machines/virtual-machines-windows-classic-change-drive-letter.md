---
title: "Conversión de la unidad D: de una máquina virtual en un disco de datos | Microsoft Docs"
description: "Se describe cómo cambiar las letras de unidad de una máquina virtual de Windows para poder usar la unidad D: como unidad de datos."
services: virtual-machines-windows
documentationcenter: 
author: cynthn
manager: timlt
editor: 
tags: azure-resource-manager,azure-service-management
ms.assetid: 0867a931-0055-4e31-8403-9b38a3eeb904
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 09/27/2016
ms.author: cynthn
translationtype: Human Translation
ms.sourcegitcommit: ee34a7ebd48879448e126c1c9c46c751e477c406
ms.openlocfilehash: 002e7fe3a0573898fff2552264a318d528eec25c


---
# <a name="use-the-d-drive-as-a-data-drive-on-a-windows-vm"></a>Uso de la unidad de disco D: como unidad de datos en una máquina virtual Windows
Si su aplicación necesita usar la unidad D para almacenar datos, siga estas instrucciones para usar una unidad distinta para el disco temporal. Nunca use el disco temporal para almacenar los datos que desee conservar.

Si se cambia el tamaño o se selecciona **Detener (desasignar)** una máquina virtual, se puede desencadenar la colocación de la máquina virtual en un hipervisor nuevo. Un evento de mantenimiento planificado o no planificado también puede desencadenar esta colocación. En este escenario, el disco temporal se reasignará a la primera letra de unidad disponible. Si tiene una aplicación que necesita específicamente la unidad D:, debe seguir estos pasos para mover temporalmente el archivo pagefile.sys, adjuntar un nuevo disco de datos, asignarle la letra D y devolver el archivo pagefile.sys a la unidad temporal. Cuando lo haya hecho, Azure no devolverá D: si la máquina virtual se desplaza a un hipervisor diferente.

Para obtener más información sobre cómo usa Azure el disco temporal, consulte [Understanding the temporary drive on Microsoft Azure Virtual Machines](https://blogs.msdn.microsoft.com/mast/2013/12/06/understanding-the-temporary-drive-on-windows-azure-virtual-machines/)

[!INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]

## <a name="attach-the-data-disk"></a>Conectar el disco de datos
En primer lugar, deberá conectar el disco de datos a la máquina virtual. 

* Para usar el portal, consulte [How to attach a data disk in the Azure portal](virtual-machines-windows-attach-disk-portal.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* Para usar el portal clásico, consulte [Cómo conectar un disco de datos a una máquina virtual Windows](virtual-machines-windows-classic-attach-disk.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json). 

## <a name="temporarily-move-pagefilesys-to-c-drive"></a>Mover temporalmente el archivo pagefile.sys a la unidad C
1. Conexión a una máquina virtual. 
2. Haga clic con el botón derecho en el menú **Inicio** y seleccione **Sistema**.
3. En el menú que aparece a la izquierda, seleccione **Configuración avanzada del sistema**.
4. En la sección **Rendimiento**, seleccione **Configuración**.
5. Seleccione la pestaña **Opciones avanzadas** .
6. En la sección **Memoria virtual**, seleccione **Cambiar**.
7. Seleccione la unidad **C**, luego haga clic en **Tamaño administrado por el sistema** y en **Establecer**.
8. Seleccione la unidad **D**, luego haga clic en **Sin archivo de paginación** y, a continuación, en **Establecer**.
9. Haga clic en Aplicar. Aparecerá una advertencia que indica que se debe reiniciar el equipo para que los cambios entren en vigor.
10. Reinicie la máquina virtual.

## <a name="change-the-drive-letters"></a>Cambiar las letras de las unidades
1. Una vez que se reinicia la máquina virtual, vuelva a iniciar sesión en ella.
2. Haga clic en el menú **Inicio**, escriba**diskmgmt.msc** y presione Entrar. Se iniciará la Administración de discos.
3. Haga clic con el botón derecho en **D**, la unidad de almacenamiento temporal, y seleccione **Cambiar la letra y rutas de acceso de unidad**.
4. En Letra de unidad, seleccione la unidad **G** y haga clic en **Aceptar**. 
5. Haga clic con el botón derecho en el disco de datos y seleccione **Cambiar la letra y rutas de acceso de unidad**.
6. En Letra de unidad, seleccione la unidad **D** y haga clic en**Aceptar**. 
7. Haga clic con el botón derecho en **G**, la unidad de almacenamiento temporal, y seleccione**Cambiar la letra y rutas de acceso de unidad**.
8. En Letra de unidad, seleccione la unidad**E** y haga clic en**Aceptar**. 

> [!NOTE]
> Si la máquina virtual tiene otros discos o unidades, use el mismo método para reasignar las letras de unidad de los demás discos y unidades. La configuración de disco puede estar en las siguientes ubicaciones:  
> 
> * C: disco del sistema operativo  
> * D: disco de datos  
> * E: disco temporal
> 
> 

## <a name="move-pagefilesys-back-to-the-temporary-storage-drive"></a>Devolver el archivo pagefile.sys a la unidad de almacenamiento temporal
1. Haga clic con el botón derecho en el menú **Inicio** y seleccione **Sistema**.
2. En el menú que aparece a la izquierda, seleccione **Configuración avanzada del sistema**.
3. En la sección **Rendimiento**, seleccione **Configuración**.
4. Seleccione la pestaña **Opciones avanzadas** .
5. En la sección **Memoria virtual**, seleccione **Cambiar**.
6. Seleccione la unidad del sistema operativo **C**, haga clic en **Sin archivo de paginación** y luego en **Establecer**.
7. Seleccione la unidad de almacenamiento temporal **E**, haga clic en **Tamaño administrado por el sistema** y, a continuación, haga clic en **Establecer**.
8. Haga clic en **Apply**. Aparecerá una advertencia que indica que se debe reiniciar el equipo para que los cambios entren en vigor.
9. Reinicie la máquina virtual.

## <a name="next-steps"></a>Pasos siguientes
* Puede aumentar el almacenamiento disponible para la máquina virtual [conectando un disco de datos adicional](virtual-machines-windows-attach-disk-portal.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).




<!--HONumber=Nov16_HO3-->


