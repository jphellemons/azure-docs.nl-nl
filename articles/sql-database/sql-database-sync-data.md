---
title: Synchroniseren van Azure SQL-gegevens (Preview) | Microsoft Docs
description: Dit overzicht introduceert Azure SQL-gegevenssynchronisatie (Preview)
services: sql-database
author: douglaslms
manager: craigg
ms.service: sql-database
ms.custom: data-sync
ms.topic: conceptual
ms.date: 04/10/2018
ms.author: douglasl
ms.reviewer: douglasl
ms.openlocfilehash: aa8f5e3b78a65c42840bbe831f5a4f2984a4a357
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34650397"
---
# <a name="sync-data-across-multiple-cloud-and-on-premises-databases-with-sql-data-sync-preview"></a>Synchronisatie van gegevens over meerdere cloud en on-premises databases met SQL synchroniseren van gegevens (Preview)

Synchroniseren van de SQL-gegevens is een service die is gebouwd op Azure SQL Database waarmee u de gegevens die u twee richtingen op meerdere SQL-databases en exemplaren van SQL Server selecteert synchroniseren.

Synchroniseren van gegevens is gebaseerd op het concept van een groep voor synchronisatie. Een groep voor synchronisatie is een groep met databases die u wilt synchroniseren.

Een groep voor synchronisatie heeft de volgende eigenschappen:

-   De **synchronisatieschema** wordt beschreven welke gegevens worden gesynchroniseerd.

-   De **Sync richting** kan in twee richtingen of kan stromen slechts in één richting. Dat wil zeggen, de richting van de synchronisatie kan worden *Hub lid* of *lid op Hub*, of beide.

-   De **synchronisatie-Interval** hoe vaak synchronisatie plaatsvindt.

-   De **Conflict resolutie beleid** is een niveau Groepsbeleid, wat kan *Hub wins* of *lid wins*.

Een hub en spoke-topologie gegevenssynchronisatie gebruikt om gegevens te synchroniseren. U definieert een van de databases in de groep als de Database van de Hub. De rest van de databases zijn lid databases. Synchronisatie doet zich alleen tussen de Hub en de afzonderlijke leden.
-   De **Hub Database** moet een Azure SQL Database.
-   De **lid databases** SQL-Databases, de lokale SQL Server-databases of de SQL Server-exemplaren op virtuele machines in Azure.
-   De **synchronisatiedatabase** bevat de metagegevens en het logboek voor synchroniseren van gegevens. De synchronisatiedatabase is dat een Azure SQL Database zich in dezelfde regio bevinden als de Database van de Hub. De synchronisatiedatabase is klant gemaakt en die eigendom zijn van de klant.

