---
title: Azure 備份支援矩陣
description: 摘要說明 Azure 備份服務的支援設定和限制。
ms.topic: conceptual
ms.date: 02/17/2019
ms.openlocfilehash: d036e527880a98d323e8de2f3a8721d7e12dbb07
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79273263"
---
# <a name="support-matrix-for-azure-backup"></a>Azure 備份的支援矩陣

可以使用[Azure 備份](backup-overview.md)將資料備份到 Microsoft Azure 雲平臺。 本文總結了 Azure 備份方案和部署的一般支援設置和限制。

另有其他的支援矩陣可供使用：

- [Azure 虛擬機器 （VM） 備份](backup-support-matrix-iaas.md)的支援矩陣
- 使用[系統中心資料保護管理器 （DPM）/微軟 Azure 備份伺服器 （MABS）](backup-support-matrix-mabs-dpm.md)支援備份矩陣
- 使用[Microsoft Azure 恢復服務 （MARS） 代理](backup-support-matrix-mars-agent.md)支援備份矩陣

[!INCLUDE [azure-lighthouse-supported-service](../../includes/azure-lighthouse-supported-service.md)]

## <a name="vault-support"></a>保存庫支援

Azure 備份使用恢復服務保存庫來協調和管理備份。 它還使用保存庫來存儲備份的資料。

下表描述了恢復服務保存庫的功能：

