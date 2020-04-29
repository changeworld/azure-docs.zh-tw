---
title: 使用 Azure Powershell 建立防禦主機 |Microsoft Docs
description: 在本文中，您將瞭解如何建立 Azure 防禦主機
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: conceptual
ms.date: 02/03/2020
ms.author: cherylmc
ms.openlocfilehash: 43d834f0c834696cd4a836466c9663fe7c31a392
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "80520508"
---
# <a name="create-an-azure-bastion-host-using-azure-powershell"></a>使用 Azure PowerShell 建立 Azure 防禦主機

本文說明如何使用 PowerShell 建立 Azure 防禦主機。 一旦在您的虛擬網路中布建 Azure 防禦服務，相同虛擬網路中的所有 Vm 都可以使用順暢的 RDP/SSH 體驗。 Azure Bastion 部署依虛擬網路來進行，而非以訂用帳戶/帳戶或虛擬機器為依據。

（選擇性）您可以使用[Azure 入口網站](bastion-create-host-portal.md)來建立 Azure 防禦主機。

## <a name="before-you-begin"></a>開始之前

請確認您有 Azure 訂用帳戶。 如果您還沒有 Azure 訂用帳戶，您可以啟用[MSDN 訂閱者權益](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details)或註冊[免費帳戶](https://azure.microsoft.com/pricing/free-trial)。

[!INCLUDE [powershell](../../includes/vpn-gateway-cloud-shell-powershell-about.md)]

## <a name="create-a-bastion-host"></a><a name="createhost"></a>建立 Bastion 主機

本節可協助您使用 Azure PowerShell 建立新的 Azure 防禦資源。

1. 建立虛擬網路和 Azure 防禦子網。 您必須使用 [名稱] 值**AzureBastionSubnet**來建立 Azure 防禦子網。 此值可讓 Azure 知道要將防禦資源部署到哪一個子網。 這與閘道子網不同。 您必須使用至少為/27 或更大子網的子網（/27、/26 等等）。 建立不含任何路由表或委派的**AzureBastionSubnet** 。 如果您在**AzureBastionSubnet**上使用網路安全性群組，請參閱[使用 nsg](bastion-nsg.md)一文。

   ```azurepowershell-interactive
   $subnetName = "AzureBastionSubnet"
   $subnet = New-AzVirtualNetworkSubnetConfig -Name $subnetName -AddressPrefix 10.0.0.0/24
   $vnet = New-AzVirtualNetwork -Name "myVnet" -ResourceGroupName "myBastionRG" -Location "westeurope" -AddressPrefix 10.0.0.0/16 -Subnet $subnet
   ```

2. 建立 Azure 防禦的公用 IP 位址。 公用 IP 是公用 IP 位址，即會存取 RDP/SSH 的防禦資源（透過埠443）。 公用 IP 位址必須與您建立的防禦資源位於相同的區域中。

   ```azurepowershell-interactive
   $publicip = New-AzPublicIpAddress -ResourceGroupName "myBastionRG" -name "myPublicIP" -location "westeurope" -AllocationMethod Static -Sku Standard
   ```

3. 在您的虛擬網路 AzureBastionSubnet 中建立新的 Azure 防禦資源。 建立和部署防禦資源需要大約5分鐘的時間。

   ```azurepowershell-interactive
   $bastion = New-AzBastion -ResourceGroupName "myBastionRG" -Name "myBastion" -PublicIpAddress $publicip -VirtualNetwork $vnet
   ```

## <a name="next-steps"></a>後續步驟

* 如需其他資訊，請閱讀防禦[常見問題](bastion-faq.md)。

* 若要搭配使用網路安全性群組與 Azure 防禦子網，請參閱使用[nsg](bastion-nsg.md)。
