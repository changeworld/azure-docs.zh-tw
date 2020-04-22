---
title: Azure 上的 Oracle 資料庫的參考體系結構 |微軟文件
description: 引用用於在 Microsoft Azure 虛擬機器上運行 Oracle 資料庫企業版資料庫的體系結構。
services: virtual-machines-linux
author: BorisB2015
manager: gwallace
tags: ''
ms.service: virtual-machines
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 12/13/2019
ms.author: borisb
ms.custom: ''
ms.openlocfilehash: bbb6665299ce9b6521eeb8801d8621dfbdc17f4a
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/21/2020
ms.locfileid: "81683495"
---
# <a name="reference-architectures-for-oracle-database-enterprise-edition-on-azure"></a>Azure 上的 Oracle 資料庫企業版的參考結構

本指南詳細介紹了有關在 Azure 上部署高可用性 Oracle 資料庫的資訊。 此外,本指南還深入探討了災難恢復注意事項。 這些體系結構是根據客戶部署創建的。 本指南僅適用於 Oracle 資料庫企業版。

如果您有興趣瞭解有關最大化 Oracle 資料庫效能的詳細資訊,請參閱[架構師 Oracle 資料庫](oracle-design.md)。

## <a name="assumptions"></a>假設

- 您瞭解 Azure 的不同概念,如[可用性區域](../../../availability-zones/az-overview.md)
- 您正在執行 Oracle 資料庫企業版 12c 或更高版本
- 在使用本文中的解決方案時,您瞭解並承認了許可的含義

## <a name="high-availability-for-oracle-databases"></a>Oracle 資料庫的建立性

實現雲的高可用性是每個組織規劃和設計的重要組成部分。 Microsoft Azure 提供[可用性區域](../../../availability-zones/az-overview.md)和可用性集(用於不可用的區域)。 詳細瞭解[如何管理虛擬機的可用性](../../../virtual-machines/linux/manage-availability.md),以便為雲端進行設計。

