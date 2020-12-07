---
title: Hyper-v 遷移在 Azure Migrate 中的運作方式為何？
description: 瞭解 Azure Migrate 的 Hyper-v 遷移
author: bsiva
ms.author: bsiva
ms.manager: abhemraj
ms.topic: conceptual
ms.date: 11/19/2019
ms.openlocfilehash: 239918cc19eefbef9e3c3f12d5ddd3bb5434b490
ms.sourcegitcommit: ea551dad8d870ddcc0fee4423026f51bf4532e19
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/07/2020
ms.locfileid: "96751014"
---
# <a name="how-does-hyper-v-replication-work"></a>Hyper-v 複寫的運作方式為何？

本文概述當您使用 Azure Migrate Server 遷移工具來遷移 Hyper-v Vm 時，所使用的架構和處理常式。

[Azure Migrate](migrate-services-overview.md) 提供集中式中樞，可追蹤內部部署應用程式和工作負載及私人/公用雲端 VM 的探索、評量和移轉 (以 Azure 為目標)。 該中樞能提供 Azure Migrate 工具以進行評估和移轉，也提供協力廠商獨立軟體廠商 (ISV) 的供應項目。

## <a name="agentless-migration"></a>無代理程式遷移

Azure Migrate Server 遷移工具會使用針對 Hyper-v 優化的遷移工作流程，為內部部署 Hyper-v Vm 提供無代理程式複寫。 您只需在 Hyper-v 主機或叢集節點上安裝軟體代理程式。 Hyper-v Vm 上不需要安裝任何專案。

## <a name="server-migration-and-azure-site-recovery"></a>伺服器遷移和 Azure Site Recovery

Azure Migrate Server 遷移是將內部部署工作負載和雲端式 Vm 遷移至 Azure 的工具。 Site Recovery 是嚴重損壞修復工具。 這些工具會共用用於資料複寫的一些常見技術元件，但提供不同的用途。 


## <a name="architectural-components"></a>架構元件

![下圖顯示具有 H T T P S 資料通道可 Microsoft Azure 的來源 Hyper-v 網路，其中詳細說明請見表格。](./media/hyper-v-replication-architecture/architecture.png)



**元件** | **部署** | 
--- | --- 
**複寫提供者** | Microsoft Azure Site Recovery 提供者會安裝在 Hyper-v 主機上，並向 Azure 遷移伺服器遷移註冊。<br/> 此提供者會協調 Hyper-v Vm 的複寫。
**復原服務代理程式** | Microsoft Azure 復原服務代理程式會處理資料複寫。 它可與提供者搭配使用，以將資料從 Hyper-v Vm 複寫至 Azure。<br/> 複寫的資料會上傳至您 Azure 訂用帳戶中的儲存體帳戶。 伺服器遷移工具會處理複寫的資料，並將其套用至訂用帳戶中的複本磁片。 當您遷移時，複本磁片會用來建立 Azure Vm。

- 元件是由單一安裝檔案所安裝，可從入口網站中的 Azure Migrate Server 遷移下載。
- 提供者和設備會使用輸出 HTTPS 埠443連線來與 Azure Migrate Server 遷移進行通訊。
- 來自提供者和代理程式的通訊是安全且加密的。


## <a name="replication-process"></a>複寫程序

1. 當您為 Hyper-v VM 啟用複寫時，會開始初始複寫。
2. 採用 Hyper-v VM 快照集。
3. VM 上的 Vhd 會逐一複寫，直到它們全部複製到 Azure 為止。 初始複寫時間取決於 VM 大小和網路頻寬。
4. 在初始複寫期間發生的磁片變更會使用 Hyper-v 複本進行追蹤，並儲存在記錄檔中， (hrl 檔) 。
    - 記錄檔位於與磁片相同的資料夾中。
    - 每個磁片都有一個相關聯的 hrl 檔案，該檔案會傳送至次要儲存體。
    - 當初始複寫正在進行時，快照和記錄檔會取用磁碟資源。
4. 初始複寫完成之後，就會刪除 VM 快照集，並開始進行差異複寫。
5. 增量磁片變更會在 hrl 檔案中追蹤。 復原服務代理程式會定期將複寫記錄上傳到 Azure 儲存體帳戶。


## <a name="performance-and-scaling"></a>效能及延展性

Hyper-v 的複寫效能受到影響，包括 VM 大小、vm 大小 (變換) 、在 Hyper-v 主機上的資料變更率、記錄檔儲存體的可用空間、複寫資料的上傳頻寬，以及 Azure 中的目標儲存體。

- 如果您要同時複寫多部電腦，請使用 Hyper-v 的 [Azure Site Recovery 部署規劃工具](../site-recovery/hyper-v-deployment-planner-overview.md) ，以協助優化複寫。
- 規劃 Hyper-v 複寫，並根據容量將複寫散發到 Azure 儲存體帳戶。

### <a name="control-upload-throughput"></a>控制上傳輸送量

您可以限制在每部 Hyper-v 主機上，用來將資料上傳至 Azure 的頻寬量。 小心。 如果您將值設得太低，則會對複寫造成負面影響，並延遲遷移。


1. 登入 Hyper-v 主機或叢集節點。
2. 執行 **C:\Program Files\Microsoft Azure Recovery Services Agent\bin\wabadmin.msc**，以開啟 WINDOWS Azure 備份 MMC 嵌入式管理單元。
3. 在嵌入式管理單元中，選取 [變更內容]。
4. 在 [ **節流**] 中，選取 [ **啟用備份作業的網際網路頻寬使用節流** 設定]。 設定工作和非工作時數的限制。 有效範圍是 512 Kbps 到 1,023 Mbps。
I

### <a name="influence-upload-efficiency"></a>影響上傳效率

如果您有備用的頻寬可進行複寫，並且想要增加上傳，您可以增加為上傳工作配置的執行緒數目，如下所示：

1. 使用 Regedit 開啟登錄。
2. 流覽至機碼 HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Replication\UploadThreadsPerVM
3. 針對每個複寫 VM 的資料上傳所用的執行緒數目增加值。 預設值為4，而最大值為32。 




## <a name="next-steps"></a>後續步驟

試用使用 Azure Migrate Server 遷移的 [hyper-v 遷移](tutorial-migrate-hyper-v.md) 。
