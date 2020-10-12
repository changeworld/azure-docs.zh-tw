---
title: Azure 備份支援矩陣
description: 摘要說明 Azure 備份服務的支援設定和限制。
ms.topic: conceptual
ms.date: 02/17/2019
ms.custom: references_regions
ms.openlocfilehash: d9da2ee893244afc7150ab6249dbe51845d5d0c2
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "91332707"
---
# <a name="support-matrix-for-azure-backup"></a>Azure 備份的支援矩陣

您可以使用 [Azure 備份](backup-overview.md)，將資料備份至 Microsoft Azure 雲端平台。 此文章將摘要說明 Azure 備份案例和部署的一般支援設定和限制。

另有其他的支援矩陣可供使用：

- [Azure 虛擬機器 (VM) 備份](backup-support-matrix-iaas.md)的支援矩陣
- 使用 [System Center Data Protection Manager (DPM)/Microsoft Azure 備份伺服器 (MABS)](backup-support-matrix-mabs-dpm.md) 進行備份的支援矩陣
- 使用 [Microsoft Azure 復原服務 (MARS) 代理程式](backup-support-matrix-mars-agent.md)進行備份的支援矩陣

[!INCLUDE [azure-lighthouse-supported-service](../../includes/azure-lighthouse-supported-service.md)]

## <a name="vault-support"></a>保存庫支援

Azure 備份會使用復原服務保存庫來協調和管理備份。 其也會使用保存庫儲存已備份的資料。

下表描述復原服務保存庫的功能：