除了雲端原生工具和產品外,Oracle 還提供可在 Azure 上設定的高可用性解決方案,如[Oracle 資料防護](https://docs.oracle.com/en/database/oracle/oracle-database/18/sbydb/introduction-to-oracle-data-guard-concepts.html#GUID-5E73667D-4A56-445E-911F-1E99092DD8D7),[帶 FSFO 的資料防護](https://docs.oracle.com/en/database/oracle/oracle-database/12.2/dgbkr/index.html),[分片](https://docs.oracle.com/en/database/oracle/oracle-database/12.2/admin/sharding-overview.html)和[金門](https://www.oracle.com/middleware/technologies/goldengate.html)。 本指南介紹每個解決方案的參考體系結構。

最後,在為雲遷移或創建應用程式時,調整應用程式代碼以添加雲原生模式(如[重試模式](https://docs.microsoft.com/azure/architecture/patterns/retry)和[斷路器模式](https://docs.microsoft.com/azure/architecture/patterns/circuit-breaker))非常重要。 [雲設計模式指南](https://docs.microsoft.com/azure/architecture/patterns/)中定義的其他模式可以説明您的應用程式更具彈性。

### <a name="oracle-rac-in-the-cloud"></a>雲中的 Oracle RAC

Oracle 實際應用程式群集 (RAC) 是 Oracle 的解決方案,透過讓多個實例存取一個資料庫儲存(共用所有體系結構模式)來協助客戶實現高輸送量。 雖然 Oracle RAC 還可用於本地的高可用性,但僅 Oracle RAC 不能單獨用於雲端中的高可用性,因為它僅可防止實例級故障,而不是機架級或資料中心級故障。 因此,Oracle 建議將 Oracle 數據防護與資料庫(無論是單個實例還是 RAC)一起使用,以獲得高可用性。 客戶通常需要高 SLA 來運行其關鍵任務應用程式。 Oracle RAC 目前未在 Azure 上獲得 Oracle 認證或支援。 但是,Azure 提供可用性區域和計劃維護視窗等功能,以説明防止實例級故障。 除此之外,客戶還可以使用 Oracle 數據保護、Oracle 金門和 Oracle 分片等技術,通過保護其資料庫免受機架級以及數據中心級和地緣政治故障的影響,從而獲得高性能和彈性。

當與 Oracle 資料保護或金門一起跨多個[可用性區域](https://docs.microsoft.com/azure/availability-zones/az-overview)運行 Oracle 資料庫時,客戶可獲得 99.99% 的正常執行時間 SLA。 在尚未存在可用性區域的 Azure 區域中,客戶可以使用[可用性集](https://docs.microsoft.com/azure/virtual-machines/linux/manage-availability#configure-multiple-virtual-machines-in-an-availability-set-for-redundancy),實現 99.95% 的停機時間 SLA。

>注:您可以有一個比 Microsoft 提供的停機時間 SLA 高得多的停機時間目標。

## <a name="disaster-recovery-for-oracle-databases"></a>Oracle 資料庫的災難復原

在雲中託管任務關鍵型應用程式時,設計高可用性和災難恢復非常重要。

對於 Oracle 資料庫企業版,Oracle 數據防護是災難恢復的有用功能。 您可以在配對的[Azure 區域](/azure/best-practices-availability-paired-regions)中設置備用資料庫實例,並為災難恢復設置數據防護故障轉移。 對於零數據丟失,建議您除了使用活動數據防護之外,還部署 Oracle 數據防護遠同步實例。 

如果應用程式允許延遲(需要徹底測試),請考慮在不同於 Oracle 資料庫的可用性區域中設置 Data Guard 遠同步實例。 使用**最大可用性**模式設置重做檔的同步傳輸到遠同步實例。 然後,這些檔將異步傳輸到備用資料庫。 

如果應用程式在**最大可用性**模式(同步)中在另一個可用性區域中設置 Far Sync 實例時不允許性能損失,則可以在與主資料庫相同的可用性區域中設置 Far Sync 實例。 為了增加可用性,請考慮設置靠近主資料庫的多個 Far Sync 實例,以及至少一個靠近備用資料庫的實例(如果角色轉換)。 在此[Oracle 主動數據衛士遠同步白皮書](https://www.oracle.com/technetwork/database/availability/farsync-2267608.pdf)中閱讀更多有關 Oracle 數據保護遠同步的白皮書。

使用 Oracle 標準版資料庫時,有 ISV 解決方案,如 DBVisit 待機,允許您設置高可用性和災難恢復。

## <a name="reference-architectures"></a>參考架構

### <a name="oracle-data-guard"></a>Oracle 資料保護

Oracle 數據防護可確保企業數據的高可用性、數據保護和災難恢復。 Data Guard 將備用資料庫維護為主資料庫的事務一致性副本。 根據主資料庫和輔助資料庫之間的距離以及應用程式對延遲的容差,可以設置同步或異步複製。 然後,如果主資料庫由於計劃內或計劃外停機而不可用,Data Guard 可以將任何備用資料庫切換到主角色,從而最大限度地減少停機時間。 

使用 Oracle 數據防護時,您還可以出於唯讀目的打開輔助資料庫。 此配置稱為活動數據防護。 Oracle 資料庫 12c 引入了一個稱為數據衛士遠同步實例的功能。 此實例允許您設置 Oracle 資料庫的零資料丟失配置,而無需在性能上妥協。

> [!NOTE]
> 活動數據防護需要額外的許可。 使用此許可證也需要使用遠同步功能。 請與您的 Oracle 代表聯絡,討論許可問題。

#### <a name="oracle-data-guard-with-fsfo"></a>使用 FSFO 的 Oracle 資料防護
具有快速啟動故障轉移 (FSFO) 的 Oracle 數據防護可以通過在單獨的電腦上設置代理來提供額外的恢復能力。 數據防護代理和輔助資料庫都運行觀察者並觀察主資料庫的停機時間。 這還允許在數據防護觀察者設置中冗餘。 

使用 Oracle 資料庫版本 12.2 及以上,還可以使用單個 Oracle 數據保護代理配置配置多個觀察者。 此設置提供其他可用性,以防一個觀察者和輔助資料庫遇到停機。 數據防護代理是輕量級的,可以託管在相對較小的虛擬機上。 要瞭解有關資料衛士代理及其優勢的更多資訊,請造訪有關本主題的[Oracle 文件](https://docs.oracle.com/en/database/oracle/oracle-database/12.2/dgbkr/oracle-data-guard-broker-concepts.html)。

下圖是一個建議體系結構,用於在 Azure 上使用具有可用性區域的 Oracle 數據防護。 此體系結構允許您獲得 99.99% 的 VM 超時 SLA。

![使用可用性區域與資料保護代理 - FSFO 的 Oracle 資料庫](./media/oracle-reference-architecture/oracledb_dg_fsfo_az.png)

在上圖中,用戶端系統通過 Web 使用 Oracle 後端訪問自訂應用程式。 Web 前端配置在負載均衡器中。 Web 前端調用相應的應用程式伺服器來處理該工作。 應用程式伺服器查詢主 Oracle 資料庫。 Oracle 資料庫已使用超線程[記憶體最佳化虛擬機器](../../../virtual-machines/windows/sizes-memory.md)配置,該虛擬機[具有受限的核心 vCPU,](../../../virtual-machines/windows/constrained-vcpu.md)以節省許可成本並最大限度地提高性能。 多個先進磁碟或超級磁碟(託管磁碟)用於性能和高可用性。

Oracle 資料庫被放置在多個可用性區域中,以便獲得高可用性。 每個區域由一個或多個數據中心組成,這些資料中心配備獨立的電源、冷卻和網路。 為確保恢復能力,在所有啟用的區域中至少設置了三個單獨的區域。 區域內可用性區域的物理分離可保護數據免受資料中心故障的影響。 此外,在兩個可用性區域中設置兩個 FSFO 觀察者,以便在發生中斷時啟動資料庫並將其故障轉移到輔助區域。 

您可以在與前一體系結構中顯示的區域不同的可用性區域(本例中為 AZ 1)中設置其他觀察點和/或備用資料庫。 最後,Oracle 企業管理員 (OEM) 監視 Oracle 資料庫的停機時間和性能。 OEM 還允許您生成各種性能和使用方式報告。

在不支援可用性區域的區域,您可以使用可用性集以高可用性的方式部署 Oracle 資料庫。 可用性集允許您實現 99.95% 的 VM 停機時間。 下圖是此用途的參考體系結構:

![使用可用性集與資料保護代理 - FSFO 的 Oracle 資料庫](./media/oracle-reference-architecture/oracledb_dg_fsfo_as.png)

> [!NOTE]
> * Oracle 企業管理器 VM 無需放置在可用性集中,因為只有一個正在部署 OEM 的實例。
> * 可用性集配置當前不支援超磁碟。

#### <a name="oracle-data-guard-far-sync"></a>Oracle 資料防護遠同步

Oracle 資料防護遠同步為 Oracle 資料庫提供零資料丟失保護功能。 此功能允許您在資料庫電腦發生故障時防止數據丟失。 Oracle 數據防護遠同步需要安裝在單獨的 VM 上。 Far Sync 是一個輕量級的 Oracle 實例,它只有控制檔、密碼檔、spfile 和備用日誌。 沒有資料檔或重新進入日誌檔。 

要實現零資料丟失保護,主資料庫和遠同步實例之間必須同步通信。 Far Sync 實例以同步方式從主資料庫接收重做,並立即以非同步方式轉發到所有備用資料庫。 此設置還減少了主資料庫的開銷,因為它只需將重做發送到遠同步實例,而不是所有備用資料庫。 如果 Far Sync 實例失敗,Data Guard 會自動使用從主資料庫到輔助資料庫的異步傳輸來維護接近零的數據丟失保護。 為了增加彈性,客戶可以為每個資料庫實例(主實例和輔助資料庫)部署多個 Far Sync 實例。

下圖是使用 Oracle 資料防護遠同步的高可用性體系結構:

![使用可用性區域與資料保護遠同步&代理 - FSFO 的 Oracle 資料庫](./media/oracle-reference-architecture/oracledb_dg_fs_az.png)

在前面的體系結構中,有一個 Far Sync 實例部署在同一可用性區域,以減少兩者之間的延遲。 如果應用程式對延遲敏感,請考慮在[接近放置組中](../../../virtual-machines/linux/proximity-placement-groups.md)部署資料庫和遠同步實例或實例。

下圖是利用 Oracle 資料保護 FSFO 和遠同步實現高可用性和災難恢復的體系結構:

![Oracle 資料庫使用可用性區域進行災難復原,資料衛士遠同步&代理 - FSFO](./media/oracle-reference-architecture/oracledb_dg_fs_az_dr.png)

### <a name="oracle-goldengate"></a>Oracle GoldenGate

金門允許在整個企業中多個異構平台之間在交易級別交換和操作數據。 它以事務完整性和現有基礎架構上最少的開銷移動已提交的事務。 其模組化架構使您能夠靈活地跨各種拓撲提取和複製選定的數據記錄、事務性更改和對 DDL(數據定義語言)的更改。

Oracle 金門允許您通過提供雙向複製來配置資料庫,使其具有高可用性。 這允許您設定**多主機**或**主動-主動設定**。 下圖是 Azure 上的 Oracle 金門主動設置的建議體系結構。 在以下體系結構中,Oracle 資料庫已使用具有[受限核心vCPU](../../../virtual-machines/windows/constrained-vcpu.md)的超線程[記憶體最佳化虛擬機器](../../../virtual-machines/windows/sizes-memory.md)進行配置,以節省許可成本並最大限度地提高性能。 多個先進磁碟或超級磁碟(託管磁碟)用於性能和可用性。

![使用可用性區域與資料保護代理 - FSFO 的 Oracle 資料庫](./media/oracle-reference-architecture/oracledb_gg_az.png)

> [!NOTE]
> 在可用性區域當前不可用的區域中,可以使用可用性集設置類似的體系結構。

Oracle GoldenGate 具有提取、泵和複製器等進程,可説明您將數據從一個 Oracle 資料庫伺服器異步複製到另一台。 這些程序允許您設置雙向複製,以確保如果存在可用性區域級停機時間,資料庫的高可用性。 在上圖中,提取過程與 Oracle 資料庫在同一伺服器上運行,而數據泵和複製副本進程在同一可用性區域中的單獨伺服器上運行。 複製進程用於從其他可用性區域中的資料庫接收數據,並將數據提交到其可用性區域中的 Oracle 資料庫。 同樣,數據泵進程將提取過程提取的數據發送到其他可用性區域中的複製過程。 

雖然前面的體系結構圖顯示了在單獨的伺服器上配置的數據泵和複製程式進程,但您可以根據伺服器的容量和使用方式在同一伺服器上設置所有 Oracle GoldenGate 進程。 始終查閱 AWR 報表和 Azure 中的指標,以瞭解伺服器的使用模式。

在不同可用性區域或不同區域中設置 Oracle GoldenGate 雙向複製時,請務必確保應用程式可以接受不同元件之間的延遲。 可用性區域和區域之間的延遲可能會有所不同,並取決於多種因素。 建議您在不同可用性區域和/或區域的應用程式層和資料庫層之間設置效能測試,以確認它滿足應用程式性能要求。

應用程式層可以在自己的子網中設置,資料庫層可以分隔成自己的子網。 如果可能,請考慮使用[Azure 應用程式閘道](../../../application-gateway/overview.md)來負載應用程式伺服器之間的流量。 Azure 應用程式閘道是一個健壯的 Web 流量負載均衡器。 它提供基於 Cookie 的作業階段相關性,使用戶會話保留在同一伺服器上,從而最大限度地減少資料庫上的衝突。 應用程式閘道的替代機制是[Azure 負載均衡器](../../../load-balancer/load-balancer-overview.md)和[Azure 流量管理員](../../../traffic-manager/traffic-manager-overview.md)。

### <a name="oracle-sharding"></a>甲骨文分片

分片是 Oracle 12.2 中引入的數據層模式。 它允許您跨獨立資料庫對數據進行水準分區和縮放。 它是一種無共用的體系結構,每個資料庫都託管在專用虛擬機上,除了恢復能力和可用性之外,還可實現高讀取和寫入輸送量。 此模式消除了單點故障,提供故障隔離,並支援滾動升級,而不會停機。 一個分片的停機時間或數據中心級故障不會影響其他資料中心中其他分片的性能或可用性。 

分片適用於無法承受任何停機時間的高輸送量 OLTP 應用程式。 始終保證具有相同分片鍵的所有行都位於同一分片上,從而提高了提供高一致性的性能。 使用分片的應用程式必須具有定義良好的資料模型和數據分發策略(一致的哈希、範圍、清單或複合),這些策略主要使用分片鍵(例如 *,customerId*或*accountnum)* 訪問數據。 分片還允許您將特定數據集存儲在更靠近最終客戶的位置,從而説明您滿足性能和合規性要求。

建議您複製分片以實現高可用性和災難恢復。 此設定可以使用 Oracle 資料衛士或 Oracle 金門等 Oracle 技術完成。 複製單元可以是分片、分片的一部分或一組分片。 分片資料庫的可用性不受一個或多個分片中斷或減速的影響。 為獲得高可用性,備用分片可以放置在放置主分片的同一可用性區域中。 對於災難恢復,備用分片可以位於另一個區域。 您還可以在多個區域部署分片,以為這些區域的流量提供服務。 閱讀有關在[Oracle 分片文件中](https://docs.oracle.com/en/database/oracle/oracle-database/19/shard/sharding-high-availability.html)設定高可用性和複製分片資料庫的詳細資訊。

Oracle 分片主要由以下元件組成。 有關這些元件的詳細資訊,請參考 Oracle[分片文件](https://docs.oracle.com/en/database/oracle/oracle-database/19/shard/sharding-overview.html):

- **分片目錄**- 特殊用途的 Oracle 資料庫,是所有分片資料庫配置數據的持久儲存。 所有配置更改(如添加或刪除分片、映射數據)和分片資料庫中的 DSL 都在分片目錄中啟動。 分片目錄還包含 SDB 中所有重複表的主副本。 

  分片目錄使用具體化檢視自動將更改複製到所有分片中的重複表。 分片目錄資料庫還充當查詢協調器,用於處理不指定分片鍵的多分片查詢和查詢。 
  
  建議將 Oracle 資料防護與可用性區域或可用性集結合使用,以便分片目錄高可用性,這是推薦的最佳做法。 分片目錄的可用性不會影響分片資料庫的可用性。 分片目錄中的停機時間僅影響數據防護故障轉移完成的短暫時間段內的維護操作和多分片查詢。 線上事務繼續由 SDB 路由和執行,並且不受目錄中斷的影響。

- **分片控制器**-需要部署在分片駐留在的每個區域/可用性區域的輕量級服務。 分片控制器是在 Oracle 分片上下文中部署的全球服務管理器。 為獲得高可用性,建議您在分片存在的每個可用性區域中部署至少一個分片控制器。 

  最初連接到資料庫時,路由資訊由分片控制器設置,並緩存用於後續請求,繞過分片控制器。 使用分片建立工作階段後,所有 SQL 查詢和 DLL 將支援並在給定分片的範圍內執行。 此路由速度很快,用於執行分片內事務的所有 OLTP 工作負載。 建議對所有需要最高性能和可用性的 OLTP 工作負載使用直接路由。 當分片不可用或分片拓撲發生更改時,路由緩存會自動刷新。 
  
  對於高性能、與數據相關的路由,Oracle 建議在訪問分片資料庫中的數據時使用連接池。 Oracle 連接池、特定於語言的庫和驅動程式支援 Oracle 分片。 有關詳細資訊,請參閱[Oracle 分片文件](https://docs.oracle.com/en/database/oracle/oracle-database/19/shard/sharding-overview.html#GUID-3D41F762-BE04-486D-8018-C7A210D809F9)。

- **全域服務**- 全域服務類似於常規資料庫服務。 除了資料庫服務的所有屬性外,全域服務還具有分片資料庫的屬性,如用戶端和分片之間的區域相關性以及複製延遲容差。 只需創建一個全域服務,才能將數據從分片資料庫讀取/寫入。 使用活動數據防護並設置分片的唯讀副本時,可以為唯讀工作負載創建另一個 gGobal 服務。 用戶端可以使用這些全域服務連接到資料庫。

- **分片資料庫**-分片資料庫是您的 Oracle 資料庫。 每個資料庫都使用 Oracle 資料保護在啟用快速啟動故障轉移 (FSFO) 的 Broker 設定中進行複製。 您無需在每個分片上設置數據防護故障轉移和複製。 創建共享資料庫時,將自動配置和部署此資料庫。 如果特定分片失敗,Oracle 共用會自動通過從主資料庫到備用資料庫的連接失敗。

您可以使用兩個介面部署和管理 Oracle 分片資料庫:Oracle 企業管理員雲端控制`GDSCTL`GUI 和/ 或命令列實用程式。 您甚至可以使用雲端監視不同分片的可用性和性能。 該`GDSCTL DEPLOY`命令會自動創建分片及其各自的偵聽器。 此外,此命令會自動部署用於管理員指定的分片級高可用性的複製配置。

分片資料庫的方法不同:

* 系統管理的分片 ─ 使用分割區自動分配在分片之間
* 使用者定義的分片 ─ 允許您指定資料映射到分片,當有法規或資料本地化要求時,這很有效)
* 複合分片 ─不同_分片空間_的系統管理與使用者定義的分片的組合
* 表子分割區 ─ 類似於常規分區表。

閱讀有關 Oracle 文件中不同[分片方法](https://docs.oracle.com/en/database/oracle/oracle-database/19/shard/sharding-methods.html)的更多內容。

雖然分片資料庫可能看起來像一個資料庫的應用程式和開發人員,當從非分片資料庫遷移到分片資料庫,需要仔細規劃,以確定哪些表將被複製或分片。 

重複的表存儲在所有分片上,而分片表分佈在不同的分片上。 建議複製小表和維度表,並分發/分片實數表。 數據可以使用分片目錄作為中央協調器載入到分片資料庫中,也可以在每個分片上運行數據泵。 閱讀有關將資料遷移到 Oracle 文件中[分片資料庫](https://docs.oracle.com/en/database/oracle/oracle-database/19/shard/sharding-loading-data.html)的詳細資訊。

#### <a name="oracle-sharding-with-data-guard"></a>使用資料防護的 Oracle 分片

Oracle 數據防護可用於使用系統管理、使用者定義和複合分片方法分片。

下圖是 Oracle 分片的參考體系結構,用於 Oracle 數據防護,用於每個分片的高可用性。 系統結構圖顯示了一種_複合分片方法_。 對於對數據局部性、負載平衡、高可用性、災難恢復等有不同要求的應用程序,體系結構圖可能會有所不同,並且可能使用不同的分片方法。 Oracle 分片允許您滿足這些要求,並通過提供這些選項水準高效地擴展。 使用 Oracle 金門甚至可以部署類似的體系結構。

![使用可用性區域與資料保護代理 - FSFO 進行 Oracle 資料庫分片處理](./media/oracle-reference-architecture/oracledb_dg_sh_az.png)

雖然系統管理的分片是最容易配置和管理的,但使用者定義的分片或複合分片非常適合數據和應用程式是地理分佈的情況,或者需要控制每個分片的複製的情況。 

在前面的體系結構中,複合分片用於地理分佈數據並水平橫向橫向橫向擴展應用程式層。 複合分片是系統管理和使用者定義的分片的組合,因此提供了這兩種方法的好處。 在前面的方案中,數據首先分片跨多個分片空間分隔的區域。 然後,數據通過在分片空間中的多個分片上的一致哈希進一步分區。 每個分片空間包含多個分片組。 每個分片組有多個分片,是複製的"單元",在這種情況下。 每個分片組都包含分片空間中的所有數據。 分片組 A1 和 B1 是主分片組,而分片組 A2 和 B2 是備用。 您可以選擇將單個分片作為複製單元,而不是分片組。

在前面的體系結構中,GSM/分片控制器部署在每個可用性區域,以獲得高可用性。 建議每個資料中心/區域至少部署一個 GSM/分片控制器。 此外,應用程式伺服器的實例部署在包含分片組的每個可用性區域中。 此設定允許應用程式將應用程式伺服器和資料庫/分片組之間的延遲保持在較低水準。 如果資料庫發生故障,與備用資料庫位於同一區域的應用程式伺服器可以在資料庫角色轉換發生后處理請求。 Azure 應用程式閘道和分片控制器追蹤請求和回應延遲,並相應地路由請求。

從應用程式的角度來看,用戶端系統向 Azure 應用程式閘道(或 Azure 中的其他負載平衡技術)發出請求,後者將請求重定向到最接近用戶端的區域。 Azure 應用程式閘道還支援黏滯會話,因此來自同一用戶端的任何請求都路由到同一應用程式伺服器。 應用程式伺服器在數據存取驅動程式中使用連接池。 此功能在 JDBC、ODP.NET、OCI 等驅動程式中可用。驅動程式可以識別作為請求的一部分指定的分片鍵。 適用於 JDBC 用戶端的[Oracle 通用連接池 (UCP)](https://docs.oracle.com/en/database/oracle/oracle-database/12.2/jjucp/ucp-database-sharding-overview.html)可以使非 Oracle 應用程式用戶端(如 Apache Tomcat 和 IIS)與 Oracle 分片配合使用。 

在初始請求期間,應用程式伺服器連接到其區域中的分片控制器,以獲取有關請求需要路由到的分片的路由資訊。 根據傳遞的分片鍵,控制器將應用程序伺服器路由到相應的分片。 應用程式伺服器通過構建地圖來緩存此資訊,對於後續請求,繞過分片控制器並將請求直接路由到分片。

#### <a name="oracle-sharding-with-goldengate"></a>甲骨文與金門分片

下圖是 Oracle 分片與 Oracle 金門的參考體系結構,用於區域內每個分片的高可用性。 與前面的體系結構相反,此體系結構僅描述單個 Azure 區域(多個可用性區域)中的高可用性。 可以使用 Oracle GoldenGate 部署多區域高可用性分片資料庫(類似於前面的範例)。

![使用可用性區域與金門的 Oracle 資料庫分片](./media/oracle-reference-architecture/oracledb_gg_sh_az.png)

前面的引用體系結構使用_系統管理的_分片方法對數據進行分片。 由於 Oracle GoldenGate 複製是在塊級別完成的,因此複製到一個分片的數據的一半可以複製到另一個分片。 另一半可以複製到不同的分片。 

數據複製的方式取決於複製因數。 複製因數為 2 時,將在分片組中的三個分片上具有每個數據塊的兩個副本。 同樣,在分片組中複製因數為 3 和 3 分片時,每個分片中的所有數據都將複製到分片組中的每個其他分片。 分片組中的每個分片可以具有不同的複製因數。 此設定可説明您在分片組內和跨多個分片組中高效地定義高可用性和災難恢復設計。

在前面的體系結構中,分片組 A 和分片組 B 都包含相同的數據,但駐留在不同的可用性區域。 如果分片組 A 和分片組 B 具有相同的複製因數 3,則分片表的每個行/塊將在兩個分片組中複製 6 次。 如果分片組 A 的複製係數為 3,而分片組 B 的複製係數為 2,則每行/塊將在兩個分片組中複製 5 次。

如果發生實例級或可用性區域級別故障,此設置可防止數據丟失。 應用程式層能夠讀取和寫入每個分片。 為了盡量減少衝突,Oracle 分片為每個哈希值範圍指定一個"主塊"。 此功能可確保特定塊的寫入請求定向到相應的塊。 此外,Oracle GoldenGate 還提供自動衝突檢測和解決,以處理可能出現的任何衝突。 有關使用 Oracle 分片實現金門的更多資訊和限制,請參閱 Oracle 有關使用 Oracle[金門與分片資料庫](https://docs.oracle.com/en/database/oracle/oracle-database/19/shard/sharding-high-availability.html#GUID-4FC0AC46-0B8B-4670-BBE4-052228492C72)的文檔。

在前面的體系結構中,GSM/分片控制器部署在每個可用性區域,以獲得高可用性。 建議每個資料中心或區域至少部署一個 GSM/分片控制器。 此外,應用程式伺服器的實例部署在包含分片組的每個可用性區域中。 此設定允許應用程式將應用程式伺服器和資料庫/分片組之間的延遲保持在較低水準。 如果資料庫發生故障,與備用資料庫位於同一區域的應用程式伺服器可以在資料庫角色轉換後處理請求。 Azure 應用程式閘道和分片控制器追蹤請求和回應延遲,並相應地路由請求。

從應用程式的角度來看,用戶端系統向 Azure 應用程式閘道(或 Azure 中的其他負載平衡技術)發出請求,後者將請求重定向到最接近用戶端的區域。 Azure 應用程式閘道還支援黏滯會話,因此來自同一用戶端的任何請求都路由到同一應用程式伺服器。 應用程式伺服器在數據存取驅動程式中使用連接池。 此功能在 JDBC、ODP.NET、OCI 等驅動程式中可用。驅動程式可以識別作為請求的一部分指定的分片鍵。 適用於 JDBC 用戶端的[Oracle 通用連接池 (UCP)](https://docs.oracle.com/en/database/oracle/oracle-database/12.2/jjucp/ucp-database-sharding-overview.html)可以使非 Oracle 應用程式用戶端(如 Apache Tomcat 和 IIS)與 Oracle 分片配合使用。 

在初始請求期間,應用程式伺服器連接到其區域中的分片控制器,以獲取有關請求需要路由到的分片的路由資訊。 根據傳遞的分片鍵,控制器將應用程序伺服器路由到相應的分片。 應用程式伺服器通過構建地圖來緩存此資訊,對於後續請求,繞過分片控制器並將請求直接路由到分片。

## <a name="patching-and-maintenance"></a>修補和維護

將 Oracle 工作負荷部署到 Azure 時,Microsoft 會負責所有主機作業系統級修補。 任何計劃的 OS 級維護都提前通知客戶,以便客戶進行此計劃維護。 兩個不同的可用區域的兩台伺服器永遠不會同時修補。 有關 VM 維護和修補的更多詳細資訊[,請參閱管理虛擬機器的可用性](../../../virtual-machines/linux/manage-availability.md)。 

修補虛擬機作業系統可以使用[Azure 自動化](../../../automation/automation-tutorial-update-management.md)自動進行。 可以使用[Azure 管道](/azure/devops/pipelines/get-started/what-is-azure-pipelines?view=azure-devops)或[Azure 自動化](../../../automation/automation-tutorial-update-management.md)自動修補和維護 Oracle 資料庫,以最大限度地減少停機時間。 請參閱[連續交付和藍色/綠色部署](/azure/devops/learn/what-is-continuous-delivery),瞭解如何在 Oracle 資料庫的上下文中使用它。

## <a name="architecture-and-design-considerations"></a>架構與設計注意事項

- 請考慮將超線程[記憶體優化虛擬機](../../../virtual-machines/windows/sizes-memory.md)與 Oracle 資料庫 VM[受限的核心 vCPU](../../../virtual-machines/windows/constrained-vcpu.md)一起使用,以節省許可成本並最大限度地提高性能。 使用多個先進磁碟或超級磁碟(託管磁碟)來提高性能和可用性。
- 使用託管磁碟時,磁碟/設備名稱可能會在重新啟動時更改。 建議您使用設備 UUID 而不是名稱,以確保安裝在重新啟動期間持續。 您可以在[這裡](../../../virtual-machines/linux/configure-raid.md#add-the-new-file-system-to-etcfstab)找到詳細資訊。
- 使用可用性區域實現區域內的高可用性。
- 請考慮為 Oracle 資料庫使用超磁碟(如果可用)或進階磁碟。
- 請考慮使用 Oracle 資料防護在另一個 Azure 區域中設置備用 Oracle 資料庫。
- 請考慮使用[鄰近放置組](../../../virtual-machines/linux/co-location.md#proximity-placement-groups)來減少應用程式和資料庫層之間的延遲。
- 設置[Oracle 企業管理員](https://docs.oracle.com/en/enterprise-manager/)以進行管理、監視和日誌記錄。
- 請考慮使用 Oracle 自動儲存管理 (ASM) 簡化資料庫的儲存管理。
- 使用[Azure 管道](/azure/devops/pipelines/get-started/what-is-azure-pipelines)管理資料庫的修補和更新,而不會有任何停機時間。
- 調整應用程式代碼以添加雲原生模式,如[重試模式](/azure/architecture/patterns/retry)、[斷路器模式](/azure/architecture/patterns/circuit-breaker),以及[雲端設計模式指南](/azure/architecture/patterns/)中定義的其他模式,這些模式可能有助於應用程式更具彈性。

## <a name="next-steps"></a>後續步驟

查看以下適用於您的方案的 Oracle 參考文章。

- [Oracle 資料防護簡介](https://docs.oracle.com/en/database/oracle/oracle-database/18/sbydb/introduction-to-oracle-data-guard-concepts.html#GUID-5E73667D-4A56-445E-911F-1E99092DD8D7)
- [Oracle 資料保護代理概念](https://docs.oracle.com/en/database/oracle/oracle-database/12.2/dgbkr/oracle-data-guard-broker-concepts.html)
- [配置 Oracle 金門,以獲得主動-主動高可用性](https://docs.oracle.com/goldengate/1212/gg-winux/GWUAD/wu_bidirectional.htm#GWUAD282)
- [甲骨文分片概述](https://docs.oracle.com/en/database/oracle/oracle-database/19/shard/sharding-overview.html)
- [Oracle 主動資料防護在任何距離上遠同步零資料丟失](https://www.oracle.com/technetwork/database/availability/farsync-2267608.pdf)
