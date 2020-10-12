---
title: 語言 QnA Maker 的設計
description: 此資源內的 QnA Maker 資源和所有知識庫都支援單一語言。 您必須使用單一語言來提供查詢的最佳解答結果。
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 01/27/2020
ms.openlocfilehash: e09e15f71b0574a5612e7f9bacd8aaa4739a441c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "91776998"
---
# <a name="design-knowledge-base-for-content-language"></a>設計內容語言的知識庫

QnA Maker 資源和該資源內的所有知識庫都支援單一語言。 您必須使用單一語言來提供查詢的最佳解答結果。

## <a name="single-language-per-resource"></a>每個資源的單一語言

語言支援的 QnA Maker 考慮：

* QnA Maker 服務及其所有知識庫都只支援一種語言。
* 建立服務的第一個知識庫時，會明確設定語言
* 語言取決於建立知識庫時新增的檔案和 Url。
* 無法變更服務中任何其他知識庫的語言
* 認知搜尋服務 (ranker #1) 和 QnA Maker 服務 (ranker #2) ，以產生查詢的最佳答案，以使用語言

## <a name="supporting-multiple-languages"></a>支援多種語言

如果您需要支援包含數種語言的知識庫系統，您可以選擇下列其中一種方法：

* 使用 [翻譯文字服務](../../translator/translator-info-overview.md) 將問題轉譯成單一語言，然後再將問題傳送至您的知識庫。 這可讓您專注于單一語言的品質，以及替代問題和解答的品質。
* 針對每個語言建立 QnA Maker 資源，以及該資源內的知識庫。 這可讓您管理個別的替代問題，以及針對每種語言更差別細微的解答文字。 這可提供您更大的彈性，但在所有語言的問題或答案變更時，需要更高的維護成本。

審核 QnA Maker [支援的語言](../overview/language-support.md) 。

### <a name="support-each-language-with-a-qna-maker-resource"></a>使用 QnA Maker 資源支援每種語言

* 為每種語言建立 QnA Maker 資源
* 只新增該語言的檔案和 Url
* 使用資源的命名慣例來識別語言。 範例適用 `qna-maker-fr` 于法文檔的所有知識庫

## <a name="next-steps"></a>後續步驟

瞭解如何查詢知識庫以取得解答的 [概念](query-knowledge-base.md) 。