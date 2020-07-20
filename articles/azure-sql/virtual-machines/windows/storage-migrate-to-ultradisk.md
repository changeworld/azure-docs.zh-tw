---
title: 將記錄磁片遷移至 Ultra 磁片
description: 瞭解如何將 Azure 虛擬機器上的 SQL Server (VM) 記錄磁片遷移至 Azure Ultradisk，以利用高效能和低延遲。
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
editor: ''
tags: azure-service-management
ms.assetid: ''
ms.service: virtual-machines-sql
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 07/09/2020
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: f5a2205ec835fb630933dd85b4b0e5846ae864cb
ms.sourcegitcommit: f7e160c820c1e2eb57dc480b2a8fd6bef7053e91
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/10/2020
ms.locfileid: "86235890"
---
# <a name="migrate-log-disk-to-ultra-disk"></a>將記錄磁片遷移至 Ultra 磁片
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

Azure ultra 磁片可針對 Azure 虛擬機器 (VM) 上的 SQL Server，提供高輸送量、高 IOPS 以及一致的低延遲磁片儲存體。 

本文說明如何將您的記錄磁片遷移至 ultra SSD，以利用 ultra 磁片所提供的效能優勢。 

## <a name="back-up-database"></a>備份資料庫

完成資料庫的[完整備份](backup-restore.md)。 

## <a name="attach-disk"></a>連接磁碟

一旦您在 VM 上啟用 ultradisk 相容性，請將 Ultra SSD 連接至虛擬機器。 

