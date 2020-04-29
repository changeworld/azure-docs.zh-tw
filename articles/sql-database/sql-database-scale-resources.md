---
title: 調整資源
description: 本文說明如何新增或移除已配置的資源來調整資料庫。
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: jrasnik, carlrab
ms.date: 06/25/2019
ms.openlocfilehash: c4366b2718271b1e27325e6946c5016e9230cea4
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "76835907"
---
# <a name="dynamically-scale-database-resources-with-minimal-downtime"></a>在最短停機時間的情況下動態調整資料庫資源

Azure SQL Database 可讓您以最短的[停機時間](https://azure.microsoft.com/support/legal/sla/sql-database/v1_2/)，以動態方式在資料庫中新增更多資源;不過，有一段很短的時間會將連線中斷連接到資料庫，而這可以使用重試邏輯來減輕。

## <a name="overview"></a>概觀

當您的應用程式需求從幾個裝置和客戶擴充到數百萬個裝置和客戶時，Azure SQL Database 會在最短停機時間內即時調整。 延展性是其中一種最重要 PaaS 特性，可讓您在需要時動態將更多資源新增至服務。 Azure SQL Database 可讓您輕鬆地變更配置給您資料庫的資源 (CPU 電源、記憶體、IO 輸送量和儲存體)。

避免效能問題的原因可能是您無法使用索引編製或查詢重寫方法固定之應用程式的使用量增加。 新增更多資源，可讓您在資料庫達到目前資源限制且需要更強大功能來處理內送工作負載時快速回應。 Azure SQL Database 也可讓您相應減少不需要的資源，以降低成本。

您不需要擔心購買硬體以及變更基礎結構。 透過 Azure 入口網站，即可使用滑桿輕鬆地調整資料庫。

![調整資料庫效能](media/sql-database-scalability/scale-performance.svg)

Azure SQL Database 提供[以 DTU 為基礎的購買模型](sql-database-service-tiers-dtu.md)和[以虛擬核心為基礎的購買模型](sql-database-service-tiers-vcore.md)。

- [以 DTU 為基礎的購買模型](sql-database-service-tiers-dtu.md)提供在下列三個服務層中混用的計算、記憶體和 IO 資源，以支援各種資料庫工作負載 (輕量型到重量型)：基本、標準和進階。 在各層內的效能層級分別提供這些資源的不同混用方式，而且您可以為其新增額外的儲存體資源。
- [以虛擬核心為基礎的購買模型](sql-database-service-tiers-vcore.md)可讓您選擇虛擬核心的數目、記憶體數量，以及儲存體的數量和速度。 此購買模型提供三個服務層級：一般用途、業務關鍵和超大規模資料庫。

您可以透過基本、標準或一般用途服務層級，以較低的每月成本在小型的單一資料庫上建置您的第一個應用程式，並在日後隨時以手動或程式設計方式將服務層級變更為進階或業務關鍵服務層級，以符合您的解決方案需求。 您的應用程式或客戶皆無須停機，即可調整效能。 動態延展性可讓您的資料庫以透明的方式回應快速變化的資源需求，並且讓您只需支付您所需的資源費用。

> [!NOTE]
> 動態延展性與自動調整規模不同。 「自動調整規模」是指服務根據準則自動調整的時間，而動態擴充性則允許以最短的停機時間進行手動調整。

單一 Azure SQL Database 支援手動的動態延展性，但不支援自動調整規模。 如需「自動」** 體驗，請考慮使用彈性集區，以根據個別資料庫的需求讓資料庫共用集區中的資源。
不過，有一些指令碼可協助您對單一 Azure SQL Database 自動調整延展性。 如需範例，請參閱[使用 PowerShell 來監視和調整單一 SQL 資料庫的規模](scripts/sql-database-monitor-and-scale-database-powershell.md)。

您可以在應用程式停機時間最短 (通常平均少於四秒) 的情況下，隨時變更 [DTU 服務層級](sql-database-service-tiers-dtu.md)或[虛擬核心特性](sql-database-vcore-resource-limits-single-databases.md)。 對於許多企業和應用程式而言，只要能夠建立資料庫，並依需求調高或調低的效能即可，尤其是當使用模式相當容易預測時更是如此。 但如果您有無法預測的使用模式，則管理成本和商務模式就會變得相當困難。 針對此案例您可使用彈性集區，以在集區中的多個資料庫之間共用特定數量的 eDTU。

![SQL Database 簡介：不同層級和等級的單一資料庫 DTU](./media/sql-database-what-is-a-dtu/single_db_dtus.png)

Azure SQL Database 的三個特性全部都提供某種動態調整資料庫的能力：

- 在[單一資料庫](sql-database-single-database-scale.md)中，您可以使用 [DTU](sql-database-dtu-resource-limits-single-databases.md) 或 [vCore](sql-database-vcore-resource-limits-single-databases.md) 模型定義將指派給每個資料庫的資源數量上限。
- [受控執行個體](sql-database-managed-instance.md)使用 [vCores](sql-database-managed-instance.md#vcore-based-purchasing-model) 模式，並可讓您定義配置給您執行個體的 CPU 核心上限和儲存體上限。 執行個體內的所有資料庫都會共用配置給執行個體的資源。
- [彈性集區](sql-database-elastic-pool-scale.md)可讓您定義集區中每個資料庫群組的資源上限。

在任何一種情況下起始相應增加或相應減少動作，會重新開機資料庫引擎進程，並視需要將它移至不同的虛擬機器。 將資料庫引擎進程移到新的虛擬機器是**線上**程式，您可以在此過程中繼續使用現有的 Azure SQL Database 服務。 目標資料庫引擎完全初始化並準備好處理查詢之後，連接就會[從來源切換到目標資料庫引擎](sql-database-single-database-scale.md#impact)。 


> [!NOTE]
> 當相應增加/相應減少程式完成時，您可以預期短暫的連接中斷。 如果您已[針對標準暫時性錯誤執行重試邏輯](sql-database-connectivity-issues.md#retry-logic-for-transient-errors)，您將不會注意到容錯移轉。

## <a name="alternative-scale-methods"></a>替代調整方法

調整資源是改善資料庫效能的最簡單且最有效的方式，而不需要變更資料庫或應用程式的程式碼。 在某些情況下，即使是最高的服務層級、計算大小和效能優化可能也無法以成功且符合成本效益的方式處理您的工作負載。 在此情況下，您有下列其他選項可調整您的資料庫：

- [[讀取](sql-database-read-scale-out.md)相應放大] 是一種可用的功能，您可以在其中取得資料的唯讀複本，以便執行報表等需求的唯讀查詢。 唯讀複本會處理您的唯讀工作負載，而不影響您主要資料庫上的資源使用狀況。
- [資料庫分區](sql-database-elastic-scale-introduction.md)是一組技術，可讓您將資料分割成數個資料庫，並分別調整它們。

## <a name="next-steps"></a>後續步驟

- 如需變更資料庫程式碼以改善資料庫效能的資訊，請參閱[尋找和套用效能建議](sql-database-advisor-portal.md)。
- 如需讓內建資料庫智慧最佳化資料庫的資訊，請參閱[自動調整](sql-database-automatic-tuning.md)。
- 如需 Azure SQL Database 服務中讀取縮放的相關資訊，請參閱如何[使用唯讀複本載入平衡唯讀查詢工作負載](sql-database-read-scale-out.md)。
- 如需資料庫分區的資訊，請參閱[使用 Azure SQL Database 相應放大](sql-database-elastic-scale-introduction.md)。
