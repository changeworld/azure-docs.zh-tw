---
title: Azure 監視器 Log Analytics 中的記錄查詢範圍
description: 描述 Azure 監視器 Log Analytics 中的記錄查詢範圍和時間範圍。
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 09/09/2020
ms.openlocfilehash: ddb73de87789934aa66893bdbe6519011cf324b3
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/26/2020
ms.locfileid: "96186094"
---
# <a name="log-query-scope-and-time-range-in-azure-monitor-log-analytics"></a>Azure 監視器 Log Analytics 中的記錄查詢範圍和時間範圍
當您在[Azure 入口網站的 Log Analytics](./log-analytics-tutorial.md)中執行[記錄查詢](log-query-overview.md)時，查詢所評估的資料集會取決於您選取的範圍和時間範圍。 本文說明範圍和時間範圍，以及如何根據您的需求進行設定。 它也會描述不同類型範圍的行為。


## <a name="query-scope"></a>查詢範圍
查詢範圍會定義查詢所評估的記錄。 這通常會在單一 Log Analytics 工作區或 Application Insights 應用程式中包含所有記錄。 Log Analytics 也可讓您設定特定受監視 Azure 資源的範圍。 這可讓資源擁有者只將焦點放在其資料上，即使該資源寫入至多個工作區也一樣。

範圍一律會顯示在 [Log Analytics] 視窗的左上角。 圖示會指出範圍是 Log Analytics 工作區或 Application Insights 應用程式。 無圖示表示另一個 Azure 資源。

![顯示在入口網站中的範圍](media/scope/scope.png)

範圍取決於您用來啟動 Log Analytics 的方法，而且在某些情況下，您可以按一下它來變更範圍。 下表列出使用的不同範圍類型，以及每個類型的不同詳細資料。

> [!IMPORTANT]
> 如果您在 Application Insights 中使用以工作區為基礎的應用程式，則會將其資料儲存在 Log Analytics 工作區中，並包含所有其他記錄資料。 為了回溯相容性，您將會在選取應用程式作為範圍時取得傳統 Application Insights 體驗。 若要在 Log Analytics 工作區中查看這項資料，請將範圍設定為工作區。

| 查詢範圍 | 範圍中的記錄 | 如何選取 | 變更範圍 |
|:---|:---|:---|:---|
| Log Analytics 工作區 | Log Analytics 工作區中的所有記錄。 | 從 [ **Azure 監視器**] 功能表或 [ **Log Analytics 工作區**] 功能表選取 [**記錄**]。  | 可以將範圍變更為任何其他資源類型。 |
| Application Insights 應用程式 | Application Insights 應用程式中的所有記錄。 | 從應用程式的 [ **Application Insights** ] 功能表中選取 [**記錄** 檔]。 | 只能將範圍變更為另一個 Application Insights 應用程式。 |
| 資源群組 | 資源群組中的所有資源所建立的記錄。 可能包含來自多個 Log Analytics 工作區的資料。 | 從 [資源群組] 功能表選取 [ **記錄** ]。 | 無法變更範圍。|
| 訂用帳戶 | 訂用帳戶中的所有資源所建立的記錄。 可能包含來自多個 Log Analytics 工作區的資料。 | 從 [訂用帳戶] 功能表選取 [ **記錄** ]。   | 無法變更範圍。 |
| 其他 Azure 資源 | 資源所建立的記錄。 可能包含來自多個 Log Analytics 工作區的資料。  | 選取 [資源] 功能表中的 [ **記錄** ]。<br>OR<br>從 **Azure 監視器**] 功能表選取 [**記錄**]，然後選取新的範圍。 | 只能將範圍變更為相同的資源類型。 |

### <a name="limitations-when-scoped-to-a-resource"></a>範圍設定為資源時的限制

