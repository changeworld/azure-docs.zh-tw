---
title: Azure Web 應用程式防火牆 ─ 常見問題
description: 本文提供了有關 Azure 前門 Web 應用程式防火牆的常見問題的解答
services: web-application-firewall
author: vhorne
ms.service: web-application-firewall
ms.devlang: na
ms.topic: article
ms.date: 09/25/2019
ms.author: victorh
ms.openlocfilehash: fefbb038cec0c061f1fd191a77164880372555a0
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/14/2020
ms.locfileid: "81314327"
---
# <a name="frequently-asked-questions-for-azure-web-application-firewall-on-azure-front-door-service"></a>Azure 前門服務上 Azure Web 應用程式防火牆的常見問題

本文回答了有關 Azure Web 應用程式防火牆 (WAF) 特性和功能的常見問題。 

## <a name="what-is-azure-waf"></a>什麼是 Azure WAF?

Azure WAF 是一種 Web 應用程式防火牆,可幫助保護 Web 應用程式免受常見威脅(如 SQL 注入、跨網站腳本和其他 Web 漏洞利用)。 您可以定義由自訂規則和託管規則組合組成的 WAF 策略,以控制對 Web 應用程式的訪問。

Azure WAF 政策可以應用於應用程式閘道或 Azure 前門上託管的 Web 應用程式。

## <a name="what-is-waf-on-azure-front-door"></a>Azure 前門上的 WAF 是什麼? 

Azure 前門是一個高度可擴展的全球分散式應用程式和內容交付網路。 Azure WAF 與前門集成後,將停止 Azure 網路邊緣的拒絕服務和目標應用程式攻擊,這些攻擊在攻擊源進入虛擬網路之前靠近攻擊源,從而在不犧牲性能的情況下提供保護。

## <a name="does-azure-waf-support-https"></a>Azure WAF 是否支援 HTTPS?

前門提供 TLS 卸載。 WAF 與前門本機集成,可在解密後檢查請求。

## <a name="does-azure-waf-support-ipv6"></a>Azure WAF 是否支援 IPv6?

是。 您可以為 IPv4 和 IPv6 配置 IP 限制。

## <a name="how-up-to-date-are-the-managed-rule-sets"></a>託管規則集的最新情況如何?

我們盡最大努力跟上不斷變化的威脅形勢。 更新新規則後,該規則將添加到具有新版本號的預設規則集中。

## <a name="what-is-the-propagation-time-if-i-make-a-change-to-my-waf-policy"></a>如果更改 WAF 策略,傳播時間是多少?

在全球部署 WAF 策略通常需要大約 5 分鐘,並且通常完成得更快。

## <a name="can-waf-policies-be-different-for-different-regions"></a>WAF 策略對於不同區域是否可以不同?

與前門集成時,WAF 是一種全域資源。 相同的配置適用於所有前門位置。
 
## <a name="how-do-i-limit-access-to-my-back-end-to-be-from-front-door-only"></a>如何限制僅從前門進入後端?

您可以在後端介面設定 IP 存取控制列表,以僅允許前門出站 IP 位址範圍,並拒絕從 Internet 直接存取。 支援服務標記,供您在虛擬網路上使用。 此外,還可以驗證 X-前行主機 HTTP 標頭欄位是否對 Web 應用程式有效。

## <a name="which-azure-waf-options-should-i-choose"></a>我應該選擇哪些 Azure WAF 選項?

在 Azure 中應用 WAF 策略時有兩個選項。 帶 Azure 前門的 WAF 是一個全域分佈的邊緣安全解決方案。 帶應用程式閘道的 WAF 是一種區域性的專用解決方案。 我們建議您根據整體性能和安全要求選擇解決方案。 有關詳細資訊,請參閱使用[Azure 的應用程式提供套件進行負載平衡](https://docs.microsoft.com/azure/frontdoor/front-door-lb-with-azure-app-delivery-suite)。


## <a name="do-you-support-same-waf-features-in-all-integrated-platforms"></a>您是否在所有整合平台上支援相同的 WAF 功能?

目前,ModSec CRS 2.2.9、CRS 3.0 和 CRS 3.1 規則僅支援應用程式網關上的 WAF。 速率限制、地理篩選和 Azure 託管預設規則集規則僅支援 Azure 前門的 WAF。

## <a name="is-ddos-protection-integrated-with-front-door"></a>DDoS 防護是否與前門集成? 

Azure 前門在 Azure 網路邊緣全域分佈,可以吸收大容量攻擊並將其在地理位置上隔離。 您可以創建自定義 WAF 策略,以自動阻止和速率限制 HTTP(s) 具有已知簽名的攻擊。 此外,您還可以在部署後端的 VNet 上啟用 DDoS 保護標準。 Azure DDoS 保護標準客戶將獲得其他好處,包括成本保護、SLA 保證以及 DDoS 快速反應團隊的專家訪問,以便在攻擊期間立即獲得説明。

## <a name="why-do-additional-requests-above-the-threshold-configured-for-my-rate-limit-rule-get-passed-to-my-backend-server"></a>為什麼超出為速率限制規則配置的閾值的其他請求傳遞到後端伺服器?

速率限制規則可以限制來自任何用戶端 IP 位址的異常高流量。 您可以在一分鐘或五分鐘的持續時間內,針對用戶端 IP 位址允許的 Web 請求數設定閾值。 對於粒度速率控制,速率限制可以與其他匹配條件(如 HTTP(S) 參數匹配相結合。 

來自同一用戶端的請求通常到達同一個前門伺服器。 在這種情況下,您將看到高於閾值的其他請求立即被阻止。 

但是,來自同一用戶端的請求可能會到達尚未刷新速率限制計數器的不同前門伺服器。 例如,用戶端可能為每個請求打開一個新連接,並且閾值較低。 在這種情況下,對新的前門伺服器的第一個請求將通過速率限制檢查。 速率限制閾值通常設置高,以防禦來自任何用戶端 IP 位址的拒絕服務攻擊。 對於非常低的閾值,您可能會看到高於閾值的其他請求通過。

## <a name="next-steps"></a>後續步驟

- 瞭解[Azure Web 應用程式防火牆](../overview.md)。
- 瞭解有關[Azure 前門的更多詳細資訊](../../frontdoor/front-door-overview.md)。
