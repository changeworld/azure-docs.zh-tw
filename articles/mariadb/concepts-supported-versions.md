---
title: 支援的版本 - MariaDB 的 Azure 資料庫
description: 瞭解 MariaDB 服務的 Azure 資料庫中支援哪些版本的 MariaDB 伺服器。
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 03/18/2020
ms.openlocfilehash: 361ba17532d27a7020be1b6874993da999f48604
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79527702"
---
# <a name="supported-azure-database-for-mariadb-server-versions"></a>支援的適用於 MariaDB 的 Azure 資料庫伺服器版本

MariaDB 的 Azure 資料庫是從開源[MariaDB 伺服器](https://downloads.mariadb.org/)開發，使用 InnoDB 引擎。

MariaDB 使用 X.Y.Z 命名方案。 X 是主要版本，Y 是次要版本，Z 是修補程式版本。

> [!NOTE]
> 在服務中，會使用閘道將連線重新導向到伺服器執行個體。 建立連線之後，MySQL 用戶端會顯示閘道中設定的 MariaDB 的版本，而非 MariaDB 伺服器執行個體上執行的實際版本。 要確定 MariaDB 伺服器實例的版本，請使用 命令`SELECT VERSION();`。

適用於 MariaDB 的 Azure 資料庫目前支援下列版本：

## <a name="mariadb-version-102"></a>MariaDB 版本 10.2

補丁版本： 10.2.25

請參閱[MariaDB 文檔](https://mariadb.com/kb/en/library/mariadb-10225-release-notes/)，瞭解有關此版本中的改進和修復的詳細資訊。

## <a name="mariadb-version-103"></a>馬里亞DB版本 10.3

補丁版本： 10.3.16

請參閱[MariaDB 文檔](https://mariadb.com/kb/en/library/mariadb-10316-release-notes/)，瞭解有關此版本中的改進和修復的詳細資訊。

## <a name="managing-updates-and-upgrades"></a>管理更新和升級
該服務自動管理修補程式更新的升級。 例如，10.2.21 到 10.2.23。  

目前不支援主要和次要版本升級。 例如，不支援從 MariaDB 10.2 升級至 MariaDB 10.3。 如果要從 10.2 升級到 10.3，請進行[轉儲並將其還原](./howto-migrate-dump-restore.md)到使用新引擎版本創建的伺服器。

## <a name="next-steps"></a>後續步驟

- 有關基於**服務層**的特定資源配額和限制的資訊，請參閱[服務層](./concepts-pricing-tiers.md)。
