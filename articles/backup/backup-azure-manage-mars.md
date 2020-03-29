---
title: 管理和監視 MARS 代理備份
description: 瞭解如何使用 Azure 備份服務管理和監視 Microsoft Azure 恢復服務 （MARS） 代理備份。
ms.reviewer: srinathv
ms.topic: conceptual
ms.date: 10/07/2019
ms.openlocfilehash: c11d73edd32c197aac2cec58eeb1cc20e5c6a339
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "78673257"
---
# <a name="manage-microsoft-azure-recovery-services-mars-agent-backups-by-using-the-azure-backup-service"></a>使用 Azure 備份服務管理 Microsoft Azure 恢復服務 （MARS） 代理備份

本文介紹如何管理使用 Microsoft Azure 恢復服務代理備份的檔和資料夾。

## <a name="modify-a-backup-policy"></a>修改備份原則

修改備份策略時，可以添加新專案、從備份中刪除現有專案，或使用排除設置排除備份檔案。

- **添加專案**僅用於添加新專案進行備份。 要刪除現有專案，請使用 **"刪除專案**"或 **"排除設置"** 選項。  
- **刪除專案**使用此選項從備份中刪除專案。
  - 使用**排除設置**刪除卷中的所有專案，而不是**刪除專案**。
  - 清除卷中的所有選擇會導致專案的舊備份（根據上次備份時保留的保留設置）保留，而沒有修改的餘地。
  - 重新選擇這些專案，將會導致第一次完全備份，並且新的策略更改不會應用於舊備份。
  - 取消選擇整個卷將保留過去的備份，沒有任何修改保留原則的餘地。
- **排除設置**使用此選項從備份中排除特定項。

### <a name="add-new-items-to-existing-policy"></a>將新專案添加到現有策略

1. 在 **"操作"** 中，按一下 **"計畫備份**"。

    ![Windows Server 備份排程](./media/backup-configure-vault/schedule-first-backup.png)

2. 在 **"選擇策略項**"選項卡中，然後選擇 **"修改檔和資料夾的備份計畫**"，然後按一下"**下一步**"。

    ![選擇策略項](./media/backup-azure-manage-mars/select-policy-items.png)

3. 在 **"修改或停止計畫備份**"選項卡中，選擇 **"更改備份專案或時間**"，然後按一下"**下一步**"。

    ![修改或計畫備份](./media/backup-azure-manage-mars/modify-schedule-backup.png)

4. 在 **"將專案選擇備份**"選項卡中，按一下"**添加專案**"以添加要備份的專案。

    ![修改或計畫備份添加專案](./media/backup-azure-manage-mars/modify-schedule-backup-add-items.png)

5. 在 **"選擇專案"** 視窗中，選擇要添加的蒼蠅或資料夾，然後按一下"**確定**"。

    ![選擇專案](./media/backup-azure-manage-mars/select-item.png)

6. 完成後續步驟，然後按一下 **"完成"** 以完成操作。

### <a name="add-exclusion-rules-to-existing-policy"></a>將排除規則添加到現有策略

您可以添加排除規則來跳過不想備份的檔和資料夾。 您可以在定義新策略或修改現有策略期間執行此操作。

1. 在"操作"窗格中，按一下 **"計畫備份**"。 轉到 **"選擇要備份的專案**"，然後按一下 **"排除設置**"。

    ![選擇專案](./media/backup-azure-manage-mars/select-exclusion-settings.png)

2. 在**排除設置中**，按一下"**添加排除**"。

    ![選擇專案](./media/backup-azure-manage-mars/add-exclusion.png)

3. 從 **"選擇專案"到"排除**"，流覽檔和資料夾並選擇要排除的專案，然後按一下"**確定**"。

    ![選擇專案](./media/backup-azure-manage-mars/select-items-exclude.png)

