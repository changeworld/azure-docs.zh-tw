---
title: 內部網路上的輸入控制器
titleSuffix: Azure Kubernetes Service
description: 了解如何在 Azure Kubernetes Service (AKS) 叢集中安裝及設定內部私人網路的 NGINX 輸入控制器。
services: container-service
ms.topic: article
ms.date: 08/17/2020
ms.openlocfilehash: 976c5581ad217064da37b0b092d2d634d30cb7e5
ms.sourcegitcommit: 78ecfbc831405e8d0f932c9aafcdf59589f81978
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/23/2021
ms.locfileid: "98729156"
---
# <a name="create-an-ingress-controller-to-an-internal-virtual-network-in-azure-kubernetes-service-aks"></a>在 Azure Kubernetes Service (AKS) 中建立內部虛擬網路的輸入控制器

輸入控制器是一項可為 Kubernetes 服務提供反向 Proxy、可設定的流量路由和 TLS 終止的軟體。 Kubernetes 輸入資源可用來設定個別 Kubernetes 服務的輸入規則和路由。 透過輸入控制器和輸入規則，您可以使用單一 IP 位址將流量路由至 Kubernetes 叢集中的多個服務。

本文示範如何在 Azure Kubernetes Service (AKS) 叢集中部署 [NGINX 輸入控制器][nginx-ingress]。 輸入控制器設定於內部私人虛擬網路和 IP 位址上。 不允許外部存取。 然後，會有兩個應用程式在 AKS 叢集中執行，且均可透過單一 IP 位址來存取。

您也可以：

- [建立具有外部網路連線的基本輸入控制器][aks-ingress-basic]
- [啟用 HTTP 應用程式路由附加元件][aks-http-app-routing]
- [建立使用自有 TLS 憑證的輸入控制器][aks-ingress-own-tls]
- 建立輸入控制器，其使用 Let's Encrypt 自動產生[具有動態公用 IP][aks-ingress-tls] 或[具有靜態公用 IP 位址][aks-ingress-static-tls]的 TLS 憑證

## <a name="before-you-begin"></a>開始之前

本文使用 [Helm 3][helm] 來安裝 NGINX 輸入控制器。 請確定您使用的是最新版本的 Helm，並可存取 *nginx* Helm 存放庫。 如需設定及使用 Helm 的詳細資訊，請參閱[在 Azure Kubernetes Service (AKS) 中使用 Helm 安裝應用程式][use-helm]。

本文也會要求您執行 Azure CLI 2.0.64 版版或更新版本。 執行 `az --version` 以尋找版本。 如果您需要安裝或升級，請參閱[安裝 Azure CLI][azure-cli-install]。

## <a name="create-an-ingress-controller"></a>建立輸入控制器

根據預設，NGINX 輸入控制器會使用靜態公用 IP 位址指派來建立。 常見的設定需求是使用內部私人網路和 IP 位址。 此方法可讓您將對服務的存取限定於內部使用者，而不提供外部存取。

使用下列範例資訊清單檔建立名為 *internal-ingress.yaml* 的檔案。 此範例會將 *10.240.0.42* 指派給 *loadBalancerIP* 資源。 請提供您自己的內部 IP 位址，用於輸入控制器。 請確定此 IP 位址在您的虛擬網路內尚未被使用。 此外，如果您使用現有的虛擬網路和子網，您必須以正確的許可權設定 AKS 叢集，以管理虛擬網路和子網。 如需詳細資訊，請參閱 [Azure Kubernetes Service (AKS) 中使用 kubenet 網路與您自己的 IP 位址範圍， ][aks-configure-kubenet-networking] 或 [設定 Azure Kubernetes Service (AKS) 中的 Azure CNI 網路 ][aks-configure-advanced-networking] 功能。

```yaml
controller:
  service:
    loadBalancerIP: 10.240.0.42
    annotations:
      service.beta.kubernetes.io/azure-load-balancer-internal: "true"
```

現在，使用 Helm 部署 nginx-ingress 圖表。 若要使用在上一個步驟中建立的資訊清單檔，請新增 `-f internal-ingress.yaml` 參數。 為了新增備援，您必須使用 `--set controller.replicaCount` 參數部署兩個 NGINX 輸入控制器複本。 為充分享有執行輸入控制器複本的好處，請確定 AKS 叢集中有多個節點。

輸入控制器也需要在 Linux 節點上排程。 Windows Server 節點不應執行輸入控制器。 您可以使用 `--set nodeSelector` 參數來指定節點選取器，以告知 Kubernetes 排程器在 Linux 式節點上執行 NGINX 輸入控制器。

> [!TIP]
> 下列範例會建立名為「輸入 *-基本*」之輸入資源的 Kubernetes 命名空間。 視需要指定您自己環境的命名空間。 如果您的 AKS 叢集未啟用 Kubernetes RBAC，請新增 `--set rbac.create=false` 至 Helm 命令。

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
    -f internal-ingress.yaml \
    --set controller.replicaCount=2 \
    --set controller.nodeSelector."beta\.kubernetes\.io/os"=linux \
    --set defaultBackend.nodeSelector."beta\.kubernetes\.io/os"=linux \
    --set controller.admissionWebhooks.patch.nodeSelector."beta\.kubernetes\.io/os"=linux
