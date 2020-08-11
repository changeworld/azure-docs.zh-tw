---
title: 監視 Azure Data Factory 中的整合執行時間
description: 了解如何監視 Azure Data Factory 中不同類型的整合執行階段。
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 08/11/2020
author: djpmsft
ms.author: daperlov
manager: anandsub
ms.openlocfilehash: b8d3472eeedab72644456b4278d3b9f3625c5850
ms.sourcegitcommit: 2ffa5bae1545c660d6f3b62f31c4efa69c1e957f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/11/2020
ms.locfileid: "88078166"
---
# <a name="monitor-an-integration-runtime-in-azure-data-factory"></a>在 Azure Data Factory 中監視整合執行階段

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]
  
**整合運行**時間是 AZURE DATA FACTORY (ADF) 所使用的計算基礎結構，可跨不同的網路環境提供各種資料整合功能。 Data Factory 提供三種類型的整合執行階段：

- Azure 整合執行階段
- 自我裝載整合執行階段
- Azure SQL Server Integration Services (SSIS) 整合執行時間

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

若要取得整合執行階段 (IR) 的執行個體狀態，請執行下列 PowerShell 命令： 

```powershell
Get-AzDataFactoryV2IntegrationRuntime -DataFactoryName MyDataFactory -ResourceGroupName MyResourceGroup -Name MyAzureIR -Status
``` 

Cmdlet 會為不同類型的整合執行階段傳回不同的資訊。 本文說明每一種整合執行階段類型的屬性和狀態。  

## <a name="azure-integration-runtime"></a>Azure 整合執行階段

可在 Azure 中完整且靈活地管理 Azure 整合執行階段的計算資源。 下表提供**AzDataFactoryV2IntegrationRuntime**命令所傳回之屬性的描述：

### <a name="properties"></a>屬性

下表提供 Azure 整合執行階段的 Cmdlet 所傳回的屬性說明：

| 屬性 | 描述 |
-------- | ------------- | 
| 名稱 | Azure 整合執行階段的名稱。 |  
| State | Azure 整合執行階段的狀態。 | 
| 位置 | Azure 整合執行階段的位置。 如需 Azure 整合執行階段的位置詳細資訊，請參閱[整合執行階段簡介](concepts-integration-runtime.md)。 |
| DataFactoryName | Azure 整合執行階段所屬的資料處理站名稱。 | 
| resourceGroupName | 資料處理站所屬的資源群組名稱。  |
| 描述 | 整合執行階段的說明。  |

### <a name="status"></a>狀態

下表提供 Azure 整合執行階段的可能狀態：

| 狀態 | 註解/案例 | 
| ------ | ------------------ |
| 線上 | Azure 整合執行階段在線上且可供使用。 | 
| 離線 | Azure 整合執行階段因內部錯誤而離線。 |

## <a name="self-hosted-integration-runtime"></a>自我裝載整合執行階段

本節提供 AzDataFactoryV2IntegrationRuntime Cmdlet 所傳回之屬性的描述。 

> [!NOTE] 
> 傳回的屬性和狀態包含整體的自我裝載整合執行階段，和執行階段中每個節點的相關資訊。  

### <a name="properties"></a>屬性

下表提供**每個節點**的監視屬性說明：

