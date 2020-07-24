---
title: 將系統狀態還原到 Windows Server
description: 從 Azure 中的備份還原 Windows Server 系統狀態的逐步說明。
ms.topic: conceptual
ms.date: 06/30/2020
ms.openlocfilehash: 8ba4cb5d5617b6a051aec8c54a595e701f62fb87
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/23/2020
ms.locfileid: "87067362"
---
# <a name="restore-system-state-to-windows-server"></a>將系統狀態還原到 Windows Server

本文說明如何從 Azure 復原服務保存庫來還原 Windows Server 系統狀態備份。 若要還原系統狀態，您必須有系統狀態備份（使用[備份系統狀態](backup-azure-system-state.md#back-up-windows-server-system-state)中的指示建立），並確定您已安裝[最新版的 Microsoft Azure 復原服務（MARS）代理程式](https://aka.ms/azurebackup_agent)。 從 Azure 復原服務保存庫來復原 Windows Server 系統狀態資料的程序需要兩個步驟：

1. 從 Azure 備份來還原檔案形式的系統狀態。 在從 Azure 備份來還原檔案形式的系統狀態時，您可以：
   * 將系統狀態還原到進行備份時所在的相同伺服器，或
   * 將系統狀態檔案還原到替代伺服器。

2. 使用 Windows Server Backup 公用程式，將還原的系統狀態檔案套用到 Windows Server。

## <a name="recover-system-state-files-to-the-same-server"></a>將系統狀態檔案復原到相同伺服器

下列步驟說明如何將 Windows Server 組態復原到先前的狀態。 將伺服器組態復原到已知的穩定狀態是非常有價值的作業。 下列步驟會從復原服務保存庫還原伺服器的系統狀態。

1. 開啟 **Microsoft Azure 備份**嵌入式管理單元。 如果您不知道快照安裝的位置，請在電腦或伺服器上搜尋 **Microsoft Azure 備份**。

    傳統型應用程式應該會出現在搜尋結果中。

2. 按一下 [復原資料]**** 以啟動精靈。

    ![復原資料](./media/backup-azure-restore-windows-server/recover.png)

3. 在 [開始使用]**** 窗格中，若要將資料還原至同一台伺服器或電腦，請選取 [這台伺服器 (`<server name>`)]**** 並按一下 [下一步]****。

    ![選擇此伺服器選項可將資料還原至同一部電腦](./media/backup-azure-restore-system-state/samemachine.png)

4. 在 [選取復原模式]**** 窗格上，選擇 [系統狀態]****，然後按 [下一步]****。

    ![瀏覽檔案](./media/backup-azure-restore-system-state/recover-type-selection.png)

5. 在 [選取磁碟區和日期]**** 窗格的行事曆上，選取復原點。

    您可以從任何時間的復原點還原。 **粗體**的日期表示至少有一個復原點可用。 一旦您選取一個日期，如果有多個復原點可用，您可以從 [時間]**** 下拉式功能表選擇特定的復原點。

    ![磁碟區和日期](./media/backup-azure-restore-system-state/select-date.png)

6. 選擇要還原的復原點之後，請按 [下一步]****。

    Azure 備份會掛接本機復原點，並且使用它做為復原磁碟區。

7. 在下一個窗格中，指定已復原之系統狀態檔案的目的地。 然後按一下 **[流覽]** 以開啟 [Windows Explorer]，然後尋找您想要的檔案和資料夾。 [建立複本，以擁有兩個版本]**** 選項會在現有系統狀態檔案封存中建立個別檔案的複本，而不是建立整個系統狀態封存的複本。

    ![修復選項](./media/backup-azure-restore-system-state/recover-as-files.png)

8. 確認 [確認]**** 窗格上的復原詳細資料，然後按一下 [復原]****。

   ![按一下 [復原] 以認可復原動作](./media/backup-azure-restore-system-state/confirm-recovery.png)

9. 將復原目的地中的 WindowsImageBackup** 目錄複製到伺服器的非重要磁碟區。 Windows 作業系統磁碟區通常是重要磁碟區。

10. 復原成功之後，請依照在[Windows Server 上套用已還原的系統狀態](#apply-restored-system-state-on-a-windows-server)一節中的步驟執行，以完成系統狀態復原程式。

## <a name="recover-system-state-files-to-an-alternate-server"></a>將系統狀態檔案復原到替代伺服器

如果您的 Windows Server 已損毀或無法存取，而且您想要藉由復原 Windows Server 系統狀態將它還原到穩定狀態，您可以從其他伺服器還原已損毀之伺服器的系統狀態。 請使用下列步驟在不同的伺服器上還原系統狀態。  

這些步驟中所使用的術語包含：

* *來源電腦*–從中取得備份並目前無法使用的原始電腦。
*  – 復原資料時的目標電腦。
* *範例*保存庫–*來源電腦*和*目的電腦*註冊所在的復原服務保存庫。

> [!NOTE]
> 從某個電腦擷取的備份無法還原到執行舊版作業系統的電腦上。 例如，從 Windows Server 2016 電腦擷取的備份便無法還原到 Windows Server 2012 R2。 不過，反過來則可行。 您可以使用 Windows Server 2012 R2 的備份來還原 Windows Server 2016。
>

1. 在*目的電腦*上開啟 [ **Microsoft Azure 備份**] 嵌入式管理單元。
2. 確定*目的機器*和*來源電腦*已註冊到相同的復原服務保存庫。
3. 按一下 [復原資料] **** 初始化工作流程。
4. 選取 [其他伺服器] ****

    ![其他伺服器](./media/backup-azure-restore-system-state/anotherserver.png)

5. 提供與「範例保存庫」 ** 相對應的保存庫認證檔。 如果保存庫認證檔無效（或已過期），請從 Azure 入口網站中的*範例*保存庫下載新的保存庫認證檔。 一旦提供保存庫認證檔，即會顯示與保存庫認證檔相關聯的復原服務保存庫。

6. 在 [選取備份伺服器]** 窗格上，從顯示的電腦清單選取 [來源電腦]。
7. 在 [選取復原模式] 窗格上，選擇 [系統狀態]****，然後按 [下一步]****。

    ![搜尋](./media/backup-azure-restore-system-state/recover-type-selection.png)

8. 在 [選取磁碟區和日期]**** 窗格的行事曆上，選取復原點。 您可以從任何時間的復原點還原。 **粗體**的日期表示至少有一個復原點可用。 選取日期之後，如果有多個復原點可用，請從 [**時間**] 下拉式功能表選擇特定的復原點。

    ![搜尋項目](./media/backup-azure-restore-system-state/select-date.png)

9. 選擇要還原的復原點之後，請按 [下一步]****。

10. 在 [選取系統狀態復原模式]**** 窗格中，指定您想要作為系統狀態檔案復原位置的目的地，然後按 [下一步]****。

    ![加密](./media/backup-azure-restore-system-state/recover-as-files.png)

    [建立複本，以擁有兩個版本]**** 選項會在現有系統狀態檔案封存中建立個別檔案的複本，而不是建立整個系統狀態封存的複本。

11. 確認 [確認] 窗格上的復原詳細資料，然後按一下 [**復原**]。

    ![按一下 [復原] 按鈕以確認復原程序](./media/backup-azure-restore-system-state/confirm-recovery.png)

12. 將 WindowsImageBackup** 目錄複製到伺服器的非重要磁碟區 (例如 D:\)。 Windows 作業系統磁碟區通常是重要磁碟區。

13. 若要完成復原程序，請使用下一節的步驟[將還原的系統狀態檔案套用到 Windows Server 上](#apply-restored-system-state-on-a-windows-server)。

## <a name="apply-restored-system-state-on-a-windows-server"></a>將還原的系統狀態套用到 Windows Server 上

在使用 Azure 復原服務代理程式復原了檔案形式的系統狀態後，請使用 Windows Server Backup 公用程式將復原的系統狀態套用到 Windows Server。 伺服器上已有 Windows Server Backup 公用程式可供使用。 下列步驟說明如何套用復原的系統狀態。

1. 開啟 [Windows Server Backup] 嵌入式管理單元。 如果您不知道快照安裝的位置，請在電腦或伺服器上搜尋 **Windows Server Backup**。

    傳統型應用程式就會出現在搜尋結果中。 如果未出現，或您在開啟應用程式時遇到錯誤，您必須在**伺服器管理員**的 [**新增功能]** 中，安裝可用的**Windows Server Backup 功能**和其底下的相依元件。

1. 在嵌入式管理單元中，選取 [本機備份]****。

    ![選取要從中還原的本機備份](./media/backup-azure-restore-system-state/win-server-backup-local-backup.png)

1. 在本機備份主控台的 [動作]**** 窗格中，按一下 [復原]**** 以開啟復原精靈。

1. 選取 [儲存在其他位置的備份]**** 選項，然後按 [下一步]****。

   ![選擇復原到不同伺服器](./media/backup-azure-restore-system-state/backup-stored-in-diff-location.png)

1. 在指定位置類型時，如果您的系統狀態備份已復原到其他伺服器，請選取 [遠端共用資料夾]****。 如果您在本機復原系統狀態，則請選取 [本機磁碟機]****。

    ![選取要從本機伺服器還是其他伺服器來復原](./media/backup-azure-restore-system-state/ss-recovery-remote-shared-folder.png)

1. 輸入 WindowsImageBackup** 目錄的路徑，或選擇包含此目錄的本機磁碟機 (例如，D:\WindowsImageBackup) (使用 Azure 復原服務代理程式隨著系統狀態檔案的復原而復原)，然後按 [下一步]****。

    ![共用檔案的路徑](./media/backup-azure-restore-system-state/ss-recovery-remote-folder.png)

1. 選取您想要還原的系統狀態版本，然後按 [下一步]****。

1. 在 [選取復原類型] 窗格中選取 [系統狀態]****，然後按 [下一步]****。

1. 在系統狀態復原的位置中，選取 [原始位置]****，然後按 [下一步]****。

    如果您要還原網域控制站，您會看到下列其他選項：

    ![系統狀態復原的位置](./media/backup-azure-restore-system-state/location-for-system-state-recovery.png)

    >[!NOTE]
    >如果您明確地想要執行所有 Active Directory 資料的授權還原，請只選取 [執行 Active Directory 檔案的授權還原]。

1. 檢閱確認詳細資料，確認重新啟動設定，然後按一下 [復原]**** 以套用還原的系統狀態檔案。

    ![啟動系統狀態檔案的還原](./media/backup-azure-restore-system-state/launch-ss-recovery.png)

    >[!NOTE]
    >如果您要以 DSRM 模式執行還原，請勿選取 [**自動重新開機伺服器**] 選項。

1. 成功完成還原之後，您必須在正常模式下重新開機伺服器。 開啟命令提示字元，然後輸入下列內容：`bcdedit /deletevalue safeboot`
1. 重新開機伺服器。

## <a name="special-considerations-for-system-state-recovery-on-a-domain-controller"></a>網域控制站上系統狀態復原的特殊考慮

系統狀態備份中包含了 Active Directory 資料。 請使用下列步驟將 Active Directory Domain Services (AD DS) 從目前的狀態還原到先前的狀態。 這種類型的還原可在兩種情況下完成：

* 當樹系中沒有任何可運作的網域控制站時，還原所有 Active Directory 資料
* 還原已刪除或損毀這些物件時的部分 Active Directory 資料

本文只會討論第一個案例，這會呼叫 AD DS 的 nonauthorative 還原，以及 sysvol 資料夾的授權還原。  如果您需要執行第二個案例（其中網域控制站仍然正常運作，但您需要還原特定的 AD 物件），請參閱[這些指示](https://support.microsoft.com/help/840001/how-to-restore-deleted-user-accounts-and-their-group-memberships-in-ac)。

1. 請遵循這裡的步驟，將[系統狀態檔案復原到替代伺服器](#recover-system-state-files-to-an-alternate-server)。
1. 使用下列命令以「目錄服務修復模式」** 重新啟動您的伺服器。 在提高權限的命令提示字元上：

    ```cmd
    Bcdedit /set safeboot dsrepair
    Shutdown /r /t 0
    ```

1. 若要在系統狀態還原過程中復原 Active Directory，您可以選擇下列兩種方法之一：

    * 遵循上面的指示，使用 Windows Server Backup 公用程式[在 Windows Server 上套用已還原的系統狀態](#apply-restored-system-state-on-a-windows-server)。

        >[!NOTE]
        >如果您要還原所有 Active Directory 資料（而且樹系中沒有任何可運作的網域控制站），則在上述步驟9中，請務必選取 [**執行 Active Directory 檔案的授權還原**]。

    * 使用[wbadmin](/windows-server/administration/windows-commands/wbadmin-start-systemstaterecovery)公用程式，從命令列執行還原。

        您將需要您想要使用之備份的版本識別碼。 您可以藉由執行下列命令來取得版本識別碼清單：

        ```cmd
        wbadmin get versions -backuptarget <servername\sharename>
        ```

        接著，您可以使用該版本識別碼來執行還原。

        例如，若要執行[nonauthorative 還原 AD DS 和 sysvol 資料夾的授權還原](/windows-server/identity/ad-ds/manage/ad-forest-recovery-nonauthoritative-restore)，請使用 9:00 04/30/2020 （儲存在共用資源上的備份 `\\servername\share` `server01` ），並輸入：

        ```cmd
        wbadmin start systemstaterecovery -version:04/30/2020-09:00 -backupTarget:\\servername\share -machine:server01 -authsysvol
        ```

1. 成功完成還原之後，您應該在正常模式下重新開機伺服器。 開啟命令提示字元，然後輸入下列內容：`bcdedit /deletevalue safeboot`
1. 重新開機伺服器。

## <a name="troubleshoot-failed-system-state-restore"></a>針對失敗的系統狀態還原進行疑難排解

如果先前套用系統狀態的程式未順利完成，請使用 Windows 修復環境（Win RE）來復原您的 Windows Server。 下列步驟說明如何使用 Win RE 來進行復原。 只有當 Windows Server 在系統狀態還原之後無法正常開機時，才使用此選項。 下列程序會清除非系統資料，請小心使用。

1. 將您的 Windows Server 開機到 Windows 修復環境 (Win RE)。

2. 在三個可用選項中選取 [疑難排解]。

    ![開啟功能表](./media/backup-azure-restore-system-state/winre-1.png)

3. 從 [進階選項]**** 畫面上選取 [命令提示字元]****，然後提供伺服器系統管理員使用者名稱和密碼。

   ![開啟功能表](./media/backup-azure-restore-system-state/winre-2.png)

4. 提供伺服器系統管理員使用者名稱和密碼。

    ![開啟功能表](./media/backup-azure-restore-system-state/winre-3.png)

5. 當您以系統管理員模式開啟命令提示字元時，請執行下列命令來取得系統狀態備份版本。

    ```cmd
    Wbadmin get versions -backuptarget:<Volume where WindowsImageBackup folder is copied>:
    ```

    ![取得系統狀態備份版本](./media/backup-azure-restore-system-state/winre-4.png)

6. 執行下列命令以取得備份中可用的所有磁碟區。

    ```cmd
    Wbadmin get items -version:<copy version from above step> -backuptarget:<Backup volume>
    ```

    ![取得系統狀態備份版本](./media/backup-azure-restore-system-state/winre-5.png)

7. 下列命令會復原所有屬於系統狀態備份的磁碟區。 請注意，此步驟只會復原屬於系統狀態的重要磁碟區。 非系統資料全都會遭到清除。

    ```cmd
    Wbadmin start recovery -items:C: -itemtype:Volume -version:<Backupversion> -backuptarget:<backup target volume>
    ```

     ![取得系統狀態備份版本](./media/backup-azure-restore-system-state/winre-6.png)

## <a name="next-steps"></a>後續步驟

* 現在您已復原檔案和資料夾，接下來您可以 [管理您的備份](backup-azure-manage-windows-server.md)。
