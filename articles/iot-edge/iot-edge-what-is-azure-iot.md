---
title: Azure-oplossingen voor het Internet of Things (IoT Edge) | Microsoft Docs
description: Overzicht van een voorbeeldoplossing met IoT-architectuur en hoe deze is gekoppeld aan apparaten, de Azure IoT Hub-service, Azure IoT-apparaat-SDK's, Azure IoT-service-SDK’s en andere Azure-services.
author: dominicbetts
manager: timlt
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 09/15/2017
ms.author: dobett
ms.openlocfilehash: bd59e740803f8f0e6f5f542805d615772efba913
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34630337"
---
[!INCLUDE [iot-azure-and-iot](../../includes/iot-azure-and-iot.md)]

## <a name="next-steps"></a>Volgende stappen

Azure IoT Edge is een Azure-service die analyse en gegevensverwerking aan de rand van het netwerk mogelijk maakt. Met IoT Edge kunt uw apparaten voorzien van op containers gebaseerde code die logica bevat die rechtstreeks wordt opgehaald vanuit de Azure-services die u al gebruikt of u kunt uw eigen oplossingsspecifieke code gebruiken. Hiermee kunt u uw apparaten de volgende mogelijkheden geven:

* Fungeren als gatewayapparaten die gegevens van meerdere leaf-apparaten samenvoegen en verwerken.
* Anomaliedetectie uitvoeren en reageren op wijzigingen in de omgeving zonder te moeten wachten op instructies uit de cloud.
* Bandbreedte en opslagkosten tot het minimum beperken door gegevens vooraf te verwerken en de resultaten te verzenden. 

IoT Edge omvat ook een cloudinterface die extern beheer van apparaten mogelijk maakt. U kunt code implementeren, de status controleren en deze bijwerken zonder dat u fysiek toegang hoeft te hebben tot uw apparaten. U kunt individuele apparaten op afstand beheren of implementaties maken die worden toegepast op grote sets met apparaten die u definieert. Zie [IoT Edge-implementaties voor individuele apparaten of op grote schaal][lnk-deployment] voor meer informatie.

Zie [Hoe Azure IoT Edge werkt][lnk-overview] voor meer informatie over de onderdelen die IoT Edge mogelijk maken.

Als u Azure IoT Hub nog niet hebt gebruikt, kunt u beginnen met een [Overzicht van de service Azure IoT Hub][lnk-iot-hub].

[lnk-deployment]: module-deployment-monitoring.md
[lnk-overview]: how-iot-edge-works.md
[lnk-iot-hub]: ../iot-hub/iot-hub-what-is-iot-hub.md
[lnk-iot-suite]: https://azure.microsoft.com/documentation/suites/iot-suite/
[lnk-iotdev]: https://azure.microsoft.com/develop/iot/
[lnk-device-management]: ../iot-hub/iot-hub-device-management-overview.md
