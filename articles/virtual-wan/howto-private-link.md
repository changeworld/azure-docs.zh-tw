---
title: 跨虛擬 WAN 共用私人連結服務
titleSuffix: Azure Virtual WAN
description: 在虛擬 WAN 中設定 Private Link 的步驟
services: virtual-wan
author: erjosito
ms.service: virtual-wan
ms.topic: how-to
ms.date: 09/22/2020
ms.author: jomore
ms.custom: fasttrack-new
ms.openlocfilehash: cc8e7314c941035207ecf809a9d85ef46bd58379
ms.sourcegitcommit: d76108b476259fe3f5f20a91ed2c237c1577df14
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/29/2020
ms.locfileid: "92913750"
---
# <a name="use-private-link-in-virtual-wan"></a>在虛擬 WAN 中使用 Private Link

[Azure Private Link](../private-link/private-link-overview.md) 是一種技術，可讓您藉由公開 [私人端點](../private-link/private-endpoint-overview.md)，使用私人 IP 位址連線來連接 Azure 平臺即服務供應專案。 您可以使用 Azure 虛擬 WAN，在連線到任何虛擬中樞的其中一個虛擬網路中部署私人端點。 這可提供連線至相同虛擬 WAN 的任何其他虛擬網路或分支的連線能力。

## <a name="before-you-begin"></a>開始之前

本文中的步驟假設您已部署具有一或多個中樞的虛擬 WAN，以及至少兩個連接到虛擬 WAN 的虛擬網路。

若要建立新的虛擬 WAN 與新的中樞，請使用下列文章中的步驟：

