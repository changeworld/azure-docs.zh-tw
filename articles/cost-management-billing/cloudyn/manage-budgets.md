---
title: 在 Azure 中管理 Cloudyn 預算
description: 本文可協助您在 Cloudyn 中快速建立預算，並開始管理這些預算。
author: bandersmsft
ms.author: banders
ms.date: 03/12/2020
ms.topic: conceptual
ms.service: cost-management-billing
ms.subservice: cloudyn
ms.reviewer: vitavor
ms.custom: seodec18
ROBOTS: NOINDEX
ms.openlocfilehash: b7a26e7d94bbfea77bfc8121923377a086aef037
ms.sourcegitcommit: 56cbd6d97cb52e61ceb6d3894abe1977713354d9
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/20/2020
ms.locfileid: "88690862"
---
# <a name="manage-azure-budgets-with-cloudyn"></a>使用 Cloudyn 管理 Azure 預算

設定預算及以預算為基礎的警示，有助於改善您雲端的控管和責任歸屬。 本文可協助您在 Cloudyn 中快速建立預算，並開始管理這些預算。

如果您有企業帳戶或 MSP 帳戶，您可以使用您的階層式成本實體結構，將每月預算配額指派給不同的業務單位、部門或任何其他成本實體。 如果您有進階帳戶，您可以使用預算管理功能，然後將其套用至整個雲端支出。 所有預算皆為手動指派。

藉由指派的預算，您可以根據耗用的預算百分比來設定閾值警示，並定義每個閾值的嚴重性。

預算報表會顯示指派的預算。 使用者可以檢視他們的支出何時超過、低於或等於一段時間中的耗用量。 當您選取預算報表頂端的 [顯示/隱藏欄位]  時，您可以檢視成本、預算、累積成本或總預算。

「Azure 成本管理」提供與 Cloudyn 類似的功能。 「Azure 成本管理」是原生的 Azure 成本管理解決方案。 它可協助您分析成本、建立及管理預算、匯出資料，以及檢閱最佳化建議並依據這些建議採取動作來節省成本。 如需有關成本管理中預算的詳細資訊，請參閱[建立及管理預算](../costs/tutorial-acm-create-budgets.md)。

[!INCLUDE [cloudyn-note](../../../includes/cloudyn-note.md)]

## <a name="create-budgets"></a>建立預算

當您建立預算時，需針對您的會計年度設定預算，接著此預算會套用至特定實體。

建立預算並將其指派至實體：

1. 瀏覽至 [成本]  &gt; [成本管理]  &gt; [預算]  。
2. 在預算管理頁面上的 [實體]  下方，選取您要在其中建立預算的實體。
3. 在預算的年份中，選取您要建立預算的年份。
4. 針對每個月設定預算值。 完成後，按一下 [儲存]  。
在此範例中，2018 年 6 月的每月預算已設定為 $135,000。 年度總預算是 $1,615,000.00。
![建立用來設定各月份預算的預算頁面](./media/manage-budgets/set-budget.png)


匯入檔案作為每年的預算：

1. 在 [動作]  下方，選取 [匯出]  來下載作為預算基礎的空白 CSV 範本。
2. 將預算項目填入 CSV 檔案，並將它儲存在本機。
3. 選取 [動作]  下方的 [匯入]  。
4. 選取您儲存的檔案，然後按一下 [確定]  。

若要將已完成的預算匯出為 CSV 檔案，按一下 [動作]  下方的 [匯出]  來下載檔案。

## <a name="view-budget-in-reports"></a>在報表中檢視預算

預算完成時會顯示在 [成本]  &gt; [成本分析]  下的大部分成本報告中，也會顯示在「隨時間變化的成本和預算」報表中。 您也可以使用 [動作]  ，根據預算閾值來排程報表。

以下是成本分析報表的範例。 其中顯示從年初開始的總預算，以及各工作負載和使用類型的成本。

![包含預算的成本分析報表範例](./media/manage-budgets/cost-analysis-budget-example.png)

在此範例中，假設目前日期為 6 月 22 日。 2018 年 6 月的成本是 $71,611.28，對比的每月預算為 $135,000。 成本遠低於每月預算，因為到月底前仍會有 8 天的支出。

檢視報表的另一個方法是查看累積成本與您的預算。 若要查看累積成本，請在 [顯示/隱藏欄位]  下方選取 [累積成本]  和 [總預算]  。 以下是從年初開始的累積成本範例。

![顯示於下列報告中的範例累積成本和總預算：隨時間變化的成本和預算報告](./media/manage-budgets/accumulated-budget.png)

未來中的某些時候，您的累積成本可能會超過預算。 若要更輕鬆地看到此狀況，您可以將圖表檢視變更為「折線」  類型。

![以折線圖顯示於每月成本報告中的預算](./media/manage-budgets/budget-line.png)

## <a name="create-budget-alerts-for-a-filter"></a>針對篩選條件建立預算警示

在上述範例中，您可以看到累積成本已接近預算。 您可以建立自動的預算警示，讓您在支出接近或超過預算時收到通知。 基本上，警示會具有閾值的已排程報表。 預算警示的閾值計量包括：

- 剩餘成本與預算 – 指定貨幣值的閾值
- 成本百分比與預算 – 指定百分比值的閾值

讓我們看看以下範例。

在「隨時間變化的成本和預算」中，按一下 [動作]  ，然後選取 [排程報表]  。 在 [閾值] 索引標籤中，選取閾值計量。 例如，[成本百分比與預算]  。 選取警示類型，並輸入預算的百分比值。 如果您只想要收到一次通知，選取 [連續警示的數目]  ，然後輸入 _1_。 按一下 [檔案]  。

![在 [儲存或排程此報告] 方塊中建立預算警示](./media/manage-budgets/budget-alert.png)

## <a name="next-steps"></a>後續步驟

- 如果您尚未完成 Cloudyn 的第一個教學課程，請閱讀[檢閱使用量和成本](tutorial-review-usage.md)。
- 深入了解 [Cloudyn中可用的報表](use-reports.md)。
