---
title: Azure Functions 可靠的事件處理
description: 避免 Azure Functions 中遺失事件中樞訊息
author: craigshoemaker
ms.topic: conceptual
ms.date: 10/01/2020
ms.author: cshoe
ms.openlocfilehash: fd784bb184ff9432efc569ac9fd40de93eec0b53
ms.sourcegitcommit: 0d171fe7fc0893dcc5f6202e73038a91be58da03
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/05/2020
ms.locfileid: "93379582"
---
# <a name="azure-functions-reliable-event-processing"></a>Azure Functions 可靠的事件處理

事件處理是與無伺服器架構相關的其中一個最常見案例。 本文說明如何使用 Azure Functions 建立可靠的訊息處理器，以避免遺失訊息。

## <a name="challenges-of-event-streams-in-distributed-systems"></a>分散式系統中事件串流的挑戰

假設系統會以每秒100個事件的固定速率來傳送事件。 以這個速率來說，在幾分鐘內，多個平行函式實例每秒可以取用內送的100事件。

但是，下列任何一項較不理想的情況都可以：

- 如果事件發行者傳送損毀事件，該怎麼辦？
- 如果您的函式實例遇到未處理的例外狀況，該怎麼辦？
- 如果下游系統離線，該怎麼辦？

您要如何處理這些情況，同時保留應用程式的輸送量？

使用佇列時，可靠的訊息自然就會出現。 與函式觸發程式搭配使用時，函式會建立佇列訊息的鎖定。 如果處理失敗，則會釋放鎖定，讓另一個實例重試處理。 然後，處理作業會繼續進行，直到成功評估訊息，或將它新增至有害佇列為止。

即使單一佇列訊息可能仍在重試迴圈中，其他平行執行仍會繼續保留清除佇列剩餘的訊息。 結果是，整體輸送量會維持不受一則錯誤訊息的影響。 不過，儲存體佇列不保證順序，也不會針對事件中樞所需的高輸送量要求進行優化。

相反地，Azure 事件中樞不包含鎖定概念。 若要允許高輸送量、多個取用者群組和重新執行功能等功能，事件中樞事件的行為就像是影片播放程式。 每個資料分割的資料流程中的單一點都會讀取事件。 從您可以從該位置向前或向後讀取的指標，但您必須選擇移動事件的指標以進行處理。

當資料流程中發生錯誤時，如果您決定將指標放在相同的位置，事件處理會遭到封鎖，直到指標前進為止。 換句話說，如果指標停止處理單一事件的問題，未處理的事件就會開始堆積。

Azure Functions 藉由在成功或失敗的情況中向前移動資料流程的指標，來避免鎖死。 因為指標不斷前進，所以您的函式需要適當地處理失敗。

## <a name="how-azure-functions-consumes-event-hubs-events"></a>Azure Functions 如何使用事件中樞事件

Azure Functions 在迴圈執行下列步驟時，會取用事件中樞事件：

1. 在事件中樞的每個分割區中，會建立並保存 Azure 儲存體的指標。
2. 根據預設，在批次中收到新的訊息 () ，主機會嘗試以訊息批次觸發函數。
3. 如果函式完成執行 (不含例外狀況) 指標前進和檢查點會儲存至儲存體帳戶。
4. 如果條件防止函式執行完成，則主機將無法進行指標的進度。 如果指標不是先進的，稍後檢查最後會處理相同的訊息。
5. 重複步驟 2-4

此行為會顯示幾個重點：

