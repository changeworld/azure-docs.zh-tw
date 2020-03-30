---
title: 教程：通過 Azure 門戶將 PostgreSQL 遷移到 Azure 資料庫，以便連線發佈
titleSuffix: Azure Database Migration Service
description: 通過 Azure 資料庫移轉服務，瞭解如何通過 Azure 資料庫移轉服務執行從本地 PostgreSQL 到 Azure 資料庫的連線遷移。
services: dms
author: HJToland3
ms.author: jtoland
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019
ms.topic: article
ms.date: 03/25/2020
ms.openlocfilehash: 4985c492c8ca71da87cf1a519ebc658c203d3952
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80246971"
---
# <a name="tutorial-migrate-postgresql-to-azure-db-for-postgresql-online-using-dms-via-the-azure-portal"></a>教程：通過 Azure 門戶使用 DMS 將 PostgreSQL 遷移到 Azure 資料庫以連線

可以使用 Azure 資料庫移轉服務將資料庫從本地 PostgreSQL 實例遷移到[用於 PostgreSQL 的 Azure 資料庫](https://docs.microsoft.com/azure/postgresql/)，而停機時間將降至最低。 在此教學課程中，您會在 Azure 資料庫移轉服務中使用線上移轉活動，將 **DVD Rental** 範例資料庫從內部部署的 PostgreSQL 9.6 執行個體移轉至適用於 PostgreSQL 的 Azure 資料庫。

在本教學課程中，您會了解如何：
> [!div class="checklist"]
>
> * 使用pg_dump實用程式遷移示例架構。
> * 建立 Azure 資料庫移轉服務的執行個體。
> * 在 Azure 資料庫移轉服務中創建遷移專案。
> * 執行移轉。
> * 監視移轉。
> * 執行遷移截止。

> [!NOTE]
> 若要使用「Azure 資料庫移轉服務」來執行線上移轉，必須根據「進階」定價層建立執行個體。 我們加密磁片，以防止遷移過程中的資料被盜

> [!IMPORTANT]
> 為了獲得最佳的移轉體驗，Microsoft 建議在目標資料庫所在的同一個 Azure 區域中，建立 Azure 資料庫移轉服務的執行個體。 跨區域或地理位置移動資料可能使移轉程序變慢，並產生錯誤。

## <a name="prerequisites"></a>Prerequisites

若要完成本教學課程，您需要：

* 下載並安裝[PostgreSQL 社區版本](https://www.postgresql.org/download/)9.4、9.5、9.6 或 10。 源 PostgreSQL 伺服器版本必須為 9.4、9.5、9.6、10 或 11。 如需詳細資訊，請參閱[支援的 PostgreSQL 資料庫版本](https://docs.microsoft.com/azure/postgresql/concepts-supported-versions)一文。

    此外，內部部署 PostgreSQL 版本必須符合適用於 PostgreSQL 的 Azure 資料庫版本。 例如，PostgreSQL 9.6 只能遷移到 PostgreSQL 9.6、10 或 11 的 Azure 資料庫，但不能遷移到 PostgreSQL 9.5 的 Azure 資料庫。

* [為 PostgreSQL 伺服器創建 Azure 資料庫](https://docs.microsoft.com/azure/postgresql/quickstart-create-server-database-portal)，或[為後格雷SQL-超大規模（Citus）伺服器創建 Azure 資料庫](https://docs.microsoft.com/azure/postgresql/quickstart-create-hyperscale-portal)。
* 通過使用 Azure 資源管理器部署模型為 Azure 資料庫移轉服務創建 Microsoft Azure 虛擬網路，該模型通過使用[ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction)或[VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways)提供到本地源伺服器的網站到網站的連接。 有關創建虛擬網路的詳細資訊，請參閱[虛擬網路文檔](https://docs.microsoft.com/azure/virtual-network/)，尤其是包含分步詳細資訊的快速入門文章。

    > [!NOTE]
    > 在虛擬網路設置期間，如果將 ExpressRoute 與網路對等互連到 Microsoft，則向將服務預配的子網添加以下服務[終結點](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview)：
    >
    > * 目標資料庫端點 (例如，SQL 端點、Cosmos DB 端點等)
    > * 儲存體端點
    > * 服務匯流排端點
    >
    > 此為必要設定，因為 Azure 資料庫移轉服務沒有網際網路連線。

* 確保虛擬網路的網路安全性群組 （NSG） 規則不會阻止以下到 Azure 資料庫移轉服務的入站通訊連接埠：443、53、9354、445、12000。 有關虛擬網路 NSG 流量篩選的更多詳細資訊，請參閱文章["使用網路安全性群組篩選網路流量](https://docs.microsoft.com/azure/virtual-network/virtual-network-vnet-plan-design-arm)"。
* 設定[用於 Database Engine 存取的 Windows 防火牆](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access)。
* 開啟您的 Windows 防火牆以允許 Azure 資料庫移轉服務存取來源 PostgreSQL Server (依預設會使用 TCP 連接埠 5432)。
* 使用來源資料庫前面的防火牆應用裝置時，您可能必須新增防火牆規則，才能讓 Azure 資料庫移轉服務存取來源資料庫，以進行移轉。
* 為適用於 PostgreSQL 的 Azure 資料庫建立伺服器層級的[防火牆規則](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure)，以允許 Azure 資料庫移轉服務存取目標資料庫。 提供用於 Azure 資料庫移轉服務的虛擬網路的子網範圍。
* 在 postgresql.config 檔案中啟用邏輯複寫，並設定下列參數：

  * wal_level = **logical**
  * max_replication_slots = [插槽數目]，建議設定為**五個插槽**
  * max_wal_senders =[並行工作數目] - max_wal_senders 參數設定可執行的並行工作數目，建議設定為 **10 個工作**

> [!IMPORTANT]
> 現有資料庫中的所有表都需要一個主鍵，以確保更改可以同步到目標資料庫。

## <a name="migrate-the-sample-schema"></a>移轉範例結構描述

若要完成所有資料表物件 (例如資料表結構描述、索引和預存程序)，我們必須從來源資料庫擷取結構描述，並套用至資料庫。

1. 使用 pg_dump-s 命令來建立資料庫的結構描述傾印檔案。

    ```
    pg_dump -o -h hostname -U db_username -d db_name -s > your_schema.sql
    ```

    例如，要為**dvdrental**資料庫創建架構轉儲檔：

    ```
    pg_dump -o -h localhost -U postgres -d dvdrental -s -O -x > dvdrentalSchema.sql
    ```

    如需有關使用 pg_dump 公用程式的詳細資訊，請參閱 [pg-dump](https://www.postgresql.org/docs/9.6/static/app-pgdump.html#PG-DUMP-EXAMPLES) 教學課程中的範例。

2. 在您的目標環境中建立一個空的資料庫，即適用於 PostgreSQL 的 Azure 資料庫。

    有關如何連接和創建資料庫的詳細資訊，請參閱在[Azure 門戶中為 PostgreSQL 伺服器創建 Azure 資料庫](https://docs.microsoft.com/azure/postgresql/quickstart-create-server-database-portal)或為 Azure[門戶中為 PostgreSQL-超大規模 （Citus） 伺服器創建 Azure 資料庫](https://docs.microsoft.com/azure/postgresql/quickstart-create-hyperscale-portal)。

    > [!NOTE]
    > 後縮放- 超大規模 （Citus） 的 Azure 資料庫的實例只有一個資料庫 **：citus**。

3. 透過還原結構描述傾印檔案，將結構描述匯入到您所建立的目標資料庫中。

    ```
    psql -h hostname -U db_username -d db_name < your_schema.sql
    ```

    例如：

    ```
    psql -h mypgserver-20170401.postgres.database.azure.com  -U postgres -d dvdrental citus < dvdrentalSchema.sql
    ```

4. 要提取刪除外鍵腳本並將其添加到目標（用於 PostgreSQL 的 Azure 資料庫）、PgAdmin 或 psql 中，請參閱以下腳本。

   > [!IMPORTANT]
   > 架構中的外鍵將導致遷移的初始載入和連續同步失敗。

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

6. 要禁用目標資料庫中的觸發器，請運行下面的腳本。

   > [!IMPORTANT]
   > 資料中的觸發器（插入或更新）在從源複製的資料之前，在目標中強制資料完整性。 因此，建議您在遷移期間禁用**目標所有**表中的觸發器，然後在遷移完成後重新啟用觸發器。

    ```
    SELECT DISTINCT CONCAT('ALTER TABLE ', event_object_schema, '.', event_object_table, ' DISABLE TRIGGER ', trigger_name, ';')
    FROM information_schema.triggers
    ```

## <a name="register-the-microsoftdatamigration-resource-provider"></a>註冊 Microsoft.DataMigration 資源提供者

1. 登入 Azure 入口網站，選取 [所有服務]****，然後選取 [訂用帳戶]****。

   ![顯示入口網站訂用帳戶](media/tutorial-postgresql-to-azure-postgresql-online-portal/portal-select-subscriptions.png)

2. 選取您要在其中建立 Azure 資料庫移轉服務執行個體的訂用帳戶，然後選取 [資源提供者]****。

    ![顯示資源提供者](media/tutorial-postgresql-to-azure-postgresql-online-portal/portal-select-resource-provider.png)

3. 搜索遷移，然後搜索 Microsoft 的右側 **。** **Register**

    ![註冊資源提供者](media/tutorial-postgresql-to-azure-postgresql-online-portal/portal-register-resource-provider.png)

## <a name="create-a-dms-instance"></a>建立 DMS 執行個體

1. 在 Azure 入口網站中，選取 [+ 建立資源]****，搜尋「Azure 資料庫移轉服務」，然後從下拉式清單選取 [Azure 資料庫移轉服務]****。

    ![Azure Marketplace](media/tutorial-postgresql-to-azure-postgresql-online-portal/portal-marketplace.png)

2. 在 [Azure 資料庫移轉服務]**** 畫面上，選取 [建立]****。

    ![建立 Azure 資料庫移轉服務執行個體](media/tutorial-postgresql-to-azure-postgresql-online-portal/dms-create1.png)
  
3. 在 **"創建遷移服務"** 螢幕上，指定名稱、訂閱、新資源組或現有資源組以及服務的位置。

4. 選擇現有虛擬網路或創建新虛擬網路。

    虛擬網路為 Azure 資料庫移轉服務提供了對源 PostgreSQL 伺服器和 PostgreSQL 實例的目標 Azure 資料庫的存取權限。

    有關如何在 Azure 門戶中創建虛擬網路的詳細資訊，請參閱[使用 Azure 門戶創建虛擬網路](https://aka.ms/DMSVnet)的文章。

5. 選取定價層。

    有關成本和定價層的詳細資訊，請參閱[定價頁](https://aka.ms/dms-pricing)。

    ![設定 Azure 資料庫移轉服務執行個體設定](media/tutorial-postgresql-to-azure-postgresql-online-portal/dms-settings4.png)

6. 選擇 **"查看 + 創建**"以創建服務。

   服務創建將在大約 10 到 15 分鐘內完成。

## <a name="create-a-migration-project"></a>建立移轉專案

建立服務之後，請在 Azure 入口網站中找出該服務，然後建立新的移轉專案。

1. 在 Azure 入口網站中，選取 [所有服務]****，搜尋 Azure 資料庫移轉服務，然後選取 [Azure 資料庫移轉服務]****。

      ![找出 Azure 資料庫移轉服務的所有執行個體](media/tutorial-postgresql-to-azure-postgresql-online-portal/dms-search.png)

2. 在**Azure 資料庫移轉服務**螢幕上，搜索您創建的 Azure 資料庫移轉服務實例的名稱，選擇實例，然後選擇 "**新的遷移專案**"。

3. 在 **"新建遷移專案"** 螢幕上，指定專案的名稱，在 **"源伺服器類型**文本"框中，在 **"目標伺服器類型**文本"框中選擇**PostgresSQL，** 為**PostgreSQL**選擇 Azure 資料庫。

4. 在 **"選擇活動類型"** 部分中，選擇**連線資料移轉**。

    ![創建 Azure 資料庫移轉服務專案](media/tutorial-postgresql-to-azure-postgresql-online-portal/dms-create-project.png)

    > [!NOTE]
    > 或者，您可以選擇 [僅建立專案]**** 以立即建立移轉專案，並於後續再執行移轉。

5. 選擇 **"保存**"，注意成功使用 Azure 資料庫移轉服務遷移資料的要求，然後選擇"**創建並運行活動**"。

## <a name="specify-source-details"></a>指定來源詳細資料

1. 在 **"添加源詳細資訊"** 螢幕上，指定源 PostgreSQL 實例的連接詳細資訊。

    ![新增來源詳細資料畫面](media/tutorial-postgresql-to-azure-postgresql-online-portal/dms-add-source-details.png)

2. 選取 [儲存]****。

## <a name="specify-target-details"></a>指定目標詳細資料

1. 在 **"目標詳細資訊**"螢幕上，指定目標超大規模 （Citus） 伺服器的連接詳細資訊，該伺服器是使用pg_dump部署**DVD 租賃**架構的預預配超量程 （Citus） 實例。

    ![目標詳細資料畫面](media/tutorial-postgresql-to-azure-postgresql-online-portal/dms-add-target-details.png)

2. 選取 [儲存]****，然後在 [對應到目標資料庫]**** 畫面上，對應要進行移轉的來源和目標資料庫。

    如果目標資料庫包含與來源資料庫相同的資料庫名稱，Azure 資料庫移轉服務依預設會選取目標資料庫。

    ![映射到目標資料庫螢幕](media/tutorial-postgresql-to-azure-postgresql-online-portal/dms-map-target-databases.png)

3. 選擇 **"保存**"，然後在 **"遷移設置"** 螢幕上接受預設值。

    ![遷移設置螢幕](media/tutorial-postgresql-to-azure-postgresql-online-portal/dms-migration-settings.png)

4. 選取 [儲存]****，在 [移轉摘要]**** 畫面的 [活動名稱]**** 文字方塊中，指定移轉活動的名稱，然後檢閱摘要，以確定來源和目標詳細資料都與您先前的指定相符。

    ![遷移摘要螢幕](media/tutorial-postgresql-to-azure-postgresql-online-portal/dms-migration-summary.png)

## <a name="run-the-migration"></a>執行移轉

* 選取 [執行移轉]****。

    將顯示遷移活動窗動**狀態**應更新以顯示為**正在進行備份**。

## <a name="monitor-the-migration"></a>監視移轉

1. 在移轉活動畫面上，選取 [重新整理]**** 以更新顯示，直到移轉的 [狀態]**** 顯示為 [完成]**** 為止。

     ![監控遷移過程](media/tutorial-postgresql-to-azure-postgresql-online-portal/dms-monitor-migration.png)

2. 遷移完成後，在 **"資料庫名稱**"下，選擇特定資料庫以達到 **"完全資料載入**"和 **"增量資料同步**操作"的遷移狀態。

   > [!NOTE]
   > [載入完整資料]**** 會顯示初始載入移轉狀態，而 [累加式資料同步]**** 則會顯示異動資料擷取 (CDC) 狀態。

     ![完整資料載入詳細資訊](media/tutorial-postgresql-to-azure-postgresql-online-portal/dms-full-data-load-details.png)

     ![增量資料同步詳細資訊](media/tutorial-postgresql-to-azure-postgresql-online-portal/dms-incremental-data-sync-details.png)

## <a name="perform-migration-cutover"></a>執行完全移轉

初始完整載入完成後，資料庫會標示為 [已可執行完全移轉]****。

1. 當您準備好要完成資料庫移轉後，請選取 [開始完全移轉]****。

2. 等待掛起**的更改**計數器顯示**0**以確保停止到源資料庫的所有傳入事務，選擇"**確認**"核取方塊，然後選擇"**應用**"。

    ![完整的剪切螢幕](media/tutorial-postgresql-to-azure-postgresql-online-portal/dms-complete-cutover.png)

3. 當資料庫移轉狀態顯示 **"已完成"** 時，將應用程式連接到 PostgreSQL Azure 資料庫的新目標實例。

## <a name="next-steps"></a>後續步驟

* 如需在線上移轉至「適用於 PostgreSQL 的 Azure 資料庫」時的已知問題與限制，請參閱[適用於 PostgreSQL 的 Azure 資料庫線上移轉的已知問題與因應措施](known-issues-azure-postgresql-online.md)一文。
* 如需 Azure 資料庫移轉服務的相關資訊，請參閱[什麼是 Azure 資料庫移轉服務？](https://docs.microsoft.com/azure/dms/dms-overview)一文。
* 如需適用於 PostgreSQL 的 Azure 資料庫的相關資訊，請參閱[什麼是 Azure Database for PostgreSQL？](https://docs.microsoft.com/azure/postgresql/overview)一文。
