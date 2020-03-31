---
title: 教程：將彈性池添加到容錯移轉組
description: 使用 Azure 門戶、PowerShell 或 Azure CLI 將 Azure SQL 資料庫彈性池添加到容錯移轉組。
services: sql-database
ms.service: sql-database
ms.subservice: high-availability
ms.custom: seo-lt-2019
ms.devlang: ''
ms.topic: conceptual
author: MashaMSFT
ms.author: mathoma
ms.reviewer: sstein, carlrab
ms.date: 08/27/2019
ms.openlocfilehash: c57f9eed2147504dd7b3313d58468fb76ab40caa
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79268973"
---
# <a name="tutorial-add-an-azure-sql-database-elastic-pool-to-a-failover-group"></a>教程：將 Azure SQL 資料庫彈性池添加到容錯移轉組

使用 Azure 門戶為 Azure SQL 資料庫彈性池配置容錯移轉組並測試容錯移轉。  在本教學課程中，您將學會如何：

> [!div class="checklist"]
> - 創建 Azure SQL 資料庫單個資料庫。
> - 將單個資料庫添加到彈性池中。 
> - 為兩個邏輯 SQL 伺服器之間的兩個彈性池創建[容錯移轉組](sql-database-auto-failover-group.md)。
> - 測試容錯移轉。

## <a name="prerequisites"></a>Prerequisites

若要完成本教學課程，請確定您具有下列項目︰ 

