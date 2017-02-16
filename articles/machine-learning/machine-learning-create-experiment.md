---
title: Un experimento sencillo en Machine Learning Studio | Microsoft Docs
description: "Este tutorial de aprendizaje automático le guiará a través de un sencillo experimento de ciencia de datos. Podremos predecir el precio de un automóvil mediante un algoritmo de regresión."
keywords: "experimento,regresión lineal,algoritmos de aprendizaje automático,tutorial de aprendizaje automático,técnicas de modelado predictivo,experimento de ciencia de datos"
services: machine-learning
documentationcenter: 
author: garyericson
manager: jhubbard
editor: cgronlun
ms.assetid: b6176bb2-3bb6-4ebf-84d1-3598ee6e01c6
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 11/21/2016
ms.author: garye
translationtype: Human Translation
ms.sourcegitcommit: dcda8b30adde930ab373a087d6955b900365c4cc
ms.openlocfilehash: dd3e982ba908e542ce0b536699e37e2bc95e6706


---

# <a name="machine-learning-tutorial-create-your-first-data-science-experiment-in-azure-machine-learning-studio"></a>Tutorial de aprendizaje automático: creación del primer experimento de ciencia de datos en Estudio de aprendizaje automático de Azure

Si nunca ha usado **Azure Machine Learning Studio** antes, este tutorial le ayudará.

Le mostraremos cómo usar Studio por primera vez para crear un experimento de aprendizaje automático. En dicho experimento probaremos un modelo analítico que predice el precio de un automóvil en función de diferentes variables, como la marca y las especificaciones técnicas.

