---
title: 管理預存程序 - MySQL 的 Azure 資料庫
description: 瞭解 MySQL Azure 資料庫中的哪些預存程序可用於説明您配置資料內複製、設置時區和終止查詢。
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: 6a3fa40eaae174d3616fd0318f81576b7c59eac7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80067707"
---
# <a name="azure-database-for-mysql-management-stored-procedures"></a>用於 MySQL 管理的預存程序的 Azure 資料庫

MySQL 伺服器的 Azure 資料庫上提供了預存程序，以説明管理 MySQL 伺服器。 這包括管理伺服器的連接、查詢和設置資料內複製。  

## <a name="data-in-replication-stored-procedures"></a>資料內複製預存程序

資料帶入複寫可讓您將來自在內部部署執行的 MySQL 伺服器、虛擬機器中或由其他雲端提供者所代管的資料庫服務的資料，同步處理到適用於 MySQL 的 Azure 資料庫服務。

下列預存程序可用來設定或移除主體和複本之間的資料輸入複寫。

|**預存程序名稱**|**輸入參數**|**輸出參數**|**使用方式注意事項**|
|-----|-----|-----|-----|
|*mysql.az_replication_change_master*|master_host<br/>master_user<br/>master_password<br/>master_port<br/>master_log_file<br/>master_log_pos<br/>master_ssl_ca|N/A|要使用 SSL 模式傳輸資料，應將 CA 憑證的上下文傳遞到master_ssl_ca參數中。 </br><br>如果不使用 SSL 傳輸資料，將空字串傳入 master_ssl_ca 參數。|
|*mysql.az_replication _start*|N/A|N/A|開始複寫。|
|*mysql.az_replication _stop*|N/A|N/A|停止複寫。|
|*mysql.az_replication _remove_master*|N/A|N/A|移除主體與複本之間的複寫關聯性。|
|*mysql.az_replication_skip_counter*|N/A|N/A|略過一個複寫錯誤。|

要在 MySQL Azure 資料庫中的主資料庫和副本之間設置資料內複製，請參閱[如何配置資料內複製](howto-data-in-replication.md)。

## <a name="other-stored-procedures"></a>其他已儲存的程序

以下預存程序在 Azure 資料庫中可用，可供 MySQL 管理伺服器。

|**預存程序名稱**|**輸入參數**|**輸出參數**|**使用方式注意事項**|
|-----|-----|-----|-----|
|*mysql.az_kill*|processlist_id|N/A|等效于[`KILL CONNECTION`](https://dev.mysql.com/doc/refman/8.0/en/kill.html)命令。 將在終止連接正在執行的任何語句後終止與提供的processlist_id關聯的連接。|
|*mysql.az_kill_query*|processlist_id|N/A|等效于[`KILL QUERY`](https://dev.mysql.com/doc/refman/8.0/en/kill.html)命令。 將終止連接當前正在執行的語句。 使連接本身保持活動狀態。|
|*mysql.az_load_timezone*|N/A|N/A|載入時區表以允許將`time_zone`參數設置為命名值（例如。 "美國/太平洋"）。|

## <a name="next-steps"></a>後續步驟
- 瞭解如何設置[資料內複製](howto-data-in-replication.md)
- 瞭解如何使用[時區表](howto-server-parameters.md#working-with-the-time-zone-parameter)
