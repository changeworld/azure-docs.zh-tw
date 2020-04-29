---
title: Azure 監視器活頁簿下拉參數
description: 使用預先建立和自訂的參數化活頁簿（包含下拉式參數）簡化複雜的報表
services: azure-monitor
author: mrbullwinkle
manager: carmonm
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 10/23/2019
ms.author: mbullwin
ms.openlocfilehash: f3220a363025d80fd7636dbfc3af3d2d9d7bc040
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "77658275"
---
# <a name="workbook-drop-down-parameters"></a>活頁簿下拉參數

下拉式清單可讓使用者從已知的集合中收集一或多個輸入值（例如，選取您應用程式的其中一個要求）。 下拉式清單提供方便使用者從使用者收集任意輸入的方式。 下拉式清單特別適用于在您的互動式報表中啟用篩選。 

若要指定下拉式清單，最簡單的方法是在參數設定中提供靜態清單。 更有趣的方法是透過 KQL 查詢來動態取得清單。 參數設定也可讓您指定是單一或多重選取，如果它是多重選取，則應如何格式化結果集（分隔符號、引號等等）。

## <a name="creating-a-static-drop-down-parameter"></a>建立靜態下拉式參數

1. 從編輯模式中的空白活頁簿開始。
2. 從活頁簿內的連結選擇 [_加入參數_]。
3. 按一下藍色的 [_加入參數_] 按鈕。
4. 在彈出的 [新增參數] 窗格中輸入：
    1. 參數名稱：`Environment`
    2. 參數類型：`Drop down`
    3. 必填：`checked`
    4. 允許`multiple selection`：`unchecked`
    5. 取得資料來源：`JSON`
5. 在 [JSON 輸入] 文字區塊中，插入下列 json 程式碼片段：
    ```json
    [
        { "value":"dev", "label":"Development" },
        { "value":"ppe", "label":"Pre-production" },
        { "value":"prod", "label":"Production", "selected":true }
    ]
    ```
6. 按藍色`Update`按鈕。
7. 從工具列選擇 [儲存] 以建立參數。
8. 環境參數將會是具有三個值的下拉式按鈕。

    ![顯示建立靜態下拉式清單的影像](./media/workbook-dropdowns/dropdown-create.png)

## <a name="creating-a-static-dropdown-with-groups-of-items"></a>建立具有專案群組的靜態下拉式清單
如果您的查詢結果/json 包含「群組」欄位，則下拉式清單會顯示值的群組。 請遵循上述範例，但改為使用下列 json：
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
    ![Image showing an example of a grouped dropdown](./media/workbook-dropdowns/grouped-dropDown.png)


## <a name="creating-a-dynamic-drop-down-parameter"></a>建立動態下拉式參數
1. 從編輯模式中的空白活頁簿開始。
2. 從活頁簿內的連結選擇 [_加入參數_]。
3. 按一下藍色的 [_加入參數_] 按鈕。
4. 在彈出的 [新增參數] 窗格中輸入：
    1. 參數名稱：`RequestName`
    2. 參數類型：`Drop down`
    3. 必填：`checked`
    4. 允許`multiple selection`：`unchecked`
    5. 取得資料來源：`Query`
5. 在 [JSON 輸入] 文字區塊中，插入下列 json 程式碼片段：

    ```kusto
        requests
        | summarize by name
        | order by name asc
    ```
1. 按藍色`Run Query`按鈕。
2. 從工具列選擇 [儲存] 以建立參數。
3. Myrequest 參數將會是應用程式中所有要求的名稱下拉式。

    ![顯示動態下拉式的建立的影像](./media/workbook-dropdowns/dropdown-dynamic.png)

## <a name="referencing-drop-down-parameter"></a>參考下拉式的參數
### <a name="in-kql"></a>在 KQL 中
1. 將查詢控制項加入至活頁簿，然後選取 Application Insights 資源。
2. 在 [KQL 編輯器] 中，輸入此程式碼片段

    ```kusto
        requests
        | where name == '{RequestName}'
        | summarize Requests = count() by bin(timestamp, 1h)

    ```
3. 這會展開查詢評估時間以：

    ```kusto
        requests
        | where name == 'GET Home/Index'
        | summarize Requests = count() by bin(timestamp, 1h)
    ```

4. 執行查詢以查看結果。 （選擇性）將它呈現為圖表。

    ![顯示 KQL 中參考之下拉式的影像](./media/workbook-dropdowns/dropdown-reference.png)


## <a name="parameter-value-label-selection-and-group"></a>參數值、標籤、選取範圍和群組
上述動態下拉式參數中所使用的查詢，只會傳回下拉式清單中呈現的一組值。 但是，如果您想要使用不同的顯示名稱，或要選取其中一項，該怎麼辦？ 下拉式參數可讓您透過值、標籤、選取和群組資料行來進行這項工作。

下列範例示範如何取得 Application Insights 相依性的清單，這些相依性的顯示名稱是以表情、已選取第一個專案，並依作業名稱分組。

```kusto
dependencies
| summarize by operation_Name, name
| where name !contains ('.')
| order by name asc
| serialize Rank = row_number()
| project value = name, label = strcat('🌐 ', name), selected = iff(Rank == 1, true, false), group = operation_Name
```
    ![Image showing a drop-down parameter using value, label, selection and group options](./media/workbook-dropdowns/dropdown-more-options.png)


## <a name="drop-down-parameter-options"></a>下拉參數選項
| 參數 | 說明 | 範例 |
| ------------- |:-------------|:-------------|
| `{DependencyName}` | 選取的值 | 取得 fabrikamaccount |
| `{DependencyName:label}` | 選取的標籤 | 🌐取得 fabrikamaccount |
| `{DependencyName:value}` | 選取的值 | 取得 fabrikamaccount |

## <a name="multiple-selection"></a>多重選取
到目前為止，範例會明確地設定參數，只在下拉式選單中選取一個值。 下拉式參數也支援`multiple selection` -啟用此功能就像檢查`Allow multiple selection`選項一樣簡單。 

使用者也可以選擇透過`delimiter`和`quote with`設定來指定結果集的格式。 預設只會以下列形式以集合形式傳回值： ' a '、' b '、' c '。 它們也可以選擇限制選取次數。

參考參數的 KQL 必須變更，才能使用結果的格式。 啟用它的最常見方式是透過`in`運算子。

```kusto
dependencies
| where name in ({DependencyName})
| summarize Requests = count() by bin(timestamp, 1h), name
```

以下是在工作時多選下拉式的範例：

![顯示多重選取下拉式參數的影像](./media/workbook-dropdowns/dropdown-multiselect.png)

## <a name="next-steps"></a>後續步驟

* [開始深入](workbooks-visualizations.md)瞭解活頁簿許多豐富的視覺效果選項。
* [控制](workbooks-access-control.md)和共用您的活頁簿資源的存取權。
