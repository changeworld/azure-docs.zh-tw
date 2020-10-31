---
title: 使用 TLS 憑證進行輸入
titleSuffix: Azure Kubernetes Service
description: 了解如何安裝及設定 NGINX 輸入控制器，而該控制器在 Azure Kubernetes Service (AKS) 叢集中使用您自有的憑證。
services: container-service
ms.topic: article
ms.date: 08/17/2020
ms.openlocfilehash: f8ea245444fa5e8e042644bd3f7a34ed021ccd1d
ms.sourcegitcommit: 857859267e0820d0c555f5438dc415fc861d9a6b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/30/2020
ms.locfileid: "93131032"
---
# <a name="create-an-https-ingress-controller-and-use-your-own-tls-certificates-on-azure-kubernetes-service-aks"></a>在 Azure Kubernetes Service (AKS) 上建立 HTTPS 輸入控制器及使用自有的 TLS 憑證

輸入控制器是一項可為 Kubernetes 服務提供反向 Proxy、可設定的流量路由和 TLS 終止的軟體。 Kubernetes 輸入資源可用來設定個別 Kubernetes 服務的輸入規則和路由。 透過輸入控制器和輸入規則，您可以使用單一 IP 位址將流量路由至 Kubernetes 叢集中的多個服務。

本文示範如何在 Azure Kubernetes Service (AKS) 叢集中部署 [NGINX 輸入控制器][nginx-ingress]。 您會產生自己的憑證，並建立 Kubernetes 祕密以便用於輸入路由。 最後，會有兩個應用程式在 AKS 叢集中執行，且均可透過單一 IP 位址來存取。

您也可以：

- [建立具有外部網路連線的基本輸入控制器][aks-ingress-basic]
- [啟用 HTTP 應用程式路由附加元件][aks-http-app-routing]
- [建立使用內部私人網路和 IP 位址的輸入控制器][aks-ingress-internal]
- 建立輸入控制器，其使用 Let's Encrypt 自動產生[具有動態公用 IP][aks-ingress-tls] 或[具有靜態公用 IP 位址][aks-ingress-static-tls]的 TLS 憑證

## <a name="before-you-begin"></a>開始之前

本文使用 [Helm 3][helm] 來安裝 NGINX 輸入控制器。 請確定您使用的是最新版本的 Helm，並可存取 *nginx* Helm 存放庫。 如需升級指示，請參閱 [Helm 安裝][helm-install]檔。如需設定和使用 Helm 的詳細資訊，請參閱 [Azure Kubernetes Service (AKS) 中的使用 Helm 安裝應用程式 ][use-helm]。

本文也會要求您執行 Azure CLI 2.0.64 版版或更新版本。 執行 `az --version` 以尋找版本。 如果您需要安裝或升級，請參閱[安裝 Azure CLI][azure-cli-install]。

## <a name="create-an-ingress-controller"></a>建立輸入控制器

若要建立輸入控制器，請使用 `Helm` 以安裝 *nginx-ingress* 。 為了新增備援，您必須使用 `--set controller.replicaCount` 參數部署兩個 NGINX 輸入控制器複本。 為充分享有執行輸入控制器複本的好處，請確定 AKS 叢集中有多個節點。

輸入控制器也需要在 Linux 節點上排程。 Windows Server 節點不應執行輸入控制器。 您可以使用 `--set nodeSelector` 參數來指定節點選取器，以告知 Kubernetes 排程器在 Linux 式節點上執行 NGINX 輸入控制器。

> [!TIP]
> 下列範例會建立名為「輸入 *-基本* 」之輸入資源的 Kubernetes 命名空間。 視需要指定您自己環境的命名空間。 如果您的 AKS 叢集未啟用 RBAC，請新增 `--set rbac.create=false` 至 Helm 命令。

> [!TIP]
> 如果您想要針對叢集中的容器要求啟用 [用戶端來源 IP 保留][client-source-ip] ，請新增 `--set controller.service.externalTrafficPolicy=Local` 至 Helm 安裝命令。 用戶端來源 IP 會儲存在要求標頭中，以 *X 轉送-表示* 。 使用已啟用用戶端來源 IP 保留的輸入控制器時，TLS 傳遞將無法運作。

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

