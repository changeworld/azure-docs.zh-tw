---
title: 設定模型轉換
description: 所有模型轉換參數的描述
author: florianborn71
ms.author: flborn
ms.date: 03/06/2020
ms.topic: how-to
ms.openlocfilehash: eb287b812c477b2e472c48d7bd8f44574a398bac
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "80681567"
---
# <a name="configure-the-model-conversion"></a>設定模型轉換

本章記載模型轉換的選項。

## <a name="settings-file"></a>設定檔案

如果在輸入模型`ConversionSettings.json`旁邊的輸入容器中找到名為的檔案，則會使用它來提供模型轉換程式的其他設定。

檔案的內容應該符合下列 json 架構：

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

範例`ConversionSettings.json`檔案可能是：

```json
{
    "scaling" : 0.01,
    "recenterToOrigin" : true,
    "material-override" : "box_materials_override.json"
}
```

### <a name="geometry-parameters"></a>Geometry 參數

* `scaling`-這個參數會一致地調整模型。 調整可以用來放大或縮小模型，例如在資料表上顯示建築物模型。 由於轉譯引擎預期會在計量中指定長度，因此當模型定義為不同的單位時，就會發生此參數的另一個重要用法。 例如，如果模型是以釐米定義，則套用尺規0.01 應該會以正確的大小呈現模型。
某些源資料格式（例如，fbx）提供單位調整提示，在這種情況下，轉換會隱含地將模型調整為計量單位。 來源格式所提供的隱含調整會套用在縮放參數的頂端。
最終的縮放比例會套用至 geometry 頂點和場景圖形節點的本機轉換。 根實體的轉換調整會保持未修改。

* `recenterToOrigin`-指出模型應該轉換，使其周框方塊在原點中央。
如果來源模型從原點中置換，則置中很重要，因為在這種情況下，浮點精確度問題可能會導致轉譯成品。

* `opaqueMaterialDefaultSidedness`-轉譯引擎會假設不透明材質為雙面。
如果這不是預期的行為，這個參數應該設定為 "SingleSided"。 如需詳細資訊，請參閱[單面](../../overview/features/single-sided-rendering.md)轉譯。

### <a name="material-overrides"></a>材質覆寫

* `material-override`-此參數允許在[轉換期間自訂](override-materials.md)材料的處理。

### <a name="color-space-parameters"></a>色彩空間參數

轉譯引擎會預期色彩值會線上性空間中。
如果模型是使用 gamma 空間來定義，則這些選項應該設定為 true。

* `gammaToLinearMaterial`-將材質色彩從 gamma 空間轉換成線性空間
* `gammaToLinearVertex`-將頂點色彩從 gamma 空間轉換成線性空間

> [!NOTE]
> 針對 FBX 檔案，這些設定預設會`true`設定為。 若為其他所有檔案類型，則預設`false`為。

### <a name="scene-parameters"></a>場景參數

* `sceneGraphMode`-定義如何轉換來源檔案中的場景圖形：
  * `dynamic`（預設值）：檔案中的所有物件都會公開為 API 中的[實體](../../concepts/entities.md)，而且可以獨立轉換。 執行時間的節點階層與來源檔案中的結構相同。
  * `static`：所有物件都會在 API 中公開，但無法獨立轉換。
  * `none`：場景圖形會折迭成一個物件。

每種模式都有不同的執行時間效能。 在`dynamic`模式中，效能成本會隨著圖形中的[實體](../../concepts/entities.md)數目呈線性調整，即使沒有任何部分移動也一樣。 它應該只在應用程式需要個別移動元件時使用，例如「分解視圖」動畫。

