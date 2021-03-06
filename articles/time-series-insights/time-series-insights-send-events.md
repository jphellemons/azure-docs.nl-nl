---
title: Het verzenden van gebeurtenissen naar een Azure Time Series Insights-omgeving | Microsoft Docs
description: Deze zelfstudie wordt uitgelegd hoe maken en event hub configureren en uitvoeren van een voorbeeldtoepassing push gebeurtenissen moet worden weergegeven in Azure Time Series Insights.
ms.service: time-series-insights
services: time-series-insights
author: venkatgct
ms.author: venkatja
manager: jhubbard
ms.reviewer: v-mamcge, jasonh, kfile, anshan
ms.devlang: csharp
ms.workload: big-data
ms.topic: conceptual
ms.date: 04/09/2018
ms.openlocfilehash: db528f5a02d90e7e1e2e2cd3da30f04755575777
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34657785"
---
# <a name="send-events-to-a-time-series-insights-environment-using-event-hub"></a>Gebeurtenissen verzenden naar een Time Series Insights-omgeving met Event Hub
Dit artikel wordt uitgelegd hoe u maken en configureren van de event hub en uitvoeren van een voorbeeldtoepassing push-gebeurtenissen. Als u een bestaande event hub met gebeurtenissen in JSON-indeling hebt, deze zelfstudie overslaan en weergeven van uw omgeving in [Time Series Insights](https://insights.timeseries.azure.com).

## <a name="configure-an-event-hub"></a>Een Event Hub configureren
1. Voor het maken van uw Event Hub volgt u de instructies in de Event Hub-[documentatie](../event-hubs/event-hubs-create.md).

2. Zoeken naar **event hub** in de zoekbalk. Klik op **Event Hubs** in de geretourneerde lijst.

3. Selecteer uw event hub door te klikken op de naam ervan.

4. Onder **entiteiten** Klik in het Configuratievenster middelste **Event Hubs** opnieuw.

5. Selecteer de naam van de event hub te configureren.

  ![Event Hub-consumergroep selecteren](media/send-events/consumer-group.png)

6. Onder **entiteiten**, selecteer **consumergroepen**.
 
7. Zorg ervoor dat u een consumergroep maakt die uitsluitend door uw Time Series Insights-gebeurtenisbron wordt gebruikt.

   > [!IMPORTANT]
   > Deze consumergroep mag niet worden gebruikt door een andere service (zoals een Stream Analytics-taak of een andere Time Series Insights-omgeving). Als de consumergroep wordt gebruikt door andere wordt services, leesbewerking negatief beïnvloed voor deze omgeving en de andere services. Als u $Default als consumergroep selecteert, kan dit leiden tot mogelijk hergebruik door andere lezers.

8. Onder de **instellingen** kop, selecteer **Share toegangsbeleid**.

9. Maak op de event hub, **MySendPolicy** die wordt gebruikt voor het verzenden van gebeurtenissen in het voorbeeld csharp.

  ![Een beleid voor gedeelde toegang selecteren en klikken op de knop Toevoegen](media/send-events/shared-access-policy.png)  

  ![Een nieuw beleid voor gedeelde toegang toevoegen](media/send-events/shared-access-policy-2.png)  

## <a name="add-time-series-insights-reference-data-set"></a>Gegevensset voor tijd reeks Insights verwijzing toevoegen 
Gebruik van referentiegegevens in TSI contextualizes uw telemetriegegevens.  Die context betekenis toegevoegd aan uw gegevens en gemakkelijker te filteren en statistische functie.  TSI join verwijzen naar gegevens op moment van toegangsroutes en kunnen niet met terugwerkende kracht deelnemen aan deze gegevens.  Het is daarom essentieel om toe te voegen referentiegegevens vóór het toevoegen van een gebeurtenisbron met gegevens.  Gegevens, zoals locatie of sensor type zijn nuttig dimensies die u kunt koppelen aan een apparaat, de tag/het sensor ID gemakkelijker kunt segment en filteren.  

> [!IMPORTANT]
> Het is essentieel om met een verwijzing gegevensset geconfigureerd tijdens het uploaden van historische gegevens.

Zorg ervoor dat u referentiegegevens aanwezig wanneer u de historische uploadgegevens naar TSI bulksgewijs.  Houd er rekening mee, TSI onmiddellijk lezen van een gebeurtenisbron van de gekoppelde wordt gestart als de bron van die gebeurtenis heeft gegevens.  Dit is handig moet worden gewacht met het toevoegen aan een gebeurtenisbron TSI totdat u uw referentiegegevens aanwezig is hebt, vooral als de bron van die gebeurtenis gegevens bevat. U kunt ook wachten met push-gegevens naar die bron totdat de gegevensset verwijzing geïmplementeerd is.

Voor het beheren van referentiegegevens zich de webgebaseerde gebruikersinterface in de Verkenner TSI en er is een programmatische C#-API. TSI Explorer een visual gebruikerservaring heeft voor het van uploadbestanden of plakken in bestaande verwijzing gegevenssets als JSON- of CSV-indeling. Met de API kunt u een aangepaste app wanneer deze nodig is.

Zie voor meer informatie over het beheren van referentiegegevens in tijd reeks inzichten de [verwijzingsartikel gegevens](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-add-reference-data-set).

## <a name="create-time-series-insights-event-source"></a>Een Time Series Insights-gebeurtenisbron maken
1. Als u nog geen gebeurtenisbron hebt gemaakt, volgt u [deze instructies](time-series-insights-how-to-add-an-event-source-eventhub.md) om een gebeurtenisbron te maken.

2. Geef **deviceTimestamp** als de naam van de timestamp-eigenschap – deze eigenschap wordt gebruikt als de werkelijke tijdstempel in het C#-voorbeeld. De naam van de tijdstempeleigenschap is hoofdlettergevoelig en waarden moeten de indeling __jjjj-MM-ddTUU. FFFFFFFK__ volgen wanneer ze als JSON worden verzonden naar een Event Hub. Als de eigenschap niet aanwezig is in de gebeurtenis, wordt de tijd gebruikt waarop de gebeurtenis in de wachtrij van de Event Hub is geplaatst.

  ![Gebeurtenisbron maken](media/send-events/event-source-1.png)

## <a name="sample-code-to-push-events"></a>Voorbeeldcode uitvoeren om gebeurtenissen te pushen
1. Ga naar de event hub-beleid met de naam **MySendPolicy**. Kopieer de **verbindingsreeks** met de beleidssleutel.

  ![MySendPolicy-verbindingsreeks kopiëren](media/send-events/sample-code-connection-string.png)

2. Voer de volgende code uit, waarmee voor elk van de drie apparaten 600 gebeurtenissen worden verzonden. Werk `eventHubConnectionString` bij met uw verbindingsreeks.

```csharp
using System;
using System.Collections.Generic;
using System.Globalization;
using System.IO;
using Microsoft.ServiceBus.Messaging;

namespace Microsoft.Rdx.DataGenerator
{
    internal class Program
    {
        private static void Main(string[] args)
        {
            var from = new DateTime(2017, 4, 20, 15, 0, 0, DateTimeKind.Utc);
            Random r = new Random();
            const int numberOfEvents = 600;

            var deviceIds = new[] { "device1", "device2", "device3" };

            var events = new List<string>();
            for (int i = 0; i < numberOfEvents; ++i)
            {
                for (int device = 0; device < deviceIds.Length; ++device)
                {
                    // Generate event and serialize as JSON object:
                    // { "deviceTimestamp": "utc timestamp", "deviceId": "guid", "value": 123.456 }
                    events.Add(
                        String.Format(
                            CultureInfo.InvariantCulture,
                            @"{{ ""deviceTimestamp"": ""{0}"", ""deviceId"": ""{1}"", ""value"": {2} }}",
                            (from + TimeSpan.FromSeconds(i * 30)).ToString("o"),
                            deviceIds[device],
                            r.NextDouble()));
                }
            }

            // Create event hub connection.
            var eventHubConnectionString = @"Endpoint=sb://...";
            var eventHubClient = EventHubClient.CreateFromConnectionString(eventHubConnectionString);

            using (var ms = new MemoryStream())
            using (var sw = new StreamWriter(ms))
            {
                // Wrap events into JSON array:
                sw.Write("[");
                for (int i = 0; i < events.Count; ++i)
                {
                    if (i > 0)
                    {
                        sw.Write(',');
                    }
                    sw.Write(events[i]);
                }
                sw.Write("]");

                sw.Flush();
                ms.Position = 0;

                // Send JSON to event hub.
                EventData eventData = new EventData(ms);
                eventHubClient.Send(eventData);
            }
        }
    }
}

```
## <a name="supported-json-shapes"></a>Ondersteunde JSON-vormen
### <a name="sample-1"></a>Voorbeeld 1

#### <a name="input"></a>Invoer

Een eenvoudig JSON-object.

```json
{
    "id":"device1",
    "timestamp":"2016-01-08T01:08:00Z"
}
```
#### <a name="output---one-event"></a>Output - één gebeurtenis

|id|tijdstempel|
|--------|---------------|
|device1|2016-01-08T01:08:00Z|

### <a name="sample-2"></a>Voorbeeld 2

#### <a name="input"></a>Invoer
Een JSON-matrix met twee JSON-objecten. Elk JSON-object wordt omgezet in een gebeurtenis.
```json
[
    {
        "id":"device1",
        "timestamp":"2016-01-08T01:08:00Z"
    },
    {
        "id":"device2",
        "timestamp":"2016-01-17T01:17:00Z"
    }
]
```
#### <a name="output---two-events"></a>Output - twee gebeurtenissen

|id|tijdstempel|
|--------|---------------|
|device1|2016-01-08T01:08:00Z|
|device2|2016-01-08T01:17:00Z|

### <a name="sample-3"></a>Voorbeeld 3

#### <a name="input"></a>Invoer

Een JSON-object met een geneste JSON-matrix die twee JSON-objecten bevat:
```json
{
    "location":"WestUs",
    "events":[
        {
            "id":"device1",
            "timestamp":"2016-01-08T01:08:00Z"
        },
        {
            "id":"device2",
            "timestamp":"2016-01-17T01:17:00Z"
        }
    ]
}

```
#### <a name="output---two-events"></a>Output - twee gebeurtenissen
U ziet dat de eigenschap 'locatie' wordt gekopieerd naar elk van de gebeurtenis.

|location|events.id|events.timestamp|
|--------|---------------|----------------------|
|WestUs|device1|2016-01-08T01:08:00Z|
|WestUs|device2|2016-01-08T01:17:00Z|

### <a name="sample-4"></a>Voorbeeld 4

#### <a name="input"></a>Invoer

Een JSON-object met een geneste JSON-matrix met twee JSON-objecten. Uit deze invoer blijkt dat de algemene eigenschappen kunnen worden vertegenwoordigd door het complexe JSON-object.

```json
{
    "location":"WestUs",
    "manufacturer":{
        "name":"manufacturer1",
        "location":"EastUs"
    },
    "events":[
        {
            "id":"device1",
            "timestamp":"2016-01-08T01:08:00Z",
            "data":{
                "type":"pressure",
                "units":"psi",
                "value":108.09
            }
        },
        {
            "id":"device2",
            "timestamp":"2016-01-17T01:17:00Z",
            "data":{
                "type":"vibration",
                "units":"abs G",
                "value":217.09
            }
        }
    ]
}
```
#### <a name="output---two-events"></a>Output - twee gebeurtenissen

|location|manufacturer.name|manufacturer.location|events.id|events.timestamp|events.data.type|events.data.units|events.data.value|
|---|---|---|---|---|---|---|---|
|WestUs|manufacturer1|EastUs|device1|2016-01-08T01:08:00Z|pressure|psi|108.09|
|WestUs|manufacturer1|EastUs|device2|2016-01-08T01:17:00Z|vibration|abs G|217.09|



## <a name="next-steps"></a>Volgende stappen
> [!div class="nextstepaction"]
> [Uw omgeving weergeven in tijd reeks Insights explorer](https://insights.timeseries.azure.com).