4. 預設情況下，所選**資料夾中的所有子資料夾**都被排除在外。 您可以通過選擇 **"是**"或 **"否**"來更改此。 您可以編輯和指定要排除的檔案類型，如下所示：

    ![選擇專案](./media/backup-azure-manage-mars/subfolders-type.png)

5. 完成後續步驟，然後按一下 **"完成"** 以完成操作。

### <a name="remove-items-from-existing-policy"></a>從現有策略中刪除專案

1. 在"操作"窗格中，按一下 **"計畫備份**"。 轉到 **"選擇要備份的專案**"。 從清單中，選擇要從備份計畫中刪除的檔和資料夾，然後按一下 **"刪除專案**"。

    ![選擇專案](./media/backup-azure-manage-mars/select-items-remove.png)

> [!NOTE]
> 從策略中完全刪除卷時，請小心操作。  如果需要再次添加它，則它將被視為新卷。 下一個計畫備份將執行初始備份（完全備份），而不是增量備份。 如果需要稍後臨時刪除和添加專案，建議使用 **"排除設置"** 而不是 **"刪除專案**"來確保增量備份而不是完全備份。

2. 完成後續步驟，然後按一下 **"完成"** 以完成操作。

## <a name="stop-protecting-files-and-folder-backup"></a>停止保護檔和資料夾備份

有兩種方法可以停止保護檔和資料夾備份：

- **停止保護並保留備份資料**。
  - 此選項將停止保護以後的所有備份作業。
  - Azure 備份服務將保留已根據保留原則備份的復原點。
  - 您將能夠為未過期的復原點還原備份的資料。
  - 如果您決定恢復保護，則可以使用 *"重新啟用備份計畫"* 選項。 之後，將根據新的保留政策保留資料。
- **停止保護並刪除備份資料**。
  - 此選項將停止所有將來的備份作業保護資料並刪除所有復原點。
  - 您將收到刪除備份資料警報電子郵件，郵件顯示*此備份專案的資料已被刪除。此資料將暫時使用 14 天，之後將被永久刪除*，建議在*14 天內重新保護備份專案以恢復您的資料。*
  - 要恢復保護，請從刪除操作的 14 天內重新保護。

### <a name="stop-protection-and-retain-backup-data"></a>停止保護並保留備份資料

1. 打開 MARS 管理主控台，轉到 **"操作"窗格**，然後**選擇"計畫備份**"。

    ![修改或停止計畫備份。](./media/backup-azure-manage-mars/mars-actions.png)
1. 在 **"選擇策略專案"** 頁**中，選擇"修改檔和資料夾的備份計畫**"，然後按一下"**下一步**"。

    ![修改或停止計畫備份。](./media/backup-azure-manage-mars/select-policy-item-retain-data.png)
1. 在 **"修改或停止計畫備份"頁中**，選擇 **"停止使用此備份計畫"，但保留存儲的備份，直到計畫再次啟動**。 然後，選擇 **"下一步**"。

    ![修改或停止計畫備份。](./media/backup-azure-manage-mars/stop-schedule-backup.png)
1. 在**暫停計畫備份**中查看資訊，然後按一下 **"完成**"。

    ![修改或停止計畫備份。](./media/backup-azure-manage-mars/pause-schedule-backup.png)
1. 在 **"修改備份過程"** 中，請檢查您的計畫備份暫停處於成功狀態，然後按一下**接近**完成。

### <a name="stop-protection-and-delete-backup-data"></a>停止保護並刪除備份資料

1. 打開 MARS 管理主控台，轉到 **"操作"** 窗格，然後選擇 **"計畫備份**"。
2. 在 **"修改或停止計畫備份"頁中**，選擇 **"停止使用此備份計畫"並刪除所有存儲的備份**。 然後，選擇 **"下一步**"。

    ![修改或停止計畫備份。](./media/backup-azure-delete-vault/modify-schedule-backup.png)

3. 在 **"停止計畫備份**"頁中，選擇 **"完成**"。

    ![停止計畫備份。](./media/backup-azure-delete-vault/stop-schedule-backup.png)
