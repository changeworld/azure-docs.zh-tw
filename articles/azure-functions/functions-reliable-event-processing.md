---
title: Azure Functions 可靠的事件處理
description: 避免在 Azure Functions 中遺失事件中樞訊息
author: craigshoemaker
ms.topic: conceptual
ms.date: 09/12/2019
ms.author: cshoe
ms.openlocfilehash: fe5efd2bf4c235688aad90ae37b54268d290540c
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "84676126"
---
# <a name="azure-functions-reliable-event-processing"></a>Azure Functions 可靠的事件處理

事件處理是與無伺服器架構相關聯的最常見案例之一。 本文說明如何使用 Azure Functions 建立可靠的訊息處理器，以避免遺失訊息。

## <a name="challenges-of-event-streams-in-distributed-systems"></a>分散式系統中事件資料流程的挑戰

假設系統以每秒100個事件的固定速率傳送事件。 在幾分鐘內，多個平行函式實例每秒可以取用傳入的100事件。

不過，下列任何較不理想的情況都是可行的：

- 如果事件發行者傳送損毀事件該怎麼辦？
- 如果您的函式實例遇到未處理的例外狀況，該怎麼辦？
- 如果下游系統離線，該怎麼辦？

您要如何處理這些情況，同時保留應用程式的輸送量？

使用佇列時，可靠的訊息傳遞自然。 與函式觸發程式配對時，函式會在佇列訊息上建立鎖定。 如果處理失敗，則會釋放鎖定，以允許另一個實例重試處理。 然後，處理會繼續進行，直到成功評估訊息，或將它新增至有害佇列為止。

即使單一佇列訊息可能會保留在重試迴圈中，其他平行執行仍會繼續保留以清除佇列剩餘的訊息。 結果是，整體輸送量主要不會受到一則錯誤訊息的影響。 不過，儲存體佇列不保證排序，而且也不會針對事件中樞所需的高輸送量需求進行優化。

相較之下，Azure 事件中樞不會包含鎖定概念。 為了允許高輸送量、多個取用者群組和重新執行能力等功能，事件中樞事件的行為會比影片播放程式更像。 事件會從每個分割區的資料流程中的單一點讀取。 從您可以從該位置向前或向後讀取的指標，但您必須選擇將事件的指標移至進程。

當資料流程中發生錯誤時，如果您決定將指標保持在相同的位置，事件處理將會遭到封鎖，直到指標為 advanced 為止。 換句話說，如果指標因為處理單一事件而停止處理問題，則未處理的事件就會開始堆積。

Azure Functions 藉由推進資料流程的指標（不論成功或失敗）來避免鎖死。 由於指標會持續前進，因此您的函式需要適當地處理失敗。

## <a name="how-azure-functions-consumes-event-hubs-events"></a>Azure Functions 如何使用事件中樞事件

Azure Functions 會在迴圈執行下列步驟時取用事件中樞事件：

1. 針對事件中樞的每個分割區，會在 Azure 儲存體中建立並保存指標。
2. 收到新訊息時（預設為批次），主機會嘗試使用訊息批次觸發函式。
3. 如果函式完成執行（包含或不含例外狀況），則指標會前進，而檢查點會儲存至儲存體帳戶。
4. 如果條件導致函式無法執行完成，主機就無法進行指標。 如果指標不是最先進的，則稍後檢查最後會處理相同的訊息。
5. 重複步驟 2-4

此行為會顯示幾個重點：

- *未處理的例外狀況可能會導致您遺失訊息。* 導致例外狀況的執行會繼續進行指標。
- *函數可保證至少一次傳遞。* 您的程式碼和相依系統可能需要[考慮可以接收兩次相同訊息的情況](./functions-idempotent.md)。

## <a name="handling-exceptions"></a>處理例外狀況

一般的規則是，每個函式都應該在最高層級的程式碼中包含[try/catch 區塊](./functions-bindings-error-pages.md)。 具體而言，使用事件中樞事件的所有函式都應該有一個 `catch` 區塊。 如此一來，當引發例外狀況時，catch 區塊會在指標進行前處理錯誤。

### <a name="retry-mechanisms-and-policies"></a>重試機制和原則

有些例外狀況本質上是暫時性的，而且當稍後嘗試操作時，並不會再次出現。 這就是為什麼第一個步驟一律會重試作業的原因。 您可以自行撰寫重試處理規則，但它們很普遍會有許多可用的工具。 使用這些程式庫可讓您定義健全的重試原則，這也有助於保留處理順序。

將錯誤處理程式庫引進至您的函式，可讓您定義基本和先進的重試原則。 例如，您可以執行原則，遵循下列規則所述的工作流程：

- 嘗試插入訊息三次（可能會在重試之間有延遲）。
- 如果所有重試的最終結果都是失敗，則將訊息新增至佇列，以便可以繼續在資料流程上進行處理。
- 之後會處理已損毀或未處理的訊息。

