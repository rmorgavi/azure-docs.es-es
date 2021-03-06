---
title: "Guía del desarrollador: Lenguaje de consulta de IoT Hub| Microsoft Docs"
description: "Guía del desarrollador de Azure IoT Hub: Descripción del lenguaje de consulta de IoT Hub de tipo SQL que se usa para recuperar información sobre los dispositivos gemelos y trabajos desde IoT Hub"
services: iot-hub
documentationcenter: .net
author: fsautomata
manager: timlt
editor: 
ms.assetid: 851a9ed3-b69e-422e-8a5d-1d79f91ddf15
ms.service: iot-hub
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/30/2016
ms.author: elioda
translationtype: Human Translation
ms.sourcegitcommit: 627de0ca1647e98e08165521e7d3a519e1950296
ms.openlocfilehash: 8007c6864368868d9cb489236d958eeada8789bd


---
# <a name="reference---iot-hub-query-language-for-device-twins-and-jobs"></a>Referencia: Lenguaje de consulta de IoT Hub para dispositivos gemelos y trabajos
## <a name="overview"></a>Información general
IoT Hub proporciona un lenguaje eficaz de tipo SQL para recuperar información sobre [dispositivos gemelos][lnk-twins] y [trabajos][lnk-jobs]. Este artículo presenta:

* una introducción a las características principales del lenguaje de consulta de IoT Hub y
* una descripción más detallada del lenguaje.

## <a name="getting-started-with-device-twin-queries"></a>Introducción a las consultas de dispositivos gemelos
Los [dispositivos gemelos][lnk-twins] pueden contener objetos JSON arbitrarios como etiquetas y propiedades. IoT Hub permite consultar los dispositivos gemelos como un solo documento JSON que contiene toda la información de los dispositivos gemelos.
Por ejemplo, supongamos que los dispositivos gemelos de IoT Hub tienen la siguiente estructura:

        {                                                                      
            "deviceId": "myDeviceId",                                            
            "etag": "AAAAAAAAAAc=",                                              
            "tags": {                                                            
                "location": {                                                      
                    "region": "US",                                                  
                    "plant": "Redmond43"                                             
                }                                                                  
            },                                                                   
            "properties": {                                                      
                "desired": {                                                       
                    "telemetryConfig": {                                             
                        "configId": "db00ebf5-eeeb-42be-86a1-458cccb69e57",            
                        "sendFrequencyInSecs": 300                                          
                    },                                                               
                    "$metadata": {                                                   
                    ...                                                     
                    },                                                               
                    "$version": 4                                                    
                },                                                                 
                "reported": {                                                      
                    "connectivity": {                                                
                        "type": "cellular"                            
                    },                                                               
                    "telemetryConfig": {                                             
                        "configId": "db00ebf5-eeeb-42be-86a1-458cccb69e57",            
                        "sendFrequencyInSecs": 300,                                         
                        "status": "Success"                                            
                    },                                                               
                    "$metadata": {                                                   
                    ...                                                
                    },                                                               
                    "$version": 7                                                    
                }                                                                  
            }                                                                    
        }

IoT Hub expone los dispositivos gemelos como una colección de documentos denominada **devices**.
Por lo tanto, la consulta siguiente recupera el conjunto completo de dispositivos gemelos:

        SELECT * FROM devices

> [!NOTE]
> Los [SDK IoT de Azure][lnk-hub-sdks] admiten la paginación de resultados de gran tamaño.
>
>

IoT Hub permite recuperar dispositivos gemelos filtrando por condiciones arbitrarias. Por ejemplo,

        SELECT * FROM devices
        WHERE tags.location.region = 'US'

recupera los dispositivos gemelos con la etiqueta **location.region** establecida en **US**.
También se admiten operadores booleanos y comparaciones aritméticas, por ejemplo,

        SELECT * FROM devices
        WHERE tags.location.region = 'US'
            AND properties.reported.telemetryConfig.sendFrequencyInSecs >= 60

recupera todos los dispositivos gemelos ubicados en Estados Unidos configurados para enviar datos de telemetría con una frecuencia inferior a un minuto. Por comodidad, también es posible usar constantes de matriz junto con los operadores **IN** (En) y **NIN** (No en). Por ejemplo,

        SELECT * FROM devices
        WHERE property.reported.connectivity IN ['wired', 'wifi']

recupera todos los dispositivos gemelos que notificaron conectividad Wi-Fi o con cable. A menudo, es necesario identificar a todos los dispositivos gemelos que contienen una propiedad concreta. IoT Hub admite la función `is_defined()` para esta finalidad. Por ejemplo,

        SELECT * FROM devices
        WHERE is_defined(property.reported.connectivity)

