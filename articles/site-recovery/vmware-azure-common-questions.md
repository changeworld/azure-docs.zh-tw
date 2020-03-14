---
title: 使用 Azure Site Recovery 進行 VMware 嚴重損壞修復的常見問題
description: 使用 Azure Site Recovery，取得內部部署 VMware Vm 到 Azure 的嚴重損壞修復常見問題的解答。
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.date: 11/14/2019
ms.topic: conceptual
ms.author: raynew
ms.openlocfilehash: 11f593bae6b3aab8355a8f39b56639cad8133b51
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/13/2020
ms.locfileid: "79257442"
---
# <a name="common-questions-about-vmware-to-azure-replication"></a>VMware 到 Azure 的複寫常見問題

本文將說明當您將內部部署 VMware 虛擬機器（Vm）的嚴重損壞修復部署至 Azure 時，可能會出現的常見問題。

## <a name="general"></a>一般

### <a name="what-do-i-need-for-vmware-vm-disaster-recovery"></a>VMware VM 嚴重損壞修復需要什麼？

瞭解 VMware Vm 的嚴重損壞修復所[牽涉到的元件](vmware-azure-architecture.md)。

### <a name="can-i-use-site-recovery-to-migrate-vmware-vms-to-azure"></a>我可以使用 Site Recovery 將 VMware Vm 遷移至 Azure 嗎？

是。 除了使用 Site Recovery 來設定 VMware Vm 的完整嚴重損壞修復之外，您也可以使用 Site Recovery 將內部部署 VMware Vm 遷移至 Azure。 在此案例中，您會將內部部署 VMware Vm 複寫至 Azure 儲存體。 然後，您會從內部部署環境容錯移轉至 Azure。 容錯移轉之後，您的應用程式和工作負載將可在 Azure VM 上使用及執行。 此程式就像是設定完整的嚴重損壞修復，不同的是，在遷移時，您無法從 Azure 容錯回復。

### <a name="does-my-azure-account-need-permissions-to-create-vms"></a>我的 Azure 帳戶是否需要建立 VM 的權限？

如果您是訂用帳戶管理員，則會具有所需的複寫權限。 如果您不是系統管理員，您需要有許可權才能執行下列動作：

- 在您設定 Site Recovery 時，于您指定的資源群組和虛擬網路中建立 Azure VM。
- 根據您的設定，寫入所選取的儲存體帳戶或受控磁片。

