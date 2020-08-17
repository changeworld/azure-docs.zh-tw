---
title: 使用 MARS 代理程式將檔案還原到 Windows Server
description: 在本文中，了解如何使用 Microsoft Azure 復原服務 (MARS) 代理程式，將儲存在 Azure 中的資料還原至 Windows Server 或 Windows 電腦。
ms.topic: conceptual
ms.date: 09/07/2018
ms.openlocfilehash: d96c6c54431d0160bd58be0c3491ef2b22753e53
ms.sourcegitcommit: 64ad2c8effa70506591b88abaa8836d64621e166
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/17/2020
ms.locfileid: "88263396"
---
# <a name="restore-files-to-windows-server-using-the-mars-agent"></a>使用 MARS 代理程式將檔案還原到 Windows Server

此文章說明如何從備份保存庫還原資料。 若要還原資料，可以使用 Microsoft Azure 復原服務 (MARS) 代理程式中的 [復原資料精靈]。 您可以：

* 將資料還原到進行備份的相同電腦。
* 將資料還原至其他電腦。

您可以使用「立即還原」功能裝載可寫入的復原點快照做為復原磁碟區。 然後，您可以使用選擇性地還原檔案，來探索復原磁片區並將檔案複製到本機電腦。

> [!NOTE]
> 如果您要使用「立即還原」來還原資料，必須要有 [2017 年 1 月 Azure 備份更新](https://support.microsoft.com/help/3216528/azure-backup-update-for-microsoft-azure-recovery-services-agent-januar)。 另外，您也必須在支援文章所列出地區設定的保存庫中，保護備份資料。 請參閱 [2017 年 1 月 Azure 備份更新](https://support.microsoft.com/help/3216528/azure-backup-update-for-microsoft-azure-recovery-services-agent-januar)，了解支援「立即還原」的最新地區設定清單。
>

在 Azure 入口網站中搭配復原服務保存庫使用立即還原。 如果您已在備份保存庫中儲存資料，則已將它們轉換至復原服務保存庫。 如果您想要使用「立即還原」，請下載 MARS 更新，並依照提及「立即還原」部分中的程序進行操作。

[!INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)]

## <a name="use-instant-restore-to-recover-data-to-the-same-machine"></a>使用「立即還原」將資料還原至同一台電腦

如果您不小心刪除檔案，而您想要將它還原到相同的電腦 (備份進行處)，下列步驟可協助您復原資料。

1. 開啟 **Microsoft Azure 備份**嵌入式管理單元。 如果您不知道快照安裝的位置，請在電腦或伺服器上搜尋 **Microsoft Azure 備份**。

    傳統型應用程式應該會出現在搜尋結果中。

2. 按一下 [復原資料] 以啟動精靈。

    ![Azure 備份的螢幕擷取畫面，其中反白顯示覆原資料 (還原至相同的電腦) ](./media/backup-azure-restore-windows-server/recover.png)

3. 在 [開始使用] 頁面中，若要將資料還原至同一台伺服器或電腦，請選取 [這台伺服器 (`<server name>`)] > [下一步]。

    ![[復原資料] Wizard 消費者入門頁面的螢幕擷取畫面， (還原到相同的電腦) ](./media/backup-azure-restore-windows-server/samemachine_gettingstarted_instantrestore.png)

4. 在 [選取復原模式] 頁面中，選擇 [個別檔案與資料夾] > [下一步]。

    ![[復原資料] Wizard [選取復原模式] 頁面的螢幕擷取畫面， (還原到相同的電腦) ](./media/backup-azure-restore-windows-server/samemachine_selectrecoverymode_instantrestore.png)
   > [!IMPORTANT]
   > 若要執行還原個別檔案與資料夾的選項，必須具備 .NET Framework 4.5.2 或更新版本。 如果您未看見 [個別檔案與資料夾] 選項，您必須先將 .NET Framework 升級至 4.5.2 版或更新版本，再重新嘗試。

   > [!TIP]
   > [個別檔案及資料夾] 選項可讓您快速存取復原點資料。 它適合用來復原個別檔案（大小總計不超過 80 GB），並在復原期間提供最多 6 MBps 的傳輸或複製速度。 [磁碟區] 選項會復原指定磁碟區中的所有備份資料。 此選項可提供更快的傳送速率 (高達 40 MBps) ，這非常適合用來復原大型資料或整個磁片區。

