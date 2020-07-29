---
title: 設定模型轉換
description: 所有模型轉換參數的描述
author: florianborn71
ms.author: flborn
ms.date: 03/06/2020
ms.topic: how-to
ms.openlocfilehash: e3be1f9ec900655f4dae45abd402ff8e6a56e283
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "84147933"
---
# <a name="configure-the-model-conversion"></a>設定模型轉換

本章節記載模型轉換的選項。

## <a name="settings-file"></a>設定檔案

如果在輸入模型旁的輸入容器中找到名為 `ConversionSettings.json` 的檔案，則會使用其來提供模型轉換程序的其他設定。

檔案的內容應該符合下列 json 結構描述：

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
        "deduplicateMaterials" : { "type" : "boolean", "default" : true },
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

範例 `ConversionSettings.json` 檔案可能是：

```json
{
    "scaling" : 0.01,
    "recenterToOrigin" : true,
    "material-override" : "box_materials_override.json"
}
```

### <a name="geometry-parameters"></a>Geometry 參數

* `scaling` - 這個參數會一致地調整模型。 縮放比例可以用來放大或縮小模型，例如在資料表頂端顯示建築物模型。 由於轉譯引擎預期會以公尺指定長度，因此當模型以不同的單位定義時，就會發生此參數的另一個重要用法。 例如，如果模型是以公分定義，則套用 0.01 的縮放比例應該會以正確的大小來轉譯模型。
某些來源資料格式 (例如 .fbx) 會提供單位縮放比例提示，在這種情況下，轉換會隱含地將模型縮放為公尺單位。 來源格式所提供的隱含縮放比例會套用在縮放參數的頂端。
最終的縮放比例因數會套用至幾何頂點和場景圖表節點的本機轉換。 根實體轉換的縮放比例會保持未修改。

* `recenterToOrigin` - 指出應該轉換模型，使其周框方塊在原點置中。
如果來源模型偏離原點，則置中很重要，因為在這種情況下，浮點數精確度問題可能會導致轉譯成品。

* `opaqueMaterialDefaultSidedness` - 轉譯引擎會假設不透明材質為雙面。
如果這不是預期的行為，這個參數應該設定為「SingleSided」。 如需詳細資訊， [ :::no-loc text="single sided"::: 請參閱轉譯](../../overview/features/single-sided-rendering.md)。

### <a name="material-overrides"></a>材質覆寫

* `material-override` - 此參數可[在轉換期間自訂](override-materials.md)材質處理。

### <a name="material-de-duplication"></a>材質重複資料刪除

* `deduplicateMaterials` - 這個參數會啟用或停用共用相同屬性和紋理的自動材質重複資料刪除。 在處理材質覆寫之後，會進行重複資料刪除。 此選項預設為啟用狀態。

### <a name="color-space-parameters"></a>色彩空間參數

轉譯引擎會預期色彩值為線性空間。
如果模型是使用色差補正空間來定義，則這些選項應該設定為 True。

* `gammaToLinearMaterial` - 將材質色彩從色差補正空間轉換成線性空間
* `gammaToLinearVertex`-將 :::no-loc text="vertex"::: 色彩從 gamma 空間轉換成線性空間

> [!NOTE]
> 若為 FBX 檔案，這些設定預設會設定為 `true`。 若為所有其他檔案類型，預設值為 `false`。

### <a name="scene-parameters"></a>場景參數

* `sceneGraphMode` - 定義來源檔案中場景圖表的轉換方式：
  * `dynamic` (預設值)︰檔案中的所有物件都會公開為 API 中的[實體](../../concepts/entities.md)，且可以獨立轉換。 執行階段的節點階層與來源檔案中的結構相同。
  * `static`:所有物件都會在 API 中公開，但無法獨立轉換。
  * `none`:場景圖表會摺疊成一個物件。

每種模式都有不同的執行階段效能。 在 `dynamic` 模式中，效能成本會隨著圖表中的[實體](../../concepts/entities.md)數目呈線性縮放比例，即使未移動任何部分也一樣。 其應該只在應用程式需要個別移動組件時使用，例如「分解視圖」動畫。

