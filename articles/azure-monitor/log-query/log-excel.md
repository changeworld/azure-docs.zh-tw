---
title: 整合 Log Analytics 和 Excel
description: 在 excel 中取得 Log Analytics 查詢並重新整理結果。
ms.subservice: logs
ms.topic: conceptual
author: roygalMS
ms.author: roygal
ms.date: 11/03/2020
ms.openlocfilehash: d903d1bb16ba3576d0092979f1cc6b82fac1c0be
ms.sourcegitcommit: 4bee52a3601b226cfc4e6eac71c1cb3b4b0eafe2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/11/2020
ms.locfileid: "94507474"
---
# <a name="integrate-log-analytics-and-excel"></a>整合 Log Analytics 和 Excel

您可以使用 M 查詢與 Log Analytics API 來整合 Azure 監視器 Log Analytics 和 Microsoft Excel。  這種整合可讓您將500000記錄傳送至 Excel。

> [!NOTE]
> 由於 Excel 是本機用戶端應用程式，因此本機硬體和軟體限制會影響其效能以及處理大型資料集的能力。

## <a name="create-your-m-query-in-log-analytics"></a>在 Log Analytics 中建立您的 M 查詢 

1. 以平常的方式在 Log analytics 中 **建立並執行您的查詢** 。 如果您在使用者介面中遇到10000記錄的限制，請不要擔心。  建議您使用相對日期（例如 ' 前 ' 函式或 UI 時間選擇器），讓 Excel 重新整理正確的資料集。
  
2. **匯出查詢** -對查詢及其結果感到滿意之後，請使用 [ *匯出* ] 功能表下的 [Log Analytics 匯出] 將查詢匯出至 M， **以 Power BI (M 查詢)** 功能表選項：

:::image type="content" source="media/log-excel/export-query.png" alt-text="具有資料和匯出選項的 Log Analytics 查詢" border="true":::



選擇這個選項會下載包含您可以在 Excel 中使用之 M 程式碼的 .txt 檔案。

以上所示的查詢會匯出下列 M 程式碼。 以下範例是在我們的範例中為查詢匯出的 M 程式碼：

```m
/*
The exported Power Query Formula Language (M Language ) can be used with Power Query in Excel
and Power BI Desktop.
For Power BI Desktop follow the instructions below: 
1) Download Power BI Desktop from https://powerbi.microsoft.com/desktop/
2) In Power BI Desktop select: 'Get Data' -> 'Blank Query'->'Advanced Query Editor'
3) Paste the M Language script into the Advanced Query Editor and select 'Done'
*/

let AnalyticsQuery =
let Source = Json.Document(Web.Contents("https://api.loganalytics.io/v1/workspaces/ddcfc599-cae0-48ee-9026-fffffffffffff/query", 
[Query=[#"query"="

Heartbeat 
| summarize dcount(ComputerIP) by bin(TimeGenerated, 1h)    
| render timechart
",#"x-ms-app"="OmsAnalyticsPBI",#"timespan"="P1D",#"prefer"="ai.response-thinning=true"],Timeout=#duration(0,0,4,0)])),
TypeMap = #table(
{ "AnalyticsTypes", "Type" }, 
{ 
{ "string",   Text.Type },
{ "int",      Int32.Type },
{ "long",     Int64.Type },
{ "real",     Double.Type },
{ "timespan", Duration.Type },
{ "datetime", DateTimeZone.Type },
{ "bool",     Logical.Type },
{ "guid",     Text.Type },
{ "dynamic",  Text.Type }
}),
DataTable = Source[tables]{0},
Columns = Table.FromRecords(DataTable[columns]),
ColumnsWithType = Table.Join(Columns, {"type"}, TypeMap , {"AnalyticsTypes"}),
Rows = Table.FromRows(DataTable[rows], Columns[name]), 
Table = Table.TransformColumnTypes(Rows, Table.ToList(ColumnsWithType, (c) => { c{0}, c{3}}))
in
Table
in AnalyticsQuery
```

## <a name="connect-query-to-excel"></a>將查詢連接到 Excel 

匯入查詢。 

1. 開啟 Microsoft Excel。 
1. 在功能區中，移至 [ **資料** ] 功能表。 選取 [ **取得資料** ]。 從 **其他來源** 選取 [ **空白查詢** ]：
 
   :::image type="content" source="media/log-excel/excel-import-blank-query.png" alt-text="在 Excel 中從空白匯入選項" border="true":::

1. 在 [Power query] 視窗中，選取 [ **Advanced editor** ]：

   :::image type="content" source="media/log-excel/advanced-editor.png" alt-text="Excel Advanced query 編輯器" border="true":::

 
1. 將 advanced editor 中的文字取代為從 Log Analytics 匯出的查詢：

   :::image type="content" source="media/log-excel/advanced-editor-2.png" alt-text="建立空白查詢" border="true":::
 
1. 選取 [ **完成** ]，然後 **載入和關閉** 。 Excel 會使用 Log analytics API 來執行查詢，然後顯示結果集。
 

   :::image type="content" source="media/log-excel/excel-query-result.png" alt-text="Excel 中的查詢結果" border="true":::

##  <a name="refreshing--data"></a>重新整理資料

您可以直接從 Excel 重新整理資料。 在 Excel 功能區的 [ **資料** ] 功能表群組中，選取 [重新整理 **] 按鈕。**
 
## <a name="next-steps"></a>後續步驟

如需 Excel 與外部資料源整合的詳細資訊，請參閱將 [資料從外部資料源匯入 (Power Query) ](https://support.office.com/article/import-data-from-external-data-sources-power-query-be4330b3-5356-486c-a168-b68e9e616f5a)