---
title: 常見問題的解答
description: '有關以下常見問題的解答：包括復原服務保存庫、可以備份的項目、其運作方式、加密和限制等 Azure 備份功能。 '
ms.topic: conceptual
ms.date: 07/07/2019
ms.openlocfilehash: 50cbd1d28ca3f04b8963c58b443765d4280b14ed
ms.sourcegitcommit: 1b47921ae4298e7992c856b82cb8263470e9e6f9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/14/2020
ms.locfileid: "92058075"
---
# <a name="azure-backup---frequently-asked-questions"></a>Azure 備份 - 常見問題集

本文提供「Azure 備份」服務的常見問題解答。

## <a name="recovery-services-vault"></a>復原服務保存庫

### <a name="is-there-any-limit-on-the-number-of-vaults-that-can-be-created-in-each-azure-subscription"></a>每個 Azure 訂用帳戶中可以建立的保存庫數目是否有任何限制？

是。 您可以為每個訂用帳戶的 Azure 備份支援區域，最多建立 500 個復原服務保存庫。 如果您需要其他保存庫，請建立其他訂用帳戶。

### <a name="are-there-limits-on-the-number-of-serversmachines-that-can-be-registered-against-each-vault"></a>針對每個保存庫註冊的伺服器/電腦具有數目限制嗎？

每個保存庫可以註冊最多 1000 個 Azure 虛擬機器。 如果您使用 Microsoft Azure 備份代理程式，則每個保存庫最多可註冊50個 MARS 代理程式。 您可以向保存庫註冊 50 MABS 伺服器/DPM 服務器。

### <a name="how-many-datasourcesitems-can-be-protected-in-a-vault"></a>保存庫中可以保護多少個資料來源/項目？

您可以跨所有工作負載保護最多2000個數據源/專案 (例如，在保存庫中的 IaaS VM、SQL、AFS) 。
例如，如果您已在保存庫中保護 500 Vm 和 400 Azure 檔案儲存體共用，您只能保護其中最多1100個 SQL 資料庫。

### <a name="how-many-policies-can-i-create-per-vault"></a>我可以為每個保存庫建立多少個原則？

每個保存庫最多只能有 200 個原則。

### <a name="if-my-organization-has-one-vault-how-can-i-isolate-data-from-different-servers-in-the-vault-when-restoring-data"></a>如果我的組織有一個保存庫，如何在還原資料時，將來自不同伺服器的資料隔離？

當您設定備份時，要一起復原的伺服器資料應該使用相同的複雜密碼。 如果您只想要復原特定的伺服器，請使用該伺服器專用的複雜密碼。 例如，人力資源伺服器可能使用一組加密複雜密碼，而會計伺服器使用另一組，並且儲存體伺服器使用第三組。

### <a name="can-i-move-my-vault-between-subscriptions"></a>我是否可以在訂用帳戶之間移動保存庫？

是。 若要移動復原服務保存庫，請參閱此[文章](backup-azure-move-recovery-services-vault.md)

### <a name="can-i-move-backup-data-to-another-vault"></a>我是否可以將備份資料移至另一個保存庫？

否。 儲存在保存庫中的備份資料無法移至不同的保存庫。

### <a name="can-i-change-the-storage-redundancy-setting-after-a-backup"></a>我可以在備份之後變更儲存體冗余設定嗎？

根據預設，儲存體複寫類型會設定為異地冗余儲存體 (GRS) 。 設定備份之後，[修改] 選項會停用且無法變更。

![儲存體複寫類型](./media/backup-azure-backup-faq/storage-replication-type.png)

