---
title: HTTP-invoegtoepassing Routering van toepassing op Azure Container Service (AKS)
description: Invoegtoepassing Routering van HTTP-toepassing in Azure Container Service (AKS) gebruiken
services: container-service
author: lachie83
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 04/25/2018
ms.author: laevenso
ms.openlocfilehash: 6e5a81742b4a6b21e5cfa28d8e772430f8ae30ba
ms.sourcegitcommit: c52123364e2ba086722bc860f2972642115316ef
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/11/2018
---
# <a name="http-application-routing"></a>Routering van HTTP-toepassing

De oplossing routering van HTTP-toepassing kunt gemakkelijk toegang geïmplementeerde toepassingen op uw AKS-cluster. Wanneer dit is ingeschakeld, configureert de oplossing voor een domeincontroller inkomend in uw cluster AKS. Bovendien, als toepassingen worden geïmplementeerd, maakt de oplossing ook openbaar toegankelijk DNS-namen voor eindpunten van de toepassingen.

Inschakelen van deze invoegtoepassing wordt gemaakt van een DNS-Zone in uw abonnement. Zie voor meer informatie over DNS-kosten [DNS-prijzen][dns-pricing].

## <a name="http-routing-solution-overview"></a>Overzicht van de HTTP-routering oplossing

De invoegtoepassing twee onderdelen implementeert een [Kubernetes inkomend Controller] [ ingress] en een [externe DNS] [ external-dns] controller.

- **Inkomend controller** -de ingress-controller wordt blootgesteld aan internet met behulp van een service Kubernetes van het type Load Balancer. De controller inkomend controleert en implementeert [Kubernetes inkomend resources][ingress-resource], routes naar de eindpunten van de toepassing gemaakt.
- **Externe DNS-controller** - bestandswijzigingsmeldingscontrole Kubernetes inkomend bronnen en DNS A-records gemaakt in de cluster-specifieke DNS-Zone.

## <a name="deploy-http-routing"></a>HTTP-routering implementeren

De invoegtoepassing Routering van HTTP-toepassing kan worden ingeschakeld via de Azure portal bij het implementeren van een cluster AKS.

![De HTTP-routering inschakelen](media/http-routing/create.png)

Zodra het cluster is geïmplementeerd, blader naar de resourcegroep AKS automatisch gemaakt en selecteert u de DNS-zone. Noteer de naam van de DNS-zone. Deze naam is vereist bij het implementeren van toepassingen naar het cluster AKS.

![De naam van de DNS-zone ophalen](media/http-routing/dns.png)

## <a name="use-http-routing"></a>Gebruik HTTP-routering

De HTTP-toepassing Routering oplossing kan alleen worden geactiveerd op inkomend bronnen die zijn gemarkeerd als volgt:

```
annotations:
  kubernetes.io/ingress.class: addon-http-application-routing
```

Maak een bestand met de naam `samples-http-application-routing.yaml` en kopieer de volgende YAML. Update op regel 43 `<CLUSTER_SPECIFIC_DNS_ZONE>` verzameld met de naam van de DNS-zone in de laatste stap van dit document.


```yaml
apiVersion: extensions/v1beta1
kind: Deployment
metadata:
  name: party-clippy
spec:
  template:
    metadata:
      labels:
        app: party-clippy
    spec:
      containers:
      - image: r.j3ss.co/party-clippy
        name: party-clippy
        tty: true
        command: ["party-clippy"]
        ports:
        - containerPort: 8080
---
apiVersion: v1
kind: Service
metadata:
  name: party-clippy
spec:
  ports:
  - port: 80
    protocol: TCP
    targetPort: 8080
  selector:
    app: party-clippy
  type: ClusterIP
---
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: party-clippy
  annotations:
    kubernetes.io/ingress.class: addon-http-application-routing
spec:
  rules:
  - host: party-clippy.<CLUSTER_SPECIFIC_DNS_ZONE>
    http:
      paths:
      - backend:
          serviceName: party-clippy
          servicePort: 80
        path: /
```