* [建立虛擬 WAN](virtual-wan-site-to-site-portal.md#openvwan)
* [建立中樞](virtual-wan-site-to-site-portal.md#hub)
* [將 VNet 連線至中樞](virtual-wan-site-to-site-portal.md#hub)

## <a name="create-a-private-link-endpoint"></a><a name="endpoint"></a>建立私人連結端點

您可以為許多不同的服務建立私用連結端點。 在此範例中，我們會使用 Azure SQL Database。 您可以在快速入門中找到如何建立 Azure SQL Database 私人端點的詳細資訊 [：使用 Azure 入口網站建立私人端點](../private-link/create-private-endpoint-portal.md)。 下圖顯示 Azure SQL Database 的網路設定：

:::image type="content" source="./media/howto-private-link/create-private-link.png" alt-text="建立私人連結" lightbox="./media/howto-private-link/create-private-link.png":::

建立 Azure SQL Database 之後，您可以驗證私人端點 IP 位址流覽您的私人端點：

:::image type="content" source="./media/howto-private-link/endpoints.png" alt-text="建立私人連結" lightbox="./media/howto-private-link/endpoints.png":::

按一下我們建立的私人端點，您應該會看到其私人 IP 位址，以及其完整功能變數名稱 (FQDN) 。 請注意，私人端點的 IP 位址在已部署 (10.1.3.0/24) 的 VNet 範圍內：

:::image type="content" source="./media/howto-private-link/sql-endpoint.png" alt-text="建立私人連結" lightbox="./media/howto-private-link/sql-endpoint.png":::

## <a name="verify-connectivity-from-the-same-vnet"></a><a name="connectivity"></a>確認相同 VNet 的連線能力

在此範例中，我們將確認已安裝 MS SQL 工具的 Ubuntu 虛擬機器對 Azure SQL Database 的連線能力。 第一個步驟是驗證 DNS 解析是否正常運作，且 Azure SQL Database 完整功能變數名稱解析為私人 IP 位址，位於私人端點已部署 (10.1.3.0/24) 的相同 VNet 中：

```bash
$ nslookup wantest.database.windows.net
Server:         127.0.0.53
Address:        127.0.0.53#53

Non-authoritative answer:
wantest.database.windows.net    canonical name = wantest.privatelink.database.windows.net.
Name:   wantest.privatelink.database.windows.net
Address: 10.1.3.228
```

如您在先前的輸出中所見，FQDN `wantest.database.windows.net` 是對應至 `wantest.privatelink.database.windows.net` ，在私人端點上建立的私人 DNS 區域會解析為私人 IP 位址 `10.1.3.228` 。 查看私人 DNS 區域將會確認私人端點的 A 記錄已對應到私人 IP 位址：

:::image type="content" source="./media/howto-private-link/dns-zone.png" alt-text="建立私人連結" lightbox="./media/howto-private-link/dns-zone.png":::

確認正確的 DNS 解析之後，我們可以嘗試連接到資料庫：

```bash
$ query="SELECT CONVERT(char(15), CONNECTIONPROPERTY('client_net_address'));"
$ sqlcmd -S wantest.database.windows.net -U $username -P $password -Q "$query"

10.1.3.75
```

如您所見，我們使用的是一個特殊的 SQL 查詢，提供 SQL server 從用戶端看到的來源 IP 位址。 在此情況下，伺服器會看到用戶端的私人 IP (`10.1.3.75`) ，這表示流量會直接從 VNet 進入私人端點。

請注意，您必須設定變數 `username` ，並 `password` 符合 Azure SQL Database 中定義的認證，才能讓本指南中的範例運作。

## <a name="connect-from-a-different-vnet"></a><a name="vnet"></a>從不同的 VNet 連接

現在，Azure 虛擬 WAN 中的一個 VNet 具有私人端點的連線能力，其他連線到虛擬 WAN 的 Vnet 和分支也可以存取它。 您必須透過 Azure 虛擬 WAN 所支援的任何模型（例如 [任何對任何案例](scenario-any-to-any.md) 或 [共用服務 VNet 案例](scenario-shared-services-vnet.md)）來提供連線，以命名兩個範例。

當您將 VNet 或分支之間的連線至已部署私人端點的 VNet 時，您必須設定 DNS 解析：

* 如果從 VNet 連線至私人端點，您可以使用與 Azure SQL Database 建立的相同私用區域。
* 如果從分支 (站對站 VPN、點對站 VPN 或 ExpressRoute) 連接到私人端點，您必須使用內部部署 DNS 解析。

在此範例中，我們將從不同的 VNet 連線，因此，我們會先將私人 DNS 區域連結至新的 VNet，讓其工作負載能夠將 Azure SQL Database 的完整功能變數名稱解析為私人 IP 位址。 這是透過將私人 DNS 區域連結至新的 VNet 來完成的：

:::image type="content" source="./media/howto-private-link/dns-link.png" alt-text="建立私人連結" lightbox="./media/howto-private-link/dns-link.png":::

現在，連結的 VNet 中的任何虛擬機器都應該正確地將 Azure SQL Database FQDN 解析為私人連結的私人 IP 位址：

```bash
$ nslookup wantest.database.windows.net
Server:         127.0.0.53
Address:        127.0.0.53#53

Non-authoritative answer:
wantest.database.windows.net    canonical name = wantest.privatelink.database.windows.net.
Name:   wantest.privatelink.database.windows.net
Address: 10.1.3.228
```

若要再次檢查此 VNet (10.1.1.0/24) 有連線到已設定私人端點 (10.1.3.0/24) 的原始 VNet，您可以在 VNet 中的任何虛擬機器中驗證有效的路由表：

:::image type="content" source="./media/howto-private-link/effective-routes.png" alt-text="建立私人連結" lightbox="./media/howto-private-link/effective-routes.png":::

如您所見，有一個路由指向 Azure 虛擬 WAN 中的虛擬網路閘道所插入的 VNet 10.1.3.0/24。 現在我們可以測試資料庫的連線能力：

```bash
$ query="SELECT CONVERT(char(15), CONNECTIONPROPERTY('client_net_address'));"
$ sqlcmd -S wantest.database.windows.net -U $username -P $password -Q "$query"

10.1.1.75
```

在此範例中，我們已瞭解如何在連接至虛擬 WAN 的其中一個 Vnet 中建立私人端點，以提供與虛擬 WAN 中其餘 Vnet 和分支的連線。

## <a name="next-steps"></a>後續步驟

如需虛擬 WAN 的詳細資訊，請參閱[常見問題集](virtual-wan-faq.md)。
