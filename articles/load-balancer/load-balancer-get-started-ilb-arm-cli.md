---
title: Een interne load balancer van het type Basic maken - Azure CLI 2.0 | Microsoft Docs
description: Leer hoe u een interne load balancer maakt met behulp van Azure CLI 2.0
services: load-balancer
documentationcenter: na
author: KumudD
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: load-balancer
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/27/2017
ms.author: kumud
ms.openlocfilehash: d90a4e74b6ad3bb95e91ad3a5327c887a87784bd
ms.sourcegitcommit: c3d53d8901622f93efcd13a31863161019325216
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2018
---
# <a name="create-an-internal-load-balancer-to-load-balance-vms-using-azure-cli-20"></a>Een interne load balancer maken met Azure CLI 2.0 om taken te verdelen over VM's

In dit artikel leest u hoe een interne load balancer maakt om taken te verdelen over VM's. U test de load balancer door twee virtuele machines (VM's) te implementeren waarop een Ubuntu-server wordt uitgevoerd en waarbij de taken van een web-app evenredig worden verdeeld.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)] 

Als u ervoor kiest om de CLI lokaal te installeren en te gebruiken, moet u voor deze zelfstudie versie 2.0.28 of hoger van Azure CLI uitvoeren. Voer `az --version` uit om de versie te bekijken. Als u Azure CLI 2.0 wilt installeren of upgraden, raadpleegt u [Azure CLI 2.0 installeren]( /cli/azure/install-azure-cli).

## <a name="create-a-resource-group"></a>Een resourcegroep maken