Gebruik de [kubectl toepassen] [ kubectl-apply] opdracht om de resources te maken.

```
$ kubectl apply -f samples-http-application-routing.yaml

deployment "party-clippy" created
service "party-clippy" created
ingress "party-clippy" created
```

Gebruik cURL of een browser om te navigeren naar de hostnaam die is opgegeven in de sectie van de host van de `samples-http-application-routing.yaml` bestand. De toepassing duurt maximaal één minuut voordat deze beschikbaar zijn via internet.

```
$ curl party-clippy.471756a6-e744-4aa0-aa01-89c4d162a7a7.canadaeast.aksapp.io

 _________________________________
/ It looks like you're building a \
\ microservice.                   /
 ---------------------------------
 \
  \
     __
    /  \
    |  |
    @  @
    |  |
    || |/
    || ||
    |\_/|
    \___/

```

## <a name="troubleshooting"></a>Problemen oplossen

Gebruik de [kubectl logboeken] [ kubectl-logs] opdracht om de logboeken voor de externe DNS-toepassing weer te geven. De logboeken moeten bevestigen dat een A- en DNS TXT-record is gemaakt.

```
$ kubectl logs -f deploy/addon-http-application-routing-external-dns -n kube-system

time="2018-04-26T20:36:19Z" level=info msg="Updating A record named 'party-clippy' to '52.242.28.189' for Azure DNS zone '471756a6-e744-4aa0-aa01-89c4d162a7a7.canadaeast.aksapp.io'."
time="2018-04-26T20:36:21Z" level=info msg="Updating TXT record named 'party-clippy' to '"heritage=external-dns,external-dns/owner=default"' for Azure DNS zone '471756a6-e744-4aa0-aa01-89c4d162a7a7.canadaeast.aksapp.io'."
```

Deze records kunnen ook worden weergegeven op de bron van de DNS-zone in de Azure portal.

![De DNS-records ophalen](media/http-routing/clippy.png)

Gebruik de [kubectl logboeken] [ kubectl-logs] opdracht om de logboeken voor de Controller Nginx-inkomend weer te geven. De logboeken te bevestigen, het maken van een resource inkomend en het opnieuw laden van de domeincontroller. Alle HTTP-activiteit worden geregistreerd.

