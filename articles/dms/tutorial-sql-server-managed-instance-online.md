---
title: 教學課程：將 SQL Server 線上移轉至 SQL 受控執行個體
titleSuffix: Azure Database Migration Service
description: 了解如何使用 Azure 資料庫移轉服務，執行從 SQL Server 至 Azure SQL 受控執行個體的線上移轉。
services: dms
author: pochiraju
ms.author: rajpo
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019
ms.topic: tutorial
ms.date: 08/04/2020
ms.openlocfilehash: 02adb6f47b907fea402f8b312b3f4e8e117927ed
ms.sourcegitcommit: 77afc94755db65a3ec107640069067172f55da67
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/22/2021
ms.locfileid: "98693675"
---
# <a name="tutorial-migrate-sql-server-to-an-azure-sql-managed-instance-online-using-dms"></a>教學課程：使用 DMS 將 SQL Server 線上移轉至 Azure SQL 受控執行個體

您可以使用 Azure 資料庫移轉服務，將資料庫從 SQL Server 執行個體移轉至 [Azure SQL 受控執行個體](../azure-sql/managed-instance/sql-managed-instance-paas-overview.md)，且停機時間最短。 如需其他可能需要手動操作的方法，請參閱[將 SQL Server 執行個體移轉至 Azure SQL 受控執行個體](../azure-sql/managed-instance/migrate-to-instance-from-sql-server.md)一文。

在本教學課程中，您要使用 Azure 資料庫移轉服務，將 **Adventureworks2012** 資料庫從 SQL Server 的內部部署執行個體移轉至 SQL 受控執行個體，且停機時間最短。

在本教學課程中，您會了解如何：
> [!div class="checklist"]
>
> * 建立 Azure 資料庫移轉服務的執行個體。
> * 使用 Azure 資料庫移轉服務來建立移轉專案並啟動線上移轉。
> * 監視移轉。
> * 在您準備好時執行完全移轉。

> [!IMPORTANT]
> 若要使用 Azure 資料庫移轉服務從 SQL Server 線上移轉至 SQL 受控執行個體，您必須在可供此服務用來移轉資料庫的 SMB 網路共用中，提供完整的資料庫備份及後續的記錄備份。 在移轉時，Azure 資料庫移轉服務不會起始任何備份，而是會使用現有備份 (您可能已在災害復原方案中備妥)。
> 請務必[使用 WITH CHECKSUM 選項來製作備份](/sql/relational-databases/backup-restore/enable-or-disable-backup-checksums-during-backup-or-restore-sql-server?preserve-view=true&view=sql-server-2017)。 此外，請務必不要將多個備份 (也就是完整和交易記錄) 附加到單一備份媒體中；請在不同的備份檔案中製作每個備份。 最後，您可以使用壓縮的備份，以減少遷移大型備份時可能發生的潛在問題。

> [!NOTE]
> 若要使用「Azure 資料庫移轉服務」來執行線上移轉，必須根據「進階」定價層建立執行個體。

> [!IMPORTANT]
> 為了獲得最佳的移轉體驗，Microsoft 建議在目標資料庫所在的同一個 Azure 區域中，建立 Azure 資料庫移轉服務的執行個體。 跨區域或地理位置移動資料可能使移轉程序變慢，並產生錯誤。

> [!IMPORTANT]
> 盡可能縮短線上移轉程序的持續時間，以將執行個體重新設定或計劃性維護所造成的中斷風險降到最低。 如果發生這類事件，則移轉程序會從頭開始。 如果是計劃性維護，則會有 36 小時的寬限期，之後才重新開始移轉程序。

[!INCLUDE [online-offline](../../includes/database-migration-service-offline-online.md)]

本文說明如何從 SQL Server 線上移轉至 SQL 受控執行個體。 若要進行離線移轉，請參閱[使用 DMS 在將 SQL Server 離線移轉至 SQL 受控執行個體](tutorial-sql-server-to-managed-instance.md)。

## <a name="prerequisites"></a>Prerequisites

若要完成本教學課程，您需要：

