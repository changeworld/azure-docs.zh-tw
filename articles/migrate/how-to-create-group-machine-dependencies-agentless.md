---
title: 在 Azure Migrate 伺服器評量中設定無代理程式相依性分析
description: 在 Azure Migrate 伺服器評量中設定無代理程式相依性分析。
ms.topic: how-to
ms.date: 6/08/2020
ms.openlocfilehash: 57e5add810cf4fac232bce08fc7ca96df0a7c3a0
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "91667464"
---
# <a name="analyze-machine-dependencies-agentless"></a>分析機器相依性 (無代理程式)

本文說明如何在 Azure Migrate：伺服器評量中設定無代理程式相依性分析。 相依性[分析](concepts-dependency-visualization.md)可協助您識別及瞭解跨電腦的相依性，以進行評量和遷移至 Azure。


> [!IMPORTANT]
> 使用 Azure Migrate：伺服器評估工具探索到的 VMware Vm，無代理程式相依性視覺效果目前為預覽狀態。
> 功能可能受限或不完整。
> 這是客戶支援所涵蓋的預覽，可用於生產工作負載。
> 如需詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

## <a name="current-limitations"></a>目前的限制

- 在 [相依性分析] 視圖中，您目前無法從群組新增或移除伺服器。
- 目前無法使用伺服器群組的相依性對應。
- 您可以為1000伺服器同時設定相依性資料收集。 您可以使用1000的批次順序來分析較高數目的伺服器。

## <a name="before-you-start"></a>開始之前

