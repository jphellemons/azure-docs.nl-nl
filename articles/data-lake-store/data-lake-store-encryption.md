---
title: Versleuteling in Azure Data Lake Store | Microsoft Docs
description: Versleuteling in Azure Data Lake Store helpt u uw gegevens te beveiligen, beveiligingsbeleid voor uw onderneming te implementeren en te voldoen aan wettelijke vereisten. In dit artikel vindt u een overzicht van het ontwerp en worden een aantal technische aspecten van de implementatie besproken.
services: data-lake-store
documentationcenter: ''
author: esung22
manager: ''
editor: ''
ms.assetid: ''
ms.service: data-lake-store
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 03/26/2018
ms.author: yagupta
ms.openlocfilehash: 2328f7e233025d9f9ee9113aa28fb74754dd9193
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2018
---
# <a name="encryption-of-data-in-azure-data-lake-store"></a>Versleuteling van gegevens in Azure Data Lake Store

Versleuteling in Azure Data Lake Store helpt u uw gegevens te beveiligen, beveiligingsbeleid voor uw onderneming te implementeren en te voldoen aan wettelijke vereisten. In dit artikel vindt u een overzicht van het ontwerp en worden een aantal technische aspecten van de implementatie besproken.

Data Lake Store biedt ondersteuning voor versleuteling van gegevens in rust en doorvoer. Data Lake Store ondersteunt on-by-default, transparante versleuteling van data-at-rest. Hier wordt iets gedetailleerder uitgelegd wat deze termen betekenen:

* **On-by-default**: wanneer u een nieuwe Data Lake Store-account maakt, wordt versleuteling standaard ingeschakeld. Daarna worden gegevens die zijn opgeslagen in de Data Lake Store vóór het opslaan op permanente media altijd versleuteld. Dit is het gedrag voor alle gegevens en kan niet worden gewijzigd nadat u een account hebt gemaakt.
* **Transparant**: Data Lake Store versleutelt gegevens automatisch voordat ze permanent worden gemaakt en ontsleutelt gegevens automatisch voordat ze worden opgehaald. De versleuteling wordt door een beheerder geconfigureerd en beheerd op het niveau van de Data Lake Store. Er worden geen wijzigingen aangebracht aan de API's voor gegevenstoegang. Er zijn dus geen wijzigingen vereist in toepassingen en services die interactie hebben met Data Lake Store vanwege versleuteling.

Gegevens tijdens overdracht (ook wel gegevens in beweging genoemd) worden ook altijd versleuteld in Data Lake Store. Naast het versleutelen van gegevens voorafgaand aan het opslaan op permanente media, worden de gegevens tijdens overdracht ook altijd beveiligd met behulp van HTTPS. HTTPS is het enige protocol dat wordt ondersteund voor de Data Lake Store REST-interfaces. Het volgende diagram toont hoe de gegevens in Data Lake Store worden versleuteld:

![Diagram van gegevensversleuteling in Data Lake Store](./media/data-lake-store-encryption/fig1.png)


## <a name="set-up-encryption-with-data-lake-store"></a>Versleuteling instellen met Data Lake Store

Versleuteling voor Data Lake Store wordt ingesteld tijdens het maken van het account en is standaard altijd ingeschakeld. U kunt de sleutels zelf beheren of Data Lake Store toestaan ze voor u te beheren (dit is de standaardinstelling).

Zie de [Introductie](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-get-started-portal) voor meer informatie.

## <a name="how-encryption-works-in-data-lake-store"></a>De werking van versleuteling in Data Lake Store

Dit artikel bevat informatie over het beheren van hoofdversleutelingssleutels en uitleg over de drie verschillende typen sleutels die u voor gegevensversleuteling voor Data Lake Store kunt gebruiken.

### <a name="master-encryption-keys"></a>Hoofdversleutelingssleutels

