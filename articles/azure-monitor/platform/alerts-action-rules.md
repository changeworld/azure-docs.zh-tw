---
title: Azure 監視器警示的動作規則
description: 瞭解 Azure 監視器中的動作規則，以及如何設定和管理它們。
ms.topic: conceptual
ms.date: 04/25/2019
ms.subservice: alerts
ms.openlocfilehash: 723da36093c895a3a4aefbe66c2d8ca2ac0cba32
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "90983133"
---
# <a name="action-rules-preview"></a>動作規則 (預覽) 

動作規則可協助您定義或隱藏任何 Azure Resource Manager 範圍 (Azure 訂用帳戶、資源群組或目標資源) 的動作。 他們有各種篩選器，可協助您縮小要採取行動的警示實例的特定子集。

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4rBZ2]

## <a name="why-and-when-should-you-use-action-rules"></a>使用動作規則的原因和時機為何？

### <a name="suppression-of-alerts"></a>隱藏警示

在許多情況下，隱藏警示所產生的通知會很有用。 這些案例的範圍是在規劃的維護期間隱藏在非上班時間的期間。 例如，負責  **ContosoVM** 的小組想要隱藏即將進行的週末的警示通知，因為 **ContosoVM** 正在進行預定的維護。

雖然小組可以在 **ContosoVM** 上手動停用設定的每個警示規則 (，並在維護) 之後再次啟用它，並不是簡單的程式。 動作規則可協助您以彈性地設定隱藏期間的能力，定義大規模的警示隱藏。 在上述範例中，小組可以在 **ContosoVM** 上定義一個動作規則，以隱藏週末的所有警示通知。

### <a name="actions-at-scale"></a>大規模動作

雖然警示規則可協助您定義產生警示時觸發的動作群組，但客戶通常會在其作業範圍內有一個共同的動作群組。 例如，負責資源群組 **ContosoRG** 的小組可能會針對 **ContosoRG**中定義的所有警示規則，定義相同的動作群組。

動作規則可協助您簡化此程式。 藉由大規模定義動作，可針對設定的範圍所產生的任何警示觸發動作群組。 在上述範例中，小組可以在 **ContosoRG** 上定義一個動作規則，以針對其中產生的所有警示觸發相同的動作群組。

> [!NOTE]
> 動作規則目前不適用於 Azure 服務健康狀態警示。

## <a name="configuring-an-action-rule"></a>設定動作規則

### <a name="portal"></a>[入口網站](#tab/portal)

您可以從 Azure 監視器中的 [**警示**] 登陸頁面選取 [**管理動作**]，以存取此功能。 然後，選取 [ **動作規則] (預覽) **。 您可以從警示登陸頁面的儀表板中，選取 [ **動作規則 (預覽) ** ] 來存取規則。

![Azure 監視器登陸頁面中的動作規則](media/alerts-action-rules/action-rules-landing-page.png)

選取 [ **+ 新增動作規則**]。

![螢幕擷取畫面顯示 [管理動作] 頁面，其中已醒目提示 [新增動作規則] 按鈕。](media/alerts-action-rules/action-rules-new-rule.png)

或者，您也可以在設定警示規則時建立動作規則。

![螢幕擷取畫面顯示 [建立規則] 頁面，其中醒目提示 [建立動作規則] 按鈕。](media/alerts-action-rules/action-rules-alert-rule.png)

您現在應該會看到建立動作規則的 [流程] 頁面。 設定下列元素：

![新增動作規則建立流程](media/alerts-action-rules/action-rules-new-rule-creation-flow.png)

### <a name="scope"></a>影響範圍

