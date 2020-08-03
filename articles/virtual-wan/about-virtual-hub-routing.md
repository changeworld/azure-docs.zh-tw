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
ms.openlocfilehash: 3d680fb105b6bde26e5b02544359009f316647bb
ms.sourcegitcommit: 29400316f0c221a43aff3962d591629f0757e780
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/02/2020
ms.locfileid: "87513718"
---
# <a name="about-virtual-hub-routing"></a>關於虛擬中樞路由

「虛擬中樞」中的路由功能是由路由器所提供，用來管理使用邊界閘道協定（BGP）的閘道之間的所有路由。 虛擬中樞可以包含多個閘道，例如站對站 VPN 閘道、ExpressRoute 閘道、點對站閘道、Azure 防火牆。 此路由器也會在連線到虛擬中樞的虛擬網路之間提供傳輸連線能力，並可支援高達 50 Gbps 的匯總輸送量。 這些路由功能適用于標準虛擬 WAN 客戶。 

若要設定路由，請參閱[如何設定虛擬中樞路由](how-to-virtual-hub-routing.md)。

## <a name="routing-concepts"></a><a name="concepts"></a>路由概念

下列各節說明虛擬中樞路由的重要概念。

> [!NOTE]
> 某些在虛擬網路連線中，中樞路由表、關聯、傳播和靜態路由的新概念可能仍在推出，預計會在8月17日的第一周完成。
>

### <a name="hub-route-table"></a><a name="hub-route"></a>中樞路由表

虛擬中樞路由表可以包含一或多個路由。 路由包含其名稱、標籤、目的地類型、目的地首碼清單，以及要路由傳送之封包的下一個躍點資訊。 **連接**通常會有關聯或傳播至路由表的路由設定

### <a name="connection"></a><a name="connection"></a>建立

連接是具有路由設定的 Resource Manager 資源。 這四種連線類型為：

* **Vpn**連線：將 vpn 網站連線到虛擬中樞 vpn 閘道。
* **Expressroute**連線：將 expressroute 線路連接至虛擬中樞 expressroute 閘道。
* **P2S**設定連線：將使用者 Vpn （點對站）設定連接到虛擬中樞使用者 Vpn （點對站）閘道。
* **中樞虛擬網路**連線：將虛擬網路連接至虛擬中樞。

您可以在安裝期間設定虛擬網路連接的路由設定。 根據預設，所有連接都會關聯並傳播至預設路由表。

### <a name="association"></a><a name="association"></a>技術協會

每個連接都會與一個路由表相關聯。 建立連接與路由表的關聯，可讓流量傳送至路由表中指定為路由的目的地。 連接的路由設定會顯示相關聯的路由表。  可以將多個連接關聯至相同的路由表。 所有 VPN、ExpressRoute 和使用者 VPN 連線都會與相同的（預設）路由表建立關聯。

根據預設，所有連線都會與虛擬中樞內的**預設路由表**建立關聯。 每個虛擬中樞都有自己的預設路由表，可以進行編輯以新增靜態路由。 針對相同的前置詞，以靜態方式新增的路由會優先于動態學習的路由。

:::image type="content" source="./media/about-virtual-hub-routing/concepts-association.png" alt-text="技術協會":::

### <a name="propagation"></a><a name="propagation"></a>傳播

連接會以動態方式將路由傳播至路由表。 使用 VPN 連線、ExpressRoute 連線或 P2S 設定連線時，會使用 BGP 將路由從虛擬中樞傳播到內部部署路由器。 路由可以傳播到一個或多個路由表。

「**無」路由表**也適用于每個虛擬中樞。 傳播至 None 路由表表示不需要從連接傳播任何路由。 VPN、ExpressRoute 和使用者 VPN 連線會將路由傳播到相同的一組路由表。

:::image type="content" source="./media/about-virtual-hub-routing/concepts-propagation.png" alt-text="傳播":::

### <a name="labels"></a><a name="static"></a>標籤
標籤提供以邏輯方式將路由表分組的機制。 這在將路由從連接傳播到多個路由表時特別有用。 例如，預設路由表有一個稱為「預設」的內建標籤。 當使用者將連線路由傳播至 [預設] 標籤時，它會自動套用至虛擬 WAN 中的每個中樞上的所有預設路由表。 

### <a name="configuring-static-routes-in-a-virtual-network-connection"></a><a name="static"></a>設定虛擬網路連接中的靜態路由

設定靜態路由會提供一種機制，可透過下一個躍點 IP 來引導流量，這可能是在連接至虛擬中樞的輪輻 VNet 中布建的網路虛擬裝置（NVA）。 靜態路由是由路由名稱、目的地首碼清單，以及下一個躍點 IP 所組成。

## <a name="reset-hub"></a><a name="route"></a>重設中樞
僅適用于 Azure 入口網站，此選項可讓使用者將任何失敗的資源（例如路由表、中樞路由器或虛擬中樞資源本身）帶回其正當布建狀態。 這是其他選項，可讓使用者在聯繫 Microsoft 尋求支援之前先考慮。 這項作業不會重設虛擬中樞內的任何閘道。 

## <a name="route-tables-in-basic-and-standard-virtual-wans-prior-to-the-feature-set-of-association-and-propagation"></a><a name="route"></a>在關聯和傳播的功能集之前，于基本和標準虛擬 Wan 中路由表

路由表現在具有關聯和傳播的功能。 既有的路由表是不具有這些功能的路由表。 如果您在中樞路由中有既有的路由，但想要使用新功能，請考慮下列方式：

* **在虛擬中樞內具有預先存在之路由的標準虛擬 WAN 客戶**：

若要使用新的路由表功能，請等到8月17日起，Azure 中的推出才會完成。 如果您在 Azure 入口網站的中樞的 [路由] 區段中有已存在的路由，則必須先將其刪除，然後嘗試建立新的路由表（適用于中樞的 [路由表] 區段，Azure 入口網站）

* **在虛擬中樞內具有預先存在之路由的基本虛擬 WAN 客戶**：若要使用新的路由表功能，請等候8月17日的第17周，以完成 Azure 的推出。 如果您在 Azure 入口網站中樞的 [路由] 區段中已存在路由，則需要先將這些路由刪除，然後將基本虛擬 WAN **升級**為標準虛擬 WAN。 請參閱[將虛擬 WAN 從基本升級至標準](upgrade-virtual-wan.md)。

## <a name="virtual-wan-routing-considerations"></a><a name="considerations"></a>虛擬 WAN 路由考慮

設定虛擬 WAN 路由時，請考慮下列事項：

* 所有分支連線（點對站、站對站和 ExpressRoute）都必須與預設路由表建立關聯。 如此一來，所有分支都將學習相同的首碼。
* 所有分支連接都必須將其路由傳播到相同的一組路由表。 例如，如果您決定分支應該傳播至預設路由表，則此設定在所有分支中應該都是一致的。 如此一來，與預設路由表相關聯的所有連線都將能夠連接到所有分支。
* 目前不支援透過 Azure 防火牆進行分支到分支。
* 在多個區域中使用 Azure 防火牆時，所有輪輻虛擬網路都必須與相同的路由表相關聯。 例如，Vnet 的子集會通過 Azure 防火牆，而其他 Vnet 會略過相同虛擬中樞中的 Azure 防火牆。

## <a name="next-steps"></a>後續步驟

若要設定路由，請參閱[如何設定虛擬中樞路由](how-to-virtual-hub-routing.md)。

如需虛擬 WAN 的詳細資訊，請參閱[常見問題集](virtual-wan-faq.md)。
