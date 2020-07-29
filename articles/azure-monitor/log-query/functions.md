---
title: Azure 監視器記錄查詢中的函式 | Microsoft Docs
description: 本文說明如何在 Azure 監視器中使用函式從另一個記錄查詢呼叫查詢。
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 05/18/2018
ms.openlocfilehash: 5fb9e48a6d6a0b95b61478a7877e9b46dd8963e9
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/19/2020
ms.locfileid: "83649388"
---
# <a name="using-functions-in-azure-monitor-log-queries"></a>在 Azure 監視器記錄查詢中使用函式

若要將記錄查詢搭配使用另一個查詢，您可以將它儲存為函式。 這可讓您將複雜的查詢分成多個組件予以簡化，並可讓您重複使用具有多個查詢的通用程式碼。

## <a name="create-a-function"></a>建立函式

按一下 [儲存]，然後提供下表中的資訊，以在 Azure 入口網站中使用記錄分析建立函式。

| 設定 | 描述 |
|:---|:---|
| 名稱           | 在 [查詢總管] 中顯示查詢名稱。 |
| 另存新檔        | 函式 |
| 函式別名 | 簡短名稱，以在其他查詢中使用函式。 不能包含空格，且必須是唯一的。 |
| 類別       | 在 [查詢總管] 用來組織已儲存查詢與函式的類別。 |




## <a name="use-a-function"></a>使用函式
藉由在另一個查詢中包含別名來使用函式。 它可以像任何其他資料表一樣使用。

## <a name="example"></a>範例
下列範例查詢會傳回過去一天內所回報的所有遺漏安全性更新。 將此查詢另存為別名為 _security_updates_last_day_ 的函式。 

```Kusto
Update
| where TimeGenerated > ago(1d) 
| where Classification == "Security Updates" 
| where UpdateState == "Needed"
```

建立另一個查詢及參考 _security_updates_last_day_ 函式，以搜尋所需的 SQL 相關安全性更新。

```Kusto
security_updates_last_day | where Title contains "SQL"
```

## <a name="next-steps"></a>後續步驟
如需了解撰寫 Azure 監視器記錄查詢，請參閱其他課程：

- [字串作業](string-operations.md)
- [日期和時間作業](datetime-operations.md)
- [彙總函式](aggregations.md)
- [進階彙總](advanced-aggregations.md)
- [JSON 與資料結構](json-data-structures.md)
- [聯結](joins.md)
- [圖表](charts.md)
