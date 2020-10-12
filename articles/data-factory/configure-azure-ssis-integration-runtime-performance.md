---
title: 設定 Azure-SSIS Integration Runtime 的效能
description: 了解如何設定 Azure-SSIS Integration Runtime 的屬性，以獲得高效能
services: data-factory
ms.date: 01/10/2018
ms.topic: conceptual
ms.service: data-factory
ms.workload: data-services
author: swinarko
ms.author: sawinark
ms.reviewer: ''
manager: anandsub
ms.openlocfilehash: db50049675766d9fd8a018c8730f48ac34e23bfc
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "91276657"
---
# <a name="configure-the-azure-ssis-integration-runtime-for-high-performance"></a>設定 Azure-SSIS Integration Runtime 以獲得高效能

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]


本文說明如何設定 Azure-SSIS Integration Runtime (IR) 以獲得高效能。 Azure-SSIS IR 可讓您在 Azure 中部署及執行 SQL Server Integration Services (SSIS) 套件。 如需 Azure-SSIS IR 的詳細資訊，請參閱[整合執行階段](concepts-integration-runtime.md#azure-ssis-integration-runtime)一文。 如需在 Azure 上部署及執行 SSIS 套件的相關資訊，請參閱[將 SQL Server Integration Services 工作負載隨即轉移至雲端](/sql/integration-services/lift-shift/ssis-azure-lift-shift-ssis-packages-overview)。

> [!IMPORTANT]
> 本文包含 SSIS 開發小組成員所完成之內部測試的效能結果與觀察。 您的結果可能會有所不同。 在您完成組態設定之前執行您自己的測試，這會同時影響成本和效能。

## <a name="properties-to-configure"></a>要設定的屬性

下列設定指令碼部分會顯示當您建立 Azure-SSIS Integration Runtime 時，您可以設定的屬性。 如需完整的 PowerShell 指令碼與描述，請參閱[將 SQL Server Integration Services 套件部署至 Azure](tutorial-deploy-ssis-packages-azure-powershell.md)。

```powershell
# If your input contains a PSH special character, e.g. "$", precede it with the escape character "`" like "`$"
$SubscriptionName = "[your Azure subscription name]"
$ResourceGroupName = "[your Azure resource group name]"
$DataFactoryName = "[your data factory name]"
# For supported regions, see https://azure.microsoft.com/global-infrastructure/services/?products=data-factory&regions=all
$DataFactoryLocation = "EastUS"

### Azure-SSIS integration runtime information - This is a Data Factory compute resource for running SSIS packages
$AzureSSISName = "[specify a name for your Azure-SSIS IR]"
$AzureSSISDescription = "[specify a description for your Azure-SSIS IR]"
# For supported regions, see https://azure.microsoft.com/global-infrastructure/services/?products=data-factory&regions=all
$AzureSSISLocation = "EastUS"
# For supported node sizes, see https://azure.microsoft.com/pricing/details/data-factory/ssis/
$AzureSSISNodeSize = "Standard_D8_v3"
# 1-10 nodes are currently supported
$AzureSSISNodeNumber = 2
# Azure-SSIS IR edition/license info: Standard or Enterprise
$AzureSSISEdition = "Standard" # Standard by default, while Enterprise lets you use advanced/premium features on your Azure-SSIS IR
# Azure-SSIS IR hybrid usage info: LicenseIncluded or BasePrice
$AzureSSISLicenseType = "LicenseIncluded" # LicenseIncluded by default, while BasePrice lets you bring your existing SQL Server license with Software Assurance to earn cost savings from Azure Hybrid Benefit (AHB) option
# For a Standard_D1_v2 node, up to 4 parallel executions per node are supported, but for other nodes, up to max(2 x number of cores, 8) are currently supported
$AzureSSISMaxParallelExecutionsPerNode = 8
# Custom setup info
$SetupScriptContainerSasUri = "" # OPTIONAL to provide SAS URI of blob container where your custom setup script and its associated files are stored
# Virtual network info: Classic or Azure Resource Manager
$VnetId = "[your virtual network resource ID or leave it empty]" # REQUIRED if you use Azure SQL Database with virtual network service endpoints/SQL Managed Instance/on-premises data, Azure Resource Manager virtual network is recommended, Classic virtual network will be deprecated soon
$SubnetName = "[your subnet name or leave it empty]" # WARNING: Please use the same subnet as the one used with your Azure SQL Database with virtual network service endpoints or a different subnet than the one used for your SQL Managed Instance

### SSISDB info
$SSISDBServerEndpoint = "[your server name or managed instance name.DNS prefix].database.windows.net" # WARNING: Please ensure that there is no existing SSISDB, so we can prepare and manage one on your behalf
# Authentication info: SQL or Azure Active Directory (AAD)
$SSISDBServerAdminUserName = "[your server admin username for SQL authentication or leave it empty for AAD authentication]"
$SSISDBServerAdminPassword = "[your server admin password for SQL authentication or leave it empty for AAD authentication]"
$SSISDBPricingTier = "[Basic|S0|S1|S2|S3|S4|S6|S7|S9|S12|P1|P2|P4|P6|P11|P15|…|ELASTIC_POOL(name = <elastic_pool_name>) for Azure SQL Database or leave it empty for SQL Managed Instance]"
```

## <a name="azuressislocation"></a>AzureSSISLocation
**AzureSSISLocation** 是整合執行階段背景工作節點的位置。 背景工作節點會在 Azure SQL Database 中維護 SSIS 目錄資料庫 (SSISDB) 的固定連接。 將 **AzureSSISLocation** 設定為與裝載 SSISDB 的 [邏輯 SQL server](../azure-sql/database/logical-servers.md) 相同的位置，讓整合執行時間盡可能有效率地運作。

## <a name="azuressisnodesize"></a>AzureSSISNodeSize
Data Factory (包括 Azure-SSIS IR) 支援下列選項：
-   Standard\_A4\_v2
-   Standard\_A8\_v2
-   Standard\_D1\_v2
-   Standard\_D2\_v2
-   Standard\_D3\_v2
-   Standard\_D4\_v2
-   標準 \_ D2 \_ v3
-   標準 \_ D4 \_ v3
-   標準 \_ D8 \_ v3
-   標準 \_ D16 \_ v3
-   標準 \_ D32 \_ v3
-   標準 \_ D64 \_ v3
-   標準 \_ E2 \_ v3
-   標準 \_ E4 \_ v3
-   標準 \_ E8 \_ v3
-   標準 \_ E16 \_ v3
-   標準 \_ E32 \_ v3
-   標準 \_ E64 \_ v3

在由 SSIS 工程團隊進行的非官方內部測試中，D 系列似乎比 A 系列更適合 SSIS 套件執行。

-   D 系列的效能/價格比率高於 A 數列，而 v3 系列的效能/價格比率高於 v2 系列。
-   D 系列的輸送量高於具有相同價格的數列，而 v3 系列的輸送量高於相同價格的 v2 系列。
-   Azure-SSIS IR 的 v2 系列節點不適合自訂安裝，因此請改用 v3 系列節點。 如果您已經使用 v2 系列節點，請儘快切換為使用 v3 系列節點。
-   E 系列是記憶體優化的 VM 大小，可提供比其他電腦更高的記憶體與 CPU 比例。如果您的套件需要大量的記憶體，您可以考慮選擇 E 系列 VM。

### <a name="configure-for-execution-speed"></a>針對執行速度進行設定
如果您不需要執行許多套件，且希望套件可以快速執行，請使用下列圖表中的資訊，以選擇適合您案例的虛擬機器類型。

此資料表示單一背景工作節點上的單一套件執行。 封裝會從 Azure Blob 儲存體載入具有名字和姓氏資料行的3000000記錄、產生完整名稱資料行，並將全名超過20個字元的記錄寫入 Azure Blob 儲存體。

Y 軸是在一小時內完成執行的封裝數目。 請注意，這只是一個記憶體取用封裝的測試結果。 如果您想要知道封裝的輸送量，建議您自行執行測試。

![SSIS Integration Runtime 套件執行速度](media/configure-azure-ssis-integration-runtime-performance/ssisir-execution-speedV2.png)

### <a name="configure-for-overall-throughput"></a>針對整體輸送量進行設定

如果您需要執行許多套件，且很重視整體輸送量，請使用下列圖表中的資訊，以選擇適合您案例的虛擬機器類型。

Y 軸是在一小時內完成執行的封裝數目。 請注意，這只是一個記憶體取用封裝的測試結果。 如果您想要知道封裝的輸送量，建議您自行執行測試。

![SSIS Integration Runtime 最大整體輸送量](media/configure-azure-ssis-integration-runtime-performance/ssisir-overall-throughputV2.png)

## <a name="azuressisnodenumber"></a>AzureSSISNodeNumber

**AzureSSISNodeNumber** 會調整整合執行階段的延展性。 整合執行階段的輸送量是與 **AzureSSISNodeNumber** 成比例。 一開始先將 **AzureSSISNodeNumber** 設定為較小的值、監視整合執行階段的輸送量，然後根據您的案例調整值。 若要重新設定背景工作節點計數，請參閱[管理 Azure-SSIS Integration Runtime](manage-azure-ssis-integration-runtime.md)。

## <a name="azuressismaxparallelexecutionspernode"></a>AzureSSISMaxParallelExecutionsPerNode

當您已經使用功能強大的背景工作節點來執行套件時，增加 **AzureSSISMaxParallelExecutionsPerNode** 可能會增加整合執行階段的整體輸送量。 如果您想要增加最大值，您需要使用 Azure PowerShell 來更新 **>azuressismaxparallelexecutionspernode**。 您可以根據套件的成本和背景工作角色節點的下列設定，評估適當的值。 如需詳細資訊，請參閱[一般用途的虛擬機器大小](../virtual-machines/windows/sizes-general.md)。

| 大小             | vCPU | 記憶體：GiB | 暫存儲存體 (SSD) GiB | 最大暫存儲存體輸送量：IOPS / 讀取 MBps / 寫入 MBps | 最大資料磁碟/輸送量：IOPS | 最大 NIC/預期的網路效能 (Mbps) |
|------------------|------|-------------|------------------------|------------------------------------------------------------|-----------------------------------|------------------------------------------------|
| Standard\_D1\_v2 | 1    | 3.5         | 50                     | 3000 / 46 / 23                                             | 2 / 2x500                         | 2 / 750                                        |
| Standard\_D2\_v2 | 2    | 7           | 100                    | 6000 / 93 / 46                                             | 4 / 4x500                         | 2 / 1500                                       |
| Standard\_D3\_v2 | 4    | 14          | 200                    | 12000 / 187 / 93                                           | 8 / 8x500                         | 4 / 3000                                       |
| Standard\_D4\_v2 | 8    | 28          | 400                    | 24000 / 375 / 187                                          | 16 / 16x500                       | 8 / 6000                                       |
| Standard\_A4\_v2 | 4    | 8           | 40                     | 4000 / 80 / 40                                             | 8 / 8x500                         | 4 / 1000                                       |
| Standard\_A8\_v2 | 8    | 16          | 80                     | 8000 / 160 / 80                                            | 16 / 16x500                       | 8 / 2000                                       |
| 標準 \_ D2 \_ v3 | 2    | 8           | 50                     | 3000 / 46 / 23                                             | 4/6x500                         | 2 / 1000                                       |
| 標準 \_ D4 \_ v3 | 4    | 16          | 100                    | 6000 / 93 / 46                                             | 8/12x500                        | 2 / 2000                                       |
| 標準 \_ D8 \_ v3 | 8    | 32          | 200                    | 12000 / 187 / 93                                           | 16/24x500                       | 4 / 4000                                       |
| 標準 \_ D16 \_ v3| 16   | 64          | 400                    | 24000 / 375 / 187                                          | 32/48x500                        | 8 / 8000                                       |
| 標準 \_ D32 \_ v3| 32   | 128         | 800                    | 48000 / 750 / 375                                          | 32/96x500                       | 8 / 16000                                      |
| 標準 \_ D64 \_ v3| 64   | 256         | 1600                   | 96000/1000/500                                         | 32/192x500                      | 8 / 30000                                      |
| 標準 \_ E2 \_ v3 | 2    | 16          | 50                     | 3000 / 46 / 23                                             | 4/6x500                         | 2 / 1000                                       |
| 標準 \_ E4 \_ v3 | 4    | 32          | 100                    | 6000 / 93 / 46                                             | 8/12x500                        | 2 / 2000                                       |
| 標準 \_ E8 \_ v3 | 8    | 64          | 200                    | 12000 / 187 / 93                                           | 16/24x500                       | 4 / 4000                                       |
| 標準 \_ E16 \_ v3| 16   | 128         | 400                    | 24000 / 375 / 187                                          | 32/48x500                       | 8 / 8000                                       |
| 標準 \_ E32 \_ v3| 32   | 256         | 800                    | 48000 / 750 / 375                                          | 32/96x500                       | 8 / 16000                                      |
| 標準 \_ E64 \_ v3| 64   | 432         | 1600                   | 96000/1000/500                                         | 32/192x500                      | 8 / 30000                                      |

以下是為 **AzureSSISMaxParallelExecutionsPerNode** 屬性設定正確值的指導方針： 

1. 一開始請將它設定為較小的值。
2. 少量增加，以檢查整體輸送量是否已獲得改善。
3. 當整體輸送量達到最大值時，停止增加此值。

## <a name="ssisdbpricingtier"></a>SSISDBPricingTier

**SSISDBPricingTier** 是 Azure SQL Database 中 (SSISDB) 之 SSIS 目錄資料庫的定價層。 此設定會影響 IR 執行個體中的背景工作數目上限、將套件執行排入佇列的速度，以及載入執行記錄的速度。

-   如果您不在意將套件執行排入佇列的速度和載入執行記錄的速度，則可以選擇最低資料庫定價層。 使用基本定價的 Azure SQL Database 支援一個整合執行階段執行個體中 8 個背景工作。

-   如果背景工作計數超過 8 個或核心計數超過 50 個，則請選擇比「基本」更強大的資料庫。 否則，資料庫會變成整合執行階段執行個體的瓶頸，而且會嚴重影響整體效能。

-   如果記錄層級設定為 [詳細資訊]，請選擇較強大的資料庫（例如 s3）。 根據我們的非官方內部測試，s3 定價層可支援具有2個節點的 SSIS 套件執行、128並行計數和詳細資訊記錄層級。

您也可以根據 Azure 入口網站上提供的[資料庫交易單位](../sql-database/sql-database-what-is-a-dtu.md) (DTU) 使用量資訊，調整資料庫定價層。

## <a name="design-for-high-performance"></a>為高效能而設計
設計一個在 Azure 上執行的 SSIS 套件，不同於設計一個內部部署執行的套件。 相對於將多個獨立工作結合在相同的套件中，Azure-SSIS IR 中將它們分成數個套件，以求更有效率地執行。 針對每個套件建立套件執行，這樣一來就不用等待彼此完成。 這種方法受益於 Azure-SSIS Integration Runtime 的延展性，並可改善整體輸送量。

## <a name="next-steps"></a>後續步驟
深入了解 Azure-SSIS Integration Runtime。 請參閱 [ Azure-SSIS Integration Runtime](concepts-integration-runtime.md#azure-ssis-integration-runtime)。