> [!NOTE]
> En este tutorial se muestran los conceptos básicos de cómo arrastrar y colocar módulos en el experimento, conectarlos, ejecutar el experimento y examinar los resultados. No vamos a explicar el tema general de aprendizaje automático o cómo seleccionar y usar los más de 100 algoritmos integrados y módulos de manipulación de datos incluidos en Studio.
>
>Si no está familiarizado con el aprendizaje automático, la serie de vídeos [Ciencia de datos para principiantes](machine-learning-data-science-for-beginners-the-5-questions-data-science-answers.md) podría ser un buen lugar para comenzar. Esta serie de vídeos es una excelente introducción al aprendizaje automático mediante el uso de conceptos y lenguaje cotidianos.
>
>Si conoce el aprendizaje automático pero busca información más general sobre Machine Learning Studio y los algoritmos de aprendizaje automático que contiene, aquí se indican unos buenos recursos:
>
- [¿Qué es Estudio de aprendizaje automático?](machine-learning-what-is-ml-studio.md) - Esta es una descripción de alto nivel de Studio.
- [Conceptos básicos de aprendizaje automático con ejemplos de algoritmos](machine-learning-basics-infographic-with-algorithm-examples.md): esta infografía es útil si quiere aprender más sobre los diferentes tipos de algoritmos de aprendizaje automático que se incluyen con Machine Learning Studio.
- [Guía de aprendizaje automático](https://gallery.cortanaintelligence.com/Tutorial/Machine-Learning-Guide-1): esta guía incluye información similar a la infografía mencionada anteriormente, pero en formato interactivo.
- [Hoja de referencia rápida de algoritmos de aprendizaje automático ](machine-learning-algorithm-cheat-sheet.md) y [Cómo elegir algoritmos para Microsoft Azure Machine Learning](machine-learning-algorithm-choice.md): este póster descargable y el artículo que le acompaña tratan en profundidad los algoritmos de Studio.
- [Machine Learning Studio: Algorithm and Module Help](https://msdn.microsoft.com/library/azure/dn905974.aspx) (Machine Learning Studio: ayuda de algoritmos y módulos): esta es la referencia completa para todos los módulos de Studio, incluidos los algoritmos de aprendizaje automático.

<!-- -->

[!INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

## <a name="how-does-machine-learning-studio-help"></a>¿Cómo funciona la ayuda de Estudio de aprendizaje automático?

Estudio de aprendizaje automático facilita la configuración de un experimento con módulos de arrastrar y colocar programados previamente con técnicas de modelado predictivo.

Mediante un área de trabajo visual e interactiva, se arrastran y colocan ***conjuntos de datos*** y ***módulos*** de análisis en un lienzo interactivo. Luego se conectan para formar un ***experimento*** que se ejecuta en Machine Learning Studio.
Se ***crea un modelo***, ***se entrena***, se ***puntúa y se prueba***.

Puede iterar en el modelo de diseño y editar el experimento y ejecutarlo hasta que le proporcione los resultados que busca. Cuando el modelo está listo, puede publicarlo como un ***servicio web*** para que otros puedan enviarle nuevos datos y obtener a cambio predicciones.

## <a name="open-machine-learning-studio"></a>Apertura de Machine Learning Studio

Para empezar a trabajar con Studio, vaya a [https://studio.azureml.net](https://studio.azureml.net). Si ya se ha registrado en Machine Learning Studio antes, haga clic en **Sign In** (Iniciar sesión). De lo contrario, haga clic en **Sign up here** (Regístrese aquí) y elija entre opciones gratuitas y de pago.

![Iniciar sesión en Machine Learning Studio][sign-in-to-studio]
<br/>
***Iniciar sesión en Machine Learning Studio***

## <a name="five-steps-to-create-an-experiment"></a>Cinco pasos para crear un experimento

En este tutorial de aprendizaje automático se enumeran los cinco pasos básicos que puede llevar a cabo para crear un experimento en Machine Learning Studio para crear, entrenar y puntuar el modelo:

- **Crear un modelo**
    - [Paso 1: Obtener los datos]
    - [Paso 2: Preparar los datos]
    - [Paso 3: Definir las características]
- **Entrenar el modelo**
    - [Paso 4: Elegir y aplicar un algoritmo de aprendizaje]
- **Puntuar y probar el modelo**
    - [Paso 5: Predecir los precios de los automóviles nuevos]

[Paso 1: Obtener los datos]: #step-1-get-data
[Paso 2: Preparar los datos]: #step-2-prepare-the-data
[Paso 3: Definir las características]: #step-3-define-features
[Paso 4: Elegir y aplicar un algoritmo de aprendizaje]: #step-4-choose-and-apply-a-learning-algorithm
[Paso 5: Predecir los precios de los automóviles nuevos]: #step-5-predict-new-automobile-prices

> [!TIP] 
> Puede encontrar una copia de trabajo del experimento siguiente en la [galería de Cortana Intelligence](https://gallery.cortanaintelligence.com). Vaya a **[Your first data science experiment - Automobile price prediction](https://gallery.cortanaintelligence.com/Experiment/Your-first-data-science-experiment-Automobile-price-prediction-1)** (Su primer experimento de ciencia de los datos: predicción de los precios de automóviles) y haga clic en **Abrir en Studio** para descargar una copia del experimento en su área de trabajo de Machine Learning Studio.


## <a name="step-1-get-data"></a>Paso 1: Obtener los datos

Lo primero que necesita para ejecutar aprendizaje automático son datos.
Machine Learning Studio incluye varios conjuntos de datos que puede usar; otra opción es importarlos de diversos orígenes. En este ejemplo, usaremos el conjunto de datos de ejemplo, **Automobile price data (Raw)**, que se incluye en el área de trabajo.
Este conjunto de datos incluye entradas para diversos automóviles individuales, por ejemplo, información sobre la marca, el modelo, las especificaciones técnicas y el precio.

Aquí se muestra cómo obtener el conjunto de datos en el experimento.

1. Cree un experimento nuevo; para ello, haga clic en **+NUEVO** en la parte inferior de la ventana de Machine Learning Studio, seleccione **EXPERIMENTO** y luego **Experimento en blanco**.

2. El experimento recibe un nombre predeterminado que puede ver en la parte superior del lienzo. Seleccione este texto y cambie su nombre por algo significativo, por ejemplo, **predicción de precios de automóviles**.

2. A la izquierda del lienzo de experimentos, hay una paleta de conjuntos de datos y módulos. Escriba **automobile** en el cuadro de búsqueda de la parte superior de esta paleta para encontrar el conjunto de datos llamado **Automobile price data (Raw)** (Datos de precios de automóviles [sin formato]). Arrastre este conjunto de datos al lienzo del experimento.

    ![Buscar el conjunto de datos de automóvil y arrastrarlo hasta el lienzo del experimento][type-automobile]
    <br/>
    ***Buscar el conjunto de datos de automóvil y arrastrarlo hasta el lienzo del experimento***

Para ver la apariencia de estos datos, haga clic en el puerto de salida, en la parte inferior del conjunto de datos de automóvil, y seleccione **Visualizar**.

![Hacer clic en el puerto de salida y seleccionar "Visualizar"][select-visualize]
<br/>
***Hacer clic en el puerto de salida y seleccionar "Visualizar"***

> [!TIP]
> Los conjuntos de datos y los módulos tienen puertos de entrada y de salida representados por pequeños círculos: los puertos de entrada arriba y los puertos de salida abajo.
Para crear un flujo de datos por su experimento, deberá conectar estos puertos.
En cualquier momento, puede hacer clic en el puerto de salida de un conjunto de datos o de un módulo para ver el aspecto de los datos en ese punto del flujo de datos.

En este conjunto de datos de ejemplo, cada instancia de un automóvil aparece como una fila y las variables asociadas a cada automóvil como columnas. Dadas las variables para un automóvil determinado, vamos a intentar predecir el precio de la columna del extremo derecho (columna 26, llamada "precio").

![Ver los datos de automóviles en la ventana de visualización de datos][visualize-auto-data]
<br/>
***Ver los datos de automóviles en la ventana de visualización de datos***

Cierre la ventana de visualización haciendo clic en la "**x**" en la esquina superior derecha.

## <a name="step-2-prepare-the-data"></a>Paso 2: Preparar los datos

Normalmente, un conjunto de datos requiere algún procesamiento previo antes de que se pueda analizar. Por ejemplo, puede que haya observado los valores que faltan en las columnas de varias filas. Estos valores que faltan se deben limpiar para que el modelo pueda analizar los datos de manera adecuada. En nuestro caso, quitaremos todas las filas que tengan valores ausentes. Además, la columna **normalized-losses** tiene una gran proporción de valores que faltan, por lo que excluiremos esa columna del modelo por completo.

> [!TIP]
> Limpiar los valores que faltan de los datos de entrada es un requisito previo para usar la mayoría de los módulos.

Primero agregaremos un módulo que quite completamente la columna **normalized-losses** y luego agregaremos otro módulo que quite cualquier fila a la que le falten datos.

1. Escriba **seleccionar columnas** en el cuadro de búsqueda de la parte superior de la paleta de módulos, y busque [Select Columns in Dataset] (Seleccionar columnas en el conjunto de datos) [select-columns]; después, arrastre este módulo al lienzo del experimento. Este módulo nos permite seleccionar las columnas de datos que queremos incluir o excluir del modelo.

2. Conecte el puerto de salida del conjunto de datos **Automobile price data (Raw)** al puerto de entrada del módulo [Select Columns in Dataset] (Seleccionar columnas en el conjunto de datos) [select-columns].

    ![Agregar el módulo "Seleccionar columnas en el conjunto de datos" al lienzo del experimento y conectarlo][type-select-columns]
    <br/>
    ***Agregar el módulo "Seleccionar columnas en el conjunto de datos" al lienzo del experimento y conectarlo***

3. Haga clic en el módulo [Select Columns in Dataset] (Seleccionar columnas en el conjunto de datos) [select-columns] y haga clic en **Launch column selector** (Iniciar el selector de columnas) en el panel **Propiedades**.

    - A la izquierda, haga clic en **Con reglas**
    - En **Empiezan por**, haga clic en **Todas las columnas**. Esto indica a [Select Columns in Dataset] (Seleccionar columnas en el conjunto de datos) [select-columns] que pase por todas las columnas (excepto las que se van a excluir).
    - En los menús desplegables, seleccione **Excluir** y **nombres de columna** y luego haga clic en el cuadro de texto. A continuación, se mostrará una lista de columnas. Seleccione **normalized-losses**; se agregará al cuadro de texto.
    - Haga clic en el botón Aceptar con la marca de verificación para cerrar el selector de columnas (en la esquina inferior derecha).

    ![Iniciar el selector de columnas y excluir la columna "normalized-losses"][launch-column-selector]
    <br/>
    ***Iniciar el selector de columnas y excluir la columna "normalized-losses"***

    Ahora el panel de propiedades de **Select Columns in Dataset** (Seleccionar columnas en el conjunto de datos) indica que se pasará por todas las columnas del conjunto de datos excepto **normalized-losses**.

    ![El panel de propiedades muestra que la columna "normalized-losses" se ha excluido][showing-excluded-column]
    <br/>
    ***El panel de propiedades muestra que la columna "normalized-losses" se ha excluido***

    > [!TIP]
    > Puede agregar un comentario a un módulo; para ello, haga doble clic en el módulo y escriba texto. Esto puede ayudarle a ver de un vistazo lo que el módulo hace en el experimento. En este caso, haga doble clic en el módulo [Select Columns in Dataset] (Seleccionar columnas en el conjunto de datos) [select-columnsselect-columns] y escriba el comentario "Excluir normalized-losses".

    ![Hacer doble clic en un módulo para agregar un comentario][add-comment]
    <br/>
    ***Hacer doble clic en un módulo para agregar un comentario***

3. Arrastre el módulo [Clean Missing Data][clean-missing-data] (Limpiar los datos que faltan) al lienzo del experimento y conéctelo con el módulo [Select Columns in Dataset][select-columns] (Seleccionar columnas en el conjunto de datos). En el panel **Propiedades**, seleccione **Remove entire row** (Quitar la fila entera) en **Cleaning mode** (Modo de limpieza). Esto indica al módulo [Clean Missing Data] (Limpiar los datos que faltan) [clean-missing-data] que quite las filas que tienen valores que faltan para limpiar los datos. Haga doble clic en el módulo y escriba el comentario "Quitar las filas sin valor".

    ![Establecer el modo de limpieza en "Quitar la fila entera" para el módulo "Limpiar los datos que faltan"][set-remove-entire-row]
    <br/>
    ***Establecer el modo de limpieza en "Quitar la fila entera" para el módulo "Limpiar los datos que faltan"***

4. Ejecute el experimento haciendo clic en **EJECUTAR** en el lienzo de experimentos.

    Cuando el experimento finalice, todos los módulos tendrán una marca de verificación verde para indicar que se han completado correctamente. Observe también el estado **Ejecución finalizada** en la esquina superior derecha.

![Después de ejecutarlo, el experimento debe tener un aspecto similar al siguiente][early-experiment-run]
<br/>
***Después de ejecutarlo, el experimento debe tener un aspecto similar al siguiente***

> [!TIP]
> ¿Por qué ejecutamos el experimento ahora? Al ejecutar el experimento, las definiciones de columna de nuestros datos pasan desde el conjunto de datos hasta los módulos [Select Columns in Dataset] (Seleccionar columnas en el conjunto de datos) [select-columns] y [Clean Missing Data] (Limpiar los datos que faltan) [clean-missing-data]. Esto significa que los módulos que conectamos a [Clean Missing Data] (Limpiar los datos que faltan) [clean-missing-data] tendrán también esta misma información.

Todo lo que hemos hecho en el experimento hasta el momento es limpiar los datos. Si quiere ver el conjunto de datos limpio, haga clic en el puerto de salida izquierdo del módulo [Clean Missing Data] (Limpiar los datos que faltan) [clean-missing-data] y seleccione **Visualizar**. Observe que la columna **normalized-losses** ya no se incluye y que no hay valores que faltan.

Ahora los datos están limpios y ya puede especificar qué características se van a usar en el modelo predictivo.

## <a name="step-3-define-features"></a>Paso 3: Definir las características

En el aprendizaje automático, las *características* son propiedades mensurables individuales de algo que le interesa. En nuestro conjunto de datos, cada fila representa un automóvil y cada columna es una característica de ese automóvil.

Encontrar un buen conjunto de funciones para la creación de un modelo de predicción requiere experimentación y conocimientos acerca del problema que desea resolver. Algunas características son mejores para predecir el destino que otras. Además, algunas características tienen una correlación fuerte con otras y se pueden quitar. Por ejemplo, city-mpg y highway-mpg están estrechamente relacionadas así que podemos mantener una y quitar la otra sin que tenga un impacto importante sobre la predicción.

Creemos un modelo que use un subconjunto de las funciones de nuestro conjunto de datos. Puede volver más tarde y seleccionar diferentes características, ejecutar de nuevo el experimento y ver si obtiene mejores resultados. Pero, para empezar, vamos a intentar las siguientes características:

    make, body-style, wheel-base, engine-size, horsepower, peak-rpm, highway-mpg, price


1. Arrastre otro módulo [Select Columns in Dataset] (Seleccionar columnas en el conjunto de datos) [select-columns] al lienzo del experimento. Conecte el puerto de salida izquierdo del módulo [Clean Missing Data] (Limpiar los datos que faltan) [clean-missing-data] a la entrada del módulo [Select Columns in Dataset] (Seleccionar columnas en el conjunto de datos) [select-columns].

    ![Conectar el módulo "Seleccionar columnas en el conjunto de datos" al módulo "Limpiar los datos que faltan"][connect-clean-to-select]
    <br/>
    ***Conectar el módulo "Seleccionar columnas en el conjunto de datos" al módulo "Limpiar los datos que faltan"***

2. Haga doble clic en el módulo y escriba "Seleccionar funciones para la predicción".

2. Haga clic en **Iniciar el selector de columnas** en el panel **Propiedades**.

3. Haga clic en **With rules**(Con reglas).

4. En **Empezar por**, haga clic en **No columns** (Ninguna columna). En la fila del filtro, seleccione **Incluir** y **nombres de columna** y seleccione nuestra lista de nombres de columna en el cuadro de texto. Esto indica al módulo que no pase por ninguna columna (características), a excepción de las especificadas.

5. Haga clic en el botón de marca de verificación (Aceptar).

    ![Seleccionar las columnas (características) que se incluirán en la predicción][select-columns-to-include]
    <br/>
    ***Seleccionar las columnas (características) que se incluirán en la predicción***

Esto genera un conjunto de datos filtrado que contiene solo las características que queremos pasar al algoritmo de aprendizaje que usaremos en el siguiente paso. Posteriormente puede volver e intentarlo de nuevo con una selección diferente de características.

## <a name="step-4-choose-and-apply-a-learning-algorithm"></a>Paso 4: Elegir y aplicar un algoritmo de aprendizaje

Ahora que los datos están listos, la construcción de un modelo predictivo consiste en entrenar y probar. Usaremos nuestros datos para entrenar el modelo y luego probarlo para ver lo que se aproxima en sus predicciones de los precios.
<!-- For now, don't worry about *why* we need to train and then test a model.-->

*Clasificación* y *regresión* son dos tipos de algoritmos de aprendizaje automático supervisado. La clasificación permite predecir una respuesta a partir de un conjunto definido de categorías, como el color (rojo, azul o verde). La regresión se usa para predecir un número.

Como queremos predecir un precio, que es un número, usaremos un modelo de regresión. En este ejemplo, vamos a usar un sencillo modelo de *regresión lineal*.

> [!TIP]
> Para más información sobre los diferentes tipos de algoritmos de aprendizaje automático y cuándo usarlos, puede ver el primer vídeo de la serie Ciencia de datos para principiantes, [Las cinco preguntas a las que responde la ciencia de datos](machine-learning-data-science-for-beginners-the-5-questions-data-science-answers.md). También puede examinar la infografía [Conceptos básicos de aprendizaje automático con ejemplos de algoritmos](machine-learning-basics-infographic-with-algorithm-examples.md) o consultar la [Hoja de referencia rápida de algoritmos de aprendizaje automático](machine-learning-algorithm-cheat-sheet.md).

Para entrenar el modelo le proporcionamos un conjunto de datos que incluye el precio. El modelo analiza los datos y busca correlaciones entre las características de un automóvil y su precio. A continuación, probaremos el modelo; le daremos un conjunto de características de automóviles con los que estamos familiarizados y veremos si el modelo se acerca en la predicción del precio conocido.

Usaremos nuestros datos para entrenar el modelo y probarlo, para lo cual los dividiremos en conjuntos de datos distintos, de entrenamiento y de prueba.

1. Seleccione y arrastre el módulo [Split Data][split] al lienzo del experimento y conéctelo al último módulo [Select Columns in Dataset] (Seleccionar columnas en el conjunto de datos) [select-columns].

2. Haga clic en el módulo [Split Data][split] (Dividir datos) para seleccionarlo. Busque **Fraction of rows in the first output dataset** (Fracción de filas del primer conjunto de datos de salida) (en el panel **Propiedades** a la derecha de lienzo) y establézcalo en 0,75. De esta manera, usaremos el 75 por ciento de los datos para entrenar el modelo y mantendremos el 25 por ciento para prueba (más adelante, puede experimentar con el uso de diferentes porcentajes).

    ![Establecer la fracción de división del módulo "Dividir datos" en 0,75][set-split-data-percentage]
    <br/>
    ***Establecer la fracción de división del módulo "Dividir datos" en 0,75***

    > [!TIP]
    > Al cambiar el parámetro **Valor de inicialización aleatorio** , puede producir muestras aleatorias diferentes para entrenamiento y prueba. Este parámetro controla la inicialización del generador de números pseudoaleatorios.

2. Ejecute el experimento. Cuando se ejecuta el experimento, los módulos [Select Columns in Dataset] (Seleccionar columnas en el conjunto de datos) [select-columns] y [Split Data] (Dividir datos) [split] pasan las definiciones de columna a los módulos que se agregarán a continuación.  

3. Para seleccionar el algoritmo de aprendizaje, expanda la categoría **Aprendizaje automático** en la paleta de módulos situada a la izquierda del lienzo y luego expanda **Inicializar modelo**. Se muestran varias categorías de módulos que se pueden usar para inicializar algoritmos de Aprendizaje automático. Para este experimento, seleccione el módulo [Regresión lineal][linear-regression] en la categoría **Regresión** y arrástrelo al lienzo del experimento.
(Otra forma de encontrar el módulo es escribir "regresión lineal" en el cuadro Buscar de la paleta.)

4. Busque y arrastre el módulo [Train Model][train-model] (Entrenar modelo) al lienzo del experimento. Conecte la salida del módulo [Linear Regression][linear-regression] (Regresión lineal) a la entrada izquierda del módulo [Train Model][train-model] (Entrenar modelo), y conecte la salida de datos de entrenamiento (puerto izquierdo) del módulo [Split Data][split] (Dividir datos) a la entrada derecha del módulo [Train Model][train-model].

    ![Conectar el módulo "Entrenar modelo" a los módulos "Regresión lineal" y "Dividir datos"][connect-train-model]
    <br/>
    ***Conectar el módulo "Entrenar modelo" a los módulos "Regresión lineal" y "Dividir datos"***

5. Haga clic en el módulo [Train Model][train-model] (Entrenar modelo), haga clic en **Launch column selector** (Iniciar el selector de columnas) en el panel **Propiedades** y, luego, seleccione la columna **price** (precio). Este es el valor que nuestro modelo va a predecir.

    Para seleccionar la columna **precio** en el selector de columnas, muévala de la lista **Columnas disponibles** a la lista **Columnas seleccionadas**.

    ![Seleccionar la columna precio del módulo "Entrenar modelo"][select-price-column]
    <br/>
    ***Seleccionar la columna precio del módulo "Entrenar modelo"***

6. Ejecute el experimento.

Ahora tenemos un modelo de regresión entrenado que puede usarse para puntuar nuevos datos de automóviles para realizar predicciones de precios.

![Tras la ejecución, el experimento debe tener este aspecto][second-experiment-run]
<br/>
***Tras la ejecución, el experimento debe tener este aspecto***

## <a name="step-5-predict-new-automobile-prices"></a>Paso 5: Predecir los precios de los automóviles nuevos

Ahora que hemos entrenado el modelo usando el 75 % de nuestros datos, podemos usarlo para puntuar el otro 25 % de los datos y ver cómo funciona el modelo.

1. Busque y arrastre el módulo [Score Model] (Puntuar modelo) [score-model] al lienzo del experimento. Conecte la salida del módulo [Train Model][train-model] (Entrenar modelo) al puerto de entrada izquierdo de [Score Model] (Puntuar modelo) [score-model]. Conecte la salida de datos de prueba (puerto derecho) del módulo [Split Data][split] (Dividir datos) al puerto de entrada derecho de [Score Model][score-model] (Puntuar modelo).

    ![Conectar el módulo "Puntuar modelo" a los módulos "Entrenar modelo" y "Dividir datos"][connect-score-model]
    <br/>
    ***Conectar el módulo "Puntuar modelo" a los módulos "Entrenar modelo" y "Dividir datos"***

2. Ejecute el experimento y vea la salida desde el módulo [Score Model][score-model] (Puntuar modelo); haga clic en el puerto de salida de [Score Model][score-model] (Puntuar modelo) y seleccione **Visualizar**. La salida muestra los valores previstos para el precio y los valores conocidos de los datos de prueba.  

    ![Salida del módulo "Puntuar modelo"][score-model-output]
    <br/>
    ***Salida del módulo "Puntuar modelo"***

3. Por último, probamos la calidad de los resultados. Seleccione y arrastre el módulo [Evaluate Model][evaluate-model] (Evaluar modelo) al lienzo del experimento y conecte la salida del módulo [Score Model][score-model] (Puntuar modelo) a la entrada izquierda del módulo [Evaluate Model][evaluate-model] (Evaluar modelo).

    > [!TIP]
    > Hay dos puertos de entrada en el módulo [Evaluate Model][evaluate-model] (Evaluar modelo) porque puede usarse para comparar dos modelos en paralelo. Más adelante, puede agregar otro algoritmo al experimento y usar [Evaluate Model][evaluate-model] (Evaluar modelo) para ver cuál ofrece mejores resultados.

4. Ejecute el experimento.

Para ver la salida del módulo [Evaluate Model][evaluate-model] (Evaluar modelo), haga clic en el puerto de salida y seleccione **Visualize** (Visualizar).

![Resultados de evaluación del experimento][evaluation-results]
<br/>
***Resultados de evaluación del experimento***

Se muestran las siguientes estadísticas para nuestro modelo:

- **Desviación media** (MAE): la media de errores absolutos (un *error* es la diferencia entre el valor de predicción y el valor real).
- **Raíz cuadrada de errores** (RMSE): la raíz cuadrada de la media de errores al cuadrado de las predicciones realizadas sobre el conjunto de datos de prueba.
- **Error absoluto relativo**: la media de errores absolutos en relación con la diferencia absoluta entre los valores reales y la media de todos los valores reales.
- **Error al cuadrado relativo**: la media de errores al cuadrado en relación con la diferencia al cuadrado entre los valores reales y la media de todos los valores reales.
- **Coeficiente de determinación**: conocido también como **valor R cuadrado**, es una métrica estadística que indica cómo de bien se ajusta un modelo a los datos.

Para cada una de las estadísticas de errores, cuanto menor sea el valor, mejor. Un valor inferior indica que las predicciones se adaptan más estrechamente a los valores reales. En **Coeficiente de determinación**, cuanto más cerca está el valor de uno (1,0), mejores son las predicciones.

## <a name="final-experiment"></a>Experimento final

El experimento final debe tener un aspecto similar a este:

![El experimento final][complete-linear-regression-experiment]
<br/>
***El experimento final***

## <a name="next-steps"></a>Pasos siguientes

Ahora que ha completado el primer tutorial de aprendizaje automático y tiene su experimento configurado, puede continuar mejorando el modelo y luego implementarlo como un servicio web predictivo.

- **Iterar para intentar mejorar el modelo**; por ejemplo, puede cambiar las características que usará en su predicción. O bien, puede modificar las propiedades del algoritmo [Linear Regression][linear-regression] (Regresión lineal) o probar un algoritmo completamente diferente. Incluso puede agregar varios algoritmos de aprendizaje automático a la vez al experimento y comparar dos de ellos mediante el módulo [Evaluate Model][evaluate-model] (Evaluar modelo).
Para ver un ejemplo de cómo comparar varios modelos en un único experimento, consulte [Compare Regressors](https://gallery.cortanaintelligence.com/Experiment/Compare-Regressors-5) (Comparar regresores) en la [galería de Cortana Intelligence](https://gallery.cortanaintelligence.com).

    > [!TIP]
    > Para copiar cualquier iteración del experimento, use el botón **GUARDAR COMO** situado bajo el lienzo del experimento. Puede ver todas las iteraciones del experimento haciendo clic en **VER HISTORIAL DE EJECUCIÓN** en el lienzo. Para más información, consulte [Administración de iteraciones de experimentos en Azure Machine Learning Studio][runhistory].

[runhistory]: machine-learning-manage-experiment-iterations.md

- **Implementar el modelo como un servicio web predictivo**: si está satisfecho con su modelo, puede implementarlo como un servicio web a fin de usarlo para predecir precios de automóviles usando nuevos datos. Para más información, consulte [Implementar un servicio web Azure Machine Learning][publish].

[publish]: machine-learning-publish-a-machine-learning-web-service.md

¿Quiere saber más? Para ver un tutorial más amplio y detallado del proceso de crear, entrenar, puntuar e implementar un modelo, consulte [Desarrollar una solución predictiva mediante Azure Machine Learning][walkthrough].

[walkthrough]: machine-learning-walkthrough-develop-predictive-solution.md

<!-- Images -->
[sign-in-to-studio]: ./media/machine-learning-create-experiment/sign-in-to-studio.png
[visualize-auto-data]:./media/machine-learning-create-experiment/visualize-auto-data.png
[select-visualize]: ./media/machine-learning-create-experiment/select-visualize.png
[showing-excluded-column]:./media/machine-learning-create-experiment/showing-excluded-column.png
[set-remove-entire-row]:./media/machine-learning-create-experiment/set-remove-entire-row.png
[early-experiment-run]:./media/machine-learning-create-experiment/early-experiment-run.png
[select-columns-to-include]:./media/machine-learning-create-experiment/select-columns-to-include.png
[second-experiment-run]:./media/machine-learning-create-experiment/second-experiment-run.png
[connect-score-model]:./media/machine-learning-create-experiment/connect-score-model.png
[evaluation-results]:./media/machine-learning-create-experiment/evaluation-results.png
[complete-linear-regression-experiment]:./media/machine-learning-create-experiment/complete-linear-regression-experiment.png

<!-- temporarily switching GIFs to PNGs to remove animation --> 
[type-automobile]:./media/machine-learning-create-experiment/type-automobile.png
[type-select-columns]:./media/machine-learning-create-experiment/type-select-columns.png
[launch-column-selector]:./media/machine-learning-create-experiment/launch-column-selector.png
[add-comment]:./media/machine-learning-create-experiment/add-comment.png
[connect-clean-to-select]:./media/machine-learning-create-experiment/connect-clean-to-select.png

[set-split-data-percentage]:./media/machine-learning-create-experiment/set-split-data-percentage.png

<!-- temporarily switching GIFs to PNGs to remove animation --> 
[connect-train-model]:./media/machine-learning-create-experiment/connect-train-model.png
[select-price-column]:./media/machine-learning-create-experiment/select-price-column.png

[score-model-output]:./media/machine-learning-create-experiment/score-model-output.png

<!-- Module References -->
[evaluate-model]: https://msdn.microsoft.com/library/azure/927d65ac-3b50-4694-9903-20f6c1672089/
[linear-regression]: https://msdn.microsoft.com/library/azure/31960a6f-789b-4cf7-88d6-2e1152c0bd1a/
[clean-missing-data]: https://msdn.microsoft.com/library/azure/d2c5ca2f-7323-41a3-9b7e-da917c99f0c4/
[select-columns]: https://msdn.microsoft.com/library/azure/1ec722fa-b623-4e26-a44e-a50c6d726223/
[score-model]: https://msdn.microsoft.com/library/azure/401b4f92-e724-4d5a-be81-d5b0ff9bdb33/
[split]: https://msdn.microsoft.com/library/azure/70530644-c97a-4ab6-85f7-88bf30a8be5f/
[train-model]: https://msdn.microsoft.com/library/azure/5cc7053e-aa30-450d-96c0-dae4be720977/



<!--HONumber=Dec16_HO2-->