如果您已設定備份，且必須從 GRS 移至 LRS，則在設定 [備份之後，請參閱如何從 GRS 變更為 LRS](backup-create-rs-vault.md#how-to-change-from-grs-to-lrs-after-configuring-backup)。

### <a name="can-i-do-an-item-level-restore-ilr-for-vms-backed-up-to-a-recovery-services-vault"></a>我是否可以針對備份到「復原服務保存庫」的 VM 執行「項目層級還原」(ILR)？

- 由 Azure VM 備份所備份的 Azure VM 支援 Azure VM。 如需詳細資訊，請參閱[文章](backup-azure-restore-files-from-vm.md)
- Azure 備份伺服器 (MABS) 或 System Center DPM 所備份的內部部署 Vm 線上復原點不支援 ILR。

## <a name="azure-backup-agent"></a>Azure 備份代理程式

### <a name="where-can-i-find-common-questions-about-the-azure-backup-agent-for-azure-vm-backup"></a>哪裡可以找到有關適用於 Azure VM 備份之 Azure 備份代理程式的常見問題？

- 針對在 Azure VM 上執行的代理程式，請參閱這個[常見問題集](backup-azure-vm-backup-faq.md)。
- 針對用來備份 Azure 檔案資料夾的代理程式，請參閱這個[常見問題集](backup-azure-file-folder-backup-faq.md)。

## <a name="general-backup"></a>一般備份

### <a name="are-there-limits-on-backup-scheduling"></a>在備份排程方面是否有任何限制？

是。

- 您一天最多可以備份 Windows Server 或 Windows 機器三次。 您可以將排程原則設定為每天或每週排程。
- 您一天最多可以備份 DPM 兩次。 您可以將排程原則設定為每天、每週、每月及每年。
- 您一天可以備份 Azure VM 一次。

### <a name="what-operating-systems-are-supported-for-backup"></a>支援使用哪些作業系統來進行備份？

「Azure 備份」支援使用下列作業系統來備份檔案和資料夾，以及受「Azure 備份伺服器」和 DPM 保護的應用程式。

**作業系統** | **SKU** | **詳細資料**
--- | --- | ---
工作站 | |
Windows 10 64 位元 | 企業版、專業版、家用版 | 機器應該執行最新的服務套件和更新。
Windows 8.1 64 位元 | Enterprise、Pro | 機器應該執行最新的服務套件和更新。
Windows 8 64 位元 | Enterprise、Pro | 機器應該執行最新的服務套件和更新。
Windows 7 64 位元 | Ultimate、Enterprise、Professional、Home Premium、Home Basic、Starter | 機器應該執行最新的服務套件和更新。
伺服器 | |
Windows Server 2019 64 位元 | Standard、Datacenter、Essentials | 含最新的服務套件/更新。
Windows Server 2016 64 位元 | Standard、Datacenter、Essentials | 含最新的服務套件/更新。
Windows Server 2012 R2 64 位元 | Standard、Datacenter、Foundation | 含最新的服務套件/更新。
Windows Server 2012 64 位元 | Datacenter、Foundation、Standard | 含最新的服務套件/更新。
Windows Storage Server 2016 64 位元 | Standard、Workgroup | 含最新的服務套件/更新。
Windows Storage Server 2012 R2 64 位元 | Standard、Workgroup、Essential | 含最新的服務套件/更新。
Windows Storage Server 2012 64 位元 | Standard、Workgroup | 含最新的服務套件/更新。
Windows Server 2008 R2 SP1 64 位元 | Standard、Enterprise、Datacenter、Foundation | 含最新的更新。
Windows Server 2008 64 位元 | Standard、Enterprise、Datacenter | 含最新的更新。

Azure 備份不支援 32 位元作業系統。

針對 Azure VM Linux 備份，Azure 備份支援 [Azure 所背書的散發套件清單](../virtual-machines/linux/endorsed-distros.md)，但 Core OS Linux 和 32 位元作業系統除外。 只要 VM 上有 VM 代理程式可用並可支援 Python，其他自備的 Linux 散發套件便可能可以運作。

### <a name="are-there-size-limits-for-data-backup"></a>資料備份是否有大小限制？

大小限制如下：

OS/機器 | 資料來源的大小限制
--- | ---
Windows 8 或更新版本 | 54,400 GB
Windows 7 |1700 GB
Windows Server 2012 或更新版本 | 54,400 GB
Windows Server 2008、Windows Server 2008 R2 | 1700 GB
Azure VM | 請參閱 [AZURE VM 備份的支援矩陣](./backup-support-matrix-iaas.md#vm-storage-support)

### <a name="how-is-the-data-source-size-determined"></a>如何判斷資料來源大小？

下表說明如何決定每個資料來源大小。

**資料來源** | **詳細資料**
--- | ---
磁碟區 |從所要備份之單一磁碟區 VM 備份的資料量。
SQL Server 資料庫 |要備份之單一資料庫大小的大小。
SharePoint | 所要備份之 SharePoint 伺服陣列中內容和設定資料庫的總和。
Exchange |所要備份之 Exchange Server 中所有 Exchange 資料庫的總和。
BMR/系統狀態 |所要備份之機器的 BMR 或系統狀態的每個個別複本。

### <a name="is-there-a-limit-on-the-amount-of-data-backed-up-using-a-recovery-services-vault"></a>使用「復原服務保存庫」來備份的資料量是否有限制？

您可以使用復原服務保存庫來備份的總數據量沒有任何限制。  (Azure Vm) 以外的個別資料來源，其大小上限為 54400 GB。 如需有關限制的詳細資訊，請參閱 [支援矩陣中的保存庫限制一節](./backup-support-matrix.md#vault-support)。

### <a name="why-is-the-size-of-the-data-transferred-to-the-recovery-services-vault-smaller-than-the-data-selected-for-backup"></a>為何傳輸到「復原服務保存庫」的資料大小會小於所選取要備份的資料？

從「Azure 備份代理程式」、DPM 或「Azure 備份伺服器」備份的資料會先經過壓縮和加密，再進行傳輸。 在套用壓縮和加密之後，保存庫中的資料會縮小 30-40%。

### <a name="can-i-delete-individual-files-from-a-recovery-point-in-the-vault"></a>可以從保存庫中的復原點刪除個別的檔案嗎？

否，Azure 備份不支援從儲存的備份中刪除或清除個別項目。

### <a name="if-i-cancel-a-backup-job-after-it-starts-is-the-transferred-backup-data-deleted"></a>如果我在備份作業開始後取消作業，是否會刪除已傳輸的備份資料？

否。 所有在備份作業取消前傳輸到保存庫的資料都會保留在保存庫中。

- Azure 備份會使用檢查點機制，在備份期間偶爾將檢查點加入至備份資料。
- 因為備份資料中有檢查點，所以下一個備份程序才可驗證檔案的完整性。
- 下一個備份工作將會增量到先前備份的資料。 增量備份只會傳輸新資料或變更的資料，相當於具有較佳的頻寬使用率。

如果您取消 Azure VM 的備份工作，則會忽略任何傳輸的資料。 下一個備份工作會傳輸自最後一個成功備份工作之後的增量資料。

## <a name="retention-and-recovery"></a>保留和復原

### <a name="are-the-retention-policies-for-dpm-and-windows-machines-without-dpm-the-same"></a>就 DPM 與沒有 DPM 的 Windows 機器而言，兩者的保留原則是否相同？

是，兩者都有每日、每週、每月及每年保留原則。

### <a name="can-i-customize-retention-policies"></a>我是否可以自訂保留原則？

是，您可以自訂原則。 例如，您可以設定每週和每天保留需求，但無法設定每年和每月保留需求。

### <a name="can-i-use-different-times-for-backup-scheduling-and-retention-policies"></a>我是否可以針對備份排程和保留原則使用不同的時間？

否。 保留原則僅能套用在復原點上。 例如，下圖顯示在上午 12:00 和下午 6:00 進行之備份的保留原則。

![排程備份和保留](./media/backup-azure-backup-faq/Schedule.png)

### <a name="if-a-backup-is-kept-for-a-long-time-does-it-take-more-time-to-recover-an-older-data-point"></a>如果備份保留了很長一段時間，是否需要較多時間才能復原較舊的資料點？

否。 復原最舊或最新時間點所需的時間都相同。 每個復原點的功能就像一個完整的復原點。

### <a name="if-each-recovery-point-is-like-a-full-point-does-it-impact-the-total-billable-backup-storage"></a>若每個復原點就像一個完整的復原點，則其是否會影響可計費的備份儲存體總數？

典型的長期保留復原點產品會將備份資料儲存為完整的復原點。

- 完整的復原點是「效率不佳的」 ** 儲存體，但可以更輕鬆且更快速地進行還原。
- 增量複本「符合儲存體效益」**，但需要您還原一連串的資料，而這會影響復原時間

Azure 備份的儲存體架構透過最佳化儲存資料以進行快速還原，並降低儲存體成本支出，可讓您魚與熊掌兼得。 這可確保有效率地使用您的輸入和輸出頻寬。 資料儲存體數量及復原資料所需的時間都會維持在最低。 深入了解[增量備份](backup-architecture.md#backup-types)。

### <a name="is-there-a-limit-on-the-number-of-recovery-points-that-can-be-created"></a>可建立的復原點數目有限制嗎？

您可以為每個受保護的執行個體最多建立 9999 個復原點。 受保護的執行個體係指會備份至 Azure 的電腦、伺服器 (實體或虛擬) 或工作負載。

- 深入了解[備份和保留](./backup-support-matrix.md)。

### <a name="how-many-times-can-i-recover-data-thats-backed-up-to-azure"></a>我可以將備份至 Azure 的資料復原幾次？

Azure 備份的復原數量沒有任何限制。

### <a name="when-restoring-data-do-i-pay-for-the-egress-traffic-from-azure"></a>還原資料時，我需要支付來自 Azure 的輸出流量嗎？

否。 復原是免費的，不會向您收取輸出流量的費用。

### <a name="what-happens-when-i-change-my-backup-policy"></a>變更我的備份原則時會發生什麼狀況？

套用新原則後，就會遵循新原則的排程和保留期。

- 如果延長保留期，則會標示現有的復原點，以根據新的原則加以保留。
- 如果縮短保留期，則會標示現有的復原點，以便在下次清除作業中剪除然後刪除。

### <a name="how-long-is-data-retained-when-stopping-backups-but-selecting-the-option-to-retain-backup-data"></a>停止備份時，資料保留的時間長度，但選取保留備份資料的選項？

當您停止備份並保留資料時，資料剪除的現有原則規則將會停止，而且資料將會無限期保留，直到系統管理員進行刪除為止。

## <a name="encryption"></a>加密

### <a name="is-the-data-sent-to-azure-encrypted"></a>傳送至 Azure 的資料會經過加密嗎？

是。 資料會在內部部署機器上以 AES256 加密。 資料會透過安全的 HTTPS 連結來傳送。 在雲端中傳輸的資料僅受到儲存體和復原服務之間的 HTTPS 連結保護。 iSCSI 通訊協定保護復原服務和使用者電腦之間傳輸的資料。 安全通道用於保護 iSCSI 通道。

### <a name="is-the-backup-data-on-azure-encrypted-as-well"></a>位於 Azure 的備份資料也會經過加密嗎？

是。 在 Azure 中的資料會進行靜態加密。

- 針對內部部署備份，會使用您在備份至 Azure 時所提供的複雜密碼來提供靜態加密。
- 針對 Azure VM，會使用「儲存體服務加密」(SSE) 對資料進行靜態加密。

Microsoft 不會在任何時間點解密備份資料。

### <a name="what-is-the-minimum-length-of-the-encryption-key-used-to-encrypt-backup-data"></a>用來加密備份資料的加密金鑰長度下限為何？

Microsoft Azure 復原服務 (MARS) 代理程式所使用的加密金鑰，是從長度至少為16個字元的複雜密碼所衍生。 針對 Azure Vm，Azure KeyVault 所使用的金鑰長度沒有限制。

### <a name="what-happens-if-i-misplace-the-encryption-key-can-i-recover-the-data-can-microsoft-recover-the-data"></a>如果我錯置加密金鑰，則會發生什麼情況？ 我是否可以復原資料？ Microsoft 是否可以復原資料？

用來加密備份資料的金鑰只存在於您的站台上。 Microsoft 不會在 Azure 中維護複本，也不會擁有金鑰的任何存取權。 如果您忘記將金鑰放在哪裡，Microsoft 並無法復原該備份資料。

## <a name="next-steps"></a>後續步驟

閱讀其他常見問題集：

- Azure VM 的相關[常見問題](backup-azure-vm-backup-faq.md)。
- 「Azure 備份」代理程式的相關[常見問題](backup-azure-file-folder-backup-faq.md)
