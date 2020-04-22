---
title: Azure 監視器工作簿文字參數
description: 使用預建構和自定義參數化工作簿簡化複雜報告。 詳細瞭解工作簿文本參數。
services: azure-monitor
author: mrbullwinkle
manager: carmonm
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 10/23/2019
ms.author: mbullwin
ms.openlocfilehash: c804cc8942a40e2f30c980636194daa82e0fb0e8
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/21/2020
ms.locfileid: "81687332"
---
# <a name="workbook-text-parameters"></a>工作簿文字參數

文字框參數提供了一種從工作簿使用者收集文本輸入的簡單方法。 當使用下拉清單來收集輸入(例如,任意閾值或泛型篩選器)時,使用這些輸入。 工作簿允許作者從查詢中獲取文本框的預設值。 這允許有趣的方案,如根據指標的 p95 設置預設閾值。

文本框的常見用途是其他工作簿控制件使用的內部變數。 這是通過利用預設值的查詢,並使輸入控制項在讀取模式下不可見來實現的。 例如,使用者可能希望閾值來自公式(而不是使用者),然後在後續查詢中使用閾值。

## <a name="creating-a-text-parameter"></a>建立文字參數
1. 從編輯模式下的空工作簿開始。
2. 從工作簿中的連結中選擇 _「添加參數_」。
3. 按下藍色 _「新增參數」_ 按鈕。
4. 在彈出的新參數窗格中,輸入:
    1. 參數名稱:`SlowRequestThreshold`
    2. 參數類型:`Text`
    3. 必填:`checked`
    4. 從查詢取得預設值:`unchecked`
5. 從工具列中選擇"儲存"以建立參數。

    ![顯示文字參數建立的影像](./media/workbooks-text/text-create.png)

這就是工作簿在閱讀模式下的樣子。

![在讀取模式下顯示文字參數的影像](./media/workbooks-text/text-readmode.png)

## <a name="referencing-a-text-parameter"></a>引言文字參數
1. 通過選擇藍色`Add query`連結並選擇"應用程式見解"資源,將查詢控制項添加到工作簿。
2. 在 KQL 框中,新增以下代碼段:
    ```kusto
    requests
    | summarize AllRequests = count(), SlowRequests = countif(duration >= {SlowRequestThreshold}) by name
    | extend SlowRequestPercent = 100.0 * SlowRequests / AllRequests
    | order by SlowRequests desc
    ```
3. 使用值為 500 的文字參數與查詢控制項結合使用,您可以有效地執行下列的查詢:
    ```kusto
    requests
    | summarize AllRequests = count(), SlowRequests = countif(duration >= 500) by name
    | extend SlowRequestPercent = 100.0 * SlowRequests / AllRequests
    | order by SlowRequests desc
    ```
4. 執行查詢以檢視結果

    ![顯示 KQL 中引用的文字參數的影像](./media/workbooks-text/text-reference.png)

> [!NOTE]
> 在上面的範例中,`{SlowRequestThreshold}`表示一個整數值。 如果要查詢字串,則`{ComputerName}`需要修改 Kusto 查詢以添加引`"{ComputerName}"`號 ,以便將參數位段添加到沒有引號的接受輸入。

## <a name="setting-default-values"></a>設定預設值
1. 從編輯模式下的空工作簿開始。
2. 從工作簿中的連結中選擇 _「添加參數_」。
3. 按下藍色 _「新增參數」_ 按鈕。
4. 在彈出的新參數窗格中,輸入:
    1. 參數名稱:`SlowRequestThreshold`
    2. 參數類型:`Text`
    3. 必填:`checked`
    4. 從查詢取得預設值:`checked`
5. 在 KQL 框中,新增以下代碼段:
    ```kusto
    requests
    | summarize round(percentile(duration, 95), 2)
    ```
    此查詢將文本框的預設值設置為應用中所有請求的第 95 個百分位持續時間。
6. 執行查詢以檢視結果
7. 從工具列中選擇"儲存"以建立參數。

    ![顯示具有 KQL 預設值的文字參數的影像](./media/workbooks-text/text-default-value.png)

> [!NOTE]
> 此示例查詢應用程式見解數據時,該方法可用於任何基於日誌的數據源 - 日誌分析、Azure 資源圖等。

## <a name="next-steps"></a>後續步驟

* [開始](workbooks-visualizations.md)瞭解有關工作簿的許多豐富可視化選項的詳細資訊。
* [控制和](workbooks-access-control.md)共用對工作簿資源的訪問許可權。
