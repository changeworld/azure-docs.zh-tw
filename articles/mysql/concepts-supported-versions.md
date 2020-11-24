---
title: 支援的版本-適用於 MySQL 的 Azure 資料庫
description: 瞭解適用於 MySQL 的 Azure 資料庫服務中支援的 MySQL 伺服器版本。
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: conceptual
ms.date: 6/3/2020
ms.openlocfilehash: 8033117d9e3c31f8aa9bba06afb7c3b1b7bba67f
ms.sourcegitcommit: 1bf144dc5d7c496c4abeb95fc2f473cfa0bbed43
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/24/2020
ms.locfileid: "95751023"
---
# <a name="supported-azure-database-for-mysql-server-versions"></a>支援的適用於 MySQL 的 Azure 資料庫伺服器版本

適用於 MySQL 的 Azure 資料庫是使用 InnoDB 引擎，從 [MySQL Community Edition](https://www.mysql.com/products/community/) 開發的。

MySQL 會使用 X.Y.Z 命名配置。 X 是主要版本，Y 是次要版本，而 Z 是 Bug 修正版本。 如需此配置的詳細資訊，請參閱 [MySQL 文件](https://dev.mysql.com/doc/refman/5.7/en/which-version.html)。


> [!NOTE]
> 在服務中，會使用閘道將連線重新導向到伺服器執行個體。 建立連線之後，MySQL 用戶端會顯示閘道中設定的 MySQL 的版本，而非 MySQL 伺服器執行個體上執行的實際版本。 若要判斷您的 MySQL 伺服器執行個體的版本，請在 MySQL 提示字元命令下使用 `SELECT VERSION();` 命令。

適用於 MySQL 的 Azure 資料庫目前支援下列版本：

## <a name="mysql-version-56"></a>MySQL 5.6 版

Bug 修正版本：5.6.47

若要深入瞭解此版本中的增強功能和修正，請參閱 MySQL [版本](https://dev.mysql.com/doc/relnotes/mysql/5.6/en/news-5-6-47.html) 資訊。

## <a name="mysql-version-57"></a>MySQL 5.7 版

Bug 修正版本：5.7.29

若要深入瞭解此版本中的增強功能和修正，請參閱 MySQL [版本](https://dev.mysql.com/doc/relnotes/mysql/5.7/en/news-5-7-29.html) 資訊。

## <a name="mysql-version-80"></a>MySQL 版本8。0

Bug 修正版本：8.0.15

若要深入瞭解此版本中的增強功能和修正，請參閱 MySQL [版本](https://dev.mysql.com/doc/relnotes/mysql/8.0/en/news-8-0-15.html) 資訊。

## <a name="managing-updates-and-upgrades"></a>管理更新和升級
服務會自動管理對於錯誤 (bug) 修正版本更新的修補。 例如 5.7.20 至 5.7.21。  

從 MySQL 5.6 升級至5.7 版的服務目前支援主要版本升級。 如需詳細資訊，請參閱 [如何執行主要版本升級](how-to-major-version-upgrade.md)。 如果您想要從5.7 升級為8.0，建議您執行傾印 [並還原](./concepts-migrate-dump-restore.md) 至使用新引擎版本建立的伺服器。

## <a name="next-steps"></a>後續步驟

- 如需適用於 MySQL 的 Azure 資料庫版本控制原則的詳細資訊，請參閱 [這份檔](concepts-version-policy.md)。
- 如需以您 **服務層級** 為依據之特定資源配額和限制的相關資訊，請參閱 [服務層級](./concepts-pricing-tiers.md)
