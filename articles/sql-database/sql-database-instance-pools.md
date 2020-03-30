---
title: 實例池（預覽）
description: 本文介紹 Azure SQL 資料庫實例池（預覽）。
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
ms.openlocfilehash: c1e740fbfa4bf1e8a77a2d9d6060ab39dba7ae7b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79256207"
---
# <a name="what-are-sql-database-instance-pools-preview"></a>什麼是 SQL 資料庫實例池（預覽）？

實例池是 Azure SQL 資料庫中的新資源，它提供了一種方便且經濟高效的方法，用於大規模將較小的 SQL 實例遷移到雲。

執行個體集區可讓您根據您的總移轉需求預先佈建計算資源。 然後，您可以部署數個個別的受控執行個體以達到您預先佈建的計算層級。 例如，如果預先預配 8 個 vCore，則可以部署兩個 2 vCore 和一個 4 個 vCore 實例，然後將資料庫移轉到這些實例中。 在實例池可用之前，在遷移到雲時，通常必須將更小且計算密集型工作負載合併到較大的託管實例中。 需要將資料庫組遷移到大型實例通常需要仔細的容量規劃和資源治理、額外的安全注意事項以及實例級別的一些額外的資料整合工作。

此外，實例池支援本機 VNet 集成，因此您可以在同一子網中部署多個實例池和多個單個實例。


## <a name="key-capabilities-of-instance-pools"></a>實例池的關鍵功能

實例池提供以下優勢：

1. 能夠承載 2 個 vCore 實例。 *僅適用于實例池中的實例。 \* *
2. 可預測且快速的實例部署時間（最多 5 分鐘）。
3. 最少的 IP 位址分配。

下圖演示了一個實例池，該實例池在虛擬網路子網中部署了多個實例。

![具有多個實例的實例池](./media/sql-database-instance-pools/instance-pools1.png)

實例池允許在同一虛擬機器上部署多個實例，其中虛擬機器的計算大小基於為池分配的 vCore 的總數。 此體系結構允許將虛擬機器*分區*為多個實例，這些實例可以是任何受支援的大小，包括 2 個 vCore（2 個 vCore 實例僅適用于池中的實例）。

