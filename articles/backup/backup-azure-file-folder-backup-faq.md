---
title: Microsoft Azure 復原服務 (MARS) 代理程式-常見問題
description: 解決使用 Azure 備份備份檔案和資料夾的相關常見問題。
ms.topic: conceptual
ms.date: 07/29/2019
ms.openlocfilehash: ca2753e4d6da7e2e3079f234a3facac27fd5f098
ms.sourcegitcommit: d7352c07708180a9293e8a0e7020b9dd3dd153ce
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/30/2020
ms.locfileid: "89144459"
---
# <a name="frequently-asked-questions---microsoft-azure-recovery-services-mars-agent"></a>常見問題-Microsoft Azure 復原服務 (MARS) 代理程式

本文將回答有關使用 [Azure 備份](backup-overview.md) 服務中的 MICROSOFT AZURE 復原服務 (MARS) 代理程式來備份資料的常見問題。

## <a name="configure-backups"></a>設定備份

### <a name="where-can-i-download-the-latest-version-of-the-mars-agent"></a>哪裡可以下載最新版本的 MARS 代理程式？

備份 Windows Server 機器、System Center DPM 和 Microsoft Azure 備份 Server 時，所使用的最新 MARS 代理程式已可供 [下載](https://aka.ms/azurebackup_agent)。

### <a name="where-can-i-download-the-vault-credentials-file"></a>哪裡可以下載保存庫認證檔案？

在 Azure 入口網站中，流覽至您保存庫的 **屬性** 。 在 [ **備份認證**] 下，選取 **已使用最新復原服務代理程式**的核取方塊。 選取 [下載]。

![下載認證](./media/backup-azure-file-folder-backup-faq/download-credentials.png)

### <a name="how-long-are-vault-credentials-valid"></a>保存庫認證的有效期限有多長？

保存庫認證會在10天后到期。 如果認證檔案過期，請從 Azure 入口網站重新下載檔案。

### <a name="what-characters-are-allowed-for-the-passphrase"></a>複雜密碼允許哪些字元？

複雜密碼應使用 ASCII 字元集中的字元， [ascii 值小於或等於 127](https://docs.microsoft.com/office/vba/language/reference/user-interface-help/character-set-0127)。

### <a name="from-what-drives-can-i-back-up-files-and-folders"></a>我可以從哪些磁片磁碟機備份檔案和資料夾？

您無法備份下列類型的磁片磁碟機和磁片區：

* 卸載式媒體：所有備份專案來源都必須報告為固定的。
* 唯讀磁片區：磁片區必須可寫入磁片區陰影複製服務， (VSS) 運作。
* 離線磁片區：磁片區必須在線上，VSS 才能運作。
* 網路共用：磁片區必須位於伺服器本機，才能使用線上備份進行備份。
* 受 BitLocker 保護的磁片區：磁片區必須先解除鎖定，才能進行備份。
* 檔案系統識別碼：NTFS 是唯一支援的檔案系統。

### <a name="what-file-and-folder-types-are-supported"></a>支援的檔案和資料夾類型為何？

[深入瞭解](backup-support-matrix-mars-agent.md#supported-file-types-for-backup) 支援備份的檔和資料夾類型。

### <a name="can-i-use-the-mars-agent-to-back-up-files-and-folders-on-an-azure-vm"></a>我可以使用 MARS 代理程式來備份 Azure VM 上的檔案和資料夾嗎？  

是。 Azure 備份使用 Azure VM 代理程式的 VM 延伸模組，為 Azure Vm 提供 VM 層級的備份。 如果您想要在 VM 上備份來賓 Windows 作業系統上的檔案和資料夾，您可以安裝 MARS 代理程式來進行。

### <a name="can-i-use-the-mars-agent-to-back-up-files-and-folders-on-temporary-storage-for-the-azure-vm"></a>我可以使用 MARS 代理程式來備份 Azure VM 暫存儲存體上的檔案和資料夾嗎？

是。 安裝 MARS 代理程式，並將來賓 Windows 作業系統上的檔案和資料夾備份至暫存儲存體。

* 清除暫存儲存體資料時，備份作業會失敗。
* 如果刪除暫存儲存體資料，您只能還原至非暫時性的儲存體。

### <a name="how-do-i-register-a-server-to-another-region"></a>如何? 將伺服器註冊到另一個區域？

備份資料會傳送至已註冊伺服器的保存庫的資料中心。 變更資料中心最簡單的方式，就是卸載並重新安裝代理程式，然後在您需要的區域中將電腦註冊到新的保存庫。

### <a name="does-the-mars-agent-support-windows-server-2012-deduplication"></a>MARS 代理程式是否支援 Windows Server 2012 重復資料刪除？

是。 MARS 代理程式在準備備份作業時，會將重復資料刪除的資料轉換成一般資料。 然後，它會將備份的資料優化，然後將資料加密，然後將加密的資料傳送至保存庫。

### <a name="do-i-need-administrator-permissions-to-install-and-configure-the-mars-agent"></a>是否需要系統管理員許可權才能安裝及設定 MARS 代理程式？

是的，MARS 代理程式的安裝和使用 MARS 主控台的備份設定，需要使用者是受保護伺服器上的本機系統管理員。

## <a name="manage-backups"></a>管理備份

### <a name="what-happens-if-i-rename-a-windows-machine-configured-for-backup"></a>如果重新命名設定為備份的 Windows 電腦，會發生什麼事？

當您重新命名 Windows 電腦時，會停止所有目前設定的備份。

* 您必須向備份保存庫註冊新的電腦名稱稱。
* 當您向保存庫註冊新名稱時，第一項作業是 *完整* 備份。
* 如果您需要使用舊的伺服器名稱來復原備份至保存庫的資料，請使用 [復原資料] 嚮導中的選項來還原至替代位置。 [深入了解](backup-azure-restore-windows-server.md#use-instant-restore-to-restore-data-to-an-alternate-machine)。

### <a name="what-is-the-maximum-file-path-length-for-backup"></a>備份的檔案路徑長度上限為何？

MARS 代理程式依賴 NTFS，並且使用 [WINDOWS API](/windows/win32/FileIO/naming-a-file#fully-qualified-vs-relative-paths)所限制的 filepath 長度規格。 如果您想要保護的檔案長度超過允許的值，請備份上層資料夾或磁片磁碟機。  

### <a name="what-characters-are-allowed-in-file-paths"></a>檔案路徑中允許哪些字元？

MARS 代理程式依賴 NTFS，並允許檔案名/路徑中 [支援的字元](/windows/win32/FileIO/naming-a-file#naming-conventions) 。

### <a name="the-warning-azure-backups-have-not-been-configured-for-this-server-appears"></a>出現「尚未為此伺服器設定 Azure 備份」警告

即使您已設定備份原則，在本機伺服器上儲存的備份排程設定與備份保存庫中儲存的設定不同時，還是會出現此警告。

* 當伺服器或設定已復原為已知的良好狀態時，備份排程可能會變成未同步處理。
* 如果您收到此警告，請再次 [設定](backup-azure-manage-windows-server.md) 備份原則，然後執行隨選備份，以將本機伺服器與 Azure 重新同步。

## <a name="manage-the-backup-cache-folder"></a>管理備份快取資料夾

### <a name="whats-the-minimum-size-requirement-for-the-cache-folder"></a>什麼是快取資料夾的最低大小需求？

快取資料夾的大小會決定您要備份的資料量。

* 快取資料夾磁片區的可用空間應該等於備份資料總大小的5-10%。
* 如果磁片區具有少於5% 的可用空間，請增加磁片區大小，或將快取資料夾移至具有足夠空間的磁片區，方法是遵循 [這些步驟](#how-do-i-change-the-cache-location-for-the-mars-agent)。
* 如果您備份 Windows 系統狀態，則在包含快取資料夾的磁片區中，將需要額外 30-35 GB 的可用空間。

### <a name="how-to-check-if-scratch-folder-is-valid-and-accessible"></a>如何檢查暫存檔案夾是否有效且可以存取？

1. 依預設，暫存檔案夾位於 `\Program Files\Microsoft Azure Recovery Services Agent\Scratch`
2. 請確定您的暫存檔案夾位置路徑符合下列登錄機碼專案的值：

    | 登錄路徑 | 登錄金鑰 | 值 |
    | --- | --- | --- |
    | `HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Config` |ScratchLocation |*「新的快取資料夾位置」* |
    | `HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Config\CloudBackupProvider` |ScratchLocation |*「新的快取資料夾位置」* |

### <a name="how-do-i-change-the-cache-location-for-the-mars-agent"></a>如何? 變更 MARS 代理程式的快取位置？

1. 在提高許可權的命令提示字元中執行此命令，以停止備份引擎：

    ```Net stop obengine```
2. 如果您已設定系統狀態備份，請開啟 [磁片管理]，然後使用格式的名稱，將磁片 (s) 卸載 `"CBSSBVol_<ID>"` 。
3. 根據預設，暫存檔案夾位於 `\Program Files\Microsoft Azure Recovery Services Agent\Scratch`
4. 將整個 `\Scratch` 資料夾複製到具有足夠空間的其他磁片磁碟機。 確定已複製但未移動內容。
5. 以新移動的暫存檔案夾路徑更新下列登錄專案。

    | 登錄路徑 | 登錄金鑰 | 值 |
    | --- | --- | --- |
    | `HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Config` |ScratchLocation |*新的暫存檔案夾位置* |
    | `HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Config\CloudBackupProvider` |ScratchLocation |*新的暫存檔案夾位置* |

6. 在提高許可權的命令提示字元中重新開機備份引擎：

    ```command
    Net stop obengine

    Net start obengine
    ```

7. 執行隨選備份。 使用新位置順利完成備份之後，您就可以移除原始的快取資料夾。

### <a name="where-should-the-cache-folder-be-located"></a>快取資料夾應該位於哪裡？

不建議快取資料夾的下列位置：

* 網路共用/卸載式媒體：快取資料夾必須是在需要使用線上備份進行備份之伺服器的本機。 不支援網路位置或卸載式媒體（例如 USB 磁片磁碟機）。
* 離線磁片區：快取資料夾必須在線上，才能使用 Azure 備份代理程式進行預期的備份

### <a name="are-there-any-attributes-of-the-cache-folder-that-arent-supported"></a>快取資料夾是否有任何不支援的屬性？

快取資料夾不支援下列屬性或其組合：

* 已加密
* 已刪除重複資料
* Compressed
* 疏鬆
* 重新分析點

快取資料夾和中繼資料 VHD 沒有 Azure 備份代理程式的必要屬性。

### <a name="is-there-a-way-to-adjust-the-amount-of-bandwidth-used-for-backup"></a>是否有方法可以調整用於備份的頻寬量？

是，您可以使用 MARS 代理程式中的 [ **變更屬性** ] 選項來調整頻寬和時間。 [深入了解](backup-windows-with-mars-agent.md#enable-network-throttling)。

## <a name="restore"></a>還原

### <a name="manage"></a>管理

#### <a name="can-i-recover-if-i-forgot-my-passphrase"></a>如果我忘記複雜密碼，是否可以復原？

Azure 備份代理程式需要您在註冊期間提供的複雜密碼 () 在還原期間解密備份的資料。 請參閱下列案例，以瞭解處理遺失複雜密碼的選項：

| 原始電腦 <br> * 取得備份的 (來源電腦) * | 複雜密碼 | 可用的選項 |
| --- | --- | --- |
| 可用 |未能拿下 |如果您的原始機器 (取得備份的位置) 可供使用，而且仍在相同的復原服務保存庫中註冊，則您可以依照下列 [步驟](./backup-azure-manage-mars.md#re-generate-passphrase)重新產生複雜密碼。  |
| 未能拿下 |未能拿下 |無法復原資料或資料無法使用 |

請考慮下列情況：

* 如果您在相同的原始電腦上卸載代理程式並重新註冊，請使用
  * *相同*的複雜密碼，您可以還原已備份的資料。
  * *不同*的複雜密碼，則無法還原已備份的資料。
* 如果您在 *不同的電腦* 上安裝代理程式，
  * 原始電腦)  (使用相同的複雜*密碼*，因此您可以還原已備份的資料。
  * *不同*的複雜密碼，您無法還原已備份的資料。
* 如果您的原始電腦已損毀 (使您無法透過 MARS 主控台) 重新產生複雜密碼，但您可以還原或存取 MARS 代理程式所使用的原始暫存資料夾，則您可能會在忘記密碼) 時還原 (。 如需更多協助，請聯絡客戶支援。

#### <a name="how-do-i-recover-if-i-lost-my-original-machine-where-backups-were-taken"></a>如果我遺失原來的電腦 (的) 執行備份如何? 復原？

如果您有在) 原始電腦的註冊期間提供的相同複雜密碼 (，則可以將備份的資料還原至其他電腦。 請參閱下列案例來瞭解您的還原選項。

| 原始電腦 | 複雜密碼 | 可用的選項 |
| --- | --- | --- |
| 未能拿下 |可用 |您可以在另一部電腦上安裝 MARS 代理程式，並在您于原始電腦的註冊期間提供的相同複雜密碼進行註冊。 選擇 [復原]**選項**  >  **Another location** ，以執行還原。 如需詳細資訊，請參閱這篇[文章](./backup-azure-restore-windows-server.md#use-instant-restore-to-restore-data-to-an-alternate-machine)。
| 未能拿下 |未能拿下 |無法復原資料或資料無法使用 |

### <a name="my-backup-jobs-have-been-failing-or-not-running-for-a-long-time-im-past-the-retention-period-can-i-still-restore"></a>我的備份作業已失敗或未執行一段長時間。 我已超過保留期限。 我仍然可以還原嗎？

作為安全措施，Azure 備份會保留最新的復原點，即使超過保留期限也是如此。 一旦備份復原點變成可用，就會根據指定的保留來移除舊的復原點。

### <a name="what-happens-if-i-cancel-an-ongoing-restore-job"></a>如果我取消進行中的還原作業，會發生什麼事？

如果正在進行還原作業，還原程式就會停止。 在取消前還原的所有檔案都會保留在設定的目的地 (原始或替代位置) ，而不會進行任何回復。

### <a name="does-the-mars-agent-back-up-and-restore-acls-set-on-files-folders-and-volumes"></a>MARS 代理程式是否會在檔案、資料夾和磁片區上進行備份和還原 Acl 設定？

* MARS 代理程式會備份檔案、資料夾和磁片區上設定的 Acl
* 若為磁片區還原復原選項，MARS 代理程式會提供選項，以略過還原要復原的檔案或資料夾的 ACL 許可權
* 針對個別檔案和資料夾的復原選項，MARS 代理程式會以 ACL 許可權還原 (沒有略過 ACL 還原) 的選項。

## <a name="next-steps"></a>接下來的步驟

[瞭解](tutorial-backup-windows-server-to-azure.md) 如何備份 Windows 電腦。
