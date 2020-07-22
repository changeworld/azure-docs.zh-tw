---
title: 將您的 Java 函式連線至 Azure 儲存體
description: 了解如何使用佇列儲存體輸出繫結，將 HTTP 觸發的 Java 函式連線到 Azure 儲存體。
author: KarlErickson
ms.author: karler
ms.date: 10/14/2019
ms.topic: quickstart
zone_pivot_groups: java-build-tools-set
ms.openlocfilehash: e5b1250170830af24ddc1f2e3b78965ebcea051e
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/20/2020
ms.locfileid: "86540310"
---
# <a name="connect-your-java-function-to-azure-storage"></a>將您的 Java 函式連線至 Azure 儲存體

[!INCLUDE [functions-add-storage-binding-intro](../../includes/functions-add-storage-binding-intro.md)]

本文說明如何整合您在[先前的快速入門文章](./functions-create-first-azure-function-azure-cli.md?pivots=programming-language-java&tabs=bash,browser)中建立的函式與 Azure 儲存體佇列。 您新增至此函式的輸出繫結，會將資料從 HTTP 要求寫入至佇列中的訊息。

大部分的繫結都需要函式用來存取繫結服務的預存連接字串。 為了方便進行此連線，您可以使用您以函式應用程式建立的儲存體帳戶。 此帳戶的連線已儲存在名為 `AzureWebJobsStorage` 的應用程式設定中。  

## <a name="prerequisites"></a>Prerequisites

在開始這篇文章之前，請先完成 [Java 快速入門第 1 部分](./functions-create-first-azure-function-azure-cli.md?pivots=programming-language-java&tabs=bash,browser)中的步驟。

## <a name="download-the-function-app-settings"></a>下載函式應用程式設定

[!INCLUDE [functions-app-settings-download-cli](../../includes/functions-app-settings-download-local-cli.md)]

## <a name="enable-extension-bundles"></a>啟用延伸模組搭售方案

[!INCLUDE [functions-extension-bundles](../../includes/functions-extension-bundles.md)]

您現在可以將儲存體輸出繫結新增至您的專案。

## <a name="add-an-output-binding"></a>新增輸出繫結

[!INCLUDE [functions-add-output-binding-java-cli](../../includes/functions-add-output-binding-java-cli.md)]

## <a name="add-code-that-uses-the-output-binding"></a>新增會使用輸出繫結的程式碼

[!INCLUDE [functions-add-output-binding-java-code](../../includes/functions-add-output-binding-java-code.md)]

[!INCLUDE [functions-add-output-binding-java-test-cli](../../includes/functions-add-output-binding-java-test-cli.md)]

您現在已準備好在本機試用新的輸出繫結。

## <a name="run-the-function-locally"></a>在本機執行函式

如同以往，使用下列命令在本機建置專案及啟動 Functions 執行階段：

# <a name="maven"></a>[Maven](#tab/maven)
```bash
mvn clean package 
mvn azure-functions:run
```
# <a name="gradle"></a>[Gradle](#tab/gradle) 
```bash
gradle jar --info
gradle azureFunctionsRun
```
---

> [!NOTE]  
> 由於您已啟用 host.json 中的擴充功能套件組合，因此[儲存體繫結擴充功能](functions-bindings-storage-blob.md#add-to-your-functions-app)已在啟動期間下載並安裝，連同其他 Microsoft 繫結擴充功能。

如同以往，在新的終端視窗中使用 cURL 從命令列觸發函式：

```CMD
curl -w "\n" http://localhost:7071/api/HttpTrigger-Java --data AzureFunctions
```

這次，輸出繫結也會在您的儲存體帳戶中建立名為 `outqueue` 的佇列，並新增具有這個相同字串的訊息。

接下來，您可以使用 Azure CLI 來檢視新的佇列，並確認已新增一則訊息。 您也可以使用 [Microsoft Azure 儲存體總管][Azure Storage Explorer]或在 [Azure 入口網站](https://portal.azure.com)中檢視您的佇列。

[!INCLUDE [functions-storage-account-set-cli](../../includes/functions-storage-account-set-cli.md)]

[!INCLUDE [functions-query-storage-cli](../../includes/functions-query-storage-cli.md)]

### <a name="redeploy-the-project"></a>重新部署專案 

若要更新已發佈的應用程式，請再次執行下列命令：  

# <a name="maven"></a>[Maven](#tab/maven)  
```bash
mvn azure-functions:deploy
```
# <a name="gradle"></a>[Gradle](#tab/gradle)  
```bash
gradle azureFunctionsDeploy
```
---

同樣地，您可以使用 cURL 來測試已部署的函式。 如同以往，將 POST 要求本文中的 `AzureFunctions` 值傳遞至 URL，如下列範例所示：

```bash
curl -w "\n" https://fabrikam-functions-20190929094703749.azurewebsites.net/api/HttpTrigger-Java?code=zYRohsTwBlZ68YF.... --data AzureFunctions
```

您可以再次[檢查儲存體佇列訊息](#query-the-storage-queue)，以確認輸出繫結會如預期般在佇列中產生新訊息。

[!INCLUDE [functions-cleanup-resources](../../includes/functions-cleanup-resources.md)]

## <a name="next-steps"></a>後續步驟

您已更新 HTTP 觸發的函式，以將資料寫入至儲存體佇列。 若要深入了解如何使用 Java 開發 Azure Functions，請參閱 [Azure Functions Java 開發人員指南](functions-reference-java.md)和 [Azure Functions 觸發程序和繫結](functions-triggers-bindings.md)。 如需 Python 中完整函式專案的範例，請參閱 [Java 函式範例](/samples/browse/?products=azure-functions&languages=Java)。 

接下來，您應為函式應用程式啟用 Application Insights 監視：

> [!div class="nextstepaction"]
> [啟用 Application Insights 整合](functions-monitoring.md#manually-connect-an-app-insights-resource)


[Azure Storage Explorer]: https://storageexplorer.com/
