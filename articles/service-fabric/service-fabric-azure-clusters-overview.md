---
title: 在 Windows Server 和 Linux 上建立叢集
description: Service Fabric 叢集是在 Windows Server 和 Linux 上執行。 您可以在任何可執行 Windows Server 或 Linux 的位置部署和裝載 Service Fabric 的應用程式。
services: service-fabric
documentationcenter: .net
author: dkkapur
ms.topic: conceptual
ms.date: 02/01/2019
ms.author: dekapur
ms.openlocfilehash: 2c0cc1ddc58347933b498b015c562c3822e8a688
ms.sourcegitcommit: bdd5c76457b0f0504f4f679a316b959dcfabf1ef
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/22/2020
ms.locfileid: "90978703"
---
# <a name="overview-of-service-fabric-clusters-on-azure"></a>Azure 上的 Service Fabric 叢集概觀
Service Fabric 叢集是一組由網路連接的虛擬或實體機器，可用來將您的微服務部署到其中並進行管理。 隸屬於叢集的機器或 VM 稱為叢集模式。 叢集可擴充至數千個節點。 若您新增節點至叢集，則 Service Fabric 會重新平衡全體增加節點數的服務資料分割複本和執行個體。 整體應用程式效能會有所改善，改善，並減少爭用記憶體的存取權。 若未有效率地使用叢集中的節點，您可減少叢集中的節點數目。 Service Fabric 會再次重新平衡全體減少節點數的資料分割複本和執行個體，以善加使用每個節點上的硬體。

