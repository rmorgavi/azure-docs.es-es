---
title: "Optimización de la máquina virtual Linux en Azure | Microsoft Docs"
description: "Aprenda algunas sugerencias de optimización para asegurarse de que ha configurado la máquina virtual Linux para obtener un rendimiento óptimo en Azure."
keywords: "máquina virtual con linux,máquina virtual linux,máquina virtual con ubuntu"
services: virtual-machines-linux
documentationcenter: 
author: rickstercdn
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: 8baa30c8-d40e-41ac-93d0-74e96fe18d4c
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 09/06/2016
ms.author: rclaus
translationtype: Human Translation
ms.sourcegitcommit: ee34a7ebd48879448e126c1c9c46c751e477c406
ms.openlocfilehash: ca1427e993416db09e7ff1603cbbf419db71f046


---
# <a name="optimize-your-linux-vm-on-azure"></a>Optimización de la máquina virtual Linux en Azure
Crear una máquina virtual con Linux es muy sencillo desde la línea de comandos o desde el Portal. Este tutorial muestra cómo asegurarse de que está configurada para optimizar su rendimiento en la Plataforma Microsoft Azure. Este tema usa una VM de servidor Ubuntu, pero también puede crear máquinas virtuales Linux mediante [sus propias imágenes como plantillas](virtual-machines-linux-create-upload-generic.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).  

