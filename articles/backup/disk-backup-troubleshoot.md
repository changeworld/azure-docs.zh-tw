---
title: 針對 Azure 磁片備份中的備份失敗進行疑難排解
description: 瞭解如何針對 Azure 磁片備份中的備份失敗進行疑難排解
ms.topic: conceptual
ms.date: 01/07/2021
ms.openlocfilehash: 0a2ef1ea20ee8d6b7a3f32e244d3e00f3add80a2
ms.sourcegitcommit: 6628bce68a5a99f451417a115be4b21d49878bb2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/18/2021
ms.locfileid: "98557693"
---
# <a name="troubleshooting-backup-failures-in-azure-disk-backup-in-preview"></a>針對 Azure 磁片備份中的備份失敗進行疑難排解 (預覽版) 

>[!IMPORTANT]
>Azure 磁片備份處於預覽狀態，不含服務等級協定，因此不建議用於生產工作負載。 如需詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。 如需區域可用性，請參閱 [支援矩陣](disk-backup-support-matrix.md)。
>
>[填寫此表單](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR1vE8L51DIpDmziRt_893LVUNFlEWFJBN09PTDhEMjVHS05UWFkxUlUzUS4u) 以註冊預覽。

本文提供有關 Azure 磁片所面臨的備份和還原問題的疑難排解資訊。 如需 [Azure 磁片備份](disk-backup-overview.md) 區域可用性、支援的案例和限制的詳細資訊，請參閱 [支援矩陣](disk-backup-support-matrix.md)。

## <a name="common-issues-faced-with-azure-disk-backup"></a>Azure 磁片備份面臨的常見問題

### <a name="error-code-usererrorsnapshotrgsubscriptionmismatch"></a>錯誤碼： UserErrorSnapshotRGSubscriptionMismatch

錯誤訊息：為快照集資料存放區選取的訂用帳戶無效

建議的動作：磁片和磁片快照集會儲存在相同的訂用帳戶中。 您可以選擇任何資源群組，以將磁片快照集儲存在訂用帳戶內。 選取與來源磁片相同的訂用帳戶。 如需詳細資訊，請參閱 [支援矩陣](disk-backup-support-matrix.md)。

### <a name="error-code-usererrorsnapshotrgnotfound"></a>錯誤碼： UserErrorSnapshotRGNotFound

錯誤訊息：無法執行操作，因為快照集資料存放區資源群組不存在。

