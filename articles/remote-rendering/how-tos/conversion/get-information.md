---
title: 取得轉換的相關資訊
description: 取得轉換的相關資訊
author: malcolmtyrrell
ms.author: matyrr
ms.date: 03/05/2020
ms.topic: how-to
ms.openlocfilehash: 89ec0ad40822785457e988cf9e0f9bd6d00ed81f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "91576620"
---
# <a name="get-information-about-conversions"></a>取得轉換的相關資訊

## <a name="information-about-a-conversion-the-result-file"></a>轉換的相關資訊：結果檔

當轉換服務轉換資產時，會將任何問題的摘要寫入「結果檔案」。 例如，如果檔案 `buggy.gltf` 已轉換，輸出容器將會包含名為的檔案 `buggy.result.json` 。

結果檔會列出轉換期間發生的任何錯誤和警告，並提供結果摘要，也就是或的其中一個 `succeeded` `failed` `succeeded with warnings` 。
結果檔案會結構化為物件的 JSON 陣列，其中每一個都有一個字串屬性，它是、、、和的其中一個 `warning` `error` `internal warning` `internal error` `result` 。 最多隻會有一個錯誤 (`error` 或 `internal error`) ，而且一律會有一個錯誤 `result` 。

## <a name="example-result-file"></a>範例 *結果* 檔

下列範例說明成功產生 arrAsset 的轉換。 不過，因為缺少材質，所以產生的 arrAsset 可能不會如預期。

```JSON
[
  {"warning":"4004","title":"Missing texture","details":{"texture":"buggy_baseColor.png","material":"buggy_col"}},
  {"result":"succeeded with warnings"}
]
```

## <a name="information-about-a-converted-model-the-info-file"></a>已轉換模型的相關資訊：資訊檔案

轉換服務所產生的 arrAsset 檔案僅供轉譯服務取用。 但是，如果您想要在不啟動轉譯會話的情況下存取模型的相關資訊，有時可能會發生。 為了支援此工作流程，轉換服務會將 JSON 檔案放在輸出容器中的 arrAsset 檔案旁邊。 例如，如果檔案 `buggy.gltf` 已轉換，則輸出容器將包含在已 `buggy.info.json` 轉換的資產旁邊呼叫的檔案 `buggy.arrAsset` 。 它包含來源模型的相關資訊、轉換過的模型，以及關於轉換本身的資訊。

## <a name="example-info-file"></a>範例 *資訊* 檔案

以下是藉由轉換名為的檔案所產生的範例 *資訊* 檔案 `buggy.gltf` ：

```JSON
{
    "files": {
        "input": "Buggy.gltf"
    },
    "conversionSettings": {
        "recenterToOrigin": true
    },
    "inputInfo": {
        "sourceAssetExtension": ".gltf",
        "sourceAssetFormat": "glTF2 Importer",
        "sourceAssetFormatVersion": "2.0",
        "sourceAssetGenerator": "COLLADA2GLTF"
    },
    "inputStatistics": {
        "numMeshes": 148,
        "numFaces": 308306,
        "numVertices": 245673,
        "numMaterial": 149,
        "numFacesSmallestMesh": 2,
        "numFacesBiggestMesh": 8764,
        "numNodes": 206,
        "numMeshUsagesInScene": 236,
        "maxNodeDepth": 3
    },
    "materialOverrides": {
        "numOverrides": 4,
        "numOverriddenMaterials": 4
    },
    "outputInfo": {
        "conversionToolVersion": "3b28d840de9916f9d628342f474d38c3ab949590",
        "conversionHash": "CCDB1F7A4C09F565"
    },
    "outputStatistics": {
        "numMeshPartsCreated": 236,
        "numMeshPartsInstanced": 88,
        "recenteringOffset": [
            -24.1,
            -50.9,
            -16.5974
        ],
        "boundingBox": {
            "min": [
                -43.52,
                -61.775,
                -79.6416
            ],
            "max": [
                43.52,
                61.775,
                79.6416
            ]
        }
    }
}
```

