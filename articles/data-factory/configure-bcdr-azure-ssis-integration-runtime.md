---
title: 設定 SQL Database 容錯移轉的 Azure SSIS Integration Runtime
description: 本文說明如何為 SSISDB 資料庫設定具有 Azure SQL Database 異地複寫和容錯移轉的 Azure-SSIS Integration Runtime
services: data-factory
ms.service: data-factory
ms.workload: data-services
ms.devlang: powershell
author: swinarko
ms.author: sawinark
manager: mflasko
ms.reviewer: douglasl
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 04/09/2020
ms.openlocfilehash: 39d55d4372f03a1625bb04d8377ed6533401e281
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "82188717"
---
# <a name="configure-the-azure-ssis-integration-runtime-with-azure-sql-database-geo-replication-and-failover"></a>設定具有 Azure SQL Database 異地複寫和容錯移轉的 Azure-SSIS Integration Runtime

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

本文說明如何為 SSISDB 資料庫設定具有 Azure SQL Database 異地複寫的 Azure-SSIS Integration Runtime。 發生容錯移轉時，您可以確保 Azure-SSIS IR 仍會使用次要資料庫來運作。

如需 SQL Database 異地複寫和容錯移轉的詳細資訊，請參閱[概觀：主動式異地複寫和自動容錯移轉群組](../sql-database/sql-database-geo-replication-overview.md)。

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="azure-ssis-ir-failover-with-azure-sql-database-managed-instance"></a>使用 Azure SQL Database 受控執行個體的 Azure SSIS IR 容錯移轉

### <a name="prerequisites"></a>先決條件
1. 在主要實例上的 SSISDB 上執行下列命令。 此步驟會新增新的加密密碼。
```sql
  ALTER MASTER KEY ADD ENCRYPTION BY PASSWORD = 'password'
```

2. 在 Azure SQL Database 受控執行個體上建立容錯移轉群組。

3. 使用新的加密密碼，在次要實例上執行**sp_control_dbmasterkey_password** 。
```sql
  EXEC sp_control_dbmasterkey_password @db_name = N'SSISDB',   
    @password = N'<password>', @action = N'add';  
  GO
```

### <a name="solution"></a>解決方法
發生容錯移轉時，如果您想要在主要區域上使用現有的 Azure SSIS IR：
1. 停止主要區域上的 Azure SSIS IR。

2. 使用次要實例的新區域、端點和 VNET 資訊來編輯 Azure SSIS IR。

```powershell
  Set-AzDataFactoryV2IntegrationRuntime -Location "new region" `
                -CatalogServerEndpoint "Azure SQL Database server endpoint" `
                -CatalogAdminCredential "Azure SQL Database server admin credentials" `
                -VNetId "new VNet" `
                -Subnet "new subnet" `
                -SetupScriptContainerSasUri "new custom setup SAS URI"
```

3. 重新開機 Azure SSIS IR。

4. 將 SSIS 封裝的**ConnectionManager**中的伺服器名稱變更為次要實例伺服器名稱，然後重新部署這些套件並執行。

如果您想要在次要區域上布建新的 Azure SSIS IR：
> [!NOTE]
> 步驟4（IR 的建立）必須透過 PowerShell 來完成。 Azure 入口網站會報告錯誤，指出 SSISDB 已經存在。
1. 停止主要區域上的 Azure SSIS IR。

2. 執行預存程式以更新 SSISDB 中的中繼資料，以接受來自** \<new_data_factory_name\> **和** \<new_integration_runtime_name\>** 的連接。
   
```SQL
  EXEC [catalog].[failover_integration_runtime] @data_factory_name='<new_data_factory_name>', @integration_runtime_name='<new_integration_runtime_name>'
```

3. 在新區域中建立名** \<為\> new_data_factory_name**的新 data factory。 如需詳細資訊，請參閱建立資料處理站。

```powershell
  Set-AzDataFactoryV2 -ResourceGroupName "new resource group name" `
                      -Location "new region"`
                      -Name "<new_data_factory_name>"
```
  如需此 PowerShell 命令的詳細資訊，請參閱[使用 PowerShell 建立 Azure data factory](quickstart-create-data-factory-powershell.md)

4. 使用 Azure PowerShell，在新的區域中建立名為** \<new_integration_runtime_name\> **的新 Azure SSIS IR。

```powershell
  Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName "new resource group name" `
                                           -DataFactoryName "new data factory name" `
                                           -Name "<new_integration_runtime_name>" `
                                           -Description $AzureSSISDescription `
                                           -Type Managed `
                                           -Location $AzureSSISLocation `
                                           -NodeSize $AzureSSISNodeSize `
                                           -NodeCount $AzureSSISNodeNumber `
                                           -Edition $AzureSSISEdition `
                                           -LicenseType $AzureSSISLicenseType `
                                           -MaxParallelExecutionsPerNode $AzureSSISMaxParallelExecutionsPerNode `
                                           -VnetId "new vnet" `
                                           -Subnet "new subnet" `
                                           -CatalogServerEndpoint $SSISDBServerEndpoint `
                                           -CatalogPricingTier $SSISDBPricingTier
