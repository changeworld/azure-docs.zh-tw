---
title: 自訂命令-語音服務
titleSuffix: Azure Cognitive Services
description: 概要說明自訂命令的功能、功能和限制，這是建立語音應用程式的解決方案。
services: cognitive-services
author: trrwilson
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/11/2020
ms.author: travisw
ms.openlocfilehash: c9f7168bc0524b483413ade9792af18ff9cfebfa
ms.sourcegitcommit: d95cab0514dd0956c13b9d64d98fdae2bc3569a0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/25/2020
ms.locfileid: "91358537"
---
# <a name="what-is-custom-commands"></a>什麼是自訂命令？

[語音助理](voice-assistants.md)之類的應用程式會接聽使用者並採取行動，通常會說回來。 他們使用 [語音轉換文字](speech-to-text.md) 來轉譯使用者的語音，然後對文字的自然語言理解採取行動。 此動作通常會包含從 [文字轉換語音](text-to-speech.md)所產生之助理的語音輸出。 裝置會使用語音 SDK 的物件來連接到助理 `DialogServiceConnector` 。

**自訂命令** 可讓您輕鬆地建立針對語音優先互動體驗優化的豐富語音命令應用程式。 它提供統一的撰寫體驗、自動化的裝載模型，以及較低的複雜度，協助您專注于為語音命令的案例建立最佳解決方案。

自訂命令最適合用於工作完成或命令和控制案例，特別符合物聯網 (IoT) 裝置、環境和無周邊裝置。 範例包括用於旅遊、零售和汽車產業的解決方案，可讓您為來賓打造最佳的現成語音控制體驗、管理商店中的清查，以及在移動時控制汽車內的功能。

> [!TIP]
> 在的登陸頁面上，查看我們的範例示範 [https://speech.microsoft.com/customcommands](https://speech.microsoft.com/customcommands) 。

如果您想要建立複雜的對話式應用程式，建議您使用 [虛擬助理解決方案](https://docs.microsoft.com/azure/bot-service/bot-builder-enterprise-template-overview)來嘗試 Bot Framework。 您可以使用 Direct Line 語音，將語音新增至任何 bot framework bot。

自訂命令的良好候選項目具有固定的詞彙，且具有妥善定義的變數集。 例如，家用自動化工作（例如控制控溫器）是理想的選擇。

   ![工作完成案例的範例](media/voice-assistants/task-completion-examples.png "工作完成範例")

## <a name="getting-started-with-custom-commands"></a>開始使用自訂命令

使用自訂命令的目標是減少您的認知負載，以瞭解所有不同的技術，並專注于建立語音命令應用程式。 使用自訂命令來<a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesSpeechServices" target="_blank">建立 Azure 語音資源 <span class="docon docon-navigate-external x-hidden-focus"></span> </a>的第一個步驟。 您可以在語音 Studio 上撰寫自訂命令應用程式並加以發佈，之後裝置應用程式便可使用語音 SDK 與其進行通訊。

#### <a name="authoring-flow-for-custom-commands"></a>自訂命令的撰寫流程
   ![自訂命令的撰寫流程](media/voice-assistants/custom-commands-flow.png "自訂命令撰寫流程")

遵循我們的快速入門，讓您的第一個自訂命令應用程式執行程式碼的時間不超過10分鐘。

* [使用自訂命令建立語音助理](quickstart-custom-commands-application.md)

當您完成本快速入門之後，請探索我們的操作指南，以取得設計、開發、偵錯工具及整合自訂命令應用程式的詳細步驟。

## <a name="building-voice-assistants-with-custom-commands"></a>使用自訂命令建立語音助理
> [!VIDEO https://www.youtube.com/embed/1zr0umHGFyc]

## <a name="next-steps"></a>後續步驟

* [免費取得語音服務的訂用帳戶金鑰](overview.md#try-the-speech-service-for-free)
* [在 GitHub 上查看我們的語音助理存放庫以取得範例](https://aka.ms/speech/cc-samples)
* [移至語音 Studio 以試用自訂命令](https://speech.microsoft.com/customcommands)
* [取得語音 SDK](speech-sdk.md)
