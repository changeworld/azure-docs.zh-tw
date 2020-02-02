---
title: Azure DNS 委派概觀
description: 了解如何變更網域委派及使用 Azure DNS 名稱伺服器提供網域主機代管。
services: dns
author: rohinkoul
ms.service: dns
ms.date: 2/19/2019
ms.author: rohink
ms.topic: conceptual
ms.openlocfilehash: 9304556edb5e6207296d8ee4e8392e345869cb92
ms.sourcegitcommit: fa6fe765e08aa2e015f2f8dbc2445664d63cc591
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/01/2020
ms.locfileid: "76939042"
---
# <a name="delegation-of-dns-zones-with-azure-dns"></a>使用 Azure DNS 的 DNS 區域委派

Azure DNS 可讓您裝載 DNS 區域，並在 Azure 中管理網域的 DNS 記錄。 網域必須從父系網域委派給 Azure DNS，該網域的 DNS 查詢才能送達 Azure DNS。 請記住，Azure DNS 不是網域註冊機構。 本文說明網域委派的運作方式，以及如何將網域委派給 Azure DNS。

## <a name="how-dns-delegation-works"></a>DNS 委派的運作方式

### <a name="domains-and-zones"></a>網域和區域

網域名稱系統是網域階層。 階層從「根」網域開始，其名稱只是 ' **.** '。  下面接著最上層網域，例如 'com'、'net'、'org'、'uk' 或 'jp'。  最上層網域下面是第二層網域，例如 'org.uk' 或 'co.jp'。  依此類推。 DNS 階層中的網域裝載於個別的 DNS 區域。 這些區域遍布全球，由世界各地的 DNS 名稱伺服器所裝載。

**DNS 區域** - 網域是網域名稱系統中的唯一名稱，例如 'contoso.com'。 DNS 區域會用於裝載特定網域的 DNS 記錄。 例如，網域 'contoso.com' 可能包含數筆 DNS 記錄，例如 'mail.contoso.com' (用於郵件伺服器) 和 'www.contoso.com' (用於網站)。

**網域註冊機構** - 網域註冊機構是指可以提供網際網路網域名稱的公司。 他們會驗證您想要使用的網際網路網域是否可用，並允許您購買。 一旦註冊網域名稱，您就成為該網域名稱的合法擁有者。 如果您已經有網際網路網域，您將使用目前的網域註冊機構委派給 Azure DNS。

如需合格網域註冊機構的詳細資訊，請參閱 [ICANN 合格註冊機構](https://www.icann.org/registrar-reports/accredited-list.html) \(英文\)。

### <a name="resolution-and-delegation"></a>解析和委派

有兩種類型的 DNS 伺服器：

* *授權* DNS 伺服器裝載 DNS 區域。 它只會回答這些區域中的 DNS 記錄查詢。
* *遞迴* DNS 伺服器不裝載 DNS 區域。 它會呼叫授權 DNS 伺服器來收集所需的資料，以回答所有 DNS 查詢。

Azure DNS 提供具權威性的 DNS 服務。  它不提供遞迴 DNS 服務。 Azure 中的雲端服務和 VM 會自動設定為使用在 Azure 的基礎結構中個別提供的遞迴 DNS 服務。 如需如何變更這些 DNS 設定的詳細資訊，請參閱 [Azure 中的名稱解析](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server)。

電腦或行動裝置中的 DNS 用戶端，通常會呼叫遞迴 DNS 伺服器，以執行用戶端應用程式需要的任何 DNS 查詢。

當遞迴 DNS 伺服器收到 DNS 記錄的查詢時，例如 'www.contoso.com'，就必須先找到裝載 'contoso.com' 網域的區域的名稱伺服器。 若要尋找名稱伺服器，它會從根名稱伺服器開始，尋找裝載 'com' 區域的名稱伺服器。 然後，查詢 'com' 名稱伺服器，尋找裝載 'contoso.com' 區域的名稱伺服器。  最後，就能夠向這些名稱伺服器查詢 'www.contoso.com'。

此程序稱為 DNS 名稱解析。 嚴格來說，DNS 解析還有其他步驟，例如追蹤 CNAME，但這對於了解 DNS 委派的運作方式並不重要。

上層區域如何「指向」子區域的名稱伺服器？ 作法是使用一種特殊的 DNS 記錄，稱為 NS 記錄 (NS 代表「名稱伺服器」)。 例如，根區域包含 'com' 的 NS 記錄，並且會顯示 'com' 區域的名稱伺服器。 接著，'com' 區域包含 'contoso.com' 的 NS 記錄，其中顯示 'contoso.com' 區域的名稱伺服器。 在上層區域中設定子區域的 NS 記錄，稱為委派網域。

下圖顯示 DNS 查詢範例。 contoso.net 和 partners.contoso.net 都是 Azure DNS 區域。

![Dns-nameserver](./media/dns-domain-delegation/image1.png)

1. 用戶端會向其本機 DNS 伺服器要求 `www.partners.contoso.net`。
2. 本機 DNS 伺服器沒有記錄，因此會對其根名稱伺服器提出要求。
3. 根名稱伺服器沒有記錄，但知道 `.net` 名稱伺服器的位址，所以會將該位址提供給 DNS 伺服器
4. 本機 DNS 伺服器將要求傳送至 `.net` 名稱伺服器。
5. `.net` 名稱伺服器沒有記錄，但是知道 `contoso.net` 名稱伺服器的位址。 在此情況下，它會回應在 Azure DNS 中裝載的 DNS 區域所包含的名稱伺服器位址。
6. 本機 DNS 伺服器將要求傳送至 Azure DNS 中裝載的 `contoso.net` 區域所包含的名稱伺服器。
7. `contoso.net` 區域沒有記錄，但知道 `partners.contoso.net` 的名稱伺服器，並以其位址回應。 在此案例中，這是在 Azure DNS 中裝載的 DNS 區域。
8. 本機 DNS 伺服器將要求傳送至 `partners.contoso.net` 區域的名稱伺服器。
9. `partners.contoso.net` 區域具有 A 記錄，並以此 IP 位址回應。
10. 本機 DNS 伺服器將此 IP 位址提供給用戶端
11. 用戶端會連線至網站`www.partners.contoso.net`。

每個委派實際上有兩份 NS 記錄：一份在上層區域中指向子區域，另一份在子區域本身。 'contoso.net' 區域包含 'contoso.net' 的 NS 記錄 (除了 'net' 中的 NS 記錄之外)。 這些記錄稱為授權 NS 記錄，位於子區域的頂點。

## <a name="next-steps"></a>後續步驟

了解如何[將您的網域委派給 Azure DNS](dns-delegate-domain-azure-dns.md)

