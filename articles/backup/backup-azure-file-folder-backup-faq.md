---
title: 備份檔案和資料夾-常見的問題
description: 解決有關使用 Azure 備份來備份檔案和資料夾的常見問題。
ms.topic: conceptual
ms.date: 07/29/2019
ms.openlocfilehash: 7b80932d49038bb42fa93f71b3ac0194c2869489
ms.sourcegitcommit: b8f2fee3b93436c44f021dff7abe28921da72a6d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/18/2020
ms.locfileid: "77425063"
---
# <a name="common-questions-about-backing-up-files-and-folders"></a>備份檔案和資料夾的相關常見問題

本文會回答在[Azure 備份](backup-overview.md)服務中使用 MICROSOFT AZURE 復原服務（MARS）代理程式來備份檔案和資料夾的常見問題為數眾多。

## <a name="configure-backups"></a>設定備份

### <a name="where-can-i-download-the-latest-version-of-the-mars-agent"></a>哪裡可以下載最新版的 MARS 代理程式？

備份 Windows Server 電腦、System Center DPM 和 Microsoft Azure 備份 Server 時所使用的最新 MARS 代理程式已可供[下載](https://aka.ms/azurebackup_agent)。

### <a name="how-long-are-vault-credentials-valid"></a>保存庫認證有效多久？

保存庫認證將於 48 小時後過期。 如果認證檔案過期，請從 Azure 入口網站重新下載檔案。

### <a name="from-what-drives-can-i-back-up-files-and-folders"></a>我可以從哪些磁片磁碟機備份檔案和資料夾？

您無法備份下列類型的磁片磁碟機和磁片區：

* 卸載式媒體：所有備份專案來源必須報告為固定。
* 唯讀磁片區：磁片區必須為可寫入，磁片區陰影複製服務（VSS）才能正常運作。
* 離線磁片區：磁片區必須在線上，VSS 才能運作。
* 網路共用：磁片區必須是本機伺服器，才能使用線上備份進行備份。
* 受 BitLocker 保護的磁片區：必須先解除鎖定磁片區，才能進行備份。
* 檔案系統識別碼：NTFS 是唯一支援的檔案系統。

### <a name="what-file-and-folder-types-are-supported"></a>支援哪些檔案和資料夾類型？

[深入瞭解](backup-support-matrix-mars-agent.md#supported-file-types-for-backup)支援備份的檔和資料夾類型。

### <a name="can-i-use-the-mars-agent-to-back-up-files-and-folders-on-an-azure-vm"></a>我可以使用 MARS 代理程式來備份 Azure VM 上的檔案和資料夾嗎？  

是。 Azure 備份使用適用于 Azure VM 代理程式的 VM 擴充功能，為 Azure Vm 提供 VM 層級備份。 如果您想要備份 VM 上的來賓 Windows 作業系統上的檔案和資料夾，您可以安裝 MARS 代理程式來執行該作業。

### <a name="can-i-use-the-mars-agent-to-back-up-files-and-folders-on-temporary-storage-for-the-azure-vm"></a>我可以使用 MARS 代理程式來備份 Azure VM 暫存儲存體上的檔案和資料夾嗎？

是。 安裝 MARS 代理程式，並將來賓 Windows 作業系統上的檔案和資料夾備份至暫存儲存體。

* 清除暫存儲存體資料時，備份作業會失敗。
* 如果暫存的儲存體資料已刪除，您只能還原至非變動性儲存體。

### <a name="how-do-i-register-a-server-to-another-region"></a>如何? 將伺服器註冊到另一個區域？

備份資料會傳送至註冊伺服器之保存庫的資料中心。 變更資料中心最簡單的方式是卸載並重新安裝代理程式，然後在您所需的區域中，將電腦註冊至新的保存庫。

### <a name="does-the-mars-agent-support-windows-server-2012-deduplication"></a>MARS 代理程式是否支援 Windows Server 2012 重復資料刪除？

是。 MARS 代理程式在準備備份作業時，會將重復資料刪除的資料轉換成一般資料。 然後，它會將資料優化以進行備份、將資料加密，然後將加密的資料傳送至保存庫。

## <a name="manage-backups"></a>管理備份

### <a name="what-happens-if-i-rename-a-windows-machine-configured-for-backup"></a>如果我重新命名已設定備份的 Windows 電腦，會發生什麼事？

當您重新命名 Windows 機器時，所有目前設定的備份都會停止。

* 您必須向備份保存庫註冊新的電腦名稱稱。
* 當您向保存庫註冊新名稱時，第一個作業是*完整*備份。
* 如果您需要將備份的資料復原到具有舊伺服器名稱的保存庫，請使用 [復原資料] 嚮導中的 [還原至替代位置] 選項。 [詳細資訊](backup-azure-restore-windows-server.md#use-instant-restore-to-restore-data-to-an-alternate-machine)。

### <a name="what-is-the-maximum-file-path-length-for-backup"></a>備份的檔案路徑長度上限為何？

MARS 代理程式依賴 NTFS，並使用[WINDOWS API](/windows/desktop/FileIO/naming-a-file#fully-qualified-vs-relative-paths)所限制的 filepath 長度規格。 如果您想要保護的檔案超過允許的值，請備份上層資料夾或磁片磁碟機。  

### <a name="what-characters-are-allowed-in-file-paths"></a>檔案路徑中允許哪些字元？

MARS 代理程式依賴 NTFS，並允許檔案名/路徑中[支援的字元](/windows/desktop/FileIO/naming-a-file#naming-conventions)。

### <a name="the-warning-azure-backups-have-not-been-configured-for-this-server-appears"></a>出現警告「未設定這部伺服器的 Azure 備份」

雖然您已設定備份原則，但在本機伺服器上儲存的備份排程設定與備份保存庫中儲存的設定不相同，但仍會出現此警告。

* 當伺服器或設定已復原至已知的良好狀態時，備份排程可能會變成未同步處理。
* 如果您收到此警告，請再次[設定](backup-azure-manage-windows-server.md)備份原則，然後執行隨選備份，以重新同步處理本機伺服器與 Azure。

## <a name="manage-the-backup-cache-folder"></a>管理備份快取資料夾

### <a name="whats-the-minimum-size-requirement-for-the-cache-folder"></a>什麼是快取資料夾的最低大小需求？

快取資料夾的大小可決定您正在備份的資料量。

* 快取資料夾磁片區的可用空間應等於備份資料大小總計的5-10%。
* 如果磁片區的可用空間少於5%，請增加磁片區大小，或依照下列[步驟](#how-do-i-change-the-cache-location-for-the-mars-agent)將快取資料夾移至具有足夠空間的磁片區。
* 如果您備份 Windows 系統狀態，在包含快取資料夾的磁片區中，您將需要額外 30-35 GB 的可用空間。

### <a name="how-to-check-if-scratch-folder-is-valid-and-accessible"></a>如何檢查暫存檔案夾是否有效並可存取？

1. 根據預設，暫存檔案夾位於 `\Program Files\Microsoft Azure Recovery Services Agent\Scratch`
2. 請確定您的暫存檔案夾位置路徑符合如下所示的登錄機碼專案的值：

    | 登錄路徑 | 登錄金鑰 | 值 |
    | --- | --- | --- |
    | `HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Config` |ScratchLocation |*「新的快取資料夾位置」* |
    | `HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Config\CloudBackupProvider` |ScratchLocation |*「新的快取資料夾位置」* |

### <a name="how-do-i-change-the-cache-location-for-the-mars-agent"></a>如何? 變更 MARS 代理程式的快取位置？

1. 在提高許可權的命令提示字元中執行此命令，以停止備份引擎：

    ```Net stop obengine```
2. 如果您已設定系統狀態備份，請開啟 [磁片管理]，並將名稱格式為 `"CBSSBVol_<ID>"`的磁片卸載。
3. 根據預設，暫存檔案夾位於 `\Program Files\Microsoft Azure Recovery Services Agent\Scratch`
4. 將整個 `\Scratch` 資料夾複製到具有足夠空間的其他磁片磁碟機。 請確定已複製內容，而不是移動。
5. 以新移動的暫存檔案夾路徑更新下列登錄專案。

    | 登錄路徑 | 登錄金鑰 | 值 |
    | --- | --- | --- |
    | `HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Config` |ScratchLocation |*新的暫存檔案夾位置* |
    | `HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Config\CloudBackupProvider` |ScratchLocation |*新的暫存檔案夾位置* |

6. 在提高許可權的命令提示字元下，重新開機備份引擎：

    ```command
    Net stop obengine

    Net start obengine
    ```

7. 執行隨選備份。 使用新位置成功完成備份之後，您就可以移除原始的快取資料夾。

### <a name="where-should-the-cache-folder-be-located"></a>快取資料夾的所在位置？

不建議將下列位置用於快取資料夾：

* 網路共用/卸載式媒體：快取資料夾必須在需要使用線上備份進行備份之伺服器的本機位置。 不支援網路位置或卸載式媒體（例如 USB 磁片磁碟機）。
* 離線磁片區：快取資料夾必須在線上，才能使用 Azure 備份代理程式進行預期的備份

### <a name="are-there-any-attributes-of-the-cache-folder-that-arent-supported"></a>是否有任何不支援的快取資料夾屬性？

快取資料夾不支援下列屬性或其組合︰

* 已加密
* 已刪除重複資料
* Compressed
* 疏鬆
* 重新分析點

快取資料夾和中繼資料 VHD 沒有 Azure 備份代理程式的必要屬性。

### <a name="is-there-a-way-to-adjust-the-amount-of-bandwidth-used-for-backup"></a>是否有方法可以調整用於備份的頻寬量？

是，您可以使用 MARS 代理程式中的 [**變更屬性**] 選項來調整頻寬和時間。 [詳細資訊](backup-configure-vault.md#enable-network-throttling)。

## <a name="restore"></a>{1}還原{2}

### <a name="manage"></a>管理

**如果我忘記複雜密碼，可以復原嗎？**
Azure 備份代理程式需要複雜密碼（在註冊期間提供）來解密還原期間的備份資料。 請參閱下列案例，以瞭解處理遺失複雜密碼的選項：

| 原始電腦 <br> *（執行備份的來源機器）* | 複雜密碼 | 可用的選項 |
| --- | --- | --- |
| 可用 |單 |如果您的原始電腦（已建立備份的位置）可供使用，而且仍使用相同的復原服務保存庫註冊，則您可以遵循下列[步驟](https://docs.microsoft.com/azure/backup/backup-azure-manage-mars#re-generate-passphrase)來重新產生複雜密碼。  |
| 單 |單 |無法復原資料或資料無法使用 |

請考慮下列條件：

* 如果您在相同的原始電腦上卸載並重新註冊代理程式
  * *相同*的複雜密碼之後，您就能夠還原已備份的資料。
  * *不同*的複雜密碼，那麼您將無法還原已備份的資料。
* 如果您使用將代理程式安裝在*不同的電腦*上
  * *相同*的複雜密碼（用於原始機器），您將能夠還原已備份的資料。
  * *不同*的複雜密碼，您將無法還原已備份的資料。
* 如果您的原始電腦已損毀（導致無法透過 MARS 主控台重新產生複雜密碼），但您可以還原或存取 MARS 代理程式所使用的原始暫存檔案夾，則您可能可以還原（如果忘記密碼）。 如需更多協助，請聯絡客戶支援。

**如果我遺失原始電腦（備份的位置），如何? 復原？**

如果您的原始電腦具有相同的複雜密碼（在註冊期間提供），則可以將備份的資料還原至其他電腦。 請參閱下列案例，以瞭解您的還原選項。

| 原始電腦 | 複雜密碼 | 可用的選項 |
| --- | --- | --- |
| 單 |可用 |您可以在另一部電腦上安裝並註冊 MARS 代理程式，其具有在您註冊原始電腦時所提供的相同複雜密碼。 選擇 [復原**選項**] > **另一個**要執行還原的位置。 如需詳細資訊，請參閱這篇[文章](https://docs.microsoft.com/azure/backup/backup-azure-restore-windows-server#use-instant-restore-to-restore-data-to-an-alternate-machine)。
| 單 |單 |無法復原資料或資料無法使用 |


### <a name="what-happens-if-i-cancel-an-ongoing-restore-job"></a>如果我取消進行中的還原作業，會發生什麼事？

如果已取消進行中的還原作業，還原程式就會停止。 在取消之前還原的所有檔案都會保留在設定的目的地（原始或替代位置），而不會有任何復原。

### <a name="does-the-mars-agent-back-up-and-restore-acls-set-on-files-folders-and-volumes"></a>MARS 代理程式會備份和還原在檔案、資料夾和磁片區上設定的 Acl 嗎？

* MARS 代理程式會備份在檔案、資料夾和磁片區上設定的 Acl
* 針對磁片區還原復原選項，MARS 代理程式會提供一個選項，以略過將 ACL 許可權還原至要復原的檔案或資料夾
* 針對 [個別檔案及資料夾] 復原選項，MARS 代理程式會以 ACL 許可權進行還原（不會有略過 ACL 還原的選項）。

## <a name="next-steps"></a>後續步驟

[瞭解](tutorial-backup-windows-server-to-azure.md)如何備份 Windows 電腦。
