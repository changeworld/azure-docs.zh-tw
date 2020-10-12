---
title: 教學課程：從 RDS SQL Server 至 SQL Database 的線上移轉
titleSuffix: Azure Database Migration Service
description: 了解如何使用 Azure 資料庫移轉服務，執行從 RDS SQL Server 至 Azure SQL Database 單一資料庫或受控執行個體的線上移轉。
services: dms
author: pochiraju
ms.author: rajpo
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019
ms.topic: tutorial
ms.date: 01/08/2020
ms.openlocfilehash: 12725c28c3e128317301bc51f9ce93f76021cc2b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "91291362"
---
# <a name="tutorial-migrate-rds-sql-server-to-azure-sql-database-or-an-azure-sql-managed-instance-online-using-dms"></a>教學課程：使用 DMS 將 RDS SQL Server 線上移轉至 Azure SQL Database 或 Azure SQL 受控執行個體

您可以使用 Azure 資料庫移轉服務，以最短停機時間將資料庫從 RDS SQL Server 執行個體移轉至 [Azure SQL Database](https://docs.microsoft.com/azure/sql-database/) 或 [Azure SQL 受控執行個體](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-index)。 在本教學課程中，您會使用 Azure 資料庫移轉服務，將已還原至 SQL Server 2012 (或更新版本) RDS SQL Server 執行個體的 **Adventureworks2012** 資料庫遷移到 SQL Database 或 SQL 受控執行個體。

在本教學課程中，您會了解如何：
> [!div class="checklist"]
> * 在 Azure SQL Database 或 SQL 受控執行個體中建立資料庫。 
> * 使用 Data Migration Assistant 移轉範例結構描述。
> * 建立 Azure 資料庫移轉服務的執行個體。
> * 使用 Azure 資料庫移轉服務來建立移轉專案。
> * 執行移轉。
> * 監視移轉。
> * 下載移轉報告。

> [!NOTE]
> 若要使用「Azure 資料庫移轉服務」來執行線上移轉，必須根據「進階」定價層建立執行個體。 如需詳細資訊，請參閱 Azure 資料庫移轉服務的[定價](https://azure.microsoft.com/pricing/details/database-migration/)頁面。

> [!IMPORTANT]
> 為了獲得最佳的移轉體驗，Microsoft 建議在目標資料庫所在的同一個 Azure 區域中，建立 Azure 資料庫移轉服務的執行個體。 跨區域或地理位置移動資料可能使移轉程序變慢，並產生錯誤。

[!INCLUDE [online-offline](../../includes/database-migration-service-offline-online.md)]

本文將說明從 RDS SQL Server 至 SQL Database 或 SQL 受控執行個體的線上移轉作業。

## <a name="prerequisites"></a>Prerequisites

若要完成本教學課程，您需要：

* 建立 [RDS SQL Server 資料庫](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/CHAP_GettingStarted.CreatingConnecting.SQLServer.html)。
* [使用 Azure 入口網站在 Azure SQL Database 中建立資料庫](https://docs.microsoft.com/azure/sql-database/sql-database-get-started-portal)，或[在 SQL 受控執行個體中建立資料庫](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-get-started)，然後建立名為 **AdventureWorks2012** 的空資料庫。 
* 下載並安裝 [Data Migration Assistant](https://www.microsoft.com/download/details.aspx?id=53595) (DMA) v3.3 或更新版本。
* 使用 Azure Resource Manager 部署模型，為 Azure 資料庫移轉服務建立 Microsoft Azure 虛擬網路。 如果您要遷移至 SQL 受控執行個體，請務必在 SQL 受控執行個體所用的相同虛擬網路中建立 DMS 執行個體 (但在不同的子網路中)。  或者，如果您針對 DMS 使用不同的虛擬網路，您需要在兩個虛擬網路之間建立虛擬網路對等互連。 如需建立虛擬網路的詳細資訊，請參閱[虛擬網路文件](https://docs.microsoft.com/azure/virtual-network/)，特別是快速入門文章，裡面會提供逐步操作詳細資料。

    > [!NOTE]
    > 在虛擬網路設定期間，如果您使用 ExpressRoute 搭配與 Microsoft 對等互連的網路，請將下列服務[端點](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview)新增至將佈建服務的子網路：
    >
    > * 目標資料庫端點 (例如，SQL 端點、Cosmos DB 端點等)
    > * 儲存體端點
    > * 服務匯流排端點
    >
    > 此為必要設定，因為 Azure 資料庫移轉服務沒有網際網路連線。 

* 確定您的虛擬網路網路安全性群組規則不會對 Azure 資料庫移轉服務封鎖下列輸入通訊埠：443、53、9354、445、12000。 如需虛擬網路 NSG 流量篩選的詳細資訊，請參閱[使用網路安全性群組來篩選網路流量](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg)。
* 設定[用於 Database Engine 存取的 Windows 防火牆](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access)。
* 開啟您的 Windows 防火牆以允許 Azure 資料庫移轉服務存取來源 SQL Server，其預設會通過 TCP 連接埠 1433。
* 請為 SQL Database 建立伺服器層級的[防火牆規則](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure)，以允許 Azure 資料庫移轉服務存取目標資料庫。 提供用於 Azure 資料庫移轉服務之虛擬網路的子網路範圍。
* 確定用來連線到來源 RDS SQL Server 執行個體的認證，與所有要遷移的資料庫上屬於 “Processadmin” 伺服器角色成員和 "db_owner" 資料庫角色成員的帳戶相關聯。
* 確定用來連線至目標資料庫的認證，在 SQL Database 中的目標資料庫上具有 CONTROL DATABASE 權限，而且是 sysadmin 角色的成員 (如果遷移至 SQL 受控執行個體中的資料庫)。
* 來源 RDS SQL Server 版本必須是 SQL Server 2012 或更新版本。 若要確認您的 SQL Server 執行個體正在執行的版本，請參閱[如何判斷 SQL Server 及其元件的版本、版次及更新層級](https://support.microsoft.com/help/321185/how-to-determine-the-version-edition-and-update-level-of-sql-server-an)一文。
* 在 RDS SQL Server 資料庫以及選取要遷移的所有使用者資料表上啟用異動資料擷取 (CDC)。
    > [!NOTE]
    > 您可以使用下列指令碼，在 RDS SQL Server 資料庫上啟用 CDC。
    ```
    exec msdb.dbo.rds_cdc_enable_db 'AdventureWorks2012'
    ```
    > 您可以使用下列指令碼，在所有資料表上啟用 CDC。
    ```
    use <Database name>
    go
    exec sys.sp_cdc_enable_table 
    @source_schema = N'Schema name', 
    @source_name = N'table name', 
    @role_name = NULL, 
    @supports_net_changes = 1 --for PK table 1, non PK tables 0
    GO
    ```
* 在目標資料庫上停用資料庫觸發程序。
    > [!NOTE]
    > 您可以使用下列查詢，在目標資料庫上尋找資料庫觸發程序：
    ```
    Use <Database name>
    go
    select * from sys.triggers
    DISABLE TRIGGER (Transact-SQL)
    ```
    如需詳細資訊，請參閱 [DISABLE TRIGGER (Transact-SQL)](https://docs.microsoft.com/sql/t-sql/statements/disable-trigger-transact-sql?view=sql-server-2017) 一文。

## <a name="migrate-the-sample-schema"></a>移轉範例結構描述
使用 DMA 來遷移結構描述。

> [!NOTE]
> 在 DMA 中建立移轉專案之前，請務必確認您已依照必要條件中的說明在 SQL Database 或 SQL 受控執行個體中佈建資料庫。 基於本教學課程的目的，資料庫的名稱會假設為 **AdventureWorks2012**，但您可以命名為不同的名稱。

若要遷移 **AdventureWorks2012** 結構描述，請執行下列步驟：

1. 在資料移轉小幫手中，選取新增 (+) 圖示，然後在 [專案類型]**** 底下選取 [移轉]****。
2. 在 [Source server type] \(來源伺服器類型\)**** 文字方塊中指定專案名稱，選取 [SQL Server]****，然後在 [Target server type] \(目標伺服器類型\)**** 文字方塊中，選取 [Azure SQL Database]****。

    > [!NOTE]
    > 針對 [目標伺服器類型]，請選取 [Azure SQL Database]，以遷移至 Azure SQL Database 和 SQL 受控執行個體。

3. 在 [Migration Scope] \(移轉範圍\)**** 下，選取 [Schema only] \(僅結構描述\)****。

    執行先前的步驟之後，DMA 介面應會如下圖所示：

    ![建立 Data Migration Assistant 專案](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/dma-create-project.png)

4. 選取 [Create] \(建立\)  以建立專案。
5. 在 DMA 中，為您的 SQL Server 指定來源連線詳細資料，選取 [連線]****，然後選取 [AdventureWorks2012]**** 資料庫。

    ![Data Migration Assistant 來源連線詳細資料](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/dma-source-connect.png)

6. 在 [連線到目標伺服器] 下方選取 [下一步]，針對 SQL Database 或 SQL 受控執行個體中的資料庫，指定目標連線詳細資料，選取 [連線]，然後選取您預先佈建的 [AdventureWorksAzure] 資料庫。

    ![Data Migration Assistant 目標連線詳細資料](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/dma-target-connect.png)

7. 選取 [下一步] 前進到 [選取物件]  畫面，您可以指定 **AdventureWorks2012** 資料庫中需要部署的結構描述物件。

    預設會選取所有物件。

    ![產生 SQL 指令碼](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/dma-assessment-source.png)

8. 選取 [Generate SQL script] \(產生 SQL 指令碼\)**** 來建立 SQL 指令碼，然後檢閱指令碼是否有任何錯誤。

    ![結構描述指令碼](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/dma-schema-script.png)

9. 選取 [部署結構描述] 以部署結構描述，並在結構描述部署好之後，檢查目標伺服器是否有任何異常狀況。

    ![部署結構描述](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/dma-schema-deploy.png)

## <a name="register-the-microsoftdatamigration-resource-provider"></a>註冊 Microsoft.DataMigration 資源提供者

1. 登入 Azure 入口網站，選取 [所有服務]****，然後選取 [訂用帳戶]****。

   ![顯示入口網站訂用帳戶](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/portal-select-subscription1.png)

2. 選取您要在其中建立 Azure 資料庫移轉服務執行個體的訂用帳戶，然後選取 [資源提供者]****。

    ![顯示資源提供者](media/tutorial-sql-server-to-azure-sql-online/portal-select-resource-provider.png)

3. 搜尋移轉，然後在 [Microsoft.DataMigration] 的右邊，選取 [註冊]。

    ![註冊資源提供者](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/portal-register-resource-provider.png)    

## <a name="create-an-instance"></a>建立執行個體

1. 在 Azure 入口網站中，選取 [+ 建立資源]****，搜尋「Azure 資料庫移轉服務」，然後從下拉式清單選取 [Azure 資料庫移轉服務]****。

    ![Azure Marketplace](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/portal-marketplace.png)

2. 在 [Azure 資料庫移轉服務]**** 畫面上，選取 [建立]****。

    ![建立 Azure 資料庫移轉服務執行個體](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/dms-create1.png)
  
3. 在 [建立移轉服務]**** 畫面上，指定服務的名稱、訂用帳戶，以及新的或現有的資源群組。

4. 選取您要在其中建立 Azure 資料庫移轉服務執行個體的位置。 

5. 選取現有的虛擬網路或建立新的虛擬網路。

    虛擬網路會為 Azure 資料庫移轉服務提供來源 SQL Server 和目標SQL Database 或 SQL 受控執行個體的存取權。

    如需如何在 Azure 入口網站中建立虛擬網路的詳細資訊，請參閱[使用 Azure 入口網站建立虛擬網路](https://aka.ms/DMSVnet)一文。

6. 選取定價層；對此線上移轉，務必選取進階定價層。

    如需成本和定價層的詳細資訊，請參閱[定價分頁](https://aka.ms/dms-pricing)。

     ![設定 Azure 資料庫移轉服務執行個體設定](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/dms-settings3.png)

7. 選取 [建立]**** 以建立服務。

## <a name="create-a-migration-project"></a>建立移轉專案

建立服務之後，請在 Azure 入口網站中找出該服務，然後建立新的移轉專案。

1. 在 Azure 入口網站中，選取 [所有服務]****，搜尋 Azure 資料庫移轉服務，然後選取 [Azure 資料庫移轉服務]****。

      ![找出 Azure 資料庫移轉服務的所有執行個體](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/dms-search.png)

2. 在 [Azure 資料庫移轉服務]**** 畫面上，搜尋您建立的 Azure 資料庫移轉服務執行個體名稱，然後選取該執行個體。

     ![找出您的 Azure 資料庫移轉服務執行個體](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/dms-instance-search.png)

3. 選取 [+ 新增移轉專案]****。
4. 在 [新增移轉專案]**** 畫面上指定專案名稱，並在 [來源伺服器類型]**** 文字方塊中中選取 [AWS RDS for SQL Server]****，然後在 [目標伺服器類型]**** 文字方塊中選取 [Azure SQL Database]****。

    > [!NOTE]
    > 針對 [目標伺服器類型]，請選取 [Azure SQL Database]，以遷移至 SQL Database 和 SQL 受控執行個體。

5. 在 [選擇活動類型] 區段中，選取 [線上資料移轉]。

    > [!IMPORTANT]
    > 請務必選取 [線上資料移轉]****；此案例不支援離線移轉。

    ![建立資料庫移轉服務專案](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/dms-create-project4.png)

    > [!NOTE]
    > 或者，您可以選擇 [僅建立專案]**** 以立即建立移轉專案，並於後續再執行移轉。

6. 選取 [儲存]。

7. 選取 [建立及執行活動]****，以建立專案並執行移轉活動。

    ![建立並執行資料庫移轉服務活動](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/dms-create-and-run-activity1.png)

## <a name="specify-source-details"></a>指定來源詳細資料

1. 在 [移轉來源詳細資料]**** 畫面上，指定來源 SQL Server 執行個體的連線詳細資料。

    請務必使用來源 SQL Server 執行個體名稱的完整網域名稱 (FQDN)。

2. 如果您尚未在來源伺服器上安裝信任的憑證，請選取 [信任伺服器憑證]**** 核取方塊。

    未安裝信任的憑證時，SQL Server 會在執行個體啟動時產生自我簽署憑證。 此憑證用來加密用戶端連線的認證。

    > [!CAUTION]
    > 使用自我簽署憑證加密的 TLS 連線不會提供增強式安全性。 這種連線容易受到攔截式攻擊。 在生產環境或連線到網際網路的伺服器上，您不應該仰賴使用自我簽署憑證的 TLS。

   ![來源詳細資料](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/dms-source-details3.png)

## <a name="specify-target-details"></a>指定目標詳細資料

1. 選取 [儲存]，然後在 [移轉目標詳細資料] 畫面上，指定 Azure 中目標資料庫的連線詳細資料。

    ![選取目標](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/dms-select-target3.png)

2. 選取 [儲存]****，然後在 [對應到目標資料庫]**** 畫面上，對應要進行移轉的來源和目標資料庫。

    如果目標資料庫包含與來源資料庫相同的資料庫名稱，Azure 資料庫移轉服務依預設會選取目標資料庫。

    ![對應到目標資料庫](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/dms-map-targets-activity4.png)

3. 在 [選取資料表]**** 畫面上選取 [儲存]****，展開資料表清單，然後檢閱受影響欄位的清單。

    Azure 資料庫移轉服務會自動選取存在於目標資料庫上的所有空來源資料表。 如果您想要重新遷移已包含資料的資料表，就必須在此畫面上明確地選取資料表。

    ![選取資料表](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/dms-configure-setting-activity4.png)

4. 在設定下列 [進階線上移轉設定]**** 之後，選取 [儲存]****。

    | 設定 | 描述 |
    | ------------- | ------------- |
    | **要平行載入的資料表數目上限** | 指定 DMS 在移轉期間平行執行的資料表數目。 預設值為 5，但可將其設定為最佳值，以符合任何 POC 移轉的特定移轉需求。 |
    | **當來源資料表已截斷時** | 指定 DMS 是否會在移轉期間截斷目標資料表。 如果會在移轉過程中截斷一或多個資料表，則此設定很有幫助。 |
    | **進行大型物件 (LOB) 資料的設定** | 指定 DMS 是否能遷移無限制的 LOB 資料，或限制遷移至特定大小的 LOB 資料。  當移轉的 LOB 資料有所限制時，就會截斷任何超出該限制的 LOB 資料。 對於生產遷移，建議選取 [不限制 LOB 大小]**** 以免資料遺失。 指定不限制 LOB 大小時，請選取 [當 LOB 大小小於指定的 (KB) 時遷移單一區塊中的 LOB 資料]**** 核取方塊，以改善效能。 |

    ![設定進階線上移轉設定](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/dms-advanced-online-migration-settings.png)

5. 選取 [儲存]****，在 [移轉摘要]**** 畫面的 [活動名稱]**** 文字方塊中，指定移轉活動的名稱，然後檢閱摘要，以確定來源和目標詳細資料都與您先前的指定相符。

    ![移轉摘要](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/dms-migration-summary.png)

## <a name="run-the-migration"></a>執行移轉

* 選取 [執行移轉]****。

    [移轉活動] 視窗隨即出現，且活動的 [狀態] 為 [正在初始化]。

    ![活動狀態 - 正在初始化](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/dms-activity-status2.png)

## <a name="monitor-the-migration"></a>監視移轉

1. 在移轉活動畫面上，選取 [重新整理]**** 以更新顯示，直到移轉的 [狀態]**** 顯示為 [執行中]**** 為止。

2. 按一下特定資料庫，以顯示 [載入完整資料]**** 和 [累加式資料同步]**** 作業的移轉狀態。

    ![活動狀態 - 進行中](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/dms-activity-in-progress.png)

## <a name="perform-migration-cutover"></a>執行完全移轉

初始完整載入完成後，資料庫會標示為 [已可執行完全移轉]****。

1. 當您準備好要完成資料庫移轉後，請選取 [開始完全移轉]****。

    ![開始完全移轉](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/dms-start-cutover.png)

2. 確實停止所有傳入來源資料庫的交易；等到 [暫止的變更]**** 計數器顯示為 **0** 為止。
3. 選取 [確認]****，然後選取 [套用]****。
4. 當資料庫移轉狀態顯示為 [已完成] 後，請將應用程式連線至新的目標資料庫。

    ![活動狀態 - 已完成](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/dms-activity-completed.png)

## <a name="next-steps"></a>後續步驟

* 如需執行線上移轉至 Azure 時的已知問題和限制，請參閱[線上移轉的已知問題和因應措施](known-issues-azure-sql-online.md)一文。
* 如需料庫移轉服務的相關資訊，請參閱[什麼是資料庫移轉服務？](https://docs.microsoft.com/azure/dms/dms-overview)一文。
* 如需 SQL Database 的相關資訊，請參閱[什麼是 SQL Database 服務？](https://docs.microsoft.com/azure/sql-database/sql-database-technical-overview)。
* 如需 SQL 受控執行個體的詳細資訊，請參閱[什麼是 SQL 受控執行個體](https://docs.microsoft.com/azure/azure-sql/managed-instance/sql-managed-instance-paas-overview)一文。
