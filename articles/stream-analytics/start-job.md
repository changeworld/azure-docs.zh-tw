---
title: 如何啟動 Azure 流分析作業
description: 本文介紹如何從 Azure 門戶、PowerShell 和視覺化工作室啟動流分析作業。
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 04/03/2019
ms.openlocfilehash: c393eb782c2ff16eb5b3e5967b39938dfe2f1534
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "75426474"
---
# <a name="how-to-start-an-azure-stream-analytics-job"></a>如何啟動 Azure 流分析作業

您可以使用 Azure 門戶、視覺化工作室和 PowerShell 啟動 Azure 流分析作業。 啟動作業時，選擇作業開始創建輸出的時間。 Azure 門戶、視覺化工作室和 PowerShell 都有不同的方法來設置開始時間。 這些方法如下所述。

## <a name="start-options"></a>起始選項
以下三個選項可用於啟動作業。 請注意，下面提到的所有時間是[在 TIMESTAMP BY](https://docs.microsoft.com/stream-analytics-query/timestamp-by-azure-stream-analytics)中指定的時間。 如果未指定時間時間，將使用到達時間。
* **現在**：使輸出事件流的起點與作業啟動時相同。 如果使用時態運算子（例如時間視窗、LAG 或 JOIN），Azure 流分析將自動查看輸入源中的資料。 例如，如果您啟動作業"現在"，並且查詢使用 5 分鐘的翻滾視窗，Azure 流分析將在 5 分鐘前在輸入中查找資料。
第一個可能的輸出事件的時間戳記等於或大於目前時間，ASA 保證所有可能邏輯上對輸出作出貢獻的輸入事件都已入帳。 例如，不生成部分視窗聚合。 它始終是完整的聚合值。

* **自訂**：您可以選擇輸出的起點。 與 **"現在"** 選項類似，如果使用時態運算子，Azure 流分析將在此時間之前自動讀取資料 

* **上次停止時**。 此選項在以前啟動作業但手動停止或失敗時可用。 選擇此選項時，Azure 流分析將使用上次輸出時間重新開機作業，以便不會丟失任何資料。 與以前的選項類似，如果使用時態運算子，Azure 流分析將在此時間之前自動讀取資料。 由於多個輸入分區可能有不同的時間，因此使用所有分區的最早停止時間，因此在輸出中可以看到一些重複。 有關一次處理的詳細資訊，請訪問["事件傳遞保證](https://docs.microsoft.com/stream-analytics-query/event-delivery-guarantees-azure-stream-analytics)"頁面。


## <a name="azure-portal"></a>Azure 入口網站

在 Azure 門戶中導航到作業，並在概覽頁上選擇 **"開始**"。 選擇**作業輸出開始時間**，然後選擇 **"開始**"。

選擇**作業輸出開始時間**的選項之一。 選項為 *"現在**"，自訂*，並且，如果作業以前運行，*則上次停止時*。 有關這些選項的詳細資訊，請參閱上文。

## <a name="visual-studio"></a>Visual Studio

在工作檢視中，選擇綠色箭頭按鈕以啟動作業。 設置**作業輸出開始模式**並選擇 **"開始**"。 作業狀態將更改為 **"正在運行**"。

**作業輸出開始模式**有三個選項：*作業開始時間*、*自訂時間和**上次輸出事件時間*。 如果此屬性不存在，則預設值為 *"作業開始時間*"。 有關這些選項的詳細資訊，請參閱上文。


## <a name="powershell"></a>PowerShell

使用以下 Cmdlet 使用 PowerShell 開始作業：

```powershell
Start-AzStreamAnalyticsJob `
  -ResourceGroupName $resourceGroup `
  -Name $jobName `
  -OutputStartMode 'JobStartTime'
```

**輸出啟動模式**有三個選項：*工作開始時間*、*自訂時間和**上次輸出事件時間*。 如果此屬性不存在，則預設值為 *"作業開始時間*"。 有關這些選項的詳細資訊，請參閱上文。

有關`Start-AzStreamAnalyitcsJob`Cmdlet 的詳細資訊，請查看[啟動-AzStreamAnalytics作業參考](/powershell/module/az.streamanalytics/start-azstreamanalyticsjob)。

## <a name="next-steps"></a>後續步驟

* [快速入門：使用 Azure 入口網站建立串流分析作業](stream-analytics-quick-create-portal.md)
* [快速入門：使用 Azure PowerShell 創建流分析作業](stream-analytics-quick-create-powershell.md)
* [快速入門：使用適用於 Visual Studio 的 Azure 串流分析工具建立串流分析作業](stream-analytics-quick-create-vs.md)
