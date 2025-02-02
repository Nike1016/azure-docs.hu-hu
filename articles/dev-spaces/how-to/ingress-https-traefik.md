---
title: Egyéni bejövő traefik-vezérlő használata és HTTPS konfigurálása
titleSuffix: Azure Dev Spaces
services: azure-dev-spaces
ms.service: azure-dev-spaces
author: zr-msft
ms.author: zarhoads
ms.date: 08/13/2019
ms.topic: conceptual
description: Ismerje meg, hogyan konfigurálhatja az Azure dev Spaces-t egyéni traefik bemenő vezérlő használatára, és hogyan konfigurálhatja a HTTPS-t az adott bejövő vezérlő használatával
keywords: Docker, Kubernetes, Azure, AK, Azure Kubernetes szolgáltatás, tárolók, Helm, Service Mesh, szolgáltatás háló útválasztás, kubectl, k8s
ms.openlocfilehash: 50908bde65b69cb475391cd30bca758dd571f114
ms.sourcegitcommit: 18061d0ea18ce2c2ac10652685323c6728fe8d5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/15/2019
ms.locfileid: "69036942"
---
# <a name="use-a-custom-traefik-ingress-controller-and-configure-https"></a>Egyéni bejövő traefik-vezérlő használata és HTTPS konfigurálása

Ebből a cikkből megtudhatja, hogyan konfigurálhatja az Azure dev Spaces-t egyéni traefik beáramlási vezérlő használatára. A cikk azt is bemutatja, hogyan konfigurálhatja az egyéni bejövő vezérlőt a HTTPS használatára.

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés. Ha még nincs fiókja, hozzon létre egy [ingyenes fiókot][azure-account-create].
* [Telepített Azure CLI][az-cli].
* [Azure Kubernetes Service (ak) fürt, amelyen engedélyezve van az Azure dev Spaces][qs-cli].
* a [kubectl][kubectl] telepítve van.
* A [Helm 2,13 vagy újabb rendszer van telepítve][helm-installed].
* [Egy egyéni tartomány][custom-domain] egy [DNS-zónával][dns-zone] , amely ugyanabban az erőforráscsoporthoz van, mint az AK-fürt.

## <a name="configure-a-custom-traefik-ingress-controller"></a>Egyéni bejövő traefik-vezérlő konfigurálása

Kapcsolódjon a fürthöz a [kubectl][kubectl]és a Kubernetes parancssori ügyfél használatával. A Kubernetes `kubectl` -fürthöz való kapcsolódás konfigurálásához használja az az az [AK Get-hitelesítőadats][az-aks-get-credentials] parancsot. Ez a parancs letölti a hitelesítő adatokat, és konfigurálja a Kubernetes CLI-t a használatára.

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKS
```

A fürthöz való csatlakozás ellenőrzéséhez használja a [kubectl get][kubectl-get] parancsot a fürtcsomópontok listájának lekéréséhez.

```console
$ kubectl get nodes
NAME                                STATUS   ROLES   AGE    VERSION
aks-nodepool1-12345678-vmssfedcba   Ready    agent   13m    v1.14.1
```

Hozzon létre egy Kubernetes-névteret a traefik beáramló vezérlőhöz, és telepítse azt a használatával `helm`.

```console
kubectl create ns traefik
helm init --wait
helm install stable/traefik --name traefik --namespace traefik --set kubernetes.ingressClass=traefik --set kubernetes.ingressEndpoint.useDefaultPublishedService=true
```

Szerezze be az IP-címet a traefik beáramló vezérlő szolgáltatáshoz a [kubectl Get][kubectl-get]használatával.

```console
kubectl get svc -n traefik --watch
```

A minta kimenet a *traefik* -névtérben lévő összes szolgáltatás IP-címeit jeleníti meg.

```console
NAME      TYPE           CLUSTER-IP    EXTERNAL-IP   PORT(S)                      AGE
traefik   LoadBalancer   10.0.205.78   <pending>     80:32484/TCP,443:30620/TCP   20s
...
traefik   LoadBalancer   10.0.205.78   MY_EXTERNAL_IP   80:32484/TCP,443:30620/TCP   60s
```

Adjon hozzá *egy* rekordot a DNS-zónához a traefik szolgáltatás külső IP-címével az [az Network DNS Record-set a Add-Record][az-network-dns-record-set-a-add-record]paranccsal.

```console
az network dns record-set a add-record \
    --resource-group myResourceGroup \
    --zone-name MY_CUSTOM_DOMAIN \
    --record-set-name *.traefik \
    --ipv4-address MY_EXTERNAL_IP
