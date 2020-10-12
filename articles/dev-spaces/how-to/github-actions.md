---
title: 'GitHub Actions & Azure Kubernetes Service (preview) '
services: azure-dev-spaces
ms.date: 04/03/2020
ms.topic: conceptual
description: 使用 GitHub Actions 和 Azure Dev Spaces，直接在 Azure Kubernetes Service 中檢查並測試提取要求的變更
keywords: Docker、Kubernetes、Azure、AKS、Azure Kubernetes Service、容器、GitHub Actions、Helm、服務網格、服務網格路由、kubectl、k8s
manager: gwallace
ms.custom: devx-track-js
ms.openlocfilehash: 25cb0de10e83069514c7918a5a2cdac019fe1ebb
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "91316098"
---
# <a name="github-actions--azure-kubernetes-service-preview"></a>GitHub Actions & Azure Kubernetes Service (preview) 

Azure Dev Spaces 提供使用 GitHub Actions 的工作流程，可讓您直接在 AKS 中測試提取要求的變更，再將提取要求合併到存放庫的主要分支。 擁有執行中的應用程式來審核提取要求的變更，可以提高開發人員和小組成員的信賴度。 這個執行中的應用程式也可以協助小組成員（像是產品經理和設計人員）在開發初期的階段，成為審核流程的一部分。

在本指南中，您將了解如何：

* 在 Azure 中於受控 Kubernetes 叢集上設定 Azure Dev Spaces。
* 將具有多個微服務的大型應用程式部署至開發人員空間。
* 使用 GitHub 動作設定 CI/CD。
* 在獨立的開發人員空間中以完整應用程式的內容測試單一微服務。

> [!IMPORTANT]
> 此功能目前為預覽狀態。 若您同意[補充的使用規定](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)即可取得預覽。 在公開上市 (GA) 之前，此功能的某些領域可能會變更。

## <a name="prerequisites"></a>必要條件

