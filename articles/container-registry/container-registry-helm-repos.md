---
title: 存儲頭盔圖表
description: 瞭解如何使用 Azure 容器註冊表中的存儲庫為 Kubernetes 應用程式存儲 Helm 圖表
ms.topic: article
ms.date: 03/20/2020
ms.openlocfilehash: 6304486ac493e235ed74f26ab4be4f843ef52513
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80131489"
---
# <a name="push-and-pull-helm-charts-to-an-azure-container-registry"></a>將 Helm 圖表推送並拉至 Azure 容器註冊表

若要快速管理及部署 Kubernetes 的應用程式，您可以使用[開放原始碼 Helm 套件管理員][helm]。 使用 Helm，應用程式包被定義為[圖表](https://helm.sh/docs/topics/charts/)，它們被收集和存儲在[Helm 圖表存儲庫](https://helm.sh/docs/topics/chart_repository/)中。

本文演示如何使用 Helm 3 或 Helm 2 安裝在 Azure 容器註冊表中託管 Helm 圖表存儲庫。 在許多情況下，您將為開發的應用程式構建和上傳自己的圖表。 有關如何構建自己的 Helm 圖表的詳細資訊，請參閱[圖表範本開發人員指南][develop-helm-charts]。 您還可以存儲來自另一個 Helm 回購的現有 Helm 圖表。

> [!IMPORTANT]
> Azure 容器註冊表中對 Helm 圖表的支援當前處於預覽狀態。 預覽版在您同意補充[使用條款][terms-of-use]的前提下提供給您。 在公開上市 (GA) 之前，此功能的某些領域可能會變更。

## <a name="helm-3-or-helm-2"></a>頭盔3或頭盔2？

要存儲、管理和安裝 Helm 圖表，請使用 Helm 用戶端和 Helm CLI。 赫爾姆用戶端的主要版本包括赫爾姆3和赫爾姆2。 Helm 3 支援新的圖表格式，不再安裝 Tiller 伺服器端元件。 有關版本差異的詳細資訊，請參閱[版本常見問題解答](https://helm.sh/docs/faq/)。 如果您以前部署了 Helm 2 圖表，請參閱[將 Helm v2 遷移到 v3](https://helm.sh/docs/topics/v2_v3_migration/)。

您可以使用 Helm 3 或 Helm 2 在 Azure 容器註冊表中託管 Helm 圖表，其中特定于每個版本的工作流：

* [Helm 3](#use-the-helm-3-client)用戶端`helm chart`- 使用 Helm CLI 中的命令將註冊表中的圖表作為[OCI 偽影管理](container-registry-image-formats.md#oci-artifacts)
* [Helm 2 用戶端](#use-the-helm-2-client)- 在 Azure CLI 中使用[az acr 掌舵][az-acr-helm]命令作為 Helm 圖表存儲庫添加和管理容器註冊表

### <a name="additional-information"></a>其他資訊

* 對於大多數方案，我們建議使用具有本機`helm chart`命令的 Helm 3 工作流將圖表作為 OCI 工件進行管理。
* 您可以使用舊式[az acr 掌舵][az-acr-helm]Azure CLI 命令和工作流與 Helm 3 用戶端和圖表。 但是，某些命令（如`az acr helm list`與 Helm 3 圖表不相容）。
* 自 Helm 3 起，[支援 az acr 掌舵][az-acr-helm]命令主要是為了與 Helm 2 用戶端和圖表格式相容。 這些命令的未來發展目前未計畫。

## <a name="use-the-helm-3-client"></a>使用 Helm 3 用戶端

### <a name="prerequisites"></a>Prerequisites

- Azure 訂閱中的**Azure 容器註冊表**。 如果需要，請使用[Azure 門戶](container-registry-get-started-portal.md)或[Azure CLI](container-registry-get-started-azure-cli.md)創建註冊表。
- **掌舵用戶端版本 3.1.0 或更高版本**- 運行`helm version`以查找當前版本。 如需有關如何安裝及升級 Helm 的詳細資訊，請參閱[安裝 Helm][helm-install]。
- **Kubernetes 群集**，您將在其中安裝 Helm 圖表。 如果需要，請創建[Azure 庫伯奈斯服務群集][aks-quickstart]。 
- **Azure CLI 版本 2.0.71 或更高版本**- 運行`az --version`以查找版本。 如果需要安裝或升級，請參閱[安裝 Azure CLI][azure-cli-install]。

### <a name="high-level-workflow"></a>高級工作流

使用**頭盔 3**您：

* 可以在 Azure 容器註冊表中創建一個或多個 Helm 存儲庫
* 將 Helm 3 圖表存儲為[OCI 工件](container-registry-image-formats.md#oci-artifacts)。 目前，赫爾姆3對OCI的支援是*實驗性的*。
* 使用`helm registry login`命令對註冊表進行身份驗證。
* 使用`helm chart`Helm CLI 中的命令在註冊表中推送、拉取和管理 Helm 圖表
* 用於`helm install`從本機存放區庫緩存將圖表安裝到 Kubernetes 群集。

有關示例，請參閱以下各節。

### <a name="enable-oci-support"></a>啟用 OCI 支援

設置以下環境變數，以便在 Helm 3 用戶端中啟用 OCI 支援。 目前，這種支援是實驗性的。 

```console
export HELM_EXPERIMENTAL_OCI=1
```

### <a name="create-a-sample-chart"></a>創建示例圖表

使用以下命令創建測試圖表：

```console
mkdir helmtest

cd helmtest
helm create hello-world
```

作為基本示例，將目錄更改為`templates`資料夾，然後首先刪除其中的內容：

```console
rm -rf *
```

在`templates`資料夾中，創建一個檔，`configmap.yaml`該檔案名為以下內容：

```yml
apiVersion: v1
kind: ConfigMap
metadata:
  name: hello-world-configmap
data:
  myvalue: "Hello World"
```

有關創建和運行此示例的更多，請參閱在 Helm 文檔中[入門](https://helm.sh/docs/chart_template_guide/getting_started/)。

### <a name="save-chart-to-local-registry-cache"></a>將圖表保存到本地註冊表緩存

將目錄更改為`hello-world`子目錄。 然後，運行`helm chart save`以在本地保存圖表的副本，並創建一個別名，該別名具有註冊表（所有小寫）和目標存儲庫和標記的完全限定名稱。 

在下面的示例中，註冊表名稱是*我的容器註冊表*，目標回購是*hello-world，* 靶心圖表表標記是*v1，* 但替換環境的值：

```console
cd ..
helm chart save . hello-world:v1
helm chart save . mycontainerregistry.azurecr.io/helm/hello-world:v1
```

運行`helm chart list`以確認將圖表保存在本地註冊表緩存中。 輸出會類似：

```console
REF                                                      NAME            VERSION DIGEST  SIZE            CREATED
hello-world:v1                                           hello-world       0.1.0   5899db0 3.2 KiB        2 minutes 
mycontainerregistry.azurecr.io/helm/hello-world:v1       hello-world       0.1.0   5899db0 3.2 KiB        2 minutes
```

### <a name="authenticate-with-the-registry"></a>使用註冊表進行身份驗證

在`helm registry login`Helm 3 CLI 中運行該命令，使用適合您方案的憑據[對註冊表進行身份驗證](container-registry-authentication.md)。

例如，創建[具有拉取許可權](container-registry-auth-service-principal.md#create-a-service-principal)（AcrPush 角色）的 Azure 活動目錄服務主體。 然後向 提供服務主體憑據`helm registry login`。 以下示例使用環境變數提供密碼：

```console
echo $spPassword | helm registry login mycontainerregistry.azurecr.io \
  --username <service-principal-id> \
  --password-stdin
```

### <a name="push-chart-to-azure-container-registry"></a>將圖表推送到 Azure 容器註冊表

在`helm chart push`Helm 3 CLI 中運行命令，將圖表推送到完全限定的目標存儲庫：

```console
helm chart push mycontainerregistry.azurecr.io/helm/hello-world:v1
```

成功推送後，輸出類似于：

```output
The push refers to repository [mycontainerregistry.azurecr.io/helm/hello-world]
ref:     mycontainerregistry.azurecr.io/helm/hello-world:v1
digest:  5899db028dcf96aeaabdadfa5899db025899db025899db025899db025899db02
size:    3.2 KiB
name:    hello-world
version: 0.1.0
```

### <a name="list-charts-in-the-repository"></a>列出存放庫中的圖表

與存儲在 Azure 容器註冊表中的圖像一樣，可以使用[az acr 存儲庫][az-acr-repository]命令來顯示託管圖表的存儲庫以及圖表標記和清單。 

例如，運行[az acr 存儲庫將顯示][az-acr-repository-show]以查看在上一步中創建的存儲庫的屬性：

```azurecli
az acr repository show \
  --name mycontainerregistry \
  --repository helm/hello-world
```

輸出會類似：

```output
{
  "changeableAttributes": {
    "deleteEnabled": true,
    "listEnabled": true,
    "readEnabled": true,
    "writeEnabled": true
  },
  "createdTime": "2020-03-20T18:11:37.6701689Z",
  "imageName": "helm/hello-world",
  "lastUpdateTime": "2020-03-20T18:11:37.7637082Z",
  "manifestCount": 1,
  "registry": "mycontainerregistry.azurecr.io",
  "tagCount": 1
}
```

運行[az acr 存儲庫顯示清單][az-acr-repository-show-manifests]命令以查看存儲在存儲庫中的圖表的詳細資訊。 例如：

```azurecli
az acr repository show-manifests \
  --name mycontainerregistry \
  --repository helm/hello-world --detail
```

在本示例中縮寫的輸出顯示了 ： `configMediaType` `application/vnd.cncf.helm.config.v1+json`

```output
[
  {
    [...]
    "configMediaType": "application/vnd.cncf.helm.config.v1+json",
    "createdTime": "2020-03-20T18:11:37.7167893Z",
    "digest": "sha256:0c03b71c225c3ddff53660258ea16ca7412b53b1f6811bf769d8c85a1f0663ee",
    "imageSize": 3301,
    "lastUpdateTime": "2020-03-20T18:11:37.7167893Z",
    "mediaType": "application/vnd.oci.image.manifest.v1+json",
    "tags": [
      "v1"
    ]
```

### <a name="pull-chart-to-local-cache"></a>將圖表拉至本機快取

要將 Helm 圖表安裝到庫伯內斯，圖表必須位於本機快取中。 在此示例中，首先運行`helm chart remove`以刪除名為 的`mycontainerregistry.azurecr.io/helm/hello-world:v1`本地圖表：

```console
helm chart remove mycontainerregistry.azurecr.io/helm/hello-world:v1
```

運行`helm chart pull`以將圖表從 Azure 容器註冊表下載到本機快取：

```console
helm chart pull mycontainerregistry.azurecr.io/helm/hello-world:v1
```

### <a name="export-helm-chart"></a>匯出頭盔圖

要進一步處理圖表，請使用`helm chart export`將其匯出到本地目錄。 例如，將拉取的圖表匯出到`install`目錄：

```console
helm chart export mycontainerregistry.azurecr.io/helm/hello-world:v1 \
  --destination ./install
```

要查看在回購中匯出的圖表的資訊，`helm show chart`請運行匯出圖表的目錄中的命令。

```console
cd install
helm show chart hello-world
```

Helm 返回有關圖表最新版本的詳細資訊，如下示例輸出所示：

```output
apiVersion: v2
appVersion: 1.16.0
description: A Helm chart for Kubernetes
name: hello-world
type: application
version: 0.1.0    
```

### <a name="install-helm-chart"></a>安裝頭盔圖

運行`helm install`以安裝您拉到本機快取並匯出的 Helm 圖表。 指定釋放名稱（如*myhelmtest*）或傳遞`--generate-name`參數。 例如：

```console
helm install myhelmtest ./hello-world
```

成功安裝圖表後輸出類似于：

```console
NAME: myhelmtest
LAST DEPLOYED: Fri Mar 20 14:14:42 2020
NAMESPACE: default
STATUS: deployed
REVISION: 1
TEST SUITE: None
```

要驗證安裝，請運行該`helm get manifest`命令。 該命令返回`configmap.yaml`範本檔中的 YAML 資料。

運行`helm uninstall`以卸載群集上的圖表版本：

```console
helm uninstall myhelmtest
```

### <a name="delete-a-helm-chart-from-the-repository"></a>從存放庫中刪除 Helm 圖表

要從存儲庫中刪除圖表，請使用 az [acr 存儲庫刪除][az-acr-repository-delete]命令。 運行以下命令並在提示時確認操作：

```azurecli
az acr repository delete --name mycontainerregistry --image helm/hello-world:v1
```

## <a name="use-the-helm-2-client"></a>使用 Helm 2 用戶端

### <a name="prerequisites"></a>Prerequisites

- Azure 訂閱中的**Azure 容器註冊表**。 如果需要，請使用[Azure 門戶](container-registry-get-started-portal.md)或[Azure CLI](container-registry-get-started-azure-cli.md)創建註冊表。
- **Helm 用戶端 2.11.0 版 (不是 RC 版本) 或更新版本** - 執行 `helm version` 以找出您目前的版本。 此外，您還需要一部在 Kubernetes 叢集內初始化的 Helm 伺服器 (Tiller)。 如果需要，請創建[Azure 庫伯奈斯服務群集][aks-quickstart]。 如需有關如何安裝及升級 Helm 的詳細資訊，請參閱[安裝 Helm][helm-install-v2]。
- **Azure CLI 2.0.46 版或更新版本** - 請執行 `az --version` 來找出版本。 如果需要安裝或升級，請參閱[安裝 Azure CLI][azure-cli-install]。

### <a name="high-level-workflow"></a>高級工作流

使用**頭盔 2**您：

* 將 Azure 容器註冊表配置為*單個*Helm 圖表存儲庫。 Azure 容器註冊表在將圖表添加到存儲庫時管理索引定義。
* 通過 Azure CLI 對 Azure 容器註冊表進行身份驗證，然後使用註冊表 URI 和憑據自動更新 Helm 用戶端。 您無需手動指定此註冊表資訊，因此憑據不會在命令歷史記錄中公開。
* 使用 Azure CLI 中的[az acr 掌舵命令][az-acr-helm]將 Azure 容器註冊表添加為 Helm 圖表存儲庫，並推送和管理圖表。 這些 Azure CLI 命令換行 Helm 2 用戶端命令。
* 將 Azure 容器註冊表中的圖表存儲庫添加到本地 Helm 存儲庫索引，支援圖表搜索。
* 用於`helm install`從本機存放區庫緩存將圖表安裝到 Kubernetes 群集。

有關示例，請參閱以下各節。

### <a name="add-repository-to-helm-client"></a>將存儲庫添加到 Helm 用戶端

使用[az acr 掌線器存儲庫添加][az-acr-helm-repo-add]命令，將 Azure 容器註冊表赫爾姆圖表存儲庫添加到 Helm 用戶端。 此命令會取得 Helm 用戶端所使用 Azure Container Registry 的驗證權杖。 身份驗證權杖的有效期為 3 小時。 與 `docker login` 類似，您可以在未來的 CLI 工作階段中執行此命令，以向 Azure Container Registry Helm 圖表存放庫驗證 Helm 用戶端：

```azurecli
az acr helm repo add --name mycontainerregistry
```

### <a name="add-a-sample-chart-to-the-repository"></a>將示例圖表添加到存儲庫

首先，在 *@/acr-helm*創建本地目錄，然後下載現有的*穩定/按詞*圖表：

```console
mkdir ~/acr-helm && cd ~/acr-helm
helm repo update
helm fetch stable/wordpress
```

鍵入`ls`以列出下載的圖表，並記下檔案名中包含的 Wordpress 版本。 `helm fetch stable/wordpress` 命令並未指定特定的版本，因此擷取的是「最新」** 版本。 在下面的示例輸出中，Wordpress 圖表是版本*8.1.0*：

```output
wordpress-8.1.0.tgz
```

使用 Azure CLI 中的[az acr 掌舵手推送][az-acr-helm-push]命令將圖表推送到 Azure 容器註冊表中的 Helm 圖表存儲庫。 指定在上一步中下載的 Helm 圖表的名稱，例如*wordpress-8.1.0.tgz*：

```azurecli
az acr helm push --name mycontainerregistry wordpress-8.1.0.tgz
```

幾分鐘後，Azure CLI 報告圖表已保存，如以下示例輸出所示：

```output
{
  "saved": true
}
```

### <a name="list-charts-in-the-repository"></a>列出存放庫中的圖表

若要使用在上一個步驟中上傳的圖表，必須更新本機 Helm 存放庫索引。 您可以為 Helm 用戶端中的存放庫重新編製索引，或使用 Azure CLI 來更新存放庫索引。 每次您將圖表新增至存放庫時，都必須完成此步驟：

```azurecli
az acr helm repo add --name mycontainerregistry
```

透過將圖表儲存在您的存放庫中，並在本機提供已更新的索引，您便可以使用一般 Helm 用戶端命令來進行搜尋或安裝。 要查看存儲庫中的所有圖表，請使用 命令`helm search`，提供您自己的 Azure 容器註冊表名稱：

```console
helm search mycontainerregistry
```

這會列出在上一個步驟中推送的 Wordpress 圖表，如以下範例輸出所示：

```output
NAME                  CHART VERSION    APP VERSION    DESCRIPTION
helmdocs/wordpress    8.1.0           5.3.2          Web publishing platform for building blogs and websites.
```

您也可以搭配 Azure CLI 使用 [az acr helm list][az-acr-helm-list]來列出圖表：

```azurecli
az acr helm list --name mycontainerregistry
```

### <a name="show-information-for-a-helm-chart"></a>顯示 Helm 圖表的資訊

要查看回購中特定圖表的資訊，可以使用 命令`helm inspect`。

```console
helm inspect mycontainerregistry/wordpress
```

未提供任何版本號碼時，會使用「最新」** 版本。 Helm 會傳回您圖表的相關詳細資訊，如以下扼要的範例輸出所示：

```output
apiVersion: v1
appVersion: 5.3.2
description: Web publishing platform for building blogs and websites.
engine: gotpl
home: http://www.wordpress.com/
icon: https://bitnami.com/assets/stacks/wordpress/img/wordpress-stack-220x234.png
keywords:
- wordpress
- cms
- blog
- http
- web
- application
- php
maintainers:
- email: containers@bitnami.com
  name: Bitnami
name: wordpress
sources:
- https://github.com/bitnami/bitnami-docker-wordpress
version: 8.1.0
[...]
```

您也可以使用 Azure CLI [az acr helm show][az-acr-helm-show] 命令來顯示圖表的資訊。 同樣地，預設會傳回圖表的「最新」** 版本。 您可以追加到`--version`列出圖表的特定版本，例如*8.1.0*：

```azurecli
az acr helm show --name mycontainerregistry wordpress
```

### <a name="install-a-helm-chart-from-the-repository"></a>從存放庫安裝 Helm 圖表

通過指定存儲庫名稱和圖表名稱來安裝存儲庫中的 Helm 圖表。 使用 Helm 用戶端來安裝 Wordpress 圖表：

```console
helm install mycontainerregistry/wordpress
```

> [!TIP]
> 如果您推送至 Azure Container Registry Helm 圖表存放庫並稍後回到新的 CLI 工作階段，您的本機 Helm 用戶端就會需要已更新的驗證權杖。 若要取得新的驗證權杖，請使用 [az acr helm repo add][az-acr-helm-repo-add] 命令。

下列步驟會在安裝程序期間完成：

- Helm 用戶端會搜尋本機存放庫索引。
- 對應的圖表會下載自 Azure Container Registry 存放庫。
- 此圖表是使用您 Kubernetes 叢集中的 Tiller 來部署的。

安裝進行時，按照命令輸出中的說明查看 WorPress URL 和憑據。 您還可以運行該`kubectl get pods`命令以查看通過 Helm 圖表部署的庫伯內特資源：

```output
NAME                                    READY   STATUS    RESTARTS   AGE
wordpress-1598530621-67c77b6d86-7ldv4   1/1     Running   0          2m48s
wordpress-1598530621-mariadb-0          1/1     Running   0          2m48s
[...]
```

### <a name="delete-a-helm-chart-from-the-repository"></a>從存放庫中刪除 Helm 圖表

若要從存放庫中刪除圖表，請使用 [az acr helm delete][az-acr-helm-delete] 命令。 指定圖表的名稱（如*字壓*）和要刪除的版本（如*8.1.0*）。

```azurecli
az acr helm delete --name mycontainerregistry wordpress --version 8.1.0
```

如果您想要刪除所指定圖表的所有版本，請省略 `--version` 參數。

運行`helm search`時將繼續返回圖表。 同樣地，Helm 用戶端並不會自動更新存放庫中的可用圖表清單。 若要更新 Helm 用戶端存放庫索引，請再次使用 [az acr helm repo add][az-acr-helm-repo-add] 命令：

```azurecli
az acr helm repo add --name mycontainerregistry
```

## <a name="next-steps"></a>後續步驟

* 如需有關如何建立及部署 Helm 圖表的詳細資訊，請參閱[開發 Helm 圖表][develop-helm-charts]。
* 瞭解有關在[Azure 庫伯奈斯服務 （AKS）](../aks/kubernetes-helm.md)中使用 Helm 安裝應用程式的更多資訊。
* Helm 圖表可以用來作為容器建置程序的一部分。 有關詳細資訊，請參閱使用[Azure 容器註冊表任務][acr-tasks]。

<!-- LINKS - external -->
[helm]: https://helm.sh/
[helm-install]: https://helm.sh/docs/intro/install/
[helm-install-v2]: https://v2.helm.sh/docs/using_helm/#installing-helm
[develop-helm-charts]: https://helm.sh/docs/chart_template_guide/
[semver2]: https://semver.org/
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/

<!-- LINKS - internal -->
[azure-cli-install]: /cli/azure/install-azure-cli
[aks-quickstart]: ../aks/kubernetes-walkthrough.md
[acr-bestpractices]: container-registry-best-practices.md
[az-configure]: /cli/azure/reference-index#az-configure
[az-acr-login]: /cli/azure/acr#az-acr-login
[az-acr-helm]: /cli/azure/acr/helm
[az-acr-repository]: /cli/azure/acr/repository
[az-acr-repository-show]: /cli/azure/acr/repository#az-acr-repository-show
[az-acr-repository-delete]: /cli/azure/acr/repository#az-acr-repository-delete
[az-acr-repository-show-tags]: /cli/azure/acr/repository#az-acr-repository-show-tags
[az-acr-repository-show-manifests]: /cli/azure/acr/repository#az-acr-repository-show-manifests
[az-acr-helm-repo-add]: /cli/azure/acr/helm/repo#az-acr-helm-repo-add
[az-acr-helm-push]: /cli/azure/acr/helm#az-acr-helm-push
[az-acr-helm-list]: /cli/azure/acr/helm#az-acr-helm-list
[az-acr-helm-show]: /cli/azure/acr/helm#az-acr-helm-show
[az-acr-helm-delete]: /cli/azure/acr/helm#az-acr-helm-delete
[acr-tasks]: container-registry-tasks-overview.md
