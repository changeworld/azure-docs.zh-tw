---
title: 使用 Azure 可用性區域的 SAP 工作負載設定 | Microsoft Docs
description: 使用 Azure 可用性區域的 SAP NetWeaver 適用的高可用性架構和案例
services: virtual-machines-windows,virtual-network,storage
documentationcenter: saponazure
author: msjuergent
manager: bburns
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: 887caaec-02ba-4711-bd4d-204a7d16b32b
ms.service: virtual-machines-windows
ms.subservice: workloads
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 12/29/2020
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 7418e5578450367e9fa37a87adb6e7036619877b
ms.sourcegitcommit: 42922af070f7edf3639a79b1a60565d90bb801c0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/31/2020
ms.locfileid: "97827443"
---
# <a name="sap-workload-configurations-with-azure-availability-zones"></a>使用 Azure 可用性區域的 SAP 工作負載設定
除了在 Azure 可用性設定組中部署不同的 SAP 架構層之外，最近引進的 [Azure 可用性區域](../../../availability-zones/az-overview.md) 也可用於 SAP 工作負載部署。 Azure 可用性區域的定義如下：「區域內的唯一實體位置。 每個區域都是由一或多個配備獨立電源、冷卻和網路的資料中心所組成。 Azure 可用性區域不適用於所有區域。 針對提供可用性區域的 Azure 區域，請檢查 [azure 區域對應](https://azure.microsoft.com/global-infrastructure/geographies/)。 這項地圖將為您示範哪些區域提供或已宣佈提供可用性區域。 

從一般的 SAP NetWeaver 或 S/4HANA 架構，您需要保護三個不同的層級：

- SAP 應用層，可以是數十個 Vm 的一層。 您想要將 Vm 部署在相同主機伺服器上的機率降到最低。 您也希望這些 Vm 可以接受 DBMS 層的相近，以在可接受的視窗中保持網路延遲
- SAP ASCS/SCS 層代表 SAP NetWeaver 和 S/4HANA 架構中的單一失敗點。 您通常會看到兩個您想要使用容錯移轉架構來涵蓋的 Vm。 因此，這些 Vm 應配置在不同的基礎結構錯誤和更新網域中
- SAP DBMS 層，也就是表示單一失敗點。 在一般情況下，它是由容錯移轉架構所涵蓋的兩部 Vm 所組成。 因此，這些 Vm 應配置在不同的基礎結構錯誤和更新網域中。 例外狀況是可使用兩個以上 Vm 的向外延展部署 SAP Hana

透過可用性設定組或可用性區域部署重要 Vm 的主要差異如下：

- 使用可用性設定組進行部署時，會將集合中的 Vm 對齊單一區域或資料中心內的 Vm (任何適用于特定區域) 的資料。 如此一來，透過可用性設定組的部署就不會受到影響整個區域的 dataceter (s) 的電源、冷卻或網路問題。 在加號旁邊，Vm 會與該區域或資料中心內的更新和容錯網域對齊。 尤其是針對每個可用性設定組保護兩個 Vm 的 SAP ASCS 或 DBMS 層，與容錯和更新網域的一致，可防止兩個 Vm 都在相同的主機硬體上 
- 透過 Azure 可用性區域部署 Vm，並選擇不同的區域 (最多三個可能的) ，就是將 Vm 部署到不同的實體位置，並將其新增至影響整個區域的 dataceter () s 的電源、冷卻或網路功能問題。 不過，當您在相同的可用性區域中部署相同 VM 系列的多部 VM 時，不會受到這些 Vm 在相同主機上的保護。 如此一來，透過可用性區域部署是最適合 SAP ASCS 和 DBMS 層，通常會查看兩個 Vm。 針對可大幅超過兩部 Vm 的 SAP 應用層，您可能必須切換回不同的部署模型 (查看之後) 

您對於跨 Azure 可用性區域部署的動機應該是您在涵蓋單一關鍵 VM 失敗的情況，或是減少重大中軟體修補停機的能力，您想要保護可能會影響一或多個 Azure 資料中心可用性的較大型基礎結構問題。 

## <a name="considerations-for-deploying-across-availability-zones"></a>在可用性區域上進行部署的考量事項


使用可用性區域時請考量下列事項：

- 對於 Azure 區域內不同可用性區域之間的距離，並不提供相關保證。
- 可用性區域並非理想的 DR 解決方案。 天然災害會導致全球某些區域產生大範圍損害，包括電力基礎結構的嚴重損害。 不同區域之間的距離可能不足以構成適當的 DR 解決方案。
- 可用性區域間的網路延遲，並非在所有 Azure 區域中都相同。 在某些情況下，您可以跨不同區域部署及執行 SAP 應用程式層，因為從某個區域到作用中 DBMS VM 的網路延遲，是可接受的。 但在某些 Azure 區域中，作用中 DBMS VM 與部署在不同區域的 SAP 應用程式執行個體之間的延遲太高，對 SAP 商務程序而言是不可接受的。 在這些情況下，部署架構必須是不同的，並具有應用程式的主動/主動架構，或是跨區域網路延遲過高的主動/被動架構。
- 決定要在何處使用可用性區域時，請以各區域間的網路延遲作為考量依據。 網路延遲在以下兩方面扮演重要角色：
    - 兩個需要同步複寫的 DBMS 執行個體之間的延遲。 網路延遲越高，就越有可能影響到您工作負載的延展性。
    - 在和作用中 DBMS 執行個體相同區域中執行 SAP 對話執行個體的 VM，以及位於另一個區域中的相似 VM 之間的網路延遲差異。 此差異擴大時，對商務程序和批次作業的執行時間也會隨之拉長，這取決於它們是否在和 DBMS 相同的區域中執行。

在可用性區域之間部署 Azure VM 以及在相同 Azure 區域內建立容錯移轉解決方案時，會有一些限制：

- 部署至 Azure 可用性區域時，必須使用 [Azure 受控磁碟](https://azure.microsoft.com/services/managed-disks/)。 
- 針對實體區域的區域列舉對應是依 Azure 訂用帳戶來決定。 如果您使用不同的訂用帳戶來部署 SAP 系統，則必須為每個訂用帳戶定義理想的區域。
- 除非您使用 [Azure 鄰近放置群組](../../linux/co-location.md)，否則無法在 Azure 可用性區域內部署 azure 可用性設定組。 如何跨區域部署 SAP DBMS 層和中央服務的方式，同時使用可用性設定組部署 SAP 應用層，並仍能達到 Vm 的近近距離，請參閱 [Azure 鄰近放置群組以取得 SAP 應用程式的最佳網路延遲](sap-proximity-placement-scenarios.md)。 如果您不是使用 Azure 鄰近放置群組，則需要選擇其中一個，作為虛擬機器的部署架構。
- 您無法使用 [Azure 基本 Load Balancer](../../../load-balancer/load-balancer-overview.md) 來建立以 Windows Server 容錯移轉叢集或 Linux Pacemaker 為基礎的容錯移轉叢集解決方案。 相反地，您必須使用 [Azure STANDARD LOAD BALANCER SKU](../../../load-balancer/load-balancer-standard-availability-zones.md)。



## <a name="the-ideal-availability-zones-combination"></a>理想的可用性區域組合
如果您想要跨區域部署 SAP NetWeaver 或 S/4HANA 系統，您可以部署兩個原則架構：

- 主動/主動：執行 ASCS/SCS 的 Vm 配對和執行 DBMS 層的 VM 配對，會分散到兩個區域。 執行 SAP 應用層的 Vm 數目會部署到相同兩個區域的偶數。 如果 DBMS 或 ASCS/SCS VM 進行容錯移轉，某些開啟和使用中的交易可能會回復。 但使用者仍在登入。 作用中 DBMS VM 和應用程式實例執行所在的區域並不重要。 此架構是跨區域部署的慣用架構。
- 主動/被動：執行 ASCS/SCS 的 Vm 配對和執行 DBMS 層的 VM 配對，會分散到兩個區域。 執行 SAP 應用層的 Vm 數目會部署到其中一個可用性區域。 您會在與現用 ASCS/SCS 和 DBMS 實例相同的區域中執行應用層。 如果跨不同區域的網路延遲太長，而無法執行跨區域分散的應用層，您就可以使用此部署架構。 相反地，SAP 應用層必須在與作用中的 ASCS/SCS 及/或 DBMS 實例相同的區域中執行。 如果 ASCS/SCS 或 DBMS VM 容錯移轉至次要區域，您可能會遇到較高的網路延遲，並降低輸送量。 而且，您必須儘快容錯回復先前已容錯移轉的 VM，以回到先前的輸送量層級。 如果發生區域性中斷，則應用層需要容錯移轉至次要區域。 使用者在完成系統關機時所遇到的活動。 在某些 Azure 區域中，當您想要使用可用性區域時，此架構是唯一可行的架構。 如果您無法接受 ASCS/SCS 或 DBMS VM 容錯移轉至次要區域的潛在影響，您可能更適合保持可用性設定組部署


因此在您決定如何使用可用性區域之前，您必須先判斷：

- Azure 區域 (Region) 的三個區域 (Zone) 之間的網路延遲。 瞭解區域區域間的網路延遲，可讓您選擇在跨區域網路流量中具有最低網路延遲的區域。
- 您所選區域內的 VM 對 VM 延遲，與您所選擇的兩個區域在網路延遲上的差異。
- 確認您需要部署的 VM 類型在您選取的兩個區域中是否適用。 某些 VM (尤其是 M 系列 VM) 可能會發生部分 SKU 僅適用於其中兩個區域 (共三個) 的情況。

## <a name="network-latency-between-and-within-zones"></a>區域之間和區域內的網路延遲
若要確認不同區域之間的延遲，您必須：

- 在全部三個區域中部署您想要用於 DBMS 執行個體的 VM SKU。 在執行此測量時，請確定已啟用 [Azure 加速網路](https://azure.microsoft.com/blog/maximize-your-vm-s-performance-with-accelerated-networking-now-generally-available-for-both-windows-and-linux/)。
- 當您找到具有最低網路延遲的兩個區域時，請部署 VM SKU 中的另外三個 VM，作為要跨三個可用性區域的應用程式層 VM。 在您所選取的兩個 DBMS 區域中，對兩個 DBMS VM 測量網路延遲。 
- 使用 **`niping`** 作為測量工具。 此為來自 SAP 的工具，相關說明請見 SAP 支援附註 [#500235](https://launchpad.support.sap.com/#/notes/500235) 和 [#1100926](https://launchpad.support.sap.com/#/notes/1100926/E)。 請將重點放在文件中的延遲測量命令上。 由於 **ping** 無法在 Azure 加速網路程式碼路徑中運作，因此不建議使用。

您不需要手動執行這些測試。 您可以找到可將所述延遲測試自動化的 PowerShell 程式 [可用性區域延遲測試](https://github.com/Azure/SAP-on-Azure-Scripts-and-Utilities/tree/master/AvZone-Latency-Test) 。 

根據測量結果和 VM SKU 在可用性區域中的可用情形，您必須做出某些決定：

- 定義適用於 DBMS 層的理想區域。
- 決定您是否想要根據區域中與跨區域的網路延遲差異，將作用中 SAP 應用程式層分布在一個、兩個或全部三個區域間。
- 從應用程式的觀點，決定您要部署主動/被動組態還是主動/主動組態。 (本文稍後將說明這些組態。)

在做出這些決定時，也請考量 SAP 附註 [#1100926](https://launchpad.support.sap.com/#/notes/1100926/E)中所提到的 SAP 網路延遲建議事項。

> [!IMPORTANT]
> 您所做的測量和決定皆僅適用於您在測量時所使用的 Azure 訂用帳戶。 如果您使用其他 Azure 訂用帳戶，則必須另行測量。 列舉區域的對應可能因 Azure 訂用帳戶的不同而有所差異。


> [!IMPORTANT]
> 前述測量在每個支援[可用性區域](https://azure.microsoft.com/global-infrastructure/geographies/)的 Azure 區域中應會產生不同的結果。 即使您的網路延遲需求相同，您在不同的 Azure 區域中可能也需要採用不同的部署策略，因為不同區域之間的網路延遲可能有所差異。 在某些 Azure 區域中，三個不同區域間的網路延遲可能會大不相同。 在其他 Azure 區域中，三個不同區域間的網路延遲可能會較為一致。 區域間一定會有 1 到 2 毫秒的網路延遲，是不正確的指稱。 Azure 區域中各個可用性區域之間的網路延遲，是無法一般化的。

## <a name="activeactive-deployment"></a>主動/主動部署
此部署架構稱為主動/主動，因為您會在兩個或三個區域中部署作用中的 SAP 應用程式伺服器。 使用加入佇列複寫的 SAP 中央服務執行個體，會部署到兩個區域之間。 DBMS 層也是如此，會部署到和 SAP 中央服務相同的區域中。 在考量此設定時，您必須在您的區域中尋找能針對您的工作負載和同步 DBMS 複寫提供可接受的跨區域網路延遲的兩個可用性區域。 您也應確定，所選區域內的網路延遲，與跨區域網路延遲之間的差異，也不能太大。 

SAP 架構的本質是，除非您以不同的方式進行設定，否則使用者和批次作業可以在不同的應用程式實例中執行。 這項事實與主動/主動部署的副作用是，任何 SAP 應用程式實例都可以執行批次作業，這些實例不會與作用中 DBMS 在相同區域中執行。 如果不同區域之間的網路延遲差異小於區域內的網路延遲，則批次作業的執行時間差異可能並不明顯。 不過，相較于跨區域網路流量，區域內網路延遲的差異越大，如果作業是在 DBMS 實例非使用中的區域中執行，則批次作業的執行時間可能會受到影響。 您是以客戶的身份決定執行時間的可接受差異。 而且有了這項功能，可容忍跨區域流量的網路延遲。

在不同可用性區域之間部署的應用層，在執行時間和輸送量之間，不會有很大差異的 Azure 區域，例如：

- West 美國 2 (三個區域) 
- 東部美國 2 (三個區域) 
- 美國中部 (三個區域) 
- 北歐 (三個區域) 
- 西歐 (三個區域中的兩個) 
- 美國東部 (三個區域中的兩個) 
- 美國中南部 (三個區域中的兩個) 
- 英國南部 (三個區域中的兩個) 

不建議在區域中使用此 SAP 部署架構的 Azure 區域為：

- 法國中部 
- 南非北部
- 加拿大中部
- 日本東部

取決於您願意容忍執行時間差異的部分，其他未列出的區域也有資格。


跨兩個區域的主動/主動部署的簡化結構描述如下所示：

![主動/主動區域部署](./media/sap-ha-availability-zones/active_active_zones_deployment.png)

此設定需要考慮下列事項：

- 若未使用 [Azure 鄰近放置群組](../../linux/co-location.md)，則會將 Azure 可用性區域視為所有 vm 的容錯和更新網域，因為可用性設定組無法在 Azure 可用性區域中部署。
- 如果您想要合併 DBMS 層和中央服務的區域部署，但想要使用應用層的 Azure 可用性設定組，您必須使用 azure 鄰近放置群組一文中所述的 Azure 相近群組， [以獲得 SAP 應用程式的最佳網路延遲](sap-proximity-placement-scenarios.md)。
- 針對 SAP 中央服務和 DBMS 層的容錯移轉叢集所使用的負載平衡器，必須是[標準 SKU Azure 負載平衡器](../../../load-balancer/load-balancer-standard-availability-zones.md)。 基本負載平衡器將無法跨區域運作。
- 您部署用以裝載 SAP 系統的 Azure 虛擬網路，會連同其子網路擴展至各個區域。 您的每個區域不需要個別的虛擬網路。
- 針對您部署的所有虛擬機器，您必須使用 [Azure 受控磁碟](https://azure.microsoft.com/services/managed-disks/)。 非受控磁碟不支援區域部署。
- Azure 進階儲存體、 [ULTRA SSD 儲存體](../../disks-types.md#ultra-disk)或 ANF 不支援跨區域進行任何類型的儲存體複寫。 應用程式 (DBMS 或 SAP 中央服務) 必須複寫重要的資料。
- 共用的 sapmnt 目錄也是如此，包括共用磁碟 (Windows)、CIFS 共用 (Windows) 或 NFS 共用 (Linux)。 您必須使用可在區域之間複寫這些共用磁碟或共用的技術。 支援的技術如下：
  - 在 Windows 中，支援使用 SIOS Datakeeper 的叢集解決方案，如[在 Azure 中使用叢集共用磁碟於 Windows 容錯移轉叢集上進行 SAP ASCS/SCS 執行個體叢集處理](./sap-high-availability-guide-wsfc-shared-disk.md)中所說明。
  - 在 SUSE Linux 中，支援依據 [SUSE Linux Enterprise Server 上的 Azure VM 針對 NFS 提供的高可用性](./high-availability-guide-suse-nfs.md)所說明的方式建置的 NFS 共用。
    
    目前，使用 Microsoft 向外延展檔案伺服器的解決方案 (如[使用 SAP ASCS/SCS 執行個體的 Windows 容錯移轉叢集和檔案共用，為 SAP 高可用性準備 Azure 基礎結構](./sap-high-availability-infrastructure-wsfc-file-share.md)中所說明) 並不支援跨區域使用。
- 如果您建立 [SUSE Linux Pacemaker](./high-availability-guide-suse-pacemaker.md#create-azure-fence-agent-stonith-device) 叢集，並使用 SBD 裝置而非 Azure 隔離代理程式，則會使用第三個區域來裝載 SBD 裝置。 或其他應用程式實例。
- 若要達到重要商務程式的執行時間一致性，您可以嘗試使用 SAP 批次伺服器群組、SAP 登入群組或 RFC 群組，將特定批次作業和使用者導向至使用中 DBMS 實例所在區域的應用程式實例。 不過，在發生區域容錯移轉的情況下，您必須手動將這些群組移至在 VM 上執行的執行個體 (其位於與作用中 DB VM 相同的區域中)。  
- 您可以在每個區域中部署休眠對話方塊執行個體。 

> [!IMPORTANT]
> 在這種主動/主動案例中，頻寬的額外費用是由 Microsoft 從04/01/2020 宣佈。 請查看檔 [頻寬定價詳細資料](https://azure.microsoft.com/pricing/details/bandwidth/)。 SAP 應用層和 SAP DBMS 層之間的資料傳輸相當密集。 因此主動/主動案例可能會對成本造成很大的貢獻。 繼續查看這篇文章以取得確切的成本 


## <a name="activepassive-deployment"></a>主動/被動部署
如果一個區域內的網路延遲與跨區域網路流量的延遲之間出現不可接受的差異，您可以部署從 SAP 應用程式層的觀點來看具有主動/被動特性的架構。 您可以定義 *作用中* 區域，在此區域中部署完整應用程式層，並且嘗試執行作用中 DBMS 和 SAP 中央服務執行個體。 使用此類設定時，您必須依據作業是否在作用中 DBMS 執行個體所在的區域中執行，確定商務程序和批次作業不會有太大的執行時間差異。

適用于跨不同區域的這類部署架構的 Azure 區域可能是：

- 東南亞
- 澳大利亞東部
- 巴西南部
- 德國中西部
- 南非北部
- 法國中部 
- 加拿大中部
- 日本東部


此架構的基本配置如下所示：

![主動/被動區域部署](./media/sap-ha-availability-zones/active_passive_zones_deployment.png)

此設定需要考慮下列事項：

- 可用性設定組無法部署到 Azure 可用性區域中。 若要彌補這一點，您可以使用 azure 鄰近放置群組（如 Azure 鄰近放置群組中所述），以提供 [SAP 應用程式的最佳網路延遲](sap-proximity-placement-scenarios.md)。
- 使用此架構時，您必須嚴密監視狀態，並嘗試將作用中的 DBMS 和 SAP 中央服務執行個體維持在已部署的應用程式層所在的區域中。 當 SAP 中央服務或 DBMS 執行個體進行容錯移轉時，您應確保能夠盡快手動容錯回復到部署 SAP 應用程式層的區域中。
- 針對 SAP 中央服務和 DBMS 層的容錯移轉叢集所使用的負載平衡器，必須是[標準 SKU Azure 負載平衡器](../../../load-balancer/load-balancer-standard-availability-zones.md)。 基本負載平衡器將無法跨區域運作。
- 您部署用以裝載 SAP 系統的 Azure 虛擬網路，會連同其子網路擴展至各個區域。 您的每個區域不需要個別的虛擬網路。
- 針對您部署的所有虛擬機器，您必須使用 [Azure 受控磁碟](https://azure.microsoft.com/services/managed-disks/)。 非受控磁碟不支援區域部署。
- Azure 進階儲存體、 [ULTRA SSD 儲存體](../../disks-types.md#ultra-disk)或 ANF 不支援跨區域進行任何類型的儲存體複寫。 應用程式 (DBMS 或 SAP 中央服務) 必須複寫重要的資料。
- 共用的 sapmnt 目錄也是如此，包括共用磁碟 (Windows)、CIFS 共用 (Windows) 或 NFS 共用 (Linux)。 您必須使用可在區域之間複寫這些共用磁碟或共用的技術。 支援的技術如下：
    - 在 Windows 中，支援使用 SIOS Datakeeper 的叢集解決方案，如[在 Azure 中使用叢集共用磁碟於 Windows 容錯移轉叢集上進行 SAP ASCS/SCS 執行個體叢集處理](./sap-high-availability-guide-wsfc-shared-disk.md)中所說明。
    - 在 SUSE Linux 中，支援依據 [SUSE Linux Enterprise Server 上的 Azure VM 針對 NFS 提供的高可用性](./high-availability-guide-suse-nfs.md)所說明的方式建置的 NFS 共用。
    
  目前，使用 Microsoft 向外延展檔案伺服器的解決方案 (如[使用 SAP ASCS/SCS 執行個體的 Windows 容錯移轉叢集和檔案共用，為 SAP 高可用性準備 Azure 基礎結構](./sap-high-availability-infrastructure-wsfc-file-share.md)中所說明) 並不支援跨區域使用。
- 如果您建立 [SUSE Linux Pacemaker](./high-availability-guide-suse-pacemaker.md#create-azure-fence-agent-stonith-device) 叢集，並使用 SBD 裝置而非 Azure 隔離代理程式，則會使用第三個區域來裝載 SBD 裝置。 或其他應用程式實例。
- 您應該從 DBMS 的觀點來看，在被動區域 (中部署休眠的 Vm) 因此，您可以在發生區域失敗時啟動應用程式資源。
    - [Azure Site Recovery](https://azure.microsoft.com/services/site-recovery/) 目前無法跨區域將作用中 VM 複寫到休眠 VM。 
- 您應投資自動化，讓您能夠在發生區域性中斷時，在第二個區域中自動啟動 SAP 應用層。

## <a name="combined-high-availability-and-disaster-recovery-configuration"></a>結合高可用性和災害復原的設定
對於裝載 Azure 區域中不同 Azure 可用性區域的設施，Microsoft 不會共用其間地理距離的任何相關資訊。 然而，某些客戶仍選擇以特定方式使用區域，以達到復原點目標 (RPO) 承諾值為零的 HA/DR 組合設定。 RPO 為零表示即使發生嚴重損壞修復，您也不應該遺失任何已認可的資料庫交易。 

> [!NOTE]
> 建議您在特定情況下才使用此類設定。 例如，當資料因安全性或合規性因素而不能離開 Azure 區域時，就可以使用。 

以下是此類設定的範例：

![在區域中結合高可用性與 DR](./media/sap-ha-availability-zones/combined_ha_dr_in_zones.png)

此設定需要考慮下列事項：

- 您應假設裝載可用性區域的設施之間的距離很大，或是您不能離開特定 Azure 區域。 可用性設定組無法部署到 Azure 可用性區域中。 若要彌補這一點，您可以使用 azure 鄰近放置群組（如 Azure 鄰近放置群組中所述），以提供 [SAP 應用程式的最佳網路延遲](sap-proximity-placement-scenarios.md)。
- 使用此架構時，您必須嚴密監視狀態，並嘗試將作用中的 DBMS 和 SAP 中央服務執行個體維持在已部署的應用程式層所在的區域中。 當 SAP 中央服務或 DBMS 執行個體進行容錯移轉時，您應確保能夠盡快手動容錯回復到部署 SAP 應用程式層的區域中。
- 您應在執行作用中 QA 應用程式執行個體的 VM 中預先安裝生產環境應用程式執行個體。
- 在區域性失敗情況下，請關閉 QA 應用程式實例，並改為啟動生產環境實例。 您必須使用應用程式實例的虛擬名稱，才能進行此工作。
- 針對 SAP 中央服務和 DBMS 層的容錯移轉叢集所使用的負載平衡器，必須是[標準 SKU Azure 負載平衡器](../../../load-balancer/load-balancer-standard-availability-zones.md)。 基本負載平衡器將無法跨區域運作。
- 您部署用以裝載 SAP 系統的 Azure 虛擬網路，會連同其子網路擴展至各個區域。 您的每個區域不需要個別的虛擬網路。
- 針對您部署的所有虛擬機器，您必須使用 [Azure 受控磁碟](https://azure.microsoft.com/services/managed-disks/)。 非受控磁碟不支援區域部署。
- Azure 進階儲存體和 [Ultra SSD 儲存體](../../disks-types.md#ultra-disk)不支援跨區域進行任何類型的儲存體複寫。 應用程式 (DBMS 或 SAP 中央服務) 必須複寫重要的資料。
- 共用的 sapmnt 目錄也是如此，包括共用磁碟 (Windows)、CIFS 共用 (Windows) 或 NFS 共用 (Linux)。 您必須使用可在區域之間複寫這些共用磁碟或共用的技術。 支援的技術如下：
    - 在 Windows 中，支援使用 SIOS Datakeeper 的叢集解決方案，如[在 Azure 中使用叢集共用磁碟於 Windows 容錯移轉叢集上進行 SAP ASCS/SCS 執行個體叢集處理](./sap-high-availability-guide-wsfc-shared-disk.md)中所說明。
    - 在 SUSE Linux 中，支援依據 [SUSE Linux Enterprise Server 上的 Azure VM 針對 NFS 提供的高可用性](./high-availability-guide-suse-nfs.md)所說明的方式建置的 NFS 共用。

  目前，使用 Microsoft 向外延展檔案伺服器的解決方案 (如[使用 SAP ASCS/SCS 執行個體的 Windows 容錯移轉叢集和檔案共用，為 SAP 高可用性準備 Azure 基礎結構](./sap-high-availability-infrastructure-wsfc-file-share.md)中所說明) 並不支援跨區域使用。
- 如果您建立 [SUSE Linux Pacemaker](./high-availability-guide-suse-pacemaker.md#create-azure-fence-agent-stonith-device) 叢集，並使用 SBD 裝置而非 Azure 隔離代理程式，則會使用第三個區域來裝載 SBD 裝置。 





## <a name="next-steps"></a>後續步驟
以下提供跨 Azure 可用性區域進行部署的後續步驟：

- [在 Azure 中使用叢集共用磁碟於 Windows 容錯移轉叢集上進行 SAP ASCS/SCS 執行個體叢集處理](./sap-high-availability-guide-wsfc-shared-disk.md)
- [使用 SAP ASCS/SCS 執行個體的 Windows 容錯移轉叢集和檔案共用，為 SAP 高可用性準備 Azure 基礎結構](./sap-high-availability-infrastructure-wsfc-file-share.md)