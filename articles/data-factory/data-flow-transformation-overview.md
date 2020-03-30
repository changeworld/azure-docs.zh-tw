---
title: 映射資料流程轉換概述
description: 映射資料流程中可用的不同轉換的概述
author: djpmsft
ms.author: daperlov
manager: anandsub
ms.service: data-factory
ms.topic: conceptual
ms.date: 03/10/2020
ms.openlocfilehash: 94f7277c1b741fb5ef6d2807fabbb266c0109415
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79086920"
---
# <a name="mapping-data-flow-transformation-overview"></a>映射資料流程轉換概述

下面是映射資料流程中當前支援的轉換的清單。 按一下每個轉換以瞭解其配置詳細資訊。

| 名稱 | 類別 | 描述 |
| ---- | -------- | ----------- |
| [骨料](data-flow-aggregate.md) | 架構修改器 | 定義不同類型的聚合，如 SUM、MIN、MAX 和 COUNT，按現有或計算列分組。 | 
| [變更資料列](data-flow-alter-row.md) | 行修改器 | 在行上設置插入、刪除、更新和向上策略。 |
| [條件拆分](data-flow-conditional-split.md) | 多個輸入/輸出 | 根據匹配條件將資料行路由到不同的流。 |
| [派生列](data-flow-derived-column.md) | 架構修改器 | 使用資料流程運算式語言生成新列或修改現有欄位。 | 
| [Exists](data-flow-exists.md) | 多個輸入/輸出 | 檢查資料是否存在於其他源或流中。 | 
| [篩選器](data-flow-filter.md) | 行修改器 | 根據條件篩選行。 |
| [扁平 化](data-flow-flatten.md) | 架構修改器 |  在分層結構（如 JSON）中獲取陣列值，並將其展開到單獨的行中。 |
| [加入](data-flow-join.md) | 多個輸入/輸出 |  合併來自兩個源或流的資料。 |
| [查閱](data-flow-lookup.md) | 多個輸入/輸出 | 來自另一個源的參考資料。 |
| [新增分支](data-flow-new-branch.md) | 多個輸入/輸出 | 針對同一資料流程應用多組操作和轉換。 |
| [樞紐](data-flow-pivot.md) | 架構修改器 | 一個或多個分組列具有將其不同行值轉換為單個列的聚合。 |
| [選取](data-flow-select.md) | 架構修改器 | 別名列和流名稱，以及刪除或重新排序列 |
| [接收](data-flow-sink.md) | - | 資料的最終目的地 |
| [排序](data-flow-sort.md) | 行修改器 | 對當前資料流程上的傳入行進行排序 |
| [來源](data-flow-source.md) | - | 資料流程的資料來源 |
| [代理鍵](data-flow-surrogate-key.md) | 架構修改器 | 添加遞增的非業務任意鍵值 |
| [Union](data-flow-union.md) | 多個輸入/輸出 | 垂直組合多個資料流程 |
| [取消支點](data-flow-unpivot.md) | 架構修改器 | 將列透視到行值 |
| [視窗](data-flow-window.md) | 架構修改器 |  定義資料流程中列的基於視窗的聚合。 |
