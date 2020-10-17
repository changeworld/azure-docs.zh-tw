---
title: 適用于 Azure 事件方格資源的網路安全性
description: 本文說明如何針對輸出使用服務標記、輸入的 IP 防火牆規則，以及使用 Azure 事件方格輸入的私人端點。
author: VidyaKukke
ms.topic: conceptual
ms.date: 07/07/2020
ms.author: vkukke
ms.openlocfilehash: 84336051fc3d653fbe73f650f2fc2badb2ec58da
ms.sourcegitcommit: dbe434f45f9d0f9d298076bf8c08672ceca416c6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/17/2020
ms.locfileid: "92148929"
---
# <a name="network-security-for-azure-event-grid-resources"></a>適用于 Azure 事件方格資源的網路安全性
本文說明如何搭配 Azure 事件方格使用下列安全性功能： 

- 輸出的服務標記
- 輸入 (預覽) 的 IP 防火牆規則
- 輸入的私人端點


## <a name="service-tags"></a>服務標籤
服務標籤代表來自指定 Azure 服務的一組 IP 位址前置詞。 Microsoft 會管理服務標籤包含的位址前置詞，並隨著位址變更自動更新服務標籤，而盡可能簡化網路安全性規則頻繁的更新。 如需服務標籤的詳細資訊，請參閱 [服務標記總覽](../virtual-network/service-tags-overview.md)。

