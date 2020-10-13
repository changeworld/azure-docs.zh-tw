---
title: Azure 監視器 views 中的篩選 |Microsoft Docs
description: Azure 監視器 view 中的篩選器可讓使用者依特定屬性的值篩選視圖中的資料，而不需要修改 view 本身。  本文說明如何使用篩選，以及如何在自訂檢視中新增篩選。
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 06/22/2018
ms.openlocfilehash: d428382493e15d2e0571f4cb4b6f090cf9056fe4
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "91449317"
---
# <a name="filters-in-azure-monitor-views"></a>Azure 監視器 views 中的篩選
[Azure 監視器 view](view-designer.md)中的**篩選器**可讓使用者依特定屬性的值篩選視圖中的資料，而不需要修改 view 本身。  例如，您可以允許檢視的使用者篩選檢視，只顯示來自某個特定電腦或一組電腦的資料。  您可以在單一檢視上建立多個篩選，以允許使用者依多個屬性進行篩選。  本文說明如何使用篩選，以及如何在自訂檢視中新增篩選。

## <a name="using-a-filter"></a>使用篩選
按一下視圖頂端的日期時間範圍，開啟下拉式清單，您可以在其中變更視圖的日期時間範圍。

![Azure 監視器中視圖的 [時間範圍] 下拉式功能表的螢幕擷取畫面，其中顯示選取的 [過去7天] 選項按鈕。](media/view-designer-filters/filters-example-time.png)

按一下 **+** 以使用針對視圖定義的自訂篩選器來新增篩選。 從下拉式清單中選取篩選條件的值或輸入一個值。 按一下，繼續新增篩選 **+** 。 


![在 Azure 監視器中新增自訂篩選之對話方塊的螢幕擷取畫面。 在 [選取屬性] 下拉式功能表中，選取 [電腦] 屬性。](media/view-designer-filters/filters-example-custom.png)

如果您移除篩選的所有值，系統就不會再套用該篩選。


## <a name="creating-a-filter"></a>建立篩選

請在[編輯檢視](view-designer.md)時，從 [篩選]**** 索引標籤建立篩選。  篩選適用於檢視全域，因此會套用至檢視的所有部分。  

![篩選設定](media/view-designer-filters/filters-settings.png)

下表說明篩選的設定。

| 設定 | 描述 |
|:---|:---|
| 欄位名稱 | 用於篩選的欄位名稱。  此欄位必須符合 [ **查詢值**] 中的 [摘要] 欄位。 |
| 查詢值 | 所要執行以填入使用者篩選下拉式清單的查詢。  此查詢必須使用 [摘要](/azure/kusto/query/summarizeoperator) 或 [相異](/azure/kusto/query/distinctoperator) 來提供特定欄位的唯一值，而且必須符合 **功能變數名稱**。  您可以使用 [sort](/azure/kusto/query/sortoperator) 來排序對使用者顯示的值。 |
| Tag | 支援篩選之查詢中使用的欄位名稱，此名稱也會對使用者顯示。 |

### <a name="examples"></a>範例

下表包含一些常見的篩選範例。  

| 欄位名稱 | 查詢值 | Tag |
|:--|:--|:--|
| 電腦   | Heartbeat &#124; distinct Computer &#124; sort by Computer asc | 電腦 |
| EventLevelName | Event &#124; distinct EventLevelName | Severity |
| SeverityLevel | Syslog &#124; distinct SeverityLevel | Severity |
| SvcChangeType | ConfigurationChange &#124; distinct svcChangeType | ChangeType |


## <a name="modify-view-queries"></a>修改檢視查詢

若要讓篩選產生任何效果，您就必須將檢視中的任何查詢修改成依據選取的值進行篩選。  如果您未修改 view 中的任何查詢，則使用者選取的任何值都不會有任何作用。

在查詢中使用篩選值的語法如下： 

`where ${filter name}`  

例如，如果您的視圖有一個查詢會傳回事件並使用名為 [ _電腦_] 的篩選，您可以使用下列查詢。

```kusto
Event | where ${Computers} | summarize count() by EventLevelName
```

如果您新增了名為 Severity 的另一個篩選，則可以使用下列查詢來使用這兩個篩選。

```kusto
Event | where ${Computers} | where ${Severity} | summarize count() by EventLevelName
```

## <a name="next-steps"></a>後續步驟
* 深入了解您可以新增到自訂檢視中的[視覺效果組件](view-designer-parts.md)。