```

A fenti példa *egy* rekordot vesz fel a *MY_CUSTOM_DOMAIN* DNS-zónába.

Ebben a cikkben az [Azure dev Spaces Bike Sharing Sample Application](https://github.com/Azure/dev-spaces/tree/master/samples/BikeSharingApp) használatával mutatjuk be az Azure dev Spaces szolgáltatást. Az alkalmazás klónozása a GitHubról, majd a címtárba való navigálása:

```cmd
git clone https://github.com/Azure/dev-spaces
cd dev-spaces/samples/BikeSharingApp/charts
```

Nyissa meg a [Values. YAML][values-yaml] , és cserélje le a *< REPLACE_ME_WITH_HOST_SUFFIX >* összes példányát a *traefik. MY_CUSTOM_DOMAIN* a tartományát a *MY_CUSTOM_DOMAIN*használatára. Cserélje le a *kubernetes.IO/ingress.Class: traefik-azds # dev Spaces-specifikust* a *kubernetes.IO/ingress.Class: traefik # Custom*beáramló kifejezésre. Az alábbi példa egy frissített `values.yaml` fájlt mutat be:

```yaml
# This is a YAML-formatted file.
# Declare variables to be passed into your templates.

bikesharingweb:
  ingress:
    annotations:
      kubernetes.io/ingress.class: traefik  # Custom Ingress
    hosts:
      - dev.bikesharingweb.traefik.MY_CUSTOM_DOMAIN  # Assumes deployment to the 'dev' space

gateway:
  ingress:
    annotations:
      kubernetes.io/ingress.class: traefik  # Custom Ingress
    hosts:
      - dev.gateway.traefik.MY_CUSTOM_DOMAIN  # Assumes deployment to the 'dev' space
```

Mentse a módosításokat, és zárjuk be a fájlt.

Telepítse a minta alkalmazást a `helm install`használatával.

```console
helm install -n bikesharing . --dep-up --namespace dev --atomic
```

A fenti példa telepíti a minta alkalmazást a *fejlesztői* névtérbe.

Válassza ki a *fejlesztői* területet a minta alkalmazással `azds space select` , és jelenítse meg azokat az URL-címeket `azds list-uris`, amelyekkel hozzáfér a minta alkalmazáshoz a használatával.

```console
azds space select -n dev
azds list-uris
```

Az alábbi kimenet a példákban szereplő URL `azds list-uris`-címeket mutatja.

```console
Uri                                                  Status
---------------------------------------------------  ---------
http://dev.bikesharingweb.traefik.MY_CUSTOM_DOMAIN/  Available
http://dev.gateway.traefik.MY_CUSTOM_DOMAIN/         Available
```

A `azds list-uris` parancsból nyissa meg a nyilvános URL-címet a *bikesharingweb* szolgáltatáshoz. A fenti példában a *bikesharingweb* szolgáltatás `http://dev.bikesharingweb.traefik.MY_CUSTOM_DOMAIN/`nyilvános URL-címe a következő:.

A `azds space select` parancs használatával hozzon létre egy gyermek területet a *dev* -ben, és sorolja fel az URL-címeket a gyermek fejlesztői terület eléréséhez.

```console
azds space select -n dev/azureuser1 -y
azds list-uris
```

Az alábbi kimenet a példaként `azds list-uris` szolgáló URL-címeket mutatja be a *azureuser1* gyermek-fejlesztési térben található minta alkalmazás eléréséhez.

```console
Uri                                                  Status
---------------------------------------------------  ---------
http://azureuser1.s.dev.bikesharingweb.traefik.MY_CUSTOM_DOMAIN/  Available
http://azureuser1.s.dev.gateway.traefik.MY_CUSTOM_DOMAIN/         Available
```

Nyissa meg a *bikesharingweb* szolgáltatást a *azureuser1* gyermekének fejlesztői területén, és nyissa meg `azds list-uris` a paranccsal a nyilvános URL-címet. A fenti példában a *bikesharingweb* szolgáltatás nyilvános URL-címe a *azureuser1* gyermekének `http://azureuser1.s.dev.bikesharingweb.traefik.MY_CUSTOM_DOMAIN/`fejlesztői területén.

## <a name="configure-the-traefik-ingress-controller-to-use-https"></a>A traefik bejövő forgalom-vezérlő konfigurálása HTTPS használatára

Hozzon `dev-spaces/samples/BikeSharingApp/traefik-values.yaml` létre egy, az alábbi példához hasonló fájlt. Frissítse az *e-mail* -értéket a saját e-mail-címével, amely a tanúsítvány titkosítással történő létrehozásához használatos.

```yaml
fullnameOverride: traefik
replicas: 1
cpuLimit: 400m
memoryRequest: 200Mi
memoryLimit: 500Mi
externalTrafficPolicy: Local
kubernetes:
  ingressClass: traefik
  ingressEndpoint:
    useDefaultPublishedService: true
dashboard:
  enabled: false
debug:
  enabled: false
accessLogs:
  enabled: true
  fields:
    defaultMode: keep
    headers:
      defaultMode: keep
      names:
        Authorization: redact
acme:
  enabled: true
  email: "someone@example.com"
  staging: false
  challengeType: tls-alpn-01
ssl:
  enabled: true
  enforced: true
  permanentRedirect: true
  tlsMinVersion: VersionTLS12
  cipherSuites:
    - TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256
    - TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384
    - TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256
    - TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384
    - TLS_RSA_WITH_AES_128_GCM_SHA256
    - TLS_RSA_WITH_AES_256_GCM_SHA384
    - TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256
```

Frissítse a *traefik* szolgáltatást a `helm repo update` használatával, beleértve a létrehozott *traefik-Values. YAML* fájlt.

