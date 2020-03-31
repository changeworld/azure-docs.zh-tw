---
title: Azure 函數的 JAVA 開發人員引用
description: 了解如何使用 Java 開發函式。
ms.topic: conceptual
ms.date: 09/14/2018
ms.openlocfilehash: 4af2a860657f6066112146e1f88d81861d9430ea
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79276747"
---
# <a name="azure-functions-java-developer-guide"></a>Azure Functions Java 開發人員指南

Azure 函數運行時支援[JAVA SE 8 LTS（zulu8.31.0.2-jre8.0.181-win_x64）。](https://repos.azul.com/azure-only/zulu/packages/zulu-8/8u181/) 本指南包含有關使用 JAVA 編寫 Azure 函數的複雜性的資訊。

與其他語言一樣，函數應用可能具有一個或多個功能。 JAVA 函數是一`public`種方法，用注釋`@FunctionName`進行修飾。 此方法定義 JAVA 函數的條目，並且必須在特定包中是唯一的。 一個在 JAVA 中編寫的函數應用程式可能具有多個類，其中有多個使用 的`@FunctionName`公開方法進行了加號。

本文假設您已經讀過 [Azure Functions 開發人員參考](functions-reference.md)。 您還應使用[Visual Studio 代碼](functions-create-first-function-vs-code.md)或[Maven](functions-create-first-java-maven.md)完成函數快速入門以創建第一個函數。

## <a name="programming-model"></a>程式設計模型 

[觸發程序和繫結](functions-triggers-bindings.md)的概念是 Azure Functions 的基礎。 觸發程序會開始執行您的程式碼。 繫結可讓您將資料傳至函式以及從函式傳回資料，而不需要撰寫自訂的資料存取程式碼。

## <a name="create-java-functions"></a>創建 JAVA 函數

為了更輕鬆地創建 JAVA 函數，有一些基於 Maven 的工具和原型使用預定義的 JAVA 範本來説明您創建具有特定函數觸發器的專案。    

### <a name="maven-based-tooling"></a>基於 Maven 的工具

以下開發人員環境具有 Azure 函數工具，允許您創建 JAVA 函數專案： 

+ [視覺工作室代碼](https://code.visualstudio.com/docs/java/java-azurefunctions)
+ [Eclipse](functions-create-maven-eclipse.md)
+ [IntelliJ](functions-create-maven-intellij.md)

上面的文章連結向您展示如何使用您選擇的 IDE 創建第一個函數。 

### <a name="project-scaffolding"></a>專案支架

如果您更喜歡終端的命令列開發，則使用基於 JAVA 的函數專案的最簡單方法是使用`Apache Maven`原型。 目前馬文有兩種函數原型：

+ **JAVA 原型**： 在以下組 Id 和工件 Id [com.microsoft.azure 中發佈：azure 函數原型](https://search.maven.org/artifact/com.microsoft.azure/azure-functions-archetype/)：

    ```
    mvn archetype:generate \
        -DarchetypeGroupId=com.microsoft.azure \
        -DarchetypeArtifactId=azure-functions-archetype 
    ```

    要開始使用此原型，請參閱[JAVA 快速入門](functions-create-first-java-maven.md)。 

+ **科特林原型（預覽）** 發佈在以下組Id和工件id [com.microsoft.azure：azure函數-科特林原型](https://search.maven.org/artifact/com.microsoft.azure/azure-functions-kotlin-archetype/)：

    ```
    mvn archetype:generate \
        -DarchetypeGroupId=com.microsoft.azure \
        -DarchetypeArtifactId=azure-functions-kotlin-archetype
    ```

這些原型的原始程式碼可以在[Azure Maven 原型 GitHub 存儲庫](https://github.com/microsoft/azure-maven-archetypes)中找到。


## <a name="folder-structure"></a>資料夾結構

下面是 Azure 函數 JAVA 專案的資料夾結構：

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

_• 科特林專案看起來非常相似，因為它仍然是馬文_

您可以使用共用[的 host.json](functions-host-json.md)檔來配置函數應用。 每個函式都有自己的程式碼檔案 (.java) 和繫結設定檔 (function.json)。

您可以在專案中放入多個函式。 請勿將函式放入個別的 jar。 目標`FunctionApp`目錄中的內容是部署到 Azure 中的函數應用的內容。

## <a name="triggers-and-annotations"></a>觸發程序和註解

 函數由觸發器調用，例如 HTTP 要求、計時器或資料更新。 您的函數需要處理該觸發器和任何其他輸入，以生成一個或多個輸出。

請使用 [com.microsoft.azure.functions.annotation.*](/java/api/com.microsoft.azure.functions.annotation) 套件中所包含的 Java 註釋，以將輸入和輸出繫結至方法。 有關詳細資訊，請參閱 JAVA[參考文檔](/java/api/com.microsoft.azure.functions.annotation)。

> [!IMPORTANT] 
> 您必須在[本地.settings.json](/azure/azure-functions/functions-run-local#local-settings-file)中配置 Azure 存儲帳戶，以便在本地運行 Azure Blob 存儲、Azure 佇列存儲或 Azure 表存儲觸發器。

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

下面是 azure`function.json`[函數-maven-外掛程式](https://mvnrepository.com/artifact/com.microsoft.azure/azure-functions-maven-plugin)對應生成的 ：

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

對於 JAVA 函數應用程式的本地開發，請下載並使用[Azul Zulu 企業版，用於來自](https://assets.azul.com/files/Zulu-for-Azure-FAQ.pdf) [Azul Systems](https://www.azul.com/downloads/azure-only/zulu/)的 Azure JAVA 8 JDK。 當您將函數應用程式部署至雲端時，Azure Functions 使用 Azul Java 8 JDK 執行階段。

[具有](https://azure.microsoft.com/support/)[限定的支援計畫](https://azure.microsoft.com/support/plans/)提供了對 JdK 和函數應用問題的 Azure 支援。

## <a name="customize-jvm"></a>自訂 JVM

函數允許您自訂用於運行 JAVA 函數的 JAVA 虛擬機器 （JVM）。 預設情況下使用[以下 JVM 選項](https://github.com/Azure/azure-functions-java-worker/blob/master/worker.config.json#L7)：

* `-XX:+TieredCompilation`
* `-XX:TieredStopAtLevel=1`
* `-noverify` 
* `-Djava.net.preferIPv4Stack=true`
* `-jar`

您可以在名為`JAVA_OPTS`的應用設置中提供其他參數。 您可以將應用設置添加到 Azure 門戶或 Azure CLI 中部署到 Azure 的函數應用。

### <a name="azure-portal"></a>Azure 入口網站

在[Azure 門戶](https://portal.azure.com)中，使用"[應用程式設定"選項卡](functions-how-to-use-azure-function-app-settings.md#settings)添加`JAVA_OPTS`設置。

### <a name="azure-cli"></a>Azure CLI

可以使用 az[函數應用配置應用設置設置](/cli/azure/functionapp/config/appsettings)命令來設置`JAVA_OPTS`，如以下示例所示：

```azurecli-interactive
az functionapp config appsettings set --name <APP_NAME> \
--resource-group <RESOURCE_GROUP> \
--settings "JAVA_OPTS=-Djava.awt.headless=true"
```
此示例啟用無頭模式。 替換為`<APP_NAME>`函數應用的名稱，並`<RESOURCE_GROUP>`替換為資源組。

> [!WARNING]  
> 在["消耗"計畫中](functions-scale.md#consumption-plan)，必須添加值`WEBSITE_USE_PLACEHOLDER`為 的`0`設置。  
此設置確實會增加 JAVA 函數的冷啟動時間。

## <a name="third-party-libraries"></a>第三方程式庫 

Azure Functions 支援使用第三方程式庫。 預設情況下，專案`pom.xml`檔中指定的所有依賴項在[`mvn package`](https://github.com/Microsoft/azure-maven-plugins/blob/master/azure-functions-maven-plugin/README.md#azure-functionspackage)目標期間會自動捆綁。 對於在 `pom.xml` 檔案中未指定為相依性的程式庫，請將其放入函式根目錄的 `lib` 目錄中。 放置在`lib`目錄中的依賴項在運行時將添加到系統類載入程式。

預設情況下`com.microsoft.azure.functions:azure-functions-java-library`，依賴項在類路徑上提供，不需要包含在`lib`目錄中。 此外[，azure 函數-java 輔助角色](https://github.com/Azure/azure-functions-java-worker)將[此處](https://github.com/Azure/azure-functions-java-worker/wiki/Azure-Java-Functions-Worker-Dependencies)列出的依賴項添加到類路徑。

## <a name="data-type-support"></a>資料類型支援

您可以使用普通舊 JAVA 物件 （POJOs）、在`azure-functions-java-library`中定義的類型或基元資料類型（如字串和整數）綁定到輸入或輸出綁定。

### <a name="pojos"></a>Pojo

要將輸入資料轉換為 POJO，azure[函數-java 輔助角色](https://github.com/Azure/azure-functions-java-worker)使用[gson](https://github.com/google/gson)庫。 作為函式輸入的 POJO 類型應為 `public`。

### <a name="binary-data"></a>二進位資料

通過將函數中的`byte[]``dataType`欄位設置為 ：json，將二進位輸入或輸出綁定到`binary`：

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

如果預期為 null 值`Optional<T>`，請使用 。

## <a name="bindings"></a>繫結

輸入和輸出繫結提供從您的程式碼內連線到資料的宣告式方法。 一個函數可以有多個輸入和輸出繫結。

### <a name="input-binding-example"></a>輸入綁定示例

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

使用 HTTP 要求調用此函數。 
- HTTP 要求有效負載作為`String`參數`inputReq`傳遞。
- 從表存儲中檢索一個條目，並將其傳遞給`TestInputData`參數`inputData`。

要接收一批輸入，可以綁定`String[]`到`POJO[]`、 或`List<String>`。 `List<POJO>`

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

每當設定的事件中樞內有新資料時，就會觸發此函式。 由於`cardinality`設置為`MANY`，因此函數從事件中心接收一批消息。 `EventData`從事件中心轉換為`TestEventData`函數執行。

### <a name="output-binding-example"></a>輸出繫結範例

可以使用 將輸出綁定綁定綁定綁定到傳回值`$return`。 

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

在 HttpRequest 上調用此函數。 它將多個值寫入佇列存儲。

## <a name="httprequestmessage-and-httpresponsemessage"></a>HttpRequestMessage 和 HttpResponseMessage

 這些在 中`azure-functions-java-library`定義。 它們是使用 HttpTrigger 函數的説明器類型。

| 專用類型      |       目標        | 一般使用方式                  |
| --------------------- | :-----------------: | ------------------------------ |
| `HttpRequestMessage<T>`  |    HTTP 觸發程序     | 獲取方法、標頭或查詢 |
| `HttpResponseMessage` | HTTP 輸出繫結 | 返回 200 以外的狀態   |

## <a name="metadata"></a>中繼資料

有幾個觸發程序會將[觸發程序中繼資料](/azure/azure-functions/functions-triggers-bindings)與輸入資料一起傳送。 您可以使用注釋`@BindingName`綁定來觸發中繼資料。


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
在前面的示例中，`queryValue`綁定到 HTTP 要求 URL`name`中的查詢字串參數`http://{example.host}/api/metadata?name=test`。 下面是另一個示例，演示如何綁定到`Id`佇列觸發器中繼資料。

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
> 注釋中提供的名稱需要與中繼資料屬性匹配。

## <a name="execution-context"></a>執行內容

`ExecutionContext`中`azure-functions-java-library`定義的 包含與函數運行時通信的説明器方法。

### <a name="logger"></a>記錄器

使用`getLogger`中`ExecutionContext`定義的 ，從 函數代碼編寫日誌。

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

可以使用 Azure CLI 資料流 JAVA 停滯和停滯日誌記錄以及其他應用程式日誌記錄。 

以下是如何使用 Azure CLI 配置函數應用以編寫應用程式日誌記錄：

```azurecli-interactive
az webapp log config --name functionname --resource-group myResourceGroup --application-logging true
```

要使用 Azure CLI 資料流函數應用的日誌記錄輸出，請打開新的命令提示符、Bash 或終端會話，然後輸入以下命令：

```azurecli-interactive
az webapp log tail --name webappname --resource-group myResourceGroup
```
[az Webapp 日誌尾部](/cli/azure/webapp/log)命令具有使用 選項`--provider`篩選輸出的選項。 

要使用 Azure CLI 將日誌檔下載為單個 ZIP 檔，請打開新的命令提示符、Bash 或終端會話，然後輸入以下命令：

```azurecli-interactive
az webapp log download --resource-group resourcegroupname --name functionappname
```

運行此命令之前，必須在 Azure 門戶或 Azure CLI 中啟用了檔案系統日誌記錄。

## <a name="environment-variables"></a>環境變數

在 Functions 中，[應用程式設定](functions-app-settings.md) (例如服務連接字串) 在執行期間會公開為環境變數。 您可以使用 訪問這些設置， `System.getenv("AzureWebJobsStorage")`

下面的示例獲取[應用程式設定](functions-how-to-use-azure-function-app-settings.md#settings)，鍵名為`myAppSetting`：

```java

public class Function {
    public String echo(@HttpTrigger(name = "req", methods = {"post"}, authLevel = AuthorizationLevel.ANONYMOUS) String req, ExecutionContext context) {
        context.getLogger().info("My app setting value: "+ System.getenv("myAppSetting"));
        return String.format(req);
    }
}

```

## <a name="next-steps"></a>後續步驟

有關 Azure 函數 JAVA 開發的詳細資訊，請參閱以下資源：

* [Azure Functions 的最佳做法](functions-best-practices.md)
* [Azure Functions 開發人員參考](functions-reference.md)
* [Azure 函數觸發器和綁定](functions-triggers-bindings.md)
* 本地開發和調試與[視覺工作室代碼](https://code.visualstudio.com/docs/java/java-azurefunctions)[，IntelliJ，](functions-create-maven-intellij.md)和[Eclipse](functions-create-maven-eclipse.md)
* [使用 Visual Studio Code 針對 Java Azure Functions 進行遠端偵錯](https://code.visualstudio.com/docs/java/java-serverless#_remote-debug-functions-running-in-the-cloud)
* [用於 Azure 函數的 Maven 外掛程式](https://github.com/Microsoft/azure-maven-plugins/blob/develop/azure-functions-maven-plugin/README.md) 
* 通過目標簡化函數創建`azure-functions:add`，並為[ZIP 檔部署](deployment-zip-push.md)準備臨時目錄。
