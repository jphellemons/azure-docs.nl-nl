---
title: Een Azure Kubernetes Service (AKS)-cluster maken
description: Een cluster AKS maken met de CLI of Azure portal.
services: container-service
author: neilpeterson
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 02/12/2018
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: 00672b6272ce9c775621e519c327c0b8368bc220
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/10/2018
---
# <a name="create-an-azure-kubernetes-service-aks-cluster"></a>Een Azure Kubernetes Service (AKS)-cluster maken

Een Azure Kubernetes Service (AKS)-cluster kan worden gemaakt met de Azure CLI of Azure portal.

## <a name="azure-cli"></a>Azure-CLI

Gebruik de [az aks maken] [ az-aks-create] opdracht de cluster AKS maken.

```azurecli-interactive
az aks create --resource-group myResourceGroup --name myAKSCluster
```

De volgende opties zijn beschikbaar met de `az aks create` opdracht.

| Argument | Beschrijving | Vereist |
|---|---|:---:|
| `--name` `-n` | De bronnaam voor het beheerde cluster. | ja |
| `--resource-group` `-g` | Naam van de resourcegroep Azure Kubernetes Service. | ja |
| `--admin-username` `-u` | De gebruikersnaam voor de virtuele Linux-Machines.  Standaard: azureuser. | nee |
| ` --client-secret` | Het geheim dat is gekoppeld aan de service-principal. | nee |
| `--dns-name-prefix` `-p` | DNS-voorvoegsel voor het openbare ip-adres van clusters. | nee |
| `--generate-ssh-keys` | SSH openbare en persoonlijke sleutelbestanden genereren indien deze ontbreken. | nee |
| `--kubernetes-version` `-k` | De versie van Kubernetes moet worden gebruikt voor het maken van het cluster, zoals '1.7.9' of '1.8.2'.  Standaard: 1.7.7. | nee |
| `--no-wait` | Wacht niet totdat de langlopende bewerking te voltooien. | nee |
| `--node-count` `-c` | Het standaardaantal knooppunten voor de knooppunt-adresgroepen.  Standaard: 3. | nee |
| `--node-osdisk-size` | De osDisk-grootte in GB aan knooppunt groep virtuele Machine. | nee |
| `--node-vm-size` `-s` | De grootte van de virtuele Machine.  Default: Standard_D1_v2. | nee |
| `--service-principal` | Service-principal is gebruikt voor verificatie van de cluster. | nee |
| `--ssh-key-value` | SSH-sleutelbestand waarde of sleutel bestandspad.  Default: ~/.ssh/id_rsa.pub. | nee |
| `--tags` | Ruimte gescheiden labels in 'key [= value]' indeling. Gebruik '' bestaande labels wissen. | nee |

## <a name="azure-portal"></a>Azure Portal

Zie voor instructies over het implementeren van een cluster AKS met de Azure-portal, de Azure Kubernetes Service (AKS) [Azure portal Quick Start][aks-portal-quickstart].

<!-- LINKS - internal -->
[az-aks-create]: /cli/azure/aks?view=azure-cli-latest#az_aks_create
[aks-portal-quickstart]: kubernetes-walkthrough-portal.md
