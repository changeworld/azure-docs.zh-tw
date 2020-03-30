---
title: 瞭解 Azure 服務結構術語
description: 瞭解文檔其餘部分中使用的關鍵 Service Fabric 術語和概念。
author: masnider
ms.topic: conceptual
ms.date: 09/17/2018
ms.author: masnider
ms.custom: sfrev
ms.openlocfilehash: a9266c2a8d2ad179cfdb12e367a14f37d1abc9b3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79258235"
---
# <a name="service-fabric-terminology-overview"></a>Service Fabric 術語概觀

Azure Service Fabric 是分散式系統平台，可讓您輕鬆封裝、部署及管理可調整和可信賴的微服務。  您可以[在任何地方裝載 Service Fabric 叢集](service-fabric-deploy-anywhere.md)：Azure、內部部署資料中心或任何雲端提供者。  Service Fabric 是支援 [Azure Service Fabric Mesh](/azure/service-fabric-mesh) 的協調器。 您可以使用任何架構來撰寫服務，並從多種環境中選擇要執行應用程式的位置。 本文詳細說明 Service Fabric 中所使用的術語，以利您了解文件中使用的詞彙。

## <a name="infrastructure-concepts"></a>基礎結構概念

**叢集**：由虛擬或實體機器連結組成的網路，微服務可於其中部署和管理。  叢集可擴充至數千部機器。

**節點**：屬於叢集一部分的電腦或 VM 都稱為節點**。 為每個節點分配一個節點名稱（字串）。 節點具有各種特性，如 placement 屬性。 每個電腦或 VM 皆有自動啟動的 Windows 服務 `FabricHost.exe`，該服務會在開機時開始執行，然後啟動兩個執行檔：`Fabric.exe` 和 `FabricGateway.exe`。 這兩個執行檔構成節點。 在測試案例中，您可以藉由執行 `Fabric.exe` 和 `FabricGateway.exe` 的多個執行個體，在單一電腦或 VM 上裝載多個節點。

## <a name="application-and-service-concepts"></a>應用程式和服務概念

**Service Fabric Mesh 應用程式**：Service Fabric Mesh 應用程式是由資源模型 (YAML 和 JSON 資源檔) 所描述，並可部署至 Service Fabric 執行的任何環境。

**Service Fabric 原生應用程式**：Service Fabric 原生應用程式是由原生應用程式模型 (XML 應用程式和服務資訊清單) 所描述。  Service Fabric 原生應用程式無法在 Service Fabric Mesh 中執行。

### <a name="service-fabric-mesh-application-concepts"></a>Service Fabric Mesh 應用程式概念

**應用程式**：應用程式是 Mesh 應用程式部署、版本設定及存留期的單位。 每個應用程式執行個體的生命週期可以獨立進行管理。  應用程式是由一或多個服務程式碼套件和設定組成。 應用程式會使用 Azure 資源模型 (RM) 結構描述來定義。  服務在 RM 範本中描述為應用程式資源的屬性。  應用程式會參考應用程式所使用的網路和磁碟區。  建立應用程式時，會使用 Service Fabric 資源模型來建立應用程式、服務、網路和磁碟區的模型。

**服務**：應用程式中的服務代表微服務，並會執行完整且獨立的功能。 每個服務由一個或多個代碼包組成，這些代碼包描述運行與代碼包關聯的容器映射所需的一切。  您可以相應增加及減少應用程式中的服務數目。

**網路**：網路資源會為您的應用程式建立私人網路，與那些可能會參考它的應用程式或服務無關。 不同應用程式的多個服務可以是屬於同一個網路。 網路是應用程式參考的可部署資源。

**程式碼套件**：程式碼套件會描述執行與程式碼套件建立關聯之容器映像時所需的所有內容，包括：

* 容器名稱、版本和登錄
* 每個容器所需的 CPU 和記憶體資源
* 網路端點
* 容器中要掛接的磁碟區，參考不同的磁碟區資源。

所有被定義為應用程式資源的程式碼套件，會以整體的形式加以部署和啟動。

