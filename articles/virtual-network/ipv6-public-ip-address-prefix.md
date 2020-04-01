---
title: Azure 虛擬網路中的公共 IPv6 位址前置碼
titlesuffix: Azure Virtual Network
description: 瞭解 Azure 虛擬網路中的公共 IPv6 位址前置碼。
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
ms.sourcegitcommit: 7581df526837b1484de136cf6ae1560c21bf7e73
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/31/2020
ms.locfileid: "80420531"
---
# <a name="reserved-public-ipv6-address-prefix"></a>保留公共 IPv6 位址前置字串

在 Azure 中,雙堆疊 (IPv4_IPv6) 虛擬網路 (VNet) 和虛擬機器 (VM) 預設是安全的,因為它們沒有網路連接。 您可以使用從 Azure 獲取的公共 IPv6 位址輕鬆向 Azure 負載均衡器和 VM 添加 IPv6 Internet 連接。

您保留的任何公共 IP 都與您選擇的 Azure 區域和 Azure 訂閱相關聯。 您可以在訂閱中的任何 Azure 負載均衡器或 VM 之間移動保留的(靜態)IPv6 公共 IP。 您可以完全分離 IPv6 公共 IP,並在準備就緒時將其持有供您使用。

> [!WARNING]
> 請謹慎不要意外刪除公共 IP 位址。 刪除公共 IP 會將其從訂閱中刪除,您將無法恢復它(即使在 Azure 支援的説明下也是如此)。

除了保留單個 IPv6 位址外,還可以保留連續範圍的 Azure IPv6 位址(稱為 IP 首碼)供使用。  與單個 IP 位址類似,保留前置碼與您選擇的 Azure 區域和 Azure 訂閱相關聯。 保留可預測、連續的位址範圍有許多用途。 例如,您可以大大簡化公司和客戶對 Azure 託管應用程式的 IP*白名單,* 因為靜態 IP 範圍可以很容易地程式設計到本地防火牆中。  您可以根據需要從 IP 首碼建立單個公共 IP,當您刪除這些單獨的公共 IP 時,它們*將傳回到*您的保留範圍,以便以後可以重複使用它們。 在刪除前置前置之前,IP 前置字串的所有 IP 位址都保留供獨佔使用。



## <a name="ipv6-prefix-sizes"></a>IPv6 前置大小
以下公共 IP 前置大小可用:

-  最小 IPv6 preprem: /127 = 2 個位址
-  最大 IPv6 preprele: /124 = 16 個位址

首碼大小指定為無類域間路由 (CIDR) 遮罩大小。 例如,/128 的遮罩表示單個IPv6位址,因為IPv6位址由128位組成。

## <a name="pricing"></a>定價
 
有關與使用 Azure 公共 IP 相關的成本(包括單一 IP 位址和 IP 範圍),請參考[公共 IP 位址定價](https://azure.microsoft.com/pricing/details/ip-addresses/)。

## <a name="limitations"></a>限制
IPv6 僅在基本公共 IP 上支援"動態"分配,這意味著如果在 Azure 中刪除和重新部署應用程式(VM 或負載均衡器),IPv6 位址將發生更改。 標準 IPv6 公共 IP 僅支援靜態(保留)分配,但標準內部負載均衡器還可以支援從分配給它們的子網中進行動態分配。  

作為最佳實務,我們建議您在IPv6應用程式中使用標準公共IP和標準負載均衡器。

## <a name="next-steps"></a>後續步驟
- 保留公共[IPv6 位址前置字串](ipv6-reserve-public-ip-address-prefix.md)。
- 瞭解有關[IPv6 位址的更多。](ipv6-overview.md)
- 瞭解如何在 Azure 中[創建和使用公共](virtual-network-public-ip-address.md)IP(IPv4 和 IPv6)。
