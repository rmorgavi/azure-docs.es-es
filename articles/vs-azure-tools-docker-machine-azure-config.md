---
title: "Creación de hosts de Docker en Azure con Docker Machine | Microsoft Docs"
description: "Describe el uso de la máquina de Docker para crear hosts de docker en Azure."
services: azure-container-service
documentationcenter: na
author: mlearned
manager: douge
editor: 
ms.assetid: 7a3ff6e1-fa93-4a62-b524-ab182d2fea08
ms.service: multiple
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 06/08/2016
ms.author: mlearned
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: e34cb21da7d08db0cd65db211102788232ac422f


---
# <a name="create-docker-hosts-in-azure-with-docker-machine"></a>Creación de hosts de Docker en Azure con docker-machine
La ejecución de contenedores de [Docker](https://www.docker.com/) requiere una máquina virtual host que ejecute el demonio de Docker.
En este tema, se describe cómo usar el comando [docker-machine](https://docs.docker.com/machine/) para crear máquinas virtuales Linux, configuradas con el demonio de Docker, que se ejecuten en Azure. 

**Nota:** 

* *Este artículo depende de la versión 0.7.0 de docker-machine, o de una versión superior*
* *Muy pronto será posible usar contenedores de Windows a través del comando docker-machine*

## <a name="create-vms-with-docker-machine"></a>Creación de VM con la máquina de Docker
Cree máquinas virtuales host de Docker en Azure con el comando `docker-machine create` mediante el controlador `azure`. 

El controlador de Azure necesitará su identificador de suscripción. Para recuperar su suscripción de Azure, puede usar la [CLI de Azure](xplat-cli-install.md) o [Azure Portal](https://portal.azure.com). 

**Uso del portal de Azure**

* Seleccione Suscripciones de la página de navegación izquierda y copie el identificador de suscripción.

**Uso de la CLI de Azure**

* Escriba ```azure account list``` y copie el identificador de suscripción.

Escriba `docker-machine create --driver azure` para ver las opciones y sus valores predeterminados.
Para más información, también puede consultar la [documentación del controlador de Azure de Docker](https://docs.docker.com/machine/drivers/azure/) . 

En el siguiente ejemplo se usan los valores predeterminados, pero brinda la opción de abrir el puerto 80 en la máquina virtual para el acceso a Internet. 

```
docker-machine create -d azure --azure-subscription-id <Your AZURE_SUBSCRIPTION_ID> --azure-open-port 80 mydockerhost
```

## <a name="choose-a-docker-host-with-docker-machine"></a>Elección de un host de Docker con una docker-machine
Una vez que tenga una entrada en docker-machine para su host, puede establecer el host predeterminado al ejecutar comandos de Docker.

## <a name="using-powershell"></a>Con PowerShell
```powershell
docker-machine env MyDockerHost | Invoke-Expression 
```

## <a name="using-bash"></a>Con Bash
```bash
eval $(docker-machine env MyDockerHost)
```

Ya se pueden ejecutar comandos de Docker en el host especificado

```
docker ps
docker info
```

## <a name="run-a-container"></a>Ejecución de un contenedor
Con un host configurado, ya se puede ejecutar un servidor web simple para probar si dicho host se ha configurado correctamente.
Aquí se usará una imagen de nginx estándar, se especifica que debe escuchar el puerto 80 y que si la máquina virtual host se reinicia, el contenedor también se reiniciará (`--restart=always`). 

```bash
docker run -d -p 80:80 --restart=always nginx
```

La salida debe tener un aspecto similar al siguiente:

```
Unable to find image 'nginx:latest' locally
latest: Pulling from library/nginx
efd26ecc9548: Pull complete
a3ed95caeb02: Pull complete
83f52fbfa5f8: Pull complete
fa664caa1402: Pull complete
Digest: sha256:12127e07a75bda1022fbd4ea231f5527a1899aad4679e3940482db3b57383b1d
Status: Downloaded newer image for nginx:latest
25942c35d86fe43c688d0c03ad478f14cc9c16913b0e1c2971cb32eb4d0ab721
```

## <a name="test-the-container"></a>Prueba del contenedor
Examine los contenedores en ejecución mediante `docker ps`:

```bash
CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS              PORTS                         NAMES
d5b78f27b335        nginx               "nginx -g 'daemon off"   5 minutes ago       Up 5 minutes        0.0.0.0:80->80/tcp, 443/tcp   goofy_mahavira
```

Y para comprobar el contenedor en ejecución, escriba `docker-machine ip <VM name>` para buscar la dirección IP que debe especificar en el explorador:

```
PS C:\> docker-machine ip MyDockerHost
191.237.46.90
```

![Ejecución del contenedor ngnix](./media/vs-azure-tools-docker-machine-azure-config/nginxsuccess.png)

## <a name="summary"></a>Resumen
Con docker-machine se pueden aprovisionar fácilmente hosts de Docker en Azure para las validaciones de host de Docker individuales.
Para la creación de hospedaje de contenedores, consulte el [Servicio de contenedores de Azure](http://aka.ms/AzureContainerService)

Para desarrollar aplicaciones de .NET Core con Visual Studio, consulte [Docker Tools para Visual Studio](http://aka.ms/DockerToolsForVS)




<!--HONumber=Nov16_HO3-->


