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
ms.openlocfilehash: a4c551a6e9b8d79fdeb7b5219f311c7c7c969757
ms.sourcegitcommit: 2ba6303e1ac24287762caea9cd1603848331dd7a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/15/2020
ms.locfileid: "97505353"
---
# <a name="example-user-scenarios-for-the-text-analytics-api"></a>Text Analytics API 的範例使用者案例

Text Analytics API 是雲端式服務，可對文字提供進階自然語言處理。 本文介紹了將 API 整合至您的商務解決方案和流程中的一些範例使用案例。 

## <a name="analyze-survey-results"></a>Analyze Survey 結果

透過使用情感分析處理未經處理的文字回應，從客戶和員工問卷調查結果中擷取深入解析。 彙總結果以進行分析、追蹤並提升參與度。

![說明如何對客戶和員工的問卷執行情感分析的影像。](media/use-cases/survey-results.svg)

## <a name="analyze-recorded-inbound-customer-calls"></a>分析已錄製的傳入客戶呼叫

使用語音轉換文字、情感分析和關鍵片語擷取，從客戶服務呼叫中解壓縮見解。 在 Power BI 儀表板或入口網站中顯示結果，以進一步了解客戶、醒目提示客戶服務趨勢並提升客戶參與度。 將 API 要求作為批次處理傳送以進行報告，或即時介入。 請參閱 [GitHub 上](https://github.com/rlagh2/callcenteranalytics)的範例程式碼。

![說明如何使用情感分析，自動從客戶服務呼叫中取得深入解析的影像](media/use-cases/azure-inbound.svg)

## <a name="process-and-categorize-support-incidents"></a>處理並分類支援事件

使用關鍵片語擷取和實體辨識來處理以非結構化文字格式提交的支援要求。 使用擷取的詞彙和實體對資源規劃和趨勢分析的要求進行分類。

![說明如何使用關鍵片語擷取和實體辨識對事件報告與趨勢進行分類的影像](media/use-cases/support-incidents.svg)

## <a name="monitor-your-products-social-media-feeds"></a>監視您產品的社交媒體摘要

監視產品 twitter 或 Facebook 頁面上的使用者產品意見反應。 使用這些資料分析客戶對新產品推出的情緒、擷取關於功能和功能要求的關鍵片語，或在發生客戶抱怨時解決它們。 請參閱 [Microsoft Power Automate 範本](https://flow.microsoft.com/galleries/public/templates/2680d2227d074c4d901e36c66e68f6f9/run-sentiment-analysis-on-tweets-and-push-results-to-a-power-bi-dataset/)範例。

![說明如何使用關鍵片語擷取監視您的產品及公司對社交社交媒體的意見反應的影像](media/use-cases/social-feed.svg)

## <a name="classify-and-redact-documents-that-have-sensitive-information"></a>分類及修訂具有敏感性資訊的檔

使用命名實體辨識來識別檔中的個人和敏感性資訊。 您可以使用資料來分類檔或修訂檔，以安全地共用它們。

![說明如何使用 NER 來偵測個人資訊以及分類和修訂檔的影像](media/use-cases/sensitive-docs.jpg)

## <a name="perform-opinion-mining"></a>執行意見挖掘

針對問卷中的產品或服務的特定層面、客戶意見反應，或在文字中保存某個方面相關意見的地方，將相關意見分組。 您可以使用它來協助引導產品上市和改進、行銷工作，或反白顯示您的產品或服務的表現。 

> [!div class="mx-imgBorder"] 
> :::image type="content" source="media/use-cases/aspect-based-sentiment.png" alt-text="旅館的範例意見。":::

## <a name="next-steps"></a>後續步驟

* [什麼是文字分析 API？](overview.md)
* [使用用戶端程式庫將要求傳送至文字分析 API](quickstarts/client-libraries-rest-api.md)
