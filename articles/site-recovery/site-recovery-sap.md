---
title: 使用 Azure Site Recovery 設定 SAP NetWeaver 嚴重損壞修復
description: 瞭解如何使用 Azure Site Recovery 設定 SAP NetWeaver 的嚴重損壞修復。
author: sideeksh
manager: rochakm
ms.topic: how-to
ms.date: 11/27/2018
ms.openlocfilehash: 29acd1b00d23e4f1c2f241027dadbbb406e5e049
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "77190785"
---
# <a name="set-up-disaster-recovery-for-a-multi-tier-sap-netweaver-app-deployment"></a>設定多層式 SAP NetWeaver 應用程式部署的災害復原

大部分的大型與中型 SAP 部署都有某種形式的災害復原解決方案。 隨著移至應用程式 (如 SAP) 的核心商務程序愈來愈多，強固且可測試的災害復原解決方案愈形重要。 Azure Site Recovery 已經過測試並與 SAP 應用程式整合。 Site Recovery 超過大部分內部部署嚴重損壞修復解決方案的功能，而且擁有的擁有權總成本會比競爭解決方案低。

使用 Site Recovery，您可以：
* 藉由將元件複寫到 Azure，啟用在內部部署環境執行之 SAP NetWeaver 和非 NetWeaver 生產應用程式的保護功能。
* 藉由將元件複寫到另一個 Azure 資料中心，啟用執行 Azure 之 SAP NetWeaver 和非 NetWeaver 生產應用程式的保護功能。
* 使用 Site Recovery 將您的 SAP 部署移轉至 Azure，來簡化雲端移轉。
* 藉由建立隨選生產複製來測試 SAP 應用程式，以簡化 SAP 專案升級、測試和原型設計。

您可以使用[Azure Site Recovery](site-recovery-overview.md)來保護 SAP NetWeaver 應用程式部署。 本文涵蓋當您使用 Site Recovery 複寫至另一個 Azure 資料中心時，在 Azure 上保護三層式 SAP NetWeaver 部署的最佳作法。 本文說明支援的案例和設定，以及如何執行測試容錯移轉（嚴重損壞修復演練）和實際的容錯移轉。

## <a name="prerequisites"></a>必要條件

在開始之前，請確定您了解如何執行下列工作：

* [將虛擬機器複寫至 Azure](azure-to-azure-walkthrough-enable-replication.md)
* [設計復原網路](site-recovery-azure-to-azure-networking-guidance.md)
* [執行測試容錯移轉至 Azure](azure-to-azure-walkthrough-test-failover.md)
* [執行容錯移轉到 Azure](site-recovery-failover.md)
* [複寫網域控制站](site-recovery-active-directory.md)
* [複寫 SQL Server 實例](site-recovery-sql.md)

## <a name="supported-scenarios"></a>支援的案例

