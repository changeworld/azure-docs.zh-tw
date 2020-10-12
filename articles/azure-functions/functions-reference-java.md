---
title: Azure Functions 的 JAVA 開發人員參考
description: 了解如何使用 Java 開發函式。
ms.topic: conceptual
ms.date: 09/14/2018
ms.custom: devx-track-java
ms.openlocfilehash: 1dd98ede537321403053e2e7c8a5f4f7272665d4
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "89144918"
---
# <a name="azure-functions-java-developer-guide"></a>Azure Functions Java 開發人員指南

本指南包含的詳細資訊可協助您使用 JAVA 成功開發 Azure Functions。

如果您是 Azure Functions 的新手，請先閱讀下列其中一篇文章，以 JAVA 開發人員為例：

| 開始使用 | 概念| 
| -- | -- |  
| <ul><li>[使用 Visual Studio Code 的 JAVA 函數](./functions-create-first-function-vs-code.md?pivots=programming-language-java)</li><li>[使用終端機/命令提示字元的 JAVA/Maven 函式](./functions-create-first-azure-function-azure-cli.md?pivots=programming-language-java)</li><li>[使用 Gradle 的 JAVA 函數](functions-create-first-java-gradle.md)</li><li>[使用 Eclipse 的 JAVA 函式](functions-create-maven-eclipse.md)</li><li>[使用 IntelliJ 構想的 JAVA 函式](functions-create-maven-intellij.md)</li></ul> | <ul><li>[開發人員指南](functions-reference.md)</li><li>[主機選項](functions-scale.md)</li><li>[效能 &nbsp; 考慮](functions-best-practices.md)</li></ul> |

## <a name="java-function-basics"></a>JAVA 函數基本概念

JAVA 函式是 `public` 方法，以註釋 `@FunctionName` 裝飾。 此方法定義 JAVA 函式的進入點，在特定封裝中必須是唯一的。 封裝可以有多個具有多個批註的公用方法的類別 `@FunctionName` 。 單一套件會部署至 Azure 中的函數應用程式。 在 Azure 中執行時，函數應用程式會提供個別 JAVA 函式的部署、執行和管理內容。

## <a name="programming-model"></a>程式設計模型 

[觸發程序和繫結](functions-triggers-bindings.md)的概念是 Azure Functions 的基礎。 觸發程序會開始執行您的程式碼。 繫結可讓您將資料傳至函式以及從函式傳回資料，而不需要撰寫自訂的資料存取程式碼。

## <a name="create-java-functions"></a>建立 JAVA 函式

為了讓您更輕鬆建立 JAVA 函式，有以 Maven 為基礎的工具和原型使用預先定義的 JAVA 範本，協助您建立具有特定函式觸發程序的專案。    

### <a name="maven-based-tooling"></a>以 Maven 為基礎的工具

下列開發人員環境具有 Azure Functions 工具，可讓您建立 JAVA 函式專案： 