`static` 模式會匯出完整的場景圖表，但此圖表內的組件具有相對於其根部分的常數轉換。 不過，物件的根節點仍然可以移動、旋轉或縮放比例，而不會有顯著的效能成本。 此外，[空間查詢](../../overview/features/spatial-queries.md)會傳回個別的組件，且每個組件分都可以透過[狀態覆寫](../../overview/features/override-hierarchical-state.md)來進行修改。 使用此模式時，每個物件的執行階段額外負荷都是可忽略的。 這很適合您在其中仍需要每個物件檢查，但不需要每個物件轉換變更的大型場景。

`none` 模式的執行階段額外負荷最少，且載入時間也稍微較佳。 在此模式中，無法進行單一物件的檢查或轉換。 例如，使用案例是一開始沒有有意義之場景圖表的攝影測量模型。

> [!TIP]
> 許多應用程式都會載入多個模型。 您應該根據使用方式，將每個模型的轉換參數進行最佳化。 例如，如果您想要向使用者顯示汽車的型號，使其更深入地仔細分析及檢查，您需要使用 `dynamic` 模式來進行轉換。 不過，如果您還想要將汽車放在「展示間」環境中，可以將 `sceneGraphMode` 設定為 `static` 或甚至 `none` 來轉換該模型。

### <a name="physics-parameters"></a>物理參數

* `generateCollisionMesh` - 如果您需要模型上[空間查詢](../../overview/features/spatial-queries.md)的支援，必須啟用此選項。 在最壞的情況下，建立衝突網格可以加倍轉換時間。 使用 `dynamic` 場景圖表時，具有碰撞網格的模型需要較長時間來載入，其也會有較高的執行階段效能額外負荷。 為了達到整體最佳效能，您應該在不需要空間查詢的所有模型上停用此選項。

### <a name="unlit-materials"></a>Unlit 材質

* `unlitMaterials` - 根據預設，轉換會偏好建立 [PBR 材質](../../overview/features/pbr-materials.md)。 此選項會指示轉換器將所有材質視為[色彩材質](../../overview/features/color-materials.md)。 如果您有已納入光源的資料，例如透過攝影測量建立的模型，則此選項可讓您快速地強制執行所有材質的正確轉換，而不需要個別[覆寫每個材質](override-materials.md)。

### <a name="converting-from-older-fbx-formats-with-a-phong-material-model"></a>使用 Phong 材質模型從較舊的 FBX 格式進行轉換

* `fbxAssumeMetallic` - 較舊版的 FBX 格式會使用 Phong 材質模型來定義其材質。 轉換程序必須推斷這些材質如何對應到轉譯器的 [PBR 模型](../../overview/features/pbr-materials.md)。 這通常會運作良好，但當材質沒有紋理、高反射值和非灰色的 Albedo 色彩時，可能會發生不明確的問題。 在這種情況下，轉換必須選擇排列高反射值的優先順序、定義高度反射的金屬材質，其中 Albedo 的色彩會漸漸溶解，或將 Albedo 色彩設為優先順序，定義類似發亮的彩色塑膠。 根據預設，轉換程序會假設高度的反射值在套用不明確的情況下，會代表金屬材質。 這個參數可以設定為 `false`，以切換至相反的項目。

### <a name="coordinate-system-overriding"></a>座標系統覆寫

* `axis` - 覆寫座標系統單位向量。 預設值為 `["+x", "+y", "+z"]`。 理論上，FBX 格式有一個標頭，其中定義了這些向量，且轉換會使用該資訊來轉換場景。 GlTF 格式也會定義固定座標系統。 在實務上，某些資產的標頭中有不正確的資訊，或是以不同的座標系統慣例儲存。 此選項可讓您覆寫要補償的座標系統。 例如：`"axis" : ["+x", "+z", "-y"]` 將會藉由反轉 Y 軸方向來交換 Z 軸和 Y 軸，並保持座標系統慣用手。

