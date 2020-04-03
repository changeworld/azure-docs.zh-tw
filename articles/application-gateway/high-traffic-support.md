---
title: 應用程式閘道高流量支援
description: 本文提供了配置 Azure 應用程式閘道以支援高網路流量方案的指導。
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: article
ms.date: 03/24/2020
ms.author: caya
ms.openlocfilehash: 65f404b52a5fc06d8fa5bb5aad291e57fde8caba
ms.sourcegitcommit: bc738d2986f9d9601921baf9dded778853489b16
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/02/2020
ms.locfileid: "80617192"
---
# <a name="application-gateway-high-traffic-support"></a>應用程式閘道高流量支援

>[!NOTE]
> 本文介紹了一些建議的指南,以説明您設置應用程式網關來處理由於 COVID-19 危機可能導致的高流量而導致的額外流量。

您可以將應用程式閘道與 Web 應用程式防火牆 (WAF) 一起用於可擴充和安全的方式來管理 Web 應用程式的流量。

以下建議可説明您使用 WAF 設定應用程式閘道以處理額外的流量。

## <a name="use-the-v2-sku-over-v1-for-its-autoscaling-capabilities-and-performance-benefits"></a>使用 v2 SKU 超過 v1 來增強其自動縮放功能和性能優勢
v2 SKU 提供自動縮放,以確保應用程式閘道可以隨著流量的增加而擴展。 它還提供其他顯著的性能優勢,例如與 v1 相比,TLS 卸載性能提高 5 倍、部署和更新時間更快、區域冗餘等。 有關詳細資訊,請參閱我們的[v2 文件](https://docs.microsoft.com/azure/application-gateway/application-gateway-autoscaling-zone-redundant)。 

## <a name="set-maximum-instance-count-to-the-maximum-possible-125"></a>將最大實體計數設定為最大可能 (125)
 
假設您有應用程式閘道 v2 SKU,將最大實例計數設置為最大可能值 125,使應用程式閘道可以根據需要橫向擴展。 這允許它處理應用程式可能的流量增加。 您只能為使用的容量單位 (C) 收費。  

## <a name="set-your-minimum-instance-count-based-on-your-average-cu-usage"></a>依您的平均 CU 使用方式設定最小實體計數

假設您有應用程式閘道 v2 SKU,自動縮放需要 6 到 7 分鐘才能橫向擴展。使用較高的最小實例計數時,應用程式閘道可以更好地在負載增加時處理流量,因為流量峰值不需要自動縮放操作。  

## <a name="alert-if-a-certain-metric-surpasses-75-of-average-cu-utilization"></a>如果某個指標超過平均 CU 利用率的 75%,則發出警報 
有關指標和其他演練的詳細說明,請參閱[應用程式閘道文件](https://docs.microsoft.com/azure/application-gateway/application-gateway-metrics#metrics-visualization)。 

### <a name="example-setting-up-an-alert-on-75-of-average-cu-usage"></a>範例:在平均 CU 使用率的 75% 上設定警報

此示例演示如何在達到平均 CU 使用量的 75% 時使用 Azure 門戶設置警報。 
1. 瀏覽到**應用程式閘道**。
2. 在左邊面板上,在「**監視」** 選項卡下選擇 **「指標**」。 
3. 添加**平均當前計算單位的**指標。 
![設定 WAF 指標](./media/application-gateway-covid-guidelines/waf-setup-metrics.png)
4. 如果將最小實例計數設置為平均 CU 使用,請繼續並在使用最小實例的 75% 時設置警報。 例如,如果您的平均使用量為 10 個 C,請設置 7.5 個庫的警報。 如果使用量增加,這將提醒您,並為您提供回應時間。 如果您認為此流量將持續,以提醒您流量可能會增加,則可以提高最低流量。 
![設定 WAF 警示](./media/application-gateway-covid-guidelines/waf-setup-monitoring-alert.png)

> [!NOTE]
> 您可以將警報設置為以較低的 CU 利用率百分比或更高的 CU 利用率百分比發生,具體取決於您希望對潛在流量峰值的敏感程度。

## <a name="set-up-waf-with-geofiltering-and-bot-protection-to-stop-attacks"></a>使用地理篩選與自動程式保護設定 WAF 以停止攻擊
如果要在應用程式前面增加一層安全性,請使用應用程式閘道WAF_v2SKU進行WAF功能。 您可以將 v2 SKU 配置為僅允許從給定國家/地區訪問您的應用程式。 設置 WAF 自訂規則以根據地理位置顯示式允許或阻止流量。 有關詳細資訊,請參閱[地理篩選自訂規則](https://docs.microsoft.com/azure/web-application-firewall/ag/geomatch-custom-rules)[以及如何透過 PowerShell 在應用程式閘道WAF_v2 SKU 上設定自訂規則](https://docs.microsoft.com/azure/web-application-firewall/ag/configure-waf-custom-rules)。

啟用自動程式保護以阻止已知的壞機器人。 這將減少訪問應用程式的流量。 有關詳細資訊,請參閱[使用設定說明的機器人保護](https://docs.microsoft.com/azure/web-application-firewall/ag/configure-waf-custom-rules)。

## <a name="turn-on-diagnostics-on-application-gateway-and-waf"></a>開啟應用程式閘道與 WAF 上的診斷

診斷日誌允許您查看防火牆日誌、性能日誌和訪問日誌。 您可以使用 Azure 中的這些紀錄來管理應用程式閘道並排除應用程式閘道的故障。 有關詳細資訊,請參閱我們的[診斷文檔](https://docs.microsoft.com/azure/application-gateway/application-gateway-diagnostics#diagnostic-logging)。 

## <a name="set-up-an-tls-policy-for-extra-security"></a>設定 TLS 政策以提供額外的安全性
確保您使用的是最新的 TLS 策略版本[(AppGwSslPolicy20170401S](https://docs.microsoft.com/azure/application-gateway/application-gateway-ssl-policy-overview#appgwsslpolicy20170401s))。 這將強制執行 TLS 1.2 和更強的密碼。 有關詳細資訊,請參閱透過[PowerShell 設定 TLS 策略版本和密碼套件](https://docs.microsoft.com/azure/application-gateway/application-gateway-configure-ssl-policy-powershell)。