+ [Visual Studio Code](https://code.visualstudio.com/docs/java/java-azurefunctions)
+ [Eclipse](functions-create-maven-eclipse.md)
+ [IntelliJ](functions-create-maven-intellij.md)

上述文章連結示範如何使用您選擇的 IDE 來建立第一個函式。 

### <a name="project-scaffolding"></a>建立專案的結構

如果您偏好從終端機執行命令列開發，則針對以 JAVA 為基礎的函式專案，使用 `Apache Maven` 原型來建立結構最簡單。 Azure Functions 的 JAVA Maven 原型已發佈於下列 _groupId_:_artifactId_: [com.microsoft.azure:azure-functions-archetype](https://search.maven.org/artifact/com.microsoft.azure/azure-functions-archetype/) 之下。 

下列命令使用此原型產生新的 JAVA 函式專案：

```
mvn archetype:generate \
    -DarchetypeGroupId=com.microsoft.azure \
    -DarchetypeArtifactId=azure-functions-archetype 
```

若要開始使用此原型，請參閱 [JAVA 快速入門](./functions-create-first-azure-function-azure-cli.md?pivots=programming-language-java)。 

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

您可以使用共用的 [host.json](functions-host-json.md) 檔案來設定函數應用程式。 每個函式都有自己的程式碼檔案 (.java) 和繫結設定檔 (function.json)。

您可以在專案中放入多個函式。 請勿將函式放入個別的 jar。 目標目錄中的 `FunctionApp` 會部署至 Azure 中的函數應用程式。

## <a name="triggers-and-annotations"></a>觸發程序和註解

 函式由觸發程序叫用，例如 HTTP 要求、計時器或更新資料。 函式必須處理該觸發程序和任何其他輸入，以產生一或多個輸出。

請使用 [com.microsoft.azure.functions.annotation.*](/java/api/com.microsoft.azure.functions.annotation) 套件中所包含的 Java 註釋，以將輸入和輸出繫結至方法。 如需詳細資訊，請參閱 [JAVA 參考文件](/java/api/com.microsoft.azure.functions.annotation)。

> [!IMPORTANT] 
> 您必須在 [local.settings.json](./functions-run-local.md#local-settings-file) 中設定 Azure 儲存體帳戶，以在本機執行 Azure Blob 儲存體、Azure 佇列儲存體或 Azure 資料表觸發程序。

範例：

```java
public class Function {
    public String echo(@HttpTrigger(name = "req", 
      methods = {HttpMethod.POST},  authLevel = AuthorizationLevel.ANONYMOUS) 
        String req, ExecutionContext context) {
        return String.format(req);
    }
}
```

以下是 [azure-functions-maven-plugin](https://mvnrepository.com/artifact/com.microsoft.azure/azure-functions-maven-plugin) 所產生的對應 `function.json`：

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

## <a name="java-versions"></a>Java 版本

_JAVA 11 的支援目前為預覽狀態_

建立函式應用程式時所使用的 JAVA 版本，在 Azure 中執行的函式是在 pom.xml 檔案中指定。 Maven 原型目前會產生 JAVA 8 的 pom.xml，您可以在發佈之前變更。 pom.xml 中的 JAVA 版本應該符合您在本機開發及測試應用程式的版本。 

### <a name="supported-versions"></a>支援的版本

下錶針對每個主要版本的函式執行時間（依作業系統），顯示目前支援的 JAVA 版本：

| Functions 版本 | Windows)  (JAVA 版本 | Linux)  (JAVA 版本 |
| ----- | ----- | --- |
| 3.x | 11 (preview) <br/>8 | 11 (preview) <br/>8 |
| 2.x | 8 | n/a |

除非您為部署指定 JAVA 版本，否則 Maven 原型會在部署至 Azure 期間預設為 JAVA 8。

### <a name="specify-the-deployment-version"></a>指定部署版本

您可以使用參數來控制 Maven 原型設為目標的 JAVA 版本 `-DjavaVersion` 。 此參數的值可以是乙太幣 `8` 或 `11` 。 JAVA 11 支援目前為預覽狀態。 

Maven 原型會產生以指定的 JAVA 版本為目標的 pom.xml。 pom.xml 中的下列元素指出要使用的 JAVA 版本：

| 元素 |  JAVA 8 值 | JAVA 11 值 | 描述 |
| ---- | ---- | ---- | --- |
| **`Java.version`** | 1.8 | 11 | Maven 編譯器-外掛程式所使用的 JAVA 版本。 |
| **`JavaVersion`** | 8 | 11 | Azure 中函數應用程式所裝載的 JAVA 版本。 |

下列範例顯示 pom.xml 檔案相關區段中的 JAVA 8 設定：

#### `Java.version`
:::code language="xml" source="~/functions-quickstart-java/functions-add-output-binding-storage-queue/pom.xml" range="12-19" highlight="14":::

#### `JavaVersion`
:::code language="xml" source="~/functions-quickstart-java/functions-add-output-binding-storage-queue/pom.xml" range="77-85" highlight="80":::

> [!IMPORTANT]
> 您必須將 JAVA_HOME 環境變數正確地設定為使用 Maven 編譯器代碼時所使用的 JDK 目錄。 請確定 JDK 的版本至少與設定的最高 `Java.version` 。 

### <a name="specify-the-deployment-os"></a>指定部署 OS

Maven 也可讓您指定在 Azure 中執行函數應用程式的作業系統。 使用 `os` 元素選擇作業系統。 

| 元素 |  Windows | Linux | Docker |
| ---- | ---- | ---- | --- |
| **`os`** | windows | Linux | docker |

下列範例顯示 pom.xml 檔案區段中的作業系統設定 `runtime` ：

:::code language="xml" source="~/functions-quickstart-java/functions-add-output-binding-storage-queue/pom.xml" range="77-85" highlight="79":::
 
## <a name="jdk-runtime-availability-and-support"></a>JDK 執行階段可用性和支援 

若要在本機開發 JAVA 函數應用程式，請從[Azul 系統](https://www.azul.com/downloads/azure-only/zulu/)下載並使用適用于 Azure JAVA jdk 的適當[Azul 祖魯企業版](https://assets.azul.com/files/Zulu-for-Azure-FAQ.pdf)。 當您將函數應用程式部署至雲端時，Azure Functions 使用 Azul JAVA JDK 執行時間。

[合格的支援方案](https://azure.microsoft.com/support/plans/)讓您享有 JDK 和函數應用程式相關問題的 [Azure 支援](https://azure.microsoft.com/support/)。

## <a name="customize-jvm"></a>自訂 JVM

函式可讓您自訂用來執行 JAVA 函式的 JAVA 虛擬機器 (JVM)。 預設會使用[下列 JVM 選項](https://github.com/Azure/azure-functions-java-worker/blob/master/worker.config.json#L7)：

* `-XX:+TieredCompilation`
* `-XX:TieredStopAtLevel=1`
* `-noverify` 
* `-Djava.net.preferIPv4Stack=true`
* `-jar`

您可以在名為 `JAVA_OPTS` 的應用程式設定中提供額外的引數。 在 Azure 入口網站或 Azure CLI 中，您可以在已部署至 Azure 的函數應用程式中新增應用程式設定。

> [!IMPORTANT]  
> 在「使用量」方案中，您也必須新增值為 0 的 WEBSITE_USE_PLACEHOLDER 設定，自訂才會生效。 此設定會延長 JAVA 函式的冷啟動時間。

### <a name="azure-portal"></a>Azure 入口網站

在 [Azure 入口網站](https://portal.azure.com)中，使用 [[應用程式設定]](functions-how-to-use-azure-function-app-settings.md#settings) 索引標籤來新增 `JAVA_OPTS` 設定。

### <a name="azure-cli"></a>Azure CLI

您可以使用 [az functionapp config appsettings set](/cli/azure/functionapp/config/appsettings) 命令來設定 `JAVA_OPTS`，如下列範例所示：

#### <a name="consumption-plan"></a>[使用量方案](#tab/consumption)
```azurecli-interactive
az functionapp config appsettings set \
--settings "JAVA_OPTS=-Djava.awt.headless=true" \
"WEBSITE_USE_PLACEHOLDER=0" \
--name <APP_NAME> --resource-group <RESOURCE_GROUP>
```
#### <a name="dedicated-plan--premium-plan"></a>[專用方案/進階方案](#tab/dedicated+premium)
```azurecli-interactive
az functionapp config appsettings set \
--settings "JAVA_OPTS=-Djava.awt.headless=true" \
--name <APP_NAME> --resource-group <RESOURCE_GROUP>
```
---

此範例啟用無周邊模式。 請將 `<APP_NAME>` 換成您的函數應用程式名稱，將 `<RESOURCE_GROUP>` 換成資源群組。 

## <a name="third-party-libraries"></a>第三方程式庫 

Azure Functions 支援使用第三方程式庫。 根據預設，在 [`mvn package`](https://github.com/Microsoft/azure-maven-plugins/blob/master/azure-functions-maven-plugin/README.md#azure-functionspackage) 目標期間，將會自動包裝專案 `pom.xml` 檔案中指定的所有相依性。 對於在 `pom.xml` 檔案中未指定為相依性的程式庫，請將其放入函式根目錄的 `lib` 目錄中。 在執行階段，放在 `lib` 目錄中的相依性會新增至系統類別載入器。

預設會在類別路徑上提供 `com.microsoft.azure.functions:azure-functions-java-library` 相依性，因此不需要加入 `lib` 目錄中。 此外，[azure-functions-java-worker](https://github.com/Azure/azure-functions-java-worker) 還將[這裡](https://github.com/Azure/azure-functions-java-worker/wiki/Azure-Java-Functions-Worker-Dependencies)列出的相依性新增至類別路徑。

## <a name="data-type-support"></a>資料類型支援

您可以使用 Plain Old JAVA Object (POJO)、`azure-functions-java-library` 中定義的類型或基本資料類型 (例如字串和整數)，以繫結至輸入或輸出繫結。

### <a name="pojos"></a>POJO

為了將輸入資料轉換成 POJO，[azure-functions-java-worker](https://github.com/Azure/azure-functions-java-worker) 使用 [gson](https://github.com/google/gson) 程式庫。 作為函式輸入的 POJO 類型應為 `public`。

### <a name="binary-data"></a>二進位資料

在 function.json 中將 `dataType` 欄位設定為 `binary`，以將二進位輸入或輸出繫結至 `byte[]`：

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

如果您預期會有 Null 值，請使用 `Optional<T>`。

## <a name="bindings"></a>繫結

輸入和輸出繫結提供從您的程式碼內連線到資料的宣告式方法。 一個函數可以有多個輸入和輸出繫結。

### <a name="input-binding-example"></a>輸入繫結範例

```java
package com.example;

import com.microsoft.azure.functions.annotation.*;

public class Function {
    @FunctionName("echo")
    public static String echo(
        @HttpTrigger(name = "req", methods = { HttpMethod.PUT }, authLevel = AuthorizationLevel.ANONYMOUS, route = "items/{id}") String inputReq,
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

您可以使用 HTTP 要求來叫用此函式。 
- HTTP 要求承載會以 `String` 形式傳給 `inputReq` 引數。
- 系統會從資料表儲存體中取出一個項目，並以 `TestInputData` 形式傳給 `inputData` 引數。

若要接收輸入批次，您可以繫結至 `String[]`、`POJO[]`、`List<String>` 或 `List<POJO>`。

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

每當設定的事件中樞內有新資料時，就會觸發此函式。 由於 `cardinality` 設為 `MANY`，此函式會從事件中樞收到一批訊息。 來自事件中樞的 `EventData` 會轉換成 `TestEventData` 供函式執行。

### <a name="output-binding-example"></a>輸出繫結範例

您可以使用 `$return` 將「輸出繫結」繫結至傳回值。 

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

請在 HttpRequest 上叫用此函式。 此函式會將多個值寫入佇列儲存體。

## <a name="httprequestmessage-and-httpresponsemessage"></a>HttpRequestMessage 和 HttpResponseMessage

 這些都在 `azure-functions-java-library` 中定義。 也是與 HttpTrigger 函式搭配使用的協助程式類型。

| 特殊類型      |       目標        | 一般使用方式                  |
| --------------------- | :-----------------: | ------------------------------ |
| `HttpRequestMessage<T>`  |    HTTP 觸發程序     | 取得方法、標頭或查詢 |
| `HttpResponseMessage` | HTTP 輸出繫結 | 傳回 200 以外的狀態   |

## <a name="metadata"></a>中繼資料

有幾個觸發程序會將[觸發程序中繼資料](./functions-triggers-bindings.md)與輸入資料一起傳送。 您可以使用註釋 `@BindingName` 來繫結至觸發程序中繼資料。


```Java
package com.example;

import java.util.Optional;
import com.microsoft.azure.functions.annotation.*;


public class Function {
    @FunctionName("metadata")
    public static String metadata(
        @HttpTrigger(name = "req", methods = { HttpMethod.GET, HttpMethod.POST }, authLevel = AuthorizationLevel.ANONYMOUS) Optional<String> body,
        @BindingName("name") String queryValue
    ) {
        return body.orElse(queryValue);
    }
}
```
在上述範例中，`queryValue` 繫結至 HTTP 要求 URL `http://{example.host}/api/metadata?name=test` 中的查詢字串參數 `name`。 以下是另一個範例，示範如何從佇列觸發程序中繼資料繫結至 `Id`。

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
> 註釋中提供的名稱必須符合中繼資料屬性。

## <a name="execution-context"></a>執行內容

`azure-functions-java-library` 中定義的 `ExecutionContext` 包含協助程式方法，用來與函式執行階段進行通訊。 如需詳細資訊，請參閱 [ExecutionCoNtext 參考文章](/java/api/com.microsoft.azure.functions.executioncontext)。

### <a name="logger"></a>記錄器

使用 `ExecutionContext` 中定義的 `getLogger`，從函式程式碼寫入記錄。

範例：

```java

import com.microsoft.azure.functions.*;
import com.microsoft.azure.functions.annotation.*;

public class Function {
    public String echo(@HttpTrigger(name = "req", methods = {HttpMethod.POST}, authLevel = AuthorizationLevel.ANONYMOUS) String req, ExecutionContext context) {
        if (req.isEmpty()) {
            context.getLogger().warning("Empty request body received by function " + context.getFunctionName() + " with invocation " + context.getInvocationId());
        }
        return String.format(req);
    }
}
```

## <a name="view-logs-and-trace"></a>檢視記錄和追蹤

您可以使用 Azure CLI 來串流 JAVA stdout 和 stderr 記錄，以及其他應用程式記錄。 

以下示範如何使用 Azure CLI，將函式應用程式設定為寫入應用程式記錄：

```azurecli-interactive
az webapp log config --name functionname --resource-group myResourceGroup --application-logging true
```

若要使用 Azure CLI 來串流函數應用程式的記錄輸出，請開啟新的命令提示字元、Bash 或終端機工作階段，然後輸入下列命令：

```azurecli-interactive
az webapp log tail --name webappname --resource-group myResourceGroup
```
[az webapp log tail](/cli/azure/webapp/log) 命令有選項讓您使用 `--provider` 選項來篩選輸出。 

若要使用 Azure CLI 將記錄檔下載為單一 ZIP 檔案，請開啟新的命令提示字元、Bash 或終端機工作階段，然後輸入下列命令：

```azurecli-interactive
az webapp log download --resource-group resourcegroupname --name functionappname
```

執行此命令之前，您必須先在 Azure 入口網站或 Azure CLI 中啟用檔案系統記錄。

## <a name="environment-variables"></a>環境變數

在 Functions 中，[應用程式設定](functions-app-settings.md) (例如服務連接字串) 在執行期間會公開為環境變數。 您可以使用 `System.getenv("AzureWebJobsStorage")` 來存取這些設定。

下列範例會使用名為 `myAppSetting` 的索引鍵來取得[應用程式設定](functions-how-to-use-azure-function-app-settings.md#settings)：

```java

public class Function {
    public String echo(@HttpTrigger(name = "req", methods = {HttpMethod.POST}, authLevel = AuthorizationLevel.ANONYMOUS) String req, ExecutionContext context) {
        context.getLogger().info("My app setting value: "+ System.getenv("myAppSetting"));
        return String.format(req);
    }
}

```

> [!NOTE]
> AppSetting FUNCTIONS_EXTENSION_VERSION 的值應該為 ~2 或 ~3，才能獲得最佳的冷啟動體驗。

## <a name="next-steps"></a>後續步驟

如需 Azure Functions JAVA 開發的詳細資訊，請參閱下列資源：

* [Azure Functions 的最佳做法](functions-best-practices.md)
* [Azure Functions 開發人員參考](functions-reference.md)
* [Azure Functions 觸發程序和繫結](functions-triggers-bindings.md)
* 使用 [Visual Studio Code](https://code.visualstudio.com/docs/java/java-azurefunctions)[IntelliJ](functions-create-maven-intellij.md) 和 [Eclipse](functions-create-maven-eclipse.md) 進行本機開發與偵錯
* [使用 Visual Studio Code 從遠端偵錯 JAVA 函式](https://code.visualstudio.com/docs/java/java-serverless#_remote-debug-functions-running-in-the-cloud)
* [適用於 Azure Functions 的 Maven 外掛程式](https://github.com/Microsoft/azure-maven-plugins/blob/develop/azure-functions-maven-plugin/README.md) 
* 透過 `azure-functions:add` 目標來簡化函式建立，並為 [ZIP 檔案部署](deployment-zip-push.md)準備暫存目錄。