---
title: 使用草稿在 Azure 庫伯奈斯服務 （AKS） 上開發
description: 使用 Draft 搭配 AKS 和 Azure Container Registry
services: container-service
author: zr-msft
ms.topic: article
ms.date: 06/20/2019
ms.author: zarhoads
ms.openlocfilehash: b03256ee65a3c40d8a64d70b877c49e44e68f822
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "77595216"
---
# <a name="quickstart-develop-on-azure-kubernetes-service-aks-with-draft"></a>快速入門：使用草稿在 Azure 庫伯奈斯服務 （AKS） 上開發

草稿是一個開源工具，可説明在 Kubernetes 群集中打包和運行應用程式容器。 使用草稿，您可以快速將應用程式重新部署到 Kubernetes，因為代碼更改發生，而無需將更改提交到版本控制。 有關草稿的詳細資訊，請參閱[GitHub 上的文檔草稿][draft-documentation]。

本文介紹如何使用 Draft 在 AKS 上打包和運行應用程式。


## <a name="prerequisites"></a>Prerequisites

* Azure 訂用帳戶。 如果您沒有 Azure 訂用帳戶，您可以建立[免費帳戶](https://azure.microsoft.com/free)。
* [已安裝 Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest)。
* 已安裝並配置 Docker。 Docker 提供可在 [Mac][docker-for-mac]、[Windows][docker-for-windows] 或 [Linux][docker-for-linux] 系統上設定 Docker 的套件。
* [已安裝 Helm v2][helm-install]。
* [已安裝拔模][draft-documentation]。

## <a name="create-an-azure-kubernetes-service-cluster"></a>建立 Azure Kubernetes Service 叢集

建立 AKS 叢集。 下列命令會建立名為 MyResourceGroup 的資源群組與名為 MyAKS 的 AKS 叢集。

```azurecli
az group create --name MyResourceGroup --location eastus
az aks create -g MyResourceGroup -n MyAKS --location eastus --node-vm-size Standard_DS2_v2 --node-count 1 --generate-ssh-keys
```

## <a name="create-an-azure-container-registry"></a>建立 Azure Container Registry
要使用 Draft 在 AKS 群集中運行應用程式，需要 Azure 容器註冊表來存儲容器映射。 下面的示例使用[az acr 創建][az-acr-create]在*MyResourceGroup*資源組中使用*基本*SKU 創建名為*MyDraftACR*的 ACR。 您應該提供您自己的唯一登錄機碼名稱。 登錄名稱在 Azure 內必須是唯一的，且包含 5-50 個英數字元。 *基本* SKU 對開發用途而言是最符合成本效益的進入點，可在儲存體和輸送量之間取得平衡。

```azurecli
az acr create --resource-group MyResourceGroup --name MyDraftACR --sku Basic
```

輸出類似於下列範例： 記下 ACR 的*登錄伺服器*值，因為它將在後面的步驟中使用。 在下面的示例中 *，mydraftacr.azurecr.io*是*MyDraftACR*的*登錄伺服器*。

```console
{
  "adminUserEnabled": false,
  "creationDate": "2019-06-11T13:35:17.998425+00:00",
  "id": "/subscriptions/<ID>/resourceGroups/MyResourceGroup/providers/Microsoft.ContainerRegistry/registries/MyDraftACR",
  "location": "eastus",
  "loginServer": "mydraftacr.azurecr.io",
  "name": "MyDraftACR",
  "networkRuleSet": null,
  "provisioningState": "Succeeded",
  "resourceGroup": "MyResourceGroup",
  "sku": {
    "name": "Basic",
    "tier": "Basic"
  },
  "status": null,
  "storageAccount": null,
  "tags": {},
  "type": "Microsoft.ContainerRegistry/registries"
}
```


要使用 ACR 實例，必須先登錄草稿。 使用[az acr 登錄命令][az-acr-login]登錄。 下面的示例將登錄到名為*MyDraftACR*的 ACR。

```azurecli
az acr login --name MyDraftACR
```

該命令在完成一次*返回"登錄成功"* 消息。

## <a name="create-trust-between-aks-cluster-and-acr"></a>在 AKS 叢集與 ACR 之間建立信任

AKS 群集還需要訪問 ACR 以拉取容器映射並運行它們。 您可以通過建立信任從 AKS 訪問 ACR。 若要在 AKS 叢集與 ACR 登錄之間建立信任，請授與權限以供 AKS 叢集所使用的 Azure Active Directory 服務主體存取 ACR 登錄。 以下命令將*MyResourceGroup*中的*MyAKS*群集的服務主體授予*MyResourceGroup*中的*MyDraftACR* ACR 的許可權。

```azurecli
# Get the service principal ID of your AKS cluster
AKS_SP_ID=$(az aks show --resource-group MyResourceGroup --name MyAKS --query "servicePrincipalProfile.clientId" -o tsv)

# Get the resource ID of your ACR instance
ACR_RESOURCE_ID=$(az acr show --resource-group MyResourceGroup --name MyDraftACR --query "id" -o tsv)

# Create a role assignment for your AKS cluster to access the ACR instance
az role assignment create --assignee $AKS_SP_ID --scope $ACR_RESOURCE_ID --role contributor
```

## <a name="connect-to-your-aks-cluster"></a>連接到 AKS 群集

若要從本機電腦連線至 Kubernetes 叢集，您應使用 [kubectl][kubectl] (Kubernetes 命令列用戶端)。

如果您使用 Azure Cloud Shell，則 `kubectl` 已安裝。 您也可以使用 [az aks install-cli][] 命令將其安裝於本機：

```azurecli
az aks install-cli
```

若要設定 `kubectl` 以連線到 Kubernetes 叢集，請使用 [az aks get-credentials][] 命令。 下面的示例獲取*MyResource 組中*名為*MyAKS*的 AKS 群集的憑據 ：

```azurecli
az aks get-credentials --resource-group MyResourceGroup --name MyAKS
```

## <a name="create-a-service-account-for-helm"></a>為 Helm 創建服務帳戶

在已啟用 RBAC 的 AKS 叢集中部署 Helm 之前，您需要適用於 Tiller 服務的服務帳戶與角色繫結。 如需在已啟用 RBAC 的叢集中保護 Helm / Tiller 的詳細資訊，請參閱 [Tiller、命名空間和 RBAC][tiller-rbac]。 如果您的 AKS 群集未啟用 RBAC，請跳過此步驟。

建立名為 `helm-rbac.yaml` 的檔案，然後將下列 YAML 複製進來：

```yaml
apiVersion: v1
kind: ServiceAccount
metadata:
  name: tiller
  namespace: kube-system
---
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRoleBinding
metadata:
  name: tiller
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: ClusterRole
  name: cluster-admin
subjects:
  - kind: ServiceAccount
    name: tiller
    namespace: kube-system
```

使用 `kubectl apply` 命令來建立服務帳戶和角色繫結：

```console
kubectl apply -f helm-rbac.yaml
```

## <a name="configure-helm"></a>設定 Helm
若要將基本 Tiller 部署到 AKS 叢集，請使用 [helm init][helm-init] 命令。 如果您的群集未啟用 RBAC，請刪除`--service-account`參數和值。

```console
helm init --service-account tiller --node-selectors "beta.kubernetes.io/os"="linux"
```

## <a name="configure-draft"></a>設定 Draft

如果尚未在本地電腦上配置草稿，請運行`draft init`：

```console
$ draft init
Installing default plugins...
Installation of default plugins complete
Installing default pack repositories...
...
Happy Sailing!
```

您還需要配置草稿以使用 ACR 的*登錄伺服器*。 以下命令用作`draft config set``mydraftacr.azurecr.io`註冊表。

```console
draft config set registry mydraftacr.azurecr.io
```

您已將草稿配置為使用 ACR，草稿可以將容器映射推送到 ACR。 當 Draft 在 AKS 群集中運行應用程式時，無需密碼或機密即可推送或從 ACR 註冊表中提取。 由於在 AKS 群集和 ACR 之間創建了信任，因此身份驗證在 Azure 資源管理器級別使用 Azure 活動目錄進行。

## <a name="download-the-sample-application"></a>下載範例應用程式

此快速入門使用[草稿 GitHub 存儲庫中的 JAVA 應用程式][example-java]示例。 從 GitHub 克隆應用程式並導航到`draft/examples/example-java/`目錄。

```console
git clone https://github.com/Azure/draft
cd draft/examples/example-java/
```

## <a name="run-the-sample-application-with-draft"></a>使用草稿運行應用程式範例

使用`draft create`命令準備應用程式。

```console
draft create
```

此命令會建立在 Kubernetes 叢集中用來執行應用程式的成品。 這些項目包含 Dockerfile、Helm 圖表和 draft.toml** 檔案 (Draft 組態檔)。

```console
$ draft create

--> Draft detected Java (92.205567%)
--> Ready to sail
```

若要在 AKS 叢集中執行應用程式範例，請使用 `draft up` 命令。

```console
draft up
```

此命令生成 Dockerfile 以創建容器映射、將映射推送到 ACR 以及安裝 Helm 圖表以在 AKS 中啟動應用程式。 第一次運行此命令時，推送和拉容器映射可能需要一些時間。 在快取基底圖層後，部署應用程式時所花費的時間就會大幅降低。

```
$ draft up

Draft Up Started: 'example-java': 01CMZAR1F4T1TJZ8SWJQ70HCNH
example-java: Building Docker Image: SUCCESS ⚓  (73.0720s)
example-java: Pushing Docker Image: SUCCESS ⚓  (19.5727s)
example-java: Releasing Application: SUCCESS ⚓  (4.6979s)
Inspect the logs with `draft logs 01CMZAR1F4T1TJZ8SWJQ70HCNH`
```

## <a name="connect-to-the-running-sample-application-from-your-local-machine"></a>從本地電腦連接到正在運行的應用程式範例

若要測試應用程式，請使用 `draft connect` 命令。

```console
draft connect
```

此命令會使用 Proxy 對 Kubernetes Pod 建立安全的連線。 完成時，就可以在提供的 URL 存取應用程式。

```console
$ draft connect

Connect to java:4567 on localhost:49804
[java]: SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
[java]: SLF4J: Defaulting to no-operation (NOP) logger implementation
[java]: SLF4J: See https://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
[java]: == Spark has ignited ...
[java]: >> Listening on 0.0.0.0:4567
```

使用`localhost`URL 在瀏覽器中導航到應用程式以查看應用程式範例。 在上面的示例中，URL 是`http://localhost:49804`。 使用 停止連接`Ctrl+c`。

## <a name="access-the-application-on-the-internet"></a>在網際網路上存取應用程式

上一個步驟針對 AKS 叢集中的應用程式 Pod 建立了 Proxy 連線。 當您在開發並測試應用程式時，您可以讓應用程式在網際網路上供人使用。 要在互聯網上公開應用程式，您可以創建具有[負載平衡器][kubernetes-service-loadbalancer]類型的 Kubernetes 服務。

更新`charts/example-java/values.yaml`以創建*負載平衡器*服務。 將*服務值從**群集 IP*更改為*負載平衡器*。

```yaml
...
service:
  name: java
  type: LoadBalancer
  externalPort: 80
  internalPort: 4567
...
```

保存更改、關閉檔並運行`draft up`以重新運行應用程式。

```console
draft up
```

需要幾分鐘的時間，服務才能傳回公用 IP 位址。 若要監視進度，請使用 `kubectl get service` 命令搭配 watch** 參數：

```console
$ kubectl get service --watch

NAME                TYPE          CLUSTER-IP    EXTERNAL-IP   PORT(S)        AGE
example-java-java   LoadBalancer  10.0.141.72   <pending>     80:32150/TCP   2m
...
example-java-java   LoadBalancer   10.0.141.72   52.175.224.118  80:32150/TCP   7m
```

使用*外部 IP*在瀏覽器中導航到應用程式的負載等化器以查看應用程式範例。 在上面的示例中，IP 是`52.175.224.118`。

## <a name="iterate-on-the-application"></a>針對應用程式反覆執行

您可以通過在本地進行更改和重新運行`draft up`來反覆運算應用程式。

更新在[src/主/java/helloworld/Hello.java][example-java-hello-l7]的第 7 行返回的消息

```java
    public static void main(String[] args) {
        get("/", (req, res) -> "Hello World, I'm Java in AKS!");
    }
```

執行 `draft up` 命令以重新部署應用程式：

```console
$ draft up

Draft Up Started: 'example-java': 01CMZC9RF0TZT7XPWGFCJE15X4
example-java: Building Docker Image: SUCCESS ⚓  (25.0202s)
example-java: Pushing Docker Image: SUCCESS ⚓  (7.1457s)
example-java: Releasing Application: SUCCESS ⚓  (3.5773s)
Inspect the logs with `draft logs 01CMZC9RF0TZT7XPWGFCJE15X4`
```

要查看更新的應用程式，請再次導航到負載等化器的 IP 位址並驗證所做的更改。

## <a name="delete-the-cluster"></a>選取叢集

當不再需要群集時，使用[az 組刪除][az-group-delete]命令刪除資源組、AKS 群集、容器註冊表、存儲在那裡的容器映射以及所有相關資源。

```azurecli-interactive
az group delete --name MyResourceGroup --yes --no-wait
```

> [!NOTE]
> 當您刪除叢集時，不會移除 AKS 叢集所使用的 Azure Active Directory 服務主體。 如需有關如何移除服務主體的步驟，請參閱[AKS 服務主體的考量和刪除][sp-delete]。

## <a name="next-steps"></a>後續步驟

如需有關使用 Draft 的詳細資訊，請參閱 GitHub 上的 Draft 文件。

> [!div class="nextstepaction"]
> [Draft 文件][draft-documentation]


[az-acr-login]: /cli/azure/acr#az-acr-login
[az-acr-create]: /cli/azure/acr#az-acr-login
[az-group-delete]: /cli/azure/group#az-group-delete
[az aks get-credentials]: /cli/azure/aks#az-aks-get-credentials
[az aks install-cli]: /cli/azure/aks#az-aks-install-cli
[kubernetes-ingress]: ./ingress-basic.md

[docker-for-linux]: https://docs.docker.com/engine/installation/#supported-platforms
[docker-for-mac]: https://docs.docker.com/docker-for-mac/
[docker-for-windows]: https://docs.docker.com/docker-for-windows/
[draft-documentation]: https://github.com/Azure/draft/tree/master/docs
[example-java]: https://github.com/Azure/draft/tree/master/examples/example-java
[example-java-hello-l7]: https://github.com/Azure/draft/blob/master/examples/example-java/src/main/java/helloworld/Hello.java#L7
[kubectl]: https://kubernetes.io/docs/user-guide/kubectl/
[kubernetes-service-loadbalancer]: https://kubernetes.io/docs/concepts/services-networking/service/#type-loadbalancer
[helm-init]: https://v2.helm.sh/docs/helm/#helm-init
[helm-install]: https://v2.helm.sh/docs/using_helm/#installing-helm
[sp-delete]: kubernetes-service-principal.md#additional-considerations
[tiller-rbac]: https://v2.helm.sh/docs/using_helm/#tiller-namespaces-and-rbac