Data Lake Store biedt twee modi voor het beheer van hoofdversleutelingssleutels (MEK's). Op dit moment wordt ervan uitgegaan dat de hoofdversleutelingssleutel de sleutel op het hoogste niveau is. Toegang tot de hoofdversleutelingssleutel is vereist voor het ontsleutelen van gegevens die zijn opgeslagen in Data Lake Store.

De twee modi voor het beheer van de hoofdversleutelingssleutel zijn als volgt:

*   Door service beheerde sleutels
*   Door klant beheerde sleutels

In beide modi wordt de hoofdversleutelingssleutel beveiligd door opslag in Azure Key Vault. Key Vault is een volledig beheerde, zeer veilige service in Azure die kan worden gebruikt ter bescherming van de cryptografische sleutels. Zie voor meer informatie [Key Vault](https://azure.microsoft.com/services/key-vault).

Hier volgt een korte vergelijking van de mogelijkheden van de twee modi voor het beheren van de MEK's.

|  | Door service beheerde sleutels | Door klant beheerde sleutels |
| --- | --- | --- |
|Hoe worden gegevens opgeslagen?|Altijd versleuteld voordat deze worden opgeslagen.|Altijd versleuteld voordat deze worden opgeslagen.|
|Waar wordt de hoofdversleutelingssleutel opgeslagen?|Key Vault|Key Vault|
|Worden versleutelingssleutels opgeslagen buiten Key Vault? |Nee|Nee|
|Kan de MEK worden opgehaald uit Key Vault?|Nee. Eenmaal opgeslagen in de Key Vault kan de MEK alleen worden gebruikt voor versleuteling en ontsleuteling.|Nee. Eenmaal opgeslagen in de Key Vault kan de MEK alleen worden gebruikt voor versleuteling en ontsleuteling.|
|Wie is eigenaar van de Key Vault-instantie en de MEK?|De Data Lake Store-service|U bent eigenaar van de Key Vault-instantie in uw eigen Azure-abonnement. De MEK in de Key Vault kan worden beheerd door software of hardware.|
|Kunt u de toegang tot de MEK voor de Data Lake Store-service intrekken?|Nee|Ja. U kunt toegangscontrolelijsten in de Key Vault beheren en kunt toegangsbeheervermeldingen voor de service-ID voor de Data Lake Store-service verwijderen.|
|Kunt u de MEK permanent verwijderen?|Nee|Ja. Als u de MEK uit de Key Vault verwijdert, kunnen de gegevens in het Data Lake Store-account door niemand meer worden ontsleuteld, met inbegrip van de Data Lake Store-service. <br><br> Als u een expliciete back-up van de MEK maakt voordat u deze uit de Key Vault verwijdert, kan deze worden hersteld en kunnen de gegevens vervolgens worden hersteld. Als u echter geen back-up van de MEK hebt gemaakt voordat u deze uit de Key Vault verwijdert, kunnen de gegevens in het Data Lake Store-account daarna nooit meer worden ontsleuteld.|


Afgezien van het verschil wat betreft wie de MEK en de Key Vault-instantie waarin deze zich bevindt beheert, is de rest van het ontwerp hetzelfde voor beide modi.

Het is belangrijk het volgende te onthouden wanneer u de modus voor de hoofdversleutelingssleutels kiest:

*   U kunt kiezen of u door de klant beheerde sleutels of door de service beheerde sleutels wilt gebruiken wanneer u een Data Lake Store-account inricht.
*   Nadat een Data Lake Store-account is ingericht, kan de modus niet meer worden gewijzigd.

### <a name="encryption-and-decryption-of-data"></a>Versleuteling en ontsleuteling van gegevens

Er zijn drie soorten sleutels die worden gebruikt in het ontwerp van gegevensversleuteling. De volgende tabel geeft een samenvatting:

| Sleutel                   | Afkorting | Gekoppeld aan | Opslaglocatie                             | Type       | Opmerkingen                                                                                                   |
|-----------------------|--------------|-----------------|----------------------------------------------|------------|---------------------------------------------------------------------------------------------------------|
| Hoofdversleutelingssleutel | MEK          | Een Data Lake Store-account | Key Vault                              | Asymmetrisch | Deze kan worden beheerd door Data Lake Store of door u.                                                              |
| Gegevensversleutelingssleutel   | DEK          | Een Data Lake Store-account | Permanente opslag, beheerd door Data Lake Store-service | Symmetrisch  | De DEK is versleuteld door de MEK. De versleutelde DEK is hetgeen wat wordt opgeslagen op persistente media. |
| Blokversleutelingssleutel  | BEK          | Een gegevensblok | Geen                                         | Symmetrisch  | De BEK wordt afgeleid van de DEK en het gegevensblok.                                                      |

Het volgende diagram illustreert deze concepten:

![Sleutels bij gegevensversleuteling](./media/data-lake-store-encryption/fig2.png)

#### <a name="pseudo-algorithm-when-a-file-is-to-be-decrypted"></a>Pseudo-algoritme wanneer een bestand moet worden ontsleuteld:
1.  Controleer of de DEK voor het Data Lake Store-account zich in de cache bevindt en klaar voor gebruik is.
    - Als dat niet het geval is, leest u de versleutelde DEK uit de permanente opslag en stuurt u deze naar de Key Vault voor ontsleuteling. Sla de ontsleutelde DEK op in het cachegeheugen. Deze is nu gereed voor gebruik.
2.  Voor elk gegevensblok in het bestand:
    - Lees het versleutelde gegevensblok uit de permanente opslag.
    - Genereer de BEK uit de DEK en het versleutelde gegevensblok.
    - Gebruik de BEK om gegevens te ontsleutelen.


#### <a name="pseudo-algorithm-when-a-block-of-data-is-to-be-encrypted"></a>Pseudo-algoritme wanneer een gegevensblok moeten worden versleuteld:
1.  Controleer of de DEK voor het Data Lake Store-account zich in de cache bevindt en klaar voor gebruik is.
    - Als dat niet het geval is, leest u de versleutelde DEK uit de permanente opslag en stuurt u deze naar de Key Vault voor ontsleuteling. Sla de ontsleutelde DEK op in het cachegeheugen. Deze is nu gereed voor gebruik.
2.  Genereer een unieke BEK voor het gegevensblok uit de DEK.
3.  Versleutel het gegevensblok met de BEK met behulp van AES-256-codering.
4.  Sla het versleutelde gegevensblok op in permanente opslag.

> [!NOTE] 
> De DEK wordt altijd versleuteld door de MEK opgeslagen, op permanente media of in het cachegeheugen.

## <a name="key-rotation"></a>Sleutelroulatie

Wanneer u door de klant beheerde sleutels gebruikt, kunt u de MEK rouleren. Zie de pagina [Aan de slag](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-get-started-portal) voor informatie over het instellen van een Data Lake Store-account met door de klant beheerde sleutels.

### <a name="prerequisites"></a>Vereisten

Bij het instellen van het Data Lake Store-account, hebt u ervoor gekozen uw eigen sleutels te gebruiken. Deze optie kan niet worden gewijzigd nadat het account is gemaakt. In de volgende stappen wordt ervan uitgegaan dat u door de klant beheerde sleutels gebruikt (u hebt uw eigen sleutels uit Key Vault gekozen).

Als u de standaardopties voor versleuteling gebruikt, moet u er rekening mee houden dat uw gegevens altijd worden versleuteld met sleutels die worden beheerd door Data Lake Store. Bij deze optie hebt u niet de mogelijkheid om sleutels te rouleren, omdat ze worden beheerd door Data Lake Store.

### <a name="how-to-rotate-the-mek-in-data-lake-store"></a>De MEK rouleren in Data Lake Store

1. Meld u aan bij [Azure Portal](https://portal.azure.com/).
2. Blader naar de Key Vault-instantie waarin de sleutels die zijn gekoppeld aan uw Data Lake Store-account zijn opgeslagen. Selecteer **Sleutels**.

    ![Schermafdruk van Key Vault](./media/data-lake-store-encryption/keyvault.png)

3.  Selecteer de sleutel die is gekoppeld aan uw Data Lake Store-account en maak een nieuwe versie van deze sleutel. Houd er rekening mee dat Data Lake Store momenteel alleen ondersteuning biedt voor sleutelroulatie naar een nieuwe versie van een sleutel. Roulatie naar een andere sleutel wordt niet ondersteund.

   ![Schermafdruk van het venster Sleutels met de nieuwe versie gemarkeerd](./media/data-lake-store-encryption/keynewversion.png)

4.  Blader naar het Data Lake Storage-account en selecteer **Versleuteling**.

    ![Schermafdruk van Data Lake Store-opslagaccountvenster met versleuteling gemarkeerd](./media/data-lake-store-encryption/select-encryption.png)

5.  Er wordt een bericht weergegeven dat een nieuwe sleutelversie van de sleutel beschikbaar is. Klik op **Sleutel rouleren** om de sleutel naar de nieuwe versie bij te werken.

    ![Schermafdruk van Data Lake Store-venster met het bericht en Sleutel rouleren gemarkeerd](./media/data-lake-store-encryption/rotatekey.png)

Deze bewerking duurt minder dan twee minuten en er is geen verwachte uitvaltijd vanwege het rouleren van de sleutel. Nadat de bewerking is voltooid, wordt de nieuwe versie van de sleutel gebruikt.

> [!IMPORTANT]
> Nadat de sleutelroulatiebewerking voltooid is, wordt de oude versie van de sleutel niet meer actief gebruikt voor het versleutelen van uw gegevens.  In zeldzame gevallen van onverwachte fouten waardoor zelfs redundante exemplaren van uw gegevens zijn getroffen, kunnen gegevens echter mogelijk worden hersteld vanuit een back-up waarvoor nog de oude sleutel wordt gebruikt. Bewaar een kopie van de vorige versie van de versleutelingssleutel, om ervoor te zorgen dat uw gegevens in deze zeldzame omstandigheden toegankelijk zijn. Zie [Disaster recovery guidance for data in Data Lake Store](data-lake-store-disaster-recovery-guidance.md) (Richtlijnen voor herstel na noodgevallen voor gegevens in Data Lake Store) voor best practices voor het plannen van herstel na noodgevallen. 