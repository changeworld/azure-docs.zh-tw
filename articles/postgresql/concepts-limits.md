---
title: 限制 - Azure 資料庫，用於後格雷SQL - 單個伺服器
description: 本文介紹 Azure 資料庫中 PostgreSQL - 單伺服器的限制，例如連接數和儲存引擎選項。
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 01/28/2020
ms.custom: fasttrack-edit
ms.openlocfilehash: 047e722a0e0ade60d1eb93a48e37333fffafd674
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "76836451"
---
# <a name="limits-in-azure-database-for-postgresql---single-server"></a>後資料庫中的限制 - 單個伺服器
下列各節說明資料庫服務中的容量和功能限制。 如果您想瞭解資源（計算、記憶體、存儲）層，請參閱[定價層](concepts-pricing-tiers.md)一文。


## <a name="maximum-connections"></a>最大連線數
下面顯示了每個定價層和 vCore 的最大連接數。 Azure 系統需要五個連線，以用於監控適用於 PostgreSQL 伺服器的 Azure 資料庫。 

|**定價層**| **虛擬核心**| **連線數目上限** | **最大使用者連接** |
|---|---|---|---|
|基本| 1| 55 | 50|
|基本| 2| 105 | 100|
|一般用途| 2| 150| 145|
|一般用途| 4| 250| 245|
|一般用途| 8| 480| 475|
|一般用途| 16| 950| 945|
|一般用途| 32| 1500| 1495|
|一般用途| 64| 1900| 1895|
|記憶體最佳化| 2| 300| 295|
|記憶體最佳化| 4| 500| 495|
|記憶體最佳化| 8| 960| 955|
|記憶體最佳化| 16| 1900| 1895|
|記憶體最佳化| 32| 1987| 1982|

當連線超過限制時，則可能會收到下列錯誤：
> 嚴重錯誤︰很抱歉，已經有太多用戶端

> [!IMPORTANT]
> 為了獲得最佳體驗，我們建議您使用 pgBouncer 這樣的連接池器來高效地管理連接。

PostgreSQL 連接，即使空閒，也會佔用大約 10MB 的記憶體。 此外，創建新連接需要時間。 大多數應用程式都要求許多短壽命連接，這加劇了這種情況。 結果是實際工作負載可用的資源更少，導致性能下降。 減少空閒連接並重用現有連接的連接池將有助於避免這種情況。 要瞭解更多資訊，請訪問我們的[博客文章](https://techcommunity.microsoft.com/t5/azure-database-for-postgresql/not-all-postgres-connection-pooling-is-equal/ba-p/825717)。

## <a name="functional-limitations"></a>功能限制：
### <a name="scale-operations"></a>調整作業
- 目前不支援基本定價層的雙向動態調整。
- 目前不支援減少伺服器儲存體大小。

### <a name="server-version-upgrades"></a>伺服器版本升級
- 目前不支援在主要資料庫引擎版本之間進行自動轉換。 如果您希望升級至下個主要版本，請將資料庫[備份和還原](./howto-migrate-using-dump-and-restore.md)至使用新引擎版本所建立的伺服器。

> 請注意，在 PostgreSQL 版本 10 之前[，PostgreSQL 版本控制策略](https://www.postgresql.org/support/versioning/)認為_主要版本_升級是第一_個或_第二個數字的增加（例如，9.5 到 9.6 被視為_主要_版本升級）。
> 從版本 10 起，只有第一個數位中的更改才被視為主要版本升級（例如，10.0 到 10.1 是_次要_版本升級，10 到 11 是_主要_版本升級）。

### <a name="vnet-service-endpoints"></a>VNet 服務端點
- VNet 服務端點的支援僅適用於一般用途伺服器和記憶體最佳化伺服器。

### <a name="restoring-a-server"></a>還原伺服器
- 使用 PITR 功能時，建立新伺服器的定價層會與作為新伺服器基礎的伺服器相同。
- 在還原期間建立的新伺服器不會有原始伺服器中的防火牆規則。 新伺服器的防火牆規則必須另外設定。
- 不支援還原已刪除的伺服器。

### <a name="utf-8-characters-on-windows"></a>Windows 上的 UTF-8 字元
- 在某些情況下，於 Windows 上的開放原始碼 PostgreSQL 中不完全支援 UTF-8 字元 ，這會影響適用於 PostgreSQL 的 Azure 資料庫。 如需詳細資訊，請參閱 [postgresql-archive 中的 Bug #15476](https://www.postgresql-archive.org/BUG-15476-Problem-on-show-trgm-with-4-byte-UTF-8-characters-td6056677.html)。

## <a name="next-steps"></a>後續步驟
- 了解[每個定價層中可用的項目](concepts-pricing-tiers.md)
- 了解[支援的 PostgreSQL 資料庫版本](concepts-supported-versions.md)
- 檢閱[如何使用 Azure 入口網站，在適用於 PostgreSQL 的 Azure 資料庫中備份和還原伺服器](howto-restore-server-portal.md)
