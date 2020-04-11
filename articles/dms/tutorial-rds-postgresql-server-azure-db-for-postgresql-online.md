---
title: 教學:將 RDS PostgreSQL 連線遷移到 Azure 資料庫,用於 PostgreSQL
titleSuffix: Azure Database Migration Service
description: 了解如何使用 Azure 資料庫移轉服務，在線上將 RDS PostgreSQL 移轉至適用於 PostgreSQL 的 Azure 資料庫。
services: dms
author: HJToland3
ms.author: jtoland
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019
ms.topic: article
ms.date: 04/11/2020
ms.openlocfilehash: be6f0cd734d31f43557b49f8e9314e925b383899
ms.sourcegitcommit: fb23286d4769442631079c7ed5da1ed14afdd5fc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/10/2020
ms.locfileid: "81113956"
---
# <a name="tutorial-migrate-rds-postgresql-to-azure-db-for-postgresql-online-using-dms"></a>教學:使用 DMS 將 RDS PostgreSQL 移至 Azure 資料庫,以便連線發佈

您可以使用 Azure 資料庫移轉服務，將資料庫從 RDS PostgreSQL 執行個體遷移至[適用於 PostgreSQL 的 Azure 資料庫](https://docs.microsoft.com/azure/postgresql/)，同時讓來源資料庫在移轉期間保持連線。 換句話說，可在最短的應用程式停機時間內完成移轉。 在此教學課程中，您會在 Azure 資料庫移轉服務中使用線上移轉活動，將 **DVD Rental** 範例資料庫從 RDS PostgreSQL 9.6 執行個體移轉至適用於 PostgreSQL 的 Azure 資料庫。

在本教學課程中，您會了解如何：
> [!div class="checklist"]
>
> * 使用 pg_dump 公用程式移轉範例結構描述。
> * 建立 Azure 資料庫移轉服務的執行個體。
> * 使用 Azure 資料庫移轉服務來建立移轉專案。
> * 執行移轉。
> * 監視移轉。
> * 執行遷移截止。

> [!NOTE]
> 若要使用「Azure 資料庫移轉服務」來執行線上移轉，必須根據「進階」定價層建立執行個體。 有關詳細資訊,請參閱 Azure 資料庫遷移服務[定價](https://azure.microsoft.com/pricing/details/database-migration/)頁。 我們加密磁碟,以防止遷移過程中的數據被盜。

> [!IMPORTANT]
> 為了獲得最佳的移轉體驗，Microsoft 建議在目標資料庫所在的同一個 Azure 區域中，建立 Azure 資料庫移轉服務的執行個體。 跨區域或地理位置移動資料可能使移轉程序變慢，並產生錯誤。

[!INCLUDE [online-offline](../../includes/database-migration-service-offline-online.md)]

本文說明如何在線上從 PostgreSQL 的內部部署執行個體遷移至適用於 PostgreSQL 的 Azure 資料庫。

## <a name="prerequisites"></a>Prerequisites

若要完成本教學課程，您需要：

* 下載並安裝 [PostgreSQL 社群版](https://www.postgresql.org/download/) 9.5、9.6 或 10。 來源 PostgreSQL 伺服器版本必須是 9.5.11、9.6.7、10 或更新版本。 如需詳細資訊，請參閱[支援的 PostgreSQL 資料庫版本](https://docs.microsoft.com/azure/postgresql/concepts-supported-versions)一文。

   另請注意,PostgreSQL 版本的目標 Azure 資料庫必須等於或晚於 RDS PostgreSQL 版本。 例如,RDS PostgreSQL 9.6 只能遷移到 Azure 資料庫,用於 PostgreSQL 9.6、10 或 11,但不能遷移到 PostgreSQL 9.5 的 Azure 資料庫。

* [為後格雷SQL](https://docs.microsoft.com/azure/postgresql/quickstart-create-server-database-portal)或 Azure 資料庫創建 Azure 資料庫的實例[,用於 PostgreSQL- 超大規模 (Citus)。](https://docs.microsoft.com/azure/postgresql/quickstart-create-hyperscale-portal) 如需如何使用 pgAdmin 連線到 PostgreSQL Server 的詳細資訊，請參閱文件的此[小節](https://docs.microsoft.com/azure/postgresql/quickstart-create-server-database-portal#connect-to-the-postgresql-server-using-pgadmin)。
* 通過使用 Azure 資源管理器部署模型為 Azure 資料庫遷移服務創建 Microsoft Azure 虛擬網路,該模型透過使用[ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction)或[VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways)提供到本地源伺服器的網站到網站的連接。 有關創建虛擬網路的詳細資訊,請參閱[虛擬網路文檔](https://docs.microsoft.com/azure/virtual-network/),尤其是包含分步詳細資訊的快速入門文章。
* 確保虛擬網路網路安全組規則不會阻止以下到 Azure 資料庫遷移服務的入站通信埠:443、53、9354、445 和 12000。 有關虛擬網路 NSG 流量篩選的更多詳細資訊,請參閱文章[「使用網路安全組篩選網路流量](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg)」。
* 設定[用於 Database Engine 存取的 Windows 防火牆](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access)。
* 開啟您的 Windows 防火牆以允許 Azure 資料庫移轉服務存取來源 PostgreSQL Server (依預設會使用 TCP 連接埠 5432)。
* 使用來源資料庫前面的防火牆應用裝置時，您可能必須新增防火牆規則，才能讓 Azure 資料庫移轉服務存取來源資料庫，以進行移轉。
* 為適用於 PostgreSQL Server 的 Azure 資料庫伺服器建立伺服器層級的[防火牆規則](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure)，以允許 Azure 資料庫移轉服務存取目標資料庫。 提供用於 Azure 資料庫遷移服務的虛擬網路的子網範圍。

### <a name="set-up-aws-rds-postgresql-for-replication"></a>設定 AWS RDS PostgreSQL 以進行複寫

1. 若要建立新的參數群組，請遵循 AWS 在[使用資料庫參數群組](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/USER_WorkingWithParamGroups.html)一文中提供的指示。
2. 使用主要使用者名稱從 Azure 資料庫移轉服務連接到來源。 如果您使用的帳戶並非非主要使用者帳戶，該帳戶必須具有 rds_superuser 角色和 rds_replication 角色。 Rds_replication 角色會授與邏輯位置的管理權限，以及使用邏輯位置進行資料串流的權限。
3. 使用下列組態建立新的參數群組：

    a. 將 DB 參數群組中的 rds.logical_replication 參數設定為 1。

    b. max_wal_senders =[並行工作數目] - max_wal_senders 參數設定可執行的並行工作數目，建議設定為 10 個工作。

    c. max_replication_slots – = [插槽數目]，建議設定為五個插槽。

4. 將您建立的參數群組與 RDS PostgreSQL 執行個體建立關聯。

## <a name="migrate-the-schema"></a>移轉結構描述

1. 從來源資料庫擷取結構描述並套用到目標資料庫，以完成所有資料庫物件 (例如資料表結構描述、索引和預存程序) 的移轉。

    若是只要遷移結構描述，最簡單的方法是使用 pg_dump with the -s 選項。 如需詳細資訊，請參閱 Postgres pg_dump 教學課程的[範例](https://www.postgresql.org/docs/9.6/app-pgdump.html#PG-DUMP-EXAMPLES)。

    ```
    pg_dump -o -h hostname -U db_username -d db_name -s > your_schema.sql
    ```

    例如，若要傾印 **dvdrental** 資料庫的結構描述檔案，請使用下列命令：

    ```
    pg_dump -o -h localhost -U postgres -d dvdrental -s  > dvdrentalSchema.sql
    ```

2. 在目標服務中建立空的資料庫，即適用於 PostgreSQL 的 Azure 資料庫。 若要連線並建立資料庫，請參閱下列其中一篇文章：

    * [在 Azure 入口網站中建立 Azure Database for PostgreSQL 伺服器](https://docs.microsoft.com/azure/postgresql/quickstart-create-server-database-portal)
    * [使用 Azure 門戶為 PostgreSQL - 超大規模(Citus) 伺服器建立 Azure 資料庫](https://docs.microsoft.com/azure/postgresql/quickstart-create-hyperscale-portal)

3. 將結構描述匯入目標服務，也就是適用於 PostgreSQL 的 Azure 資料庫。 若要還原結構描述傾印檔案，請執行下列命令：

    ```
    psql -h hostname -U db_username -d db_name < your_schema.sql
    ```

    例如：

    ```
    psql -h mypgserver-20170401.postgres.database.azure.com  -U postgres -d dvdrental < dvdrentalSchema.sql
    ```

4. 如果您的結構描述中有外部索引鍵，則移轉的初始載入和持續同步將會失敗。 若要擷取 drop 外部索引鍵指令碼，並在目的地 (適用於 PostgreSQL 的 Azure 資料庫) 新增外部索引鍵，請在 PgAdmin 或 psql 中執行下列指令碼：
  
    ```
    SELECT Queries.tablename
           ,concat('alter table ', Queries.tablename, ' ', STRING_AGG(concat('DROP CONSTRAINT ', Queries.foreignkey), ',')) as DropQuery
                ,concat('alter table ', Queries.tablename, ' ',
                                                STRING_AGG(concat('ADD CONSTRAINT ', Queries.foreignkey, ' FOREIGN KEY (', column_name, ')', 'REFERENCES ', foreign_table_name, '(', foreign_column_name, ')' ), ',')) as AddQuery
        FROM
        (SELECT
        tc.table_schema,
        tc.constraint_name as foreignkey,
        tc.table_name as tableName,
        kcu.column_name,
        ccu.table_schema AS foreign_table_schema,
        ccu.table_name AS foreign_table_name,
        ccu.column_name AS foreign_column_name
    FROM
        information_schema.table_constraints AS tc
        JOIN information_schema.key_column_usage AS kcu
          ON tc.constraint_name = kcu.constraint_name
          AND tc.table_schema = kcu.table_schema
        JOIN information_schema.constraint_column_usage AS ccu
          ON ccu.constraint_name = tc.constraint_name
          AND ccu.table_schema = tc.table_schema
    WHERE constraint_type = 'FOREIGN KEY') Queries
      GROUP BY Queries.tablename;
    ```

5. 執行查詢結果中的 drop 外部索引鍵 (這是第二個資料行)，以卸除外部索引鍵。

6. 如果資料中有觸發程序 (insert 或 update 觸發程序)，該觸發程序將會在目標中強制執行資料完整性，再複製來源中的資料。 建議您在移轉期間停用「目標上」** 所有資料表中的觸發程序，然後在移轉完成後啟用觸發程序。

    若要在目標資料庫中停用觸發程序：

    ```
    SELECT Concat('DROP TRIGGER ', Trigger_Name, ';') FROM  information_schema.TRIGGERS WHERE TRIGGER_SCHEMA = 'your_schema';
    ```

## <a name="register-the-microsoftdatamigration-resource-provider"></a>註冊 Microsoft.DataMigration 資源提供者

1. 登入 Azure 入口網站，選取 [所有服務]****，然後選取 [訂用帳戶]****。

   ![顯示入口網站訂用帳戶](media/tutorial-rds-postgresql-server-azure-db-for-postgresql-online/portal-select-subscription1.png)

2. 選取您要在其中建立 Azure 資料庫移轉服務執行個體的訂用帳戶，然後選取 [資源提供者]****。

    ![顯示資源提供者](media/tutorial-rds-postgresql-server-azure-db-for-postgresql-online/portal-select-resource-provider.png)

3. 搜尋移轉,然後搜尋 Microsoft 的右側 **。** **Register**

    ![註冊資源提供者](media/tutorial-rds-postgresql-server-azure-db-for-postgresql-online/portal-register-resource-provider.png)

## <a name="create-an-instance-of-azure-database-migration-service"></a>建立 Azure 資料庫移轉服務的執行個體

1. 在 Azure 入口網站中，選取 [+ 建立資源]****，搜尋「Azure 資料庫移轉服務」，然後從下拉式清單選取 [Azure 資料庫移轉服務]****。

    ![Azure Marketplace](media/tutorial-rds-postgresql-server-azure-db-for-postgresql-online/portal-marketplace.png)

2. 在 [Azure 資料庫移轉服務]**** 畫面上，選取 [建立]****。

    ![建立 Azure 資料庫移轉服務執行個體](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/dms-create1.png)
  
3. 在 [建立移轉服務]**** 畫面上，指定服務的名稱、訂用帳戶，以及新的或現有的資源群組。

4. 選取您要在其中建立 Azure 資料庫移轉服務執行個體的位置。

5. 選擇現有虛擬網路或創建新虛擬網路。

    虛擬網路為 Azure 資料庫遷移服務提供了對源 PostgreSQL 實例和 PostgreSQL 實例的目標 Azure 資料庫的訪問許可權。

    有關如何在 Azure 門戶中創建虛擬網路的詳細資訊,請參閱[使用 Azure 門戶創建虛擬網路](https://aka.ms/DMSVnet)的文章。

6. 選擇定價層;對於此線上遷移,請務必選擇高級:4vCores 定價層。

    ![設定 Azure 資料庫移轉服務執行個體設定](media/tutorial-rds-postgresql-server-azure-db-for-postgresql-online/dms-settings5.png)

7. 選取 [建立]**** 以建立服務。

## <a name="create-a-migration-project"></a>建立移轉專案

建立服務之後，請在 Azure 入口網站中找出該服務，然後建立新的移轉專案。

1. 在 Azure 入口網站中，選取 [所有服務]****，搜尋 Azure 資料庫移轉服務，然後選取 [Azure 資料庫移轉服務]****。

      ![找出 Azure 資料庫移轉服務的所有執行個體](media/tutorial-rds-postgresql-server-azure-db-for-postgresql-online/dms-search.png)

2. 在**Azure 資料庫遷移服務**螢幕上,搜尋您建立的 Azure 資料庫遷移服務實例的名稱,選擇實例,然後選擇「**新的遷移專案**」。
3. 在 [新增移轉專案]**** 畫面上指定專案名稱，並在 [來源伺服器類型]**** 文字方塊中中選取 [AWS RDS for PostgreSQL]****，然後在 [目標伺服器類型]**** 文字方塊中選取 [適用於 PostgreSQL 的 Azure 資料庫]****。
4. 在**選擇作用型態「** 部份中,選擇**連線資料移移**。

    > [!IMPORTANT]
    > 請務必選取 [線上資料移轉]****；此案例不支援離線移轉。

    ![建立資料庫移轉服務專案](media/tutorial-rds-postgresql-server-azure-db-for-postgresql-online/dms-create-project5.png)

    > [!NOTE]
    > 或者，您可以選擇 [僅建立專案]**** 以立即建立移轉專案，並於後續再執行移轉。

5. 選取 [儲存]  。

6. 選取 [建立及執行活動]****，以建立專案並執行移轉活動。

    > [!NOTE]
    > 請記下在專案建立刀鋒視窗中設定線上移轉所需的必要條件。

## <a name="specify-source-details"></a>指定來源詳細資料

* 在 **「添加源詳細資訊」** 螢幕上,指定源 PostgreSQL 實例的連接詳細資訊。

   ![來源詳細資料](media/tutorial-rds-postgresql-server-azure-db-for-postgresql-online/dms-source-details5.png)

## <a name="specify-target-details"></a>指定目標詳細資料

1. 選取 [儲存]****，然後在 [目標詳細資料]**** 畫面上，指定目標「適用於 PostgreSQL Server 的 Azure 資料庫」伺服器的連線詳細資料；該伺服器是使用 pg_dump 預先佈建並已部署 **DVD Rentals** 結構描述。

    ![目標詳細資訊](media/tutorial-rds-postgresql-server-azure-db-for-postgresql-online/dms-target-details.png)

2. 選取 [儲存]****，然後在 [對應到目標資料庫]**** 畫面上，對應要進行移轉的來源和目標資料庫。

    如果目標資料庫包含與來源資料庫相同的資料庫名稱，Azure 資料庫移轉服務依預設會選取目標資料庫。

    ![對應到目標資料庫](media/tutorial-rds-postgresql-server-azure-db-for-postgresql-online/dms-map-target-databases.png)

3. 選取 [儲存]****，在 [移轉摘要]**** 畫面的 [活動名稱]**** 文字方塊中，指定移轉活動的名稱，然後檢閱摘要，以確定來源和目標詳細資料都與您先前的指定相符。

    ![移轉摘要](media/tutorial-rds-postgresql-server-azure-db-for-postgresql-online/dms-migration-summary1.png)

## <a name="run-the-migration"></a>執行移轉

* 選取 [執行移轉]****。

    將顯示移轉活動視窗,並且活動**的狀態****中已初始化**。

## <a name="monitor-the-migration"></a>監視移轉

1. 在移轉活動畫面上，選取 [重新整理]**** 以更新顯示，直到移轉的 [狀態]**** 顯示為 [執行中]**** 為止。

    ![活動狀態 - 執行中](media/tutorial-rds-postgresql-server-azure-db-for-postgresql-online/dms-activity-status3.png)

2. 在 [資料庫名稱]**** 下方選取特定資料庫，以取得 [載入完整資料]**** 和 [累加式資料同步]**** 作業的移轉狀態。

    [載入完整資料]**** 會顯示初始載入移轉狀態，而 [累加式資料同步]**** 則會顯示異動資料擷取 (CDC) 狀態。

    ![清查畫面 - 載入完整資料](media/tutorial-rds-postgresql-server-azure-db-for-postgresql-online/dms-inventory-full-load.png)

    ![清查畫面 - 累加式資料同步](media/tutorial-rds-postgresql-server-azure-db-for-postgresql-online/dms-inventory-incremental.png)

## <a name="perform-migration-cutover"></a>執行完全移轉

初始完全載入完成後,資料庫將標記為 **「準備剪切**」 。

1. 當您準備好要完成資料庫移轉後，請選取 [開始完全移轉]****。

2. 等待掛起**的更改**計數器顯示**0**以確保停止到源資料庫的所有傳入事務,選擇「**Apply****確認**」

    ![完整的剪下螢幕](media/tutorial-rds-postgresql-server-azure-db-for-postgresql-online/dms-complete-cutover.png)

3. 當資料庫移轉狀態顯示為 [已完成]**** 時，請將應用程式連線至新的「適用於 PostgreSQL 的 Azure 資料庫」目標資料庫。

RDS PostgreSQL 的本地實例連線遷移到 Azure 資料庫的 PostgreSQL 現已完成。

## <a name="next-steps"></a>後續步驟

* 如需 Azure 資料庫移轉服務的相關資訊，請參閱[什麼是 Azure 資料庫移轉服務？](https://docs.microsoft.com/azure/dms/dms-overview)一文。
* 如需適用於 PostgreSQL 的 Azure 資料庫的相關資訊，請參閱[什麼是 Azure Database for PostgreSQL？](https://docs.microsoft.com/azure/postgresql/overview)一文。
* 有其他問題，請傳送電子郵件至[詢問 Azure 資料庫移轉](mailto:AskAzureDatabaseMigrations@service.microsoft.com)的名。
