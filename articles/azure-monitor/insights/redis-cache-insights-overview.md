---
title: 適用于 Azure Cache for Redis 的 Azure 監視器（預覽） |Microsoft Docs
description: 本文說明 Redis 快取功能的 Azure 監視器，可提供 Azure Cache for Redis 擁有者快速瞭解效能和使用率問題。
ms.topic: conceptual
author: mrbullwinkle
ms.author: mbullwin
ms.date: 05/07/2020
ms.openlocfilehash: af32459ffcb50c9f1cfdc59d2c8d355d3551e230
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/12/2020
ms.locfileid: "83210970"
---
# <a name="explore-azure-monitor-for-azure-cache-for-redis-preview"></a>探索 Azure Cache for Redis Azure 監視器（預覽）

Azure Cache for Redis （預覽）的 Azure 監視器可讓您以統一的互動體驗，查看所有 Azure Cache for Redis 資源的整體效能、失敗、容量和操作健全狀況。 本文將協助您瞭解這項新監視體驗的優點，以及如何修改和調整體驗，以符合組織的獨特需求。

## <a name="introduction"></a>簡介

在深入探討經驗之前，您應該先瞭解它呈現和視覺化資訊的方式。

它提供：

* 您的 Azure Cache for Redis 資源在單一位置的所有訂用帳戶的**規模調整**，可選擇性地將範圍限定于您想要評估的訂用帳戶和資源。

* **向下**切入特定 Azure Cache for Redis 資源的分析，以協助診斷問題，或依類別來執行詳細的分析-使用量、失敗、容量和作業。 選取其中任何一個選項，可提供相關的深入觀點。  

* **可自訂**-這項體驗是以 Azure 監視器活頁簿範本為基礎，可讓您變更所顯示的度量、修改或設定符合限制的臨界值，然後儲存至自訂活頁簿。 然後，活頁簿中的圖表可以釘選到 Azure 儀表板。  

這項功能不會要求您啟用或設定任何專案，預設會收集這些 Azure Cache for Redis。

