---
title: 連線遷移到 Azure SQL 資料庫託管實例的已知問題和限制
description: 瞭解與連線遷移到 Azure SQL 資料庫託管實例相關的已知問題/遷移限制。
services: database-migration
author: pochiraju
ms.author: rajpo
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: mvc
ms.topic: article
ms.date: 02/20/2020
ms.openlocfilehash: 88e2b5894686ee93caecf33e04940803eb75f394
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "77648660"
---
# <a name="known-issuesmigration-limitations-with-online-migrations-to-azure-sql-database-managed-instance"></a>連線遷移到 Azure SQL 資料庫託管實例時已知的問題/遷移限制

下面將介紹與從 SQL Server 到 Azure 資料庫託管實例的線上遷移關聯的已知問題和限制。

> [!IMPORTANT]
> 使用 SQL Server 到 Azure SQL 資料庫的線上遷移，不支援遷移SQL_variant資料類型。

## <a name="backup-requirements"></a>備份要求

- **具有校驗和的備份**

    Azure 資料庫移轉服務使用備份和還原方法將本機資料庫遷移到 SQL 資料庫託管實例。 Azure 資料庫移轉服務僅支援使用校驗和創建的備份。

    [在備份或還原期間啟用或禁用備份校驗和 （SQL 伺服器）](https://docs.microsoft.com/sql/relational-databases/backup-restore/enable-or-disable-backup-checksums-during-backup-or-restore-sql-server?view=sql-server-2017)

    > [!NOTE]
    > 如果使用壓縮進行資料庫備份，則除非顯式禁用，否則校驗和是預設行為。

    對於離線遷移，如果選擇 **"我將允許 Azure 資料庫移轉服務..."，** 則 Azure 資料庫移轉服務將在啟用校驗和選項後進行資料庫備份。

- **備份媒體**

    請確保在單獨的備份介質（備份檔案）上執行每個備份。 Azure 資料庫移轉服務不支援追加到單個備份檔案的備份。 獲取完整備份和記錄備份以分離備份檔案。

## <a name="data-and-log-file-layout"></a>資料和日誌檔佈局

- **日誌檔數**

    Azure 資料庫移轉服務不支援具有多個日誌檔的資料庫。 如果有多個日誌檔，請收縮它們並將其重新組織到單個事務日誌檔中。 由於無法遠端顯示不為空的日誌檔，因此需要首先備份日誌檔。

## <a name="sql-server-features"></a>SQL Server 功能

- **檔流/檔表**

    SQL 資料庫託管實例當前不支援檔流和檔表。 對於依賴于這些功能的工作負載，我們建議您選擇在 Azure VM 上運行的 SQL 伺服器作為 Azure 目標。

- **記憶體內部資料表**

    記憶體中 OLTP 在 SQL 資料庫託管實例的高級和業務關鍵層中可用;通用層不支援記憶體中 OLTP。

## <a name="migration-resets"></a>遷移重置

- **部署**

    SQL 資料庫託管實例是具有自動修補和版本更新的 PaaS 服務。 在 SQL 資料庫託管實例的遷移期間，非重大更新最多有助於 36 小時。 之後（對於重大更新），如果遷移中斷，進程將重置為完全還原狀態。

    只有在還原完整備份並趕上所有記錄備份後，才能調用遷移截止時間。 如果生產遷移截止點受到影響，請與 Azure [DMS 回饋別名](mailto:dmsfeedback@microsoft.com)聯繫。
