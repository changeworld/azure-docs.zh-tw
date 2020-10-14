---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 03/25/2020
ms.author: glenga
ms.openlocfilehash: 2b2c043e70aac14c7fc6f0b58aae257624b05d13
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "80673277"
---
在 Java 專案中，繫結會被定義為函式方法上的繫結註釋。 系統接著會根據這些註釋自動產生 *function.json* 檔案。

瀏覽至 _src/main/java_  底下您的函式程式碼位置，開啟 *Function java*  專案檔案，然後將下列參數新增至 `run` 方法定義：

```java
@QueueOutput(name = "msg", queueName = "outqueue", connection = "AzureWebJobsStorage") OutputBinding<String> msg
```

`msg` 參數是 [`OutputBinding<T>`](/java/api/com.microsoft.azure.functions.outputbinding) 類型，其代表會在函式完成時寫入輸出繫結的字串集合。 在此情況下，輸出是名為 `outqueue` 的儲存體佇列。 儲存體帳戶的連接字串是由 `connection` 方法設定。 您會傳遞包含儲存體帳戶連接字串的應用程式設定，而不是連接字串本身。

`run` 方法定義現在應該如下列範例所示︰  

```java
@FunctionName("HttpTrigger-Java")
public HttpResponseMessage run(
        @HttpTrigger(name = "req", methods = {HttpMethod.GET, HttpMethod.POST}, authLevel = AuthorizationLevel.FUNCTION)  
        HttpRequestMessage<Optional<String>> request, 
        @QueueOutput(name = "msg", queueName = "outqueue", connection = "AzureWebJobsStorage") 
        OutputBinding<String> msg, final ExecutionContext context) {
    ...
}
```