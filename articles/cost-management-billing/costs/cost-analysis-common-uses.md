---
title: Azure 成本管理中常見的成本分析用途
description: 本文說明如何在 Azure 成本管理中取得常見成本分析工作的結果。
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 02/12/2020
ms.topic: conceptual
ms.service: cost-management-billing
ms.reviewer: adwise
ms.openlocfilehash: 2102e3af26c8f5c4ed85543bc19360a923d070f3
ms.sourcegitcommit: 333af18fa9e4c2b376fa9aeb8f7941f1b331c11d
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/13/2020
ms.locfileid: "77199971"
---
# <a name="common-cost-analysis-uses"></a>常見的成本分析用途

許多人都曾詢問過的問題，經常也是 Azure 成本管理使用者亟待解答的問題。 本文將逐步說明如何在成本管理中取得常見成本分析工作的結果。

## <a name="view-cost-breakdown-by-azure-service"></a>依 Azure 服務檢視成本明細

依 Azure 服務來檢視成本，可協助您進一步了解基礎結構中最耗費成本的部分。 例如，VM 計算成本可能很低。 不過，從 VM 發出的資訊量，可能會產生高額的網路成本。 您必須了解拉高 Azure 服務成本的主要因素，以期能視需要調整服務使用方式。

1. 在 Azure 入口網站中，瀏覽至您的範圍的成本分析。 例如：[成本管理 + 計費]   > [成本管理]   > [成本分析]  。
1. 選取 [依服務區分的成本]  ，然後依 [服務層級]  進行分組。
1. 將檢視變更為 [資料表]  。

![依 Azure 服務顯示的成本明細](./media/cost-analysis-common-uses/breakdown-by-service.png)

## <a name="view-cost-breakdown-by-azure-resource"></a>依 Azure 資源檢視成本明細

您的服務是以 Azure 資源建置的。 根據資源檢閱成本，可協助您快速找出成本中的主要佔比項目。 如果服務有太昂貴的資源，請考慮進行變更以降低成本。

1. 在 Azure 入口網站中，瀏覽至您的範圍的成本分析。 例如：[成本管理 + 計費]   > [成本管理]   > [成本分析]  。
1. 選取 [依資源區分的成本]  。
1. 將檢視變更為 [資料表]  。

![依 Azure 資源檢視成本明細](./media/cost-analysis-common-uses/cost-by-resource.png)

## <a name="view-cost-breakdown-by-selected-dimensions"></a>依選取的維度檢視成本明細

維度可讓您根據費用中顯示的各種中繼資料值來組織成本。 例如，您可以依位置將成本分組。

1. 在 Azure 入口網站中，瀏覽至您的範圍的成本分析。 例如：[成本管理 + 計費]   > [成本管理]   > [成本分析]  。
1. 選取 [群組依據]  篩選條件。  
    ![選取群組依據項目](./media/cost-analysis-common-uses/group-by.png)
1. (選擇性) 您可以儲存檢視以供後續使用。
1. 按一下圖形下方的圓形圖，以檢視更詳細的資料。  
    ![依選取的維度檢視成本明細](./media/cost-analysis-common-uses/drill-down.png)

## <a name="view-costs-per-day-or-by-month"></a>每天或每月檢視成本

查看每日和每月的成本，可協助您進一步了解成本是否在某週或某年較高。 如果您在假期期間擁有更多客戶流量，是否會導致 Azure 成本相對應地增加？ 星期五的成本是否比星期一高？

1. 在 Azure 入口網站中，瀏覽至您的範圍的成本分析。 例如：[成本管理 + 計費]   > [成本管理]   > [成本分析]  。
1. 將 [細微性]  設定為 [每月]  或 [每日]  。

![每天檢視成本](./media/cost-analysis-common-uses/daily-granularity.png)

## <a name="view-costs-for-a-specific-tag"></a>檢視特定標記的成本

許多 Azure 使用者會將標記套用至成本中心或開發環境 (生產和測試) 之類的資源，以利進行費用的分類。 標記在成本分析中會顯示為維度。 您可以使用維度取得自訂標記分類的深入解析。

標記支援適用於將標記套用至資源「之後」  所回報的使用量。 您無法追溯套用標記以彙總成本。

1. 在 Azure 入口網站中，瀏覽至您的範圍的成本分析。 例如：[成本管理 + 計費]   > [成本管理]   > [成本分析]  。
1. 針對您的標記選取 [群組依據]  。

![檢視特定標記的成本](./media/cost-analysis-common-uses/tag.png)

## <a name="download-your-usage-details"></a>下載使用情況詳細資料

您的使用情況詳細資料報表檔案 (CSV 格式) 會針對計入發票的所有費用提供相關明細。 您可以使用此報表作為比較依據，並進一步了解您的發票。 發票上計費的每項費用都會對應於使用情況報告中的明細費用。

1. 在 Azure 入口網站中，瀏覽至計費帳戶或訂用帳戶的 [使用方式和費用]  索引標籤。 例如：[成本管理 + 計費]   > [計費]   > [使用方式 + 費用]  。
1. 選取要下載的明細項目，然後按一下下載符號。  
    ![下載使用方式和費用](./media/cost-analysis-common-uses/download1.png)
1.  選取要下載的使用方式檔案。  
    ![選擇要下載的使用方式檔案](./media/cost-analysis-common-uses/download2.png)

## <a name="view-monthly-ea-cost-breakdown"></a>檢視每月 EA 成本明細

您的 EA 註冊會累計到整個組織的成本中。 了解成本在一段時間內累計及開立發票的方式，可協助您與適當的專案關係人互動，以確保成本受到妥善的管理。

1. 在 Azure 入口網站中，瀏覽至 [成本管理 + 計費]   > [概觀]  。
1. 按一下現行月份的 [明細]  ，並檢視您的預付金額度。  
    ![EA 成本概觀 - 明細摘要](./media/cost-analysis-common-uses/breakdown1.png)
1.  按一下 [使用方式和費用]  索引標籤，然後在選擇的時間範圍內檢視先前月份的明細。  
    ![使用方式和費用索引標籤](./media/cost-analysis-common-uses/breakdown2.png)

## <a name="view-enrollment-monthly-cost-by-term"></a>依期間檢視每月註冊費用

使用註冊的每月成本圖形化檢視，可了解指定期間的成本趨勢和發票金額。

1. 在 Azure 入口網站中，瀏覽至您的範圍的成本分析。 例如：[成本管理 + 計費]   > [成本管理]   > [成本分析]  。
1. 選取您的註冊並設定註冊期間。
1. 將細微性設定為每月，然後將檢視設定為 [資料行 (堆疊)]  。

您可以分組並篩選您的資料，以進行更詳細的分析。

![依期間顯示的每月註冊費用](./media/cost-analysis-common-uses/enrollment-term1.png)

## <a name="view-ea-enrollment-accumulated-costs"></a>檢視 EA 註冊累積成本

檢視一段時間內的淨累積費用，以了解您的組織在指定期間內的整體支出。

1. 在 Azure 入口網站中，瀏覽至您的範圍的成本分析。 例如：[成本管理 + 計費]   > [成本管理]   > [成本分析]  。
1. 選取您的註冊，然後檢視目前的累積成本。

![註冊累積成本](./media/cost-analysis-common-uses/cost-analysis-enrollment.png)

## <a name="next-steps"></a>後續步驟
- 如果您尚未完成成本管理的第一個快速入門，請在[開始分析成本](quick-acm-cost-analysis.md)閱讀它。
- 閱讀[成本管理文件](../index.yml)。
