---
title: 自訂命令（預覽） - 語音服務
titleSuffix: Azure Cognitive Services
description: 自訂命令（預覽）的功能、功能和限制概述，自訂命令是創建語音應用程式的解決方案。
services: cognitive-services
author: trrwilson
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/11/2020
ms.author: travisw
ms.openlocfilehash: 2e1b6ee0bd6c392804915fac6ff23278a00b6d33
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79367834"
---
# <a name="what-are-custom-commands-preview"></a>什麼是自訂命令（預覽）？

語音應用程式（如[語音助理](voice-assistants.md)）會偵聽使用者並執行回應操作，通常還會回話。 他們使用[語音到文本](speech-to-text.md)來轉錄使用者的演講，然後對文本的自然語言理解採取行動。 此操作通常包括使用[文本到語音](text-to-speech.md)生成的助手的語音輸出。 設備使用語音 SDK`DialogServiceConnector`的物件連接到助手。

**自訂命令（預覽）** 是創建語音應用程式的簡化解決方案。 它提供了一個統一的創作體驗，一個自動託管模型，並相對較低的複雜性比其他選項，如[直接線語音](direct-line-speech.md)。 然而，這種簡化伴隨著靈活性的降低。 因此，自訂命令（預覽）最適合任務完成或命令和控制方案。 它特別適合物聯網 （IoT） 和無頭設備。

對於複雜的對話交互和與其他解決方案（如[虛擬助理解決方案和企業範本）](https://docs.microsoft.com/azure/bot-service/bot-builder-enterprise-template-overview)的集成，我們鼓勵您使用直接線上語音。

自訂命令（預覽）的良好候選者具有固定的詞彙表和定義良好的變數集。 例如，家庭自動化任務（如控制恒溫器）是理想的。

   ![任務完成方案示例](media/voice-assistants/task-completion-examples.png "任務完成示例")

## <a name="getting-started-with-custom-commands-preview"></a>開始使用自訂命令（預覽）

使用自訂命令（預覽）創建語音應用程式的第[一步是獲取語音訂閱金鑰](get-started.md)並訪問[語音工作室](https://speech.microsoft.com)上的自訂命令（預覽）產生器。 從那裡，您可以創作新的自訂命令（預覽）應用程式併發布它，之後設備上的應用程式可以使用語音 SDK 與其通信。

   ![自訂命令的創作流（預覽）](media/voice-assistants/custom-commands-flow.png "自訂命令（預覽）創作流")

我們提供快速入門，旨在讓您在 10 分鐘內運行代碼。

* [創建自訂命令（預覽）應用程式](quickstart-custom-speech-commands-create-new.md)
* [使用參數創建自訂命令（預覽）應用程式](quickstart-custom-speech-commands-create-parameters.md)
* [使用語音 SDK（C） 連接到自訂命令（預覽）應用程式#](quickstart-custom-speech-commands-speech-sdk.md)

完成快速入門後，探索我們的如何處理。

- [將驗證添加到自訂命令參數](./how-to-custom-speech-commands-validations.md)
- [使用語音 SDK 在用戶端上執行命令](./how-to-custom-speech-commands-fulfill-sdk.md)
- [在自訂命令中新增確認](./how-to-custom-speech-commands-confirmations.md)
- [在自訂命令中新增單一步驟的更正](./how-to-custom-speech-commands-one-step-correction.md)

## <a name="next-steps"></a>後續步驟

* [免費取得語音服務的訂用帳戶金鑰](get-started.md)
* [轉到語音工作室嘗試自訂命令](https://speech.microsoft.com)
* [取得語音 SDK](speech-sdk.md)
