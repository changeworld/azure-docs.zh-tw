---
title: 將 Azure （大型實例）上的 SAP Hana 遷移至 Azure 虛擬機器 |Microsoft Docs
description: 如何將 Azure （大型實例）上的 SAP Hana 遷移至 Azure 虛擬機器
services: virtual-machines-linux
documentationcenter: ''
author: saghorpa
manager: gwallace
editor: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 02/11/2020
ms.author: saghorpa
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 2dd01bf60104939fcf1f9bd1ccec0e7d72710041
ms.sourcegitcommit: 812bc3c318f513cefc5b767de8754a6da888befc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/12/2020
ms.locfileid: "77154916"
---
# <a name="sap-hana-on-azure-large-instance-migration-to-azure-virtual-machines"></a>將 Azure 大型實例遷移至 Azure 虛擬機器 SAP Hana
本文說明可能的 Azure 大型實例部署案例，並以最小化的轉換停機時間提供規劃和遷移方法

## <a name="overview"></a>概觀
自從2016年9月的「適用于 SAP Hana 的 Azure 大型實例」（今年）公告之後，許多客戶都已採用此硬體即服務來處理其記憶體內部計算平臺。  最近幾年來，與 HANA 向外延展部署模型支援結合的 Azure VM 擴充性延伸已超過大多數企業客戶的 ERP 資料庫容量需求。  我們會開始看到客戶表示想要將其 SAP Hana 工作負載從實體伺服器遷移至 Azure Vm。
本指南不是逐步設定檔，而是說明常見的部署模型和供應專案規劃，並針對準備將轉換停機時間降到最低的必要考慮，提出建議。

## <a name="assumptions"></a>假設
本文會做出以下假設：
- 唯一的重點是，同質 HANA 資料庫計算服務從 Hana 大型實例（B-HLI）遷移至 Azure VM，而不需要大量軟體升級或修補。 這些次要更新包括使用較新的 OS 版本，或是相關 SAP 附注所支援的 HANA 版本。 
- 所有更新/升級活動（如有需要）必須在遷移之前或之後執行。  例如，SAP Hana MCOS 轉換成 MDC 部署。 
- 提供最少停機時間的遷移方法是 SAP Hana 系統複寫。 其他遷移方法不屬於本檔的範圍。
- 這適用于 Rev3 和 Rev4 的兩個 Sku。
- 在遷移期間，HANA 部署架構主要維持不變。  也就是說，具有一個單一實例 DR 的系統在目的地上會維持相同的方式。
- 客戶已審查並瞭解目標（為）架構的服務等級協定（SLA）。 
- 由於 Hli 和 Vm 之間的商業詞彙差異，客戶應該監視其 Vm 的使用方式，以進行成本管理。
- 客戶瞭解並認知該 B-HLI 是專用的計算平臺。 不過，Vm 會在共用基礎結構上執行，這是安全的，並與其他租使用者隔離。
- 客戶已驗證目標 Vm 支援您想要的架構。 若要查看所有 SAP Hana 部署認證的受支援 VM Sku，請檢查[SAP Hana 硬體目錄](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure)。
- 客戶已驗證設計和遷移計畫。
- 規劃嚴重損壞修復節點以及主要網站。  在遷移之後，客戶將無法針對在 Vm 上執行的主要網站使用 [：] DR 節點。
- 客戶根據企業的復原能力和合規性需求，將所需的備份檔案複製到目標 Vm。 這是為了涵蓋轉換期間的時間點復原需求。
- 針對 HSR HA，客戶必須根據[SLES](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-pacemaker)和[RHEL](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-pacemaker)的 SAP Hana HA 指南來設定 STONITH 裝置。  其未預先設定，就像是的一種情況。
- 這種遷移方法並不涵蓋 Optane 設定的面板 Sku。

## <a name="deployment-scenarios"></a>部署案例
下表摘要說明與 B-HLI 客戶的一般部署模型。  可能的話，您可以將所有的 Azure Vm 遷移至 Azure Vm。  有些案例可能需要進行次要的架構修改，以取得目前 Azure 服務供應專案的優點。

