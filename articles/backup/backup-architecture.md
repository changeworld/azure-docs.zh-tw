---
title: 架構概觀
description: 概略說明 Azure 備份服務所使用的架構、元件和程序。
ms.topic: conceptual
ms.date: 02/19/2019
ms.openlocfilehash: b093c6702bb26fe537622727fe1b623141bf4160
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79273614"
---
# <a name="azure-backup-architecture-and-components"></a>Azure 備份體系結構和元件

可以使用 Azure[備份服務](backup-overview.md)將資料備份到 Microsoft Azure 雲平臺。 本文將摘要說明 Azure 備份的架構、元件和程序。

## <a name="what-does-azure-backup-do"></a>Azure 備份有何功能？

Azure 備份備份在本地電腦和 Azure 虛擬機器 （VM） 實例上運行的資料、電腦狀態和工作負載。 Azure 備份有多種案例。

## <a name="how-does-azure-backup-work"></a>Azure 備份如何運作？

您可以使用多種方法備份電腦和資料：

- **備份內部部署機器**：
  - 可以使用 Azure 備份 Microsoft Azure 恢復服務 （MARS） 代理將本地 Windows 電腦直接備份到 Azure。 不支援 Linux 機器。
  - 您可以將本地電腦備份到備份伺服器 - 系統中心資料保護管理器 （DPM） 或 Microsoft Azure 備份伺服器 （MABS）。 然後，可以將備份伺服器備份到 Azure 中的恢復服務保存庫。

- **備份 Azure VM**：
  - 您可以直接備份 Azure VM。 Azure 備份將備份擴展到在 VM 上運行的 Azure VM 代理。 此擴充功能會備份整個 VM。
  - 您可以執行 MARS 代理程式來備份 Azure VM 上的特定檔案和資料夾。
  - 您可以將 Azure VM 備份到 Azure 中運行的 MABS，然後將 MABS 備份到恢復服務保存庫。

詳細瞭解[您可以備份的內容](backup-overview.md)以及[有關支援的備份方案](backup-support-matrix.md)。

## <a name="where-is-data-backed-up"></a>資料會備份至何處？

Azure 備份將備份資料存儲在恢復服務保存庫中。 保存庫是 Azure 中用於保存資料（如備份副本、復原點和備份策略）的線上存儲實體。

恢復服務保存庫具有以下功能：

