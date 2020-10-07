---
title: 與其他應用程式整合-QnA Maker
description: QnA Maker 整合至用戶端應用程式，例如聊天機器人，以及其他自然語言處理服務，例如 Language Understanding (LUIS) 。
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 01/27/2020
ms.openlocfilehash: ff5e64dd31542b1e31df6d9ca709567b322995ce
ms.sourcegitcommit: ef69245ca06aa16775d4232b790b142b53a0c248
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/06/2020
ms.locfileid: "91776522"
---
# <a name="design-knowledge-base-for-client-applications"></a>設計用戶端應用程式的知識庫

QnA Maker 整合至用戶端應用程式，例如聊天機器人，以及其他自然語言處理服務，例如 Language Understanding (LUIS) 。

## <a name="integration-with-a-conversational-client"></a>與對話式用戶端整合

QnA Maker 與對話式用戶端應用程式（如 [Microsoft Bot Framework](https://dev.botframework.com/)）整合。 傳送給 QnA Maker 的文字不需要清理或轉換。 QnA Maker 接受自然語言，並傳回最佳答案。

## <a name="create-a-bot-without-writing-any-code"></a>建立 bot 而不撰寫任何程式碼

發佈知識庫之後，請選取 [**建立 bot** ] 按鈕，從 [**發佈**] 頁面建立 bot。 使用 [bot 教學](../Quickstarts/create-publish-knowledge-base.md) 課程，以瞭解您在選取按鈕之後會發生什麼事。

## <a name="providing-multi-turn-conversations"></a>提供多回合對話

Bot 用戶端會從您的知識庫提供最佳選取的答案，如果答案是多重回合 QnA 組的一部分，就可以提供後續提示。 瞭解 [如何](../how-to/multiturn-conversation.md) 將多回合對話問題和解答集新增至您的知識庫。

## <a name="natural-language-processing"></a>自然語言處理

雖然 QnA Maker 會處理使用自然語言處理的問題，但也可以使用較大系統的一部分，以從多個知識庫回答問題。 您可以將 QnA Maker 與另一個認知服務（Language Understanding (LUIS) ）結合，以提供自然語言處理，然後才取得特定的知識庫。 深入瞭解何時以及如何搭配使用 [LUIS 和 QnA Maker](../../luis/choose-natural-language-processing-service.md?toc=/azure/cognitive-services/qnamaker/toc.json) 。

## <a name="next-steps"></a>後續步驟

瞭解 QnA Maker 的開發週期 [概念](development-lifecycle-knowledge-base.md) 。