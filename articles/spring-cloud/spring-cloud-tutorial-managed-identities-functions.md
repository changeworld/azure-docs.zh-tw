---
title: 教學課程 - 從 Azure Spring Cloud 應用程式叫用 Azure Functions 的受控識別
description: 使用受控識別從 Azure Spring Cloud 應用程式叫用 Azure Functions
author: MarkGardner
ms.author: margard
ms.service: spring-cloud
ms.topic: tutorial
ms.date: 07/10/2020
ms.openlocfilehash: 8ea8376307807abff8227d82bb6de7956fa3de99
ms.sourcegitcommit: 30505c01d43ef71dac08138a960903c2b53f2499
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/15/2020
ms.locfileid: "92088528"
---
# <a name="tutorial-use-a-managed-identity-to-invoke-azure-functions-from-an-azure-spring-cloud-app"></a>教學課程：使用受控識別從 Azure Spring Cloud 應用程式叫用 Azure Functions

本文說明如何建立 Azure Spring Cloud 應用程式的受控識別，並用其來叫用由 HTTP 觸發的函式。

Azure Functions 和應用程式服務都具有 Azure Active Directory (Azure AD) 驗證的內建支援。 藉由運用此內建驗證功能以及 Azure Spring Cloud 的受控識別，我們可以使用新式 OAuth 語意來叫用 RESTful 服務。 這個方法不需要在程式碼中儲存秘密，並提供更細微的控制以控制外部資源的存取。 


## <a name="prerequisites"></a>必要條件

