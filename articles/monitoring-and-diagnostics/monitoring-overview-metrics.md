---
title: Overzicht van metrische gegevens in Microsoft Azure | Microsoft Docs
description: Overzicht van metrische gegevens en het gebruik ervan in Microsoft Azure
author: anirudhcavale
manager: orenr
editor: ''
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: 405ec51c-0946-4ec9-b535-60f65c4a5bd1
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/05/2018
ms.author: ancav
ms.openlocfilehash: ceabefa47b7627b8a9f952d487f78a96e338838d
ms.sourcegitcommit: 3017211a7d51efd6cd87e8210ee13d57585c7e3b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/06/2018
ms.locfileid: "34824741"
---
# <a name="overview-of-metrics-in-microsoft-azure"></a>Overzicht van metrische gegevens in Microsoft Azure
Dit artikel wordt beschreven wat metrische gegevens zijn in Microsoft Azure, hun voordelen en het gebruik ervan.  

## <a name="what-are-metrics"></a>Wat zijn de metrische gegevens?
Monitor voor Azure kunt u telemetrie om meer inzicht verkrijgen in de prestaties en de status van uw workloads in Azure gebruiken. De belangrijkste type Azure telemetriegegevens is de metrische gegevens die (ook wel prestatiemeteritems) die door de meeste Azure-resources. Monitor voor Azure biedt verschillende manieren configureren en gebruiken van deze metrische gegevens voor bewaking en probleemoplossing.

## <a name="what-are-the-characteristics-of-metrics"></a>Wat zijn de kenmerken van metrische gegevens?
Metrische gegevens hebben de volgende kenmerken:

* Alle metrische gegevens hebt **frequentie van één minuut** (tenzij anders opgegeven in de definitie van een metriek). U ontvangt een metrische waarde elke minuut van uw resource zodat u bijna real-time inzicht in de status en gezondheid van uw resource.
* Metrische gegevens zijn **beschikbaar onmiddellijk**. U hoeft niet te opt-in- of aanvullende diagnostische gegevens in te stellen.
* U hebt toegang tot **93 dagen** voor elke metriek. U kunt snel zoeken op de recente en een maandelijkse trends in de prestaties of de status van de resource.
* Sommige metrische naam / waarde-paar kenmerken aangeroepen kan hebben **dimensies**. Hiermee kunt u verder segmenteren en een waarde in een duidelijker manier verkennen.

## <a name="what-can-you-do-with-metrics"></a>Wat kunt u doen met metrische gegevens
Metrische gegevens kunt u de volgende taken uitvoeren:


- Configureren van een metriek **Waarschuwing regel waarmee u een melding verzendt of duurt automatische actie** wanneer de metriek overschrijdt de drempelwaarde die u hebt ingesteld. Acties worden beheerd via [actiegroepen](monitoring-action-groups.md). Voorbeeld van de acties omvatten e-mailadres, telefoonnummer en SMS-berichten voor het aanroepen van een webhook, het starten van een runbook en meer. **Automatisch schalen** is een speciale geautomatiseerde actie waarmee u kunt schalen u bent een resource omhoog en omlaag om de werklast verwerken nog voortdurend kosten lagere wanneer niet laden. U kunt een regel van de instelling voor automatisch schalen schalen in- of configureren op basis van een metriek een drempelwaarde overschrijden.
- **Route** alle metrische gegevens aan *Application Insights* of *logboekanalyse* om in te schakelen instant analytics, zoeken en aangepaste waarschuwingen op metrische gegevens van uw resources. U kunt ook de metrische gegevens om te streamen een *Event Hub*, zodat u kunt vervolgens te routeren naar Azure Stream Analytics of aangepaste apps voor vrijwel in realtime analyse. U instellen kunt de Event Hub met behulp van diagnostische instellingen voor streaming.
- **Archief** de geschiedenis van de prestaties of de status van de bron voor naleving, controle of offline rapportagedoeleinden.  Bij het configureren van diagnostische instellingen voor uw resource, kunt u uw metrische gegevens routeren naar Azure Blob-opslag.
- Gebruik de **Azure-portal** openen om te detecteren, en alle metrische gegevens weergeven wanneer u een resource selecteren en de metrische gegevens in een grafiek te tekenen. U kunt de prestaties van uw resources (zoals een VM, website of logische app) bijhouden dat de grafiek aan uw dashboard vastmaken.  
- **Geavanceerde analyses uitvoeren** of rapportage over trends prestatie- of informatie over het gebruik van de bron.
- **Query** metrische gegevens met behulp van de PowerShell-cmdlets of de REST-API voor Cross-Platform.
- **Verbruiken** de metrische gegevens via de nieuwe Azure Monitor REST-API's.

  ![Routering van metrische gegevens in Azure Monitor](./media/monitoring-overview-metrics/Metrics_Overview_v4.png)

