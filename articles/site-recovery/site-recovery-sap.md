---
title: 使用 Azure 網站恢復設置 SAP NetWeaver 災害復原
description: 瞭解如何使用 Azure 網站恢復為 SAP NetWeaver 設置災害復原。
author: sideeksh
manager: rochakm
ms.topic: how-to
ms.date: 11/27/2018
ms.openlocfilehash: 29acd1b00d23e4f1c2f241027dadbbb406e5e049
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "77190785"
---
# <a name="set-up-disaster-recovery-for-a-multi-tier-sap-netweaver-app-deployment"></a>設定多層式 SAP NetWeaver 應用程式部署的災害復原

大部分的大型與中型 SAP 部署都有某種形式的災害復原解決方案。 隨著移至應用程式 (如 SAP) 的核心商務程序愈來愈多，強固且可測試的災害復原解決方案愈形重要。 Azure Site Recovery 已經過測試並與 SAP 應用程式整合。 網站恢復超出了大多數本地災害復原解決方案的功能，擁有權總成本低於競爭解決方案。

使用 Site Recovery，您可以：
* 藉由將元件複寫到 Azure，啟用在內部部署環境執行之 SAP NetWeaver 和非 NetWeaver 生產應用程式的保護功能。
* 藉由將元件複寫到另一個 Azure 資料中心，啟用執行 Azure 之 SAP NetWeaver 和非 NetWeaver 生產應用程式的保護功能。
* 使用 Site Recovery 將您的 SAP 部署移轉至 Azure，來簡化雲端移轉。
* 通過按需創建生產克隆來測試 SAP 應用程式，簡化 SAP 專案升級、測試和原型設計。

您可以使用[Azure 網站恢復](site-recovery-overview.md)來保護 SAP NetWeaver 應用程式部署。 本文介紹使用網站恢復複製到另一個 Azure 資料中心時，在 Azure 上保護三層 SAP NetWeaver 部署的最佳做法。 本文介紹了受支援的方案和配置，以及如何執行容錯移轉（災害復原演練）和實際容錯移轉。

## <a name="prerequisites"></a>Prerequisites

在開始之前，請確定您了解如何執行下列工作：

* [將虛擬機器複寫至 Azure](azure-to-azure-walkthrough-enable-replication.md)
* [設計復原網路](site-recovery-azure-to-azure-networking-guidance.md)
* [執行測試容錯移轉至 Azure](azure-to-azure-walkthrough-test-failover.md)
* [執行容錯移轉到 Azure](site-recovery-failover.md)
* [複寫網域控制站](site-recovery-active-directory.md)
* [複製 SQL Server 實例](site-recovery-sql.md)

## <a name="supported-scenarios"></a>支援的案例

