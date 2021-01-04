---
title: 教學課程：瀏覽 Azure Purview 中的資產並檢視其譜系
description: 本教學課程說明如何瀏覽目錄中的資產並檢視資料譜系。
author: djpmsft
ms.author: daperlov
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: tutorial
ms.date: 12/01/2020
ms.openlocfilehash: 7ffbe2ded44ded4f580655f6ae9e98391490f94a
ms.sourcegitcommit: b6267bc931ef1a4bd33d67ba76895e14b9d0c661
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/19/2020
ms.locfileid: "97696087"
---
# <a name="tutorial-browse-assets-in-azure-purview-preview-and-view-their-lineage"></a>教學課程：瀏覽 Azure Purview (預覽) 中的資產並檢視其譜系

> [!IMPORTANT]
> Azure Purview 目前為預覽狀態。 [Microsoft Azure 預覽版增補使用規定](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)包含適用於 Azure 功能 (搶鮮版 (Beta)、預覽版，或尚未發行的版本) 的其他法律條款。

本教學課程說明如何在 Azure Purview 中瀏覽資產並檢視其重要的詳細資料，例如譜系。

此教學課程是「五部分教學課程系列的第 3 部分」，您將了解使用 Azure Purview 來管理跨資料資產的資料治理基本概念。 本教學課程建基於您已完成的[第 2 部分：瀏覽 Azure Purview (預覽) 首頁並搜尋資產](tutorial-asset-search.md)。

在本教學課程中，您將：

> [!div class="checklist"]
>
> * 瀏覽目錄中的資產。
> * 檢視資產的譜系。

## <a name="prerequisites"></a>必要條件

* 完成[教學課程：瀏覽 Azure Purview (預覽) 首頁並搜尋資產](tutorial-asset-search.md)。

## <a name="sign-in-to-azure"></a>登入 Azure

登入 [Azure 入口網站](https://portal.azure.com)。

## <a name="browse-for-assets-in-the-catalog"></a>瀏覽目錄中的資產

在上一個教學課程中，您已了解搜尋可如何協助您探索 Azure Purview 目錄中的資產。 在目錄中探索資產的另一種方式是使用目錄的瀏覽體驗。

本節說明如何瀏覽您的 Azure Purview 目錄。

1. 在 Azure 入口網站中瀏覽至您的 Azure Purview 資源，然後選取 [開啟 Purview Studio]。 您會自動進入 Purview Studio 的首頁。

1. 在 [首頁] 頁面上，選取 [瀏覽資產]。

   [瀏覽資產] 頁面會隨即出現，其中會顯示您目錄中所有資產類型的摘要。

1. 若要探索您目錄中可用的各種 Azure Data Lake Gen2 類型資產，請選取 [Azure Data Lake Gen2] 磚。

   :::image type="content" source="./media/tutorial-browse-and-view-lineage/browse-by-asset-type.png" alt-text="顯示 [依資產類型瀏覽] 頁面並已選取 [Azure Data Lake Gen2]的螢幕擷取畫面。":::

1. 如果有多個資產，您可以選取 [名稱] 資料行標頭，並依字母順序排序資產。 本教學課程中只有一個 Azure Data Lake Storage Gen2 資產。

1. 選取資產的名稱。

1. 選取 **Contoso_GrossProfit_{N}.ssv** 資源集。 如果此資產不存在於您的目錄中，請選擇另一個資產。

   :::image type="content" source="media/tutorial-browse-and-view-lineage/view-adls-assets.png" alt-text="Azure Data Lake Storage Gen2 資源的清單":::

## <a name="view-the-lineage-of-assets"></a>檢視資產的譜系

在 [資產詳細資料] 頁面上，探索資料的來源。

1. 選取 **Contoso_GrossProfit_{N}.ssv** 資源集的 [譜系] 索引標籤。

   您選取的資產會隨即顯示。 您看到的譜系資訊會顯示此資源集已從您的 Azure Blob 儲存體帳戶複製到 Azure Data Lake Storage Gen2。

   :::image type="content" source="./media/tutorial-browse-and-view-lineage/view-lineage.png" alt-text="顯示資產譜系檢視的螢幕擷取畫面。":::

1. 選取此頁面上的 Blob 資產，然後選取 [切換至資產] 連結。

   您會看到此視窗已切換至 Azure Blob 資源集，也就是原始 Azure Data Lake Storage Gen2 的來源。

1. 選取 [概觀] 索引標籤，以調查資產並確認其詳細資料。

如需如何在 Azure Blob 與 Azure Data Lake Storage Gen2 資源集之間建立 Azure Data Factory 資料流程活動及觀察譜系的資訊，請參閱 [Azure Data Factory 資料流程活動](../data-factory/concepts-data-flow-overview.md)。

## <a name="next-steps"></a>後續步驟

在本教學課程中，您已了解如何：

> [!div class="checklist"]
>
> * 瀏覽目錄中的資產。
> * 檢視資產的譜系。

請前進到下一個教學課程，以了解資源集、資產詳細資料、結構描述和分類。

> [!div class="nextstepaction"]
> [資源集、資產詳細資料、結構描述和分類](tutorial-schemas-and-classifications.md)
