---
title: 設定 SQL Database 容錯移轉的 Azure SSIS 整合執行時間
description: 本文說明如何為 SSISDB 資料庫設定具有 Azure SQL Database 異地複寫和容錯移轉的 Azure SSIS 整合執行時間
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
ms.openlocfilehash: 795247cd0d6adfd27115b73c1d0de02e6810d670
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/12/2020
ms.locfileid: "83201149"
---
# <a name="configure-the-azure-ssis-integration-runtime-with-sql-database-geo-replication-and-failover"></a>使用 SQL Database 異地複寫和容錯移轉設定 Azure SSIS 整合執行時間

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-xxx-md.md)]

本文說明如何為 SSISDB 資料庫設定具有 Azure SQL Database 異地複寫的 Azure SSIS 整合執行時間（IR）。 發生容錯移轉時，您可以確保 Azure-SSIS IR 仍會使用次要資料庫來運作。

如需 SQL Database 異地複寫和容錯移轉的詳細資訊，請參閱[概觀：主動式異地複寫和自動容錯移轉群組](../sql-database/sql-database-geo-replication-overview.md)。

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="azure-ssis-ir-failover-with-a-sql-database-managed-instance"></a>使用 SQL Database 受控實例的 Azure SSIS IR 容錯移轉

### <a name="prerequisites"></a>先決條件

Azure SQL Database 受控實例會使用*資料庫主要金鑰（DMK）* 來協助保護儲存在資料庫中的資料、認證和連接資訊。 若要啟用 DMK 的自動解密，則會透過*伺服器主要金鑰（SMK）* 加密金鑰的複本。 

SMK 不會在容錯移轉群組中複寫。 在容錯移轉之後，您必須在主要和次要實例上新增密碼，以進行 DMK 解密。

1. 針對主要實例上的 SSISDB 執行下列命令。 此步驟會加入新的加密密碼。

    ```sql
    ALTER MASTER KEY ADD ENCRYPTION BY PASSWORD = 'password'
    ```

2. 在 Azure SQL Database 受控實例上建立容錯移轉群組。

3. 使用新的加密密碼，在次要實例上執行**sp_control_dbmasterkey_password** 。

    ```sql
    EXEC sp_control_dbmasterkey_password @db_name = N'SSISDB',   
        @password = N'<password>', @action = N'add';  
    GO
    ```

### <a name="scenario-1-azure-ssis-ir-is-pointing-to-a-readwrite-listener-endpoint"></a>案例1： Azure SSIS IR 指向讀取/寫入接聽程式端點

如果您想要讓 Azure SSIS IR 指向讀取/寫入接聽程式端點，您必須先指向主伺服器端點。 將 SSISDB 放在容錯移轉群組之後，您可以變更為讀取/寫入接聽程式端點，並重新啟動 Azure SSIS IR。

#### <a name="solution"></a>解決方法

發生容錯移轉時，請採取下列步驟：

1. 停止主要區域中的 Azure SSIS IR。

2. 針對次要實例上的自訂設定，編輯具有新區域、虛擬網路及共用存取簽章（SAS） URI 資訊的 Azure SSIS IR。 因為 Azure SSIS IR 指向讀取/寫入接聽程式，而端點對 Azure SSIS IR 而言是透明的，所以您不需要編輯端點。

    ```powershell
    Set-AzDataFactoryV2IntegrationRuntime -Location "new region" `
                -VNetId "new VNet" `
                -Subnet "new subnet" `
                -SetupScriptContainerSasUri "new custom setup SAS URI"
    ```

3. 重新開機 Azure SSIS IR。

### <a name="scenario-2-azure-ssis-ir-is-pointing-to-a-primary-server-endpoint"></a>案例2： Azure SSIS IR 指向主伺服器端點

如果 Azure SSIS IR 指向主伺服器端點，則適用此案例。

#### <a name="solution"></a>解決方法

發生容錯移轉時，請採取下列步驟：

1. 停止主要區域中的 Azure SSIS IR。

2. 使用次要實例的新區域、端點和虛擬網路資訊來編輯 Azure SSIS IR。

    ```powershell
    Set-AzDataFactoryV2IntegrationRuntime -Location "new region" `
                -CatalogServerEndpoint "Azure SQL Database server endpoint" `
                -CatalogAdminCredential "Azure SQL Database server admin credentials" `
                -VNetId "new VNet" `
                -Subnet "new subnet" `
                -SetupScriptContainerSasUri "new custom setup SAS URI"
    ```

