---
title: Azure 檔案共用備份的移難排解
description: 本文說明如何排解您在保護 Azure 檔案共用時所發生的問題。
ms.date: 02/10/2020
ms.topic: troubleshooting
ms.openlocfilehash: a9b3514b4c1a00cc2f9bb1e1922975bf0bb70d24
ms.sourcegitcommit: 856db17a4209927812bcbf30a66b14ee7c1ac777
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/29/2020
ms.locfileid: "82562078"
---
# <a name="troubleshoot-problems-while-backing-up-azure-file-shares"></a>針對備份 Azure 檔案共用時的問題進行疑難排解

本文提供的疑難排解資訊可解決您在使用 Azure 備份服務設定備份或還原 Azure 檔案共用時所遇到的任何問題。

## <a name="common-configuration-issues"></a>一般設定問題

### <a name="could-not-find-my-storage-account-to-configure-backup-for-the-azure-file-share"></a>找不到我的儲存體帳戶來設定 Azure 檔案共用的備份

- 請等候探索完成。
- 檢查儲存體帳戶下的任何檔案共用是否已使用另一個復原服務保存庫保護。

  >[!NOTE]
  >一個儲存體帳戶中的所有檔案共用只能在一個復原服務保存庫之下加以保護。 您可以使用[此腳本](scripts/backup-powershell-script-find-recovery-services-vault.md)來尋找您的儲存體帳戶註冊所在的復原服務保存庫。

- 請確定檔案共用不存在於任何不受支援的儲存體帳戶中。 您可以參閱 Azure 檔案[共用備份的支援矩陣](azure-file-share-support-matrix.md)，以尋找支援的儲存體帳戶。
- 檢查儲存體帳戶的防火牆設定，確定已啟用 [允許信任的 Microsoft 服務存取儲存體帳戶] 選項。

### <a name="error-in-portal-states-discovery-of-storage-accounts-failed"></a>入口網站中的錯誤指出儲存體帳戶探索失敗

如果您有合作夥伴訂用帳戶（已啟用 CSP），請忽略此錯誤。 如果您的訂用帳戶未啟用 CSP，而且無法探索您的儲存體帳戶，請聯絡支援人員。

### <a name="selected-storage-account-validation-or-registration-failed"></a>選取的儲存體帳戶驗證或註冊失敗

請重試註冊。 若問題持續發生，請連絡支援服務。

### <a name="could-not-list-or-find-file-shares-in-the-selected-storage-account"></a>無法在選取的儲存體帳戶中列出或尋找檔案共用

- 請確定儲存體帳戶存在於資源群組中，而且在保存庫中上次驗證或註冊之後，尚未刪除或移動。
- 請確定您想要保護的檔案共用尚未遭到刪除。
- 請確定儲存體帳戶是檔案共用備份支援的儲存體帳戶。 您可以參閱 Azure 檔案[共用備份的支援矩陣](azure-file-share-support-matrix.md)，以尋找支援的儲存體帳戶。
- 檢查檔案共用是否已在相同的復原服務保存庫中受到保護。

### <a name="backup-file-share-configuration-or-the-protection-policy-configuration-is-failing"></a>備份檔案共用設定（或保護原則設定）失敗

- 請重試設定，以查看問題是否持續發生。
- 請確定您想要保護的檔案共用尚未遭到刪除。
- 如果您嘗試同時保護多個檔案共用，且有些檔案共用失敗，請嘗試重新設定失敗檔案共用的備份。

### <a name="unable-to-delete-the-recovery-services-vault-after-unprotecting-a-file-share"></a>解除保護檔案共用後無法刪除復原服務保存庫

在 Azure 入口網站中，開啟您的保存**庫** > **備份基礎結構** > **儲存體帳戶**，然後按一下 [**取消註冊**]，從復原服務保存庫中移除儲存體帳戶。

>[!NOTE]
>復原服務保存庫只有在取消註冊所有已登錄至保存庫的儲存體帳戶之後，才能予以刪除。

## <a name="common-backup-or-restore-errors"></a>一般備份或還原錯誤

### <a name="filesharenotfound--operation-failed-as-the-file-share-is-not-found"></a>FileShareNotFound-操作失敗，因為找不到檔案共用

錯誤碼： FileShareNotFound

錯誤訊息：因為找不到檔案共用，所以操作失敗

請確定您嘗試保護的檔案共用尚未遭到刪除。

