---
title: 使用 PowerShell 佈建 Azure-SSIS Integration Runtime
description: 了解如何使用 PowerShell 在 Azure Data Factory 中佈建 Azure-SSIS 整合執行階段，讓您可以在 Azure 部署並執行 SSIS 套件。
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: ''
ms.devlang: powershell
ms.topic: tutorial
ms.custom: seo-lt-2019
ms.date: 02/01/2020
author: swinarko
ms.author: sawinark
ms.reviewer: douglasl
manager: mflasko
ms.openlocfilehash: e7baed6e599df6630eb5dc4aa7a2cdf37533c814
ms.sourcegitcommit: 42517355cc32890b1686de996c7913c98634e348
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/02/2020
ms.locfileid: "76964561"
---
# <a name="provision-the-azure-ssis-integration-runtime-in-azure-data-factory-with-powershell"></a>使用 PowerShell 在 Azure Data Factory 中佈建 Azure-SSIS 整合執行階段

本教學課程提供在 Azure Data Factory (ADF) 中佈建 Azure-SQL Server Integration Services (SSIS) 整合執行階段 (IR) 的步驟。 套件若部署到 Azure SQL Database 伺服器/受控執行個體 (專案部署模型) 所裝載的 SSIS 目錄 (SSISDB) 以及部署到檔案系統/檔案共用/Azure 檔案 (套件部署模型) 中，Azure-SSIS IR 都可支援執行這些套件。 佈建好 Azure SSIS IR 後，您可以接著使用 SQL Server Data Tools (SSDT)/SQL Server Management Studio (SSMS) 等熟悉的工具和命令列公用程式 (例如 `dtinstall`/`dtutil`/`dtexec`)，在 Azure 中部署並執行您的套件。 在本教學課程中，您會執行下列步驟：

> [!NOTE]
> 本文使用 Azure PowerShell 來佈建 Azure-SSIS IR。 若要使用 Azure 入口網站/ADF 應用程式來佈建 Azure-SSIS IR，請參閱[教學課程：佈建 Azure-SSIS IR](tutorial-create-azure-ssis-runtime-portal.md)。 

> [!div class="checklist"]
> * 建立資料處理站。
> * 建立 Azure-SSIS 整合執行階段
> * 啟動 Azure-SSIS 整合執行階段
> * 檢閱完整的指令碼
> * 部署 SSIS 套件

## <a name="prerequisites"></a>Prerequisites

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

