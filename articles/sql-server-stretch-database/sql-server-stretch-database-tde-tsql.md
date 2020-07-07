---
title: 啟用 Stretch Database 的透明資料加密（T-sql）
description: 為 Azure TSQL 上的 SQL Server Stretch Database 啟用透明資料加密 (TDE)
services: sql-server-stretch-database
documentationcenter: ''
ms.assetid: 27753d91-9ca2-4d47-b34d-b5e2c2f029bb
ms.service: sql-server-stretch-database
ms.workload: data-management
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 01/23/2017
author: blazem-msft
ms.author: blazem
ms.reviewer: jroth
manager: jroth
ms.custom: seo-lt-2019
ms.openlocfilehash: 6f1f5f55348069dbfe11b4d5857d93f8ba8c9b19
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "74033962"
---
# <a name="enable-transparent-data-encryption-tde-for-stretch-database-on-azure-transact-sql"></a>為 Azure 上的 Stretch Database 啟用透明資料加密 (TDE) (Transact-SQL)
> [!div class="op_single_selector"]
> * [Azure 入口網站](sql-server-stretch-database-encryption-tde.md)
> * [T Q](sql-server-stretch-database-tde-tsql.md)
>
>

透明資料加密 (TDE) 可在不需變更應用程式的情況下，對靜止的資料庫、相關聯的備份和交易記錄檔執行即時加密和解密，協助防止惡意活動的威脅。

TDE 會使用稱為資料庫加密金鑰的對稱金鑰來加密整個資料庫的儲存體。 資料庫加密金鑰是由內建伺服器憑證保護。 內建伺服器憑證對每個 Azure 伺服器都是唯一的。 Microsoft 會每隔 90 天自動輪換這些憑證。 如需 TDE 的一般描述，請參閱[透明資料加密 (TDE)]。

## <a name="enabling-encryption"></a>啟用加密
若要為 Azure 資料庫 (儲存從已啟用 Stretch 之 SQL Server 料庫移轉的資料) 啟用 TDE，請執行下列步驟：

1. 使用在 master 資料庫中做為系統管理員或 *dbmanager* 角色成員的登入，連接到裝載資料庫之 Azure 伺服器上的 **master** 資料庫
2. 執行下列陳述式來加密資料庫。

```sql
ALTER DATABASE [database_name] SET ENCRYPTION ON;
```

## <a name="disabling-encryption"></a>停用加密
若要為 Azure 資料庫 (儲存從已啟用 Stretch 之 SQL Server 料庫移轉的資料) 停用 TDE，請執行下列步驟：

1. 使用在 master 資料庫中做為系統管理員或 *dbmanager* 角色成員的登入，連接到 **master** 資料庫
2. 執行下列陳述式來加密資料庫。

```sql
ALTER DATABASE [database_name] SET ENCRYPTION OFF;
```

## <a name="verifying-encryption"></a>確認加密
若要確認 Azure 資料庫 (儲存從已啟用 Stretch 之 SQL Server 料庫移轉的資料) 的加密狀態，請執行下列步驟：

1. 使用在 master 資料庫中做為系統管理員或 *dbmanager* 角色成員的登入，連接到 **master** 或執行個體資料庫
2. 執行下列陳述式來加密資料庫。

```sql
SELECT
    [name],
    [is_encrypted]
FROM
    sys.databases;
```

```1``` 的結果表示加密的資料庫，```0``` 表示未加密的資料庫。

<!--Anchors-->
[透明資料加密 (TDE)]: https://msdn.microsoft.com/library/bb934049.aspx


<!--Image references-->

<!--Link references-->
