---
title: 在 Azure 範疇中掃描資料的見解
description: 本操作指南說明如何在您的資料上查看和使用範疇 Insights 掃描報告。
author: SunetraVirdi
ms.author: suvirdi
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 11/20/2020
ms.openlocfilehash: 00f72e1de230cdc68f86010b7b25d86debaa5eb5
ms.sourcegitcommit: 16c7fd8fe944ece07b6cf42a9c0e82b057900662
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/03/2020
ms.locfileid: "96575783"
---
# <a name="scan-insights-on-your-data-in-azure-purview"></a>在 Azure 範疇中掃描資料的見解

本操作指南說明如何存取、查看和篩選資料的 Azure 範疇掃描深入解析報表。

在本操作指南中，您將瞭解如何：

> [!div class="checklist"]
> * 從您的範疇帳戶查看見解。
> * 取得您的掃描的鳥觀點。

## <a name="prerequisites"></a>先決條件

開始使用範疇 insights 之前，請確定您已完成下列步驟：

* 設定您的 Azure 資源，並在帳戶中填入資料。
* 在資料來源上設定和完成掃描。

如需詳細資訊，請參閱 [管理 Azure 範疇中的資料來源 (預覽) ](manage-data-sources.md)。

## <a name="use-purview-scan-insights"></a>使用範疇 Scan Insights

在 Azure 範疇中，您可以註冊和掃描來源類型。 您可以在 Scan Insights 中查看一段時間的掃描狀態。 此深入解析會告訴您有多少掃描失敗、成功，或在一段時間內遭到取消。

### <a name="view-scan-insights"></a>查看掃描見解

1. 移至 Azure 入口網站中的 [ **Azure 範疇** 實例] 畫面，然後選取您的範疇帳戶。

1. 在 [ **總覽** ] 頁面的 [ **開始** ] 區段中，選取 [ **開啟範疇 Studio** ] 磚。

   :::image type="content" source="./media/scan-insights/portal-access.png" alt-text="從 Azure 入口網站啟動範疇":::

1. 在範疇 **首頁** 上，選取 [ **View insights** ] 磚以存取您的 **深入** 解析 :::image type="icon" source="media/scan-insights/ico-insights.png" border="false"::: 區域。

   :::image type="content" source="./media/scan-insights/view-insights.png" alt-text="在 Azure 入口網站中查看您的見解":::

1. 在 [ **見解**] :::image type="icon" source="media/scan-insights/ico-insights.png" border="false"::: 區域中，選取 [ **掃描** ] 以顯示範疇 **Scan Insights** 報表。

### <a name="view-high-level-kpis-to-show-count-of-scans-by-status"></a>查看高階 Kpi 以顯示依狀態的掃描計數
 
高階 Kpi 會顯示在一段期間內執行的總掃描。 時間週期預設為過去30天。 不過，您也可以選取 [過去七天]。 根據時間篩選準則，KPI 值會適當地反映掃描次數。


根據選取的時間篩選值，您可以查看每週或每日在圖形中的成功、失敗和取消的掃描分佈。

   :::image type="content" source="./media/scan-insights/scan-insights.png" alt-text="查看掃描見解":::

## <a name="next-steps"></a>後續步驟

深入瞭解 Azure 範疇深入解析報表與 [資產見解](./asset-insights.md)
