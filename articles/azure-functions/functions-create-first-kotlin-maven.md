---
title: 使用 Kotlin 和 Maven 在 Azure 中建立第一個函式
description: 使用 Kotlin 和 Maven 建立 HTTP 觸發函式並將其發佈至 Azure。
author: dglover
ms.service: azure-functions
ms.topic: quickstart
ms.date: 03/25/2020
ms.author: dglover
ms.openlocfilehash: d8abf6cdf8506dc491f4e026c9a61ac1391f6ea4
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/20/2020
ms.locfileid: "86506293"
---
# <a name="quickstart-create-your-first-function-with-kotlin-and-maven"></a>快速入門：使用 Kotlin 和 Maven 建立您的第一個函式

本文會引導您使用 Maven 命令列工具來建置 Kotlin 函式專案並發佈至 Azure Functions。 當您完成時，您的函式程式碼會在 Azure 中的[取用方案](functions-scale.md#consumption-plan)上執行，並且可使用 HTTP 要求來觸發。

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Prerequisites

若要使用 Kotlin 開發函式，您必須安裝下列項目：

- [Java Developer Kit](https://aka.ms/azure-jdks) 第 8 版
- [Apache Maven](https://maven.apache.org) 3.0 版或更新版本
- [Azure CLI](/cli/azure)
- [Azure Functions Core Tools](./functions-run-local.md#v2) 2.6.666 版或更新版本

> [!IMPORTANT]
> JAVA_HOME 環境變數必須設定為 JDK 的安裝位置，才能完成本快速入門。

## <a name="generate-a-new-functions-project"></a>產生新的 Functions 專案

在空的資料夾中，執行下列命令以從 [Maven 原型](https://maven.apache.org/guides/introduction/introduction-to-archetypes.html) \(英文\) 產生 Functions 專案。

# <a name="bash"></a>[bash](#tab/bash)
```bash
mvn archetype:generate \
    -DarchetypeGroupId=com.microsoft.azure \
    -DarchetypeArtifactId=azure-functions-kotlin-archetype
```

> [!NOTE]
> 如果您遇到執行命令的問題，請查看使用哪個 `maven-archetype-plugin` 版本。 因為您在不含 `.pom` 檔案的空目錄中執行命令，如果您從舊版升級 Maven，則可能會嘗試使用來自 `~/.m2/repository/org/apache/maven/plugins/maven-archetype-plugin` 的舊版外掛程式。 若是如此，請嘗試刪除 `maven-archetype-plugin` 目錄，然後重新執行命令。

# <a name="powershell"></a>[PowerShell](#tab/powershell)
```powershell
mvn archetype:generate `
    "-DarchetypeGroupId=com.microsoft.azure" `
    "-DarchetypeArtifactId=azure-functions-kotlin-archetype"
```

# <a name="cmd"></a>[Cmd](#tab/cmd)
```cmd
mvn archetype:generate ^
    -DarchetypeGroupId=com.microsoft.azure ^
    -DarchetypeArtifactId=azure-functions-kotlin-archetype
```
---

Maven 會要求您提供完成產生專案所需的值。 如需 _groupId_、_artifactId_ 和 _version_ 值，請參閱 [Maven 命名慣例](https://maven.apache.org/guides/mini/guide-naming-conventions.html) \(英文\) 參考。 _appName_ 值必須在整個 Azure 中是唯一的，Maven 才能根據先前輸入的 _artifactId_ 作為預設值產生應用程式名稱。 _packageName_ 值決定產生的函式程式碼的 Kotlin 套件。

以下的 `com.fabrikam.functions` 和 `fabrikam-functions` 識別碼作為範例使用，讓本快速入門中的步驟更容易閱讀。 建議您在此步驟中提供自己的值給 Maven。

<pre>
[INFO] Parameter: groupId, Value: com.fabrikam.function
[INFO] Parameter: artifactId, Value: fabrikam-function
[INFO] Parameter: version, Value: 1.0-SNAPSHOT
[INFO] Parameter: package, Value: com.fabrikam.function
[INFO] Parameter: packageInPathFormat, Value: com/fabrikam/function
[INFO] Parameter: appName, Value: fabrikam-function-20190524171507291
[INFO] Parameter: resourceGroup, Value: java-functions-group
[INFO] Parameter: package, Value: com.fabrikam.function
[INFO] Parameter: version, Value: 1.0-SNAPSHOT
[INFO] Parameter: groupId, Value: com.fabrikam.function
[INFO] Parameter: appRegion, Value: westus
[INFO] Parameter: artifactId, Value: fabrikam-function
</pre>

Maven 會以 _artifactId_ 名稱在新資料夾中建立專案檔案，在此例中為 `fabrikam-functions`。 在本專案中所產生且準備要執行的程式碼是一個簡單的 [HTTP 觸發](./functions-bindings-http-webhook.md)函式，能回應要求的本文：

```kotlin
class Function {

    /**
     * This function listens at endpoint "/api/HttpTrigger-Java". Two ways to invoke it using "curl" command in bash:
     * 1. curl -d "HTTP Body" {your host}/api/HttpTrigger-Java&code={your function key}
     * 2. curl "{your host}/api/HttpTrigger-Java?name=HTTP%20Query&code={your function key}"
     * Function Key is not needed when running locally, it is used to invoke function deployed to Azure.
     * More details: https://aka.ms/functions_authorization_keys
     */
    @FunctionName("HttpTrigger-Java")
    fun run(
            @HttpTrigger(
                    name = "req",
                    methods = [HttpMethod.GET, HttpMethod.POST],
                    authLevel = AuthorizationLevel.FUNCTION) request: HttpRequestMessage<Optional<String>>,
            context: ExecutionContext): HttpResponseMessage {

        context.logger.info("HTTP trigger processed a ${request.httpMethod.name} request.")

        val query = request.queryParameters["name"]
        val name = request.body.orElse(query)

        name?.let {
            return request
                    .createResponseBuilder(HttpStatus.OK)
                    .body("Hello, $name!")
                    .build()
        }

        return request
                .createResponseBuilder(HttpStatus.BAD_REQUEST)
                .body("Please pass a name on the query string or in the request body")
                .build()
    }
}
```

## <a name="run-the-function-locally"></a>在本機執行函式

將目錄變更為新建立的專案資料夾，並且使用 Maven 建置和執行函式：

```
cd fabrikam-function
mvn clean package
mvn azure-functions:run
```

> [!NOTE]
> 如果您遇到下列例外狀況：使用 Java 9 時的 `javax.xml.bind.JAXBException`，請參閱 [GitHub](https://github.com/jOOQ/jOOQ/issues/6477) 上的因應措施。

當函式在您的系統本機執行並準備好回應 HTTP 要求時，您會看到以下輸出：

<pre>
Now listening on: http://0.0.0.0:7071
Application started. Press Ctrl+C to shut down.

Http Functions:

        HttpTrigger-Java: [GET,POST] http://localhost:7071/api/HttpTrigger-Java
</pre>

在新的終端機視窗中使用 curl 從命令列觸發函式：

```
curl -w '\n' -d LocalFunction http://localhost:7071/api/HttpTrigger-Java
```

<pre>
Hello LocalFunction!
</pre>

在終端機中使用 `Ctrl-C` 可停止函式程式碼。

## <a name="deploy-the-function-to-azure"></a>將函式部署到 Azure

部署到 Azure Functions 的程序從 Azure CLI 使用帳戶認證。 繼續執行前，請[使用 Azure CLI 登入](/cli/azure/authenticate-azure-cli?view=azure-cli-latest)。

```azurecli
az login
```

使用 `azure-functions:deploy` Maven 目標將您的程式碼部署到新的函式應用程式。

> [!NOTE]
> 當您使用 Visual Studio Code 來部署函式應用程式時，請記得選擇非免費的訂用帳戶，否則您會收到錯誤。 您可以在 IDE 左側查看您的訂用帳戶。

```
mvn azure-functions:deploy
```

部署完成時，您會看到可用來存取 Azure 函式應用程式的 URL：

<pre>
[INFO] Successfully deployed Function App with package.
[INFO] Deleting deployment package from Azure Storage...
[INFO] Successfully deleted deployment package fabrikam-function-20170920120101928.20170920143621915.zip
[INFO] Successfully deployed Function App at https://fabrikam-function-20170920120101928.azurewebsites.net
[INFO] ------------------------------------------------------------------------
</pre>

使用 `cURL` 測試在 Azure 上執行的函式應用程式。 您必須變更下面範例中的 URL，以符合上一個步驟中自有函式應用程式的已部署 URL。

> [!NOTE]
> 請確定您將 [存取權限]  設為 `Anonymous`。 當您選擇預設層級 `Function` 時，您必須在要求中提供[函式金鑰](functions-bindings-http-webhook-trigger.md#authorization-keys)以存取您的函式端點。

```
curl -w '\n' https://fabrikam-function-20170920120101928.azurewebsites.net/api/HttpTrigger-Java -d AzureFunctions
```

```Output
Hello AzureFunctions!
```

## <a name="make-changes-and-redeploy"></a>進行變更並重新部署

在產生的專案中編輯 `src/main.../Function.java` 來源檔案，以改變您的 Function 應用程式所傳回的文字。 將這一行：

```kotlin
return request
        .createResponseBuilder(HttpStatus.OK)
        .body("Hello, $name!")
        .build()
```

變更為下列程式碼：

```kotlin
return request
        .createResponseBuilder(HttpStatus.OK)
        .body("Hi, $name!")
        .build()
```

儲存變更，然後一如往常從終端機執行 `azure-functions:deploy` 來重新部署。 函數應用程式將會更新，而此要求：

```
curl -w '\n' -d AzureFunctionsTest https://fabrikam-functions-20170920120101928.azurewebsites.net/api/HttpTrigger-Java
```

您會看到更新的輸出：

<pre>
Hi, AzureFunctionsTest
</pre>


## <a name="reference-bindings"></a>參考繫結

若要使用 HTTP 觸發程序和 Timer 觸發程序以外的 [Functions 觸發程序和繫結](functions-triggers-bindings.md)，您必須安裝繫結擴充功能。 雖然本文並不要求，但您必須知道如何在使用其他繫結類型時啟用擴充功能。

[!INCLUDE [functions-extension-bundles](../../includes/functions-extension-bundles.md)]

## <a name="next-steps"></a>後續步驟

您已經建立一個包含簡單 HTTP 觸發程序的 Kotlin 函式應用程式，並部署到 Azure Functions。

- 檢閱 [Java 函式開發人員指南](functions-reference-java.md)，以了解開發 Java 和 Kotlin 函式的詳細資訊。
- 使用 `azure-functions:add`Maven 目標，將具有不同觸發程序的其他函式新增至您的專案。
- 使用 [Visual Studio Code](https://code.visualstudio.com/docs/java/java-azurefunctions) (英文)、[IntelliJ](functions-create-maven-intellij.md) 和 [Eclipse](functions-create-maven-eclipse.md)，在本機撰寫函式並進行偵錯。 
- 使用 Visual Studio Code 對部署在 Azure 中的函式進行偵錯。 請參閱 Visual Studio Code [無伺服器 Java 應用程式](https://code.visualstudio.com/docs/java/java-serverless#_remote-debug-functions-running-in-the-cloud) (英文) 文件中的指示。
