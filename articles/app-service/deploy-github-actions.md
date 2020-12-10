---
title: 使用 GitHub Actions 設定 CI/CD
description: 了解如何使用 GitHub Actions，從 CI/CD 管線將您的程式碼部署至 Azure App Service。 自訂建置工作並執行複雜的部署。
ms.devlang: na
ms.topic: article
ms.date: 09/14/2020
ms.author: jafreebe
ms.reviewer: ushan
ms.custom: devx-track-python, github-actions-azure, devx-track-azurecli
ms.openlocfilehash: b94e35f504a4c4d6e934ec01b06105f749031e35
ms.sourcegitcommit: 273c04022b0145aeab68eb6695b99944ac923465
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/10/2020
ms.locfileid: "97007376"
---
# <a name="deploy-to-app-service-using-github-actions"></a>使用 GitHub Actions 部署到 App Service

開始使用 [GitHub Actions](https://help.github.com/en/articles/about-github-actions) 將您的工作流程自動化，並從 GitHub 部署到 [Azure App Service](overview.md) 。 

## <a name="prerequisites"></a>必要條件 

- 具有有效訂用帳戶的 Azure 帳戶。 [免費建立帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
- GitHub 帳戶。 如果您沒有帳戶，請[免費](https://github.com/join)註冊。  
- 正常運作的 Azure App Service 應用程式。 
    - .NET： [在 Azure 中建立 ASP.NET Core web 應用程式](quickstart-dotnetcore.md)
    - ASP.NET： [在 Azure 中建立 ASP.NET Framework web 應用程式](quickstart-dotnet-framework.md)
    - JavaScript： [在 Azure App Service 中建立 Node.js web 應用程式](quickstart-nodejs.md)  
    - JAVA： [在 Azure App Service 上建立 java 應用程式](quickstart-java.md)
    - Python： [在 Azure App Service 中建立 python 應用程式](quickstart-python.md)

## <a name="workflow-file-overview"></a>工作流程檔案概觀

工作流程是由您存放庫內 `/.github/workflows/` 路徑中的 YAML (. yml) 檔案所定義的。 此定義包含組成工作流程的各種步驟與參數。

檔案有三個區段：

|區段  |工作  |
|---------|---------|
|**驗證** | 1. 定義服務主體或發行設定檔。 <br /> 2.建立 GitHub 祕密。 |
|**建置** | 1. 設定環境。 <br /> 2. 建立 web 應用程式。 |
|**部署** | 1. 部署 web 應用程式。 |

## <a name="use-the-deployment-center"></a>使用部署中心

您可以使用 App Service Deployment Center 快速開始 GitHub Actions。 這會根據您的應用程式堆疊自動產生工作流程檔案，並將其認可到正確目錄中的 GitHub 存放庫。

1. 在 Azure 入口網站中流覽至您的 webapp
1. 在左側，按一下 [**部署中心**]
1. 在 [**持續部署 (CI/CD)**] 下，選取 [ **GitHub** ]
1. 接下來，選取 **GitHub Actions**
1. 使用下拉式清單來選取您的 GitHub 存放庫、分支和應用程式堆疊
    - 如果選取的分支受到保護，您仍然可以繼續新增工作流程檔案。 繼續進行之前，請務必先審查您的分支保護。
1. 在最後一個畫面上，您可以檢查您的選取專案，並預覽將認可至存放庫的工作流程檔案。 如果選取專案正確無誤，請按一下 **[完成]** 。

這會將工作流程檔案認可至存放庫。 建立及部署應用程式的工作流程將會立即開始。

## <a name="set-up-a-workflow-manually"></a>手動設定工作流程

您也可以部署工作流程，而不需使用部署中心。 若要這樣做，您必須先產生部署認證。 

## <a name="generate-deployment-credentials"></a>產生部署認證

使用 Azure App Services 進行 GitHub Actions 驗證的建議方式是使用發行設定檔。 您也可以使用服務主體進行驗證，但此程式需要更多步驟。 

將您的發佈設定檔認證或服務主體儲存為 [GitHub 秘密](https://docs.github.com/en/actions/reference/encrypted-secrets) ，以向 Azure 進行驗證。 您將會在工作流程記憶體取秘密。 

# <a name="publish-profile"></a>[發行設定檔](#tab/applevel)

發行設定檔是應用層級的認證。 將您的發佈設定檔設定為 GitHub 秘密。 

1. 在 Azure 入口網站中，移至您的 app service。 

1. 在 [ **總覽** ] 頁面上，選取 [ **取得發行設定檔**]。

1. 儲存下載的檔案。 您將使用檔案的內容來建立 GitHub 秘密。

> [!NOTE]
> 從2020年10月起，Linux web 應用程式在 `WEBSITE_WEBDEPLOY_USE_SCM` `true` **下載發行設定檔之前**，必須先將應用程式設定設為。 未來將會移除這項需求。

# <a name="service-principal"></a>[服務主體](#tab/userlevel)

您可以使用 [Azure CLI](/cli/azure/) 中的 [az ad sp create-for-rbac](/cli/azure/ad/sp#az-ad-sp-create-for-rbac) 命令來建立[服務主體](../active-directory/develop/app-objects-and-service-principals.md#service-principal-object)。 請使用 Azure 入口網站中的 [Azure Cloud Shell](https://shell.azure.com/)，或選取 [試試看] 按鈕來執行此命令。

```azurecli-interactive
az ad sp create-for-rbac --name "myApp" --role contributor \
                            --scopes /subscriptions/<subscription-id>/resourceGroups/<group-name>/providers/Microsoft.Web/sites/<app-name> \
                            --sdk-auth
```

在上述範例中，請將預留位置取代為您的訂用帳戶識別碼、資源組名和應用程式名稱。 輸出是一個 JSON 物件，內有角色指派認證可讓您存取 App Service 應用程式，如下所示。 複製此 JSON 物件以供後續使用。

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


# <a name="publish-profile"></a>[發行設定檔](#tab/applevel)

在 [GitHub](https://github.com/)中，流覽您的存放庫，選取 **> 秘密 > 新增密碼的設定**。

若要使用 [應用層級的認證](#generate-deployment-credentials)，請將所下載發行設定檔的內容貼入秘密的 [值] 欄位中。 為秘密命名 `AZURE_WEBAPP_PUBLISH_PROFILE` 。

當您設定 GitHub 工作流程時，您會 `AZURE_WEBAPP_PUBLISH_PROFILE` 在 [部署 Azure Web 應用程式] 動作中使用。 例如：
    
```yaml
- uses: azure/webapps-deploy@v2
  with:
    publish-profile: ${{ secrets.AZURE_WEBAPP_PUBLISH_PROFILE }}
```

# <a name="service-principal"></a>[服務主體](#tab/userlevel)

在 [GitHub](https://github.com/)中，流覽您的存放庫，選取 **> 秘密 > 新增密碼的設定**。

若要使用 [使用者層級的認證](#generate-deployment-credentials)，請將 Azure CLI 命令中的整個 JSON 輸出貼到秘密的值欄位中。 將祕密命名為 `AZURE_CREDENTIALS`。

當您稍後設定工作流程檔案時，會將祕密用於 Azure 登入動作的輸入 `creds`。 例如：

```yaml
- uses: azure/login@v1
  with:
    creds: ${{ secrets.AZURE_CREDENTIALS }}
```

---

## <a name="set-up-the-environment"></a>設定 Azure 環境

設定環境的作業可以使用其中一個設定動作來完成。

|**語言**  |**設定動作**  |
|---------|---------|
|**.NET**     | `actions/setup-dotnet` |
|**ASP.NET**     | `actions/setup-dotnet` |
|**Java**     | `actions/setup-java` |
|**JavaScript** | `actions/setup-node` |
|**Python**     | `actions/setup-python` |

下列範例示範如何設定不同支援語言的環境：

**.NET**

```yaml
    - name: Setup Dotnet 3.3.x
      uses: actions/setup-dotnet@v1
      with:
        dotnet-version: '3.3.x'
```

**ASP.NET**

```yaml
    - name: Install Nuget
      uses: nuget/setup-nuget@v1
      with:
        nuget-version: ${{ env.NUGET_VERSION}}
```

**Java**

```yaml
    - name: Setup Java 1.8.x
      uses: actions/setup-java@v1
      with:
        # If your pom.xml <maven.compiler.source> version is not in 1.8.x,
        # change the Java version to match the version in pom.xml <maven.compiler.source>
        java-version: '1.8.x'
```

**JavaScript**

```yaml
env:
  NODE_VERSION: '14.x'                # set this to the node version to use

jobs:
  build-and-deploy:
    name: Build and Deploy
    runs-on: ubuntu-latest
    steps:
    - uses: actions/checkout@master
    - name: Use Node.js ${{ env.NODE_VERSION }}
      uses: actions/setup-node@v1
      with:
        node-version: ${{ env.NODE_VERSION }}
```
**Python**

```yaml
    - name: Setup Python 3.x 
      uses: actions/setup-python@v1
      with:
        python-version: 3.x
```

## <a name="build-the-web-app"></a>建置 Web 應用程式

根據語言，建立 web 應用程式並部署至 Azure App Service 變更的程式。 

下列範例顯示以不同的支援語言建立 web 應用程式的工作流程部分。

針對所有語言，您可以使用來設定 web 應用程式根目錄 `working-directory` 。 

**.NET**

環境變數會 `AZURE_WEBAPP_PACKAGE_PATH` 設定 web 應用程式專案的路徑。 

```yaml
- name: dotnet build and publish
  run: |
    dotnet restore
    dotnet build --configuration Release
    dotnet publish -c Release -o '${{ env.AZURE_WEBAPP_PACKAGE_PATH }}/myapp' 
```
**ASP.NET**

您可以還原 NuGet 相依性，並使用執行 msbuild `run` 。 

```yaml
- name: NuGet to restore dependencies as well as project-specific tools that are specified in the project file
  run: nuget restore

- name: Add msbuild to PATH
  uses: microsoft/setup-msbuild@v1.0.0

- name: Run msbuild
  run: msbuild .\SampleWebApplication.sln
```

**Java**

```yaml
- name: Build with Maven
  run: mvn package --file pom.xml
```

**JavaScript**

針對 Node.js，您可以 `working-directory` 在中設定或變更 npm 目錄 `pushd` 。 

```yaml
- name: npm install, build, and test
  run: |
    npm install
    npm run build --if-present
    npm run test --if-present
  working-directory: my-app-folder # set to the folder with your app if it is not the root directory
```

**Python**

```yaml
- name: Install dependencies
  run: |
    python -m pip install --upgrade pip
    pip install -r requirements.txt
```


## <a name="deploy-to-app-service"></a>部署到 App Service

若要將程式碼部署到 App Service 應用程式，請使用 `azure/webapps-deploy@v2` 動作。 此動作有四個參數：

| **參數**  | **說明**  |
|---------|---------|
| **app-name** | (必要) App Service 應用程式的名稱 | 
| **publish-profile** | (選擇性) 包含 Web Deploy 祕密的發行設定檔檔案內容 |
| **套件** | (選擇性) 套件或資料夾的路徑。 路徑可以包含 * .zip、*. war、* .jar 或要部署的資料夾 |
| **slot-name** |  (選擇性) [輸入生產位置](deploy-staging-slots.md)以外的現有插槽 |


# <a name="publish-profile"></a>[發行設定檔](#tab/applevel)

### <a name="net-core"></a>.NET Core

使用 Azure 發行設定檔來建立 .NET Core 應用程式，並將其部署到 Azure。 `publish-profile`輸入 `AZURE_WEBAPP_PUBLISH_PROFILE` 會參考您稍早建立的秘密。

```yaml
name: .NET Core CI

on: [push]

env:
  AZURE_WEBAPP_NAME: my-app-name    # set this to your application's name
  AZURE_WEBAPP_PACKAGE_PATH: '.'      # set this to the path to your web app project, defaults to the repository root
  DOTNET_VERSION: '3.1.x'           # set this to the dot net version to use

jobs:
  build:
    runs-on: ubuntu-latest

    steps:
      # Checkout the repo
      - uses: actions/checkout@master
      
      # Setup .NET Core SDK
      - name: Setup .NET Core
        uses: actions/setup-dotnet@v1
        with:
          dotnet-version: ${{ env.DOTNET_VERSION }} 
      
      # Run dotnet build and publish
      - name: dotnet build and publish
        run: |
          dotnet restore
          dotnet build --configuration Release
          dotnet publish -c Release -o '${{ env.AZURE_WEBAPP_PACKAGE_PATH }}/myapp' 
          
      # Deploy to Azure Web apps
      - name: 'Run Azure webapp deploy action using publish profile credentials'
        uses: azure/webapps-deploy@v2
        with: 
          app-name: ${{ env.AZURE_WEBAPP_NAME }} # Replace with your app name
          publish-profile: ${{ secrets.AZURE_WEBAPP_PUBLISH_PROFILE  }} # Define secret variable in repository settings as per action documentation
          package: '${{ env.AZURE_WEBAPP_PACKAGE_PATH }}/myapp'
```

### <a name="aspnet"></a>ASP.NET

建立並部署使用 NuGet 和驗證的 ASP.NET MVC 應用程式 `publish-profile` 。 


```yaml
name: Deploy ASP.NET MVC App deploy to Azure Web App

on: [push]

env:
  AZURE_WEBAPP_NAME: my-app    # set this to your application's name
  AZURE_WEBAPP_PACKAGE_PATH: '.'      # set this to the path to your web app project, defaults to the repository root
  NUGET_VERSION: '5.3.x'           # set this to the dot net version to use

jobs:
  build-and-deploy:
    runs-on: windows-latest
    steps:

    - uses: actions/checkout@master  
    
    - name: Install Nuget
      uses: nuget/setup-nuget@v1
      with:
        nuget-version: ${{ env.NUGET_VERSION}}
    - name: NuGet to restore dependencies as well as project-specific tools that are specified in the project file
      run: nuget restore
  
    - name: Add msbuild to PATH
      uses: microsoft/setup-msbuild@v1.0.0

    - name: Run MSBuild
      run: msbuild .\SampleWebApplication.sln
       
    - name: 'Run Azure webapp deploy action using publish profile credentials'
      uses: azure/webapps-deploy@v2
      with: 
        app-name: ${{ env.AZURE_WEBAPP_NAME }} # Replace with your app name
        publish-profile: ${{ secrets.AZURE_WEBAPP_PUBLISH_PROFILE  }} # Define secret variable in repository settings as per action documentation
        package: '${{ env.AZURE_WEBAPP_PACKAGE_PATH }}/SampleWebApplication/'
```

### <a name="java"></a>Java

使用 Azure 發行設定檔來建立 JAVA 春季應用程式，並將其部署到 Azure。 `publish-profile`輸入 `AZURE_WEBAPP_PUBLISH_PROFILE` 會參考您稍早建立的秘密。

```yaml
name: Java CI with Maven

on: [push]

jobs:
  build:

    runs-on: ubuntu-latest

    steps:
    - uses: actions/checkout@v2
    - name: Set up JDK 1.8
      uses: actions/setup-java@v1
      with:
        java-version: 1.8
    - name: Build with Maven
      run: mvn -B package --file pom.xml
      working-directory: my-app-path
    - name: Azure WebApp
      uses: Azure/webapps-deploy@v2
      with:
        app-name: my-app-name
        publish-profile: ${{ secrets.AZURE_WEBAPP_PUBLISH_PROFILE }}
        package: my/target/*.jar
```

若要部署 `war` 而不是 `jar` ，請變更此 `package` 值。 


```yaml
    - name: Azure WebApp
      uses: Azure/webapps-deploy@v2
      with:
        app-name: my-app-name
        publish-profile: ${{ secrets.AZURE_WEBAPP_PUBLISH_PROFILE }}
        package: my/target/*.war
```

### <a name="javascript"></a>JavaScript 

使用應用程式的發行設定檔，建立 Node.js 應用程式並部署到 Azure。 `publish-profile`輸入 `AZURE_WEBAPP_PUBLISH_PROFILE` 會參考您稍早建立的秘密。

```yaml
# File: .github/workflows/workflow.yml
name: JavaScript CI

on: [push]

env:
  AZURE_WEBAPP_NAME: my-app-name   # set this to your application's name
  AZURE_WEBAPP_PACKAGE_PATH: 'my-app-path'      # set this to the path to your web app project, defaults to the repository root
  NODE_VERSION: '14.x'                # set this to the node version to use

jobs:
  build-and-deploy:
    name: Build and Deploy
    runs-on: ubuntu-latest
    steps:
    - uses: actions/checkout@master
    - name: Use Node.js ${{ env.NODE_VERSION }}
      uses: actions/setup-node@v1
      with:
        node-version: ${{ env.NODE_VERSION }}
    - name: npm install, build, and test
      run: |
        # Build and test the project, then
        # deploy to Azure Web App.
        npm install
        npm run build --if-present
        npm run test --if-present
      working-directory: my-app-path
    - name: 'Deploy to Azure WebApp'
      uses: azure/webapps-deploy@v2
      with: 
        app-name: ${{ env.AZURE_WEBAPP_NAME }}
        publish-profile: ${{ secrets.AZURE_WEBAPP_PUBLISH_PROFILE }}
        package: ${{ env.AZURE_WEBAPP_PACKAGE_PATH }}
```

### <a name="python"></a>Python 

使用應用程式的發行設定檔，建立 Python 應用程式並部署到 Azure。 請注意 `publish-profile` 輸入如何參考 `AZURE_WEBAPP_PUBLISH_PROFILE` 您稍早建立的秘密。

```yaml
name: Python CI

on:
  [push]

env:
  AZURE_WEBAPP_NAME: my-web-app # set this to your application's name
  AZURE_WEBAPP_PACKAGE_PATH: '.' # set this to the path to your web app project, defaults to the repository root

jobs:
  build:
    runs-on: ubuntu-latest
    steps:
    - uses: actions/checkout@v2
    - name: Set up Python 3.x
      uses: actions/setup-python@v2
      with:
        python-version: 3.x
    - name: Install dependencies
      run: |
        python -m pip install --upgrade pip
        pip install -r requirements.txt
    - name: Building web app
      uses: azure/appservice-build@v2
    - name: Deploy web App using GH Action azure/webapps-deploy
      uses: azure/webapps-deploy@v2
      with:
        app-name: ${{ env.AZURE_WEBAPP_NAME }}
        publish-profile: ${{ secrets.AZURE_WEBAPP_PUBLISH_PROFILE }}
        package: ${{ env.AZURE_WEBAPP_PACKAGE_PATH }}
```

# <a name="service-principal"></a>[服務主體](#tab/userlevel)

### <a name="net-core"></a>.NET Core 

使用 Azure 服務主體來建立 .NET Core 應用程式，並將其部署到 Azure。 請注意 `creds` 輸入如何參考 `AZURE_CREDENTIALS` 您稍早建立的秘密。


```yaml
name: .NET Core

on: [push]

env:
  AZURE_WEBAPP_NAME: my-app    # set this to your application's name
  AZURE_WEBAPP_PACKAGE_PATH: '.'      # set this to the path to your web app project, defaults to the repository root
  DOTNET_VERSION: '3.1.x'           # set this to the dot net version to use

jobs:
  build:
    runs-on: ubuntu-latest

    steps:
      # Checkout the repo
      - uses: actions/checkout@master
      - uses: azure/login@v1
        with:
          creds: ${{ secrets.AZURE_CREDENTIALS }}

      
      # Setup .NET Core SDK
      - name: Setup .NET Core
        uses: actions/setup-dotnet@v1
        with:
          dotnet-version: ${{ env.DOTNET_VERSION }} 
      
      # Run dotnet build and publish
      - name: dotnet build and publish
        run: |
          dotnet restore
          dotnet build --configuration Release
          dotnet publish -c Release -o '${{ env.AZURE_WEBAPP_PACKAGE_PATH }}/myapp' 
          
      # Deploy to Azure Web apps
      - name: 'Run Azure webapp deploy action using publish profile credentials'
        uses: azure/webapps-deploy@v2
        with: 
          app-name: ${{ env.AZURE_WEBAPP_NAME }} # Replace with your app name
          package: '${{ env.AZURE_WEBAPP_PACKAGE_PATH }}/myapp'
      
      - name: logout
        run: |
          az logout
```

### <a name="aspnet"></a>ASP.NET

使用 Azure 服務主體建立 ASP.NET MVC 應用程式，並將其部署到 Azure。 請注意 `creds` 輸入如何參考 `AZURE_CREDENTIALS` 您稍早建立的秘密。

```yaml
name: Deploy ASP.NET MVC App deploy to Azure Web App

on: [push]

env:
  AZURE_WEBAPP_NAME: my-app    # set this to your application's name
  AZURE_WEBAPP_PACKAGE_PATH: '.'      # set this to the path to your web app project, defaults to the repository root
  NUGET_VERSION: '5.3.x'           # set this to the dot net version to use

jobs:
  build-and-deploy:
    runs-on: windows-latest
    steps:

    # checkout the repo
    - uses: actions/checkout@master  
    
    - uses: azure/login@v1
      with:
        creds: ${{ secrets.AZURE_CREDENTIALS }}

    - name: Install Nuget
      uses: nuget/setup-nuget@v1
      with:
        nuget-version: ${{ env.NUGET_VERSION}}
    - name: NuGet to restore dependencies as well as project-specific tools that are specified in the project file
      run: nuget restore
  
    - name: Add msbuild to PATH
      uses: microsoft/setup-msbuild@v1.0.0

    - name: Run MSBuild
      run: msbuild .\SampleWebApplication.sln
       
    - name: 'Run Azure webapp deploy action using publish profile credentials'
      uses: azure/webapps-deploy@v2
      with: 
        app-name: ${{ env.AZURE_WEBAPP_NAME }} # Replace with your app name
        package: '${{ env.AZURE_WEBAPP_PACKAGE_PATH }}/SampleWebApplication/'
  
    # Azure logout 
    - name: logout
      run: |
        az logout
```

### <a name="java"></a>Java 

使用 Azure 服務主體來建立 JAVA 春季應用程式，並將其部署到 Azure。 請注意 `creds` 輸入如何參考 `AZURE_CREDENTIALS` 您稍早建立的秘密。

```yaml
name: Java CI with Maven

on: [push]

jobs:
  build:

    runs-on: ubuntu-latest

    steps:
    - uses: actions/checkout@v2
    - uses: azure/login@v1
      with:
        creds: ${{ secrets.AZURE_CREDENTIALS }}
    - name: Set up JDK 1.8
      uses: actions/setup-java@v1
      with:
        java-version: 1.8
    - name: Build with Maven
      run: mvn -B package --file pom.xml
      working-directory: complete
    - name: Azure WebApp
      uses: Azure/webapps-deploy@v2
      with:
        app-name: my-app-name
        package: my/target/*.jar

    # Azure logout 
    - name: logout
      run: |
        az logout
```

### <a name="javascript"></a>JavaScript 

使用 Azure 服務主體建立 Node.js 應用程式，並將其部署到 Azure。 請注意 `creds` 輸入如何參考 `AZURE_CREDENTIALS` 您稍早建立的秘密。

```yaml
name: JavaScript CI

on: [push]

name: Node.js

env:
  AZURE_WEBAPP_NAME: my-app   # set this to your application's name
  NODE_VERSION: '14.x'                # set this to the node version to use

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
        
    - name: Setup Node ${{ env.NODE_VERSION }}
      uses: actions/setup-node@v1
      with:
        node-version: ${{ env.NODE_VERSION }}
    
    - name: 'npm install, build, and test'
      run: |
        npm install
        npm run build --if-present
        npm run test --if-present
      working-directory:  my-app-path
               
    # deploy web app using Azure credentials
    - uses: azure/webapps-deploy@v2
      with:
        app-name: ${{ env.AZURE_WEBAPP_NAME }}
        package: ${{ env.AZURE_WEBAPP_PACKAGE_PATH }}

    # Azure logout 
    - name: logout
      run: |
        az logout
```

### <a name="python"></a>Python 

使用 Azure 服務主體建立 Python 應用程式，並將其部署到 Azure。 請注意 `creds` 輸入如何參考 `AZURE_CREDENTIALS` 您稍早建立的秘密。

```yaml
name: Python application

on:
  [push]

env:
  AZURE_WEBAPP_NAME: my-app # set this to your application's name
  AZURE_WEBAPP_PACKAGE_PATH: '.' # set this to the path to your web app project, defaults to the repository root

jobs:
  build:
    runs-on: ubuntu-latest
    steps:
    - uses: actions/checkout@v2
    
    - uses: azure/login@v1
      with:
        creds: ${{ secrets.AZURE_CREDENTIALS }}

    - name: Set up Python 3.x
      uses: actions/setup-python@v2
      with:
        python-version: 3.x
    - name: Install dependencies
      run: |
        python -m pip install --upgrade pip
        pip install -r requirements.txt
    - name: Deploy web App using GH Action azure/webapps-deploy
      uses: azure/webapps-deploy@v2
      with:
        app-name: ${{ env.AZURE_WEBAPP_NAME }}
        package: ${{ env.AZURE_WEBAPP_PACKAGE_PATH }}
    - name: logout
      run: |
        az logout
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
