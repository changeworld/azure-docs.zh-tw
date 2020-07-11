---
title: Azure 上 Oracle 資料庫的參考架構 |Microsoft Docs
description: 參考在 Microsoft Azure 虛擬機器上執行 Oracle Database Enterprise Edition 資料庫的架構。
services: virtual-machines-linux
author: rgardler
manager: ''
tags: ''
ms.service: virtual-machines
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 12/13/2019
ms.author: rogardle
ms.custom: ''
ms.openlocfilehash: 9125d8d2177b9bc40bb280f414cdfb2797ccf8fe
ms.sourcegitcommit: f844603f2f7900a64291c2253f79b6d65fcbbb0c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/10/2020
ms.locfileid: "86221607"
---
# <a name="reference-architectures-for-oracle-database-enterprise-edition-on-azure"></a>Azure 上 Oracle Database Enterprise Edition 的參考架構

本指南詳細說明在 Azure 上部署高可用性 Oracle 資料庫的資訊。 此外，本指南也會探討嚴重損壞修復考慮。 這些架構是根據客戶部署而建立的。 本指南僅適用于 Oracle Database Enterprise Edition。

如果您想要深入瞭解如何將 Oracle 資料庫的效能最大化，請參閱[架構設計 Oracle DB](oracle-design.md)。

## <a name="assumptions"></a>假設

- 您已瞭解 Azure 的不同概念，例如[可用性區域](../../../availability-zones/az-overview.md)
- 您正在執行 Oracle Database Enterprise Edition 12c 或更新版本
- 當您使用本文中的解決方案時，您會知道並確認授權的含意

## <a name="high-availability-for-oracle-databases"></a>Oracle 資料庫的高可用性

在雲端中達到高可用性是每個組織規劃與設計的重要部分。 Microsoft Azure 提供[可用性區域](../../../availability-zones/az-overview.md)和可用性設定組， (用於可用性區域無法使用) 的區域。 深入瞭解[管理虛擬機器的可用性](../../../virtual-machines/linux/manage-availability.md)以設計雲端。

