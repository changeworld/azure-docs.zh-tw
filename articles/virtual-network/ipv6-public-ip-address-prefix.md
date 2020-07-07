---
title: Azure 虛擬網路中的公用 IPv6 位址首碼
titlesuffix: Azure Virtual Network
description: 瞭解 Azure 虛擬網路中的公用 IPv6 位址首碼。
services: virtual-network
documentationcenter: na
author: KumudD
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/31/2020
ms.author: kumud
ms.openlocfilehash: 096dbcb6b6a732bd21622658f6f30c5158a821c5
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "80420531"
---
# <a name="reserved-public-ipv6-address-prefix"></a>保留的公用 IPv6 位址首碼

在 Azure 中，雙重堆疊（IPv4 + IPv6）虛擬網路（VNet）和虛擬機器（Vm）預設是安全的，因為它們沒有網際網路連線能力。 您可以使用從 Azure 取得的公用 IPv6 位址，輕鬆地將 IPv6 網際網路連線新增至您的 Azure 負載平衡器和 Vm。

您保留的任何公用 Ip 都會與您所選的 Azure 區域和您的 Azure 訂用帳戶相關聯。 您可以在訂用帳戶中的任何 Azure 負載平衡器或 Vm 之間移動保留（靜態） IPv6 公用 IP。 您可以完全中斷與 IPv6 公用 IP 的關聯，而且當您準備就緒時，就會保留給您使用。

> [!WARNING]
> 請小心不要不小心刪除公用 IP 位址。 刪除公用 IP 會將它從您的訂用帳戶中移除，而且您將無法加以復原（即使是 Azure 支援的協助）。

除了保留個別的 IPv6 位址，您還可以保留 Azure IPv6 位址的連續範圍（也稱為 IP 首碼）供您使用。  與個別的 IP 位址類似，保留的首碼會與您所選的 Azure 區域和您的 Azure 訂用帳戶相關聯。 保留可預測且連續的位址範圍有許多用途。 例如，您可以大幅簡化公司和客戶的 Azure 託管應用程式的 IP*允許清單*，因為您的靜態 IP 範圍可以輕易地程式化到內部部署防火牆。  您可以視需要從您的 IP 首碼建立個別的公用 Ip，而當您刪除這些個別的公用 Ip 時，它們會*傳回*至您的保留範圍，讓您可以在稍後重複使用它們。 在您刪除前置詞之前，您的 IP 首碼中的所有 IP 位址都會保留供您獨佔使用。



## <a name="ipv6-prefix-sizes"></a>IPv6 首碼大小
可用的公用 IP 首碼大小如下：

-  IPv6 首碼大小下限：/127 = 2 個位址
-  IPv6 首碼大小上限：/124 = 16 個位址

首碼大小會指定為無類別網域間路由（CIDR）遮罩大小。 例如，/128 的遮罩代表個別 IPv6 位址，因為 IPv6 位址是由128位組成。

## <a name="pricing"></a>定價
 
如需使用 Azure 公用 ip （個別 IP 位址和 IP 範圍）的相關成本，請參閱[公用 Ip 位址定價](https://azure.microsoft.com/pricing/details/ip-addresses/)。

## <a name="limitations"></a>限制
只有使用「動態」配置的基本公用 Ip 才支援 IPv6，這表示如果您在 Azure 中刪除並重新部署應用程式（VM 或負載平衡器），IPv6 位址將會變更。 標準 IPv6 公用 IP 僅支援靜態（保留）配置，不過標準內部負載平衡器也可支援從指派給它們的子網內進行動態配置。  

基於最佳做法，我們建議您針對 IPv6 應用程式使用標準公用 Ip 和標準負載平衡器。

## <a name="next-steps"></a>後續步驟
- 保留公用[IPv6 位址首碼](ipv6-reserve-public-ip-address-prefix.md)。
- 深入瞭解[IPv6 位址](ipv6-overview.md)。
- 瞭解如何在 Azure 中[建立和使用公用 ip](virtual-network-public-ip-address.md) （IPv4 和 IPv6）。
