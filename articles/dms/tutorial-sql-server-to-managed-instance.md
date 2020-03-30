---
title: 教程：將 SQL 伺服器遷移到 SQL 託管實例
titleSuffix: Azure Database Migration Service
description: 了解如何使用 Azure 資料庫移轉服務，從內部部署 SQL Server 遷移至 Azure SQL Database 受控執行個體。
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
ms.openlocfilehash: 416be7de4b3cef4fb6e1bcfd09d934937f8c96d8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80297736"
---
# <a name="tutorial-migrate-sql-server-to-an-azure-sql-database-managed-instance-offline-using-dms"></a>教程：使用 DMS 將 SQL 伺服器離線遷移到 Azure SQL 資料庫託管實例

您可以使用 Azure 資料庫移轉服務，將內部部署 SQL Server 執行個體的資料庫遷移至 [Azure SQL Database 受控執行個體](../sql-database/sql-database-managed-instance.md)。 有關可能需要一些手動工作的其他方法，請參閱有關[SQL Server 實例遷移到 Azure SQL 資料庫託管實例](../sql-database/sql-database-managed-instance-migrate.md)的文章。

在本教學課程中，您將使用 Azure 資料庫移轉服務，將 **Adventureworks2012** 資料庫從內部部署 SQL Server 執行個體遷移至 SQL Database 受控執行個體。

在本教學課程中，您會了解如何：
> [!div class="checklist"]
>
> - 建立 Azure 資料庫移轉服務的執行個體。
> - 使用 Azure 資料庫移轉服務來建立移轉專案。
> - 執行移轉。
> - 監視移轉。
> - 下載移轉報告。

> [!IMPORTANT]
> 執行從 SQL Server 到 SQL Database 受控執行個體的離線移轉時，Azure 資料庫移轉服務可為您建立備份檔案。 或者，您可以在 SMB 網路共用中，提供服務將用來遷移資料庫的最新完整資料庫備份。 請勿將多個備份附加到單一備份媒體中；請在不同的備份檔案中製作每個備份。 請注意，您也可以使用壓縮的備份，以避免遷移大型備份時可能發生的問題。

[!INCLUDE [online-offline](../../includes/database-migration-service-offline-online.md)]

本文說明從 SQL Server 到 SQL Database 受控執行個體的離線移轉。 若要進行線上移轉，請參閱[使用 DMS 從 SQL Server 線上移轉至 Azure SQL Database 受控執行個體](tutorial-sql-server-managed-instance-online.md)。

## <a name="prerequisites"></a>Prerequisites

若要完成本教學課程，您需要：

