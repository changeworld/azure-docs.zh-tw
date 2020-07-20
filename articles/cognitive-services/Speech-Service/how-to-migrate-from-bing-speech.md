---
title: 從 Bing 語音遷移至語音服務
titleSuffix: Azure Cognitive Services
description: 瞭解如何從 Azure 認知服務從現有的 Bing 語音訂用帳戶遷移至語音服務。
services: cognitive-services
author: wsturman
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 04/03/2020
ms.author: nitinme
ms.openlocfilehash: 7b78bdb070cdf1364fe7fbdc75f175be7ce145ff
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/29/2020
ms.locfileid: "80656456"
---
# <a name="migrate-from-bing-speech-to-the-speech-service"></a>從 Bing 語音遷移至語音服務

請使用本文，將您的應用程式從 Bing 語音 API 遷移至語音服務。

本文概述 Bing 語音 Api 和語音服務之間的差異，並提供遷移應用程式的建議策略。 您的 Bing 語音 API 訂用帳戶金鑰無法與語音服務搭配使用;您將需要新的語音服務訂用帳戶。

單一語音服務訂用帳戶金鑰會授與下列功能的存取權。 每個功能會分開計量，因此您只需就使用的功能來付費。

* [語音轉文字](speech-to-text.md)
* [自訂語音轉換文字](https://cris.ai)
* [文字轉換語音](text-to-speech.md)
* [自訂文字轉換語音的語音](how-to-customize-voice-font.md)
* [語音翻譯](speech-translation.md) (不包括[文字翻譯](../translator/translator-info-overview.md))

[語音 SDK](speech-sdk.md) 將會取代 Bing 語音用戶端程式庫的功能，但會使用不同的 API。

## <a name="comparison-of-features"></a>功能比較

語音服務主要類似 Bing 語音，但有下列差異。

| 功能 | Bing 語音 | 語音服務 | 詳細資料 |
|--|--|--|--|
| C# SDK | :heavy_check_mark: | :heavy_check_mark: | 語音服務支援 Windows 10、通用 Windows 平臺（UWP）和 .NET Standard 2.0。 |
| C++ SDK | :heavy_minus_sign: | :heavy_check_mark: | 語音服務支援 Windows 和 Linux。 |
| Java SDK | :heavy_check_mark: | :heavy_check_mark: | 語音服務支援 Android 和語音裝置。 |
| 持續語音辨識 | 10 分鐘 | 無限制 (搭配 SDK) | Bing 語音和語音服務 Websocket 通訊協定都支援每個呼叫最多10分鐘。 不過，語音 SDK 會在逾時或中斷連線時自動重新連線。 |
| 部分或過渡結果 | :heavy_check_mark: | :heavy_check_mark: | 搭配 WebSocket 通訊協定或 SDK。 |
| 自訂語音模型 | :heavy_check_mark: | :heavy_check_mark: | Bing 語音需要個別的自訂語音訂用帳戶。 |
| 自訂聲音音調 | :heavy_check_mark: | :heavy_check_mark: | Bing 語音需要個別的自訂聲音訂用帳戶。 |
| 24 kHz 語音 | :heavy_minus_sign: | :heavy_check_mark: |
| 語音意圖辨識 | 需要個別的 LUIS API 呼叫 | 已整合 (搭配 SDK) | 您可以搭配語音服務使用 LUIS 金鑰。 |
| 簡單意圖辨識 | :heavy_minus_sign: | :heavy_check_mark: |
| 長音訊檔案的批次轉譯 | :heavy_minus_sign: | :heavy_check_mark: |
| 辨識模式 | 手動 (透過端點 URI) | 自動 | 語音服務無法使用辨識模式。 |
| 端點位置 | 全域 | 地區 | 區域端點能改善延遲。 |
| REST API | :heavy_check_mark: | :heavy_check_mark: | 語音服務 REST Api 與 Bing 語音（不同端點）相容。 REST API 能支援文字轉換語音和有限的語音轉換文字功能。 |
| WebSocket 通訊協定 | :heavy_check_mark: | :heavy_check_mark: | 語音服務 Websocket API 與 Bing 語音（不同端點）相容。 可以的話，請移轉至語音 SDK 以簡化您的程式碼。 |
| 服務對服務 API 呼叫 | :heavy_check_mark: | :heavy_minus_sign: | 透過 C# 服務程式庫在 Bing 語音中提供。 |
| 開放原始碼 SDK | :heavy_check_mark: | :heavy_minus_sign: |

語音服務會使用以時間為基礎的定價模型（而非以交易為基礎的模型）。 如需詳細資訊，請參閱[語音服務價格](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/)。

## <a name="migration-strategies"></a>移轉策略

如果您或您的組織在使用 Bing 語音 API 的開發或生產環境中有應用程式，您應該儘快更新它們以使用語音服務。 請參閱[語音服務檔](index.yml)以取得可用的 sdk、程式碼範例和教學課程。

語音服務[REST api](rest-apis.md)與 Bing 語音 api 相容。 如果您目前使用的是 Bing 語音 REST Api，您只需要變更 REST 端點，並切換至語音服務訂用帳戶金鑰。

語音服務 Websocket 通訊協定也與 Bing 語音所使用的通訊協定相容。 我們建議針對新開發使用語音 SDK，而非 WebSocket。 將現有程式碼移轉至該 SDK 也是很好的作法。 不過和 REST API 相同，透過 WebSocket 使用 Bing 語音的現有程式碼只需要變更端點並更新金鑰。

如果您是針對特定程式設計語言使用 Bing 語音用戶端程式庫，基於 API 不同的原因，移轉至[語音 SDK](speech-sdk.md) 會需要對您的應用程式做出變更。 語音 SDK 能簡化您的程式碼，同時讓您存取新的功能。 語音 SDK 提供各種不同的程式設計語言。 所有平台上的 API 都非常類似，進一步簡化多平台開發的工作。

語音服務不提供全域端點。 請判斷自己的應用程式是否能在針對所有流量使用單一區域端點的情況下有效運作。 如果不行，請使用地理位置來判斷最有效的端點。 您在每個使用的區域中都需要個別的語音服務訂用帳戶。

如果您的應用程式會使用長時間執行的連線，且無法可用的 SDK，您可以使用 WebSocket 連線。 請透過在適當時間重新連線來因應 10 分鐘的逾時限制。

開始使用語音 SDK：

1. 下載[語音 SDK](speech-sdk.md)。
1. 逐步解說語音服務[快速入門手冊](~/articles/cognitive-services/Speech-Service/quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-csharp&tabs=dotnet)和[教學](how-to-recognize-intents-from-speech-csharp.md)課程。 此外，請查看[程式碼範例](samples.md)以學習使用新的 API。
1. 更新您的應用程式以使用語音服務。

## <a name="support"></a>支援

Bing 語音客戶應該透過開啟[支援票證](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)來連絡客戶支援。 如果您的支援需求需要[技術支援方案](https://azure.microsoft.com/support/plans/)，也可以連絡我們。

如需語音服務、SDK 和 API 支援，請造訪語音服務[支援頁面](support.md)。

## <a name="next-steps"></a>後續步驟

* [免費試用語音服務](get-started.md)
* [快速入門：使用語音 SDK 在 UWP 應用程式中辨識語音](~/articles/cognitive-services/Speech-Service/quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-csharp&tabs=uwp)

## <a name="see-also"></a>請參閱
* [語音服務版本資訊](releasenotes.md)
* [什麼是語音服務](overview.md)
* [語音服務和語音 SDK 檔](speech-sdk.md#get-the-speech-sdk)
