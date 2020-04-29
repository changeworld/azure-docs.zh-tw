---
title: 自訂命令（預覽）-語音服務
titleSuffix: Azure Cognitive Services
description: 概述自訂命令的功能、功能和限制（預覽），這是建立語音應用程式的解決方案。
services: cognitive-services
author: trrwilson
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/11/2020
ms.author: travisw
ms.openlocfilehash: 2e1b6ee0bd6c392804915fac6ff23278a00b6d33
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/29/2020
ms.locfileid: "79367834"
---
# <a name="what-are-custom-commands-preview"></a>什麼是自訂命令（預覽）？

聲音應用程式（例如[語音助理](voice-assistants.md)）會接聽使用者並採取動作來回應，通常會說回來。 他們使用[語音轉換文字](speech-to-text.md)來轉譯使用者的語音，然後對文字的自然語言理解採取行動。 此動作通常包含以[文字轉換語音](text-to-speech.md)所產生的小幫手的語音輸出。 裝置會使用語音 SDK 的`DialogServiceConnector`物件連接到助理。

**自訂命令（預覽）** 是用來建立語音應用程式的簡化解決方案。 它提供統一的撰寫經驗、自動裝載模型，以及相對較低的複雜性與其他選項，例如[Direct Line Speech](direct-line-speech.md)。 不過，這種簡化功能會降低彈性。 因此，自訂命令（預覽）最適合用於工作完成或命令和控制案例。 這對於物聯網（IoT）和無周邊裝置而言特別符合。

針對複雜的交談互動，以及與其他解決方案（例如[虛擬助理解決方案和企業範本](https://docs.microsoft.com/azure/bot-service/bot-builder-enterprise-template-overview)）的整合，建議您使用 Direct Line 語音。

良好的自訂命令（預覽）候選項目具有固定的詞彙，其中包含一組妥善定義的變數。 例如，「家庭自動化」工作，像是控制控溫器，都是理想的選擇。

   ![工作完成案例的範例](media/voice-assistants/task-completion-examples.png "工作完成範例")

## <a name="getting-started-with-custom-commands-preview"></a>開始使用自訂命令（預覽）

使用自訂命令（預覽）來製作語音應用程式的第一個步驟，就是[取得語音訂用帳戶金鑰](get-started.md)，並存取[語音 Studio](https://speech.microsoft.com)上的自訂命令（預覽）產生器。 從該處，您可以撰寫新的自訂命令（預覽）應用程式並加以發佈，在此之後，裝置上的應用程式可以使用語音 SDK 與它進行通訊。

   ![撰寫自訂命令的流程（預覽）](media/voice-assistants/custom-commands-flow.png "自訂命令（預覽）撰寫流程")

我們提供的快速入門是為了讓您在10分鐘內執行程式碼而設計的。

* [建立自訂命令（預覽）應用程式](quickstart-custom-speech-commands-create-new.md)
* [建立具有參數的自訂命令（預覽）應用程式](quickstart-custom-speech-commands-create-parameters.md)
* [使用語音 SDK （C）連接到自訂命令（預覽）應用程式#](quickstart-custom-speech-commands-speech-sdk.md)

當您完成快速入門時，請探索我們的 how-to。

- [將驗證新增至自訂命令參數](./how-to-custom-speech-commands-validations.md)
- [使用語音 SDK 滿足用戶端上的命令](./how-to-custom-speech-commands-fulfill-sdk.md)
- [在自訂命令中新增確認](./how-to-custom-speech-commands-confirmations.md)
- [在自訂命令中新增單一步驟的更正](./how-to-custom-speech-commands-one-step-correction.md)

## <a name="next-steps"></a>後續步驟

* [免費取得語音服務的訂用帳戶金鑰](get-started.md)
* [前往語音 Studio 以試用自訂命令](https://speech.microsoft.com)
* [取得語音 SDK](speech-sdk.md)
