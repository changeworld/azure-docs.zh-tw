---
title: 查詢對應項圖形
titleSuffix: Azure Digital Twins
description: 如需相關資訊，請參閱如何查詢 Azure 數位 Twins 對應項圖形。
author: baanders
ms.author: baanders
ms.date: 3/26/2020
ms.topic: conceptual
ms.service: digital-twins
ms.openlocfilehash: 05bcbf8df695ba308a6eaff5e7401f0a6d638747
ms.sourcegitcommit: 46f8457ccb224eb000799ec81ed5b3ea93a6f06f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/28/2020
ms.locfileid: "87337597"
---
# <a name="query-the-azure-digital-twins-twin-graph"></a>查詢 Azure 數位 Twins 對應項圖形

本文提供使用[Azure 數位 Twins 查詢存放區語言](concepts-query-language.md)查詢對應項[圖形](concepts-twins-graph.md)的範例和詳細資料，以取得相關資訊。 您可以使用 Azure 數位 Twins[**查詢 api**](how-to-use-apis-sdks.md)在圖表上執行查詢。

## <a name="query-syntax"></a>查詢語法

以下是一些範例查詢，其中說明查詢語言結構並執行可能的查詢作業。

依屬性取得[數位 twins](concepts-twins-graph.md) （包括識別碼和中繼資料）：
```sql
SELECT  * 
FROM DigitalTwins T  
WHERE T.firmwareVersion = '1.1'
AND T.$dtId in ['123', '456']
AND T.Temperature = 70
```

依[模型](concepts-models.md)取得數位 twins
```sql
SELECT  * 
FROM DigitalTwins T  
WHERE IS_OF_MODEL(T , 'dtmi:com:contoso:Space;3')
AND T.roomSize > 50
```

> [!TIP]
> 數位對應項的識別碼是使用 [中繼資料] 欄位進行查詢 `$dtId` 。

### <a name="query-based-on-relationships"></a>以關聯性為基礎的查詢

根據數位 twins 的關聯性進行查詢時，Azure 數位 Twins 查詢存放區語言會有特殊的語法。

關聯性會提取至子句中的查詢範圍 `FROM` 。 與「傳統」 SQL 類型語言的差異在於，這個子句中的每個運算式 `FROM` 都不是資料表，而是由 `FROM` 子句來表示跨實體的關聯性，並使用的 Azure 數位 Twins 版本來撰寫 `JOIN` 。 

回想一下，使用 Azure 數位 Twins[模型](concepts-models.md)功能時，關聯性不會與 Twins 分開存在。 這表示 Azure 數位 Twins 查詢存放區語言與 `JOIN` 一般 SQL 有點不同 `JOIN` ，因為此處的關聯性無法獨立查詢，且必須系結至對應項。
為了併入這項差異， `RELATED` 子句中使用關鍵字 `JOIN` 來參考對應項的關聯性集合。 

下一節提供幾個範例，說明這看起來的樣子。

> [!TIP]
> 在概念上，這項功能會模擬以檔為中心的 CosmosDB 功能， `JOIN` 可在檔內的子物件上執行。 CosmosDB 會使用 `IN` 關鍵字來指示，其 `JOIN` 目的是要在目前的內容檔中反復查看陣列元素。

#### <a name="relationship-based-query-examples"></a>以關聯性為基礎的查詢範例

若要取得包含關聯性的資料集，請使用單一 `FROM` 語句，後面接著 N `JOIN` 個語句，其中的 `JOIN` 語句會在前一個或語句的結果上表示關聯性 `FROM` `JOIN` 。

以下是以關聯性為基礎的範例查詢。 此程式碼片段會選取*ID*屬性為 ' ABC ' 的所有數位 twins，以及透過*contains*關聯性與這些數位 twins 相關的所有數位 twins。 

```sql
SELECT T, CT
FROM DIGITALTWINS T
JOIN CT RELATED T.contains
WHERE T.$dtId = 'ABC' 
```

>[!NOTE] 
> 開發人員不需要將此 `JOIN` 值與子句中的索引鍵值相互關聯 `WHERE` （或指定以內嵌方式定義的索引鍵值 `JOIN` ）。 此相互關聯是由系統自動計算，因為關聯性屬性本身會識別目標實體。

#### <a name="query-the-properties-of-a-relationship"></a>查詢關聯性的屬性

類似于數位 twins 具有透過 DTDL 描述之屬性的方式，關聯性也可以具有屬性。 Azure 數位 Twins 查詢存放區語言會藉由將別名指派給子句內的關聯性，來篩選和投射關聯性 `JOIN` 。 