## <a name="access-metrics-via-the-portal"></a>Toegang tot metrische gegevens via de portal
Hier volgt een snel overzicht van hoe een metrische grafiek te maken met behulp van de Azure-portal.

### <a name="to-view-metrics-after-creating-a-resource"></a>Metrische gegevens na het maken van een resource weergeven
1. Open Azure Portal.
2. Maak een Azure App Service-website.
3. Nadat u een website gemaakt, gaat u naar de **overzicht** blade van de website.
4. U kunt bekijken nieuwe metrische gegevens als een **bewaking** tegel. U kunt de tegel bewerken en meer metrische gegevens te selecteren.

   ![Metrische gegevens van een resource in de Azure-Monitor](./media/monitoring-overview-metrics/MetricsOverview1.png)

### <a name="to-access-all-metrics-in-a-single-place"></a>Voor toegang tot alle metrische gegevens op één plaats
1. Open Azure Portal.
2. Navigeer naar de nieuwe **Monitor** tabblad en vervolgens selecteert de **metrische gegevens** optie eronder.
3. Selecteer uw abonnement, resourcegroep en de naam van de resource in de vervolgkeuzelijst.
4. De lijst beschikbare metrische gegevens weergeven. Selecteer vervolgens de metriek u geïnteresseerd bent in en het tekenen.
5. U kunt het vastmaken aan het dashboard door te klikken op de pincode op de rechterbovenhoek.

   ![Toegang tot alle metrische gegevens op één plaats in de Azure-Monitor](./media/monitoring-overview-metrics/MetricsOverview2.png)

> [!NOTE]
> U toegang hebt tot hostniveau metrische gegevens van virtuele machines (op een Azure Resource Manager gebaseerde) en virtuele-machineschaalsets zonder aanvullende diagnostische instellingen. Deze nieuwe hostniveau metrische gegevens zijn beschikbaar voor Windows en Linux-exemplaren. Deze metrische gegevens zijn niet te verwarren met de Guest-OS-niveau metrische gegevens die u hebt toegang tot wanneer u Azure diagnostische gegevens op uw virtuele machines of virtuele-machineschaalset sets inschakelen. Zie voor meer informatie over het configureren van diagnostische gegevens, [wat is Microsoft Azure Diagnostics](../azure-diagnostics.md).
>
>

