---
title: 優化 Azure Lsv2 系列虛擬機器的性能 - 存儲
description: 瞭解如何在 Lsv2 系列虛擬機器上優化解決方案的性能。
services: virtual-machines-windows
author: sasha-melamed
manager: gwallace
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 04/17/2019
ms.author: joelpell
ms.openlocfilehash: 57b248908a02327f2521be05920259681a26817a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "77920224"
---
# <a name="optimize-performance-on-the-lsv2-series-virtual-machines"></a>優化 Lsv2 系列虛擬機器的性能

Lsv2 系列虛擬機器支援各種工作負載，這些工作負載需要跨各種應用程式和行業的本機存放區實現高 I/O 和輸送量。  Lsv2 系列是大資料、SQL、NoSQL 資料庫、資料倉儲和大型事務資料庫（包括卡珊多拉、蒙戈DB、Cloudera 和 Redis）的理想選擇。

Lsv2 系列虛擬機器 （VM） 的設計使 AMD EPYC™ 7551 處理器最大化，從而在處理器、記憶體、NVMe 設備和 VM 之間提供最佳性能。 除了最大化硬體性能外，Lsv2 系列 VM 還旨在滿足 Windows 和 Linux 作業系統的需求，從而在硬體和軟體方面實現更好的性能。

調整軟體和硬體後[，Windows Server 2019 資料中心](https://azuremarketplace.microsoft.com/marketplace/apps/microsoftwindowsserver.windowsserver?tab=Overview)的優化版本于 2018 年 12 月初發佈給 Azure 應用商店，支援 Lsv2 系列 VM 中的 NVMe 設備的最大性能。

本文提供提示和建議，以確保工作負載和應用程式實現在 VM 中設計的最大性能。 隨著更多 Lsv2 優化映射添加到 Azure 應用商店，此頁面上的資訊將不斷更新。

## <a name="amd-eypc-chipset-architecture"></a>AMD EYPC™晶片組架構

Lsv2 系列 VM 使用基於 Zen 微架構的 AMD EYPC ™伺服器處理器。 AMD 為 EYPC 開發了無限交換矩陣 （IF™ 作為其 NUMA 型號的可擴展互連，可用於模上、封裝和多包通信。 與英特爾現代單片式處理器中使用的 QPI（快速路徑互連）和 UPI（超路徑互連）相比，AMD 的多 NUMA 小片式架構既可能帶來性能優勢，也可能帶來挑戰。 記憶體頻寬和延遲限制的實際影響可能因運行的工作負載類型而異。

## <a name="tips-for-maximizing-performance"></a>最大化性能的提示

* 為 Lsv2 系列 VM 供電的硬體使用具有八個 I/O 佇列對 （QP） 的 NVMe 設備。 每個 NVMe 設備 I/O 佇列實際上是一對：提交佇列和完成佇列。 NVMe 驅動程式通過迴圈計畫中分發 I/O 來優化這八個 I/O 季度的利用率。 為了獲得最大性能，每個設備運行八個作業以匹配。

* 避免在活動工作負載期間將 NVMe 管理命令（例如，NVMe SMART 資訊查詢等）與 NVMe I/O 命令混合。 Lsv2 NVMe 設備由 Hyper-V NVMe 直接技術支援，該技術在任何 NVMe 管理命令掛起時都會切換到"慢速模式"。 如果發生這種情況，Lsv2 使用者可能會看到 NVMe I/O 性能的顯著性能下降。

* Lsv2 使用者不應依賴從 VM 中報告的設備 NUMA 資訊（所有 0）來處理資料磁碟機，以決定其應用的 NUMA 相關性。 如果可能，建議用於提高性能的方法是跨 CPU 擴展工作負載。 

* Lsv2 VM NVMe 設備每個 I/O 佇列對受支援的最大佇列深度為 1024（與 Amazon i3 QD 32 限制）。 Lsv2 使用者應將其（綜合）基準工作負荷限制為佇列深度 1024 或更低，以避免觸發佇列完全條件，這可能會降低性能。

## <a name="utilizing-local-nvme-storage"></a>利用本地 NVMe 存儲

所有 Lsv2 VM 上的 1.92 TB NVMe 磁片上的本機存放區是短暫的。 在 VM 成功標準重新開機期間，本地 NVMe 磁片上的資料將保持不變。 如果重新部署、取消分配或刪除 VM，則該資料將不會保留在 NVMe 上。 如果另一個問題導致 VM 或正在運行的硬體不正常，則資料不會持久化。 發生這種情況時，舊主機上的所有資料將安全地擦除。

有時，VM 需要移動到其他主機，例如，在計畫維護操作期間。 計畫中的維護操作和一些硬體故障可以預期與[計畫事件](scheduled-events.md)。 應使用計畫事件來更新任何預測的維護和恢復操作。

如果計畫中的維護事件要求在具有空本地磁片的新主機上重新創建 VM，則需要重新同步資料（同樣，舊主機上的任何資料都將安全擦除）。 這是因為 Lsv2 系列 VM 當前不支援本地 NVMe 磁片上的即時移轉。

有兩種模式用於計畫維護。

### <a name="standard-vm-customer-controlled-maintenance"></a>標準 VM 客戶控制維護

- 在 30 天的時段內，VM 將移動到更新的主機。
- Lsv2 本機存放區資料可能會丟失，因此建議在事件發生之前備份資料。

### <a name="automatic-maintenance"></a>自動維護

- 如果客戶不執行客戶控制的維護，或者在發生緊急程式（如安全零日事件）時發生。
- 旨在保留客戶資料，但存在 VM 凍結或重新開機的小風險。
- Lsv2 本機存放區資料可能會丟失，因此建議在事件發生之前備份資料。

對於任何即將發生的服務事件，請使用受控維護過程選擇最方便的時間進行更新。 在活動之前，您可以將資料備份到高級存儲中。 維護事件完成後，您可以將資料返回到刷新的 Lsv2 VR 本地 NVMe 存儲。

維護本地 NVMe 磁片上資料的方案包括：

- VM 正在運行且正常。
- VM 已就地重新開機（由您或 Azure 重新開機）。
- VM 已暫停（未取消分配而停止）。
- 大多數計畫的維護維修操作。

安全擦除資料以保護客戶的方案包括：

- VM 被重新部署、停止（取消分配）或刪除（由您刪除）。
- VM 變得不正常，由於硬體問題，必須將服務修復到另一個節點。
- 少數計畫維護服務操作，這些操作要求將 VM 重新分配到另一個主機進行維修。

要瞭解有關在本機存放區中備份資料的選項的詳細資訊，請參閱[Azure IaaS 磁片的備份和災害復原](backup-and-disaster-recovery-for-azure-iaas-disks.md)。

## <a name="frequently-asked-questions"></a>常見問題集

* **如何開始部署 Lsv2 系列 VM？**  
   與任何其他 VM 一樣，請使用[門戶](quick-create-portal.md) [、Azure CLI](quick-create-cli.md)或[PowerShell](quick-create-powershell.md)創建 VM。

* **單個 NVMe 磁片故障是否會導致主機上的所有 VM 失敗？**  
   如果在硬體節點上檢測到磁片故障，則硬體處於故障狀態。 發生這種情況時，節點上的所有 VM 將自動取消分配並移動到正常節點。 對於 Lsv2 系列 VM，這意味著客戶在故障節點上的資料也會被安全地擦除，並且需要客戶在新節點上重新創建。 如前所述，在 Lsv2 上提供即時移轉之前，故障節點上的資料將在 VM 傳輸到另一個節點時主動移動。

* **我是否需要在 Windows 伺服器 2012 或 Windows 伺服器 2016 中的 Windows 中進行輪詢調整？**  
   NVMe 輪詢僅在 Azure 上的 Windows Server 2019 上可用。  

* **我可以切換回傳統的插斷服務常式 （ISR） 模型嗎？**  
   Lsv2 系列 VM 針對 NVMe 輪詢進行了優化。 不斷提供更新以提高輪詢性能。

* **我可以調整 Windows Server 2019 中的輪詢設置嗎？**  
   輪詢設置不可使用者調整。
   
## <a name="next-steps"></a>後續步驟

* 查看針對 Azure 上的[存儲性能優化](sizes-storage.md)的所有 VM 的規範
