---
title: GitHub Actions 的自訂容器 CI/CD
description: 瞭解如何使用 GitHub Actions 將自訂 Linux 容器部署至來自 CI/CD 管線的 App Service。
ms.devlang: na
ms.topic: article
ms.date: 10/25/2019
ms.author: jafreebe
ms.reviewer: ushan
ms.openlocfilehash: 7f2824f4dcacb26d8941f51db6129aea0bb5f915
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/25/2020
ms.locfileid: "91273274"
---
# <a name="deploy-a-custom-container-to-app-service-using-github-actions"></a>使用 GitHub Actions 將自訂容器部署到 App Service

[GitHub Actions](https://help.github.com/en/articles/about-github-actions) 可讓您彈性地建置自動化軟體開發生命週期工作流程。 使用 [容器的 Azure App Service 動作](https://github.com/Azure/webapps-container-deploy)，您可以將工作流程自動化，以使用 GitHub Actions [App Service](overview.md) 部署自訂容器。

> [!IMPORTANT]
> GitHub Actions 目前為搶鮮版 (Beta)。 您必須先[註冊之後，才能使用您的 GitHub 帳戶加入預覽](https://github.com/features/actions)。
> 

工作流程是由您存放庫內 `/.github/workflows/` 路徑中的 YAML (. yml) 檔案所定義的。 此定義包含組成工作流程的各種步驟與參數。

針對 Azure App Service 容器工作流程，檔案有三個區段：

|區段  |工作  |
|---------|---------|
|**驗證** | 1. 定義服務主體。 <br /> 2. 建立 GitHub 秘密。 |
|**建置** | 1. 設定環境。 <br /> 2. 建立容器映射。 |
|**部署** | 1. 部署容器映射。 |

## <a name="create-a-service-principal"></a>建立服務主體

您可以使用 [Azure CLI](/cli/azure/) 中的 [az ad sp create-for-rbac](/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-create-for-rbac) 命令來建立[服務主體](../active-directory/develop/app-objects-and-service-principals.md#service-principal-object)。 您可以使用 Azure 入口網站中的 [Azure Cloud Shell](https://shell.azure.com/)，或選取 [試試看] 按鈕來執行此命令。

```azurecli-interactive
az ad sp create-for-rbac --name "myApp" --role contributor \
                            --scopes /subscriptions/<subscription-id>/resourceGroups/<group-name> \
                            --sdk-auth
```

在上述範例中，請將預留位置取代為您的訂用帳戶識別碼和資源組名。 輸出是具有角色指派認證的 JSON 物件，可讓您存取您的 App Service 應用程式，如下所示。 複製此 JSON 物件以供稍後之用。

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
> 授與最小存取權永遠是最佳作法。 您可以將上述 Az CLI 命令中的範圍限制為特定的 App Service 應用程式，以及將容器映射推送至其中的 Azure Container Registry。

## <a name="configure-the-github-secret"></a>設定 GitHub 密碼

在 [GitHub](https://github.com/)中，流覽您的存放庫，選取 **> 秘密 > 新增密碼的設定**。

貼上 JSON 輸出的內容，從 [建立服務主體](#create-a-service-principal) 作為 secret 變數的值。 為秘密命名，例如 `AZURE_CREDENTIALS` 。

當您稍後設定工作流程檔案時，您會使用秘密來輸入 `creds` Azure 登入動作。 例如：

```yaml
- uses: azure/login@v1
  with:
    creds: ${{ secrets.AZURE_CREDENTIALS }}
```

同樣地，為容器登錄認證定義下列其他秘密，並在 Docker 登入動作中進行設定。

- REGISTRY_USERNAME
- REGISTRY_PASSWORD

## <a name="build-the-container-image"></a>建立容器映射

下列範例會顯示建立 docker 映射的部分工作流程。

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

## <a name="deploy-to-an-app-service-container"></a>部署至 App Service 容器

若要將您的映射部署到 App Service 中的自訂容器，請使用 `azure/webapps-container-deploy@v2` 動作。 此動作有五個參數：

| **參數**  | **說明**  |
|---------|---------|
| **app-name** | (必要) App Service 應用程式的名稱 | 
| **slot-name** | (選擇性) 輸入生產位置以外的現有位置。 |
| **images** |  (必要) 指定完整的容器映射 () 名稱。 例如，' myregistry.azurecr.io/nginx:latest ' 或 ' python： 3.7.2-alpine/'。 若為多容器應用程式，可以提供多個容器映射名稱 (多行分隔)  |
| **設定檔** |  (Docker 撰寫檔案的選擇性) 路徑。 應該是完整路徑或相對於預設工作目錄。 多容器應用程式的必要元件。 |
| **容器-命令** |  (選擇性) 輸入啟動命令。 例如， dotnet run 或 dotnet filename.dll |

以下是在 App Service 中建立 Node.js 應用程式並部署至自訂容器的範例工作流程。 請注意 `creds` 輸入如何參考 `AZURE_CREDENTIALS` 您稍早建立的秘密。

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
      
    - uses: azure/webapps-container-deploy@v2
      with:
        app-name: 'node-rnc'
        images: 'contoso.azurecr.io/nodejssampleapp:${{ github.sha }}'
    
    - name: Azure logout
      run: |
        az logout
```

## <a name="next-steps"></a>後續步驟

您可以在 GitHub 上找到分組到不同存放庫的一組動作，其中每一個都包含文件與範例，以協助您使用 GitHub 來進行 CI/CD，並將您的應用程式部署至 Azure。

- [要部署到 Azure 的動作工作流程](https://github.com/Azure/actions-workflow-samples)

- [Azure 登入](https://github.com/Azure/login)

- [Azure WebApp](https://github.com/Azure/webapps-deploy)

- [適用於容器的 Azure WebApp](https://github.com/Azure/webapps-container-deploy)

- [Docker 登入/登出](https://github.com/Azure/docker-login)

- [觸發工作流程的事件](https://help.github.com/en/articles/events-that-trigger-workflows)

- [K8s 部署](https://github.com/Azure/k8s-deploy)

- [入門工作流程](https://github.com/actions/starter-workflows)