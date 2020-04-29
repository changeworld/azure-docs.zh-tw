---
title: Azure Web 應用程式防火牆-常見問題
description: 本文提供 Azure Front 的 Web 應用程式防火牆常見問題的解答
services: web-application-firewall
author: vhorne
ms.service: web-application-firewall
ms.devlang: na
ms.topic: article
ms.date: 09/25/2019
ms.author: victorh
ms.openlocfilehash: fefbb038cec0c061f1fd191a77164880372555a0
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "81314327"
---
# <a name="frequently-asked-questions-for-azure-web-application-firewall-on-azure-front-door-service"></a>Azure 前端服務上的 Azure Web 應用程式防火牆常見問題

本文會回答有關 Azure web 應用程式防火牆（WAF）特性和功能的常見問題。 

## <a name="what-is-azure-waf"></a>什麼是 Azure WAF？

Azure WAF 是一種 web 應用程式防火牆，可協助保護您的 web 應用程式免于遭受常見的威脅，例如 SQL 插入式攻擊、跨網站腳本和其他 web 惡意探索。 您可以定義由自訂和受控規則組合所組成的 WAF 原則，以控制對 web 應用程式的存取。

Azure WAF 原則可以套用至裝載于應用程式閘道或 Azure Front 門板的 web 應用程式。

## <a name="what-is-waf-on-azure-front-door"></a>Azure Front 的 WAF 是什麼？ 

Azure Front 大門是可高度擴充、全域散發的應用程式和內容傳遞網路。 Azure WAF 與前門整合時，會在 Azure 網路邊緣停止拒絕服務和目標應用程式的攻擊，並在進入您的虛擬網路之前，接近攻擊來源，提供保護而不犧牲效能。

## <a name="does-azure-waf-support-https"></a>Azure WAF 是否支援 HTTPS？

Front 門提供 TLS 卸載。 WAF 原本就與前門整合，而且可以在要求解密後加以檢查。

## <a name="does-azure-waf-support-ipv6"></a>Azure WAF 是否支援 IPv6？

是。 您可以設定 IPv4 和 IPv6 的 IP 限制。

## <a name="how-up-to-date-are-the-managed-rule-sets"></a>受控規則集的最新狀態為何？

我們會盡力掌握不斷變化的威脅環境。 更新新的規則之後，它就會新增至具有新版本號碼的預設規則集。

## <a name="what-is-the-propagation-time-if-i-make-a-change-to-my-waf-policy"></a>如果我對 WAF 原則進行變更，傳播時間是什麼？

全域部署 WAF 原則通常需要大約5分鐘的時間，而且通常會更快完成。

## <a name="can-waf-policies-be-different-for-different-regions"></a>不同區域的 WAF 原則可以有所不同嗎？

與 Front 門板整合時，WAF 是全域資源。 相同的設定會套用到所有的 Front 門位置。
 
## <a name="how-do-i-limit-access-to-my-back-end-to-be-from-front-door-only"></a>如何? 將我的後端存取許可權制為僅來自 Front 門？

您可以在後端中設定 IP 存取控制清單，以允許只有 Front 門板的輸出 IP 位址範圍，並拒絕任何來自網際網路的直接存取。 支援服務標籤，以供您在虛擬網路上使用。 此外，您可以確認 X 轉送的主機 HTTP 標頭欄位對您的 web 應用程式有效。

## <a name="which-azure-waf-options-should-i-choose"></a>我應該選擇哪些 Azure WAF 選項？

在 Azure 中套用 WAF 原則時，有兩個選項。 使用 Azure Front WAF 的是全域散發的邊緣安全性解決方案。 具有應用程式閘道的 WAF 是區域專屬的解決方案。 我們建議您根據整體效能和安全性需求來選擇解決方案。 如需詳細資訊，請參閱[使用 Azure 的應用程式傳遞套件進行負載平衡](https://docs.microsoft.com/azure/frontdoor/front-door-lb-with-azure-app-delivery-suite)。


## <a name="do-you-support-same-waf-features-in-all-integrated-platforms"></a>所有整合式平臺都支援相同的 WAF 功能嗎？

目前，只有應用程式閘道上的 WAF 支援 ModSec CRS 2.2.9、CRS 3.0 和 CRS 3.1 規則。 只有在 Azure Front WAF 上才支援速率限制、地區篩選和 Azure 受控預設規則集規則。

## <a name="is-ddos-protection-integrated-with-front-door"></a>DDoS 保護是否與 Front 整合？ 

Azure Front 大門會在 Azure 網路邊緣進行全域散發，以吸收和地理位置隔離大型磁片區攻擊。 您可以建立自訂 WAF 原則，以自動封鎖具有已知簽章的 HTTP （s）攻擊並對其進行速率限制。 此外，您可以在部署後端的 VNet 上啟用 DDoS 保護標準。 Azure DDoS 保護標準客戶享有額外的好處，包括成本保護、SLA 保證，以及從 DDoS 快速回應小組存取專家，以在攻擊期間立即提供協助。

## <a name="why-do-additional-requests-above-the-threshold-configured-for-my-rate-limit-rule-get-passed-to-my-backend-server"></a>為什麼對我的速率限制規則設定超過閾值的額外要求會傳遞至我的後端伺服器？

速率限制規則可以限制來自任何用戶端 IP 位址的異常高流量。 您可以在一分鐘或五分鐘的期間內，設定允許來自用戶端 IP 位址的 web 要求數目閾值。 針對細微速率控制，速率限制可以與其他比對條件（例如 HTTP （S）參數比對）結合。 

來自相同用戶端的要求通常會抵達相同的前門伺服器。 在此情況下，您會看到高於閾值的其他要求會立即遭到封鎖。 

不過，來自相同用戶端的要求可能會到達另一部尚未重新整理 [速率限制] 計數器的前端伺服器。 例如，用戶端可能會針對每個要求開啟新的連線，而臨界值為低。 在此情況下，第一次向新的 Front 門板伺服器提出的要求會通過速率限制檢查。 速率限制閾值通常會設定為高，以抵禦來自任何用戶端 IP 位址的阻絕服務攻擊。 對於非常低的臨界值，您可能會看到超過閾值的其他要求通過。

## <a name="next-steps"></a>後續步驟

- 瞭解[Azure Web 應用程式防火牆](../overview.md)。
- 深入瞭解[Azure Front 大門](../../frontdoor/front-door-overview.md)。
