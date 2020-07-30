---
title: Azure 公用 IP 位址首碼
description: 了解什麼是 Azure 公用 IP 位址首碼，以及它如何協助您為資源指派可預測的的公用 IP 位址。
services: virtual-network
documentationcenter: na
author: asudbring
manager: KumudD
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.subservice: ip-services
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/29/2020
ms.author: allensu
ms.openlocfilehash: 53dd6d2dda762b3cbf53f4aaec6cd3692a9656e9
ms.sourcegitcommit: 0b8320ae0d3455344ec8855b5c2d0ab3faa974a3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/30/2020
ms.locfileid: "87432581"
---
# <a name="public-ip-address-prefix"></a>公用 IP 位址首碼

公用 IP 位址首碼是 Azure 中的 IP 位址保留範圍。 Azure 會根據您指定的數量，為您的訂用帳戶提供連續的位址範圍。 

如果您不熟悉如何使用公用位址，請參閱[公用 IP 位址](virtual-network-ip-addresses-overview-arm.md#public-ip-addresses)。

將會從每個 Azure 區域的位址集區指派公用 IP 位址。 您可以[下載](https://www.microsoft.com/download/details.aspx?id=56519) Azure 用於每個區域的範圍清單。 例如，40.121.0.0/16 是 Azure 在美國東部區域使用的 100 多個範圍之一。 此範圍也包括 40.121.0.1 - 40.121.255.254 的可用位址。

您可以透過指定名稱與希望首碼包含的位址數目，在 Azure 區域與訂用帳戶中建立公用 IP 位址首碼。 

系統會使用您選擇的前置詞來指派公用 IP 位址範圍。 如果您建立/28 的前置詞，Azure 會從它的其中一個範圍提供16個 ip 位址。

在建立範圍之前，您不知道 Azure 會指派哪一個範圍，但位址是連續的。 

公用 IP 位址首碼有費用，如需詳細資訊，請參閱[公用 ip 位址定價](https://azure.microsoft.com/pricing/details/ip-addresses)。

## <a name="why-create-a-public-ip-address-prefix"></a>為什麼要建立公用 IP 位址首碼？

當您建立公用 IP 位址資源時，Azure 會從該區域中使用的任何範圍指派可用的公用 IP 位址。 

在 Azure 指派 IP 位址之前，您將不會知道確切的 IP。 當您建立允許特定 IP 位址的防火牆規則時，此程式可能會造成問題。 針對每個新增的 IP 位址，必須新增對應的防火牆規則。

當您從公用 IP 位址首碼將位址指派給您的資源時，不需要防火牆規則更新。 整個範圍都會新增至規則。

## <a name="benefits"></a>優點

- 從已知範圍建立公用 IP 位址資源。
- 防火牆規則設定的範圍包括您目前指派的公用 IP 位址，以及尚未指派的位址。 當您將 IP 位址指派給新資源時，此設定不需要變更防火牆規則。
- 您可以建立之範圍的預設大小是 /28 或 16 個 IP 位址。
- 您可以建立的範圍數目並不受限。 您可以在 Azure 訂用帳戶中擁有的靜態公用 IP 位址數目上限有所限制。 您所建立的範圍數目不能超過您的訂用帳戶中所能擁有的靜態公用 IP 位址。 如需詳細資訊，請參閱 [Azure 限制](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits)。
- 使用首碼中的位址建立的位址，可以指派給任何可為其指派公用 IP 位址的 Azure 資源。
- 您可以輕鬆地查看範圍內指定和未提供的 IP 位址。

## <a name="scenarios"></a>案例
您可以將下列資源與首碼中的靜態公用 IP 位址產生關聯：

|資源|狀況|步驟|
|---|---|---|
|虛擬機器| 將來自首碼的公用 Ip 與您在 Azure 中的虛擬機器建立關聯時，會在將 IP 位址新增至防火牆的允許清單時，降低管理負擔。 您可以使用單一防火牆規則來新增完整的前置詞。 當您在調整 Azure 中的虛擬機器規模時，可以關聯來自相同首碼的 IP，進而節省成本、時間與管理額外負荷。| 將首碼中的 IP 與您的虛擬機器產生關聯： </br> 1.[建立前置詞。](manage-public-ip-address-prefix.md) </br> 2.[建立首碼中的 IP。](manage-public-ip-address-prefix.md) </br> 3.[將 IP 與虛擬機器的網路介面建立關聯。](virtual-network-network-interface-addresses.md#add-ip-addresses) </br> 您也可以[將 ip 與虛擬機器擴展集建立關聯](https://azure.microsoft.com/resources/templates/101-vmms-with-public-ip-prefix/)。
| 標準負載平衡器 | 將來自首碼的公用 Ip 與前端 IP 設定或負載平衡器的輸出規則建立關聯，可確保您的 Azure 公用 IP 位址空間的簡化。 清理連續 IP 位址範圍中的輸出連線，以簡化您的案例。 | 若要將首碼中的 Ip 與您的負載平衡器產生關聯： </br> 1.[建立前置詞。](manage-public-ip-address-prefix.md) </br> 2.[建立首碼中的 IP。](manage-public-ip-address-prefix.md) </br> 3. 建立負載平衡器時，請選取或更新在上述步驟2中建立的 IP，作為負載平衡器的前端 IP。 |
| Azure 防火牆 | 您可以為連出 SNAT 使用首碼中的公用 IP。 所有輸出虛擬網路流量都會轉譯為[Azure 防火牆](../firewall/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)公用 IP。 | 若要將首碼中的 IP 與您的防火牆建立關聯： </br> 1.[建立前置詞。](manage-public-ip-address-prefix.md) </br> 2.[建立首碼中的 IP。](manage-public-ip-address-prefix.md) </br> 3. 當您[部署 Azure 防火牆](../firewall/tutorial-firewall-deploy-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json#deploy-the-firewall)時，請務必選取您先前從前置詞提供的 IP。|
| 應用程式閘道 v2 | 您可以針對自動調整和區域多餘的應用程式閘道 v2，使用首碼中的公用 IP。 | 若要將首碼中的 IP 與您的閘道建立關聯： </br> 1.[建立前置詞。](manage-public-ip-address-prefix.md) </br> 2.[建立首碼中的 IP。](manage-public-ip-address-prefix.md) </br> 3. 當您[部署應用程式閘道](../application-gateway/quick-create-portal.md#create-an-application-gateway)時，請務必選取您先前從前置詞提供的 IP。|

## <a name="constraints"></a>條件約束

- 您無法指定首碼的 IP 位址。 Azure 會根據您指定的大小，提供首碼的 IP 位址。
- 根據預設，您可以建立最多16個 IP 位址或/28 的前置詞。 如需詳細資訊，請參閱[網路限制增加要求](https://docs.microsoft.com/azure/azure-portal/supportability/networking-quota-requests)和[Azure 限制](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits)。
- 建立首碼之後，就無法變更範圍。
- 只能從首碼的範圍指派使用標準 SKU 建立的靜態公用 IP 位址。 若要深入了解公用 IP 位址 SKU，請參閱[公用 IP 位址](virtual-network-ip-addresses-overview-arm.md#public-ip-addresses)。
- 範圍中的位址只能指派給 Azure Resource Manager 資源。 位址無法指派給傳統部署模型中的資源。
- 從前置詞建立的所有公用 IP 位址，都必須存在於相同的 Azure 區域和訂用帳戶中做為前置詞。 位址必須指派給相同地區和訂用帳戶中的資源。
- 如果首碼中的任何位址指派給與資源相關聯的公用 IP 位址資源，則無法刪除該首碼。 首先，中斷已指派來自首碼 IP 位址之所有公用 IP 位址資源的關聯。


## <a name="next-steps"></a>後續步驟

- [建立](manage-public-ip-address-prefix.md)公用 IP 位址首碼
