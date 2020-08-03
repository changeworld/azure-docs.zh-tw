---
title: 教學課程 - 備份多個 Azure 虛擬機器
description: 在本教學課程中，您將了解如何建立復原服務保存庫、定義備份原則，以及同時備份多部虛擬機器。
ms.date: 07/26/2020
ms.topic: tutorial
ms.custom: mvc
ms.openlocfilehash: 7a8849bba51939a0c40cba81925e1b4b4a6e3826
ms.sourcegitcommit: f353fe5acd9698aa31631f38dd32790d889b4dbb
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/29/2020
ms.locfileid: "87372547"
---
# <a name="use-azure-portal-to-back-up-multiple-virtual-machines"></a>使用 Azure 入口網站備份多部虛擬機器

當您使用 Azure 備份資料時，該資料會儲存在稱為復原服務保存庫的 Azure 資源中。 大部分 Azure 服務的 [設定] 功能表都提供復原服務保存庫資源。 將復原服務保存庫整合至大部分 Azure 服務 [設定] 功能表的好處，是為備份資料提供便利性。 不過，個別處理您企業中的每個資料庫或虛擬機器實在很費時。 如果想要將所有虛擬機器的資料備份在一個部門或一個位置，該怎麼辦？ 只要建立備份原則，並將該原則套用至所需的虛擬機器，即可輕鬆備份多部虛擬機器。 本教學課程說明如何：

> [!div class="checklist"]
>
> * 建立復原服務保存庫
> * 定義備份原則
> * 套用備份原則以保護多部虛擬機器
> * 觸發受保護虛擬機器的隨選備份作業

## <a name="sign-in-to-the-azure-portal"></a>登入 Azure 入口網站

