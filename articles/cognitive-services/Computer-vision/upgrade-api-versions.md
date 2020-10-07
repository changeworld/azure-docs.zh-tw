---
title: 升級至電腦視覺 API 的 Read v3.0
titleSuffix: Azure Cognitive Services
description: 了解如何從 v2.0/v2.1 升級至電腦視覺 v3.0 Read API。
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: sample
ms.date: 08/11/2020
ms.author: pafarley
ROBOTS: NOINDEX
ms.openlocfilehash: 5910c40729d07d5a759b2e5cc7b7a4272524c150
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/25/2020
ms.locfileid: "91253848"
---
# <a name="upgrade-from-read-v2x-to-read-v3x"></a>從 Read v2.x 升級至 Read v3.x

本指南說明如何將您現有的容器或雲端 API 程式碼從 Read v2.x 升級至 Read v3.0 和 v3.1 預覽。

## <a name="determine-your-api-path"></a>確認您的 API 路徑
使用下表根據您要遷移至的 Read 3.x 版確認 API 路徑中的**版本字串**。

|產品類型| 版本 | 3\.x API 路徑中的版本字串 |
|:-----|:----|:----|
|服務 | 讀取 3.0 | **v3.0** |
|容器 | Read 3.0 預覽 | **v3.0** |
|服務/容器 | Read 3.1 預覽 | **v3.1-preview.2** |

接著，使用以下各節縮小作業範圍，並將 API 路徑中的**版本字串**取代為表格中的值。 例如，對於 **Read v3.1 預覽**的雲端和容器版本，請將 API 路徑更新為 **https://{endpoint}/vision/v3.1-preview.2/read/analyze[?language]** 。

## <a name="servicecontainer"></a>服務/容器

### `Batch Read File`

|讀取 2.x |Read 3.x  |
|----------|-----------|
|https://{endpoint}/vision/**v2.0/read/core/asyncBatchAnalyze**     |https://{endpoint}/vision/<**version string**>/read/analyze[?language]|
    
有新的選擇性 _language_ 參數可供使用。 如果您不知道文件的語言，或者其可能是多語系，請勿包含此參數。 

### `Get Read Results`

|讀取 2.x |Read 3.x  |
|----------|-----------|
|https://{endpoint}/vision/**v2.0/read/operations**/{operationId}     |https://{endpoint}/vision/<**version string**>/read/analyzeResults/{operationId}|

### <a name="get-read-operation-result-status-flag"></a>`Get Read Operation Result` 狀態旗標

當 `Get Read Operation Result` 的呼叫成功時，其會在 JSON 主體中傳回狀態字串欄位。
 
|讀取 2.x |Read 3.x  |
|----------|-----------|
|`"NotStarted"` |   `"notStarted"`|
|`"Running"` | `"running"`|
|`"Failed"` | `"failed"`|
|`"Succeeded"` | `"succeeded"`|
    
### <a name="api-response-json"></a>API 回應 (JSON) 

記下 JSON 的下列變更：
* 在 v2.x 中，`Get Read Operation Result` 會在狀態為 `Succeeded"` 時傳回 OCR 辨識 JSON。 在 v3.0 中，此欄位為 `succeeded`。
* 若要取得頁面陣列的根，請將 JSON 階層從 `recognitionResults` 變更為 `analyzeResult`/`readResults`。 每頁的行和字組 JSON 階層會維持不變，因此不需要變更程式碼。
* 頁面角度 `clockwiseOrientation` 已經重新命名為 `angle`，而範圍已從 0-360 度變更為 -180 到 180 度。 視您的程式碼而定，您不一定需要進行變更，因為大部分的數學函式都可以處理任一範圍。

V3.0 API 也導入了您可以選擇運用的下列改進：
* 已新增 `createdDateTime` 和 `lastUpdatedDateTime`，讓您可以追蹤處理的持續時間。 如需詳細資訊，請參閱文件。 
* `version` 會告訴您用來產生結果的 API 版本
* 已新增每個字組的 `confidence`。 此值已校正，因此，值 0.95 表示辨識正確的機會有 95%。 信賴分數可以用來選取要傳送給人工審核的文字。 
    
    
在 2.x 中，輸出格式如下所示： 
    
```json
    {
        {
                "status": "Succeeded",
                "recognitionResults": [
                    {
                    "page": 1,
                    "language": "en",
                    "clockwiseOrientation": 349.59,
                    "width": 2661,
                    "height": 1901,
                    "unit": "pixel",
                    "lines": [
                        {
                        "boundingBox": [
                            67,
                            646,
                            2582,
                            713,
                            2580,
                            876,
                            67,
                            821
                        ],
                        "text": "The quick brown fox jumps",
                        "words": [
                            {
                            "boundingBox": [
                                143,
                                650,
                                435,
                                661,
                                436,
                                823,
                                144,
                                824
                            ],
                            "text": "The",
                            },
            // The rest of result is omitted for brevity 
            
}
```
    
在 v3.0 中，其已經過調整：
    
```json
    {
        {
            "status": "succeeded",
            "createdDateTime": "2020-05-28T05:13:21Z",
            "lastUpdatedDateTime": "2020-05-28T05:13:22Z",
            "analyzeResult": {
            "version": "3.0.0",
            "readResults": [
                {
                "page": 1,
                "language": "en",
                "angle": 0.8551,
                "width": 2661,
                "height": 1901,
                "unit": "pixel",
                "lines": [
                    {
                    "boundingBox": [
                        67,
                        646,
                        2582,
                        713,
                        2580,
                        876,
                        67,
                        821
                    ],
                    "text": "The quick brown fox jumps",
                    "words": [
                        {
                        "boundingBox": [
                            143,
                            650,
                            435,
                            661,
                            436,
                            823,
                            144,
                            824
                        ],
                        "text": "The",
                        "confidence": 0.958
                        },
        // The rest of result is omitted for brevity 
        
    }
```

