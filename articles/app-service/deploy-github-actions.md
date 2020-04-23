---
title: 使用 GitHub 操作組態 CI/CD
description: 瞭解如何使用 GitHub 操作從 CI/CD 管道將代碼部署到 Azure 應用服務。 自定義生成任務並執行複雜的部署。
ms.devlang: na
ms.topic: article
ms.date: 10/25/2019
ms.author: jafreebe
ms.reviewer: ushan
ms.openlocfilehash: 57ca5b0880d4b027e33bc0d01fc6225eb886029b
ms.sourcegitcommit: 09a124d851fbbab7bc0b14efd6ef4e0275c7ee88
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2020
ms.locfileid: "82084986"
---
# <a name="deploy-to-app-service-using-github-actions"></a>使用 GitHub 作業部署到應用服務

[GitHub 操作](https://help.github.com/en/articles/about-github-actions)使您能夠靈活地構建自動化的軟體開發生命週期工作流。 使用 GitHub 的 Azure 應用服務操作,可以自動執行工作流程以使用 GitHub 作業部署到[Azure 應用服務](overview.md)。

> [!IMPORTANT]
> GitHub 操作當前處於測試階段。 您必須首先註冊才能使用 GitHub 帳號[加入預覽](https://github.com/features/actions)。
> 

工作流由儲存庫中路徑中的`/.github/workflows/`YAML (.yml) 檔案定義。 此定義包含構成工作流的各種步驟和參數。

對於 Azure 應用服務工作流,該檔有三個部分:

|區段  |工作  |
|---------|---------|
|**驗證** | 1. 定義服務主體 <br /> 2. 建立 GitHub 機密 |
|**建置** | 1. 設定環境 <br /> 2. 建置 Web 應用程式 |
|**部署** | 1. 部署 Web 應用 |

## <a name="create-a-service-principal"></a>建立服務主體

可以使用[Azure CLI](https://docs.microsoft.com/cli/azure/)中的[az ad sp 建立 rbac](https://docs.microsoft.com/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-create-for-rbac)指令建立[服務主體](../active-directory/develop/app-objects-and-service-principals.md#service-principal-object)。 可以使用 Azure 門戶中的[Azure 雲外殼](https://shell.azure.com/)運行此命令,也可以選擇「**試用」** 按鈕。

```azurecli-interactive
az ad sp create-for-rbac --name "myApp" --role contributor --scopes /subscriptions/<subscription-id>/resourceGroups/<group-name>/providers/Microsoft.Web/sites/<app-name> --sdk-auth
```

在此範例中,將資源中的占位符替換為訂閱 ID、資源組名稱和應用名稱。 輸出是提供對應用服務應用訪問許可權的角色分配憑據。 複製此 JSON 物件,可用於從 GitHub 進行身份驗證。

> [!NOTE]
> 如果您決定使用發佈配置檔進行身份驗證,則無需創建服務主體。

> [!IMPORTANT]
> 給予最低訪問許可權總是一種好的做法。 這就是為什麼上一個範例中的範圍僅限於特定的應用服務應用,而不是整個資源組。

## <a name="configure-the-github-secret"></a>設定 GitHub 金鑰

您還可以使用應用級認證(即發佈配置檔進行部署)。 依步驟設定機密:

1. 使用 **「獲取發佈設定檔」** 選項從門戶下載應用服務應用的發佈配置檔。

2. 在[GitHub](https://github.com/)中,瀏覽儲存函式庫,選擇 **「設定>機密>添加新機密**

    ![密碼](media/app-service-github-actions/secrets.png)

3. 將下載的發佈設定檔檔的內容貼上到機密的值欄位中。

4. 現在,在分支中的工作流檔中:`.github/workflows/workflow.yml`替換部署 Azure Web`publish-profile`應用操作的輸入的機密。
    
    ```yaml
        - uses: azure/webapps-deploy@v2
          with:
            creds: ${{ secrets.azureWebAppPublishProfile }}
    ```

5. 定義后,您將看到如下所示的機密。

    ![密碼](media/app-service-github-actions/app-service-secrets.png)

## <a name="set-up-the-environment"></a>設定 Azure 環境

可以使用其中一個設置操作來設置環境。

|**語言**  |**設定作業**  |
|---------|---------|
|**.NET**     | `actions/setup-dotnet` |
|**Java**     | `actions/setup-java` |
|**JavaScript** | `actions/setup-node` |
|**Python**     | `actions/setup-python` |

以下範例顯示了為各種受支援語言設定環境的工作流部分:

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

## <a name="build-the-web-app"></a>編譯 Web 應用

這取決於 Azure 應用服務支援的語言和語言,此部分應該是每種語言的標準生成步驟。

以下範例以各種受支援的語言顯示建置 Web 應用的工作流部分。

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

要將程式碼部署到套用服務應用程式,請`azure/webapps-deploy@v2`使用操作 。 此操作有四個參數:

| **參數**  | **說明**  |
|---------|---------|
| **套用名稱** | ( 必要 )套用服務應用程式名稱 | 
| **設定檔** | ( 選擇性的 )使用 Web 部署機密發佈設定檔內容 |
| **套件** | ( 選擇性的 )包或資料夾的路徑。 *.zip、*.war、*.jar 或要部署的資料夾 |
| **插槽名稱** | ( 選擇性的 )輸入生產槽以外的現有插槽 |

### <a name="deploy-using-publish-profile"></a>使用設定設定檔進行部署

下面是使用發佈配置檔生成 Node.js 應用並將其部署到 Azure 的範例工作流。

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

### <a name="deploy-using-azure-service-principal"></a>使用 Azure 服務主體進行部署

下面是使用 Azure 服務主體生成 Node.js 應用並將其部署到 Azure 的範例工作流。

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

## <a name="next-steps"></a>後續步驟

您可以在 GitHub 上找到我們分組到不同儲存庫的操作集,每個儲存庫都包含文檔和範例,以説明您使用 GitHub 進行 CI/CD 並將應用部署到 Azure。

- [要部署到 Azure 的作業工作流](https://github.com/Azure/actions-workflow-samples)

- [Azure 登入](https://github.com/Azure/login)

- [Azure WebApp](https://github.com/Azure/webapps-deploy)

- [用於容器的 Azure WebApp](https://github.com/Azure/webapps-container-deploy)

- [Docker 登入/登出](https://github.com/Azure/docker-login)

- [觸發工作流的事件](https://help.github.com/en/articles/events-that-trigger-workflows)

- [K8s 部署](https://github.com/Azure/k8s-deploy)

- [初學者工作流](https://github.com/actions/starter-workflows)
