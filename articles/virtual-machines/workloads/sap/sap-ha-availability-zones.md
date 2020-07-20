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
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 03/05/2020
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: a7a92bef85cd4ee7530940a065135e88c7530781
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "78675614"
---
# <a name="sap-workload-configurations-with-azure-availability-zones"></a>使用 Azure 可用性區域的 SAP 工作負載設定
[Azure 可用性區域](https://docs.microsoft.com/azure/availability-zones/az-overview)是 Azure 提供的高可用性功能之一。 使用可用性區域可改善 Azure 上的 SAP 工作負載整體的可用性。 已功能已在部分 [Azure 區域](https://azure.microsoft.com/global-infrastructure/regions/)推出。 未來將可在更多區域提供此功能。

下圖顯示 SAP 高可用性的基本架構：

![標準高可用性組態](./media/sap-ha-availability-zones/standard-ha-config.png)

SAP 應用層會部署在一個 Azure[可用性設定組](https://docs.microsoft.com/azure/virtual-machines/windows/manage-availability)上。 為了讓 SAP 中央服務具有高可用性，您在個別可用性設定組中部署兩部 VM。 使用 Windows Server 容錯移轉叢集或 Pacemaker (Linux)，在發生基礎結構或軟體問題時作為具有自動容錯移轉的高可用性架構。 若要深入了解這些部署，請參閱︰

- [使用叢集共用磁片在 Windows 容錯移轉叢集上進行 SAP ASCS/SCS 實例叢集](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-guide-wsfc-shared-disk)
- [使用檔案共用於 Windows 容錯移轉叢集上進行 SAP ASCS/SCS 執行個體叢集處理](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-guide-wsfc-file-share)
- [SAP NetWeaver 在適用於 SAP 應用程式之 SUSE Linux Enterprise Server 上的 Azure VM 高可用性](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse)
- [SAP NetWeaver on Red Hat Enterprise Linux 的 Azure 虛擬機器高可用性](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel)

類似的架構同樣也適用於 SAP NetWeaver、S/4HANA 或混合式系統的 DBMS 層。 您部署主動/被動模式的 DBMS 層以及容錯移轉叢集解決方案，以保護免於基礎結構或軟體失敗。 容錯移轉叢集解決方案可以是 DBMS 特定容錯移轉架構、Windows Server 容錯移轉叢集或 Pacemaker。

若要使用 Azure 可用性區域部署相同的架構，您必須對上述架構進行某些變更。 本文將說明這些變更。

## <a name="considerations-for-deploying-across-availability-zones"></a>在可用性區域上進行部署的考量事項


使用可用性區域時請考量下列事項：

- 對於 Azure 區域內不同可用性區域之間的距離，並不提供相關保證。
- 可用性區域並非理想的 DR 解決方案。 天然災害會導致全球某些區域產生大範圍損害，包括電力基礎結構的嚴重損害。 不同區域之間的距離可能不足以構成適當的 DR 解決方案。
- 可用性區域間的網路延遲，並非在所有 Azure 區域中都相同。 在某些情況下，您可以跨不同區域部署及執行 SAP 應用程式層，因為從某個區域到作用中 DBMS VM 的網路延遲，是可接受的。 但在某些 Azure 區域中，作用中 DBMS VM 與部署在不同區域的 SAP 應用程式執行個體之間的延遲太高，對 SAP 商務程序而言是不可接受的。 在這類情況下，部署架構就必須要有適用於應用程式的主動/主動架構，或是跨區域的網路延遲過高時所適用的主動/被動架構。
- 決定要在何處使用可用性區域時，請以各區域間的網路延遲作為考量依據。 網路延遲在以下兩方面扮演重要角色：
    - 兩個需要同步複寫的 DBMS 執行個體之間的延遲。 網路延遲越高，就越有可能影響到您工作負載的延展性。
    - 在和作用中 DBMS 執行個體相同區域中執行 SAP 對話執行個體的 VM，以及位於另一個區域中的相似 VM 之間的網路延遲差異。 此差異擴大時，對商務程序和批次作業的執行時間也會隨之拉長，這取決於它們是否在和 DBMS 相同的區域中執行。

在可用性區域之間部署 Azure VM 以及在相同 Azure 區域內建立容錯移轉解決方案時，會有一些限制：

- 部署至 Azure 可用性區域時，必須使用 [Azure 受控磁碟](https://azure.microsoft.com/services/managed-disks/)。 
- 針對實體區域的區域列舉對應是依 Azure 訂用帳戶來決定。 如果您使用不同的訂用帳戶來部署 SAP 系統，則必須為每個訂用帳戶定義理想的區域。
- 除非您使用[Azure 鄰近放置群組](https://docs.microsoft.com/azure/virtual-machines/linux/co-location)，否則無法在 Azure 可用性區域中部署 azure 可用性設定組。 如何跨區域部署 SAP DBMS 層和中央服務的方式，同時使用可用性設定組來部署 SAP 應用層，而且仍然達到 Vm 的近近性，請參閱[Azure 鄰近放置群組，以取得 sap 應用程式的最佳網路延遲](sap-proximity-placement-scenarios.md)一文。 如果您不利用 Azure 鄰近放置群組，則需要選擇其中一個，做為虛擬機器的部署架構。
- 您無法使用 [Azure 基本 Load Balancer](https://docs.microsoft.com/azure/load-balancer/load-balancer-overview) 來建立以 Windows Server 容錯移轉叢集或 Linux Pacemaker 為基礎的容錯移轉叢集解決方案。 相反地，您必須使用[Azure STANDARD LOAD BALANCER SKU](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-availability-zones)。



## <a name="the-ideal-availability-zones-combination"></a>理想的可用性區域組合
在決定如何使用可用性區域之前，您必須先確認：

- Azure 區域 (Region) 的三個區域 (Zone) 之間的網路延遲。 這讓您可以選擇在跨區域網路流量中具有最低網路延遲的區域。
- 您所選區域內的 VM 對 VM 延遲，與您所選擇的兩個區域在網路延遲上的差異。
- 確認您需要部署的 VM 類型在您選取的兩個區域中是否適用。 某些 VM (尤其是 M 系列 VM) 可能會發生部分 SKU 僅適用於其中兩個區域 (共三個) 的情況。

## <a name="network-latency-between-and-within-zones"></a>區域之間和區域內的網路延遲
若要確認不同區域之間的延遲，您必須：

- 在全部三個區域中部署您想要用於 DBMS 執行個體的 VM SKU。 在執行此測量時，請確定已啟用 [Azure 加速網路](https://azure.microsoft.com/blog/maximize-your-vm-s-performance-with-accelerated-networking-now-generally-available-for-both-windows-and-linux/)。
- 當您找到具有最低網路延遲的兩個區域時，請部署 VM SKU 中的另外三個 VM，作為要跨三個可用性區域的應用程式層 VM。 在您所選取的兩個 DBMS 區域中，對兩個 DBMS VM 測量網路延遲。 
- 使用 **niping** 作為測量工具。 此為來自 SAP 的工具，相關說明請見 SAP 支援附註 [#500235](https://launchpad.support.sap.com/#/notes/500235) 和 [#1100926](https://launchpad.support.sap.com/#/notes/1100926/E)。 請將重點放在文件中的延遲測量命令上。 由於 **ping** 無法在 Azure 加速網路程式碼路徑中運作，因此不建議使用。

您不需要手動執行這些測試。 您可以找到 PowerShell 程式[可用性區域延遲測試](https://github.com/Azure/SAP-on-Azure-Scripts-and-Utilities/tree/master/AvZone-Latency-Test)，以自動化所述的延遲測試。 

根據測量結果和 VM SKU 在可用性區域中的可用情形，您必須做出某些決定：

- 定義適用於 DBMS 層的理想區域。
- 決定您是否想要根據區域中與跨區域的網路延遲差異，將作用中 SAP 應用程式層分布在一個、兩個或全部三個區域間。
- 從應用程式的觀點，決定您要部署主動/被動組態還是主動/主動組態。 (本文稍後將說明這些組態。)

在做出這些決定時，也請考量 SAP 附註 [#1100926](https://launchpad.support.sap.com/#/notes/1100926/E)中所提到的 SAP 網路延遲建議事項。

> [!IMPORTANT]
> 您所做的測量和決定皆僅適用於您在測量時所使用的 Azure 訂用帳戶。 如果您使用其他 Azure 訂用帳戶，則必須另行測量。 列舉區域的對應可能因 Azure 訂用帳戶的不同而有所差異。


> [!IMPORTANT]
> 前述測量在每個支援[可用性區域](https://docs.microsoft.com/azure/availability-zones/az-overview)的 Azure 區域中應會產生不同的結果。 即使您的網路延遲需求相同，您在不同的 Azure 區域中可能也需要採用不同的部署策略，因為不同區域之間的網路延遲可能有所差異。 在某些 Azure 區域中，三個不同區域間的網路延遲可能會大不相同。 在其他 Azure 區域中，三個不同區域間的網路延遲可能會較為一致。 區域間一定會有 1 到 2 毫秒的網路延遲，是不正確的指稱。 Azure 區域中各個可用性區域之間的網路延遲，是無法一般化的。

## <a name="activeactive-deployment"></a>主動/主動部署
此部署架構稱為主動/主動，因為您會在兩個或三個區域中部署作用中的 SAP 應用程式伺服器。 使用加入佇列複寫的 SAP 中央服務執行個體，會部署到兩個區域之間。 DBMS 層也是如此，會部署到和 SAP 中央服務相同的區域中。

在考量此設定時，您必須在您的區域中尋找能針對您的工作負載和同步 DBMS 複寫提供可接受的跨區域網路延遲的兩個可用性區域。 您也應確定，所選區域內的網路延遲，與跨區域網路延遲之間的差異，也不能太大。 這是因為，根據作業是在 DBMS 伺服器的區域內執行，還是跨區域執行，商務程序或批次作業的執行時間不應有太大的差異。 一些差異上可接受，但不能到達差的因數。

跨兩個區域的主動/主動部署的簡化結構描述如下所示：

![主動/主動區域部署](./media/sap-ha-availability-zones/active_active_zones_deployment.png)

此設定需要考慮下列事項：

- 不使用[Azure 鄰近放置群組](https://docs.microsoft.com/azure/virtual-machines/linux/co-location)，您會將 Azure 可用性區域視為所有 vm 的容錯和更新網域，因為無法在 Azure 可用性區域中部署可用性設定組。
- 如果您想要將 DBMS 層和中央服務的區域部署結合在一起，但想要使用應用層的 Azure 可用性設定組，您需要使用 azure 鄰近性群組，如[適用于 SAP 應用程式的最佳網路延遲](sap-proximity-placement-scenarios.md)一文所述。
- 針對 SAP 中央服務和 DBMS 層的容錯移轉叢集所使用的負載平衡器，必須是[標準 SKU Azure 負載平衡器](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-availability-zones)。 基本負載平衡器將無法跨區域運作。
- 您部署用以裝載 SAP 系統的 Azure 虛擬網路，會連同其子網路擴展至各個區域。 您的每個區域不需要個別的虛擬網路。
- 針對您部署的所有虛擬機器，您需要使用[Azure 受控磁碟](https://azure.microsoft.com/services/managed-disks/)。 非受控磁碟不支援區域部署。
- Azure 進階儲存體和 [Ultra SSD 儲存體](https://docs.microsoft.com/azure/virtual-machines/windows/disks-ultra-ssd)不支援跨區域進行任何類型的儲存體複寫。 應用程式 (DBMS 或 SAP 中央服務) 必須複寫重要的資料。
- 共用的 sapmnt 目錄也是如此，包括共用磁碟 (Windows)、CIFS 共用 (Windows) 或 NFS 共用 (Linux)。 您必須使用可在區域之間複寫這些共用磁碟或共用的技術。 支援的技術如下：
  - 在 Windows 中，支援使用 SIOS Datakeeper 的叢集解決方案，如[在 Azure 中使用叢集共用磁碟於 Windows 容錯移轉叢集上進行 SAP ASCS/SCS 執行個體叢集處理](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-guide-wsfc-shared-disk)中所說明。
  - 在 SUSE Linux 中，支援依據 [SUSE Linux Enterprise Server 上的 Azure VM 針對 NFS 提供的高可用性](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-nfs)所說明的方式建置的 NFS 共用。
    
    目前，使用 Microsoft 向外延展檔案伺服器的解決方案 (如[使用 SAP ASCS/SCS 執行個體的 Windows 容錯移轉叢集和檔案共用，為 SAP 高可用性準備 Azure 基礎結構](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-infrastructure-wsfc-file-share)中所說明) 並不支援跨區域使用。
- 第三個區域是用來在您建置 [SUSE Linux pacemaker 叢集](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-pacemaker#create-azure-fence-agent-stonith-device)或額外應用程式執行個體的情況下裝載 SBD 裝置。
- 若要達到重要商務程式的執行時間一致性，您可以嘗試使用 SAP 批次伺服器群組、SAP 登入群組或 RFC 群組，將特定批次作業和使用者導向至與作用中 DBMS 實例相同區域中的應用程式實例。 不過，在發生區域容錯移轉的情況下，您必須手動將這些群組移至在 VM 上執行的執行個體 (其位於與作用中 DB VM 相同的區域中)。  
- 您可以在每個區域中部署休眠對話方塊執行個體。 如此，當您的部分應用程式執行個體所使用的區域服務中斷時，就能立即恢復先前的資源容量。

> [!IMPORTANT]
> 在此主動/主動案例中，頻寬的額外費用會由 Microsoft 從04/01/2020 宣佈。 請參閱檔[頻寬定價詳細資料](https://azure.microsoft.com/pricing/details/bandwidth/)。 SAP 應用層與 SAP DBMS 層之間的資料傳輸相當耗費資源。 因此，主動/主動案例可能會對成本有相當的貢獻。 請繼續查看這篇文章以取得確切的成本 


## <a name="activepassive-deployment"></a>主動/被動部署
如果一個區域內的網路延遲與跨區域網路流量的延遲之間出現不可接受的差異，您可以部署從 SAP 應用程式層的觀點來看具有主動/被動特性的架構。 您可以定義*作用中*區域，在此區域中部署完整應用程式層，並且嘗試執行作用中 DBMS 和 SAP 中央服務執行個體。 使用此類設定時，您必須依據作業是否在作用中 DBMS 執行個體所在的區域中執行，確定商務程序和批次作業不會有太大的執行時間差異。

此架構的基本配置如下所示：

![主動/被動區域部署](./media/sap-ha-availability-zones/active_passive_zones_deployment.png)

此設定需要考慮下列事項：

- 可用性設定組無法部署到 Azure 可用性區域中。 為了補償這一點，您可以使用 azure 鄰近放置群組，如[適用于 SAP 應用程式的最佳網路延遲](sap-proximity-placement-scenarios.md)一文所述。
- 使用此架構時，您必須嚴密監視狀態，並嘗試將作用中的 DBMS 和 SAP 中央服務執行個體維持在已部署的應用程式層所在的區域中。 當 SAP 中央服務或 DBMS 執行個體進行容錯移轉時，您應確保能夠盡快手動容錯回復到部署 SAP 應用程式層的區域中。
- 針對 SAP 中央服務和 DBMS 層的容錯移轉叢集所使用的負載平衡器，必須是[標準 SKU Azure 負載平衡器](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-availability-zones)。 基本負載平衡器將無法跨區域運作。
- 您部署用以裝載 SAP 系統的 Azure 虛擬網路，會連同其子網路擴展至各個區域。 您的每個區域不需要個別的虛擬網路。
- 針對您部署的所有虛擬機器，您需要使用[Azure 受控磁碟](https://azure.microsoft.com/services/managed-disks/)。 非受控磁碟不支援區域部署。
- Azure 進階儲存體和 [Ultra SSD 儲存體](https://docs.microsoft.com/azure/virtual-machines/windows/disks-ultra-ssd)不支援跨區域進行任何類型的儲存體複寫。 應用程式 (DBMS 或 SAP 中央服務) 必須複寫重要的資料。
- 共用的 sapmnt 目錄也是如此，包括共用磁碟 (Windows)、CIFS 共用 (Windows) 或 NFS 共用 (Linux)。 您必須使用可在區域之間複寫這些共用磁碟或共用的技術。 支援的技術如下：
    - 在 Windows 中，支援使用 SIOS Datakeeper 的叢集解決方案，如[在 Azure 中使用叢集共用磁碟於 Windows 容錯移轉叢集上進行 SAP ASCS/SCS 執行個體叢集處理](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-guide-wsfc-shared-disk)中所說明。
    - 在 SUSE Linux 中，支援依據 [SUSE Linux Enterprise Server 上的 Azure VM 針對 NFS 提供的高可用性](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-nfs)所說明的方式建置的 NFS 共用。
    
  目前，使用 Microsoft 向外延展檔案伺服器的解決方案 (如[使用 SAP ASCS/SCS 執行個體的 Windows 容錯移轉叢集和檔案共用，為 SAP 高可用性準備 Azure 基礎結構](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-infrastructure-wsfc-file-share)中所說明) 並不支援跨區域使用。
- 第三個區域是用來在您建置 [SUSE Linux pacemaker 叢集](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-pacemaker#create-azure-fence-agent-stonith-device)或額外應用程式執行個體的情況下裝載 SBD 裝置。
- 您應將休眠 VM 部署到被動區域 (就 DBMS 的觀點來看)，以變在發生區域失敗時啟動應用程式資源。
    - [Azure Site Recovery](https://azure.microsoft.com/services/site-recovery/) 目前無法跨區域將作用中 VM 複寫到休眠 VM。 
- 您應投資於自動化功能，以便在某個區域失敗時自動在另一個區域中啟動 SAP 應用程式層。

## <a name="combined-high-availability-and-disaster-recovery-configuration"></a>結合高可用性和災害復原的設定
對於裝載 Azure 區域中不同 Azure 可用性區域的設施，Microsoft 不會共用其間地理距離的任何相關資訊。 然而，某些客戶仍選擇以特定方式使用區域，以達到復原點目標 (RPO) 承諾值為零的 HA/DR 組合設定。 這意味著，即使進行災害復原，您也不會失去任何已認可的資料庫交易。 

> [!NOTE]
> 建議您在特定情況下才使用此類設定。 例如，當資料因安全性或合規性因素而不能離開 Azure 區域時，就可以使用。 

以下是此類設定的範例：

![在區域中結合高可用性與 DR](./media/sap-ha-availability-zones/combined_ha_dr_in_zones.png)

此設定需要考慮下列事項：

- 您應假設裝載可用性區域的設施之間的距離很大，或是您不能離開特定 Azure 區域。 可用性設定組無法部署到 Azure 可用性區域中。 為了補償這一點，您可以使用 azure 鄰近放置群組，如[適用于 SAP 應用程式的最佳網路延遲](sap-proximity-placement-scenarios.md)一文所述。
- 使用此架構時，您必須嚴密監視狀態，並嘗試將作用中的 DBMS 和 SAP 中央服務執行個體維持在已部署的應用程式層所在的區域中。 當 SAP 中央服務或 DBMS 執行個體進行容錯移轉時，您應確保能夠盡快手動容錯回復到部署 SAP 應用程式層的區域中。
- 您應在執行作用中 QA 應用程式執行個體的 VM 中預先安裝生產環境應用程式執行個體。
- 當某個區域失敗時，請關閉 QA 應用程式執行個體，並改為啟動生產環境執行個體。 請注意，您必須使用應用程式執行個體的虛擬名稱才能達成此目的。
- 針對 SAP 中央服務和 DBMS 層的容錯移轉叢集所使用的負載平衡器，必須是[標準 SKU Azure 負載平衡器](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-availability-zones)。 基本負載平衡器將無法跨區域運作。
- 您部署用以裝載 SAP 系統的 Azure 虛擬網路，會連同其子網路擴展至各個區域。 您的每個區域不需要個別的虛擬網路。
- 針對您部署的所有虛擬機器，您需要使用[Azure 受控磁碟](https://azure.microsoft.com/services/managed-disks/)。 非受控磁碟不支援區域部署。
- Azure 進階儲存體和 [Ultra SSD 儲存體](https://docs.microsoft.com/azure/virtual-machines/windows/disks-ultra-ssd)不支援跨區域進行任何類型的儲存體複寫。 應用程式 (DBMS 或 SAP 中央服務) 必須複寫重要的資料。
- 共用的 sapmnt 目錄也是如此，包括共用磁碟 (Windows)、CIFS 共用 (Windows) 或 NFS 共用 (Linux)。 您必須使用可在區域之間複寫這些共用磁碟或共用的技術。 支援的技術如下：
    - 在 Windows 中，支援使用 SIOS Datakeeper 的叢集解決方案，如[在 Azure 中使用叢集共用磁碟於 Windows 容錯移轉叢集上進行 SAP ASCS/SCS 執行個體叢集處理](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-guide-wsfc-shared-disk)中所說明。
    - 在 SUSE Linux 中，支援依據 [SUSE Linux Enterprise Server 上的 Azure VM 針對 NFS 提供的高可用性](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-nfs)所說明的方式建置的 NFS 共用。

  目前，使用 Microsoft 向外延展檔案伺服器的解決方案 (如[使用 SAP ASCS/SCS 執行個體的 Windows 容錯移轉叢集和檔案共用，為 SAP 高可用性準備 Azure 基礎結構](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-infrastructure-wsfc-file-share)中所說明) 並不支援跨區域使用。
- 第三個區域是用來在您建置 [SUSE Linux pacemaker 叢集](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-pacemaker#create-azure-fence-agent-stonith-device)或額外應用程式執行個體的情況下裝載 SBD 裝置。





## <a name="next-steps"></a>後續步驟
以下提供跨 Azure 可用性區域進行部署的後續步驟：

- [在 Azure 中使用叢集共用磁碟於 Windows 容錯移轉叢集上進行 SAP ASCS/SCS 執行個體叢集處理](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-guide-wsfc-shared-disk)
- [使用 SAP ASCS/SCS 執行個體的 Windows 容錯移轉叢集和檔案共用，為 SAP 高可用性準備 Azure 基礎結構](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-infrastructure-wsfc-file-share)