5. 在 [選取磁碟區和日期] 頁面中，選取包含您要還原之檔案和資料夾的磁碟區。

    在日曆上，選取復原點。 **粗體**的日期表示至少有一個復原點可用。 如果單一日期內有多個復原點可用，可以從 [時間] 下拉式功能表選擇特定的復原點。

    ![[復原資料] 的 [選取磁片區和日期] 頁面的螢幕擷取畫面， (還原到相同的電腦) ](./media/backup-azure-restore-windows-server/samemachine_selectvolumedate_instantrestore.png)

6. 選擇要還原的復原點之後，請選取 [裝載]。

    Azure 備份會掛接本機復原點，並且使用它做為復原磁碟區。

7. 在 [瀏覽及復原檔案] 頁面上，選取 [瀏覽] 以開啟 [Windows 檔案總管]，然後尋找所需的檔案和資料夾。

    ![[復原資料] Wizard [流覽及復原檔案] 頁面的螢幕擷取畫面， (還原到相同的電腦) ](./media/backup-azure-restore-windows-server/samemachine_browserecover_instantrestore.png)

8. 在 [Windows 檔案總管] 中，複製要還原的檔案和資料夾，並將它們貼到伺服器或電腦的任意本機位置。 您可以直接從復原磁片區開啟或串流檔案，並確認您正在復原正確的版本。

    ![Windows 檔案總管的螢幕擷取畫面，其中已反白顯示覆制 (還原至相同的電腦) ](./media/backup-azure-restore-windows-server/samemachine_copy_instantrestore.png)

9. 當您完成時，請在 [ **流覽及復原檔案]** 頁面上，選取 [ **卸載**]。 然後選取 [是] 確認要卸載磁碟區。

    ![[復原資料] Wizard [流覽及復原檔案] 頁面的螢幕擷取畫面， (還原到相同的電腦) ](./media/backup-azure-restore-windows-server/samemachine_unmount_instantrestore.png)

    > [!Important]
    > 如果您沒有選取 [卸載]，復原磁碟區會保持掛接 6 個小時 (從掛接後開始計算)。 不過，如果是進行中的檔案複製，掛接時間可延長至高達 24 小時。 當磁碟區處於掛接狀態時，不會執行任何備份作業。 掛接磁碟區時，任何排定要執行的備份作業都會在復原磁碟區卸載之後執行。
    >

## <a name="use-instant-restore-to-restore-data-to-an-alternate-machine"></a>使用「立即還原」將資料還原至其他電腦

若您遺失整個伺服器，您仍然可從 Azure 備份將資料還原到其他電腦。 下列步驟說明工作流程。

這些步驟包括以下術語：

* *來源電腦* – 建立備份且目前無法使用的原始電腦。
*  – 復原資料時的目標電腦。
* *範例保存庫* – 來源電腦和目標電腦註冊所在的復原服務保存庫。

> [!NOTE]
> 備份無法還原到執行舊版作業系統的目標電腦。 例如，從 Windows 7 電腦建立的備份可以還原至 Windows 7 (或更新版本) 的電腦。 從 Windows 8 電腦建立的備份無法還原至 Windows 7 電腦。
>
>

1. 在目標電腦上開啟 [Microsoft Azure 備份] 嵌入式管理單元。

2. 確定目標電腦和來源電腦均已註冊到相同的復原服務保存庫。

3. 選取 [復原資料] 以開啟 [復原資料精靈]。

    ![Azure 備份的螢幕擷取畫面，其中反白顯示覆原資料 (還原至替代電腦) ](./media/backup-azure-restore-windows-server/recover.png)

