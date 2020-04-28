---
title: 如何啟動 Azure 串流分析作業
description: 本文說明如何從 Azure 入口網站、PowerShell 和 Visual Studio 啟動串流分析作業。
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 04/03/2019
ms.openlocfilehash: c393eb782c2ff16eb5b3e5967b39938dfe2f1534
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "75426474"
---
# <a name="how-to-start-an-azure-stream-analytics-job"></a>如何啟動 Azure 串流分析作業

您可以使用 Azure 入口網站、Visual Studio 和 PowerShell 來啟動 Azure 串流分析作業。 當您啟動作業時，您會選取作業開始建立輸出的時間。 Azure 入口網站、Visual Studio 和 PowerShell 各有不同的方法來設定開始時間。 這些方法如下所述。

## <a name="start-options"></a>起始選項
下列三個選項可用來啟動作業。 請注意，以下所述的所有時間都是 [[時間戳記依據]](https://docs.microsoft.com/stream-analytics-query/timestamp-by-azure-stream-analytics)中指定的。 如果未指定 TIMESTAMP BY，則會使用抵達時間。
* **Now**：使輸出事件資料流程的起點與啟動作業時相同。 如果使用時態運算子（例如時間範圍、LAG 或 JOIN），Azure 串流分析會自動回頭查看輸入來源中的資料。 例如，如果您啟動「立即」作業，而且如果您的查詢使用5分鐘的輪轉視窗，Azure 串流分析會在輸入的5分鐘前尋找資料。
第一個可能的輸出事件會有等於或大於目前時間的時間戳記，而 ASA 保證所有可能會對輸出進行邏輯貢獻的輸入事件都已納入考慮。 例如，不會產生部分視窗的匯總。 它一律是完整的匯總值。

* **Custom**：您可以選擇輸出的起始點。 類似于**Now**選項，Azure 串流分析會在這段時間之前自動讀取資料（如果使用時態運算子） 

* **上次停止的時間**。 此選項適用于先前已啟動作業，但已手動停止或失敗的情況。 選擇此選項時，Azure 串流分析會使用最後的輸出時間重新開機作業，因此不會遺失任何資料。 與先前的選項類似，Azure 串流分析會在這段時間之前自動讀取資料（如果使用時態運算子）。 因為有數個輸入資料分割可能會有不同的時間，所以會使用所有資料分割的最早停止時間，因此可能會在輸出中看到一些重複專案。 只要有一次處理的詳細資訊，就可以在「[事件傳遞保證](https://docs.microsoft.com/stream-analytics-query/event-delivery-guarantees-azure-stream-analytics)」頁面上取得。


## <a name="azure-portal"></a>Azure 入口網站

在 Azure 入口網站中流覽至您的作業，然後選取 [總覽] 頁面上的 [**開始**]。 選取**作業輸出開始時間**，然後選取 [**啟動**]。

選擇 [**作業輸出開始時間**] 的其中一個選項。 選項包括 [*現在*]、[*自訂*]，以及*上次停止時*的作業（如果先前已執行）。 如需這些選項的詳細資訊，請參閱上面的。

## <a name="visual-studio"></a>Visual Studio

在工作視圖中，選取綠色箭號按鈕來啟動作業。 設定**作業輸出啟動模式**，然後選取 [**啟動**]。 作業狀態會變更為 [**正在**執行]。

**作業輸出啟動模式**有三個選項： *JobStartTime*、 *CustomTime*和*LastOutputEventTime*。 如果此屬性不存在，則預設值為*JobStartTime*。 如需這些選項的詳細資訊，請參閱上面的。


## <a name="powershell"></a>PowerShell

使用下列 Cmdlet，以使用 PowerShell 啟動您的作業：

```powershell
Start-AzStreamAnalyticsJob `
  -ResourceGroupName $resourceGroup `
  -Name $jobName `
  -OutputStartMode 'JobStartTime'
```

**OutputStartMode**有三個選項： *JobStartTime*、 *CustomTime*和*LastOutputEventTime*。 如果此屬性不存在，則預設值為*JobStartTime*。 如需這些選項的詳細資訊，請參閱上面的。

如需`Start-AzStreamAnalyitcsJob` Cmdlet 的詳細資訊，請參閱[AzStreamAnalyticsJob 參考](/powershell/module/az.streamanalytics/start-azstreamanalyticsjob)。

## <a name="next-steps"></a>後續步驟

* [快速入門：使用 Azure 入口網站建立串流分析作業](stream-analytics-quick-create-portal.md)
* [快速入門：使用 Azure PowerShell 建立串流分析作業](stream-analytics-quick-create-powershell.md)
* [快速入門：使用適用於 Visual Studio 的 Azure 串流分析工具建立串流分析作業](stream-analytics-quick-create-vs.md)
