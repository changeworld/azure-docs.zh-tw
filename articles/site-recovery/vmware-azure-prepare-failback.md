---
title: 使用 Azure 網站恢復準備 VMware VM 以重新保護和故障恢復
description: 使用 Azure 網站恢復在容錯移轉後為 VMware VM 故障恢復做好準備
ms.topic: conceptual
ms.date: 12/24/2019
ms.openlocfilehash: 5a330f8cba31640d0116ca3d5ccab352ce5b3509
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79257182"
---
# <a name="prepare-for-reprotection-and-failback-of-vmware-vms"></a>為 VMware VM 的重新保護和故障恢復做好準備

在本地 VMware VM 或物理伺服器[容錯移轉到](site-recovery-failover.md)Azure 後，將重新保護容錯移轉後創建的 Azure VM，以便它們複製回本地網站。 將 Azure 複製到本地就位後，您可以在準備就緒時運行從 Azure 到本地的容錯移轉來故障恢復。

在繼續之前，請快速流覽此視頻，瞭解如何從 Azure 故障回本地網站。<br /><br />
> [!VIDEO https://channel9.msdn.com/Series/Azure-Site-Recovery/VMware-to-Azure-with-ASR-Video5-Failback-from-Azure-to-On-premises/player]

## <a name="reprotectionfailback-components"></a>重新保護/故障回退元件

您需要在位許多元件和設置之前，才能重新保護和故障從 Azure 中返回。

**元件**| **詳細資料**
--- | ---
**內部部署組態伺服器** | 本地佈建服務器必須運行並連接到 Azure。<br/><br/> 您故障返回的 VM 必須存在於佈建服務器資料庫中。 如果災難影響佈建服務器，請使用相同的 IP 位址還原它，以確保故障償還工作。<br/><br/>  如果在容錯移轉時保留複製電腦的 IP 位址，則應在 Azure VM 電腦和佈建服務器的故障回退 NIC 之間建立網站到網站的連接（或 ExpressRoute 連接）。 對於保留的 IP 位址，佈建服務器需要兩個 NIC- 一個用於源電腦連接，另一個用於 Azure 故障恢復連接。 這樣可以避免源的子網位址範圍重疊，並且通過 VM 失敗。
**Azure 中的處理伺服器** | 在故障回本地網站之前，需要在 Azure 中運行進程伺服器。<br/><br/> 進程伺服器從受保護的 Azure VM 接收資料，並將其發送到本地網站。<br/><br/> 進程伺服器和受保護的 VM 之間需要低延遲網路，因此我們建議您在 Azure 中部署進程伺服器以提高複製性能。<br/><br/> 對於概念驗證，可以使用本地進程伺服器和 ExpressRoute 進行專用對等互連。<br/><br/> 進程伺服器應位於 Azure 網路中，其中故障的 VM 位於其中。 進程伺服器還必須能夠與本地佈建服務器和主目標伺服器通信。
**單獨的主目標伺服器** | 主目標伺服器接收故障恢復資料，預設情況下，Windows 主目標伺服器在本地佈建服務器上運行。<br/><br/> 主目標伺服器最多可連接到 60 個磁片。 失敗回的 VM 總共有 60 個磁片，或者如果正在故障回大量流量，請創建單獨的主目標伺服器進行故障倒正。<br/><br/> 如果電腦被收集到一個複製組中，以實現多 VM 一致性，則 VM 必須全部為 Windows，或者必須都是 Linux。 原因為何？ 由於複製組中的所有 VM 都必須使用相同的主目標伺服器，並且主目標伺服器必須具有相同的作業系統（具有相同或更高的版本）與複製的電腦的作業系統相同。<br/><br/> 主目標伺服器的磁片上不應有任何快照，否則重新保護和故障恢復將不起作用。<br/><br/> 主要目標不可以有 Paravirtual SCSI 控制器。 控制器只能是 LSI Logic 控制器。 如果沒有 LSI Logic 控制器，重新保護會失敗。
**故障恢復複寫原則** | 要複製回本地網站，您需要故障倒回策略。 在創建 Azure 複寫原則時，將自動創建此策略。<br/><br/> 此原則會自動與設定伺服器產生關聯。 它設置為 15 分鐘的 RPO 閾值，復原點保留 24 小時，應用一致的快照頻率為 60 分鐘。 無法編輯策略。 
**網站到網站 VPN/ExpressRoute 專用對等互連** | 重新保護和故障恢復需要網站到網站 VPN 連接，或 ExpressRoute 專用對等互連來複製資料。 


## <a name="ports-for-reprotectionfailback"></a>用於重新保護/故障恢復的埠

許多埠必須打開才能重新保護/故障恢復。 下圖說明瞭埠和重新保護/故障恢復流。

![容錯移轉和容錯回復的端點](./media/vmware-azure-reprotect/failover-failback.png)


## <a name="deploy-a-process-server-in-azure"></a>在 Azure 中部署處理序伺服器

1. 在 Azure 中[設置進程伺服器](vmware-azure-set-up-process-server-azure.md)以進行故障倒機。
2. 確保 Azure VM 可以到達進程伺服器。 
3. 確保網站到網站 VPN 連接或 ExpressRoute 專用對等互連網路具有足夠的頻寬，可以將資料從進程伺服器發送到本地網站。

## <a name="deploy-a-separate-master-target-server"></a>部署個別的主要目標伺服器

1. 請注意主目標伺服器[要求和限制](#reprotectionfailback-components)。
2. 創建[Windows](site-recovery-plan-capacity-vmware.md#deploy-additional-master-target-servers)或[Linux](vmware-azure-install-linux-master-target.md)主目標伺服器，以匹配要重新保護和故障恢復的 VM 的作業系統。
3. 確保不會為主目標伺服器使用存儲 vMotion，否則故障恢復可能會失敗。 VM 電腦無法啟動，因為磁片不可用。
    - 為此，請從 vMotion 清單中排除主目標伺服器。
    - 如果主目標在重新保護後執行存儲 vMotion 任務，則附加到主目標伺服器的受保護 VM 磁片將遷移到 vMotion 任務的目標。 如果嘗試在此之後故障恢復，磁片分離將失敗，因為找不到磁片。 然後很難在存儲帳戶中找到磁片。 如果發生這種情況，請手動查找它們並將其附加到 VM。 之後，可以啟動本地 VM。

4. 將保留磁碟機添加到現有 Windows 主目標伺服器。 新增磁碟並格式化磁碟機。 保留磁碟機用於在 VM 複製回本地網站時停止時間點。 請注意這些條件。 如果未滿足它們，則不會為主目標伺服器列出磁碟機：
    - 該卷不用於任何其他目的，如複製目標，並且它不處於鎖定模式。
    - 磁碟區不是快取磁碟區。 處理序伺服器和主要目標的自訂安裝磁碟區不符合作為保留磁碟區的資格。 當處理序伺服器和主要目標安裝在磁碟區時，磁碟區是主要目標的快取磁碟區。
    - 磁碟區的檔案系統類型不是 FAT 或 FAT32。
    - 磁碟區容量不是零。
    - Windows 的預設保留磁碟區為 R 磁碟區。
    - Linux 的預設保留磁碟區為 /mnt/retention。

5. 如果使用現有進程伺服器，則添加磁碟機。 新磁碟機必須滿足最後一步中的要求。 如果保留磁碟機不存在，則不會出現在入口網站上的選取下拉式清單中。 將磁碟機新增至內部部署主要目標之後，磁碟機最多需要 15 分鐘才會出現在入口網站上的選取項目中。 如果磁碟機在 15 分鐘後未顯示，則可以刷新佈建服務器。
6. 在主要目標伺服器上安裝 VMware 工具或 open-vm-tools。 如果沒有工具，就無法偵測主要目標 ESXi 主機上的資料存放區。
7. 設置磁片。啟用UUID=在 VMware 中主目標 VM 的配置參數中實現 true 設置。 如果此列不存在，請加以新增。 必須進行此設定，才能提供一致的 UUID 給 VMDK，使其可正確掛接。
8. 檢查 vCenter 伺服器訪問要求：
    - 如果故障返回的 VM 位於 VMware vCenter Server 管理的 ESXi 主機上，則主目標伺服器需要訪問本地 VM 虛擬機器磁片 （VMDK） 檔，以便將複製的資料寫入虛擬機器的磁片。 確保本地 VM 資料存儲安裝在主目標主機上，具有讀/寫存取權限。
    - 如果 VM 不在 VMware vCenter 伺服器管理的 ESXi 主機上，則網站恢復會在重新保護期間創建新的 VM。 此 VM 是在創建主目標伺服器 VM 的 ESXi 主機上創建的。 請仔細選擇 ESXi 主機，在所需的主機上創建 VM。 VM 的硬碟必須位於可供主要目標伺服器執行所在主機存取的資料存放區中。
    - 另一個選項 (如果內部部署 VM 已存在以進行容錯回復) 是先將其刪除，然後才執行容錯回復。 然後，故障回退在主目標 ESXi 主機的同一主機上創建新 VM。 當您故障返回備用位置時，資料將恢復到與本地主目標伺服器使用的相同資料存儲和相同的 ESXi 主機。
9. 對於故障返回 VMware VM 的物理電腦，在重新保護電腦之前，應完成主目標伺服器運行的主機的發現。
10. 檢查主目標 VM 的 ESXi 主機中至少附加了一個虛擬機器檔案系統 （VMFS） 資料存儲。 如果未附加 VMFS 資料存儲，則重新保護設置中的資料存儲輸入為空，無法繼續。


## <a name="next-steps"></a>後續步驟

[重新保護](vmware-azure-reprotect.md)VM。
