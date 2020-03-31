---
title: Azure 春雲 CI/CD 與 GitHub 操作
description: 如何通過 GitHub 操作為 Azure 春雲構建 CI/CD 工作流
author: MikeDodaro
ms.author: barbkess
ms.service: spring-cloud
ms.topic: how-to
ms.date: 01/15/2019
ms.openlocfilehash: 559c894a2212466761de820de7486ae203337802
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "77538459"
---
# <a name="azure-spring-cloud-cicd-with-github-actions"></a>Azure 春雲 CI/CD 與 GitHub 操作

GitHub 操作支援自動化軟體發展生命週期工作流。 借助 Azure 春雲的 GitHub 操作，您可以在存儲庫中創建工作流，以生成、測試、打包、發佈和部署到 Azure。 

## <a name="prerequisites"></a>Prerequisites
此示例需要[Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)。

## <a name="set-up-github-repository-and-authenticate"></a>設置 GitHub 存儲庫並進行身份驗證
您需要 Azure 服務原則憑據來授權 Azure 登錄操作。 要獲取 Azure 憑據，請在本地電腦上執行以下命令：
```
az login
az ad sp create-for-rbac --role contributor --scopes /subscriptions/<SUBSCRIPTION_ID> --sdk-auth 
```
要訪問特定資源組，可以縮小範圍：
```
az ad sp create-for-rbac --role contributor --scopes /subscriptions/<SUBSCRIPTION_ID>/resourceGroups/<RESOURCE_GROUP> --sdk-auth
```
該命令應輸出 JSON 物件：
```JSON
{
    "clientId": "<GUID>",
    "clientSecret": "<GUID>",
    "subscriptionId": "<GUID>",
    "tenantId": "<GUID>",
    ...
}
```

此示例在 GitHub 上使用[Piggy 指標](https://github.com/Azure-Samples/piggymetrics)示例。  分叉示例，打開 GitHub 存儲庫頁面，然後按一下 **"設置"** 選項卡。打開 **"打開機密"** 功能表，然後按一下"**添加新機密**：

 ![添加新機密](./media/github-actions/actions1.png)

將機密名稱`AZURE_CREDENTIALS`及其值設置為您在標題"*設置 GitHub 存儲庫並進行身份驗證*"標題下找到的 JSON 字串。

 ![設置機密資料](./media/github-actions/actions2.png)

您還可以在 GitHub 操作中從金鑰保存庫獲取 Azure 登錄憑據，如[GitHub 操作中使用金鑰保存庫進行驗證 Azure 彈簧中](./spring-cloud-github-actions-key-vault.md)所述。

## <a name="provision-service-instance"></a>預配服務實例
要預配 Azure Spring 雲服務實例，請使用 Azure CLI 運行以下命令。
```
az extension add --name spring-cloud
az group create --location eastus --name <resource group name>
az spring-cloud create -n <service instance name> -g <resource group name>
az spring-cloud config-server git set -n <service instance name> --uri https://github.com/xxx/piggymetrics --label config
```
## <a name="build-the-workflow"></a>構建工作流
工作流使用以下選項定義。

### <a name="prepare-for-deployment-with-azure-cli"></a>準備使用 Azure CLI 進行部署
該命令`az spring-cloud app create`當前不是冪等的。  我們建議在現有 Azure 春雲應用和實例上使用此工作流。

使用以下 Azure CLI 命令進行準備：
```
az configure --defaults group=<service group name>
az configure --defaults spring-cloud=<service instance name>
az spring-cloud app create --name gateway
az spring-cloud app create --name auth-service
az spring-cloud app create --name account-service
```

### <a name="deploy-with-azure-cli-directly"></a>直接使用 Azure CLI 部署
在`.github/workflow/main.yml`存儲庫中創建檔：

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
        mvn clean package -D skipTests
    
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
### <a name="deploy-with-azure-cli-action"></a>使用 Azure CLI 操作進行部署
az`run`命令將使用最新版本的 Azure CLI。 如果存在重大更改，也可以將 Azure CLI 的特定版本與 Azure/CLI`action`一起使用。 

> [!Note] 
> 此命令將在新容器中運行，因此`env`無法工作，並且跨操作檔訪問可能具有額外的限制。

在存儲庫中創建 .github/工作流/main.yml 檔：
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
        mvn clean package -D skipTests
        
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

## <a name="deploy-with-maven-plugin"></a>使用 Maven 外掛程式進行部署
另一個選項是使用[Maven 外掛程式](https://docs.microsoft.com/azure/spring-cloud/spring-cloud-quickstart-launch-app-maven)部署 Jar 並更新應用設置。 該命令`mvn azure-spring-cloud:deploy`是冪等的，如果需要，將自動創建應用。 您無需提前創建相應的應用。

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
        mvn clean package -D skipTests
        
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
推送`.github/workflow/main.yml`到 GitHub 後，應自動啟用 GitHub**操作**。 當您推送新提交時，將觸發該操作。 如果在瀏覽器中創建此檔，則操作應該已運行。

要驗證操作是否已啟用，請按一下 GitHub 存儲庫頁上的 **"操作"** 選項卡：

 ![驗證已啟用的操作](./media/github-actions/actions3.png)

如果操作運行錯誤（例如，如果尚未設置 Azure 憑據），則可以在修復錯誤後重新運行檢查。 在 GitHub 存儲庫頁上，按一下 **"操作**"，選擇特定的工作流任務，然後按一下 **"重新運行檢查"** 按鈕以重新運行檢查：

 ![重新運行檢查](./media/github-actions/actions4.png)

## <a name="next-steps"></a>後續步驟
* [春雲 GitHub 操作的關鍵保存庫](./spring-cloud-github-actions-key-vault.md)
* [Azure 活動目錄服務主體](https://docs.microsoft.com/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-create-for-rbac)
* [Azure 的 GitHub 操作](https://github.com/Azure/actions/)
