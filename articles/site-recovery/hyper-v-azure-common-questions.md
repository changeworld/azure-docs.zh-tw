---
title: 使用 Azure Site Recovery 進行 Hyper-v 嚴重損壞修復的常見問題
description: 本文摘要說明使用 Azure Site Recovery 網站來設定「內部部署 Hyper-V VM 至Azure 的災害復原」時的常見問題。
ms.date: 11/12/2019
ms.topic: conceptual
ms.openlocfilehash: e4f5ee71fc56cad247b57f94bf08a68bed8775f9
ms.sourcegitcommit: ea551dad8d870ddcc0fee4423026f51bf4532e19
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/07/2020
ms.locfileid: "96754346"
---
# <a name="common-questions---hyper-v-to-azure-disaster-recovery"></a>常見問題 - 從 Hyper-V 至 Azure 的災害復原

此文章將解答我們將內部部署 Hyper- VM 複寫到 Azure 時的常見問題。 

## <a name="general"></a>一般

### <a name="how-is-site-recovery-priced"></a>Site Recovery 是如何定價的？
請檢閱[Azure Site Recovery 定價](https://azure.microsoft.com/pricing/details/site-recovery/)詳細資料。

### <a name="how-do-i-pay-for-azure-vms"></a>如何支付 Azure VM 費用？
在複寫期間，資料會複寫至 Azure 儲存體，您無須為任何 VM 變更付費。 當您容錯移轉到 Azure 時，Site Recovery 會自動建立 Azure IaaS 虛擬機器。 之後就會針對您在 Azure 中取用的運算資源進行計費。

### <a name="is-there-any-difference-in-cost-when-replicating-to-general-purpose-v2-storage-account"></a>複寫至一般用途 v2 儲存體帳戶時，成本是否有任何差異？

您通常會看到 GPv2 儲存體帳戶所產生的交易成本增加，因為 Azure Site Recovery 的交易繁重。 [深入](../storage/common/storage-account-upgrade.md#pricing-and-billing) 瞭解以預估變更。

## <a name="azure"></a>Azure

### <a name="what-do-i-need-in-hyper-v-to-orchestrate-replication-with-site-recovery"></a>在 Hyper-V 中，我需要什麼，才能使用 Site Recovery 來協調複寫？

對於 Hyper-V 主機伺服器，您的需求視部署案例而定。 請查看下列主題中的 Hyper-V 先決條件：

* [將 Hyper-V VM (不使用 VMM) 複寫至 Azure](./hyper-v-azure-tutorial.md)
* [將 Hyper-V VM (使用 VMM) 複寫至 Azure](./hyper-v-vmm-disaster-recovery.md)
* [將 Hyper-V VM 複寫至次要資料中心](./hyper-v-vmm-disaster-recovery.md)
* 如果您要複寫至次要資料中心，請參閱 [支援的 Hyper-V VM 客體作業系統](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/mt126277(v=ws.11))。
* 如果是覆寫至 Azure，則 Site Recovery 支援 [Azure 支援的](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc794868(v=ws.10))所有客體作業系統。

### <a name="can-i-protect-vms-when-hyper-v-is-running-on-a-client-operating-system"></a>當 Hyper-V 在用戶端作業系統上執行時，我可以保護 VM 嗎？
不能，VM 所在的 Hyper-V 主機伺服器必須在支援的 Windows 伺服器機器上執行。 如果您需要保護用戶端電腦，您可以用實體機器的形式將它複寫至 [Azure](./vmware-azure-tutorial.md) 或[次要資料中心](./vmware-physical-secondary-disaster-recovery.md)。

### <a name="do-hyper-v-hosts-need-to-be-in-vmm-clouds"></a>Hyper-V 主機是否必須在 VMM 雲端中？
如果您想要複寫至次要資料中心，Hyper-V VM 就必須在位於 VMM 雲端中的 Hyper-V 主機伺服器上。 若您想要複寫至 Azure，則不論是否使用 VMM 雲端，皆可複寫 VM。 [閱讀更多](./hyper-v-azure-tutorial.md) Hyper-V 複寫至 Azure 的相關資訊。


### <a name="can-i-replicate-hyper-v-generation-2-virtual-machines-to-azure"></a>我可以將 Hyper-V 第 2 代虛擬機器複寫至 Azure 嗎？
是。 Site Recovery 會在容錯移轉時從第 2 代轉換成第 1 代。 在容錯回復時，機器會轉換回第 2 代。 [閱讀其他資訊](https://azure.microsoft.com/blog/2015/04/28/disaster-recovery-to-azure-enhanced-and-were-listening/)。


### <a name="can-i-deploy-site-recovery-with-vmm-if-i-only-have-one-vmm-server"></a>如果我只有一部 VMM 伺服器，可以部署 Site Recovery 搭配 VMM 嗎？

是。 您可以將 VMM 雲端 Hyper-V 伺服器中的 VM 複寫至 Azure，或是在同一部伺服器上的 VMM 雲端之間進行複寫。 若要在內部部署環境之間進行複寫，建議您在主要站台與次要站台都要有 VMM 伺服器。 

### <a name="what-do-i-need-in-azure"></a>在 Azure 中需要什麼？
您需要 Azure 訂用帳戶、復原服務保存庫、儲存體帳戶和虛擬網路。 保存庫、儲存體帳戶和網路必須位於相同的區域中。

### <a name="what-azure-storage-account-do-i-need"></a>我需要怎樣的 Azure 儲存體帳戶？
您需要 LRS 或 GRS 儲存體帳戶。 我們建議使用 GRS，以便在發生區域性停電或無法復原主要區域時，能夠恢復資料。 可支援進階儲存體。

### <a name="does-my-azure-account-need-permissions-to-create-vms"></a>我的 Azure 帳戶是否需要建立 VM 的權限？
如果您是訂用帳戶管理員，則會具有所需的複寫權限。 如果您不是，則需要必要權限才能在您設定 Site Reocvery 時指定的資源群組和虛擬網路中建立 Azure VM，且需要寫入至所選儲存體帳戶的權限。 [深入了解](site-recovery-role-based-linked-access-control.md#permissions-required-to-enable-replication-for-new-virtual-machines)。

### <a name="is-replication-data-sent-to-site-recovery"></a>複寫資料是否會傳送至 Site Recovery？
否，Site Recovery 不會攔截複寫的資料，也不會擁有任何關於您的 VM 上執行哪些項目的資訊。 複寫資料會在 Hyper-V 主機與 Azure 儲存體之間交換。 Site Recovery 並不具有攔截該資料的能力。 只有協調複寫與容錯移轉所需的中繼資料會被傳送給 Site Recovery 服務。  

Site Recovery 已通過 ISO 27001:2013、27018、HIPAA、DPA 認證，並且正在進行 SOC2 和 FedRAMP JAB 評定程序。

### <a name="can-we-keep-on-premises-metadata-within-a-geographic-region"></a>我們可以在地理區域內保留內部部署中繼資料嗎？
是。 當您在某個區域中建立保存庫時，我們會確保 Site Recovery 所使用的所有中繼資料都會保留在該區域的地理界限內。

### <a name="does-site-recovery-encrypt-replication"></a>Site Recovery 會將複寫加密嗎？
是，傳輸中加密和 [Azure 中的加密](../storage/common/storage-service-encryption.md)均受支援。


## <a name="deployment"></a>部署

### <a name="what-can-i-do-with-hyper-v-to-azure-replication"></a>Hyper-V 到 Azure 的複寫有何功能？

- **災害復原**：您可以設定完整的災害復原。 在此案例中，您會將內部部署 Hyper-V VM 複寫到 Azure 儲存體：
    - 您可以將 VM 複寫到 Azure。 若您的內部部署基礎結構無法使用，您即可容錯移轉到 Azure。
    - 當您執行容錯移轉時，系統會使用複寫的資料建立 Azure VM。 您可以存取 Azure VM 上的應用程式與工作負載。
    - 當您的內部部署資料中心再次可用時，您可以從 Azure 容錯回復到您的內部部署站台。
- **移轉**：您可以使用 Site Recovery 將內部部署 Hyper-V VM 移轉到Azure 儲存體。 然後，您會從內部部署環境容錯移轉至 Azure。 容錯移轉之後，您的應用程式和工作負載將可在 Azure VM 上使用及執行。


### <a name="what-do-i-need-on-premises"></a>我的內部部署環境需要什麼？

您需要在一或多部獨立或叢集 Hyper-V 主機上執行的一或多部 VM。 您也可以複寫在主機上執行且由 System Center Virtual Machine Manager (VMM) 管理的 VM。
- 若您未執行 VMM，在 Site Recovery 部署期間，您會將 Hyper-V 主機與叢集收集到 Hyper-V 網站中。 您可在每部 Hyper-V 主機上安裝 Site Recovery 代理程式 (Azure Site Recovery Provider 與復原服務服務代理程式)。
- 若 Hyper-V 主機位於 VMM 雲端中，您可以協調 VMM 中的複寫。 您可以在每部 VMM 伺服器上安裝 Site Recovery Provider，並在每部 Hyper-V 主機上安裝復原服務代理程式。 您必須在 VMM 邏輯/VM 網路與 Azure VNet 之間對應。
- [深入了解](hyper-v-azure-architecture.md) Hyper-V 到 Azure 的架構。

### <a name="can-i-replicate-vms-located-on-a-hyper-v-cluster"></a>我是否可以複寫 Hyper-V 叢集上的 VM？

是，Site Recovery 支援叢集 Hyper-V 主機。 請注意：

- 叢集的所有節點都應該向相同的保存庫註冊。
- 若您未使用 VMM，叢集中的所有 Hyper-V 主機都應該新增到相同的 Hyper-V 網站。
- 您可以在叢集中的每部 Hyper-V 主機上安裝 Azure Site Recovery Provider 與復原服務代理程式，並將每部主機新到 Hyper-V 網站。
- 在叢集上不需要進行任何特定步驟。
- 若您執行適用於 Hyper-V 的部署規劃工具，該工具會從 VM 執行所在的執行中節點收集設定檔資料。 該工具無法從已關閉的節點收集任何資料，但它將會追蹤該節點。 當節點啟動並執行之後，該工具會開始從該節點收集 VM 設定檔資料 (若 VM 是設定檔 VM 清單的一部分且已在該節點上執行)。
- 如果 Site Recovery 中的 Hyper-V 主機上的 VM 移轉至相同叢集中的不同 Hyper-V 主機，或移轉至獨立主機，該 VM 的複寫將不受影響。 Hyper-V 主機必須符合[先決條件](hyper-v-azure-support-matrix.md#on-premises-servers)，而且必須在 Site Recovery 保存庫中設定。 


### <a name="can-i-protect-vms-when-hyper-v-is-running-on-a-client-operating-system"></a>當 Hyper-V 在用戶端作業系統上執行時，我可以保護 VM 嗎？
不能，VM 所在的 Hyper-V 主機伺服器必須在支援的 Windows 伺服器機器上執行。 如果您需要保護用戶端電腦，您可以[用實體機器的形式將它複寫至 Azure](physical-azure-disaster-recovery.md)。

### <a name="can-i-replicate-hyper-v-generation-2-virtual-machines-to-azure"></a>我可以將 Hyper-V 第 2 代虛擬機器複寫至 Azure 嗎？
是。 Site Recovery 會在容錯移轉時從第 2 代轉換成第 1 代。 在容錯回復時，機器會轉換回第 2 代。

### <a name="can-i-automate-site-recovery-scenarios-with-an-sdk"></a>我是否可以透過 SDK 自動化 Site Recovery 案例？
是。 您可以使用 Rest API、PowerShell 或 Azure SDK 將 Site Recovery 的工作流程自動化。 目前支援使用 PowerShell 將 Hyper-V 複寫到 Azure 的案例：

- [在沒有 VMM 的情況下使用 PowerShell 來複寫 Hyper-V](hyper-v-azure-powershell-resource-manager.md)
- [使用 PowerShell 以 VMM 複寫 Hyper-v](hyper-v-vmm-powershell-resource-manager.md)

## <a name="replication"></a>複寫

### <a name="where-do-on-premises-vms-replicate-to"></a>內部部署 VM 會複寫到何處？
資料會複寫到 Azure 儲存體。 當您容錯移轉時，Site Recovery 會從儲存體帳戶自動建立 Azure VM。

### <a name="what-apps-can-i-replicate"></a>我可以複寫哪些應用程式？
您可以複寫符合[複寫需求](hyper-v-azure-support-matrix.md#replicated-vms)之 Hyper-V VM 上執行的任何應用程式或工作負載。 Site Recovery 支援應用程式感知複寫，可讓應用程式容錯移轉及容錯回復至智慧型狀態。 Site Recovery 除了與 Microsoft 應用程式 (例如 SharePoint、Exchange、Dynamics、SQL Server 及 Active Directory) 整合之外，還與產業龍頭 (包括 Oracle、SAP、IBM 及 Red Hat) 密切合作。 [深入了解](site-recovery-workload.md) 工作負載保護。

### <a name="whats-the-replication-process"></a>複寫程序為何？

1. 系統會在初始複寫觸發時擷取 Hyper-V VM 快照集。
2. VM 上的虛擬硬碟會逐一複寫，直到它們全部複製到 Azure 為止。 視 VM 大小和網路頻寬而定，這可能需要一些時間。 了解如何增加網路頻寬。
3. 如果在初始複寫進行時發生磁碟變更，Hyper-V 複本複寫追蹤器會以 Hyper-V 複寫記錄 (.hrl) 的形式追蹤變更。 這些記錄檔位於與磁碟相同的資料夾中。 每個磁碟都有一個相關聯的.hrl 檔案會傳送至次要儲存體。 當初始複寫正在進行時，快照和記錄檔會取用磁碟資源。
4. 初始複寫完成時，就會刪除 VM 快照集。
5. 記錄檔中的任何磁碟變更都會同步處理，並合併到父磁碟。
6. 當初始複寫完成之後，就會執行「完成虛擬機器上的保護」作業。 這會設定網路和其他複寫後設定，讓 VM 受到保護。
7. 在這個階段，您可以檢查 VM 設定以確定它已準備好進行容錯移轉。 您可以執行 VM 的災害復原演練 (測試容錯移轉)，以檢查它是否如預期般容錯移轉。
8. 在初始複寫之後，系統會根據複寫原則，開始進行差異複寫。
9. 變更是已記錄的 .hrl 檔案。 每個設定用於複寫的磁碟都會有相關聯的 .hrl 檔案。
10. 此記錄會傳送至客戶的儲存體帳戶。 當記錄傳輸至 Azure 時，將會在同一資料夾內的另一個記錄檔中追蹤主要磁碟中的變更。
11. 在初始與差異複寫期間，您可以在 Azure 入口網站中監視 VM。

[深入了解](hyper-v-azure-architecture.md#replication-process)複寫程序。

### <a name="can-i-replicate-to-azure-with-a-site-to-site-vpn"></a>是否可透過站對站 VPN 複寫至 Azure？

Site Recovery 會透過公用端點，或使用 ExpressRoute Microsoft 對等互連，將資料從內部部署複寫至 Azure 儲存體。 不支援透過站對站 VPN 網路的複寫。

### <a name="can-i-replicate-to-azure-with-expressroute"></a>是否可透過 ExpressRoute 複寫至 Azure？

是的，ExpressRoute 可用來將 VM 複寫至 Azure。 Site Recovery 會透過公用端點將資料複寫至 Azure 儲存體帳戶，且您必須設定 Microsoft 對 [等互連](../expressroute/expressroute-circuit-peerings.md#microsoftpeering) 以進行 Site Recovery 複寫。 VM 容錯移轉至 Azure 虛擬網路之後，您可以使用[私人對等互連](../expressroute/expressroute-circuit-peerings.md#privatepeering)加以存取。


### <a name="why-cant-i-replicate-over-vpn"></a>為何我無法透過 VPN 進行複寫？

當您複寫至 Azure 時，複寫流量會到達 Azure 儲存體帳戶的公用端點。 因此，您只能使用 ExpressRoute (Microsoft 對等互連) 來複寫公用網際網路，而且 VPN 無法運作。 

### <a name="what-are-the-replicated-vm-requirements"></a>複寫的 VM 有何需求？

若要進行複寫，Hyper-V VM 必須執行支援的作業系統。 此外，VM 必須符合 Azure VM 的需求。 請在[支援矩陣](hyper-v-azure-support-matrix.md#replicated-vms)中深入了解相關資訊。

### <a name="why-is-an-additional-standard-storage-account-required-if-i-replicate-my-virtual-machine-disks-to-premium-storage"></a>如果我將虛擬機器磁片複寫至 premium 儲存體，為什麼需要額外的標準儲存體帳戶？

當您將內部部署虛擬機器/實體伺服器複寫至 premium 儲存體時，位於受保護機器磁片上的所有資料都會複寫至 premium 儲存體帳戶。 需要額外的標準儲存體帳戶來儲存複寫記錄。 完成複寫磁片資料的初始階段之後，內部部署磁片資料的所有變更都會持續追蹤並儲存為此額外標準儲存體帳戶中的複寫記錄。

### <a name="how-often-can-i-replicate-to-azure"></a>複寫到 Azure 的頻率為何？

Hyper-v Vm 每隔30秒就會複寫一次 (但 premium 儲存體) 或5分鐘除外。

### <a name="can-azure-site-recovery-and-hyper-v-replica-be-configured-together-on-a-hyper-v-machine"></a>可以在 Hyper-v 電腦上同時設定 Azure Site Recovery 和 Hyper-v 複本嗎？

是的，您可以同時為電腦設定 Azure Site Recovery 和 Hyper-v 複本。 但電腦必須以實體機器的形式受到保護，並使用設定/進程伺服器複寫至 Azure。 若要深入瞭解如何保護 [實體機器，](https://docs.microsoft.com/azure/site-recovery/physical-azure-architecture)請參閱。

### <a name="can-i-extend-replication"></a>我可以延伸複寫嗎？
不支援延伸的或鏈結的複寫。 請在 [意見反應論壇](https://feedback.azure.com/forums/256299-site-recovery/suggestions/6097959)中提出這項功能的要求。

### <a name="can-i-do-an-offline-initial-replication"></a>是否可執行離線初始複寫？
不支援此做法。 請在 [意見反應論壇](https://feedback.azure.com/forums/256299-site-recovery/suggestions/6227386-support-for-offline-replication-data-transfer-from)中提出這項功能的要求。

### <a name="can-i-exclude-disks"></a>是否可排除磁碟嗎？
是的，您可以將磁碟排除於複寫外。 

### <a name="can-i-replicate-vms-with-dynamic-disks"></a>是否可使用動態磁碟來複寫 VM？
動態磁碟可以複寫。 作業系統磁碟必須是基本磁碟。



## <a name="security"></a>安全性

### <a name="what-access-does-site-recovery-need-to-hyper-v-hosts"></a>Site Recovery 需要 Hyper-V 主機的哪些存取權

Site Recovery 需要存取 Hyper-V 主機以複寫您選取的 VM。 Site Recovery 會在 Hyper-V 主機上安裝下列項目：

- 若您未執行 VMM，Azure Site Recovery Provider 與復原服務代理程式會安裝在每一部主機上。
- 若您執行 VMM，復原服務代理程式會安裝在每一部主機上。 Provider 是在 VMM 伺服器上執行。


### <a name="what-does-site-recovery-install-on-hyper-v-vms"></a>Site Recovery 在 Hyper-V VM 上安裝哪些項目？

Site Recovery 不會在已針對複寫啟用的 Hyper-V VM 上安裝任何項目。




## <a name="failover-and-failback"></a>容錯移轉和容錯回復


### <a name="how-do-i-fail-over-to-azure"></a>如何容錯移轉至 Azure？

您可以執行從內部部署 Hyper-V VM 至 Azure 的計劃性或非計劃性容錯移轉。

- 如果您執行計劃性容錯移轉，則來源 VM 會關閉以確保不會遺失資料。
- 如果無法存取您的主要網站，您可以執行非計劃性容錯移轉。
- 您可以容錯移轉單一機器，或建立復原方案來協調多部機器的容錯移轉。
- 容錯移轉分為兩個部分：
    - 完成第一階段的容錯移轉之後，您應會在 Azure 中看到所建立的複本 VM。 如有必要，您可以對 VM 指派公用 IP 位址。
    - 然後，您要認可讓容錯移轉開始存取來自複本 Azure VM 的工作負載。
   

### <a name="how-do-i-access-azure-vms-after-failover"></a>在容錯移轉之後如何存取 Azure VM？
容錯移轉後，您可以透過安全的網際網路連線、透過站對站 VPN 或透過 Azure ExpressRoute 存取 Azure VM。 您必須做好一些準備才能連線。 [深入了解](failover-failback-overview.md#connect-to-azure-after-failover)。

### <a name="is-failed-over-data-resilient"></a>容錯移轉後的資料可復原嗎？
Azure 是針對復原能力而設計的。 Site Recovery 設計成可根據 Azure SLA 容錯移轉至次要 Azure 資料中心。 當容錯移轉發生時，我們會確保您的中繼資料和保存庫都保留在您為保存庫選擇的相同地理區域中。

### <a name="is-failover-automatic"></a>容錯移轉是自動發生的嗎？
[容錯移轉](site-recovery-failover.md) 不會自動進行。 您可以在入口網站中按一下單鍵來起始容錯移轉，也可以使用 [PowerShell](/powershell/module/az.recoveryservices) 來觸發容錯移轉。

### <a name="how-do-i-fail-back"></a>如何容錯回復？

您的內部部署基礎結構再次啟動並執行之後，您可以進行容錯回復。 容錯回復分為三個階段：

1. 使用一些不同的選項開始執行從 Azure 至內部部署網站的計劃性容錯移轉：

    - 將停機時間最小化：如果您使用這個選項，Site Recovery 會在容錯移轉前同步處理資料。 它會檢查變更的資料區塊並將其下載到內部部署網站，而 Azure VM 會保持執行，以將停機時間最小化。 當您手動指定應該完成的容錯移轉時，系統會關閉 Azure VM，複製任何最終的差異變更，而容錯移轉會開始。
    - 完整下載：使用此選項，資料會在容錯移轉期間進行同步處理。 此選項會下載整個磁碟。 它的速度更快，因為未計算任何總和檢查碼，但有更多的停機時間。 如果您已執行複本 Azure VM 一段時間，或已刪除內部部署 VM，請使用這個選項。

2. 您可以選擇容錯回復到相同的 VM 或替代 VM。 您可指定 Site Recovery 應該建立 VM (如果尚未存在)。
3. 初始同步處理完成之後，您可選擇完成容錯移轉。 完成之後，您可以登入內部部署 VM 以檢查一切是否如預期般運作。 在 Azure 入口網站中，您可以看到 Azure VM 已停止。
4. 您要認可讓容錯移轉結束，並再次開始存取來自內部部署 VM 的工作負載。
5. 容錯回復工作負載之後，您會啟用反向複寫，以便內部部署 VM 再次複寫至 Azure。

### <a name="can-i-fail-back-to-a-different-location"></a>是否可容錯回復至不同的位置？
是的，在容錯移轉至 Azure 之後，如果原始位置無法使用，您可以容錯回復至不同的位置。 [深入了解](hyper-v-azure-failback.md#fail-back-to-an-alternate-location)。
