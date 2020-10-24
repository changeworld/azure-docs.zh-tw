---
title: Azure 上 Oracle 資料庫的參考架構 |Microsoft Docs
description: 參考在 Microsoft Azure 虛擬機器上執行 Oracle Database Enterprise Edition 資料庫的架構。
author: dbakevlar
ms.service: virtual-machines-linux
ms.topic: article
ms.date: 12/13/2019
ms.author: kegorman
ms.reviewer: cynthn
ms.openlocfilehash: d2a6954ffdb9f992ada7fc24dbcc161658b21d23
ms.sourcegitcommit: 3bcce2e26935f523226ea269f034e0d75aa6693a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/23/2020
ms.locfileid: "92480423"
---
# <a name="reference-architectures-for-oracle-database-enterprise-edition-on-azure"></a>Azure 上 Oracle Database Enterprise Edition 的參考架構

本指南詳細說明在 Azure 上部署高可用性 Oracle 資料庫的詳細資訊。 此外，本指南探討嚴重損壞修復的考慮。 這些架構是根據客戶部署所建立。 本指南僅適用于 Oracle Database Enterprise Edition。

如果您有興趣深入瞭解如何將 Oracle 資料庫的效能最大化，請參閱 [架構 Oracle DB](oracle-design.md)。

## <a name="assumptions"></a>假設

- 您已瞭解 Azure 的不同概念，例如 [可用性區域](../../../availability-zones/az-overview.md)
- 您正在執行 Oracle Database Enterprise Edition 12c 或更新版本
- 當您使用本文中的解決方案時，您會瞭解並認可授權的含意

## <a name="high-availability-for-oracle-databases"></a>Oracle 資料庫的高可用性

在雲端中達到高可用性是每個組織規劃和設計的重要部分。 Microsoft Azure 提供 [可用性區域](../../../availability-zones/az-overview.md) 和可用性設定組， (在可用性區域無法使用) 的區域中使用。 深入瞭解如何 [管理虛擬機器的可用性](../../manage-availability.md) ，以設計雲端。

