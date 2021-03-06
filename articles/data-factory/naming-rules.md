---
title: Regels voor het benoemen van Azure Data Factory-entiteiten | Microsoft Docs
description: Beschrijving van naamgevingsregels voor Data Factory-entiteiten.
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: craigg
ms.assetid: bc5e801d-0b3b-48ec-9501-bb4146ea17f1
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 01/16/2018
ms.author: shlo
ms.openlocfilehash: e9d2140edc64daca4df0463dbfdcd5b06f072012
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34620405"
---
# <a name="azure-data-factory---naming-rules"></a>Azure Data Factory - naamgevingsregels
De volgende tabel bevat de naamgevingsregels voor Data Factory-artefacten.

> [!NOTE]
> Dit artikel is van toepassing op versie 2 van Data Factory, dat zich momenteel in de previewfase bevindt. Als u van versie 1 van de Data Factory-service gebruikmaakt (GA) is algemeen beschikbaar is, raadpleegt u [naamgevingsregels in Data Factory version1 gedefinieerd](v1/data-factory-naming-rules.md).

| Naam | Uniekheid van de naam | De validatie wordt gecontroleerd |
|:--- |:--- |:--- |
| Data Factory |Uniek zijn voor Microsoft Azure. Namen zijn niet hoofdlettergevoelig, dat wil zeggen, `MyDF` en `mydf` verwijzen naar de dezelfde gegevensfactory. |<ul><li>Elke gegevensfactory is gekoppeld aan één Azure-abonnement.</li><li>Object-namen moeten beginnen met een letter of cijfer en mag alleen letters, cijfers en het streepje (-) bevatten.</li><li>Elk streepje (-) moet direct worden voorafgegaan en gevolgd door een letter of cijfer. Twee opeenvolgende streepjes zijn in containernamen niet toegestaan.</li><li>Naam mag 3-63 tekens lang zijn.</li></ul> |
| Gekoppelde Services/tabellen/pijplijnen |Unieke met in een gegevensfactory. De namen zijn niet hoofdlettergevoelig. |<ul><li>Maximum aantal tekens in een tabelnaam: 260.</li><li>Objectnamen moeten beginnen met een letter, cijfer of een onderstrepingsteken (_).</li><li>Volgende tekens zijn niet toegestaan: '. ', '+ ','?', '/', ' < ', ' > "," * ","%","&",": ","\\"</li><li>Streepjes ('-') zijn niet toegestaan in de namen van de gekoppelde services en alleen gegevenssets.</li></ul>  |
| Resourcegroep |Uniek zijn voor Microsoft Azure. De namen zijn niet hoofdlettergevoelig. | Zie voor meer informatie [Azure naamgevingsregels en -beperkingen](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions#naming-rules-and-restrictions). |

## <a name="next-steps"></a>Volgende stappen
Informatie over het maken van data Factory door volgen Stapsgewijze instructies in [Snelstartgids: Maak een gegevensfactory](quickstart-create-data-factory-powershell.md) artikel. 
