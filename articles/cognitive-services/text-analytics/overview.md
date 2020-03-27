---
title: 什麼是文字分析 API？ - 功能 -
titleSuffix: Azure Cognitive Services
description: 從 Azure 認知服務中使用「文字分析 API」來進行情感分析、關鍵片語擷取、語言偵測和實體辨識。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: overview
ms.date: 03/04/2020
ms.author: aahi
ms.openlocfilehash: a9519be591581fa434825f1a1fb31749788a21a8
ms.sourcegitcommit: fe6c9a35e75da8a0ec8cea979f9dec81ce308c0e
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/26/2020
ms.locfileid: "78395733"
---
# <a name="what-is-the-text-analytics-api"></a>什麼是文字分析 API？

文字分析 API 是一種雲端式服務，可針對原始的文字提供進階的自然語言處理，同時包含四種主要功能：情感分析、關鍵片語擷取、語言偵測和具名實體辨識。

此 API 屬於 [Azure 認知服務](https://docs.microsoft.com/azure/cognitive-services/)的一部分，由雲端中的機器學習和 AI 演算法的集合所組成，可供您的開發專案使用。

> [!VIDEO https://channel9.msdn.com/Shows/AI-Show/Understanding-Text-using-Cognitive-Services/player]

文字分析可能代表不同的意義，但在認知服務中，文字分析 API 可提供四種類型的分析，如下所述。 您可以將這些功能與 [REST API](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-V2-1/) \(英文\) 或[用戶端程式庫](quickstarts/text-analytics-sdk.md)搭配使用。

## <a name="sentiment-analysis"></a>情感分析
使用[情感分析](how-tos/text-analytics-how-to-sentiment-analysis.md)可藉由分析原始文字而獲得有關於正面或負面情感的線索，來了解客戶對您的品牌或主題有何看法。 此 API 會為每份文件傳回 0 到 1 之間的情感分數，1 代表最正面的情感。<br /> 分析模型會使用大量文字主體和 Microsoft 的自然語言技術預先定型。 針對[選取的語言](text-analytics-supported-languages.md)，API 可對您所提供的任何原始文字進行分析及評分，並直接將結果傳回至呼叫端應用程式。

## <a name="key-phrase-extraction"></a>關鍵片語擷取
可自動[擷取關鍵片語](how-tos/text-analytics-how-to-keyword-extraction.md)，以快速識別重點。 例如，若輸入文字為 "The food was delicious and there were wonderful staff"，API 即會傳回主要討論要點："food" 和 "wonderful staff"。

## <a name="language-detection"></a>語言偵測
您可以[偵測輸入文字是以何種語言撰寫的](how-tos/text-analytics-how-to-language-detection.md)，並按要求針對所提交的每份文件回報單一語言代碼，包括多種不同的語言、變體、方言，和某些區域性/文化語言。 語言代碼各配有一個分數，表示分數的強度。

## <a name="named-entity-recognition"></a>具名實體辨識
[識別文字中的實體並分類](how-tos/text-analytics-how-to-entity-linking.md)為人員、位置、組織、日期/時間、數量、百分比、貨幣等等。 已知的實體也會辨識出來，並連結至網路上的其他資訊。

## <a name="use-containers"></a>使用容器

[使用文字分析容器](how-tos/text-analytics-how-to-install-containers.md)，藉由在更接近資料的位置安裝標準化的 Docker 容器，於本機擷取關鍵片語、偵測語言和分析人氣。

## <a name="typical-workflow"></a>一般工作流程

工作流程很簡單：您提交資料以進行分析，並在程式碼中處理輸出。 分析器會依原狀使用，不需要額外的設定或自訂。

1. [建立文字分析的 Azure 資源](../cognitive-services-apis-create-account.md)。 其後，請[取得為您產生的金鑰](../cognitive-services-apis-create-account.md#get-the-keys-for-your-resource)，用以驗證您的要求。

2. [編寫要求](how-tos/text-analytics-how-to-call-api.md#json-schema)，其中包含 JSON 格式的原始非結構化文字，作為您的資料。

3. 將要求發佈至註冊期間所建立的端點，並附上所需的資源：情感分析、關鍵片語擷取、語言偵測或具名實體辨識。

4. 將回應串流處理或儲存至本機。 視要求之不同，結果可能是情感分數、擷取的關鍵片語集合或語言代碼。

輸出會根據識別碼以單一 JSON 文件的形式傳回，且附有您發佈的每個文字文件所產生的結果。 您可在後續分析和視覺化結果，或將結果分類為可操作的深入解析。

資料不會儲存在您的帳戶中。 文字分析 API 所執行的作業是無狀態的，這表示您提供的文字經處理後隨即傳回結果。

## <a name="text-analytics-for-multiple-programming-experience-levels"></a>適用於多個程式設計經驗層級的 Text Analytics

即使您沒有太多程式設計經驗，也可以在您的流程中開始使用 Text Analytics API。 使用這些教學課程可以了解如何使用 API 以不同的方式來分析文字，以符合您的經驗層級。 

* 所需的最少程式設計：
    * [使用文字分析和 Power Automate 在 Excel 中擷取資訊](tutorials/extract-excel-information.md)
    * [使用文字分析 API 和 MS Flow 來識別 Yammer 群組中註解的情緒](https://docs.microsoft.com/Yammer/integrate-yammer-with-other-apps/sentiment-analysis-flow-azure?toc=%2F%2Fazure%2Fcognitive-services%2Ftext-analytics%2Ftoc.json&bc=%2F%2Fazure%2Fbread%2Ftoc.json)
    * [將 Power BI 與文字分析 API 整合以分析客戶的意見反應](tutorials/tutorial-power-bi-key-phrases.md)
* 建議的程式設計體驗：
    * [使用 Azure Databricks 對串流資料進行情感分析](https://docs.microsoft.com/azure/azure-databricks/databricks-sentiment-analysis-cognitive-services?toc=%2F%2Fazure%2Fcognitive-services%2Ftext-analytics%2Ftoc.json&bc=%2F%2Fazure%2Fbread%2Ftoc.json)
    * [建置 Flask 應用程式以翻譯文字、分析情緒及合成語音](https://docs.microsoft.com/azure/cognitive-services/translator/tutorial-build-flask-app-translation-synthesis?toc=%2F%2Fazure%2Fcognitive-services%2Ftext-analytics%2Ftoc.json&bc=%2F%2Fazure%2Fbread%2Ftoc.json)


<a name="supported-languages"></a>

## <a name="supported-languages"></a>支援的語言

本節已移至個別的文章，以利說明。 如需此內容，請參閱[文字分析 API 中支援的語言](text-analytics-supported-languages.md)。

<a name="data-limits"></a>

## <a name="data-limits"></a>資料限制

所有的文字分析 API 端點均接受原始文字資料。 目前的限制是每個文件 5,120 個字元；如果您需要分析較大的文件，您可以其分成較小的區塊。

| 限制 | 值 |
|------------------------|---------------|
| 單一文件的大小上限 | 依 [`StringInfo.LengthInTextElements`](https://docs.microsoft.com/dotnet/api/system.globalization.stringinfo.lengthintextelements) 測量的 5,120 個字元。 |
| 整體要求的大小上限 | 1 MB |
| 要求中的文件數上限 | 1,000 份文件 |

您的速率限制會隨著定價層而不同。

| 層          | 每秒要求 | 每分鐘要求 |
|---------------|---------------------|---------------------|
| S / 多服務 | 1000                | 1000                |
| S0 / F0         | 100                 | 300                 |
| S1            | 200                 | 300                 |
| S2            | 300                 | 300                 |
| S3            | 500                 | 500                 |
| S4            | 1000                | 1000                |

要求是針對每個「文字分析」功能個別進行測量。 例如，您可以同時將適用於定價層的要求數量上限傳送到每個功能。      

## <a name="unicode-encoding"></a>Unicode 編碼

針對文字表示法和字元計數計算，文字分析 API 會使用 Unicode 編碼。 要求可使用 UTF-8 和 UTF-16 來提交，兩者的字元計數並沒有明顯差異。 Unicode 字碼指標會作為字元長度的啟發學習法，且會視為等同於文字分析資料大小上限的用途。 如果您使用 [`StringInfo.LengthInTextElements`](https://docs.microsoft.com/dotnet/api/system.globalization.stringinfo.lengthintextelements) 取得字元計數，您所使用就是我們用來測量資料大小的相同方法。

## <a name="next-steps"></a>後續步驟

+ [建立文字分析的 Azure 資源](../cognitive-services-apis-create-account.md)，以取得應用程式的金鑰和端點。

+ 使用[快速入門](quickstarts/text-analytics-sdk.md)以開始傳送 API 呼叫。 請了解如何以最少的程式碼提交文字、選擇分析，以及檢視結果。

+ 如需新版本和功能的詳細資訊，請參閱[文字分析 API 的新功能](whats-new.md)。

+ 稍微深入了解這個使用 Azure Databricks 的[情感分析教學課程](https://docs.microsoft.com/azure/azure-databricks/databricks-sentiment-analysis-cognitive-services)。

+ 請至[外部和社群內容頁面](text-analytics-resource-external-community.md)參閱我們的部落格文章清單，以及觀看更多相關影片以了解如何搭配其他工具和技術來使用文字分析 API。
