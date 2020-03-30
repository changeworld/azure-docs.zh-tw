---
title: 將 Azure 連接到公共雲 |微軟文檔
description: 描述將 Azure 連接到其他公共雲的各種方法
services: expressroute
author: osamazia
ms.service: expressroute
ms.topic: article
ms.date: 07/24/2019
ms.author: osamaz
ms.openlocfilehash: b8a454c2a104dfe8545cf734bf0b020b8f749bb1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "73889635"
---
# <a name="connecting-azure-with-public-clouds"></a>將 Azure 與公共雲連接

許多企業由於業務和技術目標而正在實施多雲戰略。 其中包括成本、靈活性、功能可用性、冗余、資料主權等。此策略説明他們充分利用兩種雲。 

這種方法也給企業在網路和應用程式體系結構方面帶來了挑戰。 其中一些挑戰是延遲和資料輸送量。 為了應對這些挑戰，客戶希望直接連接到多個雲。 某些服務提供者提供了一種解決方案，用於為客戶連接多個雲供應商。 在其他情況下，客戶可以部署自己的路由器來連接多個公共雲。
## <a name="connectivity-via-expressroute"></a>通過快速路由連接
ExpressRoute 允許客戶通過連接供應商推動的專用連線將其本地網路擴展到 Microsoft 雲中。 使用 ExpressRoute，客戶可以建立與 Microsoft 雲服務的連接。

可通過快速路由進行連接的有三種方法。

1. 第 3 層提供程式
2. 第 2 層提供程式
3. 直接連接

### <a name="layer3-provider"></a>第 3 層提供程式

第 3 層提供程式通常稱為 IP VPN 或 MPLS VPN 提供程式。 客戶利用這些供應商在其資料中心、分支機搆和雲之間實現多點連接。 客戶通過 BGP 或通過靜態預設路由連接到 L3 提供程式。 服務提供者在客戶網站、資料中心和公共雲之間通告路由。 
 
通過第 3 層提供程式進行連接時，Microsoft 將通過 BGP 向服務提供者通告客戶 VNET 路由。 提供程式可以有兩個不同的實現。

![](media/expressroute-connect-azure-to-public-cloud/azure-to-public-clouds-l3.png)

如果來自所有雲供應商的流量都將到達客戶路由器，供應商可能會將每個雲供應商降落在單獨的 VRF 中。 如果客戶與服務提供者一起運行 BGP，則預設情況下這些路由將重新通告給其他雲供應商。 

如果服務提供者將所有雲供應商都在同一 VRF 中登陸，則路由將從服務提供者直接通告給其他雲供應商。 這是假設標準 BGP 操作，預設情況下將 eBGP 路由通告給其他 eBGP 鄰居。

每個公共雲都有不同的首碼限制，因此分發路由時，服務提供者在分發路由時應小心謹慎。

### <a name="layer2-provider-and-direct-connection"></a>第 2 層提供程式和直接連接

雖然兩種型號的物理連接是不同的，但在第3層BGP中，BGP直接在MSEE和客戶路由器之間建立。 對於快速路由直接客戶直接連接到 MSEE。 在第 2 層的情況下，服務提供者將 VLAN 從客戶場所擴展到雲。 客戶在 2 層網路頂部運行 BGP，將其 DC 連接到雲。
![](media/expressroute-connect-azure-to-public-cloud/azure-to-public-clouds-l2.png)
在這兩種情況下，客戶都將與每個公共雲建立點對點連接。 客戶將建立與每個公共雲的單獨 BGP 連接。 預設情況下，一個雲供應商接收的路由將通告給其他雲供應商。 每個雲供應商都有不同的首碼限制，因此在發佈路由廣告時，客戶應注意這些限制。 客戶可以使用微軟的常用 BGP 旋鈕，同時從其他公共雲發佈廣告路線。

## <a name="direct-connection-with-expressroute"></a>直接連接到快速路由

客戶可以選擇將 ExpressRoute 直接連接到雲供應商的直接連接產品。 兩個雲供應商將背對背連接，BGP 將直接在其路由器之間建立。 這種類型的連接目前可供 Oracle 使用。

## <a name="site-to-site-vpn"></a>站對站 VPN

客戶可以利用 Internet 將其在 Azure 中的實例與其他公共雲連接。 幾乎所有的雲供應商都提供網站到網站 VPN 功能。 然而，由於缺乏某些變種，可能會有不相容。 例如，某些雲供應商僅支援 IKEv1，因此該雲中需要 VPN 終止終結點。 對於支援 IKEv2 的雲供應商，可以在兩個雲供應商的 VPN 閘道之間建立直接隧道。

網站到網站 VPN 不被視為高輸送量和低延遲解決方案。 但是，它可以用作物理連接的備份。

## <a name="next-steps"></a>後續步驟
有關快速路由和虛擬網路連接的任何問題，請參閱[ExpressRoute 常見問題解答][ER-FAQ]。

有關 Azure 和 Oracle 之間的連接[，請參閱設置 Azure 和 Oracle 雲之間的直接連接][ER-OCI]

<!--Link References-->
[ER-FAQ]: https://docs.microsoft.com/azure/expressroute/expressroute-faqs
[ER-OCI]: https://docs.microsoft.com/azure/virtual-machines/workloads/oracle/configure-azure-oci-networking