除了雲端原生工具和供應專案之外，Oracle 也提供高可用性的解決方案，例如[Oracle Data guard](https://docs.oracle.com/en/database/oracle/oracle-database/18/sbydb/introduction-to-oracle-data-guard-concepts.html#GUID-5E73667D-4A56-445E-911F-1E99092DD8D7)、具有 FSFO、[分區化](https://docs.oracle.com/en/database/oracle/oracle-database/12.2/admin/sharding-overview.html)和[GoldenGate](https://www.oracle.com/middleware/technologies/goldengate.html)的[Data guard](https://docs.oracle.com/en/database/oracle/oracle-database/12.2/dgbkr/index.html)（可在 Azure 上設定）。 本指南涵蓋每個解決方案的參考架構。

最後，在遷移或建立雲端的應用程式時，請務必調整您的應用程式程式碼，以新增雲端原生模式，例如 [重試模式](/azure/architecture/patterns/retry) 和 [斷路器模式](/azure/architecture/patterns/circuit-breaker)。 在 [雲端設計模式指南](/azure/architecture/patterns/) 中定義的其他模式，可以協助您的應用程式更具復原性。

### <a name="oracle-rac-in-the-cloud"></a>雲端中的 Oracle RAC

Oracle Real Application Cluster (RAC) 是 Oracle 的解決方案，可讓許多實例存取一個資料庫儲存體 (共用所有架構模式) ，以協助客戶達成高輸送量。 雖然 Oracle RAC 也可以用於內部部署的高可用性，但 Oracle RAC 本身無法用於雲端中的高可用性，因為它只會防止實例層級失敗，而不是針對機架層級或資料中心層級的失敗。 基於這個理由，Oracle 建議將 Oracle Data Guard 與您的資料庫搭配使用， (單一實例或 RAC 是否) 以獲得高可用性。 客戶通常需要高 SLA 才能執行其要徑任務應用程式。 Azure 上的 Oracle 目前未認證或支援 Oracle RAC。 不過，Azure 提供的功能如 Azure 提供可用性區域和規劃的維護期間，以協助防範實例層級的失敗。 除此之外，客戶也可以使用 Oracle Data Guard、Oracle GoldenGate 和 Oracle 分區化之類的技術，藉由保護其資料庫免于機架層級以及資料中心層級和異地政治失敗，來提供高效能和復原能力。

當跨多個 [可用性區域](../../../availability-zones/az-overview.md) 執行 oracle 資料庫與 Oracle Data Guard 或 GoldenGate 時，客戶可以取得99.99% 的執行時間 SLA。 在可用性區域尚未存在的 Azure 區域中，客戶可以使用 [可用性設定組](../../manage-availability.md#configure-multiple-virtual-machines-in-an-availability-set-for-redundancy) 並達成99.95% 的執行時間 SLA。

>注意：您的執行時間目標可能遠高於 Microsoft 提供的執行時間 SLA。

## <a name="disaster-recovery-for-oracle-databases"></a>Oracle 資料庫的嚴重損壞修復

在雲端中裝載您的任務關鍵性應用程式時，請務必設計高可用性和嚴重損壞修復。

針對 Oracle Database Enterprise Edition，Oracle Data Guard 是嚴重損壞修復的實用功能。 您可以在 [配對的 Azure 區域](../../../best-practices-availability-paired-regions.md) 中設定待命資料庫實例，並設定災難復原的資料防護容錯移轉。 針對零資料遺失，建議您除了使用中的資料保護之外，部署 Oracle Data Guard 的同步實例。 

如果您的應用程式允許延遲 (需要) 的完整測試，請考慮在與 Oracle 主資料庫不同的可用性區域中設定 Data Guard 的同步實例。 使用 [ **最大可用性** ] 模式，將重做檔案的同步傳輸設定為最遠的同步實例。 這些檔案接著會以非同步方式傳輸到待命資料庫。 

如果您的應用程式在 **最高可用性** 模式下的另一個可用性區域中設定最多的同步處理實例時，不允許效能遺失 (同步) ，您可以在與主資料庫相同的可用性區域中設定最多的同步實例。 若要增加可用性，請考慮將多個最接近您主資料庫的同步實例，以及至少一個接近您待命資料庫的實例， (如果角色轉換) 。 深入瞭解 Oracle Data Guard 在此 [Oracle Active Data guard](https://www.oracle.com/technetwork/database/availability/farsync-2267608.pdf)的同步技術白皮書。

使用 Oracle Standard Edition 資料庫時，有一些 ISV 解決方案，例如 DBVisit 待命，可讓您設定高可用性和嚴重損壞修復。

## <a name="reference-architectures"></a>參考架構

### <a name="oracle-data-guard"></a>Oracle 資料保護

Oracle Data Guard 可確保企業資料的高可用性、資料保護和嚴重損壞修復。 Data Guard 會以交易一致的主資料庫複本來維護待命資料庫。 根據主要和次要資料庫之間的距離，以及延遲的應用程式容錯，您可以設定同步或非同步複寫。 然後，如果主資料庫因為計畫或未規劃的中斷而無法使用，Data Guard 可以將任何待命資料庫切換到主要角色，將停機時間降到最低。 

使用 Oracle Data Guard 時，您也可以針對唯讀用途開啟您的次要資料庫。 這項設定稱為「使用中資料防護」。 Oracle Database 12c 引進了一個功能，稱為「資料防護」的同步實例。 此實例可讓您設定 Oracle 資料庫的零資料遺失設定，而不會犧牲效能。

> [!NOTE]
> Active Data Guard 需要額外的授權。 您也需要此授權，才能使用最遠的同步處理功能。 請與您的 Oracle 代表交流，以討論授權的含意。

#### <a name="oracle-data-guard-with-fsfo"></a>使用 FSFO 的 Oracle Data Guard
具有 Fast-Start 容錯移轉 (FSFO) 的 Oracle Data Guard 可在個別電腦上設定訊息代理程式，以提供額外的復原能力。 Data Guard 訊息代理程式和次要資料庫都會執行觀察器，並觀察主資料庫的停機時間。 如此一來，您也可以在資料防護觀察器設定中提供冗余。 

使用 Oracle Database 12.2 版和更新版本，您也可以使用單一 Oracle Data Guard broker 設定來設定多個觀察者。 這項設定可提供額外的可用性，以防一個觀察者和次要資料庫體驗停機。 Data Guard 訊息代理程式很輕量，可以裝載于相對較小的虛擬機器上。 若要深入瞭解 Data Guard Broker 及其優點，請流覽本主題的 [Oracle 檔](https://docs.oracle.com/en/database/oracle/oracle-database/12.2/dgbkr/oracle-data-guard-broker-concepts.html) 。

下圖是在 Azure 上搭配可用性區域使用 Oracle Data Guard 的建議架構。 此架構可讓您取得99.99% 的 VM 執行時間 SLA。

![此圖顯示在 Azure 上搭配可用性區域使用 Oracle Data Guard 的建議架構。](./media/oracle-reference-architecture/oracledb_dg_fsfo_az.png)

在上圖中，用戶端系統會透過 web 存取具有 Oracle 後端的自訂應用程式。 Web 前端是在負載平衡器中設定。 Web 前端會呼叫適當的應用程式伺服器來處理工作。 應用程式伺服器會查詢主要 Oracle 資料庫。 Oracle 資料庫已設定為使用超執行緒 [記憶體優化的虛擬機器](../../sizes-memory.md) ，具有 [受限的核心個 vcpu](../../../virtual-machines/constrained-vcpu.md) ，以節省授權成本並將效能最大化。  (受控磁碟) 的多重 premium 或 ultra 磁片，用於效能和高可用性。

Oracle 資料庫會放置在多個可用性區域中，以獲得高可用性。 每個區域都是由一或多個資料中心組成，配備了獨立的電源、冷卻和網路功能。 為確保復原能力，在所有已啟用的區域中至少會設定三個不同的區域。 區域內可用性區域的實體分隔可保護資料不受資料中心失敗影響。 此外，兩個 FSFO 觀察者會在兩個可用性區域間設定，以在發生中斷時起始資料庫並將其容錯移轉至次要資料庫。 

您可以在不同的可用性區域中設定其他的觀察者和/或待命資料庫 (AZ 1，在此案例中) 的是上述架構中所顯示的區域。 最後，oracle Enterprise Manager (OEM) 監視 Oracle 資料庫的執行時間和效能。 OEM 也可讓您產生各種效能和使用方式報告。

在不支援可用性區域的區域中，您可以使用可用性設定組，以高度可用的方式來部署您的 Oracle Database。 可用性設定組可讓您達到99.95% 的 VM 執行時間。 下圖是此用途的參考架構：

![Oracle Database 搭配使用可用性設定組與 Data Guard Broker-FSFO](./media/oracle-reference-architecture/oracledb_dg_fsfo_as.png)

> [!NOTE]
> * Oracle Enterprise Manager VM 不需要放在可用性設定組中，因為只有一個要部署的 OEM 實例。
> * 可用性設定組設定目前不支援 Ultra 磁片。

#### <a name="oracle-data-guard-far-sync"></a>Oracle Data Guard 遠同步

Oracle Data Guard 的同步處理為 Oracle 資料庫提供零資料遺失保護功能。 這項功能可讓您在資料庫電腦失敗時防止資料遺失。 Oracle Data Guard 同步需要安裝在不同的 VM 上。 目前的同步處理是輕量的 Oracle 實例，其中只包含控制檔案、密碼檔案、spfile 和待命記錄。 沒有資料檔案或 rego 記錄檔。 

針對零資料遺失保護，您的主資料庫與最遠的同步處理實例之間必須有同步通訊。 最遠的同步處理實例會以同步的方式接收來自主要複本的重做，並以非同步方式將它立即轉送到所有待命資料庫。 這種設定也可以減少主資料庫上的額外負荷，因為它只需要將重做傳送到最遠的同步處理實例，而不是所有的待命資料庫。 如果目前的同步處理實例失敗，Data Guard 會自動使用主資料庫之次要資料庫的非同步傳輸，以維持近乎零的資料遺失保護。 為了提高復原能力，客戶可以針對每個資料庫實例部署多個最多的同步處理實例， (主要和次要資料庫的) 。

下圖是使用 Oracle Data Guard 遠同步的高可用性架構：

![使用可用性區域搭配 Data Guard 的 Oracle 資料庫 & Broker FSFO](./media/oracle-reference-architecture/oracledb_dg_fs_az.png)

在上述架構中，與資料庫實例在相同的可用性區域中部署了最多的同步實例，以降低兩者之間的延遲。 如果應用程式有延遲敏感性，請考慮在 [鄰近放置群組](../../../virtual-machines/linux/proximity-placement-groups.md)中部署您的資料庫，以及最遠的同步實例或實例。

下圖是使用 Oracle Data Guard FSFO 和目前的同步處理來達成高可用性和嚴重損壞修復的架構：

![Oracle Database 使用可用性區域進行嚴重損壞修復，並透過 Data Guard & Broker FSFO](./media/oracle-reference-architecture/oracledb_dg_fs_az_dr.png)

### <a name="oracle-goldengate"></a>Oracle GoldenGate

GoldenGate 可讓您在整個企業的多個異類平臺之間，交換和操作交易層級的資料。 它會在交易完整性和您現有基礎結構上的額外負荷之間移動已認可的交易。 其模組化架構可讓您在各種不同的拓撲中，靈活地將選取的資料記錄、交易變更和 DDL (資料定義語言) 的變更解壓縮和複寫。

Oracle GoldenGate 可讓您藉由提供雙向複寫來設定資料庫的高可用性。 這可讓您設定 **多主機** 或 **主動-主動**設定。 下圖是針對 Azure 上的 Oracle GoldenGate 主動-主動設定建議的架構。 在下列架構中，已使用具有[受限核心個 vcpu](../../../virtual-machines/constrained-vcpu.md)的超執行緒[記憶體優化虛擬機器](../../sizes-memory.md)來設定 Oracle 資料庫，以節省授權成本並將效能最大化。 多個 premium 或 ultra 磁片 (受控磁片) 用於效能和可用性。

![Oracle Database 搭配使用可用性區域與 Data Guard Broker-FSFO](./media/oracle-reference-architecture/oracledb_gg_az.png)

> [!NOTE]
> 您可以在目前無法使用可用性區域的區域中，使用可用性設定組來設定類似的架構。

Oracle GoldenGate 有一些進程（例如解壓縮、提取和 Replicat），可協助您以非同步方式將資料從一部 Oracle 資料庫伺服器複寫到另一部。 這些程式可讓您設定雙向複寫，以確保資料庫的高可用性（如果有可用性區域層級停機）。 在上圖中，「解壓縮」程式會在與 Oracle 資料庫相同的伺服器上執行，而資料提取和 Replicat 進程會在相同可用性區域的不同伺服器上執行。 Replicat 程式是用來接收來自其他可用性區域中資料庫的資料，並將資料認可至可用性區域中的 Oracle 資料庫。 同樣地，資料幫浦程式會將解壓縮程式所解壓縮的資料傳送到另一個可用性區域中的 Replicat 進程。 

雖然上述架構圖表顯示在個別伺服器上設定的資料提取和 Replicat 程式，但您可以根據伺服器的容量和使用方式，在同一部伺服器上設定所有的 Oracle GoldenGate 進程。 請一律查閱您的 AWR 報表和 Azure 中的計量，以瞭解您伺服器的使用模式。

在不同的可用性區域或不同區域中設定 Oracle GoldenGate 雙向複寫時，請務必確定您的應用程式可接受不同元件之間的延遲。 可用性區域與區域之間的延遲可能會有所不同，而且取決於多個因素。 建議您在不同的可用性區域和/或區域中，于應用層和資料庫層之間設定效能測試，以確認它符合您的應用程式效能需求。

應用層可以在自己的子網中設定，而且資料庫層可以區隔成它自己的子網。 可能的話，請考慮使用 [Azure 應用程式閘道](../../../application-gateway/overview.md) 來對應用程式伺服器之間的流量進行負載平衡。 Azure 應用程式閘道是健全的 web 流量負載平衡器。 它提供以 cookie 為基礎的會話親和性，讓使用者會話保持在同一部伺服器上，因此可將資料庫上的衝突降至最低。 應用程式閘道的替代方案是 [Azure Load Balancer](../../../load-balancer/load-balancer-overview.md) 和 [Azure 流量管理員](../../../traffic-manager/traffic-manager-overview.md)。

### <a name="oracle-sharding"></a>Oracle 分區化

分區化是 Oracle 12.2 中引進的資料層模式。 它可讓您在獨立資料庫之間水準分割和調整資料。 它是一種無共用的架構，其中每個資料庫都裝載于專用的虛擬機器上，除了復原能力和更高的可用性之外，還可提供高讀取和寫入輸送量。 此模式可消除單一失敗點、提供錯誤隔離，並在不需要停機的情況下啟用輪流升級。 某一分區或資料中心層級失敗的停機時間不會影響其他資料中心內其他分區的效能或可用性。 

分區化適用于無法承受任何停機時間的高輸送量 OLTP 應用程式。 所有具有相同分區化索引鍵的資料列一定會在相同的分區上，因此會增加提供高一致性的效能。 使用分區化的應用程式必須具有妥善定義的資料模型和資料散發策略 (一致的雜湊、範圍、清單或複合) ，其主要是使用分區化索引鍵來存取資料 (例如， *customerId* 或 *accountNum*) 。 分區化也可讓您將特定的資料集儲存在更接近終端客戶的資料集，以協助您符合您的效能和合規性需求。

建議您複寫分區，以獲得高可用性和嚴重損壞修復。 您可以使用 oracle Data Guard 或 Oracle GoldenGate 這類 Oracle 技術來完成此設定。 複寫單位可以是分區、分區的一部分或分區的群組。 分區化資料庫的可用性不受一或多個分區的中斷或速度變慢所影響。 為了達到高可用性，待命分區可以放在放置主要分區的相同可用性區域中。 針對嚴重損壞修復，待命分區可以位於另一個區域。 您也可以在多個區域中部署分區，以提供這些區域中的流量。 深入瞭解如何在 [Oracle 分區化檔](https://docs.oracle.com/en/database/oracle/oracle-database/19/shard/sharding-high-availability.html)中設定分區化資料庫的高可用性和複寫。

Oracle 分區化主要是由下列元件所組成。 您可以在 [Oracle 分區化檔](https://docs.oracle.com/en/database/oracle/oracle-database/19/shard/sharding-overview.html)中找到有關這些元件的詳細資訊：

- **分區 catalog** -特殊用途的 Oracle 資料庫，這是所有分區資料庫設定資料的持續性存放區。 所有設定變更（例如新增或移除分區、資料的對應，以及分區資料庫中的 Ddl）都是在分區目錄上起始。 分區目錄也包含 SDB 中所有重復資料表的主要複本。 

  分區類別目錄會使用具體化視圖，自動將變更複寫到所有分區中的重復資料表。 分區目錄資料庫也可做為查詢協調器，用來處理未指定分區化索引鍵的多分區查詢和查詢。 
  
  建議的最佳作法是使用 Oracle Data Guard 搭配可用性區域或分區目錄高可用性的可用性設定組。 分區目錄的可用性不會影響分區化資料庫的可用性。 分區目錄中的停機時間，只會影響資料保護容錯移轉完成的短暫期間內的維護作業和 multishard 查詢。 線上交易會繼續由 SDB 路由傳送和執行，並不會受到目錄中斷的影響。

- **分區 director** -需要在您的分區所在的每個區域/可用性區域中部署的輕量服務。 分區總監是在 Oracle 分區化的環境中部署的全域服務管理員。 為了達到高可用性，建議您在分區存在的每個可用性區域中至少部署一個分區 director。 

  一開始連接到資料庫時，路由資訊是由分區 director 設定的，而且會針對後續要求快取，以略過分區 director。 使用分區建立會話之後，就會支援並在指定分區的範圍內執行所有 SQL 查詢和 Dml。 這種路由很快速，可用於執行內部分區交易的所有 OLTP 工作負載。 建議您針對所有需要最高效能和可用性的 OLTP 工作負載，使用直接路由。 當分區變得無法使用或分區化拓撲發生變更時，路由快取會自動重新整理。 
  
  針對高效能、資料相依路由，Oracle 建議在存取分區化資料庫中的資料時使用連接集區。 Oracle 連接集區、特定語言的程式庫和驅動程式都支援 Oracle 分區化。 如需詳細資訊，請參閱 [Oracle 分區化檔](https://docs.oracle.com/en/database/oracle/oracle-database/19/shard/sharding-overview.html#GUID-3D41F762-BE04-486D-8018-C7A210D809F9) 。

- **全域服務** -全域服務類似于一般資料庫服務。 除了資料庫服務的所有屬性之外，全域服務還具有分區化資料庫的屬性，例如用戶端與分區之間的區域親和性，以及複寫延遲容錯。 只需要建立一個全域服務，就可以在分區化資料庫中讀取/寫入資料。 使用 Active Data Guard 並設定分區的唯讀複本時，您可以為唯讀工作負載建立另一個 gGobal 服務。 用戶端可以使用這些全域服務連接到資料庫。

- **分區資料庫** -分區資料庫是您的 Oracle 資料庫。 在啟用 Fast-Start 容錯移轉 (FSFO) 的 Broker 設定中，會使用 Oracle Data Guard 來複寫每個資料庫。 您不需要在每個分區上設定「資料防護」容錯移轉和複寫。 這會在共用資料庫建立時自動設定和部署。 如果特定分區失敗，Oracle 共用會自動將資料庫連接從主要複本容錯移轉到待命。

您可以使用兩個介面來部署和管理 Oracle 分區化資料庫： Oracle Enterprise Manager Cloud Control GUI 和/或 `GDSCTL` 命令列公用程式。 您甚至可以使用雲端控制來監視不同分區的可用性和效能。 此 `GDSCTL DEPLOY` 命令會自動建立分區及其各自的接聽程式。 此外，此命令會自動部署用於系統管理員所指定之分區層級高可用性的複寫設定。

分區資料庫的方式有很多種：

* 系統管理的分區化-使用資料分割自動分配跨分區
* 使用者定義分區化-可讓您指定將資料對應至分區，這在有法規或資料當地語系化需求時可正常運作) 
* 複合分區化-適用于不同_shardspaces_之系統管理和使用者定義分區化的組合
* 資料表子分區-類似于一般分割資料表。

請參閱 Oracle 檔中不同 [分區化方法](https://docs.oracle.com/en/database/oracle/oracle-database/19/shard/sharding-methods.html) 的詳細資訊。

雖然分區化資料庫看起來像是應用程式和開發人員的單一資料庫，但在從非分區化資料庫移轉至分區化資料庫時，必須仔細規劃，以判斷哪些資料表將複製和分區化。 

複製的資料表會儲存在所有分區，而分區化資料表則會分散到不同的分區。 建議您複製小型和維度資料表，以及散發/分區事實資料表。 您可以使用分區類別目錄作為中央協調器，或在每個分區上執行資料幫浦，將資料載入分區化資料庫。 請參閱 Oracle 檔中 [的將資料移轉至分區化資料庫](https://docs.oracle.com/en/database/oracle/oracle-database/19/shard/sharding-loading-data.html) 的詳細資訊。

#### <a name="oracle-sharding-with-data-guard"></a>具有 Data Guard 的 Oracle 分區化

Oracle Data Guard 可以用來分區化具有系統管理、使用者定義和複合分區化方法的資料。

下圖是 Oracle 分區化的參考架構，適用于每個分區的高可用性使用 Oracle Data Guard。 架構圖顯示 _複合分區化方法_。 針對具有不同資料位置、負載平衡、高可用性、嚴重損壞修復等需求的應用程式，架構圖表可能會有所不同，而且可能會使用不同的方法來進行分區化。 Oracle 分區化可讓您藉由提供這些選項，以符合這些需求，並可水準且有效率地調整規模。 您甚至可以使用 Oracle GoldenGate 來部署類似的架構。

![使用可用性區域搭配 Data Guard Broker 的 Oracle Database 分區化-FSFO](./media/oracle-reference-architecture/oracledb_dg_sh_az.png)

雖然系統管理的分區化是最容易設定和管理的，但使用者定義的分區化或複合分區化非常適合您的資料和應用程式是異地散發的案例，或是您需要控制每個分區複寫的案例。 

在上述架構中，複合分區化是用來將資料異地散發，並以水準方式相應放大您的應用層。 複合分區化是系統管理和使用者定義分區化的組合，因此提供這兩種方法的優點。 在上述案例中，資料會在多個以區域分隔的 shardspaces 上分區化。 然後，資料會在 shardspace 中的多個分區上，以一致的雜湊進一步分割。 每個 shardspace 都包含多個 shardgroups。 每個 shardgroup 都有多個分區，而且是複寫的「單位」，在此案例中為。 每個 shardgroup 都包含 shardspace 中的所有資料。 Shardgroups A1 和 B1 是主要 Shardgroups，而 Shardgroups A2 和 B2 則是待命。 您可以選擇讓個別分區成為複寫的單位，而不是 shardgroup。

在上述架構中，會在每個可用性區域中部署 GSM/分區 director 以提供高可用性。 建議您為每個資料中心/區域部署至少一個 GSM/分區總監。 此外，應用程式伺服器的實例會部署在包含 shardgroup 的每個可用性區域中。 此設定可讓應用程式保持應用程式伺服器與資料庫/shardgroup 低之間的延遲。 如果資料庫失敗，則在資料庫角色轉換發生之後，與待命資料庫位於相同區域中的應用程式伺服器就可以處理要求。 Azure 應用程式閘道和分區 director 會追蹤要求和回應延遲，並據以路由要求。

從應用程式的觀點來看，用戶端系統會要求 Azure 應用程式閘道 (或) Azure 中的其他負載平衡技術，以將要求重新導向至最接近用戶端的區域。 Azure 應用程式閘道也支援「粘滯話」，因此來自相同用戶端的所有要求都會路由傳送至相同的應用程式伺服器。 應用程式伺服器會使用資料存取驅動程式中的連接共用。 這項功能適用于 JDBC、ODP.NET、OCI 等驅動程式。驅動程式可以辨識在要求中指定的分區化金鑰。 [Oracle 通用連接集區 (UCP) ](https://docs.oracle.com/en/database/oracle/oracle-database/12.2/jjucp/ucp-database-sharding-overview.html) for JDBC 用戶端可以啟用非 oracle 應用程式用戶端（例如 Apache TOMCAT 和 IIS）來使用 oracle 分區化。 

在初始要求期間，應用程式伺服器會連線到其區域中的分區 director，以取得需要路由傳送要求之分區的路由資訊。 根據傳遞的分區化金鑰，director 會將應用程式伺服器路由傳送至各自的分區。 應用程式伺服器會藉由建立對應來快取這項資訊，而針對後續的要求，則會略過分區 director，並將要求直接路由傳送至分區。

#### <a name="oracle-sharding-with-goldengate"></a>使用 GoldenGate 的 Oracle 分區化

下圖是 Oracle 分區化的參考架構，其中包含每個分區的區域內高可用性 Oracle GoldenGate。 相對於上述架構，此架構只會在單一 Azure 區域內會高可用性， (多個可用性區域) 。 您可以使用 Oracle GoldenGate，將多區域的高可用性分區化資料庫部署 (類似上述範例) 。

![使用可用性區域搭配 GoldenGate Oracle Database 分區化](./media/oracle-reference-architecture/oracledb_gg_sh_az.png)

上述參考架構會使用 _系統管理_ 的分區化方法來分區資料。 由於 Oracle GoldenGate 複寫是在區塊層級進行，因此複寫至一個分區的一半資料可以複寫到另一個分區。 另一半可以複寫至不同的分區。 

複寫資料的方式取決於複寫因素。 在複寫因數2中，您在 shardgroup 中的三個分區上會有兩個數據區塊的複本。 同樣地，在您的 shardgroup 中，複寫因數3和3分區，每個分區中的所有資料都會複寫到 shardgroup 中的每個其他分區。 Shardgroup 中的每個分區都有不同的複寫因素。 這項設定可協助您在 shardgroup 內和跨多個 shardgroups 有效率地定義高可用性和嚴重損壞修復設計。

在上述架構中，shardgroup A 和 shardgroup B 都包含相同的資料，但位於不同的可用性區域中。 如果 shardgroup A 和 shardgroup B 都有相同的複寫因數3，則會在兩個 shardgroups 中複寫分區化資料表的每個資料列/區塊6次。 如果 shardgroup A 的複寫因數為3，而 shardgroup B 的複寫因數為2，則會在兩個 shardgroups 之間複寫每個資料列/區塊5次。

如果發生實例層級或可用性區域層級失敗，此設定可防止資料遺失。 應用層可以讀取和寫入每個分區。 為了將衝突降至最低，Oracle 分區化會為每個雜湊值範圍指定「主要區塊」。 這項功能可確保將特定區塊的寫入要求導向至對應的區塊。 此外，Oracle GoldenGate 會提供自動衝突偵測和解決方式，以處理可能發生的任何衝突。 如需使用 Oracle 分區化來執行 GoldenGate 的詳細資訊和限制，請參閱 Oracle 有關搭配 [分區化資料庫使用 Oracle GoldenGate](https://docs.oracle.com/en/database/oracle/oracle-database/19/shard/sharding-high-availability.html#GUID-4FC0AC46-0B8B-4670-BBE4-052228492C72)的檔。

在上述架構中，會在每個可用性區域中部署 GSM/分區 director 以提供高可用性。 建議您為每個資料中心或區域部署至少一個 GSM/分區總監。 此外，應用程式伺服器的實例會部署在包含 shardgroup 的每個可用性區域中。 此設定可讓應用程式保持應用程式伺服器與資料庫/shardgroup 低之間的延遲。 如果資料庫失敗，在資料庫角色轉換之後，與待命資料庫位於相同區域中的應用程式伺服器就可以處理要求。 Azure 應用程式閘道和分區 director 會追蹤要求和回應延遲，並據以路由要求。

從應用程式的觀點來看，用戶端系統會要求 Azure 應用程式閘道 (或) Azure 中的其他負載平衡技術，以將要求重新導向至最接近用戶端的區域。 Azure 應用程式閘道也支援「粘滯話」，因此來自相同用戶端的所有要求都會路由傳送至相同的應用程式伺服器。 應用程式伺服器會使用資料存取驅動程式中的連接共用。 這項功能適用于 JDBC、ODP.NET、OCI 等驅動程式。驅動程式可以辨識在要求中指定的分區化金鑰。 [Oracle 通用連接集區 (UCP) ](https://docs.oracle.com/en/database/oracle/oracle-database/12.2/jjucp/ucp-database-sharding-overview.html) for JDBC 用戶端可以啟用非 oracle 應用程式用戶端（例如 Apache TOMCAT 和 IIS）來使用 oracle 分區化。 

在初始要求期間，應用程式伺服器會連線到其區域中的分區 director，以取得需要路由傳送要求之分區的路由資訊。 根據傳遞的分區化金鑰，director 會將應用程式伺服器路由傳送至各自的分區。 應用程式伺服器會藉由建立對應來快取這項資訊，而針對後續的要求，則會略過分區 director，並將要求直接路由傳送至分區。

## <a name="patching-and-maintenance"></a>修補和維護

將您的 Oracle 工作負載部署至 Azure 時，Microsoft 會處理所有主機作業系統層級的修補。 任何規劃的作業系統層級維護都會事先向客戶傳達，以允許客戶進行這項預定的維護。 兩部不同可用性區域的伺服器永遠不會同時修補。 如需 VM 維護和修補的詳細資訊，請參閱 [管理虛擬機器的可用性](../../manage-availability.md) 。 

您可以使用 [Azure 自動化更新管理](../../../automation/update-management/update-mgmt-overview.md)，將虛擬機器作業系統的修補作業自動化。 您可以使用 [Azure Pipelines](/azure/devops/pipelines/get-started/what-is-azure-pipelines?view=azure-devops) 或 [Azure 自動化更新管理](../../../automation/update-management/update-mgmt-overview.md) 來自動修補和維護 Oracle 資料庫，以將停機時間降至最低。 請參閱 [持續傳遞和藍色/綠色部署](/azure/devops/learn/what-is-continuous-delivery) ，以瞭解如何在您的 Oracle 資料庫內容中使用它。

## <a name="architecture-and-design-considerations"></a>架構和設計考慮

- 請考慮針對 Oracle Database VM 使用具有[受限核心個 vcpu](../../../virtual-machines/constrained-vcpu.md)的超執行緒[記憶體優化虛擬機器](../../sizes-memory.md)，以節省授權成本並將效能最大化。 針對效能和可用性，使用多個 premium 或 ultra 磁片 (受控磁片) 。
- 使用受控磁片時，磁片/裝置名稱可能會在重新開機時變更。 建議您使用裝置 UUID 而不是名稱，以確保您的掛接在重新開機時保持不變。 您可以在[這裡](../../../virtual-machines/linux/configure-raid.md#add-the-new-file-system-to-etcfstab)找到詳細資訊。
- 使用可用性區域以達到區域內的高可用性。
- 如果您的 Oracle 資料庫可用) 或 premium 磁片，請考慮使用 ultra 磁片 (。
- 請考慮在另一個 Azure 區域中使用 Oracle Data Guard 設定待命 Oracle 資料庫。
- 請考慮使用 [鄰近放置群組](../../../virtual-machines/linux/co-location.md#proximity-placement-groups) ，以減少應用程式與資料庫層之間的延遲。
- 設定 [Oracle Enterprise Manager](https://docs.oracle.com/en/enterprise-manager/) 以進行管理、監視和記錄。
- 請考慮使用 Oracle 自動儲存體管理 (ASM) ，以簡化資料庫的儲存管理。
- 使用 [Azure Pipelines](/azure/devops/pipelines/get-started/what-is-azure-pipelines) 來管理資料庫的修補和更新，而不需要停機。
- 調整您的應用程式程式碼，以新增雲端原生模式，例如 [重試模式](/azure/architecture/patterns/retry)、斷路器 [模式](/azure/architecture/patterns/circuit-breaker)，以及 [雲端設計模式指南](/azure/architecture/patterns/) 中所定義的其他模式，可能有助於您的應用程式更具復原性。

## <a name="next-steps"></a>後續步驟

請參閱下列適用于您案例的 Oracle 參考文章。

- [Oracle Data Guard 簡介](https://docs.oracle.com/en/database/oracle/oracle-database/18/sbydb/introduction-to-oracle-data-guard-concepts.html#GUID-5E73667D-4A56-445E-911F-1E99092DD8D7)
- [Oracle Data Guard Broker 概念](https://docs.oracle.com/en/database/oracle/oracle-database/12.2/dgbkr/oracle-data-guard-broker-concepts.html)
- [針對 Active-Active 高可用性設定 Oracle GoldenGate](https://docs.oracle.com/goldengate/1212/gg-winux/GWUAD/wu_bidirectional.htm#GWUAD282)
- [Oracle 分區化總覽](https://docs.oracle.com/en/database/oracle/oracle-database/19/shard/sharding-overview.html)
- [Oracle Active Data Guard 遠距任意距離的零資料遺失](https://www.oracle.com/technetwork/database/availability/farsync-2267608.pdf)