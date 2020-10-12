---
title: 應用程式案例和設計
description: Service Fabric 中雲端應用程式類別概觀。 討論使用具狀態和無狀態服務的應用程式設計。
ms.topic: conceptual
ms.date: 01/08/2020
ms.custom: sfrev
ms.openlocfilehash: aaf8204158f720d8c69c98af758071cd91f6eff0
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "86248164"
---
# <a name="service-fabric-application-scenarios"></a>Service Fabric 應用程式案例

Azure Service Fabric 提供可靠且彈性的平臺，可讓您撰寫及執行許多類型的商務應用程式和服務。 這些應用程式和微服務可以是無狀態或具狀態的，而且它們會在虛擬機器之間進行資源平衡，以達到最高效率。

Service Fabric 的獨特架構可讓您在應用程式中執行接近即時的資料分析、記憶體中計算、平行交易和事件處理。 您可以根據您的變更資源需求，輕鬆地將應用程式相應縮小或放大。

如需建立應用程式的設計指引，請參閱 [Azure 上的微服務架構 Service Fabric](/azure/architecture/reference-architectures/microservices/service-fabric) 以及 [使用 Service Fabric 的應用程式設計最佳作法](service-fabric-best-practices-applications.md)。

針對下列類型的應用程式，請考慮使用 Service Fabric 平臺：

