---
title: 什麼是 Azure SQL 受控執行個體集區？
titleSuffix: Azure SQL Managed Instance
description: 深入瞭解 Azure SQL 受控執行個體集區 (preview) 這項功能可提供便利且符合成本效益的方式，將較小的 SQL Server 資料庫大規模遷移至雲端，以及管理多個受控實例。
services: sql-database
ms.service: sql-managed-instance
ms.subservice: operations
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: bonova
ms.author: bonova
ms.reviewer: sstein, carlrab
ms.date: 09/05/2019
ms.openlocfilehash: 8c49f751bae048d2faf7047e538eebcac5d818b5
ms.sourcegitcommit: f8d2ae6f91be1ab0bc91ee45c379811905185d07
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/10/2020
ms.locfileid: "89658728"
---
# <a name="what-is-an-azure-sql-managed-instance-pool-preview"></a>什麼是 Azure SQL 受控執行個體集區 (preview) ？
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Azure SQL 受控執行個體中的實例集區可提供便利且符合成本效益的方式，將較小的 SQL Server 實例大規模遷移至雲端。

執行個體集區可讓您根據您的總移轉需求預先佈建計算資源。 然後，您可以部署數個個別的受控執行個體以達到您預先佈建的計算層級。 例如，如果您預先布建8虛擬核心，您可以部署兩個 2 vCore 和一個 4 vCore 實例，然後將資料庫移轉至這些實例。 在可用的實例集區之前，在遷移至雲端時，通常必須將較小且較少的計算密集型工作負載合併到較大的受控實例。 將資料庫群組遷移至大型實例的需求，通常需要謹慎的容量規劃和資源管理、額外的安全性考慮，以及一些額外的資料匯總在實例層級上運作。

此外，實例集區支援原生 VNet 整合，因此您可以在相同的子網中部署多個實例集區和多個單一實例。

## <a name="key-capabilities"></a>主要功能

實例集區提供下列優點：

1. 裝載2個 vCore 實例的能力。 * \* 僅適用于實例*集區中的實例。
2. 可預測且快速的實例部署時間 (最多5分鐘的) 。
3. 基本 IP 位址配置。

下圖說明在虛擬網路子網內部署多個受控實例的實例集區。

![具有多個實例的實例集區](./media/instance-pools-overview/instance-pools1.png)

實例集區可讓您在相同的虛擬機器上部署多個實例，而虛擬機器的計算大小是以配置給集區的虛擬核心總數為基礎。 此架構可將虛擬機器 *分割* 成多個實例，這些實例可以是任何支援的大小，包括2虛擬核心 (2 vCore 實例僅適用于集區中的實例) 。

