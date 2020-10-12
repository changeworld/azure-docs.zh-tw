---
title: 將大型主機計算移至 Azure 虛擬機器
description: Azure 計算資源相較于大型主機容量，因此您可以遷移和現代化 IBM z14 應用程式。
author: njray
ms.author: larryme
ms.date: 04/02/2019
ms.topic: article
ms.service: multiple
ms.openlocfilehash: 9c5941ec88cd793961ad66245d0dc0b5e0d7772f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "86998930"
---
# <a name="move-mainframe-compute-to-azure"></a>將大型主機計算移至 Azure

大型主機具有高可靠性和可用性的信譽，並持續成為許多企業的受信任骨幹。 它們通常被視為幾乎無限制的擴充性和計算能力。 不過，某些企業已不再最大的可用大型主機功能。 如果這聽起來像您，Azure 會提供靈活性、觸及率及基礎結構的節省。

若要在 Microsoft Azure 上執行大型主機工作負載，您需要知道您的大型主機計算功能與 Azure 的比較方式。 根據 IBM z14 大型主機 (此撰寫) 最新的模型，本文將告訴您如何在 Azure 上取得可比較的結果。

若要開始使用，請考慮並存的環境。 下圖比較用來執行應用程式至 Azure 裝載環境的大型主機環境。

![Azure 服務和模擬環境提供可比較的支援並簡化遷移](media/mainframe-compute-azure.png)

大型主機的威力通常用於線上交易處理 (OLTP) 系統處理數千名使用者的數百萬個更新。 這些應用程式通常會使用軟體來處理交易處理、螢幕處理和表單專案。 他們可能會使用客戶資訊控制系統 (CICS) 、資訊管理系統 (IMS) 或交易介面套件 (提示) 。

如圖所示，Azure 上的 TPM 模擬器可以處理 CICS 和 IMS 工作負載。 Azure 上的 batch 系統模擬器會執行 (JCL) 的工作控制語言角色。 將大型主機資料移轉至 Azure 資料庫，例如 Azure SQL Database。 Azure 服務或 Azure 虛擬機器中裝載的其他軟體都可用於系統管理。

## <a name="mainframe-compute-at-a-glance"></a>大型主機計算一覽

在 z14 大型主機中，處理器最多會排列在四個 *抽屜*內。 隱藏式選單 *只是處理器和晶片組的叢集* 。 每個隱藏式選單都可以有六個主動中央處理器 (CP) 晶片，而每個 CP 都有10個系統控制器 (SC) 晶片。 在 Intel x86 術語中，每個抽屜都有六個通訊端，每個通訊端10個核心，以及四個抽屜。 此架構針對 z14 提供相當於24個通訊端和240核心（最大值）的粗略。

Fast z14 CP 具有 5.2 GHz 的頻率速度。 一般而言，z14 會隨方塊中的所有 CPs 一起傳遞。 您可以視需要啟動它們。 客戶通常會在每個月至少收取四小時的計算時間，而不是實際的使用量。

大型主機處理器可以設定為下列其中一種類型：

-  (GP) 處理器的一般用途
- System z 整合式資訊處理器 (zIIP) 
- Linux (IFL) 處理器的整合式設備
- 系統協助處理器 (SAP) 
- 整合式結合功能 (ICF) 處理器

## <a name="scaling-mainframe-compute-up-and-out"></a>相應增加與相應放大大型主機計算

IBM 大型主機提供最多可擴大至240核心的能力， (單一系統) 的目前 z14 大小。 此外，IBM 大型主機可以透過稱為結合功能 (CF) 的功能來向外延展。 CF 可讓多個大型主機系統同時存取相同的資料。 使用 CF 時，大型主機平行 Sysplex 技術會將大型主機處理器群組在群集中。 撰寫本指南時，Parallel Sysplex 功能支援每個64處理器32組。 您可以用這種方式將最多2048個處理器分組，以相應放大計算容量。

