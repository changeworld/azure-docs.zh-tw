---
title: 設定模型轉換
description: 所有模型轉換參數的說明
author: florianborn71
ms.author: flborn
ms.date: 03/06/2020
ms.topic: how-to
ms.openlocfilehash: eb287b812c477b2e472c48d7bd8f44574a398bac
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/06/2020
ms.locfileid: "80681567"
---
# <a name="configure-the-model-conversion"></a>設定模型轉換

本章記錄模型轉換的選項。

## <a name="settings-file"></a>設定檔案

如果在輸入模型旁邊的`ConversionSettings.json`輸入容器中找到調用的檔,則用於為模型轉換過程提供其他配置。

檔案的內容應滿足以下 json 架構:

```json
{
    "$schema" : "http://json-schema.org/schema#",
    "description" : "ARR ConversionSettings Schema",
    "type" : "object",
    "properties" :
    {
        "scaling" : { "type" : "number", "exclusiveMinimum" : 0, "default" : 1.0 },
        "recenterToOrigin" : { "type" : "boolean", "default" : false },
        "opaqueMaterialDefaultSidedness" : { "type" : "string", "enum" : [ "SingleSided", "DoubleSided" ], "default" : "DoubleSided" },
        "material-override" : { "type" : "string", "default" : "" },
        "gammaToLinearMaterial" : { "type" : "boolean", "default" : false },
        "gammaToLinearVertex" : { "type" : "boolean", "default" : false },
        "sceneGraphMode": { "type" : "string", "enum" : [ "none", "static", "dynamic" ], "default" : "dynamic" },
        "generateCollisionMesh" : { "type" : "boolean", "default" : true },
        "unlitMaterials" : { "type" : "boolean", "default" : false },
        "fbxAssumeMetallic" : { "type" : "boolean", "default" : true },
        "axis" : {
            "type" : "array",
            "items" : {
                "type" : "string",
                "enum" : ["default", "+x", "-x", "+y", "-y", "+z", "-z"]
            },
            "minItems": 3,
            "maxItems": 3
        }
    },
    "additionalProperties" : false
}
```

範例`ConversionSettings.json`檔案可能是:

```json
{
    "scaling" : 0.01,
    "recenterToOrigin" : true,
    "material-override" : "box_materials_override.json"
}
```

### <a name="geometry-parameters"></a>幾何參數

* `scaling`- 此參數均勻縮放模型。 縮放可用於增大或收縮模型,例如,在桌面上顯示建築模型。 由於渲染引擎期望以米為單位指定長度,因此當以不同單位定義模型時,將出現此參數的另一個重要用途。 例如,如果以釐米為單位定義模型,則應用 0.01 的比例應以正確的大小呈現模型。
某些源資料格式(例如 .fbx)提供單位縮放提示,在這種情況下,轉換隱式將模型縮放到儀錶單位。 源格式提供的隱式縮放將應用於縮放參數之上。
最終縮放因數應用於場景圖形節點的幾何頂點和局部變換。 根實體的轉換的縮放保持未修改。

* `recenterToOrigin`- 指出應轉換模型,以便其邊界框以原點為中心。
如果源模型遠離原點,則居中非常重要,因為在這種情況下,浮點精度問題可能會導致渲染偽影。

* `opaqueMaterialDefaultSidedness`- 渲染引擎假定不透明材質是雙面材質。
如果這不是預期的行為,則應將此參數設置為「單面」。 有關詳細資訊,請參閱[單面渲染](../../overview/features/single-sided-rendering.md)。

### <a name="material-overrides"></a>材料覆蓋

* `material-override`- 此參數允許在[轉換過程中定製](override-materials.md)材料的處理。

### <a name="color-space-parameters"></a>色彩空間參數

渲染引擎期望顏色值在線性空間中。
如果使用 Gamma 空間定義模型,則應將這些選項設置為 true。

* `gammaToLinearMaterial`- 將材料顏色從伽馬空間轉換為線性空間
* `gammaToLinearVertex`- 將頂點顏色從伽馬空間轉換為線性空間

> [!NOTE]
> 對於 FBX 檔,默認`true`情況下這些 設置設置為。 對所有其他檔案類型,預設值為`false`。

### <a name="scene-parameters"></a>場景參數

