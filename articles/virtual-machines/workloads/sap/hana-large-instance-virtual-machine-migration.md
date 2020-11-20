---
title: 將 Azure (大型實例) 上的 SAP Hana 遷移至 Azure 虛擬機器 |Microsoft Docs
description: 如何將 Azure (大型實例) 上的 SAP Hana 遷移至 Azure 虛擬機器
services: virtual-machines-linux
documentationcenter: ''
author: bentrin
manager: juergent
editor: ''
ms.service: virtual-machines-linux
ms.subservice: workloads
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 02/11/2020
ms.author: bentrin
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 25eae9f9ba0e28a5aa069972c8c7d5eb2877545f
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/20/2020
ms.locfileid: "94967681"
---
# <a name="sap-hana-on-azure-large-instance-migration-to-azure-virtual-machines"></a>Azure 大型實例遷移至 Azure 虛擬機器的 SAP Hana
本文說明可能的 Azure 大型實例部署案例，並提供最短的轉換停機時間的規劃和遷移方法

## <a name="overview"></a>概觀
自2016年9月起，在 SAP Hana (的 Azure 大型實例公告) ，許多客戶都採用此硬體即服務供應專案來作為記憶體中的計算平臺。  在最近幾年，Azure VM 大小擴充功能與 HANA 向外延展部署的支援已超過大多數企業客戶的 ERP 資料庫容量需求。  我們會開始看到客戶，表示將其 SAP Hana 工作負載從實體伺服器遷移至 Azure Vm 的興趣。
本指南不是逐步設定檔。 它描述常見的部署模型，並提供規劃和遷移建議。  其目的是要將需要的考慮用於準備，以將轉換停機時間降到最低。

## <a name="assumptions"></a>假設
本文會做出以下假設：
- 唯一的考慮是在不需要大量軟體升級或修補的情況下，從 Hana 大型實例 (的) 遷移至 Azure VM 的同質 HANA 資料庫計算服務。 這些次要更新包括使用較新的作業系統版本或明確陳述的相關 SAP 附注所支援的 HANA 版本。 
- 所有更新/升級活動都需要在遷移之前或之後完成。  例如，SAP Hana 轉換為 MDC 部署的 MCOS。 
- 可提供最少停機時間的遷移方法是 SAP Hana 系統複寫。 其他遷移方法不屬於本檔的範圍。
- 本指南適用于 Rev3 和 Rev4 Sku。
- 在遷移期間，HANA 部署架構的主要維持不變。  也就是說，具有單一實例 DR 的系統會在目的地保持相同的方式。
- 客戶已複習並瞭解目標 () 架構的服務等級協定 (SLA) 。 
- Hli 和 Vm 之間的商業條款不同。 客戶應該監視其 Vm 的使用方式以進行成本管理。
- 客戶瞭解到，當 Vm 在共用但隔離的基礎結構上執行時，就是專用的計算平臺。
- 客戶已驗證目標 Vm 支援您預期的架構。 若要查看已通過 SAP Hana 部署認證的所有支援 VM Sku，請參閱 [SAP Hana 硬體目錄](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure)。
- 客戶已驗證設計和遷移計畫。
- 規劃嚴重損壞修復 VM 和主要網站。  在遷移後，客戶無法使用此資訊作為 Vm 上執行的主要網站的 DR 節點。
- 客戶已根據企業復原能力和合規性需求，將所需的備份檔案複製到目標 Vm。 使用 VM 可存取的備份，它允許在轉換期間進行時間點恢復。
- 針對 HSR HA，客戶需要針對 [SLES](./high-availability-guide-suse-pacemaker.md) 和 [RHEL](./high-availability-guide-rhel-pacemaker.md)的 SAP Hana HA 指南設定和設定 STONITH 裝置。  它未預先設定，就像是一種。
- 這種遷移方法不涵蓋具有 Optane 設定的移動 Sku。

## <a name="deployment-scenarios"></a>部署案例
下表摘要說明使用的一般部署模型。  您可以針對所有的一間案例遷移至 Azure Vm。  若要受益于提供互補的 Azure 服務，可能需要進行次要架構變更。