除了雲端原生工具和供應專案，Oracle 也提供高可用性解決方案，例如[Oracle Data guard](https://docs.oracle.com/en/database/oracle/oracle-database/18/sbydb/introduction-to-oracle-data-guard-concepts.html#GUID-5E73667D-4A56-445E-911F-1E99092DD8D7)、 [DATA guard with FSFO](https://docs.oracle.com/en/database/oracle/oracle-database/12.2/dgbkr/index.html)、[分區化](https://docs.oracle.com/en/database/oracle/oracle-database/12.2/admin/sharding-overview.html)和[GoldenGate](https://www.oracle.com/middleware/technologies/goldengate.html) ，可在 Azure 上設定。 本指南涵蓋每個解決方案的參考架構。

最後，在遷移或建立雲端應用程式時，請務必調整您的應用程式程式碼，以新增雲端原生模式，例如[重試模式](https://docs.microsoft.com/azure/architecture/patterns/retry)和[斷路器模式](https://docs.microsoft.com/azure/architecture/patterns/circuit-breaker)。 在[雲端設計模式指南](https://docs.microsoft.com/azure/architecture/patterns/)中定義的其他模式可協助您的應用程式更有彈性。

### <a name="oracle-rac-in-the-cloud"></a>雲端中的 Oracle RAC

Oracle Real Application Cluster (RAC) 是 Oracle 的解決方案，可讓許多實例存取一個資料庫儲存體 (共用-所有架構模式) ，以協助客戶達到高輸送量。 雖然 Oracle RAC 也可以用於內部部署的高可用性，但獨立的 Oracle RAC 無法用於雲端中的高可用性，因為它只會保護實例層級的失敗，而不是針對機架層級或資料中心層級的失敗。 基於這個理由，Oracle 建議在您的資料庫中使用 Oracle Data Guard， (單一實例或 RAC 是否) 以提供高可用性。 客戶通常需要高 SLA 來執行其任務關鍵性應用程式。 Oracle RAC 目前未通過認證，或 Azure 上的 Oracle 支援。 不過，Azure 提供的功能（例如 Azure 供應專案）可用性區域和規劃的維護期間，以協助防範實例層級的失敗。 除此之外，客戶也可以使用 Oracle Data Guard、Oracle GoldenGate 和 Oracle 分區化等技術，藉由保護其資料庫免于機架層級，以及資料中心層級和地理政治的失敗，來提供高效能和復原。

當跨多個[可用性區域](https://docs.microsoft.com/azure/availability-zones/az-overview)執行 oracle 資料庫搭配 Oracle Data Guard 或 GoldenGate 時，客戶可以取得99.99% 的執行時間 SLA。 在尚未提供可用性區域的 Azure 區域中，客戶可以使用[可用性設定組](https://docs.microsoft.com/azure/virtual-machines/linux/manage-availability#configure-multiple-virtual-machines-in-an-availability-set-for-redundancy)，並達成99.95% 的執行時間 SLA。

>注意：您的執行時間目標可能比 Microsoft 提供的執行時間 SLA 高得多。

## <a name="disaster-recovery-for-oracle-databases"></a>Oracle 資料庫的嚴重損壞修復

在雲端裝載您的任務關鍵性應用程式時，請務必針對高可用性和嚴重損壞修復進行設計。

針對 Oracle Database Enterprise Edition，Oracle Data Guard 是嚴重損壞修復的實用功能。 您可以在[配對的 Azure 區域](/azure/best-practices-availability-paired-regions)中設定待命資料庫實例，並設定資料保護容錯移轉以進行嚴重損壞修復。 針對零資料遺失，建議您除了使用中的資料保護之外，部署「最多」 Oracle Data Guard 的同步實例。 

如果您的應用程式允許延遲 (需要進行完整測試) ，請考慮在與 Oracle 主資料庫不同的可用性區域中，設定 Data Guard 的同步實例。 使用 [**最大可用性**模式]，將重做檔案的同步傳輸設定為同步實例。 這些檔案接著會以非同步方式傳輸到待命資料庫。 

如果您的應用程式不允許在**最大可用性**模式下的另一個可用性區域中設定同步處理實例時效能遺失 (同步) ，您可能會在與主資料庫相同的可用性區域中設定遠同步實例。 若要增加可用性，請考慮設定靠近主資料庫的多個遠同步實例，以及至少一個接近您待命資料庫的實例， (如果角色轉換) 。 如需深入瞭解 Oracle Data Guard，請參閱此[Oracle Active Data Guard 遠同步技術白皮書](https://www.oracle.com/technetwork/database/availability/farsync-2267608.pdf)。

使用 Oracle Standard Edition 資料庫時，有一些 ISV 解決方案，例如 DBVisit 待命，可讓您設定高可用性和嚴重損壞修復。

## <a name="reference-architectures"></a>參考架構

### <a name="oracle-data-guard"></a>Oracle 資料保護

Oracle Data Guard 可確保企業資料的高可用性、資料保護和嚴重損壞修復。 Data Guard 會將待命資料庫保留為主資料庫的交易一致性複本。 視主要和次要資料庫之間的距離，以及延遲的應用程式承受度而定，您可以設定同步或非同步複寫。 然後，如果主資料庫因為計畫或非計畫的中斷而無法使用，資料防護可以將任何待命資料庫切換到主要角色，將停機時間降到最低。 

使用 Oracle Data Guard 時，您也可以開啟次要資料庫以供唯讀之用。 這種設定稱為 Active Data Guard。 Oracle Database 12c 引進了一項功能，稱為「資料防護遠同步」實例。 這個實例可讓您設定 Oracle 資料庫的零資料遺失設定，而不需要犧牲效能。

> [!NOTE]
> Active Data Guard 需要額外的授權。 若要使用最多同步處理功能，也需要此授權。 請與您的 Oracle 代表交流，以討論授權的含意。

#### <a name="oracle-data-guard-with-fsfo"></a>具有 FSFO 的 Oracle Data Guard
具有快速啟動容錯移轉的 Oracle Data Guard (FSFO) 可以藉由在個別電腦上設定訊息代理程式，提供額外的復原功能。 Data Guard broker 和次要資料庫都會執行觀察者，並觀察主資料庫的停機時間。 這也允許您的 Data Guard 觀察者設定中的冗余。 

使用 Oracle Database 12.2 版和更新版本時，也可以使用單一 Oracle Data Guard broker 設定來設定多個觀察者。 這項設定可提供額外的可用性，以防一個觀察者和次要資料庫體驗停機。 Data Guard Broker 非常輕量，而且可以裝載于相對較小的虛擬機器上。 若要深入瞭解 Data Guard 訊息代理程式及其優點，請造訪本主題的[Oracle 檔](https://docs.oracle.com/en/database/oracle/oracle-database/12.2/dgbkr/oracle-data-guard-broker-concepts.html)。

下圖是在 Azure 上使用具有可用性區域之 Oracle Data Guard 的建議架構。 此架構可讓您取得99.99% 的 VM 執行時間 SLA。

![Oracle Database 搭配 Data Guard Broker 使用可用性區域-FSFO](./media/oracle-reference-architecture/oracledb_dg_fsfo_az.png)

在上圖中，用戶端系統會透過 web 來存取具有 Oracle 後端的自訂應用程式。 Web 前端是在負載平衡器中設定。 Web 前端會呼叫適當的應用程式伺服器來處理工作。 應用程式伺服器會查詢主要 Oracle 資料庫。 Oracle 資料庫已使用具有[限制核心個 vcpu](../../../virtual-machines/windows/constrained-vcpu.md)的超執行緒[記憶體優化虛擬機器](../../../virtual-machines/windows/sizes-memory.md)來設定，以節省授權成本並將效能最大化。 有多個 premium 或 ultra 磁片 (受控磁碟) 用於效能和高可用性。

Oracle 資料庫會放在多個可用性區域中，以提供高可用性。 每個區域都由一或多個配備獨立電源、冷卻和網路功能的資料中心組成。 為確保復原，所有已啟用的區域中至少會設定三個不同的區域。 區域內可用性區域的實體分隔會保護資料不受資料中心失敗的影響。 此外，會在兩個可用性區域間設定兩個 FSFO 觀察者，以便在發生中斷時，起始資料庫並將其容錯移轉至次要複本。 

您可以在不同的可用性區域中，設定其他觀察者和（或）待命資料庫 (AZ 1，在此案例中，) 與前述架構中所示的區域相同。 最後，oracle Enterprise Manager (OEM) 監視 Oracle 資料庫的執行時間和效能。 OEM 也可讓您產生各種效能和使用方式報告。

在不支援可用性區域的區域中，您可以使用可用性設定組，以高可用性的方式部署您的 Oracle Database。 可用性設定組可讓您達到99.95% 的 VM 執行時間。 下圖是此使用的參考架構：

![Oracle Database 搭配 Data Guard Broker 使用可用性設定組-FSFO](./media/oracle-reference-architecture/oracledb_dg_fsfo_as.png)

> [!NOTE]
> * Oracle Enterprise Manager VM 不需要放在可用性設定組中，因為只會部署一個 OEM 實例。
> * 目前不支援在可用性設定組設定中使用 Ultra 磁片。

#### <a name="oracle-data-guard-far-sync"></a>Oracle Data Guard 遠同步

Oracle Data Guard 同步處理為 Oracle 資料庫提供零個數據遺失保護功能。 如果您的資料庫電腦失敗，這項功能可讓您防止資料遺失。 Oracle Data Guard 的同步處理需要安裝在不同的 VM 上。 [目前同步] 是輕量的 Oracle 實例，只有控制檔案、密碼檔案、spfile 和待命記錄。 沒有資料檔案或 rego 記錄檔。 

對於零資料遺失保護，您的主資料庫與最遠的同步實例之間必須有同步通訊。 最遠的同步實例會以同步方式從主要複本接收重做，並以非同步方式將它立即轉送到所有待命資料庫。 這種設定也會降低主資料庫的負擔，因為它只需要將重做傳送到最多的同步處理實例，而不是所有的待命資料庫。 如果最多的同步實例失敗，Data Guard 會自動使用從主資料庫到次要資料庫的非同步傳輸，以維持近乎零的資料遺失保護。 針對新增的復原功能，客戶可能會為每個資料庫實例部署多個最遠的同步實例， (主要和次要) 。

下圖是使用 Oracle Data Guard 遠同步的高可用性架構：

![使用可用性區域與 Data Guard 同步的 Oracle 資料庫 & Broker-FSFO](./media/oracle-reference-architecture/oracledb_dg_fs_az.png)

在先前的架構中，有一個同步實例已部署在與資料庫實例相同的可用性區域中，以減少兩者之間的延遲。 如果應用程式具有延遲敏感性，請考慮將您的資料庫和最多的同步實例部署在[鄰近放置群組](../../../virtual-machines/linux/proximity-placement-groups.md)中。

下圖是使用 Oracle Data Guard FSFO 和同步處理的架構，以達到高可用性和嚴重損壞修復：

![Oracle Database 使用可用性區域來進行嚴重損壞修復，並將 Data Guard 同步 & Broker-FSFO](./media/oracle-reference-architecture/oracledb_dg_fs_az_dr.png)

### <a name="oracle-goldengate"></a>Oracle GoldenGate

GoldenGate 可讓您在整個企業的多個異類平臺之間，交換和操作交易層級的資料。 它會在您現有的基礎結構上移動具有交易完整性的已認可交易和最低負擔。 其模組化架構可讓您彈性地將選取的資料記錄、交易式變更和 DDL (資料定義語言的變更，) 在各種不同的拓撲中。

Oracle GoldenGate 可讓您藉由提供雙向複寫來設定資料庫的高可用性。 這可讓您設定**多**宿主或**主動-主動**設定。 下圖是 Azure 上的 Oracle GoldenGate 主動-主動設定的建議架構。 在下列架構中，已使用具有[限制核心個 vcpu](../../../virtual-machines/windows/constrained-vcpu.md)的超執行緒[記憶體優化虛擬機器](../../../virtual-machines/windows/sizes-memory.md)來設定 Oracle 資料庫，以節省授權成本並將效能最大化。 有多個 premium 或 ultra 磁片 (受控磁片) 用於效能和可用性。

![Oracle Database 搭配 Data Guard Broker 使用可用性區域-FSFO](./media/oracle-reference-architecture/oracledb_gg_az.png)

> [!NOTE]
> 在目前無法使用可用性區域的區域中，可以使用可用性設定組來設定類似的架構。

Oracle GoldenGate 具有可協助您以非同步方式將資料從一個 Oracle 資料庫伺服器複寫至另一個的進程（例如，解壓縮、提取和 Replicat）。 這些程式可讓您設定雙向複寫，以確保資料庫的高可用性（如果有可用性區域層級停機時間）。 在上圖中，解壓縮程式會在與 Oracle 資料庫相同的伺服器上執行，而資料幫浦和 Replicat 程式則是在相同的可用性區域中的個別伺服器上執行。 Replicat 程式是用來從另一個可用性區域中的資料庫接收資料，並將資料認可到其可用性區域中的 Oracle 資料庫。 同樣地，資料幫浦程式也會將已由解壓縮進程解壓縮的資料傳送至另一個可用性區域中的 Replicat 進程。 

雖然上述架構圖表會顯示在個別伺服器上設定的資料提取和 Replicat 程式，但您可以根據伺服器的容量和使用方式，在相同的伺服器上設定所有 Oracle GoldenGate 進程。 請務必查閱您的 AWR 報告和 Azure 中的計量，以瞭解您伺服器的使用模式。

在不同的可用性區域或不同區域中設定 Oracle GoldenGate 雙向複寫時，請務必確定您的應用程式可接受不同元件之間的延遲。 可用性區域與區域之間的延遲可能會有所不同，且取決於多個因素。 建議您在應用層和不同可用性區域和/或區域中的資料庫層之間設定效能測試，以確認它符合您的應用程式效能需求。

應用層可以在自己的子網中設定，而且資料庫層可以分隔成它自己的子網。 可能的話，請考慮使用[Azure 應用程式閘道](../../../application-gateway/overview.md)，對應用程式伺服器之間的流量進行負載平衡。 Azure 應用程式閘道是健全的 web 流量負載平衡器。 它提供以 cookie 為基礎的會話親和性，可將使用者會話保留在同一部伺服器上，藉此將資料庫上的衝突降到最低。 應用程式閘道的替代方案[Azure Load Balancer](../../../load-balancer/load-balancer-overview.md)和[Azure 流量管理員](../../../traffic-manager/traffic-manager-overview.md)。

### <a name="oracle-sharding"></a>Oracle 分區化

分區化是在 Oracle 12.2 中引進的資料層模式。 它可讓您在獨立的資料庫之間水準分割和調整您的資料。 它是一種無共用的架構，其中每個資料庫都裝載在專用的虛擬機器上，除了復原能力和提高可用性之外，還能提供高讀取和寫入輸送量。 此模式可消除單一失敗點、提供錯誤隔離，並啟用輪流升級而不需要停機。 一個分區或資料中心層級失敗的停機時間，並不會影響其他資料中心內其他分區的效能或可用性。 

分區化適用于無法承受任何停機時間的高輸送量 OLTP 應用程式。 具有相同分區化索引鍵的所有資料列一定會在相同的分區上，因此會增加提供高一致性的效能。 使用分區化的應用程式必須具有妥善定義的資料模型和資料散發策略 (一致的雜湊、範圍、清單或複合) ，其主要是使用分區化索引鍵來存取資料 (例如*customerId*或*accountNum*) 。 分區化也可讓您將特定的資料集儲存在更接近終端客戶的狀態，進而協助您符合效能和合規性需求。

建議您複寫分區，以取得高可用性和嚴重損壞修復。 您可以使用 oracle Data Guard 或 Oracle GoldenGate 之類的 Oracle 技術來完成這種設定。 複寫單位可以是分區、分區的一部分，或分區的群組。 分區化資料庫的可用性不會受到一或多個分區的中斷或緩慢影響。 為了達到高可用性，待命分區可以放在主要分區所在的同一個可用性區域中。 針對嚴重損壞修復，待命分區可以位於另一個區域。 您也可以在多個區域中部署分區，以在這些區域中提供流量。 在[Oracle 分區化檔](https://docs.oracle.com/en/database/oracle/oracle-database/19/shard/sharding-high-availability.html)中深入瞭解如何設定分區化資料庫的高可用性和複寫。

Oracle 分區化主要包含下列元件。 如需這些元件的詳細資訊，請參閱[Oracle 分區化檔](https://docs.oracle.com/en/database/oracle/oracle-database/19/shard/sharding-overview.html)：

- **分區目錄**-特殊用途的 Oracle 資料庫，這是所有分區資料庫設定資料的持續性存放區。 所有設定變更（例如新增或移除分區、資料的對應，以及分區資料庫中的 Ddl）都會在分區目錄上起始。 分區目錄也包含 SDB 中所有重復資料表的主要複本。 

  分區目錄會使用具體化視圖，自動複寫所有分區中重復資料表的變更。 分區目錄資料庫也可作為查詢協調器，用來處理未指定分區化索引鍵的多分區查詢和查詢。 
  
  搭配使用 Oracle Data Guard 與可用性區域，以及分區目錄高可用性的可用性設定組，是建議的最佳作法。 分區目錄的可用性不會影響分區化資料庫的可用性。 分區目錄中的停機時間只會在資料保護容錯移轉完成的短暫期間，影響維護作業和多分區查詢。 線上交易會繼續由 SDB 來路由傳送及執行，而且不會受到目錄中斷的影響。

- **分區總監**-需要在分區所在的每個區域/可用性區域中部署的輕量服務。 分區總監是部署在 Oracle 分區化內容中的全域服務管理員。 為達高可用性，建議您在分區所在的每個可用性區域中至少部署一個分區主管。 

  一開始連接到資料庫時，路由資訊是由分區主管所設定，並會針對後續要求進行快取，並略過分區總監。 一旦使用分區建立會話，就會支援所有 SQL 查詢和目前，並在指定的分區範圍內執行。 此路由會快速，並用於執行內部分區交易的所有 OLTP 工作負載。 建議對所有需要最高效能和可用性的 OLTP 工作負載使用直接路由。 當分區變得無法使用或分區化拓撲發生變更時，路由快取會自動重新整理。 
  
  針對高效能、資料相依路由，Oracle 建議在存取分區化資料庫中的資料時使用連接集區。 Oracle 連接集區、語言特定程式庫和驅動程式支援 Oracle 分區化。 如需詳細資訊，請參閱[Oracle 分區化檔](https://docs.oracle.com/en/database/oracle/oracle-database/19/shard/sharding-overview.html#GUID-3D41F762-BE04-486D-8018-C7A210D809F9)。

- **全域服務**-全域服務與一般資料庫服務類似。 除了資料庫服務的所有屬性外，全域服務也具有分區化資料庫的屬性，例如用戶端與分區之間的區域親和性，以及複寫延遲承受度。 必須建立一個全域服務，才能對分區化資料庫進行讀取/寫入資料。 使用 Active Data Guard 並設定分區的唯讀複本時，您可以針對唯讀工作負載建立另一個 gGobal 服務。 用戶端可以使用這些全域服務來連接到資料庫。

- **分區資料庫**-分區資料庫是您的 Oracle 資料庫。 在具有快速啟動容錯移轉的 Broker 設定中，每個資料庫都會使用 Oracle Data Guard 進行複寫 (FSFO) 啟用。 您不需要在每個分區上設定資料防護容錯移轉和複寫。 這會在建立共用資料庫時自動設定和部署。 如果特定分區失敗，Oracle 共用會自動將資料庫連線從主要複本故障切換到待命。

您可以使用兩個介面來部署和管理 Oracle 分區化資料庫： Oracle Enterprise Manager 雲端控制 GUI 和（或 `GDSCTL` ）命令列公用程式。 您甚至可以使用雲端控制來監視不同分區的可用性和效能。 此 `GDSCTL DEPLOY` 命令會自動建立分區及其各自的接聽程式。 此外，此命令會自動部署系統管理員所指定的分區層級高可用性所使用的複寫設定。

分區資料庫的方式有很多種：

* 系統管理的分區化-使用資料分割自動散發跨分區
* 使用者定義的分區化-可讓您指定資料與分區的對應，當有法規或資料當地語系化需求時，這項功能就能正常運作) 
* 複合分區化-針對不同_shardspaces_的系統管理和使用者定義分區化的組合
* 資料表子分區-類似于一般資料分割資料表。

閱讀更多有關 Oracle 檔中不同[分區化方法](https://docs.oracle.com/en/database/oracle/oracle-database/19/shard/sharding-methods.html)的資訊。

雖然分區化資料庫看起來像是應用程式和開發人員的單一資料庫，但在從非分區化資料庫移轉到分區化資料庫時，必須仔細規劃，以判斷哪些資料表會重複，而不是分區化。 

重複的資料表會儲存在所有分區，而分區化資料表則會分散到不同的分區。 建議是複製小型和維度資料表，並散發/分區事實資料表。 您可以使用分區目錄做為中央協調器，或在每個分區上執行資料幫浦，將資料載入至分區化資料庫。 深入瞭解如何[將資料移轉至 Oracle 檔中的分區化資料庫](https://docs.oracle.com/en/database/oracle/oracle-database/19/shard/sharding-loading-data.html)。

#### <a name="oracle-sharding-with-data-guard"></a>具有 Data Guard 的 Oracle 分區化

Oracle Data Guard 可用於分區化系統管理的使用者定義和複合分區化方法。

下圖是 Oracle 分區化的參考架構，其中包含用於每個分區高可用性的 Oracle Data Guard。 架構圖顯示_複合分區化方法_。 對於資料位置、負載平衡、高可用性、嚴重損壞修復等不同需求的應用程式而言，架構圖表可能會有所不同，而且可能會使用不同的方法來進行分區化。 Oracle 分區化可讓您符合這些需求，並藉由提供這些選項，以水準且有效率的方式進行調整。 您甚至可以使用 Oracle GoldenGate 來部署類似的架構。

![搭配 Data Guard Broker 使用可用性區域的 Oracle Database 分區化-FSFO](./media/oracle-reference-architecture/oracledb_dg_sh_az.png)

雖然系統管理的分區化是最簡單的設定和管理，但使用者定義的分區化或複合分區化非常適合用於異地散發資料和應用程式的案例，或是在您需要控制每個分區之複寫的案例中。 

在上述架構中，複合分區化是用來異地散發資料，並以水準方式相應放大您的應用層。 複合分區化是系統管理和使用者定義分區化的組合，因此提供這兩種方法的優點。 在上述案例中，資料會先分區化到以區域分隔的多個 shardspaces。 然後，資料會在 shardspace 中的多個分區上，以一致的雜湊進一步分割。 每個 shardspace 都包含多個 shardgroups。 每個 shardgroup 都有多個分區，而是複寫的「單位」，在此情況下為。 每個 shardgroup 都包含 shardspace 中的所有資料。 Shardgroups A1 和 B1 是主要 Shardgroups，而 Shardgroups A2 和 B2 則是待命。 您可以選擇讓個別分區成為複寫的單位，而不是 shardgroup。

在上述架構中，會在每個可用性區域中部署 GSM/分區總監以提供高可用性。 建議您為每個資料中心/區域部署至少一個 GSM/分區總監。 此外，應用程式伺服器的實例會部署在包含 shardgroup 的每個可用性區域中。 此設定可讓應用程式將應用程式伺服器和資料庫/shardgroup 之間的延遲保持在最低。 如果資料庫失敗，則在資料庫角色轉換發生後，與待命資料庫位於相同區域中的應用程式伺服器可以處理要求。 Azure 應用程式閘道和分區總監會追蹤要求和回應延遲，並據以路由要求。

從應用程式的觀點來看，用戶端系統會提出要求，以 Azure 應用程式閘道 (或 Azure) 中的其他負載平衡技術，將要求重新導向至最接近用戶端的區域。 Azure 應用程式閘道也支援「手寫」會話，因此來自相同用戶端的所有要求都會路由傳送至相同的應用程式伺服器。 應用程式伺服器會使用資料存取驅動程式中的連接共用。 這項功能可在 JDBC、ODP.NET、OCI 等驅動程式中使用。驅動程式可以辨識要求中指定的分區化金鑰。 [Oracle 通用連接集區 (UCP) ](https://docs.oracle.com/en/database/oracle/oracle-database/12.2/jjucp/ucp-database-sharding-overview.html) for JDBC 用戶端可以讓非 oracle 應用程式用戶端（例如 Apache TOMCAT 和 IIS）與 oracle 分區化搭配使用。 

在初始要求期間，應用程式伺服器會連線到其區域中的分區主管，以取得需要路由傳送要求的分區路由資訊。 根據傳遞的分區化金鑰，此主管會將應用程式伺服器路由至個別的分區。 應用程式伺服器會藉由建立對應來快取這項資訊，並在後續要求中略過分區總監，並將要求直接路由傳送至分區。

#### <a name="oracle-sharding-with-goldengate"></a>使用 GoldenGate 的 Oracle 分區化

下圖是 Oracle 分區化的參考架構，其具有 Oracle GoldenGate，可用於每個分區的區域內高可用性。 相對於上述架構，此架構只會會單一 Azure 區域內的高可用性， (多個可用性區域) 。 其中一個可以部署多區域的高可用性分區化資料庫 (與上述範例) 使用 Oracle GoldenGate 相同。

![使用可用性區域搭配 GoldenGate Oracle Database 分區化](./media/oracle-reference-architecture/oracledb_gg_sh_az.png)

上述參考架構會使用_系統管理_的分區化方法來分區資料。 由於 Oracle GoldenGate 複寫是在區塊層級進行，因此複寫至一個分區的一半資料可以複寫至另一個分區。 另一半則可以複寫到不同的分區。 

複寫資料的方式取決於複寫因素。 複寫因數為2時，您會在 shardgroup 中的三個分區上有兩個數據區塊的複本。 同樣地，在您的 shardgroup 中，使用複寫因數3和三個分區，每個分區中的所有資料都會複寫到 shardgroup 中的每個其他分區。 Shardgroup 中的每個分區都可以有不同的複寫因素。 此設定可協助您在 shardgroup 內及跨多個 shardgroups 有效率地定義高可用性和嚴重損壞修復設計。

在上述架構中，shardgroup A 和 shardgroup B 都包含相同的資料，但位於不同的可用性區域中。 如果 shardgroup A 和 shardgroup B 具有相同的複寫因數3，則分區化資料表的每個資料列/區塊會在兩個 shardgroups 之間複寫6次。 如果 shardgroup A 的複寫因數為3，而 shardgroup B 的複寫因數為2，則每個資料列/區塊會跨兩個 shardgroups 複寫5次。

如果發生實例層級或可用性區域層級失敗，此設定可防止資料遺失。 應用層能夠讀取和寫入每個分區。 為了讓衝突減到最少，Oracle 分區化會針對每個雜湊值範圍指定「主要區塊」。 這項功能可確保將特定區塊的寫入要求導向至對應的區塊。 此外，Oracle GoldenGate 也提供自動衝突偵測和解決方式，以處理可能發生的任何衝突。 如需使用 Oracle 分區化來執行 GoldenGate 的詳細資訊和限制，請參閱 Oracle 的檔有關搭配[分區化資料庫使用 Oracle GoldenGate](https://docs.oracle.com/en/database/oracle/oracle-database/19/shard/sharding-high-availability.html#GUID-4FC0AC46-0B8B-4670-BBE4-052228492C72)。

在上述架構中，會在每個可用性區域中部署 GSM/分區總監以提供高可用性。 建議您為每個資料中心或區域部署至少一個 GSM/分區總監。 此外，應用程式伺服器的實例會部署在包含 shardgroup 的每個可用性區域中。 此設定可讓應用程式將應用程式伺服器和資料庫/shardgroup 之間的延遲保持在最低。 如果資料庫失敗，則在資料庫角色轉換後，與待命資料庫位於相同區域中的應用程式伺服器可以處理要求。 Azure 應用程式閘道和分區總監會追蹤要求和回應延遲，並據以路由要求。

從應用程式的觀點來看，用戶端系統會提出要求，以 Azure 應用程式閘道 (或 Azure) 中的其他負載平衡技術，將要求重新導向至最接近用戶端的區域。 Azure 應用程式閘道也支援「手寫」會話，因此來自相同用戶端的所有要求都會路由傳送至相同的應用程式伺服器。 應用程式伺服器會使用資料存取驅動程式中的連接共用。 這項功能可在 JDBC、ODP.NET、OCI 等驅動程式中使用。驅動程式可以辨識要求中指定的分區化金鑰。 [Oracle 通用連接集區 (UCP) ](https://docs.oracle.com/en/database/oracle/oracle-database/12.2/jjucp/ucp-database-sharding-overview.html) for JDBC 用戶端可以讓非 oracle 應用程式用戶端（例如 Apache TOMCAT 和 IIS）與 oracle 分區化搭配使用。 

在初始要求期間，應用程式伺服器會連線到其區域中的分區主管，以取得需要路由傳送要求的分區路由資訊。 根據傳遞的分區化金鑰，此主管會將應用程式伺服器路由至個別的分區。 應用程式伺服器會藉由建立對應來快取這項資訊，並在後續要求中略過分區總監，並將要求直接路由傳送至分區。

## <a name="patching-and-maintenance"></a>修補與維護

將您的 Oracle 工作負載部署至 Azure 時，Microsoft 會負責所有主機 OS 層級的修補。 所有規劃的 OS 層級維護都會事先向客戶傳達，以允許客戶進行此規劃的維護。 來自兩個不同可用性區域的兩部伺服器不會同時進行修補。 如需 VM 維護和修補的詳細資訊，請參閱[管理虛擬機器的可用性](../../../virtual-machines/linux/manage-availability.md)。 

您可以使用[Azure 自動化](../../../automation/automation-tutorial-update-management.md)來自動修補虛擬機器作業系統。 您可以使用[Azure Pipelines](/azure/devops/pipelines/get-started/what-is-azure-pipelines?view=azure-devops)或[Azure 自動化](../../../automation/automation-tutorial-update-management.md)來自動修補和維護 Oracle 資料庫，以將停機時間降到最低。 請參閱[持續傳遞和藍色/綠色部署](/azure/devops/learn/what-is-continuous-delivery)，以瞭解如何在 Oracle 資料庫的內容中使用它。

## <a name="architecture-and-design-considerations"></a>架構和設計考慮

- 請考慮使用超執行緒[記憶體優化的虛擬機器](../../../virtual-machines/windows/sizes-memory.md)，並將[受限制的核心個 vcpu](../../../virtual-machines/windows/constrained-vcpu.md)用於您的 Oracle Database VM，以節省授權成本並將效能最大化。 使用多個 premium 或 ultra 磁片 (受控磁片) 以取得效能和可用性。
- 使用受控磁片時，磁片/裝置名稱可能會在重新開機時變更。 建議您使用裝置 UUID，而不要使用名稱，以確保您的掛接會在重新開機時保存。 您可以在[這裡](../../../virtual-machines/linux/configure-raid.md#add-the-new-file-system-to-etcfstab)找到詳細資訊。
- 使用可用性區域，以在區域中達到高可用性。
- 當您的 Oracle 資料庫可用) 或 premium 磁片時，請考慮使用 ultra 磁片 (。
- 請考慮使用 Oracle Data Guard 在另一個 Azure 區域中設定待命 Oracle 資料庫。
- 請考慮使用[鄰近放置群組](../../../virtual-machines/linux/co-location.md#proximity-placement-groups)，以減少應用程式與資料庫層之間的延遲。
- 設定[Oracle Enterprise Manager](https://docs.oracle.com/en/enterprise-manager/)以進行管理、監視和記錄。
- 請考慮使用 Oracle 自動儲存體管理 (ASM) ，以簡化資料庫的儲存體管理。
- 使用[Azure Pipelines](/azure/devops/pipelines/get-started/what-is-azure-pipelines)管理資料庫的修補和更新，而不需要停機。
- 調整您的應用程式程式碼，以新增雲端原生模式，例如[重試模式](/azure/architecture/patterns/retry)、[斷路器模式](/azure/architecture/patterns/circuit-breaker)，以及在[雲端設計模式指南](/azure/architecture/patterns/)中定義的其他模式，可協助您的應用程式更具彈性。

## <a name="next-steps"></a>後續步驟

請參閱下列適用于您案例的 Oracle 參考文章。

- [Oracle Data Guard 簡介](https://docs.oracle.com/en/database/oracle/oracle-database/18/sbydb/introduction-to-oracle-data-guard-concepts.html#GUID-5E73667D-4A56-445E-911F-1E99092DD8D7)
- [Oracle Data Guard Broker 概念](https://docs.oracle.com/en/database/oracle/oracle-database/12.2/dgbkr/oracle-data-guard-broker-concepts.html)
- [設定主動-主動高可用性的 Oracle GoldenGate](https://docs.oracle.com/goldengate/1212/gg-winux/GWUAD/wu_bidirectional.htm#GWUAD282)
- [Oracle 分區化總覽](https://docs.oracle.com/en/database/oracle/oracle-database/19/shard/sharding-overview.html)
- [Oracle Active Data Guard 會在任何距離完全同步零資料遺失](https://www.oracle.com/technetwork/database/availability/farsync-2267608.pdf)
