---
title: 關於 Azure Site Recovery 服務的一般問題
description: 本文討論有關 Azure Site Recovery 的一般熱門問題。
ms.topic: conceptual
ms.date: 7/14/2020
ms.author: raynew
ms.openlocfilehash: 89a5785811b4f4833a5a5ddcef827b258ce1775a
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/23/2020
ms.locfileid: "87083730"
---
# <a name="general-questions-about-azure-site-recovery"></a>關於 Azure Site Recovery 的一般問題

本文摘要說明有關 Azure Site Recovery 的常見問題集。 對於特定案例，請參閱下列文章

- [關於 Azure VM 至 Azure 災害復原的問題](azure-to-azure-common-questions.md)
- [關於 VMware VM 至 Azure 災害復原的問題](vmware-azure-common-questions.md)
- [關於 Hyper-V VM 至 Azure 災害復原的問題](hyper-v-azure-common-questions.md)
 
## <a name="general"></a>一般

### <a name="what-does-site-recovery-do"></a>Site Recovery 的功能是什麼？

Site Recovery 可協調並自動執行區域、內部部署虛擬機器和實體伺服器之間至 Azure 的 Azure VM 複寫；協調並自動執行內部部署機器至次要資料中心的複寫，藉此保障您的商務持續性與災害復原 (BCDR) 策略。 [深入了解](site-recovery-overview.md)。

### <a name="can-i-protect-a-virtual-machine-that-has-a-docker-disk"></a>是否可以保護具有 Docker 磁碟的虛擬機器？

否，這是不支援的案例。

### <a name="what-does-site-recovery-do-to-ensure-data-integrity"></a>Site Recovery 如何確保資料完整性？

Site Recovery 採取各種不同的措施，以確保資料的完整性。 使用 HTTPS 通訊協定，在所有服務之間建立安全連線。 這可確保任何惡意程式碼或外部實體無法篡改資料。 所採用的另一個量值是使用總和檢查碼。 來源與目標之間的資料傳輸是透過計算兩者之間的資料總和檢查碼來執行。 這可確保傳輸的資料一致。

## <a name="service-providers"></a>服務提供者

### <a name="im-a-service-provider-does-site-recovery-work-for-dedicated-and-shared-infrastructure-models"></a>我是服務提供者。 Site Recovery 是否適用於專用和共用基礎結構模型？
是，Site Recovery 同時支援專用與共用的基礎結構模型。

### <a name="for-a-service-provider-is-the-identity-of-my-tenant-shared-with-the-site-recovery-service"></a>對服務提供者而言，租用戶的身分識別是否會與 Site Recovery 服務共用？
不可以。 租用戶身分識別會保持匿名。 您的租用戶不需要存取 Site Recovery 入口網站。 只有服務提供者系統管理員會與入口網站互動。

### <a name="will-tenant-application-data-ever-go-to-azure"></a>租用戶應用程式資料是否會傳送到 Azure？
在服務提供者擁有的站台之間進行複寫時，永遠不會將應用程式資料傳送到 Azure。 資料會在傳輸中加密，並且會在服務提供者站台之間直接進行複寫。

如果您是複寫至 Azure，應用程式資料就會傳送到 Azure 儲存體而不是 Site Recovery 服務。 資料會在傳輸中加密並在 Azure 中繼續維持加密狀態。

### <a name="will-my-tenants-receive-a-bill-for-any-azure-services"></a>我的租用戶會收到來自 Azure 服務的帳單嗎？
否。 Azure 的計費關係是直接與服務提供者相關。 服務提供者負責為其租用戶產生特定的帳單。

### <a name="if-im-replicating-to-azure-do-we-need-to-run-virtual-machines-in-azure-at-all-times"></a>如果我複寫至 Azure，我們需要在 Azure 中隨時執行虛擬機器嗎？
否，資料會複寫至您訂用帳戶中的 Azure 儲存體。 當您執行測試容錯移轉 (DR 演練) 或實際容錯移轉時，Site Recovery 會自動在您的訂用帳戶中建立虛擬機器。

