---
title: Acceso condicional de Azure Active Directory | Microsoft Docs
description: "Use el control de acceso condicional en Azure Active Directory para comprobar la existencia de condiciones específicas durante la autenticación para acceder a aplicaciones."
services: active-directory
keywords: acceso condicional a aplicaciones, acceso condicional con Azure AD, acceso seguro a recursos de empresa, directivas de acceso condicional
documentationcenter: 
author: MarkusVi
manager: femila
editor: 
ms.assetid: 8c1d978f-e80b-420e-853a-8bbddc4bcdad
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/22/2016
ms.author: markvi
translationtype: Human Translation
ms.sourcegitcommit: 0ecbaaf030e5c87ff05228af852477b865329596
ms.openlocfilehash: 3b06c7c32c6ec27659365ca4da6193457fff7162


---
# <a name="conditional-access-in-azure-active-directory---preview"></a>Acceso condicional en Azure Active Directory: versión preliminar

> [!div class="op_single_selector"]
> * [Portal de Azure](active-directory-conditional-access-azure-portal.md)
> * [Portal de Azure clásico](active-directory-conditional-access.md)


El comportamiento descrito en este tema está actualmente en [versión preliminar](active-directory-preview-explainer.md).

En un mundo Mobile First, Cloud First, Azure Active Directory permite el inicio de sesión único en dispositivos, aplicaciones y servicios desde cualquier parte. Con la proliferación de dispositivos (como BYOD), el trabajo fuera de las redes corporativas y las aplicaciones SaaS de terceros, los profesionales de TI se enfrentan con dos objetivos opuestos:

- Permitir que los usuarios finales sean productivos en cualquier lugar y en cualquier momento.
- Proteger los activos corporativos en todo momento.

Para mejorar la productividad, Azure Active Directory proporciona a los usuarios una amplia variedad de opciones de acceso a los recursos corporativos. Con la administración del acceso a las aplicaciones, Azure Active Directory le permite asegurarse de que solo *las personas adecuadas* puedan acceder a sus aplicaciones. ¿Y si quiere tener un mayor control sobre el modo en que las personas adecuadas acceden a sus recursos en determinadas condiciones? ¿Y si se dan situaciones en las que quiere bloquear el acceso a determinadas aplicaciones incluso a las *personas adecuadas*? Por ejemplo, podría querer que las personas adecuadas accedan a determinadas aplicaciones desde una red de confianza y no querer que accedan a esas mismas aplicaciones desde una red en la que no confía. También puede abordar estas cuestiones mediante el acceso condicional. 

El acceso condicional es una funcionalidad de Azure Active Directory que le permite aplicar controles en el acceso a las aplicaciones de su entorno según condiciones específicas. Mediante controles, puede enlazar requisitos adicionales con el acceso o puede bloquearlo. La implementación del acceso condicional se basa en directivas. Un enfoque basado en directivas simplifica su experiencia de configuración porque sigue su modo de pensar sobre los requisitos de acceso.  
Normalmente, los requisitos de acceso se definen mediante declaraciones basadas en el siguiente patrón:

![Control](./media/active-directory-conditional-access-azure-portal/10.png)

Al reemplazar las dos repeticiones de "*esto*" por información del mundo real, tiene un ejemplo de una declaración de directiva que probablemente le será familiar:

*Cuando los contratistas intentan acceder a nuestras aplicaciones de nube desde redes que no son de confianza, se bloquea el acceso.*

La declaración de directiva anterior destaca la eficacia del acceso condicional. Aunque puede permitir que los contratistas accedan de forma básica a sus aplicaciones de nube (**quién**), mediante acceso condicional, también puede definir condiciones bajo las cuales es posible el acceso (**cómo**). 

En el contexto del acceso condicional de Azure Active Directory, 

- "**Cuando esto sucede**" se denomina **declaración de condición**
- "**Entonces haga esto**" se denomina **controles**.

![Control](./media/active-directory-conditional-access-azure-portal/11.png)

La combinación de una declaración de condición con los controles representa una directiva de acceso condicional.

![Control](./media/active-directory-conditional-access-azure-portal/12.png)


