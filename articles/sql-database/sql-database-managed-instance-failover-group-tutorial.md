---
title: 教程：將託管實例添加到容錯移轉組
description: 瞭解如何為 Azure SQL 資料庫託管實例配置容錯移轉組。
services: sql-database
ms.service: sql-database
ms.subservice: high-availability
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: MashaMSFT
ms.author: mathoma
ms.reviewer: sashan, carlrab
manager: jroth
ms.date: 08/27/2019
ms.openlocfilehash: bf83155e971061f22e5f5fc33d216b58621c9249
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "77462644"
---
# <a name="tutorial-add-a-sql-database-managed-instance-to-a-failover-group"></a>教程：將 SQL 資料庫託管實例添加到容錯移轉組

將 SQL Database 受控執行個體新增至容錯移轉群組。 在本文中，您將了解如何：

> [!div class="checklist"]
> - 創建主託管實例
> - 創建輔助託管實例作為[容錯移轉組的](sql-database-auto-failover-group.md)一部分。 
> - 測試容錯移轉

  > [!NOTE]
  > - 在學習本教程時，請確保配置資源時，具有[為託管實例設置容錯移轉組的先決條件](sql-database-auto-failover-group.md#enabling-geo-replication-between-managed-instances-and-their-vnets)。 
  > - 創建託管實例可能需要大量時間。 因此，本教程可能需要幾個小時才能完成。 有關預配時間的詳細資訊，請參閱[託管實例管理操作](sql-database-managed-instance.md#managed-instance-management-operations)。 
  > - 參與容錯移轉組的託管實例需要[ExpressRoute](../expressroute/expressroute-howto-circuit-portal-resource-manager.md)或兩個連接的 VPN 閘道。 本教程提供了創建和連接 VPN 閘道的步驟。 如果您已經配置了 ExpressRoute，請跳過這些步驟。 


## <a name="prerequisites"></a>Prerequisites

# <a name="portal"></a>[入口網站](#tab/azure-portal)
若要完成本教學課程，請確定您具有下列項目︰ 

- Azure 訂用帳戶。 如果您還沒有[一個免費帳戶，請創建一個免費帳戶](https://azure.microsoft.com/free/)。


# <a name="powershell"></a>[電源外殼](#tab/azure-powershell)
要完成本教程，請確保您有以下專案：

- Azure 訂用帳戶。 如果您還沒有[一個免費帳戶，請創建一個免費帳戶](https://azure.microsoft.com/free/)。
- [Azure 電源外殼](/powershell/azureps-cmdlets-docs)

---


## <a name="1---create-resource-group-and-primary-managed-instance"></a>1 - 創建資源組和主託管實例
在此步驟中，您將使用 Azure 門戶或 PowerShell 為容錯移轉組創建資源組和主託管實例。 


# <a name="portal"></a>[入口網站](#tab/azure-portal) 

使用 Azure 門戶創建資源組和主要託管實例。 

1. 在 Azure 入口網站的左側功能表中，選取 [Azure SQL]****。 如果 **Azure SQL** 不在清單中，請選取 [所有服務]****，然後在搜尋方塊中輸入 Azure SQL。 (選用) 選取 **Azure SQL** 旁的星號將其設為最愛，並新增為左側導覽中的項目。 
1. 選取 [+ 新增]**** 以開啟 [選取 SQL 部署選項]**** 頁面。 您可以選取 [資料庫] 圖格上的 [顯示詳細資料]，以查看不同資料庫的其他資訊。
1. 在 SQL**託管實例**磁貼上選擇 **"創建**"。 

    ![選擇託管實例](media/sql-database-managed-instance-failover-group-tutorial/select-managed-instance.png)

1. 在 **"創建 Azure SQL 資料庫託管實例**"頁上，在 **"基礎知識"** 選項卡上
    1. 在 **"專案詳細資訊**"下，從下拉清單中選擇**訂閱**，然後選擇 **"創建新**資源組"。 鍵入資源組的名稱，如`myResourceGroup`。 
    1. 在 **"託管實例詳細資訊**"下，提供託管實例的名稱以及要部署託管實例的區域。 將**計算 + 存儲**保留為預設值。 
    1. 在**管理員帳戶**下 ，提供管理員登錄名，`azureuser`如 ， 和複雜的管理員密碼。 

    ![創建主 MI](media/sql-database-managed-instance-failover-group-tutorial/primary-sql-mi-values.png)

1. 將其餘設置保留為預設值，然後選擇 **"查看 + 創建**"以查看託管實例設置。 
1. 選擇 **"創建**"以創建主託管實例。 

# <a name="powershell"></a>[電源外殼](#tab/azure-powershell)

使用 PowerShell 創建資源組和主託管實例。 

   ```powershell-interactive
   # Connect-AzAccount
   # The SubscriptionId in which to create these objects
   $SubscriptionId = '<Subscription-ID>'
   # Create a random identifier to use as subscript for the different resource names
   $randomIdentifier = $(Get-Random)
   # Set the resource group name and location for your managed instance
   $resourceGroupName = "myResourceGroup-$randomIdentifier"
   $location = "eastus"
   $drLocation = "eastus2"
   
   # Set the networking values for your primary managed instance
   $primaryVNet = "primaryVNet-$randomIdentifier"
   $primaryAddressPrefix = "10.0.0.0/16"
   $primaryDefaultSubnet = "primaryDefaultSubnet-$randomIdentifier"
   $primaryDefaultSubnetAddress = "10.0.0.0/24"
   $primaryMiSubnetName = "primaryMISubnet-$randomIdentifier"
   $primaryMiSubnetAddress = "10.0.0.0/24"
   $primaryMiGwSubnetAddress = "10.0.255.0/27"
   $primaryGWName = "primaryGateway-$randomIdentifier"
   $primaryGWPublicIPAddress = $primaryGWName + "-ip"
   $primaryGWIPConfig = $primaryGWName + "-ipc"
   $primaryGWAsn = 61000
   $primaryGWConnection = $primaryGWName + "-connection"
   
   
   # Set the networking values for your secondary managed instance
   $secondaryVNet = "secondaryVNet-$randomIdentifier"
   $secondaryAddressPrefix = "10.128.0.0/16"
   $secondaryDefaultSubnet = "secondaryDefaultSubnet-$randomIdentifier"
   $secondaryDefaultSubnetAddress = "10.128.0.0/24"
   $secondaryMiSubnetName = "secondaryMISubnet-$randomIdentifier"
   $secondaryMiSubnetAddress = "10.128.0.0/24"
   $secondaryMiGwSubnetAddress = "10.128.255.0/27"
   $secondaryGWName = "secondaryGateway-$randomIdentifier"
   $secondaryGWPublicIPAddress = $secondaryGWName + "-IP"
   $secondaryGWIPConfig = $secondaryGWName + "-ipc"
   $secondaryGWAsn = 62000
   $secondaryGWConnection = $secondaryGWName + "-connection"
   
   
   
   # Set the managed instance name for the new managed instances
   $primaryInstance = "primary-mi-$randomIdentifier"
   $secondaryInstance = "secondary-mi-$randomIdentifier"
   
   # Set the admin login and password for your managed instance
   $secpasswd = "PWD27!"+(New-Guid).Guid | ConvertTo-SecureString -AsPlainText -Force
   $mycreds = New-Object System.Management.Automation.PSCredential ("azureuser", $secpasswd)
   
   
   # Set the managed instance service tier, compute level, and license mode
   $edition = "General Purpose"
   $vCores = 8
   $maxStorage = 256
   $computeGeneration = "Gen5"
   $license = "LicenseIncluded" #"BasePrice" or LicenseIncluded if you have don't have SQL Server licence that can be used for AHB discount
   
   # Set failover group details
   $vpnSharedKey = "mi1mi2psk"
   $failoverGroupName = "failovergroup-$randomIdentifier"
   
   # Show randomized variables
   Write-host "Resource group name is" $resourceGroupName
   Write-host "Password is" $secpasswd
   Write-host "Primary Virtual Network name is" $primaryVNet
   Write-host "Primary default subnet name is" $primaryDefaultSubnet
   Write-host "Primary managed instance subnet name is" $primaryMiSubnetName
   Write-host "Secondary Virtual Network name is" $secondaryVNet
   Write-host "Secondary default subnet name is" $secondaryDefaultSubnet
   Write-host "Secondary managed instance subnet name is" $secondaryMiSubnetName
   Write-host "Primary managed instance name is" $primaryInstance
   Write-host "Secondary managed instance name is" $secondaryInstance
   Write-host "Failover group name is" $failoverGroupName
   
   # Suppress networking breaking changes warning (https://aka.ms/azps-changewarnings
   Set-Item Env:\SuppressAzurePowerShellBreakingChangeWarnings "true"
   
   # Set subscription context
   Set-AzContext -SubscriptionId $subscriptionId 
   
   # Create a resource group
   Write-host "Creating resource group..."
   $resourceGroup = New-AzResourceGroup -Name $resourceGroupName -Location $location -Tag @{Owner="SQLDB-Samples"}
   $resourceGroup
   
   # Configure primary virtual network
   Write-host "Creating primary virtual network..."
   $primaryVirtualNetwork = New-AzVirtualNetwork `
                         -ResourceGroupName $resourceGroupName `
                         -Location $location `
                         -Name $primaryVNet `
                         -AddressPrefix $primaryAddressPrefix
   
                     Add-AzVirtualNetworkSubnetConfig `
                         -Name $primaryMiSubnetName `
                         -VirtualNetwork $primaryVirtualNetwork `
                         -AddressPrefix $PrimaryMiSubnetAddress `
                     | Set-AzVirtualNetwork
   $primaryVirtualNetwork
   
   
   # Configure primary MI subnet
   Write-host "Configuring primary MI subnet..."
   $primaryVirtualNetwork = Get-AzVirtualNetwork -Name $primaryVNet -ResourceGroupName $resourceGroupName
   
   
   $primaryMiSubnetConfig = Get-AzVirtualNetworkSubnetConfig `
                           -Name $primaryMiSubnetName `
                           -VirtualNetwork $primaryVirtualNetwork
   $primaryMiSubnetConfig
   
   # Configure network security group management service
   Write-host "Configuring primary MI subnet..."
   
   $primaryMiSubnetConfigId = $primaryMiSubnetConfig.Id
   
   $primaryNSGMiManagementService = New-AzNetworkSecurityGroup `
                         -Name 'primaryNSGMiManagementService' `
                         -ResourceGroupName $resourceGroupName `
                         -location $location
   $primaryNSGMiManagementService
   
   # Configure route table management service
   Write-host "Configuring primary MI route table management service..."
   
   $primaryRouteTableMiManagementService = New-AzRouteTable `
                         -Name 'primaryRouteTableMiManagementService' `
                         -ResourceGroupName $resourceGroupName `
                         -location $location
   $primaryRouteTableMiManagementService
   
   # Configure the primary network security group
   Write-host "Configuring primary network security group..."
   Set-AzVirtualNetworkSubnetConfig `
                         -VirtualNetwork $primaryVirtualNetwork `
                         -Name $primaryMiSubnetName `
                         -AddressPrefix $PrimaryMiSubnetAddress `
                         -NetworkSecurityGroup $primaryNSGMiManagementService `
                         -RouteTable $primaryRouteTableMiManagementService | `
                       Set-AzVirtualNetwork
   
   Get-AzNetworkSecurityGroup `
                         -ResourceGroupName $resourceGroupName `
                         -Name "primaryNSGMiManagementService" `
                       | Add-AzNetworkSecurityRuleConfig `
                         -Priority 100 `
                         -Name "allow_management_inbound" `
                         -Access Allow `
                         -Protocol Tcp `
                         -Direction Inbound `
                         -SourcePortRange * `
                         -SourceAddressPrefix * `
                         -DestinationPortRange 9000,9003,1438,1440,1452 `
                         -DestinationAddressPrefix * `
                       | Add-AzNetworkSecurityRuleConfig `
                         -Priority 200 `
                         -Name "allow_misubnet_inbound" `
                         -Access Allow `
                         -Protocol * `
                         -Direction Inbound `
                         -SourcePortRange * `
                         -SourceAddressPrefix $PrimaryMiSubnetAddress `
                         -DestinationPortRange * `
                         -DestinationAddressPrefix * `
                       | Add-AzNetworkSecurityRuleConfig `
                         -Priority 300 `
                         -Name "allow_health_probe_inbound" `
                         -Access Allow `
                         -Protocol * `
                         -Direction Inbound `
                         -SourcePortRange * `
                         -SourceAddressPrefix AzureLoadBalancer `
                         -DestinationPortRange * `
                         -DestinationAddressPrefix * `
                       | Add-AzNetworkSecurityRuleConfig `
                         -Priority 1000 `
                         -Name "allow_tds_inbound" `
                         -Access Allow `
                         -Protocol Tcp `
                         -Direction Inbound `
                         -SourcePortRange * `
                         -SourceAddressPrefix VirtualNetwork `
                         -DestinationPortRange 1433 `
                         -DestinationAddressPrefix * `
                       | Add-AzNetworkSecurityRuleConfig `
                         -Priority 1100 `
                         -Name "allow_redirect_inbound" `
                         -Access Allow `
                         -Protocol Tcp `
                         -Direction Inbound `
                         -SourcePortRange * `
                         -SourceAddressPrefix VirtualNetwork `
                         -DestinationPortRange 11000-11999 `
                         -DestinationAddressPrefix * `
                       | Add-AzNetworkSecurityRuleConfig `
                         -Priority 1200 `
                         -Name "allow_geodr_inbound" `
                         -Access Allow `
                         -Protocol Tcp `
                         -Direction Inbound `
                         -SourcePortRange * `
                         -SourceAddressPrefix VirtualNetwork `
                         -DestinationPortRange 5022 `
                         -DestinationAddressPrefix * `
                       | Add-AzNetworkSecurityRuleConfig `
                         -Priority 4096 `
                         -Name "deny_all_inbound" `
                         -Access Deny `
                         -Protocol * `
                         -Direction Inbound `
                         -SourcePortRange * `
                         -SourceAddressPrefix * `
                         -DestinationPortRange * `
                         -DestinationAddressPrefix * `
                       | Add-AzNetworkSecurityRuleConfig `
                         -Priority 100 `
                         -Name "allow_management_outbound" `
                         -Access Allow `
                         -Protocol Tcp `
                         -Direction Outbound `
                         -SourcePortRange * `
                         -SourceAddressPrefix * `
                         -DestinationPortRange 80,443,12000 `
                         -DestinationAddressPrefix * `
                       | Add-AzNetworkSecurityRuleConfig `
                         -Priority 200 `
                         -Name "allow_misubnet_outbound" `
                         -Access Allow `
                         -Protocol * `
                         -Direction Outbound `
                         -SourcePortRange * `
                         -SourceAddressPrefix * `
                         -DestinationPortRange * `
                         -DestinationAddressPrefix $PrimaryMiSubnetAddress `
                       | Add-AzNetworkSecurityRuleConfig `
                         -Priority 1100 `
                         -Name "allow_redirect_outbound" `
                         -Access Allow `
                         -Protocol Tcp `
                         -Direction Outbound `
                         -SourcePortRange * `
                         -SourceAddressPrefix VirtualNetwork `
                         -DestinationPortRange 11000-11999 `
                         -DestinationAddressPrefix * `
                       | Add-AzNetworkSecurityRuleConfig `
                         -Priority 1200 `
                         -Name "allow_geodr_outbound" `
                         -Access Allow `
                         -Protocol Tcp `
                         -Direction Outbound `
                         -SourcePortRange * `
                         -SourceAddressPrefix VirtualNetwork `
                         -DestinationPortRange 5022 `
                         -DestinationAddressPrefix * `
                       | Add-AzNetworkSecurityRuleConfig `
                         -Priority 4096 `
                         -Name "deny_all_outbound" `
                         -Access Deny `
                         -Protocol * `
                         -Direction Outbound `
                         -SourcePortRange * `
                         -SourceAddressPrefix * `
                         -DestinationPortRange * `
                         -DestinationAddressPrefix * `
                       | Set-AzNetworkSecurityGroup
   Write-host "Primary network security group configured successfully."
   
   
   Get-AzRouteTable `
                         -ResourceGroupName $resourceGroupName `
                         -Name "primaryRouteTableMiManagementService" `
                       | Add-AzRouteConfig `
                         -Name "primaryToMIManagementService" `
                         -AddressPrefix 0.0.0.0/0 `
                         -NextHopType Internet `
                       | Add-AzRouteConfig `
                         -Name "ToLocalClusterNode" `
                         -AddressPrefix $PrimaryMiSubnetAddress `
                         -NextHopType VnetLocal `
                       | Set-AzRouteTable
   Write-host "Primary network route table configured successfully."
   
   
   # Create primary managed instance
   
   Write-host "Creating primary managed instance..."
   Write-host "This will take some time, see https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance#managed-instance-management-operations or more information."
   New-AzSqlInstance -Name $primaryInstance `
                         -ResourceGroupName $resourceGroupName `
                         -Location $location `
                         -SubnetId $primaryMiSubnetConfigId `
                         -AdministratorCredential $mycreds `
                         -StorageSizeInGB $maxStorage `
                         -VCore $vCores `
                         -Edition $edition `
                         -ComputeGeneration $computeGeneration `
                         -LicenseType $license
   Write-host "Primary managed instance created successfully."
   ```

本教程的這一部分使用以下 PowerShell Cmdlet：

| Command | 注意 |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | 建立 Azure 資源群組。  |
| [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork) | 建立虛擬網路。  |
| [Add-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/add-azvirtualnetworksubnetconfig) | 對虛擬網路新增子網路組態。 | 
| [Get-AzVirtualNetwork](/powershell/module/az.network/get-azvirtualnetwork) | 取得資源群組中的虛擬網路。 | 
| [Get-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/get-azvirtualnetworksubnetconfig) | 取得虛擬網路中的子網路。 | 
| [New-AzNetworkSecurityGroup](/powershell/module/az.network/new-aznetworksecuritygroup) | 建立網路安全性群組。 | 
| [New-AzRouteTable](/powershell/module/az.network/new-azroutetable) | 建立路由表。 |
| [Set-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/set-azvirtualnetworksubnetconfig) | 更新虛擬網路的子網路組態。  |
| [Set-AzVirtualNetwork](/powershell/module/az.network/set-azvirtualnetwork) | 更新虛擬網路。  |
| [Get-AzNetworkSecurityGroup](/powershell/module/az.network/get-aznetworksecuritygroup) | 取得網路安全性群組。 |
| [Add-AzNetworkSecurityRuleConfig](/powershell/module/az.network/add-aznetworksecurityruleconfig)| 將網路安全性規則設定新增到網路安全性群組中。 |
| [Set-AzNetworkSecurityGroup](/powershell/module/az.network/set-aznetworksecuritygroup) | 更新網路安全性群組。  | 
| [Add-AzRouteConfig](/powershell/module/az.network/add-azrouteconfig) | 將路由新增到路由表。 |
| [Set-AzRouteTable](/powershell/module/az.network/set-azroutetable) | 更新路由表。  |
| [New-AzSqlInstance](/powershell/module/az.sql/new-azsqlinstance) | 建立 Azure SQL Database 受控執行個體。  |

---

## <a name="2---create-secondary-virtual-network"></a>2 - 創建輔助虛擬網路
如果使用 Azure 門戶創建託管實例，則需要單獨創建虛擬網路，因為要求主託管實例和輔助託管實例的子網沒有重疊範圍。 如果您使用 PowerShell 配置託管實例，請跳到步驟 3。 

# <a name="portal"></a>[入口網站](#tab/azure-portal) 
要驗證主虛擬網路的子網範圍，請按照以下步驟操作：
1. 在[Azure 門戶](https://portal.azure.com)中，導航到資源組並選擇主實例的虛擬網路。 
1. 在 **"設置"** 下選擇**子網**並記下**位址範圍**。 輔助託管實例的虛擬網路的子網位址範圍不能重疊。 


   ![主子網](media/sql-database-managed-instance-failover-group-tutorial/verify-primary-subnet-range.png)

要創建虛擬網路，請按照以下步驟操作：

1. 在[Azure 門戶](https://portal.azure.com)中，選擇 **"創建資源並**搜索*虛擬網路*"。 
1. 選擇 Microsoft 發佈的**虛擬網路**選項，然後在下一頁上選擇 **"創建**"。 
1. 填寫用於配置輔助託管實例的虛擬網路所需的欄位，然後選擇 **"創建**"。 

   下表顯示了輔助虛擬網路所需的值：

    | **領域** | 值 |
    | --- | --- |
    | **名稱** |  輔助託管實例（如`vnet-sql-mi-secondary`） 使用的虛擬網路的名稱。 |
    | **位址空間** | 虛擬網路的位址空間，如`10.128.0.0/16`。 | 
    | **訂閱** | 主託管實例和資源組所在的訂閱。 |
    | **地區** | 將部署輔助託管實例的位置。 |
    | **子** | 子網的名稱。 `default`預設情況下為您提供。 |
    | **位址範圍**| 子網的位址範圍。 這必須不同于主託管實例的虛擬網路（如`10.128.0.0/24`）使用的子網位址範圍。  |
    | &nbsp; | &nbsp; |

    ![輔助虛擬網路值](media/sql-database-managed-instance-failover-group-tutorial/secondary-virtual-network.png)

# <a name="powershell"></a>[電源外殼](#tab/azure-powershell)

僅當使用 Azure 門戶部署託管實例時，才需要此步驟。 如果您使用的是 PowerShell，請先跳至步驟 3。 

---

## <a name="3---create-a-secondary-managed-instance"></a>3 - 創建輔助託管實例
在此步驟中，您將在 Azure 門戶中創建輔助託管實例，該實例還將配置兩個託管實例之間的網路。 

您的第二個託管實例必須：
- 為空。 
- 具有與主託管實例不同的子網和 IP 範圍。 

# <a name="portal"></a>[入口網站](#tab/azure-portal) 

使用 Azure 門戶創建輔助託管實例。 

1. 在 Azure 入口網站的左側功能表中，選取 [Azure SQL]****。 如果 **Azure SQL** 不在清單中，請選取 [所有服務]****，然後在搜尋方塊中輸入 Azure SQL。 (選用) 選取 **Azure SQL** 旁的星號將其設為最愛，並新增為左側導覽中的項目。 
1. 選取 [+ 新增]**** 以開啟 [選取 SQL 部署選項]**** 頁面。 您可以選取 [資料庫] 圖格上的 [顯示詳細資料]，以查看不同資料庫的其他資訊。
1. 在 SQL**託管實例**磁貼上選擇 **"創建**"。 

    ![選擇託管實例](media/sql-database-managed-instance-failover-group-tutorial/select-managed-instance.png)

1. 在 **"創建 Azure SQL 資料庫託管實例"** 頁上的 **"基本"** 選項卡上，填寫用於配置輔助託管實例所需的欄位。 

   下表顯示了輔助託管實例所需的值：
 
    | **領域** | 值 |
    | --- | --- |
    | **訂閱** |  主託管實例所在的訂閱。 |
    | **資源組**| 主託管實例所在的資源組。 |
    | **受控執行個體名稱** | 新輔助託管實例的名稱，例如`sql-mi-secondary`  | 
    | **地區**| 輔助託管實例的位置。  |
    | **受控執行個體系統管理員登入** | 要用於新輔助託管實例的登錄名，如`azureuser`。 |
    | **密碼** | 管理員登錄到新的輔助託管實例將使用的複雜密碼。  |
    | &nbsp; | &nbsp; |

1. 在"**網路**"選項卡下，對於**虛擬網路**，從下拉清單中選擇為輔助託管實例創建的虛擬網路。

   ![輔助 MI 網路](media/sql-database-managed-instance-failover-group-tutorial/networking-settings-for-secondary-mi.png)

1. 在"**附加設置"** 選項卡下，對於**異地複製**，選擇"**是**"_用作容錯移轉輔助_。 從下拉清單中選擇主託管實例。 
    1. 確保排序規則和時區與主託管實例的排序規則和時區匹配。 本教程中創建的主要託管實例使用預設的`SQL_Latin1_General_CP1_CI_AS`排序規則和`(UTC) Coordinated Universal Time`時區。 

   ![輔助 MI 網路](media/sql-database-managed-instance-failover-group-tutorial/secondary-mi-failover.png)

1. 選擇 **"查看 + 創建**"以查看輔助託管實例的設置。 
1. 選擇 **"創建**"以創建輔助託管實例。 

# <a name="powershell"></a>[電源外殼](#tab/azure-powershell)

使用 PowerShell 創建輔助託管實例。 

   ```powershell-interactive
   # Configure secondary virtual network
   Write-host "Configuring secondary virtual network..."
   
   $SecondaryVirtualNetwork = New-AzVirtualNetwork `
                         -ResourceGroupName $resourceGroupName `
                         -Location $drlocation `
                         -Name $secondaryVNet `
                         -AddressPrefix $secondaryAddressPrefix
   
   Add-AzVirtualNetworkSubnetConfig `
                         -Name $secondaryMiSubnetName `
                         -VirtualNetwork $SecondaryVirtualNetwork `
                         -AddressPrefix $secondaryMiSubnetAddress `
                       | Set-AzVirtualNetwork
   $SecondaryVirtualNetwork
   
   # Configure secondary managed instance subnet
   Write-host "Configuring secondary MI subnet..."
   
   $SecondaryVirtualNetwork = Get-AzVirtualNetwork -Name $secondaryVNet `
                                   -ResourceGroupName $resourceGroupName
   
   $secondaryMiSubnetConfig = Get-AzVirtualNetworkSubnetConfig `
                           -Name $secondaryMiSubnetName `
                           -VirtualNetwork $SecondaryVirtualNetwork
   $secondaryMiSubnetConfig
   
   # Configure secondary network security group management service
   Write-host "Configuring secondary network security group management service..."
   
   $secondaryMiSubnetConfigId = $secondaryMiSubnetConfig.Id
   
   $secondaryNSGMiManagementService = New-AzNetworkSecurityGroup `
                         -Name 'secondaryToMIManagementService' `
                         -ResourceGroupName $resourceGroupName `
                         -location $drlocation
   $secondaryNSGMiManagementService
   
   # Configure secondary route table MI management service
   Write-host "Configuring secondary route table MI management service..."
   
   $secondaryRouteTableMiManagementService = New-AzRouteTable `
                         -Name 'secondaryRouteTableMiManagementService' `
                         -ResourceGroupName $resourceGroupName `
                         -location $drlocation
   $secondaryRouteTableMiManagementService
   
   # Configure the secondary network security group
   Write-host "Configuring secondary network security group..."
   
   Set-AzVirtualNetworkSubnetConfig `
                         -VirtualNetwork $SecondaryVirtualNetwork `
                         -Name $secondaryMiSubnetName `
                         -AddressPrefix $secondaryMiSubnetAddress `
                         -NetworkSecurityGroup $secondaryNSGMiManagementService `
                         -RouteTable $secondaryRouteTableMiManagementService `
                       | Set-AzVirtualNetwork
   
   Get-AzNetworkSecurityGroup `
                         -ResourceGroupName $resourceGroupName `
                         -Name "secondaryToMIManagementService" `
                       | Add-AzNetworkSecurityRuleConfig `
                         -Priority 100 `
                         -Name "allow_management_inbound" `
                         -Access Allow `
                         -Protocol Tcp `
                         -Direction Inbound `
                         -SourcePortRange * `
                         -SourceAddressPrefix * `
                         -DestinationPortRange 9000,9003,1438,1440,1452 `
                         -DestinationAddressPrefix * `
                       | Add-AzNetworkSecurityRuleConfig `
                         -Priority 200 `
                         -Name "allow_misubnet_inbound" `
                         -Access Allow `
                         -Protocol * `
                         -Direction Inbound `
                         -SourcePortRange * `
                         -SourceAddressPrefix $secondaryMiSubnetAddress `
                         -DestinationPortRange * `
                         -DestinationAddressPrefix * `
                       | Add-AzNetworkSecurityRuleConfig `
                         -Priority 300 `
                         -Name "allow_health_probe_inbound" `
                         -Access Allow `
                         -Protocol * `
                         -Direction Inbound `
                         -SourcePortRange * `
                         -SourceAddressPrefix AzureLoadBalancer `
                         -DestinationPortRange * `
                         -DestinationAddressPrefix * `
                       | Add-AzNetworkSecurityRuleConfig `
                         -Priority 1000 `
                         -Name "allow_tds_inbound" `
                         -Access Allow `
                         -Protocol Tcp `
                         -Direction Inbound `
                         -SourcePortRange * `
                         -SourceAddressPrefix VirtualNetwork `
                         -DestinationPortRange 1433 `
                         -DestinationAddressPrefix * `
                       | Add-AzNetworkSecurityRuleConfig `
                         -Priority 1100 `
                         -Name "allow_redirect_inbound" `
                         -Access Allow `
                         -Protocol Tcp `
                         -Direction Inbound `
                         -SourcePortRange * `
                         -SourceAddressPrefix VirtualNetwork `
                         -DestinationPortRange 11000-11999 `
                         -DestinationAddressPrefix * `
                       | Add-AzNetworkSecurityRuleConfig `
                         -Priority 1200 `
                         -Name "allow_geodr_inbound" `
                         -Access Allow `
                         -Protocol Tcp `
                         -Direction Inbound `
                         -SourcePortRange * `
                         -SourceAddressPrefix VirtualNetwork `
                         -DestinationPortRange 5022 `
                         -DestinationAddressPrefix * `
                       | Add-AzNetworkSecurityRuleConfig `
                         -Priority 4096 `
                         -Name "deny_all_inbound" `
                         -Access Deny `
                         -Protocol * `
                         -Direction Inbound `
                         -SourcePortRange * `
                         -SourceAddressPrefix * `
                         -DestinationPortRange * `
                         -DestinationAddressPrefix * `
                       | Add-AzNetworkSecurityRuleConfig `
                         -Priority 100 `
                         -Name "allow_management_outbound" `
                         -Access Allow `
                         -Protocol Tcp `
                         -Direction Outbound `
                         -SourcePortRange * `
                         -SourceAddressPrefix * `
                         -DestinationPortRange 80,443,12000 `
                         -DestinationAddressPrefix * `
                       | Add-AzNetworkSecurityRuleConfig `
                         -Priority 200 `
                         -Name "allow_misubnet_outbound" `
                         -Access Allow `
                         -Protocol * `
                         -Direction Outbound `
                         -SourcePortRange * `
                         -SourceAddressPrefix * `
                         -DestinationPortRange * `
                         -DestinationAddressPrefix $secondaryMiSubnetAddress `
                       | Add-AzNetworkSecurityRuleConfig `
                         -Priority 1100 `
                         -Name "allow_redirect_outbound" `
                         -Access Allow `
                         -Protocol Tcp `
                         -Direction Outbound `
                         -SourcePortRange * `
                         -SourceAddressPrefix VirtualNetwork `
                         -DestinationPortRange 11000-11999 `
                         -DestinationAddressPrefix * `
                       | Add-AzNetworkSecurityRuleConfig `
                         -Priority 1200 `
                         -Name "allow_geodr_outbound" `
                         -Access Allow `
                         -Protocol Tcp `
                         -Direction Outbound `
                         -SourcePortRange * `
                         -SourceAddressPrefix VirtualNetwork `
                         -DestinationPortRange 5022 `
                         -DestinationAddressPrefix * `
                       | Add-AzNetworkSecurityRuleConfig `
                         -Priority 4096 `
                         -Name "deny_all_outbound" `
                         -Access Deny `
                         -Protocol * `
                         -Direction Outbound `
                         -SourcePortRange * `
                         -SourceAddressPrefix * `
                         -DestinationPortRange * `
                         -DestinationAddressPrefix * `
                       | Set-AzNetworkSecurityGroup
   
   
   Get-AzRouteTable `
                         -ResourceGroupName $resourceGroupName `
                         -Name "secondaryRouteTableMiManagementService" `
                       | Add-AzRouteConfig `
                         -Name "secondaryToMIManagementService" `
                         -AddressPrefix 0.0.0.0/0 `
                         -NextHopType Internet `
                       | Add-AzRouteConfig `
                         -Name "ToLocalClusterNode" `
                         -AddressPrefix $secondaryMiSubnetAddress `
                         -NextHopType VnetLocal `
                       | Set-AzRouteTable
   Write-host "Secondary network security group configured successfully."
   
   # Create secondary managed instance
   
   $primaryManagedInstanceId = Get-AzSqlInstance -Name $primaryInstance -ResourceGroupName $resourceGroupName | Select-Object Id
   
   
   Write-host "Creating secondary managed instance..."
   Write-host "This will take some time, see https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance#managed-instance-management-operations or more information."
   New-AzSqlInstance -Name $secondaryInstance `
                     -ResourceGroupName $resourceGroupName `
                     -Location $drLocation `
                     -SubnetId $secondaryMiSubnetConfigId `
                     -AdministratorCredential $mycreds `
                     -StorageSizeInGB $maxStorage `
                     -VCore $vCores `
                     -Edition $edition `
                     -ComputeGeneration $computeGeneration `
                     -LicenseType $license `
                     -DnsZonePartner $primaryManagedInstanceId.Id
   Write-host "Secondary managed instance created successfully."
   ```

本教程的這一部分使用以下 PowerShell Cmdlet：

| Command | 注意 |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | 建立 Azure 資源群組。  |
| [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork) | 建立虛擬網路。  |
| [Add-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/add-azvirtualnetworksubnetconfig) | 對虛擬網路新增子網路組態。 | 
| [Get-AzVirtualNetwork](/powershell/module/az.network/get-azvirtualnetwork) | 取得資源群組中的虛擬網路。 | 
| [Get-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/get-azvirtualnetworksubnetconfig) | 取得虛擬網路中的子網路。 | 
| [New-AzNetworkSecurityGroup](/powershell/module/az.network/new-aznetworksecuritygroup) | 建立網路安全性群組。 | 
| [New-AzRouteTable](/powershell/module/az.network/new-azroutetable) | 建立路由表。 |
| [Set-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/set-azvirtualnetworksubnetconfig) | 更新虛擬網路的子網路組態。  |
| [Set-AzVirtualNetwork](/powershell/module/az.network/set-azvirtualnetwork) | 更新虛擬網路。  |
| [Get-AzNetworkSecurityGroup](/powershell/module/az.network/get-aznetworksecuritygroup) | 取得網路安全性群組。 |
| [Add-AzNetworkSecurityRuleConfig](/powershell/module/az.network/add-aznetworksecurityruleconfig)| 將網路安全性規則設定新增到網路安全性群組中。 |
| [Set-AzNetworkSecurityGroup](/powershell/module/az.network/set-aznetworksecuritygroup) | 更新網路安全性群組。  | 
| [Add-AzRouteConfig](/powershell/module/az.network/add-azrouteconfig) | 將路由新增到路由表。 |
| [Set-AzRouteTable](/powershell/module/az.network/set-azroutetable) | 更新路由表。  |
| [New-AzSqlInstance](/powershell/module/az.sql/new-azsqlinstance) | 建立 Azure SQL Database 受控執行個體。  |

---

## <a name="4---create-primary-gateway"></a>4 - 創建主閘道 
要參與容錯移轉組的兩個託管實例，必須在兩個託管實例的虛擬網路之間配置 ExpressRoute 或閘道，以允許網路通信。 如果選擇配置[ExpressRoute](../expressroute/expressroute-howto-circuit-portal-resource-manager.md)而不是連接兩個 VPN 閘道，請跳到[步驟 7](#7---create-a-failover-group)。  

本文提供了創建兩個 VPN 閘道並連接它們的步驟，但如果已配置 ExpressRoute，則可以跳過創建容錯移轉組。 


# <a name="portal"></a>[入口網站](#tab/azure-portal)

使用 Azure 門戶為主託管實例的虛擬網路創建閘道。 


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
    | **名稱** | 虛擬網路閘道的名稱，如`primary-mi-gateway`。 | 
    | **地區** | 輔助託管實例所在的區域。 |
    | **閘道類型** | 選擇**VPN**。 |
    | **VPN 類型** | 選擇**基於路由** |
    | **Sku**| 將 預設值`VpnGw1`保留 。 |
    | **位置**| 主託管實例和主虛擬網路的位置。   |
    | **虛擬網路**| 選擇在第 2 節中創建的虛擬網路，如`vnet-sql-mi-primary`。 |
    | **公共 IP 位址**| 選取 [建立新的]****。 |
    | **公共 IP 位址名稱**| 輸入 IP 位址的名稱，如`primary-gateway-IP`。 |
    | &nbsp; | &nbsp; |

1. 將其他值保留為預設值，然後選擇 **"查看 + 創建**"以查看虛擬網路閘道的設置。

   ![主要閘道設置](media/sql-database-managed-instance-failover-group-tutorial/settings-for-primary-gateway.png)

1. 選擇 **"創建**"以創建新的虛擬網路閘道。 


# <a name="powershell"></a>[電源外殼](#tab/azure-powershell)

使用 PowerShell 為主託管實例的虛擬網路創建閘道。 

   ```powershell-interactive
   # Create primary gateway
   Write-host "Adding GatewaySubnet to primary VNet..."
   Get-AzVirtualNetwork `
                     -Name $primaryVNet `
                     -ResourceGroupName $resourceGroupName `
                   | Add-AzVirtualNetworkSubnetConfig `
                     -Name "GatewaySubnet" `
                     -AddressPrefix $primaryMiGwSubnetAddress `
                   | Set-AzVirtualNetwork
   
   $primaryVirtualNetwork  = Get-AzVirtualNetwork `
                     -Name $primaryVNet `
                     -ResourceGroupName $resourceGroupName
   $primaryGatewaySubnet = Get-AzVirtualNetworkSubnetConfig `
                     -Name "GatewaySubnet" `
                     -VirtualNetwork $primaryVirtualNetwork
   
   Write-host "Creating primary gateway..."
   Write-host "This will take some time."
   $primaryGWPublicIP = New-AzPublicIpAddress -Name $primaryGWPublicIPAddress -ResourceGroupName $resourceGroupName `
            -Location $location -AllocationMethod Dynamic
   $primaryGatewayIPConfig = New-AzVirtualNetworkGatewayIpConfig -Name $primaryGWIPConfig `
            -Subnet $primaryGatewaySubnet -PublicIpAddress $primaryGWPublicIP
   
   $primaryGateway = New-AzVirtualNetworkGateway -Name $primaryGWName -ResourceGroupName $resourceGroupName `
       -Location $location -IpConfigurations $primaryGatewayIPConfig -GatewayType Vpn `
       -VpnType RouteBased -GatewaySku VpnGw1 -EnableBgp $true -Asn $primaryGWAsn
   $primaryGateway
   ```

本教程的這一部分使用以下 PowerShell Cmdlet：

| Command | 注意 |
|---|---|
| [Get-AzVirtualNetwork](/powershell/module/az.network/get-azvirtualnetwork) | 取得資源群組中的虛擬網路。 |
| [Add-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/add-azvirtualnetworksubnetconfig) | 對虛擬網路新增子網路組態。 | 
| [Set-AzVirtualNetwork](/powershell/module/az.network/set-azvirtualnetwork) | 更新虛擬網路。  |
| [Get-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/get-azvirtualnetworksubnetconfig) | 取得虛擬網路中的子網路。 |
| [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress) | 建立公用 IP 位址。  | 
| [New-AzVirtualNetworkGatewayIpConfig](/powershell/module/az.network/new-azvirtualnetworkgatewayipconfig) | 建立虛擬網路閘道的 IP 組態 |
| [New-AzVirtualNetworkGateway](/powershell/module/az.network/new-azvirtualnetworkgateway) | 建立虛擬網路閘道 |


---


## <a name="5---create-secondary-gateway"></a>5 - 創建輔助閘道 
在此步驟中，使用 Azure 門戶為輔助託管實例的虛擬網路創建閘道， 


# <a name="portal"></a>[入口網站](#tab/azure-portal)

使用 Azure 門戶，重複上一節中的步驟，為輔助託管實例創建虛擬網路子網和閘道。 填寫所需的欄位來配置輔助託管實例的閘道。 

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

使用 PowerShell 為輔助託管實例的虛擬網路創建閘道。 

   ```powershell-interactive
   # Create the secondary gateway
   Write-host "Creating secondary gateway..."
   
   Write-host "Adding GatewaySubnet to secondary VNet..."
   Get-AzVirtualNetwork `
                     -Name $secondaryVNet `
                     -ResourceGroupName $resourceGroupName `
                   | Add-AzVirtualNetworkSubnetConfig `
                     -Name "GatewaySubnet" `
                     -AddressPrefix $secondaryMiGwSubnetAddress `
                   | Set-AzVirtualNetwork
   
   $secondaryVirtualNetwork  = Get-AzVirtualNetwork `
                     -Name $secondaryVNet `
                     -ResourceGroupName $resourceGroupName
   $secondaryGatewaySubnet = Get-AzVirtualNetworkSubnetConfig `
                     -Name "GatewaySubnet" `
                     -VirtualNetwork $secondaryVirtualNetwork
   $drLocation = $secondaryVirtualNetwork.Location
   
   Write-host "Creating primary gateway..."
   Write-host "This will take some time."
   $secondaryGWPublicIP = New-AzPublicIpAddress -Name $secondaryGWPublicIPAddress -ResourceGroupName $resourceGroupName `
            -Location $drLocation -AllocationMethod Dynamic
   $secondaryGatewayIPConfig = New-AzVirtualNetworkGatewayIpConfig -Name $secondaryGWIPConfig `
            -Subnet $secondaryGatewaySubnet -PublicIpAddress $secondaryGWPublicIP
   
   $secondaryGateway = New-AzVirtualNetworkGateway -Name $secondaryGWName -ResourceGroupName $resourceGroupName `
       -Location $drLocation -IpConfigurations $secondaryGatewayIPConfig -GatewayType Vpn `
       -VpnType RouteBased -GatewaySku VpnGw1 -EnableBgp $true -Asn $secondaryGWAsn
   $secondaryGateway
   ```

本教程的這一部分使用以下 PowerShell Cmdlet：

| Command | 注意 |
|---|---|
| [Get-AzVirtualNetwork](/powershell/module/az.network/get-azvirtualnetwork) | 取得資源群組中的虛擬網路。 |
| [Add-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/add-azvirtualnetworksubnetconfig) | 對虛擬網路新增子網路組態。 | 
| [Set-AzVirtualNetwork](/powershell/module/az.network/set-azvirtualnetwork) | 更新虛擬網路。  |
| [Get-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/get-azvirtualnetworksubnetconfig) | 取得虛擬網路中的子網路。 |
| [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress) | 建立公用 IP 位址。  | 
| [New-AzVirtualNetworkGatewayIpConfig](/powershell/module/az.network/new-azvirtualnetworkgatewayipconfig) | 建立虛擬網路閘道的 IP 組態 |
| [New-AzVirtualNetworkGateway](/powershell/module/az.network/new-azvirtualnetworkgateway) | 建立虛擬網路閘道 |

---


## <a name="6---connect-the-gateways"></a>6 - 連接閘道
在此步驟中，在兩個虛擬網路的兩個閘道之間創建雙向連接。 


# <a name="portal"></a>[入口網站](#tab/azure-portal)

使用 Azure 門戶連接兩個閘道。 


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

使用 PowerShell 連接兩個閘道。 

   ```powershell-interactive
   # Connect the primary to secondary gateway
   Write-host "Connecting the primary gateway to secondary gateway..."
   New-AzVirtualNetworkGatewayConnection -Name $primaryGWConnection -ResourceGroupName $resourceGroupName `
       -VirtualNetworkGateway1 $primaryGateway -VirtualNetworkGateway2 $secondaryGateway -Location $location `
       -ConnectionType Vnet2Vnet -SharedKey $vpnSharedKey -EnableBgp $true
   $primaryGWConnection
   
   # Connect the secondary to primary gateway
   Write-host "Connecting the secondary gateway to primary gateway..."
   
   New-AzVirtualNetworkGatewayConnection -Name $secondaryGWConnection -ResourceGroupName $resourceGroupName `
       -VirtualNetworkGateway1 $secondaryGateway -VirtualNetworkGateway2 $primaryGateway -Location $drLocation `
       -ConnectionType Vnet2Vnet -SharedKey $vpnSharedKey -EnableBgp $true
   $secondaryGWConnection
   ```

本教程的這一部分使用以下 PowerShell Cmdlet：

| Command | 注意 |
|---|---|
| [New-AzVirtualNetworkGatewayConnection](/powershell/module/az.network/new-azvirtualnetworkgatewayconnection) | 建立兩個虛擬網路閘道之間的連線。   |

---


## <a name="7---create-a-failover-group"></a>7 - 創建容錯移轉組
在此步驟中，您將創建容錯移轉組，並將兩個託管實例添加到其中。 


# <a name="portal"></a>[入口網站](#tab/azure-portal)
使用 Azure 門戶創建容錯移轉組。 


1. 在[Azure 門戶](https://portal.azure.com)的左側功能表中選擇**Azure SQL。** 如果 **Azure SQL** 不在清單中，請選取 [所有服務]****，然後在搜尋方塊中輸入 Azure SQL。 (選用) 選取 **Azure SQL** 旁的星號將其設為最愛，並新增為左側導覽中的項目。 
1. 選擇在第一部分（如`sql-mi-primary`）中創建的主要託管實例。 
1. 在 **"設置"** 下，導航到**實例容錯移轉組**，然後選擇**添加組**以打開**實例容錯移轉組**頁面。 

   ![添加容錯移轉組](media/sql-database-managed-instance-failover-group-tutorial/add-failover-group.png)

1. 在 **"實例容錯移轉組"** 頁上，鍵入容錯移轉組的名稱，例如`failovergrouptutorial`，然後從下拉清單中選擇輔助託管實例。 `sql-mi-secondary` 選擇 **"創建**"以創建容錯移轉組。 

   ![建立容錯移轉群組](media/sql-database-managed-instance-failover-group-tutorial/create-failover-group.png)

1. 容錯移轉組部署完成後，您將被帶回**容錯移轉組**頁面。 


# <a name="powershell"></a>[電源外殼](#tab/azure-powershell)
使用 PowerShell 創建容錯移轉組。 

   ```powershell-interactive
   Write-host "Creating the failover group..."
   $failoverGroup = New-AzSqlDatabaseInstanceFailoverGroup -Name $failoverGroupName `
        -Location $location -ResourceGroupName $resourceGroupName -PrimaryManagedInstanceName $primaryInstance `
        -PartnerRegion $drLocation -PartnerManagedInstanceName $secondaryInstance `
        -FailoverPolicy Automatic -GracePeriodWithDataLossHours 1
   $failoverGroup
   ```

本教程的這一部分使用以下 PowerShell Cmdlet：

| Command | 注意 |
|---|---|
| [New-AzSqlDatabaseInstanceFailoverGroup](/powershell/module/az.sql/new-azsqldatabaseinstancefailovergroup)| 建立新的 Azure SQL Database 受控執行個體容錯移轉群組。  |


---


## <a name="8---test-failover"></a>8 - 測試容錯移轉
在此步驟中，您將容錯移轉組容錯移轉到次要伺服器，然後使用 Azure 門戶失敗。 


# <a name="portal"></a>[入口網站](#tab/azure-portal)
使用 Azure 門戶測試容錯移轉。 


1. 導航到[Azure 門戶](https://portal.azure.com)中的_輔助_託管實例，並在設置下選擇**實例容錯移轉組**。 
1. 查看哪個託管實例是主實例，哪個託管實例是輔助實例。 
1. 選擇 **"容錯移轉**"，然後在有關 TDS 會話斷開連接的警告上選擇 **"是**"。 

   ![容錯移轉組容錯移轉](media/sql-database-managed-instance-failover-group-tutorial/failover-mi-failover-group.png)

1. 查看哪個 manged 實例是主實例，哪個實例是輔助實例。 如果容錯移轉成功，則兩個實例應切換角色。 

   ![託管實例在容錯移轉後切換角色](media/sql-database-managed-instance-failover-group-tutorial/mi-switched-after-failover.png)

1. 轉到新的_輔助_託管實例，並再次選擇**容錯移轉**，使主實例故障回主角色。 


# <a name="powershell"></a>[電源外殼](#tab/azure-powershell)
使用 PowerShell 測試容錯移轉。 

   ```powershell-interactive
    
   # Verify the current primary role
   Get-AzSqlDatabaseInstanceFailoverGroup -ResourceGroupName $resourceGroupName `
       -Location $location -Name $failoverGroupName
   
   # Failover the primary managed instance to the secondary role
   Write-host "Failing primary over to the secondary location"
   Get-AzSqlDatabaseInstanceFailoverGroup -ResourceGroupName $resourceGroupName `
       -Location $drLocation -Name $failoverGroupName | Switch-AzSqlDatabaseInstanceFailoverGroup
   Write-host "Successfully failed failover group to secondary location"
   ```


將容錯移轉群組還原至主要伺服器：

   ```powershell-interactive
   # Verify the current primary role
   Get-AzSqlDatabaseInstanceFailoverGroup -ResourceGroupName $resourceGroupName `
       -Location $drLocation -Name $failoverGroupName
   
   # Fail primary managed instance back to primary role
   Write-host "Failing primary back to primary role"
   Get-AzSqlDatabaseInstanceFailoverGroup -ResourceGroupName $resourceGroupName `
       -Location $location -Name $failoverGroupName | Switch-AzSqlDatabaseInstanceFailoverGroup
   Write-host "Successfully failed failover group to primary location"
   
   # Verify the current primary role
   Get-AzSqlDatabaseInstanceFailoverGroup -ResourceGroupName $resourceGroupName `
       -Location $location -Name $failoverGroupName
   ```

本教程的這一部分使用以下 PowerShell Cmdlet：

| Command | 注意 |
|---|---|
| [Get-AzSqlDatabaseInstanceFailoverGroup](/powershell/module/az.sql/get-azsqldatabaseinstancefailovergroup) | 取得或列出受控執行個體容錯移轉群組。| 
| [Switch-AzSqlDatabaseInstanceFailoverGroup](/powershell/module/az.sql/switch-azsqldatabaseinstancefailovergroup) | 執行受控執行個體容錯移轉群組的容錯移轉。 | 

---



## <a name="clean-up-resources"></a>清除資源
首先刪除託管實例、虛擬叢集、任何剩餘資源，最後刪除資源組來清理資源。 

# <a name="portal"></a>[入口網站](#tab/azure-portal)
1. 瀏覽至您在 [Azure 入口網站](https://portal.azure.com)中的資源群組。 
1. 選取受控執行個體，然後選取 [刪除]****。 在文字方塊中輸入 `yes` 以確認您要刪除資源，然後選取 [刪除]****。 此過程可能需要一些時間才能在後臺完成，在結束之前，您將無法刪除*虛擬叢集*或任何其他從屬資源。 監視 [活動] 索引標籤中的刪除，確認您的受控執行個體已刪除。 
1. 受控執行個體刪除後，請在資源群組中選取虛擬叢集**，然後選擇 [刪除]****，加以刪除。 在文字方塊中輸入 `yes` 以確認您要刪除資源，然後選取 [刪除]****。 
1. 刪除任何剩餘的資源。 在文字方塊中輸入 `yes` 以確認您要刪除資源，然後選取 [刪除]****。 
1. 選取 [刪除資源群組]****、輸入資源群組的名稱 `myResourceGroup`，然後選取 [刪除]****，以刪除資源群組。 

# <a name="powershell"></a>[電源外殼](#tab/azure-powershell)

您將需要移除資源群組兩次。 第一次移除資源群組時，將會移除受控執行個體和虛擬叢集，但接著會失敗並出現錯誤訊息：`Remove-AzResourceGroup : Long running operation failed with status 'Conflict'.`。 請再次執行 Remove-AzResourceGroup 命令，以移除所有剩餘的資源及資源群組。

```powershell-interactive
Remove-AzResourceGroup -ResourceGroupName $resourceGroupName
Write-host "Removing managed instance and virtual cluster..."
Remove-AzResourceGroup -ResourceGroupName $resourceGroupName
Write-host "Removing residual resources and resouce group..."
```

本教程的這一部分使用以下 PowerShell Cmdlet：

| Command | 注意 |
|---|---|
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | 移除資源群組。 |

---

## <a name="full-script"></a>完整指令碼

# <a name="powershell"></a>[電源外殼](#tab/azure-powershell)
[!code-powershell-interactive[main](../../powershell_scripts/sql-database/failover-groups/add-managed-instance-to-failover-group-az-ps.ps1 "Add managed instance to a failover group")]

此指令碼會使用下列命令。 下表中的每個命令都會連結至命令特定的文件。

| Command | 注意 |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | 建立 Azure 資源群組。  |
| [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork) | 建立虛擬網路。  |
| [Add-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/add-azvirtualnetworksubnetconfig) | 對虛擬網路新增子網路組態。 | 
| [Get-AzVirtualNetwork](/powershell/module/az.network/get-azvirtualnetwork) | 取得資源群組中的虛擬網路。 | 
| [Get-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/get-azvirtualnetworksubnetconfig) | 取得虛擬網路中的子網路。 | 
| [New-AzNetworkSecurityGroup](/powershell/module/az.network/new-aznetworksecuritygroup) | 建立網路安全性群組。 | 
| [New-AzRouteTable](/powershell/module/az.network/new-azroutetable) | 建立路由表。 |
| [Set-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/set-azvirtualnetworksubnetconfig) | 更新虛擬網路的子網路組態。  |
| [Set-AzVirtualNetwork](/powershell/module/az.network/set-azvirtualnetwork) | 更新虛擬網路。  |
| [Get-AzNetworkSecurityGroup](/powershell/module/az.network/get-aznetworksecuritygroup) | 取得網路安全性群組。 |
| [Add-AzNetworkSecurityRuleConfig](/powershell/module/az.network/add-aznetworksecurityruleconfig)| 將網路安全性規則設定新增到網路安全性群組中。 |
| [Set-AzNetworkSecurityGroup](/powershell/module/az.network/set-aznetworksecuritygroup) | 更新網路安全性群組。  | 
| [Add-AzRouteConfig](/powershell/module/az.network/add-azrouteconfig) | 將路由新增到路由表。 |
| [Set-AzRouteTable](/powershell/module/az.network/set-azroutetable) | 更新路由表。  |
| [New-AzSqlInstance](/powershell/module/az.sql/new-azsqlinstance) | 建立 Azure SQL Database 受控執行個體。  |
| [Get-AzSqlInstance](/powershell/module/az.sql/get-azsqlinstance)| 傳回 Azure SQL 受控資料庫執行個體的資訊。 |
| [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress) | 建立公用 IP 位址。  | 
| [New-AzVirtualNetworkGatewayIpConfig](/powershell/module/az.network/new-azvirtualnetworkgatewayipconfig) | 建立虛擬網路閘道的 IP 組態 |
| [New-AzVirtualNetworkGateway](/powershell/module/az.network/new-azvirtualnetworkgateway) | 建立虛擬網路閘道 |
| [New-AzVirtualNetworkGatewayConnection](/powershell/module/az.network/new-azvirtualnetworkgatewayconnection) | 建立兩個虛擬網路閘道之間的連線。   |
| [New-AzSqlDatabaseInstanceFailoverGroup](/powershell/module/az.sql/new-azsqldatabaseinstancefailovergroup)| 建立新的 Azure SQL Database 受控執行個體容錯移轉群組。  |
| [Get-AzSqlDatabaseInstanceFailoverGroup](/powershell/module/az.sql/get-azsqldatabaseinstancefailovergroup) | 取得或列出受控執行個體容錯移轉群組。| 
| [Switch-AzSqlDatabaseInstanceFailoverGroup](/powershell/module/az.sql/switch-azsqldatabaseinstancefailovergroup) | 執行受控執行個體容錯移轉群組的容錯移轉。 | 
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | 移除資源群組。 | 

# <a name="portal"></a>[入口網站](#tab/azure-portal) 

Azure 門戶沒有可用的腳本。

---

## <a name="next-steps"></a>後續步驟

在本教程中，您在兩個託管實例之間配置了容錯移轉組。 您已了解如何︰

> [!div class="checklist"]
> - 創建主託管實例
> - 創建輔助託管實例作為[容錯移轉組的](sql-database-auto-failover-group.md)一部分。 
> - 測試容錯移轉

如何連接到託管實例以及如何將資料庫還原到託管實例的下一個快速入門： 

> [!div class="nextstepaction"]
> [連接到託管實例](sql-database-managed-instance-configure-vm.md)
> [將資料庫還原到託管實例](sql-database-managed-instance-get-started-restore.md)