### <a name="do-you-ensure-tenant-level-isolation-when-i-replicate-to-azure"></a>當我複寫至 Azure 時，您會確保提供租用戶層級的隔離嗎？
是。

### <a name="what-platforms-do-you-currently-support"></a>目前支援哪些平台？
我們支援「Azure 套件」、「雲端平台系統」及 System Center 架構 (2012 及更新版本) 的部署。 [深入了解](/previous-versions/azure/windows-server-azure-pack/dn850370(v=technet.10)) 「Azure 套件」和 Site Recovery 整合

### <a name="do-you-support-single-azure-pack-and-single-vmm-server-deployments"></a>您支援單一 Azure 套件與單一 VMM 伺服器部署嗎？
是，您可以將 Hyper-V 虛擬機器複寫至 Azure，或是在服務提供者網站之間進行複寫。  請注意，如果您是在服務提供者站台之間進行複寫，將無法使用 Azure Runbook 整合。

## <a name="pricing"></a>定價

### <a name="where-can-i-find-pricing-information"></a>哪裡可以找到定價資訊？
如需詳細資料，請檢閱 [Site Recovery 定價](https://azure.microsoft.com/pricing/details/site-recovery/)。


### <a name="how-can-i-calculate-approximate-charges-during-the-use-of-site-recovery"></a>如何計算使用 Site Recovery 期間的大約費用？

您可以使用[定價計算機](https://aka.ms/asr_pricing_calculator)來估算使用 Site Recovery 時的成本。

如需詳細的成本估算，請對 [VMware](https://aka.ms/siterecovery_deployment_planner) 或 [Hyper-V](https://aka.ms/asr-deployment-planner) 執行部署規劃工具，並使用[成本估算報告](https://aka.ms/asr_DP_costreport)。


### <a name="managed-disks-are-now-used-to-replicate-vmware-vms-and-physical-servers-do-i-incur-additional-charges-for-the-cache-storage-account-with-managed-disks"></a>受控磁碟現在用來複寫 VMware VM 和實體伺服器。 具有受控磁碟的快取儲存體帳戶是否會產生額外費用？

否，快取不會有額外費用。 當您複寫到標準儲存體帳戶時，此快取儲存體屬於相同的目標儲存體帳戶。

### <a name="i-have-been-an-azure-site-recovery-user-for-over-a-month-do-i-still-get-the-first-31-days-free-for-every-protected-instance"></a>我已經使用 Azure Site Recovery 一個多月了。 我的每個受保護的執行個體是否前 31 天仍然都免費？

是。 每個受保護的執行個體在前 31 天皆無 Azure Site Recovery 費用。 例如，如果您過去 6 個月保護了 10 個執行個體，而現在您將第 11 個執行個體連線到 Azure Site Recovery，則第 11 個執行個體的前 31 天不會有任何費用。 而前 10 個執行個體則因為受保護已超過 31 天，所以會繼續產生 Azure Site Recovery 費用。

### <a name="during-the-first-31-days-will-i-incur-any-other-azure-charges"></a>在這前 31 天，我是否須負擔任何其他 Azure 費用？

是，即使受保護的執行個體前 31 天的 Site Recovery 免費，您仍有可能需要負擔 Azure 儲存體、儲存體交易與資料傳輸的費用。 復原的虛擬機器也可能會產生 Azure 計算費用。


### <a name="is-there-a-cost-associated-to-perform-disaster-recovery-drillstest-failover"></a>執行災害復原演練/測試容錯移轉是否有相關成本？

DR 演練沒有個別的成本。 在測試容錯移轉之後，若建立虛擬機器，則會有計算費用。



## <a name="security"></a>安全性

### <a name="is-replication-data-sent-to-the-site-recovery-service"></a>複寫資料會傳送到 Site Recovery 服務嗎？
否，Site Recovery 不會攔截複寫的資料，也不會擁有任何關於您虛擬機器或實體伺服器上執行哪些項目的資訊。
內部部署 Hyper-V 主機、VMware Hypervisor 或實體伺服器，會與 Azure 儲存體或次要站台交換複寫資料。 Site Recovery 並不具有攔截該資料的能力。 只有協調複寫與容錯移轉所需的中繼資料會被傳送給 Site Recovery 服務。  

Site Recovery 已通過 ISO 27001:2013、27018、HIPAA、DPA 認證，並且正在進行 SOC2 和 FedRAMP JAB 評定程序。

### <a name="for-compliance-reasons-even-our-on-premises-metadata-must-remain-within-the-same-geographic-region-can-site-recovery-help-us"></a>為了遵循法規，甚至我們的內部部署中繼資料也必須保留在相同的地理區域內。 Site Recovery 可以幫助我們嗎？
是。 當您在某個區域中建立 Site Recovery 保存庫時，我們會確保我們啟用及協調複寫與容錯移轉時所需的一切中繼資料都會保留在該區域地理界限內。

### <a name="does-site-recovery-encrypt-replication"></a>Site Recovery 會將複寫加密嗎？
就虛擬機器和實體伺服器而言，在內部部署站台之間進行複寫時，支援傳輸中加密。 在將虛擬機器和實體伺服器複寫至 Azure 時，則同時支援傳輸中加密和[靜態加密 (在 Azure 中)](../storage/common/storage-service-encryption.md)。

### <a name="does-azure-to-azure-site-recovery-use-tls-12-for-all-communications-across-microservices-of-azure"></a>Azure 到 Azure Site Recovery 是否將 TLS 1.2 用於跨 Azure 微服務的所有通訊？
是，預設會針對 Azure 到 Azure Site Recovery 案例強制執行 TLS 1.2 通訊協定。 

### <a name="how-can-i-enforce-tls-12-on-vmware-to-azure-and-physical-server-to-azure-site-recovery-scenarios"></a>如何在 VMware 到 Azure 和實體伺服器到 Azure Site Recovery 案例上強制執行 TLS 1.2？
安裝在複寫項目上的行動代理程式只會在 TLS 1.2 上與處理序伺服器通訊。 不過，從組態伺服器到 Azure，以及從處理序伺服器到 Azure 的通訊都可以在 TLS 1.1 或1.0 上進行。 請遵循[指引](https://support.microsoft.com/en-us/help/3140245/update-to-enable-tls-1-1-and-tls-1-2-as-default-secure-protocols-in-wi)，在您設定的所有組態伺服器和處理序伺服器上強制執行 TLS 1.2。

### <a name="how-can-i-enforce-tls-12-on-hyperv-to-azure-site-recovery-scenarios"></a>如何在 HyperV 到 Azure Site Recovery 案例上強制執行 TLS 1.2？
Azure Site Recovery 微服務之間的所有通訊都會在 TLS 1.2 通訊協定上發生。 Site Recovery 會使用系統 (OS) 中設定的安全性提供者，並使用最新可用的 TLS 通訊協定。 其中一個必須在登錄中明確啟用 TLS 1.2，然後 Site Recovery 就會開始使用 TLS 1.2 進行與服務的通訊。 

### <a name="how-can-i-enforce-restricted-access-on-my-storage-accounts-which-are-accessed-by-site-recovery-service-for-readingwriting-replication-data"></a>如何在我的儲存體帳戶上強制執行限制存取，以 Site Recovery 服務存取來讀取/寫入複寫資料？
您可以前往 [身分*識別*] 設定來切換復原服務保存庫的受控識別。 當保存庫向 Azure Active Directory 註冊後，您就可以移至您的儲存體帳戶，並將下列角色指派授與保存庫：

- 以 Resource Manager 為基礎的儲存體帳戶（標準類型）：
  - [參與者](../role-based-access-control/built-in-roles.md#contributor)
  - [儲存體 Blob 資料參與者](../role-based-access-control/built-in-roles.md#storage-blob-data-contributor)
- 以 Resource Manager 為基礎的儲存體帳戶（Premium 類型）：
  - [參與者](../role-based-access-control/built-in-roles.md#contributor)
  - [儲存體 Blob 資料擁有者](../role-based-access-control/built-in-roles.md#storage-blob-data-owner)
- 傳統儲存體帳戶：
  - [傳統儲存體帳戶參與者](../role-based-access-control/built-in-roles.md#classic-storage-account-contributor)
  - [傳統儲存體帳戶金鑰操作員服務角色](../role-based-access-control/built-in-roles.md#classic-storage-account-key-operator-service-role)

## <a name="disaster-recovery"></a>災害復原

### <a name="what-can-site-recovery-protect"></a>Site Recovery 可以保護什麼？
* **Azure VM**：Site Recovery 可複寫在支援的 Azure VM 上執行的任何工作負載
* **Hyper-V 虛擬機器**：Site Recovery 可以保護 Hyper-V VM 上執行的任何工作負載。
* **實體伺服器**：Site Recovery 可以保護執行 Windows 或 Linux 的實體伺服器。
* **VMware 虛擬機器**：Site Recovery 可以保護 VMware VM 上執行的任何工作負載。

### <a name="what-workloads-can-i-protect-with-site-recovery"></a>我可以使用 Site Recovery 來保護哪些工作負載？
您可以使用 Site Recovery 來保護在支援的 VM 或實體伺服器上執行的大多數工作負載。 Site Recovery 支援應用程式感知複寫，可讓應用程式復原為智慧型狀態。 除了與 Microsoft 應用程式 (例如 SharePoint、Exchange、Dynamics、SQL Server 及 Active Directory) 整合之外，它還與產業龍頭 (包括 Oracle、SAP、IBM 及 Red Hat) 密切合作。 [深入了解](site-recovery-workload.md) 工作負載保護。

### <a name="can-i-manage-disaster-recovery-for-my-branch-offices-with-site-recovery"></a>我可以使用 Site Recovery 來管理分公司的災害復原嗎？
是。 當您使用 Site Recovery 來協調分公司中的複寫與容錯移轉時，會為您集中提供所有分公司工作負載的整合協調與檢視。 您不需要造訪分公司，就可以從總公司輕鬆執行所有分公司的容錯移轉及管理災害復原。


### <a name="is-disaster-recovery-supported-for-azure-vms"></a>是否支援 Azure VM 的災害復原？

是，Site Recovery 支援 Azure 區域之間 Azure VM 的災害復原。 檢閱有關 Azure VM 災害復原的[常見問題](azure-to-azure-common-questions.md)。 如果您想要在相同大陸上的兩個 Azure 區域之間進行複寫，請使用我們的 Azure 至 Azure DR 供應專案。 不需要安裝設定伺服器/進程伺服器和 ExpressRoute 連線。

### <a name="is-disaster-recovery-supported-for-vmware-vms"></a>是否支援 VMware VM 的災害復原？

是，Site Recovery 支援內部部署 VMware VM 的災害復原。 檢閱 VMware VM 災害復原的[常見問題](vmware-azure-common-questions.md)。

### <a name="is-disaster-recovery-supported-for-hyper-v-vms"></a>是否支援 Hyper-V VM 的災害復原？
是，Site Recovery 支援內部部署 Hyper-V VM 的災害復原。 檢閱 Hyper-V VM 災害復原的[常見問題](hyper-v-azure-common-questions.md)。

### <a name="is-disaster-recovery-supported-for-physical-servers"></a>是否支援實體伺服器的災害復原？
是，Site Recovery 支援執行 Windows 和 Linux 的內部部署實體伺服器災害復原到 Azure 或次要站站。 了解 [Azure](vmware-physical-azure-support-matrix.md#replicated-machines) 和[次要站台](vmware-physical-secondary-support-matrix.md#replicated-vm-support)的災害復原需求。
請注意，在容錯移轉之後，實體伺服器將會在 Azure 中以 VM 的形式執行。 目前不支援從 Azure 容錯回復至內部部署實體伺服器。 您只能容錯回復至 VMware 虛擬機器。





## <a name="replication"></a>複寫

### <a name="can-i-replicate-over-a-site-to-site-vpn-to-azure"></a>我可以透過站對站 VPN 複寫至 Azure 嗎？
Azure Site Recovery 會透過公用端點，將資料複製到 Azure 儲存體帳戶或受控磁碟。 複寫不是透過站對站 VPN。 

### <a name="why-cant-i-replicate-over-vpn"></a>為何我無法透過 VPN 進行複寫？

當您複寫至 Azure 時，複寫流量會到達 Azure 儲存體的公用端點。 因此，您只能透過公用網際網路或透過 ExpressRoute (Microsoft 對等互連或現有的公用對等互連) 進行複寫。

### <a name="can-i-use-riverbed-steelheads-for-replication"></a>我可以使用 Riverbed SteelHeads 進行複寫嗎？

我們的合作夥伴 Riverbed 提供有關使用 Azure Site Recovery 的詳細指引。 請檢閱其[解決方案指南](https://community.riverbed.com/s/article/DOC-4627)。

### <a name="can-i-use-expressroute-to-replicate-virtual-machines-to-azure"></a>可以使用 ExpressRoute 將虛擬機器複寫到 Azure 嗎？
是的，[可以使用 ExpressRoute](concepts-expressroute-with-site-recovery.md) 將內部部署虛擬機器複寫至 Azure。

- Azure Site Recovery 會透過公用端點，將資料複製到 Azure 儲存體。 您必須設定 [Microsoft 對等互連](../expressroute/expressroute-circuit-peerings.md#microsoftpeering)或使用現有的[公用對等互連](../expressroute/about-public-peering.md) (已遭新線路棄用)，才能使用 ExpressRoute 進行 Site Recovery 複寫。
- Microsoft 對等互連是建議用於複寫的路由網域。
- 私用對等互連不支援複寫。
- 如果您要保護 VMware 機器或實體機器，請確定也符合組態伺服器的[網路需求](vmware-azure-configuration-server-requirements.md#network-requirements)。 組態伺服器需要連線到特定 URL，才能進行 Site Recovery 複寫的協調流程。 ExpressRoute 無法用於此連線。
- 在虛擬機器容錯移轉到 Azure 虛擬網路之後，您可以 Azure 虛擬網路使用[私人對等互連](../expressroute/expressroute-circuit-peerings.md#privatepeering)安裝來存取這些虛擬機器。


### <a name="if-i-replicate-to-azure-what-kind-of-storage-account-or-managed-disk-do-i-need"></a>如果複寫至 Azure，我需要哪一種儲存體帳戶或受控磁碟？

您需要 LRS 或 GRS 儲存體。 我們建議使用 GRS，以便在發生區域性停電或無法復原主要區域時，能夠恢復資料。 此帳戶必須位於與復原服務保存庫相同的區域中。 當您在 Azure 入口網站部署 Site Recovery 時，進階儲存體支援 VMware VM、Hyper-V VM 和實體伺服器複寫。 受控磁碟僅支援 LRS。

### <a name="how-often-can-i-replicate-data"></a>我可以多久複寫一次資料？
* **Hyper-V：** Hyper-V VM 可以每隔 30 秒 (進階儲存體除外)、5 分鐘或 15 分鐘複寫一次。
* **Azure VM、VMware VM、實體伺服器：** 複寫頻率在此處並不相關。 複寫是連續的。

### <a name="can-i-extend-replication-from-existing-recovery-site-to-another-tertiary-site"></a>我可以將複寫從現有的復原網站延伸到另一個第三網站嗎？
不支援延伸的或鏈結的複寫。 請在 [意見反應論壇](https://feedback.azure.com/forums/256299-site-recovery/suggestions/6097959)中提出這項功能的要求。

### <a name="can-i-do-an-offline-replication-the-first-time-i-replicate-to-azure"></a>我可以在第一次複寫至 Azure 時進行離線複寫嗎？
不支援此做法。 請在 [意見反應論壇](https://feedback.azure.com/forums/256299-site-recovery/suggestions/6227386-support-for-offline-replication-data-transfer-from)中提出這項功能的要求。

### <a name="can-i-exclude-specific-disks-from-replication"></a>我可以從複寫中排除特定的磁碟嗎？
如果您使用 Azure 入口網站來將 VMware VM 和 Hyper-V VM 複寫至 Azure，就支援此做法。

### <a name="can-i-replicate-virtual-machines-with-dynamic-disks"></a>我可以使用動態磁碟來複寫虛擬機器嗎？
複寫 Hyper-V 虛擬機器時，以及將 VMware VM 和實體機器複寫至 Azure 時，會支援動態磁碟。 作業系統磁碟必須是基本磁碟。


### <a name="can-i-throttle-bandwidth-allotted-for-replication-traffic"></a>我是否可以將分配給 Hyper-V 複寫流量的頻寬節流？
是。 您可以在下列文章中深入了解如何將頻寬節流：

* [適用於複寫 VMware VM 和實體伺服器的容量規劃](site-recovery-plan-capacity-vmware.md)
* [適用於將 Hyper-V VM 複寫至 Azure 的容量規劃](./hyper-v-deployment-planner-overview.md)

### <a name="can-i-enable-replication-with-app-consistency-in-linux-servers"></a>我可以在 Linux 伺服器中使用應用程式一致性來啟用複寫嗎？ 
是。 適用于 Linux 作業系統的 Azure Site Recovery 支援應用程式的自訂腳本，以進行應用程式一致性。 具有前置和後置選項的自訂腳本，會在應用程式一致性期間由 Azure Site Recovery 行動代理程式使用。 以下是啟用它的步驟。

1. 以 root 身分登入電腦。
2. 將目錄變更為 Azure Site Recovery 行動代理程式安裝位置。 預設值為 "/usr/local/ASR"<br>
    `# cd /usr/local/ASR`
3. 在 [安裝位置] 底下，將目錄變更為 "VX/scripts"<br>
    `# cd VX/scripts`
4. 建立名為 "customscript.sh" 的 bash shell 腳本，其具有 root 使用者的執行許可權。<br>
    a. 腳本應該支援 "--pre" 和 "--post" （請注意雙虛線）命令列選項<br>
    b. 以預先選項呼叫腳本時，它應該會凍結應用程式的輸入/輸出，並在使用 post 選項呼叫時，應該解除凍結應用程式的輸入/輸出。<br>
    c. 範例範本-<br>

    `# cat customscript.sh`<br>

```
    #!/bin/bash

    if [ $# -ne 1 ]; then
        echo "Usage: $0 [--pre | --post]"
        exit 1
    elif [ "$1" == "--pre" ]; then
        echo "Freezing app IO"
        exit 0
    elif [ "$1" == "--post" ]; then
        echo "Thawed app IO"
        exit 0
    fi
```

5. 在前置和後置步驟中，針對需要應用程式一致性的應用程式新增凍結和取消凍結輸入/輸出命令。 您可以選擇新增另一個腳本來指定這些檔案，並使用前置和後置選項從 "customscript.sh" 加以叫用。

>[!Note]
>Site Recovery 代理程式版本應該是9.24 或更新版本，才能支援自訂腳本。

## <a name="failover"></a>容錯移轉
### <a name="if-im-failing-over-to-azure-how-do-i-access-the-azure-vms-after-failover"></a>如果容錯移轉到 Azure，在容錯移轉之後，我要如何存取存取 Azure VM？

您可以透過安全的網際網路連線、透過站台對站台 VPN 或透過 Azure ExpressRoute 存取 Azure VM。 您必須做好一些準備才能連線。 [深入了解](site-recovery-test-failover-to-azure.md#prepare-to-connect-to-azure-vms-after-failover)。


### <a name="if-i-fail-over-to-azure-how-does-azure-make-sure-my-data-is-resilient"></a>如果我容錯移轉到 Azure，Azure 如何確定我的資料具有復原能力？
Azure 是針對復原能力而設計的。 Site Recovery 已設計成可根據 Azure SLA 容錯移轉至次要 Azure 資料中心。 如果發生這種情況，我們會確保您的中繼資料和保存庫都保留在您為保存庫選擇的相同地理區域中。  

### <a name="if-im-replicating-between-two-datacenters-what-happens-if-my-primary-datacenter-experiences-an-unexpected-outage"></a>如果我在兩個資料中心之間進行複寫，當我的主要資料中心發生意外中斷時，會發生什麼情況？
您可以從次要站台觸發非計劃性容錯移轉。 Site Recovery 不需要來自主要站台的連線即可執行容錯移轉。

### <a name="is-failover-automatic"></a>容錯移轉是自動發生的嗎？
容錯移轉並非自動發生。 您可以在入口網站中按一下某個按鈕來起始容錯移轉，或是使用 [Site Recovery PowerShell](/powershell/module/az.recoveryservices) 來觸發容錯移轉。 容錯回復在 Site Recovery 入口網站中是一個簡單的動作。

若要進行自動化，您可以使用內部部署 Orchestrator 或 Operations Manager 來偵測虛擬機器失敗，然後使用 SDK 來觸發容錯移轉。

* [閱讀更多](site-recovery-create-recovery-plans.md) 復原方案的相關資訊。
* [深入了解](site-recovery-failover.md) 容錯移轉。
* [深入了解](./vmware-azure-failback.md) 如何容錯回復 VMware VM 和實體伺服器

### <a name="if-my-on-premises-host-is-not-responding-or-crashed-can-i-fail-back-to-a-different-host"></a>如果我的內部部署主機沒有回應或當機，我是否能針對不同的主機進行容錯回復？
是，您可以使用替代位置復原從 Azure 針對不同的主機進行容錯回復。

* [針對 VMware 虛擬機器](concepts-types-of-failback.md#alternate-location-recovery-alr)
* [針對 Hyper-V 虛擬機器](hyper-v-azure-failback.md#fail-back-to-an-alternate-location)

## <a name="automation"></a>自動化

### <a name="can-i-automate-site-recovery-scenarios-with-an-sdk"></a>我是否可以透過 SDK 自動化 Site Recovery 案例？
是。 您可以使用 Rest API、PowerShell 或 Azure SDK 將 Site Recovery 的工作流程自動化。 針對使用 PowerShell 來部署 Site Recovery，目前支援的案例包括︰

* [將 VMM 雲端中的 Hyper-V VM 複寫至 Azure PowerShell Resource Manager](hyper-v-vmm-powershell-resource-manager.md)
* [將不使用 VMM 的 Hyper-V VM 複寫至 Azure PowerShell Resource Manager](hyper-v-azure-powershell-resource-manager.md)
* [使用 PowerShell Resource Manager 將 VMware 複寫至 Azure](vmware-azure-disaster-recovery-powershell.md)

## <a name="componentprovider-upgrade"></a>元件/提供者升級

### <a name="where-can-i-find-the-release-notesupdate-rollups-of-site-recovery-upgrades"></a>哪裡可以找到 Site Recovery 升級的版本資訊/更新彙總套件

[了解](site-recovery-whats-new.md)新的更新，並取得[匯總套件資訊](service-updates-how-to.md)。

## <a name="next-steps"></a>後續步驟
* 參閱 [Site Recovery 概觀](site-recovery-overview.md)
