---
title: Azure 備份支援矩陣
description: 摘要說明 Azure 備份服務的支援設定和限制。
ms.topic: conceptual
ms.date: 02/17/2019
ms.openlocfilehash: cc817c1833c4c9aedcbc5fa111de694fab715c43
ms.sourcegitcommit: c8a0fbfa74ef7d1fd4d5b2f88521c5b619eb25f8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/05/2020
ms.locfileid: "82801258"
---
# <a name="support-matrix-for-azure-backup"></a>Azure 備份的支援矩陣

您可以使用[Azure 備份](backup-overview.md)將資料備份到 Microsoft Azure 的雲端平臺上。 本文摘要說明 Azure 備份案例和部署的一般支援設定和限制。

另有其他的支援矩陣可供使用：

- [Azure 虛擬機器（VM）備份](backup-support-matrix-iaas.md)的支援矩陣
- 使用[System Center Data Protection Manager （DPM）/Microsoft Azure 備份伺服器（MABS）](backup-support-matrix-mabs-dpm.md)進行備份的支援矩陣
- 使用[Microsoft Azure 復原服務（MARS）代理程式](backup-support-matrix-mars-agent.md)進行備份的支援矩陣

[!INCLUDE [azure-lighthouse-supported-service](../../includes/azure-lighthouse-supported-service.md)]

## <a name="vault-support"></a>保存庫支援

Azure 備份使用復原服務保存庫來協調和管理備份。 它也會使用保存庫來儲存備份的資料。

下表說明復原服務保存庫的功能：

