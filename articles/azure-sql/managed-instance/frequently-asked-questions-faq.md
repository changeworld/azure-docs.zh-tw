---
title: 常見問題集 (FAQ)
titleSuffix: Azure SQL Managed Instance
description: Azure SQL 受控執行個體常見問題（FAQ）
services: sql-database
ms.service: sql-managed-instance
ms.subservice: operations
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: sstein, carlrab
ms.date: 03/17/2020
ms.openlocfilehash: 88f92117dc07fc241ca714851956e386cd10d617
ms.sourcegitcommit: e995f770a0182a93c4e664e60c025e5ba66d6a45
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/08/2020
ms.locfileid: "86135023"
---
# <a name="azure-sql-managed-instance-frequently-asked-questions-faq"></a>Azure SQL 受控執行個體常見問題（FAQ）
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

本文包含有關[AZURE SQL 受控執行個體](sql-managed-instance-paas-overview.md)最常見的問題。

## <a name="supported-features"></a>支援的功能

**我可以在哪裡找到 SQL 受控執行個體支援的功能清單？**

如需 SQL 受控執行個體中支援的功能清單，請參閱[AZURE sql 受控執行個體功能](../database/features-comparison.md)。

如需 Azure SQL 受控執行個體和 SQL Server 之間的語法和行為差異，請參閱[SQL Server 的 t-sql 差異](transact-sql-tsql-differences-sql-server.md)。


## <a name="tech-spec--resource-limits"></a>技術規格 & 資源限制
 
**哪裡可以找到 SQL 受控執行個體的技術特性和資源限制？**

