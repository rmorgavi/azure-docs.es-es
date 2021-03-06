---
title: "Tutorial: integración de Azure Active Directory con Intacct | Microsoft Docs"
description: "Aprenda a usar Intacct con Azure Active Directory para habilitar el inicio de sesión único, el aprovisionamiento automatizado, etc."
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: 92518e02-a62c-4b1b-a8e9-2803eb2b49ac
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/29/2016
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 5d0c072a57ca3afb89dffc88895f004d66b28cb8


---
# <a name="tutorial-azure-active-directory-integration-with-intacct"></a>Tutorial: integración de Azure Active Directory con Intacct
El objetivo de este tutorial es mostrar la integración de Azure e Intacct.  
En la situación descrita en este tutorial se supone que ya cuenta con los elementos siguientes:

* Una suscripción de Azure válida
* Un inquilino de Intacct

Después de completar este tutorial, los usuarios de Azure AD que ha asignado a Intacct podrán realizar un inicio de sesión único en la aplicación en el sitio de la compañía de Intacct (inicio de sesión iniciado por el proveedor de servicios) o con la [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md).

La situación descrita en este tutorial consta de los siguientes bloques de creación:

1. Habilitación de la integración de aplicaciones para Intacct
2. Configuración del inicio de sesión único
3. Configuración del aprovisionamiento de usuario
4. Asignación de usuarios

![Escenario](./media/active-directory-saas-intacct-tutorial/IC790030.png "Scenario")

## <a name="enabling-the-application-integration-for-intacct"></a>Habilitación de la integración de aplicaciones para Intacct
El objetivo de esta sección es describir cómo se habilita la integración de aplicaciones para Intacct.

### <a name="to-enable-the-application-integration-for-intacct-perform-the-following-steps"></a>Siga estos pasos para habilitar la integración de aplicaciones para Intacct:
1. En el panel de navegación izquierdo del Portal de Azure clásico, haga clic en **Active Directory**.
   
   ![Active Directory](./media/active-directory-saas-intacct-tutorial/IC700993.png "Active Directory")
2. En la lista **Directory** , seleccione el directorio cuya integración desee habilitar.
3. Para abrir la vista de aplicaciones, haga clic en **Applications** , en el menú superior de la vista de directorios.
   
   ![Applications](./media/active-directory-saas-intacct-tutorial/IC700994.png "Applications")
4. Haga clic en **Agregar** en la parte inferior de la página.
   
   ![Agregar aplicación](./media/active-directory-saas-intacct-tutorial/IC749321.png "Add application")
5. En el cuadro de diálogo **¿Qué desea hacer?**, haga clic en **Agregar una aplicación de la galería**.
   
   ![Agregar una aplicación de la galería](./media/active-directory-saas-intacct-tutorial/IC749322.png "Add an application from gallerry")
6. En el **cuadro de búsqueda**, escriba **Intacct**.
   
   ![Galería de aplicaciones](./media/active-directory-saas-intacct-tutorial/IC790031.png "Application Gallery")
7. En el panel de resultados, seleccione **Intacct** y luego haga clic en **Completar** para agregar la aplicación.
   
   ![Intacct](./media/active-directory-saas-intacct-tutorial/IC790032.png "Intacct")
   
   ## <a name="configuring-single-sign-on"></a>Configuración del inicio de sesión único

