---
title: "Creación de un entorno del Servicio de aplicaciones"
description: "Descripción de flujo de creación de entornos de servicio de aplicación"
services: app-service
documentationcenter: 
author: ccompy
manager: stefsch
editor: 
ms.assetid: 81bd32cf-7ae5-454b-a0d2-23b57b51af47
ms.service: app-service
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/22/2016
ms.author: ccompy
translationtype: Human Translation
ms.sourcegitcommit: af98ae3b425e2e0584da53721249dbcd605a17d1
ms.openlocfilehash: 8d8e1ce7758691a392fdb6d6459e04c6b4f7f5f6


---
# <a name="how-to-create-an-app-service-environment"></a>Creación de App Service Environment
### <a name="overview"></a>Información general
Entornos del Servicio de aplicaciones es una opción del nivel Premium del Servicio de aplicaciones de Azure que ofrece una mejor funcionalidad de configuración que no está disponible en las marcas de varios inquilinos.  La característica de ASE esencialmente implementa el Servicio de aplicaciones de Azure en la red virtual de un cliente.  Para comprender mejor las funciones que ofrecen los entornos de App Service, lea el artículo donde[que explica en qué consiste un entorno de App Service][WhatisASE].

### <a name="before-you-create-your-ase"></a>Antes de crear su ASE
Es importante ser consciente de las cosas que no se pueden cambiar.  Los aspectos que no se pueden cambiar sobre ASE una vez creado son:

* Ubicación
* La suscripción
* Grupo de recursos
* Red virtual usada
* La subred usada 
* Tamaño de la subred

Al seleccionar una red virtual y especificar una subred, asegúrese de que es lo suficientemente grande como para alojar el crecimiento futuro.  

### <a name="creating-an-app-service-environment"></a>Crear un entorno de Servicio de aplicaciones
Hay dos maneras de obtener acceso a la IU de creación de ASE.  Puede encontrarlo buscando ***entorno del App Service*** en Azure Marketplace o a través de Nuevo -> Web + Móvil -> App Service Environment.  Para crear un ASE:

1. Proporcione el nombre de su ASE.  El nombre especificado para el ASE se usará en las aplicaciones creadas en él.  Si el nombre del ASE es appsvcenvdemo, el nombre del subdominio será .*appsvcenvdemo.p.azurewebsites.net*.  Por tanto, si creó una aplicación llamada *mytestapp*, se podría obtener acceso a ella en *mytestapp.appsvcenvdemo.p.azurewebsites.net*.  No puede usar espacios en blanco en el nombre del ASE.  Si utiliza caracteres en mayúsculas en el nombre, el nombre de dominio será la versión en minúsculas total de ese nombre.  Si usa un ILB, su nombre ASE no se usa en su subdominio pero, en su lugar, se indica explícitamente durante la creación del ASE.
   
    ![][1]
2. Seleccione su suscripción.  La suscripción utilizada para su ASE también es aquella con la que se crearán todas las aplicaciones en ese ASE.  No se puede colocar su ASE en una red virtual que se encuentra en otra suscripción
3. Seleccione o especifique un nuevo grupo de recursos.  El grupo de recursos que se usa para su ASE debe ser el mismo que se utiliza para la red virtual.  Si selecciona una red virtual existente, la selección del grupo de recursos para su ASE se actualizará para reflejar la red virtual.
   
    ![][2]
4. Realice las selecciones de red virtual y ubicación.  Puede crear una red virtual nueva o seleccionar una red virtual existente.  Si selecciona una red virtual nueva, puede especificar un nombre y una ubicación. La red virtual nueva tendrá el intervalo de dirección 192.168.250.0/23 y una subred denominada **predeterminada** que se define como 192.168.250.0/24.  Puede seleccionar también simplemente una red virtual de Resource Manager o clásica creadas previamente.  La selección del tipo de VIP determina si es posible obtener acceso directo a su ASE desde Internet (externo) o si usa un equilibrador de carga interno (ILB).  Para obtener más información sobre ellos, consulte [Uso de un equilibrador de carga interno con un entorno de App Service][ILBASE].  Si selecciona un tipo de VIP externo, puede seleccionar la cantidad de direcciones IP externas que crea el sistema con fines de IPSSL.  Si selecciona Interno, debe especificar el subdominio que utilizará el ASE.  Se pueden implementar los ASE en redes virtuales que usen **intervalos de direcciones públicas *o* espacios de direcciones de RFC1918 (es decir, direcciones privadas).  Para usar una red virtual con un intervalo de direcciones públicas, es necesario crear la red virtual por adelantado.  Cuando seleccione una red virtual creada previamente, debe crear una nueva subred durante la creación de ASE.  **No se puede usar una subred creada previamente en el portal.  Puede crear un ASE con una subred creada previamente si crea su ASE con una plantilla de Resource Manager.**  Para crear un ASE a partir de una plantilla, use la información de estos artículos: [Creación de un entorno de App Service a partir de una plantilla][ILBAseTemplate] y [Creación de un entorno de App Service de ILB a partir una plantilla][ASEfromTemplate].

