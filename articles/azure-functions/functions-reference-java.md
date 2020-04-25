---
title: Azure Functions 的 JAVA 開發人員參考
description: 了解如何使用 Java 開發函式。
ms.topic: conceptual
ms.date: 09/14/2018
ms.openlocfilehash: 19a290fe7717d7838e8fcd1d1f5cddb3f54eb812
ms.sourcegitcommit: f7fb9e7867798f46c80fe052b5ee73b9151b0e0b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/24/2020
ms.locfileid: "82145336"
---
# <a name="azure-functions-java-developer-guide"></a>Azure Functions Java 開發人員指南

Azure Functions 執行時間支援[JAVA SE 8 LTS （祖魯 8.31.0.2-jre 8.0.181-win_x64）](https://repos.azul.com/azure-only/zulu/packages/zulu-8/8u181/)。 本指南包含使用 JAVA 撰寫 Azure Functions 複雜性的相關資訊。

對其他語言而言，函數應用程式可能會有一或多個函式。 JAVA 函式是以`public`注釋`@FunctionName`裝飾的方法。 這個方法會定義 JAVA 函數的專案，而且在特定封裝中必須是唯一的。 以 JAVA 撰寫的其中一個函數應用程式可能會有多個類別，其中`@FunctionName`有多個以標注的公用方法。

本文假設您已經讀過 [Azure Functions 開發人員參考](functions-reference.md)。 您也應該使用[Visual Studio Code](/azure/azure-functions/functions-create-first-function-vs-code?pivots=programming-language-java)或[Maven](/azure/azure-functions/functions-create-first-azure-function-azure-cli?pivots=programming-language-java)來完成函式快速入門，以建立您的第一個函式。

## <a name="programming-model"></a>程式設計模型 

[觸發程序和繫結](functions-triggers-bindings.md)的概念是 Azure Functions 的基礎。 觸發程序會開始執行您的程式碼。 繫結可讓您將資料傳至函式以及從函式傳回資料，而不需要撰寫自訂的資料存取程式碼。

## <a name="create-java-functions"></a>建立 JAVA 函式

為了讓您更輕鬆地建立 JAVA 函式，有以 Maven 為基礎的工具和 archetype，可使用預先定義的 JAVA 範本，協助您使用特定的函式觸發程式來建立專案。    

### <a name="maven-based-tooling"></a>Maven 為基礎的工具

下列開發人員環境具有 Azure Functions 工具，可讓您建立 JAVA 函數專案： 

+ [Visual Studio Code](https://code.visualstudio.com/docs/java/java-azurefunctions)
+ [Eclipse](functions-create-maven-eclipse.md)
+ [IntelliJ](functions-create-maven-intellij.md)

上述文章連結會示範如何使用您選擇的 IDE 來建立您的第一個函式。 

### <a name="project-scaffolding"></a>專案樣板

如果您偏好從終端機執行命令列開發，scaffold 以 JAVA 為基礎的函式專案最簡單的`Apache Maven`方式就是使用 archetype。 適用于 Azure Functions 的 JAVA Maven 原型是在下列_groupId_底下發行：_artifactId_： [.com](https://search.maven.org/artifact/com.microsoft.azure/azure-functions-archetype/)。 

下列命令會使用這個原型產生新的 JAVA 函數專案：

```
mvn archetype:generate \
    -DarchetypeGroupId=com.microsoft.azure \
    -DarchetypeArtifactId=azure-functions-archetype 
```

若要開始使用此原型，請參閱[JAVA 快速入門](/azure/azure-functions/functions-create-first-azure-function-azure-cli?pivots=programming-language-java)。 

## <a name="create-kotlin-functions-preview"></a>建立 Kotlin 函式（預覽）

另外還有一個 Maven 原型可產生 Kotlin 函數。 這個原型（目前為預覽狀態）是在下列_groupId_底下發行：_artifactId_： [.com。 azure： kotlin-原型](https://search.maven.org/artifact/com.microsoft.azure/azure-functions-kotlin-archetype/)。 

下列命令會使用這個原型產生新的 JAVA 函數專案：

```
mvn archetype:generate \
    -DarchetypeGroupId=com.microsoft.azure \
    -DarchetypeArtifactId=azure-functions-kotlin-archetype
```

若要開始使用此原型，請參閱[Kotlin 快速入門](functions-create-first-kotlin-maven.md)。

## <a name="folder-structure"></a>資料夾結構

以下是 Azure Functions JAVA 專案的資料夾結構：

```
FunctionsProject
 | - src
 | | - main
 | | | - java
 | | | | - FunctionApp
 | | | | | - MyFirstFunction.java
 | | | | | - MySecondFunction.java
 | - target
 | | - azure-functions
 | | | - FunctionApp
 | | | | - FunctionApp.jar
 | | | | - host.json
 | | | | - MyFirstFunction
 | | | | | - function.json
 | | | | - MySecondFunction
 | | | | | - function.json
 | | | | - bin
 | | | | - lib
 | - pom.xml
```

_* Kotlin 專案看起來非常類似，因為它仍然是 Maven_

您可以使用共用的[主機 json](functions-host-json.md)檔案來設定函數應用程式。 每個函式都有自己的程式碼檔案 (.java) 和繫結設定檔 (function.json)。

您可以在專案中放入多個函式。 請勿將函式放入個別的 jar。 目標`FunctionApp`目錄中的會部署至 Azure 中的函數應用程式。

## <a name="triggers-and-annotations"></a>觸發程序和註解

 函數是由觸發程式叫用，例如 HTTP 要求、計時器或資料的更新。 您的函式必須處理該觸發程式和其他任何輸入，以產生一或多個輸出。

請使用 [com.microsoft.azure.functions.annotation.*](/java/api/com.microsoft.azure.functions.annotation) 套件中所包含的 Java 註釋，以將輸入和輸出繫結至方法。 如需詳細資訊，請參閱[JAVA 參考](/java/api/com.microsoft.azure.functions.annotation)檔。

> [!IMPORTANT] 
> 您必須在您的[本機. 設定](/azure/azure-functions/functions-run-local#local-settings-file)中設定 Azure 儲存體帳戶，以在本機執行 azure Blob 儲存體、Azure 佇列儲存體或 azure 資料表儲存體觸發程式。

範例：

```java
public class Function {
    public String echo(@HttpTrigger(name = "req", 
      methods = {"post"},  authLevel = AuthorizationLevel.ANONYMOUS) 
        String req, ExecutionContext context) {
        return String.format(req);
    }
}
```

以下是由 azure 函`function.json` [式-maven-外掛程式](https://mvnrepository.com/artifact/com.microsoft.azure/azure-functions-maven-plugin)產生的對應：

```json
{
  "scriptFile": "azure-functions-example.jar",
  "entryPoint": "com.example.Function.echo",
  "bindings": [
    {
      "type": "httpTrigger",
      "name": "req",
      "direction": "in",
      "authLevel": "anonymous",
      "methods": [ "post" ]
    },
    {
      "type": "http",
      "name": "$return",
      "direction": "out"
    }
  ]
}

```

## <a name="jdk-runtime-availability-and-support"></a>JDK 執行階段可用性和支援 

若要在本機開發 JAVA 函數應用程式，請下載並使用來自[Azul 系統](https://www.azul.com/downloads/azure-only/zulu/)的[Azul 祖魯 Enterprise for Azure](https://assets.azul.com/files/Zulu-for-Azure-FAQ.pdf) JAVA 8 jdk。 當您將函數應用程式部署至雲端時，Azure Functions 使用 Azul Java 8 JDK 執行階段。

Jdk 和函數應用程式的相關問題[Azure 支援](https://azure.microsoft.com/support/)可透過合格的[支援方案](https://azure.microsoft.com/support/plans/)取得。

## <a name="customize-jvm"></a>自訂 JVM

函式可讓您自訂用來執行 JAVA 功能的 JAVA 虛擬機器（JVM）。 預設會使用[下列 JVM 選項](https://github.com/Azure/azure-functions-java-worker/blob/master/worker.config.json#L7)：

* `-XX:+TieredCompilation`
* `-XX:TieredStopAtLevel=1`
* `-noverify` 
* `-Djava.net.preferIPv4Stack=true`
* `-jar`

您可以在名為`JAVA_OPTS`的應用程式設定中提供額外的引數。 您可以在 Azure 入口網站或 Azure CLI 中，將應用程式設定新增至部署至 Azure 的函數應用程式。

> [!IMPORTANT]  
> 在取用方案中，您也必須新增值為0的 WEBSITE_USE_PLACEHOLDER 設定，自訂才能正常執行。 此設定會增加 JAVA 函數的冷啟動時間。

### <a name="azure-portal"></a>Azure 入口網站

在 [ [Azure 入口網站](https://portal.azure.com)中，使用 [[應用程式設定]](functions-how-to-use-azure-function-app-settings.md#settings)索引`JAVA_OPTS`標籤來新增設定。

### <a name="azure-cli"></a>Azure CLI

您可以使用[az functionapp config appsettings set](/cli/azure/functionapp/config/appsettings)命令來設定`JAVA_OPTS`，如下列範例所示：

#### <a name="consumption-plan"></a>[取用方案](#tab/consumption)
```azurecli-interactive
az functionapp config appsettings set \
--settings "JAVA_OPTS=-Djava.awt.headless=true" \
"WEBSITE_USE_PLACEHOLDER=0" \
--name <APP_NAME> --resource-group <RESOURCE_GROUP>
```
#### <a name="dedicated-plan--premium-plan"></a>[專用方案/Premium 方案](#tab/dedicated+premium)
```azurecli-interactive
az functionapp config appsettings set \
--settings "JAVA_OPTS=-Djava.awt.headless=true" \
--name <APP_NAME> --resource-group <RESOURCE_GROUP>
```
---

這個範例會啟用無周邊模式。 請`<APP_NAME>`以您的函式應用程式名稱取代`<RESOURCE_GROUP>` ，並以資源群組取代。 

## <a name="third-party-libraries"></a>第三方程式庫 

Azure Functions 支援使用第三方程式庫。 根據預設，專案`pom.xml`檔中指定的所有相依性都會在[`mvn package`](https://github.com/Microsoft/azure-maven-plugins/blob/master/azure-functions-maven-plugin/README.md#azure-functionspackage)目標期間自動配套。 對於在 `pom.xml` 檔案中未指定為相依性的程式庫，請將其放入函式根目錄的 `lib` 目錄中。 放置在`lib`目錄中的相依性會在執行時間新增至系統類別載入器。

`com.microsoft.azure.functions:azure-functions-java-library`預設會在路徑上提供相依性，而不需要包含在`lib`目錄中。 此外， [azure 函式-java 背景工作角色](https://github.com/Azure/azure-functions-java-worker)會將[此處](https://github.com/Azure/azure-functions-java-worker/wiki/Azure-Java-Functions-Worker-Dependencies)所列的相依性新增至路徑。

## <a name="data-type-support"></a>資料類型支援

您可以使用簡單的舊版 JAVA 物件（Pojo）、中`azure-functions-java-library`所定義的類型或基本資料類型（例如字串和整數）來系結至輸入或輸出系結。

### <a name="pojos"></a>Pojo

若要將輸入資料轉換成 POJO，azure 函式[-java 背景工作角色](https://github.com/Azure/azure-functions-java-worker)會使用[gson](https://github.com/google/gson)程式庫。 作為函式輸入的 POJO 類型應為 `public`。

### <a name="binary-data"></a>二進位資料

藉由將 json 中的`byte[]` `dataType`欄位設定為`binary`，將二進位輸入或輸出系結至：

```java
   @FunctionName("BlobTrigger")
    @StorageAccount("AzureWebJobsStorage")
     public void blobTrigger(
        @BlobTrigger(name = "content", path = "myblob/{fileName}", dataType = "binary") byte[] content,
        @BindingName("fileName") String fileName,
        final ExecutionContext context
    ) {
        context.getLogger().info("Java Blob trigger function processed a blob.\n Name: " + fileName + "\n Size: " + content.length + " Bytes");
    }
```

如果您預期會有 null 值`Optional<T>`，請使用。

## <a name="bindings"></a>繫結

輸入和輸出繫結提供從您的程式碼內連線到資料的宣告式方法。 一個函數可以有多個輸入和輸出繫結。

### <a name="input-binding-example"></a>輸入系結範例

```java
package com.example;

import com.microsoft.azure.functions.annotation.*;

public class Function {
    @FunctionName("echo")
    public static String echo(
        @HttpTrigger(name = "req", methods = { "put" }, authLevel = AuthorizationLevel.ANONYMOUS, route = "items/{id}") String inputReq,
        @TableInput(name = "item", tableName = "items", partitionKey = "Example", rowKey = "{id}", connection = "AzureWebJobsStorage") TestInputData inputData
        @TableOutput(name = "myOutputTable", tableName = "Person", connection = "AzureWebJobsStorage") OutputBinding<Person> testOutputData,
    ) {
        testOutputData.setValue(new Person(httpbody + "Partition", httpbody + "Row", httpbody + "Name"));
        return "Hello, " + inputReq + " and " + inputData.getKey() + ".";
    }

    public static class TestInputData {
        public String getKey() { return this.RowKey; }
        private String RowKey;
    }
    public static class Person {
        public String PartitionKey;
        public String RowKey;
        public String Name;

        public Person(String p, String r, String n) {
            this.PartitionKey = p;
            this.RowKey = r;
            this.Name = n;
        }
    }
}
```

您可以使用 HTTP 要求叫用此函式。 
- HTTP 要求承載會傳遞做`String`為引數`inputReq`的。
- 會從資料表儲存體中抓取一個專案，並將它`TestInputData`當做引數`inputData`傳遞。

若要接收輸入批次，您可以系結`String[]`至`POJO[]`、 `List<String>`、或`List<POJO>`。

```java
@FunctionName("ProcessIotMessages")
    public void processIotMessages(
        @EventHubTrigger(name = "message", eventHubName = "%AzureWebJobsEventHubPath%", connection = "AzureWebJobsEventHubSender", cardinality = Cardinality.MANY) List<TestEventData> messages,
        final ExecutionContext context)
    {
        context.getLogger().info("Java Event Hub trigger received messages. Batch size: " + messages.size());
    }
    
    public class TestEventData {
    public String id;
}

```

每當設定的事件中樞內有新資料時，就會觸發此函式。 因為設定`cardinality`為`MANY`，所以函式會從事件中樞接收一批訊息。 `EventData`從事件中樞轉換為`TestEventData` ，以執行函式。

### <a name="output-binding-example"></a>輸出繫結範例

您可以使用`$return`，將輸出系結系結至傳回值。 

```java
package com.example;

import com.microsoft.azure.functions.annotation.*;

public class Function {
    @FunctionName("copy")
    @StorageAccount("AzureWebJobsStorage")
    @BlobOutput(name = "$return", path = "samples-output-java/{name}")
    public static String copy(@BlobTrigger(name = "blob", path = "samples-input-java/{name}") String content) {
        return content;
    }
}
```

如果有多個輸出繫結，請只對其中一個使用傳回值。

若要傳送多個輸出值，請使用 `azure-functions-java-library` 套件中定義的 `OutputBinding<T>`。 

```java
@FunctionName("QueueOutputPOJOList")
    public HttpResponseMessage QueueOutputPOJOList(@HttpTrigger(name = "req", methods = { HttpMethod.GET,
            HttpMethod.POST }, authLevel = AuthorizationLevel.ANONYMOUS) HttpRequestMessage<Optional<String>> request,
            @QueueOutput(name = "itemsOut", queueName = "test-output-java-pojo", connection = "AzureWebJobsStorage") OutputBinding<List<TestData>> itemsOut, 
            final ExecutionContext context) {
        context.getLogger().info("Java HTTP trigger processed a request.");
       
        String query = request.getQueryParameters().get("queueMessageId");
        String queueMessageId = request.getBody().orElse(query);
        itemsOut.setValue(new ArrayList<TestData>());
        if (queueMessageId != null) {
            TestData testData1 = new TestData();
            testData1.id = "msg1"+queueMessageId;
            TestData testData2 = new TestData();
            testData2.id = "msg2"+queueMessageId;

            itemsOut.getValue().add(testData1);
            itemsOut.getValue().add(testData2);

            return request.createResponseBuilder(HttpStatus.OK).body("Hello, " + queueMessageId).build();
        } else {
            return request.createResponseBuilder(HttpStatus.INTERNAL_SERVER_ERROR)
                    .body("Did not find expected items in CosmosDB input list").build();
        }
    }

     public static class TestData {
        public String id;
    }
```

您在 HttpRequest 上叫用此函式。 它會將多個值寫入佇列儲存體。

## <a name="httprequestmessage-and-httpresponsemessage"></a>HttpRequestMessage 和 HttpResponseMessage

 這些是在中`azure-functions-java-library`定義。 它們是可與 HttpTrigger 函式搭配使用的協助程式類型。

| 特殊類型      |       目標        | 一般使用方式                  |
| --------------------- | :-----------------: | ------------------------------ |
| `HttpRequestMessage<T>`  |    HTTP 觸發程序     | 取得方法、標頭或查詢 |
| `HttpResponseMessage` | HTTP 輸出繫結 | 傳回200以外的狀態   |

## <a name="metadata"></a>中繼資料

有幾個觸發程序會將[觸發程序中繼資料](/azure/azure-functions/functions-triggers-bindings)與輸入資料一起傳送。 您可以使用批註`@BindingName`來系結至觸發程式中繼資料。


```Java
package com.example;

import java.util.Optional;
import com.microsoft.azure.functions.annotation.*;


public class Function {
    @FunctionName("metadata")
    public static String metadata(
        @HttpTrigger(name = "req", methods = { "get", "post" }, authLevel = AuthorizationLevel.ANONYMOUS) Optional<String> body,
        @BindingName("name") String queryValue
    ) {
        return body.orElse(queryValue);
    }
}
```
在上述範例中， `queryValue`會系結至 HTTP 要求 URL 中`name`的查詢字串參數`http://{example.host}/api/metadata?name=test`。 以下是另一個範例，顯示如何從佇列`Id`觸發程式中繼資料系結至。

```java
 @FunctionName("QueueTriggerMetadata")
    public void QueueTriggerMetadata(
        @QueueTrigger(name = "message", queueName = "test-input-java-metadata", connection = "AzureWebJobsStorage") String message,@BindingName("Id") String metadataId,
        @QueueOutput(name = "output", queueName = "test-output-java-metadata", connection = "AzureWebJobsStorage") OutputBinding<TestData> output,
        final ExecutionContext context
    ) {
        context.getLogger().info("Java Queue trigger function processed a message: " + message + " with metadaId:" + metadataId );
        TestData testData = new TestData();
        testData.id = metadataId;
        output.setValue(testData);
    }
```

> [!NOTE]
> 注釋中提供的名稱必須符合中繼資料屬性。

## <a name="execution-context"></a>執行內容

`ExecutionContext`定義于中`azure-functions-java-library`，包含可與函數執行時間通訊的 helper 方法。

### <a name="logger"></a>記錄器

使用`getLogger`（定義于`ExecutionContext`）從函式程式碼寫入記錄。

範例：

```java

import com.microsoft.azure.functions.*;
import com.microsoft.azure.functions.annotation.*;

public class Function {
    public String echo(@HttpTrigger(name = "req", methods = {"post"}, authLevel = AuthorizationLevel.ANONYMOUS) String req, ExecutionContext context) {
        if (req.isEmpty()) {
            context.getLogger().warning("Empty request body received by function " + context.getFunctionName() + " with invocation " + context.getInvocationId());
        }
        return String.format(req);
    }
}
```

## <a name="view-logs-and-trace"></a>檢視記錄和追蹤

您可以使用 Azure CLI 來串流 JAVA stdout 和 stderr 記錄，以及其他應用程式記錄。 

以下是如何設定函式應用程式，使用 Azure CLI 來撰寫應用程式記錄的方法：

```azurecli-interactive
az webapp log config --name functionname --resource-group myResourceGroup --application-logging true
```

若要使用 Azure CLI 來串流函式應用程式的記錄輸出，請開啟新的命令提示字元、Bash 或終端機會話，然後輸入下列命令：

```azurecli-interactive
az webapp log tail --name webappname --resource-group myResourceGroup
```
[Az webapp log tail](/cli/azure/webapp/log)命令具有使用`--provider`選項來篩選輸出的選項。 

若要使用 Azure CLI，以單一 ZIP 檔案的形式下載記錄檔，請開啟新的命令提示字元、Bash 或終端機會話，然後輸入下列命令：

```azurecli-interactive
az webapp log download --resource-group resourcegroupname --name functionappname
```

在執行此命令之前，您必須先啟用 Azure 入口網站或 Azure CLI 中的檔案系統記錄。

## <a name="environment-variables"></a>環境變數

在 Functions 中，[應用程式設定](functions-app-settings.md) (例如服務連接字串) 在執行期間會公開為環境變數。 您可以使用來存取這些設定`System.getenv("AzureWebJobsStorage")`。

下列範例會取得[應用程式設定](functions-how-to-use-azure-function-app-settings.md#settings)，並使用名為`myAppSetting`的機碼：

```java

public class Function {
    public String echo(@HttpTrigger(name = "req", methods = {"post"}, authLevel = AuthorizationLevel.ANONYMOUS) String req, ExecutionContext context) {
        context.getLogger().info("My app setting value: "+ System.getenv("myAppSetting"));
        return String.format(req);
    }
}

```

> [!NOTE]
> AppSetting FUNCTIONS_EXTENSION_VERSION 的值應該是 ~ 2 或 ~ 3，才能獲得優化的冷啟動體驗。

## <a name="next-steps"></a>後續步驟

如需有關 Azure Functions JAVA 開發的詳細資訊，請參閱下列資源：

* [Azure Functions 的最佳做法](functions-best-practices.md)
* [Azure Functions 開發人員參考](functions-reference.md)
* [Azure Functions 觸發程序和繫結](functions-triggers-bindings.md)
* 使用[Visual Studio Code](https://code.visualstudio.com/docs/java/java-azurefunctions)、 [IntelliJ](functions-create-maven-intellij.md)和[Eclipse](functions-create-maven-eclipse.md)進行本機開發和調試
* [使用 Visual Studio Code 針對 Java Azure Functions 進行遠端偵錯](https://code.visualstudio.com/docs/java/java-serverless#_remote-debug-functions-running-in-the-cloud)
* [Azure Functions 的 Maven 外掛程式](https://github.com/Microsoft/azure-maven-plugins/blob/develop/azure-functions-maven-plugin/README.md) 
* 簡化透過目標的`azure-functions:add`函式建立，並準備用於 ZIP 檔案[部署](deployment-zip-push.md)的臨時目錄。
