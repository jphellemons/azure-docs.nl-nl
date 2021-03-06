---
title: Het gebruik van Azure Service Bus-onderwerpen en abonnementen met behulp van Node.js | Microsoft Docs
description: Informatie over het gebruik van Service Bus-onderwerpen en abonnementen van een Node.js-app in Azure.
services: service-bus-messaging
documentationcenter: nodejs
author: sethmanheim
manager: timlt
editor: ''
ms.assetid: b9f5db85-7b6c-4cc7-bd2c-bd3087c99875
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: nodejs
ms.topic: article
ms.date: 08/10/2017
ms.author: sethm
ms.openlocfilehash: d3a7ebd135f705a6a3ea91feb4e037a9ed6d0c79
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34641302"
---
# <a name="how-to-use-service-bus-topics-and-subscriptions-with-nodejs"></a>Gebruik Service Bus-onderwerpen en abonnementen met behulp van Node.js

[!INCLUDE [service-bus-selector-topics](../../includes/service-bus-selector-topics.md)]

Deze handleiding wordt beschreven hoe u Service Bus-onderwerpen en abonnementen van Node.js-toepassingen. De scenario's worden behandeld, zijn onder andere:

- Maken van onderwerpen en abonnementen 
- Maken van abonnementsfilters 
- Verzenden van berichten naar een onderwerp 
- Ontvangen van berichten van een abonnement
- Verwijderen van onderwerpen en abonnementen 

