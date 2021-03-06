---
title: 'Zelfstudie: Azure Active Directory-integratie met de Jitbit Helpdesk | Microsoft Docs'
description: Informatie over het configureren van eenmalige aanmelding tussen Azure Active Directory en Jitbit Helpdesk.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 15ce27d4-0621-4103-8a34-e72c98d72ec3
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/28/2017
ms.author: jeedes
ms.openlocfilehash: c8e387bb98ad2e23c667ba058ff8ab5dbfedffbf
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/20/2018
---
# <a name="tutorial-azure-active-directory-integration-with-jitbit-helpdesk"></a>Zelfstudie: Azure Active Directory-integratie met Jitbit Helpdesk

In deze zelfstudie leert u hoe Jitbit Helpdesk integreren met Azure Active Directory (Azure AD).

Jitbit Helpdesk integreren met Azure AD biedt de volgende voordelen:

- U kunt beheren in Azure AD die toegang tot Jitbit Helpdesk heeft
- U kunt uw gebruikers automatisch ophalen aangemeld bij de Jitbit Helpdesk (Single Sign-On) inschakelen met hun Azure AD-accounts
- U kunt uw accounts op één centrale locatie - en de Azure-portal beheren

Als u weten van meer informatie over de integratie van de SaaS-app met Azure AD wilt, Zie [wat is er toegang tot toepassingen en eenmalige aanmelding bij Azure Active Directory](manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Vereisten

Voor het configureren van Azure AD-integratie met Jitbit Helpdesk, moet u de volgende items:

- Een Azure AD-abonnement
- Een Jitbit Helpdesk eenmalige aanmelding ingeschakeld abonnement

> [!NOTE]
> Test de stappen in deze zelfstudie, raden we niet met behulp van een productieomgeving.

Test de stappen in deze zelfstudie, moet u deze aanbevelingen volgen:

- Gebruik niet uw productieomgeving, tenzij het noodzakelijk is.
- Als u geen een proefabonnement Azure AD-omgeving hebt, kunt u een proefversie van één maand [hier](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeschrijving
In deze zelfstudie test u Azure AD eenmalige aanmelding in een testomgeving. Het scenario in deze zelfstudie bestaat uit twee belangrijkste bouwstenen:

1. Jitbit Helpdesk uit de galerie toevoegen
2. Configureren en testen van Azure AD eenmalige aanmelding

## <a name="adding-jitbit-helpdesk-from-the-gallery"></a>Jitbit Helpdesk uit de galerie toevoegen
Voor het configureren van de integratie van Jitbit Helpdesk in Azure AD, moet u Jitbit Helpdesk uit de galerie toevoegen aan de lijst met beheerde SaaS-apps.

**Als u wilt toevoegen Jitbit Helpdesk uit de galerie, moet u de volgende stappen uitvoeren:**

1. In de  **[Azure-portal](https://portal.azure.com)**, klik in het linkernavigatievenster op **Azure Active Directory** pictogram. 

    ![Active Directory][1]

2. Navigeer naar **bedrijfstoepassingen**. Ga vervolgens naar **alle toepassingen**.

    ![Toepassingen][2]
    
3. Om de nieuwe toepassing toevoegen, klikt u op **nieuwe toepassing** knop boven aan het dialoogvenster.

    ![Toepassingen][3]

4. Typ in het zoekvak **Jitbit Helpdesk**.

    ![Een Azure AD-testgebruiker maken](./media/active-directory-saas-jitbit-helpdesk-tutorial/tutorial_jitbit-helpdesk_search.png)

5. Selecteer in het deelvenster resultaten **Jitbit Helpdesk**, en klik vervolgens op **toevoegen** om toe te voegen van de toepassing.

    ![Een Azure AD-testgebruiker maken](./media/active-directory-saas-jitbit-helpdesk-tutorial/tutorial_jitbit-helpdesk_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configureren en testen van Azure AD eenmalige aanmelding
In deze sectie maakt u configureert en test eenmalige aanmelding Azure AD met Jitbit Helpdesk op basis van een testgebruiker 'Britta Simon' genoemd.

Voor eenmalige aanmelding werkt, moet Azure AD weten wat de gebruiker equivalent in Jitbit Helpdesk is aan een gebruiker in Azure AD. Met andere woorden, moet een koppeling relatie tussen een Azure AD-gebruiker en de betreffende gebruiker in Jitbit Helpdesk tot stand worden gebracht.

Wijs in Jitbit Helpdesk, de waarde van de **gebruikersnaam** in Azure AD als de waarde van de **gebruikersnaam** de relatie van de koppeling tot stand brengen.

Om te configureren en testen van Azure AD eenmalige aanmelding met Jitbit Helpdesk, moet u de volgende bouwstenen voltooien:

1. **[Configureren van Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)**  : als u wilt dat uw gebruikers kunnen deze functie gebruiken.
2. **[Maken van een Azure AD-testgebruiker](#creating-an-azure-ad-test-user)**  - voor het testen van Azure AD eenmalige aanmelding met Britta Simon.
3. **[Maken van een testgebruiker Jitbit Helpdesk](#creating-a-jitbit-helpdesk-test-user)**  - Jitbit Helpdesk die is gekoppeld aan de Azure AD-weergave van de gebruiker van een exemplaar van Britta Simon bevatten.
4. **[Toewijzen van de Azure AD-testgebruiker](#assigning-the-azure-ad-test-user)**  - Britta Simon gebruik van Azure AD eenmalige aanmelding inschakelen.
5. **[Testen van eenmalige aanmelding](#testing-single-sign-on)**  : om te controleren of de configuratie werkt.

### <a name="configuring-azure-ad-single-sign-on"></a>Eenmalige aanmelding Azure AD configureren

In deze sectie maakt u Azure AD eenmalige aanmelding inschakelen in de Azure portal en eenmalige aanmelding configureren in uw toepassing Jitbit Helpdesk.

**Voor het configureren van Azure AD eenmalige aanmelding met de Jitbit Helpdesk, moet u de volgende stappen uitvoeren:**

1. In de Azure-portal op de **Jitbit Helpdesk** toepassing Integratiepagina, klikt u op **eenmalige aanmelding**.

    ![Eenmalige aanmelding configureren][4]

2. Op de **eenmalige aanmelding** dialoogvenster Selecteer **modus** als **op basis van SAML aanmelding** voor eenmalige aanmelding inschakelen.
 
    ![Eenmalige aanmelding configureren](./media/active-directory-saas-jitbit-helpdesk-tutorial/tutorial_jitbit-helpdesk_samlbase.png)

3. Op de **Jitbit Helpdesk domein en de URL's** sectie, voert u de volgende stappen uit:

    ![Eenmalige aanmelding configureren](./media/active-directory-saas-jitbit-helpdesk-tutorial/tutorial_jitbit-helpdesk_url.png)

    a. In de **aanmeldings-URL** textbox, typ een URL met het volgende patroon volgen: 
    | |     
    | ----------------------------------------|
    | `https://<hostname>/helpdesk/User/Login`|
    | `https://<tenant-name>.Jitbit.com`|
    | |
    
    > [!NOTE] 
    > Deze waarde is geen echte. Deze waarde bijwerken met de werkelijke URL voor eenmalige aanmelding. Neem contact op met [Jitbit Helpdesk Client ondersteuningsteam](https://www.jitbit.com/support/) deze waarde op te halen. 
    
    b.  In de **id** textbox, typ een URL als volgt: `https://www.jitbit.com/web-helpdesk/`

    
 


4. Op de **SAML-certificaat voor ondertekening van** sectie, klikt u op **Certificate(Base64)** en sla het certificaatbestand op uw computer.

    ![Eenmalige aanmelding configureren](./media/active-directory-saas-jitbit-helpdesk-tutorial/tutorial_jitbit-helpdesk_certificate.png) 

5. Klik op **opslaan** knop.

    ![Eenmalige aanmelding configureren](./media/active-directory-saas-jitbit-helpdesk-tutorial/tutorial_general_400.png)

6. Op de **Jitbit Helpdesk configuratie** sectie, klikt u op **configureren Jitbit Helpdesk** openen **eenmalige aanmelding configureren** venster. Kopieer de **SAML Single Sign-On Service-URL** van de **Naslaggids punt.**

    ![Eenmalige aanmelding configureren](./media/active-directory-saas-jitbit-helpdesk-tutorial/tutorial_jitbit-helpdesk_configure.png) 

7. In een ander browservenster, meld u bij uw bedrijf Jitbit Helpdesk site als beheerder.

8. Klik in de werkbalk bovenaan op **beheer**.
   
    ![Beheer](./media/active-directory-saas-jitbit-helpdesk-tutorial/ic777681.png "beheer")

9. Klik op **algemene instellingen**.
   
    ![Gebruikers, bedrijven en machtigingen](./media/active-directory-saas-jitbit-helpdesk-tutorial/ic777680.png "gebruikers, bedrijven en machtigingen")

10. In de **verificatie-instellingen** configuratie sectie, voert u de volgende stappen uit:
   
    ![Verificatie-instellingen](./media/active-directory-saas-jitbit-helpdesk-tutorial/ic777683.png "verificatie-instellingen")
    
    a. Selecteer **inschakelen-SAML één 2.0 aanmelden**, aan te melden met behulp van eenmalige aanmelding (SSO), met **OneLogin**.

    b. In de **eindpunt-URL** textbox, plak de waarde van **SAML Single Sign-On Service-URL** die u hebt gekopieerd vanuit Azure-portal.

    c. Open uw **base 64-** gecodeerde certificaat in Kladblok, Kopieer de inhoud ervan naar het Klembord en plakt u deze naar de **X.509-certificaat** tekstvak

    d. Klik op **wijzigingen opslaan**.

> [!TIP]
> U kunt nu een beknopte versie van deze instructies binnen lezen de [Azure-portal](https://portal.azure.com), terwijl u de app instelt!  Na het toevoegen van deze app uit de **Active Directory > bedrijfstoepassingen** sectie, klikt u op de **Single Sign-On** tabblad en toegang tot de ingesloten documentatie via de **configuratie** sectie onderaan. U kunt meer lezen over de ingesloten documentatie-functie: [embedded-documentatie voor Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Een Azure AD-testgebruiker maken
Het doel van deze sectie is het een testgebruiker maken in de Azure portal Britta Simon aangeroepen.

![Azure AD-gebruiker maken][100]

**Als u wilt een testgebruiker maken in Azure AD, moet u de volgende stappen uitvoeren:**

1. In de **Azure-portal**, klik op het navigatiedeelvenster links **Azure Active Directory** pictogram.

    ![Een Azure AD-testgebruiker maken](./media/active-directory-saas-jitbit-helpdesk-tutorial/create_aaduser_01.png) 

2. Als u wilt weergeven in de lijst met gebruikers, gaat u naar **gebruikers en groepen** en klik op **alle gebruikers**.
    
    ![Een Azure AD-testgebruiker maken](./media/active-directory-saas-jitbit-helpdesk-tutorial/create_aaduser_02.png) 

3. Openen van de **gebruiker** dialoogvenster, klikt u op **toevoegen** boven aan het dialoogvenster.
 
    ![Een Azure AD-testgebruiker maken](./media/active-directory-saas-jitbit-helpdesk-tutorial/create_aaduser_03.png) 

4. Op de **gebruiker** dialoogvenster pagina, voert u de volgende stappen uit:
 
    ![Een Azure AD-testgebruiker maken](./media/active-directory-saas-jitbit-helpdesk-tutorial/create_aaduser_04.png) 

    a. In de **naam** textbox typenaam als **BrittaSimon**.

    b. In de **gebruikersnaam** textbox type de **e-mailadres** van BrittaSimon.

    c. Selecteer **wachtwoord weergeven** en noteer de waarde van de **wachtwoord**.

    d. Klik op **Create**.
 
### <a name="creating-a-jitbit-helpdesk-test-user"></a>Maken van een testgebruiker Jitbit Helpdesk

Om in te schakelen gebruikers van Azure AD aan te melden bij Jitbit Helpdesk, moeten ze worden ingericht in Jitbit Helpdesk.  In het geval van Jitbit Helpdesk is inrichting een handmatige taak.

**Voor het inrichten van een gebruikersaccount, moet u de volgende stappen uitvoeren:**

1. Meld u aan bij uw **Jitbit Helpdesk** tenant.

2. Klik in het menu bovenaan op **beheer**.
   
    ![Beheer](./media/active-directory-saas-jitbit-helpdesk-tutorial/ic777681.png "beheer")

3. Klik op **gebruikers, bedrijven en machtigingen**.
   
    ![Gebruikers, bedrijven en machtigingen](./media/active-directory-saas-jitbit-helpdesk-tutorial/ic777682.png "gebruikers, bedrijven en machtigingen")

4. Klik op **gebruiker toevoegen**.
   
    ![Gebruiker toevoegen](./media/active-directory-saas-jitbit-helpdesk-tutorial/ic777685.png "gebruiker toevoegen")
   
5. Typ in de sectie maken de gegevens van de Azure AD-account dat u wilt inrichten als volgt:

    ![Maak](./media/active-directory-saas-jitbit-helpdesk-tutorial/ic777686.png "maken")
   
   a. In de **gebruikersnaam** textbox type **BrittaSimon**, de gebruikersnaam, zoals in de Azure-portal.

   b. In de **e** textbox type e-mailadres van de gebruiker, zoals **BrittaSimon@contoso.com**.

   c. In de **voornaam** textbox type voornaam van de gebruiker zoals **Britta**.

   d. In de **achternaam** textbox type achternaam van de gebruiker zoals **Simon**.
   
   e. Klik op **Create**.

>[!NOTE]
>U kunt geen andere hulpprogramma's voor Jitbit Helpdesk gebruiker-account maken of API's die worden geleverd door Jitbit Helpdesk gebruiken voor het inrichten van Azure AD-gebruikersaccounts.
> 
        

### <a name="assigning-the-azure-ad-test-user"></a>Toewijzen van de testgebruiker Azure AD

In deze sectie maakt inschakelen u Britta Simon gebruikt Azure eenmalige aanmelding toegang te verlenen aan de Jitbit Helpdesk.

![Gebruiker toewijzen][200] 

**Britta Simon om aan te wijzen Jitbit Helpdesk, moet u de volgende stappen uitvoeren:**

1. Open de weergave toepassingen in de Azure-portal en gaat u naar de directoryweergave en gaat u naar **bedrijfstoepassingen** klikt u vervolgens op **alle toepassingen**.

    ![Gebruiker toewijzen][201] 

2. Selecteer in de lijst met toepassingen **Jitbit Helpdesk**.

    ![Eenmalige aanmelding configureren](./media/active-directory-saas-jitbit-helpdesk-tutorial/tutorial_jitbit-helpdesk_app.png) 

3. Klik in het menu aan de linkerkant op **gebruikers en groepen**.

    ![Gebruiker toewijzen][202] 

4. Klik op **toevoegen** knop. Selecteer vervolgens **gebruikers en groepen** op **toevoegen toewijzing** dialoogvenster.

    ![Gebruiker toewijzen][203]

5. Op **gebruikers en groepen** dialoogvenster Selecteer **Britta Simon** in de lijst gebruikers.

6. Klik op **Selecteer** knop op **gebruikers en groepen** dialoogvenster.

7. Klik op **toewijzen** knop op **toevoegen toewijzing** dialoogvenster.
    
### <a name="testing-single-sign-on"></a>Testen van eenmalige aanmelding

In deze sectie kunt u uw Azure AD eenmalige aanmelding configuratie met behulp van het toegangsvenster testen.

Als u op de tegel Jitbit Helpdesk in het deelvenster toegang, krijgt u de aanmeldingspagina van Jitbit Helpdesk-toepassing.
Zie voor meer informatie over het toegangsvenster [Inleiding tot het toegangsvenster](active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Aanvullende resources

* [Lijst met zelfstudies over het integreren van SaaS-Apps met Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Wat is de toegang tot toepassingen en eenmalige aanmelding bij Azure Active Directory?](manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/active-directory-saas-jitbit-helpdesk-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-jitbit-helpdesk-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-jitbit-helpdesk-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-jitbit-helpdesk-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-jitbit-helpdesk-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-jitbit-helpdesk-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-jitbit-helpdesk-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-jitbit-helpdesk-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-jitbit-helpdesk-tutorial/tutorial_general_203.png

