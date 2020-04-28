---
title: 設定 Azure 虛擬 WAN 的全域 VNet 對等互連 |Microsoft Docs
description: 將不同區域中的 VNet 連線到您的虛擬 WAN 中樞。
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: cherylmc
ms.openlocfilehash: 340472f84d2dd2c4f46d180992745a57e8ad1884
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "73588222"
---
# <a name="configure-global-vnet-peering-cross-region-vnet-for-virtual-wan"></a>設定虛擬 WAN 的全域 VNet 對等互連（跨區域 VNet）

您可以將不同區域中的 VNet 連線到您的虛擬 WAN 中樞。

## <a name="before-you-begin"></a>開始之前

請確認您已符合下列條件：

* 跨區域 VNet （輪輻）未連接到另一個虛擬 WAN 中樞。 輪輻只能連線至一個虛擬中樞。
* VNet （輪輻）不包含虛擬網路閘道（例如，Azure VPN 閘道或 ExpressRoute 虛擬網路閘道）。 如果 VNet 包含虛擬網路閘道，您必須先移除閘道，才能將輪輻 VNet 連接到中樞。

## <a name="register-this-feature"></a><a name="register"></a>註冊此功能

您可以使用 PowerShell 註冊此功能。 如果您從下列範例選取 [試試看]，Azure Cloud Shell 會開啟，而且您不需要在本機電腦上安裝 PowerShell Cmdlet。 如有需要，您可以使用 ' Get-azsubscription-SubscriptionId <subid>' Cmdlet 來變更訂用帳戶。

```azurepowershell-interactive
Register-AzProviderFeature -FeatureName AllowCortexGlobalVnetPeering -ProviderNamespace Microsoft.Network
Register-AzResourceProvider -ProviderNamespace 'Microsoft.Network'
```

## <a name="verify-registration"></a><a name="verify"></a>確認註冊

```azurepowershell-interactive
Get-AzProviderFeature -FeatureName AllowCortexGlobalVnetPeering -ProviderNamespace Microsoft.Network
```

## <a name="connect-a-vnet-to-the-hub"></a><a name="hub"></a>將 VNet 連線至中樞

在此步驟中，您會建立中樞與跨區域 VNet 之間的對等互連連線。 為您想要連線的每個 VNet 重複這些步驟。

1. 在虛擬 WAN 頁面上，按一下 [虛擬網路連線]****。
2. 在 [虛擬網路連線] 頁面上，按一下 [+ 新增連線]****。
3. 在 [新增連線]**** 頁面上，填寫下列欄位︰

    * **名稱** - 為您的連線命名。
    * **中樞** - 選取要與此連線產生關聯的中樞。
    * **訂用帳戶** - 請確認訂用帳戶。
    * **虛擬網路** - 選取要與此中樞連線的虛擬網路。 虛擬網路不能有現有的虛擬網路閘道。
4. 按一下 [確定]**** 來建立對等互連連線。

## <a name="next-steps"></a>後續步驟

若要深入瞭解虛擬 WAN，請參閱[虛擬 Wan 總覽](virtual-wan-about.md)。