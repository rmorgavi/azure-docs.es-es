---
title: "Solución de problemas de la configuración de Enterprise State Roaming en Azure Active Directory | Microsoft Docs"
description: "Responde a algunas preguntas que los administradores de TI podrían tener sobre la sincronización de datos de aplicación y la configuración."
services: active-directory
keywords: "configuración de enterprise state roaming, nube de windows, preguntas más frecuentes sobre enterprise state roaming"
documentationcenter: 
author: femila
manager: swadhwa
editor: 
ms.assetid: f45d0515-99f7-42ad-94d8-307bc0d07be5
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/30/2016
ms.author: femila
translationtype: Human Translation
ms.sourcegitcommit: c5448ef5ad9def86b2deb1736160aa5b7ee6fd15
ms.openlocfilehash: 4a2f50caa06d383bd41a6e94a2f73f130d7a13e3


---
#<a name="troubleshooting-enterprise-state-roaming-settings-in-azure-active-directory"></a>Solución de problemas de la configuración de Enterprise State Roaming en Azure Active Directory

En este tema se proporciona información sobre cómo diagnosticar y solucionar problemas Enterprise State Roaming, y proporciona una lista de problemas conocidos.

## <a name="preliminary-steps-for-troubleshooting"></a>Pasos preliminares para solucionar problemas 
Antes de comenzar la solución de problemas, compruebe que el usuario y el dispositivo se han configurado correctamente y que se cumplen todos los requisitos de Enterprise State Roaming por el dispositivo y el usuario. 

1. Windows 10, con las actualizaciones más recientes, y la versión 1511 como mínimo (compilación del sistema operativo 10586 o posterior) está instalado en el dispositivo. 
2. El dispositivo está unido a Azure AD o unido a un dominio y registrado con Azure AD.
3. En el portal de Azure Active Directory, **Enterprise State Roaming** está habilitado para el directorio en **Configurar** > **Dispositivos** > **Los usuarios pueden sincronizar la configuración y los datos de aplicaciones empresariales**. O todos los usuarios seleccionados o el usuario están habilitados para la sincronización a través de la opción seleccionada y se incluyen en el grupo de seguridad.
4. El usuario tiene una suscripción de Azure Active Directory Premium asignada.  
5. Se ha reiniciado el dispositivo y el usuario ha iniciado sesión después de habilitado Enterprise State Roaming.

## <a name="information-to-include-when-you-need-help"></a>Información para incluir si necesita ayuda
Si no puede resolver el problema con las instrucciones siguientes, póngase en contacto con nuestros ingenieros de soporte técnico. Cuando se comunique con ellos, se recomienda que incluya la información siguiente:

- **Descripción general del error**: ¿el usuario ve mensajes de error? Si no hay ningún mensaje de error, describe detalladamente el comportamiento inesperado observado. ¿Qué características están habilitadas para la sincronización y qué espera sincronizar el usuario? ¿Son varias características las que no se sincronizan o está aislado a una?
-**Usuarios afectados**: El funcionamiento o error de la sincronización afecta a uno o a varios usuarios? ¿Cuántos dispositivos están implicados por usuario? ¿No se está sincronizando ninguno de ellos o algunos se están sincronizando y otros no?
- **Información sobre el usuario**: ¿qué identidad está usando el usuario para iniciar sesión en el dispositivo? ¿Cómo inicia sesión el usuario en el dispositivo? ¿Forman parte de un grupo de seguridad seleccionado al que se permite sincronizarse? 
- **Información sobre el dispositivo**: ¿este dispositivo está unido a Azure AD o unidos a un dominio? ¿Cuál es la compilación del dispositivo? ¿Cuáles son las actualizaciones más recientes?
- **Fecha/hora/zona horaria**: ¿en qué fecha y a qué hora exactamente se ha generado el error (incluir la zona horaria)?
- Incluir esta información nos ayudará a solucionar el problema lo antes posible.

