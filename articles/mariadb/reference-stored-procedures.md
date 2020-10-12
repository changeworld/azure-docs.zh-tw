---
title: 管理預存程式-適用於 MariaDB 的 Azure 資料庫
description: 瞭解適用於 MariaDB 的 Azure 資料庫中的預存程式對於協助您設定資料複寫、設定時區和終止查詢有何説明。
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: 453cb28b3053ee2fd2706a5537dc71b6cdca4174
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "91539838"
---
# <a name="azure-database-for-mariadb-management-stored-procedures"></a>適用於 MariaDB 的 Azure 資料庫管理預存程式

適用於 MariaDB 的 Azure 資料庫伺服器上可以使用預存程式來協助管理您的適用于 mariadb 伺服器。 這包括管理伺服器的連線、查詢及設定資料輸入複寫。  

## <a name="data-in-replication-stored-procedures"></a>資料輸入複寫預存程式

資料輸入複寫可讓您將來自在內部部署執行的 MariaDB 伺服器、虛擬機器中或由其他雲端提供者所代管的資料庫服務的資料，同步處理到適用於 MariaDB 的 Azure 資料庫服務。

下列預存程式可用來設定或移除來源與複本之間的資料輸入複寫。

|**預存程序名稱**|**輸入參數**|**輸出參數**|**使用方式注意事項**|
|-----|-----|-----|-----|
|*mysql.az_replication_change_master*|master_host<br/>master_user<br/>master_password<br/>master_port<br/>master_log_file<br/>master_log_pos<br/>master_ssl_ca|N/A|若要使用 SSL 模式傳送資料，請將 CA 憑證的內容傳入 master_ssl_ca 參數。 </br><br>如果不使用 SSL 傳輸資料，將空字串傳入 master_ssl_ca 參數。|
|*mysql.az_replication _start*|N/A|N/A|開始複寫。|
|*mysql.az_replication _stop*|N/A|N/A|停止複寫。|
|*mysql.az_replication _remove_master*|N/A|N/A|移除來源與複本之間的複寫關聯性。|
|*mysql.az_replication_skip_counter*|N/A|N/A|略過一個複寫錯誤。|

若要在適用於 MariaDB 的 Azure 資料庫中的來源和複本之間設定資料輸入複寫，請參閱 [如何設定資料輸入複寫](howto-data-in-replication.md)。

## <a name="other-stored-procedures"></a>其他已儲存的程序

下列預存程式可在適用於 MariaDB 的 Azure 資料庫中用來管理您的伺服器。

|**預存程序名稱**|**輸入參數**|**輸出參數**|**使用方式注意事項**|
|-----|-----|-----|-----|
|*mysql.az_kill*|processlist_id|N/A|相當於 [`KILL CONNECTION`](https://dev.mysql.com/doc/refman/8.0/en/kill.html) 命令。 終止連接所執行的任何語句之後，將會終止與所提供 processlist_id 相關聯的連接。|
|*mysql.az_kill_query*|processlist_id|N/A|相當於 [`KILL QUERY`](https://dev.mysql.com/doc/refman/8.0/en/kill.html) 命令。 將會終止目前正在執行之連接的語句。 讓連接本身保持運作。|
|*mysql.az_load_timezone*|N/A|N/A|載入時區資料表，以允許將 `time_zone` 參數設定為命名值 (例如 "US/太平洋" ) 。|

## <a name="next-steps"></a>後續步驟
- 瞭解如何設定 [資料輸入複寫](howto-data-in-replication.md)
- 瞭解如何使用時區 [資料表](howto-server-parameters.md#working-with-the-time-zone-parameter)