> [!NOTE]
> Als u een on-premises-database gebruikt, hebt u [configureren van een lokale agent](sql-database-get-started-sql-data-sync.md#add-on-prem).

![Gegevens tussen databases synchroniseren](media/sql-database-sync-data/sync-data-overview.png)

## <a name="when-to-use-data-sync"></a>Wanneer met het synchroniseren van gegevens

Synchroniseren van gegevens is handig in gevallen waarin gegevens moet tussen verschillende Azure SQL-Databases of SQL Server-databases up-to-date worden gehouden. Hier volgen de belangrijkste gebruiksvoorbeelden voor synchroniseren van gegevens:

-   **Hybride gegevenssynchronisatie:** met het synchroniseren van gegevens, kun je gegevens worden gesynchroniseerd tussen uw on-premises-databases en de Azure SQL-Databases hybride toepassingen inschakelen. Deze mogelijkheid kan beroep instellen voor klanten aan wie u van plan bent te verplaatsen naar de cloud en wilt enkele van de toepassing in Azure te plaatsen.

-   **Gedistribueerde toepassingen:** In veel gevallen is het nuttig om andere werkbelastingen scheiden over verschillende databases. Bijvoorbeeld als u een grote productiedatabase hebben, maar u moet ook een rapport of analytics werkbelasting uitvoeren op deze gegevens, is het handig om een tweede database voor deze extra belasting. Hierdoor minimaliseert de prestatie-invloed van de werkbelasting van uw productieomgeving. Synchroniseren van gegevens kunt u deze twee databases gesynchroniseerd houden.

-   **Globaal gedistribueerde toepassingen:** veel bedrijven verschillende regio's en zelfs meerdere landen omvatten. Om te beperken netwerklatentie, is het raadzaam om uw gegevens in een regio dicht bij u. U kunt databases eenvoudig in regio's over de hele wereld gesynchroniseerd houden met het synchroniseren van gegevens.

Synchroniseren van gegevens is niet geschikt is voor de volgende scenario's:

-   Herstel na noodgevallen

-   Lezen van schaal

-   ETL (OLTP met OLAP)

-   Migratie van on-premises SQL Server naar Azure SQL Database

## <a name="how-does-data-sync-work"></a>Hoe werkt synchroniseren van gegevens? 

-   **Bijhouden van gegevenswijzigingen:** synchroniseren van gegevens houdt wijzigingen met behulp van invoegen, bijwerken en verwijderen van triggers. De wijzigingen worden opgenomen in een tabel aan de in de database.

-   **Synchroniseren van gegevens:** synchroniseren van gegevens in een Hub en Spoke-model is ontworpen. De Hub wordt afzonderlijk gesynchroniseerd met elk lid. Wijzigingen van de Hub zijn gedownload naar het lid en vervolgens de wijzigingen van het lid worden geüpload naar de Hub.

-   **Het oplossen van conflicten:** synchroniseren van gegevens biedt twee opties voor conflictoplossing, *Hub wins* of *lid wins*.
    -   Als u selecteert *Hub wins*, de wijzigingen in de hub altijd overschreven, wijzigingen in het lid.
    -   Als u selecteert *lid wins*, de wijzigingen in de wijzigingen voor het overschrijven van lid in de hub. Als er meer dan één lid, afhankelijk van de uiteindelijke waarde waarvoor de eerste wordt gesynchroniseerd.

## <a name="sync-req-lim"></a> Vereisten en beperkingen

### <a name="general-considerations"></a>Algemene overwegingen

#### <a name="eventual-consistency"></a>Uiteindelijke consistentie
Omdat synchroniseren van gegevens op basis van een trigger, transactionele consistentie kan niet worden gegarandeerd. Microsoft zorgt ervoor dat alle uiteindelijk wijzigingen en synchroniseren van gegevens veroorzaakt geen gegevens verloren gaan.

#### <a name="performance-impact"></a>Prestatie-invloed
Gegevens synchroniseren gebruikt invoegen, bijwerken en verwijderen van triggers voor het bijhouden van wijzigingen. Tabellen wordt gemaakt in de database voor het bijhouden. Deze wijziging bijhouden activiteiten heeft dit gevolgen voor de werkbelasting van uw database. De servicelaag beoordelen en indien nodig te upgraden.

### <a name="general-requirements"></a>Algemene vereisten

-   Elke tabel moet een primaire sleutel hebben. De waarde van de primaire sleutel in elke rij niet te wijzigen. Als u de waarde van een primaire sleutel wijzigen moet, verwijdert u de rij en maak deze opnieuw met de nieuwe waarde voor de primaire sleutel. 

-   Snapshot-isolatie moet zijn ingeschakeld. Zie voor meer informatie [Snapshot-isolatie in SQL Server](https://docs.microsoft.com/dotnet/framework/data/adonet/sql/snapshot-isolation-in-sql-server).

### <a name="general-limitations"></a>Algemene beperkingen

-   Een tabel kan niet een identiteitskolom die niet de primaire sleutel hebben.

-   Een primaire sleutel kan niet het gegevenstype datum/tijd hebben.

-   De namen van objecten (databases, tabellen en kolommen) kunnen bevatten de afdrukbare tekens punt (.), vierkante linkerhaak ([) of rechts vierkante haak (]).

-   Azure Active Directory-verificatie wordt niet ondersteund.

#### <a name="unsupported-data-types"></a>Niet-ondersteunde gegevenstypen

-   FileStream

-   SQL/CLR UDT

-   XMLSchemaCollection (XML wordt ondersteund)

-   Cursor, Timestamp, Hierarchyid

#### <a name="limitations-on-service-and-database-dimensions"></a>Beperkingen met betrekking tot dimensies service en -database

| **Dimensies**                                                      | **Limiet**              | **Tijdelijke oplossing**              |
|-----------------------------------------------------------------|------------------------|-----------------------------|
| Maximum aantal synchronisatiegroepen die elke database kan deel uitmaken.       | 5                      |                             |
| Maximum aantal eindpunten in een enkel sync-groep              | 30                     | Meerdere synchronisatiegroepen maken |
| Maximum aantal lokale eindpunten in een enkel sync-groep. | 5                      | Meerdere synchronisatiegroepen maken |
| Database-, tabel-, schema-en kolomnamen                       | 50 tekens per naam |                             |
| Tabellen in een groep voor synchronisatie                                          | 500                    | Meerdere synchronisatiegroepen maken |
| Kolommen in een tabel in een groep voor synchronisatie                              | 1000                   |                             |
| Grootte van de rij gegevens in een tabel                                        | 24 mb                  |                             |
| Minimale synchronisatie-interval                                           | 5 minuten              |                             |
|||

## <a name="faq-about-sql-data-sync"></a>Veelgestelde vragen over het synchroniseren van de SQL-gegevens

### <a name="how-much-does-the-sql-data-sync-preview-service-cost"></a>Wat kost het synchroniseren van de SQL-gegevens (Preview)-service?

Er zijn geen kosten voor het synchroniseren van de SQL-gegevens (Preview)-service zelf tijdens de Preview.  Echter samenvoegen u nog steeds gegevensoverdracht kosten voor de verplaatsing van gegevens in uw exemplaar van SQL-Database. Zie voor meer informatie [prijzen SQL Database](https://azure.microsoft.com/pricing/details/sql-database/).

### <a name="what-regions-support-data-sync"></a>Welke regio's ondersteuning voor synchroniseren van gegevens?

Synchroniseren van de SQL-gegevens (Preview) is beschikbaar in alle openbare cloud-regio's.

### <a name="is-a-sql-database-account-required"></a>Is een SQL-Database-account vereist? 

Ja. U moet een SQL-Database-account voor het hosten van de Hub Database hebben.

### <a name="can-i-use-data-sync-to-sync-between-sql-server-on-premises-databases-only"></a>Kan ik synchroniseren van gegevens te synchroniseren tussen alleen lokale-databases in SQL Server gebruiken? 
Niet rechtstreeks. U kunt synchroniseren tussen de SQL Server-databases voor lokale indirect echter door het maken van een Hub-database in Azure en vervolgens de lokale databases toe te voegen aan de groep voor synchronisatie.

### <a name="can-i-use-data-sync-to-sync-between-sql-databases-that-belong-to-different-subscriptions"></a>Kan ik synchroniseren van gegevens te synchroniseren tussen de SQL-Databases die tot verschillende abonnementen behoren gebruiken?
Ja. U kunt synchroniseren tussen de SQL-Databases die deel uitmaken van de resourcegroepen die eigendom zijn van verschillende abonnementen behoren.
-   Als de abonnementen tot dezelfde tenant behoren en u machtigingen voor alle abonnementen hebt, kunt u de groep voor synchronisatie configureren in de Azure portal.
-   Anders moet u PowerShell gebruiken voor het toevoegen van de synchronisatie-leden die tot verschillende abonnementen behoren.
   
### <a name="can-i-use-data-sync-to-seed-data-from-my-production-database-to-an-empty-database-and-then-keep-them-synchronized"></a>Kan ik synchroniseren van gegevens voor seedgegevens van mijn productiedatabase in een lege database gebruiken, en vervolgens deze gesynchroniseerd houden? 
Ja. Het schema handmatig maken in de nieuwe database door het uitvoeren van scripts in het oorspronkelijke. Nadat u het schema maakt, moet u de tabellen toevoegen aan een groep voor synchronisatie met de gegevens kopiëren en het gesynchroniseerd houden.

### <a name="should-i-use-sql-data-sync-to-back-up-and-restore-my-databases"></a>Moet ik back-up en herstellen van mijn databases synchroniseren van de SQL-gegevens gebruiken?

Het is niet raadzaam om te synchroniseren van de SQL-gegevens (Preview) gebruiken voor het maken van een back-up van uw gegevens. U kunt geen back-up of herstellen naar een bepaald punt in tijd omdat synchronisaties synchroniseren van de SQL-gegevens (Preview) niet samengesteld zijn. Synchroniseren van de SQL-gegevens (Preview) is bovendien andere SQL-objecten, zoals opgeslagen procedures geen back-up en het equivalent van een herstelbewerking niet snel kan.

Zie voor een back-techniek aanbevolen, [kopiëren van een Azure SQL database](sql-database-copy.md).

### <a name="can-data-sync-sync-encrypted-tables-and-columns"></a>Kan de gegevenssynchronisatie versleutelde tabellen en kolommen synchroniseren?

-   Als een database altijd versleuteld gebruikt, kunt u alleen de tabellen en kolommen die zijn synchroniseren *niet* versleuteld. De versleutelde kolommen kunnen niet worden gesynchroniseerd omdat het synchroniseren van gegevens kan de gegevens niet ontsleutelen.

-   Als een kolom wordt gebruikt op kolomniveau-versleuteling (wissen), kunt u de kolom synchroniseren, zolang de rijomvang kleiner dan de maximale grootte van 24 Mb is. Synchroniseren van gegevens behandelt de kolom die is versleuteld met sleutel (wissen) als normale binaire gegevens. Voor het ontsleutelen van de gegevens op andere leden van de synchronisatie, moet u hetzelfde certificaat hebben.

### <a name="is-collation-supported-in-sql-data-sync"></a>Wordt de sortering in SQL-gegevenssynchronisatie ondersteund?

Ja. Synchroniseren van de SQL-gegevens ondersteunt sortering in de volgende scenario's:

-   Als de geselecteerde synchronisatie-schema-tabellen niet al zijn in uw databases hub of een lid en vervolgens bij het implementeren van de groep voor synchronisatie, maakt de service automatisch de bijbehorende tabellen en kolommen met de collatie-instellingen in de databases leeg doel hebt geselecteerd.

-   Als de tabellen worden gesynchroniseerd al in uw hub en de lid-databases bestaat, synchroniseren van de SQL-gegevens, moet de primaire-sleutelkolommen hebben dezelfde sortering tussen hub en lid databases voor een succesvolle implementatie van de groep voor synchronisatie. Er zijn geen lengtebeperkingen sortering op kolommen dan de primaire-sleutelkolommen.

### <a name="is-federation-supported-in-sql-data-sync"></a>Wordt federation ondersteund in SQL-gegevenssynchronisatie?

Hoofddatabase voor Federatie kan worden gebruikt in de Service SQL synchroniseren van gegevens (Preview) zonder een beperking. U kunt het eindpunt gefedereerde Database toevoegen aan de huidige versie van het synchroniseren van de SQL-gegevens (Preview).

## <a name="next-steps"></a>Volgende stappen

Zie de volgende onderwerpen voor meer informatie over SQL Data Sync:

-   [Azure SQL Data Sync instellen](sql-database-get-started-sql-data-sync.md)
-   [Aanbevolen procedures voor Azure SQL Data Sync](sql-database-best-practices-data-sync.md)
-   [Azure SQL Data Sync bewaken met Log Analytics](sql-database-sync-monitor-oms.md)
-   [Problemen oplossen met Azure SQL Data Sync](sql-database-troubleshoot-data-sync.md)

-   Voer PowerShell-voorbeelden uit die laten zien hoe u SQL Data Sync configureert:
    -   [PowerShell gebruiken om meerdere Azure SQL-databases te synchroniseren](scripts/sql-database-sync-data-between-sql-databases.md)
    -   [PowerShell gebruiken om te synchroniseren tussen een Azure SQL-database en een on-premises database](scripts/sql-database-sync-data-between-azure-onprem.md)

-   [De documentatie over de REST-API van SQL Data Sync downloaden](https://github.com/Microsoft/sql-server-samples/raw/master/samples/features/sql-data-sync/Data_Sync_Preview_REST_API.pdf?raw=true)

Zie de volgende onderwerpen voor meer informatie over SQL Database:

-   [Wat is de Azure SQL Database-service?](sql-database-technical-overview.md)
-   [Database Lifecycle Management (DLM)](https://msdn.microsoft.com/library/jj907294.aspx)
