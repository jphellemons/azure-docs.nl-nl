---
title: bestand opnemen
description: bestand opnemen
services: iot-suite
author: dominicbetts
ms.service: iot-suite
ms.topic: include
ms.date: 04/24/2018
ms.author: dobett
ms.custom: include file
ms.openlocfilehash: 2c6f5cf2d89da0c2418ac58ca5d47a8aa05e732f
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/07/2018
---
# <a name="internet-of-things-security-best-practices"></a>Aanbevolen beveiligingsprocedures voor Internet der dingen

Beveiligen van een Internet der dingen (IoT)-infrastructuur vereist een strengere beveiliging-in-depth-strategie. Deze strategie moet u beveiliging van gegevens in de cloud, de integriteit van de gegevens onderweg te beschermen via het openbare internet en veilig apparaten inrichten. Elke laag groter beveiligingscontrole in de algehele infrastructuur is gebaseerd.

## <a name="secure-an-iot-infrastructure"></a>Een IoT-infrastructuur beveiligen

Deze strategie security-in-depth worden ontwikkeld en uitgevoerd met actieve deelname van verschillende spelers die betrokken zijn bij de productie, ontwikkeling en implementatie van IoT-apparaten en infrastructuur. Hier volgt een beschrijving van deze spelers.

* **IoT hardware fabrikant/integrator**: deze spelers zijn meestal de fabrikanten van IoT-hardware wordt geïmplementeerd, SI's hardware montage van verschillende fabrikanten of leveranciers bieden hardware voor een IoT-implementatie gemaakte of geïntegreerd met andere leveranciers.
* **IoT-oplossing developer**: de ontwikkeling van een IoT-oplossing gewoonlijk wordt uitgevoerd door de ontwikkelaar van een oplossing. Deze ontwikkelaar kan deel uitmaken van een intern team of een system integrator (SI) die zijn gespecialiseerd in deze activiteit. De ontwikkelaar van de IoT-oplossing kan verschillende onderdelen van de IoT-oplossing vanaf het begin te ontwikkelen, integreren van verschillende gebruiksklare of open source-onderdelen of oplossing accelerators met kleine aanpassing vast.
* **IoT-oplossing deployer**: na een IoT-oplossing is ontwikkeld, moet deze worden geïmplementeerd in het veld. Dit proces omvat de implementatie van hardware, koppeling van apparaten en implementatie van oplossingen in hardwareapparaten of de cloud.
* **IoT-oplossing operator**: nadat de IoT-oplossing is geïmplementeerd, langlopende bewerkingen, bewaking, upgrades en onderhoud is vereist. Deze taken kunnen worden uitgevoerd door een interne team dat bestaat uit gegevens technologiespecialisten, hardwarebewerkingen en onderhoud teams en domein specialisten die de juiste werking van algemene IoT-infrastructuur bewaken.

De volgende gedeelten vindt u aanbevolen procedures voor elk van deze spelers te ontwikkelen, implementeren en beheren van een beveiligde IoT-infrastructuur.

## <a name="iot-hardware-manufacturerintegrator"></a>IoT hardware fabrikant/integrator

Hieronder vindt u de aanbevolen procedures voor IoT-hardwarefabrikanten en hardware SI's.

* **Bereik van hardware aan de minimumvereisten**: het hardwareontwerp van de minimale functies die nodig zijn voor de werking van de hardware, en niets meer moet bevatten. Een voorbeeld is het USB-poorten zijn alleen indien nodig voor de bewerking van het apparaat. Deze extra functies opent u het apparaat voor ongewenste aanvalsvectoren die moeten worden vermeden.
* **Controleer hardware bewijs knoeien**: bouwen in mechanismen voor het detecteren van fysieke knoeien, zoals het openen van de dekking van het apparaat of een deel van het apparaat verwijderen. Deze signalen knoeien mogelijk deel uit van de gegevensstroom geüpload naar de cloud, die operators van deze gebeurtenissen kan een waarschuwing.
* **Bouwen om beveiligde hardware**: als omzet is toegestaan, beveiligingsfuncties zoals beveiligd en versleuteld storage opbouwen of functionaliteit op basis van Trusted Platform Module (TPM) worden opgestart. Deze functies de apparatuur meer beveiligen en het beschermen van de algemene IoT-infrastructuur te maken.
* **Beveiligen van upgrades**: Firmware bijwerken tijdens de levensduur van het apparaat zijn onvermijdelijk. Opbouwen van apparaten met beveiligde paden voor upgrades en cryptografische zekerheid van de firmware-versies kunt het apparaat niet veilig tijdens en na de upgrade.

## <a name="iot-solution-developer"></a>Ontwikkelaars van IoT-oplossing

Hier volgen de best practices voor ontwikkelaars van IoT-oplossing:

