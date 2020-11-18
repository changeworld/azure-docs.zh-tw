---
title: 使用 Azure 監視器的媒體服務計量和診斷記錄
titleSuffix: Azure Media Services
description: 瞭解如何透過 Azure 監視器監視 Azure 媒體服務計量和診斷記錄。
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 11/02/2020
ms.author: inhenkel
ms.openlocfilehash: ed436336b9b1f9f5815938d13315e821694e5f1c
ms.sourcegitcommit: c157b830430f9937a7fa7a3a6666dcb66caa338b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/17/2020
ms.locfileid: "94685166"
---
# <a name="monitor-media-services-metrics-and-diagnostic-logs-with-azure-monitor"></a>使用 Azure 監視器監視媒體服務計量和診斷記錄

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

[Azure 監視器](../../azure-monitor/overview.md) 可讓您監視計量和診斷記錄，以協助您瞭解應用程式的執行情況。 Azure 監視器所收集的所有資料均符合下列兩個基本類型之一：計量和記錄。 您可以監視媒體服務診斷記錄，並為收集的計量和記錄建立警示和通知。 您可以使用 [計量瀏覽器](../../azure-monitor/platform/metrics-getting-started.md)來視覺化及分析計量資料。 您可以將記錄傳送至 [Azure 儲存體](https://azure.microsoft.com/services/storage/)、將記錄串流至 [Azure 事件中樞](https://azure.microsoft.com/services/event-hubs/)、將記錄匯出至 [Log Analytics](https://azure.microsoft.com/services/log-analytics/)，或使用協力廠商服務。

如需詳細的總覽，請參閱 [Azure 監視器計量](../../azure-monitor/platform/data-platform.md) 和 [Azure 監視器診斷記錄](../../azure-monitor/platform/platform-logs-overview.md)。

本主題討論支援的 [媒體服務計量](#media-services-metrics) 和 [媒體服務診斷記錄](#media-services-diagnostic-logs)。

## <a name="media-services-metrics"></a>媒體服務計量

不論值是否變更，系統都會定期收集計量。 它們適合用於警示，因為它們可頻繁地進行取樣，而且可使用相對較簡單的邏輯快速引發警示。 如需有關如何建立計量警示的詳細資訊，請參閱 [使用 Azure 監視器建立、查看和管理計量警示](../../azure-monitor/platform/alerts-metric.md)。

媒體服務支援監視下列資源的計量：

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
|ContentKeyPolicyQuota|內容金鑰原則配額|您帳戶中的內容金鑰原則配額。|
|ContentKeyPolicyQuotaUsedPercentage|已使用的內容金鑰原則配額百分比|已使用的內容金鑰原則配額百分比。|
|StreamingPolicyCount|串流原則計數|您帳戶中的串流原則。|
|StreamingPolicyQuota|串流原則配額|您帳戶中的串流原則配額。|
|StreamingPolicyQuotaUsedPercentage|已使用的串流原則配額百分比|已使用的串流原則配額百分比。|

您也應該檢查 [帳戶配額和限制](limits-quotas-constraints.md)。

### <a name="streaming-endpoint"></a>串流端點

以下是支援的媒體服務 [串流端點](/rest/api/media/streamingendpoints) 計量：

|度量名稱|顯示名稱|描述|
|---|---|---|
|Requests|Requests|提供串流端點所服務的 HTTP 要求總數。|
|輸出|輸出|每分鐘的輸出位元組總計（每個串流端點）。|
|SuccessE2ELatency|成功端對端延遲|當傳送回應的最後一個位元組時，從串流端點收到要求時的持續時間。|
|CPU 使用量| | Premium 串流端點的 CPU 使用量。 標準串流端點無法使用此資料。 |
|輸出頻寬 | | 每秒位數的輸出頻寬。|

### <a name="metrics-are-useful"></a>計量很有用

以下範例說明監視媒體服務計量如何協助您瞭解應用程式的執行情況。 有一些可透過媒體服務計量解決的問題如下：

* 如何? 監視我的標準串流端點，以瞭解何時超過限制？
* 如何? 知道我有足夠的 Premium 串流端點縮放單位嗎？
* 如何設定警示，以瞭解何時要擴大串流端點？
* 如何? 設定警示，以瞭解何時達到帳戶上設定的最大輸出？
* 如何查看要求失敗的明細以及造成失敗的原因？
* 如何查看正在從封裝程式提取多少 HLS 或虛線要求？
* 如何? 設定警示以得知何時遇到失敗要求數目的閾值？

平行存取會成為單一帳戶一段時間內所使用的串流端點數目的考慮。 您必須記住並行資料流程的數目與複雜發行參數（例如將動態封裝到多個通訊協定、多重 DRM 加密等）之間的關聯性。每個額外發佈的即時串流都會新增至串流端點上的 CPU 和輸出頻寬。 考慮到這一點，您應該使用 Azure 監視器仔細監看串流端點的使用率 (CPU 和輸出容量) 以確定您要適當地調整它 (或在多個串流端點之間分割流量，如果您進入非常高的平行存取) 。

### <a name="example"></a>範例

請參閱 [如何監視媒體服務計量](media-services-metrics-howto.md)。

## <a name="media-services-diagnostic-logs"></a>媒體服務診斷記錄

診斷記錄可提供 Azure 資源作業的豐富且頻繁的資料。 如需詳細資訊，請參閱 [如何收集和取用來自 Azure 資源的記錄資料](../../azure-monitor/platform/platform-logs-overview.md)。

媒體服務支援下列診斷記錄：

* 金鑰傳遞

### <a name="key-delivery"></a>金鑰傳遞

|名稱|描述|
|---|---|
|金鑰傳遞服務要求|顯示金鑰傳遞服務要求資訊的記錄。 如需詳細資訊，請參閱 [架構](media-services-diagnostic-logs-schema.md)。|

### <a name="why-would-i-want-to-use-diagnostics-logs"></a>為什麼要使用診斷記錄？

您可以使用金鑰傳遞診斷記錄來檢查的一些事項如下：

* 查看 DRM 類型所傳遞的授權數目。
* 查看原則所傳遞的授權數目。
* 查看 DRM 或原則類型的錯誤。
* 查看來自用戶端的未經授權授權數目。

### <a name="example"></a>範例

請參閱 [如何監視媒體服務診斷記錄](media-services-diagnostic-logs-howto.md)。

## <a name="next-steps"></a>後續步驟

* [如何收集和取用來自 Azure 資源的記錄資料](../../azure-monitor/platform/platform-logs-overview.md)
* [使用 Azure 監視器建立、檢視及管理計量警示](../../azure-monitor/platform/alerts-metric.md)
* [如何監視媒體服務計量](media-services-metrics-howto.md)
* [如何監視媒體服務診斷記錄](media-services-diagnostic-logs-howto.md)
