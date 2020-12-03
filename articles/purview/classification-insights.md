---
title: 使用範疇 Insights (預覽) 對資料進行分類報告
description: 本操作指南說明如何在您的資料上查看和使用範疇 Insights 分類報告。
author: batamig
ms.author: bagol
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 11/24/2020
ms.openlocfilehash: 34accbf218a782b79579d2f9857c101cc8147ab8
ms.sourcegitcommit: 65db02799b1f685e7eaa7e0ecf38f03866c33ad1
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/03/2020
ms.locfileid: "96551978"
---
# <a name="classification-insights-about-your-data-from-azure-purview"></a>Azure 範疇中資料的分類見解

本操作指南說明如何針對您的資料存取、查看和篩選範疇分類深入解析報表。

支援的資料來源包括： Azure Blob 儲存體、Azure Data Lake Storage (ADLS) GEN 1、Azure Data Lake Storage (ADLS) GEN 2、Azure Cosmos DB (SQL API) 、Azure Synapse Analytics (先前為 SQL DW) 、Azure SQL Database、Azure SQL 受控執行個體、SQL Server

在本操作指南中，您將瞭解如何：

> [!div class="checklist"]
> - 從 Azure 啟動您的範疇帳戶
> - 查看資料的分類見解
> - 深入瞭解您的資料的更多分類詳細資料

## <a name="prerequisites"></a>先決條件

開始使用範疇 insights 之前，請確定您已完成下列步驟：

- 設定您的 Azure 資源，並以測試資料填入相關帳戶

- 針對每個資料來源中的測試資料設定和完成掃描 

如需詳細資訊，請參閱 [管理 Azure 範疇中的資料來源 (預覽) ](manage-data-sources.md)。

## <a name="use-purview-classification-insights"></a>使用範疇分類見解

在 Azure 範疇中，分類類似于主旨標籤，可用來標記和識別在掃描期間于您的資料資產中找到的特定類型資料。

範疇使用與 Microsoft 365 相同的機密資訊類型，可讓您將現有的安全性原則和保護延伸至整個資料資產。

**若要查看分類見解：**

1. 移至 [Azure 入口網站中](https://aka.ms/purviewportal)的 [ **Azure 範疇** 實例] 畫面，然後選取您的範疇帳戶。

1. 在 [ **總覽** ] 頁面的 [ **開始** ] 區段中，選取 [ **啟動範疇帳戶** ] 磚。

1. 在範疇中，選取左側的 [ **深入** 解析] :::image type="icon" source="media/insights/ico-insights.png" border="false"::: 功能表項目，以存取您的 **深入** 解析區域。

1. 在 [ **見解**] :::image type="icon" source="media/insights/ico-insights.png" border="false"::: 區域中，選取 [ **分類** ] 以顯示範疇 **分類見解** 報表。

   :::image type="content" source="./media/insights/select-classification-labeling-small.png" alt-text="分類見解報表" lightbox="media/insights/select-classification-labeling.png":::

   [主要 **分類見解** ] 頁面會顯示下欄區域：

   |區域  |說明  |
   |---------|---------|
   |**具有分類的來源總覽**     |顯示提供下列各圖的圖格： <br>-在您的資料中找到的訂用帳戶數目 <br>-在您的資料中找到的唯一分類數目 <br>-找到的分類來源數目 <br>-找到的分類檔案數目 <br>-找到的分類資料表數目         |
   |**具有分類資料的熱門來源 (過去30天)**     |顯示在過去30天內，以分類資料找到的來源數量的趨勢。            |
   |**依來源分類的熱門分類**     |顯示分類類別目錄（例如 **財務** 或 **政府**）所找到的來源數目。      |
   |**檔案的頂端分類**     |顯示套用至您資料中檔案的最上層分類，例如信用卡號碼或國家（地區）識別碼。         |
   |**資料表的最大分類**     | 顯示套用至資料中資料表的最上層分類，例如個人識別資訊。 |   
   |  **分類活動** <br> (檔案和資料表)  |  顯示檔案和資料表的個別圖形，每個圖表都會顯示在所選時間範圍內分類的檔案或資料表數目。 <br>**預設值**：30天<br>選取圖形上方的 **時間** 篩選器，以選取要顯示的其他時間範圍。    |
   |    |    |

## <a name="classification-insights-drilldown"></a>分類見解深入剖析

在下列任何 **分類見解** 圖表中，選取 [ **View 更多** ] 連結以深入瞭解詳細資料：

- **依來源分類的熱門分類**
- **檔案的頂端分類**
- **資料表的最大分類**
- **分類活動 > 分類資料**

例如：

:::image type="content" source="media/insights/view-classifications-small.png" alt-text="查看所有分類" lightbox="media/insights/view-classifications.png":::

若要深入瞭解，請執行下列其中一項：

|選項  |說明  |
|---------|---------|
|**篩選資料**     |  使用方格上方的篩選準則來篩選顯示的資料，包括分類名稱、訂用帳戶名稱或來源類型。 <br><br>如果您不確定確切的分類名稱，可以在 [ **依關鍵字篩選** ] 方塊中輸入部分或全部名稱。       |
|**排序方格** |選取資料行標頭，依該資料行排序方格。 | 
|**編輯欄**     |  若要在方格中顯示更多或更少的資料行，請選取 [ **編輯資料行**] :::image type="icon" source="media/insights/ico-columns.png" border="false"::: ，然後選取您想要查看或變更順序的資料行。   |
|**進一步向下切入**     | 若要向下切入到特定分類，請在 [ **分類** ] 資料行中選取名稱，以 **依來源** 報告查看分類。 <br><br>這份報表會顯示所選分類的資料，包括來源名稱、來源類型、訂用帳戶識別碼，以及已分類的檔案和資料表的數目。      |
|**流覽資產**     |  若要流覽具有特定分類或來源的資產，請選取分類或來源（視您所看到的報表而定），然後選取 [ **流覽** :::image type="icon" source="media/insights/ico-browse-assets.png" border="false"::: 篩選上方的資產]。 <br><br>搜尋結果會顯示所選篩選準則的所有已分類資產。  如需詳細資訊，請參閱 [搜尋 Azure 範疇資料目錄](how-to-search-catalog.md)。       |
| | |

## <a name="next-steps"></a>後續步驟

深入瞭解 Azure 範疇 insights 報表
> [!div class="nextstepaction"]
> [詞彙見解](glossary-insights.md)

> [!div class="nextstepaction"]
> [掃描見解](scan-insights.md)

> [!div class="nextstepaction"]
> [敏感度標籤見解](./sensitivity-insights.md)

> [!div class="nextstepaction"]
> [副檔名見解](file-extension-insights.md)