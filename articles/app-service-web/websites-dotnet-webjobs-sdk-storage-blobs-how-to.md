---
title: Uso del almacenamiento de blobs de Azure con el SDK de WebJobs
description: "Obtenga información sobre cómo usar el almacenamiento de blobs de Azure con el SDK de WebJobs. Desencadene un proceso cuando un blob nuevo aparezca en un contenedor y controle los &quot;blobs dudosos&quot;."
services: app-service\web, storage
documentationcenter: .net
author: tdykstra
manager: wpickett
editor: 
ms.assetid: bf32f919-f7bc-4aaa-916e-461c02f2e26c
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 06/01/2016
ms.author: tdykstra
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 968df0fde8b042cdea369e566ecdb62937a3b8ee


---
# <a name="how-to-use-azure-blob-storage-with-the-webjobs-sdk"></a>Uso del almacenamiento de blobs de Azure con el SDK de WebJobs
## <a name="overview"></a>Información general
Esta guía proporciona muestras de código de C# que muestran la manera de desencadenar un proceso cuando se crea o actualiza un blob de Azure. Los ejemplos de código usan el [SDK de WebJobs](websites-dotnet-webjobs-sdk.md) versión 1.x.

En el caso de ejemplos de código que muestran cómo crear blobs, consulte [Cómo usar el almacenamiento en cola de Azure con el SDK de WebJobs](websites-dotnet-webjobs-sdk-storage-queues-how-to.md). 