登入 [Azure 入口網站](https://portal.azure.com/)。

## <a name="create-a-recovery-services-vault"></a>建立復原服務保存庫

復原服務保存庫包含備份資料，以及套用至受保護虛擬機器的備份原則。 備份虛擬機器是本機的程序。 您無法將某個位置的虛擬機器備份到另一個位置的復原服務保存庫。 因此，對於每個具有要備份之虛擬機器的 Azure 位置，該位置至少必須有一個復原服務保存庫。

1. 在左側功能表上，選取 [所有服務]  。

    ![選取所有服務](./media/tutorial-backup-vm-at-scale/click-all-services.png)

1. 在 [所有服務]  對話方塊中，輸入 *Recovery Services*。 資源清單會根據您的輸入進行篩選。 在資源清單中，選取 [復原服務保存庫]  。

    ![輸入並選擇復原服務保存庫](./media/tutorial-backup-vm-at-scale/all-services.png)

    隨即會在訂用帳戶中出現 [復原服務保存庫] 清單。

1. 在 [復原服務保存庫]  儀表板上，選取 [新增]  。

    ![新增復原服務保存庫](./media/tutorial-backup-vm-at-scale/add-button-create-vault.png)

1. 在 [復原服務保存庫] 功能表中，

    * 在 [名稱] 中鍵入 *myRecoveryServicesVault*。
    * 目前的訂用帳戶識別碼會出現在 [訂用帳戶] 中。 如果您有其他的訂用帳戶，可以為新的保存庫選擇其他訂用帳戶。
    * 針對**資源群組**：選取 [使用現有] 並選擇 [myResourceGroup]。 如果 [myResourceGroup] 不存在，請選取 [新建] 並鍵入 *myResourceGroup*。
    * 從 [位置] 下拉式功能表中，選擇 [西歐]。

    ![復原服務保存庫值](./media/tutorial-backup-vm-at-scale/review-and-create.png)

    復原服務保存庫必須和受保護的虛擬機器位在相同地區。 如果您在多個區域中有虛擬機器，請在每個區域中建立復原服務保存庫。 本教學課程會在「西歐」建立復原服務保存庫，因為這是 *myVM* (使用快速入門步驟建立之虛擬機器) 的建立位置。

1. 當您準備好建立復原服務保存庫時，選取 [建立]。

    ![建立復原服務保存庫](./media/tutorial-backup-vm-at-scale/click-create-button.png)

1. 建立復原服務保存庫可能需要一些時間。 請監視入口網站右上角 [通知] 區域中的狀態通知。 保存庫建立之後，就可以在 [復原服務保存庫] 的清單中看到。 如果您沒有看到保存庫，請選取 [重新整理]。

     ![重新整理備份保存庫的清單](./media/tutorial-backup-vm-at-scale/refresh-button.png)

當您建立復原服務保存庫時，根據預設，保存庫有異地備援儲存體。 為提供資料復原功能，異地備援儲存體會跨越兩個 Azure 區域多次複寫資料。

## <a name="set-backup-policy-to-protect-vms"></a>設定備份原則以保護 VM

建立復原服務保存庫之後，下一個步驟是針對資料類型設定保存庫，並設定備份原則。 備份原則是復原點擷取頻率和時間的排程。 原則也會包含復原點的保留範圍。 此教學課程假設您的企業是集合飯店、運動場以及餐廳和販賣部的運動休閒中心，而您要保護虛擬機器上的資料。 下列步驟會建立財務資料的備份原則。

1. 在復原服務保存庫清單中，選取 [myRecoveryServicesVault] 來開啟其儀表板。

   ![開啟 [案例] 功能表](./media/tutorial-backup-vm-at-scale/open-vault-from-list.png)

1. 在 [保存庫儀表板] 功能表中，選取 [備份] 以開啟 [備份] 功能表。

1. 在 [備份目標] 功能表中，從 [您的工作負載在何處執行] 下拉式功能表中，選取 *Azure*。 從 [您要備份什麼] 下拉式清單，選擇 [虛擬機器]，然後選取 [備份]。

    這些動作會準備好復原服務保存庫以與虛擬機器進行互動。 復原服務保存庫的預設原則，每天都會建立還原點，並保留 30 天。

    ![備份目標](./media/tutorial-backup-vm-at-scale/backup-goal.png)

1. 若要建立新的原則，請在 [備份原則] 功能表上，從 [選擇備份原則] 下拉式功能表中，選取 [建立新原則]。

    ![建立新原則](./media/tutorial-backup-vm-at-scale/create-new-policy.png)

1. [備份原則] 窗格隨即開啟。 填寫下列詳細資料：
   * 針對 [原則名稱]，輸入「財務」。 輸入備份原則的下列變更：
   * 針對 [備份頻率]將時區設定為 [美國中部時間]。 因為運動休閒中心位在德州，所以業主希望使用本地時間。 [備份頻率] 保持設定為 [每天] 上午 3:30 執行。
   * 針對 [每日備份點保留期]，將期限設定為 90 天。
   * 針對 [每週備份點保留期]，還原點使用 [星期一] 並保留 52 週。
   * 針對 [每月備份點保留期]，還原點使用每月第一個星期日並保留 36 個月。
   * 取消選取 [每年備份點保留期] 選項。 財務部主管不希望資料保留期限超過 36 個月。
   * 選取 [確定] 以建立備份原則。

     ![備份原則設定](./media/tutorial-backup-vm-at-scale/set-new-policy.png)

     建立備份原則之後，再建立該原則與虛擬機器的關聯性。

1. 在 [虛擬機器] 下，選取 [新增]。

     ![新增虛擬機器](./media/tutorial-backup-vm-at-scale/add-virtual-machines.png)

1. [選取虛擬機器] 窗格隨即開啟。 選取 [myVM]，然後選取 [確定] 將備份原則部署到虛擬機器。

    位於相同位置且未與備份原則相關聯的所有虛擬機器都會出現。 已選取 *myVMH1* 和 *myVMR1* 要與 *Finance* 原則建立關聯性。

    ![選擇要保護的 VM](./media/tutorial-backup-vm-at-scale/choose-vm-to-protect.png)

1. 選擇虛擬機器之後，請選取 [啟用備份]。

    完成部署之後，您將收到已順利完成部署的通知。

## <a name="initial-backup"></a>初始備份

您已啟用復原服務保存庫的備份，但尚未建立初始備份。 災害復原的最佳做法是觸發第一個備份，以保護您的資料。

執行隨選備份作業：

1. 在保存庫儀表板上，選取 [備份項目] 下的 [3]，開啟 [備份項目] 功能表。

    ![備份項目](./media/tutorial-backup-vm-at-scale/tutorial-vm-back-up-now.png)

    [備份項目] 功能表隨即開啟。

1. 在 [備份項目] 功能表上，選取 [Azure 虛擬機器] 開啟與保存庫相關聯的虛擬機器清單。

    ![虛擬機器清單](./media/tutorial-backup-vm-at-scale/three-virtual-machines.png)

1. [備份項目] 清單隨即開啟。

    ![備份作業已觸發](./media/tutorial-backup-vm-at-scale/initial-backup-context-menu.png)

1. 在 [備份項目] 清單上，選取省略符號 **...** 以開啟操作功能表。

1. 在操作功能表上，選取 [立即備份]。

    ![捷徑功能表](./media/tutorial-backup-vm-at-scale/context-menu.png)

    [立即備份] 功能表隨即開啟。

1. 在 [立即備份] 功能表上，輸入保留復原點的最後一天，然後選取 [確定]。

    ![設定保留 [立即備份] 復原點的最後一天](./media/tutorial-backup-vm-at-scale/backup-now-short.png)

    部署通知可讓您知道已觸發備份工作，而且您可以在 [備份工作] 頁面上監視作業的進度。 視您虛擬機器的大小而定，建立初始備份可能需要花一點時間。

    初始備份作業完成後，您會在 [備份作業] 功能表中看到它的狀態。 隨選備份作業已建立 *myVM* 的初始還原點。 如果您想要備份其他虛擬機器，請為每部虛擬機器重複這些步驟。

    ![備份工作圖格](./media/tutorial-backup-vm-at-scale/initial-backup-complete.png)

## <a name="clean-up-resources"></a>清除資源

如果您打算繼續進行後續的教學課程，請勿清除在此教學課程中建立的資源。 如果您不打算繼續，請使用下列步驟，在 Azure 入口網站中刪除此教學課程所建立的所有資源。

1. 在 [myRecoveryServicesVault] 儀表板上，選取 [備份項目] 下的 [3]，開啟 [備份項目] 功能表。

    ![備份項目](./media/tutorial-backup-vm-at-scale/tutorial-vm-back-up-now.png)

1. 在 [備份項目] 功能表上，選取 [Azure 虛擬機器] 開啟與保存庫相關聯的虛擬機器清單。

    ![虛擬機器清單](./media/tutorial-backup-vm-at-scale/three-virtual-machines.png)

    [備份項目] 清單隨即開啟。

1. 在 [備份項目] 功能表中，選取省略符號開啟操作功能表。

    ![捷徑功能表](./media/tutorial-backup-vm-at-scale/context-menu-to-delete-vm.png)

1. 在操作功能表上選取 [停止備份] 開啟 [停止備份] 功能表。

    ![[停止備份] 功能表](./media/tutorial-backup-vm-at-scale/context-menu-for-delete.png)

1. 在 [停止備份] 功能表上，選取上方的下拉式功能表，然後選擇 [刪除備份資料]。

1. 在 [Type the name of the Backup item] \(鍵入備份項目的名稱) 對話方塊中鍵入 *myVM*。

1. 確認備份項目後 (核取記號會出現)，即啟用 [停止備份] 按鈕。 選取 [停止備份] 以停止原則並刪除還原點。

    ![選取 [停止備份] 刪除保存庫](./media/tutorial-backup-vm-at-scale/provide-reason-for-delete.png)

    >[!NOTE]
    >已刪除的項目會以虛刪除狀態保留 14 天。 只有在該期間之後，才能刪除保存庫。 如需詳細資訊，請參閱[刪除 Azure 備份復原服務保存庫](backup-azure-delete-vault.md)。

1. 當保存庫中不再有任何項目時，選取 [刪除]。

    ![選取 [停止備份] 刪除保存庫](./media/tutorial-backup-vm-at-scale/deleting-the-vault.png)

    刪除保存庫後，您將會回到復原服務保存庫的清單。

## <a name="next-steps"></a>後續步驟

在本教學課程中，您已使用 Azure 入口網站來：

> [!div class="checklist"]
>
> * 建立復原服務保存庫
> * 設定保存庫以保護虛擬機器
> * 建立自訂的備份和保留原則
> * 指派原則以保護多部虛擬機器
> * 觸發虛擬機器的隨選備份

繼續下一個教學課程，從磁碟還原 Azure 虛擬機器。

> [!div class="nextstepaction"]
> [使用 CLI 還原 VM](./tutorial-restore-disk.md)
