---
title: 如何將參考資料集新增至您的環境-Azure 時間序列深入解析 |Microsoft Docs
description: 本文說明如何新增參考資料集來擴展 Azure 時間序列深入解析環境中的資料。
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: diviso
ms.reviewer: jasonh, kfile
ms.workload: big-data
ms.topic: conceptual
ms.date: 09/30/2020
ms.custom: seodec18
ms.openlocfilehash: d80d97a609aa3a464b9b114439fe7f4058e287c3
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/21/2020
ms.locfileid: "95020141"
---
# <a name="create-a-reference-data-set-for-your-azure-time-series-insights-gen1-environment-using-the-azure-portal"></a>使用 Azure 入口網站為您的 Azure 時間序列深入解析 Gen1 環境建立參考資料集

> [!CAUTION]
> 這是 Gen1 文章。

本文說明如何將參考資料集新增至 Azure 時間序列深入解析環境。 將參考資料聯結至您的來源資料很實用，可以增加資料值。

參考資料集是許多項目的集合，由這些項目來擴展您事件來源中的事件。 Azure 時間序列深入解析輸入引擎會將事件來源中的每個事件，與參考資料集中的對應資料列聯結。 然後此增強的事件可用於查詢。 這項聯結是以參考資料集中定義的主索引鍵資料行為基礎。

參考資料不會回溯加入。 因此，在設定和上傳之後，只會比對目前與未來的輸入資料，並聯結至參考資料集。

## <a name="video"></a>影片

### <a name="learn-about-time-series-insights-reference-data-modelbr"></a>深入瞭解時間序列深入解析的參考資料模型。</br>

