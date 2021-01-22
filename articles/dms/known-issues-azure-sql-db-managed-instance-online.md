---
title: 線上遷移至 Azure SQL 受控執行個體的已知問題和限制
description: 瞭解與 Azure SQL 受控執行個體線上遷移相關的已知問題/遷移限制。
services: database-migration
author: pochiraju
ms.author: rajpo
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: mvc
ms.topic: troubleshooting
ms.date: 02/20/2020
ms.openlocfilehash: 46c5f5995c7a1d4eb074f6c1b25ecaad7e2da37e
ms.sourcegitcommit: 77afc94755db65a3ec107640069067172f55da67
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/22/2021
ms.locfileid: "98695523"
---
# <a name="known-issuesmigration-limitations-with-online-migrations-to-azure-sql-managed-instance"></a>線上遷移至 Azure SQL 受控執行個體的已知問題/遷移限制

從 SQL Server 至 Azure SQL 受控執行個體的線上遷移相關的已知問題和限制如下所述。

> [!IMPORTANT]
> 將 SQL Server 的線上遷移至 Azure SQL Database 時，不支援 SQL_variant 資料類型的遷移。

## <a name="backup-requirements"></a>備份需求

- **具有總和檢查碼的備份**

    Azure 資料庫移轉服務會使用備份和還原方法，將您的內部部署資料庫移轉至 SQL 受控執行個體。 Azure 資料庫移轉服務僅支援使用總和檢查碼建立的備份。

    [在備份或還原期間啟用或停用備份總和檢查碼 (SQL Server) ](/sql/relational-databases/backup-restore/enable-or-disable-backup-checksums-during-backup-or-restore-sql-server)。

    > [!NOTE]
    > 如果您使用壓縮進行資料庫備份，除非明確停用，否則總和檢查碼會是預設行為。

    使用離線遷移時，如果您選擇 [ **我要讓 Azure 資料庫移轉服務**]，則 Azure 資料庫移轉服務將會進行資料庫備份，並啟用總和檢查碼選項。

- **備份媒體**

    請務必在不同的備份媒體上進行每次備份， (備份檔案) 。 Azure 資料庫移轉服務不支援附加到單一備份檔案的備份。 進行完整備份和記錄備份，以分隔備份檔案。

## <a name="data-and-log-file-layout"></a>資料檔案和記錄檔配置

- **記錄檔數目**

    Azure 資料庫移轉服務不支援具有多個記錄檔的資料庫。 如果您有多個記錄檔，請將它們壓縮並重新組織成單一交易記錄檔。 因為您無法從遠端連線到不是空的記錄檔，所以您必須先備份記錄檔。

## <a name="sql-server-features"></a>SQL Server 功能

- **FileStream/Filetable**

    SQL 受控執行個體目前不支援 FileStream 和 Filetable。 針對相依于這些功能的工作負載，建議您選擇在 Azure Vm 上執行的 SQL Server 做為您的 Azure 目標。

- **記憶體內部資料表**

    記憶體內部 OLTP 適用于 SQL 受控執行個體的 Premium 和業務關鍵層級;一般用途層不支援記憶體內部 OLTP。

## <a name="migration-resets"></a>遷移重設

- **部署**

    SQL 受控執行個體是具有自動修補和版本更新的 PaaS 服務。 在您的 SQL 受控執行個體的遷移期間，會保留最多36小時的非重大更新。 之後 (和重大更新) ，如果遷移中斷，進程會重設為完整還原狀態。

    只有在還原完整備份並趕上所有記錄備份之後，才能呼叫遷移轉換。 如果您的生產遷移切換受到影響，請聯繫 [AZURE DMS 意見反應別名](mailto:dmsfeedback@microsoft.com)。

## <a name="smb-file-share-connectivity"></a>SMB 檔案共用連線能力

連接到 SMB 檔案共用的問題很可能是因為許可權問題所造成。 

若要測試 SMB 檔案共用連線能力，請遵循下列步驟： 

1. 將備份儲存至 SMB 檔案共用。 
1. 確認 Azure 資料庫移轉服務的子網和來源 SQL Server 之間的網路連線能力。 若要這麼做，最簡單的方式是將 SQL Server 虛擬機器部署到 DMS 子網，並使用 SQL Server Management Studio 連接到來源 SQL Server。 
1. 從檔案共用上的備份還原來源 SQL Server 上的標頭： 

   ```sql
   RESTORE HEADERONLY   
   FROM DISK = N'\\<SMB file share path>\full.bak'
   ```

如果您無法連接到檔案共用，請使用下列步驟來設定許可權： 

1. 使用檔案總管流覽至您的檔案共用。 
1. 以滑鼠右鍵按一下檔案共用，然後選取 [屬性]。 
1. 選擇 [ **共用** ] 索引標籤，然後選取 [ **Advanced 共用**]。 
1. 新增用於遷移的 Windows 帳戶，並將它指派給它「完全控制」存取。 
1. 新增 SQL Server 服務帳戶，並將它指派為「完全控制」存取權。 如果您不確定要使用哪一個帳戶，請檢查 SQL Server 服務帳戶的 **SQL Server 組態管理員** 。 

   :::image type="content" source="media/known-issues-azure-sql-db-managed-instance-online/assign-fileshare-permissions.png" alt-text="提供用於遷移和 SQL Server 服務帳戶之 Windows 帳戶的完整控制存取權。 ":::

