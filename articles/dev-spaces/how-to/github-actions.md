---
title: GitHub 動作 & Azure Kubernetes Service （預覽）
services: azure-dev-spaces
ms.date: 04/03/2020
ms.topic: conceptual
description: 使用 GitHub 動作和 Azure Dev Spaces，直接在 Azure Kubernetes Service 中檢查並測試提取要求的變更
keywords: Docker，Kubernetes，Azure，AKS，Azure Kubernetes Service，容器，GitHub 動作，Helm，服務網格，服務網格路由，kubectl，k8s
manager: gwallace
ms.openlocfilehash: a83da0ef3958748831eb0eeda1aa5e91efa7ef2e
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "80637936"
---
# <a name="github-actions--azure-kubernetes-service-preview"></a>GitHub 動作 & Azure Kubernetes Service （預覽）

Azure Dev Spaces 提供使用 GitHub 動作的工作流程，可讓您在提取要求合併至存放庫的主要分支之前，直接在 AKS 中測試提取要求的變更。 擁有執行中的應用程式來審查提取要求的變更，可以提高開發人員和小組成員的信心。 這個執行中的應用程式也可以協助小組成員（例如，產品經理和設計人員）在開發初期階段成為審核程式的一部分。

在本指南中，您將了解如何：

* 在 Azure 中於受控 Kubernetes 叢集上設定 Azure Dev Spaces。
* 將具有多個微服務的大型應用程式部署至開發人員空間。
* 使用 GitHub 動作來設定 CI/CD。
* 在獨立的開發人員空間中以完整應用程式的內容測試單一微服務。

> [!IMPORTANT]
> 此功能目前為預覽狀態。 若您同意[補充的使用規定](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)即可取得預覽。 在公開上市 (GA) 之前，此功能的某些領域可能會變更。

## <a name="prerequisites"></a>必要條件

