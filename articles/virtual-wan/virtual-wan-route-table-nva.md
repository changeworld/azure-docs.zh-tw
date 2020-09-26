---
title: 虛擬 WAN：建立虛擬中樞路由表以 NVA： Azure PowerShell
description: 虛擬 WAN 虛擬中樞路由表可將流量引導到網路虛擬設備。
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 09/22/2020
ms.author: cherylmc
Customer intent: As someone with a networking background, I want to work with routing tables for NVA.
ms.openlocfilehash: 40154a9c3cefed69bd3f1639153099b944da79b5
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/25/2020
ms.locfileid: "91267120"
---
# <a name="create-a-virtual-hub-route-table-to-steer-traffic-to-a-network-virtual-appliance"></a>建立虛擬中樞路由表以將流量引導到網路虛擬設備

這篇文章會示範如何將流量從虛擬中樞引導到網路虛擬設備。 

![虛擬 WAN 的圖表](./media/virtual-wan-route-table-nva/vwanroute.png)

在本文中，您將了解如何：

* 建立 WAN
* 建立中樞
* 建立中樞虛擬網路連線
* 建立中樞路由
* 建立路由表
* 套用路由表

## <a name="before-you-begin"></a>開始之前

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

請確認您已符合下列條件：

* 您有網路虛擬設備 (NVA)。 這是您選擇的協力廠商軟體，通常是從虛擬網路中的 Azure Marketplace 布建。
* 您有一個指派給 NVA 網路介面的私人 IP。 
* 無法在虛擬中樞中部署 NVA。 它必須部署在不同的 VNet。 在本文中，NVA VNet 稱為 'DMZ VNet'。
* 'DMZ VNet' 可以有一或多個虛擬網路與其連接。 在本文中，此 VNet 是稱為「間接輪幅 VNet」。 這些 Vnet 可以使用 VNet 對等互連連接到 DMZ VNet。
* 請確認您已建立 2 個 Vnet。 它們會當作輪輻 Vnet。 在本文中，VNet 輪輻位址空間是 10.0.2.0/24 和 10.0.3.0/24。 如果您需要如何建立 VNet 的詳細資訊，請參閱[使用 PowerShell 建立虛擬網路](../virtual-network/quick-create-powershell.md)。
* 請確定任何 Vnet 中都沒有虛擬網路閘道。

## <a name="1-sign-in"></a><a name="signin"></a>1.登入

務必安裝最新版的 Resource Manager PowerShell Cmdlet。 如需如何安裝 PowerShell Cmdlet 的詳細資訊，請參閱[如何安裝和設定 Azure PowerShell](/powershell/azure/install-az-ps)。 這一點很重要，因為較早版本的 Cmdlet 未包含您在此練習中所需的現行值。

1. 以提高的權限開啟 PowerShell 主控台並登入您的 Azure 帳戶。 此 Cmdlet 會提示您提供登入認證。 登入之後，它會下載您的帳戶設定以供 Azure PowerShell 使用。

   ```powershell
   Connect-AzAccount
   ```
2. 取得您的 Azure 訂用帳戶清單。

   ```powershell
   Get-AzSubscription
   ```
3. 指定您要使用的訂用帳戶。

   ```powershell
   Select-AzSubscription -SubscriptionName "Name of subscription"
   ```

## <a name="2-create-resources"></a><a name="rg"></a>2. 建立資源

1. 建立資源群組。

   ```powershell
   New-AzResourceGroup -Location "West US" -Name "testRG"
   ```
2. 建立虛擬 WAN。

   ```powershell
   $virtualWan = New-AzVirtualWan -ResourceGroupName "testRG" -Name "myVirtualWAN" -Location "West US"
   ```
3. 建立虛擬中樞。

   ```powershell
   New-AzVirtualHub -VirtualWan $virtualWan -ResourceGroupName "testRG" -Name "westushub" -AddressPrefix "10.0.1.0/24" -Location "West US"
   ```

## <a name="3-create-connections"></a><a name="connections"></a>3. 建立連接

從「間接輪幅 VNet」與 DMZ VNet 到虛擬中樞之間建立中樞虛擬網路連線。

  ```powershell
  $remoteVirtualNetwork1= Get-AzVirtualNetwork -Name "indirectspoke1" -ResourceGroupName "testRG"
  $remoteVirtualNetwork2= Get-AzVirtualNetwork -Name "indirectspoke2" -ResourceGroupName "testRG"
  $remoteVirtualNetwork3= Get-AzVirtualNetwork -Name "dmzvnet" -ResourceGroupName "testRG"

  New-AzVirtualHubVnetConnection -ResourceGroupName "testRG" -VirtualHubName "westushub" -Name  "testvnetconnection1" -RemoteVirtualNetwork $remoteVirtualNetwork1
  New-AzVirtualHubVnetConnection -ResourceGroupName "testRG" -VirtualHubName "westushub" -Name  "testvnetconnection2" -RemoteVirtualNetwork $remoteVirtualNetwork2
  New-AzVirtualHubVnetConnection -ResourceGroupName "testRG" -VirtualHubName "westushub" -Name  "testvnetconnection3" -RemoteVirtualNetwork $remoteVirtualNetwork3
  ```

## <a name="4-create-a-virtual-hub-route"></a><a name="route"></a>4. 建立虛擬中樞路由

在本文中，「間接輪幅 VNet」位址空間是 10.0.2.0/24 和 10.0.3.0/24，而 DMZ NVA 網路介面私人 IP 位址是 10.0.4.5。

```powershell
$route1 = New-AzVirtualHubRoute -AddressPrefix @("10.0.2.0/24", "10.0.3.0/24") -NextHopIpAddress "10.0.4.5"
```

## <a name="5-create-a-virtual-hub-route-table"></a><a name="applyroute"></a>5. 建立虛擬中樞路由表

建立虛擬中樞路由表，然後對它套用已建立的路由。
 
```powershell
$routeTable = New-AzVirtualHubRouteTable -Route @($route1)
```

## <a name="6-commit-the-changes"></a><a name="commit"></a>6. 認可變更

將變更認可到虛擬中樞。

```powershell
Update-AzVirtualHub -ResourceGroupName "testRG" -Name "westushub" -RouteTable $routeTable
```

## <a name="next-steps"></a>後續步驟

若要深入了解虛擬 WAN，請參閱[虛擬 WAN 概觀](virtual-wan-about.md)頁面。