- Azure 訂用帳戶。 如果您還沒有[一個免費帳戶，請創建一個免費帳戶](https://azure.microsoft.com/free/)。


## <a name="1---create-a-single-database"></a>1 - 創建單個資料庫 

[!INCLUDE [sql-database-create-single-database](includes/sql-database-create-single-database.md)]

## <a name="2---add-single-database-to-elastic-pool"></a>2 - 將單個資料庫添加到彈性池
在此步驟中，您將創建一個彈性池，並將單個資料庫添加到其中。 


# <a name="portal"></a>[入口網站](#tab/azure-portal)

使用 Azure 門戶創建彈性池。 


1. 在 Azure 入口網站的左側功能表中，選取 [Azure SQL]****。 如果 **Azure SQL** 不在清單中，請選取 [所有服務]****，然後在搜尋方塊中輸入 Azure SQL。 (選用) 選取 **Azure SQL** 旁的星號將其設為最愛，並新增為左側導覽中的項目。 
1. 選取 [+ 新增]**** 以開啟 [選取 SQL 部署選項]**** 頁面。 您可以選取 [資料庫] 圖格上的 [顯示詳細資料]，以查看不同資料庫的其他資訊。
1. 從**SQL 資料庫**磁貼中的 **"資源類型**"下拉清單中選擇**彈性池**。 選擇 **"創建**"以創建彈性池。 

    ![選擇彈性池](media/sql-database-elastic-pool-failover-group-tutorial/select-azure-sql-elastic-pool.png)

1. 使用以下值配置彈性池：
   - **名稱**： 為彈性池提供唯一名稱，如`myElasticPool`。 
   - **訂閱**：從下拉清單中選擇訂閱。
   - **資源組**：從`myResourceGroup`下拉清單中選擇您在第 1 節中創建的資源組。 
   - **伺服器**：從下拉清單中選擇您在第 1 節中創建的伺服器。  

       ![為彈性池創建新伺服器](media/sql-database-elastic-pool-failover-group-tutorial/use-existing-server-for-elastic-pool.png)

   - **計算 + 存儲**：選擇 **"配置彈性池**"以配置計算、存儲並將單個資料庫添加到彈性池。 在 **"池設置"** 選項卡上，保留預設的 Gen5，包含 2 個 vCore 和 32gb。 

1. 在"**配置**"頁上，選擇"**資料庫**"選項卡，然後選擇 **"添加資料庫**"。 選擇您在第 1 節中創建的資料庫，然後選擇 **"應用"** 將其添加到彈性池。 選擇"再次**應用**"以應用彈性池設置並關閉 **"配置"** 頁。 

    ![將 SQL DB 添加到彈性池](media/sql-database-elastic-pool-failover-group-tutorial/add-database-to-elastic-pool.png)

1. 選擇 **"查看 + 創建**"以查看彈性池設置，然後選擇 **"創建"** 以創建彈性池。 


# <a name="powershell"></a>[電源外殼](#tab/azure-powershell)
使用 PowerShell 創建彈性池和次要伺服器。 

   ```powershell-interactive
   # Set variables for your server and database
   # $subscriptionId = '<SubscriptionID>'
   # $resourceGroupName = "myResourceGroup-$(Get-Random)"
   # $location = "East US"
   # $adminLogin = "azureuser"
   # $password = "PWD27!"+(New-Guid).Guid
   # $serverName = "mysqlserver-$(Get-Random)"
   $poolName = "myElasticPool"
   $databaseName = "mySampleDatabase"
   $drLocation = "West US"
   $drServerName = "mysqlsecondary-$(Get-Random)"
   $failoverGroupName = "failovergrouptutorial-$(Get-Random)"
   
   # The ip address range that you want to allow to access your server 
   # Leaving at 0.0.0.0 will prevent outside-of-azure connections
   # $startIp = "0.0.0.0"
   # $endIp = "0.0.0.0"
   
   # Show randomized variables
   Write-host "DR Server name is" $drServerName 
   Write-host "Failover group name is" $failoverGroupName
   
   # Create primary Gen5 elastic 2 vCore pool
   Write-host "Creating elastic pool..."
   $elasticPool = New-AzSqlElasticPool -ResourceGroupName $resourceGroupName `
       -ServerName $serverName `
       -ElasticPoolName $poolName `
       -Edition "GeneralPurpose" `
       -vCore 2 `
       -ComputeGeneration Gen5
   $elasticPool
   
   # Add single db into elastic pool
   Write-host "Creating elastic pool..."
   $addDatabase = Set-AzSqlDatabase -ResourceGroupName $resourceGroupName `
       -ServerName $serverName `
       -DatabaseName $databaseName `
       -ElasticPoolName $poolName
   $addDatabase
   ```

本教程的這一部分使用以下 PowerShell Cmdlet：

| Command | 注意 |
|---|---|
| [New-AzSqlElasticPool](/powershell/module/az.sql/new-azsqlelasticpool) | 為 Azure SQL 資料庫創建彈性資料庫池。| 
| [Set-AzSqlDatabase](/powershell/module/az.sql/set-azsqldatabase) | 設定資料庫的屬性，或將現有資料庫移到彈性集區中。 | 

---

## <a name="3---create-the-failover-group"></a>3 - 創建容錯移轉組 
在此步驟中，您將在現有 Azure SQL 伺服器和另一個區域中的新 Azure SQL 伺服器之間創建[容錯移轉組](sql-database-auto-failover-group.md)。 然後，將彈性池添加到容錯移轉組。 


# <a name="portal"></a>[入口網站](#tab/azure-portal)

使用 Azure 門戶創建容錯移轉組。 

1. 在[Azure 門戶](https://portal.azure.com)的左側功能表中選擇**Azure SQL。** 如果 **Azure SQL** 不在清單中，請選取 [所有服務]****，然後在搜尋方塊中輸入 Azure SQL。 (選用) 選取 **Azure SQL** 旁的星號將其設為最愛，並新增為左側導覽中的項目。 
1. 選擇上一節中創建的彈性池，如`myElasticPool`。 
1. 在 **"概述"** 窗格中，選擇**伺服器名稱**下的伺服器名稱以打開伺服器的設置。
  
    ![用於彈性池的打開伺服器](media/sql-database-elastic-pool-failover-group-tutorial/server-for-elastic-pool.png)

1. 在 **"設置"** 窗格下選擇**容錯移轉組**，然後選擇 **"添加組**"以創建新的容錯移轉組。 

    ![添加新容錯移轉組](media/sql-database-elastic-pool-failover-group-tutorial/elastic-pool-failover-group.png)

1. 在**容錯移轉組**頁上，輸入或選擇以下值，**然後選擇：**
    - **容錯移轉組名稱**：鍵入唯一容錯移轉組名稱，如`failovergrouptutorial`。 
    - **次要伺服器**：選擇*用於配置所需設置*的選項，然後選擇**創建新伺服器**。 或者，您可以選擇已經存在的伺服器作為次要伺服器。 為新的次要伺服器輸入以下值後，**選擇**。 
        - **伺服器名稱**： 鍵入次要伺服器的唯一名稱，如`mysqlsecondary`。 
        - **伺服器管理員登錄**： 類型`azureuser`
        - **密碼**：鍵入滿足密碼要求的複雜密碼。
        - **位置**：從下拉清單中選擇位置，例如`East US`。 此位置不能與主伺服器位於同一位置。

       > [!NOTE]
       > 伺服器登錄和防火牆設置必須與主伺服器的設置匹配。 
    
       ![為容錯移轉組創建次要伺服器](media/sql-database-elastic-pool-failover-group-tutorial/create-secondary-failover-server.png)

1. 選擇**組中的資料庫，** 然後選擇您在第 2 節中創建的彈性池。 應出現警告，提示您在次要伺服器上創建彈性池。 選擇警告，然後選擇 **"確定"** 以在次要伺服器上創建彈性池。 
        
    ![將彈性池添加到容錯移轉組](media/sql-database-elastic-pool-failover-group-tutorial/add-elastic-pool-to-failover-group.png)
        
1. 選擇 **"選擇"** 可將彈性池設置應用於容錯移轉組，然後選擇 **"創建"** 以創建容錯移轉組。 將彈性池添加到容錯移轉組將自動啟動異地複製過程。


# <a name="powershell"></a>[電源外殼](#tab/azure-powershell)

使用 PowerShell 創建容錯移轉組。 

   ```powershell-interactive
   # Set variables for your server and database
   # $subscriptionId = '<SubscriptionID>'
   # $resourceGroupName = "myResourceGroup-$(Get-Random)"
   # $location = "East US"
   # $adminLogin = "azureuser"
   # $password = "PWD27!"+(New-Guid).Guid
   # $serverName = "mysqlserver-$(Get-Random)"
   # $poolName = "myElasticPool"
   # $databaseName = "mySampleDatabase"
   # $drLocation = "West US"
   # $drServerName = "mysqlsecondary-$(Get-Random)"
   $failoverGroupName = "failovergrouptutorial-$(Get-Random)"

   # Create a secondary server in the failover region
   Write-host "Creating a secondary logical server in the failover region..."
   New-AzSqlServer -ResourceGroupName $resourceGroupName `
      -ServerName $drServerName `
      -Location $drLocation `
      -SqlAdministratorCredentials $(New-Object -TypeName System.Management.Automation.PSCredential `
         -ArgumentList $adminlogin, $(ConvertTo-SecureString -String $password -AsPlainText -Force))
   Write-host "Secondary logical server =" $drServerName
   
   # Create a server firewall rule that allows access from the specified IP range
   Write-host "Configuring firewall for secondary logical server..."
   New-AzSqlServerFirewallRule -ResourceGroupName $resourceGroupName `
      -ServerName $drServerName `
      -FirewallRuleName "AllowedIPs" -StartIpAddress $startIp -EndIpAddress $endIp
   Write-host "Firewall configured" 
   
   # Create secondary Gen5 elastic 2 vCore pool
   Write-host "Creating secondary elastic pool..."
   $elasticPool = New-AzSqlElasticPool -ResourceGroupName $resourceGroupName `
       -ServerName $drServerName `
       -ElasticPoolName $poolName `
       -Edition "GeneralPurpose" `
       -vCore 2 `
       -ComputeGeneration Gen5
   $elasticPool
   
   # Create a failover group between the servers
   Write-host "Creating failover group..." 
   New-AzSqlDatabaseFailoverGroup `
     –ResourceGroupName $resourceGroupName `
      -ServerName $serverName `
      -PartnerServerName $drServerName  `
      –FailoverGroupName $failoverGroupName `
      –FailoverPolicy Automatic `
      -GracePeriodWithDataLossHours 2
   Write-host "Failover group created successfully." 
   
   # Add elastic pool to the failover group
   Write-host "Enumerating databases in elastic pool...." 
   $FailoverGroup = Get-AzSqlDatabaseFailoverGroup `
                    -ResourceGroupName $resourceGroupName `
                    -ServerName $serverName `
                    -FailoverGroupName $failoverGroupName
   $databases = Get-AzSqlElasticPoolDatabase `
                  -ResourceGroupName $resourceGroupName `
                  -ServerName $serverName `
                  -ElasticPoolName $poolName
   Write-host "Adding databases to failover group..." 
   $failoverGroup = $failoverGroup | Add-AzSqlDatabaseToFailoverGroup `
                                     -Database $databases 
   $failoverGroup
   ```

本教程的這一部分使用以下 PowerShell Cmdlet：

| Command | 注意 |
|---|---|
| [新-AzSqlServer](/powershell/module/az.sql/new-azsqlserver) | 建立裝載單一資料庫和彈性集區的 SQL Database 伺服器。 |
| [New-AzSqlServerFirewallRule](/powershell/module/az.sql/new-azsqlserverfirewallrule) | 建立邏輯伺服器的防火牆規則。 | 
| [New-AzSqlElasticPool](/powershell/module/az.sql/new-azsqlelasticpool) | 為 Azure SQL 資料庫創建彈性資料庫池。| 
| [New-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/new-azsqldatabasefailovergroup) | 建立新的容錯移轉群組。 |
| [Add-AzSqlDatabaseToFailoverGroup](/powershell/module/az.sql/add-azsqldatabasetofailovergroup) | 將一或多個 Azure SQL Database 新增至容錯移轉群組。 |
| [Get-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/get-azsqldatabasefailovergroup) | 取得或列出 Azure SQL Database 容錯移轉群組。 |

---


## <a name="4---test-failover"></a>4 - 測試容錯移轉 
在此步驟中，您將容錯移轉組容錯移轉到次要伺服器，然後使用 Azure 門戶失敗。 


# <a name="portal"></a>[入口網站](#tab/azure-portal)

使用 Azure 門戶測試容錯移轉組容錯移轉。 

1. 在[Azure 門戶](https://portal.azure.com)的左側功能表中選擇**Azure SQL。** 如果 **Azure SQL** 不在清單中，請選取 [所有服務]****，然後在搜尋方塊中輸入 Azure SQL。 (選用) 選取 **Azure SQL** 旁的星號將其設為最愛，並新增為左側導覽中的項目。 
1. 選擇上一節中創建的彈性池，如`myElasticPool`。 
1. 在 **"伺服器"名稱**下選擇伺服器的名稱以打開伺服器的設置。

    ![用於彈性池的打開伺服器](media/sql-database-elastic-pool-failover-group-tutorial/server-for-elastic-pool.png)

1. 在 **"設置"** 窗格下選擇**容錯移轉組**，然後選擇您在第 2 節中創建的容錯移轉組。 
  
   ![從門戶中選擇容錯移轉組](media/sql-database-elastic-pool-failover-group-tutorial/select-failover-group.png)

1. 查看主伺服器和次要伺服器。 
1. 從工作窗格中選擇**容錯移轉**以容錯移轉包含彈性池的容錯移轉組。 
1. 在警告上選擇 **"是**"，通知您 TDS 會話將斷開連接。 

   ![容錯移轉組容錯移轉，其中包含 SQL 資料庫](media/sql-database-elastic-pool-failover-group-tutorial/failover-sql-db.png)

1. 查看主伺服器，哪個伺服器是次要伺服器。 如果容錯移轉成功，則兩台伺服器應交換角色。 
1. 再次選擇**容錯移轉**以容錯移轉組故障故障組故障回原始設置。 


# <a name="powershell"></a>[電源外殼](#tab/azure-powershell)

使用 PowerShell 測試容錯移轉組容錯移轉。 

   ```powershell-interactive
   # Set variables for your server and database
   # $subscriptionId = '<SubscriptionID>'
   # $resourceGroupName = "myResourceGroup-$(Get-Random)"
   # $location = "East US"
   # $adminLogin = "azureuser"
   # $password = "PWD27!"+(New-Guid).Guid
   # $serverName = "mysqlserver-$(Get-Random)"
   # $poolName = "myElasticPool"
   # $databaseName = "mySampleDatabase"
   # $drLocation = "West US"
   # $drServerName = "mysqlsecondary-$(Get-Random)"
   # $failoverGroupName = "failovergrouptutorial-$(Get-Random)"
   
   # Check role of secondary replica
   Write-host "Confirming the secondary server is secondary...." 
   (Get-AzSqlDatabaseFailoverGroup `
      -FailoverGroupName $failoverGroupName `
      -ResourceGroupName $resourceGroupName `
      -ServerName $drServerName).ReplicationRole
   
   # Failover to secondary server
   Write-host "Failing over failover group to the secondary..." 
   Switch-AzSqlDatabaseFailoverGroup `
      -ResourceGroupName $resourceGroupName `
      -ServerName $drServerName `
      -FailoverGroupName $failoverGroupName
   Write-host "Failover group failed over to" $drServerName 
   ```

容錯移轉組容錯移轉組容錯移轉到次要伺服器，然後使用 PowerShell 故障恢復。 

   ```powershell-interactive
   # Set variables for your server and database
   # $subscriptionId = '<SubscriptionID>'
   # $resourceGroupName = "myResourceGroup-$(Get-Random)"
   # $location = "East US"
   # $adminLogin = "azureuser"
   # $password = "PWD27!"+(New-Guid).Guid
   # $serverName = "mysqlserver-$(Get-Random)"
   # $poolName = "myElasticPool"
   # $databaseName = "mySampleDatabase"
   # $drLocation = "West US"
   # $drServerName = "mysqlsecondary-$(Get-Random)"
   # $failoverGroupName = "failovergrouptutorial-$(Get-Random)"

   # Check role of secondary replica
   Write-host "Confirming the secondary server is now primary" 
   (Get-AzSqlDatabaseFailoverGroup `
      -FailoverGroupName $failoverGroupName `
      -ResourceGroupName $resourceGroupName `
      -ServerName $drServerName).ReplicationRole
   
   # Revert failover to primary server
   Write-host "Failing over failover group to the primary...." 
   Switch-AzSqlDatabaseFailoverGroup `
      -ResourceGroupName $resourceGroupName `
      -ServerName $serverName `
      -FailoverGroupName $failoverGroupName
   Write-host "Failover group failed over to" $serverName 
   ```

本教程的這一部分使用以下 PowerShell Cmdlet：

| Command | 注意 |
|---|---|
| [Get-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/get-azsqldatabasefailovergroup) | 取得或列出 Azure SQL Database 容錯移轉群組。 |
| [Switch-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/switch-azsqldatabasefailovergroup)| 執行 Azure SQL Database 容錯移轉群組的容錯移轉。 |


---

## <a name="clean-up-resources"></a>清除資源 

通過刪除資源組來清理資源。 


# <a name="portal"></a>[入口網站](#tab/azure-portal)


1. 瀏覽至您在 [Azure 入口網站](https://portal.azure.com)中的資源群組。
1. 選擇 **"刪除資源組**"以刪除組中的所有資源以及資源組本身。 
1. 在文字方塊中鍵入資源組的名稱`myResourceGroup`，然後選擇 **"刪除**"以刪除資源組。 


# <a name="powershell"></a>[電源外殼](#tab/azure-powershell)

使用 PowerShell 清理資源。 

   ```powershell-interactive
   # Set variables for your server and database
   # $resourceGroupName = "myResourceGroup-$(Get-Random)"
   
   # Clean up resources by removing the resource group
   Write-host "Removing resource group..."
   Remove-AzResourceGroup -ResourceGroupName $resourceGroupName
   Write-host "Resource group removed =" $resourceGroupName
   ```

本教程的這一部分使用以下 PowerShell Cmdlet：

| Command | 注意 |
|---|---|
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | 移除資源群組 | 

---

> [!IMPORTANT]
> 如果要保留資源組但刪除次要資料庫，請將其從容錯移轉組中刪除，然後再將其刪除。 在從容錯移轉組中刪除次要資料庫之前刪除該資料庫可能會導致不可預知的行為。 

## <a name="full-script"></a>完整指令碼

# <a name="powershell"></a>[電源外殼](#tab/azure-powershell)

[!code-powershell-interactive[main](../../powershell_scripts/sql-database/failover-groups/add-elastic-pool-to-failover-group-az-ps.ps1 "Add elastic pool to a failover group")]

此指令碼會使用下列命令。 下表中的每個命令都會連結至命令特定的文件。

| Command | 注意 |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | 建立用來存放所有資源的資源群組。 |
| [新-AzSqlServer](/powershell/module/az.sql/new-azsqlserver) | 建立裝載單一資料庫和彈性集區的 SQL Database 伺服器。 |
| [New-AzSqlServerFirewallRule](/powershell/module/az.sql/new-azsqlserverfirewallrule) | 建立邏輯伺服器的防火牆規則。 | 
| [新-AzSql 資料庫](/powershell/module/az.sql/new-azsqldatabase) | 建立新的 Azure SQL Database 單一資料庫。 | 
| [New-AzSqlElasticPool](/powershell/module/az.sql/new-azsqlelasticpool) | 為 Azure SQL 資料庫創建彈性資料庫池。| 
| [Set-AzSqlDatabase](/powershell/module/az.sql/set-azsqldatabase) | 設定資料庫的屬性，或將現有資料庫移到彈性集區中。 | 
| [New-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/new-azsqldatabasefailovergroup) | 建立新的容錯移轉群組。 |
| [Get-AzSqlDatabase](/powershell/module/az.sql/get-azsqldatabase) | 取得一或多個 SQL 資料庫。 |
| [Add-AzSqlDatabaseToFailoverGroup](/powershell/module/az.sql/add-azsqldatabasetofailovergroup) | 將一或多個 Azure SQL Database 新增至容錯移轉群組。 |
| [Get-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/get-azsqldatabasefailovergroup) | 取得或列出 Azure SQL Database 容錯移轉群組。 |
| [Switch-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/switch-azsqldatabasefailovergroup)| 執行 Azure SQL Database 容錯移轉群組的容錯移轉。 |
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | 移除資源群組 | 


# <a name="portal"></a>[入口網站](#tab/azure-portal)
Azure 門戶沒有可用的腳本。

---

## <a name="next-steps"></a>後續步驟

在本教程中，您將 Azure SQL 資料庫彈性池添加到容錯移轉組，並測試了容錯移轉。 您已了解如何︰

> [!div class="checklist"]
> - 創建 Azure SQL 資料庫單個資料庫。
> - 將單個資料庫添加到彈性池中。 
> - 為兩個邏輯 SQL 伺服器之間的兩個彈性池創建[容錯移轉組](sql-database-auto-failover-group.md)。
> - 測試容錯移轉。

請前進到下一個關於如何使用 DMS 進行移轉的教學課程。

> [!div class="nextstepaction"]
> [教程：使用 DMS 將 SQL 伺服器遷移到池資料庫](../dms/tutorial-sql-server-to-azure-sql.md?toc=/azure/sql-database/toc.json)
