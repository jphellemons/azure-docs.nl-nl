---
title: Beveiligingswaarschuwingen configureren | Microsoft Docs
description: Informatie over het configureren van beveiligingswaarschuwingen voor Azure Privileged Identity Management-extensie.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.service: active-directory
ms.topic: article
ms.workload: identity
ms.component: users-groups-roles
ms.date: 06/06/2017
ms.author: rolyon
ms.custom: pim
ms.openlocfilehash: 8dd546b0c3a60a50989b59fa295ceeb49e6b1e9a
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/04/2018
ms.locfileid: "34698006"
---
# <a name="how-to-configure-security-alerts-in-azure-ad-privileged-identity-management"></a>Beveiligingswaarschuwingen in Azure AD Privileged Identity Management configureren
## <a name="security-alerts"></a>Beveiligingswaarschuwingen
Azure Privileged Identity Management (PIM) genereert waarschuwingen wanneer er verdachte of unsafe activiteiten in uw omgeving. Wanneer een waarschuwing wordt geactiveerd, wordt deze weergegeven op de PIM-dashboard. Selecteer de waarschuwing voor een overzicht van een rapport met de gebruikers of rollen die de waarschuwing heeft geactiveerd.

![PIM-dashboard beveiligingswaarschuwingen - schermafbeelding][1]

| Waarschuwing | Severity | Trigger | Aanbeveling |
| --- | --- | --- | --- |
| **Rollen zijn momenteel toegewezen buiten PIM** |Hoog |Een gebruiker is definitief toegewezen aan een bevoorrechte rol, buiten de PIM-interface. |Bekijk de gebruikers in de lijst en de toewijzing van rollen toegewezen buiten PIM bevoorrechte. |
| **Rollen worden te vaak geactiveerd** |Middelgroot |Er zijn te veel heractiveringen van dezelfde rol binnen de tijd die is toegestaan in de instellingen. |Neem contact op met de gebruiker om te zien waarom ze hebben geactiveerd de rol zo vaak. Mogelijk is de tijdslimiet te kort voor ze om hun taken te voltooien, of misschien bent met behulp van scripts automatisch een rol wilt activeren. Zorg ervoor dat de activeringsduur van de voor hun rol is ingesteld lang genoeg is voor hun taken uit te voeren. |
| **Rollen niet is vereist van MFA voor activering** |Middelgroot |Er zijn functies zonder MFA ingeschakeld in de instellingen. |We MFA vereisen voor het meest maximaal bevoorrechte rollen, maar raden MFA voor activering voor alle functies in te schakelen. |
| **Gebruikers hun bevoorrechte rollen niet gebruiken** |Laag |Er zijn in aanmerking komende beheerders die hun rollen onlangs nog niet geactiveerd. |Start een toegang controleren om te bepalen van de gebruikers die geen toegang meer nodig. |
| **Er zijn te veel globale beheerders** |Laag |Er zijn meer globale beheerders dan aanbevolen. |Als u een groot aantal globale beheerders hebt, is het waarschijnlijk dat gebruikers wel meer machtigingen dan ze nodig hebben. Gebruikers voor minder bevoorrechte rollen te verplaatsen, of zorg enkele ervan voor de rol in plaats van in aanmerking komende permanent zijn toegewezen. |

### <a name="severity"></a>Severity
* **Hoge**: directe actie is vereist vanwege een schending van het beleid. 
* **Gemiddeld**: geen directe actie is vereist, maar geeft u een potentiële beleidsovertreding.
* **Lage**: geen directe actie is vereist, maar stelt voor een beter dan beleidswijziging.

## <a name="configure-security-alert-settings"></a>Waarschuwing beveiligingsinstellingen configureren
U kunt sommige van de beveiligingswaarschuwingen in PIM werkt met uw omgeving en beveiligingsdoelen aanpassen. Volg deze stappen voor het bereiken van de instellingenblade:

1. Aanmelden bij de [Azure-portal](https://portal.azure.com/) en selecteer de **Azure AD Privileged Identity Management** tegel vanuit het dashboard.
2. Selecteer **beheerd bevoorrechte rollen** > **instellingen** > **waarschuwingen instellingen**.
   
    ![Navigeer in de beveiligingsinstellingen voor waarschuwingen][2]

### <a name="roles-are-being-activated-too-frequently-alert"></a>Waarschuwing 'Rollen worden geactiveerd te vaak'
Deze waarschuwing wordt geactiveerd als een gebruiker dezelfde bevoorrechte rol meerdere keren binnen een opgegeven periode activeren. U kunt zowel de periode en het aantal activeringen configureren.

* **Activering vernieuwing tijdsbestek**: Geef op in dagen, uren, minuten en de periode die u gebruiken wilt voor het bijhouden van verdachte vernieuwingen seconde.
* **Aantal vernieuwingen activering**: Geef het aantal activeringen uit 2 tot 100, waarmee u rekening houden leveringsopties waarschuwing binnen de periode die u hebt gekozen. U kunt deze instelling wijzigen door de schuifregelaar verplaatsen of een getal in het tekstvak in te voeren.

### <a name="there-are-too-many-global-administrators-alert"></a>'Er zijn te veel globale beheerders' waarschuwing
PIM geeft deze waarschuwing als twee verschillende criteria is voldaan en kunt u beide parameters configureren. Eerst moet u een bepaalde drempelwaarde globale beheerders bereiken. Ten tweede moet een bepaald percentage van uw totale roltoewijzingen globale beheerders. Als u alleen aan een van deze metingen de waarschuwing niet wordt weergegeven.  

* **Minimum aantal globale beheerders**: Geef het aantal globale beheerders, 2 en 100, dat u rekening houden met een onveilige bedrag.
* **Percentage globale beheerders**: Geef het percentage van de beheerders die globale beheerders, van 0% tot 100%, die onveilige in uw omgeving.

### <a name="administrators-arent-using-their-privileged-roles-alert"></a>Waarschuwing 'Beheerders zijn niet met behulp van hun bevoorrechte rollen'
Deze waarschuwing wordt geactiveerd wanneer een gebruiker een bepaalde hoeveelheid tijd gaat zonder een rol activeren.

* **Aantal dagen**: Geef het aantal dagen tussen 0 en 100, die een gebruiker zonder een rol activeren kunt gaan.

## <a name="next-steps"></a>Volgende stappen
[!INCLUDE [active-directory-privileged-identity-management-toc](../../includes/active-directory-privileged-identity-management-toc.md)]

<!--Image references-->

[1]: ./media/active-directory-privileged-identity-management-how-to-configure-security-alerts/PIM_security_dash.png
[2]: ./media/active-directory-privileged-identity-management-how-to-configure-security-alerts/PIM_security_settings.png
