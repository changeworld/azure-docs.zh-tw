---
title: 語言 QnA Maker 的設計
description: QnA Maker 資源和該資源內的所有知識庫都支援單一語言。 若要為查詢提供最佳的回應結果，則需要使用單一語言。
ms.topic: conceptual
ms.date: 01/27/2020
ms.openlocfilehash: 5cb1dcd35649debbafd2e234606ad4c9d6906ea6
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/29/2020
ms.locfileid: "76846354"
---
# <a name="design-knowledge-base-for-content-language"></a>內容語言的設計知識庫

QnA Maker 資源和該資源內的所有知識庫都支援單一語言。 若要為查詢提供最佳的回應結果，則需要使用單一語言。

## <a name="single-language-per-resource"></a>每個資源一種語言

語言支援的 QnA Maker 考慮：

* QnA Maker 服務及其所有知識庫都僅支援一種語言。
* 建立服務的第一個知識庫時，會明確設定語言
* 語言是從建立知識庫時新增的檔案和 Url 來決定
* 此語言無法針對服務中的任何其他知識庫進行變更
* 認知搜尋服務（ranker #1）和 QnA Maker 服務（ranker #2）會使用此語言來產生查詢的最佳答案

## <a name="supporting-multiple-languages"></a>支援多種語言

如果您需要支援包含數種語言的知識庫系統，您可以選擇下列其中一種方法：

* 使用[翻譯文字服務](../../translator/translator-info-overview.md)將問題轉譯成單一語言，再將問題傳送至您的知識庫。 這可讓您專注于單一語言的品質，以及替代問題和答案的品質。
* 針對每種語言，建立 QnA Maker 資源和該資源內的知識庫。 這可讓您管理個別的替代問題，並針對每種語言更差別細微的答案文字。 這可提供您更大的彈性，但在所有語言的問題或答案變更時，需要更高的維護成本。

審查 QnA Maker[支援的語言](../overview/language-support.md)。

### <a name="support-each-language-with-a-qna-maker-resource"></a>支援每種語言與 QnA Maker 資源

* 為每種語言建立 QnA Maker 資源
* 僅新增該語言的檔案和 Url
* 使用資源的命名慣例來識別語言。 例如， `qna-maker-fr`適用于法文檔的所有知識庫

## <a name="next-steps"></a>後續步驟

瞭解如何查詢知識庫以取得解答的相關[概念](query-knowledge-base.md)。