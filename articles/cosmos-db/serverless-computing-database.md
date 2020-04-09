---
title: 使用 Azure Cosmos DB 與 Azure 函數進行無伺服器資料庫計算
description: 了解 Azure Cosmos DB 和 Azure Functions 如何一起使用以建立事件驅動無伺服器計算的應用程式。
author: SnehaGunda
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/17/2019
ms.author: sngun
ms.openlocfilehash: 079c246f87bb8294f3c7ad6dea3391f5c67ba0ad
ms.sourcegitcommit: 7d8158fcdcc25107dfda98a355bf4ee6343c0f5c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/09/2020
ms.locfileid: "80985247"
---
# <a name="serverless-database-computing-using-azure-cosmos-db-and-azure-functions"></a>使用 Azure Cosmos DB 和 Azure Functions 的無伺服器資料庫計算

無伺服器計算是關於著重在可重複和無狀態之個別邏輯項目的能力。 這些項目不需要任何基礎結構管理，而且它們只會針對執行的項目耗用資源數秒或數毫秒。 無伺服器計算移動的核心是 [Azure Functions](https://azure.microsoft.com/services/functions) 在 Azure 生態系統中提供的函式。 若要了解 Azure 中的其他無伺服器執行環境，請參閱 [Azure 中的無伺服器](https://azure.microsoft.com/solutions/serverless/)頁面。 

使用 [Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db) 和 Azure Functions 之間的原生整合，您可以直接從您的 Azure Cosmos DB 帳戶建立資料庫觸發程序、輸入繫結，以及輸出繫結。 使用 Azure Functions 與 Azure Cosmos DB，您可以建立及部署事件驅動無伺服器的應用程式，利用低延遲存取廣大用戶群的豐富資料。

## <a name="overview"></a>概觀

Azure Cosmos DB 與 Azure Functions 可讓您以下列方式整合資料庫與無伺服器的應用程式：

* **為Cosmos DB**創建事件驅動的Azure函數觸發器。 此觸發器依賴於[更改源](change-feed.md)流來監視 Azure Cosmos 容器的更改。 對容器進行任何變更時，變更摘要串流會傳送至觸發程序，其叫用 Azure Function。
* 或者,使用**輸入綁定**將 Azure 函數綁定到 Azure Cosmos 容器。 函式執行時，輸入繫結會從容器讀取資料。
* 使用**輸出綁定**將函數綁定到 Azure Cosmos 容器。 函式完成時，輸出繫結會將資料寫入容器。

> [!NOTE]
> 目前,Cosmos DB 的 Azure 函數觸發器、輸入綁定和輸出綁定僅支援與 SQL API 一起使用。 對於其他所有的 Azure Cosmos DB API，您應對您的 API 使用靜態用戶端，以從函式存取資料庫。


下圖逐一說明這三個整合： 

![Azure Cosmos DB 和 Azure Functions 如何整合](./media/serverless-computing-database/cosmos-db-azure-functions-integration.png)

Azure Cosmos DB 的 Azure 函數觸發器、輸入繫結和輸出繫結可用於以下組合:

* Cosmos DB 的 Azure 函數觸發器可用於綁定到其他 Azure Cosmos 容器的輸出。 在函式對變更摘要中的項目執行操作後，您可以將其寫入另一個容器 (將其寫入與來源相同的容器將有效地建立遞迴迴圈)。 或者,可以使用Cosmos DB的Azure函數觸發器,使用輸出綁定有效地將所有更改的專案從一個容器遷移到另一個容器。
* Azure Cosmos DB 的輸入繫結與輸出繫結可用於相同的 Azure Function。 這也在適用於修改後，當您想要利用輸入繫結尋找特定資料、在 Azure Function 中加以修改，然後儲存到相同的容器或不同的容器的情況下。
* 與Cosmos DB的Azure函數觸發器在同一函數中使用到Azure Cosmos容器的輸入綁定,也可以與輸出綁定一起使用。 您可以使用此組合，將最新的貨幣兌換資訊 (使用兌換容器的輸入繫結提取) 套用至購物車服務中新訂單的變更摘要。 已更新的購物車總計，以及目前套用的貨幣轉換，可以使用輸出繫結寫入第三個容器。

## <a name="use-cases"></a>使用案例

下列使用案例示範幾個方法，您可以藉由將您的資料連接至事件驅動的 Azure Functions 來充分利用 Azure Cosmos DB 資料。

### <a name="iot-use-case---azure-functions-trigger-and-output-binding-for-cosmos-db"></a>IoT 用例 - Cosmos DB 的 Azure 函數觸發器和輸出繫結

在 IoT 實作中，您可以在檢查已連線汽車中顯示的引擎燈時，叫用函式。

**實施:** 使用 Cosmos DB 的 Azure 函數觸發器和輸出繫結

1. **Cosmos DB 的 Azure 函數觸發器**用於觸發與汽車警報相關的事件,例如連接的汽車中的檢查引擎指示燈亮起。
2. 檢查引擎燈時，感應器資料會傳送到 Azure Cosmos DB。
3. Azure Cosmos DB 創建或更新新的感測器資料文件,然後將這些更改流式傳輸到 Cosmos DB 的 Azure 函數觸發器。
4. 在感應器資料收集每次資料變更時叫用觸發程序，因為所有變更會透過變更摘要串流處理。
5. 閾值條件用於函式中以將感應器資料傳送至保修部門。
6. 如果溫度亦超過特定值，也會向擁有者發送警報。
7. 函數上的**輸出綁定**將更新另一個 Azure Cosmos 容器中的汽車記錄,以存儲有關檢查引擎事件的資訊。

下圖顯示此觸發程序在 Azure 入口網站中撰寫的程式碼。

![在 Azure 門戶中為 Cosmos DB 建立 Azure 函數觸發器](./media/serverless-computing-database/cosmos-db-trigger-portal.png)

### <a name="financial-use-case---timer-trigger-and-input-binding"></a>財務使用案例 - 計時器觸發程序和輸入繫結

在財務實作中，當銀行帳戶餘額低於特定金額時，您可以叫用函式。

**實作：** 具有 Azure Cosmos DB 輸入繫結的計時器觸發程序

1. 使用[計時器觸發器](../azure-functions/functions-bindings-timer.md),可以使用**輸入綁定**以定時間隔檢索存儲在 Azure Cosmos 容器中的銀行帳戶餘額資訊。
2. 如果餘額低於使用者設定的低餘額閾值，則追蹤 Azure Function 中的動作。
3. 輸出繫結可以是 [SendGrid 整合](../azure-functions/functions-bindings-sendgrid.md)，將來自服務帳戶的電子郵件傳送至針對每個低餘額帳戶識別的電子郵件地址。

下列映像顯示此案例之 Azure 入口網站中的程式碼。

![財務案例之計時器觸發程序的 Index.js 檔案](./media/serverless-computing-database/cosmos-db-functions-financial-trigger.png)

![財務案例之計時器觸發程序的 Run.csx 檔案](./media/serverless-computing-database/azure-function-cosmos-db-trigger-run.png)

### <a name="gaming-use-case---azure-functions-trigger-and-output-binding-for-cosmos-db"></a>遊戲用例 - Cosmos DB 的 Azure 函數觸發器和輸出繫結 

在遊戲中建立新的使用者時，您可以使用 [Azure Cosmos DB Gremlin API](graph-introduction.md) 搜尋可能知道的其他使用者。 然後，您可以將結果寫入 [Azure Cosmos DB SQL 資料庫] 以方便擷取。

**實施:** 使用 Cosmos DB 的 Azure 函數觸發器和輸出繫結

1. 使用 Azure Cosmos DB[圖形資料庫](graph-introduction.md)來儲存所有使用者,可以使用 Cosmos DB 的 Azure 函數觸發器創建新函數。 
2. 每當插入新的使用者時，會叫用函式，然後使用**輸出繫結**來儲存結果。
3. 函式會查詢圖表資料庫，以搜尋與新使用者直接相關的所有使用者，並將該資料集傳回函式。
4. 接著，此資料會儲存在 Azure Cosmos DB 中，讓任何顯示新使用者其連線好友的前端應用程式輕鬆地擷取。

### <a name="retail-use-case---multiple-functions"></a>零售使用案例 - 多個函式

在零售實作中，當使用者將項目加入其購物籃中時，您可以靈活地為可選的業務管道元件建立和叫用函式。

**實施:** 多個 Azure 函數觸發器,用於宇宙 DB 偵聽一個容器

1. 您可以通過將 Cosmos DB 的 Azure 函數觸發器添加到每個函數來創建多個 Azure 函數 - 所有這些都偵聽購物車數據的相同更改源。 請注意，若有多個函式接聽相同的變更摘要，則每個函式皆需要新的租用集合。 如需有關租用集合的詳細資訊，請參閱[了解變更摘要處理器程式庫](change-feed-processor.md)。
2. 每當新的項目新增至使用者的購物車時，會根據購物車容器的變更摘要單獨叫用每個函式。
   * 某個函式可以使用目前購物籃的內容來變更使用者可能感興趣之其他項目的顯示。
   * 另一個函式可更新存貨總計。
   * 另一個函式可將特定產品的客戶資訊傳送至行銷部門，向他們傳送促銷郵件。 

     任何部門都可以通過偵聽更改源為Cosmos DB創建Azure函數,並確保它們不會延遲過程中的關鍵訂單處理事件。

在所有這些用例中,由於函數已分離應用本身,因此無需一直啟動新的應用實例。 相反地，Azure Functions 會啟動個別函式，視需要完成離散程序。

## <a name="tooling"></a>Tooling

Azure Cosmos DB 和 Azure 函數之間的本機集成在 Azure 門戶和 Visual Studio 2019 中可用。

* 在 Azure 函數門戶中,可以創建觸發器。 有關快速入門說明,請參閱[在 Azure 門戶中為 Cosmos DB 創建 Azure 函數觸發器](../azure-functions/functions-create-cosmos-db-triggered-function.md)。
* 在 Azure Cosmos DB 門戶中,可以將 Cosmos DB 的 Azure 函數觸發器添加到同一資源組中的現有 Azure 函數應用。
* 在 Visual Studio 2019 中,您可以使用[Azure 函數工具](../azure-functions/functions-develop-vs.md)建立觸發器:

    >[!VIDEO https://www.youtube.com/embed/iprndNsUeeg]

## <a name="why-choose-azure-functions-integration-for-serverless-computing"></a>為何選擇 Azure Functions 整合以用於無伺服器的計算？

Azure Functions 提供了建立可擴展工作單位的功能，或可視需要執行的簡單邏輯，而無需佈建或管理基礎架構。 通過使用 Azure 函數,您不必創建成熟的應用來回應 Azure Cosmos 資料庫中的更改,則可以為特定任務創建小型可重用函數。 此外，您也可以使用 Azure Cosmos DB 資料作為 Azure Function 的輸入或輸出，以回應如 HTTP 要求或定時觸發之類的事件。

無伺服器計算建議使用 Azure Cosmos DB 的資料庫，原因如下：

* **即時存取所有資料**：您可以精確存取儲存的每個值，因為 Azure Cosmos DB 依預設會[自動編製索引](index-policy.md)所有資料，並讓這些索引立即可用。 這表示您可以經常向資料庫查詢、更新和新增項目，並透過 Azure Functions 即時存取。

* **無結構描述**。 Azure Cosmos DB 是無結構描述的 - 因此它有獨特的能力可處理來自 Azure Function 的任何資料輸出。 這種「處理任何事」的方法使其可直接建立所有輸出至 Azure Cosmos DB 的各種函式。

* **可擴充的輸送量**。 輸送量可以在 Azure Cosmos DB 中即時地相應增加或相應減少。 如果您有數百或數千個功能查詢，並且寫入相同容器，則可以相應增加輸送量的 [RU/s](request-units.md) 以處理負載。 所有函式可以使用您分配的 RU/秒來並行工作，且保證您的資料[一致](consistency-levels.md)。

* **全域複製**。 您可以複寫[全域範圍內](distribute-data-globally.md)的 Azure Cosmos DB 資料以降低延遲，並異地尋找最靠近使用者所在位置的資料。 如同所有 Azure Cosmos DB 查詢，事件驅動觸發程序的資料是從最靠近使用者的 Azure Cosmos DB 讀取資料。

如果您想要整合 Azure Functions 以儲存資料，而且不需要深度編製索引，或如果您需要儲存附件與媒體檔案，則 [Azure Blob 儲存體觸發程序](../azure-functions/functions-bindings-storage-blob.md)可能是更佳的選擇。

Azure Functions 的優點： 

* **事件驅動**。 Azure Functions 是事件驅動的，而且可接聽來自 Azure Cosmos DB 的變更摘要。 這表示您不需要建立接聽邏輯，您只要留意正在接聽的變更即可。 

* **無限制**。 函式會並行執行，而且該服務會根據您的需要啟動。 設定參數。

* **適用於快速工作**。 當事件觸發時，服務會啟動函式的新執行個體，並在函式完成後立即關閉這些執行個體。 您只需要針對函式有執行的時間來付費。

如果您不確定 Flow、Logic Apps、Azure Functions 或 WebJobs 是否適合您的實作，請參閱[在 Flow、Logic Apps、Functions 和 WebJobs 之間做選擇](../azure-functions/functions-compare-logic-apps-ms-flow-webjobs.md)。

## <a name="next-steps"></a>後續步驟

現在，讓我們實際連接 Azure Cosmos DB 與 Azure Functions： 

* [在 Azure 門戶中為 Cosmos DB 建立 Azure 函數觸發器](../azure-functions/functions-create-cosmos-db-triggered-function.md)
* [使用 Azure Cosmos DB 輸入繫結建立 Azure Functions HTTP 觸發程序 (Create an Azure Functions HTTP trigger with an Azure Cosmos DB input binding)](../azure-functions/functions-bindings-cosmosdb.md?tabs=csharp)
* [Azure DB Cosmos 繫結和觸發程序](../azure-functions/functions-bindings-cosmosdb-v2.md)