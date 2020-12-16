---
title: 使用 Azure Site Recovery 啟用 VMware Vm 以進行嚴重損壞修復
description: 本文說明如何使用 Azure Site Recovery 服務，啟用 VMware VM 複寫以進行嚴重損壞修復
author: Rajeswari-Mamilla
ms.service: site-recovery
ms.date: 12/07/2020
ms.topic: conceptual
ms.author: ramamill
ms.openlocfilehash: a1f4759bc40c4074f0dd618be8ac66ad088e848c
ms.sourcegitcommit: d2d1c90ec5218b93abb80b8f3ed49dcf4327f7f4
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/16/2020
ms.locfileid: "97587739"
---
# <a name="enable-replication-to-azure-for-vmware-vms"></a>讓 VMware VM 能夠複寫至 Azure

本文說明如何啟用將內部部署 VMware 虛擬機器 (VM) 複寫至 Azure 的複寫。

## <a name="prerequisites"></a>必要條件

本文假設您的系統符合下列準則：

- [設定您的內部部署來源環境](vmware-azure-set-up-source.md)。
- [在 Azure 中設定您的目標環境](vmware-azure-set-up-target.md)。
- 開始之前，[請先確認需求和必要條件](vmware-physical-azure-support-matrix.md)。 要注意的重要事項包括：
  - 已複寫機器的[支援作業系統](vmware-physical-azure-support-matrix.md#replicated-machines)。
  - [儲存體/磁片](vmware-physical-azure-support-matrix.md#storage) 支援。
  - 內部部署機器應符合的[Azure 需求](vmware-physical-azure-support-matrix.md#azure-vm-requirements)。

### <a name="resolve-common-issues"></a>解決常見的問題

- 每個磁片應小於 4 TB。
- 作業系統磁片應該是基本磁碟，而非動態磁碟。
- 針對第2代已啟用 UEFI 的虛擬機器，作業系統系列應為 Windows，而且開機磁片應小於 300 GB。

## <a name="before-you-start"></a>開始之前

當您複寫 VMware 虛擬機器時，請記住下列資訊：

- 您的 Azure 使用者帳戶必須具有特定[權限](site-recovery-role-based-linked-access-control.md#permissions-required-to-enable-replication-for-new-virtual-machines)，才能將新的虛擬機器複寫至 Azure。
- 系統會每隔 15 分鐘探索 VMware VM 一次。 Vm 可能需要15分鐘以上的時間才會出現在探索後的 Azure 入口網站中。 當您新增 vCenter 伺服器或 vSphere 主機時，探索可能需要15分鐘以上的時間。
- 在入口網站中更新虛擬機器上的環境變更可能需要15分鐘以上的時間。 例如，VMware 工具安裝。
- 您可以查看 VMware Vm 的上次探索時間：請參閱 vCenter server/vSphere 主機之 [設定 **伺服器**] 頁面上的 [**上次連絡人**] 欄位。
- 若要新增複寫的虛擬機器，而不等候排程的探索，請反白顯示設定伺服器 (但不要按一下) ， **然後選取 [** 重新整理]。
- 當您啟用複寫時，如果已準備好虛擬機器，進程伺服器會自動在 VM 上安裝 Azure Site Recovery 行動服務。

## <a name="enable-replication"></a>啟用複寫

在您執行本節中的步驟之前，請先參閱下列資訊：

- Azure Site Recovery 現在會針對所有新的複寫直接複寫到受控磁片。 進程伺服器會將複寫記錄寫入目的地區域中的快取儲存體帳戶。 這些記錄可用來在具有命名慣例的複本受控磁片中建立復原點 `asrseeddisk` 。
- 從[Az. az.recoveryservices module version 2.0.0](https://www.powershellgallery.com/packages/Az.RecoveryServices/2.0.0-preview)開始，可以使用 PowerShell 支援複寫至受控磁片。
- 在容錯移轉時，您選取的復原點會用來建立目標受控磁片。
- 先前設定為複寫到目標儲存體帳戶的 Vm 不會受到影響。
- 只有透過具象狀態傳輸 (REST) API 和 PowerShell 才能使用新虛擬機器的儲存體帳戶複寫。 使用 Azure REST API 2016-08-10 或2018-01-10 版來複寫至儲存體帳戶。

若要啟用複寫，請遵循下列步驟：

1. 移至 **步驟2：複寫應用程式**  >  **來源**。 當您第一次啟用複寫之後，請選取保存庫中的 [ **+** 複寫]，以啟用其他虛擬機器的複寫。
1. 在 [來源] 頁面 > [來源] 中，選取組態伺服器。
1. 針對 [ **電腦類型**]，選取 [ **虛擬機器** ] 或 [ **實體機器**]。
1. 在 [vCenter/vSphere Hypervisor] 中，選取管理 vSphere 主機的 vCenter 伺服器，或選取主機。 如果您要複寫實體電腦，此設定並不相關。
1. 選取處理序伺服器。 如果未建立任何額外的進程伺服器，則會在下拉式功能表中提供設定伺服器的內建進程伺服器。 每個進程伺服器的健康狀態會根據建議的限制和其他參數來表示。 選擇狀況良好的處理序伺服器。 無法選擇 [重要](vmware-physical-azure-monitor-process-server.md#process-server-alerts) 的進程伺服器。 您可以對錯誤進行 [疑難排解及解決問題](vmware-physical-azure-troubleshoot-process-server.md)，**或是** 設定 [相應放大的處理序伺服器](vmware-azure-set-up-process-server-scale.md)。

   :::image type="content" source="./media/vmware-azure-enable-replication/ps-selection.png" alt-text="啟用複寫來源視窗":::

   > [!NOTE]
   > 從 [9.24 版](site-recovery-whats-new.md)開始，會引進其他警示，以增強進程伺服器的健康情況警示。 將 Site Recovery 元件升級為9.24 版或更新版本，以產生所有警示。

1. 針對 [ **目標**]，選取您要在其中建立容錯移轉虛擬機器的訂用帳戶和資源群組。 選擇您想要在 Azure 中針對已容錯移轉的 Vm 使用的部署模型。
1. 選取 azure Vm 在容錯移轉後將連接的 Azure 網路和子網。 網路必須位於與 Site Recovery 服務保存庫相同的區域中。

   選取 [ **立即設定選取的電腦** ]，將網路設定套用至您選取要保護的所有虛擬機器。 選取 [ **稍後設定** ]，以選取每個虛擬機器的 Azure 網路。 如果您沒有網路，則必須建立一個。 若要使用 Azure Resource Manager 建立網路，請選取 [ **建立新** 的]。 選取子網（如果適用的話），然後選取 **[確定]**。

   :::image type="content" source="./media/vmware-azure-enable-replication/enable-rep3.png" alt-text="啟用複寫目標視窗":::

1. 針對 **虛擬機器**  >  ，**選取 [虛擬機器**]，選取您要複寫的每部虛擬機器。 您只能選取可以啟用複寫的虛擬機器。 然後選取 [確定]。 如果您看不到或無法選取任何特定的虛擬機器，請參閱 [Azure 入口網站中未列出來源電腦](vmware-azure-troubleshoot-replication.md#step-3-troubleshoot-source-machines-that-arent-available-for-replication) 以解決此問題。

   :::image type="content" source="./media/vmware-azure-enable-replication/enable-replication5.png" alt-text="啟用複寫選取虛擬機器視窗":::

1. 在 [設定屬性 **] 屬性**  >  中，選取進程伺服器用來自動在 VM 上安裝 Site Recovery 行動服務的帳戶。 此外，請根據您的資料變換模式，選擇要用於複寫的目標受控磁片類型。
1. 根據預設，會複寫來源 VM 的所有磁片。 若要排除磁片不要複寫，請清除任何您不想要複寫之磁片的 [ **包含** ] 核取方塊。 然後選取 [確定]。 您可以稍後再設定其他屬性。 [深入了解](vmware-azure-exclude-disk.md)排除磁碟。

   :::image type="content" source="./media/vmware-azure-enable-replication/enable-replication6.png" alt-text="啟用複寫設定屬性視窗":::

1. 在 [複寫 **設定**] 的 [設定複寫  >  **設定**] 中，確認已選取正確的複寫原則。 您可以在 [**設定**  >  **複製** 策略  >  _原則名稱_  >  **編輯設定**] 中修改複寫原則設定。 套用至原則的變更也適用于複寫和新的虛擬機器。
1. 如果您想要將虛擬機器收集到複寫群組，請啟用 **多 VM 一致性**。 指定群組的名稱，然後選取 **[確定]**。

   > [!NOTE]
   > - 複寫群組中的虛擬機器會一起複寫，並且在容錯移轉時具有共用的損毀一致和應用程式一致復原點。
   > - 將 VM 與實體伺服器一起收集，讓它們鏡像您的工作負載。 啟用多 VM 一致性可能會影響工作負載的效能。 只有當虛擬機器正在執行相同的工作負載，而且您需要一致性時，才執行此動作。

   :::image type="content" source="./media/vmware-azure-enable-replication/enable-replication7.png" alt-text="啟用複寫視窗":::

1. 選取 [啟用複寫]。 您可以在 [**設定** 工作]   >    >  **Site Recovery 工作** 追蹤「啟用保護」工作的進度。 執行「 **完成保護** 」工作之後，虛擬機器就可以進行容錯移轉。

## <a name="monitor-initial-replication"></a>監視初始複寫

受保護專案的「啟用複寫」完成之後，Azure Site Recovery 會起始複寫 (同義于從來源電腦到目的地區域的資料同步處理) 。 在這段期間，會建立來源磁片的複本。 只有在複製原始磁片完成後，才會將差異變更複製到目的地區域。 複製原始磁片所花費的時間取決於多個參數，例如：

- 來源機器磁片的大小
- 可用來將資料傳輸到 Azure 的頻寬 (您可以利用 deployment planner 來找出所需的最佳頻寬) 
- 進程伺服器資源，例如記憶體、可用磁碟空間、快取可用的 CPU & 處理從受保護的專案接收的資料 (確定進程伺服器的 [狀況良好](vmware-physical-azure-monitor-process-server.md#monitor-proactively)) 

若要追蹤初始複寫的進度，請流覽至 [復原服務保存庫] 中 Azure 入口網站 > 複寫的專案-> 監視 [狀態] 資料行值的複寫專案。 狀態會顯示初始複寫完成的百分比。 當滑鼠停留在狀態上時，就可以使用「傳輸的資料總計」。 按一下 [狀態] 時，會開啟內容頁面，並顯示下列參數：

- 上次重新整理時間-表示服務已重新整理整部電腦的複寫資訊最晚時間。
- 已完成的百分比-指出 VM 的初始複寫已完成的百分比
- 傳輸的總數據量-從 VM 傳輸到 Azure 的資料量

:::image type="content" source="media/vmware-azure-enable-replication/initial-replication-state.png" alt-text="複寫狀態" lightbox="media/vmware-azure-enable-replication/initial-replication-state.png":::

- 在磁片層級追蹤詳細資料的同步處理進度 () 
    - 複寫的狀態
      - 如果複寫尚未啟動，則狀態會更新為「在佇列中」。 在初始複寫期間，一次只會複寫3個磁片。 遵循此機制可避免在進程伺服器上進行節流。
      - 複寫開始之後，狀態會更新為「進行中」。
      - 初始複寫完成後，狀態會標示為「完成」。        
   - Site Recovery 讀取原始磁片、將資料傳輸至 Azure，並在磁片層級上捕獲進度。 請注意，Site Recovery 略過磁片未佔用大小的複寫，並將其新增至已完成的資料。 因此，在所有磁片之間傳輸的資料總和可能不會新增至 VM 層級的「傳輸的資料總計」。
   - 當您按一下磁片上的資訊提示時，您可以取得複寫 (對磁片觸發同步處理) 的相關詳細資料、最後15分鐘內傳送至 Azure 的資料，以及上次重新整理的時間戳記。 這個時間戳記表示 Azure 服務從來源機器的初始複寫資訊收到的最晚時間（資訊:::image type="content" source="media/vmware-azure-enable-replication/initial-replication-info-balloon.png" alt-text="-提示-詳細資料" lightbox="media/vmware-azure-enable-replication/initial-replication-info-balloon.png":::）
   - 顯示每個磁片的健全狀況
      - 如果複寫速度低於預期，則磁片狀態會變成警告
      - 如果複寫未進展，磁片狀態會變成「重大」

如果健全狀況處於「重大」/「警告」狀態，請確定機器和 [進程伺服器](vmware-physical-azure-monitor-process-server.md) 的複寫健全狀況狀況良好。 

啟用複寫作業完成後，複寫進度會是0%，而傳輸的總數據則是 NA。 按一下時，針對每個已識別磁片的資料會是 "NA"。這表示複寫尚未啟動，Azure Site Recovery 還在接收最新的統計資料。 進度會以30分鐘的間隔重新整理。

> [!NOTE]
> 請務必將設定伺服器、向外延展進程伺服器和行動代理程式更新為9.36 或更高版本，以確保會將正確的進度捕獲並傳送給 Site Recovery 服務。


## <a name="view-and-manage-vm-properties"></a>檢視及管理 VM 屬性

接下來，請確認來源虛擬機器的屬性。 請記住，Azure VM 名稱應該符合 [Azure 虛擬機器需求](vmware-physical-azure-support-matrix.md#replicated-machines)。

1. 移至 [**設定**  >  **已** 複寫的專案]，然後選取虛擬機器。 [ **基本** 資訊] 頁面會顯示 VM 設定和狀態的相關資訊。
1. 在 [屬性] 中，您可以檢視 VM 的複寫和容錯移轉資訊。
1. 在 [**計算] 和 [網路**  >  **計算] 屬性** 中，您可以變更多個 VM 屬性。

   :::image type="content" source="./media/vmware-azure-enable-replication/vmproperties.png" alt-text="[計算和網路屬性] 視窗":::

   - **AZURE VM 名稱**：如有必要，請修改名稱以符合 Azure 需求。
   - **目標 vm 大小或 VM 類型**：根據在目標 Azure 區域中包含磁片計數、NIC 計數、CPU 核心計數、記憶體和可用 VM 角色大小的參數，選擇預設的 vm 大小。 Azure Site Recovery 會挑選符合所有準則的第一個可用 VM 大小。 您可以在容錯移轉之前的任何時間，根據需求選取不同的 VM 大小。 VM 磁片大小也是根據來源磁片大小，而且只能在容錯移轉後變更。 深入瞭解 [Windows 上 VM 磁片的擴充性和效能目標的](../virtual-machines/windows/disk-scalability-targets.md)磁片大小和 IOPS 速率。
   - **資源群組**：您可以選取一個 [資源群組](../azure-resource-manager/management/overview.md#resource-groups)，讓虛擬機器成為容錯移轉後的一部分。 您可以在容錯移轉之前隨時變更此設定。 在容錯移轉之後，如果您將虛擬機器遷移至不同的資源群組，該虛擬機器的保護設定就會中斷。
   - **可用性設定組**：如果虛擬機器必須是容錯移轉後的一部分，您可以選取 [可用性設定組](../virtual-machines/windows/tutorial-availability-sets.md) 。 當您選取可用性設定組時，請記住下列資訊：
     - 只會列出屬於指定之資源群組的可用性設定組。
     - 位於不同虛擬網路的 Vm 不能屬於相同的可用性設定組。
     - 只有相同大小的虛擬機器可在相同的可用性設定組中。

1. 您也可以新增指派給 Azure VM 的目標網路、子網和 IP 位址的相關資訊。
1. 在 [ **磁片**] 中，您可以看到 VM 上將要複寫的作業系統和資料磁片。

### <a name="configure-networks-and-ip-addresses"></a>設定網路和 IP 位址

您可以設定目標 IP 位址：

- 如果您未提供位址，已容錯移轉的 VM 會使用 DHCP。
- 如果您設定的位址在容錯移轉時無法使用，容錯移轉就無法運作。
- 如果在測試容錯移轉網路中有可用的位址，您可以使用相同的目標 IP 位址進行測試容錯移轉。

網路介面卡的數目取決於您為目標虛擬機器指定的大小，如下所示：

- 如果來源虛擬機器上的網路介面卡數目小於或等於針對目標 VM 大小所允許的介面卡數目，則目標會具有與來源相同的介面卡數目。
- 如果來源虛擬機器的介面卡數目超過目標 VM 大小所允許的數目，則會使用目標大小上限。 例如，如果來源虛擬機器有兩張網路介面卡，而目標 VM 的大小支援四個，則目標虛擬機器有兩張介面卡。 如果來源 VM 有兩張介面卡，但目標大小僅支援一張，則目標 VM 只會有一張介面卡。
- 如果虛擬機器有多張網路介面卡，就會全部連線至相同的網路。 此外，清單中顯示的第一個介面卡會成為 Azure 虛擬機器中的預設網路介面卡。

### <a name="azure-hybrid-benefit"></a>Azure Hybrid Benefit

Microsoft 軟體保證客戶可以使用 Azure Hybrid Benefit 來節省遷移至 Azure 之 Windows Server 電腦的授權成本。 此權益也適用于 Azure 嚴重損壞修復。 如果您符合資格，可以將權益指派給 Site Recovery 在發生容錯移轉時建立的虛擬機器。

1. 移至已複寫虛擬機器的 **電腦和網路** 內容。
1. 當系統詢問您是否有 Windows Server 授權，讓您符合 Azure Hybrid Benefit 資格時，請回答。
1. 確認您擁有具有軟體保證的合格 Windows Server 授權，可讓您用來將其權益套用至將在容錯移轉時建立的 VM。
1. 儲存已複寫虛擬機器的設定。

[深入瞭解](https://azure.microsoft.com/pricing/hybrid-benefit/) Azure Hybrid Benefit。

## <a name="next-steps"></a>後續步驟

虛擬機器達到受保護的狀態之後，請嘗試 [容錯移轉](site-recovery-failover.md) ，以檢查您的應用程式是否出現在 Azure 中。

- [深入瞭解](site-recovery-manage-registration-and-protection.md) 如何清除註冊和保護設定，以停用複寫。
- [深入瞭解](vmware-azure-disaster-recovery-powershell.md) 如何使用 PowerShell 來自動化虛擬機器的複寫。