3. 重新開機 Azure SSIS IR。

### <a name="scenario-3-azure-ssis-ir-is-pointing-to-a-public-endpoint-of-a-sql-database-managed-instance"></a>案例3： Azure SSIS IR 指向 SQL Database 受控實例的公用端點

如果 Azure SSIS IR 指向 Azure SQL Database 受控實例的公用端點，而且它不會加入至虛擬網路，則適用此案例。 與案例2唯一不同的是，您不需要在容錯移轉之後編輯 Azure SSIS IR 的虛擬網路資訊。

#### <a name="solution"></a>解決方法

發生容錯移轉時，請採取下列步驟：

1. 停止主要區域中的 Azure SSIS IR。

2. 使用次要實例的新區域和端點資訊來編輯 Azure SSIS IR。

    ```powershell
    Set-AzDataFactoryV2IntegrationRuntime -Location "new region" `
                -CatalogServerEndpoint "Azure SQL Database server endpoint" `
                -CatalogAdminCredential "Azure SQL Database server admin credentials" `
                -SetupScriptContainerSasUri "new custom setup SAS URI"
    ```

3. 重新開機 Azure SSIS IR。

### <a name="scenario-4-attach-an-existing-ssisdb-instance-ssis-catalog-to-a-new-azure-ssis-ir"></a>案例4：將現有的 SSISDB 實例（SSIS 目錄）附加至新的 Azure SSIS IR

如果您想要 SSISDB 在新區域中使用新的 Azure SSIS IR，當目前的區域發生 Azure Data Factory 或 Azure SSIS IR 嚴重損壞時，適用此案例。

#### <a name="solution"></a>解決方法

發生容錯移轉時，請採取下列步驟。

> [!NOTE]
> 在步驟4中使用 PowerShell （建立 IR）。 如果您沒有這麼做，Azure 入口網站將會報告指出 SSISDB 已經存在的錯誤。

1. 停止主要區域中的 Azure SSIS IR。

2. 執行預存程式以更新 SSISDB 中的中繼資料，以接受來自** \< new_data_factory_name \> **和** \< new_integration_runtime_name \> **的連接。
   
    ```sql
    EXEC [catalog].[failover_integration_runtime] @data_factory_name='<new_data_factory_name>', @integration_runtime_name='<new_integration_runtime_name>'
    ```

3. 在新區域中建立名為** \< new_data_factory_name \> **的新 data factory。

    ```powershell
    Set-AzDataFactoryV2 -ResourceGroupName "new resource group name" `
                      -Location "new region"`
                      -Name "<new_data_factory_name>"
    ```
    
    如需此 PowerShell 命令的詳細資訊，請參閱[使用 PowerShell 建立 Azure data factory](quickstart-create-data-factory-powershell.md)。

4. 使用 Azure PowerShell，在新的區域中建立名為** \< new_integration_runtime_name \> **的新 Azure SSIS IR。

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

    如需此 PowerShell 命令的詳細資訊，請參閱[在 Azure Data Factory 中建立 AZURE SSIS 整合運行](create-azure-ssis-integration-runtime.md)時間。



## <a name="azure-ssis-ir-failover-with-sql-database"></a>使用 SQL Database 的 Azure SSIS IR 容錯移轉

### <a name="scenario-1-azure-ssis-ir-is-pointing-to-a-readwrite-listener-endpoint"></a>案例1： Azure SSIS IR 指向讀取/寫入接聽程式端點

此案例適用于下列情況：

- Azure SSIS IR 指向容錯移轉群組的讀取/寫入接聽程式端點。
- SQL Database 伺服器*未*設定虛擬網路服務端點的規則。

如果您想要讓 Azure SSIS IR 指向讀取/寫入接聽程式端點，您必須先指向主伺服器端點。 將 SSISDB 放在容錯移轉群組之後，您可以變更為讀取/寫入接聽程式端點，並重新啟動 Azure SSIS IR。

#### <a name="solution"></a>解決方法

發生容錯移轉時，對 Azure SSIS IR 而言是透明的。 Azure-SSIS IR 會自動連線到容錯移轉群組的新主要伺服器。 

如果您想要更新 Azure SSIS IR 中的區域或其他資訊，您可以將其停止、編輯和重新開機。


### <a name="scenario-2-azure-ssis-ir-is-pointing-to-a-primary-server-endpoint"></a>案例2： Azure SSIS IR 指向主伺服器端點

如果 Azure SSIS IR 指向主伺服器端點，則適用此案例。

#### <a name="solution"></a>解決方法

發生容錯移轉時，請採取下列步驟：

1. 停止主要區域中的 Azure SSIS IR。

2. 使用次要實例的新區域、端點和虛擬網路資訊來編輯 Azure SSIS IR。

    ```powershell
    Set-AzDataFactoryV2IntegrationRuntime -Location "new region" `
                    -CatalogServerEndpoint "Azure SQL Database server endpoint" `
                    -CatalogAdminCredential "Azure SQL Database server admin credentials" `
                    -VNetId "new VNet" `
                    -Subnet "new subnet" `
                    -SetupScriptContainerSasUri "new custom setup SAS URI"
    ```