* **資料收集、處理和 IoT**： Service Fabric 會處理大規模的規模，並透過其具狀態服務降低延遲。 它有助於處理數百萬個裝置上的資料，而裝置和計算的資料會共置。

    使用 Service Fabric 建立 IoT 服務的客戶包括 [Honeywell](https://customers.microsoft.com/story/honeywell-builds-microservices-based-thermostats-on-azure)、 [PCL 建築](https://customers.microsoft.com/story/pcl-construction-professional-services-azure)、 [Crestron](https://customers.microsoft.com/story/crestron-partner-professional-services-azure)、  [寶馬](https://customers.microsoft.com/story/bmw-enables-driver-mobility-via-azure-service-fabric/)、 [Schneider 電動](https://customers.microsoft.com/story/schneider-electric-powers-engergy-solutions-on-azure-service-fabric)和 [網狀系統](https://customers.microsoft.com/story/mesh-systems-lights-up-the-market-with-iot-based-azure-solutions)。

* **遊戲和會話架構的互動式應用程式**：如果您的應用程式需要低延遲的讀取和寫入（例如線上遊戲或立即訊息），Service Fabric 會很有用。 Service Fabric 可讓您建立這些互動式、具狀態的應用程式，而不需要建立個別的存放區或快取。 請造訪 [Azure 遊戲解決方案](https://azure.microsoft.com/solutions/gaming/) ，以取得在 [遊戲服務中使用 Service Fabric](/gaming/azure/reference-architectures/multiplayer-synchronous-sf)的設計指引。

    建立遊戲服務的客戶包括 [下一場遊戲](https://customers.microsoft.com/story/next-games-media-telecommunications-azure) 和 [Digamore](https://customers.microsoft.com/story/digamore-entertainment-scores-with-a-new-gaming-platform-based-on-azure-service-fabric/)。 建立互動式會話的客戶包括 [Honeywell 與 Hololens](https://customers.microsoft.com/story/honeywell-manufacturing-hololens)。

* **資料分析和工作流程處理**：必須可靠地處理事件或資料流程的應用程式，會從 Service Fabric 中優化的讀取和寫入獲益。 Service Fabric 也支援應用程式處理管線，其中的結果必須可靠並傳遞至下一個處理階段，而不會遺失。 這些管線包括交易式和財務系統，其中的資料一致性和計算保證是不可或缺的。

    已建立商務工作流程服務的客戶包含 [Cichy 群組](https://customers.microsoft.com/story/zeiss-group-focuses-on-azure-service-fabric-for-key-integration-platform)、 [仲裁商務解決方案](https://customers.microsoft.com/en-us/story/quorum-business-solutions-expand-energy-managemant-solutions-using-azure-service-fabric)和 [Société一般](https://customers.microsoft.com/en-us/story/societe-generale-speeds-real-time-market-quotes-using-azure-service-fabric)。

* **資料的計算**： Service Fabric 可讓您建立執行大量資料計算的具狀態應用程式。 Service Fabric 可讓您在應用程式中處理 (計算) 和資料的共置。 

   一般來說，當您的應用程式需要存取資料時，與外部資料快取或儲存層相關聯的網路延遲會限制計算時間。 可設定狀態的 Service Fabric 服務會消除延遲，並啟用更優化的讀取和寫入。

   例如，假設有一個應用程式會針對客戶執行近乎即時的建議選項，且來回時間需求小於100毫秒。 Service Fabric 服務的延遲和效能特性可為使用者提供回應式體驗，相較于必須從遠端儲存體提取必要資料的標準執行模型。 系統會更具回應性，因為建議選項的計算會與資料和規則共置。

    已建立計算服務的客戶包含 [Solidsoft Reply](https://customers.microsoft.com/story/solidsoft-reply-platform-powers-e-verification-of-pharmaceuticals) 和 [Infosupport](https://customers.microsoft.com/story/service-fabric-customer-profile-info-support-and-fudura)。

* **高可用性服務**： Service Fabric 藉由建立多個次要服務複本來提供快速的容錯移轉。 如果節點、處理序或個別服務因為硬體或其他故障而停機，其中一個次要複本會升級為主要複本，以便將服務損失降到最低。

* **可調整的服務**：可以分割個別服務，以便在叢集中相應放大。 您也可以即時建立和移除個別服務。 您可以從數個節點上的幾個實例向外延展服務至多個節點上的數千個實例，然後視需要再次調整它們。 您可以使用 Service Fabric 來建立這些服務，並管理其完整生命週期。

## <a name="application-design-case-studies"></a>應用程式設計個案研究

示範如何使用 Service Fabric 來設計應用程式的個案研究，會發佈在 [客戶案例](https://customers.microsoft.com/search?sq=%22Azure%20Service%20Fabric%22&ff=&p=2&so=story_publish_date%20desc) 和 [Azure 網站的微服務中](https://azure.microsoft.com/solutions/microservice-applications/) 。

## <a name="designing-applications-composed-of-stateless-and-stateful-microservices"></a>設計包含無狀態和具狀態微服務的應用程式

使用 Azure 雲端服務背景工作角色建立應用程式是無狀態服務的範例。 相較之下，具狀態的微服務會維護要求及其回應以外的授權狀態。 這項功能可透過簡單的 Api 提供高可用性和狀態的一致性，以提供複寫所支援的交易式保證。

Service Fabric 中的具狀態服務會為所有類型的應用程式帶來高可用性，而不只是資料庫和其他資料存放區。 這是自然的進展。 針對高可用性，應用程式已經從使用單純的關聯式資料庫進展到 NoSQL 資料庫的境界。 現在應用程式就能在其本身內管理它們的「熱門」狀態和資料，以便進一步提高效能，而不需犧牲可靠性、一致性或可用性。

當您建立包含微服務的應用程式時，您通常會有無狀態 web 應用程式的組合 (例如 ASP.NET 和 Node.js) 呼叫無狀態和具狀態的商務中介層服務。 應用程式和服務都是透過 Service Fabric 部署命令部署到相同的 Service Fabric 叢集中。 這些服務每一項都與調整、可靠性和資源使用量無關。 這種獨立性可改善開發和生命週期管理的靈活性和彈性。

具狀態的微服務簡化了應用程式設計，因此不需要傳統上為滿足純無狀態應用程式的可用性與延遲需求時所需的其他佇列與快取。 因為具狀態服務具有高可用性和低延遲，所以在您的應用程式中管理的詳細資料較少。

下圖說明設計無狀態的應用程式，以及可設定狀態的應用程式之間的差異。 藉由利用 [Reliable Services](service-fabric-reliable-services-introduction.md) 和 [Reliable Actors](service-fabric-reliable-actors-introduction.md) 程式設計模型，具狀態服務可以降低應用程式的複雜度，同時達到高輸送量和低延遲。

以下是使用無狀態服務的範例應用程式： ![ 使用無狀態服務的應用程式][Image1]

以下是使用具狀態服務的範例應用程式： ![ 使用具狀態服務的應用程式][Image2]

## <a name="next-steps"></a>接下來的步驟

* 開始使用 Service Fabric [Reliable Services](service-fabric-reliable-services-quick-start.md) 和 [Reliable Actors](service-fabric-reliable-actors-get-started.md) 程式設計模型建立無狀態和具狀態服務。
* 請造訪 Azure 架構中心，以取得在 [Azure 上建立微服務](/azure/architecture/microservices/)的指引。
* 移至 [Azure Service Fabric 應用程式設計指南的應用程式與叢集最佳做法](service-fabric-best-practices-overview.md) 。

* 另請參閱：
  * [了解微服務](service-fabric-overview-microservices.md)
  * [定義及管理服務狀態](service-fabric-concepts-state.md)
  * [服務的可用性](service-fabric-availability-services.md)
  * [調整服務](service-fabric-concepts-scalability.md)
  * [資料分割服務](service-fabric-concepts-partitioning.md)

[Image1]: media/service-fabric-application-scenarios/AppwithStatelessServices.png
[Image2]: media/service-fabric-application-scenarios/AppwithStatefulServices.png
