---
title: Azure Service Fabric 應用程式設計最佳做法
description: 使用 Azure Service Fabric 開發應用程式和服務的最佳作法和設計考慮。
author: markfussell
ms.topic: conceptual
ms.date: 06/18/2019
ms.author: mfussell
ms.openlocfilehash: 56df6e28940eb15597a3d6bccca3f85e5f690f89
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "80991649"
---
# <a name="azure-service-fabric-application-design-best-practices"></a>Azure Service Fabric 應用程式設計最佳做法

本文提供在 Azure Service Fabric 上建立應用程式和服務的最佳作法指引。
 
## <a name="get-familiar-with-service-fabric"></a>熟悉 Service Fabric
* 閱讀，[讓您瞭解 Service Fabric？](service-fabric-content-roadmap.md)一文。
* 閱讀[Service Fabric 應用程式案例](service-fabric-application-scenarios.md)的相關資訊。
* 閱讀[Service Fabric 程式設計模型總覽](service-fabric-choose-framework.md)，瞭解程式設計模型的選擇。



## <a name="application-design-guidance"></a>應用程式設計指引
熟悉 Service Fabric 應用程式的[一般架構](https://docs.microsoft.com/azure/architecture/reference-architectures/microservices/service-fabric)及其[設計考慮](https://docs.microsoft.com/azure/architecture/reference-architectures/microservices/service-fabric#design-considerations)。

### <a name="choose-an-api-gateway"></a>選擇 API 閘道
使用 API 閘道服務來與後端服務通訊，然後再進行相應放大。最常見的 API 閘道服務是：

- [與 Service Fabric 整合](https://docs.microsoft.com/azure/service-fabric/service-fabric-tutorial-deploy-api-management)的[Azure API 管理](https://docs.microsoft.com/azure/service-fabric/service-fabric-api-management-overview)。
- [Azure IoT 中樞](https://docs.microsoft.com/azure/iot-hub/)或[Azure 事件中樞](https://docs.microsoft.com/azure/event-hubs/)，使用[ServiceFabricProcessor](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/eventhub/Microsoft.Azure.EventHubs.ServiceFabricProcessor)從事件中樞分割區讀取。
- 使用[Azure Service Fabric 提供者](https://docs.traefik.io/v1.6/configuration/backends/servicefabric/) [Træfik 反向 proxy](https://blogs.msdn.microsoft.com/azureservicefabric/2018/04/05/intelligent-routing-on-service-fabric-with-traefik/)。
- [Azure 應用程式閘道](https://docs.microsoft.com/azure/application-gateway/)。

   > [!NOTE] 
   > Azure 應用程式閘道未與 Service Fabric 直接整合。 Azure API 管理通常是慣用的選擇。
- 您自己的自訂建立[ASP.NET Core](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-services-communication-aspnetcore) web 應用程式閘道。

### <a name="stateless-services"></a>無狀態服務
我們建議您一律從使用[Reliable Services](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-services-introduction)建立無狀態服務，並將狀態儲存在 Azure 資料庫、Azure Cosmos DB 或 Azure 儲存體開始。 對於大部分的開發人員而言，外部化狀態是比較熟悉的方法。 此方法也可讓您利用存放區上的查詢功能。  

### <a name="when-to-use-stateful-services"></a>使用具狀態服務的時機
當您有低延遲的案例，而且需要將資料保持在接近計算時，請考慮具狀態服務。 某些範例案例包括 IoT 數位對應項裝置、遊戲狀態、會話狀態、從資料庫快取資料，以及長時間執行的工作流程來追蹤對其他服務的呼叫。

決定資料保留時間範圍：

- 快取的**資料**。 當外部存放區的延遲發生問題時，請使用快取。 使用具狀態服務作為您自己的資料快取，或考慮使用[開放原始碼 SoCreate Service Fabric 分散式](https://github.com/SoCreate/service-fabric-distributed-cache)快取。 在此案例中，如果您遺失快取中的所有資料，就不需要擔心。
- **時間限制的資料**。 在此案例中，您需要讓資料保持接近計算一段時間以等待延遲，但是您可以承受遺失損*毀的資料。* 例如，在許多 IoT 解決方案中，資料必須接近計算，例如計算過去幾天的平均溫度時，但如果遺失這項資料，則記錄的特定資料點並不重要。 此外，在此案例中，您通常不在意備份個別的資料點。 您只會備份定期寫入外部儲存體的計算平均值值。  
- **長期資料**。 可靠的集合可以永久儲存您的資料。 但是在這種情況下，您必須為嚴重損壞[修復做準備](https://docs.microsoft.com/azure/service-fabric/service-fabric-disaster-recovery)，包括為您的叢集設定[定期備份原則](https://docs.microsoft.com/azure/service-fabric/service-fabric-backuprestoreservice-configure-periodic-backup)。 實際上，您會設定當您的叢集損毀時，會發生什麼情況，您需要建立新的叢集，以及如何部署新的應用程式實例，並從最新的備份復原。

節省成本並提升可用性：
- 您可以使用具狀態服務來降低成本，因為您不會產生來自遠端存放區的資料存取和交易成本，而且因為您不需要使用另一項服務，例如 Azure Cache for Redis。
- 使用主要用於儲存體的具狀態服務，而不是用於計算，但我們不建議您這麼做。 將具狀態服務視為具有便宜的本機儲存體的計算。
- 藉由移除其他服務的相依性，您可以改善服務可用性。 在叢集中管理 HA 的狀態，可隔離您與其他服務停機或延遲問題。

## <a name="how-to-work-with-reliable-services"></a>如何使用 Reliable Services
Service Fabric Reliable Services 可讓您輕鬆地建立無狀態和具狀態服務。 如需詳細資訊，請參閱[Reliable Services 簡介](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-services-introduction)。
- 請一律接受無狀態和具狀態服務之方法中的[取消權杖](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-services-lifecycle#stateful-service-primary-swaps) `RunAsync()` ，以及具 `ChangeRole()` 狀態服務的方法。 如果您沒有這麼做，Service Fabric 不知道您的服務是否可以關閉。 例如，如果您不接受解除標記，可能會有更長的應用程式升級時間。
-    及時開啟和關閉[通訊](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-services-communication)接聽程式，並接受解除標記。
-    絕對不要將同步處理常式代碼與非同步程式碼混合。 例如，請勿 `.GetAwaiter().GetResult()` 在您的 async 呼叫中使用。 透過呼叫堆疊以非同步*方式*使用。

## <a name="how-to-work-with-reliable-actors"></a>如何使用 Reliable Actors
Service Fabric Reliable Actors 可讓您輕鬆地建立具狀態的虛擬執行者。 如需詳細資訊，請參閱[Reliable Actors 簡介](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-actors-introduction)。

- 請認真考慮在您的動作專案之間使用 pub/sub 訊息來調整應用程式。 提供此服務的工具組括[開放原始碼 SoCreate Service Fabric Pub/Sub](https://service-fabric-pub-sub.socreate.it/)和[Azure 服務匯流排](https://docs.microsoft.com/azure/service-bus/)。
- 盡可能將動作專案狀態設為更[精細](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-actors-state-management#best-practices)。
- 管理[執行者的生命週期](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-actors-state-management#best-practices)。 如果您不打算再次使用動作專案，請將其刪除。 當您使用[volatile 狀態提供者](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-actors-state-management#state-persistence-and-replication)時，刪除不必要的執行者特別重要，因為所有狀態都會儲存在記憶體中。
- 由於動作專案是以[回合為基礎](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-actors-introduction#concurrency)，因此最適合當做獨立物件使用。 請勿建立多動作專案、同步方法呼叫的圖形（每個都有可能會變成個別的網路呼叫），或建立迴圈動作專案要求。 這些會大幅影響效能和規模。
- 請勿將同步處理常式代碼與非同步程式碼混合。 一致地使用 async 來避免發生效能問題。
- 不要在動作專案中進行長時間執行的呼叫。 長時間執行的呼叫將會封鎖對相同動作專案的其他呼叫，因為回合型並行。
- 如果您使用[Service Fabric 遠端處理](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-services-communication-remoting)來與其他服務通訊，而且您要建立 `ServiceProxyFactory` ，請在動作專案[-服務](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-actors-using)層級建立 factory，而*不*是在動作專案層級。


## <a name="application-diagnostics"></a>應用程式診斷
深入瞭解如何在服務呼叫中新增[應用程式記錄](https://docs.microsoft.com/azure/service-fabric/service-fabric-diagnostics-event-generation-app)。 這可協助您診斷服務彼此呼叫的案例。 例如，當呼叫 B 呼叫 C 呼叫 D 時，呼叫可能會在任何地方失敗。 如果您沒有足夠的記錄，則很難診斷失敗。 如果服務因為呼叫磁片區而記錄太多，請務必至少記錄錯誤和警告。

## <a name="iot-and-messaging-applications"></a>IoT 和訊息應用程式
當您從[Azure IoT 中樞](https://docs.microsoft.com/azure/iot-hub/)或[Azure 事件中樞](https://docs.microsoft.com/azure/event-hubs/)讀取訊息時，請使用[ServiceFabricProcessor](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Microsoft.Azure.EventHubs/ServiceFabricProcessor)。 ServiceFabricProcessor 會與 Service Fabric Reliable Services 整合，以維護從事件中樞分割區讀取的狀態，並透過方法將新訊息推送至您的服務 `IEventProcessor::ProcessEventsAsync()` 。


## <a name="design-guidance-on-azure"></a>Azure 上的設計指導方針
* 如需在[azure 上建立微服務](https://docs.microsoft.com/azure/architecture/microservices/)的設計指引，請造訪[azure 架構中心](https://docs.microsoft.com/azure/architecture/microservices/)。

* 如需在[遊戲服務中使用 Service Fabric](https://docs.microsoft.com/gaming/azure/reference-architectures/multiplayer-synchronous-sf)的設計指引，請流覽[開始使用 Azure 以取得遊戲](https://docs.microsoft.com/gaming/azure/)。