| 屬性 | 描述 | 
| -------- | ----------- | 
| 名稱 | 自我裝載整合執行階段及其關聯之節點的名稱。 節點是安裝了自我裝載整合執行階段的內部部署 Windows 電腦。 |  
| 狀態 | 整體自我裝載整合執行階段與每個節點的狀態。 範例：線上/離線/有限/等。如需這些狀態的相關資訊，請參閱下一節。 | 
| 版本 | 自我裝載整合執行階段與每個節點的版本。 自我裝載整合執行階段的版本取決於群組中大多數節點的版本。 如果自我裝載整合執行階段設定中有不同版本的節點，則只有版本號碼和邏輯自我裝載整合執行階段的節點會正常運作。 其他節點會進入受限制模式，並需要加以手動更新 (如果自動更新失敗才需要這麼做)。 | 
| 可用的記憶體 | 自我裝載整合執行階段節點上的可用記憶體。 這個值是近乎即時的快照集。 | 
| CPU 使用率 | 自我裝載整合執行階段節點的 CPU 使用率。 這個值是近乎即時的快照集。 |
| 網路功能 (輸入/輸出) | 自我裝載整合執行階段節點的網路使用率。 這個值是近乎即時的快照集。 | 
| 並行作業 (執行中/限制) | **正在**執行。 每個節點上執行的作業或工作數目。 這個值是近乎即時的快照集。 <br/><br/>**限制**。 限制表示每個節點的最大並行作業數。 這個值會根據機器大小來定義。 您可以提高限制以在進階案例 (即便 CPU、記憶體或網路並未充分使用，而活動照樣會逾時的案例) 中相應增加並行作業執行能力。 這項功能也可與單一節點的自我裝載整合執行階段搭配使用。 |
| 角色 | 多節點的自我裝載整合執行階段中的角色有兩種 – 發送器和背景工作角色。 所有節點都是背景工作角色，這表示它們全都能用來執行作業。 發送器節點只有一個，可用來提取雲端服務中的工作/作業，並發送到不同的背景工作節點。 發送器節點也是背景工作角色節點。 |

當自我裝載整合執行階段中有兩個或多個節點 (也就是相應放大的案例) 時，屬性的某些設定會比較合理。

#### <a name="concurrent-jobs-limit"></a>並行作業數限制

並行作業數限制的預設值取決於機器大小。 用來計算此值的因素取決於 RAM 的數量和機器的 CPU 核心數目。 因此，核心和記憶體越多時，並行作業數預設限制就越大。

您可以增加節點數目來相應放大。 當您增加節點數目時，並行作業數限制是所有可用的節點的並行作業數限制值的總和。  例如，有一個節點可讓您執行最多十二個並行作業，然後再新增三個類似的節點之後，您能執行的並行作業數最多為 48 個 (也就是 4 x 12)。 我們建議您只有在看到每個節點上的預設值會造成資源使用率偏低的狀況時，才增加並行作業數限制。

