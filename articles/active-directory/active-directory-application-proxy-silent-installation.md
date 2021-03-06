---
title: "Instalación silenciosa del conector del proxy de la aplicación de Azure AD | Microsoft Docs"
description: "Explica cómo realizar una instalación silenciosa del conector de Proxy de aplicación de Azure AD para proporcionar acceso remoto seguro a las aplicaciones locales."
services: active-directory
documentationcenter: 
author: kgremban
manager: femila
editor: 
ms.assetid: 3aa1c7f2-fb2a-4693-abd5-95bb53700cbb
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/22/2016
ms.author: kgremban
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: 01c62e1e3074d7f9addd75ba51d6131921a4ecc7


---
# <a name="how-to-silently-install-the-azure-ad-application-proxy-connector"></a>Cómo instalar de forma silenciosa el conector de Proxy de aplicación de Azure AD
Quiere poder enviar un script de instalación a varios servidores de Windows o servidores de Windows Server que no tienen habilitada la interfaz de usuario. En este tema se explica cómo crear un script de Windows PowerShell que permite instalación desatendida para instalar y registrar el conector de Proxy de aplicación de Azure AD.

## <a name="enabling-access"></a>Habilitación del acceso
El proxy de la aplicación funciona mediante la instalación de un pequeño servicio de Windows Server denominado conector dentro de la red. Para que el conector de Proxy de aplicación funcione debe estar registrado con el directorio de Azure AD mediante un administrador global y una contraseña. Normalmente esto se especifica durante la instalación del conector en un cuadro de diálogo emergente. Si lo prefiere, puede usar Windows PowerShell para crear un objeto de credenciales para escribir la información de registro, o bien puede crear su propio token y usarlo con esta finalidad.

## <a name="step-1--install-the-connector-without-registration"></a>Paso 1: Instalar el conector sin registro
Instale los MSI del conector sin registrar el conector de la siguiente manera:

1. Abra el símbolo del sistema.
2. Ejecute el siguiente comando en el que el modificador /q significa instalación desatendida (la instalación no le pedirá que acepte el contrato de licencia de usuario final).
   
        AADApplicationProxyConnectorInstaller.exe REGISTERCONNECTOR="false" /q

## <a name="step-2-register-the-connector-with-azure-active-directory"></a>Paso 2: Seleccionar el conector con Azure Active Directory
Para ello, puede usar cualquiera de los métodos siguientes:

* Registro del conector mediante un objeto de credenciales de Windows PowerShell
* Registrar el conector utilizando un token creado sin conexión

### <a name="register-the-connector-using-a-windows-powershell-credential-object"></a>Registro del conector mediante un objeto de credenciales de Windows PowerShell
1. Cree el objeto de credenciales de Windows PowerShell, para lo que debe ejecutar lo siguiente, donde "<username>" y "<password>" deben reemplazarse por el nombre de usuario y la contraseña del directorio:
   
        $User = "<username>"
        $PlainPassword = '<password>'
        $SecurePassword = $PlainPassword | ConvertTo-SecureString -AsPlainText -Force
        $cred = New-Object –TypeName System.Management.Automation.PSCredential –ArgumentList $User, $SecurePassword
2. Vaya a **C:\Archivos de programa\Microsoft AAD App Proxy Connector** y ejecute el script con el objeto de credenciales de PowerShell que creó, donde $cred es el nombre de dicho objeto:
   
        RegisterConnector.ps1 -modulePath "C:\Program Files\Microsoft AAD App Proxy Connector\Modules\" -moduleName "AppProxyPSModule" -Authenticationmode Credentials -Usercredentials $cred

### <a name="register-the-connector-using-a-token-created-offline"></a>Registrar el conector utilizando un token creado sin conexión
1. Cree un token sin conexión mediante la clase AuthenticationContext con los valores del código, por ejemplo:

        using System;
        using System.Diagnostics;
        using Microsoft.IdentityModel.Clients.ActiveDirectory;

        class Program
        {
        #region constants
        /// <summary>
        /// The AAD authentication endpoint uri
        /// </summary>
        static readonly Uri AadAuthenticationEndpoint = new Uri("https://login.windows.net/common/oauth2/token?api-version=1.0");

        /// <summary>
        /// The application ID of the connector in AAD
        /// </summary>
        static readonly string ConnectorAppId = "55747057-9b5d-4bd4-b387-abf52a8bd489";

        /// <summary>
        /// The reply address of the connector application in AAD
        /// </summary>
        static readonly Uri ConnectorRedirectAddress = new Uri("urn:ietf:wg:oauth:2.0:oob");

        /// <summary>
        /// The AppIdUri of the registration service in AAD
        /// </summary>
        static readonly Uri RegistrationServiceAppIdUri = new Uri("https://proxy.cloudwebappproxy.net/registerapp");

        #endregion

        #region private members
        private string token;
        private string tenantID;
        #endregion

        public void GetAuthenticationToken()
        {
            AuthenticationContext authContext = new AuthenticationContext(AadAuthenticationEndpoint.AbsoluteUri);

            AuthenticationResult authResult = authContext.AcquireToken(RegistrationServiceAppIdUri.AbsoluteUri,
                ConnectorAppId,
                ConnectorRedirectAddress,
                PromptBehavior.Always);

            if (authResult == null || string.IsNullOrEmpty(authResult.AccessToken) || string.IsNullOrEmpty(authResult.TenantId))
            {
                Trace.TraceError("Authentication result, token or tenant id returned are null");
                throw new InvalidOperationException("Authentication result, token or tenant id returned are null");
            }

            token = authResult.AccessToken;
            tenantID = authResult.TenantId;
        }





1. Una vez que tenga el token cree SecureString con dicho token:  <br>
   `$SecureToken = $Token | ConvertTo-SecureString -AsPlainText -Force`
2. Ejecute el siguiente comando de Windows PowerShell, donde SecureToken es el nombre del token que acaba de crear y tenanID es el GUID de su inquilino:  <br>
   `RegisterConnector.ps1 -modulePath "C:\Program Files\Microsoft AAD App Proxy Connector\Modules\" -moduleName "AppProxyPSModule" -Authenticationmode Token -Token $SecureToken -TenantId <tenant GUID>`

## <a name="see-also"></a>Consulte también
* [Habilitación del proxy de aplicación de Azure Active Directory](active-directory-application-proxy-enable.md)
* [Publicar aplicaciones mediante su propio nombre de dominio](active-directory-application-proxy-custom-domains.md)
* [Habilitar el inicio de sesión único](active-directory-application-proxy-sso-using-kcd.md)
* [Solucionar los problemas que tiene con el Proxy de aplicación](active-directory-application-proxy-troubleshoot.md)

Para ver las últimas noticias y actualizaciones, consulte el [Application Proxy blog](http://blogs.technet.com/b/applicationproxyblog/)




<!--HONumber=Nov16_HO3-->


