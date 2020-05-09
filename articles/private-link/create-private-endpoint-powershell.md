---
title: 使用 Azure PowerShell 建立 Azure 私用端點 |Microsoft Docs
description: 瞭解 Azure 私人連結
services: private-link
author: malopMSFT
ms.service: private-link
ms.topic: article
ms.date: 09/16/2019
ms.author: allensu
ms.openlocfilehash: 8af33e95c92cf51bdabe3325bd9249b4662b7d28
ms.sourcegitcommit: b9d4b8ace55818fcb8e3aa58d193c03c7f6aa4f1
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/29/2020
ms.locfileid: "82583765"
---
# <a name="create-a-private-endpoint-using-azure-powershell"></a>使用 Azure PowerShell 建立私人端點
私人端點是 Azure 中私人連結的基本要素。 其可讓 Azure 資源 (例如虛擬機器 (VM)) 與私人連結資源進行私密通訊。 

在本快速入門中，您將了解如何使用 Azure PowerShell 在 Azure 虛擬網路上建立 VM，以及建立具有 Azure 私人端點的 SQL Database 伺服器。 然後，您就可以從 VM 安全地存取 SQL Database 伺服器。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-a-resource-group"></a>建立資源群組

建立資源之前，您必須先建立資源群組，以使用[remove-azresourcegroup](/powershell/module/az.resources/new-azresourcegroup)來裝載虛擬網路和私人端點。 下列範例會在*WestUS*位置中建立名為*myResourceGroup*的資源群組：

```azurepowershell

New-AzResourceGroup `
  -ResourceGroupName myResourceGroup `
  -Location westcentralus
```

## <a name="create-a-virtual-network"></a>建立虛擬網路
在本節中，您會建立虛擬網路和子網。 接下來，將子閘道聯至您的虛擬網路。

### <a name="create-a-virtual-network"></a>建立虛擬網路

使用[new-azvirtualnetwork](/powershell/module/az.network/new-azvirtualnetwork)為您的私用端點建立虛擬網路。 下列範例會建立名為*MyVirtualNetwork*的虛擬網路：
 
```azurepowershell

$virtualNetwork = New-AzVirtualNetwork `
  -ResourceGroupName myResourceGroup `
  -Location westcentralus `
  -Name myVirtualNetwork `
  -AddressPrefix 10.0.0.0/16
```

### <a name="add-a-subnet"></a>新增子網

Azure 會將資源部署到虛擬網路內的子網，因此您必須建立子網。 使用[new-azvirtualnetworksubnetconfig](/powershell/module/az.network/add-azvirtualnetworksubnetconfig)建立名為*mySubnet*的子網設定。 下列範例會建立名為*mySubnet*的子網，並將 [私人端點網路原則] 旗標設定為 [**停用**]。

```azurepowershell
$subnetConfig = Add-AzVirtualNetworkSubnetConfig `
  -Name mySubnet `
  -AddressPrefix 10.0.0.0/24 `
  -PrivateEndpointNetworkPoliciesFlag "Disabled" `
  -VirtualNetwork $virtualNetwork
```

> [!CAUTION]
> 您可以輕鬆地將`PrivateEndpointNetworkPoliciesFlag`參數與另一個可用的旗`PrivateLinkServiceNetworkPoliciesFlag`標混淆，因為它們既是長字，而且具有類似的外觀。  請確定您使用的是正確的一個`PrivateEndpointNetworkPoliciesFlag`。

### <a name="associate-the-subnet-to-the-virtual-network"></a>將子閘道聯至虛擬網路

您可以使用[New-azvirtualnetwork 將](/powershell/module/az.network/Set-azVirtualNetwork)子網設定寫入至虛擬網路。 此命令會建立子網路：

```azurepowershell
$virtualNetwork | Set-AzVirtualNetwork
```

## <a name="create-a-virtual-machine"></a>建立虛擬機器

