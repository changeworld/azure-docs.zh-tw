---
title: Azure Service Fabric 資源模型簡介
description: 了解 Service Fabric 資源模型，一種定義 Service Fabric Mesh 應用程式的簡化方法。
author: vturecek
ms.topic: conceptual
ms.date: 10/23/2018
ms.author: vturecek
ms.custom: mvc, devcenter
ms.openlocfilehash: 0ae2ed163560aee4c0c3525ab31910e37afaa5b9
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "85847013"
---
# <a name="introduction-to-service-fabric-resource-model"></a>Azure Service Fabric 資源模型簡介

Service Fabric 資源模型描述一種簡單的方法，可定義組成 Service Fabric Mesh 應用程式的資源。 個別資源可以部署至任何 Service Fabric 環境。  Service Fabric 資源模型也可與 Azure Resource Manager 模型相容。 以下是這個模型目前支援的資源類型：

- 應用程式與服務
- 網路
- 閘道
- 祕密和祕密/值
- 磁碟區

描述每個資源時，會將它們視為宣告的資源檔，也就是簡單的 YAML 或 JSON 文件來描述 Mesh 應用程式，而且是由 Service Fabric 平台佈建的。

## <a name="applications-and-services"></a>應用程式與服務

應用程式資源是 Mesh 應用程式部署、版本控制以及存留期的單位。 它包含一或多個代表微服務的服務資源。 而每個服務資源會包含一個或多個程式碼封裝，這些封裝會描述執行程式碼套件相關容器映像時所需的內容。

![應用程式和服務][Image1]

服務資源會宣告下列項目：

- 容器名稱、版本和登錄
- 每個容器所需的 CPU 和記憶體資源
- 網路端點
- 對其他資源 (例如，網路、磁碟區和祕密) 的參考 

所有被定義為服務資源的程式碼封裝，會以整體的形式加以部署和啟動。 服務資源也會描述要執行的服務執行個體數目，也會參考它依賴的其他資源 (例如，網路資源)。

如果 Mesh 應用程式是由多個服務所組成，不保證它們會在相同節點上同時執行。 此外，應用程式升級時，某一個服務升級失敗會導致所有服務要回復成以前的版本。

如前文所述，每個應用程式執行個體的生命週期都可以獨立管理。 例如，應用程式執行個體可以獨立升級。 通常，您會儘可能將最少量的服務放入應用程式中，因為放入應用程式的服務越多，就越不容易管理每個服務。

## <a name="networks"></a>網路

網路資源是個別部署的資源，與那些會參考它的應用程式或服務資源 (相依項) 無關。 它可用來建立應用程式的網路。 不同應用程式的多個服務可以是屬於同一個網路。  如需詳細資訊，請參閱 [Service Fabric Mesh 應用程式中的網路功能](service-fabric-mesh-networks-and-gateways.md)。

> [!NOTE]
> 目前的預覽版本僅支援應用程式和網路之間的一對一對應

![網路和閘道][Image2]

## <a name="gateways"></a>閘道
閘道資源會將兩個網路連接在一起，並路由傳送流量。  閘道可讓服務與外部用戶端通訊，並對服務提供輸入。  閘道也可用來將 Mesh 應用程式與您自己的現有虛擬網路連接在一起。 如需詳細資訊，請參閱 [Service Fabric Mesh 應用程式中的網路功能](service-fabric-mesh-networks-and-gateways.md)。

![網路和閘道][Image2]

## <a name="secrets"></a>密碼

祕密資源是可部署的資源，與那些會參考它的應用程式或服務資源 (相依項) 無關。 它可用來安全地傳遞祕密給應用程式。 不同應用程式的多個服務可以參考同一個祕密的值。

## <a name="volumes"></a>磁碟區

容器通常可提供暫存磁碟。 但暫存磁碟是暫時的物件，因此您會得到新的暫存磁碟，當容器損毀時則會遺失資訊。 您也很難與其他容器共用暫存磁碟上的資訊。 磁碟區是容器裡面掛接的目錄，可用來保存狀態。 磁碟區會提供一般用途的檔案儲存體，並可讓您使用一般磁碟 I/O 檔案 API 來讀取/寫入檔案。 磁碟區資源是一種宣告方式，可描述目錄的掛接方式以及其支援儲存體 (檔案儲存體磁碟區或 Service Fabric 可靠磁碟區)。  如需詳細資訊，請參閱[儲存狀態](service-fabric-mesh-storing-state.md#volumes)。

![磁碟區][Image3]

## <a name="programming-models"></a>程式設計模型
服務資源只需要一個容器映像即可執行，而且資源相關的程式碼套件會參照該映像。 您可以在容器裡面使用任何架構，不需要知道或使用 Service Fabric Mesh 專用的 API，即可執行以任何語言撰寫的程式碼。 

您的應用程式程式碼即便是在 Service Fabric Mesh 外面，仍然可移植。而且您的應用程式部署會保持一致，無論實作服務時所採用的語言或架構為何。 無論您的應用程式是 ASP.NET Core、 Go 或只是一組處理程序和指令碼，Service Fabric Mesh 資源部署模型保持不變。 

## <a name="packaging-and-deployment"></a>封裝和部署

以資源模型為基礎的 Service Fabric Mesh 應用程式會封裝為 Docker 容器。  Service Fabric Mesh 是共用的多租用戶環境，容器則可提供高層級的隔離機制。  這些應用程式會使用 JSON 格式或 YAML 格式 (這會接著轉換為 JSON) 來描述。 將網狀應用程式部署至 Azure Service Fabric 網格時，用來描述應用程式的 JSON 是 Azure Resource Manager 範本。 資源會對應至 Azure 資源。  將網狀應用程式部署至 Service Fabric 叢集（獨立或 Azure 託管）時，用來描述應用程式的 JSON 格式類似于 Azure Resource Manager 範本。  Mesh 應用程式在部署後可透過 HTTP 介面或 Azure CLI 來管理。 


## <a name="next-steps"></a>後續步驟 
若要深入了解 Service Fabric Mesh，請閱讀下列概觀：
- [Service Fabric Mesh 概觀](service-fabric-mesh-overview.md)

[Image1]: media/service-fabric-mesh-service-fabric-resources/AppsAndServices.png
[Image2]: media/service-fabric-mesh-service-fabric-resources/NetworkAndGateway.png
[Image3]: media/service-fabric-mesh-service-fabric-resources/volumes.png
