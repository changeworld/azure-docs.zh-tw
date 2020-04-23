---
title: 將 Azure Lsv2 系列虛擬機器上的效能優化-儲存體
description: 瞭解如何在 Lsv2 系列的虛擬機器上將解決方案的效能優化。
author: sasha-melamed
ms.service: virtual-machines-windows
ms.subservice: sizes
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 04/17/2019
ms.author: joelpell
ms.openlocfilehash: cd15df2a7074463789bcf4a2d4de3c41bd012bbb
ms.sourcegitcommit: 086d7c0cf812de709f6848a645edaf97a7324360
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2020
ms.locfileid: "82100544"
---
# <a name="optimize-performance-on-the-lsv2-series-virtual-machines"></a>將 Lsv2 系列虛擬機器上的效能優化

Lsv2 系列虛擬機器支援各種不同的工作負載，其在各種應用程式和產業的本機儲存體上需要高 i/o 和輸送量。  Lsv2 系列適用于大型資料、SQL、NoSQL 資料庫、資料倉儲和大型交易式資料庫，包括 Cassandra、MongoDB、Cloudera 和 Redis。

Lsv2 系列虛擬機器（Vm）的設計可將 AMD EPYC™7551處理器最大化，以提供處理器、記憶體、NVMe 裝置和 Vm 之間的最佳效能。 除了最大化硬體效能，Lsv2 系列 Vm 的設計可與 Windows 和 Linux 作業系統的需求搭配使用，以獲得更佳的硬體和軟體效能。