您可以使用 Site Recovery，在下列案例中實作災害復原解決方案：
* SAP 系統在一個 Azure 資料中心中運行，並且要將它們複製到另一個 Azure 資料中心（Azure 到 Azure 災害復原）。 
   如需詳細資訊，請參閱 [Azure 對 Azure 複寫架構](https://aka.ms/asr-a2a-architecture)。
* 本地的 VMware（或物理）伺服器上運行 SAP 系統。 此外，您還將 SAP 系統複製到 Azure 資料中心中的災害復原網站（VMware 到 Azure 災害復原）。 
   此案例需要一些額外的元件。 如需詳細資訊，請參閱 [VMware 對 Azure 複寫架構](https://aka.ms/asr-v2a-architecture)。
* 在超 V 本地運行 SAP 系統。 此外，您還將 SAP 系統複製到 Azure 資料中心中的災害復原網站（Hyper-V 到 Azure 災害復原）。
   此案例需要一些額外的元件。 如需詳細資訊，請參閱 [Hyper-V 對 Azure 複寫架構](https://aka.ms/asr-h2a-architecture)。

在本文中，我們使用**Azure 到 Azure**災害復原方案。 該方案顯示網站恢復的 SAP 災害復原功能。 由於 Site Recovery 複寫不限於特定應用程式，因此所述程序應也可以套用至其他案例。

### <a name="required-foundation-services"></a>必要的基礎服務
我們將在本文討論的案例中，部署下列基礎服務：
* Azure ExpressRoute 或 Azure VPN 閘道
* 在 Azure 中運行的至少一個 Azure 活動目錄網域控制站和 DNS 伺服器

我們建議您先建立此基礎結構，再部署 Site Recovery。

## <a name="reference-sap-application-deployment"></a>參考 SAP 應用程式部署

此參考體系結構在 Azure 上的 Windows 環境中運行 SAP NetWeaver，並且具有高可用性。 此體系結構部署時，具有特定的虛擬機器 （VM） 大小，您可以更改這些大小以滿足組織的需求。

![典型 SAP 部署模式的圖表](./media/site-recovery-sap/sap-netweaver_latest.png)

## <a name="disaster-recovery-considerations"></a>災害復原考量

對於災害復原，您必須能夠容錯移轉到次要區域。 每個層使用不同的策略來提供災害復原保護。

#### <a name="vms-running-sap-web-dispatcher-pools"></a>運行 SAP Web 調度程式池的 VM

Web 調度程式元件充當 SAP 應用程式伺服器之間 SAP 流量的負載等化器。 為了實現 Web 調度程式元件的高可用性，Azure 負載等化器實現了並行 Web 調度程式設置。 Web 調度程式使用迴圈配置在平衡器池中的可用 Web 調度器之間分配 HTTP（S） 流量。

#### <a name="vms-running-application-servers-pools"></a>運行應用程式伺服器池的 VM
SMLG 事務管理 ABAP 應用程式伺服器的登錄組。 它使用中央服務的消息伺服器中的負載平衡功能在 SAP 應用程式伺服器池之間分配工作負載，用於 SAPGUI 和 RFC 流量。 您可以使用網站恢復來複製此管理。

#### <a name="vms-running-sap-central-services-clusters"></a>運行 SAP 中央服務群集的 VM
此參考架構會在應用程式層中的虛擬機器上執行中央服務。 在單個 VM 中，中央服務是潛在的單點故障。 典型的部署和高可用性不是要求。

要實現高可用性解決方案，可以使用共用磁片群集或檔共用群集。 若要設定共用磁碟叢集的虛擬機器，請使用 Windows Server 容錯移轉叢集。 我們建議您使用雲見證作為仲裁見證。

 > [!NOTE]
 > 由於網站恢復不會複製雲見證，因此我們建議您在災害復原區域部署雲見證。

為了支援容錯移轉叢集環境[，SIOS 資料保持群集版本](https://azuremarketplace.microsoft.com/marketplace/apps/sios_datakeeper.sios-datakeeper-8)執行群集共用卷功能。 在函數中，SIOS 資料保持群集複製叢集節點擁有的獨立磁片。 由於 Azure 不支援本機共用磁片，因此它需要 SIOS 提供的解決方案。

還可以通過實現檔共用群集來處理群集。 [SAP](https://blogs.sap.com/2018/03/19/migration-from-a-shared-disk-cluster-to-a-file-share-cluster) 最近修改了中央服務部署模式，來透過 UNC 路徑存取 /sapmnt 全域目錄。 我們仍然建議您確保 /sapmnt UNC 共用高度可用。 您可以檢查中央服務實例。 使用 Windows 伺服器容錯移轉叢集與橫向擴展檔案伺服器 （SOFS） 和 Windows Server 2016 中的存儲空間直接 （S2D） 功能。

 > [!NOTE]
 > 網站恢復目前僅支援直接使用存儲空間的虛擬機器和 SIOS 資料保管人的無源節點的崩潰一致性點複製。


## <a name="more-disaster-recovery-considerations"></a>更多災害復原注意事項

可以使用網站恢復來協調跨 Azure 區域的完整 SAP 部署的容錯移轉。
以下是設置災害復原的步驟：

1. 複寫虛擬機器
1. 設計復原網路
1. 複寫網域控制站
1. 將資料複寫到基本層
1. 執行測試容錯移轉
1. 執行容錯移轉

以下是本示例中使用的每一層的災害復原建議。

 **SAP 層** | **建議**
 --- | ---
**SAP Web Dispatcher 集區** |  使用網站恢復進行複製 
**SAP 應用程式伺服器集區** |  使用網站恢復進行複製 
**SAP 中央服務叢集** |  使用網站恢復進行複製 
**Active Directory 虛擬機器** |  使用活動目錄複寫 
**SQL 資料庫伺服器** |  始終在複製時使用 SQL 伺服器

## <a name="replicate-virtual-machines"></a>複寫虛擬機器

若要開始將所有 SAP 應用程式虛擬機器都複寫至 Azure 災害復原資料中心，請遵循[將虛擬機器複寫至 Azure](azure-to-azure-walkthrough-enable-replication.md) 中的指引。

* 有關保護活動目錄和 DNS 的指導，[請參閱如何保護活動目錄和 DNS](site-recovery-active-directory.md)。

* 有關保護在 SQL Server 上運行的資料庫層的指導，瞭解如何[保護 SQL Server](site-recovery-sql.md)。

## <a name="networking-configuration"></a>網路設定

如果您使用靜態 IP 位址，您可以指定您想要虛擬機器使用的 IP 位址。 要設置 IP 位址，請訪問**計算和網路設置** > **網路介面卡**。

![示範如何在 Site Recovery 網路介面卡窗格設定私人 IP 位址的螢幕擷取畫面](./media/site-recovery-sap/sap-static-ip.png)


## <a name="create-a-recovery-plan"></a>建立復原計畫

復原方案支援在容錯移轉期間對多層式應用程式中的各層進行排序。 排序有助於維持應用程式的一致性。 當您為多層式 Web 應用程式建立復原方案時，請完成[使用 Site Recovery 建立復原方案](site-recovery-create-recovery-plans.md)中所述的步驟。

### <a name="add-virtual-machines-to-failover-groups"></a>將虛擬機器新增至容錯移轉群組

1. 通過添加應用程式伺服器、Web 調度程式和 SAP 中央服務 VM 來創建恢復計畫。
1. 選擇 **"自訂"** 以對 VM 進行分組。 預設情況下，所有 VM 都是組 1 的一部分。

### <a name="add-scripts-to-the-recovery-plan"></a>將指令碼新增至復原計畫
要使應用程式正常運行，可能需要在 Azure 虛擬機器上執行一些操作。 在容錯移轉後或測試容錯移轉期間執行這些操作。 您還可以自動執行一些容錯移轉後操作。 例如，更新 DNS 條目，並通過向恢復計畫添加相應的腳本來更改綁定和連接。

您可以通過選擇 **"部署到 Azure"** 將最常用的網站恢復腳本部署到 Azure 自動化帳戶中。 使用任何已發佈的腳本時，請按照腳本中的指南操作。

[![部署到 Azure](https://azurecomcdn.azureedge.net/mediahandler/acomblog/media/Default/blog/c4803408-340e-49e3-9a1f-0ed3f689813d.png)](https://aka.ms/asr-automationrunbooks-deploy)

1. 將操作前腳本添加到組 1 以容錯移轉 SQL Server 可用性組。 使用示例腳本中發佈的 ASR-SQL-FailoverAG 腳本。 按照腳本中的指南操作，並在腳本中適當地進行所需的更改。
1. 添加操作後腳本，將負載等化器附加到 Web 層（組 1）的失敗虛擬機器。 使用示例腳本中發佈的 ASR-AddSingleLoad平衡器腳本。 按照腳本中的指南操作，並根據需要在腳本中進行必要的更改。

![SAP 復原方案](./media/site-recovery-sap/sap_recovery_plan.png)


## <a name="run-a-test-failover"></a>執行測試容錯移轉

1. 在 Azure 入口網站中，選取您的復原服務保存庫。
1. 選取為 SAP 應用程式建立的復原計畫。
1. 選取 [測試容錯移轉]****。
1. 若要啟動測試容錯移轉程序，請選取復原點和 Azure 虛擬網路。
1. 當次要環境啟動時，即可執行您驗證。
1. 驗證完成後，通過選擇 **"清理測試容錯移轉"** 來清理容錯移轉環境。

如需詳細資訊，請參閱[在 Site Recovery 中測試容錯移轉至 Azure](site-recovery-test-failover-to-azure.md)。

## <a name="run-a-failover"></a>執行容錯移轉

1. 在 Azure 入口網站中，選取您的復原服務保存庫。
1. 選取為 SAP 應用程式建立的復原計畫。
1. 選取 [容錯移轉]****。
1. 若要啟動容錯移轉程序，請選取復原點。

如需詳細資訊，請參閱[在 Site Recovery 中容錯移轉](site-recovery-failover.md)。

## <a name="next-steps"></a>後續步驟
* 詳細瞭解如何使用網站恢復為 SAP NetWeaver 部署構建災害復原解決方案。 請參閱可下載的白皮書[SAP NetWeaver：使用網站恢復構建災害復原解決方案](https://aka.ms/asr_sap)。 白皮書討論了各種 SAP 體系結構的建議。 您可以在 Azure 上查看 SAP 受支援的應用程式和 VM 類型。 還有用於測試災害復原解決方案的計畫選項。
* 深入了解如何使用 Site Recovery [複寫其他工作負載](site-recovery-workload.md)。
