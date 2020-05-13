---
title: Azure 監視器 Log Analytics 中的記錄查詢範圍 |Microsoft Docs
description: 描述 Azure 監視器 Log Analytics 中記錄查詢的範圍和時間範圍。
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 05/01/2020
ms.openlocfilehash: 2840e5b8ff16d44f76aaafcf68264c65e4401ff7
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/12/2020
ms.locfileid: "83199019"
---
# <a name="log-query-scope-and-time-range-in-azure-monitor-log-analytics"></a>Azure 監視器 Log Analytics 中的記錄查詢範圍和時間範圍
當您在[Azure 入口網站的 Log Analytics](get-started-portal.md)中執行[記錄查詢](log-query-overview.md)時，查詢所評估的資料集取決於您選取的範圍和時間範圍。 本文說明範圍和時間範圍，以及如何根據您的需求進行設定。 它也會描述不同類型範圍的行為。


## <a name="query-scope"></a>查詢範圍
查詢範圍會定義查詢所評估的記錄。 這通常會將所有記錄包含在單一 Log Analytics 工作區或 Application Insights 應用程式中。 Log Analytics 也可讓您設定特定受監視 Azure 資源的範圍。 這可讓資源擁有者僅專注于其資料，即使該資源寫入至多個工作區也一樣。

範圍一律會顯示在 [Log Analytics] 視窗的左上角。 圖示會指出範圍是 Log Analytics 工作區還是 Application Insights 應用程式。 沒有圖示表示另一個 Azure 資源。

![影響範圍](media/scope/scope.png)

範圍取決於您用來啟動 Log Analytics 的方法，在某些情況下，您可以按一下來變更範圍。 下表列出所使用的不同範圍類型，以及每個類別的不同詳細資料。

> [!IMPORTANT]
> 如果您使用 APM 2.1，則 Application Insights 應用程式會儲存在 Log Analytics 工作區中，並包含所有其他記錄檔資料，且 Application Insights 範圍無法使用。 如果您從 [Application Insights] 功能表中選取 [**記錄**]，則其作用會與**其他 Azure 資源**範圍相同，而且只有 Application Insights 資料表中該應用程式的資料可供使用。

| 查詢範圍 | 範圍中的記錄 | 如何選取 | 變更範圍 |
|:---|:---|:---|:---|
| Log Analytics 工作區 | Log Analytics 工作區中的所有記錄。 | 從 [ **Azure 監視器**] 功能表或 [ **Log Analytics 工作區**] 功能表中選取 [**記錄**]。  | 可以將範圍變更為任何其他資源類型。 |
| Application Insights 應用程式 | Application Insights 應用程式中的所有記錄。 | 從 Application Insights 的 **[總覽**] 頁面選取 [**分析**]。 | 只能將範圍變更為另一個 Application Insights 應用程式。 |
| 資源群組 | 資源群組中所有資源所建立的記錄。 可能包含來自多個 Log Analytics 工作區的資料。 | 從 [資源群組] 功能表中選取 [**記錄**]。 | 無法變更範圍。|
| 訂用帳戶 | 訂用帳戶中的所有資源所建立的記錄。 可能包含來自多個 Log Analytics 工作區的資料。 | 從 [訂用帳戶] 功能表中選取 [**記錄**]。   | 無法變更範圍。 |
| 其他 Azure 資源 | 資源所建立的記錄。 可能包含來自多個 Log Analytics 工作區的資料。  | 從 [資源] 功能表中選取 [**記錄**]。<br>OR<br>從 [ **Azure 監視器**] 功能表中選取 [**記錄**]，然後選取新的範圍。 | 只能將範圍變更為相同的資源類型。 |

### <a name="limitations-when-scoped-to-a-resource"></a>範圍限定于資源的限制

當查詢範圍是 Log Analytics 工作區或 Application Insights 應用程式時，入口網站和所有查詢命令中的所有選項都可以使用。 雖然範圍設定為資源，但入口網站中的下列選項無法使用，因為它們與單一工作區或應用程式相關聯：

