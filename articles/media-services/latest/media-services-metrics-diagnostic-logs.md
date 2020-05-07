---
title: 使用 Azure 監視器媒體服務計量和診斷記錄
titleSuffix: Azure Media Services
description: 瞭解如何透過 Azure 監視器監視 Azure 媒體服務計量和診斷記錄。
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
ms.openlocfilehash: f051d02ac06163cf1861ab6bfc6afd0dcbb0ad8a
ms.sourcegitcommit: 856db17a4209927812bcbf30a66b14ee7c1ac777
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/29/2020
ms.locfileid: "82559537"
---
# <a name="monitor-media-services-metrics-and-diagnostic-logs-via-azure-monitor"></a>透過 Azure 監視器監視媒體服務計量和診斷記錄

[Azure 監視器](../../azure-monitor/overview.md)可讓您監視計量和診斷記錄，以協助您瞭解應用程式的執行狀況。 Azure 監視器所收集的所有資料都符合下列兩種基本類型的其中一種：計量和記錄。 您可以監視媒體服務診斷記錄，並為所收集的計量和記錄建立警示和通知。 您可以使用[計量瀏覽器](../../azure-monitor/platform/metrics-getting-started.md)將計量資料視覺化並加以分析。 您可以將記錄傳送至[Azure 儲存體](https://azure.microsoft.com/services/storage/)、將它們串流至[Azure 事件中樞](https://azure.microsoft.com/services/event-hubs/)、將其匯出至[Log Analytics](https://azure.microsoft.com/services/log-analytics/)，或使用協力廠商服務。

如需詳細的總覽，請參閱[Azure 監視器計量](../../azure-monitor/platform/data-platform.md)和[Azure 監視器診斷記錄](../../azure-monitor/platform/platform-logs-overview.md)。

本主題討論支援的[媒體服務計量](#media-services-metrics)和[媒體服務診斷記錄](#media-services-diagnostic-logs)。

## <a name="media-services-metrics"></a>媒體服務計量

不論值是否變更，系統都會定期收集計量。 它們適合用於警示，因為它們可頻繁地進行取樣，而且可使用相對較簡單的邏輯快速引發警示。 如需有關如何建立計量警示的詳細資訊，請參閱[使用 Azure 監視器建立、查看和管理計量警示](../../azure-monitor/platform/alerts-metric.md)。

媒體服務支援下列資源的監視計量：

* 帳戶
* 串流端點

### <a name="account"></a>帳戶

您可以監視下列帳戶計量。

|度量名稱|顯示名稱|描述|
|---|---|---|
|AssetCount|資產計數|您帳戶中的資產。|
|AssetQuota|資產配額|帳戶中的資產配額。|
|AssetQuotaUsedPercentage|已使用的資產配額百分比|已使用的資產配額百分比。|
|ContentKeyPolicyCount|內容金鑰原則計數|您帳戶中的內容金鑰原則。|
|ContentKeyPolicyQuota|內容金鑰原則配額|帳戶中的內容金鑰原則配額。|
|ContentKeyPolicyQuotaUsedPercentage|已使用的內容金鑰原則配額百分比|已使用的內容金鑰原則配額百分比。|
|StreamingPolicyCount|串流原則計數|您帳戶中的串流原則。|
|StreamingPolicyQuota|串流原則配額|您帳戶中的串流原則配額。|
|StreamingPolicyQuotaUsedPercentage|已使用的串流原則配額百分比|已使用的串流原則配額百分比。|

您也應該檢查[帳戶配額和限制](limits-quotas-constraints.md)。

### <a name="streaming-endpoint"></a>串流端點

支援下列媒體服務[串流端點](https://docs.microsoft.com/rest/api/media/streamingendpoints)計量：

|度量名稱|顯示名稱|描述|
|---|---|---|
|Requests|Requests|提供串流端點所服務的 HTTP 要求總數。|
|輸出|輸出|每個串流端點的輸出位元組總計（每分鐘）。|
|SuccessE2ELatency|成功的端對端延遲|當送出回應的最後一個位元組時，從串流端點收到要求的持續時間。|

### <a name="why-would-i-want-to-use-metrics"></a>為什麼要使用計量？

以下範例說明監視媒體服務計量如何協助您瞭解應用程式的執行狀況。 媒體服務計量可以解決的一些問題如下：

* 如何? 監視我的標準串流端點，以知道我何時已超過限制？
* 如何? 知道我是否有足夠的 Premium 串流端點縮放單位？
* 如何設定警示以知道何時相應增加串流端點？
* 如何? 設定警示，以知道何時達到在帳戶上設定的輸出上限？
* 如何查看要求失敗的細目，以及造成失敗的原因為何？
* 如何查看從封裝程式提取的 HLS 或破折號要求數目？
* 如何? 設定警示，以知道何時會叫用失敗的要求數目的臨界值？

### <a name="example"></a>範例

請參閱[如何監視媒體服務計量](media-services-metrics-howto.md)。

## <a name="media-services-diagnostic-logs"></a>媒體服務診斷記錄

診斷記錄可提供有關 Azure 資源作業的豐富且經常性資料。 如需詳細資訊，請參閱[如何收集並取用來自 Azure 資源的記錄資料](../../azure-monitor/platform/platform-logs-overview.md)。

媒體服務支援下列診斷記錄：

* 金鑰傳遞

### <a name="key-delivery"></a>金鑰傳遞

|名稱|描述|
|---|---|
|金鑰傳遞服務要求|顯示金鑰傳遞服務要求資訊的記錄檔。 如需詳細資訊，請參閱[架構](media-services-diagnostic-logs-schema.md)。|

### <a name="why-would-i-want-to-use-diagnostics-logs"></a>為什麼要使用診斷記錄？

您可以使用金鑰傳遞診斷記錄來檢查的一些事項如下：

* 查看 DRM 類型所傳遞的授權數目。
* 查看原則提供的授權數目。
* 查看 DRM 或原則類型的錯誤。
* 查看用戶端未經授權的授權要求數目。

### <a name="example"></a>範例

請參閱[如何監視媒體服務診斷記錄](media-services-diagnostic-logs-howto.md)。

## <a name="next-steps"></a>後續步驟

* [如何收集並取用來自 Azure 資源的記錄資料](../../azure-monitor/platform/platform-logs-overview.md)
* [使用 Azure 監視器建立、檢視及管理計量警示](../../azure-monitor/platform/alerts-metric.md)
* [如何監視媒體服務計量](media-services-metrics-howto.md)
* [如何監視媒體服務診斷記錄](media-services-diagnostic-logs-howto.md)