## <a name="service-only"></a>僅限服務

### `Recognize Text`
`Recognize Text` 是「預覽」作業，將從所有版本的電腦視覺 API 中淘汰。 您必須從 `Recognize Text` 遷移至 `Read` (v3.0) 或 `Batch Read File` (v2.0、2.1)。 `Read` v3.0 包含較新且更佳的文字辨識模型和其他功能，因此建議使用此版本。 從 `Recognize Text` 升級至 `Read`：

|辨識文字 2.x |Read 3.x  |
|----------|-----------|
|https://{endpoint}/vision/**v2.0/recognizeText[?mode]**|https://{endpoint}/vision/<**version string**>/read/analyze[?language]|
    
`Read` 中不支援 _mode_ 參數。 手寫和列印文字都會自動受到支援。
    
v3.0 中有新的選擇性 _language_ 參數可供使用。 如果您不知道文件的語言，或者其可能是多語系，請勿包含此參數。 

### `Get Recognize Text Operation Result`

|辨識文字 2.x |Read 3.x  |
|----------|-----------|
|https://{endpoint}/vision/**v2.0/textOperations/** {operationId}|https://{endpoint}/vision/<**version string**>/read/analyzeResults/{operationId}|

### <a name="get-recognize-text-operation-result-status-flags"></a>`Get Recognize Text Operation Result` 狀態旗標
當 `Get Recognize Text Operation Result` 的呼叫成功時，其會在 JSON 主體中傳回狀態字串欄位。 
 
|辨識文字 2.x |Read 3.x  |
|----------|-----------|
|`"NotStarted"` |   `"notStarted"`|
|`"Running"` | `"running"`|
|`"Failed"` | `"failed"`|
|`"Succeeded"` | `"succeeded"`|

### <a name="api-response-json"></a>API 回應 (JSON)

記下 JSON 的下列變更：    
* 在 v2.x 中，`Get Read Operation Result` 會在狀態為 `Succeeded` 時傳回 OCR 辨識 JSON。 在 v3.x 中，此欄位為 `succeeded`。
* 若要取得頁面陣列的根，請將 JSON 階層從 `recognitionResult` 變更為 `analyzeResult`/`readResults`。 每頁的行和字組 JSON 階層會維持不變，因此不需要變更程式碼。

V3.0 API 也引進了您可以選擇運用的下列改良功能。 如需詳細資訊，請參閱 API 參考：
* 已新增 `createdDateTime` 和 `lastUpdatedDateTime`，讓您可以追蹤處理的持續時間。 如需詳細資訊，請參閱文件。 
* `version` 會告訴您用來產生結果的 API 版本
* 已新增每個字組的 `confidence`。 此值已校正，因此，值 0.95 表示辨識正確的機會有 95%。 信賴分數可以用來選取要傳送給人工審核的文字。 
* `angle` 文字的一般方向 (採順時針方向)，測量範圍介於 -180 到 180 度之間。
* `width` 和 `"height"` 會提供您文件的維度，而 `"unit"` 則會根據文件類型提供這些維度的單位 (像素或英寸)。
* `page` 支援多頁文件
* `language` 文件的輸入語言 (來自選擇性的 _language_ 參數)。


在 2.x 中，輸出格式如下所示： 
    
```json
    {
        {
                "status": "Succeeded",
                "recognitionResult": [
                    {
                    "lines": [
                        {
                        "boundingBox": [
                            67,
                            646,
                            2582,
                            713,
                            2580,
                            876,
                            67,
                            821
                        ],
                        "text": "The quick brown fox jumps",
                        "words": [
                            {
                            "boundingBox": [
                                143,
                                650,
                                435,
                                661,
                                436,
                                823,
                                144,
                                824
                            ],
                            "text": "The",
                            },
            // The rest of result is omitted for brevity 
            
    }
```
    
在 v3.x 中，其已經過調整：
    
```json
    {
        {
            "status": "succeeded",
            "createdDateTime": "2020-05-28T05:13:21Z",
            "lastUpdatedDateTime": "2020-05-28T05:13:22Z",
            "analyzeResult": {
            "version": "3.0.0",
            "readResults": [
                {
                "page": 1,
                "angle": 0.8551,
                "width": 2661,
                "height": 1901,
                "unit": "pixel",
                "lines": [
                    {
                    "boundingBox": [
                        67,
                        646,
                        2582,
                        713,
                        2580,
                        876,
                        67,
                        821
                    ],
                    "text": "The quick brown fox jumps",
                    "words": [
                        {
                        "boundingBox": [
                            143,
                            650,
                            435,
                            661,
                            436,
                            823,
                            144,
                            824
                        ],
                        "text": "The",
                        "confidence": 0.958
                        },
        // The rest of result is omitted for brevity 
        
    }
```

## <a name="container-only"></a>僅限容器

### `Synchronous Read`

|Read 2.0 |Read 3.x  |
|----------|-----------|
|https://{endpoint}/vision/**v2.0/read/core/Analyze**     |https://{endpoint}/vision/<**version string**>/read/syncAnalyze[?language]|
