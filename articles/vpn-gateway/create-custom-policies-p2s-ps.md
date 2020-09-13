---
title: 建立並設定點對站的自訂 IPsec 原則： PowerShell
titleSuffix: Azure VPN Gateway
description: 本文可協助您建立及設定 VPN 閘道 P2S 設定的自訂 IPSec 原則
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 09/09/2020
ms.author: cherylmc
ms.openlocfilehash: 852ff5e6ad847ff33f8e32b4c3d9f8b325b80716
ms.sourcegitcommit: f8d2ae6f91be1ab0bc91ee45c379811905185d07
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/10/2020
ms.locfileid: "89664056"
---
# <a name="create-and-set-custom-ipsec-policies-for-point-to-site"></a>建立並設定點對站的自訂 IPsec 原則

如果您的環境需要自訂 IPsec 原則進行加密，您可以輕鬆地使用必要的設定來設定原則物件。 本文可協助您建立自訂原則物件，然後使用 PowerShell 進行設定。

## <a name="before-you-begin"></a>開始之前

### <a name="prerequisites"></a>必要條件

確認您的環境符合下列必要條件：

* 您已設定正常運作的點對站 VPN。 如果未設定，請使用 [使用[PowerShell](vpn-gateway-howto-point-to-site-rm-ps.md)**建立點對站 VPN** ] 文章或[Azure 入口網站](vpn-gateway-howto-point-to-site-resource-manager-portal.md)來設定一個。

### <a name="working-with-azure-powershell"></a>使用 Azure PowerShell

[!INCLUDE [PowerShell](../../includes/vpn-gateway-cloud-shell-powershell.md)]

## <a name="1-set-variables"></a><a name="signin"></a>1. 設定變數

宣告您想要使用的變數。 使用下列範例，視需要將值取代為您自己的值。 如果您在練習期間的任何時間點關閉 PowerShell/Cloud Shell 會話，只要再次複製並貼上這些值以重新宣告變數即可。

  ```azurepowershell-interactive
  $RG = "TestRG"
  $GWName = "VNet1GW"
  ```

## <a name="2-create-policy-object"></a><a name="create"></a>2. 建立原則物件

建立自訂 IPsec 原則物件。 您可以調整值以符合所需的條件。

```azurepowershell-interactive
$vpnclientipsecpolicy = New-AzVpnClientIpsecPolicy -IpsecEncryption AES256 -IpsecIntegrity SHA256 -SALifeTime 86471 -SADataSize 429496 -IkeEncryption AES256 -IkeIntegrity SHA384 -DhGroup DHGroup2 -PfsGroup PFS2
```

## <a name="3-update-gateway-and-set-policy"></a><a name="update"></a>3. 更新閘道和設定原則

在此步驟中，請更新您現有的 P2S VPN 閘道，並設定 IPsec 原則。

```azurepowershell-interactive
$gateway = Get-AzVirtualNetworkGateway -ResourceGroupName $RG -name $GWName
Set-AzVirtualNetworkGateway -VirtualNetworkGateway $gateway -VpnClientIpsecPolicy $vpnclientipsecpolicy
```

## <a name="next-steps"></a>接下來的步驟

如需有關 P2S 設定的詳細資訊，請參閱 [關於點對站 VPN](point-to-site-about.md)。