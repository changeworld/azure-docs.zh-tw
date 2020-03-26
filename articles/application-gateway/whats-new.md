---
title: Azure 應用程式閘道的新功能
description: 了解 Azure 應用程式閘道的新功能，例如最新的版本資訊、已知問題、錯誤 (bug) 修正、已被取代的功能，以及即將進行的變更。
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: overview
ms.date: 4/30/2019
ms.author: victorh
ms.openlocfilehash: c6d4d290493bbd234ab048e613b88f8857513cc8
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/26/2020
ms.locfileid: "78299550"
---
# <a name="whats-new-in-azure-application-gateway"></a>Azure 應用程式閘道的新功能為何？

Azure 應用程式閘道會持續更新。 為了讓您隨時掌握最新的開發訊息，本文提供下列相關資訊：

- 最新版本
- 已知問題
- 錯誤修正
- 已被取代的功能

## <a name="new-features"></a>新功能

|功能  |描述  |新增日期  |
|---------|---------|---------|
|親和性 Cookie 變更 |啟用以 Cookie 為基礎的親和性時，除了現有的 ApplicationGatewayAffinity Cookie 以外，應用程式閘道也會插入另一個名為 *ApplicationGatewayAffinityCORS* 的相同 Cookie。 *ApplicationGatewayAffinityCORS* 已對其新增兩個屬性 (*SameSite=None; Secure*)，因此即使是跨原始來源的要求，仍會維護黏性工作階段。 如需詳細資訊，請參閱[應用程式閘道以 Cookie 為依據的親和性](configuration-overview.md#cookie-based-affinity)。 |2020 年 2 月 |
|探查增強功能 |透過應用程式閘道 v2 SKU 中的自訂探查增強功能，我們簡化了[探查設定](https://docs.microsoft.com/azure/application-gateway/application-gateway-create-probe-portal#create-probe-for-application-gateway-v2-sku)、加快了[隨選後端健康情況測試](https://docs.microsoft.com/azure/application-gateway/application-gateway-create-probe-portal#test-backend-health-with-the-probe)的執行速度，並新增了[更多診斷資訊](https://docs.microsoft.com/azure/application-gateway/application-gateway-backend-health-troubleshooting#error-messages)，以協助您對後端健康情況問題進行疑難排解。  |2019 年 10 月 |
|更多計量 |我們已新增下列新的計量，以協助您監視應用程式閘道 v2 SKU：[計時相關的計量](https://docs.microsoft.com/azure/application-gateway/application-gateway-metrics#timing-metrics) \(部分機器翻譯\)、後端回應狀態、接收的位元組、已傳送的位元組、用戶端 TLS 通訊協定和目前的計算單位。 請參閱[應用程式閘道 V2 SKU支援的計量](https://docs.microsoft.com/azure/application-gateway/application-gateway-metrics#metrics-supported-by-application-gateway-v2-sku) \(部分機器翻譯\)。 |2019 年 8 月 |
|WAF 自訂規則 |應用程式閘道 WAF_v2 現在支援建立自訂規則。 請參閱[應用程式閘道自訂規則](custom-waf-rules-overview.md)。 |2019 年 6 月 |
|自動調整規模、區域備援、靜態 VIP 支援 GA |已重寫 v2 SKU (支援自動調整規模、區域備援、增強效能、靜態 VIP、金鑰保存庫，及標頭) 的一般可用性。 請參閱[應用程式閘道自動調整規模文件](application-gateway-autoscaling-zone-redundant.md)。 |2019 年 4 月 |
|金鑰保存庫整合 |對於在已啟用 HTTPS 的接聽程式中附加的伺服器認證，應用程式閘道現在支援與金鑰保存庫 (公開預覽) 整合。 請參閱 [具備金鑰保存庫認證的 SSL 終止](key-vault-certs.md)。 |2019 年 4 月 |
|標頭 CRUD/重寫     |您現在可以重新撰寫 HTTP 標頭。 請參閱[教學課程：建立應用程式閘道及重寫 HTTP 標題](tutorial-http-header-rewrite-powershell.md)，以取得詳細資訊。|2018 年 12 月|
|WAF 組態和排除清單     |我們已新增更多選項，以協助您設定 WAF 和減少誤判。 如需詳細資訊，請參閱 [Web 應用程式防火牆要求大小限制與排除清單](application-gateway-waf-configuration.md)。|2018 年 12 月|
|自動調整規模、區域備援、靜態 VIP 支援      |v2 SKU 有許多改進，例如自動調整、增進的效能等等。 如需詳細資訊，請參閱[什麼是 Azure 應用程式閘道](overview.md)。|2018 年 9 月|
|清空連線     |清空連線可讓您依正常程序移除後端集區中的成員。 如需詳細資訊，請參閱[清空連線](features.md#connection-draining)。|2018 年 9 月|
|自訂錯誤頁面     |透過自訂錯誤頁面中，您可以用其餘網站的格式建立錯誤頁面。 若要這麼做，請參閱[建立應用程式閘道的自訂錯誤頁面](custom-error.md)。|2018 年 9 月|
|計量的增強功能     |透過強化的計量，您可以更清楚地檢視應用程式閘道的狀態。 若要啟用應用程式閘道的計量，請參閱[應用程式閘道的後端健康情況、診斷記錄和計量](application-gateway-diagnostics.md)。|2018 年 6 月|

## <a name="next-steps"></a>後續步驟

如需 Azure 應用程式閘道的詳細資訊，請參閱[什麼是 Azure 應用程式閘道？](overview.md)
