---
title: De gebruikersinterface van een gebruiker reis met aangepast beleid aanpassen | Microsoft Docs
description: Meer informatie over Azure Active Directory B2C aangepast beleid.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: article
ms.date: 04/25/2017
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 0980c79ccd9ebd170e747514bba712c498e1387c
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/04/2018
ms.locfileid: "34711906"
---
# <a name="customize-the-ui-of-a-user-journey-with-custom-policies"></a>De gebruikersinterface van een gebruiker reis met aangepast beleid aanpassen

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

> [!NOTE]
> Dit artikel is een geavanceerde beschrijving van de werking van UI-aanpassing en inschakelen met Azure AD B2C aangepast beleid, met behulp van het kader van de gebruikerservaring identiteit.


Een naadloze gebruikerservaring is de sleutel voor een oplossing voor business-to-consumer. Een naadloze gebruikerservaring is een ervaring op apparaat of de browser, waarbij traject van een gebruiker via de service is niet te onderscheiden van die van de klantenservice die ze gebruiken.

## <a name="understand-the-cors-way-for-ui-customization"></a>Inzicht in de CORS-manier voor aanpassing van de gebruikersinterface

Azure AD B2C kunt u het ontwerp-en-werking van de gebruikerservaring (UX) op de verschillende pagina's die worden geleverd en worden weergegeven door Azure AD B2C aanpassen met behulp van uw eigen beleid.

