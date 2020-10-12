---
title: 架構更新6月-1-2016
description: Azure Logic Apps 中邏輯應用程式定義的更新後結構描述版本 2016-06-01
services: logic-apps
ms.suite: integration
author: kevinlam1
ms.author: klam
ms.reviewer: estfan, logicappspm
ms.topic: article
ms.date: 07/25/2016
ms.openlocfilehash: ccc7df5bfac327fabf05f210764dbe10658b5015
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "86520710"
---
# <a name="schema-updates-for-azure-logic-apps---june-1-2016"></a>Azure Logic Apps 的結構描述更新 - 2016 年 6 月 1 日

Azure Logic Apps 之 API 版本的[已更新結構描述](https://schema.management.azure.com/schemas/2016-06-01/Microsoft.Logic.json)包含重要改進，讓邏輯應用程式更可靠且更容易使用：

* [範圍](#scopes)可讓您建立動作的巢狀或群組做為動作集合。
* [條件和迴圈](#conditions-loops)現在是第一級動作。
* 執行動作的更精確順序與 `runAfter` 屬性，取代 `dependsOn`

如需從 2015 年 8 月 1 日預覽結構描述的邏輯應用程式升級為 2016 年 6 月 1 日結構描述，[請查看升級章節](#upgrade-your-schema)。

<a name="scopes"></a>

## <a name="scopes"></a>範圍

這個結構描述包含可讓您將動作群組在一起或在彼此內巢狀動作的範圍。 例如，條件可包含另一個條件。 深入了解[範圍語法](./logic-apps-control-flow-loops.md)，或檢閱此基本範圍範例︰

```json
{
   "actions": {
      "Scope": {
         "type": "Scope",
         "actions": {                
            "Http": {
               "inputs": {
                   "method": "GET",
                   "uri": "https://www.bing.com"
               },
               "runAfter": {},
               "type": "Http"
            }
         }
      }
   }
}
```

<a name="conditions-loops"></a>

## <a name="conditions-and-loops-changes"></a>條件和迴圈的變更

在舊版結構描述中，條件和迴圈是與單一動作相關聯的參數。 此結構描述會放寬此限制，因此條件和迴圈現在可用來作為動作類型。 深入了解[迴圈和範圍](./logic-apps-control-flow-loops.md)、或檢閱這個顯示 [條件](../logic-apps/logic-apps-control-flow-conditional-statement.md) 動作的基本範例：

```json
{
   "Condition - If trigger is some trigger": {
      "type": "If",
      "expression": "@equals(triggerBody(), '<trigger-name>')",
      "runAfter": {},
      "actions": {
         "Http_2": {
            "inputs": {
                "method": "GET",
                "uri": "https://www.bing.com"
            },
            "runAfter": {},
            "type": "Http"
         }
      },
      "else": 
      {
         "Condition - If trigger is another trigger": {}
      }  
   }
}
```

<a name="run-after"></a>

## <a name="runafter-property"></a>'runAfter' 屬性

`runAfter` 屬性會取代 `dependsOn`，當您根據先前動作的狀態指定動作的執行順序時提供更高的精確度。 `dependsOn` 屬性會根據上一個動作是否成功、失敗，還是略過，來指出是否「動作已執行且成功」，而不是根據您想要執行此動作的時間量。 `runAfter` 屬性提供如同下列物件的彈性：物件會指定該物件要在其後執行的所有動作名稱。 這個屬性也會定義可接受做為觸發程序狀態的陣列。 例如，如果您想要動作在動作 A 成功後，且在動作 B 成功或失敗之後執行，請設定此 `runAfter` 屬性：

```json
{
   // Other parts in action definition
   "runAfter": {
      "A": ["Succeeded"],
      "B": ["Succeeded", "Failed"]
    }
}
```

## <a name="upgrade-your-schema"></a>升級您的結構描述

若要升級到[最近的結構描述](https://schema.management.azure.com/schemas/2016-06-01/Microsoft.Logic.json)，您只需要執行幾個步驟。 升級程序包含執行升級指令碼、儲存為新的邏輯應用程式，以及如果想要的話，可能會覆寫先前的邏輯應用程式。

1. 在 Azure 入口網站中，開啟邏輯應用程式。

2. 前往 **總覽**。 在邏輯應用程式工具列上，選擇 [更新結構描述]****。
   
   ![選擇 [更新結構描述]][1]
   
   會傳回升級的定義，您可以將其複製並貼到資源定義 (如有必要)。 

   > [!IMPORTANT]
   > 「請確定」** 您會選擇 [另存新檔]****，如此一來，所有連線參考都會在已升級的邏輯應用程式中保持有效狀態。

3. 在升級的刀鋒視窗工具列中，選擇 [另存新檔]****。

4. 輸入邏輯名稱和狀態。 若要部署已升級的邏輯應用程式，選擇 [建立]****。

5. 確認升級的邏輯應用程式如預期般運作。
   
   > [!NOTE]
   > 如果您要使用手動或要求觸發程序，新的邏輯應用程式中的回呼 URL 將會變更。 測試新的 URL 以確定端對端經驗運作。 若要保留先前的 URL，您可以在現有邏輯應用程式上複製。

6. 選擇性** 若要使用新的結構描述版本覆寫先前的邏輯應用程式，請在工具列上選擇 [更新結構描述]**** 旁的 [複製]****。 如果您想要保留邏輯應用程式的相同資源識別碼，或要求觸發程序 URL，此步驟才有必要。

## <a name="upgrade-tool-notes"></a>升級工具注意事項

### <a name="mapping-conditions"></a>對應條件

在升級的定義中，此工具會盡力將 true 和 false 分支的動作群組在一起成為一個範圍。 明確地說，`@equals(actions('a').status, 'Skipped')` 的設計工具模式會顯示為 `else` 動作。 不過，如果工具偵測到無法辨識的模式，則工具可能會為 true 和 false 分支建立不同的條件。 如有必要，您可以在升級之後重新對應動作。

#### <a name="foreach-loop-with-condition"></a>'foreach' 迴圈與條件

在新的結構描述中，您可以使用篩選動作來複寫使用 **For each** 迴圈 (其中每個項目有一個條件) 的模式。 不過，變更會在您升級時自動發生。 條件會變成要在 **For each** 迴圈之前出現的篩選動作，只傳回符合條件的項目陣列，並將該陣列會傳遞至 **For each** 動作。 如需範例，請參閱[迴圈和範圍](./logic-apps-control-flow-loops.md)。

### <a name="resource-tags"></a>資源標籤

在升級之後，會移除資源標籤，因此您必須加以重設以升級工作流程。

## <a name="other-changes"></a>其他變更

### <a name="renamed-manual-trigger-to-request-trigger"></a>將 'manual' 觸發程序重新命名為 'request' 觸發程序

`manual` 觸發程序類型已被取代，並重新命名為 `request` 類型 `http`。 這項變更會針對觸發程序用來建置的模式類型建立較佳的一致性。

### <a name="new-filter-action"></a>新的「篩選」動作

若要將大型陣列篩選至較少的項目集，新的 `filter` 類型會接受陣列和條件、評估每個項目的條件，並傳回具有符合條件之項目的陣列。

### <a name="restrictions-for-foreach-and-until-actions"></a>'foreach' 和 'until' 動作限制

`foreach` 和 `until` 迴圈僅限用於單一動作。

### <a name="new-trackedproperties-for-actions"></a>動作的新 'trackedProperties'

動作現在可以有一個額外的屬性，稱為 `trackedProperties`，這是 `runAfter` 和 `type` 屬性的同層級。 此物件會指定要包含在工作流程期間所發出的 Azure 診斷遙測之特定動作的輸入或輸出。 例如：

``` json
{
   "Http": {
      "inputs": {
         "method": "GET",
         "uri": "https://www.bing.com"
      },
      "runAfter": {},
      "type": "Http",
      "trackedProperties": {
         "responseCode": "@action().outputs.statusCode",
         "uri": "@action().inputs.uri"
      }
   }
}
```

## <a name="next-steps"></a>後續步驟

* [建立邏輯應用程式的工作流程定義](../logic-apps/logic-apps-author-definitions.md)
* [自動部署邏輯應用程式](logic-apps-azure-resource-manager-templates-overview.md)

<!-- Image references -->
[1]: ./media/logic-apps-schema-2016-04-01/upgradeButton.png
