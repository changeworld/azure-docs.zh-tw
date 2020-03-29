---
title: 將大型機計算移動到 Azure 虛擬機器
description: Azure 計算資源與大型機容量相比可得，因此您可以遷移 IBM z14 應用程式並實現現代化。
author: njray
ms.author: larryme
ms.date: 04/02/2019
ms.topic: article
ms.service: multiple
ms.openlocfilehash: 97f354d0a313d58c671366dd0e5f485504823e13
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "76288926"
---
# <a name="move-mainframe-compute-to-azure"></a>將大型機計算移動到 Azure

大型機以高可靠性和可用性著稱，並繼續是許多企業值得信賴的骨幹。 它們通常被認為具有幾乎無限的可擴充性和計算能力。 然而，一些企業已經發展出了最大的可用大型機的能力。 如果這聽起來類似您，Azure 可提供敏捷性、覆蓋範圍和基礎結構節省。

要在 Microsoft Azure 上運行大型機工作負荷，您需要瞭解大型機的計算功能與 Azure 的比較情況。 本文基於 IBM z14 大型機（本文編寫時的最最新型號），告訴您如何在 Azure 上獲得可比較的結果。

要開始，並排考慮環境。 下圖將運行應用程式的大型機環境與 Azure 託管環境進行比較。

![Azure 服務和模擬環境提供類似的支援並簡化遷移](media/mainframe-compute-azure.png)

大型機的強大功能通常用於線上交易處理 （OLTP） 系統，該系統可為數千使用者處理數百萬個更新。 這些應用程式通常使用軟體進行交易處理、螢幕處理和表單輸入。 他們可以使用客戶資訊控制系統 （CICS）、資訊管理系統 （IMS） 或事務介面包 （TIP）。

如圖所示，Azure 上的 TPM 模擬程式可以處理 CICS 和 IMS 工作負載。 Azure 上的批次處理系統模擬程式執行作業控制語言 （JCL） 的角色。 大型機資料將遷移到 Azure 資料庫，如 Azure SQL 資料庫。 Azure 服務或 Azure 虛擬機器中託管的其他軟體可用於系統管理。

## <a name="mainframe-compute-at-a-glance"></a>大型機計算一目了然

在 z14 大型機中，處理器最多排列在四*個抽屜中*。 *抽屜*只是一組處理器和晶片組。 每個抽屜可以有六個活躍的中央處理器 （CP） 晶片，每個 CP 有 10 個系統控制器 （SC） 晶片。 在英特爾 x86 術語中，每個抽屜有 6 個插槽、每個插槽 10 個內核和 4 個抽屜。 此體系結構為 z14 提供 24 個通訊端和 240 個內核的粗糙等效。

快速 z14 CP 具有 5.2 GHz 時脈速度。 通常，z14 隨框中的所有 CP 一起傳遞。 根據需要啟動它們。 儘管實際使用方式，客戶通常每月至少收取四個小時的計算費用。

大型機處理器可以配置為以下類型之一：

- 通用 （GP） 處理器
- 系統 z 集成資訊處理器 （zIIP）
- Linux （IFL） 處理器集成設施
- 系統輔助處理器 （SAP）
- 集成耦合設施 （ICF） 處理器

## <a name="scaling-mainframe-compute-up-and-out"></a>擴展大型機計算向上和出

IBM 大型機能夠擴展至 240 個內核（單個系統的當前 z14 大小）。 此外，IBM 大型機可以通過稱為耦合設施 （CF） 的功能橫向擴展。 CF 允許多個大型機系統同時訪問相同的資料。 使用 CF，大型機並行系統技術將大型機處理器分組群集中。 編寫本指南時，Parallel Sysplex 功能支援 32 個分組，每個分組包含 64 個處理器。 可以以這種方式對多達 2，048 個處理器進行分組，以橫向擴展計算容量。

