---
title: 建立虛擬網路 -快速入門 - Azure PowerShell
titlesuffix: Azure Virtual Network
description: 在此快速入門中，您將了解如何使用 Azure 入口網站建立虛擬網路。 虛擬網路可讓 Azure 資源 (例如虛擬機器) 彼此及與網際網路進行私密通訊。
services: virtual-network
documentationcenter: virtual-network
author: KumudD
tags: azure-resource-manager
Customer intent: I want to create a virtual network so that virtual machines can communicate with privately with each other and with the internet.
ms.service: virtual-network
ms.devlang: ''
ms.topic: quickstart
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure
ms.date: 12/04/2018
ms.author: kumud
ms.openlocfilehash: 1d30b35264707c59c899cc3a224e4affa2a4696e
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/29/2020
ms.locfileid: "79290226"
---
# <a name="quickstart-create-a-virtual-network-using-powershell"></a>快速入門：使用 PowerShell 建立虛擬網路

虛擬網路可讓 Azure 資源 (例如虛擬機器 (VM)) 彼此及與網際網路進行私密通訊。 在此快速入門中，您將了解如何建立虛擬網路。 建立虛擬網路之後，您需將兩個 VM 部署至虛擬網路中。 接著，您需從網際網路連線至其中一個 VM，然後透過虛擬網路進行私密通訊。