**特徵** | **詳細資料**
--- | ---
**訂用帳戶中的保存庫** | 一個訂用帳戶中最多可以有 500 個復原服務保存庫。
**保存庫中的機器** | 單個保存庫中最多有 1，000 個 Azure VM。<br/><br/> 可在單一保存庫中註冊最多 50 個 MABS 伺服器。
**資料來源** | 單個[資料來源](https://docs.microsoft.com/azure/backup/backup-azure-backup-faq#how-is-the-data-source-size-determined)的最大大小為 54，400 GB。 此限制不適用於 Azure VM 備份。 可以備份到保存庫的資料總量沒有限制。
**備份至保存庫** | **Azure VM：** 一天一次<br/><br/>**受 DPM/MABS 保護的機器：** 一天兩次<br/><br/> **使用 MARS 代理直接備份的電腦：** 一天三次
**保存庫之間的備份** | 在區域內備份。<br/><br/> 您在每個 Azure 區域中都必須有一個保存庫包含您想要備份的 VM。 您無法備份至不同的區域。
**移動保存庫** | 您可以在訂閱之間或同一訂閱中的資源組之間[移動保存庫](https://docs.microsoft.com/azure/backup/backup-azure-move-recovery-services-vault)。 但是，不支援跨區域移動保存庫。
**在保存庫之間移動資料** | 不支援在保存庫之間移動備份資料。
**修改保存庫儲存體類型** | 在存儲備份之前，可以修改保存庫的存儲複製類型（異地冗余存儲或本地冗余存儲）。 開始在保存庫中進行備份後，即無法修改複寫類型。

## <a name="on-premises-backup-support"></a>內部部署備份支援

如果要備份本地電腦，支援的內容：

**機器** | **備份的內容** | **位置** | **特徵**
--- | --- | --- | ---
**透過 MARS 代理程式直接備份 Windows 機器** | 檔案、資料夾、系統狀態 | 備份到恢復服務保存庫。 | 每天備份三次<br/><br/> 無應用感知備份<br/><br/> 還原檔、資料夾、卷
**透過 MARS 代理程式直接備份 Linux 機器** | 不支援備份
**備份到 DPM** | 檔、資料夾、卷、系統狀態、應用資料 | 備份至本機 DPM 儲存體。 然後，DPM 再備份至保存庫。 | 應用程式感知快照集<br/><br/> 用於備份和恢復的完整細微性<br/><br/> 支援 VM 的 Linux（Hyper-V/VMware）<br/><br/> 不支援 Oracle
**備份至 MABS** | 檔、資料夾、卷、系統狀態、應用資料 | 備份至 MABS 本機儲存體。 然後，MABS 再備份至保存庫。 | 應用程式感知快照集<br/><br/> 用於備份和恢復的完整細微性<br/><br/> 支援 VM 的 Linux（Hyper-V/VMware）<br/><br/> 不支援 Oracle

## <a name="azure-vm-backup-support"></a>Azure VM 備份支援

### <a name="azure-vm-limits"></a>Azure VM 限制

**限制** | **詳細資料**
--- | ---
**Azure VM 資料磁碟** | 限制 16 <br> 若要註冊具有 16 個以上磁碟 (最多 32 個磁碟) 的 VM 私人預覽版，請利用 AskAzureBackupTeam@microsoft.com 寫信給我們
**Azure VM 資料磁碟大小** | 單個磁片大小最多可為 32 TB，VM 中的所有磁片最多可組合 256 TB。

### <a name="azure-vm-backup-options"></a>Azure VM 備份選項

如果要備份 Azure VM，則支援這些功能：

**機器** | **備份的內容** | **位置** | **特徵**
--- | --- | --- | ---
**使用 VM 擴展進行 Azure VM 備份** | 整個 VM | 備份到保存庫。 | 當您為 VM 啟用備份時安裝的延伸模組。<br/><br/> 每天備份一次。<br/><br/> 適用于 Windows VM 的應用感知備份;適用于 Linux VM 的檔一致性備份。 您可以使用自訂腳本為 Linux 電腦配置應用一致性。<br/><br/> 還原 VM 或磁片。<br/><br/> 無法將 Azure VM 備份到本地位置。
**使用 MARS 代理進行 Azure VM 備份** | 檔案、資料夾、系統狀態 | 備份到保存庫。 | 每天備份三次。<br/><br/> 如果要備份特定檔或資料夾，而不是整個 VM，MARS 代理可以與 VM 副檔名一起運行。
**使用 DPM 的 Azure VM** | 檔、資料夾、卷、系統狀態、應用資料 | 備份到運行 DPM 的 Azure VM 的本機存放區。 然後，DPM 再備份至保存庫。 | 應用程式感知快照。<br/><br/> 備份和復原的完整細微性。<br/><br/> VM (Hyper-V/VMware) 支援 Linux。<br/><br/> 不支援 Oracle。
**使用 MABS 的 Azure VM** | 檔、資料夾、卷、系統狀態、應用資料 | 備份到運行 MABS 的 Azure VM 的本機存放區。 然後，MABS 再備份至保存庫。 | 應用程式感知快照。<br/><br/> 備份和復原的完整細微性。<br/><br/> VM (Hyper-V/VMware) 支援 Linux。<br/><br/> 不支援 Oracle。

## <a name="linux-backup-support"></a>Linux 備份支援

如果要備份 Linux 電腦，支援的內容：

**備份類型** | **Linux (Azure 背書)**
--- | ---
**運行 Linux 的本地電腦的直接備份** | 不支援。 MARS 代理只能安裝在 Windows 電腦上。
**使用代理擴展來備份運行 Linux 的 Azure VM** | 使用[自訂腳本](backup-azure-linux-app-consistent.md)進行應用一致的備份。<br/><br/> 檔案層級復原。<br/><br/> 從復原點或磁碟建立 VM 以進行還原。
**使用 DPM 備份運行 Linux 的本地電腦** | 在 Hyper-V 和 VMWare 上對 Linux 來賓 VM 進行檔一致的備份。<br/><br/> 虛擬機器還原 Hyper-V 和 VMWare Linux 來賓 VM。
**使用 MABS 備份運行 Linux 的本地電腦** | 在 Hyper-V 和 VMWare 上對 Linux 來賓 VM 進行檔一致的備份。<br/><br/> 虛擬機器還原 Hyper-V 和 VMWare Linux 來賓 VM。
**使用 MABS 或 DPM 備份 Linux Azure VM** | 不支援。

## <a name="daylight-saving-time-support"></a>夏令時支援

Azure 備份不支援 Azure VM 備份的夏令時自動時鐘調整。 它不會向前或向後移動備份小時。 為確保備份在所需時間運行，請根據需要手動修改備份策略。

## <a name="disk-deduplication-support"></a>磁碟重複資料刪除支援

磁碟重複資料刪除支援如下︰

- 當您使用 DPM 或 MAB 備份運行 Windows 的超 VM 時，磁片重復資料消除在本地支援。 Windows Server 在作為備份存儲連接到 VM 的虛擬硬碟 （VHD） 上執行資料重復資料重復資料（在主機分級）。
- 在 Azure 中不支援對任何備份元件進行重複資料刪除。 在 Azure 中部署 DPM 和 MABS 時，無法對連接到 VM 的存儲磁片進行重複複製。

## <a name="security-and-encryption-support"></a>安全和加密支援

Azure 備份支援對傳輸中和靜態資料的加密。

### <a name="network-traffic-to-azure"></a>到 Azure 的網路流量

- 使用進階加密標準 256 對從伺服器到恢復服務保存庫的備份流量進行加密。
- 備份資料會透過安全的 HTTPS 連結來傳送。
- 備份資料以加密形式存儲在恢復服務保存庫中。
- 只有您持有可將此資料解除鎖定的複雜密碼。 Microsoft 無法解密在任何時間點備份的資料。

    > [!WARNING]
    > 設定保存庫後，只有您能夠存取加密金鑰。 Microsoft 絕不會持有金鑰複本，也沒有存取金鑰的權限。 如果金鑰遺失，Microsoft 將無法復原備份資料。

### <a name="data-security"></a>資料安全性

- 備份 Azure VM 時，需要在虛擬機器*中*設置加密。
- Azure 備份支援 Azure 磁碟加密，其會在 Windows 虛擬機器上使用 BitLocker，而在 Linux 虛擬機器上使用 **dm-crypt**。
- 在後端，Azure 備份使用[Azure 存儲服務加密](../storage/common/storage-service-encryption.md)，以保護靜態資料。

**機器** | **傳輸中** | **待用**
--- | --- | ---
**不含 DPM/MABS 的內部部署 Windows 機器** | ![是][green] | ![是][green]
**Azure VM** | ![是][green] | ![是][green]
**本地 Windows 電腦或具有 DPM 的 Azure VM** | ![是][green] | ![是][green]
**具有 MABS 的本地 Windows 電腦或 Azure VM** | ![是][green] | ![是][green]

## <a name="compression-support"></a>壓縮支援

備份支援壓縮備份流量，如下表摘要。

- 對於 Azure VM，VM 擴展程式通過存儲網路直接從 Azure 存儲帳戶讀取資料，因此無需壓縮此流量。
- 如果使用 DPM 或 MABS，則可以在備份資料之前壓縮資料來節省頻寬。

**機器** | **壓縮到 MABS DPM (TCP)** | **壓縮到保存庫 （HTTPS）**
--- | --- | ---
**內部部署 Windows 機器的直接備份** | NA | ![是][green]
**使用 VM 擴展備份 Azure VM** | NA | NA
**使用 MABS/DPM 在本地/Azure 電腦上備份** | ![是][green] | ![是][green]

## <a name="retention-limits"></a>保留期限

**設定** | **限制**
--- | ---
**每個受保護實例（機器或工作負載）的最大復原點** | 9,999
**復原點的到期時間上限** | 沒有限制
**備份至 DPM/MABS 的頻率上限** | 每隔 15 分鐘 (SQL Server)<br/><br/> 其他工作負載每小時一次
**備份至保存庫的頻率上限** | **運行 MARS 的本地 Windows 電腦或 Azure VM：** 每天三次<br/><br/> **DPM/MABS：** 每天兩次<br/><br/> **Azure VM 備份：** 每天一次
**復原點保留** | 每日、每週、每月、每年
**最大保留期間** | 依照備份頻率而定
**DPM/MABS 磁碟上的復原點** | 64 用於檔案伺服器;448 用於應用伺服器 <br/><br/>用於本地 DPM 的無限磁帶復原點

## <a name="cross-region-restore"></a>跨區域恢復

Azure 備份添加了跨區域還原功能，以增強資料可用性和恢復能力，使客戶完全控制將資料還原到次要區域。 要配置此功能，請訪問["設置跨區域還原"一文。](backup-create-rs-vault.md#set-cross-region-restore) 以下管理類型支援此功能：

| 備份管理類型 | 支援                                                    | 支援的區域 |
| ---------------------- | ------------------------------------------------------------ | ----------------- |
| Azure VM               | 是。 支援用於磁片小於 4 TB 的加密 VM 和 VM 的公共有限預覽 | 美國中西部   |
| MARS 代理/內部 | 否                                                           | N/A               |
| SQL /SAP HANA          | 否                                                           | N/A               |
| Afs                    | 否                                                           | N/A               |

## <a name="next-steps"></a>後續步驟

- [檢閱](backup-support-matrix-iaas.md) Azure VM 備份的支援矩陣。

[green]: ./media/backup-support-matrix/green.png
[yellow]: ./media/backup-support-matrix/yellow.png
[red]: ./media/backup-support-matrix/red.png
