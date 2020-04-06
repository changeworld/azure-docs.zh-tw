---
title: 內部網路上的入口控制器
titleSuffix: Azure Kubernetes Service
description: 了解如何在 Azure Kubernetes Service (AKS) 叢集中安裝及設定內部私人網路的 NGINX 輸入控制器。
services: container-service
ms.topic: article
ms.date: 05/24/2019
ms.openlocfilehash: 4a123a02ed26a5257d3b8e3ee69fb14d96cde550
ms.sourcegitcommit: 67addb783644bafce5713e3ed10b7599a1d5c151
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/05/2020
ms.locfileid: "80668474"
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

本文使用 Helm 來安裝 NGINX 輸入控制器、cert-manager 及範例 Web 應用程式。 您需要在 AKS 叢集內將 Helm 初始化，並使用適用於 Tiller 的服務帳戶。 如需設定及使用 Helm 的詳細資訊，請參閱[在 Azure Kubernetes Service (AKS) 中使用 Helm 安裝應用程式][use-helm]。

本文還要求您運行 Azure CLI 版本 2.0.64 或更高版本。 執行 `az --version` 以尋找版本。 如果您需要安裝或升級，請參閱[安裝 Azure CLI][azure-cli-install]。

## <a name="create-an-ingress-controller"></a>建立輸入控制器

根據預設，NGINX 輸入控制器會使用靜態公用 IP 位址指派來建立。 常見的設定需求是使用內部私人網路和 IP 位址。 此方法可讓您將對服務的存取限定於內部使用者，而不提供外部存取。

使用下列範例資訊清單檔建立名為 *internal-ingress.yaml* 的檔案。 此範例會將 *10.240.0.42* 指派給 *loadBalancerIP* 資源。 請提供您自己的內部 IP 位址，用於輸入控制器。 請確定此 IP 位址在您的虛擬網路內尚未被使用。

```yaml
controller:
  service:
    loadBalancerIP: 10.240.0.42
    annotations:
      service.beta.kubernetes.io/azure-load-balancer-internal: "true"
```

現在，使用 Helm 部署 nginx-ingress** 圖表。 若要使用在上一個步驟中建立的資訊清單檔，請新增 `-f internal-ingress.yaml` 參數。 為了新增備援，您必須使用 `--set controller.replicaCount` 參數部署兩個 NGINX 輸入控制器複本。 為充分享有執行輸入控制器複本的好處，請確定 AKS 叢集中有多個節點。

輸入控制器也需要在 Linux 節點上排程。 Windows 伺服器節點(目前在 AKS 中處於預覽狀態)不應運行入口控制器。 您可以使用 `--set nodeSelector` 參數來指定節點選取器，以告知 Kubernetes 排程器在 Linux 式節點上執行 NGINX 輸入控制器。

> [!TIP]
> 下面的示例為名為入口*基本的*入口資源創建一個 Kubernetes 命名空間。 根據需要為您自己的環境指定命名空間。 如果您的 AKS 群集未啟用 RBAC,`--set rbac.create=false`請新增到 Helm 命令。

> [!TIP]
> 如果要為群集中容器的請求啟用[用戶端源 IP 保留][client-source-ip]`--set controller.service.externalTrafficPolicy=Local`,請添加到 Helm 安裝命令。 用戶端來源 IP 儲存在*X-前轉 -for*下的請求標頭中。 當使用啟用用戶端源 IP 保留的入口控制器時,SSL 傳遞將不起作用。

```console
# Create a namespace for your ingress resources
kubectl create namespace ingress-basic

# Use Helm to deploy an NGINX ingress controller
helm install stable/nginx-ingress \
    --namespace ingress-basic \
    -f internal-ingress.yaml \
    --set controller.replicaCount=2 \
    --set controller.nodeSelector."beta\.kubernetes\.io/os"=linux \
    --set defaultBackend.nodeSelector."beta\.kubernetes\.io/os"=linux
```

為 NGINX 輸入控制器建立 Kubernetes 負載平衡器服務時，會指派您的內部 IP 位址，如下列範例輸出所示：

```
$ kubectl get service -l app=nginx-ingress --namespace ingress-basic

NAME                                              TYPE           CLUSTER-IP    EXTERNAL-IP   PORT(S)                      AGE
alternating-coral-nginx-ingress-controller        LoadBalancer   10.0.97.109   10.240.0.42   80:31507/TCP,443:30707/TCP   1m
alternating-coral-nginx-ingress-default-backend   ClusterIP      10.0.134.66   <none>        80/TCP                       1m
```

尚未建立任何輸入規則，因此，如果您瀏覽至內部 IP 位址，將會顯示 NGINX 輸入控制器的預設 404 頁面。 輸入規則會於下列步驟中進行設定。

## <a name="run-demo-applications"></a>執行示範應用程式

為了檢視運作中的輸入控制器，我們將在 AKS 叢集中執行兩個示範應用程式。 在此範例中，會使用 Helm 來部署簡單 'Hello world' 應用程式的兩個執行個體。

在您可以安裝範例 Helm 圖表之前，先將 Azure 範例存放庫新增至您的 Helm 環境，如下所示：

