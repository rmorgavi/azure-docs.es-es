---
title: "Configuración de clústeres de HDInsight unidos a un dominio| Microsoft Docs"
description: "Aprenda a configurar y definir clústeres de HDInsight unidos a un dominio."
services: hdinsight
documentationcenter: 
author: saurinsh
manager: jhubbard
editor: cgronlun
tags: 
ms.assetid: 0cbb49cc-0de1-4a1a-b658-99897caf827c
ms.service: hdinsight
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 11/02/2016
ms.author: saurinsh
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: c28ad5efa3cfcdc63559b50056ca6c79f8d600c9


---
# <a name="configure-domain-joined-hdinsight-clusters-preview"></a>Configuración de clústeres de HDInsight unidos a un dominio (versión preliminar)
Aprenda a configurar un clúster de Azure HDInsight con Azure Active Directory (Azure AD) y [Apache Ranger](http://hortonworks.com/apache/ranger/) para aprovechar la autenticación sólida y las directivas de control de acceso basado en rol (RBAC) enriquecidas.  HDInsight unido a un dominio solo se puede configurar en clústeres basados en Linux. Para más información, consulte [Introduce Domain-joined HDInsight clusters](hdinsight-domain-joined-introduction.md) (Introducción a los clústeres de HDInsight unidos a dominio (versión preliminar))

Este artículo es el primer tutorial de una serie:

* Crear un clúster de HDInsight conectado a Azure AD (a través de la funcionalidad de servicios de Azure Directory Domain) con Ranger Apache habilitado.
* Crear y aplicar directivas de Hive a través de Apache Ranger y permitir a los usuarios (por ejemplo, los científicos de datos) conectarse a Hive con herramientas basadas en ODBC, por ejemplo, Excel, Tableau, etc. Microsoft está trabajando para incluir otras cargas de trabajo, como HBase, Spark y Storm, a HDInsight unido a un dominio en breve.

Un ejemplo de la topología final tiene el aspecto siguiente:

![Topología de HDInsight unido a un dominio](.\\media\\hdinsight-domain-joined-configure\\hdinsight-domain-joined-topology.png)

Dado que Azure AD actualmente solo admite redes virtuales clásicas (VNet) y los clústeres de HDInsight basados en Linux solo admiten Azure Resource Manager basado en redes virtuales, la integración de Azure AD de HDInsight requiere dos redes virtuales y un emparejamiento entre ellas. Para obtener información comparativa entre dos modelos de implementación, consulte [La implementación de Azure Resource Manager frente a la implementación clásica: los modelos de implementación y el estado de los recursos](../resource-manager-deployment-model.md). Las dos redes virtuales deben estar en la misma región que Azure AD DS.

Los nombres de servicio de Azure deben ser únicos globalmente. Los siguientes nombres se utilizan en este tutorial. Contoso es un nombre ficticio. Debe reemplazar *contoso* por un nombre diferente cuando recorra el tutorial. 

**Nombres:**

| Propiedad | Valor |
| --- | --- |
| Red virtual de Azure AD |contosoaadvnet |
| Grupo de recursos de red virtual de Azure AD |contosoaadrg |
| Máquina virtual (VM) de Azure AD |contosoaadadmin. Esta VM se utiliza para configurar la unidad organizativa y la zona DNS inversa. |
| Directorio de Azure AD |contosoaaddirectory |
| Nombre de dominio de Azure AD |contoso (contoso.onmicrosoft.com) |
| Red virtual de HDInsight |contosohdivnet |
| Grupo de recursos de red virtual de HDInsight |contosohdirg |
| Clúster de HDInsight |contosohdicluster |

Este tutorial proporciona los pasos para configurar un clúster de HDInsight unido a un dominio. Cada sección tiene vínculos a otros artículos con información adicional.

## <a name="prerequisite"></a>Requisito previo:
* Familiarícese con [Servicios de dominio de Azure AD](https://azure.microsoft.com/services/active-directory-ds/) y su estructura de [precios](https://azure.microsoft.com/pricing/details/active-directory-ds/).
* Asegúrese de que la suscripción está en la lista blanca para esta versión preliminar pública. Puede hacerlo enviando un correo electrónico a hdipreview@microsoft.com con su identificador de suscripción.
* Un certificado SSL firmado por una autoridad de firma para el dominio. Se necesita el certificado por la configuración de LDAP seguro. Los certificados autofirmados no se pueden usar.

## <a name="procedures"></a>Procedimientos
1. Cree una red virtual clásica de Azure para su Azure AD.  
2. Cree y configure Azure AD y Azure AD DS.
3. Agregue una VM a la red virtual clásica para crear una unidad organizativa. 
4. Cree una unidad organizativa para Azure AD DS.
5. Cree una red virtual de HDInsight en el modo de administración de recursos de Azure.
6. Configure zonas DNS inversas para Azure AD DS.
7. Empareje las dos redes virtuales.
8. Cree un clúster de HDInsight.

> [!NOTE]
> En este tutorial se supone que no tiene una cuenta de Azure AD. Si tiene una, puede omitir la parte del paso 2.
> 
> 

Hay un script de PowerShell que automatiza los pasos 3 al 7.  Para más información, consulte [Configuración de clústeres de HDInsight unidos a un dominio con Azure PowerShell](hdinsight-domain-joined-configure-use-powershell.md).

## <a name="create-an-azure-classic-vnet"></a>Cree una red virtual clásica de Azure.
En esta sección, creará una red virtual clásica mediante Azure Portal. En la siguiente sección, se habilita Azure AD DS para Azure AD en la red virtual clásica. Para más información sobre el siguiente procedimiento y el uso de otros métodos de creación de la red virtual, consulte [Creación de una red virtual (clásica) usando Azure Portal](../virtual-network/virtual-networks-create-vnet-classic-portal.md).

**Para crear una red virtual clásica**

1. Inicie sesión en el [Portal de Azure](https://portal.azure.com). 
2. Haga clic en **Nuevo** > **Redes** > **Red virtual**.
3. En **Seleccionar un modelo de implementación**, seleccione **Clásico** y luego haga clic en **Crear**.
4. Escriba o seleccione los siguientes valores:
   
   * **Nombre**: contosoaadvnet
   * **Espacio de direcciones**: 10.1.0.0/16
   * **Nombre de subred**: Subnet1
   * **Intervalo de direcciones de subred**: 10.1.0.0/24
   * **Suscripción**: (Seleccione una suscripción usada para crear esta red virtual.)
   * **ResourceGroup**: contosoaadrg
   * **Ubicación**: (Seleccione una región para el clúster de HDInsight.)
     
     > [!IMPORTANT]
     > Debe elegir una ubicación compatible con Azure AD DS. Para más información, consulte [Productos disponibles por región](https://azure.microsoft.com/en-us/regions/services/). 
     > 
     > Tanto la red virtual clásica como la red virtual de grupo de recursos deben estar en la misma región que Azure AD DS.
     > 
     > 
5. Haga clic en **Crear** para crear la red virtual.

## <a name="create-and-configure-azure-ad-ds-for-your-azure-ad"></a>Creación y configuración de Azure AD DS para Azure AD
En esta sección:

1. Creará Azure AD.
2. Creará usuarios de Azure AD. Estos usuarios son usuarios de dominio. El primer usuario se usa para configurar el clúster de HDInsight con Azure AD.  Los otros dos usuarios son opcionales para este tutorial. Se usarán en [Configure Hive policies for Domain-joined HDInsight clusters](hdinsight-domain-joined-run-hive.md) (Configuración de directivas de Hive para clústeres de HDInsight unidos a un dominio) al configurar las directivas de Apache Ranger.
3. Creará el grupo de administradores de controlador de dominio de AAD y agregará el usuario de Azure AD al grupo. Este usuario se usa para crear la unidad organizativa.
4. Habilitará los Servicios de dominio de Azure AD (DS de Azure AD) para Azure AD.
5. Configurará LDAPS para Azure AD. El protocolo ligero de acceso a directorios (LDAP) se usa para realizar operaciones de lectura y escritura en Azure AD.

Si prefiere usar un Azure AD existente, puede omitir los pasos 1 y 2.

**Para crear Azure AD**

1. En el [portal de Azure clásico](https://manage.windowsazure.com), haga clic en **Nuevo** > **App Services** > **Active Directory** > **Directorio** > **Creación personalizada**. 
2. Escriba o seleccione los siguientes valores:
   
   * **Nombre**: contosoaaddirectory
   * **Nombre de dominio**: contoso.  Este nombre debe ser único globalmente.
   * **País o región**: Seleccione el país o región.
3. Haga clic en **Completo**.

**Para crear un usuario de Azure AD**

1. En el [portal de Azure clásico](https://manage.windowsazure.com), haga clic en **Active Directory** -> **contosoaaddirectory**. 
2. Haga clic en **Usuarios** en el menú superior.
3. Haga clic en **Agregar usuario**.
4. Escriba un nombre en **Nombre de usuario** y haga clic en **Siguiente**. 
5. Configure el perfil de usuario; en **Rol**, seleccione **Administrador global** y haga clic en **Siguiente**.  El rol Administrador global se necesita para crear unidades organizativas.
6. Haga clic en **Crear** para obtener una contraseña temporal.
7. Haga una copia de la contraseña y luego haga clic en **Completo**. Más adelante en este tutorial, utilizará este usuario administrador global para iniciar sesión en la VM de administración para crear una unidad organizativa configurar DNS inverso.

Siga el mismo procedimiento para crear dos usuarios más con el rol **Usuario**: hiveuser1 y hiveuser2. Los siguientes usuarios se usarán en [Configure Hive policies for Domain-joined HDInsight clusters](hdinsight-domain-joined-run-hive.md) (Configuración de directivas de Hive para clústeres de HDInsight unidos a un dominio).

**Para crear el grupo de administradores de controlador de dominio de AAD y agregar un usuario de Azure AD**

1. En el [portal de Azure clásico](https://manage.windowsazure.com), haga clic en **Active Directory** > **contosoaaddirectory**. 
2. Haga clic en **Grupos** en el menú superior.
3. Haga clic en **Agregar un grupo** o **Agregar grupo**.
4. Escriba o seleccione los siguientes valores:
   
   * **Nombre**: Administradores de controlador de dominio de AAD.  No cambie el nombre del grupo.
   * **Tipo de grupo**: Seguridad.
5. Haga clic en **Completo**.
6. Haga clic en **Administradores de controlador de dominio de AAD** para abrir el grupo.
7. Haga clic en **Agregar miembros**.
8. Seleccione el primer usuario que creó en el paso anterior y haga clic en **Completo**.
9. Repita los mismos pasos para crear otro grupo denominado **HiveUsers** y agregue los dos usuarios de Hive al grupo.

Para más información, consulte [Introducción a Servicios de dominio de Azure AD - Creación del grupo "Administradores de controladores de dominio de AAD"](../active-directory-domain-services/active-directory-ds-getting-started.md).

**Para habilitar Azure AD DS para Azure AD**

1. En el [portal de Azure clásico](https://manage.windowsazure.com), haga clic en **Active Directory** > **contosoaaddirectory**. 
2. Haga clic en **Configurar** en el menú superior.
3. Desplácese hacia abajo hasta **Servicios de dominio** y establezca los valores siguientes:
   
   * **Habilitar Servicios de dominio para este directorio**: Sí.
   * **Nombre de dominio DNS de Servicios de dominio**: Muestra el nombre de DNS predeterminado del directorio de Azure. Por ejemplo, contoso.onmicrosoft.com.
   * **Conectar Servicios de dominio a esta red virtual**: Seleccione la red virtual clásica que creó anteriormente, por ejemplo **contosoaadvnet**.
4. Haga clic en **Guardar** en la parte inferior de la página. Verá **Pendiente...** junto a **Habilitar Servicios de dominio para este directorio**.  
5. Espere hasta que **Pendiente...** desaparezca y **Dirección IP** se rellene. Se rellenarán dos direcciones IP. Estas son las direcciones IP de los controladores de dominio aprovisionados por Servicios de dominio. Cada dirección IP será visible después de que el controlador de dominio correspondiente se aprovisione y esté preparado. Anote las dos direcciones IP. Las necesitará más adelante.

Para más información, consulte [Servicios de dominio de Azure AD (versión preliminar) - Habilitación de Servicios de dominio de Azure AD](../active-directory-domain-services/active-directory-ds-getting-started-enableaadds.md).

**Para sincronizar la contraseña**

Si usa su propio dominio, necesita sincronizar la contraseña. Consulte [Servicios de dominio de Azure AD (versión preliminar): habilitación de la sincronización de contraseñas con Azure Active Directory Domain Services](../active-directory-domain-services/active-directory-ds-getting-started-password-sync.md).

**Para configurar LDAPS para Azure AD.**

1. Obtenga un certificado SSL firmado por una autoridad de firma para el dominio. Los certificados autofirmados no se pueden usar. Si no se puede obtener un certificado SSL, póngase en contacto con hdipreview@microsoft.com para una excepción.
2. En el [portal de Azure clásico](https://manage.windowsazure.com), haga clic en **Active Directory** > **contosoaaddirectory**. 
3. Haga clic en **Configurar** en el menú superior.
4. Desplácese a los **servicios de dominio**.
5. Haga clic en **Configurar certificado**.
6. Siga las instrucciones para especificar el archivo de certificado y la contraseña. Verá **Pendiente...** junto a **Habilitar Servicios de dominio para este directorio**.  
7. Espere hasta que **Pendiente...** desaparezca y **Certificado LDAP seguro** se rellene.  Esto puede tardar hasta 10 minutos o más.

> [!NOTE]
> Si algunas tareas en segundo plano se ejecutan en Azure AD DS, puede ver un error al cargar certificado - <i>Hay una operación realizándose para este inquilino. Inténtelo de nuevo más tarde</i>.  En caso de que se produzca este error, inténtelo de nuevo más tarde. La segunda dirección IP del controlador de dominio puede tardar hasta 3 horas en aprovisionarse.
> 
> 

Para más información, consulte [Configuración de LDAP seguro (LDAPS) para un dominio administrado con Servicios de dominio de Azure AD](../active-directory-domain-services/active-directory-ds-admin-guide-configure-secure-ldap.md).

## <a name="configure-an-organizational-unit-and-reverse-dns"></a>Configuración de una unidad organizativa y DNS inverso
En esta sección, agregará una máquina virtual a la red virtual de Azure AD e instalará herramientas administrativas en esta VM para que pueda configurar una unidad organizativa y DNS inverso. La búsqueda de DNS inverso es necesaria para la autenticación Kerberos.

**Para crear una máquina virtual en la red virtual**

1. En el [portal de Azure clásico](https://manage.windowsazure.com), haga clic en **Nuevo** > **Calcular** > **Máquina virtual** > **De la galería**.
2. Seleccione una imagen disco y luego haga clic en **Siguiente**.  Si no sabe cuál utilizar, seleccione la predeterminada, **Windows Server 2012 R2 Datacenter**.
3. Escriba o seleccione los siguientes valores:
   
   * Nombre de la máquina virtual: **contosoaadadmin**
   * Nivel: **Básico**
   * Nuevo nombre de usuario: (Escriba un nombre de usuario)
   * Contraseña: (Escriba una contraseña)
     
     Tenga en cuenta que el nombre de usuario y la contraseña es la administrador local.
4. Haga clic en **Siguiente**
5. En **Región o red virtual**, seleccione la nueva red virtual que creó en el último paso (contosoaadvnet) y haga clic en **Siguiente**.
6. Haga clic en **Completo**.

**Para RDP en la VM**

1. En el [Portal de Azure clásico](https://manage.windowsazure.com), haga clic en **Virtual Machines** > **contosoaadadmin**.
2. Haga clic en **Panel** en el menú superior.
3. Haga clic en **Conectar** en la parte inferior de la página.
4. Siga las instrucciones y utilice el nombre de usuario de administrador local y la contraseña para conectarse.

**Para unir una VM al dominio de Azure AD**

1. En la sesión RDP, haga clic en **Inicio** y luego en **Administrador del servidor**.
2. En el menú izquierdo, haga clic en **Servidor local**.
3. En el grupo de trabajo, haga clic en **Grupo de trabajo**.
4. Haga clic en **Cambiar**.
5. Haga clic en **Dominio**, escriba **contoso.onmicrosoft.com**, y luego haga clic en **Aceptar**.
6. Escriba las credenciales de usuario de dominio y luego haga clic en **Aceptar**.
7. Haga clic en **Aceptar**.
8. Haga clic en **Aceptar** para reiniciar el equipo.
9. Haga clic en **Cerrar**.
10. Haga clic en **Restart ahora**.

Para más información, consulte [Unión de una máquina virtual de Windows Server a un dominio administrado](../active-directory-domain-services/active-directory-ds-admin-guide-join-windows-vm.md).

**Para instalar herramientas de administración de Active Directory y herramientas de DNS**

1. RDP en **contosoaadadmin** usando la cuenta de usuario de Azure AD.
2. Haga clic en **Inicio** y luego en **Administrador del servidor**.
3. Haga clic en **Panel** en el menú de la izquierda.
4. Haga clic en **Administrar** y luego en **Agregar roles y características**.
5. Haga clic en **Next**.
6. Seleccione **Instalación basada en características o en roles** y haga clic en **Siguiente**.
7. Seleccione la máquina virtual actual del grupo de servidores y haga clic en **Siguiente**.
8. Haga clic en **siguiente** para omitir los roles.
9. Expanda **Herramientas de administración remota del servidor**, expanda **Herramientas de administración de roles**, seleccione **Herramientas de AD DS y AD LDS** y **Herramientas del servidor DNS** y, por último, haga clic en **Siguiente**. 
10. Haga clic en **Siguiente**
11. Haga clic en **Instalar**.

Para más información, consulte [Instalación de herramientas de administración de Active Directory en la máquina virtual](../active-directory-domain-services/active-directory-ds-admin-guide-administer-domain.md#task-2---install-active-directory-administration-tools-on-the-virtual-machine).

**Para configurar DNS inverso**

1. RDP en contosoaadadmin usando la cuenta de usuario de Azure AD.
2. Haga clic en **Inicio**, haga clic en **Herramientas administrativas** y luego en **DNS**. 
3. Haga clic en **No** para omitir la incorporación de ContosoAADAdmin.
4. Seleccione **El siguiente equipo**, escriba la dirección IP del primer servidor DNS que configuró anteriormente y luego haga clic en **Aceptar**.  Verá que el DC/DNS se agrega al panel izquierdo.
5. Expanda el servidor DC/DNS, haga clic con el botón derecho en **Zonas de búsqueda inversa** y luego haga clic en **Zona nueva**. Se abrirá el Asistente para nueva zona.
6. Haga clic en **Siguiente**.
7. Seleccione **Zona principal** y haga clic en **Siguiente**.
8. Seleccione **Para todos los servidores DNS que se ejecutan en controladores de este dominio** y haga clic en **Siguiente**.
9. Seleccione **Zona de búsqueda inversa de IPv4** y haga clic en **Siguiente**.
10. En **Id. de red**, escriba el prefijo para el intervalo de red de red virtual de HDInsight y haga clic en **Siguiente**. En la siguiente sección creará la red virtual de HDInsight.
11. Haga clic en **Siguiente**.
12. Haga clic en **Siguiente**.
13. Haga clic en **Finalizar**

La unidad organizativa que cree a continuación se utilizará cuando cree HDInsight. Las cuentas de equipo y los usuarios del sistema de Hadoop se colocarán en esta UO.

**Creación de una unidad organizativa (UO) en un dominio administrado de Servicios de dominio de Azure AD**

1. RDP en **contosoaadadmin** usando la cuenta de dominio que se encuentra en el grupo **Administradores de controlador de dominio de AAD**.
2. Haga clic en **Inicio**, haga clic en **Herramientas administrativas** y luego haga clic en **Centro de administración de Active Directory**.
3. Haga clic en el nombre de dominio en el panel izquierdo. Por ejemplo, contoso.
4. Haga clic en **Nuevo** bajo el nombre de dominio en el panel **Tarea** y luego haga clic en **Unidad organizativa**.
5. Escriba un nombre, por ejemplo **HDInsightOU** y luego haga clic en **Aceptar**. 

Para más información consulte [Creación de una unidad organizativa en un dominio administrado de Servicios de dominio de Azure AD](../active-directory-domain-services/active-directory-ds-admin-guide-create-ou.md).

## <a name="create-a-resource-manager-vnet-for-hdinsight-cluster"></a>Creación de una red virtual de Resource Manager para un clúster de HDInsight
En esta sección, creará una red virtual de Azure Resource Manager que se utilizará para el clúster de HDInsight. Para más información sobre la creación de una red virtual de Azure utilizando otros métodos, consulte [Creación de una red virtual](../virtual-network/virtual-networks-create-vnet-arm-pportal.md).

Después de crear la red virtual, podrá configurar la red virtual de Resource Manager para usar los mismos servidores DNS que para la red virtual de Azure AD. Si ha seguido los pasos de este tutorial para crear la red virtual clásica y Azure AD, los servidores DNS son 10.1.0.4 y 10.1.0.5.

**Para crear una red virtual de Resource Manager**

1. Inicie sesión en el [Portal de Azure](https://portal.azure.com).
2. Haga clic en **Nuevo**, **Redes** y, a continuación, en **Red virtual**. 
3. En **Seleccionar un modelo de implementación**, seleccione **Resource Manager** y haga clic en **Crear**.
4. Escriba o seleccione los valores siguientes:
   
   * **Nombre**: contosohdivnet
   * **Espacio de direcciones**: 10.2.0.0/16. Asegúrese de que el intervalo de direcciones no puede solaparse con el intervalo de direcciones IP de la red virtual clásica.
   * **Nombre de subred**: Subnet1
   * **Intervalo de direcciones de subred**: 10.2.0.0/24
   * **Suscripción**: (Seleccione su suscripción de Azure.)
   * **Grupo de recursos**: contosohdirg
   * **Ubicación**: (Seleccione la misma ubicación que la red de virtual de Azure AD, es decir, contosoaadvnet.)
5. Haga clic en **Crear**.

**Para configurar DNS para la red virtual de Resource Manager**

1. En [Azure Portal](https://portal.azure.com), haga clic en **Más servicios** -> **Redes virtuales**. Asegúrese de no hacer clic en **Redes virtuales (clásico)**.
2. Haga clic en **contosohdivnet**.
3. Haga clic en **Servidores DNS** en el lado izquierdo de la nueva hoja.
4. Haga clic en **Personalizado** y escriba los siguientes valores:
   
   * 10.1.0.4
   * 10.1.0.5
     
     Estas direcciones IP del servidor DNS deben coincidir con los servidores DNS de la red virtual de Azure AD (red virtual clásica).
5. Haga clic en **Guardar**.

## <a name="peer-the-azure-ad-vnet-and-the-hdinsight-vnet"></a>Emparejamiento de la red virtual de AD Azure y la red virtual de HDInsight
**Para emparejar las dos redes virtuales**

1. Inicie sesión en el [Portal de Azure](https://portal.azure.com).
2. Haga clic en **Más servicios** en el menú izquierdo.
3. Haga clic en **Redes virtuales**. No haga clic en **Redes virtuales (clásico)**.
4. Haga clic en **contosohdivnet**.  Se trata de la red virtual de HDInsight.
5. Haga clic en **Emparejamientos** en el menú de la izquierda de la hoja.
6. Haga clic en **Agregar** en el menú superior. Se abrirá la hoja **Agregar emparejamiento**.
7. En la hoja **Agregar emparejamiento**, establezca o seleccione los valores siguientes:
   
   * **Nombre**: ContosoAADHDIVNetPeering
   * **Modelo de implementación de red virtual**: Clásico
   * **Suscripción**: Seleccione el nombre de suscripción que se utiliza para la red virtual clásica (Azure AD).
   * **Red virtual**: contosoaadvnet.
   * **Permitir acceso a red virtual**: (Comprobar)
   * **Permitir tráfico reenviado**: (Comprobar). Deje las otras dos casillas desactivadas.
8. Haga clic en **OK**.

## <a name="create-hdinsight-cluster"></a>Creación de un clúster de HDInsight
En esta sección, creará un clúster de Hadoop basado en Linux en HDInsight con Azure Portal o la [plantilla de Azure Resource Manager](../resource-group-template-deploy.md). Para conocer otros métodos de creación de clústeres y la descripción de la configuración, consulte [Creación de clústeres de Hadoop basados en Windows en HDInsight](hdinsight-hadoop-provision-linux-clusters.md). Para más información sobre cómo utilizar la plantilla de Resource Manager para crear clústeres de Hadoop en HDInsight, consulte [Creación de clústeres de Hadoop basados en Windows en HDInsight mediante plantillas de Azure Resource Manager](hdinsight-hadoop-create-windows-clusters-arm-templates.md)

**Para crear un clúster de HDInsight unido a un dominio mediante Azure Portal**

1. Inicie sesión en el [Portal de Azure](https://portal.azure.com).
2. Haga clic en **Nuevo**, **Inteligencia y análisis** y luego en **HDInsight**.
3. En la hoja **Nuevo clúster de HDInsight**, escriba o seleccione los valores siguientes:
   
   * **Nombre de clúster**: Escriba un nuevo nombre de clúster para el clúster de HDInsight unido a un dominio.
   * **Suscripción**: Seleccione una suscripción de Azure usada para crear este clúster.
   * **Configuración de clúster**:
     
     * **Tipo de clúster**: Hadoop. HDInsight unido a un dominio actualmente solo es compatible con clústeres de Hadoop.
     * **Sistema operativo**: Linux.  HDInsight unido a un dominio solo se admite en clústeres de HDInsight basado en Linux.
     * **Versión**: Hadoop 2.7.3 (HDI 3.5). HDInsight unido a un dominio solo se admite en clústeres de HDInsight versión 3.5.
     * **Tipo de clúster**: PREMIUM
       
       Haga clic en **Seleccionar** para guardar los cambios.
   * **Credenciales**: Configure las credenciales para el usuario del clúster y el usuario SSH.
   * **Origen de datos**: Cree una nueva cuenta de Storage o use una cuenta existente cuenta de Storage como la cuenta de Storage para el clúster de HDInsight. La ubicación debe ser la misma que la de las dos redes virtuales.  La ubicación es también la ubicación del clúster de HDInsight.
   * **Precios**: Seleccione el número de nodos de trabajo del clúster.
   * **Configuraciones avanzadas**: 
     
     * **Unión a un dominio y red virtual o subred**: 
       
       * **Configuración de dominio**: 
         
         * **Nombre de dominio**: contoso.onmicrosoft.com
         * **Nombre de usuario de dominio**: Escriba un nombre de usuario de dominio. Este dominio debe tener los siguientes privilegios: unir máquinas al dominio y colocarlas en la unidad organizativa que configuró anteriormente; crear entidades de seguridad de servicio dentro de la unidad de organización que configuró anteriormente; crear entradas de DNS inverso. Este usuario de dominio se convertirá en el administrador de este clúster de HDInsight unido a un dominio.
         * **Contraseña de dominio**: Escriba la contraseña de usuario de dominio.
         * **Unidad organizativa**: escriba el nombre distintivo de la unidad organizativa que configuró anteriormente. Por ejemplo: OU=HDInsightOU,DC=contoso,DC=onmicrosoft,DC=com
         * **Dirección URL de LDAPS**: ldaps://contoso.onmicrosoft.com:636
         * **Grupo de usuarios de acceso**: Especifique el grupo de seguridad cuyos usuarios desea que realicen la sincronización con el clúster. Por ejemplo, HiveUsers.
           
           Haga clic en **Seleccionar** para guardar los cambios.
           
           ![Configuración del dominio del portal de HDInsight unido a un dominio](./media/hdinsight-domain-joined-configure/hdinsight-domain-joined-portal-domain-setting.png)
       * **Red virtual**: contosohdivnet
       * **Subred**: Subnet1
         
         Haga clic en **Seleccionar** para guardar los cambios.        
         Haga clic en **Seleccionar** para guardar los cambios.
   * **Grupo de recursos**: Seleccione el grupo de recursos que se usa para la red virtual de HDInsight (contosohdirg).
4. Haga clic en **Crear**.  

Otra opción para crear el clúster de HDInsight unido a un dominio es utilizar la plantilla de Azure Resource Management. El siguiente procedimiento muestra cómo:

**Para crear un clúster de HDInsight unido a un dominio mediante una plantilla de Resource Manager**

1. Haga clic en la imagen siguiente para abrir una plantilla de Resource Manager en Azure Portal. La plantilla de Resource Manager se encuentra en un contenedor de blobs público. 
   
    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fhditutorialdata.blob.core.windows.net%2Farmtemplates%2Fcreate-domain-joined-hdinsight-cluster.json" target="_blank"><img src="https://acom.azurecomcdn.net/80C57D/cdn/mediahandler/docarticles/dpsmedia-prod/azure.microsoft.com/en-us/documentation/articles/hdinsight-hbase-tutorial-get-started-linux/20160201111850/deploy-to-azure.png" alt="Deploy to Azure"></a>
2. En la hoja **Parámetros**, escriba los siguientes valores:
   
   * **Suscripción**: (Seleccione su suscripción de Azure).
   * **Grupo de recursos**: Haga clic en **Usar existente** y especifique el mismo grupo de recursos que ha estado usando.  Por ejemplo, contosohdirg. 
   * **Ubicación**: Especifique una ubicación del grupo de recursos.
   * **Nombre de clúster**: Escriba un nombre para el clúster de Hadoop que va a crear. Por ejemplo, contosohdicluster.
   * **Tipo de clúster**: seleccione un tipo de clúster.  El valor predeterminado es **hadoop**.
   * **Ubicación**: Seleccione una ubicación para el clúster.  La cuenta de almacenamiento predeterminada usa la misma ubicación.
   * **Recuento de nodos de trabajo de clúster**: Seleccione el número de nodos de trabajo.
   * **Nombre de inicio de sesión y contraseña de clúster**: el nombre de inicio de sesión predeterminado es **admin**.
   * **Nombre de usuario y contraseña de SSH**: el nombre de usuario predeterminado es **sshuser**.  Puede cambiarlo. 
   * **Id. de red virtual**: /subscriptions/&lt;Id.Suscripción>/resourceGroups/&lt;NombreGrupoRecursos>/providers/Microsoft.Network/virtualNetworks/&lt;NombreRedVirtual>
   * **Subred de la red virtual**: /subscriptions/&lt;Id.Suscripción>/resourceGroups/&lt;NombreGrupoRecursos>/providers/Microsoft.Network/virtualNetworks/&lt;NombreRedVirtual>/subnets/Subnet1
   * **Nombre de dominio**: contoso.onmicrosoft.com
   * **DN de unidad organizativa**: OU=HDInsightOU,DC=contoso,DC=onmicrosoft,DC=com
   * **DN de grupo de usuarios de clúster**: "\"CN=HiveUsers,OU=AADDC Users,DC=<DomainName>,DC=onmicrosoft,DC=com\""
   * **LDAPUrls**: ["ldaps://contoso.onmicrosoft.com:636"]
   * **DomainAdminUserName**: (Escriba el nombre de usuario de administrador de dominio)
   * **DomainAdminPassword**: (Escriba la contraseña de usuario de administrador de dominio)
   * **Acepto los términos y condiciones indicados anteriormente** (Comprobar)
   * **Anclar al panel**: (Comprobar)
3. Haga clic en **Comprar**. Verá un icono nuevo llamado **Implementación para la implementación de plantilla**. Tarda aproximadamente 20 minutos en crear un clúster. Una vez creado el clúster, puede hacer clic en la hoja del clúster en el portal para abrirlo.

Después de completar el tutorial, quizá desee eliminar el clúster. Con HDInsight, los datos se almacenan en Almacenamiento de Azure, por lo que puede eliminar un clúster de forma segura cuando no está en uso. También se le cargará por un clúster de HDInsight aunque no esté en uso. Como en muchas ocasiones los cargos por el clúster son más que los cargos por el almacenamiento, desde el punto de vista económico tiene sentido eliminar clústeres cuando no estén en uso. Para ver instrucciones sobre cómo eliminar un clúster, consulte [Administración de clústeres de Hadoop en HDInsight mediante Azure Portal](hdinsight-administer-use-management-portal.md#delete-clusters).

## <a name="next-steps"></a>Pasos siguientes
* Para configurar un clúster de HDInsight unido a un dominio, consulte [Configuración de clústeres de HDInsight unidos a un dominio con Azure PowerShell](hdinsight-domain-joined-configure-use-powershell.md).
* Para configurar directivas de Hive y ejecución de consultas de Hive, consulte [Configure Hive policies for Domain-joined HDInsight clusters](hdinsight-domain-joined-run-hive.md) (Configuración de directivas de los clústeres de HDInsight unidos a dominio).
* Para ejecutar consultas de Hive mediante SSH en clústeres de HDInsight unidos a dominio, consulte [Utilización de SSH con Hadoop en HDInsight basado en Linux desde Linux, Unix u OS X](hdinsight-hadoop-linux-use-ssh-unix.md#connect-to-a-domain-joined-hdinsight-cluster).




<!--HONumber=Nov16_HO3-->


