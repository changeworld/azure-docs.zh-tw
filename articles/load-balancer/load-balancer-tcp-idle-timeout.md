---
title: 在 Azure 中設定負載平衡器 TCP 重設和閒置超時
titleSuffix: Azure Load Balancer
description: 在本文中，了解如何設定 Azure Load Balancer TCP 閒置逾時。
services: load-balancer
documentationcenter: na
author: asudbring
ms.custom: seodec18
ms.service: load-balancer
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/09/2020
ms.author: allensu
ms.openlocfilehash: b507fbad4d9089d918ae7a85c07f30efcb118476
ms.sourcegitcommit: 3bcce2e26935f523226ea269f034e0d75aa6693a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/23/2020
ms.locfileid: "92487240"
---
# <a name="configure-tcp-idle-timeout-for-azure-load-balancer"></a>設定 Azure Load Balancer 的 TCP 閒置超時

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

如果您選擇在本機安裝和使用 PowerShell，本文會要求使用 Azure PowerShell 模組版本 5.4.1 或更新版本。 執行 `Get-Module -ListAvailable Az` 來了解安裝的版本。 如果您需要升級，請參閱[安裝 Azure PowerShell 模組](/powershell/azure/install-Az-ps)。 如果您在本機執行 PowerShell，則也需要執行 `Connect-AzAccount` 以建立與 Azure 的連線。

Azure Load Balancer 具有下列閒置超時範圍：

針對 Load Balancer 規則和輸入 NAT 規則，4分鐘至100分鐘的輸出規則為4分鐘到30分鐘

根據預設，設定為 4 分鐘。 如果閒置期間超過逾時值，即無法保證仍能維持用戶端與雲端服務之間的 TCP 或 HTTP 工作階段。 深入瞭解 [TCP 閒置超時](load-balancer-tcp-reset.md)。

下列各節說明如何變更公用 IP 和負載平衡器資源的閒置逾時設定。


## <a name="configure-the-tcp-idle-timeout-for-your-public-ip"></a>設定公用 IP 的 TCP 閒置超時

```azurepowershell-interactive
$publicIP = Get-AzPublicIpAddress -Name MyPublicIP -ResourceGroupName MyResourceGroup
$publicIP.IdleTimeoutInMinutes = "15"
Set-AzPublicIpAddress -PublicIpAddress $publicIP
```

`IdleTimeoutInMinutes` 是選擇性的。 若未設定，則預設的逾時為 4 分鐘。 

## <a name="set-the-tcp-idle-timeout-on-rules"></a>設定規則的 TCP 閒置超時

若要設定負載平衡器的閒置逾時，請在負載平衡規則上設定 'IdleTimeoutInMinutes'。 例如：

```azurepowershell-interactive
$lb = Get-AzLoadBalancer -Name "MyLoadBalancer" -ResourceGroup "MyResourceGroup"
$lb | Set-AzLoadBalancerRuleConfig -Name myLBrule -IdleTimeoutInMinutes 15
```

## <a name="next-steps"></a>後續步驟

[內部負載平衡器概觀](load-balancer-internal-overview.md)

[開始設定網際網路對向負載平衡器](quickstart-load-balancer-standard-public-powershell.md)

[設定負載平衡器分配模式](load-balancer-distribution-mode.md)
