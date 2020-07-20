---
title: 遷移至 V3 機器學習服務實體
description: V3 authoring 提供一種新的實體類型、機器學習服務實體，以及將關聯性新增至機器學習服務實體和應用程式的其他實體或功能的功能。
ms.topic: how-to
ms.date: 05/08/2020
ms.openlocfilehash: aaa5472f25a5eca5ceadf979c57a83874ce4cb6e
ms.sourcegitcommit: 50673ecc5bf8b443491b763b5f287dde046fdd31
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/20/2020
ms.locfileid: "83684584"
---
# <a name="migrate-to-v3-authoring-entity"></a>遷移至 V3 撰寫實體

V3 authoring 提供一種新的實體類型、機器學習服務實體，以及將關聯性新增至機器學習服務實體和應用程式的其他實體或功能的功能。

## <a name="entities-are-decomposable-in-v3"></a>在 V3 中分解實體

以 V3 撰寫 Api 建立的實體（不論是使用[api](https://westeurope.dev.cognitive.microsoft.com/docs/services/luis-programmatic-apis-v3-0-preview)或透過入口網站），都可讓您建立具有父系和子系的多層式實體模型。 父系稱為「**機器學習」實體**，而子系稱為「機器學習實體」的**子實體**。

每個列也都是機器學習實體，但具有功能的新增設定選項。

* **必要功能**是保證實體符合功能時，會將其解壓縮的規則。 此規則是由模型所需的功能所定義：
    * [預建實體](luis-reference-prebuilt-entities.md)
    * [規則運算式實體](reference-entity-regular-expression.md)
    * [列出實體](reference-entity-list.md)。

## <a name="how-do-these-new-relationships-compare-to-v2-authoring"></a>這些新關聯性與 V2 撰寫的比較

V2 撰寫提供階層式和複合實體，以及角色和功能來完成這項相同的工作。 因為實體、功能和角色並非彼此明確相關，所以很難以瞭解 LUIS 在預測期間隱含關聯性的方式。

使用 V3，關聯性是明確且由應用程式作者設計的。 這可讓您（身為應用程式作者）：

* 在範例語句中，以視覺化方式查看 LUIS 如何預測這些關聯性
* 以[互動式測試窗格](luis-interactive-test.md)或端點測試這些關聯性
* 在用戶端應用程式中使用這些關聯性，透過結構良好、名為的 nested [json 物件](reference-entity-machine-learned-entity.md)

## <a name="planning"></a>規劃

當您遷移時，請考慮您的遷移計畫中的下列各項：

* 備份您的 LUIS 應用程式，並在個別的應用程式上執行遷移。 同時使用 V2 和 V3 應用程式可讓您驗證所需的變更，以及對預測結果的影響。
* 捕捉目前的預測成功計量
* 以應用程式狀態的快照集形式來捕捉目前的儀表板資訊
* 審查現有的意圖、實體、片語清單、模式和批次測試
* 下列元素可以在**不變更的情況**下進行遷移：
    * 對應方式
    * 實體
        * 規則運算式實體
        * 清單實體
    * 特性
        * 片語清單
* 下列元素需要**使用變更**進行遷移：
    * 實體
        * 階層式實體
        * 複合實體
    * 角色-角色只能套用至機器學習（父系）實體。 無法將角色套用至子實體
    * 使用階層式和複合實體的批次測試和模式

當您設計您的遷移計畫時，在所有階層式和複合實體都已遷移之後，請隨時查看最終的機器學習實體。 雖然直接的遷移作業會在您進行變更和檢查批次測試結果和預測 JSON 之後進行，但更整合的 JSON 可能會讓您進行變更，讓傳遞至用戶端應用程式的最終資訊以不同的方式組織。 這類似于程式碼重構，應使用您組織已備妥的相同審查流程加以處理。

如果您沒有適用于 V2 模型的批次測試，並在遷移過程中將批次測試遷移至 V3 模型，您將無法驗證遷移對端點預測結果的影響。

## <a name="migrating-from-v2-entities"></a>從 V2 實體進行遷移

當您開始移至 V3 撰寫模型時，您應該考慮如何移至機器學習服務實體及其子實體和功能。

下表說明哪些實體需要從 V2 遷移至 V3 實體設計。

|V2 撰寫實體類型|V3 撰寫實體類型|範例|
|--|--|--|
|複合實體|機器學習實體|[瞭解更多資訊](#migrate-v2-composite-entity)|
|階層式實體|機器學習服務實體的角色|[瞭解更多資訊](#migrate-v2-hierarchical-entity)|

## <a name="migrate-v2-composite-entity"></a>遷移 V2 複合實體

V2 複合的每個子系都應該以 V3 機器學習服務實體的列來表示。 如果複合子系是預先建立的、正則運算式或清單實體，這應該套用為列上的必要功能。

規劃將複合實體遷移至機器學習服務實體時的考慮：
* 子實體不能用於模式
* 子系實體已不再共用
* 如果子實體用為非機器學習，則需要加上標籤

### <a name="existing-features"></a>現有功能

在複合實體中用來提升單字的任何片語清單，都應該套用為機器學習（父系）實體、列（子系）實體或意圖（如果片語清單只適用于一個意圖）。 規劃將功能新增至實體應該大幅提升的位置。 如果它最能大幅提升列（子系）的預測，請勿將此功能一般新增至機器學習（父系）實體。

### <a name="new-features"></a>新功能

在 V3 撰寫中，新增規劃步驟來評估實體，以作為所有實體和意圖的可能功能。

### <a name="example-entity"></a>範例實體

此實體僅為範例。 您自己的實體遷移可能需要其他考慮。

請考慮使用 V2 複合來修改使用的比薩餅 `order` ：
* 預先建立傳遞時間的 datetimeV2
* 用來提升特定單字（例如比薩、圓形圖、crust 和配料）的片語清單
* 列出實體以偵測配料，例如蘑菇、olives、pepperoni。

此實體的範例語句為：

`Change the toppings on my pie to mushrooms and delivery it 30 minutes later`

下表示范遷移：

|V2 模型|V3 模型|
|--|--|
|名為的父系元件實體`Order`|名為的父系機器學習實體`Order`|
|子預先建立的 datetimeV2|* 將預先建立的實體遷移至新的應用程式。<br>* 針對預先建立的 datetimeV2，在父系上新增必要的功能。|
|配料的子清單實體|* 將清單實體遷移至新的應用程式。<br>* 然後在 [清單] 實體的父系上新增必要的功能。|


## <a name="migrate-v2-hierarchical-entity"></a>遷移 V2 階層式實體

在第2版的撰寫中，階層式實體是在 LUIS 中現有的角色之前提供。 這兩種服務都是根據內容使用方式來解壓縮實體的相同目的。 如果您有階層式實體，您可以將它們視為具有角色的簡單實體。

在 V3 authoring 中：
* 角色可以套用在機器學習（父系）實體上。
* 無法將角色套用至任何子實體。

此實體僅為範例。 您自己的實體遷移可能需要其他考慮。

請考慮使用 V2 階層式實體來修改比薩 `order` ：
* 其中，每個子系會決定原始配料或最終配料

此實體的範例語句為：

`Change the topping from mushrooms to olives`

下表示范遷移：

|V2 模型|V3 模型|
|--|--|
|名為的父系元件實體`Order`|名為的父系機器學習實體`Order`|
|具有原始和最終比薩配料的子階層式實體|* 將角色新增至 `Order` 每個配料。|

## <a name="api-change-constraint-replaced-with-required-feature"></a>API 變更條件約束已取代為必要功能

這項變更是在 build 會議的5月2020進行，僅適用于使用受條件約束功能的 v3 撰寫 Api。 如果您要從 v2 撰寫的版本遷移至 v3 撰寫，或未使用 v3 限制功能，請略過本節。

**功能**-能夠要求現有實體做為另一個模型的功能，而且只會在偵測到實體時，才將該模型解壓縮。 此功能未變更，但 API 和術語已變更。

|先前的術語|新術語|
|--|--|
|`constrained feature`<br>`constraint`<br>`instanceOf`|`required feature`<br>`isRequired`|

#### <a name="automatic-migration"></a>自動遷移

從**19 2020 年6月**開始，您將無法使用先前公開此功能的撰寫 API，以程式設計方式建立條件約束。

所有現有的條件約束功能都會自動遷移至所需的功能旗標。 您的預測 API 不需要任何程式設計變更，預測精確度的品質也不會產生任何變更。

#### <a name="luis-portal-changes"></a>LUIS 入口網站變更

LUIS preview 入口網站將此功能參考為**條件約束**。 目前的 LUIS 入口網站會將此功能指定為**必要功能**。

#### <a name="previous-authoring-api"></a>先前的撰寫 API

這項功能已套用在預覽撰寫中，使用實體的子系的屬性，**[建立實體子 API](https://westus.dev.cognitive.microsoft.com/docs/services/luis-programmatic-apis-v3-0-preview/operations/5d86cf3c6a25a45529767d77)** 做為實體定義的一部分 `instanceOf` ：

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

這項功能現在會使用和屬性搭配「**[新增實體功能關聯」 API](https://westus.dev.cognitive.microsoft.com/docs/services/luis-programmatic-apis-v3-0-preview/operations/5d9dc1781e38aaec1c375f26)** 來套用 `featureName` `isRequired` 。 屬性的值 `featureName` 是模型的名稱。

```json
{
    "featureName": "YOUR-MODEL-NAME-HERE",
    "isRequired" : true
}
```


## <a name="next-steps"></a>後續步驟

* [開發人員資源](developer-reference-resource.md)
