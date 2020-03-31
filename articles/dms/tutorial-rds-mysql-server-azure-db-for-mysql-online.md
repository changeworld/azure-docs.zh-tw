---
title: 教程：將 RDS MySQL 連線遷移到 MySQL 的 Azure 資料庫
titleSuffix: Azure Database Migration Service
description: 了解如何使用 Azure 資料庫移轉服務，在線上從 RDS MySQL 遷移至適用於 MySQL 的 Azure 資料庫。
services: dms
author: HJToland3
ms.author: jtoland
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019
ms.topic: article
ms.date: 01/08/2020
ms.openlocfilehash: c34de48d0184057f42d1b779abee56e1fa9ac169
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "78255155"
---
# <a name="tutorial-migrate-rds-mysql-to-azure-database-for-mysql-online-using-dms"></a>教程：使用 DMS 將 RDS MySQL 遷移到 Azure 資料庫，使 MySQL 連線

您可以使用 Azure 資料庫移轉服務，將資料庫從 RDS MySQL 執行個體遷移至[適用於 MySQL 的 Azure 資料庫](https://docs.microsoft.com/azure/mysql/)，同時讓來源資料庫在移轉期間保持連線。 換句話說，可在最短的應用程式停機時間內完成移轉。 在本教學課程中，您會在 Azure 資料庫移轉服務中使用線上移轉活動，將 **Employees** 範例資料庫從 RDS MySQL 執行個體遷移至適用於 MySQL 的 Azure 資料庫。

在本教學課程中，您會了解如何：
> [!div class="checklist"]
>
> * 使用 mysqldump 和 mysql 公用城市遷移範例結構描述。
> * 建立 Azure 資料庫移轉服務的執行個體。
> * 使用 Azure 資料庫移轉服務來建立移轉專案。
> * 執行移轉。
> * 監視移轉。

> [!NOTE]
> 若要使用「Azure 資料庫移轉服務」來執行線上移轉，必須根據「進階」定價層建立執行個體。 有關詳細資訊，請參閱 Azure 資料庫移轉服務[定價](https://azure.microsoft.com/pricing/details/database-migration/)頁。

> [!IMPORTANT]
> 為了獲得最佳的移轉體驗，Microsoft 建議在目標資料庫所在的同一個 Azure 區域中，建立 Azure 資料庫移轉服務的執行個體。 跨區域或地理位置移動資料可能使移轉程序變慢，並產生錯誤。

[!INCLUDE [online-offline](../../includes/database-migration-service-offline-online.md)]

本文說明如何在線上從 RDS MySQL 執行個體遷移至適用於 MySQL 的 Azure 資料庫。

## <a name="prerequisites"></a>Prerequisites

若要完成本教學課程，您需要：

* 確保來源 MySQL 伺服器正在執行支援的 MySQL 社群版本。 若要判斷 MySQL 執行個體的版本，請在 mysql 公用程式或 MySQL Workbench 中執行下列命令：

    ```
    SELECT @@version;
    ```

    如需詳細資訊，請參閱[支援的適用於 MySQL 的 Azure 資料庫版本](https://docs.microsoft.com/azure/mysql/concepts-supported-versions)一文。

* 下載並安裝 [MySQL **Employees** 範例資料庫](https://dev.mysql.com/doc/employee/en/employees-installation.html)。
* 建立[適用於 MySQL 的 Azure 資料庫](https://docs.microsoft.com/azure/mysql/quickstart-create-mysql-server-database-using-azure-portal)執行個體。
* 通過使用 Azure 資源管理器部署模型為 Azure 資料庫移轉服務創建 Microsoft Azure 虛擬網路，該模型通過使用[ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction)或[VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways)提供到本地源伺服器的網站到網站的連接。 有關創建虛擬網路的詳細資訊，請參閱[虛擬網路文檔](https://docs.microsoft.com/azure/virtual-network/)，尤其是包含分步詳細資訊的快速入門文章。
* 確保虛擬網路網路安全性群組規則不會阻止以下到 Azure 資料庫移轉服務的入站通訊連接埠：443、53、9354、445 和 12000。 有關虛擬網路 NSG 流量篩選的更多詳細資訊，請參閱文章["使用網路安全性群組篩選網路流量](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg)"。
* 設定 [Windows 防火牆](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access) (或是 Linux 防火牆)，以允許資料庫引擎的存取。 針對 MySQL 伺服器，請允許連接埠 3306 以進行連線。

> [!NOTE]
> 適用於 MySQL 的 Azure 資料庫僅支援 InnoDB 資料表。 若要將 MyISAM 資料表轉換為 InnoDB，請參閱[將資料表從 MyISAM 轉換為 InnoDB](https://dev.mysql.com/doc/refman/5.7/en/converting-tables-to-innodb.html) 一文

### <a name="set-up-aws-rds-mysql-for-replication"></a>設定 AWS RDS MySQL 以進行複寫

1. 若要建立新的參數群組，請遵循 AWS 在 [MySQL 資料庫記錄檔](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/USER_LogAccess.Concepts.MySQL.html)一文中提供的指示 (位於**二進位記錄格式**一節)。
2. 使用下列組態建立新的參數群組：
    * binlog_format = row
    * binlog_checksum = NONE
3. 儲存新的參數群組。
4. 建立新的參數群組與 RDS MySQL 實例之間的關聯。 可能需要重新開機。

## <a name="migrate-the-schema"></a>移轉結構描述

1. 從來源資料庫擷取結構描述並套用到目標資料庫，以完成所有資料庫物件 (例如資料表結構描述、索引和預存程序) 的移轉。

    僅遷移結構描述的最簡單方法是使用 mysqldump with the --no-data 參數。 用於前結構描述的命令如下：

    ```
    mysqldump -h [servername] -u [username] -p[password] --databases [db name] --no-data > [schema file path]
    ```
    
    例如，若要傾印 **Employees** 資料庫的結構描述檔案，請使用下列命令：
    
    ```
    mysqldump -h 10.10.123.123 -u root -p --databases employees --no-data > d:\employees.sql
    ```

2. 將結構描述匯入目標服務，也就是適用於 MySQL 的 Azure 資料庫。 若要還原結構描述傾印檔案，請執行下列命令：

    ```
    mysql.exe -h [servername] -u [username] -p[password] [database]< [schema file path]
    ```

    例如，若要匯入 **Employees** 資料庫的結構描述：

    ```
    mysql.exe -h shausample.mysql.database.azure.com -u dms@shausample -p employees < d:\employees.sql
    ```

3. 如果您的結構描述中有外部索引鍵，則移轉的初始載入和持續同步將會失敗。 若要擷取 drop 外部索引鍵指令碼，並在目的地 (適用於 MySQL 的 Azure 資料庫) 新增外部索引鍵，請在 MySQL Workbench 中執行下列指令碼：

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

4. 執行查詢結果中的 drop 外部索引鍵 (這是第二個資料行)，以卸除外部索引鍵。

5. 如果資料中有觸發程序 (insert 或 update 觸發程序)，該觸發程序將會在目標中強制執行資料完整性，再複製來源中的資料。 建議您在移轉期間停用「目標上」** 所有資料表中的觸發程序，然後在移轉完成後啟用觸發程序。

    若要在目標資料庫中停用觸發程序：

    ```
    select concat ('alter table ', event_object_table, ' disable trigger ', trigger_name)
    from information_schema.triggers;
    ```

6. 如果任何資料表中有 ENUM 資料類型的執行個體，建議您暫時在目標資料表中更新為 'character varying' 資料類型。 完成資料複寫後，將資料類型還原為 ENUM。

## <a name="register-the-microsoftdatamigration-resource-provider"></a>註冊 Microsoft.DataMigration 資源提供者

1. 登入 Azure 入口網站，選取 [所有服務]****，然後選取 [訂用帳戶]****。

   ![顯示入口網站訂用帳戶](media/tutorial-rds-mysql-server-azure-db-for-mysql-online/portal-select-subscription1.png)

2. 選取您要在其中建立 Azure 資料庫移轉服務執行個體的訂用帳戶，然後選取 [資源提供者]****。

    ![顯示資源提供者](media/tutorial-rds-mysql-server-azure-db-for-mysql-online/portal-select-resource-provider.png)

3. 搜索遷移，然後搜索 Microsoft 的右側 **。** **Register**

    ![註冊資源提供者](media/tutorial-rds-mysql-server-azure-db-for-mysql-online/portal-register-resource-provider.png)

## <a name="create-an-instance-of-azure-database-migration-service"></a>建立 Azure 資料庫移轉服務的執行個體

1. 在 Azure 入口網站中，選取 [+ 建立資源]****，搜尋「Azure 資料庫移轉服務」，然後從下拉式清單選取 [Azure 資料庫移轉服務]****。

    ![Azure Marketplace](media/tutorial-rds-mysql-server-azure-db-for-mysql-online/portal-marketplace.png)

2. 在 [Azure 資料庫移轉服務]**** 畫面上，選取 [建立]****。

    ![建立 Azure 資料庫移轉服務執行個體](media/tutorial-rds-mysql-server-azure-db-for-mysql-online/dms-create1.png)
  
3. 在 [建立移轉服務]**** 畫面上，指定服務的名稱、訂用帳戶，以及新的或現有的資源群組。

4. 選取您要在其中建立 Azure 資料庫移轉服務執行個體的位置。

5. 選擇現有虛擬網路或創建新虛擬網路。

    虛擬網路為 Azure 資料庫移轉服務提供對源 MySQL 實例和 MySQL 實例的目標 Azure 資料庫的存取權限。

    有關如何在 Azure 門戶中創建虛擬網路的詳細資訊，請參閱[使用 Azure 門戶創建虛擬網路](https://aka.ms/DMSVnet)的文章。

6. 選擇定價層;對於此線上遷移，請務必選擇高級：4vCores 定價層。

    ![設定 Azure 資料庫移轉服務執行個體設定](media/tutorial-rds-mysql-server-azure-db-for-mysql-online/dms-settings3.png)

7. 選取 [建立]**** 以建立服務。

## <a name="create-a-migration-project"></a>建立移轉專案

建立服務之後，請在 Azure 入口網站中找出該服務，然後建立新的移轉專案。

1. 在 Azure 入口網站中，選取 [所有服務]****，搜尋 Azure 資料庫移轉服務，然後選取 [Azure 資料庫移轉服務]****。

      ![找出 Azure 資料庫移轉服務的所有執行個體](media/tutorial-rds-mysql-server-azure-db-for-mysql-online/dms-search.png)

2. 在 [Azure 資料庫移轉服務]**** 畫面上，搜尋您建立的 Azure 資料庫移轉服務執行個體名稱，然後選取該執行個體。

     ![找出您的 Azure 資料庫移轉服務執行個體](media/tutorial-rds-mysql-server-azure-db-for-mysql-online/dms-instance-search.png)

3. 選取 [+ 新增移轉專案]****。
4. 在 [新增移轉專案]**** 畫面上指定專案名稱，並在 [來源伺服器類型]**** 文字方塊中選取 [MySQL]****，然後在 [目標伺服器類型]**** 文字方塊中選取 [AzureDbForMySQL]****。
5. 在 **"選擇活動類型"** 部分中，選擇**連線資料移轉**。

    > [!IMPORTANT]
    > 請務必選取 [線上資料移轉]****；此案例不支援離線移轉。

    ![建立資料庫移轉服務專案](media/tutorial-rds-mysql-server-azure-db-for-mysql-online/dms-create-project6.png)

    > [!NOTE]
    > 或者，您可以選擇 [僅建立專案]**** 以立即建立移轉專案，並於後續再執行移轉。

6. 選取 [儲存]****。

7. 選取 [建立及執行活動]****，以建立專案並執行移轉活動。

    > [!NOTE]
    > 請記下在專案建立刀鋒視窗中設定線上移轉所需的必要條件。

## <a name="specify-source-details"></a>指定來源詳細資料

* 在 [移轉來源詳細資料]**** 畫面上，指定來源 MySQL 執行個體的連線詳細資料。

   ![來源詳細資料](media/tutorial-rds-mysql-server-azure-db-for-mysql-online/dms-source-details5.png)

## <a name="specify-target-details"></a>指定目標詳細資料

1. 選取 [儲存]****，然後在 [目標詳細資料]**** 畫面上，指定目標「適用於 MySQL 的 Azure 資料庫」伺服器的連線詳細資料；該伺服器是使用 MySQLDump 預先佈建並已部署 **Employees** 結構描述。

    ![選取目標](media/tutorial-rds-mysql-server-azure-db-for-mysql-online/dms-select-target5.png)

2. 選取 [儲存]****，然後在 [對應到目標資料庫]**** 畫面上，對應要進行移轉的來源和目標資料庫。

    如果目標資料庫包含與來源資料庫相同的資料庫名稱，Azure 資料庫移轉服務依預設會選取目標資料庫。

    ![對應到目標資料庫](media/tutorial-rds-mysql-server-azure-db-for-mysql-online/dms-map-targets-activity5.png)

3. 選取 [儲存]****，在 [移轉摘要]**** 畫面的 [活動名稱]**** 文字方塊中，指定移轉活動的名稱，然後檢閱摘要，以確定來源和目標詳細資料都與您先前的指定相符。

    ![移轉摘要](media/tutorial-rds-mysql-server-azure-db-for-mysql-online/dms-migration-summary2.png)

## <a name="run-the-migration"></a>執行移轉

* 選取 [執行移轉]****。

    將顯示遷移使用中視窗，並且活動**的狀態****正在初始化**。

## <a name="monitor-the-migration"></a>監視移轉

1. 在移轉活動畫面上，選取 [重新整理]**** 以更新顯示，直到移轉的 [狀態]**** 顯示為 [執行中]**** 為止。

    ![活動狀態 - 執行中](media/tutorial-rds-mysql-server-azure-db-for-mysql-online/dms-activity-status4.png)

2. 在 [資料庫名稱]**** 下方選取特定資料庫，以取得 [載入完整資料]**** 和 [累加式資料同步]**** 作業的移轉狀態。

    [載入完整資料]**** 會顯示初始載入移轉狀態，而 [累加式資料同步]**** 則會顯示異動資料擷取 (CDC) 狀態。

    ![清查畫面 - 載入完整資料](media/tutorial-rds-mysql-server-azure-db-for-mysql-online/dms-inventory-full-load.png)

    ![清查畫面 - 累加式資料同步](media/tutorial-rds-mysql-server-azure-db-for-mysql-online/dms-inventory-incremental.png)

## <a name="perform-migration-cutover"></a>執行完全移轉

初始完全載入完成後，資料庫將標記為 **"準備剪切**"。

1. 當您準備好要完成資料庫移轉後，請選取 [開始完全移轉]****。

    ![開始完全移轉](media/tutorial-rds-mysql-server-azure-db-for-mysql-online/dms-inventory-start-cutover.png)

2. 確實停止所有傳入來源資料庫的交易；等到 [暫止的變更]**** 計數器顯示為 **0** 為止。
3. 選取 [確認]****，然後選取 [套用]****。
4. 當資料庫移轉狀態顯示為 [已完成]**** 時，請將應用程式連線至新的目標「適用於 MySQL 的 Azure 資料庫」。

在線上將內部部署 MySQL 執行個體遷移至適用於 MySQL 的 Azure 資料庫現已完成。

## <a name="next-steps"></a>後續步驟

* 如需 Azure 資料庫移轉服務的相關資訊，請參閱[什麼是 Azure 資料庫移轉服務？](https://docs.microsoft.com/azure/dms/dms-overview)一文。
* 如需適用於 MySQL 的 Azure 資料庫相關資訊，請參閱[什麼是適用於 MySQL 的 Azure 資料庫？](https://docs.microsoft.com/azure/mysql/overview)一文。
* 有其他問題，請傳送電子郵件至[詢問 Azure 資料庫移轉](mailto:AskAzureDatabaseMigrations@service.microsoft.com)的名。