當查詢範圍是 Log Analytics 工作區或 Application Insights 應用程式時，入口網站中的所有選項和所有查詢命令都可以使用。 不過，當範圍設定為資源時，入口網站中的下列選項無法使用，因為它們是與單一工作區或應用程式相關聯：

- 儲存
- 查詢總管
- 新增警示規則

當查詢範圍已經包含任何具有該資源或資源集資料的工作區時，您無法在查詢中使用下列命令：

- [app](app-expression.md)
- [工作](workspace-expression.md)
 

## <a name="query-scope-limits"></a>查詢範圍限制
將範圍設定為資源或資源集是 Log Analytics 的一項特別強大的功能，因為它可讓您在單一查詢中自動合併分散式資料。 但是，如果需要跨多個 Azure 區域的工作區抓取資料，它可能會大幅影響效能。

Log Analytics 可在使用特定數量的區域時發出警告或錯誤，以協助防止跨多個區域中工作區的查詢過度負荷。 如果範圍包含5個或更多個區域中的工作區，您的查詢就會收到警告。 它仍會執行，但可能需要花費太多時間才能完成。

![查詢警告](media/scope/query-warning.png)

如果範圍包含20個或更多個區域中的工作區，您的查詢將會遭到封鎖而無法執行。 在此情況下，系統會提示您減少工作區區域的數目，並嘗試再次執行查詢。 下拉式清單會顯示查詢範圍內的所有區域，您應該在嘗試再次執行查詢之前，減少區域數目。

![查詢失敗](media/scope/query-failed.png)


## <a name="time-range"></a>時間範圍
時間範圍會指定根據建立記錄時，針對查詢評估的一組記錄。 這是由工作區或應用程式中每一筆記錄的 **TimeGenerated** 資料行所定義，如下表所指定。 若是傳統的 Application Insights 應用程式，時間範圍會使用 **時間戳記** 資料行。


從 Log Analytics 視窗頂端的時間選擇器選取時間範圍，以設定時間範圍。  您可以選取預先定義的期間，或選取 [ **自訂** ] 來指定特定的時間範圍。

![時間選擇器](media/scope/time-picker.png)

如果您在使用如上表所示的標準時間資料行的查詢中設定篩選準則，時間選擇器會變更為 **在查詢中設定**，且時間選擇器會停用。 在此情況下，將篩選準則放在查詢頂端最有效率，讓任何後續的處理只需要使用篩選的記錄。

![篩選的查詢](media/scope/query-filtered.png)

如果您使用 [ [工作區](workspace-expression.md) ] 或 [ [應用程式](app-expression.md) ] 命令從另一個工作區或傳統應用程式取出資料，時間選擇器可能會有不同的行為。 如果範圍是 Log Analytics 工作區，而且您使用 **應用** 程式，或者範圍是傳統 Application Insights 應用程式且您使用 **工作區**，則 Log Analytics 可能不了解篩選中使用的資料行應該決定時間篩選準則。

在下列範例中，範圍設定為 Log Analytics 工作區。  此查詢會使用 **工作區** 來從另一個 Log Analytics 工作區中取出資料。 時間選擇器會變更為 **在查詢中設定** ，因為它會看到使用預期的 **TimeGenerated** 資料行的篩選。

![使用工作區查詢](media/scope/query-workspace.png)

如果查詢使用 **應用** 程式從傳統的 Application Insights 應用程式取出資料，Log Analytics 無法辨識篩選中的 **時間戳記** 資料行，時間選擇器會維持不變。 在此情況下，會套用這兩個篩選準則。 在此範例中，只有在過去24小時內建立的記錄會包含在查詢中，即使它在 **where** 子句中指定了7天也一樣。

![使用應用程式查詢](media/scope/query-app.png)

## <a name="next-steps"></a>後續步驟

- 逐步解說在 [Azure 入口網站中使用 Log Analytics 的教學](./log-analytics-tutorial.md)課程。
- 逐步解說 [撰寫查詢的教學](get-started-queries.md)課程。