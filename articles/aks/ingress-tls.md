---
title: 使用自動 TLS 建立輸入
titleSuffix: Azure Kubernetes Service
description: 瞭解如何安裝和設定 NGINX 輸入控制器，以使用 Let's Encrypt 在 Azure Kubernetes Service (AKS) 叢集中產生自動 TLS 憑證。
services: container-service
ms.topic: article
ms.date: 08/17/2020
ms.openlocfilehash: 3cf7b069d6f010a4461b22c5326589ad3ec31204
ms.sourcegitcommit: 0aec60c088f1dcb0f89eaad5faf5f2c815e53bf8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/14/2021
ms.locfileid: "98186255"
---
# <a name="create-an-https-ingress-controller-on-azure-kubernetes-service-aks"></a>在 Azure Kubernetes Service (AKS) 上建立 HTTPS 輸入控制器

輸入控制器是一項可為 Kubernetes 服務提供反向 Proxy、可設定的流量路由和 TLS 終止的軟體。 Kubernetes 輸入資源可用來設定個別 Kubernetes 服務的輸入規則和路由。 透過輸入控制器和輸入規則，您可以使用單一 IP 位址將流量路由至 Kubernetes 叢集中的多個服務。

本文示範如何在 Azure Kubernetes Service (AKS) 叢集中部署 [NGINX 輸入控制器][nginx-ingress]。 [cert-manager][cert-manager] 專案會用來自動產生和設定 [Let's Encrypt][lets-encrypt] 憑證。 最後，會有兩個應用程式在 AKS 叢集中執行，且均可透過單一 IP 位址來存取。

您也可以：

- [建立具有外部網路連線的基本輸入控制器][aks-ingress-basic]
- [啟用 HTTP 應用程式路由附加元件][aks-http-app-routing]
- [建立使用內部私人網路和 IP 位址的輸入控制器][aks-ingress-internal]
- [建立使用自有 TLS 憑證的輸入控制器][aks-ingress-own-tls]
- [建立輸入控制器，其使用 Let's Encrypt 自動產生具有靜態公用 IP 的 TLS 憑證][aks-ingress-static-tls]

## <a name="before-you-begin"></a>開始之前

此文章假設您目前具有 AKS 叢集。 如果您需要 AKS 叢集，請參閱[使用 Azure CLI][aks-quickstart-cli] 或[使用 Azure 入口網站][aks-quickstart-portal]的 AKS 快速入門。

本文也假設您在與 AKS 叢集相同的資源群組中有一個具有[DNS 區域][dns-zone]的[自訂網域][custom-domain]。

本文使用 [Helm 3][helm] 來安裝 NGINX 輸入控制器和 cert 管理員。 請確定您使用的是最新版本的 Helm，並可存取 *nginx* 和 *jetstack* Helm 存放庫。 如需升級指示，請參閱 [Helm 安裝][helm-install]檔。如需設定和使用 Helm 的詳細資訊，請參閱 [Azure Kubernetes Service (AKS) 中的使用 Helm 安裝應用程式 ][use-helm]。

本文也會要求您執行 Azure CLI 2.0.64 版版或更新版本。 執行 `az --version` 以尋找版本。 如果您需要安裝或升級，請參閱[安裝 Azure CLI][azure-cli-install]。

## <a name="create-an-ingress-controller"></a>建立輸入控制器

若要建立輸入控制器，請使用 `helm` 命令來安裝 *nginx* 輸入。 為了新增備援，您必須使用 `--set controller.replicaCount` 參數部署兩個 NGINX 輸入控制器複本。 為充分享有執行輸入控制器複本的好處，請確定 AKS 叢集中有多個節點。

輸入控制器也需要在 Linux 節點上排程。 Windows Server 節點不應執行輸入控制器。 您可以使用 `--set nodeSelector` 參數來指定節點選取器，以告知 Kubernetes 排程器在 Linux 式節點上執行 NGINX 輸入控制器。

> [!TIP]
> 下列範例會建立名為「輸入 *-基本*」之輸入資源的 Kubernetes 命名空間。 視需要指定您自己環境的命名空間。

> [!TIP]
> 如果您想要針對叢集中的容器要求啟用 [用戶端來源 IP 保留][client-source-ip] ，請新增 `--set controller.service.externalTrafficPolicy=Local` 至 Helm 安裝命令。 用戶端來源 IP 會儲存在要求標頭中，以 *X 轉送-表示*。 使用已啟用用戶端來源 IP 保留的輸入控制器時，TLS 傳遞將無法運作。

```console
# Create a namespace for your ingress resources
kubectl create namespace ingress-basic

# Add the ingress-nginx repository
helm repo add ingress-nginx https://kubernetes.github.io/ingress-nginx

# Use Helm to deploy an NGINX ingress controller
helm install nginx-ingress ingress-nginx/ingress-nginx \
    --namespace ingress-basic \
    --set controller.replicaCount=2 \
    --set controller.nodeSelector."beta\.kubernetes\.io/os"=linux \
    --set defaultBackend.nodeSelector."beta\.kubernetes\.io/os"=linux
```

在安裝期間，會為輸入控制器建立 Azure 公用 IP 位址。 在輸入控制器的生命週期內，此公用 IP 位址都是靜態的。 如果您刪除輸入控制器，公用 IP 位址指派將會遺失。 如果您後續又建立其他輸入控制器，將會指派新的公用 IP 位址。 如果您想要持續使用公用 IP 位址，您可以改為[使用靜態公用 IP 位址建立輸入控制器][aks-ingress-static-tls]。

若要取得公用 IP 位址，請使用 `kubectl get service` 命令。 將 IP 位址指派給服務需要幾分鐘的時間。

```console
$ kubectl --namespace ingress-basic get services -o wide -w nginx-ingress-ingress-nginx-controller

NAME                                     TYPE           CLUSTER-IP    EXTERNAL-IP     PORT(S)                      AGE   SELECTOR
nginx-ingress-ingress-nginx-controller   LoadBalancer   10.0.74.133   EXTERNAL_IP     80:32486/TCP,443:30953/TCP   44s   app.kubernetes.io/component=controller,app.kubernetes.io/instance=nginx-ingress,app.kubernetes.io/name=ingress-nginx
```

尚未建立任何輸入規則。 如果您瀏覽至公用 IP 位址，即會顯示 NGINX 輸入控制器的預設 404 頁面。

## <a name="add-an-a-record-to-your-dns-zone"></a>將 A 記錄新增至您的 DNS 區域

使用 [az network DNS record 記錄][az-network-dns-record-set-a-add-record]，將 *a a a a a a a a a a a a a a a a a a a a a a a a a a a a a a a a* record

```console
az network dns record-set a add-record \
    --resource-group myResourceGroup \
    --zone-name MY_CUSTOM_DOMAIN \
    --record-set-name * \
    --ipv4-address MY_EXTERNAL_IP
```

> [!NOTE]
> （選擇性）您可以設定輸入控制器 IP 位址的 FQDN，而不是自訂網域。 請注意，此範例適用于 Bash shell。
> 
> ```bash
> # Public IP address of your ingress controller
> IP="MY_EXTERNAL_IP"
> 
> # Name to associate with public IP address
> DNSNAME="demo-aks-ingress"
> 
> # Get the resource-id of the public ip
> PUBLICIPID=$(az network public-ip list --query "[?ipAddress!=null]|[?contains(ipAddress, '$IP')].[id]" --output tsv)
> 
> # Update public ip address with DNS name
> az network public-ip update --ids $PUBLICIPID --dns-name $DNSNAME
> 
> # Display the FQDN
> az network public-ip show --ids $PUBLICIPID --query "[dnsSettings.fqdn]" --output tsv
> ```

## <a name="install-cert-manager"></a>安裝 cert-manager

NGINX 輸入控制器支援 TLS 終止。 有數種方式可擷取和設定 HTTPS 的憑證。 本文示範如何使用 [cert-manager][cert-manager]，其會提供自動化的 [Lets Encrypt][lets-encrypt] 憑證產生與管理功能。

安裝 cert manager 控制器：

```console
# Label the ingress-basic namespace to disable resource validation
kubectl label namespace ingress-basic cert-manager.io/disable-validation=true

# Add the Jetstack Helm repository
helm repo add jetstack https://charts.jetstack.io

# Update your local Helm chart repository cache
helm repo update

# Install the cert-manager Helm chart
helm install \
  cert-manager \
  --namespace ingress-basic \
  --version v0.16.1 \
  --set installCRDs=true \
  --set nodeSelector."beta\.kubernetes\.io/os"=linux \
  jetstack/cert-manager
```

如需 cert-manager 設定的詳細資訊，請參閱 [cert-manager 專案][cert-manager]。

## <a name="create-a-ca-cluster-issuer"></a>建立 CA 叢集簽發者

簽發憑證之前，cert-manager 需要 [Issuer][cert-manager-issuer] 或 [ClusterIssuer][cert-manager-cluster-issuer] 資源。 這些 Kubernetes 資源在功能上完全相同，但是 `Issuer` 會在單一命名空間中運作，而 `ClusterIssuer` 會跨所有命名空間運作。 如需詳細資訊，請參閱 [cert-manager issuer][cert-manager-issuer] 文件。

使用下列範例資訊清單來建立叢集簽發者，例如 `cluster-issuer.yaml`。 利用您組織中的有效電子郵件地址來更新電子郵件地址：

```yaml
apiVersion: cert-manager.io/v1alpha2
kind: ClusterIssuer
metadata:
  name: letsencrypt
spec:
  acme:
    server: https://acme-v02.api.letsencrypt.org/directory
    email: MY_EMAIL_ADDRESS
    privateKeySecretRef:
      name: letsencrypt
    solvers:
    - http01:
        ingress:
          class: nginx
          podTemplate:
            spec:
              nodeSelector:
                "kubernetes.io/os": linux
```

若要建立簽發者，請使用 `kubectl apply` 命令。

```console
kubectl apply -f cluster-issuer.yaml
```

## <a name="run-demo-applications"></a>執行示範應用程式

輸入控制器和憑證管理解決方案皆已設定。 現在讓我們在您的 AKS 叢集中執行兩個示範應用程式。 在此範例中，Helm 是用來部署簡單的 *Hello world* 應用程式的兩個實例。

若要查看作用中的輸入控制器，請在您的 AKS 叢集中執行兩個示範應用程式。 在此範例中，您會使用 `kubectl apply` 來部署簡單的 *Hello world* 應用程式的兩個實例。

建立 *aks-helloworld-yaml* 檔案，並在下列範例 yaml 中複製：

```yml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: aks-helloworld-one
spec:
  replicas: 1
  selector:
    matchLabels:
      app: aks-helloworld-one
  template:
    metadata:
      labels:
        app: aks-helloworld-one
    spec:
      containers:
      - name: aks-helloworld-one
        image: mcr.microsoft.com/azuredocs/aks-helloworld:v1
        ports:
        - containerPort: 80
        env:
        - name: TITLE
          value: "Welcome to Azure Kubernetes Service (AKS)"
---
apiVersion: v1
kind: Service
metadata:
  name: aks-helloworld-one
spec:
  type: ClusterIP
  ports:
  - port: 80
  selector:
    app: aks-helloworld-one
```

建立 *aks-helloworld-兩個 yaml* 檔案，並在下列範例 yaml 中複製：

```yml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: aks-helloworld-two
spec:
  replicas: 1
  selector:
    matchLabels:
      app: aks-helloworld-two
  template:
    metadata:
      labels:
        app: aks-helloworld-two
    spec:
      containers:
      - name: aks-helloworld-two
        image: mcr.microsoft.com/azuredocs/aks-helloworld:v1
        ports:
        - containerPort: 80
        env:
        - name: TITLE
          value: "AKS Ingress Demo"
---
apiVersion: v1
kind: Service
metadata:
  name: aks-helloworld-two
spec:
  type: ClusterIP
  ports:
  - port: 80
  selector:
    app: aks-helloworld-two
```

使用下列程式執行兩個示範應用程式 `kubectl apply` ：

```console
kubectl apply -f aks-helloworld-one.yaml --namespace ingress-basic
kubectl apply -f aks-helloworld-two.yaml --namespace ingress-basic
```

## <a name="create-an-ingress-route"></a>建立輸入路由

這兩個應用程式現在都已在您的 Kubernetes 叢集上執行。 不過，它們是以類型的服務所設定 `ClusterIP` ，且無法從網際網路存取。 若要使其可公開使用，請建立 Kubernetes 輸入資源。 輸入資源會設定將流量路由至這兩個應用程式之一的規則。

在下列範例中，位址為 hello world 輸入的流量 *。MY_CUSTOM_DOMAIN* 會路由傳送至 *aks helloworld* 服務。 位址為 hello world 輸入的流量 *。MY_CUSTOM_DOMAIN/hello-world-two* 會路由傳送至 *aks-helloworld-兩個* 服務。 對 *hello-world 輸入的流量。MY_CUSTOM_DOMAIN/靜態* 會路由至靜態資產的 *aks-helloworld* 服務。

> [!NOTE]
> 如果您為輸入控制器 IP 位址設定 FQDN，而不是自訂網域，請使用 FQDN，而不是 *hello world 輸入。MY_CUSTOM_DOMAIN*。 例如，如果您的 FQDN 是 *demo-aks-ingress.eastus.cloudapp.azure.com*，請取代 *hello world 輸入。* 使用中的 *demo-aks-ingress.eastus.cloudapp.azure.com* MY_CUSTOM_DOMAIN `hello-world-ingress.yaml` 。

`hello-world-ingress.yaml`使用下面的範例 YAML 來建立名為的檔案。 將 *hosts* 和 *host* 更新為您在上一個步驟中建立的 DNS 名稱。

```yaml
apiVersion: networking.k8s.io/v1beta1
kind: Ingress
metadata:
  name: hello-world-ingress
  annotations:
    kubernetes.io/ingress.class: nginx
    nginx.ingress.kubernetes.io/rewrite-target: /$1
    nginx.ingress.kubernetes.io/use-regex: "true"
    cert-manager.io/cluster-issuer: letsencrypt
spec:
  tls:
  - hosts:
    - hello-world-ingress.MY_CUSTOM_DOMAIN
    secretName: tls-secret
  rules:
  - host: hello-world-ingress.MY_CUSTOM_DOMAIN
    http:
      paths:
      - backend:
          serviceName: aks-helloworld-one
          servicePort: 80
        path: /hello-world-one(/|$)(.*)
      - backend:
          serviceName: aks-helloworld-two
          servicePort: 80
        path: /hello-world-two(/|$)(.*)
      - backend:
          serviceName: aks-helloworld-one
          servicePort: 80
        path: /(.*)
---
apiVersion: networking.k8s.io/v1beta1
kind: Ingress
metadata:
  name: hello-world-ingress-static
  annotations:
    kubernetes.io/ingress.class: nginx
    nginx.ingress.kubernetes.io/rewrite-target: /static/$2
    nginx.ingress.kubernetes.io/use-regex: "true"
    cert-manager.io/cluster-issuer: letsencrypt
spec:
  tls:
  - hosts:
    - hello-world-ingress.MY_CUSTOM_DOMAIN
    secretName: tls-secret
  rules:
  - host: hello-world-ingress.MY_CUSTOM_DOMAIN
    http:
      paths:
      - backend:
          serviceName: aks-helloworld-one
          servicePort: 80
        path: /static(/|$)(.*)
```

使用 `kubectl apply` 命令建立輸入資源。

```console
kubectl apply -f hello-world-ingress.yaml --namespace ingress-basic
```

## <a name="verify-a-certificate-object-has-been-created"></a>確認已建立憑證物件

接下來，必須建立憑證資源。 憑證資源會定義所需的 X.509 憑證。 如需詳細資訊，請參閱 [cert manager 憑證][cert-manager-certificates]。 Cert 管理員已使用輸入填充碼自動為您建立憑證物件，該填充碼會自動與 cert 管理員一起部署，因為 v 0.2.2。 如需詳細資訊請，請參閱 [ingress-shim 文件][ingress-shim] \(英文\)。

若要確認已成功建立憑證，請使用 `kubectl get certificate --namespace ingress-basic` 命令並確認 [ *就緒* ] 為 *True*，這可能需要幾分鐘的時間。

```console
$ kubectl get certificate --namespace ingress-basic

NAME         READY   SECRET       AGE
tls-secret   True    tls-secret   11m
```

## <a name="test-the-ingress-configuration"></a>測試輸入組態

開啟網頁瀏覽器，以 *hello world 輸入。* Kubernetes 輸入控制器的 MY_CUSTOM_DOMAIN。 請注意，您會重新導向至使用 HTTPS，而且憑證是受信任的，且示範應用程式會顯示在網頁瀏覽器中。 新增 */hello-world-two* 路徑，並注意會顯示具有自訂標題的第二個示範應用程式。

## <a name="clean-up-resources"></a>清除資源

本文使用 Helm 來安裝輸入元件、憑證及範例應用程式。 部署 Helm 圖表時會建立一些 Kubernetes 資源。 這些資源包含 Pod、部署和服務。 若要清除這些資源，您可以刪除整個範例命名空間或個別資源。

### <a name="delete-the-sample-namespace-and-all-resources"></a>刪除範例命名空間和所有資源

若要刪除整個範例命名空間，請使用 `kubectl delete` 命令並指定您的命名空間名稱。 命名空間中的所有資源都會刪除。

```console
kubectl delete namespace ingress-basic
```

### <a name="delete-resources-individually"></a>個別刪除資源

或者，更細微的方法是刪除所建立的個別資源。 首先，移除叢集簽發者資源：

```console
kubectl delete -f cluster-issuer.yaml --namespace ingress-basic
```

使用命令列出 Helm 版本 `helm list` 。 尋找名為 *nginx* 和 *cert manager* 的圖表，如下列範例輸出所示：

```console
$ helm list --namespace ingress-basic

NAME                    NAMESPACE       REVISION        UPDATED                                 STATUS          CHART                   APP VERSION
cert-manager            ingress-basic   1               2020-01-15 10:23:36.515514 -0600 CST    deployed        cert-manager-v0.13.0    v0.13.0    
nginx                   ingress-basic   1               2020-01-15 10:09:45.982693 -0600 CST    deployed        nginx-ingress-1.29.1    0.27.0  
```

使用命令卸載版本 `helm uninstall` 。 下列範例會卸載 NGINX 輸入和 cert 管理員部署。

```console
$ helm uninstall cert-manager nginx --namespace ingress-basic

release "cert-manager" uninstalled
release "nginx" uninstalled
```

接下來，移除兩個範例應用程式：

```console
kubectl delete -f aks-helloworld-one.yaml --namespace ingress-basic
kubectl delete -f aks-helloworld-two.yaml --namespace ingress-basic
```

移除將流量導向範例應用程式的輸入路由：

```console
kubectl delete -f hello-world-ingress.yaml --namespace ingress-basic
```

最後，您可以刪除本身的命名空間。 使用 `kubectl delete` 命令並指定您的命名空間名稱：

```console
kubectl delete namespace ingress-basic
```

## <a name="next-steps"></a>後續步驟

本文包含 AKS 的一些外部元件。 若要深入了解這些元件，請參閱下列專案頁面：

- [Helm CLI][helm-cli]
- [NGINX 輸入控制器][nginx-ingress]
- [cert-manager][cert-manager]

您也可以：

- [建立具有外部網路連線的基本輸入控制器][aks-ingress-basic]
- [啟用 HTTP 應用程式路由附加元件][aks-http-app-routing]
- [建立使用內部私人網路和 IP 位址的輸入控制器][aks-ingress-internal]
- [建立使用自有 TLS 憑證的輸入控制器][aks-ingress-own-tls]
- [建立輸入控制器，其使用 Let's Encrypt 自動產生具有靜態公用 IP 的 TLS 憑證][aks-ingress-static-tls]

<!-- LINKS - external -->
[az-network-dns-record-set-a-add-record]: /cli/azure/network/dns/record-set/#az-network-dns-record-set-a-add-record
[custom-domain]: ../app-service/manage-custom-dns-buy-domain.md#buy-an-app-service-domain
[dns-zone]: ../dns/dns-getstarted-cli.md
[helm]: https://helm.sh/
[helm-cli]: ./kubernetes-helm.md
[cert-manager]: https://github.com/jetstack/cert-manager
[cert-manager-certificates]: https://cert-manager.readthedocs.io/en/latest/reference/certificates.html
[ingress-shim]: https://docs.cert-manager.io/en/latest/tasks/issuing-certificates/ingress-shim.html
[cert-manager-cluster-issuer]: https://cert-manager.readthedocs.io/en/latest/reference/clusterissuers.html
[cert-manager-issuer]: https://cert-manager.readthedocs.io/en/latest/reference/issuers.html
[lets-encrypt]: https://letsencrypt.org/
[nginx-ingress]: https://github.com/kubernetes/ingress-nginx
[helm-install]: https://docs.helm.sh/using_helm/#installing-helm

<!-- LINKS - internal -->
[use-helm]: kubernetes-helm.md
[azure-cli-install]: /cli/azure/install-azure-cli
[az-aks-show]: /cli/azure/aks#az-aks-show
[az-network-public-ip-create]: /cli/azure/network/public-ip#az-network-public-ip-create
[aks-ingress-internal]: ingress-internal-ip.md
[aks-ingress-static-tls]: ingress-static-ip.md
[aks-ingress-basic]: ingress-basic.md
[aks-http-app-routing]: http-application-routing.md
[aks-ingress-own-tls]: ingress-own-tls.md
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[client-source-ip]: concepts-network.md#ingress-controllers
[install-azure-cli]: /cli/azure/install-azure-cli
