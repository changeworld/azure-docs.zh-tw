---
title: 架構-Hyper-v 嚴重損壞修復至具有 Azure Site Recovery 的次要網站
description: 本文提供使用 Azure Site Recovery 將內部部署 Hyper-V VM 災害復原至次要 System Center VMM 網站的架構概觀。
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/12/2019
ms.author: raynew
ms.openlocfilehash: fbd11c279708cd828693baab3f9f6df91515bc48
ms.sourcegitcommit: e995f770a0182a93c4e664e60c025e5ba66d6a45
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/08/2020
ms.locfileid: "86133896"
---
# <a name="architecture---hyper-v-replication-to-a-secondary-site"></a>架構 - Hyper-V 複寫至次要網站

本文說明在 Azure 入口網站中使用 [Azure Site Recovery](site-recovery-overview.md) 服務將 System Center Virtual Machine Manager (VMM) 雲端中的內部部署 Hyper-V 虛擬機器 (VM) 複寫至次要 VMM 網站時所涉及的元件和程序。
a

## <a name="architectural-components"></a>架構元件

下表和圖形提供將 Hyper-V 複寫至次要網站時所用元件的高層級檢視。

**元件** | **需求** | **詳細資料**
--- | --- | ---
**Azure** | Azure 訂用帳戶 | 您可以在 Azure 訂用帳戶中建立復原服務保存庫，來協調和管理 VMM 位置之間的複寫。
**VMM 伺服器** | 您需要 VMM 主要和次要位置。 | 我們建議主要網站與次要網站中各要有一部 VMM 伺服器。
**Hyper-v 伺服器** |  在主要和次要 VMM 雲端中，有一或多部 Hyper-V 主機伺服器。 | 在主要和次要 Hyper-V 主機伺服器之間，使用 Kerberos 或憑證驗證透過 LAN 或 VPN 來複寫資料。  
**Hyper-V VM** | 在 Hyper-V 主機伺服器上。 | 來源主機伺服器應該至少有一個您想要複寫的 VM。

**內部部署至內部部署架構**

![內部部署至內部部署](./media/hyper-v-vmm-architecture/arch-onprem-onprem.png)

## <a name="replication-process"></a>複寫程序

1. 觸發初始複寫時，會建立[HYPER-V VM 快照](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd560637(v=ws.10))集快照集。
2. VM 上的虛擬硬碟會逐一複寫至次要位置。
3. 如果在初始複寫進行時發生磁碟變更，Hyper-V 複本複寫追蹤器會以 Hyper-V 複寫記錄 (.hrl) 的形式追蹤變更。 這些記錄檔位於與磁碟相同的資料夾中。 每個磁碟都有一個相關聯的 .hrl 檔案會傳送至次要位置。 當初始複寫正在進行時，快照和記錄檔會取用磁碟資源。
4. 初始複寫完成時，就會刪除 VM 快照集，並開始差異複寫。
5. 記錄中的差異磁碟變更會同步處理，並合併到父磁碟。


## <a name="failover-and-failback-process"></a>容錯移轉和容錯回復程序

- 您可以容錯移轉單一機器，或建立復原方案來協調多部機器的容錯移轉。
- 您可以在內部部署網站間執行計劃性或非計劃性的容錯移轉。 如果您執行計劃性容錯移轉，則來源 VM 會關閉以確保不會遺失資料。
    - 如果您執行非計劃性容錯移轉到次要網站，則在容錯移轉之後，不會保護次要位置中的容錯移轉機器。
    - 如果您執行了計劃性容錯移轉，則在容錯移轉之後，次要位置中的容錯移轉機器會受到保護。
- 執行初始容錯移轉之後，您要認可讓它開始從複寫 VM 存取工作負載。
- 當主要位置恢復可用狀態時，您就可以容錯回復。
    - 您可起始反向複寫，開始從次要網站複寫到主要網站。 反向複寫會讓虛擬機器進入受保護的狀態，但是次要資料中心仍是使用中位置。
    - 若要讓主要網站再次成為使用中位置，您需要起始從次要網站到主要網站的計劃性容錯移轉，然後再進行另一個反向複寫。



## <a name="next-steps"></a>後續步驟


遵循[教學課程](hyper-v-vmm-disaster-recovery.md)的指示，啟用 VMM 雲端之間的 Hyper-V 複寫。
