---
title: 管理及監視 MARS 代理程式備份
description: 瞭解如何使用 Azure 備份服務管理和監視 Microsoft Azure 復原服務 (MARS) 代理程式備份。
ms.reviewer: srinathv
ms.topic: conceptual
ms.date: 10/07/2019
ms.openlocfilehash: 581141ea5ffaecf774d292f66a05da5e40c8dc54
ms.sourcegitcommit: 64ad2c8effa70506591b88abaa8836d64621e166
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/17/2020
ms.locfileid: "88263294"
---
# <a name="manage-microsoft-azure-recovery-services-mars-agent-backups-by-using-the-azure-backup-service"></a>使用 Azure 備份服務來管理 Microsoft Azure 復原服務 (MARS) 代理程式備份

本文說明如何管理 Microsoft Azure 復原服務代理程式備份的檔案和資料夾。

## <a name="modify-a-backup-policy"></a>修改備份原則

當您修改備份原則時，您可以加入新的專案、從備份中移除現有的專案，或使用 [排除設定] 排除要備份的檔案。

- [**新增專案**] 只會使用此選項來新增要備份的新專案。 若要移除現有的專案，請使用 [ **移除專案** ] 或 [ **排除設定** ] 選項。  
- **移除專案** ：使用此選項可將專案從備份中移除。
  - 使用 **排除設定** 來移除磁片區中的所有專案，而不是 **移除專案**。
  - 清除磁片區中的所有選取專案，會根據上一次備份時的保留設定保留專案的舊備份，而不需要修改範圍。
  - Reselecting 這些專案，會導致第一次完整備份，而新的原則變更不會套用至舊的備份。
  - 取消全磁片區會保留過去的備份，而不需要任何範圍來修改保留原則。
- **排除設定** 會使用這個選項，將特定專案排除在備份範圍之外。

### <a name="add-new-items-to-existing-policy"></a>將新專案新增至現有的原則

1. 在 [ **動作**] 中，選取 [ **排程備份**]。

    ![Windows Server 備份排程](./media/backup-configure-vault/schedule-first-backup.png)

2. 在 [ **選取原則專案** ] 索引標籤中，選取 [ **修改檔案和資料夾的備份排程** ] 並選取 **[下一步**]。

    ![選取原則專案](./media/backup-azure-manage-mars/select-policy-items.png)

3. 在 [ **修改或停止排程備份** ] 索引標籤中，選取 [ **變更備份專案或時間** ] 並選取 **[下一步]**。

    ![修改或排程備份](./media/backup-azure-manage-mars/modify-schedule-backup.png)

4. 在 [ **選取要備份的專案** ] 索引標籤中，選取 [ **新增專案** ] 以新增您要備份的專案。

    ![修改或排程備份新增專案](./media/backup-azure-manage-mars/modify-schedule-backup-add-items.png)

5. 在 [ **選取專案** ] 視窗中，選取您要新增的檔案或資料夾，然後選取 **[確定]**。

    ![選取專案](./media/backup-azure-manage-mars/select-item.png)

6. 完成接下來的步驟，然後選取 **[完成]** 以完成此操作。

### <a name="add-exclusion-rules-to-existing-policy"></a>將排除規則新增至現有的原則

您可以新增排除規則，以略過您不想要備份的檔案和資料夾。 在定義新的原則或修改現有的原則時，您可以執行此動作。

1. 從 [動作] 窗格中，選取 [ **排程備份**]。 移至 [ **選取要備份的專案** ]，然後選取 [ **排除設定**]。

    ![排除設定](./media/backup-azure-manage-mars/select-exclusion-settings.png)

2. 在 [ **排除設定**] 中，選取 [ **新增排除**]。

    ![新增排除](./media/backup-azure-manage-mars/add-exclusion.png)

3. 從 [ **選取要排除的專案**] 中，流覽檔案和資料夾並選取您要排除的專案，然後選取 **[確定]**。

    ![選取要排除的專案](./media/backup-azure-manage-mars/select-items-exclude.png)

4. 預設會排除所選資料夾內的所有 **子資料夾** 。 您可以選取 **[是]** 或 [ **否**] 來變更此變更。 您可以編輯並指定要排除的檔案類型，如下所示：

    ![選取子資料夾類型](./media/backup-azure-manage-mars/subfolders-type.png)

5. 完成接下來的步驟，然後選取 **[完成]** 以完成此操作。

### <a name="remove-items-from-existing-policy"></a>從現有的原則中移除專案

