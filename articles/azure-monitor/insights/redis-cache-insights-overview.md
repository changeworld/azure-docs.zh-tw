---
title: 適用於 Azure Cache for Redis 的 Azure 監視器 (預覽) | Microsoft Docs
description: 此文章描述適用於 Azure Redis Cache 的 Azure 監視器功能，其可讓快取擁有者快速了解效能和使用率問題。
ms.topic: conceptual
author: mrbullwinkle
ms.author: mbullwin
ms.date: 05/21/2020
ms.openlocfilehash: 70bf39011761bcf59c8270ecdcc0542e326aef42
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/23/2020
ms.locfileid: "87045852"
---
# <a name="explore-azure-monitor-for-azure-cache-for-redis-preview"></a>探索適用於 Azure Cache for Redis 的 Azure 監視器 (預覽)

適用於 Azure Cache for Redis 的 Azure 監視器 (預覽) 可針對您所有的 Azure Cache for Redis 資源，提供下列統一的互動式檢視：

- 整體效能
- 失敗
- Capacity
- 作業健康狀態

此文章可協助您了解這項新監視體驗的優點。 其也會示範如何修改和調整體驗，以符合貴組織的獨特需求。

## <a name="introduction"></a>簡介

開始體驗之前，您應該了解適用於 Azure Cache for Redis 的 Azure 監視器如何以視覺化方式呈現資訊。

其可提供：

- Azure Cache for Redis 資源在所有訂用帳戶單一位置的**整體觀點**。 您可以選擇性地將範圍僅限定於您想要評估的訂用帳戶與資源。

- 特定 Azure Cache for Redis 資源的**向下切入分析**。 您可以診斷問題，並查看使用量、失敗、容量和作業的詳細分析。 選取其中任何類別，以查看相關資訊的深入檢視。  

- **自訂**體驗，建立在 Azure 監視器活頁簿範本的基礎之上。 此體驗可讓您變更所顯示的計量，以及修改或設定符合您限制的閾值。 您可以將變更儲存在自訂活頁簿中，然後將活頁簿圖表釘選到 Azure 儀表板。

此功能不會要求您啟用或設定任何項目。 預設會收集 Azure Cache for Redis 資訊。

