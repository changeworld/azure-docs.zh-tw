---
title: 受控執行個體常見問題集
description: SQL Database 受控實例的常見問題（FAQ）
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: sstein, carlrab
ms.date: 03/17/2020
ms.openlocfilehash: 99fbda6f6d5e8fc88f9f4f34c6e194412a120057
ms.sourcegitcommit: acc558d79d665c8d6a5f9e1689211da623ded90a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/30/2020
ms.locfileid: "82598482"
---
# <a name="sql-database-managed-instance-frequently-asked-questions-faq"></a>SQL Database 受控實例的常見問題（FAQ）

本文包含許多[SQL Database 受控實例](sql-database-managed-instance.md)最常見的問題。

## <a name="supported-features"></a>支援的功能

**哪裡可以找到受控實例上支援的功能清單？**

如需受控實例中支援的功能清單，請參閱[Azure SQL Database 與 SQL Server](sql-database-features.md)。

如需 Azure SQL Database 受控實例與內部部署 SQL Server 之間的語法和行為差異，請參閱[SQL Server 的 t-sql 差異](sql-database-managed-instance-transact-sql-information.md)。


## <a name="tech-spec--resource-limits"></a>技術規格 & 資源限制
 
**哪裡可以找到受控實例的技術特性和資源限制？**

