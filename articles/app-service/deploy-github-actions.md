---
title: 使用 GitHub Actions 設定 CI/CD
description: 了解如何使用 GitHub Actions，從 CI/CD 管線將您的程式碼部署至 Azure App Service。 自訂建置工作並執行複雜的部署。
ms.devlang: na
ms.topic: article
ms.date: 10/25/2019
ms.author: jafreebe
ms.reviewer: ushan
ms.custom: devx-track-python
ms.openlocfilehash: 264976fdfe514a8778c60fe9242ac555f268718d
ms.sourcegitcommit: 648c8d250106a5fca9076a46581f3105c23d7265
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/27/2020
ms.locfileid: "88962565"
---
# <a name="deploy-to-app-service-using-github-actions"></a>使用 GitHub Actions 部署到 App Service

[GitHub Actions](https://help.github.com/en/articles/about-github-actions) 可讓您彈性地建置自動化軟體開發生命週期工作流程。 搭配適用於 GitHub 的 Azure App Service 動作，您就可以使用 GitHub Actions 將工作流程自動化，以部署至 [Azure App Service](overview.md)。

> [!IMPORTANT]
> GitHub Actions 目前為搶鮮版 (Beta)。 您必須先[註冊之後，才能使用您的 GitHub 帳戶加入預覽](https://github.com/features/actions)。
> 

工作流程是由您存放庫內 `/.github/workflows/` 路徑中的 YAML (. yml) 檔案所定義的。 此定義包含組成工作流程的各種步驟與參數。

Azure App Service 工作流程的檔案會有三個區段：

|區段  |工作  |
|---------|---------|
|**驗證** | 1. 定義服務主體。 <br /> 2. 建立 GitHub 秘密。 |
|**建置** | 1. 設定環境。 <br /> 2. 建立 web 應用程式。 |
|**部署** | 1. 部署 web 應用程式。 |

## <a name="generate-deployment-credentials"></a>產生部署認證

# <a name="user-level-credentials"></a>[使用者層級認證](#tab/userlevel)

您可以使用 [Azure CLI](/cli/azure/) 中的 [az ad sp create-for-rbac](/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-create-for-rbac) 命令來建立[服務主體](../active-directory/develop/app-objects-and-service-principals.md#service-principal-object)。 您可以使用 Azure 入口網站中的 [Azure Cloud Shell](https://shell.azure.com/)，或選取 [試試看] 按鈕來執行此命令。

```azurecli-interactive
az ad sp create-for-rbac --name "myApp" --role contributor \
                            --scopes /subscriptions/<subscription-id>/resourceGroups/<group-name>/providers/Microsoft.Web/sites/<app-name> \
                            --sdk-auth
```

在上述範例中，請將預留位置取代為您的訂用帳戶識別碼、資源組名和應用程式名稱。 輸出是具有角色指派認證的 JSON 物件，可讓您存取您的 App Service 應用程式，如下所示。 複製此 JSON 物件以供稍後之用。

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

# <a name="app-level-credentials"></a>[應用層級認證](#tab/applevel)

您可以使用應用程式的發行設定檔，來使用應用層級的認證。 在入口網站中移至應用程式的管理頁面。 在 [ **總覽** ] 頁面中，按一下 [ **取得發行設定檔** ] 選項。

您稍後將需要檔案的內容。

---

## <a name="configure-the-github-secret"></a>設定 GitHub 密碼

# <a name="user-level-credentials"></a>[使用者層級認證](#tab/userlevel)

在 [GitHub](https://github.com/)中，流覽您的存放庫，選取 **> 秘密 > 新增密碼的設定**。

若要使用 [使用者層級的認證](#generate-deployment-credentials)，請將 Azure CLI 命令中的整個 JSON 輸出貼到秘密的值欄位中。 為秘密命名，例如 `AZURE_CREDENTIALS` 。

當您稍後設定工作流程檔案時，您會使用秘密來輸入 `creds` Azure 登入動作。 例如：

```yaml
- uses: azure/login@v1
  with:
    creds: ${{ secrets.AZURE_CREDENTIALS }}
```

# <a name="app-level-credentials"></a>[應用層級認證](#tab/applevel)

在 [GitHub](https://github.com/)中，流覽您的存放庫，選取 **> 秘密 > 新增密碼的設定**。

若要使用 [應用層級的認證](#generate-deployment-credentials)，請將所下載發行設定檔的內容貼入秘密的 [值] 欄位中。 為秘密命名，例如 `azureWebAppPublishProfile` 。

當您稍後設定工作流程檔案時，會使用秘密作為 [ `publish-profile` 部署 Azure Web 應用程式] 動作的輸入。 例如：
    
```yaml
- uses: azure/webapps-deploy@v2
  with:
    publish-profile: ${{ secrets.azureWebAppPublishProfile }}
```

---

## <a name="set-up-the-environment"></a>設定 Azure 環境

設定環境的作業可以使用其中一個設定動作來完成。

|**語言**  |**設定動作**  |
|---------|---------|
|**.NET**     | `actions/setup-dotnet` |
|**Java**     | `actions/setup-java` |
|**JavaScript** | `actions/setup-node` |
|**Python**     | `actions/setup-python` |

下列範例顯示工作流程的一部分，這部分會針對各種支援的語言設定環境：

**JavaScript**

```yaml
    - name: Setup Node 10.x
      uses: actions/setup-node@v1
      with:
        node-version: '10.x'
```
**Python**

```yaml
    - name: Setup Python 3.6
      uses: actions/setup-python@v1
      with:
        python-version: 3.6
```

**.NET**

```yaml
    - name: Setup Dotnet 2.2.300
      uses: actions/setup-dotnet@v1
      with:
        dotnet-version: '2.2.300'
```

**Java**

```yaml
    - name: Setup Java 1.8.x
      uses: actions/setup-java@v1
      with:
        # If your pom.xml <maven.compiler.source> version is not in 1.8.x
        # Please change the Java version to match the version in pom.xml <maven.compiler.source>
        java-version: '1.8.x'
```

## <a name="build-the-web-app"></a>建置 Web 應用程式

這取決於語言與 Azure App Service 支援的語言，此節應該是每種語言的標準建置步驟。

下列範例顯示工作流程的一部分，這部分會以各種支援的語言建置 Web 應用程式。

**JavaScript**

```yaml
    - name: 'Run npm'
      shell: bash
      run: |
        # If your web app project is not located in your repository's root
        # Please change your directory for npm in pushd
        pushd .
        npm install
        npm run build --if-present
        npm run test --if-present
        popd
```

**Python**

```yaml
    - name: 'Run pip'
      shell: bash
      run: |
        # If your web app project is not located in your repository's root
        # Please change your directory for pip in pushd
        pushd .
        python -m pip install --upgrade pip
        pip install -r requirements.txt --target=".python_packages/lib/python3.6/site-packages"
        popd
```

**.NET**

```yaml
    - name: 'Run dotnet build'
      shell: bash
      run: |
        # If your web app project is not located in your repository's root
        # Please consider using pushd to change your path
        pushd .
        dotnet build --configuration Release --output ./output
        popd
```

**Java**

```yaml
    - uses: actions/checkout@v1
    - name: Set up JDK 1.8
      uses: actions/setup-java@v1
      with:
        java-version: 1.8
    - name: Build with Maven
      run: mvn -B package --file pom.xml
```
## <a name="deploy-to-app-service"></a>部署到 App Service

若要將程式碼部署到 App Service 應用程式，請使用 `azure/webapps-deploy@v2` 動作。 此動作有四個參數：

| **參數**  | **說明**  |
|---------|---------|
| **app-name** | (必要) App Service 應用程式的名稱 | 
| **publish-profile** | (選擇性) 包含 Web Deploy 祕密的發行設定檔檔案內容 |
| **套件** | (選擇性) 套件或資料夾的路徑。 要部署的 *.zip、*.war、*.jar 或資料夾 |
| **slot-name** | (選擇性) 輸入生產位置以外的現有位置。 |

# <a name="user-level-credentials"></a>[使用者層級認證](#tab/userlevel)

下面是使用 Azure 服務主體建置 Node.js 應用程式並部署到 Azure 的範例工作流程。 請注意 `creds` 輸入如何參考 `AZURE_CREDENTIALS` 您稍早建立的秘密。

```yaml
on: [push]

name: Node.js

jobs:
  build-and-deploy:
    runs-on: ubuntu-latest
    steps:
    # checkout the repo
    - name: 'Checkout GitHub Action' 
      uses: actions/checkout@master
   
    - uses: azure/login@v1
      with:
        creds: ${{ secrets.AZURE_CREDENTIALS }}
        
    - name: Setup Node 10.x
      uses: actions/setup-node@v1
      with:
        node-version: '10.x'
    
    - name: 'npm install, build, and test'
      run: |
        npm install
        npm run build --if-present
        npm run test --if-present
               
    # deploy web app using Azure credentials
    - uses: azure/webapps-deploy@v2
      with:
        app-name: 'node-rn'

    # Azure logout 
    - name: logout
      run: |
        az logout
```

# <a name="app-level-credentials"></a>[應用層級認證](#tab/applevel)

以下是使用應用程式的發行設定檔，建立 Node.js 應用程式並將其部署至 Azure 的範例工作流程。 請注意 `publish-profile` 輸入如何參考 `azureWebAppPublishProfile` 您稍早建立的秘密。

```yaml
# File: .github/workflows/workflow.yml

on: push

jobs:
  build-and-deploy:
    runs-on: ubuntu-latest
    steps:
    # checkout the repo
    - name: 'Checkout GitHub Action' 
      uses: actions/checkout@master
    
    - name: Setup Node 10.x
      uses: actions/setup-node@v1
      with:
        node-version: '10.x'
    - name: 'npm install, build, and test'
      run: |
        npm install
        npm run build --if-present
        npm run test --if-present
       
    - name: 'Run Azure webapp deploy action using publish profile credentials'
          uses: azure/webapps-deploy@v2
          with: 
            app-name: node-rn
            publish-profile: ${{ secrets.azureWebAppPublishProfile }}
```

---

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