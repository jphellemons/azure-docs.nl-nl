---
title: Wat is Azure Search | Microsoft Docs
description: Azure Search is een volledig beheerde cloudzoekservice. Meer informatie ziet u in dit functieoverzicht.
manager: cgronlun
author: HeidiSteen
services: search
ms.service: search
ms.topic: overview
ms.date: 11/10/2017
ms.author: heidist
ms.openlocfilehash: ad5c60c246c2946e4dd3a5bb6b4d6e8d21d2b03d
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/23/2018
---
# <a name="what-is-azure-search"></a>Wat is Azure Search?
Azure Search is een SaaS-cloudoplossing (Search-as-a-Service) die ontwikkelaars API’s en hulpprogramma’s biedt waarmee ze de manier waarop u zoekt naar inhoud op het web, op mobiel en in bedrijfstoepassingen kunnen verrijken.

Functionaliteit wordt beschikbaar gemaakt via een eenvoudige [REST API](/rest/api/searchservice/) of [.NET SDK](search-howto-dotnet-sdk.md) waarmee de inherente complexiteit van het ophalen van gegevens wordt gemaskeerd. Naast API’s biedt Azure Portal ondersteuning voor administratie- en inhoudsbeheer met hulpprogramma’s voor het ontwikkelen van prototypen en het doorzoeken van indexen. Omdat de service wordt uitgevoerd in de cloud, worden de infrastructuur en beschikbaarheid beheerd met Microsoft.

<a name="feature-drilldown"></a>

## <a name="feature-summary"></a>Functieoverzicht

