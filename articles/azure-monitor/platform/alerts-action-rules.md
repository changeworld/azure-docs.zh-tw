---
title: Azure 監視器警示的動作規則
description: 瞭解 Azure 監視器中的動作規則，以及如何設定和管理它們。
ms.topic: conceptual
ms.date: 04/25/2019
ms.subservice: alerts
ms.openlocfilehash: 6585890395d7656f239ac3098cd374ecd4757842
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "80618980"
---
# <a name="action-rules-preview"></a>動作規則（預覽）

動作規則可協助您定義或隱藏任何 Azure Resource Manager 範圍（Azure 訂用帳戶、資源群組或目標資源）的動作。 它們具有各種篩選器，可協助您縮小要採取行動的警示實例的特定子集。

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4rBZ2]

## <a name="why-and-when-should-you-use-action-rules"></a>為何和何時應該使用動作規則？

### <a name="suppression-of-alerts"></a>隱藏警示

在許多情況下，隱藏警示產生的通知會很有用。 這些案例的範圍從在預定的維護期間，到在非上班時間顯示時隱藏。 例如，負責**ContosoVM**的小組想要隱藏近期週末的警示通知，因為**ContosoVM**正在進行規劃的維護。 

雖然小組可以手動停用在**ContosoVM**上設定的每個警示規則（並在維護之後再次啟用），但它並不是一個簡單的程式。 動作規則可協助您以彈性地設定隱藏時段的功能，大規模定義警示抑制。 在上述範例中，小組可以在**ContosoVM**上定義一個動作規則，以隱藏週末的所有警示通知。


### <a name="actions-at-scale"></a>大規模動作

雖然警示規則可協助您定義在產生警示時觸發的動作群組，但客戶在其作業範圍內通常會有共同的動作群組。 例如，負責資源群組**ContosoRG**的小組可能會針對**ContosoRG**中定義的所有警示規則定義相同的動作群組。 

動作規則可協助您簡化此程式。 藉由定義大規模的動作，可以針對在設定的範圍上產生的任何警示觸發動作群組。 在上述範例中，小組可以在**ContosoRG**上定義一個動作規則，以針對在其中產生的所有警示觸發相同的動作群組。

> [!NOTE]
> 動作規則目前不適用於 Azure 服務健康狀態警示。

## <a name="configuring-an-action-rule"></a>設定動作規則

您可以從 Azure 監視器中的 [**警示**] 登陸頁面選取 [**管理動作**]，以存取此功能。 然後選取 **[動作規則（預覽）**]。 您可以從警示登陸頁面的儀表板中選取 [**動作規則（預覽）** ] 來存取規則。

![Azure 監視器登陸頁面中的動作規則](media/alerts-action-rules/action-rules-landing-page.png)

選取 [ **+ 新增動作規則**]。 

![新增動作規則](media/alerts-action-rules/action-rules-new-rule.png)

或者，您可以在設定警示規則時建立動作規則。

![新增動作規則](media/alerts-action-rules/action-rules-alert-rule.png)

您現在應該會看到建立動作規則的 [流程] 頁面。 設定下列元素： 

![新增動作規則建立流程](media/alerts-action-rules/action-rules-new-rule-creation-flow.png)

### <a name="scope"></a>影響範圍

首先選擇範圍（Azure 訂用帳戶、資源群組或目標資源）。 您也可以在單一訂用帳戶內多重選取範圍的組合。

![動作規則範圍](media/alerts-action-rules/action-rules-new-rule-creation-flow-scope.png)

### <a name="filter-criteria"></a>篩選準則

此外，您還可以定義篩選器，將它們縮小到特定的警示子集。 

可用的篩選器是： 

* **嚴重性**：選取一或多個警示嚴重性的選項。 **嚴重性 = Sev1**表示動作規則適用于設定為 Sev1 的所有警示。
* **監視服務**：以原始監視服務為基礎的篩選準則。 此篩選也是多重選取。 例如， **Monitor Service = "Application Insights"** 表示動作規則適用于所有以 Application Insights 為基礎的警示。
* **資源類型**：以特定資源類型為基礎的篩選準則。 此篩選也是多重選取。 例如，**資源類型 = "虛擬機器"** 表示動作規則適用于所有虛擬機器。
* **警示規則識別碼**：用來篩選特定警示規則的選項，方法是使用警示規則的 Resource Manager 識別碼。
* **監視條件**：警示實例的篩選，其中會**引發**或**解析**為監視條件。
* **描述**：根據描述定義字串比對的 RegEx （正則運算式）比對，定義為警示規則的一部分。 例如，「**描述」包含「生產**」將會比對在其描述中包含「生產」字串的所有警示。
* **警示內容（承載）**：根據警示承載的警示內容欄位，定義字串相符的 RegEx 比對。 例如，**警示內容（承載）包含 ' computer-01 '** ，將會比對其裝載包含字串 "computer-01" 的所有警示。

這些篩選器會彼此搭配使用。 例如，如果您將**資源類型 ' = 虛擬機器**和**嚴重性 ' = Sev0**，則只會針對您的 vm 篩選所有**Sev0**警示。 