> [!NOTE]
> [Polly](https://github.com/App-vNext/Polly)是 c # 應用程式的復原和暫時性錯誤處理程式庫的範例。

使用預先編譯的 c # 類別庫時，[例外狀況篩選準則](https://docs.microsoft.com/dotnet/csharp/language-reference/keywords/try-catch)可讓您在每次發生未處理的例外狀況時執行程式碼。

[AZURE WEBJOBS SDK](https://github.com/Azure/azure-webjobs-sdk/wiki)存放庫中提供示範如何使用例外狀況篩選準則的範例。

## <a name="non-exception-errors"></a>非例外狀況錯誤

即使錯誤不存在，還是會發生一些問題。 例如，請考慮在執行過程中發生的失敗。 在此情況下，如果函式未完成執行，位移指標永遠不會進展。 如果指標未前進，則在失敗執行之後執行的任何實例會繼續讀取相同的訊息。 這種情況會提供「至少一次」保證。

保證每個訊息至少會被處理一次，表示某些訊息可能會處理一次以上。 您的函數應用程式需要留意這種可能性，而且必須圍繞等冪性的[原則](./functions-idempotent.md)來建立。

## <a name="stop-and-restart-execution"></a>停止並重新啟動執行

雖然有幾個錯誤可能是可接受的，但如果您的應用程式遇到重大失敗，該怎麼辦？ 您可能想要停止觸發事件，直到系統達到狀況良好狀態為止。 有機會暫停處理，通常是透過斷路器模式來達成。 斷路器模式可讓您的應用程式「中斷」事件進程的線路，並于稍後繼續。

在事件處理常式中執行斷路器需要兩個部分：

- 所有實例之間的共用狀態，用以追蹤和監視線路的健全狀況
- 可管理線路狀態（開啟或關閉）的主要進程

執行詳細資料可能會有所不同，但若要在實例之間共用狀態，您需要有儲存機制。 您可以選擇將狀態儲存在 Azure 儲存體、Redis 快取，或可由函式集合存取的任何其他帳戶。

[Azure Logic Apps](../logic-apps/logic-apps-overview.md)或長期函式是用來管理工作流程和線路[狀態的自然](./durable/durable-functions-overview.md)調整。 其他服務也可以運作，但此範例中使用的是邏輯應用程式。 使用邏輯應用程式時，您可以暫停和重新開機函式的執行，讓您能夠控制執行斷路器模式所需的控制項。

### <a name="define-a-failure-threshold-across-instances"></a>定義實例間的失敗臨界值

若要將多個實例同時處理事件，需要保存共用外部狀態，才能監視線路的健全狀況。

您可以選擇執行的規則可能會強制執行下列動作：

- 如果在所有實例的30秒內有超過100的最終失敗，則會中斷線路並停止觸發新訊息。

執行詳細資料會因您的需求而有所不同，但一般而言，您可以建立一個系統來執行下列動作：

1. 將失敗記錄到儲存體帳戶（Azure 儲存體、Redis 等）
1. 記錄新的失敗時，檢查迴圈計數以查看是否符合閾值（例如，過去30秒內超過100）。
1. 如果符合閾值，會向 Azure 事件方格發出事件，告訴系統中斷線路。

### <a name="managing-circuit-state-with-azure-logic-apps"></a>使用 Azure Logic Apps 管理線路狀態

下列描述會特別說明您可以建立 Azure 邏輯應用程式，讓函式應用程式停止處理的其中一種方式。

Azure Logic Apps 隨附不同服務的內建連接器、功能具狀態的協調流程，而且是管理線路狀態的自然選擇。 在偵測到需要中斷的電路之後，您可以建立邏輯應用程式來執行下列工作流程：

1. 觸發事件方格工作流程並停止 Azure Function （使用 Azure 資源連接器）
1. 傳送通知電子郵件，其中包含重新開機工作流程的選項

電子郵件收件者可以調查線路的健全狀況，並在適當的情況下，透過通知電子郵件中的連結重新開機線路。 當工作流程重新開機函式時，訊息會從最後一個事件中樞檢查點進行處理。

使用這種方法時，不會遺失任何訊息，所有訊息都會依序處理，而您可以視需要中斷線路。

## <a name="resources"></a>資源

- [可靠的事件處理範例](https://github.com/jeffhollan/functions-csharp-eventhub-ordered-processing)
- [Azure 持久性實體斷路器](https://github.com/jeffhollan/functions-durable-actor-circuitbreaker)

## <a name="next-steps"></a>後續步驟

如需詳細資訊，請參閱下列資源：

- [Azure 函式錯誤處理](./functions-bindings-error-pages.md)
- [使用 Event Grid 自動調整已上傳映像的大小](../event-grid/resize-images-on-storage-blob-upload-event.md?toc=%2Fazure%2Fazure-functions%2Ftoc.json&tabs=dotnet)
- [建立與 Azure Logic Apps 整合的函式](./functions-twitter-email.md)
