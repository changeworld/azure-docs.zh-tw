---
title: 使用 Azure 監視器監視宇宙資料庫（預覽）*微軟文檔
description: 本文介紹了 Cosmos DB 的 Azure 監視器功能，該功能使 Cosmos DB 擁有者能夠快速瞭解其 CosmosDB 帳戶的性能和利用率問題。
ms.subservice: ''
ms.topic: conceptual
author: mrbullwinkle
ms.author: mbullwin
ms.date: 10/27/2019
ms.openlocfilehash: 9a900a2f2e950fe9b9846ebcc047d7c344284948
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "78250685"
---
# <a name="explore-azure-monitor-for-azure-cosmos-db-preview"></a>流覽 Azure 宇宙資料庫的 Azure 監視器（預覽）

Azure Cosmos DB 的 Azure 監視器（預覽版）在統一的互動式體驗中提供了所有 Azure Cosmos DB 資源的總體性能、故障、容量和操作運行狀況的視圖。 本文將説明您瞭解此新的監視體驗的好處，以及如何修改和調整體驗以適應組織的獨特需求。   

## <a name="introduction"></a>簡介

在深入瞭解體驗之前，您應該瞭解它如何呈現和視覺化資訊。 

它提供：

* 從 Azure Cosmos DB 資源**縮放到**單個位置的所有訂閱，並能夠有選擇地僅擴展到您感興趣的訂閱和資源。

* **向下切入**對特定 Azure CosmosDB 資源的分析，以説明診斷問題或按類別執行詳細分析 - 利用率、故障、容量和操作。 選擇其中任一選項可深入瞭解相關的 Azure Cosmos DB 指標。  

* **可自訂**- 此體驗基於 Azure 監視器活頁簿範本構建，允許您更改顯示的指標，修改或設置符合限制的閾值，然後保存到自訂活頁簿中。 然後，可以將活頁簿中的圖表固定到 Azure 儀表板。  

此功能不要求您啟用或配置任何內容，預設情況下將收集這些 Azure Cosmos DB 指標。

