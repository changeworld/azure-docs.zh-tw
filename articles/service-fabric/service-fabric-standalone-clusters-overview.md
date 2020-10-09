---
title: 獨立 Service Fabric 叢集總覽
description: Service Fabric 叢集會在 Windows Server 或 Linux 上執行，這表示您能夠在任何您可以執行 Windows Server 和 Linux 的環境中部署和裝載 Service Fabric 應用程式。
ms.topic: conceptual
ms.date: 02/01/2019
ms.openlocfilehash: c57d1ef6db295400ad986adadb804fb942a723e5
ms.sourcegitcommit: b87c7796c66ded500df42f707bdccf468519943c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/08/2020
ms.locfileid: "91841062"
---
# <a name="overview-of-service-fabric-standalone-clusters"></a>Service Fabric 叢集獨立叢集的概觀

Service Fabric 叢集是一組由網路連接的虛擬或實體機器，可用來將您的微服務部署到其中並進行管理。 隸屬於叢集的機器或 VM 稱為叢集模式。 叢集可擴充至數千個節點。 若您新增節點至叢集，則 Service Fabric 會重新平衡全體增加節點數的服務資料分割複本和執行個體。 整體應用程式效能會有所改善，改善，並減少爭用記憶體的存取權。 若未有效率地使用叢集中的節點，您可減少叢集中的節點數目。 Service Fabric 會再次重新平衡全體減少節點數的資料分割複本和執行個體，以善加使用每個節點上的硬體。

節點類型會定義叢集中一組節點的大小、數目和屬性。 然後每個節點類型可以獨立相應增加或相應減少，可以開啟不同組的連接埠，並可以有不同的容量度量。 節點類型是用來定義一組叢集節點的角色，例如「前端」或「後端」。 您的叢集可以有多個節點類型，但主要節點類型必須至少有五個 VM 供生產環境叢集使用 (或至少有三個 VM 供測試叢集使用)。 [Service Fabric 系統服務](service-fabric-technical-overview.md#system-services)是放置在主要節點類型的節點上。

在內部部署環境建立 Service Fabric 叢集的程序會與在您所選擇、具有一組 VM 的任何雲端建立叢集的程序類似。 佈建 VM 的初始步驟取決於您要使用的雲端提供者或內部部署環境。 在您有一組彼此之間已啟用網路連線的 VM 之後，則安裝 Service Fabric 套件、編輯叢集設定，以及執行叢集建立與管理指令碼的步驟將會相同。 這可確保當您選擇以新裝載環境做為目標時，可將您操作和管理 Service Fabric 叢集方面的知識與經驗轉移過去。

## <a name="cluster-security"></a>叢集安全性

Service Fabric 叢集是您所擁有的資源。  保護叢集是您的責任，從而協助避免未經授權的使用者與它們連線。 在叢集上執行生產工作負載時，安全的叢集尤其重要。

> [!NOTE]
> Windows 驗證是以 Kerberos 為基礎。 NTLM 不支援做為驗證類型。
>
> 請盡可能針對 Service Fabric 叢集使用 x.509 憑證驗證。

### <a name="node-to-node-security"></a>節點對節點安全性

節點對節點安全性會保護叢集中 VM 與電腦之間的通訊。 此安全性情節可確保只有獲得授權加入叢集的電腦可以參與裝載應用程式和叢集中的服務。 Service Fabric 會使用 X.509 憑證來保護叢集，並提供應用程式的安全性功能。  需要叢集憑證才能保護叢集流量，並提供叢集和伺服器驗證。  自我簽署憑證可用於測試叢集，但是應該使用來自受信任憑證授權單位的憑證來保護生產環境叢集。

Windows 安全性也可以針對 Windows 獨立叢集啟用。 如果您有 Windows Server 2012 R2 和 Windows Active Directory，建議您搭配使用 Windows 安全性與群組受管理的服務帳戶。 否則，使用 Windows 安全性與 Windows 帳戶。

如需詳細資訊，請參閱[節點對節點安全性](service-fabric-cluster-security.md#node-to-node-security)

### <a name="client-to-node-security"></a>用戶端對節點安全性

用戶端對節點安全性會驗證用戶端，並協助保護用戶端與叢集中個別節點之間的通訊。 這個類型的安全性可協助確保只有獲得授權的使用者能存取叢集與叢集上部署的應用程式。 用戶端是透過其 X.509 憑證安全性認證進行唯一識別。 任意數目的選擇性用戶端憑證可用來向叢集驗證系統管理員或使用者用戶端。

除了用戶端憑證之外，也可以設定 Azure Active Directory 來向叢集驗證用戶端。

如需詳細資訊，請參閱[用戶端對節點安全性](service-fabric-cluster-security.md#client-to-node-security)

### <a name="role-based-access-control-rbac"></a>角色型存取控制 (RBAC)
Service Fabric 也支援存取控制來限制不同使用者群組對特定叢集作業的存取。 這樣有助於讓叢集更安全。 針對連線到叢集的用戶端，支援兩種存取控制類型：系統管理員角色和使用者角色。  

如需詳細資訊，請參閱[角色型存取控制 (RBAC)](service-fabric-cluster-security.md#role-based-access-control-rbac)。

## <a name="scaling"></a>調整大小

應用程式需求會隨著時間而變更。 您可能需要增加叢集資源以因應增加的應用程式工作負載或網路流量，或是在需要下降時減少叢集資源。 在建立 Service Fabric 叢集之後，您可以水平調整叢集 (變更節點數目)，或以垂直方式調整 (變更節點的資源)。 您可以隨時調整叢集，即使正在叢集上執行工作負載，也是如此。 在叢集進行調整時，您的應用程式也會自動調整。

如需詳細資訊，請參閱[調整獨立叢集](service-fabric-cluster-scaling-standalone.md)。

## <a name="upgrading"></a>升級中

獨立叢集是由您完全擁有的資源。 由您負責修補基礎 OS 和起始網狀架構升級。 您可以設定您的叢集 (在 Microsoft 發行新版本時) 接收自動執行階段升級，或選擇選取您需要的受支援執行階段版本。 除了網狀架構升級，您亦可修補 OS 並更新憑證或應用程式連接埠等叢集設定。 

如需詳細資訊，請參閱[升級獨立叢集](service-fabric-cluster-upgrade-standalone.md)。

## <a name="supported-operating-systems"></a>支援的作業系統

您可以在執行下列作業系統的 VM 或電腦上建立叢集 (尚不支援 Linux)：

* Windows Server 2012 R2
* Windows Server 2016 
* Windows Server 2019

## <a name="next-steps"></a>後續步驟

深入了解[保護](service-fabric-cluster-security.md)、[調整](service-fabric-cluster-scaling-standalone.md)及[升級](service-fabric-cluster-upgrade-standalone.md)獨立叢集。

了解 [Service Fabric 支援選項](service-fabric-support.md)。
