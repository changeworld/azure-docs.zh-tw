---
title: 在 Azure 入口網站中備份 Azure 檔案共用
description: 瞭解如何使用 Azure 入口網站來備份復原服務保存庫中的 Azure 檔案共用
ms.topic: conceptual
ms.date: 01/20/2020
ms.openlocfilehash: 40b966d719360570a472b17fbf733aa4c8795b4d
ms.sourcegitcommit: afa1411c3fb2084cccc4262860aab4f0b5c994ef
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/23/2020
ms.locfileid: "88757790"
---
# <a name="back-up-azure-file-shares"></a>備份 Azure 檔案共用

本文說明如何從 Azure 入口網站備份 [Azure 檔案共用](../storage/files/storage-files-introduction.md) 。

在本文中，您將學會如何：

* 建立復原服務保存庫。
* 從復原服務保存庫設定備份
* 從 [檔案共用] 窗格設定備份
* 執行隨選備份作業以建立還原點

## <a name="prerequisites"></a>Prerequisites

* [瞭解](azure-file-share-backup-overview.md) 以 Azure 檔案共用快照集為基礎的備份解決方案。
* 確定檔案共用存在於其中一種 [支援的儲存體帳戶類型](azure-file-share-support-matrix.md)中。
* 在與裝載檔案共用的儲存體帳戶相同的區域中，識別或建立復原 [服務保存庫](#create-a-recovery-services-vault) 。

[!INCLUDE [How to create a Recovery Services vault](../../includes/backup-create-rs-vault.md)]

## <a name="configure-backup-from-the-recovery-services-vault"></a>從復原服務保存庫設定備份

下列步驟說明如何從 [復原服務保存庫] 窗格，設定多個檔案共用的備份：

1. 在 [Azure 入口網站](https://portal.azure.com/)中，開啟您想要用來設定檔案共用備份的復原服務保存庫。

1. 在 [復原 **服務保存庫** ] 窗格中，選取頂端功能表中的 [ **+ 備份** ]。

   ![復原服務保存庫](./media/backup-afs/recovery-services-vault.png)

    1. 在 [**備份目標**] 窗格中，從下拉式清單中選取 [ **azure** ] 選項，以設定**您的工作負載在何處執行？** 到**azure** 。

          ![選擇 Azure 作為工作負載](./media/backup-afs/backup-goal.png)

    2. 在 [ **您要備份什麼？**] 中，從下拉式清單中選取 [ **Azure 檔案共用** ]。

          ![選取 Azure 檔案共用](./media/backup-afs/select-azure-file-share.png)

    3. 選取 [ **備份** ]，在保存庫中註冊 Azure 檔案共用延伸模組。

          ![選取備份以將 Azure 檔案共用與保存庫建立關聯](./media/backup-afs/register-extension.png)

1. 選取 [ **備份**] 之後，[ **備份** ] 窗格隨即開啟。 若要選取裝載您要保護之檔案共用的儲存體帳戶，請選取 [**儲存體帳戶**] 文字方塊下方的 [**選取**連結文字]。

   ![選擇 [選取] 連結](./media/backup-afs/choose-select-link.png)

1. [ **選取儲存體帳戶] 窗格** 會在右側開啟，並列出一組探索到的支援儲存體帳戶。 它們是與此保存庫相關聯，或存在於與保存庫相同的區域中，但尚未與任何復原服務保存庫相關聯。

1. 從探索到的儲存體帳戶清單中選取帳戶，然後選取 **[確定]**。

   ![從探索到的儲存體帳戶中選取](./media/backup-afs/select-discovered-storage-account.png)

1. 下一步是選取您想要備份的檔案共用。 在 [**要備份的檔案共用**] 區段中，選取 [**新增**] 按鈕。

   ![選取要備份的檔案共用](./media/backup-afs/select-file-shares-to-back-up.png)

1. [ **選取檔案共用** ] 內容窗格隨即在右側開啟。 Azure 會在儲存體帳戶中搜尋可備份的檔案共用。 如果您最近新增了檔案共用，但在清單中看不到它們，請等候一段時間讓檔案共用出現。

1. 從 [ **選取檔案共用** ] 清單中，選取您要備份的一或多個檔案共用。 選取 [確定]。

   ![選取檔案共用](./media/backup-afs/select-file-shares.png)

1. 若要為您的檔案共用選擇備份原則，您有三個選項：

   * 選擇預設原則。<br>
   此選項可讓您啟用將保留30天的每日備份。 如果您在保存庫中沒有現有的備份原則，則會開啟 [備份] 窗格，其中包含預設原則設定。 如果您想要選擇預設設定，可以直接選取 [ **啟用備份**]。

   * 建立新的原則 <br>

      1. 若要為您的檔案共用建立新的備份原則，請在 [ **備份原則** ] 區段中，選取下拉式清單下方的連結文字。<br>

         ![建立新原則](./media/backup-afs/create-new-policy.png)

      1. **備份原則**內容窗格隨即在右側開啟。 在文字方塊中指定原則名稱，然後根據您的需求選擇保留期限。 預設只會啟用每日保留選項。 如果您想要每週、每月或每年保留，請選取對應的核取方塊，並提供所需的保留值。

      1. 指定保留值和有效的原則名稱之後，請選取 **[確定]**。<br>

         ![提供原則名稱和保留值](./media/backup-afs/policy-name.png)

   * 選擇其中一個現有的備份原則 <br>

      若要選擇其中一個現有的備份原則來設定保護，請從 [ **備份原則** ] 下拉式清單中選取所需的原則。<br>

      ![選擇現有的原則](./media/backup-afs/choose-existing-policy.png)

1. 選取 [ **啟用備份** ] 以開始保護檔案共用。

   ![選擇 [啟用備份]](./media/backup-afs/enable-backup.png)

設定備份原則之後，就會在排定的時間取得檔案共用的快照集。 復原點也會在所選期間內保留。

>[!NOTE]
>Azure 備份現在支援每日/每週/每月/每年保留 Azure 檔案共用備份的原則。

## <a name="configure-backup-from-the-file-share-pane"></a>從 [檔案共用] 窗格設定備份

下列步驟說明如何從各自的 [檔案共用] 窗格，設定個別檔案共用的備份：

1. 在 [Azure 入口網站](https://portal.azure.com/)中，開啟裝載您要備份之檔案共用的儲存體帳戶。

1. 一旦在儲存體帳戶中，選取標示為 [檔案 **共用**] 的磚。 您也可以透過儲存體帳戶的瀏覽目錄至檔案 **共用** 。

   ![儲存體帳戶](./media/backup-afs/storage-account.png)

1. 在檔案共用清單中，您應該會看到所有存在於儲存體帳戶中的檔案共用。 選取您要備份的檔案共用。

   ![檔案共用清單](./media/backup-afs/file-shares-list.png)

1. 在 [檔案共用] 窗格的 [**作業**] 區段下，選取 [**備份**]。 [ **設定備份** ] 窗格將會載入右側。

   ![設定備份窗格](./media/backup-afs/configure-backup.png)

1. 針對 [復原服務保存庫] 選取專案，執行下列其中一項：

    * 如果您已經有保存庫，請選取 [ **選取現有** 的復原服務保存庫] 選項按鈕，然後從 [保存 **庫名稱** ] 下拉式功能表中選擇其中一個現有的保存庫。

       ![選取現有的保存庫](./media/backup-afs/select-existing-vault.png)

    * 如果您沒有保存庫，請選取 [ **建立新** 的復原服務保存庫] 選項按鈕。 指定保存庫名稱。 它是建立在與檔案共用相同的區域中。 根據預設，保存庫會建立在與檔案共用相同的資源群組中。 如果您想要選擇不同的資源群組，請選取 [**資源類型**] 下拉式清單下方的 [**建立新**連結]，然後指定資源群組的名稱。 選取 [確定]  以繼續操作。

       ![建立新的保存庫](./media/backup-afs/create-new-vault.png)

      >[!IMPORTANT]
      >如果儲存體帳戶已向保存庫註冊，或在裝載您要保護之檔案共用的儲存體帳戶內有少數受保護的共用，則會預先填入復原服務保存庫名稱，而您將無法在 [此處深入瞭解](backup-azure-files-faq.md#why-cant-i-change-the-vault-to-configure-backup-for-the-file-share)。

1. 在 [ **備份原則** ] 選取專案中，執行下列其中一項：

    * 保留預設原則。 它會排程保留30天的每日備份。

    * 從 [ **備份原則** ] 下拉式功能表中，選取現有的備份原則（如果有的話）。

       ![選擇備份原則](./media/backup-afs/choose-backup-policy.png)

    * 根據您的需求建立每日/每週/每月/每年保留的新原則。  

         1. 選取 [ **建立新的原則** 連結文字]。

         2. **備份原則**內容窗格隨即在右側開啟。 在文字方塊中指定原則名稱，然後根據您的需求選擇保留期限。 預設只會啟用每日保留選項。 如果您想要每週、每月或每年保留，請選取對應的核取方塊，並提供所需的保留值。

         3. 指定保留值和有效的原則名稱之後，請選取 **[確定]**。

            ![建立新的備份原則](./media/backup-afs/create-new-backup-policy.png)

1. 選取 [ **啟用備份** ] 以開始保護檔案共用。

   ![選取 [啟用備份]](./media/backup-afs/select-enable-backup.png)

1. 您可以在入口網站通知中追蹤設定進度，或監視您要用來保護檔案共用的保存庫下的備份工作。

   ![入口網站通知](./media/backup-afs/portal-notifications.png)

1. 完成設定備份作業之後，請在 [檔案共用] 窗格的 [**作業**] 區段底下選取 [**備份**]。 列出保存 **庫 Essentials** 的內容窗格將會載入右側。 您可以從該處觸發隨選備份和還原作業。

   ![保存庫基本概念](./media/backup-afs/vault-essentials.png)

## <a name="run-an-on-demand-backup-job"></a>執行隨選備份作業

有時候，您可能會想要在備份原則中排程的時間以外產生備份快照集或復原點。 產生隨選備份的常見原因是您在設定備份原則之後。 根據備份原則中的排程，在拍攝快照集之前，可能會是數小時或數天。 若要在備份原則參與之前保護您的資料，請起始隨選備份。 在您對檔案共用進行計畫的變更之前，通常需要建立隨選備份。

### <a name="from-the-recovery-services-vault"></a>從復原服務保存庫

1. 開啟您用來備份檔案共用的復原服務保存庫。 在 [**總覽**] 窗格中，選取 [**受保護的專案**] 區段下的 [**備份專案**]。

   ![選取備份專案](./media/backup-afs/backup-items.png)

1. 選取 [ **備份專案**] 之後，會出現一個新的窗格，列出所有 **備份管理類型** ，並顯示在 [ **總覽** ] 窗格的旁邊。

   ![備份管理類型清單](./media/backup-afs/backup-management-types.png)

1. 從 [ **備份管理類型** ] 清單中，選取 [ **Azure 儲存體 (Azure 檔案儲存體) **。 您會看到所有檔案共用的清單，以及使用此保存庫備份的對應儲存體帳戶。

   ![Azure 儲存體 (Azure 檔案儲存體) 備份專案](./media/backup-afs/azure-files-backup-items.png)

1. 從 Azure 檔案共用的清單中，選取您要的檔案共用。 [ **備份專案** ] 詳細資料隨即出現。 在 [ **備份專案** ] 功能表上，選取 [ **立即備份**]。 因為此備份作業為隨選，所以沒有與復原點相關聯的保留原則。

   ![選取立即備份](./media/backup-afs/backup-now.png)

1. [ **立即備份** ] 窗格隨即開啟。 指定您想要保留復原點的最後一天。 您最多可以保留10年的隨選備份。

   ![選擇保留日期](./media/backup-afs/retention-date.png)

1. 選取 **[確定]** 以確認執行的隨選備份作業。

1. 監視入口網站通知，以追蹤備份作業執行完成。 您可以在保存庫儀表板中監視作業進度。 選取**備份作業**  >  **進行中**。

### <a name="from-the-file-share-pane"></a>從 [檔案共用] 窗格

1. 開啟您想要進行隨選備份的檔案共用 **總覽** 窗格。

1. **在 [作業**] 區段底下選取 [**備份**]。 列出保存 **庫 Essentials** 的內容窗格將會載入右側。 選取 [ **立即備份** ] 以進行隨選備份。

   ![選取立即備份](./media/backup-afs/select-backup-now.png)

1. [ **立即備份** ] 窗格隨即開啟。 指定復原點的保留期。 您最多可以保留10年的隨選備份。

   ![保留備份日期](./media/backup-afs/retain-backup-date.png)

1. 選取 [確定] 來確認。

>[!NOTE]
>當您在對應的帳戶中設定任何檔案共用的保護時，Azure 備份鎖定儲存體帳戶。 這可防止意外刪除已備份檔案共用的儲存體帳戶。

## <a name="best-practices"></a>最佳作法

* 請勿刪除 Azure 備份所建立的快照集。 刪除快照集可能會導致遺失復原點和/或還原失敗。

* 請 Azure 備份，不要移除對儲存體帳戶所做的鎖定。 如果您刪除鎖定，您的儲存體帳戶將會很容易遭到意外刪除，如果刪除，您將會遺失快照或備份。

## <a name="next-steps"></a>後續步驟

了解如何：

* [還原 Azure 檔案共用](restore-afs.md)
* [管理 Azure 檔案共用備份](manage-afs-backup.md)