>[!NOTE]
>訪問此功能不收取任何費用，並且只需為配置或啟用的 Azure 監視器基本功能付費，如[Azure 監視器定價詳細資訊](https://azure.microsoft.com/pricing/details/monitor/)頁上所述。

## <a name="view-utilization-and-performance-metrics-for-azure-cosmos-db"></a>查看 Azure 宇宙資料庫的利用率和性能指標

要查看存儲帳戶在所有訂閱中的利用率和性能，請執行以下步驟。

1. 登錄到 Azure[門戶](https://portal.azure.com)。

2. 搜索**監視器**並選擇**監視器**。

    ![帶有"監視器"字樣的搜索框和顯示服務"監視器"且速度計樣式圖像的下拉清單](./media/cosmosdb-insights-overview/search-monitor.png)

3. 選擇**宇宙資料庫（預覽）。**

    ![宇宙資料庫概述活頁簿的螢幕截圖](./media/cosmosdb-insights-overview/cosmos-db.png)

### <a name="overview"></a>總覽

在**概述**上，該表顯示互動式 Azure 宇宙資料庫指標。 您可以根據從以下下拉清單中選擇的選項篩選結果：

* **訂閱**- 僅列出具有 Azure Cosmos DB 資源的訂閱。  

* **宇宙資料庫**- 您可以選擇所有、子集或單個 Azure Cosmos 資料庫資源。

* **時間範圍**- 預設情況下，根據所做的相應選擇顯示最後 4 小時的資訊。

下拉清單下的計數器磁貼將 Azure Cosmos DB 資源的總數匯總到所選訂閱中。 活頁簿中報告交易記錄指標的列有條件顏色編碼或熱圖。 最深的顏色具有最高值，較淺的顏色基於最低值。 

在 Azure Cosmos 資料庫資源之一旁邊選擇下拉箭頭將顯示單個資料庫容器級別的性能指標的細目：

![展開下拉清單，顯示單個資料庫容器和相關性能細分](./media/cosmosdb-insights-overview/container-view.png)

選擇以藍色突出顯示的 Azure Cosmos DB 資源名稱將帶您到關聯的 Azure Cosmos DB 帳戶的預設 **"概述**"。 

### <a name="failures"></a>失敗

選擇頁面頂部的 **"失敗"，** 並打開活頁簿範本的 **"失敗"** 部分。 它顯示包含構成這些請求的回應分佈的總請求總數：

![按 HTTP 要求類型細分的失敗螢幕截圖](./media/cosmosdb-insights-overview/failures.png)

| 程式碼      |  描述       | 
|-----------|:--------------------|
| `200 OK`  | 已成功完成下列其中一個 REST 作業： </br>- 獲取資源。 </br> - 在資源上打開。 </br> - 在資源上打開 POST。 </br> - 在預存程序資源上 POST 以執行預存程序。|
| `201 Created` | 成功完成建立資源的 POST 作業。 |
| `404 Not Found` | 作業嘗試在已經不存在的資源上執行。 例如，資源可能已經被刪除。 |

有關狀態碼的完整清單，請參閱 Azure[宇宙 DB HTTP 狀態碼一文](https://docs.microsoft.com/rest/api/cosmos-db/http-status-codes-for-cosmosdb)。

### <a name="capacity"></a>Capacity

選擇頁面頂部的 **"容量"，** 然後打開活頁簿範本的 **"容量**"部分。 它顯示您擁有的文檔數、文檔隨時間的增長、資料使用方式以及您留下的可用存儲總量。  這可用於説明確定潛在的存儲和資料利用率問題。

![容量活頁簿](./media/cosmosdb-insights-overview/capacity.png) 

與概述活頁簿一樣，在 **"訂閱"** 列中選擇 Azure Cosmos DB 資源旁邊的下拉清單將顯示構成資料庫的各個容器的細目。

### <a name="operations"></a>作業 

選擇頁面頂部的 **"操作"，** 然後打開活頁簿範本的 **"操作**"部分。 它使您能夠查看按請求類型細分的請求。 

因此，在下面的示例中，您可以看到主要`eastus-billingint`接收讀取請求，但使用少量 upsert 和創建請求。 而`westeurope-billingint`從請求的角度來看是唯讀的，但至少在過去四個小時中，活頁簿當前通過其時間範圍參數被限定為。

![操作活頁簿](./media/cosmosdb-insights-overview/operation.png) 

## <a name="pin-export-and-expand"></a>固定、匯出和擴展

您可以通過選擇分區右上角的圖釘圖示，將任一指標部分固定到[Azure 儀表板](https://docs.microsoft.com/azure/azure-portal/azure-portal-dashboards)。

![指標節針到儀表板示例](./media/cosmosdb-insights-overview/pin.png)

要將資料匯出到 Excel 格式，請選擇圖釘圖示左側的向下箭頭圖示。

![匯出活頁簿圖示](./media/cosmosdb-insights-overview/export.png)

要展開或折疊活頁簿中的所有下拉視圖，請選擇匯出圖示左側的展開圖示：

![展開活頁簿圖示](./media/cosmosdb-insights-overview/expand.png)

## <a name="customize-azure-monitor-for-azure-cosmos-db-preview"></a>自訂 Azure 宇宙資料庫的 Azure 監視器（預覽）

由於此體驗是在 Azure 監視器活頁簿範本之上構建的，因此您可以**自訂** > **編輯**並將修改後的版本的副本**保存到**自訂活頁簿中。 

![自訂欄](./media/cosmosdb-insights-overview/customize.png)

活頁簿保存在資源組中，無論是在您專用的"**我的報告"** 部分中，還是"**共用報表**"部分中，有權訪問資源組的每個人都可以訪問。 保存自訂活頁簿後，需要轉到活頁簿庫來啟動它。

![從命令列啟動活頁簿庫](./media/cosmosdb-insights-overview/gallery.png)

## <a name="next-steps"></a>後續步驟

* 配置[指標警報](../platform/alerts-metric.md)[和服務運行狀況通知](../../service-health/alerts-activity-log-service-notifications.md)以設置自動警報以説明檢測問題。

* 瞭解活頁簿旨在支援的方案、如何通過查看[使用 Azure 監視器活頁簿創建互動式報表](../app/usage-workbooks.md)來編寫新報表和自訂現有報表等。
