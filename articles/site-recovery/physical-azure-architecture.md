---
title: Azure 網站恢復中的物理伺服器災害復原體系結構
description: 本文概述使用 Azure Site Recovery 服務將內部部署實體伺服器災害復原至 Azure 期間，所使用的元件和架構。
ms.topic: conceptual
ms.date: 02/11/2020
ms.openlocfilehash: 089d981284986a2b6eb0ee7f1dbd401fc7ce4fcd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "77162832"
---
# <a name="physical-server-to-azure-disaster-recovery-architecture"></a>實體伺服器至 Azure 的災害復原架構

本文說明透過 [Azure Site Recovery](site-recovery-overview.md) 服務，在內部部署網站與 Azure 之間複寫、容錯移轉和復原 Windows 和 Linux 實體伺服器時所使用的架構和程序。

## <a name="architectural-components"></a>架構元件

下表和圖形提供了用於將物理伺服器複製到 Azure 的元件的高級視圖。

| **元件** | **要求** | **詳細資料** |
| --- | --- | --- |
| **Azure** | Azure 訂閱和 Azure 網路。 | 來自本地物理電腦的複製資料存儲在 Azure 託管磁片中。 從內部部署環境容錯移轉至 Azure 時，會以複寫的資料建立 Azure VM。 Azure VM 在建立後會連線到 Azure 虛擬網路。 |
| **處理序伺服器** | 預設會與組態伺服器一起安裝。 | 會做為複寫閘道器。 接收複寫資料，以快取、壓縮和加密進行最佳化，然後將複寫資料傳送至 Azure 儲存體。<br/><br/> 處理序伺服器也會在您要複寫的伺服器上安裝行動服務。<br/><br/> 隨著部署規模擴大，您可以新增額外的個別處理序伺服器，以處理日較大的複寫流量。 |
| **主要目標伺服器** | 預設會與組態伺服器一起安裝。 | 在從 Azure 返回故障期間處理複製資料。<br/><br/> 針對大型部署，您可以新增額外的個別主要目標伺服器進行容錯回復。 |
| **複寫的伺服器** | 行動服務會安裝在您複寫的每部伺服器上。 | 我們建議您允許從處理序伺服器自動安裝。 或者，您可以手動安裝服務，或使用自動部署方法（如組態管理員）。 |

**實體至 Azure 架構**

![元件](./media/physical-azure-architecture/arch-enhanced.png)

## <a name="replication-process"></a>複寫程序

1. 您需設定部署，包括內部部署和 Azure 元件。 在 Recovery Services 保存庫中，您需指定複寫來源和目標、設定組態伺服器、建立複寫原則，以及啟用複寫。
1. 電腦使用複寫原則複製，伺服器資料的初始副本將複製到 Azure 存儲。
1. 初始複寫完成之後，就會開始將差異變更複寫到 Azure。 電腦的跟蹤更改將包含在具有 _.hrl_副檔名的檔中。
   - 電腦與 HTTPS 埠 443 入站上的佈建服務器通信，以便進行複製管理。
   - 電腦將複製資料發送到 HTTPS 埠 9443 入站上的進程伺服器（可修改）。
   - 佈建服務器通過 HTTPS 埠 443 出站使用 Azure 協調複製管理。
   - 進程伺服器從源電腦接收資料，對其進行優化和加密，然後通過 HTTPS 埠 443 出站將其發送到 Azure 存儲。
   - 如果您啟用多部 VM 一致性，則複寫群組中的機器會透過連接埠 20004 彼此通訊。 如果您將多部機器群組為幾個共用當機時保持一致復原點和應用程式一致復原點的複寫群組，當這些群組在進行容錯移轉時，便會使用多部 VM。 如果電腦運行相同的工作負載並且需要保持一致，則這些組非常有用。
1. 流量透過網際網路複寫到 Azure 儲存體的公用端點。 或者，可以使用 Azure ExpressRoute[公共對等互連](../expressroute/about-public-peering.md)。

   > [!NOTE]
   > 本地網站或 Azure ExpressRoute[專用對等互連](concepts-expressroute-with-site-recovery.md#on-premises-to-azure-replication-with-expressroute)不支援通過網站到網站 VPN 進行複製。

**實體到 Azure 複寫程序**

![複寫程序](./media/physical-azure-architecture/v2a-architecture-henry.png)

## <a name="failover-and-failback-process"></a>容錯移轉和容錯回復程序

設置複製後，可以運行災害復原鑽（測試容錯移轉），以檢查一切是否按預期工作。 然後，您可以根據需要容錯移轉和故障恢復。 請考慮下列項目：

- 不支援有計劃的容錯移轉。
- 故障回本地 VMware VM 是必需的。 您需要本地 VMware 基礎結構，即使您將本地物理伺服器複製到 Azure 也是如此。
- 您可容錯移轉單一機器，或建立復原方案，同時容錯移轉多部機器。
- 當您執行容錯移轉時，系統會從 Azure 儲存體中複寫的資料建立 Azure VM。
- 觸發初始容錯移轉後，將提交它開始從 Azure VM 訪問工作負荷。
- 當主要的內部部署網站恢復可用狀態時，您就可以容錯回復。
- 設置故障恢復基礎結構，其中包括：
  - **Azure 中的臨時進程伺服器**：要從 Azure 故障回，可以設置 Azure VM 作為進程伺服器來處理來自 Azure 的複製。 可以刪除此 VM 後故障返回完成。
  - **VPN 連線**：若要容錯回復，您需要有從 Azure 網路到內部部署網站的 VPN 連線 (或 Azure ExpressRoute)。
  - **單獨的主目標伺服器**：預設情況下，故障返回由與佈建服務器一起安裝在本地 VMware VM 上的主目標伺服器處理。 如果需要故障回退大量流量，則應設置單獨的本地主目標伺服器。
  - **容錯回復原則**︰若要複寫回到內部部署網站，您需要容錯回復原則。 當您從本地創建複寫原則到 Azure 時，將自動創建策略。
  - **VMware 基礎設施**：要故障，您需要 VMware 基礎結構。 您無法容錯回復到實體伺服器。
- 元件到位後，故障恢復分三個階段發生：
  - **階段 1：** 重新保護 Azure VM，以便它們從 Azure 複製回本地 Vm。
  - **階段 2**：運行容錯移轉到本地網站。
  - **階段 3：** 在工作負載失敗後，重新啟用複製。

**從 Azure 容錯回復 VMware**

![容錯回復](./media/physical-azure-architecture/enhanced-failback.png)

## <a name="next-steps"></a>後續步驟

要將物理伺服器的災害復原設置為 Azure，請參閱[操作指南](physical-azure-disaster-recovery.md)。
