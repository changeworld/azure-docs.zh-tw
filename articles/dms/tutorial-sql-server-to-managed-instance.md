---
title: 教學課程：將 SQL Server 遷移至 SQL 受控執行個體
titleSuffix: Azure Database Migration Service
description: 瞭解如何使用 Azure 資料庫移轉服務從 SQL Server 遷移至 Azure SQL 受控執行個體。
services: dms
author: HJToland3
ms.author: jtoland
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019,fasttrack-edit
ms.topic: article
ms.date: 01/08/2020
ms.openlocfilehash: f20b9236631057449a337e4e161b7468f995e306
ms.sourcegitcommit: 124f7f699b6a43314e63af0101cd788db995d1cb
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/08/2020
ms.locfileid: "86085524"
---
# <a name="tutorial-migrate-sql-server-to-an-azure-sql-managed-instance-offline-using-dms"></a>教學課程：使用 DMS 將 SQL Server 遷移至 Azure SQL 受控執行個體離線

您可以使用 Azure 資料庫移轉服務，將資料庫從 SQL Server 實例遷移至[AZURE SQL 受控執行個體](../azure-sql/managed-instance/sql-managed-instance-paas-overview.md)。 如需其他可能需要手動操作的方法，請參閱[SQL Server 實例遷移至 SQL 受控執行個體](../azure-sql/managed-instance/migrate-to-instance-from-sql-server.md)一文。

在本教學課程中，您會使用 Azure 資料庫移轉服務，將**Adventureworks2012**資料庫從 SQL Server 的內部部署實例遷移至 SQL 受控執行個體。

在本教學課程中，您會了解如何：
> [!div class="checklist"]
>
> - 建立 Azure 資料庫移轉服務的執行個體。
> - 使用 Azure 資料庫移轉服務來建立移轉專案。
> - 執行移轉。
> - 監視移轉。
> - 下載移轉報告。

> [!IMPORTANT]
> 若要從 SQL Server 到 SQL 受控執行個體進行離線遷移，Azure 資料庫移轉服務可以為您建立備份檔案。 或者，您可以在 SMB 網路共用中，提供服務將用來遷移資料庫的最新完整資料庫備份。 請勿將多個備份附加到單一備份媒體中；請在不同的備份檔案中製作每個備份。 請注意，您也可以使用壓縮的備份，以避免遷移大型備份時可能發生的問題。

[!INCLUDE [online-offline](../../includes/database-migration-service-offline-online.md)]

本文說明從 SQL Server 到 SQL 受控執行個體的離線遷移。 如需線上遷移，請參閱[使用 DMS 將 SQL Server 遷移至 SQL 受控執行個體](tutorial-sql-server-managed-instance-online.md)。

## <a name="prerequisites"></a>Prerequisites

若要完成本教學課程，您需要：