>[!NOTE]
>存取此功能不需要付費。 您只需要支付所設定或啟用的 Azure 監視器基本功能的費用，如 [Azure 監視器定價詳細資料](https://azure.microsoft.com/pricing/details/monitor/)頁面上所述。

## <a name="view-utilization-and-performance-metrics-for-azure-cache-for-redis"></a>檢視 Azure Cache for Redis 的使用率和效能計量

若要檢視您所有訂用帳戶的儲存體帳戶使用率和效能，請執行下列步驟：

1. 登入 [Azure 入口網站](https://portal.azure.com)。

1. 搜尋**監視器**，然後選取 [監視器]。

    ![包含「監視器」一詞的搜尋方塊，以及顯示「監視器」和速度表符號的服務搜尋結果](./media/cosmosdb-insights-overview/search-monitor.png)

1. 選取 [Azure Cache for Redis (預覽)]。 如果此選項不存在，請選取 [更多] > [Azure Cache for Redis]。

### <a name="overview"></a>概觀

在 [概觀] 上，資料表會顯示互動式 Azure Cache for Redis 計量。 您可以根據您從下列下拉式清單中選取的選項，篩選結果：

- **訂用帳戶**：只會列出具有 Azure Cache for Redis 資源的訂用帳戶。  

- **Azure Cache for Redis**：您可以選取全部、子集或單一 Azure Cache for Redis 資源。

- **時間範圍**：資料表預設會根據對應的選取項目，顯示過去四小時的資訊。

下拉式清單底下有一個計數器圖格。 此圖格會顯示所選訂用帳戶中的 Azure Cache for Redis 資源總數。 活頁簿資料行的條件式色彩代碼或熱度圖會報告交易計量。 最深的色彩代表最高的值。 較淺的色彩代表較低的值。

選取其中一個 Azure Cache for Redis 資源旁的下拉式清單箭號，將會顯示個別資源層級的效能計量明細。

![概觀體驗的螢幕擷取畫面](./media/redis-cache-insights-overview/overview.png)

當您選取以藍色醒目提示的 Azure Cache for Redis 資源名稱時，您會看到相關帳戶的預設 [概觀] 資料表。 其會顯示下列資料行：

- **已使用的記憶體**
- **已使用的記憶體百分比**
- **伺服器負載**
- **伺服器負載時間軸**
- **CPU**
- **連線的用戶端**
- **快取遺漏**
- **錯誤 (最大值)**

### <a name="operations"></a>作業

選取頁面頂端的 [作業] 時，活頁簿範本的 [作業] 資料表就會開啟。 其會顯示下列資料行：

- **作業總計**
- **作業總計時間軸**
- **每秒的作業數**
- **取得數**
- **設定數**

![作業體驗的螢幕擷取畫面](./media/redis-cache-insights-overview/operations.png)

### <a name="usage"></a>使用量

選取頁面頂端的 [使用量] 時，活頁簿範本的 [使用量] 資料表就會開啟。 其會顯示下列資料行：

- **快取讀取**
- **快取讀取時間軸**
- **快取寫入**
- **快取命中**
- **快取遺漏**

![使用量體驗的螢幕擷取畫面](./media/redis-cache-insights-overview/usage.png)

### <a name="failures"></a>失敗

選取頁面頂端的 [失敗] 時，活頁簿範本的 [失敗] 資料表就會開啟。 其會顯示下列資料行：

- **錯誤總數**
- **容錯移轉/錯誤**
- **UnresponsiveClient/錯誤**
- **RDB/錯誤**
- **AOF/錯誤**
- **匯出/錯誤**
- **資料遺失/錯誤**
- **匯入/錯誤**

![依 HTTP 要求類型細分失敗的螢幕擷取畫面](./media/redis-cache-insights-overview/failures.png)

### <a name="metric-definitions"></a>計量定義

如需形成這些活頁簿之計量定義的完整清單，請參閱[可用的計量和報告間隔](../../azure-cache-for-redis/cache-how-to-monitor.md#available-metrics-and-reporting-intervals) \(部分機器翻譯\) 一文。

## <a name="view-from-an-azure-cache-for-redis-resource"></a>Azure Cache for Redis 資源的檢視

若要直接從個別資源存取適用於 Azure Cache for Redis 的 Azure 監視器：

1. 在 Azure 入口網站中，選取 [Azure Cache for Redis]。

2. 從清單中，選擇個別的 Azure Cache for Redis 資源。 在 [監視] 區段中，選擇 [Insights (預覽)]。

    ![以紅色方塊醒目提示 [Insights (預覽)] 字樣的功能表選項螢幕擷取畫面](./media/redis-cache-insights-overview/insights.png)

您也可以從 Azure 監視器層級活頁簿中選取 Azure Cache for Redis 的資源名稱，以存取這些檢視。

### <a name="resource-level-overview"></a>資源層級概觀

在 Azure Redis Cache 的 [概觀] 活頁簿上，會顯示數個可讓您存取下列項目的效能計量：

- 互動式效能圖表，顯示與 Azure Cache for Redis 效能相關的最基本詳細資料。

- 計量和狀態圖格，醒目提示分區效能、連線用戶端總數和整體延遲。

![概觀儀表板的螢幕擷取畫面，其中顯示 CPU 效能、已使用的記憶體、連線用戶端、錯誤、過期的金鑰和已逾時的金鑰的相關資訊](./media/redis-cache-insights-overview/resource-overview.png)

選取 [效能] 或 [作業] 的任何其他索引標籤，會開啟對應的活頁簿。

### <a name="resource-level-performance"></a>資源層級效能

![資源效能圖表的螢幕擷取畫面](./media/redis-cache-insights-overview/resource-performance.png)

### <a name="resource-level-operations"></a>資源層級作業

![資源作業圖表的螢幕擷取畫面](./media/redis-cache-insights-overview/resource-operations.png)

## <a name="pin-export-and-expand"></a>釘選、匯出和展開

若要將任何計量區段釘選到 [Azure 儀表板](../../azure-portal/azure-portal-dashboards.md)，請選取該區段右上角的圖釘符號。

![醒目提示圖釘符號的計量區段](./media/cosmosdb-insights-overview/pin.png)

若要將您的資料匯出為 Excel 格式，請選取圖釘符號左側的向下鍵符號。

![醒目提示的匯出活頁簿符號](./media/cosmosdb-insights-overview/export.png)

若要展開或摺疊活頁簿中的所有檢視，請選取 [匯出] 符號左側的 [展開] 符號。

![醒目提示的展開活頁簿符號](./media/cosmosdb-insights-overview/expand.png)

## <a name="customize-azure-monitor-for-azure-cache-for-redis-preview"></a>自訂適用於 Azure Cache for Redis 的 Azure 監視器 (預覽)

此體驗建立在 Azure 監視器活頁簿範本的基礎之上，因此您可以選取 [自訂] > [編輯] > [儲存]，將修改過的版本複本儲存到自訂活頁簿中。

![醒目提示 [自訂] 的命令列](./media/cosmosdb-insights-overview/customize.png)

活頁簿會儲存在資源群組的 [我的報表] 區段或 [共用報表] 區段中。 [我的報表] 僅供您使用。 [共用報表] 可供具有資源群組存取權的每個人使用。

儲存自訂活頁簿之後，請移至活頁簿資源庫加以開啟。

![醒目提示 [資源庫] 的命令列](./media/cosmosdb-insights-overview/gallery.png)

## <a name="troubleshooting"></a>疑難排解

如需疑難排解指引，請參閱專用的活頁簿型深入解析[疑難排解文章](troubleshoot-workbooks.md)。

## <a name="next-steps"></a>後續步驟

* 設定[計量警示](../platform/alerts-metric.md)和[服務健康情況通知](../../service-health/alerts-activity-log-service-notifications-portal.md)，以設定可協助偵測問題的自動化警示。

* 檢閱[使用 Azure 監視器活頁簿建立互動式報表](../platform/workbooks-overview.md)，以了解活頁簿支援、如何撰寫或自訂報表等等的案例。
