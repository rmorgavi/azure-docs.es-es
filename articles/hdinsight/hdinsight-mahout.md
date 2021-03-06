---
title: "Generación de recomendaciones mediante Mahout y HDInsight basado en Windows | Microsoft Docs"
description: "Aprenda a usar la biblioteca de aprendizaje automático de Apache Mahout para generar recomendaciones de películas con HDInsight basado en Windows (Hadoop)."
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 07b57208-32aa-4e59-900a-6c934fa1b7a7
ms.service: hdinsight
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/11/2016
ms.author: larryfr
translationtype: Human Translation
ms.sourcegitcommit: fc79b8017f2184091f2473a0ff9cdfbd0a4cbdf8
ms.openlocfilehash: c7499dba829e24e957cae47dae88599013c3de95


---
# <a name="generate-movie-recommendations-by-using-apache-mahout-with-hadoop-in-hdinsight"></a>Generación de recomendaciones de películas mediante Apache Mahout con Hadoop en HDInsight
[!INCLUDE [mahout-selector](../../includes/hdinsight-selector-mahout.md)]

Aprenda a usar la biblioteca de aprendizaje automático de [Apache Mahout](http://mahout.apache.org) con HDInsight de Azure para generar recomendaciones de películas con HDInsight.

> [!NOTE]
> Los pasos de este documento requieren un cliente Windows y un clúster de HDInsight basado en Windows. Para obtener información sobre el uso de Mahout en un cliente Linux, OS X de Unix, con un clúster de HDInsight basado en Linux, consulte [Generate movie recommendations by using Apache Mahout with Linux-based Hadoop in HDInsight (Generación de recomendaciones de película a través de Apache Mahout con Hadoop basado en Linux en HDInsight)](hdinsight-hadoop-mahout-linux-mac.md)

## <a name="a-namelearnawhat-you-will-learn"></a><a name="learn"></a>Qué aprenderá
Mahout es una biblioteca de[aprendizaje automático][ml] para Apache Hadoop. Mahout contiene algoritmos para el procesamiento de datos, como filtrado, clasificación y agrupación en clústeres. En este artículo usará un motor de recomendaciones para generar recomendaciones de películas que se basan en películas que sus amigos han visto. También aprenderá a realizar clasificaciones con un bosque de decisiones. Esto le enseñará a:

* Cómo ejecutar trabajos de Mahout mediante Windows PowerShell
* Ejecutar trabajos de Mahout desde la línea de comandos de Hadoop
* Cómo instalar Mahout en clústeres de HDInsight 3.0 y HDInsight 2.0

  > [!NOTE]
  > Mahout se proporciona con la versión 3.1 de HDInsight de los clústeres. Si va a usar una versión anterior de HDInsight, vea [Instalación de Mahout](#install) antes de continuar.

## <a name="prerequisites"></a>Requisitos previos
* **Un clúster de Hadoop basado en Windows en HDInsight**. Para obtener información sobre cómo crearlo, consulte [Hadoop tutorial: Get started with Hadoop and a Hive query in HDInsight on Windows][getstarted] (Tutorial de Hadoop: introducción a Hadoop y una consulta de Hive en in HDInsight en Windows).
* **Una estación de trabajo con Azure PowerShell**.

    > [!IMPORTANT]
    > La compatibilidad con Azure PowerShell para administrar recursos de HDInsight mediante Azure Service Manager está **en desuso** y desaparecerá por completo el 1 de enero de 2017. En los pasos descritos en este documento, se usan los nuevos cmdlets de HDInsight que funcionan con Azure Resource Manager.
    >
    > Para instalar la versión más reciente, siga los pasos descritos en [Cómo instalar y configurar Azure PowerShell](/powershell/azureps-cmdlets-docs) . Si tiene scripts que se deben modificar para usar los nuevos cmdlets que funcionan con Azure Resource Manager, consulte [Migrating to Azure Resource Manager-based development tools for HDInsight clusters](hdinsight-hadoop-development-using-azure-resource-manager.md) (Migración a herramientas de desarrollo basadas en Azure Resource Manager para clústeres de HDInsight) para más información.

## <a name="a-namerecommendationsagenerate-recommendations-by-using-windows-powershell"></a><a name="recommendations"></a>Generación de recomendaciones mediante Windows PowerShell
> [!NOTE]
> Aunque el trabajo usado en esta sección funciona con Windows PowerShell, muchas de las clases proporcionadas con Mahout no funcionan actualmente con Windows PowerShell y se deben ejecutar mediante la línea de comandos de Hadoop. Para obtener una lista de las clases que no funcionan con Windows PowerShell, consulte la sección [Solución de problemas](#troubleshooting) .
>
> Para ver un ejemplo del uso de la línea de comandos de Hadoop para ejecutar trabajos de Mahout, consulte [Clasificación de datos mediante la línea de comandos de Hadoop](#classify).

Una de las funciones que proporciona Mahout es un motor de recomendaciones. Este motor acepta datos en formato de `userID`, `itemId` y `prefValue` (la preferencia de los usuarios por el elemento). Mahout puede realizar entonces análisis de ocurrencias conjuntas para determinar: *los usuarios que tienen predilección por un elemento también la tienen por estos otros elementos*. Mahout determinará los usuarios con preferencias de elementos similares, lo que se puede usar para realizar recomendaciones.

A continuación se muestra un ejemplo muy sencillo que usa películas:

* **Ocurrencia conjunta**: a José, Alicia y Roberto les gusta *La Guerra de las galaxias*, *El imperio contraataca* y *El retorno del Jedi*. Mahout determina que a los usuarios que les gusta alguna de estas películas también les gustan las otras dos.
* **Ocurrencia conjunta**: a Roberto y Alicia también les gusta *La amenaza fantasma*, *El ataque de los clones* y *La venganza de los Sith*. Mahout determina que a los usuarios que les gustan las tres películas anteriores también les gustan estas tres.
* **Recomendación basada en similitud**: como a José le gustan las tres primeras películas, Mahout examina películas que a otros usuarios con preferencias similares les han gustado, pero que José no ha visto (gustado/valorado). En este caso, Mahout recomendaría *La amenaza fantasma*, *El ataque de los clones* y *La venganza de los Sith*.

### <a name="understanding-the-data"></a>Descripción de los datos
Para su comodidad, [GroupLens Research][movielens] proporciona calificaciones de películas en un formato compatible con Mahout. Estos datos están disponibles en el almacenamiento predeterminado del clúster en `/HdiSamples/MahoutMovieData`.

Existen dos archivos, `moviedb.txt` (información sobre las películas) y `user-ratings.txt`. El archivo user-ratings.txt se usa durante el análisis, mientras el archivo moviedb.txt se usa para proporcionar información de texto descriptiva al mostrar los resultados del análisis.

Los datos del archivo user-ratings.txt tienen una estructura de `userID`, `movieID`, `userRating` y `timestamp`, que nos indica qué valoración le dio cada usuario a una película. A continuación se muestra un ejemplo de los datos:

    196    242    3    881250949
    186    302    3    891717742
    22    377    1    878887116
    244    51    2    880606923
    166    346    1    886397596

### <a name="run-the-job"></a>Ejecución del trabajo
Use el siguiente script de Windows PowerShell para ejecutar un trabajo que use el motor de recomendaciones de Mahout con los datos de las películas:

```powershell
# The HDInsight cluster name.
$clusterName = "the cluster name"

#Get HTTPS/Admin credentials for submitting the job later
$creds = Get-Credential
#Get the cluster info so we can get the resource group, storage, etc.
$clusterInfo = Get-AzureRmHDInsightCluster -ClusterName $clusterName
$resourceGroup = $clusterInfo.ResourceGroup
$storageAccountName=$clusterInfo.DefaultStorageAccount.split('.')[0]
$container=$clusterInfo.DefaultStorageContainer
$storageAccountKey=(Get-AzureRmStorageAccountKey `
    -Name $storageAccountName `
    -ResourceGroupName $resourceGroup)[0].Value

#Create a storage content and upload the file
$context = New-AzureStorageContext `
    -StorageAccountName $storageAccountName `
    -StorageAccountKey $storageAccountKey

# NOTE: The version number in the file path
# may change in future versions of HDInsight.
$jarFile =  "file:///C:/apps/dist/mahout-0.9.0.2.2.9.1-8/examples/target/mahout-examples-0.9.0.2.2.9.1-8-job.jar"
#
# If you are using an earlier version of HDInsight,
# set $jarFile to the jar file you
# uploaded.
# For example,
# $jarFile = "wasbs:///example/jars/mahout-core-0.9-job.jar"

# The arguments for this job
# * input - the path to the data uploaded to HDInsight
# * output - the path to store output data
# * tempDir - the directory for temp files
$jobArguments = "--similarityClassname", "recommenditembased", `
                "-s", "SIMILARITY_COOCCURRENCE", `
                "--input", "wasbs:///HdiSamples/MahoutMovieData/user-ratings.txt",
                "--output", "wasbs:///example/out",
                "--tempDir", "wasbs:///example/temp"

# Create the job definition
$jobDefinition = New-AzureRmHDInsightMapReduceJobDefinition `
    -JarFile $jarFile `
    -ClassName "org.apache.mahout.cf.taste.hadoop.item.RecommenderJob" `
    -Arguments $jobArguments

# Start the job
$job = Start-AzureRmHDInsightJob `
    -ClusterName $clusterName `
    -JobDefinition $jobDefinition `
    -HttpCredential $creds

# Wait on the job to complete
Write-Host "Wait for the job to complete ..." -ForegroundColor Green
Wait-AzureRmHDInsightJob `
        -ClusterName $clusterName `
        -JobId $job.JobId `
        -HttpCredential $creds
# Download the output
Get-AzureStorageBlobContent `
        -Blob example/out/part-r-00000 `
        -Container $container `
        -Destination output.txt `
        -Context $context

# Write out any error information
Write-Host "STDERR"
Get-AzureRmHDInsightJobOutput `
        -Clustername $clusterName `
        -JobId $job.JobId `
        -DefaultContainer $container `
        -DefaultStorageAccountName $storageAccountName `
        -DefaultStorageAccountKey $storageAccountKey `
        -HttpCredential $creds `
        -DisplayOutputType StandardError
```

> [!NOTE]
> Los trabajos de Mahout no eliminan los datos temporales creados durante el procesamiento del trabajo. El parámetro `--tempDir` se especifica en el trabajo de ejemplo para aislar los archivos temporales en un directorio específico.

El trabajo de Mahout no devuelve la salida a STDOUT. En su lugar, lo almacena en el directorio de salida especificado como **part-r-00000**. El script descarga este archivo en **output.txt** en el directorio actual de la estación de trabajo.

A continuación se muestra un ejemplo del contenido de este archivo:

    1    [234:5.0,347:5.0,237:5.0,47:5.0,282:5.0,275:5.0,88:5.0,515:5.0,514:5.0,121:5.0]
    2    [282:5.0,210:5.0,237:5.0,234:5.0,347:5.0,121:5.0,258:5.0,515:5.0,462:5.0,79:5.0]
    3    [284:5.0,285:4.828125,508:4.7543354,845:4.75,319:4.705128,124:4.7045455,150:4.6938777,311:4.6769233,248:4.65625,272:4.649266]
    4    [690:5.0,12:5.0,234:5.0,275:5.0,121:5.0,255:5.0,237:5.0,895:5.0,282:5.0,117:5.0]

La primera columna es `userID`. Los valores contenidos en '[' y ']' son `movieId`:`recommendationScore`.

### <a name="view-the-output"></a>Visualización de la salida
Aunque puede que la salida generada esté bien para usarse en una aplicación, no es muy legible. El archivo `moviedb.txt` del servidor se puede usar para resolver `movieId` en un nombre de película, pero antes debe descargar este archivo y el de valoraciones del servidor mediante el siguiente script:

```powershell
# The HDInsight cluster name.
$clusterName = "the cluster name"

#Get the cluster info so we can get the resource group, storage, etc.
$clusterInfo = Get-AzureRmHDInsightCluster -ClusterName $clusterName
$resourceGroup = $clusterInfo.ResourceGroup
$storageAccountName=$clusterInfo.DefaultStorageAccount.split('.')[0]
$container=$clusterInfo.DefaultStorageContainer
$storageAccountKey=(Get-AzureRmStorageAccountKey `
    -Name $storageAccountName `
    -ResourceGroupName $resourceGroup)[0].Value
#Create a storage content and upload the file
$context = New-AzureStorageContext `
    -StorageAccountName $storageAccountName `
    -StorageAccountKey $storageAccountKey
#Download the files
Get-AzureStorageBlobContent -blob "HdiSamples/MahoutMovieData/moviedb.txt" `
-Container $container `
-Destination moviedb.txt `
-Context $context
Get-AzureStorageBlobContent -blob "HdiSamples/MahoutMovieData/user-ratings.txt" `
-Container $container `
-Destination user-ratings.txt `
-Context $context
```

Una vez haya descargado los archivos, use el siguiente script de PowerShell para mostrar recomendaciones con nombres de película:

```powershell
<#
.SYNOPSIS
    Displays recommendations for movies.
.DESCRIPTION
    Displays recommendations generated by Mahout
    with HDInsight example in a human readable format.
.EXAMPLE
    .\Show-Recommendation -userId 4
        -userDataFile "user-ratings.txt"
        -movieFile "moviedb.txt"
        -recommendationFile "output.txt"
#>

[CmdletBinding(SupportsShouldProcess = $true)]
param(
    #The user ID
    [Parameter(Mandatory = $true)]
    [String]$userId,

    [Parameter(Mandatory = $true)]
    [String]$userDataFile,

    [Parameter(Mandatory = $true)]
    [String]$movieFile,

    [Parameter(Mandatory = $true)]
    [String]$recommendationFile
)
# Read movie ID & description into hash table
Write-Host "Reading movies descriptions" -ForegroundColor Green
$movieById = @{}
foreach($line in Get-Content $movieFile)
{
    $tokens = $line.Split("|")
    $movieById[$tokens[0]] = $tokens[1]
}
# Load movies user has already seen (rated)
# into a hash table
Write-Host "Reading rated movies" -ForegroundColor Green
$ratedMovieIds = @{}
foreach($line in Get-Content $userDataFile)
{
    $tokens = $line.Split("`t")
    if($tokens[0] -eq $userId)
    {
        # Resolve the ID to the movie name
        $ratedMovieIds[$movieById[$tokens[1]]] = $tokens[2]
    }
}
# Read recommendations generated by Mahout
Write-Host "Reading recommendations" -ForegroundColor Green
$recommendations = @{}
foreach($line in get-content $recommendationFile)
{
    $tokens = $line.Split("`t")
    if($tokens[0] -eq $userId)
    {
        #Trim leading/treailing [] and split at ,
        $movieIdAndScores = $tokens[1].TrimStart("[").TrimEnd("]").Split(",")
        foreach($movieIdAndScore in $movieIdAndScores)
        {
            #Split at : and store title and score in a hash table
            $idAndScore = $movieIdAndScore.Split(":")
            $recommendations[$movieById[$idAndScore[0]]] = $idAndScore[1]
        }
        break
    }
}

Write-Host "Rated movies" -ForegroundColor Green
Write-Host "---------------------------" -ForegroundColor Green
$ratedFormat = @{Expression={$_.Name};Label="Movie";Width=40}, `
                @{Expression={$_.Value};Label="Rating"}
$ratedMovieIds | format-table $ratedFormat
Write-Host "---------------------------" -ForegroundColor Green

write-host "Recommended movies" -ForegroundColor Green
Write-Host "---------------------------" -ForegroundColor Green
$recommendationFormat = @{Expression={$_.Name};Label="Movie";Width=40}, `
                        @{Expression={$_.Value};Label="Score"}
$recommendations | format-table $recommendationFormat
```

A continuación se muestra un ejemplo de la ejecución del script:

    PS C:\> show-recommendation.ps1 -userId 4 -userDataFile .\user-ratings.txt -movieFile .\moviedb.txt -recommendationFile .\output.txt

La salida debe ser similar a la siguiente:

    Reading movies descriptions
    Reading rated movies
    Reading recommendations
    Rated movies
    ---------------------------
    Movie                                    Rating
    -----                                    ------
    Devil's Own, The (1997)                  1
    Alien: Resurrection (1997)               3
    187 (1997)                               2
    (lines ommitted)

    ---------------------------
    Recommended movies
    ---------------------------

    Movie                                    Score
    -----                                    -----
    Good Will Hunting (1997)                 4.6504064
    Swingers (1996)                          4.6862745
    Wings of the Dove, The (1997)            4.6666665
    People vs. Larry Flynt, The (1996)       4.834559
    Everyone Says I Love You (1996)          4.707071
    Secrets & Lies (1996)                    4.818182
    That Thing You Do! (1996)                4.75
    Grosse Pointe Blank (1997)               4.8235292
    Donnie Brasco (1997)                     4.6792455
    Lone Star (1996)                         4.7099237

## <a name="a-nameclassifyaclassify-data-by-using-the-hadoop-command-line"></a><a name="classify"></a>Clasificación de datos mediante la línea de comandos de Hadoop
Uno de los métodos de clasificación disponibles con Mahout es compilar un [bosque aleatorio][forest]. Se trata de un proceso de varios pasos que supone el uso de datos de aprendizaje para generar un árbol de decisiones, que luego se usará para clasificar los datos. Para ello se utiliza la clase **org.apache.mahout.classifier.df.tools.Describe** que se proporciona en Mahout. Actualmente se debe ejecutar con la línea de comandos de Hadoop.

### <a name="load-the-data"></a>Carga de los datos
1. Descargue los siguientes archivos desde [The NSL-KDD Data Set](http://nsl.cs.unb.ca/NSL-KDD/).

   * [KDDTrain+.ARFF](http://nsl.cs.unb.ca/NSL-KDD/KDDTrain+.arff): el archivo del curso
   * [KDDTest+.ARFF](http://nsl.cs.unb.ca/NSL-KDD/KDDTest+.arff): los datos de prueba
2. Abra cada uno de los archivos y elimine las líneas de la parte superior que comienzan por '@',. Luego, guarde los archivos. Si no las elimina, recibirá mensajes de error cuando use estos datos con Mahout.
3. Cargue los archivos en **example/data**. Para ello, puede usar el siguiente script. Reemplace **CLUSTERNAME** por el nombre del clúster de HDInsight. Reemplace FILENAME por el nombre del archivo que se va a cargar.

    ```powershell
    #Get the cluster info so we can get the resource group, storage, etc.
    $clusterName="CLUSTERNAME"
    $fileToUpload="FILENAME"
    $blobPath="example/data/FILENAME"
    $clusterInfo = Get-AzureRmHDInsightCluster -ClusterName $clusterName
    $resourceGroup = $clusterInfo.ResourceGroup
    $storageAccountName=$clusterInfo.DefaultStorageAccount.split('.')[0]
    $container=$clusterInfo.DefaultStorageContainer
    $storageAccountKey=(Get-AzureRmStorageAccountKey `
        -Name $storageAccountName `
    -ResourceGroupName $resourceGroup)[0].Value

    #Create a storage content and upload the file
    $context = New-AzureStorageContext `
        -StorageAccountName $storageAccountName `
        -StorageAccountKey $storageAccountKey

    Set-AzureStorageBlobContent `
        -File $fileToUpload `
        -Blob $blobPath `
        -Container $container `
        -Context $context
    ```

### <a name="run-the-job"></a>Ejecución del trabajo
1. Este trabajo requiere la línea de comandos de Hadoop. Habilite el Escritorio remoto para el clúster de HDInsight y conéctese a él siguiendo las instrucciones dadas en [Conexión a los clústeres de HDInsight con RDP](hdinsight-administer-use-management-portal.md#connect-to-clusters-using-rdp).
2. Después de conectar, use el icono de **línea de comandos de Hadoop** para abrir la línea de comandos de Hadoop.

    ![hadoop cli][hadoopcli]
3. Use el siguiente comando para generar el descriptor de archivos (**KDDTrain+.info**), que usa Mahout.

        hadoop jar "c:/apps/dist/mahout-0.9.0.2.2.9.1-8/examples/target/mahout-examples-0.9.0.2.2.9.1-8-job.jar" org.apache.mahout.classifier.df.tools.Describe -p "wasbs:///example/data/KDDTrain+.arff" -f "wasbs:///example/data/KDDTrain+.info" -d N 3 C 2 N C 4 N C 8 N 2 C 19 N L

    `N 3 C 2 N C 4 N C 8 N 2 C 19 N L` describe los atributos de los datos del archivo. Por ejemplo, L indica una etiqueta.
4. Compile un bosque de árboles de decisiones mediante el siguiente comando:

        hadoop jar c:/apps/dist/mahout-0.9.0.2.2.9.1-8/examples/target/mahout-examples-0.9.0.2.2.9.1-8-job.jar org.apache.mahout.classifier.df.mapreduce.BuildForest -Dmapred.max.split.size=1874231 -d wasbs:///example/data/KDDTrain+.arff -ds wasbs:///example/data/KDDTrain+.info -sl 5 -p -t 100 -o nsl-forest

    La salida de esta operación se almacena en el directorio **nsl-forest**, que está ubicado en el almacenamiento del clúster de HDInsight en __wasb://user/&lt;username>/nsl-forest/nsl-forest.seq. &lt;username> es el nombre de usuario utilizado para la sesión de Escritorio remoto. Este archivo no es legible para el ojo humano.
5. Para probar el bosque, clasifique el conjunto de datos **KDDTest+.arff** . Use el comando siguiente:

        hadoop jar c:/apps/dist/mahout-0.9.0.2.2.9.1-8/examples/target/mahout-examples-0.9.0.2.2.9.1-8-job.jar org.apache.mahout.classifier.df.mapreduce.TestForest -i wasbs:///example/data/KDDTest+.arff -ds wasbs:///example/data/KDDTrain+.info -m nsl-forest -a -mr -o wasbs:///example/data/predictions

    Este comando devuelve información resumida sobre el proceso de clasificación parecida a la siguiente.

        14/07/02 14:29:28 INFO mapreduce.TestForest:

        =======================================================
        Summary
        -------------------------------------------------------
        Correctly Classified Instances          :      17560       77.8921%
        Incorrectly Classified Instances        :       4984       22.1079%
        Total Classified Instances              :      22544

        =======================================================
        Confusion Matrix
        -------------------------------------------------------
        a       b       <--Classified as
        9437    274      |  9711        a     = normal
        4710    8123     |  12833       b     = anomaly

        =======================================================
        Statistics
        -------------------------------------------------------
        Kappa                                       0.5728
        Accuracy                                   77.8921%
        Reliability                                53.4921%
        Reliability (standard deviation)            0.4933

   Este trabajo produce también un archivo ubicado en **wasb:///example/data/predictions/KDDTest+.arff.out**. Sin embargo, este archivo no es legible por el ojo humano.

> [!NOTE]
> Los trabajos de Mahout no sobrescriben archivos. Si desea ejecutar estos trabajos de nuevo, debe eliminar los archivos creados por trabajos anteriores.

## <a name="a-nametroubleshootingatroubleshooting"></a><a name="troubleshooting"></a>Solución de problemas
### <a name="a-nameinstallainstall-mahout"></a><a name="install"></a>Instalación de Mahout
Mahout se instala en clústeres de HDInsight 3.1, y se puede instalar manualmente en clústeres de HDInsight 3.0 o HDInsight 2.1 siguiendo estos pasos.

1. La versión de Mahout que utilice depende de la versión de HDInsight del clúster. Puede encontrar la versión de clúster en las propiedades del clúster en el portal de Azure.

   * **Para HDInsight 2.1**, puede descargar un archivo de almacenamiento Java (JAR) que contiene [Mahout 0.9](http://repo2.maven.org/maven2/org/apache/mahout/mahout-core/0.9/mahout-core-0.9-job.jar).
   * **Para HDInsight 3.0**, debe [compilar Mahout desde el origen][build] y especificar la versión de Hadoop proporcionada por HDInsight. Instale los requisitos previos que aparecen en la página de compilación, descargue el código fuente y use luego el siguiente comando para crear los archivos jar de Mahout:

            mvn -Dhadoop2.version=2.2.0 -DskipTests clean package

        Una vez finalizada la compilación, se creará el archivo JAR en **mahout\mrlegacy\target\mahout-mrlegacy-1.0-SNAPSHOT-job.jar**.

     > [!NOTE]
     > Cuando Mahout 1.0 se publique, debería poder usar los paquetes precompilados con HDInsight 3.0.

2. Cargue el archivo jar en **example/jars** en el almacenamiento predeterminado del clúster. Reemplace CLUSTERNAME en el siguiente script por el nombre de su clúster de HDInsight y FILENAME por la ruta de acceso al archivo **mahout-coure-0.9-job.jar** .

        #Get the cluster info so we can get the resource group, storage, etc.
        $clusterName = "CLUSTERNAME"
        $fileToUpload = "FILENAME"
        $clusterInfo = Get-AzureRmHDInsightCluster -ClusterName $clusterName
        $resourceGroup = $clusterInfo.ResourceGroup
        $storageAccountName=$clusterInfo.DefaultStorageAccount.split('.')[0]
        $container=$clusterInfo.DefaultStorageContainer
        $storageAccountKey=(Get-AzureRmStorageAccountKey `
            -Name $storageAccountName `
        -ResourceGroupName $resourceGroup)[0].Value

        #Create a storage content and upload the file
        $context = New-AzureStorageContext `
            -StorageAccountName $storageAccountName `
            -StorageAccountKey $storageAccountKey

        Set-AzureStorageBlobContent `
            -File $fileToUpload `
            -Blob "example/jars/mahout-core-0.9-job.jar" `
            -Container $container `
            -Context $context

### <a name="cannot-overwrite-files"></a>No se pueden sobrescribir los archivos
Los trabajos de Mahout no limpian los archivos temporales creados durante el procesamiento. Además, los trabajos no sobrescriben los archivos de salida existentes.

Para evitar errores al ejecutar trabajos de Mahout, elimine los archivos temporales y de salida entre una ejecución y otra, o use nombres de directorio temporal y de salida exclusivos.

### <a name="cannot-find-the-jar-file"></a>No se encuentra el archivo JAR
Los clústeres de HDInsight 3.1 incluyen Mahout. La ruta y el nombre de archivo incluyen el número de la versión de Mahout instalada en el clúster. El script de ejemplo de Windows PowerShell de este tutorial usa una ruta que es válida a partir de noviembre de 2015, pero el número de la versión cambiará en futuras actualizaciones de HDInsight. Para determinar la ruta actual al archivo JAR de Mahout de su clúster, use el siguiente comando de Windows PowerShell y luego modifique el script para que haga referencia a la ruta de archivo devuelta:

```powershell
Use-AzureRmHDInsightCluster -ClusterName $clusterName
#Get the cluster info so we can get the resource group, storage, etc.
    $clusterInfo = Get-AzureRmHDInsightCluster -ClusterName $clusterName
    $resourceGroup = $clusterInfo.ResourceGroup
    $storageAccountName=$clusterInfo.DefaultStorageAccount.split('.')[0]
    $container=$clusterInfo.DefaultStorageContainer
    $storageAccountKey=(Get-AzureRmStorageAccountKey `
        -Name $storageAccountName `
    -ResourceGroupName $resourceGroup)[0].Value
Invoke-AzureRmHDInsightHiveJob `
        -StatusFolder "wasbs:///example/statusout" `
        -DefaultContainer $container `
        -DefaultStorageAccountName $storageAccountName `
        -DefaultStorageAccountKey $storageAccountKey `
        -Query '!${env:COMSPEC} /c dir /b /s ${env:MAHOUT_HOME}\examples\target\*-job.jar'
```

### <a name="a-namenopowershellaclasses-that-do-not-work-with-windows-powershell"></a><a name="nopowershell"></a>Clases que no funcionan con Windows PowerShell
Los trabajos de Mahout que usan las siguientes clases devuelven diversos mensajes de error si se usan desde PowerShell.

* org.apache.mahout.utils.clustering.ClusterDumper
* org.apache.mahout.utils.SequenceFileDumper
* org.apache.mahout.utils.vectors.lucene.Driver
* org.apache.mahout.utils.vectors.arff.Driver
* org.apache.mahout.text.WikipediaToSequenceFile
* org.apache.mahout.clustering.streaming.tools.ResplitSequenceFiles
* org.apache.mahout.clustering.streaming.tools.ClusterQualitySummarizer
* org.apache.mahout.classifier.sgd.TrainLogistic
* org.apache.mahout.classifier.sgd.RunLogistic
* org.apache.mahout.classifier.sgd.TrainAdaptiveLogistic
* org.apache.mahout.classifier.sgd.ValidateAdaptiveLogistic
* org.apache.mahout.classifier.sgd.RunAdaptiveLogistic
* org.apache.mahout.classifier.sequencelearning.hmm.BaumWelchTrainer
* org.apache.mahout.classifier.sequencelearning.hmm.ViterbiEvaluator
* org.apache.mahout.classifier.sequencelearning.hmm.RandomSequenceGenerator
* org.apache.mahout.classifier.df.tools.Describe

Para ejecutar los trabajos que usan estas clases, conéctese al clúster de HDInsight y utilice la línea de comandos de Hadoop. Consulte [Clasificación de datos mediante la línea de comandos de Hadoop](#classify) para ver un ejemplo.

## <a name="next-steps"></a>Pasos siguientes
Ahora que ha aprendido a usar a Mahout, descubra otras formas de trabajar con datos en HDInsight:

* [Hive con HDInsight](hdinsight-use-hive.md)
* [Pig con HDInsight](hdinsight-use-pig.md)
* [MapReduce con HDInsight](hdinsight-use-mapreduce.md)

[build]: http://mahout.apache.org/developers/buildingmahout.html
[aps]: /powershell/azureps-cmdlets-docs
[movielens]: http://grouplens.org/datasets/movielens/
[100k]: http://files.grouplens.org/datasets/movielens/ml-100k.zip
[getstarted]: hdinsight-hadoop-linux-tutorial-get-started.md
[upload]: hdinsight-upload-data.md
[ml]: http://en.wikipedia.org/wiki/Machine_learning
[forest]: http://en.wikipedia.org/wiki/Random_forest
[management]: https://manage.windowsazure.com/
[enableremote]: ./media/hdinsight-mahout/enableremote.png
[connect]: ./media/hdinsight-mahout/connect.png
[hadoopcli]: ./media/hdinsight-mahout/hadoopcli.png
[tools]: https://github.com/Blackmist/hdinsight-tools



<!--HONumber=Dec16_HO2-->


