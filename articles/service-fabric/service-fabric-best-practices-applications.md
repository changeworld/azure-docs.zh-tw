---
title: Azure 服務結構應用程式設計最佳實作
description: 使用 Azure 服務結構開發應用程式和服務的最佳做法和設計注意事項。
author: markfussell
ms.topic: conceptual
ms.date: 06/18/2019
ms.author: mfussell
ms.openlocfilehash: 56df6e28940eb15597a3d6bccca3f85e5f690f89
ms.sourcegitcommit: a53fe6e9e4a4c153e9ac1a93e9335f8cf762c604
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/09/2020
ms.locfileid: "80991649"
---
# <a name="azure-service-fabric-application-design-best-practices"></a>Azure 服務結構應用程式設計最佳實作

本文提供了在 Azure 服務結構上構建應用程式和服務的最佳做法指南。
 
## <a name="get-familiar-with-service-fabric"></a>熟悉服務結構
* 閱讀"[因此,您要瞭解服務結構?](service-fabric-content-roadmap.md)
* 閱讀有關[服務結構應用程式方案](service-fabric-application-scenarios.md)。
* 通過閱讀[Service Fabric 程式設計模型概述](service-fabric-choose-framework.md),瞭解程式設計模型選擇。



## <a name="application-design-guidance"></a>應用設計指南
熟悉服務結構應用程式的[一般體系結構](https://docs.microsoft.com/azure/architecture/reference-architectures/microservices/service-fabric)及其[設計注意事項](https://docs.microsoft.com/azure/architecture/reference-architectures/microservices/service-fabric#design-considerations)。

### <a name="choose-an-api-gateway"></a>選擇 API 閘道
使用 API 閘道服務,該服務可與後端服務進行通信,然後可以橫向擴展。最常用的 API 閘道服務包括:

- [Azure API 管理](https://docs.microsoft.com/azure/service-fabric/service-fabric-api-management-overview),[與服務結構整合](https://docs.microsoft.com/azure/service-fabric/service-fabric-tutorial-deploy-api-management).
- [Azure IoT 中心](https://docs.microsoft.com/azure/iot-hub/)或[Azure 事件中心](https://docs.microsoft.com/azure/event-hubs/),使用[ServiceFabricProcessor](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/eventhub/Microsoft.Azure.EventHubs.ServiceFabricProcessor)從事件中心分區讀取。
- [Tr_fik 反向代理](https://blogs.msdn.microsoft.com/azureservicefabric/2018/04/05/intelligent-routing-on-service-fabric-with-traefik/),使用[Azure 服務結構提供者](https://docs.traefik.io/v1.6/configuration/backends/servicefabric/)。
- [Azure 應用程式閘道](https://docs.microsoft.com/azure/application-gateway/)。

   > [!NOTE] 
   > Azure 應用程式閘道未直接與服務結構整合。 Azure API 管理通常是首選。
- 您自己的自定義[ASP.NET核心](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-services-communication-aspnetcore)Web 應用程式閘道。

### <a name="stateless-services"></a>無狀態服務
我們建議始終從使用[可靠服務和](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-services-introduction)在 Azure 資料庫、Azure Cosmos DB 或 Azure 儲存中儲存狀態來構建無狀態服務。 對於大多數開發人員來說,外部化狀態是更熟悉的方法。 此方法還使您能夠利用存儲中的查詢功能。  

### <a name="when-to-use-stateful-services"></a>何時使用有狀態服務
當您有低延遲方案並且需要將數據保持在接近計算時,請考慮有狀態的服務。 一些示例方案包括 IoT 數位孿生設備、遊戲狀態、工作階段狀態、從資料庫中緩存數據以及用於追蹤對其他服務的調用的長期運行工作流。

決定資料保留時間範圍:

- **快取的資料**。 當延遲到外部存儲出現問題時,請使用緩存。 使用有狀態服務作為您自己的資料快取,或考慮使用[開源 SoCreate 服務結構分散式快取](https://github.com/SoCreate/service-fabric-distributed-cache)。 在這種情況下,如果您丟失了緩存中的所有數據,則無需擔心。
- **有時限制的資料**。 在這種情況下,您需要將數據保持在接近以計算一段時間以進行延遲,但您可以在*災難*中丟失數據。 例如,在許多 IoT 解決方案中,數據需要接近計算,例如計算過去幾天的平均溫度時,但如果丟失此數據,記錄的特定數據點就不那麼重要了。 此外,在這種情況下,您通常不關心備份各個數據點。 您僅備份定期寫入外部存儲的計算平均值。  
- **長期資料**。 可靠的集合可以永久存儲數據。 但在這種情況下,您需要[為災難恢復做好準備](https://docs.microsoft.com/azure/service-fabric/service-fabric-disaster-recovery),包括為群集[配置定期備份策略](https://docs.microsoft.com/azure/service-fabric/service-fabric-backuprestoreservice-configure-periodic-backup)。 實際上,您可以配置如果群集在災難中遭到破壞、需要創建新群集以及如何部署新應用程式實例並從最新備份中恢復時會發生什麼情況。

節省成本並提高可用性:
- 您可以通過使用有狀態服務來降低成本,因為您不會從遠端存儲產生數據訪問和事務成本,並且不需要使用其他服務,如 Redis 的 Azure 緩存。
- 將有狀態服務主要用於存儲而不是計算是昂貴的,我們不建議這樣做。 將有狀態服務視為具有廉價本地存儲的計算。
- 通過刪除對其他服務的依賴項,可以提高服務可用性。 群集中使用 HA 管理狀態會將您與其他服務停機或延遲問題隔離開來。

## <a name="how-to-work-with-reliable-services"></a>如何使用可靠的服務
服務結構可靠服務使您能夠輕鬆建立無狀態和有狀態的服務。 有關詳細資訊,請參閱[可靠服務的介紹](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-services-introduction)。
- 始終在無狀態和狀態服務`RunAsync()`的方法與狀態`ChangeRole()`服務 方法中遵守[取消權杖](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-services-lifecycle#stateful-service-primary-swaps)。 如果沒有,服務結構不知道您的服務是否可以關閉。 例如,如果您不遵守取消令牌,則應用程式升級時間可能會更長。
-    及時打開和關閉[通信偵聽器](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-services-communication),並兌現取消令牌。
-    切勿將同步代碼與非同步代碼混合。 例如,不要在異步調用`.GetAwaiter().GetResult()`中使用。 在調用堆疊*中一直*使用非同步。

## <a name="how-to-work-with-reliable-actors"></a>如何與可靠演員合作
Service Fabric 可靠參與者使您能夠輕鬆建立有狀態的虛擬參與者。 有關詳細資訊,請參閱[可靠參與者的介紹](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-actors-introduction)。

- 認真考慮在參與者之間使用pub/sub消息來擴展應用程式。 提供此服務的工具包含開來源[SoCreate 服務結構 Pub/Sub](https://service-fabric-pub-sub.socreate.it/)和[Azure 服務總線](https://docs.microsoft.com/azure/service-bus/)。
- 讓執行元件狀態[, 讓您可能細粒度](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-actors-state-management#best-practices)。
- 管理[演員的生命週期](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-actors-state-management#best-practices)。 如果您不打算再次使用它們,請刪除參與者。 使用[易失狀態提供程式](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-actors-state-management#state-persistence-and-replication)時,刪除不需要的執行元件尤其重要,因為所有狀態都存儲在記憶體中。
- 由於其[基於回合的併發](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-actors-introduction#concurrency)性,演員最好用作獨立物件。 不要建立多執行元件、同步方法呼叫(每個最有可能成為單獨的網路呼叫)的圖形或創建迴圈執行元件請求。 這些將顯著影響性能和規模。
- 不要將同步代碼與異步代碼混合。 一致使用異步,以防止出現性能問題。
- 不要在演員中打長時間的電話。 由於基於回合的併發性,長時間運行的調用將阻止對同一執行元件的其他調用。
- 如果您正在使用[Service Fabric 遠端](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-services-communication-remoting)處理與其他服務通訊,`ServiceProxyFactory`並且正在建立, 則在[參與者服務](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-actors-using)等級建立工廠,*而不是*在參與者級別。


## <a name="application-diagnostics"></a>應用程式診斷
在服務呼叫中新增[應用程式紀錄記錄](https://docs.microsoft.com/azure/service-fabric/service-fabric-diagnostics-event-generation-app)。 它將幫助您診斷服務相互調用的方案。 例如,當 A 調用 B 調用 C 調用 D 時,呼叫可能在任何地方失敗。 如果沒有足夠的日誌記錄,則很難診斷失敗。 如果服務由於調用卷而記錄過多,請確保至少記錄錯誤和警告。

## <a name="iot-and-messaging-applications"></a>IoT 與訊息傳遞應用程式
當您從[Azure IoT 中心](https://docs.microsoft.com/azure/iot-hub/)或[Azure 事件中心](https://docs.microsoft.com/azure/event-hubs/)讀取訊息時,請使用[ServiceFabricProcessor](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Microsoft.Azure.EventHubs/ServiceFabricProcessor)。 ServiceFabricProcessor 與 Service Fabric 可靠服務集成,以維護從事件中心分區讀取的狀態,`IEventProcessor::ProcessEventsAsync()`並通過 該方法 將新消息推送到服務。


## <a name="design-guidance-on-azure"></a>Azure 的設計指南
* 有關在[Azure 上建構微服務](https://docs.microsoft.com/azure/architecture/microservices/)的設計指南,請造[訪 Azure 架構結構中心](https://docs.microsoft.com/azure/architecture/microservices/)。

* 有關在[遊戲服務中使用服務結構](https://docs.microsoft.com/gaming/azure/reference-architectures/multiplayer-synchronous-sf)的設計指南,請造[訪 Azure 開始玩遊戲](https://docs.microsoft.com/gaming/azure/)。
