---
title: Azure Front Door 服務上的 Azure Web 應用程式防火牆-常見問題
description: 本文提供 Azure Front Door 上的 Web 應用程式防火牆常見問題的解答
services: web-application-firewall
author: vhorne
ms.service: web-application-firewall
ms.devlang: na
ms.topic: article
ms.date: 05/05/2020
ms.author: victorh
ms.openlocfilehash: 5c2763112b1aa2d58f5dc57cea72a3d0bdea961e
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/24/2020
ms.locfileid: "95545664"
---
# <a name="frequently-asked-questions-for-azure-web-application-firewall-on-azure-front-door-service"></a>Azure Front Door Service 上 Azure Web 應用程式防火牆的常見問題

本文將回答有關 Azure Web 應用程式防火牆 (WAF) Azure Front Door 服務特性和功能的常見問題。 

## <a name="what-is-azure-waf"></a>什麼是 Azure WAF？

Azure WAF 是一種 Web 應用程式防火牆，可協助保護您的 Web 應用程式免於遭受常見的威脅，例如 SQL 插入式攻擊、跨網站指令碼和其他 web 惡意探索。 您可以定義由自訂和受控規則組合所組成的 WAF 原則，以控制對 Web 應用程式的存取。

Azure WAF 原則可以套用至裝載於應用程式閘道或 Azure Front Door 的 Web 應用程式。

## <a name="what-is-waf-on-azure-front-door"></a>Azure Front Door 上的 WAF 為何？ 

Azure Front Door 是可高度擴充、全域散發的應用程式和內容傳遞網路。 當 azure WAF 與 Front Door 整合時，會在 Azure 網路邊緣停止拒絕服務和鎖定目標的應用程式攻擊，在進入您的虛擬網路之前，接近攻擊來源，提供保護而不會犧牲效能。

## <a name="does-azure-waf-support-https"></a>Azure WAF 是否支援 HTTPS？

Front Door 提供 TLS 卸載。 WAF 與 Front Door 原生整合，並可在要求解密後進行檢查。

## <a name="does-azure-waf-support-ipv6"></a>Azure WAF 是否支援 IPv6？

可以。 您可以設定 IPv4 和 IPv6 的 IP 限制。

## <a name="how-up-to-date-are-the-managed-rule-sets"></a>受控規則集的最新狀態為何？

我們最好隨時掌握不斷變化的威脅環境。 更新新的規則後，就會使用新的版本號碼將其新增至預設規則集。

## <a name="what-is-the-propagation-time-if-i-make-a-change-to-my-waf-policy"></a>如果我對 WAF 原則進行變更，會有什麼傳播時間？

全域部署 WAF 原則通常需要5分鐘的時間，而且通常會更快完成。

## <a name="can-waf-policies-be-different-for-different-regions"></a>不同區域的 WAF 原則是否可以不同？

與 Front Door 整合時，WAF 是全域資源。 相同的設定會套用到所有 Front Door 位置。
 
## <a name="how-do-i-limit-access-to-my-back-end-to-be-from-front-door-only"></a>如何? 限制只能從 Front Door 存取我的後端嗎？

您可以在後端設定 IP 存取控制清單，以只允許 Front Door 輸出 IP 位址範圍，以及拒絕來自網際網路的任何直接存取。 您可在虛擬網路上使用服務標記。 此外，您可以確認 [X 轉寄-主機 HTTP 標頭] 欄位對您的 web 應用程式有效。

## <a name="which-azure-waf-options-should-i-choose"></a>我應該選擇哪一種 Azure WAF 選項？

在 Azure 中套用 WAF 原則時，有兩個選項。 WAF 與 Azure Front Door 是全域散發的邊緣安全性解決方案。 使用應用程式閘道的 WAF 是一個區域專用的解決方案。 建議您根據整體效能和安全性需求來選擇解決方案。 如需詳細資訊，請參閱 [使用 Azure 的應用程式傳遞套件進行負載平衡](../../frontdoor/front-door-lb-with-azure-app-delivery-suite.md)。

## <a name="whats-the-recommended-approach-to-enabling-waf-on-front-door"></a>在 Front Door 上啟用 WAF 的建議方法為何？

