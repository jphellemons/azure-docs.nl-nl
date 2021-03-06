---
title: Azure Event Hubs-bindingen voor Azure Functions
description: Het gebruik van Azure Event Hubs bindingen in de Azure Functions begrijpen.
services: functions
documentationcenter: na
author: tdykstra
manager: cfowler
editor: ''
tags: ''
keywords: Azure functions, functies, verwerking van gebeurtenissen, dynamische compute zonder server architectuur
ms.assetid: daf81798-7acc-419a-bc32-b5a41c6db56b
ms.service: functions
ms.devlang: multiple
ms.topic: reference
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 11/08/2017
ms.author: tdykstra
ms.openlocfilehash: 64914a1b3efe81a152f5463f74c70c22f01ec0c1
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/04/2018
ms.locfileid: "34724041"
---
# <a name="azure-event-hubs-bindings-for-azure-functions"></a>Azure Event Hubs-bindingen voor Azure Functions

Dit artikel wordt uitgelegd hoe u werkt met [Azure Event Hubs](../event-hubs/event-hubs-what-is-event-hubs.md) bindingen voor Azure Functions. Azure Functions ondersteunt activeren en uitvoer van de bindingen voor Event Hubs.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a name="packages---functions-1x"></a>Pakketten - functies 1.x

Voor Azure Functions versie 1.x, de Event Hubs-bindingen zijn opgegeven in de [Microsoft.Azure.WebJobs.ServiceBus](http://www.nuget.org/packages/Microsoft.Azure.WebJobs.ServiceBus) NuGet-pakket versie 2.x.
De broncode voor het pakket bevindt zich in de [sdk van azure webjobs](https://github.com/Azure/azure-webjobs-sdk/tree/v2.x/src/Microsoft.Azure.WebJobs.ServiceBus/EventHubs) GitHub-opslagplaats.


[!INCLUDE [functions-package](../../includes/functions-package.md)]

## <a name="packages---functions-2x"></a>Pakketten - functies 2.x

Voor functies 2.x, gebruik de [Microsoft.Azure.WebJobs.Extensions.EventHubs](http://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.EventHubs) pakket, versie 3.x.
De broncode voor het pakket bevindt zich in de [sdk van azure webjobs](https://github.com/Azure/azure-webjobs-sdk/tree/master/src/Microsoft.Azure.WebJobs.Extensions.EventHubs) GitHub-opslagplaats.

[!INCLUDE [functions-package-v2](../../includes/functions-package-v2.md)]

## <a name="trigger"></a>Trigger

Gebruik de Event Hubs-trigger om te reageren op een gebeurtenis verzonden naar de gebeurtenisstroom van een event hub. U moet leestoegang hebben tot de event hub voor het instellen van de trigger.

Wanneer een functie van Event Hubs trigger wordt geactiveerd, wordt het bericht dat deze wordt geactiveerd als een tekenreeks in de functie doorgegeven.

## <a name="trigger---scaling"></a>Activeren - schaling

Elk exemplaar van een functie Event Hub-Triggered wordt ondersteund door alleen 1 EventProcessorHost (EPH)-exemplaar. Event Hubs zorgt ervoor dat slechts 1 EPH een lease op een bepaalde partitie kunt krijgen.

Stel bijvoorbeeld dat we beginnen met de volgende instellingen en veronderstellingen voor een Event Hub:

1. 10 partities.
1. 1000 gebeurtenissen gelijkmatig verdeeld over alle partities = > 100 berichten in elke partitie.

Wanneer de functie voor het eerst wordt ingeschakeld, is er slechts 1 exemplaar van de functie. Laten we dit exemplaar van de functie Function_0 aanroepen. Function_0 hebben 1 EPH die beheert voor een lease op alle 10 partities. Lezen van gebeurtenissen uit partities 0-9 wordt gestart. Vanaf dit punt een van de volgende gebeurt:

* **Functie slechts 1 exemplaar nodig** -Function_0 kunnen worden verwerkt alle 1000 voordat de Azure Functions schalen logica gang is. Daarom worden alle 1000 berichten worden verwerkt door Function_0.

* **1 meer functie exemplaar toevoegen** -'Azure Functions schalen logica bepaalt dat Function_0 meer berichten dan kunnen worden verwerkt, heeft zodat een nieuw exemplaar Function_1, wordt gemaakt. Event Hubs detecteert dat een nieuw exemplaar van de EPH probeert berichten lezen. Event Hubs de partities voor taakverdeling over de exemplaren EPH wordt gestart, bijv, 0-4 partities zijn toegewezen aan Function_0 en partities 5-9 zijn toegewezen aan Function_1. 

* **Voeg N werken meer exemplaren** -'Azure Functions schalen logica bepaalt dat zowel Function_0 als Function_1 geen berichten meer hebben dan ze kunnen verwerken. Dit wordt opnieuw schalen voor Function_2... N, waarbij N groter dan de Event Hub-partities is. Event Hubs wordt geladen verdeeld de partities Function_0... 9 exemplaren.

Uniek is voor de huidige Azure-Functions schalen logica is het feit dat N groter dan het aantal partities is. Dit wordt gedaan om ervoor te zorgen dat er altijd exemplaren van EPH beschikken snel een vergrendeling krijgen voor de partities zodra deze beschikbaar zijn van andere exemplaren. Gebruikers worden alleen kosten in rekening gebracht voor de bronnen die worden gebruikt wanneer het exemplaar van de functie wordt uitgevoerd, en worden niet in rekening gebracht voor deze overprovisioning.

Als alle functies die zonder fouten slagen, worden controlepunten toegevoegd aan het bijbehorende opslagaccount. Als controle wijzen is geslaagd, moeten alle 1000 berichten nooit opnieuw worden opgehaald.

## <a name="trigger---example"></a>Trigger - voorbeeld

Zie het voorbeeld taalspecifieke:

* [C#](#trigger---c-example)
* [C# script (.csx)](#trigger---c-script-example)
* [F#](#trigger---f-example)
* [JavaScript](#trigger---javascript-example)

### <a name="trigger---c-example"></a>Trigger - C#-voorbeeld

Het volgende voorbeeld wordt een [C#-functie](functions-dotnet-class-library.md) die zich aanmeldt met de berichttekst van de event hub-trigger.

```csharp
[FunctionName("EventHubTriggerCSharp")]
public static void Run([EventHubTrigger("samples-workitems", Connection = "EventHubConnectionAppSetting")] string myEventHubMessage, TraceWriter log)
{
    log.Info($"C# Event Hub trigger function processed a message: {myEventHubMessage}");
}
```

Toegang krijgen tot [gebeurtenis metagegevens](#trigger---event-metadata) in functiecode, koppelen aan een [EventData](/dotnet/api/microsoft.servicebus.messaging.eventdata) object (moet een u-instructie voor `Microsoft.ServiceBus.Messaging`). U kunt ook toegang tot dezelfde eigenschappen met behulp van de expressies voor gegevensbinding in de methodehandtekening.  Het volgende voorbeeld ziet u beide manieren om dezelfde gegevens:

```csharp
[FunctionName("EventHubTriggerCSharp")]
public static void Run(
    [EventHubTrigger("samples-workitems", Connection = "EventHubConnectionAppSetting")] EventData myEventHubMessage, 
    DateTime enqueuedTimeUtc, 
    Int64 sequenceNumber,
    string offset,
    TraceWriter log)
{
    log.Info($"Event: {Encoding.UTF8.GetString(myEventHubMessage.GetBytes())}");
    // Metadata accessed by binding to EventData
    log.Info($"EnqueuedTimeUtc={myEventHubMessage.EnqueuedTimeUtc}");
    log.Info($"SequenceNumber={myEventHubMessage.SequenceNumber}");
    log.Info($"Offset={myEventHubMessage.Offset}");
    // Metadata accessed by using binding expressions
    log.Info($"EnqueuedTimeUtc={enqueuedTimeUtc}");
    log.Info($"SequenceNumber={sequenceNumber}");
    log.Info($"Offset={offset}");
}
```

Zorg voor het ontvangen van gebeurtenissen in een batch, `string` of `EventData` een matrix:

```cs
[FunctionName("EventHubTriggerCSharp")]
public static void Run([EventHubTrigger("samples-workitems", Connection = "EventHubConnectionAppSetting")] string[] eventHubMessages, TraceWriter log)
{
    foreach (var message in eventHubMessages)
    {
        log.Info($"C# Event Hub trigger function processed a message: {message}");
    }
}
```

### <a name="trigger---c-script-example"></a>Trigger - voorbeeld van C#-script

Het volgende voorbeeld ziet u een event hub-trigger binding in een *function.json* bestand en een [C# scriptfunctie](functions-reference-csharp.md) die gebruikmaakt van de binding. De functie Logboeken de berichttekst van de event hub-trigger.

De volgende voorbeelden tonen Event Hubs bindingsgegevens in de *function.json* bestand. Het eerste voorbeeld is voor 1.x functioneert en het tweede is voor functies 2.x. 

```json
{
  "type": "eventHubTrigger",
  "name": "myEventHubMessage",
  "direction": "in",
  "path": "MyEventHub",
  "connection": "myEventHubReadConnectionAppSetting"
}
```
```json
{
  "type": "eventHubTrigger",
  "name": "myEventHubMessage",
  "direction": "in",
  "eventHubName": "MyEventHub",
  "connection": "myEventHubReadConnectionAppSetting"
}
```

Dit is de C#-scriptcode:

```cs
using System;

public static void Run(string myEventHubMessage, TraceWriter log)
{
    log.Info($"C# Event Hub trigger function processed a message: {myEventHubMessage}");
}
```

Toegang krijgen tot [gebeurtenis metagegevens](#trigger---event-metadata) in functiecode, koppelen aan een [EventData](/dotnet/api/microsoft.servicebus.messaging.eventdata) object (moet een u-instructie voor `Microsoft.ServiceBus.Messaging`). U kunt ook toegang tot dezelfde eigenschappen met behulp van de expressies voor gegevensbinding in de methodehandtekening.  Het volgende voorbeeld ziet u beide manieren om dezelfde gegevens:

```cs
#r "Microsoft.ServiceBus"
using System.Text;
using System;
using Microsoft.ServiceBus.Messaging;

public static void Run(EventData myEventHubMessage,
    DateTime enqueuedTimeUtc, 
    Int64 sequenceNumber,
    string offset,
    TraceWriter log)
{
    log.Info($"Event: {Encoding.UTF8.GetString(myEventHubMessage.GetBytes())}");
    // Metadata accessed by binding to EventData
    log.Info($"EnqueuedTimeUtc={myEventHubMessage.EnqueuedTimeUtc}");
    log.Info($"SequenceNumber={myEventHubMessage.SequenceNumber}");
    log.Info($"Offset={myEventHubMessage.Offset}");
    // Metadata accessed by using binding expressions
    log.Info($"EnqueuedTimeUtc={enqueuedTimeUtc}");
    log.Info($"SequenceNumber={sequenceNumber}");
    log.Info($"Offset={offset}");
}
```

Zorg voor het ontvangen van gebeurtenissen in een batch, `string` of `EventData` een matrix:

```cs
public static void Run(string[] eventHubMessages, TraceWriter log)
{
    foreach (var message in eventHubMessages)
    {
        log.Info($"C# Event Hub trigger function processed a message: {message}");
    }
}
```

### <a name="trigger---f-example"></a>Trigger - F #-voorbeeld

Het volgende voorbeeld ziet u een event hub-trigger binding in een *function.json* bestand en een [F # functie](functions-reference-fsharp.md) die gebruikmaakt van de binding. De functie Logboeken de berichttekst van de event hub-trigger.

De volgende voorbeelden tonen Event Hubs bindingsgegevens in de *function.json* bestand. Het eerste voorbeeld is voor 1.x functioneert en het tweede is voor functies 2.x. 

```json
{
  "type": "eventHubTrigger",
  "name": "myEventHubMessage",
  "direction": "in",
  "path": "MyEventHub",
  "connection": "myEventHubReadConnectionAppSetting"
}
```
```json
{
  "type": "eventHubTrigger",
  "name": "myEventHubMessage",
  "direction": "in",
  "eventHubName": "MyEventHub",
  "connection": "myEventHubReadConnectionAppSetting"
}
```

Dit is de F #-code:

```fsharp
let Run(myEventHubMessage: string, log: TraceWriter) =
    log.Info(sprintf "F# eventhub trigger function processed work item: %s" myEventHubMessage)
```

### <a name="trigger---javascript-example"></a>Trigger - JavaScript-voorbeeld

Het volgende voorbeeld ziet u een event hub-trigger binding in een *function.json* bestand en een [JavaScript-functie](functions-reference-node.md) die gebruikmaakt van de binding. De functie leest [gebeurtenis metagegevens](#trigger---event-metadata) en registreert het bericht.

De volgende voorbeelden tonen Event Hubs bindingsgegevens in de *function.json* bestand. Het eerste voorbeeld is voor 1.x functioneert en het tweede is voor functies 2.x. 

```json
{
  "type": "eventHubTrigger",
  "name": "myEventHubMessage",
  "direction": "in",
  "path": "MyEventHub",
  "connection": "myEventHubReadConnectionAppSetting"
}
```
```json
{
  "type": "eventHubTrigger",
  "name": "myEventHubMessage",
  "direction": "in",
  "eventHubName": "MyEventHub",
  "connection": "myEventHubReadConnectionAppSetting"
}
```

Hier volgt de JavaScript-code:

```javascript
module.exports = function (context, eventHubMessage) {
    context.log('Event Hubs trigger function processed message: ', myEventHubMessage);
    context.log('EnqueuedTimeUtc =', context.bindingData.enqueuedTimeUtc);
    context.log('SequenceNumber =', context.bindingData.sequenceNumber);
    context.log('Offset =', context.bindingData.offset);
     
    context.done();
};
```

Voor het ontvangen van gebeurtenissen in een batch stelt `cardinality` naar `many` in de *function.json* -bestand, zoals wordt weergegeven in de volgende voorbeelden. Het eerste voorbeeld is voor 1.x functioneert en het tweede is voor functies 2.x. 

```json
{
  "type": "eventHubTrigger",
  "name": "eventHubMessages",
  "direction": "in",
  "path": "MyEventHub",
  "cardinality": "many",
  "connection": "myEventHubReadConnectionAppSetting"
}
```
```json
{
  "type": "eventHubTrigger",
  "name": "eventHubMessages",
  "direction": "in",
  "eventHubName": "MyEventHub",
  "cardinality": "many",
  "connection": "myEventHubReadConnectionAppSetting"
}
```

Hier volgt de JavaScript-code:

```javascript
module.exports = function (context, eventHubMessages) {
    context.log(`JavaScript eventhub trigger function called for message array ${eventHubMessages}`);
    
    eventHubMessages.forEach(message => {
        context.log(`Processed message ${message}`);
    });

    context.done();
};
```

## <a name="trigger---attributes"></a>Trigger - kenmerken

In [C#-klassebibliotheken](functions-dotnet-class-library.md), gebruiken de [EventHubTriggerAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.ServiceBus/EventHubs/EventHubTriggerAttribute.cs) kenmerk.

De constructor van het kenmerk werkt met de naam van de event hub, de naam van de consumergroep en de naam van een app-instelling met de verbindingsreeks. Zie voor meer informatie over deze instellingen de [activeren configuratiesectie](#trigger---configuration). Hier volgt een `EventHubTriggerAttribute` kenmerk voorbeeld:

```csharp
[FunctionName("EventHubTriggerCSharp")]
public static void Run([EventHubTrigger("samples-workitems", Connection = "EventHubConnectionAppSetting")] string myEventHubMessage, TraceWriter log)
{
    ...
}
```

Zie voor een compleet voorbeeld [Trigger - C#-voorbeeld](#trigger---c-example).

## <a name="trigger---configuration"></a>Trigger - configuratie

De volgende tabel beschrijft de binding-configuratie-eigenschappen die u instelt in de *function.json* bestand en de `EventHubTrigger` kenmerk.

|de eigenschap Function.JSON | De kenmerkeigenschap |Beschrijving|
|---------|---------|----------------------|
|**type** | N.v.t. | moet worden ingesteld op `eventHubTrigger`. Deze eigenschap wordt automatisch ingesteld wanneer u de trigger in de Azure-portal maakt.|
|**direction** | N.v.t. | moet worden ingesteld op `in`. Deze eigenschap wordt automatisch ingesteld wanneer u de trigger in de Azure-portal maakt. |
|**Naam** | N.v.t. | De naam van de variabele die staat voor de gebeurtenis in de functiecode. | 
|**Pad** |**EventHubName** | Alleen 1.x fungeert. De naam van de event hub.  | 
|**EventHubName** |**EventHubName** | Alleen 2.x fungeert. De naam van de event hub.  |
|**ConsumerGroup** |**ConsumerGroup** | Een optionele eigenschap die bepaalt de [consumergroep](../event-hubs/event-hubs-features.md#event-consumers) gebruikt om u te abonneren op gebeurtenissen in de hub. Als u dit weglaat, de `$Default` consumergroep wordt gebruikt. | 
|**Kardinaliteit** | N.v.t. | Als u Javascript. Ingesteld op `many` om in te schakelen batchverwerking.  Als u dit weglaat of ingesteld op `one`, één bericht doorgegeven aan functie. | 
|**Verbinding** |**Verbinding** | De naam van een app-instelling met de verbindingsreeks naar de event hub-naamruimte. Kopieer deze verbindingsreeks door te klikken op de **verbindingsgegevens** knop voor de [naamruimte](../event-hubs/event-hubs-create.md#create-an-event-hubs-namespace), niet de event hub zelf. Deze verbindingsreeks moet ten minste leesmachtigingen hebben voor de trigger wordt geactiveerd.|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="trigger---event-metadata"></a>Trigger - metagegevens van de gebeurtenis

De trigger Event Hubs bevat diverse [eigenschappen voor metagegevens](functions-triggers-bindings.md#binding-expressions---trigger-metadata). Deze eigenschappen kunnen worden gebruikt als onderdeel van de expressies voor gegevensbinding in andere bindingen of als parameters in uw code. Dit zijn de eigenschappen van de [EventData](https://docs.microsoft.com/en-us/dotnet/api/microsoft.servicebus.messaging.eventdata) klasse.

|Eigenschap|Type|Beschrijving|
|--------|----|-----------|
|`PartitionContext`|[PartitionContext](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.partitioncontext)|De `PartitionContext` exemplaar.|
|`EnqueuedTimeUtc`|`DateTime`|De tijd in de wachtrij in UTC.|
|`Offset`|`string`|De verschuiving van de gegevens ten opzichte van de Event Hub-partitie-stroom. De offset is een markering of id voor een gebeurtenis in de Event Hubs-stroom. De id is uniek zijn binnen een partitie van de Event Hubs-stroom.|
|`PartitionKey`|`string`|De partitie aan welke gebeurtenis gegevens moeten worden verzonden.|
|`Properties`|`IDictionary<String,Object>`|De gebruikerseigenschappen van de gebeurtenisgegevens worden opgeslagen.|
|`SequenceNumber`|`Int64`|Het aantal logische volgorde van de gebeurtenis.|
|`SystemProperties`|`IDictionary<String,Object>`|De eigenschappen, met inbegrip van gegevens van de gebeurtenis.|

Zie [codevoorbeelden](#trigger---example) die gebruikmaken van deze eigenschappen eerder in dit artikel.

## <a name="trigger---hostjson-properties"></a>Trigger - eigenschappen host.json

De [host.json](functions-host-json.md#eventhub) bestand bevat instellingen voor Event Hubs trigger gedrag.

[!INCLUDE [functions-host-json-event-hubs](../../includes/functions-host-json-event-hubs.md)]

## <a name="output"></a>Uitvoer

De uitvoer van de Event Hubs binding gebeurtenissen schrijven naar een stroom gebeurtenissen gebruiken. U moet gemachtigd verzenden naar een event hub gebeurtenissen om ernaar te schrijven.

## <a name="output---example"></a>Output - voorbeeld

Zie het voorbeeld taalspecifieke:

* [C#](#output---c-example)
* [C# script (.csx)](#output---c-script-example)
* [F#](#output---f-example)
* [JavaScript](#output---javascript-example)

### <a name="output---c-example"></a>Output - C#-voorbeeld

Het volgende voorbeeld wordt een [C#-functie](functions-dotnet-class-library.md) die een bericht naar een event hub, met behulp van de geretourneerde waarde van de methode als uitvoer geschreven:

```csharp
[FunctionName("EventHubOutput")]
[return: EventHub("outputEventHubMessage", Connection = "EventHubConnectionAppSetting")]
public static string Run([TimerTrigger("0 */5 * * * *")] TimerInfo myTimer, TraceWriter log)
{
    log.Info($"C# Timer trigger function executed at: {DateTime.Now}");
    return $"{DateTime.Now}";
}
```

### <a name="output---c-script-example"></a>Output - voorbeeld van C#-script

Het volgende voorbeeld ziet u een event hub-trigger binding in een *function.json* bestand en een [C# scriptfunctie](functions-reference-csharp.md) die gebruikmaakt van de binding. De functie schrijft een bericht naar een event hub.

De volgende voorbeelden tonen Event Hubs bindingsgegevens in de *function.json* bestand. Het eerste voorbeeld is voor 1.x functioneert en het tweede is voor functies 2.x. 

```json
{
    "type": "eventHub",
    "name": "outputEventHubMessage",
    "path": "myeventhub",
    "connection": "MyEventHubSendAppSetting",
    "direction": "out"
}
```
```json
{
    "type": "eventHub",
    "name": "outputEventHubMessage",
    "eventHubName": "myeventhub",
    "connection": "MyEventHubSendAppSetting",
    "direction": "out"
}
```

Hier volgt een C# script-code die een bericht worden gemaakt:

```cs
using System;

public static void Run(TimerInfo myTimer, out string outputEventHubMessage, TraceWriter log)
{
    String msg = $"TimerTriggerCSharp1 executed at: {DateTime.Now}";
    log.Verbose(msg);   
    outputEventHubMessage = msg;
}
```

Hier volgt C# script-code die wordt gemaakt van meerdere berichten:

```cs
public static void Run(TimerInfo myTimer, ICollector<string> outputEventHubMessage, TraceWriter log)
{
    string message = $"Event Hub message created at: {DateTime.Now}";
    log.Info(message);
    outputEventHubMessage.Add("1 " + message);
    outputEventHubMessage.Add("2 " + message);
}
```

### <a name="output---f-example"></a>Output - F #-voorbeeld

Het volgende voorbeeld ziet u een event hub-trigger binding in een *function.json* bestand en een [F # functie](functions-reference-fsharp.md) die gebruikmaakt van de binding. De functie schrijft een bericht naar een event hub.

De volgende voorbeelden tonen Event Hubs bindingsgegevens in de *function.json* bestand. Het eerste voorbeeld is voor 1.x functioneert en het tweede is voor functies 2.x. 

```json
{
    "type": "eventHub",
    "name": "outputEventHubMessage",
    "path": "myeventhub",
    "connection": "MyEventHubSendAppSetting",
    "direction": "out"
}
```
```json
{
    "type": "eventHub",
    "name": "outputEventHubMessage",
    "eventHubName": "myeventhub",
    "connection": "MyEventHubSendAppSetting",
    "direction": "out"
}
```

Dit is de F #-code:

```fsharp
let Run(myTimer: TimerInfo, outputEventHubMessage: byref<string>, log: TraceWriter) =
    let msg = sprintf "TimerTriggerFSharp1 executed at: %s" DateTime.Now.ToString()
    log.Verbose(msg);
    outputEventHubMessage <- msg;
```

### <a name="output---javascript-example"></a>Output - JavaScript-voorbeeld

Het volgende voorbeeld ziet u een event hub-trigger binding in een *function.json* bestand en een [JavaScript-functie](functions-reference-node.md) die gebruikmaakt van de binding. De functie schrijft een bericht naar een event hub.

De volgende voorbeelden tonen Event Hubs bindingsgegevens in de *function.json* bestand. Het eerste voorbeeld is voor 1.x functioneert en het tweede is voor functies 2.x. 

```json
{
    "type": "eventHub",
    "name": "outputEventHubMessage",
    "path": "myeventhub",
    "connection": "MyEventHubSendAppSetting",
    "direction": "out"
}
```
```json
{
    "type": "eventHub",
    "name": "outputEventHubMessage",
    "eventHubName": "myeventhub",
    "connection": "MyEventHubSendAppSetting",
    "direction": "out"
}
```

Hier volgt JavaScript-code die door een enkel bericht verzonden:

```javascript
module.exports = function (context, myTimer) {
    var timeStamp = new Date().toISOString();
    context.log('Event Hub message created at: ', timeStamp);   
    context.bindings.outputEventHubMessage = "Event Hub message created at: " + timeStamp;
    context.done();
};
```

Hier volgt JavaScript-code waarmee meerdere berichten worden verzonden:

```javascript
module.exports = function(context) {
    var timeStamp = new Date().toISOString();
    var message = 'Event Hub message created at: ' + timeStamp;

    context.bindings.outputEventHubMessage = [];

    context.bindings.outputEventHubMessage.push("1 " + message);
    context.bindings.outputEventHubMessage.push("2 " + message);
    context.done();
};
```

## <a name="output---attributes"></a>Output - kenmerken

Voor [C#-klassebibliotheken](functions-dotnet-class-library.md), gebruiken de [EventHubAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.ServiceBus/EventHubs/EventHubAttribute.cs) kenmerk.

De constructor van het kenmerk werkt met de naam van de event hub en de naam van een app-instelling met de verbindingsreeks. Zie voor meer informatie over deze instellingen [Output - configuratie](#output---configuration). Hier volgt een `EventHub` kenmerk voorbeeld:

```csharp
[FunctionName("EventHubOutput")]
[return: EventHub("outputEventHubMessage", Connection = "EventHubConnectionAppSetting")]
public static string Run([TimerTrigger("0 */5 * * * *")] TimerInfo myTimer, TraceWriter log)
{
    ...
}
```

Zie voor een compleet voorbeeld [uitvoer - C#-voorbeeld](#output---c-example).

## <a name="output---configuration"></a>Output - configuratie

De volgende tabel beschrijft de binding-configuratie-eigenschappen die u instelt in de *function.json* bestand en de `EventHub` kenmerk.

|de eigenschap Function.JSON | De kenmerkeigenschap |Beschrijving|
|---------|---------|----------------------|
|**type** | N.v.t. | Moet worden ingesteld op 'eventHub'. |
|**direction** | N.v.t. | Moet worden ingesteld op 'out'. Deze parameter wordt automatisch ingesteld bij het maken van de binding in de Azure portal. |
|**Naam** | N.v.t. | De naam van de variabele gebruikt in de functiecode waarmee de gebeurtenis. | 
|**Pad** |**EventHubName** | Alleen 1.x fungeert. De naam van de event hub.  | 
|**EventHubName** |**EventHubName** | Alleen 2.x fungeert. De naam van de event hub.  |
|**Verbinding** |**Verbinding** | De naam van een app-instelling met de verbindingsreeks naar de event hub-naamruimte. Kopieer deze verbindingsreeks door te klikken op de **verbindingsgegevens** knop voor de *naamruimte*, niet de event hub zelf. Deze verbindingsreeks moet verzenden machtigingen hebben voor het bericht naar de stroom gebeurtenissen sturen.|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="output---usage"></a>Output - gebruik

In C# en C# script, kunt u berichten verzenden met behulp van een methodeparameter zoals `out string paramName`. In C# script `paramName` is de waarde is opgegeven in de `name` eigenschap van *function.json*. U kunt gebruiken voor het schrijven van meerdere berichten `ICollector<string>` of `IAsyncCollector<string>` in plaats van `out string`.

In JavaScript, opent u de uitvoergebeurtenis met behulp van `context.bindings.<name>`. `<name>` de waarde is opgegeven in de `name` eigenschap van *function.json*.

## <a name="exceptions-and-return-codes"></a>Uitzonderingen en retourcodes

| Binding | Referentie |
|---|---|
| Event Hub | [Operations Guide](https://docs.microsoft.com/rest/api/eventhub/publisher-policy-operations) |

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Meer informatie over Azure functions triggers en bindingen](functions-triggers-bindings.md)
