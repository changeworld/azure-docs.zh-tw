---
title: 對應資料流程轉換總覽
description: 對應資料流程中可用的不同轉換概述
author: dcstwh
ms.author: weetok
manager: anandsub
ms.service: data-factory
ms.topic: conceptual
ms.date: 10/27/2020
ms.openlocfilehash: 9d44890e84e97a413543a4291d1331fee0f04841
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/02/2020
ms.locfileid: "96490868"
---
# <a name="mapping-data-flow-transformation-overview"></a>對應資料流程轉換總覽

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)] 

以下是在對應資料流程中目前支援的轉換清單。 按一下每個轉換，以瞭解其設定詳細資料。

| 名稱 | 類別 | 描述 |
| ---- | -------- | ----------- |
| [彙總](data-flow-aggregate.md) | 架構修飾詞 | 定義不同類型的匯總，例如 SUM、MIN、MAX 以及依現有或計算資料行分組的計數。 | 
| [變更資料列](data-flow-alter-row.md) | Row 修飾詞 | 設定資料列的 insert、delete、update 和 upsert 原則。 |
| [條件式分割](data-flow-conditional-split.md) | 多個輸入/輸出 | 根據比對條件，將資料列路由傳送至不同的資料流程。 |
| [衍生的資料行](data-flow-derived-column.md) | 架構修飾詞 | 使用資料流程運算式語言產生新的資料行或修改現有的欄位。 | 
| [Exists](data-flow-exists.md) | 多個輸入/輸出 | 檢查您的資料是否存在於另一個來源或資料流程中。 | 
| [Filter](data-flow-filter.md) | Row 修飾詞 | 根據條件篩選資料列。 |
| [扁平化](data-flow-flatten.md) | 架構修飾詞 |  在階層式結構（例如 JSON）內採用陣列值，並將它們展開成個別的資料列。 |
| [聯結](data-flow-join.md) | 多個輸入/輸出 |  結合兩個來源或資料流程中的資料。 |
| [查閱](data-flow-lookup.md) | 多個輸入/輸出 | 來自其他來源的參考資料。 |
| [新增分支](data-flow-new-branch.md) | 多個輸入/輸出 | 針對相同的資料流程套用多組作業和轉換。 |
| [樞紐分析](data-flow-pivot.md) | 架構修飾詞 | 一或多個群組資料行將其相異的資料列值轉換成個別資料行的匯總。 |
| [排名](data-flow-rank.md) | 架構修飾詞 | 根據排序條件產生排序的排名 |
| [選取](data-flow-select.md) | 架構修飾詞 | 別名資料行和資料流程名稱，以及卸載或重新排列資料行 |
| [接收](data-flow-sink.md) | - | 資料的最終目的地 |
| [排序](data-flow-sort.md) | Row 修飾詞 | 在目前的資料流程上排序內送資料列 |
| [來源](data-flow-source.md) | - | 資料流程的資料來源 |
| [代理鍵](data-flow-surrogate-key.md) | 架構修飾詞 | 加入遞增的非商務任意金鑰值 |
| [Union](data-flow-union.md) | 多個輸入/輸出 | 垂直合併多個資料流程 |
| [取消樞紐](data-flow-unpivot.md) | 架構修飾詞 | 將資料行分割成資料列值 |
| [Window](data-flow-window.md) | 架構修飾詞 |  在您的資料流程中定義以視窗為基礎的資料行匯總。 |
