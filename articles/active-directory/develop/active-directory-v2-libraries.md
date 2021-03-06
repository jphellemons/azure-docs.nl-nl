---
title: Azure Active Directory-verificatiebibliotheken voor v2.0 | Microsoft Docs
description: Compatibel clientbibliotheken en server middleware bibliotheken en -gerelateerde bibliotheek, bron en voorbeelden koppelingen, voor het Azure Active Directory v2.0-eindpunt.
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
editor: ''
ms.assetid: 19cec615-e51f-4141-9f8c-aaf38ff9f746
ms.service: active-directory
ms.component: develop
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/13/2018
ms.author: celested
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: 8fe3db09acbdec606f25d0bc81300bc4f5e87411
ms.sourcegitcommit: e14229bb94d61172046335972cfb1a708c8a97a5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/14/2018
---
# <a name="azure-active-directory-v20-authentication-libraries"></a>Azure Active Directory-verificatiebibliotheken voor v2.0

De [v2.0-eindpunt voor Azure Active Directory (Azure AD)](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-compare) ondersteunt de industriestandaard-protocollen voor OAuth 2.0 en OpenID Connect 1.0. De Microsoft Authentication Library (MSAL) is ontworpen voor gebruik met de Azure AD v2.0-eindpunt. Het is ook mogelijk om te gebruiken, open source-bibliotheken die ondersteuning bieden voor OAuth 2.0 en OpenID Connect 1.0.

Het verdient aanbeveling dat u bibliotheken geschreven door protocol domein experts die een methodologie Security Development Lifecycle (SDL) zoals volgen [de gevolgd door Microsoft][Microsoft-SDL]. Als u aan de hand-code-ondersteuning voor de protocollen besluit, volgt u een methodologie zoals Microsoft SDL en betalen aandacht voor zowel de beveiligingsoverwegingen in de specificaties standaarden voor elk protocol sluiten.

