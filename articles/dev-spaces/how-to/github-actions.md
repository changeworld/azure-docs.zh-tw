---
title: gitHub 操作& Azure 庫伯奈斯服務(預覽)
services: azure-dev-spaces
ms.date: 04/03/2020
ms.topic: conceptual
description: 使用 GitHub 操作與 Azure 開發空間直接在 Azure 庫伯奈斯服務中查看和測試從拉取請求中的變更
keywords: Docker、Kubernetes、Azure、AKS、Azure 庫伯奈斯服務、容器、GitHub 操作、Helm、服務網格、服務網格路由、庫貝克特爾、k8s
manager: gwallace
ms.openlocfilehash: a83da0ef3958748831eb0eeda1aa5e91efa7ef2e
ms.sourcegitcommit: 0450ed87a7e01bbe38b3a3aea2a21881f34f34dd
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/03/2020
ms.locfileid: "80637936"
---
# <a name="github-actions--azure-kubernetes-service-preview"></a>gitHub 操作& Azure 庫伯奈斯服務(預覽)

Azure 開發人員空間提供使用 GitHub 操作的工作流,允許您在拉取請求合併到儲存庫的主分支之前直接在 AKS 中測試從拉取請求中的更改。 擁有運行的應用程式來查看拉取請求的更改可以增強開發人員和團隊成員的信心。 此正在運行的應用程式還可以幫助團隊成員(如產品經理和設計人員)在開發的早期階段成為審核過程的一部分。

在本指南中，您將了解如何：

* 在 Azure 中於受控 Kubernetes 叢集上設定 Azure Dev Spaces。
* 將具有多個微服務的大型應用程式部署至開發人員空間。
* 使用 GitHub 操作設置 CI/CD。
* 在獨立的開發人員空間中以完整應用程式的內容測試單一微服務。

> [!IMPORTANT]
> 此功能目前為預覽狀態。 若您同意[補充的使用規定](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)，即可取得預覽。 在公開上市 (GA) 之前，此功能的某些領域可能會變更。

## <a name="prerequisites"></a>Prerequisites

