---
title: 佈建 Azure-SSIS Integration Runtime
description: 了解如何在 Azure Data Factory 中佈建 Azure-SSIS 整合執行階段，讓您可以在 Azure 部署並執行 SSIS 套件。
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: tutorial
ms.custom: seo-lt-2019
ms.date: 12/23/2019
author: swinarko
ms.author: sawinark
ms.reviewer: douglasl
manager: mflasko
ms.openlocfilehash: 91962b578f9620384af86d25a33ba35830abb285
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/29/2020
ms.locfileid: "81418621"
---
# <a name="provision-the-azure-ssis-integration-runtime-in-azure-data-factory"></a>在 Azure Data Factory 中佈建 Azure-SSIS 整合執行階段

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

本教學課程提供使用 Azure 入口網站在 Azure Data Factory 中佈建 Azure-SQL Server Integration Services (SSIS) 整合執行階段 (IR) 的步驟。 Azure-SSIS IR 可支援：

- 執行已部署到 SSIS 目錄 (SSISDB) 的套件，此目錄則由 Azure SQL Database 伺服器或受控執行個體 (專案部署模型) 裝載。
- 執行已部署到檔案系統、檔案共用或 Azure 檔案儲存體 (套件部署模型) 的套件。 

佈建 Azure-SSIS IR 之後，您就可以使用熟悉的工具在 Azure 中部署和執行套件。 這些工具包括 SQL Server Data Tools (SSDT)、SQL Server Management Studio (SSMS) 和命令列工具，例如 `dtinstall`、`dtutil` 和 `dtexec`。

