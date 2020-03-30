---
title: Azure 監視器警報的操作規則
description: 瞭解 Azure 監視器中的操作規則以及如何配置和管理它們。
ms.topic: conceptual
ms.date: 04/25/2019
ms.subservice: alerts
ms.openlocfilehash: 42f8d9cd30caa48376cda049f6404aa897a6866c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "77668463"
---
# <a name="action-rules-preview"></a>操作規則（預覽）

操作規則可説明您在任何 Azure 資源管理器範圍（Azure 訂閱、資源組或目標資源）上定義或禁止操作。 它們具有各種篩選器，可説明您縮小要操作的特定警報實例子集。

## <a name="why-and-when-should-you-use-action-rules"></a>為什麼要和何時使用操作規則？

### <a name="suppression-of-alerts"></a>抑制警報

在很多情況下，禁止顯示警報生成的通知非常有用。 這些方案的範圍從計畫維護時段期間的抑制到非工作時間的抑制。 例如，負責**ContosoVM**的團隊希望禁止下週末的警報通知，因為**ContosoVM**正在進行計畫維護。 

儘管團隊可以手動禁用**ContosoVM**上配置的每個警報規則（並在維護後再次啟用），但這不是一個簡單的過程。 操作規則可説明您定義大規模警報抑制，並能夠靈活配置抑制週期。 在前面的示例中，團隊可以在**ContosoVM**上定義一個操作規則，該規則禁止週末的所有警報通知。


### <a name="actions-at-scale"></a>規模行動

儘管警報規則可説明您定義生成警報時觸發的操作組，但客戶通常在其操作範圍內具有一個通用操作組。 例如，負責資源組**ContosoRG**的團隊可能會為**ContosoRG**中定義的所有警報規則定義相同的操作組。 

操作規則可説明您簡化此過程。 通過大規模定義操作，可以為在配置的作用域上生成的任何警報觸發操作組。 在前面的示例中，團隊可以在**ContosoRG**上定義一個操作規則，該規則將觸發其內生成的所有警報的相同操作組。

> [!NOTE]
> 操作規則當前不適用於 Azure 服務運行狀況警報。

## <a name="configuring-an-action-rule"></a>設定動作規則

您可以通過從 Azure 監視器中的 **"警報"** 登錄頁中選擇 **"管理操作**"來訪問該功能。 然後，選擇**操作規則（預覽）。** 您可以通過從登錄頁的儀表板中選擇 **"操作規則（預覽"）** 來訪問規則，以進行警報。

![Azure 監視器登錄頁上的操作規則](media/alerts-action-rules/action-rules-landing-page.png)

選擇 **= 新操作規則**。 

![添加新的操作規則](media/alerts-action-rules/action-rules-new-rule.png)

或者，您可以在配置警報規則時創建操作規則。

![添加新的操作規則](media/alerts-action-rules/action-rules-alert-rule.png)

現在，您應該看到用於創建操作規則的流頁。 配置以下元素： 

![新操作規則創建流](media/alerts-action-rules/action-rules-new-rule-creation-flow.png)

### <a name="scope"></a>影響範圍

首先選擇範圍（Azure 訂閱、資源組或目標資源）。 您還可以在單個訂閱中多次選擇作用域的組合。

![操作規則範圍](media/alerts-action-rules/action-rules-new-rule-creation-flow-scope.png)

### <a name="filter-criteria"></a>篩選準則

還可以定義篩選器，以將其縮小到警報的特定子集。 

可用的篩選器是： 

* **嚴重性**：選擇一個或多個警示嚴重性的選項。 **嚴重性 = Sev1**表示操作規則適用于設置為 Sev1 的所有警報。
* **監視服務**：基於原始監視服務的篩選器。 此篩選器也是多選的。 例如，**監視服務 = "應用程式見解"** 表示操作規則適用于所有基於應用程式見解的警報。
* **資源類型**：基於特定資源類型的篩選器。 此篩選器也是多選的。 例如，**資源類型 = "虛擬機器"** 表示操作規則適用于所有虛擬機器。
* **警報規則 ID**：使用警報規則的資源管理器 ID 篩選特定警報規則的選項。
* **監視器條件**：警報實例的篩選器，其中已**觸發**或**已解析**為監視器條件。
* **說明**： 註冊運算式（正則運算式）匹配，用於定義與描述匹配的字串匹配項，定義為警報規則的一部分。 例如，**描述包含"prod"** 將匹配其描述中包含字串"prod"的所有警報。
* **警報上下文（有效負載）：** 定義與警報有效負載的警報上下文欄位的字串匹配的正則運算式匹配。 例如，**警報上下文（有效負載）包含"電腦-01"** 將匹配其有效負載包含字串"電腦-01"的所有警報。

這些篩選器相互結合應用。 例如，如果設置 **"資源類型" = 虛擬機器**和**嚴重性" = Sev0**，則您只篩選了 VM 上的所有**Sev0**警報。 

