---
title: 架構概觀
description: 概略說明 Azure 備份服務所使用的架構、元件和程序。
ms.topic: conceptual
ms.date: 02/19/2019
ms.openlocfilehash: e70fe13e895315763ae305b48a72d688f09931f0
ms.sourcegitcommit: bdd5c76457b0f0504f4f679a316b959dcfabf1ef
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/22/2020
ms.locfileid: "90986482"
---
# <a name="azure-backup-architecture-and-components"></a>Azure 備份的架構與元件

您可以使用 [Azure 備份服務](backup-overview.md) ，將資料備份至 Microsoft Azure 雲端平臺。 本文將摘要說明 Azure 備份的架構、元件和程序。

## <a name="what-does-azure-backup-do"></a>Azure 備份有何功能？

Azure 備份會備份在內部部署機器和 Azure 虛擬機器上執行的資料、電腦狀態和工作負載 (VM) 實例。 Azure 備份有多種案例。

## <a name="how-does-azure-backup-work"></a>Azure 備份如何運作？

您可以使用數種方法來備份電腦和資料：

- **備份內部部署機器**：
  - 您可以使用 Azure 備份 Microsoft Azure 復原服務 (MARS) 代理程式，將內部部署 Windows 電腦直接備份至 Azure。 不支援 Linux 機器。
  - 您可以將內部部署機器備份至備份伺服器，也就是 System Center Data Protection Manager (DPM) 或 Microsoft Azure 備份 Server (MABS) 。 然後，您可以將備份伺服器備份至 Azure 中的復原服務保存庫。

- **備份 Azure vm**：
  - 您可以直接備份 Azure VM。 Azure 備份會將備份擴充功能安裝至 VM 上執行的 Azure VM 代理程式。 此擴充功能會備份整個 VM。
  - 您可以執行 MARS 代理程式來備份 Azure VM 上的特定檔案和資料夾。
  - 您可以將 Azure Vm 備份至在 Azure 中執行的 MABS，然後將 MABS 備份到復原服務保存庫。

深入瞭解 [您可以備份的內容](backup-overview.md) ，以及 [支援的備份案例](backup-support-matrix.md)。

## <a name="where-is-data-backed-up"></a>資料會備份至何處？

Azure 備份會將備份的資料儲存在保存庫中-復原服務保存庫和備份保存庫。 保存庫是 Azure 中的線上儲存實體，用來保存備份複本、復原點和備份原則等資料。

保存庫有下列功能：

