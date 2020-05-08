---
title: 如何排程 Azure SSIS Integration Runtime
description: 本文說明如何使用 Azure Data Factory 來排程 Azure-SSIS Integration Runtime 的啟動和停止。
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: ''
ms.devlang: powershell
ms.topic: conceptual
ms.date: 8/2/2019
author: swinarko
ms.author: sawinark
ms.reviewer: douglasl
manager: anandsub
ms.openlocfilehash: 83ccc3160ed62a1ea801dd8c5795328fd2b5109f
ms.sourcegitcommit: b9d4b8ace55818fcb8e3aa58d193c03c7f6aa4f1
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/29/2020
ms.locfileid: "82584014"
---
# <a name="how-to-start-and-stop-azure-ssis-integration-runtime-on-a-schedule"></a>如何按照排程來啟動和停止 Azure-SSIS Integration Runtime

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

本文說明如何使用 Azure Data Factory (ADF) 來排程 Azure-SSIS Integration Runtime (IR) 的啟動和停止。 Azure-SSIS IR 是專門用來執行 SQL Server Integration Services (SSIS) 套件的 ADF 計算資源。 執行 Azure-SSIS IR 會有相關聯的成本。 因此，您通常只應在需要於 Azure 中執行 SSIS 套件時才執行 IR，不再需要時即應停止 IR。 您可以使用 ADF 使用者介面 (UI)/應用程式或 Azure PowerShell，[以手動方式啟動或停止 IR](manage-azure-ssis-integration-runtime.md)。

或者，您也可以在 ADF 管線中建立 Web 活動，以按照排程來啟動/停止 IR，例如在早上執行每日 ETL 工作負載之前將其啟動，然後在下午執行完工作負載後將其停止。  您也可以在負責啟動和停止 IR 的兩個 Web 活動之間鏈結「執行 SSIS 套件」活動，讓 IR 可以在執行套件之前/之後及時依需求啟動/停止。 如需有關「執行 SSIS 套件」活動的詳細資訊，請參閱[在 ADF 管線中使用執行 SSIS 套件活動來執行 SSIS 套件](how-to-invoke-ssis-package-ssis-activity.md)一文。

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Prerequisites
如果您尚未佈建 Azure-SSIS IR，請遵循[教學課程](tutorial-create-azure-ssis-runtime-portal.md)中的指示加以佈建。 

## <a name="create-and-schedule-adf-pipelines-that-start-and-or-stop-azure-ssis-ir"></a>建立和排程會啟動和/或停止 Azure-SSIS IR 的 ADF 管線
本節將說明如何在 ADF 管線中使用 Web 活動，來按照排程啟動/停止 Azure-SSIS IR 或依需求將其啟動和停止。 我們會引導您建立三個管線： 

1. 第一個管線包含用來啟動 Azure-SSIS IR 的 Web 活動。 
2. 第二個管線包含用來停止 Azure-SSIS IR 的 Web 活動。
3. 第三個管線包含「執行 SSIS 套件」活動，此活動鏈結於用來啟動/停止 Azure-SSIS IR 的兩個 Web 活動之間。 

在建立並測試這些管線後，您可以建立排程觸發程序，並使其與任何管線產生關聯。 排程觸發程序會定義用來執行相關聯管線的排程。 

例如，您可以建立兩個觸發程序，讓第一個觸發程序排定在每日上午 6 點執行，並與第一個管線相關聯，第二個觸發程序則排定在每天下午 6 點執行，並與第二個管線相關聯。  如此一來，您每天就會在 IR 執行時，有一段從上午 6 點到下午 6 點的期間隨時準備好可執行每日的 ETL 工作負載。  

如果您建立第三個觸發程序，使其排定在每天午夜執行並與第三個管線相關聯，該管線就會在每天午夜執行，於執行套件前一刻啟動 IR，隨後執行套件，再於執行套件後立即停止 IR，讓 IR 不會漫無目的地執行。

### <a name="create-your-adf"></a>建立 ADF

