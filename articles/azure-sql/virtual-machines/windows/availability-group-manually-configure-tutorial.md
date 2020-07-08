---
title: 教學課程：設定 SQL Server Always On 可用性群組
description: 本教學課程說明如何在「Azure 虛擬機器」上建立「SQL Server Always On 可用性群組」。
services: virtual-machines
documentationCenter: na
author: MikeRayMSFT
editor: monicar
tags: azure-service-management
ms.assetid: 08a00342-fee2-4afe-8824-0db1ed4b8fca
ms.service: virtual-machines-sql
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 08/30/2018
ms.author: mikeray
ms.custom: seo-lt-2019
ms.openlocfilehash: 0b98838441325245b3f4322a32eb5e2376557313
ms.sourcegitcommit: 845a55e6c391c79d2c1585ac1625ea7dc953ea89
ms.contentlocale: zh-TW
ms.lasthandoff: 07/05/2020
ms.locfileid: "85960736"
---
# <a name="tutorial-configure-a-sql-server-availability-group-on-azure-virtual-machines-manually"></a>教學課程：以手動方式在 Azure 虛擬機器上設定 SQL Server 可用性群組

[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

本教學課程說明如何在「Azure 虛擬機器」上建立「SQL Server Always On 可用性群組」。 整個教學課程會建立一個在兩部 SQL Server 上都有一份資料庫複本的「可用性群組」。

**估計時間**：在符合先決條件之後，需要大約 30 分鐘才能完成。

下圖說明您在本教學課程中建置的項目。

![可用性群組](./media/availability-group-manually-configure-tutorial/00-EndstateSampleNoELB.png)

## <a name="prerequisites"></a>必要條件

本教學課程假設您對「SQL Server Always On 可用性群組」有基本的了解。 如需詳細資訊，請參閱 [AlwaysOn 可用性群組概觀 (SQL Server)](https://msdn.microsoft.com/library/ff877884.aspx)。

下表列出開始本教學課程之前，您必須完成的必要條件：

| 需求 |描述 |
|----- |----- |----- |
|![正方形 ](./media/availability-group-manually-configure-tutorial/square.png) **二 SQL Server 實例**    | - 在 Azure 可用性設定組中 <br/> - 在單一網域中 <br/> - 已安裝「容錯移轉叢集」功能 |
|![方形 ](./media/availability-group-manually-configure-tutorial/square.png) **Windows Server**    | 叢集見證的檔案共用 |  
|![方形 ](./media/availability-group-manually-configure-tutorial/square.png) **SQL Server 服務帳戶**    | 網域帳戶 |
|![方形 ](./media/availability-group-manually-configure-tutorial/square.png) **SQL Server Agent 服務帳戶**    | 網域帳戶 |  
|![方形 ](./media/availability-group-manually-configure-tutorial/square.png) **防火牆埠已開啟**    | - SQL Server：**1433** (用於預設執行個體) <br/> - 資料庫鏡像端點：**5022** 或任何可用的連接埠。 <br/> - 可用性群組負載平衡器 IP 位址健康情況探查：**59999** 或任何用的連接埠 <br/> - 叢集核心負載平衡器 IP 位址健康情況探查：**58888** 或任何可用的連接埠 |
|![方形 ](./media/availability-group-manually-configure-tutorial/square.png) **新增容錯移轉叢集功能**    | 兩個 SQL Server 執行個體都需要此功能 |
|![方形 ](./media/availability-group-manually-configure-tutorial/square.png) **安裝網域帳戶**    | - 每部 SQL Server 上的本機系統管理員 <br/> - 每個 SQL Server 執行個體之 SQL Server sysadmin 固定伺服器角色的成員  |


開始本教學課程之前，您必須先[完成在 Azure 虛擬機器中建立 Always On 可用性群組的必要條件 (英文)](availability-group-manually-configure-prerequisites-tutorial.md)。 如果這些必要條件都已經完成，您可以跳到[建立叢集](#CreateCluster)。

  >[!NOTE]
  > 此教學課程中提供的許多步驟，現在都可透過 [Azure SQL VM CLI](availability-group-az-cli-configure.md) 與 [Azure 快速入門範本](availability-group-quickstart-template-configure.md)來自動化。


<!--**Procedure**: *This is the first "step". Make titles H2's and short and clear – H2's appear in the right pane on the web page and are important for navigation.*-->

<a name="CreateCluster"></a>

## <a name="create-the-cluster"></a>建立叢集

完成必要條件之後，第一個步驟是建立一個包含兩部 SQL Server 和一部見證伺服器的「Windows Server 容錯移轉叢集」。

1. 使用遠端桌面通訊協定（RDP）連接到第一個 SQL Server。 使用同時是 SQL Server 和見證伺服器上系統管理員的網域帳戶。

   >[!TIP]
   >如果您已依照[必要條件文件](availability-group-manually-configure-prerequisites-tutorial.md)的說明操作，則您已建立名為 **CORP\Install** 的帳戶。 請使用此帳戶。

2. 在 [**伺服器管理員**] 儀表板中，選取 [**工具**]，然後選取 [**容錯移轉叢集管理員**]。
3. 在左窗格中，以滑鼠右鍵按一下 [**容錯移轉叢集管理員**]，然後選取 [**建立**叢集]。

   ![建立叢集](./media/availability-group-manually-configure-tutorial/40-createcluster.png)

4. 在「建立叢集精靈」中，以下表中的設定逐步完成每個頁面來建立單節點叢集：

   | 頁面 | 設定 |
   | --- | --- |
   | 開始之前 |使用預設值 |
   | 選取伺服器 |在 [**輸入伺服器名稱**] 中輸入第一個 SQL Server 名稱，然後選取 [**新增**]。 |
   | 驗證警告 |選取 [**否。我不需要 Microsoft 提供此叢集的支援，因此不想要執行驗證測試]。當我選取 [下一步] 時，繼續建立**叢集。 |
   | 用於管理叢集的存取點 |在 [叢集名稱] 中輸入叢集名稱，例如 **SQLAGCluster1**。|
   | 確認 |除非您使用的是儲存空間，否則請使用預設值。 請詳閱此表之後的備註。 |

### <a name="set-the-windows-server-failover-cluster-ip-address"></a>設定 Windows 伺服器容錯移轉叢集 IP 位址

  > [!NOTE]
  > 在 Windows Server 2019 上，叢集會建立**分散式伺服器名稱**，而不是**叢集網路名稱**。 如果您使用的是 Windows Server 2019，請略過此教學課程中參照叢集核心名稱的任何步驟。 您可以使用 [PowerShell](failover-cluster-instance-storage-spaces-direct-manually-configure.md#create-failover-cluster) 建立叢集網路名稱。 檢閱部落格[容錯移轉叢集：叢集網路物件](https://blogs.windows.com/windowsexperience/2018/08/14/announcing-windows-server-2019-insider-preview-build-17733/#W0YAxO8BfwBRbkzG.97) \(英文\) 以取得詳細資訊。 

1. 在 [容錯移轉叢集管理員] 中，向下捲動到 [叢集核心資源] 區段，然後展開叢集詳細資料。 在 [失敗] 狀態中，應該會同時出現 [名稱] 和 [IP 位址] 資源 。 由於指派給叢集的 IP 位址與虛擬機器本身的 IP 位址相同，因此位址重複，所以無法讓該 IP 位址資源上線。

2. 以滑鼠右鍵按一下失敗的 [ **IP 位址**] 資源，然後選取 [**屬性**]。

   ![叢集屬性](./media/availability-group-manually-configure-tutorial/42_IPProperties.png)

3. 選取 [靜態 IP 位址]，然後從相同的子網路中指定一個可用的位址作為您的虛擬機器。

4. 在 [叢集**核心資源**] 區段中，以滑鼠右鍵按一下 [叢集名稱]，然後選取 [**上線**]。 等待兩個資源上線。 當叢集名稱資源上線時，它會以新的 Active Directory （AD）電腦帳戶更新網域控制站（DC）伺服器。 稍後請使用此 AD 帳戶來執行「可用性群組」叢集服務。

### <a name="add-the-other-sql-server-to-cluster"></a><a name="addNode"></a>將其他 SQL Server 新增到叢集

將其他 SQL Server 新增到叢集。

1. 在瀏覽器樹狀結構中，以滑鼠右鍵按一下叢集，然後選取 [**新增節點**]。

    ![將節點新增至叢集](./media/availability-group-manually-configure-tutorial/44-addnode.png)

1. 在 [**加入節點嚮導]** 中，選取 **[下一步]**。 在 [選取伺服器] 頁面中，新增第二部 SQL Server。 在 [**輸入伺服器名稱**] 中輸入伺服器名稱，然後選取 [**新增**]。 完成後，選取 **[下一步]**。

1. 在 [**驗證警告**] 頁面中，選取 [**否**] （在生產環境中，您應該執行驗證測試）。 然後，選取 [下一步]。

8. 如果您使用「儲存空間」，請在 [確認] 頁面上取消選取標示為 [新增適合的儲存裝置到叢集] 的核取方塊。

   ![新增節點確認](./media/availability-group-manually-configure-tutorial/46-addnodeconfirmation.png)

   >[!WARNING]
   >如果您使用「儲存空間」但未取消選取 [新增適合的儲存裝置到叢集]，Windows 就會在進行叢集程序時將虛擬機器中斷連結。 因此，它們不會出現在 [磁片管理員] 或 [Explorer] 中，直到從叢集中移除儲存空間，並使用 PowerShell 重新附加為止。 儲存空間可將多個磁碟分組為存放集區。 如需詳細資訊，請參閱[儲存空間](https://technet.microsoft.com/library/hh831739)。
   >

1. 選取 [下一步]。

1. 選取 [完成]。

   「容錯移轉叢集管理員」會顯示您的叢集有一個新節點，並將它列在 [節點] 容器中。

10. 登出遠端桌面工作階段。

### <a name="add-a-cluster-quorum-file-share"></a>新增叢集仲裁檔案共用

在此範例中，Windows 叢集會使用檔案共用來建立叢集仲裁。 本教學課程使用「節點與檔案共用多數」仲裁。 如需詳細資訊，請參閱[了解容錯移轉叢集中的仲裁設定](https://technet.microsoft.com/library/cc731739.aspx)。

1. 使用遠端桌面工作階段來連接到檔案共用見證成員伺服器。

1. 在**伺服器管理員**上，選取 [**工具**]。 開啟 [電腦管理]。

1. 選取 [**共用資料夾**]。

1. 以滑鼠右鍵按一下 [**共用**]，然後選取 [**新增共用 ...**]。

   ![新增共用](./media/availability-group-manually-configure-tutorial/48-newshare.png)

   使用「建立共用資料夾精靈」來建立共用。

1. 在 [**資料夾路徑**] 上，選取 **[流覽]** ，並找出或建立共用資料夾的路徑。 選取 [下一步]。

1. 在 [名稱、描述和設定] 中，確認共用名稱和路徑。 選取 [下一步]。

1. 在 [共用資料夾權限] 上，設定 [自訂權限]。 選取 [**自訂 ...**]。

1. 在 [**自訂許可權**] 上，選取 [**新增**]。

1. 確定用來建立叢集的帳戶具備完整的控制權。

   ![新增共用](./media/availability-group-manually-configure-tutorial/50-filesharepermissions.png)

1. 選取 [確定]。

1. 在 [**共用資料夾許可權**] 中，選取 **[完成]**。 再次選取 [完成]****。  

1. 登出伺服器

### <a name="configure-the-cluster-quorum"></a>設定叢集仲裁

接著，設定叢集仲裁。

1. 使用遠端桌面來連接到第一個叢集節點。

1. 在**容錯移轉叢集管理員**中，以滑鼠右鍵按一下叢集，指向 [**其他動作**]，然後選取 [**設定叢集仲裁設定**]。

   ![新增共用](./media/availability-group-manually-configure-tutorial/52-configurequorum.png)

1. 在 [**設定叢集仲裁嚮導]** 中，選取 **[下一步]**。

1. 在 [**選取仲裁設定選項**] 中，選擇 **[選取仲裁見證**]，然後選取 **[下一步]**。

1. 在 [**選取仲裁見證**] 上，選取 [**設定檔案共用見證**]。

   >[!TIP]
   >Windows Server 2016 支援雲端見證。 如果您選擇此類型的見證，就不需要檔案共用見證。 如需詳細資訊，請參閱[為容錯移轉叢集部署雲端見證 (Deploy a cloud witness for a Failover Cluster)](https://technet.microsoft.com/windows-server-docs/failover-clustering/deploy-cloud-witness)。 本教學課程使用檔案共用見證，這是舊版作業系統所支援的類型。
   >

1. 在 [設定檔按共用見證] 上，輸入您所建立共用的路徑。 選取 [下一步]。

1. 確認 [確認] 上的設定。 選取 [下一步]。

1. 選取 [完成]。

此時就會為叢集核心資源設定檔案共用見證。

## <a name="enable-availability-groups"></a>啟用可用性群組

接著，啟用 [AlwaysOn 可用性群組] 功能。 在兩部 SQL Server 上執行這些步驟。

1. 在 [開始] 畫面中，啟動 [SQL Server 組態管理員]。
2. 在瀏覽器樹狀目錄中，選取 [ **SQL Server Services**]，然後以滑鼠右鍵按一下 [ **SQL Server （MSSQLSERVER）** ] 服務，然後選取 [**屬性**]。
3. 選取 [ **AlwaysOn 高可用性**] 索引標籤，然後選取 [**啟用 AlwaysOn 可用性群組**]，如下所示：

    ![啟用 AlwaysOn 可用性群組](./media/availability-group-manually-configure-tutorial/54-enableAlwaysOn.png)

4. 選取 [套用]。 在快顯對話方塊中選取 **[確定]** 。

5. 重新啟動 SQL Server 服務。

請在其他 SQL Server 上重複這些步驟。

<!-----------------
## <a name="endpoint-firewall"></a>Open firewall for the database mirroring endpoint

Each instance of SQL Server that participates in an Availability Group requires a database mirroring endpoint. This endpoint is a TCP port for the instance of SQL Server that is used to synchronize the database replicas in the Availability Groups on that instance.

On both SQL Servers, open the firewall for the TCP port for the database mirroring endpoint.

1. On the first SQL Server **Start** screen, launch **Windows Firewall with Advanced Security**.
2. In the left pane, select **Inbound Rules**. On the right pane, select **New Rule**.
3. For **Rule Type**, choose **Port**.
1. For the port, specify TCP and choose an unused TCP port number. For example, type *5022* and select **Next**.

   >[!NOTE]
   >For this example, we're using TCP port 5022. You can use any available port.

5. In the **Action** page, keep **Allow the connection** selected and select **Next**.
6. In the **Profile** page, accept the default settings and select **Next**.
7. In the **Name** page, specify a rule name, such as **Default Instance Mirroring Endpoint** in the **Name** text box, then select **Finish**.

Repeat these steps on the second SQL Server.
-------------------------->

## <a name="create-a-database-on-the-first-sql-server"></a>在第一部 SQL Server 上建立資料庫

1. 啟動 RDP 檔案，使用具備 sysadmin 固定伺服器角色成員身分的網域帳戶來連接到第一部 SQL Server。
1. 開啟 SQL Server Management Studio，然後連接到第一部 SQL Server。
7. 在**物件總管**中，以滑鼠右鍵按一下 [**資料庫**]，然後選取 [**新增資料庫**]。
8. 在 [**資料庫名稱**] 中，輸入**MyDB1**，然後選取 **[確定]**。

### <a name="create-a-backup-share"></a><a name="backupshare"></a> 建立備份共用

1. 在**伺服器管理員**的第一個 SQL Server 上，選取 [**工具**]。 開啟 [電腦管理]。

1. 選取 [**共用資料夾**]。

1. 以滑鼠右鍵按一下 [**共用**]，然後選取 [**新增共用 ...**]。

   ![新增共用](./media/availability-group-manually-configure-tutorial/48-newshare.png)

   使用「建立共用資料夾精靈」來建立共用。

1. 在 [**資料夾路徑**] 上，選取 **[流覽]** ，並找出或建立資料庫備份共用資料夾的路徑。 選取 [下一步]。

1. 在 [名稱、描述和設定] 中，確認共用名稱和路徑。 選取 [下一步]。

1. 在 [共用資料夾權限] 上，設定 [自訂權限]。 選取 [**自訂 ...**]。

1. 在 [**自訂許可權**] 上，選取 [**新增**]。

1. 確定兩部伺服器的 SQL Server 和 SQL Server Agent 服務帳戶都具備完整的控制權。

   ![新增共用](./media/availability-group-manually-configure-tutorial/68-backupsharepermission.png)

1. 選取 [確定]。

1. 在 [**共用資料夾許可權**] 中，選取 **[完成]**。 再次選取 [完成]****。  

### <a name="take-a-full-backup-of-the-database"></a>執行完整資料庫備份

您必須備份新資料庫以將記錄鏈結初始化。 如果您沒有備份新的資料庫，就無法將它納入「可用性群組」中。

1. 在**物件總管**中，以滑鼠右鍵按一下資料庫，指向 [工作 **]，然後**選取 [**備份**]。

1. 選取 **[確定]** 以進行預設備份位置的完整備份。

## <a name="create-the-availability-group"></a>建立可用性群組。

您現在已準備好使用下列步驟來設定「可用性群組」：

* 在第一部 SQL Server 上建立資料庫。
* 建立資料庫的完整備份和交易記錄備份。
* 使用**NORECOVERY**選項，將完整和記錄備份還原至第二個 SQL Server。
* 建立具有同步認可、自動容錯移轉及可讀取次要複本的可用性群組（**AG1**）。

### <a name="create-the-availability-group"></a>建立可用性群組：

1. 在連接到第一部 SQL Server 的遠端桌面工作階段上。 在 SSMS 的**物件總管**中，以滑鼠右鍵按一下 [ **AlwaysOn 高可用性**]，然後選取 [**新增可用性組嚮導]**。

    ![啟動新增可用性群組精靈](./media/availability-group-manually-configure-tutorial/56-newagwiz.png)

2. 在 [**簡介**] 頁面中，選取 **[下一步]**。 在 [**指定可用性組名**] 頁面的 [**可用性組名**] 中，輸入可用性群組的名稱。 例如**AG1**。 選取 [下一步]。

    ![新增可用性群組嚮導，請指定可用性組名](./media/availability-group-manually-configure-tutorial/58-newagname.png)

3. 在 [**選取資料庫**] 頁面中，選取您的資料庫，然後選取 **[下一步]**。

   >[!NOTE]
   >此資料庫會符合「可用性群組」的必要條件，因為您已經為預定的主要複本至少建立一份完整備份。
   >

   ![新增可用性群組嚮導，選取資料庫](./media/availability-group-manually-configure-tutorial/60-newagselectdatabase.png)

4. 在 [**指定複本**] 頁面中，選取 [**新增複本**]。

   ![新增可用性群組嚮導，指定複本](./media/availability-group-manually-configure-tutorial/62-newagaddreplica.png)

5. 隨即跳出 [連接至伺服器] 對話方塊。 在 [伺服器名稱] 中輸入第二部伺服器的名稱。 選取 [連接]。

   回到 [指定複本] 頁面，您現在應該會看到 [可用性複本] 中列出第二部伺服器。 請依照下列方式設定複本。

   ![新增可用性群組嚮導，指定複本（完成）](./media/availability-group-manually-configure-tutorial/64-newagreplica.png)

6. 選取 [**端點**] 以查看此可用性群組的資料庫鏡像端點。 請使用您設定[資料庫鏡像端點的防火牆規則](availability-group-manually-configure-prerequisites-tutorial.md#endpoint-firewall)時所用的相同連接埠。

    ![[新增可用性組嚮導] 中，選取 [初始資料同步處理]](./media/availability-group-manually-configure-tutorial/66-endpoint.png)

8. 在 [選取初始資料同步處理] 頁面中，選取 [完整]，然後指定一個共用網路位置。 針對位置，請使用[您所建立的備份共用](#backupshare)。 在範例中， ** \\ \\<先 SQL Server \> \Backup \\ **。 選取 [下一步]。

   >[!NOTE]
   >完整同步處理會完整備份第一個 SQL Server 執行個體上的資料庫，然後將它還原到第二個執行個體。 就大型資料庫而言，不建議進行完整同步處理，因為可能費時很久。 您可以手動備份並使用 `NO RECOVERY` 來還原備份，以縮短此時間。 如果在設定「可用性群組」之前已經使用 `NO RECOVERY` 在第二部 SQL Server 上還原資料庫，請選擇 [僅聯結]。 如果您想要在設定「可用性群組」之後進行備份，請選擇 [略過初始資料同步處理]。
   >

   ![[新增可用性組嚮導] 中，選取 [初始資料同步處理]](./media/availability-group-manually-configure-tutorial/70-datasynchronization.png)

9. 在 [**驗證**] 頁面中，選取 **[下一步]**。 此頁面應該會看起來如下圖：

    ![新增可用性群組嚮導，驗證](./media/availability-group-manually-configure-tutorial/72-validation.png)

    >[!NOTE]
    >由於您尚未設定「可用性群組」接聽程式，因此出現一個接聽程式組態的警告。 您可以忽略此警告，因為在 Azure 虛擬機器上，您是在建立 Azure Load Balancer 之後才會建立接聽程式。

10. 在 [**摘要**] 頁面中，選取 **[完成]**，然後等候嚮導設定新的可用性群組。 在 [**進度**] 頁面中，您可以選取 [**更多詳細資料**] 來查看詳細的進度。 在精靈完成後，請檢查 [結果] 頁面，確認已成功建立「可用性群組」。

     ![新增可用性群組嚮導，結果](./media/availability-group-manually-configure-tutorial/74-results.png)

11. 選取 [關閉]**** 結束此精靈。

### <a name="check-the-availability-group"></a>檢查可用性群組

1. 在**物件總管**中，展開 [ **AlwaysOn 高可用性**]，然後展開 [**可用性群組**]。 新的「可用性群組」現在應該會顯示在此容器中。 以滑鼠右鍵按一下可用性群組，然後選取 [**顯示儀表板**]。

   ![顯示可用性群組儀表板](./media/availability-group-manually-configure-tutorial/76-showdashboard.png)

   您的**AlwaysOn 儀表板**看起來應該類似下列螢幕擷取畫面：

   ![可用性群組儀表板](./media/availability-group-manually-configure-tutorial/78-agdashboard.png)

   當中會顯示複本、每個複本的容錯移轉模式和同步處理狀態。

2. 在 [**容錯移轉叢集管理員**中，選取您的叢集。 選取 [角色]。 您使用的「可用性群組」名稱是叢集上的一個角色。 該可用性群組沒有用戶端連接的 IP 位址，因為您未設定接聽程式。 您將在建立負載平衡器之後設定接聽程式。

   ![容錯移轉叢集管理員中的可用性群組](./media/availability-group-manually-configure-tutorial/80-clustermanager.png)

   > [!WARNING]
   > 請勿嘗試從「容錯移轉叢集管理員」容錯移轉「可用性群組」。 所有容錯移轉作業都應在 SSMS 的 **AlwaysOn 儀表板** 中執行。 如需詳細資訊，請參閱[容錯移轉叢集管理員與可用性群組一起使用的限制](https://msdn.microsoft.com/library/ff929171.aspx)。
    >

此時，您擁有一個在兩個 SQL Server 執行個體上都有複本的「可用性群組」。 您可以在執行個體之間移動「可用性群組」。 您還不能連接到「可用性群組」，因為您沒有接聽程式。 在 Azure 虛擬機器中，接聽程式需要負載平衡器。 下一個步驟就是在 Azure 中建立負載平衡器。

<a name="configure-internal-load-balancer"></a>

## <a name="create-an-azure-load-balancer"></a>建立 Azure Load Balancer

在 Azure 虛擬機器上，「SQL Server 可用性群組」需要負載平衡器。 負載平衡器會保有可用性群組接聽程式以及 Windows Server 容錯移轉叢集的 IP 位址。 本節摘要說明如何在 Azure 入口網站中建立負載平衡器。

Azure 中的負載平衡器可以是 Standard Load Balancer 或基本 Load Balancer。 標準負載平衡器的功能比基本負載平衡器多。 對於可用性群組，如果您使用「可用性區域」(而非「可用性設定組」)，則需要標準負載平衡器。 如需各 Load Balancer SKU 間之差異的詳細資訊，請參閱 [Load Balancer SKU 的比較](../../../load-balancer/skus.md)。

1. 在 [Azure 入口網站中，移至您的 SQL 伺服器所在的資源群組，然後選取 [ **+ 新增**]。
1. 搜尋 [負載平衡器]。 選擇 Microsoft 所發行的負載平衡器。

   ![容錯移轉叢集管理員中的可用性群組](./media/availability-group-manually-configure-tutorial/82-azureloadbalancer.png)

1. 選取 [建立]。
1. 設定負載平衡器的下列參數。

   | 設定 | 欄位 |
   | --- | --- |
   | **名稱** |使用負載平衡器的文字名稱，例如 **sqlLB**。 |
   | **型別** |內部 |
   | **虛擬網路** |使用 Azure 虛擬網路的名稱。 |
   | **子網路** |使用虛擬機器所在子網路的名稱。  |
   | **IP 位址指派** |靜態 |
   | **IP 位址** |使用來自子網路的可用位址。 將此位址用於您的可用性群組接聽程式。 請注意，這與您的叢集 IP 位址不同。  |
   | **訂用帳戶** |使用與虛擬機器相同的訂用帳戶。 |
   | **位置** |使用與虛擬機器相同的位置。 |

   Azure 入口網站刀鋒視窗看起來應該會像這樣：

   ![建立負載平衡器](./media/availability-group-manually-configure-tutorial/84-createloadbalancer.png)

1. 選取 [**建立**] 以建立負載平衡器。

若要設定負載平衡器，您必須建立後端集區、探查，並設定負載平衡規則。 請在 Azure 入口網站中執行這些操作。

### <a name="add-a-backend-pool-for-the-availability-group-listener"></a>新增可用性群組接聽程式的後端集區

1. 在 Azure 入口網站中，移至您的可用性群組。 您可能需要重新整理檢視，才能看到新建立的負載平衡器。

   ![在資源群組中尋找負載平衡器](./media/availability-group-manually-configure-tutorial/86-findloadbalancer.png)

1. 選取負載平衡器，選取 [**後端**集區]，然後選取 [ **+ 新增**]。

1. 輸入後端集區的名稱。

1. 將後端集區關聯至包含 VM 的可用性設定組。

1. 在 [目標網路 IP 設定] 下方，檢查**虛擬機器**並選擇兩個會託管可用性群組複本的虛擬機器。 請勿包含檔案共用見證伺服器。

   >[!NOTE]
   >如果未指定這兩個虛擬機器，則只能成功連線至主要複本。

1. 選取 [確定] 以建立後端集區。

### <a name="set-the-probe"></a>設定探查

1. 選取負載平衡器，選擇 [**健康情況探查**]，然後選取 [ **+ 新增**]。

1. 依照下列方式設定接聽程式健康情況探查：

   | 設定 | 說明 | 範例
   | --- | --- |---
   | **名稱** | Text | SQLAlwaysOnEndPointProbe |
   | **通訊協定** | 選擇 [TCP] | TCP |
   | **通訊埠** | 任何未使用的連接埠 | 59999 |
   | **間隔**  | 探查嘗試間隔的時間長度 (秒) |5 |
   | **狀況不良臨界值** | 將虛擬機器視為狀況不良之前，必須達到的連續探查失敗次數  | 2 |

1. 選取 **[確定]** 以設定健康情況探查。

### <a name="set-the-load-balancing-rules"></a>設定負載平衡規則

1. 選取負載平衡器，選擇 [**負載平衡規則**]，然後選取 [ **+ 新增**]。

1. 依照下列方式接聽程式負載平衡規則。

   | 設定 | 說明 | 範例
   | --- | --- |---
   | **名稱** | Text | SQLAlwaysOnEndPointListener |
   | **前端 IP 位址** | 選擇一個位址 |使用您建立負載平衡器時所建立的位址。 |
   | **通訊協定** | 選擇 [TCP] |TCP |
   | **通訊埠** | 使用可用性群組接聽程式的連接埠 | 1433 |
   | **後端連接埠** | 如果已為伺服器直接回傳設定「浮動 IP」，便不會使用此欄位。 | 1433 |
   | **探查** |您為探查指定的名稱 | SQLAlwaysOnEndPointProbe |
   | **工作階段持續性** | 下拉式清單 | **None** |
   | **閒置逾時** | 讓 TCP 連線保持開啟的分鐘數 | 4 |
   | **浮動 IP (伺服器直接回傳)** | |啟用 |

   > [!WARNING]
   > 伺服器直接回傳是在建立時設定。 無法予以變更。
   >

1. 選取 **[確定]** 以設定接聽程式負載平衡規則。

### <a name="add-the-cluster-core-ip-address-for-the-windows-server-failover-cluster-wsfc"></a>新增 Windows Server 容錯移轉叢集 (WSFC) 的叢集核心 IP 位址

WSFC IP 位址也必須位於負載平衡器上。

1. 在 Azure 入口網站中，移至相同的 Azure 負載平衡器。 選取 [**前端 IP**設定]，然後選取 [ **+ 新增**]。 在叢集核心資源中，使用您為 WSFC 設定的 IP 位址。 將 IP 位址設為靜態。

1. 在負載平衡器上，選取 [**健康情況探查**]，然後選取 [ **+ 新增**]。

1. 依照下列所述設定 WSFC 叢集核心 IP 位址健康情況探查：

   | 設定 | 說明 | 範例
   | --- | --- |---
   | **名稱** | Text | WSFCEndPointProbe |
   | **通訊協定** | 選擇 [TCP] | TCP |
   | **通訊埠** | 任何未使用的連接埠 | 58888 |
   | **間隔**  | 探查嘗試間隔的時間長度 (秒) |5 |
   | **狀況不良臨界值** | 將虛擬機器視為狀況不良之前，必須達到的連續探查失敗次數  | 2 |

1. 選取 **[確定]** 以設定健康情況探查。

1. 設定負載平衡規則。 選取 [**負載平衡規則**]，然後選取 [ **+ 新增**]。

1. 依照下列所述設定叢集核心 IP 位址負載平衡規則。

   | 設定 | 說明 | 範例
   | --- | --- |---
   | **名稱** | Text | WSFCEndPoint |
   | **前端 IP 位址** | 選擇一個位址 |使用您在設定 WSFC IP 位址時所建立的位址。 這與接聽程式 IP 位址不同 |
   | **通訊協定** | 選擇 [TCP] |TCP |
   | **通訊埠** | 使用叢集 IP 位址的連接埠。 這個可用的連接埠不用於接聽程式探查連接埠。 | 58888 |
   | **後端連接埠** | 如果已為伺服器直接回傳設定「浮動 IP」，便不會使用此欄位。 | 58888 |
   | **探查** |您為探查指定的名稱 | WSFCEndPointProbe |
   | **工作階段持續性** | 下拉式清單 | **None** |
   | **閒置逾時** | 讓 TCP 連線保持開啟的分鐘數 | 4 |
   | **浮動 IP (伺服器直接回傳)** | |啟用 |

   > [!WARNING]
   > 伺服器直接回傳是在建立時設定。 無法予以變更。
   >

1. 選取 **[確定]** 以設定負載平衡規則。

## <a name="configure-the-listener"></a><a name="configure-listener"></a> 設定接聽程式

下一步是在容錯移轉叢集上設定「可用性群組」接聽程式。

> [!NOTE]
> 本教學課程說明如何建立單一接聽程式，其中包含一個 ILB IP 位址。 若要使用一或多個 IP 位址來建立一或多個接聽程式，請參閱[建立可用性群組接聽程式和負載平衡器 | Azure](availability-group-listener-powershell-configure.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)。
>

[!INCLUDE [ag-listener-configure](../../../../includes/virtual-machines-ag-listener-configure.md)]

## <a name="set-listener-port"></a>設定接聽程式連接埠

在 SQL Server Management Studio 中，設定接聽程式連接埠。

1. 啟動 SQL Server Management Studio，然後連接到主要複本。

1. 瀏覽至 [AlwaysOn 高可用性] > [可用性群組] > [可用性群組接聽程式]。

1. 您現在應該會看到在容錯移轉叢集管理員中建立的接聽程式名稱。 以滑鼠右鍵按一下接聽程式名稱，然後選取 [**屬性**]。

1. 在 [連接埠] 方塊中，為「可用性群組」接聽程式指定連接埠號碼。 預設值為1433。 選取 [確定]。

現在，您在以 Resource Manager 模式執行的 Azure 虛擬機器中，已有一個「SQL Server 可用性群組」。

## <a name="test-connection-to-listener"></a>測試對接聽程式的連線

若要測試連線︰

1. 使用 RDP 連接到相同虛擬網路中的 SQL Server，但不擁有複本。 您可以使用叢集中的其他 SQL Server。

1. 使用**sqlcmd**公用程式來測試連接。 例如，下列指令碼會透過接聽程式搭配 Windows 驗證，建立與主要複本的 **sqlcmd** 連線︰

   ```cmd
   sqlcmd -S <listenerName> -E
   ```

   如果接聽程式使用預設連接埠 (1433) 以外的連接埠，請在連接字串中指定該連接埠。 例如，下列命令會連線 `sqlcmd` 到埠1435上的接聽程式：

   ```cmd
   sqlcmd -S <listenerName>,1435 -E
   ```

SQLCMD 連線會自動連線到任何一個裝載主要複本的 SQL Server 執行個體。

> [!TIP]
> 確定您指定的連接埠在兩部 SQL Server 在防火牆上開啟。 這兩部伺服器需要您使用的 TCP 連接埠的輸入規則。 如需詳細資訊，請參閱[新增或編輯防火牆規則](https://technet.microsoft.com/library/cc753558.aspx)。
>

## <a name="next-steps"></a>後續步驟

- [將第二個可用性群組的 IP 位址新增到負載平衡器](availability-group-listener-powershell-configure.md#Add-IP)。