![操作規則篩選器](media/alerts-action-rules/action-rules-new-rule-creation-flow-filters.png)

### <a name="suppression-or-action-group-configuration"></a>抑制或操作組配置

接下來，為警報抑制或操作組支援配置操作規則。 不能同時選擇這兩個。 配置對與以前定義的範圍和篩選器匹配的所有警報實例進行操作。

#### <a name="suppression"></a>隱藏

如果選擇 **"抑制"，** 請配置用於抑制操作和通知的持續時間。 選擇下列其中一個選項：
* **從現在開始（始終）：** 無限期地禁止所有通知。
* **在計畫的時間**：禁止在有界持續時間內的通知。
* **重複**：禁止每天、每週或每月週期性通知。

![操作規則抑制](media/alerts-action-rules/action-rules-new-rule-creation-flow-suppression.png)

#### <a name="action-group"></a>動作群組

如果在切換中選擇 **"操作"組**，請添加現有操作組或創建新操作組。 

> [!NOTE]
> 只能將一個操作組與操作規則相關聯。

![通過選擇操作組添加新操作規則](media/alerts-action-rules/action-rules-new-rule-creation-flow-action-group.png)

### <a name="action-rule-details"></a>操作規則詳細資訊

最後，為操作規則配置以下詳細資訊：
* 名稱
* 保存資源組的資源組
* 描述 

## <a name="example-scenarios"></a>範例案例

### <a name="scenario-1-suppression-of-alerts-based-on-severity"></a>方案 1：根據嚴重性抑制警報

Contoso 希望每個週末在訂閱**ContosoSub**中的所有 VM 上禁止發佈所有 Sev4 警報的通知。

**解決方案：** 創建具有：
* 範圍 = **ContosoSub**
* 篩選器
    * 嚴重性 = **Sev4**
    * 資源類型 =**虛擬機器**
* 禁止與重複設置為每週，星期**六**和**周日**檢查

### <a name="scenario-2-suppression-of-alerts-based-on-alert-context-payload"></a>方案 2：基於警報上下文（有效負載）抑制警報

Contoso 希望在**ContosoSub**中無限期地禁止針對**Computer-01**生成的所有日誌警報的通知，因為它正在進行維護。

**解決方案：** 創建具有：
* 範圍 = **ContosoSub**
* 篩選器
    * 監控服務 +**日誌分析**
    * 警報上下文（有效負載）包含**電腦-01**
* 禁止設置為 **"從現在開始"（始終）**

### <a name="scenario-3-action-group-defined-at-a-resource-group"></a>方案 3：在資源組定義的操作組

