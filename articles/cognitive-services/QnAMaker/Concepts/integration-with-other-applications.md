---
title: 與其他應用程式整合-QnA Maker
description: QnA Maker 整合到用戶端應用程式（例如聊天機器人）以及其他自然語言處理服務（例如 Language Understanding （LUIS））。
ms.topic: conceptual
ms.date: 01/27/2020
ms.openlocfilehash: 38b275aa2b8cf5768a2bc95634a7ff4892893eda
ms.sourcegitcommit: f915d8b43a3cefe532062ca7d7dbbf569d2583d8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/05/2020
ms.locfileid: "78300514"
---
# <a name="design-knowledge-base-for-client-applications"></a>用戶端應用程式的設計知識庫

QnA Maker 整合到用戶端應用程式（例如聊天機器人）以及其他自然語言處理服務（例如 Language Understanding （LUIS））。

## <a name="integration-with-a-conversational-client"></a>與對話式用戶端整合

QnA Maker 與對話式用戶端應用程式（例如[Microsoft Bot Framework](https://dev.botframework.com/)）整合。 傳送至 QnA Maker 的文字不需要進行清除或轉換。 QnA Maker 接受自然語言並傳回最佳答案。

## <a name="create-a-bot-without-writing-any-code"></a>建立 bot 而不撰寫任何程式碼

在您發佈知識庫之後，請選取 [**發佈**] 頁面中的 [**建立 bot** ] 按鈕來建立 bot。 使用[bot 教學](../Quickstarts/create-publish-knowledge-base.md)課程，以瞭解在您選取按鈕之後會發生什麼事。

## <a name="providing-multi-turn-conversations"></a>提供多回合交談

Bot 用戶端會從您的知識庫提供最佳的選取答案，如果答案是多回合 QnA 集的一部分，則可以提供後續追蹤提示。 瞭解[如何](../how-to/multiturn-conversation.md)在您的知識庫中新增多回合對話的問題和答案集合。

## <a name="natural-language-processing"></a>自然語言處理

雖然 QnA Maker 處理使用自然語言處理的問題，但也可以用來從多個知識庫回答問題的大型系統之一部分。 您可以將 QnA Maker 與另一個認知服務結合 Language Understanding （LUIS），以提供自然語言處理，然後再取得特定的知識庫。 深入瞭解何時和如何搭配使用[LUIS 和 QnA Maker](../../luis/choose-natural-language-processing-service.md?toc=/azure/cognitive-services/qnamaker/toc.json) 。

## <a name="next-steps"></a>後續步驟

瞭解 QnA Maker 的開發週期[概念](development-lifecycle-knowledge-base.md)。