您可以使用 Site Recovery，在下列案例中實作災害復原解決方案：
* 您的 SAP 系統在一個 Azure 資料中心內執行，而且您正在將它們複寫至另一個 Azure 資料中心（Azure 對 Azure 的嚴重損壞修復）。 
   如需詳細資訊，請參閱 [Azure 對 Azure 複寫架構](https://aka.ms/asr-a2a-architecture)。
* 您有在 VMware （或實體）伺服器上執行的 SAP 系統（在內部部署環境中）。 您也會將 SAP 系統複寫到 Azure 資料中心內的嚴重損壞修復網站（VMware 對 Azure 的嚴重損壞修復）。 
   此案例需要一些額外的元件。 如需詳細資訊，請參閱 [VMware 對 Azure 複寫架構](https://aka.ms/asr-v2a-architecture)。
* 您有在 Hyper-v 內部部署執行的 SAP 系統。 您也會將 SAP 系統複寫到 Azure 資料中心內的嚴重損壞修復網站（Hyper-v 到 Azure 的嚴重損壞修復）。
   此案例需要一些額外的元件。 如需詳細資訊，請參閱 [Hyper-V 對 Azure 複寫架構](https://aka.ms/asr-h2a-architecture)。

在本文中，我們會使用**azure 對 azure**的嚴重損壞修復案例。 此案例會顯示 Site Recovery 的 SAP 嚴重損壞修復功能。 由於 Site Recovery 複寫不限於特定應用程式，因此所述程序應也可以套用至其他案例。

### <a name="required-foundation-services"></a>必要的基礎服務
我們將在本文討論的案例中，部署下列基礎服務：
* Azure ExpressRoute 或 Azure VPN 閘道
* 至少一個 Azure Active Directory 網域控制站和 DNS 伺服器，在 Azure 中執行

我們建議您先建立此基礎結構，再部署 Site Recovery。

## <a name="reference-sap-application-deployment"></a>參考 SAP 應用程式部署

此參考架構會在具有高可用性的 Azure 上的 Windows 環境中執行 SAP NetWeaver。 此架構會以您可以變更的特定虛擬機器（VM）大小進行部署，以符合您組織的需求。

![典型 SAP 部署模式的圖表](./media/site-recovery-sap/sap-netweaver_latest.png)

## <a name="disaster-recovery-considerations"></a>災害復原考量

針對嚴重損壞修復，您必須能夠故障切換到次要區域。 每一層都使用不同的策略來提供嚴重損壞修復保護。

#### <a name="vms-running-sap-web-dispatcher-pools"></a>執行 SAP Web 發送器集區的 Vm

Web 發送器元件可作為 sap 應用程式伺服器之間 SAP 流量的負載平衡器。 為了達到 Web Dispatcher 元件的高可用性，Azure Load Balancer 會執行平行 Web 發送器設定。 Web 發送器會針對平衡器集區中可用的 Web 發送器間的 HTTP （S）流量散發，使用迴圈配置資源設定。

#### <a name="vms-running-application-servers-pools"></a>執行應用程式伺服器集區的 Vm
SMLG 交易會管理 ABAP 應用程式伺服器的登入群組。 它會使用中央服務之訊息伺服器內的負載平衡功能，在 SAP 應用程式伺服器集區間散發工作負載，以進行 SAPGUIs 和 RFC 流量。 您可以使用 Site Recovery 來複寫此管理。

#### <a name="vms-running-sap-central-services-clusters"></a>執行 SAP 中央服務叢集的 Vm
此參考架構會在應用程式層中的虛擬機器上執行中央服務。 在單一 VM 中，中央服務是潛在的單一失敗點。 一般部署和高可用性不是需求。

若要執行高可用性解決方案，您可以使用共用磁片叢集或檔案共用叢集。 若要設定共用磁碟叢集的虛擬機器，請使用 Windows Server 容錯移轉叢集。 我們建議您使用雲端見證作為仲裁見證。

 > [!NOTE]
 > 由於 Site Recovery 不會複寫雲端見證，因此建議您在嚴重損壞修復區域中部署雲端見證。

為了支援容錯移轉叢集環境， [SIOS DataKeeper Cluster Edition](https://azuremarketplace.microsoft.com/marketplace/apps/sios_datakeeper.sios-datakeeper-8)會執行叢集共用磁片區功能。 在此函數中，SIOS DataKeeper 叢集會複寫叢集節點所擁有的獨立磁片。 因為 Azure 原本就不支援共用磁片，所以它需要 SIOS 所提供的解決方案。

您也可以藉由執行檔案共用叢集來處理群集。 [SAP](https://blogs.sap.com/2018/03/19/migration-from-a-shared-disk-cluster-to-a-file-share-cluster) 最近修改了中央服務部署模式，來透過 UNC 路徑存取 /sapmnt 全域目錄。 我們仍然建議您確保/sapmnt UNC 共用具有高可用性。 您可以檢查中央服務實例。 使用 Windows Server 容錯移轉叢集搭配 Scale Out 檔案伺服器（SOFS）和 Windows Server 2016 中的儲存空間直接存取（S2D）功能。

 > [!NOTE]
 > Site Recovery 目前僅支援使用儲存空間直接存取和 SIOS Datakeeper 之被動節點的虛擬機器的當機時保持一致點複寫。


## <a name="more-disaster-recovery-considerations"></a>更多嚴重損壞修復考慮

您可以使用 Site Recovery 來協調跨 Azure 區域的完整 SAP 部署容錯移轉。
以下是設定嚴重損壞修復的步驟：

1. 複寫虛擬機器
1. 設計復原網路
1. 複寫網域控制站
1. 將資料複寫到基本層
1. 執行測試容錯移轉
1. 執行容錯移轉

以下是此範例中所使用之每一層的嚴重損壞修復建議。

 **SAP 層** | **建議**
 --- | ---
**SAP Web Dispatcher 集區** |  使用 Site Recovery 進行複寫 
**SAP 應用程式伺服器集區** |  使用 Site Recovery 進行複寫 
**SAP 中央服務叢集** |  使用 Site Recovery 進行複寫 
**Active Directory 虛擬機器** |  使用 Active directory 複寫 
**SQL Database 伺服器** |  使用 SQL Server Always On 複寫

## <a name="replicate-virtual-machines"></a>複寫虛擬機器

若要開始將所有 SAP 應用程式虛擬機器都複寫至 Azure 災害復原資料中心，請遵循[將虛擬機器複寫至 Azure](azure-to-azure-walkthrough-enable-replication.md) 中的指引。

* 如需保護 Active Directory 和 DNS 的指引，請瞭解[如何保護 Active Directory 和 dns](site-recovery-active-directory.md)。

* 如需保護在 SQL Server 上執行之資料庫層的指引，請瞭解[如何保護 SQL Server](site-recovery-sql.md)。

## <a name="networking-configuration"></a>網路設定

如果您使用靜態 IP 位址，您可以指定您想要虛擬機器使用的 IP 位址。 若要設定 IP 位址，請移至 [**計算和網路設定**] [  >  **網路介面卡**]。

![示範如何在 Site Recovery 網路介面卡窗格設定私人 IP 位址的螢幕擷取畫面](./media/site-recovery-sap/sap-static-ip.png)


## <a name="create-a-recovery-plan"></a>建立復原計畫

復原方案支援在容錯移轉期間對多層式應用程式中的各層進行排序。 排序有助於維持應用程式的一致性。 當您為多層式 Web 應用程式建立復原方案時，請完成[使用 Site Recovery 建立復原方案](site-recovery-create-recovery-plans.md)中所述的步驟。

### <a name="add-virtual-machines-to-failover-groups"></a>將虛擬機器新增至容錯移轉群組

1. 新增應用程式伺服器、web 發送器和 SAP 中央服務 Vm，以建立復原方案。
1. 選取 [**自訂**] 以群組 vm。 根據預設，所有 Vm 都是群組1的一部分。

### <a name="add-scripts-to-the-recovery-plan"></a>將指令碼新增至復原計畫
若要讓您的應用程式正確運作，您可能需要在 Azure 虛擬機器上執行一些作業。 在容錯移轉之後或在測試容錯移轉期間執行這些作業。 您也可以自動化某些容錯移轉後的作業。 例如，更新 DNS 專案，並藉由將對應的腳本新增至復原方案來變更系結和連線。

您可以選取 [**部署至 Azure**]，將最常使用的 Site Recovery 腳本部署到您的 Azure 自動化帳戶。 當您使用任何已發佈的腳本時，請遵循腳本中的指引。

[![部署至 Azure](https://azurecomcdn.azureedge.net/mediahandler/acomblog/media/Default/blog/c4803408-340e-49e3-9a1f-0ed3f689813d.png)](https://aka.ms/asr-automationrunbooks-deploy)

1. 將前置動作腳本新增至群組1，以故障切換 SQL Server 可用性群組。 使用範例腳本中發佈的 ASR-SQL Asr-sql-failoverag 腳本。 遵循腳本中的指導方針，並適當地在腳本中進行必要的變更。
1. 新增動作後腳本，以將負載平衡器連結至 Web 層的故障 over 虛擬機器（群組1）。 使用在範例腳本中發佈的 ASR-AddSingleLoadBalancer 腳本。 遵循腳本中的指導方針，並視需要在腳本中進行必要的變更。

![SAP 復原方案](./media/site-recovery-sap/sap_recovery_plan.png)


## <a name="run-a-test-failover"></a>執行測試容錯移轉

1. 在 Azure 入口網站中，選取您的復原服務保存庫。
1. 選取為 SAP 應用程式建立的復原計畫。
1. 選取 [測試容錯移轉]****。
1. 若要啟動測試容錯移轉程序，請選取復原點和 Azure 虛擬網路。
1. 當次要環境啟動時，即可執行您驗證。
1. 當驗證完成時，請選取 [**清除測試容錯移轉**] 來清除容錯移轉環境。

如需詳細資訊，請參閱[在 Site Recovery 中測試容錯移轉至 Azure](site-recovery-test-failover-to-azure.md)。

## <a name="run-a-failover"></a>執行容錯移轉

1. 在 Azure 入口網站中，選取您的復原服務保存庫。
1. 選取為 SAP 應用程式建立的復原計畫。
1. 選取 [容錯移轉]****。
1. 若要啟動容錯移轉程序，請選取復原點。

如需詳細資訊，請參閱[在 Site Recovery 中容錯移轉](site-recovery-failover.md)。

## <a name="next-steps"></a>後續步驟
* 深入瞭解如何使用 Site Recovery 建立 SAP NetWeaver 部署的嚴重損壞修復解決方案。 請參閱可下載的白皮書[SAP NetWeaver：使用 Site Recovery 建立嚴重損壞修復解決方案](https://aka.ms/asr_sap)。 本白皮書將討論各種 SAP 架構的建議。 您可以看到 Azure 上的 SAP 支援的應用程式和 VM 類型。 另外還有用於測試嚴重損壞修復解決方案的計畫選項。
* 深入了解如何使用 Site Recovery [複寫其他工作負載](site-recovery-workload.md)。