```console
cd ..
helm upgrade traefik stable/traefik --namespace traefik --values traefik-values.yaml
```

A fenti parancs a traefik szolgáltatás új verzióját futtatja a *traefik-Values. YAML* értékek alapján, és eltávolítja az előző szolgáltatást. A traefik szolgáltatás egy TLS-tanúsítványt is létrehoz a titkosításhoz, és elindítja a webes forgalom átirányítását a HTTPS használatára.

> [!NOTE]
> Eltarthat néhány percig, amíg a traefik szolgáltatás új verziója el nem indul. A folyamatot a használatával `kubectl get pods --namespace traefik --watch`tekintheti meg.

Navigáljon az alkalmazáshoz a *dev/azureuser1* , és figyelje meg, hogy a rendszer átirányítja a https használatára. Azt is figyelje meg, hogy az oldal betöltődik, de a böngésző bizonyos hibákat jelez. A böngésző konzoljának megnyitásakor a hiba a HTTP-erőforrások betöltésére tett HTTPS-oldalra vonatkozik. Példa:

```console
Mixed Content: The page at 'https://azureuser1.s.dev.bikesharingweb.traefik.MY_CUSTOM_DOMAIN/devsignin' was loaded over HTTPS, but requested an insecure resource 'http://azureuser1.s.dev.gateway.traefik.MY_CUSTOM_DOMAIN/api/user/allUsers'. This request has been blocked; the content must be served over HTTPS.
```

A hiba kijavításához frissítse a [BikeSharingWeb/azds. YAML][azds-yaml] -t a *traefik* használata a *kubernetes.IO/ingress.Class* és az egyéni tartomány számára a *$ (hostSuffix)* értékre. Példa:

```yaml
...
    ingress:
      annotations:
        kubernetes.io/ingress.class: traefik
      hosts:
      # This expands to [space.s.][rootSpace.]bikesharingweb.<random suffix>.<region>.azds.io
      - $(spacePrefix)$(rootSpacePrefix)bikesharingweb.traefik.MY_CUSTOM_DOMAIN
...
```

Frissítse a [BikeSharingWeb/Package. JSON][package-json] fájlt az *URL-* csomagra vonatkozó függőséggel.

```json
{
...
    "react-responsive": "^6.0.1",
    "universal-cookie": "^3.0.7",
    "url": "0.11.0"
  },
...
```

Frissítse a *getApiHostAsync* metódust a [BikeSharingWeb/Pages/Helpers. js][helpers-js] fájlban a https használatára:

```javascript
...
    getApiHostAsync: async function() {
        const apiRequest = await fetch('/api/host');
        const data = await apiRequest.json();
        
        var urlapi = require('url');
        var url = urlapi.parse(data.apiHost);

        console.log('apiHost: ' + "https://"+url.host);
        return "https://"+url.host;
    },
...
```

Navigáljon a `BikeSharingWeb` címtárhoz, `azds up` és a használatával futtassa a frissített BikeSharingWeb szolgáltatást.

```console
cd BikeSharingWeb/
azds up
```

Navigáljon az alkalmazáshoz a *dev/azureuser1* , és figyelje meg, hogy a rendszer a HTTPS-t hibák nélkül használja.

## <a name="next-steps"></a>További lépések

Ismerje meg, hogy az Azure dev Spaces hogyan segíti az összetettebb alkalmazások fejlesztését több tárolóban, és hogyan egyszerűsítheti az együttműködésen alapuló fejlesztést, ha a kód különböző verzióival vagy ágaival dolgozik a különböző helyeken.

> [!div class="nextstepaction"]
> [Csoportmunka az Azure fejlesztői Spaces szolgáltatásban][team-development-qs]


[az-cli]: /cli/azure/install-azure-cli?view=azure-cli-latest
[az-aks-get-credentials]: /cli/azure/aks?view=azure-cli-latest#az-aks-get-credentials
[az-network-dns-record-set-a-add-record]: /cli/azure/network/dns/record-set/a?view=azure-cli-latest#az-network-dns-record-set-a-add-record
[custom-domain]: ../../app-service/manage-custom-dns-buy-domain.md#buy-the-domain
[dns-zone]: ../../dns/dns-getstarted-cli.md
[qs-cli]: ../quickstart-cli.md
[team-development-qs]: ../quickstart-team-development.md

[azds-yaml]: https://github.com/Azure/dev-spaces/blob/master/samples/BikeSharingApp/BikeSharingWeb/azds.yaml
[azure-account-create]: https://azure.microsoft.com/free
[helm-installed]: https://github.com/helm/helm/blob/master/docs/install.md
[helpers-js]: https://github.com/Azure/dev-spaces/blob/master/samples/BikeSharingApp/BikeSharingWeb/pages/helpers.js#L7
[kubectl]: https://kubernetes.io/docs/user-guide/kubectl/
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[package-json]: https://github.com/Azure/dev-spaces/blob/master/samples/BikeSharingApp/BikeSharingWeb/package.json
[values-yaml]: https://github.com/Azure/dev-spaces/blob/master/samples/BikeSharingApp/charts/values.yaml