- 保存庫可輕鬆地組織您的備份資料，同時可減輕管理負擔。
- 您可以監視保存庫中的備份專案，包括 Azure Vm 和內部部署機器。
- 您可以使用 [azure 角色型存取控制 (AZURE RBAC) ](../role-based-access-control/role-assignments-portal.md)來管理保存庫存取。
- 您可以指定如何複寫保存庫中的資料以提供備援性：
  - **本機冗余儲存體 (LRS) **：為了防止資料中心發生失敗，您可以使用 LRS。 LRS 會將資料複寫至儲存體縮放單位。 [深入了解](../storage/common/storage-redundancy.md#locally-redundant-storage)。
  - **異地冗余儲存體 (GRS) **：若要防止整個區域的中斷，您可以使用 GRS。 GRS 會將您的資料複寫到次要區域。 [深入了解](../storage/common/storage-redundancy.md#geo-redundant-storage)。
  - **區域冗余儲存體 (ZRS) **：複寫 [可用性區域](https://docs.microsoft.com/azure/availability-zones/az-overview#availability-zones)中的資料，保證相同區域中的資料存放區和復原。 [深入了解](../storage/common/storage-redundancy.md#zone-redundant-storage)
  - 根據預設，復原服務保存庫會使用 GRS。

復原服務保存庫具有下列額外功能：

- 在一個 Azure 訂用帳戶中，您最多可以建立 500 個保存庫。

## <a name="backup-agents"></a>備份代理程式

Azure 備份會根據要備份的機器類型，提供不同的備份代理程式：

**代理程式** | **詳細資料**
--- | ---
**MARS 代理程式** | <ul><li>會在個別的內部部署 Windows Server 電腦上執行，以備份檔案、資料夾和系統狀態。</li> <li>會在 Azure Vm 上執行，以備份檔案、資料夾和系統狀態。</li> <li>在 DPM/MABS 伺服器上執行，以將 DPM/MABS 本機儲存體磁片備份到 Azure。</li></ul>
**Azure VM 擴充功能** | 會在 Azure VM 上執行以備份至保存庫。

## <a name="backup-types"></a>備份類型

下表說明不同類型的備份，以及使用它們的時機：

**備份類型** | **詳細資料** | **使用量**
--- | --- | ---
**完整** | 完整備份包含整個資料來源。 比差異或增量備份佔用更多的網路頻寬。 | 用於初始備份。
**微分** |  差異備份會儲存自初始完整備份之後所變更的區塊。 使用較少的網路和儲存空間，而不會保留未變更資料的重複複本。<br/><br/> 效率不佳，因為在後續備份之間沒有變更的資料區塊會被傳送和儲存。 | Azure 備份並未使用。
**增量** | 增量備份只會儲存自上一次備份之後變更的資料區塊。 儲存體和網路效率較高。 <br/><br/> 使用增量備份時，不需要補充完整備份。 | 供 DPM/MABS 用於磁碟備份，並且用於所有備份至 Azure 的作業。 不適用於 SQL Server 備份。

## <a name="sql-server-backup-types"></a>SQL Server 備份類型

下表說明用於 SQL Server 資料庫和其使用頻率的不同備份類型：

**備份類型** | **詳細資料** | **使用量**
--- | --- | ---
**完整備份** | 完整資料庫備份會備份整個資料庫。 它包含特定資料庫或一組檔案群組或檔案中的所有資料。 完整備份也包含足夠的記錄來復原該資料。 | 您每天最多可以觸發一次完整備份。<br/><br/> 您可以選擇每日或每週間隔進行完整備份。
**差異備份** | 差異備份是以最新的先前完整資料備份為基礎。<br/><br/> 只會擷取自完整備份後已變更的資料。 |  您每天最多可以觸發一次差異備份。<br/><br/> 您無法在同一天設定完整備份和差異備份。
**交易記錄備份** | 記錄備份最高允許特定秒的時間點還原。 | 您最多可以每隔 15 分鐘設定交易記錄備份一次。

### <a name="comparison-of-backup-types"></a>備份類型的比較

每一種備份的儲存體耗用量、復原時間目標 (RTO) 以及網路耗用量都不相同。 下圖顯示備份類型的比較：

- 資料來源 A 由每月備份的10個儲存區塊（A1-A10）所組成。
- 區塊 A2、A3、A4 和 A9 在第一個月有所變更，而 A5 則在下個月有所變更。
- 若使用差異備份，將會在第二個月備份有所變更的區塊 A2、A3、A4 和 A9。 在第三個月會再次備份這一些相同區塊，以及有所變更的區塊 A5。 在執行下次完整備份之前，都會繼續備份有所變更的區塊。
- 針對增量備份，在第二個月，區塊 A2、A3、A4 和 A9 會標示為已變更和已傳送。 在第三個月，只標示區塊 A5 有所變更，而加以傳輸。

![顯示備份方法比較的影像](./media/backup-architecture/backup-method-comparison.png)

## <a name="backup-features"></a>備份功能

下表摘要說明不同備份類型的支援功能：

**功能** | **使用 MARS 代理程式直接備份檔案和資料夾 () ** | **Azure VM 備份** | **使用 DPM/MABS 的電腦或應用程式**
--- | --- | --- | ---
備份至保存庫 | ![是][green] | ![是][green] | ![是][green]
備份至 DPM/MABS 磁片，然後再備份至 Azure | | | ![Yes][green]
壓縮要備份的傳輸資料 | ![Yes][green] | 傳輸資料時不使用壓縮。 儲存體會略為膨脹，但還原速度較快。  | ![Yes][green]
執行增量備份 |![是][green] |![是][green] |![是][green]
備份已刪除重複資料的磁碟 | | | ![部分][yellow]<br/><br/> 僅用於內部部署的 DPM/MABS 伺服器。

![資料表索引鍵](./media/backup-architecture/table-key.png)

## <a name="backup-policy-essentials"></a>備份原則基本資訊

- 每個保存庫都會建立備份原則。
- 您可以針對下列工作負載的備份建立備份原則： azure vm、Azure Vm 中的 SQL、Azure Vm 中的 SAP Hana、azure 檔案共用。 使用 MARS 代理程式的檔案和資料夾備份原則是在 MARS 主控台中指定。
  - Azure 檔案共用
- 您可以將一個原則指派給多項資源。 Azure VM 備份原則可用來保護許多 Azure VM。
- 原則是由兩個元件所組成
  - 排程：製作備份的時間
  - 保留期：每個備份應保留的時間長度。
- 排程可以定義為「每日」或「每週」的特定時間點。
- 您可以定義「每日」、「每週」、「每月」、「每年」備份點的保留期。
  - 「每週」指的是在一周的某一天備份
  - 「每月」指的是當月某一天的備份
  - 「每年」指的是一年中某一天的備份
- 「每月」、「每年」備份點的保留期稱為長期保留 (LTR) 
- 建立保存庫時，也會建立「DefaultPolicy」，並可用於備份資源。
- 對備份原則的保留期間所做的任何變更，將會追溯到新的復原點以外的所有舊的復原點。

### <a name="additional-reference"></a>其他參考資料

- Azure VM 機器：如何 [建立](./backup-azure-vms-first-look-arm.md#back-up-from-azure-vm-settings) 和 [修改](./backup-azure-manage-vms.md#manage-backup-policy-for-a-vm) 原則。
- Azure VM 電腦中的 SQL Server 資料庫：如何 [建立](./backup-sql-server-database-azure-vms.md#create-a-backup-policy) 和 [修改](./manage-monitor-sql-database-backup.md#modify-policy) 原則。
- Azure 檔案共用：如何 [建立](./backup-afs.md) 和 [修改](./manage-afs-backup.md#modify-policy) 原則。
- SAP Hana：如何 [建立](./backup-azure-sap-hana-database.md#create-a-backup-policy) 和 [修改](./sap-hana-db-manage.md#change-policy) 原則。
- MARS：如何 [建立](./backup-windows-with-mars-agent.md#create-a-backup-policy) 和 [修改](./backup-azure-manage-mars.md#modify-a-backup-policy) 原則。
- [根據工作負載的類型排定備份是否有任何限制？](./backup-azure-backup-faq.md#are-there-limits-on-backup-scheduling)
- [如果我變更保留原則，現有的復原點會發生什麼事？](./backup-azure-backup-faq.md#what-happens-when-i-change-my-backup-policy)

## <a name="architecture-built-in-azure-vm-backup"></a>架構：內建 Azure VM 備份

1. 當您啟用 Azure VM 的備份時，會根據您指定的排程來執行備份。
1. 在第一次備份期間，如果 VM 正在執行，則會在 VM 上安裝備份擴充功能。
    - 針對 Windows VM，系統會安裝 VMSnapshot 延伸模組。
    - 若是 Linux Vm，則會安裝 VMSnapshot Linux 擴充功能。
1. 延伸模組會採用儲存層級的快照集。
    - 針對正在執行的 Windows Vm，備份會與 Windows 磁碟區陰影複製服務 (VSS) ，以取得 VM 的應用程式一致快照集。 依預設，備份會進行完整的 VSS 備份。 如果「備份」無法建立應用程式一致快照集，則會建立檔案一致快照集。
    - 針對 Linux Vm，備份會取得檔案一致的快照集。 針對應用程式一致快照集，您需要手動自訂前置/後置腳本。
    - 藉由平行備份每個 VM 磁碟，可將備份作業最佳化。 對於每個要備份的磁碟，Azure 備份會讀取磁碟上的區塊，並僅儲存已變更的資料。
1. 建立快照集之後，資料會傳輸至保存庫。
    - 只會複製自上次備份之後變更的資料區塊。
    - 資料不會加密。 Azure 備份可以使用 Azure 磁碟加密來備份已加密的 Azure Vm。
    - 快照集資料可能不會立即複製到保存庫。 在尖峰時間，備份可能需要一些時間。 針對每日備份原則，VM 的總備份時間會小於 24 小時。
1. 將資料傳送至保存庫之後，就會建立復原點。 根據預設，快照集會在刪除前兩天保留。 這項功能可讓您從這些快照集進行還原作業，進而減少還原時間。 它可縮短轉換和複製資料到保存庫所需的時間。 請參閱 [Azure 備份立即還原功能](./backup-instant-restore-capability.md)。

您不需要明確地允許網際網路連線來備份您的 Azure Vm。

![備份 Azure VM](./media/backup-architecture/architecture-azure-vm.png)

## <a name="architecture-direct-backup-of-on-premises-windows-server-machines-or-azure-vm-files-or-folders"></a>架構：直接備份內部部署 Windows Server 機器或 Azure VM 檔案或資料夾

1. 若要設定案例，您可以在電腦上下載並安裝 MARS 代理程式。 然後，您可以選取要備份的內容、執行備份的時間，以及要在 Azure 中保留的時間長度。
1. 初始備份會根據您的備份設定來執行。
1. MARS 代理程式會使用 VSS 來取得選取要備份之磁片區的時間點快照集。
    - MARS 代理程式只會使用 Windows 系統寫入操作來捕捉快照集。
    - 因為代理程式不會使用任何應用程式 VSS 寫入器，所以它不會捕獲應用程式一致的快照集。
1. 使用 VSS 建立快照集之後，MARS 代理程式會在您設定備份時所指定的快取資料夾中，建立 (VHD) 的虛擬硬碟。 代理程式也會儲存每個資料區塊的總和檢查碼。
1. 增量備份會根據您指定的排程執行，除非您執行隨選備份。
1. 執行增量備份時，會識別已變更的檔案，並建立新的 VHD。 VHD 會經過壓縮和加密，然後傳送至保存庫。
1. 增量備份完成後，新的 VHD 會與初始複寫之後建立的 VHD 合併。 這個合併的 VHD 會提供最新的狀態，以用於比較進行中的備份。

![使用 MARS 代理程式備份內部部署 Windows Server 電腦](./media/backup-architecture/architecture-on-premises-mars.png)

## <a name="architecture-back-up-to-dpmmabs"></a>架構：備份至 DPM/MABS

1. 您要在想要保護的電腦上安裝 DPM 或 MABS 保護代理程式。 然後，您可以將電腦新增至 DPM 保護群組。
    - 若要保護內部部署機器，DPM 或 MABS 伺服器必須位於內部部署環境。
    - 若要保護 Azure VM，MABS 伺服器必須位於 Azure 中，以 Azure VM 的形式執行。
    - 使用 DPM/MABS，您可以保護備份磁片區、共用、檔案和資料夾。 您也可以保護電腦的系統狀態 (裸機) ，也可以使用應用程式感知的備份設定來保護特定應用程式。
1. 當您在 DPM/MABS 中設定機器或應用程式的保護時，您可以選擇備份至 MABS/DPM 本機磁片，以進行短期儲存和 Azure 以進行線上保護。 您也可以指定備份到本機 DPM/MABS 儲存體應該執行的時間，以及何時應執行線上備份至 Azure。
1. 受保護的工作負載磁片會根據您指定的排程備份至本機 MABS/DPM 磁片。
1. Dpm/MABS 磁片會由執行于 DPM/MABS 伺服器上的 MARS 代理程式備份至保存庫。

![備份受 DPM 或 MABS 保護的機器和工作負載](./media/backup-architecture/architecture-dpm-mabs.png)

## <a name="azure-vm-storage"></a>Azure VM 儲存體

Azure VM 會使用磁碟來儲存其作業系統、應用程式和資料。 每個 Azure VM 至少有兩個磁片：作業系統和暫存磁片的磁片。 Azure Vm 也可以有適用于應用程式資料的資料磁片。 磁碟會儲存為 VHD。

- Vhd 會以分頁 blob 的形式儲存在 Azure 中的標準或 premium 儲存體帳戶中：
  - **標準儲存體：** 可靠、低成本的磁片支援，適用于執行不區分延遲之工作負載的 Vm。 標準儲存體可以使用標準固態硬碟 (SSD) 磁片或標準硬碟磁片磁碟機 (HDD) 磁片。
  - **Premium 儲存體：** 高效能磁片支援。 使用進階 SSD 磁碟。
- 磁碟有不同的效能層級：
  - **標準 HDD 磁片：** 由 Hdd 所支援，並用於符合成本效益的儲存體。
  - **標準 SSD 磁片：** 結合 premium SSD 磁片和標準 HDD 磁片的元素。 提供比 HDD 更一致的效能和可靠性，但仍然符合成本效益。
  - **進階 SSD 磁片：** 受 Ssd 支援，並提供高效能且低延遲的 Vm，以執行需要大量 i/o 的工作負載。
- 磁碟可以是受控或非受控的：
  - **非受控磁片：** 虛擬機器所使用的傳統磁片類型。 對於這些磁碟，您可以建立自己的儲存體帳戶，並在建立磁碟時指定該帳戶。 然後，您必須瞭解如何將 Vm 的儲存體資源極大化。
  - **受控磁片：** Azure 會為您建立及管理儲存體帳戶。 您可以指定磁片大小和效能層級，而 Azure 會為您建立受控磁片。 當您新增磁片並調整 Vm 時，Azure 會處理儲存體帳戶。

如需磁片儲存體和 Vm 可用磁片類型的詳細資訊，請參閱下列文章：

- [適用于 Linux Vm 的 Azure 受控磁片](../virtual-machines/managed-disks-overview.md)
- [Vm 可用的磁片類型](../virtual-machines/disks-types.md)

### <a name="back-up-and-restore-azure-vms-with-premium-storage"></a>使用 premium 儲存體來備份和還原 Azure Vm

您可以搭配 Azure 備份使用 premium 儲存體來備份 Azure Vm：

- 在使用 premium 儲存體備份 Vm 的過程中，備份服務會在儲存體帳戶中建立名為 *AzureBackup*的暫存位置。 預備位置的大小等於復原點快照集的大小。
- 確定進階儲存體帳戶有足夠的可用空間可容納暫存位置。 如需詳細資訊，請參閱 [premium 頁面 blob 儲存體帳戶的擴充性目標](../storage/blobs/scalability-targets-premium-page-blobs.md)。 請勿修改暫存位置。
- 備份作業完成後，就會刪除暫存位置。
- 用於暫存位置的儲存體，價格會與[進階儲存體價格](../virtual-machines/disks-types.md#billing)一致。

當您使用 premium 儲存體還原 Azure Vm 時，可以將它們還原至 premium 或標準儲存體。 一般來說，您會將它們還原至 premium 儲存體。 但是，如果您只需要 VM 中的部分檔案，將其還原至標準儲存體可能會符合成本效益。

### <a name="back-up-and-restore-managed-disks"></a>備份及還原受控磁片

您可以使用受控磁片來備份 Azure Vm：

- 備份使用受控磁碟的 VM 時，所使用相同的方式與備份任何其他 Azure VM 時相同。 您可以直接從虛擬機器設定備份 VM，或是為復原服務保存庫中的 VM 啟用備份。
- 您可以透過以受控磁碟為基礎的 RestorePoint 集合來備份受控磁碟上的 VM。
- Azure 備份也支援使用以 Azure 磁碟加密加密的受控磁片備份 Vm。

當您還原具有受控磁片的 Vm 時，可以還原至具有受控磁片的完整 VM，或還原至儲存體帳戶：

- 在還原過程中，Azure 會處理受控磁片。 如果您使用 [儲存體帳戶] 選項，則會管理在還原程式期間所建立的儲存體帳戶。
- 如果您還原已加密的受控 VM，請先確定 VM 的金鑰和秘密存在於金鑰保存庫中，然後再開始還原程式。

## <a name="next-steps"></a>下一步

- 請參閱支援矩陣，以 [瞭解備份案例的支援功能和限制](backup-support-matrix.md)。
- 設定下列其中一個案例的備份：
  - [備份 Azure VM](backup-azure-arm-vms-prepare.md)。
  - [直接備份 Windows 機器](tutorial-backup-windows-server-to-azure.md)，而不使用備份伺服器。
  - [設定 MABS](backup-azure-microsoft-azure-backup.md) 以備份至 Azure，然後將工作負載備份至 MABS。
  - [設定 DPM](backup-azure-dpm-introduction.md) 以備份至 Azure，然後將工作負載備份至 DPM。

[green]: ./media/backup-architecture/green.png
[yellow]: ./media/backup-architecture/yellow.png
[red]: ./media/backup-architecture/red.png
