---
title: 還原 Azure 檔案共用
description: 瞭解如何使用 Azure 入口網站從 Azure 備份所建立的還原點還原整個檔案共用或特定檔案。
ms.topic: conceptual
ms.date: 01/12/2020
ms.openlocfilehash: bb846c4615377259a1c17653cc26e96f37dc32ec
ms.sourcegitcommit: e2b36c60a53904ecf3b99b3f1d36be00fbde24fb
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/24/2020
ms.locfileid: "88762816"
---
# <a name="restore-azure-file-shares"></a>還原 Azure 檔案共用

本文說明如何使用 Azure 入口網站，從 [Azure 備份](./backup-overview.md)所建立的還原點還原整個檔案共用或特定檔案。

在本文中，您將學會如何：

* 還原完整的 Azure 檔案共用。
* 還原個別的檔案或資料夾。
* 追蹤還原作業的狀態。

## <a name="steps-to-perform-a-restore-operation"></a>執行還原作業的步驟

若要執行還原作業，請遵循下列步驟。

### <a name="select-the-file-share-to-restore"></a>選取要還原的檔案共用

1. 在 [Azure 入口網站](https://portal.azure.com/)中，開啟您用來設定檔案共用備份的復原服務保存庫。

1. 在 [總覽] 窗格中，選取 [**受保護的專案**] 區段下的 [**備份專案**]。

    ![選取備份專案](./media/restore-afs/backup-items.png)

1. 選取 **備份專案**之後，會在 [總覽] 窗格旁邊開啟一個新的窗格，其中列出所有備份管理類型。

    ![備份管理類型](./media/restore-afs/backup-management.png)

1. 在 [ **備份專案** ] 窗格的 [ **備份管理類型**] 下，選取 [ **Azure 儲存體 (Azure 檔案儲存體) **。 您會看到所有檔案共用的清單，以及使用此保存庫備份的對應儲存體帳戶清單。

    ![所有檔案共用的清單](./media/restore-afs/file-shares.png)

1. 從 Azure 檔案共用的清單中，選取您要執行還原操作的檔案共用。

### <a name="full-share-recovery"></a>完整共用修復

您可以使用此還原選項來還原原始位置或替代位置中的完整檔案共用。

1. 在 [[選取要還原](#select-the-file-share-to-restore)的檔案共用] 區段的步驟5中選取要還原的檔案共用之後，在 [**備份專案**] 窗格中選取 [**還原共用**] 選項。

   ![選取還原共用](./media/restore-afs/restore-share.png)

1. 選取 [ **還原共用**] 後，[ **還原** ] 窗格隨即開啟。 若要選取您想要用來執行還原作業的還原點，請按一下 [**還原點**] 文字方塊下方的 [**選取**連結文字]。

    ![按一下 [選取]，以選取還原點](./media/restore-afs/select-restore-point.png)

1. [ **選取還原點** ] 內容窗格隨即在右側開啟，並列出所選檔案共用可用的還原點。 選取您要用來執行還原操作的還原點，然後選取 **[確定]**。

    ![選取還原點](./media/restore-afs/restore-point.png)

    >[!NOTE]
    >依預設，[ **選取還原點** ] 窗格會列出過去30天內的還原點。 如果您想要查看特定期間內建立的還原點，請選取適當的**開始時間**和**結束時間**來指定範圍，然後按一下 [重新整理 **] 按鈕。**

1. 下一步是選擇 **還原位置**。 在 [復原 **目的地** ] 區段中，指定還原資料的位置或方式。 使用切換按鈕，選取下列兩個選項的其中一個：

    * **原始位置**：將完整的檔案共用還原至與原始來源相同的位置。
    * **替代位置**：將完整的檔案共用還原至替代位置，並保留原始的檔案共用。

#### <a name="restore-to-the-original-location-full-share-recovery"></a> (完整的共用復原還原至原始位置) 

1. 選取 [ **原始位置** ] 作為復原 **目的地**，然後從 [ **衝突** 時] 下拉式清單中選擇適當的選項，以選取是否要略過或覆寫發生衝突。

1. 選取 [ **還原** ] 以開始還原作業。

    ![選取 [還原] 以開始](./media/restore-afs/click-restore.png)

#### <a name="restore-to-an-alternate-location-full-share-recovery"></a>還原至替代位置， (完整的共用復原) 

1. 選取 [ **替代位置** ] 作為復原 **目的地**。
1. 從 [ **儲存體帳戶** ] 下拉式清單中，選取您要還原備份內容的目的地儲存體帳戶。
1. [ **選取檔案共用** ] 下拉式清單會顯示您在步驟2中選取的儲存體帳戶中存在的檔案共用。 選取您要還原備份內容的檔案共用。
1. 在 [ **資料夾名稱** ] 方塊中，指定您想要在目的地檔案共用中建立的資料夾名稱和還原的內容。
1. 選取是否要在發生衝突時略過或覆寫。
1. 在 [所有] 方塊中輸入適當的值之後，請選取 [ **還原** ] 開始還原作業。

    ![選取替代位置](./media/restore-afs/alternate-location.png)

### <a name="item-level-recovery"></a>項目層級復原

您可以使用此還原選項來還原原始位置或替代位置中的個別檔案或資料夾。

1. 在 [[選取要還原](#select-the-file-share-to-restore)的檔案共用] 區段的步驟5中選取要還原的檔案共用之後，在 [**備份專案**] 窗格中**選取 [檔案復原] 選項。**

    ![選取檔案復原](./media/restore-afs/file-recovery.png)

1. **選取 [** 檔案復原] 之後，[**還原**] 窗格隨即開啟。 若要選取您想要用來執行還原作業的還原點，請按一下 [**還原點**] 文字方塊下方的 [**選取**連結文字]。

    ![按一下 [選取]，以選取還原點](./media/restore-afs/select-restore-point.png)

1. [ **選取還原點** ] 內容窗格隨即在右側開啟，並列出所選檔案共用可用的還原點。 選取您要用來執行還原操作的還原點，然後選取 **[確定]**。

    ![選取還原點](./media/restore-afs/restore-point.png)

1. 下一步是選擇 **還原位置**。 在 [復原 **目的地** ] 區段中，指定還原資料的位置或方式。 使用切換按鈕，選取下列兩個選項的其中一個：

    * **原始位置**：將選取的檔案或資料夾還原至與原始來源相同的檔案共用。
    * **替代位置**：將選取的檔案或資料夾還原到替代位置，並保留原始的檔案共用內容。

#### <a name="restore-to-the-original-location-item-level-recovery"></a> (專案層級復原還原至原始位置) 

1. 選取 [ **原始位置** ] 作為復原 **目的地**，然後從 [ **衝突** 時] 下拉式清單中選擇適當的選項，以選取是否要略過或覆寫是否發生衝突。

    ![專案層級復原的原始位置](./media/restore-afs/original-location-item-level.png)

1. 若要選取您想要還原的檔案或資料夾，請按一下 [ **新增** 檔案] 按鈕。 這會開啟右側的內容窗格，顯示您選取要還原的檔案共用復原點內容。

    ![選擇 [新增檔案]](./media/restore-afs/add-file.png)

1. 選取對應至您想要還原之檔案或資料夾的核取方塊，然後選擇 [ **選取**]。

    ![選取檔案或資料夾](./media/restore-afs/select-file-folder.png)

1. 重複步驟2到4，以選取多個要還原的檔案或資料夾。
1. 選取您要還原的所有專案之後，請選取 [ **還原** ] 以開始還原作業。

    ![選取 [還原] 以開始](./media/restore-afs/click-restore.png)

#### <a name="restore-to-an-alternate-location-item-level-recovery"></a> (專案層級復原還原至替代位置) 

1. 選取 [ **替代位置** ] 作為復原 **目的地**。
1. 從 [ **儲存體帳戶** ] 下拉式清單中，選取您要還原備份內容的目的地儲存體帳戶。
1. [ **選取檔案共用** ] 下拉式清單會顯示您在步驟2中選取的儲存體帳戶中存在的檔案共用。 選取您要還原備份內容的檔案共用。
1. 在 [ **資料夾名稱** ] 方塊中，指定您想要在目的地檔案共用中建立的資料夾名稱和還原的內容。
1. 選取是否要在發生衝突時略過或覆寫。
1. 若要選取您想要還原的檔案或資料夾，請按一下 [ **新增** 檔案] 按鈕。 這會在右側開啟內容窗格，顯示您選取要還原的檔案共用復原點內容。

    ![選取要還原至替代位置的專案](./media/restore-afs/restore-to-alternate-location.png)

1. 選取對應至您想要還原之檔案或資料夾的核取方塊，然後選擇 [ **選取**]。

    ![選取復原目的地](./media/restore-afs/recovery-destination.png)

1. 重複步驟6到步驟8，以選取多個要還原的檔案或資料夾。
1. 選取您要還原的所有專案之後，請選取 [ **還原** ] 以開始還原作業。

    ![選取所有檔案之後，請選取 [確定]](./media/restore-afs/after-selecting-all-items.png)

## <a name="track-a-restore-operation"></a>追蹤還原作業

在觸發還原作業之後，備份服務會建立用於追蹤的作業。 Azure 備份會在入口網站中顯示作業的相關通知。 若要查看作業的作業，請選取 [通知] 超連結。

![選取通知超連結](./media/restore-afs/notifications-link.png)

您也可以從復原服務保存庫監視還原進度：

1. 開啟復原服務保存庫，以從中觸發還原作業。
1. 在 [總覽] 窗格中，選取 [**監視**] 區段下的 [**備份作業**]，以查看針對不同工作負載執行的作業狀態。

    ![選取備份作業](./media/restore-afs/backup-jobs.png)

1. 選取對應至檔案共用的工作負載名稱，以查看有關還原作業的詳細資料，例如 **傳送的資料** 和 **還原的檔案數目**。

    ![查看還原的詳細資料](./media/restore-afs/restore-details.png)

## <a name="next-steps"></a>下一步

* 瞭解如何 [管理 Azure 檔案共用備份](manage-afs-backup.md)。
