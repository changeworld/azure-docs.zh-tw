---
title: 使用適用于 Cosmos DB 的 Azure 監視器監視 Azure Cosmos DB （預覽） |Microsoft Docs
description: 本文說明 Cosmos DB 功能的 Azure 監視器，可讓 Cosmos DB 擁有者快速瞭解其 CosmosDB 帳戶的效能和使用問題。
ms.subservice: ''
ms.topic: conceptual
author: mrbullwinkle
ms.author: mbullwin
ms.date: 10/27/2019
ms.openlocfilehash: 9a900a2f2e950fe9b9846ebcc047d7c344284948
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "78250685"
---
# <a name="explore-azure-monitor-for-azure-cosmos-db-preview"></a>探索 Azure Cosmos DB 的 Azure 監視器（預覽）

Azure 監視器 for Azure Cosmos DB （預覽）可讓您以統一的互動體驗，查看所有 Azure Cosmos DB 資源的整體效能、失敗、容量和操作健全狀況。 本文將協助您瞭解這項新監視體驗的優點，以及如何修改和調整體驗，以符合組織的獨特需求。   

## <a name="introduction"></a>簡介

在深入探討經驗之前，您應該先瞭解它呈現和視覺化資訊的方式。 

它提供：

* 在單一位置的所有訂用帳戶中，以大規模的方式提供您 Azure Cosmos DB 資源的**觀點**，並可選擇性地將範圍限定在您想要評估的訂用帳戶和資源。

* 向下切入特定 Azure CosmosDB 資源的**分析**，以協助診斷問題，或依類別來執行詳細的分析-使用量、失敗、容量和作業。 選取其中任何一個選項，可讓您深入瞭解相關的 Azure Cosmos DB 計量。  

* **可自訂**-這項體驗是以 Azure 監視器活頁簿範本為基礎，可讓您變更所顯示的度量、修改或設定符合限制的臨界值，然後儲存至自訂活頁簿。 然後，活頁簿中的圖表可以釘選到 Azure 儀表板。  

這項功能不會要求您啟用或設定任何專案，預設會收集這些 Azure Cosmos DB 計量。

