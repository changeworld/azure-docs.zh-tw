---
title: 將大型主機計算移至 Azure 虛擬機器
description: Azure 計算資源相較于大型主機容量，因此您可以遷移和現代化 IBM z14 應用程式。
author: njray
ms.author: larryme
ms.date: 04/02/2019
ms.topic: article
ms.service: multiple
ms.openlocfilehash: 97f354d0a313d58c671366dd0e5f485504823e13
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "76288926"
---
# <a name="move-mainframe-compute-to-azure"></a>將大型主機計算移至 Azure

大型主機具有高可靠性和可用性的信譽，並繼續成為許多企業的信任骨幹。 它們通常會被認為幾乎不會有無限的擴充性和運算能力。 不過，有些企業已不再最大的可用大型主機功能。 如果這聽起來像您，Azure 提供了靈活性、觸達和基礎結構的節約。

若要在 Microsoft Azure 上執行大型主機工作負載，您需要知道您的大型主機計算功能與 Azure 之間的比較。 本文會根據 IBM z14 大型主機（本文撰寫的最新模型），告訴您如何在 Azure 上取得可比較的結果。

若要開始使用，請並排考慮環境。 下圖比較用來執行應用程式至 Azure 裝載環境的大型主機環境。

![Azure 服務和模擬環境提供可比較的支援並簡化遷移](media/mainframe-compute-azure.png)

大型主機的威力通常用於處理數千名使用者之數百萬項更新的線上交易處理（OLTP）系統。 這些應用程式通常會使用軟體進行交易處理、螢幕處理和表單輸入。 他們可以使用客戶資訊控制系統（CICS）、資訊管理系統（IMS）或交易介面套件（TIP）。

如圖所示，Azure 上的 TPM 模擬器可以處理 CICS 和 IMS 工作負載。 Azure 上的 batch 系統模擬器會執行工作控制語言（JCL）的角色。 大型主機資料會遷移至 Azure 資料庫，例如 Azure SQL Database。 Azure 服務或裝載于 Azure 虛擬機器中的其他軟體可用於系統管理。

## <a name="mainframe-compute-at-a-glance"></a>大型主機計算一覽

在 z14 大型主機中，處理器會以最多四個*抽屜*排列。 「*抽屜*」只是處理器和晶片的叢集。 每個抽屜都可以有六個使用中的中央處理器（CP）晶片，而每個 CP 都有10個系統控制器（SC）晶片。 在 Intel x86 術語中，每個選單有六個通訊端，每個通訊端10個核心，以及四個抽屜。 此架構會針對 z14 提供相當於24個通訊端和240核心（最大）的功能。

快速 z14 CP 具有 5.2 GHz 頻率速度。 通常，z14 會隨方塊中的所有 CPs 一起傳遞。 它們會視需要啟用。 客戶通常會針對每個月至少收取四小時的計算時間，而非實際使用量。

您可以將大型主機處理器設定為下列其中一種類型：

- 一般用途（GP）處理器
- System z 整合式資訊處理器（zIIP）
- Linux （IFL）處理器的整合設施
- 系統協助處理器（SAP）
- 整合式結合設施（ICF）處理器

## <a name="scaling-mainframe-compute-up-and-out"></a>向上和向外調整大型主機計算

IBM 大型主機提供相應增加至240核心（單一系統的目前 z14 大小）的能力。 此外，IBM 大型主機可以透過稱為「結合設備」（CF）的功能進行相應放大。 CF 可讓多個大型主機系統同時存取相同的資料。 使用 CF，大型主機平行 Sysplex 技術會將大型主機處理器分組到叢集。 當撰寫本指南時，Parallel Sysplex 功能支援32個64處理器的分組。 最多2048個處理器可以用這種方式來分組，以相應放大計算容量。

