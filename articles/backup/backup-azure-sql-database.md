---
title: 將 SQL Server 資料庫備份到 Azure
description: 本文說明如何將 SQL Server 備份至 Azure。 本文也將說明 SQL Server 復原。
ms.topic: conceptual
ms.date: 06/18/2019
ms.openlocfilehash: 537257733d7693598fd8007da6ce12c28fbeb02a
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "79408755"
---
# <a name="about-sql-server-backup-in-azure-vms"></a>關於 Azure VM 中的 SQL Server 備份

[Azure 備份](backup-overview.md)提供以資料流程為基礎的特製化解決方案，以備份在 Azure vm 中執行的 SQL Server。 此解決方案與 Azure 備份的零基礎結構備份、長期保留和集中管理的優點一致。 此外，它還提供下列優點，專門用於 SQL Server：

1. 支援所有備份類型的工作負載感知備份-完整、差異和記錄
2. 使用經常記錄備份的 15-最低 RPO （復原點目標）
3. 時間點恢復最多一秒
4. 個別資料庫層級的備份與還原

若要查看目前支援的備份和還原案例，請參閱[支援矩陣](sql-support-matrix.md#scenario-support)。

## <a name="backup-process"></a>備份程序

此解決方案會利用 SQL 原生 API 來備份您的 SQL 資料庫。

* 在您指定想要保護的 SQL Server VM 並查詢此 VM 中的資料庫之後，Azure 備份服務會在此 VM 上安裝名為 `AzureBackupWindowsWorkload` 的工作負載備份擴充功能。
* 此延伸模組是由一個協調器和一個 SQL 外掛程式所組成。 協調器負責觸發各種作業 (例如設定備份、備份和還原) 的工作流程，而外掛程式則負責實際的資料流程。
* 為了能夠在此 VM 上探索資料庫，「Azure 備份」會建立 `NT SERVICE\AzureWLBackupPluginSvc` 帳戶。 此帳戶會用於備份和還原，且必須具備 SQL 系統管理員 (sysadmin) 權限。 `NT SERVICE\AzureWLBackupPluginSvc` 帳戶是[虛擬服務帳戶](https://docs.microsoft.com/windows/security/identity-protection/access-control/service-accounts#virtual-accounts)，因此不需要管理密碼。 「Azure 備份」會利用 `NT AUTHORITY\SYSTEM` 帳戶來進行資料庫探索/查詢，因此這個帳戶必須是 SQL 上的公開登入帳戶。 如果您尚未從 Azure Marketplace 建立 SQL Server VM，您可能會收到 **UserErrorSQLNoSysadminMembership** 錯誤。 若發生此狀況，請[依照下列指示操作](#set-vm-permissions)。
* 在您於選取的資料庫上觸發設定保護之後，備份服務便會為協調器設定備份排程及其他原則詳細資料，延伸模組會將這些都快取在 VM 本機。
* 在排定的時間，協調器會與外掛程式進行通訊，然後使用 VDI 開始從 SQL Server 串流處理備份資料。  
* 外掛程式會將資料直接傳送給復原服務保存庫，因此不需要暫存位置。 「Azure 備份」服務會將資料加密並儲存在儲存體帳戶中。
* 當資料傳輸完成時，協調器會向備份服務確認認可。

  ![SQL 備份架構](./media/backup-azure-sql-database/backup-sql-overview.png)

## <a name="before-you-start"></a>在您開始使用 Intune 之前

開始之前，請確認以下事項：

1. 請確定您具有在 Azure 中執行的 SQL Server 執行個體。 您可以在 Marketplace 中[快速建立 SQL Server 執行個體](../virtual-machines/windows/sql/quickstart-sql-vm-create-portal.md)。
2. 檢閱[功能考量](sql-support-matrix.md#feature-consideration-and-limitations)和[案例支援](sql-support-matrix.md#scenario-support)。
3. [檢閱關於此案例的常見問題](faq-backup-sql-server.md)。

## <a name="set-vm-permissions"></a>設定 VM 權限

  當您在 SQL Server 上執行探索時，Azure 備份會執行下列動作：

* 新增 AzureBackupWindowsWorkload 擴充功能。
* 建立 NT SERVICE\AzureWLBackupPluginSvc 帳戶以探索虛擬機器上的資料庫。 此帳戶會用於備份和還原，且必須具備 SQL 系統管理員 (sysadmin) 權限。
* 探索在虛擬機器上執行的資料庫，Azure 備份會使用 NT AUTHORITY\SYSTEM 帳戶。 此帳戶必須是 SQL 上的公用登入。

如果您未在 Azure Marketplace 建立 SQL Server VM，或如果您在 SQL 2008 和 2008 R2 上，則可能會收到 **UserErrorSQLNoSysadminMembership** 錯誤。

有關如何為 Windows 2008 R2 上執行的 **SQL 2008** 和 **2008 R2** 提供權限，請參閱[這裡](#give-sql-sysadmin-permissions-for-sql-2008-and-sql-2008-r2)。

針對所有其他版本，請依照下列步驟修正權限：

  1. 使用具有 SQL Server 系統管理員權限的帳戶登入 SQL Server Management Studio (SSMS)。 除非您需要特殊權限，否則 Windows 驗證應該能運作。
  2. 在 [SQL Server 上，開啟 [**安全性/** 登入] 資料夾。

      ![開啟 [安全性]/[登入] 資料夾來查看帳戶](./media/backup-azure-sql-database/security-login-list.png)

  3. 以滑鼠右鍵按一下 [登入]**資料夾，然後**選取 [**新增登**入]。 在 [登入 - 新增]**** 中，選取 [搜尋]****。

      ![在 [登入 - 新增] 對話方塊中，選取 [搜尋]](./media/backup-azure-sql-database/new-login-search.png)

  4. Windows 虛擬服務帳戶 **NT SERVICE\AzureWLBackupPluginSvc** 已於虛擬機器註冊期間和 SQL 探索階段建立。 請輸入 [輸入要選取的物件名稱]**** 中顯示的帳戶名稱。 選取 [檢查名稱]**** 以解析名稱。 按一下 [確定]  。

      ![選取 [檢查名稱] 以解析未知的服務名稱](./media/backup-azure-sql-database/check-name.png)

  5. 在 [伺服器角色]**** 中，確定已選取**系統管理員**角色。 按一下 [確定]  。 現在應該存在必要權限。

      ![確定已選取系統管理員伺服器角色](./media/backup-azure-sql-database/sysadmin-server-role.png)

  6. 現在，請建立資料庫與復原服務保存庫的關聯。 在 Azure 入口網站的 [受保護的伺服器]**** 清單中，以滑鼠右鍵按一下處於錯誤狀態的伺服器，然後選取 [重新探索資料庫]****。

      ![確認伺服器具有適當的權限](./media/backup-azure-sql-database/check-erroneous-server.png)

  7. 在 [通知]**** 區域中查看進度。 找到選取的資料庫之後，即會出現成功訊息。

      ![部署成功訊息](./media/backup-azure-sql-database/notifications-db-discovered.png)

> [!NOTE]
> 如果您的 SQL Server 安裝了多個 SQL Server 執行個體，您就必須將 **NT Service\AzureWLBackupPluginSvc** 帳戶的系統管理員權限新增至所有的 SQL 執行個體。

### <a name="give-sql-sysadmin-permissions-for-sql-2008-and-sql-2008-r2"></a>為 SQL 2008 和 SQL 2008 R2 提供 SQL 系統管理員權限

新增 **NT AUTHORITY\SYSTEM** 和 **NT Service\AzureWLBackupPluginSvc** 登入至 SQL Server 執行個體：

1. 移至 [物件總管] 中的 SQL Server 執行個體。
2. 瀏覽至 [安全性] -> [登入]
3. 以滑鼠右鍵按一下 [登入]，然後按一下 [新增登入...]**

    ![使用 SSMS 的新登入](media/backup-azure-sql-database/sql-2k8-new-login-ssms.png)

4. 移至 [一般] 索引標籤，然後輸入 **NT AUTHORITY\SYSTEM** 為登入名稱。

    ![SSMS 的登入名稱](media/backup-azure-sql-database/sql-2k8-nt-authority-ssms.png)

5. 移至 [伺服器角色]**，然後選擇 [公用]** 和 [sysadmin]** 角色。

    ![選擇 SSMS 中的角色](media/backup-azure-sql-database/sql-2k8-server-roles-ssms.png)

6. 移至 [狀態]**。 「授與」** 權限連線到資料庫引擎，且 [登入] 為 [已啟用]**。

    ![在 SSMS 中授與權限](media/backup-azure-sql-database/sql-2k8-grant-permission-ssms.png)

7. 按一下 [確定]。
8. 重複相同的步驟順序 (上述 1-7)，將 NT Service\AzureWLBackupPluginSvc 登入新增到 SQL Server 執行個體。 如果登入已存在，請確定其具有 sysadmin 伺服器角色，且在 [狀態] 下具有「授與權限連線到資料庫引擎，且 [登入] 為 [已啟用]」。
9. 授與許可權之後**Rediscover DBs** ，會重新探索入口網站中**->** 的 db **->** ： Azure VM 中的保存庫備份基礎結構工作負載：

    ![在 Azure 入口網站中重新探索 DB](media/backup-azure-sql-database/sql-rediscover-dbs.png)

或者，您可以藉由在管理員模式中執行下列 PowerShell 命令以自動化提供權限。 預設執行個體名稱設定為 MSSQLSERVER。 視需要在指令碼中變更執行個體名稱引數：

```powershell
param(
    [Parameter(Mandatory=$false)]
    [string] $InstanceName = "MSSQLSERVER"
)
if ($InstanceName -eq "MSSQLSERVER")
{
    $fullInstance = $env:COMPUTERNAME   # In case it is the default SQL Server Instance
}
else
{
    $fullInstance = $env:COMPUTERNAME + "\" + $InstanceName   # In case of named instance
}
try
{
    sqlcmd.exe -S $fullInstance -Q "sp_addsrvrolemember 'NT Service\AzureWLBackupPluginSvc', 'sysadmin'" # Adds login with sysadmin permission if already not available
}
catch
{
    Write-Host "An error occurred:"
    Write-Host $_.Exception|format-list -force
}
try
{
    sqlcmd.exe -S $fullInstance -Q "sp_addsrvrolemember 'NT AUTHORITY\SYSTEM', 'sysadmin'" # Adds login with sysadmin permission if already not available
}
catch
{
    Write-Host "An error occurred:"
    Write-Host $_.Exception|format-list -force
}
```

## <a name="next-steps"></a>後續步驟

* [了解](backup-sql-server-database-azure-vms.md)如何備份 SQL Server 資料庫。
* [了解](restore-sql-database-azure-vm.md)如何還原已備份的 SQL Server 資料庫。
* [了解](manage-monitor-sql-database-backup.md)如何管理已備份的 SQL Server 資料庫。