節點類型定義叢集中一組節點 (虛擬機器) 的大小、數目和屬性。 然後每個節點類型可以獨立相應增加或相應減少，可以開啟不同組的連接埠，並可以有不同的容量度量。 節點類型是用來定義一組叢集節點的角色，例如「前端」或「後端」。 您的叢集可以有多個節點類型，但主要節點類型必須至少有五個 VM 供生產環境叢集使用 (或至少有三個 VM 供測試叢集使用)。 [Service Fabric 系統服務](service-fabric-technical-overview.md#system-services)是放置在主要節點類型的節點上。 

## <a name="cluster-components-and-resources"></a>叢集元件與資源
Azure 上的 Service Fabric 叢集是 Azure 資源，使用其他 Azure 資源以及與它們互動：
* VM 與虛擬網路卡
* 虛擬機器擴展集
* 虛擬網路
* 負載平衡器
* 儲存體帳戶
* 公用 IP 位址

![Service Fabric 叢集][Image]

### <a name="virtual-machine"></a>虛擬機器
屬於叢集的[虛擬機器](../virtual-machines/index.yml)雖然稱為節點，但是技術上叢集節點是 Service Fabric 執行階段程序。 需為每個節點指派節點名稱 (字串)。 節點具有各種特性，如 [placement 屬性](service-fabric-cluster-resource-manager-cluster-description.md#node-properties-and-placement-constraints)。 每部機器或 VM 皆有自動啟動的服務 FabricHost.exe**，該服務會在開機時開始執行，然後啟動兩個可執行檔：Fabric.exe** 和 FabricGateway.exe**，這兩個可執行檔組成節點。 生產環境部署是每個實體或虛擬機器的一個節點。 針對測試案例，您可以藉由執行多個 Fabric.exe** 和 FabricGateway.exe** 執行個體，在單一機器或 VM 上裝載多個節點。

每個 VM 會與虛擬網路介面卡 (NIC) 相關聯，而且每個 NIC 會獲得指派私人 IP 位址。  VM 已透過 NIC 指派給虛擬網路和本機平衡器。

叢集中的所有 VM 都位於虛擬網路。  相同節點類型/擴展集中的所有節點都位於虛擬網路上的相同子網路。  這些節點只有私人 IP 位址，無法直接從虛擬網路外部定址。  用戶端可以透過 Azure Load Balancer 存取節點上的服務。

### <a name="scale-setnode-type"></a>擴展集/節點類型
在建立叢集時，請定義一個以上的節點類型。  節點類型中的節點或 VM 具有相同的大小和特性，例如 CPU 數量、記憶體、磁碟數量和磁碟 I/O。  例如，一個節點類型通常適用於小型、前端 VM，具有向網際網路開啟的連接埠，而另一個節點類型適用於大型、後端 VM，用來處理資料。 在 Azure 叢集中，每個節點類型都會對應到[虛擬機器擴展集](../virtual-machine-scale-sets/index.yml)。

您可以使用擴展集來將虛擬機器集合以一組的方式加以部署和管理。 在 Azure Service Fabric 叢集中定義的每個節點類型會設定不同的擴展集。 Service Fabric 執行階段會使用 Azure VM 延伸模組，啟動到擴展集中的每部虛擬機器。 您可以分開相應增加或減少每個節點類型、變更每個叢集節點上執行的 OS SKU、開啟不同組的連接埠，並使用不同的容量計量。 擴展集有五個[升級網域](service-fabric-cluster-resource-manager-cluster-description.md#upgrade-domains)和五個[容錯網域](service-fabric-cluster-resource-manager-cluster-description.md#fault-domains)，並且可以擁有最多 100 部 VM。  您可以藉由建立多個擴展集/節點類型，來建立 100 個節點以上的叢集。

> [!IMPORTANT]
> 選擇叢集的節點類型數量以及每個節點類型的屬性 (大小、主要、網際網路對應、VM 數量等等) 是重要的工作。  如需詳細資訊，請參閱[叢集容量規劃考量](service-fabric-cluster-capacity.md)。

如需詳細資訊，請參閱 [Azure Service Fabric 節點類型與虛擬機器擴展集](service-fabric-cluster-nodetypes.md)。

### <a name="azure-load-balancer"></a>Azure Load Balancer
VM 執行個體加入到 [Azure Load Balancer](../load-balancer/load-balancer-overview.md) 後面，與 [公用 IP 位址](../virtual-network/public-ip-addresses.md)和 DNS 標籤相關聯。  當您使用* &lt; &gt; clustername*布建叢集時，DNS 名稱為* &lt; clustername &gt; 。 &lt;&gt;cloudapp.azure.com*是與擴展集前方的負載平衡器相關聯的 DNS 標籤。

叢集中的 VM 只有[私人 IP 位址](../virtual-network/private-ip-addresses.md)。  管理流量和服務流量會透過公用對應的負載平衡器進行路由。  網路流量會透過 NAT 規則 (用戶端連線到特定節點/執行個體) 或負載平衡規則 (流量循環前往 VM) 路由傳送到這些機器。  負載平衡器有一個相關聯的公用 IP，其 DNS 名稱的格式為： * &lt; clustername &gt; 。 &lt;&gt;cloudapp.azure.com*。  公用 IP 是資源群組中的另一個 Azure 資源。  如果您在叢集中定義多個節點類型，會為每個節點類型/擴展集建立負載平衡器。 或者，您可以為多個節點類型設定單一負載平衡器。  主要節點類型具有 DNS 標籤* &lt; clustername &gt; 。 &lt;&gt;cloudapp.azure.com*，其他節點類型具有 DNS 標籤* &lt; clustername &gt; - &lt; nodetype &gt; 。 &lt;&gt;cloudapp.azure.com*。

### <a name="storage-accounts"></a>儲存體帳戶
每個叢集節點類型受到 [Azure 儲存體帳戶](../storage/common/storage-introduction.md)和受控磁碟支援。

## <a name="cluster-security"></a>叢集安全性
Service Fabric 叢集是您所擁有的資源。  保護叢集是您的責任，從而協助避免未經授權的使用者與它們連線。 在叢集上執行生產工作負載時，安全的叢集尤其重要。 

### <a name="node-to-node-security"></a>節點對節點安全性
節點對節點安全性會保護叢集中 VM 與電腦之間的通訊。 此安全性情節可確保只有獲得授權加入叢集的電腦可以參與裝載應用程式和叢集中的服務。 Service Fabric 會使用 X.509 憑證來保護叢集，並提供應用程式的安全性功能。  需要叢集憑證才能保護叢集流量，並提供叢集和伺服器驗證。  自我簽署憑證可用於測試叢集，但是應該使用來自受信任憑證授權單位的憑證來保護生產環境叢集。

如需詳細資訊，請參閱[節點對節點安全性](service-fabric-cluster-security.md#node-to-node-security)

### <a name="client-to-node-security"></a>用戶端對節點安全性
用戶端對節點安全性會驗證用戶端，並協助保護用戶端與叢集中個別節點之間的通訊。 這個類型的安全性可協助確保只有獲得授權的使用者能存取叢集與叢集上部署的應用程式。 用戶端是透過其 X.509 憑證安全性認證進行唯一識別。 任意數目的選擇性用戶端憑證可用來向叢集驗證系統管理員或使用者用戶端。

除了用戶端憑證之外，也可以設定 Azure Active Directory 來向叢集驗證用戶端。

如需詳細資訊，請參閱[用戶端對節點安全性](service-fabric-cluster-security.md#client-to-node-security)

### <a name="role-based-access-control"></a>角色型存取控制
角色型存取控制 (RBAC) 可讓您指派 Azure 資源的更細緻存取控制。  您可以將不同的存取規則指派給訂用帳戶、資源群組和資源。  RBAC 規則會與資源階層一起繼承，除非在較低層級覆寫。  您可以在具有 RBAC 規則的 AAD 上指派任何使用者或使用者群組，讓指定的使用者和群組可以修改您的叢集。  如需詳細資訊，請參閱 [Azure RBAC 概觀](../role-based-access-control/overview.md)。

Service Fabric 也支援存取控制來限制不同使用者群組對特定叢集作業的存取。 這樣有助於讓叢集更安全。 針對連線到叢集的用戶端，支援兩種存取控制類型：系統管理員角色和使用者角色。  

如需詳細資訊，請參閱 [Service Fabric 角色型存取控制 (RBAC)](service-fabric-cluster-security.md#role-based-access-control-rbac)。

### <a name="network-security-groups"></a>網路安全性群組 
網路安全性群組 (NSG) 控制子網路、VM 或特定 NIC 的輸入和輸出流量。  根據預設，當多個 VM 放在相同的虛擬網路上時，它們可以透過任何連接埠彼此通訊。  如果您想要限制機器之間的通訊，可以定義 NSG 來區隔網路或將它們彼此隔離。  如果您在叢集中有多個節點類型，可以將 NSG 套用至子網路，以避免屬於不同節點類型的機器彼此通訊。  

如需詳細資訊，請參閱[安全性群組](../virtual-network/security-overview.md)

## <a name="scaling"></a>調整大小

應用程式需求會隨著時間而變更。 您可能需要增加叢集資源以因應增加的應用程式工作負載或網路流量，或是在需要下降時減少叢集資源。 在建立 Service Fabric 叢集之後，您可以水平調整叢集 (變更節點數目)，或以垂直方式調整 (變更節點的資源)。 您可以隨時調整叢集，即使正在叢集上執行工作負載，也是如此。 在叢集進行調整時，您的應用程式也會自動調整。

如需詳細資訊，請參閱[調整 Azure 叢集](service-fabric-cluster-scaling.md)。

## <a name="upgrading"></a>升級中
Azure Service Fabric 叢集是您所擁有，但部分由 Microsoft 管理的資源。 Microsoft 負責修補基礎 OS，以及在叢集上執行 Service Fabric 執行階段升級。 您可以設定您的叢集 (在 Microsoft 發行新版本時) 接收自動執行階段升級，或選擇選取您需要的受支援執行階段版本。 除了執行階段升級之外，您亦可更新憑證或應用程式連接埠等叢集組態。

如需詳細資訊，請參閱[升級叢集](service-fabric-cluster-upgrade.md)。

## <a name="supported-operating-systems"></a>支援的作業系統
您可以在執行下列作業系統的虛擬機器上建立叢集：

| 作業系統 | 最早支援的 Service Fabric 版本 |
| --- | --- |
| Windows Server 2012 R2 | 所有版本 |
| Windows Server 2016 | 所有版本 |
| Windows Server 1709 | 6.0 |
| Windows Server 1803 | 6.4 |
| Windows Server 1809 | 6.4.654.9590 |
| Windows Server 2019 | 6.4.654.9590 |
| Linux Ubuntu 16.04 | 6.0 |
| Linux Ubuntu 18.04 | 7.1 |

如需其他資訊，請參閱[Azure 中支援](./service-fabric-versions.md#supported-operating-systems)的叢集版本

> [!NOTE]
> 如果您決定在 Windows Server 1709 上部署 Service Fabric，請注意 (1) 它不是長期維護分支，因此您必須在未來移轉版本，以及 (2) 如果您部署容器，則建置於 Windows Server 2016 上的容器無法在 Windows Server 1709 上運作，反之亦然 (您必須重建容器才能部署它們)。
>


## <a name="next-steps"></a>下一步
深入了解[保護](service-fabric-cluster-security.md)、[調整](service-fabric-cluster-scaling.md)及[升級](service-fabric-cluster-upgrade.md) Azure 叢集。

了解 [Service Fabric 支援選項](service-fabric-support.md)。

[Image]: media/service-fabric-azure-clusters-overview/Cluster.PNG
