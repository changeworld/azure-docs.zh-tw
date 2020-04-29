---
title: 在 Azure 中設定負載平衡器 TCP 閒置超時
titleSuffix: Azure Load Balancer
description: 在本文中，您將瞭解如何設定 Azure Load Balancer TCP 閒置超時。
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "79456823"
---
# <a name="configure-tcp-idle-timeout-settings-for-azure-load-balancer"></a>設定 Azure Load Balancer 的 TCP 閒置逾時設定

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

如果您選擇在本機安裝和使用 PowerShell，本文會要求使用 Azure PowerShell 模組版本 5.4.1 或更新版本。 執行 `Get-Module -ListAvailable Az` 來了解安裝的版本。 如果您需要升級，請參閱[安裝 Azure PowerShell 模組](/powershell/azure/install-Az-ps)。 如果您在本機執行 PowerShell，則也需要執行 `Connect-AzAccount` 以建立與 Azure 的連線。

## <a name="tcp-idle-timeout"></a>TCP 閒置超時
Azure Load Balancer 的閒置超時設定為4分鐘到30分鐘。 根據預設，它會設定為4分鐘。 如果閒置期間超過逾時值，即無法保證仍能維持用戶端與雲端服務之間的 TCP 或 HTTP 工作階段。

當連線關閉時，用戶端應用程式可能會收到以下錯誤訊息：「基礎連線已關閉：應該保持運作的連接卻被伺服器關閉。」

常見作法是使用 TCP Keep-Alive。 此作法可讓連線保持長時間連線。 如需詳細資訊，請參閱這些 [.NET 文章](https://msdn.microsoft.com/library/system.net.servicepoint.settcpkeepalive.aspx)。 啟用 Keep-Alive 之後，就會在連線無活動期間傳送封包。 Keep-alive 封包可確保不會達到閒置的超時值，且連接會長時間維持。

此設定僅適用于輸入連接。 若要避免失去連線，請將 TCP 保持連線設定為小於閒置的超時設定，或增加閒置的超時值。 為了支援這些案例，已新增可設定的閒置超時支援。

TCP keep-alive 適用于電池壽命不是條件約束的情況。 不建議用於行動應用程式。 在行動裝置應用程式中使用 TCP Keep-Alive 可能會更快耗盡裝置電池電力。

![TCP 逾時](./media/load-balancer-tcp-idle-timeout/image1.png)

下列各節說明如何變更公用 IP 和負載平衡器資源的閒置超時設定。

## <a name="configure-the-tcp-timeout-for-your-instance-level-public-ip-to-15-minutes"></a>將執行個體層級公用 IP 的 TCP 逾時值設定為 15 分鐘

```azurepowershell-interactive
$publicIP = Get-AzPublicIpAddress -Name MyPublicIP -ResourceGroupName MyResourceGroup
$publicIP.IdleTimeoutInMinutes = "15"
Set-AzPublicIpAddress -PublicIpAddress $publicIP
```

`IdleTimeoutInMinutes` 是選擇性的。 若未設定，則預設的逾時為 4 分鐘。 可接受的逾時範圍為 4 到 30 分鐘。

## <a name="set-the-tcp-timeout-on-a-load-balanced-rule-to-15-minutes"></a>將負載平衡規則的 TCP 超時設定為15分鐘

若要設定負載平衡器的閒置超時，請在負載平衡規則上設定 ' IdleTimeoutInMinutes '。 例如：

```azurepowershell-interactive
$lb = Get-AzLoadBalancer -Name "MyLoadBalancer" -ResourceGroup "MyResourceGroup"
$lb | Set-AzLoadBalancerRuleConfig -Name myLBrule -IdleTimeoutInMinutes 15
```
## <a name="next-steps"></a>後續步驟

[內部負載平衡器概觀](load-balancer-internal-overview.md)

[開始設定網際網路對向負載平衡器](quickstart-create-standard-load-balancer-powershell.md)

[設定負載平衡器分配模式](load-balancer-distribution-mode.md)