Monitor voor Azure heeft ook een nieuwe metrische gegevens voor grafieken ervaring die beschikbaar zijn in preview. Deze ervaring kan gebruikers metrische gegevens uit meerdere bronnen in een grafiek van de overlay. Gebruikers kunnen ook worden getekend, segment, en multidimensionale metrische gegevens met behulp van deze nieuwe metrische gegevens voor grafieken ervaring filteren. Voor meer informatie [Klik hier](https://aka.ms/azuremonitor/new-metrics-charts)

## <a name="access-metrics-via-the-rest-api"></a>Toegang tot metrische gegevens via de REST-API
Azure metrische gegevens zijn toegankelijk via de Azure-Monitor API's. Er zijn twee API's die u helpen detecteren en toegang tot metrische gegevens:

* Gebruik de [Azure Monitor metriek definities REST-API](https://docs.microsoft.com/rest/api/monitor/metricdefinitions) voor toegang tot de lijst met metrische gegevens en dimensies, die beschikbaar zijn voor een service.
* Gebruik de [REST API voor de metrische gegevens van de Monitor van de Azure](https://docs.microsoft.com/rest/api/monitor/metrics) segmenteren, filteren en toegang tot de werkelijke metrische gegevens.

> [!NOTE]
> In dit artikel bevat informatie over de metrische gegevens via de [nieuwe API voor metrieken](https://docs.microsoft.com/rest/api/monitor/) voor Azure-resources. De API-versie voor de nieuwe metrische definities en metrische gegevens API's is 2018-01-01. De verouderde metrische definities en metrische gegevens kunnen worden geopend met de API-versie 2014-04-01.
>
>

Zie voor een meer gedetailleerd overzicht met de Azure-Monitor REST API's, [Azure Monitor REST-API-overzicht](monitoring-rest-api-walkthrough.md).

## <a name="export-metrics"></a>Metrische gegevens exporteren
Gaat u naar de **diagnostische instellingen** blade onder de **Monitor** tabblad en de opties voor exporteren van metrische gegevens weergeven. U kunt selecteren metrische gegevens (en diagnostische logboeken) worden doorgestuurd naar de Blob-opslag naar Azure Event Hubs of met logboekanalyse voor use cases die eerder werden vermeld in dit artikel.

 ![Opties voor exporteren van metrische gegevens die in de Azure-Monitor](./media/monitoring-overview-metrics/MetricsOverview3.png)

U kunt dit configureren via Resource Manager-sjablonen, [PowerShell](insights-powershell-samples.md), [Azure CLI](insights-cli-samples.md), of [REST-API's](https://msdn.microsoft.com/library/dn931943.aspx).

> [!NOTE]
> Het verzenden van multidimensionale metrische gegevens via diagnostische instellingen wordt momenteel niet ondersteund. Metrische gegevens met dimensies worden geëxporteerd als platte eendimensionale metrische gegevens, als totaal van alle dimensiewaarden.
>
> *Een voorbeeld*: de metriek 'Binnenkomende berichten' voor een Event Hub kan worden verkend en uitgezet op wachtrijniveau. Maar wanneer de metriek wordt geëxporteerd via diagnostische instellingen, geeft de metriek alle binnenkomende berichten in alle wachtrijen in de Event Hub aan.
>
>

## <a name="take-action-on-metrics"></a>Metrische gegevens van actie ondernemen
Als u meldingen ontvangen of geautomatiseerde acties ondernemen met metrische gegevens, kunt u regels voor waarschuwingen of instellingen voor automatisch schalen configureren.

### <a name="configure-alert-rules"></a>Regels voor waarschuwingen configureren
U kunt regels voor waarschuwingen configureren op de metrische gegevens. Deze regels voor waarschuwingen kunnen controleren als een waarde een bepaalde drempelwaarde is overschreden. Er zijn twee metrische waarschuwingsmethoden mogelijkheden die worden aangeboden door Azure bewaken.

Metrische waarschuwingen: ze vervolgens kunnen u een melding via e-mail of starten van een webhook die kan worden gebruikt om een aangepast script uitvoeren. U kunt ook de webhook gebruiken voor het product van derden integraties configureren.

 ![Metrische gegevens en regels voor waarschuwingen in de Azure-Monitor](./media/monitoring-overview-metrics/MetricsOverview4.png)

Nieuwere metrische waarschuwingen hebben de mogelijkheid voor het bewaken van meerdere metrische gegevens en drempelwaarden voor een resource en vervolgens een melding via een [actie groep](/monitoring-action-groups.md). Meer informatie over [nieuwere waarschuwingen hier](https://aka.ms/azuremonitor/near-real-time-alerts).


### <a name="autoscale-your-azure-resources"></a>Automatisch schalen van uw Azure resources
Sommige Azure-resources ondersteuning voor het schalen of van meerdere exemplaren worden uw workloads verwerken. Automatisch schalen is van toepassing op App Service (Web-Apps), virtuele-machineschaalsets en klassieke Azure-Cloud-Services. U kunt regels voor automatisch schalen om te schalen of wanneer een bepaalde meetwaarde die van invloed is op uw werkbelasting overschrijdt de drempelwaarde die u opgeeft. Zie voor meer informatie [overzicht van automatisch schalen](monitoring-overview-autoscale.md).

 ![Metrische gegevens en automatisch schalen in Azure Monitor](./media/monitoring-overview-metrics/MetricsOverview5.png)

## <a name="learn-about-supported-services-and-metrics"></a>Meer informatie over ondersteunde services en metrische gegevens
U kunt een gedetailleerde lijst met alle ondersteunde services en hun metrische gegevens weergeven [Azure Monitor metrische gegevens--ondersteunde metrische gegevens per resourcetype](monitoring-supported-metrics.md).

## <a name="next-steps"></a>Volgende stappen
Raadpleeg de koppelingen in dit artikel. Bovendien kunt u meer over:  

* [Algemene metrische gegevens voor automatisch schalen](insights-autoscale-common-metrics.md)
* [Het maken van regels voor waarschuwingen](insights-alerts-portal.md)
* [Logboeken van de Azure-opslag met logboekanalyse analyseren](../log-analytics/log-analytics-azure-storage.md)