最初部署池後，對池中的實例的管理操作要快得多。 這些操作更快，因為部署或擴展[虛擬叢集](sql-database-managed-instance-connectivity-architecture.md#high-level-connectivity-architecture)（專用虛擬機器集）不是預配託管實例的一部分。

由於池中的所有實例都共用同一虛擬機器，因此總 IP 分配不依賴于部署的實例數，這便於部署在 IP 範圍狹窄的子網中。

每個池的固定 IP 分配只有九個 IP 位址（不包括子網中保留的五個 IP 位址，這些位址是為其自身需要保留的）。 有關詳細資訊，請參閱[單個實例的子網大小要求](sql-database-managed-instance-determine-size-vnet-subnet.md)。

## <a name="application-scenarios-for-instance-pools"></a>實例池的應用程式方案

以下清單提供了應考慮實例池的主要用例：

- 同時遷移*一組 SQL 實例*，其中大多數大小較小（例如 2 或 4 個 vCore）。
- *可預測和短期實例創建或縮放*很重要的方案。 例如，在需要實例級功能的多租戶 SaaS 應用程式環境中部署新租戶。
- 具有*固定成本*或*支出限制*的方案非常重要。 例如，運行具有固定（或不經常更改）大小的共用開發測試或演示環境，在環境中，您可以在需要時定期部署託管實例。
- VNet 子網中*最少 IP 位址分配*很重要的方案。 池中的所有實例都在共用虛擬機器，因此分配的 IP 位址數低於單個實例。


## <a name="architecture-of-instance-pools"></a>實例池的體系結構

實例池具有與常規託管實例（*單個實例*）類似的體系結構。 為了支援 [Azure 虛擬網路 （VNet）](../virtual-network/virtual-network-for-azure-services.md#deploy-azure-services-into-virtual-networks) 中的部署，並為客戶提供隔離和安全，實例池還依賴于 [虛擬叢集](sql-database-managed-instance-connectivity-architecture.md#high-level-connectivity-architecture)。 虛擬叢集表示部署在客戶虛擬網路子網內的專用隔離虛擬機器集。

這兩種部署模型之間的主要區別是實例池允許在同一虛擬機器節點上進行多個 SQL Server 進程部署，該部署使用[Windows 工作物件](https://docs.microsoft.com/windows/desktop/ProcThread/job-objects)進行資源管理，而單個實例始終單獨在虛擬機器節點上。

下圖顯示了實例池和部署在同一子網中的兩個單獨的實例，並說明了兩個部署模型的主要體系結構詳細資訊：

![實例池和兩個單獨的實例](./media/sql-database-instance-pools/instance-pools2.png)

每個實例池在下方創建一個單獨的虛擬叢集。 池中的實例和部署在同一子網中的單個實例不共用分配給 SQL Server 進程和閘道元件的計算資源，這可確保性能可預測性。

## <a name="instance-pools-resource-limitations"></a>實例池資源限制

執行個體集區和集區內的執行個體有幾個相關資源限制：

- 實例池僅在 Gen5 硬體上可用。
- 池中的實例具有專用 CPU 和 RAM，因此所有實例的 vCore 的聚合數量必須小於或等於分配給池的 vCore 數。
- 所有[實例級別限制](sql-database-managed-instance-resource-limits.md#service-tier-characteristics)都適用于在池中創建的實例。
- 除了實例級限制外，*在實例池級別*也規定了兩個限制：
  - 每個池的總存儲大小（8 TB）。
  - 每個池的資料庫總數 （100）。

所有實例的總存儲分配和資料庫數必須低於或等於實例池公開的限制。

- 實例池支援 8、16、24、32、40、64 和 80 個 vCore。
- 池內的託管實例支援 2、4、8、16、24、32、40、64 和 80 vCore。
- 池內的託管實例支援 32 GB 和 8 TB 之間的存儲大小，但：
  - 2 個 vCore 實例支援 32 GB 和 640 GB 之間的大小
  - 4 個 vCore 實例支援 32 GB 和 2 TB 之間的大小

[服務層屬性](sql-database-managed-instance-resource-limits.md#service-tier-characteristics)與實例池資源相關聯，因此池中的所有實例必須與池的服務層相同。 此時，只有泛型服務層可用（請參閱以下有關當前預覽中限制的部分）。

### <a name="public-preview-limitations"></a>公開預覽限制

公共預覽版有以下限制：

- 目前，只有泛型服務層可用。
- 在公共預覽期間無法縮放實例池，因此在部署之前必須仔細規劃容量。
- 實例池創建和配置的 Azure 門戶支援尚不可用。 僅通過 PowerShell 支援實例池上的所有操作。 僅通過 PowerShell 支援預創建池中的初始實例部署。 部署到池後，可以使用 Azure 門戶更新託管實例。
- 在池外創建的託管實例無法移動到現有池中，在池內創建的實例不能作為單個實例移動到外部，也不能移動到另一個池。
- 保留實例定價（包含許可證或與 Azure 混合權益一起）不可用。

## <a name="sql-features-supported"></a>SQL 功能支援

在池中創建的實例支援[單個託管實例中支援的相同相容性級別和功能](sql-database-managed-instance.md#sql-features-supported)。

在池中部署的每個託管實例都有一個單獨的 SQL 代理實例。

需要您選擇特定值的可選功能或功能（如實例級排序規則、時區、資料流量的公共終結點、容錯移轉組）在實例級別進行配置，並且對於池中的每個實例可以不同。

## <a name="performance-considerations"></a>效能考量

儘管池中的託管實例確實具有專用 vCore 和 RAM，但它們共用本地磁片（用於 tempdb 使用）和網路資源。 這不太可能，但如果池中的多個實例同時具有高資源消耗，則可能會體驗*嘈雜的鄰居*效果。 如果觀察到此行為，請考慮將這些實例部署到更大的池或單個實例。

## <a name="security-considerations"></a>安全性考量

由於部署在池中的實例共用相同的虛擬機器，因此您可能需要考慮禁用引入更高安全風險的功能，或者牢牢控制對這些功能的存取權限。 例如，CLR 集成、本機備份和還原、資料庫電子郵件等。

## <a name="instance-pool-support-requests"></a>實例池支援請求

在[Azure 門戶](https://portal.azure.com)中創建和管理實例池的支援請求。

如果遇到與實例池部署（創建或刪除）相關的問題，請確保在 **"問題子類型"** 欄位中指定**實例池**。

![實例池支援請求](./media/sql-database-instance-pools/support-request.png)

如果遇到與池中的單個實例或資料庫相關的問題，則應為 Azure SQL 資料庫託管實例創建常規支援票證。

要創建更大的託管實例部署（無論是否具有實例池），您可能需要獲取更大的區域配額。 有關詳細資訊，請參閱請求[Azure SQL 資料庫的配額增加](quota-increase-request.md)。 請注意，如果使用實例池，部署邏輯會將*池級別的總*vCore 消耗量與配額進行比較，以確定是否允許在不進一步增加配額的情況下創建新資源。

## <a name="instance-pool-billing"></a>實例池計費

實例池允許獨立擴展計算和存儲。 客戶為與 vCore 中測量的池資源關聯的計算付費，以及與以千百萬位元組為單位的每個實例關聯的存儲（每個實例都是免費的前 32 GB）。

無論池中部署了多少個實例，都會收取池的 vCore 價格。

對於計算價格（以 vCore 為單位），有兩個定價選項可用：

  1. *包含許可證*：包括 SQL 許可證的價格。 這是為選擇不使用軟體保證應用現有 SQL Server 許可證的客戶。
  2. *Azure 混合權益*：包括 SQL Server 的 Azure 混合權益的降低價格。 客戶可以通過使用其現有的 SQL Server 許可證（以及軟體保證）選擇此價格。 有關資格和其他詳細資訊，請參閱[Azure 混合權益](https://azure.microsoft.com/pricing/hybrid-benefit/)。

池中的單個實例無法設置不同的定價選項。 父池中的所有實例必須以許可證包含價格或 Azure 混合權益價格。 創建池後，可以更改池的許可證模型。

> [!IMPORTANT]
> 如果為與池中的實例不同的實例指定許可證模型，則使用池價格並忽略實例級別值。

如果在[符合開發測試權益的訂閱](https://azure.microsoft.com/pricing/dev-test/)上創建實例池，則會自動在 Azure SQL 託管實例上收到高達 55% 的折扣率。

有關實例池定價的完整詳細資訊，請參閱[託管實例定價頁上](https://azure.microsoft.com/pricing/details/sql-database/managed/)的*實例池*部分。

## <a name="next-steps"></a>後續步驟

- 要開始使用實例池，請參閱[SQL 資料庫實例池如何指導](sql-database-instance-pools-how-to.md)。
- 要瞭解如何創建第一個託管實例，請參閱[快速入門手冊](sql-database-managed-instance-get-started.md)。
- 如需功能與比較清單，請參閱 [SQL 的一般功能](sql-database-features.md)。
- 如需 VNet 組態的詳細資訊，請參閱[受控執行個體 VNet 組態](sql-database-managed-instance-connectivity-architecture.md)。
- 有關創建託管實例並從備份檔案還原資料庫的快速入門，請參閱[創建託管實例](sql-database-managed-instance-get-started.md)。
- 有關使用 Azure 資料庫移轉服務 （DMS） 進行遷移的教程，請參閱[使用 DMS 進行託管實例遷移](../dms/tutorial-sql-server-to-managed-instance.md)。
- 有關使用內置故障排除智慧對託管實例資料庫性能的高級監視，請參閱[使用 Azure SQL 分析監視 Azure SQL 資料庫](../azure-monitor/insights/azure-sql.md)。
- 有關定價資訊，請參閱[SQL 資料庫託管實例定價](https://azure.microsoft.com/pricing/details/sql-database/managed/)。
