---
title: 將 Azure Lsv2 系列虛擬機器上的效能最佳化 - 儲存體
description: 瞭解如何使用 Linux 範例，在 Lsv2 系列虛擬機器上將解決方案的效能優化。
services: virtual-machines-linux
author: laurenhughes
ms.service: virtual-machines-linux
ms-subservice: sizes
ms.topic: conceptual
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 08/05/2019
ms.author: joelpell
ms.openlocfilehash: 650164556223a73a722bc91ecb31491ee98cb8a5
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "91307096"
---
# <a name="optimize-performance-on-the-lsv2-series-linux-virtual-machines"></a>優化 Lsv2 系列 Linux 虛擬機器的效能

Lsv2 系列虛擬機器支援各種不同的工作負載，其在各種應用程式和產業的本機儲存體上需要高 I/O 和輸送量。  Lsv2 系列適用於巨量資料、SQL、NoSQL 資料庫、資料倉儲和大型交易資料庫，包括 Cassandra、MongoDB、Cloudera 和 Redis。

Lsv2 系列虛擬機器 (VM) 的設計可將 AMD EPYC™7551 處理器最大化，以提供處理器、記憶體、NVMe 裝置和 VM 之間的最佳效能。 在 Linux 中使用合作夥伴，Azure Marketplace 提供數個組建，這些組建已針對 Lsv2 系列效能進行優化，且目前包含：

- Ubuntu 18.04
- Ubuntu 16.04
- RHEL 8。0
- Debian 9
- Debian 10

本文提供秘訣和建議，以確保您的工作負載和應用程式能夠達到最高的效能，並將其設計到 VM 中。 此頁面上的資訊將會持續更新，因為已將更多 Lsv2 的最佳化影像新增至 Azure Marketplace。

## <a name="amd-eypc-chipset-architecture"></a>AMD EYPC™ 晶片架構

Lsv2 系列 VM 會使用以 Zen 微架構為基礎的 AMD EYPC™ 伺服器處理器。 AMD 針對 EYPC™ 開發了 Infinity Fabric (IF) 做為其 NUMA 模型的可擴充互連，可用於內部、套件內部和多套件通訊。 相較於 Intel 現代化多核心單晶粒處理器所使用的 QPI (快速路徑互連) 和 UPI (Ultra 路徑互連)，AMD 的多 NUMA 小晶粒架構可能會帶來效能優勢和挑戰。 記憶體頻寬和延遲條件約束的實際影響可能會根據執行的工作負載類型而有所不同。

## <a name="tips-to-maximize-performance"></a>最大化效能的秘訣

* 如果您要上傳工作負載的自訂 Linux GuestOS，請注意，根據預設，加速網路將會 **關閉** 。 如果您想要啟用加速網路，請在建立 VM 時啟用它以獲得最佳效能。

* 為 Lsv2 系列 VM 提供動力的硬體，會利用具有 8 個 I/O 佇列配對 (QP) 的 NVMe 裝置。 每個 NVMe 裝置 I/O 佇列實際上都是成對的：提交佇列和完成佇列。 NVMe 驅動程式已設定為透過散發循環配置資源排程中的 I/O，將這八個 I/O QP 的使用率最佳化。 若要獲得最大效能，請在每部裝置上執行八個作業以進行比對。

* 避免在使用中的工作負載期間，混用 NVMe 管理命令 (例如 NVMe SMART 資訊查詢等) 與 NVMe I/O 命令。 Lsv2 NVMe 裝置是由 Hyper-V NVMe Direct 技術支援，每當有任何 NVMe 的管理命令暫止時，就會切換為「慢速模式」。 如果發生這種情況，Lsv2 使用者可能會在 NVMe I/O 效能中看到顯著的效能下降。

* Lsv2 使用者不應仰賴從 VM 內部回報的裝置 NUMA 資訊 (全部 0)，來決定其應用程式的 NUMA 親和性。 建議的提升效能方式是盡可能將工作負載分散到多個 CPU。

* Lsv2 VM NVMe 裝置的每個 I/O 佇列配對支援的佇列深度上限為 1024 (與Amazon i3 QD 32 限制)。 Lsv2 使用者應該將其 (綜合) 基準工作負載限制為佇列深度 1024 或更低，以避免觸發佇列的完整狀況，這可能會降低效能。

## <a name="utilizing-local-nvme-storage"></a>利用本機 NVMe 儲存體

在所有 Lsv2 VM 的 1.92 TB NVMe 磁碟上，本機存放裝置是暫時的。 在 VM 的成功標準重新開機期間，本機 NVMe 磁碟上的資料將保留。 如果將 VM 重新部署、解除配置或刪除，資料將不會保存在 NVMe 上。 如果另一個問題造成 VM 或其執行的硬體變成狀況不良，則資料不會保留。 發生這種情況時，會安全地清除舊主機上的所有資料。

