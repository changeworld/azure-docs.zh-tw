---
title: 如何：流覽資料目錄
description: 本文概述如何根據資產類型流覽 Azure 範疇資料目錄。
author: chanuengg
ms.author: csugunan
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: conceptual
ms.date: 11/30/2020
ms.openlocfilehash: dd2fd4fbc9d8aecd9821d668dd02bd4b68659cf6
ms.sourcegitcommit: b6267bc931ef1a4bd33d67ba76895e14b9d0c661
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/19/2020
ms.locfileid: "97695059"
---
# <a name="browse-the-azure-purview-data-catalog"></a>瀏覽 Azure Purview 資料目錄

本文說明如何使用資料來源階層命名空間，探索 Azure 範疇資料目錄中的資料。

## <a name="browse-experience"></a>流覽體驗

資料取用者可以使用瀏覽器視圖，針對每個資料來源使用熟悉的階層命名空間探索資料。 一旦資料來源註冊並掃描之後，資料對應就會將資料來源的結構 (階層式命名空間) 的相關資訊解壓縮。 這項資訊是用來建立資料探索的流覽體驗。

例如，您可以輕鬆地在 Azure Data lake Storage Gen 2 中稱為 *維度* 的資料夾下，找到名為 *DateDimension* 的資料集。 您可以使用 [依資產類型流覽] 體驗來流覽至 ADLS Gen 2 儲存體帳戶，然後流覽至服務 > 容器 > 資料夾 (s) 以連接到特定的 [ *維度* ] 資料夾，然後查看 *DateDimension* 資料表。

針對每個對應的資料來源提供具有階層命名空間的原生流覽體驗。

## <a name="browse-the-data-catalog-by-asset-type"></a>依資產類型流覽資料目錄

1. 您可以藉由選取首頁上的 [ **依資產類型流覽]** 來流覽資料資產。

    :::image type="content" source="media/how-to-browse-catalog/studio-home-page.png" alt-text="範疇首頁" border="true":::

1. 在 [ **流覽資產類型]** 頁面上，圖格會依資料來源分類。 若要進一步探索每個資料來源中的資產，請選取對應的磚。

    :::image type="content" source="media/how-to-browse-catalog/browse-asset-types.jpg" alt-text="流覽資產類型頁面" border="true":::

1. 在下一個頁面上，會列出您所選擇資料類型下的最上層資產。 挑選其中一個資產，以進一步探索其內容。

    :::image type="content" source="media/how-to-browse-catalog/asset-type-specific-browse.jpg" alt-text="資產類型的特定流覽頁面。顯示 Azure 儲存體帳戶的範例" border="true":::

1. 將會開啟 [explorer] view。 選取左面板上的資產來開始流覽。 子資產將會列在頁面的右面板上。

    :::image type="content" source="media/how-to-browse-catalog/explorer-view.jpg" alt-text="Explorer 視圖" border="true":::

1. 若要查看資產的詳細資料，請選取最右側的名稱或省略號按鈕。

    :::image type="content" source="media/how-to-browse-catalog/view-asset-detail-click-ellipses.jpg" alt-text="選取省略號按鈕以查看資產詳細資料頁面" border="true":::

## <a name="view-related-data-assets"></a>檢視相關的資料資產

一旦您進入 [資產詳細資料] 頁面，您也可以查看其他相關的資料資產。 例如，您可以流覽至 *DateDimension* 的父資料夾以查看 [ *維度* ] 資料夾，或甚至流覽至容器以查看特定階層下的資產。

1. 在 [資產詳細資料] 頁面上，選取 [ **相關** ] 索引標籤以查看其他相關的資產。

    :::image type="content" source="media/how-to-browse-catalog/launch-related-tab.jpg" alt-text="啟動相關索引標籤" border="true":::

1. 目前資產的完整階層將會列在左側。

    :::image type="content" source="media/how-to-browse-catalog/hierarchical-structure.jpg" alt-text="階層式結構" border="true":::

1. 選取階層中的任何層級，以列出該層級下的立即資產。

    :::image type="content" source="media/how-to-browse-catalog/select-different-hierarchy.jpg" alt-text="選取不同的階層" border="true":::

## <a name="next-steps"></a>後續步驟

- [如何建立、匯入和匯出詞彙](how-to-create-import-export-glossary.md)
- [如何管理商務詞彙的詞彙範本](how-to-manage-term-templates.md)
