---
title: 資料 in replication-適用於 MariaDB 的 Azure 資料庫
description: 瞭解如何使用資料輸入複寫，從外部伺服器同步處理到適用於 MariaDB 的 Azure 資料庫服務。
author: savjani
ms.author: pariks
ms.service: mariadb
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: 331e064bcf11af31a778cb8dd06c463712421b7c
ms.sourcegitcommit: 6ab718e1be2767db2605eeebe974ee9e2c07022b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/12/2020
ms.locfileid: "94533424"
---
# <a name="replicate-data-into-azure-database-for-mariadb"></a>將資料複寫到適用於 MariaDB 的 Azure Database

資料輸入複寫可讓您將來自在內部部署執行的 MariaDB 伺服器、虛擬機器中或由其他雲端提供者所代管的資料庫服務的資料，同步處理到適用於 MariaDB 的 Azure 資料庫服務。 資料輸入複寫是建立在以二進位記錄 (binlog) 檔案位置為基礎的 MariaDB 原生複寫之上。 若要深入了解 binlog 複寫，請參閱 [binlog 複寫概觀](https://mariadb.com/kb/en/library/replication-overview/) \(英文\)。

## <a name="when-to-use-data-in-replication"></a>使用資料帶入複寫的時機
考慮使用資料帶入複寫的主要案例包括：

- **混合式資料同步：** 您可以使用資料輸入複寫，讓內部部署伺服器與適用於 MariaDB 的 Azure 資料庫之間的資料保持同步。 此同步適用於建立混合式應用程式。 當您目前擁有本機資料庫伺服器，但想要將資料移到更接近使用者的區域時，這個方法很吸引人。
- **多重雲端同步處理：** 針對複雜的雲端解決方案，使用資料輸入寫來同步處理適用於 MariaDB 的 Azure 資料庫與不同雲端提供者 (包括這些雲端中所裝載的虛擬機器和資料庫服務) 之間的資料。

## <a name="limitations-and-considerations"></a>限制與考量

### <a name="data-not-replicated"></a>不會複寫資料
來源伺服器上的 [*mysql 系統資料庫*](https://mariadb.com/kb/en/library/the-mysql-database-tables/) 不會複寫。 系統不會複寫來源伺服器上的帳戶和許可權變更。 如果您在來源伺服器上建立帳戶，且此帳戶需要存取複本伺服器，請在複本伺服器端手動建立相同的帳戶。 若要了解系統資料庫中包含哪些資料表，請參閱 [MariaDB 文件](https://mariadb.com/kb/en/library/the-mysql-database-tables/) \(英文\)。

### <a name="requirements"></a>需求
- 來源伺服器版本必須至少是適用于 mariadb 10.2 版。
- 來源和複本伺服器的版本必須相同。 例如，兩者都必須是 MariaDB 10.2 版。
- 每個資料表都必須有主索引鍵。
- 來源伺服器應使用 InnoDB 引擎。
- 使用者必須有權設定二進位記錄，並在來源伺服器上建立新的使用者。
- 如果來源伺服器已啟用 SSL，請確定為網域提供的 SSL CA 憑證已包含在 `mariadb.az_replication_change_master` 預存程式中。 請參閱下列 [範例](howto-data-in-replication.md#link-the-source-and-replica-servers-to-start-data-in-replication) 和 `master_ssl_ca` 參數。
- 確定來源伺服器的 IP 位址已新增至適用於 MariaDB 的 Azure 資料庫複本伺服器的防火牆規則。 使用 [Azure 入口網站](howto-manage-firewall-portal.md)或 [Azure CLI](howto-manage-firewall-cli.md) 更新防火牆規則。
- 確定裝載來源伺服器的機器允許埠3306上的輸入和輸出流量。
- 請確定來源伺服器具有 **公用 IP 位址** 、DNS 可公開存取，或具有 (FQDN) 的完整功能變數名稱。

### <a name="other"></a>其他
- 資料輸入複寫只適用於一般用途和記憶體最佳化定價層。

## <a name="next-steps"></a>後續步驟
- 了解如何[設定資料輸入複寫](howto-data-in-replication.md)。
