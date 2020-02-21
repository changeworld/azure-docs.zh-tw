---
title: 應用程式閘道的 Azure 監視器度量
description: 瞭解如何使用計量來監視應用程式閘道的效能
services: application-gateway
author: abshamsft
ms.service: application-gateway
ms.topic: article
ms.date: 2/5/2019
ms.author: absha
ms.openlocfilehash: 2d1e6e484fd704669951bd37b17356fd3689cc91
ms.sourcegitcommit: 98a5a6765da081e7f294d3cb19c1357d10ca333f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/20/2020
ms.locfileid: "77485177"
---
# <a name="metrics-for-application-gateway"></a>應用程式閘道的計量

應用程式閘道會發佈稱為「計量」的資料點，以[Azure 監視器](https://docs.microsoft.com/azure/azure-monitor/overview)應用程式閘道和後端實例的效能。 這些計量是一組已排序的時間序列資料中的數值，可在特定時間描述應用程式閘道的某些層面。 如果有要求流經應用程式閘道，它會以 60-秒的間隔測量並傳送其計量。 如果沒有任何要求流經應用程式閘道或沒有計量的資料，則不會報告計量。 如需詳細資訊，請參閱 [Azure 監視器計量](https://docs.microsoft.com/azure/azure-monitor/platform/data-platform-metrics)。

## <a name="metrics-supported-by-application-gateway-v2-sku"></a>應用程式閘道 V2 SKU 支援的計量

### <a name="timing-metrics"></a>計時計量

應用程式閘道提供數個與要求和回應相關的內建計時計量，全都以毫秒為單位來測量。 

![](./media/application-gateway-metrics/application-gateway-metrics.png)

> [!NOTE]
>
> 如果應用程式閘道中有多個接聽程式，則在比較不同的延遲標準時，一律會依接聽項*維度進行篩選*，以便取得有意義的推斷。

- **後端連線時間**

  建立與後端應用程式之連接所花費的時間。 

  這包括網路延遲，以及後端伺服器 TCP 堆疊建立新連線所花費的時間。 如果是 SSL，它也會包含在交握上所花費的時間。 

- **後端第一個位元組回應時間**

  開始建立與後端伺服器之間的連接，以及接收回應標頭的第一個位元組之間的時間間隔。 

  這近似于*後端連線時間*的總和、從應用程式閘道到達後端的要求所花費的時間、後端應用程式回應所花費的時間（伺服器產生內容的時間、可能提取資料庫查詢），以及從後端到達應用程式閘道的回應第一個位元組所花費的時間。

- **後端上次位元組回應時間**

  開始建立與後端伺服器之間的連線，以及接收回應主體最後一個位元組之間的時間間隔。 

  這接近*後端第一個位元組回應時間*和資料傳輸時間的總和（根據所要求的物件大小和伺服器網路的延遲而定，此數目可能會有極大的差異）。

- **應用程式閘道總時間**

  接收、處理要求以及傳送其回應所花費的平均時間。 

  這是從應用程式閘道接收 HTTP 要求的第一個位元組到最後一個回應位元組傳送到用戶端的時間間隔。 這包括應用程式閘道所花費的處理時間、*後端的最後位元組回應時間*，應用程式閘道傳送所有回應和*用戶端 RTT*所花費的時間。

- **用戶端 RTT**

  用戶端與應用程式閘道之間的平均來回行程時間。



這些計量可用來判斷觀察到的緩慢是否因用戶端網路、應用程式閘道效能、後端網路和後端伺服器 TCP 堆疊飽和度、後端應用程式效能或大型檔案大小所致。

例如，如果*後端第一個位元組回應時間*趨勢突然增加，但*後端連線時間*趨勢穩定，則可以推斷應用程式閘道對後端延遲和建立連線所需的時間是穩定的，而尖峰是由於後端應用程式的回應時間增加所造成。 另一方面，如果*後端第一個位元組回應時間*的尖峰與*後端連線時間*的對應尖峰相關聯，則可以推算出應用程式閘道與後端伺服器之間的網路，或是後端伺服器 TCP 堆疊的飽和。 

如果您注意到*後端的最後一個位元組回應時間*突然增加，但*後端第一個位元組回應時間*穩定，則可以推算出尖峰原因是要求的檔案較大。

同樣地，如果*應用程式閘道時間總計*有尖峰，但*後端最後位元組回應時間*穩定，則可以是應用程式閘道的效能瓶頸，或用戶端與應用程式閘道之間網路的瓶頸。 此外，如果*用戶端 RTT*也有相對應的尖峰，則表示效能降低的原因是用戶端與應用程式閘道之間的網路。

### <a name="application-gateway-metrics"></a>應用程式閘道計量

應用程式閘道可使用下列計量：

- **已接收位元組**

   應用程式閘道從用戶端接收的位元組計數

- **已傳送位元組**

   應用程式閘道傳送給用戶端的位元組計數

- **用戶端 TLS 通訊協定**

   與應用程式閘道建立連線的用戶端所起始的 TLS 和非 TLS 要求計數。 若要查看 TLS 通訊協定散發，請依維度 TLS 通訊協定進行篩選。

- **目前的容量單位**

   負載平衡流量所耗用的容量單位元數目。 容量單位有三個 determinants：計算單位、持續連線和輸送量。 每個容量單位最多包含：1個計算單位，或2500持續連線，或 2.22-Mbps 輸送量。

- **目前的計算單位**

   耗用的處理器容量計數。 影響計算單位的因素包括 TLS 連線數/秒、URL 重寫計算，以及 WAF 規則處理。 

- **目前的連接**

   從用戶端到應用程式閘道的並行連線數總計
   
- **預估的計費容量單位**

  使用 v2 SKU，計價模式是以耗用量來驅動。 容量單位會測量以耗用量為基礎的成本，除了固定成本之外，也會收取費用。 *預估的計費容量單位*表示用來預估計費的容量單位數。 這會計算為*目前容量單位*之間的較大值（負載平衡流量所需的容量單位）和*固定計費容量單位*（已布建的最小容量單位）。

- **失敗的要求**

  應用程式閘道已提供的失敗要求計數。 您可以進一步篩選要求計數，以顯示每個/特定後端集區的計數-HTTP 設定組合。
   
- **固定計費容量單位**

  在應用程式閘道設定中，根據*最小縮放單位*設定（一個實例轉譯為10個容量單位）保留布建的最小容量單位數。
   
 - **每秒的新連線數**

   從用戶端到應用程式閘道以及從應用程式閘道到後端成員所建立的每秒新 TCP 連線數平均。


- **回應狀態**

   應用程式閘道傳回的 HTTP 回應狀態。 回應狀態碼發佈可以進一步分類，以顯示回應 2xx、3xx、4xx 和 5xx 分類中的回應。

- **輸送量**

   應用程式閘道每秒已服務的位元組數目

- **要求總數**

   應用程式閘道已提供的成功要求計數。 您可以進一步篩選要求計數，以顯示每個/特定後端集區的計數-HTTP 設定組合。

- **Web 應用程式防火牆相符的規則**

- **Web 應用程式防火牆觸發的規則**

### <a name="backend-metrics"></a>後端計量

應用程式閘道可使用下列計量：

- **後端回應狀態**

  後端所傳回的 HTTP 回應狀態碼計數。 這不包含任何由應用程式閘道產生的回應碼。 回應狀態碼發佈可以進一步分類，以顯示回應 2xx、3xx、4xx 和 5xx 分類中的回應。

- **狀況良好的主機計數**

  健康情況探查判斷為狀況良好的後端數目。 您可以依據每個後端集區進行篩選，以顯示特定後端集區中狀況良好的主機數目。

- **狀況不良的主機計數**

  健康情況探查所判定為狀況不良的後端數目。 您可以根據每個後端集區進行篩選，以顯示特定後端集區中狀況不良的主機數目。
  
- **每部狀況良好主機的每分鐘要求數**

  後端集區中每個狀況良好的成員所收到的平均要求數（以分鐘為單位）。 您必須使用*BackendPool HTTPsettings)* 維度來指定後端集區。  
  

