---
title: Azure 防火牆強制通道
description: 您可以設定強制通道，將網際網路繫結流量路由傳送至額外的防火牆或網路虛擬設備，以供進一步處理。
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 08/19/2020
ms.author: victorh
ms.openlocfilehash: da2b206bf24cb33180305e32e270b989eb64dfa3
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "88612585"
---
# <a name="azure-firewall-forced-tunneling"></a>Azure 防火牆強制通道

當您設定新的 Azure 防火牆時，您可以將所有網際網路繫結流量路由傳送至指定的下一個躍點，而不是直接前往網際網路。 例如，您可能有內部部署邊緣防火牆或其他網路虛擬設備 (NVA) 能先處理網路流量，再將其傳遞至網際網路。 不過，您無法為強制通道設定現有的防火牆。

根據預設，Azure 防火牆不允許強制通道，以確保符合其所有的輸出 Azure 相依性。 AzureFirewallSubnet 上的使用者定義路由 (UDR) 設定，其預設路由不會直接連線到網際網路，已停用。

## <a name="forced-tunneling-configuration"></a>強制通道設定

為了支援強制通道，服務管理流量會與客戶流量分開。 另一個名為 AzureFirewallManagementSubnet (最小子網路大小 /26) 的專用子網路必須搭配其本身相關聯的公用 IP 位址。 這個子網路上唯一允許的路由是網際網路的預設路由，且必須停用 BGP 路由傳播。

如果您有透過 BGP 公告的預設路由，以強制傳送至內部部署的流量，您必須先建立 *AzureFirewallSubnet* 和 *AzureFirewallManagementSubnet* ，然後再部署您的防火牆，並讓 UDR 具有網際網路的預設路由，並已停用 **傳播閘道路由** 。

在此設定中， *AzureFirewallSubnet* 現在可以包含任何內部部署防火牆或 NVA 的路由，以便在流量傳遞至網際網路之前處理流量。 如果在此子網上啟用**傳播閘道路由**，您也可以透過 BGP 將這些路由發佈至*AzureFirewallSubnet* 。

例如，您可以在 *AzureFirewallSubnet* 上建立預設路由，並以您的 VPN 閘道作為下一個躍點，以進入您的內部部署裝置。 或者，您可以啟用「 **傳播閘道」路由** ，以取得適用于內部部署網路的適當路由。

:::image type="content" source="media/forced-tunneling/route-propagation.png" alt-text="虛擬網路閘道路由傳播":::

如果您啟用強制通道，則會將網際網路系結流量 Snat 轉譯至 AzureFirewallSubnet 中的其中一個防火牆私人 IP 位址，並將來源從內部部署防火牆中隱藏。

如果您的組織使用私人網路的公用 IP 位址範圍，Azure 防火牆會將流量 SNAT 轉譯到 AzureFirewallSubnet 其中一個防火牆私人 IP 位址。 不過，您可以將 Azure 防火牆設定為 **不** 會 SNAT 公用 IP 位址範圍。 如需詳細資訊，請參閱 [Azure 防火牆 SNAT 私人 IP 位址範圍](snat-private-range.md)。

一旦您設定 Azure 防火牆以支援強制通道，就無法復原設定。 如果您移除防火牆上的所有其他 IP 設定，系統也會移除管理 IP 設定，並解除配置防火牆。 指派給管理 IP 設定的公用 IP 位址無法移除，但是您可以指派不同的公用 IP 位址。

## <a name="next-steps"></a>後續步驟

- [教學課程：使用 Azure 入口網站在混合式網路中部署及設定 Azure 防火牆](tutorial-hybrid-portal.md)
