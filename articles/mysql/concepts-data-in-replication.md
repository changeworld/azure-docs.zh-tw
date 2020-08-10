---
title: 資料複寫-適用於 MySQL 的 Azure 資料庫
description: 瞭解如何使用資料輸入複寫，從外部伺服器同步處理到適用於 MySQL 的 Azure 資料庫服務。
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 8/7/2020
ms.openlocfilehash: a9d6c1b2438f20a06062842b96b147e094760238
ms.sourcegitcommit: bfeae16fa5db56c1ec1fe75e0597d8194522b396
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/10/2020
ms.locfileid: "88031212"
---
# <a name="replicate-data-into-azure-database-for-mysql"></a>將資料複寫至適用於 MySQL 的 Azure 資料庫

資料輸入複寫可讓您將來自外部 MySQL 伺服器的資料，同步處理到適用於 MySQL 的 Azure 資料庫服務。 外部伺服器可以位於內部部署環境、虛擬機器中或其他雲端提供者所裝載的資料庫服務內。 資料帶入複寫是建立在以二進位記錄 (binlog) 檔案位置為基礎的 MySQL 原生複寫之上。 若要深入了解 binlog 複寫，請參閱 [MySQL binlog 複寫概觀](https://dev.mysql.com/doc/refman/5.7/en/binlog-replication-configuration-overview.html) \(英文\)。 

## <a name="when-to-use-data-in-replication"></a>使用資料帶入複寫的時機
考慮使用資料帶入複寫的主要案例包括：

- **混合式資料同步：** 您可使用資料帶入複寫，讓內部部署伺服器與適用於 MySQL 的 Azure 資料庫之間的資料保持同步。 此同步適用於建立混合式應用程式。 當您目前擁有本機資料庫伺服器，但想要將資料移到更接近使用者的區域時，這個方法很吸引人。
- **多重雲端同步處理：** 針對複雜的雲端解決方案，使用資料帶入複寫來同步處理適用於 MySQL 的 Azure 資料庫與不同雲端提供者 (包括這些雲端中所裝載的虛擬機器和資料庫服務) 之間的資料。
 
針對遷移案例，請使用[Azure 資料庫移轉服務](https://azure.microsoft.com/services/database-migration/) (DMS) 。

## <a name="limitations-and-considerations"></a>限制與考量

### <a name="data-not-replicated"></a>不會複寫資料
不會複寫主要伺服器上的 [mysql 系統資料庫**](https://dev.mysql.com/doc/refman/5.7/en/system-schema.html) \(英文\)。 不會複寫主要伺服器上對於帳戶和權限的變更。 如果您在主要伺服器上建立帳戶且此帳戶需要存取複本伺服器，請在複本伺服器端手動建立相同的帳戶。 若要了解系統資料庫中包含哪些資料表，請參閱 [MySQL 手冊](https://dev.mysql.com/doc/refman/5.7/en/system-schema.html) \(英文\)。

### <a name="filtering"></a>篩選
若要略過從主控伺服器複寫的資料表 (裝載于內部部署環境、虛擬機器或其他雲端提供者所裝載的資料庫服務) ，則 `replicate_wild_ignore_table` 支援參數。 （選擇性）使用[Azure 入口網站](howto-server-parameters.md)或[Azure CLI](howto-configure-server-parameters-using-cli.md)，在 Azure 託管的複本伺服器上更新此參數。

請檢閱 [MySQL 文件](https://dev.mysql.com/doc/refman/8.0/en/replication-options-replica.html#option_mysqld_replicate-wild-ignore-table) \(英文\)，以深入了解此參數。

### <a name="requirements"></a>需求
- 主要伺服器版本必須至少是 MySQL 5.6 版。 
- 主要和複本伺服器的版本必須相同。 例如，兩者都必須是 MySQL 5.6 版，或兩者都必須是 MySQL 5.7 版。
- 每個資料表都必須有主索引鍵。
- 主要伺服器應該使用 MySQL InnoDB 引擎。
- 使用者必須有權設定二進位記錄，以及在主要伺服器上建立新的使用者。
- 如果主伺服器已啟用 SSL，請確定已在預存程式中包含為該網域提供的 SSL CA 憑證 `mysql.az_replication_change_master` 。 請參閱下列[範例](https://docs.microsoft.com/azure/mysql/howto-data-in-replication#link-master-and-replica-servers-to-start-data-in-replication)和 `master_ssl_ca` 參數。
- 請確定主要伺服器的 IP 位址已新增至「適用於 MySQL 的 Azure 資料庫」複本伺服器的防火牆規則。 使用 [Azure 入口網站](https://docs.microsoft.com/azure/mysql/howto-manage-firewall-using-portal)或 [Azure CLI](https://docs.microsoft.com/azure/mysql/howto-manage-firewall-using-cli) 更新防火牆規則。
- 確定裝載主要伺服器的機器允許連接埠 3306 上的輸入和輸出流量。
- 確定主伺服器具有**公用 IP 位址**、DNS 可公開存取，或具有 (FQDN) 的完整功能變數名稱。

### <a name="other"></a>其他
- 資料輸入複寫只適用於一般用途和記憶體最佳化定價層。
- 不支援全域交易識別碼 (GTID)。

## <a name="next-steps"></a>後續步驟
- 瞭解如何[設定複寫中的資料](howto-data-in-replication.md)
- 了解如何[在 Azure 中使用讀取複本進行複寫](concepts-read-replicas.md)
- 深入瞭解如何[使用 DMS 以最短的停機時間遷移資料](howto-migrate-online.md)