* 使用 Azure Resource Manager 部署模型建立 Azure 資料庫移轉服務的 Microsoft Azure 虛擬網路，以使用 [ExpressRoute](../expressroute/expressroute-introduction.md) 或 [VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md) 為您的內部部署來源伺服器提供站對站連線能力。 [了解使用 Azure 資料庫移轉服務進行 SQL 受控執行個體移轉的網路拓撲](./resource-network-topologies.md)。 如需建立虛擬網路的詳細資訊，請參閱[虛擬網路文件](../virtual-network/index.yml)，特別是快速入門文章，裡面會提供逐步操作詳細資料。

    > [!NOTE]
    > 在虛擬網路設定期間，如果您使用 ExpressRoute 搭配與 Microsoft 對等互連的網路，請將下列服務[端點](../virtual-network/virtual-network-service-endpoints-overview.md)新增至將佈建服務的子網路：
    >
    > * 目標資料庫端點 (例如，SQL 端點、Cosmos DB 端點等)
    > * 儲存體端點
    > * 服務匯流排端點
    >
    > 此為必要設定，因為 Azure 資料庫移轉服務沒有網際網路連線。
    >
    >如果您沒有內部部署網路與 Azure 之間的站對站連線，或是您的站對站連線頻寬有限，請考慮在混合模式 (預覽) 中使用 Azure 資料庫移轉服務。 混合模式會搭配使用內部部署移轉背景工作角色與雲端中執行的 Azure 資料庫移轉服務執行個體。 若要在混合模式中建立 Azure 資料庫移轉服務的執行個體，請參閱[使用 Azure 入口網站在混合模式中建立 Azure 資料庫移轉服務執行個體](./quickstart-create-data-migration-service-portal.md)一文。

    > [!IMPORTANT]
    > 關於移轉過程中使用的儲存體帳戶，您必須執行下列其中一個動作：
    > * 選擇允許所有網路存取儲存體帳戶。
    > * 開啟 MI 子網路上的 [子網路委派](../virtual-network/manage-subnet-delegation.md)，並更新儲存體帳戶防火牆規則以允許此子網路。

* 確定您虛擬網路的網路安全性群組規則不會對 Azure 資料庫移轉服務封鎖下列輸出通訊連接埠：443、53、9354、445、12000。 如需虛擬網路 NSG 流量篩選的詳細資訊，請參閱[使用網路安全性群組來篩選網路流量](../virtual-network/virtual-network-vnet-plan-design-arm.md)。
* 設定[用於來源資料庫引擎存取的 Windows 防火牆](/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access)。
* 開啟您的 Windows 防火牆以允許 Azure 資料庫移轉服務存取來源 SQL Server (依預設會使用 TCP 連接埠 1433)。 如果您的預設執行個體正在其他連接埠上接聽，請將其新增至防火牆。
* 如果您使用動態連接埠執行多個具名 SQL Server 執行個體，您可以啟用 SQL Browser 服務並允許通過防火牆存取 UDP 連接埠 1434，讓 Azure 資料庫移轉服務連線來源伺服器上的具名執行個體。
* 如果您是在來源資料庫前面使用防火牆設備，您可能必須新增防火牆規則，才能讓 Azure 資料庫移轉服務存取用於移轉的來源資料庫，以及透過 SMB 連接埠 445 存取檔案。
* 依照[在 Azure 入口網站中建立 Azure SQL 受控執行個體](../azure-sql/managed-instance/instance-create-quickstart.md)一文中的詳細資料，建立 SQL 受控執行個體。
* 確定用來連線來源 SQL Server 和目標 SQL 受控執行個體的登入是 sysadmin 伺服器角色的成員。
* 提供 SMB 網路共用，其中包含您所有資料庫的完整資料庫備份檔案及後續交易記錄備份檔案，而 Azure 資料庫移轉服務會使用這些檔案來進行資料庫移轉。
* 確認執行來源 SQL Server 執行個體的服務帳戶在您所建立的網路共用上具有寫入權限，而且來源伺服器的電腦帳戶對相同的共用具備讀取/寫入存取權。
* 記下在您先前建立的網路共用上具有完整控制權限的 Windows 使用者 (和密碼)。 Azure 資料庫移轉服務會模擬該使用者認證，以便將備份檔案上傳至 Azure 儲存體容器以進行還原作業。
* 建立 Azure Active Directory 應用程式識別碼，以產生應用程式識別碼金鑰，讓 Azure 資料庫移轉服務可用它來連線至目標 Azure Database 受控執行個體和 Azure 儲存體容器。 如需詳細資訊，請參閱[使用入口網站來建立可存取資源的 Active Directory 應用程式和服務主體](../active-directory/develop/howto-create-service-principal-portal.md)一文。

  > [!NOTE]
  > Azure 資料庫移轉服務需要訂用帳戶的「參與者」權限，以取得指定的應用程式識別碼。 或者，您可以建立自訂角色，以授與 Azure 資料庫移轉服務所需的特定權限。 如需使用自訂角色的逐步指引，請參閱[自訂 SQL Server 角色以進行 SQL 受控執行個體的線上移轉](./resource-custom-roles-sql-db-managed-instance.md)一文。