### <a name="no-loc-textvertex-format"></a>:::no-loc text="Vertex":::編排

您可以調整 :::no-loc text="vertex"::: 網格的格式，以節省記憶體的交易精確度。 較低的磁碟使用量可讓您載入較大的模型，或達到更佳的效能。 不過，視您的資料而定，錯誤的格式可能會大幅影響轉譯品質。

> [!CAUTION]
> :::no-loc text="vertex":::當模型不再符合記憶體，或優化以獲得最佳效能時，變更格式應該是最後的手段。 變更可以輕鬆引進轉譯成品，其中包括明顯和細微的成品。 除非您知道要查看的內容，否則不應變更預設值。

可能的調整如下：

* 您可以明確地包含或排除特定的資料流。
* 可以減少資料流的正確性，以降低磁碟使用量。

`.json` 檔案中的下列 `vertex` 區段是選擇性的。 對於未明確指定的每個部分，轉換服務會回復為其預設設定。

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

藉由將元件強制設定為 `NONE`，保證輸出網格沒有個別的串流。

#### <a name="component-formats-per-no-loc-textvertex-stream"></a>每個資料流程的元件格式 :::no-loc text="vertex":::

這些格式可供個別元件使用：

| :::no-loc text="Vertex"::: 元件 | 支援的格式 (粗體 = 預設值) |
|:-----------------|:------------------|
|position| **32_32_32_FLOAT**、16_16_16_16_FLOAT |
|color0| **8_8_8_8_UNSIGNED_NORMALIZED**、NONE |
|color1| 8_8_8_8_UNSIGNED_NORMALIZED、**NONE**|
|正常| **8_8_8_8_SIGNED_NORMALIZED**、16_16_16_16_FLOAT, NONE |
|正切函數| **8_8_8_8_SIGNED_NORMALIZED**、16_16_16_16_FLOAT, NONE |
|binormal| **8_8_8_8_SIGNED_NORMALIZED**、16_16_16_16_FLOAT, NONE |
|texcoord0| **32_32_FLOAT**、16_16_FLOAT、無 |
|texcoord1| **32_32_FLOAT**、16_16_FLOAT、無 |

#### <a name="supported-component-formats"></a>支援的元件格式

格式的磁碟使用量如下所示：

| [格式] | 描述 | 位元組/:::no-loc text="vertex"::: |
|:-------|:------------|:---------------|
|32_32_FLOAT|雙元件完整浮點數精確度|8
|16_16_FLOAT|雙元件半浮點數精確度|4
|32_32_32_FLOAT|三個元件的完整浮點數精確度|12
|16_16_16_16_FLOAT|四元件半浮點數精確度|8
|8_8_8_8_UNSIGNED_NORMALIZED|四個元件的位元組，正規化為 `[0; 1]` 範圍|4
|8_8_8_8_SIGNED_NORMALIZED|四個元件的位元組，正規化為 `[-1; 1]` 範圍|4

#### <a name="best-practices-for-component-format-changes"></a>元件格式變更的最佳做法

* `position`:降低精確度的情況通常會不足夠。 **16_16_16_16_FLOAT** 會引進明顯的量化成品，即使是小型模型也一樣。
* `normal`、`tangent`、`binormal`：這些值通常會一起變更。 除非標準量化會產生明顯的光源成品，否則無須增加其正確性。 不過，在某些情況下，這些元件可以設定為 [無]：
  * 只有當模型中至少有一個材質應亮起時，才需要 `normal`、`tangent` 和 `binormal`。 在 ARR 中，這是在模型上隨時使用 [PBR 材質](../../overview/features/pbr-materials.md)的情況。
  * 只有在任何一種光線材質使用標準對應紋理時，才需要 `tangent` 和 `binormal`。
* `texcoord0`、`texcoord1`：當紋理座標的值保持在 `[0; 1]` 範圍內，且當已定址紋理的大小上限為 2048 x 2048 像素時，紋理座標可能會使用較低的精確度 (**16_16_FLOAT**)。 如果超過這些限制，紋理對應的品質就會受到影響。

