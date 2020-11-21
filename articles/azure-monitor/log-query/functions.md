---
title: Azure 監視器記錄查詢中的函式 | Microsoft Docs
description: 本文說明如何在 Azure 監視器中使用函式從另一個記錄查詢呼叫查詢。
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 07/31/2020
ms.openlocfilehash: 678a6f0dc19d966f3d15e713008c19c8fbb96f5e
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/21/2020
ms.locfileid: "95024359"
---
# <a name="using-functions-in-azure-monitor-log-queries"></a>在 Azure 監視器記錄查詢中使用函式

若要將記錄查詢搭配使用另一個查詢，您可以將它儲存為函式。 這可讓您將複雜的查詢分成多個組件予以簡化，並可讓您重複使用具有多個查詢的通用程式碼。

## <a name="create-a-function"></a>建立函式

按一下 [儲存]，然後提供下表中的資訊，以在 Azure 入口網站中使用記錄分析建立函式。

| 設定 | 說明 |
|:---|:---|
| 名稱           | 在 [查詢總管] 中顯示查詢名稱。 |
| 另存新檔        | 函式 |
| 函式別名 | 簡短名稱，以在其他查詢中使用函式。 不能包含空格，且必須是唯一的。 |
| 類別       | 在 [查詢總管] 用來組織已儲存查詢與函式的類別。 |




## <a name="use-a-function"></a>使用函式
藉由在另一個查詢中包含別名來使用函式。 它可以像任何其他資料表一樣使用。

## <a name="function-parameters"></a>函數參數 
您可以將參數新增至函式，讓您可以在呼叫特定變數時提供這些參數的值。 目前使用參數建立函數的唯一方法是使用 Resource Manager 範本。 如需範例，請參閱 [Azure 監視器中記錄查詢的 Resource Manager 範本範例](../samples/resource-manager-log-queries.md#parameterized-function) 。

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

- [字串作業](/azure/data-explorer/kusto/query/samples?&pivots=azuremonitor#string-operations)
- [日期和時間作業](/azure/data-explorer/kusto/query/samples?&pivots=azuremonitor#date-and-time-operations)
- [彙總函式](/azure/data-explorer/kusto/query/samples?&pivots=azuremonitor#aggregations)
- [進階彙總](/azure/data-explorer/write-queries#advanced-aggregations)
- [JSON 與資料結構](/azure/data-explorer/kusto/query/samples?&pivots=azuremonitor#json-and-data-structures)
- [聯結](/azure/data-explorer/kusto/query/samples?&pivots=azuremonitor#joins)
- [圖表](/azure/data-explorer/kusto/query/samples?&pivots=azuremonitor#charts)
