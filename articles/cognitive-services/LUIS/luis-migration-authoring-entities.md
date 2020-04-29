---
title: 遷移至 V3 機器學習實體
titleSuffix: Azure Cognitive Services
description: V3 authoring 提供了一種新的實體類型，即機器學習的實體，以及將關聯性新增至機器學習的實體和應用程式的其他實體或功能的功能。
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 12/30/2019
ms.author: diberry
ms.openlocfilehash: b5dbcd9033d9a41e43ea907d043e0c0486b236db
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/29/2020
ms.locfileid: "75563851"
---
# <a name="migrate-to-v3-authoring-entity"></a>遷移至 V3 撰寫實體

V3 authoring 提供了一種新的實體類型，即機器學習的實體，以及將關聯性新增至機器學習的實體和應用程式的其他實體或功能的功能。

## <a name="entities-are-decomposable-in-v3"></a>在 V3 中分解實體

以 V3 撰寫 Api 建立的實體（使用[api](https://westeurope.dev.cognitive.microsoft.com/docs/services/luis-programmatic-apis-v3-0-preview)或透過[預覽入口網站](https://preview.luis.ai/)），可讓您建立具有父系和子系的多層式實體模型。 父系的已知為**機器學習的實體**，而子系則稱為機器學習實體的**子元件**。

每個子元件也是機器學習的實體，但具有條件約束和描述項的新增設定選項。

* **條件約束**是完全相符的文字比對規則，可保證在符合規則時，會將實體解壓縮。 此規則是由完全相符的文字（目前為預先建立的[實體](luis-reference-prebuilt-entities.md)、[正則運算式實體](reference-entity-regular-expression.md)或[清單實體](reference-entity-list.md)）所定義。
* **描述**項是用來強表示實體的[功能](luis-concept-feature.md)，例如片語清單或實體。

V3 authoring 提供了一種新的實體類型，即機器學習的實體，以及將關聯性新增至機器學習的實體和應用程式的其他實體或功能的功能。

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
    * 功能
        * 片語清單
* 下列元素需要**使用變更**進行遷移：
    * 實體
        * 階層式實體
        * 複合實體
    * 角色-角色只能套用至機器學習（父系）實體。 無法將角色套用至子元件
    * 使用階層式和複合實體的批次測試和模式

當您設計您的遷移計畫時，在所有階層式和複合實體都已遷移之後，請隨時查看最終機器學習的實體。 雖然直接的遷移作業會在您進行變更和檢查批次測試結果和預測 JSON 之後進行，但更整合的 JSON 可能會讓您進行變更，讓傳遞至用戶端應用程式的最終資訊以不同的方式組織。 這類似于程式碼重構，應使用您組織已備妥的相同審查流程加以處理。

如果您沒有適用于 V2 模型的批次測試，並在遷移過程中將批次測試遷移至 V3 模型，您將無法驗證遷移對端點預測結果的影響。

## <a name="migrating-from-v2-entities"></a>從 V2 實體進行遷移

當您開始移至 V3 撰寫模型時，您應該考慮如何移至機器學習的實體及其子元件，包括條件約束和描述項。

下表說明哪些實體需要從 V2 遷移至 V3 實體設計。

|V2 撰寫實體類型|V3 撰寫實體類型|範例|
|--|--|--|
|複合實體|機器學習實體|[瞭解更多資訊](#migrate-v2-composite-entity)|
|階層式實體|機器學習實體的角色|[瞭解更多資訊](#migrate-v2-hierarchical-entity)|

## <a name="migrate-v2-composite-entity"></a>遷移 V2 複合實體

V2 複合的每個子系都應該以 V3 機器學習實體的子元件來表示。 如果複合子系是預先建立的、正則運算式或清單實體，則應該將此專案套用為代表子元件的**條件約束**。

規劃將複合實體遷移至機器學習實體時的考慮：
* 子實體不能用於模式
* 子系實體已不再共用
* 如果子實體用為非機器學習，則需要加上標籤

### <a name="existing-descriptors"></a>現有的描述元

用來提升複合實體中之單字的任何片語清單，都應套用為機器學習（父系）實體、子元件（子系）實體或意圖（如果片語清單只適用于一個意圖）的描述項。 規劃將描述項新增至實體，它應該會大幅提升。 如果它最能大幅提升子元件（子系）的預測，請勿將描述項一般新增至機器學習（父系）實體。

### <a name="new-descriptors"></a>新的描述項

在 V3 撰寫中，新增規劃步驟，以將實體評估為所有實體和意圖的可能描述元。

### <a name="example-entity"></a>範例實體

此實體僅為範例。 您自己的實體遷移可能需要其他考慮。

請考慮使用 V2 複合來修改使用`order`的比薩餅：
* 預先建立傳遞時間的 datetimeV2
* 用來提升特定單字（例如比薩、圓形圖、crust 和配料）的片語清單
* 列出實體以偵測配料，例如蘑菇、olives、pepperoni。

此實體的範例語句為：

`Change the toppings on my pie to mushrooms and delivery it 30 minutes later`

下表示范遷移：

|V2 模型|V3 模型|
|--|--|
|名為的父系元件實體`Order`|名為的父系機器學習實體`Order`|
|子預先建立的 datetimeV2|* 將預先建立的實體遷移至新的應用程式。<br>* 針對預先建立的 datetimeV2，在父系上加入條件約束。|
|配料的子清單實體|* 將清單實體遷移至新的應用程式。<br>* 然後在 [清單] 實體的父系上加入條件約束。|


## <a name="migrate-v2-hierarchical-entity"></a>遷移 V2 階層式實體

在第2版的撰寫中，階層式實體是在 LUIS 中現有的角色之前提供。 這兩種服務都是根據內容使用方式來解壓縮實體的相同目的。 如果您有階層式實體，您可以將它們視為具有角色的簡單實體。

在 V3 authoring 中：
* 角色可以套用在機器學習（父系）實體上。
* 無法將角色套用至任何子元件。

此實體僅為範例。 您自己的實體遷移可能需要其他考慮。

請考慮使用 V2 階層式實體來修改`order`比薩：
* 其中，每個子系會決定原始配料或最終配料

此實體的範例語句為：

`Change the topping from mushrooms to olives`

下表示范遷移：

|V2 模型|V3 模型|
|--|--|
|名為的父系元件實體`Order`|名為的父系機器學習實體`Order`|
|具有原始和最終比薩配料的子階層式實體|* 將角色新增`Order`至每個配料。|

## <a name="next-steps"></a>後續步驟

* [開發人員資源](developer-reference-resource.md)
