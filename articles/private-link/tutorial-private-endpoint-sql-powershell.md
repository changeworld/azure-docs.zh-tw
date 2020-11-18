---
title: 教學課程 - 使用 Azure 私人端點連線到 Azure SQL 伺服器 - PowerShell
description: 使用此教學課程來了解如何使用 Azure PowerShell 建立具有私人端點的 Azure SQL 伺服器
services: private-link
author: asudbring
ms.service: private-link
ms.topic: tutorial
ms.date: 10/31/2020
ms.author: allensu
ms.openlocfilehash: 36b952131c2050230de89064adc586c5a2851b65
ms.sourcegitcommit: 4b76c284eb3d2b81b103430371a10abb912a83f4
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/01/2020
ms.locfileid: "93147566"
---
# <a name="tutorial---connect-to-an-azure-sql-server-using-an-azure-private-endpoint---azure-powershell"></a>教學課程 - 使用 Azure 私人端點連線到 Azure SQL 伺服器 - Azure PowerShell

Azure 私人端點是 Azure 中私人連結的基本建置組塊。 其可讓 Azure 資源 (例如虛擬機器 (VM)) 與私人連結資源進行私密通訊。

在本教學課程中，您會了解如何：

> [!div class="checklist"]
> * 建立虛擬網路和堡壘主機。
> * 建立虛擬機器。
> * 建立 Azure SQL 伺服器和私人端點。
> * 測試 SQL 伺服器私人端點的連線能力。

## <a name="prerequisites"></a>必要條件

