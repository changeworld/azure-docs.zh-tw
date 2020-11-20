---
title: 教學課程：透過 Azure 入口網站將 PostgreSQL 線上移轉至適用於 PostgreSQL 的 Azure DB
titleSuffix: Azure Database Migration Service
description: 了解如何透過 Azure 入口網站使用 Azure 資料庫移轉服務，將內部部署的 PostgreSQL 線上移轉至適用於 PostgreSQL 的 Azure 資料庫。
services: dms
author: arunkumarthiags
ms.author: arthiaga
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019
ms.topic: tutorial
ms.date: 04/11/2020
ms.openlocfilehash: 6b9822e16a9c5604371bd4c8c1e136ce78f29820
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/20/2020
ms.locfileid: "94955152"
---
# <a name="tutorial-migrate-postgresql-to-azure-db-for-postgresql-online-using-dms-via-the-azure-portal"></a>教學課程：透過 Azure 入口網站使用 DMS 將 PostgreSQL 線上移轉至適用於 PostgreSQL 的 Azure DB

您可以使用 Azure 資料庫移轉服務，將資料庫從內部部署 PostgreSQL 執行個體移轉至[適用於 PostgreSQL 的 Azure 資料庫](../postgresql/index.yml)，且應用程式的停機時間最短。 在此教學課程中，您會在 Azure 資料庫移轉服務中使用線上移轉活動，將 **DVD Rental** 範例資料庫從內部部署的 PostgreSQL 9.6 執行個體移轉至適用於 PostgreSQL 的 Azure 資料庫。

在本教學課程中，您會了解如何：
> [!div class="checklist"]
>
> * 使用 pg_dump 公用程式移轉範例結構描述。
> * 建立 Azure 資料庫移轉服務的執行個體。
> * 在 Azure 資料庫移轉服務中建立移轉專案。
> * 執行移轉。
> * 監視移轉。
> * 執行完全移轉。

> [!NOTE]
> 若要使用「Azure 資料庫移轉服務」來執行線上移轉，必須根據「進階」定價層建立執行個體。 我們會將磁碟加密，以防止在移轉期間使得資料遭竊

> [!IMPORTANT]
> 為了獲得最佳的移轉體驗，Microsoft 建議在目標資料庫所在的同一個 Azure 區域中，建立 Azure 資料庫移轉服務的執行個體。 跨區域或地理位置移動資料可能使移轉程序變慢，並產生錯誤。

## <a name="prerequisites"></a>Prerequisites

若要完成本教學課程，您需要：