## <a name="metrics-supported-by-application-gateway-v1-sku"></a>應用程式閘道 V1 SKU 支援的計量

### <a name="application-gateway-metrics"></a>應用程式閘道計量

應用程式閘道可使用下列計量：

- **CPU 使用率**

  顯示配置給應用程式閘道的 Cpu 使用率。  在正常情況下，CPU 使用量不應定期超過90%，因為這可能會導致在應用程式閘道背後的網站中發生延遲，並中斷用戶端體驗。 您可以藉由增加實例計數或移至較大的 SKU 大小，或同時執行這兩種方式來修改應用程式閘道的設定，以間接控制或改善 CPU 使用率。

- **目前的連接**

  目前與應用程式閘道建立的連線計數

- **失敗的要求**

  應用程式閘道已提供的失敗要求計數。 您可以進一步篩選要求計數，以顯示每個/特定後端集區的計數-HTTP 設定組合。

- **回應狀態**

  應用程式閘道傳回的 HTTP 回應狀態。 回應狀態碼發佈可以進一步分類，以顯示回應 2xx、3xx、4xx 和 5xx 分類中的回應。

- **輸送量**

  應用程式閘道每秒已服務的位元組數目

- **要求總數**

  應用程式閘道已提供的成功要求計數。 您可以進一步篩選要求計數，以顯示每個/特定後端集區的計數-HTTP 設定組合。

