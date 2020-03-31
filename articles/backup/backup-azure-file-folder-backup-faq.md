---
title: 備份檔案和資料夾 - 常見問題
description: 解決有關使用 Azure 備份備份檔案和資料夾的常見問題。
ms.topic: conceptual
ms.date: 07/29/2019
ms.openlocfilehash: adcbf5c3b404de46634423f8f59c4798d44bebe0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79273419"
---
# <a name="common-questions-about-backing-up-files-and-folders"></a>有關備份檔案和資料夾的常見問題

本文在[Azure 備份](backup-overview.md)服務中使用 Microsoft Azure 恢復服務 （MARS） 代理大量備份檔案和資料夾的常見問題。

## <a name="configure-backups"></a>設定備份

### <a name="where-can-i-download-the-latest-version-of-the-mars-agent"></a>我在哪裡可以下載最新版本的 MARS 代理？

備份 Windows 伺服器電腦、系統中心 DPM 和 Microsoft Azure 備份伺服器時使用的最新 MARS 代理可供[下載](https://aka.ms/azurebackup_agent)。

### <a name="how-long-are-vault-credentials-valid"></a>保存庫憑據的有效期是多少？

保存庫認證將於 48 小時後過期。 如果憑據檔過期，請再次從 Azure 門戶下載該檔。

### <a name="from-what-drives-can-i-back-up-files-and-folders"></a>可以從哪些磁碟機備份檔案和資料夾？

無法備份以下類型的磁碟機和卷：

* 可移動介質：所有備份項源都必須報告為固定。
* 唯讀卷：卷卷必須可寫，卷卷卷複製服務 （VSS） 才能正常工作。
* 離線卷：卷必須連線才能 VSS 正常工作。
* 網路共用：卷必須是伺服器的本地，才能使用連線備份進行備份。
* BitLocker 保護的卷：必須先解鎖卷，然後才能進行備份。
* 檔案系統識別碼：NTFS 是唯一支援的檔案系統。

### <a name="what-file-and-folder-types-are-supported"></a>支援哪些檔和資料夾類型？

[詳細瞭解](backup-support-matrix-mars-agent.md#supported-file-types-for-backup)支援備份的檔和資料夾類型。

### <a name="can-i-use-the-mars-agent-to-back-up-files-and-folders-on-an-azure-vm"></a>我可以使用 MARS 代理備份 Azure VM 上的檔和資料夾嗎？  

是。 Azure 備份使用 Azure VM 代理的 VM 擴展為 Azure VM 提供 VM 級備份。 如果要備份 VM 上的來賓 Windows 作業系統上的檔和資料夾，可以安裝 MARS 代理來執行此操作。

### <a name="can-i-use-the-mars-agent-to-back-up-files-and-folders-on-temporary-storage-for-the-azure-vm"></a>我可以使用 MARS 代理備份 Azure VM 臨時存儲上的檔和資料夾嗎？

是。 安裝 MARS 代理，並將來賓 Windows 作業系統上的檔和資料夾備份到臨時存儲。

* 清除臨時存儲資料時，備份作業將失敗。
* 如果刪除了臨時存儲資料，則只能還原到非易失性存儲。

### <a name="how-do-i-register-a-server-to-another-region"></a>如何將伺服器註冊到其他區域？

備份資料將發送到註冊伺服器的保存庫的資料中心。 更改資料中心的最簡單方法是卸載並重新安裝代理，然後將電腦註冊到所需的區域中的新保存庫。

### <a name="does-the-mars-agent-support-windows-server-2012-deduplication"></a>MARS 代理是否支援 Windows 伺服器 2012 重復資料消除？

是。 MARS 代理在準備備份操作時將重復資料轉換為正常資料。 然後優化備份資料，加密資料，然後將加密資料發送到保存庫。

## <a name="manage-backups"></a>管理備份

### <a name="what-happens-if-i-rename-a-windows-machine-configured-for-backup"></a>如果我重命名配置為備份的 Windows 電腦，會發生什麼情況？

重命名 Windows 電腦時，將停止所有當前配置的備份。

* 您需要在備份保存庫中註冊新電腦名稱稱。
* 當您向保存庫註冊新名稱時，第一個操作是*完整*備份。
* 如果需要使用舊伺服器名稱恢復備份到保存庫的資料，請使用 選項還原到"恢復資料嚮導"中的備用位置。 [深入了解](backup-azure-restore-windows-server.md#use-instant-restore-to-restore-data-to-an-alternate-machine)。

### <a name="what-is-the-maximum-file-path-length-for-backup"></a>備份的最大檔路徑長度是多少？

MARS 代理依賴于 NTFS，並使用受[Windows API](/windows/desktop/FileIO/naming-a-file#fully-qualified-vs-relative-paths)限制的檔路徑長度規範。 如果要保護的檔長於允許的值，則備份父資料夾或磁片磁碟機。  

### <a name="what-characters-are-allowed-in-file-paths"></a>檔路徑中允許哪些字元？

MARS 代理依賴于 NTFS，並允許檔案名/路徑中[支援字元](/windows/desktop/FileIO/naming-a-file#naming-conventions)。

### <a name="the-warning-azure-backups-have-not-been-configured-for-this-server-appears"></a>出現警告"尚未為此伺服器配置 Azure 備份"

即使您配置了備份策略，當存儲在本機伺服器上的備份計畫設置與存儲在備份保存庫中的設置不同時，也會顯示此警告。

* 當伺服器或設置已恢復到已知良好狀態時，備份計畫可能會變得不同步。
* 如果收到此警告，請再次[配置](backup-azure-manage-windows-server.md)備份策略，然後運行按需備份以將本機伺服器與 Azure 重新同步。

## <a name="manage-the-backup-cache-folder"></a>管理備份快取檔案夾

### <a name="whats-the-minimum-size-requirement-for-the-cache-folder"></a>什麼是快取資料夾的最低大小需求？

快取資料夾的大小可決定您正在備份的資料量。

* 快取檔案夾卷應具有至少等於備份資料總大小的 5-10% 的可用空間。
* 如果卷的可用空間小於 5%，請增加卷大小，或者按照[以下步驟](#how-do-i-change-the-cache-location-for-the-mars-agent)將快取檔案夾移動到具有足夠空間的卷。
* 如果備份 Windows 系統狀態，則需要在包含快取檔案夾的卷中增加 30-35 GB 的可用空間。

### <a name="how-to-check-if-scratch-folder-is-valid-and-accessible"></a>如何檢查暫存檔案夾是否有效且可訪問？

1. 預設情況下，暫存資料夾位於`\Program Files\Microsoft Azure Recovery Services Agent\Scratch`
2. 確保暫存資料夾位置的路徑與下面所示的註冊表鍵條目的值匹配：

    | 登錄路徑 | 登錄金鑰 | 值 |
    | --- | --- | --- |
    | `HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Config` |ScratchLocation |*「新的快取資料夾位置」* |
    | `HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Config\CloudBackupProvider` |ScratchLocation |*「新的快取資料夾位置」* |

### <a name="how-do-i-change-the-cache-location-for-the-mars-agent"></a>如何更改 MARS 代理的緩存位置？

1. 在提升的命令提示中運行此命令以停止備份引擎：

    ```Net stop obengine```
2. 如果已配置系統狀態備份，則打開磁片管理並卸載具有格式`"CBSSBVol_<ID>"`名稱的磁片。
3. 預設情況下，暫存檔案夾位於`\Program Files\Microsoft Azure Recovery Services Agent\Scratch`
4. 將整個`\Scratch`資料夾複製到具有足夠空間的不同磁碟機。 確保複製內容，而不是移動內容。
5. 使用新移動的暫存資料夾的路徑更新以下登錄機碼。

    | 登錄路徑 | 登錄金鑰 | 值 |
    | --- | --- | --- |
    | `HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Config` |ScratchLocation |*新的暫存檔案夾位置* |
    | `HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Config\CloudBackupProvider` |ScratchLocation |*新的暫存檔案夾位置* |

6. 在提升的命令提示符下重新開機備份引擎：

    ```command
    Net stop obengine

    Net start obengine
    ```

7. 執行隨選備份。 使用新位置成功完成備份後，可以刪除原始快取檔案夾。

### <a name="where-should-the-cache-folder-be-located"></a>快取檔案夾應位於何處？

不建議快取檔案夾的以下位置：

* 網路共用/卸除式媒體：快取檔案夾必須是需要使用連線備份進行備份的伺服器的本地資料夾。 不支援網路位置或卸除式媒體（如 USB 磁碟機）。
* 離線卷：快取檔案夾必須連線才能使用 Azure 備份代理進行預期備份

### <a name="are-there-any-attributes-of-the-cache-folder-that-arent-supported"></a>快取檔案夾是否有任何不支援的屬性？

快取資料夾不支援下列屬性或其組合︰

* 已加密
* 已刪除重複資料
* Compressed
* 疏鬆
* 重新分析點

快取檔案夾和中繼資料 VHD 沒有 Azure 備份代理的必要屬性。

### <a name="is-there-a-way-to-adjust-the-amount-of-bandwidth-used-for-backup"></a>是否有方法可以調整用於備份的頻寬量？

可以，您可以使用 MARS 代理中的 **"更改屬性"** 選項來調整頻寬和計時。 [深入了解](backup-windows-with-mars-agent.md#enable-network-throttling)。

## <a name="restore"></a>還原

### <a name="manage"></a>管理

**如果我忘記了密碼，我可以恢復嗎？**
Azure 備份代理需要密碼（您在註冊期間提供）才能在還原期間解密備份資料。 查看以下方案以瞭解處理丟失密碼的選項：

| 原始機器 <br> *（執行備份的源電腦）* | 複雜密碼 | 可用的選項 |
| --- | --- | --- |
| 可用 |未能拿下 |如果原始電腦（執行備份的位置）可用，並且仍註冊在同一恢復服務保存庫中，則可以按照[以下步驟](https://docs.microsoft.com/azure/backup/backup-azure-manage-mars#re-generate-passphrase)重新生成密碼。  |
| 未能拿下 |未能拿下 |無法恢復資料或資料不可用 |

請考慮下列情況：

* 如果在同一原始電腦上卸載和重新註冊代理，
  * *相同的密碼，* 然後您將能夠恢復備份的資料。
  * *不同的密碼*，那麼你將無法恢復備份的資料。
* 如果將代理安裝在*其他電腦上*，
  * *相同的密碼（* 在原始電腦中使用），然後您將能夠還原備份的資料。
  * *不同的密碼，* 您將無法還原備份的資料。
* 如果原始電腦已損壞（阻止您通過 MARS 主控台重新生成密碼），但您可以還原或訪問 MARS 代理使用的原始暫存資料夾，則您可能能夠還原（如果您忘記了密碼）。 如需更多説明，請聯繫客戶支援。

**如果我丟失了原始電腦（備份所在的位置），如何恢復？**

如果原始電腦具有相同的密碼（註冊期間提供），則可以將備份的資料還原到備用電腦。 查看以下方案以瞭解還原選項。

| 原始機器 | 複雜密碼 | 可用的選項 |
| --- | --- | --- |
| 未能拿下 |可用 |您可以在另一台電腦上安裝和註冊 MARS 代理，其密碼與註冊原始電腦時提供的相同密碼相同。 選擇 **"恢復選項** > **""其他位置**以執行還原。 有關詳細資訊，請參閱[本文](https://docs.microsoft.com/azure/backup/backup-azure-restore-windows-server#use-instant-restore-to-restore-data-to-an-alternate-machine)。
| 未能拿下 |未能拿下 |無法恢復資料或資料不可用 |


### <a name="what-happens-if-i-cancel-an-ongoing-restore-job"></a>如果我取消進行中的還原作業，會發生什麼事？

如果正在進行的還原作業被取消，還原過程將停止。 取消之前還原的所有檔都保留在配置的目標（原始或備用位置），沒有任何回滾。

### <a name="does-the-mars-agent-back-up-and-restore-acls-set-on-files-folders-and-volumes"></a>MARS 代理是否備份和恢復在檔、資料夾和卷上設置的 ACL？

* MARS 代理備份在檔、資料夾和卷上設置的 ACL
* 對於卷還原恢復選項，MARS 代理提供了一個選項，用於跳過對正在恢復的檔或資料夾還原 ACL 許可權
* 對於單個檔和資料夾恢復選項，MARS 代理將使用 ACL 許可權進行還原（沒有跳過 ACL 還原的選項）。

## <a name="next-steps"></a>後續步驟

[瞭解如何](tutorial-backup-windows-server-to-azure.md)備份 Windows 電腦。
