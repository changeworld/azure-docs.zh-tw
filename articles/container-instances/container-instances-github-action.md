---
title: 依 GitHub 動作部署容器實例
description: 設定 GitHub 動作，將建立、推送及部署容器映射的步驟自動化至 Azure 容器實例
ms.topic: article
ms.date: 03/18/2020
ms.custom: ''
ms.openlocfilehash: 13397cee8197afc65b93c587ae1505e59cfdebc1
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "80258034"
---
# <a name="configure-a-github-action-to-create-a-container-instance"></a>設定 GitHub 動作以建立容器實例

[Github 動作](https://help.github.com/actions/getting-started-with-github-actions/about-github-actions)是 github 中的一套功能，可將您的軟體發展工作流程自動化，並將您的程式碼儲存在同一個位置，並在提取要求和問題上共同作業。

使用[部署至 Azure 容器實例](https://github.com/azure/aci-deploy)GitHub 動作，自動將容器部署至 Azure 容器實例。 動作可讓您設定容器實例的屬性，其與[az container create][az-container-create]命令中的類似。

本文說明如何在 GitHub 存放庫中設定工作流程，以執行下列動作：

* 從 Dockerfile 建置映像
* 將映射推送至 Azure container registry
* 將容器映射部署至 Azure 容器實例

本文說明兩種設定工作流程的方式：

* 使用 [部署至 Azure 容器實例] 動作和其他動作，自行在 GitHub 存放庫中設定工作流程。  
* 在 Azure CLI `az container app up`中，使用 [[部署至 Azure](https://github.com/Azure/deploy-to-azure-cli-extension) ] 延伸模組中的命令。 此命令簡化了 GitHub 工作流程和部署步驟的建立。

> [!IMPORTANT]
> 適用于 Azure 容器實例的 GitHub 動作目前為預覽狀態。 若您同意[補充的使用規定][terms-of-use]即可取得預覽。 在公開上市 (GA) 之前，此功能的某些領域可能會變更。

## <a name="prerequisites"></a>Prerequisites

* **GitHub 帳戶**-如果您還沒有https://github.com帳戶，請在上建立帳戶。
* **Azure CLI** -您可以使用 Azure Cloud Shell 或本機安裝 Azure CLI 來完成 Azure CLI 步驟。 如果您需要安裝或升級，請參閱[安裝 Azure CLI][azure-cli-install]。
* **Azure container registry** -如果您沒有，請使用[Azure CLI](../container-registry/container-registry-get-started-azure-cli.md)、 [Azure 入口網站](../container-registry/container-registry-get-started-portal.md)或其他方法，在基本層中建立 azure container registry。 記下用於部署的資源群組，用於 GitHub 工作流程。

## <a name="set-up-repo"></a>設定存放庫

* 針對本文中的範例，請使用 GitHub 來派生下列存放庫：https://github.com/Azure-Samples/acr-build-helloworld-node

  此存放庫包含 Dockerfile 和來源檔案，可建立小型 web 應用程式的容器映射。

  ![GitHub 中的 [分支] 按鈕 (醒目提示) 的螢幕擷取畫面](../container-registry/media/container-registry-tutorial-quick-build/quick-build-01-fork.png)

* 請確定已為您的存放庫啟用動作。 流覽至您的分支存放庫，然後選取 [**設定** > ] [**動作**]。 在 [動作] [**許可權**] 中，確定已選取 [**啟用此存放庫的本機和協力廠商動作**]。

## <a name="configure-github-workflow"></a>設定 GitHub 工作流程

### <a name="create-service-principal-for-azure-authentication"></a>建立 Azure 驗證的服務主體

在 GitHub 工作流程中，您必須提供 Azure 認證以向 Azure CLI 進行驗證。 下列範例會建立一個服務主體，其中的參與者角色範圍設定為容器登錄的資源群組。

首先，取得資源群組的資源識別碼。 以您的群組名稱取代下列[az group show][az-acr-show]命令：

```azurecli
groupId=$(az group show \
  --name <resource-group-name> \
  --query id --output tsv)
```

使用[az ad sp create for-rbac][az-ad-sp-create-for-rbac]來建立服務主體：

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

儲存 JSON 輸出，因為在稍後的步驟中會用到它。 此外，請記下`clientId`，這是您在下一節中需要更新服務主體的。

### <a name="update-service-principal-for-registry-authentication"></a>更新登錄驗證的服務主體

更新 Azure 服務主體認證，以允許容器登錄上的推送和提取許可權。 此步驟可讓 GitHub 工作流程使用服務主體來向[您的容器登錄進行驗證](../container-registry/container-registry-auth-service-principal.md)。 

取得容器登錄的資源識別碼。 在下列[az acr show][az-acr-show]命令中，以您的登錄名稱取代：

```azurecli
registryId=$(az acr show \
  --name <registry-name> \
  --query id --output tsv)
```

使用[az role 指派 create][az-role-assignment-create]來指派 AcrPush 角色，這會提供登錄的推送和提取存取權。 取代服務主體的用戶端識別碼：

```azurecli
az role assignment create \
  --assignee <ClientId> \
  --scope $registryId \
  --role AcrPush
```

### <a name="save-credentials-to-github-repo"></a>將認證儲存至 GitHub 存放庫

1. 在 GitHub UI 中，流覽至您的分支存放庫，然後選取 [**設定** > ] [**秘密**]。 

1. 選取 [**新增密碼**]，以新增下列秘密：

|祕密  |值  |
|---------|---------|
|`AZURE_CREDENTIALS`     | 建立服務主體的整個 JSON 輸出 |
|`REGISTRY_LOGIN_SERVER`   | 登錄的登入伺服器名稱（全部小寫）。 範例： *myregistry.azure.cr.io*        |
|`REGISTRY_USERNAME`     |  從`clientId`服務主體建立的 JSON 輸出中的       |
|`REGISTRY_PASSWORD`     |  從`clientSecret`服務主體建立的 JSON 輸出中的 |
| `RESOURCE_GROUP` | 您用來界定服務主體範圍的資源組名 |

### <a name="create-workflow-file"></a>建立工作流程檔案

1. 在 GitHub UI 中，選取 [**動作** > ] [**新增工作流程**]。
1. 選取 [**自行設定工作流程**]。
1. 在 [**編輯新**檔案] 中，貼上下列 YAML 內容以覆寫範例程式碼。 接受預設檔案名`main.yml`，或提供您選擇的檔案名。
1. 選取 [**開始認可**]，選擇性地提供認可的簡短和擴充描述，然後選取 [**認可新**檔案]。

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

在您認可工作流程檔案之後，就會觸發工作流程。 若要查看工作流程進度，請流覽至 [**動作** > **工作流程**]。 

![查看工作流程進度](./media/container-instances-github-action/github-action-progress.png)

如需查看工作流程中每個步驟的狀態和結果的相關資訊，請參閱[管理工作流程執行](https://help.github.com/actions/configuring-and-managing-workflows/managing-a-workflow-run)。

當工作流程完成時，請執行[az container show][az-container-show]命令，以取得名為*aci sampleapp*之容器實例的相關資訊。 以您的資源組名取代： 

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

布建實例之後，在瀏覽器中流覽至容器的 FQDN，以查看執行中的 web 應用程式。

![在瀏覽器中執行 web 應用程式](./media/container-instances-github-action/github-action-container.png)

## <a name="use-deploy-to-azure-extension"></a>使用部署至 Azure 擴充功能

或者，使用 Azure CLI 中的 [[部署至 Azure] 延伸](https://github.com/Azure/deploy-to-azure-cli-extension)模組來設定工作流程。 延伸`az container app up`模組中的命令會從您取得輸入參數，以設定要部署至 Azure 容器實例的工作流程。 

Azure CLI 所建立的工作流程，與您可以[使用 GitHub 手動建立](#configure-github-workflow)的工作流程類似。

### <a name="additional-prerequisite"></a>其他先決條件

除了適用于此案例的[必要條件](#prerequisites)和存放庫[設定](#set-up-repo)之外，您還需要安裝適用于 Azure CLI 的 [**部署至 Azure] 延伸**模組。

執行[az extension add][az-extension-add]命令來安裝擴充功能：

```azurecli
az extension add \
  --name deploy-to-azure
```

如需尋找、安裝及管理擴充功能的相關資訊，請參閱搭配[使用擴充功能與 Azure CLI](/cli/azure/azure-cli-extensions-overview)。

### <a name="run-az-container-app-up"></a>`az container app up`執行

若要執行[az container app up][az-container-app-up]命令，請至少提供：

* Azure container registry 的名稱，例如*myregistry*
* GitHub 存放庫的 URL，例如`https://github.com/<your-GitHub-Id>/acr-build-helloworld-node`

範例命令：

```azurecli
az container app up \
  --acr myregistry \
  --repository https://github.com/myID/acr-build-helloworld-node
```

### <a name="command-progress"></a>命令進度

* 出現提示時，請提供您的 GitHub 認證，或提供[github 個人存取權杖](https://help.github.com/github/authenticating-to-github/creating-a-personal-access-token-for-the-command-line)（PAT *），其*具有存放庫和*使用者*範圍來向登錄進行驗證。 如果您提供 GitHub 認證，此命令會為您建立 PAT。

* 此命令會建立工作流程的存放庫密碼：

  * Azure CLI 的服務主體認證
  * 存取 Azure container registry 的認證

* 在命令將工作流程檔案認可至您的存放庫之後，就會觸發工作流程。 

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

### <a name="validate-workflow"></a>驗證工作流程

工作流程會使用您 GitHub 存放庫的基底名稱（在此案例中為*helloworld-node*）來部署 Azure 容器實例。 在您的瀏覽器中，您可以流覽至所提供的連結，以查看執行中的 web 應用程式。 如果您的應用程式接聽8080以外的通訊埠，請在 URL 中指定。

若要在 GitHub UI 中查看每個步驟的工作流程狀態和結果，請參閱[管理工作流程執行](https://help.github.com/actions/configuring-and-managing-workflows/managing-a-workflow-run)。

## <a name="clean-up-resources"></a>清除資源

使用 [az container delete][az-container-delete] 命令停止容器執行個體：

```azurecli
az container delete \
  --name <instance-name>
  --resource-group <resource-group-name>
```

若要刪除資源群組和其中的所有資源，請執行[az group delete][az-group-delete]命令：

```azurecli
az group delete \
  --name <resource-group-name>
```

## <a name="next-steps"></a>後續步驟

流覽[GitHub Marketplace](https://github.com/marketplace?type=actions)以取得更多動作來自動化您的開發工作流程


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
