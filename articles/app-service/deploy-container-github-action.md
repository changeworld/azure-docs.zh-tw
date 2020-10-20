---
title: GitHub Actions 的自訂容器 CI/CD
description: 瞭解如何使用 GitHub Actions 將自訂 Linux 容器部署至來自 CI/CD 管線的 App Service。
ms.devlang: na
ms.topic: article
ms.date: 10/03/2020
ms.author: jafreebe
ms.reviewer: ushan
ms.custom: github-actions-azure
ms.openlocfilehash: f3bc407791b25e4dc1dddd61b60b3cefe0195919
ms.sourcegitcommit: 957c916118f87ea3d67a60e1d72a30f48bad0db6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/19/2020
ms.locfileid: "92203189"
---
# <a name="deploy-a-custom-container-to-app-service-using-github-actions"></a>使用 GitHub Actions 將自訂容器部署到 App Service

[GitHub Actions](https://help.github.com/en/articles/about-github-actions) 可讓您彈性地建立自動化軟體發展工作流程。 透過 [Azure Web Deploy 動作](https://github.com/Azure/webapps-deploy)，您可以將工作流程自動化，以使用 GitHub Actions 將自訂容器部署到 [App Service](overview.md) 。

工作流程是由您存放庫內 `/.github/workflows/` 路徑中的 YAML (. yml) 檔案所定義的。 此定義包含工作流程中的各種步驟和參數。

針對 Azure App Service 容器工作流程，檔案有三個區段：

|區段  |工作  |
|---------|---------|
|**驗證** | 1. 取得服務主體或發行設定檔。 <br /> 2. 建立 GitHub 秘密。 |
|**建置** | 1. 建立環境。 <br /> 2. 建立容器映射。 |
|**部署** | 1. 部署容器映射。 |

## <a name="prerequisites"></a>必要條件

- 具有有效訂用帳戶的 Azure 帳戶。 [免費建立帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
- GitHub 帳戶。 如果您沒有帳戶，請 [免費](https://github.com/join)註冊。  
- 適用于容器的工作容器登錄和 Azure App Service 應用程式。 這個範例會使用 Azure Container Registry。 
    - [瞭解如何使用 Docker 建立容器化的 Node.js 應用程式、將容器映射推送至登錄，然後將映射部署到 Azure App Service](/azure/developer/javascript/tutorial-vscode-docker-node-01)

## <a name="generate-deployment-credentials"></a>產生部署認證

使用 Azure App Services 進行 GitHub Actions 驗證的建議方式是使用發行設定檔。 您也可以使用服務主體進行驗證，但此程式需要更多步驟。 

將您的發佈設定檔認證或服務主體儲存為 [GitHub 秘密](https://docs.github.com/en/actions/reference/encrypted-secrets) ，以向 Azure 進行驗證。 您將會在工作流程記憶體取秘密。 

# <a name="publish-profile"></a>[發行設定檔](#tab/publish-profile)

發行設定檔是應用層級的認證。 將您的發佈設定檔設定為 GitHub 秘密。 

1. 在 Azure 入口網站中，移至您的 app service。 

1. 在 [ **總覽** ] 頁面上，選取 [ **取得發行設定檔**]。

1. 儲存下載的檔案。 您將使用檔案的內容來建立 GitHub 秘密。

# <a name="service-principal"></a>[服務主體](#tab/service-principal)

您可以在[Azure CLI](/cli/azure/)中使用[az ad sp 建立-rbac](/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-create-for-rbac&preserve-view=true)命令來建立[服務主體](../active-directory/develop/app-objects-and-service-principals.md#service-principal-object)。 使用 Azure 入口網站中的 [Azure Cloud Shell](https://shell.azure.com/) 來執行此命令，或選取 [ **試試看** ] 按鈕。

```azurecli-interactive
az ad sp create-for-rbac --name "myApp" --role contributor \
                            --scopes /subscriptions/<subscription-id>/resourceGroups/<group-name>/providers/Microsoft.Web/sites/<app-name> \
                            --sdk-auth
```

在此範例中，請將預留位置取代為您的訂用帳戶識別碼、資源組名和應用程式名稱。 輸出是具有角色指派認證的 JSON 物件，可讓您存取您的 App Service 應用程式。 複製此 JSON 物件以供稍後之用。

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
> 授與最小存取權永遠是最佳作法。 上一個範例中的範圍僅限於特定的 App Service 應用程式，而非整個資源群組。

---

## <a name="configure-the-github-secret"></a>設定 GitHub 密碼

在 [GitHub](https://github.com/)中，流覽您的存放庫，選取 **> 秘密 > 新增密碼的設定**。

貼上 JSON 輸出的內容作為 secret 變數的值。 為秘密命名，例如 `AZURE_CREDENTIALS` 。

當您稍後設定工作流程檔案時，您會使用秘密來輸入 `creds` Azure 登入動作。 例如：

```yaml
- uses: azure/login@v1
  with:
    creds: ${{ secrets.AZURE_CREDENTIALS }}
```

## <a name="configure-the-github-secret-for-authentication"></a>設定 GitHub 秘密以進行驗證

# <a name="publish-profile"></a>[發行設定檔](#tab/publish-profile)

在 [GitHub](https://github.com/)中，流覽您的存放庫，選取 **> 秘密 > 新增密碼的設定**。

若要使用 [應用層級的認證](#generate-deployment-credentials)，請將所下載發行設定檔的內容貼入秘密的 [值] 欄位中。 為秘密命名 `AZURE_WEBAPP_PUBLISH_PROFILE` 。

當您設定 GitHub 工作流程時，您會 `AZURE_WEBAPP_PUBLISH_PROFILE` 在 [部署 Azure Web 應用程式] 動作中使用。 例如：
    
```yaml
- uses: azure/webapps-deploy@v2
  with:
    publish-profile: ${{ secrets.AZURE_WEBAPP_PUBLISH_PROFILE }}
```

# <a name="service-principal"></a>[服務主體](#tab/service-principal)

在 [GitHub](https://github.com/)中，流覽您的存放庫，選取 **> 秘密 > 新增密碼的設定**。

若要使用 [使用者層級的認證](#generate-deployment-credentials)，請將 Azure CLI 命令中的整個 JSON 輸出貼到秘密的值欄位中。 為秘密命名，例如 `AZURE_CREDENTIALS` 。

當您稍後設定工作流程檔案時，您會使用秘密來輸入 `creds` Azure 登入動作。 例如：

```yaml
- uses: azure/login@v1
  with:
    creds: ${{ secrets.AZURE_CREDENTIALS }}
```

---

## <a name="configure-github-secrets-for-your-registry"></a>為您的登錄設定 GitHub 秘密

定義要與 Docker 登入動作搭配使用的秘密。 

1. 移至 Azure 入口網站或 Docker 中的容器，並複製使用者名稱和密碼。 

2. 為名為的登錄使用者名稱定義新的密碼 `REGISTRY_USERNAME` 。 

3. 針對名為的登錄密碼定義新的密碼 `REGISTRY_PASSWORD` 。 

## <a name="build-the-container-image"></a>建立容器映射

下列範例會顯示建立 Node.JS Docker 映射的部分工作流程。 使用 [Docker 登](https://github.com/azure/docker-login) 入登入私人容器登錄。 此範例使用 Azure Container Registry，但相同的動作適用于其他登錄。 


```yaml
name: Linux Container Node Workflow

on: [push]

jobs:
  build:
    runs-on: ubuntu-latest

    steps:
    - uses: actions/checkout@v2
    - uses: azure/docker-login@v1
      with:
        login-server: mycontainer.azurecr.io
        username: ${{ secrets.REGISTRY_USERNAME }}
        password: ${{ secrets.REGISTRY_PASSWORD }}
    - run: |
        docker build . -t mycontainer.azurecr.io/myapp:${{ github.sha }}
        docker push mycontainer.azurecr.io/myapp:${{ github.sha }}     
```

您也可以使用 [Docker 登](https://github.com/azure/docker-login) 入同時登入多個容器登錄。 此範例包含兩個新的 GitHub 秘密，可使用 docker.io 進行驗證。

```yml
name: Linux Container Node Workflow

on: [push]

jobs:
  build:
    runs-on: ubuntu-latest

    steps:
    - uses: actions/checkout@v2
    - uses: azure/docker-login@v1
      with:
        login-server: mycontainer.azurecr.io
        username: ${{ secrets.REGISTRY_USERNAME }}
        password: ${{ secrets.REGISTRY_PASSWORD }}
    - uses: azure/docker-login@v1
      with:
        login-server: index.docker.io
        username: ${{ secrets.DOCKERIO_USERNAME }}
        password: ${{ secrets.DOCKERIO_PASSWORD }}
    - run: |
        docker build . -t mycontainer.azurecr.io/myapp:${{ github.sha }}
        docker push mycontainer.azurecr.io/myapp:${{ github.sha }}     
```

## <a name="deploy-to-an-app-service-container"></a>部署至 App Service 容器

若要將您的映射部署到 App Service 中的自訂容器，請使用 `azure/webapps-deploy@v2` 動作。 此動作有七個參數：

| **參數**  | **說明**  |
|---------|---------|
| **app-name** | (必要) App Service 應用程式的名稱 | 
| **publish-profile** |  (選用) 適用于 Web Apps (Windows 和 Linux) 和 Web 應用程式容器 (Linux) 。 不支援多重容器案例。 發佈設定檔 (\* . 使用 Web Deploy 秘密 .publishsettings) 檔案內容 | 
| **slot-name** | (選擇性) 輸入生產位置以外的現有位置。 |
| **套件** |  (選擇性) 僅適用于 Web 應用程式：套件或資料夾的路徑。 \*.zip、 \* war、 \* .jar 或要部署的資料夾 |
| **images** |  (必要) 僅適用于 Web 應用程式容器：請指定完整的容器映射 () 名稱。 例如，' myregistry.azurecr.io/nginx:latest ' 或 ' python： 3.7.2-alpine/'。 若為多容器應用程式，可以提供多個容器映射名稱 (多行分隔)  |
| **設定檔** |  (選擇性) 僅適用于 Web 應用程式容器： Docker-Compose 檔的路徑。 應該是完整路徑或相對於預設工作目錄。 多容器應用程式的必要元件。 |
| **startup-命令** |  (選擇性) 輸入啟動命令。 例如， dotnet run 或 dotnet filename.dll |

# <a name="publish-profile"></a>[發行設定檔](#tab/publish-profile)

```yaml
name: Linux Container Node Workflow

on: [push]

jobs:
  build:
    runs-on: ubuntu-latest

    steps:
    - uses: actions/checkout@v2

    - uses: azure/docker-login@v1
      with:
        login-server: mycontainer.azurecr.io
        username: ${{ secrets.REGISTRY_USERNAME }}
        password: ${{ secrets.REGISTRY_PASSWORD }}

    - run: |
        docker build . -t mycontainer.azurecr.io/myapp:${{ github.sha }}
        docker push mycontainer.azurecr.io/myapp:${{ github.sha }}     

    - uses: azure/webapps-deploy@v2
      with:
        app-name: 'myapp'
        publish-profile: ${{ secrets.AZURE_WEBAPP_PUBLISH_PROFILE }}
        images: 'mycontainer.azurecr.io/myapp:${{ github.sha }}'
```
# <a name="service-principal"></a>[服務主體](#tab/service-principal)

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
        login-server: mycontainer.azurecr.io
        username: ${{ secrets.REGISTRY_USERNAME }}
        password: ${{ secrets.REGISTRY_PASSWORD }}
    - run: |
        docker build . -t mycontainer.azurecr.io/myapp:${{ github.sha }}
        docker push mycontainer.azurecr.io/myapp:${{ github.sha }}     
      
    - uses: azure/webapps-deploy@v2
      with:
        app-name: 'myapp'
        images: 'mycontainer.azurecr.io/myapp:${{ github.sha }}'
    
    - name: Azure logout
      run: |
        az logout
```

---

## <a name="next-steps"></a>後續步驟

您可以在 GitHub 上找到分組到不同存放庫的一組動作，其中每一個都包含文件與範例，以協助您使用 GitHub 來進行 CI/CD，並將您的應用程式部署至 Azure。

- [要部署到 Azure 的動作工作流程](https://github.com/Azure/actions-workflow-samples)

- [Azure 登入](https://github.com/Azure/login)

- [Azure WebApp](https://github.com/Azure/webapps-deploy)

- [Docker 登入/登出](https://github.com/Azure/docker-login)

- [觸發工作流程的事件](https://help.github.com/en/articles/events-that-trigger-workflows)

- [K8s 部署](https://github.com/Azure/k8s-deploy)

- [入門工作流程](https://github.com/actions/starter-workflows)
