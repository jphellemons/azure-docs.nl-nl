---
title: Een Azure Service Fabric Windows-containertoepassing maken | Microsoft Docs
description: In deze snelstart maakt uw eerste Windows-containertoepassing in Azure Service Fabric.
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: vturecek
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: quickstart
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 04/30/2018
ms.author: ryanwi
ms.custom: mvc
ms.openlocfilehash: d78dbc9a32e804e37eb76047edcc050482df5761
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/07/2018
---
# <a name="quickstart-deploy-a-service-fabric-windows-container-application-on-azure"></a>Snelstart: een Service Fabric Windows-containertoepassing implementeren in Azure
Azure Service Fabric is een platform voor gedistribueerde systemen waarmee u schaalbare en betrouwbare microservices en containers implementeert en beheert. 

Er zijn geen wijzigingen in uw toepassing vereist om een bestaande toepassing in een Windows-container uit te voeren in een Service Fabric-cluster. In deze snelstartgids ziet u hoe u een vooraf gebouwde Docker-containerinstallatiekopie in een Service Fabric-toepassing implementeert. Wanneer u klaar bent, hebt u een actieve Windows Server 2016 Nano Server en IIS-container. In deze snelstartgids wordt beschreven hoe u een Windows-container implementeert. Lees [deze snelstartgids](service-fabric-quickstart-containers-linux.md) als u een Linux-container wilt implementeren.

![IIS-standaardwebpagina][iis-default]

In deze snelstart leert u de volgende zaken:

* Een Docker-containerinstallatiekopie verpakken
* Communicatie configureren
* De Service Fabric-toepassing bouwen en inpakken
* De containertoepassing implementeren naar Azure

