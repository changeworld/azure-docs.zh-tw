---
title: 有關 Azure 網站恢復監視的常見問題
description: 使用內置監視和 Azure 監視器（日誌分析）獲取有關 Azure 網站恢復監視的常見問題的解答
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
services: site-recovery
ms.date: 07/31/2019
ms.topic: conceptual
ms.author: raynew
ms.openlocfilehash: c1d30a9cdd2cd6ca288edd609a2e2e7bee9174d7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "68718258"
---
# <a name="common-questions-about-site-recovery-monitoring"></a>有關網站恢復監視的常見問題

本文回答了有關監視 Azure[網站恢復](site-recovery-overview.md)、使用內置網站恢復監視和 Azure 監視器（日誌分析）的常見問題。

## <a name="general"></a>一般

### <a name="how-is-the-rpo-value-logged-different-from-the-latest-available-recovery-point"></a>RPO 值的記錄與最新的可用復原點有什麼不同？

網站恢復使用多步非同步過程將電腦複製到 Azure。

- 在倒數第二個複寫步驟中，會將機器上最近的變更與中繼資料一起複製到記錄/快取儲存體帳戶。
- 這些更改以及標識可復原點的標記將寫入目的地區域中的存儲帳戶/託管磁片。
- 網站恢復現在可以為電腦生成可復原點。
- 此時，RPO 已滿足到目前為止上載到存儲帳戶的更改。 換句話說，此時的電腦 RPO 等於從與可復原點對應的時間戳記經過的時間戳記所經過的時間量。
- 現在，Site Recovery 會從儲存體帳戶選取已上傳的資料，並將其套用至為機器建立的複本磁碟。
- 然後，Site Recovery 會產生復原點，並使該復原點在容錯移轉時可供復原。
- 因此，最新的可用復原點指示與已處理並應用於副本磁片的最新復原點對應的時間戳記。


複製源電腦或本地基礎結構伺服器上的系統時間不正確會扭曲計算的 RPO 值。 若要準確報告 RPO，請確定所有伺服器和機器上的系統時鐘正確無誤。



## <a name="inbuilt-site-recovery-logging"></a>內置網站修復記錄記錄


### <a name="why-is-the-vm-count-in-the-vault-infrastructure-view-different-from-the-total-count-shown-in-replicated-items"></a>為什麼保存庫基礎結構視圖中的 VM 計數與複製專案中顯示的總計數不同？

保存庫基礎結構檢視範圍是根據複寫情況決定。 當前選擇的複製方案中只有電腦包含在視圖的計數中。 此外，我們只會計算依設定要複寫至 Azure 的 VM。 在視圖中不計算在電腦上故障或複製回本地網站的電腦。

### <a name="why-is-the-count-of-replicated-items-in-essentials-different-from-the-total-count-of-replicated-items-on-the-dashboard"></a>為什麼 Essentials 中的複製項計數與儀表板上複製項的總數不同？

只有已完成初始複製的電腦才包含在"基本"中顯示的計數中。 複製的專案總數包括保存庫中的所有電腦，包括當前正在進行初始複製的電腦。

## <a name="azure-monitor-logging"></a>Azure 監視器記錄


### <a name="how-often-does-site-recovery-send-diagnostic-logs-to-azure-monitor-log"></a>網站恢復向 Azure 監視器日誌發送診斷日誌的頻率如何？ 

- AzureSite 恢復複製統計資訊和 Azure 網站恢復復原點每 15 分鐘發送一次。  
- AzureSite 恢復複製資料上傳速率和 AzureSite 恢復保護磁片資料資料卷髮送每五分鐘發送一次。 
- AzureSite 恢復作業在作業的觸發器和完成時發送。
- 每當建置事件時都會發送 AzureSite 恢復事件。 
- 每當有任何環境更改時，都會發送 AzureSite 恢復複製專案。 通常，資料刷新時間是更改後的 15 分鐘。 

### <a name="how-long-is-data-kept-in-azure-monitor-logs"></a>資料在 Azure 監視器日誌中保留多長時間？ 

預設情況下，保留期為 31 天。 您可以在日誌分析工作區中的 **"使用方式和估計成本**"部分中增加期間。 按一下 **"資料保留**"並選擇範圍。

### <a name="whats-the-size-of-the-diagnostic-logs"></a>診斷日誌的大小是多少？ 

通常日誌的大小為 15-20 KB。 


## <a name="next-steps"></a>後續步驟

瞭解如何使用[網站恢復內置監視](site-recovery-monitor-and-troubleshoot.md)或 Azure 監視器進行[監視](monitor-log-analytics.md)。