### <a name="usererrorfileshareendpointunreachable--storage-account-not-found-or-not-supported"></a>UserErrorFileShareEndpointUnreachable-找不到或不支援儲存體帳戶

錯誤碼： UserErrorFileShareEndpointUnreachable

錯誤訊息：找不到或不支援儲存體帳戶

- 請確定儲存體帳戶存在於資源群組中，且未在上次驗證之後從資源群組中刪除或移除。

- 請確定儲存體帳戶是檔案共用備份支援的儲存體帳戶。

### <a name="afsmaxsnapshotreached--you-have-reached-the-max-limit-of-snapshots-for-this-file-share-you-will-be-able-to-take-more-once-the-older-ones-expire"></a>AFSMaxSnapshotReached-您已達到此檔案共用快照集的最大限制;當舊的版本過期之後，您將能夠再進行一次

錯誤碼： AFSMaxSnapshotReached

錯誤訊息：您已達到此檔案共用快照集的最大限制;當舊的版本過期之後，您將能夠執行更多工作。

- 當您為檔案共用建立多個隨選備份時，就會發生此錯誤。
- 每個檔案共用有200個快照集的限制，包括 Azure 備份所使用的快照集。 系統會自動清除較舊的排定備份 (或快照集)。 如果已到達限制上限，則必須刪除隨選備份 (或快照集)。

從 Azure 檔案服務入口網站中刪除隨選備份 (Azure 檔案共用快照集)。

>[!NOTE]
> 如果您刪除 Azure 備份所建立的快照集，則會遺失復原點。

### <a name="usererrorstorageaccountnotfound--operation-failed-as-the-specified-storage-account-does-not-exist-anymore"></a>UserErrorStorageAccountNotFound-作業失敗，因為指定的儲存體帳戶已不存在

錯誤碼： UserErrorStorageAccountNotFound

錯誤訊息：因為指定的儲存體帳戶已不存在，所以操作失敗。

請確定儲存體帳戶仍然存在且未刪除。

### <a name="usererrordtsstorageaccountnotfound--the-storage-account-details-provided-are-incorrect"></a>UserErrorDTSStorageAccountNotFound-提供的儲存體帳戶詳細資料不正確

錯誤碼： UserErrorDTSStorageAccountNotFound

錯誤訊息：提供的儲存體帳戶詳細資料不正確。

請確定儲存體帳戶仍然存在且未刪除。

### <a name="usererrorresourcegroupnotfound--resource-group-doesnt-exist"></a>UserErrorResourceGroupNotFound-資源群組不存在

錯誤碼： UserErrorResourceGroupNotFound

錯誤訊息：資源群組不存在

選取現有的資源群組或建立新的資源群組。

### <a name="parallelsnapshotrequest--a-backup-job-is-already-in-progress-for-this-file-share"></a>ParallelSnapshotRequest-此檔案共用的備份作業已在進行中

錯誤碼： ParallelSnapshotRequest

錯誤訊息：此檔案共用的備份作業已在進行中。

- 檔案共用備份不支援針對相同檔案共用的平行快照集要求。

- 請等候現有的備份作業完成，然後再試一次。 如果您在復原服務保存庫中找不到備份作業，請檢查相同訂用帳戶中的其他復原服務保存庫。

### <a name="filesharebackupfailedwithazurerprequestthrottling-filesharerestorefailedwithazurerprequestthrottling--file-share-backup-or-restore-failed-due-to-storage-service-throttling-this-may-be-because-the-storage-service-is-busy-processing-other-requests-for-the-given-storage-account"></a>FileshareBackupFailedWithAzureRpRequestThrottling/FileshareRestoreFailedWithAzureRpRequestThrottling-檔案共用備份或還原因儲存體服務節流而失敗。 這可能是因為儲存體服務正忙於處理指定儲存體帳戶的其他要求

錯誤碼： FileshareBackupFailedWithAzureRpRequestThrottling/FileshareRestoreFailedWithAzureRpRequestThrottling

錯誤訊息：因為儲存體服務節流，所以檔案共用備份或還原失敗。 這可能是因為儲存體服務正忙於處理指定儲存體帳戶的其他要求。

請稍後再試一次備份/還原作業。

### <a name="targetfilesharenotfound--target-file-share-not-found"></a>TargetFileShareNotFound-找不到目標檔案共用

錯誤碼： TargetFileShareNotFound

錯誤訊息：找不到目標檔案共用。

