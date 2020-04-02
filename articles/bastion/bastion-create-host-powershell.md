---
title: 使用 Azure 電源外殼創建堡壘主機 |微軟文件
description: 在本文中,瞭解如何創建 Azure 堡壘主機
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: conceptual
ms.date: 02/03/2020
ms.author: cherylmc
ms.openlocfilehash: 43d834f0c834696cd4a836466c9663fe7c31a392
ms.sourcegitcommit: b0ff9c9d760a0426fd1226b909ab943e13ade330
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/01/2020
ms.locfileid: "80520508"
---
# <a name="create-an-azure-bastion-host-using-azure-powershell"></a>使用 Azure PowerShell 建立 Azure 堡壘主機

本文介紹如何使用 PowerShell 創建 Azure 堡壘主機。 在虛擬網路中預配 Azure Bastion 服務後,同一虛擬網路中的所有 VM 都可以使用無縫的 RDP/SSH 體驗。 Azure Bastion 部署依虛擬網路來進行，而非以訂用帳戶/帳戶或虛擬機器為依據。

或者,可以使用[Azure 門戶](bastion-create-host-portal.md)創建 Azure 堡壘主機。

## <a name="before-you-begin"></a>開始之前

請確認您有 Azure 訂用帳戶。 如果您還沒有 Azure 訂閱,則可以啟動[MSDN 訂閱者權益](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details)或註冊[免費帳戶](https://azure.microsoft.com/pricing/free-trial)。

[!INCLUDE [powershell](../../includes/vpn-gateway-cloud-shell-powershell-about.md)]

## <a name="create-a-bastion-host"></a><a name="createhost"></a>建立 Bastion 主機

本節可説明您使用 Azure PowerShell 創建新的 Azure 堡壘資源。

1. 創建虛擬網路和 Azure 堡壘子網。 您必須使用名稱值**Azure Bastion 子網**創建 Azure 堡壘子網。 此值使 Azure 知道要將堡壘資源部署到哪個子網。 這與閘道網不同。 您必須使用至少 /27 或更大的子網(/27、/26 等)的子網。 創建**AzureBastion Subnet,** 沒有任何路由表或授權。 如果在**AzureBastionSubnet**上使用網路安全組,請參閱[使用 NSG](bastion-nsg.md)的文章。

   ```azurepowershell-interactive
   $subnetName = "AzureBastionSubnet"
   $subnet = New-AzVirtualNetworkSubnetConfig -Name $subnetName -AddressPrefix 10.0.0.0/24
   $vnet = New-AzVirtualNetwork -Name "myVnet" -ResourceGroupName "myBastionRG" -Location "westeurope" -AddressPrefix 10.0.0.0/16 -Subnet $subnet
   ```

2. 為 Azure 堡壘創建公共 IP 位址。 公共 IP 是公共 IP 位址,是將訪問 RDP/SSH 的堡壘資源(通過埠 443)。 公共 IP 位址必須與您正在創建的堡壘資源位於同一區域。

   ```azurepowershell-interactive
   $publicip = New-AzPublicIpAddress -ResourceGroupName "myBastionRG" -name "myPublicIP" -location "westeurope" -AllocationMethod Static -Sku Standard
   ```

3. 在虛擬網路的 Azure Bastion Subnet 建立新的 Azure 堡壘資源。 創建和部署堡壘資源大約需要5分鐘。

   ```azurepowershell-interactive
   $bastion = New-AzBastion -ResourceGroupName "myBastionRG" -Name "myBastion" -PublicIpAddress $publicip -VirtualNetwork $vnet
   ```

## <a name="next-steps"></a>後續步驟

* 有關詳細資訊,請閱讀[堡壘常見問題解答](bastion-faq.md)。

* 要將網路安全組與 Azure 堡壘子網一起使用,請參閱[使用 NSG。](bastion-nsg.md)