- [檢查](migrate-support-matrix-vmware.md#dependency-analysis-requirements-agentless) 支援的作業系統和必要的許可權。
- 請確定您已執行下列動作：
    - 有 Azure Migrate 專案。 如果沒有，請立即 [建立](how-to-add-tool-first-time.md) 一個。
    - 檢查您是否已將 Azure Migrate：伺服器評估工具 [新增](how-to-assess.md) 至專案。
    - 設定 [Azure Migrate 設備](migrate-appliance.md) 以探索內部部署機器。 為 VMware Vm[設定設備](how-to-set-up-appliance-vmware.md)。 設備會探索內部部署機器，並將中繼資料和效能資料傳送至 Azure Migrate：伺服器評量。
- 檢查您想要分析的每個 VM 上是否已安裝 VMware Tools (晚于 10.2) 。


## <a name="create-a-user-account-for-discovery"></a>建立用於探索的使用者帳戶

設定使用者帳戶，讓伺服器評量可以存取 VM 以探索相依性。 [瞭解](migrate-support-matrix-vmware.md#dependency-analysis-requirements-agentless) Windows 和 Linux vm 的帳戶需求。


## <a name="add-the-user-account-to-the-appliance"></a>將使用者帳戶新增至設備

將使用者帳戶新增至設備。

1. 開啟設備管理應用程式。 
2. 流覽至 [ **提供 vCenter 詳細資料** ] 面板。
3. 在 [**探索 vm 上的應用程式和**相依性] 中，按一下 [**新增認證**]
3. 選擇**作業系統**、提供帳戶的易記名稱和**使用者名稱** / **密碼**
6. 按一下 **[儲存]** 。
7. 按一下 [ **儲存並開始探索**]。

    ![新增 VM 使用者帳戶](./media/how-to-create-group-machine-dependencies-agentless/add-vm-credential.png)

## <a name="start-dependency-discovery"></a>啟動相依性探索

選擇您要啟用相依性探索的電腦。 

1. 在 [ **Azure Migrate：伺服器評估**] 中，按一下 [探索到的 **伺服器**]。
2. 按一下 [相依性 **分析** ] 圖示。
3. 按一下 [ **新增伺服器**]。
4. 在 [ **新增伺服器** ] 頁面中，選擇正在探索相關機器的設備。
5. 從 [電腦] 清單中選取電腦。
6. 按一下 [ **新增伺服器**]。

    ![啟動相依性探索](./media/how-to-create-group-machine-dependencies-agentless/start-dependency-discovery.png)

開始相依性探索之後，您可以將相依性視覺化大約六個小時。 如果您想要啟用數部機器，您可以使用 [PowerShell](#start-or-stop-dependency-discovery-using-powershell) 來執行這項操作。

## <a name="visualize-dependencies"></a>將相依性視覺化

1. 在 [ **Azure Migrate：伺服器評估**] 中，按一下 [探索到的 **伺服器**]。
2. 搜尋您想要查看的機器。
3. 在 [相依性 **]** 資料行中，按一下 [視圖相依性 **]**
4. 使用 [ **持續時間** ] 下拉式清單，變更您要查看其對應的時間週期。
5. 展開 **用戶端** 群組以列出具有所選電腦相依性的電腦。
6. 展開通訊 **埠** 群組，以列出相依于所選機器的機器。
7. 若要流覽至任何相依機器的地圖視圖，請按一下電腦名稱稱 > **載入伺服器對應**

    ![展開 [伺服器埠群組和載入伺服器對應]](./media/how-to-create-group-machine-dependencies-agentless/load-server-map.png)

    ![展開 [用戶端群組] ](./media/how-to-create-group-machine-dependencies-agentless/expand-client-group.png)

8. 展開選取的機器，以查看每個相依性的進程層級詳細資料。

    ![展開 [伺服器] 以顯示進程](./media/how-to-create-group-machine-dependencies-agentless/expand-server-processes.png)

> [!NOTE]
> 相依性的進程資訊不一定可以使用。 如果無法使用，相依性會以標示為「未知進程」的進程來描述。

## <a name="export-dependency-data"></a>匯出相依性資料

1. 在 [ **Azure Migrate：伺服器評估**] 中，按一下 [探索到的 **伺服器**]。
2. 按一下 [相依性 **分析** ] 圖示。
3. 按一下 [ **匯出應用程式**相依性]。
4. 在 [ **匯出應用程式** 相依性] 頁面中，選擇正在探索相關機器的設備。
5. 選取 [開始時間] 和 [結束時間]。 請注意，您只能下載過去30天的資料。
6. 按一下 [ **匯出**相依性]。

相依性資料會以 CSV 格式匯出和下載。 下載的檔案包含所有啟用相依性分析之電腦上的相依性資料。 

![匯出相依性](./media/how-to-create-group-machine-dependencies-agentless/export.png)

### <a name="dependency-information"></a>相依性資訊

匯出 CSV 中的每個資料列都會對應到在指定的時間位置中觀察到的相依性。 

下表摘要說明匯出之 CSV 中的欄位。 請注意，[伺服器名稱]、[應用程式] 和 [進程] 欄位只會填入已啟用無代理程式相依性分析的伺服器。

**欄位名稱** | **詳細資料**
--- | --- 
Timeslot | 觀察到相依性期間的 timeslot。 <br/> 相依性資料目前是在6個小時的位置上捕獲。
來源伺服器名稱 | 來源電腦的名稱 
來源應用程式 | 來源電腦上的應用程式名稱 
來源程序 | 來源電腦上的進程名稱 
目的地伺服器名稱 | 目的地電腦的名稱
目的地 IP | 目的地電腦的 IP 位址
目的地應用程式 | 目的地電腦上的應用程式名稱
目的地進程 | 目的地電腦上的進程名稱 
目的地連接埠 | 目的地電腦上的埠號碼


## <a name="stop-dependency-discovery"></a>停止相依性探索

選擇您要停止相依性探索的電腦。 

1. 在 [ **Azure Migrate：伺服器評估**] 中，按一下 [探索到的 **伺服器**]。
2. 按一下 [相依性 **分析** ] 圖示。
3. 按一下 [ **移除伺服器**]。
3. 在 [ **移除伺服器** ] 頁面中，選擇正在探索要停止相依性探索之 vm 的 **設備** 。
4. 從 [電腦] 清單中選取電腦。
5. 按一下 [ **移除伺服器**]。

如果您想要停止相依于數部電腦，您可以使用 [PowerShell](#start-or-stop-dependency-discovery-using-powershell) 來執行這項操作。


## <a name="start-or-stop-dependency-discovery-using-powershell"></a>使用 PowerShell 啟動或停止相依性探索

從 GitHub 上 [Azure PowerShell 範例](https://github.com/Azure/azure-docs-powershell-samples/tree/master/azure-migrate/dependencies-at-scale) 存放庫下載 PowerShell 模組。


### <a name="log-in-to-azure"></a>登入 Azure

1. 使用 Connect-AzAccount Cmdlet 登入您的 Azure 訂用帳戶。

    ```PowerShell
    Connect-AzAccount
    ```
    如果使用 Azure Government，請使用下列命令。
    ```PowerShell
    Connect-AzAccount -EnvironmentName AzureUSGovernment
    ```

2. 選取您已在其中建立 Azure Migrate 專案的訂用帳戶 

    ```PowerShell
    select-azsubscription -subscription "Fabrikam Demo Subscription"
    ```

3. 匯入已下載的 AzMig_Dependencies PowerShell 模組

    ```PowerShell
    Import-Module .\AzMig_Dependencies.psm1
    ```

### <a name="enable-or-disable-dependency-data-collection"></a>啟用或停用相依性資料收集

1. 使用下列命令，在您的 Azure Migrate 專案中取得探索到的 VMware Vm 清單。 在下列範例中，專案名稱是 FabrikamDemoProject，而其所屬的資源群組為 FabrikamDemoRG。 電腦清單會儲存在 FabrikamDemo_VMs.csv

    ```PowerShell
    Get-AzMigDiscoveredVMwareVMs -ResourceGroupName "FabrikamDemoRG" -ProjectName "FabrikamDemoProject" -OutputCsvFile "FabrikamDemo_VMs.csv"
    ```

    在檔案中，您可以看到 VM 顯示名稱、相依性集合的目前狀態，以及所有探索到之 Vm 的 ARM 識別碼。 

2. 若要啟用或停用相依性，請建立輸入 CSV 檔案。 檔案必須具有標頭為 "ARM ID" 的資料行。 CSV 檔案中的任何其他標頭都會被忽略。 您可以使用上一個步驟所產生的檔案來建立 CSV。 建立保留您要啟用或停用相依性之虛擬機器的檔案複本。 

    在下列範例中，FabrikamDemo_VMs_Enable.csv 的輸入檔中的 Vm 清單上會啟用相依性分析。

    ```PowerShell
    Set-AzMigDependencyMappingAgentless -InputCsvFile .\FabrikamDemo_VMs_Enable.csv -Enable
    ```

    在下列範例中，會在輸入檔 FabrikamDemo_VMs_Disable.csv 中的 Vm 清單上停用相依性分析。

    ```PowerShell
    Set-AzMigDependencyMappingAgentless -InputCsvFile .\FabrikamDemo_VMs_Disable.csv -Disable
    ```

## <a name="visualize-network-connections-in-power-bi"></a>將 Power BI 中的網路連接視覺化

Azure Migrate 提供 Power BI 範本，可讓您一次用來視覺化許多伺服器的網路連線，並依進程和伺服器篩選。 若要視覺化，請依照下列指示載入具有相依性資料的 Power BI。

1. 從 GitHub 上的 [Azure PowerShell 範例](https://github.com/Azure/azure-docs-powershell-samples/tree/master/azure-migrate/dependencies-at-scale) 存放庫下載 PowerShell 模組和 Power BI 範本。

2. 使用下列指示登入 Azure： 
- 使用 Connect-AzAccount Cmdlet 登入您的 Azure 訂用帳戶。

    ```PowerShell
    Connect-AzAccount
    ```

- 如果使用 Azure Government，請使用下列命令。

    ```PowerShell
    Connect-AzAccount -EnvironmentName AzureUSGovernment
    ```

- 選取您已在其中建立 Azure Migrate 專案的訂用帳戶 

    ```PowerShell
    select-azsubscription -subscription "Fabrikam Demo Subscription"
    ```

3. 匯入已下載的 AzMig_Dependencies PowerShell 模組

    ```PowerShell
    Import-Module .\AzMig_Dependencies.psm1
    ```

4. 執行下列命令。 此命令會下載 CSV 中的相依性資料並加以處理，以產生可用於 Power BI 視覺效果的唯一相依性清單。 在下列範例中，專案名稱是 FabrikamDemoProject，而其所屬的資源群組為 FabrikamDemoRG。 FabrikamAppliance 探索到的機器會下載相依性。 唯一的相依性將會儲存在 FabrikamDemo_Dependencies.csv

    ```PowerShell
    Get-AzMigDependenciesAgentless -ResourceGroup FabrikamDemoRG -Appliance FabrikamAppliance -ProjectName FabrikamDemoProject -OutputCsvFile "FabrikamDemo_Dependencies.csv"
    ```

5. 開啟已下載的 Power BI 範本

6. 在 Power BI 中載入已下載的相依性資料。
    - 在 Power BI 中開啟範本。
    - 按一下工具列上的 [ **取得資料** ]。 
    - 從一般資料來源選擇 **Text/CSV** 。
    - 選擇已下載的相依性檔案。
    - 按一下 [載入]。
    - 您會看到一份資料表以 CSV 檔案名的名稱匯入。 您可以在右側的欄位列中看到資料表。 將它重新命名為 AzMig_Dependencies
    - 按一下工具列上的 [重新整理]。

    網路連接圖表和來源伺服器名稱、目的地伺服器名稱、來源進程名稱、目的地進程名稱交叉分析篩選器應該與匯入的資料亮。

7. 將網路連接篩選的對應依伺服器和進程視覺化。 儲存您的檔案。


## <a name="next-steps"></a>後續步驟

將[機器分組](how-to-create-a-group.md)以進行評量。
