---
title: 教學課程：將 MySQL online 遷移至適用於 MySQL 的 Azure 資料庫
titleSuffix: Azure Database Migration Service
description: 了解如何使用 Azure 資料庫移轉服務，在線上將內部部署的 MySQL 移轉至適用於 MySQL 的 Azure 資料庫。
services: dms
author: pochiraju
ms.author: rajpo
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019
ms.topic: article
ms.date: 01/08/2020
ms.openlocfilehash: 50787a5bbfdc9baddfa4307247e8b505be6e3003
ms.sourcegitcommit: d45fd299815ee29ce65fd68fd5e0ecf774546a47
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/04/2020
ms.locfileid: "78273249"
---
# <a name="tutorial-migrate-mysql-to-azure-database-for-mysql-online-using-dms"></a>教學課程：使用 DMS 在線上將 MySQL 移轉至適用於 MySQL 的 Azure 資料庫

您可以使用 Azure 資料庫移轉服務，在最短的停止運作時間內將資料庫從內部部署 MySQL 執行個體移轉至[適用於 MySQL 的 Azure 資料庫](https://docs.microsoft.com/azure/mysql/)。 換句話說，移轉可在最短的應用程式停止運作時間內完成。 在本教學課程中，您會在 Azure 資料庫移轉服務中使用線上移轉活動，將 **Employees** 範例資料庫從內部部署的 MySQL 5.7 執行個體移轉至適用於 MySQL 的 Azure 資料庫。

在本教學課程中，您會了解如何：
> [!div class="checklist"]
>
> * 使用 mysqldump 公用程式移轉範例結構描述。
> * 建立 Azure 資料庫移轉服務的執行個體。
> * 使用 Azure 資料庫移轉服務來建立移轉專案。
> * 執行移轉。
> * 監視移轉。

> [!NOTE]
> 若要使用「Azure 資料庫移轉服務」來執行線上移轉，必須根據「進階」定價層建立執行個體。

> [!IMPORTANT]
> 為了獲得最佳的移轉體驗，Microsoft 建議在目標資料庫所在的同一個 Azure 區域中，建立 Azure 資料庫移轉服務的執行個體。 跨區域或地理位置移動資料可能使移轉程序變慢，並產生錯誤。

## <a name="prerequisites"></a>Prerequisites

若要完成本教學課程，您需要：

* 下載並安裝 [MySQL 社群版](https://dev.mysql.com/downloads/mysql/) 5.6 或 5.7。 內部部署 MySQL 版本必須符合「適用於 MySQL 的 Azure 資料庫」的版本。 例如，MySQL 5.6 只能移轉至「適用於 MySQL 的 Azure 資料庫」5.6，而無法升級至 5.7。
* [在適用於 MySQL 的 Azure 資料庫中建立執行個體](https://docs.microsoft.com/azure/mysql/quickstart-create-mysql-server-database-using-azure-portal)。 如需關於如何使用 Azure 入口網站連接及建立資料庫的詳細資訊，請參閱[使用 MySQL Workbench 來連接及查詢資料](https://docs.microsoft.com/azure/mysql/connect-workbench)一文。  
* 使用 Azure Resource Manager 部署模型建立 Azure 資料庫移轉服務的 Microsoft Azure 虛擬網路，以使用[ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction)或[VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways)為您的內部部署來源伺服器提供站對站連線能力。 如需有關建立虛擬網路的詳細資訊，請參閱[虛擬網路檔](https://docs.microsoft.com/azure/virtual-network/)，特別是快速入門文章，其中包含逐步解說的詳細資料。

    > [!NOTE]
    > 在虛擬 networkNet 設定期間，如果您搭配使用 ExpressRoute 與網路對等互連，請將下列服務[端點](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview)新增至將布建服務的子網：
    >
    > * 目標資料庫端點 (例如，SQL 端點、Cosmos DB 端點等)
    > * 儲存體端點
    > * 服務匯流排端點
    >
    > 此為必要設定，因為 Azure 資料庫移轉服務沒有網際網路連線。

* 請確定您的虛擬網路網路安全性群組規則不會對 Azure 資料庫移轉服務封鎖下列輸入通訊埠：443、53、9354、445、12000。 如需虛擬網路 NSG 流量篩選的詳細資訊，請參閱[使用網路安全性群組來篩選網路流量](https://docs.microsoft.com/azure/virtual-network/virtual-network-vnet-plan-design-arm)一文。
* 設定[用於 Database Engine 存取的 Windows 防火牆](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access)。
* 開啟您的 Windows 防火牆以允許 Azure 資料庫移轉服務存取來源 MySQL Server (依預設會使用 TCP 連接埠 3306)。
* 使用來源資料庫前面的防火牆應用裝置時，您可能必須新增防火牆規則，才能讓 Azure 資料庫移轉服務存取來源資料庫，以進行移轉。
* 為適用於 MySQL 的 Azure 資料庫建立伺服器層級的[防火牆規則](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure)，以允許 Azure 資料庫移轉服務存取目標資料庫。 提供用於 Azure 資料庫移轉服務之虛擬網路的子網範圍。
* 來源 MySQL 必須位於支援的 MySQL 社群版上。 若要判斷 MySQL 公用程式或 MySQL Workbench 中的 MySQL 執行個體版本，請執行下列命令：

    ```
    SELECT @@version;
    ```

* 適用於 MySQL 的 Azure 資料庫僅支援 InnoDB 資料表。 若要將 MyISAM 資料表轉換為 InnoDB，請參閱[將資料表從 MyISAM 轉換為 InnoDB](https://dev.mysql.com/doc/refman/5.7/en/converting-tables-to-innodb.html) 一文

* 使用下列組態，在來源資料庫的 my.ini (Windows) 或 my.cnf (Unix) 檔案中啟用二進位記錄：

  * **server_id** = 1 或更大 (僅與 MySQL 5.6 有關)
  * **log bin** =\<路徑 > （僅與 MySQL 5.6 相關）例如： log-bin = E:\ MySQL_logs \binlog
  * **binlog_format** = row
  * **Expire_logs_days** = 5 (建議不要使用零；僅與 MySQL 5.6 有關)
  * **Binlog_row_image** = full (僅與 MySQL 5.6 有關)
  * **log_slave_updates** = 1

* 使用者必須有具備下列權限的 ReplicationAdmin 角色：

  * **REPLICATION CLIENT** - 只有變更處理工作需要此權限。 換句話說，「僅限完整載入」工作並不需要此權限。
  * **REPLICATION REPLICA** - 只有變更處理工作需要此權限。 換句話說，「僅限完整載入」工作並不需要此權限。
  * **SUPER** - 只有 MySQL 5.6.6 之前的版本需要此權限。

## <a name="migrate-the-sample-schema"></a>移轉範例結構描述

若要完成所有資料表物件 (例如資料表結構描述、索引和預存程序)，我們必須從來源資料庫擷取結構描述，並套用至資料庫。 若要擷取結構描述，您可以使用 mysqldump 搭配 `--no-data` 參數。

假設您在內部部署系統中有 MySQL **員工**範例資料庫，則使用 mysqldump 命令進行結構描述移轉的命令將是：

```
mysqldump -h [servername] -u [username] -p[password] --databases [db name] --no-data > [schema file path]
```

例如：

```
mysqldump -h 10.10.123.123 -u root -p --databases employees --no-data > d:\employees.sql
```

若要將結構描述匯入至「適用於 MySQL 的 Azure 資料庫」目標，請執行下列命令：

```
mysql.exe -h [servername] -u [username] -p[password] [database]< [schema file path]
 ```

例如：

```
mysql.exe -h shausample.mysql.database.azure.com -u dms@shausample -p employees < d:\employees.sql
 ```

如果您的結構描述中有外部索引鍵，則移轉的初始載入和持續同步將會失敗。  請在 MySQL Workbench 中執行下列指令碼，以擷取 Drop 外部索引鍵指令碼和 Add 外部索引鍵指令碼。

```
SET group_concat_max_len = 8192;
    SELECT SchemaName, GROUP_CONCAT(DropQuery SEPARATOR ';\n') as DropQuery, GROUP_CONCAT(AddQuery SEPARATOR ';\n') as AddQuery
    FROM
    (SELECT
    KCU.REFERENCED_TABLE_SCHEMA as SchemaName,
    KCU.TABLE_NAME,
    KCU.COLUMN_NAME,
    CONCAT('ALTER TABLE ', KCU.TABLE_NAME, ' DROP FOREIGN KEY ', KCU.CONSTRAINT_NAME) AS DropQuery,
    CONCAT('ALTER TABLE ', KCU.TABLE_NAME, ' ADD CONSTRAINT ', KCU.CONSTRAINT_NAME, ' FOREIGN KEY (`', KCU.COLUMN_NAME, '`) REFERENCES `', KCU.REFERENCED_TABLE_NAME, '` (`', KCU.REFERENCED_COLUMN_NAME, '`) ON UPDATE ',RC.UPDATE_RULE, ' ON DELETE ',RC.DELETE_RULE) AS AddQuery
    FROM INFORMATION_SCHEMA.KEY_COLUMN_USAGE KCU, information_schema.REFERENTIAL_CONSTRAINTS RC
    WHERE
      KCU.CONSTRAINT_NAME = RC.CONSTRAINT_NAME
      AND KCU.REFERENCED_TABLE_SCHEMA = RC.UNIQUE_CONSTRAINT_SCHEMA
  AND KCU.REFERENCED_TABLE_SCHEMA = 'SchemaName') Queries
  GROUP BY SchemaName;
 ```

執行查詢結果中的 Drop 外部索引鍵 (這是第二個資料行)，以卸除外部索引鍵。

> [!IMPORTANT]
> 如果使用備份匯入資料，執行 mysqldump 時以手動方式或使用 --skip-definer 命令移除 CREATE DEFINER 命令。 DEFINER 需要進階的權限才能建立，且在適用於 MySQL 的 Azure 資料庫中會受到限制。

如果您的資料中有觸發程序 (Insert 或 Update 觸發程序)，該觸發程序將會在來源中的複寫資料傳入之前，在目標中強制執行資料完整性。 建議您在移轉期間停用目標上所有資料表中的觸發程序，並在移轉完成後再啟用觸發程序。

若要停用目標資料庫中的觸發程序，請使用下列命令：

```
SELECT Concat('DROP TRIGGER ', Trigger_Name, ';') FROM  information_schema.TRIGGERS WHERE TRIGGER_SCHEMA = 'your_schema';
```

## <a name="register-the-microsoftdatamigration-resource-provider"></a>註冊 Microsoft.DataMigration 資源提供者

1. 登入 Azure 入口網站，選取 [所有服務]，然後選取 [訂用帳戶]。

   ![顯示入口網站訂用帳戶](media/tutorial-mysql-to-azure-mysql-online/portal-select-subscriptions.png)

2. 選取您要在其中建立 Azure 資料庫移轉服務執行個體的訂用帳戶，然後選取 [資源提供者]。

    ![顯示資源提供者](media/tutorial-mysql-to-azure-mysql-online/portal-select-resource-provider.png)

3. 搜尋移轉，然後在 [Microsoft.DataMigration] 的右邊，選取 [註冊]。

    ![註冊資源提供者](media/tutorial-mysql-to-azure-mysql-online/portal-register-resource-provider.png)

## <a name="create-a-dms-instance"></a>建立 DMS 執行個體

1. 在 Azure 入口網站中，選取 [+ 建立資源]，搜尋「Azure 資料庫移轉服務」，然後從下拉式清單選取 [Azure 資料庫移轉服務]。

    ![Azure Marketplace](media/tutorial-mysql-to-azure-mysql-online/portal-marketplace.png)

2. 在 [Azure 資料庫移轉服務] 畫面上，選取 [建立]。

    ![建立 Azure 資料庫移轉服務執行個體](media/tutorial-mysql-to-azure-mysql-online/dms-create1.png)
  
3. 在 [建立移轉服務] 畫面上，指定服務的名稱、訂用帳戶，以及新的或現有的資源群組。

4. 選取現有的虛擬網路，或建立一個新的。

    虛擬網路會為 Azure 資料庫移轉服務提供來源 SQL Server 和目標 Azure SQL Database 實例的存取權。

    如需有關如何在 Azure 入口網站中建立虛擬網路的詳細資訊，請參閱[使用 Azure 入口網站建立虛擬網路](https://aka.ms/DMSVnet)一文。

5. 選取定價層。

    如需成本和定價層的詳細資訊，請參閱[定價分頁](https://aka.ms/dms-pricing)。

    ![設定 Azure 資料庫移轉服務執行個體設定](media/tutorial-mysql-to-azure-mysql-online/dms-settings3.png)

6. 選取 [建立] 以建立服務。

## <a name="create-a-migration-project"></a>建立移轉專案

建立服務之後，請在 Azure 入口網站中找出該服務，然後建立新的移轉專案。

1. 在 Azure 入口網站中，選取 [所有服務]，搜尋 Azure 資料庫移轉服務，然後選取 [Azure 資料庫移轉服務]。

      ![找出 Azure 資料庫移轉服務的所有執行個體](media/tutorial-mysql-to-azure-mysql-online/dms-search.png)

2. 在 [Azure 資料庫移轉服務] 畫面上，搜尋您建立的 Azure 資料庫移轉服務執行個體名稱，然後選取該執行個體。

     ![找出 Azure 資料庫移轉服務的執行個體](media/tutorial-mysql-to-azure-mysql-online/dms-instance-search.png)

3. 選取 [+ 新增移轉專案]。
4. 在 [新增移轉專案] 畫面上指定專案名稱，並在 [來源伺服器類型] 文字方塊中中選取 [MySQL]，然後在 [目標伺服器類型] 文字方塊中選取 [AzureDbForMySQL]。
5. 在 [選擇活動類型] 區段中，選取 [線上資料移轉]

    ![建立資料庫移轉服務專案](media/tutorial-mysql-to-azure-mysql-online/dms-create-project4.png)

    > [!NOTE]
    > 或者，您可以選擇 [僅建立專案] 以立即建立移轉專案，並於後續再執行移轉。

6. 選取 [儲存]，並記下使用 DMS 成功移轉資料的需求，然後選取 [建立及執行活動]。

## <a name="specify-source-details"></a>指定來源詳細資料

1. 在 [新增來源詳細資料] 畫面上，指定來源 MySQL 執行個體的連線詳細資料。

    ![新增來源詳細資料畫面](media/tutorial-mysql-to-azure-mysql-online/dms-add-source-details.png)

## <a name="specify-target-details"></a>指定目標詳細資料

1. 選取 [儲存]，然後在 [目標詳細資料] 畫面上指定目標「適用於 MySQL 的 Azure 資料庫」伺服器的連線詳細資料；此伺服器是使用 mysqldump 將 **Employees** 結構描述部署到的預先佈建「適用於 MySQL 的 Azure 資料庫」執行個體。

    ![目標詳細資料畫面](media/tutorial-mysql-to-azure-mysql-online/dms-add-target-details.png)

2. 選取 [儲存]，然後在 [對應到目標資料庫] 畫面上，對應要進行移轉的來源和目標資料庫。

    如果目標資料庫包含與來源資料庫相同的資料庫名稱，Azure 資料庫移轉服務依預設會選取目標資料庫。

    ![對應到目標資料庫](media/tutorial-mysql-to-azure-mysql-online/dms-map-target-details.png)
   > [!NOTE] 
   > 雖然您可以在此步驟中選取多個資料庫，但 Azure 資料庫移轉服務的每個實例最多可支援四個資料庫來進行並行遷移。 此外，訂用帳戶中的每個區域都有兩個 Azure 資料庫移轉服務實例的限制。 例如，如果您有要遷移的40資料庫，您只能同時遷移其中八個，而且只有在您已建立兩個 Azure 資料庫移轉服務實例時才可以。

3. 選取 [儲存]，在 [移轉摘要] 畫面的 [活動名稱] 文字方塊中，指定移轉活動的名稱，然後檢閱摘要，以確定來源和目標詳細資料都與您先前的指定相符。

    ![移轉摘要](media/tutorial-mysql-to-azure-mysql-online/dms-migration-summary.png)

## <a name="run-the-migration"></a>執行移轉

* 選取 [執行移轉]。

    [移轉活動] 視窗隨即出現，且活動的 [狀態] 為 [正在初始化]。

## <a name="monitor-the-migration"></a>監視移轉

1. 在移轉活動畫面上，選取 [重新整理] 以更新顯示，直到移轉的 [狀態] 顯示為 [完成] 為止。

     ![活動狀態 - 完成](media/tutorial-mysql-to-azure-mysql-online/dms-activity-completed.png)

2. 在 [資料庫名稱] 下方選取特定資料庫，以顯示 [載入完整資料] 和 [累加式資料同步] 作業的移轉狀態。

    完整資料載入會顯示初始載入移轉狀態，而累加式資料同步會顯示異動資料擷取 (CDC) 狀態。

     ![活動狀態 - 完整載入已完成](media/tutorial-mysql-to-azure-mysql-online/dms-activity-full-load-completed.png)

     ![活動狀態 - 累加式資料同步](media/tutorial-mysql-to-azure-mysql-online/dms-activity-incremental-data-sync.png)

## <a name="perform-migration-cutover"></a>執行完全移轉

初始完整載入完成後，資料庫會標示為 [已可執行完全移轉]。

1. 當您準備好要完成資料庫移轉後，請選取 [開始完全移轉]。

    ![開始完全移轉](media/tutorial-mysql-to-azure-mysql-online/dms-start-cutover.png)

2. 確實停止所有傳入來源資料庫的交易；等到 [暫止的變更] 計數器顯示為 **0** 為止。
3. 選取 [確認]，然後選取 [套用]。
4. 當資料庫移轉狀態顯示為 [已完成] 後，請將應用程式連線至新的目標 Azure SQL Database。

## <a name="next-steps"></a>後續步驟

* 如需在線上移轉至「適用於 MySQL 的 Azure 資料庫」時的已知問題和限制，請參閱[適用於 MySQL 的 Azure 資料庫線上移轉的已知問題和因應措施](known-issues-azure-mysql-online.md)一文。
* 如需 Azure 資料庫移轉服務的相關資訊，請參閱[什麼是 Azure 資料庫移轉服務？](https://docs.microsoft.com/azure/dms/dms-overview)一文。
* 如需適用於 MySQL 的 Azure 資料庫相關資訊，請參閱[什麼是適用於 MySQL 的 Azure 資料庫？](https://docs.microsoft.com/azure/mysql/overview)一文。