1. 登入 [Azure 入口網站](https://portal.azure.com/)。    
2. 按一下左邊功能表上的 [新增]****、[資料 + 分析]****，再按一下 [Data Factory]****。 
   
   ![新增->DataFactory](./media/tutorial-create-azure-ssis-runtime-portal/new-data-factory-menu.png)
   
3. 在 [新增資料處理站]**** 頁面中，輸入 **MyAzureSsisDataFactory** 作為 [名稱]****。 
      
   ![新增資料處理站頁面](./media/tutorial-create-azure-ssis-runtime-portal/new-azure-data-factory.png)
 
   ADF 的名稱必須是全域唯一的。 如果您收到下列錯誤，請變更 ADF 的名稱 (例如 yournameMyAzureSsisDataFactory)，然後試著重新建立 ADF。 請參閱 [Data Factory - 命名規則](naming-rules.md)一文，以了解 ADF 成品的命名規則。
  
   `Data factory name MyAzureSsisDataFactory is not available`
      
4. 選取據以建立 ADF 的 Azure **訂用帳戶**。 
5. 針對**資源群組**，請執行下列其中一個步驟︰
     
   - 選取 [使用現有的]  ，然後從下拉式清單選取現有的資源群組。 
   - 選取 [建立新的]****，然後輸入新資源群組的名稱。   
         
   若要了解資源群組，請參閱[使用資源群組來管理 Azure 資源](../azure-resource-manager/management/overview.md)一文。
   
6. 針對 [版本]****，選取 [V2]****。
7. 針對 [位置]****，從下拉式清單中選取其中一個支援用來建立 ADF 的位置。
8. 選取 [釘選到儀表板]****。     
9. 按一下 [建立]  。
10. 在 Azure 儀表板上，您會看到狀態如下的下列圖格：**部署 Data Factory**。 

    ![部署資料處理站圖格](media/tutorial-create-azure-ssis-runtime-portal/deploying-data-factory.png)
   
11. 建立完成之後，您就會看到您的 ADF 頁面，如下所示。
   
    ![Data Factory 首頁](./media/tutorial-create-azure-ssis-runtime-portal/data-factory-home-page.png)
   
12. 按一下 [編寫與監視]****，以在個別索引標籤中啟動 ADF UI/應用程式。

### <a name="create-your-pipelines"></a>建立管線

1. 在 [讓我們開始吧]**** 頁面上，選取 [建立管線]****。 

   ![開始使用頁面](./media/how-to-schedule-azure-ssis-integration-runtime/get-started-page.png)
   
2. 在 [活動]**** 工具箱中展開 [一般]**** 功能表，然後將 [Web]**** 活動拖放到管線設計工具介面上。 在活動屬性視窗的 [一般]**** 索引標籤中，將活動名稱變更為 **startMyIR**。 切換至 [設定]**** 索引標籤，然後執行下列步驟。

    1. 針對 [ **URL**]，為啟動 Azure SSIS ir 的 REST API 輸入下列 URL，並`{subscriptionId}`將`{resourceGroupName}`、 `{factoryName}`、和`{integrationRuntimeName}`取代為您 ir 的實際值： `https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DataFactory/factories/{factoryName}/integrationRuntimes/{integrationRuntimeName}/start?api-version=2018-06-01`或者，您也可以從 ADF UI/應用程式上的 [監視] 頁面，複製 & 貼上 ir 的資源識別碼，以取代上述 URL 的下列部分：`/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DataFactory/factories/{factoryName}/integrationRuntimes/{integrationRuntimeName}`
    
       ![ADF SSIS IR 的資源識別碼](./media/how-to-schedule-azure-ssis-integration-runtime/adf-ssis-ir-resource-id.png)
  
    2. 針對 [方法]****，選取 [POST]****。 
    3. 針對 [本文]****，輸入 `{"message":"Start my IR"}`。 
    4. 針對 [**驗證**]，選取 [ **MSI** ] 以使用您 ADF 的受控識別，如需詳細資訊，請參閱[Data Factory 的受控識別](https://docs.microsoft.com/azure/data-factory/data-factory-service-identity)一文。
    5. 針對 [資源]****，輸入 `https://management.azure.com/`。
    
       ![ADF Web 活動排程 SSIS IR](./media/how-to-schedule-azure-ssis-integration-runtime/adf-web-activity-schedule-ssis-ir.png)
  
3. 複製第一個管線來建立第二個管線，並將活動名稱變更為 **stopMyIR** 並取代下列屬性。

    1. 針對 **[URL**]，針對停止 Azure SSIS IR 的 REST API 輸入下列 URL，並`{subscriptionId}`將`{resourceGroupName}`、 `{factoryName}`、和`{integrationRuntimeName}`取代為您 IR 的實際值：`https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DataFactory/factories/{factoryName}/integrationRuntimes/{integrationRuntimeName}/stop?api-version=2018-06-01`
    
    2. 針對 [本文]****，輸入 `{"message":"Stop my IR"}`。 

4. 建立第三個管線、從 [活動]**** 工具箱將 [執行 SSIS 套件]**** 活動拖放至管線設計工具介面上，然後遵循[在 ADF 中使用執行 SSIS 套件活動來叫用 SSIS 套件](how-to-invoke-ssis-package-ssis-activity.md)一文中的指示對其進行設定。  或者，您也可以改為使用**預存程序**活動，然後遵循[在 ADF 中使用預存程序活動來叫用 SSIS 套件](how-to-invoke-ssis-package-stored-procedure-activity.md)一文中的指示對其進行設定。  接下來，在會啟動/停止 IR 的兩個 Web 活動 (類似於第一個/第二個管線中的那些 Web 活動) 之間鏈結「執行 SSIS 套件」/「預存程序」活動。

   ![ADF Web 活動依需求 SSIS IR](./media/how-to-schedule-azure-ssis-integration-runtime/adf-web-activity-on-demand-ssis-ir.png)

5. 對 ADF 的受控識別指派其本身的**參與者**角色，讓其管線中的 Web 活動可以呼叫 REST API，以啟動/停止其中所佈建的 Azure-SSIS IR。  在 Azure 入口網站的 ADF 頁面上，按一下 [存取控制 (IAM)]****，按一下 [+ 新增角色指派]****，然後在 [新增角色指派]**** 刀鋒視窗上執行下列動作。

    1. 針對 [角色]****，選取 [參與者]****。 
    2. 在 [存取權指派對象為]**** 中，選取 [Azure AD 使用者、群組或服務主體]****。 
    3. 在 [選取]**** 中，搜尋您的 ADF 名稱並加以選取。 
    4. 按一下 [檔案]  。
    
   ![ADF 受控識別角色指派](./media/how-to-schedule-azure-ssis-integration-runtime/adf-managed-identity-role-assignment.png)

6. 在中心/管線工具列上按一下 [全部驗證/驗證]**** 來驗證 ADF 和所有的管線設定。 按一下 **>>** 按鈕，以關閉 [中心/管線驗證輸出]****。  

   ![驗證管線](./media/how-to-schedule-azure-ssis-integration-runtime/validate-pipeline.png)

### <a name="test-run-your-pipelines"></a>測試執行管線

1. 在每個管線的工具列上選取 [測試執行]****，然後查看底部窗格中的 [輸出]**** 視窗。 

   ![測試執行](./media/how-to-schedule-azure-ssis-integration-runtime/test-run-output.png)
    
2. 若要測試第三個管線，請啟動 SQL Server Management Studio (SSMS)。 在 [連線到伺服器]**** 視窗中，執行下列動作。 

    1. 在 [伺服器名稱]**** 中，輸入 **&lt;您的 Azure SQL Database 伺服器名稱&gt;.database.windows.net**。
    2. 選取 [選項 >>]****。
    3. 針對 [連線到資料庫]****，選取 [SSISDB]****。
    4. 選取 [連接]  。 
    5. 展開**Integration Services 目錄** -> **SSISDB** -> 您的資料夾 >**專案**-> SSIS 專案 >**套件**。 
    6. 以滑鼠右鍵按一下要執行的指定 SSIS 封裝，然後選取 [**報告** -> ] [**標準報告** -> ] [**所有**執行]。 
    7. 確認其已執行。 

   ![確認 SSIS 套件已執行](./media/how-to-schedule-azure-ssis-integration-runtime/verify-ssis-package-run.png)

### <a name="schedule-your-pipelines"></a>為管線排程

現在，管線已如您預期的方式運作，所以您可以建立觸發程序，以指定的頻率執行管線。 如需如何將觸發程序與管線產生關聯的詳細資訊，請參閱[觸發排程上的管線](quickstart-create-data-factory-portal.md#trigger-the-pipeline-on-a-schedule)一文。

1. 在管線工具列上選取 [觸發程序]****，然後選取 [新增/編輯]****。 

   ![觸發程序 -> 新增/編輯](./media/how-to-schedule-azure-ssis-integration-runtime/trigger-new-menu.png)

2. 在 [新增觸發程序]**** 窗格中，選取 [+ 新增]****。

   ![新增觸發程序 - 新增](./media/how-to-schedule-azure-ssis-integration-runtime/add-triggers-new.png)

3. 在 [新增觸發程序]**** 窗格中，執行下列動作： 

    1. 在 [名稱]**** 中，輸入觸發程序的名稱。 在下列範例中，觸發程序名稱是**每日執行**。 
    2. 針對 [類型]****，選取 [排程]****。 
    3. 在 [開始日期 (UTC)]**** 中，輸入開始日期和時間 (UTC)。 
    4. 在 [週期性]**** 中，輸入觸發程序的頻率。 在下列範例中，頻率是**每天**一次。 
    5. 在 [結束]**** 中，選取 [不會結束]****，或在選取 [日期]**** 之後輸入結束日期和時間。 
    6. 選取 [啟動]**** 以在您發佈了整個 ADF 設定之後立即啟動觸發程序。 
    7. 選取 [下一步]  。

   ![觸發程序 -> 新增/編輯](./media/how-to-schedule-azure-ssis-integration-runtime/new-trigger-window.png)
    
4. 在 [觸發程序執行參數]**** 頁面中檢閱警告，然後選取 [完成]****。 
5. 在中心工具列中選取 [全部發佈]**** 以發佈整個 ADF 設定。 

   ![全部發佈](./media/how-to-schedule-azure-ssis-integration-runtime/publish-all.png)

### <a name="monitor-your-pipelines-and-triggers-in-azure-portal"></a>在 Azure 入口網站中監視管線和觸發程序

1. 若要監視觸發程序的執行和管線的執行，請使用 ADF UI/應用程式左側的 [監視]**** 索引標籤。 如需詳細步驟，請參閱[監視管線](quickstart-create-data-factory-portal.md#monitor-the-pipeline)一文。

   ![管線執行](./media/how-to-schedule-azure-ssis-integration-runtime/pipeline-runs.png)

2. 若要檢視與管線執行相關聯的所有活動執行，請選取 [動作]**** 資料行中的第一個連結 ([檢視活動執行]****)。 針對第三個管線，您會看到三個活動執行，管線中的每個已鏈結活動各一個 (用來啟動 IR 的 Web 活動、用來執行套件的預存程序活動，以及用來停止 IR 的 Web 活動)。 若要再次檢視管線的執行，請選取頂端的 [管線]**** 連結。

   ![活動執行](./media/how-to-schedule-azure-ssis-integration-runtime/activity-runs.png)

3. 若要檢視觸發程序的執行，請從頂端 [管線執行]**** 底下的下拉式清單選取 [觸發程序執行]****。 

   ![觸發程序執行](./media/how-to-schedule-azure-ssis-integration-runtime/trigger-runs.png)

### <a name="monitor-your-pipelines-and-triggers-with-powershell"></a>使用 PowerShell 監視管線和觸發程序

使用如以下範例的指令碼來監視管線和觸發程序。

1. 取得管線執行的狀態。

   ```powershell
   Get-AzDataFactoryV2PipelineRun -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -PipelineRunId $myPipelineRun
   ```

2. 取得有關觸發程序的資訊。

   ```powershell
   Get-AzDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name  "myTrigger"
   ```

3. 取得觸發程序執行的狀態。

   ```powershell
   Get-AzDataFactoryV2TriggerRun -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -TriggerName "myTrigger" -TriggerRunStartedAfter "2018-07-15" -TriggerRunStartedBefore "2018-07-16"
   ```

## <a name="create-and-schedule-azure-automation-runbook-that-startsstops-azure-ssis-ir"></a>建立和排程會啟動/停止 Azure-SSIS IR 的 Azure 自動化 Runbook

在本節中，您會了解如何建立 Azure 自動化 Runbook 來執行 PowerShell 指令碼，以按照排程啟動/停止 Azure-SSIS IR。  當您想要在啟動/停止 IR 之前/之後執行其他指令碼以便進行前置/後置處理時，這會非常有用。

### <a name="create-your-azure-automation-account"></a>建立 Azure 自動化帳戶

如果您還沒有 Azure 自動化帳戶，請依照此步驟中的指示建立帳戶。 如需詳細步驟，請參閱[建立 Azure 自動化帳戶](../automation/automation-quickstart-create-account.md)一文。 在此步驟中，您會建立 **Azure 執行身分**帳戶 (Azure Active Directory 中的服務主體)，並在 Azure 訂用帳戶中為其指派**參與者**角色。 請確定這就是具有您 ADF (含有 Azure SSIS IR) 的同一個訂用帳戶。 Azure 自動化會使用此帳戶對 Azure Resource Manager 進行驗證，並對您的資源運作。 

1. 啟動 **Microsoft Edge** 或 **Google Chrome** 網頁瀏覽器。 目前，只有 Microsoft Edge 和 Google Chrome 網頁瀏覽器支援 ADF UI/應用程式。
2. 登入 [Azure 入口網站](https://portal.azure.com/)。    
3. 在左側功能表上選取 [新增]****，再選取 [監視 + 管理]****，然後選取 [自動化]****。 

   ![新增 -> 監視 + 管理 -> 自動化](./media/how-to-schedule-azure-ssis-integration-runtime/new-automation.png)
    
2. 在 [新增自動化帳戶]**** 窗格中，執行下列動作。

    1. 在 [名稱]**** 中，輸入 Azure 自動化帳戶的名稱。 
    2. 在 [訂用帳戶]**** 中，選取具有您 ADF (含有 Azure SSIS IR) 的訂用帳戶。 
    3. 針對 [資源群組]**** 選取 [新建]****，以建立新的資源群組，或選取 [使用現有的]**** 以選取現有的資源群組。 
    4. 針對 [位置]****，選取 Azure 自動化帳戶的位置。 
    5. 將 [建立 Azure 執行身分帳戶]**** 確認為 [是]****。 Azure Active Directory 中便會建立服務主體，並在 Azure 訂用帳戶中為其指派**參與者**角色。
    6. 選取 [釘選到儀表板]**** 讓其永久顯示於 Azure 儀表板。 
    7. 選取 [建立]  。 

   ![新增 -> 監視 + 管理 -> 自動化](./media/how-to-schedule-azure-ssis-integration-runtime/add-automation-account-window.png)
   
3. 您會在 Azure 儀表板和通知中看到 Azure 自動化帳戶的部署狀態。 
    
   ![部署自動化](./media/how-to-schedule-azure-ssis-integration-runtime/deploying-automation.png) 
    
4. Azure 自動化帳戶成功建立後會顯示首頁。 

   ![自動化首頁](./media/how-to-schedule-azure-ssis-integration-runtime/automation-home-page.png)

### <a name="import-adf-modules"></a>匯入 ADF 模組

1. 在左側功能表的 [**共用資源**] 區段中選取 [**模組**]，並確認您的模組清單中是否有**az. DataFactory** + **az. Profile** 。

   ![驗證必要的模組](media/how-to-schedule-azure-ssis-integration-runtime/automation-fix-image1.png)

2.  如果您沒有**DataFactory**，請移至 [ [DataFactory] 模組](https://www.powershellgallery.com/packages/Az.DataFactory/)的 [PowerShell 資源庫，選取 [**部署至 Azure 自動化**]，選取您的 Azure 自動化帳戶，然後選取 **[確定]**。 回到左側功能表的 [**共用資源**] 區段，然後等到您看到**DataFactory** **模組的****狀態**變更為 [**可用**] 為止。

    ![驗證 Data Factory 模組](media/how-to-schedule-azure-ssis-integration-runtime/automation-fix-image2.png)

3.  如果您沒有**az. profile**，請移至[az. profile 模組](https://www.powershellgallery.com/packages/Az.profile/)的 PowerShell 資源庫，選取 [**部署至 Azure 自動化**]，選取您的 Azure 自動化帳戶，然後選取 **[確定]**。 回到左側**功能表的 [** **共用資源**] 區段中，然後等候直到您看到**Az. Profile**模組的**狀態**變更為 [**可用**] 為止。

    ![驗證設定檔模組](media/how-to-schedule-azure-ssis-integration-runtime/automation-fix-image3.png)

### <a name="create-your-powershell-runbook"></a>建立 PowerShell Runbook

下列章節提供建立 PowerShell Runbook 的步驟。 與 Runbook 相關聯的指令碼會根據您為 **OPERATION** 參數指定的命令來啟動/停止 Azure-SSIS IR。 本節將不提供建立 Runbook 的完整詳細資料。 如需詳細資訊，請參閱[建立 Runbook](../automation/automation-quickstart-create-runbook.md) 一文。

1. 切換至 [Runbook]**** 索引標籤，然後從工具列中選取 [+ 新增 Runbook]****。 

   ![新增 Runbook 按鈕](./media/how-to-schedule-azure-ssis-integration-runtime/runbooks-window.png)
   
2. 選取 [建立新的 Runbook]****，並執行下列動作： 

    1. 針對 [名稱]****，輸入 **StartStopAzureSsisRuntime**。
    2. 針對 [Runbook 類型]****，選取 [PowerShell]****。
    3. 選取 [建立]  。
    
   ![新增 Runbook 按鈕](./media/how-to-schedule-azure-ssis-integration-runtime/add-runbook-window.png)
   
3. 將下列 PowerShell 指令碼複製並貼到 Runbook 指令碼視窗中。 使用工具列上的 [儲存]**** 和 [發佈]**** 按鈕，在儲存 Runbook 後加以發佈。 

    ```powershell
    Param
    (
          [Parameter (Mandatory= $true)]
          [String] $ResourceGroupName,
    
          [Parameter (Mandatory= $true)]
          [String] $DataFactoryName,
    
          [Parameter (Mandatory= $true)]
          [String] $AzureSSISName,
    
          [Parameter (Mandatory= $true)]
          [String] $Operation
    )
    
    $connectionName = "AzureRunAsConnection"
    try
    {
        # Get the connection "AzureRunAsConnection "
        $servicePrincipalConnection=Get-AutomationConnection -Name $connectionName         
    
        "Logging in to Azure..."
        Connect-AzAccount `
            -ServicePrincipal `
            -TenantId $servicePrincipalConnection.TenantId `
            -ApplicationId $servicePrincipalConnection.ApplicationId `
            -CertificateThumbprint $servicePrincipalConnection.CertificateThumbprint 
    }
    catch {
        if (!$servicePrincipalConnection)
        {
            $ErrorMessage = "Connection $connectionName not found."
            throw $ErrorMessage
        } else{
            Write-Error -Message $_.Exception
            throw $_.Exception
        }
    }
    
    if($Operation -eq "START" -or $operation -eq "start")
    {
        "##### Starting #####"
        Start-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name $AzureSSISName -Force
    }
    elseif($Operation -eq "STOP" -or $operation -eq "stop")
    {
        "##### Stopping #####"
        Stop-AzDataFactoryV2IntegrationRuntime -DataFactoryName $DataFactoryName -Name $AzureSSISName -ResourceGroupName $ResourceGroupName -Force
    }  
    "##### Completed #####"    
    ```

   ![編輯 PowerShell Runbook](./media/how-to-schedule-azure-ssis-integration-runtime/edit-powershell-runbook.png)
    
4. 選取工具列上的 [啟動]**** 按鈕以測試 Runbook。 

   ![啟動 Runbook 按鈕](./media/how-to-schedule-azure-ssis-integration-runtime/start-runbook-button.png)
    
5. 在 [**啟動 Runbook** ] 窗格中，執行下列動作： 

    1. 針對 [資源群組名稱]****，輸入具有您 ADF (含有 Azure SSIS IR) 的資源群組名稱。 
    2. 針對 [資料處理站名稱]****，輸入您 ADF (含有 Azure SSIS IR) 的名稱。 
    3. 針對 [AZURESSISNAME]****，輸入 Azure-SSIS IR 的名稱。 
    4. 針對 [OPERATION]****，輸入 **START**。 
    5. 選取 [確定]  。  

   ![啟動 Runbook 視窗](./media/how-to-schedule-azure-ssis-integration-runtime/start-runbook-window.png)
   
6. 在 [作業] 視窗中，選取 [輸出]**** 圖格。 在輸出視窗中，在您看到 **##### Starting #####** 之後等待 **##### Completed #####** 訊息顯示。 啟動 Azure-SSIS IR 約需要 20 分鐘。 關閉 [作業]**** 視窗，並回到 [Runbook]**** 視窗。

   ![Azure SSIS IR - 已啟動](./media/how-to-schedule-azure-ssis-integration-runtime/start-completed.png)
    
7. 使用 [STOP]**** 作為 [OPERATION]**** 的值來重複前兩個步驟。 選取工具列上的 [啟動]**** 按鈕以重新啟動 Runbook。 輸入資源群組、ADF 和 Azure-SSIS IR 的名稱。 針對 [OPERATION]****，輸入 **STOP**。 在輸出視窗中，在您看到 **##### Stopping #####** 之後等待 **##### Completed #####** 訊息顯示。 停止 Azure-SSIS IR 不會像啟動時那麼耗時。 關閉 [作業]**** 視窗，並回到 [Runbook]**** 視窗。

8. 您也可以透過選取 [ **webhook** ] 功能表項目，或依下列方式選取 [排程 **] 功能表項目**，以建立可以建立的 webhook 來觸發 runbook。  

## <a name="create-schedules-for-your-runbook-to-startstop-azure-ssis-ir"></a>為 Runbook 建立用來啟動/停止 Azure-SSIS IR 的排程

在上一節中，您已建立可啟動或停止 Azure-SSIS IR 的 Azure 自動化 Runbook。 在本節中，您會為 Runbook 建立兩個排程。 設定第一個排程時，您會為 **OPERATION** 指定 **START**。 同理，在設定第二個排程時，您會為 **OPERATION** 指定 **STOP**。 如需建立排程的詳細步驟，請參閱[建立排程](../automation/shared-resources/schedules.md#create-a-schedule)一文。

1. 在 [Runbook]**** 視窗中選取 [排程]****，然後選取工具列上的 [+ 新增排程]****。 

   ![Azure SSIS IR - 已啟動](./media/how-to-schedule-azure-ssis-integration-runtime/add-schedules-button.png)
   
2. 在 [排程 Runbook]**** 窗格中，執行下列動作： 

    1. 選取 [將排程連結至您的 Runbook]****。 
    2. 選取 [建立新的排程]****。
    3. 在 [新增排程]**** 窗格中，輸入**每日啟動 IR** 作為 [名稱]****。 
    4. 在 [啟動]**** 中，輸入比當下時間晚幾分鐘的時間。 
    5. 針對 [週期性]****，選取 [循環]****。 
    6. 在 [重複頻率]**** 中輸入 **1**，然後選取 [天]****。 
    7. 選取 [建立]  。 

   ![啟動 Azure SSIS IR 的排程](./media/how-to-schedule-azure-ssis-integration-runtime/new-schedule-start.png)
    
3. 切換至 [**參數與執行設定**] 索引標籤。指定您的資源群組、ADF 和 AZURE SSIS IR 名稱。 針對 [OPERATION]****，輸入 **START**，然後選取 [確定]****。 再次選取 [確定]****，以在 Runbook 的 [排程]**** 頁面上檢視排程。 

   ![啟動 Azure SSIS IR 的排程](./media/how-to-schedule-azure-ssis-integration-runtime/start-schedule.png)
    
4. 重複前兩個步驟，以建立名為**每日停止 IR** 的排程。 請輸入比**每日啟動 IR** 排程的指定時間至少晚 30 分鐘的時間。 針對 [OPERATION]****，輸入 **STOP**，然後選取 [確定]****。 再次選取 [確定]****，以在 Runbook 的 [排程]**** 頁面上檢視排程。 

5. 在 [Runbook]**** 視窗中，選取左側功能表上的 [作業]****。 您應該會看到排程在指定時間建立的作業及其狀態。 您可以檢視作業詳細資料 (例如其輸出)，與您先前在測試 Runbook 時所看到的類似。 

   ![啟動 Azure SSIS IR 的排程](./media/how-to-schedule-azure-ssis-integration-runtime/schedule-jobs.png)
    
6. 測試完成後，請編輯排程來將其停用。 選取左側功能表上的 [排程]****，再選取 [每日啟動 IR/每日停止 IR]****，然後針對 [已啟用]**** 選取 [否]****。 

## <a name="next-steps"></a>後續步驟
請參閱下列部落格文章：
-   [使用 ADF 管線中的 SSIS 活動來現代化及擴充您的 ETL/ELT 工作流程](https://techcommunity.microsoft.com/t5/SQL-Server-Integration-Services/Modernize-and-Extend-Your-ETL-ELT-Workflows-with-SSIS-Activities/ba-p/388370)

請參閱 SSIS 文件中的下列文章： 

- [部署、執行和監視 Azure 上的 SSIS 套件](/sql/integration-services/lift-shift/ssis-azure-deploy-run-monitor-tutorial)   
- [連線到 Azure 上的 SSIS 目錄](/sql/integration-services/lift-shift/ssis-azure-connect-to-catalog-database)
- [在 Azure 上排程套件執行](/sql/integration-services/lift-shift/ssis-azure-schedule-packages)
- [透過 Windows 驗證連線至內部部署資料來源](/sql/integration-services/lift-shift/ssis-azure-connect-with-windows-auth)
