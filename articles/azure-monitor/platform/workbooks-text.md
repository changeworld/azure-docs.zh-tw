---
title: Azure 監視器活頁簿文字參數
description: 使用預先建立和自訂的參數化活頁簿來簡化複雜的報表。 深入瞭解活頁簿文字參數。
services: azure-monitor
author: mrbullwinkle
manager: carmonm
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 10/23/2019
ms.author: mbullwin
ms.openlocfilehash: c804cc8942a40e2f30c980636194daa82e0fb0e8
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "81687332"
---
# <a name="workbook-text-parameters"></a>活頁簿文字參數

Textbox 參數提供簡單的方式，從活頁簿使用者收集文字輸入。 當您使用下拉式記錄來收集輸入（例如，任意臨界值或一般篩選）時，會使用它們。 活頁簿可讓作者從查詢取得文字方塊的預設值。 這可讓您根據計量的 p95 設定預設閾值，來進行有趣的案例。

文字方塊的常見用法是做為其他活頁簿控制項所使用的內部變數。 其做法是利用查詢的預設值，並讓輸入控制項不會在讀取模式中隱藏。 例如，使用者可能會想要從公式（而非使用者）取得閾值，然後在後續查詢中使用閾值。

## <a name="creating-a-text-parameter"></a>建立文字參數
1. 從編輯模式中的空白活頁簿開始。
2. 從活頁簿內的連結選擇 [_加入參數_]。
3. 按一下藍色的 [_加入參數_] 按鈕。
4. 在彈出的 [新增參數] 窗格中輸入：
    1. 參數名稱：`SlowRequestThreshold`
    2. 參數類型：`Text`
    3. 必填：`checked`
    4. 從查詢取得預設值：`unchecked`
5. 從工具列選擇 [儲存] 以建立參數。

    ![顯示文字參數建立的影像](./media/workbooks-text/text-create.png)

這是活頁簿在讀取模式中看起來的樣子。

![在讀取模式中顯示文字參數的影像](./media/workbooks-text/text-readmode.png)

## <a name="referencing-a-text-parameter"></a>參考文字參數
1. 藉由選取藍色 `Add query` 連結並選取 Application Insights 資源，將查詢控制項加入至活頁簿。
2. 在 [KQL] 方塊中，新增此程式碼片段：
    ```kusto
    requests
    | summarize AllRequests = count(), SlowRequests = countif(duration >= {SlowRequestThreshold}) by name
    | extend SlowRequestPercent = 100.0 * SlowRequests / AllRequests
    | order by SlowRequests desc
    ```
3. 藉由使用值為500的 text 參數結合查詢控制項，即可有效地執行下列查詢：
    ```kusto
    requests
    | summarize AllRequests = count(), SlowRequests = countif(duration >= 500) by name
    | extend SlowRequestPercent = 100.0 * SlowRequests / AllRequests
    | order by SlowRequests desc
    ```
4. 執行查詢以查看結果

    ![顯示 KQL 中參考之文字參數的影像](./media/workbooks-text/text-reference.png)

> [!NOTE]
> 在上述範例中， `{SlowRequestThreshold}` 代表整數值。 如果您查詢的是字串， `{ComputerName}` 您將需要修改 Kusto 查詢來加入引號，以便將 `"{ComputerName}"` 參數欄位新增至不含引號的接受輸入。

## <a name="setting-default-values"></a>設定預設值
1. 從編輯模式中的空白活頁簿開始。
2. 從活頁簿內的連結選擇 [_加入參數_]。
3. 按一下藍色的 [_加入參數_] 按鈕。
4. 在彈出的 [新增參數] 窗格中輸入：
    1. 參數名稱：`SlowRequestThreshold`
    2. 參數類型：`Text`
    3. 必填：`checked`
    4. 從查詢取得預設值：`checked`
5. 在 [KQL] 方塊中，新增此程式碼片段：
    ```kusto
    requests
    | summarize round(percentile(duration, 95), 2)
    ```
    此查詢會將此文字方塊的預設值設定為應用程式中所有要求的第95個百分位數持續時間。
6. 執行查詢以查看結果
7. 從工具列選擇 [儲存] 以建立參數。

    ![顯示具有 KQL 預設值之文字參數的影像](./media/workbooks-text/text-default-value.png)

> [!NOTE]
> 雖然此範例會查詢 Application Insights 資料，但此方法可用於任何記錄式資料來源-Log Analytics、Azure Resource Graph 等。

## <a name="next-steps"></a>後續步驟

* [開始深入](workbooks-visualizations.md)瞭解活頁簿許多豐富的視覺效果選項。
* [控制](workbooks-access-control.md)和共用您的活頁簿資源的存取權。