## <a name="troubleshooting-and-diagnosing-issues"></a>Solución de problemas y diagnóstico de problemas
En esta sección se ofrecen sugerencias sobre cómo solucionar y diagnosticar problemas relacionados con Enterprise State Roaming.

## <a name="verify-sync-and-the-sync-your-settings-settings-page"></a>Comprobación de la sincronización y la página de configuración de "Sincronizar la configuración" 

1. Después de unir los equipos Windows 10 a un dominio que está configurado para permitir Enterprise State Roaming, inicie sesión con su cuenta profesional. Vaya a **Configuración** > **Cuentas** > **Sincronizar mi configuración** y confirme que la sincronización y la configuración individual están activadas y que la parte superior de la página de configuración indica que está realizando la sincronización con su cuenta profesional. Confirme que la misma cuenta también se usa como la cuenta de inicio de sesión en **Settings** > **Accounts** > **Your Info** (Configuración > Cuentas > Su información). 
2. Compruebe que la sincronización funciona en varias máquinas realizando algunos cambios en la máquina original, como mover la barra de tareas al lado derecho o en la parte superior de la pantalla. Observe que el cambio se propaga a la segunda máquina en 5 minutos. 
 - El bloqueo y desbloqueo de la pantalla (Win + L) puede ayudar a desencadenar una sincronización.
 - Debe usarse la misma cuenta de inicio de sesión en ambos equipos para que la sincronización funcione, como estado empresarial (Enterprise State).

## <a name="roaming-is-tied-to-the-user-account-and-not-the-machine-account"></a>La itinerancia está asociada a la cuenta de usuario y no la cuenta de la máquina.

**Problema potencial**: la página de configuración tiene los alternadores atenuados y, en lugar de ver una cuenta, verá un texto que indica que algunas características de Windows solo están disponibles si se usa una cuenta de Microsoft o una cuenta profesional. Este problema puede surgir en aquellos dispositivos que se han configurado para estar unidos a un dominio y registrado en Azure AD, pero el dispositivo no ha autenticado correctamente en Azure AD. Una posible causa es que se debe aplicar la directiva de dispositivo, pero esta aplicación se realiza de forma asincrónica y puede retrasarse unas pocas horas. Para comprobar este problema, siga que los pasos para comprobar el estado de registro de dispositivo para ver si este es el caso.

### <a name="verify-the-device-registration-status"></a>Comprobación del estado de registro de dispositivo
Enterprise State Roaming requiere que el dispositivo esté registrado con Azure AD. Aunque no son específicas para Enterprise State Roaming, las instrucciones siguientes pueden ayudarle a confirmar que el cliente de Windows 10 está registrado, así como confirmar la huella digital, la dirección URL de la configuración de Azure AD, el estado de NGC o cualquier otra información.

1.  Abra el símbolo del sistema sin privilegios elevados. Para ello, en Windows, abra el selector Ejecutar (Win + R) y escriba "cmd" para abrirlo.
2.  Una vez abierto el símbolo del sistema, escriba "*dsregcmd.exe /status*".
3.  Para la salida esperada, el valor del campo AzureAdJoined debe ser "YES", el valor del campo de WamDefaultSet debe ser "YES" y el valor del campo **WamDefaultGUID** debe ser un GUID con "(AzureAd)" al final.

**Problema potencial**: **WamDefaultSet** y **AzureAdJoined** tienen "NO" en el valor del campo, el dispositivo está unido a un dominio y registrado con Azure AD y no se sincroniza. Si se muestra esto, el dispositivo puede que tenga que esperar la aplicación de la directiva o se producirá un error de autenticación cuando el dispositivo se conecte a Azure AD. El usuario puede tener que esperar unas pocas horas para la directiva se aplique. Otros pasos de solución de problemas pueden incluir volver a intentar el registro automático mediante el cierre y el inicio de sesión, o el mediante el inicio de la tarea en el Programador de tareas. En algunos casos, puede ayudar a resolver este problema ejecutar "*dsregcmd.exe /leave*" en una ventana del símbolo del sistema con privilegios elevados, reiniciar y volver a intentar el registro.