| 案例識別碼 | B-HLI 案例 | 要逐字遷移至 VM 嗎？ | 備註 |
| --- | --- | --- | --- |
| 1 | [具有一個 SID 的單一節點](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario#single-node-with-one-sid) | 是 | - |
| 2 | [具有 MCOS 的單一節點](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario#single-node-mcos) | 是 | - |
| 3 | [具有使用儲存體複寫之 DR 的單一節點](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario#single-node-with-dr-using-storage-replication) | 否 | Azure 虛擬平臺無法使用儲存體複寫，請將目前的 DR 解決方案變更為 HSR 或備份/還原 |
| 4 | [具有 DR （多用途）的單一節點（使用儲存體複寫）](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario#single-node-with-dr-multipurpose-using-storage-replication) | 否 | Azure 虛擬平臺無法使用儲存體複寫，請將目前的 DR 解決方案變更為 HSR 或備份/還原 |
| 5 | [具有 STONITH 的 HSR 以提供高可用性](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario#hsr-with-stonith-for-high-availability) | 是 | 未預先設定目標 Vm 的 SBD。  選取並部署 STONITH 解決方案。  可能的選項： Azure 隔離代理程式（支援[RHEL](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-pacemaker)、 [SLES](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-pacemaker)）、SBD |
| 6 | [HA 搭配 HSR、DR 與儲存體複寫](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario#high-availability-with-hsr-and-dr-with-storage-replication) | 否 | 使用 HSR 或備份/還原來取代 DR 需求的儲存體複寫 |
| 7 | [主機自動容錯移轉（1 + 1）](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario#host-auto-failover-11) | 是 | 使用 Azure Vm 的共用儲存體及 |
| 8 | [使用待命相應放大](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario#scale-out-with-standby) | 是 | BW/4HANA with M128s、M416s、M416ms Vm 僅使用及作為儲存體 |
| 9 | [無待命相應放大](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario#scale-out-without-standby) | 是 | BW/4HANA 與 M128s、M416s、M416ms Vm （不論是否使用及來儲存） |
| 10 | [使用儲存體複寫以 DR 進行相應放大](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario#scale-out-with-dr-using-storage-replication) | 否 | 使用 HSR 或備份/還原來取代 DR 需求的儲存體複寫 |
| 11 | [具有使用 HSR 之 DR 的單一節點](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario#single-node-with-dr-using-hsr) | 是 | - |
| 12 | [單一節點 HSR 至 DR （成本優化）](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario#single-node-hsr-to-dr-cost-optimized) | 是 | - |
| 13 | [具有 HSR 的 HA 和 DR](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario#high-availability-and-disaster-recovery-with-hsr) | 是 | - |
| 14 | [HA 和 DR 與 HSR （成本優化）](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario#high-availability-and-disaster-recovery-with-hsr-cost-optimized) | 是 | - |
| 15 | [使用 HSR 相應放大 DR](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario#scale-out-with-dr-using-hsr) | 是 | BW/4HANA 與 M128s。 M416s、M416ms Vm （不論是否使用及來儲存） |


## <a name="source-hli-planning"></a>來源（B-HLI）規劃
當上線伺服器上線時，Microsoft 服務管理和客戶都會針對執行 SAP Hana 資料庫的計算、網路、儲存體和 OS 特定設定進行規劃。  需要進行類似的規劃，才能遷移至 Azure VM。

### <a name="sap-hana-housekeeping"></a>SAP Hana 維護 
在遷移 HANA 資料庫之前，最好先整理資料庫內容，使不想要的過時資料或過時的記錄不會遷移到新的資料庫。  維護通常牽涉到刪除或封存舊、過期或非使用中的資料。  因此，應該在非生產系統中測試這些「資料防護」動作，以在生產環境使用之前驗證其資料修剪有效性。

### <a name="allow-network-connectivity-for-new-vms-and-or-virtual-network"></a>允許新 Vm 和或虛擬網路的網路連線 
在客戶的一間部署中，已根據[SAP Hana （大型實例）網路架構](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-network-architecture)一文中所述的資訊，建立來自 Azure vnet 的網路連線能力。 此外，網路流量路由會以 Azure 中的路由一節中所述的方式來完成。
1. 為了將新的 VM 設定為遷移的 HSR 目標，如果將新的 Azure VM 放在具有 IP 位址範圍的現有虛擬網路中，而該位置已經有連線到的 [配置] 節點的許可權，則不需要進一步的連線更新
2. 如果將新的 Azure VM 放在新的虛擬網路（可能位於另一個區域），並使用現有的虛擬網路對等互連，則可以使用來自原始/布建的 ExpressRoute 服務金鑰和資源識別碼，以允許存取這個新的虛擬網路 IP 範圍。  與 Microsoft 服務管理協調，讓虛擬網路能夠連接。  注意：若要將應用程式和資料庫層之間的網路延遲降至最低，應用程式和資料庫層都必須位於相同的虛擬網路上。  

### <a name="existing-app-layer-availability-set-availability-zones-and-proximity-placement-group"></a>現有的應用層可用性設定組、可用性區域和近接位置群組
目前的部署模型是為了滿足特定的服務等級目標而完成。  在此移動中，請確定目標基礎結構將符合或超過設定的目標。  
較不可能的是，客戶的 SAP 應用程式伺服器會放在可用性設定組中。  如果目前的部署服務層級令人滿意， 
- 如果藉由交換 DB 主機名稱的 Ip 以透過名稱解析來取代 SAP Hana 伺服器，則不需要執行任何進一步的動作。  
- 如果您不是使用 PPG，請務必將所有的應用程式和 DB 伺服器放在相同的區域，以將網路延遲降至最低。
- 如果您使用的是 PPG，請參閱本檔的「目的地規劃」、「可用性設定組」、「可用性區域和「鄰近性放置群組」（PPG）一節

### <a name="storage-replication-discontinuance-process-if-used"></a>儲存體複寫 discontinuance 程式（若已使用）
如果將存放裝置複寫當做 DR 解決方案使用，則在關閉 SAP 應用程式之後，必須終止複寫（非排程），而且最後一個 SAP Hana 目錄、記錄檔、資料備份已複寫到遠端存放裝置磁片區。  此動作是一種預防性技巧，因為在實體到 Azure VM 轉換期間發生嚴重損壞的情況下，我們在 DR 上有目前的資料庫。

### <a name="data-backups-preservation-consideration"></a>資料備份保留考慮
在轉換至 Azure VM 上的 SAP Hana 之後，您可以視需要輕鬆地在 VM 上存取或還原所有以快照集為基礎的資料或記錄備份。 在早期轉換期間，在以 Azure 為基礎的備份建立足夠的歷程記錄以滿足時間點復原需求之前，我們建議您在轉換之前的快照集、數天/數周，取得檔案層級備份。  將這些備份複製到新 SAP Hana VM 可存取的 Azure 儲存體帳戶。 除了備份的？內容之外，在需要復原的情況下，讓 SAP 環境的完整備份隨時可供存取，也是很謹慎的作法。

### <a name="adjusting-system-monitoring"></a>調整系統監視 
客戶可以使用許多不同的工具來監視和傳送其 SAP 環境內系統的警示通知。  此專案只是在新增新的 VM 時，適當動作的簡單呼叫，其中包含監視的任何調整，並視需要更新警示通知收件者。

### <a name="microsoft-operations-team-involvement"></a>Microsoft 營運小組參與 
從現有的 B-HLI 實例上的 Azure 入口網站基底開啟票證。  建立支援票證之後，支援工程師將會透過電子郵件與您聯繫。  

### <a name="engage-microsoft-account-team"></a>參與 Microsoft 帳戶小組
將遷移計畫接近的年度合約更新時間，以將計算資源的費用降至最低。 若要解除委任 [的？] 分頁，必須協調合約終止和單位的實際關閉。

## <a name="destination-planning"></a>目的地規劃
建立一個新的基礎結構來取代現有的基礎結構，需要一些思考，以確保新的新增專案會符合大型的配置。  以下是 contemplation 的一些重點。

### <a name="resource-availability-in-the-target-region"></a>目的地區域中的資源可用性 
目前的 SAP 應用程式伺服器部署區域通常與相關聯的 Hli 相近。  不過，Hli 所提供的位置比可用的 Azure 區域少。  在從實體的中遷移至 Azure VM 時，它提供了「微調」所有相關服務的近距離以進行效能優化的機會。  在這麼做時，主要的考慮是確保所選的區域具有相關資源。  例如，針對高可用性考慮，特定 VM 系列或 Azure 區域供應專案的可用性。

### <a name="virtual-network"></a>虛擬網路 
基礎結構架構設計人員將會選擇是否要在現有的 SAP 應用程式虛擬網路內執行新的 HANA 資料庫，或建立一個新的。  主要的決定因素是 SAP 環境的目前網路版面配置。  例如，在考慮部署基礎結構變更時，從一個區域到兩個區域的部署，並利用 PPG。 這項可用性增強功能會改變架構。 如需詳細資訊，請參閱[AZURE PPG 一文，以瞭解 SAP 應用程式的最佳網路延遲](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-proximity-placement-scenarios)。   

### <a name="security"></a>安全性
無論新的 SAP Hana VM 登陸新的或現有的 vnet/子網，它都代表需要保護的新商務關鍵服務。  適當的存取控制符合此新服務類別的公司資訊安全性原則，應進行評估和部署。 

### <a name="vm-sizing-recommendation"></a>VM 大小調整建議
這種遷移也是適當調整 HANA 計算引擎大小的機會。  其中一個可將 HANA[系統檢視](https://help.sap.com/viewer/7c78579ce9b14a669c1f3295b0d8ca16/Cloud/3859e48180bb4cf8a207e15cf25a7e57.html)與 hana Studio 搭配使用，以瞭解系統資源耗用量，這可讓您進行適當的調整大小，以提高消費效率。

### <a name="storage"></a>儲存體 
儲存體效能是影響 SAP 應用程式使用者體驗的其中一個因素。  根據指定的 VM SKU，已將最低儲存體配置建議發佈[SAP Hana Azure 虛擬機器儲存體](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations-storage)設定。 我們建議您檢查這些最低規格，並與現有的？系統統計資料相比較，以確保新 HANA VM 有足夠的 IO 容量和效能。

如果您為新的 HANA VM 及其相關聯的處理常式設定 PPG，請提交支援票證，以檢查並確保儲存體和 HANA VM 的共置。  
因為您的備份解決方案可能需要變更，所以也應該再次參閱儲存成本以避免營運支出意外。 

### <a name="storage-replication-for-disaster-recovery"></a>嚴重損壞修復的儲存體複寫
在的中，儲存體複寫是針對 HANA 資料庫的嚴重損壞修復複寫提供的預設選項。 這項功能不是 Azure VM 的預設選項。 請考慮 HSR、備份/還原或其他支援的解決方案，以滿足您的業務需求。

### <a name="availability-sets-availability-zones-and-proximity-placement-groups"></a>可用性設定組、可用性區域和近接位置群組 
為了儘量減少應用層和 SAP Hana 之間的距離，以最少保留網路延遲，新的資料庫 VM 和目前的 SAP 應用程式伺服器應該放在鄰近放置群組（PPG）中。 請參閱[鄰近性放置群組](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-proximity-placement-scenarios)，以瞭解 Azure 可用性設定組和可用性區域如何與 PPG for SAP 部署搭配運作。
如果將目標 HANA 系統的成員部署在多個 Azure 區域中，客戶應該會清楚地看到所選區域的延遲設定檔。 SAP 系統元件的位置，對於 SAP 應用程式與資料庫之間的 proximal 距離而言，是最佳的。  [公用網域[可用性區域延遲測試] 工具](https://github.com/Azure/SAP-on-Azure-Scripts-and-Utilities/tree/master/AvZone-Latency-Test)可協助您更輕鬆地進行測量。  


### <a name="backup-strategy"></a>備份策略
許多客戶都已經使用協力廠商備份解決方案進行 SAP Hana。  在此情況下，只需要設定額外的受保護 VM 和 HANA 資料庫。  如果電腦在遷移後被解除委任，則目前的進度備份作業現在會處於未排程。
VM 上 SAP Hana 的 Azure 備份現已正式推出。  如需有關的詳細資訊，請參閱下列連結：[備份](https://docs.microsoft.com/azure/backup/backup-azure-sap-hana-database)、[還原](https://docs.microsoft.com/azure/backup/sap-hana-db-restore)、[管理](https://docs.microsoft.com/azure/backup/sap-hana-db-manage)Azure vm 中的 SAP Hana 備份。

### <a name="dr-strategy"></a>DR 策略
如果您的服務等級目標比對 blob 儲存體的簡單備份更長的復原時間，或就地還原或新的 VM，這是最簡單且最便宜的 DR 策略。  
如同使用 HSR 完成 HANA DR 的大型實例平臺，在 Azure VM 上，HSR 也是最自然和原生 SAP Hana DR 解決方案。  不論來源部署是單一實例、叢集是否有自動容錯移轉或多節點向外延展 HANA，DR 區域中都需要來源基礎結構的目標 HSR 複本。 此 HSR 目標 DR 複本會在主要執行 VM 遷移完成後建立。  DR HANA 實例會將 VM 實例上的主要 SAP Hana 註冊為次要複寫網站。  

### <a name="sap-application-server-connectivity-destination-change"></a>SAP 應用程式伺服器連線目的地變更
HSR 遷移方法會產生新的 HANA DB 主機，因此應用層會有新的 DB 主機名稱，因此必須修改 SAP 設定檔以反映新的主機名稱。  如果參數是透過名稱解析來保留主機名稱，則不需要變更設定檔。

### <a name="operating-system"></a>作業系統
適用于 B-HLI 和 VM 的作業系統映射，即使它們位於相同的版本層級，SLES 11 SP4 也不相同。 客戶必須在資訊安全驗證所需的套件、熱修正、修補程式、核心和安全性修正程式，才能將它們安裝在目標 VM 的作業系統上。  此外，客戶通常會在適用于 SAP HSR 的目標 VM 上設定較新的作業系統版本。  這是每個[SAP 附注 2763388](https://launchpad.support.sap.com/#/notes/2763388)的支援。

### <a name="new-sap-license-request"></a>新的 SAP 授權要求
簡單的呼叫會要求新 HANA 系統的新 SAP 授權，現在已遷移至 Vm。

### <a name="service-level-agreement-sla-differences"></a>服務等級協定（SLA）差異
這位作者想要知道在一到 Azure VM 之間的可用性 SLA 有何差異。  例如，叢集 Hli HA 配對提供99.99% 的可用性。 若要達到相同的 SLA，必須在可用性區域中部署 Vm。 [本文](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_9/)說明相關聯部署架構的可用性，讓客戶可以據以規劃該目標基礎結構。


## <a name="migration-strategy"></a>移轉策略
在本檔中，我們只會討論 HANA 系統複寫方法，以便從從：遷移至 Azure VM。  視部署的目標儲存體解決方案而定，進程會稍有不同。 高階步驟如下所述。

### <a name="vm-with-premiumultra-disks-for-data"></a>具有 premium/ultra 磁片的 VM 適用于資料
針對使用 premium 或 ultra 部署的 Vm，會套用標準 SAP Hana 系統複寫設定，並可用於安裝 HSR。  參考的[SAP 說明文章](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.04/099caa1959ce4b3fa1144562fa09e163.html)提供在兩個系統之間設定系統複寫、接管次要系統、容錯回復至主要系統，以及停用系統複寫所需的步驟的總覽。  基於遷移的目的，我們只需要設定、接管和停用在一開始的來源步驟上進行系統複寫。  

### <a name="vm-with-anf-for-data-and-log-volumes"></a>具有資料和記錄磁片區及的 VM
概括而言，完整資料和記錄磁片區的最新一層儲存體快照集必須複製到 Azure 儲存體，目標 HANA VM 可以存取和復原它們。  您可以使用任何原生 Linux 複製工具來完成複製程式。  

>[!Important]
> 複製和資料傳輸可能需要數小時，視 HANA 資料庫大小和網路頻寬而定。  大量複製程式應該在主要 HANA 資料庫停機時間之前完成。

### <a name="mcos-to-mdc-conversion"></a>MCOS 至 MDC 轉換
在一個系統（MCOS）部署模型中的多個元件是由我們的一位客戶選擇。  動機是規避舊版 SAP Hana 版本的多個資料庫容器（MDC）儲存快照集限制。  在 MCOS 模型中，會將數個獨立 SAP Hana 實例堆疊在一間的一間。  使用 HSR 來進行遷移，會使多個 HANA Vm 各自有一個租使用者 DB。  這種結束狀態的範圍會比客戶可能已讓習慣于的更忙碌。  使用 SAP Hana 2.0 的預設部署作為 MDC，有一個可行的替代方案是在 HSR 遷移之後執行[HANA 租使用者移動](https://launchpad.support.sap.com/#/notes/2472478)。  此程式會將這些獨立 HANA 資料庫合併成一個單一 HANA 容器中的 cotenants。  

### <a name="application-layer-consideration"></a>應用層考慮
資料庫伺服器會被視為 SAP 系統的中心。  所有的應用程式伺服器都應該位於 SAP Hana DB 附近。  在某些情況下，當您想要使用 PPG 時，可能需要將現有的應用程式伺服器重新置放到 HANA VM 所在的 PPG。  如果您已經有方便的部署範本，建立新的應用程式伺服器可能會被視為更容易。  
如果現有的應用程式伺服器和新的 HANA VM 以最佳方式放置，則不需要建立任何新的應用程式伺服器，除非需要額外的容量。  
如果建立新的基礎結構來加強服務可用性，現有的應用程式伺服器可能會變得不必要，而且應該關閉並刪除。
如果目標 VM 主機名稱已變更，且不同于伺服器主機名稱，則必須調整 SAP 應用程式伺服器設定檔以指向新的主機。  如果只有 HANA DB IP 位址已變更，則需要 DNS 記錄更新以將連入連線導向至新的 HANA VM。

### <a name="acceptance-test"></a>接受度測試
雖然從從紐約遷移至 VM 不會對資料庫內容進行任何資料變更（相較于異類遷移），但我們仍建議您驗證新設定的主要功能和效能層面。  

### <a name="cutover-plan"></a>轉換計畫
雖然這種遷移非常簡單，但它還是需要解除委任現有的資料庫。  在需要回溯時，請仔細規劃保留來源系統及其關聯的內容和備份映射，這是很重要的。  良好的規劃會提供 speedier 反轉。   


## <a name="post-migration"></a>移轉後
在我們安全地將任何與任何的與所有的服務或連線分離之後，才會進行遷移作業，以確保資料完整性得以保留。  此外，請關閉不必要的服務。  這一節會呼叫幾個最重要的專案。

### <a name="decommissioning-the-hli"></a>解除委任
將 HANA DB 成功遷移至 Azure VM 之後，您就不應該在此資料庫上進行生產商務交易。  不過，若要讓您的時間點等於其本機備份資料保留期間，請確定 speedier 的資料復原是安全的作法。  只有在，才應該解除委任。  除了技術方面的工作之外，客戶也希望能依履約與 Microsoft 一起做出的承諾。  客戶應洽詢其 Microsoft 代表，以透過：

### <a name="remove-any-proxy-ex-iptables-bigip-configured-for-hli"></a>移除為 B-HLI 設定的任何 proxy （例如： Iptables、BIGIP） 
如果 IPTables 之類的 proxy 服務用來將內部部署流量路由傳送到從的途中，則在成功遷移至 VM 之後，就不再需要該服務。  不過，此聯機服務應保留一段時間，只要您仍在使用 [解除委任] 主題中所討論的。  這種服務可以在停止的分頁完全解除委任之後關閉。 

### <a name="remove-global-reach-for-hli"></a>移除的範圍遍及全球 
全球觸達通常用來連接客戶的 ExpressRoute 閘道與的： ExpressRoute 閘道，讓客戶的內部部署流量能夠直接連線到服務中心租使用者，而不需要 proxy 服務。  就像 IPTables proxy 服務的情況一樣，應該保留 GlobalReach，直到已完全解除委任。

### <a name="operating-system-subscription--movereuse"></a>作業系統訂用帳戶-移動/重複使用
當 VM 伺服器已勇敢面對考驗，且已解除委任 blade 刀片時，可以取代或重複使用 OS 訂用帳戶，以避免重複支付 OS 授權。



## <a name="next-steps"></a>後續步驟
請參閱以下文章：
- [在 Azure 上 SAP Hana 基礎結構設定和作業](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations)。
- [Azure 上的 SAP 工作負載：規劃和部署檢查清單](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-deployment-checklist)。