您可以使用服務標籤來定義[網路安全性群組](../virtual-network/network-security-groups-overview.md#security-rules) 或 [Azure 防火牆](../firewall/service-tags.md)的網路存取控制。 建立安全性規則時，請以服務標籤取代特定的 IP 位址。 藉由指定服務標籤名稱 (例如， **AzureEventGrid**) 在規則的適當*來源*   或 *目的地*   欄位中，您可以允許或拒絕對應服務的流量。

| 服務標籤 | 目的 | 可以使用輸入還是輸出？ | 是否可為區域性？ | 是否可與 Azure 防火牆搭配使用？ |
| --- | -------- |:---:|:---:|:---:|
| AzureEventGrid | Azure 事件格線。 | 兩者 | 否 | 否 |


## <a name="ip-firewall"></a>IP 防火牆 
Azure Event Grid 支援以 IP 為基礎的存取控制，以發佈至主題和網域。 使用以 IP 為基礎的控制項時，您可以將一組核准的電腦和雲端服務的發行者限制為僅限一個主題或網域。 這項功能可以補充事件方格所支援的 [驗證機制](security-authentication.md) 。

根據預設，只要要求提供有效的驗證與授權，就可以從網際網路存取主題和網域。 使用 IP 防火牆，您可以將它進一步限制為 [CIDR (無類別 Inter-Domain 路由) ](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing) 標記法中的一組 ip 位址或 ip 位址範圍。 源自其他任何 IP 位址的發行者將會遭到拒絕，且會收到 403 (禁止的) 回應。

如需為主題和網域設定 IP 防火牆的逐步指示，請參閱 [設定 ip 防火牆](configure-firewall.md)。

## <a name="private-endpoints"></a>私人端點
您可以使用 [私人端點](../private-link/private-endpoint-overview.md) 來安全地透過 [私人連結](../private-link/private-link-overview.md) ，直接從您的虛擬網路將事件輸入至主題和網域，而不需經過公用網際網路。 私人端點是您 VNet 中 Azure 服務的特殊網路介面。 當您為主題或網域建立私人端點時，它會在您的 VNet 上的用戶端與您的事件方格資源之間提供安全的連線能力。 私人端點會從 VNet 的 IP 位址範圍中指派 IP 位址。 私人端點與事件方格服務之間的連接會使用安全的私用連結。

![架構圖](./media/network-security/architecture-diagram.png)

針對您的事件方格資源使用私用端點，可讓您：

- 透過 Microsoft 骨幹網路的 VNet 安全地存取您的主題或網域，而不是公用網際網路。
- 使用 VPN 或 Expressroute 搭配私人對等互連，安全地從連線到 VNet 的內部部署網路進行連線。

當您為 VNet 中的主題或網域建立私人端點時，會傳送同意要求以供核准給資源擁有者。 如果要求建立私人端點的使用者也是資源的擁有者，則會自動核准此同意要求。 否則，連接會處於 **擱置** 狀態，直到核准為止。 VNet 中的應用程式可以使用相同的連接字串和其使用的授權機制，順暢地透過私人端點連接到事件方格服務。 資源擁有者可以透過 Azure 入口網站中資源的 [ **私人端點** ] 索引標籤，來管理同意要求和私人端點。

### <a name="connect-to-private-endpoints"></a>連接至私人端點
使用私人端點之 VNet 上的發行者應該使用與主題或網域相同的連接字串，做為連接到公用端點的用戶端。 DNS 解析會透過私人連結，自動將來自 VNet 的連線路由傳送至主題或網域。 根據預設，事件方格會建立附加至 VNet 的 [私人 DNS 區域](../dns/private-dns-overview.md) ，並具有私人端點的必要更新。 但是，如果您使用自己的 DNS 伺服器，您可能需要對 DNS 設定進行額外的變更。

### <a name="dns-changes-for-private-endpoints"></a>私人端點的 DNS 變更
當您建立私人端點時，會將資源的 DNS CNAME 記錄更新為具有前置詞之子域中的別名 `privatelink` 。 根據預設，會建立對應至私用連結子域的私人 DNS 區域。 

當您透過私人端點從 VNet 外部解析主題或網域端點 URL 時，它會解析為服務的公用端點。 從裝載私人端點 **的 VNet 外部** 解析時，' topicA ' 的 DNS 資源記錄會是：

| 名稱                                          | 類型      | 值                                         |
| --------------------------------------------- | ----------| --------------------------------------------- |  
| `topicA.westus.eventgrid.azure.net`             | CNAME     | `topicA.westus.privatelink.eventgrid.azure.net` |
| `topicA.westus.privatelink.eventgrid.azure.net` | CNAME     | \<Azure traffic manager profile\>

您可以透過使用 [IP 防火牆](#ip-firewall)的公用端點，拒絕或控制 VNet 外部用戶端的存取。 

從裝載私人端點的 VNet 解析時，主題或網域端點 URL 會解析為私人端點的 IP 位址。 從裝載私人端點的 **VNet 內部** 解析時，主題 ' topicA ' 的 DNS 資源記錄會是：

| 名稱                                          | 類型      | 值                                         |
| --------------------------------------------- | ----------| --------------------------------------------- |  
| `topicA.westus.eventgrid.azure.net`             | CNAME     | `topicA.westus.privatelink.eventgrid.azure.net` |
| `topicA.westus.privatelink.eventgrid.azure.net` | A         | 10.0.0.5

這種方法可讓您使用相同的連接字串，對裝載私人端點的 VNet 上的用戶端，以及 VNet 外部的用戶端，使用相同的連接字串來存取主題或網域。

如果您是在網路上使用自訂 DNS 伺服器，用戶端可以將主題或網域端點的 FQDN 解析為私人端點 IP 位址。 設定您的 DNS 伺服器，以將私人連結子域委派給 VNet 的私人 DNS 區域，或設定 `topicOrDomainName.regionName.privatelink.eventgrid.azure.net` 具有私人端點 IP 位址的 A 記錄。

建議的 DNS 區功能變數名稱稱為 `privatelink.eventgrid.azure.net` 。

### <a name="private-endpoints-and-publishing"></a>私人端點和發佈

下表說明私人端點連接的各種狀態，以及發佈的影響：

| 連接狀態   |  已成功發佈 (是/否)  |
| ------------------ | -------------------------------|
| 已核准           | 是                            |
| 已拒絕           | 否                             |
| Pending            | 否                             |
| 已中斷連接       | 否                             |

若要讓發佈成功，應 **核准**私人端點連接狀態。 如果連線遭到拒絕，則無法使用 Azure 入口網站核准。 唯一的可能性是刪除連接，並改為建立新的連線。

## <a name="pricing-and-quotas"></a>定價和配額
**私人端點** 適用于事件方格的基本層和進階層。 事件方格最多可為每個主題或網域建立64私人端點連接。 

「 **IP 防火牆**」功能可在「事件方格」的基本層和進階層中使用。 每個主題或網域最多可以建立16個 IP 防火牆規則。

## <a name="next-steps"></a>後續步驟
您可以設定事件方格資源的 IP 防火牆，以限制只有一組選取的 IP 位址或 IP 位址範圍，才能從公用網際網路存取。 如需逐步指示，請參閱 [設定 IP 防火牆](configure-firewall.md)。

您可以設定私人端點，以限制只能從選取的虛擬網路存取。 如需逐步指示，請參閱 [設定私人端點](configure-private-endpoints.md)。

若要疑難排解網路連線問題，請參閱 [疑難排解網路連線問題](troubleshoot-network-connectivity.md)