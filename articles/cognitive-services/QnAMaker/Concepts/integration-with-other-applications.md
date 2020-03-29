---
title: 與其他應用集成 - QnA 製造商
description: QnA Maker 集成到用戶端應用程式（如聊天機器人）以及其他自然語言處理服務（如語言理解 （LUIS）） 中。
ms.topic: conceptual
ms.date: 01/27/2020
ms.openlocfilehash: 38b275aa2b8cf5768a2bc95634a7ff4892893eda
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "78300514"
---
# <a name="design-knowledge-base-for-client-applications"></a>為客戶應用程式設計知識庫

QnA Maker 集成到用戶端應用程式（如聊天機器人）以及其他自然語言處理服務（如語言理解 （LUIS）） 中。

## <a name="integration-with-a-conversational-client"></a>與對話用戶端集成

QnA Maker 與會話用戶端應用程式（如[微軟機器人框架](https://dev.botframework.com/)）集成。 發送到 QnA Maker 的文本不需要清理或轉換。 QnA Maker 接受自然語言並返回最佳答案。

## <a name="create-a-bot-without-writing-any-code"></a>創建自動程式而不編寫任何代碼

發佈知識庫後，通過選擇"**創建自動程式"** 按鈕，從 **"發佈"** 頁創建自動程式。 使用[自動程式教程](../Quickstarts/create-publish-knowledge-base.md)瞭解選擇按鈕後會發生什麼情況。

## <a name="providing-multi-turn-conversations"></a>提供多轉對話

如果答案是多轉 QnA 集的一部分，則自動程式用戶端提供來自知識庫中的最佳答案，並且可以提供後續提示。 [瞭解如何將](../how-to/multiturn-conversation.md)多轉對話問題和答案集添加到您的知識庫。

## <a name="natural-language-processing"></a>自然語言處理

雖然 QnA Maker 處理使用自然語言處理的問題，但它也可以使用一個更大的系統的一部分來回答來自多個知識庫的問題。 您可以將 QnA Maker 與另一個認知服務（語言理解 （LUIS） 相結合，在到達特定知識庫之前提供自然語言處理。 詳細瞭解何時以及如何一起使用 LUIS[和 QnA 製造商](../../luis/choose-natural-language-processing-service.md?toc=/azure/cognitive-services/qnamaker/toc.json)。

## <a name="next-steps"></a>後續步驟

瞭解 QnA 製造商的開發週期[概念](development-lifecycle-knowledge-base.md)。