* Azure 訂用帳戶。 如果您沒有 Azure 訂用帳戶，您可以建立[免費帳戶](https://azure.microsoft.com/free)。
* [已安裝 Azure CLI][azure-cli-installed]。
* [已安裝 Helm 3][helm-installed]。
* [已啟用 Github 動作][github-actions-beta-signup]的 github 帳戶。
* 在 AKS 叢集上執行的[Azure Dev Spaces 自行車共用範例應用程式](https://github.com/Azure/dev-spaces/tree/master/samples/BikeSharingApp/README.md)。

## <a name="create-an-azure-container-registry"></a>建立 Azure Container Registry

建立 Azure Container Registry (ACR)：

```azurecli
az acr create --resource-group MyResourceGroup --name <acrName> --sku Basic
```

> [!IMPORTANT]
> 您的 ACR 在 Azure 內必須是唯一的名稱，而且包含5-50 個英數位元。 您使用的任何字母都必須是小寫。

儲存輸出中的*loginServer*值，因為在稍後的步驟中會用到它。

## <a name="create-a-service-principal-for-authentication"></a>建立用於驗證的服務主體

使用[az ad sp create for-rbac][az-ad-sp-create-for-rbac]來建立服務主體。 例如：

```azurecli
az ad sp create-for-rbac --sdk-auth --skip-assignment
```

儲存 JSON 輸出，因為在稍後的步驟中會用到它。

使用[az aks show][az-aks-show]顯示 aks 叢集的*識別碼*：

```azurecli
az aks show -g MyResourceGroup -n MyAKS  --query id
```

使用[az acr show][az-acr-show]顯示 Acr 的*識別碼*：

```azurecli
az acr show --name <acrName> --query id
```

使用[az role 指派 create][az-role-assignment-create]來授與 AKS 叢集的*參與者*存取權，以及*AcrPush*對您 ACR 的存取權。

```azurecli
az role assignment create --assignee <ClientId> --scope <AKSId> --role Contributor
az role assignment create --assignee <ClientId>  --scope <ACRId> --role AcrPush
```

> [!IMPORTANT]
> 您必須是 AKS 叢集和 ACR 的擁有者，才能讓您的服務主體存取這些資源。

## <a name="configure-your-github-action"></a>設定您的 GitHub 動作

> [!IMPORTANT]
> 您的存放庫必須啟用 GitHub 動作。 若要為您的存放庫啟用 GitHub 動作，請流覽至 GitHub 上的存放庫，按一下 [動作] 索引標籤，然後選擇啟用此存放庫的動作。

流覽至您的分支存放庫，然後按一下 [*設定*]。 按一下左側邊欄中的 [*秘密*]。 按一下 [新增*新密碼*]，在下方新增每個新密碼：

1. *AZURE_CREDENTIALS*：從服務主體建立的整個輸出。
1. *RESOURCE_GROUP*： AKS 叢集的資源群組，在此範例中為*MyResourceGroup*。
1. *CLUSTER_NAME*： AKS 叢集的名稱，在此範例中為*MyAKS*。
1. *CONTAINER_REGISTRY*： ACR 的*loginServer* 。
1. *主機*：您的開發人員空間的主機，其採用的格式 *<MASTER_SPACE>。 <APP_NAME>。 <* HOST_SUFFIX>，在此範例中為*dev.bikesharingweb.fedcab0987.eus.azds.io*。
1. *IMAGE_PULL_SECRET*：您想要使用的秘密名稱，例如「*示範密碼*」。
1. *MASTER_SPACE*：您的父系開發人員空間的名稱，在此範例中為*dev*。
1. *REGISTRY_USERNAME*：從服務主體建立的 JSON 輸出中的*clientId* 。
1. *REGISTRY_PASSWORD*：從服務主體建立的 JSON 輸出*clientSecret* 。

> [!NOTE]
> 所有這些秘密都是由 GitHub 動作使用，並設定于[github/workflow/自行車. yml][github-action-yaml]中。

（選擇性）如果您想要在合併 PR 之後更新主要空間，請新增*GATEWAY_HOST*秘密，其採用 *<MASTER_SPACE>. 閘道. <* HOST_SUFFIX>，在此範例中為*dev.gateway.fedcab0987.eus.azds.io*。 將變更合併到分叉中的主要分支之後，將會執行另一個動作，以在主要開發人員空間中重建並執行整個應用程式。 在此範例中，主要空間為*dev*。 此動作是在[github/workflow/bikesharing.clients.core. yml][github-action-bikesharing-yaml]中設定。

此外，如果您想要在孫空間中執行 PR 中的變更，請更新*MASTER_SPACE*和*主機*密碼。 例如，如果您的應用程式是在具有子空間*dev/azureuser1*的*dev*中執行，則會在*dev/azureuser1*的子空間中執行 PR：

* 將*MASTER_SPACE*更新為您想要作為父空間的子空間，在此範例中為*azureuser1*。
* 將*主機*更新為 *<GRANDPARENT_SPACE>。 <APP_NAME>。 <* HOST_SUFFIX>，在此範例中為*dev.bikesharingweb.fedcab0987.eus.azds.io*。

## <a name="create-a-new-branch-for-code-changes"></a>建立程式碼變更的新分支

流覽至 `BikeSharingApp/` ，並建立稱為*自行車-images*的新分支。

```cmd
cd dev-spaces/samples/BikeSharingApp/
git checkout -b bike-images
```

編輯[自行車/server.js][bikes-server-js]以移除232和233行：

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

使用 `git push` 將您的新分支推送至分叉存放庫：

```cmd
git push origin bike-images
```

推播完成後，流覽至 GitHub 上的分支存放庫，以在分支存放庫中建立具有*master*分支的提取要求，做為與*自行車影像*分支相較之下的基底分支。

開啟提取要求之後，流覽至 [*動作*] 索引標籤。確認已啟動新的動作，並正在建立*自行車*服務。

## <a name="view-the-child-space-with-your-changes"></a>使用您的變更來查看子空間

動作完成後，您會看到批註，其中包含以提取要求中的變更為基礎的新子空間 URL。

> [!div class="mx-imgBorder"]
> ![GitHub 動作 Url](../media/github-actions/github-action-url.png)

從批註開啟 URL，以流覽至*bikesharingweb*服務。 選取 [ *Aurelia Briggs （customer）* ] 作為使用者，然後選取要出租的自行車。 確認您不會再看到自行車的預留位置影像。

如果您將變更合併到分叉中的*主要*分支，則會執行另一個動作，以在父開發人員空間中重建並執行整個應用程式。 在此範例中，父空間為*dev*。 此動作是在[github/workflow/bikesharing.clients.core. yml][github-action-bikesharing-yaml]中設定。

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
