---
title: Azure Active Directory v2.0-eindpunt | Microsoft Docs
description: Een inleiding tot het bouwen van toepassingen met zowel Microsoft-Account en Azure Active Directory aanmelden.
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
editor: ''
ms.assetid: 2dee579f-fdf6-474b-bc2c-016c931eaa27
ms.service: active-directory
ms.component: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/01/2017
ms.author: celested
ms.reviewer: dastrock
ms.custom: aaddev
ms.openlocfilehash: 0fde3cb77389994352b62f06d370c709fa115749
ms.sourcegitcommit: e14229bb94d61172046335972cfb1a708c8a97a5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/14/2018
---
# <a name="sign-in-microsoft-account-and-azure-active-directory-users-in-a-single-application"></a>Meld u aan Microsoft-Account en Azure Active Directory gebruikers in één toepassing
In het verleden moest toepassingsontwikkelaars die ondersteuning bieden voor beide persoonlijke Microsoft-accounts en -werkaccounts van Azure Active Directory wilt integreren met twee afzonderlijke systemen. Het Azure Active Directory (Azure AD) v2.0-eindpunt introduceert een nieuwe authenticatie-API-versie waarmee dit proces wordt vereenvoudigd. Het Azure AD v2.0-eindpunt kunt aanmelden van beide typen accounts met behulp van een enkele integratie. Toepassingen die gebruikmaken van het Azure AD v2.0-eindpunt kunnen ook gebruiken voor de REST-API's van de [Microsoft Graph API](https://graph.microsoft.io) met behulp van een type account.

## <a name="getting-started"></a>Aan de slag
Kies uw favoriete platform in de volgende lijst voor het bouwen van een toepassing met behulp van de Microsoft open source-bibliotheken en frameworks. U kunt ook de OAuth 2.0 en OpenID Connect-protocollen te verzenden en ontvangen protocolberichten rechtstreeks zonder met behulp van een verificatiebibliotheek.
<br />

[!INCLUDE [Azure AD v2.0 endpoint platforms](../../../includes/active-directory-v2-quickstart-table.md)]

## <a name="learn-more-about-the-azure-ad-v20-endpoint"></a>Meer informatie over het Azure AD v2.0-eindpunt
Meer informatie over wat u met de Azure AD v2.0-eindpunt doen kunt:

* Ontdek de [typen toepassingen die u met de Azure AD v2.0-eindpunt maken kunt](active-directory-v2-flows.md).
* Inzicht in de [beperkingen, beperkingen en beperkingen](active-directory-v2-limitations.md) met de Azure AD v2.0-eindpunt.
* Bekijk deze video voor een overzicht van het Azure AD v2.0-eindpunt:

>[!VIDEO https://channel9.msdn.com/Events/Build/2017/P4031/player]

## <a name="additional-resources"></a>Aanvullende resources
Bekijk de gedetailleerde informatie over de Azure AD v2.0-eindpunt-platform:

* [Azure AD v2.0 protocollen verwijzen naar](active-directory-v2-protocols.md)
* [Azure AD v2.0 tokens verwijzing](active-directory-v2-tokens.md)
* [Azure AD v2.0-verificatiebibliotheken verwijst naar](active-directory-v2-libraries.md)
* [Scopes en toestemming in de Azure AD v2.0-eindpunt](active-directory-v2-scopes.md)
* [Microsoft Graph API](https://graph.microsoft.io)

> [!NOTE]
> Als u alleen hoeft aan te melden werk- en schoolaccounts accounts van Azure Active Directory, beginnen met de [ontwikkelaarshandleiding Azure AD](active-directory-developers-guide.md). Het Azure AD v2.0-eindpunt is bedoeld voor gebruik door ontwikkelaars die expliciet hoeft aan te melden in persoonlijke Microsoft-accounts.

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]
