---
title: 使用 MARS 復原磁碟區中的所有檔案
description: 瞭解如何使用 MARS 代理程式來復原磁碟區中的所有檔案。
ms.topic: conceptual
ms.date: 01/17/2021
ms.openlocfilehash: 44c12809fc94f78721ab1788cb352076dfebabe4
ms.sourcegitcommit: 8a74ab1beba4522367aef8cb39c92c1147d5ec13
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/20/2021
ms.locfileid: "98613467"
---
# <a name="restore-all-the-files-in-a-volume-using-the-mars-agent"></a>使用 MARS 代理程式復原磁碟區中的所有檔案

本文說明如何使用 Microsoft Azure 復原服務 (MARS) 代理程式中的 [復原資料]，來還原整個磁片區中的所有備份檔案。 您可以：

- 將磁片區中的所有備份檔案還原至從中取得備份的相同電腦。
- 將磁片區中的所有備份檔案還原至替代電腦。

>[!TIP]
>[磁碟區] 選項會復原指定磁碟區中的所有備份資料。 此選項可提供更快的傳送速率 (高達 40 MBps) ，而且建議用來復原大型資料或整個磁片區。
>
>[ **個別檔案及資料夾] 選項** 可讓您快速存取復原點資料。 它適合用來復原個別檔案，建議大小總計小於 80 GB。 在復原期間，它可提供高達 6 MBps 的傳輸或複製速度。

## <a name="volume-level-restore-to-the-same-machine"></a>磁片區層級還原至同一部電腦

下列步驟可協助您復原磁片區中的所有備份檔案：

1. 開啟 **Microsoft Azure 備份** 嵌入式管理單元。 如果您不知道快照安裝的位置，請在電腦或伺服器上搜尋 **Microsoft Azure 備份**。 傳統型應用程式應該會出現在搜尋結果中。

1. 按一下 [復原資料] 以啟動精靈。

    ![復原資料功能表](./media/restore-all-files-volume-mars/recover.png)

1. 在 [**開始使用**] 頁面上，若要將資料還原到相同的伺服器或電腦，請選取 [**此伺服器 (伺服器名稱**  >  **]) 下一步]**。

    ![開始使用頁面](./media/restore-all-files-volume-mars/same-machine-instant-restore.png)

1. 在 [**選取復原模式**] 頁面上 **，選擇 [**  >  **下一步]**。

    ![選取復原模式](./media/restore-all-files-volume-mars/select-recovery-mode.png)

1. 在 [ **選取磁片區和日期** ] 頁面上，選取您要還原的磁片區。

    在日曆上，選取復原點。 **粗體** 的日期表示至少有一個復原點可用。 如果單一日期內有多個復原點可用，可以從 [時間] 下拉式功能表選擇特定的復原點。

     ![選取磁碟區和日期](./media/restore-all-files-volume-mars/select-volume-and-date.png)

1. 在 [ **指定復原選項** ] 頁面上，設定還原行為。
    1. 選擇復原目的地：
        - **原始位置**：將資料還原至原始路徑。
        - **另一個位置**：指定還原資料的替代位置。
    1. 選擇備份中的 **專案已在復原目的地** 的行為：
        - **建立複本，讓您有兩個版本**：如果已有同名的檔案存在，復原點中的資料將會以複本的形式還原。 此複本將會以下列其中一種格式使用本機還原作業時間來當地語系化檔案名前置詞：
            - `YYYY-MM-DD HH-mm Copy of <original file name>`
            - `YYYY-MM-DD HH-mm Copy (n) of <original file name>`
        - **以復原的版本覆寫現有版本**：如果已有同名的檔案存在，則會將內容取代為復原點中的資料。
        - **不要復原已存在於復原目的地的專案**：如果已經有同名的檔案存在，則會略過該檔案。
    1. 如果應該使用復原點中的原始許可權還原檔案，請 **啟用還原存取控制清單 (ACL) 要復原之檔案或資料夾的許可權**。
        ![指定復原選項](./media/restore-all-files-volume-mars/specify-recovery-options.png)

1. 確認 **確認** 窗格上的復原詳細資料，然後選取 [ **復原**]。

    ![確認詳細資料](./media/restore-all-files-volume-mars/confirmation-details.png)

1. 在 [復原 **進度** ] 頁面上，監視復原工作的進度。 您也可以安全地關閉此嚮導，復原作業將會在背景中繼續進行。 您可以按兩下儀表板中的復原工作，再次查看進度。

## <a name="volume-level-restore-to-an-alternate-machine"></a>磁片區層級還原至替代電腦