## <a name="controls"></a>Controles

En una directiva de acceso condicional, los controles definen qué es lo que debe suceder cuando se ha satisfecho una declaración de condición.  
Con controles, puede bloquear el acceso o permitir el acceso con requisitos adicionales.
Cuando configura una directiva que permite el acceso, debe seleccionar al menos un requisito.   

La implementación actual de Azure Active Directory le permite configurar los siguientes requisitos: 

![Control](./media/active-directory-conditional-access-azure-portal/05.png)

- **Multi-factor Authentication**: pude exigir autenticación fuerte mediante la autenticación multifactor. Como proveedor, puede usar la autenticación multifactor de Azure o un proveedor de autenticación multifactor de terceros, en combinación con Active Directory Federation Services (AD FS). La autenticación multifactor ayuda a proteger los recursos ante el acceso por parte de un usuario no autorizado que haya conseguido el nombre de usuario y la contraseña de un usuario válido.

- **Dispositivo compatible**: puede establecer directivas de acceso condicional en el nivel de dispositivo. Podría configurar una directiva para permitir que solo los equipos compatibles, o los dispositivos móviles que estén inscritos en una aplicación de administración de dispositivos móviles, puedan acceder a los recursos de su organización. Por ejemplo, puede usar Intune para comprobar el cumplimiento del dispositivo y después notificarlo a Azure AD para que aplique la directiva cuando el usuario intente acceder a una aplicación. Para ver instrucciones detalladas sobre cómo usar Intune para proteger datos y aplicaciones, consulte Proteger aplicaciones y datos con Microsoft Intune. También puede usar Intune para aplicar la protección de datos a dispositivos perdidos o robados. Para más información, consulte Ayudar a proteger los datos con el borrado selectivo o completo mediante Microsoft Intune.

- **Dispositivo unido a un dominio**: puede exigir que el dispositivo que ha usado para conectarse a Azure Active Directory sea un dispositivo unido a un dominio. Esta directiva se aplica a los escritorios, portátiles y tabletas de empresa Windows. Para más información sobre cómo configurar el registro automático de dispositivos unidos a un dominio con Azure AD, consulte [Registro automático de dispositivos en Azure Active Directory para dispositivos Windows unidos a un dominio](active-directory-conditional-access-automatic-device-registration.md).

Si tiene más de un requisito seleccionado en una directiva de acceso condicional, también puede configurar cómo se aplicarán. Puede elegir que se exijan todos los controles seleccionados o uno de ellos.

![Control](./media/active-directory-conditional-access-azure-portal/06.png)


## <a name="condition-statement"></a>Declaración de condición

En la sección anterior se han presentado las opciones admitidas para bloquear o restringir el acceso a los recursos en forma de controles. En una directiva de acceso condicional, se definen los criterios que es necesario satisfacer para que los controles se apliquen en forma de una declaración de condición.  

Puede incluir las siguientes asignaciones en la declaración de condición:
    
![Control](./media/active-directory-conditional-access-azure-portal/07.png)


- **Quién**: en muchos casos, querrá que sus controles se apliquen a un conjunto específico de usuarios. En una declaración de condición, puede definir este control seleccionando los usuarios y grupos a los que se aplica la directiva. Si es necesario, también puede eximir un conjunto de usuarios de su directiva si los excluye explícitamente.  
Al seleccionar usuarios y grupos, se define el ámbito de los usuarios a los que se aplica la directiva.    

    ![Control](./media/active-directory-conditional-access-azure-portal/08.png)



- **Qué**: normalmente, hay determinadas aplicaciones que se ejecutan en el entorno que requieren más atención que otras, desde la perspectiva de la protección. Esto afecta, por ejemplo, a las aplicaciones que tienen acceso a datos confidenciales. Al seleccionar aplicaciones de nube, se define el ámbito de las aplicaciones de nube a las que se aplica la directiva. Si es necesario, también puede excluir explícitamente un conjunto de aplicaciones de su directiva. 

    ![Control](./media/active-directory-conditional-access-azure-portal/09.png)


