---
title: 有關使用 Azure 網站恢復進行 VMware 災難恢復的常見問題
description: 使用 Azure 網站恢復獲取有關本地 VMware VM 災難恢復的常見問題的解答。
ms.date: 11/14/2019
ms.topic: conceptual
ms.openlocfilehash: d551cef7037c0b6d7286cbb4b70d8f7a8f7f5cae
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/13/2020
ms.locfileid: "81259505"
---
# <a name="common-questions-about-vmware-to-azure-replication"></a>VMware 到 Azure 的複寫常見問題

本文回答了將本地 VMware 虛擬機器 (VM) 的災難恢復部署到 Azure 時可能出現的常見問題。

## <a name="general"></a>一般

### <a name="what-do-i-need-for-vmware-vm-disaster-recovery"></a>VMware VM 災難恢復需要什麼?

瞭解 VMware VM 災難復原[所涉及的元件](vmware-azure-architecture.md)。

### <a name="can-i-use-site-recovery-to-migrate-vmware-vms-to-azure"></a>我可以使用網站恢復將 VMware VM 遷移到 Azure 嗎?

是。 除了使用網站恢復為 VMware VM 設置完整的災難恢復外,還可以使用網站恢復將本地 VMware VM 遷移到 Azure。 在這種情況下,您將本地 VMware VM 複製到 Azure 儲存。 然後，您會從內部部署環境容錯移轉至 Azure。 容錯移轉之後，您的應用程式和工作負載將可在 Azure VM 上使用及執行。 此過程類似於設置完整的災難恢復,只不過在遷移中,不能從 Azure 失敗。

### <a name="does-my-azure-account-need-permissions-to-create-vms"></a>我的 Azure 帳戶是否需要建立 VM 的權限？

如果您是訂用帳戶管理員，則會具有所需的複寫權限。 如果您不是管理員,則需要執行以下操作的權限:

- 在配置站台恢復時指定的資源組和虛擬網路中創建 Azure VM。
- 根據您的配置寫入選定的存儲帳戶或託管磁碟。