> [!NOTE]
> Zoekt u de Azure AD v1.0 libraries (ADAL)? Bekijk de [ADAL-bibliotheek handleiding](https://docs.microsoft.com/azure/active-directory/develop/active-directory-authentication-libraries).
>
>

## <a name="types-of-libraries"></a>Typen bibliotheken

Azure AD v2.0-eindpunt werkt met twee soorten bibliotheken:

* **Clientbibliotheken**. Systeemeigen clients en servers kunnen clientbibliotheken toegangstokens voor het aanroepen van een resource, zoals Microsoft Graph ophalen.
* **Server middleware bibliotheken**. Web-apps gebruiken server middleware bibliotheken voor gebruikersaanmelding. Web-API's gebruiken server middleware bibliotheken om tokens die worden verzonden door de systeemeigen clients of door andere servers te valideren.

## <a name="library-support"></a>Bibliotheek-ondersteuning

Omdat u kunt alle standaarden compatibele bibliotheek kiezen kunt wanneer u het v2.0-eindpunt, is het belangrijk te weten waar u voor ondersteuning. Neem contact op met de eigenaar van de bibliotheek voor problemen en functie-aanvragen in de bibliotheek-code. Neem contact op met Microsoft voor problemen en functie-aanvragen in de protocolimplementatie aan servicezijde. [Aanvragen van functie](https://feedback.azure.com/forums/169401-azure-active-directory) voor aanvullende functies die u zou willen zien in het protocol. [Maak een ondersteuningsaanvraag](https://docs.microsoft.com/en-us/azure/azure-supportability/how-to-create-azure-support-request) als u een probleem vindt waarbij het Azure AD v2.0-eindpunt is niet compatibel met OAuth 2.0- of OpenID Connect 1.0.

Bibliotheken zijn twee categorieën van ondersteuning:

* **Microsoft ondersteunde**. Microsoft biedt oplossingen voor deze bibliotheken en SDL heeft gedaan innen op deze bibliotheken.
* **Compatibel**. Microsoft heeft getest deze bibliotheken in basisscenario's en bevestigd dat ze met het v2.0-eindpunt werken. Microsoft biedt geen oplossingen voor deze bibliotheken en een overzicht van deze bibliotheken niet uitgevoerd. Problemen en functie-aanvragen moeten worden omgeleid naar de bibliotheek open source-project.

Zie de volgende secties in dit artikel voor een lijst met bibliotheken die met het v2.0-eindpunt werken.

## <a name="microsoft-supported-client-libraries"></a>Microsoft ondersteunde clientbibliotheken

> [!IMPORTANT]
> De MSAL preview bibliotheken zijn geschikt voor gebruik in een productieomgeving. Microsoft biedt dezelfde productie niveau ondersteuning voor deze bibliotheken als de huidige productie libraries (ADAL). Tijdens de preview verwachten dat de wijzigingen in de API MSAL indeling van de interne cache en andere mechanismen van deze bibliotheken zonder voorafgaande kennisgeving u moet te laten samen met oplossingen voor problemen of verbeterde functies. Dit mogelijk invloed op uw toepassing. Een wijziging in de cache-indeling moet bijvoorbeeld mogelijk uw gebruikers zich opnieuw aanmelden. Een wijziging in de API moet u mogelijk het bijwerken van uw code. Wanneer de algemene beschikbaarheid (GA)-versie beschikbaar komt, alle toepassingen met een preview-versie van de tapewisselaar moeten binnen zes maanden bijwerken of ze meer werken.

| Platform | Bibliotheek | Downloaden | Broncode | Voorbeeld | Referentie
| --- | --- | --- | --- | --- | --- |
| .NET-client, Windows Store UWP, Xamarin iOS en Android | MSAL .NET (Preview) |[NuGet](https://www.nuget.org/packages/Microsoft.Identity.Client) |[GitHub](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet) | [Bureaublad-App](guidedsetups/active-directory-mobileanddesktopapp-windowsdesktop-intro.md) |  |
| Javascript | MSAL.js (Preview) | [GitHub](https://github.com/AzureAD/microsoft-authentication-library-for-js) | [GitHub](https://github.com/AzureAD/microsoft-authentication-library-for-js) | [App met één pagina](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-dotnet-webapi-v2) |  |
| voor iOS, Mac OS | MSAL (Preview) | [GitHub](https://github.com/AzureAD/microsoft-authentication-library-for-objc) |[GitHub](https://github.com/AzureAD/microsoft-authentication-library-for-objc) | [iOS App](https://github.com/Azure-Samples/active-directory-msal-ios-swift) |  |
| Android | MSAL (Preview) | [De centrale opslagplaats](https://repo1.maven.org/maven2/com/microsoft/identity/client/msal/) |[GitHub](https://github.com/AzureAD/microsoft-authentication-library-for-android) | [Android-App](guidedsetups/active-directory-mobileanddesktopapp-android-intro.md) | [JavaDocs](http://javadoc.io/doc/com.microsoft.identity.client/msal) |

## <a name="microsoft-supported-server-middleware-libraries"></a>Microsoft ondersteund server middleware bibliotheken

| Platform | Bibliotheek | Downloaden | Broncode | Voorbeeld | Referentie
| --- | --- | --- | --- | --- | --- |
| .NET 4.x | OWIN OpenID Connect middleware |[NuGet](https://www.nuget.org/packages/Microsoft.Owin.Security.OpenIdConnect) |[GitHub](https://github.com/aspnet/AspNetKatana/) |[MVC-App](guidedsetups/active-directory-serversidewebapp-aspnetwebappowin-intro.md) | |
| .NET 4.x | OWIN OAuth Bearer-middleware voor AzureAD |[NuGet](https://www.nuget.org/packages/Microsoft.Owin.Security.ActiveDirectory/) |[GitHub](https://github.com/aspnet/AspNetKatana/) |  | |
| .NET 4.x | JWT-Handler voor .NET 4.5 | [NuGet](https://www.nuget.org/packages/System.IdentityModel.Tokens.Jwt/4.0.4.403061554) | [GitHub](https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet) | | |
| .NET Core | ASP.NET-middleware OpenID Connect |[Microsoft.AspNetCore.Authentication.OpenIdConnect (NuGet)][ServerLib-NetCore-Owin-Oidc-Lib] |[ASP.NET-beveiliging (GitHub)][ServerLib-NetCore-Owin-Oidc-Repo] |[MVC-app](https://github.com/Azure-Samples/active-directory-dotnet-webapp-openidconnect-aspnetcore-v2) |
| .NET Core | OAuth-Bearer-middleware voor ASP.NET |[Microsoft.AspNetCore.Authentication.OAuth (NuGet)][ServerLib-NetCore-Owin-Oauth-Lib] |[ASP.NET-beveiliging (GitHub)][ServerLib-NetCore-Owin-Oauth-Repo] |  |
| .NET Core | JWT-Handler voor .NET Core  |[NuGet](https://www.nuget.org/packages/System.IdentityModel.Tokens.Jwt) |[GitHub](https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet) | | |
| Node.js |Azure AD-Passport |[npm](https://www.npmjs.com/package/passport-azure-ad) |[GitHub](https://github.com/AzureAD/passport-azure-ad) | [Web-app](https://github.com/AzureADQuickStarts/AppModelv2-WebApp-OpenIDConnect-nodejs)| |

## <a name="compatible-client-libraries"></a>Compatibel clientbibliotheken

| Platform | Bibliotheeknaam | Geteste versie | Broncode | Voorbeeld |
|:---:|:---:|:---:|:---:|:---:|
| Android |[OIDCAndroidLib](https://github.com/kalemontes/OIDCAndroidLib/) |0.2.1 |[OIDCAndroidLib](https://github.com/kalemontes/OIDCAndroidLib) |[Voorbeeld van de systeemeigen app](active-directory-v2-devquickstarts-android.md) |
| iOS |[NXOAuth2Client](https://github.com/nxtbgthng/OAuth2Client) |1.2.8 |[NXOAuth2Client](https://github.com/nxtbgthng/OAuth2Client) |[Voorbeeld van de systeemeigen app](active-directory-v2-devquickstarts-ios.md) |
| Javascript |[Hello.js](https://adodson.com/hello.js/) |1.13.5 |[Hello.js](https://github.com/MrSwitch/hello.js) |[SPA](https://github.com/Azure-Samples/active-directory-javascript-graphapi-web-v2) |
| Java | [Notulist Java scribejava](https://github.com/scribejava/scribejava) | [Versie 3.2.0](https://github.com/scribejava/scribejava/releases/tag/scribejava-3.2.0) | [ScribeJava](https://github.com/scribejava/scribejava/) | |
| PHP | [De PHP-League oauth2-client](https://github.com/thephpleague/oauth2-client) | [1.4.2 versie](https://github.com/thephpleague/oauth2-client/releases/tag/1.4.2) | [oauth2-client](https://github.com/thephpleague/oauth2-client/) | |
| Ruby |[OmniAuth](https://github.com/omniauth/omniauth/wiki) |omniauth:1.3.1</br>omniauth-oauth2:1.4.0 |[OmniAuth](https://github.com/omniauth/omniauth)</br>[OmniAuth OAuth2](https://github.com/intridea/omniauth-oauth2) |  |

## <a name="related-content"></a>Gerelateerde inhoud
Zie voor meer informatie over het Azure AD v2.0-eindpunt, de [overzicht van Azure AD app model v2.0][AAD-App-Model-V2-Overview].

<!--Image references-->

<!--Reference style links -->
[AAD-App-Model-V2-Overview]: ../active-directory-appmodel-v2-overview.md
[ClientLib-NET-Lib]: http://www.nuget.org/packages/Microsoft.Identity.Client
[ClientLib-NET-Repo]: https://github.com/AzureAD/microsoft-authentication-library-for-dotnet
[ClientLib-NET-Sample]: active-directory-v2-devquickstarts-wpf.md
[ClientLib-Node-Lib]: https://www.npmjs.com/package/passport-azure-ad
[ClientLib-Node-Repo]: https://github.com/AzureAD/passport-azure-ad
[ClientLib-Node-Sample]:/
[ClientLib-Iosmac-Lib]:/
[ClientLib-Iosmac-Repo]:/
[ClientLib-Iosmac-Sample]:/
[ClientLib-Android-Lib]:/
[ClientLib-Android-Repo]:/
[ClientLib-Android-Sample]:/
[ClientLib-Js-Lib]:/
[ClientLib-Js-Repo]:/
[ClientLib-Js-Sample]:/

[Microsoft-SDL]: http://www.microsoft.com/sdl/default.aspx
[ServerLib-Net4-Owin-Oidc-Lib]: https://www.nuget.org/packages/Microsoft.Owin.Security.OpenIdConnect/
[ServerLib-Net4-Owin-Oidc-Repo]: http://katanaproject.codeplex.com/
[ServerLib-Net4-Owin-Oidc-Sample]: active-directory-v2-devquickstarts-dotnet-web.md
[ServerLib-Net4-Owin-Oauth-Lib]: https://www.nuget.org/packages/Microsoft.Owin.Security.OAuth/
[ServerLib-Net4-Owin-Oauth-Repo]: http://katanaproject.codeplex.com/
[ServerLib-Net4-Owin-Oauth-Sample]: https://azure.microsoft.com/documentation/articles/active-directory-v2-devquickstarts-dotnet-api/
[ServerLib-Net-Jwt-Lib]: https://www.nuget.org/packages/System.IdentityModel.Tokens.Jwt
[ServerLib-Net-Jwt-Repo]: https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet
[ServerLib-Net-Jwt-Sample]:/
[ServerLib-NetCore-Owin-Oidc-Lib]: https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.OpenIdConnect/
[ServerLib-NetCore-Owin-Oidc-Repo]: https://github.com/aspnet/Security
[ServerLib-NetCore-Owin-Oidc-Sample]: https://github.com/Azure-Samples/active-directory-dotnet-webapp-openidconnect-aspnetcore-v2
[ServerLib-NetCore-Owin-Oauth-Lib]: https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.OAuth/
[ServerLib-NetCore-Owin-Oauth-Repo]: https://github.com/aspnet/Security
[ServerLib-NetCore-Owin-Oauth-Sample]:/
[ServerLib-Node-Lib]: https://www.npmjs.com/package/passport-azure-ad
[ServerLib-Node-Repo]: https://github.com/AzureAD/passport-azure-ad/
[ServerLib-Node-Sample]: https://azure.microsoft.com/documentation/articles/active-directory-v2-devquickstarts-node-web/