* 具有有效訂用帳戶的 Azure 帳戶。 [免費建立帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
* 如果您選擇在本機安裝和使用 PowerShell，本文會要求使用 Azure PowerShell 模組版本 5.4.1 或更新版本。 執行 `Get-Module -ListAvailable Az` 來了解安裝的版本。 如果您需要升級，請參閱[安裝 Azure PowerShell 模組](/powershell/azure/install-Az-ps)。 如果您在本機執行 PowerShell，則也需要執行 `Connect-AzAccount` 以建立與 Azure 的連線。

## <a name="create-a-resource-group"></a>建立資源群組

Azure 資源群組是在其中部署與管理 Azure 資源的邏輯容器。

使用 [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) 來建立資源群組：

```azurepowershell-interactive
New-AzResourceGroup -Name 'CreateSQLEndpointTutorial-rg' -Location 'eastus'
```

## <a name="create-a-virtual-network-and-bastion-host"></a>建立虛擬網路和堡壘主機

在本節中，您會建立虛擬網路、子網路和堡壘主機。 

堡壘主機將用來安全地連線到虛擬機器，以測試私人端點。

使用下列方式建立虛擬網路和堡壘主機：

* [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork)
* [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress)
* [New-AzBastion](/powershell/module/az.network/new-azbastion)

```azurepowershell-interactive
## Create backend subnet config. ##
$subnetConfig = New-AzVirtualNetworkSubnetConfig -Name myBackendSubnet -AddressPrefix 10.0.0.0/24

## Create Azure Bastion subnet. ##
$bastsubnetConfig = New-AzVirtualNetworkSubnetConfig -Name AzureBastionSubnet -AddressPrefix 10.0.1.0/24

## Create the virtual network. ##
$parameters1 = @{
    Name = 'MyVNet'
    ResourceGroupName = 'CreateSQLEndpointTutorial-rg'
    Location = 'eastus'
    AddressPrefix = '10.0.0.0/16'
    Subnet = $subnetConfig, $bastsubnetConfig
}
$vnet = New-AzVirtualNetwork @parameters1

## Create public IP address for bastion host. ##
$parameters2 = @{
    Name = 'myBastionIP'
    ResourceGroupName = 'CreateSQLEndpointTutorial-rg'
    Location = 'eastus'
    Sku = 'Standard'
    AllocationMethod = 'Static'
}
$publicip = New-AzPublicIpAddress @parameters2

## Create bastion host ##
$parameters3 = @{
    ResourceGroupName = 'CreateSQLEndpointTutorial-rg'
    Name = 'myBastion'
    PublicIpAddress = $publicip
    VirtualNetwork = $vnet
}
New-AzBastion @parameters3
```

部署 Azure Bastion 主機需要幾分鐘的時間。

## <a name="create-test-virtual-machine"></a>建立測試虛擬機器

在本節中，您將建立將用來測試私人端點的虛擬機器。

使用下列方式建立虛擬機器：

  * [Get-Credential](/powershell/module/microsoft.powershell.security/get-credential)
  * [New-AzNetworkInterface](/powershell/module/az.network/new-aznetworkinterface) 
  * [New-AzVM](/powershell/module/az.compute/new-azvm)
  * [New-AzVMConfig](/powershell/module/az.compute/new-azvmconfig)
  * [Set-AzVMOperatingSystem](/powershell/module/az.compute/set-azvmoperatingsystem)
  * [Set-AzVMSourceImage](/powershell/module/az.compute/set-azvmsourceimage)
  * [Add-AzVMNetworkInterface](/powershell/module/az.compute/add-azvmnetworkinterface)


```azurepowershell-interactive
## Set credentials for server admin and password. ##
$cred = Get-Credential

## Command to get virtual network configuration. ##
$vnet = Get-AzVirtualNetwork -Name myVNet -ResourceGroupName CreateSQLEndpointTutorial-rg

## Command to create network interface for VM ##
$parameters1 = @{
    Name = 'myNicVM'
    ResourceGroupName = 'CreateSQLEndpointTutorial-rg'
    Location = 'eastus'
    Subnet = $vnet.Subnets[0]
}
$nicVM = New-AzNetworkInterface @parameters1

## Create a virtual machine configuration.##
$parameters2 = @{
    VMName = 'myVM'
    VMSize = 'Standard_DS1_v2'
}
$parameters3 = @{
    ComputerName = 'myVM'
    Credential = $cred
}
$parameters4 = @{
    PublisherName = 'MicrosoftWindowsServer'
    Offer = 'WindowsServer'
    Skus = '2019-Datacenter'
    Version = 'latest'
}
$vmConfig = 
New-AzVMConfig @parameters2 | Set-AzVMOperatingSystem -Windows @parameters3 | Set-AzVMSourceImage @parameters4 | Add-AzVMNetworkInterface -Id $nicVM.Id

## Create the virtual machine ##
New-AzVM -ResourceGroupName 'CreateSQLEndpointTutorial-rg' -Location 'eastus' -VM $vmConfig
```

## <a name="create-an-azure-sql-server"></a>建立 Azure SQL Server

在本節中，您將會使用下列命令來建立 SQL Server 和資料庫：

* [New-AzSqlServer](/powershell/module/az.sql/new-azsqlserver)
* [New-AzSQlDatabase](/powershell/module/az.sql/new-azsqldatabase)

建立 SQL Server 和資料庫。 以您唯一的伺服器名稱取代 **\<sql-server-name>** ：

```azurepowershell-interactive
## Set and administrator and password for the SQL server. ##
$cred = Get-Credential

## Create SQL server ##
$parameters1 = @{
    ResourceGroupName = 'CreateSQLEndpointTutorial-rg'
    ServerName = '<sql-server-name>'
    SqlAdministratorCredentials = $cred
    Location = 'eastus'
}
New-AzSqlServer @parameters1

## Create database. ##
$parameters2 = @{
    ResourceGroupName = 'CreateSQLEndpointTutorial-rg'
    ServerName = '<sql-server-name>'
    DatabaseName = 'mysqldatabase'
    RequestedServiceObjectiveName = 'S0'
    SampleName = 'AdventureWorksLT'
}
New-AzSqlDatabase @parameters2
```

## <a name="create-private-endpoint"></a>建立私人端點

在本節中，您會使用下列方式建立私人端點和連線：

* [New-AzPrivateLinkServiceConnection](/powershell/module/az.network/New-AzPrivateLinkServiceConnection)
* [New-AzPrivateEndpoint](/powershell/module/az.network/new-azprivateendpoint)

```azurepowershell-interactive
## Place SQL server into variable. Replace <sql-server-name> with your server name ##
$server = Get-AzSqlServer -ResourceGroupName CreateSQLEndpointTutorial-rg -ServerName <sql-server-name>

## Create private endpoint connection. ##
$parameters1 = @{
    Name = 'myConnection'
    PrivateLinkServiceId = $server.ResourceID
    GroupID = 'sqlserver'
}
$privateEndpointConnection = New-AzPrivateLinkServiceConnection @parameters1

## Place virtual network into variable. ##
$vnet = Get-AzVirtualNetwork -ResourceGroupName 'CreateSQLEndpointTutorial-rg' -Name 'myVNet'

## Disable private endpoint network policy ##
$vnet.Subnets[0].PrivateEndpointNetworkPolicies = "Disabled"
$vnet | Set-AzVirtualNetwork

## Create private endpoint
$parameters2 = @{
    ResourceGroupName = 'CreateSQLEndpointTutorial-rg'
    Name = 'myPrivateEndpoint'
    Location = 'eastus'
    Subnet = $vnet.Subnets[0]
    PrivateLinkServiceConnection = $privateEndpointConnection
}
New-AzPrivateEndpoint @parameters2
```
## <a name="configure-the-private-dns-zone"></a>設定私人 DNS 區域

在本節中，您會使用下列方式建立和設定私人 DNS 區域：

* [New-AzPrivateDnsZone](/powershell/module/az.privatedns/new-azprivatednszone)
* [New-AzPrivateDnsVirtualNetworkLink](/powershell/module/az.privatedns/new-azprivatednsvirtualnetworklink)
* [New-AzPrivateDnsZoneConfig](/powershell/module/az.network/new-azprivatednszoneconfig)
* [New-AzPrivateDnsZoneGroup](/powershell/module/az.network/new-azprivatednszonegroup)

```azurepowershell-interactive
## Place virtual network into variable. ##
$vnet = Get-AzVirtualNetwork -ResourceGroupName 'CreateSQLEndpointTutorial-rg' -Name 'myVNet'

## Create private dns zone. ##
$parameters1 = @{
    ResourceGroupName = 'CreateSQLEndpointTutorial-rg'
    Name = 'privatelink.database.windows.net'
}
$zone = New-AzPrivateDnsZone @parameters1

## Create dns network link. ##
$parameters2 = @{
    ResourceGroupName = 'CreateSQLEndpointTutorial-rg'
    ZoneName = 'privatelink.database.windows.net'
    Name = 'myLink'
    VirtualNetworkId = $vnet.Id
}
$link = New-AzPrivateDnsVirtualNetworkLink @parameters2

## Create DNS configuration ##
$parameters3 = @{
    Name = 'privatelink.database.windows.net'
    PrivateDnsZoneId = $zone.ResourceId
}
$config = New-AzPrivateDnsZoneConfig @parameters3

## Create DNS zone group. ##
$parameters4 = @{
    ResourceGroupName = 'CreateSQLEndpointTutorial-rg'
    PrivateEndpointName = 'myPrivateEndpoint'
    Name = 'myZoneGroup'
    PrivateDnsZoneConfig = $config
}
New-AzPrivateDnsZoneGroup @parameters4
```

## <a name="test-connectivity-to-private-endpoint"></a>測試對私人端點的連線能力

在本節中，您將使用您在上一個步驟中建立的虛擬機器，連線到私人端點上的 SQL 伺服器。

1. 登入 [Azure 入口網站](https://portal.azure.com) 
 
2. 在左側瀏覽窗格中，選取 [資源群組]。

3. 選取 **CreateSQLEndpointTutorial-rg**。

4. 選取 [myVM]。

5. 在 **myVM** 的 [概觀] 頁面上，選取 [連線] 然後 [堡壘]。

6. 選取藍色 [使用堡壘] 按鈕。

7. 輸入您在虛擬機器建立期間輸入的使用者名稱和密碼。

8. 連線之後，在伺服器上開啟 Windows PowerShell。

9. 輸入 `nslookup <sqlserver-name>.database.windows.net`。 將 **\<sqlserver-name>** 取代為您在先前步驟中建立的 SQL 伺服器名稱。  您會收到類似下面所顯示的訊息：

    ```powershell
    Server:  UnKnown
    Address:  168.63.129.16

    Non-authoritative answer:
    Name:    mysqlserver8675.privatelink.database.windows.net
    Address:  10.0.0.5
    Aliases:  mysqlserver8675.database.windows.net
    ```

    會針對 SQL 伺服器名稱傳回 **10.0.0.5** 的私人 IP 位址。  此位址位於您先前所建立虛擬網路的子網路中。


10. 在 **myVM** 上安裝 [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms?view=sql-server-2017&preserve-view=true)。

11. 開啟 [SQL Server Management Studio]  。

12. 在 [連線至伺服器] 中，輸入或選取這項資訊：

    | 設定 | 值 |
    | ------- | ----- |
    | 伺服器類型 | 選取 [資料庫引擎]。|
    | 伺服器名稱 | 輸入 **\<sql-server-name>.database.windows.net** |
    | 驗證 | 選取 [SQL Server 驗證]。 |
    | 使用者名稱 | 輸入您在伺服器建立期間輸入的使用者名稱 |
    | 密碼 | 輸入您在建立伺服器時輸入的密碼 |
    | 記住密碼 | 選取 [是]。 |

13. 選取 [連接]  。

14. 瀏覽左側功能表中的資料庫。

15. (選擇性) 從 **mysqldatabase** 建立或查詢資訊。

16. 關閉與 **myVM** 的堡壘主機連線。 

## <a name="clean-up-resources"></a>清除資源 
在私人端點、SQL Server 和 VM 使用完畢後，請刪除資源群組及其包含的所有資源： 

1. 在入口網站頂端的 [搜尋] 方塊中，輸入 **CreateSQLEndpointTutorial-rg**，然後從搜尋結果中選取 [CreateSQLEndpointTutorial-rg]。 

2. 選取 [刪除資源群組]。 

3. 針對 [輸入資源群組名稱] 輸入 **CreateSQLEndpointTutorial-rg**，然後選取 [刪除]。

## <a name="next-steps"></a>後續步驟

您已在本教學課程中建立：

* 建立虛擬網路和堡壘主機。
* 虛擬機器。
* 具有私人端點的 Azure SQL 伺服器。

您已使用虛擬機器在私人端點上安全地測試與 SQL 伺服器的連線。

了解如何建立私人連結服務：
> [!div class="nextstepaction"]
> [建立 Private Link 服務](create-private-link-service-portal.md)
