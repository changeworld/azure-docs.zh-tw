---
title: Azure 虛擬網路中的公共 IPv6 位址首碼
titlesuffix: Azure Virtual Network
description: 瞭解 Azure 虛擬網路中的公共 IPv6 位址首碼。
services: virtual-network
documentationcenter: na
author: KumudD
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/15/2019
ms.author: kumud
ms.openlocfilehash: 8254a7d86d5cadc2ddc03940f4ab2d08de74bd86
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "72965164"
---
# <a name="reserved-public-ipv6-address-prefix-preview"></a>保留公共 IPv6 位址首碼（預覽版）

在 Azure 中，雙堆疊 （IPv4_IPv6） 虛擬網路 （VNet） 和虛擬機器 （VM） 預設是安全的，因為它們沒有互聯網連接。 您可以使用從 Azure 獲取的公共 IPv6 位址輕鬆向 Azure 負載等化器和 VM 添加 IPv6 Internet 連接。

您保留的任何公共 IP 都與您選擇的 Azure 區域和 Azure 訂閱相關聯。 您可以在訂閱中的任何 Azure 負載等化器或 VM 之間移動保留的（靜態）IPv6 公共 IP。 您可以完全分離 IPv6 公共 IP，並在準備就緒時將其持有供您使用。

> [!WARNING]
> 請謹慎不要意外刪除公共 IP 位址。 刪除公共 IP 會將其從訂閱中刪除，您將無法恢復它（即使在 Azure 支援的説明下也是如此）。

除了保留單個 IPv6 位址外，還可以保留連續範圍的 Azure IPv6 位址（稱為 IP 首碼）供使用。  與單個 IP 位址類似，保留首碼與您選擇的 Azure 區域和 Azure 訂閱相關聯。 保留可預測、連續的位址範圍有許多用途。 例如，您可以大大簡化公司和客戶對 Azure 託管應用程式的 IP*白名單，* 因為靜態 IP 範圍可以很容易地程式設計到本地防火牆中。  您可以根據需要從 IP 首碼創建單個公共 IP，當您刪除這些單獨的公共 IP 時，它們*將返回到*您的保留範圍，以便以後可以重複使用它們。 在刪除首碼之前，IP 首碼中的所有 IP 位址都保留供獨佔使用。

> [!Important]
> Azure 虛擬網路的 IPv6 當前處於公共預覽版。 此預覽版是在沒有服務等級協定的情況下提供，不建議用於生產工作負載。 可能不支援特定功能，或可能已經限制功能。 如需詳細資訊，請參閱 [Microsoft Azure 預覽專用的補充使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

## <a name="ipv6-prefix-sizes"></a>IPv6 首碼大小
以下公共 IP 首碼大小可用：

-  最小 IPv6 首碼大小： /127 = 2 個位址
-  最大 IPv6 首碼大小： /124 = 16 個位址

首碼大小指定為無類域間路由 （CIDR） 遮罩大小。 例如，/128 的遮罩表示單個 IPv6 位址，因為 IPv6 位址由 128 位組成。

## <a name="pricing"></a>定價
 
有關與使用 Azure 公共 IP 相關的成本（包括單個 IP 位址和 IP 範圍），請參閱[公共 IP 位址定價](https://azure.microsoft.com/pricing/details/ip-addresses/)。

## <a name="limitations"></a>限制
IPv6 僅在基本公共 IP 上支援"動態"分配，這意味著如果在 Azure 中刪除和重新部署應用程式（VM 或負載等化器），IPv6 位址將發生更改。 標準 IPv6 公共 IP 僅支援靜態（保留）分配，但標準內部負載等化器還可以支援從分配給它們的子網中進行動態分配。  

作為最佳實踐，我們建議您在 IPv6 應用程式中使用標準公共 IP 和標準負載等化器。

## <a name="next-steps"></a>後續步驟
- 保留公共[IPv6 位址首碼](ipv6-reserve-public-ip-address-prefix.md)。
- 瞭解有關[IPv6 位址的更多。](ipv6-overview.md)
- 瞭解如何在 Azure 中[創建和使用公共](virtual-network-public-ip-address.md)IP（IPv4 和 IPv6）。