3. 重新開機 Azure SSIS IR。

### <a name="scenario-3-attach-an-existing-ssisdb-ssis-catalog-to-a-new-azure-ssis-ir"></a>案例3：將現有的 SSISDB （SSIS 目錄）附加至新的 Azure SSIS IR

如果您想要在次要區域中布建新的 Azure SSIS IR，則適合使用此案例。 如果您想要 SSISDB 在目前的區域發生 Azure Data Factory 或 Azure SSIS IR 嚴重損壞時，在新的區域中繼續使用新的 Azure SSIS IR，這也適用。

#### <a name="solution"></a>解決方法

發生容錯移轉時，請採取下列步驟。

> [!NOTE]
> 在步驟4中使用 PowerShell （建立 IR）。 如果您沒有這麼做，Azure 入口網站將會報告指出 SSISDB 已經存在的錯誤。

1. 停止主要區域中的 Azure SSIS IR。

2. 執行預存程式以更新 SSISDB 中的中繼資料，以接受來自** \< new_data_factory_name \> **和** \< new_integration_runtime_name \> **的連接。
   
    ```sql
    EXEC [catalog].[failover_integration_runtime] @data_factory_name='<new_data_factory_name>', @integration_runtime_name='<new_integration_runtime_name>'
    ```

3. 在新區域中建立名為** \< new_data_factory_name \> **的新 data factory。

    ```powershell
    Set-AzDataFactoryV2 -ResourceGroupName "new resource group name" `
                         -Location "new region"`
                         -Name "<new_data_factory_name>"
    ```
    
    如需此 PowerShell 命令的詳細資訊，請參閱[使用 PowerShell 建立 Azure data factory](quickstart-create-data-factory-powershell.md)。

4. 使用 Azure PowerShell，在新的區域中建立名為** \< new_integration_runtime_name \> **的新 Azure SSIS IR。

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

    如需此 PowerShell 命令的詳細資訊，請參閱[在 Azure Data Factory 中建立 AZURE SSIS 整合運行](create-azure-ssis-integration-runtime.md)時間。


## <a name="next-steps"></a>後續步驟

請考量 Azure-SSIS 整合執行階段的下列其他組態選項：

- [設定 Azure SSIS integration runtime 以獲得高效能](configure-azure-ssis-integration-runtime-performance.md)

- [自訂 Azure-SSIS 整合執行階段的安裝](how-to-configure-azure-ssis-ir-custom-setup.md)

- [布建 Azure SSIS 整合執行時間的 Enterprise Edition](how-to-configure-azure-ssis-ir-enterprise-edition.md)
