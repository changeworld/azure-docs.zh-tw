---
title: 備份 Azure 檔案服務常見問題集
description: 在本文中，您可以針對如何使用 Azure 備份服務來保護 Azure 檔案共用的常見問題，找到相關解答。
ms.date: 07/29/2019
ms.topic: conceptual
ms.openlocfilehash: c69d4642aefbd599d3783dcdfa059a0cd9d129d9
ms.sourcegitcommit: f915d8b43a3cefe532062ca7d7dbbf569d2583d8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/05/2020
ms.locfileid: "78302537"
---
# <a name="questions-about-backing-up-azure-files"></a>有關備份 Azure 檔案服務的問題

本文提供有關備份 Azure 檔案服務的常見問題解答。 在某些答案中，有具有完整資訊的文章連結。 您也可以在 [論壇](https://social.msdn.microsoft.com/forums/azure/home?forum=windowsazureonlinebackup)中張貼有關 Azure 備份服務的問題。

若要快速掃描本文中的各節內容，在**本文**下使用右方連結。

## <a name="configuring-the-backup-job-for-azure-files"></a>設定 Azure 檔案服務的備份作業

### <a name="why-cant-i-see-some-of-my-storage-accounts-i-want-to-protect-that-contain-valid-azure-file-shares"></a>為何看不到某些我想要保護且包含有效 Azure 檔案共用的儲存體帳戶？

在預覽期間，Azure 檔案共用的備份不支援所有的儲存體帳戶類型。 請參閱[這裡](troubleshoot-azure-files.md#limitations-for-azure-file-share-backup-during-preview)的清單，以查看支援的儲存體帳戶清單。 您所尋找的儲存體帳戶也可能已經受保護或向另一個保存庫註冊。 從保存庫[取消註冊](troubleshoot-azure-files.md#configuring-backup)，以探索其他保存庫中用於保護的儲存體帳戶。

### <a name="why-cant-i-see-some-of-my-azure-file-shares-in-the-storage-account-when-im-trying-to-configure-backup"></a>為何我在嘗試設定備份時，在儲存體帳戶中看不到某些 Azure 檔案共用？

請確認 Azure 檔案共用是否已在相同的復原服務保存庫中受到保護，或是否已於近期刪除。

### <a name="can-i-protect-file-shares-connected-to-a-sync-group-in-azure-files-sync"></a>我可以保護已連線到 Azure 檔案同步中同步群組的檔案共用嗎？

是。 保護已連線至同步群組的 Azure 檔案共用並處於公開預覽狀態。

### <a name="when-trying-to-back-up-file-shares-i-clicked-on-a-storage-account-for-discovering-the-file-shares-in-it-however-i-did-not-protect-them-how-do-i-protect-these-file-shares-with-any-other-vault"></a>在嘗試備份檔案共用時，我按了一下 [儲存體帳戶] 來探索其中的檔案共用。 不過，我並未保護它們。 如何使用任何其他保存庫來保護這些檔案共用？

嘗試備份，並選取 [儲存體帳戶] 以探索其中的檔案共用時，向保存庫註冊此 [儲存體帳戶]。 如果您選擇使用不同的保存庫來保護檔案共用，從這個保存庫[取消註冊](troubleshoot-azure-files.md#configuring-backup)所選的儲存體帳戶。

### <a name="can-i-change-the-vault-to-which-i-back-up-my-file-shares"></a>可以將保存庫變更為我備份檔案共用的保存庫嗎？

是。 不過，您必須從已連線的保存庫[停止保護檔案共用](manage-afs-backup.md#stop-protection-on-a-file-share)、[取消註冊](troubleshoot-azure-files.md#configuring-backup)此儲存體帳戶，然後從不同的保存庫加以保護。

### <a name="in-which-geos-can-i-back-up-azure-file-shares"></a>我可以在哪些地區備份 Azure 檔案共用？

Azure 檔案共用的備份功能目前處於預覽狀態，僅適用於下列地區：

- 澳大利亞東部 (AE)
- 澳大利亞東南部 (ASE)
- 巴西南部 (BRS)
- 加拿大中部 (CNC)
- 加拿大東部 (CE)
- 美國中部 (CUS)
- 東亞 (EA)
- 美國東部 (EUS)
- 美國東部 2 (EUS2)
- 日本東部 (JPE)
- 日本西部 (JPW)
- 印度中部 (INC)
- 印度南部 (INS)
- 南韓中部 (KRC)
- 南韓 (KRS)
- 美國中北部 (NCUS)
- 北歐 (NE)
- 美國中南部 (SCUS)
- 東南亞 (SEA)
- 英國南部 (UKS)
- 英國西部 (UKW)
- 西歐 (WE)
- 美國西部 (WUS)
- 美國中西部 (WCUS)
- 美國西部 2 (WUS 2)
- US Gov 亞利桑那州 (UGA)
- US Gov 德克薩斯州 (UGT)
- US Gov 維吉尼亞州 (UGV)
- 澳大利亞中部（ACL）
- 印度西部（INW）
- 南非北部（SAN）
- 阿拉伯聯合大公國北部（UAN）
- 法國中部（FRC）
- 德國北部（GN）                       
- 德國中西部（GWC）
- 南非西部（已看到）
- 阿拉伯聯合大公國中部（UAC）
- N 我們（挪威東部）     
- NWW （挪威西部）
- SZN （瑞士北部）

如果您在未列於上方的特定地區中需要使用該功能，請寫信送至 [AskAzureBackupTeam@microsoft.com](mailto:askazurebackupteam@microsoft.com)。

### <a name="how-many-azure-file-shares-can-i-protect-in-a-vault"></a>我可以在一個保存庫中保護多少個 Azure 檔案共用？

在預覽期間，您在每個保存庫中最多可以保護 50 個儲存體帳戶的 Azure 檔案共用。 此外，您在單一保存庫中最多可保護 200 個檔案共用。

### <a name="can-i-protect-two-different-file-shares-from-the-same-storage-account-to-different-vaults"></a>可以將相同儲存體帳戶中的兩個不同檔案共用放入不同的保存庫中保護？

否。 一個儲存體帳戶中的所有檔案共用只能由相同的保存庫保護。

## <a name="backup"></a>Backup

### <a name="how-many-scheduled-backups-can-i-configure-per-file-share"></a>每個檔案共用可以設定多少個排程備份？

Azure 備份目前支援對 Azure 檔案共用設定每日一次的排程備份。

### <a name="how-many-on-demand-backups-can-i-take-per-file-share"></a>每個檔案共用可以進行多少個隨選備份？

在任何時間點，檔案共用最多可以有 200 個快照集。 如您的原則所定義，此限制包含 Azure 備份所產生的快照集。 如果您的備份在達到此限制之後開始失敗，請刪除隨選還原點，以便未來備份成功。

## <a name="restore"></a>{1}還原{2}

### <a name="can-i-recover-from-a-deleted-azure-file-share"></a>已刪除的 Azure 檔案共用是否可以復原？

刪除 Azure 檔案共用時，系統會顯示將一併刪除的備份清單並要求您確認。 已刪除的 Azure 檔案共用無法還原。

### <a name="can-i-restore-from-backups-if-i-stopped-protection-on-an-azure-file-share"></a>如果我停止保護 Azure 檔案共用，是否可以從備份還原？

是。 如果您在停止保護時選擇 [保留備份資料]，則您可以從所有現有的還原點進行還原。

### <a name="what-happens-if-i-cancel-an-ongoing-restore-job"></a>如果我取消進行中的還原作業，會發生什麼事？

如果取消進行中的還原作業，還原程序會停止，而所有在取消前還原的檔案會留在所設定的目的地 (原始或替代位置)，不需要回復。

## <a name="manage-backup"></a>管理備份

### <a name="can-i-use-powershell-to-configuremanagerestore-backups-of-azure-file-shares"></a>我是否可以使用 PowerShell 來設定/管理/還原 Azure 檔案共用的備份？

是。 請參閱[這裡](backup-azure-afs-automation.md)的詳細文件

### <a name="can-i-access-the-snapshots-taken-by-azure-backups-and-mount-it"></a>我可以存取 Azure 備份所產生的快照集並加以裝載嗎？

在入口網站、PowerShell 或 CLI 中檢視快照集，即可存取 Azure Backup 所產生的所有快照集。 若要深入了解 Azure 檔案共用快照集，請參閱 [Azure 檔案的共用快照集 (預覽) 概觀](../storage/files/storage-snapshots-files.md)。

### <a name="what-is-the-maximum-retention-i-can-configure-for-backups"></a>我可以為備份設定的保留期上限為何？

Azure 檔案共用的備份可讓您設定保留期最高達 180 天的原則。 不過，若使用 [PowerShell 中的「隨選備份」選項](backup-azure-afs-automation.md#trigger-an-on-demand-backup)，您的復原點甚至可保留長達 10 年。

### <a name="what-happens-when-i-change-the-backup-policy-for-an-azure-file-share"></a>變更 Azure 檔案共用的備份原則時，會發生什麼狀況？

在檔案共用上套用新原則後，就會遵循新原則的排程和保留期。 如果延長保留期，會標示現有的復原點，以依據新的原則加以保留。 如果縮短保留期，則會標示現有的復原點，以便在下次清除作業中剪除並刪除。

## <a name="next-steps"></a>後續步驟

若要深入了解 Azure 備份的其他領域，請參閱其他備份常見問題集：

- [復原服務保存庫常見問題集](backup-azure-backup-faq.md)
- [Azure VM 備份常見問題集](backup-azure-vm-backup-faq.md)
- [Azure 備份代理程式常見問題集](backup-azure-file-folder-backup-faq.md)