Zie voor meer informatie over onderwerpen en abonnementen [Vervolgstappen](#next-steps) sectie.

[!INCLUDE [howto-service-bus-topics](../../includes/howto-service-bus-topics.md)]

## <a name="create-a-nodejs-application"></a>Een Node.js-toepassing maken
Maak een lege Node.js-toepassing. Zie voor instructies over het maken van een Node.js-toepassing [maken en implementeren van een Node.js-toepassing naar een Azure-website], [Node.js-Cloudservice] [ Node.js Cloud Service] met behulp van Windows PowerShell of website met WebMatrix.

## <a name="configure-your-application-to-use-service-bus"></a>Uw toepassing configureren voor het gebruik van Service Bus
Voor het gebruik van Service Bus de Node.js-Azure-pakket te downloaden. Dit pakket bevat een set van bibliotheken die met de Service Bus REST-services communiceren.

### <a name="use-node-package-manager-npm-to-obtain-the-package"></a>Knooppunt Package Manager (NPM) gebruiken om het pakket te verkrijgen
1. Open een opdrachtregelinterface zoals **PowerShell** (Windows), **Terminal** (Mac) of **Bash** (Unix).
2. Navigeer naar de map waar u uw voorbeeldtoepassing gemaakt.
3. Type **npm installeren azure** in het opdrachtvenster die moet resulteren in de volgende uitvoer:

   ```
       azure@0.7.5 node_modules\azure
   ├── dateformat@1.0.2-1.2.3
   ├── xmlbuilder@0.4.2
   ├── node-uuid@1.2.0
   ├── mime@1.2.9
   ├── underscore@1.4.4
   ├── validator@1.1.1
   ├── tunnel@0.0.2
   ├── wns@0.5.3
   ├── xml2js@0.2.7 (sax@0.5.2)
   └── request@2.21.0 (json-stringify-safe@4.0.0, forever-agent@0.5.0, aws-sign@0.3.0, tunnel-agent@0.3.0, oauth-sign@0.3.0, qs@0.6.5, cookie-jar@0.3.0, node-uuid@1.4.0, http-signature@0.9.11, form-data@0.0.8, hawk@0.13.1)
   ```
3. U kunt handmatig uitvoeren de **ls** opdracht om te controleren of een **knooppunt\_modules** map is gemaakt. In deze map te zoeken naar de **azure** , dit pakket de bibliotheken die u nodig hebt bevat voor toegang tot de Service Bus-onderwerpen.

### <a name="import-the-module"></a>Importeer de module
Het volgende in Kladblok of een andere teksteditor toevoegen aan de bovenkant van de **server.js** -bestand van de toepassing:

```javascript
var azure = require('azure');
```

### <a name="set-up-a-service-bus-connection"></a>Een Service Bus-verbinding instellen
De Azure-module leest de omgevingsvariabele `AZURE_SERVICEBUS_CONNECTION_STRING` voor de verbindingsreeks die u uit de vorige stap hebt verkregen, ' de referenties ophalen." Als deze omgevingsvariabele niet is ingesteld, moet u de accountgegevens opgeven bij het aanroepen van `createServiceBusService`.

Zie voor een voorbeeld van de omgevingsvariabelen instellen voor een Azure Cloud Service [Node.js-Cloudservice met Storage][Node.js Cloud Service with Storage].



## <a name="create-a-topic"></a>Een onderwerp maken
De **ServiceBusService** object kunt u werken met onderwerpen. De volgende code maakt een **ServiceBusService** object. Voeg deze toe aan de bovenkant van de **server.js** bestand na de instructie voor het importeren van de azure-module:

```javascript
var serviceBusService = azure.createServiceBusService();
```

Als u aanroept `createTopicIfNotExists` op de **ServiceBusService** object, het opgegeven onderwerp geretourneerd (indien aanwezig) of een nieuw onderwerp met de opgegeven naam is gemaakt. De volgende code gebruikt `createTopicIfNotExists` maken of verbinding maken met het onderwerp met de naam `MyTopic`:

```javascript
serviceBusService.createTopicIfNotExists('MyTopic',function(error){
    if(!error){
        // Topic was created or exists
        console.log('topic created or exists.');
    }
});
```

De `createServiceBusService` methode biedt ook ondersteuning voor extra opties waarmee u kunt de standaardinstellingen voor onderwerp zoals bericht of grootte van het maximum aantal onderwerp negeren. 

Het volgende voorbeeld wordt de grootte van het maximum aantal onderwerp ingesteld op 5 GB met een levensduur van één minuut:

```javascript
var topicOptions = {
        MaxSizeInMegabytes: '5120',
        DefaultMessageTimeToLive: 'PT1M'
    };

serviceBusService.createTopicIfNotExists('MyTopic', topicOptions, function(error){
    if(!error){
        // topic was created or exists
    }
});
```

### <a name="filters"></a>Filters
Optionele filteren bewerkingen kunnen worden toegepast op de bewerkingen die worden uitgevoerd met behulp van **ServiceBusService**. Bewerkingen voor het filteren kunt opnemen logboekregistratie, automatisch opnieuw, enzovoort. Filters zijn objecten die een methode met de handtekening implementeren:

```javascript
function handle (requestOptions, next)
```

Na het uitvoeren van voorverwerking van de aanvraag-opties, de methode roept `next`, en geeft een retouraanroep met de volgende handtekening:

```javascript
function (returnObject, finalCallback, next)
```

In deze retouraanroep en na de verwerking de `returnObject` (de reactie van de aanvraag naar de server), de callback moet de volgende aanroepen (indien aanwezig) om door te gaan met het verwerken van andere filters of aanroepen `finalCallback` naar einde van het service-aanroepen.

Twee filters die Pogingslogica implementeren zijn opgenomen in de Azure SDK voor Node.js, **ExponentialRetryPolicyFilter** en **LinearRetryPolicyFilter**. De volgende code maakt een **ServiceBusService** -object dat gebruikmaakt van de **ExponentialRetryPolicyFilter**:

```javascript
var retryOperations = new azure.ExponentialRetryPolicyFilter();
var serviceBusService = azure.createServiceBusService().withFilter(retryOperations);
```

## <a name="create-subscriptions"></a>Abonnementen maken
Ook onderwerpabonnementen worden gemaakt met de **ServiceBusService** object. Abonnementen kunnen met de naam zijn, en een optioneel filter waarmee de verzameling berichten in de virtuele wachtrij van het abonnement wordt beperkt.

> [!NOTE]
> Abonnementen worden permanent wanneer beide ze of het onderwerp zijn gekoppeld, worden verwijderd. Als uw toepassing bevat de logica voor het maken van een abonnement, moet deze eerst controleren of het abonnement met behulp van bestaat de `getSubscription` methode.
>
>

### <a name="create-a-subscription-with-the-default-matchall-filter"></a>Een abonnement maken met het standaardfilter (MatchAll)
De **MatchAll** filter is het standaardfilter dat wordt gebruikt wanneer een abonnement wordt gemaakt. Bij gebruik van het **MatchAll**-filter worden alle berichten die naar het onderwerp worden gepubliceerd, in de virtuele wachtrij van het abonnement geplaatst. Het volgende voorbeeld maakt u een abonnement met de naam AllMessages en gebruikt de standaardinstallatielocatie **MatchAll** filter.

```javascript
serviceBusService.createSubscription('MyTopic','AllMessages',function(error){
    if(!error){
        // subscription created
    }
});
```

### <a name="create-subscriptions-with-filters"></a>Abonnementen met filters maken
U kunt ook filters maken waarmee u bereik dat berichten worden verzonden naar een onderwerp binnen een bepaald onderwerpabonnement weergegeven.

Het meest flexibele type filter dat wordt ondersteund door abonnementen is de **SqlFilter**, waarmee een subset van SQL92 wordt geïmplementeerd implementeert. SQL-filters worden uitgevoerd voor de eigenschappen van de berichten die worden gepubliceerd naar het onderwerp. Raadpleeg voor meer informatie over de expressies die kunnen worden gebruikt met een SQL-filter, de [SqlFilter.SqlExpression] [ SqlFilter.SqlExpression] syntaxis.

Filters kunnen worden toegevoegd aan een abonnement met behulp van de `createRule` methode van de **ServiceBusService** object. Deze methode kunt u nieuwe filters toevoegen aan een bestaand abonnement.

> [!NOTE]
> Omdat het standaardfilter automatisch op alle nieuwe abonnementen toegepast wordt, moet u eerst het standaardfilter verwijderen of de **MatchAll** overschrijft alle andere filters die u kunt opgeven. U kunt de standaardregel verwijderen met behulp van de `deleteRule` methode van de **ServiceBusService** object.
>
>

Het volgende voorbeeld wordt een abonnement genaamd `HighMessages` met een **SqlFilter** die alleen berichten selecteert die een aangepaste `messagenumber` eigenschap groter is dan 3:

```javascript
serviceBusService.createSubscription('MyTopic', 'HighMessages', function (error){
    if(!error){
        // subscription created
        rule.create();
    }
});
var rule={
    deleteDefault: function(){
        serviceBusService.deleteRule('MyTopic',
            'HighMessages',
            azure.Constants.ServiceBusConstants.DEFAULT_RULE_NAME,
            rule.handleError);
    },
    create: function(){
        var ruleOptions = {
            sqlExpressionFilter: 'messagenumber > 3'
        };
        rule.deleteDefault();
        serviceBusService.createRule('MyTopic',
            'HighMessages',
            'HighMessageFilter',
            ruleOptions,
            rule.handleError);
    },
    handleError: function(error){
        if(error){
            console.log(error)
        }
    }
}
```

Op deze manier in het volgende voorbeeld wordt een abonnement genaamd `LowMessages` met een **SqlFilter** die alleen berichten selecteert die een `messagenumber` eigenschap kleiner dan of gelijk aan 3:

```javascript
serviceBusService.createSubscription('MyTopic', 'LowMessages', function (error){
    if(!error){
        // subscription created
        rule.create();
    }
});
var rule={
    deleteDefault: function(){
        serviceBusService.deleteRule('MyTopic',
            'LowMessages',
            azure.Constants.ServiceBusConstants.DEFAULT_RULE_NAME,
            rule.handleError);
    },
    create: function(){
        var ruleOptions = {
            sqlExpressionFilter: 'messagenumber <= 3'
        };
        rule.deleteDefault();
        serviceBusService.createRule('MyTopic',
            'LowMessages',
            'LowMessageFilter',
            ruleOptions,
            rule.handleError);
    },
    handleError: function(error){
        if(error){
            console.log(error)
        }
    }
}
```

Wanneer is nu een bericht verzonden naar `MyTopic`, wordt deze worden bezorgd bij ontvangers die zijn geabonneerd op de `AllMessages` onderwerpabonnement en selectief bezorgd bij ontvangers die zijn geabonneerd op de `HighMessages` en `LowMessages` onderwerpabonnementen (afhankelijk van inhoud van het bericht).

## <a name="how-to-send-messages-to-a-topic"></a>Het verzenden van berichten naar een onderwerp
Als u wilt een bericht verzendt naar een Service Bus-onderwerp, de toepassing moet gebruiken de `sendTopicMessage` methode van de **ServiceBusService** object.
Berichten die worden verzonden naar Service Bus-onderwerpen zijn **BrokeredMessage** objecten.
**BrokeredMessage** objecten hebben een aantal standaardeigenschappen (zoals `Label` en `TimeToLive`), een woordenlijst die wordt gebruikt om aangepaste toepassingsspecifieke eigenschappen te bewaren en een hoofdtekst met tekenreeksen. Een toepassing kunt instellen in de hoofdtekst van het bericht voor het doorgeven van een tekenreekswaarde naar het `sendTopicMessage` en alle vereiste standaardeigenschappen standaardwaarden worden ingevuld.

Het volgende voorbeeld toont hoe vijf testberichten naar verzendt `MyTopic`. De `messagenumber` eigenschapswaarde van elk bericht varieert op de herhaling van de lus (deze bepaalt welke abonnementen ontvangen):

```javascript
var message = {
    body: '',
    customProperties: {
        messagenumber: 0
    }
}

for (i = 0;i < 5;i++) {
    message.customProperties.messagenumber=i;
    message.body='This is Message #'+i;
    serviceBusService.sendTopicMessage(topic, message, function(error) {
      if (error) {
        console.log(error);
      }
    });
}
```

Service Bus-onderwerpen ondersteunen een maximale grootte van 256 kB in de [Standard-laag](service-bus-premium-messaging.md) en 1 MB in de [Premium-laag](service-bus-premium-messaging.md). De koptekst, die de standaard- en aangepaste toepassingseigenschappen bevat, kan maximaal 64 kB groot zijn. Er is geen limiet voor het aantal berichten in een onderwerp, maar er is een limiet op de totale grootte van de berichten in een onderwerp. De grootte van het onderwerp wordt gedefinieerd tijdens het maken, met een bovengrens van 5 GB.

## <a name="receive-messages-from-a-subscription"></a>Berichten ontvangen van een abonnement
Berichten worden ontvangen van een abonnement met de `receiveSubscriptionMessage` methode op de **ServiceBusService** object. Standaard berichten uit het abonnement verwijderd als ze worden gelezen. U kunt echter de optionele parameter instellen `isPeekLock` naar **true** (peek) lezen en vergrendelen van het bericht zonder het te verwijderen uit het abonnement.

Het standaardgedrag van lezen van en het bericht is verwijderd als onderdeel van de bewerking receive is het eenvoudigste model en werkt het beste voor scenario's waarin een toepassing kan tolereren niet verwerken van een bericht bij een storing. Om dit probleem te begrijpen, stelt u zich een scenario waarin de consument de receive-aanvraag indient en vervolgens vastloopt voordat het wordt verwerkt. Omdat Service Bus het bericht als verbruikt heeft gemarkeerd, klikt u vervolgens wanneer de toepassing opnieuw wordt opgestart en het verbruik van berichten opnieuw begint, ontbreekt het bericht dat voor het vastlopen is verbruikt.

Als de `isPeekLock` parameter is ingesteld op **true**, wordt het ontvangen, wordt een bewerking met twee fasen, waardoor er mogelijk worden ondersteuning voor toepassingen die gemiste berichten kunnen tolereren. Wanneer Service Bus een aanvraag ontvangt, het vindt het volgende bericht te gebruiken, wordt vergrendeld om te voorkomen dat andere consumenten het ontvangen en naar de toepassing wordt geretourneerd.
Nadat de toepassing het bericht verwerkt (of veilig heeft opgeslagen voor toekomstige verwerking), is de tweede fase van het ontvangstproces voltooid door het aanroepen van **deleteMessage** methode, en wordt doorgegeven als parameter verwijderen. De **deleteMessage** methode wordt het bericht gemarkeerd als verbruikt en wordt deze verwijderd uit het abonnement.

Het volgende voorbeeld laat zien hoe berichten kunnen worden ontvangen en verwerkt met behulp `receiveSubscriptionMessage`. In het voorbeeld eerst ontvangt en wordt een bericht wordt verwijderd uit het abonnement 'LowMessages' en vervolgens een bericht ontvangt van de 'HighMessages' abonnement met behulp `isPeekLock` is ingesteld op true. Vervolgens wordt verwijderd het bericht met `deleteMessage`:

```javascript
serviceBusService.receiveSubscriptionMessage('MyTopic', 'LowMessages', function(error, receivedMessage){
    if(!error){
        // Message received and deleted
        console.log(receivedMessage);
    }
});
serviceBusService.receiveSubscriptionMessage('MyTopic', 'HighMessages', { isPeekLock: true }, function(error, lockedMessage){
    if(!error){
        // Message received and locked
        console.log(lockedMessage);
        serviceBusService.deleteMessage(lockedMessage, function (deleteError){
            if(!deleteError){
                // Message deleted
                console.log('message has been deleted.');
            }
        })
    }
});
```

## <a name="how-to-handle-application-crashes-and-unreadable-messages"></a>Het vastlopen van de toepassing en onleesbare berichten afhandelen
Service Bus biedt functionaliteit om netjes te herstellen bij fouten in uw toepassing of problemen bij het verwerken van een bericht. Als een ontvangende toepassing kan niet worden verwerkt het bericht om een bepaalde reden, dan kan worden aangeroepen de `unlockMessage` methode op de **ServiceBusService** object. Deze methode zorgt ervoor dat Service Bus het bericht in het abonnement ontgrendelt en beschikbaar gesteld moet opnieuw worden ontvangen. In dit exemplaar, ofwel door dezelfde consumerende toepassing of door een andere consumerende toepassing.

Er is een time-out gekoppeld aan een bericht in het abonnement is vergrendeld. Als de toepassing niet kan verwerken van het bericht voordat de time-out van de vergrendeling verloopt (bijvoorbeeld als de toepassing vastloopt), vervolgens ontgrendelt Service Bus het bericht automatisch en wordt het beschikbaar worden om opnieuw te ontvangen.

In het geval van de toepassing is vastgelopen na het verwerken van het bericht, maar voordat de `deleteMessage` methode wordt aangeroepen, wordt het bericht is opnieuw bezorgd bij de toepassing opnieuw wordt gestart. Dit gedrag wordt vaak genoemd *tenminste één keer worden verwerkt*. Dat wil zeggen dat elk bericht ten minste één keer wordt verwerkt, maar in sommige situaties hetzelfde bericht opnieuw kan worden bezorgd. Als het scenario kan geen dubbele verwerking tolereren, moet u logica toevoegen aan uw toepassing levering van dubbele berichten afhandelen. U kunt de **MessageId** eigenschap van het bericht dat gelijk blijft bij meerdere bezorgingspogingen.

## <a name="delete-topics-and-subscriptions"></a>Onderwerpen en abonnementen verwijderen
Onderwerpen en abonnementen worden permanent en expliciet moet worden verwijderd via de [Azure-portal] [ Azure portal] of via een programma.
Het volgende voorbeeld laat zien hoe u het onderwerp met de naam `MyTopic`:

```javascript
serviceBusService.deleteTopic('MyTopic', function (error) {
    if (error) {
        console.log(error);
    }
});
```

Als een onderwerp wordt verwijderd, worden ook alle abonnementen verwijderd die zijn geregistreerd bij het onderwerp. Abonnementen kunnen ook afzonderlijk worden verwijderd. Het volgende voorbeeld laat zien hoe u een abonnement met de naam `HighMessages` van de `MyTopic` onderwerp:

```javascript
serviceBusService.deleteSubscription('MyTopic', 'HighMessages', function (error) {
    if(error) {
        console.log(error);
    }
});
```

## <a name="next-steps"></a>Volgende stappen
Nu u de basisprincipes van Service Bus-onderwerpen hebt geleerd, volgt u deze koppelingen voor meer informatie.

* Zie [wachtrijen, onderwerpen en abonnementen][Queues, topics, and subscriptions].
* API-naslaginformatie voor [SqlFilter][SqlFilter].
* Ga naar de [Azure SDK voor knooppunt] [ Azure SDK for Node] opslagplaats op GitHub.

[Azure SDK for Node]: https://github.com/Azure/azure-sdk-for-node
[Azure portal]: https://portal.azure.com
[SqlFilter.SqlExpression]: service-bus-messaging-sql-filter.md
[Queues, topics, and subscriptions]: service-bus-queues-topics-subscriptions.md
[SqlFilter]: /dotnet/api/microsoft.servicebus.messaging.sqlfilter
[Node.js Cloud Service]: ../cloud-services/cloud-services-nodejs-develop-deploy-app.md
[Maken en implementeren van een Node.js-toepassing naar een Azure-website]: ../app-service/app-service-web-get-started-nodejs.md
[Node.js Cloud Service with Storage]: ../cloud-services/cloud-services-nodejs-develop-deploy-app.md

