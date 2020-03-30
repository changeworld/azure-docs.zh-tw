---
title: 教程：將單個資料庫添加到容錯移轉組
description: 使用 Azure 門戶、PowerShell 或 Azure CLI 將 Azure SQL 資料庫單個資料庫添加到容錯移轉組。
services: sql-database
ms.service: sql-database
ms.subservice: high-availability
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: MashaMSFT
ms.author: mathoma
ms.reviewer: sstein, carlrab
ms.date: 06/19/2019
ms.openlocfilehash: c5ce6a1c2f231d372a2a8113eb9043a236090388
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80061701"
---
# <a name="tutorial-add-an-azure-sql-database-single-database-to-a-failover-group"></a>教程：將 Azure SQL 資料庫單個資料庫添加到容錯移轉組

[容錯移轉組](sql-database-auto-failover-group.md)是一個聲明性抽象層，允許您對多維複製的資料庫進行分組。 瞭解如何為 Azure SQL 資料庫單個資料庫配置容錯移轉組，並使用 Azure 門戶、PowerShell 或 Azure CLI 測試容錯移轉。  在本教學課程中，您將學會如何：

> [!div class="checklist"]
> - 創建 Azure SQL 資料庫單個資料庫。
> - 為兩個邏輯 SQL 伺服器之間的單個資料庫創建容錯移轉組。
> - 測試容錯移轉。

## <a name="prerequisites"></a>Prerequisites

# <a name="portal"></a>[入口網站](#tab/azure-portal)
若要完成本教學課程，請確定您具有下列項目︰ 

