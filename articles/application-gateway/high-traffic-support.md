---
title: 應用程式閘道高流量磁片區支援
description: 本文提供的指引可讓您設定 Azure 應用程式閘道，以支援高網路流量量案例。
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: article
ms.date: 03/24/2020
ms.author: caya
ms.openlocfilehash: 65f404b52a5fc06d8fa5bb5aad291e57fde8caba
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "80617192"
---
# <a name="application-gateway-high-traffic-support"></a>應用程式閘道高流量支援

>[!NOTE]
> 本文說明一些建議的指導方針，可協助您設定應用程式閘道，以因應 COVID-19 危機而產生的高流量而因應額外流量。

您可以使用應用程式閘道搭配 Web 應用程式防火牆（WAF），以提供可擴充且安全的方式來管理 web 應用程式的流量。

下列建議可協助您使用 WAF 設定應用程式閘道來處理額外的流量。

## <a name="use-the-v2-sku-over-v1-for-its-autoscaling-capabilities-and-performance-benefits"></a>透過 v1 使用 v2 SKU 以取得其自動調整功能和效能優點
V2 SKU 提供自動調整，以確保您的應用程式閘道可以隨著流量增加而相應增加。 它也提供其他顯著的效能優勢，例如，比起 v1 更多的 TLS 卸載效能、更快速的部署和更新時間、區域冗余等等。 如需詳細資訊，請參閱我們的[v2 檔](https://docs.microsoft.com/azure/application-gateway/application-gateway-autoscaling-zone-redundant)。 

## <a name="set-maximum-instance-count-to-the-maximum-possible-125"></a>將最大實例計數設定為最大可能（125）
 
假設您有應用程式閘道 v2 SKU，請將 [最大實例計數] 設為125的最大可能值，讓應用程式閘道能夠視需要相應放大。 這可讓它處理您的應用程式可能增加的流量。 您只需要支付所使用的容量單位（Cu）費用。  

## <a name="set-your-minimum-instance-count-based-on-your-average-cu-usage"></a>根據您的平均 CU 使用量來設定最小實例計數

假設您有應用程式閘道 v2 SKU，則自動調整需要六到七分鐘的時間來相應放大。由於最小實例計數較高，因此當負載增加時，應用程式閘道可以更妥善地處理您的流量，因為流量尖峰並不需要自動調整作業。  

## <a name="alert-if-a-certain-metric-surpasses-75-of-average-cu-utilization"></a>當特定計量超過平均 CU 使用率的75% 時發出警示 
如需計量和其他逐步解說的詳細說明，請參閱[應用程式閘道計量檔](https://docs.microsoft.com/azure/application-gateway/application-gateway-metrics#metrics-visualization)。 

### <a name="example-setting-up-an-alert-on-75-of-average-cu-usage"></a>範例：在75% 的平均 CU 使用量上設定警示

這個範例會示範如何使用 Azure 入口網站來設定當達到平均 CU 使用量75% 時的警示。 
1. 流覽至您的**應用程式閘道**。
2. 在左面板中，選取 [**監視**] 索引標籤下的 [**計量**]。 
3. 新增**平均目前計算單位**的度量。 
![設定 WAF 度量](./media/application-gateway-covid-guidelines/waf-setup-metrics.png)
4. 如果您已將 [最小實例計數] 設定為您的平均 CU 使用量，請繼續進行，並在75% 的最小實例正在使用中時設定警示。 例如，如果您的平均使用量是 10 Cu，請在 7.5 Cu 上設定警示。 這會在使用量增加時提醒您，並提供回應時間。 如果您認為此流量會持續，以提醒您流量可能已增加，您可以提高最小值。 
![設定 WAF 警示](./media/application-gateway-covid-guidelines/waf-setup-monitoring-alert.png)

> [!NOTE]
> 您可以將警示設定為以較低或較高的 CU 使用率百分比進行，視您想要對潛在流量尖峰的敏感性程度而定。

## <a name="set-up-waf-with-geofiltering-and-bot-protection-to-stop-attacks"></a>設定具有地區篩選和 bot 保護的 WAF 以停止攻擊
如果您想要在應用程式前方加入額外的安全性層級，請使用應用程式閘道 WAF_v2 SKU 來 WAF 功能。 您可以將 v2 SKU 設定為只允許從指定的國家/地區存取您的應用程式。 您可以設定 WAF 的自訂規則，以明確地允許或封鎖以地理位置為基礎的流量。 如需詳細資訊，請參閱[地區篩選自訂規則](https://docs.microsoft.com/azure/web-application-firewall/ag/geomatch-custom-rules)和[如何透過 PowerShell 在應用程式閘道 WAF_v2 SKU 上設定自訂規則](https://docs.microsoft.com/azure/web-application-firewall/ag/configure-waf-custom-rules)。

啟用 bot 保護以封鎖已知的錯誤 bot。 這應該會減少到達您應用程式的流量量。 如需詳細資訊，請參閱[bot protection 與設定指示](https://docs.microsoft.com/azure/web-application-firewall/ag/configure-waf-custom-rules)。

## <a name="turn-on-diagnostics-on-application-gateway-and-waf"></a>在應用程式閘道和 WAF 上開啟診斷

診斷記錄可讓您查看防火牆記錄、效能記錄和存取記錄。 您可以在 Azure 中使用這些記錄來管理應用程式閘道，並對其進行疑難排解。 如需詳細資訊，請參閱我們的[診斷檔](https://docs.microsoft.com/azure/application-gateway/application-gateway-diagnostics#diagnostic-logging)。 

## <a name="set-up-an-tls-policy-for-extra-security"></a>設定 TLS 原則以提供額外的安全性
請確定您使用的是最新的 TLS 原則版本（[AppGwSslPolicy20170401S](https://docs.microsoft.com/azure/application-gateway/application-gateway-ssl-policy-overview#appgwsslpolicy20170401s)）。 這會強制執行 TLS 1.2 和更強的密碼。 如需詳細資訊，請參閱透過[PowerShell 設定 TLS 原則版本和加密套件](https://docs.microsoft.com/azure/application-gateway/application-gateway-configure-ssl-policy-powershell)。