- 儲存
- 查詢總管
- 新增警示規則

當範圍設定為資源時，您無法在查詢中使用下列命令，因為查詢範圍已經包含具有該資源或資源集之資料的任何工作區：

- [app](app-expression.md)
- [區域](workspace-expression.md)
 

## <a name="query-limits"></a>查詢限制
您可能有 Azure 資源的商務需求，可將資料寫入多個 Log Analytics 工作區。 工作區不需要位於與資源相同的區域中，且單一工作區可能會從各種區域的資源收集資料。  

將範圍設定為資源或資源集是 Log Analytics 中特別強大的功能，因為它可讓您在單一查詢中自動合併分散式資料。 不過，如果需要從多個 Azure 區域的工作區中抓取資料，它可能會大幅影響效能。

Log Analytics 會在使用特定數目的區域時發出警告或錯誤，協助防止跨多個區域中的工作區的查詢過度負荷。 如果範圍包含5個或更多區域中的工作區，您的查詢將會收到警告。 它仍然會執行，但可能需要太多時間才能完成。

![查詢警告](media/scope/query-warning.png)

如果範圍包含20個或更多區域中的工作區，您的查詢將會遭到封鎖而無法執行。 在此情況下，系統會提示您減少工作區區域的數目，並嘗試再次執行查詢。 下拉式清單會顯示查詢範圍中的所有區域，您應該先減少區域數目，再嘗試再次執行查詢。

![查詢失敗](media/scope/query-failed.png)


## <a name="time-range"></a>時間範圍
時間範圍會根據建立記錄時，指定針對查詢評估的一組記錄。 這是由工作區或應用程式中每一筆記錄的標準屬性所定義，如下表所指定。

| Location | 屬性 |
|:---|:---|
| Log Analytics 工作區          | TimeGenerated |
| Application Insights 應用程式 | timestamp     |

從 [Log Analytics] 視窗頂端的 [時間選擇器] 中選取時間範圍，即可加以設定。  您可以選取預先定義的期間，或選取 [**自訂**] 來指定特定的時間範圍。

![時間選擇器](media/scope/time-picker.png)

如果您在使用 [標準時間] 屬性的查詢中設定篩選（如上表所示），時間選擇器會變更為 [**在查詢中設定**]，而 [時間選擇器] 則會停用。 在此情況下，將篩選準則放在查詢頂端最有效率，如此一來，任何後續的處理都只需要使用篩選過的記錄。

![篩選的查詢](media/scope/query-filtered.png)

如果您使用 [[工作區](workspace-expression.md)] 或 [[應用](app-expression.md)程式] 命令從另一個工作區或應用程式抓取資料，時間選擇器的行為可能會不同。 如果範圍是 Log Analytics 工作區，而且您使用**應用**程式，或如果範圍是 Application Insights 的應用程式，而且您使用**工作區**，則 Log Analytics 可能無法瞭解篩選器中使用的屬性應該決定時間篩選準則。

在下列範例中，範圍會設定為 Log Analytics 工作區。  查詢會使用**工作區**來抓取另一個 Log Analytics 工作區中的資料。 時間選擇器會變更為 [**在查詢中設定**]，因為它會看到使用預期**TimeGenerated**屬性的篩選準則。

![使用工作區查詢](media/scope/query-workspace.png)

如果查詢使用**應用**程式從 Application Insights 應用程式抓取資料，則 Log Analytics 無法辨識篩選中的**時間戳記**屬性，而時間選擇器會保持不變。 在此情況下，會套用這兩個篩選準則。 在此範例中，查詢中只會包含過去24小時內建立的記錄，即使在**where**子句中指定了7天。

![使用應用程式查詢](media/scope/query-app.png)

## <a name="next-steps"></a>後續步驟

- 逐步解說在[Azure 入口網站中使用 Log Analytics 的教學](get-started-portal.md)課程。
- 逐步解說[撰寫查詢的教學](get-started-queries.md)課程。