| 案例識別碼 | 的一間案例 | 逐字遷移至 VM？ | 備註 |
| --- | --- | --- | --- |
| 1 | [具有一個 SID 的單一節點](./hana-supported-scenario.md#single-node-with-one-sid) | 是 | - |
| 2 | [具有 MCOS 的單一節點](./hana-supported-scenario.md#single-node-mcos) | 是 | - |
| 3 | [使用儲存體複寫進行 DR 的單一節點](./hana-supported-scenario.md#single-node-with-dr-using-storage-replication) | 否 | Azure 虛擬平臺無法使用儲存體複寫，請將目前的 DR 解決方案變更為 HSR 或備份/還原 |
| 4 | [具有 DR (多用途) 使用儲存體複寫的單一節點](./hana-supported-scenario.md#single-node-with-dr-multipurpose-using-storage-replication) | 否 | Azure 虛擬平臺無法使用儲存體複寫，請將目前的 DR 解決方案變更為 HSR 或備份/還原 |
| 5 | [具有高可用性 STONITH 的 HSR](./hana-supported-scenario.md#hsr-with-stonith-for-high-availability) | 是 | 目標 Vm 沒有預先設定的 SBD。  選取並部署 STONITH 方案。  可能的選項： [RHEL](./high-availability-guide-rhel-pacemaker.md)、 [SLES](./high-availability-guide-suse-pacemaker.md)) 、SBD 支援 Azure 隔離代理程式 ( |
| 6 | [HA 搭配 HSR、DR 與儲存體複寫](./hana-supported-scenario.md#high-availability-with-hsr-and-dr-with-storage-replication) | 否 | 使用 HSR 或備份/還原來取代 DR 需求的儲存體複寫 |
| 7 | [主機自動容錯移轉 (1+1)](./hana-supported-scenario.md#host-auto-failover-11) | 是 | 使用 ANF 搭配 Azure Vm 的共用儲存體 |
| 8 | [具待命相應放大](./hana-supported-scenario.md#scale-out-with-standby) | 是 | BW/4HANA 搭配 M128s、M416s、M416ms Vm （僅使用 ANF 的儲存體） |
| 9 | [不具待命相應放大](./hana-supported-scenario.md#scale-out-without-standby) | 是 | BW/4HANA 搭配 M128s、M416s、M416ms Vm (不論是否使用 ANF 來存放)  |
| 10 | [使用儲存體複寫來向外擴充 DR](./hana-supported-scenario.md#scale-out-with-dr-using-storage-replication) | 否 | 使用 HSR 或備份/還原來取代 DR 需求的儲存體複寫 |
| 11 | [使用 HSR 進行 DR 的單一節點](./hana-supported-scenario.md#single-node-with-dr-using-hsr) | 是 | - |
| 12 | [單一節點 HSR 至 DR (成本優化) ](./hana-supported-scenario.md#single-node-hsr-to-dr-cost-optimized) | 是 | - |
| 13 | [使用 HSR 的 HA 和 DR](./hana-supported-scenario.md#high-availability-and-disaster-recovery-with-hsr) | 是 | - |
| 14 | [具有 HSR (成本優化) 的 HA 和 DR ](./hana-supported-scenario.md#high-availability-and-disaster-recovery-with-hsr-cost-optimized) | 是 | - |
| 15 | [使用 HSR 向外擴充 DR](./hana-supported-scenario.md#scale-out-with-dr-using-hsr) | 是 | BW/4HANA 與 M128s。 M416s、M416ms Vm (有或不使用 ANF 來儲存)  |


## <a name="source-hli-planning"></a>來源 (的) 規劃
當您上架一台伺服器時，Microsoft 服務管理和客戶都會進行計算、網路、儲存體和 OS 特定設定的規劃，以執行 SAP Hana 資料庫。  需要進行類似的規劃，才能遷移至 Azure VM。

### <a name="sap-hana-housekeeping"></a>SAP Hana 的維護 
這是很好的操作作法，可讓您整理資料庫內容，使不需要的過時資料或過時的記錄不會遷移至新的資料庫。  內務處理通常牽涉到刪除或封存舊的、已過期或非使用中的資料。  這些「資料防護」動作應該在非生產系統中進行測試，以在生產環境使用之前驗證其資料修剪有效性。

### <a name="allow-network-connectivity-for-new-vms-and-or-virtual-network"></a>允許新 Vm 和或虛擬網路的網路連線能力 
在客戶的部署中，已根據 [SAP Hana (大型實例) 網路架構](./hana-network-architecture.md)中所述的資訊來設定網路。 此外，也會以「在 Azure 中路由」一節所述的方式來進行網路流量路由。
- 在將新的 VM 設定為遷移目標時，如果將它放在現有的虛擬網路中，且該虛擬網路中的 IP 位址範圍已允許連線到，則不需要進一步的連線能力更新。
- 如果新的 Azure VM 位於新的 Microsoft Azure 虛擬網路、可能位於另一個區域中，並使用現有的虛擬網路對等互連，則可使用來自原始快速布建的 ExpressRoute 服務金鑰和資源識別碼，以允許此新虛擬網路 IP 範圍的存取。  與 Microsoft 服務管理協調，讓虛擬網路能夠進行連線。  注意：若要將應用程式和資料庫層之間的網路延遲降到最低，應用程式和資料庫層都必須位於相同的虛擬網路上。  

### <a name="existing-app-layer-availability-set-availability-zones-and-proximity-placement-group-ppg"></a>現有的應用層可用性設定組、可用性區域和鄰近位置群組 (PPG) 
目前的部署模型是為了滿足特定的服務等級目標而完成。  在這項移動中，請確定目標基礎結構會符合或超過設定的目標。  
更有可能的是，客戶的 SAP 應用程式伺服器放在可用性設定組中。  如果目前的部署服務層級很滿意， 
- 如果目標 VM 採用資訊名稱的主機名稱，則更新功能變數名稱服務 (DNS) 位址解析指向 VM 的 IP 將可運作，而不需要更新任何 SAP 設定檔
- 如果您不是使用 PPG，請務必將所有的應用程式和 DB 伺服器放在相同的區域中，以將網路延遲降至最低。
- 如果您使用的是 PPG，請參閱本檔的一節：「目的地規劃、可用性設定組、可用性區域和鄰近放置群組 (PPG) 」。

### <a name="storage-replication-discontinuance-process-if-used"></a>儲存體複寫 discontinuance 進程 (如果使用) 
如果使用「儲存體複寫」做為 DR 解決方案，則應該在 SAP 應用程式關閉後， (未排程的) 終止。  此外，最後 SAP Hana 目錄、記錄檔和資料備份都已複寫到遠端 DR 的存放磁片區。  這麼做是為了在實體伺服器到 Azure VM 轉換期間發生嚴重損壞的情況下的預防措施。

### <a name="data-backups-preservation-consideration"></a>資料備份保留考慮
在切換到 Azure VM 上的 SAP Hana 之後，如果需要，您的所有快照集型資料或記錄備份將無法輕易地在 VM 上存取或還原。 在早期轉換期間，在以 Azure 為基礎的備份建立足夠的歷程記錄來滿足時間點恢復需求之前，建議您在剪下之前、幾天或幾周的快照集以外，採取檔案層級備份。  將這些備份複製到新 SAP Hana VM 可存取的 Azure 儲存體帳戶。
除了備份演進內容，您也必須在需要回復時，立即存取 SAP 環境的完整備份。

### <a name="adjusting-system-monitoring"></a>調整系統監視 
客戶使用許多不同的工具來監視和傳送其 SAP 環境內系統的警示通知。  這項功能只是為了適當的動作而併入監視的變更，並視需要更新警示通知收件者。

### <a name="microsoft-operations-team-involvement"></a>Microsoft 營運小組參與 
根據現有的. 實例，從 Azure 入口網站中開啟票證。  建立支援票證之後，支援工程師會透過電子郵件與您聯繫。  

### <a name="engage-microsoft-account-team"></a>參與 Microsoft 帳戶團隊
規劃移近一年的年度續約時間，以將計算資源的不必要成本降至最低。 若要解除委任的，必須協調合約終止和單位的實際關機。

## <a name="destination-planning"></a>目的地規劃
若要建立新的基礎結構來取代現有的基礎結構，您必須先思考一下，以確保新的新增專案符合大型的配置。  以下是 contemplation 的一些重點。

### <a name="resource-availability-in-the-target-region"></a>目的地區域中的資源可用性 
目前 SAP 應用程式伺服器的部署區域通常與相關聯的 Hli 接近。  不過，Hli 的提供位置比可用的 Azure 區域少。  將實體速度遷移至 Azure VM 時，也可以「微調」所有相關服務的近距離以進行效能優化。  在這麼做時，有一個重要的考慮是確保所選區域具有所有必要的資源。  例如，特定 VM 系列的可用性或 Azure 區域的供應專案，以提供高可用性設定。

### <a name="virtual-network"></a>虛擬網路 
客戶需要選擇是否要在現有的虛擬網路中執行新的 HANA 資料庫，或建立新的 HANA 資料庫。  主要決定因素是 SAP 環境目前的網路設定。  此外，當基礎結構從一個區域到兩個區域的部署，並使用 PPG 時，它會強加架構變更。 如需詳細資訊，請參閱 Azure PPG 的文章，以 [取得 SAP 應用程式的最佳網路延遲](./sap-proximity-placement-scenarios.md)。   

### <a name="security"></a>安全性
新的 SAP Hana VM 是否會登陸至新的或現有的 vnet/子網，它代表需要保護的新商務關鍵服務。  必須針對這個新類別的服務評估和部署符合公司資訊安全性原則的存取控制。

### <a name="vm-sizing-recommendation"></a>VM 大小調整建議
這項遷移也是讓您的 HANA 計算引擎正確大小的機會。  您可以使用 HANA [系統檢視](https://help.sap.com/viewer/7c78579ce9b14a669c1f3295b0d8ca16/Cloud/3859e48180bb4cf8a207e15cf25a7e57.html) 搭配 hana Studio 來瞭解系統資源耗用量，以進行適當的大小調整以促進花費的效率。

### <a name="storage"></a>儲存體 
儲存體效能是影響 SAP 應用程式使用者體驗的其中一個因素。  根據指定的 VM SKU，已發佈的儲存體配置最少 [SAP Hana Azure 虛擬機器儲存體](./hana-vm-operations-storage.md)設定。 我們建議您檢查這些最小規格，並比較現有的速度系統統計資料，以確保新 HANA VM 有足夠的 IO 容量和效能。

如果您為新的 HANA VM 和其相關聯的服務設定 PPG，請提交支援票證以檢查並確定儲存體和 VM 的共置位置。 由於您的備份解決方案可能需要變更，因此應該也要進行儲存體成本以避免營運開支意外。

### <a name="storage-replication-for-disaster-recovery"></a>損毀修復的儲存體複寫
使用的是，會將儲存體複寫提供作為嚴重損壞修復的預設選項。 這項功能不是 Azure VM 上 SAP Hana 的預設選項。 請考慮 HSR、備份/還原或其他支援的解決方案，以滿足您的業務需求。

### <a name="availability-sets-availability-zones-and-proximity-placement-groups"></a>可用性設定組、可用性區域和鄰近位置群組 
為了縮短應用層和 SAP Hana 之間的距離，以至少保留網路延遲，新的資料庫 VM 和目前的 SAP 應用程式伺服器應放置在 PPG 中。 請參閱 [鄰近放置群組](./sap-proximity-placement-scenarios.md) ，以瞭解 Azure 可用性設定組和可用性區域如何搭配 PPG 進行 SAP 部署。
如果目標 HANA 系統的成員部署在多個 Azure 區域中，客戶應該清楚瞭解所選區域的延遲設定檔。 SAP 系統元件的放置是關於 SAP 應用程式與資料庫之間近端距離的最佳作法。  公用網域 [可用性區域延遲測試控管](https://github.com/Azure/SAP-on-Azure-Scripts-and-Utilities/tree/master/AvZone-Latency-Test) 可協助您更輕鬆地進行測量。  


### <a name="backup-strategy"></a>備份策略
許多客戶已經使用協力廠商的備份解決方案來進行 SAP Hana。  在此情況下，只需要設定其他受保護的 VM 和 HANA 資料庫。  如果電腦在遷移後已解除委任，則進行中的進度備份作業現在可以是未排程的。
VM 上的 SAP Hana Azure 備份現已正式推出。  如需詳細資訊，請參閱下列連結： Azure Vm 中的 [備份](../../../backup/backup-azure-sap-hana-database.md)、 [還原](../../../backup/sap-hana-db-restore.md)、 [管理](../../../backup/sap-hana-db-manage.md) SAP Hana 備份。

### <a name="dr-strategy"></a>DR 策略
如果您的服務等級目標能容納較長的復原時間，簡單備份至 blob 儲存體，並就地還原或還原至新的 VM，是最簡單且最便宜的 DR 策略。  
如同通常使用 HSR 完成 HANA DR 的大型實例平臺;在 Azure VM 上，HSR 也是最自然且原生 SAP Hana DR 解決方案。  不論來源部署是單一實例或叢集化，DR 區域中都需要來源基礎結構的複本。
此 DR 複本將會在主要的 VM 遷移完成後進行設定。  DR HANA DB 會將 VM 實例上的主要 SAP Hana 註冊為次要複寫網站。  

### <a name="sap-application-server-connectivity-destination-change"></a>SAP 應用程式伺服器連接目的地變更
HSR 遷移會導致新的 HANA DB 主機，因此會產生應用層的新 DB 主機名稱，因此必須修改 SAP 設定檔以反映新的主機名稱。  如果切換是藉由保留主機名稱的名稱解析完成，則不需要設定檔變更。

### <a name="operating-system"></a>作業系統
雖然在相同的發行層級上，還是會有 SLES 12 SP4 的作業系統映射（例如，）和 VM 的作業系統映射不相同。 客戶必須驗證一年中的必要套件、熱修正、修補程式、核心和安全性修正程式，才能在目標上安裝相同的套件。  支援使用 HSR 從較舊的 OS 複寫至具有較新 OS 版本的 VM。  藉由查看 [SAP 附注 2763388](https://launchpad.support.sap.com/#/notes/2763388)來確認特定支援的版本。

### <a name="new-sap-license-request"></a>新增 SAP 授權要求
針對新 HANA 系統要求新 SAP 授權的簡單呼叫，現在已遷移至 Vm。

### <a name="service-level-agreement-sla-differences"></a>服務等級協定 (SLA) 差異
這類作者想要知道在您與 Azure VM 之間的可用性 SLA 差異。  例如，叢集 Hli HA 配對提供99.99% 的可用性。 若要達到相同的 SLA，必須在可用性區域中部署 Vm。 本文 [說明相關](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_9/) 聯部署架構的可用性，讓客戶可以據以規劃其目標基礎結構。


## <a name="migration-strategy"></a>移轉策略
在本檔中，我們只會討論將從從中遷移至 Azure VM 的 HANA 系統複寫方法。  視部署的目標儲存體解決方案而定，此程式會稍有不同。 高階步驟如下所述。

### <a name="vm-with-premiumultra-disks-for-data"></a>具有 premium/ultra 磁片的 VM （適用于資料）
針對使用 premium 或 ultra 磁片部署的 Vm，標準 SAP Hana 系統複寫設定適用于設定 HSR。  [SAP 說明文章](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.04/099caa1959ce4b3fa1144562fa09e163.html)概述設定系統複寫、接管次要系統、容錯回復至主要系統，以及停用系統複寫所涉及的步驟。  基於遷移的目的，我們只需要進行設定、接管和停用複寫步驟即可。  

### <a name="vm-with-anf-for-data-and-log-volumes"></a>具有 ANF 資料和記錄磁片區的 VM
概括而言，必須將完整資料和記錄磁片區的最新的速度儲存體快照集複製到 Azure 儲存體，以供目標 HANA VM 存取及復原。  您可以使用任何原生 Linux 複製工具來完成複製程式。  

> [!IMPORTANT]
> 複製和資料傳輸可能需要數小時的時間，視 HANA 資料庫大小和網路頻寬而定。  大部分的複製程式都應該在主要 HANA 資料庫停機時間前完成。

### <a name="mcos-to-mdc-conversion"></a>MCOS 至 MDC 轉換
其中一個系統 (MCOS) 部署模型中的多個元件，是由我們的一些客戶所使用。  動機是要規避多個資料庫容器 (MDC) 早 SAP Hana 版本的儲存體快照集限制。  在 MCOS 模型中，有數個獨立的 SAP Hana 實例會堆疊在一個一次的 blade 中。  使用 HSR 進行遷移會正常運作，但會導致多個具有一個租使用者資料庫的 HANA Vm。  這種結束狀態的範圍會比客戶可能已讓習慣于的環境更忙碌。  在 SAP Hana 2.0 的預設部署為 MDC 時，有一個可行的替代方式，就是在 HSR 遷移之後，執行 [HANA 租使用者移動](https://launchpad.support.sap.com/#/notes/2472478) 。  此程式會將這些獨立的 HANA 資料庫合併為單一 HANA 容器中的 cotenants。  

### <a name="application-layer-consideration"></a>應用層考慮
資料庫伺服器會視為 SAP 系統的中心。  所有應用程式伺服器都應該位於 SAP Hana DB 附近。  在某些情況下，需要使用 PPG 的新用法時，可能需要將現有的應用程式伺服器重新放置到可能需要 HANA VM 的 PPG。  如果您已經準備好部署範本，建立新的應用程式伺服器會被視為更容易。  
如果現有的應用程式伺服器和新的 HANA VM 以最佳方式放置，除非需要額外的容量，否則不需要建立新的應用程式伺服器。  
如果建立新的基礎結構以增強服務可用性，現有的應用程式伺服器可能會變得不必要，且應關機並加以刪除。
如果目標 VM 主機名稱已變更，且與資訊主機名稱不同，則需要調整 SAP 應用程式伺服器設定檔，以指向新的主機。  如果只有 HANA DB IP 位址已變更，則需要 DNS 記錄更新，才能將連入連線導向至新的 HANA VM。

### <a name="acceptance-test"></a>接受度測試
雖然從的速度遷移至 VM，並不會變更資料庫內容（相較于異類遷移），但我們仍建議驗證新安裝程式的主要功能和效能層面。  

### <a name="cutover-plan"></a>切換計畫
雖然這項遷移作業很簡單，但卻牽涉到解除委任現有的 DB。  如果需要回復，請小心規劃將來源系統與其相關聯的內容和備份映射保持在一起。  良好的規劃提供 speedier 反轉。   


## <a name="post-migration"></a>移轉後
在我們安全地分離任何隨用隨的服務或連線能力，以確保資料完整性被保留時，不會執行遷移工作。  此外，關閉不必要的服務。  本節將呼叫幾個最重要的專案。

### <a name="decommissioning-the-hli"></a>解除委任
成功將 HANA DB 遷移至 Azure VM 之後，請確保在的資料庫上不會執行任何生產力的商務交易。  不過，讓一段時間執行的一段時間等於其本機備份保留時間，是確保 speedier 復原的安全做法（如有需要）。  然後，只應該解除委任。  客戶應該依履約透過聯繫其 Microsoft 代表，結束其對 Microsoft 的承諾。

### <a name="remove-any-proxy-ex-iptables-bigip-configured-for-hli"></a>移除任何 proxy (例如： Iptables、BIGIP) 為設定 
如果使用 IPTables 之類的 proxy 服務將內部部署流量路由傳送至從的途中，則在成功遷移至 VM 之後，就不再需要此服務。  不過，您應該保留此聯機服務，只要一段時間的 blade 分頁仍在待命。  只有在已完全解除委任完成後才關閉服務。

### <a name="remove-global-reach-for-hli"></a>移除的全球觸及範圍 
全球接觸會用來將客戶的 ExpressRoute 閘道連接到建立的 ExpressRoute 閘道。  它可讓客戶的內部部署流量直接抵達服務中心租使用者，而不需使用 proxy 服務。  在遷移之後，如果沒有，就不再需要此連接。  就像 IPTables proxy 服務的案例一樣，GlobalReach 也應該保持在，直到完成的完成。

### <a name="operating-system-subscription--movereuse"></a>作業系統訂用帳戶–移動/重複使用
當 VM 伺服器已勇敢面對考驗，並已解除委任 blade blade 時，可以取代或重複使用 OS 訂用帳戶，以避免重複支付作業系統授權。



## <a name="next-steps"></a>後續步驟
請參閱以下文章：
- [在 Azure 上 SAP Hana 基礎結構設定和作業](./hana-vm-operations.md)。
- [Azure 上的 SAP 工作負載：規劃和部署檢查清單](./sap-deployment-checklist.md)。
