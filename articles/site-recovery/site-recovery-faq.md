---
title: 有關 Azure 網站恢復服務的一般問題
description: 本文討論了有關 Azure 網站恢復的常見問題。
ms.topic: conceptual
ms.date: 1/24/2020
ms.author: raynew
ms.openlocfilehash: a9d0ae4a6e60a72bbb1148aca1a75c44506b2e9e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79257676"
---
# <a name="general-questions-about-azure-site-recovery"></a>有關 Azure 網站恢復的一般問題

本文總結了有關 Azure 網站恢復的常見問題。 對於特定方案，請查看這些文章

- [有關 Azure VM 災害復原到 Azure 的問題](azure-to-azure-common-questions.md)
- [有關 VMware VM 災害復原到 Azure 的問題](vmware-azure-common-questions.md)
- [有關超級 V VM 災害復原到 Azure 的問題](hyper-v-azure-common-questions.md)
 
## <a name="general"></a>一般

### <a name="what-does-site-recovery-do"></a>Site Recovery 的功能是什麼？
Site Recovery 可協調並自動執行區域、內部部署虛擬機器和實體伺服器之間至 Azure 的 Azure VM 複寫；協調並自動執行內部部署機器至次要資料中心的複寫，藉此保障您的商務持續性與災害復原 (BCDR) 策略。 [深入了解](site-recovery-overview.md)。

### <a name="can-i-protect-a-virtual-machine-that-has-a-docker-disk"></a>我可以保護具有 Docker 磁片的虛擬機器嗎？

否，這是不支援的案例。

## <a name="service-providers"></a>服務提供者

### <a name="im-a-service-provider-does-site-recovery-work-for-dedicated-and-shared-infrastructure-models"></a>我是服務提供者。 Site Recovery 是否適用於專用和共用基礎結構模型？
是，Site Recovery 同時支援專用與共用的基礎結構模型。

### <a name="for-a-service-provider-is-the-identity-of-my-tenant-shared-with-the-site-recovery-service"></a>對服務提供者而言，租用戶的身分識別是否會與 Site Recovery 服務共用？
否。 租用戶身分識別會保持匿名。 您的租用戶不需要存取 Site Recovery 入口網站。 只有服務提供者系統管理員會與入口網站互動。

### <a name="will-tenant-application-data-ever-go-to-azure"></a>租用戶應用程式資料是否會傳送到 Azure？
在服務提供者擁有的站台之間進行複寫時，永遠不會將應用程式資料傳送到 Azure。 資料會在傳輸中加密，並且會在服務提供者站台之間直接進行複寫。

如果您是複寫至 Azure，應用程式資料就會傳送到 Azure 儲存體而不是 Site Recovery 服務。 資料會在傳輸中加密並在 Azure 中繼續維持加密狀態。

### <a name="will-my-tenants-receive-a-bill-for-any-azure-services"></a>我的租用戶會收到來自 Azure 服務的帳單嗎？
否。 Azure 的計費關係是直接與服務提供者相關。 服務提供者負責為其租用戶產生特定的帳單。

### <a name="if-im-replicating-to-azure-do-we-need-to-run-virtual-machines-in-azure-at-all-times"></a>如果我複寫至 Azure，我們需要在 Azure 中隨時執行虛擬機器嗎？
否，資料將複製到訂閱中的 Azure 存儲。 當您執行測試容錯移轉 (DR 演練) 或實際容錯移轉時，Site Recovery 會自動在您的訂用帳戶中建立虛擬機器。

### <a name="do-you-ensure-tenant-level-isolation-when-i-replicate-to-azure"></a>當我複寫至 Azure 時，您會確保提供租用戶層級的隔離嗎？
是。

