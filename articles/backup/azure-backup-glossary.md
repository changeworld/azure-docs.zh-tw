---
title: Azure 備份詞彙
description: 本文定義有助於搭配 Azure 備份使用的術語。
ms.topic: conceptual
ms.date: 12/21/2020
ms.openlocfilehash: 8baa47667e86b99ebbbf273610809814e768c077
ms.sourcegitcommit: a89a517622a3886b3a44ed42839d41a301c786e0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/22/2020
ms.locfileid: "97733321"
---
# <a name="azure-backup-glossary"></a>Azure 備份詞彙

使用 Azure 備份時，此詞彙詞彙可能很有説明。

>[!NOTE]
>
> - 標示「 (工作負載專屬詞彙) 」前置詞的詞彙，指的是僅與 Azure 備份支援之特定工作負載子集內容相關的詞彙。
> - 針對 Azure 備份檔中經常使用但參考其他 Azure 服務的詞彙，會提供相關 Azure 服務的相關檔的直接連結。

## <a name="afs-azure-file-shares"></a>Azure 檔案共用) 的 AFS (

請參閱 [Azure 檔案儲存體檔](https://docs.microsoft.com/azure/storage/files/storage-files-introduction)。

## <a name="alternate-location-recovery"></a>替代位置復原

從復原點完成復原到原始位置，而不是建立備份的位置。 使用 Azure VM 備份時，這表示將 VM 還原到執行備份的源伺服器以外的伺服器。 使用 Azure 檔案共用備份時，這表示將資料還原至與備份檔案共用不同的檔案共用。

## <a name="application-consistent-backup"></a>應用程式一致備份

 (工作負載特定的詞彙) 

應用程式一致備份會捕捉記憶體內容和暫止 i/o 作業。 應用程式一致快照會使用 [vss](#vss-windows-volume-shadow-copy-service) vss 寫入器 (或適用于 Linux) 的前置或後置腳本，以確保在進行備份之前，應用程式資料的一致性。 [深入了解](backup-azure-vms-introduction.md)。

## <a name="azure-resource-manager-arm-templates"></a>Azure Resource Manager (ARM) 範本

請參閱 [ARM 範本檔](https://docs.microsoft.com/azure/azure-resource-manager/templates/overview)。

## <a name="autoprotection-for-databases"></a>適用于資料庫) 的 start-autoprotection (

 (工作負載特定的詞彙) 

Start-autoprotection 是一項功能，可讓您自動保護獨立 SQL Server 實例或 SQL Server Always On 可用性群組中的所有資料庫。 它不僅會為現有的資料庫啟用備份，還會保護您未來可能加入的所有資料庫。

## <a name="availability-storage-replication-types"></a>可用性 (儲存體複寫類型) 

Azure 備份提供三種類型的複寫，讓您的儲存體和資料具有高可用性：

### <a name="lrs"></a>LRS

[本機冗余儲存體 (LRS) ](https://docs.microsoft.com/azure/storage/common/storage-redundancy#locally-redundant-storage) 複寫您的備份資料三次 (它會在資料中心的儲存體縮放單位中建立三份備份資料) 。 備份資料的所有複本都存在於相同的區域中。 LRS 是一種低成本的選項，可保護您的備份資料免于本機硬體失敗。

### <a name="grs"></a>GRS

[異地備援儲存體 (GRS)](https://docs.microsoft.com/azure/storage/common/storage-redundancy#geo-redundant-storage) 是預設且建議使用的複寫選項。 GRS 會將您的備份資料複寫到與來源資料的主要位置相距數百英里的次要區域。 GRS 成本超過 LRS，但 GRS 可針對您的備份資料提供較高的持久性層級，即使發生區域中斷也是如此。

>[!NOTE]
>若為已啟用跨區域還原功能的 GRS 保存庫，則會將備份儲存體從 GRS 升級至 GRS (讀取存取權 Geo-Redundant 儲存體) 。

### <a name="zrs"></a>ZRS

[區域冗余儲存體 (ZRS) ](https://docs.microsoft.com/azure/storage/common/storage-redundancy#zone-redundant-storage) 會將備份資料複寫至 [可用性區域](https://docs.microsoft.com/azure/availability-zones/az-overview#availability-zones)，以保證相同區域中的備份資料存放區和復原。 因此，您可以在 ZRS 中備份需要 [資料](https://azure.microsoft.com/resources/achieving-compliant-data-residency-and-security-with-azure/) 存放區的重要工作負載。

## <a name="azure-command-line-interface-cli"></a>Azure 命令列介面 (CLI)

請參閱 [Azure CLI 檔](https://docs.microsoft.com/cli/azure/what-is-azure-cli)。

## <a name="azure-policy"></a>Azure 原則

請參閱 [Azure 原則檔](https://docs.microsoft.com/azure/governance/policy/overview)。

## <a name="azure-powershell"></a>Azure PowerShell

請參閱 [Azure PowerShell 檔](https://docs.microsoft.com/powershell/azure/)。

## <a name="azure-resource-manager-arm"></a>Azure Resource Manager (ARM) 

請參閱 [Azure Resource Manager 檔](https://docs.microsoft.com/azure/azure-resource-manager/management/overview)。

## <a name="azure-disk-encryption-ade"></a>Azure 磁碟加密 (ADE)

請參閱 [Azure 磁碟加密檔](https://docs.microsoft.com/azure/security/fundamentals/azure-disk-encryption-vms-vmss)。

## <a name="backend-storage--cloud-storage--backup-storage"></a>後端儲存體/雲端儲存體/備份儲存體

備份實例所使用的實際儲存空間。 包含備份實例 (的所有保留點大小，如備份和保留原則) 所定義。

## <a name="bare-metal-backup"></a>裸機備份

備份作業系統檔案以及重要磁片區上的所有資料，但使用者資料除外。 根據定義，裸機備份包含系統狀態備份。 當電腦無法啟動，而且您必須復原所有項目時，裸機備份便可提供保護。 [深入了解](backup-mabs-system-state-and-bmr.md)。

## <a name="backup-extensions--vm-extensions"></a>備份延伸模組/VM 擴充功能

適用于 Azure VM 工作負載類型的 () 

Azure 虛擬機器 (VM) 擴充功能是小型的應用程式，可在 Azure 虛擬機器上提供部署後設定及自動化工作。 為機器上執行的 Azure VM 代理程式安裝擴充功能，以 Azure 備份來備份 Azure VM。 Azure 備份會自動管理這些延伸模組，而且使用者不需要手動更新這些延伸模組。

## <a name="backup-instance--backup-item"></a>備份實例/備份專案

備份至保存庫的資料來源具有特定的備份和保留原則，構成了備份實例或備份專案。

## <a name="backup-rule--backup-policy"></a>備份規則/備份原則

備份規則是一種使用者定義的規則，可指定在資料來源上執行備份的時間和頻率。 針對某些工作負載類型，備份原則也提供方法來指定要套用至資料來源的快照集方法 (完整、累加、差異) 。 備份原則通常會建立為備份規則和保留規則的組合。

## <a name="backup-vault"></a>備份保存庫

*DataProtection/BackupVaults* 類型的 Azure Resource Manager 資源。 目前，備份保存庫是用來備份適用于于 postgresql 伺服器的 Azure 資料庫。 [深入瞭解備份保存庫](backup-azure-recovery-services-vault-overview.md)。

## <a name="bcdr-business-continuity-and-disaster-recovery"></a>BCDR (商務持續性和嚴重損壞修復) 

BCDR 牽涉到一組組織必須採用的流程，以確保應用程式和工作負載在規劃和未規劃的服務或 Azure 中斷期間都已啟動並執行，且最少的業務中斷。 [深入瞭解](https://azure.microsoft.com/solutions/backup-and-disaster-recovery/#overview) Azure 提供的各種服務，以協助您建立音效 BCDR 策略。

## <a name="churn"></a>流失

在兩個連續備份之間進行備份之資料的變更百分比。 這可能是因為新增了新資料，或是修改或刪除現有的資料。

## <a name="crash-consistent-backup"></a>損毀一致備份

 (工作負載特定的詞彙) 

當 Azure VM 在備份時關閉時，通常會發生損毀一致的快照集。 只會擷取備份時已存在磁碟上的資料，並加以備份。 [深入了解](backup-azure-vms-introduction.md#snapshot-consistency)。

## <a name="cross-region-restore-crr"></a>跨區域還原 (CRR) 

作為其中一個 [還原選項](backup-azure-arm-restore-vms.md#restore-options)，跨區域還原 (CRR) 可讓您在次要區域（也就是 [Azure 配對的區域](https://docs.microsoft.com/azure/best-practices-availability-paired-regions#what-are-paired-regions)）中還原備份專案。

## <a name="data-box"></a>資料箱

請參閱 [資料箱檔](https://docs.microsoft.com/azure/databox/data-box-overview)。

## <a name="datasource"></a>資料來源

 (Azure 資源、proxy 資源或內部部署資源) 的資源，這是要備份的候選項。 例如，Azure VM 或 Azure 檔案共用。

## <a name="dpm-data-protection-manager"></a>DPM (Data Protection Manager) 

 (工作負載特定的詞彙) 

請參閱 [DPM 檔](https://docs.microsoft.com/system-center/dpm/dpm-overview)。

## <a name="expressroute"></a>ExpressRoute

請參閱 [ExpressRoute 檔](https://docs.microsoft.com/azure/expressroute/expressroute-introduction)。

## <a name="file-system-consistent-backup"></a>檔案系統一致備份

 (工作負載特定的詞彙) 

檔案系統一致備份可同時取得所有檔案的快照集，以提供一致性。 [深入了解](backup-azure-vms-introduction.md#snapshot-consistency)。

## <a name="frontend-storage--source-size"></a>前端儲存體/來源大小

要針對資料來源備份的資料大小。 資料來源的前端大小會決定其 [受保護的實例](#protected-instance) 計數。

## <a name="full-backup"></a>完整備份

在完整備份中，會針對每個備份儲存整個資料來源的複本。

## <a name="gfs-backup-policy"></a>GFS 備份原則

GFS (祖父-父親) 備份原則可讓您定義每日、每月和每年備份排程，以及每日備份排程。 每週備份稱為「兒子」，每月備份稱為「fathers」，而年度備份稱為「grandfathers」。 每一組備份複本都可以設定為在不同的期間內保留，以針對備份複本進行保留選擇的更多自訂。 GFS 原則有助於以更具儲存效率的方式保留備份一段很長的時間。

## <a name="iaas-vms--azure-vms"></a>IaaS Vm/Azure Vm

請參閱 [AZURE VM 檔](https://docs.microsoft.com/azure/virtual-machines/)。

## <a name="incremental-backup"></a>增量備份

增量備份只會儲存自從上次備份後已變更的區塊。

## <a name="instant-restore"></a>立即還原

立即還原牽涉到直接從備份快照集還原電腦，而不是從保存庫中的快照集複本還原。 立即還原比從保存庫還原還快。 可用的立即還原點數目取決於針對快照集所設定的保留期間。

## <a name="iops"></a>IOPS

每秒的輸入/輸出作業數。

## <a name="item-level-restore"></a>專案層級還原

 (工作負載特定的詞彙) 

從復原點還原機器內的個別檔案或資料夾。

## <a name="job"></a>工作 (Job)

由使用者或 Azure 備份服務所建立的備份相關工作。 作業可以是已排程或視需要 (臨機操作) 。 有不同類型的作業-備份、還原、設定保護等等。 [深入瞭解作業](backup-azure-monitoring-built-in-monitor.md#backup-jobs-in-recovery-services-vault)。

## <a name="mabs--azure-backup-server"></a>MABS/Azure 備份伺服器

 (工作負載特定的詞彙) 

透過 Azure 備份伺服器，您將可從單一主控台保護 Hyper-V VM、Microsoft SQL Server、SharePoint Server、Microsoft Exchange 和 Windows 用戶端等應用程式工作負載。 它會從 DPM 繼承大部分的工作負載備份功能，但有幾項差異。 [深入了解](backup-azure-microsoft-azure-backup.md)

## <a name="managed-disks"></a>受控磁碟

請參閱 [受控磁片檔](https://docs.microsoft.com/azure/virtual-machines/managed-disks-overview)。

## <a name="mars-agent"></a>MARS 代理程式

 (工作負載特定的詞彙) 

也稱為 **Azure 備份代理** 程式或復原 **服務代理程式**，Azure 備份用來將資料從內部部署機器和 Azure vm 備份至 azure 中的備份復原服務保存庫。 [深入了解](backup-support-matrix-mars-agent.md)。

## <a name="nsg-network-security-group"></a>NSG (網路安全性群組) 

請參閱 [NSG 檔](https://docs.microsoft.com/azure/virtual-network/network-security-groups-overview)。

## <a name="offline-seeding"></a>離線植入

離線植入是指在不使用網路頻寬的情況下，離線傳送初始 (完整) 備份的進程。 它提供一種機制，可將備份資料複製到實體儲存體裝置上，然後再寄送到附近的 Azure 資料中心，並上傳至復原服務保存庫。 [深入了解](offline-backup-overview.md)。

## <a name="on-demand-backup--ad-hoc-backup"></a>隨選備份/臨機操作備份

由使用者在單次需求觸發的備份工作，而不是根據已針對資源設定 (原則) 的備份排程。

## <a name="original-location-recovery-olr"></a>原始位置復原 (OLR) 

從還原點完成的復原會從備份的來源位置開始，並以復原點中儲存的狀態取代。 使用 Azure VM 備份時，這表示將 VM 還原到執行備份的源伺服器。 使用 Azure 檔案共用備份時，這會表示將資料還原至備份的檔案共用。

## <a name="passphrase"></a>複雜密碼

 (工作負載特定的詞彙) 

使用 MARS 代理程式在 Azure 中備份或還原內部部署或本機電腦時，會使用複雜密碼來加密和解密資料。

## <a name="point-in-time-restore"></a>還原時間點

將專案還原至特定時間點的狀態 (PIT) 。

## <a name="private-endpoint"></a>私人端點

請參閱 [私人端點檔](https://docs.microsoft.com/azure/private-link/private-endpoint-overview)集。

## <a name="protected-instance"></a>受保護的實例

受保護的實例是指您用來設定備份至 Azure 的電腦、實體或虛擬伺服器。  從 **計費的觀點來看**，電腦的受保護實例計數是其前端大小的函式。 [深入了解](https://azure.microsoft.com/pricing/details/backup/)。

## <a name="rbac-role-based-access-control"></a>RBAC (角色型存取控制) 

請參閱 [RBAC 檔](https://docs.microsoft.com/azure/role-based-access-control/overview)。

## <a name="recovery-point-restore-point-retention-point"></a>復原點/還原點/保留點

正在備份的原始資料複本。 保留點會與時間戳記相關聯，因此您可以使用此保留點將專案還原至特定時間點。

## <a name="recovery-services-vault"></a>復原服務保存庫

*Az.recoveryservices/保存庫* 類型的 Azure Resource Manager 資源。 目前，復原服務保存庫是用來備份下列工作負載： azure Vm、Azure Vm 中的 SQL、Azure Vm 中的 SAP Hana，以及 Azure 檔案共用。 它也可用來備份使用 MARS、Azure 備份伺服器 (MABS) 和 System Center DPM 的內部部署工作負載。 [深入瞭解復原服務保存庫](backup-azure-recovery-services-vault-overview.md)。

## <a name="resource-group"></a>資源群組

請參閱 [Azure Resource Manager 檔](https://docs.microsoft.com/azure/azure-resource-manager/management/manage-resource-groups-portal#what-is-a-resource-group)。

## <a name="rest-api"></a>REST API

請參閱 [Azure REST API 檔](https://docs.microsoft.com/rest/api/azure/)。

## <a name="retention-rule"></a>保留規則

使用者定義的規則，指定備份應保留的時間長度。

## <a name="rpo-recovery-point-objective"></a>RPO (復原點目標) 

RPO 表示資料遺失案例中可接受的最大資料遺失。 這取決於備份頻率。

## <a name="rto-recovery-time-objective"></a>RTO (復原時間目標) 

RTO 表示資料遺失案例之後，可以將資料還原到最後一個可用時間點的最大可接受時間。

## <a name="scheduled-backup"></a>排定的備份

針對指定專案所設定的備份原則自動觸發的備份工作。

## <a name="secondary-region--paired-region"></a>次要區域/配對區域

區域配對是由相同地理位置內的兩個區域所組成。 其中一個是主要區域，另一個則是次要區域。 某些 Azure 服務會使用配對的區域 (包括具有 GRS 設定的 Azure 備份) 以確保業務持續性，並防止資料遺失。 [深入了解](https://docs.microsoft.com/azure/best-practices-availability-paired-regions)。

## <a name="soft-delete"></a>虛刪除

虛刪除是有助於防止意外刪除備份資料的功能。 使用虛刪除時，即使惡意執行者刪除備份 (或不小心刪除備份資料) ，備份資料仍會保留一段額外的時間，允許復原該備份專案，而不會遺失資料。 [深入了解](backup-azure-security-feature-cloud.md)。

## <a name="snapshot"></a>快照式

快照集是完整的虛擬硬碟 (VHD) 唯讀複本。 [深入了解](https://docs.microsoft.com/azure/virtual-machines/windows/snapshot-copy-managed-disk)。

## <a name="storage-account"></a>儲存體帳戶

請參閱 [儲存體帳戶檔](https://docs.microsoft.com/azure/storage/common/storage-account-overview)。

## <a name="subscription"></a>訂用帳戶

Azure 訂用帳戶是用來在 Azure 中佈建資源的邏輯容器。 它會保存您所有資源的詳細資料，例如虛擬機器 (Vm) 、資料庫等等。

## <a name="system-state-backup"></a>系統狀態備份

 (工作負載特定的詞彙) 

備份作業系統檔案。 此備份可讓您在電腦啟動時復原，但系統檔案和登錄會遺失。 [深入了解](backup-mabs-system-state-and-bmr.md)。

## <a name="tenant"></a>租用戶

租用戶是組織的代表。 它是組織或應用程式開發人員在與 Microsoft 建立關聯性 (像是註冊 Azure、Microsoft Intune 或 Microsoft 365) 時收到的 Azure AD 專屬執行個體。

## <a name="unmanaged-disk"></a>非受控磁碟

請參閱 [非受控磁片檔](https://docs.microsoft.com/azure/storage/common/storage-disaster-recovery-guidance#azure-unmanaged-disks)。

## <a name="vault"></a>保存庫

Azure 中存放備份資料的儲存實體。 它也是 RBAC 和帳單的單位。 目前有兩種類型的保存庫-復原服務保存庫和備份保存庫。

## <a name="vault-credentials"></a>保存庫認證

保存庫認證檔是入口網站針對每個保存庫所產生的憑證。 將伺服器登錄至保存庫時，會使用此功能。 [深入了解](backup-azure-dpm-introduction.md)。

## <a name="vnet-virtual-network"></a>VNET (虛擬網路) 

請參閱 [VNET 檔](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview)。

## <a name="vss-windows-volume-shadow-copy-service"></a>VSS (Windows 磁碟區陰影複製服務) 

請參閱 [VSS 檔](https://docs.microsoft.com/windows-server/storage/file-server/volume-shadow-copy-service)。

## <a name="next-steps"></a>後續步驟

- [Azure 備份總覽](backup-overview.md)
- [Azure 備份的架構與元件](backup-architecture.md)