**功能** | **詳細資料**
--- | ---
**訂用帳戶中的保存庫** | 一個訂用帳戶中最多可以有 500 個復原服務保存庫。
**保存庫中的機器** | 所有工作負載的最多2000資料來源 (例如 Azure Vm、SQL Server VM、MABS 伺服器等) 可以在單一保存庫中受到保護。<br><br>一個保存庫中最多可以有 1,000 個 Azure VM。<br/><br/> 可在單一保存庫中註冊最多 50 個 MABS 伺服器。
**資料來源** | 個別[資料來源](./backup-azure-backup-faq.md#how-is-the-data-source-size-determined)的大小上限是 54,400 GB。 此限制不適用於 Azure VM 備份。 您可以備份至保存庫的資料總量不會有任何限制。
**備份至保存庫** | **Azure VM：** 一天一次。<br/><br/>**受 DPM/MABS 保護的電腦：** 一天兩次。<br/><br/> **使用 MARS 代理程式直接備份的電腦：** 一天三次。
**保存庫之間的備份** | 在區域內備份。<br/><br/> 您在每個 Azure 區域中都必須有一個保存庫包含您想要備份的 VM。 您無法備份至不同的區域。
**移動保存庫** | 您可以在訂用帳戶之間[移動保存庫](./backup-azure-move-recovery-services-vault.md)，或在相同訂用帳戶的資源群組之間移動。 不過，不支援跨區域移動保存庫。
**在保存庫之間移動資料** | 不支援在保存庫之間移動已備份的資料。
**修改保存庫儲存體類型** | 您可以在儲存備份之前，修改保存庫的儲存體複寫類型 (異地備援儲存體或本地備援儲存體)。 開始在保存庫中進行備份後，即無法修改複寫類型。
**區域備援儲存體 (ZRS)** | 可在英國南部 (UKS) 和南東亞 (海洋) 區域中使用。

## <a name="on-premises-backup-support"></a>內部部署備份支援

以下是您要備份內部部署電腦時所支援的項目：

**機器** | **備份的項目** | **位置** | **功能**
--- | --- | --- | ---
**透過 MARS 代理程式直接備份 Windows 機器** | 檔案、資料夾、系統狀態 | 備份至復原服務保存庫。 | 一天備份三次<br/><br/> 沒有應用程式感知備份<br/><br/> 還原檔案、資料夾、磁碟區
**透過 MARS 代理程式直接備份 Linux 機器** | 不支援備份
**備份到 DPM** | 檔案、資料夾、磁碟區、系統狀態、應用程式資料 | 備份至本機 DPM 儲存體。 然後，DPM 再備份至保存庫。 | 應用程式感知快照集<br/><br/> 備份和復原的完整細微性<br/><br/> VM (Hyper-V/VMware) 支援 Linux<br/><br/> 不支援 Oracle
**備份至 MABS** | 檔案、資料夾、磁碟區、系統狀態、應用程式資料 | 備份至 MABS 本機儲存體。 然後，MABS 再備份至保存庫。 | 應用程式感知快照集<br/><br/> 備份和復原的完整細微性<br/><br/> VM (Hyper-V/VMware) 支援 Linux<br/><br/> 不支援 Oracle

## <a name="azure-vm-backup-support"></a>Azure VM 備份支援

### <a name="azure-vm-limits"></a>Azure VM 限制

**限制** | **詳細資料**
--- | ---
**Azure VM 資料磁碟** | 請參閱 [Azure VM 備份的支援矩陣](./backup-support-matrix-iaas.md#vm-storage-support)。
**Azure VM 資料磁碟大小** | 個別磁碟大小最高可達 32 TB，單一 VM 中所有磁碟大小最高可達 256 TB。

### <a name="azure-vm-backup-options"></a>Azure VM 備份選項

以下是您想要備份 Azure VM 時所支援的項目：

**機器** | **備份的項目** | **位置** | **功能**
--- | --- | --- | ---
**使用 VM 延伸模組的 Azure VM 備份** | 整個 VM | 備份至保存庫。 | 當您為 VM 啟用備份時安裝的延伸模組。<br/><br/> 一天備份一次。<br/><br/> Windows VM 的應用程式感知備份；Linux VM 的檔案一致備份。 您可以使用自訂指令碼，為 Linux 機器腦設定應用程式一致性。<br/><br/> 還原 VM 或磁碟。<br/><br/> 無法將 Azure VM 備份至內部部署位置。
**使用 MARS 代理程式的 Azure VM 備份** | 檔案、資料夾、系統狀態 | 備份至保存庫。 | 一天備份三次。<br/><br/> 如果您要備份特定的檔案或資料夾，而非整個 VM，則 MARS 代理程式可以與 VM 延伸模組一起執行。
**使用 DPM 的 Azure VM** | 檔案、資料夾、磁碟區、系統狀態、應用程式資料 | 備份至執行 DPM 的 Azure VM 本機儲存體。 然後，DPM 再備份至保存庫。 | 應用程式感知快照集。<br/><br/> 備份和復原的完整細微性。<br/><br/> VM (Hyper-V/VMware) 支援 Linux。<br/><br/> 不支援 Oracle。
**使用 MABS 的 Azure VM** | 檔案、資料夾、磁碟區、系統狀態、應用程式資料 | 備份至執行 MABS 的 Azure VM 本機儲存體。 然後，MABS 再備份至保存庫。 | 應用程式感知快照集。<br/><br/> 備份和復原的完整細微性。<br/><br/> VM (Hyper-V/VMware) 支援 Linux。<br/><br/> 不支援 Oracle。

## <a name="linux-backup-support"></a>Linux 備份支援

以下是您想要備份 Linux 電腦時所支援的項目：

**備份類型** | **Linux (Azure 背書)**
--- | ---
**直接備份執行 Linux 的內部部署電腦** | 不支援。 MARS 代理程式只能安裝在 Windows 電腦上。
**使用代理程式延伸模組備份執行 Linux 的 Azure VM** | 使用[自訂指令碼](backup-azure-linux-app-consistent.md)進行應用程式一致備份。<br/><br/> 檔案層級復原。<br/><br/> 從復原點或磁碟建立 VM 以進行還原。
**使用 DPM 備份執行 Linux 的內部部署電腦** | Hyper-v 和 VMware 上 Linux 來賓 Vm 的檔案一致備份。<br/><br/> Hyper-v 和 VMware Linux 來賓 Vm 的 VM 還原。
**使用 MABS 備份執行 Linux 的內部部署電腦** | Hyper-v 和 VMware 上 Linux 來賓 Vm 的檔案一致備份。<br/><br/> Hyper-v 和 VMware Linux 來賓 Vm 的 VM 還原。
**使用 MABS 或 DPM 備份 Linux Azure VM** | 不支援。

## <a name="daylight-saving-time-support"></a>日光節約時間支援

Azure 備份在進行 Azure VM 備份時不支援依據日光節約時間而自動調整時鐘。 它不會將備份的小時向前或向後移動。 為確保備份在所需的時間執行，請視需要，手動修改備份原則。

## <a name="disk-deduplication-support"></a>磁碟重複資料刪除支援

磁碟重複資料刪除支援如下︰

- 當您使用 DPM 或 MABS 備份執行 Windows 的 Hyper-V VM 時，內部部署支援磁碟重複資料刪除。 Windows Server 會在當作備份儲存體連結至 VM 的虛擬硬碟 (VHD) 上，執行重複資料刪除 (在主機層級)。
- 在 Azure 中不支援對任何備份元件進行重複資料刪除。 在 Azure 中部署 DPM 和 MABS 時，連結至 VM 的儲存體磁碟無法進行重複資料刪除。

## <a name="security-and-encryption-support"></a>安全性與加密支援

Azure 備份支援傳輸中和待用資料的加密。

### <a name="network-traffic-to-azure"></a>Azure 的網路流量

- 從伺服器到復原服務保存庫的備份流量會使用進階加密標準 256 進行加密。
- 備份資料會透過安全的 HTTPS 連結來傳送。

### <a name="data-security"></a>資料安全性

- 備份資料會在復原服務保存庫中以加密格式儲存。
- 使用 MARS 代理程式從內部部署伺服器備份資料時，資料會在上傳至 Azure 備份時以複雜密碼加密，而且只有在從 Azure 備份下載時才會解密。
- 備份 Azure VM 時，您必須在虛擬機器*內*設定加密。
- Azure 備份支援 Azure 磁碟加密，其會在 Windows 虛擬機器上使用 BitLocker，而在 Linux 虛擬機器上使用 **dm-crypt**。
- 在後端，Azure 備份會使用 [Azure 儲存體服務加密](../storage/common/storage-service-encryption.md)，保護待用資料。

**機器** | **傳輸中** | **待用**
--- | --- | ---
**不含 DPM/MABS 的內部部署 Windows 電腦** | ![是][green] | ![是][green]
**Azure VM** | ![是][green] | ![是][green]
**含 DPM 的內部部署 Windows 電腦或 Azure VM** | ![是][green] | ![是][green]
**含 MABS 的內部部署 Windows 電腦或 Azure VM** | ![是][green] | ![是][green]

## <a name="compression-support"></a>壓縮支援

備份支援壓縮備份流量，如下表摘要說明。

- 對於 Azure VM，VM 延伸模組會透過儲存體網路直接從 Azure 儲存體帳戶讀取資料，因此不需要壓縮此流量。
- 如果您要使用 DPM 或 MABS，則可先壓縮資料再備份，以節省頻寬。

**機器** | **壓縮到 MABS DPM (TCP)** | **壓縮到保存庫 (HTTPS)**
--- | --- | ---
**直接備份內部部署 Windows 機器** | NA | ![是][green]
**使用 VM 延伸模組備份 Azure VM** | NA | NA
**使用 MABS/DPM 在內部部署/Azure 電腦上備份** | ![是][green] | ![是][green]

## <a name="retention-limits"></a>保留期限

**設定** | **限制**
--- | ---
**每個受保護實例的最大復原點 (機或工作負載) ** | 9,999
**復原點的到期時間上限** | 沒有限制
**備份至 DPM/MABS 的頻率上限** | 每隔 15 分鐘 (SQL Server)<br/><br/> 每小時一次 (其他工作負載)
**備份至保存庫的頻率上限** | **執行 MABS 的內部部署 Windows 電腦或 Azure VM：** 每天三次<br/><br/> **DPM/MABS：** 每天兩次<br/><br/> **Azure VM 備份：** 每天一次
**復原點保留期** | 每日、每週、每月、每年
**最大保留期間** | 依照備份頻率而定
**DPM/MABS 磁碟上的復原點** | 64 (檔案伺服器)；448 (應用程式伺服器) <br/><br/>內部部署 DPM 的磁帶復原點沒有限制

## <a name="cross-region-restore"></a>跨區域還原

Azure 備份已新增跨區域還原功能來加強資料可用性和復原功能，讓您可以完全控制將資料還原至次要區域。 若要設定此功能，請參閱[設定跨區域還原](backup-create-rs-vault.md#set-cross-region-restore)一文。 下列管理類型支援此功能：

| 備份管理類型 | 支援                                                    | 支援的區域 |
| ---------------------- | ------------------------------------------------------------ | ----------------- |
| Azure VM               | 是。   支援加密 VM 和磁碟小於 4 TB 的 VM | 所有 Azure 公用區域和主權雲端。  |
| SQL/SAP HANA | 是                                                          | 美國西部 2 (WUS2) 、美國中西部 (WCUS) 、加拿大中部 (CNC) 、加拿大東部 (CNE) 、美國東部 (EUS) 、美國西部 (WUS) 、日本東部 (JPE) 、日本東部 (JPE)  |
| MARS 代理程式/內部部署  | 否                                                           | N/A               |
| Azure 檔案共用) 的 AFS (                 | 否                                                           | N/A               |

## <a name="next-steps"></a>後續步驟

- [檢閱](backup-support-matrix-iaas.md) Azure VM 備份的支援矩陣。

[green]: ./media/backup-support-matrix/green.png
[yellow]: ./media/backup-support-matrix/yellow.png
[red]: ./media/backup-support-matrix/red.png
