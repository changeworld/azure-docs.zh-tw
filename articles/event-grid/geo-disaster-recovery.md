---
title: Azure 事件網格中的地理災害復原 |微軟文檔
description: 描述 Azure 事件網格如何自動支援地理災害復原 （GeoDR）。
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: conceptual
ms.date: 05/24/2019
ms.author: spelluru
ms.openlocfilehash: 5b5c973a8daa8776efb0909092c569ea46902265
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "66307313"
---
# <a name="server-side-geo-disaster-recovery-in-azure-event-grid"></a>Azure 事件網格中的伺服器端地理災害復原
事件網格現在不僅針對新域、所有現有域、主題和事件訂閱，還自動復原中繼資料（GeoDR）。 如果整個 Azure 區域出現故障，事件網格將已有所有與事件相關的基礎結構元資料同步到配對區域。 您的新事件將再次開始流動，而您無需干預。 

通過兩個指標衡量災害復原：

- [復原點目標 （RPO）](https://en.wikipedia.org/wiki/Disaster_recovery#Recovery_Point_Objective)：可能丟失的資料的分鐘數或小時數。
- [恢復時間目標 （RTO）：](https://en.wikipedia.org/wiki/Disaster_recovery#Recovery_time_objective)服務可能關閉的小時數。

事件網格的自動容錯移轉具有不同的 RPO 和 RPO，用於中繼資料（事件訂閱等）和資料（事件）。 如果需要與以下規範不同的規範，您仍可以使用[主題運行狀況 apis 實現自己的用戶端容錯移轉](custom-disaster-recovery.md)。

## <a name="recovery-point-objective-rpo"></a>復原點目標 (RPO)
- **中繼資料 RPO**：零分鐘。 每當在事件網格中創建資源時，它都會在區域之間立即複製。 發生容錯移轉時，不會丟失任何中繼資料。
- **資料 RPO：** 如果您的系統狀況良好，並在區域容錯移轉時趕上現有流量，則事件的 RPO 約為 5 分鐘。

## <a name="recovery-time-objective-rto"></a>復原時間目標 (RTO)
- **中繼資料 RTO**：雖然通常發生得更快，但在 60 分鐘內，事件網格將開始接受主題和訂閱的創建/更新/刪除調用。
- **資料 RTO**： 與中繼資料一樣，它通常發生得更快，但在 60 分鐘內，事件網格將在區域容錯移轉後開始接受新的流量。

> [!NOTE]
> 事件網格上的中繼資料 GeoDR 的成本為：$0。


## <a name="next-steps"></a>後續步驟
如果要實現自己的用戶端容錯移轉邏輯，請參閱[# 為事件網格中的自訂主題構建您自己的災害復原](custom-disaster-recovery.md)