![動作規則篩選](media/alerts-action-rules/action-rules-new-rule-creation-flow-filters.png)

### <a name="suppression-or-action-group-configuration"></a>隱藏專案或動作群組設定

接下來，設定警示隱藏或動作群組支援的動作規則。 您無法選擇這兩者。 設定會作用於符合先前定義之範圍和篩選準則的所有警示實例。

#### <a name="suppression"></a>隱藏

如果您選取 [**隱藏**]，請設定隱藏動作和通知的持續時間。 選擇下列其中一個選項：
* **從現在（一律）**：無限期地隱藏所有通知。
* **在排程的時間**：抑制限定期間內的通知。
* **有週期**：每日、每週或每月排程抑制通知。

![動作規則隱藏專案](media/alerts-action-rules/action-rules-new-rule-creation-flow-suppression.png)

#### <a name="action-group"></a>動作群組

如果您在切換中選取 [**動作群組**]，請加入現有的動作群組，或建立一個新的群組。 

> [!NOTE]
> 您只能將一個動作群組與一個動作規則產生關聯。

![藉由選取 [動作群組] 來新增或建立新的動作規則](media/alerts-action-rules/action-rules-new-rule-creation-flow-action-group.png)

### <a name="action-rule-details"></a>動作規則詳細資料

最後，設定動作規則的下列詳細資料：
* Name
* 儲存它的資源群組
* 描述 

## <a name="example-scenarios"></a>範例案例

### <a name="scenario-1-suppression-of-alerts-based-on-severity"></a>案例1：根據嚴重性隱藏警示

Contoso 想要在每個週末，針對訂用帳戶**ContosoSub**內所有 vm 的所有 Sev4 警示隱藏通知。

**解決方案：** 使用下列方式建立動作規則：
* 範圍 = **ContosoSub**
* 篩選條件
    * 嚴重性 = **Sev4**
    * 資源類型 =**虛擬機器**
* [週期] 設為 [每週]，並核取 [**星期六**] 和 [**星期日**]

### <a name="scenario-2-suppression-of-alerts-based-on-alert-context-payload"></a>案例2：根據警示內容來隱藏警示（承載）

Contoso 想要在**ContosoSub**中，無限期地隱藏針對**電腦 01**所產生的所有記錄警示通知，因為它正在進行維護。

**解決方案：** 使用下列方式建立動作規則：
* 範圍 = **ContosoSub**
* 篩選條件
    * 監視服務 = **Log Analytics**
    * 警示內容（承載）包含**Computer-01**
* 隱藏專案設定為**從現在（一律）**

### <a name="scenario-3-action-group-defined-at-a-resource-group"></a>案例3：在資源群組中定義的動作群組