**磁碟區**：磁碟區是容器裡面掛接的目錄，可用來保存狀態。 Azure 檔案服務磁碟區驅動程式會將 Azure 檔案服務共用掛接到容器，並透過任何可支援檔案儲存的 API 提供可靠的資料儲存區。 磁碟區是應用程式參考的可部署資源。

### <a name="service-fabric-native-application-concepts"></a>Service Fabric 原生應用程式概念

**應用程式**：應用程式是組成服務的集合，這些服務會執行一或多個特定功能。 每個應用程式執行個體的生命週期可以獨立進行管理。

**服務**：服務會執行完整且獨立的功能，並且可獨立於其他服務啟動和執行。 服務是由程式碼、組態和資料所組成。 針對每個服務，程式碼由可執行檔二進位檔組成、組態由可在執行階段載入的服務設定組成，而資料由讓服務使用的任意靜態資料組成。

**應用程式類型**：分配給服務類型集合的名稱/版本。 它是在 `ApplicationManifest.xml` 檔案中定義，並內嵌在應用程式套件目錄中。 然後，系統會將目錄複製到 Service Fabric 叢集的映像存放區。 然後，您可以從這個應用程式類型，在叢集內建立具名的應用程式。

有關詳細資訊，請閱讀[應用程式模型](service-fabric-application-model.md)文章。

**應用程式包**：包含應用程式類型`ApplicationManifest.xml`檔的磁碟目錄。 參考組成此應用程式類型的每個服務類型的服務封裝。 應用程式封裝目錄中的檔案會複製到 Service Fabric 叢集的映像存放區。 例如，電子郵件應用程式類型的應用程式套件可能包含指向佇列服務套件、前端服務套件、資料庫服務套件的參考。

**具名應用程式**：您將應用程式套件複製到映像存放區之後，您會在叢集內建立應用程式的執行個體。 當您指定應用程式套件的應用程式類型時，使用其名稱或版本建立執行個體。 每個應用程式類型的執行個體會被指派一個看起來像這樣的統一資源識別項 (URI) 名稱：`"fabric:/MyNamedApp"`。 在叢集中，您可以從單一應用程式類型建立多個具名應用程式。 也可以從不同的應用程式類型建立具名應用程式。 每個具名應用程式都是獨立管理和控制版本。

**服務類型**：分配給服務的代碼包、資料包和配置包的名稱/版本。 服務類型是在 `ServiceManifest.xml` 檔案中定義，並內嵌在服務套件目錄中。 然後，應用程式套件的 `ApplicationManifest.xml` 檔案會參考此服務套件目錄。 在叢集內，建立具名應用程式之後，可以從應用程式類型的其中一個服務類型建立具名服務。 服務類型的 `ServiceManifest.xml` 檔描述該服務。

有關詳細資訊，請閱讀[應用程式模型](service-fabric-application-model.md)文章。

服務分為兩種：

* **無狀態**：當服務的持久狀態存儲在外部存儲服務（如 Azure 存儲、Azure SQL 資料庫或 Azure Cosmos DB）中時，請使用無狀態服務。 當服務沒有持續性儲存體時，請使用無狀態服務。 以計算機服務為例，首先要傳遞值給服務，服務用這些值執行計算，然後傳回結果。
* **有狀態**：當您希望 Service Fabric 通過其可靠集合或可靠參與者程式設計模型管理服務的狀態時，請使用有狀態服務。 當您在建立具名服務時，指定想要讓狀態分散到多少個資料分割 (提供延展性)。 也請指定跨節點覆寫狀態的次數 (提供可靠性)。 每個具名服務都有一個主要複本和多個次要複本。 您可以在寫入主要複本時修改具名服務的狀態。 然後，Service Fabric 將此狀態複製到所有輔助副本，以保持狀態同步。Service Fabric 會自動檢測主副本何時失敗，並將現有輔助副本提升為主副本。 然後 Service Fabric 會建立新的次要複本。  

**複本和執行個體**會參照正在部署與執行之服務的程式碼 (及具狀態服務的狀態)。 請參閱[複本和執行個體](service-fabric-concepts-replica-lifecycle.md)。

