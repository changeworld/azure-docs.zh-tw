---
title: 關於 Azure ExpressRoute Direct
description: 本頁提供 ExpressRoute Direct 的總覽
services: expressroute
author: jaredr80
ms.service: expressroute
ms.topic: conceptual
ms.date: 08/12/2019
ms.author: jaredro
ms.openlocfilehash: f26fdd75e0a6c6228d329a8b9be4743072ae54c1
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "74083596"
---
# <a name="about-expressroute-direct"></a>關於 ExpressRoute Direct

ExpressRoute Direct 可讓您在策略性分散於世界各地的對等互連位置，直接連線至 Microsoft 的全球網路。 ExpressRoute Direct 提供雙 100 Gbps 或 10 Gbps 的連線能力，可大規模支援主動/主動連線能力。

ExpressRoute Direct 提供的主要功能包括但不限於：

* 將資料大量擷取至儲存體與 Cosmos DB 等服務
* 實體隔離，適用於銀行、政府與零售等受規範且需要專用、隔離之連線的產業
* 根據業務單位決定的細微線路分散控制

## <a name="onboard-to-expressroute-direct"></a>上架至 ExpressRoute Direct

使用 ExpressRoute Direct 之前，您必須先註冊您的訂用帳戶。 若要註冊，請將電子郵件與您的訂用帳戶識別碼傳送至 <ExpressRouteDirect@microsoft.com>，其中包含下列詳細資訊：

* 您想要使用 **ExpressRoute Direct** 完成的案例
* 位置喜好設定 - 請參閱[合作夥伴和對等互連位置](expressroute-locations-providers.md)以取得所有位置的完整清單
* 實作的時間軸
* 有任何其他問題嗎

## <a name="expressroute-using-a-service-provider-and-expressroute-direct"></a>使用服務提供者的 ExpressRoute 和 ExpressRoute Direct

| **使用服務提供者的 ExpressRoute** | **ExpressRoute Direct** | 
| --- | --- |
| 利用服務提供者，可快速登入並連線至現有的基礎結構 | 需要 100 Gbps/10 Gbps 基礎結構及所有層的完整管理
| 與數百個提供者整合，包括乙太網路和 MPLS | 法定產業和大規模資料擷取具有直接/專用容量 |
| 從 50 Mbps 到 10 Gbps 的線路 SKU | 客戶可以在 100 Gbps ExpressRoute Direct 上選取下列線路 Sku 的組合： <ul><li>5 Gbps</li><li>10 Gbps</li><li>40 Gbps</li><li>100 Gbps</li></ul> 客戶可以在 10 Gbps ExpressRoute Direct 上選取下列線路 Sku 的組合：<ul><li>1 Gbps</li><li>2 Gbps</li><li>5 Gbps</li><li>10 Gbps</li></ul>
| 適用於單一租用戶 | 已針對具有多個業務單位和多個工作環境的單一租使用者優化

## <a name="expressroute-direct-circuits"></a>ExpressRoute Direct 線路

Microsoft Azure ExpressRoute 可讓您透過連線提供者所提供的私人連線，將內部部署網路延伸至 Microsoft 雲端。 透過 ExpressRoute，您可以建立與 Microsoft 雲端服務的連線，例如 Microsoft Azure 和 Office 365。

每個對等互連位置皆可存取 Microsoft 的全球網路，且依預設可存取地理政治區域中的任何區域，並且可透過 Premium 線路存取全球各個區域。  

此功能在大部分情況下相當於使用 ExpressRoute 服務提供者運作的線路。 若要進一步支援使用 ExpressRoute Direct 所提供的精細功能和新功能，可利用 ExpressRoute Direct 線路所具備的特定重要功能。

## <a name="circuit-skus"></a>線路 SKU

ExpressRoute Direct 支援將大量資料擷取至 Azure 儲存體與其他巨量資料服務的案例。 100 Gbps ExpressRoute Direct 的 ExpressRoute 線路現在也支援**40 gbps**和**100 Gbps**線路 sku。 實體埠配對僅限**100 或 10 Gbps** ，而且可以有多個虛擬電路。 線路大小：

| **100 Gbps ExpressRoute Direct** | **10 Gbps ExpressRoute Direct** | 
| --- | --- |
| **訂閱的頻寬**： 200 Gbps | **訂閱的頻寬**： 20 Gbps |
| <ul><li>5 Gbps</li><li>10 Gbps</li><li>40 Gbps</li><li>100 Gbps</li></ul> | <ul><li>1 Gbps</li><li>2 Gbps</li><li>5 Gbps</li><li>10 Gbps</li></ul>

## <a name="technical-requirements"></a>技術需求

* Microsoft Enterprise Edge 路由器（MSEE）介面：
    * 只有跨路由器配對的雙10或 100 Gigabit 乙太網路埠
    * 單一模式 LR 光纖連線能力
    * IPv4 和 IPv6
    * IP MTU 1500 位元組

* 交換器/路由器層 2/第3層連線能力：
    * 必須支援 1 802.1 Q （Dot1Q）標記或雙標記 802.1 Q （QinQ）標記封裝
    * Ethertype = 0x8100
    * 必須根據 Microsoft 所指定的 VLAN ID 新增外部 VLAN 標記（STAG）-*僅適用于 QinQ*
    * 必須支援每個埠和裝置有多個 BGP 會話（Vlan）
    * IPv4 和 IPv6 連線能力。 若*為 IPv6，將不會建立額外的子介面。IPv6 位址將會新增至現有的子介面*。 
    * 選擇性：[雙向轉送偵測（BFD）](https://docs.microsoft.com/azure/expressroute/expressroute-bfd)支援，預設會在 ExpressRoute 線路上的所有私用對等互連上設定。

## <a name="vlan-tagging"></a>VLAN 標記

ExpressRoute Direct 支援 QinQ 與 Dot1Q VLAN 標記。

* **QinQ VLAN 標記**允許個別 ExpressRoute 線路上有隔離的路由網域。 Azure 會在線路建立時以動態方式配置 S-Tag，且無法變更。 線路上的每個對等互連 (私人和 Microsoft) 都會以唯一的 C-Tag 作為 VLAN。 C-Tag 在 ExpressRoute Direct 連接埠上的線路間不需要是唯一的。

* **Dot1Q VLAN 標記**允許個別的 ExpressRoute Direct 連接埠配對上可以有單一標記的 VLAN。 對等互連使用的 C-Tag 在所有線路間和 ExpressRoute Direct 連接埠配對的對等互連間都必須是唯一的。

## <a name="workflow"></a>工作流程

[![流會](./media/expressroute-erdirect-about/workflow1.png)](./media/expressroute-erdirect-about/workflow1.png#lightbox)

## <a name="sla"></a>SLA

ExpressRoute Direct 提供與 Microsoft 全球網路的主動/主動備援連線相同的企業級 SLA。 ExpressRoute 基礎結構可作為備援，而 Microsoft 全球網路的連線具有備援性和多樣性，並且可依據客戶的需求進行調整。 

## <a name="next-steps"></a>後續步驟

[設定 ExpressRoute Direct](expressroute-howto-erdirect.md)
