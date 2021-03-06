---
title: Een externe Azure App Service-omgeving maken
description: Wordt uitgelegd hoe u een App Service-omgeving maken bij het maken van een app of een zelfstandige
services: app-service
documentationcenter: na
author: ccompy
manager: stefsch
ms.assetid: 94dd0222-b960-469c-85da-7fcb98654241
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/13/2017
ms.author: ccompy
ms.openlocfilehash: 34248d75c190aa4636c39f087d399d946b589d58
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/20/2018
---
# <a name="create-an-external-app-service-environment"></a>Een externe App Service-omgeving maken #

Azure App Service Environment is een implementatie van Azure App Service in een subnet in een virtueel Azure-netwerk (VNet). Er zijn twee manieren om een AS-omgeving te implementeren:

- Met een VIP-adres op een extern IP-adres, vaak aangeduid als Externe AS-omgeving.
- Met het VIP op een interne IP-adres, vaak genoemd, een ILB-as-omgeving omdat het interne eindpunt, een interne load balancer (ILB is).

Dit artikel ziet u het maken van een externe as-omgeving. Zie voor een overzicht van de as-omgeving [een inleiding tot de App-serviceomgeving][Intro]. Zie voor meer informatie over het maken van een as-omgeving voor de ILB [maken en gebruiken een ILB-as-omgeving][MakeILBASE].

## <a name="before-you-create-your-ase"></a>Voordat u uw as-omgeving maken ##

Nadat u uw as-omgeving hebt gemaakt, kunt u het volgende niet wijzigen:

- Locatie
- Abonnement
- Resourcegroep
- VNet gebruikt
- Subnet dat wordt gebruikt
- De subnetgrootte van het

> [!NOTE]
> Als u een VNet kiest en geef een subnet, zorg ervoor dat deze grote biedt bij toekomstige groei. Een grootte van het is raadzaam `/25` met 128 adressen.
>

## <a name="three-ways-to-create-an-ase"></a>Drie manieren om te maken van een as-omgeving ##

Er zijn drie manieren voor het maken van een as-omgeving:

- **Tijdens het maken van een App Service-plan**. Deze methode maakt het as-omgeving en de App Service-abonnement in één stap.
- **Als een zelfstandige actie**. Deze methode maakt u een zelfstandige as-omgeving, die een as-omgeving helemaal leeg is. Deze methode is een meer geavanceerde proces voor het maken van een as-omgeving. U deze gebruiken voor het maken van een as-omgeving met een ILB.
- **Van een Azure Resource Manager-sjabloon**. Deze methode is bedoeld voor ervaren gebruikers. Zie voor meer informatie [een as-omgeving te maken van een sjabloon][MakeASEfromTemplate].

Een externe as-omgeving heeft een openbare VIP, wat betekent dat alle HTTP/HTTPS-verkeer naar de apps in de as-omgeving komt binnen via internet toegankelijke IP-adres. Een as met een ILB-omgeving heeft een IP-adres van het subnet dat wordt gebruikt door de as-omgeving. De apps die wordt gehost in een ILB-as-omgeving zijn niet rechtstreeks blootgesteld aan internet.

## <a name="create-an-ase-and-an-app-service-plan-together"></a>Samen een as-omgeving en een App Service-abonnement maken ##

De App Service-abonnement is een container van apps. Wanneer u een app in App Service maakt, kunt u kiezen of maken van een App Service-abonnement. App Service-omgevingen Houd App Service-abonnementen en App Service-abonnementen Houd apps.

Een as-omgeving maken bij het maken van een App Service-abonnement:

