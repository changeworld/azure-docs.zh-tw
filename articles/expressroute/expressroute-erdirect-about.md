---
title: 關於 Azure 快速路由直接
description: 本頁提供快速路由直接的概述
services: expressroute
author: jaredr80
ms.service: expressroute
ms.topic: conceptual
ms.date: 08/12/2019
ms.author: jaredro
ms.openlocfilehash: f26fdd75e0a6c6228d329a8b9be4743072ae54c1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "74083596"
---
# <a name="about-expressroute-direct"></a>關於 ExpressRoute Direct

ExpressRoute Direct 可讓您在策略性分散於世界各地的對等互連位置，直接連線至 Microsoft 的全球網路。 ExpressRoute Direct 提供雙 100 Gbps 或 10 Gbps 連接，支援大規模主動/主動連接。

ExpressRoute Direct 提供的主要功能包括但不限於：

* 將資料大量擷取至儲存體與 Cosmos DB 等服務
* 實體隔離，適用於銀行、政府與零售等受規範且需要專用、隔離之連線的產業
* 根據業務單位決定的細微線路分散控制

## <a name="onboard-to-expressroute-direct"></a>登機到快速路由直接

在使用 ExpressRoute Direct 之前，您必須首先註冊訂閱。 若要註冊，請將電子郵件與您的訂用帳戶識別碼傳送至 <ExpressRouteDirect@microsoft.com>，其中包含下列詳細資訊：

* 您想要使用 **ExpressRoute Direct** 完成的案例
* 位置喜好設定 - 請參閱[合作夥伴和對等互連位置](expressroute-locations-providers.md)以取得所有位置的完整清單
* 實作的時間軸
* 有任何其他問題嗎

## <a name="expressroute-using-a-service-provider-and-expressroute-direct"></a>使用服務提供者的 ExpressRoute 和 ExpressRoute Direct

| **使用服務提供者的 ExpressRoute** | **ExpressRoute Direct** | 
| --- | --- |
| 利用服務提供者，可快速登入並連線至現有的基礎結構 | 需要 100 Gbps/10 Gbps 基礎架構和所有層的完全管理
| 與數百個提供者整合，包括乙太網路和 MPLS | 法定產業和大規模資料擷取具有直接/專用容量 |
| 從 50 Mbps 到 10 Gbps 的線路 SKU | 客戶可在 100 Gbps ExpressRoute Direct 上選擇以下電路 SKU 的組合： <ul><li>5 Gbps</li><li>10 Gbps</li><li>40 Gbps</li><li>100 Gbps</li></ul> 客戶可在 10 Gbps ExpressRoute Direct 上選擇以下電路 SKU 的組合：<ul><li>1 Gbps</li><li>2 Gbps</li><li>5 Gbps</li><li>10 Gbps</li></ul>
| 適用於單一租用戶 | 針對具有多個業務單位和多個工作環境的單租戶進行了優化

## <a name="expressroute-direct-circuits"></a>ExpressRoute Direct 線路

Microsoft Azure ExpressRoute 可讓您透過連線提供者所提供的私人連線，將內部部署網路延伸至 Microsoft 雲端。 使用 ExpressRoute，您可以建立與 Microsoft 雲服務（如 Microsoft Azure 和 Office 365）的連接。

每個對等互連位置皆可存取 Microsoft 的全球網路，且依預設可存取地理政治區域中的任何區域，並且可透過 Premium 線路存取全球各個區域。  

此功能在大部分情況下相當於使用 ExpressRoute 服務提供者運作的線路。 若要進一步支援使用 ExpressRoute Direct 所提供的精細功能和新功能，可利用 ExpressRoute Direct 線路所具備的特定重要功能。

## <a name="circuit-skus"></a>線路 SKU

ExpressRoute Direct 支援將大量資料擷取至 Azure 儲存體與其他巨量資料服務的案例。 100 Gbps ExpressRoute Direct 上的 ExpressRoute 電路現在也支援**40 Gbps**和**100 Gbps**電路 SKU。 物理埠對僅為**100 或 10 Gbps，** 可以有多個虛擬電路。 電路尺寸：

| **100 Gbps 快速路由直接** | **10 Gbps 快速路由直接** | 
| --- | --- |
| **訂閱頻寬**： 200 Gbps | **訂閱頻寬**： 20 Gbps |
| <ul><li>5 Gbps</li><li>10 Gbps</li><li>40 Gbps</li><li>100 Gbps</li></ul> | <ul><li>1 Gbps</li><li>2 Gbps</li><li>5 Gbps</li><li>10 Gbps</li></ul>

## <a name="technical-requirements"></a>技術需求

* 微軟企業邊緣路由器 （MSEE） 介面：
    * 雙 10 或 100 千兆乙太網埠僅跨路由器對
    * 單模式 LR 光纖連接
    * IPv4 和 IPv6
    * IP MTU 1500 位元組

* 交換器/路由器第 2 層/第 3 層連接：
    * 必須支援 1 802.1Q （Dot1Q） 標籤或兩個標籤 802.1Q （QinQ） 標籤封裝
    * 醚型 = 0x8100
    * 必須添加基於 Microsoft 指定的 VLAN ID 的外部 VLAN 標記 （STAG） -*僅適用于 QinQ*
    * 每個埠和設備必須支援多個 BGP 會話 （VLAN）
    * IPv4 和 IPv6 連接。 *對於 IPv6，不會創建額外的子介面。IPv6 位址將添加到現有的子介面*。 
    * 可選：[雙向轉發檢測 （BFD）](https://docs.microsoft.com/azure/expressroute/expressroute-bfd)支援，預設情況下在 ExpressRoute 電路上的所有專用對等互連上配置

## <a name="vlan-tagging"></a>VLAN 標記

ExpressRoute Direct 支援 QinQ 與 Dot1Q VLAN 標記。

* **QinQ VLAN 標記**允許個別 ExpressRoute 線路上有隔離的路由網域。 Azure 會在線路建立時以動態方式配置 S-Tag，且無法變更。 線路上的每個對等互連 (私人和 Microsoft) 都會以唯一的 C-Tag 作為 VLAN。 C-Tag 在 ExpressRoute Direct 連接埠上的線路間不需要是唯一的。

* **Dot1Q VLAN 標記**允許個別的 ExpressRoute Direct 連接埠配對上可以有單一標記的 VLAN。 對等互連使用的 C-Tag 在所有線路間和 ExpressRoute Direct 連接埠配對的對等互連間都必須是唯一的。

## <a name="workflow"></a>工作流程

[![流程](./media/expressroute-erdirect-about/workflow1.png)](./media/expressroute-erdirect-about/workflow1.png#lightbox)

## <a name="sla"></a>SLA

ExpressRoute Direct 提供與 Microsoft 全球網路的主動/主動備援連線相同的企業級 SLA。 ExpressRoute 基礎結構可作為備援，而 Microsoft 全球網路的連線具有備援性和多樣性，並且可依據客戶的需求進行調整。 

## <a name="next-steps"></a>後續步驟

[設定 ExpressRoute Direct](expressroute-howto-erdirect.md)