**重新設定**是指在服務複本集中進行任何變更的流程。 請參閱[重新設定](service-fabric-concepts-reconfiguration.md)。

**服務包**：包含服務類型`ServiceManifest.xml`檔的磁碟目錄。 這個檔案會參考此服務類型的程式碼、靜態資料和組態封裝。 此應用程式類型的 `ApplicationManifest.xml` 檔會參考此服務封裝目錄中的檔案。 例如，服務套件可能會參考構成資料庫服務的程式碼、靜態資料和設定套件。

**具名服務**：建立具名應用程式之後，可以在叢集內建立應用程式其中一種服務類型的執行個體。 您可以使用它的名稱/版本以指定服務類型。 需為每個服務類型執行個體指派一個 URI (名稱範圍需在其具名應用程式的 URI 之下)。 例如，如果您在具名應用程式 MyNamedApp 內建立 MyDatabase 具名服務，URI 看起來就像這樣： `"fabric:/MyNamedApp/MyDatabase"`。 在具名應用程式中，可以建立數個具名服務。 每個具名服務可以有自己的分割配置和執行個體或複本計數。

**代碼包**：包含服務類型的可執行檔的磁碟目錄，通常是 EXE/DLL 檔。 此服務類型的 `ServiceManifest.xml` 檔會參考此程式碼封裝目錄中的檔案。 當您建立具名服務時，會將程式碼套件複製到選取用來執行具名服務的一或多個節點。 然後程式碼會開始執行。 程式碼封裝執行檔分成兩種：

* **來賓執行檔**：在主機作業系統 (Windows 或 Linux) 上以原樣執行的執行檔。 這些可執行檔未連結至或參考任何 Service Fabric 執行階段檔案作業，因此不會使用任何 Service Fabric 程式設計模型。 這些可執行檔無法使用某些 Service Fabric 功能，例如端點探索的命名服務。 來賓可執行檔無法報告每個服務執行個體特定的負載計量。
* **服務主機可執行檔**：通過連結到 Service Fabric 運行時檔，啟用 Service Fabric 功能，使用 Service Fabric 程式設計模型的可執行檔。 例如，具名服務執行個體可以在 Service Fabric 命名服務註冊端點，也可以報告負載度量。

**資料套件**：磁碟目錄，包含此服務類型的靜態唯讀資料檔，通常是相片、音訊和視訊檔。 此服務類型的 `ServiceManifest.xml` 檔會參考此資料封裝目錄中的檔案。 當您建立具名服務時，會將資料套件複製到選取用來執行具名服務的一或多個節點。 程式碼會開始執行，此時即可存取資料檔案。

**設定套件**：磁碟目錄，包含此服務類型的靜態唯讀設定檔，通常是文字檔。 此服務類型的 `ServiceManifest.xml` 檔會參考此組態封裝目錄中的檔案。 創建命名服務時，配置包中的檔將複製到一個或多個選擇運行命名服務的節點。 接著會開始執行程式碼，此時即可存取設定檔。

**容器**：根據預設，Service Fabric 會以處理序形式部署和啟動這些服務。 Service Fabric 也可以在容器映像中部署服務。 容器是一種虛擬化技術，用於從應用程式抽象底層作業系統。 應用程式及其執行階段、相依性和系統程式庫會在容器內執行。 此容器有容器專屬之作業系統建構檢視的完整、私人存取權。 服務交換矩陣支援 Linux 上的 Windows Server 容器和 Docker 容器。 如需詳細資訊，請參閱 [Service Fabric 和容器](service-fabric-containers-overview.md)。

**分割配置**：當您建立具名服務時，指定分割配置。 含有大量狀態的服務會跨分割切割其資料，所以服務是將狀態分散在叢集的節點上。 藉由跨分割切割資料，您的具名服務狀態可以調整。 在分割內，無狀態的具名服務會有執行個體，而具狀態的具名服務則有複本。 通常，無狀態具名服務只會有 1 個分割，因為它們有沒有內部狀態。 資料分割執行個體提供可用性。 若某個執行個體失敗，其他執行個體會繼續正常運作，接著 Service Fabric 會建立新的執行個體。 有狀態的命名服務在副本中保持其狀態，並且每個分區都有自己的複本集，因此狀態保持同步。如果副本失敗，Service Fabric 會從現有副本生成新副本。