虛擬機器大小和區域的子集支援 Ultra 磁片。 在繼續之前，請驗證您的 VM 位於支援 ultra 磁片的區域、區域和大小。 您可以使用 Azure CLI 或 PowerShell 來[判斷和驗證 VM 大小和區域](../../../virtual-machines/windows/disks-enable-ultra-ssd.md#determine-vm-size-and-region-availability)。 

### <a name="enable-compatibility"></a>啟用相容性

若要啟用相容性，請遵循下列步驟：

1. 在[Azure 入口網站](https://portal.azure.com/)中，移至您的虛擬機器。 
1. 停止/解除配置虛擬機器。 
1. 選取 [**設定**] 底下的 [**磁片**]，然後選取 [**其他設定**]。 

   :::image type="content" source="media/storage-migrate-to-ultradisk/additional-disks-settings-azure-portal.png" alt-text="在 Azure 入口網站的 [設定] 下，選取 [磁片] 的其他設定":::

1. 選取 **[是]** 以**啟用 Ultra 磁片相容性**。 

   :::image type="content" source="../../../../includes/media/virtual-machines-disks-getting-started-ultra-ssd/ultra-options-yes-enable.png" alt-text="在 Azure 入口網站的 [設定] 下，選取 [磁片] 的其他設定":::

1. 選取 [儲存]。 



### <a name="attach-disk"></a>連接磁碟

使用 Azure 入口網站將 ultra 磁片連結至您的虛擬機器。 如需詳細資訊，請參閱[連接 ultra 磁片](../../../virtual-machines/windows/disks-enable-ultra-ssd.md#attach-an-ultra-disk-using-the-azure-portal)

連接磁片之後，使用 Azure 入口網站再次啟動您的 VM。 



## <a name="format-disk"></a>格式化磁片

連接到您的虛擬機器，並格式化您的 ultra 磁片。  

若要格式化您的 ultra 磁片，請遵循下列步驟：

1. 使用遠端桌面通訊協定 (RDP) 連接到您的 VM。
1. 使用 [[磁片管理](/windows-server/storage/disk-management/overview-of-disk-management)] 來格式化和分割新連接的 ultra 磁片。 


## <a name="use-disk-for-log"></a>使用磁片進行記錄

設定 SQL Server 以使用新的記錄磁片磁碟機。 您可以使用 Transact-sql (T-sql) 或 SQL Server Management Studio (SSMS) ）來執行此操作。 SQL Server 服務帳戶所使用的帳戶必須具有新記錄檔位置的完全控制權。 

### <a name="configure-permissions"></a>設定權限

1. 確認 SQL Server 所使用的服務帳戶。 您可以使用 SQL Server 組態管理員或 services.msc 來執行這項操作。
1. 流覽至您的新磁片。 
1. 建立一個資料夾 (或多個要用於記錄檔的資料夾) 。 
1. 以滑鼠右鍵按一下該資料夾，然後選取 [屬性]****。
1. 在 [**安全性**] 索引標籤上，將 [完全控制] 存取權授與 SQL Server 服務帳戶。 
1. 選取 **[確定]** 以儲存您的設定。 
1. 針對您打算擁有 SQL 資料的每個根層級資料夾重複此步驟。 

### <a name="use-new-log-drive"></a>使用新的記錄磁片磁碟機 

授與許可權之後，請使用 Transact-sql (T-sql) 或 SQL Server Management Studio (SSMS) 來卸離資料庫，並將現有的記錄檔移至新的位置。

   > [!CAUTION]
   > 卸離資料庫會使其離線、關閉連接，以及回復任何正在進行中的交易。 請小心，並在停機時間維護期間繼續進行。 



# <a name="transact-sql-t-sql"></a>[Transact-SQL (T-SQL)](#tab/tsql)

使用 T-sql 將現有的檔案移至新的位置：

1. 在 SQL Server Management Studio 中連接到您的資料庫，然後開啟**新的查詢**視窗。 
1. 取得現有的檔案和位置：

   ```sql
   USE AdventureWorks
   GO

   sp_helpfile
   GO
   ```

1. 卸離資料庫： 

   ```sql
   USE master
   GO

   sp_detach_db 'AdventureWorks'
   GO
   ```

1. 使用 [檔案瀏覽器] 將記錄檔移至 ultra 磁片上的新位置。 

1. 附加資料庫，並指定新的檔案位置： 

   ```sql
    sp_attach_db 'AdventureWorks'
   'E:\Fixed_FG\AdventureWorks.mdf',
   'E:\Fixed_FG\AdventureWorks_2.ndf',
   'F:\New_Log\AdventureWorks_log.ldf'
   GO
   ```

此時，資料庫會與記錄在新的位置上線。 



# <a name="sql-server-management-studio-ssms"></a>[SQL Server Management Studio (SSMS)](#tab/ssms)

使用 SSMS 將現有的檔案移至新的位置：

1. 在 SQL Server Management Studio (SSMS) 中連接到您的資料庫。 
1. 以滑鼠右鍵按一下資料庫 **，選取 [****屬性**]，然後選取 [檔案]。 
1. 記下現有檔案的路徑。 
1. 選取 [確定]**** 關閉對話方塊。 
1. 以滑鼠右鍵按一下資料庫，然後**選取 [** 工作] [卸  >  **離**]。 
1. 依照嚮導來卸離資料庫。 
1. 使用 [檔案瀏覽器] 手動將記錄檔移至新的位置。
1. 在 SQL Server Management Studio 中附加資料庫
   1. 以滑鼠右鍵按一下**物件總管**中的 [**資料庫**]，然後選取 [**附加資料庫**]。 
   1. 使用對話方塊，在新的位置加入每個檔案，包括記錄檔。 
   1. 選取 **[確定]** 以附加資料庫。 

此時，資料庫會與記錄在新的位置上線。

---


## <a name="next-steps"></a>後續步驟

請參閱[效能最佳作法](performance-guidelines-best-practices.md)以取得其他設定，以改善效能。 

如需 Azure 虛擬機器上 SQL Server 的總覽，請參閱下列文章：

- [Windows VM 上的 SQL Server 概觀](sql-server-on-azure-vm-iaas-what-is-overview.md)
- [Linux VM 上的 SQL Server 概觀](../linux/sql-server-on-linux-vm-what-is-iaas-overview.md)
