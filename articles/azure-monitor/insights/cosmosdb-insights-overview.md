---
title: 使用適用於 Cosmos DB 的 Azure 監視器監視 Azure Cosmos DB | Microsoft Docs
description: 此文章描述適用於 Cosmos DB 的 Azure 監視器功能，可讓 Cosmos DB 擁有者快速了解其 CosmosDB 帳戶的效能和使用問題。
author: mrbullwinkle
ms.author: mbullwin
ms.topic: conceptual
ms.date: 05/11/2020
ms.openlocfilehash: 5e40f91945f83af7880f272f1578370f2ee1ec42
ms.sourcegitcommit: e15c0bc8c63ab3b696e9e32999ef0abc694c7c41
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/16/2020
ms.locfileid: "97608488"
---
# <a name="explore-azure-monitor-for-azure-cosmos-db"></a>探索適用於 Azure Cosmos DB 的 Azure 監視器

適用於 Azure Cosmos DB 的 Azure 監視器可供您透過統一的互動式體驗，檢視所有 Azure Cosmos DB 資源的整體效能、失敗、容量及作業健康狀態。 此文章將協助您了解這項新監視體驗的優點，以及如何修改和調整體驗，以符合組織的獨特需求。   

## <a name="introduction"></a>簡介

深入體驗之前，您應該先了解其呈現和視覺化資訊的方式。 

其可提供：

* 單一位置所有訂用帳戶中的 Azure Cosmos DB 資源 **整體觀點**，而且能夠選擇性地將範圍僅限定在您想要評估的訂用帳戶和資源。

* 特定 Azure CosmosDB 資源的 **向下切入分析**，可協助診斷問題，或依類別 (使用量、失敗、容量和作業) 來執行詳細的分析。 選取其中任何一個選項可讓您深入檢視相關的 Azure Cosmos DB 計量。  

* **可自訂** - 此體驗建立在 Azure 監視器活頁簿範本的基礎之上，可讓您變更所顯示的計量、修改或設定符合限制的閾值，然後儲存到自訂的活頁簿中。 接著，活頁簿中的圖表可以釘選到 Azure 儀表板。  

此功能不會要求您啟用或設定任何項目，預設會收集這些 Azure Cosmos DB 計量。