在虛擬網路中使用[update-azvm](/powershell/module/az.compute/new-azvm)建立 VM。 當您執行下一個命令時，系統會提示您輸入認證。 輸入 VM 的使用者名稱和密碼：

```azurepowershell-interactive
New-AzVm `
    -ResourceGroupName "myResourceGroup" `
    -Name "myVm" `
    -Location "westcentralus" `
    -VirtualNetworkName "MyVirtualNetwork" `
    -SubnetName "mySubnet" `
    -SecurityGroupName "myNetworkSecurityGroup" `
    -PublicIpAddressName "myPublicIpAddress" `
    -OpenPorts 80,3389 `
    -AsJob  
```

`-AsJob` 選項會在背景中建立 VM。 您可以繼續進行下一個步驟。

當 Azure 開始在背景中建立 VM 時，您會收到類似下列內容：

```powershell
Id     Name            PSJobTypeName   State         HasMoreData     Location             Command
--     ----            -------------   -----         -----------     --------             -------
1      Long Running... AzureLongRun... Running       True            localhost            New-AzVM
```

## <a name="create-a-sql-database-server"></a>建立 SQL Database 伺服器 

使用 AzSqlServer 命令來建立 SQL Database 伺服器。 請記住，您 SQL Database 伺服器的名稱在整個 Azure 中必須是唯一的，因此請以您自己唯一的值取代括弧中的預留位置值：

```azurepowershell-interactive
$adminSqlLogin = "SqlAdmin"
$password = "ChangeYourAdminPassword1"

$server = New-AzSqlServer -ResourceGroupName "myResourceGroup" `
    -ServerName "myserver" `
    -Location "WestCentralUS" `
    -SqlAdministratorCredentials $(New-Object -TypeName System.Management.Automation.PSCredential -ArgumentList $adminSqlLogin, $(ConvertTo-SecureString -String $password -AsPlainText -Force))

New-AzSqlDatabase  -ResourceGroupName "myResourceGroup" `
    -ServerName "myserver"`
    -DatabaseName "myda"`
    -RequestedServiceObjectiveName "S0" `
    -SampleName "AdventureWorksLT"
```

## <a name="create-a-private-endpoint"></a>建立私人端點

具有[AzPrivateLinkServiceConnection](/powershell/module/az.network/New-AzPrivateLinkServiceConnection)之虛擬網路中 SQL Database 伺服器的私用端點： 

```azurepowershell

$privateEndpointConnection = New-AzPrivateLinkServiceConnection -Name "myConnection" `
  -PrivateLinkServiceId $server.ResourceId `
  -GroupId "sqlServer" 
 
$virtualNetwork = Get-AzVirtualNetwork -ResourceGroupName  "myResourceGroup" -Name "MyVirtualNetwork"  
 
$subnet = $virtualNetwork `
  | Select -ExpandProperty subnets `
  | Where-Object  {$_.Name -eq 'mysubnet'}  
 
$privateEndpoint = New-AzPrivateEndpoint -ResourceGroupName "myResourceGroup" `
  -Name "myPrivateEndpoint" `
  -Location "westcentralus" `
  -Subnet  $subnet `
  -PrivateLinkServiceConnection $privateEndpointConnection
``` 

## <a name="configure-the-private-dns-zone"></a>設定私人 DNS 區域 
建立 SQL Database 伺服器網域的私人 DNS 區域，並建立與虛擬網路的關聯連結： 

```azurepowershell

$zone = New-AzPrivateDnsZone -ResourceGroupName "myResourceGroup" `
  -Name "privatelink.database.windows.net" 
 
