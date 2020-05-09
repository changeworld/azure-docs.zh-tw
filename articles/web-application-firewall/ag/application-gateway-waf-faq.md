---
title: 應用程式閘道上的 Azure Web 應用程式防火牆-常見問題
description: 本文提供有關應用程式閘道上 Web 應用程式防火牆常見問題的解答
services: web-application-firewall
author: vhorne
ms.service: web-application-firewall
ms.topic: article
ms.date: 05/05/2020
ms.author: victorh
ms.openlocfilehash: 57081cd948bcee1261415eae31f91b3c61f08c9f
ms.sourcegitcommit: 11572a869ef8dbec8e7c721bc7744e2859b79962
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/05/2020
ms.locfileid: "82842847"
---
# <a name="frequently-asked-questions-for-azure-web-application-firewall-on-application-gateway"></a>應用程式閘道上的 Azure Web 應用程式防火牆常見問題

本文會回答有關 Azure Web 應用程式防火牆（WAF）應用程式閘道特性和功能的常見問題。 

## <a name="what-is-azure-waf"></a>什麼是 Azure WAF？

Azure WAF 是一種 web 應用程式防火牆，可協助保護您的 web 應用程式免于遭受常見的威脅，例如 SQL 插入式攻擊、跨網站腳本和其他 web 惡意探索。 您可以定義由自訂和受控規則組合所組成的 WAF 原則，以控制對 web 應用程式的存取。

Azure WAF 原則可以套用至裝載于應用程式閘道或 Azure Front 門板的 web 應用程式。

## <a name="what-features-does-the-waf-sku-support"></a>WAF SKU 支援哪些功能？

WAF SKU 支援標準 SKU 中所有可用的功能。

## <a name="how-do-i-monitor-waf"></a>如何監視 WAF？

透過診斷記錄來監視 WAF。 如需詳細資訊，請參閱[應用程式閘道的診斷記錄和計量](../../application-gateway/application-gateway-diagnostics.md)。

## <a name="does-detection-mode-block-traffic"></a>偵測模式是否會封鎖流量？

否。 偵測模式只會記錄觸發 WAF 規則的流量。

## <a name="can-i-customize-waf-rules"></a>我可以自訂 WAF 規則嗎？

可以。 如需詳細資訊，請參閱[自訂 WAF 規則群組和規則](application-gateway-customize-waf-rules-portal.md)。

## <a name="what-rules-are-currently-available-for-waf"></a>哪些規則目前可供 WAF？

WAF 目前支援 CRS [2.2.9](application-gateway-crs-rulegroups-rules.md#owasp229)、 [3.0](application-gateway-crs-rulegroups-rules.md#owasp30)和[3.1](application-gateway-crs-rulegroups-rules.md#owasp31)。 這些規則會針對開啟 Web 應用程式安全性專案（OWASP）識別的大多數前10大弱點，提供基準安全性： 

* SQL 插入式攻擊保護
* 跨網站腳本保護
* 防止常見的 web 攻擊，例如命令插入、HTTP 要求走私、HTTP 回應分割和遠端檔案包含攻擊
* 防範 HTTP 通訊協定違規
* 防範 HTTP 通訊協定異常行為，例如遺漏主機使用者代理程式和接受標頭
* 防範 Bot、編目程式和掃描器
* 偵測常見的應用程式錯誤（也就是 Apache、IIS 等）

如需詳細資訊，請參閱[OWASP 前10大弱點](https://www.owasp.org/index.php/Top10#OWASP_Top_10_for_2013)。

## <a name="does-waf-support-ddos-protection"></a>WAF 是否支援 DDoS 保護？

可以。 您可以在部署應用程式閘道的虛擬網路上啟用 DDoS 保護。 此設定可確保 Azure DDoS 保護服務也會保護應用程式閘道虛擬 IP （VIP）。


## <a name="next-steps"></a>後續步驟

- 瞭解[Azure Web 應用程式防火牆](../overview.md)。
- 深入瞭解[Azure Front 大門](../../frontdoor/front-door-overview.md)。
