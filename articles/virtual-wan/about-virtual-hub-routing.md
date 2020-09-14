---
title: 關於虛擬中樞路由
titleSuffix: Azure Virtual WAN
description: 本文說明虛擬中樞路由
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 06/29/2020
ms.author: cherylmc
ms.custom: fasttrack-edit
ms.openlocfilehash: f5116b3dfd53c5272ef7bb11d1719b7ee62416c4
ms.sourcegitcommit: 814778c54b59169c5899199aeaa59158ab67cf44
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/13/2020
ms.locfileid: "90055787"
---
# <a name="about-virtual-hub-routing"></a>關於虛擬中樞路由

虛擬中樞內的路由功能是由路由器所提供，該路由器會使用邊界閘道協定 (BGP) 來管理閘道之間的所有路由。 虛擬中樞可以包含多個閘道，例如站對站 VPN 閘道、ExpressRoute 閘道、點對站閘道、Azure 防火牆。 此路由器也會在連線至虛擬中樞的虛擬網路之間提供傳輸連線能力，且最多可支援 50 Gbps 的匯總輸送量。 這些路由功能適用于標準虛擬 WAN 客戶。 

若要設定路由，請參閱 [如何設定虛擬中樞路由](how-to-virtual-hub-routing.md)。

## <a name="routing-concepts"></a><a name="concepts"></a>路由概念

下列各節說明虛擬中樞路由的重要概念。

### <a name="hub-route-table"></a><a name="hub-route"></a>中樞路由表

虛擬中樞路由表可包含一或多個路由。 路由包含其名稱、標籤、目的地類型、目的地首碼清單，以及要路由傳送之封包的下一個躍點資訊。 **連接**通常會有關聯或傳播至路由表的路由設定

### <a name="connection"></a><a name="connection"></a>連接

連接是具有路由設定 Resource Manager 資源。 四種類型的連接：

* **Vpn 連接**：將 vpn 網站連線到虛擬中樞 VPN 閘道。
* **Expressroute 連接**：將 ExpressRoute 線路連線到虛擬中樞 ExpressRoute 閘道。
* **P2S**設定連線：將使用者 Vpn (點對站) 設定連接到虛擬中樞使用者 Vpn (點對站) 閘道。
* **中樞虛擬網路**連線：將虛擬網路連接到虛擬中樞。

您可以在安裝期間設定虛擬網路連接的路由設定。 依預設，所有連接都會與預設路由表產生關聯和傳播。

### <a name="association"></a><a name="association"></a>協會

每個連接都與一個路由表相關聯。 將連接與路由表建立關聯，可將流量傳送至指定為路由表中之路由的目的地。 連接的路由設定將會顯示相關聯的路由表。  多個連接可以與相同的路由表建立關聯。 所有 VPN、ExpressRoute 和使用者 VPN 連線都與相同的 (預設) 路由表建立關聯。

依預設，所有連接都會與虛擬中樞內的 **預設路由表** 建立關聯。 每個虛擬中樞都有自己的預設路由表，可進行編輯以新增靜態路由 (s) 。 以靜態方式新增的路由會優先于相同首碼的動態學習路由。

:::image type="content" source="./media/about-virtual-hub-routing/concepts-association.png" alt-text="協會":::

### <a name="propagation"></a><a name="propagation"></a>傳播

連接會動態地將路由傳播至路由表。 使用 VPN 連線、ExpressRoute 連線或 P2S 設定連線時，會使用 BGP 將路由從虛擬中樞傳播到內部部署路由器。 路由可以傳播至一或多個路由表。

每個虛擬中樞也都有 **無路由表** 可用。 傳播至無路由表表示不需要從連接傳播任何路由。 VPN、ExpressRoute 和使用者 VPN 連接會將路由傳播至同一組路由表。

:::image type="content" source="./media/about-virtual-hub-routing/concepts-propagation.png" alt-text="傳播":::

