---
title: 使用 AutoRest 建立適用于 Azure 數位 Twins 的自訂 Sdk
titleSuffix: Azure Digital Twins
description: '請參閱如何產生自訂 Sdk，以使用 c # 以外的其他語言的 Azure 數位 Twins。'
author: baanders
ms.author: baanders
ms.date: 4/24/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 895e33a111fe5bb881d198ee4995b9534ca3d528
ms.sourcegitcommit: e995f770a0182a93c4e664e60c025e5ba66d6a45
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/08/2020
ms.locfileid: "86135871"
---
# <a name="create-custom-sdks-for-azure-digital-twins-using-autorest"></a>使用 AutoRest 建立適用于 Azure 數位 Twins 的自訂 Sdk

目前，用來與 Azure 數位 Twins Api 互動的唯一已發佈資料平面 SDK 適用于 .NET （c #）。 如需瞭解 .NET SDK 和 Api 的一般資訊，請參閱[如何：使用 Azure 數位 Twins api 和 sdk](how-to-use-apis-sdks.md)。 如果您使用另一種語言，本文將說明如何使用 AutoRest，以您選擇的語言產生您自己的 SDK。

## <a name="set-up-your-machine"></a>設定您的電腦

若要產生 SDK，您將需要：
* [AutoRest](https://github.com/Azure/autorest)，版本2.0.4413 （目前不支援版本3）
* [Node.js](https://nodejs.org)為 AutoRest 的必要條件
* *digitaltwins.js*的[Azure 數位 Twins Swagger （OpenAPI）](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/digitaltwins/resource-manager/Microsoft.DigitalTwins/preview/2020-03-01-preview)檔案和其隨附的範例資料夾。 將 Swagger 檔案及其範例的資料夾下載到您的本機電腦。

當您的機器配備上述清單中的所有專案之後，您就可以開始使用 AutoRest 來建立 SDK。

## <a name="create-the-sdk-with-autorest"></a>使用 AutoRest 建立 SDK 

如果您已安裝 Node.js，您可以執行此命令，以確定您已安裝正確的 AutoRest 版本：
```cmd/sh
npm install -g autorest@2.0.4413
```

若要對 Azure 數位 Twins Swagger 檔案執行 AutoRest，請遵循下列步驟：
1. 將 Azure 數位 Twins Swagger 檔案和其隨附的範例資料夾複製到工作目錄中。
2. 使用 [命令提示字元] 視窗切換至該工作目錄。
3. 使用下列命令來執行 AutoRest。 `<language>`以您選擇的語言取代預留位置： `--python` 、 `--java` 、等 `--go` （您可以在[AutoRest 讀我檔案](https://github.com/Azure/autorest)中找到完整的選項清單）。

```cmd/sh
autorest --input-file=adtApiSwagger.json --<language> --output-folder=ADTApi --add-credentials --azure-arm --namespace=ADTApi
```

因此，您會在工作目錄中看到名為*ADTApi*的新資料夾。 產生的 SDK 檔案將具有命名空間*ADTApi*，而您將繼續在本文中的其餘使用範例中使用。

AutoRest 支援各種語言的程式碼產生器。

## <a name="add-the-sdk-to-a-visual-studio-project"></a>將 SDK 新增至 Visual Studio 專案

您可以將 AutoRest 所產生的檔案直接包含在 .NET 方案中。 不過，因為您可能需要在數個不同的專案（用戶端應用程式、Azure Functions 應用程式等）中使用 Azure 數位 Twins SDK，所以從產生的檔案建立個別的專案（.NET 類別庫）可能會很有用。 接著，您可以將此類別庫專案納入數個方案中，做為專案參考。

本節提供如何建立 SDK 做為類別庫的指示，這是它自己的專案，而且可以包含在其他專案中。 這些步驟依賴**Visual Studio** （您可以從[這裡](https://visualstudio.microsoft.com/downloads/)安裝最新版本）。

以下是步驟：

1. 為類別庫建立新的 Visual Studio 解決方案
2. 使用*ADTApi*做為專案名稱
3. 在 [方案瀏覽器] 中，以滑鼠右鍵選取所產生方案的*ADTApi*專案，然後選擇 [*加入 > 現有專案*]。
4. 尋找您在其中產生 SDK 的資料夾，然後選取根層級的檔案。
5. 按 [確定]
6. 將資料夾新增至專案（以滑鼠右鍵選取方案總管中的專案，然後選擇 [*加入 > 新增資料夾*]）
7. 將資料夾命名為*模型*
8. 以滑鼠右鍵選取方案瀏覽器中的 [*模型*] 資料夾，然後選取 [*新增 > 現有專案*]。
9. 選取所產生 SDK 的 [*模型*] 資料夾中的檔案，然後按 [確定]

若要成功建立 SDK，您的專案將需要這些參考：
* `Microsoft.Rest.ClientRuntime`
* `Microsoft.Rest.ClientRuntime.Azure`

若要新增這些，請開啟 [*工具] > NuGet 套件管理員 > 管理解決方案的 Nuget 套件*...]。

1. 在面板中，請確定已選取 [*流覽*] 索引標籤
2. 搜尋*Microsoft. Rest*
3. 選取*ClientRuntime*和*ClientRuntime*套件，並將其新增至您的方案

您現在可以建立專案，並在您撰寫的任何 Azure 數位 Twins 應用程式中將其納入做為專案參考。

## <a name="general-guidelines-for-generated-sdks"></a>所產生 Sdk 的一般指導方針

本節包含有關使用所產生 SDK 的一般資訊和指導方針。

### <a name="synchronous-and-asynchronous-calls"></a>同步和非同步呼叫

所有 SDK 函數都是以同步和非同步版本提供。

### <a name="typed-and-untyped-data"></a>具類型和不具類型的資料

REST API 呼叫通常會傳回強型別物件。 不過，因為 Azure 數位 Twins 可讓使用者定義自己的 Twins 自訂類型，所以沒有任何方法可以預先定義許多 Azure 數位 Twins 呼叫的靜態傳回資料。 相反地，Api 會在適用的情況下傳回強型別包裝函式，而自訂類型對應項資料則會在 Json.NET 物件中（使用於 API 簽章中的資料類型「物件」出現的地方）。 您可以在程式碼中適當地轉換這些物件。

### <a name="error-handling"></a>錯誤處理

每當 SDK 中發生錯誤時（包括 HTTP 錯誤，例如404），SDK 就會擲回例外狀況。 基於這個理由，請務必封裝 try/catch 區塊內的所有 API 呼叫。

以下的程式碼片段會嘗試新增對應項，並攔截此進程中的任何錯誤：

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
* 一個用於所有 Api，但查詢 API 除外
* 一個用於查詢 API

在非查詢分頁模式中，每個呼叫都有兩個版本：
* 進行初始呼叫的版本（例如 `DigitalTwins.ListEdges()` ）
* 取得後續頁面的版本，後置字元為 "Next" （例如 `DigitalTwins.ListEdgesNext()` ）

以下程式碼片段顯示如何從 Azure 數位 Twins 抓取已分頁的傳出關聯性清單：
```csharp
try
{
    // List to hold the results in
    List<object> relList = new List<object>();
    // Enumerate the IPage object returned to get the results
    // ListAsync will throw if an error occurs
    IPage<object> relPage = await client.DigitalTwins.ListEdgesAsync(id);
    relList.AddRange(relPage);
    // If there are more pages, the NextPageLink in the page is set
    while (relPage.NextPageLink != null)
    {
        // Get more pages...
        relPage = await client.DigitalTwins.ListEdgesNextAsync(relPage.NextPageLink);
        relList.AddRange(relPage);
    }
    Console.WriteLine($"Found {relList.Count} relationships on {id}");
    // Do something with each object found
    // As relationships are custom types, they are JSON.Net types
    foreach (JObject r in relList)
    {
        string relId = r.Value<string>("$edgeId");
        string relName = r.Value<string>("$relationship");
        Console.WriteLine($"Found relationship {relId} from {id}");
    }
}
catch (ErrorResponseException e)
{
    Console.WriteLine($"*** Error retrieving relationships on {id}: {e.Response.StatusCode}");
}
```

第二種模式只會針對查詢 API 產生。 它會 `continuationToken` 明確使用。

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

## <a name="next-steps"></a>後續步驟

逐步執行建立用戶端應用程式的步驟，您可以在其中使用您的 SDK：
* [教學課程：撰寫用戶端應用程式的程式碼](tutorial-code.md)