當您在現有的應用程式上啟用 WAF 時，通常會有錯誤的正面偵測，其中 WAF 規則會偵測到合法的流量為威脅。 為了將對您的使用者造成影響的風險降至最低，我們建議您執行下列流程：

* 在 [**偵測** 模式](./waf-front-door-create-portal.md#change-mode) 中啟用 WAF，以確保當您完成此程式時，WAF 不會封鎖要求。
  > [!IMPORTANT]
  > 此程式描述當您的優先順序是要將應用程式使用者的干擾降至最低時，如何在新的或現有的解決方案上啟用 WAF。 如果您遭受攻擊或遭受威脅，您可以改為立即在 **預防** 模式中部署 WAF，並使用微調程式來監視和微調一段時間的 WAF。 這可能會導致某些合法的流量遭到封鎖，這也是為什麼我們只建議您在威脅的情況下執行此操作。
* 遵循我們 [的指導方針來調整 WAF](./waf-front-door-tuning.md)。 此程式需要您啟用診斷記錄、定期審核記錄，以及新增規則排除專案和其他緩和措施。
* 重複此整個程式，定期檢查記錄，直到您滿意未封鎖合法的流量為止。 整個過程可能需要數周的時間。 在理想的情況下，您應該會在每次調整變更之後看到較少的誤報偵測。
* 最後，在 **預防模式** 中啟用 WAF。
* 即使在生產環境中執行 WAF，您還是應該持續監視記錄，以找出任何其他的誤報偵測。 定期檢查記錄檔也可協助您識別任何已封鎖的實際攻擊嘗試。

## <a name="do-you-support-same-waf-features-in-all-integrated-platforms"></a>您是否支援所有整合平臺中的相同 WAF 功能？

目前，ModSec CRS 2.2.9、CRS 3.0 和 CRS 3.1 規則僅支援應用程式閘道上的 WAF。 只有 Azure Front Door 上的 WAF 才支援速率限制、地區篩選和 Azure 受管理的預設規則集規則。

## <a name="is-ddos-protection-integrated-with-front-door"></a>DDoS protection 是否與 Front Door 整合？ 

Azure Front Door 可以在 Azure 網路邊緣全球散發，以吸收和地理位置隔離大型磁片區攻擊。 您可以建立自訂 WAF 原則，以自動封鎖 HTTP (s 的限制，並對有已知簽章的) 攻擊進行評分。 此外，您可以在您的後端部署所在的 VNet 上啟用 DDoS 保護標準。 Azure DDoS 保護標準客戶可獲得額外的權益，包括成本保護、SLA 保證，以及從 DDoS 快速回應小組存取專家，以在攻擊期間提供立即協助。 如需詳細資訊，請參閱 [Front Door 上的 DDoS 保護](../../frontdoor/front-door-ddos.md)。

## <a name="why-do-additional-requests-above-the-threshold-configured-for-my-rate-limit-rule-get-passed-to-my-backend-server"></a>為什麼額外要求超過針對我的速率限制規則設定的閾值會傳遞至我的後端伺服器？

速率限制規則可以限制來自任何用戶端 IP 位址的異常高流量。 您可以設定在一分鐘或五分鐘的期間內，從用戶端 IP 位址允許的 web 要求數目閾值。 針對細微的速率控制，速率限制可以與其他比對條件結合，例如 HTTP (S) 參數比對。 

來自相同用戶端的要求通常會抵達相同的 Front Door 伺服器。 在此情況下，您會看到超過閾值的其他要求會立即遭到封鎖。 

不過，來自相同用戶端的要求可能會抵達尚未重新整理 [速率限制] 計數器的不同 Front Door 伺服器。 例如，用戶端可能會針對每個要求開啟新的連線，且閾值較低。 在此情況下，對新 Front Door 伺服器的第一個要求會通過速率限制檢查。 速率限制閾值通常會設定為 [高]，以防止來自任何用戶端 IP 位址的拒絕服務攻擊。 針對非常低的臨界值，您可能會看到超過閾值的其他要求通過。

## <a name="next-steps"></a>後續步驟

- 了解 [Azure Web 應用程式防火牆](../overview.md)。
- 深入了解 [Azure Front Door](../../frontdoor/front-door-overview.md)。