Contoso 已[在訂用帳戶層級定義度量警示](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-metric-overview#monitoring-at-scale-using-metric-alerts-in-azure-monitor)。 但它想要針對從資源群組**ContosoRG**產生的警示定義特別觸發的動作。

**解決方案：** 使用下列方式建立動作規則：
* 範圍 = **ContosoRG**
* 沒有篩選
* 動作群組設定為**ContosoActionGroup**

> [!NOTE]
> *在動作規則中定義的動作群組和警示規則會獨立運作，而不會進行重復資料刪除。* 在稍早所述的案例中，如果為警示規則定義了動作群組，它會與動作規則中定義的動作群組一起觸發。 

## <a name="managing-your-action-rules"></a>管理您的動作規則

您可以從清單視圖中查看和管理您的動作規則：

![動作規則清單視圖](media/alerts-action-rules/action-rules-list-view.png)

您可以從這裡選取其旁邊的核取方塊，以大規模啟用、停用或刪除動作規則。 當您選取 [動作規則] 時，其 [設定] 頁面隨即開啟。 此頁面可協助您更新動作規則的定義，並啟用或停用它。

## <a name="best-practices"></a>最佳作法

您使用 [[結果數目](alerts-unified-log.md)] 選項建立的記錄警示，會使用整個搜尋結果（可能跨越多部電腦）來產生單一警示實例。 在此案例中，如果動作規則使用**警示內容（承載）** 篩選準則，它就會在警示實例上作用（只要有相符的）。 在先前所述的案例2中，如果產生之記錄警示的搜尋結果同時包含**computer-01**和**computer-02**，則會隱藏整個通知。 不會針對**Computer-02**產生任何通知。

![動作規則和記錄警示（結果數目）](media/alerts-action-rules/action-rules-log-alert-number-of-results.png)

若要充分利用具有動作規則的記錄警示，請使用 [[計量量測](alerts-unified-log.md)] 選項來建立記錄警示。 此選項會根據其定義的群組欄位來產生個別的警示實例。 然後，在案例2中，會針對**Computer-01**和**computer-02**產生個別的警示實例。 由於案例中所述的動作規則，只會抑制**電腦-01**的通知。 **電腦-02**的通知會繼續正常運作。

![動作規則和記錄警示（結果數目）](media/alerts-action-rules/action-rules-log-alert-metric-measurement.png)

## <a name="faq"></a>常見問題集

### <a name="while-im-configuring-an-action-rule-id-like-to-see-all-the-possible-overlapping-action-rules-so-that-i-avoid-duplicate-notifications-is-it-possible-to-do-that"></a>當我設定動作規則時，我想要查看所有可能重迭的動作規則，讓我避免重複的通知。 可以這麼做嗎？

在您設定動作規則之後定義範圍之後，您可以查看相同範圍（如果有的話）上重迭的動作規則清單。 這種重迭可以是下列其中一個選項：

* 完全相符：例如，您所定義的動作規則和重迭的動作規則都位於相同的訂用帳戶上。
* 子集：例如，您所定義的動作規則是在訂用帳戶上，而重迭的動作規則則是在訂用帳戶內的資源群組上。
* 超集合：例如，您所定義的動作規則是在資源群組上，而重迭的動作規則則是在包含資源群組的訂用帳戶上。
* 交集：例如，您所定義的動作規則是在**VM1**和**VM2**上，而重迭的動作規則是在**VM2**和**VM3**上。

![重迭動作規則](media/alerts-action-rules/action-rules-overlapping.png)

### <a name="while-im-configuring-an-alert-rule-is-it-possible-to-know-if-there-are-already-action-rules-defined-that-might-act-on-the-alert-rule-im-defining"></a>當我設定警示規則時，是否可以知道是否已經定義動作規則來處理我定義的警示規則？

定義警示規則的目標資源之後，您可以在 [**動作**] 區段底下選取 [**查看設定的動作**]，以查看在相同範圍（如果有的話）上作用的動作規則清單。 此清單會根據範圍的下列案例填入：

* 完全相符：例如，您所定義的警示規則和動作規則都位於相同的訂用帳戶上。
* 子集：例如，您所定義的警示規則是在訂用帳戶上，而動作規則則是在訂用帳戶內的資源群組上。
* 超集合：例如，您所定義的警示規則是在資源群組上，而動作規則則是在包含資源群組的訂用帳戶上。
* 交集：例如，您所定義的警示規則是在**VM1**和**VM2**上，而動作規則是在**VM2**和**VM3**上。
    
![重迭動作規則](media/alerts-action-rules/action-rules-alert-rule-overlapping.png)

### <a name="can-i-see-the-alerts-that-have-been-suppressed-by-an-action-rule"></a>我可以看到動作規則已隱藏的警示嗎？

在 [[警示清單] 頁面](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-managing-alert-instances)中，您可以選擇另一個稱為**隱藏狀態**的資料行。 如果警示實例的通知已隱藏，它會在清單中顯示該狀態。

![隱藏的警示實例](media/alerts-action-rules/action-rules-suppressed-alerts.png)

### <a name="if-theres-an-action-rule-with-an-action-group-and-another-with-suppression-active-on-the-same-scope-what-happens"></a>如果有一個動作規則具有動作群組，而另一個在相同的範圍內有隱藏的作用中，會發生什麼事？

隱藏專案一律優先于相同的範圍。

### <a name="what-happens-if-i-have-a-resource-thats-monitored-in-two-separate-action-rules-do-i-get-one-or-two-notifications-for-example-vm2-in-the-following-scenario"></a>如果我的資源在兩個不同的動作規則中受到監視，會發生什麼事？ 我收到一或兩個通知嗎？ 例如，下列案例中的**VM2** ：

      action rule AR1 defined for VM1 and VM2 with action group AG1
      action rule AR2 defined for VM2 and VM3 with action group AG1

針對 VM1 和 VM3 上的每個警示，動作群組 AG1 會觸發一次。 針對**VM2**上的每個警示，動作群組 AG1 會觸發兩次，因為動作規則不會刪除重複動作。 

### <a name="what-happens-if-i-have-a-resource-monitored-in-two-separate-action-rules-and-one-calls-for-action-while-another-for-suppression-for-example-vm2-in-the-following-scenario"></a>如果我的資源是在兩個不同的動作規則中進行監視，而另一項動作是針對抑制而執行，則會發生什麼事？ 例如，下列案例中的**VM2** ：

      action rule AR1 defined for VM1 and VM2 with action group AG1 
      action rule AR2 defined for VM2 and VM3 with suppression

針對 VM1 上的每個警示，動作群組 AG1 會觸發一次。 VM2 和 VM3 上的每個警示的動作和通知都會隱藏起來。 

### <a name="what-happens-if-i-have-an-alert-rule-and-an-action-rule-defined-for-the-same-resource-calling-different-action-groups-for-example-vm1-in-the-following-scenario"></a>如果我針對呼叫不同動作群組的相同資源定義了警示規則和動作規則，會發生什麼事？ 例如，下列案例中的**VM1** ：

      alert rule rule1 on VM1 with action group AG2
      action rule AR1 defined for VM1 with action group AG1 
 
針對 VM1 上的每個警示，動作群組 AG1 會觸發一次。 每次觸發警示規則 "rule1" 時，它也會另外觸發 AG2。 在動作規則中定義的動作群組和警示規則會獨立運作，而不會進行重復資料刪除。 

## <a name="next-steps"></a>後續步驟

- [深入瞭解 Azure 中的警示](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-overview)