[詳細瞭解](site-recovery-role-based-linked-access-control.md#permissions-required-to-enable-replication-for-new-virtual-machines)所需許可權。

### <a name="what-applications-can-i-replicate"></a>可以複寫哪些應用程式？

您可以複製在 VMware VM 上執行的任何符合[複製要求](vmware-physical-azure-support-matrix.md#replicated-machines)的應用或工作負載。

- 站點恢復支援應用程式感知複製,以便應用可以失敗並失敗回智慧狀態。
- 網站恢復與 Microsoft 應用程式(如 SharePoint、Exchange、動態、SQL Server 和活動目錄)集成。 它還與領先的供應商密切合作,包括 Oracle、SAP、IBM 和紅帽。

[深入了解](site-recovery-workload.md) 工作負載保護。

### <a name="can-i-use-a-guest-os-server-license-on-azure"></a>我可以在 Azure 上使用來賓 OS 伺服器許可證嗎?

是，「微軟軟體保證」客戶可以使用 [Azure Hybrid Benefit](https://azure.microsoft.com/pricing/hybrid-benefit/)，節省遷移至 Azure 之 Windows Server 電腦的授權成本，或使用 Azure 進行災害復原。

## <a name="security"></a>安全性

### <a name="what-access-to-vmware-servers-does-site-recovery-need"></a>站點恢復需要訪問 VMware 伺服器?

Site Recovery 需要存取 VMware 伺服器才能：

- 設置運行網站恢復配置伺服器的 VMware VM。
- 自動探索需要複寫的 VM。

### <a name="what-access-to-vmware-vms-does-site-recovery-need"></a>網站恢復需要存取 VMware VM 的哪些訪問許可權?

- 要複製,VMware VM 必須安裝並運行網站恢復行動服務。 您可以手動部署該工具,也可以指定網站恢復在為 VM 啟用複製時執行服務的推送安裝。
- 在複寫期間，VM 會與 Site Recovery 通訊，如下所示：
    - VM 與 HTTPS 埠 443 上的設定伺服器通訊,以便進行複製管理。
    - VM 將複製資料發送到 HTTPS 連接埠 9443 上的程序伺服器。 (可以修改此設置。
    - 如果您啟用多部 VM 一致性，則 VM 會透過連接埠 20004 互相通訊。

### <a name="is-replication-data-sent-to-site-recovery"></a>複寫資料是否會傳送至 Site Recovery？

否,網站恢復不會截獲複製的數據,並且沒有關於 VM 上運行的內容的任何資訊。 在 VMware 虛擬機器管理程式和 Azure 儲存之間交換複製數據。 Site Recovery 並不具有攔截該資料的能力。 只有協調複寫與容錯移轉所需的中繼資料會被傳送給 Site Recovery 服務。

網站恢復認證為 ISO 27001:2013 和 27018、HIPAA 和 DPA。 它正在進行中SOC2和FedRAMP JAB評估。

## <a name="pricing"></a>定價

### <a name="how-do-i-calculate-approximate-charges-for-vmware-disaster-recovery"></a>如何計算 VMware 災難恢復的大致費用?

使用[定價計算機](https://aka.ms/asr_pricing_calculator)在使用網站恢復時估計成本。

有關成本的詳細估計,請使用[VMware](https://aka.ms/siterecovery_deployment_planner)的部署計畫工具並使用[成本估算報告](https://aka.ms/asr_DP_costreport)。

### <a name="is-there-any-difference-in-cost-between-replicating-to-storage-or-directly-to-managed-disks"></a>複製到存儲或直接複製到託管磁碟之間的成本是否有差異?

託管磁碟的收費與存儲帳戶略有不同。 [瞭解有關](https://azure.microsoft.com/pricing/details/managed-disks/)託管磁碟定價的更多詳細資訊。

### <a name="is-there-any-difference-in-cost-when-replicating-to-general-purpose-v2-storage-account"></a>複製到通用 v2 儲存帳戶時,成本是否有任何差異?

由於 Azure 網站恢復事務繁重,因此通常會看到 GPv2 存儲帳戶上發生的事務成本增加。 [閱讀更多](../storage/common/storage-account-upgrade.md#pricing-and-billing)以估計更改。

## <a name="mobility-service"></a>行動服務

### <a name="where-can-i-find-the-mobility-service-installers"></a>哪裡可以找到行動服務安裝程式？

安裝程式位於設定伺服器上的 %程式資料%\ASR_home_pushsasr_儲存庫資料夾中。

## <a name="how-do-i-install-the-mobility-service"></a>如何安裝行動服務？

在要複製的每個 VM 上,請透過以下幾種方法之一安裝服務:

- [推送安裝](vmware-physical-mobility-service-overview.md#push-installation)
- 從 UI 或 PowerShell[手動安裝](vmware-physical-mobility-service-overview.md#install-the-mobility-service-using-ui)
- 使用部署工具(如[設定管理員](vmware-azure-mobility-install-configuration-mgr.md))進行部署

## <a name="managed-disks"></a>受控磁碟

### <a name="where-does-site-recovery-replicate-data-to"></a>網站恢復將數據複製到何處?

網站恢復將本地 VMware VM 和實體伺服器複製到 Azure 中的託管磁碟。

- 網站恢復進程伺服器將複製日誌寫入目標區域中的快取存儲帳戶。
- 這些日誌用於在具有**asrseeddisk**的前置碼的 Azure 託管磁碟上創建復原點。
- 發生故障轉移時,您選擇的恢復點用於創建新的目標託管磁碟。 此託管磁碟附加到 Azure 中的 VM。
- 以前複製到存儲帳戶的 VM(2019 年 3 月之前)不受影響。

### <a name="can-i-replicate-new-machines-to-storage-accounts"></a>我可以將新計算機複製到存儲帳戶嗎?

否。 從 2019 年 3 月開始,在 Azure 門戶中,只能複製到 Azure 託管磁碟。

只有使用 PowerShell 或 REST API(版本 2018-01-10 或 2016-08-10)才能將新 VM 複製到存儲帳戶。

### <a name="what-are-the-benefits-of-replicating-to-managed-disks"></a>複製到託管磁碟有什麼好處?

[瞭解如何](https://azure.microsoft.com/blog/simplify-disaster-recovery-with-managed-disks-for-vmware-and-physical-servers/)站點恢復通過託管磁碟簡化了災難恢復。

### <a name="can-i-change-the-managed-disk-type-after-a-machine-is-protected"></a>在計算機受到保護后,是否可以更改託管磁碟類型?

可以,您可以輕鬆地[更改託管磁碟的類型](https://docs.microsoft.com/azure/virtual-machines/windows/convert-disk-storage),以便進行複製。 變更類型之前,請確保在託管磁碟上未產生共享存取簽章網址:

1. 跳到 Azure 門戶上的**託管磁碟**資源,並檢查 **「概述」** 邊欄選項卡上是否有共用訪問簽名 URL 橫幅。
1. 如果存在橫幅,請選擇它以取消正在進行的匯出。
1. 在接下來的幾分鐘內更改磁碟的類型。 如果更改託管磁碟類型,請等待 Azure 網站恢復生成新的恢復點。
1. 將來對任何測試故障轉移或故障轉移使用新的恢復點。

### <a name="can-i-switch-replication-from-managed-disks-to-unmanaged-disks"></a>是否可以將複製從託管磁片切換到非託管磁碟?

否。 不支援從託管切換到非託管。

## <a name="replication"></a>複寫

### <a name="what-are-the-replicated-vm-requirements"></a>複寫的 VM 有何需求？

[詳細瞭解](vmware-physical-azure-support-matrix.md#replicated-machines)VMware VM 和物理伺服器的支援要求。

### <a name="how-often-can-i-replicate-to-azure"></a>複寫到 Azure 的頻率為何？

從 VMware VM 到 Azure 的複寫會持續執行。

### <a name="can-i-extend-replication"></a>我可以延伸複寫嗎？

不支援延伸的或鏈結的複寫。 請在 [意見反應論壇](https://feedback.azure.com/forums/256299-site-recovery/suggestions/6097959)中提出這項功能的要求。

### <a name="can-i-do-an-offline-initial-replication"></a>是否可執行離線初始複寫？

不支持離線複製。 請在 [意見反應論壇](https://feedback.azure.com/forums/256299-site-recovery/suggestions/6227386-support-for-offline-replication-data-transfer-from)中提出這項功能的要求。

### <a name="what-is-asrseeddisk"></a>什麼是阿斯塞德磁盤?

對於每個源磁碟,數據都會複製到 Azure 中的託管磁碟。 此磁碟具有**asrseeddisk**的前置碼。 它存儲源磁碟的副本和所有恢復點快照。

### <a name="can-i-exclude-disks-from-replication"></a>我可以從複製中排除磁碟嗎?

可以,您可以排除磁碟。

### <a name="can-i-replicate-vms-that-have-dynamic-disks"></a>我可以複製具有動態磁碟的 VM 嗎?

動態磁碟可以複寫。 作業系統磁碟必須是基本磁碟。

### <a name="if-i-use-replication-groups-for-multi-vm-consistency-can-i-add-a-new-vm-to-an-existing-replication-group"></a>如果我使用複寫群組以獲取多 VM 一致性，我是否可將新的 VM 新增至現有的複寫群組？

是，您可以將新的 VM 新增至現有的複寫群組，為這些 VM 啟用複寫。 但是：

- 複製開始後,無法將 VM 添加到現有複製組。
- 不能為現有 VM 創建複製組。

### <a name="can-i-modify-vms-that-are-replicating-by-adding-or-resizing-disks"></a>我是否可藉由新增磁碟或調整其大小來修改要複寫的 VM？

對於 VMware 複製到 Azure,可以修改源 VM 的磁碟大小。 如果要添加新磁碟,則必須添加磁碟並重新啟用 VM 的保護。

### <a name="can-i-migrate-on-premises-machines-to-a-new-vcenter-server-without-impacting-ongoing-replication"></a>是否可以在不影響正在進行的複製的情況下將本地電腦移到新的 vCenter 伺服器?

否。 VMware Vcenter 或遷移的更改將影響正在進行的複製。 使用新的 vCenter 伺服器設置網站恢復,並再次為電腦啟用複製。

### <a name="can-i-replicate-to-a-cache-or-target-storage-account-that-has-a-virtual-network-with-azure-firewalls-configured-on-it"></a>是否可以複製到配置了虛擬網路(包含 Azure 防火牆)的緩存或目標存儲帳戶?

否,網站恢復不支援在虛擬網路上複製到 Azure 存儲。

## <a name="component-upgrade"></a>元件升級

### <a name="my-version-of-the-mobility-services-agent-or-configuration-server-is-old-and-my-upgrade-failed-what-do-i-do"></a>我的行動服務代理或配置伺服器版本已舊,升級失敗。 該怎麼辦？

網站恢復遵循 N-4 支援模型。 [詳細瞭解](https://aka.ms/asr_support_statement)如何從非常舊的版本升級。

### <a name="where-can-i-find-the-release-notes-and-update-rollups-for-azure-site-recovery"></a>在哪裡可以找到 Azure 網站恢復的發行說明和更新匯總?

[瞭解新的更新](site-recovery-whats-new.md),並[取得匯總資訊](service-updates-how-to.md)。

### <a name="where-can-i-find-upgrade-information-for-disaster-recovery-to-azure"></a>在哪裡可以找到災難恢復到 Azure 的升級資訊?

[瞭解升級](https://aka.ms/asr_vmware_upgrades)。

## <a name="do-i-need-to-reboot-source-machines-for-each-upgrade"></a>我是否需要重新啟動每次升級的源計算機?

建議重新啟動,但不強制每次升級。 [深入了解](https://aka.ms/asr_vmware_upgrades)。

## <a name="configuration-server"></a>組態伺服器

### <a name="what-does-the-configuration-server-do"></a>組態伺服器有何功用？

組態伺服器會執行內部部署 Site Recovery 元件，包括：

- 配置伺服器本身。 伺服器協調本地元件和 Azure 之間的通訊,並管理數據複製。
- 進程伺服器,充當複製閘道。 此伺服器:
    1. 接收複寫資料。
    2. 運用快取、壓縮和加密將該資料最佳化。
    3. 將數據發送到 Azure 儲存。
  進程伺服器還會在 VM 上推送行動服務,並自動發現本地 VMware VM。
- 主目標伺服器,在 Azure 故障倒回期間處理複製數據。

[深入了解](vmware-azure-architecture.md)組態伺服器元件和程序。

### <a name="where-do-i-set-up-the-configuration-server"></a>我應在何處設定組態伺服器？

配置伺服器需要單個、高度可用的本地 VMware VM。 對於物理伺服器災難恢復,請在物理電腦上安裝配置伺服器。

### <a name="what-do-i-need-for-the-configuration-server"></a>配置伺服器需要什麼?

請檢閱[必要條件](vmware-azure-deploy-configuration-server.md#prerequisites)。

### <a name="can-i-manually-set-up-the-configuration-server-instead-of-using-a-template"></a>是否可以手動設定組態伺服器，而不要使用範本？

我們建議您使用最新版本的開放虛擬化格式 (OVF) 樣本[創建配置伺服器 VM。](vmware-azure-deploy-configuration-server.md) 如果無法使用範本(例如,如果您無法存取 VMware 伺服器),請從門戶[下載](physical-azure-set-up-source.md)安裝程式檔並設置設定伺服器。

### <a name="can-a-configuration-server-replicate-to-more-than-one-region"></a>組態伺服器是否可複寫到多個區域？

否。 要複製到多個區域,需要每個區域中的配置伺服器。

### <a name="can-i-host-a-configuration-server-in-azure"></a>是否可將組態伺服器裝載在 Azure 中？

儘管可以,但運行配置伺服器的 Azure VM 需要與本地 VMware 基礎結構和 VM 進行通訊。 此通信會增加延遲並影響正在進行的複製。

### <a name="how-do-i-update-the-configuration-server"></a>如何更新組態伺服器？

[瞭解如何](vmware-azure-manage-configuration-server.md#upgrade-the-configuration-server)更新配置伺服器。

- 您可以在[Azure 更新頁上](https://azure.microsoft.com/updates/?product=site-recovery)找到最新的更新資訊。
- 可以從門戶下載最新版本。 或者,您可以直接從[Microsoft 下載中心](https://aka.ms/asrconfigurationserver)下載最新版本的配置伺服器。
- 如果您的版本比當前版本大四個版本,請參閱[支援語句](https://aka.ms/asr_support_statement)以進行升級指導。

### <a name="should-i-back-up-the-configuration-server"></a>我應該備份配置伺服器嗎?

建議對於組態伺服器進行定期排程的備份。

- 要成功進行故障倒退,配置伺服器資料庫中必須存在失敗回的 VM。
- 配置伺服器必須處於運行狀態。
- [瞭解有關](vmware-azure-manage-configuration-server.md)常見配置伺服器管理任務的更多資訊。

### <a name="when-im-setting-up-the-configuration-server-can-i-download-and-install-mysql-manually"></a>在設定組態伺服器時，是否可以手動下載並安裝 MySQL？

是。 請下載 MySQL 並將它放在 C:\Temp\ASRSetup 資料夾中。 然後,手動安裝它。 當您設定組態伺服器 VM 時，如果您接受條款，MySQL 在 [下載並安裝]**** 中就會列示為 [已安裝]****。

### <a name="can-i-avoid-downloading-mysql-but-let-site-recovery-install-it"></a>是否可以不要下載 MySQL 而讓 Site Recovery 加以安裝？

是。 請下載 MySQL 安裝程式，並將它放在 C:\Temp\ASRSetup 資料夾中。 設置設定伺服器 VM 時,請接受這些條款並選擇 **「下載並安裝**」 。 門戶將使用您添加的安裝程式來安裝 MySQL。

### <a name="can-i-use-the-configuration-server-vm-for-anything-else"></a>組態伺服器 VM 是否可用於任何其他位置？

否。 僅對配置伺服器使用 VM。

### <a name="can-i-clone-a-configuration-server-and-use-it-for-orchestration"></a>是否可以複製組態伺服器並將它用於協調流程？

否。 設置新的配置伺服器以避免註冊問題。

### <a name="can-i-change-the-vault-in-which-the-configuration-server-is-registered"></a>我可以更改註冊配置伺服器的保管庫嗎?

否。 保管庫與配置伺服器關聯后,無法更改它。 [瞭解如何](vmware-azure-manage-configuration-server.md#register-a-configuration-server-with-a-different-vault)使用其他保管庫註冊配置伺服器。

### <a name="can-i-use-the-same-configuration-server-for-disaster-recovery-of-both-vmware-vms-and-physical-servers"></a>是否可以使用相同的配置伺服器來恢復 VMware VM 和實體伺服器?

是,但請注意,物理計算機只能故障回 VMware VM。

### <a name="where-can-i-download-the-passphrase-for-the-configuration-server"></a>哪裡可以下載組態伺服器的複雜密碼？

[瞭解如何](vmware-azure-manage-configuration-server.md#generate-configuration-server-passphrase)下載密碼短語。

### <a name="where-can-i-download-vault-registration-keys"></a>哪裡可以下載保存庫註冊金鑰？

在回復服務保存庫中,在**站台修復基礎結構** > **管理**中選擇**設定伺服器**。 然後,在 **「伺服器」** 中,選擇**下載註冊金鑰**以下載保管庫憑據檔。

### <a name="can-a-single-configuration-server-be-used-to-protect-multiple-vcenter-instances"></a>是否可以使用單個配置伺服器來保護多個 vCenter 實例?

是的,單個配置伺服器可以保護跨多個 vCenter 的 VM。  可以添加到配置伺服器的 vCenter 實例數沒有限制,但單個配置伺服器可以保護的 VM 限制確實適用。

### <a name="can-a-single-configuration-server-protect-multiple-clusters-within-vcenter"></a>單個配置伺服器能否保護 vCenter 中的多個群集?

是的,Azure 網站恢復可以保護不同群集中的 VM。

## <a name="process-server"></a>處理序伺服器

### <a name="why-am-i-unable-to-select-the-process-server-when-i-enable-replication"></a>為什麼在啟用複製時無法選擇進程伺服器?

版本 9.24 和更高版本的更新現在顯示[進程伺服器的運行狀況,當您啟用複製](vmware-azure-enable-replication.md#enable-replication)時。 此功能有助於避免進程伺服器限制,並最大限度地減少不正常的進程伺服器的使用。

### <a name="how-do-i-update-the-process-server-to-version-924-or-later-for-accurate-health-information"></a>如何將進程伺服器更新到版本 9.24 或更高版本,以獲得準確的運行狀況資訊?

從[版本9.24](service-updates-how-to.md#links-to-currently-supported-update-rollups)開始,添加了更多警報以指示進程伺服器的運行狀況。 [將網站恢復元件更新到版本 9.24 或更高版本](service-updates-how-to.md#links-to-currently-supported-update-rollups),以便生成所有警報。

### <a name="how-can-i-ensure-high-availability-of-the-process-server"></a>如何確保進程伺服器的高可用性?

通過配置多個進程伺服器,該設計提供了將受保護的計算機從不正常的進程伺服器移動到工作進程伺服器的靈活性。 必須透過此處定義的步驟顯式/手動啟動電腦從一個程序伺服器到另一個進程伺服器的[移動:在程序伺服器之間移動 VM。](vmware-azure-manage-process-server.md#move-vms-to-balance-the-process-server-load)

## <a name="failover-and-failback"></a>容錯移轉和容錯回復

### <a name="can-i-use-the-on-premises-process-server-for-failback"></a>我可以使用本地進程伺服器進行故障倒機嗎?

我們強烈建議在 Azure 中創建進程伺服器以進行故障倒機,以避免數據傳輸延遲。 此外,如果配置伺服器中源 VM 網路與面向 Azure 的網路分開,必須使用 Azure 中創建的進程伺服器進行故障倒正。

### <a name="can-i-keep-the-ip-address-on-failover"></a>我可以在故障轉移時保留 IP 位址嗎?

可以,您可以將 IP 位址保留在故障轉移上。 請確保在故障轉移之前在 VM**的計算和網路**設定中指定目標 IP 位址。 此外,在故障轉移時關閉計算機,以避免故障恢復期間的IP位址衝突。

### <a name="can-i-change-the-target-vm-size-or-vm-type-before-failover"></a>是否可以在故障轉移之前更改目標 VM 大小或 VM 類型?

可以,您可以在故障轉移前的任何時間更改 VM 的類型或大小。 在門戶中,對複製的 VM 使用**計算和網路**設置。

### <a name="how-far-back-can-i-recover"></a>最多可復原至多久之前？

對於 VMware 到 Azure,可以使用的最舊恢復點是 72 小時。

### <a name="how-do-i-access-azure-vms-after-failover"></a>在容錯移轉之後如何存取 Azure VM？

故障轉移後,您可以通過安全互聯網連接、網站到網站 VPN 或 Azure ExpressRoute 訪問 Azure VM。 要連接,您必須準備幾件事情。 [深入了解](site-recovery-test-failover-to-azure.md#prepare-to-connect-to-azure-vms-after-failover)。

### <a name="is-failed-over-data-resilient"></a>故障過復數據是否具有彈性?

Azure 是針對復原能力而設計的。 站點恢復設計用於故障轉移到輔助 Azure 資料中心,這是 Azure 服務級別協定 (SLA) 的要求。 發生故障轉移時,我們會確保您的元數據和保管庫保留在您為保管庫選擇的同一地理區域中。

### <a name="is-failover-automatic"></a>容錯移轉是自動發生的嗎？

[故障轉移](site-recovery-failover.md)不是自動的。 通過在門戶中進行單個選擇來啟動故障轉移,或者可以使用[PowerShell](/powershell/module/az.recoveryservices)觸發故障轉移。

### <a name="can-i-fail-back-to-a-different-location"></a>是否可容錯回復至不同的位置？

是。 如果故障轉移到 Azure,則如果原始位置不可用,則可以故障回其他位置。 [深入了解](concepts-types-of-failback.md#alternate-location-recovery-alr)。

### <a name="why-do-i-need-a-vpn-or-expressroute-with-private-peering-to-fail-back"></a>為什麼我需要 VPN 或帶有私有對等互連的 ExpressRoute 才能故障回?

從 Azure 故障返回時,Azure 的數據將複製回本地 VM,並且需要私有訪問。


## <a name="automation-and-scripting"></a>自動化和指令碼

### <a name="can-i-set-up-replication-with-scripting"></a>是否可以使用指令碼設定複寫？

是。 您可以使用休息 API、PowerShell 或 Azure SDK 自動執行網站恢復工作流。 [深入了解](vmware-azure-disaster-recovery-powershell.md)。

## <a name="performance-and-capacity"></a>效能與容量

### <a name="can-i-throttle-replication-bandwidth"></a>是否可進行複寫頻寬節流？

是。 [深入了解](site-recovery-plan-capacity-vmware.md)。

## <a name="next-steps"></a>後續步驟

- [檢閱](vmware-physical-azure-support-matrix.md)支援需求。
- [設定](vmware-azure-tutorial.md) VMware 到 Azure 的複寫。
