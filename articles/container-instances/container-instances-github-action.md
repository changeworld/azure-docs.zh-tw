---
title: 按 GitHub 操作部署容器實例
description: 配置 GitHub 操作，該操作可自動執行生成、推送容器映射並將其部署到 Azure 容器實例的步驟
ms.topic: article
ms.date: 03/18/2020
ms.custom: ''
ms.openlocfilehash: 13397cee8197afc65b93c587ae1505e59cfdebc1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80258034"
---
# <a name="configure-a-github-action-to-create-a-container-instance"></a>配置 GitHub 操作以創建容器實例

[GitHub 操作](https://help.github.com/actions/getting-started-with-github-actions/about-github-actions)是 GitHub 中的一組功能，用於在存儲代碼並協作處理拉取請求和問題的同一位置自動執行軟體發展工作流。

使用["部署到 Azure 容器實例](https://github.com/azure/aci-deploy)GitHub"操作自動將容器部署到 Azure 容器實例。 該操作允許您為容器實例設置類似于[az 容器創建][az-container-create]命令中的屬性。

本文演示如何在 GitHub 存儲庫中設置工作流，該存儲庫執行以下操作：

* 從 Dockerfile 建置映像
* 將映射推送到 Azure 容器註冊表
* 將容器映射部署到 Azure 容器實例

本文介紹了設置工作流的兩種方法：

* 使用"部署到 Azure 容器實例"操作和其他操作，在 GitHub 存儲庫中自行配置工作流。  
* 在`az container app up`Azure CLI 中的["部署到 Azure](https://github.com/Azure/deploy-to-azure-cli-extension)擴展"中使用該命令。 此命令簡化了 GitHub 工作流和部署步驟的創建。

> [!IMPORTANT]
> Azure 容器實例的 GitHub 操作當前處於預覽狀態。 若您同意[補充的使用規定][terms-of-use]，即可取得預覽。 在公開上市 (GA) 之前，此功能的某些領域可能會變更。

## <a name="prerequisites"></a>Prerequisites

* **GitHub 帳戶**- 如果您https://github.com還沒有帳戶，請創建帳戶。
* **Azure CLI** - 您可以使用 Azure 雲外殼或 Azure CLI 的本地安裝來完成 Azure CLI 步驟。 如果需要安裝或升級，請參閱[安裝 Azure CLI][azure-cli-install]。
* **Azure 容器註冊表**- 如果沒有，請使用[Azure CLI、Azure](../container-registry/container-registry-get-started-azure-cli.md)[門戶](../container-registry/container-registry-get-started-portal.md)或其他方法在基本層中創建 Azure 容器註冊表。 請注意用於部署的資源組，該資源組用於 GitHub 工作流。

## <a name="set-up-repo"></a>設置回購

* 對於本文中的示例，請使用 GitHub 分叉以下存儲庫：https://github.com/Azure-Samples/acr-build-helloworld-node

  此回購包含 Dockerfile 和原始檔案，用於創建小型 Web 應用的容器映射。

  ![GitHub 中的 [分支] 按鈕 (醒目提示) 的螢幕擷取畫面](../container-registry/media/container-registry-tutorial-quick-build/quick-build-01-fork.png)

* 確保為存儲庫啟用了操作。 導航到分叉存儲庫並選擇 **"設置** > **操作**"。 在 **"操作"許可權**中，確保為此**存儲庫啟用本地和協力廠商操作**。

## <a name="configure-github-workflow"></a>配置 GitHub 工作流

### <a name="create-service-principal-for-azure-authentication"></a>為 Azure 身份驗證創建服務主體

在 GitHub 工作流中，您需要提供 Azure 憑據以向 Azure CLI 進行身份驗證。 下面的示例創建一個服務主體，其中參與者角色限定到容器註冊表的資源組。

首先，獲取資源組的資源識別碼。 在以下[az 組顯示][az-acr-show]命令中替換組的名稱：

```azurecli
groupId=$(az group show \
  --name <resource-group-name> \
  --query id --output tsv)
```

使用[az ad sp 創建為 rbac][az-ad-sp-create-for-rbac]創建服務主體：

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

保存 JSON 輸出，因為它在後面的步驟中使用。 此外，請注意`clientId`在下一節中更新服務主體時需要更新 的 。

### <a name="update-service-principal-for-registry-authentication"></a>更新註冊表身份驗證的服務主體

更新 Azure 服務主體憑據，以允許對容器註冊表進行推送和拉取許可權。 此步驟允許 GitHub 工作流使用服務主體[對容器註冊表進行身份驗證](../container-registry/container-registry-auth-service-principal.md)。 

獲取容器註冊表的資源識別碼。 在以下[az acr 顯示][az-acr-show]命令中替換註冊表的名稱：

```azurecli
registryId=$(az acr show \
  --name <registry-name> \
  --query id --output tsv)
```

使用[az 角色指派創建][az-role-assignment-create]來分配 AcrPush 角色，該角色提供對註冊表的推送和拉取存取權限。 替換服務主體的用戶端 ID：

```azurecli
az role assignment create \
  --assignee <ClientId> \
  --scope $registryId \
  --role AcrPush
```

### <a name="save-credentials-to-github-repo"></a>將憑據保存到 GitHub 存儲庫

1. 在 GitHub UI 中，導航到分叉存儲庫並選擇 **"設置** > **機密**"。 

1. 選擇 **"添加新機密**"以添加以下機密：

|祕密  |值  |
|---------|---------|
|`AZURE_CREDENTIALS`     | 服務主體創建的整個 JSON 輸出 |
|`REGISTRY_LOGIN_SERVER`   | 註冊表的登錄伺服器名稱（所有小寫）。 示例 *：myregistry.azure.cr.io*        |
|`REGISTRY_USERNAME`     |  `clientId`來自服務主體創建的 JSON 輸出       |
|`REGISTRY_PASSWORD`     |  `clientSecret`來自服務主體創建的 JSON 輸出 |
| `RESOURCE_GROUP` | 用於限定服務主體的資源組的名稱 |

### <a name="create-workflow-file"></a>創建工作流檔

1. 在 GitHub UI 中，選擇 > **"操作新工作流**"。 **Actions**
1. 選擇 **"自己設置工作流**"。
1. 在 **"編輯新檔"** 中，粘貼以下 YAML 內容以覆蓋示例代碼。 接受預設檔案名`main.yml`，或提供您選擇的檔案名。
1. 選擇 **"開始提交**"，可選地提供提交的簡短和擴展說明，然後選擇 **"提交新檔**"。

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

### <a name="validate-workflow"></a>驗證工作流

提交工作流檔後，將觸發工作流。 要查看工作流進度，導航到**操作** > **工作流**。 

![查看工作流進度](./media/container-instances-github-action/github-action-progress.png)

有關查看工作流中每個步驟的狀態和結果的資訊，請參閱[管理工作流運行](https://help.github.com/actions/configuring-and-managing-workflows/managing-a-workflow-run)。

工作流完成後，通過運行[az 容器顯示][az-container-show]命令獲取有關名為*aci-sampleapp 的*容器實例的資訊。 替換資源組的名稱： 

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

預配實例後，在瀏覽器中導航到容器的 FQDN 以查看正在運行的 Web 應用。

![在瀏覽器中運行 Web 應用](./media/container-instances-github-action/github-action-container.png)

## <a name="use-deploy-to-azure-extension"></a>使用部署到 Azure 擴展

或者，使用 Azure CLI 中的["部署到 Azure 擴展"](https://github.com/Azure/deploy-to-azure-cli-extension)來配置工作流。 擴展`az container app up`中的命令從中獲取輸入參數，以設置要部署到 Azure 容器實例的工作流。 

Azure CLI 創建的工作流與[可以使用 GitHub 手動創建的](#configure-github-workflow)工作流類似。

### <a name="additional-prerequisite"></a>其他先決條件

除了此方案[的先決條件](#prerequisites)和[回購設置](#set-up-repo)外，還需要為 Azure CLI 安裝 **"部署到 Azure"擴展**。

運行[az 擴展添加][az-extension-add]命令以安裝擴展：

```azurecli
az extension add \
  --name deploy-to-azure
```

有關查找、安裝和管理擴展的資訊，請參閱使用 Azure [CLI 使用擴展](/cli/azure/azure-cli-extensions-overview)。

### <a name="run-az-container-app-up"></a>執行 

要運行[az 容器應用上部][az-container-app-up]命令，請至少提供：

* Azure 容器註冊表的名稱，例如，*我的註冊表*
* 例如，GitHub 存儲庫的 URL，`https://github.com/<your-GitHub-Id>/acr-build-helloworld-node`

範例命令：

```azurecli
az container app up \
  --acr myregistry \
  --repository https://github.com/myID/acr-build-helloworld-node
```

### <a name="command-progress"></a>命令進度

* 出現提示時，請提供 GitHub 憑據或提供具有*存儲庫*和*使用者*作用域的[GitHub 個人訪問權杖](https://help.github.com/github/authenticating-to-github/creating-a-personal-access-token-for-the-command-line)（PAT），以便通過註冊表進行身份驗證。 如果提供 GitHub 憑據，該命令將為您創建 PAT。

* 該命令為工作流創建回購機密：

  * Azure CLI 的服務主體憑據
  * 訪問 Azure 容器註冊表的憑據

* 該命令將工作流檔提交到您的回購後，工作流將觸發。 

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

### <a name="validate-workflow"></a>驗證工作流

工作流部署一個 Azure 容器實例，其基本名稱為 GitHub 存儲庫，在本例中為*acr-build-helloworld 節點*。 在瀏覽器中，您可以流覽到提供的連結以查看正在運行的 Web 應用。 如果應用偵聽 8080 以外的埠，請在 URL 中指定該埠。

要查看 GitHub UI 中每個步驟的工作流狀態和結果，請參閱[管理工作流運行](https://help.github.com/actions/configuring-and-managing-workflows/managing-a-workflow-run)。

## <a name="clean-up-resources"></a>清除資源

使用 [az container delete][az-container-delete] 命令停止容器執行個體：

```azurecli
az container delete \
  --name <instance-name>
  --resource-group <resource-group-name>
```

要刪除資源組及其中的所有資源，請運行[az 組刪除][az-group-delete]命令：

```azurecli
az group delete \
  --name <resource-group-name>
```

## <a name="next-steps"></a>後續步驟

流覽[GitHub 市場](https://github.com/marketplace?type=actions)，瞭解有關自動開發工作流的更多操作


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
