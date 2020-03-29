---
title: 遷移到 V3 機器學習實體
titleSuffix: Azure Cognitive Services
description: V3 創作提供了一種新的實體類型，即機器學習的實體，以及向機器學習實體和應用程式的其他實體或功能添加關係的能力。
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 12/30/2019
ms.author: diberry
ms.openlocfilehash: b5dbcd9033d9a41e43ea907d043e0c0486b236db
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "75563851"
---
# <a name="migrate-to-v3-authoring-entity"></a>遷移到 V3 創作實體

V3 創作提供了一種新的實體類型，即機器學習的實體，以及向機器學習實體和應用程式的其他實體或功能添加關係的能力。

## <a name="entities-are-decomposable-in-v3"></a>實體在 V3 中可分解

使用 V3 創作 API 創建的實體（使用[API](https://westeurope.dev.cognitive.microsoft.com/docs/services/luis-programmatic-apis-v3-0-preview)還是預覽[門戶](https://preview.luis.ai/)）允許您使用父實體和子級構建分層實體模型。 父級稱為**機器學習實體**，子級稱為機器學習實體的**子元件**。

每個子元件也是機器學習的實體，但添加了約束和描述項的配置選項。

* **約束**是保證實體與規則匹配時提取的精確文本匹配規則。 規則由一個確切的文本匹配實體定義，當前：[預構建實體](luis-reference-prebuilt-entities.md)、[正則運算式實體](reference-entity-regular-expression.md)或[清單實體](reference-entity-list.md)。
* **描述項**是用於強指示實體[的特徵](luis-concept-feature.md)，如片語清單或實體。

V3 創作提供了一種新的實體類型，即機器學習的實體，以及向機器學習實體和應用程式的其他實體或功能添加關係的能力。

## <a name="how-do-these-new-relationships-compare-to-v2-authoring"></a>這些新關係如何與 V2 創作進行比較

V2 創作提供了分層和複合實體以及角色和功能來完成此相同的任務。 由於實體、要素和角色沒有明確關聯，因此很難理解 LUIS 在預測期間如何隱含關係。

使用 V3 時，關係由應用作者明確設計。 這允許您作為應用作者：

* 直觀地查看 LUIS 如何預測這些關係，在示例陳述中
* 使用[互動式測試窗格](luis-interactive-test.md)或終結點測試這些關係
* 通過結構良好的命名嵌套[.json 物件](reference-entity-machine-learned-entity.md)在用戶端應用程式中使用這些關係

## <a name="planning"></a>規劃

遷移時，請考慮遷移計畫中的以下內容：

* 備份 LUIS 應用，並在單獨的應用上執行遷移。 同時提供 V2 和 V3 應用可以驗證所需的更改及其對預測結果的影響。
* 捕獲當前預測成功指標
* 將當前儀表板資訊捕獲為應用狀態快照
* 查看現有意圖、實體、片語清單、模式和批次處理測試
* 可以遷移以下元素**而不進行更改**：
    * 對應方式
    * 實體
        * 規則運算式實體
        * 清單實體
    * 特性
        * 片語清單
* 需要**使用更改**遷移以下元素：
    * 實體
        * 階層式實體
        * 複合實體
    * 角色 - 角色只能應用於機器學習的（父）實體。 角色不能應用於子元件
    * 使用分層和複合實體的批次處理測試和模式

設計遷移計畫時，請留出時間查看所有分層和複合實體之後的最終機器學習實體。 雖然直接遷移將起作用，但當您進行更改並查看批次處理測試結果並預測 JSON 後，JSON 越統一，可能會導致您進行更改，以便傳遞到用戶端應用的最終資訊以不同的方式組織。 這與代碼重構類似，應該與您的組織所設置的相同審核流程進行處理。

如果 V2 模型沒有批次處理測試，並且批次處理測試作為遷移的一部分遷移到 V3 模型，則無法驗證遷移將如何影響終結點預測結果。

## <a name="migrating-from-v2-entities"></a>從 V2 實體遷移

開始遷移到 V3 創作模型時，應考慮如何移動到機器學習的實體及其子元件（包括約束和描述項）。

下表記下哪些實體需要從 V2 遷移到 V3 實體設計。

|V2 創作實體類型|V3 創作實體類型|範例|
|--|--|--|
|複合實體|機器學習實體|[瞭解更多資訊](#migrate-v2-composite-entity)|
|階層式實體|機器學習實體的角色|[瞭解更多資訊](#migrate-v2-hierarchical-entity)|

## <a name="migrate-v2-composite-entity"></a>遷移 V2 複合實體

V2 複合的每個子級都應使用 V3 電腦學習實體的子元件表示。 如果複合子項是預構建的正則運算式或清單實體，則應將此子項作為表示子子元件**的約束**應用於該實體。

計畫將複合實體遷移到機器學習實體時的注意事項：
* 子實體不能在模式中使用
* 子實體不再共用
* 子實體以前是非機器學習的，則需要標記它們

### <a name="existing-descriptors"></a>現有描述項

任何用於提升複合實體中單詞的片語清單都應作為描述項應用於機器學習的（父實體）實體、子元件（子）實體或意圖（如果片語清單僅適用于一個意圖）。 計畫向它應該最顯著的提升的實體添加描述項。 如果描述項將最顯著地增強子元件（子元件）的預測，則不要將描述項一般地添加到機器學習（父）實體中。

### <a name="new-descriptors"></a>新的描述項

在 V3 創作中，添加一個規劃步驟，以評估實體作為所有實體和意圖的可能描述項。

### <a name="example-entity"></a>示例實體

此實體僅是一個示例。 您自己的實體遷移可能需要其他注意事項。

考慮用於修改使用以下的比薩餅`order`的 V2 複合物：
* 預構建日期時間V2，用於交貨時間
* 片語清單，以提高某些詞，如比薩餅，餡餅，地殼，和澆頭
* 列出實體，以檢測澆頭，如蘑菇，橄欖，辣椒。

此實體的示例表述是：

`Change the toppings on my pie to mushrooms and delivery it 30 minutes later`

下表演示了遷移：

|V2 型號|V3 型號|
|--|--|
|父 - 名為`Order`|父級 - 名為機器學習的實體`Order`|
|子級 - 預構建日期時間V2|• 將預構建的實體遷移到新應用。<br>• 為預構建的日期時間 V2 添加父級約束。|
|子 - 用於澆頭的清單實體|• 將清單實體遷移到新應用。<br>• 然後對清單實體的父實體添加約束。|


## <a name="migrate-v2-hierarchical-entity"></a>遷移 V2 分層實體

在 V2 創作中，在 LUIS 中存在的角色之前提供了分層實體。 兩者都達到了基於上下文使用方式提取實體的相同目的。 如果您有分層實體，則可以將它們視為具有角色的簡單實體。

在 V3 創作中：
* 角色可以應用於機器學習的（父實體）實體。
* 不能將角色應用於任何子元件。

此實體僅是一個示例。 您自己的實體遷移可能需要其他注意事項。

考慮用於修改比薩餅`order`的 V2 分層實體：
* 其中每個子項確定原始澆頭或最終澆頭

此實體的示例表述是：

`Change the topping from mushrooms to olives`

下表演示了遷移：

|V2 型號|V3 型號|
|--|--|
|父 - 名為`Order`|父級 - 名為機器學習的實體`Order`|
|兒童 - 分層實體與原始和最終比薩餅澆頭|•`Order`為每個澆頭添加角色。|

## <a name="next-steps"></a>後續步驟

* [開發人員資源](developer-reference-resource.md)
