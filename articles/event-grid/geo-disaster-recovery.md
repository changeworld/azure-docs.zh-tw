---
title: Azure 事件方格中的地理災難復原 |Microsoft Docs
description: 說明 Azure Event Grid 如何自動支援異地嚴重損壞修復 (GeoDR) 。
ms.topic: conceptual
ms.date: 07/07/2020
ms.openlocfilehash: ccb16971020a65932daa8f9adf4b7cd9008a9253
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "86105841"
---
# <a name="server-side-geo-disaster-recovery-in-azure-event-grid"></a>Azure 事件方格中的伺服器端地理損毀修復
事件方格現在具有自動異地嚴重損壞修復 (GeoDR) 的中繼資料，不僅適用于新的，還包括所有現有的網域、主題和事件訂閱。 如果整個 Azure 區域停止運作，事件方格就會將所有事件相關的基礎結構元資料同步處理到配對的區域。 您的新活動將會再次開始流動，而不會讓您介入。 

嚴重損壞修復是以兩個計量來測量：

- [復原點目標 (RPO) ](https://en.wikipedia.org/wiki/Disaster_recovery#Recovery_Point_Objective)：可能會遺失的資料分鐘數或時數。
- 復原[時間目標 (RTO) ](https://en.wikipedia.org/wiki/Disaster_recovery#Recovery_time_objective)：服務可能關閉的分鐘數。

事件方格的自動容錯移轉具有不同的 Rpo 和 Rto，適用于您的中繼資料 (事件訂閱等 ) 和資料 (事件) 。 如果您需要來自下列各項的不同規格，您仍然可以使用「健康情況 api」主題來執行您自己 [的用戶端容錯移轉](custom-disaster-recovery.md)。

## <a name="recovery-point-objective-rpo"></a>復原點目標 (RPO)
- **中繼資料 RPO**：零分鐘。 每當在事件方格中建立資源時，它會立即跨區域複寫。 發生容錯移轉時，不會遺失中繼資料。
- **資料 RPO**：如果您的系統狀況良好，且在區域容錯移轉時攔截到現有的流量，則事件的 RPO 大約是5分鐘。

## <a name="recovery-time-objective-rto"></a>復原時間目標 (RTO)
- **中繼資料 RTO**：雖然通常會更快發生，但在60分鐘內，事件方格將會開始接受主題和訂用帳戶的建立/更新/刪除呼叫。
- **資料 RTO**：像是中繼資料，通常會更快發生，但在60分鐘內，事件方格會在區域容錯移轉之後開始接受新的流量。

> [!NOTE]
> 事件方格的中繼資料 GeoDR 成本是： $0。


## <a name="next-steps"></a>接下來的步驟
如果您想要執行自己的用戶端容錯移轉邏輯，請參閱[# 為事件方格中的自訂主題建立您自己的](custom-disaster-recovery.md)嚴重損壞修復
