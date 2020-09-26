---
title: 從翻譯工具語音 API 遷移至語音服務
titleSuffix: Azure Cognitive Services
description: 瞭解如何將您的應用程式從翻譯工具語音 API 遷移至語音服務。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 01/21/2020
ms.author: aahi
ms.openlocfilehash: d6b1085d51d7345b233087986127cbc97c0597e1
ms.sourcegitcommit: d95cab0514dd0956c13b9d64d98fdae2bc3569a0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/25/2020
ms.locfileid: "91362056"
---
# <a name="migrate-from-the-translator-speech-api-to-the-speech-service"></a>從翻譯工具語音 API 遷移至語音服務

使用本文將您的應用程式從 Microsoft 翻譯工具語音 API 遷移至 [語音服務](index.yml)。 本指南概述翻譯工具語音 API 和語音服務之間的差異，並建議用來遷移應用程式的策略。

> [!NOTE]
> 語音服務不會接受您的翻譯工具語音 API 訂用帳戶金鑰。 您必須建立新的語音服務訂用帳戶。

## <a name="comparison-of-features"></a>功能比較

| 功能                                           | Translator Speech API                                  | 語音服務 | 詳細資料                                                                                                                                                                                                                                                                            |
|---------------------------------------------------|-----------------------------------------------------------------|------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| 翻譯為文字                               | :heavy_check_mark:                                              | :heavy_check_mark:                 |                                                                                                                                                                                                                                                                                    |
| 翻譯為語音                             | :heavy_check_mark:                                              | :heavy_check_mark:                 |                                                                                                                                                                                                                                                                                    |
| 全域端點                                   | :heavy_check_mark:                                              | :heavy_minus_sign:                 | 語音服務不提供全域端點。 全域端點可自動將流量導向最接近的區域端點，並降低應用程式中的延遲。                                                    |
| 區域端點                                | :heavy_minus_sign:                                              | :heavy_check_mark:                 |                                                                                                                                                                                                                                                                                    |
| 連線時間限制                             | 90 分鐘                                               | 搭配 SDK 時沒有限制。 搭配 WebSocket 連線時為 10 分鐘。                                                                                                                                                                                                                                                                                   |
| 標頭中的驗證金鑰                                | :heavy_check_mark:                                              | :heavy_check_mark:                 |                                                                                                                                                                                                                                                                                    |
| 在單一要求中翻譯多個語言 | :heavy_minus_sign:                                              | :heavy_check_mark:                 |                                                                                                                                                                                                                                                                                    |
| 可用的 SDK                                    | :heavy_minus_sign:                                              | :heavy_check_mark:                 | 請參閱 [語音服務檔](index.yml) 以取得可用的 sdk。                                                                                                                                                    |
| WebSocket 連線                            | :heavy_check_mark:                                              | :heavy_check_mark:                 |                                                                                                                                                                                                                                                                                    |
| 語言 API                                     | :heavy_check_mark:                                              | :heavy_minus_sign:                 | 語音服務支援 [翻譯工具語言參考](../translator-speech/languages-reference.md) 文章中所述的相同語言範圍。 |
| 粗話篩選和標記                       | :heavy_minus_sign:                                              | :heavy_check_mark:                 |                                                                                                                                                                                                                                                                                    |
| .WAV/PCM 作為輸入                                 | :heavy_check_mark:                                              | :heavy_check_mark:                 |                                                                                                                                                                                                                                                                                    |
| 其他檔案類型作為輸入                         | :heavy_minus_sign:                                              | :heavy_minus_sign:                 |                                                                                                                                                                                                                                                                                    |
| 部分結果                                   | :heavy_check_mark:                                              | :heavy_check_mark:                 |                                                                                                                                                                                                                                                                                    |
| 計時資訊                                       | :heavy_check_mark:                                              | :heavy_minus_sign:                 |                                                                                                                                                                 |
| 相互關連識別碼                                    | :heavy_check_mark:                                              | :heavy_minus_sign:                 |                                                                                                                                                                                                                                                                                    |
| 自訂語音模型                              | :heavy_minus_sign:                                              | :heavy_check_mark:                 | 語音服務提供自訂語音模型，可讓您針對組織的獨特詞彙自訂語音辨識。                                                                                                                                           |
| 自訂翻譯模型                         | :heavy_minus_sign:                                              | :heavy_check_mark:                 | 訂閱 Microsoft 文字翻譯 API 可讓您使用[自訂翻譯器](https://www.microsoft.com/translator/business/customization/)，來使用您自己的資料取得更精確的翻譯。                                                 |

## <a name="migration-strategies"></a>移轉策略

如果您或您的組織在使用翻譯工具語音 API 的開發或生產環境中有應用程式，您應該更新它們以使用語音服務。 請參閱 [語音服務](index.yml) 檔以取得可用的 sdk、程式碼範例和教學課程。 移轉時，請考慮下列事項：

* 語音服務不提供全域端點。 請判斷自己的應用程式是否能在針對所有流量使用單一區域端點的情況下有效運作。 如果不行，請使用地理位置來判斷最有效的端點。

* 如果您的應用程式會使用長時間執行的連線，且無法可用的 SDK，您可以使用 WebSocket 連線。 請透過在適當時間重新連線來因應 10 分鐘的逾時限制。

* 如果您的應用程式使用 Translator service 和翻譯工具語音 API 來啟用自訂翻譯模型，您可以使用「語音服務」直接新增類別識別碼。

* 不同于翻譯工具語音 API，語音服務可以在單一要求中完成翻譯成多種語言。

## <a name="next-steps"></a>後續步驟

* [免費試用語音服務](overview.md#try-the-speech-service-for-free)
* [快速入門：使用語音 SDK 在 UWP 應用程式中辨識語音](~/articles/cognitive-services/Speech-Service/quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-csharp&tabs=uwp)

## <a name="see-also"></a>另請參閱

* [什麼是語音服務](overview.md)
* [語音服務和語音 SDK 檔](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-devices-sdk-qsg)