$link  = New-AzPrivateDnsVirtualNetworkLink -ResourceGroupName "myResourceGroup" `
  -ZoneName "privatelink.database.windows.net"`
  -Name "mylink" `
  -VirtualNetworkId $virtualNetwork.Id  
 
$networkInterface = Get-AzResource -ResourceId $privateEndpoint.NetworkInterfaces[0].Id -ApiVersion "2019-04-01" 
 
foreach ($ipconfig in $networkInterface.properties.ipConfigurations) { 
foreach ($fqdn in $ipconfig.properties.privateLinkConnectionProperties.fqdns) { 
Write-Host "$($ipconfig.properties.privateIPAddress) $($fqdn)"  
$recordName = $fqdn.split('.',2)[0] 
$dnsZone = $fqdn.split('.',2)[1] 
New-AzPrivateDnsRecordSet -Name $recordName -RecordType A -ZoneName "privatelink.database.windows.net"  `
-ResourceGroupName "myResourceGroup" -Ttl 600 `
-PrivateDnsRecords (New-AzPrivateDnsRecordConfig -IPv4Address $ipconfig.properties.privateIPAddress)  
} 
} 
``` 
  
## <a name="connect-to-a-vm-from-the-internet"></a>從網際網路連線至 VM

請使用 [Get-AzPublicIpAddress](/powershell/module/az.network/Get-AzPublicIpAddress) 來傳回 VM 的公用 IP 位址。 這個範例會傳回*myVM* VM 的公用 IP 位址：

```azurepowershell
Get-AzPublicIpAddress `
  -Name myPublicIpAddress `
  -ResourceGroupName myResourceGroup `
  | Select IpAddress 
```  
在您的本機電腦上開啟命令提示字元。 執行 mstsc 命令。 將 <publicIpAddress> 取代為先前步驟傳回的公用 IP 位址： 


> [!NOTE]
> 如果您已在本機電腦上的 PowerShell 提示字元中執行這些命令，且您所使用的 Az PowerShell 模組為 1.0 版或更新版本，您可以在該介面中繼續進行。
```
mstsc /v:<publicIpAddress>
```

1. 如果出現提示，請選取 [連接]  。 
2. 輸入您在建立 VM 時指定的使用者名稱和密碼。
  > [!NOTE]
  > 您可能需要選取 [更多選擇] > [使用不同的帳戶]，以指定您在建立 VM 時輸入的認證。 
  
3. 選取 [確定]  。 
4. 您可能會收到憑證警告。 如果如此，請選取 [是]**** 或 [繼續]****。 

## <a name="access-sql-database-server-privately-from-the-vm"></a>從 VM 私下存取 SQL Database 伺服器

1. 在 myVm  的遠端桌面中，開啟 PowerShell。
2. 輸入 `nslookup myserver.database.windows.net`。 請記得將`myserver`取代為您的 SQL server 名稱。

    您將收到如下訊息：
    
    ```azurepowershell
    Server:  UnKnown
    Address:  168.63.129.16
    Non-authoritative answer:
    Name:    myserver.privatelink.database.windows.net
    Address:  10.0.0.5
    Aliases:   myserver.database.windows.net
    ```
    
3. 安裝 SQL Server Management Studio。
4. 在 [連線至伺服器]  中，輸入或選取這項資訊：

    | 設定 | 值 |
    | --- | --- |
    | 伺服器類型 | Database Engine |
    | 伺服器名稱 | myserver.database.windows.net |
    | 使用者名稱 | 輸入建立期間提供的使用者名稱 |
    | 密碼 | 輸入在建立期間提供的密碼 |
    | 記住密碼 | 是 |
    
5. 選取 [連接]  。
6. 流覽左側功能表中的 [**資料庫**]。 
7. (選擇性) 從 mydatabase 建立或查詢資訊。
8. 關閉對*myVM*的遠端桌面連線。 

## <a name="clean-up-resources"></a>清除資源 
當您使用私用端點（SQL Database 伺服器和 VM）完成時，請使用[remove-azresourcegroup](/powershell/module/az.resources/remove-azresourcegroup)移除資源群組及其擁有的所有資源：

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>後續步驟
- 深入了解 [Azure Private Link](private-link-overview.md)
