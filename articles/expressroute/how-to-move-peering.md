---
title: Azure 快速路由：將公共對等互連移動到 Microsoft 對等互連
description: 本文會示範在 ExpressRoute 中將公用對等互連移至 Microsoft 對等互連的步驟。
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: article
ms.date: 12/12/2019
ms.author: cherylmc
ms.openlocfilehash: 48ecfcc0d6241e7926892a3ca1c9925b0dc07241
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "75436847"
---
# <a name="move-a-public-peering-to-microsoft-peering"></a>將公用互連移至 Microsoft 對等互連

本文可協助您將公用對等設定移至 Microsoft 對等互連，不需要停機。 ExpressRoute 支援使用 Microsoft 對等互連與路由篩選進行 Azure PaaS 服務，例如 Azure 儲存體和 Azure SQL Database。 現在存取 Microsoft PaaS 和 SaaS 服務只需要一個路由網域。 您可以使用路由篩選，選擇性地公告您想要使用的 Azure 區域 PaaS 服務首碼。

Azure 公共對等互連具有與每個 BGP 會話關聯的 1 個 NAT IP 位址。 Microsoft 對等互連允許您配置自己的 NAT 分配，以及使用路由篩選器進行選擇性首碼播發。 公共對等互連是一種單向服務，使用該服務時，連接始終從 WAN 啟動到 Microsoft Azure 服務。 Microsoft Azure 服務將無法透過這個路由網域啟動連線到您的網路。

一旦啟用公用對等互連，您便能夠連線到所有 Azure 服務。 我們不允許您選擇性地選取已通告路由的服務。 雖然 Microsoft 對等互連是雙向連接，可以從 Microsoft Azure 服務以及 WAN 啟動連接。 有關路由域和對等互連的詳細資訊，請參閱[ExpressRoute 電路和路由域](expressroute-circuit-peerings.md)。

## <a name="before-you-begin"></a><a name="before"></a>開始之前

