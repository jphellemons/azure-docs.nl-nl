---
title: Firewall voor SQL-database van Azure Blockchain Workbench configureren
description: Leer hoe u een firewall voor de SQL-database van Azure Blockchain Workbench configureert.
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 5/4/2018
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: mmercuri
manager: femila
ms.openlocfilehash: afeea143f73fa4f7d3e373535007846a668616ab
ms.sourcegitcommit: fc64acba9d9b9784e3662327414e5fe7bd3e972e
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/12/2018
---
# <a name="configure-the-azure-blockchain-workbench-database-firewall"></a>Firewall voor database van Azure Blockchain Workbench configureren

In dit artikel wordt uitgelegd hoe u een firewallregel configureert met Azure Portal. Met behulp van firewallregels kunnen externe clients of toepassingen verbinding maken met uw Azure Blockchain Workbench-database.

## <a name="connect-to-the-blockchain-workbench-database"></a>Verbinding maken met een database van Blockchain Workbench

Verbinding maken met de database waar u een regel wilt configureren:

1. Meld u aan bij Azure Portal met een account met **Eigenaar**-machtigingen voor de resources van Azure Blockchain Workbench.
2. Kies **Resourcegroepen** in het linkernavigatievenster.
3. Kies de naam van de resourcegroep voor uw implementatie van Blockchain Workbench.
4. Selecteer **Type** om de lijst met resources te sorteren en kies vervolgens uw **SQL-server**.
5. In het voorbeeld met een resource-overzicht in de volgende schermafbeelding ziet u twee databases: *master* en *lsgn sdk*. U configureert de firewallregel in *lsgn sdk*.

![Resources van Azure Blockchain Workbench opvragen](media/blockchain-workbench-database-firewall/list-database-resources.png)

## <a name="create-a-database-firewall-rule"></a>Een firewallregel voor een database maken

Een firewallregel maken:

1. Kies de link naar de database 'lsgn sdk'.
2. Selecteer **Serverfirewall instellen** op de menubalk.

   ![Serverfirewall instellen](media/blockchain-workbench-database-firewall/configure-server-firewall.png)

3. Een regel maken voor uw organisatie:

   * Geef een waarde op voor **REGELNAAM**
   * Geef een IP-adres voor **Eerste IP-adres** van het adresbereik
   * Geef een IP-adres voor **Laatste IP-adres** van het adresbereik

   ![Firewallregel maken](media/blockchain-workbench-database-firewall/create-firewall-rule.png)

    > [!NOTE]
    > Als u alleen het IP-adres van uw computer wilt toevoegen, kiest u **+ IP-adres van client toevoegen**.
        
1. Selecteer **Opslaan** om de firewallconfiguratie op te slaan.
2. Test het IP-adresbereik dat u hebt geconfigureerd voor de database door verbinding te maken vanuit een toepassing of hulpprogramma, zoals SQL Server Management Studio.

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Databaseweergaven in Azure Blockchain Workbench](blockchain-workbench-database-views.md)