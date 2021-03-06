---
title: 'Azure AD Connect: puertos | Microsoft Docs'
description: "Esta página es una página de referencia técnica para puertos que deben estar abiertos para Azure AD Connect."
services: active-directory
documentationcenter: 
author: billmath
manager: femila
editor: curtand
ms.assetid: de97b225-ae06-4afc-b2ef-a72a3643255b
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/06/2016
ms.author: billmath
translationtype: Human Translation
ms.sourcegitcommit: 28b5da6098316f8fbe84966e0dac88f5b7d2cb1d
ms.openlocfilehash: 954c3d138b0fde54c1866135a20f0fc5ff05b85d


---
# <a name="hybrid-identity-required-ports-and-protocols"></a>La identidad híbrida requería puertos y protocolos
El documento siguiente es una referencia técnica sobre los puertos y protocolos para implementar una solución de identidad híbrida. Use la siguiente ilustración y consulte la tabla correspondiente.

![Qué es Azure AD Connect](./media/active-directory-aadconnect-ports/required2.png)

## <a name="table-1---azure-ad-connect-and-on-premises-ad"></a>Tabla 1: Azure AD Connect y AD local
En esta tabla se describen los puertos y protocolos que son necesarios para la comunicación entre el servidor de Azure AD Connect y AD local.

| Protocol | Puertos | Description |
| --- | --- | --- |
| DNS |53 (TCP/UDP) |Búsquedas DNS en el bosque de destino. |
| Kerberos |88 (TCP/UDP) |Autenticación Kerberos para el bosque de AD. |
| MS-RPC |135 (TCP/UDP) |Se usa durante la configuración inicial del asistente para Azure AD Connect cuando se enlaza con el bosque de AD. |
| LDAP |389 (TCP/UDP) |Se usa para la importación de datos de AD. Los datos se cifran con Kerberos Sign & Seal. |
| LDAP/SSL |636 (TCP/UDP) |Se usa para la importación de datos de AD. La transferencia de datos se firma y se cifra. Solo se utiliza si está usando SSL. |
| RPC |49152- 65535 (Puerto RCP alto aleatorio)(TCP/UDP) |Se usa durante la configuración inicial de Azure AD Connect cuando se enlaza con los bosques de AD. Consulte [KB929851](https://support.microsoft.com/kb/929851), [KB832017](https://support.microsoft.com/kb/832017) y [KB224196](https://support.microsoft.com/kb/224196) para más información. |

## <a name="table-2---azure-ad-connect-and-azure-ad"></a>Tabla 2: Azure AD Connect y Azure AD
En esta tabla se describen los puertos y protocolos que son necesarios para la comunicación entre el servidor de Azure AD Connect y Azure AD.

| Protocol | Puertos | Description |
| --- | --- | --- |
| HTTP |80 (TCP/UDP) |Se usa para descargar CRL (listas de revocación de certificados) para comprobar certificados SSL. |
| HTTPS |443 (TCP/UDP) |Se usa para sincronizar con Azure AD. |

Para una lista de direcciones URL y de direcciones IP que necesita para abrir en el firewall, consulte [URL de Office 365 e intervalos de direcciones IP](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2).

## <a name="table-3---azure-ad-connect-and-ad-fs-federation-serverswap"></a>Tabla 3: Azure AD Connect y servidores de federación AD FS/WAP
En esta tabla se describen los puertos y protocolos que son necesarios para la comunicación entre el servidor de Azure AD Connect y servidores de federación AD FS/WAP.  

| Protocolo | Puertos | Description |
| --- | --- | --- |
| HTTP |80 (TCP/UDP) |Se usa para descargar CRL (listas de revocación de certificados) para comprobar certificados SSL. |
| HTTPS |443 (TCP/UDP) |Se usa para sincronizar con Azure AD. |
| WinRM |5985 |Agente de escucha de WinRM |

## <a name="table-4---wap-and-federation-servers"></a>Tabla 4: Servidores de federación y WAP
En esta tabla se describen los puertos y protocolos que son necesarios para la comunicación entre los servidores de federación y los servidores WAP.

| Protocol | Puertos | Description |
| --- | --- | --- |
| HTTPS |443 (TCP/UDP) |Se usa para autenticación. |

## <a name="table-5---wap-and-users"></a>Tabla 5 - WAP y usuarios
En esta tabla se describen los puertos y protocolos que son necesarios para la comunicación entre los usuarios y los servidores WAP.

| Protocol | Puertos | Description |
| --- | --- | --- |
| HTTPS |443 (TCP/UDP) |Se usa para la autenticación de dispositivos. |
| TCP |49443 (TCP) |Se usa para la autenticación de certificados. |

## <a name="table-6---pass-through-authentication"></a>Tabla 6: autenticación de paso a través
En esta tabla se describen los puertos y protocolos que son necesarios para la comunicación entre el conector y Azure AD.

|Protocolo|Número de puerto|Descripción
| --- | --- | ---
|HTTP|80|Habilita el tráfico HTTP saliente para la validación de seguridad, como SSL.
|HTTPS|443| Habilita la autenticación de usuario en Azure AD.
|HTTPS|10100–10120| Habilita las respuestas del conector a Azure AD. 
|Azure Service Bus|9352, 5671|  Habilita la comunicación entre el conector hacia el servicio de Azure para las solicitudes entrantes
|HTTPS|9350|    Opcional; posibilita un mejor rendimiento de las solicitudes entrantes.
|HTTPS|8080/443|    Habilita la secuencia de arranque del conector y la actualización automática del conector
|HTTPS|9090|    Habilita el registro de conector (solo es necesario para el proceso de registro del conector)
|HTTPS|9091|    Habilita la renovación automática de certificados de confianza del conector

## <a name="table-7a--7b---azure-ad-connect-health-agent-for-ad-fssync-and-azure-ad"></a>Tabla 7a y 7b: Agente de Azure AD Connect Health para (AD FS/sincronización) y Azure AD
En las tablas siguientes se describen los puntos de conexión, puertos y protocolos que son necesarios para la comunicación entre los agentes de Azure AD Connect Health y Azure AD.

### <a name="table-7a---ports-and-protocols-for-azure-ad-connect-health-agent-for-ad-fssync-and-azure-ad"></a>Tabla 7a: Puertos y protocolos para el agente de Azure AD Connect Health para (AD FS/sincronización) y Azure AD
En esta tabla se describen los siguientes puertos y protocolos de salida que son necesarios para la comunicación entre los agentes de Azure AD Connect Health y Azure AD.  

| Protocol | Puertos | Description |
| --- | --- | --- |
| HTTPS |443 (TCP/UDP) |Salida |
| Bus de servicio |5671 (TCP/UDP) |Salida |

### <a name="7b---endpoints-for-azure-ad-connect-health-agent-for-ad-fssync-and-azure-ad"></a>7b: Puntos de conexión para el agente de Azure AD Connect Health para (AD FS/sincronización) y Azure AD
Para ver una lista de puntos de conexión, consulte [la sección de requisitos para el agente de Azure AD Connect Health](../connect-health/active-directory-aadconnect-health-agent-install.md#requirements).




<!--HONumber=Dec16_HO3-->


