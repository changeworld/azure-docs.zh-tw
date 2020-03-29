---
title: Azure 網站恢復中的 VMware VM 災害復原體系結構
description: 本文概述使用 Azure Site Recovery 來設定從內部部署 VMware VM 至 Azure 的災害復原時，所使用的元件和架構
author: rayne-wiselman
ms.service: site-recovery
services: site-recovery
ms.topic: conceptual
ms.date: 11/06/2019
ms.author: raynew
ms.openlocfilehash: ccf258594aa68fc9b5d0189c9ada640078e0ba6f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "76514862"
---
# <a name="vmware-to-azure-disaster-recovery-architecture"></a>VMware 至 Azure 災害復原架構

本文說明使用 [Azure Site Recovery](site-recovery-overview.md) 服務在內部部署 VMware 網站與 Azure 之間部署 VMware 虛擬機器 (VM) 的災害復原複寫、容錯移轉和復原時，所使用的架構和程序。


## <a name="architectural-components"></a>架構元件

下表和圖形提供將 VMware 災害復原至 Azure 時所用元件的高層級檢視。

**元件** | **要求** | **詳細資料**
--- | --- | ---
**Azure** | Azure 訂閱、緩存的 Azure 存儲帳戶、託管磁片和 Azure 網路。 | 來自本地 VM 的複製資料存儲在 Azure 存儲中。 從內部部署環境容錯移轉至 Azure 時，會以複寫的資料建立 Azure VM。 Azure VM 在建立後會連線到 Azure 虛擬網路。
**組態伺服器電腦** | 單一的內部部署電腦。 建議您將其當作可從下載的 OVF 範本部署的 VMware VM 來執行。<br/><br/> 此機器會執行所有的內部部署 Site Recovery 元件，包括組態伺服器、處理序伺服器和主要目標伺服器。 | **組態伺服器**：負責協調內部部署與 Azure 之間的通訊，以及管理資料複寫。<br/><br/> **處理序伺服器**：依預設會安裝在組態伺服器上。 負責接收複寫資料，以快取、壓縮和加密進行最佳化，然後將複寫資料傳送至 Azure 儲存體。 處理序伺服器也會在您要複寫的 VM 上安裝 Azure Site Recovery 行動服務，並自動探索內部部署機器。 隨著部署規模擴大，您可以新增額外的個別處理序伺服器，以處理日較大的複寫流量。<br/><br/> **主要目標伺服器**：預設會安裝在組態伺服器上。 負責處理從 Azure 進行容錯回復期間的複寫資料。 針對大型部署，您可以新增額外的個別主要目標伺服器進行容錯回復。
**VMware 伺服器** | VMware VMs 會裝載於內部部署 vSphere ESXi 伺服器。 我們建議以 vCenter 伺服器管理主機。 | 在 Site Recovery 部署期間，您可將 VMware 伺服器新增至復原服務保存庫。
**複寫的機器** | 行動服務會安裝在您複寫的每個 VMware VM 上。 | 建議您允許從處理序伺服器自動安裝。 或者，您可以手動安裝服務或使用自動部署方法，如組態管理員。

**VMware 至 Azure 架構**

![元件](./media/vmware-azure-architecture/arch-enhanced.png)



## <a name="replication-process"></a>複寫程序

1. 當您啟用 VM 複寫時，首先會使用指定的複寫原則開始複寫至 Azure 儲存體。 請注意：
    - 如為 VMware VM，複寫程序為區塊層級、幾乎連續性，並會使用在 VM 執行的流動性服務代理程式。
    - 任何複寫原則設定均會套用：
        - **RPO 閾值**. 此設定不會影響複寫。 其可協助進行監視。 如果目前的 RPO 超過您指定的閾值限制，則系統會引發事件並選擇性傳送電子郵件。
        - **復原點保留**。 發生中斷時，此設定會指定您所希望回溯的時間。 進階儲存體中的保留期上限為 24 小時。 標準儲存體則為 72 小時。 
        - **應用程式一致快照集**。 應用程式快照集會視應用程式的需求每隔 1 至 12 小時拍攝一次。 快照集為標準的 Azure blob 快照集。 在 VM 執行的流動性代理程式會根據此設定要求 VSS 快照集，並在複寫串流中將該時間點標記為應用程式一致時間點。

