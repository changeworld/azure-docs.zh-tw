---
title: Azure 監視活頁簿資源參數
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "77658097"
---
# <a name="workbook-resource-parameters"></a>活頁簿資源參數

資源參數允許在活頁簿中選取資源。 這在設置從中獲取資料的範圍時非常有用。 一個例子是允許使用者選擇 VM 集，圖表稍後將在呈現資料時使用這些 VM。

來自資源選取器的值可能來自活頁簿上下文、靜態清單或 Azure 資源圖查詢。

## <a name="creating-a-resource-parameter-workbook-resources"></a>創建資源參數（活頁簿資源）
1. 從編輯模式下的空活頁簿開始。
2. 從活頁簿中的連結中選擇 _"添加參數_"。
3. 按一下藍色 _"添加參數"_ 按鈕。
4. 在彈出的新參數窗格中，輸入：
    1. 參數名稱：`Applications`
    2. 參數類型：`Resource picker`
    3. 必填：`checked`
    4. 允許多種選擇：`checked`
5. 從：`Workbook Resources`
6. 僅包括資源類型：`Application Insights`
7. 從工具列中選擇"保存"以創建參數。

![顯示使用活頁簿資源創建資源參數的圖像](./media/workbooks-resources/resource-create.png)

## <a name="creating-a-resource-parameter-azure-resource-graph"></a>創建資源參數（Azure 資源圖）
1. 從編輯模式下的空活頁簿開始。
2. 從活頁簿中的連結中選擇 _"添加參數_"。
3. 按一下藍色 _"添加參數"_ 按鈕。
4. 在彈出的新參數窗格中，輸入：
    1. 參數名稱：`Applications`
    2. 參數類型：`Resource picker`
    3. 必填：`checked`
    4. 允許多種選擇：`checked`
5. 從：`Query`
    1. 查詢類型：`Azure Resource Graph`
    2. 訂閱：`Use default subscriptions`
    3. 在查詢控制項中，添加此程式碼片段
    ```kusto
    where type == 'microsoft.insights/components'
    | project value = id, label = name, selected = false, group = resourceGroup
    ```
7. 從工具列中選擇"保存"以創建參數。

![顯示使用 Azure 資源圖創建資源參數的圖像](./media/workbooks-resources/resource-query.png)

> [!NOTE]
> Azure 資源圖尚未在所有雲中可用。 如果選擇此方法，請確保目標雲中支援它。

[Azure 資源圖文檔](https://docs.microsoft.com/azure/governance/resource-graph/overview)

## <a name="creating-a-resource-parameter--json-list"></a>創建資源參數（JSON 清單）
1. 從編輯模式下的空活頁簿開始。
2. 從活頁簿中的連結中選擇 _"添加參數_"。
3. 按一下藍色 _"添加參數"_ 按鈕。
4. 在彈出的新參數窗格中，輸入：
    1. 參數名稱：`Applications`
    2. 參數類型：`Resource picker`
    3. 必填：`checked`
    4. 允許多種選擇：`checked`
5. 從：`JSON`
    1. 在內容控制項中，添加此 json 程式碼片段
    ```json
    [
        { "value":"/subscriptions/<sub-id>/resourceGroups/<resource-group>/providers/<resource-type>/acmeauthentication", "label": "acmeauthentication", "selected":true, "group":"Acme Backend" },
        { "value":"/subscriptions/<sub-id>/resourceGroups/<resource-group>/providers/<resource-type>/acmeweb", "label": "acmeweb", "selected":false, "group":"Acme Frontend" }
    ]
    ```
    2. 點擊藍色_更新_按鈕。
6. 可選地將`Include only resource types`設置為_應用程式見解_
7. 從工具列中選擇"保存"以創建參數。

## <a name="referencing-a-resource-parameter"></a>引用資源參數
1. 向活頁簿添加查詢控制項並選擇應用程式見解資源。
2. 使用下拉_的應用程式見解_將參數綁定到控制項。 執行此操作會將查詢的範圍設置到運行時參數返回的資源。
4. 在 KQL 控制項中，添加此程式碼片段
    ```kusto
    requests
    | summarize Requests = count() by appName, name
    | order by Requests desc
    ```
5. 執行查詢以查看結果。 

![顯示查詢控制項中引用的資源參數的圖像](./media/workbooks-resources/resource-reference.png)

> 此方法可用於將資源綁定到其他控制項（如指標）。

## <a name="resource-parameter-options"></a>資源參數選項
| 參數 | 說明 | 範例 |
| ------------- |:-------------|:-------------|
| `{Applications}` | 所選資源識別碼 | _/訂閱/<子 id>/資源組/<資源組>/提供程式/<資源類型>/acme 身份驗證_ |
| `{Applications:label}` | 所選資源的標籤 | `acmefrontend` |
| `{Applications:value}` | 所選資源的值 | _'/訂閱/<子 id>/資源組/<資源組>/提供程式/<資源類型>/acme 身份驗證"_ |
| `{Applications:name}` | 所選資源的名稱 | `acmefrontend` |
| `{Applications:resourceGroup}` | 所選資源的資源組 | `acmegroup` |
| `{Applications:resourceType}` | 所選資源的類型 | _微軟.insights/元件_ |
| `{Applications:subscription}` | 所選資源的訂閱 |  |
| `{Applications:grid}` | 顯示資源屬性的網格。 調試時在文字區塊中渲染很有用  |  |

## <a name="next-steps"></a>後續步驟

* [開始](workbooks-visualizations.md)瞭解有關活頁簿的許多豐富視覺化選項的詳細資訊。
* [控制和](workbooks-access-control.md)共用對活頁簿資源的存取權限。