- *未處理的例外狀況可能會導致您遺失訊息。* 導致例外狀況的執行將會繼續進行指標的進度。  設定 [重試原則](./functions-bindings-error-pages.md#retry-policies-preview) 會延遲指標的進度，直到評估整個重試原則為止。
- *函數可保證至少一次傳遞。* 您的程式碼和相依系統可能需要 [考慮相同訊息接收兩次的情況](./functions-idempotent.md)。

## <a name="handling-exceptions"></a>處理例外狀況

一般來說，每個函式都應該在最高層級的程式碼中包含 [try/catch 區塊](./functions-bindings-error-pages.md) 。 具體而言，所有使用事件中樞事件的函式都應該有一個 `catch` 區塊。 如此一來，當引發例外狀況時，catch 區塊會在指標進行之前處理錯誤。

### <a name="retry-mechanisms-and-policies"></a>重試機制和原則

有些例外狀況本質上是暫時性的，而且在稍後重試一次作業時不會重新顯示。 這就是為什麼第一個步驟一律會重試作業的原因。  您可以利用函式應用程式 [重試原則](./functions-bindings-error-pages.md#retry-policies-preview) ，或在函式執行內撰寫重試邏輯。

將錯誤處理行為引進您的函式，可讓您定義基本和先進重試原則。 例如，您可以依照下列規則所說明的工作流程來執行原則：

- 請嘗試插入訊息三次， (可能會在重試之間有延遲) 。
- 如果所有重試的最終結果都是失敗，則將訊息新增至佇列，以便可以在資料流程上繼續處理。
- 之後會處理已損毀或未處理的訊息。

> [!NOTE]
> [Polly](https://github.com/App-vNext/Polly) 是 c # 應用程式的復原和暫時性錯誤處理程式庫的範例。

## <a name="non-exception-errors"></a>非例外狀況錯誤

即使不存在錯誤，還是會發生一些問題。 例如，請考慮在執行中途發生的失敗。 在此情況下，如果函式未完成執行，則不會有位移指標的進展。 如果指標未前進，則在失敗執行之後執行的任何實例都會繼續讀取相同的訊息。 這種情況會提供「至少一次」保證。

保證每個訊息至少會被處理一次，表示某些訊息可能會被處理一次以上。 您的函式應用程式必須注意這種可能性，而且必須根據 [等冪性原則](./functions-idempotent.md)來建立。

## <a name="stop-and-restart-execution"></a>停止並重新啟動執行

雖然有幾個錯誤可能是可接受的，但如果您的應用程式遇到嚴重的失敗，會怎麼樣？ 您可能會想要停止觸發事件，直到系統到達狀況良好狀態為止。 有機會暫停處理，通常是使用斷路器模式來達成。 斷路器模式可讓您的應用程式「中斷」事件進程的線路，並于稍後繼續。

在事件處理常式中執行斷路器需要兩個部分：

- 跨所有實例的共用狀態，以追蹤和監視電路的健康情況
- 可管理電路狀態 (開啟或關閉的主要進程) 

執行的詳細資料可能會不同，但若要在實例之間共用狀態，則您需要儲存機制。 您可以選擇將狀態儲存在 Azure 儲存體、Redis 快取或函式集合可存取的任何其他帳戶中。

[Azure Logic Apps](../logic-apps/logic-apps-overview.md) 或 [長期](./durable/durable-functions-overview.md) 函式很適合用來管理工作流程和線路狀態。 其他服務也可以運作，但在此範例中會使用邏輯應用程式。 使用邏輯應用程式時，您可以暫停和重新開機函式的執行，讓您控制執行斷路器模式所需的工作。

### <a name="define-a-failure-threshold-across-instances"></a>定義跨實例的失敗臨界值

若要將多個實例同時處理事件，需要保存共用的外部狀態，才能監視電路的健康情況。

您可以選擇執行的規則可能會強制執行：

- 如果在所有實例的30秒內有超過100的最終失敗，則中斷線路並停止在新訊息上觸發。

視您的需求而異，執行的詳細資料會有所不同，但一般而言，您可以建立一個系統來：

1. 將失敗記錄至儲存體帳戶 (Azure 儲存體、Redis 等 ) 
1. 當記錄新的失敗時，請檢查輪流計數以查看是否符合閾值 (例如，過去 30) 秒內的100超過。
1. 如果達到臨界值，就會發出事件至 Azure 事件方格，告知系統中斷線路。

### <a name="managing-circuit-state-with-azure-logic-apps"></a>使用 Azure Logic Apps 管理線路狀態

下列描述強調您可以建立 Azure 邏輯應用程式，以停止函式應用程式進行處理的一種方式。

Azure Logic Apps 隨附不同服務的內建連接器、具有可設定狀態的協調流程，而且是管理電路狀態的自然選擇。 偵測到電路需要中斷之後，您可以建立邏輯應用程式來執行下列工作流程：

1. 使用 Azure 資源連接器觸發事件方格工作流程，並停止 Azure Function () 
1. 傳送通知電子郵件，其中包含重新開機工作流程的選項

電子郵件收件者可以調查線路的健康情況，並在適當時透過通知電子郵件中的連結重新開機線路。 當工作流程重新開機函式時，會從最後一個事件中樞檢查點來處理訊息。

使用這個方法時，不會遺失任何訊息，而是依序處理所有訊息，而且您可以視需要中斷線路。

## <a name="resources"></a>資源

- [可靠的事件處理範例](https://github.com/jeffhollan/functions-csharp-eventhub-ordered-processing)
- [Azure 持久性實體斷路器](https://github.com/jeffhollan/functions-durable-actor-circuitbreaker)

## <a name="next-steps"></a>後續步驟

如需詳細資訊，請參閱下列資源：

- [Azure 函式錯誤處理](./functions-bindings-error-pages.md)
- [使用 Event Grid 自動調整已上傳映像的大小](../event-grid/resize-images-on-storage-blob-upload-event.md?toc=%2Fazure%2Fazure-functions%2Ftoc.json&tabs=dotnet)
- [建立與 Azure Logic Apps 整合的函式](./functions-twitter-email.md)
