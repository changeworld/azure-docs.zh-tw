---
title: 從 DTU 遷移至虛擬核心
description: 從 DTU 模型遷移到 vCore 模型。 遷移至 vCore 類似于 standard 和 premium 層之間的升級或降級。
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: sashan, moslake, carlrab
ms.date: 03/09/2020
ms.openlocfilehash: 693065046f92e0e9eade14c43e9942772440937d
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "78945406"
---
# <a name="migrate-from-the-dtu-based-model-to-the-vcore-based-model"></a>從以 DTU 為基礎的模型遷移到 vCore 為基礎的模型

## <a name="migrate-a-database"></a>移轉資料庫

將資料庫從以 DTU 為基礎的購買模型遷移到 vCore 為基礎的購買模型，類似于以 DTU 為基礎的購買模型中的 standard 和 premium 服務層級之間的升級或降級。

## <a name="migrate-databases-that-use-geo-replication"></a>遷移使用異地複寫的資料庫

從以 DTU 為基礎的模型遷移到 vCore 為基礎的購買模型，類似于在 standard 和 premium 服務層級中的資料庫之間升級或降級異地複寫關聯性。 在遷移期間，您不需要停止異地複寫，但必須遵循下列排序規則：

- 升級時，您必須先升級次要資料庫，然後再升級主要資料庫。
- 降級時，順序相反︰您必須先降級主要資料庫，然後再降級次要資料庫。

當您在兩個彈性集區之間使用異地複寫時，建議您將一個集區指定為主要複本，並將另一個做為次要資料庫。 在此情況下，當您要遷移彈性集區時，您應該使用相同的排序指引。 不過，如果您有同時包含主要和次要資料庫的彈性集區，請將具有較高使用率的集區視為主要複本，並據以遵循順序規則。  

下表提供特定遷移案例的指引：

|目前的服務層級|目標服務層級|遷移類型|使用者動作|
|---|---|---|---|
|Standard|一般用途|橫向|可依任何順序遷移，但必須確保適當的虛擬核心大小調整*|
|Premium|業務關鍵|橫向|可依任何順序遷移，但必須確保適當的虛擬核心大小調整*|
|Standard|業務關鍵|升級|必須先遷移次要|
|業務關鍵|Standard|降級|必須先遷移主要|
|Premium|一般用途|降級|必須先遷移主要|
|一般用途|Premium|升級|必須先遷移次要|
|業務關鍵|一般用途|降級|必須先遷移主要|
|一般用途|業務關鍵|升級|必須先遷移次要|
||||

\*根據經驗法則，標準層中的每個 100 Dtu 需要至少 1 vCore，而進階層中的每個 125 Dtu 至少需要 1 vCore。 如需詳細資訊，請參閱[以虛擬核心為基礎的購買模型](https://docs.microsoft.com/azure/sql-database/sql-database-purchase-models#vcore-based-purchasing-model).

## <a name="migrate-failover-groups"></a>遷移容錯移轉群組

在遷移具有多個資料庫的容錯移轉群組時，必須個別遷移主要和次要資料庫。 過程中適用相同的考量和排序規則。 將資料庫轉換成 vCore 為基礎的購買模型之後，容錯移轉群組將會使用相同的原則設定繼續作用。

### <a name="create-a-geo-replication-secondary-database"></a>建立異地複寫次要資料庫

您只能使用與主資料庫相同的服務層級，來建立異地複寫次要資料庫（地理位置）。 對於記錄產生率較高的資料庫，我們建議使用與主要複本相同的計算大小來建立異地次要資料庫。

如果您要在彈性集區中為單一主資料庫建立異地次要資料庫，請確定集區`maxVCore`的設定符合主資料庫的計算大小。 如果您要為另一個彈性集區中的主要複本建立異地次要資料庫，建議您讓集區具有`maxVCore`相同的設定。

## <a name="use-database-copy-to-convert-a-dtu-based-database-to-a-vcore-based-database"></a>使用資料庫複製，將以 DTU 為基礎的資料庫轉換成以 vCore 為基礎的資料庫

您可以將任何資料庫 (具有以 DTU 為基礎的計算大小) 複製到資料庫 (具有以虛擬核心為基礎的計算大小)，而不會有任何限制或特殊排序，但前提是目標計算大小支援來源資料庫的最大資料庫大小。 資料庫複製會在複製作業開始時建立資料的快照集，而不會同步處理來源與目標之間的資料。

## <a name="next-steps"></a>後續步驟

- 如需單一資料庫可用的特定計算大小和儲存體大小選項，請參閱[SQL Database 單一資料庫的 vCore 為基礎的資源限制](sql-database-vcore-resource-limits-single-databases.md)。
- 如需彈性集區可用的特定計算大小和儲存體大小選項，請參閱彈性集區[SQL Database vCore 為基礎的資源限制](sql-database-vcore-resource-limits-elastic-pools.md)。