```

當針對 NGINX 輸入控制器建立 Kubernetes 負載平衡器服務時，系統會指派您的內部 IP 位址。 若要取得公用 IP 位址，請使用 `kubectl get service` 命令。

```console
kubectl --namespace ingress-basic get services -o wide -w nginx-ingress-ingress-nginx-controller
```

將 IP 位址指派給服務需要幾分鐘的時間，如下列範例輸出所示：

```
$ kubectl --namespace ingress-basic get services -o wide -w nginx-ingress-ingress-nginx-controller

NAME                                     TYPE           CLUSTER-IP    EXTERNAL-IP     PORT(S)                      AGE   SELECTOR
nginx-ingress-ingress-nginx-controller   LoadBalancer   10.0.74.133   EXTERNAL_IP     80:32486/TCP,443:30953/TCP   44s   app.kubernetes.io/component=controller,app.kubernetes.io/instance=nginx-ingress,app.kubernetes.io/name=ingress-nginx
```

尚未建立任何輸入規則，因此，如果您瀏覽至內部 IP 位址，將會顯示 NGINX 輸入控制器的預設 404 頁面。 輸入規則會於下列步驟中進行設定。

## <a name="run-demo-applications"></a>執行示範應用程式

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

這兩個應用程式現在都已在您的 Kubernetes 叢集上執行。 若要將流量路由至每個應用程式，請建立 Kubernetes 輸入資源。 輸入資源會設定將流量路由至這兩個應用程式之一的規則。

在下列範例中，傳至位址 `http://10.240.0.42/` 的流量會路由傳送至名為 `aks-helloworld` 的服務。 傳至位址 `http://10.240.0.42/hello-world-two` 的流量會路由至 `ingress-demo` 服務。

建立名為 `hello-world-ingress.yaml` 的檔案，並複製到下列範例 YAML 中。

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

下列範例輸出顯示已建立輸入資源。

```
$ kubectl apply -f hello-world-ingress.yaml

ingress.extensions/hello-world-ingress created
```

## <a name="test-the-ingress-controller"></a>測試輸入控制器

若要測試輸入控制器的路由，請使用 Web 用戶端瀏覽至這兩個應用程式。 如有需要，您可以從 AKS 叢集上的 Pod 快速測試此僅供內部使用的功能。 建立測試 Pod，並將終端機工作階段與它連結：

```console
kubectl run -it --rm aks-ingress-test --image=debian --namespace ingress-basic
```

使用 `apt-get` 在 Pod 中安裝 `curl`：

```console
apt-get update && apt-get install -y curl
```

現在使用來存取 Kubernetes 輸入控制器的位址 `curl` ，例如 *http://10.240.0.42* 。 請提供您自己在本文的第一個步驟中部署輸入控制器時所指定的內部 IP 位址。

```console
curl -L http://10.240.0.42
```

位址未提供任何額外的路徑，因此輸入控制器會預設為 */* 路由。 此時會傳回第一個示範應用程式，如下列簡要範例輸出所示：

```
$ curl -L http://10.240.0.42

<!DOCTYPE html>
<html xmlns="http://www.w3.org/1999/xhtml">
<head>
    <link rel="stylesheet" type="text/css" href="/static/default.css">
    <title>Welcome to Azure Kubernetes Service (AKS)</title>
[...]
```

現在將 */hello-world-two* 路徑新增至位址，例如 *http://10.240.0.42/hello-world-two* 。 此時會傳回含有自訂標題的第二個示範應用程式，如下列簡要範例輸出所示：

```
$ curl -L -k http://10.240.0.42/hello-world-two

<!DOCTYPE html>
<html xmlns="http://www.w3.org/1999/xhtml">
<head>
    <link rel="stylesheet" type="text/css" href="/static/default.css">
    <title>AKS Ingress Demo</title>
[...]
```

## <a name="clean-up-resources"></a>清除資源

本文使用 Helm 來安裝輸入元件。 部署 Helm 圖表時會建立一些 Kubernetes 資源。 這些資源包含 Pod、部署和服務。 若要清除這些資源，您可以刪除整個範例命名空間或個別資源。

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

尋找名為nginx-ingress 和 aks-helloworld 的圖表，如下列範例輸出所示：

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
- [使用動態公用 IP 建立輸入控制器，並設定 Let's Encrypt 以自動產生 TLS 憑證][aks-ingress-tls]
- [使用靜態公用 IP 位址建立輸入控制器，並設定 Let's Encrypt 以自動產生 TLS 憑證][aks-ingress-static-tls]

<!-- LINKS - external -->
[helm]: https://helm.sh/
[helm-cli]: ./kubernetes-helm.md
[nginx-ingress]: https://github.com/kubernetes/ingress-nginx

<!-- LINKS - internal -->
[use-helm]: kubernetes-helm.md
[azure-cli-install]: /cli/azure/install-azure-cli
[aks-ingress-basic]: ingress-basic.md
[aks-ingress-tls]: ingress-tls.md
[aks-ingress-static-tls]: ingress-static-ip.md
[aks-http-app-routing]: http-application-routing.md
[aks-ingress-own-tls]: ingress-own-tls.md
[client-source-ip]: concepts-network.md#ingress-controllers
[aks-configure-kubenet-networking]: configure-kubenet.md
[aks-configure-advanced-networking]: configure-azure-cni.md