- **Cómo**: siempre y cuando el acceso se realice bajo condiciones que pueda controlar, puede que no haya necesidad de imponer controles adicionales sobre cómo los usuarios accederán a las aplicaciones. Sin embargo, las cosas podrían cambiar si el acceso a sus aplicaciones de nube se realiza, por ejemplo, desde redes o dispositivos que no son de confianza. En una declaración de condición, puede definir determinadas condiciones de acceso que tienen requisitos adicionales respecto al modo en que se realiza el acceso a las aplicaciones.

    ![Condiciones](./media/active-directory-conditional-access-azure-portal/01.png)


## <a name="conditions"></a>Condiciones

En la implementación actual de Azure Active Directory, puede definir condiciones para las siguientes áreas:


- **Plataformas de dispositivos**: la plataforma de dispositivos se caracteriza por el sistema operativo que se ejecuta en el dispositivo (Android, iOS, Windows Phone, Windows). Puede definir las plataformas de dispositivos que se incluyen, así como las plataformas de dispositivos que se excluyen de una directiva.  
Para usar plataformas de dispositivos en la directiva, primero cambie los conmutadores de configuración a **Sí** y, luego, seleccione las plataformas de dispositivos a las que se aplica la directiva (puede ser todas o algunas de ellas). Si selecciona algunas plataformas de dispositivos, la directiva solo afecta a estas plataformas. En este caso, la directiva no afecta a los inicios de sesión en otras plataformas compatibles. 

    ![Condiciones](./media/active-directory-conditional-access-azure-portal/02.png)

