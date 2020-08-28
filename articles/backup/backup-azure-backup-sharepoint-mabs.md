---
title: 使用 MABS 將 SharePoint 伺服器陣列備份至 Azure
description: 使用 Azure 備份伺服器來備份和還原 SharePoint 資料。 本文提供設定 SharePoint 伺服器陣列，讓所需的資料可以儲存在 Azure 中的相關資訊。 您可以從磁碟或 Azure 還原受保護的 SharePoint 資料。
ms.topic: conceptual
ms.date: 04/26/2020
ms.openlocfilehash: 00af51764d5a9454b002de6375b2b16d6e80c300
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/27/2020
ms.locfileid: "89017425"
---
# <a name="back-up-a-sharepoint-farm-to-azure-with-mabs"></a>使用 MABS 將 SharePoint 伺服器陣列備份至 Azure

您可以使用 Microsoft Azure 備份伺服器 (MABS)，將 SharePoint 伺服器陣列備份到 Microsoft Azure，其方法與備份其他資料來源極為類似。 Azure 備份提供靈活的備份排程來建立每日、每週、每月或每年備份點，並可讓您針對各種備份點執行保留原則選項。 MABS 可讓您儲存本機磁碟複本來達成快速復原時間目標 (RTO)，也可以將複本儲存到 Azure 以節省成本來長期保留。

使用 MABS 將 SharePoint 備份至 Azure 的過程，類似於在本機將 SharePoint 備份至 DPM (Data Protection Manager)。 本文說明 Azure 的特定考量。

## <a name="sharepoint-supported-versions-and-related-protection-scenarios"></a>SharePoint 支援的版本與相關保護案例

如需支援的 SharePoint 版本清單及其備份時所需的 MABS 版本清單，請參閱 [MABS 保護矩陣](./backup-mabs-protection-matrix.md)

## <a name="before-you-start"></a>開始之前

您需要先確定幾件事，再將 SharePoint 伺服器陣列備份至 Azure。

### <a name="whats-not-supported"></a>不支援的內容

* 保護 SharePoint 伺服器陣列的 MABS 不會保護搜尋索引或應用程式服務資料庫。 您必須另外設定這些資料庫的保護。

* MABS 不支援備份向外延展檔案伺服器 (SOFS) 上託管的 SharePoint SQL Server 資料庫。

### <a name="prerequisites"></a>必要條件

