---
title: 支援的版本-適用於 MariaDB 的 Azure 資料庫
description: 瞭解適用於 MariaDB 的 Azure 資料庫服務中支援哪些版本的適用于 mariadb 伺服器。
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 03/18/2020
ms.openlocfilehash: 361ba17532d27a7020be1b6874993da999f48604
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "79527702"
---
# <a name="supported-azure-database-for-mariadb-server-versions"></a>支援的適用於 MariaDB 的 Azure 資料庫伺服器版本

適用於 MariaDB 的 Azure 資料庫是使用 InnoDB 引擎，從開放原始碼[適用于 mariadb 伺服器](https://downloads.mariadb.org/)進行開發。

適用于 mariadb 會使用 X-y 命名配置。 X 是主要版本，Y 是次要版本，而 Z 是修補程式版本。

> [!NOTE]
> 在服務中，會使用閘道將連線重新導向到伺服器執行個體。 建立連線之後，MySQL 用戶端會顯示閘道中設定的 MariaDB 的版本，而非 MariaDB 伺服器執行個體上執行的實際版本。 若要判斷適用于 mariadb 伺服器實例的版本，請使用`SELECT VERSION();`命令。

適用於 MariaDB 的 Azure 資料庫目前支援下列版本：

## <a name="mariadb-version-102"></a>適用于 mariadb 版本10。2

修補程式版本：10.2.25

請參閱[適用于 mariadb 檔](https://mariadb.com/kb/en/library/mariadb-10225-release-notes/)，以深入瞭解此版本中的改進功能和修正。

## <a name="mariadb-version-103"></a>適用于 mariadb 版本10。3

修補程式版本：10.3.16

請參閱[適用于 mariadb 檔](https://mariadb.com/kb/en/library/mariadb-10316-release-notes/)，以深入瞭解此版本中的改進功能和修正。

## <a name="managing-updates-and-upgrades"></a>管理更新和升級
服務會自動管理修補程式更新的升級。 例如，10.2.21 至10.2.23。  

目前不支援主要和次要版本升級。 例如，不支援從 MariaDB 10.2 升級至 MariaDB 10.3。 如果您想要從10.2 升級至10.3，請使用傾印並將它[還原](./howto-migrate-dump-restore.md)至使用新引擎版本所建立的伺服器。

## <a name="next-steps"></a>後續步驟

- 如需根據您的**服務層級**之特定資源配額和限制的詳細資訊，請參閱[服務層級](./concepts-pricing-tiers.md)。
