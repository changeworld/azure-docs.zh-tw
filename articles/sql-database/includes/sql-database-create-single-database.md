---
author: MashaMSFT
ms.service: sql-database
ms.subservice: single-database
ms.topic: include
ms.date: 02/14/2020
ms.author: mathoma
ms.reviewer: vanto
ms.openlocfilehash: d800d273cce995c618422a3a9d0934b2657e6ef5
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/29/2020
ms.locfileid: "78194288"
---
在此步驟中，您將建立 Azure SQL Database 單一資料庫。 

> [!IMPORTANT]
> 請務必設定防火牆規則，以使用您用來完成本文的電腦公用 IP 位址。
>
> 如需資訊，請參閱[建立資料庫層級防火牆規則](/sql/relational-databases/system-stored-procedures/sp-set-database-firewall-rule-azure-sql-database)，或者，若要判斷對於電腦的伺服器層級防火牆規則所用的 IP 位址，請參閱[建立伺服器層級防火牆](../sql-database-server-level-firewall-rule.md)。  

# <a name="portal"></a>[入口網站](#tab/azure-portal)

使用 Azure 入口網站建立您的資源群組和單一資料庫。

1. 在 [Azure 入口網站](https://portal.azure.com)的左側功能表中，選取 [Azure SQL]  。 如果 **Azure SQL** 不在清單中，請選取 [所有服務]  ，然後在搜尋方塊中輸入 Azure SQL  。 (選用) 選取 **Azure SQL** 旁的星號將其設為最愛，並新增為左側導覽中的項目。 
2. 選取 [+ 新增]  以開啟 [選取 SQL 部署選項]  頁面。 您可以選取 [資料庫]  圖格上的 [顯示詳細資料]  ，以查看不同資料庫的其他資訊。
3. 選取 [建立]  ：

   ![建立單一資料庫](../media/sql-database-get-started-portal/create-single-database.png)

4. 在 [基本資料]  索引標籤的 [專案詳細資料]  區段中，輸入或選取下列值：

   - 訂用帳戶  ：下拉並選取正確的訂用帳戶 (若未出現)。
   - **資源群組**：選取 [新建]  ，輸入 `myResourceGroup`，然後選取 [確定]  。

     ![新的 SQL 資料庫 - 基本資料索引標籤](../media/sql-database-get-started-portal/new-sql-database-basics.png)

5. 在 [資料庫詳細資料]  區段中，輸入或選取下列值：

   - **資料庫名稱**：輸入 `mySampleDatabase`。
   - **伺服器**：選取 [新建]  ，輸入下列值，然後選取 [選取]  。
       - **伺服器名稱**：輸入`mysqlserver`類別；以及一些表示唯一性的數字。
       - **伺服器管理員登入**：輸入 `azureuser`。
       - **密碼**：輸入符合密碼需求的複雜密碼。
       - **位置**：從下拉式清單中選擇位置，例如 `West US`。

         ![New server](../media/sql-database-get-started-portal/new-server.png)

      > [!IMPORTANT]
      > 請記得記錄下伺服器管理員登入和密碼，以便在進行這個和其他快速入門時能夠登入伺服器和資料庫。 如果您忘記登入或密碼，您可以在 [SQL Server]  頁面上取得登入名稱或重設密碼。 若要開啟 [SQL Server]  頁面，請在資料庫建立後，選取資料庫 [概觀]  頁面上的伺服器名稱。

   - **您要使用 SQL 彈性集區嗎**：選取 [否]  選項。
   - **計算 + 儲存體**：選取 [設定資料庫]  。 

     ![SQL Database 詳細資料](../media/sql-database-get-started-portal/sql-db-basic-db-details.png)

   - 選取 [已佈建]  。  或者，選取 [無伺服器]  以建立無伺服器資料庫。

     ![已佈建的 Gen4](../media/sql-database-get-started-portal/create-database-provisioned.png)

   - 檢閱 [vCores]  和 [資料大小上限]  的設定。 視需要變更這些設定。 
     - 您也可以選取 [變更組態]  來變更硬體世代。
   - 選取 [套用]  。

6. 選取 [網路]  索引標籤，並決定是否要[**允許 Azure 服務和資源存取此伺服器**](../sql-database-networkaccess-overview.md)，或新增[私人端點](../../private-link/private-endpoint-overview.md)。

   ![網路索引標籤](../media/sql-database-get-started-portal/create-database-networking.png)

7. 選取 [其他設定]  索引標籤。 
8. 在 [資料來源]  區段的 [使用現有資料]  下方，選取 `Sample`。

   ![其他 SQL DB 設定](../media/sql-database-get-started-portal/create-sql-database-additional-settings.png)

   > [!IMPORTANT]
   > 請務必選取 [範例 (AdventureWorksLT)]  資料，以便依照本快速入門和使用這項資料的其他 Azure SQL Database 快速入門的指示操作。

9. 將其餘的值保留為預設值，然後在表單底部選取 [檢閱 + 建立]  。
10. 檢閱最終設定，然後選取 [建立]  。

11. 在 [SQL Database]  表單中，選取 [建立]  以部署和佈建資源群組、伺服器和資料庫。

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

使用 PowerShell 建立您的資源群組和單一資料庫。

   ```powershell-interactive
   # Set variables for your server and database
   $subscriptionId = '<SubscriptionID>'
   $resourceGroupName = "myResourceGroup-$(Get-Random)"
   $location = "West US"
   $adminLogin = "azureuser"
   $password = "PWD27!"+(New-Guid).Guid
   $serverName = "mysqlserver-$(Get-Random)"
   $databaseName = "mySampleDatabase"

   # The ip address range that you want to allow to access your server 
   # (leaving at 0.0.0.0 will prevent outside-of-azure connections to your DB)
   $startIp = "0.0.0.0"
   $endIp = "0.0.0.0"

   # Show randomized variables
   Write-host "Resource group name is" $resourceGroupName 
   Write-host "Password is" $password  
   Write-host "Server name is" $serverName 

   # Connect to Azure
   Connect-AzAccount

   # Set subscription ID
   Set-AzContext -SubscriptionId $subscriptionId 

   # Create a resource group
   Write-host "Creating resource group..."
   $resourceGroup = New-AzResourceGroup -Name $resourceGroupName -Location $location -Tag @{Owner="SQLDB-Samples"}
   $resourceGroup

   # Create a server with a system wide unique server name
   Write-host "Creating primary logical server..."
   $server = New-AzSqlServer -ResourceGroupName $resourceGroupName `
      -ServerName $serverName `
      -Location $location `
      -SqlAdministratorCredentials $(New-Object -TypeName System.Management.Automation.PSCredential `
      -ArgumentList $adminLogin, $(ConvertTo-SecureString -String $password -AsPlainText -Force))
   $server

   # Create a server firewall rule that allows access from the specified IP range
   Write-host "Configuring firewall for primary logical server..."
   $serverFirewallRule = New-AzSqlServerFirewallRule -ResourceGroupName $resourceGroupName `
      -ServerName $serverName `
      -FirewallRuleName "AllowedIPs" -StartIpAddress $startIp -EndIpAddress $endIp
   $serverFirewallRule

   # Create General Purpose Gen4 database with 1 vCore
   Write-host "Creating a gen5 2 vCore database..."
   $database = New-AzSqlDatabase  -ResourceGroupName $resourceGroupName `
      -ServerName $serverName `
      -DatabaseName $databaseName `
      -Edition GeneralPurpose `
      -VCore 2 `
      -ComputeGeneration Gen5 `
      -MinimumCapacity 2 `
      -SampleName "AdventureWorksLT"
   $database
   ```

本文的這個部分會使用下列 PowerShell Cmdlet：

| Command | 注意 |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | 建立用來存放所有資源的資源群組。 |
| [New-AzSqlServer](/powershell/module/az.sql/new-azsqlserver) | 建立裝載單一資料庫和彈性集區的 SQL Database 伺服器。 |
| [New-AzSqlServerFirewallRule](/powershell/module/az.sql/new-azsqlserverfirewallrule) | 建立邏輯伺服器的防火牆規則。 | 
| [New-AzSqlDatabase](/powershell/module/az.sql/new-azsqldatabase) | 建立新的 Azure SQL Database 單一資料庫。 | 

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

使用 AZ CLI 建立您的資源群組和單一資料庫。

   ```azurecli-interactive
   #!/bin/bash
   # set variables
   $subscription = "<subscriptionID>"
   $randomIdentifier = $(Get-Random)

   $resourceGroup = "resource-$randomIdentifier"
   $location = "East US"
   
   $login = "sampleLogin"
   $password = "samplePassword123!"

   $server = "server-$randomIdentifier"
   $database = "database-$randomIdentifier"
  
   az login # connect to Azure
   az account set -s $subscription # set subscription context for the Azure account

   echo "Creating resource group..."
   az group create --name $resourceGroup --location $location

   echo "Creating primary logical server..."
   az sql server create --name $server --resource-group $resourceGroup --location $location --admin-user $login --admin-password $password

   echo "Creating a gen5 2 vCore database..."
   az sql db create --resource-group $resourceGroup --server $server --name $database --sample-name AdventureWorksLT --edition GeneralPurpose --family Gen5 --capacity 2
   ```

此指令碼會使用下列命令。 下表中的每個命令都會連結至命令特定的文件。

| Command | 注意 |
|---|---|
| [az account set](/cli/azure/account?view=azure-cli-latest#az-account-set) | 將訂用帳戶設定為目前使用中的訂用帳戶。 | 
| [az group create](/cli/azure/group#az-group-create) | 建立用來存放所有資源的資源群組。 |
| [az sql server create](/cli/azure/sql/server#az-sql-server-create) | 建立裝載單一資料庫和彈性集區的 SQL Database 伺服器。 |
| [az sql server firewall-rule create](/cli/azure/sql/server/firewall-rule) | 建立伺服器的防火牆規則。 | 
| [az sql db create](/cli/azure/sql/db?view=azure-cli-latest) | 建立資料庫。 | 


---
