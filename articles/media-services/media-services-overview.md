---
title: "Información general y escenarios comunes de Azure Media Services | Microsoft Docs"
description: "En este tema se proporciona información general de Servicios multimedia de Azure."
services: media-services
documentationcenter: 
author: Juliako
manager: erikre
editor: 
ms.assetid: 7a5e9723-c379-446b-b4d6-d0e41bd7d31f
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 01/05/2017
ms.author: juliako;anilmur
translationtype: Human Translation
ms.sourcegitcommit: f6d6b7b1051a22bbc865b237905f8df84e832231
ms.openlocfilehash: 946f6e480083a0007a88c85b744ddeafa0385990


---
# <a name="azure-media-services-overview-and-common-scenarios"></a>Información general y escenarios comunes de Azure Media Services

Servicios multimedia de Microsoft Azure es una plataforma extensible basada en la nube que permite a los desarrolladores crear aplicaciones de administración y entrega de contenido multimedia escalables. Servicios multimedia se basa en las API de REST, que permiten cargar, almacenar, codificar y empaquetar de forma segura contenido de audio o vídeo para la entrega bajo demanda y de streaming en vivo a varios clientes (por ejemplo, televisión, PC y dispositivos móviles).

Puede crear flujos de trabajo de un extremo a otro usando solamente Servicios multimedia. También puede usar componentes de terceros para algunas partes del flujo de trabajo. Por ejemplo, codifique mediante un codificador de terceros. A continuación, cargue, proteja, empaquete y entregue con Servicios multimedia.

