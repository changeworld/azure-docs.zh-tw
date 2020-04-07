---
title: 實體
description: Azure 遠端呈現 API 範圍內的實體定義
author: florianborn71
ms.author: flborn
ms.date: 02/03/2020
ms.topic: conceptual
ms.openlocfilehash: d7b9ecd048b080ae0ec9fd3fb7a4fb35009551b8
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/06/2020
ms.locfileid: "80681944"
---
# <a name="entities"></a>實體

*實體*表示空間中的可移動物件,是遠端呈現內容的基本構建基塊。

## <a name="entity-properties"></a>實體屬性

實體具有由位置、旋轉和比例定義的變換。 實體本身沒有任何可觀察到的功能。 相反,行為通過附加到實體的元件添加。 例如,附加[CutPlane 元件](../overview/features/cut-planes.md)將在實體的位置創建切割平面。

實體本身最重要的方面是層次結構和生成的層次結構轉換。 例如,當多個實體作為子實體附加到共用父實體時,所有這些實體可以通過更改父實體的轉換以一致方式移動、旋轉和縮放。

實體由其父實體唯一擁有,這意味著當父實體被`Entity.Destroy()`銷毀時,其子級和所有連接[的元件](components.md)也是如此。 因此,通過調用`Destroy`返回的模型的根`AzureSession.Actions.LoadModelAsync()`節點 或其 SAS 變體`AzureSession.Actions.LoadModelFromSASAsync()`,從場景中刪除模型。

實體是在伺服器載入內容或使用者要向場景中添加物件時創建的。 例如,如果使用者想要添加切割平面以可視化網格的內部,則使用者可以創建平面應存在的實體,然後將切割平面元件添加到其中。

## <a name="query-functions"></a>查詢函數

實體上有兩種類型的查詢函數:同步調用和異步調用。 同步查詢只能用於用戶端上存在且不需要太多計算的數據。 範例是查詢元件、相對物件轉換或父/子關係。 非同步查詢用於僅駐留在伺服器上或涉及額外計算的數據,這些資料在用戶端上運行成本太高。 示例包括空間邊界查詢或元數據查詢。

### <a name="querying-components"></a>查詢元件

要尋找特定型態的元件,請使用`FindComponentOfType`:

```cs
CutPlaneComponent cutplane = (CutPlaneComponent)entity.FindComponentOfType(ObjectType.CutPlaneComponent);

// or alternatively:
CutPlaneComponent cutplane = entity.FindComponentOfType<CutPlaneComponent>();
```

### <a name="querying-transforms"></a>查詢轉換

轉換查詢是物件的同步調用。 請務必注意,通過 API 查詢的轉換是相對於物件的父級的本地空間轉換。 例外是根對象,對於根物件,本地空間和世界空間是相同的。

> [!NOTE]
> 沒有專用 API 來查詢任意物件的世界空間轉換。

```cs
// local space transform of the entity
Double3 translation = entity.Position;
Quaternion rotation = entity.Rotation;
```

### <a name="querying-spatial-bounds"></a>查詢空間邊界

邊界查詢是使用一個實體作為根的對完整物件層次結構進行操作的非同步調用。 請參閱有關[物件邊界的](object-bounds.md)專用章節。

### <a name="querying-metadata"></a>查詢中繼資料

元數據是存儲在物件上的其他數據,伺服器忽略這些數據。 物件中繼資料本質上是一組(名稱、值)對,_其中值_可以是數位、布林或字串類型。 元數據可以隨模型一起匯出。

元數據查詢是特定實體上的非同步調用。 查詢僅返回單個實體的元數據,而不是子圖形的合併資訊。

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

即使物件不包含任何元數據,查詢也會成功。

## <a name="next-steps"></a>後續步驟

* [元件](components.md)
* [物件邊界](object-bounds.md)
