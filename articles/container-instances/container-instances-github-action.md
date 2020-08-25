---
title: 依 GitHub 動作部署容器實例
description: 設定 GitHub 動作，以自動化建立、推送及部署容器映射至 Azure 容器實例的步驟
ms.topic: article
ms.date: 08/20/2020
ms.custom: ''
ms.openlocfilehash: 8da72d3911797e8e3a4551f2af100afb0d7ea0fb
ms.sourcegitcommit: afa1411c3fb2084cccc4262860aab4f0b5c994ef
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/23/2020
ms.locfileid: "88755002"
---
# <a name="configure-a-github-action-to-create-a-container-instance"></a>設定 GitHub 動作以建立容器執行個體

[GitHub Actions](https://help.github.com/actions/getting-started-with-github-actions/about-github-actions) 是 GitHub 中的一組功能，可讓您在儲存程式碼及共同處理提取要求和問題的相同位置，自動化軟體發展工作流程。

使用 [ [部署至 Azure 容器實例](https://github.com/azure/aci-deploy) ] GitHub 動作，將單一容器的部署自動化至 Azure 容器實例。 此動作可讓您設定容器實例的屬性，類似于 [az container create][az-container-create] 命令中的內容。

本文說明如何在 GitHub 存放庫中設定工作流程，以執行下列動作：

* 從 Dockerfile 建置映像
* 將映射推送至 Azure container registry
* 將容器映射部署至 Azure 容器實例

本文說明設定工作流程的兩種方式：

* [設定 github 工作流程](#configure-github-workflow) -使用 [部署至 Azure 容器實例] 動作和其他動作，在 GitHub 存放庫中建立工作流程。  
* [使用 CLI 擴充](#use-deploy-to-azure-extension) 功能-在 `az container app up` Azure CLI 中使用 [ [部署至 Azure](https://github.com/Azure/deploy-to-azure-cli-extension) ] 延伸模組中的命令。 此命令可簡化 GitHub 工作流程的建立和部署步驟。

> [!IMPORTANT]
> Azure 容器實例的 GitHub 動作目前為預覽狀態。 若您同意[補充的使用規定][terms-of-use]即可取得預覽。 在公開上市 (GA) 之前，此功能的某些領域可能會變更。

## <a name="prerequisites"></a>Prerequisites

* **GitHub 帳戶** -如果您還沒有帳戶，請建立一個帳戶 https://github.com 。
* **Azure CLI** -您可以使用 Azure CLI 的 Azure Cloud Shell 或本機安裝來完成 Azure CLI 步驟。 如果您需要安裝或升級，請參閱[安裝 Azure CLI][azure-cli-install]。
* **Azure container registry** -如果您沒有，請使用 [Azure CLI](../container-registry/container-registry-get-started-azure-cli.md)、 [Azure 入口網站](../container-registry/container-registry-get-started-portal.md)或其他方法，在基本層中建立 azure container registry。 記下用於部署的資源群組，用於 GitHub 工作流程。

## <a name="set-up-repo"></a>設定存放庫

* 針對本文中的範例，請使用 GitHub 來派生下列存放庫： https://github.com/Azure-Samples/acr-build-helloworld-node

  此存放庫包含 Dockerfile 和原始程式檔，可建立小型 web 應用程式的容器映射。

  ![GitHub 中的 [分支] 按鈕 (醒目提示) 的螢幕擷取畫面](../container-registry/media/container-registry-tutorial-quick-build/quick-build-01-fork.png)

* 確定已為您的存放庫啟用動作。 流覽至您的分支存放庫，並選取 [**設定**  >  **動作**]。 在 [ **動作許可權**] 中，確定已選取 [ **啟用此存放庫的本機和協力廠商動作** ]。

## <a name="configure-github-workflow"></a>設定 GitHub 工作流程

### <a name="create-service-principal-for-azure-authentication"></a>建立 Azure 驗證的服務主體

在 GitHub 工作流程中，您必須提供 Azure 認證以驗證 Azure CLI。 下列範例會建立服務主體，並將參與者角色的範圍設定為容器登錄的資源群組。

首先，取得資源群組的資源識別碼。 在下列 [az group show][az-group-show] 命令中替換您的組名：

```azurecli
groupId=$(az group show \
  --name <resource-group-name> \
  --query id --output tsv)
```

使用 [az ad sp create-rbac][az-ad-sp-create-for-rbac] 來建立服務主體：

```azurecli
az ad sp create-for-rbac \
  --scope $groupId \
  --role Contributor \
  --sdk-auth
```

輸出會類似：

```json
{
  "clientId": "xxxx6ddc-xxxx-xxxx-xxx-ef78a99dxxxx",
  "clientSecret": "xxxx79dc-xxxx-xxxx-xxxx-aaaaaec5xxxx",
  "subscriptionId": "xxxx251c-xxxx-xxxx-xxxx-bf99a306xxxx",
  "tenantId": "xxxx88bf-xxxx-xxxx-xxxx-2d7cd011xxxx",
  "activeDirectoryEndpointUrl": "https://login.microsoftonline.com",
  "resourceManagerEndpointUrl": "https://management.azure.com/",
  "activeDirectoryGraphResourceId": "https://graph.windows.net/",
  "sqlManagementEndpointUrl": "https://management.core.windows.net:8443/",
  "galleryEndpointUrl": "https://gallery.azure.com/",
  "managementEndpointUrl": "https://management.core.windows.net/"
}
```

儲存 JSON 輸出，因為它會在稍後的步驟中使用。 此外，請記 `clientId` 下您在下一節中需要更新服務主體的。

### <a name="update-service-principal-for-registry-authentication"></a>更新登錄驗證的服務主體

更新 Azure 服務主體認證，以允許對容器登錄的推送和提取存取權。 此步驟可讓 GitHub 工作流程使用服務主體來 [驗證您的容器](../container-registry/container-registry-auth-service-principal.md) 登錄，以及推送和提取 Docker 映射。 

取得容器登錄的資源識別碼。 在下列 [az acr show][az-acr-show] 命令中，以您的登錄名稱取代：

```azurecli
registryId=$(az acr show \
  --name <registry-name> \
  --query id --output tsv)
```

使用 [az role assign create][az-role-assignment-create] 來指派 AcrPush 角色，以提供登錄的推送和提取存取權。 以您的服務主體的用戶端識別碼取代：

```azurecli
az role assignment create \
  --assignee <ClientId> \
  --scope $registryId \
  --role AcrPush
```

### <a name="save-credentials-to-github-repo"></a>將認證儲存到 GitHub 存放庫

1. 在 GitHub UI 中，流覽至您的分支存放庫，並選取 [**設定**  >  **秘密**]。 

1. 選取 [ **新增密碼** ] 以新增下列秘密：

|祕密  |值  |
|---------|---------|
|`AZURE_CREDENTIALS`     | 來自服務主體建立步驟的整個 JSON 輸出 |
|`REGISTRY_LOGIN_SERVER`   | 登錄的登入伺服器名稱 (全部小寫) 。 範例： *myregistry.azurecr.io*        |
|`REGISTRY_USERNAME`     |  `clientId`從服務主體建立的 JSON 輸出中的。       |
|`REGISTRY_PASSWORD`     |  `clientSecret`從服務主體建立的 JSON 輸出中的。 |
| `RESOURCE_GROUP` | 您用來界定服務主體範圍的資源組名 |

### <a name="create-workflow-file"></a>建立工作流程檔案

1. 在 GitHub UI 中，選取 [**動作**  >  **新增工作流程**]。
1. 選取 [ **自行設定工作流程**]。
1. 在 [ **編輯新**檔案] 中，貼上下列 YAML 內容以覆寫範例程式碼。 接受預設檔案名 `main.yml` ，或提供您選擇的檔案名。
1. 選取 [ **開始認可**]，選擇性地提供認可的簡短和延伸描述，然後選取 [ **認可新**檔案]。

```yml
on: [push]
name: Linux_Container_Workflow

jobs:
    build-and-deploy:
        runs-on: ubuntu-latest
        steps:
        # checkout the repo
        - name: 'Checkout GitHub Action'
          uses: actions/checkout@master
          
        - name: 'Login via Azure CLI'
          uses: azure/login@v1
          with:
            creds: ${{ secrets.AZURE_CREDENTIALS }}
        
        - name: 'Build and push image'
          uses: azure/docker-login@v1
          with:
            login-server: ${{ secrets.REGISTRY_LOGIN_SERVER }}
            username: ${{ secrets.REGISTRY_USERNAME }}
            password: ${{ secrets.REGISTRY_PASSWORD }}
        - run: |
            docker build . -t ${{ secrets.REGISTRY_LOGIN_SERVER }}/sampleapp:${{ github.sha }}
            docker push ${{ secrets.REGISTRY_LOGIN_SERVER }}/sampleapp:${{ github.sha }}

        - name: 'Deploy to Azure Container Instances'
          uses: 'azure/aci-deploy@v1'
          with:
            resource-group: ${{ secrets.RESOURCE_GROUP }}
            dns-name-label: ${{ secrets.RESOURCE_GROUP }}${{ github.run_number }}
            image: ${{ secrets.REGISTRY_LOGIN_SERVER }}/sampleapp:${{ github.sha }}
            registry-login-server: ${{ secrets.REGISTRY_LOGIN_SERVER }}
            registry-username: ${{ secrets.REGISTRY_USERNAME }}
            registry-password: ${{ secrets.REGISTRY_PASSWORD }}
            name: aci-sampleapp
            location: 'west us'
```

### <a name="validate-workflow"></a>驗證工作流程

認可工作流程檔案之後，就會觸發工作流程。 若要查看工作流程進度，請流覽至 [**動作**  >  **工作流程**]。 

![查看工作流程進度](./media/container-instances-github-action/github-action-progress.png)

如需有關在您的工作流程中查看每個步驟的狀態和結果的詳細資訊，請參閱 [管理工作流程執行](https://help.github.com/actions/configuring-and-managing-workflows/managing-a-workflow-run) 。 如果工作流程未完成，請參閱 [查看記錄以診斷失敗](https://docs.github.com/actions/configuring-and-managing-workflows/managing-a-workflow-run#viewing-logs-to-diagnose-failures)。

當工作流程順利完成時，請執行[az container show][az-container-show]命令以取得名為*aci-sampleapp*的容器實例的相關資訊。 以您的資源組名取代： 

```azurecli
az container show \
  --resource-group <resource-group-name> \
  --name aci-sampleapp \
  --query "{FQDN:ipAddress.fqdn,ProvisioningState:provisioningState}" \
  --output table
```

輸出會類似：

```console
FQDN                                   ProvisioningState
---------------------------------      -------------------
aci-action01.westus.azurecontainer.io  Succeeded
```

布建實例之後，請在瀏覽器中流覽至容器的 FQDN，以查看正在執行的 web 應用程式。

![在瀏覽器中執行 web 應用程式](./media/container-instances-github-action/github-action-container.png)

## <a name="use-deploy-to-azure-extension"></a>使用部署至 Azure 擴充功能

或者，您也可以使用 Azure CLI 中的 [ [部署至 Azure] 延伸](https://github.com/Azure/deploy-to-azure-cli-extension) 模組來設定工作流程。 `az container app up`延伸模組中的命令會從您取得輸入參數，以設定要部署至 Azure 容器實例的工作流程。 

Azure CLI 所建立的工作流程，類似于您可以 [使用 GitHub 手動建立](#configure-github-workflow)的工作流程。

### <a name="additional-prerequisite"></a>其他先決條件

除了此案例的 [必要條件](#prerequisites) 和存放庫 [設定](#set-up-repo) 以外，您還需要安裝 Azure CLI 的 [  **部署至 Azure] 延伸** 模組。

執行 [az extension add][az-extension-add] 命令以安裝延伸模組：

```azurecli
az extension add \
  --name deploy-to-azure
```

如需尋找、安裝和管理延伸模組的相關資訊，請參閱搭配 [使用擴充功能與 Azure CLI](/cli/azure/azure-cli-extensions-overview)。

### <a name="run-az-container-app-up"></a>`az container app up`執行

若要執行 [az container app up][az-container-app-up] 命令，請至少提供：

* 您的 Azure container registry 名稱，例如， *myregistry*
* 您 GitHub 存放庫的 URL，例如 `https://github.com/<your-GitHub-Id>/acr-build-helloworld-node`

範例命令：

```azurecli
az container app up \
  --acr myregistry \
  --repository https://github.com/myID/acr-build-helloworld-node
```

### <a name="command-progress"></a>命令進度

* 出現提示時，請提供您的 GitHub 認證，或提供[github 個人存取權杖](https://help.github.com/github/authenticating-to-github/creating-a-personal-access-token-for-the-command-line) (PAT) ，其中具有存放庫和*使用者**範圍，可*使用您的 GitHub 帳戶進行驗證。 如果您提供 GitHub 認證，此命令會為您建立 PAT。 遵循其他提示來設定工作流程。

* 此命令會建立工作流程的存放庫秘密：

  * Azure CLI 的服務主體認證
  * 用來存取 Azure container registry 的認證

* 命令將工作流程檔案認可至您的存放庫之後，就會觸發工作流程。 

輸出會類似：

```console
[...]
Checking in file github/workflows/main.yml in the Github repository myid/acr-build-helloworld-node
Creating workflow...
GitHub Action Workflow has been created - https://github.com/myid/acr-build-helloworld-node/runs/515192398
GitHub workflow completed.
Workflow succeeded
Your app is deployed at:  http://acr-build-helloworld-node.eastus.azurecontainer.io:8080/
```

若要在 GitHub UI 中查看每個步驟的工作流程狀態和結果，請參閱 [管理工作流程執行](https://help.github.com/actions/configuring-and-managing-workflows/managing-a-workflow-run)。

### <a name="validate-workflow"></a>驗證工作流程

工作流程會使用 GitHub 存放庫的基底名稱（在此案例中為 *helloworld-node*）來部署 Azure 容器實例。 當工作流程順利完成時，執行[az container show][az-container-show]命令以取得名為*acr-helloworld-node*的容器實例的相關資訊。 以您的資源組名取代： 

```azurecli
az container show \
  --resource-group <resource-group-name> \
  --name acr-build-helloworld-node \
  --query "{FQDN:ipAddress.fqdn,ProvisioningState:provisioningState}" \
  --output table
```

輸出會類似：

```console
FQDN                                                   ProvisioningState
---------------------------------                      -------------------
acr-build-helloworld-node.westus.azurecontainer.io     Succeeded
```

布建實例之後，請在瀏覽器中流覽至容器的 FQDN，以查看正在執行的 web 應用程式。

## <a name="clean-up-resources"></a>清除資源

使用 [az container delete][az-container-delete] 命令停止容器執行個體：

```azurecli
az container delete \
  --name <instance-name>
  --resource-group <resource-group-name>
```

若要刪除資源群組和其中的所有資源，請執行 [az group delete][az-group-delete] 命令：

```azurecli
az group delete \
  --name <resource-group-name>
```

## <a name="next-steps"></a>後續步驟

流覽 [GitHub Marketplace](https://github.com/marketplace?type=actions) 以取得自動化開發工作流程的其他動作


<!-- LINKS - external -->
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/

<!-- LINKS - internal -->

[azure-cli-install]: /cli/azure/install-azure-cli
[az-group-show]: /cli/azure/group#az-group-show
[az-group-delete]: /cli/azure/group#az-group-delete
[az-ad-sp-create-for-rbac]: /cli/azure/ad/sp#az-ad-sp-create-for-rbac
[az-role-assignment-create]: /cli/azure/role/assignment#az-role-assignment-create
[az-container-create]: /cli/azure/container#az-container-create
[az-acr-show]: /cli/azure/acr#az-acr-show
[az-container-show]: /cli/azure/container#az-container-show
[az-container-delete]: /cli/azure/container#az-container-delete
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-container-app-up]: /cli/azure/ext/deploy-to-azure/container/app#ext-deploy-to-azure-az-container-app-up
