---
title: 管理並監控 MARS 代理程式
description: 瞭解如何使用 Azure 備份服務管理和監視 Microsoft Azure 恢復服務 (MARS) 代理備份。
ms.reviewer: srinathv
ms.topic: conceptual
ms.date: 10/07/2019
ms.openlocfilehash: 404341c8324d9e127e8d8e6bc8083926c0d3106f
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/16/2020
ms.locfileid: "81537350"
---
# <a name="manage-microsoft-azure-recovery-services-mars-agent-backups-by-using-the-azure-backup-service"></a>使用 Azure 備份服務管理 Microsoft Azure 復原服務 (MARS) 代理備份

本文介紹如何管理使用 Microsoft Azure 恢復服務代理備份的檔和資料夾。

## <a name="modify-a-backup-policy"></a>修改備份原則

修改備份策略時,可以添加新專案、從備份中刪除現有專案,或使用排除設置排除備份檔。

- **添加專案**僅用於添加新項目進行備份。 要刪除現有專案,請使用 **「刪除項目**」或 **「排除設定」** 選項。  
- **移除專案**使用此選項從備份中刪除專案。
  - 使用**排除設定**刪除所選取的所有項目,而不是**刪除項目**。
  - 清除卷中的所有選擇會導致專案的舊備份(根據上次備份時保留的保留設置)保留,而沒有修改的餘地。
  - 重新選擇這些專案,將會導致第一次完全備份,並且新的策略更改不會應用於舊備份。
  - 取消選擇整個卷將保留過去的備份,沒有任何修改保留策略的餘地。
- **排除設置**使用此選項從備份中排除特定項。

### <a name="add-new-items-to-existing-policy"></a>新增新專案加入現有政策

1. 在 **"操作"** 中,單擊 **「計劃備份**」。。

    ![Windows Server 備份排程](./media/backup-configure-vault/schedule-first-backup.png)

2. 在 **「選擇策略項**」選項卡中,然後選擇 **「修改檔案和資料夾的備份計畫**」,然後按下「**下一步**」。

    ![選擇原則項目](./media/backup-azure-manage-mars/select-policy-items.png)

3. 在 **"修改或停止計劃備份**"選項卡中,選擇 **"更改備份專案或時間**",然後按一下"**下一步**" 。

    ![變更或計劃備份](./media/backup-azure-manage-mars/modify-schedule-backup.png)

4. 在 **「將項目選擇備份**」選項卡中,按下「**添加專案**」以添加要備份的專案。

    ![變更或計劃備份新增專案](./media/backup-azure-manage-mars/modify-schedule-backup-add-items.png)

5. 在 **「選擇項目」** 視窗中,選擇要添加的蒼蠅或資料夾,然後單擊「**確定**」。

    ![選擇項目](./media/backup-azure-manage-mars/select-item.png)

6. 完成後續步驟,然後單擊 **"完成"** 以完成操作。

### <a name="add-exclusion-rules-to-existing-policy"></a>將排除規則新增到現有政策

您可以添加排除規則來跳過不想備份的檔案和資料夾。 您可以在定義新策略或修改現有策略期間執行此操作。

1. 在「操作」窗格中,按下 **「計畫備份**」 。。 跳到 **「選擇要備份的專案**」,然後單擊 **「排除設置**」 。。

    ![選擇項目](./media/backup-azure-manage-mars/select-exclusion-settings.png)

2. 在**排除設置中**,按下「**添加排除**」。。

    ![選擇項目](./media/backup-azure-manage-mars/add-exclusion.png)

3. 從 **「選擇項目」到「排除**」,瀏覽檔和資料夾並選擇要排除的項目,然後按下「**確定**」。

    ![選擇項目](./media/backup-azure-manage-mars/select-items-exclude.png)

4. 預設情況下,所選**資料夾中的所有子資料夾**都被排除在外。 您可以通過選擇 **「或****」或 「否**」來更改此。 您可以編輯和指定要排除的檔案類型,如下所示:

    ![選擇項目](./media/backup-azure-manage-mars/subfolders-type.png)