* 建立或記下 Azure 儲存體帳戶的 **標準效能層**，這可讓 DMS 服務將資料庫備份檔案上傳至該處，並用於遷移資料庫。  請務必在建立 Azure 資料庫移轉服務執行個體的相同區域中建立 Azure 儲存體帳戶。

  > [!NOTE]
  > 使用線上移轉將受到[透明資料加密](../azure-sql/database/transparent-data-encryption-tde-overview.md)的資料庫移轉至受控執行個體時，必須先移轉內部部署或 Azure VM SQL Server 執行個體的對應憑證，才能還原資料庫。 如需詳細步驟，請參閱[將 TDE 憑證移轉至受控執行個體](../azure-sql/database/transparent-data-encryption-tde-overview.md)。

## <a name="register-the-microsoftdatamigration-resource-provider"></a>註冊 Microsoft.DataMigration 資源提供者

1. 登入 Azure 入口網站，選取 [所有服務]，然後選取 [訂用帳戶]。

    ![顯示入口網站訂用帳戶](media/tutorial-sql-server-to-managed-instance-online/portal-select-subscriptions.png)

2. 選取您要在其中建立 Azure 資料庫移轉服務執行個體的訂用帳戶，然後選取 [資源提供者]。

    ![顯示資源提供者](media/tutorial-sql-server-to-managed-instance-online/portal-select-resource-provider.png)

3. 搜尋移轉，然後在 [Microsoft.DataMigration] 的右邊，選取 [註冊]。

    ![註冊資源提供者](media/tutorial-sql-server-to-managed-instance-online/portal-register-resource-provider.png)

## <a name="create-an-azure-database-migration-service-instance"></a>建立 Azure 資料庫移轉服務執行個體

1. 在 Azure 入口網站中，選取 [+ 建立資源]，搜尋 [Azure 資料庫移轉服務]，然後從下拉式清單選取 [Azure 資料庫移轉服務]。

     ![Azure Marketplace](media/tutorial-sql-server-to-managed-instance-online/portal-marketplace.png)

2. 在 [Azure 資料庫移轉服務] 畫面上，選取 [建立]。

    ![建立 Azure 資料庫移轉服務執行個體](media/tutorial-sql-server-to-managed-instance-online/dms-create1.png)

3. 在 [建立移轉服務] 畫面上，指定服務的名稱、訂用帳戶，以及新的或現有的資源群組。

4. 選取您要建立 DMS 執行個體的位置。

5. 選取現有的虛擬網路或建立一個虛擬網路。

    虛擬網路會為 Azure 資料庫移轉服務提供來源 SQL Server 和目標 SQL 受控執行個體的存取權。

    如需如何在 Azure 入口網站中建立虛擬網路的詳細資訊，請參閱[使用 Azure 入口網站建立虛擬網路](../virtual-network/quick-create-portal.md)一文。

    如需其他詳細資訊，請參閱[了解使用 Azure 資料庫移轉服務進行 SQL 受控執行個體移轉的網路拓撲](./resource-network-topologies.md)一文。

