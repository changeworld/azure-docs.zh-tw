---
title: 管理和監視 MARS 代理程式備份
description: 瞭解如何使用 Azure 備份服務來管理和監視 Microsoft Azure 復原服務（MARS）代理程式備份。
ms.reviewer: srinathv
ms.topic: conceptual
ms.date: 10/07/2019
ms.openlocfilehash: 376e2d53165ab822f75e635b42106e1fe13282a9
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/23/2020
ms.locfileid: "87054979"
---
# <a name="manage-microsoft-azure-recovery-services-mars-agent-backups-by-using-the-azure-backup-service"></a>使用 Azure 備份服務管理 Microsoft Azure 復原服務（MARS）代理程式備份

本文說明如何管理使用 Microsoft Azure 復原服務代理程式備份的檔案和資料夾。

## <a name="modify-a-backup-policy"></a>修改備份原則

當您修改備份原則時，可以加入新的專案、從備份中移除現有的專案，或排除檔案，使其無法使用排除設定進行備份。

- [**新增專案**] 僅使用此選項來新增要備份的新專案。 若要移除現有的專案，請使用 [**移除專案**] 或 [**排除設定**] 選項。  
- [**移除專案**] 使用此選項可移除要備份的專案。
  - 使用**排除設定**來移除磁片區中的所有專案，而不是**移除專案**。
  - 清除磁片區中的所有選取專案時，會根據上一次備份時的保留設定來保留舊的專案備份，而不需要修改範圍。
  - 重新選取這些專案，會導致第一次完整備份，而新的原則變更不會套用到舊的備份。
  - 取消取消全磁片區會保留過去的備份，而不需要任何範圍來修改保留原則。
- **排除設定**使用此選項可排除要備份的特定專案。

### <a name="add-new-items-to-existing-policy"></a>將新專案新增至現有的原則

1. 在 [**動作**] 中，按一下 [**排程備份**]。

    ![Windows Server 備份排程](./media/backup-configure-vault/schedule-first-backup.png)

2. 在 [**選取原則專案**] 索引標籤中，選取 [**修改檔案和資料夾的備份排程**] 並按 **[下一步]**。

    ![選取原則專案](./media/backup-azure-manage-mars/select-policy-items.png)

3. 在 [**修改或停止排程備份**] 索引標籤中選取 [**變更備份專案或時間**]，然後按 **[下一步]**。

    ![修改或排程備份](./media/backup-azure-manage-mars/modify-schedule-backup.png)

4. 在 [**選取要備份的專案**] 索引標籤中，按一下 [**新增專案**] 以新增您要備份的專案。

    ![修改或排程備份新增專案](./media/backup-azure-manage-mars/modify-schedule-backup-add-items.png)

5. 在 [**選取專案**] 視窗中，選取要新增的 [飛出] 或 [資料夾]，然後按一下 **[確定]**。

    ![選取專案](./media/backup-azure-manage-mars/select-item.png)

6. 完成接下來的步驟，然後按一下 **[完成**] 以完成操作。

### <a name="add-exclusion-rules-to-existing-policy"></a>將排除規則新增至現有的原則

您可以新增排除規則，以略過您不想要備份的檔案和資料夾。 在定義新原則或修改現有原則時，您可以執行此動作。

1. 按一下 [動作] 窗格中的 [**排程備份**]。 移至 [**選取要備份的專案**]，然後按一下 [**排除設定**]。

    ![選取專案](./media/backup-azure-manage-mars/select-exclusion-settings.png)

2. 在 [**排除設定**] 中，按一下 [**新增排除**]。

    ![選取專案](./media/backup-azure-manage-mars/add-exclusion.png)

3. 從 [**選取要排除的專案**] 中，流覽檔案和資料夾，並選取您要排除的專案，然後按一下 **[確定]**。

    ![選取專案](./media/backup-azure-manage-mars/select-items-exclude.png)

4. 預設會排除所選資料夾內的所有**子資料夾**。 您可以選取 **[是]** 或 [**否**] 加以變更。 您可以編輯並指定要排除的檔案類型，如下所示：

    ![選取專案](./media/backup-azure-manage-mars/subfolders-type.png)

5. 完成接下來的步驟，然後按一下 **[完成**] 以完成操作。

### <a name="remove-items-from-existing-policy"></a>從現有的原則移除專案

