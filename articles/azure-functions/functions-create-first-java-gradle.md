---
title: 使用 JAVA 和 Gradle 將函式發佈至 Azure
description: 使用 JAVA 和 Gradle 建立 HTTP 觸發的函式，並將其發佈至 Azure。
author: KarlErickson
ms.custom: devx-track-java
ms.author: karler
ms.topic: how-to
ms.date: 04/08/2020
ms.openlocfilehash: 48a732e3935d78bdbf8b81fe989b59be1fbe2203
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/25/2020
ms.locfileid: "96020427"
---
# <a name="use-java-and-gradle-to-create-and-publish-a-function-to-azure"></a>使用 JAVA 和 Gradle 建立函式並將其發佈至 Azure

本文說明如何使用 Gradle 命令列工具來建立和發佈 JAVA 函式專案，以 Azure Functions。 完成時，您的函式程式碼會在 Azure 的[無伺服器主控方案](functions-scale.md#consumption-plan)中執行，並由 HTTP 要求所觸發。 

> [!NOTE]
> 如果 Gradle 不是您慣用的開發工具，請參閱使用 [Maven](./create-first-function-cli-java.md)、 [IntelliJ 概念](/azure/developer/java/toolkit-for-intellij/quickstart-functions) 和 [VS Code](./create-first-function-vs-code-java.md)的 JAVA 開發人員類似教學課程。

## <a name="prerequisites"></a>Prerequisites

若要使用 Java 開發函式，您必須安裝下列項目：

- [Java Developer Kit](/azure/developer/java/fundamentals/java-jdk-long-term-support) 第 8 版
- [Azure CLI]
- [Azure Functions Core Tools](./functions-run-local.md#v2) 2.6.666 版或更新版本
- [Gradle](https://gradle.org/)，4.10 版和更新版本

您也需要作用中的 Azure 訂用帳戶。 [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

> [!IMPORTANT]
> JAVA_HOME 環境變數必須設定為 JDK 的安裝位置，才能完成本快速入門。

## <a name="prepare-a-functions-project"></a>準備函數專案

使用下列命令來複製範例專案：

```bash
git clone https://github.com/Azure-Samples/azure-functions-samples-java.git
cd azure-functions-samples-java/
```

開啟 `build.gradle` 並將 `appName` 下一節中的變更為唯一名稱，以避免部署至 Azure 時的功能變數名稱衝突。 

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
> [我遇到問題](https://www.research.net/r/javae2e?tutorial=functions-create-first-java-gradle&step=generate-project)

## <a name="run-the-function-locally"></a>在本機執行函式

執行下列命令以建立函式專案，然後執行該函數專案：

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

在新的終端機視窗中，使用下列捲曲命令，從命令列觸發函式：

```bash
curl -w "\n" http://localhost:7071/api/HttpExample --data AzureFunctions
```

預期的輸出如下所示：

<pre>
Hello, AzureFunctions
</pre>

> [!NOTE]
> 如果您將 authLevel 設定為 `FUNCTION` 或 `ADMIN` ，則在本機執行時，不需要函式 [金鑰](functions-bindings-http-webhook-trigger.md#authorization-keys) 。  

在終端機中使用 `Ctrl+C` 可停止函式程式碼。

> [!div class="nextstepaction"]
> [我遇到問題](https://www.research.net/r/javae2e?tutorial=functions-create-first-java-gradle&step=local-run)

## <a name="deploy-the-function-to-azure"></a>將函式部署到 Azure

當您第一次部署函式應用程式時，會在 Azure 中建立函式應用程式和相關資源。 請先使用 [az login](/cli/azure/authenticate-azure-cli) Azure CLI 命令登入您的 Azure 訂用帳戶，才可部署。 

```azurecli
az login
```

> [!TIP]
> 如果您的帳戶可以存取多個訂用帳戶，請使用 [az account set](/cli/azure/account#az-account-set) 來設定此工作階段的預設訂用帳戶。 

使用下列命令，將您的專案部署至新的函式應用程式。 

```bash
gradle azureFunctionsDeploy
```

這會根據 gradle 檔案中的值，在 Azure 中建立下列資源：

+ 資源群組。 以您提供的 _resourceGroup_ 命名。
+ 儲存體帳戶。 Functions 所需。 此名稱會根據儲存體帳戶名稱需求隨機產生。
+ App Service 方案。 在指定的 _appRegion_ 中，為您的函數應用程式裝載的無伺服器耗用量方案。 此名稱是隨機產生的。
+ 函式應用程式。 函式應用程式是您函式的部署和執行單位。 此名稱是您的 _appName_，其附加隨機產生的數字。 

部署也會封裝專案檔案，並使用 [zip deployment](functions-deployment-technologies.md#zip-deploy) 將其部署至新的函式應用程式，並已啟用從套件執行模式。

範例專案中的 HTTP 觸發程式 authLevel 是 `ANONYMOUS` ，它會略過驗證。 但是，如果您使用其他 authLevel （例如 `FUNCTION` 或 `ADMIN` ），則必須取得函式金鑰，才能透過 HTTP 呼叫函式端點。 [Azure 入口網站]是取得函式金鑰的最簡單方式。

> [!div class="nextstepaction"]
> [我遇到問題](https://www.research.net/r/javae2e?tutorial=functions-create-first-java-gradle&step=deploy)

## <a name="get-the-http-trigger-url"></a>取得 HTTP 觸發程序 URL

您可以從 Azure 入口網站取得觸發函式所需的 URL 和函式金鑰。 

1. 瀏覽至 [Azure 入口網站]、登入，在頁面頂端的 [搜尋] 中輸入您函式應用程式的 _appName_，然後按 Enter。
 
1. 在您的函數應用程式中，選取 [函式]，選擇您的函 **式，然後** 按一下 [ **</> 取得** 函式 Url]。 

    :::image type="content" source="./media/functions-create-first-java-gradle/get-function-url-portal.png" alt-text="從 Azure 入口網站複製函式 URL":::

1. 選擇 [預設值 (函式金鑰)]，然後選取 [複製]。 

您現在可以使用所複製的 URL 來存取您的函式。

## <a name="verify-the-function-in-azure"></a>在 Azure 中驗證函式

若要使用 `cURL` 驗證在 Azure 上執行的函式應用程式，請將下列範例中的 URL 取代為您從入口網站複製的 URL。

```console
curl -w "\n" http://azure-functions-sample-demo.azurewebsites.net/api/HttpExample --data AzureFunctions
```

這會在要求本文中使用 `AzureFunctions`，將 POST 要求傳送至函式端點。 您會看見下列回應。

<pre>
Hello, AzureFunctions
</pre>

> [!div class="nextstepaction"]
> [我遇到問題](https://www.research.net/r/javae2e?tutorial=functions-create-first-java-gradle&step=verify-deployment)

## <a name="next-steps"></a>後續步驟

您已使用 HTTP 觸發的函式建立 Java 函式專案，並在本機電腦上執行，然後部署到 Azure。 現在，請藉由下列作業擴充您的函式...

> [!div class="nextstepaction"]
> [新增 Azure 儲存體佇列輸出繫結](functions-add-output-binding-storage-queue-java.md)


[Azure CLI]: /cli/azure
[Azure 入口網站]: https://portal.azure.com