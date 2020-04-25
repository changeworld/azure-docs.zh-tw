---
title: Azure Site Recovery 監視的相關常見問題
description: 使用內建監視和 Azure 監視器（Log Analytics）來取得有關 Azure Site Recovery 監視的常見問題解答
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
services: site-recovery
ms.date: 07/31/2019
ms.topic: conceptual
ms.author: raynew
ms.openlocfilehash: 66ce267581d4748ea51a3dcbd7caa61907115cc1
ms.sourcegitcommit: edccc241bc40b8b08f009baf29a5580bf53e220c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/24/2020
ms.locfileid: "82131152"
---
# <a name="common-questions-about-site-recovery-monitoring"></a>Site Recovery 監視的相關常見問題

本文會回答有關使用內建 Site Recovery 監視和 Azure 監視器（Log Analytics）來監視 Azure [Site Recovery](site-recovery-overview.md)的常見問題。

## <a name="general"></a>一般

### <a name="how-is-the-rpo-value-logged-different-from-the-latest-available-recovery-point"></a>RPO 值的記錄與最新的可用復原點有何不同？

Site Recovery 使用多步驟非同步程式將機器複寫至 Azure。

- 在倒數第二個複寫步驟中，會將機器上最近的變更與中繼資料一起複製到記錄/快取儲存體帳戶。
- 這些變更以及識別可復原點的標記，都會寫入目的地區域中的儲存體帳戶/受控磁片。
- Site Recovery 現在可以產生機器的可復原點。
- 到目前為止，已上傳至儲存體帳戶的變更已符合 RPO。 換句話說，此時的機器 RPO 等同于從對應至可復原點的時間戳記經過的時間量。
- 現在，Site Recovery 會從儲存體帳戶選取已上傳的資料，並將其套用至為機器建立的複本磁碟。
- 然後，Site Recovery 會產生復原點，並使該復原點在容錯移轉時可供復原。
- 因此，最新的可用復原點會指出對應到最近已處理之復原點的時間戳記，並套用到複本磁片。


複寫來源機器或內部部署基礎結構伺服器上的系統時間不正確，將會扭曲計算的 RPO 值。 若要準確報告 RPO，請確定所有伺服器和機器上的系統時鐘正確無誤。



## <a name="inbuilt-site-recovery-logging"></a>內建 Site Recovery 記錄


### <a name="why-is-the-vm-count-in-the-vault-infrastructure-view-different-from-the-total-count-shown-in-replicated-items"></a>為什麼保存庫基礎結構中的 VM 計數與 [複寫的專案] 中所顯示的總計數不同？

保存庫基礎結構檢視範圍是根據複寫情況決定。 此視圖的計數中只會包含目前所選複寫案例中的機器。 此外，我們只會計算依設定要複寫至 Azure 的 VM。 已容錯回復的機器或複寫回內部部署網站的機器不會在此視圖中計算。

### <a name="why-is-the-count-of-replicated-items-in-essentials-different-from-the-total-count-of-replicated-items-on-the-dashboard"></a>為什麼 Essentials 中的複寫專案計數與儀表板上的複寫專案總計數不同？

只有已完成初始複寫的電腦才會包含在 [基本功能] 顯示的計數中。 複寫的專案總計包含保存庫中的所有機器，包括目前正在進行初始複寫的電腦。

## <a name="azure-monitor-logging"></a>Azure 監視器記錄


### <a name="how-often-does-site-recovery-send-resource-logs-to-azure-monitor-log"></a>Site Recovery 將資源記錄檔傳送至 Azure 監視器記錄檔的頻率為何？ 

- AzureSiteRecoveryReplicationStats 和 AzureSiteRecoveryRecoveryPoints 會每隔15分鐘傳送一次。  
- AzureSiteRecoveryReplicationDataUploadRate 和 AzureSiteRecoveryProtectedDiskDataChurn 會每五分鐘傳送一次。 
- AzureSiteRecoveryJobs 會在觸發程式中傳送，並完成工作。
- 每當產生事件時，就會傳送 AzureSiteRecoveryEvents。 
- 每當有任何環境變更時，就會傳送 AzureSiteRecoveryReplicatedItems。 一般來說，資料重新整理時間是變更後的15分鐘。 

### <a name="how-long-is-data-kept-in-azure-monitor-logs"></a>資料會保留在 Azure 監視器記錄中多久？ 

根據預設，保留期為31天。 您可以在 Log Analytics 工作區的 [**使用量和估計成本**] 區段中增加期間。 按一下 [**資料保留**]，然後選擇範圍。

### <a name="whats-the-size-of-the-resource-logs"></a>資源記錄檔的大小為何？ 

通常，記錄檔的大小為 15-20 KB。 


## <a name="next-steps"></a>後續步驟

瞭解如何使用 Site Recovery 內建[監視](site-recovery-monitor-and-troubleshoot.md)或[Azure 監視器](monitor-log-analytics.md)進行監視。