此`static`模式會匯出完整的場景圖形，但此圖形內的部分具有相對於其根部分的常數轉換。 不過，物件的根節點仍然可以移動、旋轉或縮放，而不會有顯著的效能成本。 此外，[空間查詢](../../overview/features/spatial-queries.md)會傳回個別的部分，而且每個元件都可以透過[狀態覆寫](../../overview/features/override-hierarchical-state.md)進行修改。 使用此模式時，每個物件的執行時間負荷都是可忽略的。 這很適合您仍然需要個別物件檢查，但不會變更每個物件的轉換。

`none`模式具有最少的執行時間額外負荷，而且更有更好的載入時間。 在此模式下，不可能進行單一物件的檢查或轉換。 例如，使用案例是一開始沒有有意義之場景圖形的攝影測量模型。

> [!TIP]
> 許多應用程式都會載入多個模型。 您應該根據使用方式，將每個模型的轉換參數優化。 例如，如果您想要顯示汽車的模型，讓使用者更深入地進行檢查，您需要使用`dynamic`模式來進行轉換。 不過，如果您還想要將汽車放在「顯示房間」環境中，則可以將該`sceneGraphMode`模型轉換`static`成或`none`甚至是。

### <a name="physics-parameters"></a>物理參數

* `generateCollisionMesh`-如果您需要對模型進行[空間查詢](../../overview/features/spatial-queries.md)的支援，則必須啟用此選項。 在最壞的情況下，建立衝突網格可以兩倍的轉換時間。 具有碰撞網格的模型需要較長的時間來載入`dynamic` ，而使用場景圖形時，它們也會有較高的執行時間效能額外負荷。 為了達到整體最佳效能，您應該在不需要空間查詢的所有模型上停用此選項。

### <a name="unlit-materials"></a>Unlit 材質

* `unlitMaterials`-根據預設，轉換會偏好建立[.pbr 材質](../../overview/features/pbr-materials.md)。 此選項會指示轉換器將所有材質視為[色彩材質](../../overview/features/color-materials.md)。 如果您有已納入光源的資料（例如透過攝影測量建立的模型），此選項可讓您快速地強制執行所有材質的正確轉換，而不需要個別覆[寫每個](override-materials.md)資料。

### <a name="converting-from-older-fbx-formats-with-a-phong-material-model"></a>使用 Phong 材質模型從較舊的 FBX 格式轉換

* `fbxAssumeMetallic`-較舊版本的 FBX 格式會使用 Phong 材質模型來定義其材質。 轉換程式必須推斷這些材質如何對應到轉譯器的[.pbr 模型](../../overview/features/pbr-materials.md)。 這通常很不錯，但當材質沒有材質、高反射值和非灰色的 albedo 色彩時，可能會發生不明確的問題。 在這種情況下，轉換必須選擇排列高反射值的優先順序、定義高度反射的金屬材質，其中 albedo 的色彩會漸漸溶解，或將 albedo 色彩設為優先順序，定義類似發亮的彩色塑膠。 根據預設，轉換程式會假設高度的反射值在套用不明確的情況下，會代表金屬材質。 這個參數可以設定為`false` ，以切換至相反的。

### <a name="coordinate-system-overriding"></a>座標系統覆寫

* `axis`-覆寫座標系統單位向量。 預設值為`["+x", "+y", "+z"]`。 理論上，FBX 格式有一個標頭，其中定義了這些向量，而轉換會使用該資訊來轉換場景。 GlTF 格式也會定義固定座標系統。 在實務上，某些資產的標頭中有不正確的資訊，或是以不同的座標系統慣例儲存。 此選項可讓您覆寫要補償的座標系統。 例如： `"axis" : ["+x", "+z", "-y"]`會藉由反轉 y 軸方向來交換 Z 軸和 y 軸，並保持座標系統慣用手。

### <a name="vertex-format"></a>頂點格式

您可以調整網格的頂點格式，以節省記憶體的交易精確度。 較低的記憶體使用量可讓您載入較大的模型，或達到更佳的效能。 不過，視您的資料而定，錯誤的格式可能會大幅影響轉譯品質。