- **Ubicaciones**: la ubicación se identifica mediante la dirección IP del cliente que ha usado para conectar con Azure Active Directory. Para esta condición es necesario estar familiarizado con IP de confianza. IP de confianza es una característica de autenticación multifactor que permite definir intervalos de direcciones IP de confianza que representan la intranet local de su organización. Cuando se configura una condición de ubicación, IP de confianza le permite distinguir entre conexiones realizadas desde la red de su organización y todas las demás ubicaciones. Para más información, consulte [IP de confianza](../multi-factor-authentication/multi-factor-authentication-whats-next.md#trusted-ips).  
Puede incluir todas las ubicaciones o todas las direcciones IP de confianza y puede excluir todas las direcciones IP de confianza.

    ![Condiciones](./media/active-directory-conditional-access-azure-portal/03.png)


- **Aplicación cliente**: la aplicación cliente puede estar en un nivel genérico, es decir, la aplicación (explorador web, aplicación móvil, cliente de escritorio) que ha usado para conectarse a Azure Active Directory, o puede seleccionar específicamente Exchange Active Sync.  
La autenticación heredada hace referencia a clientes que usan autenticación básica, como clientes de Office antiguos que no usan autenticación moderna. El acceso condicional no se admite actualmente con la autenticación heredada.

    ![Condiciones](./media/active-directory-conditional-access-azure-portal/04.png)


## <a name="what-you-should-know"></a>Qué debería saber

### <a name="do-i-need-to-assign-a-user-to-my-policy"></a>¿Es necesario asignar un usuario a la directiva?

Al configurar una directiva de acceso condicional, debe asignarle al menos un grupo. Una directiva de acceso condicional que no tiene usuarios ni grupos asignados nunca se desencadena.

Cuando pretenda asignar varios usuarios y grupos a una directiva, debe comenzar por poco y asignar un solo usuario o grupo y luego probar su configuración. Si la directiva funciona según lo previsto, puede agregarle asignaciones adicionales.  


### <a name="how-are-assignments-evaluated"></a>¿Cómo se evalúan las asignaciones?

A todas las asignaciones se les asigna **la operación lógica AND**. Si tiene más de una asignación configurada, se deben satisfacer todas las asignaciones para desencadenar una directiva.  

Si debe configurar una condición de ubicación que se aplica a todas las conexiones realizadas desde fuera de la red de la organización, puede:

- Incluir **todas las ubicaciones**.
- Excluir **todas las direcciones IP de confianza**. 

### <a name="what-happens-if-you-have-policies-in-the-azure-classic-portal-and-azure-portal-configured"></a>¿Qué ocurre si tiene directivas configuradas en el Portal de Azure clásico y en Azure Portal?  
Las dos directivas se aplican mediante Azure Active Directory y el usuario obtiene acceso únicamente cuando se cumplen todos los requisitos.

### <a name="what-happens-if-you-have-policies-in-the-intune-silverlight-portal-and-the-azure-portal"></a>¿Qué sucede si tiene directivas en el portal de Intune Silverlight y en Azure Portal?
Las dos directivas se aplican mediante Azure Active Directory y el usuario obtiene acceso únicamente cuando se cumplen todos los requisitos. 

### <a name="what-happens-if-i-have-multiple-policies-for-the-same-user-configured"></a>¿Qué ocurre si tiene varias directivas configuradas para el mismo usuario?  
En cada inicio de sesión, Azure Active Directory evalúa todas las directivas y garantiza que se cumplan todos los requisitos antes de conceder acceso al usuario.


### <a name="does-conditional-access-work-with-exchange-activesync"></a>¿Funciona el acceso condicional con Exchange ActiveSync?
 
Puede usar Exchange ActiveSync en una directiva de acceso condicional. Sin embargo, la compatibilidad con este escenario es limitada.  
Las siguientes limitaciones se aplican a una directiva que incluye Exchange ActiveSync:

- Como asignación de **aplicaciones de nube**, solo ha seleccionado **Exchange Online**.

- Si es necesario establecer un **control**, solo puede seleccionar **Requerir dispositivo compatible**. 
 
    ![Conceder](./media/active-directory-conditional-access-azure-portal/22.png)
 
- Si tiene que configurar una **condición**, solo puede configurar **aplicaciones cliente**.   

    ![Condiciones](./media/active-directory-conditional-access-azure-portal/21.png)


## <a name="common-scenarios"></a>Escenarios comunes

### <a name="requiring-multi-factor-authentication-for-apps"></a>Exigir autenticación multifactor para las aplicaciones

Muchos entornos tienen aplicaciones que requieren un mayor nivel de protección que otras.
Este es, por ejemplo, el caso de aplicaciones que tienen acceso a datos confidenciales. Si quiere agregar otra capa de protección a estas aplicaciones, puede configurar una directiva de acceso condicional que exija autenticación multifactor cuando los usuarios accedan a estas aplicaciones.


### <a name="requiring-multi-factor-authentication-for-access-from-networks-that-are-not-trusted"></a>Exigir autenticación multifactor para el acceso desde redes que no son de confianza

Este escenario es similar al escenario anterior porque agrega un requisito para la autenticación multifactor.
Sin embargo, la diferencia principal es la condición de este requisito.  
Mientras que el escenario anterior se centra en aplicaciones con acceso a datos confidenciales, este escenario se centra en ubicaciones de confianza.  
En otras palabras, podría tener un requisito de autenticación multifactor si un usuario accede a una aplicación desde una red que no es de confianza. 


### <a name="only-trusted-devices-can-access-office-365-services"></a>Solo los dispositivos de confianza pueden acceder a servicios de Office 365

Si usa Intune en su entorno, puede comenzar a usar inmediatamente la directiva de acceso condicional en la consola de Azure.

Muchos clientes de Intune usan el acceso condicional para asegurarse de que solo los dispositivos de confianza puedan acceder a servicios de Office 365. Esto significa que los dispositivos móviles se inscriben con Intune y satisfacen los requisitos de la directiva de cumplimiento y que los equipos con Windows están unidos a un dominio local. Una importante mejora es que no es necesario establecer la misma directiva para cada uno de los servicios de Office 365.  Cuando cree una nueva directiva, configure las aplicaciones de nube para que incluyan cada una de las aplicaciones de Office 365 que quiere proteger con acceso condicional. 

## <a name="next-steps"></a>Pasos siguientes

Si quiere saber cómo configurar una directiva de acceso condicional, consulte [Get started with conditional access in Azure Active Directory](active-directory-conditional-access-azure-portal-get-started.md) (Introducción al acceso condicional en Azure Active Directory).



<!--HONumber=Dec16_HO4-->


