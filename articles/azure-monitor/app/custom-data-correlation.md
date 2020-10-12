---
title: Azure Application Insights | Microsoft Docs
description: 將 Application Insights 的資料與其他資料集相互關聯，例如資料擴充或查閱資料表、非 Application Insights 的資料來源和自訂資料。
ms.topic: conceptual
author: eternovsky
ms.author: evternov
ms.date: 08/08/2018
ms.reviewer: mbullwin
ms.openlocfilehash: 17de697686485d695586ffa798196efb4a34c251
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "87092910"
---
# <a name="correlating-application-insights-data-with-custom-data-sources"></a>使自訂資料來源與 Application Insights 資料相互關聯

Application Insights 會收集數個不同的資料類型：例外狀況、追蹤、網頁檢視和其他項目。 雖然這通常足以用來調查您應用程式的效能、可靠性和使用方式，但在某些情況下，將 Application Insights 中儲存的資料與其他完全自訂的資料集相互關聯會很有用。

您需要自訂資料的情況可能包括：

- 資料擴充或查閱資料表：例如，在伺服器名稱中增添伺服器擁有者，以及能在其中找到該伺服器名稱的實驗室位置 
- 與非 Application Insights 資料來源相互關聯：例如，讓網路商店上的購買資料與您的購買履約服務相互關聯，以判斷寄送時間的預估準確度 
- 完全自訂的資料：我們有許多客戶喜愛 Azure 監視器記錄平台 (用來支援 Application Insights) 的查詢語言和效能，而且想要使用它來查詢與 Application Insights 完全不相關的資料。 例如，追蹤屬於智慧型家電安裝的太陽能面板效能，如[此處](https://www.catapultsystems.com/blogs/using-log-analytics-and-a-special-guest-to-forecast-electricity-generation/)所述。

## <a name="how-to-correlate-custom-data-with-application-insights-data"></a>如何使自訂資料與 Application Insights 資料相互關聯 

由於 Application Insights 受到功能強大的 Azure 監視器記錄平台所支援，因此我們能夠使用 Azure 監視器的完整功能來內嵌資料。 然後，我們會使用 "join" 運算子撰寫查詢，此運算子會將此自訂資料與我們在 Azure 監視器記錄中可用的資料相互關聯。 

## <a name="ingesting-data"></a>內嵌資料

在本節中，我們將檢閱如何將您的資料放入 Azure 監視器記錄。

如果您還沒有帳戶，請透過遵循 [這些指示](../learn/quick-collect-azurevm.md) 來布建新的 Log Analytics 工作區，並包含「建立工作區」步驟。

若要開始將記錄資料傳送至 Azure 監視器。 有幾種選項可用：

- 針對同步機制，您可以直接呼叫 [資料收集器 API](../platform/data-collector-api.md) ，或使用我們的邏輯應用程式連線程式–只需尋找「Azure Log Analytics」，然後挑選 [傳送資料] 選項：

  ![選擇和動作的螢幕擷取畫面](./media/custom-data-correlation/01-logic-app-connector.png)  

- 若要使用非同步選項，請使用「資料收集器 API」來建置處理管線。 如需詳細資訊，請參閱 [這篇文章](../platform/create-pipeline-datacollector-api.md) 。

## <a name="correlating-data"></a>使資料相互關聯

Application Insights 會以 Azure 監視器記錄平台作為基礎。 因此我們可以使用[跨資源聯結](../log-query/cross-workspace-query.md)，讓內嵌至 Azure 監視器的所有資料與 Application Insights 資料相互關聯。

例如，我們可以將實驗室清查和位置內嵌至 Log Analytics 工作區（稱為 "myLA"）中名為 "LabLocations_CL" 的資料表。 如果我們接著想要查看在名為 "myAI" 的 Application Insights 應用程式中追蹤的要求，並將提供要求的電腦名稱稱與先前所述的自訂資料表中儲存的這些電腦位置產生關聯，我們可以從 Application Insights 或 Azure 監視器內容執行下列查詢：

```
app('myAI').requests
| join kind= leftouter (
    workspace('myLA').LabLocations_CL
    | project Computer_S, Owner_S, Lab_S
) on $left.cloud_RoleInstance == $right.Computer
```

## <a name="next-steps"></a>後續步驟

- 請參閱[資料收集器 API](../platform/data-collector-api.md) 參考。
- 取得[跨資源聯結](../log-query/cross-workspace-query.md)的詳細資訊。
