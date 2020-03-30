---
title: 設定容錯移轉群組
description: 瞭解如何使用 Azure 門戶、Az CLI 和 PowerShell 為 Azure SQL 資料庫單個資料庫、彈性池和託管實例配置自動容錯移轉組。
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "77461796"
---
# <a name="configure-a-failover-group-for-azure-sql-database"></a>為 Azure SQL 資料庫配置容錯移轉組

本主題介紹如何使用 Azure 門戶或 PowerShell 為 Azure SQL 資料庫單個資料庫、彈性池和託管實例配置[自動容錯移轉組](sql-database-auto-failover-group.md)。 

## <a name="single-database"></a>單一資料庫
創建容錯移轉組，並使用 Azure 門戶或 PowerShell 向其添加單個資料庫。

### <a name="prerequisites"></a>Prerequisites

請考慮以下先決條件：

- 次要伺服器的伺服器登錄和防火牆設置必須與主伺服器的設置匹配。 

### <a name="create-failover-group"></a>建立容錯移轉群組

# <a name="portal"></a>[入口網站](#tab/azure-portal)
創建容錯移轉組並使用 Azure 門戶將單個資料庫添加到該組。


1. 在[Azure 門戶](https://portal.azure.com)的左側功能表中選擇**Azure SQL。** 如果 **Azure SQL** 不在清單中，請選取 [所有服務]****，然後在搜尋方塊中輸入 Azure SQL。 (選用) 選取 **Azure SQL** 旁的星號將其設為最愛，並新增為左側導覽中的項目。 
1. 選擇要添加到容錯移轉組的單個資料庫。 
1. 在 **"伺服器"名稱**下選擇伺服器的名稱以打開伺服器的設置。

   ![打開單個 db 的伺服器](media/sql-database-single-database-failover-group-tutorial/open-sql-db-server.png)

1. 在 **"設置"** 窗格下選擇**容錯移轉組**，然後選擇 **"添加組**"以創建新的容錯移轉組。 

    ![添加新容錯移轉組](media/sql-database-single-database-failover-group-tutorial/sqldb-add-new-failover-group.png)

1. 在**容錯移轉組**頁上，輸入或選擇所需值，然後選擇"**創建**"。

   - **組中的資料庫**：選擇要添加到容錯移轉組的資料庫。 將資料庫添加到容錯移轉組將自動啟動異地複製過程。 
        
    ![將 SQL DB 添加到容錯移轉組](media/sql-database-single-database-failover-group-tutorial/add-sqldb-to-failover-group.png)

# <a name="powershell"></a>[電源外殼](#tab/azure-powershell)
創建容錯移轉組並使用 PowerShell 將單個資料庫添加到該組。 

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

使用 Azure 門戶或 PowerShell 測試容錯移轉組容錯移轉。 

# <a name="portal"></a>[入口網站](#tab/azure-portal)

使用 Azure 門戶測試容錯移轉組容錯移轉。 

1. 在[Azure 門戶](https://portal.azure.com)的左側功能表中選擇**Azure SQL。** 如果 **Azure SQL** 不在清單中，請選取 [所有服務]****，然後在搜尋方塊中輸入 Azure SQL。 (選用) 選取 **Azure SQL** 旁的星號將其設為最愛，並新增為左側導覽中的項目。 
1. 選擇要添加到容錯移轉組的單個資料庫。 

   ![打開單個 db 的伺服器](media/sql-database-single-database-failover-group-tutorial/open-sql-db-server.png)

1. 在 **"設置"** 窗格下選擇**容錯移轉組**，然後選擇剛剛創建的容錯移轉組。 
  
   ![從門戶中選擇容錯移轉組](media/sql-database-single-database-failover-group-tutorial/select-failover-group.png)

1. 查看主伺服器和次要伺服器。 
1. 從工作窗格中選擇**容錯移轉**以容錯移轉包含單個資料庫的容錯移轉組。 
1. 在警告上選擇 **"是**"，通知您 TDS 會話將斷開連接。 

   ![容錯移轉組容錯移轉，其中包含 SQL 資料庫](media/sql-database-single-database-failover-group-tutorial/failover-sql-db.png)

1. 查看當前是主伺服器和次要伺服器。 如果容錯移轉成功，則兩台伺服器應交換角色。 
1. 再次選擇**容錯移轉**，使伺服器故障回其最初的角色。 

# <a name="powershell"></a>[電源外殼](#tab/azure-powershell)

使用 PowerShell 測試容錯移轉組容錯移轉。  

檢查輔助副本的角色： 

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
容錯移轉到次要伺服器： 

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
> 如果需要刪除次要資料庫，請將其從容錯移轉組中刪除，然後再將其刪除。 在從容錯移轉組中刪除次要資料庫之前刪除該資料庫可能會導致不可預知的行為。 

## <a name="elastic-pool"></a>彈性集區
創建容錯移轉組並使用 Azure 門戶或 PowerShell 向其添加彈性池。  

### <a name="prerequisites"></a>Prerequisites

請考慮以下先決條件：

- 次要伺服器的伺服器登錄和防火牆設置必須與主伺服器的設置匹配。 

### <a name="create-the-failover-group"></a>創建容錯移轉組 

使用 Azure 門戶或 PowerShell 為彈性池創建容錯移轉組。 

# <a name="portal"></a>[入口網站](#tab/azure-portal)
創建容錯移轉組並使用 Azure 門戶向其添加彈性池。

1. 在[Azure 門戶](https://portal.azure.com)的左側功能表中選擇**Azure SQL。** 如果 **Azure SQL** 不在清單中，請選取 [所有服務]****，然後在搜尋方塊中輸入 Azure SQL。 (選用) 選取 **Azure SQL** 旁的星號將其設為最愛，並新增為左側導覽中的項目。 
1. 選擇要添加到容錯移轉組的彈性池。 
1. 在 **"概述"** 窗格中，選擇**伺服器名稱**下的伺服器名稱以打開伺服器的設置。
  
    ![用於彈性池的打開伺服器](media/sql-database-elastic-pool-failover-group-tutorial/server-for-elastic-pool.png)

1. 在 **"設置"** 窗格下選擇**容錯移轉組**，然後選擇 **"添加組**"以創建新的容錯移轉組。 

    ![添加新容錯移轉組](media/sql-database-single-database-failover-group-tutorial/sqldb-add-new-failover-group.png)

1. 在**容錯移轉組**頁上，輸入或選擇所需值，然後選擇"**創建**"。 創建新次要伺服器，或選擇現有次要伺服器。 

1. 選擇**組中的資料庫，** 然後選擇要添加到容錯移轉組的彈性池。 如果次要伺服器上不存在彈性池，則會出現一條警告，提示您在次要伺服器上創建彈性池。 選擇警告，然後選擇 **"確定"** 以在次要伺服器上創建彈性池。 
        
    ![將彈性池添加到容錯移轉組](media/sql-database-elastic-pool-failover-group-tutorial/add-elastic-pool-to-failover-group.png)
        
1. 選擇 **"選擇"** 可將彈性池設置應用於容錯移轉組，然後選擇 **"創建"** 以創建容錯移轉組。 將彈性池添加到容錯移轉組將自動啟動異地複製過程。 

# <a name="powershell"></a>[電源外殼](#tab/azure-powershell)

創建容錯移轉組並使用 PowerShell 向其添加彈性池。 

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

使用 Azure 門戶或 PowerShell 測試彈性池的容錯移轉。 

# <a name="portal"></a>[入口網站](#tab/azure-portal)

容錯移轉組容錯移轉組容錯移轉到次要伺服器，然後使用 Azure 門戶故障返回。 

1. 在[Azure 門戶](https://portal.azure.com)的左側功能表中選擇**Azure SQL。** 如果 **Azure SQL** 不在清單中，請選取 [所有服務]****，然後在搜尋方塊中輸入 Azure SQL。 (選用) 選取 **Azure SQL** 旁的星號將其設為最愛，並新增為左側導覽中的項目。 
1. 選擇要添加到容錯移轉組的彈性池。 
1. 在 **"概述"** 窗格中，選擇**伺服器名稱**下的伺服器名稱以打開伺服器的設置。
  
    ![用於彈性池的打開伺服器](media/sql-database-elastic-pool-failover-group-tutorial/server-for-elastic-pool.png)
1. 在 **"設置"** 窗格下選擇**容錯移轉組**，然後選擇您在第 2 節中創建的容錯移轉組。 
  
   ![從門戶中選擇容錯移轉組](media/sql-database-single-database-failover-group-tutorial/select-failover-group.png)

1. 查看主伺服器和次要伺服器。 
1. 從工作窗格中選擇**容錯移轉**以容錯移轉包含彈性池的容錯移轉組。 
1. 在警告上選擇 **"是**"，通知您 TDS 會話將斷開連接。 

   ![容錯移轉組容錯移轉，其中包含 SQL 資料庫](media/sql-database-single-database-failover-group-tutorial/failover-sql-db.png)

1. 查看主伺服器，哪個伺服器是次要伺服器。 如果容錯移轉成功，則兩台伺服器應交換角色。 
1. 再次選擇**容錯移轉**以容錯移轉組故障故障組故障回原始設置。 

# <a name="powershell"></a>[電源外殼](#tab/azure-powershell)

使用 PowerShell 測試容錯移轉組容錯移轉。

檢查輔助副本的角色： 

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

容錯移轉到次要伺服器： 

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
> 如果需要刪除次要資料庫，請將其從容錯移轉組中刪除，然後再將其刪除。 在從容錯移轉組中刪除次要資料庫之前刪除該資料庫可能會導致不可預知的行為。 

## <a name="managed-instance"></a>受控執行個體

使用 Azure 門戶或 PowerShell 在兩個託管實例之間創建容錯移轉組。 

您需要配置[ExpressRoute](../expressroute/expressroute-howto-circuit-portal-resource-manager.md)或為每個託管實例的虛擬網路創建閘道，連接兩個閘道，然後創建容錯移轉組。 

### <a name="prerequisites"></a>Prerequisites
請考慮以下先決條件：

- 輔助託管實例必須為空。
- 輔助虛擬網路的子網範圍不得與主虛擬網路的子網範圍重疊。 
- 輔助實例的排序規則和時區必須與主實例的排序規則和時區匹配。 
- 連接兩個閘道時，共用**金鑰**對於兩個連接應相同。 

### <a name="create-primary-virtual-network-gateway"></a>創建主虛擬網路閘道 

如果尚未配置[ExpressRoute，](../expressroute/expressroute-howto-circuit-portal-resource-manager.md)可以使用 Azure 門戶或 PowerShell 創建主虛擬網路閘道。 

# <a name="portal"></a>[入口網站](#tab/azure-portal)

使用 Azure 門戶創建主虛擬網路閘道。 

1. 在[Azure 門戶](https://portal.azure.com)中，轉到資源組並選擇主託管實例的**虛擬網路**資源。 
1. 在 **"設置"** 下選擇**子網**，然後選擇添加新**的閘道子網**。 保留預設值。 

   ![添加主託管實例的閘道](media/sql-database-managed-instance-failover-group-tutorial/add-subnet-gateway-primary-vnet.png)

1. 創建子網閘道後，選擇從左側功能窗格**創建資源**，然後在搜索框中鍵入`Virtual network gateway`。 選擇**微軟**發佈的**虛擬網路閘道**資源。 

   ![創建新的虛擬網路閘道](media/sql-database-managed-instance-failover-group-tutorial/create-virtual-network-gateway.png)

1. 填寫所需的欄位來配置主託管實例的閘道。 

   下表顯示了主託管實例的閘道所需的值：
 
    | **領域** | 值 |
    | --- | --- |
    | **訂閱** |  主託管實例所在的訂閱。 |
    | **名稱** | 虛擬網路閘道的名稱。 | 
    | **地區** | 輔助託管實例所在的區域。 |
    | **閘道類型** | 選擇**VPN**。 |
    | **VPN 類型** | 選擇**基於路由** |
    | **Sku**| 將 預設值`VpnGw1`保留 。 |
    | **位置**| 輔助託管實例和輔助虛擬網路的位置。   |
    | **虛擬網路**| 為輔助託管實例選擇虛擬網路。 |
    | **公共 IP 位址**| 選取 [建立新的]****。 |
    | **公共 IP 位址名稱**| 輸入 IP 位址的名稱。 |
    | &nbsp; | &nbsp; |

1. 將其他值保留為預設值，然後選擇 **"查看 + 創建**"以查看虛擬網路閘道的設置。

   ![主要閘道設置](media/sql-database-managed-instance-failover-group-tutorial/settings-for-primary-gateway.png)

1. 選擇 **"創建**"以創建新的虛擬網路閘道。 

# <a name="powershell"></a>[電源外殼](#tab/azure-powershell)

使用 PowerShell 創建主虛擬網路閘道。 

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

### <a name="create-secondary-virtual-network-gateway"></a>創建輔助虛擬網路閘道

使用 Azure 門戶或 PowerShell 創建輔助虛擬網路閘道。 

# <a name="portal"></a>[入口網站](#tab/azure-portal)
重複上一節中的步驟，為輔助託管實例創建虛擬網路子網和閘道。 填寫所需的欄位來配置輔助託管實例的閘道。 

   下表顯示了輔助託管實例的閘道所需的值：

   | **領域** | 值 |
   | --- | --- |
   | **訂閱** |  輔助託管實例所在的訂閱。 |
   | **名稱** | 虛擬網路閘道的名稱，如`secondary-mi-gateway`。 | 
   | **地區** | 輔助託管實例所在的區域。 |
   | **閘道類型** | 選擇**VPN**。 |
   | **VPN 類型** | 選擇**基於路由** |
   | **Sku**| 將 預設值`VpnGw1`保留 。 |
   | **位置**| 輔助託管實例和輔助虛擬網路的位置。   |
   | **虛擬網路**| 選擇在第 2 節中創建的虛擬網路，如`vnet-sql-mi-secondary`。 |
   | **公共 IP 位址**| 選取 [建立新的]****。 |
   | **公共 IP 位址名稱**| 輸入 IP 位址的名稱，如`secondary-gateway-IP`。 |
   | &nbsp; | &nbsp; |

   ![輔助閘道設置](media/sql-database-managed-instance-failover-group-tutorial/settings-for-secondary-gateway.png)

# <a name="powershell"></a>[電源外殼](#tab/azure-powershell)

使用 PowerShell 創建輔助虛擬網路閘道。 

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
使用 Azure 門戶或 PowerShell 在兩個閘道之間創建連接。 

需要創建兩個連接 - 從主閘道到輔助閘道的連接，然後從輔助閘道連接到主閘道。 

用於兩個連接的共用金鑰組于每個連接應相同。 

# <a name="portal"></a>[入口網站](#tab/azure-portal)
使用 Azure 門戶在兩個閘道之間創建連接。 

1. 選擇從[Azure 門戶](https://portal.azure.com)**創建資源**。
1. 鍵入`connection`搜索框，然後按 Enter 進行搜索，這將帶您訪問 Microsoft 發佈的**連接**資源。
1. 選擇 **"創建**"以創建連接。 
1. 在 **"基本"** 選項卡上，選擇以下值，然後選擇 **"確定**"。 
    1. 為`VNet-to-VNet`**連線類型**選擇 。 
    1. 從下拉式清單中選取訂用帳戶。 
    1. 在下拉清單中選擇託管實例的資源組。 
    1. 從下拉清單中選擇主託管實例的位置 
1. 在 **"設置"** 選項卡上，選擇或輸入以下值，然後選擇 **"確定**" ：
    1. 選擇**第一個虛擬網路閘道的主網路閘道**，如`Primary-Gateway`。  
    1. 選擇**第二個虛擬網路閘道的輔助網路閘道**，例如`Secondary-Gateway`。 
    1. 選擇 **"建立雙向連接"** 旁邊的核取方塊。 
    1. 保留預設主連接名稱，或將其重命名為您選擇的值。 
    1. 為連接提供**共用金鑰 （PSK），** 例如`mi1m2psk`。 

   ![創建閘道連接](media/sql-database-managed-instance-failover-group-tutorial/create-gateway-connection.png)

1. 在 **"摘要"** 選項卡上，查看雙向連接的設置，然後選擇 **"確定"** 以創建連接。 

# <a name="powershell"></a>[電源外殼](#tab/azure-powershell)

使用 PowerShell 在兩個閘道之間創建連接。 

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

### <a name="create-the-failover-group"></a>創建容錯移轉組 
使用 Azure 門戶或 PowerShell 為託管實例創建容錯移轉組。 

# <a name="portal"></a>[入口網站](#tab/azure-portal)

使用 Azure 門戶為託管實例創建容錯移轉組。 

1. 在[Azure 門戶](https://portal.azure.com)的左側功能表中選擇**Azure SQL。** 如果 **Azure SQL** 不在清單中，請選取 [所有服務]****，然後在搜尋方塊中輸入 Azure SQL。 (選用) 選取 **Azure SQL** 旁的星號將其設為最愛，並新增為左側導覽中的項目。 
1. 選擇要添加到容錯移轉組的主要託管實例。  
1. 在 **"設置"** 下，導航到**實例容錯移轉組**，然後選擇**添加組**以打開**實例容錯移轉組**頁面。 

   ![添加容錯移轉組](media/sql-database-managed-instance-failover-group-tutorial/add-failover-group.png)

1. 在 **"實例容錯移轉組"** 頁上，鍵入容錯移轉組的名稱，然後從下拉清單中選擇輔助託管實例。 選擇 **"創建**"以創建容錯移轉組。 

   ![建立容錯移轉群組](media/sql-database-managed-instance-failover-group-tutorial/create-failover-group.png)

1. 容錯移轉組部署完成後，您將被帶回**容錯移轉組**頁面。 

# <a name="powershell"></a>[電源外殼](#tab/azure-powershell)

使用 PowerShell 為託管實例創建容錯移轉組。 

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

使用 Azure 門戶或 PowerShell 測試容錯移轉組容錯移轉。 

# <a name="portal"></a>[入口網站](#tab/azure-portal)

使用 Azure 門戶測試容錯移轉組容錯移轉。 

1. 導航到[Azure 門戶](https://portal.azure.com)中的_輔助_託管實例，並在設置下選擇**實例容錯移轉組**。 
1. 查看哪個託管實例是主實例，哪個託管實例是輔助實例。 
1. 選擇 **"容錯移轉**"，然後在有關 TDS 會話斷開連接的警告上選擇 **"是**"。 

   ![容錯移轉組容錯移轉](media/sql-database-managed-instance-failover-group-tutorial/failover-mi-failover-group.png)

1. 查看哪個 manged 實例是主實例，哪個實例是輔助實例。 如果容錯移轉成功，則兩個實例應切換角色。 

   ![託管實例在容錯移轉後切換角色](media/sql-database-managed-instance-failover-group-tutorial/mi-switched-after-failover.png)

1. 轉到新的_輔助_託管實例，並再次選擇**容錯移轉**，使主實例故障回主角色。 

# <a name="powershell"></a>[電源外殼](#tab/azure-powershell)

使用 PowerShell 測試容錯移轉組容錯移轉。 

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

## <a name="locate-listener-endpoint"></a>查找攔截器終結點

配置容錯移轉組後，將應用程式的連接字串更新到攔截器終結點。 這將使應用程式保持連接到容錯移轉組攔截器，而不是主資料庫、彈性池或託管實例。 這樣，您不必在每次 Azure SQL 資料庫實體失敗時手動更新連接字串，並且流量將路由到當前主實體。 

攔截器終結點以 的形式`fog-name.database.windows.net`出現，在 Azure 門戶中查看容錯移轉組時可見：

![容錯移轉組連接字串](media/sql-database-configure-failover-group/find-failover-group-connection-string.png)

## <a name="remarks"></a>備註

- 刪除單個或池資料庫的容錯移轉組不會停止複製，也不會刪除複製的資料庫。 如果要在刪除容錯移轉組後將單個或池資料庫添加回容錯移轉組，則需要手動停止異地複製並從次要伺服器中刪除資料庫。 如果執行任一操作，都可能導致類似于嘗試將資料庫`The operation cannot be performed due to multiple errors`添加到容錯移轉組時的錯誤。 


## <a name="next-steps"></a>後續步驟

有關配置容錯移轉組的詳細步驟，請參閱以下教程：
- [將單個資料庫添加到容錯移轉組](sql-database-single-database-failover-group-tutorial.md)
- [將彈性集區新增到容錯移轉群組](sql-database-elastic-pool-failover-group-tutorial.md)
- [將託管實例添加到容錯移轉組](sql-database-managed-instance-failover-group-tutorial.md)
 
有關 Azure SQL 資料庫高可用性選項的概述，請參閱[異地複製](sql-database-active-geo-replication.md)和[自動容錯移轉組](sql-database-auto-failover-group.md)。 