4. 在 [開始使用] 頁面中，選取 [其他伺服器]。

    ![[復原資料] Wizard 消費者入門頁面的螢幕擷取畫面， (還原至替代電腦) ](./media/backup-azure-restore-windows-server/alternatemachine_gettingstarted_instantrestore.png)

5. 提供與範例保存庫相對應的保存庫認證檔，然後選取 [下一步]。

    如果保存庫認證檔無效 (或已過期)，請從 Azure 入口網站中的「範例保存庫」下載新的保存庫認證檔。 在您提供有效的保存庫認證檔之後，就會顯示對應的備份保存庫名稱。

6. 在 [選取備份伺服器] 頁面中，從顯示的電腦清單選取來源電腦，並提供複雜密碼。 然後選取 [下一步]。

    ![[復原資料] Wizard 的 [選取備份伺服器] 頁面的螢幕擷取畫面， (還原至替代電腦) ](./media/backup-azure-restore-windows-server/alternatemachine_selectmachine_instantrestore.png)

7. 在 [選取復原模式] 頁面中，選取 [個別檔案與資料夾] > [下一步]。

    ![復原資料 Wizard 選取復原模式頁面的螢幕擷取畫面 (還原至替代電腦) ](./media/backup-azure-restore-windows-server/alternatemachine_selectrecoverymode_instantrestore.png)

8. 在 [選取磁碟區和日期] 頁面中，選取包含您要還原之檔案和資料夾的磁碟區。

    在日曆上，選取復原點。 **粗體**的日期表示至少有一個復原點可用。 如果單一日期內有多個復原點可用，可以從 [時間] 下拉式功能表選擇特定的復原點。

    ![[復原資料] 的 [選取磁片區和日期] 頁面的螢幕擷取畫面， (還原至替代電腦) ](./media/backup-azure-restore-windows-server/alternatemachine_selectvolumedate_instantrestore.png)

9. 選取 [掛接] 以掛接本機的復原點，做為目標電腦的復原磁碟區。

10. 在 [瀏覽及復原檔案] 頁面中，選取 [瀏覽] 以開啟 [Windows 檔案總管]，然後尋找所需的檔案和資料夾。

    ![[復原資料] Wizard [流覽和復原檔案] 頁面的螢幕擷取畫面， (還原至替代電腦) ](./media/backup-azure-restore-windows-server/alternatemachine_browserecover_instantrestore.png)

11. 在 [Windows 檔案總管] 中，從復原磁碟區複製檔案和資料夾，然後貼到目標電腦位置。 您可以直接從復原磁碟區開啟或串流檔案，並確認是否復原正確的版本。

    ![Windows 檔案總管的螢幕擷取畫面，其中已反白顯示覆制 (還原至替代電腦) ](./media/backup-azure-restore-windows-server/alternatemachine_copy_instantrestore.png)

12. 當您完成時，請在 [ **流覽及復原檔案]** 頁面上，選取 [ **卸載**]。 然後選取 [是] 確認要卸載磁碟區。

    ![將磁片區卸載 (還原至替代電腦) ](./media/backup-azure-restore-windows-server/alternatemachine_unmount_instantrestore.png)

    > [!Important]
    > 如果您沒有選取 [卸載]，復原磁碟區會保持掛接 6 個小時 (從掛接後開始計算)。 不過，如果是進行中的檔案複製，掛接時間可延長至高達 24 小時。 當磁碟區處於掛接狀態時，不會執行任何備份作業。 掛接磁碟區時，任何排定要執行的備份作業都會在復原磁碟區卸載之後執行。
    >

## <a name="next-steps"></a>後續步驟

* 現在您已復原檔案和資料夾，接下來您可以 [管理您的備份](backup-azure-manage-windows-server.md)。

* 尋找[有關備份檔案和資料夾的常見問題](backup-azure-file-folder-backup-faq.md)。