#### <a name="example"></a>範例

假設您有一個攝影測量模型，其光源會內建到紋理。 呈現模型所需的全部都是 :::no-loc text="vertex"::: 位置和材質座標。

根據預設，轉換器必須假設您可能有時想要在模型上使用 PBR 的資料，以便為您產生 `normal`、`tangent` 和 `binormal` 資料。 因此，每個頂點的記憶體使用量是 `position` (12 個位元組) + `texcoord0` (8 個位元組) + `normal` (4 個位元組) + `tangent` (4 個位元組) + `binormal` (4 個位元組) = 32 個位元組。 這種類型的較大型模型可以輕鬆地產生許多數百萬個 :::no-loc text="vertices"::: 模型，而可能佔用多 gb 的記憶體。 這類大量資料會影響效能，且您甚至可能會用盡記憶體。

知道您在模型上永遠不需要動態光源，而且知道所有材質座標都在 `[0; 1]` 範圍內，您可以將、和設定為，並將設為 `normal` `tangent` `binormal` `NONE` `texcoord0` 半精確度（ `16_16_FLOAT` ），因此每個只會產生16個位元組 :::no-loc text="vertex"::: 。 將網格資料減半可讓您載入較大的模型，且可能會改善效能。

## <a name="memory-optimizations"></a>記憶體優化

載入內容的記憶體耗用量可能會成為轉譯系統上的瓶頸。 如果記憶體承載變得太大，可能會危害轉譯效能，或導致模型不會全部載入。 這段影片將討論減少記憶體使用量的一些重要策略。

### <a name="instancing"></a>執行個體化

實例是一種概念，其中網格會針對具有不同空間轉換的部分重複使用，而不是每個參考自己唯一幾何的元件。 實例對記憶體使用量有顯著的影響。
實例的使用案例範例是引擎模型中的螺絲或架構模型中的椅子。

> [!NOTE]
> 實例可以大幅改善記憶體耗用量（因而載入時間），不過，轉譯效能端的改善並不重要。

轉換服務會在原始檔中標示元件時，遵循實例。 不過，轉換不會對網格資料執行額外的深入分析，以識別可重複使用的元件。 因此，「內容建立工具」和其「匯出管線」是適當實例設定的決定性準則。