2. 流量會透過網際網路複寫到 Azure 儲存體的公用端點。 或者，您可以將 Azure ExpressRoute 與[Microsoft 對等互連](../expressroute/expressroute-circuit-peerings.md#microsoftpeering)一起使用。 不支援從內部部署網站透過站對站虛擬私人網路 (VPN) 將流量複寫至 Azure。
3. 初始複寫完成之後，就會開始將差異變更複寫到 Azure。 機器的追蹤變更會傳送至流程伺服器。
4. 進行通訊的過程如下：

    - VM 會在輸入連接埠 HTTPS 443 上與內部部署設定伺服器進行通訊，以進行複寫管理。
    - 設定伺服器會透過輸出連接埠 HTTPS 443 與 Azure 協調複寫。
    - VM 會透過輸入連接埠 HTTPS 9443 將複寫資料傳送至處理伺服器 (在設定伺服器電腦上執行)。 您可以修改此連接埠。
    - 處理伺服器會透過輸出連接埠 443 接收複寫資料、將其最佳化並加密，然後傳送至 Azure 儲存體。
5. 複製資料首先記錄在 Azure 中的緩存存儲帳戶中。 這些日誌將處理，並將資料存儲在 Azure 託管磁片（稱為 asr 種子磁片）中。 復原點在此磁片上創建。




**VMware 到 Azure 複寫程序**

![複寫程序](./media/vmware-azure-architecture/v2a-architecture-henry.png)

## <a name="failover-and-failback-process"></a>容錯移轉和容錯回復程序

設定複寫並執行災害復原演練 (測試容錯移轉) 來檢查一切如預期般運作之後，您可以視需要執行容錯移轉和容錯回復。

1. 您可以容錯移轉單一電腦，或建立復原方案來同時容錯移轉多個 VM。 與單一電腦容錯移轉相比，復原方案的優點包括	：
    - 您可以藉由將應用程式的所有 VM 都納入單一復原方案中，來建立應用程式相依性模型。
    - 您可以新增指令碼、Azure Runbook 及暫停來進行手動動作。
2. 觸發初始容錯移轉之後，您要認可讓它開始從 Azure VM 存取工作負載。
3. 當主要內部部署網站恢復可用狀態時，您就可以準備容錯回復。 若要進行容錯回復，您必須設定容錯回復基礎結構，包括：

    * **Azure 中的暫時處理序伺服器**︰若要從 Azure 容錯回復，您可以設定 Azure VM 作為處理序伺服器，以處理來自 Azure 的複寫。 容錯回復完成後，您可以刪除此 VM。
    * **VPN 連線**：若要容錯回復，您必須要有從 Azure 網路到內部部署網站的 VPN 連線 (或 ExpressRoute)。
    * **個別的主要目標伺服器**︰根據預設，隨組態伺服器安裝的主要目標伺服器 (在內部部署 VMware VM 上) 會處理容錯回復。 如果您需要容錯回復大量流量，請針對此用途設定個別的內部部署主要目標伺服器。
    * **容錯回復原則**︰若要複寫回到內部部署網站，您需要容錯回復原則。 當您建立從內部部署至 Azure 的複寫原則後，此原則會自動建立。
4. 備妥元件之後，容錯回復會分三個階段進行：

    - 第 1 階段：重新保護 Azure VM，使其能從 Azure 複寫回到內部部署 VMware VM。
    -  第 2 階段：執行容錯移轉至內部部署網站。
    - 第 3 階段：工作負載進行容錯回復之後，您必須重新啟用內部部署 VM 的複寫。
    
 
**從 Azure 容錯回復 VMware**

![容錯回復](./media/vmware-azure-architecture/enhanced-failback.png)


## <a name="next-steps"></a>後續步驟

請遵循[此教學課程](vmware-azure-tutorial.md)，以啟用 VMware 至 Azure 的複寫。