CF 可讓計算叢集與直接存取共用資料。 它用於鎖定資訊、快取資訊，以及共用資料資源的清單。 使用一或多個 CFs 的平行 Sysplex，可以視為「共用所有專案」相應放大計算叢集。 如需這些功能的詳細資訊，請參閱 IBM 網站上的[Parallel Sysplex ON Ibm Z](https://www.ibm.com/it-infrastructure/z/technologies/parallel-sysplex-resources) 。

應用程式可以使用這些功能來提供相應放大效能和高可用性。 如需 CICS 如何搭配 CF 來使用 Parallel Sysplex 的相關資訊，請下載[IBM CICS 和結合設施：超越基本](https://www.redbooks.ibm.com/redbooks/pdfs/sg248420.pdf)redbook。

## <a name="azure-compute-at-a-glance"></a>Azure 計算一覽

有些人不小心認為 Intel 型伺服器的功能不如大型主機。 不過，新的核心密集 Intel 型系統具有與大型主機一樣多的計算容量。 本節說明適用于運算和儲存體的 Azure 基礎結構即服務（IaaS）選項。 Azure 也提供平臺即服務（PaaS）選項，但本文著重于提供可比較的大型主機容量的 IaaS 選擇。

Azure 虛擬機器提供各種大小和類型的計算能力。 在 Azure 中，虛擬 CPU （vCPU）大致等同于大型主機上的核心。

目前，Azure 虛擬機器大小的範圍提供1到128個 vcpu。 虛擬機器（VM）類型已針對特定工作負載優化。 例如，下列清單顯示 VM 類型（此撰寫的目前內容）和其建議用法：

| 大小     | 類型和描述                                                                 |
|----------|--------------------------------------------------------------------------------------|
| D 系列 | 使用 64 vCPU 的一般用途以及最高 3.5-GHz 的頻率速度                           |
| E 系列 | 最多64個 vcpu 的記憶體優化                                                 |
| F 系列 | 最高可達64個 vcpu 和 3. 7 GHz 頻率速度的計算優化                       |
| H 系列 | 已針對高效能運算（HPC）應用程式優化                          |
| L 系列 | 儲存體已針對高輸送量的應用程式優化，以資料庫（如 NoSQL）為後盾 |
| M 系列 | 最大計算和記憶體優化的 Vm，最多128個 vcpu                        |

如需可用 Vm 的詳細資訊，請參閱[虛擬機器系列](https://azure.microsoft.com/pricing/details/virtual-machines/series/)。

Z14 大型主機最多可以有240核心。 不過，z14 大型主機幾乎不會針對單一應用程式或工作負載使用所有核心。 相反地，大型主機會將工作負載都會隔離至邏輯分割區（Lpar），而 Lpar 的評等為 MIPS （每秒數百萬個指令）或 MSU （百萬個服務單位）。 判斷在 Azure 上執行大型主機工作負載所需的可比較 VM 大小時，請考慮 MIPS （或 MSU）分級。

以下是一般估計值：

-   150每個 vCPU 的 MIPS

-   1000每個處理器的 MIPS

若要為 LPAR 中指定的工作負載判斷正確的 VM 大小，請先將 VM 的工作負載優化。 然後決定所需的個 vcpu 數目。 針對每個 vCPU，保守的估計值為 150 MIPS。 例如，以這種估計值為基礎，具有16個 vcpu 的 F 系列 VM 可以輕鬆地支援來自具有 2400 MIPS 之 LPAR 的 IBM Db2 工作負載。

## <a name="azure-compute-scale-up"></a>Azure 計算相應增加

M 系列 Vm 可以相應增加至128個 vcpu （在本文撰寫時）。 針對每個 vCPU 使用保守估計的 150 MIPS，M 系列 VM 等同于大約19000的 MIPS。 針對大型主機評估 MIPS 的一般規則是每個處理器1000的 MIPS。 Z14 大型主機最多可以有24個處理器，並針對單一大型主機系統提供大約24000的 MIPS。

最大的單一 z14 大型主機具有大約5000的 MIPS，而不是 Azure 中可用的最大 VM。 但請務必比較工作負載的部署方式。 如果大型主機系統同時具有應用程式和關係資料庫，它們通常會部署在相同的實體大型主機上，每個都在自己的 LPAR 中。 Azure 上的相同解決方案通常是使用應用程式的一個 VM，以及適用于資料庫的個別、適當大小的 VM 來部署。

例如，如果 M64 vCPU 系統支援應用程式，而且針對資料庫使用 M96 vCPU，則需要約150個 vcpu，或大約是 24000 MIPS，如下圖所示。

![比較 24000 MIPS 的工作負載部署](media/mainframe-compute-mips.png)

方法是將 Lpar 遷移至個別的 Vm。 然後，Azure 可以輕鬆地相應增加到部署在單一大型主機系統上的大部分應用程式所需的大小。

## <a name="azure-compute-scale-out"></a>Azure 計算相應放大

以 Azure 為基礎的解決方案有一項優點，就是能夠相應放大。調整可讓應用程式有幾乎無限的計算容量。 Azure 支援多種方法來相應放大計算能力：

- **叢集間的負載平衡。** 在此案例中，應用程式可以使用[負載平衡器](/azure/load-balancer/load-balancer-overview)或資源管理員，在叢集中的多個 vm 之間散佈工作負載。 如果需要更多計算容量，則會將其他 Vm 新增至叢集。

- **虛擬機器擴展集。** 在此高載案例中，應用程式可以根據 VM 使用量調整為額外的[計算資源](/azure/virtual-machine-scale-sets/overview)。 當需求下降時，擴展集中的 Vm 數目也可以減少，以確保能有效率地使用計算能力。

- **PaaS 調整。** Azure PaaS 供應專案會調整計算資源。 例如， [Azure Service Fabric](/azure/service-fabric/service-fabric-overview)會配置計算資源，以符合要求量的增加。

- **Kubernetes 叢集。** Azure 上的應用程式可以針對指定的資源，使用適用于計算服務的[Kubernetes](/azure/aks/concepts-clusters-workloads)叢集。 Azure Kubernetes Service （AKS）是一種受控服務，可協調 Azure 上的 Kubernetes 節點、集區和叢集。

若要選擇相應放大計算資源的正確方法，請務必瞭解 Azure 和主機的差異。 金鑰是指計算資源是否共用資料，或（如果）。 在 Azure 中，資料（預設）通常不會由多個 Vm 共用。 如果相應放大計算叢集中的多個 Vm 需要資料共用，共用資料必須位於支援這項功能的資源中。 在 Azure 上，資料共用牽涉到儲存體，如下節所討論。

## <a name="azure-compute-optimization"></a>Azure 計算優化

您可以將 Azure 架構中的每一層處理優化。 針對每個環境使用最適合的 Vm 和功能類型。 下圖顯示在 Azure 中部署 Vm 以支援使用 Db2 之 CICS 應用程式的一種可能模式。 在主要站台中，會為生產、生產階段前和測試 VM 部署高可用性。 次要站台供備份和災害復原之用。

每一層也可以提供適當的嚴重損壞修復服務。 例如，生產環境和資料庫 VM 可能需要熱復原或暖復原，而開發和測試 VM 則支援冷復原。

![支援嚴重損壞修復的高可用性部署](media/mainframe-compute-dr.png)

## <a name="next-steps"></a>後續步驟

- [大型主機遷移](/azure/architecture/cloud-adoption/infrastructure/mainframe-migration/overview)
- [Azure 虛擬機器上的大型主機重新裝載](/azure/virtual-machines/workloads/mainframe-rehosting/overview)
- [將大型主機儲存體移至 Azure](mainframe-storage-Azure.md)

### <a name="ibm-resources"></a>IBM 資源

- [IBM Z 上的平行 Sysplex](https://www.ibm.com/it-infrastructure/z/technologies/parallel-sysplex-resources)
- [IBM CICS 和結合設施：超越基本概念](https://www.redbooks.ibm.com/redbooks/pdfs/sg248420.pdf)
- [建立 Db2 pureScale 功能安裝所需的使用者](https://www.ibm.com/support/knowledgecenter/en/SSEPGG_11.1.0/com.ibm.db2.luw.qb.server.doc/doc/t0055374.html?pos=2)
- [Db2icrt-建立實例命令](https://www.ibm.com/support/knowledgecenter/en/SSEPGG_11.1.0/com.ibm.db2.luw.admin.cmd.doc/doc/r0002057.html)
- [Db2 pureScale 叢集資料庫解決方案](https://www.ibmbigdatahub.com/blog/db2-purescale-clustered-database-solution-part-1)
- [IBM Data Studio](https://www.ibm.com/developerworks/downloads/im/data/index.html/)

### <a name="azure-government"></a>Azure Government

- [適用于大型主機應用程式的 Microsoft Azure Government 雲端](https://azure.microsoft.com/resources/microsoft-azure-government-cloud-for-mainframe-applications/)
- [Microsoft 和 FedRAMP](https://www.microsoft.com/TrustCenter/Compliance/FedRAMP)

### <a name="more-migration-resources"></a>更多遷移資源

- [Azure 虛擬資料中心：原形移轉指南](https://azure.microsoft.com/resources/azure-virtual-datacenter-lift-and-shift-guide/)
- [GlusterFS iSCSI](https://docs.gluster.org/en/latest/Administrator%20Guide/GlusterFS%20iSCSI/)
