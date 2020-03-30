---
title: Azure 監視器活頁簿文本參數
description: 使用預構建和自訂參數化活頁簿簡化複雜報告。 詳細瞭解活頁簿文本參數。
services: azure-monitor
author: mrbullwinkle
manager: carmonm
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 10/23/2019
ms.author: mbullwin
ms.openlocfilehash: c5fb585d0eb6aeb7866c2ab04b324ee31fe903ca
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "77658044"
---
# <a name="workbook-text-parameters"></a>活頁簿文本參數

文字方塊參數提供了一種從活頁簿使用者收集文本輸入的簡單方法。 當使用下拉清單來收集輸入（例如，任意閾值或泛型篩選器）時，使用這些輸入。 活頁簿允許作者從查詢中獲取文字方塊的預設值。 這允許有趣的方案，如根據指標的 p95 設置預設閾值。

文字方塊的常見用途是其他活頁簿控制項使用的內部變數。 這是通過利用預設值的查詢，並使輸入控制項在讀取模式下不可見來實現的。 例如，使用者可能希望閾值來自公式（而不是使用者），然後在後續查詢中使用閾值。

## <a name="creating-a-text-parameter"></a>創建文本參數
1. 從編輯模式下的空活頁簿開始。
2. 從活頁簿中的連結中選擇 _"添加參數_"。
3. 按一下藍色 _"添加參數"_ 按鈕。
4. 在彈出的新參數窗格中，輸入：
    1. 參數名稱：`SlowRequestThreshold`
    2. 參數類型：`Text`
    3. 必填：`checked`
    4. 從查詢獲取預設值：`unchecked`
5. 從工具列中選擇"保存"以創建參數。

    ![顯示文本參數創建的圖像](./media/workbooks-text/text-create.png)

這就是活頁簿在閱讀模式下的樣子。

![在讀取模式下顯示文本參數的圖像](./media/workbooks-text/text-readmode.png)

## <a name="referencing-a-text-parameter"></a>引用文本參數
1. 通過選擇藍色`Add query`連結並選擇"應用程式見解"資源，將查詢控制項添加到活頁簿。
2. 在 KQL 框中，添加以下程式碼片段：
    ```kusto
    requests
    | summarize AllRequests = count(), SlowRequests = countif(duration >= {SlowRequestThreshold}) by name
    | extend SlowRequestPercent = 100.0 * SlowRequests / AllRequests
    | order by SlowRequests desc
    ```
3. 通過使用值為 500 的文本參數與查詢控制項結合使用，您可以有效地運行下面的查詢：
    ```kusto
    requests
    | summarize AllRequests = count(), SlowRequests = countif(duration >= 500) by name
    | extend SlowRequestPercent = 100.0 * SlowRequests / AllRequests
    | order by SlowRequests desc
    ```
4. 執行查詢以查看結果

    ![顯示 KQL 中引用的文本參數的圖像](./media/workbooks-text/text-reference.png)


## <a name="setting-default-values"></a>設置預設值
1. 從編輯模式下的空活頁簿開始。
2. 從活頁簿中的連結中選擇 _"添加參數_"。
3. 按一下藍色 _"添加參數"_ 按鈕。
4. 在彈出的新參數窗格中，輸入：
    1. 參數名稱：`SlowRequestThreshold`
    2. 參數類型：`Text`
    3. 必填：`checked`
    4. 從查詢獲取預設值：`checked`
5. 在 KQL 框中，添加以下程式碼片段：
    ```kusto
    requests
    | summarize round(percentile(duration, 95), 2)
    ```
    此查詢將文字方塊的預設值設置為應用中所有請求的第 95 個百分位持續時間。
6. 執行查詢以查看結果
7. 從工具列中選擇"保存"以創建參數。

    ![顯示具有 KQL 預設值的文本參數的圖像](./media/workbooks-text/text-default-value.png)

> [!NOTE]
> 此依例查詢應用程式見解資料時，該方法可用於任何基於日誌的資料來源 - 日誌分析、Azure 資源圖等。

## <a name="next-steps"></a>後續步驟

* [開始](workbooks-visualizations.md)瞭解有關活頁簿的許多豐富視覺化選項的詳細資訊。
* [控制和](workbooks-access-control.md)共用對活頁簿資源的存取權限。
