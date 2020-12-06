---
title: 使用範疇 Insights 報告資料的副檔名
description: 本操作指南說明如何在您的資料上查看和使用範疇 Insights 副檔名報告。
author: batamig
ms.author: bagol
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 11/24/2020
ms.openlocfilehash: 222106f5838a1eb237987afaa93924f1efe4a1f4
ms.sourcegitcommit: ad83be10e9e910fd4853965661c5edc7bb7b1f7c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/06/2020
ms.locfileid: "96746279"
---
# <a name="file-extension-insights-about-your-data-from-azure-purview"></a>Azure 範疇中資料的檔案延伸模組深入解析 

本操作指南說明如何存取、查看和篩選您的資料中所找到的副檔名或檔案類型的相關見解。

支援的資料來源包括： Azure Blob 儲存體、Azure Data Lake Storage (ADLS) GEN 1、Azure Data Lake Storage (ADLS) GEN 2

在本操作指南中，您將瞭解如何：
> [!div class="checklist"]
> * 從 Azure 啟動您的範疇帳戶。 
> - 在您的資料上查看副檔名見解
> - 深入瞭解資料的更多檔案延伸模組詳細資料

## <a name="prerequisites"></a>先決條件 

開始使用範疇 insights 之前，請確定您已完成下列步驟：

- 設定您的 Azure 資源，並以測試資料填入相關帳戶

- 針對每個資料來源中的測試資料設定和完成掃描

如需詳細資訊，請參閱 [管理 Azure 範疇中的資料來源 (預覽) ](manage-data-sources.md)。

## <a name="use-purview-file-extension-insights"></a>使用範疇副檔名深入解析

在掃描您的資產時，Azure 範疇能夠偵測您資料資產中的檔案類型，並提供有關每個檔案類型的詳細資料。 詳細資料包括您擁有之每個類型的檔案數目、這些檔案的所在位置，以及是否可流覽敏感性資料。

> [!NOTE]
> 在您掃描來源類型之後，請在幾小時內提供 **副檔名** 見解，以反映新的資產。

**若要查看副檔名見解：**

1. 移至 [Azure 入口網站中](https://aka.ms/purviewportal)的 [ **Azure 範疇** 實例] 畫面，然後選取您的範疇帳戶。

1. 在 [ **總覽** ] 頁面的 [ **開始** ] 區段中，選取 [ **啟動範疇帳戶** ] 磚。

1. 在範疇中，選取左側的 [ **深入** 解析] :::image type="icon" source="media/insights/ico-insights.png" border="false"::: 功能表項目，以存取您的 **深入** 解析區域。
    
1. 在 [ **見解**] 中，選取 [ **副檔名** ] 索引標籤。

    報表會在選取的時間範圍 (預設值：30天) ，顯示在所選時間範圍內找到多少唯一副檔名的圖表，以及前10個擴充功能的圖表。

    :::image type="content" source="media/file-extension-insights/file-extension-overview-small.png" alt-text="副檔名報表-總覽" lightbox="media/file-extension-insights/file-extension-overview.png":::

    請執行下列其中一項作業以找出更多：

    - 選取報表頂端的 **時間** 選取器，以變更找到副檔名的時間範圍。
    
    - 選取圖表下方的 [ **查看更多** ]，以查看找到的副檔名完整清單。 如需詳細資訊，請參閱檔案 [延伸深入](#file-extension-insights-drilldown)解析摘要。 

### <a name="file-extension-insights-drilldown"></a>檔案延伸深入解析摘要

當您在資料資產中找到有關檔案類型的高階資訊之後，請向下切入以取得其所在位置的詳細資訊，以及是否可以掃描是否有機密資料。

例如：

:::image type="content" source="media/file-extension-insights/file-extension-drilldown-small.png" alt-text="擴充功能報表-交叉分析" lightbox="media/file-extension-insights/file-extension-drilldown.png":::

方格會顯示找到的每個副檔名的詳細資料，包括：

- 檔案 **計數**。 具有指定副檔名的檔案數目。
- **內容掃描**。 是否支援掃描機密資料的副檔名。
- **訂閱**。 找到指定之延伸模組的訂閱數目。
- **來源**。 找到具有指定副檔名的來源數目。



使用方格上方的篩選準則來篩選顯示的資料：

|選項  |描述  |
|---------|---------|
|**依關鍵字篩選**     |    在 [ **依關鍵字篩選**  ] 方塊中輸入文字，以依名稱查看篩選您的檔案類型。 例如，若只要查看 Pdf，請輸入 `PDF` 。     |
|**Time**        | 選取此項可依特定時間範圍篩選資料的建立時間。 <br>**預設值：** 30 天  |
|**副檔名**     |選取此項可依一或多個檔案類型篩選方格。        |
|**來源**    |選取此項可依特定資料來源篩選方格。 |
|**內容掃描**     |選取即可選擇 [ **支援** ] 或 [ **不支援**]，只顯示可以針對機密資料進一步掃描的檔案類型，或無法掃描的資料（例如 **cert** 或 **.jpg** 檔案）。 |
| | |

在篩選準則的上方，選取 [ **編輯資料行**] :::image type="icon" source="media/insights/ico-columns.png" border="false"::: 以在方格中顯示更多或更少的資料行，或變更順序。 

若要排序方格，請選取要依該資料行排序的資料行標題。
## <a name="next-steps"></a>後續步驟

深入瞭解 Azure 範疇 insights 報表
> [!div class="nextstepaction"]
> [詞彙見解](glossary-insights.md)

> [!div class="nextstepaction"]
> [掃描見解](scan-insights.md)

> [!div class="nextstepaction"]
> [分類見解](./classification-insights.md)

> [!div class="nextstepaction"]
> [敏感度標籤見解](sensitivity-insights.md)
