---
title: 在 Azure Data Factory 中建立 Azure-SSIS 整合執行階段
description: 了解如何在 Azure Data Factory 中建立 Azure-SSIS 整合執行階段，您就可以在 Azure 部署並執行 SSIS 套件。
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 03/27/2020
author: swinarko
ms.author: sawinark
ms.reviewer: douglasl
manager: mflasko
ms.openlocfilehash: 18555fbffbc48594793163894c010998094b3b59
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80336236"
---
# <a name="create-an-azure-ssis-integration-runtime-in-azure-data-factory"></a>在 Azure Data Factory 中建立 Azure-SSIS 整合執行階段

本文提供了在 Azure 資料工廠中預配 Azure-SQL 伺服器整合服務 （SSIS） 集成運行時 （IR） 的步驟。 Azure-SSIS IR 可支援：

- 執行已部署到 SSIS 目錄 (SSISDB) 的套件，此目錄則由 Azure SQL Database 伺服器或受控執行個體 (專案部署模型) 裝載。
- 執行已部署到檔案系統、檔案共用或 Azure 檔案儲存體 (套件部署模型) 的套件。 

佈建 Azure-SSIS IR 之後，您就可以使用熟悉的工具在 Azure 中部署和執行套件。 這些工具包括 SQL Server Data Tools (SSDT)、SQL Server Management Studio (SSMS) 和命令列工具，例如 `dtinstall`、`dtutil` 和 `dtexec`。

預配[Azure-SSIS IR](tutorial-create-azure-ssis-runtime-portal.md)教程演示如何通過 Azure 門戶或資料工廠應用創建 Azure-SSIS IR。 本教程還演示如何選擇使用 Azure SQL 資料庫伺服器或託管實例來託管 SSISDB。 本文將介紹本教程，並介紹如何執行這些可選任務：

- 使用具有 IP 防火牆規則/虛擬網路服務終結點的 Azure SQL 資料庫伺服器，或者使用專用終結點的託管實例來承載 SSISDB。 作為先決條件，您需要為 Azure-SSIS IR 配置虛擬網路許可權和設置以加入虛擬網路。

- 使用 Azure 活動目錄 （Azure AD） 身份驗證與資料工廠的託管標識連接到 Azure SQL 資料庫伺服器或託管實例。 作為先決條件，您需要將資料工廠的託管標識添加為可以創建 SSISDB 實例的資料庫使用者。

- 將 Azure-SSIS IR 加入虛擬網路，或配置自承載的 IR 作為 Azure-SSIS IR 的代理，以在本地訪問資料。

本文演示如何使用 Azure 門戶、Azure PowerShell 和 Azure 資源管理器範本預配 Azure-SSIS IR。

## <a name="prerequisites"></a>Prerequisites

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

