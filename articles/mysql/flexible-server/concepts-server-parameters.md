---
title: 伺服器參數-適用於 MySQL 的 Azure 資料庫彈性的伺服器
description: 本主題提供在適用於 MySQL 的 Azure 資料庫彈性的伺服器中設定伺服器參數的指導方針。
author: ambhatna
ms.author: ambhatna
ms.service: mysql
ms.topic: conceptual
ms.date: 11/10/2020
ms.openlocfilehash: 58978f120578afeca129b0d8928713835def8418
ms.sourcegitcommit: b4880683d23f5c91e9901eac22ea31f50a0f116f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/11/2020
ms.locfileid: "94496603"
---
# <a name="server-parameters-in-azure-database-for-mysql---flexible-server"></a>適用於 MySQL 的 Azure 資料庫-彈性伺服器中的伺服器參數

> [!IMPORTANT]
> 適用於 MySQL 的 Azure 資料庫 - 彈性伺服器目前處於公開預覽狀態。

本文提供在適用於 MySQL 的 Azure 資料庫彈性的伺服器中設定伺服器參數的考慮和指導方針。

## <a name="what-are-server-variables"></a>什麼是伺服器變數？ 

MySQL 引擎提供許多不同的 [伺服器變數/參數](https://dev.mysql.com/doc/refman/5.7/en/server-option-variable-reference.html) ，可用來設定和微調引擎行為。 某些參數可在執行時間期間動態設定，而其他參數則是「靜態」，需要重新開機伺服器才能套用。

適用於 MySQL 的 Azure 資料庫彈性的伺服器可讓您使用 [Azure 入口網站](./how-to-configure-server-parameters-portal.md) 來變更各種 MySQL 伺服器參數的值，並 [Azure CLI](./how-to-configure-server-parameters-cli.md) 符合您的工作負載需求。

## <a name="configurable-server-parameters"></a>可設定的伺服器參數

您可以使用伺服器參數來管理適用於 MySQL 的 Azure 資料庫彈性的伺服器設定。 當您建立伺服器時，伺服器參數會設定為預設值和建議值。 Azure 入口網站上的伺服器參數分頁會顯示可修改和不可修改的伺服器參數。 不可修改的伺服器參數會呈現灰色。

支援的伺服器參數清單會不斷成長。 使用 Azure 入口網站中的 [伺服器參數] 索引標籤，即可查看完整清單及設定伺服器參數值。

請參閱下列各節，以深入瞭解數個經常更新之伺服器參數的限制。 這些限制取決於伺服器 (虛擬核心) 的計算層和大小。

> [!NOTE]
> 如果您想要修改的伺服器參數是不可修改的，但您想要將它視為可修改的環境，請開啟 [UserVoice](https://feedback.azure.com/forums/597982-azure-database-for-mysql) 專案或投票是否已存在意見反應，以協助我們設定優先順序。

### <a name="log_bin_trust_function_creators"></a>log_bin_trust_function_creators

在適用於 MySQL 的 Azure 資料庫彈性的伺服器中，一律會啟用二進位記錄 (也就是 `log_bin` 在) 上設定為。 如果您想要使用觸發程式，將會收到類似于 *您未啟用超級許可權和二進位記錄的錯誤 (您可能會想要使用較不安全的 `log_bin_trust_function_creators` 變數)* 。 

二進位記錄格式一律是 **row** ，而伺服器的所有連接 **一律** 會使用以資料列為基礎的二進位記錄。 使用以資料列為基礎的二進位記錄時，不會有安全性問題，而且二進位記錄無法中斷，因此您可以安全地設定 [`log_bin_trust_function_creators`](https://dev.mysql.com/doc/refman/5.7/en/replication-options-binary-log.html#sysvar_log_bin_trust_function_creators) 為 **TRUE** 。

### <a name="innodb_buffer_pool_size"></a>innodb_buffer_pool_size

請檢閱 [MySQL 文件](https://dev.mysql.com/doc/refman/5.7/en/innodb-parameters.html#sysvar_innodb_buffer_pool_size) \(英文\)，以深入了解此參數。

|定價層|**vCore(s)**|**記憶體大小 (GiB)**|**預設值 (位元組)**|**最小值 (位元組)**|**最大值 (位元組)**|
|---|---|---|---|---|---|
|高載 (B1s) |1|1|134217728|33554432|134217728|
|高載 (B1ms) |1|2|536870912|134217728|536870912|
|高載|2|4|2147483648|134217728|2147483648|
|一般用途|2|8|6442450944|134217728|6442450944|
|一般用途|4|16|12884901888|134217728|12884901888|
|一般用途|8|32|25769803776|134217728|25769803776|
|一般用途|16|64|51539607552|134217728|51539607552|
|一般用途|32|128|103079215104|134217728|103079215104|
|一般用途|48|192|154618822656|134217728|154618822656|
|一般用途|64|256|206158430208|134217728|206158430208|
|記憶體最佳化|2|16|12884901888|134217728|12884901888|
|記憶體最佳化|4|32|25769803776|134217728|25769803776|
|記憶體最佳化|8|64|51539607552|134217728|51539607552|
|記憶體最佳化|16|128|103079215104|134217728|103079215104|
|記憶體最佳化|32|256|206158430208|134217728|206158430208|
|記憶體最佳化|48|384|309237645312|134217728|309237645312|
|記憶體最佳化|64|504|405874409472|134217728|405874409472|

### <a name="innodb_file_per_table"></a>innodb_file_per_table

MySQL 會根據在建立資料表期間所提供的設定，將 InnoDB 資料表儲存在不同的資料表空間中。 [系統資料表空間](https://dev.mysql.com/doc/refman/5.7/en/innodb-system-tablespace.html) \(英文\) 是 InnoDB 資料字典的儲存區域。 [file-per-table 資料表空間](https://dev.mysql.com/doc/refman/5.7/en/innodb-file-per-table-tablespaces.html) \(英文\) 包含單一 InnoDB 資料表的資料和索引，且會獨自儲存在檔案系統的資料檔中。 這個行為是由 `innodb_file_per_table` 伺服器參數所控制。 將 `innodb_file_per_table` 設定為 `OFF` 會導致 InnoDB 在系統資料表空間中建立資料表。 否則，InnoDB 會在 file-per-table 資料表空間中建立資料表。

適用於 MySQL 的 Azure 資料庫彈性的伺服器在單一資料檔案中最大可支援 **4 TB** 。 如果您的資料庫大小超過 4 TB，您應該在 [innodb_file_per_table](https://dev.mysql.com/doc/refman/5.7/en/innodb-parameters.html#sysvar_innodb_file_per_table) 表空間中建立資料表。 如果您的單一資料表大小超過 4 TB，您應該使用資料分割資料表。

### <a name="max_connections"></a>max_connections

Max_connection 的值是由伺服器的記憶體大小所決定。 

|定價層|**vCore(s)**|**記憶體大小 (GiB)**|**預設值**|**最小值**|**最大值**|
|---|---|---|---|---|---|
|高載 (B1s) |1|1|85|10|171|
|高載 (B1ms) |1|2|171|10|341|
|高載|2|4|341|10|683|
|一般用途|2|8|683|10|1365|
|一般用途|4|16|1365|10|2731|
|一般用途|8|32|2731|10|5461|
|一般用途|16|64|5461|10|10923|
|一般用途|32|128|10923|10|21845|
|一般用途|48|192|16384|10|32768|
|一般用途|64|256|21845|10|43691|
|記憶體最佳化|2|16|1365|10|2731|
|記憶體最佳化|4|32|2731|10|5461|
|記憶體最佳化|8|64|5461|10|10923|
|記憶體最佳化|16|128|10923|10|21845|
|記憶體最佳化|32|256|21845|10|43691|
|記憶體最佳化|48|384|32768|10|65536|
|記憶體最佳化|64|504|43008|10|86016|

當連線超過限制時，則可能會收到下列錯誤：
> 錯誤 1040 (08004)：太多連線

> [!IMPORTANT]
> 為了獲得最佳體驗，建議您使用連線共用器 (例如 ProxySQL)，有效率地管理連線。

建立 MySQL 的新用戶端連線需要一段時間，且在建立之後，這些連線會佔用資料庫資源，即使閒置時也一樣。 大部分應用程式會要求許多短期連線，這會加重這種情況。 結果會減少實際工作負載的可用資源，因而導致效能降低。 減少閒置連線並重複使用現有連線的連接共用器，有助於避免這種情況。 若要了解如何設定 ProxySQL，請前往[部落格文章](https://techcommunity.microsoft.com/t5/azure-database-for-mysql/load-balance-read-replicas-using-proxysql-in-azure-database-for/ba-p/880042) \(英文\)。

>[!Note]
>ProxySQL 是開放原始碼的社區工具。 Microsoft 會以最大的努力來支援它。 為了取得具有授權指導的生產環境支援，您可以評估並與 [ProxySQL 產品支援人員](https://proxysql.com/services/support/)聯繫。

### <a name="innodb_strict_mode"></a>innodb_strict_mode

如果您收到類似「資料列大小太大 ( # A0 8126) 」的錯誤，您可能會想要關閉參數 **innodb_strict_mode** 。 不允許在伺服器層級全域修改伺服器參數 **innodb_strict_mode** ，因為如果資料列資料大小大於8k，資料將會被截斷而不會發生錯誤，這可能會導致資料遺失。 建議您修改架構以符合頁面大小的限制。 

您可以使用，在工作階段層級設定這個參數 `init_connect` 。 若要在工作階段層級設定 **innodb_strict_mode** ，請參閱 [未列出的設定參數](./how-to-configure-server-parameters-portal.md#setting-non-modifiable-server-parameters)。

> [!NOTE]
> 如果您有讀取複本伺服器，請在來源伺服器的工作階段層級將 **innodb_strict_mode** 設定為 OFF，將會中斷複寫。 如果您有讀取的複本，建議您將參數設定為 [關閉]。

### <a name="time_zone"></a>time_zone

在初始部署時，適用于 MySQL 的 Azure 彈性伺服器會包含時區資訊的系統資料表，但不會填入這些資料表。 時區資料表可藉由從 MySQL 命令列或 MySQL Workbench 等工具呼叫 `mysql.az_load_timezone` 預存程序來填入。 請參閱 [Azure 入口網站](./how-to-configure-server-parameters-portal.md#working-with-the-time-zone-parameter)或 [Azure CLI](./how-to-configure-server-parameters-cli.md#working-with-the-time-zone-parameter) 文章，以了解如何呼叫預存程序，以及設定全域或工作階段層級的時區。

## <a name="non-modifiable-server-parameters"></a>不可修改的伺服器參數

Azure 入口網站上的伺服器參數分頁會顯示可修改和不可修改的伺服器參數。 不可修改的伺服器參數會呈現灰色。如果您想要在工作階段層級設定不可修改的伺服器參數，請參閱 [Azure 入口網站](./how-to-configure-server-parameters-portal.md#setting-non-modifiable-server-parameters) 或 [Azure CLI](./how-to-configure-server-parameters-cli.md#setting-non-modifiable-server-parameters) 文章，以使用來設定連接層級的參數 `init_connect` 。

## <a name="next-steps"></a>後續步驟

- 如何設定 [Azure 入口網站中的伺服器參數](./how-to-configure-server-parameters-portal.md)
- 如何 [在 Azure CLI 中設定伺服器參數](./how-to-configure-server-parameters-cli.md)
