---
title: 實體
description: Azure 遠端轉譯 API 範圍中的實體定義
author: florianborn71
ms.author: flborn
ms.date: 02/03/2020
ms.topic: conceptual
ms.openlocfilehash: d7b9ecd048b080ae0ec9fd3fb7a4fb35009551b8
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "80681944"
---
# <a name="entities"></a>實體

*實體*代表空間中的可移動物件，而是遠端呈現內容的基本建立區塊。

## <a name="entity-properties"></a>實體屬性

實體具有由位置、旋轉和尺規定義的轉換。 By 本身的實體沒有任何可觀察的功能。 相反地，行為是透過附加至實體的元件來新增。 例如，附加[CutPlaneComponent](../overview/features/cut-planes.md)會在實體的位置建立剪下平面。

實體本身最重要的一點是階層和產生的階層式轉換。 例如，當多個實體附加為共用父實體的子系時，可以藉由變更父實體的轉換，來移動、旋轉和調整所有這些實體。

實體是由其父系唯一擁有的，這表示當父系使用`Entity.Destroy()`終結時，就是其子系和所有連接的[元件](components.md)。 因此，從場景中移除模型的方式是在模型`Destroy`的根節點上呼叫， `AzureSession.Actions.LoadModelAsync()`或其 SAS variant `AzureSession.Actions.LoadModelFromSASAsync()`所傳回的。

當伺服器載入內容時，或當使用者想要將物件新增至場景時，會建立實體。 例如，如果使用者想要新增剪下平面以視覺化網格的內部，使用者可以建立一個應該存在的平面，然後在其中新增「剪下平面」元件。

## <a name="query-functions"></a>查詢函數

實體上有兩種類型的查詢函數：同步和非同步呼叫。 同步查詢只可用於用戶端上的資料，而不需要大量計算。 範例包括查詢元件、相對物件轉換或父/子關聯性。 非同步查詢用於僅位於伺服器上的資料，或牽涉到在用戶端上執行太多資源的額外計算。 範例包括空間界限查詢或中繼資料查詢。

### <a name="querying-components"></a>查詢元件

若要尋找特定類型的元件，請使用`FindComponentOfType`：

```cs
CutPlaneComponent cutplane = (CutPlaneComponent)entity.FindComponentOfType(ObjectType.CutPlaneComponent);

// or alternatively:
CutPlaneComponent cutplane = entity.FindComponentOfType<CutPlaneComponent>();
```

### <a name="querying-transforms"></a>查詢轉換

轉換查詢是物件上的同步呼叫。 請務必注意，透過 API 查詢的轉換是與物件父系相對的本機空間轉換。 例外狀況是根物件，其本機空間和世界空間都相同。

> [!NOTE]
> 沒有專用的 API 可查詢任意物件的世界空間轉換。

```cs
// local space transform of the entity
Double3 translation = entity.Position;
Quaternion rotation = entity.Rotation;
```

### <a name="querying-spatial-bounds"></a>查詢空間界限

界限查詢是在完整物件階層上運作的非同步呼叫，會使用一個實體做為根。 請參閱有關[物件界限](object-bounds.md)的專屬章節。

### <a name="querying-metadata"></a>查詢中繼資料

中繼資料是指儲存在物件上的其他資料，伺服器會忽略此資訊。 物件中繼資料基本上是一組（名稱、值）配對，其中的_值_可以是數值、布林或字串類型。 中繼資料可以與模型一起匯出。

中繼資料查詢是特定實體上的非同步呼叫。 查詢只會傳回單一實體的中繼資料，而不會傳回子圖形的合併資訊。

```cs
MetadataQueryAsync metaDataQuery = entity.QueryMetaDataAsync();
metaDataQuery.Completed += (MetadataQueryAsync query) =>
{
    if (query.IsRanToCompletion)
    {
        ObjectMetaData metaData = query.Result;
        ObjectMetaDataEntry entry = metaData.GetMetadataByName("MyInt64Value");
        System.Int64 intValue = entry.AsInt64;

        // ...
    }
};
```

即使物件未包含任何中繼資料，查詢也會成功。

## <a name="next-steps"></a>後續步驟

* [元件](components.md)
* [物件界限](object-bounds.md)
