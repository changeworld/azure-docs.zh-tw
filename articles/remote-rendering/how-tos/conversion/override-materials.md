---
title: 在模型轉換期間覆寫材質
description: 說明在轉換時間覆寫工作流程的材質
author: florianborn71
ms.author: flborn
ms.date: 02/13/2020
ms.topic: how-to
ms.openlocfilehash: 11bd79a1bc88d2605a20744f5a6b6536d754c100
ms.sourcegitcommit: a422b86148cba668c7332e15480c5995ad72fa76
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/30/2020
ms.locfileid: "91576637"
---
# <a name="override-materials-during-model-conversion"></a>在模型轉換期間覆寫材質

來源模型中的材質設定會用來定義轉譯器所使用的 [.pbr 材質](../../overview/features/pbr-materials.md) 。
有時候 [預設轉換](../../reference/material-mapping.md) 並不會提供所需的結果，因此您需要進行變更。
當模型轉換為用於 Azure 遠端轉譯時，您可以提供材質覆寫檔案，以自訂每個材質的材質轉換如何完成。
如果在 `<modelName>.MaterialOverrides.json` 輸入模型旁邊的輸入容器中找到名為的檔案 `<modelName>.<ext>` ，則會使用它作為材質覆寫檔案。

## <a name="the-override-file-used-during-conversion"></a>轉換期間使用的覆寫檔案

簡單的範例是，假設方塊模型有稱為「預設」的單一材質。
此外，假設其 >albedo 色彩需要調整以用於 ARR。
在此情況下，您 `box.MaterialOverrides.json` 可以依照下列方式建立檔案：

```json
[
    {
        "name": "Default",
        "albedoColor": {
            "r": 0.33,
            "g": 0.33,
            "b": 0.33,
            "a": 1.0
        }
    }
]
```

檔案 `box.MaterialOverrides.json` 會放在旁邊的輸入容器中 `box.fbx` ，這會指示轉換服務套用新的設定。

### <a name="color-materials"></a>色彩材質

[色彩材質](../../overview/features/color-materials.md)模型描述與光源無關的持續陰影表面。
例如，色彩材質適用于攝影測量演算法所建立的資產。
在材質覆寫檔案中，您可以藉由設定為，將材質宣告為色彩材質 `unlit` `true` 。

```json
[
    {
        "name": "Photogrametry_mat1",
        "unlit" : true
    },
    {
        "name": "Photogrametry_mat2",
        "unlit" : true
    }
]
```

### <a name="ignore-specific-texture-maps"></a>略過特定紋理對應

有時您可能會想要讓轉換程式忽略特定的材質對應。 當您的模型是由產生轉譯器無法正確理解的特殊對應的工具所產生時，就可能會發生這種情況。 例如，用來定義不透明度以外之內容的 "OpacityMap"，或 "NormalMap" 儲存為 "BumpMap" 的模型。  (在第二個案例中，您想要忽略 "NormalMap"，這會造成轉換器使用 "BumpMap" 作為 "NormalMap"。 ) 

準則很簡單。 只要新增名為的屬性 `ignoreTextureMaps` ，並加入您想要忽略的任何材質對應：

```json
[
    {
        "name": "Default",
        "ignoreTextureMaps": ["OpacityMap", "NormalMap"]
    }
]
```

如需您可以忽略之材質對應的完整清單，請參閱下面的 JSON 架構。

### <a name="applying-the-same-overrides-to-multiple-materials"></a>將相同的覆寫套用至多個材質

依預設，材質覆寫檔案中的專案會在其名稱完全符合材質名稱時套用。
因為相同的覆寫應該適用于多個資料，所以您可以選擇性地提供正則運算式做為專案名稱。
欄位 `nameMatching` 具有預設值 `exact` ，但可以設定為， `regex` 以指出專案應套用至每個相符的材質。
使用的語法與用於 JavaScript 的語法相同。 下列範例顯示的覆寫適用于名稱類似 "Material2"、"Material01" 和 "Material999" 的材質。

```json
[
    {
        "name": "Material[0-9]+",
        "nameMatching": "regex",
        "albedoColor": {
            "r": 0.0,
            "g": 0.0,
            "b": 1.0,
            "a": 1.0
        }
    }
]
```

材質覆寫檔案中最多隻會有一個專案套用至單一材質。
如果有完全相符的 (亦即 `nameMatching` 不存在或等於 `exact`) 的材質名稱，則會選擇該專案。
否則，會選擇檔案中符合材質名稱的第一個 RegEx 專案。

### <a name="getting-information-about-which-entries-applied"></a>取得已套用專案的相關資訊

寫入至輸出容器的 [資訊](get-information.md#information-about-a-converted-model-the-info-file) 檔案會提供所提供覆寫數目的相關資訊，以及已覆寫的材質數目。

## <a name="json-schema"></a>JSON 結構描述

本檔提供完整的材質檔案 JSON 架構。 除了 `unlit` 和以外 `ignoreTextureMaps` ，可用的屬性是 [色彩材質](../../overview/features/color-materials.md) 和 [.pbr 材質](../../overview/features/pbr-materials.md) 模型各節所述的屬性子集。

```json
{
    "definitions" :
    {
        "color":
        {
            "type" : "object",
            "description" : "Color as 4 components vector",
            "properties":
            {
                "r": {"type":"number"},
                "g": {"type":"number"},
                "b": {"type":"number"},
                "a": {"type":"number"}
            },
            "required": ["r", "g", "b"]
        },
        "alpha":
        {
            "type" : "object",
            "description" : "Alpha channel for color",
            "properties":
            {
                "a": {"type":"number"}
            },
            "required": ["a"]
        },
        "colorOrAlpha":
        {
            "anyOf": [
                {"$ref": "#/definitions/color"},
                {"$ref": "#/definitions/alpha"}
            ]
        },
        "listOfMaps":
        {
            "type": "array",
            "items": {
                "type": "string",
                "enum": ["AlbedoMap",
                            "EmissiveMap",
                            "NormalMap",
                            "OcclusionMap",
                            "RoughnessMap",
                            "MetalnessMap",
                            "ReflectivityMap",
                            "BumpMap",
                            "OpacityMap",
                            "DiffuseMap",
                            "SpecularMap",
                            "ShininessMap",
                            "MetallicRoughnessMap",
                            "SpecularGlossinessMap"]
            }
        }
    },
    "type" : "array",
    "description" : "List of materials to override",
    "items":
    {
        "type" : "object",
        "description" : "List of parameters to override",
        "properties":
        {
            "name": { "type" : "string"},
            "nameMatching" : { "type" : "string", "enum" : ["exact", "regex"] },
            "unlit": { "type" : "boolean" },
            "albedoColor": { "$ref": "#/definitions/colorOrAlpha" },
            "roughness": { "type": "number" },
            "metalness": { "type": "number" },
            "transparent": { "type" : "boolean" },
            "alphaClipEnabled": { "type" : "boolean" },
            "alphaClipThreshold": { "type": "number" },
            "useVertexColor": { "type" : "boolean" },
            "isDoubleSided": { "type" : "boolean" },
            "ignoreTextureMaps": { "$ref" : "#/definitions/listOfMaps" }
        },
        "required": ["name"],
        "additionalProperties" : false
    }
}
```

## <a name="next-steps"></a>後續步驟

* [色彩材質](../../overview/features/color-materials.md)
* [PBR 材質](../../overview/features/pbr-materials.md)
