---
title: Emparejamiento de VNet mediante cmdlets de Powershell | Microsoft Docs
description: Aprenda a crear una red virtual mediante el Portal de Azure en Resource Manager.
services: virtual-network
documentationcenter: 
author: NarayanAnnamalai
manager: jefco
editor: 
tags: azure-resource-manager
ms.assetid: dac579bd-7545-461a-bdac-301c87434c84
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/14/2016
ms.author: narayan; annahar
translationtype: Human Translation
ms.sourcegitcommit: 3fe204c09eebf7d254a1bf2bb130e2d3498b6b45
ms.openlocfilehash: c2ccb107cbfec16440d5ff5ffe43a9009b5c4e48


---
# <a name="create-vnet-peering-using-powershell-cmdlets"></a>Emparejamiento de VNet mediante cmdlets de Powershell
[!INCLUDE [virtual-networks-create-vnet-selectors-arm-include](../../includes/virtual-networks-create-vnetpeering-selectors-arm-include.md)]

[!INCLUDE [virtual-networks-create-vnet-intro](../../includes/virtual-networks-create-vnetpeering-intro-include.md)]

[!INCLUDE [virtual-networks-create-vnet-scenario-basic-include](../../includes/virtual-networks-create-vnetpeering-scenario-basic-include.md)]

Para el emparejamiento de VNet mediante PowerShell, siga estos pasos:

