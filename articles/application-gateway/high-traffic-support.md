---
title: 應用程式閘道高流量支援
description: 此文章提供的指導方針可讓您設定 Azure 應用程式閘道，以支援高網路流量案例。
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: conceptual
ms.date: 03/24/2020
ms.author: caya
ms.openlocfilehash: 3854e7f3c19f1724a2df1508c9fa519809e07ba9
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/17/2020
ms.locfileid: "94658667"
---
# <a name="application-gateway-high-traffic-support"></a>應用程式閘道高流量支援

>[!NOTE]
> 本文說明一些建議的指導方針，可協助您設定應用程式閘道，以處理可能發生的高流量量的額外流量。 警示閾值是純粹的建議和一般特性。 使用者可以根據其工作負載和預期的使用方式來判斷警示閾值。

您可以使用應用程式閘道搭配 Web 應用程式防火牆 (WAF)，以透過可調整且安全的方式來管理 Web 應用程式的流量。

您必須根據您的流量和一些緩衝區來調整應用程式閘道，讓您準備好任何流量激增或尖峰，並將它在您的 QoS 中可能造成的影響降至最低。 下列建議可協助您設定搭配 WAF 的應用程式閘道來處理額外的流量。

請查看 [計量檔](./application-gateway-metrics.md) ，以取得應用程式閘道所提供的完整計量清單。 請參閱在 Azure 入口網站中將 [計量視覺化](./application-gateway-metrics.md#metrics-visualization) ，以及如何設定計量警示的 [Azure 監視器檔](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-metric) 。

## <a name="scaling-for-application-gateway-v1-sku-standardwaf-sku"></a>應用程式閘道 v1 SKU (Standard/WAF SKU) 的調整

### <a name="set-your-instance-count-based-on-your-peak-cpu-usage"></a>根據您的尖峰 CPU 使用量設定您的實例計數
如果您使用 v1 SKU 閘道，您可以將應用程式閘道設定為最多32實例以進行調整。 在過去一個月內檢查應用程式閘道的 CPU 使用率是否有任何超過80% 的尖峰，可作為您要監視的度量。 建議您根據尖峰使用量設定實例計數，並使用10% 到20% 的額外緩衝區來處理任何流量尖峰。

### <a name="use-the-v2-sku-over-v1-for-its-autoscaling-capabilities-and-performance-benefits"></a>使用 v2 SKU 而非 v1，以取得其自動調整功能和效能優點
v2 SKU 提供自動調整，以確保您的應用程式閘道可以隨著流量增加而擴大。 與 v1 相比，其還具有其他顯著的效能優勢，例如提升 5 倍的 TLS 卸載效能、更快的部署和更新時間，區域備援等等。 如需詳細資訊，請參閱我們的 [v2 檔](./application-gateway-autoscaling-zone-redundant.md) ，並查看 v1 至 v2 的 [遷移檔](./migrate-v1-v2.md) ，以瞭解如何將現有的 v1 sku 閘道遷移至 v2 sku。 

## <a name="autoscaling-for-application-gateway-v2-sku-standard_v2waf_v2-sku"></a>自動調整應用程式閘道 v2 SKU (Standard_v2/WAF_v2 SKU) 

### <a name="set-maximum-instance-count-to-the-maximum-possible-125"></a>將最大執行個體計數設定為最大可能值 (125)
 
針對應用程式閘道 v2 SKU，將 [最大實例計數] 設定為125的最大可能值，可讓應用程式閘道視需要相應放大。 這可讓其處理應用程式中可能增加的流量。 您只需要支付所使用的容量單位 (CU) 費用。 

請務必檢查您子網中的子網大小和可用的 IP 位址計數，並根據該值設定最大實例計數。 如果您的子網沒有足夠的空間可容納，您將必須在具有足夠容量的相同或不同子網中重新建立閘道。 

### <a name="set-your-minimum-instance-count-based-on-your-average-compute-unit-usage"></a>根據您的平均計算單位使用量來設定最小實例計數

針對應用程式閘道 v2 SKU，自動調整需要六到七分鐘的時間來向外延展，並布建額外的實例集，準備好接受流量。 在那之前，如果流量有短暫的尖峰，您現有的閘道實例可能會在壓力下發生，這可能會導致非預期的流量延遲或遺失。 

建議您將最小實例計數設定為最佳層級。 例如，如果您需要50實例來處理尖峰負載的流量，則將最小值25設定為30是不錯的想法，而不是在 <10，所以即使流量有短暫的高載，應用程式閘道也可以處理它，並提供足夠的時間讓自動調整回應並生效。

檢查您的計算單位計量是否已超過一個月。 計算單位計量是您閘道 CPU 使用率的標記法，並根據尖峰使用量除以10，您可以設定所需的實例數目下限。 請注意，1個應用程式閘道實例最少可以處理10個計算單位

## <a name="manual-scaling-for-application-gateway-v2-sku-standard_v2waf_v2"></a>應用程式閘道 v2 SKU 的手動調整 (Standard_v2/WAF_v2) 

### <a name="set-your-instance-count-based-on-your-peak-compute-unit-usage"></a>根據您的尖峰計算單位使用量設定您的實例計數 

不同于自動調整，在手動調整的情況下，您必須根據流量需求手動設定應用程式閘道的實例數目。 建議您根據尖峰使用量設定實例計數，並使用10% 到20% 的額外緩衝區來處理任何流量尖峰。 例如，如果您的流量需要尖峰的50實例，請布建55至60實例，以處理可能發生的非預期流量尖峰。

檢查您的計算單位計量是否已超過一個月。 計算單位計量代表您閘道的 CPU 使用率，並根據尖峰使用量除以10，您可以設定所需的實例數目，因為1個應用程式閘道實例最少可以處理10個計算單位

## <a name="monitoring-and-alerting"></a>監視和警示

若要取得任何流量或使用狀況異常的通知，您可以設定特定計量的警示。 如需應用程式閘道所提供的完整度量清單，請參閱 [計量檔](./application-gateway-metrics.md) 。 請參閱在 Azure 入口網站中將 [計量視覺化](./application-gateway-metrics.md#metrics-visualization) ，以及如何設定計量警示的 [Azure 監視器檔](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-metric) 。

## <a name="alerts-for-application-gateway-v1-sku-standardwaf"></a>應用程式閘道 v1 SKU (Standard/WAF) 的警示

### <a name="alert-if-average-cpu-utilization-crosses-80"></a>平均 CPU 使用率超過80% 時發出警示

在正常情況下，CPU 使用量不應經常超過 90%，因為這可能會導致應用程式閘道背後的網站中出現延遲，並且會中斷用戶端體驗。 您可以藉由增加實例計數或移至較大的 SKU 大小或進行這兩項操作，來間接控制或改進 CPU 使用率。 如果 CPU 使用率計量超過80% 平均，則設定警示。

### <a name="alert-if-unhealthy-host-count-crosses-threshold"></a>如果狀況不良的主機計數超出閾值則發出警示

此標準表示應用程式閘道無法成功探查的後端伺服器數目。 這將會攔截應用程式閘道實例無法連線到後端的問題。 如果此數位超過後端容量的20%，則發出警示。 例如 如果您目前的後端集區中有30部後端伺服器，請在狀況不良的主機計數超過6時設定警示。

### <a name="alert-if-response-status-4xx-5xx-crosses-threshold"></a>如果回應狀態 (4xx，5xx) 超過閾值，則發出警示 

當應用程式閘道回應狀態為4xx 或5xx 時建立警示。 由於暫時性的問題，可能會出現偶而出現的4xx 或5xx 回應。 您應觀察生產環境中的閘道，以判斷靜態閾值或針對警示使用動態閾值。

### <a name="alert-if-failed-requests-crosses-threshold"></a>當失敗的要求超出閾值時發出警示 

當失敗的要求計量超出閾值時建立警示。 您應觀察生產環境中的閘道，以判斷靜態閾值或針對警示使用動態閾值。

## <a name="alerts-for-application-gateway-v2-sku-standard_v2waf_v2"></a>應用程式閘道 v2 SKU 的警示 (Standard_v2/WAF_v2) 

### <a name="alert-if-compute-unit-utilization-crosses-75-of-average-usage"></a>如果計算單位使用率超過平均使用量的75%，則發出警示 

計算單位是應用程式閘道計算使用率的測量單位。 檢查您在過去一個月中的平均計算單位使用量，如果其超過75%，請設定警示。 例如，如果您的平均使用量是10個計算單位，請在 7.5 Cu 上設定警示。 這會在使用量增加時提醒您，並給您時間回應。 如果您認為此流量將會持續，則可以提高最小值以在流量可能增加時提示您。 依照上述的調整建議，視需要相應放大。

### <a name="example-setting-up-an-alert-on-75-of-average-cu-usage"></a>範例：針對平均 CU 使用量的 75% 設定警示

這個範例會示範如何使用 Azure 入口網站來設定會在達到平均 CU 使用量的 75% 時觸發的警示。 
1. 瀏覽至您的 **應用程式閘道**。
2. 在左側面板中，選取 [監視] 索引標籤下的 [計量]。 
3. 新增 [平均目前計算單位] 的計量。 
![設定 WAF 計量](./media/application-gateway-covid-guidelines/waf-setup-metrics.png)
4. 如果您已將最小執行個體計數設定為您的平均 CU 使用量，請繼續設定會在已使用最小執行個體計數的 75% 時觸發的警示。 例如，如果您的平均使用量是 10 個 CU，請針對 7.5 個 CU 設定警示。 這會在使用量增加時提醒您，並給您時間回應。 如果您認為此流量將會持續，則可以提高最小值以在流量可能增加時提示您。 
![設定 WAF 警示](./media/application-gateway-covid-guidelines/waf-setup-monitoring-alert.png)

> [!NOTE]
> 您可以視您對潛在流量尖峰的所需敏感性程度，將警示設定為在較低或較高的 CU 使用量百分比時發生。

### <a name="alert-if-capacity-unit-utilization-crosses-75-of-peak-usage"></a>如果容量單位使用率超過尖峰使用量的75%，則發出警示 

容量單位代表輸送量、計算和連線計數的整體閘道使用率。 檢查最後一個月的最大容量單位使用量，如果其超過75%，請設定警示。 例如，如果您的使用量上限為100容量單位，請在 75 Cu 上設定警示。 視需要依照上述兩個建議進行相應放大。

### <a name="alert-if-unhealthy-host-count-crosses-threshold"></a>如果狀況不良的主機計數超出閾值則發出警示 

此標準表示應用程式閘道無法成功探查的後端伺服器數目。 這將會攔截應用程式閘道實例無法連線到後端的問題。 如果此數位超過後端容量的20%，則發出警示。 例如 如果您目前的後端集區中有30部後端伺服器，請在狀況不良的主機計數超過6時設定警示。

### <a name="alert-if-response-status-4xx-5xx-crosses-threshold"></a>如果回應狀態 (4xx，5xx) 超過閾值，則發出警示 

當應用程式閘道回應狀態為4xx 或5xx 時建立警示。 由於暫時性的問題，可能會出現偶而出現的4xx 或5xx 回應。 您應觀察生產環境中的閘道，以判斷靜態閾值或針對警示使用動態閾值。

### <a name="alert-if-failed-requests-crosses-threshold"></a>當失敗的要求超出閾值時發出警示 

當失敗的要求計量超出閾值時建立警示。 您應觀察生產環境中的閘道，以判斷靜態閾值或針對警示使用動態閾值。

### <a name="alert-if-backend-last-byte-response-time-crosses-threshold"></a>當後端最後一個位元組回應時間超出閾值時發出警示 

此計量指出開始建立與後端伺服器之間的連線，並接收回應本文的最後一個位元組之間的時間間隔。 如果後端回應延遲比平常的特定臨界值更高，則建立警示。 例如，將此設定為當後端回應延遲從一般值增加超過30% 時收到警示。

### <a name="alert-if-application-gateway-total-time-crosses-threshold"></a>應用程式閘道總時間超出閾值時發出警示

這是來自應用程式閘道接收 HTTP 要求的第一個位元組到最後一個回應位元組傳送給用戶端的時間的間隔。 如果後端回應延遲比平常的特定臨界值更高，則應建立警示。 例如，他們可以設定此值，以在總時間延遲從一般值增加超過30% 時收到警示。

## <a name="set-up-waf-with-geofiltering-and-bot-protection-to-stop-attacks"></a>設定具有地理篩選和 Bot 保護的 WAF 以阻止攻擊
如果您想要在應用程式前方加入額外的安全性層級，請使用應用程式閘道 WAF_v2 SKU 來取得 WAF 功能。 您可以設定 v2 SKU 以只允許從指定的國家/地區存取您的應用程式。 您可以設定 WAF 自訂規則，以根據地理位置明確允許或封鎖流量。 如需詳細資訊，請參閱[地理篩選自訂規則](../web-application-firewall/ag/geomatch-custom-rules.md) \(部分機器翻譯\) 和[如何透過 PowerShell 設定應用程式閘道 WAF_v2 SKU 上的自訂規則](../web-application-firewall/ag/configure-waf-custom-rules.md) \(部分機器翻譯\)。

啟用 Bot 保護以封鎖已知的不良 Bot。 這應該會減少到達您應用程式的流量。 如需詳細資訊，請參閱 [Bot 保護與設定指示](../web-application-firewall/ag/configure-waf-custom-rules.md) \(部分機器翻譯\)。

## <a name="turn-on-diagnostics-on-application-gateway-and-waf"></a>在應用程式閘道和 WAF 上開啟診斷

診斷記錄可讓您檢視防火牆記錄、效能記錄和存取記錄。 您可以在 Azure 中使用這些記錄來管理應用程式閘道並對其進行疑難排解。 如需詳細資訊，請參閱[診斷文件](./application-gateway-diagnostics.md#diagnostic-logging) \(部分機器翻譯\)。 

## <a name="set-up-an-tls-policy-for-extra-security"></a>設定 TLS 原則以提供額外的安全性
請確定您使用的是最新的 TLS 原則版本 ([AppGwSslPolicy20170401S](./application-gateway-ssl-policy-overview.md#appgwsslpolicy20170401s) \(部分機器翻譯\))。 這會強制執行 TLS 1.2 和更強的加密。 如需詳細資訊，請參閱[透過 PowerShell 設定 TLS 原則版本和加密套件](./application-gateway-configure-ssl-policy-powershell.md) \(部分機器翻譯\)。