在某些情況下，VM 必須移至不同的主機電腦，例如在計劃性維護作業期間。 計劃性維護作業和一些硬體故障可透過 [Scheduled Events](scheduled-events.md) 進行預期。 Scheduled Events 應該用來隨時更新任何預測的維護和復原作業。

如果計劃性維護事件需要在具有空白本機磁碟的新主機上重新建立 VM，則必須重新同步處理資料 (同樣地，舊主機上的所有資料都要安全地清除)。 這是因為 Lsv2 系列 VM 目前不支援在本機 NVMe 磁碟上進行即時移轉。

有兩種模式可進行計劃性維護。

### <a name="standard-vm-customer-controlled-maintenance"></a>標準 VM 客戶控制的維護

- 在 30 天的時間範圍內，VM 會移至更新的主機。
- Lsv2 本機儲存體資料可能會遺失，因此建議您在進行事件之前備份資料。

### <a name="automatic-maintenance"></a>自動維護

- 當客戶未執行客戶控制的維護，或發生緊急程序 (例如「安全性零時差」事件) 時即會發生。
- 其目的是要保留客戶資料，但 VM 凍結或重新開機的風險很小。
- Lsv2 本機儲存體資料可能會遺失，因此建議您在進行事件之前備份資料。

針對任何即將推出的服務事件，請使用受控制的維護程序來選取您最方便進行更新的時間。 在事件之前，您可以備份進階儲存體中的資料。 維護事件完成之後，您可以將資料傳回至重新整理的 Lsv2 VM 本機 NVMe 儲存體。

在本機 NVMe 磁碟上維護資料的案例包括：

- VM 正在執行且狀況良好。
- VM 會就地重新開機 (由您或 Azure)。
- VM 已暫停 (未解除配置即停止)。
- 大部分計劃性維護服務作業。

安全地清除資料以保護客戶的案例包括：

- VM 會重新部署、停止 (解除配置) 或刪除 (由您執行)。
- VM 會變得狀況不良，而且由於硬體問題而必須對另一個節點進行服務修復。
- 需要將 VM 重新配置給另一部主機以進行服務的少數計劃性維護作業。

若要深入了解在本機儲存體中備份資料的選項，請參閱 [Azure IaaS 磁碟的備份和災害復原](../backup-and-disaster-recovery-for-azure-iaas-disks.md)。

## <a name="frequently-asked-questions"></a>常見問題集

* **如何開始部署 Lsv2 系列 VM？**  
   就像任何其他 VM 一樣，請使用[入口網站](quick-create-portal.md)、[Azure CLI](quick-create-cli.md) 或 [PowerShell](quick-create-powershell.md) 來建立 VM。

* **單一 NVMe 磁碟失敗是否會導致主機上的所有 VM 失敗？**  
   如果在硬體節點上偵測到磁碟失敗，則硬體會處於失敗狀態。 發生這種情況時，節點上的所有 VM 都會自動解除配置，並移至狀況良好的節點。 對於 Lsv2 系列 VM，這表示客戶在失敗節點上的資料也會安全地清除，且必須由客戶在新節點上重新建立。 如先前所述，在 Lsv2 上推出「即時移轉」之前，失敗節點上的資料會在 VM 傳輸至另一個節點時，主動隨之移動。

* **我是否需要對效能進行調整以進行 rq_affinity？**  
   使用每秒的絕對最大輸入/輸出作業 (IOPS) 時，rq_affinity 設定為次要調整。 一旦一切正常運作，請嘗試將 rq_affinity 設定為0，以查看是否有差異。

* **我是否需要變更 blk_mq 設定？**  
   RHEL/CentOS 7.x 會自動針對 NVMe 裝置使用 blk-mq。 不需要進行任何設定變更或設定。 Scsi_mod。使用 _blk_mq 設定僅適用于 SCSI，並在 Lsv2 Preview 期間使用，因為 NVMe 裝置在來賓 Vm 中顯示為 SCSI 裝置。 目前 NVMe 裝置會顯示為 NVMe 裝置，因此 SCSI blk-mq 設定是不相關的。

* **我需要變更 "fio" 嗎？**  
   若要使用效能測量工具（例如 L64v2 和 L80v2 VM 大小中的 ' fio '）來取得最大 IOPS，請在每個 NVMe 裝置上將 "rq_affinity" 設定為0。  例如，此命令列將 L80v2 VM 中所有10部 NVMe 裝置的 "rq_affinity" 設定為零：

   ```console
   for i in `seq 0 9`; do echo 0 >/sys/block/nvme${i}n1/queue/rq_affinity; done
   ```

   另請注意，當 i/o 直接對每個原始 NVMe 裝置（沒有磁碟分割、無檔案系統、無 RAID 0 設定等）進行時，就會取得最佳效能。開始測試會話之前，請先在 NVMe 裝置上執行，以確定 configuration 處於已知的全新/清除狀態 `blkdiscard` 。
   
## <a name="next-steps"></a>後續步驟

* 請參閱針對 Azure 上所有的[儲存體效能進行最佳化的 VM](../sizes-storage.md) 規格
