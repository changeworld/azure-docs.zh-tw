---
title: 嚴重損壞修復和異地散發 Azure Durable Functions
description: 了解 Durable Functions 中的災難復原和地理分散。
author: MS-Santi
ms.topic: conceptual
ms.date: 08/27/2020
ms.author: azfuncdf
ms.openlocfilehash: 01c400f51cce85ef39e9d39bcad1221253c6942d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "89071205"
---
# <a name="disaster-recovery-and-geo-distribution-in-azure-durable-functions"></a>Azure Durable Functions 中的災難復原和地理分散

Microsoft 致力於確保 Azure 服務皆能持續可用。 不過仍然可能會發生計畫外的服務中斷。 如果您的應用程式需要復原功能，Microsoft 建議您將應用程式設定為異地冗余。 此外，客戶應該要建立災害復原計畫來處理區域服務中斷。 嚴重損壞修復計畫的重要部分，就是在主要複本無法使用時，準備容錯移轉至您的應用程式和儲存體的次要複本。

在 Durable Functions 中，所有狀態預設都會保存在 Azure 儲存體中。 工作 [中樞](durable-functions-task-hubs.md) 是用於協調 [流程](durable-functions-types-features-overview.md#orchestrator-functions) 和 [實體](durable-functions-types-features-overview.md#entity-functions)Azure 儲存體資源的邏輯容器。 協調器、活動和實體函式只要屬於相同的工作中樞，就可以彼此互動。 本檔會在描述將這些 Azure 儲存體資源保持高可用性的案例時，參考工作中樞。

您可以使用用戶端函式觸發協調流程和實體，這些 [用戶端](durable-functions-types-features-overview.md#client-functions) 函式本身是透過 HTTP 或其他其中一個支援的 Azure Functions 觸發程式類型所觸發。 也可以使用 [內建的 HTTP api](durable-functions-http-features.md#built-in-http-apis)來觸發它們。 為了簡單起見，本文將著重于涉及 Azure 儲存體和 HTTP 型函式觸發程式的案例，以及在嚴重損壞修復活動期間增加可用性及縮短停機時間的選項。 其他觸發程式類型，例如服務匯流排或 Cosmos DB 觸發程式，則不會明確涵蓋。

下列案例是根據 Active-Passive 設定，因為它們是由使用 Azure 儲存體來引導。 此模式包含將備份 (被動) 函式應用程式部署至不同的區域。 流量管理員會監視主要 (作用中的) 函式應用程式以取得 HTTP 可用性。 它會在主要函式應用程式失敗時，容錯移轉至備份函式應用程式。 如需詳細資訊，請參閱 [Azure 流量管理員](https://azure.microsoft.com/services/traffic-manager/)的 [優先順序 Traffic-Routing 方法。](../../traffic-manager/traffic-manager-routing-methods.md#priority-traffic-routing-method)

> [!NOTE]
> - 提議的主動-被動組態可確保用戶端永遠能透過 HTTP 觸發新的協調流程。 不過，因為有兩個函式應用程式在儲存體中共用相同的工作中樞，所以會在兩者之間散發部分背景儲存體交易。 因此，此設定會產生額外的額外輸出成本給次要函數應用程式。
> - 基礎儲存體帳戶和工作中樞均建立於主要區域中，並由兩個函式應用程式所共用。
> - 在透過 HTTP 啟用的情況下，所有已部署的函式應用程式都必須共用相同的函數存取金鑰。 Functions 執行階段會公開[管理 API](https://github.com/Azure/azure-functions-host/wiki/Key-management-API) ，讓取用者能以程式設計方式新增、刪除和更新功能鍵。 也可以使用 [Azure Resource Manager api](https://www.markheath.net/post/managing-azure-functions-keys-2)來管理金鑰。

## <a name="scenario-1---load-balanced-compute-with-shared-storage"></a>案例 1 - 具有共用儲存體的負載平衡計算

如果 Azure 中的計算基礎結構失敗，函式應用程式可能會無法使用。 為了將這類停機的可能性降到最低，此案例會使用部署到不同區域的兩個函式應用程式。
流量管理員已設定為要偵測主要函式應用程式中的問題，並自動將流量重新導向至次要區域中的函式應用程式。 此函式應用程式會共用相同的 Azure 儲存體帳戶和工作中樞。 因此，函式應用程式的狀態並未遺失，而且工作可以正常繼續。 一旦健康情況還原到主要區域，Azure 流量管理員就會自動啟動對該函式應用程式的路由要求。

![顯示案例 1 的圖表。](./media/durable-functions-disaster-recovery-geo-distribution/durable-functions-geo-scenario01.png)

使用此部署案例時有幾個好處：

- 如果計算基礎結構失敗，可在容錯移轉區域中繼續工作，而不會遺失資料。
- 流量管理員會自動自動容錯移轉至狀況良好的函式應用程式。
- 流量管理員會在中斷情況解決之後，自動重新建立對主要函式應用程式的流量。

不過，使用此案例時，請考慮：

- 如果函數應用程式是使用專用的 App Service 方案進行部署，則在容錯移轉資料中心複寫計算基礎結構會增加成本。
- 此案例涵蓋計算基礎結構的中斷，但儲存體帳戶仍然會是函式應用程式的單一失敗點。 如果發生儲存體中斷，應用程式會造成停機時間。
- 如果已容錯移轉函式應用程式，則會增加延遲時間，因為它會跨區域存取其儲存體帳戶。
- 從不同的所在區域存取儲存體服務，會因為網路傳出流量而造成較高的成本。
- 此案例取決於流量管理員。 考量到[流量管理員的運作方式](../../traffic-manager/traffic-manager-how-it-works.md)，在取用 Durable Function 的用戶端應用程式需要從流量管理員再次查詢函式應用程式位址之前，可能會經過一些時間。

> [!NOTE]
> 從 Durable Functions 擴充功能的 **v 2.3.0** 開始，可以使用相同的儲存體帳戶和工作中樞設定，安全地同時執行兩個函式應用程式。 第一個啟動的應用程式將會取得應用層級的 blob 租用，以防止其他應用程式竊取來自工作中樞佇列的訊息。 如果第一個應用程式停止執行，其租用將會過期，並可由第二個應用程式取得，然後將繼續處理工作中樞訊息。
> 
> 在2.3.0 之前，設定為使用相同儲存體帳戶的函式應用程式將會同時處理訊息和更新儲存體成品，導致整體延遲和輸出成本更高。 如果主要和複本應用程式部署了不同的程式碼，即使是暫時的，協調流程也可能無法正確執行，因為協調器在兩個應用程式之間的功能不一致。 因此，建議所有需要異地散發以進行嚴重損壞修復的應用程式，使用2.3.0 或更高的持久性延伸模組。

## <a name="scenario-2---load-balanced-compute-with-regional-storage"></a>案例 2 - 具有區域性儲存體的負載平衡計算

先前的案例只涵蓋計算基礎結構中的失敗案例。 如果儲存體服務失敗，它會導致函式應用程式中斷。
為了確保 Durable Functions 能夠連續作業，此案例會使用已部署函式應用程式的每個區域的本機儲存體帳戶。

![顯示案例 2 的圖表。](./media/durable-functions-disaster-recovery-geo-distribution/durable-functions-geo-scenario02.png)

這種方法可改善前一個案例：

- 如果函式應用程式失敗，則流量管理員會負責容錯移轉到次要區域。 不過，因為函式應用程式依賴自己的儲存體帳戶，所以 Durable Functions 會繼續運作。
- 在容錯移轉期間，容錯移轉區域沒有額外的延遲，因為函式應用程式和儲存體帳戶是共置的。
- 儲存層失敗會導致長期函式失敗，進而觸發重新導向至容錯移轉區域的功能。 同樣地，因為函式應用程式和儲存體依區域隔離，所以 Durable Functions 會繼續運作。

此案例的重要考量：

- 如果函數應用程式是使用專用的 App Service 方案進行部署，則在容錯移轉資料中心複寫計算基礎結構會增加成本。
- 目前的狀態不會進行容錯移轉，這表示現有的協調流程和實體在主要區域復原之前，將會有效地暫停且無法使用。

總而言之，第一和第二個案例之間的取捨是保留延遲，並將輸出成本降至最低，但現有的協調流程和實體在停機期間將無法使用。 這些取捨是否可接受，取決於應用程式的需求。

## <a name="scenario-3---load-balanced-compute-with-grs-shared-storage"></a>案例 3 - 具有 GRS 共用儲存體的負載平衡計算

此案例是修改自第一個案例，並實作共用儲存體帳戶。 主要差異在於儲存體帳戶是在啟用異地複寫的情況下建立的。
在功能上，此案例提供與案例 1 相同的優點，但它還有額外的資料復原優點：

- 異地備援儲存體 (GRS) 和讀取權限異地備援儲存體 (RA-GRS) 可為儲存體帳戶提供最大的可用性。
- 如果儲存體服務發生區域性中斷，您可以 [手動起始容錯移轉至次要複本](../../storage/common/storage-initiate-account-failover.md)。 在區域因嚴重災害而遺失的極端情況下，Microsoft 可能會起始區域容錯移轉。 在此情況下，您不需要採取任何動作。
- 當容錯移轉發生時，長期函式的狀態將會保留到最後一次複寫儲存體帳戶，這通常會在每隔幾分鐘發生一次。

如同其他案例，此案例有一些重要考量：

- 容錯移轉至複本可能需要一些時間。 在容錯移轉完成且已更新 Azure 儲存體 DNS 記錄之前，函式應用程式將會發生中斷。
- 使用異地複寫儲存體帳戶的成本於是增加。
- GRS 複寫會以非同步方式複製您的資料。 有些最新交易可能會因為複寫程序延遲而遺失。

![顯示案例 3 的圖表。](./media/durable-functions-disaster-recovery-geo-distribution/durable-functions-geo-scenario03.png)

> [!NOTE]
> 如案例1所述，強烈建議使用此策略部署的函式應用程式使用 **2.3.0** 或更高版本的 Durable Functions 擴充功能。

如需詳細資訊，請參閱 Azure 儲存體嚴重損壞 [修復和儲存體帳戶容錯移轉](../../storage/common/storage-disaster-recovery-guidance.md) 檔。

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [深入瞭解如何在 Azure 儲存體中設計高可用性應用程式](../../storage/common/geo-redundant-design.md)
