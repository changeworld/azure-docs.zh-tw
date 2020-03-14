---
title: 使用 Azure Site Recovery 啟用 VMware Vm 以進行嚴重損壞修復
description: 本文說明如何使用 Azure Site Recovery 服務，啟用 VMware VM 複寫以進行嚴重損壞修復
author: Rajeswari-Mamilla
ms.service: site-recovery
ms.date: 06/28/2019
ms.topic: conceptual
ms.author: ramamill
ms.openlocfilehash: 10b3e572ec61d1eff342f24a6a5a7bcba6276983
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/13/2020
ms.locfileid: "79257312"
---
# <a name="enable-replication-to-azure-for-vmware-vms"></a>讓 VMware VM 能夠複寫至 Azure

本文說明如何啟用將內部部署 VMware VM 複寫至 Azure 的作業。

## <a name="resolve-common-issues"></a>解決常見的問題

* 每個磁片都應該小於 4 TB。
* OS 磁片應該是基本磁碟，而非動態磁碟。
* 針對第2代/已啟用 UEFI 的虛擬機器，作業系統系列應為 Windows，而且開機磁片應小於 300 GB。

## <a name="prerequisites"></a>Prerequisites

本文假設您已經：

- [設定您的內部部署來源環境](vmware-azure-set-up-source.md)。
- [在 Azure 中設定您的目標環境](vmware-azure-set-up-target.md)。
- 開始之前，[請先確認需求和必要條件](vmware-physical-azure-support-matrix.md)。 要注意的重要事項包括：
    - 複寫電腦[支援的作業系統](vmware-physical-azure-support-matrix.md#replicated-machines)。
    - [儲存體/磁片](vmware-physical-azure-support-matrix.md#storage)支援。
    - 內部部署機器應該符合的[Azure 需求](vmware-physical-azure-support-matrix.md#azure-vm-requirements)。


## <a name="before-you-start"></a>開始之前
當您要複寫 VMware 虛擬機器時，請記住這項資訊：

* 您的 Azure 使用者帳戶必須具有特定[權限](site-recovery-role-based-linked-access-control.md#permissions-required-to-enable-replication-for-new-virtual-machines)，才能將新的虛擬機器複寫至 Azure。
* 系統會每隔 15 分鐘探索 VMware VM 一次。 在探索之後，Vm 可能需要15分鐘或更久的時間才會出現在 Azure 入口網站中。 同樣地，當您新增 vCenter 伺服器或 vSphere 主機時，探索可能需要15分鐘或更久的時間。
* 可能需要15分鐘或更久的時間，才能在入口網站中更新虛擬機器上的環境變更（例如 VMware 工具安裝）。
* 您可以查看 VMware Vm 的上次探索時間：請參閱 vCenter server/vSphere 主機的 [設定**伺服器**] 頁面上的 [**上次連絡人位置**] 欄位。
* 若要新增複寫的虛擬機器，而不等候排程的探索，請反白顯示設定伺服器（但不要按一下），**然後選取 [** 重新整理]。
* 當您啟用複寫時，如果虛擬機器已備妥，進程伺服器會自動在其上安裝 Azure Site Recovery 行動服務。

## <a name="enable-replication"></a>啟用複寫

在您遵循本節中的步驟之前，請注意下列資訊：
* Azure Site Recovery 現在會將所有新的複寫直接複寫到受控磁片。 進程伺服器會將複寫記錄寫入目的地區域中的快取儲存體帳戶。 這些記錄是用來在具有 asrseeddisk 命名慣例的複本受控磁片中建立復原點。
* 從[Az. azurerm.recoveryservices 模組版本 2.0.0](https://www.powershellgallery.com/packages/Az.RecoveryServices/2.0.0-preview)提供複寫至受控磁片的 Powershell 支援。 
* 在容錯移轉時，您選取的復原點會用來建立目標受控磁片。
* 先前設定以複寫至目標儲存體帳戶的 Vm 不會受到影響。
* 只有透過具像狀態傳輸（REST） API 和 Powershell，才可對新虛擬機器的儲存體帳戶進行複寫。 使用 Azure REST API 2016-08-10 或2018-01-10 版來複寫至儲存體帳戶。

請遵循下列步驟來啟用複寫：
1. 移至**步驟2：複寫應用程式** > **來源**。 第一次啟用複寫之後，請選取保存庫中的 [ **+** 複寫]，以啟用其他虛擬機器的複寫。
2. 在 [來源] 頁面 > [來源] 中，選取組態伺服器。
3. 針對 [**電腦類型**]，選取 [**虛擬機器**] 或 [**實體機器**]。
4. 在 [vCenter/vSphere Hypervisor] 中，選取管理 vSphere 主機的 vCenter 伺服器，或選取主機。 如果您要複寫實體電腦，此設定並不相關。
5. 選取處理序伺服器。 如果沒有建立任何額外的進程伺服器，則會在下拉式清單中提供設定伺服器的內建進程伺服器。 每個處理序伺服器的健康狀態都會根據建議限制和其他參數來指示。 選擇狀況良好的處理序伺服器。 無法選擇狀態為[嚴重](vmware-physical-azure-monitor-process-server.md#process-server-alerts)的處理序伺服器。 您可以對錯誤進行[疑難排解及解決問題](vmware-physical-azure-troubleshoot-process-server.md)，**或是**設定[相應放大的處理序伺服器](vmware-azure-set-up-process-server-scale.md)。
    ![啟用複寫來源視窗](media/vmware-azure-enable-replication/ps-selection.png)

> [!NOTE]
> 從[9.24 版本](service-updates-how-to.md#links-to-currently-supported-update-rollups)中引進了額外的警示，以增強進程伺服器的健康情況警示。 將 Site Recovery 元件升級至9.24 版或更新版本，以產生所有警示。

6. 針對 [**目標**]，選取您要在其中建立故障後虛擬機器的訂用帳戶和資源群組。 選擇您想要在 Azure 中用來進行故障 over Vm 的部署模型。
2. 選取 azure Vm 在容錯移轉之後將連接的 Azure 網路和子網。 網路必須位於與 Site Recovery 服務保存庫相同的區域中。

   選取 [**立即設定選取的機器**]，將網路設定套用至您選取要保護的所有虛擬機器。 選取 [**稍後設定**] 以選取每個虛擬機器的 Azure 網路。 如果您沒有網路，則必須建立一個。 若要使用 Azure Resource Manager 建立網路，請選取 [**新建**]。 選取子網（如果適用），然後選取 **[確定]** 。
   
   ![啟用複寫目標視窗](./media/vmware-azure-enable-replication/enable-rep3.png)

1. 針對 [**虛擬**機] > **選取 [虛擬機器**]，選取您要複寫的每部虛擬機器。 您只能選取可啟用複寫的虛擬機器。 然後選取 [確定]。 如果您看不到或未選取任何特定的虛擬機器，請參閱[Azure 入口網站不會列出來源電腦](https://aka.ms/doc-plugin-VM-not-showing)來解決問題。

    ![啟用複寫選取虛擬機器視窗](./media/vmware-azure-enable-replication/enable-replication5.png)

1. 針對 **屬性** > **設定屬性**，選取進程伺服器用來在虛擬機器上自動安裝 Site Recovery 行動服務的帳戶。 此外，請根據您的資料變換模式，選擇要複寫的目標受控磁片類型。
10. 根據預設，系統會複寫來源虛擬機器的所有磁片。 若要排除磁片不進行複寫，請清除您不想要複寫之任何磁片的 [**包含**] 核取方塊。 然後選取 [確定]。 您可以稍後再設定其他屬性。 深入瞭解如何[排除磁片](vmware-azure-exclude-disk.md)。

    ![啟用複寫設定屬性視窗](./media/vmware-azure-enable-replication/enable-replication6.png)

1. 在 複寫**設定** > **設定複寫設定**，確認已選取正確的複寫原則。 您可以在 > **複製**策略 > ***原則名稱*** > **編輯設定** 中修改複製**策略設定。** 套用至原則的變更也適用于複寫和新的虛擬機器。
1. 如果您想要將虛擬機器收集到複寫群組，請啟用**多部 VM 一致性**。 指定群組的名稱，然後選取 **[確定]** 。

    > [!NOTE]
    >    * 複寫群組中的虛擬機器會一起複寫，並在故障時擁有共用的損毀一致和應用程式一致的復原點。
    >    * 將 VM 與實體伺服器一起收集，讓它們鏡像您的工作負載。 啟用多 VM 一致性可能會影響工作負載的效能。 只有當虛擬機器正在執行相同的工作負載，且您需要一致性時，才這麼做。

    ![啟用複寫視窗](./media/vmware-azure-enable-replication/enable-replication7.png)
    
1. 選取 [啟用複寫]。 您可以在 **設定** > **作業** > **Site Recovery 作業** 追蹤 **啟用保護** 作業的進度。 執行「完成保護」作業之後，虛擬機器即準備好進行容錯移轉。

## <a name="view-and-manage-vm-properties"></a>檢視及管理 VM 屬性

接下來，請確認來源虛擬機器的屬性。 請記住，Azure VM 名稱應該符合 [Azure 虛擬機器需求](vmware-physical-azure-support-matrix.md#replicated-machines)。

1. 移至 [**設定**] > [複寫的**專案**]，然後選取虛擬機器。 [**基本**資料] 頁面會顯示 VM 設定和狀態的相關資訊。
1. 在 [屬性] 中，您可以檢視 VM 的複寫和容錯移轉資訊。
1. 在 [**計算和網路**] > [**計算屬性**] 中，您可以變更多個 VM 屬性。 

    ![[計算和網路屬性] 視窗](./media/vmware-azure-enable-replication/vmproperties.png)

    * Azure VM 名稱：視需要修改名稱以符合 Azure 需求。
    * 目標 VM 大小或 VM 類型：根據目標 Azure 區域中的磁片計數、NIC 計數、CPU 核心計數、記憶體和可用 VM 角色大小等幾個參數，選擇預設 VM 大小。 Azure Site Recovery 挑選符合所有準則的第一個可用 VM 大小。 您可以在容錯移轉之前隨時根據您的需求選取不同的 VM 大小。 請注意，VM 磁片大小也是根據來源磁片大小，而且只能在容錯移轉後變更。 深入瞭解[Windows 上 VM 磁片的擴充性和效能目標](../virtual-machines/windows/disk-scalability-targets.md)中的磁片大小和 IOPS 速率。

    *  資源群組：您可以選取一個[資源群組](https://docs.microsoft.com/azure/virtual-machines/windows/infrastructure-resource-groups-guidelines)，其中虛擬機器會成為後置容錯移轉的一部分。 您可以在容錯移轉之前隨時變更此設定。 容錯移轉之後，如果您將虛擬機器遷移至不同的資源群組，該虛擬機器的保護設定就會中斷。
    * 可用性設定組：如果您的虛擬機器必須是容錯移轉後的一部分，您可以選取[可用性設定組](https://docs.microsoft.com/azure/virtual-machines/windows/infrastructure-availability-sets-guidelines)。 當您選取可用性設定組時，請記住下列資訊：

        * 只會列出屬於指定之資源群組的可用性設定組。  
        * 位於不同虛擬網路上的 Vm 不能屬於相同的可用性設定組。
        * 只有相同大小的虛擬機器可在相同的可用性設定組中。
1. 您也可以新增已指派給 Azure VM 的目標網路、子網和 IP 位址的相關資訊。
2. 在 [磁碟] 中，您可以看見 VM 上將要複寫的作業系統和資料磁碟。

### <a name="configure-networks-and-ip-addresses"></a>設定網路和 IP 位址

您可以設定目標 IP 位址。 如果您未提供位址，則故障的虛擬機器會使用 DHCP。 如果您設定的位址在容錯移轉時無法使用，容錯移轉就無法運作。 如果在測試容錯移轉網路中有可用的位址，您可以針對測試容錯移轉使用相同的目標 IP 位址。

網路介面卡的數目取決於您為目標虛擬機器指定的大小，如下所示：

- 如果來源虛擬機器上的網路介面卡數目小於或等於針對目標 VM 大小所允許的介面卡數目，則目標會有與來源相同的介面卡數目。
- 如果來源虛擬機器的介面卡數目超過目標 VM 大小所允許的數目，則會使用目標大小的最大值。 例如，如果來源虛擬機器有兩張網路介面卡，而目標 VM 的大小支援四個，則目標虛擬機器有兩個介面卡。 如果來源 VM 有兩張介面卡，但目標大小僅支援一張，則目標 VM 只有一個介面卡。
- 如果虛擬機器有多張網路介面卡，就會全部連線至相同的網路。 此外，清單中顯示的第一個介面卡會變成 Azure 虛擬機器中的*預設*網路介面卡。 

### <a name="azure-hybrid-benefit"></a>Azure Hybrid Benefit

Microsoft 軟體保證客戶可以使用 Azure Hybrid Benefit 來節省遷移至 Azure 之 Windows Server 電腦的授權成本。 此權益也適用于 Azure 嚴重損壞修復。 如果您符合資格，可以將權益指派給 Site Recovery 在容錯移轉時建立的虛擬機器。 若要這樣做，請遵循下列步驟：
1. 移至複寫虛擬機器的 [**電腦] 和 [網路] 屬性**。
2. 當系統詢問您是否有可讓您 Azure Hybrid Benefit 的 Windows Server 授權時，回答。
3. 請確認您擁有具有軟體保證的合格 Windows Server 授權，可用於將其權益套用至將在容錯移轉時建立的 VM。
4. 儲存複寫虛擬機器的設定。

深入了解 [Azure Hybrid Benefit](https://aka.ms/azure-hybrid-benefit-pricing)。



## <a name="next-steps"></a>後續步驟

虛擬機器達到受保護的狀態之後，請嘗試[容錯移轉](site-recovery-failover.md)，以檢查您的應用程式是否出現在 Azure 中。

* 了解如何[清除註冊與保護設定](site-recovery-manage-registration-and-protection.md)，以停用複寫。
* 瞭解如何[使用 Powershell 自動進行虛擬機器的](vmware-azure-disaster-recovery-powershell.md)複寫。
