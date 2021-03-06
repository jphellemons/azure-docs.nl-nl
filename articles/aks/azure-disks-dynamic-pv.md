---
title: De schijf van Azure gebruiken met AKS
description: Azure-schijven met AKS gebruiken
services: container-service
author: neilpeterson
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 03/06/2018
ms.author: nepeters
ms.openlocfilehash: 3841222d08b23f43f69e77fa43c469793b70ce63
ms.sourcegitcommit: b7290b2cede85db346bb88fe3a5b3b316620808d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/05/2018
ms.locfileid: "34801378"
---
# <a name="persistent-volumes-with-azure-disks"></a>Permanente volumes met Azure-schijven

Een permanente volume vertegenwoordigt een onderdeel van de opslag die is ingericht voor gebruik met Kubernetes gehele product. Een permanente volume kan worden gebruikt door een of meer gehele product en worden dynamisch of statisch ingericht. Zie voor meer informatie over Kubernetes permanente volumes [Kubernetes permanente volumes][kubernetes-volumes].

Dit Documentdetails permanente volumes gebruiken met Azure-schijven in een Azure Kubernetes Service (AKS)-cluster.

> [!NOTE]
> Een Azure-schijf kan alleen worden gekoppeld met de modus toegangstype ReadWriteOnce waardoor beschikbaar is op slechts één AKS knooppunt. Als dat een permanente volume delen op meerdere knooppunten, overweeg dan [Azure Files][azure-files-pvc].

## <a name="built-in-storage-classes"></a>Ingebouwde Opslagklassen

Een opslagklasse wordt gebruikt om te definiëren hoe een opslageenheid dynamisch wordt gemaakt met een permanente volume. Zie voor meer informatie over Kubernetes Opslagklassen [Kubernetes Opslagklassen][kubernetes-storage-classes].

Elk cluster AKS bevat twee vooraf gemaakte Opslagklassen, beide geconfigureerd om te werken met Azure-schijven. De `default` opslagklasse voorziet in een standard-Azure schijven. De `managed-premium` opslagklasse voorziet in een premium Azure-schijf. Als de knooppunten AKS in uw cluster premium-opslag gebruikt, selecteert de `managed-premium` klasse.

Gebruik de [kubectl ophalen sc] [ kubectl-get] opdracht om te zien van de vooraf gemaakte Opslagklassen.

```console
NAME                PROVISIONER                AGE
default (default)   kubernetes.io/azure-disk   1h
managed-premium     kubernetes.io/azure-disk   1h
```

> [!NOTE]
> Permanente volume claims zijn opgegeven in GiB maar Azure beheerd schijven wordt gefactureerd per SKU voor een specifieke grootte. Deze SKU's bereik van 32GiB voor S4 of P4 schijven 4TiB voor aanbeveling S50 of P50 schijven. Bovendien de doorvoer en prestaties van de IOPS van een Premium beheerd schijf afhankelijk is van zowel de SKU en de exemplaargrootte van de knooppunten in het cluster AKS. Zie [prijzen en prestaties van beheerde schijven][managed-disk-pricing-performance].

## <a name="create-persistent-volume-claim"></a>Permanente volume claim maken

Een claim permanente volume (PVC) wordt gebruikt voor het automatisch inrichten van opslag op basis van een opslagklasse. In dit geval een PVC kunt een van de vooraf gemaakte Opslagklassen maken een standard- of premium Azure beheerde schijven.

Maak een bestand met de naam `azure-premium.yaml`, en kopieer het volgende manifest.

Let op dat de `managed-premium` opslagklasse is opgegeven in de aantekening en de claim vraagt een schijf `5GB` aan de grootte van `ReadWriteOnce` toegang.

```yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: azure-managed-disk
  annotations:
    volume.beta.kubernetes.io/storage-class: managed-premium
spec:
  accessModes:
  - ReadWriteOnce
  resources:
    requests:
      storage: 5Gi
```

Maken van de claim permanente volume met de [kubectl toepassen] [ kubectl-apply] opdracht.

```azurecli-interactive
kubectl apply -f azure-premium.yaml
```

## <a name="using-the-persistent-volume"></a>Met behulp van de permanente volume

Nadat de claim permanente volume is gemaakt en de schijf is ingericht, een schil kan worden gemaakt met toegang tot de schijf. Het volgende manifest maakt een schil die gebruikmaakt van de claim permanente volume `azure-managed-disk` koppelen van de Azure-schijf op de `/mnt/azure` pad.

Maak een bestand met de naam `azure-pvc-disk.yaml`, en kopieer het volgende manifest.

```yaml
kind: Pod
apiVersion: v1
metadata:
  name: mypod
spec:
  containers:
    - name: myfrontend
      image: nginx
      volumeMounts:
      - mountPath: "/mnt/azure"
        name: volume
  volumes:
    - name: volume
      persistentVolumeClaim:
        claimName: azure-managed-disk
```

Maken van de schil met de [kubectl toepassen] [ kubectl-apply] opdracht.

```azurecli-interactive
kubectl apply -f azure-pvc-disk.yaml
```

U hebt nu een actieve schil met uw Azure-schijf gekoppeld in de `/mnt/azure` directory. Deze configuratie kan worden weergegeven bij de inspectie van uw schil via `kubectl describe pod mypod`.

## <a name="next-steps"></a>Volgende stappen

Meer informatie over Kubernetes permanente volumes met behulp van Azure-schijven.

> [!div class="nextstepaction"]
> [Kubernetes-invoegtoepassing voor Azure-schijven][azure-disk-volume]

<!-- LINKS - external -->
[access-modes]: https://kubernetes.io/docs/concepts/storage/persistent-volumes/#access-modes
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubernetes-storage-classes]: https://kubernetes.io/docs/concepts/storage/storage-classes/
[kubernetes-volumes]: https://kubernetes.io/docs/concepts/storage/persistent-volumes/
[managed-disk-pricing-performance]: https://azure.microsoft.com/pricing/details/managed-disks/

<!-- LINKS - internal -->
[azure-disk-volume]: azure-disk-volume.md 
[azure-files-pvc]: azure-files-dynamic-pv.md
[premium-storage]: ../virtual-machines/windows/premium-storage.md