* `sceneGraphMode`- 定義來源的場景圖的轉換方式:
  * `dynamic`(預設):檔案中的所有物件都作為 API 中的[實體](../../concepts/entities.md)公開,可以獨立轉換。 運行時的節點層次結構與源檔中的結構相同。
  * `static`:所有物件都在 API 中公開,但不能獨立轉換。
  * `none`:場景圖摺疊成一個物件。

每個模式具有不同的運行時性能。 在`dynamic`模式下,即使未移動零件,性能成本也會與圖形中的[實體](../../concepts/entities.md)數線性縮放。 僅當應用程式需要單獨行動零件時,才應使用它,例如對於「爆炸檢視」動畫。

模式`static`匯出完整場景圖,但此圖形中的零件相對於其根部分具有恆定變換。 但是,仍可以移動、旋轉或縮放物件的根節點,無需顯著的性能成本。 此外,[空間查詢](../../overview/features/spatial-queries.md)將返回單個部分,每個部分可以通過[狀態重寫](../../overview/features/override-hierarchical-state.md)進行修改。 使用此模式,每個物件的運行時開銷可以忽略不計。 它非常適合需要每個物件檢查但無需每個物件轉換更改的大型場景。

該`none`模式具有最少的運行時開銷,並且載入時間稍好。 在此模式下,無法檢查或轉換單個物件。 例如,用例是照片測量模型,它們最初沒有有意義的場景圖。

> [!TIP]
> 許多應用程式將載入多個模型。 您應該根據所使用的方式優化每個模型的轉換參數。 例如,如果要顯示汽車模型,以便使用者分崩離析並進行詳細檢查,則需要使用`dynamic`模式進行轉換。 但是,如果您還希望將汽車放置在展示室環境中,則可以將該車型轉換為`sceneGraphMode``static`設置為或`none`甚至 。

### <a name="physics-parameters"></a>物理參數

* `generateCollisionMesh`- 如果需要支援模型上[的空間查詢](../../overview/features/spatial-queries.md),必須啟用此選項。 在最壞的情況下,創建衝突網格可以使轉換時間加倍。 具有衝突同流器的模型載入時間較長,使用`dynamic`場景圖時,它們的運行時性能開銷也較高。 為了獲得總體最佳性能,您應該在所有不需要空間查詢的模型上禁用此選項。

### <a name="unlit-materials"></a>未點燃的材料

* `unlitMaterials`- 預設情況下,轉換將傾向於創建[PBR 材料](../../overview/features/pbr-materials.md)。 這個選項告訴轉換器將所有材料視為[顏色材料](../../overview/features/color-materials.md)。 如果您的資料已包含照明,例如透過攝影測量建立的模型,這個選項允許您快速對所有材質強制實施正確的轉換,而無需單獨[涵蓋每個材質](override-materials.md)。

### <a name="converting-from-older-fbx-formats-with-a-phong-material-model"></a>使用 Phong 材料模型從較舊的 FBX 格式進行轉換

* `fbxAssumeMetallic`- 較舊版本的 FBX 格式使用 Phong 材料模型定義其材料。 轉換過程必須推斷這些材料如何映射到渲染器的[PBR 模型](../../overview/features/pbr-materials.md)。 通常這工作得很好,但當材質沒有紋理、高鏡面值和非灰色反光度顏色時,可能會出現歧義。 在這種情況下,轉換必須在確定高鏡面值的優先順序、定義高反射性金屬材料(使反光度顏色溶解的地方)或確定反光度顏色的優先順序、定義像閃亮的彩色塑膠一樣的東西之間做出選擇。 默認情況下,轉換過程假定高鏡面值意味著在模稜兩可的情況下的金屬材料。 可以將其參數設置為`false`切換到相反。

### <a name="coordinate-system-overriding"></a>座標系重寫

* `axis`- 覆蓋座標系單位向量。 預設值為`["+x", "+y", "+z"]`。 理論上,FBX 格式具有一個標頭,其中定義了這些向量,轉換使用該資訊來轉換場景。 glTF 格式還定義了固定座標系。 實際上,某些資產的標頭中的資訊不正確,或者使用不同的座標系約定保存。 這個選項允許您覆蓋座標系以進行補償。 例如:`"axis" : ["+x", "+z", "-y"]`透過反轉 Y 軸方向來交換 Z 軸和 Y 軸並保持座標系的傳遞。

