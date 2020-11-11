---
title: 設定伺服器參數-Azure 入口網站適用於 MySQL 的 Azure 資料庫彈性伺服器
description: 本文說明如何使用 Azure 入口網站，在適用於 MySQL 的 Azure 資料庫彈性的伺服器中設定 MySQL 伺服器參數。
author: ambhatna
ms.author: ambhatna
ms.service: mysql
ms.topic: how-to
ms.date: 11/10/2020
ms.openlocfilehash: 7733a6211363b4f1c9e9006f757b4d152c7af7f5
ms.sourcegitcommit: b4880683d23f5c91e9901eac22ea31f50a0f116f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/11/2020
ms.locfileid: "94489551"
---
# <a name="configure-server-parameters-in-azure-database-for-mysql---flexible-server-using-the-azure-portal"></a>使用 Azure 入口網站在適用於 MySQL 的 Azure 資料庫彈性的伺服器中設定伺服器參數

> [!IMPORTANT] 
> 適用於 MySQL 的 Azure 資料庫 - 彈性伺服器目前處於公開預覽狀態。

您可以使用伺服器參數來管理適用於 MySQL 的 Azure 資料庫彈性的伺服器設定。 當您建立伺服器時，伺服器參數會設定為預設值和建議值。  

本文描述如何使用 Azure 入口網站來查看和設定伺服器參數。 Azure 入口網站上的伺服器參數分頁會顯示可修改和不可修改的伺服器參數。 不可修改的伺服器參數會呈現灰色。

>[!Note]
> 伺服器參數可以在伺服器層級全域更新，請使用 [Azure CLI](./how-to-configure-server-parameters-cli.md) 或 [Azure 入口網站](./how-to-configure-server-parameters-portal.md)。

## <a name="configure-server-parameters"></a>設定伺服器參數

1. 登入 [Azure 入口網站](https://portal.azure.com)，然後找出您適用於 MySQL 的 Azure 資料庫彈性的伺服器。
2. 在 [ **設定** ] 區段下，按一下 [ **伺服器參數** ]，開啟適用於 MySQL 的 Azure 資料庫彈性伺服器的 [伺服器參數] 頁面。
[:::image type="content" source="./media/how-to-server-parameters/azure-portal-server-parameters.png" alt-text="Azure 入口網站伺服器參數] 頁面":::](./media/how-to-server-parameters/azure-portal-server-parameters.png#lightbox)
3. 找出您需要調整的任何伺服器參數。 檢閱 [描述] 資料行，以了解用途和允許的值。
[:::image type="content" source="./media/how-to-server-parameters/3-toggle-parameter.png" alt-text="列舉下拉式清單":::](./media/how-to-server-parameters/3-toggle-parameter.png#lightbox)
4. 按一下 [  **儲存** ] 以儲存您的變更。
[:::image type="content" source="./media/how-to-server-parameters/4-save-parameters.png" alt-text="儲存或捨棄變更":::](./media/how-to-server-parameters/4-save-parameters.png#lightbox)
5. 靜態參數是需要伺服器重新開機才會生效的參數。 如果您要修改靜態參數，系統會提示您 **立即重新開機** ，或 **稍後重新開機** 。
[:::image type="content" source="./media/how-to-server-parameters/5-save-parameter.png" alt-text="在靜態參數儲存時重新開機":::](./media/how-to-server-parameters/5-save-parameter.png#lightbox)
6. 儲存新的參數值後，隨時可以選取 [全部重設為預設值] 回復為所有參數的預設值。
[:::image type="content" source="./media/how-to-server-parameters/6-reset-parameters.png" alt-text="全部重設為預設值":::](./media/how-to-server-parameters/6-reset-parameters.png#lightbox)

## <a name="setting-non-modifiable-server-parameters"></a>設定不可修改的伺服器參數

如果您要更新的伺服器參數不可修改，您可以選擇性地使用在連接層級設定參數 `init_connect` 。 這會為每個連接到伺服器的用戶端設定伺服器參數。 

1. 在 [設定] 區段下方，按一下 [伺服器參數] 以開啟適用於 MySQL 伺服器的 Azure 資料庫的 [伺服器參數] 頁面。
2. 搜尋 `init_connect`
3. 以下列格式加入伺服器參數： [值] 資料 `SET parameter_name=YOUR_DESIRED_VALUE` 行。

    例如，您可以藉由將設定為，來變更伺服器的字元集 `init_connect``SET character_set_client=utf8;SET character_set_database=utf8mb4;SET character_set_connection=latin1;SET character_set_results=latin1;`
4. 按一下 [儲存] 以儲存變更。

>[!Note]
> `init_connect` 可用來變更在工作階段層級不需要 SUPER 權限的參數。 若要確認您是否可以使用 `init_connect` 來設定參數，請執行 `set session parameter_name=YOUR_DESIRED_VALUE;` 命令；如果出現「拒絕存取；您需要 SUPER 權限」錯誤，表示您無法使用 'init_connect' 來設定參數。

## <a name="working-with-the-time-zone-parameter"></a>使用時區參數

### <a name="populating-the-time-zone-tables"></a>填入時區資料表

伺服器上的時區資料表，可以藉由從 MySQL 命令列或 MySQL Workbench 等工具呼叫 `mysql.az_load_timezone` 預存程序來填入。

> [!NOTE]
> 如果您是從 MySQL Workbench 執行 `mysql.az_load_timezone` 命令，您可能需要先執行 `SET SQL_SAFE_UPDATES=0;` 以關閉安全更新模式。

```sql
CALL mysql.az_load_timezone();
```

> [!IMPORTANT]
> 建議重新開機伺服器，以確保正確填入時區資料表。<!-- FIX ME To restart the server, use the [Azure portal](how-to-restart-server-portal.md) or [CLI](how-to-restart-server-cli.md).-->

若要檢視可用的時區值，請執行以下命令：

```sql
SELECT name FROM mysql.time_zone_name;
```

### <a name="setting-the-global-level-time-zone"></a>設定全域層級時區

全域層級時區可從 Azure 入口網站中的 [伺服器參數] 頁面設定。 下方的命令可將全域時區的值設定為 "US/Pacific"。

[:::image type="content" source="./media/how-to-server-parameters/timezone.png" alt-text="設定時區參數":::](./media/how-to-server-parameters/timezone.png#lightbox)

### <a name="setting-the-session-level-time-zone"></a>設定工作階段層級時區

工作階段層級時區可以藉由從 MySQL 命令列或 MySQL Workbench 等工具呼叫 `SET time_zone` 命令來設定。 以下範例將時區設為 **US/Pacific** 時區。

```sql
SET time_zone = 'US/Pacific';
```

如需[日期和時間函式](https://dev.mysql.com/doc/refman/5.7/en/date-and-time-functions.html#function_convert-tz) \(英文\) 的詳細資訊，請參閱 MySQL 文件。

## <a name="next-steps"></a>後續步驟

- 如何 [在 Azure CLI 中設定伺服器參數](./how-to-configure-server-parameters-cli.md)
