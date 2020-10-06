---
title: Microsoft Translator 服務
titlesuffix: Azure Cognitive Services
description: 將翻譯工具整合到您的應用程式、網站、工具與其他解決方案中，以提供多語言使用者體驗。
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: overview
ms.date: 09/11/2020
ms.author: swmachan
ms.custom: cog-serv-seo-aug-2020
keywords: 翻譯工具, 文字翻譯, 機器翻譯, 翻譯服務
ms.openlocfilehash: 32ae7f75c1b953e8af7dfef83c1971c2f78b0b62
ms.sourcegitcommit: 03662d76a816e98cfc85462cbe9705f6890ed638
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/15/2020
ms.locfileid: "90530330"
---
# <a name="what-is-the-translator-service"></a>什麼是翻譯服務？

翻譯工具是雲端式機器翻譯服務，屬於 [Azure 認知服務](https://docs.microsoft.com/azure/?pivot=products&panel=ai) 系列的認知 API，可用來建置智慧型應用程式。 您可以輕鬆地在應用程式、網站、工具和解決方案中整合翻譯工具。 其可讓您以 [70 種以上的語言](languages.md)新增多語言使用者體驗，並且可在使用任何作業系統的任何硬體平台上用於文字翻譯。

## <a name="about-microsoft-translator"></a>關於 Microsoft Translator

翻譯工具可為多種 Microsoft 產品和服務提供技術支援，並由全球數千個企業運用在其應用程式和工作流程中，使其內容得以觸及全球各地的用戶。

語音翻譯由翻譯工具提供技術支援，也可透過 [Azure 語音服務](https://docs.microsoft.com/azure/cognitive-services/speech-service/)來使用。 此服務將翻譯工具語音 API 和自訂語音服務的功能結合為統合且可完整自訂的服務。 

## <a name="language-support"></a>語言支援

翻譯工具提供文字翻譯、音譯、語言偵測和字典的多語言支援。 如需完整清單請參閱[語言支援](language-support.md)，或透過 [REST API](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-languages) 以程式設計方式存取清單。  

## <a name="microsoft-translator-neural-machine-translation"></a>Microsoft Translator 類神經機器翻譯

類神經機器翻譯 (NMT) 是高品質 AI 技術架構機器翻譯的新標準。 它取代了已在 2010 年代中期達到品質高原期的舊有統計機器翻譯 (SMT) 技術。

NMT 所提供的翻譯之所以優於 SMT，不僅僅是從原始翻譯品質評分的觀點來看，也因為其翻譯聽起來更流暢、更人性化。 這個流暢性的主要原因在於 NMT 使用句子的完整內容來翻譯單字。 SMT 則只採用每個字前後幾個字的緊鄰內容。

NMT 模型位於 API 核心，使用者並不會看到這些模型。 唯一可見的差異是翻譯品質的提升，特別是中文、日文和阿拉伯文等語言。

深入了解 [NMT 的運作方式](https://www.microsoft.com/en-us/translator/mt.aspx#nnt) \(英文\)。

## <a name="improve-translations-with-custom-translator"></a>使用自訂翻譯改善翻譯

自訂翻譯工具是翻譯工具服務的擴充功能，與翻譯工具搭配使用時，可協助您自訂類神經翻譯系統以及改善特定術語和風格的翻譯。

透過自訂翻譯工具，您將可建置翻譯系統，以處理您自己的企業或產業中使用的術語。 其後，您自訂的翻譯系統將可透過一般翻譯工具，使用類別參數跨多種類型的裝置輕易整合到您現有的應用程式、工作流程和網站中。

深入了解[自訂翻譯](customization.md)。

## <a name="next-steps"></a>後續步驟

- [註冊](translator-text-how-to-signup.md)存取金鑰。
- 請試用我們的[快速入門](quickstart-translator.md)來快速呼叫翻譯工具服務。
- [API 參考](https://docs.microsoft.com/azure/cognitive-services/Translator/reference/v3-0-reference)提供 API 的技術文件。
- [價格詳細資料](https://azure.microsoft.com/pricing/details/cognitive-services/translator-text-api/)
