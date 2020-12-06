---
title: Azure Blob 儲存體中的資料上報告的敏感度標籤
description: 本操作指南說明如何在 Azure Blob 儲存體中，針對您的資料，查看和使用範疇敏感度標籤報告。
author: batamig
ms.author: bagol
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 11/24/2020
ms.openlocfilehash: e6a92282d2bcd316a771742048dacd9a7181de4f
ms.sourcegitcommit: ad83be10e9e910fd4853965661c5edc7bb7b1f7c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/06/2020
ms.locfileid: "96746177"
---
# <a name="sensitivity-label-insights-about-your-data-in-azure-purview"></a>Azure 範疇中資料的敏感度標籤深入解析

本操作指南說明如何存取、查看及篩選套用至您資料的敏感度標籤所提供的安全性見解。

支援的資料來源包括： Azure Blob 儲存體、Azure Data Lake Storage (ADLS) GEN 1、Azure Data Lake Storage (ADLS) GEN 2、SQL Server、Azure SQL Database、Azure SQL 受控執行個體

在本操作指南中，您將瞭解如何：

> [!div class="checklist"]
> - 從 Azure 啟動您的範疇帳戶。
> - 查看您資料上的敏感度標籤見解
> - 向下切入以取得更多關於您資料的敏感度標籤詳細資料

## <a name="prerequisites"></a>先決條件

開始使用範疇 insights 之前，請確定您已完成下列步驟：

- 設定您的 Azure 資源，並以測試資料填入相關帳戶

- 將[Microsoft 365 敏感度標籤延伸至 Azure 範疇中的資產](create-sensitivity-label.md)，並建立或選取您想要套用至資料的標籤。

- 針對每個資料來源中的測試資料設定和完成掃描

如需詳細資訊，請參閱 [管理 Azure 範疇中的資料來源 (預覽版) ](manage-data-sources.md) 並 [自動在 azure 範疇中為您的資料加上標籤](create-sensitivity-label.md)。

## <a name="use-purview-sensitivity-labeling-insights"></a>使用範疇敏感度標籤見解

在範疇中，分類類似于主旨標記，可用來標記和識別在掃描期間于您的資料資產內找到的特定類型資料。

敏感度標籤可讓您陳述組織中特定資料的敏感程度。 例如，特定的專案名稱在您的組織內可能會有高度機密，但該相同的詞彙不是其他組織的機密。 

雖然分類會直接符合 (社會安全號碼具有) 的 **社會安全號碼** 分類，但在一或多個分類和案例一起找到時，會套用敏感度標籤。 

範疇會使用相同的分類，也稱為敏感性資訊類型，Microsoft 365。 這可讓您在 Azure 範疇資產之間擴充現有的敏感度標籤。

> [!NOTE]
> 掃描來源類型之後，請在幾小時內提供 **敏感度標籤** 見解，以反映新的資產。

**若要查看敏感度標籤見解：**

1. 移至 **Azure 範疇** 首頁。

1. 在 [ **總覽** ] 頁面的 [ **開始** ] 區段中，選取 [ **啟動範疇帳戶** ] 磚。

1. 在範疇中，選取左側的 [ **深入** 解析] :::image type="icon" source="media/insights/ico-insights.png" border="false"::: 功能表項目，以存取您的 **深入** 解析區域。