首先，選擇 Azure 訂用帳戶、資源群組或目標資源)  (範圍。 您也可以在單一訂用帳戶內多重選取範圍的組合。

![動作規則範圍](media/alerts-action-rules/action-rules-new-rule-creation-flow-scope.png)

### <a name="filter-criteria"></a>篩選準則

您可以另外定義篩選器，將它們縮小至警示的特定子集。

可用的篩選器是：

* **嚴重性**：選取一或多個警示嚴重性的選項。 **嚴重性 = Sev1** 表示動作規則適用于所有設定為 Sev1 的警示。
* **監視服務**：根據原始監視服務的篩選準則。 此篩選準則也會進行多重選取。 例如， **監視服務 = "Application Insights"** 表示動作規則適用于所有以 Application Insights 為基礎的警示。
* **資源類型**：以特定資源類型為基礎的篩選準則。 此篩選準則也會進行多重選取。 例如， **資源類型 = 「虛擬機器** 」表示動作規則適用于所有虛擬機器。
* **警示規則識別碼**：使用警示規則的 Resource Manager 識別碼來篩選特定警示規則的選項。
* **監視條件**：使用 **引發** 或 **解決** 為監視條件之警示實例的篩選準則。
* **描述**： RegEx (正則運算式) 相符，它會定義與描述的字串相符，定義為警示規則的一部分。 例如， **描述包含「生產** 」，將符合其描述中包含「生產」字串的所有警示。
* **警示內容 (承載) **：根據警示內容的警示內容欄位定義字串相符的 RegEx 相符項。 例如， ** (承載) 包含 ' computer-01 ' 的警示內容** 將會符合承載包含字串 "computer-01" 的所有警示。

這些篩選器會與另一個篩選一起套用。 例如，如果您設定 **資源類型「= 虛擬機器** 和 **嚴重性」 = Sev0**，則只會篩選您 Vm 上的所有 **Sev0** 警示。

![動作規則篩選](media/alerts-action-rules/action-rules-new-rule-creation-flow-filters.png)

### <a name="suppression-or-action-group-configuration"></a>隱藏專案或動作群組設定

接下來，設定警示隱藏或動作群組支援的動作規則。 您無法選擇兩者。 設定會作用於所有符合先前定義之範圍和篩選準則的警示實例。

#### <a name="suppression"></a>隱藏

如果您選取 [ **隱藏**]，請設定隱藏動作和通知的持續時間。 選擇下列其中一個選項：
* **從現在開始 (一律) **：無限期抑制所有通知。
* **在排程的時間**：隱藏限定期間內的通知。
* **使用**週期性：抑制每日、每週或每月排程的通知。

![動作規則隱藏](media/alerts-action-rules/action-rules-new-rule-creation-flow-suppression.png)

#### <a name="action-group"></a>動作群組

如果您在切換中選取 [ **動作群組** ]，請新增現有的動作群組或建立一個新的動作群組。

> [!NOTE]
> 您只能將一個動作群組與動作規則產生關聯。

![藉由選取動作群組來新增或建立新的動作規則](media/alerts-action-rules/action-rules-new-rule-creation-flow-action-group.png)

### <a name="action-rule-details"></a>動作規則詳細資料

最後，設定動作規則的下列詳細資料：
* 名稱
* 儲存的資源群組
* 描述

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

您可以使用 [az monitor action-rule create](/cli/azure/ext/alertsmanagement/monitor/action-rule#ext-alertsmanagement-az-monitor-action-rule-create) 命令，利用 Azure CLI 建立動作規則。  `az monitor action-rule`參考只是 Azure 監視器的許多[Azure CLI 參考](/cli/azure/azure-cli-reference-for-monitor)之一。

### <a name="prepare-your-environment"></a>準備您的環境

1. [安裝 Azure CLI](/cli/azure/install-azure-cli)

   如果您想要的話，也可以使用 Azure Cloud Shell 來完成本文中的步驟。  Azure Cloud Shell 是透過瀏覽器使用的互動式 Shell 環境。  使用下列其中一種方法來開始 Cloud Shell：

   - 開啟 Cloud Shell，方法是前往 [https://shell.azure.com](https://shell.azure.com)

   - 在 [ [Azure 入口網站](https://portal.azure.com)中右上角的功能表列上，選取 [ **Cloud Shell** ] 按鈕

1. 登入。

   如果您使用的是本機的 CLI 安裝，請使用 [az login 命令登](/cli/azure/reference-index#az-login) 入。  請遵循您終端機上顯示的步驟，來完成驗證程序。

    ```azurecli
    az login
    ```

1. 安裝 `alertsmanagement` 擴充功能

   此 `az monitor action-rule` 命令是核心 Azure CLI 的實驗性延伸模組。 深入瞭解搭配 [Azure CLI 使用延伸](/cli/azure/azure-cli-extensions-overview?)模組中的擴充功能參考。

   ```azurecli
   az extension add --name alertsmanagement
   ```

   預期會出現下列警告。

   ```output
   The installed extension `alertsmanagement` is experimental and not covered by customer support.  Please use with discretion.
   ```

### <a name="create-action-rules-with-the-azure-cli"></a>使用 Azure CLI 建立動作規則

請參閱「 [az 監視器動作-規則建立](/cli/azure/ext/alertsmanagement/monitor/action-rule#ext-alertsmanagement-az-monitor-action-rule-create) 」 Azure CLI 參考內容，以瞭解必要和選擇性參數。

建立動作規則以隱藏資源群組中的通知。

```azurecli
az monitor action-rule create --resource-group MyResourceGroupName \
                              --name MyNewActionRuleName \
                              --location Global \
                              --status Enabled \
                              --rule-type Suppression \
                              --scope-type ResourceGroup \
                              --scope /subscriptions/0b1f6471-1bf0-4dda-aec3-cb9272f09590/resourceGroups/MyResourceGroupName \
                              --suppression-recurrence-type Always \
                              --alert-context Contains Computer-01 \
                               --monitor-service Equals "Log Analytics"
```

建立動作規則，以針對每個週末的訂用帳戶中的所有 Vm，隱藏所有 Sev4 警示的通知。

```azurecli
az monitor action-rule create --resource-group MyResourceGroupName \
                              --name MyNewActionRuleName \
                              --location Global \
                              --status Enabled \
                              --rule-type Suppression \
                              --severity Equals Sev4 \
                              --target-resource-type Equals Microsoft.Compute/VirtualMachines \
                              --suppression-recurrence-type Weekly \
                              --suppression-recurrence 0 6 \
                              --suppression-start-date 12/09/2018 \
                              --suppression-end-date 12/18/2018 \
                              --suppression-start-time 06:00:00 \
                              --suppression-end-time 14:00:00

```

* * *

## <a name="example-scenarios"></a>範例案例

### <a name="scenario-1-suppression-of-alerts-based-on-severity"></a>案例1：根據嚴重性抑制警示

Contoso 想要針對每個週末 **ContosoSub** 訂用帳戶中的所有 vm，隱藏所有 Sev4 警示的通知。

**解決方案：** 使用下列方式建立動作規則：
* 範圍 = **ContosoSub**
* 篩選器
    * 嚴重性 = **Sev4**
    * 資源類型 = **虛擬機器**
* 將 [週期] 設定為 [每週]，並核取**星期六**和**星期日**

### <a name="scenario-2-suppression-of-alerts-based-on-alert-context-payload"></a>案例2：根據警示內容 (承載來抑制警示) 

Contoso 想要針對在**ContosoSub**中針對**電腦 01**產生的所有記錄警示隱藏通知，因為它正在進行維護。

**解決方案：** 使用下列方式建立動作規則：
* 範圍 = **ContosoSub**
* 篩選器
    * 監視服務 = **Log Analytics**
    * 警示內容 (承載) 包含 **電腦-01**
* 隱藏專案設定為 **從現在開始 (一律) **

### <a name="scenario-3-action-group-defined-at-a-resource-group"></a>案例3：在資源群組上定義的動作群組

Contoso 已 [在訂用帳戶層級](./alerts-metric-overview.md#monitoring-at-scale-using-metric-alerts-in-azure-monitor)定義計量警示。 但它想要針對從資源群組 **ContosoRG**產生的警示，定義特別觸發的動作。

**解決方案：** 使用下列方式建立動作規則：
* 範圍 = **ContosoRG**
* 沒有篩選
* 動作群組設定為 **ContosoActionGroup**

> [!NOTE]
> *動作規則和警示規則內定義的動作群組會獨立運作，而不會重復資料刪除。* 在稍早所述的案例中，如果已定義警示規則的動作群組，它會與動作規則中所定義的動作群組一起觸發。

## <a name="managing-your-action-rules"></a>管理您的動作規則

### <a name="portal"></a>[入口網站](#tab/portal)

您可以從清單視圖來查看和管理動作規則：

![動作規則清單視圖](media/alerts-action-rules/action-rules-list-view.png)

您可以從這裡選取旁邊的核取方塊，以啟用、停用或刪除調整動作規則。 當您選取動作規則時，將會開啟其 [設定] 頁面。 此頁面可協助您更新動作規則的定義，並啟用或停用它。

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

您可以使用 Azure CLI 中的 [az monitor action-rule](/cli/azure/ext/alertsmanagement/monitor) 命令，來查看和管理您的動作規則。

使用 Azure CLI 管理動作規則之前，請先使用設定 [動作規則](#configuring-an-action-rule)中提供的指示來準備您的環境。

```azurecli
# List all action rules for a subscription
az monitor action-rule list

# Get details of an action rule
az monitor action-rule show --resource-group MyResourceGroupName --name MyActionRuleName

# Update an action rule.
az monitor action-rule update --resource-group MyResourceGroupName --name MyActionRuleName --status Disabled

# Delete an action rule.
az monitor action-rule delete --resource-group MyResourceGroupName --name MyActionRuleName
```

* * *

## <a name="best-practices"></a>最佳作法

您使用 [ [結果數目](alerts-unified-log.md) ] 選項建立的記錄警示會使用整個搜尋結果產生單一警示實例， (可能跨越多部電腦) 。 在此案例中，如果動作規則使用 **警示內容 (承載) ** 篩選器，則只要有相符的內容，它就會作用於警示實例。 在先前所述的案例2中，如果產生的記錄警示的搜尋結果同時包含 **電腦-01** 和 **computer-02**，則會隱藏整個通知。 沒有為 **電腦-02** 產生的通知。

![圖表會顯示已醒目提示單一警示實例的動作規則和記錄警示。](media/alerts-action-rules/action-rules-log-alert-number-of-results.png)

若要利用動作規則來充分利用記錄警示，請使用 [計量測量](alerts-unified-log.md) 選項來建立記錄警示。 此選項會根據其定義的群組欄位來產生個別的警示實例。 然後，在案例2中，會產生 **電腦-01** 和 **computer-02**的個別警示實例。 由於案例中所述的動作規則，只會隱藏 **電腦-01** 的通知。 **電腦-02**的通知會繼續正常引發。

![動作規則和記錄警示 (結果數目) ](media/alerts-action-rules/action-rules-log-alert-metric-measurement.png)

## <a name="faq"></a>常見問題集

### <a name="while-im-configuring-an-action-rule-id-like-to-see-all-the-possible-overlapping-action-rules-so-that-i-avoid-duplicate-notifications-is-it-possible-to-do-that"></a>當我設定動作規則時，我想要查看所有可能的重迭動作規則，以避免重複的通知。 可以這麼做嗎？

當您在設定動作規則之後定義範圍之後，您可以看到在相同範圍內重迭的動作規則清單 (是否有任何) 。 此重迭可以是下列其中一個選項：

* 完全相符：例如，您要定義的動作規則和重迭的動作規則都在相同的訂用帳戶上。
* 子集：例如，您要定義的動作規則是在訂用帳戶上，而重迭的動作規則則是在訂用帳戶內的資源群組中。
* 超集合：例如，您所定義的動作規則是在資源群組中，而重迭的動作規則則是在包含資源群組的訂用帳戶上。
* 交集：例如，您所定義的動作規則是在 **VM1** 和 **VM2**上，而重迭的動作規則則是在 **VM2** 和 **VM3**上。

![螢幕擷取畫面顯示 [新增動作規則] 頁面，其中包含在相同範圍視窗上定義的動作規則中所顯示的重迭動作規則。](media/alerts-action-rules/action-rules-overlapping.png)

### <a name="while-im-configuring-an-alert-rule-is-it-possible-to-know-if-there-are-already-action-rules-defined-that-might-act-on-the-alert-rule-im-defining"></a>當我設定警示規則時，是否可以知道是否已定義一些動作規則，這些規則可能會對我定義的警示規則採取動作？

定義警示規則的目標資源之後，您可以在 [**動作**] 區段下選取 [ **View 已設定的動作**]，以查看在相同範圍)  (的動作規則清單。 此清單會根據以下的範圍案例填入：

* 完全相符：例如，您所定義的警示規則和動作規則都在相同的訂用帳戶上。
* 子集：例如，您所定義的警示規則是在訂用帳戶上，而動作規則則是在訂用帳戶內的資源群組中。
* 超集合：例如，您所定義的警示規則是在資源群組中，而動作規則則是在包含資源群組的訂用帳戶上。
* 交集：例如，您所定義的警示規則是在 **VM1** 和 **VM2**，而動作規則是在 **VM2** 和 **VM3**。

![重迭的動作規則](media/alerts-action-rules/action-rules-alert-rule-overlapping.png)

### <a name="can-i-see-the-alerts-that-have-been-suppressed-by-an-action-rule"></a>我可以看到動作規則已隱藏的警示嗎？

在 [ [警示清單] 頁面](./alerts-managing-alert-instances.md)中，您可以選擇另一個稱為 **隱藏狀態**的資料行。 如果警示實例的通知已隱藏，則會在清單中顯示該狀態。

![隱藏的警示實例](media/alerts-action-rules/action-rules-suppressed-alerts.png)

### <a name="if-theres-an-action-rule-with-an-action-group-and-another-with-suppression-active-on-the-same-scope-what-happens"></a>如果有動作規則具有動作群組，而另一個具有在相同範圍中處於作用中狀態，則會發生什麼事？

隱藏範圍一律優先于相同的範圍。

### <a name="what-happens-if-i-have-a-resource-thats-monitored-in-two-separate-action-rules-do-i-get-one-or-two-notifications-for-example-vm2-in-the-following-scenario"></a>如果我在兩個不同的動作規則中監視資源，會發生什麼事？ 是否取得一或兩個通知？ 例如，在下列案例中 **VM2** ：

   `action rule AR1 defined for VM1 and VM2 with action group AG1`

   `action rule AR2 defined for VM2 and VM3 with action group AG1`

針對 VM1 和 VM3 上的每個警示，動作群組 AG1 將會觸發一次。 針對 **VM2**上的每個警示，動作群組 AG1 都會觸發兩次，因為動作規則不會刪除動作。

### <a name="what-happens-if-i-have-a-resource-monitored-in-two-separate-action-rules-and-one-calls-for-action-while-another-for-suppression-for-example-vm2-in-the-following-scenario"></a>如果我在兩個不同的動作規則中監視資源，並在另一個呼叫時進行隱藏，則會發生什麼事？ 例如，在下列案例中 **VM2** ：

   `action rule AR1 defined for VM1 and VM2 with action group AG1`

   `action rule AR2 defined for VM2 and VM3 with suppression`

針對 VM1 上的每個警示，動作群組 AG1 會觸發一次。 將會抑制 VM2 和 VM3 上每個警示的動作和通知。

### <a name="what-happens-if-i-have-an-alert-rule-and-an-action-rule-defined-for-the-same-resource-calling-different-action-groups-for-example-vm1-in-the-following-scenario"></a>如果我針對呼叫不同動作群組的相同資源定義了警示規則和動作規則，會發生什麼事？ 例如，在下列案例中 **VM1** ：

   `alert rule rule1 on VM1 with action group AG2`

   `action rule AR1 defined for VM1 with action group AG1`

針對 VM1 上的每個警示，動作群組 AG1 會觸發一次。 每當觸發警示規則 "rule1" 時，也會觸發 AG2。 動作規則和警示規則內定義的動作群組會獨立運作，而不會重復資料刪除。

## <a name="next-steps"></a>後續步驟

- [深入瞭解 Azure 中的警示](./alerts-overview.md)