* [註冊 Azure 訂用帳戶](https://azure.microsoft.com/free/)
* [安裝 Azure CLI 2.0.67 版或更新版本](/cli/azure/install-azure-cli)
* [安裝 Maven 3.0 或更新版本](https://maven.apache.org/download.cgi) \(英文\)
* [安裝 Azure Functions Core Tools 3.0.2009 版或更新版本](../azure-functions/functions-run-local.md#install-the-azure-functions-core-tools)


## <a name="create-a-resource-group"></a>建立資源群組
資源群組是在其中部署與管理 Azure 資源的邏輯容器。 使用 [az group create](/cli/azure/group#az-group-create) 命令建立用來包含函數應用程式和 Spring Cloud 的資源群組：

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```


## <a name="create-a-function-app"></a>建立函數應用程式
若要建立函數應用程式，您必須先建立支援儲存體帳戶，使用 [az storage account create](/cli/azure/storage/account#az-storage-account-create) 命令：

> [!Important]
> 每個函數應用程式和儲存體帳戶都必須有唯一的名稱。 在下列範例中以您的函數應用程式名稱取代 <your-functionapp-name>，並以您的儲存體帳戶名稱取代 <your-storageaccount-name>。

```azurecli-interactive
az storage account create --name <your-storageaccount-name> --resource-group myResourceGroup --location eastus --sku Standard_LRS
```

建立儲存體帳戶之後，您就可以建立函數應用程式。

```azurecli-interactive
az functionapp create --name <your-functionapp-name> --resource-group myResourceGroup --consumption-plan-location eastus --os-type windows --runtime node --storage-account <your-storageaccount-name> --functions-version 3
```

記下傳回的 **hostNames**，其格式為 "https://<your-functionapp-name>.azurewebsites.net"。 這會在下列步驟中用到。


## <a name="enable-azure-active-directory-authentication"></a>啟用 Azure Active Directory 驗證

從 [Azure 入口網站](https://portal.azure.com)存取新建立的函數應用程式，然後從設定功能表中選取 [驗證/授權]。 啟用 App Service 驗證，並將 [當要求未經驗證時所要採取的動作] 設定為 [使用 Azure Active Directory 登入]。 此設定可確保拒絕所有未經驗證的要求 (401 回應)。

![顯示 Azure Active Directory 做為預設提供者的驗證設定](media/spring-cloud-tutorial-managed-identities-functions/function-auth-config-1.jpg)

在 [驗證提供者] 下，選取 [Azure Active Directory] 來設定應用程式註冊。 選取 [快速管理模式] 將會在您的 Azure AD 租用戶中，自動建立具有正確設定的應用程式註冊。

![Azure Active Directory 提供者設定為快速管理模式](media/spring-cloud-tutorial-managed-identities-functions/function-auth-config-2.jpg)

儲存設定之後，函數應用程式將會重新開機，並會提示所有後續要求透過 Azure AD 進行登入。 您可以藉由瀏覽至函數應用程式根 URL (在上述步驟中的 **hostNames** 輸出中傳回)，來測試現在是否拒絕未驗證的要求。 系統應該會將您重新導向至您的組織 Azure AD 登入畫面。


## <a name="create-an-http-triggered-function"></a>建立由 HTTP 觸發的函式

在空的本機目錄中，建立新的函數應用程式，並新增由 HTTP 觸發的函式。

```console
func init --worker-runtime node
func new --template HttpTrigger --name HttpTrigger
```

根據預設，函式會使用以金鑰為基礎的驗證來保護 HTTP 端點。 因為我們將啟用 Azure AD 驗證來保護對函式的存取，所以我們想要 [將函式驗證層級設定為匿名](../azure-functions/functions-bindings-http-webhook-trigger.md#secure-an-http-endpoint-in-production)。

```json function.json
{
  "bindings": [
    {
      "authLevel": "anonymous",
      "type": "httpTrigger",
      ...
    }
  ]
}
```

應用程式現在可以發行至上一個步驟中建立的[函式應用程式](#create-a-function-app)執行個體。

```console
func azure functionapp publish <your-functionapp-name>
```

publish 命令的輸出應該會列出新建立函式的 URL。

```output
Deployment completed successfully.
Syncing triggers...
Functions in <your-functionapp-name>:
    HttpTrigger - [httpTrigger]
        Invoke url: https://<your-functionapp-name>.azurewebsites.net/api/httptrigger
```


## <a name="create-azure-spring-cloud-service-and-app"></a>建立 Azure Spring Cloud 服務和應用程式
安裝 spring-cloud 擴充功能之後，請使用 Azure CLI 命令 `az spring-cloud create` 建立 Azure Spring Cloud 執行個體。 

```azurecli-interactive
az extension add --name spring-cloud
az spring-cloud create --name mymsispringcloud --resource-group myResourceGroup --location eastus
```

下列範例會根據 `--assign-identity`參數的要求，建立具有系統指派受控識別且名為 `msiapp` 的虛擬機器。

```azurecli
az spring-cloud app create --name "msiapp" --service "mymsispringcloud" --resource-group "myResourceGroup" --is-public true --assign-identity
```

## <a name="build-sample-spring-boot-app-to-invoke-the-function"></a>用來叫用函式的組建範例 Spring Boot 應用程式

這個範例會先從 [MSI 端點](../active-directory/managed-identities-azure-resources/how-to-use-vm-token.md#get-a-token-using-http)要求存取權杖，然後使用該權杖來驗證函式 HTTP 要求，藉此叫用由 HTTP 觸發的函式。

1. 複製範例專案。 

    ```console
    git clone https://github.com/Azure-Samples/Azure-Spring-Cloud-Samples.git
    ```

2. 在您的應用程式屬性中，指定您的函式 URI 和觸發程序名稱。 

    ```bash
    cd Azure-Spring-Cloud-Samples/managed-identity-function
    vim src/main/resources/application.properties
    ```

    若要使用Azure Spring Cloud 應用程式的受控識別，請將具有下列內容的屬性新增至 src/main/resources/application.properties。

    ```
    azure.function.uri=https://<your-functionapp-name>.azurewebsites.net
    azure.function.triggerPath=httptrigger
    ```

3. 封裝您的範例應用程式。 

    ```console
    mvn clean package
    ```

4. 現在，使用 Azure CLI 命令 `az spring-cloud app deploy` 將應用程式部署至 Azure。 

    ```azurecli
    az spring-cloud app deploy  --name "msiapp" --service "mymsispringcloud" --resource-group "myResourceGroup" --jar-path target/sc-managed-identity-function-sample-0.1.0.jar
    ```

5. 存取公用端點或測試端點，以測試您的應用程式。 

    ```console
    curl https://mymsispringcloud-msiapp.azuremicroservices.io/func/springcloud
    ```

    您會在回應主體中看到下列訊息。
    ```output
    Function Response: Hello, springcloud. This HTTP triggered function executed successfully.
    ```
    
    您可以藉由變更 path 參數，嘗試將不同的值傳遞至函式。

## <a name="next-steps"></a>下一步

* [如何針對 Azure Spring Cloud 應用程式啟用系統指派的受控識別](./spring-cloud-howto-enable-system-assigned-managed-identity.md)
* [深入了解 Azure 資源受控識別](https://github.com/MicrosoftDocs/azure-docs/blob/master/articles/active-directory/managed-identities-azure-resources/overview.md)
* [設定服務對服務呼叫的精靈用戶端應用程式](../app-service/configure-authentication-provider-aad.md#configure-a-daemon-client-application-for-service-to-service-calls)