>[!NOTE]
>存取此功能不需要付費，您只需要支付所設定或啟用的 Azure 監視器基本功能的費用，如 [Azure 監視器定價詳細資料](https://azure.microsoft.com/pricing/details/monitor/)頁面上所述。

## <a name="view-utilization-and-performance-metrics-for-azure-cosmos-db"></a>檢視 Azure Cosmos DB 的使用率和效能計量

若要檢視您所有訂用帳戶的儲存體帳戶使用率和效能，請執行下列步驟。

1. 登入 [Azure 入口網站](https://portal.azure.com)。

2. 搜尋 **監視器**，然後選取 [監視器]。

    ![包含「監視器」一詞的搜尋方塊，以及一個顯示服務「監視器」和速度表樣式影像的下拉式清單](./media/cosmosdb-insights-overview/search-monitor.png)

3. 選取 [Cosmos DB]。

    ![Cosmos DB 概觀活頁簿的螢幕擷取畫面](./media/cosmosdb-insights-overview/cosmos-db.png)

### <a name="overview"></a>概觀

在 [概觀] 上，資料表會顯示互動式 Azure Cosmos DB 計量。 您可以根據您從下列下拉式清單中選取的選項，篩選結果：

* **訂用帳戶** - 只會列出具有 Azure Cosmos DB 資源的訂用帳戶。  

* **Cosmos DB** - 您可以選取全部、子集或單一 Azure Cosmos DB 資源。

* **時間範圍** - 預設會根據對應的選取項目，顯示過去 4 小時的資訊。

下拉式清單底下的計數器磚會彙總所選訂用帳戶中 Azure Cosmos DB 資源的總數。 在活頁簿中，報告交易計量的資料行有條件式色彩編碼或熱度圖。 最深的色彩具有最高的值，而較淺的色彩是以最低值為基礎。 

選取其中一個 Azure Cosmos DB 資源旁的下拉式箭頭，將會顯示個別資料庫容器層級的效能計量明細：

![展開的下拉式清單顯示個別資料庫容器與相關聯的效能明細](./media/cosmosdb-insights-overview/container-view.png)

選取以藍色醒目提示的 Azure Cosmos DB 資源名稱時，將會帶您前往相關 Azure Cosmos DB 帳戶的預設 [概觀]。 

### <a name="failures"></a>失敗

選取頁面頂端的 [失敗]，活頁簿範本的 [失敗] 部分就會開啟。 其會顯示要求總數，以及組成那些要求的回應分佈：

![依 HTTP 要求類型細分失敗的螢幕擷取畫面](./media/cosmosdb-insights-overview/failures.png)

| 程式碼 |  描述       | 
|-----------|:--------------------|
| `200 OK`  | 下列其中一個 REST 作業成功： </br>- 在資源上 GET。 </br> - 在資源上 PUT。 </br> - 在資源上 POST。 </br> - 在可執行預存程序的預存程序資源上 POST。|
| `201 Created` | 建立資源的 POST 作業成功。 |
| `404 Not Found` | 作業嘗試在已經不存在的資源上執行。 例如，資源可能已經遭到刪除。 |

如需狀態碼的完整清單，請參閱 [Azure Cosmos DB HTTP 狀態碼](/rest/api/cosmos-db/http-status-codes-for-cosmosdb) \(部分機器翻譯\) 一文。

### <a name="capacity"></a>Capacity

選取頁面頂端的 [容量]，活頁簿範本的 [容量] 部分就會開啟。 其會顯示您擁有的文件數目、您文件隨著時間成長的數目、資料使用量，以及剩餘的可用儲存空間總計。  這可以用來協助識別潛在的儲存體和資料使用量問題。

![容量活頁簿](./media/cosmosdb-insights-overview/capacity.png) 

如同概觀活頁簿，在 [訂用帳戶] 資料行中選取 Azure Cosmos DB 資源旁的下拉式清單，將會依組成資料庫的個別容器顯示明細。

### <a name="operations"></a>作業 

選取頁面頂端的 [作業]，活頁簿範本的 [作業] 部分就會開啟。 其可讓您查看依提出的要求類型細分的要求。 

因此，在下面的範例中，您會看到 `eastus-billingint` 主要是接收 read 要求，但有少量的 upsert 和 create 要求。 從要求的觀點來看，`westeurope-billingint` 是唯讀的，但在過去至少四小時內，活頁簿目前的範圍是透過其時間範圍參數來表示。

![作業活頁簿](./media/cosmosdb-insights-overview/operation.png) 

## <a name="pin-export-and-expand"></a>釘選、匯出和展開

您可以將其中任何一個計量區段釘選到 [Azure 儀表板](../../azure-portal/azure-portal-dashboards.md)，只要選取該區段右上角的圖釘圖示即可。

![計量區段釘選到儀表板範例](./media/cosmosdb-insights-overview/pin.png)

若要將您的資料匯出為 Excel 格式，請選取圖釘圖示左側的向下鍵圖示。

![匯出活頁簿圖示](./media/cosmosdb-insights-overview/export.png)

若要展開或摺疊活頁簿中的所有下拉式檢視，請選取 [匯出] 圖示左側的 [展開] 圖示：

![展開活頁簿圖示](./media/cosmosdb-insights-overview/expand.png)

## <a name="customize-azure-monitor-for-azure-cosmos-db"></a>自訂適用於 Azure Cosmos DB 的 Azure 監視器

此體驗建立在 Azure 監視器活頁簿範本的基礎之上，因此您可以在自訂活頁簿中 [自訂] > [編輯] 和 [儲存] 修改過的版本複本。 

![自訂列](./media/cosmosdb-insights-overview/customize.png)

活頁簿會儲存在資源群組內，保存在您私人使用的 [我的報表] 區段中，或是可供每個具有資源群組存取權的人員存取的 [共用報表] 區段中。 儲存自訂活頁簿之後，您必須移至活頁簿資源庫加以啟動。

![從命令列啟動活頁簿資源庫](./media/cosmosdb-insights-overview/gallery.png)

## <a name="troubleshooting"></a>疑難排解

如需疑難排解指引，請參閱專用的以活頁簿為基礎的深入解析 [疑難排解文章](troubleshoot-workbooks.md)。

## <a name="next-steps"></a>後續步驟

* 設定[計量警示](../platform/alerts-metric.md)和[服務健康情況通知](../../service-health/alerts-activity-log-service-notifications-portal.md)，以設定自動化警示來協助偵測問題。

* 檢閱[使用 Azure 監視器活頁簿建立互動式報表](../platform/workbooks-overview.md)，以了解設計活頁簿以提供支援、如何撰寫新報表和自訂現有報表等等的案例。
