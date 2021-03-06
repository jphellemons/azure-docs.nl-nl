---
title: SSL-verbindingen voor de Azure-Database voor MySQL
description: Informatie voor het configureren van Azure-Database voor MySQL en de bijbehorende toepassingen juist gebruik van SSL-verbindingen
services: mysql
author: JasonMAnderson
ms.author: janders
editor: jasonwhowell
manager: kfile
ms.service: mysql-database
ms.topic: article
ms.date: 02/28/2018
ms.openlocfilehash: f59d5eab9772515a3c59f887a48d597d27bab135
ms.sourcegitcommit: c765cbd9c379ed00f1e2394374efa8e1915321b9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/28/2018
---
# <a name="ssl-connectivity-in-azure-database-for-mysql"></a>SSL-verbindingen in Azure voor MySQL-Database
Azure MySQL-Database ondersteunt verbindingen van uw database-server met clienttoepassingen met Secure Sockets Layer (SSL). Het afdwingen van SSL-verbindingen tussen uw databaseserver en clienttoepassingen zorgt dat u bent beschermt tegen 'man in the middle'-aanvallen omdat de gegevensstroom tussen de server en uw toepassing wordt versleuteld.

## <a name="default-settings"></a>Standaardinstellingen
Standaard wordt de database-service zo dat SSL-verbindingen vereisen bij het verbinden met MySQL.  Het is raadzaam om te voorkomen dat de SSL-optie indien mogelijk uitschakelen. 

Bij het inrichten van een nieuwe Azure-Database voor de MySQL-server via de Azure portal en CLI kan afdwinging van SSL-verbindingen is standaard ingeschakeld. 

Tekenreeksen voor databaseverbindingen voor verschillende programmeertalen worden weergegeven in de Azure-portal. De verbindingsreeksen zijn de vereiste parameters SSL verbinding maken met uw database. Selecteer uw server in de Azure-portal. Onder de **instellingen** kop, selecteer de **verbindingsreeksen**. De parameter SSL varieert op basis van de connector, bijvoorbeeld ' ssl = true ' of ' sslmode = vereisen ' of ' sslmode = vereist ' en andere variaties.

Raadpleeg voor informatie over het in- of uitschakelen van SSL-verbinding bij het ontwikkelen van toepassing, [SSL configureren](howto-configure-ssl.md). 

## <a name="next-steps"></a>Volgende stappen
[Verbindingsbibliotheken voor Azure-Database voor MySQL](concepts-connection-libraries.md)