El objetivo de esta sección es describir cómo se habilita la autenticación de usuarios en Intacct con su cuenta de Azure AD mediante la federación basada en el protocolo SAML.  
Como parte de este procedimiento, se requiere crear un archivo de certificado codificado en base 64.  
Si no está familiarizado con este procedimiento, consulte [Conversión de un certificado binario en un archivo de texto](http://youtu.be/PlgrzUZ-Y1o).

### <a name="to-configure-single-sign-on-perform-the-following-steps"></a>Siga estos pasos para configurar el inicio de sesión único:
1. En el Portal de Azure clásico, en la página de integración de la aplicación **Intacct**, haga clic en **Configurar inicio de sesión único** para abrir el cuadro de diálogo **Configurar inicio de sesión único**.
   
   ![Configurar inicio de sesión único](./media/active-directory-saas-intacct-tutorial/IC790033.png "Configure Single Sign-On")
2. En la página **¿Cómo desea que los usuarios inicien sesión en Intacct?**, seleccione **Inicio de sesión único de Microsoft Azure AD** y luego haga clic en **Siguiente**.
   
   ![Configurar inicio de sesión único](./media/active-directory-saas-intacct-tutorial/IC790034.png "Configure Single Sign-On")
3. En la página **Configurar dirección URL de la aplicación**, en el cuadro de texto **URL de inicio de sesión de Intacc**, escriba su dirección URL con el siguiente patrón *https://Intacct.com/company* y luego haga clic en **Siguiente**.
   
   ![Configurar dirección URL de la aplicación](./media/active-directory-saas-intacct-tutorial/IC790035.png "Configure App URL")
4. En la página **Configuración de inicio de sesión único en Intacct**, haga clic en **Descargar certificado** y luego guarde el archivo de certificado en el equipo.
   
   ![Configurar inicio de sesión único](./media/active-directory-saas-intacct-tutorial/IC790036.png "Configure Single Sign-On")
5. En otra ventana del explorador web, inicie sesión como administrador en el sitio de la compañía de Intacct.
6. Haga clic en la pestaña **Compañía** y luego haga clic en**Información de la compañía**.
   
   ![Compañía](./media/active-directory-saas-intacct-tutorial/IC790037.png "Company")
7. Haga clic en la pestaña **Seguridad** y luego haga clic en **Editar**.
   
   ![Seguridad](./media/active-directory-saas-intacct-tutorial/IC790038.png "Security")
8. En la sección **Inicio de sesión único (SSO)** , siga estos pasos:
   
   ![Inicio de sesión único](./media/active-directory-saas-intacct-tutorial/IC790039.png "Single Sign On")
   
   1. Seleccione **Habilitar inicio de sesión único**.
   2. En **Tipo de proveedor de identidades**, seleccione **SAML 2.0**.
   3. En el Portal de Azure clásico, en la página de diálogo **Configurar inicio de sesión único en Intacct**, copie el valor de **URL del emisor** y péguelo en el cuadro de texto **URL del emisor**.
   4. En el Portal de Azure clásico, en la página de diálogo **Configurar inicio de sesión único en Intacct**, copie el valor de **Dirección URL de inicio de sesión remoto** y péguelo en el cuadro de texto **Dirección URL de inicio de sesión**.
   5. Cree un archivo **codificado en base 64** a partir del certificado descargado.
      
      > [!TIP]
      > Para obtener más información, consulte [Conversión de un certificado binario en un archivo de texto](http://youtu.be/PlgrzUZ-Y1o)
      > 
      > 
   6. Abra el certificado codificado en base 64 en el Bloc de notas, copie el contenido del mismo en el Portapapeles y luego péguelo en el cuadro de texto **Certificado** .
   7. Haga clic en **Guardar**.
9. En el Portal de Azure clásico, seleccione la confirmación de configuración de inicio de sesión único y haga clic en **Completar** para cerrar el cuadro de diálogo **Configurar inicio de sesión único**.
   
   ![Configurar inicio de sesión único](./media/active-directory-saas-intacct-tutorial/IC790040.png "Configure Single Sign-On")
   
   ## <a name="configuring-user-provisioning"></a>Configuración del aprovisionamiento de usuario

Para permitir que los usuarios de Azure AD inicien sesión en Intacct, tienen que aprovisionarse en Intacct.  
En el caso de Intacct, el aprovisionamiento es una tarea manual.

### <a name="to-provision-a-user-accounts-perform-the-following-steps"></a>Para aprovisionar cuentas de usuario, realice estos pasos:
1. Inicie sesión en su inquilino de **Intacct** .
2. Haga clic en la pestaña **Compañía** y luego haga clic en **Usuarios**.
   
   ![Usuarios](./media/active-directory-saas-intacct-tutorial/IC790041.png "Users")
3. Haga clic en la pestaña **Agregar** .
   
   ![Agregar](./media/active-directory-saas-intacct-tutorial/IC790042.png "Add")
4. En la sección **Información del usuario** , lleve a cabo estos pasos:
   
   ![Información del usuario](./media/active-directory-saas-intacct-tutorial/IC790043.png "User Information")
   
   1. Escriba el **Id. de usuario**, **Apellidos**, **Nombre**, **Dirección de correo electrónico**, **Puesto** y **Teléfono** de una cuenta de Azure AD que quiera aprovisionar en los cuadros de texto relacionados.
   2. Seleccione **Privilegios administrativos** de una cuenta de Azure AD que quiera aprovisionar.
   3. Haga clic en **Guardar**.
      
      > [!NOTE]
      > El titular de la cuenta de AAD recibirá un mensaje de correo y seguirá un vínculo para confirmar su cuenta antes de que se active.
      > 
      > 

> [!NOTE]
> Puede usar cualquier otra API o herramienta de creación de cuentas de usuario de Intacct ofrecida por Intacct para aprovisionar cuentas de usuario de AAD.
> 
> 

## <a name="assigning-users"></a>Asignación de usuarios
Para probar la configuración, tiene que conceder acceso, mediante su asignación, a los usuarios de Azure AD a los que quiere permitir el uso de su aplicación.

### <a name="to-assign-users-to-intacct-perform-the-following-steps"></a>Para asignar usuarios a Intacct, lleve a cabo los siguientes pasos:
1. En el Portal de Azure clásico, cree una cuenta de prueba.
2. En la página de integración de aplicaciones de **Intacct**, haga clic en **Asignar usuarios**.
   
   ![Asignar usuarios](./media/active-directory-saas-intacct-tutorial/IC790044.png "Assign Users")
3. Seleccione su usuario de prueba, haga clic en **Asignar** y en **Sí** para confirmar la asignación.
   
   ![Sí](./media/active-directory-saas-intacct-tutorial/IC767830.png "Yes")

Si desea probar la configuración de inicio de sesión único, abra el Panel de acceso. Para obtener más información sobre el Panel de acceso, vea [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md).




<!--HONumber=Nov16_HO3-->