例如，假設有一個具有*reportedCondition*屬性的*servicedBy*關聯性。 在下列查詢中，會為此關聯性提供 ' R ' 的別名，以便參考其屬性。

```sql
SELECT T, SBT, R
FROM DIGITALTWINS T
JOIN SBT RELATED T.servicedBy R
WHERE T.$dtId = 'ABC' 
AND R.reportedCondition = 'clean'
```

在上述範例中，請注意*reportedCondition*是*servicedBy*關聯性本身的屬性（而不是具有*servicedBy*關聯性的數位對應項）。

## <a name="run-queries-with-an-api-call"></a>使用 API 呼叫執行查詢

一旦決定了查詢字串，您就可以呼叫**查詢 API**來執行它。
下列程式碼片段說明從用戶端應用程式進行的這項呼叫：

```csharp
var client = new AzureDigitalTwinsAPIClient(<your-credentials>);
client.BaseUri = new Uri(<your-Azure-Digital-Twins-instance-URL>);

QuerySpecification spec = new QuerySpecification("SELECT * FROM digitaltwins");
QueryResult result = await client.Query.QueryTwinsAsync(spec);
```

此呼叫會以 QueryResult 物件的形式傳回查詢結果。 

查詢呼叫支援分頁。 以下是具有錯誤處理和分頁的完整範例：

```csharp
string query = "SELECT * FROM digitaltwins";
try
{
    AsyncPageable<string> qresult = client.QueryAsync(query);
    await foreach (string item in qresult) 
    {
        // Do something with each result
    }
}
catch (RequestFailedException e)
{
    Log.Error($"Error {e.Status}: {e.Message}");
    return null;
}
```

## <a name="query-limitations"></a>查詢限制

在查詢中反映實例的變更之前，可能會有最多10秒的延遲。 例如，如果您使用選取 API 完成建立或刪除 twins 之類的作業，結果可能不會立即反映在查詢 API 要求中。 等待短時間應該就足以解決。

在預覽期間使用有其他限制 `JOIN` 。
* 語句內不支援任何子查詢 `FROM` 。
* `OUTER JOIN`不支援語義，也就是說，如果關聯性的次序為零，則會從輸出結果集中刪除整個「資料列」。
* 在公開預覽期間，graph 遍歷深度會受到限制： `JOIN` 每個查詢只允許一個。
* 作業的來源 `JOIN` 受到限制：查詢必須宣告開始查詢的 twins。

## <a name="query-best-practices"></a>查詢最佳做法

以下是使用 Azure 數位 Twins 進行查詢的一些秘訣。

* 在模型設計階段中，請考慮查詢模式。 請嘗試確定單一查詢中需要回答的關聯性會模型化為單一層關係。
* 以避免來自圖表遍歷的大型結果集的方式設計屬性。
* 您可以藉由建立 twins 陣列並使用運算子查詢，大幅減少所需的查詢數目 `IN` 。 例如，假設有一個*建築物*包含*樓層*，而*樓層*包含*房間*。 若要在室內的建築物內搜尋會議室，您可以：

    1. 根據關聯性在大樓中尋找樓層 `contains`
        ```sql
        SELECT Floor
        FROM DIGITALTWINS Building
        JOIN Floor RELATED Building.contains
        WHERE Building.$dtId = @buildingId
        ``` 
    2. 若要找出房間，而不是逐一考慮樓層並執行 `JOIN` 查詢來尋找每個室內的房間，您可以使用建築物中的樓層集合（以下查詢中的命名*樓層*）進行查詢。

        在用戶端應用程式中：
        ```csharp
        var floors = "['floor1','floor2', ..'floorn']"; 
        ```
        在 [查詢：
        ```sql
        SELECT Room
        FROM DIGITALTWINS Floor
        JOIN Room RELATED Floor.contains
        WHERE Floor.$dtId IN ['floor1','floor2', ..'floorn']
        AND Room. Temperature > 72
        AND IS_OF_MODEL(Room, 'dtmi:com:contoso:Room;1')
        ```
* 屬性名稱和值會區分大小寫，因此請小心使用模型中定義的確切名稱。 如果屬性名稱拼錯或大小寫不正確，結果集會是空的，而且不會傳回任何錯誤。


## <a name="next-steps"></a>後續步驟

深入瞭解[Azure 數位 Twins api 和 sdk](how-to-use-apis-sdks.md)，包括用來執行本文中查詢的查詢 API。