**功能** | **詳細資料**
--- | ---
**訂用帳戶中的保存庫** | 一個訂用帳戶中最多可以有 500 個復原服務保存庫。
**保存庫中的機器** | 單一保存庫中最多1000個 Azure Vm。<br/><br/> 可在單一保存庫中註冊最多 50 個 MABS 伺服器。
**資料來源** | 個別[資料來源](https://docs.microsoft.com/azure/backup/backup-azure-backup-faq#how-is-the-data-source-size-determined)的大小上限為 54400 GB。 此限制不適用於 Azure VM 備份。 您可以備份至保存庫的資料總量不會有任何限制。
**備份至保存庫** | **Azure vm：** 一天一次。<br/><br/>**受 DPM/MABS 保護的機器：** 一天兩次。<br/><br/> **使用 MARS 代理程式直接備份的機器：** 一天三次。
**保存庫之間的備份** | 在區域內備份。<br/><br/> 您在每個 Azure 區域中都必須有一個保存庫包含您想要備份的 VM。 您無法備份至不同的區域。
**移動保存庫** | 您可以跨訂用帳戶或相同訂用帳戶中的資源群組之間[移動保存庫](https://docs.microsoft.com/azure/backup/backup-azure-move-recovery-services-vault)。 不過，不支援跨區域移動保存庫。
**在保存庫之間移動資料** | 不支援在保存庫之間移動備份的資料。
**修改保存庫儲存體類型** | 您可以在儲存備份之前，修改保存庫的儲存體複寫類型（異地多餘儲存體或本機多餘儲存體）。 開始在保存庫中進行備份後，即無法修改複寫類型。

## <a name="on-premises-backup-support"></a>內部部署備份支援

如果您想要備份內部部署機器，以下是支援的這是什麼：

**機器** | **備份的內容** | **位置** | **功能**
--- | --- | --- | ---
**透過 MARS 代理程式直接備份 Windows 機器** | 檔案、資料夾、系統狀態 | 備份至復原服務保存庫。 | 一天備份三次<br/><br/> 無應用程式感知備份<br/><br/> 還原檔案、資料夾、磁片區
**透過 MARS 代理程式直接備份 Linux 機器** | 不支援備份
**備份至 DPM** | 檔案、資料夾、磁片區、系統狀態、應用程式資料 | 備份至本機 DPM 儲存體。 然後，DPM 再備份至保存庫。 | 應用程式感知快照集<br/><br/> 備份和復原的完整細微性<br/><br/> Vm 支援的 Linux （Hyper-v/VMware）<br/><br/> 不支援 Oracle
**備份至 MABS** | 檔案、資料夾、磁片區、系統狀態、應用程式資料 | 備份至 MABS 本機儲存體。 然後，MABS 再備份至保存庫。 | 應用程式感知快照集<br/><br/> 備份和復原的完整細微性<br/><br/> Vm 支援的 Linux （Hyper-v/VMware）<br/><br/> 不支援 Oracle

## <a name="azure-vm-backup-support"></a>Azure VM 備份支援

### <a name="azure-vm-limits"></a>Azure VM 限制

**限制** | **詳細資料**
--- | ---
**Azure VM 資料磁碟** | 16的限制 <br> 若要註冊具有16個磁片的有限預覽 Vm （最多32個磁片），請將寫入我們的AskAzureBackupTeam@microsoft.com
**Azure VM 資料磁碟大小** | 針對 VM 中的所有磁片，個別磁片大小最高可達 32 TB，最大為 256 TB。

### <a name="azure-vm-backup-options"></a>Azure VM 備份選項

如果您想要備份 Azure Vm，以下是支援的這是什麼：

**機器** | **備份的內容** | **位置** | **功能**
--- | --- | --- | ---
**使用 VM 擴充功能的 Azure VM 備份** | 整個 VM | 備份至保存庫。 | 當您為 VM 啟用備份時安裝的延伸模組。<br/><br/> 一天備份一次。<br/><br/> 適用于 Windows Vm 的應用程式感知備份;Linux Vm 的檔案一致備份。 您可以使用自訂腳本來設定 Linux 機器的應用程式一致性。<br/><br/> 還原 VM 或磁片。<br/><br/> 無法將 Azure VM 備份至內部部署位置。
**使用 MARS 代理程式的 Azure VM 備份** | 檔案、資料夾、系統狀態 | 備份至保存庫。 | 一天備份三次。<br/><br/> 如果您想要備份特定的檔案或資料夾，而不是整個 VM，則 MARS 代理程式可以與 VM 擴充功能一起執行。
**使用 DPM 的 Azure VM** | 檔案、資料夾、磁片區、系統狀態、應用程式資料 | 備份至執行 DPM 的 Azure VM 本機儲存體。 然後，DPM 再備份至保存庫。 | 應用程式感知快照集。<br/><br/> 備份和復原的完整細微性。<br/><br/> VM (Hyper-V/VMware) 支援 Linux。<br/><br/> 不支援 Oracle。
**使用 MABS 的 Azure VM** | 檔案、資料夾、磁片區、系統狀態、應用程式資料 | 備份至執行 MABS 的 Azure VM 本機儲存體。 然後，MABS 再備份至保存庫。 | 應用程式感知快照集。<br/><br/> 備份和復原的完整細微性。<br/><br/> VM (Hyper-V/VMware) 支援 Linux。<br/><br/> 不支援 Oracle。

## <a name="linux-backup-support"></a>Linux 備份支援

如果您想要備份 Linux 機器，以下是支援的這是什麼：

**備份類型** | **Linux (Azure 背書)**
--- | ---
**直接備份執行 Linux 的內部部署機器** | 不支援。 MARS 代理程式只能安裝在 Windows 電腦上。
**使用代理程式擴充功能來備份執行 Linux 的 Azure VM** | 使用[自訂腳本](backup-azure-linux-app-consistent.md)進行應用程式一致備份。<br/><br/> 檔案層級復原。<br/><br/> 從復原點或磁碟建立 VM 以進行還原。
**使用 DPM 備份執行 Linux 的內部部署機器** | Hyper-v 和 VMWare 上 Linux 來賓 Vm 的檔案一致備份。<br/><br/> Hyper-v 和 VMWare Linux 來賓 Vm 的 VM 還原。
**使用 MABS 備份執行 Linux 的內部部署機器** | Hyper-v 和 VMWare 上 Linux 來賓 Vm 的檔案一致備份。<br/><br/> Hyper-v 和 VMWare Linux 來賓 Vm 的 VM 還原。
**使用 MABS 或 DPM 備份 Linux Azure Vm** | 不支援。

## <a name="daylight-saving-time-support"></a>日光節約時間支援

針對 Azure VM 備份，Azure 備份不支援日光節約時間的自動時鐘調整。 它不會向前或向後移動備份的小時。 若要確保備份會在所需的時間執行，請視需要手動修改備份原則。

## <a name="disk-deduplication-support"></a>磁碟重複資料刪除支援

磁碟重複資料刪除支援如下︰

- 當您使用 DPM 或 MABS 來備份執行 Windows 的 Hyper-v Vm 時，內部部署支援磁片重復資料刪除。 Windows Server 會在連接至 VM 作為備份儲存體的虛擬硬碟（Vhd）上執行重復資料刪除（在主機層級）。
- 在 Azure 中不支援對任何備份元件進行重複資料刪除。 在 Azure 中部署 DPM 和 MABS 時，連接至 VM 的儲存體磁片無法重復資料刪除。

## <a name="security-and-encryption-support"></a>安全性和加密支援

Azure 備份支援傳輸中和待用資料的加密。

### <a name="network-traffic-to-azure"></a>Azure 的網路流量

- 從伺服器到復原服務保存庫的備份流量會使用進階加密標準256進行加密。
- 備份資料會透過安全的 HTTPS 連結來傳送。
- 備份資料會以加密形式儲存在復原服務保存庫中。
- 只有您持有可將此資料解除鎖定的複雜密碼。 Microsoft 無法解密在任何時間點備份的資料。

    > [!WARNING]
    > 設定保存庫後，只有您能夠存取加密金鑰。 Microsoft 絕不會持有金鑰複本，也沒有存取金鑰的權限。 如果金鑰遺失，Microsoft 將無法復原備份資料。

### <a name="data-security"></a>資料安全性

- 當您要備份 Azure Vm 時，您*需要在虛擬機器內設定加密。*
- Azure 備份支援 Azure 磁碟加密，其會在 Windows 虛擬機器上使用 BitLocker，而在 Linux 虛擬機器上使用 **dm-crypt**。
- 在後端，Azure 備份會使用[Azure 儲存體服務加密](../storage/common/storage-service-encryption.md)，以保護待用資料。

**機器** | **傳輸中** | **待用**
--- | --- | ---
**不含 DPM/MABS 的內部部署 Windows 機器** | ![是][green] | ![是][green]
**Azure VM** | ![是][green] | ![是][green]
**使用 DPM 的內部部署 Windows 機器或 Azure Vm** | ![是][green] | ![是][green]
**具有 MABS 的內部部署 Windows 機器或 Azure Vm** | ![是][green] | ![是][green]

## <a name="compression-support"></a>壓縮支援

備份支援壓縮備份流量，如下表摘要所示。

- 針對 Azure Vm，VM 擴充功能會透過儲存體網路直接從 Azure 儲存體帳戶讀取資料，因此不需要壓縮此流量。
- 如果您使用的是 DPM 或 MABS，您可以在備份前壓縮資料，藉以節省頻寬。

**機器** | **壓縮到 MABS DPM (TCP)** | **壓縮至保存庫（HTTPS）**
--- | --- | ---
**內部部署 Windows 機器的直接備份** | NA | ![是][green]
**使用 VM 擴充功能來備份 Azure Vm** | NA | NA
**使用 MABS/DPM 在內部部署/Azure 機器上備份** | ![是][green] | ![是][green]

## <a name="retention-limits"></a>保留期限

**設定** | **限制**
--- | ---
**每個受保護的實例（機器或工作負載）的最大復原點** | 9999
**復原點的到期時間上限** | 沒有限制
**備份至 DPM/MABS 的頻率上限** | 每隔 15 分鐘 (SQL Server)<br/><br/> 針對其他工作負載一小時一次
**備份至保存庫的頻率上限** | 內部**部署 Windows 機器或執行 MARS 的 Azure vm：** 每天三個<br/><br/> **DPM/MABS：** 每天兩個<br/><br/> **AZURE VM 備份：** 一天一次
**復原點保留期** | 每日、每週、每月、每年
**最大保留期間** | 依照備份頻率而定
**DPM/MABS 磁碟上的復原點** | 64（適用于檔案伺服器）;448適用于應用程式伺服器 <br/><br/>內部部署 DPM 的磁帶復原點不受限制

## <a name="cross-region-restore"></a>跨區域還原

Azure 備份已新增跨區域還原功能，以加強資料可用性和復原功能，讓客戶可以完全控制將資料還原到次要區域。 若要設定這項功能，請造訪[Set 跨區域還原一文。](backup-create-rs-vault.md#set-cross-region-restore) 下列管理類型支援這項功能：

| 備份管理類型 | 支援                                                    | 支援的區域 |
| ---------------------- | ------------------------------------------------------------ | ----------------- |
| Azure VM               | 可以。   支援的加密 Vm 和具有低於 4 TB 磁片的 Vm | 所有 Azure 公用區域。  |
| MARS 代理程式/內部部署 | 否                                                           | N/A               |
| SQL/SAP HANA          | 否                                                           | N/A               |
| AFS                    | 否                                                           | N/A               |

## <a name="next-steps"></a>後續步驟

- [檢閱](backup-support-matrix-iaas.md) Azure VM 備份的支援矩陣。

[green]: ./media/backup-support-matrix/green.png
[yellow]: ./media/backup-support-matrix/yellow.png
[red]: ./media/backup-support-matrix/red.png
