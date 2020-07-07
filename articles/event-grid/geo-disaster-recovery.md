---
title: Azure 事件方格中的異地嚴重損壞修復 |Microsoft Docs
description: 說明 Azure 事件方格如何自動支援異地嚴重損壞修復（GeoDR）。
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: conceptual
ms.date: 05/24/2019
ms.author: spelluru
ms.openlocfilehash: 5b5c973a8daa8776efb0909092c569ea46902265
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "66307313"
---
# <a name="server-side-geo-disaster-recovery-in-azure-event-grid"></a>Azure 事件方格中的伺服器端地理故障復原
事件方格現在具有自動異地嚴重損壞修復（GeoDR）的中繼資料，不僅適用于新的，而是所有現有的網域、主題和事件訂閱。 如果整個 Azure 區域停止運作，事件方格就會將所有事件相關的基礎結構中繼資料，同步處理到配對的區域。 您的新事件將會開始再次流動，而您不需要介入。 

嚴重損壞修復是以兩個計量來測量：

- [復原點目標（RPO）](https://en.wikipedia.org/wiki/Disaster_recovery#Recovery_Point_Objective)：可能會遺失的資料分鐘數或小時數。
- 復原[時間目標（RTO）](https://en.wikipedia.org/wiki/Disaster_recovery#Recovery_time_objective)：服務可能關閉的分鐘數。

事件方格的自動容錯移轉針對您的中繼資料（事件訂閱等）和資料（事件）有不同的 Rpo 和 Rto。 如果您需要來自下列各項的不同規格，您仍然可以[使用健康情況 api 主題](custom-disaster-recovery.md)來執行您自己的用戶端故障切換。

## <a name="recovery-point-objective-rpo"></a>復原點目標 (RPO)
- **中繼資料 RPO**：零分鐘。 每當在事件方格中建立資源時，它會立即跨區域複寫。 發生容錯移轉時，不會遺失任何中繼資料。
- **資料 RPO**：如果您的系統狀況良好，且在區域容錯移轉時攔截到現有的流量，事件的 RPO 大約為5分鐘。

## <a name="recovery-time-objective-rto"></a>復原時間目標 (RTO)
- **中繼資料 RTO**：雖然通常會在60分鐘內更快速地發生，事件方格會開始接受主題和訂用帳戶的建立/更新/刪除呼叫。
- **資料 RTO**：類似中繼資料，通常會更快速地發生，但在60分鐘內，事件方格會在區域容錯移轉後開始接受新流量。

> [!NOTE]
> 在事件方格上 GeoDR 中繼資料的成本為： $0。


## <a name="next-steps"></a>後續步驟
如果您想要執行自己的用戶端容錯移轉邏輯，請參閱[# Build 您自己的嚴重損壞修復以取得事件方格中的自訂主題](custom-disaster-recovery.md)
