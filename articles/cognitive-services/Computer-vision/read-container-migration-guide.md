---
title: 遷移至 Read v3. x OCR 容器
titleSuffix: Azure Cognitive Services
description: 了解如何遷移至 v3 Read OCR 容器
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: overview
ms.date: 09/21/2020
ms.author: aahi
ms.openlocfilehash: 856e73181ee02fe2bb21c4317ec8c733e2536d53
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/13/2020
ms.locfileid: "91973116"
---
# <a name="migrate-to-the-read-v3x-ocr-containers"></a>遷移至 Read v3. x OCR 容器

如果您使用第 2 版的電腦視覺 Read OCR 容器，請使用本文來了解如何將您的應用程式升級為使用 3.x 版的容器。 


## <a name="configuration-changes"></a>組態變更

* `ReadEngineConfig:ResultExpirationPeriod` 不再受支援。 Read 容器具有建立的 Cron 作業，會在 48 小時後移除與要求相關聯的結果和中繼資料。
* `Cache:Redis:Configuration` 不再受支援。 此快取不會用於 v3.x 容器中，因此您不需要加以設定。

## <a name="api-changes"></a>API 變更

Read v3.x 容器會使用第 3 版的電腦視覺 API，並具有下列端點：

#### <a name="version-31-preview"></a>[3.1-preview 版](#tab/version-3-1)

* `/vision/v3.1-preview.2/read/analyzeResults/{operationId}`
* `/vision/v3.1-preview.2/read/analyze`
* `/vision/v3.1-preview.2/read/syncAnalyze`

#### <a name="version-30-preview"></a>[3.0-preview 版](#tab/version-3)

* `/vision/v3.0/read/analyzeResults/{operationId}`
* `/vision/v3.0/read/analyze`
* `/vision/v3.0/read/syncAnalyze`

---

如需將應用程式更新為使用第 3 版雲端式讀取 API 的詳細資訊，請參閱[電腦視覺 v3 REST API 移轉指南](https://docs.microsoft.com/azure/cognitive-services/computer-vision/upgrade-api-versions)。 此資訊也適用於容器。 請注意，僅在容器中支援同步作業。

## <a name="memory-requirements"></a>記憶體需求

需求和建議是以每秒單一要求的基準測試為基礎，使用 8 MB 影像的掃描商務信件，其中包含 29 行及總計 803 個字元。 下表描述每個 Read 容器的資源最低和建議配置。

|容器  |最小值 | 建議  |
|---------|---------|------|
|Read 3.0-預覽     | 8 核心，16-GB 記憶體         | 8 核心，24-GB 記憶體
|Read 3.1-預覽 | 8 核心，16-GB 記憶體         | 8 核心，24-GB 記憶體

每個核心必須至少 2.6 GHz 或更快。

核心和記憶體會對應至 `--cpus` 和 `--memory` 設定，用來做為 docker run 命令的一部分。

## <a name="storage-implementations"></a>儲存體實作

>[!NOTE]
> 在 3.x 版的容器中，已不再支援 MongoDB。 相反地，容器支援 Azure 儲存體和離線檔案系統。

| 實作 |    必要的執行時間引數 |
|---------|---------|
|檔案層級 (預設)    | 不需要任何執行階段引數。 將使用 `/share` 目錄。 |
|Azure Blob    | `Storage:ObjectStore:AzureBlob:ConnectionString={AzureStorageConnectionString}` |

## <a name="queue-implementations"></a>佇列實作

在 v3.x 的容器中，目前不支援 RabbitMQ。 支援的支援實作為：

| 實作 | 執行階段引數 | 預定用途 |
|---------|---------|-------|
| 在記憶體中 (預設) | 不需要任何執行階段引數。 | 開發與測試 |
| Azure 佇列 | `Queue:Azure:ConnectionString={AzureStorageConnectionString}` | 生產 |
| RabbitMQ    | [無法使用] | 生產 |

針對新增的備援，Read v3.x 容器會使用可見度計時器，以確保在多容器設定中執行期間發生損毀時成功處理要求。 

將計時器設定為 [`Queue:Azure:QueueVisibilityTimeoutInMilliseconds`]，這會設定當另一個背景工作角色正在處理訊息時，不會顯示訊息的時間。 若要避免頁面重複處理，建議您將逾時期間設定為 120 秒。 預設值為 30 秒。

| 預設值 | 建議值 |
|---------|---------|
| 30000 |    120000 |


## <a name="next-steps"></a>後續步驟

* 檢閱[設定容器](computer-vision-resource-container-config.md)以了解組態設定
* 檢閱[電腦視覺概觀](overview.md)，以深入了解辨識印刷和手寫的文字
* 參閱[電腦視覺 API](//westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa)，以取得容器支援的方法之詳細資訊。
* 參閱[常見問題集 (FAQ)](FAQ.md) 來解決與電腦視覺功能相關的問題。
* 使用更多[認知服務容器](../cognitive-services-container-support.md)