測試在轉換期間是否保留實例資訊的簡單方式，就是查看[輸出統計資料](get-information.md#example-info-file)，尤其是 `numMeshPartsInstanced` 成員。 如果的值大於 `numMeshPartsInstanced` 零，則表示網格會在實例之間共用。

#### <a name="example-instancing-setup-in-3ds-max"></a>範例：3ds 中的實例設定 Max

[Autodesk 3Ds Max](https://www.autodesk.de/products/3ds-max)具有不同的物件複製模式 **`Copy`** ，稱為、和，其 **`Instance`** **`Reference`** 行為與匯出檔案中的實例不同 `.fbx` 。

![以 3ds Max 複製](./media/3dsmax-clone-object.png)

* **`Copy`**：在此模式中，會複製網格，因此不會使用任何實例（ `numMeshPartsInstanced` = 0）。
* **`Instance`**：這兩個物件會共用相同的網格，因此會使用實例（ `numMeshPartsInstanced` = 1）。
* **`Reference`**：不同的修飾詞可以套用至幾何，因此，匯出工具會選擇保守的方法，而不會使用實例（ `numMeshPartsInstanced` = 0）。


### <a name="depth-based-composition-mode"></a>深度式組合模式

如果記憶體有問題，請使用[深度式組合模式](../../concepts/rendering-modes.md#depthbasedcomposition-mode)來設定轉譯器。 在此模式中，GPU 承載會分散到多個 Gpu。

### <a name="decrease-vertex-size"></a>減少頂點大小

如[元件格式變更的最佳做法](configure-model-conversion.md#best-practices-for-component-format-changes)一節中所述，調整頂點格式可能會減少記憶體使用量。 不過，此選項應該是最後的手段。

### <a name="texture-sizes"></a>材質大小

視案例的類型而定，材質資料的數量可能會超過用於網格資料的記憶體。 攝影測量模型是候選項目。
轉換設定不會提供自動相應減少材質的方式。 如有必要，材質調整必須以用戶端前置處理步驟來完成。 不過，轉換步驟會挑選適當的[材質壓縮格式](https://docs.microsoft.com/windows/win32/direct3d11/texture-block-compression-in-direct3d-11)：

* `BC1`針對不透明色彩材質
* `BC7`針對具有 Alpha 色板的來源色彩材質

`BC7`相較于，格式具有兩倍的記憶體使用量 `BC1` ，因此請務必確定輸入材質不會不必要地提供 Alpha 色板。

## <a name="typical-use-cases"></a>一般使用案例

為指定使用案例尋找良好的匯入設定可能是繁瑣的過程。 另一方面，轉換設定可能會對執行階段效能造成重大影響。

有一些特定的使用案例類別符合特定的最佳化。 以下提供一些範例。

### <a name="use-case-architectural-visualization--large-outdoor-maps"></a>使用案例：架構視覺效果/大型室外地圖

* 這些類型的場景通常是靜態的，這表示其不需要可移動的組件。 據此，您可以將 `sceneGraphMode` 設定為 `static` 或甚至 `none`，從而改善執行階段效能。 使用 `static` 模式時，場景的根節點仍然可以移動、旋轉和縮放比例，例如，在 1:1 縮放比例 (適用於第一人稱視圖) 與資料表頂端視圖之間動態切換。

* 當您需要移動組件時，通常也表示您需要支援 raycasts 或其他[空間查詢](../../overview/features/spatial-queries.md)，讓您能夠首先挑選這些組件。 另一方面，如果您不想要將物件四處移動，很可能也不需要其參與空間查詢，因此可以關閉 `generateCollisionMesh` 旗標。 此參數對轉換時間、載入時間及執行階段每個畫面更新成本有顯著的影響。

* 如果應用程式未使用[切割平面](../../overview/features/cut-planes.md)，則 `opaqueMaterialDefaultSidedness` 旗標應該關閉。 效能增益通常是 20%-30%。 切割平面仍可以使用，但在深入查看物件的內部組件時並沒有背面，這看似違反直覺的行為。 如需詳細資訊， [ :::no-loc text="single sided"::: 請參閱轉譯](../../overview/features/single-sided-rendering.md)。

### <a name="use-case-photogrammetry-models"></a>使用案例：攝影測量模型

轉譯攝影測量模型時，通常不需要場景圖表，因此您可以將 `sceneGraphMode` 設定為 `none`。 雖然這些模型很少包含一開始的複雜場景圖表，但此選項的影響應該是不顯著的。

因為光源已內建到紋理中，所以不需要動態光源。 因此：

* 將 `unlitMaterials` 旗標設定為 `true`，將所有材質轉換成 unlit [色彩材質](../../overview/features/color-materials.md)。
* 從頂點格式移除不需要的資料。 請參閱上述的[範例](#example)。

### <a name="use-case-visualization-of-compact-machines-etc"></a>使用案例：精簡電腦的視覺效果等等。

在這些使用案例中，模型在小型磁碟內通常會有非常高的細節。 轉譯器非常適用於處理這種案例。 不過，先前使用案例中提及的大部分最佳化並不適用於此：

* 個別組件應該是可選取且可移動的，因此 `sceneGraphMode` 必須保持 `dynamic`。
* 光線投射通常是應用程式的整數部分，因此必須產生衝突網格。
* 在啟用 `opaqueMaterialDefaultSidedness` 旗標的情況下，切割平面外觀較佳。

## <a name="next-steps"></a>後續步驟

* [模型轉換](model-conversion.md)
* [色彩材質](../../overview/features/color-materials.md)
* [PBR 材質](../../overview/features/pbr-materials.md)
* [在模型轉換期間覆寫材質](override-materials.md)
