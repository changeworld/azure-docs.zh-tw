---
title: Azure 監視器活頁簿下拉式參數
description: 使用預建和自訂參數化活頁簿（包含下拉式參數）簡化複雜的報表
services: azure-monitor
manager: carmonm
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 10/23/2019
ms.openlocfilehash: 07a8ece76bffe0fbbcbc211e83730433cdeda2c4
ms.sourcegitcommit: dbe434f45f9d0f9d298076bf8c08672ceca416c6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/17/2020
ms.locfileid: "92143797"
---
# <a name="workbook-drop-down-parameters"></a>活頁簿下拉式參數

下拉式清單可讓使用者從已知的集合中收集一或多個輸入值 (例如，選取其中一個應用程式的要求) 。 下拉式清單提供方便使用的方式，向使用者收集任意輸入。 在互動式報表中啟用篩選時，下拉式清單特別有用。 

指定下拉式清單最簡單的方式，就是在參數設定中提供靜態清單。 比較有趣的方法是透過 KQL 查詢動態取得清單。 參數設定也可讓您指定是單一或多重選取，如果是多重選取，則應該如何將結果集格式化 (分隔符號、引號等 ) 。

## <a name="creating-a-static-drop-down-parameter"></a>建立靜態下拉式參數

1. 在編輯模式中，從空白的活頁簿開始。
2. 從活頁簿內的連結選擇 [ _加入參數_ ]。
3. 按一下藍色的 [ _加入參數_ ] 按鈕。
4. 在彈出的新參數窗格中輸入：
    1. 參數名稱： `Environment`
    2. 參數類型： `Drop down`
    3. 必填： `checked`
    4. 允許 `multiple selection` ： `unchecked`
    5. 取得資料來源： `JSON`
5. 在 JSON 輸入文字區塊中，插入此 json 程式碼片段：
    ```json
    [
        { "value":"dev", "label":"Development" },
        { "value":"ppe", "label":"Pre-production" },
        { "value":"prod", "label":"Production", "selected":true }
    ]
    ```
6. 按藍色 `Update` 按鈕。
7. 從工具列中選擇 [儲存] 以建立參數。
8. 環境參數將會是具有三個值的下拉式清單。

    ![顯示靜態下拉式清單建立的影像](./media/workbook-dropdowns/dropdown-create.png)

## <a name="creating-a-static-dropdown-with-groups-of-items"></a>使用專案群組建立靜態下拉式清單

如果您的查詢結果/json 包含「群組」欄位，則下拉式清單會顯示值的群組。 遵循上述範例，但改為使用下列 json：

```json
[
    { "value":"dev", "label":"Development", "group":"Development" },
    { "value":"dev-cloud", "label":"Development (Cloud)", "group":"Development" },
    { "value":"ppe", "label":"Pre-production", "group":"Test" },
    { "value":"ppe-test", "label":"Pre-production (Test)", "group":"Test" },
    { "value":"prod1", "label":"Prod 1", "selected":true, "group":"Production" },
    { "value":"prod2", "label":"Prod 2", "group":"Production" }
]
```

![顯示分組下拉式清單範例的影像](./media/workbook-dropdowns/grouped-dropDown.png)


## <a name="creating-a-dynamic-drop-down-parameter"></a>建立動態下拉式參數
1. 在編輯模式中，從空白的活頁簿開始。
2. 從活頁簿內的連結選擇 [ _加入參數_ ]。
3. 按一下藍色的 [ _加入參數_ ] 按鈕。
4. 在彈出的新參數窗格中輸入：
    1. 參數名稱： `RequestName`
    2. 參數類型： `Drop down`
    3. 必填： `checked`
    4. 允許 `multiple selection` ： `unchecked`
    5. 取得資料來源： `Query`
5. 在 JSON 輸入文字區塊中，插入此 json 程式碼片段：

    ```kusto
        requests
        | summarize by name
        | order by name asc
    ```
1. 按藍色 `Run Query` 按鈕。
2. 從工具列中選擇 [儲存] 以建立參數。
3. Myrequest 參數將會是應用程式中所有要求名稱的下拉式清單。

    ![顯示動態下拉式清單建立的影像](./media/workbook-dropdowns/dropdown-dynamic.png)

## <a name="referencing-drop-down-parameter"></a>參考下拉式參數

### <a name="in-kql"></a>在 KQL 中
1. 將查詢控制項新增至活頁簿，然後選取 Application Insights 資源。
2. 在 [KQL 編輯器] 中，輸入此程式碼片段

    ```kusto
        requests
        | where name == '{RequestName}'
        | summarize Requests = count() by bin(timestamp, 1h)

    ```
3. 這會將查詢評估時間擴展至：

    ```kusto
        requests
        | where name == 'GET Home/Index'
        | summarize Requests = count() by bin(timestamp, 1h)
    ```

4. 執行查詢以查看結果。 （選擇性）將它轉譯成圖表。

    ![顯示 KQL 中所參考之下拉式清單的影像](./media/workbook-dropdowns/dropdown-reference.png)


## <a name="parameter-value-label-selection-and-group"></a>參數值、標籤、選取專案和群組
上述動態下拉式參數中所使用的查詢，只會傳回在下拉式清單中呈現為如實轉譯的值清單。 但是如果您想要選取不同的顯示名稱，或要選取其中一個顯示名稱，該怎麼辦？ 下拉式參數可透過 [值]、[標籤]、[選取] 和 [群組] 資料行來允許。

下列範例會示範如何取得 Application Insights 相依性的清單，這些相依性的顯示名稱會以表情來樣式、已選取第一個，並依作業名稱分組。

```kusto
dependencies
| summarize by operation_Name, name
| where name !contains ('.')
| order by name asc
| serialize Rank = row_number()
| project value = name, label = strcat('🌐 ', name), selected = iff(Rank == 1, true, false), group = operation_Name
```

![顯示使用 [值]、[標籤]、[選取] 和 [群組] 選項之下拉式參數的影像](./media/workbook-dropdowns/dropdown-more-options.png)


## <a name="drop-down-parameter-options"></a>下拉式參數選項
| 參數 | 說明 | 範例 |
| ------------- |:-------------|:-------------|
| `{DependencyName}` | 選取的值 | 取得 fabrikamaccount |
| `{DependencyName:label}` | 選取的標籤 | 🌐 GET fabrikamaccount |
| `{DependencyName:value}` | 選取的值 | 取得 fabrikamaccount |

## <a name="multiple-selection"></a>多重選取
到目前為止，我們會明確地將參數設定為只在下拉式清單中選取一個值。 下拉式參數也支援 `multiple selection` -啟用這項功能與檢查選項一樣簡單 `Allow multiple selection` 。 

使用者也可以選擇透過和設定來指定結果集的格式 `delimiter` `quote with` 。 預設只會以下列格式傳回集合的值： ' a '、' b '、' c '。 它們也可以選擇限制選取專案的數目。

參考參數的 KQL 需要變更，才能使用結果的格式。 啟用此功能最常見的方法是透過 `in` 操作員。

```kusto
dependencies
| where name in ({DependencyName})
| summarize Requests = count() by bin(timestamp, 1h), name
```

以下是在工作時進行多重選取下拉式清單的範例：

![顯示多重選取下拉式參數的影像](./media/workbook-dropdowns/dropdown-multiselect.png)

## <a name="next-steps"></a>後續步驟

* [開始深入](./workbooks-overview.md#visualizations) 瞭解活頁簿許多豐富的視覺效果選項。
* [控制](workbooks-access-control.md) 和共用活頁簿資源的存取權。