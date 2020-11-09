---
title: 使用 Azure Site Recovery 的 VMware 嚴重損壞修復常見問題
description: 使用 Azure Site Recovery 取得內部部署 VMware Vm 至 Azure 的嚴重損壞修復常見問題解答。
ms.date: 11/14/2019
ms.topic: conceptual
ms.openlocfilehash: 8f292e7f624b80e8e13514a714c5759d88fbe57c
ms.sourcegitcommit: 8a1ba1ebc76635b643b6634cc64e137f74a1e4da
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/09/2020
ms.locfileid: "94379985"
---
# <a name="common-questions-about-vmware-to-azure-replication"></a>VMware 到 Azure 的複寫常見問題

本文將回答當您部署內部部署 VMware 虛擬機器的嚴重損壞修復 (Vm) 至 Azure 時，可能會遇到的常見問題。

## <a name="general"></a>一般

### <a name="what-do-i-need-for-vmware-vm-disaster-recovery"></a>VMware VM 損毀修復需要什麼？

瞭解 VMware Vm 的嚴重損壞修復所[牽涉的元件](vmware-azure-architecture.md)。

### <a name="can-i-use-site-recovery-to-migrate-vmware-vms-to-azure"></a>我可以使用 Site Recovery 將 VMware Vm 遷移至 Azure 嗎？

是。 除了使用 Site Recovery 設定 VMware Vm 的完整災難復原之外，您也可以使用 Site Recovery，將內部部署 VMware Vm 遷移至 Azure。 在此案例中，您會將內部部署 VMware Vm 複寫至 Azure 儲存體。 然後，您會從內部部署環境容錯移轉至 Azure。 容錯移轉之後，您的應用程式和工作負載將可在 Azure VM 上使用及執行。 此程式就像是設定完整的災難復原，但在遷移中，您無法從 Azure 容錯回復。

### <a name="does-my-azure-account-need-permissions-to-create-vms"></a>我的 Azure 帳戶是否需要建立 VM 的權限？

如果您是訂用帳戶管理員，則會具有所需的複寫權限。 如果您不是系統管理員，則需要執行下列動作的許可權：

- 在您設定 Site Recovery 時指定的資源群組和虛擬網路中建立 Azure VM。
- 根據您的設定寫入選取的儲存體帳戶或受控磁片。

