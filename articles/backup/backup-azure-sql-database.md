---
title: 將 SQL Server 資料庫備份到 Azure
description: 本文說明如何將 SQL Server 備份至 Azure。 本文也將說明 SQL Server 復原。
ms.topic: conceptual
ms.date: 06/18/2019
ms.openlocfilehash: 7305a75852deac466028e6278fca76626d8c1820
ms.sourcegitcommit: c29b7870f1d478cec6ada67afa0233d483db1181
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/13/2020
ms.locfileid: "79297470"
---
# <a name="about-sql-server-backup-in-azure-vms"></a>關於 Azure VM 中的 SQL Server 備份

[Azure 備份](backup-overview.md)提供以資料流程為基礎的特製化解決方案，以備份在 Azure vm 中執行的 SQL Server。 此解決方案與 Azure 備份的零基礎結構備份、長期保留和集中管理的優點一致。 此外，它還提供下列優點，專門用於 SQL Server：

1. 支援所有備份類型的工作負載感知備份-完整、差異和記錄
2. 使用經常記錄備份的 15-最低 RPO （復原點目標）
3. 時間點恢復最多一秒
4. 個別資料庫層級的備份與還原

若要查看目前支援的備份和還原案例，請參閱[支援矩陣](backup-azure-sql-database.md#scenario-support)。

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

## <a name="before-you-start"></a>開始之前

開始之前，請確認以下事項：

1. 請確定您具有在 Azure 中執行的 SQL Server 執行個體。 您可以在 Marketplace 中[快速建立 SQL Server 執行個體](../virtual-machines/windows/sql/quickstart-sql-vm-create-portal.md)。
2. 檢閱[功能考量](#feature-consideration-and-limitations)和[案例支援](#scenario-support)。
3. [檢閱關於此案例的常見問題](faq-backup-sql-server.md)。

## <a name="scenario-support"></a>案例支援

**支援** | **詳細資料**
--- | ---
**支援的部署** | 支援 SQL Marketplace Azure VM 和非 Marketplace (手動安裝 SQL Server) VM。
**支援的地區** | 澳大利亞東南部 (ASE)、澳大利亞東部 (AE)、澳大利亞中部 (AC)、澳大利亞中部 2 (AC) <br> 巴西南部 (BRS)<br> 加拿大中部 (CNC)、加拿大東部 (CE)<br> 東南亞 (SEA)、東亞 (EA) <br> 美國東部 (EUS)、美國東部 2 (EUS2)、美國中西部 (WCUS)、美國西部 (WUS)、美國西部 2 (WUS2)、美國中北部 (NCUS)、美國中部 (CUS)、美國中南部 (SCUS) <br> 印度中部 (INC)、印度南部 (INS)、印度西部 <br> 日本東部 (JPE)、日本西部 (JPW) <br> 南韓中部 (KRC)、南韓南部 (KRS) <br> 北歐 (NE)、西歐 <br> 英國南部 (UKS)、英國西部 (UKW) <br> US Gov 亞利桑那州、US Gov 維吉尼亞州、US Gov 德克薩斯州、US DoD 中部、US DoD 東部 <br> 德國北部、德國中西部 <br> 瑞士北部、瑞士西部 <br> 法國中部 <br> 中國東部、中國東部 2、中國北部、中國北部 2
**受支援的作業系統** | Windows Server 2019、Windows Server 2016、Windows Server 2012、Windows Server 2008 R2 SP1 <br/><br/> 目前不支援 Linux。
**支援的 SQL Server 版本** | SQL Server 2019、SQL Server 2017 (如 [搜尋產品生命週期](https://support.microsoft.com/lifecycle/search?alpha=SQL%20server%202017) 頁面所述)、SQL Server 2016 and SPs (如 [搜尋產品生命週期](https://support.microsoft.com/lifecycle/search?alpha=SQL%20server%202016%20service%20pack) 頁面所述)、SQL Server 2014、SQL Server 2012、SQL Server 2008 R2、SQL Server 2008 <br/><br/> Enterprise、Standard、Web、Developer、Express。
**支援的 .NET 版本** | 安裝在 VM 上的 .NET Framework 4.5.2 或更新版本

## <a name="feature-consideration-and-limitations"></a>功能考量和限制

* 您可以透過 Azure 入口網站或 **PowerShell** 來設定 SQL Server 備份。 我們不支援 CLI。
* 有兩種[部署](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-deployment-model)支援此解決方案 - Azure Resource Manager VM 和傳統 VM。
* 執行 SQL Server 的 VM 需要有網際網路連線能力，才能存取 Azure 公用 IP 位址。
* 不支援 SQL Server**容錯移轉叢集實例（FCI）** 。
* 不支援鏡像資料庫和資料庫快照集的備份和還原作業。
* 使用多個備份解決方案來備份獨立 SQL Server 執行個體或 SQL Always On 可用性群組可能會導致備份失敗；請避免這麼做。
* 使用相同或不同的解決方案來備份可用性群組的兩個節點，也可能導致備份失敗。
* 「Azure 備份」針對**唯讀**資料庫僅支援「完整」和「只複製完整」備份
* 無法保護含有大量檔案的資料庫。 支援的檔案數目上限為 **1000** 個。  
* 您最多可在保存庫中備份 **2000** 個 SQL Server 資料庫。 如果您有更多資料庫，則可以建立多個保存庫。
* 您最多可以一次設定 **50** 個資料庫的備份；此限制有助於將備份負載最佳化。
* 我們支援的資料庫大小上限為**2 TB** ;對於大於該效能問題的大小，可能會出現。
* 若要瞭解每個伺服器可以保護多少個資料庫，我們必須考慮頻寬、VM 大小、備份頻率、資料庫大小等因素，並[下載](https://download.microsoft.com/download/A/B/5/AB5D86F0-DCB7-4DC3-9872-6155C96DE500/SQL%20Server%20in%20Azure%20VM%20Backup%20Scale%20Calculator.xlsx)資源規劃工具，以根據 VM 資源和備份原則來提供每個伺服器的大約資料庫數目。
* 如果是可用性群組，則會根據幾個因素，從不同節點進行備份。 可用性群組的備份行為摘述於下方。

### <a name="back-up-behavior-in-case-of-always-on-availability-groups"></a>Alaways On 可用性群組的備份行為

建議僅在 AG 的一個節點上設定備份。 備份應該一律設定於與主要節點相同的區域中。 換句話說，您設定備份的區域中一定要出現主要節點。 如果 AG 的所有節點都位於已設定備份的相同區域中，就不會有任何顧慮。

#### <a name="for-cross-region-ag"></a>若為跨區域 AG

* 無論備份喜好設定為何，都不會從不是位於已設定備份之相同區域中的節點進行備份。 這是因為不支援跨區域備份。 如果您只有兩個節點，而次要節點位於另一個區域中，則為，在此情況下，備份將會繼續從主要節點進行（除非您的備份喜好設定為「僅次要」）。
* 如果備份設定所在區域以外的區域發生容錯移轉，則備份會在已容錯移轉區域中的節點發生失敗。

視備份喜好設定和備份類型 (完整/差異/記錄/只複製完整) 而定，會從特定節點 (主要/次要) 進行備份。

* **備份喜好設定：主要**

**備份類型** | **節點**
    --- | ---
    完整 | Primary
    差異 | Primary
    Log |  Primary
    只複製完整 |  Primary

* **備份喜好設定：僅次要**

**備份類型** | **節點**
--- | ---
完整 | Primary
差異 | Primary
Log |  次要
只複製完整 |  次要

* **備份喜好設定：次要**

**備份類型** | **節點**
--- | ---
完整 | Primary
差異 | Primary
Log |  次要
只複製完整 |  次要

* **沒有備份喜好設定**

**備份類型** | **節點**
--- | ---
完整 | Primary
差異 | Primary
Log |  次要
只複製完整 |  次要

## <a name="set-vm-permissions"></a>設定 VM 權限

  當您在 SQL Server 上執行探索時，Azure 備份會執行下列動作：

* 新增 AzureBackupWindowsWorkload 擴充功能。
* 建立 NT SERVICE\AzureWLBackupPluginSvc 帳戶以探索虛擬機器上的資料庫。 此帳戶會用於備份和還原，且必須具備 SQL 系統管理員 (sysadmin) 權限。
* 探索在虛擬機器上執行的資料庫，Azure 備份會使用 NT AUTHORITY\SYSTEM 帳戶。 此帳戶必須是 SQL 上的公用登入。

如果您未在 Azure Marketplace 建立 SQL Server VM，或如果您在 SQL 2008 和 2008 R2 上，則可能會收到 **UserErrorSQLNoSysadminMembership** 錯誤。

有關如何為 Windows 2008 R2 上執行的 **SQL 2008** 和 **2008 R2** 提供權限，請參閱[這裡](#give-sql-sysadmin-permissions-for-sql-2008-and-sql-2008-r2)。

針對所有其他版本，請依照下列步驟修正權限：

  1. 使用具有 SQL Server 系統管理員權限的帳戶登入 SQL Server Management Studio (SSMS)。 除非您需要特殊權限，否則 Windows 驗證應該能運作。
  2. 在 SQL Server 上，開啟 [安全性]/[登入] 資料夾。

      ![開啟 [安全性]/[登入] 資料夾來查看帳戶](./media/backup-azure-sql-database/security-login-list.png)

  3. 以滑鼠右鍵按一下 [登入] 資料夾，然後選取 [新增登入]。 在 [登入 - 新增] 中，選取 [搜尋]。

      ![在 [登入 - 新增] 對話方塊中，選取 [搜尋]](./media/backup-azure-sql-database/new-login-search.png)

  4. Windows 虛擬服務帳戶 **NT SERVICE\AzureWLBackupPluginSvc** 已於虛擬機器註冊期間和 SQL 探索階段建立。 請輸入 [輸入要選取的物件名稱] 中顯示的帳戶名稱。 選取 [檢查名稱] 以解析名稱。 按一下 [確定]。

      ![選取 [檢查名稱] 以解析未知的服務名稱](./media/backup-azure-sql-database/check-name.png)

  5. 在 [伺服器角色] 中，確定已選取**系統管理員**角色。 按一下 [確定]。 現在應該存在必要權限。

      ![確定已選取系統管理員伺服器角色](./media/backup-azure-sql-database/sysadmin-server-role.png)

  6. 現在，請建立資料庫與復原服務保存庫的關聯。 在 Azure 入口網站的 [受保護的伺服器] 清單中，以滑鼠右鍵按一下處於錯誤狀態的伺服器，然後選取 [重新探索資料庫]。

      ![確認伺服器具有適當的權限](./media/backup-azure-sql-database/check-erroneous-server.png)

  7. 在 [通知] 區域中查看進度。 找到選取的資料庫之後，即會出現成功訊息。

      ![部署成功訊息](./media/backup-azure-sql-database/notifications-db-discovered.png)

> [!NOTE]
> 如果您的 SQL Server 安裝了多個 SQL Server 執行個體，您就必須將 **NT Service\AzureWLBackupPluginSvc** 帳戶的系統管理員權限新增至所有的 SQL 執行個體。

### <a name="give-sql-sysadmin-permissions-for-sql-2008-and-sql-2008-r2"></a>為 SQL 2008 和 SQL 2008 R2 提供 SQL 系統管理員權限

新增 **NT AUTHORITY\SYSTEM** 和 **NT Service\AzureWLBackupPluginSvc** 登入至 SQL Server 執行個體：

1. 移至 [物件總管] 中的 SQL Server 執行個體。
2. 瀏覽至 [安全性] -> [登入]
3. 以滑鼠右鍵按一下 [登入]，然後按一下 [新增登入...]

    ![使用 SSMS 的新登入](media/backup-azure-sql-database/sql-2k8-new-login-ssms.png)

4. 移至 [一般] 索引標籤，然後輸入 **NT AUTHORITY\SYSTEM** 為登入名稱。

    ![SSMS 的登入名稱](media/backup-azure-sql-database/sql-2k8-nt-authority-ssms.png)

5. 移至 [伺服器角色]，然後選擇 [公用] 和 [sysadmin] 角色。

    ![選擇 SSMS 中的角色](media/backup-azure-sql-database/sql-2k8-server-roles-ssms.png)

6. 移至 [狀態]。 「授與」權限連線到資料庫引擎，且 [登入] 為 [已啟用]。

    ![在 SSMS 中授與權限](media/backup-azure-sql-database/sql-2k8-grant-permission-ssms.png)

7. 按一下 [確定]。
8. 重複相同的步驟順序 (上述 1-7)，將 NT Service\AzureWLBackupPluginSvc 登入新增到 SQL Server 執行個體。 如果登入已存在，請確定其具有 sysadmin 伺服器角色，且在 [狀態] 下具有「授與權限連線到資料庫引擎，且 [登入] 為 [已啟用]」。
9. 授與許可權之後 **，會**在入口網站中重新探索資料庫：保存庫 **->** Azure VM 中的備份基礎結構 **->** 工作負載：

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