若要取得公用 IP 位址，請使用 `kubectl get service` 命令。

```console
kubectl --namespace ingress-basic get services -o wide -w nginx-ingress-ingress-nginx-controller
```

將 IP 位址指派給服務需要幾分鐘的時間。

```
$ kubectl --namespace ingress-basic get services -o wide -w nginx-ingress-ingress-nginx-controller

NAME                                     TYPE           CLUSTER-IP    EXTERNAL-IP     PORT(S)                      AGE   SELECTOR
nginx-ingress-ingress-nginx-controller   LoadBalancer   10.0.74.133   EXTERNAL_IP     80:32486/TCP,443:30953/TCP   44s   app.kubernetes.io/component=controller,app.kubernetes.io/instance=nginx-ingress,app.kubernetes.io/name=ingress-nginx
```

請記下此公用 IP 位址，因為在最後一個步驟中該位址用來測試部署。

尚未建立任何輸入規則。 如果您瀏覽至公用 IP 位址，即會顯示 NGINX 輸入控制器的預設 404 頁面。

## <a name="generate-tls-certificates"></a>產生 TLS 憑證

在本文中，使用 `openssl` 產生自我簽署的憑證。 針對生產用途，您應該透過提供者或自己的憑證授權單位 (CA) 來要求受信任、已簽署的憑證。 在下一個步驟中，您會使用 OpenSSL 所產生的 TLS 憑證和私密金鑰來產生 Kubernetes「祕密」  。

下列範例會產生名為 aks-ingress-tls.crt  的 2048 位元 RSA X509 憑證，有效期為 365 天。 此私密金鑰檔案名為 aks-ingress-tls.key  。 Kubernetes TLS 祕密需要這兩個檔案。

這篇文章使用 demo.azure.com  主體一般名稱，而且不需要變更。 針對生產用途，指定自己組織的 `-subj` 參數值：

```console
openssl req -x509 -nodes -days 365 -newkey rsa:2048 \
    -out aks-ingress-tls.crt \
    -keyout aks-ingress-tls.key \
    -subj "/CN=demo.azure.com/O=aks-ingress-tls"
```

## <a name="create-kubernetes-secret-for-the-tls-certificate"></a>為 TLS 憑證建立 Kubernetes 祕密

若要允許 Kubernetes 使用輸入控制器的 TLS 憑證和私密金鑰，您可建立及使用祕密。 定義祕密一次，並使用在上一個步驟中建立的憑證和金鑰檔案。 您會接著在定義輸入路由時參考此祕密。

下列範例會建立祕密名稱 aks-ingress-tls  ：

```console
kubectl create secret tls aks-ingress-tls \
    --namespace ingress-basic \
    --key aks-ingress-tls.key \
    --cert aks-ingress-tls.crt
```

## <a name="run-demo-applications"></a>執行示範應用程式

輸入控制器和您憑證的祕密皆已設定。 現在讓我們在您的 AKS 叢集中執行兩個示範應用程式。 在此範例中，會使用 Helm 來部署簡單 'Hello world' 應用程式的兩個執行個體。

若要查看作用中的輸入控制器，請在您的 AKS 叢集中執行兩個示範應用程式。 在此範例中，您會使用 `kubectl apply` 來部署簡單的 *Hello world* 應用程式的兩個實例。

建立 *aks-helloworld yaml* 檔案，並複製下列範例 yaml：

```yml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: aks-helloworld
spec:
  replicas: 1
  selector:
    matchLabels:
      app: aks-helloworld
  template:
    metadata:
      labels:
        app: aks-helloworld
    spec:
      containers:
      - name: aks-helloworld
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
  name: aks-helloworld
spec:
  type: ClusterIP
  ports:
  - port: 80
  selector:
    app: aks-helloworld
```

建立 *yaml* 檔案，並在下列範例 yaml 中複製：

```yml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: ingress-demo
spec:
  replicas: 1
  selector:
    matchLabels:
      app: ingress-demo
  template:
    metadata:
      labels:
        app: ingress-demo
    spec:
      containers:
      - name: ingress-demo
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
  name: ingress-demo
spec:
  type: ClusterIP
  ports:
  - port: 80
  selector:
    app: ingress-demo
```

