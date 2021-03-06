---
title: "Tutorial: Integración de Azure Active Directory con Sciforma | Microsoft Docs"
description: "Aprenda cómo usar Sciforma con Azure Active Directory para habilitar el inicio de sesión único, el aprovisionamiento automatizado, etc."
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: abbfb5ac-7687-4153-b263-8090102dae37
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/26/2016
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 2a61552f3275aa58aca9c014d24ae69d91ce59d5


---
# <a name="tutorial-azure-ad-integration-with-sciforma"></a>Tutorial: Integración de Azure AD con Sciforma
El objetivo de este tutorial es mostrar la integración de Azure y Sciforma.  
En la situación descrita en este tutorial se supone que ya cuenta con los elementos siguientes:

* Una suscripción de Azure válida
* Un inquilino de Sciforma

Después de completar este tutorial, los usuarios de Azure AD que ha asignado a Sciforma podrán realizar un inicio de sesión único en la aplicación en el sitio de la compañía de Sciforma (inicio de sesión iniciado por el proveedor de servicios) o con la [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md).

La situación descrita en este tutorial consta de los siguientes bloques de creación:

1. Habilitación de la integración de aplicaciones en Sciforma
2. Configuración del inicio de sesión único
3. Configuración del aprovisionamiento de usuario
4. Asignación de usuarios

![Escenario](./media/active-directory-saas-sciforma-tutorial/IC777369.png "Scenario")

## <a name="enabling-the-application-integration-for-sciforma"></a>Habilitación de la integración de aplicaciones en Sciforma
El objetivo de esta sección es describir cómo se habilita la integración de aplicaciones en Sciforma.

### <a name="to-enable-the-application-integration-for-sciforma-perform-the-following-steps"></a>Siga estos pasos para habilitar la integración de aplicaciones en Sciforma:
1. En el panel de navegación izquierdo del Portal de Azure clásico, haga clic en **Active Directory**.
   
   ![Active Directory](./media/active-directory-saas-sciforma-tutorial/IC700993.png "Active Directory")
2. En la lista **Directory** , seleccione el directorio cuya integración desee habilitar.
3. Para abrir la vista de aplicaciones, haga clic en **Applications** , en el menú superior de la vista de directorios.
   
   ![Applications](./media/active-directory-saas-sciforma-tutorial/IC700994.png "Applications")
4. Haga clic en **Agregar** en la parte inferior de la página.
   
   ![Agregar aplicación](./media/active-directory-saas-sciforma-tutorial/IC749321.png "Add application")
5. En el cuadro de diálogo **¿Qué desea hacer?**, haga clic en **Agregar una aplicación de la galería**.
   
   ![Agregar una aplicación de la galería](./media/active-directory-saas-sciforma-tutorial/IC749322.png "Add an application from gallerry")
6. En el **cuadro de búsqueda**, escriba **Sciforma**.
   
   ![Galería de aplicaciones](./media/active-directory-saas-sciforma-tutorial/IC777370.png "Application gallery")
7. En el panel de resultados, seleccione **Sciforma** y haga clic en **Completar** para agregar la aplicación.
   
   ![Sciforma](./media/active-directory-saas-sciforma-tutorial/IC777371.png "Sciforma")
   
   ## <a name="configuring-single-sign-on"></a>Configuración del inicio de sesión único

El objetivo de esta sección es describir cómo habilitar usuarios para que se autentiquen en Sciforma con su cuenta de Azure AD a través de la federación basada en el protocolo SAML.

### <a name="to-configure-single-sign-on-perform-the-following-steps"></a>Siga estos pasos para configurar el inicio de sesión único:
1. En el Portal de Azure clásico, en la página de integración de aplicaciones de **Sciforma**, haga clic en **Configurar inicio de sesión único** para abrir el cuadro de diálogo **Configurar inicio de sesión único**.
   
   ![Configurar inicio de sesión único](./media/active-directory-saas-sciforma-tutorial/IC777372.png "Configure single sign-on")
2. En la página **¿Cómo desea que los usuarios inicien sesión en Sciforma?**, seleccione **Inicio de sesión único de Microsoft Azure AD** y haga clic en **Siguiente**.
   
   ![Configurar inicio de sesión único](./media/active-directory-saas-sciforma-tutorial/IC777373.png "Configure single sign-on")
3. En la página **Configurar dirección URL de la aplicación**, en el cuadro de texto **URL de inicio de sesión de Sciforma**, escriba su dirección URL con el siguiente patrón "*https://\<nombre-inquilino\>.Sciforma.com*" y haga clic en **Siguiente**.
   
   ![Configurar dirección URL de la aplicación](./media/active-directory-saas-sciforma-tutorial/IC777374.png "Configure app URL")
4. En la página **Configurar inicio de sesión único en Sciforma**, para descargar los metadatos, haga clic en **Descargar metadatos** y el archivo de datos localmente como **c:\\SciformaMetaData.xml**.
   
   ![Configurar inicio de sesión único](./media/active-directory-saas-sciforma-tutorial/IC777375.png "Configure single sign-on")
5. Reenvíe el archivo de metadatos al equipo de soporte técnico de Sciforma. El equipo de soporte técnico necesita configurar un inicio de sesión único para usted.
6. Seleccione la confirmación de la configuración de inicio de sesión único y haga clic en **Completar** para cerrar el cuadro de diálogo **Configurar inicio de sesión único**.
   
   ![Configurar inicio de sesión único](./media/active-directory-saas-sciforma-tutorial/IC777376.png "Configure single sign-on")
   
   ## <a name="configuring-user-provisioning"></a>Configuración del aprovisionamiento de usuario

No hay elemento de acción para que configure el aprovisionamiento de usuarios en Sciforma.  
Cuando un usuario asignado intenta iniciar sesión en Sciforma desde el panel de acceso, Sciforma comprueba si el usuario existe.  
Si no hay cuentas de usuario disponibles todavía, Sciforma crea una automáticamente.

## <a name="assigning-users"></a>Asignación de usuarios
Para probar la configuración, debe conceder acceso a los usuarios de Azure AD a los que quiere permitir el uso de su aplicación.

### <a name="to-assign-users-to-sciforma-perform-the-following-steps"></a>Para asignar usuarios a Sciforma, lleve a cabo los siguientes pasos:
1. En el Portal de Azure clásico, cree una cuenta de prueba.
2. En la página de integración de aplicaciones de **Sciforma**, haga clic en **Asignar usuarios**.
   
   ![Asignar usuarios](./media/active-directory-saas-sciforma-tutorial/IC777377.png "Assign users")
3. Seleccione su usuario de prueba, haga clic en **Asignar** y en **Sí** para confirmar la asignación.
   
   ![Sí](./media/active-directory-saas-sciforma-tutorial/IC767830.png "Yes")

Si desea probar la configuración de inicio de sesión único, abra el Panel de acceso. Para obtener más información sobre el Panel de acceso, vea [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md).




<!--HONumber=Nov16_HO3-->


