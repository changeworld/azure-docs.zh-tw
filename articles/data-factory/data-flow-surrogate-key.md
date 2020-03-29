---
title: 映射資料流程代理金鑰轉換
description: 如何使用 Azure 資料工廠的映射資料流程代理金鑰轉換生成順序鍵值
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 02/12/2019
ms.openlocfilehash: bab48aa9079c1b8020bb828a6bb91bd244a78cf1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "74930209"
---
# <a name="mapping-data-flow-surrogate-key-transformation"></a>映射資料流程代理金鑰轉換



使用 Surrogate 索引鍵轉換，將遞增的非商務任意索引鍵值新增至您的資料流資料列集。 這在設計星型結構描述分析資料模型的維度資料表時非常有用，您維度資料表中的每個成員都需要有唯一的非商務索引鍵 (Kimball DW 方法論的一部分)。

![代理金鑰轉換](media/data-flow/surrogate.png "代理金鑰轉換")

[索引鍵資料行] 是您要為新 Surrogate 索引鍵資料行取的名稱。

[開始值] 是遞增值的開始點。

## <a name="increment-keys-from-existing-sources"></a>來自現有源的增量鍵

如果要從源中存在的值啟動序列，可以在代理金鑰轉換後立即使用派生列轉換，並將這兩個值一起添加：

![SK 添加最大值](media/data-flow/sk006.png "代理金鑰轉換添加最大值")

要用前面的最大值設定鍵值的種子，可以使用兩種技術：

### <a name="database-sources"></a>資料庫源

使用"查詢"選項使用源轉換從源中選擇 MAX（）：

![代理金鑰查詢](media/data-flow/sk002.png "代理金鑰轉換查詢")

### <a name="file-sources"></a>檔源

如果以前的最大值位於檔中，則可以使用源變換和聚合轉換，並使用 MAX（） 運算式函數獲取以前的最大值：

![代理金鑰檔](media/data-flow/sk008.png "代理金鑰檔")

在這兩種情況下，都必須將傳入的新資料與包含前一個最大值的源聯接在一起：

![代理金鑰聯接](media/data-flow/sk004.png "代理金鑰聯接")

## <a name="next-steps"></a>後續步驟

這些示例使用[聯接](data-flow-join.md)和[派生列](data-flow-derived-column.md)轉換。