- 通過使用 Azure 資源管理器部署模型為 Azure 資料庫移轉服務創建 Microsoft Azure 虛擬網路，該模型通過使用[ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction)或[VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways)提供到本地源伺服器的網站到網站的連接。 [了解使用 Azure 資料庫移轉服務進行 Azure SQL Database 受控執行個體移轉的網路拓樸](https://aka.ms/dmsnetworkformi)。 有關創建虛擬網路的詳細資訊，請參閱[虛擬網路文檔](https://docs.microsoft.com/azure/virtual-network/)，尤其是包含分步詳細資訊的快速入門文章。

    > [!NOTE]
    > 在虛擬網路設置期間，如果將 ExpressRoute 與網路對等互連到 Microsoft，則向將服務預配的子網添加以下服務[終結點](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview)：
    > - 目標資料庫端點 (例如，SQL 端點、Cosmos DB 端點等)
    > - 儲存體端點
    > - 服務匯流排端點
    >
    > 此為必要設定，因為 Azure 資料庫移轉服務沒有網際網路連線。

- 確保虛擬網路網路安全性群組規則不會阻止以下到 Azure 資料庫移轉服務的入站通訊連接埠：443、53、9354、445、12000。 有關虛擬網路 NSG 流量篩選的更多詳細資訊，請參閱文章["使用網路安全性群組篩選網路流量](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg)"。
- 設定[用於來源資料庫引擎存取的 Windows 防火牆](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access)。
- 開啟您的 Windows 防火牆以允許 Azure 資料庫移轉服務存取來源 SQL Server (依預設會使用 TCP 連接埠 1433)。
- 如果您使用動態連接埠執行多個具名 SQL Server 執行個體，您可以啟用 SQL Browser 服務並允許通過防火牆存取 UDP 連接埠 1434，讓 Azure 資料庫移轉服務連線來源伺服器上的具名執行個體。
- 如果您是在來源資料庫前面使用防火牆設備，您可能必須新增防火牆規則，才能讓 Azure 資料庫移轉服務存取用於移轉的來源資料庫，以及透過 SMB 連接埠 445 存取檔案。
- 通過按照文章"[在 Azure 門戶中創建 Azure SQL 資料庫託管實例"](https://aka.ms/sqldbmi)中的詳細資訊創建 Azure SQL 資料庫託管實例。
- 確定用來連線來源 SQL Server 和目標受控執行個體的登入是 sysadmin 伺服器角色的成員。

    >[!NOTE]
    >預設情況下，Azure 資料庫移轉服務僅支援遷移 SQL 登錄。 但是，您可以通過以下因素啟用遷移 Windows 登錄名的能力：
    >
    >- 確保目標 SQL 資料庫託管實例具有 AAD 讀取存取許可權，可通過 Azure 門戶配置**具有公司管理員**或**全域管理員**"角色的使用者。
    >- 配置 Azure 資料庫移轉服務實例以在"配置"頁上啟用通過 Azure 門戶設置的 Windows 使用者/組登錄遷移。 啟用此設置後，重新開機服務，使更改生效。
    >
    > 重新開機服務後，Windows 使用者/組登錄將顯示在可用於遷移的登錄清單中。 對於遷移的任何 Windows 使用者/組登錄名，系統將提示您提供關聯的功能變數名稱。 不支援服務使用者帳戶（具有功能變數名稱 NT AUTHORITY 的帳戶）和虛擬使用者帳戶（具有功能變數名稱 NT SERVICE 的帳戶名稱）。

- 建立 Azure 資料庫移轉服務可用來備份來源資料庫的網路共用。
- 確認執行來源 SQL Server 執行個體的服務帳戶在您所建立的網路共用上具有寫入權限，而且來源伺服器的電腦帳戶對相同的共用具備讀取/寫入存取權。
- 記下在您先前建立的網路共用上具有完整控制權限的 Windows 使用者 (和密碼)。 Azure 資料庫移轉服務類比使用者憑據，將備份檔案上載到 Azure 存儲容器以進行還原操作。
- 使用[使用儲存體總管來管理 Azure Blob 儲存體資源](https://docs.microsoft.com/azure/vs-azure-tools-storage-explorer-blobs#get-the-sas-for-a-blob-container) 一文中的步驟來建立 blob 容器及取出其 SAS URI；在建立 SAS URI 時，請務必選取原則視窗上的所有權限 (讀取、寫入、刪除、列出)。 此詳細資料會向 Azure 資料庫移轉服務提供您儲存體帳戶容器的存取權，以供上傳用於將資料庫遷移至 Azure SQL Database 受控執行個體的備份檔案。

## <a name="register-the-microsoftdatamigration-resource-provider"></a>註冊 Microsoft.DataMigration 資源提供者

1. 登入 Azure 入口網站，選取 [所有服務]****，然後選取 [訂用帳戶]****。

    ![顯示入口網站訂用帳戶](media/tutorial-sql-server-to-managed-instance/portal-select-subscriptions.png)

2. 選取您要在其中建立 Azure 資料庫移轉服務執行個體的訂用帳戶，然後選取 [資源提供者]****。

    ![顯示資源提供者](media/tutorial-sql-server-to-managed-instance/portal-select-resource-provider.png)

3. 搜索遷移，然後搜索 Microsoft 的右側 **。** **Register**

    ![註冊資源提供者](media/tutorial-sql-server-to-managed-instance/portal-register-resource-provider.png)

## <a name="create-an-azure-database-migration-service-instance"></a>建立 Azure 資料庫移轉服務執行個體

1. 在 Azure 門戶中，選擇 "**創建資源**"，搜索**Azure 資料庫移轉服務**，然後從下拉清單中選擇**Azure 資料庫移轉服務**。

    ![Azure Marketplace](media/tutorial-sql-server-to-managed-instance/portal-marketplace.png)

2. 在 [Azure 資料庫移轉服務]**** 畫面上，選取 [建立]****。

    ![建立 Azure 資料庫移轉服務執行個體](media/tutorial-sql-server-to-managed-instance/dms-create1.png)

3. 在 [建立移轉服務]**** 畫面上，指定服務的名稱、訂用帳戶，以及新的或現有的資源群組。

4. 選取您要建立 DMS 執行個體的位置。

5. 選擇現有虛擬網路或創建虛擬網路。

    虛擬網路為 Azure 資料庫移轉服務提供對源 SQL Server 和目標 Azure SQL 資料庫託管實例的訪問。

    有關如何在 Azure 門戶中創建虛擬網路的詳細資訊，請參閱使用 Azure[門戶創建虛擬網路](https://aka.ms/DMSVnet)的文章。

    如需其他詳細資訊，請參閱[了解使用 Azure 資料庫移轉服務進行 Azure SQL DB 受控執行個體移轉的網路拓樸](https://aka.ms/dmsnetworkformi)一文。

6. 選取定價層。

    有關成本和定價層的詳細資訊，請參閱[定價頁](https://aka.ms/dms-pricing)。

    ![建立 DMS 服務](media/tutorial-sql-server-to-managed-instance/dms-create-service2.png)

7. 選取 [建立]**** 以建立服務。

## <a name="create-a-migration-project"></a>建立移轉專案

建立服務執行個體之後，請在 Azure 入口網站中找出該服務，然後建立新的移轉專案。

1. 在 Azure 入口網站中，選取 [所有服務]****，搜尋 Azure 資料庫移轉服務，然後選取 [Azure 資料庫移轉服務]****。

    ![找出 Azure 資料庫移轉服務的所有執行個體](media/tutorial-sql-server-to-managed-instance/dms-search.png)

2. 在**Azure 資料庫移轉服務**螢幕上，搜索所創建的實例的名稱，然後選擇實例。

3. 選取 [+ 新增移轉專案]****。

4. 在 [新增移轉專案]**** 畫面上指定專案名稱、在 [來源伺服器類型]**** 文字方塊中選取 [SQL Server]****、在 [目標伺服器類型]**** 文字方塊中選取 [Azure SQL Database 受控執行個體]****，然後針對 [選擇活動類型]****，選取 [離線資料移轉]****。

   ![建立 DMS 專案](media/tutorial-sql-server-to-managed-instance/dms-create-project2.png)

5. 選取 [Create] \(建立\)**** 以建立專案。

## <a name="specify-source-details"></a>指定來源詳細資料

1. 在 [移轉來源詳細資料]**** 畫面上，指定來源 SQL Server 的連線詳細資料。

2. 如果您尚未在伺服器上安裝信任的憑證，請選取 [信任伺服器憑證]**** 核取方塊。

    未安裝信任的憑證時，SQL Server 會在執行個體啟動時產生自我簽署憑證。 此憑證用來加密用戶端連線的認證。

    > [!CAUTION]
    > 使用自簽章憑證加密的 TLS 連接不提供強大的安全性。 這種連線容易受到攔截式攻擊。 不應依賴 TLS 在生產環境中或連接到 Internet 的伺服器上使用自簽章憑證。

   ![來源詳細資料](media/tutorial-sql-server-to-managed-instance/dms-source-details1.png)

3. 選取 [儲存]****。

4. 在 [選取來源資料庫]**** 畫面上，選取 [Adventureworks2012]**** 資料庫進行移轉。

   ![選取來源資料庫](media/tutorial-sql-server-to-managed-instance/dms-source-database1.png)

    > [!IMPORTANT]
    > 如果您使用 SQL Server Integration Services (SSIS)，DMS 目前不支援將 SSIS 專案/套件 (SSISDB) 的目錄資料庫從 SQL Server 遷移至 Azure SQL Database 受控執行個體。 不過，您可以在 Azure Data Factory (ADF) 中佈建 SSIS，並將 SSIS 專案/套件重新部署到 Azure SQL Database 受控執行個體所裝載的目的地 SSISDB。 如需有關遷移 SSIS 套件的詳細資訊，請參閱[將 SQL Server Integration Services 套件遷移到 Azure](https://docs.microsoft.com/azure/dms/how-to-migrate-ssis-packages) 一文。

5. 選取 [儲存]****。

## <a name="specify-target-details"></a>指定目標詳細資料

1. 在 [移轉目標詳細資料]**** 畫面上指定目標的連線詳細資料；此目標是 **AdventureWorks2012** 資料庫所要移轉到的預先佈建 Azure SQL Database 受控執行個體。

    如果您尚未佈建 SQL Database 受控執行個體，請選取[連結](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-get-started)來協助您佈建執行個體。 您仍然可以繼續建立專案，然後在 Azure SQL Database 受控執行個體準備就緒時，返回此特定專案來執行移轉。

    ![選取目標](media/tutorial-sql-server-to-managed-instance/dms-target-details2.png)

2. 選取 [儲存]****。

## <a name="select-source-databases"></a>選取來源資料庫

1. 在 [選取來源資料庫]**** 畫面上，選取您要遷移的來源資料庫。

    ![選取來源資料庫](media/tutorial-sql-server-to-managed-instance/select-source-databases.png)

2. 選取 [儲存]****。

## <a name="select-logins"></a>選取登入

1. 在 [選取登入]**** 畫面上，選取您要移轉的登入。

    >[!NOTE]
    >預設情況下，Azure 資料庫移轉服務僅支援遷移 SQL 登錄。 要啟用對遷移 Windows 登錄的支援，請參閱本教程的 **"先決條件"** 部分。

    ![選取登入](media/tutorial-sql-server-to-managed-instance/select-logins.png)

2. 選取 [儲存]****。

## <a name="configure-migration-settings"></a>設定移轉設定

1. 在 [設定移轉設定]**** 畫面上，提供下列詳細資料：

    | | |
    |--------|---------|
    |**選擇來源備份選項** | 當您已有可供 DMS 用於資料庫移轉的完整備份檔案時，請選擇 [我會提供最新的備份檔案]**** 選項。 如果您想要讓 DMS 先建立來源資料庫完整備份，並將其用於移轉，請選擇 [我允許 Azure 資料庫移轉服務建立備份檔案]**** 選項。 |
    |**網路位置共用** | Azure 資料庫移轉服務可將來源資料庫備份移入的本機 SMB 網路共用。 執行來源 SQL Server 執行個體的服務帳戶在此網路共用上必須具有寫入權限。 在網路共用中提供伺服器的 FQDN 或 IP 位址，例如，'\\\servername.domainname.com\backupfolder' 或 '\\\IP address\backupfolder'。|
    |**使用者名稱** | 請確定 Windows 使用者對於您先前提供的網路共用具有完整控制權限。 Azure 資料庫移轉服務將類比使用者憑據，將備份檔案上載到 Azure 存儲容器以進行還原操作。 如果選取已啟用 TDE 的資料庫進行移轉，則上述的 Windows 使用者必須是內建的系統管理員帳戶，且必須停用 Azure 資料庫移轉服務的[使用者帳戶控制](https://docs.microsoft.com/windows/security/identity-protection/user-account-control/user-account-control-overview)，才能上傳及刪除憑證檔案。 |
    |**密碼** | 使用者的密碼。 |
    |**儲存體帳戶設定** | 提供 Azure 資料庫移轉服務的 SAS URI 可訪問存儲帳戶容器，該服務將備份檔案上載到該容器，用於將資料庫移轉到 Azure SQL 資料庫託管實例。 [瞭解如何獲取 Blob 容器的 SAS URI。](https://docs.microsoft.com/azure/vs-azure-tools-storage-explorer-blobs#get-the-sas-for-a-blob-container)|
    |**TDE 設定** | 如果您要移轉已啟用透明資料加密 (TDE) 的來源資料庫，您必須具備目標 Azure SQL Database 受控執行個體的寫入權限。  請從下拉式功能表中選取用來佈建 Azure SQL DB 受控執行個體的訂用帳戶。  在下拉式功能表中選取目標 **Azure SQL 資料庫受控執行個體**。 |

    ![設定移轉設定](media/tutorial-sql-server-to-managed-instance/dms-configure-migration-settings3.png)

2. 選取 [儲存]****。

## <a name="review-the-migration-summary"></a>檢閱移轉摘要

1. 在 [移轉摘要]**** 畫面上的 [活動名稱]**** 文字方塊中，指定移轉活動的名稱。

2. 展開 [驗證選項]**** 區段以顯示 [選擇驗證選項]**** 畫面，指定是否驗證已遷移資料庫的查詢正確性，然後選取 [儲存]****。

3. 檢閱並確認與移轉專案相關聯的詳細資料。

    ![移轉專案摘要](media/tutorial-sql-server-to-managed-instance/dms-project-summary2.png)

4. 選取 [儲存]****。

## <a name="run-the-migration"></a>執行移轉

- 選取 [執行移轉]****。

  [移轉活動] 視窗隨即出現，而且活動的狀態為 [擱置]****。

## <a name="monitor-the-migration"></a>監視移轉

1. 在移轉活動畫面中，選取 [重新整理]**** 以更新顯示。

   ![移轉活動進行中](media/tutorial-sql-server-to-managed-instance/dms-monitor-migration1.png)

    您可以進一步展開資料庫和登入類別，以監視個別伺服器物件的移轉狀態。

   ![移轉活動進行中](media/tutorial-sql-server-to-managed-instance/dms-monitor-migration-extend.png)

2. 移轉完成之後，請選取 [下載報告]**** 以取得報告，其中會列出與移轉程序相關聯的詳細資料。

3. 確認目標 Azure SQL Database 受控執行個體環境上的目標資料庫。

## <a name="next-steps"></a>後續步驟

- 有關如何使用 T-SQL RESTORE 命令將資料庫移轉到託管實例的教程，請參閱[使用 還原命令將備份還原到託管實例](../sql-database/sql-database-managed-instance-restore-from-backup-tutorial.md)。
- 有關託管實例的資訊，請參閱[什麼是託管實例](../sql-database/sql-database-managed-instance.md)。
- 有關將應用連接到託管實例的資訊，請參閱[連接應用程式](../sql-database/sql-database-managed-instance-connect-app.md)。