1. 從 [動作] 窗格中，選取 [ **排程備份**]。 移至 [ **選取要備份的專案**]。 從清單中選取您想要從備份排程移除的檔案和資料夾，然後選取 [ **移除專案**]。

    ![選取要移除的專案](./media/backup-azure-manage-mars/select-items-remove.png)

    > [!NOTE]
    > 當您從原則中完全移除磁片區時，請繼續小心。  如果您需要再次新增，則會將它視為新的磁片區。 下一個排定的備份將會執行 (完整備份) 的初始備份，而不是增量備份。 如果您稍後需要暫時移除及新增專案，則建議使用 **排除設定** 而非 **移除專案** ，以確保增量備份而不是完整備份。

2. 完成接下來的步驟，然後選取 **[完成]** 以完成此操作。

## <a name="stop-protecting-files-and-folder-backup"></a>停止保護檔案和資料夾備份

有兩種方式可停止保護檔案和資料夾備份：

- **停止保護並保留備份資料**。
  - 此選項將會停止所有未來的備份作業進行保護。
  - Azure 備份服務會繼續保留所有現有的復原點。  
  - 您將能夠針對未到期的復原點還原已備份的資料。
  - 如果您決定繼續保護，則可以使用 *重新啟用備份排程* 選項。 之後，會根據新的保留原則來保留資料。
- **停止保護並刪除備份資料**。
  - 此選項將會停止所有未來的備份作業以保護您的資料，並刪除所有復原點。
  - 您將會收到刪除備份資料警示電子郵件，其中包含已 *刪除此備份專案資料的訊息。這項資料將暫時可供14天使用，之後將會永久刪除* ，並且建議您 *在14天內重新保護備份專案以復原您的資料。*
  - 若要繼續保護，請從刪除作業的14天內重新保護。

### <a name="stop-protection-and-retain-backup-data"></a>停止保護並保留備份資料

1. 開啟 MARS 管理主控台，移至 [ **動作] 窗格**，然後 **選取 [排程備份**]。

    ![選取排程備份](./media/backup-azure-manage-mars/mars-actions.png)
1. 在 [ **選取原則專案** ] 頁面中，選取 [ **修改檔案和資料夾的備份排程** ]，然後選取 **[下一步]**。

    ![選取原則專案](./media/backup-azure-manage-mars/select-policy-item-retain-data.png)
1. 在 [ **修改或停止排定的備份** ] 頁面上，選取 [ **停止使用此備份排程]，但保留已儲存的備份，直到重新開機排程為止**。 然後，選取 [下一步]。

    ![停止已排程的備份。](./media/backup-azure-manage-mars/stop-schedule-backup.png)
1. 在 [ **暫停排程備份**] 中，檢查資訊並選取 **[完成]**。

    ![暫停排定的備份。](./media/backup-azure-manage-mars/pause-schedule-backup.png)
1. 在 [ **修改備份進度**] 中，檢查您的排程備份暫停是否為成功狀態，然後選取 [ **關閉** ] 以完成。

### <a name="stop-protection-and-delete-backup-data"></a>停止保護並刪除備份資料

1. 開啟 MARS 管理主控台，移至 [ **動作** ] 窗格，然後選取 [ **排程備份**]。
2. 在 [ **修改或停止排定的備份** ] 頁面上，選取 [ **停止使用此備份排程]，然後刪除所有儲存的備份**。 然後，選取 [下一步]。

    ![修改或停止已排程的備份。](./media/backup-azure-delete-vault/modify-schedule-backup.png)

3. 從 [ **停止排程備份** ] 頁面上，選取 **[完成]**。

    ![停止已排程的備份，然後選取 [完成]](./media/backup-azure-delete-vault/stop-schedule-backup.png)
4. 系統會提示您輸入安全性 PIN 碼 (個人識別碼) ，您必須手動產生此識別碼。 若要這樣做，請先登入 Azure 入口網站。
5. 移至 [復原**服務保存庫**  >  **設定**  >  **屬性**]。
6. 在 [ **安全性 PIN**] 底下，選取 [ **產生**]。 複製此 PIN。 PIN 只適用于五分鐘。
7. 在管理主控台中，貼上 PIN，然後選取 **[確定]**。

    ![產生安全性 PIN 碼。](./media/backup-azure-delete-vault/security-pin.png)

8. 在 [ **修改備份進度** ] 頁面中，會出現下列訊息： *已刪除的備份資料會保留14天。在這段時間之後，將會永久刪除備份資料。*  

    ![修改備份進度](./media/backup-azure-delete-vault/deleted-backup-data.png)

刪除內部部署備份專案之後，請遵循入口網站中的後續步驟。

## <a name="re-enable-protection"></a>重新啟用保護

如果您在保留資料並決定繼續保護時停止保護，則可以使用 [修改備份原則] 重新啟用備份排程。