* 下載並安裝 [PostgreSQL 社群版](https://www.postgresql.org/download/) 9.4、9.5、9.6 或 10。 來源 PostgreSQL 伺服器版本必須是 9.4、9.5、9.6、10 或 11。 如需詳細資訊，請參閱[支援的 PostgreSQL 資料庫版本](../postgresql/concepts-supported-versions.md)一文。

    另請注意，目標「適用於 PostgreSQL 的 Azure 資料庫」版本必須等於或晚於內部部署 PostgreSQL 版本。 例如，PostgreSQL 9.6 可以移轉至適用於 PostgreSQL 的 Azure 資料庫 9.6、10 或 11，但無法移轉至適用於 PostgreSQL 的 Azure 資料庫 9.5。

* [建立適用於 PostgreSQL 的 Azure 資料庫伺服器](../postgresql/quickstart-create-server-database-portal.md)或[建立適用於 PostgreSQL 的 Azure 資料庫 - 超大規模 (Citus) 伺服器](../postgresql/quickstart-create-hyperscale-portal.md)。
* 使用 Azure Resource Manager 部署模型建立 Azure 資料庫移轉服務的 Microsoft Azure 虛擬網路，以使用 [ExpressRoute](../expressroute/expressroute-introduction.md) 或 [VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md) 為您的內部部署來源伺服器提供站對站連線能力。 如需建立虛擬網路的詳細資訊，請參閱[虛擬網路文件](../virtual-network/index.yml)，特別是快速入門文章，裡面會提供逐步操作詳細資料。

    > [!NOTE]
    > 在虛擬網路設定期間，如果您使用 ExpressRoute 搭配與 Microsoft 對等互連的網路，請將下列服務[端點](../virtual-network/virtual-network-service-endpoints-overview.md)新增至將佈建服務的子網路：
    >
    > * 目標資料庫端點 (例如，SQL 端點、Cosmos DB 端點等)
    > * 儲存體端點
    > * 服務匯流排端點
    >
    > 此為必要設定，因為 Azure 資料庫移轉服務沒有網際網路連線。

* 確定您虛擬網路的網路安全性群組 (NSG) 規則不會對 Azure 資料庫移轉服務封鎖下列輸入通訊連接埠：443、53、9354、445、12000。 如需虛擬網路 NSG 流量篩選的詳細資訊，請參閱[使用網路安全性群組來篩選網路流量](../virtual-network/virtual-network-vnet-plan-design-arm.md)。
* 設定[用於 Database Engine 存取的 Windows 防火牆](/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access)。
* 開啟您的 Windows 防火牆以允許 Azure 資料庫移轉服務存取來源 PostgreSQL Server (依預設會使用 TCP 連接埠 5432)。
* 使用來源資料庫前面的防火牆應用裝置時，您可能必須新增防火牆規則，才能讓 Azure 資料庫移轉服務存取來源資料庫，以進行移轉。
* 為適用於 PostgreSQL 的 Azure 資料庫建立伺服器層級的[防火牆規則](../azure-sql/database/firewall-configure.md)，以允許 Azure 資料庫移轉服務存取目標資料庫。 提供用於 Azure 資料庫移轉服務之虛擬網路的子網路範圍。
* 在 postgresql.config 檔案中啟用邏輯複寫，並設定下列參數：

  * wal_level = **logical**
  * max_replication_slots = [插槽數目]，建議設定為 **五個插槽**
  * max_wal_senders =[並行工作數目] - max_wal_senders 參數設定可執行的並行工作數目，建議設定為 **10 個工作**

> [!IMPORTANT]
> 現有資料庫中的所有資料表都需要主要索引鍵，以確保變更可以同步處理至目標資料庫。

## <a name="migrate-the-sample-schema"></a>移轉範例結構描述

若要完成所有資料表物件 (例如資料表結構描述、索引和預存程序)，我們必須從來源資料庫擷取結構描述，並套用至資料庫。

1. 使用 pg_dump-s 命令來建立資料庫的結構描述傾印檔案。

    ```
    pg_dump -o -h hostname -U db_username -d db_name -s > your_schema.sql
    ```

    例如，若要建立 **dvdrental** 資料庫的結構描述傾印檔案：

    ```
    pg_dump -o -h localhost -U postgres -d dvdrental -s -O -x > dvdrentalSchema.sql
    ```

    如需有關使用 pg_dump 公用程式的詳細資訊，請參閱 [pg-dump](https://www.postgresql.org/docs/9.6/static/app-pgdump.html#PG-DUMP-EXAMPLES) 教學課程中的範例。

2. 在您的目標環境中建立一個空的資料庫，即適用於 PostgreSQL 的 Azure 資料庫。

    如需如何連接及建立資料庫的詳細資訊，請參閱[在 Azure 入口網站中建立適用於 PostgreSQL 的 Azure 資料庫伺服器](../postgresql/quickstart-create-server-database-portal.md)或[在 Azure 入口網站中建立適用於 PostgreSQL 的 Azure 資料庫 - 超大規模 (Citus) 伺服器](../postgresql/quickstart-create-hyperscale-portal.md)文章。

    > [!NOTE]
    > 適用於 PostgreSQL 的 Azure 資料庫 - 超大規模 (Citus) 的一個執行個體只有單一資料庫：**citus**。

3. 透過還原結構描述傾印檔案，將結構描述匯入到您所建立的目標資料庫中。

    ```
    psql -h hostname -U db_username -d db_name < your_schema.sql
    ```

    例如：

    ```
    psql -h mypgserver-20170401.postgres.database.azure.com  -U postgres -d dvdrental citus < dvdrentalSchema.sql
    ```

4. 若要擷取 drop 外部索引鍵指令碼，並在目的地 (適用於 PostgreSQL 的 Azure 資料庫) 新增它，請在 PgAdmin 或 psql 中執行下列指令碼。

   > [!IMPORTANT]
   > 結構描述中的外部索引鍵會導致移轉的初始載入和持續同步失敗。

    ```
    SELECT Q.table_name
        ,CONCAT('ALTER TABLE ', table_schema, '.', table_name, STRING_AGG(DISTINCT CONCAT(' DROP CONSTRAINT ', foreignkey), ','), ';') as DropQuery
            ,CONCAT('ALTER TABLE ', table_schema, '.', table_name, STRING_AGG(DISTINCT CONCAT(' ADD CONSTRAINT ', foreignkey, ' FOREIGN KEY (', column_name, ')', ' REFERENCES ', foreign_table_schema, '.', foreign_table_name, '(', foreign_column_name, ')' ), ','), ';') as AddQuery
    FROM
        (SELECT
        S.table_schema,
        S.foreignkey,
        S.table_name,
        STRING_AGG(DISTINCT S.column_name, ',') AS column_name,
        S.foreign_table_schema,
        S.foreign_table_name,
        STRING_AGG(DISTINCT S.foreign_column_name, ',') AS foreign_column_name
    FROM
        (SELECT DISTINCT
        tc.table_schema,
        tc.constraint_name AS foreignkey,
        tc.table_name,
        kcu.column_name,
        ccu.table_schema AS foreign_table_schema,
        ccu.table_name AS foreign_table_name,
        ccu.column_name AS foreign_column_name
        FROM information_schema.table_constraints AS tc
        JOIN information_schema.key_column_usage AS kcu ON tc.constraint_name = kcu.constraint_name AND tc.table_schema = kcu.table_schema
        JOIN information_schema.constraint_column_usage AS ccu ON ccu.constraint_name = tc.constraint_name AND ccu.table_schema = tc.table_schema
    WHERE constraint_type = 'FOREIGN KEY'
        ) S
        GROUP BY S.table_schema, S.foreignkey, S.table_name, S.foreign_table_schema, S.foreign_table_name
        ) Q
        GROUP BY Q.table_schema, Q.table_name;
    ```

5. 在查詢結果中執行卸除外部索引鍵 (這是第二個資料行)。

6. 若要在目標資料庫中停用觸發程序，請執行下列指令碼。

   > [!IMPORTANT]
   > 資料中的觸發程序 (Insert 或 Update) 會在從來源複寫資料之前，於目標中強制執行資料完整性。 因此，建議您在移轉期間停用 **目標上** 所有資料表中的觸發程序，並在移轉完成後重新啟用觸發程序。

    ```
    SELECT DISTINCT CONCAT('ALTER TABLE ', event_object_schema, '.', event_object_table, ' DISABLE TRIGGER ', trigger_name, ';')
    FROM information_schema.triggers
    ```

## <a name="register-the-microsoftdatamigration-resource-provider"></a>註冊 Microsoft.DataMigration 資源提供者

1. 登入 Azure 入口網站，選取 [所有服務]，然後選取 [訂用帳戶]。

   ![顯示入口網站訂用帳戶](media/tutorial-postgresql-to-azure-postgresql-online-portal/portal-select-subscriptions.png)

2. 選取您要在其中建立 Azure 資料庫移轉服務執行個體的訂用帳戶，然後選取 [資源提供者]。

    ![顯示資源提供者](media/tutorial-postgresql-to-azure-postgresql-online-portal/portal-select-resource-provider.png)

3. 搜尋移轉，然後在 [Microsoft.DataMigration] 的右邊，選取 [註冊]。

    ![註冊資源提供者](media/tutorial-postgresql-to-azure-postgresql-online-portal/portal-register-resource-provider.png)

## <a name="create-a-dms-instance"></a>建立 DMS 執行個體

1. 在 Azure 入口網站中，選取 [+ 建立資源]，搜尋「Azure 資料庫移轉服務」，然後從下拉式清單選取 [Azure 資料庫移轉服務]。

    ![Azure Marketplace](media/tutorial-postgresql-to-azure-postgresql-online-portal/portal-marketplace.png)

2. 在 [Azure 資料庫移轉服務] 畫面上，選取 [建立]。

    ![建立 Azure 資料庫移轉服務執行個體](media/tutorial-postgresql-to-azure-postgresql-online-portal/dms-create1.png)
  
3. 在 [建立移轉服務] 畫面上，指定名稱、訂用帳戶、現有的資源群組，以及服務的位置。

4. 選取現有的虛擬網路或建立新的虛擬網路。

    虛擬網路會為 Azure 資料庫移轉服務提供來源 PostgreSQL 伺服器和目標適用於 PostgreSQL 的 Azure 資料庫執行個體的存取權。

    如需如何在 Azure 入口網站中建立虛擬網路的詳細資訊，請參閱[使用 Azure 入口網站建立虛擬網路](../virtual-network/quick-create-portal.md)一文。

5. 選取定價層。

    如需成本和定價層的詳細資訊，請參閱[定價分頁](https://aka.ms/dms-pricing)。

    ![設定 Azure 資料庫移轉服務執行個體設定](media/tutorial-postgresql-to-azure-postgresql-online-portal/dms-settings4.png)

6. 選取 [檢閱 + 建立] 以建立服務。

   服務建立大約會在 10 到 15 分鐘內完成。

## <a name="create-a-migration-project"></a>建立移轉專案

建立服務之後，請在 Azure 入口網站中找出該服務，然後建立新的移轉專案。

1. 在 Azure 入口網站中，選取 [所有服務]，搜尋 Azure 資料庫移轉服務，然後選取 [Azure 資料庫移轉服務]。

      ![找出 Azure 資料庫移轉服務的所有執行個體](media/tutorial-postgresql-to-azure-postgresql-online-portal/dms-search.png)

2. 在 [Azure 資料庫移轉服務] 畫面上，搜尋您建立的 Azure 資料庫移轉服務執行個體名稱，選取該執行個體，然後選取 [新增移轉專案]。

3. 在 [新增移轉專案] 畫面上指定專案名稱，並在 [來源伺服器類型] 文字方塊中中選取 [PostgreSQL]，然後在 [目標伺服器類型] 文字方塊中選取 [適用於 PostgreSQL 的 Azure 資料庫]。

4. 在 [選擇活動類型] 區段中，選取 [線上資料移轉]。

    ![建立 Azure 資料庫移轉服務專案](media/tutorial-postgresql-to-azure-postgresql-online-portal/dms-create-project.png)

    > [!NOTE]
    > 或者，您可以選擇 [僅建立專案] 以立即建立移轉專案，並於後續再執行移轉。

5. 選取 [儲存]，記下要成功使用 Azure 資料庫移轉服務來移轉資料的需求，然後選取 [建立及執行活動]。

## <a name="specify-source-details"></a>指定來源詳細資料

1. 在 [新增來源詳細資料] 畫面上，指定來源 PostgreSQL 執行個體的連線詳細資料。

    ![新增來源詳細資料畫面](media/tutorial-postgresql-to-azure-postgresql-online-portal/dms-add-source-details.png)

2. 選取 [儲存]。

## <a name="specify-target-details"></a>指定目標詳細資料

1. 在 [目標詳細資料] 畫面上，指定目標超大規模 (Citus) 伺服器的連線詳細資料，這是使用 pg_dump 部署的 **DVD Rentals** 結構描述超大規模 (Citus) 的預先佈建執行個體。

    ![目標詳細資料畫面](media/tutorial-postgresql-to-azure-postgresql-online-portal/dms-add-target-details.png)

2. 選取 [儲存]，然後在 [對應到目標資料庫] 畫面上，對應要進行移轉的來源和目標資料庫。

    如果目標資料庫包含與來源資料庫相同的資料庫名稱，Azure 資料庫移轉服務依預設會選取目標資料庫。

    ![對應到目標資料庫畫面](media/tutorial-postgresql-to-azure-postgresql-online-portal/dms-map-target-databases.png)

3. 選取 [儲存]，然後在 [移轉設定] 畫面上接受預設值。

    ![移轉設定畫面](media/tutorial-postgresql-to-azure-postgresql-online-portal/dms-migration-settings.png)

4. 選取 [儲存]，在 [移轉摘要] 畫面的 [活動名稱] 文字方塊中，指定移轉活動的名稱，然後檢閱摘要，以確定來源和目標詳細資料都與您先前的指定相符。

    ![移轉摘要畫面](media/tutorial-postgresql-to-azure-postgresql-online-portal/dms-migration-summary.png)

## <a name="run-the-migration"></a>執行移轉

* 選取 [執行移轉]。

    移轉活動視窗隨即出現，且活動的 [狀態] 應該會更新，以顯示為 [備份進行中]。

## <a name="monitor-the-migration"></a>監視移轉

1. 在移轉活動畫面上，選取 [重新整理] 以更新顯示，直到移轉的 [狀態] 顯示為 [完成] 為止。

     ![監視移轉程序](media/tutorial-postgresql-to-azure-postgresql-online-portal/dms-monitor-migration.png)

2. 移轉完成時，於 [資料庫名稱] 下方選取特定資料庫，以取得 [載入完整資料] 和 [累加式資料同步] 作業的移轉狀態。

   > [!NOTE]
   > [載入完整資料] 會顯示初始載入移轉狀態，而 [累加式資料同步] 則會顯示異動資料擷取 (CDC) 狀態。

     ![完整資料載入詳細資料](media/tutorial-postgresql-to-azure-postgresql-online-portal/dms-full-data-load-details.png)

     ![累加式資料同步詳細資料](media/tutorial-postgresql-to-azure-postgresql-online-portal/dms-incremental-data-sync-details.png)

## <a name="perform-migration-cutover"></a>執行完全移轉

初始完整載入完成後，資料庫會標示為 [已可執行完全移轉]。

1. 當您準備好要完成資料庫移轉後，請選取 [開始完全移轉]。

2. 等到 [暫止的變更] 計數器顯示 **0**，以確保對來源資料庫的所有傳入交易都已停止，選取 [確認] 核取方塊，然後選取 [套用]。

    ![完成完全移轉畫面](media/tutorial-postgresql-to-azure-postgresql-online-portal/dms-complete-cutover.png)

3. 當資料庫移轉狀態顯示 [已完成] 時，請[重新建立序列](https://wiki.postgresql.org/wiki/Fixing_Sequences) (如果適用)，然後將應用程式連線至適用於 PostgreSQL 的 Azure 資料庫的新目標執行個體。

## <a name="next-steps"></a>後續步驟

* 如需在線上移轉至「適用於 PostgreSQL 的 Azure 資料庫」時的已知問題與限制，請參閱[適用於 PostgreSQL 的 Azure 資料庫線上移轉的已知問題與因應措施](known-issues-azure-postgresql-online.md)一文。
* 如需 Azure 資料庫移轉服務的相關資訊，請參閱[什麼是 Azure 資料庫移轉服務？](./dms-overview.md)一文。
* 如需適用於 PostgreSQL 的 Azure 資料庫的相關資訊，請參閱[什麼是 Azure Database for PostgreSQL？](../postgresql/overview.md)一文。