En la guía se supone que sabe [cómo crear un proyecto de WebJob en Visual Studio con cadenas de conexión que señalan a su cuenta de almacenamiento](websites-dotnet-webjobs-sdk-get-started.md) o a [varias cuentas de almacenamiento](https://github.com/Azure/azure-webjobs-sdk/blob/master/test/Microsoft.Azure.WebJobs.Host.EndToEndTests/MultipleStorageAccountsEndToEndTests.cs).

## <a name="a-idtriggera-how-to-trigger-a-function-when-a-blob-is-created-or-updated"></a><a id="trigger"></a> Cómo desencadenar una función cuando se crea o actualiza un blob
Esta sección muestra cómo usar el atributo `BlobTrigger` . 

> [!NOTE]
> El SDK de WebJobs analiza los archivos de registro en busca de blobs nuevos o modificados. Este proceso no se produce en tiempo real; podrían tardarse varios minutos o más en desencadenar una función después de crear el blob. Además, los [registros de almacenamiento se crean como "el mejor esfuerzo"](https://msdn.microsoft.com/library/azure/hh343262.aspx) ; no hay ninguna garantía de que se capturarán todos los eventos. En algunos casos, podrían faltar registros. Si los límites de velocidad y confiabilidad de los desencadenadores de blobs no son aceptables para su aplicación, el método recomendado es crear un mensaje de cola al crear el blob y usar el atributo [QueueTrigger](websites-dotnet-webjobs-sdk-storage-queues-how-to.md#trigger) en lugar del atributo `BlobTrigger` en la función que procesa el blob.
> 
> 

### <a name="single-placeholder-for-blob-name-with-extension"></a>Único marcador de posición para el nombre de blob con extensión
El siguiente ejemplo de código copia blobs de texto que aparecen en el contenedor *input* al contenedor *output*:

        public static void CopyBlob([BlobTrigger("input/{name}")] TextReader input,
            [Blob("output/{name}")] out string output)
        {
            output = input.ReadToEnd();
        }

El constructor de atributo toma un parámetro de cadena que especifica el nombre del contenedor y un marcador de posición para el nombre del blob. En este ejemplo, si se crea un blob llamado *Blob1.txt* en el contenedor *input*, la función crea un blob llamado *Blob1.txt* en el contenedor *output*. 

Puede especificar un patrón de nombre con el marcador de posición de nombre de blob, como se muestra en el siguiente ejemplo de código:

        public static void CopyBlob([BlobTrigger("input/original-{name}")] TextReader input,
            [Blob("output/copy-{name}")] out string output)
        {
            output = input.ReadToEnd();
        }

Este código solo copia blobs con nombres que comienzan con "original-". Por ejemplo, *original-Blob1.txt* en el contenedor *input* se copia en *copy- Blob1.txt* en el contenedor *output*.

Si necesita especificar un patrón de nombre para nombres de blob que contienen llaves en el nombre, duplique las llaves. Por ejemplo, si desea encontrar blobs en el contenedor *images* que tengan nombres como este:

        {20140101}-soundfile.mp3

use esto para el patrón:

        images/{{20140101}}-{name}

En el ejemplo, el valor de marcador de posición *name* sería *soundfile.mp3*. 

### <a name="separate-blob-name-and-extension-placeholders"></a>Separar marcadores de posición de extensión y nombre de blob
El siguiente ejemplo de código cambia la extensión de archivo cuando copia los blobs que aparecen en el contenedor *input* al contenedor *output*. El código registra la extensión del blob *input* y establece la extensión del blob *output* en *.txt*.

        public static void CopyBlobToTxtFile([BlobTrigger("input/{name}.{ext}")] TextReader input,
            [Blob("output/{name}.txt")] out string output,
            string name,
            string ext,
            TextWriter logger)
        {
            logger.WriteLine("Blob name:" + name);
            logger.WriteLine("Blob extension:" + ext);
            output = input.ReadToEnd();
        }

## <a name="a-idtypesa-types-that-you-can-bind-to-blobs"></a><a id="types"></a> Tipos que puede enlazar a blobs
Puede utilizar el atributo `BlobTrigger` en los siguientes tipos:

* `string`
* `TextReader`
* `Stream`
* `ICloudBlob`
* `CloudBlockBlob`
* `CloudPageBlob`
* `CloudBlobContainer`
* `CloudBlobDirectory`
* `IEnumerable<CloudBlockBlob>`
* `IEnumerable<CloudPageBlob>`
* Otros tipos deserializados por [ICloudBlobStreamBinder](#icbsb) 

Si desea trabajar directamente con la cuenta de almacenamiento de Azure, también puede agregar un parámetro `CloudStorageAccount` a la firma del método.

Para ver ejemplos, consulte el [código de enlace de blob en el repositorio azure-webjobs-sdk en GitHub.com](https://github.com/Azure/azure-webjobs-sdk/blob/master/test/Microsoft.Azure.WebJobs.Host.EndToEndTests/BlobBindingEndToEndTests.cs).

## <a name="a-idstringa-getting-text-blob-content-by-binding-to-string"></a><a id="string"></a> Obtención del contenido del blob de texto enlazando a la cadena
Si se esperan blobs de texto, se puede aplicar `BlobTrigger` a un parámetro `string`. El siguiente ejemplo de código enlaza un blob de texto a un parámetro `string` llamado `logMessage`. La función usa ese parámetro para escribir el contenido del blob en el panel del SDK de WebJobs. 

        public static void WriteLog([BlobTrigger("input/{name}")] string logMessage,
            string name, 
            TextWriter logger)
        {
             logger.WriteLine("Blob name: {0}", name);
             logger.WriteLine("Content:");
             logger.WriteLine(logMessage);
        }

## <a name="a-idicbsba-getting-serialized-blob-content-by-using-icloudblobstreambinder"></a><a id="icbsb"></a> Obtención del contenido del blob serializado mediante el uso de ICloudBlobStreamBinder
El siguiente ejemplo de código usa una clase que implementa `ICloudBlobStreamBinder` para permitir que el atributo `BlobTrigger` vincule un blob al tipo `WebImage`.

        public static void WaterMark(
            [BlobTrigger("images3/{name}")] WebImage input,
            [Blob("images3-watermarked/{name}")] out WebImage output)
        {
            output = input.AddTextWatermark("WebJobs SDK", 
                horizontalAlign: "Center", verticalAlign: "Middle",
                fontSize: 48, opacity: 50);
        }
        public static void Resize(
            [BlobTrigger("images3-watermarked/{name}")] WebImage input,
            [Blob("images3-resized/{name}")] out WebImage output)
        {
            var width = 180;
            var height = Convert.ToInt32(input.Height * 180 / input.Width);
            output = input.Resize(width, height);
        }

El código de enlace `WebImage` se proporciona en una clase `WebImageBinder` que deriva de `ICloudBlobStreamBinder`.

        public class WebImageBinder : ICloudBlobStreamBinder<WebImage>
        {
            public Task<WebImage> ReadFromStreamAsync(Stream input, 
                System.Threading.CancellationToken cancellationToken)
            {
                return Task.FromResult<WebImage>(new WebImage(input));
            }
            public Task WriteToStreamAsync(WebImage value, Stream output,
                System.Threading.CancellationToken cancellationToken)
            {
                var bytes = value.GetBytes();
                return output.WriteAsync(bytes, 0, bytes.Length, cancellationToken);
            }
        }

## <a name="getting-the-blob-path-for-the-triggering-blob"></a>Obtención de la ruta de acceso del blob de desencadenamiento
Para obtener el nombre del contenedor y el del blob que desencadenó la función, incluya un parámetro de cadena `blobTrigger` en la firma de función.

        public static void WriteLog([BlobTrigger("input/{name}")] string logMessage,
            string name,
            string blobTrigger,
            TextWriter logger)
        {
             logger.WriteLine("Full blob path: {0}", blobTrigger);
             logger.WriteLine("Content:");
             logger.WriteLine(logMessage);
        }


## <a name="a-idpoisona-how-to-handle-poison-blobs"></a><a id="poison"></a> Cómo administrar los blobs dudosos
Cuando una función `BlobTrigger` presenta un error, el SDK vuelve a llamarla, en caso de que se hubiese producido por un error transitorio. Si el error se produce debido al contenido del blob, la función presentará un error cada vez que intente procesar el blob. De manera predeterminada, el SDK llama una función hasta cinco veces para un blob determinado. Si el quinto intento falla, el SDK agrega un mensaje a una cola llamada *webjobs-blobtrigger-poison*.

Es posible configurar el número máximo de reintentos. Se usa la misma configuración [MaxDequeueCount](websites-dotnet-webjobs-sdk-storage-queues-how-to.md#configqueue) para controlar los blobs dudosos y los mensajes de cola dudosos. 

El mensaje de cola para los blobs dudosos es un objeto JSON que contiene las siguientes propiedades:

* FunctionId (con el formato *{nombre de WebJob}*.Functions.*{nombre de función}*, por ejemplo: WebJob1.Functions.CopyBlob)
* BlobType ("BlockBlob" o "PageBlob")
* ContainerName
* BlobName
* ETag (un identificador de la versión del blob, por ejemplo: "0x8D1DC6E70A277EF")

En el siguiente ejemplo de código, la función `CopyBlob` tiene un código que hace que presente un error cada vez que se la llama. Una vez que el SDK la llama por el número máximo de reintentos, se crea un mensaje en la cola de blobs dudosos y la función `LogPoisonBlob` procesa ese mensaje. 

        public static void CopyBlob([BlobTrigger("input/{name}")] TextReader input,
            [Blob("textblobs/output-{name}")] out string output)
        {
            throw new Exception("Exception for testing poison blob handling");
            output = input.ReadToEnd();
        }

        public static void LogPoisonBlob(
        [QueueTrigger("webjobs-blobtrigger-poison")] PoisonBlobMessage message,
            TextWriter logger)
        {
            logger.WriteLine("FunctionId: {0}", message.FunctionId);
            logger.WriteLine("BlobType: {0}", message.BlobType);
            logger.WriteLine("ContainerName: {0}", message.ContainerName);
            logger.WriteLine("BlobName: {0}", message.BlobName);
            logger.WriteLine("ETag: {0}", message.ETag);
        }

El SDK deserializa automáticamente el mensaje JSON. Esta es la clase `PoisonBlobMessage` : 

        public class PoisonBlobMessage
        {
            public string FunctionId { get; set; }
            public string BlobType { get; set; }
            public string ContainerName { get; set; }
            public string BlobName { get; set; }
            public string ETag { get; set; }
        }

### <a name="a-idpollinga-blob-polling-algorithm"></a><a id="polling"></a> Algoritmo de sondeo de blobs
El SDK de WebJobs examina todos los contenedores especificados por los atributos `BlobTrigger` en el inicio de la aplicación. En una cuenta de almacenamiento de gran tamaño, este análisis puede demorar un tiempo, por lo que podría pasar mucho tiempo antes de que se encuentren blobs nuevos y que se ejecuten las funciones `BlobTrigger` .

Para detectar blobs nuevos o cambiados después del inicio de la aplicación, el SDK lee de manera periódica de los registros de almacenamiento de blobs. Los registros de blobs se almacenan en búfer y solo se escriben físicamente cada diez minutos aproximadamente, por lo que puede haber un retraso importante una vez que se crea o actualiza un blob antes de que la función `BlobTrigger` correspondiente se ejecute. 

Existe una excepción para los blobs que crea mediante el uso del atributo `Blob` . Cuando el SDK de WebJobs crea un blob nuevo, pasa el blob nuevo inmediatamente a cualquier función `BlobTrigger` coincidente. Por tanto, si tiene una cadena de entradas y salidas categorizadas como blob, el SDK puede procesarlas de manera eficaz. Pero si desea baja latencia de ejecución para sus funciones de procesamiento de blobs para blobs que se crean o actualizan mediante otros medios, se recomienda usar `QueueTrigger` en lugar de `BlobTrigger`.

### <a name="a-idreceiptsa-blob-receipts"></a><a id="receipts"></a> Recepciones de blobs
El SDK de WebJobs se asegura de que ninguna función `BlobTrigger` se llame más de una vez para el mismo blob nuevo o actualizado. Para hacerlo, mantiene *recepciones de blobs* para determinar si se ha procesado alguna versión de blob determinada.

Las recepciones de blobs se almacenan en un contenedor llamado *azure-webjobs-hosts* en la cuenta de almacenamiento de Azure que especifica la cadena de conexión AzureWebJobsStorage. Una recepción de blobs tiene la información siguiente:

* La función que se llamó para el blob ("*{nombre de WebJob}*.Functions.*{nombre de función}*", por ejemplo: "WebJob1.Functions.CopyBlob")
* El nombre del contenedor
* El tipo de blob ("BlockBlob" o "PageBlob")
* El nombre del blob
* ETag (un identificador de la versión del blob, por ejemplo: "0x8D1DC6E70A277EF")

Si desea forzar el reprocesamiento de un blob, puede eliminar manualmente la recepción de blob de ese blob desde el contenedor *azure-webjobs-hosts* .

## <a name="a-idqueuesarelated-topics-covered-by-the-queues-article"></a><a id="queues"></a>Temas relacionados tratados en el artículo sobre colas
Para obtener información sobre cómo controlar el procesamiento de blobs desencadenado por un mensaje de cola o para ver los escenarios de SDK de WebJobs no específicos para el procesamiento de blobs, consulte [Cómo usar el almacenamiento de cola de Azure con el SDK de WebJobs](websites-dotnet-webjobs-sdk-storage-queues-how-to.md). 

Los temas relacionados tratados en ese artículo incluyen:

* Funciones asincrónicas
* Varias instancias
* Apagado correcto
* Utilizar atributos del SDK de WebJobs en el cuerpo de una función
* Establecer las cadenas de conexión de SDK en el código.
* Establecer valores para los parámetros del constructor del SDK de WebJobs en el código
* Configurar `MaxDequeueCount` para el control de blobs dudosos.
* Desencadenar una función manualmente
* Escribir registros

## <a name="a-idnextstepsa-next-steps"></a><a id="nextsteps"></a> Pasos siguientes
En esta guía se han proporcionado ejemplos de código que muestran cómo controlar los escenarios comunes para trabajar con blobs de Azure. Para obtener más información acerca de cómo usar el SDK de WebJobs y WebJobs de Azure, consulte [Recursos de WebJobs de Azure recomendados](http://go.microsoft.com/fwlink/?linkid=390226).




<!--HONumber=Nov16_HO3-->


