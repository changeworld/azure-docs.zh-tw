---
title: 快速入門：在 JavaScript 中建立搜尋索引
titleSuffix: Azure Cognitive Search
description: 在此 JavaScript 快速入門中，了解如何使用 JavaScript 來建立索引、載入資料，以及在 Azure 認知搜尋上執行查詢
author: dereklegenzoff
manager: luisca
ms.author: delegenz
ms.devlang: javascript
ms.service: cognitive-search
ms.topic: quickstart
ms.date: 10/26/2020
ms.custom: devx-track-js
ms.openlocfilehash: 69cef4c17fd17219f0221c509775ad3fd6a80a27
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/02/2020
ms.locfileid: "96498977"
---
# <a name="quickstart-create-an-azure-cognitive-search-index-using-the-javascript-sdk"></a>快速入門：使用 JavaScript SDK 建立 Azure 認知搜尋索引
> [!div class="op_single_selector"]
> * [JavaScript](search-get-started-javascript.md)
> * [C#](search-get-started-dotnet.md)
> * [入口網站](search-get-started-portal.md)
> * [PowerShell](./search-get-started-powershell.md)
> * [Python](search-get-started-python.md)
> * [REST](search-get-started-rest.md)


使用[適用於 Azure 認知搜尋的 JAVAscript/Typscript SDK](/javascript/api/overview/azure/search-documents-readme) 在 JavaScript 中建立 Node.js 應用程式，以建立、載入和查詢搜尋索引。

本文示範如何逐步建立應用程式。 或者，您可以[下載原始程式碼和資料](https://github.com/Azure-Samples/azure-search-javascript-samples/tree/master/quickstart/v11)，並從命令列執行應用程式。

## <a name="prerequisites"></a>先決條件

開始之前，請先準備下列工具和服務：

+ 具有有效訂用帳戶的 Azure 帳戶。 [免費建立帳戶](https://azure.microsoft.com/free/)。

+ Azure 認知搜尋服務。 [建立服務](search-create-service-portal.md)或[尋找現有服務](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices)。 您可以使用本快速入門的免費服務。 

+ [Node.js](https://nodejs.org) 和 [NPM](https://www.npmjs.com)

+ [Visual Studio Code](https://code.visualstudio.com) 或其他 IDE


## <a name="set-up-your-project"></a>設定專案

一開始先取得搜尋服務的端點和金鑰。 然後使用 NPM 建立新的專案，如下所述。

<a name="get-service-info"></a>

### <a name="copy-a-key-and-endpoint"></a>複製金鑰和端點

在每個對服務發出呼叫的要求上，都需要 URL 端點和存取金鑰。 第一個步驟是尋找要新增至專案的 API 金鑰和 URL。 您在稍後的步驟中建立用戶端時，將會指定這兩個值。

1. [登入 Azure 入口網站](https://portal.azure.com/)，並在搜尋服務的 [概觀]  頁面上取得 URL。 範例端點看起來會像是 `https://mydemo.search.windows.net`。

2. 在 [設定] > [金鑰] 中，取得管理員金鑰以獲得服務的完整權限 (必須有完整權限才能建立或刪除物件)。 共有兩個可交換的主要和次要金鑰。 您可以使用任一個。

   ![取得 HTTP 端點和存取金鑰](media/search-get-started-rest/get-url-key.png "取得 HTTP 端點和存取金鑰")

所有要求均都需要在傳送至您服務上的每個要求上使用 API 金鑰。 擁有有效的金鑰就能為每個要求在傳送要求之應用程式與處理要求之服務間建立信任。

### <a name="create-a-new-npm-project"></a>建立新的 NPM 專案

從開啟 VS Code 及其[整合式終端機](https://code.visualstudio.com/docs/editor/integrated-terminal)或另一個終端機 (例如 Node.js 命令提示字元) 開始。

1. 建立開發目錄，並將其命名為 `quickstart`：

    ```cmd
    mkdir quickstart
    cd quickstart
    ```

2. 使用 NPM 將空的專案初始化，方法是執行 

    ```cmd
    npm init
    ```
     接受預設值，但 [授權] 除外，您應將其設定為 [MIT]。 

3. 安裝 `@azure/search-documents`，這是[適用於 Azure 認知搜尋的 JAVAscript/Typscript SDK](/javascript/api/overview/azure/search-documents-readme)。

    ```cmd
    npm install @azure/search-documents
    ```

4. 安裝用來匯入環境變數的 `dotenv`，例如我們的服務名稱和 API 金鑰。
    ```cmd
    npm install dotenv
    ```

5. 檢查您的 **package. json** 檔案是否如下 JSON 所示，確認您已設定好專案及其相依性：

    ```json
    {
      "name": "quickstart",
      "version": "1.0.0",
      "description": "Azure Cognitive Search Quickstart",
      "main": "index.js",
      "scripts": {
        "test": "echo \"Error: no test specified\" && exit 1"
      },
      "keywords": [
        "Azure",
        "Search"
      ],
      "author": "Your Name",
      "license": "MIT",
      "dependencies": {
        "@azure/search-documents": "^11.0.3",
        "dotenv": "^8.2.0"
      }
    }
    ```

6. 建立 **env** 的檔案，以保存您的搜尋服務參數：

    ```
    SEARCH_API_KEY=<search-admin-key>
    SEARCH_API_ENDPOINT=https://<search-service-name>.search.windows.net
    ```

使用您的搜尋服務名稱來取代 `<search-service-name>` 值。 使用您稍早記錄的金鑰值來取代 `<search-admin-key>`。 

### <a name="create-indexjs-file"></a>建立 index.js 檔案

接下來，我們會建立一個 **index.js** 檔案，這是裝載程式碼的主要檔案。

在此檔案頂端，匯入 `@azure/search-documents` 程式庫：

```javascript
const { SearchIndexClient, SearchClient, AzureKeyCredential, odata } = require("@azure/search-documents");
```

接下來，我們必須要求 `dotenv` 套件從 **.env** 檔案中讀取參數，如下所示：

```javascript
// Load the .env file if it exists
require("dotenv").config();

// Getting endpoint and apiKey from .env file
const endpoint = process.env.SEARCH_API_ENDPOINT || "";
const apiKey = process.env.SEARCH_API_KEY || "";
```

當匯入和環境變數就緒後，我們就可以定義 main 函式。

SDK 中的大部分功能都是非同步，因此我們會將主要函式 `async`。 我們也會在 main 函式下包含 `main().catch()` ，以攔截並記錄遇到的任何錯誤：

```javascript
async function main() {
    console.log(`Running Azure Cognitive Search Javascript quickstart...`);
    if (!endpoint || !apiKey) {
        console.log("Make sure to set valid values for endpoint and apiKey with proper authorization.");
        return;
    }

    // remaining quickstart code will go here
}

main().catch((err) => {
    console.error("The sample encountered an error:", err);
});
```

備妥之後，我們就可以建立索引。

## <a name="1---create-index"></a>1 - 建立索引 

建立 **hotels_quickstart_index.json** 檔案。  此檔案會定義 Azure 認知搜尋將如何處理您要在下一個步驟中載入的文件。 每個欄位都會由 `name` 識別，並具有指定的 `type`。 每個欄位也有一系列的索引屬性，可指定 Azure 認知搜尋是否可在欄位上進行搜尋、篩選、排序和 Facet 處理。 大部分的欄位都是簡單的資料類型，但有些 (像是 `AddressType`) 則為複雜類型，其可讓您在索引中建立豐富的資料結構。  您可以深入了解[支援的資料類型](/rest/api/searchservice/supported-data-types)和[索引屬性](./search-what-is-an-index.md#index-attributes)。 

將下列內容新增至 **hotels_quickstart_index.json**，或 [下載檔案](https://github.com/Azure-Samples/azure-search-javascript-samples/blob/master/quickstart/v11/hotels_quickstart_index.json)。 

```json
{
    "name": "hotels-quickstart",
    "fields": [
        {
            "name": "HotelId",
            "type": "Edm.String",
            "key": true,
            "filterable": true
        },
        {
            "name": "HotelName",
            "type": "Edm.String",
            "searchable": true,
            "filterable": false,
            "sortable": true,
            "facetable": false
        },
        {
            "name": "Description",
            "type": "Edm.String",
            "searchable": true,
            "filterable": false,
            "sortable": false,
            "facetable": false,
            "analyzer": "en.lucene"
        },
        {
            "name": "Description_fr",
            "type": "Edm.String",
            "searchable": true,
            "filterable": false,
            "sortable": false,
            "facetable": false,
            "analyzer": "fr.lucene"
        },
        {
            "name": "Category",
            "type": "Edm.String",
            "searchable": true,
            "filterable": true,
            "sortable": true,
            "facetable": true
        },
        {
            "name": "Tags",
            "type": "Collection(Edm.String)",
            "searchable": true,
            "filterable": true,
            "sortable": false,
            "facetable": true
        },
        {
            "name": "ParkingIncluded",
            "type": "Edm.Boolean",
            "filterable": true,
            "sortable": true,
            "facetable": true
        },
        {
            "name": "LastRenovationDate",
            "type": "Edm.DateTimeOffset",
            "filterable": true,
            "sortable": true,
            "facetable": true
        },
        {
            "name": "Rating",
            "type": "Edm.Double",
            "filterable": true,
            "sortable": true,
            "facetable": true
        },
        {
            "name": "Address",
            "type": "Edm.ComplexType",
            "fields": [
                {
                    "name": "StreetAddress",
                    "type": "Edm.String",
                    "filterable": false,
                    "sortable": false,
                    "facetable": false,
                    "searchable": true
                },
                {
                    "name": "City",
                    "type": "Edm.String",
                    "searchable": true,
                    "filterable": true,
                    "sortable": true,
                    "facetable": true
                },
                {
                    "name": "StateProvince",
                    "type": "Edm.String",
                    "searchable": true,
                    "filterable": true,
                    "sortable": true,
                    "facetable": true
                },
                {
                    "name": "PostalCode",
                    "type": "Edm.String",
                    "searchable": true,
                    "filterable": true,
                    "sortable": true,
                    "facetable": true
                },
                {
                    "name": "Country",
                    "type": "Edm.String",
                    "searchable": true,
                    "filterable": true,
                    "sortable": true,
                    "facetable": true
                }
            ]
        }
    ],
    "suggesters": [
        {
            "name": "sg",
            "searchMode": "analyzingInfixMatching",
            "sourceFields": [
                "HotelName"
            ]
        }
    ]
}
```

在索引定義備妥後，我們想要將 **hotels_quickstart_index.json** 匯入 **index.js** 的頂端，讓 main 函式可以存取索引定義。

```javascript
const indexDefinition = require('./hotels_quickstart_index.json');
```

在 main 函式中，我們接著建立一個 `SearchIndexClient`，用來建立和管理 Azure 認知搜尋的索引。 

```javascript
const indexClient = new SearchIndexClient(endpoint, new AzureKeyCredential(apiKey));
```

接下來，我們要刪除索引 (如果已經存在)。 這是測試/示範程式碼的常見做法。

我們會定義一個嘗試刪除索引的簡單函式來完成這項工作。

```javascript
async function deleteIndexIfExists(indexClient, indexName) {
    try {
        await indexClient.deleteIndex(indexName);
        console.log('Deleting index...');
    } catch {
        console.log('Index does not exist yet.');
    }
}
```

若要執行函式，我們會從索引定義中將索引名稱解壓縮，並將 `indexName` 連同 `indexClient` 傳遞至 `deleteIndexIfExists()` 函式。

```javascript
const indexName = indexDefinition["name"];

console.log('Checking if index exists...');
await deleteIndexIfExists(indexClient, indexName);
```

之後，我們就可以使用 `createIndex()` 方法建立索引。

```javascript
console.log('Creating index...');
let index = await indexClient.createIndex(indexDefinition);

console.log(`Index named ${index.name} has been created.`);
```

### <a name="run-the-sample"></a>執行範例

現在，您準備好要執行範例了。 使用終端機視窗，執行下列命令：

```cmd
node index.js
```

如果您[下載了原始程式碼](https://github.com/Azure-Samples/azure-search-javascript-samples/tree/master/quickstart/v11)而且尚未安裝必要的套件，請先執行 `npm install`。

您應會看到一系列的訊息，描述程式所採取的動作。 

在 Azure 入口網站中開啟搜尋服務的 **概觀**。 選取 [索引] 標籤。您應該會看到如下的內容：

:::image type="content" source="media/search-get-started-javascript/create-index-no-data.png" alt-text="Azure 入口網站、搜尋服務概觀、索引標籤的螢幕擷取畫面" border="false":::

在下一個步驟中，您會將資料新增至索引。 

## <a name="2---load-documents"></a>2 - 載入文件 


在 Azure 認知搜尋中，文件是同時屬於索引輸入與查詢輸出的資料結構。 您可以將這類資料推送至索引，或使用[索引子](search-indexer-overview.md)。 在此情況下，我們會以程式設計方式將文件推送至索引。

文件輸入可能是資料庫中的資料列、Blob 儲存體中的 Blob，或磁碟上的 JSON 文件。 您可以下載 [hotels. json](https://github.com/Azure-Samples/azure-search-javascript-samples/blob/master/quickstart/v11/hotels.json)，或使用下列內容建立自己的 **hotels.json** 檔案：

```json
{
    "value": [
        {
            "HotelId": "1",
            "HotelName": "Secret Point Motel",
            "Description": "The hotel is ideally located on the main commercial artery of the city in the heart of New York. A few minutes away is Time's Square and the historic centre of the city, as well as other places of interest that make New York one of America's most attractive and cosmopolitan cities.",
            "Description_fr": "L'hôtel est idéalement situé sur la principale artère commerciale de la ville en plein cœur de New York. A quelques minutes se trouve la place du temps et le centre historique de la ville, ainsi que d'autres lieux d'intérêt qui font de New York l'une des villes les plus attractives et cosmopolites de l'Amérique.",
            "Category": "Boutique",
            "Tags": ["pool", "air conditioning", "concierge"],
            "ParkingIncluded": false,
            "LastRenovationDate": "1970-01-18T00:00:00Z",
            "Rating": 3.6,
            "Address": {
                "StreetAddress": "677 5th Ave",
                "City": "New York",
                "StateProvince": "NY",
                "PostalCode": "10022"
            }
        },
        {
            "HotelId": "2",
            "HotelName": "Twin Dome Motel",
            "Description": "The hotel is situated in a  nineteenth century plaza, which has been expanded and renovated to the highest architectural standards to create a modern, functional and first-class hotel in which art and unique historical elements coexist with the most modern comforts.",
            "Description_fr": "L'hôtel est situé dans une place du XIXe siècle, qui a été agrandie et rénovée aux plus hautes normes architecturales pour créer un hôtel moderne, fonctionnel et de première classe dans lequel l'art et les éléments historiques uniques coexistent avec le confort le plus moderne.",
            "Category": "Boutique",
            "Tags": ["pool", "free wifi", "concierge"],
            "ParkingIncluded": "false",
            "LastRenovationDate": "1979-02-18T00:00:00Z",
            "Rating": 3.6,
            "Address": {
                "StreetAddress": "140 University Town Center Dr",
                "City": "Sarasota",
                "StateProvince": "FL",
                "PostalCode": "34243"
            }
        },
        {
            "HotelId": "3",
            "HotelName": "Triple Landscape Hotel",
            "Description": "The Hotel stands out for its gastronomic excellence under the management of William Dough, who advises on and oversees all of the Hotel’s restaurant services.",
            "Description_fr": "L'hôtel est situé dans une place du XIXe siècle, qui a été agrandie et rénovée aux plus hautes normes architecturales pour créer un hôtel moderne, fonctionnel et de première classe dans lequel l'art et les éléments historiques uniques coexistent avec le confort le plus moderne.",
            "Category": "Resort and Spa",
            "Tags": ["air conditioning", "bar", "continental breakfast"],
            "ParkingIncluded": "true",
            "LastRenovationDate": "2015-09-20T00:00:00Z",
            "Rating": 4.8,
            "Address": {
                "StreetAddress": "3393 Peachtree Rd",
                "City": "Atlanta",
                "StateProvince": "GA",
                "PostalCode": "30326"
            }
        },
        {
            "HotelId": "4",
            "HotelName": "Sublime Cliff Hotel",
            "Description": "Sublime Cliff Hotel is located in the heart of the historic center of Sublime in an extremely vibrant and lively area within short walking distance to the sites and landmarks of the city and is surrounded by the extraordinary beauty of churches, buildings, shops and monuments. Sublime Cliff is part of a lovingly restored 1800 palace.",
            "Description_fr": "Le sublime Cliff Hotel est situé au coeur du centre historique de sublime dans un quartier extrêmement animé et vivant, à courte distance de marche des sites et monuments de la ville et est entouré par l'extraordinaire beauté des églises, des bâtiments, des commerces et Monuments. Sublime Cliff fait partie d'un Palace 1800 restauré avec amour.",
            "Category": "Boutique",
            "Tags": ["concierge", "view", "24-hour front desk service"],
            "ParkingIncluded": true,
            "LastRenovationDate": "1960-02-06T00:00:00Z",
            "Rating": 4.6,
            "Address": {
                "StreetAddress": "7400 San Pedro Ave",
                "City": "San Antonio",
                "StateProvince": "TX",
                "PostalCode": "78216"
            }
        }
    ]
}
```

與處理 indexDefinition 的方法一樣，我們也需要將 `hotels.json` 匯入 **index .js** 頂端，以便在 main 函數中存取資料。

```javascript
const hotelData = require('./hotels.json');
```


若要在搜尋索引中編製資料索引，現在需要建立 `SearchClient`。 當 `SearchIndexClient` 用來建立和管理索引時，`SearchClient` 用來上傳文件和查詢索引。

建立 `SearchClient` 的方法有兩種。 第一個選項是從頭開始建立 `SearchClient`：

```javascript
 const searchClient = new SearchClient(endpoint, indexName, new AzureKeyCredential(apiKey));
```

或者，您可以使用 `SearchIndexClient` 的 `getSearchClient()` 方法來建立 `SearchClient`：

```javascript
const searchClient = indexClient.getSearchClient(indexName);
```

現在已定義用戶端，請將文件上傳至搜尋索引。 在此情況下，我們會使用 `mergeOrUploadDocuments()` 方法，如果已具有相同金鑰的文件，則會上傳文件，或與現有文件合併。

```javascript
console.log('Uploading documents...');
let indexDocumentsResult = await searchClient.mergeOrUploadDocuments(hotelData['value']);

console.log(`Index operations succeeded: ${JSON.stringify(indexDocumentsResult.results[0].succeeded)}`);
```

使用 `node index.js` 再次執行程式。 您應該會看到一組與步驟 1 所示訊息略有不同的訊息。 這次，索引「確實」存在，而且您應該會在應用程式建立新索引並對其發佈資料之前，看到有關刪除該索引的訊息。 

在下一個步驟中執行查詢之前，請先定義一個函式，讓程式等候一秒。 這只是為了測試/示範目的，以確保完成編製索引，並在查詢的索引中提供文件。

```javascript
function sleep(ms) {
    var d = new Date();
    var d2 = null;
    do {
        d2 = new Date();
    } while (d2 - d < ms);
}
```

若要讓程式等候一秒，請呼叫 `sleep` 函式，如下所示：

```javascript
sleep(1000);
```

## <a name="3---search-an-index"></a>3 - 搜尋索引

建立索引並上傳文件之後，您就可以開始將查詢傳送至索引。 在本節中，我們會將五個不同的查詢傳送至搜尋索引，以示範可供您使用的不同查詢功能內容。

這些查詢是以將在 main 函式中呼叫的 `sendQueries()` 函式撰寫，如下所示：

```javascript
await sendQueries(searchClient);
```

系統會使用 `searchClient` 的 `search()` 方法來傳送查詢。 第一個參數是搜尋文字，而第二個參數是任何額外的搜尋選項。

第一個查詢會搜尋 `*`，這相當於搜尋所有項目，並選取索引中的三個欄位。 最佳做法是只針對所需的欄位執行 `select`，因為提取不必要的資料可能會讓查詢延遲。

此查詢的 `searchOptions` 也將 `includeTotalCount` 設定為 `true`，這會傳回找到的相符結果數目。

```javascript
async function sendQueries(searchClient) {
    console.log('Query #1 - search everything:');
    let searchOptions = {
        includeTotalCount: true,
        select: ["HotelId", "HotelName", "Rating"]
    };

    let searchResults = await searchClient.search("*", searchOptions);
    for await (const result of searchResults.results) {
        console.log(`${JSON.stringify(result.document)}`);
    }
    console.log(`Result count: ${searchResults.count}`);

    // remaining queries go here
}
```

下面所述的其餘查詢也應新增至 `sendQueries()` 函式。 為了方便閱讀，此處會加以分隔。

在下一個查詢中，我們會指定搜尋詞彙 `"wifi"`，同時包含一個篩選準則，只會傳回狀態等於 `'FL'` 的結果。 結果也會依飯店的 `Rating` 排序。

```javascript
console.log('Query #2 - Search with filter, orderBy, and select:');
let state = 'FL';
searchOptions = {
    filter: odata`Address/StateProvince eq ${state}`,
    orderBy: ["Rating desc"],
    select: ["HotelId", "HotelName", "Rating"]
};

searchResults = await searchClient.search("wifi", searchOptions);
for await (const result of searchResults.results) {
    console.log(`${JSON.stringify(result.document)}`);
}
```

接下來，使用 `searchFields` 參數，將搜尋限制為單一可搜尋的欄位。 如果您只想要知道在特定欄位中的相符項目，這是很好的選項，會讓您的查詢更有效率。 

```javascript
console.log('Query #3 - Limit searchFields:');
searchOptions = {
    select: ["HotelId", "HotelName", "Rating"],
    searchFields: ["HotelName"]
};

searchResults = await searchClient.search("sublime cliff", searchOptions);
for await (const result of searchResults.results) {
    console.log(`${JSON.stringify(result.document)}`);
}
console.log();
```

另一個要包含在查詢中的常見選項是 `facets`。 Facet 可讓您在 UI 上建立篩選，讓使用者能夠輕鬆地知道可以篩選的值。

```javascript
console.log('Query #4 - Use facets:');
searchOptions = {
    facets: ["Category"],
    select: ["HotelId", "HotelName", "Rating"],
    searchFields: ["HotelName"]
};

searchResults = await searchClient.search("*", searchOptions);
for await (const result of searchResults.results) {
    console.log(`${JSON.stringify(result.document)}`);
}
```

最後一個查詢會使用 `searchClient` 的 `getDocument()` 方法。 這可讓您依其索引鍵有效率地擷取文件。 

```javascript
console.log('Query #5 - Lookup document:');
let documentResult = await searchClient.getDocument(key='3')
console.log(`HotelId: ${documentResult.HotelId}; HotelName: ${documentResult.HotelName}`)
```

### <a name="run-the-sample"></a>執行範例

以 `node index.js` 執行程式。 現在，除了先前的步驟之外，查詢也會送出，並將結果寫入主控台。

## <a name="clean-up-resources"></a>清除資源

使用您自己的訂用帳戶時，在專案結束後確認您是否還需要您建立的資源，是很好的做法。 讓資源繼續執行可能會產生費用。 您可以個別刪除資源，或刪除資源群組以刪除整組資源。

您可以使用左導覽窗格中的 [所有資源]  或 [資源群組]  連結，在入口網站中尋找和管理資源。

如果您使用免費服務，請記住您會有三個索引、索引子和資料來源的限制。 您可以在入口網站中刪除個別項目，以避免超出限制。 

## <a name="next-steps"></a>後續步驟

在此 JavaScript 快速入門中，您已執行一系列的工作來建立索引、連同文件加以載入，以及執行查詢。 

如果您已有一些 Azure 認知搜尋的背景知識，可以利用此範例作為試用建議工具 (預先輸入或自動完成查詢)、篩選及多面向導覽的跳板。 如果您不熟悉 Azure 認知搜尋，建議您嘗試藉由其他教學課程了解您可以建立的項目。 請瀏覽我們的 [文件頁面](https://azure.microsoft.com/documentation/services/search/) 以尋找更多資源。 

> [!div class="nextstepaction"]
> [建置 Azure 認知搜尋的回應前端](https://github.com/dereklegenzoff/azure-search-react-template)