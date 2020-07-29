---
title: 實體
description: Azure 遠端轉譯 API 範圍中的實體定義
author: florianborn71
ms.author: flborn
ms.date: 02/03/2020
ms.topic: conceptual
ms.openlocfilehash: 5f6f7fc52a186117afcb92f6a2f80bf068e50ab9
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "84509197"
---
# <a name="entities"></a>實體

「實體」代表空間中的可移動物件，也是遠端轉譯內容的基本建置區塊。

## <a name="entity-properties"></a>實體屬性

實體具有由位置、旋轉和調整定義的轉換。 實體本身的沒有任何可觀察的功能。 相反地，行為是透過附加至實體的元件來新增。 比方說，附加 [CutPlaneComponent](../overview/features/cut-planes.md) 會在實體的位置建立切割平面。

實體本身最重要的一點是階層和產生的階層式轉換。 例如，當多個實體附加為共用父實體的子系時，可以藉由變更父實體的轉換，來移動、旋轉和調整所有實體。

實體是由其父系唯一擁有的，這表示當父系使用 `Entity.Destroy()` 終結時，其子系和所有連線的[元件](components.md)都會終結。 因此，從場景中移除模型是藉由在模型的根節點上呼叫 `Destroy` 來完成，並由 `AzureSession.Actions.LoadModelAsync()` 或其 SAS 變體 `AzureSession.Actions.LoadModelFromSASAsync()` 傳回。

當伺服器載入內容，或使用者想將物件新增至場景時，會建立實體。 例如，如果使用者想要新增切割平面以將網格的內部視覺化，可以建立一個應該存在的平面，然後在其中新增「切割平面」元件。

## <a name="create-an-entity"></a>建立實體

若要將新實體新增至場景，例如，若要將它當做載入模型的根物件傳遞，或將元件附加至它，請使用下列程式碼：

```cs
Entity CreateNewEntity(AzureSession session)
{
    Entity entity = session.Actions.CreateEntity();
    entity.Position = new LocalPosition(1, 2, 3);
    return entity;
}
```

```cpp
ApiHandle<Entity> CreateNewEntity(ApiHandle<AzureSession> session)
{
    ApiHandle<Entity> entity(nullptr);
    if (auto entityRes = session->Actions()->CreateEntity())
    {
        entity = entityRes.value();
        entity->Position(Double3{ 1, 2, 3 });
        return entity;
    }
    return entity;
}
```

## <a name="query-functions"></a>查詢函式

實體上有兩種類型的查詢函式，分別是同步和非同步呼叫。 同步查詢只可用於用戶端上的資料，而不需要大量計算。 範例包括查詢元件、相對物件轉換或父系/子系關聯性。 非同步查詢用於僅位於伺服器上的資料，或牽涉到在用戶端上執行太多資源的額外計算。 範例包括空間界限查詢或中繼資料查詢。

### <a name="querying-components"></a>查詢元件

若要尋找特定類型的元件，請使用 `FindComponentOfType`：

```cs
CutPlaneComponent cutplane = (CutPlaneComponent)entity.FindComponentOfType(ObjectType.CutPlaneComponent);

// or alternatively:
CutPlaneComponent cutplane = entity.FindComponentOfType<CutPlaneComponent>();
```

```cpp
ApiHandle<CutPlaneComponent> cutplane = entity->FindComponentOfType(ObjectType::CutPlaneComponent)->as<CutPlaneComponent>();

// or alternatively:
ApiHandle<CutPlaneComponent> cutplane = *entity->FindComponentOfType<CutPlaneComponent>();
```

### <a name="querying-transforms"></a>查詢轉換

轉換查詢是物件上的同步呼叫。 請務必注意，透過 API 查詢的轉換是與物件父系相對的本機空間轉換。 根物件為例外狀況，其本機空間和世界空間都相同。

> [!NOTE]
> 沒有專用的 API 可查詢任意物件的世界空間轉換。

```cs
// local space transform of the entity
Double3 translation = entity.Position;
Quaternion rotation = entity.Rotation;
```

```cpp
// local space transform of the entity
Double3 translation = *entity->Position();
Quaternion rotation = *entity->Rotation();
```


### <a name="querying-spatial-bounds"></a>查詢空間界限

界限查詢是在完整物件階層上運作的非同步呼叫，會使用一個實體做為根。 請參閱有關[物件界限](object-bounds.md)的專屬章節。

### <a name="querying-metadata"></a>查詢中繼資料

中繼資料是指儲存在物件上的其他資料，伺服器會忽略此資訊。 物件中繼資料基本上是一組 (名稱、值)配對，其中「值」可以是數值、布林值或字串類型。 中繼資料可以與模型一起匯出。

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

```cpp
ApiHandle<MetadataQueryAsync> metaDataQuery = *entity->QueryMetaDataAsync();
metaDataQuery->Completed([](const ApiHandle<MetadataQueryAsync>& query)
    {
        if (query->IsRanToCompletion())
        {
            ApiHandle<ObjectMetaData> metaData = *query->Result();
            ApiHandle<ObjectMetaDataEntry> entry = *metaData->GetMetadataByName("MyInt64Value");
            int64_t intValue = *entry->AsInt64();

            // ...
        }
    });
```

即使物件未包含任何中繼資料，查詢也會成功。

## <a name="next-steps"></a>後續步驟

* [Components](components.md)
* [物件界限](object-bounds.md)