## <a name="information-in-the-info-file"></a>資訊檔中的資訊

### <a name="the-files-section"></a>*Files*區段

本節包含所提供的檔案名。

* `input`：原始程式檔的名稱。
* `output`：當使用者指定非預設名稱時，輸出檔的名稱。

### <a name="the-conversionsettings-section"></a>*ConversionSettings*區段

此區段包含轉換模型時所指定之 [ConversionSettings](configure-model-conversion.md#settings-file) 的複本。

### <a name="the-inputinfo-section"></a>*InputInfo*區段

此區段會記錄來源檔案格式的相關資訊。

* `sourceAssetExtension`：原始程式檔的副檔名。
* `sourceAssetFormat`：來源檔案格式的描述。
* `sourceAssetFormatVersion`：來源檔案格式的版本。
* `sourceAssetGenerator`：產生原始程式檔的工具名稱（如果有的話）。

### <a name="the-materialoverrides-section"></a>*MaterialOverrides*區段

本節提供將材質覆寫檔案提供給轉換服務時， [材質覆寫](override-materials.md) 的相關資訊。
其包含下列資訊：
* `numOverrides`：從材質覆寫檔案讀取的覆寫專案數目。
* `numOverriddenMaterials`：已覆寫的材質數目。

### <a name="the-inputstatistics-section"></a>*InputStatistics*區段

本節提供來源場景的相關資訊。 此區段中的值與建立來源模型的工具中的對等值之間通常會有不一致的情況。 這類差異是預期的，因為模型會在匯出和轉換步驟期間進行修改。

* `numMeshes`：網格零件的數目，其中每個部分都可以參考單一材質。
* `numFaces`：整個模型中的 _三角形_ 總數。 請注意，在轉換期間，網格會被三角化。 此數目會對 [標準轉譯伺服器大小](../../reference/vm-sizes.md#how-the-renderer-evaluates-the-number-of-polygons)的多邊形限制有貢獻。
* `numVertices`：整個模型中的總頂點數。
* `numMaterial`：整個模型中的總材質數。
* `numFacesSmallestMesh`：模型最小網格中的三角形數目。
* `numFacesBiggestMesh`：模型的最大網格中的三角形數目。
* `numNodes`：模型場景圖形中的節點數目。
* `numMeshUsagesInScene`：節點參考網格的次數。 一個以上的節點可以參考相同的網格。
* `maxNodeDepth`：場景圖形內節點的最大深度。

### <a name="the-outputinfo-section"></a>*OutputInfo*區段

此區段會記錄有關產生之輸出的一般資訊。

* `conversionToolVersion`：模型轉換器的版本。
* `conversionHash`： ArrAsset 中可參與轉譯的資料雜湊。 可以用來瞭解轉換服務是否在相同檔案上重新執行時，產生不同的結果。

### <a name="the-outputstatistics-section"></a>*OutputStatistics*區段

此區段會記錄從已轉換的資產計算的資訊。

* `numMeshPartsCreated`： ArrAsset 中的網格數目。 它可能與 `numMeshes` 一節中的不同 `inputStatistics` ，因為實例會受到轉換程式的影響。
* `numMeshPartsInstanced`： ArrAsset 中重複使用的網格數目。
* `recenteringOffset`：當 `recenterToOrigin` [ConversionSettings](configure-model-conversion.md) 中的選項啟用時，此值是將轉換的模型移回其原始位置的轉譯。
* `boundingBox`：模型的界限。

## <a name="deprecated-features"></a>即將淘汰的功能

轉換服務會將檔案 `stdout.txt` 和 `stderr.txt` 輸出容器寫入，而這些檔案是唯一的警告和錯誤來源。
這些檔案現在已被取代。 相反地，請將 [結果](#information-about-a-conversion-the-result-file) 檔用於此用途。

## <a name="next-steps"></a>後續步驟

* [模型轉換](model-conversion.md)
* [設定模型轉換](configure-model-conversion.md)