- 使用 Azure Resource Manager 部署模型建立 Azure 資料庫移轉服務的 Microsoft Azure 虛擬網路，以使用[ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction)或[VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways)為您的內部部署來源伺服器提供站對站連線能力。 [瞭解使用 Azure 資料庫移轉服務進行 SQL 受控執行個體遷移的網路拓朴](https://aka.ms/dmsnetworkformi)。 如需有關建立虛擬網路的詳細資訊，請參閱[虛擬網路檔](https://docs.microsoft.com/azure/virtual-network/)，特別是快速入門文章，其中包含逐步解說的詳細資料。

    > [!NOTE]
    > 在虛擬網路設定期間，如果您搭配與 Microsoft 對等互連的網路使用 ExpressRoute，請將下列服務[端點](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview)新增至將布建服務的子網：
    > - 目標資料庫端點 (例如，SQL 端點、Cosmos DB 端點等)
    > - 儲存體端點
    > - 服務匯流排端點
    >
    > 此為必要設定，因為 Azure 資料庫移轉服務沒有網際網路連線。

- 請確定您的虛擬網路網路安全性群組規則不會對 Azure 資料庫移轉服務封鎖下列輸入通訊埠：443、53、9354、445、12000。 如需虛擬網路 NSG 流量篩選的詳細資訊，請參閱[使用網路安全性群組來篩選網路流量](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg)一文。
- 設定[用於來源資料庫引擎存取的 Windows 防火牆](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access)。
- 開啟您的 Windows 防火牆以允許 Azure 資料庫移轉服務存取來源 SQL Server (依預設會使用 TCP 連接埠 1433)。
- 如果您使用動態連接埠執行多個具名 SQL Server 執行個體，您可以啟用 SQL Browser 服務並允許通過防火牆存取 UDP 連接埠 1434，讓 Azure 資料庫移轉服務連線來源伺服器上的具名執行個體。
- 如果您是在來源資料庫前面使用防火牆設備，您可能必須新增防火牆規則，才能讓 Azure 資料庫移轉服務存取用於移轉的來源資料庫，以及透過 SMB 連接埠 445 存取檔案。
- 遵循在[Azure 入口網站中建立 sql 受控執行個體一](https://aka.ms/sqldbmi)文中的詳細資料，建立 SQL 受控執行個體。
- 確定用來連接來源 SQL Server 和目標 SQL 受控執行個體的登入是系統管理員（sysadmin）伺服器角色的成員。

    >[!NOTE]
    >根據預設，Azure 資料庫移轉服務僅支援遷移 SQL 登入。 不過，您可以藉由下列方式啟用遷移 Windows 登入的功能：
    >
    >- 確保目標 SQL 受控執行個體具有 AAD 讀取權限，可透過具有「**公司系統管理員**」或「**全域管理員**」角色的使用者 Azure 入口網站來設定。
    >- 設定 Azure 資料庫移轉服務實例，以啟用 Windows 使用者/群組登入遷移，此功能是透過 Azure 入口網站在 [設定] 頁面上設定。 啟用此設定之後，請重新開機服務，變更才會生效。
    >
    > 重新開機服務之後，Windows 使用者/群組登入就會出現在可供遷移的登入清單中。 對於您遷移的任何 Windows 使用者/群組登入，系統會提示您提供相關聯的功能變數名稱。 不支援服務使用者帳戶（具有功能變數名稱 NT 授權單位的帳戶）和虛擬使用者帳戶（具有功能變數名稱 NT 服務的帳戶名稱）。

- 建立 Azure 資料庫移轉服務可用來備份來源資料庫的網路共用。
- 確認執行來源 SQL Server 執行個體的服務帳戶在您所建立的網路共用上具有寫入權限，而且來源伺服器的電腦帳戶對相同的共用具備讀取/寫入存取權。
- 記下在您先前建立的網路共用上具有完整控制權限的 Windows 使用者 (和密碼)。 Azure 資料庫移轉服務會模擬使用者認證，以便將備份檔案上傳到 Azure 儲存體容器以進行還原作業。
- 使用[使用儲存體總管來管理 Azure Blob 儲存體資源](https://docs.microsoft.com/azure/vs-azure-tools-storage-explorer-blobs#get-the-sas-for-a-blob-container) 一文中的步驟來建立 blob 容器及取出其 SAS URI；在建立 SAS URI 時，請務必選取原則視窗上的所有權限 (讀取、寫入、刪除、列出)。 此詳細資料會為 Azure 資料庫移轉服務提供儲存體帳戶容器的存取權，以上傳用來將資料庫移轉至 SQL 受控執行個體的備份檔案。

    > [!NOTE]
    > 在[設定遷移設定](https://docs.microsoft.com/azure/dms/tutorial-sql-server-to-managed-instance#configure-migration-settings)步驟期間，Azure 資料庫移轉服務不支援使用帳戶層級 SAS 權杖進行儲存體帳戶設定。
    
## <a name="register-the-microsoftdatamigration-resource-provider"></a>註冊 Microsoft.DataMigration 資源提供者

1. 登入 Azure 入口網站，選取 [所有服務]****，然後選取 [訂用帳戶]****。

    ![顯示入口網站訂用帳戶](media/tutorial-sql-server-to-managed-instance/portal-select-subscriptions.png)

2. 選取您要在其中建立 Azure 資料庫移轉服務執行個體的訂用帳戶，然後選取 [資源提供者]。

    ![顯示資源提供者](media/tutorial-sql-server-to-managed-instance/portal-select-resource-provider.png)

3. 搜尋移轉，然後在 [Microsoft.DataMigration] 的右邊，選取 [註冊]。

    ![註冊資源提供者](media/tutorial-sql-server-to-managed-instance/portal-register-resource-provider.png)

## <a name="create-an-azure-database-migration-service-instance"></a>建立 Azure 資料庫移轉服務執行個體

1. 在 [Azure 入口網站中，選取 [+**建立資源**]，搜尋 [ **Azure 資料庫移轉服務**]，然後從下拉式清單中選取 [ **azure 資料庫移轉服務**]。

    ![Azure Marketplace](media/tutorial-sql-server-to-managed-instance/portal-marketplace.png)

2. 在 [Azure 資料庫移轉服務]**** 畫面上，選取 [建立]****。

    ![建立 Azure 資料庫移轉服務執行個體](media/tutorial-sql-server-to-managed-instance/dms-create1.png)

3. 在 [建立移轉服務]**** 畫面上，指定服務的名稱、訂用帳戶，以及新的或現有的資源群組。

4. 選取您要建立 DMS 執行個體的位置。

5. 選取現有的虛擬網路或建立一個。

    虛擬網路會為 Azure 資料庫移轉服務提供來源 SQL Server 和目標 SQL 受控執行個體的存取權。

    如需有關如何在 Azure 入口網站中建立虛擬網路的詳細資訊，請參閱[使用 Azure 入口網站建立虛擬網路](https://aka.ms/DMSVnet)一文。

    如需其他詳細資訊，請參閱[使用 Azure 資料庫移轉服務的 AZURE SQL 受控執行個體遷移的網路拓朴](https://aka.ms/dmsnetworkformi)一文。

6. 選取定價層。

    如需成本和定價層的詳細資訊，請參閱[定價頁面](https://aka.ms/dms-pricing)。

    ![建立 DMS 服務](media/tutorial-sql-server-to-managed-instance/dms-create-service2.png)

7. 選取 [建立]**** 以建立服務。

## <a name="create-a-migration-project"></a>建立移轉專案

建立服務執行個體之後，請在 Azure 入口網站中找出該服務，然後建立新的移轉專案。

1. 在 Azure 入口網站中，選取 [所有服務]****，搜尋 Azure 資料庫移轉服務，然後選取 [Azure 資料庫移轉服務]****。

    ![找出 Azure 資料庫移轉服務的所有執行個體](media/tutorial-sql-server-to-managed-instance/dms-search.png)

2. 在 [ **Azure 資料庫移轉服務**] 畫面上，搜尋您建立的實例名稱，然後選取該實例。

3. 選取 [+ 新增移轉專案]****。

4. 在 [**新增遷移專案**] 畫面上指定專案名稱、在 [**來源伺服器類型**] 文字方塊中選取 [ **SQL Server**]、在 [**目標伺服器類型**] 文字方塊中選取 [ **Azure SQL 受控執行個體**]，然後針對 **[選擇活動類型**]，選取 [**離線資料移轉**]。

   ![建立 DMS 專案](media/tutorial-sql-server-to-managed-instance/dms-create-project2.png)

5. 選取 [Create] \(建立\) 以建立專案。

## <a name="specify-source-details"></a>指定來源詳細資料

1. 在 [移轉來源詳細資料]**** 畫面上，指定來源 SQL Server 的連線詳細資料。

2. 如果您尚未在伺服器上安裝信任的憑證，請選取 [信任伺服器憑證]**** 核取方塊。

    未安裝信任的憑證時，SQL Server 會在執行個體啟動時產生自我簽署憑證。 此憑證用來加密用戶端連線的認證。

    > [!CAUTION]
    > 使用自我簽署憑證加密的 TLS 連線不會提供強大的安全性。 這種連線容易受到攔截式攻擊。 您不應該在生產環境中使用自我簽署憑證，或在連線到網際網路的伺服器上依賴 TLS。

   ![來源詳細資料](media/tutorial-sql-server-to-managed-instance/dms-source-details1.png)

3. 選取 [儲存]。

4. 在 [選取來源資料庫]**** 畫面上，選取 [Adventureworks2012]**** 資料庫進行移轉。

   ![選取來源資料庫](media/tutorial-sql-server-to-managed-instance/dms-source-database1.png)

    > [!IMPORTANT]
    > 如果您使用 SQL Server Integration Services （SSIS），DMS 目前不支援將 SSIS 專案/套件（SSISDB）的目錄資料庫從 SQL Server 遷移至 SQL 受控執行個體。 不過，您可以在 Azure Data Factory （ADF）中布建 SSIS，並將 SSIS 專案/套件重新部署至 SQL 受控執行個體所裝載的目的地 SSISDB。 如需有關遷移 SSIS 套件的詳細資訊，請參閱[將 SQL Server Integration Services 套件遷移到 Azure](https://docs.microsoft.com/azure/dms/how-to-migrate-ssis-packages) 一文。

5. 選取 [儲存]。

## <a name="specify-target-details"></a>指定目標詳細資料

1. 在 [**遷移目標詳細資料**] 畫面上，指定目標的連線詳細資料，這是您要將**AdventureWorks2012**資料庫移轉到其中的預先布建 SQL 受控執行個體。

    如果您尚未布建 SQL 受控執行個體，請選取[連結](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-get-started)以協助您布建實例。 您仍然可以繼續建立專案，然後在 SQL 受控執行個體準備就緒時，返回此特定專案來執行遷移。

    ![選取目標](media/tutorial-sql-server-to-managed-instance/dms-target-details2.png)

2. 選取 [儲存]。

## <a name="select-source-databases"></a>選取來源資料庫

1. 在 [選取來源資料庫]**** 畫面上，選取您要遷移的來源資料庫。

    ![選取來源資料庫](media/tutorial-sql-server-to-managed-instance/select-source-databases.png)

2. 選取 [儲存]。

## <a name="select-logins"></a>選取登入

1. 在 [選取登入]**** 畫面上，選取您要移轉的登入。

    >[!NOTE]
    >根據預設，Azure 資料庫移轉服務僅支援遷移 SQL 登入。 若要啟用遷移 Windows 登入的支援，請參閱本教學**課程的必要條件**一節。

    ![選取登入](media/tutorial-sql-server-to-managed-instance/select-logins.png)

2. 選取 [儲存]。

## <a name="configure-migration-settings"></a>設定移轉設定

1. 在 [設定移轉設定]**** 畫面上，提供下列詳細資料：

    | | |
    |--------|---------|
    |**選擇來源備份選項** | 當您已有可供 DMS 用於資料庫移轉的完整備份檔案時，請選擇 [我會提供最新的備份檔案]**** 選項。 如果您想要讓 DMS 先建立來源資料庫完整備份，並將其用於移轉，請選擇 [我允許 Azure 資料庫移轉服務建立備份檔案]**** 選項。 |
    |**網路位置共用** | Azure 資料庫移轉服務可將來源資料庫備份移入的本機 SMB 網路共用。 執行來源 SQL Server 執行個體的服務帳戶在此網路共用上必須具有寫入權限。 在網路共用中提供伺服器的 FQDN 或 IP 位址，例如，'\\\servername.domainname.com\backupfolder' 或 '\\\IP address\backupfolder'。|
    |**使用者名稱** | 請確定 Windows 使用者對於您先前提供的網路共用具有完整控制權限。 Azure 資料庫移轉服務會模擬使用者認證，將備份檔案上傳到 Azure 儲存體容器以進行還原作業。 如果選取已啟用 TDE 的資料庫進行移轉，則上述的 Windows 使用者必須是內建的系統管理員帳戶，且必須停用 Azure 資料庫移轉服務的[使用者帳戶控制](https://docs.microsoft.com/windows/security/identity-protection/user-account-control/user-account-control-overview)，才能上傳及刪除憑證檔案。 |
    |**密碼** | 使用者的密碼。 |
    |**儲存體帳戶設定** | 提供 Azure 資料庫移轉服務的 SAS URI，可讓您存取儲存體帳戶容器，服務會將備份檔案上傳到其中，並用於將資料庫移轉至 SQL 受控執行個體。 [瞭解如何取得 blob 容器的 SAS URI](https://docs.microsoft.com/azure/vs-azure-tools-storage-explorer-blobs#get-the-sas-for-a-blob-container)。 此 SAS URI 必須適用于 blob 容器，而不是儲存體帳戶的。|
    |**TDE 設定** | 如果您要遷移的源資料庫已啟用透明資料加密（TDE），您必須具有目標 SQL 受控執行個體的寫入權限。  從下拉式功能表中選取要在其中布建 SQL 受控執行個體的訂用帳戶。  在下拉式功能表中選取目標 **Azure SQL 資料庫受控執行個體**。 |

    ![設定移轉設定](media/tutorial-sql-server-to-managed-instance/dms-configure-migration-settings3.png)

2. 選取 [儲存]。

## <a name="review-the-migration-summary"></a>檢閱移轉摘要

1. 在 [移轉摘要]**** 畫面上的 [活動名稱]**** 文字方塊中，指定移轉活動的名稱。

2. 展開 [驗證選項]**** 區段以顯示 [選擇驗證選項]**** 畫面，指定是否驗證已遷移資料庫的查詢正確性，然後選取 [儲存]****。

3. 檢閱並確認與移轉專案相關聯的詳細資料。

    ![移轉專案摘要](media/tutorial-sql-server-to-managed-instance/dms-project-summary2.png)

4. 選取 [儲存]。

## <a name="run-the-migration"></a>執行移轉

- 選取 [執行移轉]****。

  [移轉活動] 視窗隨即出現，而且活動的狀態為 [擱置]****。

## <a name="monitor-the-migration"></a>監視移轉

1. 在移轉活動畫面中，選取 [重新整理]**** 以更新顯示。

   ![移轉活動進行中](media/tutorial-sql-server-to-managed-instance/dms-monitor-migration1.png)

    您可以進一步展開資料庫和登入類別，以監視個別伺服器物件的移轉狀態。

   ![移轉活動進行中](media/tutorial-sql-server-to-managed-instance/dms-monitor-migration-extend.png)

2. 移轉完成之後，請選取 [下載報告]**** 以取得報告，其中會列出與移轉程序相關聯的詳細資料。

3. 確認目標 SQL 受控執行個體環境上的目標資料庫。

## <a name="next-steps"></a>後續步驟

- 如需示範如何使用 T-sql RESTORE 命令將資料庫移轉至 SQL 受控執行個體的教學課程，請參閱[使用 restore 命令將備份還原至 sql 受控執行個體](../sql-database/sql-database-managed-instance-restore-from-backup-tutorial.md)。
- 如需 SQL 受控執行個體的詳細資訊，請參閱[什麼是 sql 受控執行個體](../azure-sql/managed-instance/sql-managed-instance-paas-overview.md)。
- 如需將應用程式連接到 SQL 受控執行個體的詳細資訊，請參閱[連接應用程式](../azure-sql/managed-instance/connect-application-instance.md)。
