---
title: 'Zelfstudie: Azure Active Directory-integratie met Pega systemen | Microsoft Docs'
description: Informatie over het configureren van eenmalige aanmelding tussen Azure Active Directory en Pega systemen.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: 31acf80f-1f4b-41f1-956f-a9fbae77ee69
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/16/2017
ms.author: jeedes
ms.openlocfilehash: 539de49f24b2ca0c9b70be5a339625c1e14edc44
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/20/2018
---
# <a name="tutorial-azure-active-directory-integration-with-pega-systems"></a>Zelfstudie: Azure Active Directory-integratie met Pega systemen

In deze zelfstudie leert u hoe Pega systemen integreren met Azure Active Directory (Azure AD).

Pega systemen integreren met Azure AD biedt de volgende voordelen:

- U kunt beheren in Azure AD die toegang tot Pega systemen heeft.
- U kunt uw gebruikers automatisch ophalen aangemeld bij Pega systemen (Single Sign-On) inschakelen met hun Azure AD-accounts.
- U kunt uw accounts op één centrale locatie - en de Azure-portal beheren.

Als u weten van meer informatie over de integratie van de SaaS-app met Azure AD wilt, Zie [wat is er toegang tot toepassingen en eenmalige aanmelding bij Azure Active Directory](manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Vereisten

Voor het configureren van Azure AD-integratie met Pega systemen, moet u de volgende items:

- Een Azure AD-abonnement
- Een Pega systemen eenmalige aanmelding ingeschakeld abonnement

> [!NOTE]
> Test de stappen in deze zelfstudie, raden we niet met behulp van een productieomgeving.

Test de stappen in deze zelfstudie, moet u deze aanbevelingen volgen:

- Gebruik niet uw productieomgeving, tenzij het noodzakelijk is.
- Als u geen een proefabonnement Azure AD-omgeving hebt, kunt u [ophalen van een proefversie van één maand](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeschrijving
In deze zelfstudie test u Azure AD eenmalige aanmelding in een testomgeving. Het scenario in deze zelfstudie bestaat uit twee belangrijkste bouwstenen:

1. Pega systemen uit de galerie toevoegen
2. Configureren en testen van Azure AD eenmalige aanmelding

## <a name="adding-pega-systems-from-the-gallery"></a>Pega systemen uit de galerie toevoegen
Voor het configureren van de integratie van Pega systemen in Azure AD, moet u Pega systemen uit de galerie toe te voegen aan de lijst met beheerde SaaS-apps.

**Als u wilt toevoegen Pega systemen uit de galerie, moet u de volgende stappen uitvoeren:**

1. In de  **[Azure-portal](https://portal.azure.com)**, klik in het linkernavigatievenster op **Azure Active Directory** pictogram. 

    ![De Azure Active Directory-knop][1]

2. Navigeer naar **bedrijfstoepassingen**. Ga vervolgens naar **alle toepassingen**.

    ![De blade Enterprise-toepassingen][2]
    
3. Om de nieuwe toepassing toevoegen, klikt u op **nieuwe toepassing** knop boven aan het dialoogvenster.

    ![De knop Nieuw toepassing][3]

4. Typ in het zoekvak **Pega systemen**, selecteer **Pega systemen** van resultaat deelvenster klik vervolgens op **toevoegen** om toe te voegen van de toepassing.

    ![Pega systemen in de lijst met resultaten](./media/active-directory-saas-pegasystems-tutorial/tutorial_pegasystems_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configureren en testen eenmalige aanmelding Azure AD

In deze sectie kunt u configureren en testen eenmalige aanmelding Azure AD met Pega systemen op basis van een testgebruiker 'Britta Simon' genoemd.

Voor eenmalige aanmelding werkt, moet Azure AD weten wat de gebruiker equivalent in Pega systemen is aan een gebruiker in Azure AD. Met andere woorden, moet een koppeling relatie tussen een Azure AD-gebruiker en de betreffende gebruiker in Pega systemen worden gemaakt.

Wijs in Pega systemen, de waarde van de **gebruikersnaam** in Azure AD als de waarde van de **gebruikersnaam** de relatie van de koppeling tot stand brengen.

Om te configureren en testen van Azure AD eenmalige aanmelding met Pega systemen, moet u de volgende bouwstenen voltooien:

1. **[Azure AD eenmalige aanmelding configureren](#configure-azure-ad-single-sign-on)**  : als u wilt dat uw gebruikers kunnen deze functie gebruiken.
2. **[Maken van een Azure AD-testgebruiker](#create-an-azure-ad-test-user)**  - voor het testen van Azure AD eenmalige aanmelding met Britta Simon.
3. **[Maak een testgebruiker Pega systemen](#create-a-pega-systems-test-user)**  - Pega systemen die is gekoppeld aan de Azure AD-weergave van de gebruiker van een exemplaar van Britta Simon bevatten.
4. **[Toewijzen van de Azure AD-testgebruiker](#assign-the-azure-ad-test-user)**  - Britta Simon gebruik van Azure AD eenmalige aanmelding inschakelen.
5. **[Test eenmalige aanmelding](#test-single-sign-on)**  : om te controleren of de configuratie werkt.

### <a name="configure-azure-ad-single-sign-on"></a>Eenmalige aanmelding Azure AD configureren

In dit gedeelte Azure AD eenmalige aanmelding inschakelen in de Azure portal en eenmalige aanmelding configureren in uw toepassing Pega systemen.

**Voor het configureren van Azure AD eenmalige aanmelding met Pega systemen, moet u de volgende stappen uitvoeren:**

1. In de Azure-portal op de **Pega systemen** toepassing Integratiepagina, klikt u op **eenmalige aanmelding**.

    ![Koppeling voor eenmalige aanmelding configureren][4]

2. Op de **eenmalige aanmelding** dialoogvenster Selecteer **modus** als **op basis van SAML aanmelding** voor eenmalige aanmelding inschakelen.
 
    ![Dialoogvenster voor eenmalige aanmelding](./media/active-directory-saas-pegasystems-tutorial/tutorial_pegasystems_samlbase.png)

3. Op de **Pega systemen domein en de URL's** sectie, voert u de volgende stappen uit als u wilt configureren, de toepassing in **IDP** modus gestart:

    ![URL's en Pega systemen domein eenmalige aanmelding informatie](./media/active-directory-saas-pegasystems-tutorial/tutorial_pegasystems_url.png)

    a. In de **id** textbox, typ een URL met het volgende patroon volgen: `https://<CUSTOMERNAME>.pegacloud.io:443/prweb/sp/<INSTANCEID>`

    b. In de **antwoord-URL** textbox, typ een URL met het volgende patroon volgen: `https://<CUSTOMERNAME>.pegacloud.io:443/prweb/PRRestService/WebSSO/SAML/AssertionConsumerService`

4. Controleer **weergeven geavanceerde instellingen voor URL** en voer de volgende stap als u wilt configureren van de toepassing in **SP** modus gestart:

    ![URL's en Pega systemen domein eenmalige aanmelding informatie](./media/active-directory-saas-pegasystems-tutorial/tutorial_pegasystems_url1.png)

    In de **Relay status** textbox, typ een URL met het volgende patroon volgen: `https://<CUSTOMERNAME>.pegacloud.io/prweb/sso`
     
    > [!NOTE] 
    > Deze waarden zijn niet echt. Deze waarden bijwerken met de werkelijke id, antwoord-URL en Relay status URL. U vindt de waarden van de id en de antwoord-URL van Pega-toepassing die verderop in deze zelfstudie wordt beschreven. Voor de status van de Relay, neem contact op met [Pega Systems Client ondersteuningsteam](https://www.pega.com/contact-us) de waarde op te halen. 

5. De toepassing Pega systemen verwacht de SAML-asserties in een specifieke indeling waarvoor u aangepaste kenmerktoewijzingen toevoegen aan uw configuratie van SAML-token kenmerken. Deze claims worden specifieke klanten en is afhankelijk van uw behoeften. Volgende optioneel claims zijn bijvoorbeeld alleen die u kunt configureren voor uw toepassing. U kunt beheren de waarden van deze kenmerken van de '**gebruikerskenmerken**' sectie op de pagina van de toepassing-integratie. 

    ![Eenmalige aanmelding configureren](./media/active-directory-saas-pegasystems-tutorial/tutorial_attribute.png)

6. In de **gebruikerskenmerken** sectie op de **eenmalige aanmelding** dialoogvenster SAML-token kenmerk configureren zoals wordt weergegeven in de voorgaande afbeelding en de volgende stappen uitvoeren:
    
    | Naam kenmerk | Waarde kenmerk |
    | ------------------- | -------------------- |    
    | UID | *********** |
    | algemene naam  | *********** |
    | mail | *********** |
    | accessgroup | *********** |
    | Organisatie | *********** |
    | orgdivision | *********** |
    | orgunit | *********** |
    | Werkgroep | *********** |
    | Telefoon | *********** |

    > [!NOTE]
    > Dit zijn de specifieke waarden klant. Geef de juiste waarden.

    a. Klik op **toevoegen kenmerk** openen de **kenmerk toevoegen** dialoogvenster.

    ![Eenmalige aanmelding configureren](./media/active-directory-saas-pegasystems-tutorial/tutorial_attribute_04.png)

    ![Eenmalige aanmelding configureren](./media/active-directory-saas-pegasystems-tutorial/tutorial_attribute_05.png)

    b. In de **naam** textbox, typ de naam van het kenmerk wordt weergegeven voor die rij.

    c. Van de **waarde** typt u de waarde van het kenmerk wordt weergegeven voor die rij.
    
    d. Klik op **OK**.

7. Op de **SAML-certificaat voor ondertekening van** sectie, klikt u op **Metadata XML** en sla het bestand met metagegevens op uw computer.

    ![De downloadkoppeling certificaat](./media/active-directory-saas-pegasystems-tutorial/tutorial_pegasystems_certificate.png) 
8. Klik op **opslaan** knop.

    ![Knop Single Sign-On opslaan configureren](./media/active-directory-saas-pegasystems-tutorial/tutorial_general_400.png)
    
9. Eenmalige aanmelding configureren op **Pega systemen** aan clientzijde, open de **Pega Portal** met beheerdersaccount in een ander browservenster.

10. Selecteer **maken** -> **SysAdmin** -> **verificatieservice**.

    ![Knop Single Sign-On opslaan configureren](./media/active-directory-saas-pegasystems-tutorial/tutorial_pegasystems_admin.png)
    
11. Volgende acties uitvoeren op **Aauthentication Service maken** scherm:

    ![Knop Single Sign-On opslaan configureren](./media/active-directory-saas-pegasystems-tutorial/tutorial_pegasystems_admin1.png)

    a. Selecteer **SAML 2.0** van Type

    b. In de **naam** textbox, voer een naam bijvoorbeeld Azure AD SSO

    c. In de **korte beschrijving** textbox, voer een beschrijving  

    d. Klik op **maken en openen** 
    
12. In **identiteitsprovider (IdP) informatie** sectie, klikt u op **metagegevens importeren IdP** en blader naar het metagegevensbestand die u hebt gedownload vanuit de Azure-portal. Klik op **indienen** laden van de metagegevens.

    ![Knop Single Sign-On opslaan configureren](./media/active-directory-saas-pegasystems-tutorial/tutorial_pegasystems_admin2.png)
    
13. Hiermee wordt de IdP-gegevens gevuld zoals hieronder wordt weergegeven.

    ![Knop Single Sign-On opslaan configureren](./media/active-directory-saas-pegasystems-tutorial/tutorial_pegasystems_admin3.png)
    
14. Volgende acties uitvoeren op **Service Provider (SP) instellingen** sectie:

    ![Knop Single Sign-On opslaan configureren](./media/active-directory-saas-pegasystems-tutorial/tutorial_pegasystems_admin4.png)

    a. Kopieer de **entiteits-id** waarde en plak terug in Azure-Portal **id** textbox.

    b.  Kopieer de **Assertion Consumer Service (ACS) locatie** waarde en plak terug in Azure-Portal **antwoord-URL** textbox.

    c. Selecteer **aanvraag ondertekening uitschakelen**.

15. Klik op **Opslaan**.
    
> [!TIP]
> U kunt nu een beknopte versie van deze instructies binnen lezen de [Azure-portal](https://portal.azure.com), terwijl u de app instelt!  Na het toevoegen van deze app uit de **Active Directory > bedrijfstoepassingen** sectie, klikt u op de **Single Sign-On** tabblad en toegang tot de ingesloten documentatie via de **configuratie** sectie onderaan. U kunt meer lezen over de ingesloten documentatie-functie: [embedded-documentatie voor Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="create-an-azure-ad-test-user"></a>Een Azure AD-testgebruiker maken

Het doel van deze sectie is het een testgebruiker maken in de Azure portal Britta Simon aangeroepen.

   ![Een Azure AD-testgebruiker maken][100]

**Als u wilt een testgebruiker maken in Azure AD, moet u de volgende stappen uitvoeren:**

1. Klik in de Azure-portal in het linkerdeelvenster op het **Azure Active Directory** knop.

    ![De Azure Active Directory-knop](./media/active-directory-saas-pegasystems-tutorial/create_aaduser_01.png)

2. Als u wilt weergeven in de lijst met gebruikers, gaat u naar **gebruikers en groepen**, en klik vervolgens op **alle gebruikers**.

    !['Gebruikers en groepen' en 'Alle gebruikers' koppelingen](./media/active-directory-saas-pegasystems-tutorial/create_aaduser_02.png)

3. Openen van de **gebruiker** in het dialoogvenster klikt u op **toevoegen** boven aan de **alle gebruikers** in het dialoogvenster.

    ![De knop toevoegen](./media/active-directory-saas-pegasystems-tutorial/create_aaduser_03.png)

4. In de **gebruiker** dialoogvenster vak, voert u de volgende stappen uit:

    ![Het dialoogvenster gebruiker](./media/active-directory-saas-pegasystems-tutorial/create_aaduser_04.png)

    a. In de **naam** in het vak **BrittaSimon**.

    b. In de **gebruikersnaam** typt u het e-mailadres van gebruiker Britta Simon.

    c. Selecteer de **wachtwoord weergeven** selectievakje, en noteer de waarde die wordt weergegeven in de **wachtwoord** vak.

    d. Klik op **Create**.
 
### <a name="create-a-pega-systems-test-user"></a>Maak een testgebruiker Pega systemen

Het doel van deze sectie is het maken van een gebruiker Britta Simon aangeroepen in Pega systemen. Neem contact op met [Pega Systems Client ondersteuningsteam](https://www.pega.com/contact-us) in Pega Sysyems gebruikers kunnen maken.


### <a name="assign-the-azure-ad-test-user"></a>De Azure AD-testgebruiker toewijzen

In deze sectie maakt inschakelen u Britta Simon Azure eenmalige aanmelding gebruiken door het verlenen van toegang tot systemen Pega.

![Toewijzen van de gebruikersrol][200] 

**Als u wilt toewijzen Britta Simon Pega systemen, moet u de volgende stappen uitvoeren:**

1. Open de weergave toepassingen in de Azure-portal en gaat u naar de directoryweergave en gaat u naar **bedrijfstoepassingen** klikt u vervolgens op **alle toepassingen**.

    ![Gebruiker toewijzen][201] 

2. Selecteer in de lijst met toepassingen **Pega systemen**.

    ![De koppeling Pega systemen in de lijst met toepassingen](./media/active-directory-saas-pegasystems-tutorial/tutorial_pegasystems_app.png)  

3. Klik in het menu aan de linkerkant op **gebruikers en groepen**.

    ![De koppeling 'Gebruikers en groepen'][202]

4. Klik op **toevoegen** knop. Selecteer vervolgens **gebruikers en groepen** op **toevoegen toewijzing** dialoogvenster.

    ![Het deelvenster toewijzing toevoegen][203]

5. Op **gebruikers en groepen** dialoogvenster Selecteer **Britta Simon** in de lijst gebruikers.

6. Klik op **Selecteer** knop op **gebruikers en groepen** dialoogvenster.

7. Klik op **toewijzen** knop op **toevoegen toewijzing** dialoogvenster.
    
### <a name="test-single-sign-on"></a>Eenmalige aanmelding testen

In deze sectie kunt u uw Azure AD eenmalige aanmelding configuratie met behulp van het toegangsvenster testen.

Als u op de tegel Pega systemen in het deelvenster toegang, u moet ophalen automatisch aangemeld bij uw toepassing Pega systemen.
Zie voor meer informatie over het toegangsvenster [Inleiding tot het toegangsvenster](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Aanvullende resources

* [Lijst met zelfstudies over het integreren van SaaS-Apps met Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Wat is de toegang tot toepassingen en eenmalige aanmelding bij Azure Active Directory?](manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/active-directory-saas-pegasystems-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-pegasystems-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-pegasystems-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-pegasystems-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-pegasystems-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-pegasystems-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-pegasystems-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-pegasystems-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-pegasystems-tutorial/tutorial_general_203.png