如需詳細資訊，請閱讀 [分割 Service Fabric Reliable Services](service-fabric-concepts-partitioning.md) 。

## <a name="system-services"></a>系統服務

有在每個叢集中建立的系統服務，用來提供 Service fabric 的平台功能。

**命名服務**：每個服務結構群集都有一個命名服務，該服務將服務名稱解析為群集中的位置。 您可以管理服務名稱與屬性，類似叢集的網際網路網域名稱系統 (DNS)。 用戶端可以使用命名服務，與叢集中的任何節點安全地進行通訊，以便解析服務名稱及其位置。 應用程式會在叢集內移動。 例如，由於失敗、平衡資源、或調整叢集大小。 您可以開發可解析目前網路位置的服務和用戶端。 用戶端會取得實際的電腦 IP 位址，以及目前執行所在的連接埠。

閱讀[與服務通信](service-fabric-connect-and-communicate-with-services.md)，瞭解有關與命名服務配合使用的用戶端和服務通信 API 的詳細資訊。

**映射存儲服務**：每個服務交換矩陣群集都有一個映射存儲服務，其中已部署版本化的應用程式包。 將應用程式封裝複製到映像存放區，然後註冊該應用程式封裝內包含的應用程式類型。 佈建應用程式類型後，您可以從中建立具名應用程式。 在刪除應用程式類型的所有具名應用程式之後，可以從映像存放區服務取消註冊該應用程式類型。

如需映像存放區服務的詳細資訊，請參閱[了解 ImageStoreConnectionString 設定](service-fabric-image-store-connection-string.md)。

如需有關將應用程式部署至映像存放區服務的詳細資訊，請閱讀[部署應用程式](service-fabric-deploy-remove-applications.md)一文。

**容錯移轉管理員服務**：每個 Service Fabric 叢集有容錯移轉管理員服務會負責下列動作：

 - 執行與服務高可用性和一致性相關的功能。
 - 協調應用程式和叢集升級。
 - 與其他系統元件互動。