* Azure 訂用帳戶。 如果您沒有 Azure 訂用帳戶，您可以建立[免費帳戶](https://azure.microsoft.com/free)。
* [已安裝 Azure CLI][azure-cli-installed]。
* [已安裝 Helm 3][helm-installed]。
* [已啟用 GitHub Actions][github-actions-beta-signup]的 GitHub 帳戶。
* 在 AKS 叢集上執行的 [Azure Dev Spaces 自行車共用範例應用程式](https://github.com/Azure/dev-spaces/tree/master/samples/BikeSharingApp/README.md) 。

## <a name="create-an-azure-container-registry"></a>建立 Azure Container Registry

建立 Azure Container Registry (ACR)：

```azurecli
az acr create --resource-group MyResourceGroup --name <acrName> --sku Basic
```

> [!IMPORTANT]
> 您的 ACR 名稱在 Azure 中必須是唯一的，並且包含5-50 個英數位元。 您使用的任何字母都必須是小寫。

從輸出儲存 *loginServer* 值，因為它會在稍後的步驟中使用。

## <a name="create-a-service-principal-for-authentication"></a>建立驗證的服務主體

使用 [az ad sp create-rbac][az-ad-sp-create-for-rbac] 來建立服務主體。 例如：

```azurecli
az ad sp create-for-rbac --sdk-auth --skip-assignment
```

儲存 JSON 輸出，因為它會在稍後的步驟中使用。

使用 [az aks show][az-aks-show] 來顯示 aks 叢集的 *識別碼* ：

```azurecli
az aks show -g MyResourceGroup -n MyAKS  --query id
```

使用 [az acr show][az-acr-show] 來顯示 Acr 的 *識別碼* ：

```azurecli
az acr show --name <acrName> --query id
```

使用 [az role 指派 create][az-role-assignment-create] 來授與 *參與者* 對您 AKS 叢集的存取權，以及 *AcrPush* 您 ACR 的存取權。

```azurecli
az role assignment create --assignee <ClientId> --scope <AKSId> --role Contributor
az role assignment create --assignee <ClientId>  --scope <ACRId> --role AcrPush
```

> [!IMPORTANT]
> 您必須是 AKS 叢集和 ACR 的擁有者，才能將這些資源的存取權授與您的服務主體。

## <a name="configure-your-github-action"></a>設定您的 GitHub 動作

> [!IMPORTANT]
> 您必須為您的存放庫啟用 GitHub Actions。 若要為您的存放庫啟用 GitHub Actions，請流覽至 GitHub 上的存放庫，按一下 [動作] 索引標籤，然後選擇啟用此存放庫的動作。

流覽至您的分支存放庫，然後按一下 [ *設定*]。 按一下左側提要欄位中的 [ *秘密* ]。 按一下 [新增 *密碼* ]，在下面新增每個新密碼：

1. *AZURE_CREDENTIALS*：服務主體建立的完整輸出。
1. *RESOURCE_GROUP*： AKS 叢集的資源群組，在此範例中為 *MyResourceGroup*。
1. *CLUSTER_NAME*： AKS 叢集的名稱，在此範例中為 *MyAKS*。
1. *CONTAINER_REGISTRY*： ACR 的 *loginServer* 。
1. *Host*：適用于您的開發空間的主機，其採用 *<MASTER_SPACE> 的格式。 <APP_NAME *> <HOST_SUFFIX，在此範例中為 *dev.bikesharingweb.fedcab0987.eus.azds.io*。
1. *IMAGE_PULL_SECRET*：您想要使用的秘密名稱，例如 *示範秘密*。
1. *MASTER_SPACE*：父開發人員空間的名稱，在此範例中為 *dev*。
1. *REGISTRY_USERNAME*：從服務主體建立的 JSON 輸出中的 *clientId* 。
1. *REGISTRY_PASSWORD*：從服務主體建立的 JSON 輸出 *clientSecret* 。

> [!NOTE]
> 所有這些秘密都是由 GitHub 動作使用，並設定于 [github/workflow/自行車. yml][github-action-yaml]中。

（選擇性）如果您想要在合併 PR 之後更新主要空間，請新增 *GATEWAY_HOST* 的秘密，這會採用 *<MASTER_SPACE> *的格式。 <HOST_SUFFIX>，在此範例中為 *dev.gateway.fedcab0987.eus.azds.io*。 當您將變更合併至分支中的主要分支之後，會執行另一個動作，以在主要開發人員空間中重建並執行整個應用程式。 在此範例中，主要空間為 *dev*。 此動作設定于 [github/workflow/>bikesharing.clients.windows. yml][github-action-bikesharing-yaml]中。

此外，如果您想要在孫空間中執行 PR 中的變更，請更新 *MASTER_SPACE* 並 *裝載* 秘密。 例如，如果您的應用程式在具有子空間*開發/>azureuser1*的*開發環境*中執行，則會在*dev/>azureuser1*的子空間中執行 PR：

* 將 *MASTER_SPACE* 更新為您想要作為父空間的子空間，在此範例中為 *>azureuser1*。
* 將 *主機* 更新為 *<GRANDPARENT_SPACE>。 <APP_NAME *> <HOST_SUFFIX>，在此範例中 *dev.bikesharingweb.fedcab0987.eus.azds.io*。

## <a name="create-a-new-branch-for-code-changes"></a>建立新的分支以進行程式碼變更

流覽 `BikeSharingApp/` 並建立新的分支，稱為 *自行車影像*。

```cmd
cd dev-spaces/samples/BikeSharingApp/
git checkout -b bike-images
```

編輯 [自行車/server.js][bikes-server-js] 以移除行232和233：

```javascript
    // Hard code image url *FIX ME*
    theBike.imageUrl = "/static/logo.svg";
```

區段現在看起來應該像這樣：

```javascript
    var theBike = result;
    theBike.id = theBike._id;
    delete theBike._id;
```

儲存檔案，然後使用 `git add` 和 `git commit` 來暫存您的變更。

```cmd
git add Bikes/server.js 
git commit -m "Removing hard coded imageUrl from /bikes/:id route"
```

## <a name="push-your-changes"></a>推送您的變更

使用 `git push` 將新分支推送至您的分支存放庫：

```cmd
git push origin bike-images
```

推送完成之後，請流覽至 GitHub 上的分支存放庫，在您的分支存放庫中建立含有 *主要* 分支的提取要求，以作為與 *自行車映射* 分支相較的基底分支。

開啟您的提取要求之後，請流覽至 [ *動作* ] 索引標籤。確認已開始新的動作，並且正在建立 *自行車* 服務。

## <a name="view-the-child-space-with-your-changes"></a>使用您的變更來查看子空間

動作完成後，您會看到批註，其中包含以提取要求中的變更為基礎的新子空間 URL。

> [!div class="mx-imgBorder"]
> ![GitHub 動作 Url](../media/github-actions/github-action-url.png)

開啟批註中的 URL，以流覽至 *>bikesharingweb* 服務。 選取 *Aurelia Briggs (客戶) * 作為使用者，然後選取要租借的自行車。 確認您不再看到自行車的預留位置影像。

如果您將變更合併至分支中的 *主要* 分支，則會執行另一個動作，以在父開發空間中重建並執行整個應用程式。 在此範例中，父系空間為 *dev*。 此動作設定于 [github/workflow/>bikesharing.clients.windows. yml][github-action-bikesharing-yaml]中。

## <a name="clean-up-your-azure-resources"></a>清除 Azure 資源

```azurecli
az group delete --name MyResourceGroup --yes --no-wait
```

## <a name="next-steps"></a>後續步驟

深入了解 Azure Dev Spaces 的運作方式。

> [!div class="nextstepaction"]
> [Azure Dev Spaces 如何運作](../how-dev-spaces-works.md)

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
