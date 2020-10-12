---
title: 儲存 Helm 圖表
description: 瞭解如何使用 Azure Container Registry 中的存放庫，為您的 Kubernetes 應用程式儲存 Helm 圖表
ms.topic: article
ms.date: 06/12/2020
ms.openlocfilehash: 69b16f35589586787e1c31a0e9755b9030af755d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "86537862"
---
# <a name="push-and-pull-helm-charts-to-an-azure-container-registry"></a>將 Helm 圖推送至 Azure container registry 並提取

若要快速管理及部署 Kubernetes 的應用程式，您可以使用[開放原始碼 Helm 套件管理員][helm]。 使用 Helm 時，應用程式封裝會定義為 [圖表](https://helm.sh/docs/topics/charts/)，並收集並儲存在 [Helm 圖表存放庫](https://helm.sh/docs/topics/chart_repository/)中。

本文說明如何使用 Helm 3 命令，在 Azure container registry 中裝載 Helm 圖存放庫。 在許多案例中，您會針對您所開發的應用程式，建立並上傳您自己的圖表。 如需如何建立您自己的 Helm 圖的詳細資訊，請參閱 [圖表範本開發人員指南][develop-helm-charts]。 您也可以從另一個 Helm 存放庫儲存現有的 Helm 圖表。

> [!IMPORTANT]
> Azure Container Registry 中的 Helm 圖表支援目前為預覽狀態。 當您同意補充 [使用條款][terms-of-use]的條件時，就可以使用預覽。 在公開上市 (GA) 之前，此功能的某些領域可能會變更。

## <a name="helm-3-or-helm-2"></a>Helm 3 或 Helm 2？

若要儲存、管理及安裝 Helm 圖，您可以使用 Helm 用戶端和 Helm CLI。 Helm 用戶端的主要版本包括 Helm 3 和 Helm 2。 如需版本差異的詳細資訊，請參閱 [版本的常見問題](https://helm.sh/docs/faq/)。 

Helm 3 應該用來在 Azure Container Registry 中裝載 Helm 圖表。 使用 Helm 3，您可以：

* 可以在 Azure container registry 中建立一或多個 Helm 存放庫
* 將 Helm 3 圖表儲存在登錄中作為 [OCI 構件](container-registry-image-formats.md#oci-artifacts)。 目前，OCI 的 Helm 3 支援為 *實驗*性。
* 使用命令向登錄進行驗證 `helm registry login` 。
* 使用 `helm chart` HELM CLI 中的命令來推送、提取及管理登錄中的 Helm 圖表
* 使用 `helm install` 從本機存放庫快取將圖表安裝至 Kubernetes 叢集。
> [!NOTE]
> 從 Helm 3 開始，與 Helm 2 用戶端搭配使用的 [az acr Helm][az-acr-helm] 命令即將淘汰。 請參閱 [產品藍圖](https://github.com/Azure/acr/blob/master/docs/acr-roadmap.md#acr-helm-ga)。 如果您先前已部署 Helm 2 圖表，請參閱 [將 Helm V2 遷移至 v3](https://helm.sh/docs/topics/v2_v3_migration/)。

## <a name="prerequisites"></a>Prerequisites

本文中的案例需要下列資源：

- Azure 訂用帳戶中**的 azure container registry** 。 如有需要，請使用 [Azure 入口網站](container-registry-get-started-portal.md) 或 [Azure CLI](container-registry-get-started-azure-cli.md)建立登錄。
- **Helm client version 3.1.0 或更新** 版本-執行 `helm version` 以尋找您目前的版本。 如需有關如何安裝及升級 Helm 的詳細資訊，請參閱[安裝 Helm][helm-install]。
- 您將在其中安裝 Helm 圖表**的 Kubernetes**叢集。 如有需要，請建立 [Azure Kubernetes Service][aks-quickstart]叢集。 
- **Azure CLI 2.0.71 版或更新** 版本-執行 `az --version` 以尋找版本。 如果您需要安裝或升級，請參閱[安裝 Azure CLI][azure-cli-install]。

## <a name="enable-oci-support"></a>啟用 OCI 支援

使用 `helm version` 命令確認您已安裝 Helm 3：

```console
helm version
```

設定下列環境變數，以啟用 Helm 3 用戶端中的 OCI 支援。 目前，這項支援是實驗性的。 

```console
export HELM_EXPERIMENTAL_OCI=1
```

## <a name="create-a-sample-chart"></a>建立範例圖表

使用下列命令建立測試圖表：

```console
mkdir helmtest

cd helmtest
helm create hello-world
```

在基本範例中，將目錄變更為 `templates` 資料夾，並先刪除該處的內容：

```console
cd hello-world/templates
rm -rf *
```

在 `templates` 資料夾中， `configmap.yaml` 執行下列命令來建立名為的檔案：

```console
cat <<EOF > configmap.yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: hello-world-configmap
data:
  myvalue: "Hello World"
EOF
```

如需建立和執行此範例的詳細資訊，請參閱 Helm 檔中的 [消費者入門](https://helm.sh/docs/chart_template_guide/getting_started/) 。

## <a name="save-chart-to-local-registry-cache"></a>將圖表儲存至本機登錄快取

將目錄變更為 `hello-world` 子目錄。 然後，執行 `helm chart save` 以在本機儲存圖表的複本，並建立具有登錄完整名稱的別名 (所有小寫) 和目標存放庫和標記。 

在下列範例中，登錄名稱為 *>mycontainerregistry*、目標存放庫為 *hello world*，而靶心圖表表標記為 *v1*，但替代您環境的值：

```console
cd ..
helm chart save . hello-world:v1
helm chart save . mycontainerregistry.azurecr.io/helm/hello-world:v1
```

執行 `helm chart list` 以確認您已將圖表儲存在本機登錄快取中。 輸出會類似：

```console
REF                                                      NAME            VERSION DIGEST  SIZE            CREATED
hello-world:v1                                           hello-world       0.1.0   5899db0 3.2 KiB        2 minutes 
mycontainerregistry.azurecr.io/helm/hello-world:v1       hello-world       0.1.0   5899db0 3.2 KiB        2 minutes
```

## <a name="authenticate-with-the-registry"></a>使用登錄進行驗證

`helm registry login`在 Helm 3 CLI 中執行命令，使用適合您案例的認證向登錄[進行驗證](container-registry-authentication.md)。

例如，建立 [具有提取和推送許可權的 Azure Active Directory 服務主體](container-registry-auth-service-principal.md#create-a-service-principal) (AcrPush 角色) 登錄。 然後提供服務主體認證給 `helm registry login` 。 下列範例會使用環境變數來提供密碼：

```console
echo $spPassword | helm registry login mycontainerregistry.azurecr.io \
  --username <service-principal-id> \
  --password-stdin
```

## <a name="push-chart-to-registry"></a>將圖表推送至登錄

`helm chart push`在 Helm 3 CLI 中執行命令，將圖表推送至完整的目標存放庫：

```console
helm chart push mycontainerregistry.azurecr.io/helm/hello-world:v1
```

成功推送之後，輸出類似于：

```output
The push refers to repository [mycontainerregistry.azurecr.io/helm/hello-world]
ref:     mycontainerregistry.azurecr.io/helm/hello-world:v1
digest:  5899db028dcf96aeaabdadfa5899db025899db025899db025899db025899db02
size:    3.2 KiB
name:    hello-world
version: 0.1.0
```

## <a name="list-charts-in-the-repository"></a>列出存放庫中的圖表

如同儲存在 Azure container registry 中的映射，您可以使用 [az acr repository][az-acr-repository] 命令來顯示裝載圖表的存放庫，以及圖表標記和資訊清單。 

例如，執行 [az acr repository show][az-acr-repository-show] 以查看您在上一個步驟中建立的存放庫屬性：

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

執行 [az acr repository show-資訊清單][az-acr-repository-show-manifests] 命令，以查看儲存在存放庫中的圖表詳細資料。 例如：

```azurecli
az acr repository show-manifests \
  --name mycontainerregistry \
  --repository helm/hello-world --detail
```

輸出（在此範例中為縮寫）會顯示 `configMediaType` 下列各項 `application/vnd.cncf.helm.config.v1+json` ：

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

## <a name="pull-chart-to-local-cache"></a>將圖表提取至本機快取

若要將 Helm 圖安裝到 Kubernetes，圖表必須在本機快取中。 在此範例中，請先執行 `helm chart remove` 以移除名為的現有本機圖表 `mycontainerregistry.azurecr.io/helm/hello-world:v1` ：

```console
helm chart remove mycontainerregistry.azurecr.io/helm/hello-world:v1
```

執行 `helm chart pull` 以將圖表從 Azure container registry 下載至本機快取：

```console
helm chart pull mycontainerregistry.azurecr.io/helm/hello-world:v1
```

## <a name="export-helm-chart"></a>匯出 Helm 圖表

若要進一步處理圖表，請使用將其匯出至本機目錄 `helm chart export` 。 例如，將您提取的圖表匯出至 `install` 目錄：

```console
helm chart export mycontainerregistry.azurecr.io/helm/hello-world:v1 \
  --destination ./install
```

若要在存放庫中查看匯出的圖表資訊，請 `helm show chart` 在您匯出圖表的目錄中執行命令。

```console
cd install
helm show chart hello-world
```

Helm 會傳回圖表最新版本的詳細資訊，如下列範例輸出所示：

```output
apiVersion: v2
appVersion: 1.16.0
description: A Helm chart for Kubernetes
name: hello-world
type: application
version: 0.1.0    
```

## <a name="install-helm-chart"></a>安裝 Helm 圖表

執行 `helm install` 以安裝您提取至本機快取的 Helm 圖表，並匯出。 請指定發行名稱（例如 *myhelmtest*），或傳遞 `--generate-name` 參數。 例如：

```console
helm install myhelmtest ./hello-world
```

成功安裝圖表之後的輸出如下所示：

```console
NAME: myhelmtest
LAST DEPLOYED: Fri Mar 20 14:14:42 2020
NAMESPACE: default
STATUS: deployed
REVISION: 1
TEST SUITE: None
```

若要確認安裝，請執行 `helm get manifest` 命令。 

```console
helm get manifest myhelmtest
```

此命令會傳回您範本檔案中的 YAML 資料 `configmap.yaml` 。

執行 `helm uninstall` 以卸載叢集上的圖表版本：

```console
helm uninstall myhelmtest
```

## <a name="delete-chart-from-the-registry"></a>從登錄中刪除圖表

若要從容器登錄中刪除圖表，請使用 [az acr repository delete][az-acr-repository-delete] 命令。 執行下列命令，並在出現提示時確認操作：

```azurecli
az acr repository delete --name mycontainerregistry --image helm/hello-world:v1
```

## <a name="next-steps"></a>接下來的步驟

* 如需有關如何建立及部署 Helm 圖表的詳細資訊，請參閱[開發 Helm 圖表][develop-helm-charts]。
* 深入瞭解如何在 [Azure Kubernetes Service (AKS) ](../aks/kubernetes-helm.md)中使用 Helm 安裝應用程式。
* Helm 圖表可以用來作為容器建置程序的一部分。 如需詳細資訊，請參閱 [使用 Azure Container Registry 工作][acr-tasks]。

<!-- LINKS - external -->
[helm]: https://helm.sh/
[helm-install]: https://helm.sh/docs/intro/install/
[develop-helm-charts]: https://helm.sh/docs/chart_template_guide/
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
[acr-tasks]: container-registry-tasks-overview.md