1. In de [Azure-portal](https://portal.azure.com/), selecteer **maken van een resource** > **Web en mobiel** > **Web-App**.

    ![Web-Apps maken][1]

2. Selecteer uw abonnement. De app en de as-omgeving worden in de dezelfde abonnementen gemaakt.

3. Selecteer of maak een resourcegroep. Met resourcegroepen kunt u Azure gerelateerde resources beheren als één eenheid. Resourcegroepen zijn ook nuttig wanneer u regels voor toegangsbeheer op basis van rollen tot stand voor uw apps brengen. Zie voor meer informatie de [overzicht van Azure Resource Manager][ARMOverview].

4. Selecteer uw besturingssysteem. 

    * Het is een nieuwe functie van de preview, die als host fungeert voor een Linux-app in een as-omgeving, zodat we raden aan dat u niet Linux apps toevoegen in een as-omgeving die productieworkloads momenteel wordt uitgevoerd. 
    * Toevoegen van een Linux-app in een as-omgeving, betekent het as-omgeving worden ook in de voorbeeldmodus. 

5. Selecteer de App Service-abonnement en selecteer vervolgens **nieuw**. Linux-web-apps en web-apps voor Windows kunnen niet in de dezelfde App Service-Plan, maar kunnen zich in dezelfde App Service-omgeving. 

    ![Nieuw App Service-plan][2]

6. In de **locatie** vervolgkeuzelijst, selecteer de regio waar u wilt maken van de as-omgeving. Als u een bestaande as-omgeving selecteert, wordt een nieuwe as-omgeving is niet gemaakt. De App Service-abonnement wordt gemaakt in de as-omgeving die u hebt geselecteerd. 

    > [!NOTE]
    > Linux op de as-omgeving op dit moment alleen in 6 gebieden is ingeschakeld: **VS-West, VS-Oost, West-Europa, Noord-Europa, Australië-Oost, Zuidoost-Azië.** Selecteer omdat Linux op de as-omgeving een preview-functie is, niet een as-omgeving die u hebt gemaakt voordat u deze preview.
    >

7. Selecteer **prijscategorie**, en kies een van de **geïsoleerd** prijzen SKU's. Als u ervoor kiest een **geïsoleerd** SKU-kaart en een locatie die niet een as-omgeving, een nieuwe as-omgeving wordt gemaakt op die locatie. U start het proces voor het maken van een as-omgeving, selecteren **Selecteer**. De **geïsoleerd** SKU is alleen beschikbaar in combinatie met een as-omgeving. U ook niet gebruiken andere prijscategorie SKU in een as-omgeving anders dan **geïsoleerd**. 

    * Voor de Linux op preview as-omgeving, wordt een korting van 50% worden toegepast op de geïsoleerde SKU (Er zijn geen korting op de platte-kosten voor de as-omgeving zelf).

    ![Het selecteren van een prijscategorie][3]

8. Voer de naam voor uw as-omgeving. Deze naam wordt gebruikt in de adresseerbare naam voor uw apps. Als de naam van de as-omgeving _appsvcenvdemo_, de domeinnaam is *. appsvcenvdemo.p.azurewebsites.net*. Als u een app met de naam maakt *mytestapp*, adresseerbare op mytestapp.appsvcenvdemo.p.azurewebsites.net is. U kunt witruimte niet gebruiken in de naam. Als u hoofdletters gebruikt, is de domeinnaam de totale kleine versie met die naam.

    ![Naam van een nieuwe App Service-abonnement][4]

9. Geef de details van Azure virtuele netwerken. Selecteer een **maken van nieuwe** of **Selecteer een bestaande**. De optie voor het selecteren van een bestaande VNet is alleen beschikbaar als u een VNet in de geselecteerde regio hebben. Als u selecteert **nieuw**, voer een naam voor het VNet. Een nieuwe Resource Manager VNet met deze naam wordt gemaakt. Dit maakt gebruik van de adresruimte `192.168.250.0/23` in de geselecteerde regio. Als u selecteert **bestaande selecteren**, moet u:

    a. Selecteer de VNet-Adresblok, hebt u meer dan één.

    b. Voer de subnetnaam van een nieuwe.

    c. Selecteer de grootte van het subnet. *Vergeet niet te selecteren van een groot genoeg biedt bij toekomstige groei van uw as-omgeving.* Het is raadzaam `/25`, waardoor 128 adressen heeft en een maximale grootte as-omgeving kan verwerken. Wordt niet aanbevolen `/28`, bijvoorbeeld omdat alleen 16 adressen beschikbaar zijn. Infrastructuur ten minste zeven adressen worden gebruikt en een andere 5 Azure-netwerken gebruikt. In een `/28` subnet, bent u links met een maximale schaal van 4 plan van App Service-exemplaren voor een externe as-omgeving en slechts 3 App Service-abonnement instanties voor een as ILB-omgeving.

    d. Selecteer het bereik van de IP-subnet.

10. Selecteer **maken** voor het maken van de as-omgeving. Dit proces maakt ook het App Service-abonnement en de app. De as-omgeving App Service-abonnement en de app zijn allemaal onder hetzelfde abonnement en dezelfde resourcegroep. Als uw as-omgeving een afzonderlijke resourcegroep moet of als u een ILB-as-omgeving moet, volg de stappen voor het maken van een as-omgeving op zichzelf.

## <a name="create-an-ase-and-a-linux-web-app-using-a-custom-docker-image-together"></a>Een as-omgeving en een Linux-web-app met een aangepaste Docker-installatiekopie samen maken

1. In de [Azure-portal](https://portal.azure.com/), **maken van een Resource** > **Web en mobiel** > **Web-App voor Containers.** 

    ![Web-Apps maken][7]

2. Selecteer uw abonnement. De app en de as-omgeving worden in de dezelfde abonnementen gemaakt.

3. Selecteer of maak een resourcegroep. Met resourcegroepen kunt u Azure gerelateerde resources beheren als één eenheid. Resourcegroepen zijn ook nuttig wanneer u regels voor toegangsbeheer op basis van rollen tot stand voor uw apps brengen. Zie voor meer informatie de [overzicht van Azure Resource Manager][ARMOverview].

4. Selecteer de App Service-abonnement en selecteer vervolgens **nieuw**. Linux-web-apps en web-apps voor Windows kunnen niet in de dezelfde App Service-Plan, maar kunnen zich in dezelfde App Service-omgeving. 

    ![Nieuw App Service-plan][8]

5. In de **locatie** vervolgkeuzelijst, selecteer de regio waar u wilt maken van de as-omgeving. Als u een bestaande as-omgeving selecteert, wordt een nieuwe as-omgeving is niet gemaakt. De App Service-abonnement wordt gemaakt in de as-omgeving die u hebt geselecteerd. 

    > [!NOTE]
    > Linux op de as-omgeving op dit moment alleen in 6 gebieden is ingeschakeld: **VS-West, VS-Oost, West-Europa, Noord-Europa, Australië-Oost, Zuidoost-Azië.** Selecteer omdat Linux op de as-omgeving een preview-functie is, niet een as-omgeving die u hebt gemaakt voordat u deze preview.
    >

6. Selecteer **prijscategorie**, en kies een van de **geïsoleerd** prijzen SKU's. Als u ervoor kiest een **geïsoleerd** SKU-kaart en een locatie die niet een as-omgeving, een nieuwe as-omgeving wordt gemaakt op die locatie. U start het proces voor het maken van een as-omgeving, selecteren **Selecteer**. De **geïsoleerd** SKU is alleen beschikbaar in combinatie met een as-omgeving. U ook niet gebruiken andere prijscategorie SKU in een as-omgeving anders dan **geïsoleerd**. 

    * Voor de Linux op preview as-omgeving, wordt een korting van 50% worden toegepast op de geïsoleerde SKU (Er zijn geen korting op de platte-kosten voor de as-omgeving zelf).

    ![Het selecteren van een prijscategorie][3]

7. Voer de naam voor uw as-omgeving. Deze naam wordt gebruikt in de adresseerbare naam voor uw apps. Als de naam van de as-omgeving _appsvcenvdemo_, de domeinnaam is *. appsvcenvdemo.p.azurewebsites.net*. Als u een app met de naam maakt *mytestapp*, adresseerbare op mytestapp.appsvcenvdemo.p.azurewebsites.net is. U kunt witruimte niet gebruiken in de naam. Als u hoofdletters gebruikt, is de domeinnaam de totale kleine versie met die naam.

    ![Naam van een nieuwe App Service-abonnement][4]

8. Geef de details van Azure virtuele netwerken. Selecteer een **maken van nieuwe** of **Selecteer een bestaande**. De optie voor het selecteren van een bestaande VNet is alleen beschikbaar als u een VNet in de geselecteerde regio hebben. Als u selecteert **nieuw**, voer een naam voor het VNet. Een nieuwe Resource Manager VNet met deze naam wordt gemaakt. Dit maakt gebruik van de adresruimte `192.168.250.0/23` in de geselecteerde regio. Als u selecteert **bestaande selecteren**, moet u:

    a. Selecteer de VNet-Adresblok, hebt u meer dan één.

    b. Voer de subnetnaam van een nieuwe.

    c. Selecteer de grootte van het subnet. *Vergeet niet te selecteren van een groot genoeg biedt bij toekomstige groei van uw as-omgeving.* Het is raadzaam `/25`, waardoor 128 adressen heeft en een maximale grootte as-omgeving kan verwerken. Wordt niet aanbevolen `/28`, bijvoorbeeld omdat alleen 16 adressen beschikbaar zijn. Infrastructuur ten minste zeven adressen worden gebruikt en een andere 5 Azure-netwerken gebruikt. In een `/28` subnet, bent u links met een maximale schaal van 4 plan van App Service-exemplaren voor een externe as-omgeving en slechts 3 App Service-abonnement instanties voor een as ILB-omgeving.

    d. Selecteer het bereik van de IP-subnet.

9.  Selecteer 'Container configureren'.
    * Voer de naam van uw aangepaste installatiekopie (u kunt Azure Container register, Docker-Hub en uw eigen persoonlijke register). Als u niet dat uw eigen aangepaste container gebruiken wilt, kunt u zojuist hebt brengen van uw code en de installatiekopie van een ingebouwde gebruiken met App-Service op Linux, met behulp van de bovenstaande instructies. 

    ! [Configureren Container] [9]

10. Selecteer **maken** voor het maken van de as-omgeving. Dit proces maakt ook het App Service-abonnement en de app. De as-omgeving App Service-abonnement en de app zijn allemaal onder hetzelfde abonnement en dezelfde resourcegroep. Als uw as-omgeving een afzonderlijke resourcegroep moet of als u een ILB-as-omgeving moet, volg de stappen voor het maken van een as-omgeving op zichzelf.


## <a name="create-an-ase-by-itself"></a>Zelf een as-omgeving maken ##

Als u een zelfstandige as-omgeving maakt, heeft niets in deze. Een leeg as-omgeving maakt nog steeds een maandelijkse kosten voor de infrastructuur. Volg deze stappen voor het maken van een as-omgeving met een ILB of maken van een as-omgeving in een eigen resourcegroep bevinden. Nadat u uw as-omgeving maakt, kunt u apps in deze maken met behulp van het normale proces. Selecteer uw nieuwe as-omgeving als de locatie.

1. Zoek de Azure Marketplace voor **App Service-omgeving**, of selecteer **maken van een resource** > **Web Mobile** > **App Service-omgeving**. 

2. Voer de naam van uw as-omgeving. Deze naam wordt gebruikt voor de apps die zijn gemaakt in de as-omgeving. Als de naam *mynewdemoase*, is de subdomeinnaam *. mynewdemoase.p.azurewebsites.net*. Als u een app met de naam maakt *mytestapp*, adresseerbare op mytestapp.mynewdemoase.p.azurewebsites.net is. U kunt witruimte niet gebruiken in de naam. Als u hoofdletters gebruikt, is de domeinnaam de totale kleine versie van de naam. Als u een ILB, wordt de naam van uw as-omgeving wordt niet gebruikt in een subdomein maar in plaats daarvan expliciet is opgegeven tijdens het maken van de as-omgeving.

    ![Naamgeving van de as-omgeving][5]

3. Selecteer uw abonnement. Dit abonnement is ook een die gebruikmaken van alle apps in de as-omgeving. U kunt geen uw as-omgeving plaatsen in een VNet dat in een ander abonnement.

4. Selecteer of geef een nieuwe resourcegroep. De resourcegroep die wordt gebruikt voor uw as-omgeving moet hetzelfde account dat wordt gebruikt voor uw VNet. Als u een bestaande VNet selecteert, wordt de selectie van de resourcegroep voor uw as-omgeving wordt bijgewerkt naar aanleiding van uw VNet. *U kunt een as-omgeving maken met een resourcegroep die verschilt van de resourcegroep VNet als u een Resource Manager-sjabloon gebruikt.* Zie voor informatie over het maken van een as-omgeving van een sjabloon [maken van een App Service-omgeving van een sjabloon][MakeASEfromTemplate].

    ![Resourcegroep selecteren][6]

5. Selecteer uw VNet en locatie. U kunt een nieuw VNet maken of een bestaande VNet selecteren: 

    * Als u een nieuw VNet selecteert, kunt u een naam en locatie opgeven. Als u van plan bent om Linux-apps te hosten op deze ASE, worden op dit moment alleen deze zes regio's ondersteund: **VS West, VS Oost, West-Europa, Noord-Europa, Australië Oost en Zuidoost-Azië.** 
    
    * De nieuwe VNet heeft de adres-bereik 192.168.250.0/23 en een subnet met de naam default. Het subnet is gedefinieerd als 192.168.250.0/24. U kunt alleen een Resource Manager VNet selecteren. De **VIP Type** selectie bepaalt als uw as-omgeving rechtstreeks toegankelijk zijn vanaf internet (extern) of als een ILB wordt gebruikt. Zie voor meer informatie over deze opties, [maken en gebruiken van een interne load balancer met een App Service-omgeving][MakeILBASE]. 

      * Als u selecteert **externe** voor de **VIP Type**, kunt u het aantal externe IP-adressen het systeem is gemaakt met voor SSL op basis van IP-doeleinden. 
    
      * Als u selecteert **intern** voor de **VIP Type**, moet u het domein dat gebruikmaakt van uw as-omgeving. U kunt een as-omgeving implementeren in een VNet die gebruikmaakt van openbare of particuliere adresbereiken. Voor het gebruik van een VNet met een openbare-adresbereik, moet u het VNet tevoren maken. 
    
    * Als u een bestaande VNet selecteert, wordt een nieuw subnet wordt gemaakt wanneer de as-omgeving wordt gemaakt. *U kunt een vooraf gemaakte subnet niet gebruiken in de portal. Als u een Resource Manager-sjabloon gebruikt, kunt u een as-omgeving met een bestaande subnet.* Zie voor informatie over het maken van een as-omgeving van een sjabloon [maken van een App Service-omgeving van een sjabloon][MakeASEfromTemplate].

## <a name="app-service-environment-v1"></a>App Service-omgeving v1 ##

U kunt nog steeds exemplaren van de eerste versie van App Service-omgeving (ASEv1) maken. Zoeken om te starten, die de Marketplace voor **App Service-omgeving v1**. U kunt de as-omgeving maken op dezelfde manier die u de zelfstandige as-omgeving maakt. Wanneer dit voltooid, wordt uw ASEv1 heeft twee front-ends en twee werknemers. Met ASEv1, moet u de front-ends en werknemers beheren. Ze zijn niet automatisch toegevoegd wanneer u uw App Service-abonnementen maakt. De front-ends fungeren als de HTTP/HTTPS-eindpunten en verkeer naar de werknemers verzenden. De werknemers zijn de functies die voor het hosten van uw apps. Nadat u uw as-omgeving hebt gemaakt, kunt u de hoeveelheid front-ends en werknemers aanpassen. 

Zie voor meer informatie over ASEv1, [Inleiding tot de App Service-omgeving v1][ASEv1Intro]. Zie voor meer informatie over het schalen, beheren en controleren van ASEv1, [het configureren van een App-serviceomgeving][ConfigureASEv1].

<!--Image references-->
[1]: ./media/how_to_create_an_external_app_service_environment/createexternalase-create.png
[2]: ./media/how_to_create_an_external_app_service_environment/createexternalase-aspcreate.png
[3]: ./media/how_to_create_an_external_app_service_environment/createexternalase-pricing.png
[4]: ./media/how_to_create_an_external_app_service_environment/createexternalase-embeddedcreate.png
[5]: ./media/how_to_create_an_external_app_service_environment/createexternalase-standalonecreate.png
[6]: ./media/how_to_create_an_external_app_service_environment/createexternalase-network.png
[7]: ./media/how_to_create_an_external_app_service_environment/createexternalase-createwafc.png
[8]: ./media/how_to_create_an_external_app_service_environment/createexternalase-aspcreatewafc.png
[8]: ./media/how_to_create_an_external_app_service_environment/createexternalase-configurecontainer.png



<!--Links-->
[Intro]: ./intro.md
[MakeExternalASE]: ./create-external-ase.md
[MakeASEfromTemplate]: ./create-from-template.md
[MakeILBASE]: ./create-ilb-ase.md
[ASENetwork]: ./network-info.md
[UsingASE]: ./using-an-ase.md
[UDRs]: ../../virtual-network/virtual-networks-udr-overview.md
[NSGs]: ../../virtual-network/security-overview.md
[ConfigureASEv1]: app-service-web-configure-an-app-service-environment.md
[ASEv1Intro]: app-service-app-service-environment-intro.md
[webapps]: ../app-service-web-overview.md
[mobileapps]: ../../app-service-mobile/app-service-mobile-value-prop.md
[Functions]: ../../azure-functions/index.yml
[Pricing]: http://azure.microsoft.com/pricing/details/app-service/
[ARMOverview]: ../../azure-resource-manager/resource-group-overview.md
