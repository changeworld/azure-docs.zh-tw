---
title: Azure 監視器活頁簿下拉參數
description: 使用包含下拉參數的預構建和自訂參數化活頁簿簡化複雜報告
services: azure-monitor
author: mrbullwinkle
manager: carmonm
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 10/23/2019
ms.author: mbullwin
ms.openlocfilehash: f3220a363025d80fd7636dbfc3af3d2d9d7bc040
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "77658275"
---
# <a name="workbook-drop-down-parameters"></a>活頁簿下拉參數

下拉允許使用者從已知集收集一個或多個輸入值（例如，選擇應用的請求之一）。 下拉提供一種方便使用的方式，用於收集使用者提供的任意輸入。 下拉清單在互動式報表中啟用篩選特別有用。 

指定下拉清單的最簡單方法是在參數設置中提供靜態清單。 更有趣的方法是通過 KQL 查詢動態獲取清單。 參數設置還允許您指定它是單選還是多選，如果是多選，則結果集的格式應如何設置（分隔符號、報價單等）。

## <a name="creating-a-static-drop-down-parameter"></a>創建靜態下拉參數

1. 從編輯模式下的空活頁簿開始。
2. 從活頁簿中的連結中選擇 _"添加參數_"。
3. 按一下藍色 _"添加參數"_ 按鈕。
4. 在彈出的新參數窗格中，輸入：
    1. 參數名稱：`Environment`
    2. 參數類型：`Drop down`
    3. 必填：`checked`
    4. 允許`multiple selection`：`unchecked`
    5. 從：`JSON`
5. 在 JSON 輸入文字區塊中，插入此 json 程式碼片段：
    ```json
    [
        { "value":"dev", "label":"Development" },
        { "value":"ppe", "label":"Pre-production" },
        { "value":"prod", "label":"Production", "selected":true }
    ]
    ```
6. 點擊藍色`Update`按鈕。
7. 從工具列中選擇"保存"以創建參數。
8. "環境"參數將是三個值的下拉。

    ![顯示靜態淹沒創建的圖像](./media/workbook-dropdowns/dropdown-create.png)

## <a name="creating-a-static-dropdown-with-groups-of-items"></a>使用專案組創建靜態下拉清單
如果查詢結果/json 包含"組"欄位，下拉將顯示值組。 請按照上述示例操作，而是使用以下 json：
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


## <a name="creating-a-dynamic-drop-down-parameter"></a>創建動態下拉參數
1. 從編輯模式下的空活頁簿開始。
2. 從活頁簿中的連結中選擇 _"添加參數_"。
3. 按一下藍色 _"添加參數"_ 按鈕。
4. 在彈出的新參數窗格中，輸入：
    1. 參數名稱：`RequestName`
    2. 參數類型：`Drop down`
    3. 必填：`checked`
    4. 允許`multiple selection`：`unchecked`
    5. 從：`Query`
5. 在 JSON 輸入文字區塊中，插入此 json 程式碼片段：

    ```kusto
        requests
        | summarize by name
        | order by name asc
    ```
1. 點擊藍色`Run Query`按鈕。
2. 從工具列中選擇"保存"以創建參數。
3. "請求名稱"參數將是應用中所有請求的名稱的下拉清單。

    ![顯示動態下拉清單創建的圖像](./media/workbook-dropdowns/dropdown-dynamic.png)

## <a name="referencing-drop-down-parameter"></a>引用下拉參數
### <a name="in-kql"></a>在 KQL 中
1. 向活頁簿添加查詢控制項並選擇應用程式見解資源。
2. 在 KQL 編輯器中，輸入此程式碼片段

    ```kusto
        requests
        | where name == '{RequestName}'
        | summarize Requests = count() by bin(timestamp, 1h)

    ```
3. 這將在查詢評估時間上擴展為：

    ```kusto
        requests
        | where name == 'GET Home/Index'
        | summarize Requests = count() by bin(timestamp, 1h)
    ```

4. 執行查詢以查看結果。 或者，將其呈現為圖表。

    ![顯示 KQL 中引用的下拉清單的圖像](./media/workbook-dropdowns/dropdown-reference.png)


## <a name="parameter-value-label-selection-and-group"></a>參數值、標籤、選擇和組
上面動態下拉參數中使用的查詢將返回在下拉清單中忠實地呈現的值清單。 但是，如果您希望選擇不同的顯示名稱或其中一個顯示名稱，該怎麼辦？ 下拉參數允許通過值、標籤、選擇和組列進行此參數。

下面的示例演示如何獲取應用程式見解依賴項的清單，其顯示名稱具有表情符號的樣式，選擇了第一個，並且按操作名稱分組。

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
| `{DependencyName}` | 所選值 | GET 法布裡卡姆帳戶 |
| `{DependencyName:label}` | 所選標籤 | 🌐 GET fabrikam 帳戶 |
| `{DependencyName:value}` | 所選值 | GET 法布裡卡姆帳戶 |

## <a name="multiple-selection"></a>多重選取
到目前為止，示例顯式設置參數，以便在下拉清單中只選擇一個值。 下拉參數也支援`multiple selection`- 啟用此功能非常簡單，`Allow multiple selection`只需檢查選項即可。 

使用者還可以選擇通過`delimiter`和`quote with`設置指定結果集的格式。 預設值僅將值作為此表單中的集合返回："a"、"b"、"c"。 它們還可以選擇限制選擇的數量。

傳址參數的 KQL 需要更改才能使用結果的格式。 啟用它的最常見方法是通過`in`操作員。

```kusto
dependencies
| where name in ({DependencyName})
| summarize Requests = count() by bin(timestamp, 1h), name
```

下面是多選下拉工作的示例：

![顯示多選下拉參數的圖像](./media/workbook-dropdowns/dropdown-multiselect.png)

## <a name="next-steps"></a>後續步驟

* [開始](workbooks-visualizations.md)瞭解有關活頁簿的許多豐富視覺化選項的詳細資訊。
* [控制和](workbooks-access-control.md)共用對活頁簿資源的存取權限。