Contoso 已在[訂閱級別定義了指標警報](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-metric-overview#monitoring-at-scale-using-metric-alerts-in-azure-monitor)。 但它希望定義專門為從資源組**ContosoRG**生成的警報觸發的操作。

**解決方案：** 創建具有：
* 範圍 = **ContosoRG**
* 無篩檢程式
* 操作組設置為**ContosoActionGroup**

> [!NOTE]
> *在操作規則和警報規則中定義的操作組獨立運行，沒有重復資料消除。* 在前面描述的方案中，如果為警報規則定義了操作組，它將與操作規則中定義的操作組一起觸發。 

## <a name="managing-your-action-rules"></a>管理操作規則

您可以在清單視圖中查看和管理操作規則：

![操作規則清單視圖](media/alerts-action-rules/action-rules-list-view.png)

在此處，您可以通過選擇操作規則旁邊的核取方塊來大規模啟用、禁用或刪除操作規則。 選擇操作規則時，將打開其配置頁。 該頁可説明您更新操作規則的定義，並啟用或禁用它。

## <a name="best-practices"></a>最佳作法

使用[結果數創建日誌](alerts-unified-log.md)警報選項使用整個搜尋結果（可能跨越多台電腦）生成單個警報實例。 在這種情況下，如果操作規則使用**警報上下文（有效負載）** 篩選器，則只要有匹配項，它就對警報實例執行操作。 在方案 2 中，如果生成的日誌警報的搜尋結果同時包含**Computer-01**和**Computer-02，** 則將禁止整個通知。 根本沒有為**Computer-02**生成任何通知。

![操作規則和日誌警報（結果數）](media/alerts-action-rules/action-rules-log-alert-number-of-results.png)

為了最好使用具有操作規則的日誌警報，請使用[指標度量](alerts-unified-log.md)選項創建日誌警報。 此選項基於其定義的組欄位生成單獨的警報實例。 然後，在方案 2 中，為**Computer-01**和**Computer-02**生成單獨的警報實例。 由於方案仲介紹的操作規則，僅禁止對**電腦-01**的通知。 **電腦-02**的通知繼續像往常一樣觸發。

![操作規則和日誌警報（結果數）](media/alerts-action-rules/action-rules-log-alert-metric-measurement.png)

## <a name="faq"></a>常見問題集

### <a name="while-im-configuring-an-action-rule-id-like-to-see-all-the-possible-overlapping-action-rules-so-that-i-avoid-duplicate-notifications-is-it-possible-to-do-that"></a>在配置操作規則時，我希望查看所有可能的重疊操作規則，以便避免重複通知。 有可能嗎？

在配置操作規則時定義作用域後，可以看到在同一作用域（如果有）上重疊的操作規則清單。 此重疊可以是以下選項之一：

* 完全符合：例如，您正在定義的操作規則和重疊操作規則位於同一訂閱上。
* 子集：例如，您定義的操作規則位於訂閱上，重疊操作規則位於訂閱中的資源組上。
* 超級集：例如，您定義的操作規則位於資源組上，重疊操作規則位於包含資源組的訂閱上。
* 一個交集：例如，您定義的操作規則位於**VM1**和**VM2**上，重疊操作規則位於**VM2**和**VM3**上。

![重疊操作規則](media/alerts-action-rules/action-rules-overlapping.png)

### <a name="while-im-configuring-an-alert-rule-is-it-possible-to-know-if-there-are-already-action-rules-defined-that-might-act-on-the-alert-rule-im-defining"></a>在配置警報規則時，是否可以知道是否已定義了可能執行我定義的警報規則的操作規則？

定義警報規則的目標資源後，可以通過在 **"操作"** 部分下選擇 **"查看已配置的操作**"來查看對同一範圍（如果有）執行的操作規則的清單。 此清單基於作用域的以下方案填充：

* 完全符合：例如，您正在定義的警報規則和操作規則位於同一訂閱上。
* 子集：例如，您定義的警報規則位於訂閱上，操作規則位於訂閱中的資源組上。
* 超級集：例如，您定義的警報規則位於資源組上，操作規則位於包含資源組的訂閱上。
* 交叉點：例如，您定義的警報規則位於**VM1**和**VM2**上，操作規則位於**VM2**和**VM3**上。
    
![重疊操作規則](media/alerts-action-rules/action-rules-alert-rule-overlapping.png)

### <a name="can-i-see-the-alerts-that-have-been-suppressed-by-an-action-rule"></a>我能否看到已作規則抑制的警報？

在[警報清單頁](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-managing-alert-instances)中，您可以選擇名為 **"抑制狀態"** 的其他列。 如果警報實例的通知被抑制，它將在清單中顯示該狀態。

![已抑制警報實例](media/alerts-action-rules/action-rules-suppressed-alerts.png)

### <a name="if-theres-an-action-rule-with-an-action-group-and-another-with-suppression-active-on-the-same-scope-what-happens"></a>如果有一個操作規則，操作組和另一個在同一作用域上具有抑制活動的規則，會發生什麼情況？

抑制始終優先于同一範圍。

### <a name="what-happens-if-i-have-a-resource-thats-monitored-in-two-separate-action-rules-do-i-get-one-or-two-notifications-for-example-vm2-in-the-following-scenario"></a>如果我有一個在兩個單獨的操作規則中監視的資源，會發生什麼情況？ 我收到一個或兩個通知嗎？ 例如，在以下情況下的**VM2：**

      action rule AR1 defined for VM1 and VM2 with action group AG1
      action rule AR2 defined for VM2 and VM3 with action group AG1

對於 VM1 和 VM3 上的每個警報，操作組 AG1 將觸發一次。 對於**VM2**上的每個警報，操作組 AG1 將觸發兩次，因為操作規則不會重複重複操作。 

### <a name="what-happens-if-i-have-a-resource-monitored-in-two-separate-action-rules-and-one-calls-for-action-while-another-for-suppression-for-example-vm2-in-the-following-scenario"></a>如果我在兩個單獨的操作規則中監視了資源，一個請求執行操作，而另一個請求執行，則會發生什麼情況？ 例如，在以下情況下的**VM2：**

      action rule AR1 defined for VM1 and VM2 with action group AG1 
      action rule AR2 defined for VM2 and VM3 with suppression

對於 VM1 上的每個警報，操作組 AG1 將觸發一次。 將禁止對 VM2 和 VM3 上的每個警報的操作和通知。 

### <a name="what-happens-if-i-have-an-alert-rule-and-an-action-rule-defined-for-the-same-resource-calling-different-action-groups-for-example-vm1-in-the-following-scenario"></a>如果我有為調用不同操作組的同一資源定義的警報規則和操作規則，會發生什麼情況？ 例如，在以下情況下的**VM1：**

      alert rule rule1 on VM1 with action group AG2
      action rule AR1 defined for VM1 with action group AG1 
 
對於 VM1 上的每個警報，操作組 AG1 將觸發一次。 每當觸發警報規則"規則1"時，它也會觸發AG2。 在操作規則和警報規則中定義的操作組獨立運行，沒有重復資料消除。 

## <a name="next-steps"></a>後續步驟

- [瞭解有關 Azure 中的警報的更多詳細資訊](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-overview)