- Azure 訂用帳戶。 如果您還沒有[一個免費帳戶，請創建一個免費帳戶](https://azure.microsoft.com/free/)。


# <a name="powershell"></a>[電源外殼](#tab/azure-powershell)
要完成本教程，請確保您有以下專案：

- Azure 訂用帳戶。 如果您還沒有[一個免費帳戶，請創建一個免費帳戶](https://azure.microsoft.com/free/)。
- [Azure 電源外殼](/powershell/azureps-cmdlets-docs)


# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
要完成本教程，請確保您有以下專案：

- Azure 訂用帳戶。 如果您還沒有[一個免費帳戶，請創建一個免費帳戶](https://azure.microsoft.com/free/)。
- 最新版本的[Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest)。 

---

## <a name="1---create-a-single-database"></a>1 - 創建單個資料庫 

[!INCLUDE [sql-database-create-single-database](includes/sql-database-create-single-database.md)]

## <a name="2---create-the-failover-group"></a>2 - 創建容錯移轉組 
在此步驟中，您將在現有 Azure SQL 伺服器和另一個區域中的新 Azure SQL 伺服器之間創建[容錯移轉組](sql-database-auto-failover-group.md)。 然後將範例資料庫新增到容錯移轉群組。 

# <a name="portal"></a>[入口網站](#tab/azure-portal)
創建容錯移轉組並使用 Azure 門戶將單個資料庫添加到該組。 

1. 在[Azure 門戶](https://portal.azure.com)的左側功能表中選擇**Azure SQL。** 如果 **Azure SQL** 不在清單中，請選取 [所有服務]****，然後在搜尋方塊中輸入 Azure SQL。 (選用) 選取 **Azure SQL** 旁的星號將其設為最愛，並新增為左側導覽中的項目。 
1. 選擇在第 1 節中創建的單個資料庫，如`mySampleDatabase`。 
1. 容錯移轉組可以在伺服器級別配置。 在 **"伺服器"名稱**下選擇伺服器的名稱以打開伺服器的設置。

   ![打開單個 db 的伺服器](media/sql-database-single-database-failover-group-tutorial/open-sql-db-server.png)

1. 在 **"設置"** 窗格下選擇**容錯移轉組**，然後選擇 **"添加組**"以創建新的容錯移轉組。 

    ![添加新容錯移轉組](media/sql-database-single-database-failover-group-tutorial/sqldb-add-new-failover-group.png)

1. 在**容錯移轉組**頁上，輸入或選擇以下值，**然後選擇：**
    - **容錯移轉組名稱**：鍵入唯一容錯移轉組名稱，如`failovergrouptutorial`。 
    - **次要伺服器**：選擇*用於配置所需設置*的選項，然後選擇**創建新伺服器**。 或者，您可以選擇已經存在的伺服器作為次要伺服器。 輸入以下值後，選擇 **" 選擇**"。 
        - **伺服器名稱**： 鍵入次要伺服器的唯一名稱，如`mysqlsecondary`。 
        - **伺服器管理員登錄**： 類型`azureuser`
        - **密碼**：鍵入滿足密碼要求的複雜密碼。
        - **位置**：從下拉清單中選擇位置，例如`East US`。 此位置不能與主伺服器位於同一位置。

    > [!NOTE]
    > 伺服器登錄和防火牆設置必須與主伺服器的設置匹配。 
    
      ![為容錯移轉組創建次要伺服器](media/sql-database-single-database-failover-group-tutorial/create-secondary-failover-server.png)

   - **組中的資料庫**：選擇次要伺服器後，此選項將解鎖。 選擇它以**選擇要添加的資料庫**，然後選擇您在第 1 節中創建的資料庫。 將資料庫添加到容錯移轉組將自動啟動異地複製過程。 
        
    ![將 SQL DB 添加到容錯移轉組](media/sql-database-single-database-failover-group-tutorial/add-sqldb-to-failover-group.png)
        

# <a name="powershell"></a>[電源外殼](#tab/azure-powershell)
創建容錯移轉組並使用 PowerShell 將單個資料庫添加到該組。 

   > [!NOTE]
   > 伺服器登錄和防火牆設置必須與主伺服器的設置匹配。 

   ```powershell-interactive
   # $subscriptionId = '<SubscriptionID>'
   # $resourceGroupName = "myResourceGroup-$(Get-Random)"
   # $location = "West US"
   # $adminLogin = "azureuser"
   # $password = "PWD27!"+(New-Guid).Guid
   # $serverName = "mysqlserver-$(Get-Random)"
   # $databaseName = "mySampleDatabase"
   $drLocation = "East US"
   $drServerName = "mysqlsecondary-$(Get-Random)"
   $failoverGroupName = "failovergrouptutorial-$(Get-Random)"

   # The ip address range that you want to allow to access your server 
   # (leaving at 0.0.0.0 will prevent outside-of-azure connections to your DB)
   $startIp = "0.0.0.0"
   $endIp = "0.0.0.0"

   # Show randomized variables
   Write-host "DR Server name is" $drServerName 
   Write-host "Failover group name is" $failoverGroupName
   
   # Create a secondary server in the failover region
   Write-host "Creating a secondary logical server in the failover region..."
   $drServer = New-AzSqlServer -ResourceGroupName $resourceGroupName `
      -ServerName $drServerName `
      -Location $drLocation `
      -SqlAdministratorCredentials $(New-Object -TypeName System.Management.Automation.PSCredential `
         -ArgumentList $adminlogin, $(ConvertTo-SecureString -String $password -AsPlainText -Force))
   $drServer

   # Create a server firewall rule that allows access from the specified IP range
   Write-host "Configuring firewall for secondary logical server..."
   $serverFirewallRule = New-AzSqlServerFirewallRule -ResourceGroupName $resourceGroupName `
      -ServerName $drServerName `
      -FirewallRuleName "AllowedIPs" -StartIpAddress $startIp -EndIpAddress $endIp
   $serverFirewallRule   
   
   # Create a failover group between the servers
   $failovergroup = Write-host "Creating a failover group between the primary and secondary server..."
   New-AzSqlDatabaseFailoverGroup `
      –ResourceGroupName $resourceGroupName `
      -ServerName $serverName `
      -PartnerServerName $drServerName  `
      –FailoverGroupName $failoverGroupName `
      –FailoverPolicy Automatic `
      -GracePeriodWithDataLossHours 2
   $failovergroup
   
   # Add the database to the failover group
   Write-host "Adding the database to the failover group..." 
   Get-AzSqlDatabase `
      -ResourceGroupName $resourceGroupName `
      -ServerName $serverName `
      -DatabaseName $databaseName | `
   Add-AzSqlDatabaseToFailoverGroup `
      -ResourceGroupName $resourceGroupName `
      -ServerName $serverName `
      -FailoverGroupName $failoverGroupName
   Write-host "Successfully added the database to the failover group..." 
   ```

本教程的這一部分使用以下 PowerShell Cmdlet：

| Command | 注意 |
|---|---|
| [新-AzSqlServer](/powershell/module/az.sql/new-azsqlserver) | 建立裝載單一資料庫和彈性集區的 SQL Database 伺服器。 |
| [New-AzSqlServerFirewallRule](/powershell/module/az.sql/new-azsqlserverfirewallrule) | 建立邏輯伺服器的防火牆規則。 | 
| [新-AzSql 資料庫](/powershell/module/az.sql/new-azsqldatabase) | 建立新的 Azure SQL Database 單一資料庫。 | 
| [New-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/new-azsqldatabasefailovergroup) | 建立新的容錯移轉群組。 |
| [Get-AzSqlDatabase](/powershell/module/az.sql/get-azsqldatabase) | 取得一或多個 SQL 資料庫。 |
| [Add-AzSqlDatabaseToFailoverGroup](/powershell/module/az.sql/add-azsqldatabasetofailovergroup) | 將一或多個 Azure SQL Database 新增至容錯移轉群組。 |

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
創建容錯移轉組並使用 AZ CLI 將單個資料庫添加到該組。 

   > [!NOTE]
   > 伺服器登錄和防火牆設置必須與主伺服器的設置匹配。 

   ```azurecli-interactive
   #!/bin/bash
   # set variables
   $failoverLocation = "West US"
   $failoverServer = "failoverServer-$randomIdentifier"
   $failoverGroup = "failoverGroup-$randomIdentifier"

   echo "Creating a secondary logical server in the DR region..."
   az sql server create --name $failoverServer --resource-group $resourceGroup --location $failoverLocation --admin-user $login --admin-password $password
   
   echo "Creating a failover group between the two servers..."
   az sql failover-group create --name $failoverGroup --partner-server $failoverServer --resource-group $resourceGroup --server $server --add-db $database --failover-policy Automatic
   ```

本教程的這一部分使用以下 Az CLI Cmdlet：

| Command | 注意 |
|---|---|
| [az sql server create](/cli/azure/sql/server#az-sql-server-create) | 建立裝載單一資料庫和彈性集區的 SQL Database 伺服器。 |
| [az sql server firewall-rule create](/cli/azure/sql/server/firewall-rule) | 建立伺服器的防火牆規則。 | 
| [az sql failover-group create](/cli/azure/sql/failover-group?view=azure-cli-latest#az-sql-failover-group-create) | 建立容錯移轉群組。 | 

---

## <a name="3---test-failover"></a>3 - 測試容錯移轉 
在此步驟中，您將容錯移轉組容錯移轉到次要伺服器，然後使用 Azure 門戶失敗。 

# <a name="portal"></a>[入口網站](#tab/azure-portal)
使用 Azure 門戶測試容錯移轉。 

1. 在[Azure 門戶](https://portal.azure.com)的左側功能表中選擇**Azure SQL。** 如果 **Azure SQL** 不在清單中，請選取 [所有服務]****，然後在搜尋方塊中輸入 Azure SQL。 (選用) 選取 **Azure SQL** 旁的星號將其設為最愛，並新增為左側導覽中的項目。 
1. 選擇在第 2 節（如`mySampleDatbase`）中創建的單個資料庫。 
1. 在 **"伺服器"名稱**下選擇伺服器的名稱以打開伺服器的設置。

   ![打開單個 db 的伺服器](media/sql-database-single-database-failover-group-tutorial/open-sql-db-server.png)

1. 在 **"設置"** 窗格下選擇**容錯移轉組**，然後選擇您在第 2 節中創建的容錯移轉組。 
  
   ![從門戶中選擇容錯移轉組](media/sql-database-single-database-failover-group-tutorial/select-failover-group.png)

1. 查看主伺服器和次要伺服器。 
1. 從工作窗格中選擇**容錯移轉**以容錯移轉容錯移轉組，其中包含示例單個資料庫。 
1. 在警告上選擇 **"是**"，通知您 TDS 會話將斷開連接。 

   ![容錯移轉組容錯移轉，其中包含 SQL 資料庫](media/sql-database-single-database-failover-group-tutorial/failover-sql-db.png)

1. 查看當前是主伺服器和次要伺服器。 如果容錯移轉成功，則兩台伺服器應交換角色。 
1. 再次選擇**容錯移轉**，使伺服器故障回其最初的角色。 

# <a name="powershell"></a>[電源外殼](#tab/azure-powershell)
使用 PowerShell 測試容錯移轉。 


檢查輔助副本的角色： 

   ```powershell-interactive
   # Set variables
   # $resourceGroupName = "myResourceGroup-$(Get-Random)"
   # $serverName = "mysqlserver-$(Get-Random)"
   # $failoverGroupName = "failovergrouptutorial-$(Get-Random)"
   
   # Check role of secondary replica
   Write-host "Confirming the secondary replica is secondary...." 
   (Get-AzSqlDatabaseFailoverGroup `
      -FailoverGroupName $failoverGroupName `
      -ResourceGroupName $resourceGroupName `
      -ServerName $drServerName).ReplicationRole
   ```

容錯移轉到次要伺服器： 

   ```powershell-interactive
   # Set variables
   # $resourceGroupName = "myResourceGroup-$(Get-Random)"
   # $serverName = "mysqlserver-$(Get-Random)"
   # $failoverGroupName = "failovergrouptutorial-$(Get-Random)"
   
   # Failover to secondary server
   Write-host "Failing over failover group to the secondary..." 
   Switch-AzSqlDatabaseFailoverGroup `
      -ResourceGroupName $resourceGroupName `
      -ServerName $drServerName `
      -FailoverGroupName $failoverGroupName
   Write-host "Failed failover group successfully to" $drServerName 
   ```

將容錯移轉群組還原至主要伺服器：

   ```powershell-interactive
   # Set variables
   # $resourceGroupName = "myResourceGroup-$(Get-Random)"
   # $serverName = "mysqlserver-$(Get-Random)"
   # $failoverGroupName = "failovergrouptutorial-$(Get-Random)"
   
   # Revert failover to primary server
   Write-host "Failing over failover group to the primary...." 
   Switch-AzSqlDatabaseFailoverGroup `
      -ResourceGroupName $resourceGroupName `
      -ServerName $serverName `
      -FailoverGroupName $failoverGroupName
   Write-host "Failed failover group successfully back to" $serverName
   ```

本教程的這一部分使用以下 PowerShell Cmdlet：

| Command | 注意 |
|---|---|
| [Get-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/get-azsqldatabasefailovergroup) | 取得或列出 Azure SQL Database 容錯移轉群組。 |
| [Switch-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/switch-azsqldatabasefailovergroup)| 執行 Azure SQL Database 容錯移轉群組的容錯移轉。 |



# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
使用 AZ CLI 測試容錯移轉。 

驗證哪個伺服器是次要伺服器：

   
   ```azurecli-interactive
   echo "Verifying which server is in the secondary role..."
   az sql failover-group list --server $server --resource-group $resourceGroup
   ```

容錯移轉到次要伺服器： 

   ```azurecli-interactive
   echo "Failing over group to the secondary server..."
   az sql failover-group set-primary --name $failoverGroup --resource-group $resourceGroup --server $failoverServer
   echo "Successfully failed failover group over to" $failoverServer
   ```

將容錯移轉群組還原至主要伺服器：

   ```azurecli-interactive
   echo "Failing over group back to the primary server..."
   az sql failover-group set-primary --name $failoverGroup --resource-group $resourceGroup --server $server
   echo "Successfully failed failover group back to" $server
   ```

本教程的這一部分使用以下 Az CLI Cmdlet：

| Command | 注意 |
|---|---|
| [az sql failover-group list](/cli/azure/sql/failover-group?view=azure-cli-latest#az-sql-failover-group-list) | 列出伺服器中的容錯移轉群組。 |
| [az sql failover-group set-primary](/cli/azure/sql/failover-group?view=azure-cli-latest#az-sql-failover-group-set-primary) | 從目前的主伺服器容錯移轉所有資料庫，以設定容錯移轉群組的主要複本。 | 

---

## <a name="clean-up-resources"></a>清除資源 
通過刪除資源組來清理資源。 

# <a name="portal"></a>[入口網站](#tab/azure-portal)
使用 Azure 門戶刪除資源組。 

1. 瀏覽至您在 [Azure 入口網站](https://portal.azure.com)中的資源群組。
1. 選擇 **"刪除資源組**"以刪除組中的所有資源以及資源組本身。 
1. 在文字方塊中鍵入資源組的名稱`myResourceGroup`，然後選擇 **"刪除**"以刪除資源組。  

# <a name="powershell"></a>[電源外殼](#tab/azure-powershell)

使用 PowerShell 刪除資源組。 

   ```powershell-interactive
   # Set variables
   # $resourceGroupName = "myResourceGroup-$(Get-Random)"

   # Remove the resource group
   Write-host "Removing resource group..."
   Remove-AzResourceGroup -ResourceGroupName $resourceGroupName
   Write-host "Resource group removed =" $resourceGroupName
   ```

本教程的這一部分使用以下 PowerShell Cmdlet：

| Command | 注意 |
|---|---|
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | 移除資源群組 | 

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

使用 AZ CLI 刪除資源組。 


   ```azurecli-interactive
   echo "Cleaning up resources by removing the resource group..."
   az group delete --name $resourceGroup
   echo "Successfully removed resource group" $resourceGroup
   ```

本教程的這一部分使用以下 Az CLI Cmdlet：

| Command | 注意 |
|---|---|
| [az group delete](https://docs.microsoft.com/cli/azure/vm/extension#az-vm-extension-set) | 刪除資源群組，包括所有的巢狀資源。 |

---


> [!IMPORTANT]
> 如果要保留資源組但刪除次要資料庫，請將其從容錯移轉組中刪除，然後再將其刪除。 在從容錯移轉組中刪除次要資料庫之前刪除該資料庫可能會導致不可預知的行為。 


## <a name="full-scripts"></a>完整腳本

# <a name="powershell"></a>[電源外殼](#tab/azure-powershell)

[!code-powershell-interactive[main](../../powershell_scripts/sql-database/failover-groups/add-single-db-to-failover-group-az-ps.ps1 "Add single database to a failover group")]

此指令碼會使用下列命令。 下表中的每個命令都會連結至命令特定的文件。

| Command | 注意 |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | 建立用來存放所有資源的資源群組。 |
| [新-AzSqlServer](/powershell/module/az.sql/new-azsqlserver) | 建立裝載單一資料庫和彈性集區的 SQL Database 伺服器。 |
| [New-AzSqlServerFirewallRule](/powershell/module/az.sql/new-azsqlserverfirewallrule) | 建立邏輯伺服器的防火牆規則。 | 
| [新-AzSql 資料庫](/powershell/module/az.sql/new-azsqldatabase) | 建立新的 Azure SQL Database 單一資料庫。 | 
| [New-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/new-azsqldatabasefailovergroup) | 建立新的容錯移轉群組。 |
| [Get-AzSqlDatabase](/powershell/module/az.sql/get-azsqldatabase) | 取得一或多個 SQL 資料庫。 |
| [Add-AzSqlDatabaseToFailoverGroup](/powershell/module/az.sql/add-azsqldatabasetofailovergroup) | 將一或多個 Azure SQL Database 新增至容錯移轉群組。 |
| [Get-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/get-azsqldatabasefailovergroup) | 取得或列出 Azure SQL Database 容錯移轉群組。 |
| [Switch-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/switch-azsqldatabasefailovergroup)| 執行 Azure SQL Database 容錯移轉群組的容錯移轉。 |
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | 移除資源群組 | 

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

[!code-azurecli-interactive[main](../../cli_scripts/sql-database/failover-groups/add-single-db-to-failover-group-az-cli.sh "Add single database to a failover group")]

此指令碼會使用下列命令。 下表中的每個命令都會連結至命令特定的文件。

| Command | 注意 |
|---|---|
| [az account set](/cli/azure/account?view=azure-cli-latest#az-account-set) | 將訂用帳戶設定為目前使用中的訂用帳戶。 | 
| [az group create](/cli/azure/group#az-group-create) | 建立用來存放所有資源的資源群組。 |
| [az sql server create](/cli/azure/sql/server#az-sql-server-create) | 建立裝載單一資料庫和彈性集區的 SQL Database 伺服器。 |
| [az sql server firewall-rule create](/cli/azure/sql/server/firewall-rule) | 建立伺服器的防火牆規則。 | 
| [az sql db create](/cli/azure/sql/db?view=azure-cli-latest) | 建立資料庫。 | 
| [az sql failover-group create](/cli/azure/sql/failover-group?view=azure-cli-latest#az-sql-failover-group-create) | 建立容錯移轉群組。 | 
| [az sql failover-group list](/cli/azure/sql/failover-group?view=azure-cli-latest#az-sql-failover-group-list) | 列出伺服器中的容錯移轉群組。 |
| [az sql failover-group set-primary](/cli/azure/sql/failover-group?view=azure-cli-latest#az-sql-failover-group-set-primary) | 從目前的主伺服器容錯移轉所有資料庫，以設定容錯移轉群組的主要複本。 | 
| [az group delete](https://docs.microsoft.com/cli/azure/vm/extension#az-vm-extension-set) | 刪除資源群組，包括所有的巢狀資源。 |

# <a name="portal"></a>[入口網站](#tab/azure-portal)
Azure 門戶沒有可用的腳本。 
 
---

您可以在此處找到其他 Azure SQL 資料庫腳本[：Azure PowerShell](sql-database-powershell-samples.md)和[Azure CLI](sql-database-cli-samples.md)。 

## <a name="next-steps"></a>後續步驟

在本教程中，您將 Azure SQL 資料庫單個資料庫添加到容錯移轉組，並測試了容錯移轉。 您已了解如何︰ 

> [!div class="checklist"]
> - 創建 Azure SQL 資料庫單個資料庫。 
> - 為兩個邏輯 SQL 伺服器之間的單個資料庫創建[容錯移轉組](sql-database-auto-failover-group.md)。
> - 測試容錯移轉。

進入下一教程，瞭解如何將彈性池添加到容錯移轉組。 

> [!div class="nextstepaction"]
> [教程：將 Azure SQL 資料庫彈性池添加到容錯移轉組](sql-database-elastic-pool-failover-group-tutorial.md)