**Problema potencial**: el campo de **AzureAdSettingsUrl** está vacío y el dispositivo no se sincroniza. El usuario puede haber iniciado sesión en el dispositivo antes de habilitar Enterprise State Roaming en el portal de Azure Active Directory. En el portal, pruebe a deshabilitar el administrador de TI y vuelva a habilitar la opción de que los usuarios puedan sincronizar la configuración y los datos de aplicaciones empresariales. Cuando se haya vuelto a habilitar, reinicie el dispositivo pida al usuario que inicie sesión. 

## <a name="enterprise-state-roaming-and-multi-factor-authentication"></a>Enterprise State Roaming y Multi-Factor Authentication 
En algunas circunstancias, Enterprise State Roaming no puede sincronizar los datos si se ha configurado Azure Multi-Factor Authentication. Para más detalles sobre estos síntomas, consulte el documento de soporte técnico KB3193683. 

**Problema potencial**: Si el dispositivo está configurado para requerir Multi-Factor Authentication en el portal de Azure Active Directory, se puede producir un error al sincronizar la configuración cuando se inicia sesión en un dispositivo Windows 10 con una contraseña. Este tipo de configuración de Multi-Factor Authentication se ha diseñado para proteger cuentas de administrador de Azure. Los usuarios administradores pueden seguir realizando la sincronización al iniciar sesión en sus dispositivos Windows 10 con su PIN de Microsoft Passport for Work o también pueden completar Multi-Factor Authentication al acceder a otros servicios de Azure, como Office 365.

**Problema potencial**: La sincronización puede producir un error si el administrador configura la directiva de acceso condicional de Multi-Factor Authentication de Active Directory Federation Services y caduca el token de acceso en el dispositivo. Asegúrese de iniciar sesión y de cerrarla con el PIN de Microsoft Passport for Work, o bien de completar Multi-Factor Authentication al acceder a otros servicios de Azure, como Office 365.

###<a name="event-viewer"></a>Visor de eventos
Para la solución avanzada de problemas, se puede usar el Visor de eventos para buscar errores específicos. Estos aparecen documentados en la tabla siguiente. Los eventos se pueden encontrar en Visor de eventos > Registros de aplicaciones y servicios > **Microsoft** > **Windows** > **SettingSync** y para problemas relacionados con la identidad con la sincronización **Microsoft** > **Windows** > **Azure AD**.


## <a name="known-issues"></a>Problemas conocidos

### <a name="sync-does-not-work-on-devices-that-have-apps-side-loaded-using-mdm-software"></a>La sincronización no funciona en los dispositivos que tienen aplicaciones que se hayan cargado con software MDM

Afecta a los dispositivos que ejecutan la actualización de aniversario de Windows 10 (versión 1607). En el Visor de eventos en los registros de SettingSync-Azure, se suele ver el evento Id. 6013 con el error 80070259.

