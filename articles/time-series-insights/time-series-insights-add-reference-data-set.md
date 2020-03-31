---
title: 如何向環境添加參考資料集 - Azure 時間序列見解 |微軟文檔
description: 本文說明如何新增參考資料集來擴展 Azure 時間序列深入解析環境中的資料。
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.reviewer: jasonh, kfile
ms.workload: big-data
ms.topic: conceptual
ms.date: 02/07/2020
ms.custom: seodec18~~~~
ms.openlocfilehash: 79628ed44753577023464ef6208027e1b7996d30
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "77087255"
---
# <a name="create-a-reference-data-set-for-your-time-series-insights-environment-using-the-azure-portal"></a>使用 Azure 入口網站建立時間序列深入解析環境的參考資料集

本文說明如何將參考資料集新增至 Azure 時間序列深入解析環境。 將參考資料聯結至您的來源資料很實用，可以增加資料值。

參考資料集是許多項目的集合，由這些項目來擴展您事件來源中的事件。 Time Series Insights 輸入引擎會將事件來源的每個事件和參考資料集中的對應資料聯結在一起。 然後此增強的事件可用於查詢。 這項聯結是以參考資料集中定義的主索引鍵資料行為基礎。

參考資料不會回溯加入。 因此，一旦配置並上載當前和未來入口資料，才會匹配並加入參考日期集。

## <a name="video"></a>影片

### <a name="learn-about-time-series-insights-reference-data-modelbr"></a>瞭解時序洞察的參考資料模型。</br>

> [!VIDEO https://www.youtube.com/embed/Z0NuWQUMv1o]

## <a name="add-a-reference-data-set"></a>新增參考資料集

1. 登錄到 Azure[門戶](https://portal.azure.com)。

1. 查找現有的 Azure 時間序列見解環境。 在 Azure 入口網站左側的功能表中選取 [所有資源]****。 選取 Time Series Insights 環境。

1. 選取 [概觀]**** 頁面。 展開頁面頂部附近的 **"基本"** 部分，以找到**時序見解資源管理器 URL**並打開連結。  

   [![展開要點部分](media/add-reference-data-set/essentials.png)](media/add-reference-data-set/essentials.png#lightbox)

   查看時序見解環境的資源管理器。

1. 展開時間序列見解資源管理器中的環境選擇器。 選擇使用中的環境。 在 [總管] 頁面右上方選取參考資料圖示。

   [![新增參考資料](media/add-reference-data-set/tsi-select-environment-and-data-icons.png)](media/add-reference-data-set/tsi-select-environment-and-data-icons.png#lightbox)

1. 選取 [+ 新增資料集]**** 按鈕，開始新增資料集。

   [![新增資料集](media/add-reference-data-set/tsi-add-a-reference-data-set.png)](media/add-reference-data-set/tsi-add-a-reference-data-set.png#lightbox)

1. 在 [新增參考資料集]**** 頁面上，選擇資料的格式：

   - 針對逗號分隔資料選擇 [CSV]****。 第一個資料列會視為標題資料列。
   - 為 JavaScript 物件標記法 （JSON） 格式化資料選擇**JSON 陣列**。

   [![選擇資料格式。](media/add-reference-data-set/tsi-select-data-upload-option.png)](media/add-reference-data-set/tsi-select-data-upload-option.png#lightbox)

1. 使用兩種方法的其中一個來提供資料：

   - 將資料貼到文字編輯器中。 然後，選取 [剖析參考資料]**** 按鈕。
   - 選取 [選擇檔案]**** 按鈕，從本機文字檔新增資料。

   例如，粘貼 CSV 資料：[![粘貼 CSV 資料](media/add-reference-data-set/select-csv-and-enter-data.png)](media/add-reference-data-set/select-csv-and-enter-data.png#lightbox)

   例如，粘貼 JSON 陣列資料：[![粘貼 JSON 資料](media/add-reference-data-set/select-json-option-and-enter-data.png)](media/add-reference-data-set/select-json-option-and-enter-data.png#lightbox)

   如果剖析資料值時發生錯誤，錯誤會在頁面的底部顯示為紅色，例如 `CSV parsing error, no rows extracted`。

1. 一旦成功剖析資料後，會出現資料格，顯示表示資料的資料行和資料列。 檢閱資料格，以確保正確性。

   [![查看參考資料](media/add-reference-data-set/review-displayed-data-grid.png)](media/add-reference-data-set/review-displayed-data-grid.png#lightbox)

1. 查看每列以瞭解假定的資料類型，並根據需要更改資料類型。  在列標題中選擇資料類型符號：**#** 對於雙精度（數位資料 **），T*F**表示布林，或**用於字串的 Abc。**

   [![選擇欄位標題上的資料類型。](media/add-reference-data-set/select-column-types.png)](media/add-reference-data-set/select-column-types.png#lightbox)

1. 視需要將資料行標題重新命名。 索引鍵資料行名稱必須加入至事件來源中的對應屬性。 

   > [!IMPORTANT]
   > 請確定參考資料索引鍵資料行名稱與您傳入資料的名稱完全符合，包括區分大小寫。 非索引鍵資料行名稱會使用對應的參考資料值來擴充傳入資料。

1. 在 [篩選資料列...]**** 欄位中輸入值，以視需要檢閱特定資料列。 篩選對於檢視資料非常有用，但在上傳資料時不會套用。

1. 若要為資料集命名，請在資料格上方的 [資料集名稱]**** 欄位中填入名稱。

    [![為資料集命名。](media/add-reference-data-set/enter-reference-data-set-name.png)](media/add-reference-data-set/enter-reference-data-set-name.png#lightbox)

1. 選取資料格上方的下拉式清單，可提供資料集中的 [主索引鍵]**** 資料行。

    [![選取索引鍵資料行。](media/add-reference-data-set/select-primary-key-column.png)](media/add-reference-data-set/select-primary-key-column.png#lightbox)

    **（可選）** 選擇按鈕**+** 以添加輔助鍵列，作為複合主鍵。 如果您需要復原選取項目，請從下拉式清單中選擇空白值，以移除次要索引鍵。

1. 若要上傳資料，請選取 [上傳資料列]**** 按鈕。

    [![上載行並確認資料。](media/add-reference-data-set/confirm-upload-reference-data.png)](media/add-reference-data-set/confirm-upload-reference-data.png#lightbox)

    頁面會確認已完成上傳，並顯示 [已成功上傳資料集]**** 訊息。

    > [!WARNING]
    > 參考資料集之間共用的列或屬性將顯示**重複屬性名稱**上載錯誤。 該錯誤不會阻止參考資料集的成功上載。 可以通過合併共用重複屬性名稱的行來刪除它。

1. 根據需要選擇 **"添加行**、**大量匯入行**"或 **"添加列**以添加更多參考資料值"。

    [![添加行、大量匯入行或添加列。](media/add-reference-data-set/add-row-or-bulk-upload.png)](media/add-reference-data-set/add-row-or-bulk-upload.png#lightbox)

   > [!IMPORTANT]
   > 任何與另一行共用唯一鍵的行都將將其列由添加共用該唯一鍵的最後一行覆蓋。

   > [!NOTE]
   > 添加的行**不需要**是*矩形*的 - 它們可能具有與參考資料集中的其他條目不同的列。

## <a name="next-steps"></a>後續步驟

* 以程式設計的方式[管理參考資料](time-series-insights-manage-reference-data-csharp.md)。

* 有關完整的 API 引用，請閱讀[參考資料 API](https://docs.microsoft.com/rest/api/time-series-insights/ga-reference-data-api)文檔。
