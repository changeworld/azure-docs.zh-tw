---
title: 在 Azure 中設定負載平衡器 TCP 閒置逾時
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
ms.date: 01/09/2020
ms.author: allensu
ms.openlocfilehash: 38db681655a839983ebf38e94ec28eb05ed65d1f
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "84808581"
---
# <a name="configure-tcp-idle-timeout-settings-for-azure-load-balancer"></a>設定 Azure Load Balancer 的 TCP 閒置逾時設定

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

如果您選擇在本機安裝和使用 PowerShell，本文會要求使用 Azure PowerShell 模組版本 5.4.1 或更新版本。 執行 `Get-Module -ListAvailable Az` 來了解安裝的版本。 如果您需要升級，請參閱[安裝 Azure PowerShell 模組](/powershell/azure/install-Az-ps)。 如果您在本機執行 PowerShell，則也需要執行 `Connect-AzAccount` 以建立與 Azure 的連線。

## <a name="tcp-idle-timeout"></a>TCP 閒置逾時
Azure Load Balancer 的閒置逾時設定是 4 分鐘到 30 分鐘。 根據預設，設定為 4 分鐘。 如果閒置期間超過逾時值，即無法保證仍能維持用戶端與雲端服務之間的 TCP 或 HTTP 工作階段。

當連線關閉時，您的用戶端應用程式可能會收到下列錯誤訊息：「基礎連線已關閉：應該保持運作的連接卻被伺服器關閉。」

常見作法是使用 TCP Keep-Alive。 此作法可讓連線保持長時間連線。 如需詳細資訊，請參閱這些 [.NET 文章](https://msdn.microsoft.com/library/system.net.servicepoint.settcpkeepalive.aspx)。 啟用 Keep-Alive 之後，就會在連線無活動期間傳送封包。 這些 Keep-Alive 封包可確保不會達到閒置逾時值，因此可以長期維持連線。

此設定僅適用於輸入連線。 若要避免連線中斷，請使用比閒置逾時設定短的間隔來設定 TCP Keep-Alive，或增加閒置逾時值。 為了支援這些情節，新增了可設定閒置逾時的支援。

TCP Keep-Alive 適用於電池使用時間不受約束的情節。 不建議用於行動應用程式。 在行動裝置應用程式中使用 TCP Keep-Alive 可能會更快耗盡裝置電池電力。

![TCP 逾時](./media/load-balancer-tcp-idle-timeout/image1.png)

下列各節說明如何變更公用 IP 和負載平衡器資源的閒置逾時設定。

>[!NOTE]
> TCP 閒置超時不會影響 UDP 通訊協定上的負載平衡規則。


## <a name="configure-the-tcp-timeout-for-your-instance-level-public-ip-to-15-minutes"></a>將執行個體層級公用 IP 的 TCP 逾時值設定為 15 分鐘

```azurepowershell-interactive
$publicIP = Get-AzPublicIpAddress -Name MyPublicIP -ResourceGroupName MyResourceGroup
$publicIP.IdleTimeoutInMinutes = "15"
Set-AzPublicIpAddress -PublicIpAddress $publicIP
```

`IdleTimeoutInMinutes` 是選擇性的。 若未設定，則預設的逾時為 4 分鐘。 可接受的逾時範圍為 4 到 30 分鐘。

## <a name="set-the-tcp-timeout-on-a-load-balanced-rule-to-15-minutes"></a>將負載平衡端點上的 TCP 逾時設定為 15 分鐘

若要設定負載平衡器的閒置逾時，請在負載平衡規則上設定 'IdleTimeoutInMinutes'。 例如：

```azurepowershell-interactive
$lb = Get-AzLoadBalancer -Name "MyLoadBalancer" -ResourceGroup "MyResourceGroup"
$lb | Set-AzLoadBalancerRuleConfig -Name myLBrule -IdleTimeoutInMinutes 15
```
## <a name="next-steps"></a>後續步驟

[內部負載平衡器概觀](load-balancer-internal-overview.md)

[開始設定網際網路對向負載平衡器](quickstart-create-standard-load-balancer-powershell.md)

[設定負載平衡器分配模式](load-balancer-distribution-mode.md)
