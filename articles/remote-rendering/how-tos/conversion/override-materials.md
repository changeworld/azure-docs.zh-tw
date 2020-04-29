---
title: 在模型轉換期間覆寫材質
description: 說明在轉換階段覆寫工作流程的材料
author: florianborn71
ms.author: flborn
ms.date: 02/13/2020
ms.topic: how-to
ms.openlocfilehash: 90653db4c572877a728964851a99beebf2e823a4
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "80681476"
---
# <a name="override-materials-during-model-conversion"></a>在模型轉換期間覆寫材質

在轉換期間，來源模型中的材質設定會用來定義轉譯器所使用的[.pbr 材質](../../overview/features/pbr-materials.md)。
有時候，[預設轉換](../../reference/material-mapping.md)不會提供所需的結果，因此您需要進行變更。
當模型轉換成在 Azure 遠端轉譯中使用時，您可以提供材質覆寫檔案，以自訂如何以每個材質為基礎來進行材質轉換。
設定[模型轉換](configure-model-conversion.md)的一節包含宣告材質覆寫檔案名的指示。

## <a name="the-override-file-used-during-conversion"></a>轉換期間使用的覆寫檔案

簡單的範例是，假設方塊模型有一個稱為「預設」的單一材質。 Albedo 色彩必須經過調整，才能在 ARR 中使用。
在此情況下， `box_materials_override.json`可以建立一個檔案，如下所示：

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

`box_materials_override.json`檔案會放在輸入容器中，並`ConversionSettings.json`在旁邊`box.fbx`加入，以指示轉換何處尋找覆寫檔案（請參閱設定[模型轉換](configure-model-conversion.md)）：

```json
{
    "material-override" : "box_materials_override.json"
}
```

轉換模型時，將會套用新的設定。

### <a name="color-materials"></a>色彩材質

[色彩材質](../../overview/features/color-materials.md)模型描述與光源無關的持續陰影表面。
例如，這適用于攝影測量演算法所製作的資產。
在材質覆寫檔案中，將設定`unlit`為`true`，可以將材質宣告為色彩材質。

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

### <a name="ignore-specific-texture-maps"></a>忽略特定材質對應

有時候，您可能會想要轉換程式忽略特定材質對應。 當您的模型是由產生轉譯器無法正確理解的特殊對應的工具產生時，就可能會發生這種情況。 例如，用來定義不透明度的「OpacityMap」，或「NormalMap」儲存為「BumpMap」的模型。 （在後者的情況下，您想要忽略 "NormalMap"，這會導致轉換器使用 "BumpMap" 作為 "NormalMap"）。

原則非常簡單。 直接新增名`ignoreTextureMaps`為的屬性，並新增您想要忽略的任何材質對應：

```json
[
    {
        "name": "Default",
        "ignoreTextureMaps": ["OpacityMap", "NormalMap"]
    }
]
```

如需您可以忽略之材質對應的完整清單，請參閱下面的 JSON 架構。

## <a name="json-schema"></a>JSON 結構描述

這裡提供材質檔案的完整 JSON 架構。 除了`unlit`和`ignoreTextureMaps`以外，可用的屬性是[色彩材質](../../overview/features/color-materials.md)和[.pbr 材質](../../overview/features/pbr-materials.md)模型各節中所述屬性的子集。

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