您可以覆寫 Azure 入口網站中計算得出的預設值。 依序選取 [作者] > [連線] > [整合執行階段] > [編輯] > [節點] > [修改每個節點的並行作業值]。 您也可以使用 PowerShell[更新-Azdatafactoryv2integrationruntimenode](https://docs.microsoft.com/powershell/module/az.datafactory/update-Azdatafactoryv2integrationruntimenode#examples)命令。
  
### <a name="status-per-node"></a>狀態 (每個節點)

下表提供自我裝載整合執行階段節點的可能狀態：

| 狀態 | 描述 |
| ------ | ------------------ | 
| 線上 | 節點已連線至 Data Factory 服務。 |
| 離線 | 節點已離線。 |
| 升級中 | 節點正在自動更新。 |
| 限制 | 由於連線能力問題。 可能是因為 HTTP 連接埠 8050 問題、服務匯流排連線問題或認證同步問題。 |
| 非使用中 | 節點所在的組態不同於其他大多數節點的組態。 |

節點無法連線至其他節點時，便會處於非使用中狀態。

### <a name="status-overall-self-hosted-integration-runtime"></a>狀態 (整體自我裝載整合執行階段)

下表提供自我裝載整合執行階段的可能狀態。 此狀態取決於隸屬於執行階段的所有節點狀態。 

| 狀態 | 描述 |
| ------ | ----------- | 
| 需要註冊 | 尚未針對此自我裝載整合執行階段註冊節點。 |
| 線上 | 所有節點已連線。 |
| 離線 | 沒有節點在線上。 |
| 限制 | 在此自我裝載整合執行階段中，並非所有節點皆處於健康狀態。 這個狀態是某些節點可能會關閉的警告。 此狀態可能是發送器/背景工作角色節點的認證同步問題所引起。 |

使用**AzDataFactoryV2IntegrationRuntimeMetric** Cmdlet 來提取 JSON 承載，其中包含詳細的自我裝載整合執行時間屬性，以及其在執行 Cmdlet 期間的快照集值。

```powershell
Get-AzDataFactoryV2IntegrationRuntimeMetric -name $integrationRuntimeName -ResourceGroupName $resourceGroupName -DataFactoryName $dataFactoryName | ConvertTo-Json 
```

輸出範例 (假設此自我裝載整合執行階段有兩個相關節點)：

```json
{
    "IntegrationRuntimeName":  "<Name of your integration runtime>",
    "ResourceGroupName":  "<Resource Group Name>",
    "DataFactoryName":  "<Data Factory Name>",
    "Nodes":  [
        {
            "NodeName":  "<Node Name>",
            "AvailableMemoryInMB":  <Value>,
            "CpuUtilization":  <Value>,
            "ConcurrentJobsLimit":  <Value>,
            "ConcurrentJobsRunning":  <Value>,
            "MaxConcurrentJobs":  <Value>,
            "SentBytes":  <Value>,
            "ReceivedBytes":  <Value>
        },
        {
            "NodeName":  "<Node Name>",
            "AvailableMemoryInMB":  <Value>,
            "CpuUtilization":  <Value>,
            "ConcurrentJobsLimit":  <Value>,
            "ConcurrentJobsRunning":  <Value>,
            "MaxConcurrentJobs":  <Value>,
            "SentBytes":  <Value>,
            "ReceivedBytes":  <Value>
        }

    ]
} 
```

## <a name="azure-ssis-integration-runtime"></a>Azure SSIS 整合執行階段

Azure SSIS IR 是完全受控的 Azure 虛擬機器叢集， (Vm 或節點) 專門用來執行您的 SSIS 套件。 您可以使用各種方法在 Azure SSIS IR 上叫用 SSIS 套件執行，例如透過啟用 Azure 的 SQL Server Data Tools (SSDT) 、AzureDTExec 命令列公用程式、SQL Server Management Studio (SSMS) /SQL 伺服器代理程式上的 T-sql，以及在 ADF 管線中執行 SSIS 套件活動。 Azure SSIS IR 不會執行任何其他 ADF 活動。 布建之後，您可以透過 Azure PowerShell、Azure 入口網站和 Azure 監視器監視其整體/節點特定的屬性和狀態。

### <a name="monitor-the-azure-ssis-integration-runtime-with-azure-powershell"></a>使用 Azure PowerShell 監視 Azure SSIS 整合執行時間

使用下列 Azure PowerShell Cmdlet 來監視 Azure SSIS IR 的整體/節點特定屬性和狀態。

```powershell
Get-AzDataFactoryV2IntegrationRuntime -DataFactoryName $DataFactoryName -Name $AzureSSISName -ResourceGroupName $ResourceGroupName -Status
```

#### <a name="properties"></a>屬性

下表提供上述 Cmdlet 針對 Azure SSIS IR 所傳回之屬性的描述。

| 屬性/狀態              | 描述                  |
| ---------------------------- | ---------------------------- |
| CreateTime                   | 建立 Azure SSIS IR 時的 UTC 時間。 |
| 節點                        | 具有節點特定狀態的 Azure SSIS IR 已配置/可用的節點， (啟動/可用/回收/無法使用) 和可操作的錯誤。 |
| OtherErrors                  | 您的 Azure SSIS IR 上的非節點特定可操作錯誤。 |
| LastOperation                | 在您的 Azure SSIS IR 上上一次啟動/停止作業的結果，如果失敗，則會有可採取動作的錯誤 (s) 。 |
| State                        | 整體狀態 (您的 Azure SSIS IR 的初始/啟動/啟動/停止/停止) 。 |
| 位置                     | 您的 Azure SSIS IR 位置。 |
| NodeSize                     | 您的 Azure SSIS IR 中的每個節點大小。 |
| NodeCount                    | 您的 Azure SSIS IR 中的節點數目。 |
| MaxParallelExecutionsPerNode | 在您的 Azure SSIS IR 中，每個節點的平行執行數目上限。 |
| CatalogServerEndpoint        | 要裝載 SSIS 目錄 (SSISDB) 的現有 Azure SQL Database 伺服器或受控實例的端點。 |
| CatalogAdminUserName         | 現有 Azure SQL Database 伺服器或受控實例的管理員使用者名稱。 ADF 會使用此資訊來代表您準備和管理 SSISDB。 |
| CatalogAdminPassword         | 現有 Azure SQL Database 伺服器或受控實例的管理員密碼。 |
| CatalogPricingTier           | Azure SQL Database 伺服器所裝載之 SSISDB 的定價層。  不適用於 Azure SQL 受控執行個體裝載 SSISDB。 |
| VNetId                       | 要聯結之 Azure SSIS IR 的虛擬網路資源識別碼。 |
| 子網路                       | 要聯結的 Azure SSIS IR 的子網名稱。 |
| 識別碼                           | 您的 Azure SSIS IR 的資源識別碼。 |
| 類型                         | IR 類型 (您的 Azure SSIS IR 的受控/自我裝載) 。 |
| resourceGroupName            | 您的 ADF 和 Azure SSIS IR 建立所在的 Azure 資源組名。 |
| DataFactoryName              | ADF 的名稱。 |
| 名稱                         | 您的 Azure SSIS IR 名稱。 |
| 描述                  | 您的 Azure SSIS IR 的描述。 |
  
#### <a name="status-per-azure-ssis-ir-node"></a>每個 Azure SSIS IR 節點的狀態 () 

下表提供 Azure SSIS IR 節點的可能狀態：

| 節點特定狀態 | 描述 |
| -------------------- | ----------- | 
| 啟動中             | 正在準備此節點。 |
| 可用            | 此節點已準備好部署/執行 SSIS 套件。 |
| 回收中            | 此節點正在修復/重新啟動。 |
| [無法使用]          | 此節點尚未準備好部署/執行 SSIS 套件，而且有可採取動作的錯誤/問題，您可以解決。 |

#### <a name="status-overall-azure-ssis-ir"></a>整體 Azure SSIS IR) 的狀態 (

下表提供 Azure SSIS IR 的可能整體狀態。 整體狀態會依屬於 Azure SSIS IR 的所有節點的結合狀態而定。 

| 整體狀態 | 描述 | 
| -------------- | ----------- | 
| Initial        | 您的 Azure SSIS IR 節點尚未配置/備妥。 | 
| 啟動中       | 您的 Azure SSIS IR 節點正在配置/備妥，並已開始計費。 |
| 已開始        | 您的 Azure SSIS IR 節點已配置/備妥，並已準備好部署/執行 SSIS 套件。 |
| 停止中       | 您的 Azure SSIS IR 的節點即將發行。 |
| 已停止        | 您的 Azure SSIS IR 節點已發行，並已停止計費。 |

### <a name="monitor-the-azure-ssis-integration-runtime-in-azure-portal"></a>在 Azure 入口網站中監視 Azure SSIS 整合執行時間

若要在 Azure 入口網站中監視您的 Azure SSIS IR，請移至 ADF UI 上 [**監視**] 中樞的 [**整合運行**時間] 頁面，您可以在其中看到所有的整合執行時間。

![監視所有整合執行時間](media/monitor-integration-runtime/monitor-integration-runtimes.png)

接下來，選取 Azure SSIS IR 的名稱以開啟其 [監視] 頁面，您可以在其中查看其整體/節點特定屬性和狀態。 在此頁面上，視您設定 Azure SSIS IR 的一般、部署和先進設定的方式而定，您會發現各種資訊/功能磚。  [**類型**] 和 [**區域**] 資訊磚會分別顯示 Azure SSIS IR 的類型和區域。 [**節點大小**] 資訊磚會顯示 SKU (SSIS edition_VM tier_VM 系列) 、CPU 核心數目，以及您的 AZURE SSIS IR 的每個節點的 RAM 大小。 [執行中 **/要求的節點 (S) **資訊] 磚會比較目前執行的節點數目與您的 AZURE SSIS IR 先前要求的節點總數。 下圖詳細說明功能磚。

![監視您的 Azure SSIS IR](media/monitor-integration-runtime/monitor-azure-ssis-integration-runtime.png)

#### <a name="status-tile"></a>狀態磚

在 [Azure SSIS IR 監視] 頁面的 [**狀態**] 磚上，您可以看到其整體狀態，**例如 [** 執行中] 或 [**已停止**]。 選取 [執行中] 狀態會彈出具有 [即時**停止**]**按鈕的視窗**，以停止您的 Azure SSIS IR。 選取 [**已停止**] 狀態會彈出具有 [即時**啟動**] 按鈕的視窗，以啟動您的 Azure SSIS IR。 快顯視窗也具有 [**執行 ssis 套件**] 按鈕，可使用在您的 AZURE ssis IR 上執行的 Execute ssis 套件活動來自動產生 ADF 管線 (請參閱在[adf 管線中將 ssis 套件執行為 execute ssis 套件活動](https://docs.microsoft.com/azure/data-factory/how-to-invoke-ssis-package-ssis-activity)) 和**資源識別碼**文字方塊，您可以從中複製 azure ssis ir 資源識別碼 (`/subscriptions/YourAzureSubscripton/resourcegroups/YourResourceGroup/providers/Microsoft.DataFactory/factories/YourADF/integrationruntimes/YourAzureSSISIR`) 。 包含 ADF 和 Azure SSIS IR 名稱的 Azure SSIS IR 資源識別碼的尾碼會形成一個叢集識別碼，可用來向獨立軟體廠商購買額外的高階/授權 SSIS 元件 (Isv) 並將其系結至您的 Azure SSIS IR (請參閱在[AZURE SSIS ir) 上安裝 premium/授權元件](https://docs.microsoft.com/azure/data-factory/how-to-develop-azure-ssis-ir-licensed-components)。

![監視您的 Azure SSIS IR-狀態圖格](media/monitor-integration-runtime/monitor-azure-ssis-integration-runtime-status.png)

#### <a name="ssisdb-server-endpoint-tile"></a>SSISDB 伺服器端點磚

如果您使用專案部署模型，其中封裝儲存在 Azure SQL Database 伺服器或受控實例所裝載的 SSISDB 中，您會在 [Azure SSIS IR 監視] 頁面上看到 [ **SSISDB 伺服器端點**] 磚， (參閱設定[您的 azure ssis ir 部署設定](https://docs.microsoft.com/azure/data-factory/tutorial-deploy-ssis-packages-azure#deployment-settings-page)) 。 在此磚上，您可以選取一個連結來指定您的 Azure SQL Database 伺服器或受控實例，以快顯視窗，您可以在其中從文字方塊複製伺服器端點，並在從 SSMS 連接時使用它來部署、設定、執行和管理您的封裝。 在快顯視窗中，您也可以選取 [**查看您的 Azure SQL Database 或受控實例設定**] 連結，在 Azure 入口網站中重新設定或調整 SSISDB 大小。

![監視您的 Azure SSIS IR-SSISDB 磚](media/monitor-integration-runtime/monitor-azure-ssis-integration-runtime-ssisdb.png)

#### <a name="proxy--staging-tile"></a>PROXY/暫存磚

如果您下載、安裝及設定自我裝載 IR (SHIR) 作為 Azure SSIS IR 的 proxy 以存取內部部署資料，您會在 Azure SSIS IR 監視頁面上看到 [ **proxy/暫存**] 磚 (請參閱將[SHIR 設定為 azure ssis ir](https://docs.microsoft.com/azure/data-factory/self-hosted-integration-runtime-proxy-ssis)) 的 proxy。 在此磚上，您可以選取一個連結來指定您的 SHIR，以開啟其 [監視] 頁面。 您也可以選取另一個連結，指定您的 Azure Blob 儲存體進行預備，以重新設定其連結服務。

#### <a name="validate-vnet--subnet-tile"></a>驗證 VNET/子網磚

如果您將 Azure SSIS IR 加入 VNet，您會在 Azure SSIS IR 監視頁面上看到 [**驗證 VNet/子網**] 磚 (查看將[您的 Azure ssis Ir 加入至 vnet](https://docs.microsoft.com/azure/data-factory/join-azure-ssis-integration-runtime-virtual-network)) 。 在此磚上，您可以選取一個連結來指定您的 VNet 和子網以快顯視窗，您可以在其中從文字方塊中複製 VNet 資源識別碼 (`/subscriptions/YourAzureSubscripton/resourceGroups/YourResourceGroup/providers/Microsoft.Network/virtualNetworks/YourARMVNet`) 和子網名稱，以及驗證您的 vnet 和子網設定，以確保您的 AZURE SSIS IR 所需的輸入/輸出網路流量和管理不會受到阻礙。

![監視您的 Azure SSIS IR-驗證磚](media/monitor-integration-runtime/monitor-azure-ssis-integration-runtime-validate.png)

#### <a name="diagnose-connectivity-tile"></a>診斷連線磚

在您的 Azure SSIS IR 監視頁面的 [**診斷**連線] 磚上，您可以選取 [**測試**連線] 連結以顯示視窗，您可以在其中檢查您的 azure SSIS ir 與相關套件/設定/資料存放區之間的連線，以及管理服務，透過其完整功能變數名稱 (FQDN) /ip 位址和指定的埠 (請參閱[從您的 Azure SSIS IR) 測試連接](https://docs.microsoft.com/azure/data-factory/ssis-integration-runtime-diagnose-connectivity-faq)。

![監視您的 Azure SSIS IR-診斷磚](media/monitor-integration-runtime/monitor-azure-ssis-integration-runtime-diagnose.png)

#### <a name="static-public-ip-addresses-tile"></a>靜態公用 IP 位址磚

如果您為 Azure SSIS IR 攜帶自己的靜態公用 IP 位址，您會在 Azure SSIS IR 監視頁面上看到 [**靜態公用 IP 位址**] 圖格 (查看[為 azure Ssis ir) 帶入您自己的靜態公用 ip 位址](https://docs.microsoft.com/azure/data-factory/join-azure-ssis-integration-runtime-virtual-network#publicIP)。 在此磚中，您可以選取連結來指定 Azure SSIS IR 的第一個/第二個靜態公用 IP 位址，以快顯視窗，您可以在其中從文字方塊複製其資源識別碼 (`/subscriptions/YourAzureSubscripton/resourceGroups/YourResourceGroup/providers/Microsoft.Network/publicIPAddresses/YourPublicIPAddress`) 。 在快顯視窗中，您也可以選取 [**查看您的第一個/第二個靜態公用 ip 位址設定**] 連結，以管理 Azure 入口網站中的第一個/第二個靜態公用 ip 位址。

![監視您的 Azure SSIS IR-診斷磚](media/monitor-integration-runtime/monitor-azure-ssis-integration-runtime-static.png)

#### <a name="package-stores-tile"></a>封裝存放區磚

如果您使用封裝部署模型，其中封裝儲存在檔案系統/Azure 檔案儲存體/SQL Server 資料庫 (MSDB) 裝載于您的 Azure SQL 受控執行個體，並透過 Azure SSIS IR 套件存放區進行管理，您會在 Azure SSIS IR 監視頁面上看到 [**套件存放區**] 磚 (查看[設定您的 azure ssis ir 部署設定](https://docs.microsoft.com/azure/data-factory/tutorial-deploy-ssis-packages-azure#deployment-settings-page)) 。 在此磚上，您可以選取連結以指定附加至您的 Azure SSIS IR 的封裝存放區數目，以快顯視窗，您可以在其中為您的 azure SSIS IR 套件存放區重新設定相關的已連結服務，其位於 Azure SQL 受控執行個體所裝載的檔案系統/Azure 檔案儲存體/MSDB 上。

![監視您的 Azure SSIS IR-封裝磚](media/monitor-integration-runtime/monitor-azure-ssis-integration-runtime-package.png)

#### <a name="errors-tile"></a> (S) 磚時發生錯誤

如果您的 Azure SSIS IR 的啟動/停止/維護/升級發生問題，您會在 [Azure SSIS IR 監視] 頁面上看到額外的**錯誤 (S) ** ] 磚。 在此磚中，您可以選取一個連結，指定您的 Azure SSIS IR 所產生的錯誤數目以快顯視窗，您可以在此查看這些錯誤的詳細資料，並將其複製以尋找疑難排解指南中的建議解決方案 (參閱針對[您的 AZURE SSIS IR) 進行疑難排解](https://docs.microsoft.com/azure/data-factory/ssis-integration-runtime-management-troubleshoot)。

![監視您的 Azure SSIS IR-診斷磚](media/monitor-integration-runtime/monitor-azure-ssis-integration-runtime-error.png)

### <a name="monitor-the-azure-ssis-integration-runtime-with-azure-monitor"></a>使用 Azure 監視器監視 Azure SSIS 整合執行時間

若要使用 Azure 監視器監視您的 Azure SSIS IR，請參閱[使用 Azure 監視器監視 ssis 作業](https://docs.microsoft.com/azure/data-factory/monitor-using-azure-monitor#monitor-ssis-operations-with-azure-monitor)。

### <a name="more-info-about-the-azure-ssis-integration-runtime"></a>深入了解 Azure-SSIS 整合執行階段

請參閱下列文章以深入了解 Azure SSIS 整合執行階段：

- [Azure-SSIS Integration Runtime](concepts-integration-runtime.md#azure-ssis-integration-runtime)。 本文提供整合執行階段的一般概念性資訊，包括 Azure-SSIS IR。 
- [教學課程：將 SSIS 套件部署至 Azure](tutorial-create-azure-ssis-runtime-portal.md)。 本文提供逐步指示，說明如何建立您的 Azure SSIS IR，並使用 Azure SQL Database 來裝載 SSIS 目錄 (SSISDB) 。 
- [如何：建立 Azure-SSIS 整合執行階段](create-azure-ssis-integration-runtime.md)。 本文會針對教學課程進行擴充，並提供使用 Azure SQL 受控執行個體來裝載 SSISDB 的指示。 
- [管理 Azure-SSIS IR](manage-azure-ssis-integration-runtime.md). 本文說明如何啟動、停止或刪除您的 Azure SSIS IR。 它也會說明如何藉由新增更多節點來相應放大。 
- [將 Azure-SSIS IR 加入虛擬網路](join-azure-ssis-integration-runtime-virtual-network.md)。 本文提供將您的 Azure SSIS IR 加入虛擬網路的指示。

## <a name="next-steps"></a>後續步驟
請參閱以下文章，以不同的方式監視管線： 

- [快速入門：建立 data factory](quickstart-create-data-factory-dot-net.md)。
- [使用 Azure 監視器監視 Data Factory 管線](monitor-using-azure-monitor.md)