下列步驟可協助您將磁片區中的所有備份檔案復原至替代電腦。 如果整個伺服器遺失，您可以使用這些步驟來復原 Azure 備份中的資料。

這些步驟包括以下術語：

- *來源電腦* – 建立備份且目前無法使用的原始電腦。
-  – 復原資料時的目標電腦。
- *範例保存庫* – 來源電腦和目標電腦註冊所在的復原服務保存庫。

> [!NOTE]
> 備份無法還原到執行舊版作業系統的目的電腦。 例如，從 Windows 7 電腦建立的備份可以還原至 Windows 7 (或更新版本) 的電腦。 從 Windows 10 電腦建立的備份無法還原至 Windows 7 電腦。

1. 在目標電腦上開啟 [Microsoft Azure 備份] 嵌入式管理單元。

1. 確定目標電腦和來源電腦均已註冊到相同的復原服務保存庫。

1. 選取 [復原資料] 以開啟 [復原資料精靈]。

    ![Azure 備份的螢幕擷取畫面，其中反白顯示覆原資料 (還原至替代電腦) ](./media/backup-azure-restore-windows-server/recover.png)

1. 在 [開始使用] 頁面中，選取 [其他伺服器]。

    ![[復原資料] Wizard 開始使用頁面的螢幕擷取畫面， (還原至替代電腦) ](./media/backup-azure-restore-windows-server/alternatemachine_gettingstarted_instantrestore.png)

1. 提供與範例保存庫相對應的保存庫認證檔，然後選取 [下一步]。

    如果保存庫認證檔 (或過期) ，請 [從 Azure 入口網站的範例保存庫下載新的保存庫認證](backup-azure-file-folder-backup-faq.md#where-can-i-download-the-vault-credentials-file) 檔。 在您提供有效的保存庫認證檔之後，就會顯示對應的備份保存庫名稱。

1. 在 [選取備份伺服器] 頁面中，從顯示的電腦清單選取來源電腦，並提供複雜密碼。 然後選取 [下一步]。

    ![[復原資料] Wizard 的 [選取備份伺服器] 頁面的螢幕擷取畫面， (還原至替代電腦) ](./media/backup-azure-restore-windows-server/alternatemachine_selectmachine_instantrestore.png)

1. 在 [**選取復原模式**] 頁面上 **，選擇 [**  >  **下一步]**。

    ![選取復原模式](./media/restore-all-files-volume-mars/select-recovery-mode.png)

1. 在 [ **選取磁片區和日期** ] 頁面上，選取您要還原的磁片區。

    在日曆上，選取復原點。 **粗體** 的日期表示至少有一個復原點可用。 如果單一日期內有多個復原點可用，可以從 [時間] 下拉式功能表選擇特定的復原點。

     ![選取磁碟區和日期](./media/restore-all-files-volume-mars/select-volume-and-date.png)

1. 在 [ **指定復原選項** ] 頁面上，設定還原行為。
    1. 選擇復原目的地：
        - **原始位置**：將資料還原至原始路徑。
        - **另一個位置**：指定還原資料的替代位置。
    1. 選擇備份中的 **專案已在復原目的地** 的行為：
        - **建立複本，讓您有兩個版本**：如果已有同名的檔案存在，復原點中的資料將會以複本的形式還原。 此複本將會以下列其中一種格式使用本機還原作業時間來當地語系化檔案名前置詞：
            - `YYYY-MM-DD HH-mm Copy of <original file name>`
            - `YYYY-MM-DD HH-mm Copy (n) of <original file name>`
        - **以復原的版本覆寫現有版本**：如果已有同名的檔案存在，則會將內容取代為復原點中的資料。
        - **不要復原已存在於復原目的地的專案**：如果已經有同名的檔案存在，則會略過該檔案。
    1. 如果應該使用復原點中的原始許可權還原檔案，請 **啟用還原存取控制清單 (ACL) 要復原之檔案或資料夾的許可權**。
        ![指定復原選項](./media/restore-all-files-volume-mars/specify-recovery-options.png)

1. 確認 **確認** 窗格上的復原詳細資料，然後選取 [ **復原**]。

    ![確認詳細資料](./media/restore-all-files-volume-mars/confirmation-details.png)

1. 在 [復原 **進度** ] 頁面上，監視復原工作的進度。 您也可以安全地關閉此嚮導，復原作業將會在背景中繼續進行。 您可以按兩下儀表板中的復原工作，再次查看進度。

## <a name="next-steps"></a>後續步驟

- 現在您已復原檔案和資料夾，接下來您可以 [管理您的備份](backup-azure-manage-windows-server.md)。
- 尋找 [有關備份檔案和資料夾的常見問題](backup-azure-file-folder-backup-faq.md)。