CF 可讓計算叢集與直接存取共用資料。 它會用於鎖定資訊、快取資訊，以及共用資料資源的清單。 使用一或多個 CFs 的平行 Sysplex 可以視為「共用所有專案」的相應放大計算叢集。 如需這些功能的詳細資訊，請參閱 IBM 網站上的 [Ibm Z 平行 Sysplex](https://www.ibm.com/it-infrastructure/z/technologies/parallel-sysplex-resources) 。

應用程式可以使用這些功能來提供相應放大效能和高可用性。 如需 CICS 如何搭配 CF 使用 Parallel Sysplex 的相關資訊，請下載 [IBM CICS 和結合功能：超越基本](https://www.redbooks.ibm.com/redbooks/pdfs/sg248420.pdf) redbook。

## <a name="azure-compute-at-a-glance"></a>Azure 計算一覽

有些人誤以為 Intel 伺服器不像大型主機一樣強大。 不過，新的核心密集 Intel 型系統擁有的計算容量與大型主機一樣多。 本節說明適用于計算和儲存的 Azure 基礎結構即服務 (IaaS) 選項。 Azure 也提供平臺即服務 (PaaS) 選項，但這篇文章著重于提供可比較的大型主機容量的 IaaS 選擇。

Azure 虛擬機器可讓您以各種大小和類型來提供計算能力。 在 Azure 中，虛擬 CPU (vCPU) 大約等於大型主機上的核心。

目前，Azure 虛擬機器大小的範圍提供從1到128的個 vcpu。 虛擬機器 (VM) 類型已針對特定工作負載優化。 例如，下列清單顯示此撰寫)  (目前的 VM 類型，以及它們的建議用法：

| 大小     | 類型和描述                                                                 |
|----------|--------------------------------------------------------------------------------------|
| D 系列 | 使用 64 vCPU 和最高 3.5-GHz 頻率速度的一般用途                           |
| E 系列 | 最高可達64個 vcpu 的記憶體優化                                                 |
| F 系列 | 以最高64個 vcpu 和 3. 7 GHz 頻率速度優化的計算                       |
| H 系列 | 針對高效能運算 (HPC) 應用程式優化                          |
| L 系列 | 針對高輸送量的應用程式（例如 NoSQL）支援的儲存體 |
| M 系列 | 最大的計算和記憶體優化 Vm，最多可達128個 vcpu                        |

如需可用 Vm 的詳細資訊，請參閱 [虛擬機器系列](https://azure.microsoft.com/pricing/details/virtual-machines/series/)。

Z14 大型主機最多可有240個核心。 不過，z14 大型主機幾乎不會針對單一應用程式或工作負載使用所有核心。 相反地，大型主機會將工作負載隔離到邏輯分割區 (Lpar) ，而 Lpar 具有評等（MIPS (每秒數百萬個指示) 或 MSU (百萬個服務單位) 。 判斷在 Azure 上執行大型主機工作負載所需的可比較 VM 大小時，請考慮 MIPS (或 MSU) 評等。

以下是一般估計值：

-   150每個 vCPU 的 MIPS

-   1000每個處理器的 MIPS

若要在 LPAR 中判斷指定工作負載的正確 VM 大小，請先將工作負載的 VM 優化。 然後判斷所需的個 vcpu 數目。 保守估計值為每個 vCPU 150 MIPS。 例如，根據這種估計值，具有16個 vcpu 的 F 系列 VM 可以輕鬆地支援來自 2400 MIPS 之 LPAR 的 IBM Db2 工作負載。

## <a name="azure-compute-scale-up"></a>Azure 計算擴充

M 系列 Vm 在撰寫本文時，最多可擴大至128個 vcpu () 。 針對每個 vCPU 使用保守估計的 150 MIPS，M 系列 VM 等同于大約 19000 MIPS。 針對大型主機進行 MIPS 評估的一般規則是每個處理器 1000 MIPS。 Z14 大型主機最多可有24個處理器，並針對單一大型主機系統提供大約24000的 MIPS。

最大的單一 z14 大型主機大約有5000的 MIPS 超過 Azure 中可用的最大 VM。 但請務必比較工作負載的部署方式。 如果大型主機系統同時具有應用程式和關係資料庫，它們通常會部署在相同的實體大型主機上，每個都在自己的 LPAR 中。 在 Azure 上的相同解決方案通常會使用一個適用于應用程式的 VM，以及適用于資料庫的個別、適當大小的 VM 進行部署。

例如，如果 M64 可使用 vCPU 系統支援此應用程式，而且資料庫使用 M96 vCPU，則需要大約150個 vcpu，或大約是 24000 MIPS，如下圖所示。

![比較 24000 MIPS 的工作負載部署](media/mainframe-compute-mips.png)

方法是將 Lpar 遷移至個別 Vm。 然後，Azure 可輕鬆地相應增加到在單一大型主機系統上部署的大部分應用程式所需的大小。

## <a name="azure-compute-scale-out"></a>Azure 計算擴充

以 Azure 為基礎的解決方案的優點之一，就是向外擴充的能力。調整可讓應用程式使用的計算容量幾乎沒有限制。 Azure 支援多種方法來擴充計算能力：

- **在叢集之間進行負載平衡。** 在此案例中，應用程式可以使用 [負載平衡器](../../../../load-balancer/load-balancer-overview.md) 或資源管理員，在叢集中的多個 vm 之間分散工作負載。 如果需要更多計算容量，則會將其他 Vm 新增至叢集中。

- **虛擬機器擴展集。** 在此高載案例中，應用程式可以根據 VM 使用量擴充至其他 [計算資源](../../../../virtual-machine-scale-sets/overview.md) 。 當需求降低時，擴展集中的 Vm 數目也會降低，以確保有效率地使用計算能力。

- **PaaS 調整規模。** Azure PaaS 供應專案會調整計算資源。 例如， [Azure Service Fabric](../../../../service-fabric/service-fabric-overview.md) 會配置計算資源，以符合要求數量的增加。

- **Kubernetes 叢集。** Azure 上的應用程式可以針對指定的資源使用計算服務的 [Kubernetes](../../../../aks/concepts-clusters-workloads.md) 叢集。 Azure Kubernetes Service (AKS) 是一項受控服務，可在 Azure 上協調 Kubernetes 節點、集區和叢集。

若要選擇適當的方法來相應放大計算資源，請務必瞭解 Azure 和大型主機之間的差異。 重要的是，或（如果）資料是由計算資源所共用。 在 Azure 中，預設的資料 () 通常不會被多個 Vm 共用。 如果相應放大計算叢集中的多個 Vm 需要資料共用，共用資料必須位於支援這項功能的資源中。 在 Azure 上，資料共用牽涉到儲存體，如下列章節所討論。

## <a name="azure-compute-optimization"></a>Azure 計算優化

您可以優化 Azure 架構中的每個處理層。 針對每個環境使用最適合的 Vm 類型和功能。 下圖顯示在 Azure 中部署 Vm 以支援使用 Db2 之 CICS 應用程式的一種可能模式。 在主要站台中，會為生產、生產階段前和測試 VM 部署高可用性。 次要站台供備份和災害復原之用。

每一層也都可以提供適當的嚴重損壞修復服務。 例如，生產環境和資料庫 VM 可能需要熱復原或暖復原，而開發和測試 VM 則支援冷復原。

![支援嚴重損壞修復的高可用性部署](media/mainframe-compute-dr.png)

## <a name="next-steps"></a>接下來的步驟

- [大型主機遷移](/azure/architecture/cloud-adoption/infrastructure/mainframe-migration/overview)
- [Azure 虛擬機器上的大型主機重新裝載](../overview.md)
- [將大型主機儲存體移至 Azure](mainframe-storage-Azure.md)

### <a name="ibm-resources"></a>IBM 資源

- [在 IBM Z 平行 Sysplex](https://www.ibm.com/it-infrastructure/z/technologies/parallel-sysplex-resources)
- [IBM CICS 和結合功能：超越基本概念](https://www.redbooks.ibm.com/redbooks/pdfs/sg248420.pdf)
- [建立 Db2 pureScale 功能安裝所需的使用者](https://www.ibm.com/support/knowledgecenter/en/SSEPGG_11.1.0/com.ibm.db2.luw.qb.server.doc/doc/t0055374.html?pos=2)
- [Db2icrt-建立實例命令](https://www.ibm.com/support/knowledgecenter/en/SSEPGG_11.1.0/com.ibm.db2.luw.admin.cmd.doc/doc/r0002057.html)
- [Db2 pureScale 叢集資料庫解決方案](https://www.ibmbigdatahub.com/blog/db2-purescale-clustered-database-solution-part-1)
- [IBM Data Studio](https://www.ibm.com/developerworks/downloads/im/data/index.html/)

### <a name="azure-government"></a>Azure Government

- [適用于大型主機應用程式的 Microsoft Azure Government 雲端](https://azure.microsoft.com/resources/microsoft-azure-government-cloud-for-mainframe-applications/)
- [Microsoft 與 FedRAMP](https://www.microsoft.com/TrustCenter/Compliance/FedRAMP)

### <a name="more-migration-resources"></a>更多的遷移資源

- [Azure 虛擬資料中心：原形移轉指南](https://azure.microsoft.com/resources/azure-virtual-datacenter-lift-and-shift-guide/)
- [GlusterFS iSCSI](https://docs.gluster.org/en/latest/Administrator%20Guide/GlusterFS%20iSCSI/)
