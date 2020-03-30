---
title: 受控執行個體常見問題集
description: SQL 資料庫託管實例常見問題 （FAQ）
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
ms.openlocfilehash: 393d67b200a4f8d44cb001b3a7e2e491209e9d58
ms.sourcegitcommit: 07d62796de0d1f9c0fa14bfcc425f852fdb08fb1
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "80364164"
---
# <a name="sql-database-managed-instance-frequently-asked-questions-faq"></a>SQL 資料庫託管實例常見問題 （FAQ）

本文包含有關[SQL 資料庫託管實例](sql-database-managed-instance.md)的許多常見問題。

## <a name="supported-features"></a>支援的功能

**在哪裡可以找到託管實例上支援的功能清單？**

有關託管實例中受支援的功能的清單，請參閱[Azure SQL 資料庫與 SQL Server](sql-database-features.md)。

有關 Azure SQL 資料庫託管實例和本地 SQL Server 之間的語法和行為差異，請參閱[SQL Server 中的 T-SQL 差異](sql-database-managed-instance-transact-sql-information.md)。


## <a name="tech-spec--resource-limits"></a>技術規格&資源限制
 
**在哪裡可以找到託管實例的技術特徵和資源限制？**

有關可用的硬體生成特性，請參閱[硬體代中的技術差異](sql-database-managed-instance-resource-limits.md#hardware-generation-characteristics)。
有關可用的服務層及其特徵，請參閱[服務層之間的技術差異](sql-database-managed-instance-resource-limits.md#service-tier-characteristics)。

## <a name="known-issues--bugs"></a>已知問題& bug

**我在哪裡可以找到已知的問題和錯誤？**

有關 Bug 和已知問題，請參閱[已知問題](sql-database-release-notes.md#known-issues)。

## <a name="new-features"></a>新功能

**在公開預覽中在哪裡可以找到最新的功能和功能？**

有關新功能和預覽功能，請參閱[版本資訊](sql-database-release-notes.md?tabs=managed-instance)。

## <a name="deployment-times"></a>部署時間 

**創建或更新實例或還原資料庫所需的時間？**

創建新託管實例或更改服務層（vCore、存儲）的預期時間取決於幾個因素。 查看[管理操作](/azure/sql-database/sql-database-managed-instance#managed-instance-management-operations) 

## <a name="naming-convention"></a>命名慣例

**託管實例的名稱與本地 SQL Server 的名稱相同嗎？**

不支援更改託管實例名稱。

可以更改託管實例預設 DNS 區域 *.database.windows.net。* 

要使用另一個 DNS 區域而不是預設值，例如 *.contoso.com*： 
- 使用 CliConfig 定義別名。 該工具只是一個註冊表設置包裝器，因此也可以使用群組原則或腳本來完成。
- 將*CNAME*與*信任伺服器憑證_true*選項一起使用。

## <a name="move-db-from-mi"></a>從 MI 移動資料庫 

**如何將資料庫從託管實例移回 SQL 伺服器或 Azure SQL 資料庫？**

您可以將[資料庫匯出到 BACPAC，](sql-database-export.md)然後[導入 BACPAC 檔]( sql-database-import.md)。 如果資料庫小於 100 GB，則建議使用此方法。

如果資料庫中的所有表都有主鍵，則可以使用異動複寫。

無法`COPY_ONLY`將從託管實例獲取的本機備份還原到 SQL Server，因為與 SQL Server 相比，託管實例的資料庫版本更高。

## <a name="migrate-instance-db"></a>遷移實例資料庫

**如何將實例資料庫移轉到單個 Azure SQL 資料庫？**

一個選項是[將資料庫匯出到 BACPAC，](sql-database-export.md)然後[導入 BACPAC 檔](sql-database-import.md)。 

如果資料庫小於 100 GB，則建議使用此方法。 如果資料庫中的所有表都有主鍵，則可以使用異動複寫。

## <a name="switch-hardware-generation"></a>交換器硬體生成 

**我可以線上在 Gen 4 和 5 代之間切換託管實例硬體生成嗎？**

如果兩個硬體生成在託管實例預配的區域都可用，則硬體生成之間可以自動線上切換。 在這種情況下，您可以檢查[vCore 模型概述頁面](sql-database-service-tiers-vcore.md)，解釋如何在硬體生成之間切換。

這是一個長時間運行的操作，因為新的託管實例將在後臺預配，資料庫會在新舊實例之間自動傳輸，並在進程結束時快速容錯移轉。 

如果同一區域不支援兩個硬體生成，則更改硬體生成是可能的，但必須手動完成。 這要求您在需要生成所需的硬體的區域預配新實例，並在新舊實例之間手動備份和還原資料。


## <a name="tune-performance"></a>微調效能

**如何調整託管實例的性能？**

通用託管實例使用遠端存放，因為資料和日誌檔的大小對性能很重要。 有關詳細資訊，請參閱[日誌檔案大小對通用託管實例性能的影響](https://medium.com/azure-sqldb-managed-instance/impact-of-log-file-size-on-general-purpose-managed-instance-performance-21ad170c823e)。

如果工作負荷包含大量小事務，請考慮將連線類型從代理切換到重定向模式。

## <a name="maximum-storage-size"></a>儲存體大小上限

**託管實例的最大存儲大小是多少？**

託管實例的存儲大小取決於所選服務層（通用或業務關鍵）。 有關這些服務層的存儲限制，請參閱[服務層特徵](sql-database-service-tiers-general-purpose-business-critical.md)。

## <a name="back-up-storage-cost"></a>備份存儲成本 

**備份存儲是否從我的託管實例存儲中扣除？**

否，不會從託管實例存儲空間中扣除備份存儲。 備份存儲獨立于實例存儲空間，並且大小不受限制。 備份存儲受保留實例資料庫備份的時間段的限制，該備份可配置 7 到 35 天。 有關詳細資訊，請參閱[自動備份](https://docs.microsoft.com/azure/sql-database/sql-database-automated-backups)。

## <a name="track-billing"></a>跟蹤計費

**是否有方法跟蹤託管實例的計費成本？**

可以使用[Azure 成本管理解決方案](/azure/cost-management/)執行此操作。 導航到 Azure[門戶](https://portal.azure.com)中的**訂閱**，然後選擇**成本分析**。 

使用 **"累積成本"** 選項，然後按**資源類型**篩選為`microsoft.sql/managedinstances`。 
  
## <a name="inbound-nsg-rules"></a>入站 NSG 規則

**如何在管理埠上設置入站 NSG 規則？**

託管實例控制平面維護保護管理埠的 NSG 規則。

以下是管理埠用於：

埠 9000 和 9003 由 Service Fabric 基礎結構使用。 Service Fabric 的主要角色是保持虛擬叢集正常運行，並在元件副本數量方面保持目標狀態。

埠 1438、1440 和 1452 由節點代理使用。 節點代理是在群集內運行的應用程式，由控制平面用於執行管理命令。

除了 NSG 規則之外，內置防火牆還保護網路層上的實例。 在應用程式層上，通信受證書保護。
  
有關詳細資訊以及如何驗證內置防火牆，請參閱[Azure SQL 資料庫託管實例內置防火牆](sql-database-managed-instance-management-endpoint-verify-built-in-firewall.md)。


## <a name="mitigate-data-exfiltration-risks"></a>降低資料滲漏風險  

**如何降低資料滲漏風險？**

為了降低任何資料洩露風險，建議客戶應用一組安全設置和控制：

- 在所有資料庫上打開[透明資料加密 （TDE）。](https://docs.microsoft.com/azure/sql-database/transparent-data-encryption-azure-sql)
- 關閉通用語言運行時 （CLR）。 建議在本地這樣做。
- 僅使用 Azure 活動目錄 （AAD） 身份驗證。
- 具有低特權 DBA 帳戶的訪問實例。
- 為系統管理員帳戶配置 JiT 跳轉盒訪問。
- 打開[SQL 審核](https://docs.microsoft.com/sql/relational-databases/security/auditing/sql-server-audit-database-engine)，並將其與警報機制集成。
- 打開[高級資料安全 （ADS）](https://docs.microsoft.com/azure/sql-database/sql-database-advanced-data-security)套件[中的威脅檢測](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection)。


## <a name="cost-saving-use-cases"></a>節省成本的用例

**在哪裡可以找到用例和託管實例的成本節約？**

託管實例案例研究：

- [小松](https://customers.microsoft.com/story/komatsu-australia-manufacturing-azure)
- [KMD](https://customers.microsoft.com/en-ca/story/kmd-professional-services-azure-sql-database)
- [電源詳細資訊](https://customers.microsoft.com/story/powerdetails-partner-professional-services-azure-sql-database-managed-instance)
- [所有腳本](https://customers.microsoft.com/story/allscripts-partner-professional-services-azure)  
為了更好地瞭解與部署 Azure SQL 資料庫託管實例相關的收益、成本和風險，還有一個 Forrester 的研究[：MI 的總體經濟影響](https://azure.microsoft.com/resources/forrester-tei-sql-database-managed-instance)。


## <a name="dns-refresh"></a>DNS 刷新 

**我可以進行 DNS 刷新嗎？**

目前，我們不提供更新託管實例的 DNS 伺服器配置的功能。

最終刷新 DNS 配置：

- 當 DHCP 租約到期時。
- 在平臺升級。

作為解決方法，將託管實例降級為 4 vCore，然後再次升級。 這具有刷新 DNS 配置的副作用。


## <a name="ip-address"></a>IP 位址

**我能否使用 IP 位址連接到託管實例？**

不支援使用 IP 位址連接到託管實例。 託管實例主機名稱映射到託管實例虛擬叢集前面的負載等化器。 由於一個虛擬叢集可以承載多個託管實例，因此如果不指定其名稱，就無法將連接路由到適當的託管實例。

有關託管實例虛擬叢集體系結構的詳細資訊，請參閱[虛擬叢集連接體系結構](sql-database-managed-instance-connectivity-architecture.md#virtual-cluster-connectivity-architecture)。

**託管實例是否可以具有靜態 IP 位址？**

在極少數但必要的情況下，我們可能需要對託管實例進行連線遷移以遷移到新的虛擬叢集。 如果需要，此遷移是由於我們的技術堆疊進行了更改，旨在提高服務的安全性和可靠性。 遷移到新的虛擬叢集會導致更改映射到託管實例主機名稱的 IP 位址。 託管實例服務不聲明靜態 IP 位址支援，並保留更改它的權利，恕不另行通知，作為常規維護週期的一部分。

因此，我們強烈建議不要依賴 IP 位址的不變性，因為它可能會導致不必要的停機時間。

## <a name="change-time-zone"></a>更改時區

**是否可以更改現有託管實例的時區？**

首次預配託管實例時，可以設置時區配置。 不支援更改現有託管實例的時區。 有關詳細資訊，請參閱[時區限制](sql-database-managed-instance-timezone.md#limitations)。

解決方法包括使用適當的時區創建新的託管實例，然後執行手動備份和還原，或者我們的建議執行[跨實例點還原](https://blogs.msdn.microsoft.com/sqlserverstorageengine/2018/06/07/cross-instance-point-in-time-restore-in-azure-sql-database-managed-instance/)。


## <a name="resolve-performance-issues"></a>解決性能問題

**如何解決託管實例的性能問題？**

對於託管實例和 SQL Server 之間的性能比較，一個很好的起點是[Azure SQL 託管實例和 SQL Server 文章之間性能比較的最佳做法](https://techcommunity.microsoft.com/t5/Azure-SQL-Database/The-best-practices-for-performance-comparison-between-Azure-SQL/ba-p/683210)。

由於強制完全恢復模型和事務日誌寫入輸送量[的限制](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-resource-limits#service-tier-characteristics)，託管實例上的資料載入速度通常比 SQL Server 慢。 有時，可以通過將瞬態資料載入到 tempdb 而不是使用者資料庫，或使用群集列存儲或記憶體優化表來解決此問題。


## <a name="restore-encrypted-backup"></a>恢復加密備份

**我可以將加密資料庫還原到託管實例嗎？**

是的，您不需要解密資料庫就能將其還原到託管實例。 您確實需要向託管實例提供在源系統中用作加密金鑰保護器的證書/金鑰，以便能夠從加密的備份檔案讀取資料。 要執行此動作有兩個可行的方式：

- *將證書保護器上載到託管實例*。 只能使用 PowerShell 完成此操作。 [示例腳本](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-migrate-tde-certificate)描述了整個過程。
- *將非對稱金鑰保護器上載到 Azure 金鑰保存庫 （AKV） 並將託管實例指向它*。 此方法類似于自帶金鑰 （BYOK） TDE 用例，該用例還使用 AKV 集成來存儲加密金鑰。 如果您不想將金鑰用作加密金鑰保護裝置，並且只想使該金鑰可用於託管實例以還原加密資料庫，請按照[設置 BYOK TDE](https://docs.microsoft.com/azure/sql-database/transparent-data-encryption-azure-sql#manage-transparent-data-encryption)的說明操作，不要選中核取方塊 *，使所選金鑰成為預設 TDE 保護器*。

使加密保護程式可用於託管實例後，可以繼續執行標準資料庫還原過程。

## <a name="migrate-from-single-db"></a>從單個資料庫移轉 

**如何從 Azure SQL 資料庫單個或彈性池遷移到託管實例？**

託管實例提供與 Azure SQL 資料庫的其他部署選項相同的每個計算和存儲大小的性能級別。 如果要合併單個實例上的資料，或者只需在託管實例中獨佔支援的功能，則可以使用匯出/導入 （BACPAC） 功能遷移資料。