- **Web 應用程式防火牆相符的規則**

- **Web 應用程式防火牆觸發的規則**

### <a name="backend-metrics"></a>後端計量

應用程式閘道可使用下列計量：

- **狀況良好的主機計數**

  健康情況探查判斷為狀況良好的後端數目。 您可以依據每個後端集區進行篩選，以顯示特定後端集區中狀況良好的主機數目。

- **狀況不良的主機計數**

  健康情況探查所判定為狀況不良的後端數目。 您可以根據每個後端集區進行篩選，以顯示特定後端集區中狀況不良的主機數目。

## <a name="metrics-visualization"></a>度量視覺效果

流覽至 [**監視**] [選取**計量**] 底下的應用程式閘道。 若要檢視可用的值，請選取 [計量] 下拉式清單。

下圖中的範例顯示了最近 30 分鐘內的三項計量：

[![](media/application-gateway-diagnostics/figure5.png "Metric view")](media/application-gateway-diagnostics/figure5-lb.png#lightbox)

若要查看最新的度量清單，請參閱[支援 Azure Monitor 的計量](../azure-monitor/platform/metrics-supported.md)。

### <a name="alert-rules-on-metrics"></a>計量的警示規則

您可以根據資源的計量來啟動警示規則。 例如，如果應用程式閘道的輸送量高於、低於或等於臨界值達到一段指定時間，警示便可以呼叫 Webhook 或寄送電子郵件給系統管理員。

下列範例會逐步引導您建立警示規則，以在輸送量達到臨界值之後傳送電子郵件給系統管理員：

1. 選取 [**新增度量警示**] 以開啟 [**新增規則**] 頁面。 您也可以從 [計量] 頁面連接此頁面。

   ![新增計量警示按鈕][6]

2. 在 [**新增規則**] 頁面上，填寫 [名稱]、[條件] 和 [通知] 區段，然後選取 **[確定]** 。

   * 在 [條件] 選取器中，選取這 4 個值之一：[大於]、[大於或等於]、[小於] 或 [小於或等於]。

   * 在 [期間] 選取器中，選取 5 分鐘到 6 小時的期間。

   * 如果選取 [傳送電子郵件給擁有者、參與者和讀者]，便可根據可存取該資源的使用者動態傳送電子郵件。 否則，您可以在 [其他系統管理員電子郵件] 方塊中提供以逗號分隔的使用者清單。

   ![[新增規則] 頁面][7]

如果達到臨界值，送達的電子郵件會類似下圖︰

![違反臨界值的電子郵件][8]

建立計量警示之後，就會出現警示的清單， 其中提供所有警示規則的概觀。

![警示和規則的清單][9]

若要深入了解警示通知，請參閱[接收警示通知](../monitoring-and-diagnostics/insights-receive-alert-notifications.md)。

若要深入了解 Webhook 以及其如何與警示搭配使用，請造訪[針對 Azure 計量警示設定 Webhook](../azure-monitor/platform/alerts-webhooks.md)。

## <a name="next-steps"></a>後續步驟

* 利用 [Azure 監視器記錄](../azure-monitor/insights/azure-networking-analytics.md)將計數器和事件記錄視覺化。
* [使用 Power BI 將您的 Azure 活動記錄視覺化](https://blogs.msdn.com/b/powerbi/archive/2015/09/30/monitor-azure-audit-logs-with-power-bi.aspx)部落格文章。
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