```
$ kubectl logs -f deploy/addon-http-application-routing-nginx-ingress-controller -n kube-system

-------------------------------------------------------------------------------
NGINX Ingress controller
  Release:    0.13.0
  Build:      git-4bc943a
  Repository: https://github.com/kubernetes/ingress-nginx
-------------------------------------------------------------------------------

I0426 20:30:12.212936       9 flags.go:162] Watching for ingress class: addon-http-application-routing
W0426 20:30:12.213041       9 flags.go:165] only Ingress with class "addon-http-application-routing" will be processed by this ingress controller
W0426 20:30:12.213505       9 client_config.go:533] Neither --kubeconfig nor --master was specified.  Using the inClusterConfig.  This might not work.
I0426 20:30:12.213752       9 main.go:181] Creating API client for https://10.0.0.1:443
I0426 20:30:12.287928       9 main.go:225] Running in Kubernetes Cluster version v1.8 (v1.8.11) - git (clean) commit 1df6a8381669a6c753f79cb31ca2e3d57ee7c8a3 - platform linux/amd64
I0426 20:30:12.290988       9 main.go:84] validated kube-system/addon-http-application-routing-default-http-backend as the default backend
I0426 20:30:12.294314       9 main.go:105] service kube-system/addon-http-application-routing-nginx-ingress validated as source of Ingress status
I0426 20:30:12.426443       9 stat_collector.go:77] starting new nginx stats collector for Ingress controller running in namespace  (class addon-http-application-routing)
I0426 20:30:12.426509       9 stat_collector.go:78] collector extracting information from port 18080
I0426 20:30:12.448779       9 nginx.go:281] starting Ingress controller
I0426 20:30:12.463585       9 event.go:218] Event(v1.ObjectReference{Kind:"ConfigMap", Namespace:"kube-system", Name:"addon-http-application-routing-nginx-configuration", UID:"2588536c-4990-11e8-a5e1-0a58ac1f0ef2", APIVersion:"v1", ResourceVersion:"559", FieldPath:""}): type: 'Normal' reason: 'CREATE' ConfigMap kube-system/addon-http-application-routing-nginx-configuration
I0426 20:30:12.466945       9 event.go:218] Event(v1.ObjectReference{Kind:"ConfigMap", Namespace:"kube-system", Name:"addon-http-application-routing-tcp-services", UID:"258ca065-4990-11e8-a5e1-0a58ac1f0ef2", APIVersion:"v1", ResourceVersion:"561", FieldPath:""}): type: 'Normal' reason: 'CREATE' ConfigMap kube-system/addon-http-application-routing-tcp-services
I0426 20:30:12.467053       9 event.go:218] Event(v1.ObjectReference{Kind:"ConfigMap", Namespace:"kube-system", Name:"addon-http-application-routing-udp-services", UID:"259023bc-4990-11e8-a5e1-0a58ac1f0ef2", APIVersion:"v1", ResourceVersion:"562", FieldPath:""}): type: 'Normal' reason: 'CREATE' ConfigMap kube-system/addon-http-application-routing-udp-services
I0426 20:30:13.649195       9 nginx.go:302] starting NGINX process...
I0426 20:30:13.649347       9 leaderelection.go:175] attempting to acquire leader lease  kube-system/ingress-controller-leader-addon-http-application-routing...
I0426 20:30:13.649776       9 controller.go:170] backend reload required
I0426 20:30:13.649800       9 stat_collector.go:34] changing prometheus collector from  to default
I0426 20:30:13.662191       9 leaderelection.go:184] successfully acquired lease kube-system/ingress-controller-leader-addon-http-application-routing
I0426 20:30:13.662292       9 status.go:196] new leader elected: addon-http-application-routing-nginx-ingress-controller-5cxntd6
I0426 20:30:13.763362       9 controller.go:179] ingress backend successfully reloaded...
I0426 21:51:55.249327       9 event.go:218] Event(v1.ObjectReference{Kind:"Ingress", Namespace:"default", Name:"party-clippy", UID:"092c9599-499c-11e8-a5e1-0a58ac1f0ef2", APIVersion:"extensions", ResourceVersion:"7346", FieldPath:""}): type: 'Normal' reason: 'CREATE' Ingress default/party-clippy
W0426 21:51:57.908771       9 controller.go:775] service default/party-clippy does not have any active endpoints
I0426 21:51:57.908951       9 controller.go:170] backend reload required
I0426 21:51:58.042932       9 controller.go:179] ingress backend successfully reloaded...
167.220.24.46 - [167.220.24.46] - - [26/Apr/2018:21:53:20 +0000] "GET / HTTP/1.1" 200 234 "" "Mozilla/5.0 (compatible; MSIE 9.0; Windows NT 6.1; Trident/5.0)" 197 0.001 [default-party-clippy-80] 10.244.0.13:8080 234 0.004 200
```

## <a name="cleanup"></a>Opschonen

Verwijder de bijbehorende Kubernetes objecten in deze stap hebt gemaakt.

```
$ kubectl delete -f samples-http-application-routing.yaml

deployment "party-clippy" deleted
service "party-clippy" deleted
ingress "party-clippy" deleted
```

## <a name="next-steps"></a>Volgende stappen

Zie voor informatie over het installeren van een HTTPS beveiligd inkomend controller in AKS, [HTTPS, inkomend op Azure Container Service (AKS)][ingress-https]

<!-- LINKS - internal -->
[ingress-https]: ./ingress.md

<!-- LINKS - external -->
[dns-pricing]: https://azure.microsoft.com/pricing/details/dns/
[external-dns]: https://github.com/kubernetes-incubator/external-dns
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubectl-logs]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#logs
[ingress]: https://kubernetes.io/docs/concepts/services-networking/ingress/
[ingress-resource]: https://kubernetes.io/docs/concepts/services-networking/ingress/#the-ingress-resource