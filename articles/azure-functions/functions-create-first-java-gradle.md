---
title: 使用 Java 與 Gradle 將函數發布到 Azure
description: 使用 Java 和 Gradle 創建 HTTP 觸發的函數並將其發表到 Azure。
author: KarlErickson
ms.author: karler
ms.topic: how-to
ms.date: 08/10/2018
ms.openlocfilehash: 1a24569a89755a33a80b7f884b803bd36f38ca3f
ms.sourcegitcommit: 441db70765ff9042db87c60f4aa3c51df2afae2d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/06/2020
ms.locfileid: "80757136"
---
# <a name="quickstart-use-java-and-gradle-to-create-and-publish-a-function-to-azure"></a>快速入門:使用 Java 和 Gradle 建立函數並將其發表到 Azure

本文介紹如何使用 Gradle 命令列工具將 Java 函數專案構建併發佈到 Azure 函數。 完成時，您的函式程式碼會在 Azure 的[無伺服器主控方案](functions-scale.md#consumption-plan)中執行，並由 HTTP 要求所觸發。 

您還可以使用[Maven 原型](/azure/azure-functions/functions-create-first-azure-function-azure-cli?pivots=programming-language-java)從命令列構建和發佈 Java 函數專案。

## <a name="prerequisites"></a>Prerequisites

若要使用 Java 開發函式，您必須安裝下列項目：

- [Java Developer Kit](https://aka.ms/azure-jdks) 第 8 版
- [Azure CLI]
- [Azure Functions Core Tools](./functions-run-local.md#v2) 2.6.666 版或更新版本
- [格拉德爾](https://gradle.org/), 版本 4.10 及以上

您也需要作用中的 Azure 訂用帳戶。 [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

> [!IMPORTANT]
> JAVA_HOME 環境變數必須設定為 JDK 的安裝位置，才能完成本快速入門。

## <a name="prepare-a-functions-project"></a>準備功能項目

使用以下指令複製例項目:

```bash
git clone https://github.com/Azure-Samples/azure-functions-samples-java.git
cd azure-functions-samples-java/
```

打開`build.gradle`下一節`appName`中 的將更改為唯一名稱,以避免在部署到 Azure 時出現域名衝突。 

```gradle
azurefunctions {
    resourceGroup = 'java-functions-group'
    appName = 'azure-functions-sample-demo'
    pricingTier = 'Consumption'
    region = 'westus'
    runtime {
      os = 'windows'
    }
    localDebug = "transport=dt_socket,server=y,suspend=n,address=5005"
}
```

在文字編輯器中，從 *src/main/java* 路徑開啟新的 Function.java 檔案，並檢閱所產生的程式碼。 此程式碼是一個 [HTTP 觸發](functions-bindings-http-webhook.md)函式，可回應要求的本文。 

> [!div class="nextstepaction"]
> [我遇到問題](https://www.research.net/r/javae2e?tutorial=functions-maven-quickstart&step=generate-project)

## <a name="run-the-function-locally"></a>在本機執行函式

執行以下指令以產生後執行函數專案:

```bash
gradle jar --info
gradle azureFunctionsRun
```
當您在本機執行專案時，您會在 Azure Functions Core Tools 中看到如下所示的輸出：

<pre>
...

Now listening on: http://0.0.0.0:7071
Application started. Press Ctrl+C to shut down.

Http Functions:

    HttpExample: [GET,POST] http://localhost:7071/api/HttpExample
...
</pre>

使用新終端視窗中的以下 cURL 命令從命令列觸發函數:

```bash
curl -w "\n" http://localhost:7071/api/HttpExample --data AzureFunctions
```

預期輸出如下:

<pre>
Hello AzureFunctions!
</pre>

在本機執行時不需要[函式金鑰](functions-bindings-http-webhook-trigger.md#authorization-keys)。  
在終端機中使用 `Ctrl+C` 可停止函式程式碼。

> [!div class="nextstepaction"]
> [我遇到問題](https://www.research.net/r/javae2e?tutorial=functions-maven-quickstart&step=local-run)

## <a name="deploy-the-function-to-azure"></a>將函式部署到 Azure

當您第一次部署函式應用程式時，會在 Azure 中建立函式應用程式和相關資源。 請先使用 [az login](/cli/azure/authenticate-azure-cli) Azure CLI 命令登入您的 Azure 訂用帳戶，才可部署。 

```azurecli
az login
```

> [!TIP]
> 如果您的帳戶可以存取多個訂用帳戶，請使用 [az account set](/cli/azure/account#az-account-set) 來設定此工作階段的預設訂用帳戶。 

使用以下命令將專案部署到新的函數應用。 

```bash
gradle azureFunctionsDeploy
```

這會根據 build.gradle 檔中的值在 Azure 中建立以下資源:

+ 資源群組。 以您提供的 _resourceGroup_ 命名。
+ 儲存體帳戶。 Functions 所需。 此名稱會根據儲存體帳戶名稱需求隨機產生。
+ App Service 方案。 在指定的_應用區域_中託管函數應用的無伺服器消耗計劃。 此名稱是隨機產生的。
+ 函式應用程式。 函式應用程式是您函式的部署和執行單位。 此名稱是您的 _appName _，其附加隨機產生的數字。 

部署也會封裝專案檔案，並使用 [zip deployment](functions-deployment-technologies.md#zip-deploy) 將其部署至新的函式應用程式，並已啟用從套件執行模式。

因為我們發佈的 HTTP 觸發程式會使用 `authLevel = AuthorizationLevel.FUNCTION`，所以您必須取得函式金鑰，才能透過 HTTP 呼叫函式端點。 [Azure 入口網站]是取得函式金鑰的最簡單方式。

> [!div class="nextstepaction"]
> [我遇到問題](https://www.research.net/r/javae2e?tutorial=functions-maven-quickstart&step=deploy)

## <a name="get-the-http-trigger-url"></a>取得 HTTP 觸發程序 URL

您可以從 Azure 入口網站取得觸發函式所需的 URL 和函式金鑰。 

1. 瀏覽至 [Azure 入口網站 ]、登入，在頁面頂端的 [搜尋]**** 中輸入您函式應用程式的 _appName_，然後按 Enter。
 
1. 在函式應用程式中，展開 [函式 (唯讀)]****，選擇您的函式，然後選取右上方的 [</> 取得函數 URL]****。 

    ![從 Azure 入口網站複製函式 URL](./media/functions-create-java-maven/get-function-url-portal.png)

1. 選擇 [預設值 (函式金鑰)]****，然後選取 [複製]****。 

您現在可以使用所複製的 URL 來存取您的函式。

## <a name="verify-the-function-in-azure"></a>在 Azure 中驗證函式

若要使用 `cURL` 驗證在 Azure 上執行的函式應用程式，請將下列範例中的 URL 取代為您從入口網站複製的 URL。

```console
curl -w "\n" https://fabrikam-functions-20190929094703749.azurewebsites.net/api/HttpExample?code=zYRohsTwBlZ68YF.... --data AzureFunctions
```

這會在要求本文中使用 `AzureFunctions`，將 POST 要求傳送至函式端點。 您會看見下列回應。

<pre>
Hello AzureFunctions!
</pre>

> [!div class="nextstepaction"]
> [我遇到問題](https://www.research.net/r/javae2e?tutorial=functions-maven-quickstart&step=verify-deployment)

## <a name="next-steps"></a>後續步驟

您已使用 HTTP 觸發的函式建立 Java 函式專案，並在本機電腦上執行，然後部署到 Azure。 現在，請藉由下列作業擴充您的函式...

> [!div class="nextstepaction"]
> [新增 Azure 儲存體佇列輸出繫結](functions-add-output-binding-storage-queue-java.md)


[Azure CLI]: /cli/azure
[Azure 入口網站]: https://portal.azure.com