**Acción recomendada**  
Asegúrese de que el cliente de Windows 10 v1607 tiene la actualización acumulativa del 23 de agosto de 2016 ([KB3176934](https://support.microsoft.com/kb/3176934) compilación del sistema operativo 14393.82). 

---

### <a name="internet-explorer-favorites-do-not-sync"></a>Los favoritos de Internet Explorer no se sincronizan

Afecta a los dispositivos que ejecutan la actualización de noviembre de Windows 10 (versión 1511).

**Acción recomendada**  
Asegúrese de que el cliente de Windows 10 v1511 tiene la actualización acumulativa de julio de 2016 ([KB3172985](https://support.microsoft.com/kb/3172985) compilación del sistema operativo 10586.494).

---

### <a name="theme-is-not-syncing-as-well-as-data-protected-with-windows-information-protection"></a>El tema no se está sincronizando, así como los datos protegidos Windows Information Protection 

Para evitar la pérdida de datos, los datos que están protegidos con [Windows Information Protection](https://technet.microsoft.com/itpro/windows/keep-secure/protect-enterprise-data-using-wip) no se sincronizarán a través de Enterprise State Roaming para dispositivos con la actualización de aniversario de Windows 10.

**Acción recomendada**  
Ninguno. Las futuras actualizaciones de Windows pueden resolver este problema.

---

### <a name="date-time-and-region-settings-do-not-sync-on-domain-joined-device"></a>La configuración de fecha, hora y región no se sincroniza en dispositivos unidos a un dominio 
  
Los dispositivos que están unidos a un dominio no sincronizarán la configuración de fecha, hora y región: hora automática. El uso de la hora automática puede invalidar otras configuraciones de fecha, hora y región, y hacer que esos valores se sincronicen. 

**Acción recomendada**  
Ninguno. 

---

### <a name="uac-prompts-when-syncing-passwords"></a>UAC avisa al sincronizar las contraseñas

Afecta a los dispositivos que ejecutan la actualización de noviembre de Windows 10 (versión 1511) con una NIC inalámbrica configurada para sincronizar contraseñas.

**Acción recomendada**  
Asegúrese de que el cliente de Windows 10 v1511 tiene la actualización acumulativa ([KB3140743](https://support.microsoft.com/kb/3140743) Compilación del sistema operativo 10586.494).

---

### <a name="sync-does-not-work-on-devices-that-use-smart-card-for-login"></a>La sincronización no funciona en dispositivos que usan tarjetas inteligentes para el inicio de sesión
Si intenta iniciar sesión en el dispositivo Windows con una tarjeta inteligente o tarjeta inteligente virtual, la sincronización dejará de funcionar.     

**Acción recomendada**  
Ninguno. Las futuras actualizaciones de Windows pueden resolver este problema.

---

### <a name="domain-joined-device-is-not-syncing-after-leaving-corporate-network"></a>El dispositivo unidos a un dominio no se sincroniza después de salir de la red corporativa     
Los dispositivos unidos a un dominio registrados en Azure AD pueden experimentar errores de sincronización si el dispositivo está fuera del sitio durante largos períodos de tiempo y no se puede completar la autenticación de dominio.

**Acción recomendada**  
Conecte el dispositivo a una red corporativa, para que pueda reanudarse la sincronización.

---

### <a name="event-id-6065-80070533-this-user-cant-sign-in-because-this-account-is-currently-disabled"></a>Id. de evento 6065: 80070533 Este usuario no puede iniciar sesión porque esta cuenta está deshabilitada  
En el Visor de eventos, en los registros de SettingSync/Debug, este error puede verse cuando el inquilino no ha aprovisionando automáticamente AzureRMS. 

**Acción recomendada**  
Continúe con los pasos enumerados en [KB3193791](https://support.microsoft.com/kb/3193791). 

---

### <a name="event-id-1098-error-0xcaa5001c-token-broker-operation-failed"></a>Id. de evento 1098: Error: error en la operación del agente de token de 0xCAA5001C  
En el Visor de eventos, en los registros AAD/Operational, este error puede aparecer como evento 1104: llamada de complemento AP de nube AAD Obtener error devuelto por token: 0xC000005F. Este problema se produce si faltan permisos o atributos de propiedad.  

**Acción recomendada**  
Continúe con los pasos enumerados en el artículo [KB3196528](https://support.microsoft.com/kb/3196528).  



##<a name="next-steps"></a>Pasos siguientes

- Emplee el foro [UserVoice](https://feedback.azure.com/forums/169401-azure-active-directory/category/158658-enterprise-state-roaming) para proporcionar comentarios y realizar sugerencias sobre cómo mejorar Enterprise State Roaming.

- Para más información, consulte [Información general de Enterprise State Roaming](active-directory-windows-enterprise-state-roaming-overview.md). 


<!--HONumber=Dec16_HO1-->