5. 完成後續步驟,然後單擊 **"完成"** 以完成操作。

### <a name="remove-items-from-existing-policy"></a>從現有策略中移除項目

1. 在「操作」窗格中,按下 **「計畫備份**」 。。 跳到**選擇要備份的專案**。 從清單中,選擇要從備份計畫中刪除的檔和資料夾,然後單擊 **「刪除專案**」。。

    ![選擇項目](./media/backup-azure-manage-mars/select-items-remove.png)

> [!NOTE]
> 從策略中完全刪除卷時,請小心操作。  如果需要再次添加它,則它將被視為新卷。 下一個計劃備份將執行初始備份(完全備份),而不是增量備份。 如果需要稍後臨時刪除和添加項目,建議使用 **「排除設置」** 而不是 **「刪除專案**」來確保增量備份而不是完全備份。

2. 完成後續步驟,然後單擊 **"完成"** 以完成操作。

## <a name="stop-protecting-files-and-folder-backup"></a>停止保護檔案與資料夾備份

有兩種方法可以停止保護檔案和資料夾備份:

- **停止保護並保留備份資料**。
  - 此選項將停止保護以後的所有備份作業。
  - Azure 備份服務將無限期地保留所有現有恢復點。 在恢復保護之前,不會檢查恢復點是否過期。
  - 您將能夠為未過期的恢復點還原備份的數據。
  - 如果您決定恢復保護,則可以使用 *「重新啟用備份計劃」* 選項。 之後,將根據新的保留政策保留數據。
- **停止保護並移除備份資料**。
  - 這個選項將停止所有將來的備份作業保護數據並刪除所有恢復點。
  - 您將收到刪除備份資料警報電子郵件,郵件顯示*此備份項目的數據已被刪除。此數據將暫時使用 14 天,之後將被永久刪除*,建議在*14 天內重新保護備份專案以恢復您的數據。*
  - 要恢復保護,請從刪除操作的 14 天內重新保護。

### <a name="stop-protection-and-retain-backup-data"></a>停止保護並保留備份資料

1. 打開 MARS 管理主控台,轉到 **"操作"窗格**,然後**選擇"計劃備份**"。

    ![修改或停止計劃備份。](./media/backup-azure-manage-mars/mars-actions.png)
1. 在**選擇策略項目「** 頁**中,選擇」修改檔案和資料夾的備份計畫**」,然後按下 **「 下一步**」。

    ![修改或停止計劃備份。](./media/backup-azure-manage-mars/select-policy-item-retain-data.png)
1. 在 **"修改或停止計劃備份" 頁中**,選擇 **「停止使用此備份計畫」,但保留儲存的備份,直到計畫再次啟動**。 然後，選取 [下一步]  。

    ![修改或停止計劃備份。](./media/backup-azure-manage-mars/stop-schedule-backup.png)
1. 在**暫停計劃備份**中查看資訊,然後按一**下 「完成**」。

    ![修改或停止計劃備份。](./media/backup-azure-manage-mars/pause-schedule-backup.png)
1. 在 **「修改備份過程」** 中,請檢查您的計畫備份暫停處於成功狀態,然後單擊**接近**完成。

### <a name="stop-protection-and-delete-backup-data"></a>停止保護並移除備份資料

1. 打開 MARS 管理主控台,轉到 **"操作"** 窗格,然後選擇 **「計畫備份**」。。
2. 在 **「修改或停止計劃備份」 頁中**,選擇 **「停止使用此備份計畫」 並移除所有儲存的備份**。 然後，選取 [下一步]  。

    ![修改或停止計劃備份。](./media/backup-azure-delete-vault/modify-schedule-backup.png)

3. 在 **「停止計劃備份**」頁中,選擇 **「完成**」。。

    ![停止計劃備份。](./media/backup-azure-delete-vault/stop-schedule-backup.png)
