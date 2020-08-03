---
title: 教學課程：將資料庫新增至容錯移轉群組
description: 使用 Azure 入口網站、PowerShell 或 Azure CLI，將 Azure SQL Database 中的資料庫新增至 autofailover 群組。
services: sql-database
ms.service: sql-database
ms.subservice: high-availability
ms.custom: sqldbrb=1, devx-track-azurecli
ms.devlang: ''
ms.topic: conceptual
author: MashaMSFT
ms.author: mathoma
ms.reviewer: sstein, carlrab
ms.date: 06/19/2019
ms.openlocfilehash: 4caad36d21d3facb97dc358fdfee61e89c420213
ms.sourcegitcommit: 11e2521679415f05d3d2c4c49858940677c57900
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/31/2020
ms.locfileid: "87496333"
---
# <a name="tutorial-add-an-azure-sql-database-to-an-autofailover-group"></a>教學課程：將 Azure SQL Database 新增至 autofailover 群組
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

[容錯移轉群組](auto-failover-group-overview.md)是宣告式抽象層，可讓您將多個異地複寫資料庫分組。 瞭解如何使用 Azure 入口網站、PowerShell 或 Azure CLI 來設定 Azure SQL Database 的容錯移轉群組，並測試容錯移轉。  在本教學課程中，您將了解如何：

> [!div class="checklist"]
>
> - 在 Azure SQL Database 中建立資料庫
> - 在兩部伺服器之間建立資料庫的容錯移轉群組。
> - 測試容錯移轉。

## <a name="prerequisites"></a>Prerequisites

# <a name="the-portal"></a>[入口網站](#tab/azure-portal)

若要完成本教學課程，請確定您具有下列項目︰