調整軟體和硬體會導致[Windows Server 2019 Datacenter](https://azuremarketplace.microsoft.com/marketplace/apps/microsoftwindowsserver.windowsserver?tab=Overview)的優化版本，在12月2018日之前發行至 Azure Marketplace，這可支援 Lsv2 系列 Vm 中 NVMe 裝置上的最大效能。

本文提供秘訣和建議，以確保您的工作負載和應用程式能夠達到最高的效能，並將其設計到 Vm 中。 此頁面上的資訊將會持續更新，因為已將更 Lsv2 的優化影像新增至 Azure Marketplace。

## <a name="amd-eypc-chipset-architecture"></a>AMD EYPC™晶片架構

Lsv2 系列 Vm 會使用以 Zen 微架構為基礎的 AMD EYPC™伺服器處理器。 AMD 針對 EYPC™開發了無限大網狀架構（如果）做為其 NUMA 模型的可擴充互連，可用於內部、套件內部和多套件通訊。 相較于 Intel 現代化整合型骰子處理器所使用的 QPI （快速路徑互連）和 UPI （Ultra 路徑互連），AMD 的多 NUMA 小型片架構可能會帶來效能優勢和挑戰。 記憶體頻寬和延遲條件約束的實際影響可能會根據執行的工作負載類型而有所不同。

## <a name="tips-for-maximizing-performance"></a>最大化效能的秘訣

* 為 Lsv2 系列 Vm 提供動力的硬體，會利用具有8個 i/o 佇列配對（QP）的 NVMe 裝置。 每個 NVMe 裝置 i/o 佇列實際上都是成對的：提交佇列和完成佇列。 NVMe 驅動程式已設定為藉由將 i/o 分散到迴圈配置資源排程，來優化這八個 i/o QPs 的使用率。 若要取得最大效能，請在每部裝置上執行八個工作以進行比對。

* 避免在使用中的工作負載期間，混用 nvme 管理命令（例如 NVMe 智慧型資訊查詢等）與 NVMe 的 i/o 命令。 Lsv2 NVMe 裝置是由 Hyper-v NVMe Direct 技術支援，每當有任何 NVMe 的管理命令暫止時，就會切換為「慢速模式」。 如果發生這種情況，Lsv2 使用者可能會在 NVMe i/o 效能中看到顯著的效能下降。

* Lsv2 使用者不應依賴從 VM 內部回報的裝置 NUMA 資訊（全部0）來決定其應用程式的 NUMA 親和性。 建議的最佳效能方式是盡可能將工作負載分散到多個 Cpu。 

* Lsv2 VM NVMe 裝置的每個 i/o 佇列對支援的佇列深度上限為1024（相對於 Amazon i3 QD 32 限制）。 Lsv2 使用者應該將其（綜合）基準工作負載限制為佇列深度1024或更低，以避免觸發佇列的完整狀況，這可能會降低效能。

## <a name="utilizing-local-nvme-storage"></a>利用本機 NVMe 儲存體

所有 Lsv2 Vm 上 1.92 TB NVMe 磁片上的本機存放裝置是暫時的。 在 VM 的成功標準重新開機期間，本機 NVMe 磁片上的資料將會保存。 如果重新部署、解除配置或刪除 VM，資料不會保存在 NVMe 上。 如果另一個問題造成 VM 或其執行的硬體變成狀況不良，則資料不會保存。 發生這種情況時，會安全地清除舊主機上的所有資料。

在某些情況下，VM 必須移至不同的主機電腦，例如在規劃的維護作業期間。 已規劃的維護作業和一些硬體故障可能會在[Scheduled Events](scheduled-events.md)中預期。 Scheduled Events 應該用來隨時更新任何預測的維護和復原作業。

如果預定的維護事件需要在具有空白本機磁片的新主機上重新建立 VM，則必須重新同步處理資料（同樣地，舊主機上的所有資料都要安全地清除）。 這是因為 Lsv2 系列 Vm 目前不支援在本機 NVMe 磁片上進行即時移轉。

有兩種模式可進行預定的維護。

### <a name="standard-vm-customer-controlled-maintenance"></a>標準 VM 客戶控制的維護

- 在30天的時間範圍內，VM 會移至更新的主機。
- Lsv2 本機儲存體資料可能會遺失，因此建議您在進行事件之前的備份資料。

### <a name="automatic-maintenance"></a>自動維護

- 當客戶未執行客戶控制的維護，或發生緊急程式（例如「安全性零日」事件）時發生。
- 其目的是要保留客戶資料，但 VM 凍結或重新開機的風險很小。
- Lsv2 本機儲存體資料可能會遺失，因此建議您在進行事件之前的備份資料。

針對任何即將推出的服務事件，請使用受控制的維護程式來選取最方便您進行更新的時間。 在事件之前，您可以備份 premium 儲存體中的資料。 維護事件完成之後，您可以將資料傳回至重新整理的 Lsv2 Vm 本機 NVMe 儲存體。

在本機 NVMe 磁片上維護資料的案例包括：

- VM 正在執行且狀況良好。
- VM 會就地重新開機（由您或 Azure）。
- VM 已暫停（未解除配置即停止）。
- 大部分預定的維護服務作業。

安全地清除資料以保護客戶的案例包括：

- VM 會重新部署、停止（解除配置）或刪除（由您執行）。
- VM 會變得狀況不良，而且由於硬體問題而必須對另一個節點進行服務修復。
- 需要將 VM 重新配置給另一部主機進行服務的少數預定維護服務作業。

若要深入瞭解在本機儲存體中備份資料的選項，請參閱[Azure IaaS 磁片的備份和](backup-and-disaster-recovery-for-azure-iaas-disks.md)嚴重損壞修復。

## <a name="frequently-asked-questions"></a>常見問題集

* **如何? 開始部署 Lsv2 系列 Vm 嗎？**  
   與任何其他 VM 很類似，請使用[入口網站](quick-create-portal.md)、 [Azure CLI](quick-create-cli.md)或[PowerShell](quick-create-powershell.md)來建立 VM。

* **單一 NVMe 磁片失敗會導致主機上的所有 Vm 失敗嗎？**  
   如果在硬體節點上偵測到磁片失敗，則硬體處於 [失敗] 狀態。 發生這種情況時，節點上的所有 Vm 都會自動解除配置，並移至狀況良好的節點。 對於 Lsv2 系列 Vm，這表示客戶在失敗節點上的資料也會安全地清除，而且必須由客戶在新節點上重新建立。 如先前所述，在 Lsv2 上推出「即時移轉」之前，失敗節點上的資料會在 Vm 傳輸至另一個節點時，主動隨之移動。

* **我是否需要在 windows Server 2012 或 Windows Server 2016 的 Windows 中進行輪詢調整？**  
   NVMe 輪詢僅適用于 Azure 上的 Windows Server 2019。  

* **我可以切換回傳統的插斷服務常式（ISR）模型嗎？**  
   Lsv2 系列 Vm 已針對 NVMe 輪詢進行優化。 持續提供更新以改善輪詢效能。

* **我可以調整 Windows Server 2019 中的輪詢設定嗎？**  
   輪詢設定不是使用者可調整的。
   
## <a name="next-steps"></a>後續步驟

* 請參閱針對 Azure 上[的儲存體效能優化的所有 vm](sizes-storage.md)規格
