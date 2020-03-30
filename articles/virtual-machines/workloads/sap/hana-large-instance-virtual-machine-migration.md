---
title: 在 Azure（大型實例）上將 SAP HANA 遷移到 Azure 虛擬機器*微軟文檔
description: 如何將 Azure 上的 SAP HANA（大型實例）遷移到 Azure 虛擬機器
services: virtual-machines-linux
documentationcenter: ''
author: bentrin
manager: juergent
editor: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 02/11/2020
ms.author: bentrin
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: fd1267711871b3e55f1a6229e46ae27b360322f6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "77617047"
---
# <a name="sap-hana-on-azure-large-instance-migration-to-azure-virtual-machines"></a>Azure 大型實例上的 SAP HANA 遷移到 Azure 虛擬機器
本文介紹了可能的 Azure 大型實例部署方案，並提供規劃和遷移方法，並最大限度地減少過渡停機時間

## <a name="overview"></a>總覽
自 2016 年 9 月宣佈 SAP HANA （HLI） Azure 大型實例以來，許多客戶已使用此硬體作為其記憶體計算平臺的服務產品。  近年來，Azure VM 大小擴展加上對 HANA 橫向擴展部署的支援，已超過大多數企業客戶的 ERP 資料庫容量需求。  我們開始看到客戶表示有興趣將其 SAP HANA 工作負載從物理伺服器遷移到 Azure VM。
本指南不是分步配置文檔。 它描述了常見的部署模型，並提供規劃和遷移建議。  目的是指出必要的準備注意事項，以儘量減少過渡停機時間。

## <a name="assumptions"></a>假設
本文會做出以下假設：
- 唯一考慮的興趣是同質的 HANA 資料庫計算服務從 Hana 大型實例 （HLI） 遷移到 Azure VM，而無需進行重要的軟體升級或修補。 這些次要更新包括使用較新的作業系統版本或 HANA 版本，明確說明相關 SAP 說明支援。 
- 所有更新/升級活動都需要在遷移之前或之後完成。  例如，SAP HANA MCOS 轉換為 MDC 部署。 
- 提供最少停機時間的遷移方法是 SAP HANA 系統複製。 其他遷移方法不是本文檔範圍的一部分。
- 本指南適用于 HLI 的 Rev3 和 Rev4 SKU。
- HANA 部署體系結構在遷移期間主要保持不變。  也就是說，具有單個實例 DR 的系統將在目標上保持不變。
- 客戶已經查看並理解目標（即將成為）體系結構的服務等級協定 （SLA）。 
- HLIs 和 VM 之間的商業術語是不同的。 客戶應監控其 VM 的使用以進行成本管理。
- 客戶瞭解 HLI 是專用的計算平臺，而 VM 則在共用但隔離的基礎結構上運行。
- 客戶已驗證目標 VM 支援您預期的體系結構。 要查看所有支援的 VM SKU 認證的 SAP HANA 部署，請參閱[SAP HANA 硬體目錄](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure)。
- 客戶已驗證了設計和遷移計畫。
- 計畫災害復原 VM 以及主網站。  遷移後，客戶不能將 HLI 用作在 VM 上運行的主網站的 DR 節點。
- 客戶根據業務可恢復性和合規性要求將所需的備份檔案複製到目標 VM。 借助 VM 可訪問的備份，它允許在過渡期間進行時間點恢復。
- 對於 HSR HA，客戶需要根據 SAP HANA HA 指南為[SLES](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-pacemaker)和[RHEL](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-pacemaker)設置和配置 STONITH 設備。  它不像 HLI 大小寫那樣預配置。
- 此遷移方法不包括具有 Optane 配置的 HLI SKU。

## <a name="deployment-scenarios"></a>部署案例
下表匯總了 HLI 客戶的常見部署模型。  可以遷移到 Azure VM 的所有 HLI 方案。  為了從可用的補充 Azure 服務中獲益，可能需要進行細微的體系結構更改。

