---
title: 實例集區（預覽）
description: 本文說明 Azure SQL Database 實例集區（預覽）。
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: bonova
ms.author: bonova
ms.reviewer: sstein, carlrab
ms.date: 09/05/2019
ms.openlocfilehash: 0938fbe94cb0d1e6dae3dcb84950a11f90dd9db8
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "80878148"
---
# <a name="what-are-sql-database-instance-pools-preview"></a>什麼是 SQL Database 實例集區（預覽）？

實例集區是 Azure SQL Database 中的新資源，可提供方便且符合成本效益的方式，將較小的 SQL 實例大規模地遷移至雲端。

執行個體集區可讓您根據您的總移轉需求預先佈建計算資源。 然後，您可以部署數個個別的受控執行個體以達到您預先佈建的計算層級。 例如，如果您預先布建8虛擬核心，您可以部署兩個 2 vCore 和一個4個 vCore 實例，然後將資料庫移轉到這些實例。 在實例集區可用之前，較小且較少的計算密集型工作負載通常必須在遷移至雲端時，合併至較大的受控實例。 需要將資料庫群組遷移至大型實例，通常需要謹慎的容量規劃和資源管理、其他安全性考慮，以及在實例層級進行一些額外的資料匯總。

此外，實例集區支援原生 VNet 整合，因此您可以在相同的子網中部署多個實例集區和多個單一實例。


## <a name="key-capabilities-of-instance-pools"></a>實例集區的主要功能

實例集區提供下列優點：

1. 能夠裝載2個 vCore 實例。 *僅適用于實例集區中的實例。 \* *
2. 可預測且快速的實例部署時間（最長5分鐘）。
3. 最低 IP 位址配置。

下圖說明在虛擬網路子網內部署多個實例的實例集區。

![具有多個實例的實例集區](./media/sql-database-instance-pools/instance-pools1.png)

實例集區可讓您在相同的虛擬機器上部署多個實例，其中虛擬機器的計算大小是以配置給集區的虛擬核心總數為基礎。 此架構允許將虛擬機器*分割*成多個實例，可以是任何支援的大小，包括2個虛擬核心（2個 vCore 實例僅適用于集區中的實例）。

