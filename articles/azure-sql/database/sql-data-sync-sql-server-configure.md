---
title: 設定 SQL 資料同步
description: 本教學課程說明如何設定 Azure 的 SQL 資料同步
services: sql-database
ms.service: sql-database
ms.subservice: data-movement
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: tutorial
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 01/14/2019
ms.openlocfilehash: 90f1e068bf816ecf72bcc8a3ba9439883e69a069
ms.sourcegitcommit: 4bebbf664e69361f13cfe83020b2e87ed4dc8fa2
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/01/2020
ms.locfileid: "91616664"
---
# <a name="tutorial-set-up-sql-data-sync-between-databases-in-azure-sql-database-and-sql-server"></a>教學課程：設定 Azure SQL Database 與 SQL Server 中資料庫之間的 SQL 資料同步
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

在本教學課程中，您將了解如何使用包含 Azure SQL Database 和 SQL Server 執行個體的同步群組設定 SQL 資料同步。 同步群組會依照您設定的排程自訂設定和同步。

本教學課程假設您先前至少有一些使用 SQL Database 和 SQL Server 的經驗。

如需 SQL 資料同步的概觀，請參閱[使用 SQL 資料同步跨雲端和內部部署資料庫同步處理資料](sql-data-sync-data-sql-server-sql-database.md)。

如需如何設定 SQL 資料同步的 PowerShell 範例，請參閱[如何在 SQL Database 中的資料庫之間進行同步](scripts/sql-data-sync-sync-data-between-sql-databases.md)或[如何在 Azure SQL Database 與 SQL Server 中的資料庫之間進行同步](scripts/sql-data-sync-sync-data-between-azure-onprem.md)

> [!IMPORTANT]
> SQL 資料同步目前**不**支援 Azure SQL 受控執行個體。

## <a name="create-sync-group"></a>建立同步群組

