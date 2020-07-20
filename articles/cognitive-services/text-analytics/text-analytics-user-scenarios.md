---
title: Text Analytics API 的範例使用者案例
titleSuffix: Azure Cognitive Services
description: 使用這篇文章來了解將 Text Analytics API 整合至您的服務和流程中的一些常見案例。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: conceptual
ms.date: 05/13/2020
ms.author: aahi
ms.openlocfilehash: eb82422aa0e3d45743539da623fcb919e8e77bbc
ms.sourcegitcommit: f0b206a6c6d51af096a4dc6887553d3de908abf3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/28/2020
ms.locfileid: "84141803"
---
# <a name="example-user-scenarios-for-the-text-analytics-api"></a>Text Analytics API 的範例使用者案例

Text Analytics API 是雲端式服務，可對文字提供進階自然語言處理。 本文介紹了將 API 整合至您的商務解決方案和流程中的一些範例使用案例。 

## <a name="analyze-survey-results"></a>Analyze Survey 結果

透過使用情感分析處理未經處理的文字回應，從客戶和員工問卷調查結果中擷取深入解析。 彙總結果以進行分析、追蹤並提升參與度。

![說明如何對客戶和員工的問卷執行情感分析的影像。](media/use-cases/survey-results.svg)

## <a name="analyze-recorded-inbound-customer-calls"></a>分析已錄製的傳入客戶呼叫

使用文字轉換語音、情感分析和關鍵片語擷取，從客戶服務呼叫中擷取深入解析。 在 Power BI 儀表板或入口網站中顯示結果，以進一步了解客戶、醒目提示客戶服務趨勢並提升客戶參與度。 將 API 要求作為批次處理傳送以進行報告，或即時介入。 請參閱[GitHub 上](https://github.com/rlagh2/callcenteranalytics)的範例程式碼。

![說明如何使用情感分析，自動從客戶服務呼叫中取得深入解析的影像](media/use-cases/azure-inbound.svg)

## <a name="process-and-categorize-support-incidents"></a>處理並分類支援事件

使用關鍵片語擷取和實體辨識來處理以非結構化文字格式提交的支援要求。 使用擷取的詞彙和實體對資源規劃和趨勢分析的要求進行分類。

![說明如何使用關鍵片語擷取和實體辨識對事件報告與趨勢進行分類的影像](media/use-cases/support-incidents.svg)

## <a name="monitor-your-products-social-media-feeds"></a>監視產品的社交媒體摘要

在您產品的 twitter 或 Facebook 頁面上監視使用者產品意見反應。 使用這些資料分析客戶對新產品推出的情緒、擷取關於功能和功能要求的關鍵片語，或在發生客戶抱怨時解決它們。 請參閱範例 [Microsoft Flow 範本](https://flow.microsoft.com/galleries/public/templates/2680d2227d074c4d901e36c66e68f6f9/run-sentiment-analysis-on-tweets-and-push-results-to-a-power-bi-dataset/)。

![說明如何使用關鍵片語擷取監視您的產品及公司對社交社交媒體的意見反應的影像](media/use-cases/social-feed.svg)

## <a name="classify-and-redact-documents-that-have-sensitive-information"></a>分類和修訂具有敏感性資訊的檔

使用命名實體辨識來識別檔中的個人和敏感性資訊。 使用資料對檔進行分類或修訂，讓它們可以安全地共用。

![說明如何使用 NER 來偵測個人資訊及分類和修訂檔的影像](media/use-cases/sensitive-docs.jpg)

## <a name="perform-opinion-mining"></a>執行意見挖掘

在問卷中與產品或服務的特定層面相關的意見、客戶意見反應，或文字持有某方面的意見。 使用它來協助引導產品上市和改進、行銷工作，或反白顯示您的產品或服務的執行方式。 

> [!div class="mx-imgBorder"] 
> :::image type="content" source="media/use-cases/aspect-based-sentiment.png" alt-text="飯店的範例意見。":::

## <a name="next-steps"></a>後續步驟

* [什麼是文字分析 API？](overview.md)
* [使用用戶端程式庫將要求傳送至文字分析 API](quickstarts/text-analytics-sdk.md)
