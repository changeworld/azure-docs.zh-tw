---
title: 'Azure 範疇中資料的資產深入解析 (預覽版) '
description: 本操作指南說明如何在您的資料上查看和使用範疇 Insights 資產報告。
author: SunetraVirdi
ms.author: suvirdi
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 11/20/2020
ms.openlocfilehash: 5045a13bb932f5907584bae23e956374a5757a68
ms.sourcegitcommit: 65db02799b1f685e7eaa7e0ecf38f03866c33ad1
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/03/2020
ms.locfileid: "96552176"
---
# <a name="asset-insights-on-your-data-in-azure-purview"></a>Azure 範疇中資料的資產深入解析

本操作指南說明如何存取、查看和篩選資料的範疇資產深入解析報表。

在本操作指南中，您將瞭解如何：

* 從您的範疇帳戶查看見解。
* 取得資料的鳥瞰圖。
* 深入瞭解資產計數詳細資料。

## <a name="prerequisites"></a>先決條件

開始使用範疇 insights 之前，請確定您已完成下列步驟：

* 設定您的 Azure 資源，並在帳戶中填入資料。

* 設定並完成來源類型的掃描。

如需詳細資訊，請參閱 [管理 Azure 範疇中的資料來源 (預覽) ](manage-data-sources.md)。

## <a name="use-purview-asset-insights"></a>使用範疇資產深入解析

在 Azure 範疇中，您可以註冊和掃描來源類型。 掃描完成後，您可以在 [資產深入解析] 中查看資產分佈，以分類和資源集告訴您資料資產的狀態。 它也會告訴您資料大小是否有任何變更。

> [!NOTE]
> 掃描來源類型之後，請提供最多一小時的資產見解，以反映新的資產。

1. 在 Azure 入口網站中，流覽至您的 Azure 範疇資源。

1. 在 [ **總覽** ] 頁面的 [ **開始** ] 區段中，選取 [ **開啟範疇 Studio** ] 磚。

   :::image type="content" source="./media/asset-insights/portal-access.png" alt-text="從 Azure 入口網站啟動範疇":::

1. 在範疇 **首頁** 上，選取 [ **View insights** ] 磚以存取您的 **深入** 解析 :::image type="icon" source="media/asset-insights/ico-insights.png" border="false"::: 區域。

   :::image type="content" source="./media/asset-insights/view-insights.png" alt-text="在 Azure 入口網站中查看您的見解":::

1. 在 [ **見解**] :::image type="icon" source="media/asset-insights/ico-insights.png" border="false"::: 區域中，選取 [ **資產** ] 以顯示範疇 **資產深入** 解析報表。

### <a name="view-asset-insights"></a>查看資產深入解析

1. 主要 **資產深入** 解析頁面會顯示下欄區域：

2. 高階 KPI，以顯示來源類型、分類的資產和已探索的資產
 
3. 第一個圖表顯示 **每個來源類型的資產**。

4. 依來源類型來查看您的資產散發。 挑選分類或整個分類類別，以查看依來源類型的資產散發。 
 
5. 若要深入瞭解，請選取 [ **view 更多**]，以顯示來源類型和資產計數的表格式形式。 分類篩選器會傳送至此頁面。

   :::image type="content" source="./media/asset-insights/highlight-kpis.png" alt-text="在資產見解中查看 Kpi 和圖形":::
 
6. 選取您要查看其資產計數的最上層資料夾的特定來源。 

   :::image type="content" source="./media/asset-insights/select-data-source.png" alt-text="選取來源類型":::
 
7. 針對您在上面選取的來源類型中，選取最上層資料夾的總資產。

   :::image type="content" source="./media/asset-insights/file-path.png" alt-text="View file 路徑":::

8. 查看資料夾內的檔案清單。 使用階層連結流覽回見解。

   :::image type="content" source="./media/asset-insights/list-page.png" alt-text="查看資產清單":::  

### <a name="file-based-source-types"></a>以檔案為基礎的來源類型
在 [資產深入解析] 中，接下來的幾個圖表會顯示以檔案為基礎的來源類型分佈。 第一個圖表（稱為 **大小趨勢） (GB) 在來源類型中的檔案類型**，會顯示過去30天內的最上層檔案類型大小趨勢。 
 
1. 選擇您的來源類型，以在來源中查看檔案類型。 
 
1. 選取 [ **更多** ] 以查看目前的資料大小、變更大小、目前的資產計數，以及資產計數的變更。
 
   > [!NOTE]
   > 如果掃描在過去30天內只執行一次，或任何類別目錄變更（例如分類新增/移除）只在30天內發生一次，則上述變更資訊會顯示為空白。

1. 當您按一下 [來源類型] 時，請參閱頂端有變更最上層資產計數的資料夾。

1. 選取路徑以查看資產清單。

以檔案為基礎之來源類型中的第二個圖形是 **_未與資源集相關聯_** 的檔案。 如果您預期所有檔案都應該匯總至資源集，此圖形可協助您瞭解哪些資產尚未匯總。 遺漏的資產可能表示資料夾中有錯誤的檔案模式。 遵循其他圖形中的相同步驟，以查看檔案的詳細資料。

   :::image type="content" source="./media/asset-insights/file-based-assets.png" alt-text="查看以檔案為基礎的資產":::  

## <a name="next-steps"></a>後續步驟

深入瞭解使用[Scan Insights](./scan-insights.md)的 Azure 範疇深入解析報表