1. 在 [ **見解**] :::image type="icon" source="media/insights/ico-insights.png" border="false"::: 區域中選取 [ **敏感度標籤** ]，以顯示範疇 **敏感度標籤見解** 報表。

    > [!NOTE]
    > 如果此報表是空的，您可能尚未將您的敏感度標籤延伸至 Azure 範疇。 如需詳細資訊，請參閱 [在 Azure 範疇中自動為數據加上標籤](create-sensitivity-label.md)。

   :::image type="content" source="media/insights/sensitivity-labeling-insights-small.png" alt-text="敏感度標籤見解" lightbox="media/insights/sensitivity-labeling-insights.png":::

   主要 **敏感度標籤深入** 解析頁面會顯示下欄區域：

   |區域  |描述  |
   |---------|---------|
   |**使用敏感度標籤的來源總覽**     |顯示提供下列各圖的圖格： <br>-在您的資料中找到的訂閱數目。 <br>-在您的資料上套用的唯一敏感度標籤數目 <br>-套用敏感度標籤的來源數目 <br>-已套用敏感度標籤的檔案和資料表數目|
   |**過去30天內加上標籤資料的熱門來源 ()**     | 顯示過去30天內套用敏感度標籤之來源數目的趨勢。       |
   |**跨來源套用的上方標籤**     |顯示在所有範疇資料資源上套用的上方標籤。 |
   |**在檔案上套用的上方標籤**     |顯示套用至資料中檔案的最上層敏感度標籤。          |
   |**在資料表上套用的上方標籤**     | 顯示套用至資料中資料庫資料表的最上層敏感度標籤。 |   
   |  **標記活動**  |  針對檔案和資料表顯示個別的圖表，每個圖表都會顯示在所選時間範圍內標記的檔案或資料表數目。 <br>**預設值**：30天<br>選取圖形上方的 **時間** 篩選器，以選取要顯示的其他時間範圍。    |
   |    |    |

## <a name="sensitivity-labeling-insights-drilldown"></a>敏感度標籤深入解析摘要

在下列任何敏感性卷 **標見解** 圖表中，選取 [ **View 更多** ] 連結以深入瞭解詳細資料：

- **跨來源套用的上方標籤**
- **在檔案上套用的上方標籤**
- **在資料表上套用的上方標籤**
- **標記活動 > 標記的資料**

例如：

:::image type="content" source="media/insights/sensitivity-label-drilldown-small.png" alt-text="敏感度標籤明細" lightbox="media/insights/sensitivity-label-drilldown.png":::

若要深入瞭解，請執行下列其中一項：

|選項  |描述  |
|---------|---------|
|**篩選資料**     |  使用方格上方的篩選來篩選顯示的資料，包括標籤名稱、訂用帳戶名稱或來源類型。 <br><br>如果您不確定確切的標籤名稱，您可以在 [ **依關鍵字篩選** ] 方塊中輸入部分或全部名稱。       |
|**排序方格** |選取資料行標頭，依該資料行排序方格。 | 
|**編輯欄**     |  若要在方格中顯示更多或更少的資料行，請選取 [ **編輯資料行**] :::image type="icon" source="media/insights/ico-columns.png" border="false"::: ，然後選取您想要查看或變更順序的資料行。    <br><br>選取資料行標頭，依該資料行排序方格。   |
|**進一步向下切入**     | 若要向下切入到特定標籤，請在 [ **敏感度標籤** ] 資料行中選取名稱，以依來源報表來查看 **標籤** 。 <br><br>這份報表會顯示所選標籤的資料，包括來源名稱、來源類型、訂用帳戶識別碼，以及已分類的檔案和資料表的數目。      |
|**流覽資產**     |  若要流覽具有特定標籤或來源的資產，請選取一或多個標籤或來源（視您所看到的報表而定），然後選取 [ **流覽** :::image type="icon" source="media/insights/ico-browse-assets.png" border="false"::: 篩選上方的資產]。 <br><br>搜尋結果會顯示針對所選篩選準則找到的所有標記資產。  如需詳細資訊，請參閱 [搜尋 Azure 範疇資料目錄](how-to-search-catalog.md)。       |
| | |

## <a name="sensitivity-label-integration-with-microsoft-365-compliance"></a>敏感度標籤與 Microsoft 365 合規性的整合

Microsoft 365 中所提供的 [Microsoft 資訊保護](/microsoft-365/compliance/information-protection) 的緊密整合，表示範疇能讓您直接擴充資料資產的可見度，以及將資料分類和標籤。

若要將您的 Microsoft 365 敏感度標籤延伸至 Azure 範疇中的資產，您必須在 Microsoft 365 合規性中心中主動開啟 Azure 範疇的資訊保護。

如需詳細資訊，請參閱 [在 Azure 範疇中自動為數據加上標籤](create-sensitivity-label.md)。

## <a name="next-steps"></a>後續步驟

深入瞭解這些 Azure 範疇深入解析報表：

- [詞彙見解](glossary-insights.md)
- [掃描見解](scan-insights.md)
- [分類見解](./classification-insights.md)
- [副檔名見解](file-extension-insights.md)