| Category | Functies |
|----------|----------|
|Zoeken in volledige tekst en tekstanalyse | [Zoeken in volledige tekst](search-lucene-query-architecture.md) is een primair gebruiksvoorbeeld voor de meeste op zoekopdrachten gebaseerde apps. Query’s kunnen worden geformuleerd met behulp van een ondersteunde syntaxis. <br/><br/>[**Eenvoudige querysyntaxis**](https://docs.microsoft.com/rest/api/searchservice/simple-query-syntax-in-azure-search) biedt logische operators, zoekoperators voor woordgroepen, operators voor achtervoegsels, operators voor bewerkingsvolgorde.<br/><br/>[**Lucene-querysyntaxis**](https://docs.microsoft.com/rest/api/searchservice/lucene-query-syntax-in-azure-search) omvat alle bewerkingen in eenvoudige syntaxis, met uitbreidingen voor zoeken bij benadering, zoeken op nabijheid, termenverbetering en reguliere expressies.| 
| Gegevensintegratie | Azure Search-indexen accepteren gegevens uit elke willekeurige bron, mits ze zijn verzonden in een JSON-gegevensstructuur. <br/><br/> Voor ondersteunde gegevensbronnen in Azure kunt u optioneel gebruikmaken van [**indexeerfuncties**](search-indexer-overview.md) voor het automatisch verkennen van [Azure SQL Database](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md), [Azure Cosmos DB](search-howto-index-cosmosdb.md) of [ Azure Blob Storage](search-howto-indexing-azure-blob-storage.md) om de inhoud van uw zoekindex te synchroniseren met de primaire gegevensopslag. Azure Blob-indexeerfuncties kunnen *documenten kraken* om [grote bestandsindelingen te indexeren](search-howto-indexing-azure-blob-storage.md), zoals Microsoft Office, PDF en HTML-bestanden. |
| Taalkundige analyse | Analysefuncties zijn onderdelen die worden gebruikt om tekst te verwerken tijdens het indexeren en zoeken. Er zijn twee typen. <br/><br/>[**Aangepaste lexicale analysefuncties**](https://docs.microsoft.com/rest/api/searchservice/custom-analyzers-in-azure-search) worden gebruikt voor complexe zoekquery’s met behulp van fonetische overeenkomsten en reguliere expressies. <br/><br/>[**Taalanalysefuncties**](https://docs.microsoft.com/rest/api/searchservice/language-support) van Lucene of Microsoft worden gebruikt voor het intelligent verwerken van taalspecifieke taalkundige aspecten, zoals werkwoordtijden, geslacht, afwijkende meervoudsvormen voor zelfstandige naamwoorden, het opsplitsen van woorden, het afbreken van woorden (voor talen zonder spaties), en meer. |
| Op geografische locaties zoeken | Met Azure Search worden geografische locaties verwerkt, gefilterd en weergegeven. Zo worden gebruikers in staat gesteld om gegevens te verkennen op basis van de afstand van een zoekresultaat tot een fysieke locatie. [Bekijk deze video ](https://channel9.msdn.com/Shows/Data-Exposed/Azure-Search-and-Geospatial-Data) of [dit voorbeeld](https://github.com/Azure-Samples/search-dotnet-asp-net-mvc-jobs) voor meer informatie. |
| Functies voor de gebruikerservaring | [**Zoeksuggesties**](https://docs.microsoft.com/rest/api/searchservice/suggesters) kunnen worden ingeschakeld voor aangevulde query’s in een zoekbalk. Terwijl gebruikers delen van een zoekopdracht typen, worden de aanwezige documenten in uw index voorgesteld. <br/><br/>[**Facetnavigatie**](https://docs.microsoft.com/azure/search/search-faceted-navigation) is ingeschakeld via een enkele queryparameter. Met Azure Search wordt een facetnavigatiestructuur geretourneerd die u kunt gebruiken als de code achter een lijst met categorieën, voor zelfgestuurd filteren (bijvoorbeeld om catalogusitems te filteren op prijsbereik of merk). <br/><br/> [**Filters**](https://docs.microsoft.com/rest/api/searchservice/odata-expression-syntax-for-azure-search) kunnen worden gebruikt om facetnavigatie op te nemen in de gebruikersinterface van uw toepassing, het formuleren van query’s te verbeteren, en te filteren op basis van criteria die zijn opgegeven door gebruikers of ontwikkelaars. Maak filters met behulp van de OData-syntaxis.<br/><br/> Met [**Treffers markeren**](https://docs.microsoft.com/rest/api/searchservice/Search-Documents) wordt tekstopmaak toegepast op een overeenkomend trefwoord in zoekresultaten. U kunt kiezen welke velden gemarkeerde fragmenten retourneren.<br/><br/>[**Sorteren**](https://docs.microsoft.com/rest/api/searchservice/Search-Documents) wordt aangeboden voor meerdere velden via het indexschema en vervolgens tijdens het uitvoeren van een query in-/uitgeschakeld met een enkele zoekparameter.<br/><br/> [**Pagineren**](search-pagination-page-layout.md) en beperken van de zoekresultaten is eenvoudig dankzij het goed afgestemde besturingselement dat Azure Search biedt voor uw zoekresultaten.  
| Relevantie | [**Eenvoudig scoren**](/rest/api/searchservice/add-scoring-profiles-to-a-search-index) is een belangrijk voordeel van Azure Search. Scoreprofielen worden gebruikt om relevantie te modelleren als een functie van waarden in de documenten zelf. Zo wilt u bijvoorbeeld dat nieuwere producten of afgeprijsde producten hoger worden weergegeven in de zoekresultaten. U kunt scoreprofielen ook bouwen met behulp van labels voor persoonlijke scores op basis van de zoekvoorkeuren van klanten die u hebt bijgehouden en apart opgeslagen. |
| Controle en rapportage | [**Analytische gegevens over zoekverkeer**](search-traffic-analytics.md) worden verzameld en geanalyseerd om inzicht te krijgen in wat gebruikers in het zoekvak typen. <br/><br/>Metrische gegevens over query’s per seconde, latentie en beperkingen worden vastgelegd en gerapporteerd op portalpagina’s zonder dat hiervoor extra configuratie is vereist. U kunt ook eenvoudig het aantal indexen en documenten controleren zodat u de capaciteit naar behoefte kunt aanpassen. Zie [Servicebeheer](search-manage.md) voor meer informatie |
| Hulpprogramma's voor het ontwikkelen van prototypen en voor controle | In de portal kunt u de [**wizard Gegevens importeren**](search-import-data-portal.md) gebruiken om indexeerfuncties te configureren, Index Designer om een index te bouwen, en [**Search Explorer**](search-explorer.md) om query’s te testen en scoreprofielen te verfijnen. U kunt ook elke gewenste index openen om het bijbehorende schema te bekijken. |
| Infrastructuur | Het **maximaal beschikbare platform** zorgt ervoor dat de zoekservice uiterst betrouwbaar is. [Azure Search biedt een SLA voor 99,9% beschikbaarheid](https://azure.microsoft.com/support/legal/sla/search/v1_0/) als er naar behoren is geschaald.<br/><br/> Azure Search is een end-to-end-oplossing die **volledig beheerd en schaalbaar** is en vereist geen enkel infrastructuurbeheer. De service kan worden aangepast aan uw persoonlijke behoeften door in twee dimensies te schalen voor het verwerken van meer documentopslag, een hogere querybelasting, of beide.

## <a name="how-to-use-azure-search"></a>Het gebruik van Azure Search
### <a name="step-1-provision-service"></a>Stap 1: Service inrichten
U kunt een Azure Search-service instellen in [Azure Portal](https://portal.azure.com/) of met behulp van de [Azure Resource Management API](/rest/api/searchmanagement/). U kunt kiezen voor de gratis service die is gedeeld met andere abonnees, of u kunt een [betaalde laag](https://azure.microsoft.com/pricing/details/search/) kiezen met toegewezen resources die alleen voor uw service worden gebruikt. Voor betaalde lagen kunt u een service in twee dimensies schalen: 

- Voeg replica’s toe om de capaciteit voor het verwerken van zware querybelastingen te vergroten.   
- Voeg partities toe om meer documenten op te kunnen slaan. 

Door de opslag van documenten en de doorvoer van query’s afzonderlijk aan te pakken kunt u het gebruik van resources afstemmen op basis van productievereisten.

### <a name="step-2-create-index"></a>Stap 2: Index maken
Voordat u doorzoekbare inhoud kunt uploaden, moet u eerst een Azure Search-index definiëren. Een index is vergelijkbaar met een databasetabel waarin uw gegevens zijn opgeslagen, en u kunt zoekquery’s uitvoeren voor een index. U definieert het indexschema dat moet worden toegewezen, om de structuur van de documenten te weerspiegelen waarin u wilt zoeken, vergelijkbaar met de velden in een database.

U kunt een schema maken in Azure Portal of via een programma met behulp van de [.NET SDK](search-howto-dotnet-sdk.md) of [REST API](/rest/api/searchservice/).

### <a name="step-3-index-data"></a>Stap 3: Gegevens indexeren
Nadat u een index hebt gedefinieerd, bent u klaar om inhoud te uploaden. U kunt hiervoor een push- of een pull-model gebruiken.

Met het pull-model worden gegevens opgehaald uit externe gegevensbronnen. Het model wordt ondersteund met *indexeerfuncties* die aspecten van de gegevensopname stroomlijnen en automatiseren, zoals het verbinden met, en lezen en serialiseren van gegevens. Er zijn [indexeerfuncties](/rest/api/searchservice/Indexer-operations) beschikbaar voor Azure Cosmos DB, Azure SQL Database, Azure Blob Storage en SQL Server gehost op een Azure-VM. U kunt een indexeerfunctie configureren op-aanvraag of als geplande gegevensvernieuwing.

Het push-model wordt geboden via de SDK of REST API’s, en gebruikt om bijgewerkte documenten naar een index te verzenden. U kunt gegevens uit nagenoeg elke gegevensset pushen met behulp van de JSON-indeling. Zie [Add, update, or delete Documents](/rest/api/searchservice/addupdate-or-delete-documents) (Documenten toevoegen, bijwerken of verwijderen) of [How to use the .NET SDK](search-howto-dotnet-sdk.md) (De .NET SDK gebruiken) voor informatie over het laden van gegevens.

### <a name="step-4-search"></a>Stap 4: Zoeken
Nadat u een index hebt gevuld, kunt u [zoekquery’s verzenden](/rest/api/searchservice/Search-Documents) naar het service-eindpunt met behulp van eenvoudige HTTP-aanvragen met REST API of de .NET SDK.

## <a name="how-azure-search-compares"></a>Azure Search vergelijken

Klanten vragen vaak wat de verschillen zijn tussen Azure Search en andere zoekgerelateerde oplossingen. In de volgende tabel worden de verschillen samengevat.

| Vergeleken met | Belangrijke verschillen |
|--|--|
|Bing | Met [Bing Webzoekopdrachten-API](https://docs.microsoft.com/azure/cognitive-services/bing-web-search/) wordt in de indexen op Bing.com gezocht naar overeenkomsten voor termen die u hebt ingediend. Indexen zijn gebouwd uit HTML, XML en andere webinhoud op openbare sites. [Bing Aangepaste zoekopdrachten](https://docs.microsoft.com/azure/cognitive-services/bing-custom-search/) biedt dezelfde verkenningstechnologie voor typen webinhoud, met een bereik ingesteld op afzonderlijke websites.<br/><br/>Met Azure Search wordt gezocht in een door u gedefinieerde index, die is gevuld met gegevens en documenten waarvan u de eigenaar bent, vaak afkomstig uit verschillende bronnen. Azure Search heeft verkenningsmogelijkheden voor bepaalde gegevensbronnen via [indexeerfuncties](search-indexer-overview.md), maar u kunt elk JSON-document dat overeenstemt met uw indexschema, naar een enkele geconsolideerde resource pushen. |
|Zoeken in database | [Zoeken in volledige tekst van SQL Server](https://docs.microsoft.com/sql/relational-databases/search/full-text-search) is voor de inhoud in het DBMS, in SQL-tabellen. <br/><br/>Met Azure Search wordt inhoud uit heterogene bronnen opgeslagen. Het biedt gespecialiseerde functies voor het verwerken van tekst, zoals taalkundige en aangepaste analyse. De [engine voor zoekopdrachten in volledige tekst](search-lucene-query-architecture.md) in Azure Search is gebouwd op Apache Lucene, een industrienorm op het gebied van het ophalen van gegevens. <br/><br/>Een ander belangrijk punt is het gebruik van resources. Zoeken naar natuurlijke taal is vaak rekenintensief. Offloading van de zoekopdracht naar een toegewezen oplossing zorgt ervoor dat resources behouden blijven voor transactieverwerking. Door de zoekfunctie te externaliseren kunt u eenvoudig schalen naar het juiste queryvolume.|
|Toegewezen zoekoplossing | On-premises oplossingen of cloudserviceoplossingen zijn toegewezen zoekoplossingen met het volledige spectrum aan functies. Zoektechnologieën bieden gewoonlijk controle over het indexeren en doorzoeken van pijplijnen, toegang tot rijkere syntaxis voor filteren en het uitvoeren van query’s, beheer van rangen en relevantie, en functies voor zelfgestuurde en intelligente zoekopdrachten. <br/><br/>Toegewezen zoekoplossingen worden aangeboden als een cloudservice of als een zelfstandige server die on-premises wordt gehost of op een virtuele machine. Als u op zoek bent naar een [pasklare oplossing met minimale overhead, minimaal onderhoud en aanpasbare schaling](#cloud-service-advantage) is een cloudservice de juiste keuze voor u. <br/><br/>Binnen het cloudmodel bieden verschillende providers vergelijkbare basisfuncties, met Zoekopdracht in volledige tekst, op geografische locaties zoeken en de mogelijkheid om te gaan met een zekere dubbelzinnigheid van zoekinvoergegevens. Meestal bepaalt een [gespecialiseerde functie](#feature-drilldown), of het gemak en de algehele eenvoud van de API’s, de hulpprogramma’s en het beheer, welke oplossing het meest geschikt voor u is. |

Azure Search is van alle cloudproviders het sterkst op het gebied van zoeken in volledige tekst in inhoudsopslag en databases in Azure, voor apps die hoofdzakelijk afhankelijk zijn van het ophalen van gegevens en van inhoudsnavigatie. Belangrijke pluspunten zijn onder andere:

+ Azure-gegevensintegratie (verkenners) in de indexeringslaag
+ Azure Portal voor centraal beheer
+ Schaling, betrouwbaarheid en beschikbaarheid van wereldklasse in Azure
+ Taalkundige en aangepaste analyse met analysefuncties voor grondig zoeken in volledige tekst in 56 talen
+ [Kernfuncties die eigen zijn aan zoekgerichte apps](#feature-drilldown): scoren, facetten, suggesties, synoniemen, op geografische locaties zoeken, en meer.

> [!Note]
> Niet-Azure-gegevensbronnen worden volledig ondersteund, maar zijn gebaseerd op code-intensievere pushmethodes in plaats van op indexeerfuncties. Als u API’s gebruikt, kunt u elke JSON-documentverzameling doorsluizen naar een Azure Search-index.

Onder onze klanten bevinden zich degenen die het breedste scala aan functies in Azure Search gebruiken, onder andere onlinecatalogussen, Line-Of-Business-programma’s en toepassingen voor het ontdekken van documenten.

## <a name="rest-api--net-sdk"></a>REST API | .Net SDK

Hoewel veel taken in de portal kunnen worden uitgevoerd, is Azure Search bedoelt voor ontwikkelaars die de zoekfunctie willen integreren in bestaande toepassingen. De volgende programmeerinterfaces zijn beschikbaar.

|Platform |Beschrijving |
|-----|------------|
|[REST](/rest/api/searchservice/) | HTTP-opdrachten die worden ondersteund door een willekeurig programmeerplatform en een willekeurige programmeertaal, waaronder Xamarin, Java en JavaScript|
|[.NET SDK](search-howto-dotnet-sdk.md) | .NET-wrapper voor de REST API biedt efficiënte codering in C# en andere beheerde codetalen die zijn bedoeld voor .NET Framework |

## <a name="free-trial"></a>Gratis proefversie
Azure-abonnees kunnen [een service inrichten in de gratis laag](search-create-service-portal.md).

Als u geen abonnee bent, kunt u [gratis een Azure-account openen](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F). U krijgt een tegoed om de betaalde Azure-services uit te proberen. Als uw tegoed op is, kunt u het account behouden en de [gratis Azure-services](https://azure.microsoft.com/free/) gebruiken. Er worden nooit kosten in rekening gebracht bij uw creditcard tenzij u de instellingen expliciet wijzigt en aangeeft dat u wilt betalen.

U kunt ook [de voordelen voor MSDN-abonnees activeren](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F): bij uw MSDN-abonnement ontvangt u elke maand een tegoed dat u kunt gebruiken voor betaalde Azure-services. 

## <a name="how-to-get-started"></a>Hoe gaat u aan de slag

1. Maak een service in de [gratis laag](search-create-service-portal.md).

2. Volg een of meer van de volgende zelfstudies. 

  + In [How to use the .NET SDK](search-howto-dotnet-sdk.md) (.NET SDK gebruiken) worden de hoofdstappen voor beheerde code gedemonstreerd.  
  + In [Get started with the REST API](https://github.com/Azure-Samples/search-rest-api-getting-started) (Aan de slag met REST API) worden dezelfde stappen getoond met behulp van de REST API.  
  + In [Create your first index in the portal](search-get-started-portal.md) (Uw eerste index maken in de portal) leest u hoe u een index in de portal maakt met behulp van ingebouwde functies voor indexeren en het ontwikkelen van prototypen.   

Zoekmachines zijn de normale stuurprogramma’s voor het ophalen van informatie in mobiele apps, op het web en in zakelijke gegevensopslag. Azure Search biedt hulpprogramma’s voor het creëren van een zoekervaring die vergelijkbaar is met die op grote commerciële websites.

In deze 9 minuten durende video van programmamanager Liam Cavanagh leert u hoe het integreren van een zoekmachine uw app kan verbeteren. Korte demo´s gaan over de belangrijke functies in Azure Search en hoe een typische werkstroom eruitziet. 

>[!VIDEO https://channel9.msdn.com/Events/Connect/2016/138/player]
 
+ 0-3 minuten, gaat over de belangrijkste functies en gebruiksvoorbeelden.
+ 3-4 minuten, gaat over het inrichten van de service. 
+ 4-6 minuten, gaat over de wizard Gegevens importeren die wordt gebruikt om een index te maken met behulp van de ingebouwde vastgoedgegevensset.
+ 6-9 minuten, gaat over Search Explorer en verschillende query’s.