### <a name="what-platforms-do-you-currently-support"></a>目前支援哪些平台？
我們支援「Azure 套件」、「雲端平台系統」及 System Center 架構 (2012 及更新版本) 的部署。 [深入了解](https://technet.microsoft.com/library/dn850370.aspx) 「Azure 套件」和 Site Recovery 整合

### <a name="do-you-support-single-azure-pack-and-single-vmm-server-deployments"></a>您支援單一 Azure 套件與單一 VMM 伺服器部署嗎？
是，您可以將 Hyper-V 虛擬機器複寫至 Azure，或是在服務提供者網站之間進行複寫。  請注意，如果您是在服務提供者站台之間進行複寫，將無法使用 Azure Runbook 整合。

## <a name="pricing"></a>定價

### <a name="where-can-i-find-pricing-information"></a>我在哪裡可以找到定價資訊？
查看[網站恢復定價](https://azure.microsoft.com/pricing/details/site-recovery/)詳細資訊。


### <a name="how-can-i-calculate-approximate-charges-during-the-use-of-site-recovery"></a>如何計算使用 Site Recovery 期間的大約費用？

您可以使用[定價計算機](https://aka.ms/asr_pricing_calculator)在使用網站恢復時估計成本。

有關成本的詳細估計，請使用[VMware](https://aka.ms/siterecovery_deployment_planner)或[Hyper-V](https://aka.ms/asr-deployment-planner)的部署計畫工具，並使用[成本估算報告](https://aka.ms/asr_DP_costreport)。


### <a name="managed-disks-are-now-used-to-replicate-vmware-vms-and-physical-servers-do-i-incur-additional-charges-for-the-cache-storage-account-with-managed-disks"></a>託管磁片現在用於複製 VMware VM 和物理伺服器。 是否對具有託管磁片的緩存存儲帳戶產生額外費用？

否，緩存不收取額外費用。 複製到標準存儲帳戶時，此緩存存儲是同一目標存儲帳戶的一部分。

### <a name="i-have-been-an-azure-site-recovery-user-for-over-a-month-do-i-still-get-the-first-31-days-free-for-every-protected-instance"></a>我已經使用 Azure Site Recovery 一個多月了。 我的每個受保護的執行個體是否前 31 天仍然都免費？

是。 每個受保護的執行個體在前 31 天皆無 Azure Site Recovery 費用。 例如，如果您在過去 6 個月內保護了 10 個實例，並將第 11 個實例連接到 Azure 網站恢復，則前 31 天第 11 個實例不收取任何費用。 前 10 個實例繼續產生 Azure 網站恢復費用，因為它們已保護超過 31 天。

### <a name="during-the-first-31-days-will-i-incur-any-other-azure-charges"></a>在這前 31 天，我是否須負擔任何其他 Azure 費用？

可以，即使網站恢復在受保護實例的前 31 天是免費的，您也可能對 Azure 存儲、存儲事務和資料傳輸產生費用。 復原的虛擬機器也可能會產生 Azure 計算費用。


### <a name="is-there-a-cost-associated-to-perform-disaster-recovery-drillstest-failover"></a>執行災害復原演練/測試容錯移轉是否相關成本？

DR 演練沒有個別的成本。 在測試容錯移轉後創建 VM 後，將收取計算費用。



## <a name="security"></a>安全性

### <a name="is-replication-data-sent-to-the-site-recovery-service"></a>複寫資料會傳送到 Site Recovery 服務嗎？
否，Site Recovery 不會攔截複寫的資料，也不會擁有任何關於您虛擬機器或實體伺服器上執行哪些項目的資訊。
內部部署 Hyper-V 主機、VMware Hypervisor 或實體伺服器，會與 Azure 儲存體或次要站台交換複寫資料。 Site Recovery 並不具有攔截該資料的能力。 只有協調複寫與容錯移轉所需的中繼資料會被傳送給 Site Recovery 服務。  

Site Recovery 已通過 ISO 27001:2013、27018、HIPAA、DPA 認證，並且正在進行 SOC2 和 FedRAMP JAB 評定程序。

### <a name="for-compliance-reasons-even-our-on-premises-metadata-must-remain-within-the-same-geographic-region-can-site-recovery-help-us"></a>為了遵循法規，甚至我們的內部部署中繼資料也必須保留在相同的地理區域內。 Site Recovery 可以幫助我們嗎？
是。 當您在某個區域中建立 Site Recovery 保存庫時，我們會確保我們啟用及協調複寫與容錯移轉時所需的一切中繼資料都會保留在該區域地理界限內。

### <a name="does-site-recovery-encrypt-replication"></a>Site Recovery 會將複寫加密嗎？
就虛擬機器和實體伺服器而言，在內部部署站台之間進行複寫時，支援傳輸中加密。 在將虛擬機器和實體伺服器複寫至 Azure 時，則同時支援傳輸中加密和[靜態加密 (在 Azure 中)](https://docs.microsoft.com/azure/storage/storage-service-encryption)。

### <a name="how-can-i-enforce-tls-12-on-all-on-premises-azure-site-recovery-components"></a>如何在所有本地 Azure 網站恢復元件上強制實施 TLS 1.2？
安裝在複製項上的移動代理僅在 TLS 1.2 上與進程伺服器通信。 但是，從佈建服務器到 Azure 以及從進程伺服器到 Azure 的通信可能位於 TLS 1.1 或 1.0 上。 請按照[指南](https://support.microsoft.com/en-us/help/3140245/update-to-enable-tls-1-1-and-tls-1-2-as-default-secure-protocols-in-wi)對您設置的所有佈建服務器和處理伺服器強制實施 TLS 1.2。


## <a name="disaster-recovery"></a>災害復原

### <a name="what-can-site-recovery-protect"></a>Site Recovery 可以保護什麼？
* **Azure VM**：Site Recovery 可複寫在支援的 Azure VM 上執行的任何工作負載。
* **Hyper-V 虛擬機器**：Site Recovery 可以保護 Hyper-V VM 上執行的任何工作負載。
* **實體伺服器**：Site Recovery 可以保護執行 Windows 或 Linux 的實體伺服器。
* **VMware 虛擬機器**：Site Recovery 可以保護 VMware VM 上執行的任何工作負載。

### <a name="what-workloads-can-i-protect-with-site-recovery"></a>我可以使用 Site Recovery 來保護哪些工作負載？
您可以使用 Site Recovery 來保護在支援的 VM 或實體伺服器上執行的大多數工作負載。 Site Recovery 支援應用程式感知複寫，可讓應用程式復原為智慧型狀態。 除了與 Microsoft 應用程式 (例如 SharePoint、Exchange、Dynamics、SQL Server 及 Active Directory) 整合之外，它還與產業龍頭 (包括 Oracle、SAP、IBM 及 Red Hat) 密切合作。 [深入了解](site-recovery-workload.md) 工作負載保護。

### <a name="can-i-manage-disaster-recovery-for-my-branch-offices-with-site-recovery"></a>我可以使用 Site Recovery 來管理分公司的災害復原嗎？
是。 當您使用 Site Recovery 來協調分公司中的複寫與容錯移轉時，會為您集中提供所有分公司工作負載的整合協調與檢視。 您不需要造訪分公司，就可以從總公司輕鬆執行所有分公司的容錯移轉及管理災害復原。


### <a name="is-disaster-recovery-supported-for-azure-vms"></a>Azure VM 是否支援災害復原？

是，網站恢復支援 Azure VM 在 Azure 區域之間的災難。 查看有關 Azure VM 災害復原[的常見問題](azure-to-azure-common-questions.md)。

### <a name="is-disaster-recovery-supported-for-vmware-vms"></a>VMware VM 是否支援災害復原？

是，網站恢復支援本地 VMware VM 的災害復原。 [查看](vmware-azure-common-questions.md)VMware VM 災害復原的常見問題。

### <a name="is-disaster-recovery-supported-for-hyper-v-vms"></a>對於超 VM，是否支援災害復原？
是，網站恢復支援本地超 V VM 的災害復原。 [查看](hyper-v-azure-common-questions.md)超 VM 災害復原的常見問題。

## <a name="is-disaster-recovery-supported-for-physical-servers"></a>物理伺服器是否支援災害復原？
是的，網站恢復支援將 Windows 和 Linux 運行到 Azure 或輔助網站的本地物理伺服器的災害復原。 瞭解對[Azure](vmware-physical-azure-support-matrix.md#replicated-machines)和[輔助網站](vmware-physical-secondary-support-matrix.md#replicated-vm-support)的災害復原要求。
請注意，物理伺服器在容錯移轉後將作為 Azure 中的 VM 運行。 當前不支援從 Azure 故障回本地物理伺服器。 您只能故障返回 VMware 虛擬機器。





## <a name="replication"></a>複寫

### <a name="can-i-replicate-over-a-site-to-site-vpn-to-azure"></a>我可以透過站對站 VPN 複寫至 Azure 嗎？
Azure 網站恢復將資料複製到 Azure 存儲帳戶或託管磁片，通過公共終結點。 複寫不是透過站對站 VPN。 

### <a name="why-cant-i-replicate-over-vpn"></a>為何我無法透過 VPN 進行複寫？

複製到 Azure 時，複製流量將到達 Azure 存儲的公共終結點。 因此，您只能通過公共互聯網或通過 ExpressRoute（微軟對等互連或現有公共對等互連）進行複製。

### <a name="can-i-use-riverbed-steelheads-for-replication"></a>我可以使用河床鋼頭進行複製嗎？

我們的合作夥伴 Riverbed 提供有關使用 Azure 網站恢復的詳細指南。 查看其[解決方案指南](https://community.riverbed.com/s/article/DOC-4627)。

### <a name="can-i-use-expressroute-to-replicate-virtual-machines-to-azure"></a>可以使用 ExpressRoute 將虛擬機器複寫到 Azure 嗎？
是的，[可以使用 ExpressRoute](concepts-expressroute-with-site-recovery.md) 將內部部署虛擬機器複寫至 Azure。

- Azure 網站恢復將資料複製到公共終結點上的 Azure 存儲。 您需要設置 Microsoft[對等互連](../expressroute/expressroute-circuit-peerings.md#microsoftpeering)或使用現有的[公共對等互連](../expressroute/about-public-peering.md)（用於新電路的棄用）才能使用 ExpressRoute 進行網站恢復複製。
- Microsoft 對等互連是建議用於複寫的路由網域。
- 私用對等互連不支援複寫。
- 如果要保護 VMware 電腦或物理電腦，請確保也滿足佈建服務器[的網路要求](vmware-azure-configuration-server-requirements.md#network-requirements)。 佈建服務器需要連接到特定 URL 才能進行網站恢復複製的編排。 無法將 ExpressRoute 用於此連接。
- 在虛擬機器容錯移轉到 Azure 虛擬網路之後，您可以 Azure 虛擬網路使用[私人對等互連](../expressroute/expressroute-circuit-peerings.md#privatepeering)安裝來存取這些虛擬機器。


### <a name="if-i-replicate-to-azure-what-kind-of-storage-account-or-managed-disk-do-i-need"></a>如果我複製到 Azure，我需要什麼樣的存儲帳戶或託管磁片？

您需要 LRS 或 GRS 存儲。 我們建議使用 GRS，以便在發生區域性停電或無法復原主要區域時，能夠恢復資料。 此帳戶必須位於與復原服務保存庫相同的區域中。 當您在 Azure 入口網站部署 Site Recovery 時，進階儲存體支援 VMware VM、Hyper-V VM 和實體伺服器複寫。 託管磁片僅支援 LRS。

### <a name="how-often-can-i-replicate-data"></a>我可以多久複寫一次資料？
* **Hyper-V：** Hyper-V VM 可以每 30 秒複製一次（高級存儲除外）、5 分鐘或 15 分鐘。
* **Azure VM、VMware VM、物理伺服器：** 此處與複製頻率無關。 複寫是連續的。

### <a name="can-i-extend-replication-from-existing-recovery-site-to-another-tertiary-site"></a>我可以將複寫從現有的復原網站延伸到另一個第三網站嗎？
不支援延伸的或鏈結的複寫。 請在 [意見反應論壇](https://feedback.azure.com/forums/256299-site-recovery/suggestions/6097959)中提出這項功能的要求。

### <a name="can-i-do-an-offline-replication-the-first-time-i-replicate-to-azure"></a>我可以在第一次複寫至 Azure 時進行離線複寫嗎？
不支援此做法。 請在 [意見反應論壇](https://feedback.azure.com/forums/256299-site-recovery/suggestions/6227386-support-for-offline-replication-data-transfer-from)中提出這項功能的要求。

### <a name="can-i-exclude-specific-disks-from-replication"></a>我可以從複寫中排除特定的磁碟嗎？
如果您使用 Azure 入口網站來將 VMware VM 和 Hyper-V VM 複寫至 Azure，就支援此做法。

### <a name="can-i-replicate-virtual-machines-with-dynamic-disks"></a>我可以使用動態磁碟來複寫虛擬機器嗎？
複製 Hyper-V 虛擬機器以及將 VMware VM 和物理電腦複製到 Azure 時，支援動態磁碟。 作業系統磁碟必須是基本磁碟。


### <a name="can-i-throttle-bandwidth-allotted-for-replication-traffic"></a>我可以限制為複製流量分配的頻寬嗎？
是。 您可以在以下文章中閱讀有關限制頻寬的更多內容：

* [適用於複寫 VMware VM 和實體伺服器的容量規劃](site-recovery-plan-capacity-vmware.md)
* [適用於將 Hyper-V VM 複寫至 Azure 的容量規劃](site-recovery-capacity-planning-for-hyper-v-replication.md)



## <a name="failover"></a>容錯移轉
### <a name="if-im-failing-over-to-azure-how-do-i-access-the-azure-vms-after-failover"></a>如果我容錯移轉到 Azure，如何在容錯移轉後訪問 Azure VM？

您可以透過安全的網際網路連線、透過站台對站台 VPN 或透過 Azure ExpressRoute 存取 Azure VM。 您需要準備一些內容才能連接。 [深入了解](site-recovery-test-failover-to-azure.md#prepare-to-connect-to-azure-vms-after-failover)。


### <a name="if-i-fail-over-to-azure-how-does-azure-make-sure-my-data-is-resilient"></a>如果我容錯移轉到 Azure，Azure 如何確定我的資料具有復原能力？
Azure 是針對復原能力而設計的。 網站恢復已設計為容錯移轉到輔助 Azure 資料中心，根據 Azure SLA。 如果發生這種情況，我們會確保您的中繼資料和保存庫都保留在您為保存庫選擇的相同地理區域中。  

### <a name="if-im-replicating-between-two-datacenters-what-happens-if-my-primary-datacenter-experiences-an-unexpected-outage"></a>如果我在兩個資料中心之間進行複寫，當我的主要資料中心發生意外中斷時，會發生什麼情況？
您可以從次要站台觸發非計劃性容錯移轉。 Site Recovery 不需要來自主要站台的連線即可執行容錯移轉。

### <a name="is-failover-automatic"></a>容錯移轉是自動發生的嗎？
容錯移轉並非自動發生。 在門戶中按一下即可啟動容錯移轉，或者可以使用[網站恢復 PowerShell](/powershell/module/az.recoveryservices)觸發容錯移轉。 容錯回復在 Site Recovery 入口網站中是一個簡單的動作。

若要進行自動化，您可以使用內部部署 Orchestrator 或 Operations Manager 來偵測虛擬機器失敗，然後使用 SDK 來觸發容錯移轉。

* [閱讀更多](site-recovery-create-recovery-plans.md) 復原方案的相關資訊。
* [深入了解](site-recovery-failover.md) 容錯移轉。
* [深入了解](site-recovery-failback-azure-to-vmware.md) 如何容錯回復 VMware VM 和實體伺服器

### <a name="if-my-on-premises-host-is-not-responding-or-crashed-can-i-fail-back-to-a-different-host"></a>如果我的本地主機沒有回應或崩潰，我是否可以容錯回復到其他主機？
是，您可以使用替代位置復原從 Azure 針對不同的主機進行容錯回復。

* [針對 VMware 虛擬機器](concepts-types-of-failback.md#alternate-location-recovery-alr)
* [針對 Hyper-V 虛擬機器](hyper-v-azure-failback.md#fail-back-to-an-alternate-location)

## <a name="automation"></a>自動化

### <a name="can-i-automate-site-recovery-scenarios-with-an-sdk"></a>我是否可以透過 SDK 自動化 Site Recovery 案例？
是。 您可以使用 Rest API、PowerShell 或 Azure SDK 將 Site Recovery 的工作流程自動化。 針對使用 PowerShell 來部署 Site Recovery，目前支援的案例包括︰

* [將 VMM 雲端中的 Hyper-V VM 複寫至 Azure PowerShell Resource Manager](hyper-v-vmm-powershell-resource-manager.md)
* [將不使用 VMM 的 Hyper-V VM 複寫至 Azure PowerShell Resource Manager](hyper-v-azure-powershell-resource-manager.md)
* [使用 PowerShell Resource Manager 將 VMware 複寫至 Azure](vmware-azure-disaster-recovery-powershell.md)

## <a name="componentprovider-upgrade"></a>元件/提供程式升級

### <a name="where-can-i-find-the-release-notesupdate-rollups-of-site-recovery-upgrades"></a>在哪裡可以找到網站恢復升級的版本資訊/更新彙總套件

[瞭解](site-recovery-whats-new.md)新更新，並[獲取匯總資訊](service-updates-how-to.md)。

## <a name="next-steps"></a>後續步驟
* 參閱 [Site Recovery 概觀](site-recovery-overview.md)

