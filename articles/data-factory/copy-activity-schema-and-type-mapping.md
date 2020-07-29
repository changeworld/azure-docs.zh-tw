---
title: 複製活動中的架構和資料類型對應
description: 深入瞭解 Azure Data Factory 中的複製活動如何將架構和資料類型從來源資料對應到接收資料。
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.reviewer: craigg
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 06/22/2020
ms.author: jingwang
ms.openlocfilehash: b48fb28a56cdc1c836233cd2bd03a1f9e750a0a7
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "85249647"
---
# <a name="schema-and-data-type-mapping-in-copy-activity"></a>複製活動中的架構和資料類型對應
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

本文說明 Azure Data Factory 複製活動如何從來源資料執行架構對應和資料類型對應到接收資料。

## <a name="schema-mapping"></a>結構描述對應

### <a name="default-mapping"></a>預設對應

根據預設，複製活動會以區分大小寫的方式，將來源資料對應至**依資料行名稱的**接收。 如果接收不存在（例如寫入檔案），則來源功能變數名稱會保存為接收名稱。 這類預設對應支援彈性架構，而從來源到接收的架構漂移會從執行到執行，而來源資料存放區所傳回的所有資料都可以複製到接收。

如果您的來源是不含標頭的文字檔，則需要[明確對應](#explicit-mapping)，因為來源不包含資料行名稱。

### <a name="explicit-mapping"></a>明確對應

您也可以指定明確對應，依據您的需求自訂從來源到接收的資料行/欄位對應。 使用明確對應時，您可以只將部分來源資料複製到接收，或將來源資料對應至具有不同名稱的接收器，或重新塑造表格式/階層式資料。 複製活動：

1. 從來源讀取資料，並判斷來源架構。
2. 套用您定義的對應。
3. 將資料寫入至接收。

深入了解：

- [表格式來源到表格式接收](#tabular-source-to-tabular-sink)
- [表格式接收的階層式來源](#hierarchical-source-to-tabular-sink)
- [階層式接收的表格式/階層式來源](#tabularhierarchical-source-to-hierarchical-sink)

您可以在 Data Factory 撰寫 UI-> 複製活動-> 對應] 索引標籤上設定對應，或以程式設計方式指定複製活動-> 屬性中的對應 `translator` 。 下列屬性在 `translator`  ->  `mappings` 陣列 > 物件中受到支援-> `source` 和 `sink` ，其指向要對應資料的特定資料行/欄位。

| 屬性 | 說明                                                  | 必要 |
| -------- | ------------------------------------------------------------ | -------- |
| NAME     | 來源或接收資料行/欄位的名稱。 適用于表格式來源和接收。 | Yes      |
| 序數  | 資料行索引。 從1開始。 <br>使用不含標頭行的分隔文字時，請套用和必要。 | No       |
| path     | 要解壓縮或對應之每個欄位的 JSON 路徑運算式。 適用于階層式來源和接收，例如 Cosmos DB、MongoDB 或 REST 連接器。<br>若為根物件下的欄位，JSON 路徑的開頭為根 `$` ; 針對屬性所選擇之陣列內的欄位 `collectionReference` ，json 路徑會從沒有的陣列元素開始 `$` 。 | No       |
| type     | 來源或接收資料行的 Data Factory 過渡資料類型。 一般來說，您不需要指定或變更此屬性。 深入瞭解[資料類型對應](#data-type-mapping)。 | 否       |
| culture  | 來源或接收資料行的文化特性。 當類型為或時套用 `Datetime` `Datetimeoffset` 。 預設值為 `en-us`。<br>一般來說，您不需要指定或變更此屬性。 深入瞭解[資料類型對應](#data-type-mapping)。 | No       |
| format   | 當類型為或時，所要使用的格式字串 `Datetime` `Datetimeoffset` 。 有關如何格式化日期時間的資訊，請參閱[自訂日期和時間格式字串](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings)。 一般來說，您不需要指定或變更此屬性。 深入瞭解[資料類型對應](#data-type-mapping)。 | No       |

除了之外，還支援下列屬性 `translator` `mappings` ：

| 屬性            | 說明                                                  | 必要 |
| ------------------- | ------------------------------------------------------------ | -------- |
| collectionReference | 從階層式來源（例如，Cosmos DB、MongoDB 或 REST 連接器）複製資料時適用。<br>如果您想要逐一查看**陣列欄位內**相同模式的物件並擷取資料，然後轉換為每個物件一個資料列，則請指定該陣列的 JSON 路徑，以執行交叉套用。 | No       |

#### <a name="tabular-source-to-tabular-sink"></a>表格式來源到表格式接收

例如，若要將資料從 Salesforce 複製到 Azure SQL Database 並明確對應三個數據行：

1. 在 [複製活動-> 對應] 索引標籤上，按一下 [匯**入架構**] 按鈕以匯入來源和接收架構。

2. 對應所需的欄位，並排除/刪除其餘部分。

![將表格式對應至表格式](media/copy-activity-schema-and-type-mapping/map-tabular-to-tabular.png)

您可以在複製活動裝載中設定相同的對應，如下所示（請參閱 `translator` ）：

```json
{
    "name": "CopyActivityTabularToTabular",
    "type": "Copy",
    "typeProperties": {
        "source": { "type": "SalesforceSource" },
        "sink": { "type": "SqlSink" },
        "translator": {
            "type": "TabularTranslator",
            "mappings": [
                {
                    "source": { "name": "Id" },
                    "sink": { "name": "CustomerID" }
                },
                {
                    "source": { "name": "Name" },
                    "sink": { "name": "LastName" }
                },
                {
                    "source": { "name": "LastModifiedDate" },
                    "sink": { "name": "ModifiedDate" }
                }
            ]
        }
    },
    ...
}
```

若要從不含標頭行的帶分隔符號的文本檔案複製資料，則會以序數而不是名稱來表示欄。 

```json
{
    "name": "CopyActivityTabularToTabular",
    "type": "Copy",
    "typeProperties": {
        "source": { "type": "DelimitedTextSource" },
        "sink": { "type": "SqlSink" },
        "translator": {
            "type": "TabularTranslator",
            "mappings": [
                {
                    "source": { "ordinal": "1" },
                    "sink": { "name": "CustomerID" }
                }, 
                {
                    "source": { "ordinal": "2" },
                    "sink": { "name": "LastName" }
                }, 
                {
                    "source": { "ordinal": "3" },
                    "sink": { "name": "ModifiedDate" }
                }
            ]
        }
    },
    ...
}
```

#### <a name="hierarchical-source-to-tabular-sink"></a>表格式接收的階層式來源

將資料從階層式來源複製到表格式接收時，複製活動支援下列功能：

- 從物件和陣列中解壓縮資料。
- Cross 會將多個具有相同模式的物件套用至陣列，在此情況下，會將一個 JSON 物件轉換成表格式結果中的多個記錄。

如需更先進的階層對表格式轉換，您可以使用[資料流程](concepts-data-flow-overview.md)。 

例如，如果您的來源 MongoDB 檔包含下列內容：

```json
{
    "id": {
        "$oid": "592e07800000000000000000"
    },
    "number": "01",
    "date": "20170122",
    "orders": [
        {
            "prod": "p1",
            "price": 23
        },
        {
            "prod": "p2",
            "price": 13
        },
        {
            "prod": "p3",
            "price": 231
        }
    ],
    "city": [ { "name": "Seattle" } ]
}
```

而且您想要以下列格式，將它複製到具有標頭行的文字檔中，方法是將陣列內的資料簡維 *（order_pd 和 order_price）* ，並使用一般根資訊 *（number、date 和 city）* 進行交叉聯結：

| orderNumber | orderDate | order_pd | order_price | city    |
| ----------- | --------- | -------- | ----------- | ------- |
| 01          | 20170122  | P1       | 23          | Seattle |
| 01          | 20170122  | P2       | 13          | Seattle |
| 01          | 20170122  | P3       | 231         | Seattle |

您可以在 Data Factory 撰寫 UI 上定義這類對應：

1. 在 [複製活動-> 對應] 索引標籤上，按一下 [匯**入架構**] 按鈕以匯入來源和接收架構。 如同 Data Factory 在匯入架構時，會取樣前幾個物件，如果沒有顯示任何欄位，您可以將它加入至階層中的正確圖層-將游標停留在現有的功能變數名稱上，然後加入宣告節點、物件或陣列。

2. 選取您要從中逐一查看和解壓縮資料的陣列。 它會自動填入為**集合參考**。 請注意，這類作業只支援單一陣列。

3. 將所需的欄位對應至接收。 Data Factory 會自動判斷階層式端的對應 JSON 路徑。

![使用 UI 將階層對應至表格式](media/copy-activity-schema-and-type-mapping/map-hierarchical-to-tabular-ui.png)

您也可以切換到 [ **Advanced editor**]，在此情況下，您可以直接查看和編輯欄位的 JSON 路徑。 如果您選擇在此視圖中加入新的對應，請指定 JSON 路徑。

![使用 advanced editor 將階層式對應至表格式](media/copy-activity-schema-and-type-mapping/map-hierarchical-to-tabular-advanced-editor.png)

您可以在複製活動裝載中設定相同的對應，如下所示（請參閱 `translator` ）：


```json
{
    "name": "CopyActivityHierarchicalToTabular",
    "type": "Copy",
    "typeProperties": {
        "source": { "type": "MongoDbV2Source" },
        "sink": { "type": "DelimitedTextSink" },
        "translator": {
            "type": "TabularTranslator",
            "mappings": [
                {
                    "source": { "path": "$['number']" },
                    "sink": { "name": "orderNumber" }
                },
                {
                    "source": { "path": "$['date']" },
                    "sink": { "name": "orderDate" }
                },
                {
                    "source": { "path": "['prod']" },
                    "sink": { "name": "order_pd" }
                },
                {
                    "source": { "path": "['price']" },
                    "sink": { "name": "order_price" }
                },
                {
                    "source": { "path": "$['city'][0]['name']" },
                    "sink": { "name": "city" }
                }
            ],
            "collectionReference": "$['orders']"
        }
    },
    ...
}
```

#### <a name="tabularhierarchical-source-to-hierarchical-sink"></a>階層式接收的表格式/階層式來源

使用者體驗流程類似于[表格式接收的階層式來源](#hierarchical-source-to-tabular-sink)。 

將資料從表格式來源複製到階層式接收時，不支援寫入至物件內的陣列。

將資料從階層式來源複製到階層式接收時，您可以在不觸及內部欄位的情況下，選取物件/陣列並對應至接收器，以額外保留整個層級的階層。

如需更先進的資料重新調整轉換，您可以使用[資料流程](concepts-data-flow-overview.md)。 

### <a name="parameterize-mapping"></a>參數化對應

如果您想要建立範本化管線，以動態方式複製大量物件，請判斷您是否可以利用[預設對應](#default-mapping)，或是您需要為個別的物件定義[明確對應](#explicit-mapping)。

如果需要明確對應，您可以：

1. 在管線層級定義具有物件類型的參數，例如 `mapping` 。

2. 參數化對應：在 [複製活動-> 對應] 索引標籤上，選擇 [新增動態內容]，然後選取上述參數。 活動裝載如下所示：

    ```json
    {
        "name": "CopyActivityHierarchicalToTabular",
        "type": "Copy",
        "typeProperties": {
            "source": {...},
            "sink": {...},
            "translator": {
                "value": "@pipeline().parameters.mapping",
                "type": "Expression"
            },
            ...
        }
    }
    ```

3. 建立要傳入對應參數的值。 它應該是整個定義的物件 `translator` ，請參閱[明確對應](#explicit-mapping)一節中的範例。 例如，如果是表格式來源到表格式接收復制，此值應該是 `{"type":"TabularTranslator","mappings":[{"source":{"name":"Id"},"sink":{"name":"CustomerID"}},{"source":{"name":"Name"},"sink":{"name":"LastName"}},{"source":{"name":"LastModifiedDate"},"sink":{"name":"ModifiedDate"}}]}` 。

## <a name="data-type-mapping"></a>資料類型對應

複製活動會執行來源類型與下列流程的接收類型對應： 

1. 從來源原生資料類型轉換成 Azure Data Factory 過渡資料類型。
2. 視需要自動轉換過渡資料類型，以符合對應的接收類型，適用于[預設](#default-mapping)的對應和[明確](#explicit-mapping)對應。
3. 從 Azure Data Factory 過渡資料類型轉換為接收原生資料類型。

複製活動目前支援下列過渡資料類型： Boolean、Byte、Byte array、Datetime、DatetimeOffset、Decimal、Double、GUID、Int16、Int32、Int64、SByte、Single、String、Timespan、UInt16、UInt32 和 UInt64。

從來源到接收的過渡類型之間，支援下列資料類型轉換。

| Source\Sink | Boolean | 位元組陣列 | Decimal | 日期/時間<small>（1）</small> | 浮點數<small>（2）</small> | GUID | 整數<small>（3）</small> | String | TimeSpan |
| ----------- | ------- | ---------- | ------- | ---------------------------- | ------------------------------ | ---- | -------------------------- | ------ | -------- |
| Boolean     | ✓       |            | ✓       |                              | ✓                              |      | ✓                          | ✓      |          |
| 位元組陣列  |         | ✓          |         |                              |                                |      |                            | ✓      |          |
| 日期/時間   |         |            |         | ✓                            |                                |      |                            | ✓      |          |
| Decimal     | ✓       |            | ✓       |                              | ✓                              |      | ✓                          | ✓      |          |
| 浮點 | ✓       |            | ✓       |                              | ✓                              |      | ✓                          | ✓      |          |
| GUID        |         |            |         |                              |                                | ✓    |                            | ✓      |          |
| 整數     | ✓       |            | ✓       |                              | ✓                              |      | ✓                          | ✓      |          |
| String      | ✓       | ✓          | ✓       | ✓                            | ✓                              | ✓    | ✓                          | ✓      | ✓        |
| TimeSpan    |         |            |         |                              |                                |      |                            | ✓      | ✓        |

（1）日期/時間包含 DateTime 和 DateTimeOffset。

（2）浮點數包含 Single 和 Double。

（3）整數包括 SByte、Byte、Int16、UInt16、Int32、UInt32、Int64 和 UInt64。

> [!NOTE]
> - 在表格式資料之間進行複製時，目前支援這類資料類型轉換。 不支援階層式來源/接收，這表示來源與接收過渡類型之間沒有系統定義的資料類型轉換。
> - 這項功能適用于最新的資料集模型。 如果您在 UI 中沒有看到此選項，請嘗試建立新的資料集。

以下是資料類型轉換的複製活動支援的屬性（在 `translator` programmatical 撰寫的區段下）：

| 屬性                         | 說明                                                  | 必要 |
| -------------------------------- | ------------------------------------------------------------ | -------- |
| typeConversion                   | 啟用新的資料類型轉換體驗。 <br>由於回溯相容性，預設值為 false。<br><br>針對透過在2020年6月之後從 Data Factory 撰寫 UI 建立的新複製活動，預設會啟用此資料類型轉換，以獲得最佳體驗，而且您可以在適用案例的 [複製活動-> 對應] 索引標籤上看到下列類型轉換設定。 <br>若要以程式設計方式建立管線，您需要將屬性明確設定 `typeConversion` 為 true 以啟用它。<br>針對在釋放此功能之前建立的現有複製活動，您不會看到 Data Factory 撰寫 UI 上的類型轉換選項，以提供回溯相容性。 | No       |
| typeConversionSettings           | 類型轉換設定的群組。 當 `typeConversion` 設定為時套用 `true` 。 下列屬性全都在此群組底下。 | No       |
| *下`typeConversionSettings`* |                                                              |          |
| allowDataTruncation              | 在複製期間將來源資料轉換成具有不同類型的接收時，允許資料截斷，例如從十進位到整數（從 DatetimeOffset 到 Datetime）。 <br>預設值為 true。 | No       |
| treatBooleanAsNumber             | 將布林值視為數位，例如 true 為1。<br>預設值為 false。 | No       |
| dateTimeFormat                   | 在沒有時區位移和字串的日期之間進行轉換時的格式字串，例如 `yyyy-MM-dd HH:mm:ss.fff` 。  如需詳細資訊，請參閱[自訂日期和時間格式字串](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings)。 | No       |
| dateTimeOffsetFormat             | 使用時區位移和字串在日期之間進行轉換時的格式字串，例如 `yyyy-MM-dd HH:mm:ss.fff zzz` 。  如需詳細資訊，請參閱[自訂日期和時間格式字串](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings)。 | No       |
| timeSpanFormat                   | 在時間週期和字串之間進行轉換時的格式字串，例如 `dd\.hh\:mm` 。 如需詳細資訊，請參閱[自訂 TimeSpan 格式字串](https://docs.microsoft.com/dotnet/standard/base-types/custom-timespan-format-strings)。 | 否       |
| culture                          | 轉換類型時要使用的文化特性資訊，例如 `en-us` 或 `fr-fr` 。 | No       |

**範例︰**

```json
{
    "name": "CopyActivity",
    "type": "Copy",
    "typeProperties": {
        "source": {
            "type": "ParquetSource"
        },
        "sink": {
            "type": "SqlSink"
        },
        "translator": {
            "type": "TabularTranslator",
            "typeConversion": true,
            "typeConversionSettings": {
                "allowDataTruncation": true,
                "treatBooleanAsNumber": true,
                "dateTimeFormat": "yyyy-MM-dd HH:mm:ss.fff",
                "dateTimeOffsetFormat": "yyyy-MM-dd HH:mm:ss.fff zzz",
                "timeSpanFormat": "dd\.hh\:mm",
                "culture": "en-gb"
            }
        }
    },
    ...
}
```

## <a name="legacy-models"></a>舊版模型

> [!NOTE]
> 下列用於將來源資料行/欄位對應至接收的模型，仍受到支援，因為這是為了回溯相容性。 我們建議您使用[架構對應](#schema-mapping)中所述的新模型。 Data Factory 撰寫 UI 已切換為產生新的模型。

### <a name="alternative-column-mapping-legacy-model"></a>替代資料行對應（舊版模型）

您可以指定 [複製活動->] `translator`  ->  `columnMappings` ，以在表格式資料之間進行對應。 在此情況下，輸入和輸出資料集都必須要有 "structure" 區段。 資料行對應支援將來源資料集「結構」中的所有或一部分資料行，對應至接收資料集「結構」中的所有資料行****。 以下是會導致發生例外狀況的錯誤狀況：

- 來源資料存放區查詢結果在輸入資料集的「結構」區段中並未指定資料行名稱。
- 接收資料存放區 (如果含有預先定義的結構描述) 在輸出資料集的「結構」區段中並未指定資料行名稱。
- 接收資料集「結構」中的資料行數量多於或少於對應中所指定的數量。
- 重複的對應。

在下列範例中，輸入資料集具有結構，且指向內部部署 Oracle 資料庫中的資料表。

```json
{
    "name": "OracleDataset",
    "properties": {
        "structure":
         [
            { "name": "UserId"},
            { "name": "Name"},
            { "name": "Group"}
         ],
        "type": "OracleTable",
        "linkedServiceName": {
            "referenceName": "OracleLinkedService",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "tableName": "SourceTable"
        }
    }
}
```

在此範例中，輸出資料集具有結構，並指向 Salesfoce 中的資料表。

```json
{
    "name": "SalesforceDataset",
    "properties": {
        "structure":
        [
            { "name": "MyUserId"},
            { "name": "MyName" },
            { "name": "MyGroup"}
        ],
        "type": "SalesforceObject",
        "linkedServiceName": {
            "referenceName": "SalesforceLinkedService",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "tableName": "SinkTable"
        }
    }
}
```

下列 JSON 定義了管線中的複製活動。 來源中的資料行會使用**translator**  ->  **columnMappings**屬性，對應至接收器中的資料行。

```json
{
    "name": "CopyActivity",
    "type": "Copy",
    "inputs": [
        {
            "referenceName": "OracleDataset",
            "type": "DatasetReference"
        }
    ],
    "outputs": [
        {
            "referenceName": "SalesforceDataset",
            "type": "DatasetReference"
        }
    ],
    "typeProperties":    {
        "source": { "type": "OracleSource" },
        "sink": { "type": "SalesforceSink" },
        "translator":
        {
            "type": "TabularTranslator",
            "columnMappings":
            {
                "UserId": "MyUserId",
                "Group": "MyGroup",
                "Name": "MyName"
            }
        }
    }
}
```

如果您正在使用 `"columnMappings": "UserId: MyUserId, Group: MyGroup, Name: MyName"` 的語法指定資料行對應，則仍會依原狀支援該對應。

### <a name="alternative-schema-mapping-legacy-model"></a>替代架構對應（舊版模型）

您可以指定複製活動-> 在 `translator`  ->  `schemaMapping` 階層式資料和表格式資料之間進行對應，例如，從 MongoDB/REST 複製到文字檔，然後從 Oracle 複製到 Azure Cosmos DB 適用于 MongoDB 的 API。 複製活動的 `translator` 區段支援下列屬性：

| 屬性            | 說明                                                  | 必要 |
| :------------------ | :----------------------------------------------------------- | :------- |
| type                | 複製活動 translator 的 type 屬性必須設定為： **TabularTranslator** | Yes      |
| schemaMapping       | 索引鍵/值組的集合，表示**從來源端到接收端**的對應關聯性。<br/>- 索引**鍵：** 代表來源。 針對 [**表格式來源**]，指定資料集結構中所定義的資料行名稱;針對 [**階層式來源**]，為每個要解壓縮和對應的欄位指定 JSON 路徑運算式。<br>- **值：** 表示接收。 若為**表格式接收**，請指定資料集結構中所定義的資料行名稱;針對**階層式接收**，為每個要解壓縮和對應的欄位指定 JSON 路徑運算式。 <br>在階層式資料的情況下，針對根物件下的欄位，JSON 路徑會以根 $ 開頭;對於屬性所選陣列內的欄位 `collectionReference` ，JSON 路徑會從陣列元素開始。 | Yes      |
| collectionReference | 如果您想要逐一查看**陣列欄位內**相同模式的物件並擷取資料，然後轉換為每個物件一個資料列，則請指定該陣列的 JSON 路徑，以執行交叉套用。 只有在階層式資料是來源時，才支援這個屬性。 | No       |

**範例：從 MongoDB 複製到 Oracle：**

例如，如果您有具備下列內容的 MongoDB 文件：

```json
{
    "id": {
        "$oid": "592e07800000000000000000"
    },
    "number": "01",
    "date": "20170122",
    "orders": [
        {
            "prod": "p1",
            "price": 23
        },
        {
            "prod": "p2",
            "price": 13
        },
        {
            "prod": "p3",
            "price": 231
        }
    ],
    "city": [ { "name": "Seattle" } ]
}
```

您想要壓平合併陣列內的資料 (order_pd 和 order_price)**，將內容複製到下列格式的 Azure SQL 資料表，並與一般根資訊 (編號、日期和城市)** 交叉聯結︰

| orderNumber | orderDate | order_pd | order_price | city    |
| ----------- | --------- | -------- | ----------- | ------- |
| 01          | 20170122  | P1       | 23          | Seattle |
| 01          | 20170122  | P2       | 13          | Seattle |
| 01          | 20170122  | P3       | 231         | Seattle |

如下列複製活動 JSON 範例所示，設定結構描述對應規則：

```json
{
    "name": "CopyFromMongoDBToOracle",
    "type": "Copy",
    "typeProperties": {
        "source": {
            "type": "MongoDbV2Source"
        },
        "sink": {
            "type": "OracleSink"
        },
        "translator": {
            "type": "TabularTranslator",
            "schemaMapping": {
                "$.number": "orderNumber",
                "$.date": "orderDate",
                "prod": "order_pd",
                "price": "order_price",
                "$.city[0].name": "city"
            },
            "collectionReference":  "$.orders"
        }
    }
}
```

## <a name="next-steps"></a>後續步驟
請參閱其他複製活動文章：

- [複製活動概觀](copy-activity-overview.md)
