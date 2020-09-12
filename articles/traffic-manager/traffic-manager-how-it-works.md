---
title: Azure 流量管理員的運作方式 | Microsoft Docs
description: 本文將協助您了解流量管理員如何路由傳送流量，以提高 Web 應用程式的效能和可用性
services: traffic-manager
documentationcenter: ''
author: duongau
manager: twooley
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/05/2019
ms.author: duau
ms.openlocfilehash: 471895f1a615770521584a627e6bca850b87d0ac
ms.sourcegitcommit: 4a7a4af09f881f38fcb4875d89881e4b808b369b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/04/2020
ms.locfileid: "89462627"
---
# <a name="how-traffic-manager-works"></a>流量管理員的運作方式

Azure 流量管理員可讓您控制流量分散到應用程式端點的方式。 端點是裝載於 Azure 內部或外部的任何網際網路對向服務。

流量管理員提供兩大優點︰

- 根據幾個 [流量路由方法](traffic-manager-routing-methods.md)
- [連續監視端點健康狀態](traffic-manager-monitoring.md) 以及在端點失敗時自動容錯移轉

當用戶端嘗試連接至服務時，它必須先將服務的 DNS 名稱解析為 IP 位址。 然後用戶端才能連線到該 IP 位址以存取服務。

**最重要的一點是了解流量管理員是在 DNS 層級上運作。**  流量管理員會使用 DNS，根據流量路由方法的規則，將用戶端導向特定的服務端點。 用戶端會**直接**連接至選取的端點。 流量管理員不是 Proxy 或閘道。 流量管理員看不到在用戶端與服務之間傳遞的流量。

## <a name="traffic-manager-example"></a>流量管理員範例

Contoso Corp 開發出新的合作夥伴入口網站。 此入口網站的 URL 是 `https://partners.contoso.com/login.aspx`。 應用程式裝載於 Azure 的三個區域。 為了改善可用性和最佳化全域效能，他們使用流量管理員將用戶端流量分配給最靠近的可用端點。

若要達成這個設定，要完成下列步驟︰

1. 部署三個服務執行個體。 這些部署的 DNS 名稱為 'contoso-us.cloudapp.net'、'contoso-eu.cloudapp.net' 和 'contoso-asia.cloudapp.net'。
1. 建立名為 'contoso.trafficmanager.net' 的流量管理員設定檔，並設定為在這三個端點之間使用「效能」流量路由方法。
1. 使用 DNS CNAME 記錄，將虛名網域名稱 'partners.contoso.com' 設定成指向 'contoso.trafficmanager.net'。

![流量管理員 DNS 組態][1]

> [!NOTE]
> 虛名網域配合 Azure 流量管理員使用時，您必須使用 CNAME 將虛名網域名稱指向流量管理員網域名稱。 DNS 標準不允許您在網域的「頂點」(或根) 上建立 CNAME。 因此，您無法建立 'contoso.com' 的 CNAME (有時稱為「裸」網域)。 您只能為 'contoso.com' 下的網域建立 CNAME，例如 'www.contoso.com'。 為因應這項限制，我們建議您將 DNS 網域裝載於 [Azure DNS](../dns/dns-overview.md)，並使用[別名記錄](../dns/tutorial-alias-tm.md)來指向您的流量管理員設定檔。 或是，您可以使用簡單的 HTTP 重新導向，將 'contoso.com' 的要求指向至替代名稱，例如 'www.contoso.com'。

### <a name="how-clients-connect-using-traffic-manager"></a>用戶端連接如何使用流量管理員

接續上述範例，當用戶端要求頁面 `https://partners.contoso.com/login.aspx` 時，用戶端會執行下列步驟來解析 DNS 名稱，然後建立連接︰

![使用流量管理員建立連接][2]

1. 用戶端會將 DNS 查詢傳送至已設定的遞迴 DNS 服務，以解析名稱 'partners.contoso.com'。 遞迴 DNS 服務 (有時稱為「本機 DNS」服務) 不直接裝載 DNS 網域。 相反地，用戶端不負責連絡網際網路上解析 DNS 名稱所需的各種授權 DNS 服務。
2. 為了解析 DNS 名稱，遞迴 DNS 服務會尋找 'contoso.com' 網域的名稱伺服器。 然後，連絡這些名稱伺服器來要求 'partners.contoso.com' DNS 記錄。 Contoso.com DNS 伺服器會傳回指向 contoso.trafficmanager.net 的 CNAME 記錄。
3. 接下來，遞迴 DNS 服務會尋找 'trafficmanager.net' 網域的名稱伺服器 (由 Azure 流量管理員服務提供)。 然後，將 'contoso.trafficmanager.net' DNS 記錄的要求傳送至這些 DNS 伺服器。
4. 流量管理員名稱伺服器接收要求。 它們會根據下列項目來選擇端點︰

    - 每個端點已設定的狀態 (不會傳回已停用的端點)
    - 每個端點目前的健康狀態，由流量管理員健康狀態檢查所決定。 如需詳細資訊，請參閱 [流量管理員端點監視](traffic-manager-monitoring.md)。
    - 所選的流量路由方法。 如需詳細資訊，請參閱 [流量管理員路由方法](traffic-manager-routing-methods.md)。

