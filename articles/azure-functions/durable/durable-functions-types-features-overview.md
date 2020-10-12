---
title: Azure Durable Functions 中的函數類型
description: 瞭解在 Azure Functions 的 Durable Functions 協調流程中，支援函式通訊的函數和角色類型。
author: cgillum
ms.topic: conceptual
ms.date: 08/22/2019
ms.author: azfuncdf
ms.openlocfilehash: 35ef9d8731e169e890f5985ce01215fec5d6e3de
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "84697702"
---
# <a name="durable-functions-types-and-features"></a>Durable Functions 類型和功能

Durable Functions 是 [Azure Functions](../functions-overview.md) 的擴充功能。 您可以使用 Durable Functions 來執行函式的可設定狀態協調流程。 持久函式應用程式是由不同 Azure 函式所組成的解決方案。 函數可以在持久函式協調流程中扮演不同的角色。 

目前 Azure Functions 中有四個長期函數類型：活動、協調器、實體和用戶端。 本節的其餘部分將詳細說明協調流程中涉及的函數類型。

## <a name="orchestrator-functions"></a>協調器函式

協調器函式描述動作的執行方式，以及動作的執行順序。 協調器函式會以程式碼 (c # 或 JavaScript) 描述協調流程，如 [Durable Functions 應用程式模式](durable-functions-overview.md#application-patterns)所示。 協調流程可以有許多不同類型的動作，包括 [活動](#activity-functions)函 [式、子協調流程](durable-functions-orchestrations.md#sub-orchestrations)、 [等候外來事件](durable-functions-orchestrations.md#external-events)、 [HTTP](durable-functions-http-features.md)和 [計時器](durable-functions-orchestrations.md#durable-timers)。 協調器函式也可以與 [實體函數](#entity-functions)互動。

> [!NOTE]
> 協調器函式是使用一般程式碼撰寫的，但在撰寫程式碼時有嚴格的需求。 具體而言，協調器函式程式碼必須具 *決定性*。 若無法遵循這些確定性需求，可能會導致協調器函式無法正常執行。 這些需求的詳細資訊，以及如何解決這些需求的詳細資訊，請參閱程式 [代碼條件約束](durable-functions-code-constraints.md) 主題。

如需協調器函式和其功能的詳細資訊，請參閱長期協調 [流程](durable-functions-orchestrations.md) 文章。

## <a name="activity-functions"></a>活動函式

活動函式是長期函數協調流程中的基本工作單位。 活動函式是在進程中協調的函式和工作。 例如，您可以建立協調器函數來處理訂單。 這些工作牽涉到檢查清查、向客戶收費，以及建立出貨。 每項工作都是個別的活動函式。 這些活動函式可能會以平行方式執行，或兩者的組合。

與協調器函式不同的是，活動函式不會限制在您可以在其中執行的工作類型。 活動函式經常用來進行網路呼叫或執行需要大量 CPU 的作業。 活動函式也可以將資料傳回給協調器函式。 「長期工作架構」保證每個呼叫的活動函式在協調流程執行期間至少會執行 *一次* 。

> [!NOTE]
> 因為活動函式只保證 *至少執行一次* ，所以建議您盡可能讓活動函式邏輯 *等冪* 。

使用 [活動觸發](durable-functions-bindings.md#activity-trigger) 程式來定義活動函式。 .NET 函式會收到 `DurableActivityContext` 做為參數的。 您也可以將觸發程式系結至任何其他 JSON 可序列化物件，以將輸入傳遞至函式。 在 JavaScript 中，您可以透過 `<activity trigger binding name>` [ `context.bindings` 物件](../functions-reference-node.md#bindings)上的屬性來存取輸入。 活動函式只能有傳遞給它們的單一值。 若要傳遞多個值，您必須使用元組、陣列或複雜類型。

> [!NOTE]
> 您只能從協調器函式觸發活動函式。

## <a name="entity-functions"></a>實體函式

實體函式會定義讀取和更新一小段狀態的作業。 我們通常會將這些具狀態實體稱為 *持久性實體*。 和協調器函式一樣，實體函式也是具有特殊觸發程序類型 (「實體觸發程序」**) 的函式。 您也可以從用戶端函式或從協調器函式叫用這些函數。 不同于協調器函式，實體函數沒有任何特定的程式碼條件約束。 實體函式也會明確管理狀態，而不是透過控制流程來隱含表示狀態。

> [!NOTE]
> 只有 Durable Functions 2.0 和更新版本有提供實體函式和相關功能。

如需實體函式的詳細資訊，請參閱長期 [實體](durable-functions-entities.md) 文章。

## <a name="client-functions"></a>用戶端函式

[協調流程觸發](durable-functions-bindings.md#orchestration-trigger)程式系結會觸發協調器函式，而實體函式是由[實體觸發](durable-functions-bindings.md#entity-trigger)程式系結所觸發。 這兩個觸發程式的運作方式，是回應加入工作 [中樞](durable-functions-task-hubs.md)的訊息。 傳遞這些訊息的主要方式是從*用戶端*函式中使用[協調器用戶端](durable-functions-bindings.md#orchestration-client)系結或[實體用戶端](durable-functions-bindings.md#entity-client)系結。 任何非協調器函數都可以是 *用戶端*函式。 例如，您可以從 HTTP 觸發的函式、Azure 事件中樞觸發的函式觸發協調器等等。讓函式成為 *用戶端* 函式是使用永久性用戶端輸出系結的功能。

> [!NOTE]
> 與其他函式類型不同的是，您無法使用 Azure 入口網站中的按鈕直接觸發 orchestrator 和 entity 函數。 如果您想要在 Azure 入口網站中測試 orchestrator 或 entity 函式，您必須改為執行 *用戶端* 函式，以啟動 orchestrator 或 entity 函式作為其執行的一部分。 為了最簡單的測試體驗，建議使用 *手動觸發* 程式函數。

除了觸發協調器或實體函式之外，還可以使用長期 *用戶端* 系結來與執行中的協調流程和實體互動。 例如，可以查詢、終止協調流程，而且可以將事件引發給它們。 如需管理協調流程和實體的詳細資訊，請參閱 [實例管理](durable-functions-instance-management.md) 文章。

## <a name="next-steps"></a>接下來的步驟

若要開始使用，請以 [c #](durable-functions-create-first-csharp.md) 或 [JavaScript](quickstart-js-vscode.md)建立您的第一個持久函式。

> [!div class="nextstepaction"]
> [深入瞭解 Durable Functions 協調流程](durable-functions-orchestrations.md)