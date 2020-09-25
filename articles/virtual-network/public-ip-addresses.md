---
title: Azure 中的公用 IP 位址
titlesuffix: Azure Virtual Network
description: 了解 Azure 中的公用 IP 位址。
services: virtual-network
documentationcenter: na
author: asudbring
manager: KumudD
ms.service: virtual-network
ms.subservice: ip-services
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/28/2020
ms.author: allensu
ms.openlocfilehash: fbd4c4ecfa2be9815e5d301a02460dc28171716a
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/25/2020
ms.locfileid: "91329256"
---
# <a name="public-ip-addresses"></a>公用 IP 位址

公用 IP 位址可讓網際網路資源向 Azure 資源進行輸入通訊。 公用 IP 位址可讓 Azure 資源與網際網路和公眾面向的 Azure 服務通訊。 位址專用於資源，直到您解除指派為止。 未指派公用 IP 的資源可以進行輸出通訊。 Azure 會動態指派未專門用於資源的可用 IP 位址。 如需 Azure 中的輸出連線詳細資訊，請參閱[了解輸出連線](../load-balancer/load-balancer-outbound-connections.md?toc=%2fazure%2fvirtual-network%2ftoc.json)。

在 Azure 資源管理員中， [公用 IP](virtual-network-public-ip-address.md) 位址是有自己的屬性的資源。 您可以將公用 IP 位址資源與下列相關聯的資源：

* 虛擬機器網路介面
* 網際網路對應負載平衡器
* VPN 閘道
* 應用程式閘道
* Azure 防火牆

## <a name="ip-address-version"></a>IP 位址版本

公用 IP 位址是使用 IPv4 或 IPv6 位址所建立的。 

## <a name="sku"></a>SKU

公用 IP 位址是使用下列其中一個 SKU 所建立的：

>[!IMPORTANT]
> 負載平衡器和公用 IP 資源必須要有相符的 Sku。 您無法將基本 SKU 資源與標準 SKU 資源混用。 您無法將獨立虛擬機器、可用性設定組資源中的虛擬機器或虛擬機器擴展集資源同時連結到這兩個 SKU。  新的設計應該考慮使用標準 SKU 資源。  請檢閱[標準負載平衡器](../load-balancer/load-balancer-standard-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)以取得詳細資料。

### <a name="standard"></a>標準

標準 SKU 公用 IP 位址：

