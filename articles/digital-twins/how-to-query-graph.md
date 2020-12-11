---
title: 查詢對應項圖形
titleSuffix: Azure Digital Twins
description: 如需詳細資訊，請參閱如何查詢 Azure 數位 Twins 對應項圖形。
author: baanders
ms.author: baanders
ms.date: 11/19/2020
ms.topic: conceptual
ms.service: digital-twins
ms.custom: contperf-fy21q2
ms.openlocfilehash: 966b87dfb3111d7a112ea99f37dee730495d491f
ms.sourcegitcommit: 3ea45bbda81be0a869274353e7f6a99e4b83afe2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/10/2020
ms.locfileid: "97032825"
---
# <a name="query-the-azure-digital-twins-twin-graph"></a>查詢 Azure 數位 Twins 對應項圖表

本文提供查詢範例和更詳細的指示，說明如何使用 **Azure 數位 Twins 查詢語言** 來查詢對應項 [圖表](concepts-twins-graph.md) 以取得相關資訊。  (如需查詢語言的簡介和其功能的完整清單，請參閱 [*概念：查詢語言*](concepts-query-language.md)) 。

本文的開頭是範例查詢，其中說明了數位 twins 的查詢語言結構和一般查詢作業。 接著，它會說明如何使用 Azure 數位 Twins [查詢 API](/rest/api/digital-twins/dataplane/query) 或 [SDK](how-to-use-apis-sdks.md#overview-data-plane-apis)，在撰寫查詢之後執行查詢。

> [!TIP]
> 如果您正在使用 API 或 SDK 呼叫來執行下列範例查詢，則必須將查詢文字壓縮成單一行。

## <a name="show-all-digital-twins"></a>顯示所有數位 twins

以下是會傳回實例中所有數位 twins 清單的基本查詢：

```sql
SELECT *
FROM DIGITALTWINS
```

## <a name="query-by-property"></a>依屬性查詢

依 **屬性** 取得數位 twins (包括識別碼和中繼資料) ：

```sql
SELECT  *
FROM DigitalTwins T  
WHERE T.firmwareVersion = '1.1'
AND T.$dtId in ['123', '456']
AND T.Temperature = 70
```

> [!TIP]
> 數位對應項的識別碼會使用元資料欄位來查詢 `$dtId` 。

您也可以根據 **是否已定義特定屬性** 來取得 twins。 以下查詢會取得具有已定義 *位置* 屬性的 twins：

```sql
SELECT * FROM DIGITALTWINS WHERE IS_DEFINED(Location)
```

這可協助您依卷 *標屬性進行* twins，如將標籤 [新增至數位 twins](how-to-use-tags.md)所述。 以下查詢會取得以 *紅色* 標記的所有 twins：

```sql
SELECT * FROM DIGITALTWINS WHERE IS_DEFINED(tags.red)
```

您也可以根據 **屬性的類型** 來取得 twins。 以下查詢會取得其 *溫度* 屬性為數字的 twins：

```sql
SELECT * FROM DIGITALTWINS T WHERE IS_NUMBER(T.Temperature)
```

## <a name="query-by-model"></a>依模型查詢

`IS_OF_MODEL`操作員可以用來根據對應項的 [**模型**](concepts-models.md)進行篩選。

它會考慮 [繼承](concepts-models.md#model-inheritance) 和模型 [版本](how-to-manage-model.md#update-models)設定，如果對應項符合下列其中一個條件，則會針對指定的對應項評估為 **true** ：

* 對應項會直接實作為提供的模型 `IS_OF_MODEL()` ，而且對應項上的模型版本號碼 *大於或等於* 提供之模型的版本號碼。
* 對應項會執行 *擴充* 提供之模型的模型 `IS_OF_MODEL()` ，而且對應項的擴充模型版本號碼會 *大於或等於* 提供之模型的版本號碼。

比方說，如果您查詢模型的 twins `dtmi:example:widget;4` ，則查詢會傳回所有以 **第4版或更高** 的 **widget** 模型為基礎的 twins，也會根據 **繼承自 widget** 之任何模型的 **第4版或更高** 版本來 twins。

`IS_OF_MODEL` 可以採用數個不同的參數，而本節的其餘部分則是專屬於其不同的多載選項。

最簡單的用法 `IS_OF_MODEL` 只會採用 `twinTypeName` 參數： `IS_OF_MODEL(twinTypeName)` 。
以下是在此參數中傳遞值的查詢範例：

```sql
SELECT * FROM DIGITALTWINS WHERE IS_OF_MODEL('dtmi:example:thing;1')
```

若要在有多個 (時指定要搜尋的對應項集合，例如 `JOIN` 使用) 時，請新增 `twinCollection` 參數： `IS_OF_MODEL(twinCollection, twinTypeName)` 。
以下是為此參數新增值的查詢範例：

```sql
SELECT * FROM DIGITALTWINS DT WHERE IS_OF_MODEL(DT, 'dtmi:example:thing;1')
```

若要執行完全相符的動作，請新增 `exact` 參數： `IS_OF_MODEL(twinTypeName, exact)` 。
以下是為此參數新增值的查詢範例：

```sql
SELECT * FROM DIGITALTWINS WHERE IS_OF_MODEL('dtmi:example:thing;1', exact)
```

您也可以將這三個引數一起傳遞給： `IS_OF_MODEL(twinCollection, twinTypeName, exact)` 。
以下是針對所有三個參數指定值的查詢範例：

```sql
SELECT ROOM FROM DIGITALTWINS DT WHERE IS_OF_MODEL(DT, 'dtmi:example:thing;1', exact)
```

## <a name="query-by-relationship"></a>依關聯性查詢

根據數位 twins 的 **關聯** 性進行查詢時，Azure 數位 twins 查詢語言具有特殊的語法。

關聯性會提取到子句中的查詢範圍內 `FROM` 。 與「傳統」 SQL 類型語言的差異在於，這個子句中的每個運算式 `FROM` 都不是資料表; 而是會 `FROM` 表示跨實體的關聯性，並使用 Azure 數位 Twins 版來撰寫 `JOIN` 。

回想一下，使用 Azure 數位 Twins [模型](concepts-models.md) 功能時，關聯性不會與 Twins 獨立存在。 這表示 Azure 數位 Twins 查詢語言與 `JOIN` 一般 SQL 稍有不同 `JOIN` ，因為此處的關聯性無法獨立查詢，而且必須系結至對應項。
為了併入這項差異， `RELATED` 子句中會使用關鍵字 `JOIN` 來參考對應項的關聯性集合。

下一節提供幾個範例，說明這看起來的樣子。

> [!TIP]
> 從概念上來說，這項功能會模擬 CosmosDB 的檔中心功能，在這種情況下， `JOIN` 可以在檔內的子物件上執行。 CosmosDB 會使用 `IN` 關鍵字來指出 `JOIN` 要在目前的內容檔內反復查看陣列元素。

### <a name="relationship-based-query-examples"></a>以關聯性為基礎的查詢範例

若要取得包含關聯性的資料集，請使用單一 `FROM` 語句，後面接著 N 個 `JOIN` 語句，這些語句會在上一個 `JOIN` 或語句的結果上表達關聯性 `FROM` `JOIN` 。

以下是以關聯性為基礎的查詢範例。 此程式碼片段會選取 *識別碼* 屬性為 ' ABC ' 的所有數位 twins，以及透過 *contains* 關聯性與這些數位 twins 相關的所有數位 twins。

```sql
SELECT T, CT
FROM DIGITALTWINS T
JOIN CT RELATED T.contains
WHERE T.$dtId = 'ABC'
```

> [!NOTE]
> 開發人員不需要將此關聯 `JOIN` 與子句中的索引鍵值 `WHERE` (或指定以定義) 內嵌的索引鍵值 `JOIN` 。 系統會自動計算此關聯性，因為關聯性屬性本身會識別目標實體。

### <a name="query-the-properties-of-a-relationship"></a>查詢關聯性的屬性

類似于數位 twins 具有透過 DTDL 描述的屬性，關聯性也可以有屬性。 您可以 **根據關聯** 性的屬性來查詢 twins。
Azure 數位 Twins 查詢語言可讓您藉由將別名指派給子句內的關聯性，來篩選和投射關聯性 `JOIN` 。

例如，假設有一個具有 *reportedCondition* 屬性的 *servicedBy* 關聯性。 在下列查詢中，會為此關聯性指定 ' R ' 的別名，以便參考其屬性。

```sql
SELECT T, SBT, R
FROM DIGITALTWINS T
JOIN SBT RELATED T.servicedBy R
WHERE T.$dtId = 'ABC'
AND R.reportedCondition = 'clean'
```

在上述範例中，請注意 *reportedCondition* 是 *servicedBy* 關聯性本身的屬性， (不是具有 *servicedBy* 關聯性) 的數位對應項。

### <a name="query-with-multiple-joins"></a>使用多個聯結進行查詢

`JOIN`單一查詢支援最多五秒。 這可讓您一次跨越多個層級的關聯性。

以下是多重聯結查詢的範例，它會取得房間1和2中的淺色面板中包含的所有光源燈泡。

```sql
SELECT LightBulb
FROM DIGITALTWINS Room
JOIN LightPanel RELATED Room.contains
JOIN LightBulb RELATED LightPanel.contains
WHERE IS_OF_MODEL(LightPanel, 'dtmi:contoso:com:lightpanel;1')
AND IS_OF_MODEL(LightBulb, 'dtmi:contoso:com:lightbulb ;1')
AND Room.$dtId IN ['room1', 'room2']
```

## <a name="count-items"></a>計數專案

您可以使用子句來計算結果集中的專案數 `Select COUNT` ：

```sql
SELECT COUNT()
FROM DIGITALTWINS
```

加入 `WHERE` 子句來計算符合特定準則的專案數目。 以下範例會根據對應項模型的類型，使用套用的篩選來進行計數 (如需此語法的詳細資訊，請參閱下面的 [*依模型查詢*](#query-by-model)) ：

```sql
SELECT COUNT()
FROM DIGITALTWINS
WHERE IS_OF_MODEL('dtmi:sample:Room;1')

SELECT COUNT()
FROM DIGITALTWINS c
WHERE IS_OF_MODEL('dtmi:sample:Room;1') AND c.Capacity > 20
```

您也可以 `COUNT` 搭配子句來使用 `JOIN` 。 以下查詢會計算房間1和2的淺色面板中包含的所有光源燈泡：

```sql
SELECT COUNT()  
FROM DIGITALTWINS Room  
JOIN LightPanel RELATED Room.contains  
JOIN LightBulb RELATED LightPanel.contains  
WHERE IS_OF_MODEL(LightPanel, 'dtmi:contoso:com:lightpanel;1')  
AND IS_OF_MODEL(LightBulb, 'dtmi:contoso:com:lightbulb ;1')  
AND Room.$dtId IN ['room1', 'room2']
```

## <a name="filter-results-select-top-items"></a>篩選結果：選取最上層專案

您可以使用子句，在查詢中選取數個 "top" 專案 `Select TOP` 。

```sql
SELECT TOP (5)
FROM DIGITALTWINS
WHERE ...
```

## <a name="filter-results-specify-return-set-with-projections"></a>篩選結果：使用投影指定傳回集

藉由在語句中使用投影 `SELECT` ，您可以選取查詢將傳回的資料行。

>[!NOTE]
>此時不支援複雜的屬性。 若要確定投射屬性是否有效，請將投影與檢查合併 `IS_PRIMITIVE` 。

以下是使用投射來傳回 twins 和關聯性的查詢範例。 下列查詢會從案例中投影取用 *者*、*工廠* 和 *邊緣*，其中識別碼為 *ABC* 的 *factory* 透過 *factory* 的關聯性與取用 *者* 相關聯，而該關聯性會顯示為 *邊緣*。

```sql
SELECT Consumer, Factory, Edge
FROM DIGITALTWINS Factory
JOIN Consumer RELATED Factory.customer Edge
WHERE Factory.$dtId = 'ABC'
```

您也可以使用投影來傳回對應項的屬性。 下列查詢會透過 *factory* 的關聯性，投射與具有 *ABC* 的 *Factory* 相關聯之取用 *者* 的 *名稱* 屬性。

```sql
SELECT Consumer.name
FROM DIGITALTWINS Factory
JOIN Consumer RELATED Factory.customer Edge
WHERE Factory.$dtId = 'ABC'
AND IS_PRIMITIVE(Consumer.name)
```

您也可以使用投影來傳回關聯性的屬性。 如同上述範例中所示，下列查詢會透過 Factory 的關聯 *性，將與處理站**相關之取用**者* 的 *名稱* 屬性投影至 *客戶*;但現在它也會傳回該關聯性的兩個屬性： *prop1* 和 *this.prop2*。 其方式是將關聯性 *邊緣* 命名並收集其屬性。  

```sql
SELECT Consumer.name, Edge.prop1, Edge.prop2, Factory.area
FROM DIGITALTWINS Factory
JOIN Consumer RELATED Factory.customer Edge
WHERE Factory.$dtId = 'ABC'
AND IS_PRIMITIVE(Factory.area) AND IS_PRIMITIVE(Consumer.name) AND IS_PRIMITIVE(Edge.prop1) AND IS_PRIMITIVE(Edge.prop2)
```

您也可以使用別名來簡化投影的查詢。

下列查詢會執行與上一個範例相同的作業，但它會將屬性名稱的別名設為 `consumerName` 、 `first` 、 `second` 和 `factoryArea` 。

```sql
SELECT Consumer.name AS consumerName, Edge.prop1 AS first, Edge.prop2 AS second, Factory.area AS factoryArea
FROM DIGITALTWINS Factory
JOIN Consumer RELATED Factory.customer Edge
WHERE Factory.$dtId = 'ABC'
AND IS_PRIMITIVE(Factory.area) AND IS_PRIMITIVE(Consumer.name) AND IS_PRIMITIVE(Edge.prop1) AND IS_PRIMITIVE(Edge.prop2)
```

以下是查詢相同集合的類似查詢，但僅將 *Consumer.name* 屬性設定為 `consumerName` ，並將完整 *Factory* 投射為對應項。

```sql
SELECT Consumer.name AS consumerName, Factory
FROM DIGITALTWINS Factory
JOIN Consumer RELATED Factory.customer Edge
WHERE Factory.$dtId = 'ABC'
AND IS_PRIMITIVE(Factory.area) AND IS_PRIMITIVE(Consumer.name)
```

## <a name="build-efficient-queries-with-the-in-operator"></a>使用 IN 運算子建立有效率的查詢

您可以藉由建立 twins 的陣列，並使用運算子查詢來大幅減少所需的查詢數目 `IN` 。 

例如，假設 *大樓* 包含 *樓層* ，而 *地面* 包含 *房間*。 若要搜尋熱大樓內的房間，有一種方式是遵循這些步驟。

1. 根據關聯性找出大樓中的樓層 `contains` 。

    ```sql
    SELECT Floor
    FROM DIGITALTWINS Building
    JOIN Floor RELATED Building.contains
    WHERE Building.$dtId = @buildingId
    ```

2. 若要找出房間，而不是逐一考慮樓層並執行 `JOIN` 查詢以找出每個房間的房間，您可以在下列查詢中，使用名為 *Floor* 的建築物 (中的樓層集合進行查詢) 。

    在用戶端應用程式中：
    
    ```csharp
    var floors = "['floor1','floor2', ..'floorn']"; 
    ```
    
    在查詢中：
    
    ```sql
    
    SELECT Room
    FROM DIGITALTWINS Floor
    JOIN Room RELATED Floor.contains
    WHERE Floor.$dtId IN ['floor1','floor2', ..'floorn']
    AND Room. Temperature > 72
    AND IS_OF_MODEL(Room, 'dtmi:com:contoso:Room;1')
    
    ```

## <a name="other-compound-query-examples"></a>其他複合查詢範例

您可以使用組合運算子 **結合** 上述任何類型的查詢，以在單一查詢中包含更多詳細資料。 以下是一些額外的複合查詢範例，可同時查詢多個類型的對應項描述項。

| 描述 | 查詢 |
| --- | --- |
| 在 *123 空間* 的裝置上，傳回服務角色為操作員的 MxChip 裝置 | `SELECT device`<br>`FROM DigitalTwins space`<br>`JOIN device RELATED space.has`<br>`WHERE space.$dtid = 'Room 123'`<br>`AND device.$metadata.model = 'dtmi:contoso:com:DigitalTwins:MxChip:3'`<br>`AND has.role = 'Operator'` |
| 取得具有名為 *Contains* 之關聯性的 twins，以及識別碼為 *id1* 的另一個對應項 | `SELECT Room`<br>`FROM DIGITALTWINS Room`<br>`JOIN Thermostat RELATED Room.Contains`<br>`WHERE Thermostat.$dtId = 'id1'` |
| 取得 *floor11* 所包含之房間模型的所有房間 | `SELECT Room`<br>`FROM DIGITALTWINS Floor`<br>`JOIN Room RELATED Floor.Contains`<br>`WHERE Floor.$dtId = 'floor11'`<br>`AND IS_OF_MODEL(Room, 'dtmi:contoso:com:DigitalTwins:Room;1')` |

## <a name="run-queries-with-the-api"></a>使用 API 執行查詢

一旦您決定查詢字串之後，就可以呼叫 [**查詢 API**](/rest/api/digital-twins/dataplane/query)來執行它。

您可以直接呼叫 API，或使用其中一個適用于 Azure 數位 Twins 的 [sdk](how-to-use-apis-sdks.md#overview-data-plane-apis) 。

下列程式碼片段說明從用戶端應用程式呼叫 [.net (c # ) SDK](/dotnet/api/overview/azure/digitaltwins/client?view=azure-dotnet&preserve-view=true) ：

```csharp
    string adtInstanceEndpoint = "https://<your-instance-hostname>";

    var credential = new DefaultAzureCredential();
    DigitalTwinsClient client = new DigitalTwinsClient(new Uri(adtInstanceEndpoint), credential);

    // Run a query for all twins   
    string query = "SELECT * FROM DIGITALTWINS";
    AsyncPageable<BasicDigitalTwin> result = client.QueryAsync<BasicDigitalTwin>(query);
```

此呼叫會以 [BasicDigitalTwin](/dotnet/api/azure.digitaltwins.core.basicdigitaltwin?view=azure-dotnet&preserve-view=true) 物件的形式傳回查詢結果。

查詢呼叫支援分頁。 以下是使用 `BasicDigitalTwin` as 查詢結果型別以及錯誤處理和分頁的完整範例：

```csharp
try
{
    await foreach(BasicDigitalTwin twin in result)
        {
            // You can include your own logic to print the result
            // The logic below prints the twin's ID and contents
            Console.WriteLine($"Twin ID: {twin.Id} \nTwin data");
            IDictionary<string, object> contents = twin.Contents;
            foreach (KeyValuePair<string, object> kvp in contents)
            {
                Console.WriteLine($"{kvp.Key}  {kvp.Value}");
            }
        }
}
catch (RequestFailedException e)
{
    Console.WriteLine($"Error {e.Status}: {e.Message}");
    throw;
}
```

## <a name="next-steps"></a>後續步驟

深入瞭解 [Azure 數位 Twins api 和 sdk](how-to-use-apis-sdks.md)，包括用來執行本文中查詢的查詢 API。
