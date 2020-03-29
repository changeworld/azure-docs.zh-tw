---
title: 語言設計 - QnA 製造商
description: QnA Maker 資源和該資源中的所有知識庫都支援單一語言。 單一語言是為查詢提供最佳答案結果所必需的。
ms.topic: conceptual
ms.date: 01/27/2020
ms.openlocfilehash: 5cb1dcd35649debbafd2e234606ad4c9d6906ea6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "76846354"
---
# <a name="design-knowledge-base-for-content-language"></a>設計內容語言知識庫

QnA Maker 資源以及該資源中的所有知識庫都支援單一語言。 單一語言是為查詢提供最佳答案結果所必需的。

## <a name="single-language-per-resource"></a>每個資源的單一語言

QnA 製造商對語言支援的注意事項：

* QnA Maker 服務及其所有知識庫僅支援一種語言。
* 創建服務的第一個知識庫時，將顯式設置語言
* 創建知識庫時從添加的檔和 URL 確定語言
* 無法為服務中的任何其他知識庫更改語言
* 認知搜索服務（ranker #1）和 QnA Maker 服務（ranker #2）使用該語言來生成查詢的最佳答案

## <a name="supporting-multiple-languages"></a>支援多種語言

如果需要支援包含多種語言的知識庫系統，可以選擇以下方法之一：

* 在將問題發送到知識庫之前，使用[翻譯文本服務](../../translator/translator-info-overview.md)將問題翻譯成單一語言。 這使您可以專注于單一語言的品質以及備用問題和答案的品質。
* 為每個語言創建 QnA Maker 資源以及該資源內的知識庫。 這允許您管理單獨的備用問題和回答每種語言更細微的文本。 這為您提供了更大的靈活性，但當問題或答案在所有語言之間發生變化時，需要更高的維護成本。

查看 QnA 製造商[支援的語言](../overview/language-support.md)。

### <a name="support-each-language-with-a-qna-maker-resource"></a>使用 QnA Maker 資源支援每種語言

* 為每種語言創建 QnA Maker 資源
* 僅添加該語言的檔和 URL
* 使用資源的命名約定來標識語言。 法語文檔的所有`qna-maker-fr`知識庫都有一個示例

## <a name="next-steps"></a>後續步驟

瞭解如何[查詢](query-knowledge-base.md)知識庫以尋找答案。