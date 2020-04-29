---
title: Azure 監視器活頁簿資源參數
description: 使用預先建立及使用自訂參數之活頁簿來簡化複雜的報告
services: azure-monitor
author: mrbullwinkle
manager: carmonm
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 10/23/2019
ms.author: mbullwin
ms.openlocfilehash: cc2cde7932f783f63ee2783f0589ce4f88f248a2
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "77658097"
---
# <a name="workbook-resource-parameters"></a>活頁簿資源參數

資源參數允許在活頁簿中挑選資源。 這適用于設定要從中取得資料的範圍。 例如，可讓使用者選取一組 Vm，之後的圖表會在呈現資料時使用。

來自資源選擇器的值可以來自活頁簿內容、靜態清單或 Azure Resource Graph 查詢。

## <a name="creating-a-resource-parameter-workbook-resources"></a>建立資源參數（活頁簿資源）
1. 從編輯模式中的空白活頁簿開始。
2. 從活頁簿內的連結選擇 [_加入參數_]。
3. 按一下藍色的 [_加入參數_] 按鈕。
4. 在彈出的 [新增參數] 窗格中輸入：
    1. 參數名稱：`Applications`
    2. 參數類型：`Resource picker`
    3. 必填：`checked`
    4. 允許多重選取：`checked`
5. 取得資料來源：`Workbook Resources`
6. 僅包含資源類型：`Application Insights`
7. 從工具列選擇 [儲存] 以建立參數。

![顯示使用活頁簿資源建立資源參數的影像](./media/workbooks-resources/resource-create.png)

## <a name="creating-a-resource-parameter-azure-resource-graph"></a>建立資源參數（Azure Resource Graph）
1. 從編輯模式中的空白活頁簿開始。
2. 從活頁簿內的連結選擇 [_加入參數_]。
3. 按一下藍色的 [_加入參數_] 按鈕。
4. 在彈出的 [新增參數] 窗格中輸入：
    1. 參數名稱：`Applications`
    2. 參數類型：`Resource picker`
    3. 必填：`checked`
    4. 允許多重選取：`checked`
5. 取得資料來源：`Query`
    1. 查詢類型：`Azure Resource Graph`
    2. 訂戶`Use default subscriptions`
    3. 在查詢控制項中，新增此程式碼片段
    ```kusto
    where type == 'microsoft.insights/components'
    | project value = id, label = name, selected = false, group = resourceGroup
    ```
7. 從工具列選擇 [儲存] 以建立參數。

![顯示使用 Azure Resource Graph 建立資源參數的影像](./media/workbooks-resources/resource-query.png)

> [!NOTE]
> Azure Resource Graph 尚未在所有雲端中提供。 如果您選擇此方法，請確定您的目標雲端中支援它。

[Azure Resource Graph 檔](https://docs.microsoft.com/azure/governance/resource-graph/overview)

## <a name="creating-a-resource-parameter--json-list"></a>建立資源參數（JSON 清單）
1. 從編輯模式中的空白活頁簿開始。
2. 從活頁簿內的連結選擇 [_加入參數_]。
3. 按一下藍色的 [_加入參數_] 按鈕。
4. 在彈出的 [新增參數] 窗格中輸入：
    1. 參數名稱：`Applications`
    2. 參數類型：`Resource picker`
    3. 必填：`checked`
    4. 允許多重選取：`checked`
5. 取得資料來源：`JSON`
    1. 在內容控制項中，新增此 json 程式碼片段
    ```json
    [
        { "value":"/subscriptions/<sub-id>/resourceGroups/<resource-group>/providers/<resource-type>/acmeauthentication", "label": "acmeauthentication", "selected":true, "group":"Acme Backend" },
        { "value":"/subscriptions/<sub-id>/resourceGroups/<resource-group>/providers/<resource-type>/acmeweb", "label": "acmeweb", "selected":false, "group":"Acme Frontend" }
    ]
    ```
    2. 點擊藍色的 [_更新_] 按鈕。
6. 選擇性地將`Include only resource types`設定為_Application Insights_
7. 從工具列選擇 [儲存] 以建立參數。

## <a name="referencing-a-resource-parameter"></a>參考資源參數
1. 將查詢控制項加入至活頁簿，然後選取 Application Insights 資源。
2. 使用 [ _Application Insights_ ] 下拉按鈕，將參數系結至控制項。 這麼做會將查詢的範圍設定為在執行時間由參數所傳回的資源。
4. 在 KQL 控制項中，新增此程式碼片段
    ```kusto
    requests
    | summarize Requests = count() by appName, name
    | order by Requests desc
    ```
5. 執行查詢以查看結果。 

![顯示查詢控制項中所參考之資源參數的影像](./media/workbooks-resources/resource-reference.png)

> 這種方法可用來將資源系結至其他控制項，例如計量。

## <a name="resource-parameter-options"></a>資源參數選項
| 參數 | 說明 | 範例 |
| ------------- |:-------------|:-------------|
| `{Applications}` | 選取的資源識別碼 | _/subscriptions/<子識別碼>/resourceGroups/<資源群組>/providers/<資源類型>/acmeauthentication_ |
| `{Applications:label}` | 所選取資源的標籤 | `acmefrontend` |
| `{Applications:value}` | 所選取資源的值 | _'/subscriptions/<子識別碼>/resourceGroups/<資源群組>/providers/<資源類型>/acmeauthentication '_ |
| `{Applications:name}` | 所選取資源的名稱 | `acmefrontend` |
| `{Applications:resourceGroup}` | 所選取資源的資源群組 | `acmegroup` |
| `{Applications:resourceType}` | 所選取資源的類型 | _microsoft insights/元件_ |
| `{Applications:subscription}` | 所選取資源的訂用帳戶 |  |
| `{Applications:grid}` | 顯示資源屬性的方格。 在進行調試時于文字區塊中轉譯很有用  |  |

## <a name="next-steps"></a>後續步驟

* [開始深入](workbooks-visualizations.md)瞭解活頁簿許多豐富的視覺效果選項。
* [控制](workbooks-access-control.md)和共用您的活頁簿資源的存取權。