使用下列程式執行兩個示範應用程式 `kubectl apply` ：

```console
kubectl apply -f aks-helloworld.yaml --namespace ingress-basic
kubectl apply -f ingress-demo.yaml --namespace ingress-basic
```

## <a name="create-an-ingress-route"></a>建立輸入路由

這兩個應用程式現在都已在您的 Kubernetes 叢集上執行，不過，它們是以 `ClusterIP` 類型的服務設定的。 因此，這些應用程式無法從網際網路存取。 若要使其可公開使用，請建立 Kubernetes 輸入資源。 輸入資源會設定將流量路由至這兩個應用程式之一的規則。

在下列範例中，傳至位址 `https://demo.azure.com/` 的流量會路由傳送至名為 `aks-helloworld` 的服務。 傳至位址 `https://demo.azure.com/hello-world-two` 的流量會路由至 `ingress-demo` 服務。 在本文中，您不需要變更這些示範主機名稱。 針對生產用途，提供在憑證要求和產生過程中指定的名稱。

> [!TIP]
> 如果在憑證要求程式期間指定的主機名稱（CN 名稱）與輸入路由中所定義的主機不相符，則輸入控制器會顯示 *Kubernetes 輸入控制器假憑證* 警告。 請確定您的憑證與輸入路由主機名稱相符。

tls  區段會告知輸入路由對主機 demo.azure.com  使用名為 aks-ingress-tls  的祕密。 同樣地，針對生產用途，指定您自己的主機位址。

建立名為 `hello-world-ingress.yaml` 的檔案，並複製到下列範例 YAML 中。

```yaml
apiVersion: networking.k8s.io/v1beta1
kind: Ingress
metadata:
  name: hello-world-ingress
  namespace: ingress-basic
  annotations:
    kubernetes.io/ingress.class: nginx
    nginx.ingress.kubernetes.io/use-regex: "true"
    nginx.ingress.kubernetes.io/rewrite-target: /$1
spec:
  tls:
  - hosts:
    - demo.azure.com
    secretName: aks-ingress-tls
  rules:
  - host: demo.azure.com
    http:
      paths:
      - backend:
          serviceName: aks-helloworld
          servicePort: 80
        path: /hello-world-one(/|$)(.*)
      - backend:
          serviceName: ingress-demo
          servicePort: 80
        path: /hello-world-two(/|$)(.*)
      - backend:
          serviceName: aks-helloworld
          servicePort: 80
        path: /(.*)
```

使用 `kubectl apply -f hello-world-ingress.yaml` 命令建立輸入資源。

```console
kubectl apply -f hello-world-ingress.yaml
```

範例輸出會顯示已建立輸入資源。

```
$ kubectl apply -f hello-world-ingress.yaml

ingress.extensions/hello-world-ingress created
```

## <a name="test-the-ingress-configuration"></a>測試輸入組態

若要使用假的 demo.azure.com  主機測試憑證，請使用 `curl` 並指定 --resolve  參數。 此參數可讓您將 demo.azure.com  名稱對應到輸入控制器的公用 IP 位址。 指定您自有輸入控制器的公用 IP 位址，如下列範例所示：

```
curl -v -k --resolve demo.azure.com:443:EXTERNAL_IP https://demo.azure.com
```

位址未提供任何額外的路徑，因此輸入控制器會預設為 */* 路由。 此時會傳回第一個示範應用程式，如下列簡要範例輸出所示：

```
$ curl -v -k --resolve demo.azure.com:443:EXTERNAL_IP https://demo.azure.com

[...]
<!DOCTYPE html>
<html xmlns="http://www.w3.org/1999/xhtml">
<head>
    <link rel="stylesheet" type="text/css" href="/static/default.css">
    <title>Welcome to Azure Kubernetes Service (AKS)</title>
[...]
```

`curl` 命令中的 -v  參數會輸出詳細資訊，包括所收到的 TLS 憑證。 在 curl 輸出的中途，您可驗證是否已使用自有的 TLS 憑證。 即使我們使用自我簽署的憑證，-k  參數會繼續載入頁面。 下列範例顯示已使用 issuer: CN=demo.azure.com; O=aks-ingress-tls  憑證：

```
[...]
* Server certificate:
*  subject: CN=demo.azure.com; O=aks-ingress-tls
*  start date: Oct 22 22:13:54 2018 GMT
*  expire date: Oct 22 22:13:54 2019 GMT
*  issuer: CN=demo.azure.com; O=aks-ingress-tls
*  SSL certificate verify result: self signed certificate (18), continuing anyway.
[...]
```

現在，將 */hello-world-two* 路徑新增至位址，例如 `https://demo.azure.com/hello-world-two`。 此時會傳回含有自訂標題的第二個示範應用程式，如下列簡要範例輸出所示：

