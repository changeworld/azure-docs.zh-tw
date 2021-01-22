---
title: 設定伺服器參數-Azure 入口網站-適用於 MariaDB 的 Azure 資料庫
description: 此文章說明如何使用 Azure 入口網站，在適用於 MariaDB 的 Azure 資料庫中設定 MariaDB 伺服器參數。
author: savjani
ms.author: pariks
ms.service: jroth
ms.topic: how-to
ms.date: 10/1/2020
ms.openlocfilehash: cda6b2bd3d49bd71b4c57d5d459c07b61addb493
ms.sourcegitcommit: 52e3d220565c4059176742fcacc17e857c9cdd02
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/21/2021
ms.locfileid: "98664795"
---
# <a name="configure-server-parameters-in-azure-database-for-mariadb-using-the-azure-portal"></a>使用 Azure 入口網站在適用於 MariaDB 的 Azure 資料庫中設定伺服器參數

適用於 MariaDB 的 Azure 資料庫支援某些伺服器參數的組態。 本文說明如何使用 Azure 入口網站來設定這些參數。 並非所有伺服器參數皆可調整。

>[!Note]
> 伺服器參數可以在伺服器層級全域更新，請使用 [Azure CLI](./howto-configure-server-parameters-cli.md)、[PowerShell](./howto-configure-server-parameters-using-powershell.md) 或 [Azure 入口網站](./howto-server-parameters.md)。

## <a name="configure-server-parameters"></a>設定伺服器參數

1. 登入 Azure 入口網站，然後找到適用於 MariaDB 的 Azure 資料庫伺服器。
2. 在 [設定] 區段下方，按一下 [伺服器參數] 以開啟適用於 MariaDB 的 Azure 資料庫伺服器的 [伺服器參數] 頁面。
![Azure 入口網站伺服器參數頁面](./media/howto-server-parameters/azure-portal-server-parameters.png)
3. 找出您需要調整的任何設定。 檢閱 [描述] 資料行，以了解用途和允許的值。
![列舉下拉按鈕](./media/howto-server-parameters/3-toggle_parameter.png)
4. 按一下 [  **儲存** ] 以儲存您的變更。
![儲存或捨棄變更](./media/howto-server-parameters/4-save_parameters.png)
5. 儲存新的參數值後，隨時可以選取 [全部重設為預設值] 回復為所有參數的預設值。
![全部重設為預設值](./media/howto-server-parameters/5-reset_parameters.png)

## <a name="setting-parameters-not-listed"></a>未列出設定參數

如果您要更新的伺服器參數未列在 Azure 入口網站中，您可以選擇性地使用在連接層級設定參數 `init_connect` 。 這會為每個連接到伺服器的用戶端設定伺服器參數。 

1. 在 [設定] 區段下方，按一下 [伺服器參數] 以開啟適用於 MariaDB 的 Azure 資料庫伺服器的 [伺服器參數] 頁面。
2. 搜尋 `init_connect`
3. 以下列格式加入伺服器參數： [值] 資料 `SET parameter_name=YOUR_DESIRED_VALUE` 行。

    例如，您可以藉由將設定為，來變更伺服器的字元集 `init_connect``SET character_set_client=utf8;SET character_set_database=utf8mb4;SET character_set_connection=latin1;SET character_set_results=latin1;`
4. 按一下 [儲存] 以儲存變更。

## <a name="working-with-the-time-zone-parameter"></a>使用時區參數

### <a name="populating-the-time-zone-tables"></a>填入時區資料表

伺服器上的時區資料表，可以藉由從 MySQL 命令列或 MySQL Workbench 等工具呼叫 `mysql.az_load_timezone` 預存程序來填入。

> [!NOTE]
> 如果您是從 MySQL Workbench 執行 `mysql.az_load_timezone` 命令，您可能需要先執行 `SET SQL_SAFE_UPDATES=0;` 以關閉安全更新模式。

```sql
CALL mysql.az_load_timezone();
```

> [!IMPORTANT]
> 建議重新開機伺服器，以確保正確填入時區資料表。 若要重新開機伺服器，請使用 [Azure 入口網站](howto-restart-server-portal.md)或 [CLI](howto-restart-server-cli.md)。
若要檢視可用的時區值，請執行以下命令：

```sql
SELECT name FROM mysql.time_zone_name;
```

### <a name="setting-the-global-level-time-zone"></a>設定全域層級時區

全域層級時區可從 Azure 入口網站中的 [伺服器參數] 頁面設定。 下方的命令可將全域時區的值設定為 "US/Pacific"。

![設定時區參數](./media/howto-server-parameters/timezone.png)

### <a name="setting-the-session-level-time-zone"></a>設定工作階段層級時區

工作階段層級時區可以藉由從 MySQL 命令列或 MySQL Workbench 等工具呼叫 `SET time_zone` 命令來設定。 以下範例將時區設為 **US/Pacific** 時區。

```sql
SET time_zone = 'US/Pacific';
```

請參閱有關[日期和時間函數](https://mariadb.com/kb/en/library/convert_tz/)的 MariaDB 文件。

## <a name="next-steps"></a>後續步驟

- 深入瞭解 [伺服器參數](concepts-server-parameters.md)