CF 允許計算群集通過直接存取共用資料。 它用於鎖定資訊、緩存資訊和共用資料資源清單。 使用一個或多個 CF 的並行系統可被視為"共用所有內容"橫向擴展計算群集。 有關這些功能的詳細資訊，請參閱 IBM 網站上的 IBM Z 上的[並行系統](https://www.ibm.com/it-infrastructure/z/technologies/parallel-sysplex-resources)。

應用程式可以使用這些功能提供橫向擴充性能和高可用性。 有關 CICS 如何使用 CF 並行系統的資訊，請下載[IBM CICS 和耦合工具：超越基礎知識](https://www.redbooks.ibm.com/redbooks/pdfs/sg248420.pdf)紅皮書。

## <a name="azure-compute-at-a-glance"></a>Azure 計算一覽

有些人錯誤地認為基於英特爾的伺服器不如大型機強大。 但是，基於英特爾的新核心密集型系統具有與大型機一樣多的計算能力。 本節介紹用於計算和存儲的 Azure 基礎架構即服務 （IaaS） 選項。 Azure 還提供平臺即服務 （PaaS） 選項，但本文重點介紹了提供類似大型機容量的 IaaS 選項。

Azure 虛擬機器提供各種大小和類型的計算能力。 在 Azure 中，虛擬 CPU （vCPU） 大致等同于大型機上的內核。

目前，Azure 虛擬機器大小範圍提供從 1 到 128 vCPU。 虛擬機器 （VM） 類型針對特定工作負載進行了優化。 例如，下面的清單顯示 VM 類型（截至本文編寫時當前）及其建議的用途：

| 大小     | 類型和描述                                                                 |
|----------|--------------------------------------------------------------------------------------|
| D 系列 | 通用，64 vCPU 和高達 3.5-GHz 時脈速度                           |
| 電子系列 | 記憶體優化，最多 64 個 vCPU                                                 |
| F 系列 | 優化了高達 64 個 vCPU 和 3.7 GHz 時脈速度的計算優化                       |
| H 系列 | 針對高性能計算 （HPC） 應用進行了優化                          |
| L 系列 | 針對由 NoSQL 等資料庫支援的高通量應用程式優化的存儲 |
| M 系列 | 最大的計算和記憶體優化的 VM，多達 128 個 vCPU                        |

有關可用 VM 的詳細資訊，請參閱[虛擬機器系列](https://azure.microsoft.com/pricing/details/virtual-machines/series/)。

z14 大型機最多可具有 240 個內核。 但是，z14 大型機幾乎從不將所有內核用於單個應用程式或工作負載。 相反，大型機將工作負載劃分為邏輯分區 （LPAR），並且 LPAR 具有評級 — MIPS（每秒數百萬條指令）或 MSU（百萬服務單元）。 在確定在 Azure 上運行大型機工作負荷所需的可比 VM 大小時，將 MIPS（或 MSU） 評級考慮在內。

以下是一般估計值：

-   每 vCPU 150 MIPS

-   每個處理器 1，000 MIPS

要確定 LPAR 中給定工作負載的正確 VM 大小，請先優化工作負載的 VM。 然後確定所需的 vCPU 數量。 保守估計是每 vCPU 150 MIPS。 例如，基於此估計值，具有 16 個 vCPU 的 F 系列 VM 可以輕鬆支援來自具有 2，400 個 MIPS 的 LPAR 的 IBM Db2 工作負載。

## <a name="azure-compute-scale-up"></a>Azure 計算擴展

M 系列 VM 可以擴展到 128 vCPU（在撰寫本文時）。 使用每個 vCPU 150 MIPS 的保守估計值，M 系列 VM 相當於大約 19，000 個 MIPS。 估計大型機的 MIPS 的一般規則是每個處理器 1，000 MIPS。 z14 大型機最多可具有 24 個處理器，並為單個大型機系統提供約 24，000 個 MIPS。

最大的單個 z14 大型機比 Azure 中可用的最大 VM 多約 5，000 個 MIPS。 但是，比較工作負載的部署方式非常重要。 如果大型機系統同時具有應用程式和關係資料庫，則它們通常部署在同一物理主機上，每個機都部署在自己的 LPAR 中。 Azure 上的相同解決方案通常使用一個 VM 來部署應用程式，並為資料庫使用單獨、大小合適的 VM 部署。

例如，如果 M64 vCPU 系統支援該應用程式，並且資料庫使用 M96 vCPU，則大約需要 150 vCPU，或者如下圖所示，大約需要 24，000 個 MICPU。

![比較 24，000 個 MIPS 的工作負載部署](media/mainframe-compute-mips.png)

方法是將 LPAR 遷移到各個 VM。 然後，Azure 可輕鬆擴展到部署在單個大型機系統上的大多數應用程式所需的大小。

## <a name="azure-compute-scale-out"></a>Azure 計算橫向擴展

基於 Azure 的解決方案的優點之一是能夠橫向擴展。擴展使應用程式幾乎可以無限地使用計算容量。 Azure 支援多種方法來擴展計算能力：

- **跨群集的負載平衡。** 在這種情況下，應用程式可以使用[負載等化器](/azure/load-balancer/load-balancer-overview)或資源管理器在群集中的多個 VM 之間分散工作負載。 如果需要更多計算容量，則向群集中添加其他 VM。

- **虛擬機器縮放集。** 在此突發方案中，應用程式可以根據 VM 使用方式擴展到其他[計算資源](/azure/virtual-machine-scale-sets/overview)。 當需求下降時，規模集中的 VM 數量也會下降，從而確保計算能力的高效使用。

- **PaaS 縮放。** Azure PaaS 提供縮放計算資源。 例如[，Azure Service Fabric](/azure/service-fabric/service-fabric-overview)分配計算資源以滿足請求量的增加。

- **庫伯內斯集群。** Azure 上的應用程式可以使用[Kubernetes 群集](/azure/aks/concepts-clusters-workloads)來計算指定資源的服務。 Azure 庫伯奈斯服務 （AKS） 是一種託管服務，用於協調 Azure 上的庫伯奈斯節點、池和群集。

要選擇正確的方法來擴展計算資源，請務必瞭解 Azure 和大型機有何不同。 關鍵是計算資源如何共用資料，或者資料是否共用。 在 Azure 中，資料（預設情況下）通常不由多個 VM 共用。 如果橫向擴展計算群集中的多個 VM 需要資料共用，則共用資料必須駐留在支援此功能的資源中。 在 Azure 上，資料共用涉及存儲，如下節討論。

## <a name="azure-compute-optimization"></a>Azure 計算優化

您可以在 Azure 體系結構中優化每一層處理。 為每個環境使用最合適的 VM 和功能類型。 下圖顯示了在 Azure 中部署 VM 以支援使用 Db2 的 CICS 應用程式的潛在模式。 在主要站台中，會為生產、生產階段前和測試 VM 部署高可用性。 次要站台供備份和災害復原之用。

每個層還可以提供適當的災害復原服務。 例如，生產環境和資料庫 VM 可能需要熱復原或暖復原，而開發和測試 VM 則支援冷復原。

![支援災害復原的極可用部署](media/mainframe-compute-dr.png)

## <a name="next-steps"></a>後續步驟

- [大型機遷移](/azure/architecture/cloud-adoption/infrastructure/mainframe-migration/overview)
- [大型機在 Azure 虛擬機器上重新託管](/azure/virtual-machines/workloads/mainframe-rehosting/overview)
- [將大型機存儲移動到 Azure](mainframe-storage-Azure.md)

### <a name="ibm-resources"></a>IBM 資源

- [IBM Z 上的並行系統](https://www.ibm.com/it-infrastructure/z/technologies/parallel-sysplex-resources)
- [IBM CICS 和耦合設施：超越基礎知識](https://www.redbooks.ibm.com/redbooks/pdfs/sg248420.pdf)
- [為 Db2 純尺規功能安裝創建所需的使用者](https://www.ibm.com/support/knowledgecenter/en/SSEPGG_11.1.0/com.ibm.db2.luw.qb.server.doc/doc/t0055374.html?pos=2)
- [Db2icrt - 創建實例命令](https://www.ibm.com/support/knowledgecenter/en/SSEPGG_11.1.0/com.ibm.db2.luw.admin.cmd.doc/doc/r0002057.html)
- [Db2 純標群集資料庫解決方案](https://www.ibmbigdatahub.com/blog/db2-purescale-clustered-database-solution-part-1)
- [IBM Data Studio](https://www.ibm.com/developerworks/downloads/im/data/index.html/)

### <a name="azure-government"></a>Azure Government

- [適用于大型機應用程式的 Microsoft Azure 政府雲](https://azure.microsoft.com/resources/microsoft-azure-government-cloud-for-mainframe-applications/)
- [微軟和FedRAMP](https://www.microsoft.com/TrustCenter/Compliance/FedRAMP)

### <a name="more-migration-resources"></a>更多遷移資源

- [Azure 虛擬資料中心：原形移轉指南](https://azure.microsoft.com/resources/azure-virtual-datacenter-lift-and-shift-guide/)
- [GlusterFS iSCSI](https://docs.gluster.org/en/latest/Administrator%20Guide/GlusterFS%20iSCSI/)