### <a name="vertex-format"></a>頂點格式

可以調整網格的頂點格式,以交換精度以節省記憶體。 較低的記憶體佔用空間使您能夠載入更大的模型或實現更好的性能。 但是,根據數據的不同,錯誤的格式可能會顯著影響渲染品質。

> [!CAUTION]
> 當模型不再適合記憶體時,或者優化以獲得最佳性能時,更改頂點格式應該是最後的手段。 更改可以輕鬆地引入渲染偽影,無論是明顯的,也可以是細微的。 除非您知道要查找什麼,否則不應更改預設值。

這些調整是可能的:

* 可以顯式包含或排除特定數據流。
* 可以降低數據流的準確性,以減少記憶體佔用。

檔中的`vertex`以下部分是可`.json`選的。 對於未顯式指定的每個部分,轉換服務將回退到其預設設置。

```json
{
    ...
    "vertex" : {
        "position"  : "32_32_32_FLOAT",
        "color0"    : "NONE",
        "color1"    : "NONE",
        "normal"    : "NONE",
        "tangent"   : "NONE",
        "binormal"  : "NONE",
        "texcoord0" : "32_32_FLOAT",
        "texcoord1" : "NONE"
    },
    ...
```

通過強制元件 到`NONE`,可以保證輸出網格沒有相應的流。

#### <a name="component-formats-per-vertex-stream"></a>每個頂點流的元件格式

這些格式允許用於相應的元件:

| 頂點元件 | 支援的格式 (粗體= 預設值) |
|:-----------------|:------------------|
|position| **32_32_32_FLOAT**, 16_16_16_16_FLOAT |
|顏色0| **8_8_8_8_UNSIGNED_NORMALIZED**, 無 |
|顏色1| 8_8_8_8_UNSIGNED_NORMALIZED,**無**|
|正常| **8_8_8_8_SIGNED_NORMALIZED**, 16_16_16_16_FLOAT, 無 |
|正切函數| **8_8_8_8_SIGNED_NORMALIZED**, 16_16_16_16_FLOAT, 無 |
|雙正態| **8_8_8_8_SIGNED_NORMALIZED**, 16_16_16_16_FLOAT, 無 |
|特克斯科德0| **32_32_FLOAT**, 16_16_FLOAT, 無 |
|特克斯科德1| **32_32_FLOAT**, 16_16_FLOAT, 無 |

#### <a name="supported-component-formats"></a>支援的元件格式

格式的記憶體佔用空間如下:

| [格式] | 描述 | 每個頂點的位元組數 |
|:-------|:------------|:---------------|
|32_32_FLOAT|雙組分全浮點精度|8
|16_16_FLOAT|雙組分半浮點精度|4
|32_32_32_FLOAT|三個元件全浮點精度|12
|16_16_16_16_FLOAT|四組半浮點精度|8
|8_8_8_8_UNSIGNED_NORMALIZED|四個分量位元組,規範化`[0; 1]`為範圍|4
|8_8_8_8_SIGNED_NORMALIZED|四個分量位元組,規範化`[-1; 1]`為範圍|4

#### <a name="best-practices-for-component-format-changes"></a>元件格式變更的最佳做法

* `position`:精度降低就足夠了,這是罕見的。 **16_16_16_16_FLOAT**引入了明顯的量化偽影,即使是對於小型模型也是如此。
* `normal`,`tangent``binormal`通常這些值一起更改。 除非有來自正常量化產生的明顯的照明偽影,否則沒有理由提高其精度。 但是,在某些情況下,這些元件可以設置為**NONE:**
  * `normal`,`tangent``binormal`並且僅在模型中至少應點亮一個材質時才需要。 在 ARR 中,當模型上隨時使用[PBR 材料](../../overview/features/pbr-materials.md)時,就是這種情況。
  * `tangent``binormal`並且僅在任何照明材質使用法線貼圖紋理時才需要。
* `texcoord0`:`texcoord1`紋理座標在值保持`[0; 1]`在 範圍內且定址紋理的最大大小為 2048 x 2048 像素時,可以使用較低的精度 **(16_16_FLOAT**)。 如果超過這些限制,紋理映射的品質將受到影響。

