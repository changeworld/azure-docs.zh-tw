---
title: 取得有關轉換模型的資訊
description: 所有模型轉換參數的說明
author: malcolmtyrrell
ms.author: matyrr
ms.date: 03/05/2020
ms.topic: how-to
ms.openlocfilehash: d5f843add0649682bae8c472bc50b6beea33bf93
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/06/2020
ms.locfileid: "80681515"
---
# <a name="get-information-about-a-converted-model"></a>取得有關轉換模型的資訊

轉換服務生成的 arrAsset 檔僅用於呈現服務使用。 但是,有時您可能希望在不啟動呈現會話的情況下訪問有關模型的資訊。 因此,轉換服務在輸出容器中的 arrAsset 檔旁邊放置一個 JSON 檔。 例如,如果轉換了檔`buggy.gltf`,輸出容器將包含一個在轉換后的`buggy.info.json``buggy.arrAsset`資產 旁邊稱為的檔。 它包含有關源模型、轉換模型和轉換本身的資訊。

## <a name="example-info-file"></a>*範例資訊*檔案

下面是透過轉換檔名 *:* `buggy.gltf`

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

## <a name="information-in-the-info-file"></a>資訊檔案中的資訊

### <a name="the-files-section"></a>*檔案*部份

本節包含提供的檔名。

* `input`:源文件的名稱。
* `output`:當使用者指定非預設名稱時,輸出檔的名稱。

### <a name="the-conversionsettings-section"></a>*轉換設定*部份

本節包含轉換模型時指定的[轉換設置](configure-model-conversion.md#settings-file)的副本。

### <a name="the-inputinfo-section"></a>*輸入資訊*部份

本節記錄有關源檔格式的資訊。

* `sourceAssetExtension`:源檔的文件副檔名。
* `sourceAssetFormat`:源檔案格式的說明。
* `sourceAssetFormatVersion`:源檔案格式的版本。
* `sourceAssetGenerator`:生成源檔的工具的名稱(如果可用)。

### <a name="the-inputstatistics-section"></a>*輸入統計資訊*部份

本節提供有關源場景的資訊。 本節中的值與創建源模型的工具中的等效值之間通常存在差異。 這種差異是預料之中的,因為模型在匯出和轉換步驟期間被修改。

* `numMeshes`:網格零件的數量,其中每個零件可以引用單個材質。
* `numFaces`:整個模型中的_三角形_總數。 請注意,網格在轉換期間進行三角化。
* `numVertices`:整個模型中的頂點總數。
* `numMaterial`:整個模型中的材料總數。
* `numFacesSmallestMesh`:模型最小網格中的三角形數。
* `numFacesBiggestMesh`:模型最大網格中的三角形數。
* `numNodes`:模型場景圖中的節點數。
* `numMeshUsagesInScene`:節點引用等號的次數。 多個節點可以引用同一網格。
* `maxNodeDepth`:場景圖中節點的最大深度。

### <a name="the-outputinfo-section"></a>*輸出資訊*部份

本節記錄有關生成的輸出的一般資訊。

* `conversionToolVersion`:模型轉換器的版本。
* `conversionHash`:arrAsset 中數據的哈希,有助於呈現。 可用於瞭解轉換服務在同一檔上重新運行時是否產生了不同的結果。

### <a name="the-outputstatistics-section"></a>*輸出統計*部份

本節記錄從轉換后的資產計算的資訊。

* `numMeshPartsCreated`:arrAsset 中的模子數。 它可以與`inputStatistics`部分`numMeshes`不同 ,因為實例化受轉換過程的影響。
* `numMeshPartsInstanced`:在 arrAsset 中重複使用的等件數。
* `recenteringOffset`:啟用`recenterToOrigin`[轉換設定](configure-model-conversion.md)中的選項后,此值是將轉換后的模型移回其原始位置的平移。
* `boundingBox`:模型的邊界。

## <a name="next-steps"></a>後續步驟

* [模型轉換](model-conversion.md)
* [設定模型轉換](configure-model-conversion.md)
