---
title: 使用 AutoRest 建立 Azure 數位 Twins 的自訂 Sdk
titleSuffix: Azure Digital Twins
description: '瞭解如何產生自訂 Sdk，以搭配 c # 以外的語言使用 Azure 數位 Twins。'
author: baanders
ms.author: baanders
ms.date: 4/24/2020
ms.topic: how-to
ms.service: digital-twins
ms.custom: devx-track-js
ms.openlocfilehash: 158d22ffb3bc5486e0523c07cc2c022c49f2ee9c
ms.sourcegitcommit: 4b76c284eb3d2b81b103430371a10abb912a83f4
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/01/2020
ms.locfileid: "93145594"
---
# <a name="create-custom-sdks-for-azure-digital-twins-using-autorest"></a>使用 AutoRest 建立 Azure 數位 Twins 的自訂 Sdk

現在，用來與 Azure 數位 Twins Api 互動的唯一已發佈資料平面 Sdk 適用于 .NET (c # ) 、JavaScript 和 JAVA。 您可以閱讀有關這些 Sdk 的資訊，以及有關 [*如何：使用 Azure 數位 Twins api 和 sdk*](how-to-use-apis-sdks.md)的 api。 如果您使用另一種語言，本文將說明如何使用 AutoRest，以您所選的語言產生您自己的資料平面 SDK。

>[!NOTE]
> 您也可以使用 AutoRest 來產生控制平面 SDK （如果您想要的話）。 若要這樣做，請使用最新的 **控制平面 swagger** (OpenAPI) 檔案從 [控制平面 swagger 資料夾](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/digitaltwins/resource-manager/Microsoft.DigitalTwins/) （而不是資料平面1）來完成本文中的步驟。

## <a name="set-up-your-machine"></a>設定您的電腦

若要產生 SDK，您將需要：
* 目前不支援[AutoRest](https://github.com/Azure/autorest)，version 2.0.4413 (第3版) 
* [Node.js](https://nodejs.org) 為 AutoRest 的先決條件
* 最新的 Azure 數位 Twins **資料平面 swagger** (OpenAPI 來自 [資料平面 swagger 資料夾](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/digitaltwins/data-plane/Microsoft.DigitalTwins)的) 檔案，以及其隨附的範例資料夾。  將 Swagger 檔案 *digitaltwins.js* 和其範例資料夾下載至您的本機電腦。

一旦您的電腦具備上述清單中的一切，您就可以開始使用 AutoRest 來建立 SDK。

## <a name="create-the-sdk-with-autorest"></a>使用 AutoRest 建立 SDK 

如果您已安裝 Node.js，可以執行此命令以確定您已安裝正確版本的 AutoRest：
```cmd/sh
npm install -g autorest@2.0.4413
```

若要對 Azure 數位 Twins Swagger 檔案執行 AutoRest，請遵循下列步驟：
1. 將 Azure 數位 Twins Swagger 檔案及其隨附的範例資料夾複製到工作目錄。
2. 使用 [命令提示字元] 視窗切換到該工作目錄。
3. 使用下列命令執行 AutoRest。 將 `<language>` 預留位置取代為您選擇的語言： `python` 、 `java` 、 `go` 等等。  (您可以在 [AUTOREST 讀我檔案](https://github.com/Azure/autorest)中找到完整的選項清單。 ) 

```cmd/sh
autorest --input-file=digitaltwins.json --<language> --output-folder=ADTApi --add-credentials --azure-arm --namespace=ADTApi
```

因此，您會在工作目錄中看到名為 *ADTApi* 的新資料夾。 產生的 SDK 檔案將會具有命名空間 *ADTApi* 。 您將繼續使用該命名空間，此命名空間是本文中其餘的使用範例。

AutoRest 支援各種不同的語言程式碼產生器。

## <a name="add-the-sdk-to-a-visual-studio-project"></a>將 SDK 新增至 Visual Studio 專案

您可以將 AutoRest 所產生的檔案直接包含在 .NET 方案中。 不過，您可能會想要在數個不同的專案中包含 Azure 數位 Twins SDK， (用戶端應用程式、Azure Functions 應用程式等) 。 基於這個理由，從產生的檔案中建立個別的專案 (.NET 類別庫) 會很有用。 然後，您可以將此類別庫專案包含在數個方案中，做為專案參考。

本節提供的指示說明如何將 SDK 建立為類別庫，也就是它自己的專案，而且可以包含在其他專案中。 這些步驟依賴 **Visual Studio** (您可以從 [這裡](https://visualstudio.microsoft.com/downloads/) 安裝最新版本) 。

以下是步驟：

1. 為類別庫建立新的 Visual Studio 方案
2. 使用 *ADTApi* 做為專案名稱
3. 在 [方案瀏覽器] 中，以滑鼠右鍵選取所產生方案的 *ADTApi* 專案，然後選擇 [ *加入 > 現有專案* ]。
4. 尋找您產生 SDK 的資料夾，然後選取根目錄層級的檔案
5. 按 [確定]
6. 將資料夾新增至專案 (以滑鼠右鍵選取方案總管中的專案，然後選擇 [ *加入 > 新資料夾* ]) 
7. 命名資料夾 *模型*
8. 以滑鼠右鍵選取 [方案瀏覽器] 中的 [ *模型* ] 資料夾，然後選取 [ *加入 > 現有專案* ]。
9. 選取所產生 SDK 的 [ *模型* ] 資料夾中的檔案，然後按 [確定]

若要成功建立 SDK，您的專案將需要下列參考：
* `Microsoft.Rest.ClientRuntime`
* `Microsoft.Rest.ClientRuntime.Azure`

若要新增這些 *工具 > nuget 封裝管理員 > 管理解決方案的 Nuget 套件* ...。

1. 在面板中，確定已選取 [ *流覽* ] 索引標籤
2. 搜尋 *Microsoft. Rest*
3. 選取 `ClientRuntime` 和 `ClientRuntime.Azure` 套件，並將其新增至您的方案

您現在可以建立專案，並在您撰寫的任何 Azure 數位 Twins 應用程式中，將其納入為專案參考。

## <a name="general-guidelines-for-generated-sdks"></a>所產生 Sdk 的一般指導方針

本章節包含使用所產生 SDK 的一般資訊與指導方針。

### <a name="synchronous-and-asynchronous-calls"></a>同步和非同步呼叫

所有 SDK 函式都有同步和非同步版本。

### <a name="typed-and-untyped-data"></a>具型別和不具類型的資料

REST API 呼叫通常會傳回強型別物件。 不過，因為 Azure 數位 Twins 可讓使用者定義自己的自訂類型來進行 Twins，所以沒有任何方法可預先定義許多 Azure 數位 Twins 呼叫的靜態傳回資料。 相反地，Api 會傳回強型別包裝函式類型（如果適用的話），而且自訂型別對應項資料會在 Json.NET 物件中， (在 API 簽章) 中出現資料類型「物件」的任何地方使用。 您可以在程式碼中適當地轉換這些物件。

### <a name="error-handling"></a>錯誤處理

每當 SDK 發生錯誤時 (包括 HTTP 錯誤（如 404) ），SDK 將會擲回例外狀況。 基於這個理由，請務必將所有 API 呼叫封裝在 try/catch 區塊內。

以下程式碼片段會嘗試新增對應項並攔截此進程中的任何錯誤：

```csharp
try
{
    await client.DigitalTwins.AddAsync(id, initData);
    Console.WriteLine($"Created a twin successfully: {id}");
}
catch (ErrorResponseException e)
{
    Console.WriteLine($"*** Error creating twin {id}: {e.Response.StatusCode}"); 
}
```

### <a name="paging"></a>Paging

AutoRest 會針對 SDK 產生兩種類型的分頁模式：
* 一個用於所有 Api （查詢 API 除外）
* 一個用於查詢 API

在非查詢分頁模式中，以下程式碼片段顯示如何從 Azure 數位 Twins 抓取傳出關聯性的分頁清單：

```csharp
 try 
 {
     // List the relationships.
    AsyncPageable<BasicRelationship> results = client.GetRelationshipsAsync<BasicRelationship>(srcId);
    Console.WriteLine($"Twin {srcId} is connected to:");
    // Iterate through the relationships found.
    int numberOfRelationships = 0;
    await foreach (string rel in results)
    {
         ++numberOfRelationships;
         // Do something with each relationship found
         Console.WriteLine($"Found relationship-{rel.Name}->{rel.TargetId}");
    }
    Console.WriteLine($"Found {numberOfRelationships} relationships on {srcId}");
} catch (RequestFailedException rex) {
    Console.WriteLine($"Relationship retrieval error: {rex.Status}:{rex.Message}");   
}
```

第二個模式只會針對查詢 API 產生。 它會 `continuationToken` 明確使用。

以下是使用此模式的範例：

```csharp
string query = "SELECT * FROM digitaltwins";
string conToken = null; // continuation token from the query
int page = 0;
try
{
    // Repeat the query while there are pages
    do
    {
        QuerySpecification spec = new QuerySpecification(query, conToken);
        QueryResult qr = await client.Query.QueryTwinsAsync(spec);
        page++;
        Console.WriteLine($"== Query results page {page}:");
        if (qr.Items != null)
        {
            // Query returns are JObjects
            foreach(JObject o in qr.Items)
            {
                string twinId = o.Value<string>("$dtId");
                Console.WriteLine($"  Found {twinId}");
            }
        }
        Console.WriteLine($"== End query results page {page}");
        conToken = qr.ContinuationToken;
    } while (conToken != null);
} catch (ErrorResponseException e)
{
    Console.WriteLine($"*** Error in twin query: ${e.Response.StatusCode}");
}
```

## <a name="next-steps"></a>下一步

逐步解說建立用戶端應用程式的步驟，讓您可以在其中使用 SDK：
* [*教學課程：撰寫用戶端應用程式的程式碼*](tutorial-code.md)