```
$ curl -v -k --resolve demo.azure.com:443:EXTERNAL_IP https://demo.azure.com/hello-world-two

[...]
<!DOCTYPE html>
<html xmlns="http://www.w3.org/1999/xhtml">
<head>
    <link rel="stylesheet" type="text/css" href="/static/default.css">
    <title>AKS Ingress Demo</title>
[...]
```

## <a name="clean-up-resources"></a>清除資源

本文使用 Helm 來安裝輸入元件和範例應用程式。 部署 Helm 圖表時會建立一些 Kubernetes 資源。 這些資源包含 Pod、部署和服務。 若要清除這些資源，您可以刪除整個範例命名空間或個別資源。

### <a name="delete-the-sample-namespace-and-all-resources"></a>刪除範例命名空間和所有資源

若要刪除整個範例命名空間，請使用 `kubectl delete` 命令並指定您的命名空間名稱。 命名空間中的所有資源都會刪除。

```console
kubectl delete namespace ingress-basic
```

### <a name="delete-resources-individually"></a>個別刪除資源

或者，更細微的方法是刪除所建立的個別資源。 使用命令列出 Helm 版本 `helm list` 。 

```console
helm list --namespace ingress-basic
```

尋找名為 *nginx* 的圖表，如下列範例輸出所示：

```
$ helm list --namespace ingress-basic

NAME                    NAMESPACE       REVISION        UPDATED                                 STATUS          CHART                   APP VERSION
nginx-ingress           ingress-basic   1               2020-01-06 19:55:46.358275 -0600 CST    deployed        nginx-ingress-1.27.1    0.26.1 
```

使用命令卸載版本 `helm uninstall` 。 

```console
helm uninstall nginx-ingress --namespace ingress-basic
```

下列範例會卸載 NGINX 輸入部署。

```
$ helm uninstall nginx-ingress --namespace ingress-basic

release "nginx-ingress" uninstalled
```

接下來，移除兩個範例應用程式：

```console
kubectl delete -f aks-helloworld.yaml --namespace ingress-basic
kubectl delete -f ingress-demo.yaml --namespace ingress-basic
```

移除將流量導向範例應用程式的輸入路由：

```console
kubectl delete -f hello-world-ingress.yaml
```

刪除憑證秘密：

```console
kubectl delete secret aks-ingress-tls
```

最後，您可以刪除本身的命名空間。 使用 `kubectl delete` 命令並指定您的命名空間名稱：

```console
kubectl delete namespace ingress-basic
```

## <a name="next-steps"></a>後續步驟

本文包含 AKS 的一些外部元件。 若要深入了解這些元件，請參閱下列專案頁面：

- [Helm CLI][helm-cli]
- [NGINX 輸入控制器][nginx-ingress]

您也可以：

- [建立具有外部網路連線的基本輸入控制器][aks-ingress-basic]
- [啟用 HTTP 應用程式路由附加元件][aks-http-app-routing]
- [建立使用內部私人網路和 IP 位址的輸入控制器][aks-ingress-internal]
- 建立輸入控制器，其使用 Let's Encrypt 自動產生[具有動態公用 IP][aks-ingress-tls] 或[具有靜態公用 IP 位址][aks-ingress-static-tls]的 TLS 憑證

<!-- LINKS - external -->
[helm-cli]: ./kubernetes-helm.md
[nginx-ingress]: https://github.com/kubernetes/ingress-nginx
[helm]: https://helm.sh/
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
[aks-ingress-tls]: ingress-tls.md
[client-source-ip]: concepts-network.md#ingress-controllers
