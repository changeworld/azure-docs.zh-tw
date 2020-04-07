---
title: 模型轉換期間覆寫材料
description: 解釋轉換時材料重寫工作流
author: florianborn71
ms.author: flborn
ms.date: 02/13/2020
ms.topic: how-to
ms.openlocfilehash: 90653db4c572877a728964851a99beebf2e823a4
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/06/2020
ms.locfileid: "80681476"
---
# <a name="override-materials-during-model-conversion"></a>模型轉換期間覆寫材料

在轉換過程中,源模型中的材料設定用於定義渲染器使用的[PBR 材質](../../overview/features/pbr-materials.md)。
有時,[預設轉換](../../reference/material-mapping.md)不會提供所需的結果,您需要進行更改。
轉換模型以在 Azure 遠端渲染中使用時,可以提供材質覆蓋檔,以自定義基於每個材質的材料轉換方式。
有關[配置模型轉換](configure-model-conversion.md)的部分具有聲明材質重寫檔名的說明。

## <a name="the-override-file-used-during-conversion"></a>轉換期間使用的覆寫檔案

作為一個簡單的示例,假設一個框模型具有單個材質,稱為"預設"。 需要調整色澤顏色以用於 ARR。
在這種情況下,可以按照如下`box_materials_override.json`方式建立檔:

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

該檔案`box_materials_override.json`放置在輸入容器中,並在`ConversionSettings.json``box.fbx`旁邊新增, 告訴轉換在哪裡可以找到重寫檔(請參考[設定模型轉換](configure-model-conversion.md)):

```json
{
    "material-override" : "box_materials_override.json"
}
```

轉換模型后,將應用新設置。

### <a name="color-materials"></a>色彩材質

[彩色材質](../../overview/features/color-materials.md)模型描述了獨立於照明的不斷著色表面。
例如,這對於攝影測量演演演算法所製作的資產很有用。
在材質覆蓋檔中,材質可以通過設置`unlit`為 聲明為顏色`true`材質。

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

### <a name="ignore-specific-texture-maps"></a>忽略特定的紋理貼圖

有時,您可能希望轉換過程忽略特定的紋理貼圖。 當模型由生成渲染器無法正確理解的特殊貼圖的工具生成時,可能就是這種情況。 例如,用於定義非數性以外的事物的"不一元性映射"或"正常映射"存儲為"BumpMap"的模型。 (在後一種情況下,您希望忽略"正常映射",這將導致轉換器使用"BumpMap"作為"正常地圖"。

原理很簡單。 只需新增一個稱為`ignoreTextureMaps`的屬性並加入要忽略的任何紋理貼圖:

```json
[
    {
        "name": "Default",
        "ignoreTextureMaps": ["OpacityMap", "NormalMap"]
    }
]
```

有關可以忽略的紋理貼圖的完整清單,請參閱下面的 JSON 架構。

## <a name="json-schema"></a>JSON 結構描述

此處給出了材質檔的完整 JSON 架構。 除`unlit``ignoreTextureMaps`和 外,可用的屬性是[顏色材質](../../overview/features/color-materials.md)和[PBR 材質](../../overview/features/pbr-materials.md)模型部分中描述的屬性的子集。

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
* [PBR 材料](../../overview/features/pbr-materials.md)