---
title: 來自 GitHub 操作的自訂容器 CI/CD
description: 瞭解如何使用 GitHub 操作從 CI/CD 管道將自訂 Linux 容器部署到應用服務。
ms.devlang: na
ms.topic: article
ms.date: 10/25/2019
ms.author: jafreebe
ms.reviewer: ushan
ms.openlocfilehash: d5f175d887cec1d5b5e567d3f716e6492f4516dd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "78246979"
---
# <a name="deploy-a-custom-container-to-app-service-using-github-actions"></a>使用 GitHub 操作將自訂容器部署到應用服務

[GitHub 操作](https://help.github.com/en/articles/about-github-actions)使您能夠靈活地構建自動化的軟體發展生命週期工作流。 使用[容器的 Azure 應用服務操作](https://github.com/Azure/webapps-container-deploy)，可以自動執行工作流，使用 GitHub 操作將應用作為[自訂容器部署到應用服務](https://azure.microsoft.com/services/app-service/containers/)。

> [!IMPORTANT]
> GitHub 操作當前處於測試階段。 您必須首先註冊才能使用 GitHub 帳戶[加入預覽](https://github.com/features/actions)。
> 

工作流由存儲庫中路徑中的`/.github/workflows/`YAML （.yml） 檔定義。 此定義包含構成工作流的各種步驟和參數。

對於 Azure 應用服務容器工作流，該檔有三個部分：

|區段  |工作  |
|---------|---------|
|[驗證]**** | 1. 定義服務主體。 <br /> 2. 創建 GitHub 金鑰。 |
|**建立** | 1. 設置環境。 <br /> 2. 構建容器映射。 |
|**部署** | 1. 部署容器映射。 |

## <a name="create-a-service-principal"></a>建立服務主體

可以使用[Azure CLI](https://docs.microsoft.com/cli/azure/)中的[az ad sp 創建 rbac](https://docs.microsoft.com/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-create-for-rbac)命令創建[服務主體](https://docs.microsoft.com/azure/active-directory/develop/app-objects-and-service-principals#service-principal-object)。 可以使用 Azure 門戶中的[Azure 雲外殼](https://shell.azure.com/)運行此命令，也可以選擇"**試用"** 按鈕。

```azurecli-interactive
az ad sp create-for-rbac --name "myApp" --role contributor \
                            --scopes /subscriptions/{subscription-id}/resourceGroups/{resource-group} \
                            --sdk-auth
                            
# Replace {subscription-id}, {resource-group} with the subscription, resource group details of the WebApp
```

輸出是一個 JSON 物件，具有角色指派憑據，提供對應用服務應用的訪問，如下所示。 複製此 JSON 物件以從 GitHub 進行身份驗證。

 ```output 
  {
    "clientId": "<GUID>",
    "clientSecret": "<GUID>",
    "subscriptionId": "<GUID>",
    "tenantId": "<GUID>",
    (...)
  }
```

> [!IMPORTANT]
> 給予最低存取權限總是一種好的做法。 您可以將上述 Az CLI 命令中的範圍限制為特定的應用服務應用和將容器映射推送到的 Azure 容器註冊表。

## <a name="configure-the-github-secret"></a>配置 GitHub 金鑰

下面的示例使用使用者級憑據（即 Azure 服務主體）進行部署。 按照步驟配置機密：

1. 在[GitHub](https://github.com/)中，流覽存儲庫，選擇 **"設置>機密>添加新機密**

2. 將以下`az cli`命令的內容粘貼為機密變數的值。 例如： `AZURE_CREDENTIALS` 。

    
    ```azurecli
    az ad sp create-for-rbac --name "myApp" --role contributor \
                                --scopes /subscriptions/{subscription-id}/resourceGroups/{resource-group} \
                                --sdk-auth
                                
    # Replace {subscription-id}, {resource-group} with the subscription, resource group details
    ```

3. 現在，在分支中的工作流檔中：`.github/workflows/workflow.yml`將 Azure 登錄操作中的機密替換為機密。

4. 同樣，為容器註冊表憑據定義以下其他機密，並在 Docker 登錄操作中設置它們。 

    - REGISTRY_USERNAME
    - REGISTRY_PASSWORD

5. 定義後，您將看到如下所示的機密。

    ![容器機密](../media/app-service-github-actions/app-service-secrets-container.png)

## <a name="build-the-container-image"></a>生成容器映射

下面的示例顯示生成 docker 映射的工作流的一部分。

```yaml
on: [push]

name: Linux_Container_Node_Workflow

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
    
    - uses: azure/docker-login@v1
      with:
        login-server: contoso.azurecr.io
        username: ${{ secrets.REGISTRY_USERNAME }}
        password: ${{ secrets.REGISTRY_PASSWORD }}
    
    - run: |
        docker build . -t contoso.azurecr.io/nodejssampleapp:${{ github.sha }}
        docker push contoso.azurecr.io/nodejssampleapp:${{ github.sha }}
```

## <a name="deploy-to-an-app-service-container"></a>部署到應用服務容器

要將映射部署到應用服務中的自訂容器，請使用 操作`azure/webapps-container-deploy@v1`。 此操作有五個參數：

| **參數**  | **說明**  |
|---------|---------|
| **應用名稱** | （必需）應用服務應用的名稱 | 
| **插槽名稱** | （可選）輸入生產槽以外的現有插槽 |
| **圖像** | （必需）指定完全限定的容器映射名稱。 例如，"myregistry.azurecr.io/nginx:latest"或"python：3.7.2-高山/"。 對於多容器應用，可以提供多個容器映射名稱（多行分隔） |
| **設定檔** | （可選）Docker-Compose 檔的路徑。 應是完全限定的路徑或相對於預設工作目錄。 多容器應用需要。 |
| **容器命令** | （可選）輸入啟動命令。 對於前 點網運行或點網檔案名.dll |

下面是生成 Node.js 應用並將其部署到應用服務中的自訂容器的示例工作流。

```yaml
on: [push]

name: Linux_Container_Node_Workflow

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
    
    - uses: azure/docker-login@v1
      with:
        login-server: contoso.azurecr.io
        username: ${{ secrets.REGISTRY_USERNAME }}
        password: ${{ secrets.REGISTRY_PASSWORD }}
    
    - run: |
        docker build . -t contoso.azurecr.io/nodejssampleapp:${{ github.sha }}
        docker push contoso.azurecr.io/nodejssampleapp:${{ github.sha }} 
      
    - uses: azure/webapps-container-deploy@v1
      with:
        app-name: 'node-rnc'
        images: 'contoso.azurecr.io/nodejssampleapp:${{ github.sha }}'
    
    - name: Azure logout
      run: |
        az logout
```

## <a name="next-steps"></a>後續步驟

您可以在 GitHub 上找到我們分組到不同存儲庫的操作集，每個存儲庫都包含文檔和示例，以説明您使用 GitHub 進行 CI/CD 並將應用部署到 Azure。

- [Azure 登入](https://github.com/Azure/login)

- [Azure WebApp](https://github.com/Azure/webapps-deploy)

- [用於容器的 Azure WebApp](https://github.com/Azure/webapps-container-deploy)

- [Docker 登錄/登出](https://github.com/Azure/docker-login)

- [觸發工作流的事件](https://help.github.com/en/articles/events-that-trigger-workflows)

- [K8s 部署](https://github.com/Azure/k8s-deploy)

- [初學者 CI 工作流](https://github.com/actions/starter-workflows)

- [要部署到 Azure 的初學者工作流](https://github.com/Azure/actions-workflow-samples)