[深入瞭解](site-recovery-role-based-linked-access-control.md#permissions-required-to-enable-replication-for-new-virtual-machines) 必要的許可權。

### <a name="what-applications-can-i-replicate"></a>可以複寫哪些應用程式？

您可以複寫在符合複寫 [需求](vmware-physical-azure-support-matrix.md#replicated-machines)的 VMware VM 上執行的任何應用程式或工作負載。

- Site Recovery 支援應用程式感知複寫，讓應用程式可以容錯移轉並容錯回復為智慧型狀態。
- Site Recovery 與 Microsoft 應用程式（例如 SharePoint、Exchange、Dynamics、SQL Server 和 Active Directory）整合。 它也與領導廠商密切合作，包括 Oracle、SAP、IBM 和 Red Hat。

[深入了解](site-recovery-workload.md) 工作負載保護。

### <a name="can-i-use-a-guest-os-server-license-on-azure"></a>我可以使用 Azure 上的客體作業系統伺服器授權嗎？

是，「微軟軟體保證」客戶可以使用 [Azure Hybrid Benefit](https://azure.microsoft.com/pricing/hybrid-benefit/)，節省遷移至 Azure 之 Windows Server 電腦的授權成本，或使用 Azure 進行災害復原。

## <a name="security"></a>安全性

### <a name="what-access-to-vmware-servers-does-site-recovery-need"></a>Site Recovery 需要哪些 VMware 伺服器存取權？

Site Recovery 需要存取 VMware 伺服器才能：

- 設定執行 Site Recovery 設定伺服器的 VMware VM。
- 自動探索需要複寫的 VM。

### <a name="what-access-to-vmware-vms-does-site-recovery-need"></a>Site Recovery 需要哪些 VMware Vm 存取權？

- 若要進行複寫，VMware VM 必須已安裝且正在執行 Site Recovery 行動服務。 您可以手動部署工具，也可以指定當您啟用 VM 的複寫時，Site Recovery 進行服務的推送安裝。
- 在複寫期間，VM 會與 Site Recovery 通訊，如下所示：
    - Vm 會與 HTTPS 埠443上的設定伺服器通訊，以進行複寫管理。
    - Vm 會將複寫資料傳送至 HTTPS 埠9443上的進程伺服器。  (可以修改此設定。 ) 
    - 如果您啟用多部 VM 一致性，則 VM 會透過連接埠 20004 互相通訊。

### <a name="is-replication-data-sent-to-site-recovery"></a>複寫資料是否會傳送至 Site Recovery？

否，Site Recovery 不會攔截複寫的資料，也不會有您 Vm 上執行的任何資訊。 複寫資料會在 VMware 虛擬程式和 Azure 儲存體之間進行交換。 Site Recovery 並不具有攔截該資料的能力。 只有協調複寫與容錯移轉所需的中繼資料會被傳送給 Site Recovery 服務。

Site Recovery 通過 ISO 27001:2013 和27018、HIPAA 和 DPA 的認證。 SOC2 和 FedRAMP JAB 評量正在進行中。

## <a name="pricing"></a>定價

### <a name="how-do-i-calculate-approximate-charges-for-vmware-disaster-recovery"></a>如何? 計算 VMware 嚴重損壞修復的估計費用嗎？

使用 [定價計算機](https://aka.ms/asr_pricing_calculator) 來預估使用 Site Recovery 的成本。

如需成本的詳細估計，請執行 [VMware](./site-recovery-deployment-planner.md) 的 deployment planner 工具，並使用 [成本估計報告](./site-recovery-vmware-deployment-planner-cost-estimation.md)。

### <a name="is-there-any-difference-in-cost-between-replicating-to-storage-or-directly-to-managed-disks"></a>複寫至儲存體或直接複寫到受控磁片之間的成本有任何差異嗎？

受控磁片的計費方式與儲存體帳戶稍有不同。 [深入瞭解](https://azure.microsoft.com/pricing/details/managed-disks/) 受控磁片定價。

### <a name="is-there-any-difference-in-cost-when-replicating-to-general-purpose-v2-storage-account"></a>複寫至一般用途 v2 儲存體帳戶時，成本是否有任何差異？

您通常會看到 GPv2 儲存體帳戶所產生的交易成本增加，因為 Azure Site Recovery 的交易繁重。 [深入](../storage/common/storage-account-upgrade.md#pricing-and-billing) 瞭解以預估變更。

## <a name="mobility-service"></a>行動服務

### <a name="where-can-i-find-the-mobility-service-installers"></a>哪裡可以找到行動服務安裝程式？

安裝程式位於設定伺服器的%ProgramData%\ASR\home\svsystems\pushinstallsvc\repository 資料夾中。

## <a name="how-do-i-install-the-mobility-service"></a>如何安裝行動服務？

在您要複寫的每部 VM 上，透過下列其中一種方法來安裝服務：

- [推入安裝](vmware-physical-mobility-service-overview.md#push-installation)
- 從 UI 或 PowerShell[手動安裝](vmware-physical-mobility-service-overview.md#install-the-mobility-service-using-ui)
- 使用部署工具（例如[Configuration Manager](vmware-azure-mobility-install-configuration-mgr.md) ）進行部署

## <a name="managed-disks"></a>受控磁碟

### <a name="where-does-site-recovery-replicate-data-to"></a>Site Recovery 將資料複寫到哪裡？

Site Recovery 會將內部部署 VMware Vm 和實體伺服器複寫至 Azure 中的受控磁片。

- Site Recovery 進程伺服器會將複寫記錄寫入目的地區域中的快取儲存體帳戶。
- 這些記錄會用來在具有 **asrseeddisk** 前置詞的 Azure 受控磁片上建立復原點。
- 發生容錯移轉時，您選取的復原點會用來建立新的目標受控磁片。 此受控磁片會連接至 Azure 中的 VM。
- 先前複寫到) 2019 年3月之前的儲存體帳戶 (的 Vm 不會受到影響。

### <a name="can-i-replicate-new-machines-to-storage-accounts"></a>我可以將新機器複寫至儲存體帳戶嗎？

否。 從2019年3月開始，在 Azure 入口網站中，您只能複寫到 Azure 受控磁片。

只有使用 PowerShell ([Az. az.recoveryservices module version 1.4.5](https://www.powershellgallery.com/packages/Az.RecoveryServices/1.4.5)) 或 REST API (2018-01-10 版或) 2016-08-10 版，才能將新的 vm 複寫至儲存體帳戶。 [瞭解如何](./vmware-azure-disaster-recovery-powershell.md) 使用 PowerShell 命令來設定複寫。

### <a name="what-are-the-benefits-of-replicating-to-managed-disks"></a>複寫至受控磁片的優點有哪些？

[瞭解如何](https://azure.microsoft.com/blog/simplify-disaster-recovery-with-managed-disks-for-vmware-and-physical-servers/) Site Recovery 使用受控磁片來簡化嚴重損壞修復。

### <a name="can-i-change-the-managed-disk-type-after-a-machine-is-protected"></a>我可以在電腦受保護之後變更受控磁片類型嗎？

是，您可以輕鬆地 [變更受控磁片的類型](../virtual-machines/windows/convert-disk-storage.md) 以進行進行中的複寫。 變更類型之前，請確定受控磁片上沒有產生任何共用存取簽章 URL：

1. 移至 Azure 入口網站上的 **受控磁片** 資源，並檢查您是否在 **總覽** 分頁上有共用存取簽章 URL 橫幅。
1. 如果出現橫幅，請選取它來取消進行中的匯出。
1. 在接下來的幾分鐘內變更磁片的類型。 如果您變更受控磁片類型，請等候 Azure Site Recovery 產生最新的復原點。
1. 未來請使用新的復原點進行任何測試容錯移轉或容錯移轉。

### <a name="can-i-switch-replication-from-managed-disks-to-unmanaged-disks"></a>是否可以將複寫從受控磁片切換至非受控磁片？

否。 不支援從受控切換至非受控。

## <a name="replication"></a>複寫

### <a name="what-are-the-replicated-vm-requirements"></a>複寫的 VM 有何需求？

[深入瞭解](vmware-physical-azure-support-matrix.md#replicated-machines) VMware vm 和實體伺服器的支援需求。

### <a name="how-often-can-i-replicate-to-azure"></a>複寫到 Azure 的頻率為何？

從 VMware VM 到 Azure 的複寫會持續執行。

### <a name="can-i-extend-replication"></a>我可以延伸複寫嗎？

不支援延伸的或鏈結的複寫。 請在 [意見反應論壇](https://feedback.azure.com/forums/256299-site-recovery/suggestions/6097959)中提出這項功能的要求。

### <a name="can-i-do-an-offline-initial-replication"></a>是否可執行離線初始複寫？

不支援離線複寫。 請在 [意見反應論壇](https://feedback.azure.com/forums/256299-site-recovery/suggestions/6227386-support-for-offline-replication-data-transfer-from)中提出這項功能的要求。

### <a name="what-is-asrseeddisk"></a>什麼是 asrseeddisk？

針對每個來源磁片，資料會複寫至 Azure 中的受控磁片。 此磁片的前置詞為 **asrseeddisk** 。 它會儲存來源磁片和所有復原點快照集的複本。

### <a name="can-i-exclude-disks-from-replication"></a>我可以從複寫中排除磁片嗎？

是，您可以排除磁片。

### <a name="can-i-replicate-vms-that-have-dynamic-disks"></a>我可以複寫具有動態磁碟的 Vm 嗎？

動態磁碟可以複寫。 作業系統磁碟必須是基本磁碟。

### <a name="if-i-use-replication-groups-for-multi-vm-consistency-can-i-add-a-new-vm-to-an-existing-replication-group"></a>如果我使用複寫群組以獲取多 VM 一致性，我是否可將新的 VM 新增至現有的複寫群組？

是，您可以將新的 VM 新增至現有的複寫群組，為這些 VM 啟用複寫。 但是：

- 複寫開始之後，您就無法將 VM 新增至現有的複寫群組。
- 您無法為現有的 Vm 建立複寫群組。

### <a name="can-i-modify-vms-that-are-replicating-by-adding-or-resizing-disks"></a>我是否可藉由新增磁碟或調整其大小來修改要複寫的 VM？

針對 VMware 至 Azure 的複寫，您可以修改來源 Vm 的磁片大小。 如果您想要新增磁片，您必須新增磁片並重新啟用 VM 的保護。

### <a name="can-i-migrate-on-premises-machines-to-a-new-vcenter-server-without-impacting-ongoing-replication"></a>我可以將內部部署機器遷移至新的 vCenter Server，而不會影響進行中的複寫嗎？

請參閱我們的 [指引](vmware-azure-manage-vcenter.md#migrate-all-vms-to-a-new-server) ，以將機器遷移至新的 vCenter

### <a name="can-i-replicate-to-a-cache-or-target-storage-account-that-has-a-virtual-network-with-azure-firewalls-configured-on-it"></a>我可以將虛擬網路 (的快取或目標儲存體帳戶複寫到 Azure 防火牆) 設定嗎？

否，Site Recovery 不支援複寫至虛擬網路上的 Azure 儲存體。

### <a name="what-is-the-frequency-of-generation-of-crash-consistent-recovery-points"></a>損毀一致復原點產生的頻率為何？

Site Recovery 每隔5分鐘會產生一次損毀一致復原點。

## <a name="component-upgrade"></a>元件升級

### <a name="my-version-of-the-mobility-services-agent-or-configuration-server-is-old-and-my-upgrade-failed-what-do-i-do"></a>我的行動服務代理程式或設定伺服器版本是舊的，我的升級失敗。 該怎麼辦？

Site Recovery 遵循 N-4 支援模型。 [深入瞭解](./service-updates-how-to.md#support-statement-for-azure-site-recovery) 如何從非常舊的版本進行升級。

### <a name="where-can-i-find-the-release-notes-and-update-rollups-for-azure-site-recovery"></a>我可以在哪裡找到 Azure Site Recovery 的版本資訊和更新彙總套件？

[瞭解新的更新](site-recovery-whats-new.md)，並 [取得匯總資訊](service-updates-how-to.md)。

### <a name="where-can-i-find-upgrade-information-for-disaster-recovery-to-azure"></a>哪裡可以找到損毀修復至 Azure 的升級資訊？

[瞭解如何升級](./service-updates-how-to.md#vmware-vmphysical-server-disaster-recovery-to-azure)。

## <a name="do-i-need-to-reboot-source-machines-for-each-upgrade"></a>每次升級時是否需要重新開機來源機器？

建議您重新開機，而不是每次升級都強制進行。 [深入了解](./service-updates-how-to.md#reboot-after-mobility-service-upgrade)。

## <a name="configuration-server"></a>組態伺服器

### <a name="what-does-the-configuration-server-do"></a>組態伺服器有何功用？

組態伺服器會執行內部部署 Site Recovery 元件，包括：

- 設定伺服器本身。 伺服器會協調內部部署元件與 Azure 之間的通訊，以及管理資料複寫。
- 進程伺服器，作為複寫閘道。 此伺服器：
    1. 接收複寫資料。
    2. 運用快取、壓縮和加密將該資料最佳化。
    3. 將資料傳送至 Azure 儲存體。
  進程伺服器也會在 Vm 上進行行動服務的推送安裝，並執行內部部署 VMware Vm 的自動探索。
- 主要目標伺服器，會在從 Azure 容錯回復期間，處理複寫資料。

[深入了解](vmware-azure-architecture.md)組態伺服器元件和程序。

### <a name="where-do-i-set-up-the-configuration-server"></a>我應在何處設定組態伺服器？

您需要設定伺服器的單一、高可用性、內部部署 VMware VM。 針對實體伺服器嚴重損壞修復，請在實體電腦上安裝設定伺服器。

### <a name="what-do-i-need-for-the-configuration-server"></a>設定伺服器需要什麼？

檢查 [必要條件](vmware-azure-deploy-configuration-server.md#prerequisites)。

### <a name="can-i-manually-set-up-the-configuration-server-instead-of-using-a-template"></a>是否可以手動設定組態伺服器，而不要使用範本？

建議您使用最新版的開放式虛擬化格式 (OVF) 範本來 [建立設定伺服器 VM](vmware-azure-deploy-configuration-server.md) 。 如果您無法使用範本 (例如，如果您沒有 VMware 伺服器) 的存取權，請從入口網站 [下載](physical-azure-set-up-source.md) 安裝程式檔案，然後設定伺服器。

### <a name="can-a-configuration-server-replicate-to-more-than-one-region"></a>組態伺服器是否可複寫到多個區域？

否。 若要複寫到多個區域，您需要在每個區域中都有設定伺服器。

### <a name="can-i-host-a-configuration-server-in-azure"></a>是否可將組態伺服器裝載在 Azure 中？

雖然這是可行的，但執行設定伺服器的 Azure VM 必須與您的內部部署 VMware 基礎結構和 Vm 進行通訊。 這種通訊會增加延遲，並影響進行中的複寫。

### <a name="how-do-i-update-the-configuration-server"></a>如何更新組態伺服器？

[瞭解](vmware-azure-manage-configuration-server.md#upgrade-the-configuration-server) 如何更新設定伺服器。

- 您可以在 [Azure 更新頁面](https://azure.microsoft.com/updates/?product=site-recovery)上找到最新的更新資訊。
- 您可以從入口網站下載最新版本。 或者，您可以直接從 [Microsoft 下載中心](https://aka.ms/asrconfigurationserver)下載最新版的設定伺服器。
- 如果您的版本比目前的版本還舊了四個以上的版本，請參閱升級指南的 [支援聲明](./service-updates-how-to.md#support-statement-for-azure-site-recovery) 。

### <a name="should-i-back-up-the-configuration-server"></a>我應該備份設定伺服器嗎？

建議對於組態伺服器進行定期排程的備份。

- 若要成功容錯回復，已容錯回復的 VM 必須存在於設定伺服器資料庫中。
- 設定伺服器必須正在執行且處於線上狀態。
- [深入瞭解](vmware-azure-manage-configuration-server.md) 一般設定伺服器管理工作。

### <a name="when-im-setting-up-the-configuration-server-can-i-download-and-install-mysql-manually"></a>在設定組態伺服器時，是否可以手動下載並安裝 MySQL？

是。 請下載 MySQL 並將它放在 C:\Temp\ASRSetup 資料夾中。 然後以手動方式進行安裝。 當您設定組態伺服器 VM 時，如果您接受條款，MySQL 在 [下載並安裝] 中就會列示為 [已安裝]。

### <a name="can-i-avoid-downloading-mysql-but-let-site-recovery-install-it"></a>是否可以不要下載 MySQL 而讓 Site Recovery 加以安裝？

是。 請下載 MySQL 安裝程式，並將它放在 C:\Temp\ASRSetup 資料夾中。 當您設定伺服器 VM 時，請接受條款，然後選取 [ **下載並安裝** ]。 入口網站將會使用您新增的安裝程式來安裝 MySQL。

### <a name="can-i-use-the-configuration-server-vm-for-anything-else"></a>組態伺服器 VM 是否可用於任何其他位置？

否。 請僅針對設定伺服器使用 VM。

### <a name="can-i-clone-a-configuration-server-and-use-it-for-orchestration"></a>是否可以複製組態伺服器並將它用於協調流程？

否。 設定全新的設定伺服器，以避免註冊問題。

### <a name="can-i-change-the-vault-in-which-the-configuration-server-is-registered"></a>我可以變更佈建服務器註冊所在的保存庫嗎？

否。 保存庫與設定伺服器建立關聯之後，就無法變更。 [瞭解](vmware-azure-manage-configuration-server.md#register-a-configuration-server-with-a-different-vault) 如何在不同的保存庫中註冊設定伺服器。

### <a name="can-i-use-the-same-configuration-server-for-disaster-recovery-of-both-vmware-vms-and-physical-servers"></a>是否可以將相同的設定伺服器用於 VMware Vm 和實體伺服器的嚴重損壞修復？

是的，但請注意，實體機器只能容錯回復到 VMware VM。

### <a name="where-can-i-download-the-passphrase-for-the-configuration-server"></a>哪裡可以下載組態伺服器的複雜密碼？

[瞭解](vmware-azure-manage-configuration-server.md#generate-configuration-server-passphrase) 如何下載複雜密碼。

### <a name="where-can-i-download-vault-registration-keys"></a>哪裡可以下載保存庫註冊金鑰？

在復原服務保存庫中，選取 [ **Site Recovery 基礎結構** 管理] 中的 [設定 **伺服器** ]  >  **** 。 然後，在 [ **伺服器** ] 中，選取 [ **下載註冊金鑰** ] 以下載保存庫認證檔。

### <a name="can-a-single-configuration-server-be-used-to-protect-multiple-vcenter-instances"></a>是否可以使用單一設定伺服器來保護多個 vCenter 實例？

是的，單一設定伺服器可以跨多個 vCenters 保護 Vm。  可以新增至設定伺服器的 vCenter 實例數量沒有限制，不過，單一設定伺服器可保護的 Vm 數量限制適用。

### <a name="can-a-single-configuration-server-protect-multiple-clusters-within-vcenter"></a>單一設定伺服器是否可以保護 vCenter 內的多個叢集？

是的，Azure Site Recovery 可以跨不同的叢集保護 Vm。

## <a name="process-server"></a>處理序伺服器

### <a name="why-am-i-unable-to-select-the-process-server-when-i-enable-replication"></a>為什麼我在啟用複寫時無法選取進程伺服器？

[當您啟用複寫時](vmware-azure-enable-replication.md#enable-replication)，9.24 版和更新版本中的更新現在會顯示進程伺服器的健康情況。 這項功能有助於避免進程伺服器的節流，以及將狀況不良的進程伺服器的使用降至最低。

### <a name="how-do-i-update-the-process-server-to-version-924-or-later-for-accurate-health-information"></a>如何? 將進程伺服器更新為9.24 版或更新版本，以取得準確的健康情況資訊？

從 [9.24 版](service-updates-how-to.md#links-to-currently-supported-update-rollups)開始，已新增更多警示來指出進程伺服器的健康情況。 將[Site Recovery 元件更新為9.24 版或更新版本](service-updates-how-to.md#links-to-currently-supported-update-rollups)，以產生所有警示。

### <a name="how-can-i-ensure-high-availability-of-the-process-server"></a>如何確保進程伺服器的高可用性？

藉由設定一部以上的進程伺服器，此設計可讓您彈性地將受保護的機器從狀況不良的進程伺服器移至工作進程伺服器。 將機器從某個進程伺服器移到另一個進程伺服器，必須透過下列定義的步驟明確/手動起始：在 [進程伺服器之間移動 vm](vmware-azure-manage-process-server.md#move-vms-to-balance-the-process-server-load)。

## <a name="failover-and-failback"></a>容錯移轉和容錯回復

### <a name="can-i-use-the-on-premises-process-server-for-failback"></a>是否可以使用內部部署進程伺服器來進行容錯回復？

基於容錯回復目的，我們強烈建議在 Azure 中建立進程伺服器，以避免資料傳輸延遲。 此外，如果您將來源 Vm 網路與設定伺服器中的 Azure 面向網路區隔開，則必須使用在 Azure 中建立的進程伺服器來進行容錯回復。

### <a name="can-i-keep-the-ip-address-on-failover"></a>我可以在容錯移轉時保留 IP 位址嗎？

是，您可以在容錯移轉時保留 IP 位址。 在容錯移轉之前，請確定您已在 VM 的 [ **計算和網路** ] 設定中指定目標 IP 位址。 此外，在容錯移轉時將機器關機，以避免 IP 位址在容錯回復期間發生衝突。

### <a name="can-i-change-the-target-vm-size-or-vm-type-before-failover"></a>我可以在容錯移轉之前變更目標 VM 大小或 VM 類型嗎？

是，您可以在容錯移轉之前的任何時間變更 VM 的類型或大小。 在入口網站中，使用複寫 VM 的 [ **計算和網路** ] 設定。

### <a name="how-far-back-can-i-recover"></a>最多可復原至多久之前？

針對 VMware 到 Azure，您可以使用的最舊復原點為72小時。

### <a name="how-do-i-access-azure-vms-after-failover"></a>在容錯移轉之後如何存取 Azure VM？

在容錯移轉之後，您可以透過安全的網際網路連線、透過站對站 VPN 或透過 Azure ExpressRoute 存取 Azure Vm。 若要連接，您必須準備數個專案。 [深入了解](site-recovery-test-failover-to-azure.md#prepare-to-connect-to-azure-vms-after-failover)。

### <a name="is-failed-over-data-resilient"></a>是否已容錯移轉資料復原？

Azure 是針對復原能力而設計的。 Site Recovery 的設計是為了容錯移轉至次要 Azure 資料中心，如 Azure 服務等級協定 (SLA) 所要求。 當容錯移轉發生時，我們會確保您的中繼資料和保存庫會保留在您為保存庫選擇的相同地理區域中。

### <a name="is-failover-automatic"></a>容錯移轉是自動發生的嗎？

[容錯移轉](site-recovery-failover.md) 不會自動進行。 您可以在入口網站中進行單一選取，以啟動容錯移轉，也可以使用 [PowerShell](/powershell/module/az.recoveryservices) 來觸發容錯移轉。

### <a name="can-i-fail-back-to-a-different-location"></a>是否可容錯回復至不同的位置？

是。 如果您容錯移轉至 Azure，您可以容錯回復至不同的位置（如果原始的位置無法使用的話）。 [深入了解](concepts-types-of-failback.md#alternate-location-recovery-alr)。

### <a name="why-do-i-need-a-vpn-or-expressroute-with-private-peering-to-fail-back"></a>為什麼我需要具有私人對等互連的 VPN 或 ExpressRoute 才能容錯回復？

當您從 Azure 容錯回復時，Azure 中的資料會複製回您的內部部署 VM，而且需要私人存取。


## <a name="automation-and-scripting"></a>自動化和指令碼

### <a name="can-i-set-up-replication-with-scripting"></a>是否可以使用指令碼設定複寫？

是。 您可以使用 Rest API、PowerShell 或 Azure SDK 將 Site Recovery 工作流程自動化。 [深入了解](vmware-azure-disaster-recovery-powershell.md)。

## <a name="performance-and-capacity"></a>效能與容量

### <a name="can-i-throttle-replication-bandwidth"></a>是否可進行複寫頻寬節流？

是。 [深入了解](site-recovery-plan-capacity-vmware.md)。

## <a name="next-steps"></a>後續步驟

- [檢閱](vmware-physical-azure-support-matrix.md)支援需求。
- [設定](vmware-azure-tutorial.md) VMware 到 Azure 的複寫。