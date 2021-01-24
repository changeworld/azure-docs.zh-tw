---
title: Azure 事件中樞-.NET 例外狀況
description: 本文提供 Azure 事件中樞的 .NET 訊息例外狀況和建議的動作清單。
services: event-hubs
documentationcenter: na
author: spelluru
ms.service: event-hubs
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.custom: seodec18
ms.date: 09/23/2020
ms.author: spelluru
ms.openlocfilehash: af2d820fd255d041ade21a00d7d7a9ac6b12fcba
ms.sourcegitcommit: 4d48a54d0a3f772c01171719a9b80ee9c41c0c5d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/24/2021
ms.locfileid: "98745897"
---
# <a name="eventhubsexception---net"></a>EventHubsException-.NET
當事件中樞特定的作業造成問題時，會觸發 **EventHubsException** ，包括服務內的錯誤，以及用戶端特定的錯誤。 

## <a name="exception-information"></a>例外狀況資訊
例外狀況包括下列內容相關資訊，可協助您瞭解錯誤的內容和其相對的嚴重性。 

- **IsTransient**：識別是否將例外狀況視為可復原。 在被視為暫時性的情況下，已套用適當的重試原則，而重試失敗。
- **原因**：提供一組已知的失敗原因，有助於分類和闡明根本原因。 這些原因的目的是要在檢查例外狀況訊息的文字不理想時，允許套用例外狀況篩選和其他邏輯。 以下是一些重要的失敗原因：
    - **用戶端已關閉**：已關閉或已處置的事件中樞用戶端時發生。 建議您檢查應用程式的程式碼，以確保事件中樞用戶端程式庫中的物件會在預期的範圍內建立和關閉。
    - **服務超時**：表示事件中樞服務未在預期的時間內回應作業。 發生此問題的原因可能是暫時性網路問題或服務問題。 事件中樞服務不一定會成功完成要求;狀態未知。 建議您嘗試驗證目前的狀態，並視需要重試。
    - **超過配額**：表示單一取用者群組有太多作用中的讀取作業。 此限制取決於事件中樞命名空間的階層，而且可能需要移至較高的層級。 替代方式是建立額外的取用者群組，並確定任何群組的取用者用戶端讀取數目都在限制範圍內。 如需詳細資訊，請參閱 [Azure 事件中樞配額和限制](event-hubs-quotas.md)。
    - **超過訊息大小**：事件資料是針對個別事件和批次事件所允許的最大大小。 它包含事件的資料，以及任何相關聯的中繼資料和系統額外負荷。 若要解決此錯誤，請減少批次中傳送的事件數目，或減少訊息中包含的資料大小。 因為大小限制可能會變更，請參閱，以 Azure 事件中樞詳細資訊的 [配額和限制](event-hubs-quotas.md) 。
    - **消費者已中斷** 連線：用戶端用戶端已由事件中樞實例的事件中樞服務中斷連線。 通常會在具有較高擁有者層級的取用者在分割區和取用者群組配對上判斷提示擁有權時發生。
    - **找不到資源**：事件中樞服務找不到資源，例如事件中樞、取用者群組或資料分割。 它可能已被刪除，或事件中樞服務本身發生問題。

## <a name="handling-exceptions"></a>處理例外狀況
您可以透過數種方式來回應 **EventHubException**  的特定失敗原因。 其中一種方式是將例外狀況篩選子句套用為 catch 區塊的一部分。

```csharp
try
{
    // Read events using the consumer client
}
catch (EventHubsException ex) where 
    (ex.Reason == EventHubsException.FailureReason.ConsumerDisconnected)
{
    // Take action based on a consumer being disconnected
}
```

## <a name="next-steps"></a>後續步驟
[舊版文章](event-hubs-messaging-exceptions.md)中記載了其他的例外狀況。 其中有些只適用于舊版事件中樞 .NET 用戶端程式庫。