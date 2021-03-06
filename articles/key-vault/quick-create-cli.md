---
title: Azure-snelstart - Een Key Vault CLI maken | Microsoft Docs
description: Snelstart voor het maken van een Azure-sleutelkluis met behulp van de CLI
services: key-vault
author: barclayn
manager: mbaldwin
tags: azure-resource-manager
ms.assetid: 4acc894f-fee0-4c2f-988e-bc0eceea5eda
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.custom: mvc
ms.date: 05/10/2018
ms.author: barclayn
ms.openlocfilehash: ae8957e5bf87fc190076db87d4eaca0e7a757c5e
ms.sourcegitcommit: c52123364e2ba086722bc860f2972642115316ef
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/11/2018
---
# <a name="quickstart-create-an-azure-key-vault-using-the-cli"></a>Snelstart: Een Azure-sleutelkluis maken met behulp van de CLI

Azure Key Vault is een cloudservice die werkt als een beveiligd geheimenarchief. U kunt veilig sleutels, wachtwoorden, certificaten en andere geheime informatie opslaan. U kunt het [Overzicht](key-vault-overview.md) raadplegen voor meer informatie over Key Vault. Azure CLI wordt gebruikt voor het maken en beheren van Azure-resources met behulp van opdrachten of scripts. In deze snelstart maakt u een sleutelkluis. Nadat u dat hebt gedaan, gaat u een geheim opslaan.

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint.

[!INCLUDE [cloud-shell-powershell.md](../../includes/cloud-shell-try-it.md)]

Als u ervoor kiest om de CLI lokaal te installeren en te gebruiken, moet u voor deze snelstart versie 2.0.4 of hoger van Azure CLI uitvoeren. Voer `az --version` uit om de versie te bekijken. Als u Azure CLI 2.0 wilt installeren of upgraden, raadpleegt u [Azure CLI 2.0 installeren]( /cli/azure/install-azure-cli).

Om u aan te melden bij Azure met behulp van de CLI, kunt u het volgende typen:

```azurecli
az login
```

Zie [Aanmelden met Azure CLI 2.0](https://docs.microsoft.com/cli/azure/authenticate-azure-cli?view=azure-cli-latest) voor meer informatie over opties voor aanmelding via de CLI.

## <a name="create-a-resource-group"></a>Een resourcegroep maken

Een resourcegroep is een logische container waarin Azure-resources worden geïmplementeerd en beheerd. In het volgende voorbeeld wordt een resourcegroep met de naam *ContosoResourceGroup* in de locatie *eastus* gemaakt.

```azurecli
az group create --name 'ContosoResourceGroup' --location eastus
```

## <a name="create-a-key-vault"></a>Een sleutelkluis maken

Vervolgens maakt u een sleutelkluis in de resourcegroep die u in de vorige stap hebt gemaakt. U moet enkele gegevens verstrekken:

- Voor deze snelstart gebruiken we **Contoso-vault2**. U moet in de test een unieke naam opgeven.
- Naam van resourcegroep **ContosoResourceGroup**.
- De locatie **VS - oost**.

```azurecli
az keyvault create --name 'Contoso-Vault2' --resource-group 'ContosoResourceGroup' --location eastus
```

De uitvoer van deze cmdlet toont eigenschappen van de nieuw gemaakte sleutelkluis. Let op de onderstaande twee eigenschappen:

- **Kluisnaam**: in het voorbeeld is dat **Contoso-Vault2**. U gebruikt deze naam voor andere Key Vault-opdrachten.
- **Kluis-URI**: in het voorbeeld is dat https://contoso-vault2.vault.azure.net/. Toepassingen die via de REST API gebruikmaken van uw kluis, moeten deze URI gebruiken.

Vanaf dit punt is uw Azure-account nu als enige gemachtigd om bewerkingen op deze nieuwe kluis uit te voeren.

## <a name="add-a-secret-to-key-vault"></a>Een geheim toevoegen aan Key Vault

Als u een geheim wilt toevoegen aan de kluis, hoeft u maar een paar extra stappen uit te voeren. Dit wachtwoord zou kunnen worden gebruikt door een toepassing. Het wachtwoord krijgt de naam **ExamplePassword** en slaat daarin de waarde van **Pa$$w0rd** op.

Typ de onderstaande opdrachten om een geheim te maken in de sleutelkluis met de naam **ExamplePassword** waarin de waarde **Pa$$w0rd** wordt opgeslagen:

```azurecli
az keyvault secret set --vault-name 'Contoso-Vault2' --name 'ExamplePassword' --value 'Pa$$w0rd'
```

U kunt nu verwijzen naar dit wachtwoord dat u aan Azure Key Vault hebt toegevoegd met behulp van de bijbehorende URI. Gebruik **https://ContosoVault.vault.azure.net/secrets/ExamplePassword** om de huidige versie op te halen. 

Als u de waarde in het geheim als tekst zonder opmaak wilt weergeven:

```azurecli
az keyvault secret show --name 'ExamplePassword' --vault-name 'Contoso-Vault2'
```

U hebt nu een sleutelkluis gemaakt, een geheim opgeslagen en dat vervolgens opgehaald.

## <a name="clean-up-resources"></a>Resources opschonen

Andere snelstartgidsen en zelfstudies in deze verzameling zijn gebaseerd op deze snelstartgids. Als u van plan bent om verder te gaan met volgende snelstarts en zelfstudies, kunt u deze resources intact laten.
U kunt de opdracht [az group delete](/cli/azure/group#delete) gebruiken om de resourcegroep en alle gerelateerde resources te verwijderen wanneer u ze niet meer nodig hebt. U kunt de resources als volgt verwijderen:

```azurecli
az group delete --name ContosoResourceGroup
```

## <a name="next-steps"></a>Volgende stappen

In deze snelstart hebt u een sleutelkluis gemaakt en daar een geheim in opgeslagen. Meer informatie over Key Vault en hoe u dat kunt gebruiken samen met uw toepassingen is te vinden in de zelfstudie voor webtoepassingen die geschikt zijn voor Key Vault.

> [!div class="nextstepaction"]
> Voor informatie over het lezen van een geheim uit Key Vault met behulp van een toepassing via beheerde service-identiteit kunt u verdergaan met de volgende zelfstudie, [Een Azure-webtoepassing configureren om een geheim uit Key Vault te lezen](tutorial-web-application-keyvault.md)
