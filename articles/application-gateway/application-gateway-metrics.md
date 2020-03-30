---
title: 應用程式閘道的 Azure 監視器指標
description: 瞭解如何使用指標來監視應用程式閘道的性能
services: application-gateway
author: abshamsft
ms.service: application-gateway
ms.topic: article
ms.date: 2/5/2019
ms.author: absha
ms.openlocfilehash: abff2f16d9559f015417711820a993badd636f7c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80133083"
---
# <a name="metrics-for-application-gateway"></a>應用程式閘道的指標

應用程式閘道將資料點（稱為指標）發佈到[Azure 監視器](https://docs.microsoft.com/azure/azure-monitor/overview)，以顯示應用程式閘道和後端實例的性能。 這些指標是一組有序的時間序列資料中的數值，用於描述特定時間應用程式閘道的某些方面。 如果存在通過應用程式閘道流動的請求，它將測量併發送其指標，間隔 60 秒。 如果沒有請求流經應用程式閘道或指標沒有資料，則不報告該指標。 如需詳細資訊，請參閱 [Azure 監視器計量](https://docs.microsoft.com/azure/azure-monitor/platform/data-platform-metrics)。

## <a name="metrics-supported-by-application-gateway-v2-sku"></a>應用程式閘道 V2 SKU 支援的指標

### <a name="timing-metrics"></a>計時指標

應用程式閘道提供了與請求和回應相關的多個內置計時指標，這些指標均以毫秒為單位。 

![](./media/application-gateway-metrics/application-gateway-metrics.png)

> [!NOTE]
>
> 如果應用程式閘道中有多個攔截器，則始終按*攔截器*維度進行篩選，同時比較不同的延遲指標，以獲得有意義的推斷。

- **後端連線時間**

  與後端應用程式建立連接所花費的時間。 

  這包括網路延遲以及後端伺服器的 TCP 堆疊建立新連接所佔用的時間。 在 TLS 的情況下，它還包括握手所花費的時間。 

- **後端第一位元組回應時間**

  從建立與後端伺服器的連接開始到接收回應標頭的第一個位元組之間的時間間隔。 

  這近似于*後端連線時間*的總和、請求從應用程式閘道到達後端所花的時間、後端應用程式回應所花的時間（伺服器生成內容所花的時間、可能獲取資料庫查詢的時間）以及回應的第一個位元組從後端到達應用程式閘道所花的時間。

- **後端最後一個位元組回應時間**

  建立與後端伺服器的連接開始到接收回應正文的最後一個位元組之間的時間間隔。 

  這近似于*後端第一位元組回應時間*和資料傳輸時間的總和（此數位可能因請求的物件大小和伺服器網路延遲而有很大差異）。

- **應用程式閘道總時間**

  接收、處理請求及其回應所需的平均時間。 

  這是從應用程式閘道接收 HTTP 要求的第一個位元組到將最後一個回應位元組發送到用戶端的時間的間隔。 這包括應用程式閘道的處理時間、*後端最後位元組回應時間*、應用程式閘道發送所有回應和*用戶端 RTT*所佔用的時間。

- **用戶端 RTT**

  用戶端和應用程式閘道之間的平均往返時間。



這些指標可用於確定觀察到的減速是否由於用戶端網路、應用程式閘道性能、後端網路和後端伺服器 TCP 堆疊飽和度、後端應用程式性能或大型檔案大小。

例如，如果*後端第一位元組回應時間*趨勢出現峰值，但*後端連線時間*趨勢穩定，則可以推斷應用程式閘道對後端延遲和建立連接所佔用的時間是穩定的，並且峰值是由於後端應用程式的回應時間增加造成的。 另一方面，如果*後端第一位元組回應時間*中的峰值與*後端連線時間的*相應峰值相關聯，則可以推斷應用程式閘道和後端伺服器之間的網路已飽和。 

如果您注意到*後端最後一個位元組回應時間*的峰值，但*後端第一位元組回應時間*穩定，則可以推斷出峰值是因為請求了更大的檔。

同樣，如果*應用程式閘道的總時間*有峰值，但*後端最後一個位元組回應時間*穩定，則它可以是應用程式閘道性能瓶頸的標誌，也可以是用戶端和應用程式閘道之間的網路中的瓶頸。 此外，如果*用戶端 RTT*也有相應的峰值，則表明降級是由於用戶端和應用程式閘道之間的網路。

### <a name="application-gateway-metrics"></a>應用程式閘道指標

應用程式閘道可使用下列計量：

- **收到的位元組數**

   應用程式閘道從用戶端接收的位元組數

- **發送的位元組數**

   應用程式閘道發送到用戶端的位元組數

- **用戶端 TLS 協定**

   由建立與應用程式閘道連接的用戶端啟動的 TLS 和非 TLS 請求的計數。 要查看 TLS 協定分佈，按維度 TLS 協定進行篩選。

- **當前容量單位**

   用於負載平衡流量的容量單位元數目。 容量單位有三個決定因素 - 計算單元、持久連接和輸送量。 每個容量單元最多由：1 個計算單元或 2500 個持久連接或 2.22-Mbps 輸送量組成。

- **當前計算單位**

   消耗的處理器容量計數。 影響計算單位的因素包括 TLS 連接/秒、URL 重寫計算和 WAF 規則處理。 

- **當前連接**

   從用戶端到應用程式閘道處於活動狀態的併發連接的總數
   
- **估計計費容量單位**

  使用 v2 SKU，定價模型由消耗驅動。 容量單位測量除固定成本外收取的基於消耗的成本。 *估計計費容量單位*指示用於計費的容量單位數。 計算此值時，*當前容量單位*（負載平衡流量所需的容量單位）和*固定計費容量單位*（保留預配的最小容量單位）之間的值越大。

- **失敗的請求**

  應用程式閘道已提供服務的失敗請求數。 可以進一步篩選請求計數，以顯示每個/特定後端池-HTTP 設置組合的計數。
   
- **固定計費容量單位**

  根據應用程式閘道配置中*的最低規模單位*設置（一個實例轉換為 10 個容量單位）保留的最小容量單位數。
   
 - **每秒新連接數**

   從用戶端到應用程式閘道以及從應用程式閘道到後端成員每秒建立的新 TCP 連接的平均數量。


- **回應狀態**

   應用程式閘道返回的 HTTP 回應狀態。 回應狀態碼發佈可以進一步分類，以顯示回應 2xx、3xx、4xx 和 5xx 分類中的回應。

- **輸送量**

   應用程式閘道每秒已服務的位元組數目

- **請求總數**

   應用程式閘道已服務的成功請求數。 可以進一步篩選請求計數，以顯示每個/特定後端池-HTTP 設置組合的計數。

### <a name="backend-metrics"></a>後端指標

應用程式閘道可使用下列計量：

- **後端回應狀態**

  後端返回的 HTTP 回應狀態碼計數。 這不包括應用程式閘道生成的任何回應代碼。 回應狀態碼發佈可以進一步分類，以顯示回應 2xx、3xx、4xx 和 5xx 分類中的回應。

- **狀況良好的主機計數**

  由運行狀況探測器確定健康的後端數。 您可以按每個後端池進行篩選，以顯示特定後端池中正常運行的主機數。

- **狀況不良的主機計數**

  運行狀況調查確定不正常的後端數。 您可以按每個後端池進行篩選，以顯示特定後端池中的不正常主機數。
  
- **每個健康主機每分鐘的請求**

  後端池中每個正常成員在一分鐘內收到的平均請求數。 您必須使用*後端池 HttpSettings*維度指定後端池。  
  

## <a name="metrics-supported-by-application-gateway-v1-sku"></a>應用程式閘道 V1 SKU 支援的指標

### <a name="application-gateway-metrics"></a>應用程式閘道指標

應用程式閘道可使用下列計量：

- **CPU 利用率**

  顯示分配給應用程式閘道的 CPU 的利用率。  在正常情況下，CPU 使用率不應經常超過 90%，因為這可能導致應用程式閘道後面的網站延遲並中斷用戶端體驗。 您可以通過通過增加實例計數或移動到較大的 SKU 大小或同時執行這兩種操作來間接控制或提高 CPU 利用率。

- **當前連接**

  目前與應用程式閘道建立的連線計數

- **失敗的請求**

  應用程式閘道已提供服務的失敗請求數。 可以進一步篩選請求計數，以顯示每個/特定後端池-HTTP 設置組合的計數。

- **回應狀態**

  應用程式閘道返回的 HTTP 回應狀態。 回應狀態碼發佈可以進一步分類，以顯示回應 2xx、3xx、4xx 和 5xx 分類中的回應。

- **輸送量**

  應用程式閘道每秒已服務的位元組數目

- **請求總數**

  應用程式閘道已服務的成功請求數。 可以進一步篩選請求計數，以顯示每個/特定後端池-HTTP 設置組合的計數。

- **Web 應用程式防火牆阻止的請求計數**
- **Web 應用程式防火牆阻止請求分發**
- **Web 應用程式防火牆總規則分發**

### <a name="backend-metrics"></a>後端指標

應用程式閘道可使用下列計量：

- **狀況良好的主機計數**

  由運行狀況探測器確定健康的後端數。 您可以按每個後端池進行篩選，以顯示特定後端池中正常運行的主機數。

- **狀況不良的主機計數**

  運行狀況調查確定不正常的後端數。 您可以按每個後端池進行篩選，以顯示特定後端池中的不正常主機數。

## <a name="metrics-visualization"></a>指標視覺化

流覽到應用程式閘道，在 **"監視**選擇**指標**"下。 若要檢視可用的值，請選取 [計量]**** 下拉式清單。

下圖中的範例顯示了最近 30 分鐘內的三項計量：

[![](media/application-gateway-diagnostics/figure5.png "Metric view")](media/application-gateway-diagnostics/figure5-lb.png#lightbox)

若要查看最新的度量清單，請參閱[支援 Azure Monitor 的計量](../azure-monitor/platform/metrics-supported.md)。

### <a name="alert-rules-on-metrics"></a>指標上的警報規則

您可以根據資源的計量來啟動警示規則。 例如，如果應用程式閘道的輸送量高於、低於或等於臨界值達到一段指定時間，警示便可以呼叫 Webhook 或寄送電子郵件給系統管理員。

下列範例會逐步引導您建立警示規則，以在輸送量達到臨界值之後傳送電子郵件給系統管理員：

1. 選擇 **"添加指標警報**"以打開 **"添加規則**"頁。 您還可以從指標頁到達此頁面。

   ![新增計量警示按鈕][6]

2. 在 **"添加規則"** 頁上，填寫名稱、條件和通知節，然後選擇 **"確定**"。

   * 在 [條件]**** 選取器中，選取這 4 個值之一：[大於]****、[大於或等於]****、[小於]**** 或 [小於或等於]****。

   * 在 [期間]**** 選取器中，選取 5 分鐘到 6 小時的期間。

   * 如果選取 [傳送電子郵件給擁有者、參與者和讀者]****，便可根據可存取該資源的使用者動態傳送電子郵件。 否則，您可以在 [其他系統管理員電子郵件]**** 方塊中提供以逗號分隔的使用者清單。

   ![添加規則頁][7]

如果達到臨界值，送達的電子郵件會類似下圖︰

![違反臨界值的電子郵件][8]

建立計量警示之後，就會出現警示的清單， 其中提供所有警示規則的概觀。

![警示和規則的清單][9]

若要深入了解警示通知，請參閱[接收警示通知](../monitoring-and-diagnostics/insights-receive-alert-notifications.md)。

要瞭解有關 Webhook 以及如何將其與警報一起使用它們的更多內容，請訪問[在 Azure 指標警報上配置 Webhook。](../azure-monitor/platform/alerts-webhooks.md)

## <a name="next-steps"></a>後續步驟

* 利用 [Azure 監視器記錄](../azure-monitor/insights/azure-networking-analytics.md)將計數器和事件記錄視覺化。
* [使用 Power BI 博客文章視覺化 Azure 活動日誌](https://blogs.msdn.com/b/powerbi/archive/2015/09/30/monitor-azure-audit-logs-with-power-bi.aspx)。
* [在 Power BI 和其他工具中檢視和分析 Azure 活動記錄](https://azure.microsoft.com/blog/analyze-azure-audit-logs-in-powerbi-more/)部落格文章。

[1]: ./media/application-gateway-diagnostics/figure1.png
[2]: ./media/application-gateway-diagnostics/figure2.png
[3]: ./media/application-gateway-diagnostics/figure3.png
[4]: ./media/application-gateway-diagnostics/figure4.png
[5]: ./media/application-gateway-diagnostics/figure5.png
[6]: ./media/application-gateway-diagnostics/figure6.png
[7]: ./media/application-gateway-diagnostics/figure7.png
[8]: ./media/application-gateway-diagnostics/figure8.png
[9]: ./media/application-gateway-diagnostics/figure9.png
[10]: ./media/application-gateway-diagnostics/figure10.png
