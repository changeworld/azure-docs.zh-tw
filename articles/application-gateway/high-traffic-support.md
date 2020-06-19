---
title: 應用程式閘道高流量支援
description: 此文章提供的指導方針可讓您設定 Azure 應用程式閘道，以支援高網路流量案例。
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: article
ms.date: 03/24/2020
ms.author: caya
ms.openlocfilehash: 56622ee2c014bd8dbca7c61ec00b927c56f63a40
ms.sourcegitcommit: 493b27fbfd7917c3823a1e4c313d07331d1b732f
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/21/2020
ms.locfileid: "83740394"
---
# <a name="application-gateway-high-traffic-support"></a>應用程式閘道高流量支援

>[!NOTE]
> 此文章說明一些建議的指導方針，可協助您設定應用程式閘道以處理因 COVID-19 危機而可能產生的高流量所導致的額外流量。

您可以使用應用程式閘道搭配 Web 應用程式防火牆 (WAF)，以透過可調整且安全的方式來管理 Web 應用程式的流量。

下列建議可協助您設定搭配 WAF 的應用程式閘道來處理額外的流量。

## <a name="use-the-v2-sku-over-v1-for-its-autoscaling-capabilities-and-performance-benefits"></a>使用 v2 SKU 而非 v1，以取得其自動調整功能和效能優點
v2 SKU 提供自動調整，以確保您的應用程式閘道可以隨著流量增加而擴大。 與 v1 相比，其還具有其他顯著的效能優勢，例如提升 5 倍的 TLS 卸載效能、更快的部署和更新時間，區域備援等等。 如需詳細資訊，請參閱 [v2 文件](https://docs.microsoft.com/azure/application-gateway/application-gateway-autoscaling-zone-redundant)。 

## <a name="set-maximum-instance-count-to-the-maximum-possible-125"></a>將最大執行個體計數設定為最大可能值 (125)
 
假設您有應用程式閘道 v2 SKU，將最大執行個體計數設為 125 的最大可能值，可以讓應用程式閘道視需要擴增。 這可讓其處理應用程式中可能增加的流量。 您只需要支付所使用的容量單位 (CU) 費用。  

## <a name="set-your-minimum-instance-count-based-on-your-average-cu-usage"></a>根據您的平均 CU 使用量來設定最小執行個體計數

在您有應用程式閘道 v2 SKU 的情況下，自動調整需要六到七分鐘的時間來擴增。在使用較高的最小執行個體計數的情況下，當負載增加時，應用程式閘道可以更妥善地處理您的流量，因為流量尖峰並不需要進行自動調整作業。  

## <a name="alert-if-a-certain-metric-surpasses-75-of-average-cu-utilization"></a>當特定計量超過平均 CU 使用率的 75% 時發出警示 
如需計量的詳細說明和其他逐步解說，請參閱[應用程式閘道計量文件](https://docs.microsoft.com/azure/application-gateway/application-gateway-metrics#metrics-visualization) \(部分機器翻譯\)。 

### <a name="example-setting-up-an-alert-on-75-of-average-cu-usage"></a>範例：針對平均 CU 使用量的 75% 設定警示

這個範例會示範如何使用 Azure 入口網站來設定會在達到平均 CU 使用量的 75% 時觸發的警示。 
1. 瀏覽至您的**應用程式閘道**。
2. 在左側面板中，選取 [監視] 索引標籤下的 [計量]。 
3. 新增 [平均目前計算單位] 的計量。 
![設定 WAF 計量](./media/application-gateway-covid-guidelines/waf-setup-metrics.png)
4. 如果您已將最小執行個體計數設定為您的平均 CU 使用量，請繼續設定會在已使用最小執行個體計數的 75% 時觸發的警示。 例如，如果您的平均使用量是 10 個 CU，請針對 7.5 個 CU 設定警示。 這會在使用量增加時提醒您，並給您時間回應。 如果您認為此流量將會持續，則可以提高最小值以在流量可能增加時提示您。 
![設定 WAF 警示](./media/application-gateway-covid-guidelines/waf-setup-monitoring-alert.png)

> [!NOTE]
> 您可以視您對潛在流量尖峰的所需敏感性程度，將警示設定為在較低或較高的 CU 使用量百分比時發生。

## <a name="set-up-waf-with-geofiltering-and-bot-protection-to-stop-attacks"></a>設定具有地理篩選和 Bot 保護的 WAF 以阻止攻擊
如果您想要在應用程式前方加入額外的安全性層級，請使用應用程式閘道 WAF_v2 SKU 來取得 WAF 功能。 您可以設定 v2 SKU 以只允許從指定的國家/地區存取您的應用程式。 您可以設定 WAF 自訂規則，以根據地理位置明確允許或封鎖流量。 如需詳細資訊，請參閱[地理篩選自訂規則](https://docs.microsoft.com/azure/web-application-firewall/ag/geomatch-custom-rules) \(部分機器翻譯\) 和[如何透過 PowerShell 設定應用程式閘道 WAF_v2 SKU 上的自訂規則](https://docs.microsoft.com/azure/web-application-firewall/ag/configure-waf-custom-rules) \(部分機器翻譯\)。

啟用 Bot 保護以封鎖已知的不良 Bot。 這應該會減少到達您應用程式的流量。 如需詳細資訊，請參閱 [Bot 保護與設定指示](https://docs.microsoft.com/azure/web-application-firewall/ag/configure-waf-custom-rules) \(部分機器翻譯\)。

## <a name="turn-on-diagnostics-on-application-gateway-and-waf"></a>在應用程式閘道和 WAF 上開啟診斷

診斷記錄可讓您檢視防火牆記錄、效能記錄和存取記錄。 您可以在 Azure 中使用這些記錄來管理應用程式閘道並對其進行疑難排解。 如需詳細資訊，請參閱[診斷文件](https://docs.microsoft.com/azure/application-gateway/application-gateway-diagnostics#diagnostic-logging) \(部分機器翻譯\)。 

## <a name="set-up-an-tls-policy-for-extra-security"></a>設定 TLS 原則以提供額外的安全性
請確定您使用的是最新的 TLS 原則版本 ([AppGwSslPolicy20170401S](https://docs.microsoft.com/azure/application-gateway/application-gateway-ssl-policy-overview#appgwsslpolicy20170401s) \(部分機器翻譯\))。 這會強制執行 TLS 1.2 和更強的加密。 如需詳細資訊，請參閱[透過 PowerShell 設定 TLS 原則版本和加密套件](https://docs.microsoft.com/azure/application-gateway/application-gateway-configure-ssl-policy-powershell) \(部分機器翻譯\)。
