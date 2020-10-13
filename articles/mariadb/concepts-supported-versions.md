---
title: 支援的版本-適用於 MariaDB 的 Azure 資料庫
description: 瞭解適用於 MariaDB 的 Azure 資料庫服務中支援的適用于 mariadb 伺服器版本。
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 7/20/2020
ms.openlocfilehash: 0b495bf21b1f78e5e6a9285f67e1769d2d2a0db1
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "87065693"
---
# <a name="supported-azure-database-for-mariadb-server-versions"></a>支援的適用於 MariaDB 的 Azure 資料庫伺服器版本

使用 InnoDB 引擎從開放原始碼 [適用于 mariadb 伺服器](https://downloads.mariadb.org/)開發適用於 MariaDB 的 Azure 資料庫。

適用于 mariadb 會使用 X.x.x.x 命名配置。 X 是主要版本，Y 是次要版本，而 Z 是修補程式版本。

> [!NOTE]
> 在服務中，會使用閘道將連線重新導向到伺服器執行個體。 建立連線之後，MySQL 用戶端會顯示閘道中設定的 MariaDB 的版本，而非 MariaDB 伺服器執行個體上執行的實際版本。 若要判斷適用于 mariadb 伺服器實例的版本，請使用 `SELECT VERSION();` 命令。

適用於 MariaDB 的 Azure 資料庫目前支援下列版本：

## <a name="mariadb-version-102"></a>適用于 mariadb 版本10。2

修補程式版本：10.2.32

請參閱 [適用于 mariadb 檔](https://mariadb.com/kb/en/mariadb-10232-release-notes/) ，以深入瞭解此版本中的改進功能與修正。

## <a name="mariadb-version-103"></a>適用于 mariadb 版本10。3

修補程式版本：10.3.23

請參閱 [適用于 mariadb 檔](https://mariadb.com/kb/en/mariadb-10323-release-notes/) ，以深入瞭解此版本中的改進功能與修正。

## <a name="managing-updates-and-upgrades"></a>管理更新和升級
服務會自動管理修補程式更新的升級。 例如，10.2.21 至10.2.23。  

目前不支援主要和次要版本升級。 例如，不支援從 MariaDB 10.2 升級至 MariaDB 10.3。 如果您想要從10.2 升級為10.3，請取得傾印，並將其 [還原](./howto-migrate-dump-restore.md) 至使用新引擎版本建立的伺服器。

## <a name="next-steps"></a>接下來的步驟

- 如需根據 **服務層級**的特定資源配額和限制的詳細資訊，請參閱 [服務層級](./concepts-pricing-tiers.md)。