> [!CAUTION]
> 當模型不再放入記憶體中，或優化以獲得最佳效能時，變更頂點格式應該是最後的手段。 變更可以輕鬆引進轉譯成品，其中包括明顯的成品和細微的專案。 除非您知道要查看的內容，否則不應變更預設值。

可能的調整如下：

* 您可以明確地包含或排除特定的資料流程。
* 資料流程的精確度可以減少，以降低記憶體使用量。

檔案中`vertex` `.json`的下列區段是選擇性的。 對於未明確指定的每個部分，轉換服務會回復為其預設設定。

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

藉由強制元件至`NONE`，可保證輸出網格沒有個別的資料流程。

#### <a name="component-formats-per-vertex-stream"></a>每個頂點資料流程的元件格式

這些格式可供個別元件使用：

| 頂點元件 | 支援的格式（粗體 = 預設值） |
|:-----------------|:------------------|
|position| **32_32_32_FLOAT**，16_16_16_16_FLOAT |
|color0| **8_8_8_8_UNSIGNED_NORMALIZED**，無 |
|color1| 8_8_8_8_UNSIGNED_NORMALIZED，**無**|
|正常| **8_8_8_8_SIGNED_NORMALIZED**、16_16_16_16_FLOAT、無 |
|正切函數| **8_8_8_8_SIGNED_NORMALIZED**、16_16_16_16_FLOAT、無 |
|binormal| **8_8_8_8_SIGNED_NORMALIZED**、16_16_16_16_FLOAT、無 |
|texcoord0| **32_32_FLOAT**、16_16_FLOAT、無 |
|texcoord1| **32_32_FLOAT**、16_16_FLOAT、無 |

#### <a name="supported-component-formats"></a>支援的元件格式

格式的記憶體痕跡如下所示：

| [格式] | 描述 | 每個頂點的位元組數 |
|:-------|:------------|:---------------|
|32_32_FLOAT|雙元件完整浮點精確度|8
|16_16_FLOAT|雙元件半浮點精確度|4
|32_32_32_FLOAT|三個元件的完整浮點精確度|12
|16_16_16_16_FLOAT|四個分量半浮點精確度|8
|8_8_8_8_UNSIGNED_NORMALIZED|四個元件的位元組，正規化`[0; 1]`為範圍|4
|8_8_8_8_SIGNED_NORMALIZED|四個元件的位元組，正規化`[-1; 1]`為範圍|4

#### <a name="best-practices-for-component-format-changes"></a>元件格式變更的最佳做法

* `position`：很少會降低精確度。 **16_16_16_16_FLOAT**引進了明顯的量化成品，即使是小型模型也一樣。
* `normal`、 `tangent`、 `binormal`：通常這些值會一起變更。 除非標準量化會產生明顯的光源成品，否則無法增加其精確度。 不過，在某些情況下，這些元件可以設定為 [**無**]：
  * `normal`只有`tangent`當模型`binormal`中至少有一個材質應亮起時，才需要、和。 在 ARR 中，這是在模型上隨時使用[.pbr 材質](../../overview/features/pbr-materials.md)的情況。
  * `tangent`只有`binormal`在任何一種光線材質使用一般地圖材質時，才需要和。
* `texcoord0`、 `texcoord1` ：材質座標可以在其值留**16_16_FLOAT**在`[0; 1]`範圍內，以及當已定址紋理的大小上限為 2048 x 2048 圖元時，使用降低的精確度（16_16_FLOAT）。 如果超過這些限制，材質對應的品質就會受到影響。

#### <a name="example"></a>範例

假設您有一個攝影測量模型，其光源會內建到紋理。 呈現模型所需的全部都是頂點位置和材質座標。

