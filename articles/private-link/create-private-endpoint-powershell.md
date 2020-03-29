---
title: 使用 Azure PowerShell 創建 Azure 專用終結點*微軟文檔
description: 瞭解 Azure 專用連結
services: private-link
author: malopMSFT
ms.service: private-link
ms.topic: article
ms.date: 09/16/2019
ms.author: allensu
ms.openlocfilehash: 60032677594537f1e7791b7108eebd5d4cfad5b4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "75430323"
---
# <a name="create-a-private-endpoint-using-azure-powershell"></a>使用 Azure PowerShell 創建專用終結點
私人端點是 Azure 中私人連結的基本要素。 其可讓 Azure 資源 (例如虛擬機器 (VM)) 與私人連結資源進行私密通訊。 

在本快速入門中，您將了解如何使用 Azure PowerShell 在 Azure 虛擬網路上建立 VM，以及建立具有 Azure 私人端點的 SQL Database 伺服器。 然後，您就可以從 VM 安全地存取 SQL Database 伺服器。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-a-resource-group"></a>建立資源群組

在創建資源之前，必須創建一個資源組，該資源組承載虛擬網路和具有[New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup)的專用終結點。 下面的示例在*WestUS*位置創建名為*myResourceGroup*的資源組：

```azurepowershell

New-AzResourceGroup `
  -ResourceGroupName myResourceGroup `
  -Location westcentralus
```

## <a name="create-a-virtual-network"></a>建立虛擬網路
在本節中，您將創建一個虛擬網路和一個子網。 接下來，您將子閘道聯到虛擬網路。

### <a name="create-a-virtual-network"></a>建立虛擬網路

使用[New-Az虛擬網路](/powershell/module/az.network/new-azvirtualnetwork)為您的專用終結點創建虛擬網路。 下面的示例創建名為*MyVirtualNetwork 的虛擬網路*：
 
```azurepowershell

$virtualNetwork = New-AzVirtualNetwork `
  -ResourceGroupName myResourceGroup `
  -Location westcentralus `
  -Name myVirtualNetwork `
  -AddressPrefix 10.0.0.0/16
```

### <a name="add-a-subnet"></a>添加子網

Azure 將資源部署到虛擬網路中的子網，因此您需要創建子網。 使用[Add-Az 虛擬網路子網配置創建](/powershell/module/az.network/add-azvirtualnetworksubnetconfig)名為*mySubnet*的子網配置。 下面的示例創建一個名為*mySubnet*的子網，其中專用終結點網路原則標誌設置為 **"已禁用**"。

```azurepowershell
$subnetConfig = Add-AzVirtualNetworkSubnetConfig `
  -Name mySubnet `
  -AddressPrefix 10.0.0.0/24 `
  -PrivateEndpointNetworkPoliciesFlag "Disabled" `
  -VirtualNetwork $virtualNetwork
```

> [!CAUTION]
> 很容易將`PrivateEndpointNetworkPoliciesFlag`參數與另一個可用標誌混淆，`PrivateLinkServiceNetworkPoliciesFlag`因為它們都是長字，外觀相似。  請確保您使用的是正確的。 `PrivateEndpointNetworkPoliciesFlag`

### <a name="associate-the-subnet-to-the-virtual-network"></a>將子閘道聯到虛擬網路

您可以使用[Set-Az虛擬網路](/powershell/module/az.network/Set-azVirtualNetwork)將子網配置寫入虛擬網路。 此命令會建立子網路：

```azurepowershell
$virtualNetwork | Set-AzVirtualNetwork
```

## <a name="create-a-virtual-machine"></a>建立虛擬機器

使用[新 AzVM](/powershell/module/az.compute/new-azvm)在虛擬網路中創建 VM。 當您執行下一個命令時，系統會提示您輸入認證。 輸入 VM 的使用者名稱和密碼：

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

使用 New-AzSqlServer 命令創建 SQL 資料庫伺服器。 請記住，SQL 資料庫伺服器的名稱必須在 Azure 中是唯一的，因此請將括弧中的預留位置值替換為您自己的唯一值：

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

具有[新-AzPrivateLinkService連接](/powershell/module/az.network/New-AzPrivateLinkServiceConnection)的虛擬網路中 SQL 資料庫伺服器的專用終結點 ： 

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
  -Subnet  $subnet`
  -PrivateLinkServiceConnection $privateEndpointConnection
``` 

## <a name="configure-the-private-dns-zone"></a>設定私人 DNS 區域 
為 SQL 資料庫伺服器域創建專用 DNS 區域，並與虛擬網路創建關聯連結： 

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

請使用 [Get-AzPublicIpAddress](/powershell/module/az.network/Get-AzPublicIpAddress) 來傳回 VM 的公用 IP 位址。 此示例返回*myVM VM*的公共 IP 位址：

```azurepowershell
Get-AzPublicIpAddress `
  -Name myPublicIpAddress `
  -ResourceGroupName myResourceGroup `
  | Select IpAddress 
```  
在您的本機電腦上開啟命令提示字元。 運行 mstsc 命令。 將 <publicIpAddress> 取代為先前步驟傳回的公用 IP 位址： 


> [!NOTE]
> 如果您已在本機電腦上的 PowerShell 提示字元中執行這些命令，且您所使用的 Az PowerShell 模組為 1.0 版或更新版本，您可以在該介面中繼續進行。
```
mstsc /v:<publicIpAddress>
```

1. 如果出現提示，請選取 [連接]****。 
2. 輸入您在建立 VM 時指定的使用者名稱和密碼。
  > [!NOTE]
  > 您可能需要選擇更多選項>使用其他帳戶，以指定創建 VM 時輸入的憑據。 
  
3. 選取 [確定]****。 
4. 您可能會收到憑證警告。 如果如此，請選取 [是]**** 或 [繼續]****。 

## <a name="access-sql-database-server-privately-from-the-vm"></a>從 VM 私下存取 SQL Database 伺服器

1. 在 myVm 的遠端桌面中，開啟 PowerShell。
2. 輸入 `nslookup myserver.database.windows.net`。 

    您將收到如下訊息：
    ```azurepowershell
    Server:  UnKnown
    Address:  168.63.129.16
    Non-authoritative answer:
    Name:    myserver.privatelink.database.windows.net
    Address:  10.0.0.5
    Aliases:   myserver.database.windows.net
    ```
3. 安裝 SQL Server Management Studio
4. 在"連接到伺服器"中，輸入或選擇此資訊：設置值伺服器類型選擇資料庫引擎。
      伺服器名稱 選擇myserver.database.windows.net使用者名 輸入創建期間提供的使用者名。
      密碼 輸入創建過程中提供的密碼。
      記住密碼 選擇"是"。
5. 選取 [連接]。
6. 從左側功能表流覽資料庫。 
7. （可選）從我的資料庫創建或查詢資訊
8. 關閉遠端桌面連線到*myVM。* 

## <a name="clean-up-resources"></a>清除資源 
使用專用終結點 SQL 資料庫伺服器和 VM 完成後，請使用[刪除 AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup)刪除資源組及其擁有的所有資源：

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>後續步驟
- 深入了解 [Azure Private Link](private-link-overview.md)