**Repair Manager 服務**：這是選擇性的系統服務，讓您以安全、自動化及透明的方式針對叢集執行修復動作。 Repair Manager 適用於：

   - 針對 [Silver 和 Gold 持久性](service-fabric-cluster-capacity.md#the-durability-characteristics-of-the-cluster) Azure Service Fabric 叢集執行 Azure 維護修復作業。
   - 針對[修補程式協調流程應用程式](service-fabric-patch-orchestration-application.md)執行修復動作

## <a name="deployment-and-application-models"></a>部署和應用程式模型

若要部署您的服務，需要說明服務的執行方式。 Service Fabric 支援三種不同的部署模型：

### <a name="resource-model-preview"></a>資源模型 (預覽)

Service Fabric 資源是可個別部署至 Service Fabric 的任何項目，包括應用程式、服務、網路和磁碟區。 資源會使用可部署至叢集端點的 JSON 檔案來定義。  針對 Service Fabric Mesh，會使用 Azure 資源模型結構描述。 您也可以使用 YAML 檔案結構描述更輕鬆地撰寫定義檔。 資源可以部署於 Service Fabric 執行所在的任何位置。 資源模型是描述 Service Fabric 應用程式的最簡單方式。 其主要重點是簡單的部署和容器化服務的管理。 若要深入了解，請參閱 [Service Fabric 資源模型簡介](/azure/service-fabric-mesh/service-fabric-mesh-service-fabric-resources)。

### <a name="native-model"></a>原生模型

原生應用程式模型會為您的應用程式提供對 Service Fabric 的完整低層級存取權。 應用程式和服務會定義為已在 XML 資訊清單檔案中註冊的類型。

原生模型支援 Reliable Services 和 Reliable Actors 架構，可在 C# 和 Java 中提供對 Service Fabric 執行階段 API 及叢集管理 API 的存取權。 原生模型也支援任意的容器和可執行檔。 [Service Fabric Mesh 環境](/azure/service-fabric-mesh/service-fabric-mesh-overview)中不支援原生模型。

**Reliable Services**：用於建置無狀態和具狀態服務的 API。 具狀態服務將其狀態儲存在 Reliable Collections 中，例如字典或佇列。 您也可以插入各種不同的通訊堆疊，例如 Web API、Windows Communication Foundation (WCF)。

**Reliable Actors**：可透過虛擬的 Actor 程式設計模型，建置無狀態和具狀態物件的 API。 當您有多個獨立的計算/狀態單位時，此模型相當實用。 此模型使用回合式執行緒模型，因此最好避免向外呼叫其他動作項目或服務的程式碼，因為直到個別動作項目的所有輸出要求完成，動作項目才能處理其他連入要求。

您也可以在 Service Fabric 上執行現有的應用程式：

****：Service Fabric 支援將 Docker 容器部署至 Linux，也支援將 Windows Server 容器部署至 Windows Server 2016，並支援 Hyper-V 隔離模式。 在 Service Fabric[應用程式模型中](service-fabric-application-model.md)，容器表示在其中放置多個服務副本的應用程式主機。 Service Fabric 可以執行任何容器，其案例類似於來賓可執行檔案例，可讓您封裝容器中的現有應用程式。 此外，您還可以[在容器內運行服務交換矩陣服務](service-fabric-services-inside-containers.md)。

**來賓可執行檔**：您可以作為服務在 Azure 服務結構中運行任何類型的代碼，如 Node.js、Python、JAVA 或C++。 Service Fabric 將這些類型的服務稱為來賓可執行檔，並且視為無狀態服務。 在 Service Fabric 叢集中執行來賓可執行檔的 優點包括高可用性、健康情況監控、應用程式生命週期管理、高密度及可搜尋性。

有關詳細資訊，請閱讀"[為服務選擇程式設計模型](service-fabric-choose-framework.md)"一文。

### <a name="docker-compose"></a>Docker Compose 

[Docker Compose](https://docs.docker.com/compose/) \(英文\) 是 Docker 專案的一部分。 Service Fabric 針對[使用 Docker Compose 模型來部署應用程式](service-fabric-docker-compose.md)，提供有限的支援。

## <a name="environments"></a>環境

Service Fabric 是一項開放原始碼平台技術，有數個不同的服務和產品會以此技術為基礎。 Microsoft 提供下列選項：

 - **Azure Service Fabric Mesh**：一個完全受控的服務，可用來在 Microsoft Azure 中執行 Service Fabric 應用程式。
 - **Azure Service Fabric**：Azure 裝載的 Service Fabric 叢集供應項目。 它提供 Service Fabric 和 Azure 基礎結構之間的整合，以及 Service Fabric 叢集的升級和設定管理。
 - **獨立 Service Fabric**：一組安裝和設定工具，[可將 Service Fabric 叢集部署到任何地方](/azure/service-fabric/service-fabric-deploy-anywhere) (內部部署或在任何雲端提供者上)。 不會由 Azure 管理。
 - **Service Fabric 開發叢集**：提供在 Windows、Linux 或 Mac 上的本機開發體驗，適用於開發 Service Fabric 應用程式。

## <a name="environment-framework-and-deployment-model-support-matrix"></a>環境、架構和部署模型支援對照表

不同的環境對框架和部署模型具有不同級別的支援。 下表說明支援的架構和部署模型組合。

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

若要深入了解 Service Fabric：

* [Service Fabric 概觀](service-fabric-overview.md)
* [為何要用微服務方式建置應用程式？](service-fabric-overview-microservices.md)
* [應用程式方案](service-fabric-application-scenarios.md)

若要深入了解 Service Fabric Mesh：

* [Service Fabric Mesh 概觀](/azure/service-fabric-mesh/service-fabric-mesh-overview)
