---
title: Azure Site Recovery 監視的常見問題
description: '使用內建監視和 Azure 監視器 (Log Analytics，來取得 Azure Site Recovery 監視的常見問題解答。) '
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
services: site-recovery
ms.date: 07/31/2019
ms.topic: conceptual
ms.author: raynew
ms.openlocfilehash: 66ce267581d4748ea51a3dcbd7caa61907115cc1
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "82131152"
---
# <a name="common-questions-about-site-recovery-monitoring"></a>Site Recovery 監視的常見問題

本文將回答有關使用內建 Site Recovery 監視來監視 Azure [Site Recovery](site-recovery-overview.md)，以及 Azure 監視器 (Log Analytics) 的常見問題。

## <a name="general"></a>一般

### <a name="how-is-the-rpo-value-logged-different-from-the-latest-available-recovery-point"></a>從最新的可用復原點記錄的 RPO 值有何不同？

Site Recovery 會使用多步驟非同步處理常式，將機器複寫至 Azure。

- 在倒數第二個複寫步驟中，會將機器上最近的變更與中繼資料一起複製到記錄/快取儲存體帳戶。
- 這些變更以及識別可復原點的標籤，會寫入目的地區域中的儲存體帳戶/受控磁片。
- Site Recovery 現在可以為機器產生復原點。
- 至此，目前為止已上傳至儲存體帳戶的變更已符合 RPO。 換句話說，機器 RPO 的位置會等於從對應至可復原點的時間戳記所經過的時間量。
- 現在，Site Recovery 會從儲存體帳戶選取已上傳的資料，並將其套用至為機器建立的複本磁碟。
- 然後，Site Recovery 會產生復原點，並使該復原點在容錯移轉時可供復原。
- 因此，最新的可用復原點會指出對應到已處理的最新復原點，並套用至複本磁片的時間戳記。


複寫來源機器或內部部署基礎結構伺服器上的系統時間不正確，將會扭曲計算的 RPO 值。 若要準確報告 RPO，請確定所有伺服器和機器上的系統時鐘正確無誤。



## <a name="inbuilt-site-recovery-logging"></a>內建 Site Recovery 記錄


### <a name="why-is-the-vm-count-in-the-vault-infrastructure-view-different-from-the-total-count-shown-in-replicated-items"></a>為什麼保存庫基礎結構視圖中的 VM 計數與複寫專案中顯示的總數不同？

保存庫基礎結構檢視範圍是根據複寫情況決定。 只有目前所選複寫案例中的機器才會包含在此視圖的計數中。 此外，我們只會計算依設定要複寫至 Azure 的 VM。 已容錯移轉的機器或複寫回內部部署網站的機器不會在此視圖中計算。

### <a name="why-is-the-count-of-replicated-items-in-essentials-different-from-the-total-count-of-replicated-items-on-the-dashboard"></a>為什麼 Essentials 中的複寫專案計數與儀表板上複寫的專案總數不同？

只有已完成初始複寫的機器才會包含在 Essentials 所顯示的計數中。 複寫的專案總數包含保存庫中的所有電腦，包括目前正在進行初始複寫的機器。

## <a name="azure-monitor-logging"></a>Azure 監視器記錄


### <a name="how-often-does-site-recovery-send-resource-logs-to-azure-monitor-log"></a>Site Recovery 將資源記錄檔傳送到 Azure 監視器記錄檔的頻率為何？ 

- AzureSiteRecoveryReplicationStats 和 AzureSiteRecoveryRecoveryPoints 會每隔15分鐘傳送一次。  
- AzureSiteRecoveryReplicationDataUploadRate 和 AzureSiteRecoveryProtectedDiskDataChurn 會每隔五分鐘傳送一次。 
- AzureSiteRecoveryJobs 會在觸發程式和完成作業時傳送。
- 每當產生事件時，就會傳送 AzureSiteRecoveryEvents。 
- 每當有任何環境變更時，就會傳送 AzureSiteRecoveryReplicatedItems。 一般來說，資料重新整理時間是在變更之後的15分鐘。 

### <a name="how-long-is-data-kept-in-azure-monitor-logs"></a>資料保留在 Azure 監視器記錄中多久？ 

根據預設，保留期為31天。 您可以在 Log Analytics 工作區的 **使用量和估計成本** 區段中增加期間。 按一下 [ **資料保留**]，然後選擇範圍。

### <a name="whats-the-size-of-the-resource-logs"></a>資源記錄檔的大小為何？ 

記錄檔的大小通常是 15-20 KB。 


## <a name="next-steps"></a>接下來的步驟

瞭解如何使用 Site Recovery 內建 [監視](site-recovery-monitor-and-troubleshoot.md)或 [Azure 監視器](monitor-log-analytics.md)來進行監視。


