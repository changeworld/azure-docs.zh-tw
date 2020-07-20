---
title: Azure Durable Functions 中的函數類型
description: 深入瞭解在 Azure Functions 的 Durable Functions 協調流程中，支援函式對函式通訊的函數和角色類型。
author: cgillum
ms.topic: conceptual
ms.date: 08/22/2019
ms.author: azfuncdf
ms.openlocfilehash: 35ef9d8731e169e890f5985ce01215fec5d6e3de
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "84697702"
---
# <a name="durable-functions-types-and-features"></a>Durable Functions 類型和功能

Durable Functions 是 [Azure Functions](../functions-overview.md) 的擴充功能。 您可以使用 Durable Functions 來執行函式的具狀態協調流程。 長期函數應用程式是由不同 Azure 函式所組成的解決方案。 函數可以在長期函式協調流程中扮演不同的角色。 

Azure Functions 中目前有四個長期函式類型： activity、orchestrator、entity 和 client。 本節的其餘部分將詳細說明協調流程中涉及的函數類型。

## <a name="orchestrator-functions"></a>協調器函式

協調器函式會描述動作的執行方式，以及執行動作的順序。 協調器函式會以程式碼（c # 或 JavaScript）來描述協調流程，如[Durable Functions 應用程式模式](durable-functions-overview.md#application-patterns)所示。 協調流程可以有許多不同類型的動作，包括[活動](#activity-functions)函[式、子協調流程](durable-functions-orchestrations.md#sub-orchestrations)、[等待外來事件](durable-functions-orchestrations.md#external-events)、 [HTTP](durable-functions-http-features.md)和[計時器](durable-functions-orchestrations.md#durable-timers)。 協調器函式也可以與[實體](#entity-functions)函式互動。

> [!NOTE]
> 協調器函式是使用一般程式碼撰寫的，但對於撰寫程式碼的方式有嚴格的要求。 具體而言，協調器函式程式碼必須具*決定性*。 若無法遵循這些確定性需求，可能會導致協調器功能無法正常執行。 如需這些需求的詳細資訊以及如何解決這些問題，請參閱程式[代碼條件約束](durable-functions-code-constraints.md)主題。

如需有關 orchestrator 函式及其功能的詳細資訊，請參閱長期協調[流程](durable-functions-orchestrations.md)一文。

## <a name="activity-functions"></a>活動函式

活動函數是長期函式協調流程中的基本工作單位。 活動函式是在進程中協調的函式和工作。 例如，您可以建立協調器函式來處理訂單。 這些工作牽涉到檢查清查、向客戶收費，以及建立出貨。 每個工作都是個別的活動函式。 這些活動函數可能會以平行方式或兩者的某種組合執行。

不同于協調器函式，活動函式不會限制在您可以在其中執行的工作類型。 活動函數經常用來進行網路呼叫或執行需要大量 CPU 的作業。 活動函數也可以將資料傳回給協調器函式。 長期工作架構保證每個被呼叫的活動函數在協調流程執行期間至少會執行*一次*。

> [!NOTE]
> 由於活動功能只保證*至少一次*執行，因此建議您盡可能讓活動函式邏輯為*等冪*。

使用[活動觸發](durable-functions-bindings.md#activity-trigger)程式來定義活動函數。 .NET 函數會接收 `DurableActivityContext` 做為參數。 您也可以將觸發程式系結到任何其他的 JSON 可序列化物件，以將輸入傳遞至函式。 在 JavaScript 中，您可以透過 `<activity trigger binding name>` [ `context.bindings` 物件](../functions-reference-node.md#bindings)上的屬性來存取輸入。 活動函式只能傳遞一個值給它們。 若要傳遞多個值，您必須使用元組、陣列或複雜類型。

> [!NOTE]
> 您只能從協調器函式觸發活動函數。

## <a name="entity-functions"></a>實體函式

實體函式會定義讀取和更新較小部分狀態的作業。 我們通常會將這些具狀態實體稱為「*持久實體*」。 和協調器函式一樣，實體函式也是具有特殊觸發程序類型 (「實體觸發程序」**) 的函式。 您也可以從用戶端函式或協調器函式來叫用這些功能。 與協調器函式不同的是，實體函式沒有任何特定的程式碼條件約束。 實體函式也會明確管理狀態，而不是透過控制流程來隱含表示狀態。

> [!NOTE]
> 只有 Durable Functions 2.0 和更新版本有提供實體函式和相關功能。

如需實體函式的詳細資訊，請參閱[持久實體](durable-functions-entities.md)一文。

## <a name="client-functions"></a>用戶端函式

[協調流程觸發](durable-functions-bindings.md#orchestration-trigger)程式系結會觸發協調器函式，而實體函式是由[實體觸發](durable-functions-bindings.md#entity-trigger)程式系結所觸發。 這兩個觸發程式都是透過回應加入工作[中樞](durable-functions-task-hubs.md)的訊息來執行。 傳遞這些訊息的主要方式是從*用戶端*函式中使用[orchestrator 用戶端](durable-functions-bindings.md#orchestration-client)系結或[實體用戶端](durable-functions-bindings.md#entity-client)系結。 任何非協調器函數都可以是*用戶端*函式。 例如，您可以從 HTTP 觸發的函式、Azure 事件中樞觸發的函式等觸發協調器。函式可讓函式成為*用戶端*函式，其使用持久性用戶端輸出系結。

> [!NOTE]
> 不同于其他函式類型，無法使用 Azure 入口網站中的按鈕直接觸發 orchestrator 和 entity 函數。 如果您想要在 Azure 入口網站中測試 orchestrator 或 entity 函式，您必須改為執行*用戶端*函式，以啟動協調器或實體功能作為其執行的一部分。 為了獲得最簡單的測試經驗，建議使用*手動觸發*程式函式。

除了觸發協調器或實體函式，長期*用戶端*系結也可以用來與執行中的協調流程和實體進行互動。 例如，您可以查詢、終止協調流程，而且可以將事件引發給它們。 如需管理協調流程和實體的詳細資訊，請參閱[實例管理](durable-functions-instance-management.md)一文。

## <a name="next-steps"></a>後續步驟

若要開始使用，請使用[c #](durable-functions-create-first-csharp.md)或[JavaScript](quickstart-js-vscode.md)建立您的第一個持久性函式。

> [!div class="nextstepaction"]
> [深入瞭解 Durable Functions 協調流程](durable-functions-orchestrations.md)