---
title: 管理 Azure 檔案共用備份
description: 本文說明管理和監視 Azure 備份所備份之 Azure 檔案共用的一般工作。
ms.topic: conceptual
ms.date: 01/07/2020
ms.openlocfilehash: cb764fa441c063328dc350cf26f42c5bc7a0ca99
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/13/2020
ms.locfileid: "79247653"
---
# <a name="manage-azure-file-share-backups"></a>管理 Azure 檔案共用備份

本文說明管理和監視[Azure 備份](https://docs.microsoft.com/azure/backup/backup-overview)所備份之 Azure 檔案共用的一般工作。 您將瞭解如何在復原服務保存庫中執行管理工作。

## <a name="monitor-jobs"></a>監視工作

當您觸發備份或還原作業時，備份服務會建立追蹤的工作。 您可以在 [備份作業] 頁面上監視所有作業的進度。

若要開啟 [備份作業] 頁面：

1. 開啟您用來設定檔案共用備份的復原服務保存庫。 在 [**總覽**] 窗格中，選取 [**監視**] 區段下的 [**備份作業**]。

   ![監視區段中的備份作業](./media/manage-afs-backup/backup-jobs.png)

1. 選取 **[確定]** 之後，[**備份作業**] 窗格會列出所有作業的狀態。 選取對應至您要監視之檔案共用的工作負載名稱。

   ![工作負載名稱](./media/manage-afs-backup/workload-name.png)

## <a name="create-a-new-policy"></a>建立新的原則

您可以建立新的原則，以從復原服務保存庫的 [**備份原則**] 區段備份 Azure 檔案共用。 當您為檔案共用設定備份時所建立的所有原則，都會以**原則類型**顯示為**Azure 檔案共用**。

若要查看現有的備份原則：

1. 開啟您用來設定檔案共用備份的復原服務保存庫。 在 [復原服務保存庫] 功能表上，選取 [**管理**] 區段下的 [**備份原則**]。 保存庫中設定的所有備份原則都會出現。

   ![所有備份原則](./media/manage-afs-backup/all-backup-policies.png)

1. 若要查看 Azure 檔案**共用**特有的原則，請從右上方的下拉式清單中選取 [ **Azure 檔案共用**]。

   ![選取 Azure 檔案共用](./media/manage-afs-backup/azure-file-share.png)

若要建立新的備份原則：

1. 在 [**備份原則**] 窗格中，選取 [ **+ 新增**]。

   ![新增備份原則](./media/manage-afs-backup/new-backup-policy.png)

1. 在 [**新增**] 窗格中，選取 [ **Azure 檔案共用**] 作為 [**原則類型**]。 **Azure 檔案共用**的 [**備份原則**] 窗格隨即開啟。 指定復原點的原則名稱、備份頻率和保留範圍。 定義原則之後，請選取 **[確定]** 。

   ![定義備份原則](./media/manage-afs-backup/define-backup-policy.png)

## <a name="modify-policy"></a>修改原則

您可以修改備份原則來變更備份頻率或保留範圍。

若要修改原則：

1. 開啟您用來設定檔案共用備份的復原服務保存庫。 在 [復原服務保存庫] 功能表上，選取 [**管理**] 區段下的 [**備份原則**]。 保存庫中設定的所有備份原則都會出現。

   ![保存庫中的所有備份原則](./media/manage-afs-backup/all-backup-policies-modify.png)

1. 若要查看 Azure 檔案共用特有的原則，請從右上方的下拉式清單中選取 [ **Azure 檔案共用**]。 選取您想要修改的備份原則。

   ![要修改的 Azure 檔案共用](./media/manage-afs-backup/azure-file-share-modify.png)

1. [**排程**] 窗格隨即開啟。 視需要編輯**備份排程**和**保留範圍**，然後選取 [**儲存**]。 您會在窗格中看到「更新進行中」訊息。 原則變更成功更新之後，您會看到「已成功更新備份原則」訊息。

   ![儲存修改過的原則](./media/manage-afs-backup/save-policy.png)

## <a name="stop-protection-on-a-file-share"></a>停止保護檔案共用

有兩種方式可停止保護 Azure 檔案共用︰

* 停止所有未來的備份作業，並*刪除所有復原點*。
* 停止所有未來的備份工作，但*保留復原點*。

在儲存體中保留復原點可能會產生相關成本，因為將會保留 Azure 備份所建立的基礎快照集。 保留復原點的好處是您可以稍後再還原檔案共用。 如需復原點保留成本的相關資訊，請參閱 [定價詳細資料](https://azure.microsoft.com/pricing/details/backup/)。 如果您決定刪除所有復原點，則無法還原檔案共用。

若要停止保護 Azure 檔案共用：

1. 開啟包含檔案共用復原點的復原服務保存庫。 選取 [**受保護的專案**] 區段下的 [**備份專案**]。 備份專案類型的清單隨即出現。

   ![備份項目](./media/manage-afs-backup/backup-items.png)

1. 在 [備份管理類型] 清單中，選取 [Azure 儲存體 (Azure 檔案服務)]。 [**備份專案（Azure 儲存體（Azure 檔案儲存體））** ] 清單隨即出現。

   ![選取 Azure 儲存體（Azure 檔案儲存體）](./media/manage-afs-backup/azure-storage-azure-files.png)

1. 在 [**備份專案（Azure 儲存體（Azure 檔案儲存體））** ] 清單中，選取您要停止保護的備份專案。

1. 選取 [**停止備份**] 選項。

   ![選取 [停止備份]](./media/manage-afs-backup/stop-backup.png)

1. 在 [**停止備份**] 窗格中，選取 [**保留備份資料**] 或 [**刪除備份資料**]。 然後選取 [**停止備份**]。

    ![選取 [保留備份資料] 或 [刪除備份資料]](./media/manage-afs-backup/retain-or-delete-backup-data.png)

## <a name="resume-protection-on-a-file-share"></a>繼續保護檔案共用

如果已在檔案共用的保護停止時選取 [**保留備份資料**] 選項，則可以繼續保護。 如果已選取 [**刪除備份資料**] 選項，則無法繼續保護檔案共用。

若要繼續保護 Azure 檔案共用：

1. 開啟包含檔案共用復原點的復原服務保存庫。 選取 [**受保護的專案**] 區段下的 [**備份專案**]。 備份專案類型的清單隨即出現。

   ![繼續的備份專案](./media/manage-afs-backup/backup-items-resume.png)

1. 在 [備份管理類型] 清單中，選取 [Azure 儲存體 (Azure 檔案服務)]。 [**備份專案（Azure 儲存體（Azure 檔案儲存體））** ] 清單隨即出現。

   ![Azure 儲存體（Azure 檔案儲存體）清單](./media/manage-afs-backup/azure-storage-azure-files.png)

1. 在 [**備份專案（Azure 儲存體（Azure 檔案儲存體））** ] 清單中，選取您要繼續保護的備份專案。

1. 選取 [**繼續備份**] 選項。

   ![選取 [繼續備份]](./media/manage-afs-backup/resume-backup.png)

1. [**備份原則**] 窗格隨即開啟。 選取您選擇的原則來繼續備份。

1. 選取備份原則之後，請選取 [**儲存**]。 您會在入口網站中看到「更新進行中」訊息。 備份成功繼續後，您會看到「已成功更新受保護 Azure 檔案共用的備份原則」訊息。

   ![已成功更新備份原則](./media/manage-afs-backup/successfully-updated.png)

## <a name="delete-backup-data"></a>刪除備份資料

您可以在**停止備份**作業期間，或在停止保護後的任何時間，刪除檔案共用的備份。 在您刪除復原點之前，請先等候幾天或甚至數周的時間。 當您刪除備份資料時，您無法選擇要刪除的特定復原點。 如果您決定刪除備份資料，則會刪除與檔案共用相關聯的所有復原點。

下列程式假設已停止檔案共用的保護。

若要刪除 Azure 檔案共用的備份資料：

1. 在備份工作停止之後，[**備份專案**] 儀表板中會提供 [**繼續備份**] 和 [**刪除備份資料**] 選項。 選取 [**刪除備份資料**] 選項。

   ![刪除備份資料](./media/manage-afs-backup/delete-backup-data.png)

1. [**刪除備份資料**] 窗格隨即開啟。 輸入檔案共用的名稱以確認刪除。 （選擇性）在 [**原因**] 或 [**批註**] 方塊中提供詳細資訊。 確定刪除備份資料之後，請選取 [**刪除**]。

   ![確認刪除資料](./media/manage-afs-backup/confirm-delete-data.png)

## <a name="unregister-a-storage-account"></a>取消註冊儲存體帳戶

若要使用不同的復原服務保存庫來保護特定儲存體帳戶中的檔案共用，請先[停止保護](#stop-protection-on-a-file-share)該儲存體帳戶中的所有檔案共用。 然後，從目前用於保護的復原服務保存庫中取消註冊該帳戶。

下列程式假設您想要取消註冊之儲存體帳戶中的所有檔案共用都已停止保護。

若要取消註冊儲存體帳戶：

1. 開啟您的儲存體帳戶註冊所在的復原服務保存庫。
1. 在 [**總覽**] 窗格中，選取 [**管理**] 區段下的 [**備份基礎結構**] 選項。

   ![選取 [備份基礎結構]](./media/manage-afs-backup/backup-infrastructure.png)

1. [**備份基礎結構**] 窗格隨即開啟。 在 [ **Azure 儲存體帳戶**] 區段下，選取 [**儲存體帳戶**]。

   ![選取儲存體帳戶](./media/manage-afs-backup/storage-accounts.png)

1. 在您選取 [**儲存體帳戶**] 之後，會顯示向保存庫註冊的儲存體帳戶清單。
1. 以滑鼠右鍵按一下您要取消註冊的儲存體帳戶，然後選取 [**取消註冊**]。

   ![選取 [取消註冊]](./media/manage-afs-backup/select-unregister.png)

## <a name="next-steps"></a>後續步驟

如需詳細資訊，請參閱針對[Azure 檔案共用備份進行疑難排解](https://docs.microsoft.com/azure/backup/troubleshoot-azure-files)。
