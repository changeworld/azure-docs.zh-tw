---
title: 教學課程 - 在 Azure 中建立執行 SQL、IIS、.NET 堆疊的 VM
description: 在本教學課程中，您將學習如何在 Azure 中的 Windows 虛擬機器上安裝 Azure SQL、IIS、.NET 堆疊。
author: cynthn
ms.service: virtual-machines-windows
ms.topic: tutorial
ms.workload: infrastructure
ms.date: 12/05/2018
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: 1c53194bd345c18ac582acd538f1e8f8e1e34d54
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/23/2020
ms.locfileid: "87027847"
---
# <a name="tutorial-install-the-sql-iis-net-stack-in-a-windows-vm-with-azure-powershell"></a>教學課程：使用 Azure PowerShell 在 Windows VM 中安裝 SQL、IIS、.NET 堆疊

在本教學課程中，我們會使用 Azure PowerShell 來安裝 SQL、IIS、.NET 堆疊。 此堆疊是由兩個執行 Windows Server 2016 的 VM 所組成，一個包含 IIS 和 .NET 而另一個則是包含 SQL Server。

> [!div class="checklist"]
> * 建立 VM 
> * 在 VM 上安裝 IIS 和 .NET Core SDK
> * 建立執行 SQL Server 的 VM
> * 安裝 SQL Server 擴充功能

## <a name="launch-azure-cloud-shell"></a>啟動 Azure Cloud Shell

Azure Cloud Shell 是免費的互動式 Shell，可讓您用來執行本文中的步驟。 它具有預先安裝和設定的共用 Azure 工具，可與您的帳戶搭配使用。 

若要開啟 Cloud Shell，只要選取程式碼區塊右上角的 [試試看]  即可。 您也可以移至 [https://shell.azure.com/powershell](https://shell.azure.com/powershell)，從另一個瀏覽器索引標籤啟動 Cloud Shell。 選取 [複製]  即可複製程式碼區塊，將它貼到 Cloud Shell 中，然後按 enter 鍵加以執行。

## <a name="create-an-iis-vm"></a>建立 IIS VM 

在此範例中，我們會使用 PowerShell Cloud Shell 中的 [New-AzVM](/powershell/module/az.compute/new-azvm) Cmdlet 快速建立 Windows Server 2016 VM，然後再安裝 IIS 和 .NET Framework。 IIS 和 SQL VM 會共用資源群組和虛擬網路，因此我們要建立那些名稱的變數。


```azurepowershell-interactive
$vmName = "IISVM"
$vNetName = "myIISSQLvNet"
$resourceGroup = "myIISSQLGroup"
New-AzVm `
    -ResourceGroupName $resourceGroup `
    -Name $vmName `
    -Location "East US" `
    -VirtualNetworkName $vNetName `
    -SubnetName "myIISSubnet" `
    -SecurityGroupName "myNetworkSecurityGroup" `
    -AddressPrefix 192.168.0.0/16 `
    -PublicIpAddressName "myIISPublicIpAddress" `
    -OpenPorts 80,3389 
```

使用自訂指令碼擴充功能和 [Set-AzVMExtension](/powershell/module/az.compute/set-azvmextension) Cmdlet 來安裝 IIS 和 .NET Framework。

```azurepowershell-interactive
Set-AzVMExtension `
    -ResourceGroupName $resourceGroup `
    -ExtensionName IIS `
    -VMName $vmName `
    -Publisher Microsoft.Compute `
    -ExtensionType CustomScriptExtension `
    -TypeHandlerVersion 1.4 `
    -SettingString '{"commandToExecute":"powershell Add-WindowsFeature Web-Server,Web-Asp-Net45,NET-Framework-Features"}' `
    -Location EastUS
```

## <a name="create-another-subnet"></a>建立另一個子網路

為 SQL VM 建立第二個子網路。 使用 [Get-AzVirtualNetwork]{/powershell/module/az.network/get-azvirtualnetwork} 取得 vNet。

```azurepowershell-interactive
$vNet = Get-AzVirtualNetwork `
   -Name $vNetName `
   -ResourceGroupName $resourceGroup
```

使用 [Add-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/add-azvirtualnetworksubnetconfig) 建立子網路的設定。


```azurepowershell-interactive
Add-AzVirtualNetworkSubnetConfig `
   -AddressPrefix 192.168.0.0/24 `
   -Name mySQLSubnet `
   -VirtualNetwork $vNet `
   -ServiceEndpoint Microsoft.Sql
```

使用 [Set-AzVirtualNetwork](/powershell/module/az.network/set-azvirtualnetwork) 將 vNet 更新為新的子網路資訊
   
```azurepowershell-interactive   
$vNet | Set-AzVirtualNetwork
```

## <a name="azure-sql-vm"></a>Azure SQL VM

使用 SQL Server 的預先設定 Azure Marketplace 映像來建立 SQL VM。 我們會先建立 VM，然後在 VM 上安裝 SQL Server 擴充功能。 


```azurepowershell-interactive
New-AzVm `
    -ResourceGroupName $resourceGroup `
    -Name "mySQLVM" `
    -ImageName "MicrosoftSQLServer:SQL2016SP1-WS2016:Enterprise:latest" `
    -Location eastus `
    -VirtualNetworkName $vNetName `
    -SubnetName "mySQLSubnet" `
    -SecurityGroupName "myNetworkSecurityGroup" `
    -PublicIpAddressName "mySQLPublicIpAddress" `
    -OpenPorts 3389,1401 
```

使用 [Set-AzVMSqlServerExtension](/powershell/module/az.compute/set-azvmsqlserverextension) 將 [SQL Server 擴充功能](../../azure-sql/virtual-machines/windows/sql-server-iaas-agent-extension-automate-management.md)新增至 SQL VM。

```azurepowershell-interactive
Set-AzVMSqlServerExtension `
   -ResourceGroupName $resourceGroup  `
   -VMName mySQLVM `
   -Name "SQLExtension" `
   -Location "EastUS"
```

## <a name="next-steps"></a>後續步驟

在本教學課程中，您會使用 Azure PowerShell 來安裝 SQL&#92;IIS&#92;.NET 堆疊。 您已了解如何︰

> [!div class="checklist"]
> * 建立 VM 
> * 在 VM 上安裝 IIS 和 .NET Core SDK
> * 建立執行 SQL Server 的 VM
> * 安裝 SQL Server 擴充功能

前進到下一個教學課程，以了解如何使用 TLS/SSL 憑證保護 IIS Web 伺服器。

> [!div class="nextstepaction"]
> [使用 TLS/SSL 憑證保護 IIS Web 伺服器](tutorial-secure-web-server.md)
