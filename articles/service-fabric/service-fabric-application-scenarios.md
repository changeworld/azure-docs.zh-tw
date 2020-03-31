---
title: 應用程式方案和設計
description: Service Fabric 中雲端應用程式類別概觀。 討論使用具狀態和無狀態服務的應用程式設計。
ms.topic: conceptual
ms.date: 01/08/2020
ms.custom: sfrev
ms.openlocfilehash: 0aeb8ab2923915befdd11f96025687be3b3c4ff9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "76024732"
---
# <a name="service-fabric-application-scenarios"></a>Service Fabric 應用程式案例

Azure 服務結構提供了一個可靠且靈活的平臺，您可以在其中編寫和運行多種類型的商務應用程式和服務。 這些應用程式和微服務可以是無狀態的或有狀態的，並且它們跨虛擬機器實現資源平衡，以最大限度地提高效率。

Service Fabric 的獨特架構可讓您在應用程式中執行接近即時的資料分析、記憶體中計算、平行交易和事件處理。 您可以根據不斷變化的資源需求輕鬆將應用程式擴展到或擴展。

有關構建應用程式的設計指南，請閱讀[Azure 服務結構上的微服務體系結構](https://docs.microsoft.com/azure/architecture/reference-architectures/microservices/service-fabric)以及[使用 Service Fabric 進行應用程式設計的最佳做法](service-fabric-best-practices-applications.md)。

請考慮將 Service Fabric 平臺用於以下類型的應用程式：

* **資料收集、處理和 IoT**：Service Fabric 可處理大規模資料，並通過其有狀態的服務具有低延遲。 它可以説明處理數百萬台設備上的資料，其中設備和計算的資料是同地的。

    使用服務交換矩陣構建物聯網服務的客戶包括[霍尼韋爾](https://customers.microsoft.com/story/honeywell-builds-microservices-based-thermostats-on-azure)[、PCL建築](https://customers.microsoft.com/story/pcl-construction-professional-services-azure)、[克雷斯特龍](https://customers.microsoft.com/story/crestron-partner-professional-services-azure)、[寶馬](https://customers.microsoft.com/story/bmw-enables-driver-mobility-via-azure-service-fabric/)、[施耐德電氣](https://customers.microsoft.com/story/schneider-electric-powers-engergy-solutions-on-azure-service-fabric)和[網狀系統](https://customers.microsoft.com/story/mesh-systems-lights-up-the-market-with-iot-based-azure-solutions)。

* **遊戲和基於會話的互動式應用程式**：如果應用程式需要低延遲讀取和寫入（如線上遊戲或即時消息），則服務交換矩陣非常有用。 Service Fabric 使您能夠構建這些互動式、有狀態的應用程式，而無需創建單獨的存儲或緩存。 有關[在遊戲服務中使用服務交換矩陣](https://docs.microsoft.com/gaming/azure/reference-architectures/multiplayer-synchronous-sf)的設計指南，請訪問[Azure 遊戲解決方案](https://azure.microsoft.com/solutions/gaming/)。

    已經建立遊戲服務的客戶包括[下一個遊戲](https://customers.microsoft.com/story/next-games-media-telecommunications-azure)和[迪加莫爾](https://customers.microsoft.com/story/digamore-entertainment-scores-with-a-new-gaming-platform-based-on-azure-service-fabric/)。 已經建立了互動會話的客戶包括[霍尼韋爾與霍洛倫斯](https://customers.microsoft.com/story/honeywell-manufacturing-hololens)。

* **資料分析和工作流處理**：必須可靠地處理事件或資料流程的應用程式受益于 Service Fabric 中優化的讀取和寫入。 Service Fabric 還支援應用程式處理管道，其中結果必須可靠，並傳遞到下一個處理階段，而不會丟失任何損失。 這些管道包括事務和財務系統，其中資料一致性和計算保證至關重要。

    已建立業務工作流程服務的客戶包括[蔡司集團](https://customers.microsoft.com/story/zeiss-group-focuses-on-azure-service-fabric-for-key-integration-platform)[、Quorum 業務解決方案](https://customers.microsoft.com/en-us/story/quorum-business-solutions-expand-energy-managemant-solutions-using-azure-service-fabric)和[興業銀行](https://customers.microsoft.com/en-us/story/societe-generale-speeds-real-time-market-quotes-using-azure-service-fabric)。

* **對資料的計算**：Service Fabric 使您能夠構建執行密集型資料計算的有狀態應用程式。 Service Fabric 允許在應用程式中託管處理（計算）和資料。 

   通常，當應用程式需要訪問資料時，與外部資料緩存或存儲層關聯的網路延遲會限制計算時間。 有狀態的服務交換矩陣服務消除了這種延遲，從而實現了更優化的讀取和寫入。

   例如，考慮為客戶執行近乎即時建議選擇的應用程式，其往返時間要求小於 100 毫秒。 與必須從遠端存放獲取所需資料的標準實現模型相比，Service Fabric 服務的延遲和性能特性為使用者提供了回應式體驗。 系統回應更快，因為建議選擇的計算與資料和規則共存。

    已構建計算服務的客戶包括[Solidsoft](https://customers.microsoft.com/story/solidsoft-reply-platform-powers-e-verification-of-pharmaceuticals)回復[和資訊支援](https://customers.microsoft.com/story/service-fabric-customer-profile-info-support-and-fudura)。

* **高度可用的服務**：服務結構通過創建多個輔助服務副本提供快速容錯移轉。 如果節點、處理序或個別服務因為硬體或其他故障而停機，其中一個次要複本會升級為主要複本，以便將服務損失降到最低。

* **可調整的服務**：可以分割個別服務，以便在叢集中相應放大。 還可以動態創建和刪除各個服務。 您可以將服務從幾個節點上的幾個實例擴展到多個節點上的數千個實例，然後根據需要再次擴展服務。 您可以使用 Service Fabric 構建這些服務並管理其整個生命週期。

## <a name="application-design-case-studies"></a>應用程式設計個案研究

案例研究，說明如何使用 Service Fabric 設計應用程式，這些案例研究發佈在 Azure 網站[中的客戶情景](https://customers.microsoft.com/search?sq=%22Azure%20Service%20Fabric%22&ff=&p=2&so=story_publish_date%20desc)和[微服務](https://azure.microsoft.com/solutions/microservice-applications/)上。

## <a name="designing-applications-composed-of-stateless-and-stateful-microservices"></a>設計由無狀態和狀態微服務組成的應用程式

使用 Azure 雲服務輔助角色構建應用程式是無狀態服務的示例。 相較之下，具狀態的微服務會維護要求及其回應以外的授權狀態。 此功能通過提供由複製支援的事務保證的簡單 API 提供狀態的高可用性和一致性。

Service Fabric 中的有狀態服務為所有類型的應用程式（而不僅僅是資料庫和其他資料存儲）帶來高可用性。 這是自然的進展。 針對高可用性，應用程式已經從使用單純的關聯式資料庫進展到 NoSQL 資料庫的境界。 現在應用程式就能在其本身內管理它們的「熱門」狀態和資料，以便進一步提高效能，而不需犧牲可靠性、一致性或可用性。

構建由微服務組成的應用程式時，通常將無狀態 Web 應用程式（如ASP.NET和 Node.js）組合到無狀態和有狀態的業務中介層服務上。 應用和服務都通過服務結構部署命令部署在同一服務結構群集中。 這些服務中的每一個在規模、可靠性和資源使用方面都是獨立的。 這種獨立性提高了開發和生命週期管理的靈活性和靈活性。

具狀態的微服務簡化了應用程式設計，因此不需要傳統上為滿足純無狀態應用程式的可用性與延遲需求時所需的其他佇列與快取。 由於有狀態服務具有高可用性和低延遲，因此應用程式中需要管理的詳細資訊較少。

下圖說明瞭設計無狀態應用程式與有狀態應用程式之間的差異。 通過利用[可靠的服務和](service-fabric-reliable-services-introduction.md)[可靠的參與者](service-fabric-reliable-actors-introduction.md)程式設計模型，有狀態的服務降低了應用程式複雜性，同時實現了高輸送量和低延遲。

下面是一個使用無狀態服務的應用程式範例：![使用無狀態服務的應用程式][Image1]

下面是一個使用有狀態服務的應用程式範例：![使用有狀態服務的應用程式][Image2]

## <a name="next-steps"></a>後續步驟

* 使用服務交換矩陣[可靠服務和](service-fabric-reliable-services-quick-start.md)[可靠參與者](service-fabric-reliable-actors-get-started.md)程式設計模型開始構建無狀態和有狀態的服務。
* 有關在[Azure 上構建微服務](https://docs.microsoft.com/azure/architecture/microservices/)的指導，請訪問 Azure 體系結構中心。
* 轉到[Azure 服務結構應用程式和群集最佳實踐](service-fabric-best-practices-overview.md)，以進行應用程式設計指導。

* 另請參閱：
  * [了解微服務](service-fabric-overview-microservices.md)
  * [定義及管理服務狀態](service-fabric-concepts-state.md)
  * [服務的可用性](service-fabric-availability-services.md)
  * [調整服務](service-fabric-concepts-scalability.md)
  * [分區服務](service-fabric-concepts-partitioning.md)

[Image1]: media/service-fabric-application-scenarios/AppwithStatelessServices.png
[Image2]: media/service-fabric-application-scenarios/AppwithStatefulServices.png
