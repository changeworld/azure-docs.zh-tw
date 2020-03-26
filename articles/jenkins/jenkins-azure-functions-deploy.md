---
title: 使用 Jenkins Azure Functions 外掛程式部署至 Azure Functions
description: 了解如何使用 Jenkins Azure Functions 外掛程式部署至 Azure Functions
keywords: jenkins, azure, devops, java, azure functions
ms.topic: tutorial
ms.date: 10/23/2019
ms.openlocfilehash: 731bac13a596bbeaf970b3f6ce976a582d1f11ae
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/24/2020
ms.locfileid: "78250914"
---
# <a name="deploy-to-azure-functions-using-the-jenkins-azure-functions-plug-in"></a>使用 Jenkins Azure Functions 外掛程式部署至 Azure Functions

[Azure Functions](/azure/azure-functions/) 是無伺服器計算服務。 您可以視需要使用 Azure Functions 來執行程式碼，無須佈建或管理基礎結構。 本教學課程會示範如何使用 Azure Functions 外掛程式將 JAVA 函數部署至 Azure Functions。

## <a name="prerequisites"></a>Prerequisites

- **Azure 訂用帳戶**：如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)。
- **Jenkins 伺服器**：如果您沒有安裝 Jenkins 伺服器，請參閱本文：[在 Azure 上建立 Jenkins 伺服器](./install-jenkins-solution-template.md)。

  > [!TIP]
  > 本教學課程使用的原始程式碼位於 [Visual Studio China GitHub 存放庫](https://github.com/VSChina/odd-or-even-function/blob/master/src/main/java/com/microsoft/azure/Function.java)。

## <a name="create-a-java-function"></a>建立 Java 函式

若要建立具有 Java 執行階段堆疊的 Java 函式，請使用 [Azure 入口網站](https://portal.azure.com)或 [Azure CLI](/cli/azure/?view=azure-cli-latest)。

下列步驟將說明如何使用 Azure CLI 建立 Java 函式：

1. 建立資源群組，並以您的資源群組名稱取代 **&lt;resource_group>** 預留位置。

    ```azurecli
    az group create --name <resource_group> --location eastus
    ```

1. 建立 Azure 儲存體帳戶，並以適當的值取代預留位置。
 
    ```azurecli
    az storage account create --name <storage_account> --location eastus --resource-group <resource_group> --sku Standard_LRS    
    ```

1. 建立測試函式應用程式，並以適當的值取代預留位置。

    ```azurecli
    az functionapp create --resource-group <resource_group> --consumption-plan-location eastus --name <function_app> --storage-account <storage_account>
    ```

## <a name="prepare-jenkins-server"></a>準備 Jenkins 伺服器

下列步驟將說明如何準備 Jenkins 伺服器：

1. 在 Azure 上部署 [Jenkins 伺服器](https://aka.ms/jenkins-on-azure)。 如果您尚未安裝 Jenkins 伺服器的執行個體，[在 Azure 上建立 Jenkins 伺服器](./install-jenkins-solution-template.md)一文可引導您完成此程序。

1. 使用 SSH 登入 Jenkins 執行個體。

1. 在 Jenkins 執行個體上，使用下列命令安裝 maven：

    ```terminal
    sudo apt install -y maven
    ```

1. 在 Jenkins 執行個體上，藉由在終端機提示字元上發出下列命令來安裝 [Azure Functions Core Tools](/azure/azure-functions/functions-run-local)：

    ```terminal
    wget -q https://packages.microsoft.com/config/ubuntu/16.04/packages-microsoft-prod.deb
    sudo dpkg -i packages-microsoft-prod.deb
    sudo apt-get update
    sudo apt-get install azure-functions-core-tools
    ```

1. 在 Jenkins 儀表板中，安裝下列外掛程式：

    - Azure Functions 外掛程式
    - EnvInject 外掛程式

1. Jenkins 需要 Azure 服務主體來驗證和存取 Azure 資源。 如需逐步指示，請參閱[部署至 Azure App Service](./tutorial-jenkins-deploy-web-app-azure-app-service.md)。

1. 使用 Azure 服務主體，在 Jenkins 中新增「Microsoft Azure 服務主體」認證類型。 請參閱[部署至 Azure App Service](./tutorial-jenkins-deploy-web-app-azure-app-service.md#add-service-principal-to-jenkins)教學課程。

## <a name="fork-the-sample-github-repo"></a>派生範例 GitHub 存放庫

1. [登入 GitHub 存放庫以取得奇數或偶數範例應用程式](https://github.com/VSChina/odd-or-even-function.git) \(英文\)。

1. 在 GitHub 的右上角選擇 [派生]  。

1. 依照提示選取您的 GitHub 帳戶，並完成派生。

## <a name="create-a-jenkins-pipeline"></a>建立 Jenkins 管線

在本節中，您將建立 [Jenkins 管線](https://jenkins.io/doc/book/pipeline/)。

1. 在 Jenkins 儀表板中建立管線。

1. 啟用**準備執行環境**。

1. 在**屬性內容**中新增下列環境變數，並以適合您環境的值取代預留位置：

    ```
    AZURE_CRED_ID=<service_principal_credential_id>
    RES_GROUP=<resource_group>
    FUNCTION_NAME=<function_name>
    ```
    
1. 在 [管線] -> [定義]  區段中，選取 [來自 SCM 的管線指令碼]  。

1. 輸入您 GitHub 派生的 URL 和指令碼路徑 ("doc/resources/jenkins/JenkinsFile") 以在 [JenkinsFile 範例](https://github.com/VSChina/odd-or-even-function/blob/master/doc/resources/jenkins/JenkinsFile)中使用。

   ```
   node {
    stage('Init') {
        checkout scm
        }

    stage('Build') {
        sh 'mvn clean package'
        }

    stage('Publish') {
        azureFunctionAppPublish appName: env.FUNCTION_NAME, 
                                azureCredentialsId: env.AZURE_CRED_ID, 
                                filePath: '**/*.json,**/*.jar,bin/*,HttpTrigger-Java/*', 
                                resourceGroup: env.RES_GROUP, 
                                sourceDirectory: 'target/azure-functions/odd-or-even-function-sample'
        }
    }
    ```

## <a name="build-and-deploy"></a>建置及部署

現在可以開始執行 Jenkins 作業。

1. 首先，透過 [Azure Functions HTTP 觸發程序和繫結](/azure/azure-functions/functions-bindings-http-webhook-trigger#authorization-keys)一文中的指示取得授權金鑰。

1. 在瀏覽器中，輸入應用程式的 URL。 以適當的值取代預留位置，並將 **&lt;input_number>** 的數值指定為 Java 函式的輸入。

    ```
    https://<function_app>.azurewebsites.net/api/HttpTrigger-Java?code=<authorization_key>&number=<input_number>
    ```
1. 您會看到類似下列範例輸出的結果 (其中奇數 365 是測試用的數字)：

    ```output
    The number 365 is Odd.
    ```

## <a name="clean-up-resources"></a>清除資源

如果您不打算繼續使用此應用程式，請使用下列步驟來刪除建立的資源：

```azurecli
az group delete -y --no-wait -n <resource_group>
```

## <a name="next-steps"></a>後續步驟

若要深入了解 Azure Functions，請參閱下列資源：
> [!div class="nextstepaction"]
> [Azure Functions 文件](/azure/azure-functions/)
