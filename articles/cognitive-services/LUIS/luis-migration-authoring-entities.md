---
title: 遷移至 V3 機器學習實體
description: V3 撰寫提供了一種新的實體類型、機器學習實體，以及將關聯性新增至機器學習服務實體和其他應用程式實體或功能的能力。
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: how-to
ms.date: 05/08/2020
ms.openlocfilehash: 667226770d25ef1687420b1c13bc71863f987e33
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "91324683"
---
# <a name="migrate-to-v3-authoring-entity"></a>遷移至 V3 撰寫實體

V3 撰寫提供了一種新的實體類型、機器學習實體，以及將關聯性新增至機器學習服務實體和其他應用程式實體或功能的能力。

## <a name="entities-are-decomposable-in-v3"></a>可分解在 V3 中的實體

使用 V3 撰寫 Api （使用 [api](https://westeurope.dev.cognitive.microsoft.com/docs/services/luis-programmatic-apis-v3-0-preview) 或入口網站）建立的實體，可讓您建立具有父系和子系的多層式實體模型。 所謂的父代就是 **機器學習實體** ，而子系稱為機器學習實體的 **子實體** 。

每個列也都是機器學習實體，但具有功能的新增設定選項。

* **必要的功能** 是保證在符合某項功能時，會將實體解壓縮的規則。 規則是由模型的必要功能所定義：
    * [預建實體](luis-reference-prebuilt-entities.md)
    * [規則運算式實體](reference-entity-regular-expression.md)
    * [列出實體](reference-entity-list.md)。

## <a name="how-do-these-new-relationships-compare-to-v2-authoring"></a>這些新的關聯性與 V2 撰寫有何不同

V2 撰寫提供階層式和複合實體以及角色和功能，以完成這項相同的工作。 因為實體、功能和角色並未彼此明確相關，所以很難瞭解 LUIS 在預測期間隱含關聯性的方式。

使用 V3 時，關聯性是明確的，且是由應用程式作者所設計。 這可讓您以應用程式作者的身份執行：

* 以視覺化方式查看 LUIS 在範例中的預測這些關聯性語句
* 使用 [互動式測試窗格](luis-interactive-test.md) 或端點測試這些關聯性
* 在用戶端應用程式中，透過結構完整、名稱為 nested 的[json 物件](reference-entity-machine-learned-entity.md)來使用這些關聯性

## <a name="planning"></a>規劃

當您遷移時，請在您的遷移計畫中考慮下列事項：

* 備份您的 LUIS 應用程式，並在個別的應用程式上執行遷移。 同時提供 V2 和 V3 應用程式，可讓您驗證所需的變更，以及對預測結果的影響。
* 捕獲目前的預測成功計量
* 以應用程式狀態的快照集形式捕獲目前的儀表板資訊
* 檢查現有的意圖、實體、片語清單、模式和批次測試
* 您可以在 **不變更的情況**下遷移下列元素：
    * 對應方式
    * 實體
        * 規則運算式實體
        * 清單實體
    * 特性
        * 片語清單
* 下列元素需要 **隨變更一起**遷移：
    * 實體
        * 階層式實體
        * 複合實體
    * 角色-角色只能套用至機器學習服務 (父) 實體。 無法將角色套用至子實體
    * 使用階層式和複合實體的批次測試和模式

當您設計您的遷移計畫時，請在所有階層式和複合實體都已遷移之後，離開審核最終機器學習實體的時間。 雖然直接遷移將可運作，但在您進行變更並檢查批次測試結果，以及預測 JSON 之後，更一致的 JSON 可能會導致您進行變更，因此傳遞給用戶端應用程式的最終資訊會以不同的方式組織。 這與程式碼重構類似，而且應該使用您的組織所擁有的相同審查流程來處理。

如果您沒有 V2 模型的批次測試，並將批次測試遷移至 V3 模型作為遷移的一部分，您將無法驗證遷移將如何影響端點預測結果。

## <a name="migrating-from-v2-entities"></a>從 V2 實體遷移

當您開始移至 V3 撰寫模型時，您應該考慮如何移至機器學習服務實體及其子實體和功能。

下表說明哪些實體需要從 V2 遷移至 V3 實體設計。

|V2 撰寫實體類型|V3 撰寫實體類型|範例|
|--|--|--|
|複合實體|機器學習實體|[瞭解更多資訊](#migrate-v2-composite-entity)|
|階層式實體|機器學習實體的角色|[瞭解更多資訊](#migrate-v2-hierarchical-entity)|

## <a name="migrate-v2-composite-entity"></a>遷移 V2 複合實體

V2 複合的每個子系都應該以 V3 機器學習實體的列來表示。 如果複合子系是預建的、正則運算式或清單實體，這應該套用為列上的必要功能。

規劃將複合實體遷移至機器學習服務實體時的考慮：
* 子實體不能用在模式中
* 子實體不再共用
* 如果子實體被用為非機器學習，就必須加上標籤

### <a name="existing-features"></a>現有功能

在複合實體中用來提升單字的任何片語清單，都應該以功能的形式套用至機器學習 (父) 實體、列 (子) 實體，或意圖 (如果片語清單只適用于一個意圖) 。 規劃將功能新增至實體，以大幅提升最大的目標。 如果最能大幅提升列 (子) 的預測，請不要以一般方式將功能新增至機器學習 (父) 實體。

### <a name="new-features"></a>新功能

在 V3 撰寫中，新增規劃步驟，以針對所有實體和意圖的可能功能評估實體。

### <a name="example-entity"></a>範例實體

此實體僅為範例。 您自己的實體遷移可能需要其他考慮。

請考慮使用 V2 複合來修改 `order` 使用下列內容的比薩：
* 傳遞時間的預建 datetimeV2
* 用來提升特定字組的片語清單，例如比薩、圓形圖、餅皮和配料
* 列出實體來偵測配料，例如蘑菇、olives、辣肉腸。

此實體的範例語句為：

`Change the toppings on my pie to mushrooms and delivery it 30 minutes later`

下表示范遷移：

|V2 模型|V3 模型|
|--|--|
|父元件實體，名為 `Order`|名為的上層機器學習實體 `Order`|
|子預建 datetimeV2|* 將預建實體遷移至新的應用程式。<br>* 在父代上為預建 datetimeV2 新增必要的功能。|
|配料的子清單實體|* 將清單實體遷移至新的應用程式。<br>* 然後在清單實體的父系上新增必要的功能。|


## <a name="migrate-v2-hierarchical-entity"></a>遷移 V2 階層式實體

在 V2 撰寫中，會在 LUIS 中現有的角色之前提供階層式實體。 這兩個服務都是以內容使用方式來解壓縮實體的相同目的。 如果您有階層式實體，您可以將它們視為具有角色的簡單實體。

在 V3 撰寫中：
* 角色可套用至機器學習服務 (父) 實體。
* 角色無法套用至任何子實體。

此實體僅為範例。 您自己的實體遷移可能需要其他考慮。

請考慮使用 V2 階層式實體來修改比薩 `order` ：
* 每個子系都可判斷原始配料或最終配料

此實體的範例語句為：

`Change the topping from mushrooms to olives`

下表示范遷移：

|V2 模型|V3 模型|
|--|--|
|父元件實體，名為 `Order`|名為的上層機器學習實體 `Order`|
|具有原始和最終比薩配料的子階層式實體|* 將角色新增至 `Order` 每個配料。|

## <a name="api-change-constraint-replaced-with-required-feature"></a>以必要功能取代的 API 變更條件約束

這項變更是在//Build 會議的2020月進行的變更，僅適用于應用程式使用受限功能的 v3 撰寫 Api。 如果您要從 v2 撰寫遷移至 v3 撰寫，或未使用 v3 限制功能，請略過本節。

**功能** ：需要現有的實體做為另一個模型的功能，並只在偵測到實體時才能將該模型解壓縮。 功能尚未變更，但 API 和術語已變更。

|先前的術語|新術語|
|--|--|
|`constrained feature`<br>`constraint`<br>`instanceOf`|`required feature`<br>`isRequired`|

#### <a name="automatic-migration"></a>自動遷移

自 **19 2020 年6月**起，您將無法使用公開這項功能的先前撰寫 API，以程式設計方式建立條件約束。

所有現有的條件約束功能都會自動遷移至必要的功能旗標。 您的預測 API 不需要任何程式設計變更，也不會產生預測精確度品質的變更。

#### <a name="luis-portal-changes"></a>LUIS 入口網站變更

LUIS preview 入口網站將此功能參考為 **條件約束**。 目前的 LUIS 入口網站會將這項功能指定為 **必要功能**。

#### <a name="previous-authoring-api"></a>先前撰寫的 API

這項功能已套用於預覽撰寫 **[建立實體子 API](https://westus.dev.cognitive.microsoft.com/docs/services/luis-programmatic-apis-v3-0-preview/operations/5d86cf3c6a25a45529767d77)** 做為實體定義的一部分，並使用 `instanceOf` 實體子系的屬性：

```json
{
    "name" : "dayOfWeek",
    "instanceOf": "datetimeV2",
    "children": [
        {
           "name": "dayNumber",
           "instanceOf": "number",
           "children": []
        }
    ]
}
```

#### <a name="new-authoring-api"></a>新增撰寫 API

這項功能現在會與使用和屬性的「 **[新增實體」功能關聯 API](https://westus.dev.cognitive.microsoft.com/docs/services/luis-programmatic-apis-v3-0-preview/operations/5d9dc1781e38aaec1c375f26)** 搭配使用 `featureName` `isRequired` 。 屬性的值 `featureName` 是模型的名稱。

```json
{
    "featureName": "YOUR-MODEL-NAME-HERE",
    "isRequired" : true
}
```


## <a name="next-steps"></a>後續步驟

* [開發人員資源](developer-reference-resource.md)
