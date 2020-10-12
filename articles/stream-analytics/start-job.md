---
title: 如何啟動 Azure 串流分析作業
description: 本文說明如何從 Azure 入口網站、PowerShell 和 Visual Studio 啟動串流分析工作。
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: how-to
ms.date: 04/03/2019
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: bce1f00603d9304fc544fb92d4cc5f1a429504ed
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "89068995"
---
# <a name="how-to-start-an-azure-stream-analytics-job"></a>如何啟動 Azure 串流分析作業

您可以使用 Azure 入口網站、Visual Studio 和 PowerShell 來啟動 Azure 串流分析作業。 當您啟動作業時，您會選取作業開始建立輸出的時間。 Azure 入口網站、Visual Studio 和 PowerShell 各有不同的方法來設定開始時間。 這些方法如下所述。

## <a name="start-options"></a>起始選項
您可以使用下列三個選項來啟動作業。 請注意，以下所述的所有時間都是 [TIMESTAMP BY](https://docs.microsoft.com/stream-analytics-query/timestamp-by-azure-stream-analytics)中所指定的時間。 如果未指定 TIMESTAMP BY，則會使用抵達時間。
* **現在**：讓輸出事件資料流程的起點與作業啟動時相同。 如果使用時態運算子 (例如時間範圍、延遲或聯結) ，Azure 串流分析會自動查看輸入來源中的資料。 比方說，如果您啟動 "Now" 作業，而且查詢使用5分鐘的輪轉視窗，則 Azure 串流分析會在輸入中搜尋5分鐘前的資料。
第一個可能的輸出事件的時間戳記等於或大於目前的時間，而 ASA 保證所有可能以邏輯方式參與輸出的輸入事件都已列入考慮。 例如，不會產生任何部分視窗化匯總。 它一律是完整的匯總值。

* **自訂**：您可以選擇輸出的起點。 同樣地，如果使用時態運算子，Azure 串流分析也 **會在這** 段時間之前自動讀取資料 

* **上次停止時**。 此選項適用于先前已啟動的工作，但已手動停止或失敗。 當您選擇此選項時，Azure 串流分析會使用最後的輸出時間來重新開機作業，因此不會遺失任何資料。 類似于先前的選項，如果使用時態運算子，Azure 串流分析會在此時間之前自動讀取資料。 由於有數個輸入資料分割可能會有不同的時間，因此會使用所有資料分割的最早停止時間，因此可能會在輸出中看到一些重複專案。 您可以在頁面 [事件傳遞保證](https://docs.microsoft.com/stream-analytics-query/event-delivery-guarantees-azure-stream-analytics)上取得精確處理一次的詳細資訊。


## <a name="azure-portal"></a>Azure 入口網站

在 Azure 入口網站中流覽至您的作業，然後選取 [總覽] 頁面上的 [ **開始** ]。 選取 **作業輸出開始時間** ，然後選取 [ **啟動**]。

選擇 [ **工作輸出開始時間**] 的其中一個選項。 這些選項 *現在*是 *自訂*的，如果是先前  *已停止*的作業，則為。 如需這些選項的詳細資訊，請參閱上述內容。

## <a name="visual-studio"></a>Visual Studio

在工作檢視中，選取綠色箭號按鈕來啟動作業。 設定 **作業輸出啟動模式** ，然後選取 [ **啟動**]。 作業狀態將會變更為 [ **正在**執行]。

**作業輸出啟動模式**有三個選項： *JobStartTime*、 *CustomTime*和*LastOutputEventTime*。 如果這個屬性不存在，則預設值為 *JobStartTime*。 如需這些選項的詳細資訊，請參閱上述內容。


## <a name="powershell"></a>PowerShell

使用下列 Cmdlet，以使用 PowerShell 來啟動您的作業：

```powershell
Start-AzStreamAnalyticsJob `
  -ResourceGroupName $resourceGroup `
  -Name $jobName `
  -OutputStartMode 'JobStartTime'
```

**OutputStartMode**有三個選項： *JobStartTime*、 *CustomTime*和*LastOutputEventTime*。 如果這個屬性不存在，則預設值為 *JobStartTime*。 如需這些選項的詳細資訊，請參閱上述內容。

如需此 Cmdlet 的詳細資訊 `Start-AzStreamAnalyitcsJob` ，請參閱 [開始 AzStreamAnalyticsJob 參考](/powershell/module/az.streamanalytics/start-azstreamanalyticsjob)。

## <a name="next-steps"></a>後續步驟

* [快速入門：使用 Azure 入口網站建立串流分析作業](stream-analytics-quick-create-portal.md)
* [快速入門：使用 Azure PowerShell 建立串流分析作業](stream-analytics-quick-create-powershell.md)
* [快速入門：使用適用於 Visual Studio 的 Azure 串流分析工具建立串流分析作業](stream-analytics-quick-create-vs.md)
