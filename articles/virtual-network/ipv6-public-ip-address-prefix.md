---
title: Azure 虛擬網路中的公用 IPv6 位址首碼
titlesuffix: Azure Virtual Network
description: 深入瞭解 Azure 虛擬網路中的公用 IPv6 位址首碼。
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
ms.openlocfilehash: ff33bf771065e7af209934a5c54b6f8f2e34835e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "91666778"
---
# <a name="reserved-public-ipv6-address-prefix"></a>保留的公用 IPv6 位址首碼

在 Azure 中，雙重堆疊 (IPv4 + IPv6) 虛擬網路 (VNet) 和虛擬機器 (Vm) 預設是安全的，因為它們沒有網際網路連線能力。 您可以輕鬆地將 IPv6 網際網路連線新增至 Azure 負載平衡器，以及具有您從 Azure 取得之公用 IPv6 位址的 Vm。

您保留的任何公用 Ip 都會與您選擇的 Azure 區域和您的 Azure 訂用帳戶相關聯。 您可以在訂用帳戶中的任何 Azure 負載平衡器或 Vm 之間，移動保留 (靜態) IPv6 公用 IP。 您可以完全中斷 IPv6 公用 IP 的關聯，並在您準備就緒時，將其保留供您使用。

> [!WARNING]
> 請小心不要不慎刪除您的公用 IP 位址。 刪除公用 IP 會將它從您的訂用帳戶中移除，而且您也無法將它復原 (即使是 Azure 支援) 的協助也一樣。

除了保留個別的 IPv6 位址，您可以保留連續的 Azure IPv6 位址範圍 (稱為 IP 首碼) 供您使用。  與個別 IP 位址類似，保留的前置詞會與您選擇的 Azure 區域和您的 Azure 訂用帳戶相關聯。 保留可預測、連續的位址範圍有許多用途。 例如，您可以大幅簡化公司和客戶對 Azure 託管應用程式的 IP *篩選* ，因為您的靜態 IP 範圍可以輕易地設計成內部部署防火牆。  您可以視需要從 IP 首碼建立個別的公用 Ip，而當您刪除這些個別的公用 Ip 時，這些 ip 會 *傳回* 給您的保留範圍，讓您稍後可以重複使用它們。 在您刪除首碼之前，您的 IP 首碼中的所有 IP 位址都會保留給您的專屬用途。



## <a name="ipv6-prefix-sizes"></a>IPv6 首碼大小
可用的公用 IP 首碼大小如下：

-  IPv6 首碼的最小值：/127 = 2 個位址
-  IPv6 首碼大小上限：/124 = 16 個位址

首碼大小會指定為無類別 Inter-Domain 路由 (CIDR) 遮罩大小。 例如，a/128 的遮罩代表個別的 IPv6 位址，因為 IPv6 位址是由128位組成。

## <a name="pricing"></a>定價
 
如需使用 Azure 公用 ip、個別 IP 位址和 IP 範圍的相關成本，請參閱 [公用 Ip 位址定價](https://azure.microsoft.com/pricing/details/ip-addresses/)。

## <a name="limitations"></a>限制
只有使用「動態」配置的基本公用 Ip 才支援 IPv6，這表示如果您刪除應用程式，並將其重新部署至 Azure 中 (VM 或負載平衡器) ，則 IPv6 位址會變更。 標準 IPv6 公用 IP 僅支援靜態 (保留) 配置，不過標準的內部負載平衡器也可支援從指派給它們的子網內進行動態配置。  

基於最佳作法，建議您針對 IPv6 應用程式使用標準公用 Ip 和標準負載平衡器。

## <a name="next-steps"></a>後續步驟
- 保留公用 [IPv6 位址首碼](ipv6-reserve-public-ip-address-prefix.md)。
- 深入瞭解 [IPv6 位址](ipv6-overview.md)。
- 瞭解如何在 Azure 中的 IPv4 和 IPv6)  ([建立和使用公用 ip](virtual-network-public-ip-address.md) 。