如果您沒有 Azure 訂用帳戶，請立即建立[免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

如果您決定在本機安裝和使用 PowerShell，則在進行此快速入門時，您必須使用 Azure PowerShell 模組 1.0.0 版或更新版本。 若要尋找已安裝的版本，請執行 `Get-Module -ListAvailable Az`。 如需安裝和升級的資訊，請參閱[安裝 Azure PowerShell 模組](/powershell/azure/install-az-ps)。

最後，如果您在本機執行 PowerShell，也需要執行 `Connect-AzAccount`。 該命令會建立與 Azure 的連線。

## <a name="create-a-resource-group-and-a-virtual-network"></a>建立資源群組和虛擬網路

若要設定資源群組和虛擬網路，您必須逐步執行幾個實用的步驟。

### <a name="create-the-resource-group"></a>建立資源群組

建立虛擬網路之前，您必須先建立資源群組來裝載虛擬網路。 使用 [New-AzResourceGroup](/powershell/module/az.Resources/New-azResourceGroup) 來建立資源群組。 此範例會在 *eastus* 位置中建立名為 *myResourceGroup* 的資源群組：

```azurepowershell-interactive
New-AzResourceGroup -Name myResourceGroup -Location EastUS
```

### <a name="create-the-virtual-network"></a>建立虛擬網路

使用 [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork) 建立虛擬網路。 此範例會在 *EastUS* 位置建立名為 *myVirtualNetwork* 的預設虛擬網路：

```azurepowershell-interactive
$virtualNetwork = New-AzVirtualNetwork `
  -ResourceGroupName myResourceGroup `
  -Location EastUS `
  -Name myVirtualNetwork `
  -AddressPrefix 10.0.0.0/16
```

### <a name="add-a-subnet"></a>新增子網路

Azure 會將資源部署至虛擬網路內的子網路，因此您必須建立子網路。 使用 [Add-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/add-azvirtualnetworksubnetconfig) 建立名為 *default* 的子網路設定：

```azurepowershell-interactive
$subnetConfig = Add-AzVirtualNetworkSubnetConfig `
  -Name default `
  -AddressPrefix 10.0.0.0/24 `
  -VirtualNetwork $virtualNetwork
```

### <a name="associate-the-subnet-to-the-virtual-network"></a>將子網路與虛擬網路建立關聯

您可以使用 [Set-AzVirtualNetwork](/powershell/module/az.network/Set-azVirtualNetwork)，將子網路設定寫入至虛擬網路。 此命令會建立子網路：

```azurepowershell-interactive
$virtualNetwork | Set-AzVirtualNetwork
```

## <a name="create-virtual-machines"></a>建立虛擬機器

在虛擬網路內建立兩個 VM。

### <a name="create-the-first-vm"></a>建立第一個 VM

使用 [New-AzVM](/powershell/module/az.compute/new-azvm) 建立第一個 VM。 當您執行下一個命令時，系統會提示您輸入認證。 輸入 VM 的使用者名稱和密碼：

```azurepowershell-interactive
New-AzVm `
    -ResourceGroupName "myResourceGroup" `
    -Location "East US" `
    -VirtualNetworkName "myVirtualNetwork" `
    -SubnetName "default" `
    -Name "myVm1" `
    -AsJob
```

`-AsJob` 選項會在背景中建立 VM。 您可以繼續進行下一個步驟。

當 Azure 開始在背景中建立 VM 時，您會收到類似下列內容：

```powershell
Id     Name            PSJobTypeName   State         HasMoreData     Location             Command
--     ----            -------------   -----         -----------     --------             -------
1      Long Running... AzureLongRun... Running       True            localhost            New-AzVM
```

### <a name="create-the-second-vm"></a>建立第二個 VM

使用此命令建立第二個 VM：

```azurepowershell-interactive
New-AzVm `
  -ResourceGroupName "myResourceGroup" `
  -VirtualNetworkName "myVirtualNetwork" `
  -SubnetName "default" `
  -Name "myVm2"
```

您必須建立另一個使用者和密碼。 Azure 需要幾分鐘的時間來建立 VM。

> [!IMPORTANT]
> 在 Azure 完成前，請勿繼續進行下一個步驟。  當它傳回輸出至 PowerShell 時，您將會知道已經完成。

## <a name="connect-to-a-vm-from-the-internet"></a>從網際網路連線至 VM

請使用 [Get-AzPublicIpAddress](/powershell/module/az.network/get-azpublicipaddress) 來傳回 VM 的公用 IP 位址。 此範例會傳回 *myVm1* VM 的公用 IP 位址：

```azurepowershell-interactive
Get-AzPublicIpAddress `
  -Name myVm1 `
  -ResourceGroupName myResourceGroup `
  | Select IpAddress
```

在您的本機電腦上開啟命令提示字元。 執行 `mstsc` 命令。 將 `<publicIpAddress>` 取代為先前步驟傳回的公用 IP 位址：

> [!NOTE]
> 如果您已在本機電腦上的 PowerShell 提示字元中執行這些命令，且您所使用的 Az PowerShell 模組為 1.0 版或更新版本，您可以在該介面中繼續進行。

```cmd
mstsc /v:<publicIpAddress>
```
1. 如果出現提示，請選取 [連接]  。

1. 輸入您在建立 VM 時指定的使用者名稱和密碼。

    > [!NOTE]
    > 您可能需要選取 [其他選擇]   > [使用不同的帳戶]  ，以指定您在建立 VM 時輸入的認證。

1. 選取 [確定]  。

1. 您可能會收到憑證警告。 如果如此，請選取 [是]  或 [繼續]  。

## <a name="communicate-between-vms"></a>虛擬機器之間的通訊

1. 在 *myVm1* 的遠端桌面中，開啟 PowerShell。

1. 輸入 `ping myVm2`。

    您會得到類似此內容：

    ```powershell
    PS C:\Users\myVm1> ping myVm2

    Pinging myVm2.ovvzzdcazhbu5iczfvonhg2zrb.bx.internal.cloudapp.net
    Request timed out.
    Request timed out.
    Request timed out.
    Request timed out.

    Ping statistics for 10.0.0.5:
        Packets: Sent = 4, Received = 0, Lost = 4 (100% loss),
    ```

    Ping 失敗，因為它使用網際網路控制訊息通訊協定 (ICMP)。 根據預設，ICMP 不允許通過 Windows 防火牆。

1. 為了讓 *myVm2* 在稍後的步驟中可以 Ping *myVm1*，輸入此命令：

    ```powershell
    New-NetFirewallRule –DisplayName "Allow ICMPv4-In" –Protocol ICMPv4
    ```

    該命令可讓 ICMP 輸入通過 Windows 防火牆。

1. 關閉對 *myVm1* 的遠端桌面連線。

1. 重複[從網際網路連線至 VM](#connect-to-a-vm-from-the-internet) 中的步驟。 這一次，連線到 *myVm2*。

1. 從 *myVm2* VM 上的命令提示字元中，輸入 `ping myvm1`。

    您會得到類似此內容：

    ```cmd
    C:\windows\system32>ping myVm1

    Pinging myVm1.e5p2dibbrqtejhq04lqrusvd4g.bx.internal.cloudapp.net [10.0.0.4] with 32 bytes of data:
    Reply from 10.0.0.4: bytes=32 time=2ms TTL=128
    Reply from 10.0.0.4: bytes=32 time<1ms TTL=128
    Reply from 10.0.0.4: bytes=32 time<1ms TTL=128
    Reply from 10.0.0.4: bytes=32 time<1ms TTL=128

    Ping statistics for 10.0.0.4:
        Packets: Sent = 4, Received = 4, Lost = 0 (0% loss),
    Approximate round trip times in milli-seconds:
        Minimum = 0ms, Maximum = 2ms, Average = 0ms
    ```

    您會從 *myVm1* 收到回覆，因為您在先前的步驟中允許 ICMP 通過 *myVm1* VM 上的 Windows 防火牆。

1. 關閉對 *myVm2* 的遠端桌面連線。

## <a name="clean-up-resources"></a>清除資源

當您完成使用虛擬網路和 VM 時，使用 [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) 來移除資源群組以及其具有的所有資源：

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>後續步驟

在此快速入門中，您建立了一個預設的虛擬網路和兩個 VM。 您從網際網路連線至其中一個 VM，然後在該 VM 與另一個 VM 進行私密通訊。 若要深入了解虛擬網路設定，請參閱[管理虛擬網路](manage-virtual-network.md)。

Azure 允許虛擬機器之間進行無限制的私密通訊。 根據預設，Azure 只允許從網際網路連線到 Windows VM 的輸入遠端桌面連線。 若要深入了解設定不同類型的 VM 網路通訊，請移至[篩選網路流量](tutorial-filter-network-traffic.md)教學課程。