[深入瞭解](site-recovery-role-based-linked-access-control.md#permissions-required-to-enable-replication-for-new-virtual-machines)必要的許可權。

### <a name="what-applications-can-i-replicate"></a>可以複寫哪些應用程式？

您可以複寫在符合複寫[需求](vmware-physical-azure-support-matrix.md#replicated-machines)的 VMware VM 上執行的任何應用程式或工作負載。

- Site Recovery 支援應用程式感知複寫，因此應用程式可以容錯回復並容錯回復為智慧型狀態。
- Site Recovery 與 Microsoft 應用程式（例如 SharePoint、Exchange、Dynamics、SQL Server 和 Active Directory）整合。 它也與領導廠商密切合作，包括 Oracle、SAP、IBM 和 Red Hat。

[深入了解](site-recovery-workload.md) 工作負載保護。

### <a name="can-i-use-a-guest-os-server-license-on-azure"></a>我可以在 Azure 上使用來賓 OS 伺服器授權嗎？

是，Microsoft 軟體保證客戶可以使用[Azure Hybrid Benefit](https://azure.microsoft.com/pricing/hybrid-benefit/)來節省遷移至 Azure 之 Windows Server 電腦的授權成本，或使用 Azure 進行嚴重損壞修復。

## <a name="security"></a>安全性

### <a name="what-access-to-vmware-servers-does-site-recovery-need"></a>Site Recovery 需要哪些 VMware 伺服器的存取權？

Site Recovery 需要存取 VMware 伺服器才能：

- 設定執行 Site Recovery configuration server 的 VMware VM。
- 自動探索需要複寫的 VM。

### <a name="what-access-to-vmware-vms-does-site-recovery-need"></a>Site Recovery 需要 VMware Vm 的存取權？

- 若要進行複寫，VMware VM 必須已安裝且正在執行 Site Recovery 行動服務。 您可以手動部署此工具，也可以指定在啟用 VM 複寫時，Site Recovery 執行服務的推送安裝。
- 在複寫期間，VM 會與 Site Recovery 通訊，如下所示：
    - Vm 會與 HTTPS 埠443上的設定伺服器通訊，以進行複寫管理。
    - Vm 會將複寫資料傳送至 HTTPS 埠9443上的進程伺服器。 （這是可修改的設定）。
    - 如果您啟用多部 VM 一致性，則 VM 會透過連接埠 20004 互相通訊。

### <a name="is-replication-data-sent-to-site-recovery"></a>複寫資料是否會傳送至 Site Recovery？

否，Site Recovery 不會攔截複寫的資料，也不會有任何有關您 Vm 上執行之內容的資訊。 複寫資料會在 VMware 虛擬機器和 Azure 儲存體之間交換。 Site Recovery 並不具有攔截該資料的能力。 只有協調複寫與容錯移轉所需的中繼資料會被傳送給 Site Recovery 服務。  

Site Recovery 已通過 ISO 27001:2013 和27018、HIPAA 和 DPA 的認證。 它正在進行 SOC2 和 FedRAMP JAB 評量。

## <a name="pricing"></a>價格

### <a name="how-do-i-calculate-approximate-charges-for-vmware-disaster-recovery"></a>如何? 計算 VMware 嚴重損壞修復的大約費用？

使用[定價計算機](https://aka.ms/asr_pricing_calculator)來預估使用 Site Recovery 時的成本。

如需成本的詳細估計，請執行適用于[VMware](https://aka.ms/siterecovery_deployment_planner)的部署規劃工具，並使用[成本估計報告](https://aka.ms/asr_DP_costreport)。

### <a name="is-there-any-difference-in-cost-between-replicating-to-storage-or-directly-to-managed-disks"></a>複寫到儲存體或直接複寫到受控磁片之間的成本有任何差異嗎？

受控磁片的收費方式與儲存體帳戶稍有不同。 [深入瞭解](https://azure.microsoft.com/pricing/details/managed-disks/)受控磁片的定價。

### <a name="is-there-any-difference-in-cost-when-replicating-to-general-purpose-v2-storage-account"></a>複寫至一般用途 v2 儲存體帳戶時，成本是否有任何差異？

您通常會看到 GPv2 儲存體帳戶所產生的交易成本增加，因為 Azure Site Recovery 的交易量很高。 [閱讀更多](../storage/common/storage-account-upgrade.md#pricing-and-billing)以估計變更。

## <a name="mobility-service"></a>行動服務

### <a name="where-can-i-find-the-mobility-service-installers"></a>哪裡可以找到行動服務安裝程式？

安裝程式位於設定伺服器上的%ProgramData%\ASR\home\svsystems\pushinstallsvc\repository 資料夾中。

## <a name="how-do-i-install-the-mobility-service"></a>如何安裝行動服務？

在您要複寫的每個 VM 上，使用下列其中一種方法來安裝服務：

- [推送安裝](vmware-physical-mobility-service-overview.md#push-installation)
- 從 UI 或 PowerShell[手動安裝](vmware-physical-mobility-service-overview.md#install-mobility-agent-through-ui)
- 使用部署工具（例如[Configuration Manager](vmware-azure-mobility-install-configuration-mgr.md) ）進行部署

## <a name="managed-disks"></a>受控磁碟

### <a name="where-does-site-recovery-replicate-data-to"></a>Site Recovery 將資料複製到何處？

Site Recovery 會將內部部署 VMware Vm 和實體伺服器複寫至 Azure 中的受控磁片。

- Site Recovery 進程伺服器會將複寫記錄寫入目的地區域中的快取儲存體帳戶。
- 這些記錄是用來在 Azure 管理的磁片上建立具有**asrseeddisk**前置詞的復原點。
- 發生容錯移轉時，您選取的復原點會用來建立新的目標受控磁片。 此受控磁片會連結至 Azure 中的 VM。
- 先前複寫至儲存體帳戶的 Vm （2019年3月之前）不會受到影響。

### <a name="can-i-replicate-new-machines-to-storage-accounts"></a>我可以將新機器複寫到儲存體帳戶嗎？

否。 自2019年3月起，在 Azure 入口網站中，您只能複寫至 Azure 受控磁片。

只有使用 PowerShell 或 REST API （版本2018-01-10 或2016-08-10），才可以將新 Vm 複寫到儲存體帳戶。

### <a name="what-are-the-benefits-of-replicating-to-managed-disks"></a>複寫至受控磁片有哪些優點？

[深入瞭解](https://azure.microsoft.com/blog/simplify-disaster-recovery-with-managed-disks-for-vmware-and-physical-servers/)Site Recovery 使用受控磁片來簡化嚴重損壞修復。

### <a name="can-i-change-the-managed-disk-type-after-a-machine-is-protected"></a>我可以在電腦受到保護之後，變更受控磁片類型嗎？

是，您可以輕鬆地[變更受控磁片的類型](https://docs.microsoft.com/azure/virtual-machines/windows/convert-disk-storage)，以進行進行中的複寫。 變更類型之前，請確定在受控磁片上不會產生共用存取簽章 URL：

1. 移至 Azure 入口網站上的**受控磁片**資源，並檢查 [**總覽**] 分頁上是否有 [共用存取簽章 URL] 橫幅。
1. 如果出現橫幅，請選取它以取消進行中的匯出。
1. 在接下來的幾分鐘內變更磁片的類型。 如果您變更受控磁片類型，請等候 Azure Site Recovery 產生新的復原點。
1. 未來將會針對任何測試容錯移轉或容錯移轉使用新的復原點。

### <a name="can-i-switch-replication-from-managed-disks-to-unmanaged-disks"></a>我可以將複寫從受控磁片切換到非受控磁片嗎？

否。 不支援從受控切換至非受控。

## <a name="replication"></a>複寫

### <a name="what-are-the-replicated-vm-requirements"></a>複寫的 VM 有何需求？

[深入瞭解](vmware-physical-azure-support-matrix.md#replicated-machines)VMware vm 和實體伺服器的支援需求。

### <a name="how-often-can-i-replicate-to-azure"></a>複寫到 Azure 的頻率為何？

從 VMware VM 到 Azure 的複寫會持續執行。

### <a name="can-i-extend-replication"></a>我可以延伸複寫嗎？

不支援延伸的或鏈結的複寫。 請在 [意見反應論壇](https://feedback.azure.com/forums/256299-site-recovery/suggestions/6097959)中提出這項功能的要求。

### <a name="can-i-do-an-offline-initial-replication"></a>是否可執行離線初始複寫？

不支援離線複寫。 請在 [意見反應論壇](https://feedback.azure.com/forums/256299-site-recovery/suggestions/6227386-support-for-offline-replication-data-transfer-from)中提出這項功能的要求。

### <a name="what-is-asrseeddisk"></a>什麼是 asrseeddisk？

針對每個來源磁片，資料會複寫至 Azure 中的受控磁片。 此磁片的前置詞為**asrseeddisk**。 它會儲存來源磁片和所有復原點快照集的複本。

### <a name="can-i-exclude-disks-from-replication"></a>我可以排除磁片不要複寫嗎？

是，您可以排除磁片。

### <a name="can-i-replicate-vms-that-have-dynamic-disks"></a>我可以複寫具有動態磁碟的 Vm 嗎？

動態磁碟可以複寫。 作業系統磁碟必須是基本磁碟。

### <a name="if-i-use-replication-groups-for-multi-vm-consistency-can-i-add-a-new-vm-to-an-existing-replication-group"></a>如果我使用複寫群組以獲取多 VM 一致性，我是否可將新的 VM 新增至現有的複寫群組？

是，您可以將新的 VM 新增至現有的複寫群組，為這些 VM 啟用複寫。 但是：

- 複寫開始之後，您就無法將 VM 新增至現有的複寫群組。
- 您無法為現有的 Vm 建立複寫群組。

### <a name="can-i-modify-vms-that-are-replicating-by-adding-or-resizing-disks"></a>我是否可藉由新增磁碟或調整其大小來修改要複寫的 VM？

若要將 VMware 複寫至 Azure，您可以修改來源 Vm 的磁片大小。 如果您想要新增磁片，則必須新增磁片並重新啟用 VM 的保護。

### <a name="can-i-migrate-on-premises-machines-to-a-new-vcenter-server-without-impacting-ongoing-replication"></a>我可以將內部部署機器遷移到新的 vCenter Server，而不會影響進行中的複寫嗎？

否。 VMware Vcenter 或遷移的變更會影響進行中的複寫。 使用新的 vCenter Server 設定 Site Recovery，並再次啟用機器的複寫。

### <a name="can-i-replicate-to-a-cache-or-target-storage-account-that-has-a-virtual-network-with-azure-firewalls-configured-on-it"></a>我可以複寫至已設定虛擬網路（具有 Azure 防火牆）的快取或目標儲存體帳戶嗎？

否，Site Recovery 不支援複寫至虛擬網路上的 Azure 儲存體。

## <a name="component-upgrade"></a>元件升級

### <a name="my-version-of-the-mobility-services-agent-or-configuration-server-is-old-and-my-upgrade-failed-what-do-i-do"></a>我的行動服務代理程式或設定伺服器的版本是舊的，而我的升級失敗。 該怎麼辦？

Site Recovery 遵循 N-4 支援模型。 [深入瞭解](https://aka.ms/asr_support_statement)如何從非常舊的版本升級。

### <a name="where-can-i-find-the-release-notes-and-update-rollups-for-azure-site-recovery"></a>哪裡可以找到 Azure Site Recovery 的版本資訊和更新彙總套件？

[深入瞭解新的更新](site-recovery-whats-new.md)，並[取得匯總資訊](service-updates-how-to.md)。

### <a name="where-can-i-find-upgrade-information-for-disaster-recovery-to-azure"></a>哪裡可以找到損毀修復到 Azure 的升級資訊？

[瞭解升級](https://aka.ms/asr_vmware_upgrades)。

## <a name="do-i-need-to-reboot-source-machines-for-each-upgrade"></a>是否需要針對每個升級重新開機來源機器？

建議您重新開機，但不是每次升級都強制。 [詳細資訊](https://aka.ms/asr_vmware_upgrades)。

## <a name="configuration-server"></a>組態伺服器

### <a name="what-does-the-configuration-server-do"></a>組態伺服器有何功用？

組態伺服器會執行內部部署 Site Recovery 元件，包括：

- 設定伺服器本身。 伺服器會協調內部部署元件與 Azure 之間的通訊，以及管理資料複寫。
- 進程伺服器，作為複寫閘道。 此伺服器：
    1. 接收複寫資料。
    2. 運用快取、壓縮和加密將該資料最佳化。
    3. 將資料傳送至 Azure 儲存體。
  進程伺服器也會在 Vm 上執行行動服務的推入安裝，並自動探索內部部署 VMware Vm。
- 主要目標伺服器，其會在從 Azure 容錯回復期間處理複寫資料。

[深入了解](vmware-azure-architecture.md)組態伺服器元件和程序。

### <a name="where-do-i-set-up-the-configuration-server"></a>我應在何處設定組態伺服器？

您需要設定伺服器的單一、高可用性、內部部署 VMware VM。 針對實體伺服器嚴重損壞修復，請在實體電腦上安裝設定伺服器。

### <a name="what-do-i-need-for-the-configuration-server"></a>設定伺服器需要什麼？

請檢閱[必要條件](vmware-azure-deploy-configuration-server.md#prerequisites)。

### <a name="can-i-manually-set-up-the-configuration-server-instead-of-using-a-template"></a>是否可以手動設定組態伺服器，而不要使用範本？

我們建議您使用最新版的開放式虛擬化格式（OVF）範本來[建立設定伺服器 VM](vmware-azure-deploy-configuration-server.md) 。 如果您無法使用此範本（例如，如果您沒有 VMware 伺服器的存取權），請從入口網站[下載](physical-azure-set-up-source.md)安裝程式檔案，並設定設定伺服器。

### <a name="can-a-configuration-server-replicate-to-more-than-one-region"></a>組態伺服器是否可複寫到多個區域？

否。 若要複寫到多個區域，您必須在每個區域中都有設定伺服器。

### <a name="can-i-host-a-configuration-server-in-azure"></a>是否可將組態伺服器裝載在 Azure 中？

雖然可能，但執行設定伺服器的 Azure VM 必須與您的內部部署 VMware 基礎結構和 Vm 通訊。 這種通訊會增加延遲，並影響進行中的複寫。

### <a name="how-do-i-update-the-configuration-server"></a>如何更新組態伺服器？

[瞭解](vmware-azure-manage-configuration-server.md#upgrade-the-configuration-server)如何更新設定伺服器。

- 您可以在[Azure 更新頁面](https://azure.microsoft.com/updates/?product=site-recovery)上找到最新的更新資訊。
- 您可以從入口網站下載最新版本。 或者，您可以直接從[Microsoft 下載中心](https://aka.ms/asrconfigurationserver)下載最新版的設定伺服器。
- 如果您的版本超過目前版本的四個版本，請參閱升級指引的[支援聲明](https://aka.ms/asr_support_statement)。

### <a name="should-i-back-up-the-configuration-server"></a>我應該備份設定伺服器嗎？

建議對於組態伺服器進行定期排程的備份。

- 若要成功進行容錯回復，已容錯回復的 VM 必須存在於設定伺服器資料庫中。
- 設定伺服器必須正在執行，且處於線上狀態。
- [深入瞭解](vmware-azure-manage-configuration-server.md)一般設定伺服器管理工作。

### <a name="when-im-setting-up-the-configuration-server-can-i-download-and-install-mysql-manually"></a>在設定組態伺服器時，是否可以手動下載並安裝 MySQL？

是。 下載 MySQL，並將它放在 C:\Temp\ASRSetup 資料夾中。 然後，手動安裝它。 當您設定組態伺服器 VM 時，如果您接受條款，MySQL 在 [下載並安裝] 中就會列示為 [已安裝]。

### <a name="can-i-avoid-downloading-mysql-but-let-site-recovery-install-it"></a>是否可以不要下載 MySQL 而讓 Site Recovery 加以安裝？

是。 下載 MySQL 安裝程式，並將它放在 C:\Temp\ASRSetup 資料夾中。 當您安裝設定伺服器 VM 時，請接受條款，然後選取 [**下載並安裝**]。 入口網站將會使用您新增的安裝程式來安裝 MySQL。

### <a name="can-i-use-the-configuration-server-vm-for-anything-else"></a>組態伺服器 VM 是否可用於任何其他位置？

否。 僅針對設定伺服器使用 VM。

### <a name="can-i-clone-a-configuration-server-and-use-it-for-orchestration"></a>是否可以複製組態伺服器並將它用於協調流程？

否。 設定全新的設定伺服器，以避免註冊問題。

### <a name="can-i-change-the-vault-in-which-the-configuration-server-is-registered"></a>我可以變更設定伺服器註冊所在的保存庫嗎？

否。 保存庫與設定伺服器相關聯之後，即無法變更。 [瞭解](vmware-azure-manage-configuration-server.md#register-a-configuration-server-with-a-different-vault)如何向不同的保存庫註冊設定伺服器。

### <a name="can-i-use-the-same-configuration-server-for-disaster-recovery-of-both-vmware-vms-and-physical-servers"></a>是否可以將相同的設定伺服器用於 VMware Vm 和實體伺服器的嚴重損壞修復？

是，但請注意，實體機器只能容錯回復到 VMware VM。

### <a name="where-can-i-download-the-passphrase-for-the-configuration-server"></a>哪裡可以下載組態伺服器的複雜密碼？

[瞭解](vmware-azure-manage-configuration-server.md#generate-configuration-server-passphrase)如何下載複雜密碼。

### <a name="where-can-i-download-vault-registration-keys"></a>哪裡可以下載保存庫註冊金鑰？

在復原服務保存庫中，選取  **Site Recovery 基礎結構** 中的 設定**伺服器** > **管理**。 然後，在 [**伺服器**] 中，選取 [**下載註冊金鑰**] 以下載保存庫認證檔案。

### <a name="can-a-single-configuration-server-be-used-to-protect-multiple-vcenter-instances"></a>是否可以使用單一設定伺服器來保護多個 vCenter 實例？

是，單一設定伺服器可以保護跨多個 vCenters 的 Vm。  您可以將多少個 vCenter 實例新增至設定伺服器，不過，單一設定伺服器可以保護的 Vm 數目限制會受到限制。

### <a name="can-a-single-configuration-server-protect-multiple-clusters-within-vcenter"></a>單一設定伺服器是否可以保護 vCenter 內的多個叢集？

是，Azure Site Recovery 可以保護不同叢集上的 Vm。

## <a name="process-server"></a>處理序伺服器

### <a name="why-am-i-unable-to-select-the-process-server-when-i-enable-replication"></a>為什麼我在啟用複寫時無法選取進程伺服器？

當您啟用複寫時，9.24 版和更新版本中的更新現在[會顯示進程伺服器的健全狀況](vmware-azure-enable-replication.md#enable-replication)。 這項功能有助於避免進程伺服器節流，並將狀況不良的進程伺服器的使用降到最低。

### <a name="how-do-i-update-the-process-server-to-version-924-or-later-for-accurate-health-information"></a>如何? 將進程伺服器更新至9.24 版或更新版本，以取得正確的健全狀況資訊嗎？

從[9.24 版](service-updates-how-to.md#links-to-currently-supported-update-rollups)開始，已新增更多警示來指出進程伺服器的健全狀況。 將[您的 Site Recovery 元件更新為9.24 版或更新版本](service-updates-how-to.md#links-to-currently-supported-update-rollups)，以產生所有警示。

## <a name="failover-and-failback"></a>容錯移轉和容錯回復

### <a name="can-i-use-the-on-premises-process-server-for-failback"></a>我可以使用內部部署進程伺服器來進行容錯回復嗎？

我們強烈建議您在 Azure 中基於容錯回復目的建立進程伺服器，以避免資料傳輸延遲。 此外，如果您將來源 Vm 網路與設定伺服器中的 Azure 面向網路隔開，則必須使用在 Azure 中建立的進程伺服器進行容錯回復。

### <a name="can-i-keep-the-ip-address-on-failover"></a>我可以在容錯移轉時保留 IP 位址嗎？

是，您可以在容錯移轉時保留 IP 位址。 請確定您在容錯移轉之前，在 VM 的 [**計算和網路**] 設定中指定目標 IP 位址。 此外，在容錯移轉時將機器關機，以避免在容錯回復期間發生 IP 位址衝突。

### <a name="can-i-change-the-target-vm-size-or-vm-type-before-failover"></a>我可以在容錯移轉前變更目標 VM 大小或 VM 類型嗎？

是，您可以在容錯移轉之前隨時變更 VM 的類型或大小。 在入口網站中，使用複寫 VM 的 [**計算和網路**] 設定。

### <a name="how-far-back-can-i-recover"></a>最多可復原至多久之前？

針對 VMware 到 Azure，您可以使用的最舊復原點為72小時。

### <a name="how-do-i-access-azure-vms-after-failover"></a>在容錯移轉之後如何存取 Azure VM？

容錯移轉之後，您可以透過安全的網際網路連線、透過站對站 VPN 或透過 Azure ExpressRoute 存取 Azure Vm。 若要連接，您必須準備幾個專案。 [詳細資訊](site-recovery-test-failover-to-azure.md#prepare-to-connect-to-azure-vms-after-failover)。

### <a name="is-failed-over-data-resilient"></a>容錯回復資料是否可復原？

Azure 是針對復原能力而設計的。 Site Recovery 是根據 Azure 服務等級協定（SLA）的要求，進行容錯移轉至次要 Azure 資料中心的程式設計。 發生容錯移轉時，我們會確保您的中繼資料和保存庫會保留在您為保存庫選擇的相同地理區域中。

### <a name="is-failover-automatic"></a>容錯移轉是自動發生的嗎？

[容錯移轉](site-recovery-failover.md)不會自動執行。 您可以在入口網站中進行單一選擇，或使用[PowerShell](/powershell/module/az.recoveryservices)來觸發容錯移轉，以開始容錯移轉。

### <a name="can-i-fail-back-to-a-different-location"></a>是否可容錯回復至不同的位置？

是。 如果您已容錯回復至 Azure，如果原始的位置無法使用，您可以容錯回復至不同的位置。 [詳細資訊](concepts-types-of-failback.md#alternate-location-recovery-alr)。

### <a name="why-do-i-need-a-vpn-or-expressroute-with-private-peering-to-fail-back"></a>為什麼我需要具有私人對等互連的 VPN 或 ExpressRoute 才能容錯回復？

當您從 Azure 容錯回復時，來自 Azure 的資料會複製回您的內部部署 VM，而且需要私用存取。


## <a name="automation-and-scripting"></a>自動化和指令碼

### <a name="can-i-set-up-replication-with-scripting"></a>是否可以使用指令碼設定複寫？

是。 您可以使用 Rest API、PowerShell 或 Azure SDK，將 Site Recovery 工作流程自動化。 [詳細資訊](vmware-azure-disaster-recovery-powershell.md)。

## <a name="performance-and-capacity"></a>效能與容量

### <a name="can-i-throttle-replication-bandwidth"></a>是否可進行複寫頻寬節流？

是。 [詳細資訊](site-recovery-plan-capacity-vmware.md)。

## <a name="next-steps"></a>後續步驟

- [檢閱](vmware-physical-azure-support-matrix.md)支援需求。
- [設定](vmware-azure-tutorial.md) VMware 到 Azure 的複寫。