6. 從「進階」定價層選取 SKU。

    > [!NOTE]
    > 只有在使用「進階」層的情況下，才支援線上移轉。

    如需成本和定價層的詳細資訊，請參閱[定價分頁](https://aka.ms/dms-pricing)。

    ![建立 DMS 服務](media/tutorial-sql-server-to-managed-instance-online/dms-create-service3.png)

7. 選取 [建立] 以建立服務。

## <a name="create-a-migration-project"></a>建立移轉專案

建立服務執行個體之後，請在 Azure 入口網站中找出該服務，然後建立新的移轉專案。

1. 在 Azure 入口網站中，選取 [所有服務]，搜尋 Azure 資料庫移轉服務，然後選取 [Azure 資料庫移轉服務]。

    ![找出 Azure 資料庫移轉服務的所有執行個體](media/tutorial-sql-server-to-managed-instance-online/dms-search.png)

2. 在 [Azure 資料庫移轉服務] 畫面上，搜尋您建立的執行個體名稱，然後選取該執行個體。

3. 選取 [+ 新增移轉專案]。

4. 在 [新增移轉專案] 畫面上指定專案名稱、在 [來源伺服器類型] 文字方塊中選取 [SQL Server]、在 [目標伺服器類型] 文字方塊中選取 [Azure SQL 受控執行個體]，然後針對 [選擇活動類型]，選取 [連線資料移轉]。

   ![建立 Azure 資料庫移轉服務專案](media/tutorial-sql-server-to-managed-instance-online/dms-create-project3.png)

5. 選取 [建立及執行活動]，以建立專案。

## <a name="specify-source-details"></a>指定來源詳細資料

1. 在 [移轉來源詳細資料] 畫面上，指定來源 SQL Server 的連線詳細資料。

2. 如果您尚未在伺服器上安裝信任的憑證，請選取 [信任伺服器憑證] 核取方塊。

    未安裝信任的憑證時，SQL Server 會在執行個體啟動時產生自我簽署憑證。 此憑證用來加密用戶端連線的認證。

    > [!CAUTION]
    > 使用自我簽署憑證加密的 TLS 連線不會提供增強式安全性。 這種連線容易受到攔截式攻擊。 在生產環境或連線到網際網路的伺服器上，您不應該仰賴使用自我簽署憑證的 TLS。

   ![來源詳細資料](media/tutorial-sql-server-to-managed-instance-online/dms-source-details2.png)

3. 選取 [儲存]。

4. 在 [選取來源資料庫] 畫面上，選取 [Adventureworks2012] 資料庫進行移轉。

   ![選取來源資料庫](media/tutorial-sql-server-to-managed-instance-online/dms-source-database1.png)

    > [!IMPORTANT]
    > 如果您使用 SQL Server Integration Services (SSIS)，DMS 目前不支援將 SSIS 專案/套件 (SSISDB) 的目錄資料庫從 SQL Server 移轉至 SQL 受控執行個體。 不過，您可以在 Azure Data Factory (ADF) 中佈建 SSIS，並將 SSIS 專案/套件重新部署到 SQL 受控執行個體所裝載的目的地 SSISDB。 如需有關遷移 SSIS 套件的詳細資訊，請參閱[將 SQL Server Integration Services 套件遷移到 Azure](./how-to-migrate-ssis-packages.md) 一文。

5. 選取 [儲存]。

## <a name="specify-target-details"></a>指定目標詳細資料

1. 在 [移轉目標詳細資料] 畫面上，指定 [應用程式識別碼] 和 [金鑰]，DMS 執行個體可以使用這些資料來連線到 SQL 受控執行個體的目標執行個體及 Azure 儲存體帳戶。

    如需詳細資訊，請參閱[使用入口網站來建立可存取資源的 Active Directory 應用程式和服務主體](../active-directory/develop/howto-create-service-principal-portal.md)一文。

2. 選取 [訂用帳戶] (其中包含 SQL 受控執行個體的目標執行個體)，然後選取目標執行個體。

    如果您尚未佈建 SQL 受控執行個體，請選取[連結](../azure-sql/managed-instance/instance-create-quickstart.md)來協助您佈建執行個體。 當 SQL 受控執行個體準備就緒時，返回此特定專案來執行移轉。

3. 提供 [SQL 使用者] 和 [密碼]，以連線到 SQL 受控執行個體。

    ![選取目標](media/tutorial-sql-server-to-managed-instance-online/dms-target-details3.png)

4. 選取 [儲存]。

## <a name="select-source-databases"></a>選取來源資料庫

1. 在 [選取來源資料庫] 畫面上，選取您要遷移的來源資料庫。

    ![選取來源資料庫](media/tutorial-sql-server-to-managed-instance-online/dms-select-source-databases2.png)

2. 選取 [儲存]。

## <a name="configure-migration-settings"></a>設定移轉設定

1. 在 [設定移轉設定] 畫面上，提供下列詳細資料：

    | | |
    |--------|---------|
    |**SMB 網路位置共用** | 本機 SMB 網路共用或 Azure 檔案共用包含完整資料庫備份檔案及交易記錄備份檔案，Azure 資料庫移轉服務可使用這些檔案來進行移轉。 執行來源 SQL Server 執行個體的服務帳戶在此網路共用上必須具有讀取\寫入權限。 在網路共用中提供伺服器的 FQDN 或 IP 位址，例如，'\\\servername.domainname.com\backupfolder' 或 '\\\IP address\backupfolder'。 為了改善效能，建議您針對要移轉的每個資料庫使用不同的資料夾。 您可以使用 [進階設定] 選項來提供資料庫層級的檔案共用路徑。 如果您在連線到 SMB 共用時遇到問題，請參閱 [smb 共用](known-issues-azure-sql-db-managed-instance-online.md#smb-file-share-connectivity)。 |
    |**使用者名稱** | 請確定 Windows 使用者對於您先前提供的網路共用具有完整控制權限。 Azure 資料庫移轉服務將會模擬該使用者認證，以便將備份檔案上傳至 Azure 儲存體容器以進行還原作業。 如果使用 Azure 檔案共用，請使用在前面加上 AZURE\ 的儲存體帳戶名稱，作為使用者名稱。 |
    |**密碼** | 使用者的密碼。 如果使用 Azure 檔案共用，請使用儲存體帳戶金鑰作為密碼。 |
    |**Azure 儲存體帳戶的訂用帳戶** | 選取包含 Azure 儲存體帳戶的訂用帳戶。 |
    |**Azure 儲存體帳戶** | 選取 Azure 儲存體帳戶，讓 DMS 可以將備份檔案從 SMB 網路共用上傳至該處，並用於資料庫移轉。  建議您選取與 DMS 服務位在相同區域的儲存體帳戶，以達到最佳檔案上傳效能。 |

    ![設定移轉設定](media/tutorial-sql-server-to-managed-instance-online/dms-configure-migration-settings4.png)

    > [!NOTE]
    > 如果 Azure 資料庫移轉服務顯示「系統錯誤 53」或「系統錯誤 57」錯誤，其原因可能是 Azure 資料庫移轉服務無法存取 Azure 檔案共用。 如果發生上述其中一個錯誤，請使用[這裡](../storage/common/storage-network-security.md?toc=%2fazure%2fvirtual-network%2ftoc.json#grant-access-from-a-virtual-network)的指示，授與虛擬網路對儲存體帳戶的存取權。

    > [!IMPORTANT]
    > 如果已啟用回送檢查功能，而且來源 SQL Server 和檔案共用位於相同的電腦上，則來源將無法使用 FQDN 存取檔案共用。 若要修正此問題，請使用[此處](https://support.microsoft.com/help/926642/error-message-when-you-try-to-access-a-server-locally-by-using-its-fqd)的指示來停用回送檢查功能。

2. 選取 [儲存]。

## <a name="review-the-migration-summary"></a>檢閱移轉摘要

1. 在 [移轉摘要] 畫面上的 [活動名稱] 文字方塊中，指定移轉活動的名稱。

2. 檢閱並確認與移轉專案相關聯的詳細資料。

    ![移轉專案摘要](media/tutorial-sql-server-to-managed-instance-online/dms-project-summary3.png)

## <a name="run-and-monitor-the-migration"></a>執行並監視移轉

1. 選取 [執行移轉]。

2. 在移轉活動畫面上，選取 [重新整理] 以更新顯示。

   ![移轉活動進行中](media/tutorial-sql-server-to-managed-instance-online/dms-monitor-migration2.png)

    您可以進一步展開資料庫和登入類別，以監視個別伺服器物件的移轉狀態。

   ![移轉活動狀態](media/tutorial-sql-server-to-managed-instance-online/dms-monitor-migration-extend2.png)

## <a name="performing-migration-cutover"></a>執行完全移轉

當完整資料庫備份在 SQL 受控執行個體的目標執行個體上還原後，此資料庫就可用來執行完全移轉。

1. 當您準備好要完成線上資料庫移轉後，請選取 [開始完全移轉]。

2. 停止對來源資料庫傳入任何流量。

3. 進行 [結尾記錄備份]、在 SMB 網路共用中提供備份檔案，然後等到最後一個交易記錄備份還原完成。

    此時，您會看到 [暫止的變更] 已設為 0。

4. 選取 [確認]，然後選取 [套用]。

    ![準備完成完全移轉](media/tutorial-sql-server-to-managed-instance-online/dms-complete-cutover.png)

    > [!IMPORTANT]
    > 完全移轉之後，具有業務關鍵服務層 SQL 受控執行個體的可用性，只會比一般用途明顯來得長，因為必須為 AlwaysOn 高可用性群組植入三個次要複本。 此作業持續時間取決於資料大小，如需詳細資訊，請參閱[管理作業持續時間](../azure-sql/managed-instance/management-operations-overview.md#duration)。

5. 當資料庫移轉狀態顯示為 [已完成] 後，請將應用程式連線至 SQL 受控執行個體的新目標執行個體。

    ![完全移轉完成](media/tutorial-sql-server-to-managed-instance-online/dms-cutover-complete.png)

## <a name="next-steps"></a>後續步驟

* 如需如何使用 T-SQL 還原命令將資料庫移轉至 SQL 受控執行個體的教學課程，請參閱[使用還原命令將備份還原到 SQL 受控執行個體](../azure-sql/managed-instance/restore-sample-database-quickstart.md)。
* 如需 SQL 受控執行個體的詳細資訊，請參閱[什麼是 SQL 受控執行個體](../azure-sql/managed-instance/sql-managed-instance-paas-overview.md)。
* 如需將應用程式連線至 SQL 受控執行個體的相關資訊，請參閱[應用程式連線](../azure-sql/managed-instance/connect-application-instance.md)。