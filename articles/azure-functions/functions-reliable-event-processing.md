---
title: Azure 函數可靠的事件處理
description: 避免 Azure 函數中缺少事件中心消息
author: craigshoemaker
ms.topic: conceptual
ms.date: 09/12/2019
ms.author: cshoe
ms.openlocfilehash: e4f35495d8a01146068cffb9159c29c46c3c0d29
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "75561862"
---
# <a name="azure-functions-reliable-event-processing"></a>Azure 函數可靠的事件處理

事件處理是與無伺服器體系結構關聯的最常見的方案之一。 本文介紹如何使用 Azure 函數創建可靠的消息處理器，以避免丟失消息。

## <a name="challenges-of-event-streams-in-distributed-systems"></a>分散式系統中事件流的挑戰

考慮以每秒 100 個事件的恒定速率發送事件的系統。 在此速率下，在幾分鐘內，多個並行函數實例每秒可以使用傳入的 100 個事件。

但是，以下任何不太理想的條件都是可能的：

- 如果事件發行者發送損壞的事件怎麼辦？
- 如果函數實例遇到未處理的異常，該怎麼辦？
- 如果下游系統離線怎麼辦？

在保留應用程式的輸送量的同時，如何處理這些情況？

使用佇列時，可靠的消息傳遞自然。 當與函數觸發器配對時，函數會在佇列消息上創建鎖。 如果處理失敗，將釋放鎖以允許另一個實例重試處理。 然後，繼續處理，直到消息被成功計算，或者將其添加到毒佇列中。

即使單個佇列消息可能仍停留在重試週期中，但其他並存執行仍將繼續對剩餘消息進行清除佇列。 結果是，總體輸送量基本上不受一個不良消息的影響。 但是，存儲佇列不保證排序，並且未針對事件中心所需的高輸送量需求進行優化。

相比之下，Azure 事件中心不包括鎖定概念。 為了允許高通量、多個消費者組和重播能力等功能，事件中心事件更像視頻播放機。 事件從每個分區流中的單個點讀取。 從指標中，可以從該位置向前或向後讀取，但必須選擇移動指標以處理事件。

當流中發生錯誤時，如果您決定將指標保留在同一點上，則事件處理將被阻止，直到指標被前進。 換句話說，如果指標停止以處理處理單個事件的問題，則未處理的事件將開始堆積。

Azure 函數通過推進流的指標來避免鎖死，而不管成功還是失敗。 由於指標不斷前進，您的函數需要適當地處理故障。

## <a name="how-azure-functions-consumes-event-hubs-events"></a>Azure 函數如何使用事件中心事件

Azure 函數在迴圈執行以下步驟時使用事件中心事件：

1. 為事件中心的每個分區在 Azure 存儲中創建並持久化指標。
2. 收到新消息時（預設情況下在批次處理中），主機嘗試使用消息批次處理觸發函數。
3. 如果函數完成執行（有或無例外），指標前進，檢查點將保存到存儲帳戶。
4. 如果條件阻止函數執行完成，則主機無法執行指標。 如果指標未前進，則稍後的檢查最終將處理相同的消息。
5. 重複步驟 2⁄4

此行為揭示了幾個要點：

- *未處理的異常可能會導致您丟失消息。* 導致異常的執行將繼續推進指標。
- *功能保證至少一次交付。* 您的代碼和從屬系統可能需要[考慮同一消息可以接收兩次這一事實](./functions-idempotent.md)。

## <a name="handling-exceptions"></a>處理例外狀況

通常，每個函數都應在最高級別的代碼中包括[try/catch 塊](./functions-bindings-error-pages.md)。 具體而言，使用事件中心事件的所有函數都應具有塊`catch`。 這樣，當引發異常時，catch 塊在指標進行之前處理錯誤。

### <a name="retry-mechanisms-and-policies"></a>重試機制和政策

某些異常本質上是暫時性的，在稍後再次嘗試操作時不會重新出現。 這就是為什麼第一步始終是重試該操作的原因。 您可以自己編寫重試處理規則，但它們非常常見，以至於有許多工具可用。 使用這些庫可以定義可靠的重試策略，這也有助於維護處理順序。

將故障處理庫引入函數允許您定義基本和高級重試策略。 例如，您可以實現遵循以下規則所示的工作流的策略：

- 嘗試插入消息三次（可能在重試之間延遲）。
- 如果所有重試的最終結果都是失敗，則向佇列添加消息，以便處理可以繼續在流上。
- 稍後處理損壞或未處理的郵件。

