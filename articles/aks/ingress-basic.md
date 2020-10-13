---
title: 建立輸入控制器
titleSuffix: Azure Kubernetes Service
description: 了解如何在 Azure Kubernetes Service (AKS) 叢集中安裝及設定基本 NGINX 輸入控制器。
services: container-service
ms.topic: article
ms.date: 08/17/2020
ms.openlocfilehash: 9ab177e2756227f3893d13c97d12ad67cfb1ff62
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "88855826"
---
# <a name="create-an-ingress-controller-in-azure-kubernetes-service-aks"></a>在 Azure Kubernetes Service (AKS) 中建立輸入控制器

輸入控制器是一項可為 Kubernetes 服務提供反向 Proxy、可設定的流量路由和 TLS 終止的軟體。 Kubernetes 輸入資源可用來設定個別 Kubernetes 服務的輸入規則和路由。 透過輸入控制器和輸入規則，您可以使用單一 IP 位址將流量路由至 Kubernetes 叢集中的多個服務。

本文示範如何在 Azure Kubernetes Service (AKS) 叢集中部署 [NGINX 輸入控制器][nginx-ingress]。 然後，會有兩個應用程式在 AKS 叢集中執行，且均可透過單一 IP 位址來存取。

您也可以：

- [啟用 HTTP 應用程式路由附加元件][aks-http-app-routing]
- [建立使用內部私人網路和 IP 位址的輸入控制器][aks-ingress-internal]
- [建立使用自有 TLS 憑證的輸入控制器][aks-ingress-own-tls]
- 建立輸入控制器，其使用 Let's Encrypt 自動產生[具有動態公用 IP][aks-ingress-tls] 或[具有靜態公用 IP 位址][aks-ingress-static-tls]的 TLS 憑證

## <a name="before-you-begin"></a>開始之前

本文使用 [Helm 3][helm] 來安裝 NGINX 輸入控制器。 請確定您使用的是最新版本的 Helm，並可存取 *nginx* Helm 存放庫。

本文也會要求您執行 Azure CLI 2.0.64 版版或更新版本。 執行 `az --version` 以尋找版本。 如果您需要安裝或升級，請參閱[安裝 Azure CLI][azure-cli-install]。

## <a name="create-an-ingress-controller"></a>建立輸入控制器

若要建立輸入控制器，請使用 Helm 來安裝 *nginx*輸入。 為了新增備援，您必須使用 `--set controller.replicaCount` 參數部署兩個 NGINX 輸入控制器複本。 為充分享有執行輸入控制器複本的好處，請確定 AKS 叢集中有多個節點。

輸入控制器也需要在 Linux 節點上排程。 Windows Server 節點不應執行輸入控制器。 您可以使用 `--set nodeSelector` 參數來指定節點選取器，以告知 Kubernetes 排程器在 Linux 式節點上執行 NGINX 輸入控制器。

> [!TIP]
> 下列範例會建立名為「輸入 *-基本*」之輸入資源的 Kubernetes 命名空間。 視需要指定您自己環境的命名空間。

> [!TIP]
> 如果您想要針對叢集中的容器要求啟用 [用戶端來源 IP 保留][client-source-ip] ，請新增 `--set controller.service.externalTrafficPolicy=Local` 至 Helm 安裝命令。 用戶端來源 IP 會儲存在要求標頭中，以 *X 轉送-表示*。 使用輸入控制器並啟用用戶端來源 IP 保留時，SSL 傳遞將無法運作。

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

為 NGINX 輸入控制器建立 Kubernetes 負載平衡器服務時，系統會指派動態公用 IP 位址，如下列範例輸出所示：

```
$ kubectl --namespace ingress-basic get services -o wide -w nginx-ingress-ingress-nginx-controller

NAME                                     TYPE           CLUSTER-IP    EXTERNAL-IP     PORT(S)                      AGE   SELECTOR
nginx-ingress-ingress-nginx-controller   LoadBalancer   10.0.74.133   EXTERNAL_IP     80:32486/TCP,443:30953/TCP   44s   app.kubernetes.io/component=controller,app.kubernetes.io/instance=nginx-ingress,app.kubernetes.io/name=ingress-nginx
```

尚未建立任何輸入規則，因此，如果您瀏覽至內部 IP 位址，將會顯示 NGINX 輸入控制器的預設 404 頁面。 輸入規則會於下列步驟中進行設定。

## <a name="run-demo-applications"></a>執行示範應用程式

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
        image: neilpeterson/aks-helloworld:v1
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
        image: neilpeterson/aks-helloworld:v1
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

這兩個應用程式現在都已在您的 Kubernetes 叢集上執行。 若要將流量路由至每個應用程式，請建立 Kubernetes 輸入資源。 輸入資源會設定將流量路由至這兩個應用程式之一的規則。

在下列範例中， *EXTERNAL_IP* 的流量會路由傳送至名為的服務 `aks-helloworld-one` 。 流向 *EXTERNAL_IP/hello-world-two* 的流量會路由傳送至 `aks-helloworld-two` 服務。 流向 *EXTERNAL_IP/靜態* 的流量會路由傳送至名 `aks-helloworld-one` 為「靜態資產」的服務。

建立名為 *yaml* 的檔案，並在下列範例 yaml 中複製。

