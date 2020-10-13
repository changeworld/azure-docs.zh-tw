---
title: 常見問題集 (FAQ)
titleSuffix: Azure SQL Managed Instance
description: Azure SQL 受控執行個體常見問題集 (FAQ)
services: sql-database
ms.service: sql-managed-instance
ms.subservice: operations
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: sstein
ms.date: 09/21/2020
ms.openlocfilehash: fedbcf00512e2eb671656ca1c585df83560a8c02
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "91627613"
---
# <a name="azure-sql-managed-instance-frequently-asked-questions-faq"></a>Azure SQL 受控執行個體常見問題集 (FAQ)
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

本文包含有關 [AZURE SQL 受控執行個體](sql-managed-instance-paas-overview.md)的最常見問題。

## <a name="supported-features"></a>支援的功能

**哪裡可以找到 SQL 受控執行個體所支援的功能清單？**

如需 SQL 受控執行個體中支援的功能清單，請參閱 [AZURE sql 受控執行個體功能](../database/features-comparison.md)。

如需 Azure SQL 受控執行個體和 SQL Server 之間的語法和行為差異，請參閱 [SQL Server 的 t-sql 差異](transact-sql-tsql-differences-sql-server.md)。


## <a name="technical-specification-resource-limits-and-other-limitations"></a>技術規格、資源限制及其他限制
 
**哪裡可以找到 SQL 受控執行個體的技術特性和資源限制？**