recupera todos los dispositivos gemelos que definen la propiedad notificada `connectivity`. Consulte la sección [Cláusula WHERE][lnk-query-where] para obtener la referencia completa de las funcionalidades de filtrado.

También se admiten la agrupación y las agregaciones. Por ejemplo,

        SELECT properties.reported.telemetryConfig.status AS status,
            COUNT() AS numberOfDevices
        FROM devices
        GROUP BY properties.reported.telemetryConfig.status

devuelve el recuento de los dispositivos en cada estado de configuración de telemetría.

        [
            {
                "numberOfDevices": 3,
                "status": "Success"
            },
            {
                "numberOfDevices": 2,
                "status": "Pending"
            },
            {
                "numberOfDevices": 1,
                "status": "Error"
            }
        ]

En el ejemplo anterior, se demuestra una situación en la que tres dispositivos notificaron una configuración correcta, dos aún están aplicándola y uno notificó un error.

### <a name="c-example"></a>Ejemplo de C#
El [SDK del servicio C#][lnk-hub-sdks] expone la funcionalidad de consulta en la clase **RegistryManager**.
Este ejemplo corresponde a una consulta simple:

        var query = registryManager.CreateQuery("SELECT * FROM devices", 100);
        while (query.HasMoreResults)
        {
            var page = await query.GetNextAsTwinAsync();
            foreach (var twin in page)
            {
                // do work on twin object
            }
        }

Observe cómo se crea una instancia del objeto **query** con un tamaño de página (hasta 1000) y después se pueden recuperar varias páginas llamando a los métodos **GetNextAsTwinAsync** varias veces.
Es importante tener en cuenta que el objeto query expone varios elementos **next\***, según la opción de deserialización que requiera la consulta, como objetos de trabajo o dispositivo gemelo, o JSON sin formato que se usará cuando se utilicen proyecciones.

### <a name="node-example"></a>Ejemplo de Node
El [SDK del servicio Node][lnk-hub-sdks] expone la funcionalidad de consulta en el objeto **Registry**.
Este ejemplo corresponde a una consulta simple:

        var query = registry.createQuery('SELECT * FROM devices', 100);
        var onResults = function(err, results) {
            if (err) {
                console.error('Failed to fetch the results: ' + err.message);
            } else {
                // Do something with the results
                results.forEach(function(twin) {
                    console.log(twin.deviceId);
                });

                if (query.hasMoreResults) {
                    query.nextAsTwin(onResults);
                }
            }
        };
        query.nextAsTwin(onResults);

Observe cómo se crea una instancia del objeto **query** con un tamaño de página (hasta 1000) y después se pueden recuperar varias páginas llamando a los métodos **nextAsTwin** varias veces.
Es importante tener en cuenta que el objeto query expone varios elementos **next\***, según la opción de deserialización que requiera la consulta, como objetos de trabajo o dispositivo gemelo, o JSON sin formato que se usará cuando se utilicen proyecciones.

### <a name="limitations"></a>Limitaciones
Actualmente, las comparaciones solo se admiten entre tipos primitivos (no objetos), por ejemplo `... WHERE properties.desired.config = properties.reported.config` solo se admite si esas propiedades tienen valores primitivos.

## <a name="getting-started-with-jobs-queries"></a>Introducción a las consultas de trabajos
Los [trabajos][lnk-jobs] proporcionan una forma de ejecutar operaciones en conjuntos de dispositivos. Cada dispositivo gemelo contiene la información de los trabajos de los que forma parte en una colección denominada **jobs**.
Lógicamente,

        {                                                                      
            "deviceId": "myDeviceId",                                            
            "etag": "AAAAAAAAAAc=",                                              
            "tags": {                                                            
                ...                                                              
            },                                                                   
            "properties": {                                                      
                ...                                                                 
            },
            "jobs": [
                {
                    "deviceId": "myDeviceId",
                    "jobId": "myJobId",    
                    "jobType": "scheduleTwinUpdate",            
                    "status": "completed",                    
                    "startTimeUtc": "2016-09-29T18:18:52.7418462",
                    "endTimeUtc": "2016-09-29T18:20:52.7418462",
                    "createdDateTimeUtc": "2016-09-29T18:18:56.7787107Z",
                    "lastUpdatedDateTimeUtc": "2016-09-29T18:18:56.8894408Z",
                    "outcome": {
                        "deviceMethodResponse": null   
                    }                                         
                },
                ...
            ]                                                             
        }

Ahora, esta colección se puede consultar como **devices.jobs** en el lenguaje de consulta de IoT Hub.

> [!IMPORTANT]
> Actualmente, la propiedad jobs no se devuelve nunca cuando se consulta a dispositivos gemelos (es decir, las consultas que contienen 'FROM devices'). Solo es accesible directamente con las consultas que utilizan `FROM devices.jobs`.
>
>

Por ejemplo, para obtener todos los trabajos (pasados y programados) que afecten a un único dispositivo, puede usar la siguiente consulta:

        SELECT * FROM devices.jobs
        WHERE devices.jobs.deviceId = 'myDeviceId'

Observe cómo esta consulta proporciona el estado específico del dispositivo (y posiblemente la respuesta del método directo) para cada trabajo devuelto.
También es posible filtrar por condiciones booleanas arbitrarias en todas las propiedades de los objetos de la colección **devices.jobs**.
Por ejemplo, la siguiente consulta:

        SELECT * FROM devices.jobs
        WHERE devices.jobs.deviceId = 'myDeviceId'
            AND devices.jobs.jobType = 'scheduleTwinUpdate'
            AND devices.jobs.status = 'completed'
            AND devices.jobs.createdTimeUtc > '2016-09-01'

recupera todos los trabajos de actualización de dispositivos gemelos completados para el dispositivo **myDeviceId** que se crearon después de septiembre de 2016.

También es posible recuperar los resultados por dispositivo de un único trabajo.

        SELECT * FROM devices.jobs
        WHERE devices.jobs.jobId = 'myJobId'

### <a name="limitations"></a>Limitaciones
Actualmente, las consultas en **devices.jobs** no admiten:

* proyecciones, por lo tanto, solo `SELECT *` es posible;
* condiciones que hagan referencia al dispositivo gemelo, además de a las propiedades de trabajo, como se muestra antes;
* realizar agregaciones, como count, avg, group by.

## <a name="basics-of-an-iot-hub-query"></a>Conceptos básicos de una consulta de IoT Hub
Cada consulta de IoT Hub consta de cláusulas SELECT y FROM, y de cláusulas WHERE y GROUP BY opcionales. Cada consulta se ejecuta en una colección de documentos JSON, por ejemplo, dispositivos gemelos. La cláusula FROM indica la colección de documentos en la que se va a iterar (**devices** o **devices.jobs**). Después se aplica el filtro en la cláusula WHERE. En el caso de las agregaciones, se agrupan los resultados de este paso como se especifica en la cláusula GROUP BY y, para cada grupo, se genera una fila como se especifica en la cláusula SELECT.

        SELECT <select_list>
        FROM <from_specification>
        [WHERE <filter_condition>]
        [GROUP BY <group_specification>]

## <a name="from-clause"></a>Cláusula FROM
La cláusula **FROM <from_specification>** solo puede suponer dos valores: **FROM devices**, para consultar dispositivos gemelos, o **FROM devices.jobs**, para consultar detalles por dispositivo del trabajo.

## <a name="where-clause"></a>Cláusula WHERE
La cláusula **WHERE <filter_condition>** es opcional. Especifica las condiciones que los documentos JSON en la colección FROM deben satisfacer para incluirse como parte del resultado. Cualquier documento JSON debe evaluar las condiciones especificadas como "true" para que se incluya en el resultado.

Las condiciones permitidas se describen en la sección [Expresiones y condiciones][lnk-query-expressions].

## <a name="select-clause"></a>Cláusula SELECT
La cláusula SELECT (**SELECT <select_list>**) es obligatoria y especifica qué valores se recuperarán de la consulta. Especifica los valores JSON que se usarán para generar nuevos objetos JSON. Para cada elemento del subconjunto filtrado (y, opcionalmente, agrupado) de la colección FROM, la fase de proyección genera un nuevo objeto JSON, construido con los valores especificados en la cláusula SELECT.

Esta es la gramática de la cláusula SELECT:

        SELECT [TOP <max number>] <projection list>

        <projection_list> ::=
            '*'
            | <projection_element> AS alias [, <projection_element> AS alias]+

        <projection_element> :==
            attribute_name
            | <projection_element> '.' attribute_name
            | <aggregate>

        <aggregate> :==
            count()
            | avg(<projection_element>)
            | sum(<projection_element>)
            | min(<projection_element>)
            | max(<projection_element>)

donde **attribute_name** hace referencia a cualquier propiedad del documento JSON en la colección FROM. Puede ver algunos ejemplos de las cláusulas SELECT en la sección [Introducción a las consultas de gemelos][lnk-query-getstarted].

Actualmente, las cláusulas de selección distintas a **SELECT \*** solo se admiten en las consultas agregadas de dispositivos gemelos.

## <a name="group-by-clause"></a>Cláusula GROUP BY
La cláusula **GROUP BY <group_specification>** es un paso opcional que se puede ejecutar después del filtro especificado en la cláusula WHERE y antes de la proyección especificada en la cláusula SELECT. Agrupa los documentos según el valor de un atributo. Estos grupos se usan para generar valores agregados, como se especifica en la cláusula SELECT.

Un ejemplo de consulta con GROUP BY es:

        SELECT properties.reported.telemetryConfig.status AS status,
            COUNT() AS numberOfDevices
        FROM devices
        GROUP BY properties.reported.telemetryConfig.status

La sintaxis formal de GROUP BY es:

        GROUP BY <group_by_element>
        <group_by_element> :==
            attribute_name
            | < group_by_element > '.' attribute_name

donde **attribute_name** hace referencia a cualquier propiedad del documento JSON en la colección FROM.

Actualmente, solo se admite la cláusula GROUP BY al consultar dispositivos gemelos.

## <a name="expressions-and-conditions"></a>Expresiones y condiciones
Brevemente, una *expresión*:

* Se evalúa como una instancia de tipo JSON (como booleano, número, cadena, matriz u objeto), y
* Se define manipulando datos procedentes del documento JSON del dispositivo y constantes mediante funciones y operadores integrados.

Las *condiciones* son expresiones que se evalúan como booleano, por lo tanto, cualquier constante diferente del booleano **true** se considera **false** (incluidos **null**, **undefined**, cualquier instancia de objeto o matriz, cualquier cadena y, obviamente, el booleano **false**).

La sintaxis de las expresiones es:

        <expression> ::=
            <constant> |
            attribute_name |
            <function_call> |
            <expression> binary_operator <expression> |
            <create_array_expression> |
            '(' <expression> ')'

        <function_call> ::=
            <function_name> '(' expression ')'

        <constant> ::=
            <undefined_constant>
            | <null_constant>
            | <number_constant>
            | <string_constant>
            | <array_constant>

        <undefined_constant> ::= undefined
        <null_constant> ::= null
        <number_constant> ::= decimal_literal | hexadecimal_literal
        <string_constant> ::= string_literal
        <array_constant> ::= '[' <constant> [, <constant>]+ ']'

donde:

| Símbolo | Definición |
| --- | --- |
| attribute_name |Cualquier propiedad del documento JSON en la colección FROM. |
| binary_operator |Cualquier operador binario según la sección Operadores. |
| function_name| La única función admitida es `is_defined()` |
| decimal_literal |Un valor float expresado en notación decimal. |
| hexadecimal_literal |Un número expresado por la cadena "0x" seguida de una cadena de dígitos hexadecimales. |
| string_literal |Los literales de cadena son cadenas Unicode representadas por una secuencia de cero o varios caracteres Unicode o secuencias de escape. Los literales de cadena se encierran entre comillas simples (apóstrofo: ') o comillas dobles (comillas: "). Caracteres de escape permitidos: `\'`, `\"`, `\\`, `\uXXXX` para los caracteres Unicode definidos con 4 dígitos hexadecimales. |

### <a name="operators"></a>Operadores
Se admiten los siguientes operadores:

| Familia | Operadores |
| --- | --- |
| Aritméticos |+,-,*,/,% |
| Lógicos |AND, OR, NOT |
| De comparación |=, !=, <, >, <=, >=, <> |

## <a name="next-steps"></a>Pasos siguientes
Aprenda a ejecutar consultas en sus aplicaciones mediante los [SDK IoT de Azure ][lnk-hub-sdks].

[lnk-query-where]: iot-hub-devguide-query-language.md#where-clause
[lnk-query-expressions]: iot-hub-devguide-query-language.md#expressions-and-conditions
[lnk-query-getstarted]: iot-hub-devguide-query-language.md#getting-started-with-device-twin-queries

[lnk-twins]: iot-hub-devguide-device-twins.md
[lnk-jobs]: iot-hub-devguide-jobs.md
[lnk-devguide-endpoints]: iot-hub-devguide-endpoints.md
[lnk-devguide-quotas]: iot-hub-devguide-quotas-throttling.md
[lnk-devguide-mqtt]: iot-hub-mqtt-support.md

[lnk-hub-sdks]: iot-hub-devguide-sdks.md



<!--HONumber=Nov16_HO5-->