- 一律使用靜態配置方法。
- 讓可調整的輸入起源流量閒置逾時 4 到 30 分鐘 (預設值為 4 分鐘)，固定的輸出起源流量閒置逾時 4 分鐘。
- 預設為安全且已關閉連入流量。 允許列出具有 [網路安全性群組](security-overview.md#network-security-groups)的輸入流量。
- 指派給網路介面、標準公用負載平衡器或應用程式閘道。 如需標準負載平衡器的詳細資訊，請參閱 [Azure Standard Load Balancer](../load-balancer/load-balancer-standard-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)。
- 可以是區域冗余或區域性 (可以建立區域性，並在特定的可用性區域) 中保證。 若要了解可用性區域，請參閱[可用性區域概觀](../availability-zones/az-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)和[標準負載平衡器和可用性區域](../load-balancer/load-balancer-standard-availability-zones.md?toc=%2fazure%2fvirtual-network%2ftoc.json)。
 
> [!NOTE]
> 在建立和關聯[網路安全性群組](security-overview.md#network-security-groups)並明確地允許所要輸入流量前，與標準 SKU 資源進行的輸入通訊會失敗。

> [!NOTE]
> 使用 [實例中繼資料服務 IMDS](../virtual-machines/windows/instance-metadata-service.md)時，只有具有基本 SKU 的公用 IP 位址可供使用。 不支援標準 SKU。

### <a name="basic"></a>基本

在 SKU 推出之前建立的公用 IP 位址全部都是基本 SKU 的公用 IP 位址。 

隨著 Sku 的推出，請指定您想要公用 IP 位址的 SKU。 

基本 SKU 位址：

- 使用靜態或動態配置方法來指派。
- 讓可調整的輸入起源流量閒置逾時 4 到 30 分鐘 (預設值為 4 分鐘)，固定的輸出起源流量閒置逾時 4 分鐘。
- 預設為開放狀態。  建議 (但不強制) 使用網路安全性群組來限制連入或連出流量。
- 指派給任何可以指派公用 IP 位址的 Azure 資源，例如：
    * 網路介面
    * VPN 閘道
    * 應用程式閘道
    * 公用負載平衡器
- 不支援可用性區域案例。 針對可用性區域案例使用標準 SKU 公用 IP。 若要了解可用性區域，請參閱[可用性區域概觀](../availability-zones/az-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)和[標準負載平衡器和可用性區域](../load-balancer/load-balancer-standard-availability-zones.md?toc=%2fazure%2fvirtual-network%2ftoc.json)。

## <a name="allocation-method"></a>配置方法

基本和標準公用 Ip 支援 **靜態** 指派。  資源會在建立時指派 IP 位址。 當刪除資源時，會釋放 IP 位址。

基本 SKU 的公用 IP 位址支援 **動態** 指派。 動態是預設的指派方法。 選取 [動態] 時， **不** 會在建立資源時將 IP 位址提供給資源。

當您將公用 IP 位址資源與建立關聯時，會指派 IP：

* 虛擬機器 
* 第一個虛擬機器與負載平衡器的後端集區相關聯。

此 IP 位址會在您停止 (或刪除) 資源時釋出 。  

例如，公用 IP 資源會從名為 **Resource a**的資源釋出。如果已重新指派公用 IP 資源，則 **資源 A** 會在啟動時收到不同的 ip。

當配置方法從 **靜態** 變更為 **動態**時，就會釋放 IP 位址。 若要確保相關聯資源的 IP 位址維持不變，請明確地將配置方法設定為 **靜態**。 系統會立即指派靜態 IP 位址。

> [!NOTE]
> 即使將配置方法設定為「靜態」****，您也無法指定已指派給公用 IP 位址資源的實際 IP 位址。 Azure 會從資源建立所在的 Azure 位置中可用的 IP 位址集區指派 IP 位址。
>

靜態公用 IP 位址通常用於下列案例：

* 當您必須更新防火牆規則才能與您的 Azure 資源進行通訊時。
* DNS 名稱解析，其中當 IP 位址發生變更時將需要更新 A 記錄。
* 您的 Azure 資源與其他使用 IP 位址型安全性模型的應用程式或服務進行通訊。
* 您可以使用連結至 IP 位址的 TLS/SSL 憑證。

> [!NOTE]
> 在每個 Azure 雲端中，Azure 會從對每個區域來說都是唯一的範圍來配置公用 IP 位址。 您可以針對 Azure [公開](https://www.microsoft.com/download/details.aspx?id=56519)、[US Gov](https://www.microsoft.com/download/details.aspx?id=57063)、[中國](https://www.microsoft.com/download/details.aspx?id=57062)及[德國](https://www.microsoft.com/download/details.aspx?id=57064)雲端，下載範圍 (前置詞) 清單。
>

## <a name="dns-hostname-resolution"></a>DNS 主機名稱解析

選取此選項可指定公用 IP 資源的 DNS 功能變數名稱標籤。 

此選取專案會建立**domainnamelabel**的對應。cloudapp.azure.com 至 Azure 受控 DNS 中的公用**IP。** 

例如，使用下列方式建立公用 IP：

* **contoso** 作為 **domainnamelabel**
* **美國西部** Azure **位置**

 (FQDN) **contoso.westus.cloudapp.azure.com** 的完整功能變數名稱會解析為資源的公用 IP 位址。

> [!IMPORTANT]
> 所建立的每個網域名稱標籤必須是 Azure 位置中唯一的。  
>

## <a name="dns-recommendations"></a>DNS 建議

如果需要移動區域，您將無法遷移公用 IP 的 FQDN。 使用 FQDN 來建立指向公用 IP 位址的自訂 CNAME 記錄。 

如果需要移至不同的公用 IP，請更新 CNAME 記錄，而不是更新 FQDN。

您可以針對 DNS 記錄使用 [Azure DNS](../dns/dns-custom-domain.md?toc=%2fazure%2fvirtual-network%2ftoc.json#public-ip-address) 或外部 dns 提供者。 

## <a name="virtual-machines"></a>虛擬機器

您可以藉由將公用 IP 位址指派給其**網路介面**，以建立其與 [Windows](../virtual-machines/windows/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) 或 [Linux](../virtual-machines/linux/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) 虛擬機器的關聯。 

選擇 [ **動態** ] 或 [ **靜態** ] 作為公用 IP 位址。 深入了解如何[將 IP 位址指派給網路介面](virtual-network-network-interface-addresses.md)。

## <a name="internet-facing-load-balancers"></a>網際網路對應負載平衡器

您可以藉由將任一個 SKU 的公用 IP 位址指派給負載平衡器**前端**設定，使其中一個[SKU](#sku)的公用 IP 位址與[Azure Load Balancer](../load-balancer/load-balancer-overview.md)產生關聯。 公用 IP 可作為負載平衡的 IP。 

您可以將動態或靜態公用 IP 位址指派給負載平衡器前端。 您可以將多個公用 IP 位址指派給負載平衡器前端。 此設定可啟用 [多 VIP](../load-balancer/load-balancer-multivip-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) 案例，例如具有 TLS 型網站的多租使用者環境。 

如需 Azure 負載平衡器 SKU 的詳細資訊，請參閱 [Azure 負載平衡器的標準 SKU](../load-balancer/load-balancer-standard-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)。

## <a name="vpn-gateways"></a>VPN 閘道

[AZURE VPN 閘道](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%2ftoc.json) 會將 azure 虛擬網路連接至：

* Azure 虛擬網路
* 內部部署網路 (s) 。 

公用 IP 位址會指派給 VPN 閘道，以啟用與遠端網路的通訊。 您只可以將「動態」** 基本公用 IP 位址指派給 VPN 閘道。

## <a name="application-gateways"></a>應用程式閘道

您可以將公用 IP 位址指派給閘道的 [前端](../application-gateway/application-gateway-introduction.md?toc=%2fazure%2fvirtual-network%2ftoc.json)組態，以建立其與 Azure **應用程式閘道** 的關聯。 

* 將 **動態** 基本公用 IP 指派給應用程式閘道 V1 前端設定。 
* 將 **靜態** 標準 SKU 位址指派給 V2 前端設定。

## <a name="azure-firewall"></a>Azure 防火牆

[Azure 防火牆](../firewall/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) 可讓您跨訂用帳戶和虛擬網路，建立、強制執行及記錄應用程式和網路連線原則。

您只能將 **靜態** 標準公用 IP 位址與防火牆建立關聯。 這可讓外部防火牆識別來自您虛擬網路的流量。 


## <a name="at-a-glance"></a>快速總覽

下表顯示可透過其將公用 IP 與最上層資源相關聯的屬性，以及可能的配置方法。

| 最上層資源 | IP 位址關聯 | 動態 | Static |
| --- | --- | --- | --- |
| 虛擬機器 |Linux |是 |是 |
| 網際網路對應負載平衡器 |前端組態 |是 |是 |
| VPN 閘道 |閘道 IP 組態 |是 |否 |
| 應用程式閘道 |前端組態 |是 (僅限 V1) |是 (僅限 V2) |
| Azure 防火牆 | 前端組態 | 否 | 是|

## <a name="limits"></a>限制

IP 位址的限制列在 Azure 中的一組完整 [網路限制](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#networking-limits) 中。 

這些限制是針對每一區域和每一訂用帳戶。 [請聯絡支援](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade) 人員，以根據您的業務需求增加最大限制的預設限制。

## <a name="pricing"></a>定價

公用 IP 位址可能需要少許費用。 若要深入了解 IP 位址的價格，請參閱 [IP 位址價格](https://azure.microsoft.com/pricing/details/ip-addresses)頁面。

## <a name="next-steps"></a>後續步驟
* 瞭解 [Azure 中的私人 IP 位址](private-ip-addresses.md)
* [使用 Azure 入口網站部署使用靜態公用 IP 的 VM](virtual-network-deploy-static-pip-arm-portal.md)