4. 系統將提示您輸入安全 PIN（個人識別碼），您必須手動生成該 PIN 碼。 為此，請先登錄到 Azure 門戶。
5. 轉到**恢復服務保存庫** > **設置** > **屬性**。
6. 在 **"安全 PIN"** 下，選擇 **"生成**"。 複製此 PIN。 PIN 僅有效五分鐘。
7. 在管理主控台中，粘貼 PIN，然後選擇 **"確定**"。

    ![生成安全 PIN。](./media/backup-azure-delete-vault/security-pin.png)

8. 在 **"修改備份進度"** 頁中，將顯示以下消息：*已刪除的備份資料將保留 14 天。之後，備份資料將被永久刪除。*  

    ![刪除備份基礎結構。](./media/backup-azure-delete-vault/deleted-backup-data.png)

刪除本地備份項後，請按照門戶中的後續步驟操作。

## <a name="re-enable-protection"></a>重新啟用保護

如果在保留資料時停止保護，並決定恢復保護，則可以使用修改備份策略重新啟用備份計畫。

1. 在 **"操作"** 上選擇 **"計畫備份**"。
1. 選擇 **"重新啟用備份計畫"。您還可以修改備份專案或時間**，然後按一下 **"下一步**"。<br>

    ![刪除備份基礎結構。](./media/backup-azure-manage-mars/re-enable-policy-next.png)
1. 在 **"選擇要備份的專案**"中，按一下 **"下一步**"。

    ![刪除備份基礎結構。](./media/backup-azure-manage-mars/re-enable-next.png)
1. 在 **"指定備份計畫"** 中，指定備份計畫，然後按一下"**下一步**"。
1. 在 **"選擇保留原則"** 中，指定保留期限，然後按一下 **"下一步**"。
1. 最後在**確認**螢幕中，查看策略詳細資訊，然後按一下 **"完成**"。

## <a name="re-generate-passphrase"></a>重新生成密碼短語

密碼短語用於加密和解密資料，同時使用 MARS 代理備份或還原本地或本地電腦以從 Azure 備份或還原本地電腦。 如果您丟失或忘記了密碼短語，則可以按照以下步驟重新生成密碼短語（前提是您的電腦仍在恢復服務保存庫註冊並配置了備份）：

- 從 MARS 代理主控台轉到**執行窗格** > **更改屬性**>。 然後轉到**加密選項卡**。<br>
- 選擇 **"更改密碼"** 核取方塊。<br>
- 輸入新的密碼短語或按一下 **"生成密碼短語**"。
- 按一下 **"流覽"** 以保存新密碼。

    ![生成密碼。](./media/backup-azure-manage-mars/passphrase.png)
- 按一下"**確定"** 以應用更改。  如果在恢復服務保存庫的 Azure 門戶上啟用[了安全功能](https://docs.microsoft.com/azure/backup/backup-azure-security-feature#enable-security-features)，則系統將提示您輸入安全 PIN。 要接收 PIN，請按照[本文](https://docs.microsoft.com/azure/backup/backup-azure-security-feature#authentication-to-perform-critical-operations)中列出的步驟操作。<br>
- 從門戶粘貼安全 PIN 並按一下 **"確定"** 以應用更改。<br>

    ![生成密碼。](./media/backup-azure-manage-mars/passphrase2.png)
- 確保密碼短語安全地保存在備用位置（源電腦之外），最好保存在 Azure 金鑰保存庫中。 如果使用 MARS 代理備份多台電腦，請跟蹤所有密碼。


## <a name="next-steps"></a>後續步驟

- 有關受支援的方案和限制的資訊，請參閱 MARS[代理的支援矩陣](https://docs.microsoft.com/azure/backup/backup-support-matrix-mars-agent)。
- 詳細瞭解[按需備份策略保留行為](backup-windows-with-mars-agent.md#set-up-on-demand-backup-policy-retention-behavior)。