繼續之前，請確定您符合使用 Microsoft Azure 備份來保護工作負載的所有[必要條件](backup-azure-dpm-introduction.md#prerequisites-and-limitations)。 關於必要條件的一些工作包括︰建立備份保存庫、下載保存庫認證、安裝 Azure 備份代理程式，以及向保存庫註冊 Azure 備份伺服器。

其他必要條件和限制：

* 根據預設，當您保護 SharePoint 時，也會保護所有內容資料庫 (以及 SharePoint_Config 和 SharePoint_AdminContent* 資料庫)。 如果您想要新增自訂，例如搜尋索引、範本或應用程式服務資料庫，或使用者設定檔服務，您需要另外設定保護這些功能。 對於包含這些幾種功能或自訂檔案的所有資料夾，請務必啟用保護。

* 您無法將 SharePoint 資料庫當做 SQL Server 資料來源來保護。 您可以從伺服器陣列備份中復原個別的資料庫。

* 請記住，MABS 以**本機系統**身分執行，若要備份 SQL Server 資料庫，SQL Server 在該帳戶上需要 sysadmin 權限。 在您想要備份的 SQL Server 上，請將 NT AUTHORITY\SYSTEM 設定為 **sysadmin**。

* 針對伺服器陣列中每 1000 萬個項目，必須有至少 2 GB 的磁碟區空間來放置 MABS 資料夾。 此空間對目錄產生是必要的。 為了讓您使用 MABS 來復原特定項目 (網站集合、網站、清單、文件庫、資料夾、個別文件和清單項目)，產生目錄時會建立每個內容資料庫所包含的 URL 清單。 在 [MABS 系統管理員主控台] 的 [復原] 工作區域中，您可以在 [可復原的項目] 窗格中檢視 URL 清單。

* 在 SharePoint 伺服器陣列中，如果有以 SQL Server 別名設定的 SQL Server 資料庫，請在 MABS 要保護的前端網頁伺服器上，安裝 SQL Server 用戶端元件。

* SharePoint 2013 不支援保護應用程式市集項目。

* MABS 不支援保護遠端 FILESTREAM。 FILESTREAM 應該是資料庫的一部分。

## <a name="configure-backup"></a>設定備份

若要備份 SharePoint 伺服器陣列，請使用 ConfigureSharePoint.exe 設定 SharePoint 的保護，然後在 MABS 中建立保護群組。

1. **執行 ConfigureSharePoint.exe** - 此工具會設定 SharePoint VSS 寫入器服務 \(WSS\)，並提供 SharePoint 伺服器陣列的認證給保護代理程式。 部署保護代理程式之後，您可以在前端網頁服務器的 `<MABS Installation Path\>\bin` 資料夾中找到 ConfigureSharePoint.exe 檔案。  如果您有多個 WFE 伺服器，則只需在其中一個伺服器上安裝。 執行方式如下：

    * 在 WFE 伺服器的命令提示字元中，巡覽至 `\<MABS installation location\>\\bin\\` 並執行 `ConfigureSharePoint \[\-EnableSharePointProtection\] \[\-EnableSPSearchProtection\] \[\-ResolveAllSQLAliases\] \[\-SetTempPath <path>\]`，其中：

        * **EnableSharePointProtection** 會啟用保護 SharePoint 伺服器陣列、啟用 VSS 寫入器，並註冊 DCOM 應用程式 WssCmdletsWrapper 的身分識別，以使用隨著此選項輸入認證的使用者身分執行。 此帳戶應該為伺服器陣列管理員，以及前端網路伺服器的本機系統管理員。

        * **EnableSPSearchProtection** 會在前端網頁伺服器上使用 HKLM\\Software\\Microsoft\\ Microsoft Data Protection Manager\\Agent\\2.0\\ 下的登錄機碼 SharePointSearchEnumerationEnabled，以啟用保護 WSS 3.0 SP Search，並註冊 DCOM 應用程式 WssCmdletsWrapper 的身分識別，以使用隨著此選項輸入認證的使用者身分執行。 此帳戶應該為伺服器陣列管理員，以及前端網路伺服器的本機系統管理員。

        * **ResolveAllSQLAliases** 會顯示 SharePoint VSS 寫入器報告的所有別名，並解析為對應的 SQL Server。 還會顯示已解析的執行個體名稱。 如果伺服器已鏡像，則也會顯示鏡像伺服器。 將會報告所有未解析為 SQL Server 的別名。

        * **SetTempPath** 會將環境變數 TEMP 和 TMP 設定為指定的路徑。 如果復原大型網站集合、網站、清單或項目，但伺服器陣列管理員 Temporary 資料夾中沒有足夠的空間，則項目層級復原會失敗。 此選項可讓您將暫存檔案的資料夾路徑變更另一個磁碟區，該磁碟區有足夠空間可儲存要復原的網站集合或網站。

    * 輸入伺服器陣列系統管理員認證。 這個帳戶應該是 WFE 伺服器上本機 Administrator 群組的成員。 如果伺服器陣列管理員不是本機系統管理員，請在 WFE 伺服器上授與下列權限：

        * 授與 **WSS_Admin_WPG** 群組對 MABS 資料夾的完整控制 (`%Program Files%\Data Protection Manager\DPM\`)。

        * 授與 **WSS_Admin_WPG** 群組對 MABS 登錄機碼 (`HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Microsoft Data Protection Manager`) 的讀取權限。

        執行 ConfigureSharePoint.exe 之後，如果 SharePoint 伺服器陣列管理員認證有所有變更，您必須重新執行此程式。

1. 若要建立保護群組，請在 MABS 主控台按一下 [保護] > [動作] > [建立保護群組]，以開啟 [建立新保護群組] 精靈。

1. 在 [選取保護群組類型] 中，選取 [伺服器]。

1. 在 [選取群組成員]中，展開擔任 WFE 角色的伺服器。 如果有一個以上的 WFE 伺服器，請選取您已安裝 ConfigureSharePoint.exe 的伺服器。

    當您展開 SharePoint 伺服器時，MABS 會查詢 VSS，以查看 MABS 可保護什麼資料。  如果 SharePoint 資料庫在遠端，MABS 會連線到資料庫。 如果沒有顯示 SharePoint 資料來源，請檢查 VSS 寫入器是否正在 SharePoint 伺服器和任何遠端 SQL Server 上執行，並確定 SharePoint 伺服器和遠端 SQL Server 上都已安裝 MABS 代理程式。 此外，請確定 SharePoint 資料庫沒有在別處當成 SQL Server 資料庫來保護。

1. 在 [選取資料保護方法] 中，指定您要如何處理短期和長期備份。 短期 \- 備份一律會先移至磁片，並可選擇是否要使用 Azure 備份短期或長期從磁片備份至 Azure 雲端 \( \- \) 。

1. 在 [選取短期目標] 中，指定您要如何備份到磁碟上的短期儲存體。   在 [保留期間] 中，指定您要讓資料在磁碟上保留多久。 在 [同步頻率] 中，指定您要增量備份到磁碟的頻率。 如果您不想設定備份間隔，您可以核取 [恰好在復原點之前]，讓 MABS 就在每個排定的復原點之前，執行快速完整備份。

1. 在 [檢閱磁碟配置] 頁面中，檢閱配置給保護群組的存放集區磁碟空間。

    [資料大小總計] 是您想要備份的資料大小，[要在 MABS 上佈建的磁碟空間] 是 MABS 建議給保護群組的空間。 MABS 會根據設定來選擇理想的備份磁碟區。 不過，您也可以在 [磁碟配置詳細資料] 中編輯備份磁碟區選擇。 針對工作負載，請在下拉式功能表中選取您偏好的儲存體。 您的編輯內容會變更 [可用磁碟儲存體] 窗格中 [儲存體總計] 和 [可用儲存體] 的值。 佈建不足的空間是指 MABS 建議為磁碟區新增的儲存體數量，以便未來繼續順暢地備份。

1. 在 [選擇複本的建立方式] 中，選取您要如何處理首次的完整資料複寫。  如果您選擇透過網路複寫，建議您選擇離峰時間。 對於大量資料或較差的網路狀況，請考慮使用卸除式媒體來離線複寫資料。

1. 在 [選擇一致性檢查選項] 中，選取要如何自動執行一致性檢查。 您可以只在複本資料變得不一致時才檢查，或根據排程來執行檢查。 如果您不設定自動一致性檢查，則隨時想要執行手動檢查時，請在 MABS 主控台的 [保護] 區域中，以滑鼠右鍵按一下保護群組，然後選取 [執行一致性檢查]。

1. 如果您已選擇使用 Azure 備份來備份到雲端，在 [指定線上保護資料] 頁面上，請確定已選取您想要備份至 Azure 的工作負載。

1. 在 [指定線上備份排程] 中，指定增量備份至 Azure 的頻率。 您可以將備份排程為每日、每週、每月、每年執行，並選取備份的執行時間和日期。 一天最多可以備份兩次。 每次執行備份時，都會根據儲存在 MABS 上的已備份資料的複本，在 Azure 中建立復原點。

1. 在 [指定線上保留原則] 中，您可以指定如何在 Azure 中保留根據每日/每週/每月/每年備份建立的復原點。

1. 在 [選擇線上複寫] 中，指定如何首次完整複寫資料。 您可以透過網路進行複寫，也可以執行離線備份 (離線植入)。 離線備份會使用 Azure 匯入功能。 [閱讀更多](./backup-azure-backup-import-export.md)。

1. 在 [摘要] 頁面上檢閱您的設定。 按一下 [建立群組] 後，就會首次複寫資料。 完成時，在 [狀態] 頁面上，保護群組狀態會顯示為 [正常]。 之後，就按照保護群組設定進行備份。

## <a name="monitoring"></a>監視

建立保護群組之後，就會進行初始複寫，MABS 也會開始備份並同步處理 SharePoint 資料。 MABS 會監視首次同步處理和後續備份。  您有幾種方式可以監視 SharePoint 資料：

* 如果使用預設 MABS 監視，您可以發佈警示和設定通知，以設定主動式監視的通知。 針對危急、警告或資訊警示，以及具現化復原的狀態，您可以透過電子郵件傳送通知。

* 如果使用 Operations Manager，您可以集中發佈警示。

### <a name="set-up-monitoring-notifications"></a>設定監視通知

1. 在 [MABS 系統管理員主控台]，按一下 [監視] > [動作] > [選項]。

2. 按一下 [SMTP 伺服器]，輸入伺服器名稱、連接埠，以及寄出通知的電子郵件地址。 地址必須有效。

3. 在 [已驗證的 SMTP 伺服器] 中，輸入使用者名稱和密碼。 使用者名稱和密碼必須是某人的網域帳戶名稱，而此人的「寄件者」地址如上一個步驟所述。 否則，通知傳遞會失敗。

4. 若要測試 SMTP 伺服器設定，請按一下 [傳送測試電子郵件]，輸入電子郵件地址以接收 MABS 傳送的測試訊息，然後按一下 [確定]。 按一下 [選項] > [通知]，然後選取收件者想要收到通知的警示類型。 在 [收件者] 中，輸入每位收件者的電子郵件地址，讓這些收件者收到 MABS 傳送的一份通知。

### <a name="publish-operations-manager-alerts"></a>發佈 Operations Manager 警示

1. 在 [MABS 系統管理員主控台]，按一下 [監視] > [動作] > [選項] > [警示發佈] > [發佈作用中警示]

2. 啟用 [警示發佈] 之後，所有可能需要使用者動作的現有 MABS 警示，都會發佈至 **MABS 警示**事件記錄檔。 MABS 伺服器上安裝的 Operations Manager 代理程式接著會將這些警示發佈至 Operations Manager，並在產生新警示時繼續更新主控台。

## <a name="restore-a-sharepoint-item-from-disk-by-using-mabs"></a>使用 MABS 從磁碟還原 SharePoint 項目

在下列範例中， *Recovering SharePoint item* 已被意外刪除，而需要復原。
![MABS SharePoint Protection4](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection5.png)

1. 開啟 [MABS 系統管理員主控台]。 MABS 保護的所有 SharePoint 伺服器陣列都顯示在 [保護]  索引標籤中。

    ![MABS SharePoint Protection3](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection4.png)
2. 若要開始復原項目，請選取 [復原]  索引標籤。

    ![MABS SharePoint Protection5](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection6.png)
3. 您可以使用萬用字元型搜尋，在復原點範圍內搜尋 SharePoint 中的 *Recovering SharePoint item* 。

    ![MABS SharePoint Protection6](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection7.png)
4. 從搜尋結果中選取適當的復原點，以滑鼠右鍵按一下項目，然後選取 [復原]。
5. 您也可以瀏覽不同的復原點，並選取要復原的資料庫或項目。 選取 [日期] > [復原時間]，然後選取正確的 [資料庫] > [SharePoint 伺服器陣列] > [復原點] > [項目]。

    ![MABS SharePoint Protection7](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection8.png)
6. 在該項目上按一下滑鼠右鍵，然後選取 [復原] 以開啟 [復原精靈]。 按 [下一步] 。

    ![檢閱復原選項](./media/backup-azure-backup-sharepoint/review-recovery-selection.png)
7. 選取您想要執行的復原類型，然後按 [下一步] 。

    ![復原類型](./media/backup-azure-backup-sharepoint/select-recovery-type.png)

   > [!NOTE]
   > 範例中選取的 [復原到原始] 會將項目復原到原始的 SharePoint 網站。
   >
   >
8. 選取您想要使用的 [復原程序]  。

   * 如果 SharePoint 伺服器陣列未變更，而且與正在還原的復原點相同，請選取 [ **不使用復原伺服器陣列來復原** ]。
   * 如果 SharePoint 伺服器陣列自建立復原點後已變更，請選取 [使用復原伺服器陣列執行復原]  。

     ![復原程序](./media/backup-azure-backup-sharepoint/recovery-process.png)
9. 提供暫時復原資料庫的預備 SQL Server 執行個體位置，並在要復原項目的 MABS 和執行 SharePoint 的伺服器上提供預備檔案共用。

    ![Staging Location1](./media/backup-azure-backup-sharepoint/staging-location1.png)

    MABS 會將裝載 SharePoint 專案的內容資料庫附加至暫存的 SQL Server 實例。 它會從內容資料庫復原項目，並將它放在 MABS 上的預備檔案位置。 位於預備位置上的復原項目，現在需要匯出至 SharePoint 伺服器陣列上的預備位置。

    ![Staging Location2](./media/backup-azure-backup-sharepoint/staging-location2.png)
10. 選取 [指定復原選項] ，並將安全性設定套用至 SharePoint 伺服器陣列，或套用復原點的安全性設定。 按 [下一步] 。

    ![修復選項](./media/backup-azure-backup-sharepoint/recovery-options.png)

    > [!NOTE]
    > 您可以選擇調節網路頻寬使用量。 這會對生產時段的生產伺服器產生最小的影響。
    >
    >
11. 檢閱摘要資訊，然後按一下 [復原]  以開始復原檔案。

    ![復原摘要](./media/backup-azure-backup-sharepoint/recovery-summary.png)
12. 現在，在 [MABS 系統管理員主控台] 中選取 [監視] 索引標籤，以檢視復原的**狀態**。

    ![復原狀態](./media/backup-azure-backup-sharepoint/recovery-monitoring.png)

    > [!NOTE]
    > 現在即可還原檔案。 您可以重新整理 SharePoint 網站來檢查已還原的檔案。
    >
    >

## <a name="restore-a-sharepoint-database-from-azure-by-using-mabs"></a>使用 MABS 從 Azure 還原 SharePoint 資料庫

1. 若要復原 SharePoint 內容資料庫，請瀏覽各種復原點 (如上所示)，並選取要還原的復原點。

    ![MABS SharePoint Protection8](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection9.png)
2. 按兩下 SharePoint 復原點以顯示可用的 SharePoint 目錄資訊。

   > [!NOTE]
   > 因為 SharePoint 伺服器陣列長期保留在 Azure 中受到保護，所以 MABS 伺服器上不會有目錄資訊 (中繼資料)。 如此一來，每當需要復原時間點 SharePoint 內容資料庫，您就需要重新編目 SharePoint 伺服器陣列。
   >
   >
3. 按一下 [重新編目] 。

    ![MABS SharePoint Protection10](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection12.png)

    [雲端重新編目]  狀態視窗隨即會開啟。

    ![MABS SharePoint Protection11](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection13.png)

    完成編目後，狀態會變更為 [成功] 。 按一下 [關閉] 。

    ![MABS SharePoint Protection12](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection14.png)
4. 按一下 MABS [復原] 索引標籤中顯示的 SharePoint 物件，以取得內容資料庫結構。 在項目上按一下滑鼠右鍵，然後按一下 [復原] 。

    ![MABS SharePoint Protection13](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection15.png)
5. 此時，依照本文前述的復原步驟，從磁碟復原 SharePoint 內容資料庫。

## <a name="switching-the-front-end-web-server"></a>切換前端網頁伺服器

如果您有一部以上的前端網頁伺服器，而且想要切換 MABS 保護伺服器陣列時使用的伺服器，請遵循下列指示：

下列程序採用的伺服器陣列範例有兩部前端網頁伺服器：*Server1* 和 *Server2*。 MABS 使用 Server1 來保護伺服器陣列。 將 MABS 使用的前端網頁伺服器改為 Server2，這樣您就可以從伺服器陣列中移除 Server1。

> [!NOTE]
> 如果 MABS 保護伺服器陣列時使用的前端網頁伺服器無法使用，請根據下列程序，從步驟 4 開始變更前端網頁伺服器。

### <a name="to-change-the-front-end-web-server-that-mabs-uses-to-protect-the-farm"></a>變更 MABS 保護伺服器陣列時使用的前端網頁伺服器

1. 在命令提示字元中執行下列命令，以停止 Server1 上的 SharePoint VSS 寫入器服務：

    ```CMD
    stsadm -o unregisterwsswriter
    ```

1. 在 Server1 上，開啟 [登錄編輯程式] 並巡覽至下列機碼：

   **HKLM\System\CCS\Services\VSS\VssAccessControl**

1. 檢查 VssAccessControl 子機碼中列出的所有值。 如果有任何項目的值資料為 0，而且有另一個 VSS 寫入器正在以相關聯的帳戶憑證執行，請將值資料變更為 1。

1. 在 Server2 上安裝保護代理程式。

   > [!WARNING]
   > 只有當兩部伺服器都位於相同網域時，您才能切換前端網頁伺服器。

1. 在 Server2 上，在命令提示字元中切換到 `_MABS installation location_\bin\` 目錄，然後執行 **ConfigureSharepoint**。 如需 ConfigureSharePoint 的詳細資訊，請參閱[設定備份](#configure-backup)。

1. 選取伺服器陣列所屬的保護群組，然後按一下 [修改保護群組]。

1. 在 [修改群組精靈] 的 [選取群組成員] 頁面上，展開 [Server2]，選取伺服器陣列，然後完成精靈。

   系統會啟動一致性檢查。

1. 如果您已執行步驟 6，則現在可以從保護群組中移除磁碟區。

## <a name="next-steps"></a>後續步驟

請參閱[備份 Exchange Server](backup-azure-exchange-mabs.md) 一文。
請參閱[備份 SQL Server](backup-azure-sql-mabs.md) 一文。