根據預設，轉換器必須假設您可能想要在一段時間對模型使用 .pbr 材質，因此它會為您產生`normal`、 `tangent`和`binormal`資料。 因此，每個頂點的記憶體使用量`position`是（12位元組） `texcoord0` + （8個位元組`normal` ） + （4個`tangent`位元組） + （4 `binormal`個位元組） + （4位元組） = 32 個位元組。 此類型的較大型模型可以輕鬆地擁有多個以百萬計的頂點，而導致可能佔用多 gb 記憶體的模型。 這類大量資料會影響效能，而且您甚至可能會用盡記憶體。

知道您在模型上永遠不需要動態光源，而且知道`[0; 1]`所有材質座標都在範圍內，您可以將`normal`、 `tangent`和設`binormal`為`NONE` ， `texcoord0`並將設定為`16_16_FLOAT`半精確度（），因此每個頂點只能有16個位元組。 以一半的方式剪下網格資料，可讓您載入較大的模型，而且可能會改善效能。

## <a name="typical-use-cases"></a>一般使用案例

為指定的使用案例尋找良好的匯入設定可能是繁瑣的過程。 另一方面，轉換設定可能會對執行時間效能造成重大影響。

有特定的使用案例類別，符合特定的優化。 以下提供一些範例。

### <a name="use-case-architectural-visualization--large-outdoor-maps"></a>使用案例：架構視覺效果/大型室外地圖

* 這些類型的場景通常是靜態的，這表示它們不需要可移動的元件。 因此， `sceneGraphMode`可以設定為或甚至`static` `none`，以改善執行時間效能。 使用`static`模式時，場景的根節點仍然可以移動、旋轉和縮放，例如，在1:1 刻度之間動態切換（針對第一個人員視圖）和 [資料表頂端視圖]。

* 當您需要移動部分時，通常也表示您需要支援 raycasts 或其他[空間查詢](../../overview/features/spatial-queries.md)，讓您能夠在一開始就挑選那些部分。 另一方面，如果您不想要四處移動，可能也不需要它來參與空間查詢，因此可以關閉`generateCollisionMesh`旗標。 此參數對轉換時間、載入時間，以及每個畫面格的更新成本有顯著的影響。

* 如果應用程式不使用[切割平面](../../overview/features/cut-planes.md)，則`opaqueMaterialDefaultSidedness`應該關閉旗標。 效能增益通常是 20%-30%。 剪下的平面仍然可以使用，但在查看物件的內部部分時，並不會有背面的臉部，這看起來會是直覺的。 如需詳細資訊，請參閱[單面](../../overview/features/single-sided-rendering.md)轉譯。

### <a name="use-case-photogrammetry-models"></a>使用案例：攝影測量模型

呈現攝影測量模型時，通常不需要場景圖形，因此您可以將設定`sceneGraphMode`為。 `none` 雖然這些模型很少包含一開始的複雜場景圖形，但此選項的影回應該是不重要的。

因為光源已內建到紋理中，所以不需要動態光源。 因此：

* 將`unlitMaterials`旗標設定`true`為，以將所有材質轉換成 unlit[色彩材質](../../overview/features/color-materials.md)。
* 從頂點格式移除不需要的資料。 請參閱上面的[範例](#example)。

### <a name="use-case-visualization-of-compact-machines-etc"></a>使用案例： compact 機器的視覺效果等等。

在這些使用案例中，模型在小型磁片區中通常會有非常高的詳細資料。 轉譯器非常適合用來處理這種情況。 不過，先前使用案例中提及的大部分優化都不適用於此：

* 個別元件應該是可選取和可移動的`sceneGraphMode` ，因此必須保持為`dynamic`。
* 光線轉換通常是應用程式不可或缺的一部分，因此必須產生衝突網格。
* 已啟用旗標時， `opaqueMaterialDefaultSidedness`剪下飛機的外觀較佳。

## <a name="next-steps"></a>後續步驟

* [模型轉換](model-conversion.md)
* [色彩材質](../../overview/features/color-materials.md)
* [PBR 材質](../../overview/features/pbr-materials.md)
* [在模型轉換期間覆寫材質](override-materials.md)