1. Si es la primera vez que usa Azure PowerShell, consulte [Cómo instalar y configurar Azure PowerShell](/powershell/azureps-cmdlets-docs) y siga las instrucciones hasta el final para iniciar sesión en Azure y seleccionar su suscripción.

    > [!NOTE]
    > El cmdlet de PowerShell para administrar el emparejamiento de VNET se suministra con [Azure PowerShell 1.6](http://www.powershellgallery.com/packages/Azure/1.6.0).
    >

1. Leer objetos de redes virtuales:
   
        $vnet1 = Get-AzureRmVirtualNetwork -ResourceGroupName vnet101 -Name vnet1
        $vnet2 = Get-AzureRmVirtualNetwork -ResourceGroupName vnet101 -Name vnet2
2. Para establecer el emparejamiento de VNET es preciso crear dos vínculos, uno para cada dirección. En el siguiente paso, primero se creará un vínculo del emparejamiento de VNET de VNet1 a VNet2:
   
        Add-AzureRmVirtualNetworkPeering -Name LinkToVNet2 -VirtualNetwork $vnet1 -RemoteVirtualNetworkId $vnet2.Id
   
    La salida muestra:
   
        Name            : LinkToVNet2
        Id: /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/vnet101/providers/Microsoft.Network/virtualNetworks/vnet1/virtualNetworkPeerings/LinkToVNet2
        Etag            : W/"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
        ResourceGroupName    : vnet101
        VirtualNetworkName    : vnet1
        PeeringState        : Initiated
        ProvisioningState    : Succeeded
        RemoteVirtualNetwork    : {
                                            "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/vnet101/providers/Microsoft.Network/virtualNetworks/vnet2"
                                        }
        AllowVirtualNetworkAccess    : True
        AllowForwardedTraffic    : False
        AllowGatewayTransit    : False
        UseRemoteGateways    : False
        RemoteGateways        : null
        RemoteVirtualNetworkAddressSpace : null
3. En este paso se creará un vínculo del emparejamiento de VNET de VNet2 a VNet1:
   
        Add-AzureRmVirtualNetworkPeering -Name LinkToVNet1 -VirtualNetwork $vnet2 -RemoteVirtualNetworkId $vnet1.Id
   
    La salida muestra:
   
        Name            : LinkToVNet1
        Id                : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/vnet101/providers/Microsoft.Network/virtualNetworks/vnet2/virtualNetworkPeerings/LinkToVNet1
        Etag            : W/"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
        ResourceGroupName    : vnet101
        VirtualNetworkName    : vnet2
        PeeringState        : Connected
        ProvisioningState    : Succeeded
        RemoteVirtualNetwork    : {
                                            "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/vnet101/providers/Microsoft.Network/virtualNetworks/vnet1"
                                        }
        AllowVirtualNetworkAccess    : True
        AllowForwardedTraffic    : False
        AllowGatewayTransit    : False
        UseRemoteGateways    : False
        RemoteGateways        : null
        RemoteVirtualNetworkAddressSpace : null
4. Una vez creado el vínculo de emparejamiento de VNET, puede ver el estado del vínculo:
   
        Get-AzureRmVirtualNetworkPeering -VirtualNetworkName vnet1 -ResourceGroupName vnet101 -Name linktovnet2
   
    La salida muestra:
   
        Name            : LinkToVNet2
        Id                : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/vnet101/providers/Microsoft.Network/virtualNetworks/vnet1/virtualNetworkPeerings/LinkToVNet2
        Etag            : W/"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
        ResourceGroupName    : vnet101
        VirtualNetworkName    : vnet1
        PeeringState        : Connected
        ProvisioningState    : Succeeded
        RemoteVirtualNetwork    : {
                                             "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/vnet101/providers/Microsoft.Network/virtualNetworks/vnet2"
                                        }
        AllowVirtualNetworkAccess    : True
        AllowForwardedTraffic            : False
        AllowGatewayTransit              : False
        UseRemoteGateways                : False
        RemoteGateways                   : null
        RemoteVirtualNetworkAddressSpace : null
   
    Hay algunas propiedades configurables para el emparejamiento de VNet:
   
   | Opción | Description | Valor predeterminado |
   |:--- |:--- |:--- |
   | AllowVirtualNetworkAccess |Si el espacio de direcciones de red virtual emparejada se incluye como parte de la etiqueta Virtual_network. |Sí |
   | AllowForwardedTraffic |Si el tráfico que no parte de una red virtual emparejada se acepta o rechaza. |No |
   | AllowGatewayTransit |Permite a la red virtual emparejada usar la puerta de enlace de red virtual. |No |
   | UseRemoteGateways |Se usa la puerta de enlace de la red virtual emparejada. La red virtual emparejada debe tener configurada una puerta de enlace y AllowGatewayTransit debe estar seleccionado. No se puede utilizar esta opción si tiene configurada una puerta de enlace. |No |
   
    Cada vínculo de emparejamiento de VNET tiene el conjunto de propiedades anteriores. Por ejemplo, puede establecer AllowVirtualNetworkAccess en True para el vínculo de emparejamiento de VNET entre VNet1 y VNet2, y en False para el vínculo de emparejamiento de VNET en la otra dirección.
   
        $LinktoVNet2 = Get-AzureRmVirtualNetworkPeering -VirtualNetworkName vnet1 -ResourceGroupName vnet101 -Name LinkToVNet2
        $LinktoVNet2.AllowForwardedTraffic = $true
        Set-AzureRmVirtualNetworkPeering -VirtualNetworkPeering $LinktoVNet2
   
    Puede ejecutar Get-AzureRmVirtualNetworkPeering para volver a comprobar el valor de las propiedades después del cambio. En la salida puede ver que AllowForwardedTraffic cambia a True después de ejecutar los cmdlets anteriores.
   
        Name            : LinkToVNet2
        Id            : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/vnet101/providers/Microsoft.Network/virtualNetworks/vnet1/virtualNetworkPeerings/LinkToVNet2
        Etag            : W/"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
        ResourceGroupName    : vnet101
        VirtualNetworkName    : vnet1
        PeeringState        : Connected
        ProvisioningState    : Succeeded
        RemoteVirtualNetwork    : {
                                            "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/vnet101/providers/Microsoft.Network/virtualNetworks/vnet2"
                                        }
        AllowVirtualNetworkAccess    : True
        AllowForwardedTraffic        : True
        AllowGatewayTransit        : False
        UseRemoteGateways        : False
        RemoteGateways        : null
        RemoteVirtualNetworkAddressSpace : null
   
    Después de que se establece el emparejamiento en este escenario, debería poder iniciar la conexión entre dos máquinas virtuales cualquiera de ambas redes virtuales. De manera predeterminada, el valor de AllowVirtualNetworkAccess es True y el emparejamiento de VNET aprovisionará las ACL adecuadas para permitir la comunicación entre redes virtuales. Se pueden seguir aplicando las reglas de grupo de seguridad de red (NSG) para bloquear la conectividad entre subredes o máquinas virtuales concretas para conseguir un mayor control del acceso entre dos redes virtuales.  Para más información acerca de la creación de reglas de NSG, consulte este [artículo](virtual-networks-create-nsg-arm-ps.md).

[!INCLUDE [virtual-networks-create-vnet-scenario-crosssub-include](../../includes/virtual-networks-create-vnetpeering-scenario-crosssub-include.md)]

Para crear un emparejamiento de VNET entre suscripciones mediante PowerShell, siga estos pasos:

1. Inicie sesión en Azure con la cuenta de UserA con privilegios para la suscripción A y ejecute el siguiente cmdlet:
   
        New-AzureRmRoleAssignment -SignInName <UserB ID> -RoleDefinitionName "Network Contributor" -Scope /subscriptions/<Subscription-A-ID>/resourceGroups/<ResourceGroupName>/providers/Microsoft.Network/VirtualNetworks/VNet5
   
    Esto no es un requisito; se puede establecer el emparejamiento aunque los usuarios realicen individualmente solicitudes de emparejamiento de sus respectivas redes virtuales, siempre y cuando las solicitudes coincidan. La adición de usuarios con privilegios de la otra red virtual como usuarios de la red virtual local facilita la configuración.
2. Inicie sesión en Azure con la cuenta de UserB con privilegios para la suscripción B y ejecute el siguiente cmdlet:
   
        New-AzureRmRoleAssignment -SignInName <UserA ID> -RoleDefinitionName "Network Contributor" -Scope /subscriptions/<Subscription-B-ID>/resourceGroups/<ResourceGroupName>/providers/Microsoft.Network/VirtualNetworks/VNet3
3. En la sesión de inicio de UserA, ejecute el cmdlet siguiente:
   
        $vnet3 = Get-AzureRmVirtualNetwork -ResourceGroupName hr-vnets -Name vnet3
   
        Add-AzureRmVirtualNetworkPeering -Name LinkToVNet5 -VirtualNetwork $vnet3 -RemoteVirtualNetworkId "/subscriptions/<Subscription-B-Id>/resourceGroups/<ResourceGroupName>/providers/Microsoft.Network/virtualNetworks/VNet5" -BlockVirtualNetworkAccess
4. En la sesión de inicio de UserB, ejecute el cmdlet siguiente:
   
        $vnet5 = Get-AzureRmVirtualNetwork -ResourceGroupName vendor-vnets -Name vnet5
   
        Add-AzureRmVirtualNetworkPeering -Name LinkToVNet3 -VirtualNetwork $vnet5 -RemoteVirtualNetworkId "/subscriptions/<Subscriptoin-A-Id>/resourceGroups/<ResourceGroupName>/providers/Microsoft.Network/virtualNetworks/VNet3" -BlockVirtualNetworkAccess
5. Después de que se establezca el emparejamiento, todas las máquinas virtuales de VNet3 deberían poder comunicarse con cualquier máquina virtual de VNet5.

[!INCLUDE [virtual-networks-create-vnet-scenario-transit-include](../../includes/virtual-networks-create-vnetpeering-scenario-transit-include.md)]

1. En este escenario, se pueden ejecutar los cmdlets de PowerShell siguientes para establecer el emparejamiento de VNET.  La propiedad AllowForwardedTraffic debe tener el valor True y VNET1 se debe vincular a HubVNet, lo que permite el tráfico entrante desde fuera del espacio de direcciones de red virtual de emparejamiento.
   
        $hubVNet = Get-AzureRmVirtualNetwork -ResourceGroupName vnet101 -Name HubVNet
        $vnet1 = Get-AzureRmVirtualNetwork -ResourceGroupName vnet101 -Name vnet1
   
        Add-AzureRmVirtualNetworkPeering -Name LinkToHub -VirtualNetwork $vnet1 -RemoteVirtualNetworkId $HubVNet.Id -AllowForwardedTraffic
   
        Add-AzureRmVirtualNetworkPeering -Name LinkToVNet1 -VirtualNetwork $HubVNet -RemoteVirtualNetworkId $vnet1.Id
2. Después de establecer el emparejamiento, puede consultar este [artículo](virtual-network-create-udr-arm-ps.md) y establecer la ruta definida por el usuario (UDR) para redirigir el tráfico de VNet1 a través de una aplicación virtual para usar sus funcionalidades. Cuando especifique la dirección del próximo salto en la ruta, puede establecerla en la dirección IP de la aplicación virtual del HubVNet de la red virtual emparejada. Ejemplo:
   
        $route = New-AzureRmRouteConfig -Name TestNVA -AddressPrefix 10.3.0.0/16 -NextHopType VirtualAppliance -NextHopIpAddress 192.0.1.5
   
        $routeTable = New-AzureRmRouteTable -ResourceGroupName VNet101 -Location brazilsouth -Name TestRT -Route $route
   
        $vnet1 = Get-AzureRmVirtualNetwork -ResourceGroupName VNet101 -Name VNet1
   
        Set-AzureRmVirtualNetworkSubnetConfig -VirtualNetwork $vnet1 -Name subnet-1 -AddressPrefix 10.1.1.0/24 -RouteTable $routeTable
   
        Set-AzureRmVirtualNetwork -VirtualNetwork $vnet1

[!INCLUDE [virtual-networks-create-vnet-scenario-asmtoarm-include](../../includes/virtual-networks-create-vnetpeering-scenario-asmtoarm-include.md)]

Para crear un emparejamiento de red virtual entre una red virtual clásica y una de Azure Resource Manager en PowerShell, siga estos pasos:

1. Lea el objeto de red virtual para **VNET1**, la red virtual de Azure Resource Manager, como sigue:
   
        $vnet1 = Get-AzureRmVirtualNetwork -ResourceGroupName vnet101 -Name vnet1
2. Para establecer el emparejamiento de redes virtuales en este escenario, se necesita solo un vínculo y, más concretamente, un vínculo desde **VNET1** a **VNET2**. Este paso requiere conocer el identificador de recurso de la red virtual clásica. El formato del identificador del grupo de recursos se parece a este:
   
        /subscriptions/{SubscriptionID}/resourceGroups/{ResourceGroupName}/providers/Microsoft.ClassicNetwork/virtualNetworks/{VirtualNetworkName}
   
    Asegúrese de reemplazar SubscriptionID, ResourceGroupName y VirtualNetworkName por los nombres correspondientes.
   
    Esto se puede lograr de la siguiente manera:
   
        Add-AzureRmVirtualNetworkPeering -Name LinkToVNet2 -VirtualNetwork $vnet1 -RemoteVirtualNetworkId /subscriptions/xxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxx/resourceGroups/MyResourceGroup/providers/Microsoft.ClassicNetwork/virtualNetworks/VNET2
3. Una vez creado el vínculo de emparejamiento de la red virtual, puede ver el estado del vínculo como se indica en la siguiente salida:
   
        Name                             : LinkToVNet2
        Id                               : /subscriptions/xxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxx/resourceGroups/MyResourceGroup/providers/Microsoft.Network/virtualNetworks/VNET1/virtualNetworkPeerings/LinkToVNet2
        Etag                             : W/"acecbd0f-766c-46be-aa7e-d03e41c46b16"
        ResourceGroupName                : MyResourceGroup
        VirtualNetworkName               : VNET1
        PeeringState                     : Connected
        ProvisioningState                : Succeeded
        RemoteVirtualNetwork             : {
                                         "Id": "/subscriptions/xxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxx/resourceGroups/MyResourceGroup/providers/Microsoft.ClassicNetwork/virtualNetworks/VNET2"
                                       }
        AllowVirtualNetworkAccess        : True
        AllowForwardedTraffic            : False
        AllowGatewayTransit              : False
        UseRemoteGateways                : False
        RemoteGateways                   : null
        RemoteVirtualNetworkAddressSpace : null

## <a name="remove-vnet-peering"></a>Desemparejamiento de VNET
1. Para desemparejar las redes virtuales, es preciso ejecutar el siguiente cmdlet:
   
     Remove-AzureRmVirtualNetworkPeering  
   
     Quite los vínculos utilizando los comandos siguientes:
   
     Remove-AzureRmVirtualNetworkPeering -ResourceGroupName vnet101 -VirtualNetworkName vnet1 -Name linktovnet2   Remove-AzureRmVirtualNetworkPeering -ResourceGroupName vnet101 -VirtualNetworkName vnet1 -Name linktovnet2
2. Una vez que se quita un vínculo de un emparejamiento de VNET, el estado del vínculo del emparejamiento cambia a desconectado. En este estado, no se puede volver a crear el vínculo hasta que cambie el estado del vínculo de emparejamiento a iniciado. Se recomienda quitar ambos vínculos antes de volver a crear el emparejamiento de VNET.




<!--HONumber=Dec16_HO1-->


