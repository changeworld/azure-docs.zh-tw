---
title: 使用具有靜態 IP 的輸入控制器
titleSuffix: Azure Kubernetes Service
description: 了解如何在 Azure Kubernetes Service (AKS) 叢集中，使用靜態公用 IP 位址來安裝及設定 NGINX 輸入控制器。
services: container-service
ms.topic: article
ms.date: 05/24/2019
ms.openlocfilehash: f0a8f1f1e1b724745e69aef30e2e6404ff6a5484
ms.sourcegitcommit: 34a6fa5fc66b1cfdfbf8178ef5cdb151c97c721c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "82207355"
---
# <a name="create-an-ingress-controller-with-a-static-public-ip-address-in-azure-kubernetes-service-aks"></a>在 Azure Kubernetes Service (AKS) 中使用靜態公用 IP 位址建立輸入控制器

輸入控制器是一項可為 Kubernetes 服務提供反向 Proxy、可設定的流量路由和 TLS 終止的軟體。 Kubernetes 輸入資源可用來設定個別 Kubernetes 服務的輸入規則和路由。 透過輸入控制器和輸入規則，您可以使用單一 IP 位址將流量路由至 Kubernetes 叢集中的多個服務。

本文示範如何在 Azure Kubernetes Service (AKS) 叢集中部署 [NGINX 輸入控制器][nginx-ingress]。 輸入控制器會使用靜態公用 IP 位址來設定。 [cert-manager][cert-manager] 專案會用來自動產生和設定 [Let's Encrypt][lets-encrypt] 憑證。 最後，會有兩個應用程式在 AKS 叢集中執行，且均可透過單一 IP 位址來存取。

您也可以：

- [建立具有外部網路連線的基本輸入控制器][aks-ingress-basic]
- [啟用 HTTP 應用程式路由附加元件][aks-http-app-routing]
- [建立使用自有 TLS 憑證的輸入控制器][aks-ingress-own-tls]
- [建立輸入控制器，其使用 Let's Encrypt 自動產生具有動態公用 IP 的 TLS 憑證][aks-ingress-tls]

## <a name="before-you-begin"></a>開始之前

此文章假設您目前具有 AKS 叢集。 如果您需要 AKS 叢集，請參閱[使用 Azure CLI][aks-quickstart-cli] 或[使用 Azure 入口網站][aks-quickstart-portal]的 AKS 快速入門。

本文使用[Helm 3][helm]來安裝 NGINX 輸入控制器、cert 管理員和範例 web 應用程式。 請確定您使用的是 Helm 的最新版本。 如需升級指示，請參閱[Helm 安裝][helm-install]檔。如需設定和使用 Helm 的詳細資訊，請參閱[在 Azure Kubernetes Service （AKS）中使用 Helm 安裝應用程式][use-helm]。

本文也會要求您執行 Azure CLI 版本2.0.64 或更新版本。 執行 `az --version` 以尋找版本。 如果您需要安裝或升級，請參閱[安裝 Azure CLI][azure-cli-install]。

## <a name="create-an-ingress-controller"></a>建立輸入控制器

根據預設，NGINX 輸入控制器會使用新的公用 IP 位址指派來建立。 此公用 IP 位址對於輸入控制器的生命週期而言只是靜態的，而且如果刪除並重新建立控制器，則會遺失。 常見的設定需求是為 NGINX 輸入控制器提供現有的靜態公用 IP 位址。 如果刪除了輸入控制器，靜態公用 IP 位址仍會保留。 這種方法可讓您在應用程式的整個生命週期中，以一致的方式使用現有的 DNS 記錄和網路設定。

如果您需要建立靜態的公用 IP 位址，請先使用 [az aks show][az-aks-show] 命令，取得 AKS 叢集的資源群組名稱：

```azurecli-interactive
az aks show --resource-group myResourceGroup --name myAKSCluster --query nodeResourceGroup -o tsv
```

接下來，使用 [az network public-ip create][az-network-public-ip-create] 命令，建立具有「靜態」** 配置方法的公用 IP 位址。 下列範例會在上一個步驟所取得的 AKS 叢集資源群組中，建立名為 myAKSPublicIP** 的公用 IP 位址：

```azurecli-interactive
az network public-ip create --resource-group MC_myResourceGroup_myAKSCluster_eastus --name myAKSPublicIP --sku Standard --allocation-method static --query publicIp.ipAddress -o tsv
```

現在，使用 Helm 部署 nginx-ingress** 圖表。 為了新增備援，您必須使用 `--set controller.replicaCount` 參數部署兩個 NGINX 輸入控制器複本。 為充分享有執行輸入控制器複本的好處，請確定 AKS 叢集中有多個節點。

您必須將兩個額外的參數傳遞至 Helm 版本，讓輸入控制器知道要配置給輸入控制器服務的負載平衡器靜態 IP 位址，以及要套用至公用 IP 位址資源的 DNS 名稱標籤。 為了讓 HTTPS 憑證能夠正常運作，會使用 DNS 名稱標籤來設定輸入控制器 IP 位址的 FQDN。

1. 新增`--set controller.service.loadBalancerIP`參數。 指定您自己在上一個步驟中建立的公用 IP 位址。
1. 新增`--set controller.service.annotations."service\.beta\.kubernetes\.io/azure-dns-label-name"`參數。 指定要套用至在上一個步驟中建立之公用 IP 位址的 DNS 名稱標籤。

輸入控制器也需要在 Linux 節點上排程。 Windows Server 節點不應執行輸入控制器。 您可以使用 `--set nodeSelector` 參數來指定節點選取器，以告知 Kubernetes 排程器在 Linux 式節點上執行 NGINX 輸入控制器。

> [!TIP]
> 下列範例會建立名為「輸入 *-基本*」的輸入資源的 Kubernetes 命名空間。 視需要指定您自己環境的命名空間。 如果您的 AKS 叢集未啟用 RBAC，請`--set rbac.create=false`將新增至 Helm 命令。

> [!TIP]
> 如果您想要為叢集中的容器要求啟用[用戶端來源 IP 保留][client-source-ip]，請將`--set controller.service.externalTrafficPolicy=Local`新增至 Helm install 命令。 用戶端來源 IP 會儲存在要求標頭的 [ *X-轉送-*] 下。 當使用已啟用用戶端來源 IP 保留的輸入控制器時，TLS 傳遞將無法正常執行。

以輸入控制器的**IP 位址**和您想要用於 FQDN 首碼的**唯一名稱**，更新下列腳本：

```console
# Create a namespace for your ingress resources
kubectl create namespace ingress-basic

# Use Helm to deploy an NGINX ingress controller
helm install nginx-ingress stable/nginx-ingress \
    --namespace ingress-basic \
    --set controller.replicaCount=2 \
    --set controller.nodeSelector."beta\.kubernetes\.io/os"=linux \
    --set defaultBackend.nodeSelector."beta\.kubernetes\.io/os"=linux \
    --set controller.service.loadBalancerIP="40.121.63.72"
    --set controller.service.annotations."service\.beta\.kubernetes\.io/azure-dns-label-name"="demo-aks-ingress"
```

為 NGINX 輸入控制器建立 Kubernetes 負載平衡器服務時，系統會指派靜態 IP 位址，如下列輸出範例所示：

```
$ kubectl get service -l app=nginx-ingress --namespace ingress-basic

NAME                                        TYPE           CLUSTER-IP    EXTERNAL-IP    PORT(S)                      AGE
nginx-ingress-controller                    LoadBalancer   10.0.232.56   40.121.63.72   80:31978/TCP,443:32037/TCP   3m
nginx-ingress-default-backend               ClusterIP      10.0.95.248   <none>         80/TCP                       3m
```

尚未建立任何輸入規則，因此，如果您瀏覽到公用 IP 位址，就會顯示 NGINX 輸入控制器的預設 404 頁面。 輸入規則會於下列步驟中進行設定。

您可以查詢公用 IP 位址上的 FQDN，以確認 DNS 名稱標籤已套用，如下所示：

```azurecli-interactive
#!/bin/bash
az network public-ip list --resource-group MC_myResourceGroup_myAKSCluster_eastus --query $("[?name=='myAKSPublicIP'].[dnsSettings.fqdn]") -o tsv
```

輸入控制器現在可以透過 IP 位址或 FQDN 來存取。

## <a name="install-cert-manager"></a>安裝 cert-manager

NGINX 輸入控制器支援 TLS 終止。 有數種方式可擷取和設定 HTTPS 的憑證。 本文示範如何使用 [cert-manager][cert-manager]，其會提供自動化的 [Lets Encrypt][lets-encrypt] 憑證產生與管理功能。

> [!NOTE]
> 本文會針對 Let's Encrypt 使用 `staging` 環境。 在生產環境部署中，於資源定義中以及安裝 Helm 圖表時使用 `letsencrypt-prod` 和 `https://acme-v02.api.letsencrypt.org/directory`。

若要在已啟用 RBAC 的叢集中安裝 cert-manager 控制器，請使用下列 `helm install` 命令：

```console
# Install the CustomResourceDefinition resources separately
kubectl apply --validate=false -f https://raw.githubusercontent.com/jetstack/cert-manager/release-0.13/deploy/manifests/00-crds.yaml

# Label the cert-manager namespace to disable resource validation
kubectl label namespace ingress-basic cert-manager.io/disable-validation=true

# Add the Jetstack Helm repository
helm repo add jetstack https://charts.jetstack.io

# Update your local Helm chart repository cache
helm repo update

# Install the cert-manager Helm chart
helm install \
  cert-manager \
  --namespace ingress-basic \
  --version v0.13.0 \
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
  name: letsencrypt-staging
spec:
  acme:
    server: https://acme-staging-v02.api.letsencrypt.org/directory
    email: user@contoso.com
    privateKeySecretRef:
      name: letsencrypt-staging
    solvers:
    - http01:
        ingress:
          class: nginx
```

若要建立簽發者，請使用 `kubectl apply -f cluster-issuer.yaml` 命令。

```
$ kubectl apply -f cluster-issuer.yaml --namespace ingress-basic

clusterissuer.cert-manager.io/letsencrypt-staging created
```

## <a name="run-demo-applications"></a>執行示範應用程式

輸入控制器和憑證管理解決方案皆已設定。 現在讓我們在您的 AKS 叢集中執行兩個示範應用程式。 在此範例中，會使用 Helm 來部署簡單 'Hello world' 應用程式的兩個執行個體。

在您可以安裝範例 Helm 圖表之前，先將 Azure 範例存放庫新增至您的 Helm 環境，如下所示：

```console
helm repo add azure-samples https://azure-samples.github.io/helm-charts/
```

使用下列命令，從 Helm 圖表建立第一個示範應用程式：

```console
helm install aks-helloworld azure-samples/aks-helloworld --namespace ingress-basic
```

現在，請安裝示範應用程式的第二個執行個體。 對第二個執行個體指定新的標題，以便在視覺上區分這兩個應用程式。 您也要指定唯一的服務名稱：

```console
helm install aks-helloworld-2 azure-samples/aks-helloworld \
    --namespace ingress-basic \
    --set title="AKS Ingress Demo" \
    --set serviceName="ingress-demo"
```

## <a name="create-an-ingress-route"></a>建立輸入路由

這兩個應用程式現在都已在您的 Kubernetes 叢集上執行，不過，它們是以 `ClusterIP` 類型的服務設定的。 因此，這些應用程式無法從網際網路存取。 若要使其可公開使用，請建立 Kubernetes 輸入資源。 輸入資源會設定將流量路由至這兩個應用程式之一的規則。

在下列範例中，傳至位址 `https://demo-aks-ingress.eastus.cloudapp.azure.com/` 的流量會路由傳送至名為 `aks-helloworld` 的服務。 傳至位址 `https://demo-aks-ingress.eastus.cloudapp.azure.com/hello-world-two` 的流量會路由至 `ingress-demo` 服務。 將 *hosts* 和 *host* 更新為您在上一個步驟中建立的 DNS 名稱。

建立名為 `hello-world-ingress.yaml` 的檔案，並複製到下列範例 YAML 中。

```yaml
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: hello-world-ingress
  annotations:
    kubernetes.io/ingress.class: nginx
    cert-manager.io/cluster-issuer: letsencrypt-staging
    nginx.ingress.kubernetes.io/rewrite-target: /$1
spec:
  tls:
  - hosts:
    - demo-aks-ingress.eastus.cloudapp.azure.com
    secretName: tls-secret
  rules:
  - host: demo-aks-ingress.eastus.cloudapp.azure.com
    http:
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

使用 `kubectl apply -f hello-world-ingress.yaml --namespace ingress-basic` 命令建立輸入資源。

```
$ kubectl apply -f hello-world-ingress.yaml --namespace ingress-basic

ingress.extensions/hello-world-ingress created
```

## <a name="create-a-certificate-object"></a>建立憑證物件

接下來，必須建立憑證資源。 憑證資源會定義所需的 X.509 憑證。 如需詳細資訊，請參閱[cert-管理員憑證][cert-manager-certificates]。

Cert-manager 有可能已使用 ingress-shim 自動為您建立憑證物件，v0.2.2 之後的 cert-manager 會自動與 ingress-shim 搭配部署。 如需詳細資訊請，請參閱 [ingress-shim 文件][ingress-shim] \(英文\)。

若要確認已成功建立憑證，請使用 `kubectl describe certificate tls-secret --namespace ingress-basic` 命令。

如果已發出憑證，您將會看到類似下列的輸出：
```
Type    Reason          Age   From          Message
----    ------          ----  ----          -------
  Normal  CreateOrder     11m   cert-manager  Created new ACME order, attempting validation...
  Normal  DomainVerified  10m   cert-manager  Domain "demo-aks-ingress.eastus.cloudapp.azure.com" verified with "http-01" validation
  Normal  IssueCert       10m   cert-manager  Issuing certificate...
  Normal  CertObtained    10m   cert-manager  Obtained certificate from ACME server
  Normal  CertIssued      10m   cert-manager  Certificate issued successfully
```

如果您需要建立額外的憑證資源，可以使用下列範例資訊清單來執行該動作。 將 *dnsNames* 和 *domains* 更新為您在上一個步驟中建立的 DNS 名稱。 如果您使用僅供內部使用的輸入控制器，請為您的服務指定內部 DNS 名稱。

```yaml
apiVersion: cert-manager.io/v1alpha2
kind: Certificate
metadata:
  name: tls-secret
  namespace: ingress-basic
spec:
  secretName: tls-secret
  dnsNames:
  - demo-aks-ingress.eastus.cloudapp.azure.com
  acme:
    config:
    - http01:
        ingressClass: nginx
      domains:
      - demo-aks-ingress.eastus.cloudapp.azure.com
  issuerRef:
    name: letsencrypt-staging
    kind: ClusterIssuer
```

若要建立憑證資源，請使用 `kubectl apply -f certificates.yaml` 命令。

```
$ kubectl apply -f certificates.yaml

certificate.cert-manager.io/tls-secret created
```

## <a name="test-the-ingress-configuration"></a>測試輸入組態

將網頁瀏覽器開啟至 Kubernetes 輸入控制器的 FQDN，例如*`https://demo-aks-ingress.eastus.cloudapp.azure.com`*。

如這些範例所`letsencrypt-staging`使用，所發出的 TLS/SSL 憑證不受瀏覽器的信任。 接受警告提示以繼續您的應用程式。 憑證資訊顯示這個 *Fake LE Intermediate X1* 憑證是由 Let's Encrypt 所簽發的。 這個假憑證表示 `cert-manager` 已正確處理要求並接收來自提供者的憑證：

![Let's Encrypt 暫存憑證](media/ingress/staging-certificate.png)

當您變更 Let's Encrypt 來使用 `prod` 而非 `staging` 時，會使用由 Let's Encrypt 所簽發的信任憑證，如下列範例所示：

![Let’s Encrypt 憑證](media/ingress/certificate.png)

示範應用程式會顯示於網頁瀏覽器中：

![應用程式範例一](media/ingress/app-one.png)

現在，將 */hello-world-two* 路徑新增至 FQDN，例如 *`https://demo-aks-ingress.eastus.cloudapp.azure.com/hello-world-two`*。 即會顯示第二個具有自訂標題的示範應用程式：

![應用程式範例二](media/ingress/app-two.png)

## <a name="clean-up-resources"></a>清除資源

本文使用 Helm 來安裝輸入元件、憑證及範例應用程式。 部署 Helm 圖表時會建立一些 Kubernetes 資源。 這些資源包含 Pod、部署和服務。 若要清除這些資源，您可以刪除整個範例命名空間或個別資源。

### <a name="delete-the-sample-namespace-and-all-resources"></a>刪除範例命名空間和所有資源

若要刪除整個範例命名空間，請`kubectl delete`使用命令並指定您的命名空間名稱。 命名空間中的所有資源都會被刪除。

```console
kubectl delete namespace ingress-basic
```

然後，移除 AKS hello world 應用程式的 Helm 存放庫：

```console
helm repo remove azure-samples
```

### <a name="delete-resources-individually"></a>個別刪除資源

或者，更細微的方法是刪除所建立的個別資源。 首先，移除憑證資源：

```console
kubectl delete -f certificates.yaml
kubectl delete -f cluster-issuer.yaml
```

現在使用 `helm list` 命令，列出 Helm 版本。 尋找名為 nginx-ingress**、cert-manager** 和 aks-helloworld** 的圖表，如下列範例輸出所示：

```
$ helm list --all-namespaces

NAME                    NAMESPACE       REVISION        UPDATED                        STATUS          CHART                   APP VERSION
aks-helloworld          ingress-basic   1               2020-01-11 15:02:21.51172346   deployed        aks-helloworld-0.1.0
aks-helloworld-2        ingress-basic   1               2020-01-11 15:03:10.533465598  deployed        aks-helloworld-0.1.0
nginx-ingress           ingress-basic   1               2020-01-11 14:51:03.454165006  deployed        nginx-ingress-1.28.2    0.26.2
cert-manager            ingress-basic    1               2020-01-06 21:19:03.866212286  deployed        cert-manager-v0.13.0    v0.13.0
```

使用 `helm uninstall` 命令刪除版本。 下列範例會刪除 NGINX 輸入部署、憑證管理員以及兩個範例 AKS hello world 應用程式。

```
$ helm uninstall aks-helloworld aks-helloworld-2 nginx-ingress cert-manager -n ingress-basic

release "aks-helloworld" deleted
release "aks-helloworld-2" deleted
release "nginx-ingress" deleted
release "cert-manager" deleted
```

接下來，移除 AKS hello world 應用程式的 Helm 存放庫：

```console
helm repo remove azure-samples
```

刪除本身的命名空間。 使用`kubectl delete`命令，並指定您的命名空間名稱：

```console
kubectl delete namespace ingress-basic
```

最後，移除針對輸入控制器所建立的靜態公用 IP 位址。 提供您在本文第一個步驟中取得的 MC_** 叢集資源群組名稱，例如 MC_myResourceGroup_myAKSCluster_eastus**：

```azurecli-interactive
az network public-ip delete --resource-group MC_myResourceGroup_myAKSCluster_eastus --name myAKSPublicIP
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
- [使用動態公用 IP 建立輸入控制器，並設定 Let's Encrypt 以自動產生 TLS 憑證][aks-ingress-tls]

<!-- LINKS - external -->
[helm-cli]: https://docs.microsoft.com/azure/aks/kubernetes-helm
[cert-manager]: https://github.com/jetstack/cert-manager
[cert-manager-certificates]: https://cert-manager.readthedocs.io/en/latest/reference/certificates.html
[cert-manager-cluster-issuer]: https://cert-manager.readthedocs.io/en/latest/reference/clusterissuers.html
[cert-manager-issuer]: https://cert-manager.readthedocs.io/en/latest/reference/issuers.html
[lets-encrypt]: https://letsencrypt.org/
[nginx-ingress]: https://github.com/kubernetes/ingress-nginx
[helm]: https://helm.sh/
[helm-install]: https://docs.helm.sh/using_helm/#installing-helm
[ingress-shim]: https://docs.cert-manager.io/en/latest/tasks/issuing-certificates/ingress-shim.html

<!-- LINKS - internal -->
[use-helm]: kubernetes-helm.md
[azure-cli-install]: /cli/azure/install-azure-cli
[az-aks-show]: /cli/azure/aks#az-aks-show
[az-network-public-ip-create]: /cli/azure/network/public-ip#az-network-public-ip-create
[aks-ingress-internal]: ingress-internal-ip.md
[aks-ingress-basic]: ingress-basic.md
[aks-ingress-tls]: ingress-tls.md
[aks-http-app-routing]: http-application-routing.md
[aks-ingress-own-tls]: ingress-own-tls.md
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[client-source-ip]: concepts-network.md#ingress-controllers
[install-azure-cli]: /cli/azure/install-azure-cli
