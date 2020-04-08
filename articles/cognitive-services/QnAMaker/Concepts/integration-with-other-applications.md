---
title: 與其他應用整合 - QnA 製造商
description: QnA Maker 整合到用戶端應用程式(如聊天機器人)以及其他自然語言處理服務(如語言理解 (LUIS)) 中。
ms.topic: conceptual
ms.date: 01/27/2020
ms.openlocfilehash: c1edbfb6badfb73ce08a99709da0f8bfb61b7dc3
ms.sourcegitcommit: 6397c1774a1358c79138976071989287f4a81a83
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/07/2020
ms.locfileid: "80804182"
---
# <a name="design-knowledge-base-for-client-applications"></a>為客戶應用程式設計知識庫

QnA Maker 整合到用戶端應用程式(如聊天機器人)以及其他自然語言處理服務(如語言理解 (LUIS)) 中。

## <a name="integration-with-a-conversational-client"></a>與對話用戶端整合

QnA Maker 與會話用戶端應用程式(如[微軟機器人框架](https://dev.botframework.com/))集成。 發送到 QnA Maker 的文字不需要清理或轉換。 QnA Maker 接受自然語言並返回最佳答案。

## <a name="create-a-bot-without-writing-any-code"></a>建立自動程式而不編寫任何程式碼

發佈知識庫後,通過選擇「**創建自動程式」** 按鈕,從 **「發布」** 頁創建自動程式。 使用[自動程式教程](../Quickstarts/create-publish-knowledge-base.md)瞭解選擇按鈕後會發生什麼情況。

## <a name="providing-multi-turn-conversations"></a>提供多轉對話

如果答案是多轉QnA對的一部分,則自動用戶端提供來自知識庫中的最佳答案,並提供後續提示。 [瞭解如何將](../how-to/multiturn-conversation.md)多轉對話問題和答案集添加到您的知識庫。

## <a name="natural-language-processing"></a>自然語言處理

雖然 QnA Maker 處理使用自然語言處理的問題,但它也可以使用一個更大的系統的一部分來回答來自多個知識庫的問題。 您可以將 QnA Maker 與另一個認知服務(語言理解 (LUIS) 相結合,在到達特定知識庫之前提供自然語言處理。 詳細瞭解何時以及如何一起使用 LUIS[和 QnA 製造商](../../luis/choose-natural-language-processing-service.md?toc=/azure/cognitive-services/qnamaker/toc.json)。

## <a name="next-steps"></a>後續步驟

瞭解 QnA 製造商的開發週期[概念](development-lifecycle-knowledge-base.md)。