1. 移至 [Azure 入口網站](https://portal.azure.com)，在 SQL Database 中尋找您的資料庫。 搜尋並選取 [SQL 資料庫]****。

    ![搜尋資料庫、Microsoft Azure 入口網站](./media/sql-data-sync-sql-server-configure/search-for-sql-databases.png)

1. 選取您想要用來作為資料同步中樞資料庫的資料庫。

    ![從資料庫清單中選取、Microsoft Azure 入口網站](./media/sql-data-sync-sql-server-configure/select-sql-database.png)

    > [!NOTE]
    > 中樞資料庫是同步拓撲的中央端點，其中的同步群組具有多個資料庫端點。 同步群組中具有端點的所有其他成員資料庫都會與中樞資料庫進行同步處理。

1. 在所選取資料庫的 [SQL 資料庫] 選單上，選取 [同步至其他資料庫]。

    ![同步至其他資料庫、Microsoft Azure 入口網站](./media/sql-data-sync-sql-server-configure/sync-to-other-databases.png)

1. 在 [同步至其他資料庫]**** 頁面上，選取 [新增同步群組]****。 [新增同步群組]**** 頁面隨即開啟並醒目提示 [建立同步群組 (步驟 1)]****。

   ![步驟 1 設定](./media/sql-data-sync-sql-server-configure/stepone.png)

   在 [建立資料同步群組]**** 頁面上，變更下列設定：

   | 設定                        | 描述 |
   | ------------------------------ | ------------------------------------------------- |
   | **同步群組名稱** | 輸入新同步群組的名稱。 這個名稱與資料庫本身的名稱不同。 |
   | **同步處理中繼資料資料庫** | 選擇建立資料庫 (建議) 或使用現有的資料庫。<br/><br/>如果您選擇 [新增資料庫]****，請選取 [建立新的資料庫]****。 然後在 [SQL Database]**** 頁面上，命名並設定新的資料庫，然後選取 [確定]****。<br/><br/>如果您選擇 [使用現有資料庫]****，請從清單中選取資料庫。 |
   | **自動同步處理** | 選取 [開啟]**** 或 [關閉]****。<br/><br/>如果您選擇 [開啟]****，請在 [同步頻率]**** 區段中輸入數字，然後選取 [秒]****、[分鐘]****、[小時]**** 或 [天]****。<br/> 儲存設定並經過選取的間隔期間之後，就會開始進行第一次同步。|
   | **衝突解決** | 選取 [中樞獲勝]**** 或 [成員獲勝]****。<br/><br/>**中樞獲勝**表示發生衝突時，中樞資料庫中的資料會覆寫成員資料庫中的衝突資料。<br/><br/>**成員獲勝**表示發生衝突時，成員資料庫中的資料會覆寫中樞資料庫中的衝突資料。 |

   > [!NOTE]
   > Microsoft 建議您建立新的空白資料庫作為 [同步中繼資料資料庫]****。 資料同步會在此資料庫中建立資料表，並頻繁執行工作負載。 此資料庫會作為所選區域與訂用帳戶中所有同步群組的共用**同步中繼資料資料庫**。 您無法在不移除區域中所有同步群組和同步代理程式的情況下，變更資料庫或其名稱。

   選取 [確定]**** 並等候同步群組建立和部署完成。

## <a name="add-sync-members"></a>新增同步成員

建立和部署新同步群組之後，系統會在 [新增同步群組]**** 頁面上醒目提示 [新增同步成員 (步驟 2)]****。

在 [中樞資料庫] 區段中，輸入中樞資料庫所在伺服器的現有認證。 請不要在此區段輸入「新」** 的認證。

![步驟 2 設定](./media/sql-data-sync-sql-server-configure/steptwo.png)

### <a name="to-add-a-database-in-azure-sql-database"></a>新增 Azure SQL Database 中的資料庫

在 [成員資料庫] 區段中，選取 [新增 Azure SQL 資料庫]，可選擇性地將 Azure SQL Database 中的資料庫新增至同步群組。 [設定 Azure SQL Database]**** 頁面隨即開啟。

  ![步驟 2 - 設定資料庫](./media/sql-data-sync-sql-server-configure/steptwo-configure.png)

  在 [設定 Azure SQL Database]**** 頁面上，變更下列設定：

  | 設定                       | 描述 |
  | ----------------------------- | ------------------------------------------------- |
  | **同步成員名稱** | 提供新同步成員的名稱。 這個名稱與資料庫本身的名稱不同。 |
  | **訂用帳戶** | 選取相關聯的 Azure 訂用帳戶以便計費。 |
  | **Azure SQL Server** | 選取現有的伺服器。 |
  | **Azure SQL Database** | 在 SQL Database 中選取現有的資料庫。 |
  | **同步方向** | 選取 [雙向同步]****、[至中樞]**** 或 [從中樞]****。 |
  | **使用者名稱**和**密碼** | 輸入成員資料庫所在伺服器的現有認證。 請不要在此區段輸入「新」** 的認證。 |

  選取 [確定]**** 並等候新的同步成員建立和部署完成。

<a name="add-on-prem"></a>

### <a name="to-add-a-sql-server-database"></a>新增 SQL Server 資料庫

在 [成員資料庫] 區段中，選取 [新增內部部署資料庫]，可選擇性地將 SQL Server 資料庫新增至同步群組。 [設定內部部署]**** 頁面隨即開啟，以便您執行下列步驟：

1. 選取 [選擇同步代理程式閘道]****。 [選取同步代理程式]**** 頁面隨即開啟。

   ![建立同步代理程式](./media/sql-data-sync-sql-server-configure/steptwo-agent.png)

1. 在 [選擇同步代理程式]**** 頁面上，選擇要使用現有代理程式，或建立代理程式。

   如果您選擇 [現有代理程式]****，請從清單中選取現有的代理程式。

   如果您選擇 [建立新的代理程式]****，請執行下列步驟：

   1. 從提供的連結下載資料同步代理程式，並安裝在 SQL Server 所在的電腦上。 您也可以直接從 [Azure SQL Data Sync Agent](https://www.microsoft.com/download/details.aspx?id=27693) 下載代理程式。

      > [!IMPORTANT]
      > 您必須在防火牆開啟輸出 TCP 連接埠 1433，以讓用戶端代理程式和伺服器通訊。

   1. 輸入代理程式的名稱。

   1. 選取 [建立並產生金鑰]**** 並將代理程式金鑰複製到剪貼簿。

   1. 選取 [確定]**** 以關閉 [選取同步代理程式]**** 頁面。

1. 在 SQL Server 電腦上，找出並執行用戶端同步代理程式應用程式。

   ![資料同步用戶端代理程式應用程式](./media/sql-data-sync-sql-server-configure/datasync-preview-clientagent.png)

    1. 在同步處理代理程式應用程式中，選取 [提交代理程式金鑰]****。 [同步中繼資料的資料庫組態]**** 對話方塊隨即開啟。

    1. 在 [同步中繼資料的資料庫組態]**** 對話方塊中，貼上從 Azure 入口網站複製的代理程式金鑰。 還要提供輸入中繼資料資料庫所在伺服器的現有認證。 (如果您已建立中繼資料資料庫，此資料庫會位在和中樞資料庫相同的伺服器)。選取 [確定] 並等待完成組態。

        ![輸入代理程式金鑰和伺服器認證](./media/sql-data-sync-sql-server-configure/datasync-preview-agent-enterkey.png)

        > [!NOTE]
        > 如果收到防火牆錯誤，請在 Azure 上建立防火牆規則，以允許來自 SQL Server 電腦的傳入流量。 您可以在入口網站或 SQL Server Management Studio (SSMS) 中手動建立規則。 在 SSMS 中，輸入 <hub_database_name>.database.windows.net 作為名稱，以連線到 Azure 上的中樞資料庫。

    1. 選取 [註冊]****，以向代理程式註冊 SQL Server 資料庫。 [SQL Server 組態]**** 對話方塊隨即開啟。

        ![新增和設定 SQL Server 資料庫](./media/sql-data-sync-sql-server-configure/datasync-preview-agent-adddb.png)

    1. 在 [SQL Server 組態]**** 對話方塊中，選擇使用 SQL Server 驗證或 Windows 驗證來連線。 如果您選擇 SQL Server 驗證，請輸入現有的認證。 提供 SQL Server 名稱和您要同步之資料庫的名稱，然後選取 [測試連線]**** 以測試您的設定。 接著選取 [儲存]****，而已註冊的資料庫會出現在清單中。

        ![現在已註冊 SQL Server 資料庫](./media/sql-data-sync-sql-server-configure/datasync-preview-agent-dbadded.png)

    1. 關閉用戶端同步代理程式應用程式。

1. 在入口網站中的 [設定內部部署]**** 頁面上，選取 [選取資料庫]****。

1. 在 [選取資料庫]**** 頁面上的 [同步成員名稱]**** 欄位中，提供新同步成員的名稱。 這個名稱與資料庫本身的名稱不同。 從清單中選取資料庫。 在 [同步方向]**** 欄位中，選取 [雙向同步]****、[至中樞]**** 或 [從中樞]****。

    ![選取內部部署資料庫](./media/sql-data-sync-sql-server-configure/datasync-preview-selectdb.png)

1. 選取 [確定]**** 以關閉 [選取資料庫]**** 頁面。 然後選取 [確定]**** 以關閉 [設定內部部署]**** 頁面，並等候系統建立並部署新的同步成員。 最後，選取 [確定]**** 以關閉 [選取同步成員]**** 頁面。

> [!NOTE]
> 若要連接到 [SQL 資料同步] 和本機代理程式，請將您的使用者名稱新增至 DataSync_Executor** 角色。 資料同步會在 SQL Server 執行個體上建立此角色。

## <a name="configure-sync-group"></a>設定同步群組

建立並部署新的同步群組成員之後，系統會在 [新增同步群組]**** 頁面中醒目提示 [設定同步群組 (步驟 3)]****。

![步驟 3 設定](./media/sql-data-sync-sql-server-configure/stepthree.png)

1. 在 [資料表]**** 頁面上，從同步群組成員清單中選取資料庫，然後選取 [重新整理結構描述]****。

1. 從清單中，選取您要同步的資料表。預設會選取所有的資料行，所以請取消選取您不想同步的資料行核取方塊。請務必保留選取的主索引鍵資料行。

1. 選取 [儲存]。

1. 根據預設，資料庫在排程或手動執行後才會同步。 若要執行手動同步，請在 Azure 入口網站中瀏覽至 SQL Database 中的資料庫，選取 [同步至其他資料庫]，然後選取同步群組。 [資料同步] 頁面隨即開啟。 選取 [同步]****。

    ![手動同步](./media/sql-data-sync-sql-server-configure/datasync-sync.png)

## <a name="faq"></a>常見問題集

**資料同步多久會同步我的資料一次？**

同步處理之間的最短間隔時間為 5 分鐘。

**SQL 資料同步是否會完整地建立資料表？**

如果目的地資料庫中遺漏同步結構描述資料表，則 SQL 資料同步會使用您選取的資料行加以建立。 然而，此行為不會導致結構描述完整無缺，原因如下：

- 只有您選取的資料行會建立在目的地資料表中。 未選取的資料行會被忽略。
- 只有選取的資料行索引會建立在目的地資料表中。 若為未選取的資料行，其索引會被忽略。
- 不會建立 XML 型別資料行的索引。
- 不會建立 CHECK 條件約束。
- 不會建立來源資料表上的觸發程序。
- 不會建立檢視和預存程序。

由於有這些限制，我們的建議事項如下：

- 針對生產環境，自行建立完整無缺的結構描述。
- 試驗服務時，請使用自動佈建功能。

**為什麼我會看到並非自己建立的資料表？**

資料同步會在資料庫中建立額外資料表，以便進行變更追蹤。 請勿刪除它們，否則資料同步無法正常運作。

**同步處理之後我的資料會聚合嗎？**

不一定。 在有一個中樞和三個支點 (A、B 及 C) 的同步群組中，同步處理是以中樞對 A 點、中樞對 B 點及中樞對 C 點的方式進行。如果在中樞對 A 點同步「之後」**，才變更資料庫 A，在進行下次同步工作之前，該變更不會寫入資料庫 B 或資料庫 C。

**如何變更同步群組中的結構描述？**

手動進行及傳播所有的結構描述變更。

1. 以手動方式將結構描述變更複寫至中樞和所有的同步成員。
1. 更新同步結構描述。

新增資料表和資料行：

新的資料表和資料行不會影響目前的同步處理，而在新增至同步結構描述之前，資料同步會忽略它們。 新增資料庫物件時，請依照下列順序執行：

1. 將新的資料表或資料行新增至中樞和所有的同步成員。
1. 將新的資料表或資料行新增至同步結構描述。
1. 開始將值插入新的資料表和資料行。

變更資料行的資料類型：

當您變更現有資料行的資料類型時，只要新的值符合同步結構描述中定義的原始資料類型，資料同步就會繼續運作。 例如，如果您將來源資料庫中的類型從 **int** 變更為 **bigint**，資料同步就會繼續運作，直到您將對於 **int** 資料類型太大的值插入為止。 若要完成變更，請以手動方式將結構描述變更複寫至中樞和所有的同步成員，然後更新同步結構描述。

**如何使用資料同步匯出和匯入資料庫？**

將資料庫匯出為 .bacpac** 檔案，並匯入該檔案以建立資料庫之後，請執行下列動作以在新的資料庫中使用資料同步：

1. 使用[這個指令碼](https://github.com/vitomaz-msft/DataSyncMetadataCleanup/blob/master/Data%20Sync%20complete%20cleanup.sql)清除新資料庫上的資料同步物件和額外資料表。 此指令碼會刪除資料庫中所有必要的資料同步物件。
1. 利用新資料庫重新建立同步群組。 如果您不再需要舊有的同步群組，請將它刪除。

**哪裡可以找到用戶端代理程式相關資訊？**

如需有關用戶端代理程式的常見問題集，請參閱 [Azure 常見問題集](sql-data-sync-agent-overview.md#agent-faq)。

## <a name="next-steps"></a>後續步驟

恭喜！ 您已建立一個同時包含 SQL Database 執行個體與 SQL Server 資料庫的同步群組。

如需 SQL 資料同步的詳細資訊，請參閱：

- [適用於 Azure SQL Data Sync 的 Data Sync Agent](sql-data-sync-agent-overview.md)
- [最佳做法](sql-data-sync-best-practices.md)和[如何針對 Azure SQL 資料同步問題進行疑難排解](sql-data-sync-troubleshoot.md)
- [使用 Azure 監視器記錄監視 SQL 資料同步](sql-data-sync-monitor-sync.md)
- 使用 [Transact-SQL](sql-data-sync-update-sync-schema.md) 或 [PowerShell](scripts/update-sync-schema-in-sync-group.md) 更新同步結構描述

如需 SQL Database 的詳細資訊，請參閱：

- [SQL Database 概觀](sql-database-paas-overview.md)
- [資料庫生命週期管理](https://msdn.microsoft.com/library/jj907294.aspx)