* Azure 訂用帳戶。 如果您沒有 Azure 訂用帳戶，您可以建立[免費帳戶](https://azure.microsoft.com/free)。
* [已安裝 Azure CLI][azure-cli-installed]。
* [已安裝 Helm 3][helm-installed]。
* [啟用 GitHub 操作的][github-actions-beta-signup]GitHub 帳戶。
* 在 AKS 叢集上執行的[Azure 開發人員空間的共享範例應用程式](https://github.com/Azure/dev-spaces/tree/master/samples/BikeSharingApp/README.md)。

## <a name="create-an-azure-container-registry"></a>建立 Azure Container Registry

建立 Azure Container Registry (ACR)：

```azurecli
az acr create --resource-group MyResourceGroup --name <acrName> --sku Basic
```

> [!IMPORTANT]
> ACR 的名稱在 Azure 中必須是唯一的,並且包含 5-50 個字母數位字元。 您使用的任何字母都必須為小寫字母。

從輸出中保存*登錄伺服器*值,因為它在後面的步驟中使用。

## <a name="create-a-service-principal-for-authentication"></a>建立用於認證的服務主體

使用[az ad sp 創建為 rbac][az-ad-sp-create-for-rbac]創建服務主體。 例如：

```azurecli
az ad sp create-for-rbac --sdk-auth --skip-assignment
```

保存 JSON 輸出,因為它在後面的步驟中使用。

使用[az aks 顯示][az-aks-show]AKS 群組的*ID:*

```azurecli
az aks show -g MyResourceGroup -n MyAKS  --query id
```

使用[az acr 顯示][az-acr-show]顯示 ACR 的*識別碼:*

```azurecli
az acr show --name <acrName> --query id
```

使用[az 角色分配創建][az-role-assignment-create]向*參與者*授予對 AKS 群集的訪問許可權,並*授予 AcrPush*對 ACR 的訪問許可權。

```azurecli
az role assignment create --assignee <ClientId> --scope <AKSId> --role Contributor
az role assignment create --assignee <ClientId>  --scope <ACRId> --role AcrPush
```

> [!IMPORTANT]
> 您必須同時擁有 AKS 群集和 ACR,才能授予服務主體對這些資源的訪問許可權。

## <a name="configure-your-github-action"></a>設定 GitHub 作業

> [!IMPORTANT]
> 您必須為儲存庫啟用 GitHub 操作。 要為儲存庫啟用 GitHub 操作,請在 GitHub 上導航到儲存庫,按下「操作」選項卡,然後選擇為此存儲庫啟用操作。

導航到分叉存儲庫,然後單擊 *"設置*"。 點選左邊列中*的祕密*。 點選「*新增新機密*」 以新增以下每個新機密:

1. *AZURE_CREDENTIALS*: 服務主體創建的整個輸出。
1. *RESOURCE_GROUP*: AKS 叢集的資源群組,在此範例中為*MyResourceGroup*。
1. *CLUSTER_NAME*: AKS 叢集名稱,在此範例中為*MyAKS*。
1. *CONTAINER_REGISTRY*: ACR 的*登入伺服器*。
1. *HOST*: 開發空間的主機,它採用*的形式<MASTER_SPACE>.<APP_NAME>.<HOST_SUFFIX>,**在此示例中dev.bikesharingweb.fedcab0987.eus.azds.io*。
1. *IMAGE_PULL_SECRET:* 你想使用的秘密的名稱,例如*展示機密*。
1. *MASTER_SPACE*: 父開發人員空間的名稱,在此範例中是*dev*.
1. *REGISTRY_USERNAME*: 從服務主體創建的 JSON 輸出的*用戶端 Id。*
1. *REGISTRY_PASSWORD*: 來自服務主體建立的 JSON 輸出的*客戶端機密*。

> [!NOTE]
> 所有這些機密都由 GitHub 操作使用,並在[.github/工作流/bikes.yml][github-action-yaml]中配置。

或者,如果要在 PR 合併後更新主空間,請添加*GATEWAY_HOST*機密,該密名採用 *<MASTER_SPACE>.gateway.<HOST_SUFFIX>,**在此示例中dev.gateway.fedcab0987.eus.azds.io*。 將更改合併到分叉中的主分支後,將運行另一個操作,以在主開發空間中重新生成和運行整個應用程式。 這個功能, 主空間是*開發*。 此操作在[.github/工作流/自行車共用.yml][github-action-bikesharing-yaml]中配置。

此外,如果您希望 PR 中的更改在孫級空間中運行,請更新*MASTER_SPACE*和*HOST*機密。 例如,如果應用程式在*開發*中運行,使用子空間*開發/azureuser1,* 則讓 PR 在*dev/azureuser1*的子空間中執行:

* 將*MASTER_SPACE*更新為父空間所需的子空間,在此範例中*為 azureuser1*。
* 在本*範例中,* 將*HOST*更新為 GRANDPARENT_SPACE *<>.<APP_NAME>.<HOST_SUFFIX>,dev.bikesharingweb.fedcab0987.eus.azds.io *。

## <a name="create-a-new-branch-for-code-changes"></a>變更新分支

導航到`BikeSharingApp/`並創建一個名為*自行車圖像*的新分支。

```cmd
cd dev-spaces/samples/BikeSharingApp/
git checkout -b bike-images
```

編輯[自行車/伺服器.js][bikes-server-js]刪除行 232 與 233:

```javascript
    // Hard code image url *FIX ME*
    theBike.imageUrl = "/static/logo.svg";
```

該部分現在應如下所示:

```javascript
    var theBike = result;
    theBike.id = theBike._id;
    delete theBike._id;
```

保存檔,然後使用`git add``git commit`並暫存更改。

```cmd
git add Bikes/server.js 
git commit -m "Removing hard coded imageUrl from /bikes/:id route"
```

## <a name="push-your-changes"></a>推送變更

將`git push`新分支推送到分叉儲存函式庫:

```cmd
git push origin bike-images
```

推送完成後,導航到 GitHub 上的分叉儲存庫,以與*自行車映射*分支相比,將分叉存儲庫中的*主*分支作為基本分支創建拉取請求。

開啟拉取請求後,導航到 *"操作"* 選項卡。驗證新操作已啟動並正在構建*自行車*服務。

## <a name="view-the-child-space-with-your-changes"></a>使用變更檢視子空間

操作完成後,您將看到一個註釋,該註釋包含指向新子空間的 URL,該註釋基於拉取請求中的更改。

> [!div class="mx-imgBorder"]
> ![GitHub 操作 URL](../media/github-actions/github-action-url.png)

通過從註釋中打開 URL 導航到*自行車共用 Web*服務。 選擇*奧雷利亞布裡格斯(客戶)* 作為用戶,然後選擇自行車出租。 驗證您不再看到自行車的占位符圖像。

如果將更改合併到分叉中的*主*分支中,則將運行另一個操作,以在父開發空間中重新生成和運行整個應用程式。 這個功能, 此表示此一個範例, 父空間*是開發*。 此操作在[.github/工作流/自行車共用.yml][github-action-bikesharing-yaml]中配置。

## <a name="clean-up-your-azure-resources"></a>清除 Azure 資源

```azurecli
az group delete --name MyResourceGroup --yes --no-wait
```

## <a name="next-steps"></a>後續步驟

了解 Azure Dev Spaces 如何協助您跨多個容器開發更複雜的應用程式，以及如何藉由在不同的空間中使用不同的程式碼版本或分支，來簡化共同開發。

> [!div class="nextstepaction"]
> [在 Azure Dev Spaces 中進行小組開發][team-quickstart]

[azure-cli-installed]: /cli/azure/install-azure-cli?view=azure-cli-latest
[az-ad-sp-create-for-rbac]: /cli/azure/ad/sp#az-ad-sp-create-for-rbac
[az-acr-show]: /cli/azure/acr#az-acr-show
[az-aks-show]: /cli/azure/aks?view=azure-cli-latest#az-aks-show
[az-role-assignment-create]: /cli/azure/role/assignment#az-role-assignment-create
[bikes-server-js]: https://github.com/Azure/dev-spaces/blob/master/samples/BikeSharingApp/Bikes/server.js#L232-L233
[bike-sharing-gh]: https://github.com/Azure/dev-spaces/
[bike-sharing-values-yaml]: https://github.com/Azure/dev-spaces/blob/master/samples/BikeSharingApp/charts/values.yaml
[github-actions-beta-signup]: https://github.com/features/actions
[github-action-yaml]: https://github.com/Azure/dev-spaces/blob/master/.github/workflows/bikes.yml
[github-action-bikesharing-yaml]: https://github.com/Azure/dev-spaces/blob/master/.github/workflows/bikesharing.yml
[helm-installed]: https://helm.sh/docs/intro/install/
[supported-regions]: https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service
[sp-acr]: ../../container-registry/container-registry-auth-service-principal.md
[sp-aks]: ../../aks/kubernetes-service-principal.md
[team-quickstart]: ../quickstart-team-development.md
