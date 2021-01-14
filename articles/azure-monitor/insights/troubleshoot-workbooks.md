---
title: 針對以活頁簿為基礎的深入解析 Azure 監視器進行疑難排解
description: 針對 Azure Key Vault、Azure CosmosDB、Azure 儲存體和 Azure Cache for Redis 等服務 Azure 監視器以活頁簿為基礎的深入解析，提供疑難排解指引。
services: azure-monitor
ms.author: lagayhar
author: lgayhardt
ms.topic: conceptual
ms.date: 06/17/2020
ms.openlocfilehash: 3a2765e9dcf3b0e7bf28191e6ecbb069f26c42f5
ms.sourcegitcommit: f5b8410738bee1381407786fcb9d3d3ab838d813
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/14/2021
ms.locfileid: "98208517"
---
# <a name="troubleshooting-workbook-based-insights"></a>疑難排解以活頁簿為基礎的深入解析

本文將協助您診斷和疑難排解使用 Azure 監視器活頁簿型深入解析時可能會遇到的一些常見問題。


## <a name="why-can-i-only-see-200-resources"></a>為什麼我只能看到200資源

無論選取的訂用帳戶數量為何，所選資源的數目都有200的限制。

## <a name="what-happens-when-i-click-on-a-recently-pinned-tile-in-the-dashboard"></a>當我按一下儀表板中最近釘選的磚時，會發生什麼事

* 如果您按一下磚上的任何位置，即會帶您前往從中釘選磚的索引標籤。 例如，如果您在 [總覽] 索引標籤中釘選圖形，則當您在儀表板中按一下該圖格時，就會開啟該預設視圖，不過，如果您從自己儲存的副本釘選圖形，則會開啟您儲存的複本視圖。
* 標題左上方的篩選圖示會開啟 [設定磚的設定] 索引標籤。
* 右上方的省略符號圖示將為您提供 [自訂標題資料]、[自訂]、[重新整理] 和 [從儀表板移除] 等選項。

## <a name="what-happens-when-i-save-a-workbook"></a>儲存活頁簿時，會發生什麼事

* 當您儲存活頁簿時，它可讓您使用編輯來建立新的活頁簿複本，並變更標題。 儲存不會覆寫活頁簿，目前的活頁簿一律為預設檢視。
* **未儲存的** 活頁簿只是預設檢視。

## <a name="why-dont-i-see-all-my-subscriptions-in-the-portal"></a>為什麼我在入口網站中看不到所有的訂閱

入口網站啟動時，入口網站只會針對選取的訂用帳戶顯示資料。 若要變更選取的訂用帳戶，請移至右上方，然後按一下具有篩選圖示的筆記本。 此選項會顯示 [ **目錄 +** 訂用帳戶] 索引標籤。

![目錄 + 訂用帳戶](./media/storage-insights-overview/fqa3.png)

## <a name="what-is-time-range"></a>什麼是時間範圍

時間範圍會顯示特定時間範圍內的資料。 例如，如果時間範圍是 24 小時，則會顯示過去 24 小時內的資料。

## <a name="what-is-time-granularity-time-grain"></a>時間細微性 (時間細微性) 

時間細微性是兩個資料點之間的時間差異。 例如，如果將時間粒紋設定為 1 秒，即表示每秒都會收集計量。

## <a name="what-is-the-time-granularity-once-we-pin-any-part-of-the-workbooks-to-a-dashboard"></a>將活頁簿的任何部分釘選到儀表板時的時間細微性為何？

預設的時間細微性會設定為自動，目前無法變更。

## <a name="how-do-i-change-the-timespan-time-range-of-the-workbook-step-on-my-dashboard"></a>如何? 變更我的儀表板上的活頁簿步驟時間範圍/時間範圍

根據預設，儀表板磚上的時間會設定為 24 小時，若要變更此值，按一下右上方的省略符號、選取 [自訂磚的資料]、勾選 [在標題等級覆寫儀表板時間設定] 方塊，然後使用下拉式功能表挑選時間範圍。  

![選取磚右上角的省略符號，然後選擇 [自訂此資料]](./media/storage-insights-overview/fqa-data-settings.png)

![在 [設定磚] 設定中，選取 [時間範圍] 下拉式清單以變更 timespan/時間範圍](./media/storage-insights-overview/fqa-timespan.png)

## <a name="how-do-i-change-the-title-of-the-workbook-or-a-workbook-step-i-pinned-to-a-dashboard"></a>如何? 變更我釘選到儀表板的活頁簿或活頁簿步驟的標題

釘選到儀表板之活頁簿或活頁簿步驟的標題，會保留其在活頁簿中的相同名稱。 若要變更標題，您必須儲存自己的活頁簿複本。 接著，就能夠在按下儲存圖示之前，先將活頁簿命名。

![選取頂端的儲存圖示，以儲存活頁簿的複本，並變更其名稱](./media/storage-insights-overview/fqa-change-workbook-name.png)

若要在已儲存的活頁簿中變更步驟的名稱，請選取 [在此步驟中編輯]，然後選取 [設定] 底部的齒輪。

![選取活頁簿步驟底部的 [編輯]，以開啟設定](./media/storage-insights-overview/fqa-edit.png)
![在 [設定] 中選取底部的齒輪，即可變更步驟名稱](./media/storage-insights-overview/fqa-change-name.png)

## <a name="next-steps"></a>後續步驟

深入瞭解活頁簿設計來支援的案例、如何撰寫新的報表及自訂現有的報表，以及如何 [使用 Azure 監視器活頁簿來建立互動式報表](../platform/workbooks-overview.md)。