4. 系統將提示您輸入安全 PIN(個人識別號),您必須手動生成該 PIN 碼。 為此,請先登錄到 Azure 門戶。
5. 跳到**修復服務保存庫** > **設定** > **屬性**。
6. 在 **"安全 PIN"** 下,選擇 **"生成**"。 複製此 PIN。 PIN 僅有效五分鐘。
7. 在管理控制台中,粘貼 PIN,然後選擇 **「確定**」。。

    ![生成安全 PIN。](./media/backup-azure-delete-vault/security-pin.png)

8. 在 **「修改備份進度」** 頁中,將顯示以下消息:*已刪除的備份資料將保留 14 天。之後,備份數據將被永久刪除。*  

    ![刪除備份基礎結構。](./media/backup-azure-delete-vault/deleted-backup-data.png)

刪除本地備份項后,請按照門戶中的後續步驟操作。

## <a name="re-enable-protection"></a>重新開啟保護

如果在保留數據時停止保護,並決定恢復保護,則可以使用修改備份策略重新啟用備份計劃。

1. 在 **『操作』** 上選擇**計畫備份**。
1. 選擇 **「重新啟用備份計畫」。您還可以修改備份項目或時間**,然後按下 **「下一步**」。<br>

    ![刪除備份基礎結構。](./media/backup-azure-manage-mars/re-enable-policy-next.png)
1. 在 **「選擇要備份的專案**」中,按**下「下一步**」 。

    ![刪除備份基礎結構。](./media/backup-azure-manage-mars/re-enable-next.png)
1. 在 **「指定備份計劃」** 中,指定備份計畫,然後單擊「**下一步**」。
1. 在 **「選擇保留策略」** 中,指定保留期限,然後單擊 **「下一步**」 。
1. 最後在**確認**螢幕中,查看策略詳細資訊,然後單擊 **「完成**」。

## <a name="re-generate-passphrase"></a>重新產生密碼短語

密碼短語用於加密和解密數據,同時使用 MARS 代理備份或還原本地或本地電腦以從 Azure 備份或還原本地電腦。 如果您丟失或忘記了密碼短語,則可以按照以下步驟重新生成密碼短語(前提是您的電腦仍在恢復服務保管庫註冊並配置了備份):

- 從 MARS 代理主控台轉到**操作窗格** > **更改屬性**>。 然後轉到**加密選項卡**。<br>
- 選擇 **「更改密碼」** 選方塊。<br>
- 輸入新的密碼短語或單擊 **「生成密碼短語**」。
- 按下 **「瀏覽」** 以保存新密碼。

    ![生成密碼。](./media/backup-azure-manage-mars/passphrase.png)
- 按下「**確定」** 以應用更改。  如果在恢復服務保管庫的 Azure 門戶上啟用[了安全功能](https://docs.microsoft.com/azure/backup/backup-azure-security-feature#enable-security-features),則系統將提示您輸入安全 PIN。 要接收 PIN,請按照[本文](https://docs.microsoft.com/azure/backup/backup-azure-security-feature#authentication-to-perform-critical-operations)中列出的步驟操作。<br>
- 從門戶粘貼安全 PIN 並按下 **「確定」** 以應用更改。<br>

    ![生成密碼。](./media/backup-azure-manage-mars/passphrase2.png)
- 確保密碼短語安全地保存在備用位置(原始電腦之外),最好保存在 Azure 密鑰保管庫中。 如果使用 MARS 代理備份多台電腦,請追蹤所有密碼。


## <a name="next-steps"></a>後續步驟

- 有關受支援的方案和限制的資訊,請參閱 MARS[代理的支援矩陣](https://docs.microsoft.com/azure/backup/backup-support-matrix-mars-agent)。
- 詳細瞭解[按備份原則保留行為](backup-windows-with-mars-agent.md#set-up-on-demand-backup-policy-retention-behavior)。
