---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: 89f7be31514f0b78c3bfb3efd6e6aca14658d5cd
ms.sourcegitcommit: 6a902230296a78da21fbc68c365698709c579093
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/05/2020
ms.locfileid: "93376095"
---
#### <a name="to-create-a-manual-backup"></a>建立手動備份

1. 移至 StorSimple 裝置管理員服務，然後按一下 [裝置]。 從表格式的裝置清單中，選取您的裝置。 移至 [設定] > [管理] > [備份原則]。

2. 此 [備份原則] 刀鋒視窗會以表格式的格式將所有備份原則列出，包括您想要備份的磁碟區原則。 選取與您想要備份之磁碟區相關聯的原則，並以滑鼠右鍵按一下來叫用內容功能表。 從下拉式清單選取 [立即備份]。

    ![建立手動備份](./media/storsimple-8000-create-manual-backup/createmanualbu1.png)

3. 在 [立即備份] 刀鋒視窗中，執行下列步驟︰

    1. 從下拉式清單中選擇適當的 **快照集類型** ︰ **本機** 快照集或 **雲端** 快照集。 選取本機快照集可快速備份或還原，選取雲端快照集則可進行資料復原。

        ![建立手動備份 2](./media/storsimple-8000-create-manual-backup/createmanualbu2.png)

    2. 按一下 [確認] 可啟動建立快照集的作業。 成功建立作業之後，您就會在頁面頂端看到通知。

        ![建立手動備份3](./media/storsimple-8000-create-manual-backup/createmanualbu4.png)

    3. 若要監視作業，請按一下 [通知]。 這會帶您前往 [作業] 刀鋒視窗，您可以在當中檢視作業進度。


5. 備份工作完成後，請移至 [備份類別目錄] 索引標籤。

6. 將篩選選取項目設定為適當的裝置、備份原則和時間範圍。 備份應該會出現在類別目錄中顯示的備份組清單中。