>[!NOTE]
>存取這項功能不需要付費，您只需支付所設定或啟用的 Azure 監視器基本功能的費用，如[Azure 監視器定價詳細資料](https://azure.microsoft.com/pricing/details/monitor/)頁面所述。

## <a name="view-utilization-and-performance-metrics-for-azure-cosmos-db"></a>查看 Azure Cosmos DB 的使用率和效能計量

若要查看您所有訂用帳戶的儲存體帳戶使用率和效能，請執行下列步驟。

1. 登入 [Azure 入口網站](https://portal.azure.com)。

2. 搜尋 [**監視**]，然後選取 [**監視**]。

    ![含有 "Monitor" 一字的搜尋方塊，以及顯示服務 "Monitor" 的下拉式清單（具有 speedometer 樣式影像）](./media/cosmosdb-insights-overview/search-monitor.png)

3. 選取 **[Cosmos DB （預覽）**]。

    ![Cosmos DB 總覽活頁簿的螢幕擷取畫面](./media/cosmosdb-insights-overview/cosmos-db.png)

### <a name="overview"></a>總覽

在**總覽**中，資料表會顯示互動式 Azure Cosmos DB 計量。 您可以根據您從下列下拉式清單中選取的選項來篩選結果：

* **訂閱**-只會列出具有 Azure Cosmos DB 資源的訂用帳戶。  

* **Cosmos DB** -您可以選取 [全部]、[子集] 或 [單一 Azure Cosmos DB 資源]。

* **時間範圍**：根據預設，會依據對應的選取專案顯示最後4小時的資訊。

下拉式清單底下的 [計數器] 磚會匯總所選訂用帳戶中 Azure Cosmos DB 資源的總數。 在報表交易計量的活頁簿中，資料行有條件式色彩編碼或熱度圖。 最深的色彩具有最高的值，而較淡的色彩則是根據最低值。 

選取其中一個 Azure Cosmos DB 資源旁的下拉式箭號，將會顯示個別資料庫容器層級的效能計量明細：

![展開的下拉式顯示會顯示個別的資料庫容器和相關聯的效能細目](./media/cosmosdb-insights-overview/container-view.png)

選取以藍色反白顯示的 Azure Cosmos DB 資源名稱，會帶您前往相關聯 Azure Cosmos DB 帳戶的預設**總覽**。 

### <a name="failures"></a>失敗

選取頁面頂端的 [**失敗**]，活頁簿範本的 [**失敗**] 部分隨即開啟。 它會顯示要求總數，以及組成這些要求的回應分佈：

![依 HTTP 要求類型的細目的失敗螢幕擷取畫面](./media/cosmosdb-insights-overview/failures.png)

| 程式碼      |  描述       | 
|-----------|:--------------------|
| `200 OK`  | 已成功完成下列其中一個 REST 作業： </br>-在資源上取得。 </br> -放在資源上。 </br> -在資源上張貼。 </br> -在預存程式資源上張貼，以執行預存程式。|
| `201 Created` | 成功完成建立資源的 POST 作業。 |
| `404 Not Found` | 作業嘗試在已經不存在的資源上執行。 例如，資源可能已經被刪除。 |

如需狀態碼的完整清單，請參閱[AZURE COSMOS DB HTTP 狀態碼一文](https://docs.microsoft.com/rest/api/cosmos-db/http-status-codes-for-cosmosdb)。

### <a name="capacity"></a>Capacity

選取頁面頂端的 [**容量**]，[活頁簿] 範本的 [**容量**] 部分隨即開啟。 它會顯示您擁有多少份檔、您的檔隨著時間成長、資料使用量，以及剩餘的可用儲存空間總量。  這可以用來協助識別潛在的儲存體和資料使用問題。

![容量活頁簿](./media/cosmosdb-insights-overview/capacity.png) 

如同總覽活頁簿，在 [**訂閱**] 資料行中選取 Azure Cosmos DB 資源旁的下拉式清單，將會顯示構成資料庫的個別容器明細。

### <a name="operations"></a>作業 

選取頁面頂端的 [**作業**]，活頁簿範本的**作業**部分隨即開啟。 它可讓您查看依提出的要求類型細分的要求。 

因此，在下列範例中，您`eastus-billingint`會看到主要是接收讀取要求，但有少量的 upsert 和 create 要求。 從`westeurope-billingint`要求的角度來看，這是唯讀的，至少在過去四小時內，活頁簿目前的範圍是透過其時間範圍參數。

![作業活頁簿](./media/cosmosdb-insights-overview/operation.png) 

## <a name="pin-export-and-expand"></a>釘選、匯出和展開

您可以選取區段右上角的圖釘圖示，將任何一個度量區段釘選到[Azure 儀表板](https://docs.microsoft.com/azure/azure-portal/azure-portal-dashboards)。

![度量區段釘選至儀表板範例](./media/cosmosdb-insights-overview/pin.png)

若要將您的資料匯出為 Excel 格式，請選取圖釘圖示左邊的向下箭號圖示。

![匯出活頁簿圖示](./media/cosmosdb-insights-overview/export.png)

若要展開或折迭活頁簿中的所有下拉式視圖，請選取 [匯出] 圖示左邊的展開圖示：

![展開活頁簿圖示](./media/cosmosdb-insights-overview/expand.png)

## <a name="customize-azure-monitor-for-azure-cosmos-db-preview"></a>自訂 Azure Cosmos DB 的 Azure 監視器（預覽）

由於這項體驗是以 Azure 監視器活頁簿範本為基礎，因此您可以**自訂** > **編輯**，並將修改過的版本複本**儲存**到自訂活頁簿中。 

![自訂橫條](./media/cosmosdb-insights-overview/customize.png)

活頁簿會儲存在資源群組中，不論是在您私人的**我的報表**區段中，或是在可存取資源群組的每個人都可存取的 [**共用報表**] 區段中。 儲存自訂活頁簿之後，您必須移至活頁簿庫來啟動它。

![從命令列啟動活頁簿圖庫](./media/cosmosdb-insights-overview/gallery.png)

## <a name="next-steps"></a>後續步驟

* 設定計量[警示](../platform/alerts-metric.md)和[服務健康情況通知](../../service-health/alerts-activity-log-service-notifications.md)，以設定自動化警示以協助偵測問題。

* 瞭解活頁簿設計來支援的案例、如何撰寫新的和自訂現有的報表，以及如何[使用 Azure 監視器活頁簿建立互動式報表](../app/usage-workbooks.md)。
