---
title: Azure Service Fabric Mesh 的概觀
description: 了解 Azure Service Fabric Mesh。 透過 Service Fabric Mesh，您將可安心部署及調整應用程式，而無須考量應用程式的基礎結構需求。
author: dkkapur
ms.author: dekapur
ms.date: 10/1/2018
ms.topic: overview
ms.openlocfilehash: e64cc158a18e604cbd022be5c91350c95258272c
ms.sourcegitcommit: dabd9eb9925308d3c2404c3957e5c921408089da
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/11/2020
ms.locfileid: "86257798"
---
# <a name="what-is-service-fabric-mesh"></a>什麼是 Service Fabric Mesh？

這段影片提供 Service Fabric Mesh 的快速概觀。
> [!VIDEO https://www.youtube.com/embed/7qWeVGzAid0]

Azure Service Fabric Mesh 是一個受到完整管理的服務，讓開發人員能夠部署微服務應用程式，而不需管理虛擬機器、儲存體或網路功能。 裝載於 Service Fabric Mesh 的 應用程式可適當地執行及擴充，您無須擔心其所需的基礎結構。  Service Fabric Mesh 包含由數千個機器組成的叢集。  所有叢集作業都不會對開發人員顯示。 上傳程式碼，並指定您所需的資源、可用性需求和資源限制即可。  Service Fabric Mesh 會自動配置基礎結構並處理基礎結構失敗，以確保應用程式具備高可用性。 您只需留意應用程式的健康情況和回應性即可，而無須考量基礎結構。  

[!INCLUDE [preview note](./includes/include-preview-note.md)]

本文提供 Service Fabric Mesh 主要優點的概觀。

## <a name="great-developer-experience"></a>更理想的開發人員體驗

Service Fabric Mesh 支援任何可在容器中執行的程式設計語言或架構。 Visual Studio 2019 和 Visual Studio Code 工具支援可針對 .NET 和 .NET Core 應用程式提供完備的編輯和偵錯體驗。 

透過 Service Fabric Mesh，您可以：

- 將現有的應用程式「隨即轉移」至容器中，以大規模現代化並執行您目前的應用程式。
- 在 Azure 中大規模建置並部署新的微服務應用程式。  與其他 Azure 服務或在容器中執行的現有應用程式整合。 每個微服務都屬於與網路隔離的安全應用程式。 微服務已有專為 CPU 核心、記憶體和磁碟空間等項目定義的資源治理原則。
- 整合並擴充現有的應用程式，而不變更這些應用程式。 使用您自己的虛擬網路將現有應用程式連線至新的應用程式。  
- 藉由移轉至 Service Fabric Mesh，將現有的雲端服務應用程式現代化。  

## <a name="simple-operational-lifecycle"></a>簡單的作業生命週期

輕鬆地在生產環境中管理執行中的應用程式、監視應用程式和進行偵錯。 此管理包含應用程式升級和版本控制。 這些應用程式可包含單一或多個隔離於其本身網路內的微服務。 應用程式可透過快速的部署、放置和容錯移轉有效率地執行。

透過 Service Fabric Mesh，您可以：

- 部署及管理應用程式，而無須明確佈建和管理基礎結構。  Service Fabric Mesh 可為您佈建、升級、修補和維護基礎結構。
- 使用整合式工具輕鬆地封裝及部署應用程式，以設定持續整合。
- 運用 Azure Resource Manager 資源的所有功能。 這些功能的範例包括稽核線索和[角色型存取控制](../role-based-access-control/overview.md))。 您部署到 Azure 中 Service Fabric Mesh 服務的所有資源都是 Azure Resource Manager 資源。 這些資源包括應用程式、服務和秘密等等。
- 使用 [Azure 入口網站](https://portal.azure.com)、Resource Manager 範本或 Azure CLI/PowerShell 程式庫來部署和管理資源。
- 使用 [Application Insights](/azure/application-insights/) (或您選擇的工具) 設定作業監視和警示，以從平台擷取作業和診斷追蹤。
- 使用 [Application Insights](/azure/application-insights/) 或您選擇的工具，從應用程式模型存取應用程式診斷資訊。
- 在應用程式定義中指定服務的自動調整規則，以將資源使用方式最佳化。

## <a name="mission-critical-platform-capabilities"></a>任務關鍵性平台功能

Service Fabric Mesh 會建立跨 [Azure 可用性區域](../availability-zones/az-overview.md)和/或地理政治區域界限的叢集集合。 Service Fabric Mesh 可使用一組意圖來說明應用程式，例如調整、硬體需求、耐久性需求和安全性原則。  應用程式進行部署時，Service Fabric Mesh 會尋找最理想的執行位置。

透過 Service Fabric Mesh，您可以：

- 運用高可用性、相應縮小/放大、可搜尋性、協調流程、訊息路由、可靠傳訊、不需停機的升級、安全性/秘密管理、災害復原、狀態管理、組態管理和分散式交易等功能。
- 在建立應用程式時，從多個應用程式模型中選擇。
- 利用以 Swagger 產生的特定語言繫結，使用透過 REST 端點公開的平台功能。
- 跨[可用性區域](../availability-zones/az-overview.md)和多個區域部署應用程式，以達到異地可靠性。
- 使用 Azure 所提供的所有安全性和合規性功能。

## <a name="next-steps"></a>後續步驟

只需幾個步驟，即可部署 Visual Studio 的範例專案。 如需詳細資訊，請參閱[建立 ASP.NET Core 網站](service-fabric-mesh-quickstart-dotnet-core.md)。 

尋找[常見問題](service-fabric-mesh-faq.md)的解答。


<!-- Links -->

[service-fabric-overview]: ../service-fabric/service-fabric-overview.md
