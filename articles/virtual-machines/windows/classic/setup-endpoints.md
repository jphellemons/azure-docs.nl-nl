---
title: Eindpunten van een klassieke Windows-VM instellen | Microsoft Docs
description: Informatie over het instellen van eindpunten voor een klassieke Windows-VM in de Azure portal voor de communicatie met een virtuele Windows-machine in Azure.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-service-management
ms.assetid: 8afc21c2-d3fb-43a3-acce-aa06be448bb6
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 06/09/2017
ms.author: cynthn
ms.openlocfilehash: cca9adb40557cf7bf9e1d4129fc6bd61cbf0df4f
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/06/2018
---
# <a name="how-to-set-up-endpoints-on-a-classic-windows-virtual-machine-in-azure"></a>Het instellen van de eindpunten van een klassieke Windows-virtuele machine in Azure
Alle Windows kan de virtuele machines die u maakt in Azure met behulp van het klassieke implementatiemodel automatisch communiceren via een particulier netwerkkanaal met andere virtuele machines in dezelfde cloudservice- of virtuele netwerk. Computers op Internet of een andere virtuele netwerken vereisen echter eindpunten het binnenkomend netwerkverkeer naar een virtuele machine wordt omgeleid. In dit artikel is ook beschikbaar voor [virtuele Linux-machines](../../linux/classic/setup-endpoints.md).

> [!IMPORTANT]
> Azure heeft twee verschillende implementatiemodellen voor het maken en werken met resources: [Resource Manager en Classic](../../../resource-manager-deployment-model.md). In dit artikel bevat informatie over met behulp van het klassieke implementatiemodel. U doet er verstandig aan voor de meeste nieuwe implementaties het Resource Manager-model te gebruiken.
> [!INCLUDE [virtual-machines-common-classic-createportal](../../../../includes/virtual-machines-classic-portal.md)]

In de **Resource Manager** implementatiemodel eindpunten zijn geconfigureerd met behulp van **Netwerkbeveiligingsgroepen (nsg's)**. Zie voor meer informatie [toestaan van externe toegang tot uw virtuele machine met de Azure portal](../nsg-quickstart-portal.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

Wanneer u een virtuele Windows-computer in de Azure portal maakt, worden algemene eindpunten zoals die voor extern bureaublad- en Windows PowerShell voor externe toegang doorgaans automatisch voor u gemaakt. Tijdens het maken van de virtuele machine of daarna indien nodig, kunt u extra eindpunten configureren.

[!INCLUDE [virtual-machines-common-classic-setup-endpoints](../../../../includes/virtual-machines-common-classic-setup-endpoints.md)]

## <a name="next-steps"></a>Volgende stappen
* Zie voor het gebruik van een Azure PowerShell-cmdlet voor het instellen van een VM-eindpunt, [toevoegen AzureEndpoint](https://msdn.microsoft.com/library/azure/dn495300.aspx).
* Zie voor het gebruik van een Azure PowerShell-cmdlet voor het beheren van een ACL voor een eindpunt, [beheren toegangsbeheerlijsten (ACL's) voor eindpunten met behulp van PowerShell](../../../virtual-network/virtual-networks-acl-powershell.md).
* Als u een virtuele machine hebt gemaakt in het Resource Manager-implementatiemodel, kunt u Azure PowerShell te [netwerk beveiligingsgroepen maken](../../../virtual-network/tutorial-filter-network-traffic.md) aan verkeer voor beheer met de virtuele machine.