```yaml
apiVersion: networking.k8s.io/v1beta1
kind: Ingress
metadata:
  name: hello-world-ingress
  namespace: ingress-basic
  annotations:
    kubernetes.io/ingress.class: nginx
    nginx.ingress.kubernetes.io/ssl-redirect: "false"
    nginx.ingress.kubernetes.io/use-regex: "true"
    nginx.ingress.kubernetes.io/rewrite-target: /$1
spec:
  rules:
  - http:
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
  namespace: ingress-basic
  annotations:
    kubernetes.io/ingress.class: nginx
    nginx.ingress.kubernetes.io/ssl-redirect: "false"
    nginx.ingress.kubernetes.io/rewrite-target: /static/$2
spec:
  rules:
  - http:
      paths:
      - backend:
          serviceName: aks-helloworld-one
          servicePort: 80
        path: /static(/|$)(.*)
```

使用 `kubectl apply -f hello-world-ingress.yaml` 命令建立輸入資源。

```
$ kubectl apply -f hello-world-ingress.yaml

ingress.extensions/hello-world-ingress created
ingress.extensions/hello-world-ingress-static created
```

## <a name="test-the-ingress-controller"></a>測試輸入控制器

若要測試輸入控制器的路由，請瀏覽至這兩個應用程式。 將網頁瀏覽器開啟至 NGINX 輸入控制器的 IP 位址，例如 *EXTERNAL_IP*。 第一個示範應用程式會顯示在網頁瀏覽器中，如下列範例所示：

![執行於輸入控制器後方的第一個應用程式](media/ingress-basic/app-one.png)

現在將 */hello-world-two* 路徑新增至 IP 位址，例如 *EXTERNAL_IP/hello-world-two*。 此時會顯示具有自訂標題的第二個示範應用程式：

![執行於輸入控制器後方的第二個應用程式](media/ingress-basic/app-two.png)

## <a name="clean-up-resources"></a>清除資源

本文使用 Helm 來安裝輸入元件和範例應用程式。 部署 Helm 圖表時會建立一些 Kubernetes 資源。 這些資源包含 Pod、部署和服務。 若要清除這些資源，您可以刪除整個範例命名空間或個別資源。

### <a name="delete-the-sample-namespace-and-all-resources"></a>刪除範例命名空間和所有資源

若要刪除整個範例命名空間，請使用 `kubectl delete` 命令並指定您的命名空間名稱。 命名空間中的所有資源都會刪除。

```console
kubectl delete namespace ingress-basic
```

### <a name="delete-resources-individually"></a>個別刪除資源

或者，更細微的方法是刪除所建立的個別資源。 使用命令列出 Helm 版本 `helm list` 。 尋找名為nginx-ingress** 和 aks-helloworld** 的圖表，如下列範例輸出所示：

```
$ helm list --namespace ingress-basic

NAME                    NAMESPACE       REVISION        UPDATED                                 STATUS          CHART                   APP VERSION
nginx-ingress           ingress-basic   1               2020-01-06 19:55:46.358275 -0600 CST    deployed        nginx-ingress-1.27.1    0.26.1  
```

使用命令卸載版本 `helm uninstall` 。 下列範例會卸載 NGINX 輸入部署。

```
$ helm uninstall nginx-ingress --namespace ingress-basic

release "nginx-ingress" uninstalled
```

接下來，移除兩個範例應用程式：

```console
kubectl delete -f aks-helloworld-one.yaml --namespace ingress-basic
kubectl delete -f aks-helloworld-two.yaml --namespace ingress-basic
```

移除將流量導向範例應用程式的輸入路由：

```console
kubectl delete -f hello-world-ingress.yaml
```

最後，您可以刪除本身的命名空間。 使用 `kubectl delete` 命令並指定您的命名空間名稱：

```console
kubectl delete namespace ingress-basic
```

## <a name="next-steps"></a>接下來的步驟

本文包含 AKS 的一些外部元件。 若要深入了解這些元件，請參閱下列專案頁面：

- [Helm CLI][helm-cli]
- [NGINX 輸入控制器][nginx-ingress]

您也可以：

- [啟用 HTTP 應用程式路由附加元件][aks-http-app-routing]
- [建立使用內部私人網路和 IP 位址的輸入控制器][aks-ingress-internal]
- [建立使用自有 TLS 憑證的輸入控制器][aks-ingress-own-tls]
- 建立輸入控制器，其使用 Let's Encrypt 自動產生[具有動態公用 IP][aks-ingress-tls] 或[具有靜態公用 IP 位址][aks-ingress-static-tls]的 TLS 憑證

<!-- LINKS - external -->
[helm]: https://helm.sh/
[helm-cli]: ./kubernetes-helm.md
[nginx-ingress]: https://github.com/kubernetes/ingress-nginx

<!-- LINKS - internal -->
[use-helm]: kubernetes-helm.md
[azure-cli-install]: /cli/azure/install-azure-cli
[aks-ingress-internal]: ingress-internal-ip.md
[aks-ingress-tls]: ingress-tls.md
[aks-ingress-static-tls]: ingress-static-ip.md
[aks-http-app-routing]: http-application-routing.md
[aks-ingress-own-tls]: ingress-own-tls.md
[client-source-ip]: concepts-network.md#ingress-controllers