### <a name="details"></a>Detalles
Se crea un ASE con 2 servidores front-end y 2 trabajos.  Los front-end actúan como los puntos de conexión HTTP/HTTPS y envían tráfico a los trabajos que son los roles que hospedan sus aplicaciones.   Puede ajustar la cantidad después de la creación de ASE y puede incluso configurar reglas de escalado automático en estos grupos de recursos.  Para obtener información sobre el escalado manual, la administración y la supervisión del entorno de App Service, consulte [Configuración de un entorno de App Service][ASEConfig]. 

Solo un ASE puede existir en la subred usada por ASE.  La subred no se puede usar para cualquier elemento que no sea el ASE

### <a name="after-app-service-environment-creation"></a>Después de la creación del entorno del Servicio de aplicaciones
Después de la creación de ASE puede ajustar:

* La cantidad de servidores front-end (mínimo: 2)
* La cantidad de trabajos (mínimo: 2)
* Cantidad de direcciones IP disponibles para SSL de IP
* Los tamaños de recursos de proceso usados por los servidores front-end o los trabajos (el tamaño mínimo de front-end es P2)

Aquí podrá obtener información sobre el escalado manual, la administración y la supervisión del entorno de App Service: [Configuración de un entorno de App Service][ASEConfig]. 

Para obtener información sobre el escalado automático, puede encontrar aquí una guía: [Configuración del escalado automático para un entorno de App Service][ASEAutoscale].

Existen dependencias adicionales que no están disponibles para personalización, como la base de datos y el almacenamiento.  Estas son controlados por Azure y se incluyen con el sistema.  El almacenamiento del sistema admite hasta 500 GB para todo el entorno del Servicio de aplicaciones, y la base de datos se ajusta con Azure según sea necesario por medio de la escala del sistema.

## <a name="getting-started"></a>Introducción
Todos los artículos y procedimientos para los entornos del Servicio de aplicaciones están disponibles en el archivo [Léame para entornos del Servicio de aplicaciones](../app-service/app-service-app-service-environments-readme.md).

Para empezar a trabajar con entornos de App Service, consulte [Introducción al entorno de App Service][WhatisASE].

Para obtener más información sobre la plataforma Azure App Service, consulte [Azure App Service][AzureAppService].

[!INCLUDE [app-service-web-whats-changed](../../includes/app-service-web-whats-changed.md)]

[!INCLUDE [app-service-web-try-app-service](../../includes/app-service-web-try-app-service.md)]

<!--Image references-->
[1]: ./media/app-service-web-how-to-create-an-app-service-environment/asecreate-basecreateblade.png
[2]: ./media/app-service-web-how-to-create-an-app-service-environment/asecreate-vnetcreation.png

<!--Links-->
[WhatisASE]: http://azure.microsoft.com/documentation/articles/app-service-app-service-environment-intro/
[ASEConfig]: http://azure.microsoft.com/documentation/articles/app-service-web-configure-an-app-service-environment/
[AppServicePricing]: http://azure.microsoft.com/pricing/details/app-service/ 
[AzureAppService]: http://azure.microsoft.com/documentation/articles/app-service-value-prop-what-is/ 
[ASEAutoscale]: http://azure.microsoft.com/documentation/articles/app-service-environment-auto-scale/
[ILBASE]: http://azure.microsoft.com/documentation/articles/app-service-environment-with-internal-load-balancer/
[ILBAseTemplate]: http://azure.microsoft.com/documentation/templates/201-web-app-ase-create/
[ASEfromTemplate]: http://azure.microsoft.com/documentation/articles/app-service-app-service-environment-create-ilb-ase-resourcemanager/



<!--HONumber=Dec16_HO3-->


