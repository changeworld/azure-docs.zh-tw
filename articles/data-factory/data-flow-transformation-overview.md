---
title: 對應資料流程轉換總覽
description: 對應資料流程中可用的各種轉換總覽
author: djpmsft
ms.author: daperlov
manager: anandsub
ms.service: data-factory
ms.topic: conceptual
ms.date: 03/10/2020
ms.openlocfilehash: 94f7277c1b741fb5ef6d2807fabbb266c0109415
ms.sourcegitcommit: 20429bc76342f9d365b1ad9fb8acc390a671d61e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/11/2020
ms.locfileid: "79086920"
---
# <a name="mapping-data-flow-transformation-overview"></a>對應資料流程轉換總覽

以下是目前對應資料流程中支援的轉換清單。 按一下每個轉換，以瞭解其設定詳細資料。

| 名稱 | 類別 | 描述 |
| ---- | -------- | ----------- |
| [彙總](data-flow-aggregate.md) | 架構修飾詞 | 定義不同類型的匯總，例如 SUM、MIN、MAX 和 COUNT，依現有或計算資料行分組。 | 
| [改變數據列](data-flow-alter-row.md) | 資料列修飾詞 | 在資料列上設定 insert、delete、update 和 upsert 原則。 |
| [條件式分割](data-flow-conditional-split.md) | 多個輸入/輸出 | 根據相符的條件，將資料列路由傳送至不同的資料流程。 |
| [衍生的資料行](data-flow-derived-column.md) | 架構修飾詞 | 使用資料流程運算式語言產生新的資料行或修改現有欄位。 | 
| [Exists](data-flow-exists.md) | 多個輸入/輸出 | 檢查您的資料是否存在於另一個來源或資料流程中。 | 
| [Filter](data-flow-filter.md) | 資料列修飾詞 | 根據條件來篩選資料列。 |
| [平展](data-flow-flatten.md) | 架構修飾詞 |  接受階層式結構（例如 JSON）內的陣列值，並將其展開為個別的資料列。 |
| [Join](data-flow-join.md) | 多個輸入/輸出 |  結合兩個來源或資料流程的資料。 |
| [查閱](data-flow-lookup.md) | 多個輸入/輸出 | 參考來自另一個來源的資料。 |
| [新增分支](data-flow-new-branch.md) | 多個輸入/輸出 | 針對相同的資料流程套用多組作業和轉換。 |
| [樞紐](data-flow-pivot.md) | 架構修飾詞 | 一或多個群組資料行的匯總，其中會將其相異的資料列值轉換成個別資料行。 |
| [選取](data-flow-select.md) | 架構修飾詞 | 別名資料行和資料流程名稱，以及放置或重新排序資料行 |
| [接收](data-flow-sink.md) | - | 資料的最終目的地 |
| [排序](data-flow-sort.md) | 資料列修飾詞 | 排序目前資料流程上的傳入資料列 |
| [Source](data-flow-source.md) | - | 資料流程的資料來源 |
| [代理鍵](data-flow-surrogate-key.md) | 架構修飾詞 | 新增遞增的非企業任意索引鍵值 |
| [Union](data-flow-union.md) | 多個輸入/輸出 | 垂直合併多個資料流程 |
| [取消樞紐](data-flow-unpivot.md) | 架構修飾詞 | 將資料行轉換成資料列值 |
| [範圍](data-flow-window.md) | 架構修飾詞 |  定義資料流程中資料行的以視窗為基礎的匯總。 |
