---
title: Azure 事件網格資源的網路安全
description: 本文介紹如何配置來自專用終結點的訪問
services: event-grid
author: VidyaKukke
ms.service: event-grid
ms.topic: conceptual
ms.date: 03/11/2020
ms.author: vkukke
ms.openlocfilehash: ed3b70ad267252981110e7970bc5c5fad6cf4b4b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79300150"
---
# <a name="network-security-for-azure-event-grid-resources"></a>Azure 事件網格資源的網路安全
本文介紹如何使用以下安全功能與 Azure 事件網格： 

- 出口服務標籤（預覽）
- 入口的 IP 防火牆規則（預覽）
- 入口專用終結點（預覽）


## <a name="service-tags"></a>服務標籤
服務標記表示給定 Azure 服務的一組 IP 位址首碼。 Microsoft 管理服務標記包含的位址首碼，並在位址更改時自動更新服務標記，從而最大限度地減少頻繁更新網路安全規則的複雜性。 有關服務標記的詳細資訊，請參閱[服務標記概述](../virtual-network/service-tags-overview.md)。

可以使用服務標記在[網路安全性群組](../virtual-network/security-overview.md#security-rules) 或 [Azure 防火牆](../firewall/service-tags.md)上定義網路訪問控制項。 創建安全規則時，使用服務標記代替特定的 IP 位址。 通過在規則的相應*源* 或 *目標* 欄位中指定服務標記名稱（例如 **，AzureEventGrid），** 可以允許或拒絕相應服務的流量。

| 服務標籤 | 目的 | 可以使用入站或出站嗎？ | 可以是區域性的嗎？ | 可與 Azure 防火牆一起使用？ |
| --- | -------- |:---:|:---:|:---:|:---:|:---:|:---:|:---:|
| Azure 事件網格 | Azure 事件格線。 <br/><br/>*注：* 此標記僅涵蓋美國中南部、美國東部、美國東部 2、美國西部 2 和美國中部中的 Azure 事件網格終結點。 | 兩者 | 否 | 否 |


## <a name="ip-firewall"></a>IP 防火牆 
Azure 事件網格支援基於 IP 的訪問控制項，用於發佈到主題和域。 使用基於 IP 的控制項，可以將發行者限制為主題或域，而只能將一組經過批准的電腦和雲服務。 此功能補充事件網格支援的[身份驗證機制](security-authentication.md)。

預設情況下，只要請求附帶有效的身份驗證和授權，主題和域都可以從 Internet 訪問。 使用 IP 防火牆，您可以進一步將其限制為[CIDR（無類域間路由）](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing)標記法中的一組 IP 位址或 IP 位址範圍。 來自任何其他 IP 位址的發行者將被拒絕，並將收到 403（禁止）回應。


## <a name="private-endpoints"></a>專用終結點
您可以使用[專用終結點](../private-link/private-endpoint-overview.md)允許事件直接從虛擬網路通過[專用鏈路](../private-link/private-link-overview.md)安全地進入您的主題和域，而無需通過公共互聯網。 專用終結點是 VNet 中 Azure 服務的特殊網路介面。 為主題或域創建專用終結點時，它將在 VNet 上的用戶端和事件網格資源之間的用戶端之間提供安全連線。 從 VNet 的 IP 位址範圍為專用終結點分配了 IP 位址。 專用終結點和事件網格服務之間的連接使用安全的專用鏈路。

![架構圖](./media/network-security/architecture-diagram.png)

為事件網格資源使用專用終結點使您能夠：

- 通過 Microsoft 骨幹網路（而不是公共互聯網）從 VNet 安全地訪問您的主題或域。
- 使用 VPN 或快速路由使用專用對等互連從連接到 VNet 的本地網路進行安全連線。

在 VNet 中為主題或域創建專用終結點時，將發送同意請求以向資源擁有者發送核准請求。 如果請求創建私有終結點的使用者也是資源的擁有者，則此同意請求將自動獲得批准。 否則，連接處於**掛起**狀態，直到獲得批准。 VNet 中的應用程式可以使用它們將以其他方式使用的相同連接字串和授權機制，通過專用終結點無縫連接到事件網格服務。 資源擁有者可以通過 Azure 門戶中資源的**專用終結點**選項卡管理同意請求和專用終結點。

### <a name="connect-to-private-endpoints"></a>連接到專用終結點
使用專用終結點的 VNet 上的發行者應對主題或域使用與連接到公共終結點的用戶端相同的連接字串。 DNS 解析通過專用鏈路自動路由從 VNet 到主題或域的連接。 預設情況下，事件網格創建附加到 VNet 的[專用 DNS 區域](../dns/private-dns-overview.md)，並針對專用終結點進行必要的更新。 但是，如果您使用的是自己的 DNS 伺服器，則可能需要對 DNS 配置進行其他更改。

### <a name="dns-changes-for-private-endpoints"></a>專用終結點的 DNS 更改
創建專用終結點時，資源的 DNS CNAME 記錄將更新為子域中的別名，該別名的首碼`privatelink`為 。 預設情況下，將創建與專用連結子域對應的專用 DNS 區域。 

當您使用專用終結點從 VNet 外部解析主題或域終結點 URL 時，它將解析為服務的公共終結點。 從託管專用終結點**的 VNet 外部**解析時，"主題 A"的 DNS 資源記錄將是：

| 名稱                                          | 類型      | 值                                         |
| --------------------------------------------- | ----------| --------------------------------------------- |  
| `topicA.westus.eventgrid.azure.net`             | CNAME     | `topicA.westus.privatelink.eventgrid.azure.net` |
| `topicA.westus.privatelink.eventgrid.azure.net` | CNAME     | \<azure 流量管理器設定檔\>

您可以使用[IP 防火牆](#ip-firewall)通過公共終結點拒絕或控制 VNet 外部用戶端的訪問。 

從託管專用終結點的 VNet 解析時，主題或域終結點 URL 解析為專用終結點的 IP 位址。 主題"主題 A"的 DNS 資源記錄在從託管私有終結點**的 VNet 內部**解析時，將為：

| 名稱                                          | 類型      | 值                                         |
| --------------------------------------------- | ----------| --------------------------------------------- |  
| `topicA.westus.eventgrid.azure.net`             | CNAME     | `topicA.westus.privatelink.eventgrid.azure.net` |
| `topicA.westus.privatelink.eventgrid.azure.net` | A         | 10.0.0.5

此方法允許對承載專用終結點的 VNet 上的用戶端和 VNet 外部的用戶端使用相同的連接字串訪問主題或域。

如果您在網路上使用自訂 DNS 伺服器，用戶端可以將主題的 FQDN 或域終結點解析為專用終結點 IP 位址。 配置 DNS 伺服器以將專用連結子域委派為 VNet 的專用 DNS 區域，或使用專用終結點`topicOrDomainName.regionName.privatelink.eventgrid.azure.net`IP 位址配置 A 記錄。

建議的 DNS 區功能變數名稱稱`privatelink.eventgrid.azure.net`為 。

### <a name="private-endpoints-and-publishing"></a>專用終結點和發佈

下表描述了專用終結點連接的各種狀態及其對發佈的影響：

| 連接狀態   |  成功發佈（是/否） |
| ------------------ | -------------------------------|
| 已核准           | 是                            |
| 已拒絕           | 否                             |
| Pending            | 否                             |
| 已中斷連接       | 否                             |

要成功發佈，應**批准**私有終結點連接狀態。 如果連接被拒絕，則無法使用 Azure 門戶批准連接。 唯一的可能性是刪除連接並創建一個新的連接。

## <a name="pricing-and-quotas"></a>定價和配額
**專用終結點**僅適用于高級層主題和域。 事件網格允許每個主題或域最多創建 64 個專用終結點連接。 要從基本層升級到高級層，請參閱[更新定價層](update-tier.md)一文。

**IP 防火牆**功能在事件網格的基本層和高級層中都可用。 我們允許每個主題或域最多創建 16 個 IP 防火牆規則。


## <a name="next-steps"></a>後續步驟
您可以為事件網格資源配置 IP 防火牆，以便僅從一組選定的 IP 位址或 IP 位址範圍限制通過公共 Internet 進行訪問。 有關分步說明，請參閱配置 IP[防火牆](configure-firewall.md)。

您可以配置專用終結點以僅限制從選定的虛擬網路進行訪問。 有關分步說明，請參閱[配置專用終結點](configure-private-endpoints.md)。
