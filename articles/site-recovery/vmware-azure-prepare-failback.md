---
title: 準備 VMware Vm 以使用 Azure Site Recovery 進行重新保護和容錯回復
description: 準備容錯移轉之後，使用 Azure Site Recovery 進行 VMware Vm 的容錯回復
ms.topic: conceptual
ms.date: 12/24/2019
ms.openlocfilehash: 5a330f8cba31640d0116ca3d5ccab352ce5b3509
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/13/2020
ms.locfileid: "79257182"
---
# <a name="prepare-for-reprotection-and-failback-of-vmware-vms"></a>準備 VMware Vm 的重新保護和容錯回復

將內部部署 VMware Vm 或實體伺服器[容錯移轉](site-recovery-failover.md)至 azure 之後，您會重新保護在容錯移轉後建立的 azure vm，使其複寫回到內部部署網站。 有了從 Azure 到內部部署的複寫，您就可以在準備就緒時，從 Azure 執行容錯移轉至內部部署，以進行容錯回復。

繼續進行之前，請先快速瞭解如何從 Azure 容錯回復至內部部署網站，以取得這段影片。<br /><br />
> [!VIDEO https://channel9.msdn.com/Series/Azure-Site-Recovery/VMware-to-Azure-with-ASR-Video5-Failback-from-Azure-to-On-premises/player]

## <a name="reprotectionfailback-components"></a>重新保護/容錯回復元件

您必須先備妥一些元件和設定，才能從 Azure 重新保護和容錯回復。

**元件**| **詳細資料**
--- | ---
**內部部署組態伺服器** | 內部部署設定伺服器必須正在執行並聯機到 Azure。<br/><br/> 您要容錯回復到的 VM 必須存在於設定伺服器資料庫中。 如果嚴重損壞影響設定伺服器，請使用相同的 IP 位址進行還原，以確保容錯回復能夠運作。<br/><br/>  如果複寫機器的 IP 位址已在容錯移轉時保留，則應該在 Azure Vm 電腦與設定伺服器的容錯回復 NIC 之間建立站對站連線能力（或 ExpressRoute 連線能力）。 針對保留的 IP 位址，設定伺服器需要兩個 Nic-一個用於來源機器連線，另一個用於 Azure 容錯回復連線。 這可避免與來源和已故障 over Vm 的子網位址範圍重迭。
**Azure 中的進程伺服器** | 您在 Azure 中需要有進程伺服器，才能容錯回復至內部部署網站。<br/><br/> 進程伺服器會從受保護的 Azure VM 接收資料，並將其傳送至內部部署網站。<br/><br/> 進程伺服器與受保護的 VM 之間需要低延遲的網路，因此我們建議您在 Azure 中部署進程伺服器，以獲得更高的複寫效能。<br/><br/> 對於概念證明，您可以使用內部部署進程伺服器，以及具有私人對等互連的 ExpressRoute。<br/><br/> 進程伺服器應該位於故障的 VM 所在的 Azure 網路中。 進程伺服器也必須能夠與內部部署設定伺服器和主要目標伺服器通訊。
**個別的主要目標伺服器** | 主要目標伺服器會接收容錯回復資料，而且根據預設，Windows 主要目標伺服器會在內部部署設定伺服器上執行。<br/><br/> 主要目標伺服器最多可以附加60個磁片。 容錯回復的 Vm 超過60磁片的共同總計，或如果您要容錯回復大量流量，請建立個別的主要目標伺服器來進行容錯回復。<br/><br/> 如果將機器收集到複寫群組以進行多部 VM 的一致性，則 Vm 必須全部為 Windows，或必須全部為 Linux。 原因為何？ 因為複寫群組中的所有 Vm 都必須使用相同的主要目標伺服器，而且主要目標伺服器必須具有與複寫電腦相同的作業系統（具有相同或更高的版本）。<br/><br/> 主要目標伺服器在其磁片上不應該有任何快照，否則重新保護和容錯回復將無法使用。<br/><br/> 主要目標不可以有 Paravirtual SCSI 控制器。 控制器只能是 LSI Logic 控制器。 如果沒有 LSI Logic 控制器，重新保護會失敗。
**容錯回復複寫原則** | 若要複寫回內部部署網站，您需要容錯回復原則。 當您建立複寫原則至 Azure 時，會自動建立此原則。<br/><br/> 此原則會自動與設定伺服器產生關聯。 其設定為15分鐘的 RPO 閾值、24小時的復原點保留期，以及應用程式一致快照頻率為60分鐘。 無法編輯此原則。 
**站對站 VPN/ExpressRoute 私人對等互連** | 重新保護和容錯回復需要站對站 VPN 連線，或 ExpressRoute 私用對等互連來複寫資料。 


## <a name="ports-for-reprotectionfailback"></a>重新保護/容錯回復的埠

必須開啟一些埠以進行重新保護/容錯回復。 下圖說明埠和重新保護/容錯回復流程。

![容錯移轉和容錯回復的端點](./media/vmware-azure-reprotect/failover-failback.png)


## <a name="deploy-a-process-server-in-azure"></a>在 Azure 中部署處理序伺服器

1. 在 Azure 中[設定處理伺服器](vmware-azure-set-up-process-server-azure.md)以進行容錯回復。
2. 確定 Azure Vm 可以連線到進程伺服器。 
3. 請確定站對站 VPN 連線或 ExpressRoute 私人對等互連網路有足夠的頻寬，可將來自進程伺服器的資料傳送至內部部署網站。

## <a name="deploy-a-separate-master-target-server"></a>部署個別的主要目標伺服器

1. 請注意主要目標伺服器的[需求和限制](#reprotectionfailback-components)。
2. 建立[Windows](site-recovery-plan-capacity-vmware.md#deploy-additional-master-target-servers)或[Linux](vmware-azure-install-linux-master-target.md)主要目標伺服器，以符合您想要重新保護並容錯回復之 vm 的作業系統。
3. 請確定您不會針對主要目標伺服器使用 Storage vMotion，否則容錯回復會失敗。 無法啟動 VM 電腦，因為磁片無法使用。
    - 若要避免這種情況，請從 vMotion 清單中排除主要目標伺服器。
    - 如果主要目標在重新保護後經歷了儲存體 vMotion 工作，則附加至主要目標伺服器的受保護 VM 磁片會遷移至 vMotion 工作的目標。 如果您嘗試在此之後進行容錯回復，磁片表示中斷連結會失敗，因為找不到磁片。 然後，很難在您的儲存體帳戶中找到磁片。 如果發生這種情況，請手動尋找它們，並將它們連結至 VM。 之後，就可以啟動內部部署 VM。

4. 將保留磁片磁碟機新增至現有的 Windows 主要目標伺服器。 新增磁碟並格式化磁碟機。 保留磁片磁碟機可用來停止 VM 複寫回內部部署網站時的時間點。 請注意這些準則。 如果不符合，則不會針對主要目標伺服器列出磁片磁碟機：
    - 磁片區不會用於任何其他用途，例如複寫目標，而且不會處於鎖定模式。
    - 磁碟區不是快取磁碟區。 處理序伺服器和主要目標的自訂安裝磁碟區不符合作為保留磁碟區的資格。 當處理序伺服器和主要目標安裝在磁碟區時，磁碟區是主要目標的快取磁碟區。
    - 磁碟區的檔案系統類型不是 FAT 或 FAT32。
    - 磁碟區容量不是零。
    - Windows 的預設保留磁碟區為 R 磁碟區。
    - Linux 的預設保留磁碟區為 /mnt/retention。

5. 如果您要使用現有的進程伺服器，請新增磁片磁碟機。 新的磁片磁碟機必須符合上一個步驟中的需求。 如果保留磁碟機不存在，則不會出現在入口網站上的選取下拉式清單中。 將磁碟機新增至內部部署主要目標之後，磁碟機最多需要 15 分鐘才會出現在入口網站上的選取項目中。 如果磁片磁碟機未在15分鐘後出現，您可以重新整理設定伺服器。
6. 在主要目標伺服器上安裝 VMware 工具或 open-vm-tools。 如果沒有工具，就無法偵測主要目標 ESXi 主機上的資料存放區。
7. 設定磁片。Disk.enableuuid = true 在 VMware 中主要目標 VM 的設定參數中設定。 如果此列不存在，請加以新增。 必須進行此設定，才能提供一致的 UUID 給 VMDK，使其可正確掛接。
8. 檢查 vCenter Server 的存取需求：
    - 如果您要容錯回復的 VM 是在 VMware vCenter Server 所管理的 ESXi 主機上，主要目標伺服器必須能夠存取內部部署 VM 虛擬機器磁片（VMDK）檔案，才能將複寫的資料寫入虛擬機器的磁片。 請確定內部部署 VM 資料存放區已掛接在主要目標主機上，且具有讀取/寫入存取權。
    - 如果 VM 不在 VMware vCenter Server 所管理的 ESXi 主機上，Site Recovery 會在重新保護期間建立新的 VM。 此 VM 建立于您建立主要目標伺服器 VM 的 ESXi 主機上。 小心選擇 ESXi 主機，在主機上建立所需的 VM。 VM 的硬碟必須位於可供主要目標伺服器執行所在主機存取的資料存放區中。
    - 另一個選項 (如果內部部署 VM 已存在以進行容錯回復) 是先將其刪除，然後才執行容錯回復。 接著，容錯回復會在與主要目標 ESXi 主機相同的主機上建立新的 VM。 當您容錯回復至替代位置時，資料會復原到與內部部署主要目標伺服器所使用相同的資料存放區和相同的 ESXi 主機。
9. 對於容錯回復至 VMware Vm 的實體機器，您應該先完成主要目標伺服器執行所在主機的探索，才能重新保護機器。
10. 檢查主要目標 VM 至少有一個連接到它的虛擬機器檔案系統（VMFS）資料存放區的 ESXi 主機。 如果未附加任何 VMFS 資料存放區，重新保護設定中的資料存放區輸入會是空的，而且您無法繼續進行。


## <a name="next-steps"></a>後續步驟

[Reprotect](vmware-azure-reprotect.md)重新保護 VM。