建議動作：建立資源群組，並在其上提供所需的許可權。 如需詳細資訊，請參閱 [設定備份](backup-managed-disks.md#configure-backup)。

### <a name="error-code-usererrormanageddisknotfound"></a>錯誤碼： UserErrorManagedDiskNotFound

錯誤訊息：無法執行操作，因為受控磁片已不存在。

建議動作：由於來源磁片可能已刪除或移至不同的位置，因此備份將會繼續失敗。 如果不小心刪除磁片，請使用現有的還原點來復原磁碟。 如果磁片移至不同的位置，請設定磁片的備份。

### <a name="error-code-usererrornotenoughpermissionondisk"></a>錯誤碼： UserErrorNotEnoughPermissionOnDisk

錯誤訊息： Azure 備份服務需要磁片上的額外許可權，才能執行此操作。

建議動作：在磁片上將適當的許可權授與備份保存庫的受控識別。 請參閱 [檔](backup-managed-disks.md) 以瞭解備份保存庫受控識別所需的許可權，以及如何提供。

### <a name="error-code-usererrornotenoughpermissiononsnapshotrg"></a>錯誤碼： UserErrorNotEnoughPermissionOnSnapshotRG

錯誤訊息： Azure 備份服務需要快照集資料存放區資源群組的額外許可權，才能執行此作業。

建議動作：將快照集資料存放區資源群組的適當許可權授與備份保存庫的受控識別。 快照集資料存放區資源群組是磁片快照集的儲存位置。 請參閱 [檔](backup-managed-disks.md) 以瞭解何者為資源群組、備份保存庫受控身分識別所需的許可權，以及如何提供。

### <a name="error-code-usererrordiskbackupdiskormsipermissionsnotpresent"></a>錯誤碼： UserErrorDiskBackupDiskOrMSIPermissionsNotPresent

錯誤訊息：不正確磁片或 Azure 備份服務需要磁片上的額外許可權，才能執行此操作

建議動作：由於來源磁片可能已刪除或移至不同的位置，因此備份將會繼續失敗。 如果不小心刪除磁片，請使用現有的還原點來復原磁碟。 如果磁片移至不同的位置，請設定磁片的備份。 如果未刪除或移動磁片，請將磁片上的適當許可權授與備份保存庫的受控識別。 請參閱 [檔](backup-managed-disks.md) 以瞭解備份保存庫的受控識別需要哪些許可權，以及如何提供。

### <a name="error-code-usererrordiskbackupsnapshotrgormsipermissionsnotpresent"></a>錯誤碼： UserErrorDiskBackupSnapshotRGOrMSIPermissionsNotPresent

錯誤訊息：無法執行操作，因為快照集資料存放區資源群組已不存在。 或 Azure 備份服務需要快照集資料存放區資源群組的額外許可權，才能進行這種操作。

建議動作：建立資源群組，並將快照集資料存放區資源群組的適當許可權授與備份保存庫的受控識別。 快照集資料存放區資源群組是磁片快照集的儲存位置。 請參閱 [檔](backup-managed-disks.md) 以瞭解何謂資源群組、備份保存庫的受控識別需要哪些許可權，以及如何提供。

### <a name="error-code-usererrordiskbackupauthorizationfailed"></a>錯誤碼： UserErrorDiskBackupAuthorizationFailed

錯誤訊息：備份保存庫受控識別缺少執行這項操作所需的許可權。

建議動作：在儲存快照集的快照集資料存放區資源群組上，將要備份之磁片上的適當許可權授與備份保存庫的受控識別。 請參閱 [檔](backup-managed-disks.md) 以瞭解備份保存庫的受控識別需要哪些許可權，以及如何提供。

### <a name="error-code-usererrorsnapshotrgormsipermissionsnotpresent"></a>錯誤碼： UserErrorSnapshotRGOrMSIPermissionsNotPresent

錯誤訊息：無法執行操作，因為快照集資料存放區資源群組已不存在。 或者，Azure 備份服務需要快照集資料存放區資源群組的額外許可權，才能進行此作業。

建議動作：建立資源群組，並將快照集資料存放區資源群組的適當許可權授與備份保存庫的受控識別。 快照集資料存放區資源群組是儲存快照集的位置。 請參閱 [檔](backup-managed-disks.md) 以瞭解何謂資源群組、備份保存庫的受控識別需要哪些許可權，以及如何提供。

### <a name="error-code-usererroroperationalstoreparametersnotprovided"></a>錯誤碼： UserErrorOperationalStoreParametersNotProvided

錯誤訊息：無法執行操作，因為未提供快照集資料存放區的資源群組參數

建議的動作：提供快照集資料存放區資源群組參數。 快照集資料存放區資源群組是磁片快照集的儲存位置。 如需詳細資訊，請參閱 [檔](backup-managed-disks.md)。

### <a name="error-code-usererrorinvalidoperationalstoreresourcegroup"></a>錯誤碼： UserErrorInvalidOperationalStoreResourceGroup

錯誤訊息：提供的快照集資料存放區資源群組無效

建議的動作：提供快照集資料存放區的有效資源群組。 快照集資料存放區資源群組是磁片快照集的儲存位置。 如需詳細資訊，請參閱 [檔](backup-managed-disks.md)。

### <a name="error-code-usererrordiskbackupdisktypenotsupported"></a>錯誤碼： UserErrorDiskBackupDiskTypeNotSupported

錯誤訊息：不支援的磁片類型

建議動作：請參閱 [支援矩陣](disk-backup-support-matrix.md) 以支援不支援的案例和限制。

### <a name="error-code-usererrorsamenamediskalreadyexists"></a>錯誤碼： UserErrorSameNameDiskAlreadyExists

錯誤訊息：無法還原，因為選取的目標資源群組中已經有相同名稱的磁片

建議的動作：提供不同的磁片名稱來進行還原。 如需詳細資訊，請參閱 [還原 Azure 受控磁碟](restore-managed-disks.md)。

### <a name="error-code-usererrorrestoretargetrgnotfound"></a>錯誤碼： UserErrorRestoreTargetRGNotFound

錯誤訊息：因為目標資源群組不存在，所以操作失敗。

建議的動作：提供要還原的有效資源群組。 如需詳細資訊，請參閱 [還原 Azure 受控磁碟](restore-managed-disks.md)。

### <a name="error-code-usererrornotenoughpermissionontargetrg"></a>錯誤碼： UserErrorNotEnoughPermissionOnTargetRG

錯誤訊息： Azure 備份服務需要目標資源群組的其他許可權，才能執行此作業。

建議動作：將目標資源群組的適當許可權授與備份保存庫的受控識別。 目標資源群組是要復原磁碟的選取位置。 請參閱 [還原檔](restore-managed-disks.md) ，以瞭解備份保存庫的受控識別需要哪些許可權，以及如何提供。

### <a name="error-code-usererrorsubscriptiondiskquotalimitreached"></a>錯誤碼： UserErrorSubscriptionDiskQuotaLimitReached

錯誤訊息：因為訂用帳戶已達到磁片配額上限，所以作業失敗。

建議動作：如需進一步指引，請參閱 [Azure 訂用帳戶和服務限制和配額檔](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits) 或聯絡 Microsoft 支援服務。

### <a name="error-code-usererrordiskbackuprestorergormsipermissionsnotpresent"></a>錯誤碼： UserErrorDiskBackupRestoreRGOrMSIPermissionsNotPresent

錯誤訊息：因為目標資源群組不存在，所以操作失敗。 或 Azure 備份服務需要目標資源群組上的額外許可權，才能進行這種操作。

建議的動作：提供要還原的有效資源群組，並將目標資源群組上適當的許可權授與備份保存庫的受控識別。 目標資源群組是要復原磁碟的選取位置。 請參閱 [還原檔](restore-managed-disks.md) ，以瞭解備份保存庫的受控識別需要哪些許可權，以及如何提供。

### <a name="error-code-usererrordeskeyvaultkeydisabled"></a>錯誤碼： UserErrorDESKeyVaultKeyDisabled

錯誤訊息：用於磁片加密集的金鑰保存庫金鑰未處於啟用狀態。

建議的動作：啟用用於磁片加密集的金鑰保存庫金鑰。 請參閱 [支援矩陣](disk-backup-support-matrix.md)中的限制。

### <a name="error-code-usererrormsipermissionsnotpresentondes"></a>錯誤碼： UserErrorMSIPermissionsNotPresentOnDES

錯誤訊息： Azure 備份服務需要存取磁片加密集的許可權。

建議動作：將備份保存庫的受控識別存取權提供給 (DES) 的磁片加密集。

### <a name="error-code-usererrordeskeyvaultkeynotavailable"></a>錯誤碼： UserErrorDESKeyVaultKeyNotAvailable

錯誤訊息：無法使用用於磁片加密集的金鑰保存庫金鑰。

建議的動作：確定用於磁片加密集的金鑰保存庫金鑰未停用或刪除。

### <a name="error-code-usererrordisksnapshotnotfound"></a>錯誤碼： UserErrorDiskSnapshotNotFound

錯誤訊息：已刪除此還原點的磁片快照集。

建議動作：快照集會儲存在您訂用帳戶內的快照集資料存放區資源群組中。 可能已從此資源群組刪除或移動與所選還原點相關的快照集。 請考慮使用另一個復原點來還原。 此外，請遵循建議的指導方針來選擇 [還原檔](restore-managed-disks.md)中所述的快照集資源群組。

### <a name="error-code-usererrorsnapshotmetadatanotfound"></a>錯誤碼： UserErrorSnapshotMetadataNotFound

錯誤訊息：此還原點的磁片快照集中繼資料已刪除

建議動作：考慮使用其他復原點進行還原。 如需詳細資訊，請參閱 [還原檔](restore-managed-disks.md)。

### <a name="error-code-usererrormaxconcurrentoperationlimitreached"></a>錯誤碼： UserErrorMaxConcurrentOperationLimitReached

錯誤訊息：無法啟動作業，因為此作業類型已達到允許的並行作業數目上限。

建議動作：等候先前的作業完成。

## <a name="next-steps"></a>後續步驟

- [Azure 磁片備份支援矩陣](disk-backup-support-matrix.md)
