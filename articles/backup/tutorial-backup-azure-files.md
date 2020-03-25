---
title: 教學課程 - 備份 Azure 檔案儲存體檔案共用
description: 在本教學課程中，您將了解如何使用 Azure 入口網站來設定復原服務保存庫，以及備份 Azure 檔案共用。
ms.date: 06/10/2019
ms.topic: tutorial
ms.openlocfilehash: ec9074a39f2ece7878c0c3ef828dc21748d0ab89
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/24/2020
ms.locfileid: "76293924"
---
# <a name="back-up-azure-file-shares-in-the-azure-portal"></a>在 Azure 入口網站中備份 Azure 檔案共用

本教學課程說明如何使用 Azure 入口網站來備份 [Azure 檔案共用](../storage/files/storage-files-introduction.md)。

在本指南中，您將了解如何：
> [!div class="checklist"]
>
> * 設定復原服務保存庫以備份 Azure 檔案
> * 執行隨選備份作業以建立還原點

## <a name="prerequisites"></a>Prerequisites

請先確定 Azure 檔案共用存在於其中一種[支援的儲存體帳戶類型](tutorial-backup-azure-files.md#limitations-for-azure-file-share-backup-during-preview)中，您才可以備份 Azure 檔案共用。 一旦確認這點，您即可保護您的檔案共用。

## <a name="limitations-for-azure-file-share-backup-during-preview"></a>預覽期間的 Azure 檔案共用備份限制

Azure 檔案共用的備份處於預覽狀態。 支援一般用途 v1 和一般用途 v2 儲存體帳戶中的 Azure 檔案共用。 Azure 檔案共用不支援下列備份案例︰

* 無法透過 CLI 使用 Azure 備份來保護 Azure 檔案服務。
* 每天的排程備份次數上限為 1 次。
* 每天的隨選備份次數上限為 4 次。
* 在儲存體帳戶上使用[資源鎖定](https://docs.microsoft.com/cli/azure/resource/lock?view=azure-cli-latest)，以防止在您的復原服務保存庫中意外刪除備份。
* 請勿刪除 Azure 備份所建立的快照集。 刪除快照集可能會導致遺失復原點和/或還原失敗。
* 請勿刪除由 Azure 備份所保護的檔案共用。 目前的解決方案會在刪除檔案共用後，刪除 Azure 備份使用的所有快照集，並因此失去所有還原點

在儲存體帳戶中使用[區域備援儲存體](../storage/common/storage-redundancy-zrs.md) (ZRS) 複寫功能來備份 Azure 檔案共用，目前僅適用於美國中部 (CUS)、美國東部 (EUS)、美國東部 2 (EUS2)、北歐 (NE)、東南亞 (SEA)、西歐 (WE) 和西歐 2 (WUS2)。

## <a name="configuring-backup-for-an-azure-file-share"></a>設定 Azure 檔案共用備份

本教學課程假設您已經建立了 Azure 檔案共用。 若要備份 Azure 檔案共用：

1. 在與您的檔案共用相同的區域中建立復原服務保存庫。 如果您已經有保存庫，請開啟保存庫的 [概觀] 頁面，然後按一下 [備份]  。

    ![按一下保存庫 [概觀] 頁面中的 [備份]](./media/tutorial-backup-azure-files/overview-backup-page.png)

2. 在 [備份目標]  功能表中，從 [您想要備份什麼？]  ，選擇 Azure 檔案共用。

    ![選擇 Azure 檔案共用作為備份目標](./media/tutorial-backup-azure-files/choose-azure-fileshare-from-backup-goal.png)

3. 按一下 [備份]  ，將 Azure 檔案共用設定為備份至復原服務保存庫。

   ![按一下 [備份] 讓 Azure 檔案共用與保存庫建立關聯](./media/tutorial-backup-azure-files/set-backup-goal.png)

    一旦保存庫與 Azure 檔案共用建立關聯，[備份] 功能表隨即開啟並提示您選取儲存體帳戶。 此功能表會顯示您的保存庫所在區域中所有支援的儲存體帳戶，而且這些儲存體帳戶尚未與復原服務保存庫建立關聯。

   ![選取您的儲存體帳戶](./media/tutorial-backup-azure-files/list-of-storage-accounts.png)

4. 在 [儲存體帳戶] 清單中，選取帳戶並按一下 [確定]  。 Azure 會搜尋儲存體帳戶中可以備份的檔案共用。 如果您最近新增了檔案共用，但並未在清單中看到它們，請稍後片刻讓檔案共用出現。

   ![正在探索檔案共用](./media/tutorial-backup-azure-files/discover-file-shares.png)

5. 從 [檔案共用]  清單中，選取您要備份的一或多個檔案，然後按一下 [確定]  。

6. 選擇您的檔案共用之後，[備份] 功能表會切換至 [備份原則]  。 從這個功能表中選取現有的備份原則，或建立新的備份原則，然後按一下 [啟用備份]  。

   ![選取備份原則或建立新的原則](./media/tutorial-backup-azure-files/apply-backup-policy.png)

    建立備份原則之後，系統會在排定的時間產生檔案共用的快照集，並針對所選的期間保留復原點。

## <a name="create-an-on-demand-backup"></a>建立隨選備份

設定備份原則之後，您會想要建立隨選備份，以確保您的資料直到下一次排程備份之前都受到保護。

### <a name="to-create-an-on-demand-backup"></a>若要建立隨選備份：

1. 開啟包含檔案共用復原點的復原服務保存庫，然後按一下 [備份項目]  。 [備份項目] 類型清單隨即出現。

   ![備份項目清單](./media/tutorial-backup-azure-files/list-of-backup-items.png)

2. 從清單中，選取 [Azure 儲存體 (Azure 檔案服務)]  。 Azure 檔案共用的清單隨即出現。

   ![Azure 檔案共用的清單](./media/tutorial-backup-azure-files/list-of-azure-files-backup-items.png)

3. 從 Azure 檔案共用的清單中，選取所需的檔案共用。 所選檔案共用的 [備份項目] 功能表隨即開啟。

   ![所選檔案共用的 [備份項目] 功能表](./media/tutorial-backup-azure-files/backup-item-menu.png)

4. 從 [備份項目] 功能表，按一下 [立即備份]  。 因為這是隨選備份作業，所以沒有與復原點相關聯的保留原則。 [立即備份]  對話方塊隨即開啟。 指定您想要保留復原點的最後一天。

   ![選擇復原點保留期的日期](./media/tutorial-backup-azure-files/backup-now-menu.png)

## <a name="next-steps"></a>後續步驟

在本教學課程中，您已使用 Azure 入口網站來：

> [!div class="checklist"]
>
> * 設定復原服務保存庫以備份 Azure 檔案
> * 執行隨選備份作業以建立還原點

繼續到下一篇文章，從 Azure 檔案共用的備份進行還原。

> [!div class="nextstepaction"]
> [從 Azure 檔案共用的備份進行還原](restore-afs.md)
