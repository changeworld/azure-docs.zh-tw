---
title: 在 Azure Data Factory 中建立 Azure-SSIS 整合執行階段
description: 了解如何在 Azure Data Factory 中建立 Azure-SSIS 整合執行階段，您就可以在 Azure 部署並執行 SSIS 套件。
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 08/11/2020
author: swinarko
ms.author: sawinark
ms.reviewer: douglasl
manager: mflasko
ms.openlocfilehash: 07cfb0048e6027b0bac219b3fe28018db2d10257
ms.sourcegitcommit: faeabfc2fffc33be7de6e1e93271ae214099517f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/13/2020
ms.locfileid: "88185259"
---
# <a name="create-an-azure-ssis-integration-runtime-in-azure-data-factory"></a>在 Azure Data Factory 中建立 Azure-SSIS 整合執行階段

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

本文提供在) Azure Data Factory ADF (中，布建 Azure SQL Server Integration Services (SSIS) Integration runtime (IR) 的步驟。 Azure-SSIS IR 可支援：

- 執行已部署到 SSIS 目錄 (SSISDB) 的套件，此目錄由 Azure SQL Database 伺服器/受控執行個體 (專案部署模型) 裝載
- 執行已部署到 Azure SQL 受控執行個體 (套件部署模型) 所裝載檔案系統、Azure 檔案儲存體或 SQL Server 資料庫 (MSDB) 中的套件

佈建 Azure-SSIS IR 之後，您就可以使用熟悉的工具在 Azure 中部署和執行套件。 這些工具已針對 Azure 啟用，且包括 SQL Server Data Tools (SSDT)、SQL Server Management Studio (SSMS) 與命令列公用程式，例如 [dtutil](https://docs.microsoft.com/sql/integration-services/dtutil-utility?view=sql-server-2017) 和 [AzureDTExec](https://docs.microsoft.com/azure/data-factory/how-to-invoke-ssis-package-azure-enabled-dtexec)。

布建[AZURE SSIS ir](tutorial-create-azure-ssis-runtime-portal.md)教學課程會示範如何透過 Azure 入口網站或 Data Factory 應用程式來建立 AZURE ssis ir。 本教學課程也會說明如何選擇性地使用 Azure SQL Database 伺服器或受控實例來裝載 SSISDB。 本文會針對教學課程進行擴充，並說明如何執行這些選擇性工作：

- 使用具有 IP 防火牆規則/虛擬網路服務端點的 Azure SQL Database 伺服器，或具有私人端點的受控實例來裝載 SSISDB。 必要條件是您必須為您的 Azure SSIS IR 設定虛擬網路許可權和設定，以加入虛擬網路。

- 使用 Azure Active Directory (Azure AD 與您的資料處理站的受控識別進行) 驗證，以連線至 Azure SQL Database 伺服器或受控實例。 必要條件是，您必須將 data factory 的受控識別新增為可建立 SSISDB 實例的資料庫使用者。

- 將您的 Azure SSIS IR 加入虛擬網路，或設定自我裝載 IR 作為 Azure SSIS IR 的 proxy，以存取內部部署資料。

本文說明如何使用 Azure 入口網站、Azure PowerShell 和 Azure Resource Manager 範本來布建 Azure SSIS IR。

## <a name="prerequisites"></a>必要條件

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

- **Azure 訂用帳戶**。 如果您還沒有訂用帳戶，您可以建立[免費試用](https://azure.microsoft.com/pricing/free-trial/)帳戶。

- **Azure SQL Database server 或 SQL 受控執行個體 (選擇性的) **。 如果您還沒有資料庫伺服器或受控實例，請在 [Azure 入口網站] 中建立一個，然後再開始進行。 Data Factory 接著會在此資料庫伺服器上建立 SSISDB 執行個體。 

  我們建議您在整合執行時間所在的相同 Azure 區域中，建立資料庫伺服器或受控實例。 此設定可讓整合執行階段將執行記錄寫入 SSISDB，而不需要跨 Azure 區域。

  請記住下列重點：

  - SSISDB 實例可以代表您建立為單一資料庫、彈性集區的一部分，或在受控實例中建立。 該執行個體可以在公用網路中供您存取，而您也可以藉由加入虛擬網路來加以存取。 如需選擇 SQL Database 和 SQL 受控執行個體以裝載 SSISDB 的指引，請參閱本文中的[比較 SQL Database 和 sql 受控執行個體](#comparison-of-sql-database-and-sql-managed-instance)一節。 
  
    如果您使用具有 IP 防火牆規則/虛擬網路服務端點的 Azure SQL Database 伺服器，或具有私人端點的 SQL 受控實例來裝載 SSISDB，或如果您需要存取內部部署資料而不設定自我裝載的 IR，則需要將您的 Azure SSIS IR 加入虛擬網路。 如需詳細資訊，請參閱將[AZURE SSIS IR 加入虛擬網路](https://docs.microsoft.com/azure/data-factory/join-azure-ssis-integration-runtime-virtual-network)。

  - 確認資料庫伺服器的 [允許存取 Azure 服務] 設定已啟用。 當您使用具有 IP 防火牆規則/虛擬網路服務端點的 Azure SQL Database 伺服器，或具有私人端點的 SQL 受控實例來裝載 SSISDB 時，不適用此設定。 如需詳細資訊，請參閱[保護 Azure SQL Database](../sql-database/sql-database-security-tutorial.md#create-firewall-rules)。 若要使用 PowerShell 來啟用此設定，請參閱 [New-AzSqlServerFirewallRule](/powershell/module/az.sql/new-azsqlserverfirewallrule)。

  - 新增用戶端電腦的 IP 位址，或新增 IP 位址範圍，其中包含資料庫伺服器之防火牆設定中用戶端電腦 IP 位址到用戶端 IP 位址清單。 如需詳細資訊，請參閱 [Azure SQL Database 伺服器層級和資料庫層級防火牆規則](../sql-database/sql-database-firewall-configure.md)

  - 若要連線至資料庫伺服器，您可以使用 SQL 驗證搭配伺服器管理員認證，也可以使用 Azure AD 驗證搭配資料處理站的受控識別。 若為後者，您必須將資料處理站的受控識別新增至具有資料庫伺服器存取權限的 Azure AD 群組中。 如需詳細資訊，請參閱[啟用 AZURE SSIS IR 的 Azure AD 驗證](https://docs.microsoft.com/azure/data-factory/enable-aad-authentication-azure-ssis-ir)。

  - 請確認您的資料庫伺服器尚未有 SSISDB 執行個體。 Azure-SSIS IR 的佈建不支援使用現有的 SSISDB 執行個體。

- **Azure Resource Manager 虛擬網路 (選擇性)**。 如果下列至少一個條件成立，您就必須具備 Azure Resource Manager 虛擬網路：

  - 您在具有 IP 防火牆規則/虛擬網路服務端點或具有私人端點的受控實例的 Azure SQL Database 伺服器上裝載 SSISDB。

  - 您想要從 Azure SSIS IR 上執行的 SSIS 套件連接到內部部署資料存放區，而不需設定自我裝載 IR。

- **Azure PowerShell (選擇性) **。 如果您想要執行 PowerShell 指令碼來佈建 Azure-SSIS IR，請遵循[如何安裝和設定 Azure PowerShell](/powershell/azure/install-az-ps) 中的指示。

### <a name="regional-support"></a>區域支援

如需可使用 Data Factory 和 Azure SSIS IR 的 Azure 區域清單，請參閱[依區域的 Data Factory 和 SSIS ir 可用性](https://azure.microsoft.com/global-infrastructure/services/?products=data-factory&regions=all)。

### <a name="comparison-of-sql-database-and-sql-managed-instance"></a>SQL Database 和 SQL 受控執行個體的比較

下表比較 Azure SQL Database server 和 SQL 受控執行個體的特定功能，因為它們與 SSIR IR 相關：

| 功能 | SQL Database| SQL 受控實例 |
|---------|--------------|------------------|
| **排程** | SQL Server Agent 無法使用。<br/><br/>請參閱[在 Data Factory 管線中排程封裝執行](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-schedule-packages?view=sql-server-2017#activity)。| 受控執行個體代理程式可供使用。 |
| **驗證** | 您可以使用自主資料庫使用者來建立 SSISDB 實例，其代表任何 Azure AD 群組，並以您的 data factory 受控識別作為**db_owner**角色中的成員。<br/><br/>請參閱[啟用 Azure AD 驗證，以在 Azure SQL Database 伺服器中建立 SSISDB](enable-aad-authentication-azure-ssis-ir.md#enable-azure-ad-on-azure-sql-database)。 | 您可以使用自主資料庫使用者來建立 SSISDB 實例，以代表您的 data factory 受控識別。 <br/><br/>請參閱[啟用 Azure AD 驗證以在 AZURE SQL 受控執行個體中建立 SSISDB](enable-aad-authentication-azure-ssis-ir.md#enable-azure-ad-on-sql-managed-instance)。 |
| **服務層級** | 當您使用 Azure SQL Database 伺服器建立 Azure SSIS IR 時，可以選取 SSISDB 的服務層級。 目前有多個服務層級。 | 當您使用受控實例建立 Azure SSIS IR 時，您無法選取 SSISDB 的服務層級。 受控實例中的所有資料庫會共用配置給該實例的相同資源。 |
| **虛擬網路** | 如果您使用具有 IP 防火牆規則/虛擬網路服務端點的 Azure SQL Database 伺服器，您的 Azure SSIS IR 可以加入 Azure Resource Manager 的虛擬網路。 | 如果您搭配使用受控實例與私人端點，則您的 Azure SSIS IR 可以加入 Azure Resource Manager 的虛擬網路。 當您未啟用受控實例的公用端點時，需要虛擬網路。<br/><br/>如果您將 Azure SSIS IR 加入與受控實例相同的虛擬網路，請確定您的 Azure SSIS IR 與受控實例位於不同的子網中。 如果您將 Azure SSIS IR 加入受控實例中的不同虛擬網路，建議使用虛擬網路對等互連或網路對網路連線。 請參閱[將您的應用程式連線至 Azure SQL Database 受控執行個體](../sql-database/sql-database-managed-instance-connect-app.md)。 |
| **分散式交易** | 透過彈性交易可支援這項功能。 不支援 Microsoft Distributed Transaction Coordinator (MSDTC) 交易。 如果您的 SSIS 套件使用 MSDTC 來協調分散式交易，請考慮遷移至彈性交易以進行 Azure SQL Database。 如需詳細資訊，請參閱[跨雲端資料庫的分散式交易](../sql-database/sql-database-elastic-transactions-overview.md)。 | 不支援。 |
| | | |

## <a name="use-the-azure-portal-to-create-an-integration-runtime"></a>使用 Azure 入口網站建立整合執行時間

在本節中，您會使用 Azure 入口網站，特別是 Data Factory 的使用者介面 (UI) 或應用程式來建立 Azure SSIS IR。

### <a name="create-a-data-factory"></a>建立 Data Factory

若要透過 Azure 入口網站來建立資料處理站，請遵循[透過 UI 來建立資料處理站](https://docs.microsoft.com/azure/data-factory/quickstart-create-data-factory-portal#create-a-data-factory)中的逐步指示。 遵循指示操作時請選取 [釘選到儀表板]，以便能快速存取建立好的資料處理站。 

資料處理站建立好之後，請在 Azure 入口網站中開啟其 [概觀] 頁面。 選取 [**作者 & 監視器**] 圖格，以在另一個索引標籤上開啟其「**我們的**「開始使用」頁面。在這裡，您可以繼續建立您的 Azure SSIS IR。   

### <a name="provision-an-azure-ssis-integration-runtime"></a>佈建 Azure-SSIS 整合執行階段

在 [**現在就開始**吧] 頁面上，選取 [**設定 SSIS Integration Runtime** ] 圖格，以開啟 [ **Integration Runtime 設定**] 窗格。

   ![設定 SSIS 整合執行階段圖格](./media/tutorial-create-azure-ssis-runtime-portal/configure-ssis-integration-runtime-tile.png)

   [整合執行階段設定] 窗格有三個頁面，您可以在其中依次設定一般、部署與進階設定。

#### <a name="general-settings-page"></a>一般設定頁面

在 [整合執行階段設定] 窗格的 [一般設定] 頁面上，完成下列步驟。

   ![一般設定](./media/tutorial-create-azure-ssis-runtime-portal/general-settings.png)

   1. 針對 [名稱]，輸入取您整合執行階段的名稱。

   1. 針對 [描述]，輸入整合執行階段的描述。

   1. 針對 [位置]，選取整合執行階段的位置。 系統只會顯示支援的位置。 我們建議您選取裝載 SSISDB 的資料庫伺服器所在位置。

   1. 針對 [**節點大小**]，選取整合執行時間叢集中的節點大小。 系統只會顯示支援的節點大小。 如果您想要執行許多計算密集或記憶體密集的套件，請選取大型節點大小 (擴大)。

   1. 針對 [節點數目]，選取整合執行階段叢集中的節點數目。 系統只會顯示支援的節點數目。 如果您想要平行執行許多套件，請選取具有許多節點的大型叢集 (擴增)。

   1. 針對 [版本/授權]，選取適合您整合執行階段的 SQL Server 版本：[標準版] 或 [企業版]。 如果您想要在整合執行階段上使用進階功能，請選取 [企業版]。

   1. 針對 [節省費用]，選取適合您整合執行階段的 Azure Hybrid Benefit 選項：[是] 或 [否]。 如果您想要自備附有軟體保證的 SQL Server 授權，以混搭使用來享有節省費用的權益，請選取 [是]。

   1. 選取 [下一步] 。

#### <a name="deployment-settings-page"></a>部署設定頁面

在 [**整合執行時間設定**] 窗格的 [**部署設定**] 頁面上，您可以選擇建立 SSISDB 和或 Azure SSIS IR 封裝存放區。

##### <a name="creating-ssisdb"></a>建立 SSISDB

在 [ **Integration runtime 安裝程式**] 窗格的 [**部署設定**] 頁面上，如果您想要將套件部署到 SSISDB (專案部署模型) 中，請選取 [**建立 SSIS 目錄 (由 Azure SQL Database 伺服器/受控執行個體裝載的 ssisdb) ，以儲存您的專案/套件/環境/執行記錄**] 核取方塊。 或者，如果您想要將封裝部署到檔案系統、Azure 檔案儲存體或 SQL Server 資料庫 (MSDB) 裝載于 Azure SQL 受控執行個體 (套件部署模型) ，則不需要建立 SSISDB 或選取核取方塊。

不論您的部署模型為何，如果您想要使用 Azure SQL 受控執行個體所裝載的 SQL Server Agent 來協調/排程您的套件執行，則會由 SSISDB 啟用，因此請選取該核取方塊。 如需詳細資訊，請參閱[透過 Azure SQL 受控執行個體代理程式排程 SSIS 套件執行](https://docs.microsoft.com/azure/data-factory/how-to-invoke-ssis-package-managed-instance-agent) \(部分機器翻譯\)。
   
如果您選取此核取方塊，請完成下列步驟，讓您自己的資料庫伺服器裝載 SSISDB，以代表您建立及管理。

   ![SSISDB 的部署設定](./media/tutorial-create-azure-ssis-runtime-portal/deployment-settings.png)
   
   1. 針對 [訂用帳戶]，選取具有資料庫伺服器可裝載 SSISDB 的 Azure 訂用帳戶。 

   1. 針對 [位置]，選取裝載 SSISDB 的資料庫伺服器所在位置。 我們建議您選取整合執行階段所在的相同位置。

   1. 針對 [目錄資料庫伺服器端點]，選取裝載 SSISDB 的資料庫伺服器端點。 
   
      根據所選的資料庫伺服器，系統可以代表您將 SSISDB 執行個體建立為單一資料庫、建立為彈性集區的一部分，或建立在受控執行個體中。 該執行個體可以在公用網路中供您存取，而您也可以藉由加入虛擬網路來加以存取。 如需選擇適當資料庫伺服器類型來裝載 SSISDB 的指導方針，請參閱[比較 SQL Database 與 SQL 受控執行個體](../data-factory/create-azure-ssis-integration-runtime.md#comparison-of-sql-database-and-sql-managed-instance)。   

      如果您選取具有 IP 防火牆規則/虛擬網路服務端點的 Azure SQL Database 伺服器或具有私人端點的受控執行個體來裝載 SSISDB，或您需要在不設定自我裝載 IR 的情況下存取內部部署資料，則必須將 Azure-SSIS IR 加入虛擬網路。 如需詳細資訊，請參閱[在虛擬網路中建立 Azure-SSIS IR](https://docs.microsoft.com/azure/data-factory/create-azure-ssis-integration-runtime)。

   1. 選取 [**使用適用于 ADF 的受控識別 Azure AD 驗證**] 核取方塊，為您的資料庫伺服器選擇用來裝載 SSISDB 的驗證方法。 您可以選擇 SQL 驗證或 Azure AD 驗證來搭配資料處理站的受控識別。

      如果您選取該核取方塊，則必須將資料處理站的受控識別新增至具有資料庫伺服器存取權限的 Azure AD 群組中。 如需詳細資訊，請參閱[使用 Azure AD 驗證來建立 Azure-SSIS IR](https://docs.microsoft.com/azure/data-factory/create-azure-ssis-integration-runtime)。
   
   1. 針對 [管理使用者名稱]，為裝載 SSISDB 的資料庫伺服器輸入 SQL 驗證使用者名稱。 

   1. 針對 [管理密碼]，為裝載 SSISDB 的資料庫伺服器輸入 SQL 驗證密碼。 

   1. 針對 [目錄資料庫服務層級]，選取您資料庫伺服器用來裝載 SSISDB 的服務層級。 選取 [基本]、[標準] 或 [進階] 層，或選取彈性集區名稱。

在適用時，選取 [測試連接]，如果測試成功，請選取 [下一步]。

##### <a name="creating-azure-ssis-ir-package-stores"></a>建立 Azure SSIS IR 套件存放區

在 [ **Integration runtime 安裝程式**] 窗格的 [**部署設定**] 頁面上，如果您想要管理部署到 MSDB、檔案系統或 Azure 檔案儲存體 (套件部署模型) 與 azure SSIS IR 套件存放區的套件，請選取 [**建立封裝存放區]，以管理部署到 [檔案系統]/[AZURE 檔案儲存體/SQL Server 資料庫 (MSDB) 裝載于 azure SQL 受控執行個體**] 核取方塊中的
   
Azure-SSIS IR 套件存放區可讓您透過 SSMS (類似於[舊版 SSIS 套件存放區](https://docs.microsoft.com/sql/integration-services/service/package-management-ssis-service?view=sql-server-2017))，來匯入/匯出/刪除/執行套件，以及監視/停止執行套件。 如需詳細資訊，請參閱[使用 Azure-SSIS IR 套件存放區管理 SSIS 套件](https://docs.microsoft.com/azure/data-factory/azure-ssis-integration-runtime-package-store) \(英文\)。
   
如果您選取此核取方塊，您可以選取 [新增]，將多個套件存放區新增至 Azure-SSIS IR。 相反地，多個 Azure SSIS IR 也可以共用一個套件存放區。

![MSDB/檔案系統/Azure 檔案儲存體的部署設定](./media/tutorial-create-azure-ssis-runtime-portal/deployment-settings2.png)

在 [新增套件存放區] 窗格上，完成下列步驟。
   
   1. 針對 [套件存放區名稱]，輸入套件存放區的名稱。 

   1. 針對 [套件存放區連結的服務]，選取現有的連結服務，其儲存您的套件部署所在檔案系統/Azure 檔案儲存體/Azure SQL 受控執行個體的存取資訊，或選取 [新增] 來建立新的服務。 在 [新增連結服務] 窗格上，完成下列步驟。 

      ![連結服務的部署設定](./media/tutorial-create-azure-ssis-runtime-portal/deployment-settings-linked-service.png)

      1. 針對 [名稱]，輸入連結服務的名稱。 
         
      1. 針對 [描述]，輸入連結服務的描述。 
         
      1. 針對 [類型]，選取 [Azure 檔案儲存體]、[Azure SQL 受控執行個體] 或 [檔案系統]。

      1. 您可以忽略 [透過整合執行階段連線]，因為我們一律使用 Azure-SSIS IR 來擷取套件存放區的存取資訊。

      1. 如果您選取 [Azure 檔案儲存體]，請完成下列步驟。 

         1. 針對 [帳戶選取方法]，選取 [從 Azure 訂用帳戶] 或 [手動輸入]。
         
         1. 如果您選取 [從 Azure 訂用帳戶]，請選取相關的 [Azure 訂用帳戶]、[儲存體帳戶名稱] 與 [檔案共用]。
            
         1. 如果您選取 [手動輸入]，請針對 [主機] 輸入`\\<storage account name>.file.core.windows.net\<file share name>`、針對 [使用者名稱] 輸入 `Azure\<storage account name>`，並針對 [密碼] 輸入 `<storage account key>`，或選取將其儲存為祕密的 [Azure Key Vault]。

      1. 如果您選取 [Azure SQL 受控執行個體]，請完成下列步驟。 

         1. 選取 [連接字串] 以手動輸入，或輸入將其儲存為祕密的 [Azure Key Vault]。
         
         1. 如果您選取 [連接字串]，請完成下列步驟。 

            1. 針對 [完整網域名稱]，輸入 `<server name>.<dns prefix>.database.windows.net` 或 `<server name>.public.<dns prefix>.database.windows.net,3342` 分別作為 Azure SQL 受控執行個體的私人或公用端點。 如果您輸入私人端點，則 [測試連線] 不適用，因為 ADF UI 無法與其連線。

            1. 針對 [資料庫名稱]，輸入 `msdb`。
               
            1. 針對 [驗證類型]，選取 [SQL 驗證]、[受控識別] 或 [服務主體]。

            1. 如果您選取 [SQL 驗證]，請輸入相關 [使用者名稱] 與 [密碼]，或選取將其儲存為祕密的 [Azure Key Vault]。

            1. 如果您選取 [受控識別]，請將 ADF 受控識別存取權授與 Azure SQL 受控執行個體。

            1. 如果您選取 [服務主體]，請輸入相關 [服務主體識別碼] 與 [服務主體金鑰]，或選取將其儲存為祕密的 [Azure Key Vault]。

      1. 如果您選取 [檔案系統]，請針對 [主機] 輸入套件部署所在資料夾的 UNC 路徑，以及相關 [使用者名稱] 與 [密碼]，或選取將其儲存為祕密的 [Azure Key Vault]。

      1. 在適用時，選取 [測試連接]，如果測試成功，請選取 [建立]。

   1. 已新增的套件存放區會出現在 [部署設定] 頁面上。 若要將其移除，請選取其核取方塊，然後選取 [刪除]。

在適用時，選取 [測試連接]，如果測試成功，請選取 [下一步]。

#### <a name="advanced-settings-page"></a>進階設定頁面

在 [整合執行階段設定] 窗格的 [進階設定] 頁面上，完成下列步驟。

   ![進階設定](./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings.png)

   1. 針對 [每節點的平行執行數上限]，選取執行階段叢集中每節點上可同時執行的套件數量上限。 系統只會顯示支援的套件數目。 如果您想要使用多個核心來執行計算密集或記憶體密集的單一大型套件，請選取較低的數字。 如果您想要在單一核心中執行一或多個小型套件，請選取較高的數字。

   1. 選取 [使用額外的系統設定/元件安裝來自訂您的 Azure-SSIS Integration Runtime] 核取方塊，選擇是否要在您的 Azure-SSIS IR 上新增標準/快速自訂設定。 如需詳細資訊，請參閱 [Azure-SSIS IR 的自訂設定](https://docs.microsoft.com/azure/data-factory/how-to-configure-azure-ssis-ir-custom-setup)。

      如果您選取此核取方塊，請完成下列步驟。

      ![自訂安裝的進階設定](./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings-custom.png)
   
      1. 針對 [**自訂安裝容器 SAS uri**]，輸入您的容器的 SAS uri，您可以在其中儲存標準自訂設定的腳本和相關檔案。

      1. 針對**快速自訂安裝**，請選取 [新增] 以開啟 [**新增****快速自訂安裝**面板]，然後選取 [**快速自訂安裝類型**] 下拉式功能表下的任何類型，例如 [**執行 cmdkey 命令**]、[**新增環境變數**]、[**安裝授權的元件**] 等等。

         如果您選取 [**安裝授權] 元件**類型，則可以從 [**元件名稱**] 下拉式功能表底下的 ISV 合作夥伴選取任何整合元件，並視需要輸入產品授權金鑰/將您購買的產品授權檔案，上傳到 [**授權金鑰** / **授權**檔案] 方塊中。
  
         您新增的快速自訂設定將會出現在 [ **Advanced settings** ] 頁面上。 若要移除它們，您可以選取其核取方塊，然後選取 [**刪除**]。

   1. 選取 [**選取要加入的 AZURE SSIS Integration Runtime 的 VNet]、[允許 ADF 建立特定的網路資源] 和 [選擇性地攜帶自己的靜態公用 IP 位址**] 核取方塊，以選擇是否要將整合執行時間加入虛擬網路。 

      如果您使用具有 IP 防火牆規則/虛擬網路服務端點的 Azure SQL Database 伺服器或具有私人端點的受控實例來裝載 SSISDB，或如果您需要存取內部部署資料 (（也就是您的 SSIS 套件中有內部部署資料來源或目的地) 但不設定自我裝載 IR），請選取它。 如需詳細資訊，請參閱將[AZURE SSIS IR 加入虛擬網路](https://docs.microsoft.com/azure/data-factory/join-azure-ssis-integration-runtime-virtual-network)。 

      如果您選取此核取方塊，請完成下列步驟。

      ![虛擬網路進階設定](./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings-vnet.png)

      1. 針對 [訂用帳戶]****，選取具有您的虛擬網路的 Azure 訂用帳戶。

      1. 針對 [位置]****，選取整合執行階段的相同位置。

      1. 針對 [**類型**]，選取您的虛擬網路類型： [傳統] 或 [Azure Resource Manager]。 我們建議您選取 Azure Resource Manager 虛擬網路，因為傳統虛擬網路即將淘汰。

      1. 針對 **[VNet 名稱]**，選取您虛擬網路的名稱。 它應該與用於具有虛擬網路服務端點或受控實例（具有私人端點）來裝載 SSISDB 的 Azure SQL Database 伺服器相同。 或者，它應該與連線到內部部署網路的同一個相同。 否則，它可以是任何虛擬網路，以將您自己的靜態公用 IP 位址帶入 Azure SSIS IR。

      1. 針對 [子網路名稱]****，選取虛擬網路的子網路名稱。 它應該與您的 Azure SQL Database 伺服器用來裝載 SSISDB 的虛擬網路服務端點相同。 或者，它應該與您的受控實例所使用的子網不同，而私人端點會裝載 SSISDB。 否則，它可以是任何子網，以將您自己的靜態公用 IP 位址帶入 Azure SSIS IR。

      1. 選取 [**為您的 AZURE ssis Integration Runtime 的靜態公用 ip 位址**] 核取方塊，選擇是否要為 AZURE ssis IR 攜帶自己的靜態公用 ip 位址，以便在您的資料來源的防火牆上允許它們。

         如果您選取此核取方塊，請完成下列步驟。

         1. 針對 [**第一個靜態公用 ip 位址**]，選取符合您的 AZURE SSIS IR 需求的第一個靜態公用 ip 位址。 如果您沒有任何專案，請按一下 [**建立新**連結]，在 Azure 入口網站上建立靜態公用 IP 位址，然後按一下這裡的 [重新整理] 按鈕，即可加以選取。
      
         1. 針對 [**第二個靜態公用 ip 位址**]，選取符合您的 AZURE SSIS IR 需求的第二個靜態公用 ip 位址。 如果您沒有任何專案，請按一下 [**建立新**連結]，在 Azure 入口網站上建立靜態公用 IP 位址，然後按一下這裡的 [重新整理] 按鈕，即可加以選取。

   1. 選取 [將自我裝載整合執行階段設定為 Azure-SSIS Integration Runtime 的 Proxy] 核取方塊，來選擇是否要將自我裝載 IR 設定為 Azure-SSIS IR 的 Proxy。 如需詳細資訊，請參閱[將自我裝載 IR 設定為 Proxy](https://docs.microsoft.com/azure/data-factory/self-hosted-integration-runtime-proxy-ssis)。 

      如果您選取此核取方塊，請完成下列步驟。

      ![使用自我裝載 IR 的 Advanced 設定](./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings-shir.png)

      1. 對於**自我**裝載的 Integration Runtime，請選取您現有的自我裝載 IR 作為 AZURE SSIS IR 的 proxy。

      1. 針對**暫存儲存體連結服務**，請選取您現有的 Azure Blob 儲存體連結服務，或建立一個新的來進行預備。

      1. 針對 [**暫存路徑**]，請在您選取的 Azure blob 儲存體帳戶中指定 blob 容器，或將其保留空白，以使用預設值來進行預備。

   1. 選取 [ **VNet 驗證**  >  **繼續**]。 

在 [摘要]**** 區段上，檢閱所有佈建設定、將建議的文件連結設為書籤，然後選取 [完成]**** 以開始建立您的整合執行階段。

   > [!NOTE]
   > 若不包含任何自訂的設定時間，此程序應該會在 5 分鐘內完成。 但可能需要20-30 分鐘的時間，Azure SSIS IR 才能加入虛擬網路。
   >
   > 如果您使用 SSISDB，Data Factory 服務將會連線到資料庫伺服器來準備 SSISDB。 它也會為您的虛擬網路設定許可權和設定（若有指定），並將您的 Azure SSIS IR 加入虛擬網路。
   > 
   > 當您佈建 Azure-SSIS IR 時，也會安裝 Access 可轉散發套件和適用於 SSIS 的 Azure Feature Pack。 除了內建元件已支援的資料來源外，這些元件還可讓您連線到 Excel 檔案、Access 檔案及各種 Azure 資料來源。 如需內建/預先安裝元件的詳細資訊，請參閱 [Azure-SSIS IR 上的內建/預先安裝元件](https://docs.microsoft.com/azure/data-factory/built-in-preinstalled-components-ssis-integration-runtime) \(英文\)。 如需其他可安裝元件的詳細資訊，請參閱 [Azure-SSIS IR 的自訂設定](https://docs.microsoft.com/azure/data-factory/how-to-configure-azure-ssis-ir-custom-setup) \(部分機器翻譯\)。

#### <a name="connections-pane"></a>[連線] 窗格

在 [管理] 中樞的 [連線] 窗格上，切換至 [整合執行階段] 頁面，然後選取 [重新整理]。 

   ![[連線] 窗格](./media/tutorial-create-azure-ssis-runtime-portal/connections-pane.png)

   您可以透過選取 Azure-SSIS IR 的名稱來加以編輯/重新設定。 您也可以選取相關按鈕來監視/啟動/停止/刪除 Azure-SSIS IR、自動產生具有執行 SSIS 套件活動的 ADF 管線，以在 Azure-SSIS IR 上執行，以及檢視 Azure-SSIS IR 的 JSON 程式碼/承載。  只有當 Azure-SSIS IR 停止時，才能加以編輯/刪除。

### <a name="azure-ssis-integration-runtimes-in-the-portal"></a>入口網站中的 Azure SSIS 整合執行階段

1. 在 Azure Data Factory UI 中，切換至 [編輯] 索引標籤，然後選取 [連線]。 然後切換至 [整合執行階段] 索引標籤，以在資料處理站中檢視現有的整合執行階段。

   ![檢視現有的 IR](./media/tutorial-create-azure-ssis-runtime-portal/view-azure-ssis-integration-runtimes.png)

1. 選取 [**新增**] 以建立新的 AZURE SSIS IR，並開啟 [ **Integration runtime 設定**] 窗格。

   ![整合執行階段功能表](./media/tutorial-create-azure-ssis-runtime-portal/edit-connections-new-integration-runtime-button.png)

1. 在 [整合執行階段設定] 面板中，選取 [隨即轉移現有的 SSIS 套件以在 Azure 中執行] 圖格，然後選取 [下一步]。

   ![指定整合執行階段的類型](./media/tutorial-create-azure-ssis-runtime-portal/integration-runtime-setup-options.png)

1. 如需設定 Azure SSIS IR 的其餘步驟，請參閱布建[AZURE ssis 整合運行](#provision-an-azure-ssis-integration-runtime)時間一節。

## <a name="use-azure-powershell-to-create-an-integration-runtime"></a>使用 Azure PowerShell 建立整合執行時間

在本節中，您會使用 Azure PowerShell 來建立 Azure SSIS IR。

### <a name="create-variables"></a>建立變數

複製並貼上下列指令碼。 指定變數值。 

```powershell
### Azure Data Factory info
# If your input contains a PSH special character like "$", precede it with the escape character "`" - for example, "`$"
$SubscriptionName = "[your Azure subscription name]"
$ResourceGroupName = "[your Azure resource group name]"
# Data factory name - must be globally unique
$DataFactoryName = "[your data factory name]"
# For supported regions, see https://azure.microsoft.com/global-infrastructure/services/?products=data-factory&regions=all
$DataFactoryLocation = "EastUS"

### Azure-SSIS integration runtime info - This is a Data Factory compute resource for running SSIS packages.
$AzureSSISName = "[your Azure-SSIS IR name]"
$AzureSSISDescription = "[your Azure-SSIS IR description]"
# For supported regions, see https://azure.microsoft.com/global-infrastructure/services/?products=data-factory&regions=all
$AzureSSISLocation = "EastUS"
# For supported node sizes, see https://azure.microsoft.com/pricing/details/data-factory/ssis/
$AzureSSISNodeSize = "Standard_D8_v3"
# 1-10 nodes are currently supported
$AzureSSISNodeNumber = 2
# Azure-SSIS IR edition/license info: Standard or Enterprise
$AzureSSISEdition = "Standard" # Standard by default, whereas Enterprise lets you use advanced features on your Azure-SSIS IR
# Azure-SSIS IR hybrid usage info: LicenseIncluded or BasePrice
$AzureSSISLicenseType = "LicenseIncluded" # LicenseIncluded by default, whereas BasePrice lets you bring your own on-premises SQL Server license with Software Assurance to earn cost savings from Azure Hybrid Benefit option
# For a Standard_D1_v2 node, up to 4 parallel executions per node are supported. For other nodes, up to (2 x number of cores) are currently supported.
$AzureSSISMaxParallelExecutionsPerNode = 8
# Custom setup info: Standard/express custom setups
$SetupScriptContainerSasUri = "" # OPTIONAL to provide a SAS URI of blob container for standard custom setup where your script and its associated files are stored
$ExpressCustomSetup = "[RunCmdkey|SetEnvironmentVariable|InstallAzurePowerShell|SentryOne.TaskFactory|oh22is.SQLPhonetics.NET|oh22is.HEDDA.IO|KingswaySoft.IntegrationToolkit|KingswaySoft.ProductivityPack|Theobald.XtractIS|AecorSoft.IntegrationService or leave it empty]" # OPTIONAL to configure an express custom setup without script
# Virtual network info: Classic or Azure Resource Manager
$VnetId = "[your virtual network resource ID or leave it empty]" # REQUIRED if you use an Azure SQL Database server with IP firewall rules/virtual network service endpoints or a managed instance with private endpoint to host SSISDB, or if you require access to on-premises data without configuring a self-hosted IR. We recommend an Azure Resource Manager virtual network, because classic virtual networks will be deprecated soon.
$SubnetName = "[your subnet name or leave it empty]" # WARNING: Use the same subnet as the one used for your Azure SQL Database server with virtual network service endpoints, or a different subnet from the one used for your managed instance with a private endpoint
# Public IP address info: OPTIONAL to provide two standard static public IP addresses with DNS name under the same subscription and in the same region as your virtual network
$FirstPublicIP = "[your first public IP address resource ID or leave it empty]"
$SecondPublicIP = "[your second public IP address resource ID or leave it empty]"

### SSISDB info
$SSISDBServerEndpoint = "[your Azure SQL Database server name.database.windows.net or managed instance name.DNS prefix.database.windows.net or managed instance name.public.DNS prefix.database.windows.net,3342 or leave it empty if you do not use SSISDB]" # WARNING: If you use SSISDB, ensure that there's no existing SSISDB on your database server, so we can prepare and manage one on your behalf
# Authentication info: SQL or Azure AD
$SSISDBServerAdminUserName = "[your server admin username for SQL authentication or leave it empty for Azure AD authentication]"
$SSISDBServerAdminPassword = "[your server admin password for SQL authentication or leave it empty for Azure AD authentication]"
# For the basic pricing tier, specify "Basic," not "B." For standard, premium, and elastic pool tiers, specify "S0," "S1," "S2," "S3," etc. See https://docs.microsoft.com/azure/sql-database/sql-database-resource-limits-database-server.
$SSISDBPricingTier = "[Basic|S0|S1|S2|S3|S4|S6|S7|S9|S12|P1|P2|P4|P6|P11|P15|…|ELASTIC_POOL(name = <elastic_pool_name>) for Azure SQL Database server or leave it empty for managed instance]"

### Self-hosted integration runtime info - This can be configured as a proxy for on-premises data access 
$DataProxyIntegrationRuntimeName = "" # OPTIONAL to configure a proxy for on-premises data access 
$DataProxyStagingLinkedServiceName = "" # OPTIONAL to configure a proxy for on-premises data access 
$DataProxyStagingPath = "" # OPTIONAL to configure a proxy for on-premises data access 
```

### <a name="sign-in-and-select-a-subscription"></a>登入並選取訂用帳戶

新增下列腳本以登入並選取您的 Azure 訂用帳戶。

```powershell
Connect-AzAccount
Select-AzSubscription -SubscriptionName $SubscriptionName
```

### <a name="validate-the-connection-to-database-server"></a>驗證資料庫伺服器的連線

新增下列腳本來驗證您的 Azure SQL Database 伺服器或受控實例。

```powershell
# Validate only if you use SSISDB and you don't use virtual network or Azure AD authentication
if(![string]::IsNullOrEmpty($SSISDBServerEndpoint))
{
    if([string]::IsNullOrEmpty($VnetId) -and [string]::IsNullOrEmpty($SubnetName))
    {
        if(![string]::IsNullOrEmpty($SSISDBServerAdminUserName) -and ![string]::IsNullOrEmpty($SSISDBServerAdminPassword))
        {
            $SSISDBConnectionString = "Data Source=" + $SSISDBServerEndpoint + ";User ID=" + $SSISDBServerAdminUserName + ";Password=" + $SSISDBServerAdminPassword
            $sqlConnection = New-Object System.Data.SqlClient.SqlConnection $SSISDBConnectionString;
            Try
            {
                $sqlConnection.Open();
            }
            Catch [System.Data.SqlClient.SqlException]
            {
                Write-Warning "Cannot connect to your Azure SQL Database server, exception: $_";
                Write-Warning "Please make sure the server you specified has already been created. Do you want to proceed? [Y/N]"
                $yn = Read-Host
                if(!($yn -ieq "Y"))
                {
                    Return;
                }
            }
        }
    }
}
```

### <a name="configure-the-virtual-network"></a>設定虛擬網路

新增下列腳本，以自動設定虛擬網路許可權和設定，讓您的 Azure SSIS 整合執行時間加入。

```powershell
# Make sure to run this script against the subscription to which the virtual network belongs
if(![string]::IsNullOrEmpty($VnetId) -and ![string]::IsNullOrEmpty($SubnetName))
{
    # Register to the Azure Batch resource provider
    $BatchApplicationId = "ddbf3205-c6bd-46ae-8127-60eb93363864"
    $BatchObjectId = (Get-AzADServicePrincipal -ServicePrincipalName $BatchApplicationId).Id
    Register-AzResourceProvider -ProviderNamespace Microsoft.Batch
    while(!(Get-AzResourceProvider -ProviderNamespace "Microsoft.Batch").RegistrationState.Contains("Registered"))
    {
    Start-Sleep -s 10
    }
    if($VnetId -match "/providers/Microsoft.ClassicNetwork/")
    {
        # Assign the VM contributor role to Microsoft.Batch
        New-AzRoleAssignment -ObjectId $BatchObjectId -RoleDefinitionName "Classic Virtual Machine Contributor" -Scope $VnetId
    }
}
```

### <a name="create-a-resource-group"></a>建立資源群組

使用 [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) 命令來建立 [Azure 資源群組](../azure-resource-manager/management/overview.md)。 資源群組是在其中以群組方式部署與管理 Azure 資源的邏輯容器。

如果您的資源群組已經存在，請勿將此程式碼複製到您的指令碼。 

```powershell
New-AzResourceGroup -Location $DataFactoryLocation -Name $ResourceGroupName
```

### <a name="create-a-data-factory"></a>建立 Data Factory

執行下列命令來建立資料處理站。

```powershell
Set-AzDataFactoryV2 -ResourceGroupName $ResourceGroupName `
    -Location $DataFactoryLocation `
    -Name $DataFactoryName
```

### <a name="create-an-integration-runtime"></a>建立整合執行階段

執行下列命令，以建立在 Azure 中執行 SSIS 套件的 Azure-SSIS 整合執行階段。

如果您不使用 SSISDB，可以省略 `CatalogServerEndpoint` 、 `CatalogPricingTier` 和 `CatalogAdminCredential` 參數。

如果您未使用具有 IP 防火牆規則/虛擬網路服務端點的 Azure SQL Database 伺服器，或具有私人端點的受控實例來裝載 SSISDB，或要求存取內部部署資料，您可以省略 `VNetId` 和參數， `Subnet` 或為其傳遞空白值。 如果您將自我裝載 IR 設定為 Azure SSIS IR 的 proxy 以存取內部部署資料，您也可以省略它們。 否則，您不能省略它們，而且必須從您的虛擬網路設定傳遞有效的值。 如需詳細資訊，請參閱將[AZURE SSIS IR 加入虛擬網路](https://docs.microsoft.com/azure/data-factory/join-azure-ssis-integration-runtime-virtual-network)。

如果您使用受控實例來裝載 SSISDB，您可以省略 `CatalogPricingTier` 參數，或為其傳遞空白值。 否則，您不能省略它，而且必須從 Azure SQL Database 的支援定價層清單中傳遞有效的值。 如需詳細資訊，請參閱[SQL Database 資源限制](../sql-database/sql-database-resource-limits.md)。

如果您使用 Azure AD 驗證搭配受控識別，以讓您的資料處理站連線到資料庫伺服器，您可以省略 `CatalogAdminCredential` 參數。 但是，您必須將資料處理站的受控識別新增到具有資料庫伺服器存取權限的 Azure AD 群組中。 如需詳細資訊，請參閱[啟用 AZURE SSIS IR 的 Azure AD 驗證](https://docs.microsoft.com/azure/data-factory/enable-aad-authentication-azure-ssis-ir)。 否則，您不能省略它，而且必須傳遞以伺服器管理員使用者名稱和密碼形成的有效物件，以進行 SQL 驗證。

```powershell
Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
    -DataFactoryName $DataFactoryName `
    -Name $AzureSSISName `
    -Description $AzureSSISDescription `
    -Type Managed `
    -Location $AzureSSISLocation `
    -NodeSize $AzureSSISNodeSize `
    -NodeCount $AzureSSISNodeNumber `
    -Edition $AzureSSISEdition `
    -LicenseType $AzureSSISLicenseType `
    -MaxParallelExecutionsPerNode $AzureSSISMaxParallelExecutionsPerNode `
    -VnetId $VnetId `
    -Subnet $SubnetName
       
# Add the CatalogServerEndpoint, CatalogPricingTier, and CatalogAdminCredential parameters if you use SSISDB
if(![string]::IsNullOrEmpty($SSISDBServerEndpoint))
{
    Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
        -DataFactoryName $DataFactoryName `
        -Name $AzureSSISName `
        -CatalogServerEndpoint $SSISDBServerEndpoint `
        -CatalogPricingTier $SSISDBPricingTier

    if(![string]::IsNullOrEmpty($SSISDBServerAdminUserName) –and ![string]::IsNullOrEmpty($SSISDBServerAdminPassword)) # Add the CatalogAdminCredential parameter if you don't use Azure AD authentication
    {
        $secpasswd = ConvertTo-SecureString $SSISDBServerAdminPassword -AsPlainText -Force
        $serverCreds = New-Object System.Management.Automation.PSCredential($SSISDBServerAdminUserName, $secpasswd)

        Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
            -DataFactoryName $DataFactoryName `
            -Name $AzureSSISName `
            -CatalogAdminCredential $serverCreds
    }
}

# Add custom setup parameters if you use standard/express custom setups
if(![string]::IsNullOrEmpty($SetupScriptContainerSasUri))
{
    Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
        -DataFactoryName $DataFactoryName `
        -Name $AzureSSISName `
        -SetupScriptContainerSasUri $SetupScriptContainerSasUri
}
if(![string]::IsNullOrEmpty($ExpressCustomSetup))
{
    if($ExpressCustomSetup -eq "RunCmdkey")
    {
        $addCmdkeyArgument = "YourFileShareServerName or YourAzureStorageAccountName.file.core.windows.net"
        $userCmdkeyArgument = "YourDomainName\YourUsername or azure\YourAzureStorageAccountName"
        $passCmdkeyArgument = New-Object Microsoft.Azure.Management.DataFactory.Models.SecureString("YourPassword or YourAccessKey")
        $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.CmdkeySetup($addCmdkeyArgument, $userCmdkeyArgument, $passCmdkeyArgument)
    }
    if($ExpressCustomSetup -eq "SetEnvironmentVariable")
    {
        $variableName = "YourVariableName"
        $variableValue = "YourVariableValue"
        $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.EnvironmentVariableSetup($variableName, $variableValue)
    }
    if($ExpressCustomSetup -eq "InstallAzurePowerShell")
    {
        $moduleVersion = "YourAzModuleVersion"
        $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.AzPowerShellSetup($moduleVersion)
    }
    if($ExpressCustomSetup -eq "SentryOne.TaskFactory")
    {
        $licenseKey = New-Object Microsoft.Azure.Management.DataFactory.Models.SecureString("YourLicenseKey")
        $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.ComponentSetup($ExpressCustomSetup, $licenseKey)
    }
    if($ExpressCustomSetup -eq "oh22is.SQLPhonetics.NET")
    {
        $licenseKey = New-Object Microsoft.Azure.Management.DataFactory.Models.SecureString("YourLicenseKey")
        $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.ComponentSetup($ExpressCustomSetup, $licenseKey)
    }
    if($ExpressCustomSetup -eq "oh22is.HEDDA.IO")
    {
        $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.ComponentSetup($ExpressCustomSetup)
    }
    if($ExpressCustomSetup -eq "KingswaySoft.IntegrationToolkit")
    {
        $licenseKey = New-Object Microsoft.Azure.Management.DataFactory.Models.SecureString("YourLicenseKey")
        $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.ComponentSetup($ExpressCustomSetup, $licenseKey)
    }
    if($ExpressCustomSetup -eq "KingswaySoft.ProductivityPack")
    {
        $licenseKey = New-Object Microsoft.Azure.Management.DataFactory.Models.SecureString("YourLicenseKey")
        $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.ComponentSetup($ExpressCustomSetup, $licenseKey)
    }    
    if($ExpressCustomSetup -eq "Theobald.XtractIS")
    {
        $jsonData = Get-Content -Raw -Path YourLicenseFile.json
        $jsonData = $jsonData -replace '\s',''
        $jsonData = $jsonData.replace('"','\"')
        $licenseKey = New-Object Microsoft.Azure.Management.DataFactory.Models.SecureString($jsonData)
        $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.ComponentSetup($ExpressCustomSetup, $licenseKey)
    }
    if($ExpressCustomSetup -eq "AecorSoft.IntegrationService")
    {
        $licenseKey = New-Object Microsoft.Azure.Management.DataFactory.Models.SecureString("YourLicenseKey")
        $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.ComponentSetup($ExpressCustomSetup, $licenseKey)
    }
    # Create an array of one or more express custom setups
    $setups = New-Object System.Collections.ArrayList
    $setups.Add($setup)

    Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
        -DataFactoryName $DataFactoryName `
        -Name $AzureSSISName `
        -ExpressCustomSetup $setups
}

# Add self-hosted integration runtime parameters if you configure a proxy for on-premises data accesss
if(![string]::IsNullOrEmpty($DataProxyIntegrationRuntimeName) -and ![string]::IsNullOrEmpty($DataProxyStagingLinkedServiceName))
{
    Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
        -DataFactoryName $DataFactoryName `
        -Name $AzureSSISName `
        -DataProxyIntegrationRuntimeName $DataProxyIntegrationRuntimeName `
        -DataProxyStagingLinkedServiceName $DataProxyStagingLinkedServiceName

    if(![string]::IsNullOrEmpty($DataProxyStagingPath))
    {
        Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
            -DataFactoryName $DataFactoryName `
            -Name $AzureSSISName `
            -DataProxyStagingPath $DataProxyStagingPath
    }
}

# Add public IP address parameters if you bring your own static public IP addresses
if(![string]::IsNullOrEmpty($FirstPublicIP) -and ![string]::IsNullOrEmpty($SecondPublicIP))
{
    $publicIPs = @($FirstPublicIP, $SecondPublicIP)
    Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
        -DataFactoryName $DataFactoryName `
        -Name $AzureSSISName `
        -PublicIPs $publicIPs
}
```

### <a name="start-the-integration-runtime"></a>啟動整合執行時間

執行下列命令以啟動 Azure-SSIS 整合執行階段。

```powershell
write-host("##### Starting #####")
Start-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
    -DataFactoryName $DataFactoryName `
    -Name $AzureSSISName `
    -Force

write-host("##### Completed #####")
write-host("If any cmdlet is unsuccessful, please consider using -Debug option for diagnostics.")
```

> [!NOTE]
> 若不包含任何自訂的設定時間，此程序應該會在 5 分鐘內完成。 但可能需要20-30 分鐘的時間，Azure SSIS IR 才能加入虛擬網路。
>
> 如果您使用 SSISDB，Data Factory 服務將會連線到資料庫伺服器來準備 SSISDB。 它也會為您的虛擬網路設定許可權和設定（若有指定），並將您的 Azure SSIS IR 加入虛擬網路。
> 
> 當您佈建 Azure-SSIS IR 時，也會安裝 Access 可轉散發套件和適用於 SSIS 的 Azure Feature Pack。 除了內建元件已支援的資料來源外，這些元件還可讓您連線到 Excel 檔案、Access 檔案及各種 Azure 資料來源。 如需內建/預先安裝元件的詳細資訊，請參閱 [Azure-SSIS IR 上的內建/預先安裝元件](https://docs.microsoft.com/azure/data-factory/built-in-preinstalled-components-ssis-integration-runtime) \(英文\)。 如需其他可安裝元件的詳細資訊，請參閱 [Azure-SSIS IR 的自訂設定](https://docs.microsoft.com/azure/data-factory/how-to-configure-azure-ssis-ir-custom-setup) \(部分機器翻譯\)。

### <a name="full-script"></a>完整指令碼

以下是建立 Azure SSIS 整合執行時間的完整腳本。

```powershell
### Azure Data Factory info
# If your input contains a PSH special character like "$", precede it with the escape character "`" - for example, "`$"
$SubscriptionName = "[your Azure subscription name]"
$ResourceGroupName = "[your Azure resource group name]"
# Data factory name - must be globally unique
$DataFactoryName = "[your data factory name]"
# For supported regions, see https://azure.microsoft.com/global-infrastructure/services/?products=data-factory&regions=all
$DataFactoryLocation = "EastUS"

### Azure-SSIS integration runtime info - This is a Data Factory compute resource for running SSIS packages.
$AzureSSISName = "[your Azure-SSIS IR name]"
$AzureSSISDescription = "[your Azure-SSIS IR description]"
# For supported regions, see https://azure.microsoft.com/global-infrastructure/services/?products=data-factory&regions=all
$AzureSSISLocation = "EastUS"
# For supported node sizes, see https://azure.microsoft.com/pricing/details/data-factory/ssis/
$AzureSSISNodeSize = "Standard_D8_v3"
# 1-10 nodes are currently supported
$AzureSSISNodeNumber = 2
# Azure-SSIS IR edition/license info: Standard or Enterprise
$AzureSSISEdition = "Standard" # Standard by default, whereas Enterprise lets you use advanced features on your Azure-SSIS IR
# Azure-SSIS IR hybrid usage info: LicenseIncluded or BasePrice
$AzureSSISLicenseType = "LicenseIncluded" # LicenseIncluded by default, whereas BasePrice lets you bring your own on-premises SQL Server license with Software Assurance to earn cost savings from the Azure Hybrid Benefit option
# For a Standard_D1_v2 node, up to four parallel executions per node are supported. For other nodes, up to (2 x number of cores) are currently supported.
$AzureSSISMaxParallelExecutionsPerNode = 8
# Custom setup info: Standard/express custom setups
$SetupScriptContainerSasUri = "" # OPTIONAL to provide a SAS URI of blob container for standard custom setup where your script and its associated files are stored
$ExpressCustomSetup = "[RunCmdkey|SetEnvironmentVariable|InstallAzurePowerShell|SentryOne.TaskFactory|oh22is.SQLPhonetics.NET|oh22is.HEDDA.IO|KingswaySoft.IntegrationToolkit|KingswaySoft.ProductivityPack|Theobald.XtractIS|AecorSoft.IntegrationService or leave it empty]" # OPTIONAL to configure an express custom setup without script
# Virtual network info: Classic or Azure Resource Manager
$VnetId = "[your virtual network resource ID or leave it empty]" # REQUIRED if you use an Azure SQL Database server with IP firewall rules/virtual network service endpoints or a managed instance with private endpoint to host SSISDB, or if you require access to on-premises data without configuring a self-hosted IR. We recommend an Azure Resource Manager virtual network, because classic virtual networks will be deprecated soon.
$SubnetName = "[your subnet name or leave it empty]" # WARNING: Use the same subnet as the one used for your Azure SQL Database server with virtual network service endpoints, or a different subnet from the one used for your managed instance with a private endpoint
# Public IP address info: OPTIONAL to provide two standard static public IP addresses with DNS name under the same subscription and in the same region as your virtual network
$FirstPublicIP = "[your first public IP address resource ID or leave it empty]"
$SecondPublicIP = "[your second public IP address resource ID or leave it empty]"

### SSISDB info
$SSISDBServerEndpoint = "[your Azure SQL Database server name.database.windows.net or managed instance name.DNS prefix.database.windows.net or managed instance name.public.DNS prefix.database.windows.net,3342 or leave it empty if you do not use SSISDB]" # WARNING: If you use SSISDB, ensure that there's no existing SSISDB on your database server, so we can prepare and manage one on your behalf
# Authentication info: SQL or Azure AD
$SSISDBServerAdminUserName = "[your server admin username for SQL authentication or leave it empty for Azure AD authentication]"
$SSISDBServerAdminPassword = "[your server admin password for SQL authentication or leave it empty for Azure AD authentication]"
# For the basic pricing tier, specify "Basic," not "B." For standard, premium, and elastic pool tiers, specify "S0," "S1," "S2," "S3," etc. See https://docs.microsoft.com/azure/sql-database/sql-database-resource-limits-database-server.
$SSISDBPricingTier = "[Basic|S0|S1|S2|S3|S4|S6|S7|S9|S12|P1|P2|P4|P6|P11|P15|…|ELASTIC_POOL(name = <elastic_pool_name>) for Azure SQL Database server or leave it empty for managed instance]"

### Self-hosted integration runtime info - This can be configured as a proxy for on-premises data access 
$DataProxyIntegrationRuntimeName = "" # OPTIONAL to configure a proxy for on-premises data access 
$DataProxyStagingLinkedServiceName = "" # OPTIONAL to configure a proxy for on-premises data access 
$DataProxyStagingPath = "" # OPTIONAL to configure a proxy for on-premises data access 

### Sign in and select a subscription
Connect-AzAccount
Select-AzSubscription -SubscriptionName $SubscriptionName

### Validate the connection to the database server
# Validate only if you use SSISDB and don't use a virtual network or Azure AD authentication
if(![string]::IsNullOrEmpty($SSISDBServerEndpoint))
{
    if([string]::IsNullOrEmpty($VnetId) -and [string]::IsNullOrEmpty($SubnetName))
    {
        if(![string]::IsNullOrEmpty($SSISDBServerAdminUserName) -and ![string]::IsNullOrEmpty($SSISDBServerAdminPassword))
        {
            $SSISDBConnectionString = "Data Source=" + $SSISDBServerEndpoint + ";User ID=" + $SSISDBServerAdminUserName + ";Password=" + $SSISDBServerAdminPassword
            $sqlConnection = New-Object System.Data.SqlClient.SqlConnection $SSISDBConnectionString;
            Try
            {
                $sqlConnection.Open();
            }
            Catch [System.Data.SqlClient.SqlException]
            {
                Write-Warning "Cannot connect to your Azure SQL Database server, exception: $_";
                Write-Warning "Please make sure the server you specified has already been created. Do you want to proceed? [Y/N]"
                $yn = Read-Host
                if(!($yn -ieq "Y"))
                {
                    Return;
                }
            }
        }
    }
}

### Configure a virtual network
# Make sure to run this script against the subscription to which the virtual network belongs
if(![string]::IsNullOrEmpty($VnetId) -and ![string]::IsNullOrEmpty($SubnetName))
{
    # Register to the Azure Batch resource provider
    $BatchApplicationId = "ddbf3205-c6bd-46ae-8127-60eb93363864"
    $BatchObjectId = (Get-AzADServicePrincipal -ServicePrincipalName $BatchApplicationId).Id
    Register-AzResourceProvider -ProviderNamespace Microsoft.Batch
    while(!(Get-AzResourceProvider -ProviderNamespace "Microsoft.Batch").RegistrationState.Contains("Registered"))
    {
    Start-Sleep -s 10
    }
    if($VnetId -match "/providers/Microsoft.ClassicNetwork/")
    {
        # Assign the VM contributor role to Microsoft.Batch
        New-AzRoleAssignment -ObjectId $BatchObjectId -RoleDefinitionName "Classic Virtual Machine Contributor" -Scope $VnetId
    }
}

### Create a data factory
Set-AzDataFactoryV2 -ResourceGroupName $ResourceGroupName `
    -Location $DataFactoryLocation `
    -Name $DataFactoryName

### Create an integration runtime
Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
    -DataFactoryName $DataFactoryName `
    -Name $AzureSSISName `
    -Description $AzureSSISDescription `
    -Type Managed `
    -Location $AzureSSISLocation `
    -NodeSize $AzureSSISNodeSize `
    -NodeCount $AzureSSISNodeNumber `
    -Edition $AzureSSISEdition `
    -LicenseType $AzureSSISLicenseType `
    -MaxParallelExecutionsPerNode $AzureSSISMaxParallelExecutionsPerNode `
    -VnetId $VnetId `
    -Subnet $SubnetName
       
# Add CatalogServerEndpoint, CatalogPricingTier, and CatalogAdminCredential parameters if you use SSISDB
if(![string]::IsNullOrEmpty($SSISDBServerEndpoint))
{
    Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
        -DataFactoryName $DataFactoryName `
        -Name $AzureSSISName `
        -CatalogServerEndpoint $SSISDBServerEndpoint `
        -CatalogPricingTier $SSISDBPricingTier

    if(![string]::IsNullOrEmpty($SSISDBServerAdminUserName) –and ![string]::IsNullOrEmpty($SSISDBServerAdminPassword)) # Add the CatalogAdminCredential parameter if you don't use Azure AD authentication
    {
        $secpasswd = ConvertTo-SecureString $SSISDBServerAdminPassword -AsPlainText -Force
        $serverCreds = New-Object System.Management.Automation.PSCredential($SSISDBServerAdminUserName, $secpasswd)

        Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
            -DataFactoryName $DataFactoryName `
            -Name $AzureSSISName `
            -CatalogAdminCredential $serverCreds
    }
}

# Add custom setup parameters if you use standard/express custom setups
if(![string]::IsNullOrEmpty($SetupScriptContainerSasUri))
{
    Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
        -DataFactoryName $DataFactoryName `
        -Name $AzureSSISName `
        -SetupScriptContainerSasUri $SetupScriptContainerSasUri
}
if(![string]::IsNullOrEmpty($ExpressCustomSetup))
{
    if($ExpressCustomSetup -eq "RunCmdkey")
    {
        $addCmdkeyArgument = "YourFileShareServerName or YourAzureStorageAccountName.file.core.windows.net"
        $userCmdkeyArgument = "YourDomainName\YourUsername or azure\YourAzureStorageAccountName"
        $passCmdkeyArgument = New-Object Microsoft.Azure.Management.DataFactory.Models.SecureString("YourPassword or YourAccessKey")
        $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.CmdkeySetup($addCmdkeyArgument, $userCmdkeyArgument, $passCmdkeyArgument)
    }
    if($ExpressCustomSetup -eq "SetEnvironmentVariable")
    {
        $variableName = "YourVariableName"
        $variableValue = "YourVariableValue"
        $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.EnvironmentVariableSetup($variableName, $variableValue)
    }
    if($ExpressCustomSetup -eq "InstallAzurePowerShell")
    {
        $moduleVersion = "YourAzModuleVersion"
        $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.AzPowerShellSetup($moduleVersion)
    }
    if($ExpressCustomSetup -eq "SentryOne.TaskFactory")
    {
        $licenseKey = New-Object Microsoft.Azure.Management.DataFactory.Models.SecureString("YourLicenseKey")
        $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.ComponentSetup($ExpressCustomSetup, $licenseKey)
    }
    if($ExpressCustomSetup -eq "oh22is.SQLPhonetics.NET")
    {
        $licenseKey = New-Object Microsoft.Azure.Management.DataFactory.Models.SecureString("YourLicenseKey")
        $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.ComponentSetup($ExpressCustomSetup, $licenseKey)
    }
    if($ExpressCustomSetup -eq "oh22is.HEDDA.IO")
    {
        $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.ComponentSetup($ExpressCustomSetup)
    }
    if($ExpressCustomSetup -eq "KingswaySoft.IntegrationToolkit")
    {
        $licenseKey = New-Object Microsoft.Azure.Management.DataFactory.Models.SecureString("YourLicenseKey")
        $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.ComponentSetup($ExpressCustomSetup, $licenseKey)
    }
    if($ExpressCustomSetup -eq "KingswaySoft.ProductivityPack")
    {
        $licenseKey = New-Object Microsoft.Azure.Management.DataFactory.Models.SecureString("YourLicenseKey")
        $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.ComponentSetup($ExpressCustomSetup, $licenseKey)
    }    
    if($ExpressCustomSetup -eq "Theobald.XtractIS")
    {
        $jsonData = Get-Content -Raw -Path YourLicenseFile.json
        $jsonData = $jsonData -replace '\s',''
        $jsonData = $jsonData.replace('"','\"')
        $licenseKey = New-Object Microsoft.Azure.Management.DataFactory.Models.SecureString($jsonData)
        $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.ComponentSetup($ExpressCustomSetup, $licenseKey)
    }
    if($ExpressCustomSetup -eq "AecorSoft.IntegrationService")
    {
        $licenseKey = New-Object Microsoft.Azure.Management.DataFactory.Models.SecureString("YourLicenseKey")
        $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.ComponentSetup($ExpressCustomSetup, $licenseKey)
    }
    # Create an array of one or more express custom setups
    $setups = New-Object System.Collections.ArrayList
    $setups.Add($setup)

    Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
        -DataFactoryName $DataFactoryName `
        -Name $AzureSSISName `
        -ExpressCustomSetup $setups
}

# Add self-hosted integration runtime parameters if you configure a proxy for on-premises data accesss
if(![string]::IsNullOrEmpty($DataProxyIntegrationRuntimeName) -and ![string]::IsNullOrEmpty($DataProxyStagingLinkedServiceName))
{
    Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
        -DataFactoryName $DataFactoryName `
        -Name $AzureSSISName `
        -DataProxyIntegrationRuntimeName $DataProxyIntegrationRuntimeName `
        -DataProxyStagingLinkedServiceName $DataProxyStagingLinkedServiceName

    if(![string]::IsNullOrEmpty($DataProxyStagingPath))
    {
        Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
            -DataFactoryName $DataFactoryName `
            -Name $AzureSSISName `
            -DataProxyStagingPath $DataProxyStagingPath
    }
}

# Add public IP address parameters if you bring your own static public IP addresses
if(![string]::IsNullOrEmpty($FirstPublicIP) -and ![string]::IsNullOrEmpty($SecondPublicIP))
{
    $publicIPs = @($FirstPublicIP, $SecondPublicIP)
    Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
        -DataFactoryName $DataFactoryName `
        -Name $AzureSSISName `
        -PublicIPs $publicIPs
}

### Start the integration runtime
write-host("##### Starting #####")
Start-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
    -DataFactoryName $DataFactoryName `
    -Name $AzureSSISName `
    -Force

write-host("##### Completed #####")
write-host("If any cmdlet is unsuccessful, please consider using -Debug option for diagnostics.")
```

## <a name="use-an-azure-resource-manager-template-to-create-an-integration-runtime"></a>使用 Azure Resource Manager 範本來建立整合執行時間

在本節中，您會使用 Azure Resource Manager 範本來建立 Azure SSIS 整合執行時間。 以下是範例逐步解說：

1. 使用下列 Azure Resource Manager 範本建立 JSON 檔案。 以您自己的值取代角括弧中的值 (預留位置) 。

    ```json
    {
        "contentVersion": "1.0.0.0",
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "parameters": {},
        "variables": {},
        "resources": [{
            "name": "<Specify a name for your data factory>",
            "apiVersion": "2018-06-01",
            "type": "Microsoft.DataFactory/factories",
            "location": "East US",
            "properties": {},
            "resources": [{
                "type": "integrationruntimes",
                "name": "<Specify a name for your Azure-SSIS IR>",
                "dependsOn": [ "<The name of the data factory you specified at the beginning>" ],
                "apiVersion": "2018-06-01",
                "properties": {
                    "type": "Managed",
                    "typeProperties": {
                        "computeProperties": {
                            "location": "East US",
                            "nodeSize": "Standard_D8_v3",
                            "numberOfNodes": 1,
                            "maxParallelExecutionsPerNode": 8
                        },
                        "ssisProperties": {
                            "catalogInfo": {
                                "catalogServerEndpoint": "<Azure SQL Database server name>.database.windows.net",
                                "catalogAdminUserName": "<Azure SQL Database server admin username>",
                                "catalogAdminPassword": {
                                    "type": "SecureString",
                                    "value": "<Azure SQL Database server admin password>"
                                },
                                "catalogPricingTier": "Basic"
                            }
                        }
                    }
                }
            }]
        }]
    }
    ```

2. 若要部署 Azure Resource Manager 範本，請執行 `New-AzResourceGroupDeployment` 命令，如下列範例所示。 在此範例中， `ADFTutorialResourceGroup` 是您資源群組的名稱。 `ADFTutorialARM.json`是包含您 data factory 和 Azure SSIS IR 之 JSON 定義的檔案。

    ```powershell
    New-AzResourceGroupDeployment -Name MyARMDeployment -ResourceGroupName ADFTutorialResourceGroup -TemplateFile ADFTutorialARM.json
    ```

    此命令會在其中建立您的 data factory 和 Azure SSIS IR，但不會啟動 IR。

3. 若要啟動您的 Azure SSIS IR，請執行 `Start-AzDataFactoryV2IntegrationRuntime` 下列命令：

    ```powershell
    Start-AzDataFactoryV2IntegrationRuntime -ResourceGroupName "<Resource Group Name>" `
        -DataFactoryName "<Data Factory Name>" `
        -Name "<Azure SSIS IR Name>" `
        -Force
    ```

> [!NOTE]
> 若不包含任何自訂的設定時間，此程序應該會在 5 分鐘內完成。 但可能需要20-30 分鐘的時間，Azure SSIS IR 才能加入虛擬網路。
>
> 如果您使用 SSISDB，Data Factory 服務將會連線到資料庫伺服器來準備 SSISDB。 它也會為您的虛擬網路設定許可權和設定（若有指定），並將您的 Azure SSIS IR 加入虛擬網路。
> 
> 當您佈建 Azure-SSIS IR 時，也會安裝 Access 可轉散發套件和適用於 SSIS 的 Azure Feature Pack。 除了內建元件已支援的資料來源外，這些元件還可讓您連線到 Excel 檔案、Access 檔案及各種 Azure 資料來源。 如需內建/預先安裝元件的詳細資訊，請參閱 [Azure-SSIS IR 上的內建/預先安裝元件](https://docs.microsoft.com/azure/data-factory/built-in-preinstalled-components-ssis-integration-runtime) \(英文\)。 如需其他可安裝元件的詳細資訊，請參閱 [Azure-SSIS IR 的自訂設定](https://docs.microsoft.com/azure/data-factory/how-to-configure-azure-ssis-ir-custom-setup) \(部分機器翻譯\)。

## <a name="deploy-ssis-packages"></a>部署 SSIS 套件

如果您使用 SSISDB，則可以使用已針對 Azure 啟用的 SSDT 或 SSMS 工具，將套件部署到 SSISDB 中，並在 Azure-SSIS IR 上執行套件。 這些工具在連線至資料庫伺服器時會透過其伺服器端點來進行： 

- 針對 Azure SQL Database 伺服器，伺服器端點的格式為 `<server name>.database.windows.net`。
- 針對具有私人端點的受控執行個體，伺服器端點的格式為 `<server name>.<dns prefix>.database.windows.net`。
- 針對具有公用端點的受控執行個體，伺服器端點的格式為 `<server name>.public.<dns prefix>.database.windows.net,3342`。 

如果您不使用 SSISDB，則可以將套件部署到 Azure SQL 受控執行個體所裝載的檔案系統、Azure 檔案儲存體或 MSDB 中，然後使用 [dtutil](https://docs.microsoft.com/sql/integration-services/dtutil-utility?view=sql-server-2017) 和 [AzureDTExec](https://docs.microsoft.com/azure/data-factory/how-to-invoke-ssis-package-azure-enabled-dtexec) 命令列公用程式，在 Azure-SSIS IR 上加以執行。 

如需詳細資訊，請參閱[部署 SSIS 專案/套件](https://docs.microsoft.com/sql/integration-services/packages/deploy-integration-services-ssis-projects-and-packages?view=sql-server-ver15)。

在這兩種狀況中，您也可以使用 Data Factory 管線中的「執行 SSIS 套件」活動，在 Azure-SSIS IR 上執行已部署的套件。 如需詳細資訊，請參閱[以第一級 Data Factory 活動的形式來叫用 SSIS 套件執行](https://docs.microsoft.com/azure/data-factory/how-to-invoke-ssis-package-ssis-activity)。

## <a name="next-steps"></a>後續步驟

請參閱本檔中的其他 Azure SSIS IR 主題：

- [AZURE SSIS 整合運行](concepts-integration-runtime.md#azure-ssis-integration-runtime)時間。 本文提供整合執行時間的一般相關資訊，包括 Azure SSIS IR。
- [監視 Azure-SSIS IR](monitor-integration-runtime.md#azure-ssis-integration-runtime). 本文說明如何取得及瞭解 Azure SSIS IR 的相關資訊。
- [管理 Azure-SSIS IR](manage-azure-ssis-integration-runtime.md). 本文說明如何停止、啟動或刪除您的 Azure SSIS IR。 它也會示範如何藉由新增更多節點來相應放大您的 Azure SSIS IR。
- [在 Azure 中部署、執行和監視 SSIS 套件](/sql/integration-services/lift-shift/ssis-azure-deploy-run-monitor-tutorial)   
- [連線至 Azure 中的 SSISDB](/sql/integration-services/lift-shift/ssis-azure-connect-to-catalog-database)
- [透過 Windows 驗證連線至內部部署資料來源](/sql/integration-services/lift-shift/ssis-azure-connect-with-windows-auth) 
- [在 Azure 中排程套件執行](/sql/integration-services/lift-shift/ssis-azure-schedule-packages)