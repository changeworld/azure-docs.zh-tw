---
title: 使用 GitHub Actions 的 Azure 春季 Cloud CI/CD
description: 如何使用 GitHub Actions 建立適用于 Azure 春季 Cloud 的 CI/CD 工作流程
author: MikeDodaro
ms.author: barbkess
ms.service: spring-cloud
ms.topic: how-to
ms.date: 09/08/2020
ms.custom: devx-track-java, devx-track-azurecli
zone_pivot_groups: programming-languages-spring-cloud
ms.openlocfilehash: 95a97d3f5104891ef5e23a03ffe1477f9b6ad036
ms.sourcegitcommit: 77ab078e255034bd1a8db499eec6fe9b093a8e4f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/16/2020
ms.locfileid: "97561608"
---
# <a name="azure-spring-cloud-cicd-with-github-actions"></a>使用 GitHub Actions 的 Azure 春季 Cloud CI/CD

GitHub Actions 支援自動化軟體發展生命週期工作流程。 您可以使用適用于 Azure 春季雲端的 GitHub Actions，在您的存放庫中建立工作流程，以建立、測試、封裝、發行及部署至 Azure。 

## <a name="prerequisites"></a>必要條件
此範例需要 [Azure CLI](/cli/azure/install-azure-cli?preserve-view=true&view=azure-cli-latest)。

::: zone pivot="programming-language-csharp"
## <a name="set-up-github-repository-and-authenticate"></a>設定 GitHub 存放庫並進行驗證
您必須要有 Azure 服務主體認證，才能授權 Azure 登入動作。 若要取得 Azure 認證，請在您的本機電腦上執行下列命令：

```
az login
az ad sp create-for-rbac --role contributor --scopes /subscriptions/<SUBSCRIPTION_ID> --sdk-auth 
```

若要存取特定的資源群組，您可以減少範圍：

```
az ad sp create-for-rbac --role contributor --scopes /subscriptions/<SUBSCRIPTION_ID>/resourceGroups/<RESOURCE_GROUP> --sdk-auth
```

此命令應該會輸出 JSON 物件：

```JSON
{
    "clientId": "<GUID>",
    "clientSecret": "<GUID>",
    "subscriptionId": "<GUID>",
    "tenantId": "<GUID>",
    ...
}
```