> [!NOTE]
> [Polly](https://github.com/App-vNext/Polly)是 C# 應用程式的彈性和瞬態故障處理庫的示例。

使用預遵守的 C# 類庫時，[異常篩選器](https://docs.microsoft.com/dotnet/csharp/language-reference/keywords/try-catch)允許您在發生未處理的異常時運行代碼。

演示如何使用異常篩選器的示例在[Azure WebJobs SDK](https://github.com/Azure/azure-webjobs-sdk/wiki)回購中可用。

## <a name="non-exception-errors"></a>非異常錯誤

即使不存在錯誤，也會出現一些問題。 例如，考慮在執行期間發生的失敗。 在這種情況下，如果函數未完成執行，則偏移指標永遠不會進行。 如果指標未前進，則在執行失敗後運行的任何實例將繼續讀取相同的消息。 這種情況提供了"至少一次"保證。

保證每條消息至少處理一次，這意味著某些消息可能會處理多次。 您的功能應用程式需要意識到這種可能性，並且必須圍繞[陽萎原則](./functions-idempotent.md)構建。

## <a name="stop-and-restart-execution"></a>停止並重新啟動執行

雖然一些錯誤是可以接受的，但如果你的應用遇到重大故障怎麼辦？ 您可能希望在系統達到正常狀態之前停止觸發事件。 斷路器模式通常能夠有機會暫停處理。 斷路器模式允許你的應用"打破"事件程序的電路，並在以後恢復。

在事件程序中實現斷路器需要兩件：

- 跨所有實例的共用狀態，用於跟蹤和監視電路的運行狀況
- 可管理電路狀態的主過程（打開或關閉）

實現詳細資訊可能有所不同，但要在實例之間共用狀態，您需要一個存儲機制。 您可以選擇在 Azure 存儲、Redis 緩存或函數集合可訪問的任何其他帳戶中存儲狀態。

[Azure 邏輯應用](../logic-apps/logic-apps-overview.md)或[持久實體](./durable/durable-functions-overview.md)非常適合管理工作流和電路狀態。 其他服務可能也一樣，但邏輯應用用於此示例。 使用邏輯應用，您可以暫停並重新啟動函數的執行，從而獲得實現斷路器模式所需的控制。

### <a name="define-a-failure-threshold-across-instances"></a>跨實例定義故障閾值

為了同時考慮多個實例處理事件，需要持久化共用外部狀態來監視電路的運行狀況。

您可以選擇實現的規則可能會強制實施：

- 如果所有實例在 30 秒內出現 100 多個最終故障，則斷開電路並停止觸發新消息。

實現詳細資訊將根據您的需求而變化，但通常您可以創建一個系統：

1. 日誌故障到存儲帳戶（Azure 存儲、雷瑞斯等）
1. 記錄新故障時，檢查滾動計數以查看是否滿足閾值（例如，在過去 30 秒內超過 100 個）。
1. 如果達到閾值，則向 Azure 事件網格發出事件，告訴系統斷開電路。

### <a name="managing-circuit-state-with-azure-logic-apps"></a>使用 Azure 邏輯應用管理電路狀態

以下說明突出顯示了創建 Azure 邏輯應用以阻止函數應用處理的一種方式。

Azure 邏輯應用隨內置連接器連接到不同的服務，具有有狀態的業務流程，是管理電路狀態的自然選擇。 檢測電路需要斷開後，可以構建邏輯應用以實現以下工作流：

1. 觸發事件網格工作流並停止 Azure 函數（使用 Azure 資源連接器）
1. 發送通知電子郵件，其中包含重新開機工作流的選項

電子郵件收件者可以調查電路的運行狀況，並在適當時通過通知電子郵件中的連結重新開機電路。 當工作流重新開機函數時，將從最後一個事件中心檢查點處理消息。

使用此方法，不會丟失任何消息，所有消息都按連續處理，並且只要必要，就可以中斷電路。

## <a name="resources"></a>資源

- [可靠的事件處理示例](https://github.com/jeffhollan/functions-csharp-eventhub-ordered-processing)
- [Azure 持久功能斷路器](https://github.com/jeffhollan/functions-durable-actor-circuitbreaker)

## <a name="next-steps"></a>後續步驟

如需詳細資訊，請參閱下列資源：

- [Azure 函式錯誤處理](./functions-bindings-error-pages.md)
- [使用 Event Grid 自動調整已上傳映像的大小](../event-grid/resize-images-on-storage-blob-upload-event.md?toc=%2Fazure%2Fazure-functions%2Ftoc.json&tabs=dotnet)
- [建立與 Azure Logic Apps 整合的函式](./functions-twitter-email.md)