#### <a name="example"></a>範例

假設您有一個攝影測量模型,該模型將照明烘焙到紋理中。 渲染模型所需的全部是頂點位置和紋理座標。

預設情況下,轉換器必須假定您可能希望在某個時間在模型上使用 PBR 材質,以便它將為`normal`您`tangent`生成`binormal`、 和數據。 因此,每個頂點記憶體的`position`用量 是 (12`texcoord0`位元組)`normal`= (8 位元組) = `tangent` (4 位元組) `binormal` = (4 位元組) = (4 位元組) = 32 位元組。 這種類型的較大模型可以很容易地具有數百萬頂點,從而產生可能佔用多個千兆位元組記憶體的模型。 如此大量的數據將影響性能,您甚至可能耗盡記憶體。

知道您永遠不需要模型上的動態照明,`[0; 1]`並且知道所有紋理座標都在範圍內,您可以`normal`設置`tangent``binormal`、`NONE``texcoord0`和 到`16_16_FLOAT`和 到 到 一半 精度 (),因此每個頂點僅產生 16 個字節。 將格格數據減半使您能夠載入更大的模型,並可能提高性能。

## <a name="typical-use-cases"></a>一般使用案例

為給定用例查找良好的導入設置可能是一個繁瑣的過程。 另一方面,轉換設置可能對運行時性能產生重大影響。

某些類別的用例符合特定的優化條件。 下面給出了一些示例。

### <a name="use-case-architectural-visualization--large-outdoor-maps"></a>用例:建築視覺化/大型戶外地圖

* 這些類型的場景往往是靜態的,這意味著它們不需要可移動的部分。 因此,`sceneGraphMode`可以設置`static`為或`none`, 這提高了運行時效能。 使用`static`模式時,仍可以移動、旋轉和縮放場景的根節點,例如,在 1:1 比例(對於第一人稱視圖)和桌面視圖之間動態切換。

* 當您需要行動零件時,這通常也意味著您需要支援光線轉換或其他[空間查詢](../../overview/features/spatial-queries.md),以便可以首先選取這些零件。 另一方面,如果您不打算移動某些內容,則很可能也不需要它來參與空間查詢,因此可以關閉`generateCollisionMesh`標誌。 此交換機對轉換時間、載入時間和每幀更新成本的運行時也產生重大影響。

* 如果應用程式不使用[切割平面](../../overview/features/cut-planes.md),`opaqueMaterialDefaultSidedness`應關閉標誌。 性能提升通常為 20%-30%。 切割平面仍可以使用,但在查看對象的內部部分時不會有背面,這看起來有悖常理。 有關詳細資訊,請參閱[單面渲染](../../overview/features/single-sided-rendering.md)。

### <a name="use-case-photogrammetry-models"></a>用例:攝影測量模型

渲染攝影測量模型時,通常不需要場景圖,因此您可以將`sceneGraphMode`設定`none`為 。 由於這些模型很少包含複雜的場景圖,因此此選項的影響應該微不足道。

由於照明已烘焙到紋理中,因此無需動態照明。 因此：

* 將`unlitMaterials`旗標`true`設定為將所有材料轉換為未亮[的顏色材料](../../overview/features/color-materials.md)。
* 從頂點格式中刪除不需要的資料。 請參考上面[的範例](#example)。

### <a name="use-case-visualization-of-compact-machines-etc"></a>用例:緊湊型機器的可視化等。

在這些用例中,模型在小卷中通常具有非常高的細節。 渲染器經過大量優化,可很好地處理此類情況。 但是,上一個用例中提到的大多數優化都不適用於此處:

* 單個零件應可選擇且可移動,因此`sceneGraphMode`必須留給`dynamic`。
* 光線強制轉換通常是應用程式的組成部分,因此必須生成衝突套。
* 啟用了標誌后,`opaqueMaterialDefaultSidedness`切割平面看起來更好。

## <a name="next-steps"></a>後續步驟

* [模型轉換](model-conversion.md)
* [色彩材質](../../overview/features/color-materials.md)
* [PBR 材料](../../overview/features/pbr-materials.md)
* [模型轉換期間覆寫材料](override-materials.md)
