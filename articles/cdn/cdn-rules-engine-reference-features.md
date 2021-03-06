---
title: Azure CDN regels engine functies | Microsoft Docs
description: Documentatie bij Azure CDN regels engine functies.
services: cdn
documentationcenter: ''
author: dksimpson
manager: cfowler
editor: ''
ms.assetid: 669ef140-a6dd-4b62-9b9d-3f375a14215e
ms.service: cdn
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/09/2018
ms.author: v-deasim
ms.openlocfilehash: e1e002b51aa5a93e7fcc800f5cf48ac401c5cb2d
ms.sourcegitcommit: 909469bf17211be40ea24a981c3e0331ea182996
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/10/2018
---
# <a name="azure-cdn-rules-engine-features"></a>Functies in de engine Azure CDN-regels
Dit artikel vindt u gedetailleerde beschrijvingen van de beschikbare functies voor Azure Content Delivery Network (CDN) [regelengine](cdn-rules-engine.md).

Het derde deel van een regel is de functie. Een functie bepaalt het type actie dat wordt toegepast op het aanvraagtype die wordt geïdentificeerd door een set voorwaarden van de overeenkomst.

## <a name="access-features"></a>Access-functies

Deze functies zijn ontworpen om toegang tot inhoud beheren.

Naam | Doel
-----|--------
[(403) toegang weigeren](#deny-access-403) | Hiermee bepaalt u of alle aanvragen zijn afgewezen met een 403-verboden-antwoord.
[Token Auth](#token-auth) | Bepaalt of verificatie op basis van het Token wordt toegepast op een aanvraag.
[Token Auth DOS-Code](#token-auth-denial-code) | Bepaalt het type van de reactie die aan een gebruiker worden geretourneerd wanneer een aanvraag wordt geweigerd vanwege verificatie op basis van tokens.
[Token Auth hoofdlettergevoeligheid niet van belang URL](#token-auth-ignore-url-case) | Bepaalt of de URL vergelijkingen aangebracht door verificatie op basis van tokens hoofdlettergevoelig zijn.
[Token Auth-Parameter](#token-auth-parameter) | Hiermee wordt bepaald of de verificatie op basis van tokens querytekenreeksparameter moet worden gewijzigd.


## <a name="caching-features"></a>Functies in cache opslaan

Deze functies zijn ontworpen om aan te passen wanneer en hoe inhoud in cache wordt opgeslagen.

Naam | Doel
-----|--------
[Bandbreedte-Parameters](#bandwidth-parameters) | Bepaalt of bandbreedte bandbreedteregeling parameters (bijvoorbeeld ec_rate en ec_prebuf) actief zijn.
[Bandbreedtebeperking](#bandwidth-throttling) | Beperkt de bandbreedte voor de respons van de punt-of-presence (POP).
[De Bypass-Cache](#bypass-cache) | Hiermee wordt bepaald of de aanvraag moet overslaan.
[Cache-Control Header behandeling](#cache-control-header-treatment) | Hiermee bepaalt u het genereren van `Cache-Control` headers door de pop-server als onderdeel van de externe maximumleeftijd actief is.
[Cache-sleutel queryreeks](#cache-key-query-string) | Hiermee wordt bepaald of de cachesleutel opnemen en uitsluiten van queryreeksparameters die zijn gekoppeld aan een aanvraag.
[Cache-sleutel opnieuw schrijven](#cache-key-rewrite) | Herschrijft de cache-sleutel die is gekoppeld aan een aanvraag.
[Voltooien van de opvulling van de Cache](#complete-cache-fill) | Hiermee bepaalt u wat er gebeurt wanneer een aanvraag resulteert in een gedeeltelijke Cachemisser op een pop-server.
[Bestandstypen comprimeren](#compress-file-types) | Hiermee definieert u de bestandsindelingen voor de bestanden die zijn gecomprimeerd op de server.
[Standaard interne-maximumleeftijd](#default-internal-max-age) | Bepaalt het standaardinterval voor de maximale leeftijd voor pop-server naar de oorsprong server cache hervalidatie.
[Koptekst behandeling verloopt](#expires-header-treatment) | Hiermee bepaalt u het genereren van `Expires` headers door een pop-server als de functie externe maximumleeftijd actief is.
[Externe maximumleeftijd](#external-max-age) | Bepaalt het maximumleeftijd interval voor de browser opnieuw valideren pop-server.
[Interne maximumleeftijd forceren](#force-internal-max-age) | Bepaalt het maximumleeftijd interval voor POP naar de oorsprong server cache hervalidatie.
[H.264-ondersteuning (http-progressief downloaden)](#h264-support-http-progressive-download) | Bepaalt de soorten H.264 bestandsindelingen die kunnen worden gebruikt om inhoud streamen.
[EEr No-Cache-aanvraag](#honor-no-cache-request) | Hiermee wordt bepaald of een HTTP-client Nee-cache-aanvragen worden doorgestuurd naar de oorspronkelijke server.
[Negeren oorsprong No-Cache](#ignore-origin-no-cache) | Hiermee wordt bepaald of de CDN bepaalde richtlijnen geleverd van een bronserver negeert.
[Ongeldig bereiken negeren](#ignore-unsatisfiable-ranges) | Bepaalt de reactie die aan clients worden geretourneerd wanneer een aanvraag statuscode 416 aangevraagd bereik niet geldig genereert.
[Interne Max-verouderd](#internal-max-stale) | Bepaalt hoe lang voorbij de verlooptijd van de normale een activum in de cache kan worden geleverd vanuit een POP wanneer de pop-server niet kan valideren van de cache asset met de oorspronkelijke server.
[Gedeeltelijke Cache delen](#partial-cache-sharing) | Hiermee wordt bepaald of een aanvraag deels in cache opgeslagen inhoud kan genereren.
[De inhoud in cache prevalidate](#prevalidate-cached-content) | Hiermee wordt bepaald of de inhoud in cache in aanmerking komen voor vroege opnieuw te worden gevalideerd voordat de TTL verloopt.
[Vernieuwen van nul bytes cachebestanden](#refresh-zero-byte-cache-files) | Hiermee wordt bepaald hoe van de client van een HTTP-aanvraag voor een asset 0-byte-cache wordt verwerkt door de POP's.
[Statuscodes voor caching geschikte instellen](#set-cacheable-status-codes) | Definieert de set van statuscodes die tot de inhoud in cache leiden kunnen.
[Verouderde Contentlevering bij fout](#stale-content-delivery-on-error) | Bepaalt of verlopen in de cache inhoud wordt geleverd als een fout optreedt tijdens de hervalidatie van de cache of bij het ophalen van de aangevraagde inhoud op de bronserver van de klant.
[Verouderde tijdens Revalidate](#stale-while-revalidate) | Verbetert de prestaties doordat de POP's te bedienen verouderde client aan de aanvrager terwijl hervalidatie plaatsvindt.

## <a name="comment-feature"></a>Optie Opmerking

Deze functie is ontworpen om aanvullende gegevens binnen een regel.

Naam | Doel
-----|--------
[Opmerking](#comment) | Hiermee kunt een opmerking in een regel worden toegevoegd.
 
## <a name="header-features"></a>Header-functies

Deze functies zijn ontworpen om toevoegen, wijzigen of verwijderen van headers van de aanvraag of antwoord.

Naam | Doel
-----|--------
[Leeftijd antwoordheader](#age-response-header) | Hiermee wordt bepaald of een antwoordheader leeftijd is opgenomen in het antwoord verzonden naar de aanvrager.
[Fouten opsporen in Cache antwoordheaders](#debug-cache-response-headers) | Hiermee wordt bepaald of een antwoord antwoordheader van de X-EC-Debug die informatie over het cachebeleid voor de aangevraagde asset bevat kan bevatten.
[De aanvraagheader Client wijzigen](#modify-client-request-header) | Overschreven, wordt toegevoegd of verwijderd van een koptekst van een aanvraag.
[Client-antwoordheader wijzigen](#modify-client-response-header) | Overschreven, wordt toegevoegd of verwijderd van een koptekst van een antwoord.
[Stel aangepaste Header voor Client-IP](#set-client-ip-custom-header) | Kan het IP-adres van de aanvragende client moet worden toegevoegd aan de aanvraag als een aangepaste aanvraagheader.


## <a name="logging-features"></a>Logboekregistratiefuncties

Deze functies zijn ontworpen om aan te passen de gegevens die zijn opgeslagen in onbewerkte logboekbestanden.

Naam | Doel
-----|--------
[Aangepast logboekveld 1](#custom-log-field-1) | Bepaalt de indeling en de inhoud die is toegewezen aan het aangepaste logboekveld in een onbewerkte logboekbestand.
[Queryreeks logboek](#log-query-string) | Hiermee wordt bepaald of een queryreeks samen met de URL in Logboeken worden opgeslagen.


<!---
## Optimize

These features determine whether a request will undergo the optimizations provided by Edge Optimizer.

Name | Purpose
-----|--------
Edge Optimizer | Determines whether Edge Optimizer can be applied to a request.
Edge Optimizer – Instantiate Configuration | Instantiates or activates the Edge Optimizer configuration associated with a site.

### Edge Optimizer
**Purpose:** Determines whether Edge Optimizer can be applied to a request.

If this feature has been enabled, then the following criteria must also be met before the request will be processed by Edge Optimizer:

- The requested content must use an edge CNAME URL.
- The edge CNAME referenced in the URL must correspond to a site whose configuration has been activated in a rule.

This feature requires the ADN platform and the Edge Optimizer feature.

Value|Result
-|-
Enabled|Indicates that the request is eligible for Edge Optimizer processing.
Disabled|Restores the default behavior. The default behavior is to deliver content over the ADN platform without any additional processing.

**Default Behavior:** Disabled
 

### Edge Optimizer - Instantiate Configuration
**Purpose:** Instantiates or activates the Edge Optimizer configuration associated with a site.

This feature requires the ADN platform and the Edge Optimizer feature.

Key information:

- Instantiation of a site configuration is required before requests to the corresponding edge CNAME can be processed by Edge Optimizer.
- This instantiation only needs to be performed a single time per site configuration. A site configuration that has been instantiated will remain in that state until the Edge Optimizer – Instantiate Configuration feature that references it is removed from the rule.
- The instantiation of a site configuration does not mean that all requests to the corresponding edge CNAME will automatically be processed by Edge Optimizer. The Edge Optimizer feature determines whether an individual request will be processed.

If the desired site does not appear in the list, then you should edit its configuration and verify that the Active option has been marked.

**Default Behavior:** Site configurations are inactive by default.
--->

## <a name="origin-features"></a>Functies van de oorsprong

Deze functies zijn ontworpen om te bepalen hoe de CDN communiceert met een bronserver.

Naam | Doel
-----|--------
[Maximum aantal keepalive-aanvragen](#maximum-keep-alive-requests) | Definieert het maximum aantal aanvragen voor een Keep-Alive verbinding wordt gesloten.
[Speciale proxy-kopteksten](#proxy-special-headers) | Definieert de set van CDN-specifieke aanvraagheaders die van een POP-worden doorgestuurd naar een bronserver.


## <a name="specialty-features"></a>Speciale functies

Deze functies bieden geavanceerde functionaliteit voor ervaren gebruikers.

Naam | Doel
-----|--------
[Caching geschikte HTTP-methoden](#cacheable-http-methods) | Bepaalt de set met extra HTTP-methoden die in de cache kan worden opgeslagen in het netwerk.
[Grootte van standaardberichthoofdtekst voor caching geschikte aanvraag](#cacheable-request-body-size) | Hiermee definieert u de drempelwaarde voor het bepalen of een POST-antwoord in cache.
[Variabele van de gebruiker](#user-variable) | Alleen voor intern gebruik.

 
## <a name="url-features"></a>URL-functies

Deze functies kunnen een aanvraag moet worden omgeleid of herschreven naar een andere URL.

Naam | Doel
-----|--------
[Omleidingen volgen](#follow-redirects) | Hiermee bepaalt u of aanvragen kunnen worden omgeleid naar de hostnaam die is gedefinieerd in de locatieheader geretourneerd door een klant-bronserver.
[URL-omleiding](#url-redirect) | U wordt doorgestuurd aanvragen via de locatie-header.
[Herschrijven van URL](#url-rewrite)  | Herschrijft de aanvraag-URL.



## <a name="azure-cdn-rules-engine-features-reference"></a>Azure CDN regels engine functies verwijzing

---
### <a name="age-response-header"></a>Leeftijd antwoordheader
**Doel**: Hiermee wordt bepaald of een antwoordheader leeftijd is opgenomen in het antwoord verzonden naar de aanvrager.
Waarde|Resultaat
--|--
Ingeschakeld | De antwoordheader van de leeftijd is opgenomen in het antwoord verzonden naar de aanvrager.
Uitgeschakeld | De antwoordheader van de leeftijd is uitgesloten van het antwoord verzonden naar de aanvrager.

**Standaardgedrag**: uitgeschakeld.

[Terug naar boven](#azure-cdn-rules-engine-features)

</br>

---
### <a name="bandwidth-parameters"></a>Bandbreedte-Parameters
**Doel:** bepaalt of bandbreedte bandbreedteregeling parameters (bijvoorbeeld ec_rate en ec_prebuf) actief zijn.

Bandbreedte, snelheidsbeperking parameters kunt u bepalen of de overdrachtssnelheid van gegevens voor de aanvraag van een client zijn beperkt tot een aangepaste frequentie.

Waarde|Resultaat
--|--
Ingeschakeld|Hiermee kunt u de POP's te voldoen aan aanvragen voor bandbreedteregeling.
Uitgeschakeld|Zorgt ervoor dat de POP's voor het negeren van parameters voor bandbreedteregeling. De gevraagde inhoud normaal gesproken wordt geleverd (dat wil zeggen, zonder bandbreedtebeperking).

**Standaardgedrag:** ingeschakeld.
 
[Terug naar boven](#azure-cdn-rules-engine-features)

</br>

---
### <a name="bandwidth-throttling"></a>Bandbreedtebeperking
**Doel:** beperkt de bandbreedte voor de respons van de POP's.

Beide van de volgende opties moeten worden gedefinieerd voor het correct ingesteld bandbreedtebeperking.

Optie|Beschrijving
--|--
KB per seconde|Deze optie instelt op de maximale bandbreedte (in Kb per seconde) die kan worden gebruikt voor het leveren van het antwoord.
Prebuf seconden|Deze optie instelt op het aantal seconden voor de POP's te wachten tot de bandbreedte wordt beperkt. Het doel van deze periode van onbeperkte bandbreedte is om te voorkomen dat een mediaspeler momenteel haperend of buffering problemen als gevolg van bandbreedtebeperking.

**Standaardgedrag:** uitgeschakeld.

[Terug naar boven](#azure-cdn-rules-engine-features)

</br>

---
### <a name="bypass-cache"></a>De Bypass-Cache
**Doel:** bepaalt of de aanvraag moet overslaan.

Waarde|Resultaat
--|--
Ingeschakeld|Zelfs als de inhoud is eerder in de cache op de POP's, zorgt ervoor dat alle aanvragen aan niet op de bronserver.
Uitgeschakeld|Zorgt ervoor dat pop's cache activa volgens het cachebeleid dat is gedefinieerd in de antwoordheaders.

**Standaardgedrag:**

- **Grote HTTP:** uitgeschakeld

<!---
- **ADN:** Enabled

--->

[Terug naar boven](#azure-cdn-rules-engine-features)

</br>

---
### <a name="cacheable-http-methods"></a>Caching geschikte HTTP-methoden
**Doel:** bepaalt de set met extra HTTP-methoden die in de cache kan worden opgeslagen in het netwerk.

Belangrijke informatie:

- Deze functie wordt ervan uitgegaan dat GET antwoorden moeten altijd opslaan in de cache. Als gevolg hiervan moet HALEN HTTP-methode niet worden opgenomen bij het instellen van deze functie.
- Deze functie ondersteunt alleen HTTP POST-methode. POST antwoord in cache opslaan door deze functie in te stellen op `POST`.
- Standaard alleen aanvragen waarvan hoofdtekst kleiner dan 14 Kb is in cache zijn opgeslagen. De caching geschikte aanvraag hoofdtekst grootte-functie gebruiken om de maximale aanvraag hoofdtekst grootte instellen.

**Standaardgedrag:** alleen GET-reacties in de cache zijn opgeslagen.

[Terug naar boven](#azure-cdn-rules-engine-features)

</br>

---
### <a name="cacheable-request-body-size"></a>Grootte van standaardberichthoofdtekst voor caching geschikte aanvraag
**Doel:** definieert de drempelwaarde voor het bepalen of een POST-antwoord in cache.

Deze drempelwaarde wordt bepaald door het opgeven van een maximale aanvraag hoofdtekst grootte. Dat aanvragen met een grotere aanvraagtekst niet in cache zijn opgeslagen.

Belangrijke informatie:

- Deze functie is alleen van toepassing wanneer POST antwoorden in aanmerking voor het opslaan in cache komen. Gebruik de functie voor methoden van caching geschikte HTTP POST-aanvraag cache inschakelen.
- De aanvraagtekst voor rekening gehouden met:
    - x-1-800-www-Dell-form-urlencoded waarden
    - Een unieke Cachesleutel gezorgd
- Voor het definiëren van een grote maximale aanvraag hoofdtekst mogelijk gegevens levering van de prestaties beïnvloeden.
    - **Aanbevolen waarde:** 14 Kb
    - **Minimumwaarde:** 1 Kb

**Standaardgedrag:** 14 Kb

[Terug naar boven](#azure-cdn-rules-engine-features)

</br>

---
### <a name="cache-control-header-treatment"></a>Cache-Control Header behandeling
**Doel:** bepaalt het genereren van `Cache-Control` headers door de pop-server als de externe maximumleeftijd functie actief is.

De eenvoudigste manier om dit type configuratie is de externe maximumleeftijd en de functies van de Cache-Control Header behandeling plaatsen in dezelfde instructie.

Waarde|Resultaat
--|--
Overschrijven|Zorgt ervoor dat de volgende acties uitgevoerd:<br/> -Overschrijft de `Cache-Control` header die worden gegenereerd door de bronserver. <br/>-Voegt de `Cache-Control` header geproduceerd door de functie externe maximumleeftijd aan het antwoord.
Pass Through|Zorgt ervoor dat de `Cache-Control` header geproduceerd door de functie externe maximumleeftijd nooit wordt toegevoegd aan het antwoord. <br/> Als de bronserver produceert een `Cache-Control` -kop, dit wordt doorgegeven via voor de eindgebruiker. <br/> Als de bronserver geen produceert een `Cache-Control` header, wordt deze optie kan ertoe leiden dat de response-header bevat geen een `Cache-Control` header.
Indien deze ontbreken toevoegen|Als een `Cache-Control` header is niet ontvangen op de bronserver en vervolgens deze optie wordt de `Cache-Control` header geproduceerd door de functie externe maximumleeftijd. Deze optie is handig om ervoor te zorgen dat alle activa zijn toegewezen een `Cache-Control` header.
Verwijderen| Deze optie zorgt ervoor dat een `Cache-Control` header is niet opgenomen in de header-reactie. Als een `Cache-Control` header is al toegewezen en vervolgens wordt deze verwijderd uit de header-reactie.

**Standaardgedrag:** overschrijven.

[Terug naar boven](#azure-cdn-rules-engine-features)

</br>

---
### <a name="cache-key-query-string"></a>Cache-sleutel queryreeks
**Doel:** bepaalt of de cache-sleutel bevat of queryreeksparameters die zijn gekoppeld aan een aanvraag sluit.

Belangrijke informatie:

- Geef een of meer query tekenreeks parameternamen en scheid elke parameternaam met een spatie.
- Deze functie bepaalt of queryreeksparameters worden opgenomen of van de cache-sleutel uitgesloten. Aanvullende informatie is beschikbaar voor elke optie in de volgende tabel.

Type|Beschrijving
--|--
 Opnemen|  Geeft aan dat elke opgegeven parameter in de cache-sleutel moet worden opgenomen. Een unieke cache-sleutel wordt gegenereerd voor elke aanvraag met een unieke waarde voor een queryreeksparameter gedefinieerd in deze functie. 
 Alle  |Hiermee wordt aangegeven dat een unieke cache-sleutel is gemaakt voor elke aanvraag naar een activum met een unieke queryreeks. Dit type configuratie wordt meestal niet aanbevolen omdat dit tot een laag percentage treffers in cache leiden kan. Een klein aantal treffers in cache verhoogt de belasting van de bronserver, omdat deze meer aanvragen moet dienen. Deze configuratie dupliceert het cachegedrag bekend als 'unieke-cache' op de pagina queryreeks opslaan in cache. 
 Uitsluiten | Geeft aan dat alleen de opgegeven parameters is uitgesloten van de cache-sleutel. Alle andere queryreeksparameters zijn opgenomen in de cache-sleutel. 
 Sluit alle  |Hiermee wordt aangegeven dat alle queryreeksparameters zijn uitgesloten van de cache-sleutel. Deze configuratie een duplicaat van de 'standaard '-cache standaard cachegedrag op de pagina queryreeks opslaan in cache.  

De regelengine voor kunt u voor het aanpassen van de manier waarop de query opslaan in cache is geïmplementeerd. U kunt bijvoorbeeld opgeven dat query opslaan in cache alleen op bepaalde locaties of bestandstypen wordt uitgevoerd.

Als u wilt de queryreeks van 'no-cache' cachegedrag op de pagina queryreeks Caching dupliceren, maakt u een regel met een URL-Query jokertekens overeen voorwaarde en een Bypass-Cache-functie. Stel de overeenkomst jokerteken voor URL-Query op een sterretje (*).

>[!IMPORTANT] 
> Als het token autorisatie voor elk willekeurig pad voor dit account is ingeschakeld, is standaard cachemodus alleen de modus die kan worden gebruikt voor het opslaan van de query-tekenreeks. Zie [Cachegedrag in Azure CDN bepalen met queryreeksen](cdn-query-string-premium.md) voor meer informatie.

#### <a name="sample-scenarios"></a>Voorbeeldscenario 's

Het volgende Voorbeeldgebruik voor deze functie biedt een voorbeeld van een aanvraag en de standaard-cache-sleutel:

- **Voorbeeld van een aanvraag:** http://wpc.0001.&lt; Domein&gt;/800001/Origin/folder/asset.htm?sessionid=1234 en taal = nl & userid = 01
- **Standaard-cache-sleutel:** /800001/Origin/folder/asset.htm

##### <a name="include"></a>Opnemen

Configuratie van de steekproef:

- **Type:** opnemen
- **Parameter (s):** taal

Dit type configuratie genereert de volgende query tekenreeks parameter cache-sleutel:

    /800001/Origin/folder/asset.htm?language=EN

##### <a name="include-all"></a>Alle

Configuratie van de steekproef:

- **Type:** alle

Dit type configuratie genereert de volgende query tekenreeks parameter cache-sleutel:

    /800001/Origin/folder/asset.htm?sessionid=1234&language=EN&userid=01

##### <a name="exclude"></a>Uitsluiten

Configuratie van de steekproef:

- **Type:** uitsluiten
- **Parameter (s):** sessionid userid

Dit type configuratie genereert de volgende query tekenreeks parameter cache-sleutel:

    /800001/Origin/folder/asset.htm?language=EN

##### <a name="exclude-all"></a>Sluit alle

Configuratie van de steekproef:

- **Type:** sluit alle

Dit type configuratie genereert de volgende query tekenreeks parameter cache-sleutel:

    /800001/Origin/folder/asset.htm

[Terug naar boven](#azure-cdn-rules-engine-features)

</br>

---
### <a name="cache-key-rewrite"></a>Cache-sleutel opnieuw schrijven
**Doel:** herschrijft de cache-sleutel die is gekoppeld aan een aanvraag.

Een cache-sleutel is het relatieve pad dat een asset voor de doeleinden van in het cachegeheugen identificeert. De servers controleren met andere woorden, de versie van een cache van een activum volgens het bijbehorende pad zoals gedefinieerd door de cachesleutel.

Deze functie configureren met het definiëren van de volgende opties:

Optie|Beschrijving
--|--
Oorspronkelijke pad| Definieer het relatieve pad naar de soorten waarvan Cachesleutel wordt herschreven aanvragen. Een relatief pad worden gedefinieerd met een base oorsprongpad selecteren en vervolgens een reguliere-expressiepatroon te definiëren.
Nieuwe pad|Definieer het relatieve pad voor de nieuwe cache-sleutel. Een relatief pad worden gedefinieerd met een base oorsprongpad selecteren en vervolgens een reguliere-expressiepatroon te definiëren. Deze relatieve pad kan dynamisch worden geconstrueerd met behulp van [HTTP variabelen](cdn-http-variables.md).
**Standaardgedrag:** Cachesleutel van een aanvraag wordt bepaald door de aanvraag-URI.

[Terug naar boven](#azure-cdn-rules-engine-features)

</br>

---
### <a name="comment"></a>Opmerking
**Doel:** kunt een opmerking in een regel worden toegevoegd.

Er wordt één gebruikt voor deze functie vindt u aanvullende informatie over het algemeen doel van een regel of waarom een bepaalde overeenkomen met de voorwaarde of functie is toegevoegd aan de regel.

Belangrijke informatie:

- Maximaal 150 tekens mag worden opgegeven.
- Gebruik alleen alfanumerieke tekens.
- Deze functie heeft geen invloed op het gedrag van de regel. Dit is alleen bedoeld voor een gebied waarin u de informatie voor toekomstig gebruik of die kan helpen bij het oplossen van de regel kunt opgeven.

[Terug naar boven](#azure-cdn-rules-engine-features)

</br>

---
### <a name="complete-cache-fill"></a>Voltooien van de opvulling van de Cache
**Doel:** bepaalt wat er gebeurt wanneer een aanvraag in een gedeeltelijke Cachemisser op een pop-server resulteert.

Een gedeeltelijke Cachemisser beschrijft de status van de cache voor een asset die niet volledig is gedownload naar een pop-server. Als een asset is slechts gedeeltelijk in cache op een pop-server, klikt u vervolgens de volgende aanvraag voor de activa doorgestuurd opnieuw met de oorspronkelijke server.
<!---
This feature is not available for the ADN platform. The typical traffic on this platform consists of relatively small assets. The size of the assets served through these platforms helps mitigate the effects of partial cache misses, since the next request will typically result in the asset being cached on that POP.

--->
Een gedeeltelijke Cachemisser treedt meestal op nadat een gebruiker een download annuleert of voor bedrijfsmiddelen die uitsluitend worden aangevraagd met behulp van HTTP-aanvragen voor bereik. Deze functie is vooral handig voor grote activa die meestal niet van begin tot eind (bijvoorbeeld video's) worden gedownload. Als gevolg hiervan is deze functie standaard ingeschakeld op het grote HTTP-platform. Het is uitgeschakeld op alle andere platforms.

Houd de standaardconfiguratie voor het HTTP-grote platform, omdat deze de belasting van de bronserver voor uw klant wordt en verhoogt de snelheid waarmee uw klanten uw inhoud downloaden.

Waarde|Resultaat
--|--
Ingeschakeld|Hiermee herstelt u het standaardgedrag. Het standaardgedrag is om af te dwingen de pop-server voor het initiëren van een op de achtergrond ophalen van de activa op de bronserver. Waarna de asset worden weergegeven in lokale cache van de pop-server.
Uitgeschakeld|Hiermee voorkomt dat een pop-server uitvoeren van een op de achtergrond ophalen voor de asset. Het resultaat is een pop-server om aan te vragen deze op de bronserver klant zorgt ervoor dat de volgende aanvraag voor de activa van die regio.

**Standaardgedrag:** ingeschakeld.

#### <a name="compatibility"></a>Compatibiliteit
Dit onderdeel kan niet worden gekoppeld aan de volgende voorwaarden van de overeenkomst vanwege de manier die in cache van welke instellingen worden bijgehouden: 
- AS-nummer
- IP-adres van client
- Cookie-Parameter
- Cookie Parameter Regex
- Land
- Apparaat
- Rand Cname
- Verwijzende domein
- Aanvraag-Header Literal
- Aanvraag-Header Regex
- Aanvraag-Header jokertekens
- Verzoekmethode
- Aanvraag-schema
- URL-Query Literal
- URL-Query Regex
- URL-Query jokertekens
- URL-queryparameter

[Terug naar boven](#azure-cdn-rules-engine-features)

</br>

---
### <a name="compress-file-types"></a>Bestandstypen comprimeren
**Doel:** worden de bestandsindelingen voor de bestanden die zijn gecomprimeerd gedefinieerd op de server.

Een bestandsindeling kan worden opgegeven met de Internet-mediatype (bijvoorbeeld Content-Type). Internet-mediatype is platformonafhankelijk metagegevens waarmee de servers voor het identificeren van de bestandsindeling van een bepaald actief. Hieronder vindt u een lijst met algemene typen voor Internet-media.

Internet-mediatype|Beschrijving
--|--
text/plain|Bestanden met tekst zonder opmaak
text/html| HTML-bestanden
text/css|Cascading stylesheets (CSS)
x-toepassing-javascript|Javascript
toepassing/javascript|Javascript
Belangrijke informatie:

- Geef meerdere mediatypen voor Internet door die begrenst elkaar met een spatie. 
- Deze functie worden alleen gecomprimeerd activa waarvan de grootte minder dan 1 MB is. Grotere activa zijn niet door de servers gecomprimeerd.
- Bepaalde typen inhoud, zoals afbeeldingen, video en audio-media-elementen (bijvoorbeeld, JPG, MP3, MP4, enzovoort), zijn al gecomprimeerd. Omdat u extra compressie op deze typen elementen geen aanzienlijk afbreuk doen bestandsgrootte, wordt het aanbevolen dat u niet de mogelijkheid compressie op deze.
- Jokertekens zoals sterretjes, worden niet ondersteund.
- Voordat u deze functie naar een regel toevoegt, zorg ervoor dat u stelt u de optie compressie uitgeschakeld op de pagina compressie voor het platform waarop deze regel wordt toegepast.

[Terug naar boven](#azure-cdn-rules-engine-features)

</br>

---
### <a name="custom-log-field-1"></a>Aangepast logboekveld 1
**Doel:** bepaalt de indeling en de inhoud die wordt toegewezen aan het aangepaste logboekveld in een onbewerkte logboekbestand.

Dit aangepaste veld kunt u bepalen welke headerwaarden aanvraag en -antwoord in de logboekbestanden worden opgeslagen.

Standaard wordt het aangepaste logboekveld 'x-ec_custom-1.' genoemd. De naam van dit veld kan worden aangepast via de pagina onbewerkte logboekinstellingen.

De indeling voor het opgeven van de aanvraag- en reactieheaders wordt als volgt gedefinieerd:

Header-Type|Indeling|Voorbeelden
-|-|-
Aanvraag-Header|%{[RequestHeader]()}[ik]() | %{Accept-Encoding}i <br/> {Referer}i <br/> % {Autorisatie} i
Antwoordkoptekst|%{[ResponseHeader]()}[o]()| %{Age}o <br/> %{Content-Type}o <br/> %{Cookie}o

Belangrijke informatie:

- Een veld van het aangepaste logboek kan bestaan uit een combinatie van header-velden en tekst zonder opmaak.
- Geldige tekens voor dit veld als volgt zijn: alfanumerieke (0-9, a-z en A-Z), streepjes, dubbele punten, puntkomma's, enkele aanhalingstekens, komma's, punten, onderstrepingstekens, gelijk tekens, haakjes, haakjes en spaties. Het percentagesymbool en de accolades zijn alleen toegestaan als gebruikt voor het opgeven van een headerveld.
- De spelling voor elk veld van de opgegeven header moet overeenkomen met de naam van de gewenste aanvraag/antwoord-header.
- Als u meerdere headers opgeven wilt, gebruikt u een scheidingsteken om aan te geven van elke koptekst. U kunt bijvoorbeeld een afkorting voor elke header:
    - AE: % {accepteren codering} i A: % {autorisatie} i CT: % {Content-Type} o 

**Standaardwaarde:** -

[Terug naar boven](#azure-cdn-rules-engine-features)

</br>

---
### <a name="debug-cache-response-headers"></a>Fouten opsporen in Cache antwoordheaders
**Doel:** bepaalt of een antwoord kunt opnemen [X-EC-Debug antwoordheaders](cdn-http-debug-headers.md), waarmee u informatie op het cachebeleid voor de aangevraagde asset.

Fouten opsporen in cacheantwoord headers worden opgenomen in het antwoord wanneer het volgende waar zijn:

- De functie antwoordheaders voor fouten opsporen in Cache is ingeschakeld op de opgegeven aanvraag.
- De opgegeven aanvraag definieert de set van foutopsporing cache antwoordheaders die wordt opgenomen in het antwoord.

Fouten opsporen in cacheantwoord headers kunnen worden aangevraagd door de volgende header en de opgegeven richtlijnen in de aanvraag, waaronder:

`X-EC-Debug: _&lt;Directive1&gt;_,_&lt;Directive2&gt;_,_&lt;DirectiveN&gt;_`

**Voorbeeld:**

X-EC-Debug: x-ec-cache,x-ec-check-cacheable,x-ec-cache-key,x-ec-cache-state

Waarde|Resultaat
-|-
Ingeschakeld|Aanvragen voor foutopsporing cache antwoordheaders wordt een antwoord dat bestaat uit de header X-EC-Debug geretourneerd.
Uitgeschakeld|De X-EC-Debug response-header worden uitgesloten van het antwoord.

**Standaardgedrag:** uitgeschakeld.

[Terug naar boven](#azure-cdn-rules-engine-features)

</br>

---
### <a name="default-internal-max-age"></a>Standaard interne-maximumleeftijd
**Doel:** bepaalt het standaardinterval voor de maximale leeftijd voor POP naar oorsprong server cache opnieuw te worden gevalideerd. Met andere woorden, de hoeveelheid tijd dat wordt gewacht voordat een pop-server wordt gecontroleerd of een in cache asset overeenkomt met de asset die zijn opgeslagen op de bronserver.

Belangrijke informatie:

- Deze actie vindt alleen plaats voor antwoorden van een bronserver die niet een indicatie van de maximale leeftijd in hebt toegewezen de `Cache-Control` of `Expires` header.
- Deze actie vindt niet plaats voor activa die niet als caching geschikte worden beschouwd.
- Deze actie heeft geen invloed op de POP-cache revalidations browser. Deze typen revalidations worden bepaald door de `Cache-Control` of `Expires` verzonden naar de browser, die kan worden aangepast met de functie externe maximumleeftijd headers.
- De resultaten van deze actie hoeft niet een waarneembare effect op de antwoordheaders en de inhoud die is geretourneerd door de POP's voor uw inhoud, maar deze mogelijk gevolgen hebben voor de hoeveelheid verkeer van hervalidatie van POP's naar de oorspronkelijke server verzonden.
- Deze functie door configureren:
    - Als u de statuscode waarvoor een standaard interne-maximumleeftijd kan worden toegepast.
    - Een geheel getal opgeven en vervolgens te klikken op de gewenste tijdseenheid (bijvoorbeeld seconden, minuten, uren, enzovoort). Deze waarde bepaalt het standaardinterval voor de interne maximumleeftijd.

- Instellen van de tijdseenheid 'Uit' toewijst een interne maximumleeftijd standaardinterval van 7 dagen voor aanvragen die niet zijn toegewezen aan een indicatie van de maximale leeftijd in hun `Cache-Control` of `Expires` header.

**Standaardwaarde:** 7 dagen

#### <a name="compatibility"></a>Compatibiliteit
Dit onderdeel kan niet worden gekoppeld aan de volgende voorwaarden van de overeenkomst vanwege de manier die in cache van welke instellingen worden bijgehouden: 
- AS-nummer
- IP-adres van client
- Cookie-Parameter
- Cookie Parameter Regex
- Land
- Apparaat
- Rand Cname
- Verwijzende domein
- Aanvraag-Header Literal
- Aanvraag-Header Regex
- Aanvraag-Header jokertekens
- Verzoekmethode
- Aanvraag-schema
- URL-Query Literal
- URL-Query Regex
- URL-Query jokertekens
- URL-queryparameter

[Terug naar boven](#azure-cdn-rules-engine-features)

</br>

---
### <a name="deny-access-403"></a>(403) toegang weigeren
**Doel**: Hiermee bepaalt u of alle aanvragen zijn afgewezen met een 403-verboden-antwoord.

Waarde | Resultaat
------|-------
Ingeschakeld| Zorgt ervoor dat alle aanvragen die voldoen aan de criteria kunnen worden geweigerd met een 403-verboden-antwoord.
Uitgeschakeld| Hiermee herstelt u het standaardgedrag. De standaardinstelling is dat de oorspronkelijke server om te bepalen van het type van het antwoord dat wordt geretourneerd.

**Standaardgedrag**: uitgeschakeld

> [!TIP]
   > Een mogelijke gebruik voor deze functie is het koppelen aan een voorwaarde van de overeenkomst Header aanvragen toegang tot HTTP-verwijzingen die van inline koppelingen naar uw inhoud gebruikmaken blokkeren.

[Terug naar boven](#azure-cdn-rules-engine-features)

</br>

---
### <a name="expires-header-treatment"></a>Koptekst behandeling verloopt
**Doel:** bepaalt het genereren van `Expires` headers door een pop-server als de functie externe maximumleeftijd actief is.

De eenvoudigste manier om dit type configuratie is de externe maximumleeftijd en de functies verloopt Header behandeling plaatsen in dezelfde instructie.

Waarde|Resultaat
--|--
Overschrijven|Zorgt ervoor dat de volgende acties wordt uitgevoerd:<br/>-Overschrijft de `Expires` header die worden gegenereerd door de bronserver.<br/>-Voegt de `Expires` header geproduceerd door de functie externe maximumleeftijd aan het antwoord.
Pass Through|Zorgt ervoor dat de `Expires` header geproduceerd door de functie externe maximumleeftijd nooit wordt toegevoegd aan het antwoord. <br/> Als de bronserver produceert een `Expires` -kop, worden doorgegeven aan de eindgebruiker. <br/>Als de bronserver geen produceert een `Expires` header, wordt deze optie kan ertoe leiden dat de response-header bevat geen een `Expires` header.
Indien deze ontbreken toevoegen| Als een `Expires` header is niet ontvangen op de bronserver en vervolgens deze optie wordt de `Expires` header geproduceerd door de functie externe maximumleeftijd. Deze optie is handig om ervoor te zorgen dat alle activa zal worden toegewezen een `Expires` header.
Verwijderen| Zorgt ervoor dat een `Expires` header is niet opgenomen in de header-reactie. Als een `Expires` header is al toegewezen en vervolgens wordt deze verwijderd uit de header-reactie.

**Standaardgedrag:** overschrijven

[Terug naar boven](#azure-cdn-rules-engine-features)

</br>

---
### <a name="external-max-age"></a>Externe maximumleeftijd
**Doel:** bepaalt het maximumleeftijd interval voor de browser opnieuw valideren pop-server. Met andere woorden, de hoeveelheid tijd die wordt doorgegeven voordat een browser kan worden gecontroleerd voor een nieuwe versie van een actief van een pop-server.

Inschakelen van deze functie genereert `Cache-Control: max-age` en `Expires` headers van de POP's en verzend dit naar de HTTP-client. Standaard wordt deze koppen deze koppen gemaakt door de oorspronkelijke server overschreven. Maar de behandeling van Cache-Control-Header en de onderdelen van de Header behandeling verloopt gebruikt voor het wijzigen van dit probleem.

Belangrijke informatie:

- Deze actie heeft geen invloed op pop-server naar de oorsprong server cache revalidations. Deze typen revalidations worden bepaald door de `Cache-Control` en `Expires` headers ontvangen van de bronserver en kunnen worden aangepast met de standaard interne-maximumleeftijd en de onderdelen van Force interne-maximumleeftijd.
- Deze functie configureren door een geheel getal opgeven en het selecteren van de gewenste tijdseenheid (bijvoorbeeld seconden, minuten, uren, enzovoort).
- Als u deze functie op een negatieve waarde zorgt ervoor dat de POP's verzenden een `Cache-Control: no-cache` en een `Expires` tijd die is ingesteld in het verleden met elk antwoord naar de browser. Hoewel een HTTP-client wordt de reactie niet in cache, wordt deze instelling geen invloed op de mogelijkheid de POP's in de cache van het antwoord op de bronserver.
- Instellen van de tijdseenheid 'Uit', wordt deze functie uitschakelen. De `Cache-Control` en `Expires` headers in de cache opgeslagen met de reactie van de bronserver naar de browser worden doorgegeven.

**Standaardgedrag:** uitschakelen

[Terug naar boven](#azure-cdn-rules-engine-features)

</br>

---
### <a name="follow-redirects"></a>Omleidingen volgen
**Doel:** bepaalt of de aanvragen kunnen worden omgeleid naar de hostnaam die is gedefinieerd in de locatieheader geretourneerd door een klant-bronserver.

Belangrijke informatie:

- Aanvragen kunnen alleen worden omgeleid naar rand CNAME-records die met hetzelfde platform overeenkomen.

Waarde|Resultaat
-|-
Ingeschakeld|Aanvragen kunnen worden omgeleid.
Uitgeschakeld|Geen zal aanvragen worden omgeleid.

**Standaardgedrag:** uitgeschakeld.

[Terug naar boven](#azure-cdn-rules-engine-features)

</br>

---
### <a name="force-internal-max-age"></a>Interne maximumleeftijd forceren
**Doel:** bepaalt het maximumleeftijd interval voor POP naar oorsprong server cache opnieuw te worden gevalideerd. Met andere woorden, de hoeveelheid tijd dat wordt gewacht voordat een pop-server controleren kunt of een in cache asset overeenkomt met de asset die zijn opgeslagen op de bronserver.

Belangrijke informatie:

- Deze functie overschrijft het maximumleeftijd interval dat is gedefinieerd in `Cache-Control` of `Expires` headers gegenereerd op basis van een bronserver.
- Deze functie heeft geen invloed op de POP-cache revalidations browser. Deze typen revalidations worden bepaald door de `Cache-Control` of `Expires` headers verzonden naar de browser.
- Deze functie heeft geen waarneembare gevolgen heeft voor het antwoord geleverd door een pop-server aan de aanvrager. Het kan wel gevolgen heeft voor de hoeveelheid verkeer van hervalidatie van de POP's naar de oorspronkelijke server verzonden.
- Deze functie door configureren:
    - Als u de statuscode waarvoor een interne maximumleeftijd worden toegepast.
    - Een geheel getal opgeven en het selecteren van de gewenste tijdseenheid (bijvoorbeeld seconden, minuten, uren, enzovoort). Deze waarde bepaalt van de aanvraag maximumleeftijd interval.

- Als de tijdseenheid 'Uit', wordt deze functie uitgeschakeld. Een interne maximumleeftijd interval wordt niet worden toegewezen aan de aangevraagde activa. Als de oorspronkelijke header geen instructies cache bevat, wordt de asset overeenkomstig de actieve instelling in de functie standaard interne-maximumleeftijd worden opgeslagen.

**Standaardgedrag:** uitschakelen

#### <a name="compatibility"></a>Compatibiliteit
Dit onderdeel kan niet worden gekoppeld aan de volgende voorwaarden van de overeenkomst vanwege de manier die in cache van welke instellingen worden bijgehouden: 
- AS-nummer
- IP-adres van client
- Cookie-Parameter
- Cookie Parameter Regex
- Land
- Apparaat
- Rand Cname
- Verwijzende domein
- Aanvraag-Header Literal
- Aanvraag-Header Regex
- Aanvraag-Header jokertekens
- Verzoekmethode
- Aanvraag-schema
- URL-Query Literal
- URL-Query Regex
- URL-Query jokertekens
- URL-queryparameter

[Terug naar boven](#azure-cdn-rules-engine-features)

</br>

---
### <a name="h264-support-http-progressive-download"></a>H.264-ondersteuning (http-progressief downloaden)
**Doel:** bepaalt de soorten H.264 bestandsindelingen die kunnen worden gebruikt om inhoud streamen.

Belangrijke informatie:

- Een door spaties gescheiden set toegestane H.264 filename extensies in de optie bestandsextensies definiëren. De optie bestandsextensies wordt het standaardgedrag negeren. Ondersteuning voor MP4 en F4V onderhouden door te nemen die bestandsnaamextensies als u deze optie. 
- Een periode bij het opgeven van elke extensie bevatten (bijvoorbeeld _MP4_, _.f4v_).

**Standaardgedrag:** MP4 en F4V media standaard HTTP progressief downloaden ondersteunt.

[Terug naar boven](#azure-cdn-rules-engine-features)

</br>

---
### <a name="honor-no-cache-request"></a>EEr No-Cache-aanvraag
**Doel:** bepaalt of een HTTP-client geen cache's aanvragen zal worden doorgestuurd naar de oorspronkelijke server.

Een aanvraag Nee-cache treedt op wanneer de HTTP-client verzendt een `Cache-Control: no-cache` en/of `Pragma: no-cache` -header in de HTTP-aanvraag.

Waarde|Resultaat
--|--
Ingeschakeld|Kan een HTTP-client Nee-cache-aanvragen worden doorgestuurd naar de oorspronkelijke server en de bronserver terug naar de HTTP-client de antwoordheaders en de hoofdtekst van de pop-server wordt geretourneerd.
Uitgeschakeld|Hiermee herstelt u het standaardgedrag. Het standaardgedrag wordt voorkomen dat Nee-cache-aanvragen worden doorgestuurd naar de bronserver.

Voor alle verkeer van de productie, is het raadzaam deze functie in de standaardstatus uitgeschakeld laten staan. Oorsprong-servers wordt anders niet worden afgeschermd van eindgebruikers die per ongeluk activeren mogelijk veel aanvragen voor Nee-cache bij het vernieuwen van webpagina's, of van de veel populaire mediaspelers die zijn gecodeerd voor het verzenden van een koptekst Nee-cache met elke aanvraag video. Deze functie kan echter nuttig om te passen op bepaalde niet-productieve staging- of -adreslijsten moet testen om nieuwe inhoud op aanvraag worden opgehaald vanaf de oorspronkelijke server toestaan zijn.

De status van de cache die is gerapporteerd voor een aanvraag die kan worden doorgestuurd naar een bronserver als gevolg van deze functie is `TCP_Client_Refresh_Miss`. De statussen van de Cache-rapport, die beschikbaar in de module reporting Core is, bevat statistische gegevens door de status van de cache. Dit rapport kunt u het aantal en het percentage verzoeken die worden doorgestuurd naar een bronserver als gevolg van deze functie bijhouden.

**Standaardgedrag:** uitgeschakeld.

[Terug naar boven](#azure-cdn-rules-engine-features)

</br>

---
### <a name="ignore-origin-no-cache"></a>Negeren oorsprong No-Cache
**Doel:** bepaalt of de CDN wordt genegeerd door de volgende richtlijnen geleverd van een bronserver:

- `Cache-Control: private`
- `Cache-Control: no-store`
- `Cache-Control: no-cache`
- `Pragma: no-cache`

Belangrijke informatie:

- Deze functie configureren met het definiëren van een door spaties gescheiden lijst met statuscodes waarvoor de bovenstaande richtlijnen worden genegeerd.
- Het aantal geldige statuscodes voor met deze functie: 200, 203, 300, 301, 302, 305, 307, 400, 401, 402, 403, 404, 405, 406, 407, 408, 409, 410, 411, 412, 413, 414, 415, 416, 417, 500, 501, 502, 503, 504, en 505.
- Deze functie uitschakelen door deze op een lege waarde.

**Standaardgedrag:** het standaardgedrag is om te voldoen aan de bovenstaande richtlijnen.

#### <a name="compatibility"></a>Compatibiliteit
Dit onderdeel kan niet worden gekoppeld aan de volgende voorwaarden van de overeenkomst vanwege de manier die in cache van welke instellingen worden bijgehouden: 
- AS-nummer
- IP-adres van client
- Cookie-Parameter
- Cookie Parameter Regex
- Land
- Apparaat
- Rand Cname
- Verwijzende domein
- Aanvraag-Header Literal
- Aanvraag-Header Regex
- Aanvraag-Header jokertekens
- Verzoekmethode
- Aanvraag-schema
- URL-Query Literal
- URL-Query Regex
- URL-Query jokertekens
- URL-queryparameter

[Terug naar boven](#azure-cdn-rules-engine-features)

</br>

---
### <a name="ignore-unsatisfiable-ranges"></a>Ongeldig bereiken negeren 
**Doel:** bepaalt de reactie die naar clients wordt geretourneerd wanneer een aanvraag statuscode 416 aangevraagd bereik niet geldig genereert.

Standaard wordt deze statuscode geretourneerd wanneer de aanvraag opgegeven byte-bereik kan niet worden voldaan door een pop-server en een If-Range aanvraag-header-veld is niet opgegeven.

Waarde|Resultaat
-|-
Ingeschakeld|Hiermee voorkomt dat de POP's reageert op een ongeldige bytebereik-aanvraag met statuscode 416 aangevraagd bereik niet geldig. In plaats daarvan de servers leveren de aangevraagde asset en een 200 OK naar de client geretourneerd.
Uitgeschakeld|Hiermee herstelt u het standaardgedrag. Het standaardgedrag is om te voldoen aan de 416 statuscode aangevraagd bereik niet geldig.

**Standaardgedrag:** uitgeschakeld.

[Terug naar boven](#azure-cdn-rules-engine-features)

</br>

---
### <a name="internal-max-stale"></a>Interne Max-verouderd
**Doel:** bepaalt hoe lang voorbij de normale verlooptijd een activum in de cache kan worden geleverd vanuit een pop-server wanneer de pop-server niet kan valideren van de cache asset met de oorspronkelijke server.

Normaal gesproken als een asset maximumleeftijd tijd is verstreken, wordt de pop-server een validatieaanvraag verzonden naar de oorspronkelijke server. De oorsprong server wordt vervolgens reageren met een 304 niet worden gewijzigd zodat de pop-server een nieuwe lease op het in de cache actief, hetzij met 200 OK om de pop-server met een bijgewerkte versie van de activa in de cache.

Als u de pop-server kan geen verbinding maken met de oorspronkelijke server tijdens een poging van dergelijke hervalidatie, en vervolgens deze interne Max-verouderde-functie of en hoe lang bepaalt, blijven de pop-server voldoen aan de asset nu verouderd.

Houd er rekening mee dat dit tijdsinterval wordt gestart wanneer de activa maximumleeftijd is verlopen, niet als de mislukte hervalidatie optreedt. De maximale periode gedurende welke een asset kan worden geleverd zonder geslaagde hervalidatie is daarom de hoeveelheid tijd die is opgegeven door de combinatie van maximumleeftijd plus max verouderd. Bijvoorbeeld, als een actief is in de cache om 9:00 uur met een maximumleeftijd van 30 minuten en een maximale-verouderde van 15 minuten, zou klikt u vervolgens een hervalidatie mislukte poging om 9:44 leiden tot een eindgebruiker de verlopen in de cache asset ontvangen tijdens een poging tot mislukte hervalidatie om 9:46 tot de nl leiden zou d gebruiker ontvangt een 504 Gateway timeout gegenereerd.

Een waarde die is geconfigureerd voor deze functie wordt vervangen door `Cache-Control: must-revalidate` of `Cache-Control: proxy-revalidate` headers ontvangen van de bronserver. Als een van deze koppen wordt ontvangen op de bronserver wanneer u een actief is in eerste instantie in de cache opgeslagen, klikt u vervolgens in de pop-server niet een verlopen in de cache asset fungeren. Als de pop-server niet kan valideren met de oorsprong wanneer de activa maximumleeftijd interval is verstreken, geeft de pop-server in dat geval een 504 Gateway Timeout-fout.

Belangrijke informatie:

- Deze functie door configureren:
    - Als u de statuscode waarvoor een max-verouderde worden toegepast.
    - Een geheel getal opgeven en vervolgens te klikken op de gewenste tijdseenheid (bijvoorbeeld seconden, minuten, uren, enzovoort). Deze waarde bepaalt de interne max-verouderde die worden toegepast.

- Instellen van de tijdseenheid 'Uit', wordt deze functie uitschakelen. Een element in de cache kan niet buiten de normale verlooptijd worden geleverd.

**Standaardgedrag:** twee minuten

#### <a name="compatibility"></a>Compatibiliteit
Dit onderdeel kan niet worden gekoppeld aan de volgende voorwaarden van de overeenkomst vanwege de manier die in cache van welke instellingen worden bijgehouden: 
- AS-nummer
- IP-adres van client
- Cookie-Parameter
- Cookie Parameter Regex
- Land
- Apparaat
- Rand Cname
- Verwijzende domein
- Aanvraag-Header Literal
- Aanvraag-Header Regex
- Aanvraag-Header jokertekens
- Verzoekmethode
- Aanvraag-schema
- URL-Query Literal
- URL-Query Regex
- URL-Query jokertekens
- URL-queryparameter

[Terug naar boven](#azure-cdn-rules-engine-features)

</br>

---
### <a name="log-query-string"></a>Queryreeks logboek
**Doel:** bepaalt of een queryreeks samen met de URL in Logboeken worden opgeslagen.

Waarde|Resultaat
-|-
Ingeschakeld|Kan de opslag van queryreeksen bij de registratie van URL's in een toegangslogboek. Als een URL een queryreeks bevat geen bevat, klikt u vervolgens heeft deze optie geen effect.
Uitgeschakeld|Hiermee herstelt u het standaardgedrag. Het standaardgedrag is querytekenreeksen negeren bij de registratie van URL's in een toegangslogboek.

**Standaardgedrag:** uitgeschakeld.

[Terug naar boven](#azure-cdn-rules-engine-features)

</br>

---
### <a name="maximum-keep-alive-requests"></a>Maximum aantal keepalive-aanvragen
**Doel:** definieert het maximum aantal aanvragen voor een Keep-Alive verbinding wordt gesloten.

Als het maximale aantal aanvragen op een lage waarde wordt afgeraden en kan leiden tot verminderde prestaties.

Belangrijke informatie:

- Deze waarde als een geheel getal opgeven.
- Neem geen komma's of punten in de opgegeven waarde.

**Standaardwaarde:** 10.000 aanvragen

[Terug naar boven](#azure-cdn-rules-engine-features)

</br>

---
### <a name="modify-client-request-header"></a>De aanvraagheader Client wijzigen
**Doel:** elke aanvraag bevat een set van aanvraagheaders beschrijving van het. Deze functie de volgende mogelijkheden:

- Toevoegen of de waarde die is toegewezen aan een aanvraagheader overschrijven. Als de opgegeven aanvraagkop niet bestaat, wordt klikt u vervolgens deze functie deze toevoegen aan de aanvraag.
- Verwijdert een aanvraagheader van de aanvraag.

Aanvragen die worden doorgestuurd naar een bronserver bevatten de wijzigingen die door deze functie.

Een van de volgende acties worden uitgevoerd op een aanvraag-header:

Optie|Beschrijving|Voorbeeld
-|-|-
Toevoegen|De opgegeven waarde zal worden toegevoegd aan het einde van de bestaande waarde voor de aanvraag-header.|**Aanvraag-header-waarde (client):**<br/>Value1<br/>**Aanvraag-header-waarde (regelengine):**<br/>Value2 <br/>**Nieuwe aanvraag-header-waarde:** <br/>Value1Value2
Overschrijven|De waarde van de aanvraag-header wordt ingesteld op de opgegeven waarde.|**Aanvraag-header-waarde (client):**<br/>Value1<br/>**Aanvraag-header-waarde (regelengine):**<br/>Value2<br/>**Nieuwe aanvraag-header-waarde:**<br/> Value2 <br/>
Verwijderen|Hiermee verwijdert u de opgegeven aanvraagheader.|**Aanvraag-header-waarde (client):**<br/>Value1<br/>**Client-aanvraag-header-configuratie wijzigen:**<br/>Verwijder de desbetreffende aanvraagheader.<br/>**Resultaat:**<br/>De opgegeven aanvraagkop niet doorgestuurd naar de oorspronkelijke server.

Belangrijke informatie:

- Zorg ervoor dat de opgegeven waarde in de optie voor een exacte overeenkomst voor de gewenste aanvraagheader.
- Aanvraag is niet in aanmerking voor de identificatie van een koptekst genomen. Bijvoorbeeld, een van de volgende variaties van de `Cache-Control` headernaam kan worden gebruikt bij het identificeren ervan:
    - cache-control
    - CACHE-CONTROL
    - cachE-Control
- Wanneer u een header-naam opgeeft, gebruik alleen alfanumerieke tekens, streepjes of onderstrepingstekens bevatten.
- Verwijderen van een koptekst, kunnen er door de POP's worden doorgestuurd naar een server van de oorsprong.
- De volgende headers zijn gereserveerd en kunnen niet worden gewijzigd door deze functie:
    - doorgestuurd
    - host
    - via
    - Waarschuwing
    - x doorgestuurd voor
    - Alle headernamen die met 'x ec beginnen' zijn gereserveerd.

[Terug naar boven](#azure-cdn-rules-engine-features)

</br>

---
### <a name="modify-client-response-header"></a>Client-antwoordheader wijzigen
Elk antwoord bevat een set van antwoordheaders beschrijving van het. Deze functie de volgende mogelijkheden:

- Toevoegen of de waarde die is toegewezen aan een antwoordheader overschrijven. Als de opgegeven antwoordkop niet bestaat, wordt klikt u vervolgens deze functie deze toevoegen aan het antwoord.
- Een antwoordheader verwijderen uit het antwoord.

Standaard worden met de headerwaarden antwoord op een bronserver en op de POP's gedefinieerd.

Een van de volgende acties worden uitgevoerd op een antwoordheader:

Optie|Beschrijving|Voorbeeld
-|-|-
Toevoegen|De opgegeven waarde zal worden toegevoegd aan het einde van de bestaande waarde voor de antwoord-header.|**De headerwaarde (client) antwoord:**<br />Value1<br/>**De headerwaarde (regelengine) antwoord:**<br/>Value2<br/>**Nieuwe antwoord header-waarde:**<br/>Value1Value2
Overschrijven|De waarde van de antwoord-header wordt met de opgegeven waarde worden ingesteld.|**De headerwaarde (client) antwoord:**<br/>Value1<br/>**De headerwaarde (regelengine) antwoord:**<br/>Value2 <br/>**Nieuwe antwoord header-waarde:**<br/>Value2 <br/>
Verwijderen|Hiermee verwijdert u de opgegeven antwoordheader.|**De headerwaarde (client) antwoord:**<br/>Value1<br/>**Client antwoord header-configuratie wijzigen:**<br/>Verwijder de desbetreffende response-header.<br/>**Resultaat:**<br/>De opgegeven antwoordkop niet doorgestuurd naar de aanvrager.

Belangrijke informatie:

- Zorg ervoor dat de opgegeven waarde in de optie voor een exacte overeenkomst voor de gewenste response-header. 
- Aanvraag is niet in aanmerking voor de identificatie van een koptekst genomen. Bijvoorbeeld, een van de volgende variaties van de `Cache-Control` headernaam kan worden gebruikt bij het identificeren ervan:
    - cache-control
    - CACHE-CONTROL
    - cachE-Control
- Verwijderen van een koptekst voorkomt dat het wordt doorgestuurd naar de aanvrager.
- De volgende headers zijn gereserveerd en kunnen niet worden gewijzigd door deze functie:
    - Accepteer codering
    - leeftijd
    - verbinding
    - codering van inhoud
    - lengte van inhoud
    - inhoud bereik
    - datum
    - server
    - aanhangwagen
    - Transfer-encoding
    - upgrade
    - variëren
    - via
    - Waarschuwing
    - Alle headernamen die met 'x ec beginnen' zijn gereserveerd.

[Terug naar boven](#azure-cdn-rules-engine-features)

</br>

---
### <a name="partial-cache-sharing"></a>Gedeeltelijke Cache delen
**Doel:** bepaalt of een aanvraag deels in cache opgeslagen inhoud kan genereren.

Deze gedeeltelijke cache kan vervolgens worden gebruikt om te voldoen aan nieuwe aanvragen voor de inhoud totdat de gevraagde inhoud volledig opgeslagen in de cache.

Waarde|Resultaat
-|-
Ingeschakeld|Aanvragen deels in cache opgeslagen inhoud kunnen worden gegenereerd.
Uitgeschakeld|Aanvragen kunnen alleen genereren voor een volledig in de cache opgeslagen versie van de gevraagde inhoud.

**Standaardgedrag:** uitgeschakeld.

[Terug naar boven](#azure-cdn-rules-engine-features)

</br>

---
### <a name="prevalidate-cached-content"></a>De inhoud in cache prevalidate
**Doel:** Hiermee wordt bepaald of de inhoud in cache in aanmerking komen voor vroege hervalidatie voordat de TTL verloopt.

De hoeveelheid tijd vóór de vervaldatum van de gevraagde inhoud TTL waarover in aanmerking komen voor vroege hervalidatie moeten definiëren.

Belangrijke informatie:

- "Off" selecteren als de tijdseenheid hervalidatie vereist te kunnen uitvoeren na de opgeslagen inhoud is TTL verlopen. Tijd moet niet worden opgegeven en wordt genegeerd.

**Standaardgedrag:** uitschakelen. Hervalidatie kan alleen plaatsvinden nadat de inhoud in de cache van TTL is verlopen.

[Terug naar boven](#azure-cdn-rules-engine-features)

</br>

---
### <a name="proxy-special-headers"></a>Speciale proxy-kopteksten
**Doel:** definieert de set met [Verizon-specifieke HTTP-aanvraagheaders](cdn-verizon-http-headers.md) die zal van een POP-worden doorgestuurd naar een bronserver.

Belangrijke informatie:

- Elke aanvraag-header van het CDN-specifieke gedefinieerd in deze functie wordt doorgestuurd naar een bronserver. Uitgesloten headers niet doorgestuurd.
- Om te voorkomen dat een aanvraagheader CDN-specifieke die ernaar worden doorgestuurd, moet u deze uit door spaties gescheiden lijst in het veld header lijst verwijderd.

De volgende HTTP-headers zijn opgenomen in de lijst met:
- Via
- X doorgestuurd voor
- X-doorgestuurd-protocol
- X-Host
- X-Midgress
- X-Gateway-lijst
- X-EC-naam
- Host

**Standaardgedrag:** alle CDN-specifieke aanvraagheaders doorgestuurd naar de oorspronkelijke server.

[Terug naar boven](#azure-cdn-rules-engine-features)

</br>

---
### <a name="refresh-zero-byte-cache-files"></a>Vernieuwen van nul bytes cachebestanden
**Doel:** bepaalt hoe van de client van een HTTP-aanvraag voor een asset 0-byte-cache wordt verwerkt door de POP's.

Geldige waarden zijn:

Waarde|Resultaat
--|--
Ingeschakeld|Zorgt ervoor dat de POP opnieuw de asset ophalen op de bronserver.
Uitgeschakeld|Hiermee herstelt u het standaardgedrag. Het standaardgedrag is om te dienen als geldige cache activa op verzoek.
Deze functie is niet vereist voor de juiste caching en leveren van inhoud, maar mogelijk van pas als tijdelijke oplossing. Genereren van dynamische inhoud op servers van de oorsprong kunnen bijvoorbeeld per ongeluk resulteren in 0-byte-antwoorden worden verzonden naar de POP's. Deze typen antwoorden zijn doorgaans in het cachegeheugen van de POP's. Als u weet dat een antwoord 0 bytes nooit een geldig antwoord is 

voor deze inhoud vervolgens deze functie kunt voorkomen dat deze typen elementen worden geleverd aan uw clients.

**Standaardgedrag:** uitgeschakeld.

[Terug naar boven](#azure-cdn-rules-engine-features)

</br>

---
### <a name="set-cacheable-status-codes"></a>Statuscodes voor caching geschikte instellen
**Doel:** definieert de set met statuscodes die tot de inhoud in cache leiden kunnen.

Standaard is opslaan in cache alleen ingeschakeld voor 200 OK antwoorden.

Een set door spaties gescheiden van de gewenste statuscodes definiëren.

Belangrijke informatie:

- De functie negeren oorsprong No-Cache inschakelen. Als deze functie niet is ingeschakeld, klikt u vervolgens 200 OK antwoorden mogelijk niet opgeslagen.
- Het aantal geldige statuscodes voor met deze functie: 203, 300, 301, 302, 305, 307, 400, 401, 402, 403, 404, 405, 406, 407, 408, 409, 410, 411, 412, 413, 414, 415, 416, 417, 500, 501, 502, 503, 504, en 505.
- Deze functie kan niet worden gebruikt om uit te schakelen in cache opslaan voor reacties waarbij 200 OK statuscode is gegenereerd.

**Standaardgedrag:** opslaan in cache is alleen ingeschakeld voor reacties waarbij 200 OK statuscode is gegenereerd.

[Terug naar boven](#azure-cdn-rules-engine-features)

</br>

---
### <a name="set-client-ip-custom-header"></a>Stel aangepaste Header voor Client-IP
**Doel:** voegt u een aangepaste header die de aanvragende client op IP-adres op de aanvraag identificeert.

De optie Header-naam definieert de naam van de aangepaste aanvraagheaders waar de client-IP-adres wordt opgeslagen.

Met deze functie kunt u een klant bronserver om erachter te komen client-IP-adressen via een aangepaste aanvraagheader. Als de aanvraag is geleverd vanuit cache, wordt er niet de oorspronkelijke server van de client-IP-adres worden geïnformeerd. Daarom is het raadzaam dat deze functie worden gebruikt bij de activa die niet zijn opgeslagen in de cache.

Zorg ervoor dat de opgegeven header-naam komt niet overeen met een van de volgende namen:

- Namen standaard aanvraag-header. Een lijst met headernamen van de standaard-kan worden gevonden in [RFC 2616](http://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html).
- Headernamen van de gereserveerde:
    - doorgestuurd voor
    - host
    - variëren
    - via
    - Waarschuwing
    - x doorgestuurd voor
    - Alle headernamen die met 'x ec beginnen' zijn gereserveerd.

[Terug naar boven](#azure-cdn-rules-engine-features)

</br>

---
### <a name="stale-content-delivery-on-error"></a>Verouderde Contentlevering bij fout
**Doel:** bepaalt of verlopen in de cache inhoud wordt geleverd als een fout optreedt tijdens de hervalidatie van de cache of bij het ophalen van de aangevraagde inhoud op de bronserver van de klant.

Waarde|Resultaat
-|-
Ingeschakeld|Verouderde inhoud is geleverd aan de aanvrager wanneer er een fout optreedt tijdens een verbinding met een bronserver.
Uitgeschakeld|Fout bij de bronserver wordt doorgestuurd naar de aanvrager.

**Standaardgedrag:** uitgeschakeld

[Terug naar boven](#azure-cdn-rules-engine-features)

</br>

---
### <a name="stale-while-revalidate"></a>Verouderde tijdens Revalidate
**Doel:** verbetert de prestaties doordat de POP's te bedienen verouderde inhoud aan de aanvrager terwijl hervalidatie plaatsvindt.

Belangrijke informatie:

- Het gedrag van deze functie is afhankelijk van de geselecteerde tijdseenheid.
    - **Tijdseenheid:** een tijdsduur opgeven en selecteer een tijdseenheid (bijvoorbeeld seconden, minuten, uren, enz.) om toe te staan van verouderde leveren van inhoud. Dit type installatieprogramma kan de CDN naar de tijdsduur die dit kan worden bezorgd uitbreiden inhoud voordat u validatie volgens de volgende formule: **TTL** + **verlopen tijdens de validatie opnieuw tijd** 
    - **Uit:** Selecteer 'uit' aan moet worden gevalideerd voordat een aanvraag voor verouderde inhoud kan worden geleverd.
        - Geef een tijdsduur geen omdat deze niet van toepassing en worden genegeerd.

**Standaardgedrag:** uitschakelen. Hervalidatie moet plaatsvinden voordat de aangevraagde inhoud kan worden geleverd.

[Terug naar boven](#azure-cdn-rules-engine-features)

</br>

---
### <a name="token-auth"></a>Token Auth
**Doel:** bepaalt of verificatie op basis van tokens worden toegepast op een aanvraag.

Als verificatie op basis van het Token is ingeschakeld, wordt alleen aanvragen die een gecodeerde token bieden en voldoen aan de vereisten die zijn opgegeven in die worden uitgevoerd.

De versleutelingssleutel die wordt gebruikt voor het versleutelen en ontsleutelen van token waarden wordt bepaald door de primaire sleutel en de sleutel van de back-up-opties op de pagina Token Auth. Houd er rekening mee dat versleutelingssleutels platform-specifieke.

**Standaardgedrag:** uitgeschakeld.

Deze functie heeft voorrang op de meeste functies met uitzondering van de functie voor het herschrijven van URL's.

Waarde | Resultaat
------|---------
Ingeschakeld | Beveiligt de gevraagde inhoud met verificatie op basis van tokens. Alleen aanvragen van clients die voorzien van een geldig token en voldoen aan de vereisten gehonoreerd. FTP-transacties zijn uitgesloten van verificatie op basis van tokens.
Uitgeschakeld| Hiermee herstelt u het standaardgedrag. De standaardinstelling is dat de configuratie van verificatie op basis van tokens om te bepalen of een aanvraag worden beveiligd.

#### <a name="compatibility"></a>Compatibiliteit
Gebruik geen Token Auth met de voorwaarde van een altijd overeen. 

[Terug naar boven](#azure-cdn-rules-engine-features)

</br>

---
### <a name="token-auth-denial-code"></a>Token Auth DOS-Code
**Doel:** bepaalt het type van de reactie die wordt geretourneerd voor een gebruiker wanneer een aanvraag wordt geweigerd vanwege verificatie op basis van tokens.

De beschikbare reactiecodes worden vermeld in de volgende tabel.

Antwoordcode|De naam van de reactie|Beschrijving
-------------|-------------|--------
301|Permanent verplaatst|Deze statuscode wordt niet-geautoriseerde gebruikers omgeleid naar de URL die is opgegeven in de locatie-header.
302|Gevonden|Deze statuscode wordt niet-geautoriseerde gebruikers omgeleid naar de URL die is opgegeven in de locatie-header. Deze statuscode is de standaardmethode voor de branche van de uitvoering van een omleiding.
307|Tijdelijke omleiding|Deze statuscode wordt niet-geautoriseerde gebruikers omgeleid naar de URL die is opgegeven in de locatie-header.
401|Niet geautoriseerd|Deze statuscode combineren met de reactie WWW-Authenticate-header, kunt u een gebruiker voor de verificatie wordt gevraagd.
403|Verboden|Dit bericht is de standaard 403 verboden statusbericht dat een onbevoegde gebruiker ziet wanneer u probeert te krijgen tot beveiligde inhoud.
404|Het bestand is niet gevonden|Deze statuscode geeft aan dat de HTTP-client kan communiceren met de server is, maar de aangevraagde inhoud is niet gevonden.

#### <a name="compatibility"></a>Compatibiliteit
Gebruik geen Token autorisatiecode denial of met de voorwaarde van een altijd overeen. Gebruik in plaats daarvan de **afhandeling van aangepaste DOS** sectie de **Token Auth** pagina van de **beheren** portal. Zie voor meer informatie [beveiligen van Azure CDN activa met tokenverificatie](cdn-token-auth.md).

#### <a name="url-redirection"></a>URL-omleiding

Deze functie ondersteunt URL omleiding naar een door de gebruiker gedefinieerde URL wanneer deze is geconfigureerd voor het retourneren van een statuscode 3xx. Deze door de gebruiker gedefinieerde URL kan worden opgegeven door de volgende stappen uit te voeren:

1. Selecteer een antwoordcode 3xx voor de functie Auth DOS-tokencode.
2. Selecteer 'Locatie' in de optie optionele Header-naam.
3. De optie optionele Header-waarde ingesteld op de gewenste URL.

Als een URL is niet gedefinieerd voor een statuscode 3xx, wordt de antwoordpagina standaard voor 3xx statuscode geretourneerd aan de gebruiker.

URL-doorverwijzing is alleen van toepassing voor 3xx responscodes.

De optie optioneel Header-waarde ondersteunt alfanumerieke tekens, aanhalingstekens en spaties.

#### <a name="authentication"></a>Verificatie

Deze functie biedt ondersteuning voor de mogelijkheid om op te nemen van de WWW-Authenticate-header bij het reageren op een ongeautoriseerde aanvraag voor inhoud die is beveiligd door verificatie op basis van tokens. Als de WWW-Authenticate-header is ingesteld op 'basic' in de configuratie en vervolgens de niet-geautoriseerde gebruiker wordt gevraagd om referenties.

De bovenstaande configuratie kan worden bereikt door het uitvoeren van de volgende stappen uit:

1. Selecteer '401' als de responscode voor de functie Auth DOS-tokencode.
2. Selecteer 'WWW ' verificatie van de optionele naam van de Header-optie.
3. De optionele waarde voor Header-optie voor 'basic'.

De WWW-Authenticate-header is alleen van toepassing op 401 responscodes.

[Terug naar boven](#azure-cdn-rules-engine-features)

</br>

---
### <a name="token-auth-ignore-url-case"></a>Token Auth hoofdlettergevoeligheid niet van belang URL
**Doel:** bepaalt of de URL vergelijkingen aangebracht door verificatie op basis van tokens hoofdlettergevoelig zijn.

De parameters die beïnvloed door deze functie zijn:

- ec_url_allow
- ec_ref_allow
- ec_ref_deny

Geldige waarden zijn:

Waarde|Resultaat
---|----
Ingeschakeld|Zorgt ervoor dat de POP hoofdlettergevoeligheid niet van belang bij het vergelijken van URL's voor de parameters voor verificatie op basis van tokens.
Uitgeschakeld|Hiermee herstelt u het standaardgedrag. Het standaardgedrag is voor de URL vergelijkingen voor tokenverificatie zijn hoofdlettergevoelig.

**Standaardgedrag:** uitgeschakeld.

[Terug naar boven](#azure-cdn-rules-engine-features)

</br>

---
### <a name="token-auth-parameter"></a>Token Auth-Parameter
**Doel:** bepaalt of de verificatie op basis van tokens querytekenreeksparameter moet worden gewijzigd.

Belangrijke informatie:

- De optie waarde definieert de parameternaam van querytekenreeks waarmee een token worden opgegeven.
- De optie waarde kan niet worden ingesteld op 'ec_token'.
- Zorg ervoor dat de naam die is gedefinieerd in de optie waarde alleen geldige URL-tekens bevat.

Waarde|Resultaat
----|----
Ingeschakeld|De optie waarde definieert de parameternaam van querytekenreeks waarmee tokens moeten worden gedefinieerd.
Uitgeschakeld|Een token kan worden opgegeven als een niet-gedefinieerde queryreeksparameter opgeven in de aanvraag-URL.

**Standaardgedrag:** uitgeschakeld. Een token kan worden opgegeven als een niet-gedefinieerde queryreeksparameter opgeven in de aanvraag-URL.

[Terug naar boven](#azure-cdn-rules-engine-features)

</br>

---
### <a name="url-redirect"></a>URL-omleiding
**Doel:** aanvragen via de locatie-header wordt omgeleid.

De configuratie van deze functie is vereist voor het instellen van de volgende opties:

Optie|Beschrijving
-|-
Code|Selecteer de antwoordcode die wordt geretourneerd naar de aanvrager.
Bron & patroon| Deze instellingen geven aan een aanvraag-URI-patroon waarmee het type van aanvragen die kunnen worden omgeleid. Alleen aanvragen waarvan de URL van de volgende criteria voldoet wordt omgeleid: <br/> <br/> **Bron (of een punt voor toegang tot inhoud):** selecteert u een relatief pad zijn dat een bronserver identificeert. Dit pad is de _/XXXX/_ sectie en de naam van uw eindpunt. <br/><br/> **Bron (patroon):** een patroon dat aanvragen worden aangeduid met het relatieve pad moet worden gedefinieerd. Dit patroon van een reguliere expressie moet een pad dat begint direct na de eerder geselecteerde toegang tot inhoud wijst (Zie hierboven) definiëren. <br/> -Zorg ervoor dat de aanvraag-URI (dat wil zeggen, bron & patroon) eerder gedefinieerde criteria niet conflicteert met eventuele overeenkomst voorwaarden gedefinieerd voor deze functie. <br/> -Geef een patroon; Als u een lege waarde als het patroon, worden alle tekenreeksen worden vergeleken.
Doel| Definieer de URL waarnaar de bovenstaande aanvragen worden omgeleid. <br/><br/> Dynamisch maken gebruik van deze URL: <br/> -Een reguliere-expressiepatroon <br/>- [HTTP-variabelen](cdn-http-variables.md) <br/><br/> Vervang de waarden die zijn vastgelegd in het patroon van de bron in het doel-patroon met $_n_ waar _n_ identificeert een waarde door de volgorde waarin deze is vastgelegd. $1 vertegenwoordigt bijvoorbeeld de eerste waarde die is vastgelegd in het patroon van de bron, terwijl $2 de tweede waarde vertegenwoordigt. <br/> 
Het is raadzaam een absolute URL gebruiken. Het gebruik van een relatieve URL kan CDN URL's omleiden naar een ongeldig pad.

**Voorbeeldscenario**

In dit voorbeeld laat zien hoe een edge CNAME-URL die wordt omgezet naar dit basis-URL in CDN omleiden: http:\//marketing.azureedge.net/brochures

In aanmerking komende aanvragen worden omgeleid naar deze base rand CNAME-URL: http:\//cdn.mydomain.com/resources

Deze URL-omleiding kan worden bereikt via de volgende configuratie: ![URL omleiden](./media/cdn-rules-engine-reference/cdn-rules-engine-redirect.png)

**Belangrijke punten:**

- De functie Omleidings-URL van de aanvraag definieert URL's die wordt omgeleid. Als gevolg hiervan zijn aanvullende overeenkomst voorwaarden niet vereist. Hoewel de voorwaarde van de overeenkomst is gedefinieerd als "Always", worden alleen aanvragen die naar de map 'brochures' op de 'marketing' oorsprong van de klant verwijzen wordt omgeleid. 
- Alle overeenkomende aanvragen wordt omgeleid naar de rand CNAME-URL die is gedefinieerd in de doel-optie. 
    - Voorbeeldscenario voor #1: 
        - Voorbeeld van een aanvraag (CDN URL): http:\//marketing.azureedge.net/brochures/widgets.pdf 
        - Aanvraag-URL (na omleiding): http:\//cdn.mydomain.com/resources/widgets.pdf  
    - Voorbeeldscenario voor #2: 
        - Voorbeeld van een aanvraag (Edge CNAME URL): http:\//marketing.mydomain.com/brochures/widgets.pdf 
        - Aanvraag-URL (na omleiding): http:\//cdn.mydomain.com/resources/widgets.pdf voorbeeldscenario
    - Voorbeeldscenario voor #3: 
        - Voorbeeld van een aanvraag (Edge CNAME URL): http:\//brochures.mydomain.com/campaignA/final/productC.ppt 
        - Aanvraag-URL (na omleiding): http:\//cdn.mydomain.com/resources/campaignA/final/productC.ppt  
- De variabele aanvraag schema (% {schema}) wordt gebruikt in de doel-optie, die zorgt ervoor dat het schema van de aanvraag ongewijzigd na omleiding blijft.
- De URL-segmenten die zijn opgenomen in de aanvraag worden toegevoegd aan de nieuwe URL via "$1."

[Terug naar boven](#azure-cdn-rules-engine-features)

</br>

---
### <a name="url-rewrite"></a>Herschrijven van URL
**Doel:** herschrijft de aanvraag-URL.

Belangrijke informatie:

- De configuratie van deze functie is vereist voor het instellen van de volgende opties:

Optie|Beschrijving
-|-
 Bron & patroon | Deze instellingen geven aan een aanvraag-URI-patroon waarmee het type van aanvragen die opnieuw kunnen worden geschreven. Alleen aanvragen waarvan de URL van de volgende criteria voldoet herschreven: <br/><br/>  - **Bron (of een punt voor toegang tot inhoud):** selecteert u een relatief pad zijn dat een bronserver identificeert. Dit pad is de _/XXXX/_ sectie en de naam van uw eindpunt. <br/><br/> - **Bron (patroon):** een patroon dat aanvragen worden aangeduid met het relatieve pad moet worden gedefinieerd. Dit patroon van een reguliere expressie moet een pad dat begint direct na de eerder geselecteerde toegang tot inhoud wijst (Zie hierboven) definiëren. <br/> Controleer of dat de aanvraag-URI (dat wil zeggen, bron & patroon) eerder gedefinieerde criteria niet conflicteert met een van de overeenkomst voorwaarden gedefinieerd voor deze functie. Geef een patroon; Als u een lege waarde als het patroon, worden alle tekenreeksen worden vergeleken. 
 Doel  |Definieer de relatieve URL waarnaar de bovenstaande aanvragen worden herschreven door: <br/>    1. Als u een punt voor toegang tot inhoud die een bronserver identificeert. <br/>    2. Het definiëren van een relatief pad met: <br/>        -Een reguliere-expressiepatroon <br/>        - [HTTP-variabelen](cdn-http-variables.md) <br/> <br/> Vervang de waarden die zijn vastgelegd in het patroon van de bron in het doel-patroon met $_n_ waar _n_ identificeert een waarde door de volgorde waarin deze is vastgelegd. $1 vertegenwoordigt bijvoorbeeld de eerste waarde die is vastgelegd in het patroon van de bron, terwijl $2 de tweede waarde vertegenwoordigt. 
 Deze functie kunt de POP's moeten worden herschreven van de URL zonder dat u een traditionele omleiding uitvoert. Dat wil zeggen, ontvangt de aanvrager de dezelfde antwoordcode alsof de herschreven URL hadden aangevraagd.

**Voorbeeldscenario 1**

In dit voorbeeld laat zien hoe een edge CNAME-URL die wordt omgezet naar dit basis-URL in CDN omleiden: http:\//marketing.azureedge.net/brochures/

In aanmerking komende aanvragen worden omgeleid naar deze base rand CNAME-URL: http:\//MyOrigin.azureedge.net/resources/

Deze URL-omleiding kan worden bereikt via de volgende configuratie: ![URL omleiden](./media/cdn-rules-engine-reference/cdn-rules-engine-rewrite.png)

**Voorbeeldscenario 2**

Dit voorbeeld wordt het omleiden van een edge CNAME URL uit hoofdletters in kleine letters reguliere expressies gebruiken.

Deze URL-omleiding kan worden bereikt via de volgende configuratie: ![URL omleiden](./media/cdn-rules-engine-reference/cdn-rules-engine-to-lowercase.png)


**Belangrijke punten:**

- De functie voor het herschrijven van URL's definieert de aanvraag URL's die herschreven. Als gevolg hiervan zijn aanvullende overeenkomst voorwaarden niet vereist. Hoewel de voorwaarde van de overeenkomst is gedefinieerd als "Always", wordt er alleen aanvragen die naar de map 'brochures' op de 'marketing' klant oorsprong verwijzen herschreven.

- De URL-segmenten die zijn opgenomen in de aanvraag worden toegevoegd aan de nieuwe URL via "$1."

#### <a name="compatibility"></a>Compatibiliteit
Deze functie bevat die overeenkomt met de criteria die moeten worden voldaan voordat deze kan worden toegepast op een aanvraag. Om te voorkomen dat een conflicterende vergelijkingscriterium instellen, moet aan deze functie is niet compatibel met de volgende voorwaarden van de overeenkomst:

- AS-nummer
- CDN-oorsprong
- IP-adres van client
- Oorsprong van de klant
- Aanvraag-schema
- URL-pad naar map
- URL-pad-uitbreiding
- URL-pad bestandsnaam
- URL-pad Literal
- URL-pad Regex
- URL-pad jokerteken
- URL-Query Literal
- URL-queryparameter
- URL-Query Regex
- URL-Query jokertekens

[Terug naar boven](#azure-cdn-rules-engine-features)

</br>

---
### <a name="user-variable"></a>Variabele van de gebruiker
**Doel:** alleen voor intern gebruik.

[Terug naar boven](#azure-cdn-rules-engine-features)

</br>

## <a name="next-steps"></a>Volgende stappen
* [Verwijzing regelengine](cdn-rules-engine-reference.md)
* [Voorwaardelijke expressies regelengine](cdn-rules-engine-reference-conditional-expressions.md)
* [Criteria voor overeenkomst regelengine](cdn-rules-engine-reference-match-conditions.md)
* [HTTP-instelling met de regelengine van wijzigen](cdn-rules-engine.md)
* [Overzicht van Azure CDN](cdn-overview.md)
