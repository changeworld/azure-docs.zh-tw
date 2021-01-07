---
title: 複製活動中的架構和資料類型對應
description: 瞭解 Azure Data Factory 中的「複製活動」如何將來源資料中的架構和資料類型對應到接收資料。
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
ms.openlocfilehash: ce7c0cba4a231fbdb33679f8cdac7d57c79845f5
ms.sourcegitcommit: 9514d24118135b6f753d8fc312f4b702a2957780
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/07/2021
ms.locfileid: "97968869"
---
# <a name="schema-and-data-type-mapping-in-copy-activity"></a>複製活動中的架構和資料類型對應
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

本文描述 Azure Data Factory 複製活動如何執行架構對應以及從來源資料到接收資料的資料類型對應。

## <a name="schema-mapping"></a>結構描述對應

### <a name="default-mapping"></a>預設對應

依預設，複製活動會以區分大小寫的方式，將來源資料對應至接收（ **依資料行名稱** ）。 如果接收不存在（例如，寫入至檔案 (s) ，來源功能變數名稱將會保存為接收名稱。 這類預設對應支援從來源到接收的彈性架構和架構漂移，使其無法執行-來源資料存放區所傳回的所有資料都可以複製到接收。

如果您的來源是沒有標頭行的文字檔，則需要 [明確的對應](#explicit-mapping) ，因為來源不包含資料行名稱。

### <a name="explicit-mapping"></a>明確對應

您也可以指定明確對應，依據您的需求自訂從來源到接收的資料行/欄位對應。 有了明確的對應，您就只能將部分來源資料複製到接收，或是將來源資料對應到具有不同名稱的接收，或調整表格式/階層式資料。 複製活動：

1. 從來源讀取資料，並判斷來源架構。
2. 套用您定義的對應。
3. 將資料寫入至接收。

深入了解：

- [表格式接收器的表格式來源](#tabular-source-to-tabular-sink)
- [表格式接收器的階層式來源](#hierarchical-source-to-tabular-sink)
- [階層式接收的表格式/階層式來源](#tabularhierarchical-source-to-hierarchical-sink)

您可以在 Data Factory 撰寫 UI > 複製活動-> 對應] 索引標籤上設定對應，或以程式設計方式指定複製活動-> 屬性中的對應 `translator` 。 陣列 > 的物件支援下列屬性 `translator`  ->  `mappings` -> `source` 和 `sink` ，這些屬性指向特定的資料行/欄位以對應資料。

| 屬性 | 描述                                                  | 必要 |
| -------- | ------------------------------------------------------------ | -------- |
| NAME     | 來源或接收資料行/欄位的名稱。 適用于表格式來源和接收。 | 是      |
| 序數  | 資料行索引。 從1開始。 <br>使用不含標頭行的分隔文字時，請套用和要求。 | 否       |
| path     | 要解壓縮或對應的每個欄位的 JSON 路徑運算式。 適用于階層式來源和接收，例如 Cosmos DB、MongoDB 或 REST 連接器。<br>針對根物件底下的欄位，JSON 路徑的開頭為根 `$` ; 針對屬性所選擇的陣列中的欄位 `collectionReference` ，json 路徑會從陣列元素開始，而不需要 `$` 。 | 否       |
| type     | 來源或接收資料行 Data Factory 過渡期資料類型。 一般而言，您不需要指定或變更這個屬性。 深入瞭解 [資料類型對應](#data-type-mapping)。 | 否       |
| culture  | 來源或接收資料行的文化特性。 當類型為或時套用 `Datetime` `Datetimeoffset` 。 預設為 `en-us`。<br>一般而言，您不需要指定或變更這個屬性。 深入瞭解 [資料類型對應](#data-type-mapping)。 | 否       |
| format   | 當類型為或時，要使用的格式字串 `Datetime` `Datetimeoffset` 。 有關如何格式化日期時間的資訊，請參閱[自訂日期和時間格式字串](/dotnet/standard/base-types/custom-date-and-time-format-strings)。 一般而言，您不需要指定或變更這個屬性。 深入瞭解 [資料類型對應](#data-type-mapping)。 | 否       |

除了下列屬性之外，還支援下列屬性 `translator` `mappings` ：

| 屬性            | 描述                                                  | 必要 |
| ------------------- | ------------------------------------------------------------ | -------- |
| collectionReference | 從階層式來源複製資料（例如 Cosmos DB、MongoDB 或 REST 連接器）時適用。<br>如果您想要逐一查看 **陣列欄位內** 相同模式的物件並擷取資料，然後轉換為每個物件一個資料列，則請指定該陣列的 JSON 路徑，以執行交叉套用。 | 否       |

#### <a name="tabular-source-to-tabular-sink"></a>表格式接收器的表格式來源

例如，若要將資料從 Salesforce 複製到 Azure SQL Database 並明確地對應三個數據行：

1. 在 [複製活動-> 對應] 索引標籤上，按一下 [匯 **入架構** ] 按鈕以匯入來源和接收架構。

2. 對應需要的欄位，並排除/刪除其餘部分。

![將表格式對應至表格式](media/copy-activity-schema-and-type-mapping/map-tabular-to-tabular.png)

相同的對應可以設定為複製活動承載中的下列內容 (查看 `translator`) ：

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

若要從帶分隔符號的文本檔案複製資料 (s) 沒有標頭行，則資料行是以序數而非名稱表示。 

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

#### <a name="hierarchical-source-to-tabular-sink"></a>表格式接收器的階層式來源

將資料從階層式來源複製到表格式接收時，複製活動支援下列功能：

- 從物件和陣列中取出資料。
- 交叉套用具有相同模式的多個物件，在此情況下，會將一個 JSON 物件轉換成表格式結果中的多個記錄。

如需更先進的階層式到表格式轉換，您可以使用 [資料流程](concepts-data-flow-overview.md)。 

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

而且，您想要使用標題列將它複製到文字檔中，方法是將陣列中的資料簡維 *(order_pd 和 order_price)* 和交叉聯結與一般根資訊 *(數位、日期和城市)*：

| orderNumber | orderDate | order_pd | order_price | city    |
| ----------- | --------- | -------- | ----------- | ------- |
| 01          | 20170122  | P1       | 23          | Seattle |
| 01          | 20170122  | P2       | 13          | Seattle |
| 01          | 20170122  | P3       | 231         | Seattle |

您可以在 Data Factory 撰寫 UI 上定義這類對應：

1. 在 [複製活動-> 對應] 索引標籤上，按一下 [匯 **入架構** ] 按鈕以匯入來源和接收架構。 當 Data Factory 在匯入架構時，範例前幾個物件，如果未顯示任何欄位，您可以將它加入至階層中的正確層-將滑鼠停留在現有的功能變數名稱上，然後加入宣告節點、物件或陣列。

2. 選取您要從中反復查看和解壓縮資料的陣列。 它會自動填入做為 **集合參考**。 請注意，這類操作只支援單一陣列。

3. 將需要的欄位對應到接收。 Data Factory 會自動判斷階層式端的對應 JSON 路徑。

![使用 UI 將階層式對應至表格式](media/copy-activity-schema-and-type-mapping/map-hierarchical-to-tabular-ui.png)

您也可以切換至 **Advanced editor**，在這種情況下，您可以直接查看並編輯欄位的 JSON 路徑。 如果您選擇在此視圖中加入新的對應，請指定 JSON 路徑。

![使用 advanced editor 將階層式對應至表格式](media/copy-activity-schema-and-type-mapping/map-hierarchical-to-tabular-advanced-editor.png)

相同的對應可以設定為複製活動承載中的下列內容 (查看 `translator`) ：


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

使用者體驗流程類似于 [表格式接收器的階層式來源](#hierarchical-source-to-tabular-sink)。 

將資料從表格式來源複製到階層式接收時，不支援在物件內寫入陣列。

從階層式來源複製資料到階層式接收時，您可以藉由選取物件/陣列並對應至接收器而不觸及內部欄位，來額外保留整個層的階層。

如需更先進的資料調整轉換，您可以使用 [資料流程](concepts-data-flow-overview.md)。 

### <a name="parameterize-mapping"></a>參數化對應

如果您想要建立範本化管線以動態複製大量物件，請判斷您是否可以利用 [預設對應](#default-mapping) ，或您需要為個別物件定義 [明確的對應](#explicit-mapping) 。

如果需要明確的對應，您可以：

1. 在管線層級定義具有物件類型的參數，例如 `mapping` 。

2. 參數化對應：在 [複製活動-> 對應] 索引標籤上，選擇新增動態內容並選取上述參數。 活動承載如下所示：

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

3. 建立要傳入對應參數的值。 它應該是整個定義的物件 `translator` ，請參閱 [明確對應](#explicit-mapping) 一節中的範例。 例如，針對表格式接收復制的表格式來源，此值應為 `{"type":"TabularTranslator","mappings":[{"source":{"name":"Id"},"sink":{"name":"CustomerID"}},{"source":{"name":"Name"},"sink":{"name":"LastName"}},{"source":{"name":"LastModifiedDate"},"sink":{"name":"ModifiedDate"}}]}` 。

## <a name="data-type-mapping"></a>資料類型對應

複製活動會使用下列流程，執行來源類型與接收類型的對應： 

1. 從來源原生資料類型轉換成 Azure Data Factory 過渡期資料類型。
2. 視需要自動轉換過渡資料類型，以符合對應的接收類型，適用于 [預設](#default-mapping) 對應和 [明確](#explicit-mapping)對應。
3. 從 Azure Data Factory 過渡期資料類型轉換為接收原生資料類型。

複製活動目前支援下列過渡資料類型：布林值、位元組、位元組陣列、Datetime、DatetimeOffset、Decimal、Double、GUID、Int16、Int32、Int64、SByte、Single、String、Timespan、UInt16、UInt32 和 UInt64。

從來源到接收的過渡型別之間，支援下列資料類型轉換。

| Source\Sink | Boolean | 位元組陣列 | Decimal | 日期/時間 <small> (1) </small> | 浮點數 <small> (2) </small> | GUID | 整數 <small> (3) </small> | String | TimeSpan |
| ----------- | ------- | ---------- | ------- | ---------------------------- | ------------------------------ | ---- | -------------------------- | ------ | -------- |
| Boolean     | ✓       |            | ✓       |                              | ✓                              |      | ✓                          | ✓      |          |
| 位元組陣列  |         | ✓          |         |                              |                                |      |                            | ✓      |          |
| 日期/時間   |         |            |         | ✓                            |                                |      |                            | ✓      |          |
| Decimal     | ✓       |            | ✓       |                              | ✓                              |      | ✓                          | ✓      |          |
| 浮點數 | ✓       |            | ✓       |                              | ✓                              |      | ✓                          | ✓      |          |
| GUID        |         |            |         |                              |                                | ✓    |                            | ✓      |          |
| 整數     | ✓       |            | ✓       |                              | ✓                              |      | ✓                          | ✓      |          |
| String      | ✓       | ✓          | ✓       | ✓                            | ✓                              | ✓    | ✓                          | ✓      | ✓        |
| TimeSpan    |         |            |         |                              |                                |      |                            | ✓      | ✓        |

 (1) 日期/時間包含 DateTime 和 DateTimeOffset。

 (2) 浮點數包含 Single 和 Double。

 (3) 整數包含 SByte、Byte、Int16、UInt16、Int32、UInt32、Int64 和 UInt64。

> [!NOTE]
> - 在表格式資料之間進行複製時，目前支援這類資料類型轉換。 不支援階層式來源/接收，這表示來源和接收過渡類型之間沒有系統定義的資料類型轉換。
> - 這項功能適用于最新的資料集模型。 如果您在 UI 中看不到這個選項，請嘗試建立新的資料集。

以下是在 `translator` programmatical 編寫) 一節中，用於資料類型轉換的複製活動中支援的屬性 (：

| 屬性                         | 描述                                                  | 必要 |
| -------------------------------- | ------------------------------------------------------------ | -------- |
| typeConversion                   | 啟用新的資料類型轉換體驗。 <br>預設值為 false，因為回溯相容性。<br><br>針對在2020年6月之後透過 Data Factory 撰寫 UI 所建立的新複製活動，預設會啟用此資料類型轉換以獲得最佳體驗，而您可以在 [複製活動-> 對應] 索引標籤上看到適用案例的下列類型轉換設定。 <br>若要以程式設計方式建立管線，您必須明確地將屬性設為 `typeConversion` true 以啟用它。<br>針對在發行這項功能之前建立的現有複製活動，您將不會在 Data Factory 撰寫 UI 上看到類型轉換選項，以提供回溯相容性。 | 否       |
| typeConversionSettings           | 類型轉換設定的群組。 當 `typeConversion` 設為時套用 `true` 。 下列屬性全都在此群組底下。 | 否       |
| *下 `typeConversionSettings`* |                                                              |          |
| allowDataTruncation              | 在複製期間將來源資料轉換成具有不同類型的接收時，允許資料截斷，例如從 DatetimeOffset 轉換成整數，從 DatetimeOffset 到 Datetime。 <br>預設值為 true。 | 否       |
| treatBooleanAsNumber             | 將布林值視為數位，例如，true 為1。<br>預設值為 False。 | 否       |
| dateTimeFormat                   | 在不含時區時差與字串的日期之間進行轉換時的格式字串，例如 `yyyy-MM-dd HH:mm:ss.fff` 。  如需詳細資訊，請參閱 [自訂日期和時間格式字串](/dotnet/standard/base-types/custom-date-and-time-format-strings) 。 | 否       |
| dateTimeOffsetFormat             | 使用時區位移和字串在日期之間轉換時的格式字串，例如 `yyyy-MM-dd HH:mm:ss.fff zzz` 。  如需詳細資訊，請參閱 [自訂日期和時間格式字串](/dotnet/standard/base-types/custom-date-and-time-format-strings) 。 | 否       |
| timeSpanFormat                   | 在時間週期和字串之間轉換時的格式字串，例如 `dd\.hh\:mm` 。 如需詳細資訊，請參閱 [自訂 TimeSpan 格式字串](/dotnet/standard/base-types/custom-timespan-format-strings) 。 | 否       |
| culture                          | 轉換類型時要使用的文化特性資訊，例如 `en-us` 或 `fr-fr` 。 | 否       |

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
> 為了回溯相容性，仍支援下列將來源資料行/欄位對應至接收的模型。 建議您使用 [架構對應](#schema-mapping)中所述的新模型。 Data Factory 撰寫 UI 已切換為產生新的模型。

### <a name="alternative-column-mapping-legacy-model"></a>替代資料行對應 (舊版模型) 

您可以指定複製活動-> `translator`  ->  `columnMappings` ，以便在表格式資料之間進行對應。 在此情況下，輸入和輸出資料集都需要 "structure" 區段。 資料行對應支援將來源資料集「結構」中的所有或一部分資料行，對應至接收資料集「結構」中的所有資料行。 以下是會導致發生例外狀況的錯誤狀況：

- 來源資料存放區查詢結果在輸入資料集的「結構」區段中並未指定資料行名稱。
- 接收資料存放區 (如果含有預先定義的結構描述) 在輸出資料集的「結構」區段中並未指定資料行名稱。
- 接收資料集「結構」中的資料行數量多於或少於對應中所指定的數量。
- 重複的對應。

在下列範例中，輸入資料集有一個結構，且指向內部部署 Oracle 資料庫中的資料表。

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

下列 JSON 定義了管線中的複製活動。 來源中的資料行，使用 **translator**  ->  **>columnmappings** 屬性對應至接收中的資料行。

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

### <a name="alternative-schema-mapping-legacy-model"></a>替代架構對應 (舊版模型) 

您可以指定複製活動->， `translator`  ->  `schemaMapping` 以便在階層式資料和表格式資料之間進行對應，例如，從 MongoDB/REST 複製到文字檔，以及從 Oracle 複製到 Azure Cosmos DB 適用于 mongodb 的 API。 複製活動的 `translator` 區段支援下列屬性：

| 屬性            | 描述                                                  | 必要 |
| :------------------ | :----------------------------------------------------------- | :------- |
| type                | 複製活動轉譯器的 type 屬性必須設定為： **TabularTranslator** | 是      |
| schemaMapping       | 機碼值組的集合，表示 **從來源端到接收端** 的對應關聯性。<br/>- **Key：** 代表 source。 若為 **表格式來源**，請指定資料集結構中所定義的資料行名稱;針對 [ **階層式來源**]，指定要解壓縮和對應的每個欄位的 JSON 路徑運算式。<br>- **Value：** 代表 sink。 若為 **表格式接收**，請指定資料集結構中所定義的資料行名稱;針對 **階層式接收**，請針對要解壓縮和對應的每個欄位指定 JSON 路徑運算式。 <br>在階層式資料的情況下，針對根物件底下的欄位，JSON 路徑的開頭為根 $;針對屬性所選擇的陣列內的欄位 `collectionReference` ，JSON 路徑會從陣列元素開始。 | 是      |
| collectionReference | 如果您想要逐一查看 **陣列欄位內** 相同模式的物件並擷取資料，然後轉換為每個物件一個資料列，則請指定該陣列的 JSON 路徑，以執行交叉套用。 只有在階層式資料是來源時，才支援這個屬性。 | 否       |

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

您想要壓平合併陣列內的資料 (order_pd 和 order_price)，將內容複製到下列格式的 Azure SQL 資料表，並與一般根資訊 (編號、日期和城市) 交叉聯結︰

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