> [!VIDEO <https://www.youtube.com/embed/Z0NuWQUMv1o>]

## <a name="add-a-reference-data-set"></a>新增參考資料集

1. 登入 [Azure 入口網站](https://portal.azure.com)。

1. 找出您現有的 Azure 時間序列深入解析環境。 在 Azure 入口網站左側的功能表中選取 [所有資源]。 選取您的 Azure 時間序列深入解析環境。

1. 選取 [概觀] 頁面。 展開靠近頁面頂端的 [ **基本** ] 區段，找出 **時間序列深入解析總管 URL** ，然後開啟連結。  

   [![展開 [基本] 區段](media/add-reference-data-set/essentials.png)](media/add-reference-data-set/essentials.png#lightbox)

   查看 Azure 時間序列深入解析環境的 Explorer。

1. 展開 Azure 時間序列深入解析 Explorer 中的環境選取器。 選擇使用中的環境。 在 [Explorer] 頁面的右上方選取 [參考資料] 圖示。

   [![新增參考資料](media/add-reference-data-set/tsi-select-environment-and-data-icons.png)](media/add-reference-data-set/tsi-select-environment-and-data-icons.png#lightbox)

1. 選取 [+ 新增資料集] 按鈕，開始新增資料集。

   [![新增資料集](media/add-reference-data-set/tsi-add-a-reference-data-set.png)](media/add-reference-data-set/tsi-add-a-reference-data-set.png#lightbox)

1. 在 [新增參考資料集] 頁面上，選擇資料的格式：

   - 針對逗號分隔資料選擇 [CSV]。 第一個資料列會視為標題資料列。
   - 選擇 **Json 陣列** 的 JavaScript 物件標記法 (json) 格式化的資料。

   [![選擇資料格式。](media/add-reference-data-set/tsi-select-data-upload-option.png)](media/add-reference-data-set/tsi-select-data-upload-option.png#lightbox)

1. 使用兩種方法的其中一個來提供資料：

   - 將資料貼到文字編輯器中。 然後，選取 [剖析參考資料] 按鈕。
   - 選取 [選擇檔案] 按鈕，從本機文字檔新增資料。

   例如，貼上 CSV 資料：貼上的[ ![ csv 資料](media/add-reference-data-set/select-csv-and-enter-data.png)](media/add-reference-data-set/select-csv-and-enter-data.png#lightbox)

   例如，貼上 JSON 陣列資料： [ ![ 貼上 json 資料](media/add-reference-data-set/select-json-option-and-enter-data.png)](media/add-reference-data-set/select-json-option-and-enter-data.png#lightbox)

   如果剖析資料值時發生錯誤，錯誤會在頁面的底部顯示為紅色，例如 `CSV parsing error, no rows extracted`。

1. 一旦成功剖析資料後，會出現資料格，顯示表示資料的資料行和資料列。 檢閱資料格，以確保正確性。

   [![查看參考資料](media/add-reference-data-set/review-displayed-data-grid.png)](media/add-reference-data-set/review-displayed-data-grid.png#lightbox)

1. 請查看每個資料行，以瞭解假設的資料類型，並視需要變更資料類型。  選取資料行標題中的資料類型符號： **#** 雙 (數值資料) 、 **T |F** 表示布林值或 **Abc** 表示字串。

   [![選擇欄位標題上的資料類型。](media/add-reference-data-set/select-column-types.png)](media/add-reference-data-set/select-column-types.png#lightbox)

1. 視需要將資料行標題重新命名。 索引鍵資料行名稱必須加入至事件來源中的對應屬性。

   > [!IMPORTANT]
   > 請確定參考資料索引鍵資料行名稱與您傳入資料的名稱完全符合，包括區分大小寫。 非索引鍵資料行名稱會使用對應的參考資料值來擴充傳入資料。

1. 在 [篩選資料列...] 欄位中輸入值，以視需要檢閱特定資料列。 篩選對於檢視資料非常有用，但在上傳資料時不會套用。

1. 若要為資料集命名，請在資料格上方的 [資料集名稱] 欄位中填入名稱。

    [![為資料集命名。](media/add-reference-data-set/enter-reference-data-set-name.png)](media/add-reference-data-set/enter-reference-data-set-name.png#lightbox)

1. 選取資料格上方的下拉式清單，可提供資料集中的 [主索引鍵] 資料行。

    [![選取索引鍵資料行。](media/add-reference-data-set/select-primary-key-column.png)](media/add-reference-data-set/select-primary-key-column.png#lightbox)

    **(選擇性)** 選取 **+** 按鈕以新增次要索引鍵資料行，做為複合主鍵。 如果您需要復原選取項目，請從下拉式清單中選擇空白值，以移除次要索引鍵。

1. 若要上傳資料，請選取 [上傳資料列] 按鈕。

    [![上傳資料列並確認資料。](media/add-reference-data-set/confirm-upload-reference-data.png)](media/add-reference-data-set/confirm-upload-reference-data.png#lightbox)

    頁面會確認已完成上傳，並顯示 [已成功上傳資料集] 訊息。

    > [!WARNING]
    > 參考資料集之間共用的資料行或屬性會顯示 **重複的屬性名稱** 上傳錯誤。 此錯誤不會防止成功上傳參考資料集。 您可以結合共用重複屬性名稱的資料列來移除它。

1. 視需要選取 [ **加入** 資料列]、[ **大量匯入** 資料列] 或 [ **加入** 資料行]，以加入更多參考資料值。

    [![加入資料列、大量匯入資料列或加入資料行。](media/add-reference-data-set/add-row-or-bulk-upload.png)](media/add-reference-data-set/add-row-or-bulk-upload.png#lightbox)

   > [!IMPORTANT]
   > 與另一個資料列共用唯一索引鍵的任何資料列，都會將其資料行覆寫為共用該唯一索引鍵的最後一個資料列。

   > [!NOTE]
   > 新增的資料列不需要是 *矩形*，它們可能會有較少、更大或不同的資料行，而 **不** 是參考資料集中的其他專案。

## <a name="next-steps"></a>下一步

- 以程式設計的方式[管理參考資料](time-series-insights-manage-reference-data-csharp.md)。

- 如需完整的 API 參考，請參閱資料 API 檔的 [參考](/rest/api/time-series-insights/gen1-reference-data-api) 。