初始部署之後，集區實例的管理作業會更快。 這是因為 [虛擬叢集](connectivity-architecture-overview.md#high-level-connectivity-architecture) 的部署或延伸 (一組專用的虛擬機器) 不是布建受控實例的一部分。

因為集區中的所有實例都共用相同的虛擬機器，所以總 IP 配置不會相依于部署的實例數目，因此在具有窄 IP 範圍的子網中部署是很方便的。

每個集區僅有九個 IP 位址的固定 IP 配置 (不包括子網中保留的五個 IP 位址，) 。 如需詳細資訊，請參閱 [單一實例的子網大小需求](vnet-subnet-determine-size.md)。

## <a name="application-scenarios"></a>應用程式案例

下列清單提供應考慮實例集區的主要使用案例：

- 同時遷移 *一組 SQL Server 實例* ，其中多數是較小的大小 (例如2或4虛擬核心) 。
- *可預測和簡短實例建立或調整*的案例很重要。 例如，在需要實例層級功能的多租使用者 SaaS 應用程式環境中部署新的租使用者。
- 具有 *固定成本* 或 *消費限制* 的案例很重要。 例如，執行固定 (的共用開發/測試或示範環境，或不常變更) 大小，您可以在需要時定期部署受控實例。
- VNet 子網中 *最短 IP 位址配置* 很重要的案例。 集區中的所有實例都會共用虛擬機器，因此配置的 IP 位址數目低於單一實例的大小。

## <a name="architecture"></a>架構

實例集區具有與一般 (*單一*) 受控實例類似的架構。 為了支援 [Azure 虛擬網路內的部署](../../virtual-network/virtual-network-for-azure-services.md)，   並為客戶提供隔離和安全性，實例集區也會依賴 [虛擬叢集](connectivity-architecture-overview.md#high-level-connectivity-architecture)。 虛擬叢集代表在客戶的虛擬網路子網內部署的一組專用的隔離式虛擬機器。

這兩種部署模型之間的主要差異在於，實例集區允許在相同的虛擬機器節點上進行多個 SQL Server 程式部署，也就是使用 [Windows 工作物件](https://docs.microsoft.com/windows/desktop/ProcThread/job-objects)來控管資源，而單一實例一律單獨在虛擬機器節點上。

下圖顯示實例集區，以及兩個部署在相同子網中的個別實例，並說明這兩種部署模型的主要架構詳細資料：

![實例集區和兩個個別實例](./media/instance-pools-overview/instance-pools2.png)

每個實例集區會在底下建立個別的虛擬叢集。 集區中的實例和在相同子網中部署的單一實例不會共用配置給 SQL Server 處理常式和閘道元件的計算資源，以確保效能可預測性。

## <a name="resource-limitations"></a>資源限制

執行個體集區和集區內的執行個體有幾個相關資源限制：

- 實例集區僅適用于第5代硬體。
- 集區中的受控實例具有專用 CPU 和 RAM，因此所有實例的虛擬核心匯總數目必須小於或等於配置給集區的虛擬核心數目。
- 所有 [實例層級的限制](resource-limits.md#service-tier-characteristics) 都適用于在集區中建立的實例。
- 除了實例層級限制之外，還會在 *實例集區層級*強加兩個限制：
  - 每個集區的儲存體大小總計 (8 TB) 。
  - 每個集區的資料庫總數 (100) 。
- 無法為實例集區中部署的實例設定 AAD 系統管理員，因此無法使用 AAD 驗證。

所有實例的總儲存體配置和資料庫數目，必須低於或等於實例集區所公開的限制。

- 實例集區支援8、16、24、32、40、64和80虛擬核心。
- 集區中的受控實例支援2、4、8、16、24、32、40、64和80虛擬核心。
- 集區中的受控實例支援 32 GB 到 8 TB 之間的儲存體大小，但下列情況除外：
  - 2個 vCore 實例支援 32 GB 到 640 GB 之間的大小
  - 4 vCore 實例支援 32 GB 到 2 TB 之間的大小

[服務層屬性](resource-limits.md#service-tier-characteristics)與實例集區資源相關聯，因此集區中的所有實例都必須是與集區服務層相同的服務層級。 目前，只有一般用途服務層級可供使用 (請參閱下一節，以瞭解目前預覽) 的限制。

### <a name="public-preview-limitations"></a>公開預覽限制

公開預覽版有下列限制：

- 目前只有一般用途服務層級可供使用。
- 實例集區無法在公開預覽期間進行調整，因此在部署之前，請先仔細規劃容量規劃。
- 尚未提供實例集區建立和設定的 Azure 入口網站支援。 只有透過 PowerShell 支援實例集區上的所有作業。 預先建立的集區中的初始實例部署也僅透過 PowerShell 支援。 一旦部署到集區之後，就可以使用 Azure 入口網站來更新受控實例。
- 在集區以外建立的受控實例無法移至現有的集區，而且在集區內建立的實例不能以單一實例或另一個集區的形式移出。
- 無法使用[保留容量](../database/reserved-capacity-overview.md)實例定價。

## <a name="sql-features-supported"></a>SQL 功能支援

在集區中建立的受控實例，支援 [單一受控實例中支援的相同相容性層級和功能](sql-managed-instance-paas-overview.md#sql-features-supported)。

部署在集區中的每個受控實例都有個別的 SQL Agent 實例。

選擇性的功能或功能，需要您選擇特定的值 (例如實例層級的定序、時區、資料流量的公用端點、容錯移轉群組) 在實例層級設定，而且在集區中的每個實例都可能不同。

## <a name="performance-considerations"></a>效能考量

雖然集區中的受控實例具有專用的 vCore 和 RAM，但它們共用本機磁片 (用於 tempdb 使用) 和網路資源。 但是，如果集區中有多個實例同時具有高資源耗用量，則可能會遇到 *雜訊的鄰近* 效果。 如果您觀察到此行為，請考慮將這些實例部署到較大的集區或單一實例。

## <a name="security-considerations"></a>安全性考量

由於部署在集區中的實例會共用相同的虛擬機器，因此您可能會想要考慮停用造成較高安全性風險的功能，或嚴格控制這些功能的存取權限。 例如，CLR 整合、原生備份和還原、資料庫電子郵件等等。

## <a name="instance-pool-support-requests"></a>實例集區支援要求

在 [Azure 入口網站](https://portal.azure.com)中建立和管理實例集區的支援要求。

如果您在建立或刪除)  (遇到與實例集區部署相關的問題，請務必在 [**問題子類型**] 欄位中指定**實例**集區。

![實例集區支援要求](./media/instance-pools-overview/support-request.png)

如果您遇到與集區中單一受控實例或資料庫相關的問題，您應該建立 Azure SQL 受控執行個體的定期支援票證。

若要建立較大型的 SQL 受控執行個體部署 (不論) 的實例集區，您可能需要取得較大的區域配額。 如需詳細資訊，請參閱 [Azure SQL Database 的要求配額增加](../database/quota-increase-request.md)。 實例集區的部署邏輯會比較集區 *層級的* 總 vCore 耗用量與您的配額，以判斷是否允許您建立新的資源，而不需要進一步增加您的配額。

## <a name="instance-pool-billing"></a>實例集區計費

實例集區允許個別調整計算與儲存體。 客戶需支付與以虛擬核心測量的集區資源相關聯的計算，以及與每個實例相關聯的儲存體（以 32 (gb 為單位），每個實例) 都免費。

無論在該集區中部署的實例數目為何，集區的 vCore 價格都是收費的。

針對虛擬核心) 測量的計算價格 (，有兩個定價選項可用：

  1. *包含的授權*：內含 SQL Server 授權的價格。 這適用于選擇不要將現有 SQL Server 授權套用至軟體保證的客戶。
  2. *Azure Hybrid Benefit*：包含 SQL Server Azure Hybrid Benefit 的較低價格。 客戶可以使用其現有的 SQL Server 授權搭配軟體保證來選擇使用此價格。 如需資格和其他詳細資料，請參閱 [Azure Hybrid Benefit](https://azure.microsoft.com/pricing/hybrid-benefit/)。

針對集區中的個別實例，不可能設定不同的定價選項。 父集區中的所有實例都必須以授權包含價格或 Azure Hybrid Benefit 價格。 建立集區之後，您可以變更集區的授權模型。

> [!IMPORTANT]
> 如果您為實例指定的授權模式與集區中的不同，則會使用集區價格，並忽略實例層級值。

如果您在 [適合開發/測試權益](https://azure.microsoft.com/pricing/dev-test/)的訂用帳戶上建立實例集區，您會在 Azure SQL 受控執行個體上自動獲得最高55% 的折扣費率。

如需有關實例集區定價的完整詳細資訊，請參閱[SQL 受控執行個體定價頁面](https://azure.microsoft.com/pricing/details/sql-database/managed/)上的*實例*集區一節。

## <a name="next-steps"></a>接下來的步驟

- 若要開始使用實例集區，請參閱 [SQL 受控執行個體](instance-pools-configure.md)集區操作指南。
- 若要瞭解如何建立您的第一個受控實例，請參閱 [快速入門手冊](instance-create-quickstart.md)。
- 如需功能和比較清單，請參閱 [AZURE SQL 通用功能](../database/features-comparison.md)。
- 如需 VNet 設定的詳細資訊，請參閱 [SQL 受控執行個體 vnet](connectivity-architecture-overview.md)設定。
- 如需建立受控實例，並從備份檔案還原資料庫的快速入門，請參閱 [建立受控實例](instance-create-quickstart.md)。
- 如需使用 Azure 資料庫移轉服務進行遷移的教學課程，請參閱 [SQL 受控執行個體使用資料庫移轉服務進行遷移](../../dms/tutorial-sql-server-to-managed-instance.md)。
- 若要使用內建的疑難排解智慧來進行 SQL 受控執行個體資料庫效能的 advanced monitoring，請參閱 [使用 Azure SQL 分析監視 AZURE SQL 受控執行個體](../../azure-monitor/insights/azure-sql.md)。
- 如需定價資訊，請參閱 [SQL 受控執行個體定價](https://azure.microsoft.com/pricing/details/sql-database/managed/)。
