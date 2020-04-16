---
title: 對應資料流程轉換概述
description: 對應資料串體的不同轉換的概述
author: djpmsft
ms.author: daperlov
manager: anandsub
ms.service: data-factory
ms.topic: conceptual
ms.date: 03/10/2020
ms.openlocfilehash: 68423bee6096357add9b5d4b107c984ac67c9cab
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/16/2020
ms.locfileid: "81412973"
---
# <a name="mapping-data-flow-transformation-overview"></a>對應資料流程轉換概述

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

下面是映射數據流中當前支援的轉換的清單。 按一下每個轉換以瞭解其配置詳細資訊。

| 名稱 | 類別 | 描述 |
| ---- | -------- | ----------- |
| [骨料](data-flow-aggregate.md) | 架構修改器 | 定義不同類型的聚合,如 SUM、MIN、MAX 和 COUNT,按現有或計算列分組。 | 
| [變更資料列](data-flow-alter-row.md) | 列變更器 | 在行上設置插入、刪除、更新和向上策略。 |
| [條件式分割](data-flow-conditional-split.md) | 多個輸入/輸出 | 根據匹配條件將數據行路由到不同的流。 |
| [衍生的資料行](data-flow-derived-column.md) | 架構修改器 | 使用數據流表達式語言生成新列或修改現有欄位。 | 
| [存在](data-flow-exists.md) | 多個輸入/輸出 | 檢查數據是否存在於其他源或流中。 | 
| [Filter](data-flow-filter.md) | 列變更器 | 根據條件篩選行。 |
| [扁平化](data-flow-flatten.md) | 架構修改器 |  在分層結構(如 JSON)中獲取陣列值,並將其展開到單獨的行中。 |
| [加入](data-flow-join.md) | 多個輸入/輸出 |  合併來自兩個源或流的數據。 |
| [尋找](data-flow-lookup.md) | 多個輸入/輸出 | 來自另一個源的引用數據。 |
| [新增分支](data-flow-new-branch.md) | 多個輸入/輸出 | 針對同一數據流應用多組操作和轉換。 |
| [樞紐](data-flow-pivot.md) | 架構修改器 | 一個或多個分組列具有將其不同行值轉換為單個列的聚合。 |
| [選取](data-flow-select.md) | 架構修改器 | 別名列與串流名稱,以及刪除或重新排序列 |
| [接收](data-flow-sink.md) | - | 資料的最終目的地 |
| [排序](data-flow-sort.md) | 列變更器 | 在目前資料串流的傳入列排序 |
| [來源](data-flow-source.md) | - | 資料流程資料來源 |
| [代理鍵](data-flow-surrogate-key.md) | 架構修改器 | 新增遞增的非業務的按鍵值 |
| [聯盟](data-flow-union.md) | 多個輸入/輸出 | 垂直組合多個資料串流 |
| [取消樞紐](data-flow-unpivot.md) | 架構修改器 | 將列透視到行值 |
| [視窗](data-flow-window.md) | 架構修改器 |  定義數據流中列的基於視窗的聚合。 |
