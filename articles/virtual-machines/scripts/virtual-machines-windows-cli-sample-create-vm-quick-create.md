---
title: Voorbeeld van Azure CLI-script - Snel een virtuele machine met Windows Server 2016 maken | Microsoft Docs
description: Voorbeeld van Azure CLI-script - Snel een virtuele machine met Windows Server 2016 maken
services: virtual-machines-Windows
documentationcenter: virtual-machines
author: rickstercdn
manager: timlt
editor: tysonn
tags: ''
ms.assetid: ''
ms.service: virtual-machines-Windows
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: vm-Windows
ms.workload: infrastructure
ms.date: 02/23/2017
ms.author: rickstercdn
ms.custom: mvc
ms.openlocfilehash: bc2e42f6097b4e2605b55424a8c3e7185f4fed3d
ms.sourcegitcommit: 8c3267c34fc46c681ea476fee87f5fb0bf858f9e
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/09/2018
---
# <a name="quick-create-a-virtual-machine-with-the-azure-cli"></a>Snel een virtuele machine maken met de Azure CLI

Met dit script maakt u een virtuele Azure-machine waarop Windows Server 2016 wordt uitgevoerd. Nadat het script is uitgevoerd, hebt u toegang tot de virtuele machine via een Extern bureaublad-verbinding.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Voorbeeldscript

[!code-azurecli-interactive[main](../../../cli_scripts/virtual-machine/create-vm-quick/create-windows-vm-quick.sh "Quick Create VM")]

## <a name="clean-up-deployment"></a>Opschonen van implementatie 

Gebruik de volgende opdracht om de resourcegroep, VM, en alle gerelateerde resources te verwijderen.

```azurecli-interactive 
az group delete --name myResourceGroup --yes
```

## <a name="script-explanation"></a>Uitleg van het script

Dit script gebruikt de volgende opdrachten voor het maken van een resourcegroep, een virtuele machine en alle gerelateerde resources. Elke opdracht in de tabel is een koppeling naar specifieke documentatie over de opdracht.

| Opdracht | Opmerkingen |
|---|---|
| [az group create](https://docs.microsoft.com/cli/azure/group#az_group_create) | Hiermee maakt u een resourcegroep waarin alle resources worden opgeslagen. |
| [az vm create](https://docs.microsoft.com/cli/azure/vm#az_vm_create) | Hiermee maakt u de virtuele machine en verbindt u deze met de netwerkkaart, het virtuele netwerk, het subnet en de netwerkbeveiligingsgroep. Met deze opdracht geeft u ook de installatiekopie van de virtuele machine op die moet worden gebruikt, samen met beheerdersreferenties.  |
| [az group delete](https://docs.microsoft.com/cli/azure/vm/extension#az_vm_extension_set) | Hiermee verwijdert u een resourcegroep met inbegrip van alle geneste resources. |

## <a name="next-steps"></a>Volgende stappen

Raadpleeg de [documentatie van Azure CLI](https://docs.microsoft.com/cli/azure) voor meer informatie over de Azure CLI.

U kunt extra CLI-scriptvoorbeelden voor virtuele machines vinden in de [Azure-documentatie voor Windows-VM's](../windows/cli-samples.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
