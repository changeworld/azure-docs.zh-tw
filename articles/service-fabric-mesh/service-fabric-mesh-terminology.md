---
title: 適用于 Azure Service Fabric 網狀的術語
description: 本文將詳細說明 Azure Service Fabric 網格所使用的術語，以協助您進一步瞭解檔中使用的詞彙。
author: georgewallace
ms.author: gwallace
ms.date: 11/28/2018
ms.topic: conceptual
ms.openlocfilehash: d82df6ee598664c2b85cfa82395ac66f3865cf7f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "91843001"
---
# <a name="service-fabric-mesh-terminology"></a>Service Fabric Mesh 術語

Azure Service Fabric Mesh 是一個受到完整管理的服務，讓開發人員能夠部署微服務應用程式，而不需管理虛擬機器、儲存體或網路功能。 本文將詳細說明 Azure Service Fabric 網格所使用的術語，以協助您進一步瞭解檔中使用的詞彙。

## <a name="service-fabric"></a>Service Fabric

[Service Fabric](../service-fabric/index.yml) 是一個開放原始碼的分散式系統平台，可讓您輕鬆封裝、部署及管理可調整規模和可信賴的微服務。 Service Fabric 是支援 Service Fabric Mesh 的協調器。 Service Fabric 提供如何建置及執行您微服務應用程式的選項。 您可以使用任何架構來撰寫服務，並從多種環境中選擇要執行應用程式的位置。

## <a name="application-and-service-concepts"></a>應用程式和服務概念

**Service Fabric Mesh 應用程式**：Service Fabric Mesh 應用程式是由[資源模型](./service-fabric-mesh-service-fabric-resources.md) (YAML 和 JSON 資源檔) 所描述，並可部署至 Service Fabric 執行的任何環境。

**Service Fabric 原生應用程式**：Service Fabric 原生應用程式是由[原生應用程式模型](../service-fabric/service-fabric-application-model.md) (XML 應用程式和服務資訊清單) 所描述。  Service Fabric 原生應用程式無法在 Service Fabric Mesh 中執行。

**應用程式**：Service Fabric Mesh 應用程式是 Mesh 應用程式部署、版本設定及存留期的單位。 每個應用程式執行個體的生命週期可以獨立進行管理。  應用程式是由一或多個服務程式碼套件和設定組成。 應用程式會使用 Azure 資源模型 (RM) 結構描述來定義。  服務在 RM 範本中描述為應用程式資源的屬性。  應用程式會參考應用程式所使用的網路和磁碟區。  建立應用程式時，會使用 Service Fabric 資源模型來建立應用程式、服務、網路和磁碟區的模型。

**服務**：應用程式中的服務代表微服務，並會執行完整且獨立的功能。 每項服務是由一或多個程式碼套件組成，這些套件會描述執行與程式碼套件建立關聯之容器映像時所需的所有內容。  您可以相應縮小及放大應用程式中的服務複本數目。

**程式碼套件**：程式碼套件會描述執行與程式碼套件建立關聯之容器映像時所需的所有內容，包括：

* 容器名稱、版本和登錄
* 每個容器所需的 CPU 和記憶體資源
* 網路端點
* 容器中要掛接的磁碟區，參考不同的磁碟區資源。

## <a name="deployment-and-application-models"></a>部署和應用程式模型 

若要部署您的服務，需要說明服務的執行方式。 Service Fabric 支援三種不同的部署模型：

### <a name="resource-model"></a>資源模型
Service Fabric 資源是可個別部署至 Service Fabric 的任何項目，包括應用程式、服務、網路和磁碟區。 資源會使用可部署至叢集端點的 JSON 檔案來定義。  針對 Service Fabric Mesh，會使用 Azure 資源模型結構描述。 您也可以使用 YAML 檔案結構描述更輕鬆地撰寫定義檔。 資源可以部署於 Service Fabric 執行所在的任何位置。 資源模型是描述 Service Fabric 應用程式的最簡單方式。 其主要重點是簡單的部署和容器化服務的管理。 若要深入了解，請參閱 [Service Fabric 資源模型簡介](./service-fabric-mesh-service-fabric-resources.md)。

### <a name="native-model"></a>原生模型
原生應用程式模型會為您的應用程式提供對 Service Fabric 的完整低層級存取權。 應用程式和服務會定義為已在 XML 資訊清單檔案中註冊的類型。

原生模型支援 Reliable Services 架構，可在 C# 和 Java 中提供對 Service Fabric 執行階段 API 及叢集管理 API 的存取權。 原生模型也支援任意的容器和可執行檔。

Service Fabric Mesh 環境中不支援原生模型。  如需詳細資訊，請參閱[程式設計模型概觀](../service-fabric/service-fabric-choose-framework.md)。

### <a name="docker-compose"></a>Docker Compose 
[Docker Compose](https://docs.docker.com/compose/) \(英文\) 是 Docker 專案的一部分。 Service Fabric 針對使用 Docker Compose 模型來部署應用程式，提供有限的支援。

## <a name="environments"></a>環境

Service Fabric 是一項開放原始碼平台技術，有數個不同的服務和產品會以此技術為基礎。 Microsoft 提供下列選項：

 - **Service Fabric Mesh**：一個受到完整管理的服務，可用來在 Microsoft Azure 中執行 Service Fabric 應用程式。
 - **Azure Service Fabric**：Azure 裝載的 Service Fabric 叢集供應項目。 它提供 Service Fabric 和 Azure 基礎結構之間的整合，以及 Service Fabric 叢集的升級和設定管理。
 - **獨立 Service Fabric**：一組安裝和設定工具，[可將 Service Fabric 叢集部署到任何地方](../service-fabric/service-fabric-deploy-anywhere.md) (內部部署或在任何雲端提供者上)。 不會由 Azure 管理。
 - **Service Fabric 開發叢集**：提供在 Windows、Linux 或 Mac 上的本機開發體驗，適用於開發 Service Fabric 應用程式。

## <a name="environment-framework-and-deployment-model-support-matrix"></a>環境、架構和部署模型支援對照表
不同的環境對於架構和部署模型會有不同層級的支援。 下表說明支援的架構和部署模型組合。

| 應用程式類型 | 描述依據 | Azure Service Fabric Mesh | Azure Service Fabric 叢集 (任何 OS)| 本機叢集 | 獨立叢集 |
|---|---|---|---|---|---|
| Service Fabric Mesh 應用程式 | 資源模型 (YAML & JSON) | 支援 |不支援 | Windows - 支援，Linux 和 Mac - 不支援 | Windows - 不支援 |
|Service Fabric 原生應用程式 | 原生應用程式模型 (XML) | 不支援| 支援|支援|Windows - 支援|

下表針對 Service Fabric 描述不同的應用程式模型以及對應存在的工具。

| 應用程式類型 | 描述依據 | Visual Studio | Eclipse | SFCTL | AZ CLI | Powershell|
|---|---|---|---|---|---|---|
| Service Fabric Mesh 應用程式 | 資源模型 (YAML & JSON) | VS 2017 |不支援 |不支援 | 支援 - 僅限 Mesh 環境 | 不支援|
|Service Fabric 原生應用程式 | 原生應用程式模型 (XML) | VS 2017 和 VS 2015| 支援|支援|支援|支援|

## <a name="next-steps"></a>後續步驟

若要深入了解 Service Fabric Mesh，請閱讀 [Service Fabric Mesh 概觀](service-fabric-mesh-overview.md)。

尋找[常見問題](service-fabric-mesh-faq.md)的解答。
