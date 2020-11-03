---
title: 快速入門-使用 Azure PowerShell 建立 Azure 私人端點
description: 您可以使用本快速入門來瞭解如何使用 Azure PowerShell 來建立私人端點。
services: private-link
author: asudbring
ms.service: private-link
ms.topic: how-to
ms.date: 11/02/2020
ms.author: allensu
ms.openlocfilehash: 147e646738df9d70355f379a9e64a52116e9f16f
ms.sourcegitcommit: bbd66b477d0c8cb9adf967606a2df97176f6460b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/03/2020
ms.locfileid: "93233588"
---
# <a name="quickstart-create-a-private-endpoint-using-azure-powershell"></a>快速入門：使用 Azure PowerShell 建立私人端點

藉由使用私人端點來安全地連線到 Azure Web 應用程式，以開始使用 Azure Private Link。

在本快速入門中，您將建立 Azure Web 應用程式的私人端點，並部署虛擬機器來測試私人連線。  

您可以針對不同類型的 Azure 服務 (例如，Azure SQL 和 Azure 儲存體) 建立私人端點。

## <a name="prerequisites"></a>必要條件

* 具有有效訂用帳戶的 Azure 帳戶。 [免費建立帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
* 在 Azure 訂用帳戶中部署具有 **PremiumV2 層** 或更高層級 App Service 方案的 Azure Web 應用程式。  
    * 如需詳細資訊和範例，請參閱[快速入門：在 Azure 中建立 ASP.NET Core Web 應用程式](../app-service/quickstart-dotnetcore.md)。 
    * 如需關於建立 Web 應用程式和端點的詳細教學課程，請參閱[教學課程：使用 Azure 私人端點連線至 Web 應用程式](tutorial-private-endpoint-webapp-portal.md)。

如果您選擇在本機安裝和使用 PowerShell，本文會要求使用 Azure PowerShell 模組版本 5.4.1 或更新版本。 執行 `Get-Module -ListAvailable Az` 來了解安裝的版本。 如果您需要升級，請參閱[安裝 Azure PowerShell 模組](/powershell/azure/install-Az-ps)。 如果您在本機執行 PowerShell，則也需要執行 `Connect-AzAccount` 以建立與 Azure 的連線。

## <a name="create-a-resource-group"></a>建立資源群組

Azure 資源群組是在其中部署與管理 Azure 資源的邏輯容器。

使用 [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) 來建立資源群組：

```azurepowershell-interactive
New-AzResourceGroup -Name 'CreatePrivateEndpointQS-rg' -Location 'eastus'
```

## <a name="create-a-virtual-network-and-bastion-host"></a>建立虛擬網路和堡壘主機

在本節中，您會建立虛擬網路、子網路和堡壘主機。 

堡壘主機將用來安全地連線到虛擬機器，以測試私人端點。

使用下列方式建立虛擬網路和堡壘主機：

* [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork)
* [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress)
* [新 AzBastion](/powershell/module/az.network/new-azbastion)

```azurepowershell-interactive
## Create backend subnet config. ##
$subnetConfig = New-AzVirtualNetworkSubnetConfig -Name myBackendSubnet -AddressPrefix 10.0.0.0/24

## Create Azure Bastion subnet. ##
$bastsubnetConfig = New-AzVirtualNetworkSubnetConfig -Name AzureBastionSubnet -AddressPrefix 10.0.1.0/24

## Create the virtual network. ##
$parameters1 = @{
    Name = 'MyVNet'
    ResourceGroupName = 'CreatePrivateEndpointQS-rg'
    Location = 'eastus'
    AddressPrefix = '10.0.0.0/16'
    Subnet = $subnetConfig, $bastsubnetConfig
}
$vnet = New-AzVirtualNetwork @parameters1

## Create public IP address for bastion host. ##
$parameters2 = @{
    Name = 'myBastionIP'
    ResourceGroupName = 'CreatePrivateEndpointQS-rg'
    Location = 'eastus'
    Sku = 'Standard'
    AllocationMethod = 'Static'
}
$publicip = New-AzPublicIpAddress @parameters2

## Create bastion host ##
$parameters3 = @{
    ResourceGroupName = 'CreatePrivateEndpointQS-rg'
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
$vnet = Get-AzVirtualNetwork -Name myVNet -ResourceGroupName CreatePrivateEndpointQS-rg

## Command to create network interface for VM ##
$parameters1 = @{
    Name = 'myNicVM'
    ResourceGroupName = 'CreatePrivateEndpointQS-rg'
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
New-AzVM -ResourceGroupName 'CreatePrivateEndpointQS-rg' -Location 'eastus' -VM $vmConfig
```

## <a name="create-private-endpoint"></a>建立私人端點

在本節中，您將使用下列各項來建立私人端點和連接：

* [New-AzPrivateLinkServiceConnection](/powershell/module/az.network/New-AzPrivateLinkServiceConnection)
* [New-AzPrivateEndpoint](/powershell/module/az.network/new-azprivateendpoint)

```azurepowershell-interactive
## Place web app into variable. Replace <your-webapp-name> with your server name ##
$webapp = Get-AzWebApp -ResourceGroupName CreatePrivateEndpointQS-rg -Name <your-webapp-name>

## Create private endpoint connection. ##
$parameters1 = @{
    Name = 'myConnection'
    PrivateLinkServiceId = $webapp.ID
    GroupID = 'sites'
}
$privateEndpointConnection = New-AzPrivateLinkServiceConnection @parameters1

## Place virtual network into variable. ##
$vnet = Get-AzVirtualNetwork -ResourceGroupName 'CreatePrivateEndpointQS-rg' -Name 'myVNet'

## Disable private endpoint network policy ##
$vnet.Subnets[0].PrivateEndpointNetworkPolicies = "Disabled"
$vnet | Set-AzVirtualNetwork

## Create private endpoint
$parameters2 = @{
    ResourceGroupName = 'CreatePrivateEndpointQS-rg'
    Name = 'myPrivateEndpoint'
    Location = 'eastus'
    Subnet = $vnet.Subnets[0]
    PrivateLinkServiceConnection = $privateEndpointConnection
}
New-AzPrivateEndpoint @parameters2
```
## <a name="configure-the-private-dns-zone"></a>設定私人 DNS 區域

在本節中，您將使用下列方式來建立和設定私人 DNS 區域：

* [New-AzPrivateDnsZone](/powershell/module/az.privatedns/new-azprivatednszone)
* [New-AzPrivateDnsVirtualNetworkLink](/powershell/module/az.privatedns/new-azprivatednsvirtualnetworklink)
* [New-AzPrivateDnsZoneConfig](/powershell/module/az.network/new-azprivatednszoneconfig)
* [New-AzPrivateDnsZoneGroup](/powershell/module/az.network/new-azprivatednszonegroup)

```azurepowershell-interactive
## Place virtual network into variable. ##
$vnet = Get-AzVirtualNetwork -ResourceGroupName 'CreatePrivateEndpointQS-rg' -Name 'myVNet'

## Create private dns zone. ##
$parameters1 = @{
    ResourceGroupName = 'CreatePrivateEndpointQS-rg'
    Name = 'privatelink.azurewebsites.net'
}
$zone = New-AzPrivateDnsZone @parameters1

## Create dns network link. ##
$parameters2 = @{
    ResourceGroupName = 'CreatePrivateEndpointQS-rg'
    ZoneName = 'privatelink.azurewebsites.net'
    Name = 'myLink'
    VirtualNetworkId = $vnet.Id
}
$link = New-AzPrivateDnsVirtualNetworkLink @parameters2

## Create DNS configuration ##
$parameters3 = @{
    Name = 'privatelink.azurewebsites.net'
    PrivateDnsZoneId = $zone.ResourceId
}
$config = New-AzPrivateDnsZoneConfig @parameters3

## Create DNS zone group. ##
$parameters4 = @{
    ResourceGroupName = 'CreatePrivateEndpointQS-rg'
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

3. 選取 [CreatePrivateEndpointQS-rg]。

4. 選取 [myVM]  。

5. 在 **myVM** 的 [概觀] 頁面上，選取 [連線] 然後 [堡壘]。

6. 選取藍色 [使用堡壘] 按鈕。

7. 輸入您在虛擬機器建立期間輸入的使用者名稱和密碼。

8. 連線之後，在伺服器上開啟 Windows PowerShell。

9. 輸入 `nslookup <your-webapp-name>.azurewebsites.net`。 以您在上一個步驟中建立的 Web 應用程式名稱取代 **\<your-webapp-name>** 。  您會收到類似下面所顯示的訊息：

    ```powershell
    Server:  UnKnown
    Address:  168.63.129.16

    Non-authoritative answer:
    Name:    mywebapp8675.privatelink.azurewebsites.net
    Address:  10.0.0.5
    Aliases:  mywebapp8675.azurewebsites.net
    ```

    針對 web 應用程式名稱，會傳回 **10.0.0.5** 的私人 IP 位址。  此位址位於您先前所建立虛擬網路的子網路中。

10. 在與 **myVM** 的堡壘連線中，開啟 Internet Explorer。

11. 輸入 Web 應用程式的 URL： **https://\<your-webapp-name>.azurewebsites.net** .。

12. 如果您尚未部署應用程式，則會收到預設的 Web 應用程式頁面：

    :::image type="content" source="./media/create-private-endpoint-portal/web-app-default-page.png" alt-text="預設 Web 應用程式頁面。" border="true":::

13. 關閉與 **myVM** 的連線。

## <a name="clean-up-resources"></a>清除資源 
當您完成使用私人端點和 VM 時，請使用 [>new-azresourcegroup](/powershell/module/az.resources/remove-azresourcegroup) 來移除資源群組及其所有資源：

```azurepowershell-interactive
Remove-AzResourceGroup -Name CreatePrivateEndpointQS-rg -Force
```

## <a name="next-steps"></a>後續步驟

在本快速入門中，您已建立一個：

* 建立虛擬網路和堡壘主機。
* 虛擬機器。
* Azure Web 應用程式的私人端點。

您已使用虛擬機器在私人端點上安全地測試與 Web 應用程式的連線。

若要深入了解可支援私人端點的服務，請參閱：
> [!div class="nextstepaction"]
> [Private Link 可用性](private-link-overview.md#availability)
