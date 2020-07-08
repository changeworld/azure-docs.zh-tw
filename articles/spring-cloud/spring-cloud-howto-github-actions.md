---
title: 具有 GitHub 動作的 Azure 春季雲端 CI/CD
description: 如何使用 GitHub 動作來建立 Azure 春季雲端的 CI/CD 工作流程
author: MikeDodaro
ms.author: barbkess
ms.service: spring-cloud
ms.topic: how-to
ms.date: 01/15/2019
ms.openlocfilehash: e17fa3e99de9f429fa279ba2e5413b60e9084de8
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "85125697"
---
# <a name="azure-spring-cloud-cicd-with-github-actions"></a>具有 GitHub 動作的 Azure 春季雲端 CI/CD

GitHub 動作支援自動化軟體發展生命週期工作流程。 透過 Azure 春季雲端的 GitHub 動作，您可以在存放庫中建立工作流程，以建立、測試、封裝、發行和部署至 Azure。 

## <a name="prerequisites"></a>必要條件
這個範例需要[Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)。

## <a name="set-up-github-repository-and-authenticate"></a>設定 GitHub 存放庫並進行驗證
您需要 Azure 服務主體認證，才能授權 Azure 登入動作。 若要取得 Azure 認證，請在您的本機電腦上執行下列命令：
```
az login
az ad sp create-for-rbac --role contributor --scopes /subscriptions/<SUBSCRIPTION_ID> --sdk-auth 
```
若要存取特定的資源群組，您可以減少範圍：
```
az ad sp create-for-rbac --role contributor --scopes /subscriptions/<SUBSCRIPTION_ID>/resourceGroups/<RESOURCE_GROUP> --sdk-auth
```
此命令應輸出 JSON 物件：
```JSON
{
    "clientId": "<GUID>",
    "clientSecret": "<GUID>",
    "subscriptionId": "<GUID>",
    "tenantId": "<GUID>",
    ...
}
```

這個範例會使用 GitHub 上的[Piggy-back 計量](https://github.com/Azure-Samples/piggymetrics)範例。  派生範例，開啟 GitHub 存放庫頁面，然後按一下 [**設定**] 索引標籤。開啟 [**秘密**] 功能表，然後按一下 [新增**密碼**]：

 ![加入新的秘密](./media/github-actions/actions1.png)

將秘密名稱設為 `AZURE_CREDENTIALS` ，並將其值設定為您在*設定 GitHub 存放庫和驗證*標題底下找到的 JSON 字串。

 ![設定秘密資料](./media/github-actions/actions2.png)

您也可以從 GitHub 動作中的 Key Vault 取得 Azure 登入認證，如在[Github 動作中使用 Key Vault 驗證 Azure 春季](./spring-cloud-github-actions-key-vault.md)中所述。

## <a name="provision-service-instance"></a>布建服務實例
若要布建您的 Azure 春季雲端服務實例，請使用 Azure CLI 執行下列命令。
```
az extension add --name spring-cloud
az group create --location eastus --name <resource group name>
az spring-cloud create -n <service instance name> -g <resource group name>
az spring-cloud config-server git set -n <service instance name> --uri https://github.com/xxx/piggymetrics --label config
```
## <a name="build-the-workflow"></a>建立工作流程
工作流程是使用下列選項來定義。

### <a name="prepare-for-deployment-with-azure-cli"></a>準備使用 Azure CLI 進行部署
命令 `az spring-cloud app create` 目前不具等冪性。  我們建議您在現有的 Azure 春季雲端應用程式和實例上進行此工作流程。

使用下列 Azure CLI 命令進行準備：
```
az configure --defaults group=<service group name>
az configure --defaults spring-cloud=<service instance name>
az spring-cloud app create --name gateway
az spring-cloud app create --name auth-service
az spring-cloud app create --name account-service
```

### <a name="deploy-with-azure-cli-directly"></a>使用 Azure CLI 直接部署
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
    
    - uses: actions/checkout@master
    
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
### <a name="deploy-with-azure-cli-action"></a>使用 Azure CLI 動作部署
Az `run` 命令會使用最新版本的 Azure CLI。 如果有重大變更，您也可以搭配使用特定版本的 Azure CLI 與 Azure/CLI `action` 。 

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
    
    - uses: actions/checkout@master
    
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
另一個選項是使用[Maven 外掛程式](https://docs.microsoft.com/azure/spring-cloud/spring-cloud-quickstart-launch-app-maven)來部署 Jar 和更新應用程式設定。 此命令 `mvn azure-spring-cloud:deploy` 具有等冪性，並且會視需要自動建立應用程式。 您不需要事先建立對應的應用程式。

```
name: AzureSpringCloud
on: push

jobs:
  build-and-deploy:
    runs-on: ubuntu-latest
    steps:
    
    - uses: actions/checkout@master
    
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

## <a name="run-the-workflow"></a>執行工作流程
在您推送至 GitHub 之後，應該會自動啟用 GitHub**動作** `.github/workflow/main.yml` 。 當您推送新的認可時，將會觸發此動作。 如果您在瀏覽器中建立這個檔案，您的動作應該已經執行。

若要確認已啟用此動作，請按一下 [GitHub 存放庫] 頁面上的 [**動作**] 索引標籤：

 ![驗證動作已啟用](./media/github-actions/actions3.png)

如果您的動作在錯誤中執行（例如，如果您尚未設定 Azure 認證），您可以在修正錯誤之後重新執行檢查。 在 [GitHub 存放庫] 頁面上，按一下 [**動作**]，選取特定的工作流程工作，然後按一下 [**重新執行檢查**] 按鈕以重新執行檢查：

 ![重新執行檢查](./media/github-actions/actions4.png)

## <a name="next-steps"></a>後續步驟
* [適用于春季雲端 GitHub 動作的 Key Vault](./spring-cloud-github-actions-key-vault.md)
* [Azure Active Directory 服務主體](https://docs.microsoft.com/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-create-for-rbac)
* [適用於 Azure 的 GitHub Actions](https://github.com/Azure/actions/)