* **Ga als volgt beveiligde software development methodologie**: ontwikkeling van beveiligde software vereist a t/m nadenken over de beveiliging van het begin van het project helemaal tot aan de implementatie, testen en implementeren. De keuzes platforms, talen en hulpprogramma's, worden met deze methode beïnvloed. Microsoft Security Development Lifecycle biedt een stapsgewijze benadering voor het ontwikkelen van beveiligde software.
* **Kies open source software zorgvuldig**: Open-source software biedt de mogelijkheid snel om oplossingen te ontwikkelen. Bij het kiezen van open-source software, overweeg dan het activiteitenniveau van de community voor elk onderdeel van de open source. Een actieve community zorgt ervoor dat de software wordt ondersteund en dat problemen zijn gedetecteerd en opgelost. U kunt ook een project verborgen en inactieve open source software wordt mogelijk niet ondersteund en problemen zijn waarschijnlijk niet gedetecteerd.
* **Integreren met zorg**: veel software beveiligingsfouten bestaat op de grens van bibliotheken en -API. Functionaliteit die niet vereist voor de huidige implementatie zijn is mogelijk nog steeds beschikbaar is via een API-laag. Zorg dat u alle interfaces van onderdelen wordt geïntegreerd voor beveiligingsfouten controleren zodat algemene beveiliging.

## <a name="iot-solution-deployer"></a>Implementatie van IoT-oplossing

De volgende zijn aanbevolen procedures voor deployers van IoT-oplossing:

* **Hardware veilig implementeren**: IoT-implementaties vereisen hardware in niet-beveiligde locaties, zoals in openbare ruimten of zonder supervisie landinstellingen worden geïmplementeerd. In dergelijke situaties ervoor te zorgen dat de implementatie van de hardware fraudebestendig is voor zover maximaal wettelijk. Zorg ervoor dat ze veilig worden behandeld als het USB- of andere poorten beschikbaar op de hardware zijn. Veel aanvalsvectoren kunnen deze als toegangspunten gebruiken.
* **Verificatiesleutels veilig te houden**: tijdens de implementatie van elk apparaat vereist dat apparaat-id's en bijbehorende verificatiesleutels die worden gegenereerd door de cloudservice. Beveilig deze sleutels fysiek zelfs na de implementatie. Een willekeurige toets waarmee is geknoeid kan worden gebruikt door een kwaadwillende apparaat naar zich voordoen als een bestaand apparaat.

## <a name="iot-solution-operator"></a>IoT-oplossing operator

Hier volgen de best practices voor operators van IoT-oplossing:

* **Het systeem up-to-date houden**: Zorg ervoor dat de besturingssystemen van apparaten en alle apparaatstuurprogramma's worden bijgewerkt naar de meest recente versies. Als u automatische updates in Windows 10 (IoT of andere SKU's), Microsoft bijgehouden, biedt een beveiligd besturingssysteem voor de IoT-apparaten. Andere besturingssystemen (zoals Linux) ervoor up-to-date zorgt dat ze ook zijn beschermd tegen schadelijke aanvallen.
* **Bescherming tegen schadelijke activiteiten**: als het besturingssysteem toestaat, installeert u de nieuwste mogelijkheden voor antivirus- en antimalwaresoftware op elk besturingssysteem. Hierdoor kunt de meeste externe bedreigingen te verhelpen. U kunt de meeste moderne besturingssystemen tegen bedreigingen beveiligen door passende maatregelen te nemen.
* **Regelmatig controleren**: controle IoT-infrastructuur voor beveiligingsproblemen is sleutel bij het reageren op beveiligingsincidenten. De meeste besturingssystemen bieden ingebouwde logboekregistratie die vaak moet worden gecontroleerd om ervoor te zorgen dat er geen inbreuk op de beveiliging is opgetreden. Controle-informatie kan worden verzonden als een afzonderlijke telemetriestroom naar de cloudservice waarin deze kan worden geanalyseerd.
* **Fysiek de IoT-infrastructuur beveiligen**: de slechtste beveiligingsaanvallen tegen IoT-infrastructuur worden gestart met behulp van de fysieke toegang tot apparaten. Een belangrijk veiligheid procedure is als bescherming tegen kwaadaardig gebruik van USB-poorten en andere fysieke toegang. Een sleutel uncovering schendingen die hebben plaatsgevonden is logboekregistratie van fysieke toegang, zoals het gebruik van USB-poort. Windows 10 (IoT en andere SKU's) kunnen ook gedetailleerde logboekregistratie van deze gebeurtenissen.
* **Beveiligen van referenties van de cloud**: referenties voor Cloud-verificatie gebruikt voor het configureren en gebruiken van een IoT-implementatie zijn mogelijk de eenvoudigste manier om toegang te krijgen en een IoT-systeem. De referenties beveiligen door het wijzigen van het wachtwoord regelmatig, en geen deze referenties te gebruiken op openbare computers.

Mogelijkheden van andere IoT-apparaten variëren. Sommige apparaten mogelijk computers met algemene desktopbesturingssystemen en sommige apparaten mogelijk zeer lichte besturingssystemen worden uitgevoerd. De aanbevolen beveiligingsprocedures beschreven eerder mogelijk van toepassing op deze apparaten in verschillende mate. Indien opgegeven, moeten aanvullende beveiligings- en best practices uit de fabrikanten van deze apparaten worden gevolgd.

Sommige oudere en beperkte apparaten mogelijk niet hebben is speciaal ontworpen voor IoT-implementatie. Deze apparaten mogelijk niet over de mogelijkheid om te coderen van gegevens, verbinding maken met Internet of bieden geavanceerde controle. In dergelijke gevallen een moderne en veilige veldgateway statistische gegevens van oudere apparaten en voorzien in de beveiliging die vereist zijn voor deze apparaten via Internet verbinding te maken. Veld gateways kunnen bieden veilige verificatie, onderhandeling van versleutelde sessies, ontvangst van opdrachten uit de cloud en veel andere beveiligingsfuncties.