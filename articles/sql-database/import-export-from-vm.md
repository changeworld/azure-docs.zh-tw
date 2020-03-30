---
title: 導入或匯出 SQL 資料庫
description: 導入或匯出 Azure SQL 資料庫，而不允許 Azure 服務訪問伺服器。
services: sql-database
ms.service: sql-database
ms.subservice: migration
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 01/08/2020
ms.openlocfilehash: 9f694f3f0ec740d0a4e8dc4e6bf8845c408802c8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "75897841"
---
# <a name="import-or-export-an-azure-sql-database-without-allowing-azure-services-to-access-the-server"></a>導入或匯出 Azure SQL 資料庫，而無需允許 Azure 服務訪問伺服器

本文介紹如何在 Azure SQL 伺服器上將允許 Azure*服務*設置為*OFF*時導入或匯出 Azure SQL 資料庫。 工作流使用 Azure 虛擬機器運行 SqlPackage 來執行導入或匯出操作。

## <a name="sign-in-to-the-azure-portal"></a>登入 Azure 入口網站

登錄到 Azure[門戶](https://portal.azure.com/)。

## <a name="create-the-azure-virtual-machine"></a>創建 Azure 虛擬機器

通過選擇"**部署到 Azure"按鈕創建 Azure**虛擬機器。

此範本允許您使用 Windows 版本的幾個不同選項，使用最新的修補版本部署簡單的 Windows 虛擬機器。 這將在資源組位置部署 A2 大小的 VM，並返回 VM 的完全限定功能變數名稱。
<br><br>

<a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-vm-simple-windows%2Fazuredeploy.json" target="_blank">
    <img src="https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.png"/>
</a>

有關詳細資訊，請參閱[Windows VM 的非常簡單的部署](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-simple-windows)。


## <a name="connect-to-the-virtual-machine"></a>連接至虛擬機器

以下步驟演示如何使用遠端桌面連線連接到虛擬機器。

1. 部署完成之後，請移至虛擬機器資源。

    ![VM](./media/import-export-from-vm/vm.png)  

2. 選取 [連接]****。

   將顯示遠端桌面通訊協定檔案 (.rdp 檔案) 表單，其中包含虛擬機器的公用 IP 位址與連接埠號碼。

   ![RDP 表單](./media/import-export-from-vm/rdp.png)  

3. 選擇**下載 RDP 檔**。

   > [!NOTE]
   > 您也可以使用 SSH 來連線到您的 VM。

4. 關閉**連線至虛擬機器**表單。
5. 若要連線至您的 VM，請開啟下載的 RDP 檔案。
6. 出現提示時，請選取 [連接]****。 在 Mac 上，您需要 RDP 用戶端，例如來自 Mac App Store 的[遠端桌面用戶端](https://itunes.apple.com/us/app/microsoft-remote-desktop/id715768417?mt=12)。

7. 輸入在建立虛擬機器時指定的使用者名稱和密碼，然後選擇 [確定]****。

8. 您可能會在登入過程中收到憑證警告。 選擇 [是]**** 或 [繼續]**** 以繼續進行連線。



## <a name="install-sqlpackage"></a>安裝 Sql 包

[下載並安裝最新版本的 SqlPackage。](https://docs.microsoft.com/sql/tools/sqlpackage-download)




有關詳細資訊，請參閱[SqlPackage.exe](https://docs.microsoft.com/sql/tools/sqlpackage)。

## <a name="create-a-firewall-rule-to-allow-the-vm-access-to-the-database"></a>創建防火牆規則以允許 VM 訪問資料庫

將虛擬機器的公共 IP 位址添加到 SQL 資料庫伺服器防火牆。

以下步驟為虛擬機器的公共 IP 位址創建伺服器級 IP 防火牆規則，並啟用虛擬機器的連接。

1. 從左側功能表中選擇**SQL 資料庫**，然後在**SQL 資料庫**頁面上選擇資料庫。 資料庫的概述頁將打開，顯示完全限定的伺服器名稱（如**servername.database.windows.net），** 並提供進一步配置的選項。

2. 複製此完全限定的伺服器名稱，以便連接到伺服器及其資料庫時使用。

   ![伺服器名稱](./media/sql-database-get-started-portal/server-name.png)

3. 在工具列上選取 [設定伺服器防火牆]****。 資料庫伺服器的 [防火牆設定]**** 頁面隨即開啟。

   ![伺服器層級 IP 防火牆規則](./media/sql-database-get-started-portal/server-firewall-rule.png)

4. 選擇**在工具列上添加用戶端 IP，** 將虛擬機器的公共 IP 位址添加到新的伺服器級 IP 防火牆規則中。 伺服器層級 IP 防火牆規則可以針對單一 IP 位址或 IP 位址範圍開啟連接埠 1433。

5. 選取 [儲存]****。 為虛擬機器在 SQL 資料庫伺服器上打開端口 1433 的公共 IP 位址創建了伺服器級 IP 防火牆規則。

6. 關閉 [防火牆設定]**** 頁面。



## <a name="export-a-database-using-sqlpackage"></a>使用 SqlPackage 匯出資料庫

若要使用 [SqlPackage](https://docs.microsoft.com/sql/tools/sqlpackage) 命令列公用程式匯出 SQL 資料庫，請參閱[匯出參數和屬性](https://docs.microsoft.com/sql/tools/sqlpackage#export-parameters-and-properties)。 SqlPackage 實用程式附帶最新版本的 SQL [Server 管理工作室](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms)和[SQL 伺服器資料工具](https://docs.microsoft.com/sql/ssdt/download-sql-server-data-tools-ssdt)，或者您可以下載最新版本的[SqlPackage](https://docs.microsoft.com/sql/tools/sqlpackage-download)。

我們建議在大多數生產環境中使用 SqlPackage 實用程式進行擴展和性能。 如需 SQL Server 客戶諮詢小組部落格中有關使用 BACPAC 檔案進行移轉的主題，請參閱[使用 BACPAC 檔案從 SQL Server 移轉至 Azure SQL Database](https://blogs.msdn.microsoft.com/sqlcat/20../../migrating-from-sql-server-to-azure-sql-database-using-bacpac-files/)。

此示例演示如何使用 SqlPackage.exe 使用活動目錄通用身份驗證匯出資料庫。 替換為特定于環境的值。

```cmd
SqlPackage.exe /a:Export /tf:testExport.bacpac /scs:"Data Source=<servername>.database.windows.net;Initial Catalog=MyDB;" /ua:True /tid:"apptest.onmicrosoft.com"
```




## <a name="import-a-database-using-sqlpackage"></a>使用 SqlPackage 導入資料庫

若要使用 [SqlPackage](https://docs.microsoft.com/sql/tools/sqlpackage) 命令列公用程式匯入 SQL Server 資料庫，請參閱[匯入參數和屬性](https://docs.microsoft.com/sql/tools/sqlpackage#import-parameters-and-properties)。 SqlPackage 具有最新的[SQL 伺服器管理工作室](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms)和[SQL 伺服器資料工具](https://docs.microsoft.com/sql/ssdt/download-sql-server-data-tools-ssdt)。 您還可以下載最新版本的[SqlPackage。](https://docs.microsoft.com/sql/tools/sqlpackage-download)

為了規模和效能，我們建議在大部分生產環境中使用 SqlPackage，而不使用 Azure 入口網站。 如需 SQL Server 客戶諮詢小組部落格中有關使用 `BACPAC` 檔案進行移轉的主題，請參閱[使用 BACPAC 檔案從 SQL Server 移轉至 Azure SQL Database](https://blogs.msdn.microsoft.com/sqlcat/2016/10/20/migrating-from-sql-server-to-azure-sql-database-using-bacpac-files/) \(英文\)。

以下 SqlPackage 命令將**AdventureWorks2017**資料庫從本機存放區導入 Azure SQL 資料庫伺服器。 此命令會建立名為 **myMigratedDatabase**、且具有**進階**服務層級和 **P6** 服務目標的新資料庫。 請針對您的環境適當變更這些值。

```cmd
sqlpackage.exe /a:import /tcs:"Data Source=<serverName>.database.windows.net;Initial Catalog=myMigratedDatabase>;User Id=<userId>;Password=<password>" /sf:AdventureWorks2017.bacpac /p:DatabaseEdition=Premium /p:DatabaseServiceObjective=P6
```

> [!IMPORTANT]
> 若要連線到從公司防火牆後方管理單一資料庫的 SQL Database 伺服器，該防火牆必須開啟連接埠 1433。 若要連線到受控執行個體，您必須有[點對站連線](sql-database-managed-instance-configure-p2s.md)或快速路由連線。

此範例說明如何使用 SqlPackage 與 Active Directory 通用驗證來匯入資料庫。

```cmd
sqlpackage.exe /a:Import /sf:testExport.bacpac /tdn:NewDacFX /tsn:apptestserver.database.windows.net /ua:True /tid:"apptest.onmicrosoft.com"
```

## <a name="performance-considerations"></a>效能考量

匯出速度因許多因素（例如資料形狀）而異，因此無法預測應期望的速度。 SqlPackage 可能需要相當長的時間，尤其是對於大型資料庫。

要獲得最佳性能，您可以嘗試以下策略：

1. 確保資料庫上沒有其他工作負荷運行。 在匯出之前棄置站台可能是確保沒有其他工作負荷運行的最佳解決方案。
2. 提高資料庫服務等級目標 （SLO） 以更好地處理匯出工作負載（主要是讀取 I/O）。 如果資料庫當前GP_Gen5_4，則業務關鍵型層可能有助於讀取工作負載。
3. 確保有群集索引，特別是對於大型表。 
4. 虛擬機器 （VM） 應與資料庫位於同一區域，以説明避免網路約束。
5. VM 應具有足夠大小的 SSD，用於在上載到 Blob 存儲之前生成臨時工件。
6. VM 應具有特定資料庫的足夠核心和記憶體配置。

## <a name="store-the-imported-or-exported-bacpac-file"></a>存儲導入或匯出的 。BACPAC 檔

.BACPAC檔可以存儲在[Azure Blob](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-overview)或[Azure 檔中](https://docs.microsoft.com/azure/storage/files/storage-files-introduction)。 

要獲得最佳性能，請使用 Azure 檔。 Sql包使用檔案系統運行，以便可以直接存取 Azure 檔。

要降低成本，請使用 Azure Blob，其成本低於高級 Azure 檔共用。 但是，它將要求您複製[。](https://docs.microsoft.com/sql/relational-databases/data-tier-applications/data-tier-applications#bacpac)在導入或匯出操作之前，blob 和本地檔案系統之間的 BACPAC 檔。 因此，該過程將需要更長的時間。

上傳或下載 。BACPAC 檔，請參閱[使用 AzCopy 和 Blob 存儲傳輸資料](../storage/common/storage-use-azcopy-blobs.md)，以及[使用 AzCopy 和檔存儲傳輸資料](../storage/common/storage-use-azcopy-files.md)。

根據您的環境，您可能需要[配置 Azure 存儲防火牆和虛擬網路](../storage/common/storage-network-security.md)。

## <a name="next-steps"></a>後續步驟

- 要瞭解如何連接到和查詢導入的 SQL 資料庫，請參閱[快速入門：Azure SQL 資料庫：使用 SQL 伺服器管理工作室連接和查詢資料](sql-database-connect-query-ssms.md)。
- 如需 SQL Server 客戶諮詢小組部落格中有關使用 BACPAC 檔案進行移轉的主題，請參閱[使用 BACPAC 檔案從 SQL Server 移轉至 Azure SQL Database](https://techcommunity.microsoft.com/t5/DataCAT/Migrating-from-SQL-Server-to-Azure-SQL-Database-using-Bacpac/ba-p/305407)。
- 如需有關整個 SQL Server 資料庫移轉程序的討論，包括效能建議，請參閱[將 SQL Server 資料庫移轉至 Azure SQL Database](sql-database-single-database-migrate.md)。
- 若要了解如何管理和共用儲存體金鑰，以及安全地共用存取簽章，請參閱 [Azure 儲存體安全性指南](https://docs.microsoft.com/azure/storage/common/storage-security-guide)。
