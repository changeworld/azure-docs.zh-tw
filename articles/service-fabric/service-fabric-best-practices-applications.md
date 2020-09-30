---
title: Azure Service Fabric 應用程式設計最佳做法
description: 使用 Azure Service Fabric 開發應用程式和服務的最佳作法和設計考慮。
ms.topic: conceptual
ms.date: 06/18/2019
ms.openlocfilehash: ddf846e9e3ac6add7cf3f584b702de5accfb22af
ms.sourcegitcommit: f5580dd1d1799de15646e195f0120b9f9255617b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/29/2020
ms.locfileid: "91538493"
---
# <a name="azure-service-fabric-application-design-best-practices"></a>Azure Service Fabric 應用程式設計最佳做法

本文提供在 Azure Service Fabric 建立應用程式和服務的最佳作法指引。
 
## <a name="get-familiar-with-service-fabric"></a>熟悉 Service Fabric
* 閱讀 [您想要瞭解 Service Fabric？](service-fabric-content-roadmap.md) 文章。
* 閱讀 [Service Fabric 應用程式案例](service-fabric-application-scenarios.md)的相關資訊。
* 閱讀 [Service Fabric 程式設計模型總覽](service-fabric-choose-framework.md)，瞭解程式設計模型的選擇。



## <a name="application-design-guidance"></a>應用程式設計指導方針
熟悉 Service Fabric 應用程式的 [一般架構](/azure/architecture/reference-architectures/microservices/service-fabric) 及其 [設計考慮](/azure/architecture/reference-architectures/microservices/service-fabric#design-considerations)。

### <a name="choose-an-api-gateway"></a>選擇 API 閘道
使用可與後端服務通訊的 API 閘道服務，然後相應放大。最常見的 API 閘道服務是：

- [AZURE API 管理](./service-fabric-api-management-overview.md)， [與 Service Fabric 整合](./service-fabric-tutorial-deploy-api-management.md)。
- [Azure IoT 中樞](../iot-hub/index.yml) 或 [Azure 事件中樞](../event-hubs/index.yml)，使用 [ServiceFabricProcessor](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/eventhub/Microsoft.Azure.EventHubs.ServiceFabricProcessor) 從事件中樞分割區讀取。
- 使用[Azure Service Fabric 提供者](https://docs.traefik.io/v1.6/configuration/backends/servicefabric/) [Træfik 反向 proxy](https://techcommunity.microsoft.com/t5/azure-service-fabric/bg-p/Service-Fabric)。
- [Azure 應用程式閘道](../application-gateway/index.yml)。

   > [!NOTE] 
   > Azure 應用程式閘道未直接與 Service Fabric 整合。 Azure API 管理通常是偏好的選擇。
- 您專屬的自訂 [ASP.NET Core](./service-fabric-reliable-services-communication-aspnetcore.md) web 應用程式閘道。

### <a name="stateless-services"></a>無狀態服務
建議您一開始先建立無狀態服務，方法是使用 [Reliable Services](./service-fabric-reliable-services-introduction.md) ，並在 Azure 資料庫、Azure Cosmos DB 或 Azure 儲存體中儲存狀態。 外部化狀態是對大部分開發人員來說比較熟悉的方法。 這種方法也可讓您利用存放區上的查詢功能。  

### <a name="when-to-use-stateful-services"></a>使用具狀態服務的時機
當您有低延遲的案例，且需要將資料保留在計算的附近時，請考慮具狀態服務。 某些範例案例包括 IoT 數位對應項裝置、遊戲狀態、會話狀態、快取資料庫中的資料，以及長時間執行的工作流程來追蹤其他服務的呼叫。

決定資料保留時間範圍：

- 快取的**資料**。 在延遲至外部存放區時使用快取是問題。 使用具狀態服務作為您自己的資料快取，或考慮使用 [開放原始碼 SoCreate Service Fabric 分散式](https://github.com/SoCreate/service-fabric-distributed-cache)快取。 在此案例中，如果您遺失快取中的所有資料，您就不需要擔心。
- **時間界限的資料**。 在此案例中，您必須將資料保持在接近計算一段時間的延遲時間，但您可以承受遺失損*毀的資料。* 例如，在許多 IoT 解決方案中，資料必須接近計算，例如，當計算過去幾天的平均溫度時，但如果遺失此資料，則記錄的特定資料點並不重要。 此外，在此案例中，您通常不會在意如何備份個別的資料點。 您只會備份定期寫入至外部儲存體的計算平均值。  
- **長期資料**。 可靠的集合可以永久儲存您的資料。 但在此情況下，您需要 [準備進行](./service-fabric-disaster-recovery.md)嚴重損壞修復，包括設定叢集的 [定期備份原則](./service-fabric-backuprestoreservice-configure-periodic-backup.md) 。 實際上，如果您的叢集在損毀時，您需要建立新的叢集，以及如何部署新的應用程式實例並從最新的備份中復原，則可以設定會發生什麼事。

節省成本並提升可用性：
- 您可以使用具狀態服務來降低成本，因為您不會產生來自遠端存放區的資料存取和交易成本，而且因為您不需要使用另一個服務（例如 Azure Cache for Redis）。
- 使用具狀態服務主要用於儲存，而不是用於計算，因此我們不建議使用。 將具狀態服務視為具有便宜本機儲存體的計算。
- 藉由移除其他服務的相依性，您可以改善服務可用性。 使用叢集中的 HA 來管理狀態，可隔離您與其他服務停機時間或延遲問題。

## <a name="how-to-work-with-reliable-services"></a>如何使用 Reliable Services
Service Fabric Reliable Services 可讓您輕鬆地建立無狀態和具狀態服務。 如需詳細資訊，請參閱 [Reliable Services 簡介](./service-fabric-reliable-services-introduction.md)。
- 一律[cancellation token](./service-fabric-reliable-services-lifecycle.md#stateful-service-primary-swaps) `RunAsync()` 針對無狀態和具狀態服務的方法，以及具狀態服務的方法，接受取消權杖 `ChangeRole()` 。 如果您沒有這麼做，Service Fabric 不知道是否可以關閉您的服務。 例如，如果您不接受取消權杖，可能會有較長的應用程式升級時間。
-    及時開啟和關閉 [通訊](./service-fabric-reliable-services-communication.md) 接聽程式，並接受取消權杖。
-    絕對不要將同步程式碼與非同步程式碼混在一起。 例如，請勿 `.GetAwaiter().GetResult()` 在您的非同步呼叫中使用。 透過呼叫堆疊，以非同步 *方式* 使用。

## <a name="how-to-work-with-reliable-actors"></a>如何使用 Reliable Actors
Service Fabric Reliable Actors 可讓您輕鬆地建立具狀態的虛擬執行者。 如需詳細資訊，請參閱 [Reliable Actors 簡介](./service-fabric-reliable-actors-introduction.md)。

- 認真考慮使用您的動作專案之間的 pub/sub 訊息來調整應用程式。 提供此服務的工具組括 [開放原始碼 SoCreate Service Fabric Pub/Sub](https://service-fabric-pub-sub.socreate.it/) 和 [Azure 服務匯流排](/azure/service-bus/)。
- 讓動作專案狀態盡可能 [精細](./service-fabric-reliable-actors-state-management.md#best-practices)。
- 管理 [執行者的生命週期](./service-fabric-reliable-actors-state-management.md#best-practices)。 如果您不打算再次使用動作專案，請將其刪除。 當您使用 [volatile 狀態提供者](./service-fabric-reliable-actors-state-management.md#state-persistence-and-replication)時，刪除不必要的動作專案特別重要，因為所有狀態都會儲存在記憶體中。
- 由於其以 [回合為基礎的並行](./service-fabric-reliable-actors-introduction.md#concurrency)存取，因此最適合使用動作專案作為獨立物件。 請勿建立多個動作專案的同步方法呼叫圖形， (每一個都可能會變成個別的網路呼叫) 或建立迴圈動作專案要求。 這些會大幅影響效能和規模。
- 請勿將同步程式碼與非同步程式碼混在一起。 以一致的方式使用非同步來防止效能問題。
- 請勿在動作專案中進行長時間執行的呼叫。 長時間執行的呼叫會封鎖對相同動作專案的其他呼叫，因為回合式並行。
- 如果您要使用 [Service Fabric 遠端處理](./service-fabric-reliable-services-communication-remoting.md) 來與其他服務通訊，而且您要建立 `ServiceProxyFactory` ，請在動作專案 [服務](./service-fabric-reliable-actors-using.md) 層級建立 factory，而 *不* 是在動作專案層級。


## <a name="application-diagnostics"></a>應用程式診斷
請徹底瞭解如何在服務呼叫中新增 [應用程式記錄](./service-fabric-diagnostics-event-generation-app.md) 。 它將協助您診斷服務彼此呼叫的案例。 例如，當呼叫 B 呼叫 C 呼叫 D 時，呼叫可能會在任何位置失敗。 如果您沒有足夠的記錄，則會難以診斷失敗。 如果服務因為呼叫磁片區而記錄太多，請務必至少記錄錯誤和警告。

## <a name="iot-and-messaging-applications"></a>IoT 和訊息應用程式
當您從 [Azure IoT 中樞](../iot-hub/index.yml) 或 [Azure 事件中樞](../event-hubs/index.yml)讀取訊息時，請使用  [ServiceFabricProcessor](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Microsoft.Azure.EventHubs/ServiceFabricProcessor)。 ServiceFabricProcessor 會與 Service Fabric Reliable Services 整合，以維護從事件中樞分割區讀取的狀態，並透過方法將新訊息推送到您的服務 `IEventProcessor::ProcessEventsAsync()` 。


## <a name="design-guidance-on-azure"></a>Azure 上的設計指導方針
* 請造訪 [azure 架構中心](/azure/architecture/microservices/) ，以取得在 [azure 上建立微服務](/azure/architecture/microservices/)的設計指引。

* 造訪 [開始的 Azure For 遊戲](/gaming/azure/) ，以取得在 [遊戲服務中使用 Service Fabric](/gaming/azure/reference-architectures/multiplayer-synchronous-sf)的設計指引。