- Azure 訂用帳戶。 如果您還沒有帳戶，請[建立一個免費帳戶](https://azure.microsoft.com/free/)。

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

若要完成本教學課程，請確定您有下列專案：

- Azure 訂用帳戶。 如果您還沒有帳戶，請[建立一個免費帳戶](https://azure.microsoft.com/free/)。
- [Azure PowerShell](/powershell/azure/)

# <a name="the-azure-cli"></a>[Azure CLI](#tab/azure-cli)

若要完成本教學課程，請確定您有下列專案：

- Azure 訂用帳戶。 如果您還沒有帳戶，請[建立一個免費帳戶](https://azure.microsoft.com/free/)。
- [Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest)的最新版本。

---

## <a name="1---create-a-database"></a>1-建立資料庫

[!INCLUDE [sql-database-create-single-database](../includes/sql-database-create-single-database.md)]

## <a name="2---create-the-failover-group"></a>2-建立容錯移轉群組

在此步驟中，您將會在現有的伺服器與另一個區域中的新伺服器之間建立[容錯移轉群組](auto-failover-group-overview.md)。 然後將範例資料庫新增到容錯移轉群組。

# <a name="the-portal"></a>[入口網站](#tab/azure-portal)

建立您的容錯移轉群組，並使用 Azure 入口網站將您的資料庫新增至其中。

1. 在[Azure 入口網站](https://portal.azure.com)的左側功能表中，選取 [ **Azure SQL** ]。 如果**AZURE SQL**不在清單中，請選取 [**所有服務**]，然後在搜尋方塊中輸入 Azure sql。 (選用) 選取 **Azure SQL** 旁的星號將其設為最愛，並新增為左側導覽中的項目。
1. 選取在第1節中建立的資料庫，例如 `mySampleDatabase` 。
1. 您可以在伺服器層級設定容錯移轉群組。 在 [**伺服器名稱**] 底下選取伺服器的名稱，以開啟伺服器的設定。

   ![開啟資料庫的伺服器](./media/failover-group-add-single-database-tutorial/open-sql-db-server.png)

1. 在 [**設定**] 窗格下選取 [**容錯移轉群組**]，然後選取 [**新增群組**] 以建立新的容錯移轉群組。

   ![加入新的容錯移轉群組](./media/failover-group-add-single-database-tutorial/sqldb-add-new-failover-group.png)

1. 在 [**容錯移轉群組**] 頁面上，輸入或選取下列值，然後選取 [**建立**]：

   - **容錯移轉組名**：輸入唯一的容錯移轉組名，例如 `failovergrouptutorial` 。
   - **次要伺服器**：選取 [*設定必要設定*] 選項，然後選擇 [**建立新的伺服器**]。 或者，您也可以選擇已經存在的伺服器做為次要伺服器。 輸入下列值之後，請選取 [**選取**]。
      - **伺服器名稱**：輸入次要伺服器的唯一名稱，例如 `mysqlsecondary` 。
      - **伺服器管理員登**入：輸入`azureuser`
      - **密碼**：輸入符合密碼需求的複雜密碼。
      - **位置**：從下拉式選單中選擇位置，例如 `East US` 。 這個位置不能與您的主伺服器位於相同的位置。

     > [!NOTE]
     > 伺服器登入和防火牆設定必須符合您的主伺服器。

     ![建立容錯移轉群組的次要伺服器](./media/failover-group-add-single-database-tutorial/create-secondary-failover-server.png)

   - **群組內的資料庫**：選取次要伺服器之後，這個選項就會變成 [解除鎖定]。 選取此項以**選取要新增的資料庫**，然後選擇您在第1節中建立的資料庫。 將資料庫新增到容錯移轉群組，將會自動啟動異地複寫程式。

   ![將 SQL Database 新增至容錯移轉群組](./media/failover-group-add-single-database-tutorial/add-sqldb-to-failover-group.png)

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

建立您的容錯移轉群組，並使用 PowerShell 將您的資料庫新增至其中。

   > [!NOTE]
   > 伺服器登入和防火牆設定必須符合您的主伺服器。

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
   Write-host "Creating a secondary server in the failover region..."
   $drServer = New-AzSqlServer -ResourceGroupName $resourceGroupName `
      -ServerName $drServerName `
      -Location $drLocation `
      -SqlAdministratorCredentials $(New-Object -TypeName System.Management.Automation.PSCredential `
         -ArgumentList $adminlogin, $(ConvertTo-SecureString -String $password -AsPlainText -Force))
   $drServer

   # Create a server firewall rule that allows access from the specified IP range
   Write-host "Configuring firewall for secondary server..."
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

本教學課程的這個部分會使用下列 PowerShell Cmdlet：

| Command | 注意 |
|---|---|
| [New-AzSqlServer](/powershell/module/az.sql/new-azsqlserver) | 在裝載單一資料庫和彈性集區的 Azure SQL Database 中建立伺服器。 |
| [New-AzSqlServerFirewallRule](/powershell/module/az.sql/new-azsqlserverfirewallrule) | 在 Azure SQL Database 中建立伺服器的防火牆規則。 |
| [New-AzSqlDatabase](/powershell/module/az.sql/new-azsqldatabase) | 在 Azure SQL Database 中建立新的單一資料庫。 |
| [New-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/new-azsqldatabasefailovergroup) | 在 Azure SQL Database 中建立新的容錯移轉群組。 |
| [Get-AzSqlDatabase](/powershell/module/az.sql/get-azsqldatabase) | 取得 Azure SQL Database 中的一個或多個資料庫。 |
| [Add-AzSqlDatabaseToFailoverGroup](/powershell/module/az.sql/add-azsqldatabasetofailovergroup) | 將一個或多個資料庫新增至 Azure SQL Database 中的容錯移轉群組。 |

# <a name="the-azure-cli"></a>[Azure CLI](#tab/azure-cli)

建立您的容錯移轉群組，並使用 Azure CLI 將您的資料庫新增至其中。

   > [!NOTE]
   > 伺服器登入和防火牆設定必須符合您的主伺服器。

   ```azurecli-interactive
   #!/bin/bash
   # set variables
   $failoverLocation = "West US"
   $failoverServer = "failoverServer-$randomIdentifier"
   $failoverGroup = "failoverGroup-$randomIdentifier"

   echo "Creating a secondary server in the DR region..."
   az sql server create --name $failoverServer --resource-group $resourceGroup --location $failoverLocation --admin-user $login --admin-password $password

   echo "Creating a failover group between the two servers..."
   az sql failover-group create --name $failoverGroup --partner-server $failoverServer --resource-group $resourceGroup --server $server --add-db $database --failover-policy Automatic
   ```

本教學課程的這個部分會使用下列 Azure CLI Cmdlet：

| Command | 注意 |
|---|---|
| [az sql server create](/cli/azure/sql/server#az-sql-server-create) | 建立裝載資料庫和彈性集區的伺服器。 |
| [az sql server firewall-rule create](/cli/azure/sql/server/firewall-rule) | 建立伺服器的防火牆規則。 |
| [az sql failover-group create](/cli/azure/sql/failover-group?view=azure-cli-latest#az-sql-failover-group-create) | 建立容錯移轉群組。 |

---

## <a name="3---test-failover"></a>3-測試容錯移轉

在此步驟中，您會將容錯移轉群組容錯移轉到次要伺服器，然後使用 Azure 入口網站進行容錯回復。

# <a name="the-portal"></a>[入口網站](#tab/azure-portal)

使用 Azure 入口網站測試容錯移轉。

1. 在[Azure 入口網站](https://portal.azure.com)的左側功能表中，選取 [ **Azure SQL** ]。 如果**AZURE SQL**不在清單中，請選取 [**所有服務**]，然後在搜尋方塊中輸入 Azure sql。 (選用) 選取 **Azure SQL** 旁的星號將其設為最愛，並新增為左側導覽中的項目。
1. 選取在第2節中建立的資料庫，例如 `mySampleDatbase` 。
1. 在 [**伺服器名稱**] 底下選取伺服器的名稱，以開啟伺服器的設定。

   ![開啟資料庫的伺服器](./media/failover-group-add-single-database-tutorial/open-sql-db-server.png)

1. 在 [**設定**] 窗格下選取 [**容錯移轉群組**]，然後選擇您在第2節中建立的容錯移轉群組。
  
   ![從入口網站選取容錯移轉群組](./media/failover-group-add-single-database-tutorial/select-failover-group.png)

1. 檢查哪一個伺服器是主要伺服器，哪一個是次要伺服器。
1. 從工作窗格中選取 [**容錯移轉**]，以容錯移轉包含範例資料庫的容錯移轉群組。
1. 在通知您將會中斷 TDS 會話的警告上，選取 **[是]** 。

   ![容錯移轉包含您資料庫的容錯移轉群組](./media/failover-group-add-single-database-tutorial/failover-sql-db.png)

1. 檢查哪一個伺服器現在是主要的，哪個伺服器是次要的。 如果容錯移轉成功，這兩部伺服器應該會有已交換的角色。
1. 再次選取 [**容錯移轉**]，讓伺服器無法回到其原始角色。

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

使用 PowerShell 測試容錯移轉。

檢查次要複本的角色：

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

故障切換到次要伺服器：

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

本教學課程的這個部分會使用下列 PowerShell Cmdlet：

| Command | 注意 |
|---|---|
| [Get-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/get-azsqldatabasefailovergroup) | 取得或列出 Azure SQL Database 容錯移轉群組。 |
| [Switch-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/switch-azsqldatabasefailovergroup)| 執行 Azure SQL Database 容錯移轉群組的容錯移轉。 |

# <a name="the-azure-cli"></a>[Azure CLI](#tab/azure-cli)

使用 Azure CLI 測試容錯移轉。

確認哪一個伺服器是次要複本：

   ```azurecli-interactive
   echo "Verifying which server is in the secondary role..."
   az sql failover-group list --server $server --resource-group $resourceGroup
   ```

故障切換到次要伺服器：

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

本教學課程的這個部分會使用下列 Azure CLI Cmdlet：

| Command | 注意 |
|---|---|
| [az sql failover-group list](/cli/azure/sql/failover-group?view=azure-cli-latest#az-sql-failover-group-list) | 列出伺服器中的容錯移轉群組。 |
| [az sql failover-group set-primary](/cli/azure/sql/failover-group?view=azure-cli-latest#az-sql-failover-group-set-primary) | 從目前的主伺服器容錯移轉所有資料庫，以設定容錯移轉群組的主要複本。 |

---

## <a name="clean-up-resources"></a>清除資源

藉由刪除資源群組來清除資源。

# <a name="the-portal"></a>[入口網站](#tab/azure-portal)

使用 Azure 入口網站刪除資源群組。

1. 瀏覽至您在 [Azure 入口網站](https://portal.azure.com)中的資源群組。
1. 選取 [**刪除資源群組**] 以刪除群組中的所有資源，以及資源群組本身。
1. 在文字方塊中輸入資源群組的名稱， `myResourceGroup` 然後選取 [**刪除**] 以刪除資源群組。  

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

使用 PowerShell 刪除資源群組。

   ```powershell-interactive
   # Set variables
   # $resourceGroupName = "myResourceGroup-$(Get-Random)"

   # Remove the resource group
   Write-host "Removing resource group..."
   Remove-AzResourceGroup -ResourceGroupName $resourceGroupName
   Write-host "Resource group removed =" $resourceGroupName
   ```

本教學課程的這個部分會使用下列 PowerShell Cmdlet：

| Command | 注意 |
|---|---|
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | 移除資源群組 |

# <a name="the-azure-cli"></a>[Azure CLI](#tab/azure-cli)

使用 Azure CLI 刪除資源群組。

   ```azurecli-interactive
   echo "Cleaning up resources by removing the resource group..."
   az group delete --name $resourceGroup
   echo "Successfully removed resource group" $resourceGroup
   ```

本教學課程的這個部分會使用下列 Azure CLI Cmdlet：

| Command | 注意 |
|---|---|
| [az group delete](https://docs.microsoft.com/cli/azure/vm/extension#az-vm-extension-set) | 刪除資源群組，包括所有的巢狀資源。 |

---

> [!IMPORTANT]
> 如果您想要保留資源群組，但要刪除次要資料庫，請先將其從容錯移轉群組中移除，然後再刪除它。 將次要資料庫從容錯移轉群組中移除之前先將它刪除，可能會導致無法預期的行為。

## <a name="full-scripts"></a>完整腳本

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

[!code-powershell-interactive[main](../../../powershell_scripts/sql-database/failover-groups/add-single-db-to-failover-group-az-ps.ps1 "Add database to a failover group")]

此指令碼會使用下列命令。 下表中的每個命令都會連結至命令特定的文件。

| Command | 注意 |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | 建立用來存放所有資源的資源群組。 |
| [New-AzSqlServer](/powershell/module/az.sql/new-azsqlserver) | 在 Azure SQL Database 中建立裝載單一資料庫和彈性集區的伺服器。 |
| [New-AzSqlServerFirewallRule](/powershell/module/az.sql/new-azsqlserverfirewallrule) | 在 Azure SQL Database 中建立伺服器的防火牆規則。 |
| [New-AzSqlDatabase](/powershell/module/az.sql/new-azsqldatabase) | 在 Azure SQL Database 中建立新的資料庫。 |
| [New-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/new-azsqldatabasefailovergroup) | 在 Azure SQL Database 中建立新的容錯移轉群組。 |
| [Get-AzSqlDatabase](/powershell/module/az.sql/get-azsqldatabase) | 取得 Azure SQL Database 中的一個或多個資料庫。 |
| [Add-AzSqlDatabaseToFailoverGroup](/powershell/module/az.sql/add-azsqldatabasetofailovergroup) | 將一個或多個資料庫新增至 Azure SQL Database 中的容錯移轉群組。 |
| [Get-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/get-azsqldatabasefailovergroup) | 取得或列出 Azure SQL Database 中的容錯移轉群組。 |
| [Switch-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/switch-azsqldatabasefailovergroup)| 在 Azure SQL Database 中執行容錯移轉群組的容錯移轉。 |
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | 移除 Azure SQL Database 中的資源群組。|

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

[!code-azurecli-interactive[main](../../../cli_scripts/sql-database/failover-groups/add-single-db-to-failover-group-az-cli.sh "Add database to a failover group")]

此指令碼會使用下列命令。 下表中的每個命令都會連結至命令特定的文件。

| Command | 注意 |
|---|---|
| [az account set](/cli/azure/account?view=azure-cli-latest#az-account-set) | 將訂用帳戶設定為目前使用中的訂用帳戶。 |
| [az group create](/cli/azure/group#az-group-create) | 建立用來存放所有資源的資源群組。 |
| [az sql server create](/cli/azure/sql/server#az-sql-server-create) | 在 Azure SQL Database 中建立裝載單一資料庫和彈性集區的伺服器。 |
| [az sql server firewall-rule create](/cli/azure/sql/server/firewall-rule) | 在 Azure SQL Database 中建立伺服器層級 IP 防火牆規則。 |
| [az sql db create](/cli/azure/sql/db?view=azure-cli-latest) | 在 Azure SQL Database 中建立資料庫。 |
| [az sql failover-group create](/cli/azure/sql/failover-group?view=azure-cli-latest#az-sql-failover-group-create) | 在 Azure SQL Database 中建立容錯移轉群組。 |
| [az sql failover-group list](/cli/azure/sql/failover-group?view=azure-cli-latest#az-sql-failover-group-list) | 列出伺服器中的容錯移轉群組 Azure SQL Database。 |
| [az sql failover-group set-primary](/cli/azure/sql/failover-group?view=azure-cli-latest#az-sql-failover-group-set-primary) | 從目前的主伺服器容錯移轉所有資料庫，以設定容錯移轉群組的主要複本。 |
| [az group delete](https://docs.microsoft.com/cli/azure/vm/extension#az-vm-extension-set) | 刪除資源群組，包括所有的巢狀資源。 |

# <a name="the-portal"></a>[入口網站](#tab/azure-portal)

沒有可供 Azure 入口網站使用的腳本。

---

您可以在這裡找到其他 Azure SQL Database 腳本： [Azure PowerShell](powershell-script-content-guide.md)和[Azure CLI](az-cli-script-samples-content-guide.md)。

## <a name="next-steps"></a>後續步驟

在本教學課程中，您已將 Azure SQL Database 中的資料庫新增到容錯移轉群組，並已測試容錯移轉。 您已了解如何︰

> [!div class="checklist"]
>
> - 在 Azure SQL Database 中建立資料庫
> - 在兩部伺服器之間建立資料庫的容錯移轉群組。
> - 測試容錯移轉。

前進到下一個教學課程，以瞭解如何將彈性集區新增至容錯移轉群組。

> [!div class="nextstepaction"]
> [教學課程：將 Azure SQL Database 彈性集區新增至容錯移轉群組](failover-group-add-elastic-pool-tutorial.md)
