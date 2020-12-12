---
title: 如何使用 Azure 儲存體進行 SQL Server 備份和還原 |Microsoft Docs
description: 了解如何將 SQL Server 備份到「Azure 儲存體」。 說明將 SQL 資料庫備份到「Azure 儲存體」的好處。
services: virtual-machines-windows
documentationcenter: ''
author: MikeRayMSFT
tags: azure-service-management
ms.assetid: 0db7667d-ef63-4e2b-bd4d-574802090f8b
ms.service: virtual-machines-sql
ms.subservice: backup
ms.topic: conceptual
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 01/31/2017
ms.author: mathoma
ms.openlocfilehash: b4100800385792557358d3fb6438f52650483f89
ms.sourcegitcommit: dfc4e6b57b2cb87dbcce5562945678e76d3ac7b6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/12/2020
ms.locfileid: "97359782"
---
# <a name="use-azure-storage-for-sql-server-backup-and-restore"></a>使用 Azure 儲存體進行 SQL Server 備份和還原
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

從 SQL Server 2012 SP1 CU2 開始，您現在可以直接將 SQL Server 備份寫入 Azure Blob 儲存體。 您可以使用此功能來備份至 Azure Blob 儲存體和 SQL Server 資料庫，並從中還原。 備份至雲端可提供可用性、無限制的異地複寫異地儲存體，以及輕鬆地將資料移轉到雲端的優點。 您可以使用 Transact-SQL 或 SMO 發出 BACKUP 或 RESTORE 陳述式。

## <a name="overview"></a>概觀
SQL Server 2016 導入了新功能；您可以使用 [檔案快照集備份](/sql/relational-databases/backup-restore/file-snapshot-backups-for-database-files-in-azure) 來執行幾乎即時的備份與非常快速的還原。

本主題說明為何您可能會選擇使用 Azure 儲存體進行 SQL Server 備份，然後說明相關的元件。 您可以使用本文結尾提供的資源來存取解說和其他資訊，以開始使用此服務搭配您的 SQL Server 備份。

## <a name="benefits-of-using-azure-blob-storage-for-sql-server-backups"></a>使用 Azure Blob 儲存體進行 SQL Server 備份的優點
備份 SQL Server 時，您會面臨數個挑戰。 這些挑戰包括儲存體管理、儲存體故障風險、異地儲存體存取以及硬體組態。 其中許多挑戰都是使用 Azure Blob 儲存體進行 SQL Server 備份來解決。 請考量下列好處：