```console
helm repo add azure-samples https://azure-samples.github.io/helm-charts/
```

使用下列命令，從 Helm 圖表建立第一個示範應用程式：

```console
helm install azure-samples/aks-helloworld --namespace ingress-basic
```

現在，請安裝示範應用程式的第二個執行個體。 對第二個執行個體指定新的標題，以便在視覺上區分這兩個應用程式。 您也要指定唯一的服務名稱：

```console
helm install azure-samples/aks-helloworld \
    --namespace ingress-basic \
    --set title="AKS Ingress Demo" \
    --set serviceName="ingress-demo"
```

## <a name="create-an-ingress-route"></a>建立輸入路由

這兩個應用程式現在都已在您的 Kubernetes 叢集上執行。 若要將流量路由至每個應用程式，請建立 Kubernetes 輸入資源。 輸入資源會設定將流量路由至這兩個應用程式之一的規則。

在下列範例中，傳至位址 `http://10.240.0.42/` 的流量會路由傳送至名為 `aks-helloworld` 的服務。 傳至位址 `http://10.240.0.42/hello-world-two` 的流量會路由至 `ingress-demo` 服務。

建立名為 `hello-world-ingress.yaml` 的檔案，並複製到下列範例 YAML 中。

```yaml
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: hello-world-ingress
  namespace: ingress-basic
  annotations:
    kubernetes.io/ingress.class: nginx
    nginx.ingress.kubernetes.io/ssl-redirect: "false"
    nginx.ingress.kubernetes.io/rewrite-target: /$1
spec:
  rules:
  - http:
      paths:
      - backend:
          serviceName: aks-helloworld
          servicePort: 80
        path: /(.*)
      - backend:
          serviceName: ingress-demo
          servicePort: 80
        path: /hello-world-two(/|$)(.*)
```

使用 `kubectl apply -f hello-world-ingress.yaml` 命令建立輸入資源。

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

現在使用 存取 Kubernets 入口控制`curl`器*http://10.240.0.42*的位址, 例如 。 請提供您自己在本文的第一個步驟中部署輸入控制器時所指定的內部 IP 位址。

```console
curl -L http://10.240.0.42
```

位址未提供其他路徑,因此入口控制器預設為*/* 路由。 此時會傳回第一個示範應用程式，如下列簡要範例輸出所示：

```
$ curl -L 10.240.0.42

<!DOCTYPE html>
<html xmlns="http://www.w3.org/1999/xhtml">
<head>
    <link rel="stylesheet" type="text/css" href="/static/default.css">
    <title>Welcome to Azure Kubernetes Service (AKS)</title>
[...]
```

現在向位址添加 */hello 世界兩個**http://10.240.0.42/hello-world-two*路徑,如 。 此時會傳回含有自訂標題的第二個示範應用程式，如下列簡要範例輸出所示：

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

本文使用 Helm 來安裝輸入元件和範例應用程式。 部署 Helm 圖表時會建立一些 Kubernetes 資源。 這些資源包含 Pod、部署和服務。 要清理這些資源,可以刪除整個範例命名空間或單個資源。

### <a name="delete-the-sample-namespace-and-all-resources"></a>刪除範例命名空間與所有資源

要刪除整個範例命名空間,請使用指令`kubectl delete`並指定命名空間名稱。 命名空間中的所有資源都將被刪除。

```console
kubectl delete namespace ingress-basic
```

然後,刪除 AKS hello 世界應用程式的 Helm 回購:

```console
helm repo remove azure-samples
```

### <a name="delete-resources-individually"></a>單獨刪除資源

或者,更精細的方法是刪除創建的單個資源。 使用`helm list`命令列出 Helm 版本。 尋找名為nginx-ingress** 和 aks-helloworld** 的圖表，如下列範例輸出所示：

```
$ helm list

NAME                REVISION    UPDATED                     STATUS      CHART                   APP VERSION NAMESPACE
kissing-ferret      1           Tue Oct 16 17:13:39 2018    DEPLOYED    nginx-ingress-0.22.1    0.15.0      kube-system
intended-lemur      1           Tue Oct 16 17:20:59 2018    DEPLOYED    aks-helloworld-0.1.0                default
pioneering-wombat   1           Tue Oct 16 17:21:05 2018    DEPLOYED    aks-helloworld-0.1.0                default
```

使用 `helm delete` 命令刪除版本。 下列範例會刪除 NGINX 輸入部署和兩個範例 AKS hello world 應用程式。

```
$ helm delete kissing-ferret intended-lemur pioneering-wombat

release "kissing-ferret" deleted
release "intended-lemur" deleted
release "pioneering-wombat" deleted
```

接下來，移除 AKS hello world 應用程式的 Helm 存放庫：

```console
helm repo remove azure-samples
```

移除將流量導向範例應用程式的輸入路由：

```console
kubectl delete -f hello-world-ingress.yaml
```

最後,您可以刪除自身命名空間。 使用`kubectl delete`指令並指定命名空間名稱:

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
[helm-cli]: https://docs.microsoft.com/azure/aks/kubernetes-helm
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