- 保存庫可輕鬆地組織您的備份資料，同時可減輕管理負擔。
- 在一個 Azure 訂用帳戶中，您最多可以建立 500 個保存庫。
- 可以監視保存庫中的備份項，包括 Azure VM 和本地電腦。
- 您可以使用 Azure [角色型存取控制 (RBAC)](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal) 來管理保存庫存取。
- 您可以指定如何複寫保存庫中的資料以提供備援性：
  - **本地冗余存儲 （LRS）：** 為了防止資料中心出現故障，可以使用 LRS。 LRS 會將資料複寫至儲存體縮放單位。 [深入了解](https://docs.microsoft.com/azure/storage/common/storage-redundancy-lrs)。
  - **異地冗余存儲 （GRS）：** 為了防止區域範圍的中斷，您可以使用 GRS。 GRS 將資料複製到次要區域。 [深入了解](https://docs.microsoft.com/azure/storage/common/storage-redundancy-grs)。
  - 預設情況下，恢復服務保存庫使用 GRS。

## <a name="backup-agents"></a>備份代理程式

Azure 備份提供不同的備份代理，具體取決於要備份的電腦類型：

**代理** | **詳細資料**
--- | ---
**MARS 代理** | <ul><li>在單個本地 Windows 伺服器電腦上運行以備份檔案、資料夾和系統狀態。</li> <li>在 Azure VM 上運行以備份檔案、資料夾和系統狀態。</li> <li>在 DPM/MABS 伺服器上運行，以將 DPM/MABS 本機存放區磁片備份到 Azure。</li></ul>
**Azure VM 擴充功能** | 會在 Azure VM 上執行以備份至保存庫。

## <a name="backup-types"></a>備份類型

下表解釋了不同類型的備份以及何時使用備份：

**備份類型** | **詳細資料** | **使用**
--- | --- | ---
**全** | 完整備份包含整個資料來源。 與差分備份或增量備份相比，佔用更多的網路頻寬。 | 用於初始備份。
**差異** |  差異備份存儲自初始完整備份以來更改的塊。 使用較少的網路和存儲量，並且不會保留未更改資料的冗余副本。<br/><br/> 效率低下，因為在以後備份之間未更改的資料塊被傳輸和存儲。 | Azure 備份並未使用。
**增量** | 增量備份僅存儲自上次備份以來更改的資料塊。 儲存體和網路效率較高。 <br/><br/> 使用增量備份，無需使用完整備份進行補充。 | 供 DPM/MABS 用於磁碟備份，並且用於所有備份至 Azure 的作業。 不用於 SQL 伺服器備份。

## <a name="sql-server-backup-types"></a>SQL Server 備份類型

下表解釋了用於 SQL Server 資料庫的不同類型的備份及其使用頻率：

**備份類型** | **詳細資料** | **使用**
--- | --- | ---
**完全備份** | 完整資料庫備份會備份整個資料庫。 它包含特定資料庫或一組檔組或檔中的所有資料。 完整備份還包含足夠的日誌來恢復該資料。 | 您每天最多可以觸發一次完整備份。<br/><br/> 您可以選擇在每日或每週的時間間隔進行完整備份。
**差分備份** | 差分備份基於最新的、以前的完整資料備份。<br/><br/> 只會擷取自完整備份後已變更的資料。 |  您每天最多可以觸發一次差異備份。<br/><br/> 您無法在同一天設定完整備份和差異備份。
**事務記錄備份** | 記錄備份最高允許特定秒的時間點還原。 | 您最多可以每隔 15 分鐘設定交易記錄備份一次。

### <a name="comparison-of-backup-types"></a>備份類型的比較

每一種備份的儲存體耗用量、復原時間目標 (RTO) 以及網路耗用量都不相同。 下圖顯示了備份類型的比較：

- 資料來源 A 由 10 個存儲塊 A1-A10 組成，這些存儲塊每月備份一次。
- 區塊 A2、A3、A4 和 A9 在第一個月有所變更，而 A5 則在下個月有所變更。
- 若使用差異備份，將會在第二個月備份有所變更的區塊 A2、A3、A4 和 A9。 在第三個月會再次備份這一些相同區塊，以及有所變更的區塊 A5。 在執行下次完整備份之前，都會繼續備份有所變更的區塊。
- 對於增量備份，在第二個月，塊 A2、A3、A4 和 A9 被標記為更改和傳輸。 在第三個月，只標示區塊 A5 有所變更，而加以傳輸。

![顯示備份方法比較的圖像](./media/backup-architecture/backup-method-comparison.png)

## <a name="backup-features"></a>備份功能

下表總結了不同類型的備份的支援功能：

**特徵** | **直接備份檔案和資料夾（使用 MARS 代理）** | **Azure VM 備份** | **具有 DPM/MABS 的電腦或應用**
--- | --- | --- | ---
備份至保存庫 | ![是][green] | ![是][green] | ![是][green]
備份到 DPM/MABS 磁片，然後備份到 Azure | | | ![是][green]
壓縮要備份的傳輸資料 | ![是][green] | 傳輸資料時不使用壓縮。 儲存體會略為膨脹，但還原速度較快。  | ![是][green]
執行增量備份 |![是][green] |![是][green] |![是][green]
備份已刪除重複資料的磁碟 | | | ![部分][yellow]<br/><br/> 僅用於內部部署的 DPM/MABS 伺服器。

![表鍵](./media/backup-architecture/table-key.png)

## <a name="backup-policy-essentials"></a>備份原則基本資訊

- 每個保存庫都會建立備份原則。
- 您可以針對下列工作負載的備份建立備份原則
  - Azure VM
  - Azure VM 中的 SQL
  - Azure 檔案共用
- 您可以將一個原則指派給多項資源。 Azure VM 備份原則可用來保護許多 Azure VM。
- 原則是由兩個元件所組成
  - 排程：製作備份的時間
  - 保留期：每個備份應保留的時間長度。
- 排程可以定義為「每日」或「每週」的特定時間點。
- 您可以定義「每日」、「每週」、「每月」、「每年」備份點的保留期。
- 「每週」是指於當週的特定一天備份，「每月」代表於當月的特定一天備份，而「每年」是指於當年的特定一天備份。
- 「每月」、「每年」備份點的保留期也稱為 "LongTermRetention"。
- 創建保存庫時，還會創建名為"預設策略"的 Azure VM 備份策略，並可用於備份 Azure VM。

## <a name="architecture-built-in-azure-vm-backup"></a>體系結構：內置 Azure VM 備份

1. 為 Azure VM 啟用備份時，備份會根據指定的計畫運行。
1. 在第一次備份期間，如果 VM 正在運行，則 VM 上將安裝備份擴展。
    - 對於 Windows VM，將安裝 VMSnapshot 擴展。
    - 對於 Linux VM，將安裝 VMSnapshot Linux 擴展。
1. 擴展採用存儲級快照。
    - 對於正在運行的 Windows VM，備份與 Windows 卷卷卷卷影複製服務 （VSS） 協調，以拍攝與應用一致的 VM 快照。 預設情況下，備份需要完整的 VSS 備份。 如果「備份」無法建立應用程式一致快照集，則會建立檔案一致快照集。
    - 對於 Linux VM，備份會獲取檔一致的快照。 對於應用一致的快照，您需要手動自訂預/後腳本。
    - 藉由平行備份每個 VM 磁碟，可將備份作業最佳化。 對於每個要備份的磁碟，Azure 備份會讀取磁碟上的區塊，並僅儲存已變更的資料。
1. 建立快照集之後，資料會傳輸至保存庫。
    - 僅複製自上次備份以來更改的資料塊。
    - 資料不會加密。 Azure 備份可以使用 Azure 磁片加密備份已加密的 Azure VM。
    - 快照集資料可能不會立即複製到保存庫。 在高峰時間，備份可能需要幾個小時。 對於每日備份策略，VM 的總備份時間將少於 24 小時。
1. 將資料發送到保存庫後，將創建一個復原點。 預設情況下，快照在刪除之前保留兩天。 此功能允許從這些快照進行還原操作，從而減少還原時間。 它減少了從保存庫轉換和複製資料所需的時間。 請參閱[Azure 備份即時還原功能](https://docs.microsoft.com/azure/backup/backup-instant-restore-capability)。

不需要顯式允許 Internet 連接來備份 Azure VM。

![備份 Azure VM](./media/backup-architecture/architecture-azure-vm.png)

## <a name="architecture-direct-backup-of-on-premises-windows-server-machines-or-azure-vm-files-or-folders"></a>體系結構：直接備份本地 Windows 伺服器電腦或 Azure VM 檔或資料夾

1. 要設置方案，請在電腦上下載並安裝 MARS 代理。 然後選擇要備份的內容、備份何時運行以及備份將在 Azure 中保留多長時間。
1. 初始備份會根據您的備份設置運行。
1. MARS 代理使用 VSS 獲取選擇備份的卷的時間點快照。
    - MARS 代理僅使用 Windows 系統寫入操作來捕獲快照。
    - 由於代理不使用任何應用程式 VSS 編寫器，因此不會捕獲與應用一致的快照。
1. 使用 VSS 拍攝快照後，MARS 代理會在配置備份時指定的快取檔案夾中創建虛擬硬碟 （VHD）。 代理還存儲每個資料塊的校驗和。
1. 增量備份根據指定的計畫運行，除非您運行按需備份。
1. 執行增量備份時，會識別已變更的檔案，並建立新的 VHD。 VHD 被壓縮和加密，然後發送到保存庫。
1. 增量備份完成後，新的 VHD 將與初始複製後創建的 VHD 合併。 此合併的 VHD 提供用於比較持續備份的最新狀態。

![使用 MARS 代理備份本地 Windows 伺服器電腦](./media/backup-architecture/architecture-on-premises-mars.png)

## <a name="architecture-back-up-to-dpmmabs"></a>架構：備份到 DPM/MABS

1. 在要保護的電腦上安裝 DPM 或 MABS 保護代理。 然後，將電腦添加到 DPM 保護組。
    - 若要保護內部部署機器，DPM 或 MABS 伺服器必須位於內部部署環境。
    - 若要保護 Azure VM，MABS 伺服器必須位於 Azure 中，以 Azure VM 的形式執行。
    - 使用 DPM/MABS，您可以保護備份卷、共用、檔和資料夾。 您還可以保護電腦的系統狀態（裸機），並且可以使用應用感知備份設置保護特定應用。
1. 在 DPM/MABS 中為電腦或應用設置保護時，您可以選擇備份到 MABS/DPM 本地磁片進行短期存儲，然後備份到 Azure 進行連線保護。 您還可以指定何時運行本地 DPM/MABS 存儲的備份以及何時運行到 Azure 的連線備份。
1. 受保護的工作負載磁片將根據您的指定計劃備份到本地 MABS/DPM 磁片。
1. DPM/MABS 磁片由在 DPM/MABS 伺服器上運行的 MARS 代理備份到保存庫。

![DPM 或 MABS 保護的電腦和工作負載的備份](./media/backup-architecture/architecture-dpm-mabs.png)

## <a name="azure-vm-storage"></a>Azure VM 儲存體

Azure VM 會使用磁碟來儲存其作業系統、應用程式和資料。 每個 Azure VM 至少有兩個磁片：作業系統的磁片和臨時磁片。 Azure VM 還可以具有用於應用資料的資料磁片。 磁碟會儲存為 VHD。

- VHD 作為頁面 blob 存儲在 Azure 中的標準存儲帳戶或高級存儲帳戶中：
  - **標準存儲：** 可靠、低成本的磁片支援，支援運行對延遲不敏感的工作負載的 VM。 標準存儲可以使用標準固態硬碟 （SSD） 磁片或標準硬碟 （HDD） 磁片。
  - **高級存儲：** 高性能磁片支援。 使用進階 SSD 磁碟。
- 磁碟有不同的效能層級：
  - **標準硬碟磁片：** 由 HDD 支援，用於經濟高效的存儲。
  - **標準 SSD 磁片：** 組合高級 SSD 磁片和標準硬碟磁片的元素。 提供比 HDD 更一致的性能和可靠性，但仍具有成本效益。
  - **高級 SSD 磁片：** 由 SSD 支援，為運行 I/O 密集型工作負載的 VM 提供高性能和低延遲。
- 磁碟可以是受控或非受控的：
  - **非託管磁片：** VM 使用的傳統磁片類型。 對於這些磁碟，您可以建立自己的儲存體帳戶，並在建立磁碟時指定該帳戶。 然後，您需要瞭解如何最大化 VM 的存儲資源。
  - **託管磁片：** Azure 為您創建和管理存儲帳戶。 指定磁片大小和性能層，Azure 會為您創建託管磁片。 添加磁片和縮放 VM 時，Azure 處理存儲帳戶。

有關磁片存儲和 VM 的可用磁片類型的詳細資訊，請參閱以下文章：

- [適用于 Windows VM 的 Azure 託管磁片](../virtual-machines/windows/managed-disks-overview.md)
- [用於 Linux VM 的 Azure 託管磁片](../virtual-machines/linux/managed-disks-overview.md)
- [VM 的可用磁片類型](../virtual-machines/windows/disks-types.md)

### <a name="back-up-and-restore-azure-vms-with-premium-storage"></a>使用高級存儲備份和恢復 Azure VM

通過將高級存儲與 Azure 備份一起使用，可以備份 Azure VM：

- 在使用高級存儲備份 VM 的過程中，備份服務會在存儲帳戶中創建一個臨時暫存位置，名為*AzureBackup-。* 暫存位置的大小等於復原點快照的大小。
- 確定進階儲存體帳戶有足夠的可用空間可容納暫存位置。 有關詳細資訊，請參閱[高級頁面 blob 存儲帳戶的可伸縮性目標](../storage/blobs/scalability-targets-premium-page-blobs.md)。 請勿修改暫存位置。
- 備份作業完成後，就會刪除暫存位置。
- 用於暫存位置的儲存體，價格會與[進階儲存體價格](../virtual-machines/windows/disks-types.md#billing)一致。

使用高級存儲還原 Azure VM 時，可以將它們還原到高級存儲或標準存儲。 通常，您將將它們還原到高級存儲。 但是，如果您只需要 VM 中的一個檔子集，則將它們還原到標準存儲可能很經濟。

### <a name="back-up-and-restore-managed-disks"></a>備份和恢復託管磁片

您可以使用託管磁片備份 Azure VM：

- 備份使用受控磁碟的 VM 時，所使用相同的方式與備份任何其他 Azure VM 時相同。 您可以直接從虛擬機器設定備份 VM，或是為復原服務保存庫中的 VM 啟用備份。
- 您可以透過以受控磁碟為基礎的 RestorePoint 集合來備份受控磁碟上的 VM。
- Azure 備份還支援使用 Azure 磁片加密加密的託管磁片備份 VM。

使用託管磁片還原 VM 時，可以使用託管磁片還原到完整的 VM 或存儲帳戶：

- 在還原過程中，Azure 處理託管磁片。 如果使用存儲帳戶選項，則管理在還原過程中創建的存儲帳戶。
- 如果還原已加密的託管 VM，請確保在開始還原過程之前，金鑰保存庫中存在 VM 的金鑰和機密。

## <a name="next-steps"></a>後續步驟

- 查看支援矩陣以瞭解[備份方案的支援功能和限制](backup-support-matrix.md)。
- 為以下方案之一設置備份：
  - [備份 Azure VM。](backup-azure-arm-vms-prepare.md)
  - [直接備份 Windows 機器](tutorial-backup-windows-server-to-azure.md)，而不使用備份伺服器。
  - [設定 MABS](backup-azure-microsoft-azure-backup.md) 以備份至 Azure，然後將工作負載備份至 MABS。
  - [設定 DPM](backup-azure-dpm-introduction.md) 以備份至 Azure，然後將工作負載備份至 DPM。

[green]: ./media/backup-architecture/green.png
[yellow]: ./media/backup-architecture/yellow.png
[red]: ./media/backup-architecture/red.png