```

  如需此 PowerShell 命令的詳細資訊，請參閱[在 Azure Data Factory 中建立 Azure-SSIS 整合執行階段](create-azure-ssis-integration-runtime.md)

5. 將 SSIS 封裝的**ConnectionManager**中的伺服器名稱變更為次要實例伺服器名稱，然後重新部署這些套件並執行。



## <a name="azure-ssis-ir-failover-with-azure-sql-database"></a>使用 Azure SQL Database 的 Azure SSIS IR 容錯移轉

### <a name="scenario-1---azure-ssis-ir-is-pointing-to-read-write-listener-endpoint"></a>案例 1 - Azure-SSIS IR 指向讀寫接聽程式端點

#### <a name="conditions"></a>條件

下列條件成立時，本節便適用：

- Azure-SSIS IR 指向容錯移轉群組的讀寫接聽程式端點。

  AND

- SQL Database 伺服器不是** 使用虛擬網路服務端點規則來設定的。

#### <a name="solution"></a>解決方法

發生容錯移轉時，Azure-SSIS IR 不會察覺。 Azure-SSIS IR 會自動連線到容錯移轉群組的新主要伺服器。


### <a name="scenario-2---azure-ssis-ir-is-pointing-to-primary-server-endpoint"></a>案例 2 - Azure-SSIS IR 指向主要伺服器端點

#### <a name="conditions"></a>條件

下列其中一個條件成立時，本節便適用：

- Azure-SSIS IR 指向容錯移轉群組的主要伺服器端點。 發生容錯移轉時，此端點會變更。

  或者

- Azure SQL Database 伺服器是使用虛擬網路服務端點規則來設定的。


#### <a name="solution"></a>解決方法

1. 停止主要區域上的 Azure SSIS IR。

2. 使用次要實例的新區域、端點和 VNET 資訊來編輯 Azure SSIS IR。

```powershell
  Set-AzDataFactoryV2IntegrationRuntime -Location "new region" `
                    -CatalogServerEndpoint "Azure SQL Database server endpoint" `
                    -CatalogAdminCredential "Azure SQL Database server admin credentials" `
                    -VNetId "new VNet" `
                    -Subnet "new subnet" `
                    -SetupScriptContainerSasUri "new custom setup SAS URI"
```

3. 重新開機 Azure SSIS IR。

4. 將 SSIS 封裝的**ConnectionManager**中的伺服器名稱變更為次要實例伺服器名稱，然後重新部署這些套件並執行。


### <a name="scenario-3---attaching-an-existing-ssisdb-ssis-catalog-to-a-new-azure-ssis-ir"></a>案例 3-將現有的 SSISDB （SSIS 目錄）附加至新的 Azure SSIS IR

當目前區域中發生 ADF 或 Azure SSIS IR 嚴重損壞時，您可以讓 SSISDB 在新的區域中持續使用新的 Azure SSIS IR。

#### <a name="solution"></a>解決方法

> [!NOTE]
> 步驟4（IR 的建立）必須透過 PowerShell 來完成。 Azure 入口網站會報告錯誤，指出 SSISDB 已經存在。

1. 停止主要區域上的 Azure SSIS IR。

2. 執行預存程式以更新 SSISDB 中的中繼資料，以接受來自** \<new_data_factory_name\> **和** \<new_integration_runtime_name\>** 的連接。
   
```SQL
  EXEC [catalog].[failover_integration_runtime] @data_factory_name='<new_data_factory_name>', @integration_runtime_name='<new_integration_runtime_name>'
```

3. 在新區域中建立名** \<為\> new_data_factory_name**的新 data factory。 如需詳細資訊，請參閱建立資料處理站。

```powershell
  Set-AzDataFactoryV2 -ResourceGroupName "new resource group name" `
                         -Location "new region"`
                         -Name "<new_data_factory_name>"
```
  如需此 PowerShell 命令的詳細資訊，請參閱[使用 PowerShell 建立 Azure data factory](quickstart-create-data-factory-powershell.md)

4. 使用 Azure PowerShell，在新的區域中建立名為** \<new_integration_runtime_name\> **的新 Azure SSIS IR。

```powershell
  Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName "new resource group name" `
                                           -DataFactoryName "new data factory name" `
                                           -Name "<new_integration_runtime_name>" `
                                           -Description $AzureSSISDescription `
                                           -Type Managed `
                                           -Location $AzureSSISLocation `
                                           -NodeSize $AzureSSISNodeSize `
                                           -NodeCount $AzureSSISNodeNumber `
                                           -Edition $AzureSSISEdition `
                                           -LicenseType $AzureSSISLicenseType `
                                           -MaxParallelExecutionsPerNode $AzureSSISMaxParallelExecutionsPerNode `
                                           -VnetId "new vnet" `
                                           -Subnet "new subnet" `
                                           -CatalogServerEndpoint $SSISDBServerEndpoint `
                                           -CatalogPricingTier $SSISDBPricingTier
```

  如需此 PowerShell 命令的詳細資訊，請參閱[在 Azure Data Factory 中建立 Azure-SSIS 整合執行階段](create-azure-ssis-integration-runtime.md)

5. 將 SSIS 封裝的**ConnectionManager**中的伺服器名稱變更為次要實例伺服器名稱，然後重新部署這些套件並執行。


## <a name="next-steps"></a>後續步驟

請考量 Azure-SSIS 整合執行階段的下列其他組態選項：

- [設定 Azure-SSIS Integration Runtime 以獲得高效能](configure-azure-ssis-integration-runtime-performance.md)

- [自訂 Azure-SSIS 整合執行階段的安裝](how-to-configure-azure-ssis-ir-custom-setup.md)

- [佈建 Azure-SSIS 整合執行階段的企業版](how-to-configure-azure-ssis-ir-enterprise-edition.md)