- **Azure 訂用帳戶**。 如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/)。 如需 Azure-SSIS IR 的概念資訊，請參閱 [Azure-SSIS Integration Runtime 概觀](concepts-integration-runtime.md#azure-ssis-integration-runtime)。
- **Azure SQL Database 伺服器 (選用)** 。 如果您還沒有資料庫伺服器，請在 Azure 入口網站中建立一個，然後再開始。 ADF 接著會在此資料庫伺服器上建立 SSISDB。 建議於整合執行階段所在的相同 Azure 區域中建立資料庫伺服器。 此設定可讓整合執行階段將執行記錄寫入 SSISDB，而不需要跨 Azure 區域。 
    - 根據選取的資料庫伺服器，SSISDB 可代表您建立為單一資料庫、彈性集區的一部分，或建立在受控執行個體中，並且可在公用網路中或透過加入虛擬網路來存取。 如需有關選擇適當資料庫伺服器類型來裝載 SSISDB 的指引，請參閱[比較 Azure SQL Database 單一資料庫、彈性集區及受控執行個體](../data-factory/create-azure-ssis-integration-runtime.md#comparison-of-a-sql-database-single-database-elastic-pool-and-managed-instance)。 如果您使用具有 IP 防火牆規則/虛擬網路服務端點的 Azure SQL Database 伺服器或具有私人端點的受控執行個體來裝載 SSISDB，或您需要在不設定自我裝載 IR 的情況下存取內部部署資料，則必須將 Azure-SSIS IR 加入虛擬網路。 如需詳細資訊，請參閱[在虛擬網路中建立 Azure-SSIS IR](https://docs.microsoft.com/azure/data-factory/create-azure-ssis-integration-runtime)。
    - 確認資料庫伺服器的 [允許存取 Azure 服務]  設定已啟用。 如果您使用具有IP 防火牆規則/虛擬網路服務端點的 Azure SQL Database 伺服器或具有私人端點的受控執行個體來裝載 SSISDB，則不適用此設定。 如需詳細資訊，請參閱[保護 Azure SQL 資料庫資料庫](../sql-database/sql-database-security-tutorial.md#create-firewall-rules)。 若要使用 PowerShell 來啟用此設定，請參閱 [New-AzSqlServerFirewallRule](/powershell/module/az.sql/new-azsqlserverfirewallrule)。
    - 新增用戶端電腦的 IP 位址，或新增 IP 位址範圍，其中包含資料庫伺服器之防火牆設定中用戶端電腦 IP 位址到用戶端 IP 位址清單。 如需詳細資訊，請參閱 [Azure SQL Database 伺服器層級和資料庫層級防火牆規則](../sql-database/sql-database-firewall-configure.md)
    - 若要連線至資料庫伺服器，您可以使用伺服器管理員認證來執行 SQL 驗證，或使用 ADF 受控識別來執行 Azure Active Directory (AAD) 驗證。  對於後者，您需要將 ADF 的受控識別新增到具有資料庫伺服器存取權的 AAD 群組，請參閱[使用 AAD 驗證建立 Azure-SSIS IR](https://docs.microsoft.com/azure/data-factory/create-azure-ssis-integration-runtime)。
    - 請確認您的資料庫伺服器尚未有 SSISDB。 Azure-SSIS IR 的佈建不支援使用現有的 SSISDB。
- **Azure PowerShell**(英文)。 如果您想要執行 PowerShell 指令碼來佈建 Azure-SSIS IR，請遵循[如何安裝和設定 Azure PowerShell](/powershell/azure/install-Az-ps) 中的指示。

> [!NOTE]
> - 如需目前可使用 ADF 和 Azure-SSIS IR 的 Azure 區域清單，請參閱[依區域的 ADF + SSIS IR 可用性](https://azure.microsoft.com/global-infrastructure/services/?products=data-factory&regions=all)。 

## <a name="launch-windows-powershell-ise"></a>啟動 Windows PowerShell ISE

以系統管理權限啟動 **Windows PowerShell ISE**。 

## <a name="create-variables"></a>建立變數

複製並貼上下列指令碼 - 指定變數的值。 

```powershell
### Azure Data Factory info
# If your input contains a PSH special character, e.g. "$", precede it with the escape character "`" like "`$"
$SubscriptionName = "[your Azure subscription name]"
$ResourceGroupName = "[your Azure resource group name]"
# Data factory name - Must be globally unique
$DataFactoryName = "[your data factory name]"
# For supported regions, see https://azure.microsoft.com/global-infrastructure/services/?products=data-factory&regions=all
$DataFactoryLocation = "EastUS"

### Azure-SSIS integration runtime info - This is a Data Factory compute resource for running SSIS packages
$AzureSSISName = "[your Azure-SSIS IR name]"
$AzureSSISDescription = "[your Azure-SSIS IR description]"
# For supported regions, see https://azure.microsoft.com/global-infrastructure/services/?products=data-factory&regions=all
$AzureSSISLocation = "EastUS"
# For supported node sizes, see https://azure.microsoft.com/pricing/details/data-factory/ssis/
$AzureSSISNodeSize = "Standard_D8_v3"
# 1-10 nodes are currently supported
$AzureSSISNodeNumber = 2 
# Azure-SSIS IR edition/license info: Standard or Enterprise 
$AzureSSISEdition = "Standard" # Standard by default, while Enterprise lets you use advanced/premium features on your Azure-SSIS IR
# Azure-SSIS IR hybrid usage info: LicenseIncluded or BasePrice
$AzureSSISLicenseType = "LicenseIncluded" # LicenseIncluded by default, while BasePrice lets you bring your own on-premises SQL Server license with Software Assurance to earn cost savings from Azure Hybrid Benefit (AHB) option
# For a Standard_D1_v2 node, up to 4 parallel executions per node are supported, but for other nodes, up to (2 x number of cores) are currently supported
$AzureSSISMaxParallelExecutionsPerNode = 8
# Custom setup info: Standard/express custom setups
$SetupScriptContainerSasUri = "" # OPTIONAL to provide a SAS URI of blob container for standard custom setup where your script and its associated files are stored
$ExpressCustomSetup = "[RunCmdkey|SetEnvironmentVariable|SentryOne.TaskFactory|oh22is.SQLPhonetics.NET|oh22is.HEDDA.IO or leave it empty]" # OPTIONAL to configure an express custom setup without script

### SSISDB info
$SSISDBServerEndpoint = "[your Azure SQL Database server name.database.windows.net or Managed Instance name.public.DNS prefix.database.windows.net,3342 or leave it empty if you do not use SSISDB]" # WARNING: If you use SSISDB, please ensure that there is no existing SSISDB on your database server, so we can prepare and manage one on your behalf    
$SSISDBServerAdminUserName = "[your server admin username for SQL authentication]"
$SSISDBServerAdminPassword = "[your server admin password for SQL authentication]"
# For the basic pricing tier, specify "Basic", not "B" - For standard/premium/elastic pool tiers, specify "S0", "S1", "S2", "S3", etc., see https://docs.microsoft.com/azure/sql-database/sql-database-resource-limits-database-server
$SSISDBPricingTier = "[Basic|S0|S1|S2|S3|S4|S6|S7|S9|S12|P1|P2|P4|P6|P11|P15|…|ELASTIC_POOL(name = <elastic_pool_name>) for Azure SQL Database server or leave it empty for Managed Instance]"

### Self-hosted integration runtime info - This can be configured as a proxy for on-premises data access 
$DataProxyIntegrationRuntimeName = "" # OPTIONAL to configure a proxy for on-premises data access 
$DataProxyStagingLinkedServiceName = "" # OPTIONAL to configure a proxy for on-premises data access 
$DataProxyStagingPath = "" # OPTIONAL to configure a proxy for on-premises data access 
```

## <a name="sign-in-and-select-subscription"></a>登入並選取訂用帳戶

將下列程式碼新增至指令碼，以登入並選取您的 Azure 訂用帳戶。

```powershell
Connect-AzAccount
Select-AzSubscription -SubscriptionName $SubscriptionName
```

## <a name="validate-the-connection-to-database-server"></a>驗證資料庫伺服器的連線

新增下列指令碼來驗證您的 Azure SQL Database 伺服器。 

```powershell
# Validate only if you use SSISDB
if(![string]::IsNullOrEmpty($SSISDBServerEndpoint))
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
```

若要建立 Azure SQL Database 作為指令碼的一部分，請參閱下列範例： 

為尚未定義的變數設定值。 例如：SSISDBServerName、FirewallIPAddress。 

```powershell
New-AzSqlServer -ResourceGroupName $ResourceGroupName `
    -ServerName $SSISDBServerName `
    -Location $DataFactoryLocation `
    -SqlAdministratorCredentials $(New-Object -TypeName System.Management.Automation.PSCredential -ArgumentList $SSISDBServerAdminUserName, $(ConvertTo-SecureString -String $SSISDBServerAdminPassword -AsPlainText -Force))    

New-AzSqlServerFirewallRule -ResourceGroupName $ResourceGroupName `
    -ServerName $SSISDBServerName `
    -FirewallRuleName "ClientIPAddress_$today" -StartIpAddress $FirewallIPAddress -EndIpAddress $FirewallIPAddress

New-AzSqlServerFirewallRule -ResourceGroupName $ResourceGroupName -ServerName $SSISDBServerName -AllowAllAzureIPs
```

## <a name="create-a-resource-group"></a>建立資源群組

使用 [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) 命令來建立 [Azure 資源群組](../azure-resource-manager/management/overview.md)。 資源群組是在其中以群組方式部署與管理 Azure 資源的邏輯容器。

如果您的資源群組已經存在，請勿將此程式碼複製到您的指令碼。 

```powershell
New-AzResourceGroup -Location $DataFactoryLocation -Name $ResourceGroupName
```

## <a name="create-a-data-factory"></a>建立 Data Factory

執行下列命令來建立資料處理站：

```powershell
Set-AzDataFactoryV2 -ResourceGroupName $ResourceGroupName `
    -Location $DataFactoryLocation `
    -Name $DataFactoryName
```

## <a name="create-an-integration-runtime"></a>建立整合執行階段

執行下列命令，以建立在 Azure 中執行 SSIS 套件的 Azure-SSIS 整合執行階段。

如果您不是使用 SSISDB，您可以省略 CatalogServerEndpoint、CatalogPricingTier 和 CatalogAdminCredential 參數。

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
    -MaxParallelExecutionsPerNode $AzureSSISMaxParallelExecutionsPerNode

# Add CatalogServerEndpoint, CatalogPricingTier, and CatalogAdminCredential parameters if you use SSISDB
if(![string]::IsNullOrEmpty($SSISDBServerEndpoint))
{
    $secpasswd = ConvertTo-SecureString $SSISDBServerAdminPassword -AsPlainText -Force
    $serverCreds = New-Object System.Management.Automation.PSCredential($SSISDBServerAdminUserName, $secpasswd)

    Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
        -DataFactoryName $DataFactoryName `
        -Name $AzureSSISName `
        -CatalogServerEndpoint $SSISDBServerEndpoint `
        -CatalogPricingTier $SSISDBPricingTier `
        -CatalogAdminCredential $serverCreds
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
```

## <a name="start-integration-runtime"></a>啟動整合執行階段

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
> 若不包含任何自訂的安裝時間，此程序應該會在 5 分鐘內完成。
>
> 如果您使用 SSISDB，ADF 服務將會連線到您資料庫伺服器來準備 SSISDB。 
> 
> 當您佈建 Azure-SSIS IR 時，也會安裝 Access 可轉散發套件和適用於 SSIS 的 Azure Feature Pack。 除了內建元件已支援的資料來源，這些元件還提供對 Excel/Access 檔案及各種 Azure 資料來源的連線能力。 您也可以安裝其他元件，請參閱 [Azure-SSIS IR 的自訂安裝](how-to-configure-azure-ssis-ir-custom-setup.md)。

## <a name="full-script"></a>完整指令碼

本節中的 PowerShell 指令碼會設定執行 SSIS 套件的 Azure-SSIS IR 執行個體。 成功執行此指令碼之後，您可以在 Azure 中部署並執行 SSIS 套件。

1. 啟動 Windows PowerShell 整合式指令碼環境 (ISE)。
2. 在 ISE 中，從命令提示字元執行下列命令。  

    ```powershell
    Set-ExecutionPolicy Unrestricted -Scope CurrentUser
    ```

3. 複製本節中的 PowerShell 指令碼並貼入 ISE 中。
4. 在指令碼開頭提供所有適當的參數值。
5. 執行指令碼。 

```powershell
### Azure Data Factory info
# If your input contains a PSH special character, e.g. "$", precede it with the escape character "`" like "`$"
$SubscriptionName = "[your Azure subscription name]"
$ResourceGroupName = "[your Azure resource group name]"
# Data factory name - Must be globally unique
$DataFactoryName = "[your data factory name]"
# For supported regions, see https://azure.microsoft.com/global-infrastructure/services/?products=data-factory&regions=all
$DataFactoryLocation = "EastUS"

### Azure-SSIS integration runtime info - This is a Data Factory compute resource for running SSIS packages
$AzureSSISName = "[your Azure-SSIS IR name]"
$AzureSSISDescription = "[your Azure-SSIS IR description]"
# For supported regions, see https://azure.microsoft.com/global-infrastructure/services/?products=data-factory&regions=all
$AzureSSISLocation = "EastUS"
# For supported node sizes, see https://azure.microsoft.com/pricing/details/data-factory/ssis/
$AzureSSISNodeSize = "Standard_D8_v3"
# 1-10 nodes are currently supported
$AzureSSISNodeNumber = 2 
# Azure-SSIS IR edition/license info: Standard or Enterprise 
$AzureSSISEdition = "Standard" # Standard by default, while Enterprise lets you use advanced/premium features on your Azure-SSIS IR
# Azure-SSIS IR hybrid usage info: LicenseIncluded or BasePrice
$AzureSSISLicenseType = "LicenseIncluded" # LicenseIncluded by default, while BasePrice lets you bring your own on-premises SQL Server license with Software Assurance to earn cost savings from Azure Hybrid Benefit (AHB) option
# For a Standard_D1_v2 node, up to 4 parallel executions per node are supported, but for other nodes, up to (2 x number of cores) are currently supported
$AzureSSISMaxParallelExecutionsPerNode = 8
# Custom setup info: Standard/express custom setups
$SetupScriptContainerSasUri = "" # OPTIONAL to provide a SAS URI of blob container for standard custom setup where your script and its associated files are stored
$ExpressCustomSetup = "[RunCmdkey|SetEnvironmentVariable|SentryOne.TaskFactory|oh22is.SQLPhonetics.NET|oh22is.HEDDA.IO or leave it empty]" # OPTIONAL to configure an express custom setup without script

### SSISDB info
$SSISDBServerEndpoint = "[your Azure SQL Database server name.database.windows.net or Managed Instance name.public.DNS prefix.database.windows.net,3342 or leave it empty if you do not use SSISDB]" # WARNING: If you want to use SSISDB, please ensure that there is no existing SSISDB on your database server, so we can prepare and manage one on your behalf    
$SSISDBServerAdminUserName = "[your server admin username for SQL authentication]"
$SSISDBServerAdminPassword = "[your server admin password for SQL authentication]"
# For the basic pricing tier, specify "Basic", not "B" - For standard/premium/elastic pool tiers, specify "S0", "S1", "S2", "S3", etc., see https://docs.microsoft.com/azure/sql-database/sql-database-resource-limits-database-server
$SSISDBPricingTier = "[Basic|S0|S1|S2|S3|S4|S6|S7|S9|S12|P1|P2|P4|P6|P11|P15|…|ELASTIC_POOL(name = <elastic_pool_name>) for Azure SQL Database server or leave it empty for Managed Instance]"

### Self-hosted integration runtime info - This can be configured as a proxy for on-premises data access 
$DataProxyIntegrationRuntimeName = "" # OPTIONAL to configure a proxy for on-premises data access 
$DataProxyStagingLinkedServiceName = "" # OPTIONAL to configure a proxy for on-premises data access 
$DataProxyStagingPath = "" # OPTIONAL to configure a proxy for on-premises data access 

### Sign in and select subscription
Connect-AzAccount
Select-AzSubscription -SubscriptionName $SubscriptionName

### Validate the connection to database server
# Validate only if you use SSISDB
if(![string]::IsNullOrEmpty($SSISDBServerEndpoint))
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
    -MaxParallelExecutionsPerNode $AzureSSISMaxParallelExecutionsPerNode

# Add CatalogServerEndpoint, CatalogPricingTier, and CatalogAdminCredential parameters if you use SSISDB
if(![string]::IsNullOrEmpty($SSISDBServerEndpoint))
{
    $secpasswd = ConvertTo-SecureString $SSISDBServerAdminPassword -AsPlainText -Force
    $serverCreds = New-Object System.Management.Automation.PSCredential($SSISDBServerAdminUserName, $secpasswd)

    Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
        -DataFactoryName $DataFactoryName `
        -Name $AzureSSISName `
        -CatalogServerEndpoint $SSISDBServerEndpoint `
        -CatalogPricingTier $SSISDBPricingTier `
        -CatalogAdminCredential $serverCreds
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

### Start integration runtime
write-host("##### Starting #####")
Start-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
    -DataFactoryName $DataFactoryName `
    -Name $AzureSSISName `
    -Force

write-host("##### Completed #####")
write-host("If any cmdlet is unsuccessful, please consider using -Debug option for diagnostics.")   
```

## <a name="monitor-and-manage-azure-ssis-ir"></a>監視和管理 Azure-SSIS IR

請參閱下列文章中有關監視和管理 Azure-SSIS IR 的線詳細資訊。 

- [監視 Azure-SSIS IR](monitor-integration-runtime.md#azure-ssis-integration-runtime)
- [管理 Azure-SSIS IR](manage-azure-ssis-integration-runtime.md)

## <a name="deploy-ssis-packages"></a>部署 SSIS 套件

如果您使用 SSISDB，您可以使用透過伺服器端點來與資料庫伺服器連線的 SSDT/SSMS 工具，將套件部署到其中，並在 Azure-SSIS IR 上執行這些套件。 對於具有公用端點的 Azure SQL Database 伺服器/受控執行個體，伺服器端點格式分別為 `<server name>.database.windows.net`/`<server name>.public.<dns prefix>.database.windows.net,3342`。 如果您不是使用 SSISDB，您可以使用 `dtinstall`/`dtutil`/`dtexec` 命令列公用程式，將套件部署到檔案系統/檔案共用/Azure 檔案，並在 Azure-SSIS IR 上執行這些套件。 如需詳細資訊，請參閱[部署 SSIS 套件](/sql/integration-services/packages/deploy-integration-services-ssis-projects-and-packages#deploy-packages-to-integration-services-server)。 在這兩種情況下，您也可以使用 ADF 管線中的執行 SSIS 套件活動在 Azure-SSIS IR 上執行已部署的套件，請參閱[以頂級 ADF 活動的形式叫用 SSIS 套件執行](https://docs.microsoft.com/azure/data-factory/how-to-invoke-ssis-package-ssis-activity)。

另請參閱 SSIS 文件中的下列文章： 

- [在 Azure 中部署、執行和監視 SSIS 套件](/sql/integration-services/lift-shift/ssis-azure-deploy-run-monitor-tutorial)   
- [連線至 Azure 中的 SSISDB](/sql/integration-services/lift-shift/ssis-azure-connect-to-catalog-database)
- [在 Azure 中排程套件執行](/sql/integration-services/lift-shift/ssis-azure-schedule-packages)
- [透過 Windows 驗證連線至內部部署資料來源](/sql/integration-services/lift-shift/ssis-azure-connect-with-windows-auth) 

## <a name="next-steps"></a>後續步驟

在本教學課程中，您已了解如何： 

> [!div class="checklist"]
> * 建立資料處理站。
> * 建立 Azure-SSIS 整合執行階段
> * 啟動 Azure-SSIS 整合執行階段
> * 檢閱完整的指令碼
> * 部署 SSIS 套件

若要了解如何自訂 Azure-SSIS 整合執行階段，請前往下列文章：

> [!div class="nextstepaction"]
>[自訂 AZURE-SSIS IR](https://docs.microsoft.com/azure/data-factory/how-to-configure-azure-ssis-ir-custom-setup)