Puede elegir entre transmisión del contenido en directo o entrega a petición. En este tema se muestran escenarios comunes para la entrega de contenido [en directo](media-services-overview.md#live_scenarios) o [a petición](media-services-overview.md#vod_scenarios). El tema también incluye vínculos a otros temas de interés.

## <a name="sdks-and-tools"></a>SDK y herramientas

Para compilar soluciones de Servicios multimedia, puede usar:

* [API de REST de Servicios multimedia](https://msdn.microsoft.com/library/azure/hh973617.aspx)
* Uno de los SDK de cliente disponibles:
    * [SDK de Servicios multimedia de Azure para .NET](https://github.com/Azure/azure-sdk-for-media-services)
    * [SDK de Azure para Java](https://github.com/Azure/azure-sdk-for-java)
    * [SDK de Azure para PHP](https://github.com/Azure/azure-sdk-for-php)
    * [Servicios multimedia de Azure para Node.js](https://github.com/michelle-becker/node-ams-sdk/blob/master/lib/request.js) (es decir, una versión de un SDK de Node.js que no sea de Microsoft. Su mantenimiento corre a cargo de una comunidad y actualmente no tiene una cobertura del 100 % de las API de AMS).
* Herramientas existentes:
    * [Portal de Azure](https://portal.azure.com/)
    * [Azure-Media-Services-Explorer](https://github.com/Azure/Azure-Media-Services-Explorer) (El Explorador de servicios multimedia de Azure (AMSE) es una aplicación Winforms/C# para Windows)

En la ilustración siguiente, se muestran algunos de los objetos que se utilizan más a menudo al desarrollar con el modelo OData de Media Services.

Haga clic en la imagen para verla a tamaño completo.  

<a href="https://docs.microsoft.com/en-us/azure/media-services/media/media-services-overview/media-services-overview-object-model.png" target="_blank"><img src="./media/media-services-overview/media-services-overview-object-model-small.png"></a>  

Puede ver el modelo completo [aquí](https://media.windows.net/API/$metadata?api-version=2.15).  


## <a name="media-services-learning-paths"></a>Rutas de aprendizaje de Servicios multimedia
Puede ver las rutas de aprendizaje de Servicios multimedia de Azure aquí:

* [Flujo de trabajo de streaming en vivo de Servicios multimedia de Azure](https://azure.microsoft.com/documentation/learning-paths/media-services-streaming-live/)
* [Flujo de trabajo de streaming a petición de Servicios multimedia de Azure](https://azure.microsoft.com/documentation/learning-paths/media-services-streaming-on-demand/)

## <a name="prerequisites"></a>Requisitos previos

Para empezar a usar Servicios multimedia de Azure, debe tener lo siguiente:

1. Una cuenta de Azure. En caso de no tener ninguna, puede crear una cuenta de evaluación gratuita en tan solo unos minutos. Para obtener más información, consulte [Evaluación gratuita de Azure](https://azure.microsoft.com).
2. Una cuenta de Servicios multimedia de Azure. Use Azure Portal, .NET o API de REST para crear la cuenta de Azure Media Services. Para obtener más información, consulte [Creación de una cuenta](media-services-portal-create-account.md).
3. (Opcional) Configurar el entorno de desarrollo. Elija .NET o API de REST para el entorno de desarrollo. Para obtener más información, consulte [Configuración del entorno](media-services-dotnet-how-to-use.md).

    Además, aprenda a [conectarse mediante programación](media-services-dotnet-connect-programmatically.md).
4. Un punto de conexión de streaming estándar o premium en estado iniciado.  Para más información, consulte [Administración de puntos de conexión de streaming](https://docs.microsoft.com/en-us/azure/media-services/media-services-portal-manage-streaming-endpoints).

## <a name="concepts-and-overview"></a>Introducción y conceptos
Para conocer los conceptos de Servicios multimedia de Azure, consulte [Conceptos](media-services-concepts.md).

Para una serie de procedimientos en que se presentan todos los componentes principales de Servicios multimedia de Azure, consulte los [tutoriales paso a paso de Servicios multimedia de Azure](https://docs.com/fukushima-shigeyuki/3439/english-azure-media-services-step-by-step-series). Esta serie tiene una excelente introducción de conceptos y utiliza la herramienta AMSE para demostrar las tareas AMS. Tenga en cuenta que la herramienta AMSE es una herramienta de Windows. Esta herramienta admite la mayoría de las tareas que puede lograr mediante programación con el [SDK de AMS para .NET](https://github.com/Azure/azure-sdk-for-media-services), el [Azure SDK para Java](https://github.com/Azure/azure-sdk-for-java) o el [Azure PHP SDK](https://github.com/Azure/azure-sdk-for-php).

## <a name="a-idvodscenariosadelivering-media-on-demand-with-azure-media-services-common-scenarios-and-tasks"></a><a id="vod_scenarios"></a>Entrega de contenido multimedia a petición con Servicios multimedia de Azure: escenarios y tareas comunes
En esta sección se describe escenarios comunes y se proporcionan vínculos a temas relevantes. En el diagrama siguiente se muestran las partes principales de la plataforma de Servicios multimedia que intervienen en la entrega de contenido a petición.

![Flujo de trabajo de VoD](./media/media-services-video-on-demand-workflow/media-services-video-on-demand.png)

>[!NOTE]
>Cuando se crea la cuenta de AMS, se agrega un punto de conexión de streaming **predeterminado** a la cuenta en estado **Stopped** (Detenido). Para iniciar la transmisión del contenido y aprovechar el empaquetado dinámico y el cifrado dinámico, el punto de conexión de streaming desde el que va a transmitir el contenido debe estar en estado **Running** (En ejecución). 
    
### <a name="protect-content-in-storage-and-deliver-streaming-media-in-the-clear-non-encrypted"></a>Protección del contenido en almacenamiento y entrega de contenido multimedia en streaming sin cifrar
1. Cargue un archivo intermedio de alta calidad en un recurso.

    Se recomienda aplicar la opción de cifrado de almacenamiento a su recurso con el fin de proteger su contenido durante la carga y mientras se encuentra en reposo en el almacenamiento.
2. Codifique en conjunto de archivos MP4 de velocidades de bits adaptativas.

    Se recomienda aplicar la opción de cifrado de almacenamiento al recurso de salida con el fin de proteger su contenido mientras se encuentra en reposo.
3. Configure la directiva de entrega de recursos(usada por paquetes dinámicos).

    Si el recurso tiene el almacenamiento cifrado, **asegúrese** de configurar la directiva de entrega de recursos.
4. Publique el recurso mediante la creación de un localizador bajo demanda.
5. Trasmita el contenido publicado.

### <a name="protect-content-in-storage-deliver-dynamically-encrypted-streaming-media"></a>Protección del contenido en el almacenamiento, entrega de soportes de streaming cifrados dinámicamente

1. Cargue un archivo intermedio de alta calidad en un recurso. Aplique una opción de cifrado de almacenamiento al recurso.
2. Codifique en conjunto de archivos MP4 de velocidades de bits adaptativas. Aplique una opción de cifrado de almacenamiento al recurso de salida.
3. Cree la clave de cifrado de contenido para el recurso que desee que se cifre dinámicamente durante la reproducción.
4. Configure la directiva de autorización de claves de contenido.
5. Configure la directiva de entrega de recursos (usada por el empaquetado y el cifrado dinámicos).
6. Publique el recurso mediante la creación de un localizador bajo demanda.
7. Trasmita el contenido publicado.

### <a name="use-media-analytics-to-derive-actionable-insights-from-your-videos"></a>Use Análisis multimedia para obtener conocimientos útiles de los vídeos
Análisis multimedia es una colección de componentes de voz y visión que facilita a las empresas y organizaciones obtener conocimiento útil de sus archivos de vídeo. Para más información, consulte [Información general de análisis de Servicios multimedia de Azure](media-services-analytics-overview.md).

1. Cargue un archivo intermedio de alta calidad en un recurso.
2. Utilice uno de los siguientes servicios de Análisis multimedia para procesar sus vídeos:

   * **Indexador** : [procesa vídeos con Azure Media Indexer 2](media-services-process-content-with-indexer2.md)
   * **Hyperlapse** : [archivos multimedia de Hyperlapse con Azure Media Hyperlapse](media-services-hyperlapse-content.md)
   * **Detección de movimiento** : [detección de movimiento con Análisis multimedia de Azure](media-services-motion-detection.md).
   * **Detección de cara y emociones** : [detección de caras y emociones con Análisis multimedia de Azure](media-services-face-and-emotion-detection.md).
   * **Resumen de vídeo** : [uso de miniaturas de vídeo multimedia de Azure para crear un resumen de vídeo](media-services-video-summarization.md)
3. Los procesadores multimedia de Análisis multimedia generan archivos MP4 o JSON. Si un procesador multimedia genera un archivo MP4, puede descargar progresivamente el archivo. Si un procesador multimedia genera un archivo JSON, puede descargar el archivo desde Almacenamiento de blobs de Azure.

### <a name="deliver-progressive-download"></a>Entregar la descarga progresiva
1. Cargue un archivo intermedio de alta calidad en un recurso.
2. Codifique en un solo archivo MP4.
3. Publique el recurso creando un localizador OnDemand o SAS.

    Si utiliza un localizador SAS, el contenido se descarga desde el almacenamiento de blobs de Azure. En este caso, no necesita tener puntos de conexión de streaming en estado iniciado.
4. Descargue contenido de forma progresiva.

## <a name="a-idlivescenariosadelivering-live-streaming-events-with-azure-media-services"></a><a id="live_scenarios"></a>Entrega de eventos de streaming en vivo con Servicios multimedia de Azure
Cuando se trabaja con streaming en vivo, normalmente participan los siguientes componentes:

* Una cámara que se usa para difundir un evento.
* Un codificador de vídeo en directo que convierte las señales de la cámara en secuencias que se envían a un servicio de streaming en vivo.

Opcionalmente, varios codificadores sincronizados en directo. Para determinados eventos en directo críticos que demandan una disponibilidad y una calidad de experiencia muy altas, se recomienda emplear codificadores redundantes activo-activo con sincronización de hora para lograr una conmutación por error perfecta sin pérdida de datos.

* Un servicio de streaming en vivo que permita hacer lo siguiente:
* Recopilar contenido en directo mediante varios protocolos de streaming en vivo (por ejemplo RTMP o Smooth Streaming).
* (Opcionalmente) Codificar la transmisión en una transmisión con velocidad de bits adaptable.
* Mostrar una vista previa de la secuencia en vivo.
* Registrar y almacenar el contenido recibido para transmitirlo posteriormente (vídeo bajo demanda).
* Entregar el contenido mediante protocolos de streaming comunes (por ejemplo, MPEG DASH, Smooth o HLS) directamente a sus clientes o a una red de entrega de contenido (CDN) para ampliar la distribución.

**Microsoft Azure Media Services** (AMS) permite recopilar, codificar, mostrar una vista previa, almacenar y entregar el contenido de streaming en vivo.

Al entregar contenido a sus clientes, el objetivo es entregar un vídeo de alta calidad a diversos dispositivos en condiciones de red diferentes. Para abordar la calidad y las condiciones de la red, use codificadores en directo para codificar la secuencia en una secuencia de vídeo de velocidad de bits múltiple (velocidad de bits adaptativa).  Para abordar el streaming en diferentes dispositivos, use el [empaquetado dinámico](media-services-dynamic-packaging-overview.md) de Servicios multimedia para volver a empaquetar dinámicamente su secuencia para distintos protocolos. Media Services admite la entrega de las siguientes tecnologías de streaming con velocidad de bits adaptable: HTTP Live Streaming (HLS), Smooth Streaming y MPEG DASH.

En Azure Media Services, los **canales**, **programas** y **extremos de streaming** controlan todas las funcionalidades de streaming en vivo, incluidas la recopilación, el formato, DVR, la seguridad, la escalabilidad y la redundancia.

Un **canal** representa una canalización para procesar contenido de streaming en vivo. Un canal puede recibir transmisiones de entrada en directo de la siguiente manera:

* Un codificador local en directo envía contenido **RTMP** o **Smooth Streaming** (MP4 fragmentado) con velocidades de bits múltiples al canal que está configurado por la entrega de **paso a través**. La entrega de **paso a través** significa que las transmisiones ingeridas pasan a través de **canales** sin más transcodificación o codificación. Puede usar los siguientes codificadores en directo que generan Smooth Streaming con velocidad de bits múltiple: MediaExcel, Imagine Communications, Ateme, Envivio, Cisco and Elemental. Los siguientes codificadores en directo generan RTMP: Adobe Flash Live Encoder, Haivision, Telestream Wirecast, Teradek y Tricaster.  El codificador en directo también puede enviar una secuencia de una sola velocidad de bits a un canal que no está habilitado para la codificación en directo, pero esto no es recomendable. Cuando se solicita, Servicios multimedia entrega la secuencia a los clientes.

> [!NOTE]
> El uso de un método de paso a través es la forma más económica de streaming en vivo cuando está realizando varios eventos en un largo período y ya ha invertido en codificadores locales. Consulte los detalles en [Precios de Servicios multimedia](https://azure.microsoft.com/pricing/details/media-services/) .
>
>

* Un codificador en directo local envía una secuencia de una sola velocidad de bits al canal que está habilitado para realizar codificación en directo con Servicios multimedia, con uno de los siguientes formatos: RTP (MPEG-TS), RTMP o Smooth Streaming (MP4 fragmentado). Después, el canal codifica en directo la secuencia entrante de una sola velocidad de bits en una secuencia de vídeo de varias velocidades de bits (adaptable). Cuando se solicita, Servicios multimedia entrega la secuencia a los clientes.

### <a name="working-with-channels-that-receive-multi-bitrate-live-stream-from-on-premises-encoders-pass-through"></a>Uso de canales que reciben streaming en vivo con velocidad de bits múltiple de codificadores locales (paso a través)
En el diagrama siguiente se muestran las partes principales de la plataforma AMS que intervienen en el flujo de trabajo de **paso a través** .

![Flujo de trabajo activo][live-overview2]

Para más información, consulte [Uso de canales que reciben streaming en vivo con velocidad de bits múltiple de codificadores locales](media-services-live-streaming-with-onprem-encoders.md).

### <a name="working-with-channels-that-are-enabled-to-perform-live-encoding-with-azure-media-services"></a>Uso de canales habilitados para realizar la codificación en directo con Servicios multimedia de Azure
El diagrama siguiente muestra las partes principales de la plataforma AMS que intervienen en el flujo de trabajo de streaming en vivo, en la que se habilita un canal para realizar la codificación en directo con Servicios multimedia.

![Flujo de trabajo activo][live-overview1]

Para obtener más información, vea [Uso de canales habilitados para realizar la codificación en directo con Servicios multimedia de Azure](media-services-manage-live-encoder-enabled-channels.md).

## <a name="consuming-content"></a>Consumo de contenido
Servicios multimedia de Azure proporciona las herramientas que necesita para crear aplicaciones cliente de reproductor enriquecidas y dinámicas para la mayoría de las plataformas, como dispositivos iOS, dispositivos Android, Windows, Windows Phone, Xbox y decodificadores (set-top boxes). El tema siguiente proporciona vínculos a los SDK y Player Framework que puede usar para desarrollar sus propias aplicaciones cliente que pueden consumir contenido multimedia en streaming desde Servicios multimedia.

[Desarrollo de aplicaciones de reproductor de vídeo](media-services-develop-video-players.md)

## <a name="enabling-azure-cdn"></a>Habilitación de CDN de Azure
Servicios multimedia admite la integración con CDN de Azure. Para obtener información sobre cómo habilitar CDN de Azure, consulte [Administración de extremos de streaming en una cuenta de Servicios multimedia](media-services-portal-manage-streaming-endpoints.md).

## <a name="scaling-a-media-services-account"></a>Escalado de una cuenta de Servicios multimedia

Puede escalar **Media Services** mediante la especificación del número de **unidades reservadas de streaming** y **unidades reservadas de codificación** con las que desea aprovisionar la cuenta.

También puede escalar la cuenta de Servicios multimedia agregándole cuentas de almacenamiento. Cada cuenta de almacenamiento está limitada a 500 TB. Para ampliar el almacenamiento más allá del límite predeterminado, puede asociar varias cuentas de almacenamiento a una sola cuenta de Servicios multimedia.
Los clientes de Media Services eligen un punto de conexión de streaming **estándar** o uno o varios puntos de conexión de streaming **premium**, según sus necesidades. El punto de conexión de streaming estándar es adecuado para la mayoría de las cargas de trabajo de streaming. Incluye las mismas características que las unidades de streaming premium. El punto de conexión de streaming estándar es adecuado para la mayoría de las cargas de trabajo de streaming. Si tiene una carga de trabajo avanzada o sus requisitos de capacidad de streaming no se ajusta a los objetivos de rendimiento del punto de conexión de streaming estándar o desea controlar la capacidad del servicio de StreamingEndpoint para administrar las crecientes necesidades de ancho de banda mediante el ajuste de unidades de escalado (también conocido como unidades de streaming premium), se recomienda asignar unidades de escalado.

[Este](media-services-portal-scale-streaming-endpoints.md) tema contiene vínculos a temas relevantes.

## <a name="support"></a>Soporte técnico
[Soporte técnico de Azure](https://azure.microsoft.com/support/options/) proporciona opciones de soporte técnico para Azure, incluido Servicios multimedia.

## <a name="provide-feedback"></a>Envío de comentarios
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

## <a name="service-level-agreement-sla"></a>Contrato de nivel de servicio (SLA)
* Garantizamos la disponibilidad del 99,9% de las transacciones de API de REST para la codificación de Servicios multimedia.
* Para el streaming, atenderemos correctamente las solicitudes de servicio con una garantía de disponibilidad del 99,9 % para el contenido multimedia existente cuando se adquiera un punto de conexión de streaming estándar o premium.
* Para los canales en directo, garantizamos que los canales en ejecución tendrán una conectividad externa como mínimo el 99,9 % del tiempo.
* Para la protección de contenido, garantizamos que procesaremos correctamente las solicitudes clave como mínimo el 99,9% del tiempo.
* Para el indizador, atenderemos correctamente las solicitudes de tarea de indizador procesadas con una unidad reservada de codificación el 99,9% del tiempo.

Para obtener más información, consulte el [Contrato de nivel de servicio (SLA) de Microsoft Azure](https://azure.microsoft.com/support/legal/sla/).

<!-- Images -->
[overview]: ./media/media-services-overview/media-services-overview.png
[vod-overview]: ./media/media-services-video-on-demand-workflow/media-services-video-on-demand.png
[live-overview1]: ./media/media-services-live-streaming-workflow/media-services-live-streaming-new.png
[live-overview2]: ./media/media-services-live-streaming-workflow/media-services-live-streaming-current.png



<!--HONumber=Jan17_HO2-->