### <a name="labels"></a><a name="static"></a>標籤
標籤提供以邏輯方式分組路由表的機制。 這在從連接到多個路由表的路由傳播期間特別有用。 例如，預設的路由表有一個稱為「預設」的內建標籤。 當使用者將連線路由傳播至「預設」標籤時，它會自動套用至虛擬 WAN 中的每個中樞上的所有預設路由表。 

### <a name="configuring-static-routes-in-a-virtual-network-connection"></a><a name="static"></a>設定虛擬網路連接中的靜態路由

設定靜態路由會提供一種機制，讓流量通過下一個躍點 IP，這可能是網路虛擬裝置 (NVA) 布建在連接至虛擬中樞的輪輻 VNet 中。 靜態路由是由路由名稱、目的地首碼清單和下一個躍點 IP 所組成。

## <a name="reset-hub"></a><a name="route"></a>重設中樞
此選項僅適用于 Azure 入口網站，此選項可讓使用者將任何失敗的資源（例如路由表、中樞路由器或虛擬中樞資源本身）移回其正當布建狀態。 這是使用者在聯繫 Microsoft 尋求支援之前，要考慮的另一個選項。 這項作業不會重設虛擬中樞內的任何閘道。 

## <a name="route-tables-in-basic-and-standard-virtual-wans-prior-to-the-feature-set-of-association-and-propagation"></a><a name="route"></a>在關聯和傳播功能集合之前的基本和標準虛擬 Wan 中路由表

路由表現在具有關聯和傳播的功能。 既有的路由表是不具有這些功能的路由表。 如果您在中樞路由中有既有的路由，但想要使用新功能，請考慮下列方式：

* **在虛擬中樞內具有預先存在路由的標準虛擬 WAN 客戶**：

如果您在 Azure 入口網站中的中樞有 [路由] 區段中現有的路由，則必須先將其刪除，然後嘗試建立新的路由表， (適用于中樞的 [路由表] 區段中 Azure 入口網站) 

* **在虛擬中樞內具有預先存在之路由的基本虛擬 WAN 客戶**：如果您在 Azure 入口網站中的中樞在 [路由] 區段中有預先存在的路由，則必須先將其刪除，然後將基本虛擬 wan **升級** 至標準虛擬 wan。 請參閱[將虛擬 WAN 從基本升級至標準](upgrade-virtual-wan.md)。

## <a name="virtual-wan-routing-considerations"></a><a name="considerations"></a>虛擬 WAN 路由考慮

設定虛擬 WAN 路由時，請考慮下列事項：

* 所有分支連線 (點對站、站對站和 ExpressRoute) 都必須與預設路由表建立關聯。 如此一來，所有分支都會學習相同的首碼。
* 所有分支連線都必須將其路由傳播至同一組路由表。 例如，如果您決定分支應傳播至預設路由表，則此設定在所有分支中都應該是一致的。 如此一來，與預設路由表相關聯的所有連接都將能夠連接到所有的分支。
* 目前不支援透過 Azure 防火牆進行分支對分支。
* 在多個區域中使用 Azure 防火牆時，所有輪輻虛擬網路都必須與相同的路由表建立關聯。 例如，讓 Vnet 的子集進入 Azure 防火牆，而其他 Vnet 略過相同虛擬中樞內的 Azure 防火牆則是不可行的。
* 您可以針對每個 VNet 連線設定單一下一個躍點 IP。
* 虛擬中樞不支援 0.0.0.0/0 和下一個躍點虛擬網路連線 (的靜態路由，或 VNet 連線中的設備 IP) 
* 與 0.0.0.0/0 路由相關的所有資訊都局限于本機中樞的路由表。 此路由不會跨中樞傳播。

## <a name="next-steps"></a>接下來的步驟

若要設定路由，請參閱 [如何設定虛擬中樞路由](how-to-virtual-hub-routing.md)。

如需虛擬 WAN 的詳細資訊，請參閱[常見問題集](virtual-wan-faq.md)。