## <a name="prerequisites"></a>Requisitos previos
En este tema se da por supuesto que ya tiene una suscripción de Azure activa ([suscripción de evaluación gratuita](https://azure.microsoft.com/pricing/free-trial/)), ya [ha instalado la CLI de Azure](../xplat-cli-install.md) y ya ha aprovisionado una máquina virtual en su suscripción de Azure. Antes de hacer cualquier cosa con Azure, tendrá que autenticarse en su suscripción. Para hacerlo con CLI de Azure, solo tiene que escribir `azure login` para iniciar el proceso interactivo. 

## <a name="azure-os-disk"></a>Disco de sistema operativo de Azure
Al crear la máquina virtual Linux en Azure, tiene dos discos asociados a ella. /dev/sda es el disco del sistema operativo, /dev/sdb es el disco temporal.  No utilice el disco del sistema operativo principal (/dev/sda) salvo para el mismo sistema operativo, ya que se ha optimizado para un tiempo de arranque rápido de la VM y no proporcionará un buen rendimiento para sus cargas de trabajo. Querrá conectar uno o más discos a la VM con el fin de obtener un almacenamiento optimizado y persistente para sus datos. 

## <a name="adding-disks-for-size-and-performance-targets"></a>Adición de discos para objetivos de rendimiento y tamaño
En función del tamaño de la VM, puede conectar hasta 16 discos adicionales en una máquina de serie A, 32 discos en una de serie D y 64 discos en una máquina de serie G, cada una de ellas con hasta 1 TB de tamaño. Agregue discos adicionales según sea necesario en función del espacio y los requisitos de IOPS. Cada disco tiene un objetivo de rendimiento de 500 IOPS para el Almacenamiento estándar y de hasta 5000 IOPS por disco para el Almacenamiento Premium.  Para obtener más información sobre los discos de Premium Storage, consulte [Premium Storage: almacenamiento de alto rendimiento para cargas de trabajo de VM de Azure](../storage/storage-premium-storage.md)

Para alcanzar el máximo valor de IOPS en los discos de Premium Storage con la configuración de caché como "ReadOnly" o "None", debe deshabilitar las "barreras" al montar el sistema de archivos en Linux. No necesita las barreras porque las escrituras en los discos de Almacenamiento premium de copia de seguridad son duraderas para esta configuración de caché.

* Si utiliza **reiserFS**, deshabilite las barreras mediante la opción de montaje “barrier=none” (para habilitar las barreras, use “barrier=flush”)
* Si utiliza **ext3/ext4**, deshabilite las barreras mediante la opción de montaje “barrier=0” (para habilitar las barreras, use “barrier=1”)
* Si utiliza **XFS**, deshabilite las barreras mediante la opción de montaje “nobarrier” (para habilitar las barreras, use la opción “barrier”)

## <a name="storage-account-considerations"></a>Consideraciones de la cuenta de almacenamiento
Cuando cree la máquina virtual Linux en Azure, asegúrese de que conecta los discos desde las cuentas de almacenamiento que residen en la misma región que la máquina virtual para garantizar la proximidad y minimizar la latencia de red.  Cada cuenta de almacenamiento estándar tiene un máximo de IOPS de 20 k y una capacidad de tamaño de 500 TB.  Esto nos da aproximadamente 40 discos muy utilizados, incluido el disco del sistema operativo y cualquier disco de datos que haya creado. Para las cuentas de Almacenamiento premium, no hay ningún límite máximo de IOPS, pero hay un límite de tamaño de 32 TB. 

Al tratar con cargas de trabajo de IOPS elevadas y de haber elegido el almacenamiento estándar para los discos, debe dividir los discos entre varias cuentas de almacenamiento para asegurarse de no alcanzar el límite de 20 000 IOPS para las cuentas de Almacenamiento estándar. La máquina virtual puede contener una combinación de discos de las diferentes cuentas de almacenamiento y de los tipos de cuentas de almacenamiento para alcanzar la configuración óptima. 

## <a name="your-vm-temporary-drive"></a>Su unidad temporal de máquina virtual
De forma predeterminada, cuando se crea una VM, Azure proporciona un disco de sistema operativo (/dev/sda) y un disco temporal (/dev/sdb).  Todos los discos adicionales que se agreguen se mostrarán como /dev/sdc, /dev/sdd, /dev/sde, etc. Todos los datos del disco temporal (/dev/sdb) no son duraderos y se pueden perder si eventos específicos, como el cambio de tamaño de la VM, la reimplementación o el mantenimiento, fuerzan un reinicio de la VM.  El tamaño y el tipo de disco temporal está relacionado con el tamaño de memoria virtual que seleccionó en el momento de la implementación. En cualquiera de las VM de la serie premium (serie DS, G y DS_V2) se realizará una copia de seguridad de la unidad temporal en una unidad SSD local para obtener un rendimiento adicional de hasta 48 000 IOPS. 

## <a name="linux-swap-file"></a>Archivo de intercambio de Linux
Si la VM de Azure procede de una imagen de Ubuntu o CoreOS, puede usar CustomData para enviar una cloud-config a cloud-init. Si [cargó una imagen personalizada de Linux](virtual-machines-linux-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) que usa cloud-init, configure también particiones de intercambio mediante cloud-init.

En las imágenes de nube de Ubuntu, debe usar cloud-init para configurar la partición de intercambio. Consulte la siguiente página en la wiki de Ubuntu para obtener más detalles: [AzureSwapPartitions](https://wiki.ubuntu.com/AzureSwapPartitions).

En el caso de las imágenes sin compatibilidad con cloud-init, las imágenes de máquina virtual implementadas desde Azure Marketplace tienen un agente Linux de máquina virtual integrado con el sistema operativo. Este agente permite que la máquina virtual interactúe con diversos servicios de Azure. Suponiendo que ha implementado una imagen estándar desde Azure Marketplace, deberá hacer lo siguiente para configurar correctamente los valores del archivo de intercambio de Linux:

Busque y modifique dos entradas en el archivo **/etc/waagent.conf** . Estas entradas controlan la existencia de un archivo de intercambio dedicado y el tamaño del archivo de intercambio. Los parámetros que desea modificar son `ResourceDisk.EnableSwap=N` y `ResourceDisk.SwapSizeMB=0`. 

Tendrá que cambiarlos a los siguientes:

* ResourceDisk.EnableSwap=Y
* ResourceDisk.SwapSizeMB={tamaño en MB que satisfaga sus requisitos} 

Una vez haya realizado el cambio, debe reiniciar waagent o la VM Linux con el fin de reflejar dichos cambios.  Sabrá que se han implementado los cambios y que se ha creado un archivo de intercambio cuando use el comando `free` para ver el espacio libre. En el ejemplo siguiente tiene un archivo de intercambio de 512 MB creado como resultado de modificar el archivo waagent.conf.

    admin@mylinuxvm:~$ free
                total       used       free     shared    buffers     cached
    Mem:       3525156     804168    2720988        408       8428     633192
    -/+ buffers/cache:     162548    3362608
    Swap:       524284          0     524284
    admin@mylinuxvm:~$

## <a name="io-scheduling-algorithm-for-premium-storage"></a>Algoritmo de programación de E/S para Almacenamiento premium
Con el kernel de Linux 2.6.18, el algoritmo de programación de E/S predeterminado se cambió de Deadline (Fecha límite) a CFQ (Algoritmo de cola completamente razonable). Para patrones de E/S de acceso aleatorio, hay una diferencia insignificante en diferencias de rendimiento entre CFQ y Deadline.  En el caso de los discos SSD donde el patrón de E/S del disco es principalmente secuencial, el cambio al algoritmo NOOP o al de Deadline puede lograr un mejor rendimiento de E/S.

### <a name="view-the-current-io-scheduler"></a>Visualización del programador de E/S actual
Use el comando siguiente:  

    admin@mylinuxvm:~# cat /sys/block/sda/queue/scheduler

Se mostrará el la salida siguiente, que indica cuál es el programador actual.  

    noop [deadline] cfq

### <a name="change-the-current-device-devsda-of-io-scheduling-algorithm"></a>Cambiar el dispositivo actual (/dev/sda) del algoritmo de programación de E/S
Use los comandos siguientes:  

    azureuser@mylinuxvm:~$ sudo su -
    root@mylinuxvm:~# echo "noop" >/sys/block/sda/queue/scheduler
    root@mylinuxvm:~# sed -i 's/GRUB_CMDLINE_LINUX=""/GRUB_CMDLINE_LINUX_DEFAULT="quiet splash elevator=noop"/g' /etc/default/grub
    root@mylinuxvm:~# update-grub

> [!NOTE]
> Establecer este valor solo para /dev/sda no es útil. Debe establecerse en todos los discos de datos donde la E/S secuencial domina el patrón de E/S.  
> 
> 

Debería mostrarse el resultado siguiente, que indica que grub.cfg se ha vuelto a compilar correctamente y que se ha actualizado el programador predeterminado a NOOP.  

    Generating grub configuration file ...
    Found linux image: /boot/vmlinuz-3.13.0-34-generic
    Found initrd image: /boot/initrd.img-3.13.0-34-generic
    Found linux image: /boot/vmlinuz-3.13.0-32-generic
    Found initrd image: /boot/initrd.img-3.13.0-32-generic
    Found memtest86+ image: /memtest86+.elf
    Found memtest86+ image: /memtest86+.bin
    done

Para la familia de distribución Redhat, solo necesita el siguiente comando:   

    echo 'echo noop >/sys/block/sda/queue/scheduler' >> /etc/rc.local

## <a name="using-software-raid-to-achieve-higher-iops"></a>Uso del software RAID para alcanzar mayores IOPS
Si las cargas de trabajo necesitan más IOPS de lo que puede proporcionar un único disco, debe utilizar una configuración de software RAID de varios discos. Como Azure ya realiza la resistencia de disco en el nivel de tejido local, obtendrá el máximo nivel de rendimiento en una configuración de creación de bandas RAID-0.  Deberá aprovisionar y crear nuevos discos en el entorno de Azure y conectarlos a la VM Linux antes de la creación de particiones, del formato y del montaje de las unidades.  Para más información sobre cómo configurar el software RAID en la máquina virtual Linux en Azure, consulte el documento **[Configuración del software RAID en Linux](virtual-machines-linux-configure-raid.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**.

## <a name="next-steps"></a>Pasos siguientes
Recuerde que, como en todos los debates sobre optimización, debe realizar pruebas antes y después de cada cambio para cuantificar el impacto que tendrá el cambio.  La optimización es un proceso paso a paso que tendrá resultados diferentes en las distintas máquinas en su entorno.  Lo que funciona para una configuración puede no funcionar para otras.

Algunos vínculos útiles a recursos adicionales: 

* [Almacenamiento premium: Almacenamiento de alto rendimiento para cargas de trabajo de máquina virtual de Azure](../storage/storage-premium-storage.md)
* [Guía de usuario del Agente de Linux de Azure](virtual-machines-linux-agent-user-guide.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Optimización del rendimiento de MySQL en máquinas virtuales de Azure con Linux](virtual-machines-linux-classic-optimize-mysql.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json)
* [Configuración del software RAID en Linux](virtual-machines-linux-configure-raid.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)




<!--HONumber=Nov16_HO3-->


