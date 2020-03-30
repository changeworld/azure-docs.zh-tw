---
title: 使用 Azure 網站恢復啟用 VMware VM 以進行災害復原
description: 本文介紹如何使用 Azure 網站恢復服務啟用 VMware VM 複製以進行災害復原
author: Rajeswari-Mamilla
ms.service: site-recovery
ms.date: 06/28/2019
ms.topic: conceptual
ms.author: ramamill
ms.openlocfilehash: 10b3e572ec61d1eff342f24a6a5a7bcba6276983
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79257312"
---
# <a name="enable-replication-to-azure-for-vmware-vms"></a>讓 VMware VM 能夠複寫至 Azure

本文說明如何啟用將內部部署 VMware VM 複寫至 Azure 的作業。

## <a name="resolve-common-issues"></a>解決常見問題

* 每個磁片應小於 4 TB。
* OS 磁片應該是基本磁碟，而不是動態磁碟。
* 對於啟用第 2 代/UEFI 的虛擬機器，作業系統系列應為 Windows，並且引導磁片應小於 300 GB。

## <a name="prerequisites"></a>Prerequisites

本文假設您已經：

- [設置本地源環境](vmware-azure-set-up-source.md)。
- [在 Azure 中設置目標環境](vmware-azure-set-up-target.md)。
- 在開始之前[驗證要求和先決條件](vmware-physical-azure-support-matrix.md)。 需要注意的重要事項包括：
    - [支援](vmware-physical-azure-support-matrix.md#replicated-machines)複製電腦的作業系統。
    - [存儲/磁片](vmware-physical-azure-support-matrix.md#storage)支援。
    - 本地電腦應符合的[Azure 要求](vmware-physical-azure-support-matrix.md#azure-vm-requirements)。


## <a name="before-you-start"></a>開始之前
複製 VMware 虛擬機器時，請記住此資訊：

* 您的 Azure 使用者帳戶必須具有特定[權限](site-recovery-role-based-linked-access-control.md#permissions-required-to-enable-replication-for-new-virtual-machines)，才能將新的虛擬機器複寫至 Azure。
* 系統會每隔 15 分鐘探索 VMware VM 一次。 發現後，VM 可能需要 15 分鐘或更長時間才能顯示在 Azure 門戶中。 同樣，當您添加新的 vCenter 伺服器或 vSphere 主機時，發現可能需要 15 分鐘或更長時間。
* 在門戶中更新虛擬機器上的環境更改（如 VMware 工具安裝）可能需要 15 分鐘或更長時間。
* 您可以檢查 VMware VM 的最後發現時間：請參閱**vCenter 伺服器**/vSphere 主機佈建服務器頁面上**的最後一個連絡人"在"處"** 欄位。
* 要添加虛擬機器進行複製而不等待計畫發現，請突出顯示佈建服務器（但不要按一下它），然後選擇 **"刷新**"。
* 啟用複製時，如果已準備好虛擬機器，進程伺服器會自動在它上安裝 Azure 網站恢復移動服務。

## <a name="enable-replication"></a>啟用複寫

在按照本節中的步驟操作之前，請注意以下資訊：
* Azure 網站恢復現在直接複製到所有新複製的託管磁片。 進程伺服器將複製日誌寫入目的地區域中的緩存存儲帳戶。 這些日誌用於在具有 asrseeddisk 命名約定的副本託管磁片中創建復原點。
* [從 Az.RecoveryServices 模組版本 2.0.0 開始](https://www.powershellgallery.com/packages/Az.RecoveryServices/2.0.0-preview)，提供複製到託管磁片的電源外殼支援 
* 在容錯移轉時，您選擇的復原點用於創建目標託管磁片。
* 以前配置為複製到目標存儲帳戶的 VM 不受影響。
* 新虛擬機器的存儲帳戶複製只能通過表示狀態傳輸 （REST） API 和 Powershell 提供。 使用 Azure REST API 版本 2016-08-10 或 2018-01-10 複製到存儲帳戶。

請按照以下步驟啟用複製：
1. 轉到**步驟 2：複製應用程式** > **源**。 首次啟用複製後，在保存庫中選擇 **"複製"，** 以啟用其他虛擬機器的複製。
2. 在 [來源]**** 頁面 > [來源]**** 中，選取組態伺服器。
3. 對於**機器類型**，請選擇 **"虛擬機器**"或"**物理電腦**"。
4. 在 [vCenter/vSphere Hypervisor]**** 中，選取管理 vSphere 主機的 vCenter 伺服器，或選取主機。 如果要複製物理電腦，則此設置不相關。
5. 選取處理序伺服器。 如果沒有創建其他進程伺服器，則佈建服務器的內置進程伺服器將在下拉清單中可用。 每個處理序伺服器的健康狀態都會根據建議限制和其他參數來指示。 選擇狀況良好的處理序伺服器。 無法選擇狀態為[嚴重](vmware-physical-azure-monitor-process-server.md#process-server-alerts)的處理序伺服器。 您可以對錯誤進行[疑難排解及解決問題](vmware-physical-azure-troubleshoot-process-server.md)，**或是**設定[相應放大的處理序伺服器](vmware-azure-set-up-process-server-scale.md)。
    ![啟用複製源視窗](media/vmware-azure-enable-replication/ps-selection.png)

> [!NOTE]
> 從[9.24 版本](service-updates-how-to.md#links-to-currently-supported-update-rollups)開始，引入了其他警報，以增強進程伺服器的運行狀況警報。 將網站恢復元件升級到 9.24 版本或以上，以便生成所有警報。

6. 對於**Target**，選擇要創建故障過期虛擬機器的訂閱和資源組。 選擇要在 Azure 中用於故障轉換 VM 的部署模型。
2. 選擇 Azure VM 將在容錯移轉後連接到的 Azure 網路和子網。 網路必須與網站恢復服務保存庫位於同一區域。

   選擇"**立即配置"，以便所選電腦**將網路設置應用於您選擇的所有虛擬機器進行保護。 **選擇"稍後配置**"以選擇每個虛擬機器的 Azure 網路。 如果您沒有網路，則必須建立一個。 要使用 Azure 資源管理器創建網路，請選擇"**創建新**"。 如果適用，請選擇子網，然後選擇 **"確定**"。
   
   ![啟用複製目標視窗](./media/vmware-azure-enable-replication/enable-rep3.png)

1. 對於**虛擬機器** > **選擇虛擬機器**，請選擇要複製的每個虛擬機器。 您只能選擇要為其啟用複製的虛擬機器。 然後選擇 **"確定**"。 如果看不到或選擇任何特定虛擬機器，請參閱[Azure 門戶中未列出源電腦](https://aka.ms/doc-plugin-VM-not-showing)以解決此問題。

    ![啟用複製 選擇虛擬機器視窗](./media/vmware-azure-enable-replication/enable-replication5.png)

1. 對於**屬性** > **配置屬性**，選擇進程伺服器用於在虛擬機器上自動安裝網站恢復移動服務的帳戶。 此外，根據資料改動模式選擇要複製到的目標託管磁片的類型。
10. 預設情況下，將複製源虛擬機器的所有磁片。 要從複製中排除磁片，請清除不想複製的任何磁片的 **"包括**"核取方塊。 然後選擇 **"確定**"。 您可以稍後再設定其他屬性。 瞭解有關[排除磁片 的更多詳細資訊](vmware-azure-exclude-disk.md)。

    ![啟用複製配置屬性視窗](./media/vmware-azure-enable-replication/enable-replication6.png)

1. 在**複製設置** > **配置複製設置**時，驗證是否選擇了正確的複寫原則。 您可以在 **"設置** > **複寫原則** > "策略***名稱*** > **"編輯設置**"中修改複寫原則設置。 應用於策略的更改也適用于複製和新的虛擬機器。
1. 如果要將虛擬機器收集到複製組中，則啟用**多 VM 一致性**。 指定組的名稱，然後選擇 **"確定**"。

    > [!NOTE]
    >    * 複製組中的虛擬機器一起複製，並在容錯移轉時共用崩潰一致性和應用一致的復原點。
    >    * 將 VM 與實體伺服器一起收集，讓它們鏡像您的工作負載。 啟用多 VM 一致性可能會影響工作負載的效能。 僅當虛擬機器運行相同的工作負載且需要一致性時，才執行此操作。

    ![啟用複製視窗](./media/vmware-azure-enable-replication/enable-replication7.png)
    
1. 選擇**啟用複製**。 您可以在 **"設置** > **作業** > **網站恢復作業**"中跟蹤**啟用保護**作業的進度。 **完成保護**作業運行後，虛擬機器已準備好進行容錯移轉。

## <a name="view-and-manage-vm-properties"></a>檢視及管理 VM 屬性

接下來，驗證源虛擬機器的屬性。 請記住，Azure VM 名稱應該符合 [Azure 虛擬機器需求](vmware-physical-azure-support-matrix.md#replicated-machines)。

1. 轉到 **"設置** > **複製專案**"，然後選擇虛擬機器。 "**基本"** 頁顯示有關 VM 的設置和狀態的資訊。
1. 在 [屬性]**** 中，您可以檢視 VM 的複寫和容錯移轉資訊。
1. 在**計算和網路** > **計算屬性**中，可以更改多個 VM 屬性。 

    ![計算和網路屬性視窗](./media/vmware-azure-enable-replication/vmproperties.png)

    * Azure VM 名稱：如有必要，修改名稱以滿足 Azure 要求。
    * 目標 VM 大小或 VM 類型：預設 VM 大小是根據幾個參數選擇的，這些參數包括目標 Azure 區域中的磁片計數、NIC 計數、CPU 核心計數、記憶體和可用 VM 角色大小。 Azure 網站恢復選擇滿足所有條件的第一個可用 VM 大小。 您可以在容錯移轉前的任何時間根據您的需求選擇不同的 VM 大小。 請注意，VM 磁片大小也基於源磁片大小，並且只能在容錯移轉後更改。 詳細瞭解[Windows 上 VM 磁片的可伸縮性和性能目標處的](../virtual-machines/windows/disk-scalability-targets.md)磁片大小和 IOPS 速率。

    *  資源組：您可以選擇[一個資源組](https://docs.microsoft.com/azure/virtual-machines/windows/infrastructure-resource-groups-guidelines)，從該組中，虛擬機器將成為容錯移轉後的一部分。 您可以在容錯移轉前的任何時間更改此設置。 容錯移轉後，如果將虛擬機器遷移到其他資源組，該虛擬機器的保護設置將中斷。
    * 可用性集：如果您的虛擬機器需要是容錯移轉後的一部分，則可以選擇[可用性集](https://docs.microsoft.com/azure/virtual-machines/windows/infrastructure-availability-sets-guidelines)。 選擇可用性集時，請記住以下資訊：

        * 僅列出屬於指定資源組的可用性集。  
        * 不同虛擬網路上的 VM 不能是同一可用性集的一部分。
        * 只有相同大小的虛擬機器可在相同的可用性設定組中。
1. 您還可以添加有關分配給 Azure VM 的目標網路、子網和 IP 位址的資訊。
2. 在**磁片中**，您可以看到要複製的 VM 上的作業系統和資料磁片。

### <a name="configure-networks-and-ip-addresses"></a>設定網路和 IP 位址

您可以設定目標 IP 位址。 如果不提供位址，故障處理虛擬機器將使用 DHCP。 如果您設定的位址在容錯移轉時無法使用，容錯移轉就無法運作。 如果位址在測試容錯移轉網路中可用，則可以使用相同的目標 IP 位址進行測試容錯移轉。

網路介面卡的數量取決於您為目標虛擬機器指定的大小，如下所示：

- 如果源虛擬機器上的網路介面卡數小於或等於目標 VM 大小允許的配接器數，則目標具有與源相同的配接器數。
- 如果源虛擬機器的配接器數超過目標 VM 大小允許的數量，則使用目標大小最大值。 例如，如果源虛擬機器具有兩個網路介面卡，並且目標 VM 的大小支援四個，則目標虛擬機器有兩個配接器。 如果源 VM 有兩個配接器，但目標大小僅支援一個配接器，則目標 VM 只有一個配接器。
- 如果虛擬機器有多張網路介面卡，就會全部連線至相同的網路。 此外，清單中顯示的第一個配接器將成為 Azure 虛擬機器中的*預設*網路介面卡。 

### <a name="azure-hybrid-benefit"></a>Azure Hybrid Benefit

Microsoft 軟體保證客戶可以使用 Azure 混合權益來節省遷移到 Azure 的 Windows 伺服器電腦的許可成本。 該權益也適用于 Azure 災害復原。 如果您符合條件，則可以將權益分配給網站恢復創建（如果存在容錯移轉）創建的虛擬機器。 若要這樣做，請遵循下列步驟：
1. 轉到複製虛擬機器的電腦**和網路屬性**。
2. 當被問及您是否擁有 Windows Server 許可證，使您有資格享受 Azure 混合權益時，請回答。
3. 確認您具有符合軟體保證的合格 Windows Server 許可證，您可以使用該許可證將權益應用於將在容錯移轉時創建的 VM。
4. 保存複製虛擬機器的設置。

深入了解 [Azure Hybrid Benefit](https://aka.ms/azure-hybrid-benefit-pricing)。



## <a name="next-steps"></a>後續步驟

虛擬機器達到受保護狀態後，嘗試[容錯移轉](site-recovery-failover.md)以檢查應用程式是否顯示在 Azure 中。

* 了解如何[清除註冊與保護設定](site-recovery-manage-registration-and-protection.md)，以停用複寫。
* 瞭解如何使用[Powershell 自動複製虛擬機器](vmware-azure-disaster-recovery-powershell.md)。
