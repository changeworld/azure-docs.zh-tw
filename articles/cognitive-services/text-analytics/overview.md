---
title: 使用文字分析 API 進行文字採礦和分析 - Azure 認知服務
titleSuffix: Azure Cognitive Services
description: 了解如何使用文字分析 API 進行文字採礦。 將其用於情感分析、語言偵測，以及其他形式的自然語言處理。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: overview
ms.date: 11/17/2020
ms.author: aahi
keywords: 文字採礦, 情感分析, 文字分析
ms.custom: cog-serv-seo-aug-2020
ms.openlocfilehash: 14dfa6111bbda8750b175c8c9b2a96321e1af2c5
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/24/2020
ms.locfileid: "95519399"
---
# <a name="what-is-the-text-analytics-api"></a>什麼是文字分析 API？

文字分析 API 是一項雲端式服務，可提供自然語言處理 (NLP) 功能來進行文字採礦和文字分析，包括：情感分析、意見挖掘、關鍵片語擷取、語言偵測，以及具名實體辨識。

此 API 屬於 [Azure 認知服務](../index.yml)的一部分，由雲端中的機器學習和 AI 演算法的集合所組成，可供您的開發專案使用。 您可以將這些功能與 [REST API](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-V2-1/) \(英文\) 或[用戶端程式庫](quickstarts/text-analytics-sdk.md)搭配使用。

> [!VIDEO https://channel9.msdn.com/Shows/AI-Show/Understanding-Text-using-Cognitive-Services/player]

## <a name="sentiment-analysis"></a>情感分析

使用[情感分析](how-tos/text-analytics-how-to-sentiment-analysis.md)對文字進行採礦，可獲得關於正面或負面情感的線索，進而了解客戶對您的品牌或主題有何看法。 

此功能會根據服務在句子和文件層級找到的最高信賴分數提供情感標籤 (例如「負面」、「中性」和「正面」)。 此功能也會為每份文件和其中的句子傳回 0 到 1 之間的信賴分數，以表示正面、中性和負面情感。 您也可以[使用容器](how-tos/text-analytics-how-to-install-containers.md)在內部部署執行服務。

從 v3.1 預覽版開始，意見挖掘是情感分析的功能。 這項功能也稱為自然語言處理 (NLP) 中的層面型情感分析，可提供文字中與各層面相關意見的詳細資訊 (例如產品或服務的屬性)。

## <a name="key-phrase-extraction"></a>關鍵片語擷取

使用[關鍵片語擷取](how-tos/text-analytics-how-to-keyword-extraction.md)可快速識別文字中的主要概念。 例如，若輸入文字 "The food was delicious and there were wonderful staff"，關鍵片語擷取即會傳回主要討論要點："food" 和 "wonderful staff"。

## <a name="language-detection"></a>語言偵測

語言偵測可以[偵測輸入文字的撰寫語言](how-tos/text-analytics-how-to-language-detection.md)，並按要求針對所提交的每份文件回報單一語言代碼，包括多種不同的語言、變體、方言，和某些區域性/文化語言。 語言代碼會與信賴分數配對。

## <a name="named-entity-recognition"></a>具名實體辨識

「具名實體辨識 (NER)」可以[將文字中的實體識別和分類](how-tos/text-analytics-how-to-entity-linking.md)為人員、地點、組織或數量，而眾所皆知的實體還可以在辨識後連結至網路上的更多資訊。

## <a name="deploy-on-premises-using-docker-containers"></a>使用 Docker 容器在內部部署環境進行部署

[使用文字分析容器](how-tos/text-analytics-how-to-install-containers.md)在內部部署環境中部署 API 功能。 這些 Docker 容器可讓服務更加契合您的資料，以實現合規性、安全性或其他操作性原因。 文字分析提供下列容器：

* 情感分析
* 關鍵片語擷取 (預覽)
* 語言偵測 (預覽)
* 健康情況的文字分析 (預覽)

## <a name="asynchronous-operations"></a>非同步作業

`/analyze` 端點可讓您[以非同步方式](how-tos/text-analytics-how-to-call-api.md)使用文字分析 API 的精選功能，例如 NER 和關鍵片語擷取。

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
    * [使用文字分析 API 和 MS Flow 來識別 Yammer 群組中註解的情緒](/Yammer/integrate-yammer-with-other-apps/sentiment-analysis-flow-azure?bc=%252f%252fazure%252fbread%252ftoc.json&toc=%252f%252fazure%252fcognitive-services%252ftext-analytics%252ftoc.json)
    * [將 Power BI 與文字分析 API 整合以分析客戶的意見反應](tutorials/tutorial-power-bi-key-phrases.md)
* 建議的程式設計體驗：
    * [使用 Azure Databricks 對串流資料進行情感分析](/azure/databricks/scenarios/databricks-sentiment-analysis-cognitive-services?bc=%252f%252fazure%252fbread%252ftoc.json&toc=%252f%252fazure%252fcognitive-services%252ftext-analytics%252ftoc.json)
    * [建置 Flask 應用程式以翻譯文字、分析情緒及合成語音](../translator/tutorial-build-flask-app-translation-synthesis.md?bc=%252f%252fazure%252fbread%252ftoc.json&toc=%252f%252fazure%252fcognitive-services%252ftext-analytics%252ftoc.json)


<a name="supported-languages"></a>

## <a name="supported-languages"></a>支援的語言

本節已移至個別的文章，以利說明。 如需此內容，請參閱[文字分析 API 中支援的語言](./language-support.md)。

<a name="data-limits"></a>

## <a name="data-limits"></a>資料限制

所有的文字分析 API 端點均接受原始文字資料。 如需詳細資訊，請參閱[資料限制](concepts/data-limits.md)一文。

## <a name="unicode-encoding"></a>Unicode 編碼

針對文字表示法和字元計數計算，文字分析 API 會使用 Unicode 編碼。 要求可使用 UTF-8 和 UTF-16 來提交，兩者的字元計數並沒有明顯差異。 Unicode 字碼指標會作為字元長度的啟發學習法，且會視為等同於文字分析資料大小上限的用途。 如果您使用 [`StringInfo.LengthInTextElements`](/dotnet/api/system.globalization.stringinfo.lengthintextelements) 取得字元計數，您所使用就是我們用來測量資料大小的相同方法。

## <a name="next-steps"></a>後續步驟

+ [建立文字分析的 Azure 資源](../cognitive-services-apis-create-account.md)，以取得應用程式的金鑰和端點。

+ 使用[快速入門](quickstarts/text-analytics-sdk.md)以開始傳送 API 呼叫。 請了解如何以最少的程式碼提交文字、選擇分析，以及檢視結果。

+ 如需新版本和功能的詳細資訊，請參閱[文字分析 API 的新功能](whats-new.md)。

+ 稍微深入了解這個使用 Azure Databricks 的[情感分析教學課程](/azure/databricks/scenarios/databricks-sentiment-analysis-cognitive-services)。

+ 請至[外部和社群內容頁面](text-analytics-resource-external-community.md)參閱我們的部落格文章清單，以及觀看更多相關影片以了解如何搭配其他工具和技術來使用文字分析 API。