- 請確認選取的儲存體帳戶存在，且未刪除目標檔案共用。

- 請確定儲存體帳戶是檔案共用備份支援的儲存體帳戶。

### <a name="usererrorstorageaccountislocked--backup-or-restore-jobs-failed-due-to-storage-account-being-in-locked-state"></a>UserErrorStorageAccountIsLocked-備份或還原作業失敗，因為儲存體帳戶處於鎖定狀態

錯誤碼： UserErrorStorageAccountIsLocked

錯誤訊息：備份或還原作業失敗，因為儲存體帳戶處於鎖定狀態。

請移除儲存體帳戶上的鎖定，或使用**刪除鎖定**而不是**讀取鎖定**，然後重試備份或還原作業。

### <a name="datatransferservicecoflimitreached--recovery-failed-because-number-of-failed-files-are-more-than-the-threshold"></a>DataTransferServiceCoFLimitReached 復原失敗，因為失敗的檔案數目超過閾值

錯誤碼： DataTransferServiceCoFLimitReached

錯誤訊息：復原失敗，因為失敗的檔案數目超過閾值。

- 復原失敗原因會列在檔案中（作業詳細資料中提供的路徑）。 請解決失敗的問題，然後再重試失敗檔案的還原作業。

- 檔案還原失敗的常見原因：

  - 失敗的檔案目前正在使用中
  - 與失敗檔案同名的目錄存在於上層目錄中。

### <a name="datatransferserviceallfilesfailedtorecover--recovery-failed-as-no-file-could-be-recovered"></a>DataTransferServiceAllFilesFailedToRecover-復原失敗，因為沒有檔案可以復原

錯誤碼： DataTransferServiceAllFilesFailedToRecover

錯誤訊息：復原失敗，因為沒有可用的檔案。

- 復原失敗原因會列在檔案中（作業詳細資料中提供的路徑）。 請解決失敗狀況，然後只針對失敗的檔案重試還原作業。

- 檔案還原失敗的常見原因：

  - 失敗的檔案目前正在使用中
  - 與失敗檔案同名的目錄存在於上層目錄中。

### <a name="usererrordtssourceurinotvalid---restore-fails-because-one-of-the-files-in-the-source-does-not-exist"></a>UserErrorDTSSourceUriNotValid-還原失敗，因為來源中的其中一個檔案不存在

錯誤碼： DataTransferServiceSourceUriNotValid

錯誤訊息：還原失敗，因為來源中的其中一個檔案不存在。

- 選取的項目不存在於復原點資料中。 若要復原檔案，請提供正確的檔案清單。
- 已手動刪除復原點對應的檔案共用快照集。 選取不同的復原點，然後重試還原作業。

### <a name="usererrordtsdestlocked--a-recovery-job-is-in-process-to-the-same-destination"></a>UserErrorDTSDestLocked-相同目的地的復原工作正在處理中

錯誤碼： UserErrorDTSDestLocked

錯誤訊息：復原工作正在處理至相同的目的地。

- 檔案共用備份不支援平行復原至相同的目標檔案共用。

- 等待現有的復原完成，然後再試一次。 如果您在復原服務保存庫中找不到復原工作，請檢查相同訂用帳戶中的其他復原服務保存庫。

### <a name="usererrortargetfilesharefull--restore-operation-failed-as-target-file-share-is-full"></a>UserErrorTargetFileShareFull-還原作業失敗，因為目標檔案共用已滿

錯誤碼： UserErrorTargetFileShareFull

錯誤訊息：因為目標檔案共用已滿，所以還原作業失敗。

增加目標檔案共用大小配額以容納還原資料，然後重試還原作業。

### <a name="usererrortargetfilesharequotanotsufficient--target-file-share-does-not-have-sufficient-storage-size-quota-for-restore"></a>UserErrorTargetFileShareQuotaNotSufficient-目標檔案共用沒有足夠的儲存體大小配額可進行還原

錯誤碼： UserErrorTargetFileShareQuotaNotSufficient

錯誤訊息：目標檔案共用沒有足夠的儲存體大小配額可進行還原

請增加目標檔案共用大小配額以容納還原資料，然後重試此作業

### <a name="file-sync-prerestorefailed--restore-operation-failed-as-an-error-occurred-while-performing-pre-restore-operations-on-file-sync-service-resources-associated-with-the-target-file-share"></a>檔案同步 PreRestoreFailed-還原作業失敗，因為在與目標檔案共用相關聯的檔案同步服務資源上執行還原前操作時發生錯誤