## <a name="prerequisites"></a>Vereisten
* Een Azure-abonnement (u kunt een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) maken).
* Een ontwikkelcomputer waarop wordt uitgevoerd:
  * Visual Studio 2015 of Visual Studio 2017.
  * [Service Fabric SDK en hulpprogramma's](service-fabric-get-started.md).

## <a name="package-a-docker-image-container-with-visual-studio"></a>Een Docker-containerinstallatiekopie verpakken met Visual Studio
De Service Fabric SDK en hulpprogramma's bieden een servicesjabloon waarmee u een container kunt implementeren in een Service Fabric-cluster.

Start Visual Studio als 'Beheerder'.  Selecteer **Bestand** > **Nieuw** > **Project**.

Selecteer **Service Fabric-toepassing**, geef deze de naam MyFirstContainer en klik op **OK**.

Selecteer **Container** in de lijst met sjablonen voor **gehoste containers en toepassingen**.

In **Naam van installatiekopie** voert u 'microsoft/iis:nanoserver' in, de [Windows Server Nano Server en IIS-basisinstallatiekopie](https://hub.docker.com/r/microsoft/iis/). 

Configureer de toewijzing van de containerpoort naar de hostpoort zó, dat binnenkomende aanvragen voor de service op poort 80 worden toegewezen aan poort 80 van de container.  Stel **Containerpoort** in op '80' en **Hostpoort** op '80'.  

Geef uw service de naam 'MyContainerService' en klik op **OK**.

![Dialoogvenster voor nieuwe service][new-service]

## <a name="create-a-cluster"></a>Een cluster maken
U kunt voor het implementeren van de toepassing naar een cluster in Azure een cluster van derden gebruiken. Clusters van derden zijn gratis, tijdelijke Service Fabric-clusters die worden gehost op Azure en uitgevoerd door het Service Fabric-team. Iedereen kan hier toepassingen implementeren en meer te weten komen over het platform.  Het cluster gebruikt één zelfondertekend certificaat voor beveiliging van knooppunt-naar-knooppunt en client-naar-knooppunt. Party-clusters ondersteunen containers. Als u besluit uw eigen cluster in te stellen en te gebruiken, moet het cluster worden uitgevoerd op een SKU die ondersteuning biedt voor containers (zoals Windows Server 2016 Datacenter met Containers).

Meld u aan en [neem deel aan een Windows-cluster](http://aka.ms/tryservicefabric). Download het PFX-certificaat naar uw computer door op de koppeling **PFX** te klikken. Klik op de koppeling **Hoe kan ik verbinding maken met een beveiligd Party-cluster?** en kopieer het certificaatwachtwoord. Het certificaat, het certificaatwachtwoord en de waarde van het **verbindingseindpunt** worden in volgende stappen gebruikt.

![PFX en verbindingseindpunt](./media/service-fabric-quickstart-containers/party-cluster-cert.png)

> [!Note]
> Er zijn per uur een beperkt aantal Party-clusters beschikbaar. Als er een fout optreedt wanneer u zich probeert aan te melden voor een Party-cluster, kunt u een bepaalde tijd wachten en het opnieuw proberen, of kunt u deze stappen in de zelfstudie [Een .NET-app implementeren](https://docs.microsoft.com/azure/service-fabric/service-fabric-tutorial-deploy-app-to-party-cluster#deploy-the-sample-application) volgen om een Service Fabric-cluster in uw Azure-abonnement te maken en daarin de toepassing te implementeren. Het cluster dat is gemaakt met behulp van Visual Studio ondersteunt containers. Nadat u de toepassing in uw cluster hebt geïmplementeerd en geverifieerd, kunt u verdergaan naar [Volledig voorbeeld van de manifesten voor de Service Fabric-toepassing en -service](#complete-example-service-fabric-application-and-service-manifests) in deze quickstart. 
>

Op een Windows-computer installeert u het PFX-bestand in het certificaatarchief *CurrentUser\My*.

```powershell
PS C:\mycertificates> Import-PfxCertificate -FilePath .\party-cluster-873689604-client-cert.pfx -CertStoreLocation Cert:\CurrentUser\My -Password (ConvertTo-SecureString 873689604 -AsPlainText -Force)


  PSParentPath: Microsoft.PowerShell.Security\Certificate::CurrentUser\My

Thumbprint                                Subject
----------                                -------
3B138D84C077C292579BA35E4410634E164075CD  CN=zwin7fh14scd.westus.cloudapp.azure.com
``` 

## <a name="deploy-the-application-to-azure-using-visual-studio"></a>De toepassing publiceren in Azure met Visual Studio
Nu de toepassing klaar is, kunt u deze rechtstreeks vanuit Visual Studio implementeren naar een cluster.

Klik met de rechtermuisknop op **MyFirstContainer** in Solution Explorer en kies **Publiceren**. Het dialoogvenster Publiceren wordt weergegeven.

Kopieer het **verbindingseindpunt** van het cluster van derden naar het veld **Verbindingseindpunt**. Bijvoorbeeld `zwin7fh14scd.westus.cloudapp.azure.com:19000`. 

Klik op **Publish**.

Elke toepassing in het cluster moet een unieke naam hebben.  Clusters van derden vormen echter een openbare, gedeelde omgeving en er kan een conflict met een bestaande toepassing optreden.  Als er een naamconflict is, wijzigt u de naam van het Visual Studio-project en voert u de implementatie opnieuw uit.

Open een browser en ga naar het **verbindingseindpunt** dat is opgegeven op de pagina met het cluster van een derde. U kunt de URL eventueel vooraf laten gaan door de schema-id, `http://`, en de poort, `:80`, aan het einde toevoegen. Bijvoorbeeld http://zwin7fh14scd.westus.cloudapp.azure.com:80. U ziet de IIS-standaardwebpagina: ![IIS-standaardwebpagina][iis-default]

## <a name="next-steps"></a>Volgende stappen
In deze snelstartgids hebt u de volgende zaken geleerd:

* Een Docker-containerinstallatiekopie verpakken
* Communicatie configureren
* De Service Fabric-toepassing bouwen en inpakken
* De containertoepassing implementeren naar Azure

Meer informatie over het werken met Windows-containers in Service Fabric vindt u in de zelfstudie voor Windows-containerapps.

> [!div class="nextstepaction"]
> [Een Windows-containerapp maken](./service-fabric-host-app-in-a-container.md)

[iis-default]: ./media/service-fabric-quickstart-containers/iis-default.png
[publish-dialog]: ./media/service-fabric-quickstart-containers/publish-dialog.png
[new-service]: ./media/service-fabric-quickstart-containers/NewService.png
