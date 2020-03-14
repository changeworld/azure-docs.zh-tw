---
title: 適用于 Azure 事件方格資源的網路安全性
description: 本文說明如何設定來自私人端點的存取權
services: event-grid
author: VidyaKukke
ms.service: event-grid
ms.topic: conceptual
ms.date: 03/11/2020
ms.author: vkukke
ms.openlocfilehash: ed3b70ad267252981110e7970bc5c5fad6cf4b4b
ms.sourcegitcommit: c29b7870f1d478cec6ada67afa0233d483db1181
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/13/2020
ms.locfileid: "79300150"
---
# <a name="network-security-for-azure-event-grid-resources"></a>適用于 Azure 事件方格資源的網路安全性
本文說明如何搭配 Azure 事件方格使用下列安全性功能： 

- 輸出的服務標記（預覽）
- 輸入的 IP 防火牆規則（預覽）
- 輸入的私用端點（預覽）


## <a name="service-tags"></a>服務標籤
服務標記代表來自指定 Azure 服務的一組 IP 位址首碼。 Microsoft 會管理服務標籤所包含的位址前置詞，並隨著位址變更自動更新服務標籤，將經常更新網路安全性規則的複雜度降到最低。 如需服務標記的詳細資訊，請參閱[服務標記總覽](../virtual-network/service-tags-overview.md)。

