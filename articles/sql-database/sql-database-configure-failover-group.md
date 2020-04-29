---
title: 設定容錯移轉群組
description: 瞭解如何使用 Azure 入口網站、Az CLI 和 PowerShell，為 Azure SQL Database 單一資料庫、彈性集區和受控實例設定自動容錯移轉群組。
services: sql-database
ms.service: sql-database
ms.subservice: high-availability
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: MashaMSFT
ms.author: mathoma
ms.reviewer: sstein, carlrab
ms.date: 08/14/2019
ms.openlocfilehash: 3b423a25b6b13ad543ef4a74bc0335ce19f5766d
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "77461796"
---
# <a name="configure-a-failover-group-for-azure-sql-database"></a>設定 Azure SQL Database 的容錯移轉群組

本主題將教您如何使用 Azure 入口網站或 PowerShell，為 Azure SQL Database 單一資料庫、彈性集區和受控實例設定[自動容錯移轉群組](sql-database-auto-failover-group.md)。 

## <a name="single-database"></a>單一資料庫
使用 Azure 入口網站或 PowerShell，建立容錯移轉群組，並在其中新增單一資料庫。

### <a name="prerequisites"></a>先決條件

請考慮下列必要條件：

- 次要伺服器的伺服器登入和防火牆設定必須符合您的主伺服器。 

### <a name="create-failover-group"></a>建立容錯移轉群組

# <a name="portal"></a>[入口網站](#tab/azure-portal)
建立您的容錯移轉群組，並使用 Azure 入口網站將您的單一資料庫新增至其中。