若要連接到 Microsoft 對等互連，您必須設定與管理 NAT。 您的連線服務提供者可能會將 NAT 當成受控服務加以設定及管理。 如果您打算存取 Microsoft 對等互連上的 Azure PaaS 和 Azure SaaS 服務，請務必正確調整 NAT IP 集區的大小。 如需 NAT for ExpressRoute 的詳細資訊，請參閱 [Microsoft 對等互連的 NAT 需求](expressroute-nat.md#nat-requirements-for-microsoft-peering)。 當您通過 Azure ExpressRoute（微軟對等互連）連接到 Microsoft 時，您有多個指向 Microsoft 的連結。 一個連結是您現有的網際網路連線，而另一個則是透過 ExpressRoute。 Microsoft 一些流量可能會通過網際網路，但透過 ExpressRoute 返回，反之亦然。

![雙向連接](./media/how-to-move-peering/bidirectional-connectivity.jpg)

> [!Warning]
> 向 Microsoft 公告的 NAT IP 集區不得向網際網路公告。 這樣會中斷其他 Microsoft 服務的連線。

在配置 Microsoft 對等互連之前，請參閱[具有多個網路路徑的非對稱路由](https://docs.microsoft.com/azure/expressroute/expressroute-asymmetric-routing)，以警告非對稱路由。

* 如果您使用公用對等互連，且已經有用於存取 [Azure 儲存體](../storage/common/storage-network-security.md)或 [Azure SQL Database](../sql-database/sql-database-vnet-service-endpoint-rule-overview.md) 的公用 IP 位址 IP 網路規則，必須確定 Microsoft 對等互連搭配設定的 NAT IP 集區有包含在 Azure 儲存體帳戶或 Azure SQL 帳戶的公用 IP 位址清單中。<br>
* 為了在移至 Microsoft 對等互連時不用停機，請依本文中的出現順序操作步驟。

## <a name="1-create-microsoft-peering"></a><a name="create"></a>1. 創建微軟對等互連

如果尚未建立 Microsoft 對等互連，請使用下列任一文章建立 Microsoft 對等互連。 若您的連線提供者提供受控第 3 層服務，您可以要求連線提供者為您的線路啟用 Microsoft 對等互連。

如果層 3 由您管理，則在繼續操作之前需要以下資訊：

* 主要連結的 /30 子網路。 這必須是您所擁有且註冊在 RIR / IRR 中的有效公用 IPv4 首碼。 您會從這個子網路將第一個可用 IP 位址指派給路由器，因為 Microsoft 會將第二個可用 IP 用於其路由器。<br>
* 次要連結的 /30 子網路。 這必須是您所擁有且註冊在 RIR / IRR 中的有效公用 IPv4 首碼。 您會從這個子網路將第一個可用 IP 位址指派給路由器，因為 Microsoft 會將第二個可用 IP 用於其路由器。<br>
* 供建立此對等的有效 VLAN ID。 請確定線路有沒有其他對等使用相同的 VLAN ID。 如果同時需要主要和次要連結，則必須使用相同的 VLAN ID。<br>
* 對等的 AS 編號。 您可以使用 2 位元組和 4 位元組 AS 編號。<br>
* 公告的首碼：您必須提供一份您打算在 BGP 工作階段上公告的所有首碼的清單。 只接受公用 IP 位址首碼。 如果計劃傳送一組首碼，可以傳送以逗號分隔的清單。 這些首碼必須在 RIR / IRR 中註冊給您。<br>
* 路由登錄名稱：您可以指定可供註冊 AS 編號和首碼的 RIR / IRR。

* **可選**- 客戶 ASN：如果您是未註冊到對等 AS 編號的廣告首碼，則可以指定它們註冊的 AS 編號。<br>
* **可選**- 如果選擇使用 MD5 雜湊。

有關啟用 Microsoft 對等互連的詳細說明，請參閱以下文章：

* [建立使用 Azure 入口網站的 Microsoft 對等互連](expressroute-howto-routing-portal-resource-manager.md#msft)<br>
* [建立使用 Azure Powershell 的 Microsoft 對等互連](expressroute-howto-routing-arm.md#msft)<br>
* [建立使用 Azure CLI 的 Microsoft 對等互連](howto-routing-cli.md#msft)

## <a name="2-validate-microsoft-peering-is-enabled"></a><a name="validate"></a>2. 驗證啟用了 Microsoft 對等互連

確認已啟用 Microsoft 對等互連，且已公告公用首碼為已設定狀態。

* [Azure 門戶](expressroute-howto-routing-portal-resource-manager.md#getmsft)<br>
* [Azure 電源外殼](expressroute-howto-routing-arm.md#getmsft)<br>
* [Azure CLI](howto-routing-cli.md#getmsft)

## <a name="3-configure-and-attach-a-route-filter-to-the-circuit"></a><a name="routefilter"></a>3. 配置線路濾波器並將其連接到電路

預設情況下，在路由篩選器附加到電路之前，新的 Microsoft 對等互連不會通告任何首碼。 創建路由篩選器規則時，可以為要用於 Azure PaaS 服務的 Azure 區域指定服務社區清單。 這為您提供了根據您的要求篩選路由的靈活性，如以下螢幕截圖所示：

![合併公用對等](./media/how-to-move-peering/routefilter.jpg)

依照下列任一文章來設定路由篩選：

* [針對使用 Azure 入口網站的 Microsoft 對等互連設定路由篩選](how-to-routefilter-portal.md)<br>
* [針對使用 Azure PowerShell 的 Microsoft 對等互連設定路由篩選](how-to-routefilter-powershell.md)<br>
* [針對使用 Azure CLI 的 Microsoft 對等互連設定路由篩選](how-to-routefilter-cli.md)

## <a name="4-delete-the-public-peering"></a><a name="delete"></a>4. 刪除公共對等互連

確認已設定 Microsoft 對等互連，而且已在 Microsoft 對等互連上正確公告您想要使用的首碼之後，您就可以刪除公用對等互連。 若要刪除公用對等互連，使用下列任一文章：

* [刪除使用 Azure PowerShell 的 Azure 公用對等互連](about-public-peering.md#powershell)
* [刪除使用 CLI 的 Azure 公用對等互連](about-public-peering.md#cli)
  
## <a name="5-view-peerings"></a><a name="view"></a>5. 查看對等互連
  
在 Azure 入口網站中，您可以看到所有 ExpressRoute 線路和對等互連的清單。 如需詳細資訊，請參閱[檢視 Microsoft 對等互連的詳細資料](expressroute-howto-routing-portal-resource-manager.md#getmsft)。

## <a name="next-steps"></a>後續步驟

如需有關 ExpressRoute 的詳細資訊，請參閱 [ExpressRoute 常見問題集](expressroute-faqs.md)。