您可以使用服務標籤，在 或 [Azure 防火牆](../firewall/service-tags.md)的[網路安全性群組](../virtual-network/security-overview.md#security-rules)上定義網路存取控制。 當您建立安全性規則時，請使用服務標記來取代特定的 IP 位址。 藉由在適當的*來源* 或規則的 [ *目的地* ] 欄位中指定服務標籤名稱（例如， **AzureEventGrid**），您可以允許或拒絕對應服務的流量。

| 服務標記 | 目的 | 可以使用輸入或輸出嗎？ | 可以是地區嗎？ | 可以與 Azure 防火牆搭配使用嗎？ |
| --- | -------- |:---:|:---:|:---:|:---:|:---:|:---:|:---:|
| AzureEventGrid | Azure 事件格線。 <br/><br/>*注意：* 此標記涵蓋美國中南部、美國東部、美國東部2、美國西部2和美國中部的 Azure 事件方格端點。 | 兩者 | 否 | 否 |


## <a name="ip-firewall"></a>IP 防火牆 
Azure 事件方格支援以 IP 為基礎的存取控制，以發佈至主題和網域。 透過 IP 型控制項，您可以將主題或網域的發行者限制為只有一組核准的機器和雲端服務。 這項功能會補充事件方格所支援的[驗證機制](security-authentication.md)。

根據預設，只要要求隨附有效的驗證和授權，就可以從網際網路存取主題和網域。 使用 IP 防火牆，您可以將它進一步限制為[CIDR （無類別網域間路由）](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing)標記法中的一組 ip 位址或 ip 位址範圍。 源自任何其他 IP 位址的發行者將會遭到拒絕，並會收到403（禁止）回應。


## <a name="private-endpoints"></a>私人端點
您可以使用[私用端點](../private-link/private-endpoint-overview.md)，透過[私人連結](../private-link/private-link-overview.md)，直接從虛擬網路將事件輸入到主題和網域，而不需要經過公用網際網路。 私人端點是您 VNet 中 Azure 服務的特殊網路介面。 當您建立主題或網域的私人端點時，它會在您的 VNet 上的用戶端與您的事件方格資源之間提供安全的連線。 私人端點會從 VNet 的 IP 位址範圍指派 IP 位址。 私人端點與事件方格服務之間的連線會使用安全的私用連結。

![架構圖](./media/network-security/architecture-diagram.png)

使用事件方格資源的私用端點可讓您：

- 透過 Microsoft 骨幹網路（而不是公用網際網路），從 VNet 安全地存取您的主題或網域。
- 使用 VPN 或 ExpressRoutes 搭配私用對等互連，安全地從連線到 VNet 的內部部署網路進行連線。

當您為 VNet 中的主題或網域建立私人端點時，會將同意要求傳送給資源擁有者。 如果要求建立私用端點的使用者也是資源的擁有者，則會自動核准此同意要求。 否則，在核准之前，連接會處於**擱置**狀態。 VNet 中的應用程式可以使用相同的連接字串和授權機制，順暢地透過私人端點連接到事件方格服務，否則會使用它們。 資源擁有者可以透過 Azure 入口網站中資源的 [**私人端點**] 索引標籤，來管理同意要求和私人端點。

### <a name="connect-to-private-endpoints"></a>連接到私人端點
使用私用端點之 VNet 上的發行者，應使用與連接至公用端點的用戶端相同的主題或網域連接字串。 DNS 解析會透過私人連結，自動將 VNet 的連線路由至主題或網域。 根據預設，事件方格會建立附加至 VNet 的[私人 DNS 區域](../dns/private-dns-overview.md)，並具有私人端點的必要更新。 不過，如果您是使用自己的 DNS 伺服器，您可能需要對 DNS 設定進行其他變更。

### <a name="dns-changes-for-private-endpoints"></a>私人端點的 DNS 變更
當您建立私人端點時，資源的 DNS CNAME 記錄會更新為 `privatelink`首碼的子域中的別名。 根據預設，會建立一個對應于私人連結子域的私人 DNS 區域。 

當您從具有私用端點的 VNet 外部解析主題或網域端點 URL 時，它會解析為服務的公用端點。 從裝載私用端點**的 VNet 外部**解析時，' topicA ' 的 DNS 資源記錄將會是：

| 名稱                                          | 類型      | 值                                         |
| --------------------------------------------- | ----------| --------------------------------------------- |  
| `topicA.westus.eventgrid.azure.net`             | CNAME     | `topicA.westus.privatelink.eventgrid.azure.net` |
| `topicA.westus.privatelink.eventgrid.azure.net` | CNAME     | \<azure 流量管理員設定檔\>

您可以透過使用[IP 防火牆](#ip-firewall)的公用端點，拒絕或控制 VNet 外部的用戶端存取。 

從裝載私用端點的 VNet 解析時，主題或網域端點 URL 會解析為私人端點的 IP 位址。 從裝載私用端點**的 VNet 內部**解析主題 ' topicA ' 的 DNS 資源記錄時，將會是：

| 名稱                                          | 類型      | 值                                         |
| --------------------------------------------- | ----------| --------------------------------------------- |  
| `topicA.westus.eventgrid.azure.net`             | CNAME     | `topicA.westus.privatelink.eventgrid.azure.net` |
| `topicA.westus.privatelink.eventgrid.azure.net` | A         | 10.0.0.5

這種方法可讓您使用相同的連接字串，針對裝載私人端點的 VNet 和 VNet 外的用戶端，存取主題或網域。

如果您是在網路上使用自訂 DNS 伺服器，用戶端可以將主題或網域端點的 FQDN 解析為私人端點 IP 位址。 將您的 DNS 伺服器設定為將私人連結子域委派給 VNet 的私人 DNS 區域，或設定具有私人端點 IP 位址之 `topicOrDomainName.regionName.privatelink.eventgrid.azure.net` 的 A 記錄。

建議的 DNS 區功能變數名稱稱為 `privatelink.eventgrid.azure.net`。

### <a name="private-endpoints-and-publishing"></a>私人端點和發行

下表說明私人端點連線的各種狀態，以及發行時的影響：

| 連接狀態   |  已成功發佈（是/否） |
| ------------------ | -------------------------------|
| 已核准           | 是                            |
| 已拒絕           | 否                             |
| Pending            | 否                             |
| 已中斷連接       | 否                             |

若要成功發行，應**核准**私人端點線上狀態。 如果連接遭到拒絕，則無法使用 Azure 入口網站核准。 唯一的可能性是刪除連線並改為建立新的連接。

## <a name="pricing-and-quotas"></a>定價和配額
**私人端點**僅適用于高階層主題和網域。 事件方格允許每個主題或網域建立最多64個私人端點連線。 若要從基本層升級至高階層，請參閱[更新定價層](update-tier.md)一文。

「 **IP 防火牆**」功能適用于事件方格的「基本」和「高階」層級。 我們最多允許為每個主題或網域建立16個 IP 防火牆規則。


## <a name="next-steps"></a>後續步驟
您可以設定事件方格資源的 IP 防火牆，以限制只能從一組選取的 IP 位址或 IP 位址範圍存取公用網際網路。 如需逐步指示，請參閱[設定 IP 防火牆](configure-firewall.md)。

您可以設定私人端點，以限制只有來自所選虛擬網路的存取權。 如需逐步指示，請參閱[設定私人端點](configure-private-endpoints.md)。