Daartoe Azure AD B2C code in uw consumenten browser wordt uitgevoerd en gebruikt de benadering van moderne en standard [Cross-Origin-Resource delen (CORS)](http://www.w3.org/TR/cors/) aangepaste inhoud laden vanuit een specifieke URL die u opgeeft in een aangepast beleid om te verwijzen naar uw HTML5/CSS-sjablonen. CORS is een mechanisme waarmee beperkte resources, zoals lettertypen, op een webpagina worden aangevraagd vanuit een ander domein buiten het domein waarvan de bron afkomstig is.

Vergeleken met de oude traditionele manier waarop de sjabloonpagina's eigendom zijn van de oplossing waar u opgegeven beperkt tekst en afbeeldingen, waarbij beperkt beheer van indeling en de werking is aangeboden voorloopspaties aan meer dan problemen voor een naadloze ervaring, de CORS-manier ondersteunt HTML5 en CSS en kunnen u:

- De inhoud te hosten en de oplossing injects clientscript met besturingselementen.
- Volledige controle over elke pixel van lay-out en zorgeloos hebben.

U kunt zoveel inhoudspagina's desgewenst door HTML5/CSS-bestanden naar gelang van toepassing op te geven.

> [!NOTE]
> Uit veiligheidsoverwegingen is het gebruik van JavaScript momenteel geblokkeerd om aan te passen. Als u wilt deblokkeren JavaScript, is gebruik van een aangepaste domeinnaam voor uw Azure AD B2C-tenant vereist.

In elk van uw sjablonen HTML5/CSS, bieden u een *anker* element, wat overeenkomt met de vereiste `<div id=”api”>` element in de HTML-code of de pagina inhoud zoals hierna illustreren. Azure AD B2C vereist dat alle inhoudspagina's deze specifieke div

```
<!DOCTYPE html>
<html>
  <head>
    <title>Your page content’s tile!</title>
  </head>
  <body>
    <div id="api"></div>
  </body>
</html>
```

Azure AD B2C-gerelateerde inhoud voor de pagina is opgenomen in deze div, terwijl de rest van de pagina jouw e-mailadres om te bepalen. De Azure AD B2C JavaScript-code in uw inhoud ophaalt en HTML injects in dit specifieke div-element. Azure AD B2C injects de volgende besturingselementen naar gelang van toepassing: kiezer besturingselement account, meld u bij besturingselementen, besturingselementen van meerdere factoren (momenteel telefonische) en kenmerk verzameling besturingselementen. Azure AD B2C zorgt ervoor dat alle besturingselementen zijn HTML5 compatibel is en toegankelijk is, alle besturingselementen kunnen volledig worden opgemaakt en die een versie van het besturingselement niet gaat.

De samengevoegde inhoud wordt uiteindelijk weergegeven als het dynamisch document met de consumer.

Om ervoor te zorgen dat alles werkt zoals verwacht, moet u het volgende doen:

- Zorg ervoor dat uw inhoud HTML5 compatibel is en toegankelijk is
- Zorg ervoor dat de inhoudsserver voor CORS is ingeschakeld.
- Fungeren inhoud via HTTPS.
- Absolute URL's gebruiken zoals https://yourdomain/content voor alle koppelingen en CSS-inhoud.

> [!TIP]
> Controleer of de site die u bij het hosten van uw inhoud op CORS is ingeschakeld en testen van CORS-aanvragen, kunt u de site http://test-cors.org/. Dankzij deze site, kunt u de CORS-aanvraag verzenden naar een externe server (om te testen of CORS wordt ondersteund) of de CORS-aanvraag verzenden naar een testserver (om het verkennen van bepaalde functies van CORS).

> [!TIP]
> De site http://enable-cors.org/ ook vormt een meer dan nuttige informatiebronnen op CORS.

Dankzij deze benadering op basis van CORS hebben eindgebruikers consistente ervaring tussen uw toepassing en de pagina's die worden bediend door Azure AD B2C.

## <a name="create-a-storage-account"></a>Create a storage account

Als een vereiste moet u een opslagaccount maken. U moet een Azure-abonnement om een Azure Blob Storage-account te maken. U kunt aanmelden met een gratis proefversie op de [Azure-website](https://azure.microsoft.com/pricing/free-trial/).

1. Open een browsersessie en navigeer naar de [Azure-portal](https://portal.azure.com).
2. Aanmelden met uw beheerdersreferenties.
3. Klik op **maken van een resource** > **opslag** > **opslagaccount**.  Een **storage-account maken** deelvenster wordt geopend.
4. In **naam**, Geef een naam voor de storage-account, bijvoorbeeld *contoso369b2c*. Deze waarde hoger aangeduid als *storageAccountName*.
5. Kies de gewenste selecties voor de prijscategorie, de resourcegroep en het abonnement. Zorg ervoor dat u hebt de **vastmaken aan Startboard** optie ingeschakeld. Klik op **Create**.
6. Ga terug naar het Startboard en klik op het opslagaccount dat u hebt gemaakt.
7. In de **Services** sectie, klikt u op **Blobs**. Een **Blob-service deelvenster** wordt geopend.
8. Klik op **+ Container**.
9. In **naam**, Geef een naam voor de container, bijvoorbeeld *b2c*. Deze waarde hoger aangeduid als *containerName*.
9. Selecteer **Blob** als de **toegangstype**. Klik op **Create**.
10. De container die u hebt gemaakt, wordt weergegeven in de lijst op de **Blob-service deelvenster**.
11. Sluit de **Blobs** deelvenster.
12. Op de **storage account deelvenster**, klikt u op de **sleutel** pictogram. Een **toegang sleutels deelvenster** wordt geopend.  
13. Noteer de waarde van **key1**. Deze waarde hoger genoemd *key1*.

## <a name="downloading-the-helper-tool"></a>Het hulpprogramma helper downloaden

1.  Download het hulpprogramma helper van [GitHub](https://github.com/azureadquickstarts/b2c-azureblobstorage-client/archive/master.zip).
2.  Sla de *B2C-AzureBlobStorage-Client-master.zip* bestand op uw lokale computer.
3.  Pak de inhoud van het bestand B2C-AzureBlobStorage-Client-master.zip op uw lokale schijf, bijvoorbeeld onder de **UI-aanpassing-Pack** map, die u maakt een *B2C-AzureBlobStorage-Client-master*submap.
4.  Opent u deze map en pak de inhoud van het archiefbestand *B2CAzureStorageClient.zip* binnen deze.

## <a name="upload-the-ui-customization-pack-sample-files"></a>De voorbeeldbestanden UI-aanpassing-Pack uploaden

1.  Met Windows Verkenner, Ga naar de map *B2C-AzureBlobStorage-Client-master* zich onder de *UI-aanpassing-Pack* map in de vorige sectie hebt gemaakt.
2.  Voer de *B2CAzureStorageClient.exe* bestand. Dit programma uploadt u alle bestanden in de map die u voor uw opslagaccount opgeeft en CORS-toegang voor deze bestanden.
3.  Wanneer u wordt gevraagd, geeft: een.  De naam van uw opslagaccount *storageAccountName*, bijvoorbeeld *contoso369b2c*.
    b.  De primaire toegangssleutel van uw azure-blobopslag *key1*, bijvoorbeeld *contoso369b2c*.
    c.  De naam van uw opslag blob storage-container *containerName*, bijvoorbeeld *b2c*.
    d.  Het pad van de *Starter Pack* voorbeeldbestanden bijvoorbeeld *... \B2CTemplates\wingtiptoys*.

Als u de voorgaande stappen hebt gevolgd de HTML5 en CSS-bestanden van de *UI-aanpassing-Pack* voor het fictieve bedrijf **wingtiptoys** nu wijst naar uw opslagaccount.  U kunt controleren dat de inhoud is geüpload goed door het openen van het deelvenster gerelateerde container in Azure portal. U kunt ook controleren of de inhoud is geüpload correct met het openen van de pagina vanuit een browser. Zie voor meer informatie [Azure Active Directory B2C: een helper hulpprogramma dat wordt gebruikt voor het demonstreren van de pagina gebruiker gebruikersinterface (UI) aanpassing functie](active-directory-b2c-reference-ui-customization-helper-tool.md).

## <a name="ensure-the-storage-account-has-cors-enabled"></a>Zorg ervoor dat het opslagaccount heeft CORS ingeschakeld

CORS (Cross-Origin Resource Sharing) moet zijn ingeschakeld op uw eindpunt voor Azure AD B2C om uw inhoud te laden. Dit komt doordat de inhoud wordt gehost op een ander domein dan het domein dat Azure AD B2C wordt de behoeve van de pagina.

Om te controleren of de opslag die zijn voor het hosten van uw inhoud op CORS ingeschakeld is, gaat u verder met de volgende stappen:

1. Open een browsersessie en navigeer naar de pagina *unified.html* met behulp van de volledige URL van de locatie in uw opslagaccount `https://<storageAccountName>.blob.core.windows.net/<containerName>/unified.html`. Bijvoorbeeld https://contoso369b2c.blob.core.windows.net/b2c/unified.html.
2. Navigeer naar http://test-cors.org. Deze site kunt u controleren of de pagina die u gebruikt CORS ingeschakeld heeft.  
<!--
![test-cors.org](../../media/active-directory-b2c-customize-ui-of-a-user-journey/test-cors.png)
-->

3. In **externe URL**, voer de volledige URL voor uw inhoud unified.html en klikt u op **aanvraag verzenden**.
4. Controleer de uitvoer in de **resultaten** sectie bevat *XHR status: 200*, wat aangeeft dat CORS is ingeschakeld.
<!--
![CORS enabled](../../media/active-directory-b2c-customize-ui-of-a-user-journey/cors-enabled.png)
-->
Het opslagaccount moet nu een blob-container met de naam bevatten *b2c* in de afbeelding met de volgende wingtiptoys sjablonen van de *Starter Pack*.

<!--
![Correctly configured storage account](../../articles/active-directory-b2c/media/active-directory-b2c-reference-customize-ui-custom/storage-account-final.png)
-->

De volgende tabel beschrijft het doel van de voorgaande HTML5-pagina's.

| HTML5-sjabloon | Beschrijving |
|----------------|-------------|
| *phonefactor.HTML* | Deze pagina kan worden gebruikt als een sjabloon voor een multi-factor authentication-pagina. |
| *resetpassword.html* | Deze pagina kan worden gebruikt als een sjabloon voor een wachtwoordpagina vergeten. |
| *selfasserted.html* | Deze pagina kan worden gebruikt als een sjabloon voor een sociale account aanmelden pagina, de registratiepagina voor een lokaal account of een lokale account-aanmeldingspagina. |
| *Unified.HTML* | Deze pagina kan worden gebruikt als een sjabloon voor een uniforme Meld u aan of de aanmeldingspagina. |
| *updateprofile.HTML* | Deze pagina kan worden gebruikt als een sjabloon voor een profiel update-pagina. |

## <a name="add-a-link-to-your-html5css-templates-to-your-user-journey"></a>Een koppeling naar uw HTML5/CSS-sjablonen naar uw reis gebruiker toevoegen

U kunt een koppeling naar uw HTML5/CSS-sjablonen toevoegen aan uw reis gebruiker een aangepast beleid door rechtstreeks te bewerken.

De aangepaste HTML5/CSS-sjablonen te gebruiken in uw reis gebruiker moeten worden opgegeven in een lijst met inhoud definities die kunnen worden gebruikt in deze trajecten gebruiker. Dien een optionele *<ContentDefinitions>* XML-element moet worden gedeclareerd onder de *<BuildingBlocks>* sectie van het aangepaste beleid XML-bestand.

De volgende tabel beschrijft de set van inhoud definitie-id's wordt herkend door de identiteit van de Azure AD B2C-engine en het type van pagina's dat is gekoppeld aan deze optreden.

| De definitie van de inhoud-ID | Beschrijving |
|-----------------------|-------------|
| *api.error* | **Foutpagina**. Deze pagina wordt weergegeven wanneer een uitzondering of een fout is opgetreden. |
| *api.idpselections* | **Id-provider selectiepagina**. Deze pagina bevat een lijst met de id-providers die de gebruiker uit tijdens het aanmelden kiezen kan. Deze providers zijn enterprise identiteitsproviders, sociale id-providers zoals Facebook en Google + of lokale accounts (op basis van e-mailadres of de gebruiker naam). |
| *api.idpselections.signup* | **Selectie van de id-provider voor registratie**. Deze pagina bevat een lijst met de id-providers die de gebruiker uit tijdens de registratie kiezen kan. Deze providers zijn enterprise identiteitsproviders, sociale id-providers zoals Facebook en Google + of lokale accounts (op basis van e-mailadres of de gebruiker naam). |
| *api.localaccountpasswordreset* | **Wachtwoordpagina vergeten**. Deze pagina bevat een formulier dat de gebruiker heeft voor het vervullen van voor het initiëren van hun wachtwoord opnieuw instellen.  |
| *api.localaccountsignin* | **Aanmeldingspagina voor lokaal account**. Deze pagina bevat een formulier aanmelden met de gebruiker in te vullen wanneer aanmelden met een lokaal account dat is gebaseerd op een e-mailadres of een gebruikersnaam. Het formulier kan een tekstinvoervak en wachtwoordvak vermelding bevatten. |
| *api.localaccountsignup* | **Lokaal account aanmeldingspagina**. Deze pagina bevat een aanmeldingsformulier hebt ingevuld dat de gebruiker heeft in te vullen wanneer aanmelden voor een lokaal account dat is gebaseerd op een e-mailadres of een gebruikersnaam. Het formulier kan andere invoer besturingselementen zoals tekstinvoervak, vermelding wachtwoordvak keuzerondje, één vervolgkeuzelijsten en meervoudige selectie selectievakjes bevatten. |
| *api.phonefactor* | **Multi-factor authentication-pagina**. Gebruikers kunnen hun telefoonnummers (met behulp van de tekst of stem) verifiëren tijdens het registreren of aanmelden op deze pagina. |
| *api.selfasserted* | **De aanmeldpagina sociale account**. Deze pagina bevat een aanmeldingsformulier hebt ingevuld dat de gebruiker heeft in te vullen tijdens het aanmelden met behulp van een bestaand account van een identiteitsprovider van sociale zoals Facebook of Google +. Deze pagina is vergelijkbaar met de voorgaande sociale account aanmeldpagina met uitzondering van de invoervelden wachtwoord. |
| *api.selfasserted.profileupdate* | **Update profielpagina**. Deze pagina bevat een formulier dat de gebruiker gebruiken kunt voor hun profiel bijwerken. Deze pagina is vergelijkbaar met de voorgaande sociale account aanmeldpagina met uitzondering van de invoervelden wachtwoord. |
| *api.signuporsignin* | **Unified registreren of aanmelden pagina**.  Deze pagina verwerkt beide registreren en aanmelden van gebruikers, die enterprise identiteitsproviders, sociale id-providers zoals Facebook of Google + of lokale accounts kunnen gebruiken.

## <a name="next-steps"></a>Volgende stappen
[Naslaginformatie: Inzicht in hoe aangepaste beleidsregels werken met de identiteit ervaring Framework in B2C](active-directory-b2c-reference-custom-policies-understanding-contents.md)
