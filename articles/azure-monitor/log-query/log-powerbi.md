---
title: Log Analytics 與 Power BI 和 Excel 的整合
description: 如何將 Log Analytics 的結果傳送給 Power BI
ms.subservice: logs
ms.topic: conceptual
author: roygalMS
ms.author: roygal
ms.date: 11/03/2020
ms.openlocfilehash: a4e2faf87ac3dddf91e6945343a46b02df72db0a
ms.sourcegitcommit: 4bee52a3601b226cfc4e6eac71c1cb3b4b0eafe2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/11/2020
ms.locfileid: "94507463"
---
# <a name="log-analytics-integration-with-power-bi"></a>Log Analytics 與 Power BI 整合

本文著重于將資料從 Log Analytics 送入 Microsoft Power BI 的方式，以建立更具視覺效果的報表和儀表板。 

## <a name="background"></a>背景 

Azure 監視器記錄是一種平臺，可為擷取記錄提供端對端解決方案。 [Azure 監視器 Log Analytics](../platform/data-platform.md#) 是查詢這些記錄檔的介面。 如需整個 Azure 監視器資料平臺（包括 Log Analytics）的詳細資訊，請參閱 [Azure 監視器資料平臺](../platform/data-platform.md)。 

Microsoft Power BI 是 Microsoft 的資料視覺效果平臺。 如需如何開始使用的詳細資訊，請參閱 [Power BI 的首頁](https://powerbi.microsoft.com/)。 


一般而言，您可以使用免費的 Power BI 功能來整合和建立視覺效果吸引人的報表和儀表板。

更先進的功能可能需要購買 Power BI Pro 或 premium 帳戶。 這些功能包括： 
 - 共用您的工作 
 - 排程重新整理
 - Power BI 應用程式 
 - 資料流程和累加式重新整理 

如需詳細資訊，請參閱 [深入瞭解 Power BI 定價和功能](https://powerbi.microsoft.com/pricing/) 

## <a name="integrating-queries"></a>整合查詢  

Power BI 使用 [M 查詢語言](/powerquery-m/power-query-m-language-specification/) 做為其主要查詢語言。 

您可以將 Log Analytics 查詢匯出至 M，並直接在 Power BI 中使用。 執行成功的查詢之後，請從 Log Analytics UI 頂端動作列中的 [ **匯出** ] 按鈕，選取 [ **匯出至 Power BI] (M 查詢)** 。

:::image type="content" source="media/log-powerbi-excel/export-query2.png" alt-text="顯示匯出選項下拉式功能表下拉式的 Log Analytics 查詢" border="true":::

Log Analytics 會建立一個 .txt 檔案，其中包含可直接在 Power BI 中使用的 M 程式碼。

## <a name="connecting-your-logs-to-a-dataset"></a>將記錄連接至資料集 

Power BI 資料集是可供報表和視覺效果使用的資料來源。 若要將 Log Analytics 查詢連接至資料集，請將從 Log Analytics 匯出的 M 程式碼複製到 Power BI 中的空白查詢。 

如需詳細資訊，請參閱 [瞭解 Power BI 資料集](/power-bi/service-datasets-understand/)。 

## <a name="collect-data-with-power-bi-dataflows"></a>使用 Power BI 資料流程收集資料 

Power BI 資料流程也可讓您收集和儲存資料。 如需詳細資訊，請參閱 [Power BI 資料流程](/power-bi/service-dataflows-overview)。

資料流程是「雲端 ETL」的一種類型，其設計目的是協助您收集和準備您的資料。 資料集是「模型」，其設計目的是要協助您連接不同的實體並建立它們的模型，以滿足您的需求。

## <a name="incremental-refresh"></a>累加式重新整理 

Power BI 資料集和 Power BI 資料流程都有累加式重新整理選項。 Power BI 資料流程和 Power BI 資料集支援這項功能，但您需要 Power BI Premium 才能使用它。  


累加式重新整理會在每次執行時執行小型查詢並更新較少量的資料，而不會在您執行查詢時再次擷取所有資料。 您可以選擇儲存大量的資料，但每次執行查詢時，都會加入新的資料增量。 此行為適用于長時間執行的報表。

Power BI 累加式重新整理會依賴結果集內的 *日期時間* 欄位是否存在。 設定累加式重新整理之前，請確定您的 Log Analytics 查詢結果集至少包含一個 *日期時間* 欄位。 

若要深入瞭解及如何設定累加式重新整理，請參閱 [Power BI 資料集和累加式](/power-bi/service-premium-incremental-refresh) 重新整理，以及 [Power BI 資料流程和累加式](/power-bi/service-dataflows-incremental-refresh)重新整理。

## <a name="reports-and-dashboards"></a>報表和儀表板

將資料傳送至 Power BI 之後，您可以繼續使用 Power BI 來建立報表和儀表板。

如需詳細資訊，請參閱 [本指南，以瞭解如何建立您的第一個 Power BI 模型和報告](/learn/modules/build-your-first-power-bi-report/)。  

## <a name="excel-integration"></a>Excel 整合

您可以使用 Power BI 中使用的相同 M 整合，與 Excel 試算表整合。 如需詳細資訊，請參閱本 [指南中的如何整合 excel](https://support.microsoft.com/office/import-data-from-external-data-sources-power-query-be4330b3-5356-486c-a168-b68e9e616f5a) ，然後貼上從 Log Analytics 匯出的 M 查詢。

您可以在[整合 Log Analytics 和 Excel](log-excel.md)中找到其他資訊

## <a name="next-steps"></a>後續步驟

開始使用 [Log Analytics 查詢](log-query-overview.md)。
