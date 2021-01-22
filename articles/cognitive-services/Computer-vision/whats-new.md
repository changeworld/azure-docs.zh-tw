---
title: 電腦視覺的新功能
titleSuffix: Azure Cognitive Services
description: 本文包含有關電腦視覺的消息。
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: overview
ms.date: 01/13/2021
ms.author: pafarley
ms.openlocfilehash: 33987be39258adc74cf4f88dbb0544f7026f6086
ms.sourcegitcommit: 0aec60c088f1dcb0f89eaad5faf5f2c815e53bf8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/14/2021
ms.locfileid: "98183348"
---
# <a name="whats-new-in-computer-vision"></a>電腦視覺的新功能

了解該服務的新功能。 這些項目可能是版本資訊、影片、部落格文章與其他類型的資訊。 將此頁面加入書籤，以掌握服務的最新狀態。

## <a name="january-2021"></a>2021 年 1 月

### <a name="spatial-analysis-container-update"></a>空間分析容器更新

已發行具有新功能集的新版[空間分析容器](spatial-analysis-container.md)。 此 Docker 容器可讓您分析即時串流影片，透過實體環境了解人員之間的空間關聯性，以及移動情況。 

* [空間分析作業](spatial-analysis-operations.md)現在可以設定為偵測某個人是否戴著面罩這類的臉部保護覆蓋物。 
    * 您可以藉由設定 `ENABLE_FACE_MASK_CLASSIFIER` 參數，為 `personcount`、`personcrossingline` 和 `personcrossingpolygon` 作業啟用面罩分類器。
    * `face_mask` 和 `face_noMask` 屬性會以中繼資料的形式傳回，且影片串流中所偵測到的每個人都會有信賴分數


## <a name="october-2020"></a>2020 年 10 月

### <a name="computer-vision-api-v31-ga"></a>電腦視覺 API v3.1 GA

正式運作的電腦視覺 API 已升級至 v3.1。

## <a name="september-2020"></a>2020 年 9 月

### <a name="spatial-analysis-container-preview"></a>Spatial 分析容器預覽

[空間分析容器](spatial-analysis-container.md)目前為預覽狀態。 電腦視覺的空間分析功能可讓您分析即時串流影片，透過實體環境了解人員之間的空間關聯性，以及移動情況。 Spatial 分析是一種 Docker 容器，可供您用來執行內部部署。 

### <a name="read-api-v31-public-preview-adds-ocr-for-japanese"></a>Read API v3.1 公開預覽版新增日文的 OCR
電腦視覺的 Read API v3.1 公開預覽版新增了這些功能：
* 日文 OCR
* 指出每行文字的外觀是手寫或列印樣式，以及信賴分數 (僅限拉丁語言)。
* 若文件包含多個頁面，則只會擷取選取的頁面或頁面範圍文字。

* 此預覽版本的 Read API 支援英文、荷蘭文、法文、德文、義大利文、日文、葡萄牙文、簡體中文和西班牙文語言。

若要深入了解，請參閱 [Read API 概觀](concept-recognizing-text.md)。

> [!div class="nextstepaction"]
> [深入了解 Read API v3.1 公開預覽 2](https://westus2.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-1-preview-2/operations/5d986960601faab4bf452005)

## <a name="july-2020"></a>2020 年 7 月

### <a name="read-api-v31-public-preview-with-ocr-for-simplified-chinese"></a>Read API 3.1 公開預覽版新增了簡體中文 OCR
電腦視覺的 Read API v3.1 公開預覽新增了簡體中文的支援。

* 此預覽版本的 Read API 支援英文、荷蘭文、法文、德文、義大利文、葡萄牙文、簡體中文和西班牙文語言。

若要深入了解，請參閱 [Read API 概觀](concept-recognizing-text.md)。

> [!div class="nextstepaction"]
> [深入了解 Read API v3.1 公開預覽 1](https://westus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-1-preview-1/operations/5d986960601faab4bf452005)

## <a name="may-2020"></a>2020 年 5 月
電腦視覺 API v3.0 已公開上市，並包含[讀取 API](concept-recognizing-text.md) 的更新：

* 支援英文、荷蘭文、法文、德文、義大利文、葡萄牙文和西班牙文
* 更高的精確度
* 每個擷取字組的信賴分數
* 新的輸出格式

## <a name="march-2020"></a>2020 年 3 月

* 現在對於此服務的所有 HTTP 要求都會強制執行 TLS 1.2。 如需詳細資訊，請參閱 [Azure 認知服務安全性](../cognitive-services-security.md)。

## <a name="january-2020"></a>2020 年 1 月

### <a name="read-api-30-public-preview"></a>Read API 3.0 公開預覽

您現在可選擇使用 3.0 版的讀取 API，從影像中擷取列印或手寫的文字。 相較於舊版，3.0 提供：
* 更高的精確度
* 新的輸出格式
* 每個擷取字組的信賴分數
* 支援搭配其他語言參數的西班牙文與英文

遵循[擷取文字快速入門](./quickstarts/csharp-hand-text.md?tabs=version-3)，開始使用 3.0 API。

## <a name="cognitive-service-updates"></a>認知服務更新

[認知服務的 Azure 更新公告](https://azure.microsoft.com/updates/?product=cognitive-services)