- **Azure 訂閱**。 如果您還沒有訂閱，您可以創建[免費試用](https://azure.microsoft.com/pricing/free-trial/)帳戶。

- **Azure SQL 資料庫伺服器或託管實例（可選）。** 如果您還沒有資料庫伺服器，請在 Azure 入口網站中建立一個，然後再開始。 Data Factory 接著會在此資料庫伺服器上建立 SSISDB 執行個體。 

  建議於整合執行階段所在的相同 Azure 區域中建立資料庫伺服器。 此設定可讓整合執行階段將執行記錄寫入 SSISDB，而不需要跨 Azure 區域。

  請記住下列重點：

  - 根據所選的資料庫伺服器，系統可以代表您將 SSISDB 執行個體建立為單一資料庫、建立為彈性集區的一部分，或建立在受控執行個體中。 該執行個體可以在公用網路中供您存取，而您也可以藉由加入虛擬網路來加以存取。 有關選擇承載 SSISDB 的資料庫伺服器類型的指南，請參閱本文中的["比較 Azure SQL 資料庫單個資料庫、彈性池和託管實例](#comparison-of-a-sql-database-single-database-elastic-pool-and-managed-instance)"部分。 
  
    如果您使用具有 IP 防火牆規則/虛擬網路服務端點的 Azure SQL Database 伺服器或具有私人端點的受控執行個體來裝載 SSISDB，或您需要在不設定自我裝載 IR 的情況下存取內部部署資料，則必須將 Azure-SSIS IR 加入虛擬網路。 有關詳細資訊，請參閱將[Azure-SSIS IR 加入虛擬網路](https://docs.microsoft.com/azure/data-factory/join-azure-ssis-integration-runtime-virtual-network)。

  - 確認資料庫伺服器的 [允許存取 Azure 服務]**** 設定已啟用。 如果您使用具有IP 防火牆規則/虛擬網路服務端點的 Azure SQL Database 伺服器或具有私人端點的受控執行個體來裝載 SSISDB，則不適用此設定。 如需詳細資訊，請參閱[保護 Azure SQL 資料庫資料庫](../sql-database/sql-database-security-tutorial.md#create-firewall-rules)。 若要使用 PowerShell 來啟用此設定，請參閱 [New-AzSqlServerFirewallRule](/powershell/module/az.sql/new-azsqlserverfirewallrule)。

  - 新增用戶端電腦的 IP 位址，或新增 IP 位址範圍，其中包含資料庫伺服器之防火牆設定中用戶端電腦 IP 位址到用戶端 IP 位址清單。 如需詳細資訊，請參閱 [Azure SQL Database 伺服器層級和資料庫層級防火牆規則](../sql-database/sql-database-firewall-configure.md)

  - 若要連線至資料庫伺服器，您可以使用 SQL 驗證搭配伺服器管理員認證，也可以使用 Azure AD 驗證搭配資料處理站的受控識別。 若為後者，您必須將資料處理站的受控識別新增至具有資料庫伺服器存取權限的 Azure AD 群組中。 有關詳細資訊，請參閱為[Azure-SSIS IR 啟用 Azure AD 身份驗證](https://docs.microsoft.com/azure/data-factory/enable-aad-authentication-azure-ssis-ir)。

  - 請確認您的資料庫伺服器尚未有 SSISDB 執行個體。 Azure-SSIS IR 的佈建不支援使用現有的 SSISDB 執行個體。

- **Azure Resource Manager 虛擬網路 (選擇性)**。 如果下列至少一個條件成立，您就必須具備 Azure Resource Manager 虛擬網路：
  - 您託管 SSISDB 在 Azure SQL 資料庫伺服器上，該伺服器具有 IP 防火牆規則/虛擬網路服務終結點，或者託管具有專用終結點的實例。
  - 您希望從 Azure-SSIS IR 上運行的 SSIS 包連接到本地資料存儲，而無需配置自承載的 IR。

- **Azure 電源外殼（可選）**。 如果您想要執行 PowerShell 指令碼來佈建 Azure-SSIS IR，請遵循[如何安裝和設定 Azure PowerShell](/powershell/azure/install-az-ps) 中的指示。

### <a name="regional-support"></a>區域支援

有關資料工廠和 Azure-SSIS IR 可用的 Azure 區域清單，請參閱[資料工廠和 SSIS IR 可用性（按區域）。](https://azure.microsoft.com/global-infrastructure/services/?products=data-factory&regions=all)

### <a name="comparison-of-a-sql-database-single-database-elastic-pool-and-managed-instance"></a>比較 SQL 資料庫單個資料庫、彈性池和託管實例

下表比較了 Azure SQL 資料庫伺服器和託管實例與 Azure-SSIR IR 相關的某些功能：

| 功能 | 單個資料庫/彈性池| 受控執行個體 |
|---------|--------------|------------------|
| **調度** | SQL 伺服器代理不可用。<br/><br/>請參閱[在資料工廠管道中安排包執行](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-schedule-packages?view=sql-server-2017#activity)。| 託管實例代理可用。 |
| [驗證]**** | 可以使用包含的資料庫使用者創建 SSISDB 實例，該使用者表示任何 Azure AD 組，該組具有資料工廠的託管標識，作為**db_owner**角色的成員。<br/><br/>請參閱[啟用 Azure AD 身份驗證以在 Azure SQL 資料庫伺服器上創建 SSISDB 實例](enable-aad-authentication-azure-ssis-ir.md#enable-azure-ad-on-azure-sql-database)。 | 您可以使用表示資料工廠的託管標識的包含資料庫使用者的 SSISDB 實例創建。 <br/><br/>請參閱[啟用 Azure AD 身份驗證以在 Azure SQL 資料庫託管實例中創建 SSISDB 實例](enable-aad-authentication-azure-ssis-ir.md#enable-azure-ad-on-azure-sql-database-managed-instance)。 |
| **服務層** | 使用 Azure SQL 資料庫伺服器創建 Azure-SSIS IR 時，可以為 SSISDB 選擇服務層。 目前有多個服務層級。 | 使用託管實例創建 Azure-SSIS IR 時，無法為 SSISDB 選擇服務層。 託管實例中的所有資料庫共用分配給該實例的相同資源。 |
| **虛擬網路** | 如果使用具有 IP 防火牆規則/虛擬網路服務終結點的 Azure SQL 資料庫伺服器，則 Azure-SSIS IR 可以加入 Azure 資源管理器虛擬網路。 | 如果將託管實例與專用終結點一起使用，則 Azure-SSIS IR 可以加入 Azure 資源管理器虛擬網路。 當您未為託管實例啟用公共終結點時，需要虛擬網路。<br/><br/>如果將 Azure-SSIS IR 加入與託管實例相同的虛擬網路，請確保 Azure-SSIS IR 與託管實例位於不同的子網中。 如果將 Azure-SSIS IR 連接到與託管實例不同的虛擬網路，我們建議使用虛擬網路對等互連或網路到網路連接。 請參閱[將應用程式連接到 Azure SQL 資料庫託管實例](../sql-database/sql-database-managed-instance-connect-app.md)。 |
| **分散式交易** | 此功能通過彈性事務支援。 不支援 Microsoft Distributed Transaction Coordinator (MSDTC) 交易。 如果 SSIS 包使用 MSDTC 協調分散式交易，請考慮遷移到 Azure SQL 資料庫的彈性事務。 有關詳細資訊，請參閱[跨雲資料庫的分散式交易](../sql-database/sql-database-elastic-transactions-overview.md)。 | 不支援。 |
| | | |

## <a name="use-the-azure-portal-to-create-an-integration-runtime"></a>使用 Azure 門戶創建集成運行時

在本節中，您可以使用 Azure 門戶（特別是資料工廠使用者介面 （UI） 或應用來創建 Azure-SSIS IR。

### <a name="create-a-data-factory"></a>建立 Data Factory

若要透過 Azure 入口網站來建立資料處理站，請遵循[透過 UI 來建立資料處理站](https://docs.microsoft.com/azure/data-factory/quickstart-create-data-factory-portal#create-a-data-factory)中的逐步指示。 遵循指示操作時請選取 [釘選到儀表板]****，以便能快速存取建立好的資料處理站。 

資料處理站建立好之後，請在 Azure 入口網站中開啟其 [概觀] 頁面。 選擇 **"作者&監視器"** 磁貼以在單獨的選項卡上打開其 **"讓我們開始"** 頁面。在那裡，您可以繼續創建 Azure-SSIS IR。   

### <a name="provision-an-azure-ssis-integration-runtime"></a>佈建 Azure-SSIS 整合執行階段

1. 在 [讓我們開始吧]**** 頁面上，選取 [設定 SSIS 整合執行階段]**** 圖格。

   ![設定 SSIS 整合執行階段圖格](./media/tutorial-create-azure-ssis-runtime-portal/configure-ssis-integration-runtime-tile.png)

1. 在 [整合執行階段設定]**** 面板的 [一般設定]**** 區段上，完成下列步驟。

   ![一般設定](./media/tutorial-create-azure-ssis-runtime-portal/general-settings.png)

   1. 針對 [名稱]****，輸入取您整合執行階段的名稱。

   1. 針對 [描述]****，輸入整合執行階段的描述。

   1. 針對 [位置]****，選取整合執行階段的位置。 系統只會顯示支援的位置。 我們建議您選取裝載 SSISDB 的資料庫伺服器所在位置。

   1. 對於**節點大小**，選擇集成運行時群集中節點的大小。 系統只會顯示支援的節點大小。 如果您想要執行許多計算密集或記憶體密集的套件，請選取大型節點大小 (相應增加)。

   1. 針對 [節點數目]****，選取整合執行階段叢集中的節點數目。 系統只會顯示支援的節點數目。 如果您想要平行執行許多套件，請選取具有許多節點的大型叢集 (相應放大)。

   1. 對於**版本/許可證**，為集成運行時選擇 SQL Server 版本：標準版或企業版。 如果您想要在整合執行階段上使用進階功能，請選取 [企業版]。

   1. 為了**節省資金**，為集成運行時選擇 Azure 混合權益選項：**是**或**否**。 如果要將自己的 SQL Server 許可證與軟體保證一起帶來，請選擇 **"是**"，以便通過混合使用節省成本。

   1. 選取 [下一步]****。

1. 在 [SQL 設定]**** 階段上，完成下列步驟。

   ![SQL 設定](./media/tutorial-create-azure-ssis-runtime-portal/sql-settings.png)

   1. 選取 [建立由 Azure SQL Database 伺服器/受控執行個體裝載的 SSIS 目錄 (SSISDB) 來儲存您的專案/套件/環境/執行記錄]**** 核取方塊，為您 Azure-SSIS IR 上執行的套件選擇部署模型。 您可以選擇專案部署模型 (會將套件部署到資料庫伺服器所裝載的 SSISDB)，也可以選擇套件部署模型 (會將套件部署到檔案系統、檔案共用或 Azure 檔案儲存體)。 
    
      如果選擇此核取方塊，則需要自帶資料庫伺服器來託管我們將代表您創建和管理的 SSISDB 實例。
   
      1. 針對 [訂用帳戶]****，選取具有資料庫伺服器可裝載 SSISDB 的 Azure 訂用帳戶。 

      1. 針對 [位置]****，選取裝載 SSISDB 的資料庫伺服器所在位置。 我們建議您選取整合執行階段所在的相同位置。 

      1. 針對 [目錄資料庫伺服器端點]****，選取裝載 SSISDB 的資料庫伺服器端點。 
    
         根據所選的資料庫伺服器，系統可以代表您將 SSISDB 執行個體建立為單一資料庫、建立為彈性集區的一部分，或建立在受控執行個體中。 該執行個體可以在公用網路中供您存取，而您也可以藉由加入虛擬網路來加以存取。 有關選擇承載 SSISDB 的資料庫伺服器類型的指南，請參閱本文中的["比較 Azure SQL 資料庫單個資料庫、彈性池和託管實例](#comparison-of-a-sql-database-single-database-elastic-pool-and-managed-instance)"部分。 
    
         如果您選取具有 IP 防火牆規則/虛擬網路服務端點的 Azure SQL Database 伺服器或具有私人端點的受控執行個體來裝載 SSISDB，或您需要在不設定自我裝載 IR 的情況下存取內部部署資料，則必須將 Azure-SSIS IR 加入虛擬網路。 有關詳細資訊，請參閱將[Azure-SSIS IR 加入虛擬網路](https://docs.microsoft.com/azure/data-factory/join-azure-ssis-integration-runtime-virtual-network)。 

      1. 選取 [使用 AAD 驗證搭配 ADF 的受控識別]**** 核取方塊，來為要裝載 SSISDB 的資料庫伺服器選擇驗證方法。 您可以選擇 SQL 驗證或 Azure AD 驗證來搭配資料處理站的受控識別。 
    
         如果您選取該核取方塊，則必須將資料處理站的受控識別新增至具有資料庫伺服器存取權限的 Azure AD 群組中。 有關詳細資訊，請參閱為[Azure-SSIS IR 啟用 Azure AD 身份驗證](https://docs.microsoft.com/azure/data-factory/enable-aad-authentication-azure-ssis-ir)。 

      1. 針對 [管理使用者名稱]****，為裝載 SSISDB 的資料庫伺服器輸入 SQL 驗證使用者名稱。 

      1. 針對 [管理密碼]****，為裝載 SSISDB 的資料庫伺服器輸入 SQL 驗證密碼。 

      1. 針對 [目錄資料庫服務層級]****，選取您資料庫伺服器用來裝載 SSISDB 的服務層級。 選取 [基本]、[標準] 或 [進階] 層，或選取彈性集區名稱。 

      1. 選擇**測試連接**。 如果測試成功，請選取 [下一步]****。 

1. 在 [進階設定]**** 階段上，完成下列步驟。

   ![進階設定](./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings.png)

   1. 針對 [每節點的平行執行數上限]****，選取執行階段叢集中每節點上可同時執行的套件數量上限。 系統只會顯示支援的套件數目。 如果您想要使用多個核心來執行計算密集或記憶體密集的單一大型套件，請選取較低的數字。 如果您想要在單一核心中執行一或多個小型套件，請選取較高的數字。

   1. 選取 [使用額外的系統設定/元件安裝來自訂您的 Azure-SSIS Integration Runtime]**** 核取方塊，選擇是否要在您的 Azure-SSIS IR 上新增標準/快速自訂設定。 如需詳細資訊，請參閱 [Azure-SSIS IR 的自訂設定](https://docs.microsoft.com/azure/data-factory/how-to-configure-azure-ssis-ir-custom-setup)。

      如果選擇該核取方塊，請完成以下步驟。

      ![具有自訂設置的高級設置](./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings-custom.png)
   
      1. 對於**自訂設置容器 SAS URI，** 輸入容器的 SAS URI，您可以在其中存儲用於標準自訂設置的腳本和相關檔。

      1. 對於**快速自訂設置**，選擇 **"新建**"以打開 **"添加快速自訂設置**面板"，然後在 **"快速自訂設置類型**"下拉式功能表下選擇任何類型，例如**運行 cmdkey 命令**、**添加環境變數**、**安裝許可元件**等。

         如果您選擇 **"安裝許可元件**類型"，則可以在 **"元件名稱**下拉式功能表"下從我們的 ISV 合作夥伴中選擇任何集成元件，如果需要，請在 **"許可證"金鑰**欄位中輸入從他們處購買的產品許可證金鑰。
  
         您添加的快速自訂設置將顯示在 **"高級設置"** 部分。 要刪除它們，可以選擇它們的核取方塊，然後選擇 **"刪除**"。

   1. 選擇**Azure-SSIS 集成運行時的 VNet，允許 ADF 創建某些網路資源，並選擇自帶靜態公共 IP 位址**核取方塊以選擇是否要將集成運行時加入虛擬網路。 

      如果使用具有 IP 防火牆規則/虛擬網路服務終結點的 Azure SQL 資料庫伺服器或具有專用終結點的託管實例來承載 SSISDB，或者需要訪問本地資料（即 SSIS 包中具有本地資料來源或目標），而無需配置自承載 IR，請選擇它。 有關詳細資訊，請參閱將[Azure-SSIS IR 加入虛擬網路](https://docs.microsoft.com/azure/data-factory/join-azure-ssis-integration-runtime-virtual-network)。 

      如果選擇該核取方塊，請完成以下步驟。

      ![虛擬網路進階設定](./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings-vnet.png)

      1. 針對 [訂用帳戶]****，選取具有您的虛擬網路的 Azure 訂用帳戶。

      1. 針對 [位置]****，選取整合執行階段的相同位置。

      1. 對於**類型**，請選擇虛擬網路的類型：經典或 Azure 資源管理器。 我們建議您選擇 Azure 資源管理器虛擬網路，因為經典虛擬網路將很快被棄用。

      1. 針對 **[VNet 名稱]**，選取您虛擬網路的名稱。 它應該與 Azure SQL 資料庫伺服器使用的相同，該伺服器具有虛擬網路服務終結點，或者使用專用終結點託管 SSISDB 的託管實例。 或者它應該是連接到本地網路的同一個。 否則，任何虛擬網路都可以為 Azure-SSIS IR 帶來自己的靜態公共 IP 位址。

      1. 針對 [子網路名稱]****，選取虛擬網路的子網路名稱。 它應該與 Azure SQL 資料庫伺服器所用的相同，該伺服器具有虛擬網路服務終結點來承載 SSISDB。 或者，它應該是一個不同的子網，與用於託管實例的子網，具有專用終結點來承載 SSISDB。 否則，它可以是任何子網，為 Azure-SSIS IR 自帶靜態公共 IP 位址。

      1. 選擇 **"為 Azure-SSIS 集成運行時帶來靜態公共 IP 位址**"核取方塊，以選擇是否要為 Azure-SSIS IR 攜帶自己的靜態公共 IP 位址，以便允許在資料來源的防火牆上設置這些位址。

         如果選擇該核取方塊，請完成以下步驟。

         1. 對於**第一個靜態公共 IP 位址**，選擇滿足 Azure-SSIS IR 要求的第一個靜態公共 IP 位址。 如果沒有，請按一下"**創建新**連結"在 Azure 門戶上創建靜態公共 IP 位址，然後按一下此處的刷新按鈕，以便選擇這些位址。
      
         1. 對於**第二個靜態公共 IP 位址**，選擇第二個滿足 Azure-SSIS IR 要求的靜態公共 IP 位址。 如果沒有，請按一下"**創建新**連結"在 Azure 門戶上創建靜態公共 IP 位址，然後按一下此處的刷新按鈕，以便選擇這些位址。

   1. 選取 [將自我裝載整合執行階段設定為 Azure-SSIS Integration Runtime 的 Proxy]**** 核取方塊，來選擇是否要將自我裝載 IR 設定為 Azure-SSIS IR 的 Proxy。 如需詳細資訊，請參閱[將自我裝載 IR 設定為 Proxy](https://docs.microsoft.com/azure/data-factory/self-hosted-integration-runtime-proxy-ssis)。 

      如果選擇該核取方塊，請完成以下步驟。

      ![具有自承載 IR 的高級設置](./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings-shir.png)

      1. 對於**自託管集成運行時**，選擇現有的自託管 IR 作為 Azure-SSIS IR 的代理。

      1. 對於**暫存存儲連結服務**，請選擇現有的 Azure Blob 存儲連結服務或創建新的暫存服務。

      1. 對於**暫存路徑**，在選定的 Azure Blob 存儲帳戶中指定 Blob 容器，或將其留空以使用預設容器進行暫存。

   1. 選擇 **"繼續 VNet 驗證** > **"。** 

1. 在 [摘要]**** 區段上，檢閱所有佈建設定、將建議的文件連結設為書籤，然後選取 [完成]**** 以開始建立您的整合執行階段。

   > [!NOTE]
   > 若不包含任何自訂的設定時間，此程序應該會在 5 分鐘內完成。 但是，Azure-SSIS IR 可能需要 20-30 分鐘才能加入虛擬網路。
   >
   > 如果您使用 SSISDB，Data Factory 服務將會連線到資料庫伺服器來準備 SSISDB。 它還配置虛擬網路的許可權和設置（如果指定），並將 Azure-SSIS IR 加入虛擬網路。
   > 
   > 當您佈建 Azure-SSIS IR 時，也會安裝 Access 可轉散發套件和適用於 SSIS 的 Azure Feature Pack。 除了內建元件已支援的資料來源外，這些元件還可讓您連線到 Excel 檔案、Access 檔案及各種 Azure 資料來源。 如需其他可安裝元件的相關資訊，請參閱 [Azure-SSIS IR 的自訂設定](how-to-configure-azure-ssis-ir-custom-setup.md)。

1. 如有必要，在 [連線]**** 索引標籤上，切換到 [整合執行階段]****。 選取 [重新整理]**** 可重新整理狀態。

   ![建立狀態](./media/tutorial-create-azure-ssis-runtime-portal/azure-ssis-ir-creation-status.png)

1. 使用 [動作]**** 資料行中的連結來停止/啟動、編輯或刪除整合執行階段。 使用最後一個連結來檢視整合執行階段的 JSON 程式碼。 只有當 IR 停止時，才會啟用編輯和刪除按鈕。

   ![Azure SSIS IR 操作](./media/tutorial-create-azure-ssis-runtime-portal/azure-ssis-ir-actions.png)

### <a name="azure-ssis-integration-runtimes-in-the-portal"></a>入口網站中的 Azure SSIS 整合執行階段

1. 在 Azure Data Factory UI 中，切換至 [編輯]**** 索引標籤，然後選取 [連線]****。 然後切換至 [整合執行階段]**** 索引標籤，以在資料處理站中檢視現有的整合執行階段。

   ![檢視現有的 IR](./media/tutorial-create-azure-ssis-runtime-portal/view-azure-ssis-integration-runtimes.png)

1. 選擇 **"新建**"以創建新的 Azure-SSIS IR。

   ![整合執行階段功能表](./media/tutorial-create-azure-ssis-runtime-portal/edit-connections-new-integration-runtime-button.png)

1. 在 [整合執行階段設定]**** 面板中，選取 [隨即轉移現有的 SSIS 套件以在 Azure 中執行]**** 圖格，然後選取 [下一步]****。

   ![指定整合執行階段的類型](./media/tutorial-create-azure-ssis-runtime-portal/integration-runtime-setup-options.png)

1. 有關設置 Azure-SSIS IR 的其餘步驟，請參閱[預配 Azure SSIS 集成運行時](#provision-an-azure-ssis-integration-runtime)部分。

## <a name="use-azure-powershell-to-create-an-integration-runtime"></a>使用 Azure PowerShell 創建集成運行時

在本節中，您可以使用 Azure PowerShell 創建 Azure-SSIS IR。

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
$ExpressCustomSetup = "[RunCmdkey|SetEnvironmentVariable|SentryOne.TaskFactory|oh22is.SQLPhonetics.NET|oh22is.HEDDA.IO|KingswaySoft.IntegrationToolkit|KingswaySoft.ProductivityPack|Theobald.XtractIS or leave it empty]" # OPTIONAL to configure an express custom setup without script
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

### <a name="sign-in-and-select-a-subscription"></a>登錄並選擇訂閱

添加以下腳本以登錄並選擇 Azure 訂閱。

```powershell
Connect-AzAccount
Select-AzSubscription -SubscriptionName $SubscriptionName
```

### <a name="validate-the-connection-to-database-server"></a>驗證資料庫伺服器的連線

添加以下腳本以驗證 Azure SQL 資料庫伺服器或託管實例。

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

添加以下腳本以自動設定要加入的 Azure-SSIS 集成運行時的虛擬網路許可權和設置。

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

如果不使用 SSISDB，則可以省略`CatalogServerEndpoint`和`CatalogPricingTier``CatalogAdminCredential`參數。

如果不將 Azure SQL 資料庫伺服器與 IP 防火牆規則/虛擬網路服務終結點一起使用，或者使用專用終結點託管 SSISDB 的託管實例，或者需要訪問本地資料，則可以省略`VNetId`和`Subnet`參數或為其傳遞空值。 如果將自承載的 IR 配置為 Azure-SSIS IR 的代理以在本地訪問資料，也可以省略它們。 否則，您無法省略它們，並且必須從虛擬網路配置傳遞有效值。 有關詳細資訊，請參閱將[Azure-SSIS IR 加入虛擬網路](https://docs.microsoft.com/azure/data-factory/join-azure-ssis-integration-runtime-virtual-network)。

如果使用託管實例來承載 SSISDB，則可以省略`CatalogPricingTier`該參數或為其傳遞空值。 否則，您不能省略它，並且必須從 Azure SQL 資料庫受支援的定價層清單中傳遞有效值。 有關詳細資訊，請參閱[SQL 資料庫資源限制](../sql-database/sql-database-resource-limits.md)。

如果將 Azure AD 身份驗證與資料工廠的託管標識一起連接到資料庫伺服器，則可以省略該`CatalogAdminCredential`參數。 但是，您必須將資料工廠的託管標識添加到具有資料庫伺服器存取權限的 Azure AD 組中。 有關詳細資訊，請參閱為[Azure-SSIS IR 啟用 Azure AD 身份驗證](https://docs.microsoft.com/azure/data-factory/enable-aad-authentication-azure-ssis-ir)。 否則，您無法省略它，並且必須傳遞從伺服器管理員使用者名和密碼中形成的有效物件進行 SQL 身份驗證。

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

### <a name="start-the-integration-runtime"></a>啟動集成運行時

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
> 若不包含任何自訂的設定時間，此程序應該會在 5 分鐘內完成。 但是，Azure-SSIS IR 可能需要 20-30 分鐘才能加入虛擬網路。
>
> 如果您使用 SSISDB，Data Factory 服務將會連線到資料庫伺服器來準備 SSISDB。 它還配置虛擬網路的許可權和設置（如果指定），並將 Azure-SSIS IR 加入虛擬網路。
> 
> 當您佈建 Azure-SSIS IR 時，也會安裝 Access 可轉散發套件和適用於 SSIS 的 Azure Feature Pack。 除了內建元件已支援的資料來源外，這些元件還可讓您連線到 Excel 檔案、Access 檔案及各種 Azure 資料來源。 如需其他可安裝元件的相關資訊，請參閱 [Azure-SSIS IR 的自訂設定](how-to-configure-azure-ssis-ir-custom-setup.md)。

### <a name="full-script"></a>完整指令碼

下面是創建 Azure-SSIS 集成運行時的完整腳本。

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
$ExpressCustomSetup = "[RunCmdkey|SetEnvironmentVariable|SentryOne.TaskFactory|oh22is.SQLPhonetics.NET|oh22is.HEDDA.IO|KingswaySoft.IntegrationToolkit|KingswaySoft.ProductivityPack|Theobald.XtractIS or leave it empty]" # OPTIONAL to configure an express custom setup without script
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

## <a name="use-an-azure-resource-manager-template-to-create-an-integration-runtime"></a>使用 Azure 資源管理器範本創建集成運行時

在本節中，使用 Azure 資源管理器範本創建 Azure-SSIS 集成運行時。 下面是一個示例演練：

1. 使用下列 Azure Resource Manager 範本建立 JSON 檔案。 將角括弧（預留位置）中的值替換為您自己的值。

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

2. 要部署 Azure 資源管理器範本，運行命令`New-AzResourceGroupDeployment`，如以下示例所示。 在此示例中，`ADFTutorialResourceGroup`是資源組的名稱。 `ADFTutorialARM.json`是包含資料工廠和 Azure-SSIS IR 的 JSON 定義的檔。

    ```powershell
    New-AzResourceGroupDeployment -Name MyARMDeployment -ResourceGroupName ADFTutorialResourceGroup -TemplateFile ADFTutorialARM.json
    ```

    此命令在其中創建資料工廠和 Azure-SSIS IR，但不會啟動 IR。

3. 要啟動 Azure-SSIS IR，請`Start-AzDataFactoryV2IntegrationRuntime`運行以下命令：

    ```powershell
    Start-AzDataFactoryV2IntegrationRuntime -ResourceGroupName "<Resource Group Name>" `
        -DataFactoryName "<Data Factory Name>" `
        -Name "<Azure SSIS IR Name>" `
        -Force
    ```

> [!NOTE]
> 若不包含任何自訂的設定時間，此程序應該會在 5 分鐘內完成。 但是，Azure-SSIS IR 可能需要 20-30 分鐘才能加入虛擬網路。
>
> 如果您使用 SSISDB，Data Factory 服務將會連線到資料庫伺服器來準備 SSISDB。 它還配置虛擬網路的許可權和設置（如果指定），並將 Azure-SSIS IR 加入虛擬網路。
> 
> 當您佈建 Azure-SSIS IR 時，也會安裝 Access 可轉散發套件和適用於 SSIS 的 Azure Feature Pack。 除了內建元件已支援的資料來源外，這些元件還可讓您連線到 Excel 檔案、Access 檔案及各種 Azure 資料來源。 如需其他可安裝元件的相關資訊，請參閱 [Azure-SSIS IR 的自訂設定](how-to-configure-azure-ssis-ir-custom-setup.md)。

## <a name="deploy-ssis-packages"></a>部署 SSIS 套件

如果使用 SSISDB，則可以使用 SQL 伺服器資料工具 （SSDT） 或 SQL 伺服器管理工作室 （SSMS） 工具將包部署到其中並在 Azure-SSIS IR 上運行它們。 這些工具在連線至資料庫伺服器時會透過其伺服器端點來進行： 

- 針對 Azure SQL Database 伺服器，伺服器端點的格式為 `<server name>.database.windows.net`。
- 針對具有私人端點的受控執行個體，伺服器端點的格式為 `<server name>.<dns prefix>.database.windows.net`。
- 針對具有公用端點的受控執行個體，伺服器端點的格式為 `<server name>.public.<dns prefix>.database.windows.net,3342`。 

如果不使用 SSISDB，則可以將包部署到檔案系統、檔共用或 Azure 檔，並使用`dtinstall`和`dtutil``dtexec`命令列工具在 Azure-SSIS IR 上運行它們。 如需詳細資訊，請參閱[部署 SSIS 套件](/sql/integration-services/packages/deploy-integration-services-ssis-projects-and-packages#deploy-packages-to-integration-services-server)。 

在這兩種狀況中，您也可以使用 Data Factory 管線中的「執行 SSIS 套件」活動，在 Azure-SSIS IR 上執行已部署的套件。 如需詳細資訊，請參閱[以第一級 Data Factory 活動的形式來叫用 SSIS 套件執行](https://docs.microsoft.com/azure/data-factory/how-to-invoke-ssis-package-ssis-activity)。

## <a name="next-steps"></a>後續步驟

請參閱本文檔中的其他 Azure-SSIS IR 主題：

- [Azure-SSIS 集成運行時](concepts-integration-runtime.md#azure-ssis-integration-runtime)。 本文提供有關集成運行時的資訊，包括 Azure-SSIS IR。
- [監視 Azure-SSIS IR](monitor-integration-runtime.md#azure-ssis-integration-runtime). 本文介紹如何檢索和理解有關 Azure-SSIS IR 的資訊。
- [管理 Azure-SSIS IR](manage-azure-ssis-integration-runtime.md). 本文介紹如何停止、啟動或刪除 Azure-SSIS IR。 它還演示如何通過添加更多節點來橫向擴展 Azure-SSIS IR。
- [在 Azure 中部署、執行和監視 SSIS 套件](/sql/integration-services/lift-shift/ssis-azure-deploy-run-monitor-tutorial)   
- [連線至 Azure 中的 SSISDB](/sql/integration-services/lift-shift/ssis-azure-connect-to-catalog-database)
- [透過 Windows 驗證連線至內部部署資料來源](/sql/integration-services/lift-shift/ssis-azure-connect-with-windows-auth) 
- [在 Azure 中排程套件執行](/sql/integration-services/lift-shift/ssis-azure-schedule-packages)