>[!NOTE]
>存取這項功能不需要付費，您只需支付所設定或啟用的 Azure 監視器基本功能的費用，如[Azure 監視器定價詳細資料](https://azure.microsoft.com/pricing/details/monitor/)頁面所述。

## <a name="view-utilization-and-performance-metrics-for-azure-cache-for-redis"></a>查看 Azure Cache for Redis 的使用率和效能計量

若要查看您所有訂用帳戶的儲存體帳戶使用率和效能，請執行下列步驟。

1. 登入 [Azure 入口網站](https://portal.azure.com)。

2. 搜尋 [**監視**]，然後選取 [**監視**]。

    ![含有 "Monitor" 一字的搜尋方塊，以及顯示服務 "Monitor" 的下拉式清單（具有 speedometer 樣式影像）](./media/cosmosdb-insights-overview/search-monitor.png)

3. 選取 **[Azure Cache For Redis （預覽）**]。

如果此選項不存在，請按一下 [**其他**]，然後選取 [ **Azure Cache for Redis**]。

### <a name="overview"></a>概觀

**總覽**時，資料表會顯示互動式 Azure Cache for Redis 計量。 您可以根據您從下列下拉式清單中選取的選項來篩選結果：

* 訂用**帳戶-只**會列出具有 Azure Cache for Redis 資源的訂用帳戶。  

* **Azure cache For Redis** -您可以選取 [全部]、[子集] 或 [單一 Azure Cache for Redis] 資源。

* **時間範圍**：根據預設，會依據對應的選取專案顯示最後4小時的資訊。

下拉式清單底下的 [計數器] 磚會匯總所選訂用帳戶中的 Azure Cache for Redis 資源總數。 在報表交易計量的活頁簿中，資料行有條件式色彩編碼或熱度圖。 最深的色彩具有最高的值，而較淡的色彩則是根據最低值。

選取其中一個 Azure Cache for Redis 資源旁的下拉式箭號，將會顯示個別資源層級的效能計量明細：

![總覽體驗的螢幕擷取畫面](./media/redis-cache-insights-overview/overview.png)

選取以藍色反白顯示的 Azure Cache for Redis 資源名稱，會帶您前往相關帳戶的預設**總覽**。 它會顯示 `Used Memory` 、 `Used Memory Percentage` 、 `Server Load` 、 `Server Load Timeline` 、、、 `CPU` `Connected Clients` `Cache Misses` 、 `Errors (Max)` 。

### <a name="operations"></a>作業

選取頁面頂端的 [**作業**]，活頁簿範本的**作業**部分隨即開啟。 它會顯示 `Total Operations` 、、 `Total Operations Timeline` `Operations Per Second` 、 `Gets` 、 `Sets` 。

![總覽體驗的螢幕擷取畫面](./media/redis-cache-insights-overview/operations.png)

### <a name="usage"></a>使用方式

選取頁面頂端的 [**使用量**]，活頁簿範本的**使用**部分隨即開啟。 它會顯示 `Cache Read` 、、 `Cache Read Timeline` `CacheWrite` 、 `CacheHits` 、 `Cache Misses` 。

![總覽體驗的螢幕擷取畫面](./media/redis-cache-insights-overview/usage.png)

選取頁面頂端的 [**失敗**]，活頁簿範本的 [**失敗**] 部分隨即開啟。 它會顯示 `Total Errors` 、 `Failover/Errors` 、 `UnresponsiveClient/Errors` 、 `RDB/Errors` 、、、 `AOF/Errors` `Export/Errors` `Dataloss/Errors` 、 `Import/Errors` 。

![依 HTTP 要求類型的細目的失敗螢幕擷取畫面](./media/redis-cache-insights-overview/failures.png)

### <a name="metric-definitions"></a>度量定義

如需組成這些活頁簿之計量定義的完整清單，請參閱[可用的度量和報告間隔一文](https://docs.microsoft.com/azure/azure-cache-for-redis/cache-how-to-monitor#available-metrics-and-reporting-intervals)。

## <a name="pin-export-and-expand"></a>釘選、匯出和展開

您可以選取區段右上角的圖釘圖示，將任何一個度量區段釘選到[Azure 儀表板](https://docs.microsoft.com/azure/azure-portal/azure-portal-dashboards)。

![度量區段釘選至儀表板範例](./media/cosmosdb-insights-overview/pin.png)

若要將您的資料匯出為 Excel 格式，請選取圖釘圖示左邊的向下箭號圖示。

![匯出活頁簿圖示](./media/cosmosdb-insights-overview/export.png)

若要展開或折迭活頁簿中的所有下拉式視圖，請選取 [匯出] 圖示左邊的展開圖示：

![展開活頁簿圖示](./media/cosmosdb-insights-overview/expand.png)

## <a name="customize-azure-monitor-for-azure-cache-for-redis-preview"></a>自訂 Azure Cache for Redis Azure 監視器（預覽）

由於這項體驗是以 Azure 監視器活頁簿範本為基礎，因此您可以**自訂**  >  **編輯**，並**將**修改過的版本複本儲存到自訂活頁簿中。 

![自訂橫條](./media/cosmosdb-insights-overview/customize.png)

活頁簿會儲存在資源群組中，不論是在您私人的**我的報表**區段中，或是在可存取資源群組的每個人都可存取的 [**共用報表**] 區段中。 儲存自訂活頁簿之後，您必須移至活頁簿庫來啟動它。

![從命令列啟動活頁簿圖庫](./media/cosmosdb-insights-overview/gallery.png)

## <a name="next-steps"></a>後續步驟

* 設定計量[警示](../platform/alerts-metric.md)和[服務健康情況通知](../../service-health/alerts-activity-log-service-notifications.md)，以設定自動化警示以協助偵測問題。

* 瞭解活頁簿設計來支援的案例、如何撰寫新的和自訂現有的報表，以及如何[使用 Azure 監視器活頁簿建立互動式報表](../app/usage-workbooks.md)。