1. 按一下 [動作] 窗格中的 [**排程備份**]。 移至 [**選取要備份的專案**]。 從清單中，選取您想要從備份排程中移除的檔案和資料夾，然後按一下 [**移除專案**]。

    ![選取專案](./media/backup-azure-manage-mars/select-items-remove.png)

    > [!NOTE]
    > 當您從原則中完全移除磁片區時，請務必小心。  如果您需要再次新增，則會將它視為新的磁片區。 下一個排定的備份將會執行初始備份（完整備份），而不是增量備份。 如果您稍後需要暫時移除並新增專案，建議使用**排除設定**而不是**移除專案**，以確保增量備份，而不是完整備份。

2. 完成接下來的步驟，然後按一下 **[完成**] 以完成操作。

## <a name="stop-protecting-files-and-folder-backup"></a>停止保護檔案和資料夾備份

有兩種方式可停止保護檔案和資料夾備份：

- **停止保護並保留備份資料**。
  - 此選項會停止所有未來的備份作業，使其無法進行保護。
  - Azure 備份服務會繼續保留所有現有的復原點。  
  - 您將能夠還原未過期復原點的已備份資料。
  - 如果您決定繼續保護，則可以使用 [*重新啟用備份排程*] 選項。 之後，資料會根據新的保留原則加以保留。
- **停止保護並刪除備份資料**。
  - 此選項會停止所有未來的備份工作，保護您的資料並刪除所有復原點。
  - 您會收到「刪除備份資料警示」電子郵件，其中包含已*刪除此備份專案之資料的訊息。此資料將暫時可供使用14天，之後將會永久刪除*，並在*14 天內重新保護備份專案，以復原您的資料。*
  - 若要繼續保護，請在14天內從刪除作業進行重新保護。

### <a name="stop-protection-and-retain-backup-data"></a>停止保護並保留備份資料

1. 開啟 MARS 管理主控台，移至 [**動作] 窗格**，然後**選取 [排程備份**]。

    ![修改或停止排定的備份。](./media/backup-azure-manage-mars/mars-actions.png)
1. 在 [**選取原則專案**] 頁面中，選取 [**修改檔案和資料夾的備份排程**]，然後按 **[下一步]**。

    ![修改或停止排定的備份。](./media/backup-azure-manage-mars/select-policy-item-retain-data.png)
1. 從 [**修改或停止排程備份**] 頁面，選取 [**停止使用此備份排程]，但保留儲存的備份，直到重新開機排程為止**。 然後，選取 [下一步]。

    ![修改或停止排定的備份。](./media/backup-azure-manage-mars/stop-schedule-backup.png)
1. 在 [**暫停排定的備份**] 中，檢查資訊並按一下 **[完成]**。

    ![修改或停止排定的備份。](./media/backup-azure-manage-mars/pause-schedule-backup.png)
1. 在 [**修改備份**程式] 中，檢查您的排程備份暫停是否處於成功狀態，然後按一下 [**關閉**] 以完成。

### <a name="stop-protection-and-delete-backup-data"></a>停止保護並刪除備份資料

1. 開啟 MARS 管理主控台，移至 [**動作**] 窗格，然後選取 [**排程備份**]。
2. 從 [**修改或停止排程備份**] 頁面，選取 [**停止使用此備份排程] 並刪除所有儲存的備份**。 然後，選取 [下一步]。

    ![修改或停止排定的備份。](./media/backup-azure-delete-vault/modify-schedule-backup.png)

3. 從 [**停止排程的備份**] 頁面，選取 **[完成]**。

    ![停止排程的備份。](./media/backup-azure-delete-vault/stop-schedule-backup.png)
4. 系統會提示您輸入您必須手動產生的安全性 PIN 碼（個人識別碼）。 若要這麼做，請先登入 Azure 入口網站。
5. 移至 [復原**服務保存庫**] [設定] [  >  **Settings**  >  **屬性**]。
6. 在 [**安全性 PIN**] 底下，選取 [**產生**]。 複製此 PIN。 PIN 僅適用于五分鐘。
7. 在管理主控台中，貼上 PIN，然後選取 **[確定]**。

    ![產生安全性 PIN 碼。](./media/backup-azure-delete-vault/security-pin.png)

8. 在 [**修改備份進度**] 頁面中，會出現下列訊息：*已刪除的備份資料將會保留14天。在這段時間之後，備份資料將會永久刪除。*  

    ![刪除備份基礎結構。](./media/backup-azure-delete-vault/deleted-backup-data.png)

刪除內部部署備份專案之後，請遵循入口網站中的後續步驟。

## <a name="re-enable-protection"></a>重新啟用保護

如果您在保留資料時停止保護，並決定繼續保護，則可以使用 [修改備份原則] 重新啟用備份排程。

1. 在 [**動作**] 選取 [**排程備份**]。
1. 選取 [**重新啟用備份排程]。您也可以修改備份專案或時間**，然後按 **[下一步]**。<br>

    ![刪除備份基礎結構。](./media/backup-azure-manage-mars/re-enable-policy-next.png)
