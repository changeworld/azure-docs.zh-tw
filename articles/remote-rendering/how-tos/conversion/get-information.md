---
title: 取得已轉換模型的相關資訊
description: 所有模型轉換參數的描述
author: malcolmtyrrell
ms.author: matyrr
ms.date: 03/05/2020
ms.topic: how-to
ms.openlocfilehash: f5c38ac88503416b37b720a091c9e46d819a3146
ms.sourcegitcommit: 54d8052c09e847a6565ec978f352769e8955aead
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/18/2020
ms.locfileid: "88509292"
---
# <a name="get-information-about-a-converted-model"></a>取得已轉換模型的相關資訊

轉換服務所產生的 arrAsset 檔案僅供轉譯服務取用。 但是，如果您想要在不啟動轉譯會話的情況下存取模型的相關資訊，有時可能會發生。 因此，轉換服務會將 JSON 檔案放在輸出容器中的 arrAsset 檔案旁邊。 例如，如果檔案 `buggy.gltf` 已轉換，則輸出容器將包含在已 `buggy.info.json` 轉換的資產旁邊呼叫的檔案 `buggy.arrAsset` 。 它包含來源模型的相關資訊、轉換過的模型，以及關於轉換本身的資訊。

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

## <a name="next-steps"></a>後續步驟

* [模型轉換](model-conversion.md)
* [設定模型轉換](configure-model-conversion.md)