如需 Azure-SSIS IR 的概念資訊，請參閱 [Azure-SSIS 整合執行階段概觀](concepts-integration-runtime.md#azure-ssis-integration-runtime)。

在本教學課程中，您會完成下列步驟：

> [!div class="checklist"]
> * 建立資料處理站。
> * 佈建 Azure-SSIS 整合執行階段。

## <a name="prerequisites"></a>Prerequisites

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

- **Azure 訂用帳戶**。 如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/)。
- **Azure SQL Database 伺服器 (選用)** 。 如果您還沒有資料庫伺服器，請在 Azure 入口網站中建立一個，然後再開始。 Data Factory 接著會在此資料庫伺服器上建立 SSISDB 執行個體。 

  建議於整合執行階段所在的相同 Azure 區域中建立資料庫伺服器。 此設定可讓整合執行階段將執行記錄寫入 SSISDB，而不需要跨 Azure 區域。

  請記住下列重點：

  - 根據所選的資料庫伺服器，系統可以代表您將 SSISDB 執行個體建立為單一資料庫、建立為彈性集區的一部分，或建立在受控執行個體中。 該執行個體可以在公用網路中供您存取，而您也可以藉由加入虛擬網路來加以存取。 如需有關選擇適當資料庫伺服器類型來裝載 SSISDB 的指引，請參閱[比較 Azure SQL Database 單一資料庫、彈性集區及受控執行個體](../data-factory/create-azure-ssis-integration-runtime.md#comparison-of-a-sql-database-single-database-elastic-pool-and-managed-instance)。 
  
    如果您使用具有 IP 防火牆規則/虛擬網路服務端點的 Azure SQL Database 伺服器或具有私人端點的受控執行個體來裝載 SSISDB，或您需要在不設定自我裝載 IR 的情況下存取內部部署資料，則必須將 Azure-SSIS IR 加入虛擬網路。 如需詳細資訊，請參閱[在虛擬網路中建立 Azure-SSIS IR](https://docs.microsoft.com/azure/data-factory/create-azure-ssis-integration-runtime)。

  - 確認資料庫伺服器的 [允許存取 Azure 服務]  設定已啟用。 如果您使用具有IP 防火牆規則/虛擬網路服務端點的 Azure SQL Database 伺服器或具有私人端點的受控執行個體來裝載 SSISDB，則不適用此設定。 如需詳細資訊，請參閱[保護 Azure SQL 資料庫資料庫](../sql-database/sql-database-security-tutorial.md#create-firewall-rules)。 若要使用 PowerShell 來啟用此設定，請參閱 [New-AzSqlServerFirewallRule](/powershell/module/az.sql/new-azsqlserverfirewallrule)。

  - 新增用戶端電腦的 IP 位址，或新增 IP 位址範圍，其中包含資料庫伺服器之防火牆設定中用戶端電腦 IP 位址到用戶端 IP 位址清單。 如需詳細資訊，請參閱 [Azure SQL Database 伺服器層級和資料庫層級防火牆規則](../sql-database/sql-database-firewall-configure.md)

  - 若要連線至資料庫伺服器，您可以使用 SQL 驗證搭配伺服器管理員認證，也可以使用 Azure AD 驗證搭配資料處理站的受控識別。 若為後者，您必須將資料處理站的受控識別新增至具有資料庫伺服器存取權限的 Azure AD 群組中。 如需詳細資訊，請參閱[使用 Azure AD 驗證來建立 Azure-SSIS IR](https://docs.microsoft.com/azure/data-factory/create-azure-ssis-integration-runtime)。

  - 請確認您的資料庫伺服器尚未有 SSISDB 執行個體。 Azure-SSIS IR 的佈建不支援使用現有的 SSISDB 執行個體。


> [!NOTE]
> 如需目前有提供 Data Factory 和 Azure-SSIS IR 的 Azure 區域清單，請參閱[依區域的 Data Factory 和 SSIS IR 可用性](https://azure.microsoft.com/global-infrastructure/services/?products=data-factory&regions=all)。 

## <a name="create-a-data-factory"></a>建立 Data Factory

若要透過 Azure 入口網站來建立資料處理站，請遵循[透過 UI 來建立資料處理站](https://docs.microsoft.com/azure/data-factory/quickstart-create-data-factory-portal#create-a-data-factory)中的逐步指示。 遵循指示操作時請選取 [釘選到儀表板]  ，以便能快速存取建立好的資料處理站。 

資料處理站建立好之後，請在 Azure 入口網站中開啟其 [概觀] 頁面。 選取 [撰寫和監視]  圖格，以在另一個索引標籤上開啟 [讓我們開始吧]  頁面。您可以在該頁面繼續建立 Azure-SSIS IR。

## <a name="create-an-azure-ssis-integration-runtime"></a>建立 Azure-SSIS 整合執行階段

### <a name="from-the-data-factory-overview"></a>從 Data Factory 概觀

1. 在 [讓我們開始吧]  頁面上，選取 [設定 SSIS 整合執行階段]  圖格。 

   ![[設定 SSIS 整合執行階段] 圖格](./media/tutorial-create-azure-ssis-runtime-portal/configure-ssis-integration-runtime-tile.png)

1. 如需設定 Azure-SSIS IR 的剩餘步驟，請參閱[佈建 Azure-SSIS 整合執行階段](#provision-an-azure-ssis-integration-runtime)一節。 

### <a name="from-the-authoring-ui"></a>從撰寫 UI

1. 在 Azure Data Factory UI 中，切換至 [編輯]  索引標籤，然後選取 [連線]  。 然後切換至 [整合執行階段]  索引標籤，以在資料處理站中檢視現有的整合執行階段。 

   ![檢視現有 IR 的選取項目](./media/tutorial-create-azure-ssis-runtime-portal/view-azure-ssis-integration-runtimes.png)

1. 選取 [新增]  以建立 Azure-SSIS IR。 

   ![整合執行階段功能表](./media/tutorial-create-azure-ssis-runtime-portal/edit-connections-new-integration-runtime-button.png)

1. 在 [整合執行階段設定]  面板中，選取 [隨即轉移現有的 SSIS 套件以在 Azure 中執行]  圖格，然後選取 [下一步]  。 

   ![指定整合執行階段的類型](./media/tutorial-create-azure-ssis-runtime-portal/integration-runtime-setup-options.png)

1. 如需設定 Azure-SSIS IR 的剩餘步驟，請參閱[佈建 Azure-SSIS 整合執行階段](#provision-an-azure-ssis-integration-runtime)一節。 

## <a name="provision-an-azure-ssis-integration-runtime"></a>佈建 Azure-SSIS 整合執行階段

1. 在 [整合執行階段設定]  面板的 [一般設定]  區段上，完成下列步驟。 

   ![一般設定](./media/tutorial-create-azure-ssis-runtime-portal/general-settings.png)

   1. 針對 [名稱]  ，輸入取您整合執行階段的名稱。 

   1. 針對 [描述]  ，輸入整合執行階段的描述。 

   1. 針對 [位置]  ，選取整合執行階段的位置。 系統只會顯示支援的位置。 我們建議您選取裝載 SSISDB 的資料庫伺服器所在位置。 

   1. 針對 [節點大小]  ，選取整合執行階段叢集中的節點大小。 系統只會顯示支援的節點大小。 如果您想要執行許多計算密集或記憶體密集的套件，請選取大型節點大小 (擴大)。 

   1. 針對 [節點數目]  ，選取整合執行階段叢集中的節點數目。 系統只會顯示支援的節點數目。 如果您想要平行執行許多套件，請選取具有許多節點的大型叢集 (擴增)。 

   1. 針對 [版本/授權]  ，選取適合您整合執行階段的 SQL Server 版本：[標準版] 或 [企業版]。 如果您想要在整合執行階段上使用進階功能，請選取 [企業版]。 

   1. 針對 [節省費用]  ，選取適合您整合執行階段的 Azure Hybrid Benefit 選項：[是]  或 [否]  。 如果您想要自備附有軟體保證的 SQL Server 授權，以混搭使用來享有節省費用的權益，請選取 [是]  。 

   1. 選取 [下一步]  。 

1. 在 [SQL 設定]  階段上，完成下列步驟。 

   ![SQL 設定](./media/tutorial-create-azure-ssis-runtime-portal/sql-settings.png)

   1. 選取 [建立由 Azure SQL Database 伺服器/受控執行個體裝載的 SSIS 目錄 (SSISDB) 來儲存您的專案/套件/環境/執行記錄]  核取方塊，為您 Azure-SSIS IR 上執行的套件選擇部署模型。 您可以選擇專案部署模型 (會將套件部署到資料庫伺服器所裝載的 SSISDB)，也可以選擇套件部署模型 (會將套件部署到檔案系統、檔案共用或 Azure 檔案儲存體)。
   
      如果您選取該核取方塊，則必須用自己的資料庫伺服器來裝載 SSISDB，而我們會為您建立及管理 SSISDB。
   
      1. 針對 [訂用帳戶]  ，選取具有資料庫伺服器可裝載 SSISDB 的 Azure 訂用帳戶。 

      1. 針對 [位置]  ，選取裝載 SSISDB 的資料庫伺服器所在位置。 我們建議您選取整合執行階段所在的相同位置。

      1. 針對 [目錄資料庫伺服器端點]  ，選取裝載 SSISDB 的資料庫伺服器端點。 
   
         根據所選的資料庫伺服器，系統可以代表您將 SSISDB 執行個體建立為單一資料庫、建立為彈性集區的一部分，或建立在受控執行個體中。 該執行個體可以在公用網路中供您存取，而您也可以藉由加入虛擬網路來加以存取。 如需有關選擇適當資料庫伺服器類型來裝載 SSISDB 的指引，請參閱[比較 Azure SQL Database 單一資料庫、彈性集區及受控執行個體](../data-factory/create-azure-ssis-integration-runtime.md#comparison-of-a-sql-database-single-database-elastic-pool-and-managed-instance)。   

         如果您選取具有 IP 防火牆規則/虛擬網路服務端點的 Azure SQL Database 伺服器或具有私人端點的受控執行個體來裝載 SSISDB，或您需要在不設定自我裝載 IR 的情況下存取內部部署資料，則必須將 Azure-SSIS IR 加入虛擬網路。 如需詳細資訊，請參閱[在虛擬網路中建立 Azure-SSIS IR](https://docs.microsoft.com/azure/data-factory/create-azure-ssis-integration-runtime)。

      1. 選取 [使用 AAD 驗證搭配 ADF 的受控識別]  核取方塊，來為要裝載 SSISDB 的資料庫伺服器選擇驗證方法。 您可以選擇 SQL 驗證或 Azure AD 驗證來搭配資料處理站的受控識別。

         如果您選取該核取方塊，則必須將資料處理站的受控識別新增至具有資料庫伺服器存取權限的 Azure AD 群組中。 如需詳細資訊，請參閱[使用 Azure AD 驗證來建立 Azure-SSIS IR](https://docs.microsoft.com/azure/data-factory/create-azure-ssis-integration-runtime)。
   
      1. 針對 [管理使用者名稱]  ，為裝載 SSISDB 的資料庫伺服器輸入 SQL 驗證使用者名稱。 

      1. 針對 [管理密碼]  ，為裝載 SSISDB 的資料庫伺服器輸入 SQL 驗證密碼。 

      1. 針對 [目錄資料庫服務層級]  ，選取您資料庫伺服器用來裝載 SSISDB 的服務層級。 選取 [基本]、[標準] 或 [進階] 層，或選取彈性集區名稱。

      1. 選取 [測試連線]  。 如果測試成功，請選取 [下一步]  。 

1. 在 [進階設定]  階段上，完成下列步驟。 

   ![進階設定](./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings.png)

   1. 針對 [每節點的平行執行數上限]  ，選取執行階段叢集中每節點上可同時執行的套件數量上限。 系統只會顯示支援的套件數目。 如果您想要使用多個核心來執行計算密集或記憶體密集的單一大型套件，請選取較低的數字。 如果您想要在單一核心中執行一或多個小型套件，請選取較高的數字。 

   1. 選取 [使用額外的系統設定/元件安裝來自訂您的 Azure-SSIS Integration Runtime]  核取方塊，選擇是否要在您的 Azure-SSIS IR 上新增標準/快速自訂設定。 如需詳細資訊，請參閱 [Azure-SSIS IR 的自訂設定](https://docs.microsoft.com/azure/data-factory/how-to-configure-azure-ssis-ir-custom-setup)。
   
   1. 選取 [選取要讓 Azure-SSIS Integration Runtime 加入的 VNet、允許 ADF 建立某些網路資源及選擇性帶入自己的靜態公用 IP 位址]  核取方塊，以選擇是否要讓您的 Azure-SSIS IR 加入虛擬網路。

      如果您使用具有 IP 防火牆規則/虛擬網路服務端點的 Azure SQL Database 伺服器或具有私人端點的受控執行個體來裝載 SSISDB，或您需要在不設定自我裝載 IR 的情況下存取內部部署資料，請選取此核取方塊。 如需詳細資訊，請參閱[在虛擬網路中建立 Azure-SSIS IR](https://docs.microsoft.com/azure/data-factory/create-azure-ssis-integration-runtime)。 
   
   1. 選取 [將自我裝載整合執行階段設定為 Azure-SSIS Integration Runtime 的 Proxy]  核取方塊，來選擇是否要將自我裝載 IR 設定為 Azure-SSIS IR 的 Proxy。 如需詳細資訊，請參閱[將自我裝載 IR 設定為 Proxy](https://docs.microsoft.com/azure/data-factory/self-hosted-integration-runtime-proxy-ssis)。   

   1. 選取 [繼續]  。 

1. 在 [摘要]  區段上，檢閱所有佈建設定、將建議的文件連結設為書籤，然後選取 [完成]  以開始建立您的整合執行階段。 

   > [!NOTE]
   > 若不包含任何自訂的設定時間，此程序應該會在 5 分鐘內完成。
   >
   > 如果您使用 SSISDB，Data Factory 服務將會連線到資料庫伺服器來準備 SSISDB。 
   > 
   > 當您佈建 Azure-SSIS IR 時，也會安裝 Access 可轉散發套件和適用於 SSIS 的 Azure Feature Pack。 除了內建元件已支援的資料來源外，這些元件還可讓您連線到 Excel 檔案、Access 檔案及各種 Azure 資料來源。 如需其他可安裝元件的相關資訊，請參閱 [Azure-SSIS IR 的自訂設定](how-to-configure-azure-ssis-ir-custom-setup.md)。

1. 如有必要，在 [連線]  索引標籤上，切換到 [整合執行階段]  。 選取 [重新整理]  可重新整理狀態。 

   ![建立狀態，以及 [重新整理] 按鈕](./media/tutorial-create-azure-ssis-runtime-portal/azure-ssis-ir-creation-status.png)

1. 使用 [動作]  資料行中的連結來停止/啟動、編輯或刪除整合執行階段。 使用最後一個連結來檢視整合執行階段的 JSON 程式碼。 只有當 IR 停止時，才會啟用編輯和刪除按鈕。 

   ![[動作] 資料行中的連結](./media/tutorial-create-azure-ssis-runtime-portal/azure-ssis-ir-actions.png) 

## <a name="deploy-ssis-packages"></a>部署 SSIS 套件

如果您使用 SSISDB，則可以使用 SQL Server Data Tools (SSDT) 或 SQL Server Management Studio (SSMS) 工具，將套件部署到 SSISDB 中，並在 Azure-SSIS IR 上執行套件。 這些工具在連線至資料庫伺服器時會透過其伺服器端點來進行： 

- 針對 Azure SQL Database 伺服器，伺服器端點的格式為 `<server name>.database.windows.net`。
- 針對具有私人端點的受控執行個體，伺服器端點的格式為 `<server name>.<dns prefix>.database.windows.net`。
- 針對具有公用端點的受控執行個體，伺服器端點的格式為 `<server name>.public.<dns prefix>.database.windows.net,3342`。 

如果您不是使用 SSISDB，您可以使用 `dtinstall`、`dtutil`和 `dtexec` 命令列工具，將套件部署到檔案系統、檔案共用或 Azure 檔案儲存體，並在 Azure-SSIS IR 上執行這些套件。 如需詳細資訊，請參閱[部署 SSIS 套件](/sql/integration-services/packages/deploy-integration-services-ssis-projects-and-packages#deploy-packages-to-integration-services-server)。 

在這兩種狀況中，您也可以使用 Data Factory 管線中的「執行 SSIS 套件」活動，在 Azure-SSIS IR 上執行已部署的套件。 如需詳細資訊，請參閱[以第一級 Data Factory 活動的形式來叫用 SSIS 套件執行](https://docs.microsoft.com/azure/data-factory/how-to-invoke-ssis-package-ssis-activity)。

另請參閱下列 SSIS 文件： 

- [在 Azure 中部署、執行和監視 SSIS 套件](/sql/integration-services/lift-shift/ssis-azure-deploy-run-monitor-tutorial) 
- [連線至 Azure 中的 SSISDB](/sql/integration-services/lift-shift/ssis-azure-connect-to-catalog-database) 
- [在 Azure 中排程套件執行](/sql/integration-services/lift-shift/ssis-azure-schedule-packages) 
- [透過 Windows 驗證連線至內部部署資料來源](/sql/integration-services/lift-shift/ssis-azure-connect-with-windows-auth) 

## <a name="next-steps"></a>後續步驟

若要了解如何自訂 Azure-SSIS 整合執行階段，請前往下列文章： 

> [!div class="nextstepaction"]
> [自訂 Azure-SSIS IR](https://docs.microsoft.com/azure/data-factory/how-to-configure-azure-ssis-ir-custom-setup)