1. 在 [**選取要備份的專案**] 中，按 **[下一步]**。

    ![刪除備份基礎結構。](./media/backup-azure-manage-mars/re-enable-next.png)
1. 在 [**指定備份排程**] 中，指定備份排程，然後按 **[下一步]**。
1. 在 [**選取保留原則**] 中，指定保留期間，然後按 **[下一步]**。
1. 最後，在 [**確認**] 畫面中，檢查原則詳細資料，然後按一下 **[完成]**。

## <a name="re-generate-passphrase"></a>重新產生複雜密碼

複雜密碼是用來加密和解密資料，同時使用 MARS 代理程式在 Azure 中備份或還原內部部署或本機電腦。 如果遺失或忘記複雜密碼，您可以依照下列步驟重新產生複雜密碼（前提是您的電腦仍已向復原服務保存庫註冊，並已設定備份）：

1. 從 MARS 代理程式主控台，移至 [**動作] 窗格**  >  **變更屬性**>]。 然後移至 [**加密]** 索引標籤。<br>
1. 選取 [**變更複雜密碼**] 核取方塊。<br>
1. 輸入新的複雜密碼，或按一下 [**產生複雜密碼**]。
1. 按一下 **[流覽]** 以儲存新的複雜密碼。

    ![產生複雜密碼。](./media/backup-azure-manage-mars/passphrase.png)

1. 按一下 **[確定]** 以套用變更。  如果已在復原服務保存庫的 Azure 入口網站上啟用[安全性功能](./backup-azure-security-feature.md#enable-security-features)，則系統會提示您輸入安全性 PIN 碼。 若要接收 PIN，請遵循這[篇文章](./backup-azure-security-feature.md#authentication-to-perform-critical-operations)中所列的步驟。<br>
1. 從入口網站貼上安全性 PIN 碼，然後按一下 **[確定]** 套用變更。<br>

    ![產生複雜密碼。](./media/backup-azure-manage-mars/passphrase2.png)
1. 確保複雜密碼安全地儲存在替代位置（來源機器以外），最好是在 Azure Key Vault 中。 如果您有多部電腦要使用 MARS 代理程式進行備份，請追蹤所有的複雜密碼。

## <a name="managing-backup-data-for-unavailable-machines"></a>管理無法使用之機器的備份資料

本節討論使用 MARS 保護的來源機器已無法再使用的案例，因為它已被刪除、損毀、感染惡意程式碼/勒索軟體或已解除委任。

對於這些機器，Azure 備份服務會根據備份原則中指定的保留規則，確保最後一個復原點不會過期（也就是不會被剪除）。 因此，您可以安全地還原電腦。  請考慮您可以對已備份資料執行的下列案例：

### <a name="scenario-1-the-source-machine-is-unavailable-and-you-no-longer-need-to-retain-backup-data"></a>案例1：來源電腦無法使用，而且您不再需要保留備份資料

- 您可以使用本文中所列的步驟，從 Azure 入口網站刪除已備份的[資料。](backup-azure-delete-vault.md#delete-protected-items-on-premises)

### <a name="scenario-2-the-source-machine-is-unavailable-and-you-need-to-retain-backup-data"></a>案例2：來源電腦無法使用，而且您需要保留備份資料

管理 MARS 的備份原則是透過 MARS 主控台完成，而不是透過入口網站。 如果您需要在現有復原點到期之前延伸保留設定，則需要還原電腦、安裝 MARS 主控台，並擴充原則。

- 若要還原電腦，請執行下列步驟：
  1. [將 VM 還原至替代目的電腦](backup-azure-restore-windows-server.md#use-instant-restore-to-restore-data-to-an-alternate-machine)
  1. 使用與來源電腦相同的主機名稱來重新建立目的電腦
  1. 安裝代理程式，並使用相同的複雜密碼重新註冊到相同的保存庫
  1. 啟動 MARS 用戶端，以根據您的需求擴充保留期間
- 您剛還原的電腦（以 MARS 保護）將繼續進行備份。  

## <a name="next-steps"></a>後續步驟

- 如需有關支援的案例和限制的詳細資訊，請參閱[MARS 代理程式的支援矩陣](./backup-support-matrix-mars-agent.md)。
- 深入瞭解[隨選備份原則保留行為](backup-windows-with-mars-agent.md#set-up-on-demand-backup-policy-retention-behavior)。
- 如需更多常見問題，請參閱[MARS 代理程式常見問題](backup-azure-file-folder-backup-faq.md)。