* **容易使用**：將您的備份存放在 Azure Blob 中可以是一個既方便、有彈性又容易存取的異地選項。 為您的 SQL Server 備份建立異地儲存體相當簡單，只需將現有的指令碼/工作修改成使用 **BACKUP TO URL** 語法即可。 異地儲存體通常應該遠離實際執行資料庫位置，防止單一災害同時影響異地和實際執行資料庫位置。 藉由選擇 [異地複寫 Azure Blob](../../../storage/common/storage-redundancy.md)，在發生可能影響整個區域的災害時，您會有額外的一層保護。
* **備份** 封存： Azure Blob 儲存體提供較好的替代方案，可讓您用來封存備份的常用磁帶選項。 磁帶儲存體可能需要實際運輸至異地設施並採取措施來保護媒體。 將您的備份儲存在 Azure Blob 儲存體中，可提供立即、高可用性且持久的封存選項。
* **受控硬體**：使用 Azure 服務時，沒有硬體管理的額外負荷。 Azure 服務會管理硬體，並提供地埋區域備援複寫，以提供備援及硬體故障的防護。
* **無限制的儲存體**：藉由啟用直接備份到 Azure Blob 的功能，您可以存取幾乎無限制的儲存體。 或者，您也可以選擇備份到 Azure 虛擬機器磁碟，所受的限制會取決於機器大小。 您可以連結到 Azure 虛擬機器以進行備份的磁碟數是有限制的。 超大執行個體的限制為 16 個磁碟，而較小執行個體的限制則更低。
* **備份可用性**：儲存在 Azure Blob 中的備份可隨時從任何位置取得，而且可以輕易地存取以還原至 SQL Server 執行個體，而不需要資料庫連結/中斷連結或下載並連結 VHD。
* **成本**：只有使用的服務才要付費。 成為符合成本效益的異地與備份封存選項。 如需詳細資訊，請參閱 [Azure 定價計算機](https://go.microsoft.com/fwlink/?LinkId=277060 "定價計算機")和 [Azure 定價文章](https://go.microsoft.com/fwlink/?LinkId=277059 "定價文章")。
* **儲存體快照集**：當資料庫檔案是存放在 Azure Blob 中且您使用的是 SQL Server 2016 時，您可以使用 [檔案快照集備份](/sql/relational-databases/backup-restore/file-snapshot-backups-for-database-files-in-azure) 來執行幾乎即時的備份與非常快速的還原。

如需詳細資訊，請參閱 [使用 Azure Blob 儲存體 SQL Server 備份和還原](/sql/relational-databases/backup-restore/sql-server-backup-and-restore-with-microsoft-azure-blob-storage-service)。

下列兩節將介紹 Azure Blob 儲存體，包括必要的 SQL Server 元件。 請務必瞭解元件及其互動，才能成功地從 Azure Blob 儲存體使用備份和還原。

## <a name="azure-blob-storage-components"></a>Azure Blob 儲存體元件
備份至 Azure Blob 儲存體時，會使用下列 Azure 元件。

| 元件 | 描述 |
| --- | --- |
| **儲存體帳戶** |儲存體帳戶是所有儲存體服務的起點。 若要存取 Azure Blob 儲存體，請先建立 Azure 儲存體帳戶。 如需 Azure Blob 儲存體的詳細資訊，請參閱 [如何使用 Azure blob 儲存體](https://azure.microsoft.com/develop/net/how-to-guides/blob-storage/)。 |
| **容器** |容器可提供一組 Blob 的分組，並可存放不限數目的 Blob。 若要將 SQL Server 備份寫入 Azure Blob 儲存體，您至少必須建立根容器。 |
| **Blob** |任何類型和大小的檔案。 可使用下列 URL 格式來定址 Blob：**https://[storage account].blob.core.windows.net/[container]/[blob]** 。 如需有關分頁 Blob 的詳細資訊，請參閱 [了解區塊 Blob 和分頁 Blob](/rest/api/storageservices/Understanding-Block-Blobs--Append-Blobs--and-Page-Blobs) |

## <a name="sql-server-components"></a>SQL Server 元件
備份至 Azure Blob 儲存體時，會使用下列 SQL Server 元件。

| 元件 | 描述 |
| --- | --- |
| **URL** |URL 會指定唯一備份檔案的統一資源識別項 (URI)。 URL 用來提供 SQL Server 備份檔的位置和名稱。 此 URL 必須指向實際的 Blob，而非只有容器。 如果 Blob 不存在，就會建立 Blob。 如果指定現有的 Blob，則 BACKUP 會失敗，除非指定了 > WITH FORMAT 選項。 以下是您會在 BACKUP 命令中指定的 URL 範例： **http[s]://[storageaccount].blob.core.windows.net/[container]/[FILENAME.bak]** 。 建議使用 HTTPS，但並非必要。 |
| **認證** |連接和驗證 Azure Blob 儲存體所需的資訊會儲存為認證。 為了讓 SQL Server 將備份寫入 Azure Blob 或從 Azure Blob 還原，必須建立 SQL Server 認證。 如需詳細資訊，請參閱 [SQL Server 認證](/sql/t-sql/statements/create-credential-transact-sql)。 |

> [!NOTE]
> SQL Server 2016 已更新為支援區塊 blob。 如需詳細資料，請參閱 [教學課程：使用 Microsoft Azure Blob 儲存體搭配 SQL Server 2016 資料庫](/sql/relational-databases/tutorial-use-azure-blob-storage-service-with-sql-server-2016) 。
> 
> 

## <a name="next-steps"></a>後續步驟
1. 建立 Azure 訂用帳戶 (如果您還沒有帳戶的話)。 如果您正在評估 Azure，請考慮使用 [免費試用版](https://azure.microsoft.com/free/)。
2. 接著，完成下列其中一個教學課程，這些教學課程會逐步引導您建立儲存體帳戶及執行還原。
   
   * **SQL Server 2014**： [教學課程： SQL Server 2014 備份和還原至 Microsoft Azure Blob 儲存體](https://msdn.microsoft.com/library/jj720558\(v=sql.120\).aspx)。
   * **SQL Server 2016**： [教學課程：搭配使用 Microsoft Azure Blob 儲存體與 SQL Server 2016 資料庫](/sql/relational-databases/tutorial-use-azure-blob-storage-service-with-sql-server-2016)
3. 請參閱從 [SQL Server 備份和還原 Microsoft Azure Blob 儲存體](/sql/relational-databases/backup-restore/sql-server-backup-and-restore-with-microsoft-azure-blob-storage-service)開始的其他檔。

如果您有任何問題，請檢閱 [SQL Server 備份至 URL 的最佳做法和疑難排解](/sql/relational-databases/backup-restore/sql-server-backup-to-url-best-practices-and-troubleshooting)主題。

如需其他 SQL Server 備份和還原選項，請參閱 [Azure 虛擬機器上的 SQL Server 備份和還原](backup-restore.md)。