此範例會使用 [GitHub 上的 steeltoe 範例](https://github.com/Azure-Samples/Azure-Spring-Cloud-Samples/tree/master/steeltoe-sample)。  派生存放庫，開啟分叉的 GitHub 存放庫頁面，然後選取 [ **設定** ] 索引標籤。開啟 [ **秘密** ] 功能表，然後選取 [ **新增密碼**]：

 ![新增秘密](./media/github-actions/actions1.png)

將秘密名稱設為 `AZURE_CREDENTIALS` ，並將其值設定為您在 [ *設定您的 GitHub 存放庫] 和 [驗證*] 標題下找到的 JSON 字串。

 ![設定秘密資料](./media/github-actions/actions2.png)

您也可以從 GitHub 動作中的 Key Vault 取得 Azure 登入認證，如 [使用 GitHub Actions 中的 Key Vault 驗證 Azure 春季](./spring-cloud-github-actions-key-vault.md)所述。

## <a name="provision-service-instance"></a>布建服務實例
若要布建您的 Azure 春季 Cloud 服務實例，請使用 Azure CLI 執行下列命令。

```azurecli
az extension add --name spring-cloud
az group create --location eastus --name <resource group name>
az spring-cloud create -n <service instance name> -g <resource group name>
az spring-cloud config-server git set -n <service instance name> --uri https://github.com/xxx/Azure-Spring-Cloud-Samples --label main --search-paths steeltoe-sample/config
```

## <a name="build-the-workflow"></a>建立工作流程
您可以使用下列選項來定義工作流程。

### <a name="prepare-for-deployment-with-azure-cli"></a>使用 Azure CLI 準備部署

此命令 `az spring-cloud app create` 目前不具等冪性。 執行一次之後，如果您再次執行相同的命令，將會收到錯誤。 我們建議您在現有的 Azure 春季雲端應用程式和實例上進行此工作流程。

使用下列 Azure CLI 命令進行準備：
```
az configure --defaults group=<service group name>
az configure --defaults spring-cloud=<service instance name>
az spring-cloud app create --name planet-weather-provider
az spring-cloud app create --name solar-system-weather
```

### <a name="deploy-with-azure-cli-directly"></a>直接使用 Azure CLI 部署

`.github/workflows/main.yml`使用下列內容在存放庫中建立檔案。 `<your resource group name>` `<your service name>` 以正確的值取代和。

```yaml
name: Steeltoe-CD

# Controls when the action will run. Triggers the workflow on push or pull request
# events but only for the main branch
on:
  push:
    branches: [ main]

# A workflow run is made up of one or more jobs that can run sequentially or in parallel
jobs:
  # This workflow contains a single job called "build"
  build:
    # The type of runner that the job will run on
    runs-on: ubuntu-latest
    env:
      working-directory: ./steeltoe-sample
      resource-group-name: <your resource group name>
      service-name: <your service name>
    
    # Supported .NET Core version matrix.
    strategy:
      matrix:
        dotnet: [ '3.1.x' ]

    # Steps represent a sequence of tasks that will be executed as part of the job
    steps:
      # Checks-out your repository under $GITHUB_WORKSPACE, so your job can access it
      - uses: actions/checkout@v2

      # Set up .NET Core 3.1 SDK
      - uses: actions/setup-dotnet@v1
        with:
          dotnet-version: ${{ matrix.dotnet }}
          
      # Set credential for az login
      - uses: azure/login@v1.1
        with:
          creds: ${{ secrets.AZURE_CREDENTIALS }}

      - name: install Azure CLI extension
        run: |
          az extension add --name spring-cloud --yes
      
      - name: Build and package planet-weather-provider app
        working-directory: ${{env.working-directory}}/src/planet-weather-provider
        run: |
          dotnet publish
          az spring-cloud app deploy -n planet-weather-provider --runtime-version NetCore_31 --main-entry Microsoft.Azure.SpringCloud.Sample.PlanetWeatherProvider.dll --artifact-path ./publish-deploy-planet.zip -s ${{ env.service-name }} -g ${{ env.resource-group-name }}
      - name: Build solar-system-weather app
        working-directory: ${{env.working-directory}}/src/solar-system-weather
        run: |
          dotnet publish
          az spring-cloud app deploy -n solar-system-weather --runtime-version NetCore_31 --main-entry Microsoft.Azure.SpringCloud.Sample.SolarSystemWeather.dll --artifact-path ./publish-deploy-solar.zip -s ${{ env.service-name }} -g ${{ env.resource-group-name }}
```
::: zone-end

::: zone pivot="programming-language-java"
## <a name="set-up-github-repository-and-authenticate"></a>設定 GitHub 存放庫並進行驗證
您必須要有 Azure 服務主體認證，才能授權 Azure 登入動作。 若要取得 Azure 認證，請在您的本機電腦上執行下列命令：
```
az login
az ad sp create-for-rbac --role contributor --scopes /subscriptions/<SUBSCRIPTION_ID> --sdk-auth 
```
若要存取特定的資源群組，您可以減少範圍：
```
az ad sp create-for-rbac --role contributor --scopes /subscriptions/<SUBSCRIPTION_ID>/resourceGroups/<RESOURCE_GROUP> --sdk-auth
```
此命令應該會輸出 JSON 物件：
```JSON
{
    "clientId": "<GUID>",
    "clientSecret": "<GUID>",
    "subscriptionId": "<GUID>",
    "tenantId": "<GUID>",
    ...
}
```

此範例會使用 GitHub 上的 [PiggyMetrics](https://github.com/Azure-Samples/piggymetrics) 範例。  派生範例，開啟 GitHub 存放庫頁面，然後按一下 [ **設定** ] 索引標籤。開啟 [ **秘密** ] 功能表，然後按一下 [新增 **密碼**]：

 ![新增秘密](./media/github-actions/actions1.png)

將秘密名稱設為 `AZURE_CREDENTIALS` ，並將其值設定為您在 [ *設定您的 GitHub 存放庫] 和 [驗證*] 標題下找到的 JSON 字串。

 ![設定秘密資料](./media/github-actions/actions2.png)

您也可以從 GitHub 動作中的 Key Vault 取得 Azure 登入認證，如 [使用 GitHub Actions 中的 Key Vault 驗證 Azure 春季](./spring-cloud-github-actions-key-vault.md)所述。

## <a name="provision-service-instance"></a>布建服務實例
若要布建您的 Azure 春季 Cloud 服務實例，請使用 Azure CLI 執行下列命令。
```
az extension add --name spring-cloud
az group create --location eastus --name <resource group name>
az spring-cloud create -n <service instance name> -g <resource group name>
az spring-cloud config-server git set -n <service instance name> --uri https://github.com/xxx/piggymetrics --label config
```
## <a name="build-the-workflow"></a>建立工作流程
您可以使用下列選項來定義工作流程。

### <a name="prepare-for-deployment-with-azure-cli"></a>使用 Azure CLI 準備部署
此命令 `az spring-cloud app create` 目前不具等冪性。  我們建議您在現有的 Azure 春季雲端應用程式和實例上進行此工作流程。

使用下列 Azure CLI 命令進行準備：
```
az configure --defaults group=<service group name>
az configure --defaults spring-cloud=<service instance name>
az spring-cloud app create --name gateway
az spring-cloud app create --name auth-service
az spring-cloud app create --name account-service
```

### <a name="deploy-with-azure-cli-directly"></a>直接使用 Azure CLI 部署
在存放 `.github/workflow/main.yml` 庫中建立檔案：

```
name: AzureSpringCloud
on: push

env:
  GROUP: <resource group name>
  SERVICE_NAME: <service instance name>

jobs:
  build-and-deploy:
    runs-on: ubuntu-latest
    steps:
    
    - uses: actions/checkout@main
    
    - name: Set up JDK 1.8
      uses: actions/setup-java@v1
      with:
        java-version: 1.8
    
    - name: maven build, clean
      run: |
        mvn clean package -DskipTests
    
    - name: Azure Login
      uses: azure/login@v1
      with:
        creds: ${{ secrets.AZURE_CREDENTIALS }}
      
    - name: Install ASC AZ extension
      run: az extension add --name spring-cloud
   
    - name: Deploy with AZ CLI commands
      run: |
        az configure --defaults group=$GROUP
        az configure --defaults spring-cloud=$SERVICE_NAME
        az spring-cloud app deploy -n gateway --jar-path ${{ github.workspace }}/gateway/target/gateway.jar
        az spring-cloud app deploy -n account-service --jar-path ${{ github.workspace }}/account-service/target/account-service.jar
        az spring-cloud app deploy -n auth-service --jar-path ${{ github.workspace }}/auth-service/target/auth-service.jar
```
### <a name="deploy-with-azure-cli-action"></a>使用 Azure CLI 動作進行部署
Az `run` 命令將會使用最新版本的 Azure CLI。 如果有重大變更，您也可以使用特定版本的 Azure CLI 搭配 Azure/CLI `action` 。 

> [!Note] 
> 此命令將會在新容器中執行，因此 `env` 將無法運作，而且跨動作檔案存取可能會有額外的限制。

在存放庫中建立 github/workflow/yml 檔案：
```
name: AzureSpringCloud
on: push

jobs:
  build-and-deploy:
    runs-on: ubuntu-latest
    steps:
    
    - uses: actions/checkout@main
    
    - name: Set up JDK 1.8
      uses: actions/setup-java@v1
      with:
        java-version: 1.8
    
    - name: maven build, clean
      run: |
        mvn clean package -DskipTests
        
    - name: Azure Login
      uses: azure/login@v1
      with:
        creds: ${{ secrets.AZURE_CREDENTIALS }}
              
    - name: Azure CLI script
      uses: azure/CLI@v1
      with:
        azcliversion: 2.0.75
        inlineScript: |
          az extension add --name spring-cloud
          az configure --defaults group=<service group name>
          az configure --defaults spring-cloud=<service instance name>
          az spring-cloud app deploy -n gateway --jar-path $GITHUB_WORKSPACE/gateway/target/gateway.jar
          az spring-cloud app deploy -n account-service --jar-path $GITHUB_WORKSPACE/account-service/target/account-service.jar
          az spring-cloud app deploy -n auth-service --jar-path $GITHUB_WORKSPACE/auth-service/target/auth-service.jar
```

## <a name="deploy-with-maven-plugin"></a>使用 Maven 外掛程式部署
另一個選項是使用 [Maven 外掛程式](./spring-cloud-quickstart.md) 來部署 Jar 和更新應用程式設定。 此命令 `mvn azure-spring-cloud:deploy` 為等冪，並且會在需要時自動建立應用程式。 您不需要事先建立對應的應用程式。

```
name: AzureSpringCloud
on: push

jobs:
  build-and-deploy:
    runs-on: ubuntu-latest
    steps:
    
    - uses: actions/checkout@main
    
    - name: Set up JDK 1.8
      uses: actions/setup-java@v1
      with:
        java-version: 1.8
    
    - name: maven build, clean
      run: |
        mvn clean package -DskipTests
        
    # Maven plugin can cosume this authentication method automatically
    - name: Azure Login
      uses: azure/login@v1
      with:
        creds: ${{ secrets.AZURE_CREDENTIALS }}
    
    # Maven deploy, make sure you have correct configurations in your pom.xml
    - name: deploy to Azure Spring Cloud using Maven
      run: |
        mvn azure-spring-cloud:deploy
```

::: zone-end

## <a name="run-the-workflow"></a>執行工作流程

您必須在推送至 GitHub 之後，自動啟用 GitHub **動作** `.github/workflow/main.yml` 。 當您推送新的認可時，將會觸發此動作。 如果您在瀏覽器中建立這個檔案，您的動作應該已經執行。

若要確認已啟用該動作，請按一下 GitHub 存放庫頁面上的 [ **動作** ] 索引標籤：

![確認動作已啟用](./media/github-actions/actions3.png)

如果您的動作執行錯誤（例如，如果您尚未設定 Azure 認證），您可以在修正錯誤後重新執行檢查。 在 [GitHub 存放庫] 頁面上，按一下 [ **動作**]，選取特定的工作流程工作，然後按一下 [ **重新執行檢查** ] 按鈕以重新執行檢查：

![重新執行檢查](./media/github-actions/actions4.png)

## <a name="next-steps"></a>後續步驟

* [適用于春季雲端 GitHub 動作的 Key Vault](./spring-cloud-github-actions-key-vault.md)
* [Azure Active Directory 服務主體](/cli/azure/ad/sp?preserve-view=true&view=azure-cli-latest#az-ad-sp-create-for-rbac)
* [適用於 Azure 的 GitHub Actions](https://github.com/Azure/actions/)