集區中實例上的管理作業，會在一開始部署集區時更快。 這些作業的速度較快，因為部署或擴充[虛擬](sql-database-managed-instance-connectivity-architecture.md#high-level-connectivity-architecture)叢集（一組專用的虛擬機器）不是布建受控實例的一部分。

由於集區中的所有實例都共用相同的虛擬機器，因此，總 IP 配置不會取決於部署的實例數目，這在具有較窄 IP 範圍的子網中部署時相當方便。

每個集區只有九個 IP 位址的固定 IP 配置（不包括子網中保留給其本身需求的五個 IP 位址）。 如需詳細資訊，請參閱[單一實例的子網大小需求](sql-database-managed-instance-determine-size-vnet-subnet.md)。

## <a name="application-scenarios-for-instance-pools"></a>實例集區的應用程式案例

下列清單提供應考慮實例集區的主要使用案例：

- 同時遷移*一組 SQL 實例*，其中多數是較小的大小（例如2或4虛擬核心）。
- *可預測和簡短實例建立或調整*的案例很重要。 例如，在需要實例層級功能的多租使用者 SaaS 應用程式環境中部署新的租使用者。
- 具有*固定成本*或*消費限制*的案例很重要。 例如，執行固定（或不常變更）大小的共用開發/測試或示範環境，您可以在需要時定期部署受控實例。
- VNet 子網中的*最低 IP 位址配置*很重要的案例。 集區中的所有實例都是共用虛擬機器，因此，配置的 IP 位址數目會低於單一實例的大小寫。


## <a name="architecture-of-instance-pools"></a>實例集區的架構

實例集區具有與一般受控實例（*單一實例*）類似的架構。 為了支援 [Azure 虛擬網路（vnet）](../virtual-network/virtual-network-for-azure-services.md) 內的部署，以及為客戶提供隔離和安全性，實例集區也會依賴 [虛擬叢集](sql-database-managed-instance-connectivity-architecture.md#high-level-connectivity-architecture)。 虛擬叢集代表一組在客戶的虛擬網路子網內部署的專屬獨立虛擬機器。

這兩種部署模型的主要差異在於，實例集區允許相同虛擬機器節點上的多個 SQL Server 程式部署，也就是使用[Windows 工作物件](https://docs.microsoft.com/windows/desktop/ProcThread/job-objects)管理的資源，而單一實例一律單獨在虛擬機器節點上。

下圖顯示實例集區和兩個部署在相同子網中的個別實例，並說明這兩種部署模型的主要架構詳細資料：

![實例集區和兩個個別的實例](./media/sql-database-instance-pools/instance-pools2.png)

每個實例集區會在底下建立個別的虛擬叢集。 集區中的實例和在相同子網中部署的單一實例，不會共用配置給 SQL Server 處理常式和閘道元件的計算資源，這確保可預測效能。

## <a name="instance-pools-resource-limitations"></a>實例集區資源限制

執行個體集區和集區內的執行個體有幾個相關資源限制：

- 實例集區僅適用于第5代硬體。
- 集區中的實例具有專用的 CPU 和 RAM，因此在所有實例上的虛擬核心匯總數目必須小於或等於配置給集區的虛擬核心數目。
- 所有[實例層級限制](sql-database-managed-instance-resource-limits.md#service-tier-characteristics)適用于在集區內建立的實例。
- 除了實例層級限制，在*實例集區層級*也會有兩個限制：
  - 每個集區的儲存體大小總計（8 TB）。
  - 每個集區的資料庫總數（100）。

所有實例的總儲存空間配置和資料庫數目，必須小於或等於實例集區所公開的限制。

- 實例集區支援8、16、24、32、40、64和80虛擬核心。
- 集區中的受控實例支援2、4、8、16、24、32、40、64和80虛擬核心。
- 集區中的受控實例支援介於 32 GB 和 8 TB 之間的儲存體大小，但除外：
  - 2 vCore 實例支援 32 GB 和 640 GB 之間的大小
  - 4 vCore 實例支援 32 GB 到 2 TB 之間的大小

[服務層屬性](sql-database-managed-instance-resource-limits.md#service-tier-characteristics)與實例集區資源相關聯，因此集區中的所有實例都必須與集區服務層相同的服務層級。 目前只有一般用途服務層級可供使用（請參閱下一節中有關目前預覽的限制）。

### <a name="public-preview-limitations"></a>公開預覽限制

公開預覽具有下列限制：

- 目前只有一般用途的服務層級可供使用。
- 在公開預覽期間，無法調整實例集區，因此請務必仔細規劃部署後的容量。
- 尚未提供實例集區建立和設定的 Azure 入口網站支援。 只有透過 PowerShell 才支援實例集區上的所有作業。 預先建立的集區中的初始實例部署也僅透過 PowerShell 支援。 一旦部署到集區，就可以使用 Azure 入口網站來更新受控實例。
- 在集區外部建立的受控實例無法移至現有的集區，而且在集區內建立的實例無法做為單一實例或另一個集區移到外部。
- 保留實例定價（包含 Azure Hybrid Benefit 的授權）無法使用。

## <a name="sql-features-supported"></a>SQL 功能支援

在集區中建立的實例，支援[單一受控實例中支援的相同相容性層級和功能](sql-database-managed-instance.md#sql-features-supported)。

部署在集區中的每個受控實例都有個別的 SQL 代理程式實例。

選擇性功能或需要您選擇特定值的功能（例如實例層級定序、時區、資料流量的公用端點、容錯移轉群組）是在實例層級設定，而且在集區中的每個實例都可能不同。

## <a name="performance-considerations"></a>效能考量

雖然集區中的受控實例有專用的 vCore 和 RAM，但它們會共用本機磁片（適用于 tempdb 使用量）和網路資源。 不可能，但如果集區中有多個實例同時具有高資源耗用量，則可能會遇到*雜訊的鄰近*效果。 如果您發現此行為，請考慮將這些實例部署到較大的集區或單一實例。

## <a name="security-considerations"></a>安全性考量

由於部署在集區中的實例會共用相同的虛擬機器，因此您可能會想要考慮停用造成較高安全性風險的功能，或嚴格地控制這些功能的存取權限。 例如，CLR 整合、原生備份與還原、資料庫電子郵件等等。

## <a name="instance-pool-support-requests"></a>實例集區支援要求

在[Azure 入口網站](https://portal.azure.com)中建立和管理實例集區的支援要求。

如果您遇到與實例集區部署（建立或刪除）相關的問題，請確定您在**問題子類型**欄位中指定了**實例**集區。

![實例集區支援要求](./media/sql-database-instance-pools/support-request.png)

如果您遇到與集區中的單一實例或資料庫相關的問題，您應該為 Azure SQL Database 受控實例建立一般支援票證。

若要建立較大型的受控實例部署（不論是否有實例集區），您可能需要取得較大的區域配額。 如需詳細資訊，請參閱[Azure SQL Database 的要求配額增加](quota-increase-request.md)。 請注意，如果您使用的是實例集區，部署邏輯會將*集區層級的*總 vCore 耗用量與您的配額進行比較，以判斷是否允許您建立新的資源，而不需要進一步增加您的配額。

## <a name="instance-pool-billing"></a>實例集區計費

實例集區可讓您獨立調整計算和儲存體。 客戶需支付與以虛擬核心測量的集區資源相關聯的計算，以及與每個實例相關聯的儲存體（以 gb 為單位）（前 32 GB 為每個實例免費）。

不論該集區中有多少個實例部署，集區的 vCore 價格都會計費。

針對計算價格（以虛擬核心測量），有兩個可用的定價選項：

  1. *包含的授權*：包含 SQL 授權的價格。 這適用于選擇不套用具有軟體保證之現有 SQL Server 授權的客戶。
  2. *Azure Hybrid Benefit*：包含 SQL Server Azure Hybrid Benefit 的縮減價格。 客戶可以使用其現有的 SQL Server 授權與軟體保證來選擇此價格。 如需資格和其他詳細資料，請參閱[Azure Hybrid Benefit](https://azure.microsoft.com/pricing/hybrid-benefit/)。

針對集區中的個別實例，不可能設定不同的定價選項。 父集區中的所有實例都必須是包含授權的價格或 Azure Hybrid Benefit 價格。 集區的授權模型可以在建立集區之後修改。

> [!IMPORTANT]
> 如果您為實例指定的授權模型與集區中的不同，則會使用集區價格，而且會忽略實例層級值。

如果您在[適用于開發/測試權益](https://azure.microsoft.com/pricing/dev-test/)的訂用帳戶上建立實例集區，您會在 Azure SQL 受控實例上自動收到折扣率高達55%。

如需有關實例集區定價的完整詳細資料，請參閱[受控實例定價頁面](https://azure.microsoft.com/pricing/details/sql-database/managed/)上的 [*實例池*] 區段。

## <a name="next-steps"></a>後續步驟

- 若要開始使用實例集區，請參閱[SQL Database 實例](sql-database-instance-pools-how-to.md)集區操作指南。
- 若要瞭解如何建立您的第一個受控實例，請參閱[快速入門手冊](sql-database-managed-instance-get-started.md)。
- 如需功能與比較清單，請參閱 [SQL 的一般功能](sql-database-features.md)。
- 如需 VNet 組態的詳細資訊，請參閱[受控執行個體 VNet 組態](sql-database-managed-instance-connectivity-architecture.md)。
- 如需建立受控實例並從備份檔案還原資料庫的快速入門，請參閱[建立受控實例](sql-database-managed-instance-get-started.md)。
- 如需使用 Azure 資料庫移轉服務（DMS）進行遷移的教學課程，請參閱[使用 DMS 的受控實例遷移](../dms/tutorial-sql-server-to-managed-instance.md)。
- 如需使用內建的疑難排解智慧進行受控實例資料庫效能的先進監視，請參閱[使用 Azure SQL 分析監視 Azure SQL Database](../azure-monitor/insights/azure-sql.md)。
- 如需定價資訊，請參閱[SQL Database 受控實例定價](https://azure.microsoft.com/pricing/details/sql-database/managed/)。