Maak een resourcegroep maken met [az group create](https://docs.microsoft.com/cli/azure/group#create). Een Azure-resourcegroep is een logische container waarin Azure-resources worden geïmplementeerd en beheerd.

In het volgende voorbeeld wordt een resourcegroep met de naam *myResourceGroupILB* gemaakt op de locatie *eastus*:

```azurecli-interactive
  az group create \
    --name myResourceGroupILB \
    --location eastus
```
## <a name="create-a-virtual-network"></a>Een virtueel netwerk maken

Maak met [az network vnet create](https://docs.microsoft.com/cli/azure/network/vnet#create) een virtueel netwerk met de naam *myVnet* met een subnet met de naam *mySubnet* in *myResourceGroup*.

```azurecli-interactive
  az network vnet create \
    --name myVnet
    --resource-group myResourceGroupILB \
    --location eastus \
    --subnet-name mySubnet
```
## <a name="create-basic-load-balancer"></a>Load balancer van het type Basic maken

In deze sectie wordt beschreven hoe u de volgende onderdelen van de load balancer kunt maken en configureren:
  - een front-end-IP-configuratie die het binnenkomende netwerkverkeer op de load balancer ontvangt.
  - een back-end-IP-pool waar de front-end-pool het netwerkverkeer op de load balancer naartoe stuurt.
  - een statustest die de status van de back-end-VM-exemplaren vaststelt.
  - een load balancer-regel die bepaalt hoe het verkeer over de VM's wordt verdeeld.

### <a name="create-the-load-balancer"></a>Load balancer maken

Maak met [az network lb create](https://docs.microsoft.com/cli/azure/network/lb?view=azure-cli-latest#create) een openbare load balancer van het type Basic met de naam **myLoadBalancer** die een front-end-IP-configuratie bevat met de naam **myFrontEnd** en een back-end-pool met de naam **myBackEndPool** die wordt gekoppeld aan het privé-IP-adres **10.0.0.7.

```azurecli-interactive
  az network lb create \
    --resource-group myResourceGroupILB \
    --name myLoadBalancer \
    --frontend-ip-name myFrontEnd \
    --private-ip-address 10.0.0.7 \
    --backend-pool-name myBackEndPool \
    --vnet-name myVnet \
    --subnet mySubnet      
  ```
### <a name="create-the-health-probe"></a>Statustest maken

Een statustest controleert alle exemplaren van de virtuele machines om ervoor te zorgen dat deze netwerkverkeer kunnen ontvangen. Het exemplaar van een virtuele machine met mislukte testcontroles wordt uit de load balancer verwijderd totdat deze weer online komt en een testcontrole bepaalt of deze in orde is. Maak met [az network lb probe create](https://docs.microsoft.com/cli/azure/network/lb/probe?view=azure-cli-latest#create) een statustest om de status van de virtuele machines te bewaken. 

```azurecli-interactive
  az network lb probe create \
    --resource-group myResourceGroupILB \
    --lb-name myLoadBalancer \
    --name myHealthProbe \
    --protocol tcp \
    --port 80   
```

### <a name="create-the-load-balancer-rule"></a>Load balancer-regel maken

Een load balancer-regel definieert de front-end-IP-configuratie voor het binnenkomende verkeer en de back-end-IP-pool om het verkeer te ontvangen, samen met de gewenste bron- en doelpoort. Maak met [az network lb rule create](https://docs.microsoft.com/cli/azure/network/lb/rule?view=azure-cli-latest#create) de regel *myLoadBalancerRuleWeb* voor het luisteren naar poort 80 in de front-endpool *myFrontEnd* en het verzenden van netwerkverkeer met evenredige taakverdeling naar de back-endadresgroep *myBackEndPool* waarbij ook van poort 80 gebruik wordt gemaakt. 

```azurecli-interactive
  az network lb rule create \
    --resource-group myResourceGroupILB \
    --lb-name myLoadBalancer \
    --name myHTTPRule \
    --protocol tcp \
    --frontend-port 80 \
    --backend-port 80 \
    --frontend-ip-name myFrontEnd \
    --backend-pool-name myBackEndPool \
    --probe-name myHealthProbe  
```

## <a name="create-servers-for-the-backend-address-pool"></a>Servers voor de back-end-adresgroep maken

Voordat u enkele VM's implementeert om uw load balancer te testen, maakt u de ondersteunende virtuele-netwerkbronnen.

###  <a name="create-a-network-security-group"></a>Een netwerkbeveiligingsgroep maken
Maak een netwerkbeveiligingsgroep om de binnenkomende verbindingen met uw virtuele netwerk te definiëren.

```azurecli-interactive
  az network nsg create \
    --resource-group myResourceGroupILB \
    --name myNetworkSecurityGroup
```

### <a name="create-a-network-security-group-rule"></a>Regel voor netwerkbeveiligingsgroep maken

Maak een regel voor de netwerkbeveiligingsgroep om binnenkomende verbindingen via poort 80 toe te staan.

```azurecli-interactive
  az network nsg rule create \
    --resource-group myResourceGroupILB \
    --nsg-name myNetworkSecurityGroup \
    --name myNetworkSecurityGroupRuleHTTP \
    --protocol tcp \
    --direction inbound \
    --source-address-prefix '*' \
    --source-port-range '*' \
    --destination-address-prefix '*' \
    --destination-port-range 22 \
    --access allow \
    --priority 300
```
### <a name="create-nics"></a>NIC's maken

Maak met [az network nic create](/cli/azure/network/nic#az_network_nic_create) twee netwerkinterfaces en koppel deze aan het privé-IP-adres en de netwerkbeveiligingsgroep. 

```azurecli-interactive
for i in `seq 1 2`; do
  az network nic create \
    --resource-group myResourceGroupILB \
    --name myNic$i \
    --vnet-name myVnet \
    --subnet mySubnet \
    --network-security-group myNetworkSecurityGroup \
    --lb-name myLoadBalancer \
    --lb-address-pools myBackEndPool
done
```

## <a name="create-backend-servers"></a>Back-endservers maken

In dit voorbeeld maakt u twee virtuele machines die worden gebruikt als back-endservers voor de load balancer. Installeer NGINX op de virtuele machines om te controleren of de load balancer is gemaakt.

### <a name="create-an-availability-set"></a>Een beschikbaarheidsset maken

Een beschikbaarheidsset maken met [az vm availabilityset create](/cli/azure/network/nic#az_network_availabilityset_create)

 ```azurecli-interactive
  az vm availability-set create \
    --resource-group myResourceGroupILB \
    --name myAvailabilitySet
```

### <a name="create-two-virtual-machines"></a>Twee virtuele machines maken

U kunt een cloud-init-configuratiebestand maken om NGINX te installeren en een Node.js-app 'Hello World' uit te voeren op een virtuele Linux-machine. Maak in uw huidige shell een bestand met de naam cloud-init.txt en plak de volgende configuratie in de shell. Zorg ervoor dat u het hele cloud-init-bestand correct kopieert, met name de eerste regel:

```yaml
#cloud-config
package_upgrade: true
packages:
  - nginx
  - nodejs
  - npm
write_files:
  - owner: www-data:www-data
  - path: /etc/nginx/sites-available/default
    content: |
      server {
        listen 80;
        location / {
          proxy_pass http://localhost:3000;
          proxy_http_version 1.1;
          proxy_set_header Upgrade $http_upgrade;
          proxy_set_header Connection keep-alive;
          proxy_set_header Host $host;
          proxy_cache_bypass $http_upgrade;
        }
      }
  - owner: azureuser:azureuser
  - path: /home/azureuser/myapp/index.js
    content: |
      var express = require('express')
      var app = express()
      var os = require('os');
      app.get('/', function (req, res) {
        res.send('Hello World from host ' + os.hostname() + '!')
      })
      app.listen(3000, function () {
        console.log('Hello world app listening on port 3000!')
      })
runcmd:
  - service nginx restart
  - cd "/home/azureuser/myapp"
  - npm init
  - npm install express -y
  - nodejs index.js
``` 
 
Maak de virtuele machines met [az vm create](/cli/azure/vm#az_vm_create).

 ```azurecli-interactive
for i in `seq 1 2`; do
  az vm create \
    --resource-group myResourceGroupILB \
    --name myVM$i \
    --availability-set myAvailabilitySet \
    --nics myNic$i \
    --image UbuntuLTS \
    --generate-ssh-keys \
    --custom-data cloud-init.txt
    done
```
Het kan enkele minuten duren voordat de VM's zijn geïmplementeerd.

### <a name="create-a-vm-for-testing-the-load-balancer"></a>Een VM maken voor het testen van de load balancer

Als u de load balancer wilt testen, maakt u een virtuele machine, *myVMTest*, en koppelt u deze aan *myNic3*.

```azurecli-interactive
 az vm create \
    --resource-group myResourceGroupILB \
    --name myVMTest \
    --image win2016datacenter \
    --admin-username azureuser \
    --admin-password myPassword123456!
```

## <a name="test-the-internal-load-balancer"></a>Interne load balancer testen

Als u de load balancer wilt testen, moet u eerst het privé-IP-adres van de load balancer opvragen. Vervolgens meldt u zich aan bij de virtuele machine myVMTest en typt u het privé-IP-adres in de adresbalk van de webbrowser.

Gebruik [az network lb show](/cli/azure/network/public-ip##az-network-lb-show) om het privé-IP-adres van de load balancer op te vragen. Kopieer het privé-IP-adres en plak het in de adresbalk van een webbrowser op uw virtuele machine - *myVMTest*.

```azurecli-interactive
  az network lb show \
    --name myLoadBalancer
    --resource-group myResourceGroupILB
``` 
![Load balancer testen](./media/load-balancer-get-started-ilb-arm-cli/load-balancer-test.png)

## <a name="clean-up-resources"></a>Resources opschonen

U kunt de opdracht [az group delete](/cli/azure/group#az_group_delete) gebruiken om de resourcegroep, de load balancer en alle gerelateerde resources te verwijderen wanneer u deze niet meer nodig hebt.

```azurecli-interactive 
  az group delete --name myResourceGroupILB
```


## <a name="next-steps"></a>Volgende stappen
In dit artikel hebt u een interne load balancer van het type Basic gemaakt, VM's aan de load balancer gekoppeld, een regel voor het load balancer-verkeer geconfigureerd, een statustest gemaakt en vervolgens de load balancer getest. Als u meer wilt weten over load balancers en de bijbehorende resources, raadpleegt u de artikelen met procedures.