如需可用的硬體產生特性，請參閱[硬體世代中的技術差異](resource-limits.md#hardware-generation-characteristics)。
如需可用的服務層級及其特性，請參閱[服務層級之間的技術差異](resource-limits.md#service-tier-characteristics)。

## <a name="known-issues--bugs"></a>已知問題 & bug

**哪裡可以找到已知的問題和 bug？**

如有錯誤和已知問題，請參閱[已知問題](../database/doc-changes-updates-release-notes.md#known-issues)。

## <a name="new-features"></a>新功能

**我可以在哪裡找到最新的功能和公開預覽中的功能？**

如需新的和預覽功能，請參閱[版本](../database/doc-changes-updates-release-notes.md?tabs=managed-instance)資訊。

## <a name="deployment-times"></a>部署時間 

**建立或更新實例或還原資料庫需要多久的時間？**

建立受控實例或變更服務層（虛擬核心、儲存體）的預期時間取決於數個因素。 請參閱[管理作業](/azure/sql-database/sql-database-managed-instance#managed-instance-management-operations)。 

## <a name="naming-conventions"></a>命名規範

**受控實例可以與 SQL Server 內部部署實例的名稱相同嗎？**

不支援變更受控實例名稱。

受控實例的預設 DNS 區域*database.windows.net*可以變更。 

若要使用另一個 DNS 區域，而非預設值，例如 *. contoso.com*： 
- 使用 CliConfig 來定義別名。 此工具只是一個登錄設定包裝函式，因此也可以使用群組原則或腳本來完成。
- 使用*CNAME*搭配*TrustServerCertificate = true*選項。

## <a name="move-a-database-from-sql-managed-instance"></a>從 SQL 受控執行個體移動資料庫 

**如何將資料庫從 SQL 受控執行個體移回 SQL Server 或 Azure SQL Database？**

您可以將[資料庫匯出至 bacpac](../database/database-export.md) ，然後匯[入 bacpac](../database/database-import.md)檔案。 如果您的資料庫小於 100 GB，則這是建議的方法。

如果資料庫中的所有資料表都有主鍵，則可以使用異動複寫。

`COPY_ONLY`從 sql 受控執行個體取得的原生備份無法還原到 SQL Server，因為 sql 受控執行個體與 SQL Server 相比，資料庫版本較高。

## <a name="migrate-an-instance-database"></a>遷移實例資料庫

**如何將實例資料庫移轉至 Azure SQL Database？**

其中一個選項是將[資料庫匯出至 bacpac](../database/database-export.md) ，然後匯[入 bacpac](../database/database-import.md)檔案。 

如果您的資料庫小於 100 GB，則這是建議的方法。 如果資料庫中的所有資料表都有主鍵，則可以使用異動複寫。

## <a name="switch-hardware-generation"></a>交換器硬體世代 

**我可以在第4代與第5代連線之間切換 SQL 受控執行個體硬體世代嗎？**

如果在布建 SQL 受控執行個體的區域中有兩個硬體層代，則可以在硬體世代之間進行自動線上切換。 在這種情況下，您可以查看[vCore 模型的 [總覽] 頁面](../database/service-tiers-vcore.md)，其中說明如何在硬體層代之間切換。

這是長時間執行的作業，因為新的受控實例將會在背景中布建，而資料庫會在程式結束時自動在新的實例之間轉移。 

**如果同一個區域中不支援這兩個硬體層代，該怎麼辦？**

如果相同區域中不支援這兩種硬體層代，則可以變更硬體世代，但必須手動完成。 這會要求您在需要產生硬體的區域中布建新的實例，並在舊的和新的實例之間手動備份和還原資料。

**如果沒有足夠的 IP 位址可執行更新作業，會發生什麼事？**

如果您的受控實例布建所在的子網中沒有足夠的 IP 位址，您就必須在其中建立新的子網和新的受控實例。 我們也建議您建立新的子網，並將更多 IP 位址配置給它，讓未來的更新作業可以避免類似的情況。 （如需適當的子網大小，請檢查[如何判斷 VNet 子網的大小](vnet-subnet-determine-size.md)）。布建新的實例之後，您可以在舊的和新的實例之間手動備份和還原資料，或執行跨實例的[時間點還原](point-in-time-restore.md?tabs=azure-powershell)。 


## <a name="tune-performance"></a>微調效能

**如何? 微調 SQL 受控執行個體的效能嗎？**

一般用途層中的 SQL 受控執行個體會使用遠端存放裝置，因此資料和記錄檔的大小對效能很重要。 如需詳細資訊，請參閱[一般用途 SQL 受控執行個體效能的記錄檔大小影響](https://medium.com/azure-sqldb-managed-instance/impact-of-log-file-size-on-general-purpose-managed-instance-performance-21ad170c823e)。

如果您的工作負載包含許多小型交易，請考慮將連線類型從 proxy 切換到重新導向模式。

## <a name="maximum-storage-size"></a>儲存體大小上限

**SQL 受控執行個體的儲存體大小上限為何？**

SQL 受控執行個體的儲存體大小取決於選取的服務層級（一般用途或業務關鍵）。 如需這些服務層級的儲存體限制，請參閱[服務層特性](../database/service-tiers-general-purpose-business-critical.md)。

  
## <a name="networking-requirements"></a>網路需求 

**受控執行個體子網上的目前輸入/輸出 NSG 條件約束為何？**

必要的 NSG 和 UDR 規則記載于[此](connectivity-architecture-overview.md#mandatory-inbound-security-rules-with-service-aided-subnet-configuration)，並由服務自動設定。
請記住，這些規則只是維護服務所需的規則。 若要連線到受控實例並使用不同的功能，您必須設定其他需要維護的功能特定規則。

**如何設定管理埠上的輸入 NSG 規則？**

SQL 受控執行個體負責設定管理埠上的規則。 這是透過名為[服務輔助子網](connectivity-architecture-overview.md#service-aided-subnet-configuration)設定的功能來達成。
這是為了確保不中斷管理流量的流程，以履行 SLA。

**我可以取得用於輸入管理流量的來源 IP 範圍嗎？**

是。 您可以藉由設定[網路監看員流量記錄](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview#analyze-traffic-to-or-from-a-network-security-group)，來分析透過網路安全性群組傳入的流量。

**我可以設定 NSG 來控制對資料端點的存取（埠1433）嗎？**

是。 布建受控執行個體之後，您可以設定 NSG 來控制埠1433的輸入存取。 建議盡可能縮小其 IP 範圍。

**我可以設定 NVA 或內部部署防火牆，以根據 Fqdn 篩選輸出管理流量嗎？**

否。 這種情況不受支援，原因如下：
-   代表回應輸入管理要求的路由流量會是非對稱的，而且無法正常執行。
-   路由傳送至儲存體的流量會受到輸送量限制和延遲的影響，因此我們無法提供預期的服務品質和可用性。
-   根據經驗，這些設定容易發生錯誤且無法支援。

**我可以針對輸出非管理流量設定 NVA 或防火牆嗎？**

是。 達成此目標最簡單的方式，就是將0/0 規則新增至與受控實例子網相關聯的 UDR，以透過 NVA 路由傳送流量。
 
**受控執行個體需要多少個 IP 位址？**

子網必須有足夠的可用[IP 位址](connectivity-architecture-overview.md#network-requirements)數目。 若要判斷 SQL 受控執行個體的 VNet 子網大小，請參閱[決定所需的子網大小和受控執行個體的範圍](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-determine-size-vnet-subnet)。 

**如果沒有足夠的 IP 位址可執行實例更新作業，會發生什麼事？**

如果您的受控實例布建所在的子網中沒有足夠的[IP 位址](connectivity-architecture-overview.md#network-requirements)，您就必須在其中建立新的子網和新的受控實例。 我們也建議您建立新的子網，並將更多 IP 位址配置給它，讓未來的更新作業可以避免類似的情況。 布建新的實例之後，您可以在舊的和新的實例之間手動備份和還原資料，或執行跨實例的[時間點還原](point-in-time-restore.md?tabs=azure-powershell)。

**我是否需要空的子網來建立受控執行個體？**

否。 您可以使用空的子網或已包含受控執行個體的子網。 

**我可以變更子網位址範圍嗎？**

如果內有受管理的實例，則不是。 這是 Azure 網路基礎結構的限制。 您只允許[將其他位址空間新增至空白子網](https://docs.microsoft.com/azure/virtual-network/virtual-network-manage-subnet#change-subnet-settings)。 

**我可以將受控實例移至另一個子網嗎？**

否。 這是目前的受控執行個體設計限制。 不過，您可以在另一個子網中布建新的實例，並在舊的和新的實例之間手動備份和還原資料，或執行跨實例的[時間點還原](point-in-time-restore.md?tabs=azure-powershell)。

**我是否需要空的虛擬網路來建立受控執行個體？**

這不是必要項。 您可以[為 AZURE sql 受控執行個體建立虛擬網路](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-create-vnet-subnet)，或[為 Azure sql 受控執行個體設定現有的虛擬網路](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-configure-vnet-subnet)。

**我是否可以將受控執行個體與子網中的其他服務搭配使用？**

否。 目前，我們不支援將受控執行個體放在已包含其他資源類型的子網中。

## <a name="connectivity"></a>連線能力 

**我可以使用 IP 位址連接到我的受控實例嗎？**

不行，不支援此方式。 受控執行個體的主機名稱會對應至受控執行個體虛擬叢集前方的負載平衡器。 當一個虛擬叢集可以裝載多個受控實例時，連接無法路由傳送至適當的受控執行個體，而不需要指定其名稱。
如需 SQL 受控執行個體虛擬叢集架構的詳細資訊，請參閱[虛擬叢集連線架構](connectivity-architecture-overview.md#virtual-cluster-connectivity-architecture)。

**受控實例可以有靜態 IP 位址嗎？**

目前不支援。

在罕見但必要的情況下，我們可能需要在線上將受控實例遷移至新的虛擬叢集。 如有需要，這項遷移是因為我們的技術堆疊有所變更，目的在於改善服務的安全性和可靠性。 遷移至新的虛擬叢集會導致變更對應至受控實例主機名稱的 IP 位址。 受控實例服務不會宣告靜態 IP 位址支援，並保留變更的權利，而不會在一般維護週期中另行通知。

基於這個理由，我們強烈建議您不要依賴不必要的 IP 位址，因為這可能會造成不必要的停機時間。

**受控執行個體有公用端點嗎？**

是。 受控執行個體具有僅供服務管理使用的公用端點，但客戶也可將其啟用資料存取。 如需詳細資訊，請參閱[使用具有公用端點的 SQL 受控執行個體](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-public-endpoint-securely)。 若要設定公用端點，請移至[在 SQL 受控執行個體中設定公用端點](public-endpoint-configure.md)。

**受控執行個體如何控制公用端點的存取權？**

受控執行個體可控制對網路和應用層級的公用端點的存取。

管理和部署服務會使用對應到外部負載平衡器的[管理端點](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-connectivity-architecture#management-endpoint)，連接到受控實例。 只有在只有受控實例的管理元件使用的一組預先定義的埠上接收到時，才會將流量路由傳送至節點。 節點上的內建防火牆設定為只允許來自 Microsoft IP 範圍的流量。 憑證會相互驗證管理元件與管理平面之間的所有通訊。 如需詳細資訊，請參閱[SQL 受控執行個體的連接架構](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-connectivity-architecture#virtual-cluster-connectivity-architecture)。

**我可以使用公用端點來存取受控執行個體資料庫中的資料嗎？**

是。 客戶必須從[Azure 入口網站](public-endpoint-configure.md#enabling-public-endpoint-for-a-managed-instance-in-the-azure-portal)  /  [PowerShell](public-endpoint-configure.md#enabling-public-endpoint-for-a-managed-instance-using-powershell) /ARM 啟用公用端點資料存取，並將 NSG 設定為鎖定對資料埠的存取（埠號碼3342）。 如需詳細資訊，請參閱[在 AZURE sql 受控執行個體中設定公用端點](public-endpoint-configure.md)和[使用 azure sql 受控執行個體安全地搭配公用端點](public-endpoint-overview.md)。 

**我可以為 SQL 資料端點指定自訂埠嗎？**

否，無法使用此選項。  對於私用資料端點，受控執行個體會使用預設埠號碼1433，而對於公用資料端點，受控執行個體會使用預設埠號碼3342。

**連接位於不同區域之受控實例的建議方式為何？**

Express Route 線路對等互連是執行此動作的最佳方式。 這不會與跨區域虛擬網路對等互連混合，因為內部負載平衡器相關的[條件約束](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview)不支援。

如果不可能有 Express Route 線路對等互連，唯一的另一個選項是建立站對站 VPN 連線（[Azure 入口網站](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal)、 [PowerShell](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell)、 [Azure CLI](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-cli)）。


## <a name="mitigate-data-exfiltration-risks"></a>降低資料外泄風險  

**如何減少資料外泄風險？**

若要降低任何資料外泄風險，建議客戶套用一組安全性設定和控制項：

- 在所有資料庫上開啟[透明資料加密（TDE）](https://docs.microsoft.com/azure/sql-database/transparent-data-encryption-azure-sql) 。
- 關閉 Common Language Runtime （CLR）。 這在內部部署中也是建議的做法。
- 僅使用 Azure Active Directory （Azure AD）驗證。
- 存取具有低許可權 DBA 帳戶的實例。
- 設定系統管理員（sysadmin）帳戶的 JIT jumpbox 存取。
- 開啟[SQL 審核](https://docs.microsoft.com/sql/relational-databases/security/auditing/sql-server-audit-database-engine)，並將其與警示機制整合。
- 從[advanced data security （ADS）](https://docs.microsoft.com/azure/sql-database/sql-database-advanced-data-security)套件開啟[威脅偵測](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection)。


## <a name="cost-saving-use-cases"></a>節省成本的使用案例

**哪裡可以找到使用案例，並利用 SQL 受控執行個體節省成本？**

SQL 受控執行個體案例研究：

- [Komatsu](https://customers.microsoft.com/story/komatsu-australia-manufacturing-azure)
- [KMD](https://customers.microsoft.com/en-ca/story/kmd-professional-services-azure-sql-database)
- [PowerDETAILS](https://customers.microsoft.com/story/powerdetails-partner-professional-services-azure-sql-database-managed-instance)
- [Allscripts](https://customers.microsoft.com/story/allscripts-partner-professional-services-azure)

若要進一步瞭解與部署 Azure SQL 受控執行個體相關的優點、成本和風險，還有一項 Forrester 研究： [Microsoft Azure SQL Database 受控執行個體的整體經濟影響](https://azure.microsoft.com/resources/forrester-tei-sql-database-managed-instance)。


## <a name="dns"></a>DNS

**我可以設定 SQL 受控執行個體的自訂 DNS 嗎？**

是。 請參閱[如何設定 AZURE SQL 受控執行個體的自訂 DNS](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-custom-dns)。

**我可以進行 DNS 重新整理嗎？**

目前，我們不提供重新整理 SQL 受控執行個體的 DNS 伺服器設定的功能。

DNS 設定最後會重新整理：

- 當 DHCP 租用到期時。
- 平臺升級。

因應措施是將 SQL 受控執行個體降級為4虛擬核心，之後再升級。 這會產生重新整理 DNS 設定的副作用。


## <a name="change-time-zone"></a>變更時區

**我可以變更現有受控實例的時區嗎？**

當您第一次布建受控實例時，可以設定時區設定。 不支援變更現有受控實例的時區。 如需詳細資訊，請參閱[時區限制](timezones-overview.md#limitations)。

因應措施包括以適當的時區建立新的受控實例，然後執行手動備份和還原，或建議您執行[跨實例的時間點還原](https://blogs.msdn.microsoft.com/sqlserverstorageengine/2018/06/07/cross-instance-point-in-time-restore-in-azure-sql-database-managed-instance/)。


## <a name="resolve-performance-issues"></a>解決效能問題

**如何? 解決 SQL 受控執行個體的效能問題嗎？**

如需 SQL 受控執行個體和 SQL Server 之間的效能比較，最好的起點是[AZURE SQL 受控執行個體與 SQL Server 之間效能比較的最佳作法](https://techcommunity.microsoft.com/t5/Azure-SQL-Database/The-best-practices-for-performance-comparison-between-Azure-SQL/ba-p/683210)。

由於必要的完整復原模式和交易記錄寫入輸送量的[限制](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-resource-limits#service-tier-characteristics)，SQL 受控執行個體上的資料載入通常會比 SQL Server 的速度慢。 有時候，這可以藉由將暫時性資料載入至 tempdb，而不是使用者資料庫，或是使用叢集資料行存放區或記憶體優化資料表來解決。


## <a name="restore-encrypted-backup"></a>還原加密的備份

**我可以將加密的資料庫還原到 SQL 受控執行個體嗎？**

是，您不需要解密資料庫，將它還原到 SQL 受控執行個體。 您需要提供一個憑證/金鑰，做為來源系統上的加密金鑰保護裝置受控執行個體，以便從加密的備份檔案讀取資料。 要執行此動作有兩個可行的方式：

- *將憑證保護裝置上傳至 SQL 受控執行個體*。 只能使用 PowerShell 來完成。 [範例腳本](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-migrate-tde-certificate)會描述整個程式。
- 將*非對稱金鑰保護裝置上傳至 Azure Key Vault，並將 SQL 受控執行個體指向其中*。 這種方法類似于攜帶您自己的金鑰（BYOK） TDE 使用案例，也會使用 Key Vault 整合來儲存加密金鑰。 如果您不想使用金鑰做為加密金鑰保護裝置，而且只想要讓 SQL 受控執行個體的金鑰還原加密的資料庫，請遵循[設定 BYOK TDE](https://docs.microsoft.com/azure/sql-database/transparent-data-encryption-azure-sql#manage-transparent-data-encryption)的指示，而不要核取 [**將選取的金鑰設為預設的 TDE 保護**裝置] 核取方塊。

一旦將加密保護裝置提供給 SQL 受控執行個體之後，您就可以繼續進行標準資料庫還原程式。

## <a name="purchasing-models-and-benefits"></a>購買模型和優點

**適用于 SQL 受控執行個體的購買模型有哪些？**

SQL 受控執行個體提供[vCore 為基礎的購買模型](sql-managed-instance-paas-overview.md#vcore-based-purchasing-model)。

**SQL 受控執行個體有哪些成本效益？**

您可以利用下列方式，以 Azure SQL 權益節省成本：
-   最大化內部部署授權的現有投資，並使用[Azure Hybrid Benefit](https://docs.microsoft.com/azure/azure-sql/azure-hybrid-benefit?tabs=azure-powershell)省下高達55% 的成本。 
-   認可計算資源的保留，並使用[保留實例權益](https://docs.microsoft.com/azure/sql-database/sql-database-reserved-capacity)省下高達33% 的費用。 結合此項與 Azure 混合式權益，省下高達82% 的費用。 
-   使用[Azure 開發/測試定價權益](https://azure.microsoft.com/pricing/dev-test/)，為您進行中的開發和測試工作負載提供折扣費率，最高可省下55% 的費用與標價。

**誰符合保留實例權益的資格？**

若要符合保留實例權益的資格，您的訂用帳戶類型必須是 enterprise 合約（供應專案號碼： MS-AZR-0017P-Ms-azr-0017p 或 MS-MS-AZR-0017P-Ms-azr-0148p）或具有隨用隨付定價的個別合約（供應專案號碼： MS-MS-AZR-0017P-Ms-azr-0003p 或 MS-MS-AZR-0017P-Ms-azr-0023p）。 如需保留的詳細資訊，請參閱[保留實例權益](https://docs.microsoft.com/azure/sql-database/sql-database-reserved-capacity)。 

**可以取消、交換或退款保留嗎？**

您可以取消、交換或退款保留，但有某些限制。 如需詳細資訊，請參閱 [Azure 保留的自助式交換和退費](https://docs.microsoft.com/azure/cost-management-billing/reservations/exchange-and-refund-azure-reservations)。

## <a name="billing-for-managed-instance-and-backup-storage"></a>受控執行個體和備份儲存體的計費

**SQL 受控執行個體定價選項有哪些？**

若要探索受控執行個體定價選項，請參閱[定價頁面](https://azure.microsoft.com/pricing/details/azure-sql/sql-managed-instance/single/)。

**我要如何追蹤受控實例的帳單費用？**

您可以使用[Azure 成本管理方案](https://docs.microsoft.com/azure/cost-management-billing/)來執行此動作。 流覽至[Azure 入口網站](https://portal.azure.com)**中的 [** 訂用帳戶]，然後選取 [**成本分析**]。 

使用 [**累積的成本**] 選項，然後依**資源類型**篩選為 `microsoft.sql/managedinstances` 。

**多少自動備份費用？**

不論已設定備份保留期限，您都可以取得與購買的保留資料儲存空間相同的可用備份儲存空間數量。 如果您的備份儲存體耗用量是在配置的可用備份儲存空間內，則受控實例上的自動備份將不會有額外費用，因此將免費提供給您。 超過可用空間以上的備份儲存體使用量，會導致美國地區每 GB/每月大約 $0.20-$0.24 的成本，或參閱定價頁面以取得您的區域詳細資料。 如需詳細資訊，請參閱[說明的備份儲存體耗用量](https://techcommunity.microsoft.com/t5/azure-sql-database/backup-storage-consumption-on-managed-instance-explained/ba-p/1390923)。

**如何監視備份儲存體耗用量的計費成本？**

您可以透過 Azure 入口網站監視備份儲存體的成本。 如需指示，請參閱[監視自動備份的成本](https://docs.microsoft.com/azure/azure-sql/database/automated-backups-overview?tabs=managed-instance#monitor-costs)。 

**如何將受控實例上的備份儲存體成本優化？**

若要優化您的備份儲存體成本，請參閱[SQL 受控執行個體的微調備份](https://techcommunity.microsoft.com/t5/azure-sql-database/fine-tuning-backup-storage-costs-on-managed-instance/ba-p/1390935)微調。

## <a name="password-policy"></a>密碼原則 

**適用于 SQL 受控執行個體 SQL 登入的密碼原則為何？**

適用于 SQL 登入的 SQL 受控執行個體密碼原則會繼承套用至組成虛擬叢集之 Vm 的 Azure 平臺原則，而這些虛擬叢集會保留受控實例。 目前無法變更這些設定的任何一項，因為這些設定是由 Azure 所定義，並由受控實例繼承。

 > [!IMPORTANT]
 > Azure 平臺可以變更原則需求，而不會通知服務依賴該原則。

**什麼是目前的 Azure 平臺原則？**

每個登入都必須在登入時設定其密碼，並在達到最長存留期後變更其密碼。

| **原則** | **安全性設定** |
| --- | --- |
| 密碼最長使用期限 | 42天 |
| 密碼最短使用期限 | 1 日 |
| 密碼長度下限 | 10個字元 |
| 密碼必須符合複雜性需求 | 已啟用 |

**是否可以停用登入層級上的 SQL 受控執行個體中的密碼複雜性和到期？**

是，您可以控制登入層級的 CHECK_POLICY 和 CHECK_EXPIRATION 欄位。 您可以藉由執行下列 T-sql 命令來檢查目前的設定：

```sql
SELECT *
FROM sys.sql_logins
```

之後，您可以藉由執行下列動作來修改指定的登入設定：

```sql
ALTER LOGIN <login_name> WITH CHECK_POLICY = OFF;
ALTER LOGIN <login_name> WITH CHECK_EXPIRATION = OFF;
```

（以所需的登入名稱取代 ' test '，並調整原則和到期值）

## <a name="azure-feedback-and-support"></a>Azure 意見反應與支援

**我可以在哪裡留下 SQL 受控執行個體改進的想法？**

您可以針對新的受控執行個體功能進行投票，或為[SQL 受控執行個體意見反應論壇提出](https://feedback.azure.com/forums/915676-sql-managed-instance)新的改進想法。 如此一來，您就可以參與產品開發，並協助我們設定潛在改進的優先順序。

**如何建立 Azure 支援要求？**

若要瞭解如何建立 Azure 支援要求，請參閱[如何建立 Azure 支援要求](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request)。