錯誤碼：檔案同步 PreRestoreFailed

錯誤訊息：由於在與目標檔案共用相關聯的檔案同步服務資源上執行還原作業時發生錯誤，還原操作失敗。

請稍後再嘗試還原資料。 如果問題持續發生， 請連絡 Microsoft 支援服務。

### <a name="azurefilesyncchangedetectioninprogress--azure-file-sync-service-change-detection-is-in-progress-for-the-target-file-share-the-change-detection-was-triggered-by-a-previous-restore-to-the-target-file-share"></a>AzureFileSyncChangeDetectionInProgress-正在為目標檔案共用進行 Azure 檔案同步服務變更偵測。 先前的還原已觸發變更偵測到目標檔案共用

錯誤碼： AzureFileSyncChangeDetectionInProgress

錯誤訊息：目標檔案共用的 Azure 檔案同步服務變更偵測正在進行中。 先前的還原已觸發變更偵測到目標檔案共用。

使用不同的目標檔案共用。 或者，您可以等候目標檔案共用的 Azure 檔案同步服務變更偵測完成，再重試還原。

### <a name="usererrorafsrecoverysomefilesnotrestored--one-or-more-files-could-not-be-recovered-successfully-for-more-information-check-the-failed-file-list-in-the-path-given-above"></a>UserErrorAFSRecoverySomeFilesNotRestored-無法成功復原一或多個檔案。 如需詳細資訊，請查看上述路徑中的失敗檔案清單

錯誤碼： UserErrorAFSRecoverySomeFilesNotRestored

錯誤訊息：無法成功復原一或多個檔案。 如需詳細資訊，請檢查上述路徑中的失敗檔案清單。

- 復原失敗原因會列在檔案中（作業詳細資料中提供的路徑）。 請解決原因，然後重試失敗檔案的還原作業。
- 檔案還原失敗的常見原因：

  - 失敗的檔案目前正在使用中
  - 與失敗檔案同名的目錄存在於上層目錄中。

### <a name="usererrorafssourcesnapshotnotfound--azure-file-share-snapshot-corresponding-to-recovery-point-cannot-be-found"></a>UserErrorAFSSourceSnapshotNotFound-找不到對應至復原點的 Azure 檔案共用快照集

錯誤碼： UserErrorAFSSourceSnapshotNotFound

錯誤訊息：找不到對應至復原點的 Azure 檔案共用快照集

- 請確定檔案共用快照集對應至您嘗試復原時所使用的復原點，但仍存在。

  >[!NOTE]
  >如果您刪除 Azure 備份所建立的檔案共用快照集，對應的復原點就會變成無法使用。 我們建議您不要刪除快照集，以確保能進行復原。

- 請嘗試選取另一個還原點來復原您的資料。

### <a name="usererroranotherrestoreinprogressonsametarget--another-restore-job-is-in-progress-on-the-same-target-file-share"></a>UserErrorAnotherRestoreInProgressOnSameTarget-相同的目標檔案共用正在進行另一個還原作業

錯誤碼： UserErrorAnotherRestoreInProgressOnSameTarget

錯誤訊息：另一個還原作業正在相同的目標檔案共用上進行

使用不同的目標檔案共用。 或者，您可以取消或等候其他還原完成。

## <a name="common-modify-policy-errors"></a>常見的修改原則錯誤

### <a name="bmsusererrorconflictingprotectionoperation--another-configure-protection-operation-is-in-progress-for-this-item"></a>BMSUserErrorConflictingProtectionOperation-此專案正在進行另一個設定保護作業

錯誤碼： BMSUserErrorConflictingProtectionOperation

錯誤訊息：此專案有另一個設定保護作業正在進行中。

等候先前的修改原則作業完成，然後稍後再試一次。

### <a name="bmsusererrorobjectlocked--another-operation-is-in-progress-on-the-selected-item"></a>BMSUserErrorObjectLocked-另一個作業正在選取的專案上進行

錯誤碼： BMSUserErrorObjectLocked

錯誤訊息：另一個作業正在選取的專案上進行。

等候另一個進行中的作業完成，然後稍後再試一次。

## <a name="next-steps"></a>後續步驟

如需備份 Azure 檔案共用的更多資訊，請參閱︰

- [備份 Azure 檔案共用](backup-afs.md)
- [備份 Azure 檔案共用常見問題集](backup-azure-files-faq.md)