1. 在 [ **動作** ] 選取 [ **排程備份**]。
1. 選取 [ **重新啟用備份排程]。您也可以修改備份專案或時間** ，然後選取 **[下一步]**。<br>

    ![重新啟用備份排程](./media/backup-azure-manage-mars/re-enable-policy-next.png)
1. 在 [ **選取要備份的專案**] 中，選取 **[下一步]**。

    ![選取要備份的專案](./media/backup-azure-manage-mars/re-enable-next.png)
1. 在 [ **指定備份排程**] 中，指定備份排程，然後選取 **[下一步]**。
1. 在 [ **選取保留原則**] 中，指定保留期間，然後選取 **[下一步]**。
1. 最後在 **確認** 畫面中，檢查原則詳細資料，然後選取 **[完成]**。

## <a name="re-generate-passphrase"></a>重新產生複雜密碼

使用 MARS 代理程式在 Azure 中備份或還原內部部署或本機電腦時，會使用複雜密碼來加密和解密資料。 如果遺失或忘記複雜密碼，則您可以重新產生複雜密碼 (前提是您的電腦仍向復原服務保存庫註冊，且已依照下列步驟將備份設定) ：

1. 從 MARS 代理程式主控台，移至 [**動作] 窗格**的 [  >  **變更**內容] >。 然後移至 [ **加密]** 索引標籤。<br>
1. 選取 [ **變更複雜密碼** ] 核取方塊。<br>
1. 輸入新的複雜密碼，或選取 [ **產生複雜密碼**]。
1. 選取 **[流覽]** 以儲存新的複雜密碼。

    ![產生複雜密碼。](./media/backup-azure-manage-mars/passphrase.png)

1. 選取 **[確定]** 以套用變更。  如果在復原服務保存庫的 Azure 入口網站上啟用了 [安全性功能](./backup-azure-security-feature.md#enable-security-features) ，系統將會提示您輸入安全性 PIN 碼。 若要接收 PIN，請遵循本文所列的[步驟。](./backup-azure-security-feature.md#authentication-to-perform-critical-operations)<br>
1. 從入口網站貼上安全性 PIN 碼，然後選取 **[確定]** 以套用變更。<br>

    ![貼上安全性 PIN 碼](./media/backup-azure-manage-mars/passphrase2.png)
1. 確定複雜密碼會安全地儲存在來源電腦以外的替代位置 () ，最好是在 Azure Key Vault 中。 如果您有多部電腦與 MARS 代理程式進行備份，請追蹤所有的複雜密碼。

## <a name="managing-backup-data-for-unavailable-machines"></a>管理無法使用之電腦的備份資料

本節討論因為已刪除、損毀、受惡意程式碼/勒索軟體感染或解除委任而受到 MARS 保護的來源電腦無法再使用的案例。

針對這些機器，Azure 備份服務可確保最後一個復原點不會過期 (也就是說，不會根據備份原則中指定的保留規則來剪除) 。 因此，您可以安全地還原電腦。  請考慮下列您可以在備份資料上執行的案例：

### <a name="scenario-1-the-source-machine-is-unavailable-and-you-no-longer-need-to-retain-backup-data"></a>案例1：來源電腦無法使用，因此您不再需要保留備份資料

- 您可以 [使用本文所](backup-azure-delete-vault.md#delete-protected-items-on-premises)列的步驟，從 Azure 入口網站中刪除備份的資料。

### <a name="scenario-2-the-source-machine-is-unavailable-and-you-need-to-retain-backup-data"></a>案例2：來源電腦無法使用，而且您需要保留備份資料

管理 MARS 的備份原則是透過 MARS 主控台完成的，而不是透過入口網站。 如果您需要在現有復原點到期前擴充保留設定，則必須還原電腦、安裝 MARS 主控台，並擴充原則。

- 若要還原電腦，請執行下列步驟：
  1. [將 VM 還原至替代目的電腦](backup-azure-restore-windows-server.md#use-instant-restore-to-restore-data-to-an-alternate-machine)
  1. 重新建立與來源電腦具有相同主機名稱的目的電腦
  1. 安裝代理程式，並使用相同的複雜密碼重新註冊到相同的保存庫
  1. 啟動 MARS 用戶端以根據您的需求擴充保留期間
- 您新還原的機器（以 MARS 保護）將繼續進行備份。  

## <a name="next-steps"></a>後續步驟

- 如需有關支援案例和限制的詳細資訊，請參閱 [MARS 代理程式的支援矩陣](./backup-support-matrix-mars-agent.md)。
- 深入瞭解 [隨選備份原則保留行為](backup-windows-with-mars-agent.md#set-up-on-demand-backup-policy-retention-behavior)。
- 如需常見問題的詳細資訊，請參閱 [MARS 代理程式常見問題](backup-azure-file-folder-backup-faq.md)。
