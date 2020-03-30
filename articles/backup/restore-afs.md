---
title: 還原 Azure 檔共用
description: 瞭解如何使用 Azure 門戶從 Azure 備份創建的還原點還原整個檔共用或特定檔。
ms.topic: conceptual
ms.date: 01/12/2020
ms.openlocfilehash: c22078ebd89f5f6f8299e1424d4d9e21edce8b92
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "77586824"
---
# <a name="restore-azure-file-shares"></a>還原 Azure 檔共用

本文介紹如何使用 Azure 門戶從[Azure 備份](https://docs.microsoft.com/azure/backup/backup-overview)創建的還原點還原整個檔共用或特定檔。

在本文中，您將學會如何：

* 還原完整的 Azure 檔共用。
* 還原單個檔或資料夾。
* 跟蹤還原操作狀態。

## <a name="steps-to-perform-a-restore-operation"></a>執行還原操作的步驟

要執行還原操作，請按照以下步驟操作。

### <a name="select-the-file-share-to-restore"></a>選擇要還原的檔共用

1. 在[Azure 門戶](https://portal.azure.com/)中，打開用於設定檔共用備份的恢復服務保存庫。

1. 在"概述"窗格中，在 **"受保護專案**"部分下選擇 **"備份專案**"。

    ![選擇備份專案](./media/restore-afs/backup-items.png)

1. 選擇 **"備份專案**"後，將在"概覽"窗格旁邊打開一個列出所有備份管理類型的新窗格。

    ![備份管理類型](./media/restore-afs/backup-management.png)

1. 在 **"備份專案"** 窗格中，在 **"備份管理類型"** 下，選擇**Azure 存儲（Azure 檔）。** 您將看到使用此保存庫備份的所有檔共用及其相應存儲帳戶的清單。

    ![所有檔共用的清單](./media/restore-afs/file-shares.png)

1. 從 Azure 檔共用清單中，選擇要為其執行還原操作的檔共用。

### <a name="full-share-recovery"></a>完全共用恢復

您可以使用此還原選項在原始位置或備用位置還原完整的檔共用。

1. 選擇 **"備份專案**"窗格中的 **"還原共用**"選項，該選項在"[選擇要還原的檔共用](#select-the-file-share-to-restore)"步驟 5 中選擇要還原的檔共用後顯示。

   ![選擇還原共用](./media/restore-afs/restore-share.png)

1. 選擇 **"還原共用**"後，"**還原**"窗格將打開 **"還原點"** 功能表，該功能表顯示可用於所選檔共用的還原點清單。

1. 選擇要用於執行還原操作的還原點，然後選擇 **"確定**"。

    ![選取還原點](./media/restore-afs/restore-point.png)

1. 選擇 **"確定"** 後，"**還原**窗格"功能表將切換到 **"還原位置**"。 在 **"還原位置**"中，指定還原資料的位置或方式。 選擇以下兩個選項之一：

    * **原始位置**：將完整的檔共用還原到與原始源相同的位置。
    * **備用位置**：將完整的檔共用還原到備用位置，並保持原始檔案共用原樣。

#### <a name="restore-to-the-original-location"></a>還原到原始位置

1. 選擇**原始位置**作為**復原目標**，並選擇是否跳過或覆蓋是否存在衝突。 進行適當的選擇後，選擇 **"確定**"。

    ![選擇原始位置](./media/restore-afs/original-location.png)

1. 選擇 **"還原**"以啟動還原操作。

    ![選擇"還原"以啟動](./media/restore-afs/click-restore.png)

#### <a name="restore-to-an-alternate-location"></a>還原至替代位置

1. 選擇**備用位置**作為**復原目標**。
1. 選擇要從 **"存儲帳戶**"下拉清單中還原備份內容的目標存儲帳戶。
1. **"選擇檔共用**"下拉清單顯示您在步驟 2 中選擇的存儲帳戶中的檔共用。 選擇要還原備份內容的檔共用。
1. 在"**資料夾名稱"** 框中，指定要在目的檔案共用中與還原的內容創建的資料夾名稱。
1. 如果存在衝突，請選擇是跳過還是覆蓋。
1. 在所有框中輸入適當的值後，選擇 **"確定**"。

    ![選擇備用位置](./media/restore-afs/alternate-location.png)

1. 選擇 **"還原**"以啟動還原操作。

    ![選擇"還原"以啟動](./media/restore-afs/click-restore.png)

### <a name="item-level-recovery"></a>項目層級復原

您可以使用此還原選項還原原始位置或備用位置中的單個檔或資料夾。

1. 選擇"**備份專案"** 窗格中**的檔案修復**選項，該選項在選擇要還原的檔共用後顯示[。"選擇要還原的檔共用](#select-the-file-share-to-restore)"步驟 5 中。

    ![選擇檔案修復](./media/restore-afs/file-recovery.png)

1. 選擇 **"檔案修復**"後，"**還原**"窗格將打開 **"還原點"** 功能表，該功能表顯示可用於所選檔共用的還原點清單。

1. 選擇要用於執行還原操作的還原點，然後選擇 **"確定**"。

    ![選取還原點](./media/restore-afs/restore-point.png)

1. 選擇 **"確定"** 後，還原窗格功能表將切換到 **"還原位置**"。 在 **"還原位置**"中，指定還原資料的位置或方式。 選擇以下兩個選項之一：

    * **原始位置**：將選定的檔或資料夾還原到與原始源相同的檔共用。
    * **備用位置**：將選定的檔或資料夾還原到備用位置，並保持原始檔案共用內容原樣。

#### <a name="restore-to-the-original-location"></a>還原到原始位置

1. 選擇**原始位置**作為**復原目標**，並選擇是否跳過或覆蓋是否存在衝突。

    ![專案級恢復的原始位置](./media/restore-afs/original-location-item-level.png)

1. 選擇 **"選擇檔"** 以選擇要還原的檔或資料夾。

    ![選擇"選擇檔"](./media/restore-afs/select-file.png)

1. 選擇 **"選擇檔"** 後，檔共用窗格將顯示選擇要還原的檔共用復原點的內容。

1. 選擇與要還原的檔或資料夾對應的核取方塊，然後**選擇**。

    ![選擇檔或資料夾](./media/restore-afs/select-file-folder.png)

1. 重複步驟 2 到 4 以選擇要還原的多個檔或資料夾。
1. 選擇要還原的所有專案後，選擇 **"確定**"。

    ![選擇要還原的所有專案後，選擇"確定"](./media/restore-afs/after-selecting-items.png)

1. 選擇 **"還原**"以啟動還原操作。

    ![選擇"還原"以啟動](./media/restore-afs/click-restore.png)

#### <a name="restore-to-an-alternate-location"></a>還原至替代位置

1. 選擇**備用位置**作為**復原目標**。
1. 選擇要從 **"存儲帳戶**"下拉清單中還原備份內容的目標存儲帳戶。
1. **"選擇檔共用**"下拉清單顯示您在步驟 2 中選擇的存儲帳戶中的檔共用。 選擇要還原備份內容的檔共用。
1. 在"**資料夾名稱"** 框中，指定要在目的檔案共用中與還原的內容創建的資料夾名稱。
1. 如果存在衝突，請選擇是跳過還是覆蓋。
1. 選擇 **"選擇檔"** 以選擇要還原的檔或資料夾。

    ![選擇要還原到備用位置的專案](./media/restore-afs/restore-to-alternate-location.png)

1. 選擇 **"選擇檔"** 時，檔共用窗格將顯示選擇要還原的檔共用復原點的內容。
1. 選擇與要還原的檔或資料夾對應的核取方塊，然後**選擇**。

    ![選擇復原目標](./media/restore-afs/recovery-destination.png)

1. 重複步驟 6 到 8 以選擇要還原的多個檔或資料夾。
1. 選擇要還原的所有專案後，選擇 **"確定**"。

    ![選擇所有檔後選擇"確定"](./media/restore-afs/after-selecting-all-items.png)

1. 選擇 **"還原**"以啟動還原操作。

## <a name="track-a-restore-operation"></a>跟蹤還原操作

在觸發還原作業之後，備份服務會建立用於追蹤的作業。 Azure 備份會在入口網站中顯示作業的相關通知。 要查看作業的操作，請選擇通知超連結。

![選擇通知超連結](./media/restore-afs/notifications-link.png)

您還可以監視恢復服務保存庫的還原進度：

1. 從觸發還原操作的位置打開恢復服務保存庫。
1. 在"概述"窗格中，在 **"監視**"部分下選擇 **"備份作業**"以查看針對不同工作負載運行的操作狀態。

    ![選擇備份作業](./media/restore-afs/backup-jobs.png)

1. 選擇與檔共用對應的工作負載名稱，以查看有關還原操作的更多詳細資訊，如 **"傳輸的資料**"和 **"還原檔數**"。

    ![查看還原的詳細資訊](./media/restore-afs/restore-details.png)

## <a name="next-steps"></a>後續步驟

* 瞭解如何管理[Azure 檔共用備份](manage-afs-backup.md)。