5. 選擇的端點會傳回成為另一筆 DNS CNAME 記錄。 在此情況下，讓我們假設會傳回 contoso-eu.cloudapp.net。
6. 接下來，遞迴 DNS 服務會尋找 'cloudapp.net' 網域的名稱伺服器。 它會聯繫這些名稱伺服器，要求 ' contoso-eu.cloudapp.net ' DNS 記錄。 會傳回 DNS ' A ' 記錄，其中包含以 EU 為基礎之服務端點的 IP 位址。
7. 遞迴 DNS 服務會合併結果，並傳回單一 DNS 回應給用戶端。
8. 用戶端收到 DNS 結果，然後連接至指定的 IP 位址。 用戶端會直接連接至應用程式服務端點，而不透過流量管理員。 因為它是 HTTPS 端點，用戶端會執行必要的 SSL/TLS 交握，然後提出 '/login.aspx' 頁面的 HTTP GET 要求。

遞迴 DNS 服務會快取它收到的 DNS 回應。 用戶端裝置上的 DNS 解析程式也會快取結果。 快取功能會使用快取中的資料，而不查詢其他名稱伺服器，因而可以更快回應後續的 DNS 查詢。 快取持續時間取決於每一筆 DNS 記錄的「存留時間」(TTL) 屬性。 較短的值會導致快取更快到期，因此需要更多次往返於流量管理員的名稱伺服器。 較長的值表示從失敗端點引開流量會花費更長的時間。 流量管理員允許您將流量管理員 DNS 回應中使用的 TTL，設定為最低是 0 秒及最高是 2,147,483,647 秒 (符合 [RFC-1035](https://www.ietf.org/rfc/rfc1035.txt) 的最大範圍)，讓您選擇最能平衡應用程式需求的值。

## <a name="faqs"></a>常見問題集

* [「流量管理員」使用什麼 IP 位址？](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#what-ip-address-does-traffic-manager-use)

* [哪種類型的流量可以使用流量管理員路由傳送？](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#what-types-of-traffic-can-be-routed-using-traffic-manager)

* [流量管理員是否支援「粘滯」會話？](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#does-traffic-manager-support-sticky-sessions)

* [我在使用流量管理員時為何看到 HTTP 錯誤？](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#why-am-i-seeing-an-http-error-when-using-traffic-manager)

* [使用「流量管理員」對效能有什麼影響？](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#what-is-the-performance-impact-of-using-traffic-manager)

* [我可以搭配「流量管理員」使用哪些應用程式通訊協定？](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#what-application-protocols-can-i-use-with-traffic-manager)

* [我可以使用流量管理員搭配「裸」功能變數名稱嗎？](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#can-i-use-traffic-manager-with-a-naked-domain-name)

* [處理 DNS 查詢時，流量管理員會考量用戶端子網路位址嗎？](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#does-traffic-manager-consider-the-client-subnet-address-when-handling-dns-queries)

* [什麼是 DNS TTL，以及它如何影響我的使用者？](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#what-is-dns-ttl-and-how-does-it-impact-my-users)

* [我可以將流量管理員回應的 TTL 設為多高或多低？](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#how-high-or-low-can-i-set-the-ttl-for-traffic-manager-responses)

* [如何了解傳給我的設定檔的查詢量？](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#how-can-i-understand-the-volume-of-queries-coming-to-my-profile)

## <a name="next-steps"></a>接下來的步驟

深入了解「流量管理員」的 [端點監視和自動容錯移轉](traffic-manager-monitoring.md)。

深入了解「流量管理員」的 [流量路由方法](traffic-manager-routing-methods.md)。

<!--Image references-->
[1]: ./media/traffic-manager-how-traffic-manager-works/dns-configuration.png
[2]: ./media/traffic-manager-how-traffic-manager-works/flow.png