如需可用的硬體產生特性，請參閱 [硬體世代中的技術差異](resource-limits.md#hardware-generation-characteristics)。
如需可用的服務層級及其特性，請參閱 [服務層級之間的技術差異](resource-limits.md#service-tier-characteristics)。

**我有哪些服務層級符合資格？**

任何客戶都有資格享有任何服務層級。 但是，如果您想要在 Azure SQL 受控執行個體上使用 [Azure Hybrid Benefit](https://azure.microsoft.com/pricing/hybrid-benefit/)來交換您現有的授權以取得折扣優惠，請記住，使用軟體保證 SQL Server Enterprise edition 客戶符合 [一般用途](../database/service-tier-general-purpose.md) 或 [業務關鍵](../database/service-tier-business-critical.md) 效能層級的資格，以及 SQL Server Standard Edition 客戶（僅限使用軟體保證）符合一般用途效能層級。 如需詳細資訊，請參閱 [AHB 的特定許可權](../azure-hybrid-benefit.md?tabs=azure-powershell#what-are-the-specific-rights-of-the-azure-hybrid-benefit-for-sql-server)。

**SQL 受控執行個體支援哪些訂用帳戶類型？**

如需支援的訂用帳戶類型清單，請參閱 [支援的訂](resource-limits.md#supported-subscription-types)用帳戶類型。 

**支援哪些 Azure 區域？**

您可以在大部分的 Azure 區域中建立受控實例;請參閱 [SQL 受控執行個體的支援區域](https://azure.microsoft.com/global-infrastructure/services/?products=sql-database&regions=all)。 如果您在目前不支援的區域中需要受控實例，請透過 [Azure 入口網站傳送支援要求](../database/quota-increase-request.md)。

**SQL 受控執行個體部署是否有任何配額限制？**

受控實例有兩個預設限制：您可以使用的子網數目限制，以及您可以布建的虛擬核心數目限制。 各訂用帳戶類型和區域的限制各有不同。 如需依訂用帳戶類型的區域資源限制清單，請參閱來自 [區域資源限制](resource-limits.md#regional-resource-limitations)的表格。 這些是可隨需增加的軟限制。 如果您需要在目前的區域中布建更多受控實例，請傳送支援要求以使用 Azure 入口網站增加配額。 如需詳細資訊，請參閱 [Azure SQL Database 的要求配額增加](../database/quota-increase-request.md)。

**我是否可以依需求增加受管理的實例上的資料庫數目 (100) 限制？**

否，目前沒有任何已認可的計畫可增加 SQL 受控執行個體上的資料庫數目。 

**如果我有超過8TB 的資料，可以在哪裡進行遷移？**
您可以考慮遷移至符合您工作負載的其他 Azure 風格： [Azure SQL Database 超大規模](../database/service-tier-hyperscale.md) 或 [azure 虛擬機器上的 SQL Server](../virtual-machines/windows/sql-server-on-azure-vm-iaas-what-is-overview.md)。

**如果我有特定的硬體需求（例如，較大的 RAM vCore 比例或更多 Cpu），我可以在哪裡進行遷移？**
您可以考慮遷移至 [Azure 虛擬機器上的 SQL Server](../virtual-machines/windows/sql-server-on-azure-vm-iaas-what-is-overview.md) 或 [Azure SQL Database](../database/sql-database-paas-overview.md) 記憶體/cpu 優化。

## <a name="known-issues-and-defects"></a>已知問題與瑕疵

**我可以在哪裡找到已知的問題和瑕疵？**

如有產品瑕疵和已知問題，請參閱 [已知問題](../database/doc-changes-updates-release-notes.md#known-issues)。

## <a name="new-features"></a>新功能

**我可以在哪裡找到最新的功能和公開預覽的功能？**

如需新功能和預覽功能，請參閱 [版本](../database/doc-changes-updates-release-notes.md?tabs=managed-instance)資訊。

## <a name="create-update-delete-or-move-sql-managed-instance"></a>建立、更新、刪除或移動 SQL 受控執行個體

**如何布建 SQL 受控執行個體？**

您可以從 [Azure 入口網站](instance-create-quickstart.md)、 [PowerShell](scripts/create-configure-managed-instance-powershell.md)、 [Azure CLI](https://techcommunity.microsoft.com/t5/azure-sql-database/create-azure-sql-managed-instance-using-azure-cli/ba-p/386281) 和 [ARM 範本](https://docs.microsoft.com/archive/blogs/sqlserverstorageengine/creating-azure-sql-managed-instance-using-arm-templates)布建實例。

**我可以在現有的訂用帳戶中布建受控實例嗎？**

是的，如果訂用帳戶屬於 [支援的訂](resource-limits.md#supported-subscription-types)用帳戶類型，您就可以在現有的訂用帳戶中布建受控執行個體。

**為什麼我無法在子網中布建名稱以數位開頭的受控執行個體？**

這是基礎元件目前的限制，可根據 RegEx ^ [a-zA-Z_] [^ \\ \/ \: \* \? \" \<\> \| \` \' \^ ] * (？ <！ [ \. 的子網名稱進行驗證\s] ) $。 目前支援傳遞 RegEx 的所有名稱以及有效的子網名稱。

**如何調整受控實例？**

您可以從 [Azure 入口網站](../database/service-tiers-vcore.md?tabs=azure-portal#selecting-a-hardware-generation)、 [PowerShell](https://docs.microsoft.com/archive/blogs/sqlserverstorageengine/change-size-azure-sql-managed-instance-using-powershell)、 [Azure CLI](https://docs.microsoft.com/cli/azure/sql/mi?view=azure-cli-latest#az-sql-mi-update&preserve-view=true) 或 [ARM 範本](https://docs.microsoft.com/archive/blogs/sqlserverstorageengine/updating-azure-sql-managed-instance-properties-using-arm-templates)來調整受控實例。

**我可以將我的受控執行個體從一個區域移到另一個區域嗎？**

是，您可以。 如需相關指示，請參閱 [跨區域移動資源](../database/move-resources-across-regions.md)。

**如何刪除我的受控執行個體？**

您可以透過 Azure 入口網站、 [PowerShell](https://docs.microsoft.com/powershell/module/az.sql/remove-azsqlinstance?view=azps-4.3.0&preserve-view=true)、 [AZURE CLI](https://docs.microsoft.com/cli/azure/sql/mi?view=azure-cli-latest#az-sql-mi-delete&preserve-view=true) 或 [Resource Manager REST api](https://docs.microsoft.com/rest/api/sql/managedinstances/delete)來刪除受控實例。

**建立或更新實例或還原資料庫需要花費多少時間？**

建立新的受控實例或變更服務層 (虛擬核心，儲存體) 的預期時間取決於數個因素。 請參閱 [管理作業](sql-managed-instance-paas-overview.md#management-operations)。
 
## <a name="naming-conventions"></a>命名規範

**受控實例的名稱是否可以與 SQL Server 的內部部署實例相同？**

不支援變更受控實例名稱。

**我可以變更 DNS 區域首碼嗎？**

是，受控執行個體預設的 DNS 區域 *。 database.windows.net* 可以變更。 

若要使用另一個 DNS 區域，而不是預設值，例如， *. contoso.com*： 
- 使用 CliConfig 來定義別名。 此工具只是一個登錄設定包裝函式，因此也可以使用群組原則或腳本來完成。
- 使用 *CNAME* 搭配 *TrustServerCertificate = true* 選項。

## <a name="migration-options"></a>移轉選項

**如何從 Azure SQL Database 單一或彈性集區遷移至 SQL 受控執行個體？**

受控實例提供每個計算和儲存體大小相同的效能等級，作為 Azure SQL Database 的其他部署選項。 如果您想要合併單一實例上的資料，或只需要受控實例中所支援的功能，您可以使用匯出/匯入 (BACPAC) 功能來遷移資料。 以下是考慮將 SQL Database 遷移至 SQL 受控執行個體的其他方法： 
- 使用 [外部資料源](https://techcommunity.microsoft.com/t5/azure-database-support-blog/lesson-learned-129-using-data-source-external-from-azure-sql/ba-p/1443210)
- 使用 [SQLPackage](https://techcommunity.microsoft.com/t5/azure-database-support-blog/how-to-migrate-azure-sql-database-to-azure-sql-managed-instance/ba-p/369182)
- 使用 [BCP](https://medium.com/azure-sqldb-managed-instance/migrate-from-azure-sql-managed-instance-using-bcp-674c92efdca7)

**如何將我的實例資料庫移轉至單一 Azure SQL Database？**

其中一個選項是將 [資料庫匯出至 bacpac](../database/database-export.md) ，然後匯 [入 bacpac](../database/database-import.md)檔案。 如果您的資料庫小於 100 GB，這是建議的方法。

如果資料庫中的所有資料表都有*主鍵*，而且資料庫中沒有任何記憶體內部 OLTP 物件，則可以使用[異動複寫](replication-two-instances-and-sql-server-configure-tutorial.md?view=sql-server-2017&preserve-view=true)。

從受控實例取得的原生 COPY_ONLY 備份無法還原到 SQL Server，因為相較于 SQL Server，受控實例具有較高的資料庫版本。 如需詳細資訊，請參閱 [僅複本備份](https://docs.microsoft.com/sql/relational-databases/backup-restore/copy-only-backups-sql-server?view=sql-server-ver15&preserve-view=true)。

**如何將 SQL Server 實例遷移至 SQL 受控執行個體？**

若要遷移您的 SQL Server 實例，請參閱 [SQL Server 實例遷移至 AZURE SQL 受控執行個體](migrate-to-instance-from-sql-server.md)。

**如何從其他平臺遷移至 SQL 受控執行個體？**

如需從其他平台移轉的移轉資訊，請參閱 [Azure 資料庫移轉指南](https://datamigration.microsoft.com/) \(英文\)。

## <a name="switch-hardware-generation"></a>交換器硬體世代 

**我可以在 Gen 4 和 Gen 5 之間，切換受控實例硬體世代嗎？**

如果您的受控實例布建所在的區域中有第5代硬體，就可以從第4代自動線上切換至第5代。 在此情況下，您可以檢查 [vCore 模型的總覽頁面](../database/service-tiers-vcore.md) ，其中說明如何在硬體世代之間切換。

這是長時間執行的作業，因為系統會在背景中布建新的受控實例，並在程式結束時自動容錯移轉，在新的實例之間自動傳送資料庫。

注意：第4代硬體即將推出，且不再適用于新的部署。 所有新的資料庫都必須部署在第5代硬體上。 也無法使用從第5代切換至第4代。

## <a name="performance"></a>效能 

**如何比較受控執行個體效能與 SQL Server 效能？**

如需受控實例與 SQL Server 之間的效能比較，最好的起點是 [AZURE SQL 受控實例與 SQL Server 文章之間效能比較的最佳做法](https://techcommunity.microsoft.com/t5/azure-sql-database/the-best-practices-for-performance-comparison-between-azure-sql/ba-p/683210) 。

**什麼會導致受控執行個體與 SQL Server 之間的效能差異？**

查看 [SQL 受控實例與 SQL Server 之間效能差異的主要原因](https://azure.microsoft.com/blog/key-causes-of-performance-differences-between-sql-managed-instance-and-sql-server/)。 如需有關一般用途受控執行個體效能之記錄檔大小影響的詳細資訊，請參閱 [一般用途之記錄檔大小的影響](https://medium.com/azure-sqldb-managed-instance/impact-of-log-file-size-on-general-purpose-managed-instance-performance-21ad170c823e)。

**如何? 調整受控實例的效能？**

您可以透過下列方式將受控實例的效能優化：
- [自動調整](../database/automatic-tuning-overview.md) 可透過以 AI 和機器學習為基礎的持續效能調整，來提供尖峰效能和穩定的工作負載。
-   [記憶體內部 OLTP](../in-memory-oltp-overview.md) 可改善交易處理工作負載的輸送量和延遲，並提供更快速的商業見解。 

若要更進一步地調整效能，請考慮套用[應用程式和資料庫調整](../database/performance-guidance.md#tune-your-database)的一些*最佳作法*。
如果您的工作負載是由許多小型交易所組成，請考慮將連線 [類型從 proxy 切換至重新導向模式](connection-types-overview.md#changing-connection-type) ，以降低延遲和提高輸送量。

## <a name="monitoring-metrics-and-alerts"></a>監視、計量和警示

**監視及警示受控實例的選項有哪些？**

如需監視 SQL 受控執行個體耗用量和效能警示的所有可能選項，請參閱 [AZURE sql 受控執行個體監視選項的 blog 文章](https://techcommunity.microsoft.com/t5/azure-sql-database/monitoring-options-available-for-azure-sql-managed-instance/ba-p/1065416)。 如需 SQL MI 的即時效能監視，請參閱 [AZURE SQL DB 受控執行個體的即時效能監視](https://docs.microsoft.com/archive/blogs/sqlcat/real-time-performance-monitoring-for-azure-sql-database-managed-instance)。

**是否可以使用 SQL Profiler 進行效能追蹤？**

是，支援 SQL Profiler 或 SQL 受控執行個體。 如需詳細資訊，請參閱 [SQL Profiler](https://docs.microsoft.com/sql/tools/sql-server-profiler/sql-server-profiler?view=sql-server-ver15&preserve-view=true)。

**受控執行個體資料庫 Database Advisor 和查詢效能深入解析支援嗎？**

否，不支援它們。 您可以使用 [dmv](../database/monitoring-with-dmvs.md) 和 [查詢存放區](https://docs.microsoft.com/sql/relational-databases/performance/monitoring-performance-by-using-the-query-store?view=sql-server-ver15&preserve-view=true) 搭配 [SQL Profiler](https://docs.microsoft.com/sql/tools/sql-server-profiler/sql-server-profiler?view=sql-server-ver15&preserve-view=true) 和 [XEvents](https://docs.microsoft.com/sql/relational-databases/extended-events/extended-events?view=sql-server-ver15&preserve-view=true) 來監視您的資料庫。

**我可以在 SQL 受控執行個體上建立計量警示嗎？**

是。 如需相關指示，請參閱 [建立 SQL 受控執行個體的警示](alerts-create.md)。

**是否可以在受控實例中的資料庫上建立計量警示？**

您無法，警示計量僅適用于受控實例。 無法使用受控實例中個別資料庫的警示計量。

## <a name="storage-size"></a>儲存體大小

**SQL 受控執行個體的最大儲存體大小為何？**

SQL 受控執行個體的儲存體大小取決於所選服務層級 (一般用途或業務關鍵) 。 如需這些服務層的儲存體限制，請參閱 [服務層特性](../database/service-tiers-general-purpose-business-critical.md)。

**受控實例可用的儲存體大小下限為何？**

實例中可用的最小儲存體數量為 32 GB。 儲存體可以增加至最大儲存體大小的 32 GB。 第一個32GB 是免費的。

**是否可以將指派給實例的儲存空間與計算資源分開增加？**

是的，您可以從計算中獨立購買附加元件儲存區。 請參閱[資料表](resource-limits.md#hardware-generation-characteristics)中的*最大實例保留儲存體*。

**如何優化一般用途服務層級的儲存體效能？**

若要將儲存體效能優化，請參閱 [一般用途的儲存體最佳做法](https://techcommunity.microsoft.com)。

## <a name="backup-and-restore"></a>備份與還原

**備份儲存體是否會從我的受控實例儲存體中扣除？**

否，備份儲存體不會從您的受控實例儲存空間中扣除。 備份儲存體與實例儲存空間無關，而且大小不限。 備份儲存體會受限於保留實例資料庫備份的時間週期，最多可設定為35天。 如需詳細資訊，請參閱 [自動備份](../database/automated-backups-overview.md)。

**如何查看我的受控實例上自動備份的時間？**

若要追蹤在受控執行個體上執行自動備份的時間，請參閱 [如何追蹤 AZURE SQL 受控執行個體的自動備份](https://techcommunity.microsoft.com/t5/azure-database-support-blog/lesson-learned-128-how-to-track-the-automated-backup-for-an/ba-p/1442355)。

**是否支援隨選備份？**

是的，您可以在 Azure Blob 儲存體中建立僅限複製的完整備份，但它只能在受控執行個體中還原。 如需詳細資訊，請參閱 [僅複本備份](https://docs.microsoft.com/sql/relational-databases/backup-restore/copy-only-backups-sql-server?view=sql-server-ver15&preserve-view=true)。 但是，如果資料庫因為加密所用的憑證無法存取，而由服務管理的 TDE 加密，就不可能只複本備份。 在這種情況下，請使用時間點還原功能將資料庫移至另一個 SQL 受控執行個體，或切換至客戶管理的金鑰。

**原生還原 (從 .bak 檔案) 至受控執行個體支援嗎？**

是的，SQL Server 2005 + 版本支援和提供。  若要使用原生還原，請將 .bak 檔案上傳至 Azure blob 儲存體，並執行 T-sql 命令。 如需詳細資訊，請參閱 [從 URL 原生還原](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-migrate#native-restore-from-url)。

## <a name="business-continuity"></a>業務持續性

**我的系統資料庫是否已複寫至容錯移轉群組中的次要實例？**

系統資料庫不會複寫至容錯移轉群組中的次要實例。 因此，除非在次要資料庫上以手動方式建立物件，否則不可能在次要實例上相依于系統資料庫中物件的案例。 如需因應措施，請參閱 [從系統資料庫啟用相依于物件的案例](../database/auto-failover-group-overview.md?tabs=azure-powershell#enable-scenarios-dependent-on-objects-from-the-system-databases)。
 
## <a name="networking-requirements"></a>網路需求 

**受控執行個體子網上目前的輸入/輸出 NSG 條件約束為何？**

必要的 NSG 和 UDR 規則記載于 [此處](connectivity-architecture-overview.md#mandatory-inbound-security-rules-with-service-aided-subnet-configuration)，並且由服務自動設定。
請記住，這些規則只是維護服務所需的規則。 若要連線到受控實例並使用不同的功能，您必須設定您需要維護的額外功能特定規則。

**如何設定管理埠的輸入 NSG 規則？**

SQL 受控執行個體負責設定管理埠的規則。 這是透過名為 [服務輔助子網](connectivity-architecture-overview.md#service-aided-subnet-configuration)設定的功能來達成。
這是為了確保管理流量不中斷的流程，以履行 SLA。

**我可以取得用於輸入管理流量的來源 IP 範圍嗎？**

是。 您可以藉由設定網路監看員 [流量記錄](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview#analyze-traffic-to-or-from-a-network-security-group)，來分析通過網路安全性群組的流量。

**我可以設定 NSG 來控制對資料端點的存取 (埠 1433) 嗎？**

是。 布建受控執行個體之後，您可以設定 NSG 來控制埠1433的輸入存取。 建議您盡可能將 IP 範圍縮小。

**是否可以設定 NVA 或內部部署防火牆，以根據 Fqdn 篩選輸出管理流量？**

否。 這種情況不受支援，原因如下：
-   代表回應輸入管理要求的路由傳送流量是非對稱的，且無法運作。
-   路由傳送至儲存體的流量會受到輸送量限制和延遲的影響，如此一來，我們就無法提供預期的服務品質和可用性。
-   根據經驗，這些設定容易出錯，也不支援。

**我可以針對輸出非管理流量設定 NVA 或防火牆嗎？**

是。 達成此目的的最簡單方式是將0/0 規則新增至與受控實例子網相關聯的 UDR，以透過 NVA 路由傳送流量。
 
**受控執行個體需要多少個 IP 位址？**

子網必須有足夠的可用 [IP 位址](connectivity-architecture-overview.md#network-requirements)數目。 若要判斷 SQL 受控執行個體的 VNet 子網大小，請參閱 [判斷受控執行個體所需的子網大小與範圍](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-determine-size-vnet-subnet)。 

**如果沒有足夠的 IP 位址可執行實例更新作業，該怎麼辦？**

如果您的受控實例布建所在的子網中沒有足夠的 [IP 位址](connectivity-architecture-overview.md#network-requirements) ，您就必須在其中建立新的子網和新的受控實例。 此外也建議您建立配置更多 IP 位址的新子網路，以免日後的更新作業又碰到類似情況。 布建新的實例之後，您可以在舊的和新的實例之間手動備份和還原資料，或執行跨實例的 [時間點還原](point-in-time-restore.md?tabs=azure-powershell)。

**需要空白的子網才能建立受控執行個體嗎？**

否。 您可以使用空的子網或已包含受控執行個體 (s) 的子網。 

**我可以變更子網位址範圍嗎？**

如果內有受控實例，則不是。 這是 Azure 網路基礎結構的限制。 您只允許 [將其他位址空間新增至空白子網](https://docs.microsoft.com/azure/virtual-network/virtual-network-manage-subnet#change-subnet-settings)。 

**我可以將受控實例移至另一個子網嗎？**

否。 這是目前的受控執行個體設計限制。 不過，您可以在另一個子網中布建新的實例，並在舊的和新的實例之間手動備份和還原資料，或執行跨實例的 [時間點還原](point-in-time-restore.md?tabs=azure-powershell)。

**是否需要空的虛擬網路來建立受控執行個體？**

這不是必要項。 您可以 [建立 AZURE sql 受控執行個體的虛擬網路](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-create-vnet-subnet) ，或 [設定 azure sql 受控執行個體的現有虛擬網路](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-configure-vnet-subnet)。

**我可以使用子網中的其他服務來放置受控執行個體嗎？**

否。 我們目前不支援將受控執行個體放在已包含其他資源類型的子網中。

## <a name="connectivity"></a>連線能力 

**我可以使用 IP 位址連線到受控實例嗎？**

不行，不支援此方式。 受控執行個體的主機名稱會對應至受控執行個體的虛擬叢集前方的負載平衡器。 因為一個虛擬叢集可以裝載多個受控實例，所以無法將連接路由至適當的受控執行個體，而不需要指定其名稱。
如需 SQL 受控執行個體虛擬叢集架構的詳細資訊，請參閱 [虛擬叢集連線架構](connectivity-architecture-overview.md#virtual-cluster-connectivity-architecture)。

**我的受控實例可以有靜態 IP 位址嗎？**

目前不支援。

在罕見但必要的情況下，我們可能需要線上將受控實例遷移至新的虛擬叢集。 如有需要，這是因為我們的技術堆疊中有變更，目的是要改善服務的安全性和可靠性。 遷移至新的虛擬叢集會導致變更對應至受控實例主機名稱的 IP 位址。 受控實例服務不會宣告靜態 IP 位址支援，並且保留在正常維護週期中不另行通知的情況下變更它的權利。

基於這個理由，我們強烈建議您不要依賴 IP 位址的永久性，因為這可能會導致不必要的停機時間。

**受控執行個體是否有公用端點？**

是。 受控執行個體具有僅用於服務管理的公用端點，但客戶也可將其啟用以進行資料存取。 如需詳細資訊，請參閱搭配 [使用 SQL 受控執行個體與公用端點](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-public-endpoint-securely)。 若要設定公用端點，請移至 [在 SQL 受控執行個體中設定公用端點](public-endpoint-configure.md)。

**受控執行個體如何控制對公用端點的存取？**

受控執行個體可控制在網路和應用層級的公用端點存取權。

管理和部署服務會使用對應到外部負載平衡器的 [管理端點](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-connectivity-architecture#management-endpoint) 來連線到受控實例。 只有在一組預先定義的埠（僅限受控實例的管理元件使用）接收到節點時，流量才會路由傳送至節點。 節點上的內建防火牆設定為只允許來自 Microsoft IP 範圍的流量。 憑證會相互驗證管理元件與管理平面之間的所有通訊。 如需詳細資訊，請參閱 [SQL 受控執行個體的連接架構](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-connectivity-architecture#virtual-cluster-connectivity-architecture)。

**我可以使用公用端點來存取受控執行個體資料庫中的資料嗎？**

是。 客戶必須啟用[Azure 入口網站](public-endpoint-configure.md#enabling-public-endpoint-for-a-managed-instance-in-the-azure-portal)  /  [PowerShell](public-endpoint-configure.md#enabling-public-endpoint-for-a-managed-instance-using-powershell) /ARM 的公用端點資料存取，並將 NSG 設定為鎖定資料埠的存取， (埠號碼 3342) 。 如需詳細資訊，請參閱 [在 AZURE sql 受控執行個體中設定公用端點](public-endpoint-configure.md) ，並 [使用公用端點安全地使用 Azure sql 受控執行個體](public-endpoint-overview.md)。 

**我可以為 SQL 資料端點 () 指定自訂埠嗎？**

否，無法使用此選項。  針對私用資料端點，受控執行個體使用預設通訊埠編號1433和公用資料端點，受控執行個體使用預設的埠號碼3342。

**將受管理的實例連接到位於不同區域的建議方式為何？**

Express Route 線路對等互連是最好的做法。 支援全域虛擬網路對等互連，但有下列注意事項中所述的限制。  

> [!IMPORTANT]
> [在9/22/2020 上，我們為新建立的虛擬叢集宣佈了全域虛擬網路對等互連](https://azure.microsoft.com/en-us/updates/global-virtual-network-peering-support-for-azure-sql-managed-instance-now-available/)。 這表示在公告日期之後于空白子網中建立的 SQL 受控實例，以及這些子網中建立的所有後續受控實例，都支援全域虛擬網路對等互連。 針對所有其他 SQL 受控實例對等互連支援，受限於相同區域中的網路，原因是 [全域虛擬網路對等互連的限制](../../virtual-network/virtual-network-manage-peering.md#requirements-and-constraints)。 另請參閱 [Azure 虛擬網路常見問題](https://docs.microsoft.com/azure/virtual-network/virtual-networks-faq#what-are-the-constraints-related-to-global-vnet-peering-and-load-balancers) 文章中的相關章節，以取得詳細資料。 

如果不可能有 Express Route 線路對等互連和全域虛擬網路對等互連，唯一的選項是建立站對站 VPN 連線 ([Azure 入口網站](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal)、 [PowerShell](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell) [Azure CLI](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-cli)) 。

## <a name="mitigate-data-exfiltration-risks"></a>緩和資料遭到外泄風險  

**如何緩和資料遭到外泄風險？**

若要減輕任何資料遭到外泄風險，建議客戶套用一組安全性設定和控制項：

- 開啟所有資料庫上的 [透明資料加密 (TDE) ](https://docs.microsoft.com/azure/sql-database/transparent-data-encryption-azure-sql) 。
- 關閉 Common Language Runtime (CLR) 。 這也是內部部署的建議做法。
- 使用 Azure Active Directory (Azure AD) 驗證。
- 使用低許可權 DBA 帳戶存取實例。
- 設定系統管理員（sysadmin）帳戶的 JIT jumpbox 存取權。
- 開啟 [SQL 審核](https://docs.microsoft.com/sql/relational-databases/security/auditing/sql-server-audit-database-engine)，並將其與警示機制整合。
- 從[Azure Defender FOR SQL](https://docs.microsoft.com/azure/azure-sql/database/azure-defender-for-sql) Suite 開啟[威脅偵測](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection)。

## <a name="dns"></a>DNS

**我可以為 SQL 受控執行個體設定自訂 DNS 嗎？**

是。 請參閱 [如何設定 AZURE SQL 受控執行個體的自訂 DNS](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-custom-dns)。

**我可以進行 DNS 重新整理嗎？**

目前，我們並未提供重新整理 SQL 受控執行個體 DNS 伺服器設定的功能。

最後會重新整理 DNS 設定：

- 當 DHCP 租用到期時。
- 在平臺升級。

因應措施是將 SQL 受控執行個體降級為4虛擬核心，然後在之後再次進行升級。 這會產生重新整理 DNS 設定的副作用。

## <a name="change-time-zone"></a>變更時區

**我可以變更現有受控實例的時區嗎？**

您可以在第一次布建受控實例時設定時區設定。 不支援變更現有受控實例的時區。 如需詳細資訊，請參閱 [時區限制](timezones-overview.md#limitations)。

因應措施包括以適當的時區建立新的受控實例，然後執行手動備份和還原，或建議執行 [跨實例的時間點還原](https://blogs.msdn.microsoft.com/sqlserverstorageengine/2018/06/07/cross-instance-point-in-time-restore-in-azure-sql-database-managed-instance/)。


## <a name="security-and-database-encryption"></a>安全性和資料庫加密

**SQL 受控執行個體是否有系統管理員（sysadmin）伺服器角色？**

是，客戶可以建立屬於系統管理員（sysadmin）角色成員的登入。  採用系統管理員（sysadmin）許可權的客戶也會擔任操作實例的責任，這可能會對 SLA 承諾造成負面影響。 若要將登入加入至系統管理員（sysadmin）伺服器角色，請參閱 [Azure AD authentication](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-aad-security-tutorial#azure-ad-authentication)。

**SQL 受控執行個體支援透明資料加密嗎？**

是，SQL 受控執行個體支援透明資料加密。 如需詳細資訊，請參閱 [SQL 受控執行個體的透明資料加密](https://docs.microsoft.com/azure/sql-database/transparent-data-encryption-azure-sql?tabs=azure-portal)。

**我可以利用「攜帶您自己的金鑰」模型來進行 TDE 嗎？**

是，適用于 BYOK 的 Azure Key Vault 案例適用于 Azure SQL 受控執行個體。 如需詳細資訊，請參閱 [透明資料加密與客戶管理的金鑰](https://docs.microsoft.com/azure/sql-database/transparent-data-encryption-azure-sql?view=sql-server-ver15&tabs=azure-portal#customer-managed-transparent-data-encryption---bring-your-own-key&preserve-view=true)。

**我可以遷移加密的 SQL Server 資料庫嗎？**

是，您可以。 若要遷移加密的 SQL Server 資料庫，您必須將現有的憑證匯出並匯入受控執行個體，然後取得完整的資料庫備份，然後在受控執行個體中加以還原。 

您也可以使用 [Azure 資料庫移轉服務](https://azure.microsoft.com/services/database-migration/) 來遷移 TDE 的加密資料庫。

**如何為 SQL 受控執行個體設定 TDE 保護裝置旋轉？**

您可以使用 Azure Cloud Shell 來輪替受控執行個體的 TDE 保護裝置。 如需相關指示，請參閱 [SQL 受控執行個體中的透明資料加密從 Azure Key Vault 使用您自己的金鑰](scripts/transparent-data-encryption-byok-powershell.md)。

**我可以將加密的資料庫還原到 SQL 受控執行個體嗎？**

是，您不需要將資料庫解密以將其還原至 SQL 受控執行個體。 您必須提供憑證/金鑰，作為來源系統上的加密金鑰保護裝置，以供 SQL 受控執行個體，以便從加密的備份檔案讀取資料。 要執行此動作有兩個可行的方式：

- *將憑證保護裝置上傳至 SQL 受控執行個體*。 您只能使用 PowerShell 來完成此操作。 [範例腳本](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-migrate-tde-certificate)會描述整個進程。
- 將*非對稱金鑰保護裝置上傳至 Azure Key Vault，並將 SQL 受控執行個體指向其中*。 這種方法類似「攜帶您自己的金鑰」 (BYOK) TDE 使用案例，也會使用 Key Vault 整合來儲存加密金鑰。 如果您不想要使用金鑰做為加密金鑰保護裝置，而且只想要讓 SQL 受控執行個體可將加密的資料庫還原 () ，請遵循 [設定 BYOK TDE](https://docs.microsoft.com/azure/sql-database/transparent-data-encryption-azure-sql#manage-transparent-data-encryption)的指示，而不要勾選核取方塊將 **選取的金鑰設為預設 TDE 保護**裝置。

一旦您將加密保護裝置提供給 SQL 受控執行個體，就可以繼續進行標準的資料庫還原程式。

## <a name="purchasing-models-and-benefits"></a>購買模型和權益

**哪些購買模型適用于 SQL 受控執行個體？**

SQL 受控執行個體提供以 [vCore 為基礎的購買模型](sql-managed-instance-paas-overview.md#vcore-based-purchasing-model)。

**SQL 受控執行個體有哪些成本效益？**

您可以透過下列方式，利用 Azure SQL 權益來節省成本：
-   將內部部署授權的現有投資最大化，並透過 [Azure Hybrid Benefit](https://docs.microsoft.com/azure/azure-sql/azure-hybrid-benefit?tabs=azure-powershell)省下最多55% 的費用。 
-   認可至計算資源的保留，並省下高達33% 的 [保留實例權益](https://docs.microsoft.com/azure/sql-database/sql-database-reserved-capacity)。 結合此項與 Azure 混合式權益，可節省高達82% 的費用。 
-   利用 [Azure 開發/測試定價權益](https://azure.microsoft.com/pricing/dev-test/) ，為持續進行的開發和測試工作負載提供折扣費率，最多可省下55% 的費用與標價。

**誰有資格獲得保留實例權益？**

若要符合保留實例權益的資格，您的訂用帳戶類型必須是 enterprise 合約 (供應專案號碼： MS-AZR-0003P->ms-azr-0017p 或 MS-AZR-0003P-Ms-azr-0148p) 或具有隨用隨付定價的個別合約 (供應專案號碼： MS-MS-AZR-0003P-Ms-azr-0003p 或 MS-MS-AZR-0003P-Ms-azr-0023p) 。 如需有關保留的詳細資訊，請參閱 [保留實例權益](https://docs.microsoft.com/azure/sql-database/sql-database-reserved-capacity)。 

**可以取消、交換或退款保留嗎？**

您可以取消、交換或退款保留，但有某些限制。 如需詳細資訊，請參閱 [Azure 保留的自助式交換和退費](https://docs.microsoft.com/azure/cost-management-billing/reservations/exchange-and-refund-azure-reservations)。

## <a name="billing-for-managed-instance-and-backup-storage"></a>受控執行個體和備份儲存體的計費

**SQL 受控執行個體定價選項有哪些？**

若要探索受控執行個體定價選項，請參閱 [定價頁面](https://azure.microsoft.com/pricing/details/azure-sql/sql-managed-instance/single/)。

**如何追蹤受控實例的計費成本？**

您可以使用 [Azure 成本管理解決方案](https://docs.microsoft.com/azure/cost-management-billing/)來這麼做。 流覽至[Azure 入口網站](https://portal.azure.com)中的 [訂用帳戶 **]，然後**選取 [**成本分析**]。 

使用 [ **累積成本** ] 選項，然後依 **資源類型** 篩選為 `microsoft.sql/managedinstances` 。

**自動備份需要多少費用？**

無論備份保留期限設定為何，您都可以取得相同數量的可用備份儲存空間作為所購買的保留資料儲存空間。 如果您的備份儲存體耗用量是在配置的免費備份儲存空間內，則受控實例上的自動備份將不會有額外的費用，因此將免費。 超過可用空間以上的備份儲存體時，將會產生大約每 GB 每月 $0.20-$0.24 的成本，或參閱定價頁面以取得您區域的詳細資料。 如需詳細資訊，請參閱 [說明的備份儲存體耗用量](https://techcommunity.microsoft.com/t5/azure-sql-database/backup-storage-consumption-on-managed-instance-explained/ba-p/1390923)。

**如何監視備份儲存體耗用量的計費成本？**

您可以透過 Azure 入口網站監視備份儲存體的成本。 如需相關指示，請參閱 [監視自動備份的成本](https://docs.microsoft.com/azure/azure-sql/database/automated-backups-overview?tabs=managed-instance#monitor-costs)。 

**如何將受控實例上的備份儲存體成本優化？**

若要將您的備份儲存體成本優化，請參閱 [SQL 受控執行個體的微調備份](https://techcommunity.microsoft.com/t5/azure-sql-database/fine-tuning-backup-storage-costs-on-managed-instance/ba-p/1390935)微調。

## <a name="cost-saving-use-cases"></a>節省成本的使用案例

**哪裡可以找到使用案例，並利用 SQL 受控執行個體節省成本？**

SQL 受控執行個體案例研究：

- [小松](https://customers.microsoft.com/story/komatsu-australia-manufacturing-azure)
- [KMD](https://customers.microsoft.com/en-ca/story/kmd-professional-services-azure-sql-database)
- [PowerDETAILS](https://customers.microsoft.com/story/powerdetails-partner-professional-services-azure-sql-database-managed-instance)
- [Allscripts](https://customers.microsoft.com/story/allscripts-partner-professional-services-azure)

若要深入瞭解部署 Azure SQL 受控執行個體的相關優點、成本和風險，還有一項 Forrester 研究： [Microsoft Azure SQL Database 受控執行個體的整體經濟影響](https://azure.microsoft.com/resources/forrester-tei-sql-database-managed-instance)。

## <a name="password-policy"></a>密碼原則 

**適用于 SQL 受控執行個體 SQL 登入的密碼原則為何？**

Sql 登入的 SQL 受控執行個體密碼原則會繼承 Azure 平臺原則，這些原則會套用至構成受控實例之虛擬叢集的 Vm。 目前無法變更這些設定中的任何一項，因為這些設定是由 Azure 所定義，且受受控實例繼承。

 > [!IMPORTANT]
 > Azure 平臺可以變更原則需求，而不會通知服務依賴該原則。

**什麼是目前的 Azure 平臺原則？**

每個登入都必須在登入時設定其密碼，並在達到最大存留期之後變更其密碼。

| **原則** | **安全性設定** |
| --- | --- |
| 密碼最長使用期限 | 42天 |
| 密碼最短使用期限 | 1 日 |
| 密碼長度下限 | 10個字元 |
| 密碼必須符合複雜性需求 | 啟用 |

**是否可以在登入層級上停用 SQL 受控執行個體中的密碼複雜性和到期日？**

是的，您可以控制登入層級的 CHECK_POLICY 和 CHECK_EXPIRATION 欄位。 您可以執行下列 T-sql 命令來檢查目前的設定：

```sql
SELECT *
FROM sys.sql_logins
```

之後，您可以藉由執行下列動作來修改指定的登入設定：

```sql
ALTER LOGIN <login_name> WITH CHECK_POLICY = OFF;
ALTER LOGIN <login_name> WITH CHECK_EXPIRATION = OFF;
```

 (以所需的登入名稱取代 ' test '，並調整原則和到期值) 


## <a name="service-updates"></a>服務更新

**Azure SQL Database & SQL 受控執行個體的根 CA 變更為何？**

請參閱 [Azure SQL Database & SQL 受控執行個體的憑證輪替](https://docs.microsoft.com/azure/azure-sql/updates/ssl-root-certificate-expiring)。 

**什麼是 SQL 受控執行個體的規劃維護事件？**

請參閱 [SQL 受控執行個體中的規劃 Azure 維護事件](https://docs.microsoft.com/azure/azure-sql/database/planned-maintenance)。 


## <a name="azure-feedback-and-support"></a>Azure 意見反應和支援

**我可以將我的概念用於 SQL 受控執行個體改進？**

您可以針對新的受控執行個體功能投票，或針對 [SQL 受控執行個體意見反應論壇](https://feedback.azure.com/forums/915676-sql-managed-instance)上的投票提出新的改進想法。 如此一來，您就可以參與產品開發，並協助我們設定潛在改進的優先順序。

**如何建立 Azure 支援要求？**

若要瞭解如何建立 Azure 支援要求，請參閱 [如何建立 Azure 支援要求](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request)。

