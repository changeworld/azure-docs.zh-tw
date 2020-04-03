---
title: 使用 Azure 監視器的媒體服務指標和診斷紀錄
titleSuffix: Azure Media Services
description: 瞭解如何通過 Azure 監視器監視 Azure 媒體服務指標和診斷日誌。
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/08/2019
ms.author: juliako
ms.openlocfilehash: 75363212684bb09e84a4bc4881af26e6d2a8e7e3
ms.sourcegitcommit: 3c318f6c2a46e0d062a725d88cc8eb2d3fa2f96a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/02/2020
ms.locfileid: "80585280"
---
# <a name="monitor-media-services-metrics-and-diagnostic-logs-via-azure-monitor"></a>透過 Azure 監視器監視媒體服務指標和診斷紀錄

[Azure 監視器](../../azure-monitor/overview.md)允許您監視指標和診斷日誌,説明您瞭解應用的性能。 Azure 監視器收集的所有數據都適合兩種基本類型之一:指標和日誌。 您可以監視媒體服務診斷日誌,並為收集的指標和日誌創建警報和通知。 您可以使用[指標資源管理員](../../azure-monitor/platform/metrics-getting-started.md)來可視化和分析指標資料。 您可以將日誌發送到[Azure 儲存](https://azure.microsoft.com/services/storage/),將它們流式傳輸到[Azure 事件中心](https://azure.microsoft.com/services/event-hubs/),將其匯出到[日誌分析](https://azure.microsoft.com/services/log-analytics/),或使用第三方服務。

有關詳細概述,請參閱[Azure 監視器指標](../../azure-monitor/platform/data-platform.md)和[Azure 監視器診斷日誌](../../azure-monitor/platform/platform-logs-overview.md)。

此主題討論受支援的[媒體服務指標](#media-services-metrics)[與媒體服務診斷紀錄](#media-services-diagnostic-logs)。

## <a name="media-services-metrics"></a>媒體服務指標

不論值是否變更，系統都會定期收集計量。 它們適合用於警示，因為它們可頻繁地進行取樣，而且可使用相對較簡單的邏輯快速引發警示。 有關如何建立指標警報的資訊,請參閱使用 Azure[監視器 建立、檢視和管理指標警報](../../azure-monitor/platform/alerts-metric.md)。

媒體服務支援以下資源的監視指標:

* 帳戶
* 串流端點

### <a name="account"></a>帳戶

您可以監視以下帳戶指標。

|度量名稱|顯示名稱|描述|
|---|---|---|
|資產計數|資產計數|您帳戶中的資產。|
|資產配額|資產配額|您帳戶中的資產配額。|
|資產配額使用百分比|資產配額使用百分比|已使用的資產配額的百分比。|
|內容關鍵原則計數|內容金鑰策略計數|帳戶中的內容關鍵策略。|
|內容關鍵原則配額|內容關鍵原則配額|帳戶中的內容關鍵策略配額。|
|內容關鍵策略配額使用百分比|內容 關鍵策略配額使用百分比|已使用的內容密鑰策略配額的百分比。|
|流式處理策略計數|串流原則計數|帳戶中的流式處理策略。|
|流式處理策略配額|流式處理策略配額|帳戶中的流式處理策略配額。|
|流式處理策略配額使用百分比|串流策略配額使用百分比|已使用的流式處理策略配額的百分比。|

您還應檢視[帳號配額和限制](limits-quotas-constraints.md)。

### <a name="streaming-endpoint"></a>串流端點

支援以下媒體服務[串流終結點](https://docs.microsoft.com/rest/api/media/streamingendpoints)指標:

|度量名稱|顯示名稱|描述|
|---|---|---|
|Requests|Requests|提供流式處理終結點提供的 HTTP 請求總數。|
|輸出|輸出|出口位元組的總數。 例如,流式處理終結點流式傳輸的位元組。|
|SuccessE2ELatency|成功端到端延遲|從流式處理終結點收到請求到發送回應的最後一個字節的持續時間。|

### <a name="why-would-i-want-to-use-metrics"></a>為什麼要使用指標?

以下是監視媒體服務指標如何説明您瞭解應用性能的範例。 媒體服務指標可以解決的一些問題包括:

* 如何監視標準流式處理終結點,瞭解我何時超出了限制?
* 如何知道我是否有足夠的高級流式處理終結點縮放單位?
* 如何設置警報,以便知道何時擴展流式處理終結點?
* 如何設置警報,以便知道何時到達了帳戶上配置的最大出口?
* 如何查看請求失敗的細目以及導致失敗的原因?
* 如何查看從打包程式提取的 HLS 或 DASH 請求數?
* 如何設置警報,以便知道故障請求的閾值何時命中?

### <a name="example"></a>範例

請參考[如何監視媒體服務指標](media-services-metrics-howto.md)。

## <a name="media-services-diagnostic-logs"></a>媒體服務診斷紀錄

診斷日誌提供有關 Azure 資源操作的豐富且頻繁的數據。 有關詳細資訊,請參閱[如何從 Azure 資源收集和使用紀錄資料](../../azure-monitor/platform/platform-logs-overview.md)。

媒體服務支援以下診斷日誌:

* 金鑰交付

### <a name="key-delivery"></a>金鑰交付

|名稱|描述|
|---|---|
|金鑰交付服務要求|顯示金鑰傳遞服務請求資訊的日誌。 有關詳細資訊,請參閱[架構](media-services-diagnostic-logs-schema.md)。|

### <a name="why-would-i-want-to-use-diagnostics-logs"></a>為什麼要使用診斷日誌?

您可以使用金鑰傳遞診斷紀錄檢查的一些事項包括:

* 請參閱 DRM 類型提供的許可證數。
* 查看策略交付的許可證數。
* 按 DRM 或策略類型查看錯誤。
* 查看來自用戶端的未授權許可證請求數。

### <a name="example"></a>範例

請參考[如何監視媒體服務診斷紀錄](media-services-diagnostic-logs-howto.md)。

## <a name="next-steps"></a>後續步驟

* [如何從 Azure 資源收集和使用紀錄資料](../../azure-monitor/platform/platform-logs-overview.md)
* [使用 Azure 監視器建立、檢視及管理計量警示](../../azure-monitor/platform/alerts-metric.md)
* [如何監視媒體服務指標](media-services-metrics-howto.md)
* [如何監視媒體服務診斷紀錄](media-services-diagnostic-logs-howto.md)