1. 在[Azure 入口網站](https://portal.azure.com)的左側功能表中，選取 [ **Azure SQL** ]。 如果 **Azure SQL** 不在清單中，請選取 [所有服務]****，然後在搜尋方塊中輸入 Azure SQL。 (選用) 選取 **Azure SQL** 旁的星號將其設為最愛，並新增為左側導覽中的項目。 
1. 選取您想要新增到容錯移轉群組的單一資料庫。 
1. 在 [**伺服器名稱**] 底下選取伺服器的名稱，以開啟伺服器的設定。

   ![單一 db 的開啟伺服器](media/sql-database-single-database-failover-group-tutorial/open-sql-db-server.png)

1. 在 [**設定**] 窗格下選取 [**容錯移轉群組**]，然後選取 [**新增群組**] 以建立新的容錯移轉群組。 

    ![加入新的容錯移轉群組](media/sql-database-single-database-failover-group-tutorial/sqldb-add-new-failover-group.png)

1. 在 [**容錯移轉群組**] 頁面上，輸入或選取所需的值，然後選取 [**建立**]。

   - **群組內的資料庫**：選擇您要加入至容錯移轉群組的資料庫。 將資料庫新增到容錯移轉群組，將會自動啟動異地複寫程式。 
        
    ![將 SQL DB 新增到容錯移轉群組](media/sql-database-single-database-failover-group-tutorial/add-sqldb-to-failover-group.png)

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
建立您的容錯移轉群組，並使用 PowerShell 將您的單一資料庫新增至其中。 

   ```powershell-interactive
   $subscriptionId = "<SubscriptionID>"
   $resourceGroupName = "<Resource-Group-Name>"
   $location = "<Region>"
   $adminLogin = "<Admin-Login>"
   $password = "<Complex-Password>"
   $serverName = "<Primary-Server-Name>"
   $databaseName = "<Database-Name>"
   $drLocation = "<DR-Region>"
   $drServerName = "<Secondary-Server-Name>"
   $failoverGroupName = "<Failover-Group-Name>"

   # Create a secondary server in the failover region
   Write-host "Creating a secondary logical server in the failover region..."
   $drServer = New-AzSqlServer -ResourceGroupName $resourceGroupName `
      -ServerName $drServerName `
      -Location $drLocation `
      -SqlAdministratorCredentials $(New-Object -TypeName System.Management.Automation.PSCredential `
         -ArgumentList $adminlogin, $(ConvertTo-SecureString -String $password -AsPlainText -Force))
   $drServer
   
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

---

### <a name="test-failover"></a>測試容錯移轉 

使用 Azure 入口網站或 PowerShell 測試容錯移轉群組的容錯移轉。 

# <a name="portal"></a>[入口網站](#tab/azure-portal)

使用 Azure 入口網站測試容錯移轉群組的容錯移轉。 

1. 在[Azure 入口網站](https://portal.azure.com)的左側功能表中，選取 [ **Azure SQL** ]。 如果 **Azure SQL** 不在清單中，請選取 [所有服務]****，然後在搜尋方塊中輸入 Azure SQL。 (選用) 選取 **Azure SQL** 旁的星號將其設為最愛，並新增為左側導覽中的項目。 
1. 選取您想要新增到容錯移轉群組的單一資料庫。 

   ![單一 db 的開啟伺服器](media/sql-database-single-database-failover-group-tutorial/open-sql-db-server.png)

1. 在 [**設定**] 窗格下選取 [**容錯移轉群組**]，然後選擇您剛建立的容錯移轉群組。 
  
   ![從入口網站選取容錯移轉群組](media/sql-database-single-database-failover-group-tutorial/select-failover-group.png)

1. 檢查哪一個伺服器是主要伺服器，哪一個是次要伺服器。 
1. 從工作窗格中選取 [**容錯移轉**]，以容錯移轉包含單一資料庫的容錯移轉群組。 
1. 在通知您將會中斷 TDS 會話的警告上，選取 **[是]** 。 

   ![容錯移轉包含 SQL 資料庫的容錯移轉群組](media/sql-database-single-database-failover-group-tutorial/failover-sql-db.png)

1. 檢查哪一個伺服器現在是主要的，哪個伺服器是次要的。 如果容錯移轉成功，這兩部伺服器應該會有已交換的角色。 
1. 再次選取 [**容錯移轉**]，讓伺服器無法回到原先的角色。 

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

使用 PowerShell 測試容錯移轉群組的容錯移轉。  

檢查次要複本的角色： 

   ```powershell-interactive
   # Set variables
   $resourceGroupName = "<Resource-Group-Name>"
   $serverName = "<Primary-Server-Name>"
   $failoverGroupName = "<Failover-Group-Name>"
   
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
   $resourceGroupName = "<Resource-Group-Name>"
   $serverName = "<Primary-Server-Name>"
   $failoverGroupName = "<Failover-Group-Name>"
   
   # Failover to secondary server
   Write-host "Failing over failover group to the secondary..." 
   Switch-AzSqlDatabaseFailoverGroup `
      -ResourceGroupName $resourceGroupName `
      -ServerName $drServerName `
      -FailoverGroupName $failoverGroupName
   Write-host "Failed failover group to successfully to" $drServerName 
   ```

將容錯移轉群組還原至主要伺服器：

   ```powershell-interactive
   # Set variables
   $resourceGroupName = "<Resource-Group-Name>"
   $serverName = "<Primary-Server-Name>"
   $failoverGroupName = "<Failover-Group-Name>"
   
   # Revert failover to primary server
   Write-host "Failing over failover group to the primary...." 
   Switch-AzSqlDatabaseFailoverGroup `
      -ResourceGroupName $resourceGroupName `
      -ServerName $serverName `
      -FailoverGroupName $failoverGroupName
   Write-host "Failed failover group successfully to back to" $serverName
   ```

---

> [!IMPORTANT]
> 如果您需要刪除次要資料庫，請將它從容錯移轉群組中移除，然後再刪除它。 將次要資料庫從容錯移轉群組中移除之前先將它刪除，可能會導致無法預期的行為。 

## <a name="elastic-pool"></a>彈性集區
使用 Azure 入口網站或 PowerShell，建立容錯移轉群組，並將彈性集區新增至其中。  

### <a name="prerequisites"></a>先決條件

請考慮下列必要條件：

- 次要伺服器的伺服器登入和防火牆設定必須符合您的主伺服器。 

### <a name="create-the-failover-group"></a>建立容錯移轉群組 

使用 Azure 入口網站或 PowerShell，為您的彈性集區建立容錯移轉群組。 

# <a name="portal"></a>[入口網站](#tab/azure-portal)
建立您的容錯移轉群組，並使用 Azure 入口網站將彈性集區新增至其中。

1. 在[Azure 入口網站](https://portal.azure.com)的左側功能表中，選取 [ **Azure SQL** ]。 如果 **Azure SQL** 不在清單中，請選取 [所有服務]****，然後在搜尋方塊中輸入 Azure SQL。 (選用) 選取 **Azure SQL** 旁的星號將其設為最愛，並新增為左側導覽中的項目。 
1. 選取您想要新增到容錯移轉群組的彈性集區。 
1. 在 [**總覽**] 窗格中，選取 [**伺服器名稱**] 下的伺服器名稱，以開啟伺服器的設定。
  
    ![開啟適用于彈性集區的伺服器](media/sql-database-elastic-pool-failover-group-tutorial/server-for-elastic-pool.png)

1. 在 [**設定**] 窗格下選取 [**容錯移轉群組**]，然後選取 [**新增群組**] 以建立新的容錯移轉群組。 

    ![加入新的容錯移轉群組](media/sql-database-single-database-failover-group-tutorial/sqldb-add-new-failover-group.png)

1. 在 [**容錯移轉群組**] 頁面上，輸入或選取所需的值，然後選取 [**建立**]。 請建立新的次要伺服器，或選取現有的次要伺服器。 

1. 選取**群組中**的 [資料庫]，然後選擇您想要新增到容錯移轉群組的彈性集區。 如果次要伺服器上還沒有彈性集區存在，則會出現警告，提示您在次要伺服器上建立彈性集區。 選取警告，然後選取 **[確定]** 以在次要伺服器上建立彈性集區。 
        
    ![將彈性集區新增至容錯移轉群組](media/sql-database-elastic-pool-failover-group-tutorial/add-elastic-pool-to-failover-group.png)
        
1. 選取 [**選取**] 將您的彈性集區設定套用到容錯移轉群組，然後選取 [**建立**] 以建立容錯移轉群組。 將彈性集區新增至容錯移轉群組，將會自動啟動異地複寫程式。 

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

建立您的容錯移轉群組，並使用 PowerShell 將您的彈性集區新增至其中。 

   ```powershell-interactive
   $subscriptionId = "<SubscriptionID>"
   $resourceGroupName = "<Resource-Group-Name>"
   $location = "<Region>"
   $adminLogin = "<Admin-Login>"
   $password = "<Complex-Password>"
   $serverName = "<Primary-Server-Name>"
   $databaseName = "<Database-Name>"
   $poolName = "myElasticPool"
   $drLocation = "<DR-Region>"
   $drServerName = "<Secondary-Server-Name>"
   $failoverGroupName = "<Failover-Group-Name>"

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
   Write-host "Databases added to failover group successfully." 
  ```

---

### <a name="test-failover"></a>測試容錯移轉

使用 Azure 入口網站或 PowerShell 測試彈性集區的容錯移轉。 

# <a name="portal"></a>[入口網站](#tab/azure-portal)

將容錯移轉群組容錯移轉到次要伺服器，然後使用 Azure 入口網站進行容錯回復。 

1. 在[Azure 入口網站](https://portal.azure.com)的左側功能表中，選取 [ **Azure SQL** ]。 如果 **Azure SQL** 不在清單中，請選取 [所有服務]****，然後在搜尋方塊中輸入 Azure SQL。 (選用) 選取 **Azure SQL** 旁的星號將其設為最愛，並新增為左側導覽中的項目。 
1. 選取您想要新增到容錯移轉群組的彈性集區。 
1. 在 [**總覽**] 窗格中，選取 [**伺服器名稱**] 下的伺服器名稱，以開啟伺服器的設定。
  
    ![開啟適用于彈性集區的伺服器](media/sql-database-elastic-pool-failover-group-tutorial/server-for-elastic-pool.png)
1. 在 [**設定**] 窗格下選取 [**容錯移轉群組**]，然後選擇您在第2節中建立的容錯移轉群組。 
  
   ![從入口網站選取容錯移轉群組](media/sql-database-single-database-failover-group-tutorial/select-failover-group.png)

1. 檢查哪一台伺服器是主要的，哪個伺服器是次要的。 
1. 從工作窗格中選取 [**容錯移轉**]，以容錯移轉包含彈性集區的容錯移轉群組。 
1. 在通知您將會中斷 TDS 會話的警告上，選取 **[是]** 。 

   ![容錯移轉包含 SQL 資料庫的容錯移轉群組](media/sql-database-single-database-failover-group-tutorial/failover-sql-db.png)

1. 檢查哪一個伺服器是主要的，哪一個是次要伺服器。 如果容錯移轉成功，這兩部伺服器應該會有已交換的角色。 
1. 再次選取 [**容錯移轉**]，將容錯移轉群組容錯回復至原始設定。 

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

使用 PowerShell 測試容錯移轉群組的容錯移轉。

檢查次要複本的角色： 

   ```powershell-interactive
   # Set variables
   $resourceGroupName = "<Resource-Group-Name>"
   $serverName = "<Primary-Server-Name>"
   $failoverGroupName = "<Failover-Group-Name>"
   
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
   $resourceGroupName = "<Resource-Group-Name>"
   $serverName = "<Primary-Server-Name>"
   $failoverGroupName = "<Failover-Group-Name>"
   
   # Failover to secondary server
   Write-host "Failing over failover group to the secondary..." 
   Switch-AzSqlDatabaseFailoverGroup `
      -ResourceGroupName $resourceGroupName `
      -ServerName $drServerName `
      -FailoverGroupName $failoverGroupName
   Write-host "Failed failover group to successfully to" $drServerName 
   ```

---

> [!IMPORTANT]
> 如果您需要刪除次要資料庫，請將它從容錯移轉群組中移除，然後再刪除它。 將次要資料庫從容錯移轉群組中移除之前先將它刪除，可能會導致無法預期的行為。 

## <a name="managed-instance"></a>受控執行個體

使用 Azure 入口網站或 PowerShell，在兩個受控實例之間建立容錯移轉群組。 

您將需要設定[ExpressRoute](../expressroute/expressroute-howto-circuit-portal-resource-manager.md) ，或為每個受控實例的虛擬網路建立閘道、連接兩個閘道，然後建立容錯移轉群組。 

### <a name="prerequisites"></a>先決條件
請考慮下列必要條件：

- 次要受控實例必須是空的。
- 次要虛擬網路的子網範圍不得與主要虛擬網路的子網範圍重迭。 
- 次要實例的定序和時區必須符合主要實例的定序和時區。 
- 連接這兩個閘道時，這兩個連線的**共用金鑰**應相同。 

### <a name="create-primary-virtual-network-gateway"></a>建立主要虛擬網路閘道 

如果您尚未設定[ExpressRoute](../expressroute/expressroute-howto-circuit-portal-resource-manager.md)，您可以使用 Azure 入口網站或 PowerShell 來建立主要虛擬網路閘道。 

# <a name="portal"></a>[入口網站](#tab/azure-portal)

使用 Azure 入口網站建立主要虛擬網路閘道。 

1. 在 [ [Azure 入口網站](https://portal.azure.com)中，移至您的資源群組，然後選取主要受控實例的**虛擬網路**資源。 
1. 選取 [**設定**] 底下的 [**子網**]，然後選取以新增新的**閘道子網**。 保留預設值。 

   ![為主要受控實例新增閘道](media/sql-database-managed-instance-failover-group-tutorial/add-subnet-gateway-primary-vnet.png)

1. 建立子網閘道之後，從左側流覽窗格中選取 [**建立資源**]，然後`Virtual network gateway`在 [搜尋] 方塊中輸入。 選取**Microsoft**發佈的**虛擬網路閘道**資源。 

   ![建立新的虛擬網路閘道](media/sql-database-managed-instance-failover-group-tutorial/create-virtual-network-gateway.png)

1. 填寫必要欄位，以設定您的主要受控實例閘道。 

   下表顯示主要受控實例的閘道所需的值：
 
    | **欄位** | 值 |
    | --- | --- |
    | **訂用帳戶** |  您的主要受控實例所在的訂用帳戶。 |
    | **Name** | 虛擬網路閘道的名稱。 | 
    | **區域** | 次要受控實例所在的區域。 |
    | **閘道類型** | 選取 [ **VPN**]。 |
    | **VPN 類型** | 選取以**路由為基礎的** |
    | **SKU**| 保留預設值`VpnGw1`。 |
    | **位置**| 次要受控實例和次要虛擬網路所在的位置。   |
    | **虛擬網路**| 選取次要受控實例的虛擬網路。 |
    | **公用 IP 位址**| 選取 [建立新的]  。 |
    | **公用 IP 位址名稱**| 輸入 IP 位址的 [名稱]。 |
    | &nbsp; | &nbsp; |

1. 將其他值保留為 [預設]，然後選取 [**檢查 + 建立**] 來檢查虛擬網路閘道的設定。

   ![主要閘道設定](media/sql-database-managed-instance-failover-group-tutorial/settings-for-primary-gateway.png)

1. 選取 [**建立**] 以建立新的虛擬網路閘道。 

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

使用 PowerShell 建立主要虛擬網路閘道。 

   ```powershell-interactive
   $primaryResourceGroupName = "<Primary-Resource-Group>"
   $primaryVnetName = "<Primary-Virtual-Network-Name>"
   $primaryGWName = "<Primary-Gateway-Name>"
   $primaryGWPublicIPAddress = $primaryGWName + "-ip"
   $primaryGWIPConfig = $primaryGWName + "-ipc"
   $primaryGWAsn = 61000
   
   # Get the primary virtual network
   $vnet1 = Get-AzVirtualNetwork -Name $primaryVnetName -ResourceGroupName $primaryResourceGroupName
   $primaryLocation = $vnet1.Location
   
   # Create primary gateway
   Write-host "Creating primary gateway..."
   $subnet1 = Get-AzVirtualNetworkSubnetConfig -Name GatewaySubnet -VirtualNetwork $vnet1
   $gwpip1= New-AzPublicIpAddress -Name $primaryGWPublicIPAddress -ResourceGroupName $primaryResourceGroupName `
            -Location $primaryLocation -AllocationMethod Dynamic
   $gwipconfig1 = New-AzVirtualNetworkGatewayIpConfig -Name $primaryGWIPConfig `
            -SubnetId $subnet1.Id -PublicIpAddressId $gwpip1.Id
    
   $gw1 = New-AzVirtualNetworkGateway -Name $primaryGWName -ResourceGroupName $primaryResourceGroupName `
       -Location $primaryLocation -IpConfigurations $gwipconfig1 -GatewayType Vpn `
       -VpnType RouteBased -GatewaySku VpnGw1 -EnableBgp $true -Asn $primaryGWAsn
   $gw1
   ```

---

### <a name="create-secondary-virtual-network-gateway"></a>建立次要虛擬網路閘道

使用 Azure 入口網站或 PowerShell 建立次要虛擬網路閘道。 

# <a name="portal"></a>[入口網站](#tab/azure-portal)
重複上一節中的步驟，以建立次要受控實例的虛擬網路子網和閘道。 填寫必要欄位，為您的次要受控實例設定閘道。 

   下表顯示次要受控實例的閘道所需的值：

   | **欄位** | 值 |
   | --- | --- |
   | **訂用帳戶** |  次要受控實例所在的訂用帳戶。 |
   | **Name** | 虛擬網路閘道的名稱，例如`secondary-mi-gateway`。 | 
   | **區域** | 次要受控實例所在的區域。 |
   | **閘道類型** | 選取 [ **VPN**]。 |
   | **VPN 類型** | 選取以**路由為基礎的** |
   | **SKU**| 保留預設值`VpnGw1`。 |
   | **位置**| 次要受控實例和次要虛擬網路所在的位置。   |
   | **虛擬網路**| 選取在第2節中建立的虛擬網路，例如`vnet-sql-mi-secondary`。 |
   | **公用 IP 位址**| 選取 [建立新的]  。 |
   | **公用 IP 位址名稱**| 輸入 IP 位址的名稱，例如`secondary-gateway-IP`。 |
   | &nbsp; | &nbsp; |

   ![次要閘道設定](media/sql-database-managed-instance-failover-group-tutorial/settings-for-secondary-gateway.png)

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

使用 PowerShell 建立次要虛擬網路閘道。 

   ```powershell-interactive
   $secondaryResourceGroupName = "<Secondary-Resource-Group>"
   $secondaryVnetName = "<Secondary-Virtual-Network-Name>"
   $secondaryGWName = "<Secondary-Gateway-Name>"
   $secondaryGWPublicIPAddress = $secondaryGWName + "-IP"
   $secondaryGWIPConfig = $secondaryGWName + "-ipc"
   $secondaryGWAsn = 62000
   
   # Get the secondary virtual network
   $vnet2 = Get-AzVirtualNetwork -Name $secondaryVnetName -ResourceGroupName $secondaryResourceGroupName
   $secondaryLocation = $vnet2.Location
    
   # Create the secondary gateway
   Write-host "Creating secondary gateway..."
   $subnet2 = Get-AzVirtualNetworkSubnetConfig -Name GatewaySubnet -VirtualNetwork $vnet2
   $gwpip2= New-AzPublicIpAddress -Name $secondaryGWPublicIPAddress -ResourceGroupName $secondaryResourceGroupName `
            -Location $secondaryLocation -AllocationMethod Dynamic
   $gwipconfig2 = New-AzVirtualNetworkGatewayIpConfig -Name $secondaryGWIPConfig `
            -SubnetId $subnet2.Id -PublicIpAddressId $gwpip2.Id
    
   $gw2 = New-AzVirtualNetworkGateway -Name $secondaryGWName -ResourceGroupName $secondaryResourceGroupName `
       -Location $secondaryLocation -IpConfigurations $gwipconfig2 -GatewayType Vpn `
       -VpnType RouteBased -GatewaySku VpnGw1 -EnableBgp $true -Asn $secondaryGWAsn
   
   $gw2
   ```

---


### <a name="connect-the-gateways"></a>連接閘道 
使用 Azure 入口網站或 PowerShell，建立兩個閘道之間的連線。 

需要建立兩個連線-從主要閘道到次要閘道的連線，再到從次要閘道到主要閘道的連接。 

針對每個連線，這兩個連線所使用的共用金鑰應相同。 

# <a name="portal"></a>[入口網站](#tab/azure-portal)
使用 Azure 入口網站建立兩個閘道之間的連接。 

1. 從 [ [Azure 入口網站](https://portal.azure.com)中選取 [**建立資源**]。
1. 在`connection`搜尋方塊中輸入，然後按 enter 鍵進行搜尋，這會帶您前往 Microsoft 所發佈的**連線資源。**
1. 選取 [**建立**] 以建立連接。 
1. 在 [**基本**] 索引標籤上，選取下列值，然後選取 **[確定]**。 
    1. 選取`VNet-to-VNet` [] 作為 [連線**類型**]。 
    1. 從下拉式清單中選取訂用帳戶。 
    1. 在下拉式選單中，選取受控實例的資源群組。 
    1. 從下拉式選單選取主要受控實例的位置 
1. 在 [**設定**] 索引標籤上，選取或輸入下列值，然後選取 **[確定]**：
    1. 選擇**第一個虛擬網路閘道**的主要網路閘道，例如`Primary-Gateway`。  
    1. 選擇**第二個虛擬網路閘道**的 [次要網路閘道`Secondary-Gateway`]，例如。 
    1. 選取 [**建立雙向連接**] 旁的核取方塊。 
    1. 請保留預設的主要連線名稱，或將它重新命名為您選擇的值。 
    1. 提供連接的**共用金鑰（PSK）** ，例如`mi1m2psk`。 

   ![建立閘道連線](media/sql-database-managed-instance-failover-group-tutorial/create-gateway-connection.png)

1. 在 [**摘要**] 索引標籤上，檢查雙向連線的設定，然後選取 **[確定]** 以建立您的連線。 

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

使用 PowerShell 建立兩個閘道之間的連線。 

   ```powershell-interactive
   $vpnSharedKey = "mi1mi2psk"
    
   $primaryResourceGroupName = "<Primary-Resource-Group>"
   $primaryGWConnection = "<Primary-connection-name>"
   $primaryLocation = "<Primary-Region>"
    
   $secondaryResourceGroupName = "<Secondary-Resource-Group>"
   $secondaryGWConnection = "<Secondary-connection-name>"
   $secondaryLocation = "<Secondary-Region>"
  
   # Connect the primary to secondary gateway
   Write-host "Connecting the primary gateway"
   New-AzVirtualNetworkGatewayConnection -Name $primaryGWConnection -ResourceGroupName $primaryResourceGroupName `
       -VirtualNetworkGateway1 $gw1 -VirtualNetworkGateway2 $gw2 -Location $primaryLocation `
       -ConnectionType Vnet2Vnet -SharedKey $vpnSharedKey -EnableBgp $true
   $primaryGWConnection
   
   # Connect the secondary to primary gateway
   Write-host "Connecting the secondary gateway"
   
   New-AzVirtualNetworkGatewayConnection -Name $secondaryGWConnection -ResourceGroupName $secondaryResourceGroupName `
       -VirtualNetworkGateway1 $gw2 -VirtualNetworkGateway2 $gw1 -Location $secondaryLocation `
       -ConnectionType Vnet2Vnet -SharedKey $vpnSharedKey -EnableBgp $true
   $secondaryGWConnection
   ```

---

### <a name="create-the-failover-group"></a>建立容錯移轉群組 
使用 Azure 入口網站或 PowerShell，為您的受控實例建立容錯移轉群組。 

# <a name="portal"></a>[入口網站](#tab/azure-portal)

使用 Azure 入口網站，為您的受控實例建立容錯移轉群組。 

1. 在[Azure 入口網站](https://portal.azure.com)的左側功能表中，選取 [ **Azure SQL** ]。 如果 **Azure SQL** 不在清單中，請選取 [所有服務]****，然後在搜尋方塊中輸入 Azure SQL。 (選用) 選取 **Azure SQL** 旁的星號將其設為最愛，並新增為左側導覽中的項目。 
1. 選取您想要新增到容錯移轉群組的主要受控實例。  
1. 在 [**設定**] 下，流覽至 [**實例容錯移轉群組**]，然後選擇 [**新增群組**] 以開啟 [**實例容錯移轉群組**] 頁面。 

   ![新增容錯移轉群組](media/sql-database-managed-instance-failover-group-tutorial/add-failover-group.png)

1. 在 [**實例容錯移轉群組**] 頁面上，輸入容錯移轉群組的名稱，然後從下拉式選單選擇次要受控實例。 選取 [**建立**] 以建立您的容錯移轉群組。 

   ![建立容錯移轉群組](media/sql-database-managed-instance-failover-group-tutorial/create-failover-group.png)

1. 容錯移轉群組部署完成後，您會回到 [**容錯移轉群組**] 頁面。 

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

使用 PowerShell 為您的受控實例建立容錯移轉群組。 

   ```powershell-interactive
   $primaryResourceGroupName = "<Primary-Resource-Group>"
   $failoverGroupName = "<Failover-Group-Name>"
   $primaryLocation = "<Primary-Region>"
   $secondaryLocation = "<Secondary-Region>"
   $primaryManagedInstance = "<Primary-Managed-Instance-Name>"
   $secondaryManagedInstance = "<Secondary-Managed-Instance-Name>"
   
   # Create failover group
   Write-host "Creating the failover group..."
   $failoverGroup = New-AzSqlDatabaseInstanceFailoverGroup -Name $failoverGroupName `
        -Location $primaryLocation -ResourceGroupName $primaryResourceGroupName -PrimaryManagedInstanceName $primaryManagedInstance `
        -PartnerRegion $secondaryLocation -PartnerManagedInstanceName $secondaryManagedInstance `
        -FailoverPolicy Automatic -GracePeriodWithDataLossHours 1
   $failoverGroup
   ```
---

### <a name="test-failover"></a>測試容錯移轉

使用 Azure 入口網站或 PowerShell 測試容錯移轉群組的容錯移轉。 

# <a name="portal"></a>[入口網站](#tab/azure-portal)

使用 Azure 入口網站測試容錯移轉群組的容錯移轉。 

1. 流覽至[Azure 入口網站](https://portal.azure.com)內您的_次要_受控實例，然後選取 [設定] 底下的 [**實例容錯移轉群組**]。 
1. 檢查哪個受控實例是主要複本，以及哪個受控實例是次要複本。 
1. 選取 [**容錯移轉**]，然後在關於 TDS 會話中斷連線的警告上選取 **[是]** 。 

   ![容錯移轉群組](media/sql-database-managed-instance-failover-group-tutorial/failover-mi-failover-group.png)

1. 檢查哪一個受控實例是主要複本，哪個實例是次要複本。 如果容錯移轉成功，這兩個實例應該已切換角色。 

   ![受控實例在容錯移轉後已切換角色](media/sql-database-managed-instance-failover-group-tutorial/mi-switched-after-failover.png)

1. 移至新的_次要_受控實例，然後再次選取 [**容錯移轉**]，將主要實例容錯回復至主要角色。 

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

使用 PowerShell 測試容錯移轉群組的容錯移轉。 

   ```powershell-interactive
   $primaryResourceGroupName = "<Primary-Resource-Group>"
   $secondaryResourceGroupName = "<Secondary-Resource-Group>"
   $failoverGroupName = "<Failover-Group-Name>"
   $primaryLocation = "<Primary-Region>"
   $secondaryLocation = "<Secondary-Region>"
   $primaryManagedInstance = "<Primary-Managed-Instance-Name>"
   $secondaryManagedInstance = "<Secondary-Managed-Instance-Name>"
   
   # Verify the current primary role
   Get-AzSqlDatabaseInstanceFailoverGroup -ResourceGroupName $primaryResourceGroupName `
       -Location $secondaryLocation -Name $failoverGroupName
   
   # Failover the primary managed instance to the secondary role
   Write-host "Failing primary over to the secondary location"
   Get-AzSqlDatabaseInstanceFailoverGroup -ResourceGroupName $secondaryResourceGroupName `
       -Location $secondaryLocation -Name $failoverGroupName | Switch-AzSqlDatabaseInstanceFailoverGroup
   Write-host "Successfully failed failover group to secondary location"
   
   # Verify the current primary role
   Get-AzSqlDatabaseInstanceFailoverGroup -ResourceGroupName $primaryResourceGroupName `
       -Location $secondaryLocation -Name $failoverGroupName
   
   # Fail primary managed instance back to primary role
   Write-host "Failing primary back to primary role"
   Get-AzSqlDatabaseInstanceFailoverGroup -ResourceGroupName $primaryResourceGroupName `
       -Location $primaryLocation -Name $failoverGroupName | Switch-AzSqlDatabaseInstanceFailoverGroup
   Write-host "Successfully failed failover group to primary location"
   
   # Verify the current primary role
   Get-AzSqlDatabaseInstanceFailoverGroup -ResourceGroupName $primaryResourceGroupName `
       -Location $secondaryLocation -Name $failoverGroupName
   ```

---

## <a name="locate-listener-endpoint"></a>尋找接聽程式端點

一旦設定您的容錯移轉群組，請將應用程式的連接字串更新為接聽程式端點。 這會讓您的應用程式連接到容錯移轉群組接聽程式，而不是主資料庫、彈性集區或受控實例。 如此一來，您就不需要在每次 Azure SQL database 實體故障時手動更新連接字串，而會將流量路由傳送至目前主要的任何實體。 

接聽程式端點的形式為`fog-name.database.windows.net`，當您查看容錯移轉群組時，會顯示在 Azure 入口網站中：

![容錯移轉群組連接字串](media/sql-database-configure-failover-group/find-failover-group-connection-string.png)

## <a name="remarks"></a>備註

- 移除單一或集區資料庫的容錯移轉群組並不會停止複寫，而且不會刪除複寫的資料庫。 如果您想要在移除容錯移轉群組後，將單一或集區資料庫重新加入至該群組，您必須手動停止異地複寫，並從次要伺服器刪除該資料庫。 `The operation cannot be performed due to multiple errors`當嘗試將資料庫新增到容錯移轉群組時，無法執行任何動作可能會導致類似的錯誤。 


## <a name="next-steps"></a>後續步驟

如需設定容錯移轉群組的詳細步驟，請參閱下列教學課程：
- [將單一資料庫新增至容錯移轉群組](sql-database-single-database-failover-group-tutorial.md)
- [將彈性集區新增到容錯移轉群組](sql-database-elastic-pool-failover-group-tutorial.md)
- [將受控實例新增至容錯移轉群組](sql-database-managed-instance-failover-group-tutorial.md)
 
如需 Azure SQL Database 高可用性選項的總覽，請參閱[異地](sql-database-active-geo-replication.md)複寫和[自動容錯移轉群組](sql-database-auto-failover-group.md)。 