如需可用的硬體產生特性，請參閱[硬體世代中的技術差異](sql-database-managed-instance-resource-limits.md#hardware-generation-characteristics)。
如需可用的服務層級及其特性，請參閱[服務層級之間的技術差異](sql-database-managed-instance-resource-limits.md#service-tier-characteristics)。

## <a name="known-issues--bugs"></a>已知問題 & bug

**哪裡可以找到已知的問題和 bug？**

對於錯誤和已知問題，請參閱[已知問題](sql-database-release-notes.md#known-issues)。

## <a name="new-features"></a>新功能

**我可以在哪裡找到最新的功能和公開預覽中的功能？**

如需新的和預覽功能，請參閱[版本](sql-database-release-notes.md?tabs=managed-instance)資訊。

## <a name="deployment-times"></a>部署時間 

**建立或更新實例或還原資料庫需要多少時間？**

建立新的受控實例或變更服務層（虛擬核心、儲存體）的預期時間取決於數個因素。 請參閱[管理作業](/azure/sql-database/sql-database-managed-instance#managed-instance-management-operations) 

## <a name="naming-convention"></a>命名慣例

**受控實例是否可以與內部部署 SQL Server 具有相同的名稱？**

不支援變更受控實例名稱。

受控實例的預設 DNS 區域 *。 database.windows.net*可以變更。 

若要使用另一個 DNS 區域，而非預設值，例如 *. contoso.com*： 
- 使用 CliConfig 來定義別名。 此工具只是一個登錄設定包裝函式，因此也可以使用群組原則或腳本來完成。
- 搭配*TrustServerCertificate = true*選項使用*CNAME* 。

## <a name="move-db-from-mi"></a>從 MI 移動資料庫 

**如何將資料庫從受控實例移回 SQL Server 或 Azure SQL Database？**

您可以將[資料庫匯出至 bacpac](sql-database-export.md) ，然後匯[入 bacpac]( sql-database-import.md)檔案。 如果您的資料庫小於 100 GB，這是建議的方法。

如果資料庫中的所有資料表都有主鍵，則可以使用異動複寫。

從`COPY_ONLY`受控實例取得的原生備份無法還原至 SQL Server，因為相較于 SQL Server，受控實例具有更高的資料庫版本。

## <a name="migrate-instance-db"></a>遷移實例 DB

**如何將實例資料庫移轉至單一 Azure SQL Database？**

其中一個選項是將[資料庫匯出至 bacpac](sql-database-export.md) ，然後匯[入 bacpac](sql-database-import.md)檔案。 

如果您的資料庫小於 100 GB，則這是建議的方法。 如果資料庫中的所有資料表都有主鍵，則可以使用異動複寫。

## <a name="switch-hardware-generation"></a>交換器硬體世代 

**我可以在第4代和第5代連線之間切換受控實例的硬體世代嗎？**

如果您的受控實例布建所在的區域中有兩個硬體世代可供使用，則可以在硬體世代之間進行自動線上切換。 在此情況下，您可以查看[vCore 模型總覽頁面](sql-database-service-tiers-vcore.md)，說明如何在硬體世代之間切換。

這是長時間執行的作業，因為新的受控實例將會在背景中布建，且資料庫會在程式結束時，自動在舊實例和新實例之間傳輸，並進行快速容錯移轉。 

**如果同一個區域中不支援這兩個硬體層代，該怎麼辦？**

如果相同區域中不支援這兩種硬體層代，則可以變更硬體世代，但必須手動完成。 這會要求您在需要產生硬體的區域中布建新的實例，並在舊的和新的實例之間手動備份和還原資料。

**如果沒有足夠的 IP 位址可執行更新作業，該怎麼辦？**

如果您的受控實例布建所在的子網中沒有足夠的 IP 位址，您就必須在其中建立新的子網和新的受控實例。 我們也建議您以更多 IP 位址建立新的子網 alocated，讓未來的更新作業可以避免類似的情況（針對 propper 子網大小，檢查[如何判斷 vnet 子網的大小](sql-database-managed-instance-determine-size-vnet-subnet.md)。 布建新的實例之後，您可以在舊的和新的實例之間手動備份和還原資料，或執行跨實例的[時間點還原](sql-database-managed-instance-point-in-time-restore.md?tabs=azure-powershell)。 


## <a name="tune-performance"></a>微調效能

**如何? 調整受控實例的效能？**

一般用途受控實例會使用遠端存放，因為資料和記錄檔的大小對效能很重要。 如需詳細資訊，請參閱[一般用途的記錄檔大小影響受控執行個體效能](https://medium.com/azure-sqldb-managed-instance/impact-of-log-file-size-on-general-purpose-managed-instance-performance-21ad170c823e)。

如果您的工作負載包含許多小型交易，請考慮將連線類型從 proxy 切換到重新導向模式。

## <a name="maximum-storage-size"></a>儲存體大小上限

**受控實例的儲存體大小上限為何？**

受控實例的儲存體大小取決於選取的服務層級（一般用途或業務關鍵）。 如需這些服務層級的儲存體限制，請參閱[服務層特性](sql-database-service-tiers-general-purpose-business-critical.md)。

## <a name="back-up-storage-cost"></a>備份儲存體成本 

**備份儲存體是否從我的受控實例儲存體中扣除？**

不會，備份儲存體不會從您的受控實例儲存空間中扣除。 備份儲存體與實例儲存空間無關，而且大小不受限制。 備份儲存體受限於保留實例資料庫備份的時間週期，可設定為7到35天。 如需詳細資訊，請參閱[自動備份](https://docs.microsoft.com/azure/sql-database/sql-database-automated-backups)。

## <a name="track-billing"></a>追蹤帳單

**是否有方法可以追蹤我的受控實例的帳單費用？**

您可以使用[Azure 成本管理方案](/azure/cost-management/)來執行此動作。 流覽至[Azure 入口網站](https://portal.azure.com)**中的 [** 訂用帳戶]，然後選取 [**成本分析**]。 

使用 [**累積的成本**] 選項，然後依**資源類型**篩選`microsoft.sql/managedinstances`為。 
  
## <a name="inbound-nsg-rules"></a>輸入 NSG 規則

**如何設定管理埠上的輸入 NSG 規則？**

受控實例控制平面會維護可保護管理埠的 NSG 規則。

以下是所使用的管理埠：

Service Fabric 基礎結構會使用埠9000和9003。 Service Fabric 主要角色是讓虛擬叢集保持狀況良好，並根據元件複本的數目來保留目標狀態。

節點代理程式會使用埠1438、1440和1452。 Node agent 是在叢集內執行的應用程式，由控制平面用來執行管理命令。

除了 NSG 規則以外，內建防火牆也會保護網路層上的實例。 在應用層通訊上，會使用憑證來保護。
  
如需詳細資訊以及如何驗證內建防火牆，請參閱[Azure SQL Database 受控實例內建防火牆](sql-database-managed-instance-management-endpoint-verify-built-in-firewall.md)。


## <a name="mitigate-data-exfiltration-risks"></a>降低資料外泄風險  

**如何減少資料外泄風險？**

若要降低任何資料外泄風險，建議客戶套用一組安全性設定和控制項：

- 在所有資料庫上開啟[透明資料加密（TDE）](https://docs.microsoft.com/azure/sql-database/transparent-data-encryption-azure-sql) 。
- 關閉 Common Language Runtime （CLR）。 這在內部部署中也是建議的做法。
- 僅使用 Azure Active Directory （AAD）驗證。
- 具有低許可權 DBA 帳戶的存取實例。
- 設定系統管理員（sysadmin）帳戶的 JiT jumpbox 存取。
- 開啟[SQL 審核](https://docs.microsoft.com/sql/relational-databases/security/auditing/sql-server-audit-database-engine)，並將其與警示機制整合。
- 從[Advanced Data Security （ADS）](https://docs.microsoft.com/azure/sql-database/sql-database-advanced-data-security)套件開啟[威脅偵測](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection)。


## <a name="cost-saving-use-cases"></a>節省成本的使用案例

**哪裡可以找到使用案例，並使用受控實例節省成本？**

受控實例案例研究：

- [Komatsu](https://customers.microsoft.com/story/komatsu-australia-manufacturing-azure)
- [KMD](https://customers.microsoft.com/en-ca/story/kmd-professional-services-azure-sql-database)
- [PowerDETAILS](https://customers.microsoft.com/story/powerdetails-partner-professional-services-azure-sql-database-managed-instance)
- [Allscripts](https://customers.microsoft.com/story/allscripts-partner-professional-services-azure)  
為了進一步瞭解與部署 Azure SQL Database 受控實例相關的優點、成本和風險，另外還有 Forrester 的研究： [MI 的總經濟影響](https://azure.microsoft.com/resources/forrester-tei-sql-database-managed-instance)。


## <a name="dns-refresh"></a>DNS 重新整理 

**我可以進行 DNS 重新整理嗎？**

目前，我們並未提供重新整理受控實例之 DNS 伺服器設定的功能。

DNS 設定最後會重新整理：

- 當 DHCP 租用到期時。
- 平臺升級。

因應措施是將受控實例降級為 4 vCore，之後再升級。 這會產生重新整理 DNS 設定的副作用。


## <a name="ip-address"></a>IP 位址

**我可以使用 IP 位址連接到受控實例嗎？**

不支援使用 IP 位址連接到受控實例。 受控實例主機名稱會對應至受控實例虛擬叢集前方的負載平衡器。 因為一個虛擬叢集可以裝載多個受管理的實例連線，所以無法將其指定為其名稱，而無法路由傳送至適當的受控實例。

如需受控實例虛擬叢集架構的詳細資訊，請參閱[虛擬叢集連線架構](sql-database-managed-instance-connectivity-architecture.md#virtual-cluster-connectivity-architecture)。

**受控實例可以有靜態 IP 位址嗎？**

在罕見但必要的情況下，我們可能需要在線上將受控實例遷移至新的虛擬叢集。 如有需要，這項遷移是因為我們的技術堆疊有所變更，目的在於改善服務的安全性和可靠性。 遷移至新的虛擬叢集會導致變更對應至受控實例主機名稱的 IP 位址。 受控實例服務不會宣告靜態 IP 位址支援，並保留變更的權利，而不會在一般維護週期中另行通知。

基於這個理由，我們強烈建議您不要依賴不必要的 IP 位址，因為這可能會造成不必要的停機時間。

## <a name="change-time-zone"></a>變更時區

**我可以變更現有受控實例的時區嗎？**

當您第一次布建受控實例時，可以設定時區設定。 不支援變更現有受控實例的時區。 如需詳細資訊，請參閱[時區限制](sql-database-managed-instance-timezone.md#limitations)。

因應措施包括建立具有適當時區的新受控實例，然後執行手動備份和還原，或我們建議的方法，執行[跨實例的時間點還原](https://blogs.msdn.microsoft.com/sqlserverstorageengine/2018/06/07/cross-instance-point-in-time-restore-in-azure-sql-database-managed-instance/)。


## <a name="resolve-performance-issues"></a>解決效能問題

**如何? 解決受控實例的效能問題嗎？**

如需受控實例與 SQL Server 之間的效能比較，最好的起點是[AZURE SQL 受控實例與 SQL Server 文章之間效能比較的最佳作法](https://techcommunity.microsoft.com/t5/Azure-SQL-Database/The-best-practices-for-performance-comparison-between-Azure-SQL/ba-p/683210)。

在受控實例上載入資料的速度通常會比 SQL Server，因為強制的完整復原模式和交易記錄寫入輸送量的[限制](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-resource-limits#service-tier-characteristics)。 有時候，這可以藉由將暫時性資料載入至 tempdb，而不是使用者資料庫，或使用叢集資料行存放區或記憶體優化資料表來解決。


## <a name="restore-encrypted-backup"></a>還原加密的備份

**我可以將加密的資料庫還原到受控實例嗎？**

是，您不需要解密資料庫，就能夠將它還原到受控實例。 您需要提供一個憑證/金鑰，做為來源系統中的加密金鑰保護裝置，使其能夠從加密的備份檔案讀取資料。 要執行此動作有兩個可行的方式：

- 將*憑證保護裝置上傳至受控實例*。 只能使用 PowerShell 來完成。 [範例腳本](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-migrate-tde-certificate)會描述整個程式。
- 將*非對稱金鑰保護裝置上傳至 Azure Key Vault （AKV），並將受控實例指向它*。 這種方法類似于攜帶您自己的金鑰（BYOK） TDE 使用案例，也會使用 AKV 整合來儲存加密金鑰。 如果您不想要使用金鑰做為加密金鑰保護裝置，而且只想要讓受控實例能夠還原加密的資料庫，請遵循[設定 BYOK TDE](https://docs.microsoft.com/azure/sql-database/transparent-data-encryption-azure-sql#manage-transparent-data-encryption)的指示，而不要核取 [*將選取的金鑰設為預設的 TDE 保護*裝置] 核取方塊。

將加密保護裝置提供給受控實例之後，您就可以繼續進行標準資料庫還原程式。

## <a name="migrate-from-single-db"></a>從單一資料庫移轉 

**如何從 Azure SQL Database 單一或彈性集區遷移至受控實例？**

受控實例會針對每個計算和儲存體大小提供相同的效能等級，做為 Azure SQL Database 的其他部署選項。 如果您想要在單一實例上合併資料，或只需要在受控實例中以獨佔方式支援的功能，您可以使用匯出/匯入（BACPAC）功能來遷移資料。
