---
title: 設定伺服器參數 ─ Azure 門戶 - MySQL 的 Azure 資料庫
description: 本文說明如何使用 Azure 入口網站，在適用於 MySQL 的 Azure 資料庫中設定 MySQL 伺服器參數。
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 4/16/2020
ms.openlocfilehash: bd0a867cce9b2a9ad793b491b9042034ef5810f5
ms.sourcegitcommit: 5e49f45571aeb1232a3e0bd44725cc17c06d1452
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/17/2020
ms.locfileid: "81605161"
---
# <a name="how-to-configure-server-parameters-in-azure-database-for-mysql-by-using-the-azure-portal"></a>如何使用 Azure 入口網站，在適用於 MySQL 的 Azure 資料庫中設定伺服器參數

MySQL 的 Azure 資料庫支援某些伺服器參數的組態。 本文說明如何使用 Azure 入口網站來設定這些參數。 並非所有伺服器參數皆可調整。

## <a name="navigate-to-server-parameters-on-azure-portal"></a>瀏覽至 Azure 入口網站上的伺服器參數

1. 登入 Azure 入口網站，然後找到適用於 MySQL 的 Azure 資料庫伺服器。
2. 在 [設定]**** 區段下方，按一下 [伺服器參數]**** 以開啟適用於 MySQL 伺服器的 Azure 資料庫的 [伺服器參數] 頁面。
![Azure 入口網站伺服器參數頁面](./media/howto-server-parameters/auzre-portal-server-parameters.png)
3. 找出您需要調整的任何設定。 檢閱 [描述]**** 資料行，以了解用途和允許的值。
![列舉下拉按鈕](./media/howto-server-parameters/3-toggle_parameter.png)
4. 按下 **「儲存」** 以儲存變更。
![儲存或捨棄變更](./media/howto-server-parameters/4-save_parameters.png)
5. 儲存新的參數值後，隨時可以選取 [全部重設為預設值]**** 回復為所有參數的預設值。
![全部重設為預設值](./media/howto-server-parameters/5-reset_parameters.png)

## <a name="list-of-configurable-server-parameters"></a>可設定的伺服器參數清單

支援的伺服器參數清單會不斷成長。 使用 Azure 入口網站中的 [伺服器參數] 索引標籤，即可根據您的應用程式需求取得定義及設定伺服器參數。

## <a name="non-configurable-server-parameters"></a>無法設定的伺服器參數

InnoDB 緩衝區池大小不可設定,並綁定到[定價層](concepts-service-tiers.md)。

|**定價層**|**虛擬核心**|**InnoDB 緩衝區池大小(MB)(<br>支援最多 4 TB 儲存的伺服器)**| **InnoDB 緩衝區池大小(MB)(<br>支援多達 16 TB 儲存的伺服器)**|
|:---|---:|---:|---:|
|基本| 1| 832| |
|基本| 2| 2560| |
|一般用途| 2| 3584| 7168|
|一般用途| 4| 7680| 15360|
|一般用途| 8| 15360| 30720|
|一般用途| 16| 31232| 62464|
|一般用途| 32| 62976| 125952|
|一般用途| 64| 125952| 251904|
|記憶體最佳化| 2| 7168| 14336|
|記憶體最佳化| 4| 15360| 30720|
|記憶體最佳化| 8| 30720| 61440|
|記憶體最佳化| 16| 62464| 124928|
|記憶體最佳化| 32| 125952| 251904|

這些額外的伺服器參數皆無法在系統中設定：

|**參數**|**固定值**|
| :------------------------ | :-------- |
|基本層中的 innodb_file_per_table|OFF|
|innodb_flush_log_at_trx_commit|1|
|sync_binlog|1|
|innodb_log_file_size|256MB|
|innodb_log_files_in_group|2|

未列在此的其他伺服器參數會設定為其在 MySQL [5.7](https://dev.mysql.com/doc/refman/5.7/en/innodb-parameters.html) 和 [5.6](https://dev.mysql.com/doc/refman/5.6/en/innodb-parameters.html) 版中的現成可用預設值。

## <a name="working-with-the-time-zone-parameter"></a>使用時區參數

### <a name="populating-the-time-zone-tables"></a>填入時區資料表

伺服器上的時區資料表，可以藉由從 MySQL 命令列或 MySQL Workbench 等工具呼叫 `mysql.az_load_timezone` 預存程序來填入。

> [!NOTE]
> 如果您是從 MySQL Workbench 執行 `mysql.az_load_timezone` 命令，您可能需要先執行 `SET SQL_SAFE_UPDATES=0;` 以關閉安全更新模式。

```sql
CALL mysql.az_load_timezone();
```

> [!IMPORTANT]
> 應重新啟動伺服器以確保時區表已正確填充。 要重新啟動伺服器,請使用[Azure 門戶](howto-restart-server-portal.md)或[CLI](howto-restart-server-cli.md)。

若要檢視可用的時區值，請執行以下命令：

```sql
SELECT name FROM mysql.time_zone_name;
```

### <a name="setting-the-global-level-time-zone"></a>設定全域層級時區

全域層級時區可從 Azure 入口網站中的 [伺服器參數]**** 頁面設定。 下方的命令可將全域時區的值設定為 "US/Pacific"。

![設定時區參數](./media/howto-server-parameters/timezone.png)

### <a name="setting-the-session-level-time-zone"></a>設定工作階段層級時區

工作階段層級時區可以藉由從 MySQL 命令列或 MySQL Workbench 等工具呼叫 `SET time_zone` 命令來設定。 以下範例將時區設為 **US/Pacific** 時區。

```sql
SET time_zone = 'US/Pacific';
```

如需[日期和時間函式](https://dev.mysql.com/doc/refman/5.7/en/date-and-time-functions.html#function_convert-tz) \(英文\) 的詳細資訊，請參閱 MySQL 文件。

## <a name="next-steps"></a>後續步驟

- [MySQL Azure 資料庫的連線庫](concepts-connection-libraries.md)。
