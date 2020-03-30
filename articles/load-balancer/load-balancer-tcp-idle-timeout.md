---
title: 在 Azure 中配置負載等化器 TCP 空閒超時
titleSuffix: Azure Load Balancer
description: 在本文中，瞭解如何配置 Azure 負載等化器 TCP 空閒超時。
services: load-balancer
documentationcenter: na
author: asudbring
ms.custom: seodec18
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/09/2020
ms.author: allensu
ms.openlocfilehash: d0bb73b58aa23e5f7eb784772acf37b05df463ba
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79456823"
---
# <a name="configure-tcp-idle-timeout-settings-for-azure-load-balancer"></a>設定 Azure Load Balancer 的 TCP 閒置逾時設定

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

如果您選擇在本機安裝和使用 PowerShell，本文會要求使用 Azure PowerShell 模組版本 5.4.1 或更新版本。 執行 `Get-Module -ListAvailable Az` 來了解安裝的版本。 如果您需要升級，請參閱[安裝 Azure PowerShell 模組](/powershell/azure/install-Az-ps)。 如果您在本機執行 PowerShell，則也需要執行 `Connect-AzAccount` 以建立與 Azure 的連線。

## <a name="tcp-idle-timeout"></a>TCP 空閒超時
Azure 負載等化器的空閒超時設置為 4 分鐘到 30 分鐘。 預設情況下，它設置為 4 分鐘。 如果閒置期間超過逾時值，即無法保證仍能維持用戶端與雲端服務之間的 TCP 或 HTTP 工作階段。

當連線關閉時，用戶端應用程式可能會收到以下錯誤訊息：「基礎連線已關閉：應該保持運作的連接卻被伺服器關閉。」

常見作法是使用 TCP Keep-Alive。 此作法可讓連線保持長時間連線。 如需詳細資訊，請參閱這些 [.NET 文章](https://msdn.microsoft.com/library/system.net.servicepoint.settcpkeepalive.aspx)。 啟用 Keep-Alive 之後，就會在連線無活動期間傳送封包。 保持活動資料包可確保不達到空閒超時值並長時間維護連接。

該設置僅適用于入站連接。 為避免丟失連接，請以小於空閒超時設置的間隔配置 TCP 保持活動狀態，或增加空閒超時值。 為了支援這些方案，添加了對可配置空閒超時的支援。

TCP 保持活動適用于電池壽命不是約束的情況。 不建議移動應用程式使用。 在行動裝置應用程式中使用 TCP Keep-Alive 可能會更快耗盡裝置電池電力。

![TCP 逾時](./media/load-balancer-tcp-idle-timeout/image1.png)

以下各節介紹如何更改公共 IP 和負載等化器資源的空閒超時設置。

## <a name="configure-the-tcp-timeout-for-your-instance-level-public-ip-to-15-minutes"></a>將執行個體層級公用 IP 的 TCP 逾時值設定為 15 分鐘

```azurepowershell-interactive
$publicIP = Get-AzPublicIpAddress -Name MyPublicIP -ResourceGroupName MyResourceGroup
$publicIP.IdleTimeoutInMinutes = "15"
Set-AzPublicIpAddress -PublicIpAddress $publicIP
```

`IdleTimeoutInMinutes` 是選擇性的。 若未設定，則預設的逾時為 4 分鐘。 可接受的逾時範圍為 4 到 30 分鐘。

## <a name="set-the-tcp-timeout-on-a-load-balanced-rule-to-15-minutes"></a>將負載平衡規則的 TCP 超時設置為 15 分鐘

要設置負載等化器的空閒超時，在負載平衡規則上設置"空閒超時 In分鐘"。 例如：

```azurepowershell-interactive
$lb = Get-AzLoadBalancer -Name "MyLoadBalancer" -ResourceGroup "MyResourceGroup"
$lb | Set-AzLoadBalancerRuleConfig -Name myLBrule -IdleTimeoutInMinutes 15
```
## <a name="next-steps"></a>後續步驟

[內部負載平衡器概觀](load-balancer-internal-overview.md)

[開始設定網際網路對向負載平衡器](quickstart-create-standard-load-balancer-powershell.md)

[設定負載平衡器分配模式](load-balancer-distribution-mode.md)
