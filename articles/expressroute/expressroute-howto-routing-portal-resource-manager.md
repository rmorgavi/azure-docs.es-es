---
title: "Configuración del enrutamiento de un circuito de ExpressRoute mediante Azure Portal | Microsoft Docs"
description: "Este artículo le guiará por los pasos necesarios para crear y aprovisionar las configuraciones entre pares privados, públicos y de Microsoft de un circuito ExpressRoute. Este artículo también muestra cómo comprobar el estado, actualizar, o eliminar configuraciones entre pares en el circuito."
documentationcenter: na
services: expressroute
author: cherylmc
manager: carmonm
editor: 
tags: azure-resource-manager
ms.assetid: 8c2a7ed2-ae5c-4e49-81f6-77cf9f2b2ac9
ms.service: expressroute
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/13/2016
ms.author: cherylmc
translationtype: Human Translation
ms.sourcegitcommit: ec5e547b88bedd50f451997616c7d72b0b1b4bd4
ms.openlocfilehash: 879207512b7c61d48dcd25342ab88121258ba853


---
# <a name="create-and-modify-routing-for-an-expressroute-circuit"></a>Creación y modificación del enrutamiento de un circuito ExpressRoute
> [!div class="op_single_selector"]
> * [Resource Manager: Azure Portal](expressroute-howto-routing-portal-resource-manager.md)
> * [Resource Manager - PowerShell](expressroute-howto-routing-arm.md)
> * [Modelo de implementación clásica de Azure: PowerShell](expressroute-howto-routing-classic.md)
> * [Vídeo: pares privados](http://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-set-up-azure-private-peering-for-your-expressroute-circuit)
> * [Vídeo: pares públicos](http://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-set-up-azure-public-peering-for-your-expressroute-circuit)
> * [Vídeo: emparejamiento de Microsoft](http://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-set-up-microsoft-peering-for-your-expressroute-circuit)
> 
> 

Este artículo le guiará por los pasos necesarios para crear y administrar la configuración de enrutamiento de un circuito ExpressRoute mediante el Portal de Azure y el modelo de implementación de Resource Manager.

**Información acerca de los modelos de implementación de Azure**

[!INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-classic-rm-include.md)]

## <a name="configuration-prerequisites"></a>Requisitos previos de configuración
* Antes de comenzar la configuración, asegúrese de que ha revisado la página de [requisitos previos](expressroute-prerequisites.md), la página de [requisitos de enrutamiento](expressroute-routing.md) y la página de [flujos de trabajo](expressroute-workflows.md).
* Tiene que tener un circuito ExpressRoute activo. Antes de continuar, siga las instrucciones para [crear un circuito ExpressRoute](expressroute-howto-circuit-arm.md) y para que el proveedor de conectividad habilite el circuito. El circuito ExpressRoute debe estar en un estado habilitado y aprovisionado para poder ejecutar los cmdlets que se describen a continuación.

Estas instrucciones se aplican solo a los circuitos creados con proveedores de servicios que ofrecen servicios de conectividad de capa 2. Si usa un proveedor de servicios que ofrece servicios administrados de nivel 3 (normalmente IPVPN, como MPLS), el mismo proveedor de conectividad configurará y administrará el enrutamiento. 

> [!IMPORTANT]
> Actualmente no anunciamos los emparejamientos configurados por proveedores de servicios a través del Portal de administración de servicios. Se está trabajando para habilitar esta funcionalidad pronto. Antes de configurar los emparejamientos de BGP realice las comprobaciones pertinentes con su proveedor de servicios.
> 
> 

Puede configurar una, dos o las tres configuraciones entre pares (Azure privado, Azure público y Microsoft) para un circuito ExpressRoute. Puede establecer las configuraciones entre pares en cualquier orden. Pero tiene que asegurarse de que completa cada configuración entre pares de una en una. 

## <a name="azure-private-peering"></a>Configuración entre pares privados de Azure
Esta sección proporciona instrucciones sobre cómo crear, obtener, actualizar y eliminar la configuración entre pares privados de Azure para un circuito ExpressRoute. 

### <a name="to-create-azure-private-peering"></a>Creación de un emparejamiento privado de Azure
1. Configure el circuito de ExpressRoute. Antes de continuar, asegúrese de que el proveedor de conectividad ha aprovisionado el circuito por completo.
   
    ![](./media/expressroute-howto-routing-portal-resource-manager/listprovisioned.png)
2. Establecimiento de la configuración entre pares privados de Azure para el circuito. Asegúrese de que tiene los elementos siguientes antes de continuar con los siguientes pasos:
   
   * Una subred /30 para el vínculo principal. No debe ser parte de ningún espacio de direcciones reservado para redes virtuales.
   * Una subred /30 para el vínculo secundario. No debe ser parte de ningún espacio de direcciones reservado para redes virtuales.
   * Un identificador VLAN válido para establecer esta configuración entre pares. Asegúrese de que ninguna otra configuración entre pares en el circuito usa el mismo identificador de VLAN.
   * Número de sistema autónomo (AS) para la configuración entre pares. Puede usar 2 bytes o 4 bytes como números AS. Puede usar un número AS privado para esta configuración entre pares. Asegúrese de que no usa 65515.
   * Un hash MD5, en caso de que haya decidido usarlo. **Esto es opcional**.
3. Seleccione la fila de emparejamiento privado de Azure, como se muestra a continuación.
   
    ![](./media/expressroute-howto-routing-portal-resource-manager/rprivate1.png)
4. Configure el emparejamiento privado. La imagen siguiente muestra un ejemplo de configuración.
   
    ![](./media/expressroute-howto-routing-portal-resource-manager/rprivate2.png)
5. Guarde la configuración una vez que haya especificado todos los parámetros. Una vez que la configuración se haya aceptado correctamente, verá algo similar al siguiente ejemplo.
   
    ![](./media/expressroute-howto-routing-portal-resource-manager/rprivate3.png)

### <a name="to-view-azure-private-peering-details"></a>Visualización de los detalles del emparejamiento privado
Para ver las propiedades del emparejamiento privado de Azure seleccione el emparejamiento.

![](./media/expressroute-howto-routing-portal-resource-manager/rprivate3.png)

### <a name="to-update-azure-private-peering-configuration"></a>Actualización del establecimiento de configuración del emparejamiento privado de Azure
Puede seleccionar la fila del emparejamiento y modificar las propiedades del mismo. 

![](./media/expressroute-howto-routing-portal-resource-manager/rprivate2.png)

### <a name="to-delete-azure-private-peering"></a>Eliminación del emparejamiento privado de Azure
Para quitar la configuración del emparejamiento, seleccione el icono de eliminación, como se muestra a continuación.

![](./media/expressroute-howto-routing-portal-resource-manager/rprivate4.png)

## <a name="azure-public-peering"></a>Configuración entre pares públicos de Azure
En esta sección se proporcionan instrucciones sobre cómo crear, obtener, actualizar y eliminar la configuración del emparejamiento público de Azure para un circuito ExpressRoute. 

### <a name="to-create-azure-public-peering"></a>Creación de un emparejamiento público de Azure
1. Configure un circuito de ExpressRoute. Antes de continuar, asegúrese de que el proveedor de que el proveedor de conectividad ha aprovisionado el circuito por completo.
   
    ![](./media/expressroute-howto-routing-portal-resource-manager/listprovisioned.png)
2. Establezca la configuración del emparejamiento publico de Azure para el circuito. Asegúrese de que tiene los elementos siguientes antes de continuar con los siguientes pasos:
   
   * Una subred /30 para el vínculo principal. 
   * Una subred /30 para el vínculo secundario. 
   * Todas las direcciones IP usadas para configurar este emparejamiento deben ser direcciones IPv4 públicas válidas.
   * Un identificador VLAN válido para establecer esta configuración entre pares. Asegúrese de que ninguna otra configuración entre pares en el circuito usa el mismo identificador de VLAN.
   * Número de sistema autónomo (AS) para la configuración entre pares. Puede usar 2 bytes o 4 bytes como números AS.
   * Un hash MD5, en caso de que haya decidido usarlo. **Esto es opcional**.
3. Seleccione la fila de emparejamiento público de Azure, como se muestra a continuación.
   
    ![](./media/expressroute-howto-routing-portal-resource-manager/rpublic1.png)
4. Configure el emparejamiento público. La imagen siguiente muestra un ejemplo de configuración.
   
    ![](./media/expressroute-howto-routing-portal-resource-manager/rpublic2.png)
5. Guarde la configuración una vez que haya especificado todos los parámetros. Una vez que la configuración se haya aceptado correctamente, verá algo similar al siguiente ejemplo.
   
    ![](./media/expressroute-howto-routing-portal-resource-manager/rpublic3.png)

### <a name="to-view-azure-public-peering-details"></a>Visualización de detalles de un emparejamiento público de Azure
Para ver las propiedades de un emparejamiento público de Azure, seleccione el emparejamiento.

![](./media/expressroute-howto-routing-portal-resource-manager/rpublic3.png)

### <a name="to-update-azure-public-peering-configuration"></a>Actualización del establecimiento de configuración del emparejamiento público de Azure
Puede seleccionar la fila del emparejamiento y modificar las propiedades del mismo. 

![](./media/expressroute-howto-routing-portal-resource-manager/rpublic2.png)

### <a name="to-delete-azure-public-peering"></a>Eliminación del emparejamiento público de Azure
Para quitar la configuración del emparejamiento, seleccione el icono de eliminación, como se muestra a continuación.

![](./media/expressroute-howto-routing-portal-resource-manager/rpublic4.png)

## <a name="microsoft-peering"></a>Emparejamiento de Microsoft
En esta sección se proporcionan instrucciones sobre cómo crear, obtener, actualizar y eliminar la configuración del emparejamiento de Microsoft para un circuito ExpressRoute. 

### <a name="to-create-microsoft-peering"></a>Creación del emparejamiento de Microsoft
1. Configure un circuito de ExpressRoute. Antes de continuar, asegúrese de que el proveedor de que el proveedor de conectividad ha aprovisionado el circuito por completo.
   
    ![](./media/expressroute-howto-routing-portal-resource-manager/listprovisioned.png)
2. Establezca la configuración del emparejamiento de Microsoft para el circuito. Asegúrese de que tiene la siguiente información antes de empezar:
   
   * Una subred /30 para el vínculo principal. Debe ser un prefijo de IPv4 público válido que sea de su propiedad y esté registrado en un Registro regional de Internet (RIR) o un Registro de enrutamiento de Internet (IRR).
   * Una subred /30 para el vínculo secundario. Debe ser un prefijo de IPv4 público válido que sea de su propiedad y esté registrado en un Registro regional de Internet (RIR) o un Registro de enrutamiento de Internet (IRR).
   * Un identificador VLAN válido para establecer esta configuración entre pares. Asegúrese de que ninguna otra configuración entre pares en el circuito usa el mismo identificador de VLAN.
   * Número de sistema autónomo (AS) para la configuración entre pares. Puede usar 2 bytes o 4 bytes como números AS.
   * **Prefijos anunciados:** tiene que proporcionar una lista de todos los prefijos que planea anunciar en la sesión BGP. Se aceptan solo prefijos de direcciones IP públicas. Puede enviar una lista separada por comas si tiene pensado enviar un conjunto de prefijos. Estos prefijos tienen que estar registrados a su nombre en un Registro regional de Internet (RIR) o un Registro de enrutamiento de Internet (IRR).
   * **Cliente ASN** : si se anuncian prefijos que no están registrados en el número de AS de configuración entre pares, puede especificar el número de AS en el que están registrados. **Esto es opcional**.
   * **Nombre del enrutamiento del registro:** puede especificar el RIR o TIR en el que están registrados el número AS y los prefijos. **Esto es opcional.**
   * Un hash MD5, en caso de que haya decidido usarlo. **Esto es opcional.**
3. Puede seleccionar el emparejamiento que desea configurar, como se muestra a continuación. Seleccione la fila del emparejamiento de Microsoft.
   
    ![](./media/expressroute-howto-routing-portal-resource-manager/rmicrosoft1.png)
4. Configure el emparejamiento de Microsoft La imagen siguiente muestra un ejemplo de configuración.
   
   ![](./media/expressroute-howto-routing-portal-resource-manager/rmicrosoft2.png)
5. Guarde la configuración una vez que haya especificado todos los parámetros. 
   
    Si el circuito llega a un estado de validación necesaria (como se muestra a continuación), debe abrir una incidencia de soporte técnico para mostrar la prueba de propiedad de los prefijos a nuestro equipo de soporte técnico.    
   
    ![](./media/expressroute-howto-routing-portal-resource-manager/rmicrosoft5.png)

    Puede abrir una incidencia de soporte técnico directamente desde el portal, tal como se muestra a continuación     

    ![](./media/expressroute-howto-routing-portal-resource-manager/rmicrosoft6.png)


1. Una vez que la configuración se haya aceptado correctamente, verá algo similar al siguiente ejemplo.
   
    ![](./media/expressroute-howto-routing-portal-resource-manager/rmicrosoft7.png)

### <a name="to-view-microsoft-peering-details"></a>Visualización de detalles del emparejamiento de Microsoft
Para ver las propiedades de un emparejamiento público de Azure, seleccione el emparejamiento.

![](./media/expressroute-howto-routing-portal-resource-manager/rmicrosoft3.png)

### <a name="to-update-microsoft-peering-configuration"></a>Actualización de la configuración de emparejamiento de Microsoft
Puede seleccionar la fila del emparejamiento y modificar las propiedades del mismo. 

![](./media/expressroute-howto-routing-portal-resource-manager/rmicrosoft7.png)

### <a name="to-delete-microsoft-peering"></a>Eliminación del emparejamiento de Microsoft
Para quitar la configuración del emparejamiento, seleccione el icono de eliminación, como se muestra a continuación.

![](./media/expressroute-howto-routing-portal-resource-manager/rmicrosoft4.png)

## <a name="next-steps"></a>Pasos siguientes
Siguiente paso, [Vinculación de redes virtuales a circuitos ExpressRoute](expressroute-howto-linkvnet-arm.md).

* Para más información sobre los flujos de trabajo de ExpressRoute, vea [Flujos de trabajo de ExpressRoute](expressroute-workflows.md).
* Para más información sobre el emparejamiento de circuitos, vea [Circuitos y dominios de enrutamiento de ExpressRoute](expressroute-circuit-peerings.md).
* Para más información sobre redes virtuales, vea [Información general sobre redes virtuales](../virtual-network/virtual-networks-overview.md).




<!--HONumber=Dec16_HO2-->