| 方案 ID | HLI 方案 | 逐字遷移到 VM？ | 備註 |
| --- | --- | --- | --- |
| 1 | [具有一個 SID 的單一節點](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario#single-node-with-one-sid) | 是 | - |
| 2 | [帶 MCOS 的單節點](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario#single-node-mcos) | 是 | - |
| 3 | [使用存儲複製的 DR 的單節點](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario#single-node-with-dr-using-storage-replication) | 否 | Azure 虛擬平臺不提供存儲複製，將當前 DR 解決方案更改為 HSR 或備份/還原 |
| 4 | [使用存儲複製的具有 DR（多用途）的單節點](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario#single-node-with-dr-multipurpose-using-storage-replication) | 否 | Azure 虛擬平臺不提供存儲複製，將當前 DR 解決方案更改為 HSR 或備份/還原 |
| 5 | [具有 STONITH 的 HSR，提供高可用性](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario#hsr-with-stonith-for-high-availability) | 是 | 沒有針對目標 VM 的預配置 SBD。  選擇並部署 STONITH 解決方案。  可能的選項：Azure 遮罩代理（支援兩個[RHEL](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-pacemaker) [、SLES](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-pacemaker)）SBD |
| 6 | [HA 帶 HSR，DR 帶存儲複製](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario#high-availability-with-hsr-and-dr-with-storage-replication) | 否 | 將存儲複製替換為 HSR 或備份/還原，以滿足 DR 需求 |
| 7 | [主機自動容錯移轉 (1+1)](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario#host-auto-failover-11) | 是 | 將 ANF 與 Azure VM 共用存儲 |
| 8 | [具待命相應放大](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario#scale-out-with-standby) | 是 | BW/4HANA，帶 M128s、M416s、M416ms VM，僅用於存儲 |
| 9 | [不具待命相應放大](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario#scale-out-without-standby) | 是 | BW/4HANA 帶 M128、M416、M416ms VM（無論是否使用 ANF 進行存儲） |
| 10 | [使用存儲複製使用 DR 橫向擴展](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario#scale-out-with-dr-using-storage-replication) | 否 | 將存儲複製替換為 HSR 或備份/還原，以滿足 DR 需求 |
| 11 | [使用 HSR 使用 DR 的單節點](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario#single-node-with-dr-using-hsr) | 是 | - |
| 12 | [單個節點 HSR 到 DR（成本優化）](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario#single-node-hsr-to-dr-cost-optimized) | 是 | - |
| 13 | [帶 HSR 的醫管局和 DR](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario#high-availability-and-disaster-recovery-with-hsr) | 是 | - |
| 14 | [具有 HSR 的 HA 和 DR（成本優化）](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario#high-availability-and-disaster-recovery-with-hsr-cost-optimized) | 是 | - |
| 15 | [使用 HSR 使用 DR 橫向擴展](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario#scale-out-with-dr-using-hsr) | 是 | BW/4HANA，帶M128。 M416s，M416ms VM（無論是否使用 ANF 進行存儲） |


## <a name="source-hli-planning"></a>源 （HLI） 規劃
在 HLI 伺服器中，Microsoft 服務管理和客戶都進行了用於運行 SAP HANA 資料庫的計算、網路、存儲和作業系統特定設置的規劃。  需要進行類似的規劃才能遷移到 Azure VM。

### <a name="sap-hana-housekeeping"></a>SAP HANA 內務管理 
清理資料庫內容是一種很好的操作實踐，這樣不需要的、過時的資料或陳舊的日誌不會遷移到新資料庫。  內務管理通常涉及刪除或存檔舊資料、過期資料或非活動資料。  這些"資料衛生"操作應在非生產系統中進行測試，以在生產使用之前驗證其資料縮減有效性。

### <a name="allow-network-connectivity-for-new-vms-and-or-virtual-network"></a>允許新 VM 和 虛擬網路進行網路連接 
在客戶的 HLI 部署中，網路已基於文章[SAP HANA（大型實例）網路架構](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-network-architecture)中描述的資訊進行設置。 此外，網路流量路由以"Azure 中路由"部分中概述的方式完成。
- 在將新 VM 設置為遷移目標時，如果將其放置在現有虛擬網路中，其 IP 位址範圍已允許連接到 HLI，則無需進一步的連接更新。
- 如果新的 Azure VM 放置在新的 Microsoft Azure 虛擬網路中，可能位於另一個區域中，並與現有虛擬網路對等，則原始 HLI 預配中的 ExpressRoute 服務金鑰和資源識別碼 可用於允許訪問此新虛擬虛擬網路 IP 範圍。  與 Microsoft 服務管理協調，使虛擬網路能夠連接到 HLI 連接。  注意：為了最小化應用程式層和資料庫層之間的網路延遲，應用程式層和資料庫層必須位於同一虛擬網路上。  

### <a name="existing-app-layer-availability-set-availability-zones-and-proximity-placement-group-ppg"></a>現有應用圖層可用性集、可用性區域和鄰近放置組 （PPG）
當前部署模型是為了滿足某些服務等級目標。  在此移動中，確保目標基礎結構達到或超過設定的目標。  
更有可能的是，客戶 SAP 應用程式伺服器被放置在可用性集中。  如果當前部署服務等級令人滿意，並且 
- 如果目標 VM 假定 HLI 邏輯名稱的主機名稱，則更新指向 VM IP 的功能變數名稱服務 （DNS） 位址解析將工作，而無需更新任何 SAP 設定檔
- 如果不使用 PPG，請確保將所有應用程式和資料庫伺服器放在同一個區域中，以儘量減少網路延遲。
- 如果您使用的是 PPG，請參閱本文檔的部分："目標規劃、可用性集、可用性區域和鄰近放置組 （PPG）"。""。

### <a name="storage-replication-discontinuance-process-if-used"></a>存儲複製中斷過程（如果使用）
如果存儲複製用作 DR 解決方案，則應在 SAP 應用程式關閉後終止（計畫外）。  此外，最後一個 SAP HANA 目錄、日誌檔和資料備份已複製到遠端 DR HLI 存儲卷上。  這樣做是一種預防措施，以防在物理伺服器到 Azure VM 轉換期間發生災難。

### <a name="data-backups-preservation-consideration"></a>資料備份保存注意事項
在 Azure VM 上共用到 SAP HANA 後，HLI 上的所有基於快照的資料或記錄備份都不容易訪問或恢復，如果需要，VM 也不容易訪問或恢復。 在早期過渡期間，在基於 Azure 的備份構建足夠的歷史記錄以滿足時間點恢復要求之前，我們建議在切換前幾天或數周之前，除 HLI 上的快照外，還獲取檔級別備份。  將這些備份複製到 Azure 存儲帳戶，以便由新的 SAP HANA VM 訪問。
除了備份 HLI 內容外，在需要回滾時，最好能夠輕鬆訪問 SAP 環境的完整備份。

### <a name="adjusting-system-monitoring"></a>調整系統監控 
客戶使用許多不同的工具來監視和發送 SAP 環境中系統的警報通知。  此專案只是適當操作的呼出，用於合併用於監視和更新警報通知收件者（如果需要）的更改。

### <a name="microsoft-operations-team-involvement"></a>微軟運營團隊參與 
根據現有的 HLI 實例從 Azure 門戶打開票證。  創建支援票證後，支援工程師將通過電子郵件與您聯繫。  

### <a name="engage-microsoft-account-team"></a>參與微軟客戶團隊
在 HLI 合同的周年續訂時間附近規劃遷移，以儘量減少計算資源不必要的超支。 要停用 HLI 刀片，需要協調合同終止和裝置的實際關閉。

## <a name="destination-planning"></a>目的地規劃
建立一個新的基礎設施，取代現有的基礎設施，值得思考，以確保新的增加將適合大計畫的事情。  以下是思考的一些要點。

### <a name="resource-availability-in-the-target-region"></a>目的地區域的資源可用性 
當前 SAP 應用程式伺服器的部署區域通常與關聯的 HLIs 非常接近。  但是，與可用 Azure 區域相比，HLI 提供的位置更少。  將物理 HLI 遷移到 Azure VM 時，也是"微調"所有相關服務距離以進行性能優化的好時機。  在這樣做的同時，一個關鍵考慮是確保選定的區域擁有所有必要的資源。  例如，某些 VM 系列的可用性或 Azure 區域的可用性設置。

### <a name="virtual-network"></a>虛擬網路 
客戶需要選擇是在現有虛擬網路中運行新的 HANA 資料庫，還是創建新的 HANA 資料庫。  主要決定因素是 SAP 環境的當前網路佈局。  此外，當基礎結構從一個區域部署到雙區部署並使用 PPG 時，它強制實施體系結構更改。 有關詳細資訊，請參閱有關 Azure [PPG 的文章，瞭解 SAP 應用程式的最佳網路延遲](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-proximity-placement-scenarios)。   

### <a name="security"></a>安全性
無論是新的 SAP HANA VM 在新的 vnet/子網上著陸，它都代表著一種新的業務關鍵型服務，需要保護。  應針對這種新的服務類別評估和部署符合公司資訊安全性原則的存取控制。

### <a name="vm-sizing-recommendation"></a>VM 大小調整建議
此遷移也是正確調整 HANA 計算引擎大小的機會。  我們可以與 HANA Studio 結合使用 HANA[系統檢視](https://help.sap.com/viewer/7c78579ce9b14a669c1f3295b0d8ca16/Cloud/3859e48180bb4cf8a207e15cf25a7e57.html)來瞭解系統資源消耗，從而允許正確調整大小以提高支出效率。

### <a name="storage"></a>存放裝置 
存儲性能是影響 SAP 應用程式使用者體驗的因素之一。  基於給定的 VM SKU，有最少的存儲佈局發佈[SAP HANA Azure 虛擬機器存儲配置](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations-storage)。 我們建議查看這些最低規格，並對現有 HLI 系統統計資訊進行比較，以確保新 HANA VM 有足夠的 IO 容量和性能。

如果為新 HANA VM 及其關聯的分單元配置 PPG，請提交支援票證以檢查和確保存儲和 VM 的同地位置。 由於備份解決方案可能需要更改，因此還應重新查看存儲成本，以避免運營支出意外。

### <a name="storage-replication-for-disaster-recovery"></a>用於災害復原的存儲複製
使用 HLI 時，存儲複製作為災害復原的預設選項提供。 此功能不是 Azure VM 上的 SAP HANA 的預設選項。 考慮 HSR、備份/恢復或其他支援的解決方案，滿足您的業務需求。

### <a name="availability-sets-availability-zones-and-proximity-placement-groups"></a>可用性集、可用性區域和鄰近放置組 
為了縮短應用程式層和 SAP HANA 之間的距離，以將網路延遲降至最低，應將新的資料庫 VM 和當前的 SAP 應用程式伺服器置於 PPG 中。 請參閱[鄰近放置組](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-proximity-placement-scenarios)，瞭解 Azure 可用性集和可用性區域如何使用 PPG 進行 SAP 部署。
如果目標 HANA 系統的成員部署在多個 Azure 區域中，客戶應清楚地瞭解所選區域的延遲設定檔。 SAP 系統元件的放置對於 SAP 應用程式和資料庫之間的近距是最佳選擇。  公域[可用性區域延遲測試控管](https://github.com/Azure/SAP-on-Azure-Scripts-and-Utilities/tree/master/AvZone-Latency-Test)有助於簡化測量。  


### <a name="backup-strategy"></a>備份策略
許多客戶已經在 HLI 上使用 SAP HANA 的協力廠商備份解決方案。  在這種情況下，只需要配置其他受保護的 VM 和 HANA 資料庫。  如果電腦在遷移後正在停用，則現在可以計畫正在進行的 HLI 備份作業。
VM 上的 SAP HANA 的 Azure 備份現在通常可用。  有關以下連結的詳細資訊，請參閱以下連結：在 Azure VM 中[備份](https://docs.microsoft.com/azure/backup/backup-azure-sap-hana-database)、[還原](https://docs.microsoft.com/azure/backup/sap-hana-db-restore)[、管理](https://docs.microsoft.com/azure/backup/sap-hana-db-manage)SAP HANA 備份。

### <a name="dr-strategy"></a>DR 戰略
如果服務等級目標適應較長的恢復時間，則對 Blob 存儲進行簡單備份，並就地還原或還原到新 VM 是最簡單、成本最低的 DR 策略。  
與 HANA DR 通常使用 HSR 完成的大型實例平臺類似;在 Azure VM 上，HSR 也是最自然和本機 SAP HANA DR 解決方案。  無論源部署是單實例部署還是群集部署，DR 區域都需要源基礎結構的副本。
此 DR 副本將在主 HLI 到 VM 遷移完成後進行配置。  DR HANA 資料庫將作為輔助複製網站註冊到 VM 實例上的主要 SAP HANA。  

### <a name="sap-application-server-connectivity-destination-change"></a>SAP 應用程式伺服器連接目標更改
HSR 遷移產生了新的 HANA 資料庫主機，因此應用程式層的新資料庫主機名稱需要修改 SAP 設定檔以反映新的主機名稱。  如果切換是通過保留主機名稱的名稱解析完成的，則不需要更改設定檔。

### <a name="operating-system"></a>作業系統
例如，HLI 和 VM 的作業系統映射（儘管位於同一版本級別）並不相同。 客戶必須在 HLI 上驗證所需的包、熱修復程式、修補程式、內核和安全修補程式，才能在目標上安裝相同的包。  支援使用 HSR 將舊作業系統複製到具有較新作業系統版本的 VM 上。  通過查看[SAP 注釋 2763388](https://launchpad.support.sap.com/#/notes/2763388)驗證特定支援的版本。

### <a name="new-sap-license-request"></a>新的 SAP 許可證請求
一個簡單的呼出，以請求新的SAP許可證的新HANA系統，因為它已遷移到VM。

### <a name="service-level-agreement-sla-differences"></a>服務等級協定 （SLA） 差異
作者喜歡指出 HLI 和 Azure VM 之間的可用性 SLA 差異。  例如，群集 HLIs HA 對提供 99.99% 的可用性。 要實現相同的 SLA，必須在可用性區域中部署 VM。 [本文](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_9/)介紹具有相關部署體系結構的可用性，以便客戶可以相應地規劃其目標基礎結構。


## <a name="migration-strategy"></a>移轉策略
在本文中，我們僅介紹從 HLI 遷移到 Azure VM 的 HANA 系統複製方法。  取決於部署的目標存儲解決方案，該過程略有不同。 下面將介紹高級步驟。

### <a name="vm-with-premiumultra-disks-for-data"></a>具有高級/超磁片資料的 VM
對於使用高級磁片或超磁片部署的 VM，標準 SAP HANA 系統複製配置適用于設置 HSR。  [SAP 説明文章](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.04/099caa1959ce4b3fa1144562fa09e163.html)概述了設置系統複製、接管輔助系統、故障回主系統以及禁用系統複製所涉及的步驟。  出於遷移的目的，我們只需要設置、接管和禁用複製步驟。  

### <a name="vm-with-anf-for-data-and-log-volumes"></a>用於資料和日誌卷的 ANF 的 VM
在高級別上，需要將完整資料和日誌卷的最新 HLI 存儲快照複製到 Azure 存儲，目標 HANA VM 可訪問和恢復這些快照。  複製過程可以使用任何本機 Linux 複製工具完成。  

> [!IMPORTANT]
> 複製和資料傳輸可能需要數小時，具體取決於 HANA 資料庫大小和網路頻寬。  複製過程的大部分應在主要 HANA DB 停機之前完成。

### <a name="mcos-to-mdc-conversion"></a>MCOS 到 MDC 轉換
一個系統中的多個元件 （MCOS） 部署模型被我們的一些 HLI 客戶使用。  其動機是規避早期 SAP HANA 版本的多個資料庫容器 （MDC） 存儲快照限制。  在 MCOS 模型中，多個獨立的 SAP HANA 實例堆疊在一個 HLI 刀片中。  將 HSR 用於遷移可以正常工作，但會導致多個 HANA VM，每個租戶資料庫都有一個租戶資料庫。  這種終端狀態使環境比客戶可能習慣的更繁忙。  當 SAP HANA 2.0 預設部署為 MDC 時，一個可行的替代方法是在 HSR 遷移後執行[HANA 租戶移動](https://launchpad.support.sap.com/#/notes/2472478)。  此過程將這些獨立的 HANA 資料庫"合併"為單個 HANA 容器中的共同租戶。  

### <a name="application-layer-consideration"></a>應用層考慮
資料庫伺服器被視為 SAP 系統的中心。  所有應用程式伺服器都應位於 SAP HANA 資料庫附近。  在某些情況下，當需要使用 PPG 時，可能需要將現有應用程式伺服器重新置放到可能需要 HANA VM 的 PPG 上。  如果您已經方便了部署範本，則構建新的應用程式伺服器可能更容易。  
如果現有應用程式伺服器和新的 HANA VM 處於最佳位置，則無需構建新的應用程式伺服器，除非需要額外的容量。  
如果構建新的基礎結構以提高服務可用性，則現有應用程式伺服器可能變得沒有必要，應關閉並刪除。
如果目標 VM 主機名稱已更改，並且與 HLI 主機名稱不同，則需要調整 SAP 應用程式伺服器設定檔以指向新主機。  如果僅更改了 HANA DB IP 位址，則需要 DNS 記錄更新才能引導傳入連接到新的 HANA VM。

### <a name="acceptance-test"></a>接受度測試
儘管與異構遷移相比，從 HLI 遷移到 VM 不會對資料庫內容進行實質性更改，但我們仍建議驗證新設置的關鍵功能和性能方面。  

### <a name="cutover-plan"></a>截止計畫
儘管這種遷移是直接的，但它涉及現有 DB 的停用。  如果有必要回退，仔細規劃保留源系統及其相關內容和備份映射至關重要。  良好的規劃確實提供了更快的逆轉。   


## <a name="post-migration"></a>移轉後
遷移作業不會完成，直到我們安全地分離任何與 HLI 相關的服務或連接，以確保資料完整性得到保留。  此外，關閉不必要的服務。  本節會調用一些最高級的專案。

### <a name="decommissioning-the-hli"></a>HLI 退役
成功將 HANA DB 遷移到 Azure VM 後，請確保 HLI DB 上沒有運行任何高效的業務事務。  但是，保持 HLI 運行一段時間等於其本地備份保留視窗是一種安全的做法，確保在需要時加快恢復速度。  只有到那時，HLI 刀片才能停用。  客戶應通過聯繫其 Microsoft 代表來合同完成與 Microsoft 的 HLI 承諾。

### <a name="remove-any-proxy-ex-iptables-bigip-configured-for-hli"></a>刪除為 HLI 配置的任何代理（例如：Iptables、BIGIP） 
如果 IPTables 等代理服務用於路由本地流量到 HLI 和從 HLI 路由，則在成功遷移到 VM 後不再需要它。  但是，只要 HLI 刀片仍然處於待命狀態，此連接服務應保留。  只有在 HLI 刀片完全停用後關閉服務。

### <a name="remove-global-reach-for-hli"></a>刪除 HLI 的全球覆蓋範圍 
全球覆蓋用於將客戶的快速路由閘道與 HLI ExpressRoute 閘道連接起來。  它允許客戶的本地流量直接到達 HLI 租戶，而無需使用代理服務。  遷移後沒有 HLI 單元，則不再需要此連接。  與 IPTables 代理服務的情況一樣，GlobalReach 也應保留，直到 HLI 刀片完全停用。

### <a name="operating-system-subscription--movereuse"></a>作業系統訂閱 = 移動/重用
當 VM 伺服器已啟動且 HLI 刀片已停用時，可以替換或重複使用作業系統訂閱，以避免作業系統許可證的雙重支付。



## <a name="next-steps"></a>後續步驟
 請參閱以下文章：
- [SAP HANA 基礎結構配置和在 Azure 上的操作](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations)。
- [Azure 上的 SAP 工作負載：規劃和部署檢查表](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-deployment-checklist)。
