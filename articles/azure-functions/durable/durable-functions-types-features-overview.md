---
title: Azure 持久函數中的函數類型
description: 瞭解在 Azure 函數中的持久函數業務流程中支援函數到函數通信的函數和角色類型。
author: cgillum
ms.topic: conceptual
ms.date: 08/22/2019
ms.author: azfuncdf
ms.openlocfilehash: 35ef9d8731e169e890f5985ce01215fec5d6e3de
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79277878"
---
# <a name="durable-functions-types-and-features"></a>耐用的功能類型和功能

Durable Functions 是 [Azure Functions](../functions-overview.md) 的擴充功能。 您可以使用持久函數進行有狀態的函數執行業務流程。 持久函數應用是由不同的 Azure 函陣列成的解決方案。 函數可以在持久函數業務流程中扮演不同的角色。 

Azure 函數中當前有四種持久函數類型：活動、協調器、實體和用戶端。 本節的其餘部分將詳細介紹業務流程中涉及的函數類型。

## <a name="orchestrator-functions"></a>協調器函式

協調器函數描述操作的執行方式和執行操作的順序。 協調器函數描述代碼（C# 或 JavaScript）中的業務流程，如[持久函數應用程式模式](durable-functions-overview.md#application-patterns)所示。 業務流程可以具有許多不同類型的操作，包括[活動函數](#activity-functions)、[子業務流程](durable-functions-orchestrations.md#sub-orchestrations)、[等待外來事件](durable-functions-orchestrations.md#external-events)[、HTTP](durable-functions-http-features.md)和[計時器](durable-functions-orchestrations.md#durable-timers)。 協調器函數還可以與[實體函數](#entity-functions)進行交互。

> [!NOTE]
> 協調器函數是使用普通代碼編寫的，但對於如何編寫代碼有嚴格的要求。 具體而言，協調器函數代碼必須是*確定性*的 。 不遵守這些確定性要求可能會導致協調器函數無法正確運行。 有關這些要求的詳細資訊以及如何解決這些問題，請參閱[代碼約束](durable-functions-code-constraints.md)主題。

有關協調器函數及其功能的更多詳細資訊，請參閱[持久業務流程一](durable-functions-orchestrations.md)文。

## <a name="activity-functions"></a>活動函式

活動函數是持久函數編排中的基本工作單元。 活動函數是流程中編排的函數和任務。 例如，您可以創建協調器函數來處理訂單。 這些任務包括檢查庫存、向客戶收費和創建貨件。 每個任務將是一個單獨的活動函數。 這些活動函數可以串列、並存執行或兩者的某種組合執行。

與協調器函數不同，活動函數不受可在其中執行的工作類型的限制。 活動函數通常用於進行網路調用或運行 CPU 密集型操作。 活動函數還可以將資料返回給協調器函數。 持久任務框架保證每個調用的活動函數在業務流程執行期間至少執行一*次*。

> [!NOTE]
> 由於活動函數僅保證*至少執行一次*，因此我們建議您盡可能使活動函數邏輯*具有冪等作用*。

使用[活動觸發器](durable-functions-bindings.md#activity-trigger)定義活動函數。 .NET 函數作為`DurableActivityContext`參數接收 。 您還可以將觸發器綁定到任何其他可觸發的 JSON 可序列化物件，以將輸入傳遞到函數。 在 JavaScript 中，您可以通過[`context.bindings`物件](../functions-reference-node.md#bindings)上的`<activity trigger binding name>`屬性訪問輸入。 活動函數只能將單個值傳遞給它們。 要傳遞多個值，必須使用元組、陣列或複雜類型。

> [!NOTE]
> 只能從協調器函數觸發活動函數。

## <a name="entity-functions"></a>實體函式

實體函式定義用於讀取和更新小段狀態的操作。 我們經常將這些有狀態的實體稱為*持久實體*。 和協調器函式一樣，實體函式也是具有特殊觸發程序類型 (「實體觸發程序」**) 的函式。 也可以從用戶端函數或協調器函式呼叫它們。 與協調器函數不同，實體函數沒有任何特定的代碼約束。 實體函式也會明確管理狀態，而不是透過控制流程來隱含表示狀態。

> [!NOTE]
> 只有 Durable Functions 2.0 和更新版本有提供實體函式和相關功能。

有關實體函數的詳細資訊，請參閱[持久實體](durable-functions-entities.md)一文。

## <a name="client-functions"></a>用戶端函式

協調器函數由[業務流程觸發器綁定](durable-functions-bindings.md#orchestration-trigger)觸發，實體函數由[實體觸發器綁定](durable-functions-bindings.md#entity-trigger)觸發。 這兩個觸發器都通過回應排隊到[任務中心](durable-functions-task-hubs.md)的消息來工作。 傳遞這些消息的主要方法是使用[業務流程中的協調器用戶端綁定](durable-functions-bindings.md#orchestration-client)或[實體用戶端綁定](durable-functions-bindings.md#entity-client)*。* 任何非協調器函數都可以是*用戶端函數*。 例如，可以從 HTTP 觸發的函數、Azure 事件中心觸發函數等觸發協調器。使函數成為*用戶端函數*的是使用持久用戶端輸出綁定。

> [!NOTE]
> 與其他函數類型不同，協調器和實體函數不能直接使用 Azure 門戶中的按鈕觸發。 如果要在 Azure 門戶中測試協調器或實體函數，則必須運行*用戶端函數，該用戶端函數*將啟動協調器或實體函數作為其實現的一部分。 對於最簡單的測試體驗，建議使用*手動觸發*功能。

除了觸發協調器或實體函數外，*持久用戶端*綁定還可用於與正在運行的業務流程和實體進行交互。 例如，可以查詢、終止業務流程，也可以將事件引發到它們。 有關管理業務流程和實體的詳細資訊，請參閱[實例管理](durable-functions-instance-management.md)一文。

## <a name="next-steps"></a>後續步驟

要開始，請用[C#](durable-functions-create-first-csharp.md)或[JavaScript](quickstart-js-vscode.md)創建第一個持久函數。

> [!div class="nextstepaction"]
> [閱讀有關持久函數業務流程的更多內容](durable-functions-orchestrations.md)