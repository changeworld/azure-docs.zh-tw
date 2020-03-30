---
title: 管理 Azure 檔案共用備份
description: 本文介紹用於管理和監視由 Azure 備份備份的 Azure 檔共用的常見任務。
ms.topic: conceptual
ms.date: 01/07/2020
ms.openlocfilehash: cb764fa441c063328dc350cf26f42c5bc7a0ca99
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79247653"
---
# <a name="manage-azure-file-share-backups"></a>管理 Azure 檔案共用備份

本文介紹用於管理和監視 Azure[備份](https://docs.microsoft.com/azure/backup/backup-overview)備份的 Azure 檔共用的常見任務。 您將學習如何在恢復服務保存庫中執行管理工作。

## <a name="monitor-jobs"></a>監視工作

觸發備份或還原操作時，備份服務將創建用於跟蹤的作業。 您可以在 [備份作業]**** 頁面上監視所有作業的進度。

若要開啟 [備份作業]**** 頁面：

1. 打開用於為檔共用配置備份的恢復服務保存庫。 在 **"概述"** 窗格中，在 **"監視**"部分下選擇 **"備份作業**"。

   ![監視部分中的備份作業](./media/manage-afs-backup/backup-jobs.png)

1. 選擇 **"確定"** 後，"**備份作業"** 窗格將列出所有作業的狀態。 選擇與要監視的檔共用對應的工作負荷名稱。

   ![工作負載名稱](./media/manage-afs-backup/workload-name.png)

## <a name="create-a-new-policy"></a>建立新的原則

您可以創建一個新策略，以便從恢復服務保存庫的**備份策略**部份備份 Azure 檔共用。 設定檔共用備份時創建的所有策略都將顯示為**Azure 檔共用****的策略類型**。

要查看現有備份策略：

1. 打開用於設定檔共用備份的恢復服務保存庫。 在"恢復服務保存庫"功能表上，選擇 **"管理"** 部分下的**備份策略**。 將顯示保存庫中配置的所有備份策略。

   ![所有備份策略](./media/manage-afs-backup/all-backup-policies.png)

1. 要查看特定于 Azure**檔共用**的策略，請從右上角的下拉清單中選擇**Azure 檔共用**。

   ![選擇 Azure 檔共用](./media/manage-afs-backup/azure-file-share.png)

要創建新的備份策略，請：

1. 在 **"備份策略"** 窗格中，選擇 **" 添加**"。

   ![新的備份策略](./media/manage-afs-backup/new-backup-policy.png)

1. 在 **"添加**"窗格中，選擇**Azure 檔共用**作為**策略類型**。 將打開 Azure**檔共用**的**備份策略**窗格。 指定復原點的策略名稱、備份頻率和保留範圍。 定義策略後，選擇 **"確定**"。

   ![定義備份策略](./media/manage-afs-backup/define-backup-policy.png)

## <a name="modify-policy"></a>修改原則

您可以修改備份策略以更改備份頻率或保留範圍。

要修改策略：

1. 打開用於設定檔共用備份的恢復服務保存庫。 在"恢復服務保存庫"功能表上，選擇 **"管理"** 部分下的**備份策略**。 將顯示保存庫中配置的所有備份策略。

   ![保存庫中的所有備份策略](./media/manage-afs-backup/all-backup-policies-modify.png)

1. 要查看特定于 Azure 檔共用的策略，請從右上角的下拉清單中選擇**Azure 檔共用**。 選擇要修改的備份策略。

   ![要修改的 Azure 檔共用](./media/manage-afs-backup/azure-file-share-modify.png)

1. 將打開 **"計畫"** 窗格。 根據需要編輯**備份計畫和****保留範圍**，然後選擇 **"保存**"。 您將在窗格中看到"正在進行的更新"消息。 策略更改成功更新後，您將看到消息"已成功更新備份策略"。

   ![保存修改後的策略](./media/manage-afs-backup/save-policy.png)

## <a name="stop-protection-on-a-file-share"></a>停止保護檔案共用

有兩種方式可停止保護 Azure 檔案共用︰

* 停止所有將來的備份作業，並*刪除所有復原點*。
* 停止所有將來的備份作業，但*離開復原點*。

將復原點留在存儲中可能會產生相關成本，因為 Azure 備份創建的基礎快照將保留。 離開復原點的好處是，您可以稍後還原檔共用。 有關離開復原點的成本的資訊，請參閱[定價詳細資訊](https://azure.microsoft.com/pricing/details/backup/)。 如果您決定刪除所有復原點，則無法還原檔共用。

若要停止保護 Azure 檔案共用：

1. 打開包含檔共用復原點的恢復服務保存庫。 在 **"受保護專案"** 部分下選擇**備份專案**。 將顯示備份項類型的清單。

   ![備份項目](./media/manage-afs-backup/backup-items.png)

1. 在 [備份管理類型]**** 清單中，選取 [Azure 儲存體 (Azure 檔案服務)]****。 將顯示**備份專案（Azure 存儲（Azure 檔）清單**。

   ![選擇 Azure 存儲（Azure 檔）](./media/manage-afs-backup/azure-storage-azure-files.png)

1. 在**備份專案（Azure 存儲（Azure 檔））** 清單中，選擇要停止保護的備份項。

1. 選擇 **"停止備份**"選項。

   ![選取 [停止備份]](./media/manage-afs-backup/stop-backup.png)

1. 在 **"停止備份**"窗格中，選擇 **"保留備份資料**"或 **"刪除備份資料**"。 然後選擇 **"停止備份**"。

    ![選擇"保留備份資料"或刪除備份資料](./media/manage-afs-backup/retain-or-delete-backup-data.png)

## <a name="resume-protection-on-a-file-share"></a>繼續保護檔案共用

如果在停止檔共用保護時選擇了 **"保留備份資料"** 選項，則可以恢復保護。 如果選擇了 **"刪除備份資料"** 選項，則無法恢復對檔共用的保護。

要恢復對 Azure 檔共用的保護，請執行以下規定：

1. 打開包含檔共用復原點的恢復服務保存庫。 在 **"受保護專案"** 部分下選擇**備份專案**。 將顯示備份項類型的清單。

   ![恢復的備份專案](./media/manage-afs-backup/backup-items-resume.png)

1. 在 [備份管理類型]**** 清單中，選取 [Azure 儲存體 (Azure 檔案服務)]****。 將顯示**備份專案（Azure 存儲（Azure 檔）清單**。

   ![Azure 存儲清單（Azure 檔）](./media/manage-afs-backup/azure-storage-azure-files.png)

1. 在**備份專案（Azure 存儲（Azure 檔））** 清單中，選擇要為其恢復保護的備份項。

1. 選擇"**恢復備份**"選項。

   ![選擇"恢復備份"](./media/manage-afs-backup/resume-backup.png)

1. 將打開 **"備份策略"** 窗格。 選擇您選擇的策略以恢復備份。

1. 選擇備份策略後，選擇 **"保存**"。 您將在門戶中看到"正在進行的更新"消息。 備份成功恢復後，您將看到消息"已成功更新受保護 Azure 檔共用的備份策略"。

   ![成功更新備份策略](./media/manage-afs-backup/successfully-updated.png)

## <a name="delete-backup-data"></a>刪除備份資料

您可以在 **"停止備份作業"** 期間或停止保護後的任何時間刪除檔共用的備份。 在刪除復原點之前等待數天甚至數周可能是有益的。 刪除備份資料時，無法選擇要刪除的特定復原點。 如果您決定刪除備份資料，請刪除與檔共用關聯的所有復原點。

以下過程假定檔共用的保護已停止。

要刪除 Azure 檔共用的備份資料，請執行以下規定：

1. 備份作業停止後，"**備份**"和 **"刪除"備份資料**選項在 **"備份專案"** 儀表板中可用。 選擇"**刪除備份資料**"選項。

   ![刪除備份資料](./media/manage-afs-backup/delete-backup-data.png)

1. 將打開 **"刪除備份資料"** 窗格。 輸入檔共用的名稱以確認刪除。 可選，在 **"原因**"或 **"注釋"** 框中提供更多資訊。 確定刪除備份資料後，請選擇 **"刪除**"。

   ![確認刪除資料](./media/manage-afs-backup/confirm-delete-data.png)

## <a name="unregister-a-storage-account"></a>取消註冊存儲帳戶

要使用不同的恢復服務保存庫保護特定存儲帳戶中的檔共用，請首先停止保護該存儲帳戶[中的所有檔共用](#stop-protection-on-a-file-share)。 然後從用於保護的當前恢復服務保存庫中取消註冊帳戶。

以下過程假定已停止對要取消註冊的存儲帳戶中的所有檔共用的保護。

要取消註冊存儲帳戶，請進行以下說明：

1. 打開存儲帳戶註冊的恢復服務保存庫。
1. 在 **"概述"** 窗格中，在 **"管理**"部分下選擇 **"備份基礎結構**"選項。

   ![選取 [備份基礎結構]](./media/manage-afs-backup/backup-infrastructure.png)

1. 將打開 **"備份基礎結構"** 窗格。 在**Azure 存儲帳戶**部分下選擇**存儲帳戶**。

   ![選擇存儲帳戶](./media/manage-afs-backup/storage-accounts.png)

1. 選擇 **"存儲帳戶**"後，將顯示在保存庫註冊的存儲帳戶清單。
1. 按右鍵要取消註冊的存儲帳戶，然後選擇 **"取消註冊**"。

   ![選擇取消註冊](./media/manage-afs-backup/select-unregister.png)

## <a name="next-steps"></a>後續步驟

有關詳細資訊，請參閱排除[Azure 檔共用備份的疑難排解](https://docs.microsoft.com/azure/backup/troubleshoot-azure-files)。
