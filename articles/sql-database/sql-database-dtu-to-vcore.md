---
title: 從 DTU 遷移至虛擬核心
description: 從 DTU 模型遷移到 vCore 模型。 遷移到 vCore 類似于在標準和高級層之間升級或降級。
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: sashan, moslake, carlrab
ms.date: 03/09/2020
ms.openlocfilehash: 693065046f92e0e9eade14c43e9942772440937d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "78945406"
---
# <a name="migrate-from-the-dtu-based-model-to-the-vcore-based-model"></a>從基於 DTU 的模型遷移到基於 vCore 的模型

## <a name="migrate-a-database"></a>移轉資料庫

將資料庫從基於 DTU 的採購模型遷移到基於 vCore 的採購模型類似于在基於 DTU 的採購模型中的標準服務和高級服務層之間升級或降級。

## <a name="migrate-databases-that-use-geo-replication"></a>遷移使用異地複製的資料庫

從基於 DTU 的模型遷移到基於 vCore 的採購模型類似于升級或降級標準層和高級服務層中的資料庫之間的異地複製關係。 在遷移期間，您不必停止異地複製，但必須遵循以下排序規則：

- 升級時，您必須先升級次要資料庫，然後再升級主要資料庫。
- 降級時，順序相反︰您必須先降級主要資料庫，然後再降級次要資料庫。

在兩個彈性池之間使用異地複製時，我們建議您指定一個池作為主池，另一個池指定為輔助池。 在這種情況下，在遷移彈性池時，應使用相同的排序指導。 但是，如果您有同時包含主資料庫和次要資料庫的彈性池，則將利用率較高的池視為主資料庫，並相應地遵循排序規則。  

下表提供了特定遷移方案的指導：

|目前的服務層級|目標服務層級|遷移類型|使用者動作|
|---|---|---|---|
|標準|一般用途|橫向|可依任何順序遷移，但必須確保適當的虛擬核心大小調整*|
|Premium|業務關鍵|橫向|可依任何順序遷移，但必須確保適當的虛擬核心大小調整*|
|標準|業務關鍵|升級|必須先遷移次要|
|業務關鍵|標準|降級|必須先遷移主要|
|Premium|一般用途|降級|必須先遷移主要|
|一般用途|Premium|升級|必須先遷移次要|
|業務關鍵|一般用途|降級|必須先遷移主要|
|一般用途|業務關鍵|升級|必須先遷移次要|
||||

\*根據經驗，標準層中每 100 個 DU 至少需要 1 個 vCore，而高級層中每 125 個 DTU 至少需要 1 個 vCore。 如需詳細資訊，請參閱[以虛擬核心為基礎的購買模型](https://docs.microsoft.com/azure/sql-database/sql-database-purchase-models#vcore-based-purchasing-model).

## <a name="migrate-failover-groups"></a>遷移容錯移轉組

在遷移具有多個資料庫的容錯移轉群組時，必須個別遷移主要和次要資料庫。 過程中適用相同的考量和排序規則。 將資料庫轉換為基於 vCore 的採購模型後，容錯移轉組將使用相同的策略設置保持有效。

### <a name="create-a-geo-replication-secondary-database"></a>創建異地複製次要資料庫

只能通過使用與主資料庫相同的服務層創建異地複製次要資料庫（異地次要資料庫）。 對於日誌生成速率較高的資料庫，我們建議創建與主資料庫具有相同計算大小的地理次要資料庫。

如果要在彈性池中為單個主資料庫創建地理次要資料庫，請確保池`maxVCore`的設置與主資料庫的計算大小相匹配。 如果要為另一個彈性池中的主資料庫創建地理次要資料庫，我們建議池具有相同的`maxVCore`設置。

## <a name="use-database-copy-to-convert-a-dtu-based-database-to-a-vcore-based-database"></a>使用資料庫副本將基於 DTU 的資料庫轉換為基於 vCore 的資料庫

您可以將任何資料庫 (具有以 DTU 為基礎的計算大小) 複製到資料庫 (具有以虛擬核心為基礎的計算大小)，而不會有任何限制或特殊排序，但前提是目標計算大小支援來源資料庫的最大資料庫大小。 資料庫副本在複製操作的開始時間創建資料的快照，並且不會在源和目標之間同步資料。

## <a name="next-steps"></a>後續步驟

- 有關可用於單個資料庫的特定計算大小和存儲大小選擇，請參閱[單個資料庫的 SQL 資料庫 vCore 資源限制](sql-database-vcore-resource-limits-single-databases.md)。
- 有關彈性池可用的特定計算大小和存儲大小選擇，請參閱[彈性池的 SQL 資料庫 vCore 資源限制](sql-database-vcore-resource-limits-elastic-pools.md)。
