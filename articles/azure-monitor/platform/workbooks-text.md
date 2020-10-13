---
title: Azure 監視器活頁簿文字參數
description: 使用預建和自訂參數化活頁簿簡化複雜的報告。 深入瞭解活頁簿文字參數。
services: azure-monitor
manager: carmonm
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 10/23/2019
ms.openlocfilehash: 7241777c0b94ce7ccadbd273c5c305c679bfe30a
ms.sourcegitcommit: 50802bffd56155f3b01bfb4ed009b70045131750
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "91932306"
---
# <a name="workbook-text-parameters"></a>活頁簿文字參數

Textbox 參數提供簡單的方式來收集活頁簿使用者的文字輸入。 當使用下拉式清單來收集輸入 (例如，) 的任意臨界值或泛型篩選準則時，就會使用這些值。 活頁簿可讓作者從查詢取得文字方塊的預設值。 這可讓您進行有趣的案例，例如根據度量的 p95 設定預設閾值。

文字方塊的常見用法是做為其他活頁簿控制項使用的內部變數。 這是利用查詢的預設值來完成，並讓輸入控制項在讀取模式中不可見。 例如，使用者可能會想要從公式 (的臨界值，而不是使用者) ，然後在後續查詢中使用臨界值。

## <a name="creating-a-text-parameter"></a>建立文字參數
1. 在編輯模式中，從空白的活頁簿開始。
2. 從活頁簿內的連結選擇 [ _加入參數_ ]。
3. 按一下藍色的 [ _加入參數_ ] 按鈕。
4. 在彈出的新參數窗格中輸入：
    1. 參數名稱： `SlowRequestThreshold`
    2. 參數類型： `Text`
    3. 必填： `checked`
    4. 從查詢取得預設值： `unchecked`
5. 從工具列中選擇 [儲存] 以建立參數。

    ![顯示文字參數建立的影像](./media/workbooks-text/text-create.png)

這就是活頁簿在讀取模式中的樣子。

![以讀取模式顯示文字參數的影像](./media/workbooks-text/text-readmode.png)

## <a name="referencing-a-text-parameter"></a>參考文字參數
1. 選取藍色 `Add query` 連結並選取 Application Insights 資源，以將查詢控制項新增至活頁簿。
2. 在 [KQL] 方塊中，新增下列程式碼片段：
    ```kusto
    requests
    | summarize AllRequests = count(), SlowRequests = countif(duration >= {SlowRequestThreshold}) by name
    | extend SlowRequestPercent = 100.0 * SlowRequests / AllRequests
    | order by SlowRequests desc
    ```
3. 藉由使用 text 參數，其值為500，並結合查詢控制項，可有效執行下列查詢：
    ```kusto
    requests
    | summarize AllRequests = count(), SlowRequests = countif(duration >= 500) by name
    | extend SlowRequestPercent = 100.0 * SlowRequests / AllRequests
    | order by SlowRequests desc
    ```
4. 執行查詢以查看結果

    ![顯示 KQL 中所參考之文字參數的影像](./media/workbooks-text/text-reference.png)

> [!NOTE]
> 在上述範例中， `{SlowRequestThreshold}` 代表一個整數值。 如果您要查詢字串，就 `{ComputerName}` 需要修改您的 Kusto 查詢，將參數欄位的引號新增至 `"{ComputerName}"` 不含引號的接受輸入。

## <a name="setting-default-values"></a>設定預設值
1. 在編輯模式中，從空白的活頁簿開始。
2. 從活頁簿內的連結選擇 [ _加入參數_ ]。
3. 按一下藍色的 [ _加入參數_ ] 按鈕。
4. 在彈出的新參數窗格中輸入：
    1. 參數名稱： `SlowRequestThreshold`
    2. 參數類型： `Text`
    3. 必填： `checked`
    4. 從查詢取得預設值： `checked`
5. 在 [KQL] 方塊中，新增下列程式碼片段：
    ```kusto
    requests
    | summarize round(percentile(duration, 95), 2)
    ```
    此查詢會針對應用程式中的所有要求，將文字方塊的預設值設定為第95個百分位數的持續時間。
6. 執行查詢以查看結果
7. 從工具列中選擇 [儲存] 以建立參數。

    ![顯示具有 KQL 預設值之文字參數的影像](./media/workbooks-text/text-default-value.png)

> [!NOTE]
> 雖然此範例會查詢 Application Insights 資料，但此方法可用於任何以記錄為基礎的資料來源-Log Analytics、Azure Resource Graph 等等。

## <a name="next-steps"></a>後續步驟

* [開始深入](workbooks-visualizations.md) 瞭解活頁簿許多豐富的視覺效果選項。
* [控制](workbooks-access-control.md) 和共用活頁簿資源的存取權。
