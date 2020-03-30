---
title: 為 Azure 虛擬 WAN 配置全域 VNet 對等互連 |微軟文檔
description: 將不同區域中的 VNet 連接到虛擬廣域網路中心。
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: cherylmc
ms.openlocfilehash: 340472f84d2dd2c4f46d180992745a57e8ad1884
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "73588222"
---
# <a name="configure-global-vnet-peering-cross-region-vnet-for-virtual-wan"></a>為虛擬 WAN 配置全域 VNet 對等互連（跨區域 VNet）

您可以將不同區域中的 VNet 連接到虛擬廣域網路中心。

## <a name="before-you-begin"></a>開始之前

請確認您已符合下列條件：

* 跨區域 VNet（分支）未連接到另一個虛擬廣域網路中心。 分支只能連接到一個虛擬中心。
* VNet（分支）不包含虛擬網路閘道（例如，Azure VPN 閘道或 ExpressRoute 虛擬網路閘道）。 如果 VNet 包含虛擬網路閘道，則必須在將分支 VNet 連接到集線器之前刪除閘道。

## <a name="register-this-feature"></a><a name="register"></a>註冊此功能

您可以使用 PowerShell 註冊此功能。 如果從下面的示例中選擇"試用"，Azure 雲殼將打開，並且無需在本地將 PowerShell Cmdlet 安裝到您的電腦。 如有必要，您可以使用"選擇-訂閱 -訂閱 Id" <subid>Cmdlet 更改訂閱。

```azurepowershell-interactive
Register-AzProviderFeature -FeatureName AllowCortexGlobalVnetPeering -ProviderNamespace Microsoft.Network
Register-AzResourceProvider -ProviderNamespace 'Microsoft.Network'
```

## <a name="verify-registration"></a><a name="verify"></a>驗證註冊

```azurepowershell-interactive
Get-AzProviderFeature -FeatureName AllowCortexGlobalVnetPeering -ProviderNamespace Microsoft.Network
```

## <a name="connect-a-vnet-to-the-hub"></a><a name="hub"></a>將 VNet 連接到集線器

在此步驟中，您將在集線器和跨區域 VNet 之間創建對等互連連接。 為您想要連線的每個 VNet 重複這些步驟。

1. 在虛擬 WAN 頁面上，按一下 [虛擬網路連線]****。
2. 在 [虛擬網路連線] 頁面上，按一下 [+ 新增連線]****。
3. 在 [新增連線]**** 頁面上，填寫下列欄位︰

    * **名稱** - 為您的連線命名。
    * **中樞** - 選取要與此連線產生關聯的中樞。
    * **訂用帳戶** - 請確認訂用帳戶。
    * **虛擬網路** - 選取要與此中樞連線的虛擬網路。 虛擬網路不能有現有的虛擬網路閘道。
4. 按一下 [確定]**** 來建立對等互連連線。

## <a name="next-steps"></a>後續步驟

要瞭解有關虛擬 WAN 的詳細資訊，請參閱[虛擬 WAN 概述](virtual-wan-about.md)。