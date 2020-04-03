---
title: 使用 Azure 網站恢復啟用 VMware VM 以進行災難復原
description: 本文介紹如何使用 Azure 網站恢復服務啟用 VMware VM 複製以進行災難復原
author: Rajeswari-Mamilla
ms.service: site-recovery
ms.date: 04/01/2020
ms.topic: conceptual
ms.author: ramamill
ms.openlocfilehash: 6547bcf2061213cd01550367171d432900693ea5
ms.sourcegitcommit: 3c318f6c2a46e0d062a725d88cc8eb2d3fa2f96a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/02/2020
ms.locfileid: "80584136"
---
# <a name="enable-replication-to-azure-for-vmware-vms"></a>讓 VMware VM 能夠複寫至 Azure

本文介紹如何將本地 VMware 虛擬機器 (VM) 複製到 Azure。

## <a name="prerequisites"></a>Prerequisites

本文假定您的系統滿足以下條件:

- [設定本地源環境](vmware-azure-set-up-source.md)。
- [在 Azure 中設定目標環境](vmware-azure-set-up-target.md)。
- 在開始之前[驗證要求與先決條件](vmware-physical-azure-support-matrix.md)。 需要注意的重要事項包括:
  - [支援](vmware-physical-azure-support-matrix.md#replicated-machines)複製電腦的作業系統。
  - [存儲/磁碟](vmware-physical-azure-support-matrix.md#storage)支援。
  - 本地電腦應符合的[Azure 要求](vmware-physical-azure-support-matrix.md#azure-vm-requirements)。

### <a name="resolve-common-issues"></a>解決常見問題

- 每個磁碟應小於 4 TB。
- 操作系統磁碟應該是基本磁碟,而不是動態磁碟。
- 對於啟用 UEFI 的第 2 代虛擬機,作業系統系列應為 Windows,引導磁碟應小於 300 GB。

## <a name="before-you-start"></a>開始之前

複製 VMware 虛擬機器時,請記住此資訊:

- 您的 Azure 使用者帳戶必須具有特定[權限](site-recovery-role-based-linked-access-control.md#permissions-required-to-enable-replication-for-new-virtual-machines)，才能將新的虛擬機器複寫至 Azure。
- 系統會每隔 15 分鐘探索 VMware VM 一次。 發現後,VM 可能需要 15 分鐘或更長時間才能顯示在 Azure 門戶中。 添加新 vCenter 伺服器或 vSphere 主機時,發現可能需要 15 分鐘或更長時間。
- 在門戶中更新虛擬機上的環境更改可能需要 15 分鐘或更長時間。 例如,VMware 工具安裝。
- 您可以檢查 VMware VM 的最後發現時間:請參閱**vCenter 伺服器**/vSphere 主機設定伺服器頁面上**的最後一個聯絡人" 在「處」** 欄位。
- 要添加虛擬機進行複製而不等待計畫發現,請突出顯示配置伺服器(但不要單擊它),然後選擇 **「刷新**」。
- 啟用複製時,如果已準備好虛擬機,程序伺服器將自動在 VM 上安裝 Azure 網站恢復行動服務。

## <a name="enable-replication"></a>啟用複寫

在執行此操作之前,請檢視以下資訊:

- Azure 網站恢復現在直接複製到所有新複製的託管磁碟。 進程伺服器將複製日誌寫入目標區域中的快取儲存帳戶。 這些日誌用於在具有 命名約定的副`asrseeddisk`本 託管磁盤中創建恢復點。
- PowerShell 支援複製到託管磁碟,從[Az.RecoveryServices 模組版本 2.0.0](https://www.powershellgallery.com/packages/Az.RecoveryServices/2.0.0-preview)開始提供
- 在故障轉移時,您選擇的復原點用於創建目標託管磁碟。
- 以前配置為複製到目標存儲帳戶的 VM 不受影響。
- 新虛擬機的儲存帳戶複製只能通過表示狀態傳輸 (REST) API 和 PowerShell 獲得。 使用 Azure REST API 版本 2016-08-10 或 2018-01-10 複製到存儲帳戶。

要開啟複製,請按照以下步驟操作:

1. 跳到**步驟 2: 複製應用程式** > **來源**。 首次啟用複製後,在保管庫中選擇 **「複製」,** 以啟用其他虛擬機的複製。
1. 在 [來源]**** 頁面 > [來源]**** 中，選取組態伺服器。
1. 對於**機器類型**,請選擇 **「虛擬機器**」或「**物理電腦**」。
1. 在 [vCenter/vSphere Hypervisor]**** 中，選取管理 vSphere 主機的 vCenter 伺服器，或選取主機。 如果要複製物理計算機,則此設置不相關。
1. 選取處理序伺服器。 如果沒有創建其他進程伺服器,則配置伺服器的內置進程伺服器將在下拉菜單中可用。 每個進程伺服器的運行狀況根據建議的限制和其他參數指示。 選擇狀況良好的處理序伺服器。 無法選擇[關鍵](vmware-physical-azure-monitor-process-server.md#process-server-alerts)進程伺服器。 您可以對錯誤進行[疑難排解及解決問題](vmware-physical-azure-troubleshoot-process-server.md)，**或是**設定[相應放大的處理序伺服器](vmware-azure-set-up-process-server-scale.md)。

   :::image type="content" source="./media/vmware-azure-enable-replication/ps-selection.png" alt-text="開啟複製來源視窗":::

   > [!NOTE]
   > 從[版本9.24](site-recovery-whats-new.md)開始,引入了其他警報,以增強進程伺服器的運行狀況警報。 將網站恢復元件升級到版本 9.24 或以上,以便生成所有警報。

1. 對於**Target**,選擇要在虛擬機器上建立失敗的訂閱和資源群組。 選擇要在 Azure 中用於故障的 VM 的部署模型。
1. 選擇 Azure VM 將在故障轉移後連接到的 Azure 網路和子網。 網路必須與網站恢復服務保管庫位於同一區域。

   選擇「**立即設定」,以便選擇**所有虛擬機器進行保護。 **選擇「稍後設定**」以選擇每個虛擬機器的 Azure 網路。 如果您沒有網路，則必須建立一個。 要使用 Azure 資源管理員建立網路,請選擇「**創建新**」 。 如果適用,請選擇子網,然後選擇 **"確定**"。

   :::image type="content" source="./media/vmware-azure-enable-replication/enable-rep3.png" alt-text="開啟複製目標視窗":::

1. 對於**虛擬機器** > **選擇虛擬機器**,請選擇要複製的每個虛擬機器。 您只能選擇要為其啟用複製的虛擬機器。 然後選取 [確定]  。 如果看不到或選擇任何特定虛擬機,請參閱[Azure 門戶中未列出源電腦](vmware-azure-troubleshoot-replication.md#step-3-troubleshoot-source-machines-that-arent-available-for-replication)以解決此問題。

   :::image type="content" source="./media/vmware-azure-enable-replication/enable-replication5.png" alt-text="開啟複製 選擇虛擬機器視窗":::

1. 對於**屬性** > **配置屬性**,選擇進程伺服器用於在 VM 上自動安裝網站恢復行動服務的帳戶。 此外,根據數據改動模式選擇要用於複製的目標託管磁碟的類型。
1. 默認情況下,將複製源 VM 的所有磁碟。 要從複製中排除磁碟,請清除不想複製的任何磁碟的 **「包括**」複選框。 然後選取 [確定]  。 您可以稍後再設定其他屬性。 [深入了解](vmware-azure-exclude-disk.md)排除磁碟。

   :::image type="content" source="./media/vmware-azure-enable-replication/enable-replication6.png" alt-text="開啟複製設定屬性視窗":::

1. 從**複製設定** > **配置複製設置**,驗證是否選擇了正確的複製策略。 您可以在 **「設定** > **複製策略** > 」策略_名稱_ > **「編輯設定**」中修改複製策略設定。 應用於策略的更改也適用於複製和新的虛擬機器。
1. 如果要將虛擬機器收集到複製群組中,則啟用多 VM**一致性**。 指定群組的名稱,然後選擇 **"確定**"。

   > [!NOTE]
   > - 複製組中的虛擬機一起複製,並在故障轉移時共用崩潰一致性和應用一致的恢復點。
   > - 將 VM 與實體伺服器一起收集，讓它們鏡像您的工作負載。 啟用多 VM 一致性可能會影響工作負載的效能。 僅當虛擬機運行相同的工作負載且需要一致性時,才執行此操作。

   :::image type="content" source="./media/vmware-azure-enable-replication/enable-replication7.png" alt-text="開啟複製視窗":::

1. 選擇**複製**。 您可以在 **「設定** > **作業** > **網站恢復作業**」中追蹤**啟用保護**作業的進度。 **完成保護**作業運行後,虛擬機已準備好進行故障轉移。

## <a name="view-and-manage-vm-properties"></a>檢視及管理 VM 屬性

接下來,驗證源虛擬機器的屬性。 請記住，Azure VM 名稱應該符合 [Azure 虛擬機器需求](vmware-physical-azure-support-matrix.md#replicated-machines)。

1. 轉到 **"設置** > **複製專案**",然後選擇虛擬機器。 基本 **「** 頁顯示有關 VM」的設定和狀態的資訊。
1. 在 [屬性]**** 中，您可以檢視 VM 的複寫和容錯移轉資訊。
1. 在**計算和網路** > **計算屬性**中,可以更改多個 VM 屬性。

   :::image type="content" source="./media/vmware-azure-enable-replication/vmproperties.png" alt-text="計算與網路屬性視窗":::

   - **Azure VM 名稱**:如有必要,修改名稱以滿足 Azure 要求。
   - **目標 VM 大小或 VM 類型**:預設 VM 大小是根據目標 Azure 區域中的磁碟計數、NIC 計數、CPU 核心計數、記憶體和可用 VM 角色大小等參數選擇的。 Azure 網站恢復選擇滿足所有條件的第一個可用 VM 大小。 您可以在故障轉移前的任何時間根據您的需求選擇不同的 VM 大小。 VM 磁碟大小也基於源磁碟大小,並且只能在故障轉移後更改。 詳細瞭解[Windows 上 VM 磁碟的可伸縮性和性能目標處的](/azure/virtual-machines/windows/disk-scalability-targets)磁碟大小和 IOPS 速率。
   - **資源組**:您可以選擇[一個資源組](/azure/azure-resource-manager/management/overview#resource-groups),從該組中,虛擬機將成為故障轉移后的一部分。 您可以在故障轉移前的任何時間更改此設置。 故障轉移后,如果將虛擬機器遷移到其他資源組,該虛擬機的保護設置將中斷。
   - **可用性集**:如果您的虛擬機器需要是故障轉移後的一部分,則可以選擇[可用性集](/azure/virtual-machines/windows/tutorial-availability-sets)。 選擇可用性集時,請記住以下資訊:
     - 僅列出屬於指定資源組的可用性集。
     - 不同虛擬網路上的 VM 不能是同一可用性集的一部分。
     - 只有相同大小的虛擬機器可在相同的可用性設定組中。

1. 您還可以添加有關分配給 Azure VM 的目標網路、子網和 IP 位址的資訊。
1. 在**磁碟中**,您可以看到要複製的 VM 上的作業系統和資料磁碟。

### <a name="configure-networks-and-ip-addresses"></a>設定網路和 IP 位址

您可以設定目標 IP 位址:

- 如果不提供位址,則 VM 故障使用 DHCP。
- 如果您設定的位址在容錯移轉時無法使用，容錯移轉就無法運作。
- 如果位址在測試故障轉移網路中可用,則可以使用相同的目標 IP 位址進行測試故障轉移。

網路配接器的數量取決於您為目標虛擬機指定的大小,如下所示:

- 如果源虛擬機上的網路適配器數小於或等於目標 VM 大小允許的適配器數,則目標具有與源相同的適配器數。
- 如果源虛擬機的適配器數超過目標 VM 大小允許的數量,則使用目標大小最大值。 例如,如果源虛擬機具有兩個網路適配器,並且目標 VM 的大小支援四個,則目標虛擬機有兩個適配器。 如果源 VM 有兩個適配器,但目標大小僅支援一個適配器,則目標 VM 只有一個適配器。
- 如果虛擬機器有多張網路介面卡，就會全部連線至相同的網路。 此外,清單中顯示的第一個適配器將成為 Azure 虛擬機中的默認網路適配器。

### <a name="azure-hybrid-benefit"></a>Azure Hybrid Benefit

Microsoft 軟體保障客戶可以使用 Azure 混合權益來節省遷移到 Azure 的 Windows 伺服器電腦的許可成本。 該權益也適用於 Azure 災難恢復。 如果您符合條件,則可以將權益分配給網站恢復創建(如果存在故障轉移)創建的虛擬機。

1. 跳到複製虛擬機器的電腦**與網路屬性**。
1. 當被問及您是否擁有 Windows Server 許可證,使您有資格享受 Azure 混合權益時,請回答。
1. 確認您具有符合軟體保障的合格 Windows Server 許可證,您可以使用該許可證將權益應用於將在故障轉移時創建的 VM。
1. 保存複製虛擬機的設置。

[詳細瞭解](https://azure.microsoft.com/pricing/hybrid-benefit/)Azure 混合權益。

## <a name="next-steps"></a>後續步驟

虛擬機達到受保護狀態後,嘗試[故障轉移](site-recovery-failover.md)以檢查應用程式是否顯示在 Azure 中。

- [詳細瞭解如何](site-recovery-manage-registration-and-protection.md)清理註冊和保護設置以禁用複製。
- [詳細瞭解](vmware-azure-disaster-recovery-powershell.md)如何使用 PowerShell 自動複製虛擬機器。
