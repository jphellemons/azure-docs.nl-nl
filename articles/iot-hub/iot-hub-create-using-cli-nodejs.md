---
title: Maken van een iothub met Azure CLI (azure.js) | Microsoft Docs
description: Het maken van een Azure-IoT-hub met behulp van de platformoverschrijdende Azure CLI (azure.js).
author: kgremban
manager: timlt
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 04/01/2018
ms.author: kgremban
ms.openlocfilehash: 5b8a7ded940f59bba63556e844c45bd7cfb8eb63
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34632088"
---
# <a name="create-an-iot-hub-using-the-azure-cli"></a>Een iothub met de Azure CLI maken

[!INCLUDE [iot-hub-resource-manager-selector](../../includes/iot-hub-resource-manager-selector.md)]

## <a name="introduction"></a>Inleiding

U kunt Azure CLI (azure.js) maken en beheren van Azure IoT hubs programmatisch gebruiken. In dit artikel leest u hoe de Azure CLI (azure.js) gebruiken om te maken van een IoT-hub.

U kunt de taak uitvoeren met behulp van een van de volgende CLI-versies:

* Azure CLI (azure.js) – de CLI voor het klassieke en resource management-implementatiemodel zoals beschreven in dit artikel.
* [Azure CLI 2.0 (az.py)](iot-hub-create-using-cli.md) : de volgende generatie CLI voor de resource management-implementatiemodel.

Voor het voltooien van deze zelfstudie hebt u het volgende nodig:

* Een actief Azure-account. Als u geen account hebt, kunt u binnen een paar minuten een [gratis account][lnk-free-trial] maken.
* [Azure CLI 0.10.4] [ lnk-CLI-install] of hoger. Als u de Azure CLI geïnstalleerd hebt, kunt u de huidige versie bij de opdrachtprompt met de volgende opdracht valideren:

```azurecli
azure --version
```

> [!NOTE]
> Azure heeft twee verschillende implementatiemodellen voor het maken en werken met resources: [Azure Resource Manager en classic](../azure-resource-manager/resource-manager-deployment-model.md). De Azure CLI moet zich in de modus Azure Resource Manager:
>
> ```azurecli
> azure config mode arm
> ```

## <a name="set-your-azure-account-and-subscription"></a>Uw Azure-account en -abonnement instellen

1. Bij de opdrachtprompt, aanmelding door de volgende opdracht te typen:

   ```azurecli
    azure login
   ```

   De voorgestelde webbrowser en code voor verificatie gebruiken.
1. Als u meerdere Azure-abonnementen hebt, verleent verbinding maken met Azure u toegang tot alle de Azure-abonnementen die zijn gekoppeld aan uw referenties. U kunt zien die de Azure-abonnementen en bepalen welke is de standaard, met de opdracht:

   ```azurecli
    azure account list
   ```

   De context van het abonnement waaronder u wilt uitvoeren van de rest van het gebruik van de opdrachten instellen:

   ```azurecli
    azure account set <subscription name>
   ```

1. Als u een resourcegroep niet hebt, kunt u een met de naam **exampleResourceGroup**:

   ```azurecli
    azure group create -n exampleResourceGroup -l westus
   ```

> [!TIP]
> Het artikel [de Azure CLI gebruiken voor het beheren van Azure-resources en resourcegroepen] [ lnk-CLI-arm] vindt u meer informatie over het gebruik van de Azure CLI voor het beheren van Azure-resources.

## <a name="create-an-iot-hub"></a>Een IoT Hub maken

Vereiste parameters:

```azurecli
azure iothub create -g <resource-group> -n <name> -l <location> -s <sku-name> -u <units>
```

* **resourcegroep**. De naam van de resourcegroep. De indeling is niet hoofdlettergevoelig alfanumerieke, onderstrepingstekens en liggende streepjes, 1-64-lengte.
* **Naam**. De naam van de iothub worden gemaakt. De indeling is niet hoofdlettergevoelig alfanumerieke en afbreekstreepjes, de lengte 3 50.
* **Locatie**. De locatie (azure-regio/datacenter) voor het inrichten van de IoT-hub.
* **SKU-naam**. De naam van de sku, een van: [F1, S1, S2, S3]. Zie voor meer informatie over elke sku [prijzen van Azure IoT Hub](https://azure.microsoft.com/pricing/details/iot-hub/). Basic-lagen zijn momenteel alleen beschikbaar via de portal. 
* **eenheden**. Het aantal ingerichte eenheden. Zie voor meer informatie over de limieten van de eenheid [prijzen van Azure IoT Hub](https://azure.microsoft.com/pricing/details/iot-hub/).

[!INCLUDE [iot-hub-pii-note-naming-hub](../../includes/iot-hub-pii-note-naming-hub.md)]

Overzicht van de parameters die zijn gemaakt, kunt u de opdracht help in de opdrachtprompt:

```azurecli
azure iothub create -h
```

Snel voorbeeld: een IoT-Hub maken aangeroepen **exampleIoTHubName** in de resourcegroep **exampleResourceGroup**, voer de volgende opdracht:

```azurecli
azure iothub create -g exampleResourceGroup -n exampleIoTHubName -l westus -k s1 -u 1
```

> [!NOTE]
> Deze opdracht Azure CLI maakt een standaard IoT-Hub van S1, waarvoor u wordt gefactureerd. U kunt de IoT-hub verwijderen **exampleIoTHubName** met volgende opdracht:
>
> ```azurecli
> azure iothub delete -g exampleResourceGroup -n exampleIoTHubName
> ```

## <a name="next-steps"></a>Volgende stappen

Zie het volgende artikel voor meer informatie over het ontwikkelen voor IoT-Hub:

* [IoT SDK 's][lnk-sdks]

Als u wilt de mogelijkheden van IoT Hub verder verkennen, Zie:

* [IoT Hub beheren met behulp van de Azure-portal][lnk-portal]

<!-- Links -->
[lnk-free-trial]: https://azure.microsoft.com/pricing/free-trial/
[lnk-azure-portal]: https://portal.azure.com/
[lnk-status]: https://azure.microsoft.com/status/
[lnk-CLI-install]:../cli-install-nodejs.md
[lnk-rest-api]: https://docs.microsoft.com/rest/api/iothub/iothubresource
[lnk-CLI-arm]: ../azure-resource-manager/xplat-cli-azure-resource-manager.md

[lnk-sdks]: iot-hub-devguide-sdks.md
[lnk-portal]: iot-hub-create-through-portal.md 
