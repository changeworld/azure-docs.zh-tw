---
title: 從 Bing 語音遷移至語音服務
titleSuffix: Azure Cognitive Services
description: 瞭解如何從 Azure 認知服務將現有的 Bing 語音訂用帳戶遷移至語音服務。
services: cognitive-services
author: wsturman
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 04/03/2020
ms.author: nitinme
ms.openlocfilehash: d0ffd786d3fb6bb5f0d70095d947c81caa070518
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/02/2020
ms.locfileid: "96499130"
---
# <a name="migrate-from-bing-speech-to-the-speech-service"></a>從 Bing 語音遷移至語音服務

使用本文將您的應用程式從 Bing 語音 API 遷移至語音服務。

本文概述 Bing 語音 Api 和語音服務之間的差異，並建議遷移應用程式的策略。 您的 Bing 語音 API 訂用帳戶金鑰無法搭配語音服務使用;您將需要新的語音服務訂用帳戶。

單一語音服務訂用帳戶金鑰會授與下列功能的存取權。 每個功能會分開計量，因此您只需就使用的功能來付費。

* [語音轉換文字](speech-to-text.md)
* [自訂語音轉換文字](https://cris.ai)
* [文字轉換語音](text-to-speech.md)
* [自訂文字轉換語音的語音](./how-to-custom-voice-create-voice.md)
* [語音翻譯](speech-translation.md) (不包括[文字翻譯](../translator/translator-info-overview.md))

[語音 SDK](speech-sdk.md) 將會取代 Bing 語音用戶端程式庫的功能，但會使用不同的 API。

## <a name="comparison-of-features"></a>功能比較

語音服務主要類似于 Bing 語音，但有下列差異。

| 特徵 | Bing 語音 | 語音服務 | 詳細資料 |
|--|--|--|--|
| C# SDK | :heavy_check_mark: | :heavy_check_mark: | 語音服務支援 Windows 10、通用 Windows 平臺 (UWP) 和 .NET Standard 2.0。 |
| C++ SDK | :heavy_minus_sign: | :heavy_check_mark: | 語音服務支援 Windows 和 Linux。 |
| Java SDK | :heavy_check_mark: | :heavy_check_mark: | 語音服務支援 Android 和語音裝置。 |
| 持續語音辨識 | 10 分鐘 | 無限制 | 語音 SDK 支援無限制的連續辨識，並在 timeout 或中斷連線時自動重新連接。 |
| 部分或過渡結果 | :heavy_check_mark: | :heavy_check_mark: | 語音 SDK 支援。 |
| 自訂語音模型 | :heavy_check_mark: | :heavy_check_mark: | Bing 語音需要個別的自訂語音訂用帳戶。 |
| 自訂聲音音調 | :heavy_check_mark: | :heavy_check_mark: | Bing 語音需要個別的自訂聲音訂用帳戶。 |
| 24 kHz 聲音 | :heavy_minus_sign: | :heavy_check_mark: |
| 語音意圖辨識 | 需要個別的 LUIS API 呼叫 | 已整合 (搭配 SDK) | 您可以搭配語音服務使用 LUIS 金鑰。 |
| 簡單意圖辨識 | :heavy_minus_sign: | :heavy_check_mark: |
| 長音訊檔案的批次轉譯 | :heavy_minus_sign: | :heavy_check_mark: |
| 辨識模式 | 手動 (透過端點 URI) | 自動 | 語音服務中無法使用辨識模式。 |
| 端點位置 | 全球 | 地區 | 區域端點能改善延遲。 |
| REST API | :heavy_check_mark: | :heavy_check_mark: | 語音服務 REST Api 與 Bing 語音 (不同的端點) 相容。 REST API 能支援文字轉換語音和有限的語音轉換文字功能。 |
| WebSocket 通訊協定 | :heavy_check_mark: | :heavy_minus_sign: | 語音 SDK 會針對需要持續連線到服務的功能來抽象化 web 通訊端連線，因此不再支援手動訂閱這些連接。 |
| 服務對服務 API 呼叫 | :heavy_check_mark: | :heavy_minus_sign: | 透過 C# 服務程式庫在 Bing 語音中提供。 |
| 開放原始碼 SDK | :heavy_check_mark: | :heavy_minus_sign: |

語音服務會使用以時間為基礎的定價模型 (而不是以交易為基礎的模型) 。 如需詳細資訊，請參閱 [語音服務定價](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/) 。

## <a name="migration-strategies"></a>移轉策略

如果您或您的組織在使用 Bing 語音 API 的開發或生產環境中有應用程式，您應該儘快更新它們以使用語音服務。 請參閱 [語音服務檔](index.yml) 以取得可用的 sdk、程式碼範例和教學課程。

語音服務 [REST api](./overview.md#reference-docs) 與 Bing 語音 api 相容。 如果您目前使用 Bing 語音 REST Api，您只需要變更 REST 端點，並切換至語音服務訂用帳戶金鑰。

如果您是針對特定程式設計語言使用 Bing 語音用戶端程式庫，基於 API 不同的原因，移轉至[語音 SDK](speech-sdk.md) 會需要對您的應用程式做出變更。 語音 SDK 能簡化您的程式碼，同時讓您存取新的功能。 語音 SDK 有各式各樣的程式設計語言。 所有平台上的 API 都非常類似，進一步簡化多平台開發的工作。

語音服務不提供全域端點。 請判斷自己的應用程式是否能在針對所有流量使用單一區域端點的情況下有效運作。 如果不行，請使用地理位置來判斷最有效的端點。 您在每個使用的區域中都需要個別的語音服務訂用帳戶。

開始使用語音 SDK：

1. 下載[語音 SDK](speech-sdk.md)。
1. 透過語音服務 [快速入門手冊](./get-started-speech-to-text.md?pivots=programming-language-csharp&tabs=dotnet) 和 [教學](how-to-recognize-intents-from-speech-csharp.md)課程進行。 此外，請查看[程式碼範例](./speech-sdk.md#sample-source-code)以學習使用新的 API。
1. 更新您的應用程式以使用語音服務。

## <a name="support"></a>支援

Bing 語音客戶應該透過開啟[支援票證](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)來連絡客戶支援。 如果您的支援需求需要[技術支援方案](https://azure.microsoft.com/support/plans/)，也可以連絡我們。

如需語音服務、SDK 和 API 支援，請造訪語音服務 [支援頁面](../cognitive-services-support-options.md?context=%2fazure%2fcognitive-services%2fspeech-service%2fcontext%2fcontext%253fcontext%253d%2fazure%2fcognitive-services%2fspeech-service%2fcontext%2fcontext)。

## <a name="next-steps"></a>後續步驟

* [免費試用語音服務](overview.md#try-the-speech-service-for-free)
* [開始使用語音轉換文字](get-started-speech-to-text.md)
* [開始使用文字轉換語音](get-started-text-to-speech.md)

## <a name="see-also"></a>另請參閱

* [語音服務版本資訊](releasenotes.md)
* [什麼是語音服務](overview.md)
* [語音服務和語音 SDK 檔](speech-sdk.md#get-the-speech-sdk)
