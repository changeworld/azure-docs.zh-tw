---
title: SQL 資料同步的資料同步代理程式
description: 了解如何安裝及執行「適用於 Azure SQL Data Sync 的 Data Sync Agent」，以與內部部署 SQL Server 資料庫同步資料
services: sql-database
ms.service: sql-database
ms.subservice: data-movement
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: carlrab
ms.date: 12/20/2018
ms.openlocfilehash: 39471ebded6280e7d394ee69c2d732b779c9ea50
ms.sourcegitcommit: ea006cd8e62888271b2601d5ed4ec78fb40e8427
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/14/2020
ms.locfileid: "81380901"
---
# <a name="data-sync-agent-for-azure-sql-data-sync"></a>適用於 Azure SQL Data Sync 的 Data Sync Agent

通過安裝和配置 Azure SQL 資料同步的資料同步代理,將資料與本地 SQL Server 資料庫同步。有關 SQL 資料同步的詳細資訊,請參考[SQL 資料同步跨多個雲端和本地資料庫同步資料](sql-database-sync-data.md)。

> [!IMPORTANT]
> Azure SQL 資料同步此時**不支援**Azure SQL 資料庫託管實例。

## <a name="download-and-install"></a>下載並安裝

若要下載 Data Sync Agent，請前往 [SQL Azure Data Sync Agent](https://www.microsoft.com/download/details.aspx?id=27693)。

### <a name="install-silently"></a>無訊息安裝

若要從命令提示字元以無訊息方式安裝 Data Sync Agent，請輸入類似以下範例的命令。 請檢查所下載 .msi 檔案的檔案名稱，然後提供您自己的 **TARGETDIR** 和 **SERVICEACCOUNT** 引數值。

- 如果您未提供 **TARGETDIR** 的值，預設值將是 `C:\Program Files (x86)\Microsoft SQL Data Sync 2.0`。

- 如果您提供 `LocalSystem` 作為 **SERVICEACCOUNT** 的值，當您設定代理程式以連線至內部部署 SQL Server 時，請使用 SQL Server 驗證。

- 如果您提供網域使用者帳戶或本機使用者帳戶作為 **SERVICEACCOUNT** 的值，您也必須使用 **SERVICEPASSWORD** 引數提供密碼。 例如： `SERVICEACCOUNT="<domain>\<user>"  SERVICEPASSWORD="<password>"` 。

```cmd
msiexec /i "SQLDataSyncAgent-2.0-x86-ENU.msi" TARGETDIR="C:\Program Files (x86)\Microsoft SQL Data Sync 2.0" SERVICEACCOUNT="LocalSystem" /qn
```

## <a name="sync-data-with-sql-server-on-premises"></a>與內部部署 SQL Server 同步資料

若要設定 Data Sync Agent 以便與一或多個內部部署 SQL Server 資料庫同步資料，請參閱[新增內部部署 SQL Server 資料庫](sql-database-get-started-sql-data-sync.md#add-on-prem)。

## <a name="data-sync-agent-faq"></a><a name="agent-faq"></a> Data Sync Agent 常見問題集

### <a name="why-do-i-need-a-client-agent"></a>我為何需要用戶端代理程式？

SQL 資料同步服務會透過用戶端代理程式來與 SQL Server 資料庫通訊。 這項安全性功能可防止直接與防火牆後方的資料庫通訊。 當 SQL 資料同步服務與代理程式通訊時，會使用加密的連線與唯一的權杖或「代理程式金鑰」** 來執行此動作。 SQL Server 資料庫會使用連接字串和代理程式金鑰來驗證代理程式。 這項設計可為您的資料提供高階安全性。

### <a name="how-many-instances-of-the-local-agent-ui-can-be-run"></a>一共可執行多少個本機代理程式 UI 執行個體？

只能執行一個 UI 執行個體。

### <a name="how-can-i-change-my-service-account"></a>如何變更我的服務帳戶？

安裝用戶端代理程式之後，變更服務帳戶的唯一方式是予以解除安裝，並使用新的服務帳戶來安裝新的用戶端代理程式。

### <a name="how-do-i-change-my-agent-key"></a>如何變更我的代理程式金鑰？

代理程式只會使用代理程式金鑰一次。 當您移除再重新安裝新的代理程式時，並不能重複使用它，也不能由多個代理程式使用。 如果您需要為現有代理程式建立新的金鑰，務必確定會以用戶端代理程式和 SQL 資料同步服務來記錄相同的金鑰。

### <a name="how-do-i-retire-a-client-agent"></a>如何淘汰用戶端代理程式？

若要讓代理程式立即失效或淘汰，請在入口網站中重新產生其金鑰，但不在代理程式 UI 中提交。 不論對應的代理程式處於連線或離線，重新產生金鑰即可讓先前的金鑰失效。

### <a name="how-do-i-move-a-client-agent-to-another-computer"></a>如何將用戶端代理程式移至另一部電腦？

如果您想要從另一部電腦執行本機代理程式，而非它目前所在的電腦，請執行下列動作：

1. 在所需電腦上安裝代理程式。
2. 登入 SQL 資料同步入口網站，然後為新的代理程式重新產生代理程式金鑰。
3. 使用新的代理程式 UI 來提交新的代理程式金鑰。
4. 請等待用戶端代理程式下載先前所註冊內部部署資料庫的清單。
5. 提供資料庫認證給顯示為無法連線的所有資料庫。 這些資料庫必須要能從安裝該代理程式的新電腦連線。

## <a name="troubleshoot-data-sync-agent-issues"></a><a name="agent-tshoot"></a> Data Sync Agent 問題疑難排解

- [用戶端代理程式安裝、解除安裝或修復失敗](#agent-install)

- [取消卸載後,用戶端代理無法正常工作](#agent-uninstall)

- [我的資料庫未列在代理程式清單](#agent-list)

- [用戶端代理無法啟動 (錯誤 1069)](#agent-start)

- [無法提交代理金鑰](#agent-key)

- [如果用戶端代理的關聯本地資料庫無法存取,則無法從門戶中刪除用戶端代理](#agent-delete)

- [本機同步代理程式應用程式無法連線至本機同步服務](#agent-connect)

### <a name="the-client-agent-install-uninstall-or-repair-fails"></a><a name="agent-install"></a>用戶端代理安裝、卸載或修復失敗

- **原因**。 許多情節可能會造成這個失敗。 若要判斷此失敗的特定原因，請查看記錄。

- **解決方案**。 若要尋找失敗的特定原因，請產生並查看 Windows Installer 記錄。 您可以在命令提示字元開啟記錄。 例如，如果下載的安裝檔案為 `SQLDataSyncAgent-2.0-x86-ENU.msi`，請使用下列命令列來產生並檢查記錄：

  - 安裝：`msiexec.exe /i SQLDataSyncAgent-2.0-x86-ENU.msi /l*v LocalAgentSetup.Log`
  - 解除安裝：`msiexec.exe /x SQLDataSyncAgent-2.0-x86-ENU.msi /l*v LocalAgentSetup.Log`

    您也可以為 Windows Installer 所執行的所有安裝開啟記錄。 Microsoft 知識庫文章[如何啟用 Windows Installer 記錄](https://support.microsoft.com/help/223300/how-to-enable-windows-installer-logging)提供開啟 Windows Installer 記錄的單鍵解決方案。 它也提供記錄的位置。

### <a name="the-client-agent-doesnt-work-after-i-cancel-the-uninstall"></a><a name="agent-uninstall"></a> 我取消解除安裝之後，我的用戶端代理程式無法運作

用戶端代理程式無法運作，即使您取消其解除安裝亦然。

- **原因**。 SQL 資料同步用戶端代理程式不會儲存認證，因此發生此問題。

- **解決方案**。 您可以嘗試這兩種解決方案：

    -   使用 services.msc 重新輸入用戶端代理程式的認證。
    -   解除安裝此用戶端代理程式，然後安裝新的用戶端代理程式。 從[下載中心](https://www.microsoft.com/download/details.aspx?id=27693)下載並安裝最新的用戶端代理程式。

### <a name="my-database-isnt-listed-in-the-agent-list"></a><a name="agent-list"></a>我的資料庫未列在代理清單中

當您嘗試將現有的 SQL Server 資料庫新增至同步群組時，資料庫不會顯示在代理程式的清單中。

這些情節可能會造成此問題：

- **原因**。 用戶端代理程式和同步群組位於不同的資料中心。

- **解決方案**。 用戶端代理程式和同步群組必須在相同資料中心。 若要設定此情況，您有兩個選項：

    -   在與同步群組所在的相同資料中心中建立新的代理程式。 然後向該代理程式註冊資料庫。
    -   刪除目前的同步處理群組。 然後，在代理程式所在的資料中心重新建立同步群組。

- **原因**。 用戶端代理程式的資料庫清單不是最新的。

- **解決方案**。 停止然後再重新啟動用戶端代理程式服務。

    本機代理程式只會在第一次提交代理程式金鑰時下載相關聯的資料庫清單。 在後續的代理程式金鑰提交時，它不會下載相關聯資料庫的清單。 在代理程式移動期間註冊的資料庫不會出現在原始代理程式執行個體上。

### <a name="client-agent-doesnt-start-error-1069"></a><a name="agent-start"></a> 用戶端代理程式無法啟動 (錯誤 1069)

您發現代理程式未在裝載 SQL Server 的電腦上執行。 當您嘗試以手動方式啟動代理程式時，您會看到對話方塊顯示錯誤訊息：「錯誤 1069：登入失敗所以服務無法啟動。」

![資料同步處理錯誤 1069 對話方塊](media/sql-database-troubleshoot-data-sync/sync-error-1069.png)

- **原因**。 此錯誤的可能原因是，在本機伺服器上的密碼在您建立代理程式及代理程式密碼之後已變更。

- **解決方案**。 將代理程式的密碼更新成目前的伺服器密碼：

  1. 找出 SQL 資料同步用戶端代理程式預覽服務。  
    a. 選取 [開始]****。  
    b. 在搜尋方塊中輸入 **services.msc**。  
    c. 在搜尋結果中,選擇 **「服務**」。。  
    d. 在 [服務]**** 視窗中，捲動至 [SQL Data Sync Agent]**** 的項目。  
  1. 在 [SQL Data Sync Agent]**** 上按一下滑鼠右鍵，然後選取 [停止]****。
  1. 在 [SQL Data Sync Agent]**** 上按一下滑鼠右鍵，然後選取 [屬性]****。
  1. 在 [SQL Data Sync Agent 屬性]**** 上，選取 [登入]**** 索引標籤。
  1. 在 [密碼]**** 方塊中，輸入您的密碼。
  1. 在 [確認密碼]**** 方塊中，重新輸入密碼。
  1. 選取 [套用]****，然後選取 [確定]****。
  1. 在 [服務]**** 視窗中，以滑鼠右鍵按一下 [SQL Data Sync Agent]**** 服務，然後按一下 [啟動]****。
  1. 關閉 [服務]**** 視窗。

### <a name="i-cant-submit-the-agent-key"></a><a name="agent-key"></a> 我無法提交代理程式金鑰

您建立或重新建立代理程式金鑰之後，嘗試透過 SqlAzureDataSyncAgent 應用程式提交該金鑰。 提交無法完成。

![[同步錯誤] 對話方塊 - 無法提交代理程式金鑰](media/sql-database-troubleshoot-data-sync/sync-error-cant-submit-agent-key.png)

- **先決條件**。 在繼續之前，請檢查下列必要條件：

  - SQL 資料同步 Windows 服務正在執行。

  - SQL 資料同步 Windows 服務的服務帳戶具有網路存取權。

  - 輸出 1433 連接埠已在您的本機防火牆規則中開啟。

  - 本機 IP 會加入同步中繼資料資料庫的伺服器或資料庫防火牆規則。

- **原因**。 代理程式金鑰可唯一識別每個本機代理程式。 金鑰必須符合兩個條件：

  -   SQL 資料同步伺服器和本機電腦上的用戶端代理程式金鑰必須相同。
  -   用戶端代理程式金鑰只能使用一次。

- **解決方案**。 如果您的代理程式無法運作，這是因為不符合這些條件之一或兩者皆不符合。 讓代理程式重新運作：

  1. 產生新的金鑰。
  1. 將新的金鑰套用至代理程式。

  若要將新的金鑰套用至代理程式：

  1. 在檔案總管中，移至您的代理程式安裝目錄。 預設安裝目錄為 C:\\Program Files (x86)\\Microsoft SQL Data Sync。
  1. 按兩下 bin 子目錄。
  1. 開啟 SqlAzureDataSyncAgent 應用程式。
  1. 選取 [提交代理程式金鑰]****。
  1. 在提供的空間中，貼上剪貼簿中的金鑰。
  1. 選取 [確定]  。
  1. 關閉程式。

### <a name="the-client-agent-cant-be-deleted-from-the-portal-if-its-associated-on-premises-database-is-unreachable"></a><a name="agent-delete"></a> 如果無法連線到與用戶端代理程式相關聯的內部部署資料庫，則無法從入口網站刪除用戶端代理程式

如果無法連線到向 SQL 資料同步用戶端代理程式註冊的本機端點 (也就是資料庫)，則無法刪除用戶端代理程式。

- **原因**。 無法刪除本機代理程式，因為無法連線的資料庫仍向代理程式註冊。 當您嘗試刪除代理程式時，刪除程序會嘗試連接資料庫，但是失敗。

- **解決方案**。 使用「強制刪除」以刪除無法連線的資料庫。

> [!NOTE]
> 如果「強制刪除」之後，同步中繼資料資料表仍然存在，請使用 `deprovisioningutil.exe` 來清除它們。

### <a name="local-sync-agent-app-cant-connect-to-the-local-sync-service"></a><a name="agent-connect"></a>本地同步代理應用程式無法連線到本地同步服務

- **解決方案**。 請嘗試下列步驟：

  1. 結束應用程式。  
  1. 開啟 [元件服務] 畫面。  
    a. 在工作列上的 [搜尋] 方塊中，輸入 **services.msc**。  
    b. 在搜尋結果中按兩下 [服務]****。  
  1. 停止 **SQL 資料同步**服務。
  1. 重新啟動**SQL 資料同步**服務。  
  1. 重新開啟應用程式。

## <a name="run-the-data-sync-agent-from-the-command-prompt"></a>從命令提示字元執行 Data Sync Agent

您可以從命令提示字元執行下列 Data Sync Agent 命令：

### <a name="ping-the-service"></a>偵測服務

#### <a name="usage"></a>使用量

```cmd
SqlDataSyncAgentCommand.exe -action pingsyncservice
```

#### <a name="example"></a>範例

```cmd
SqlDataSyncAgentCommand.exe -action "pingsyncservice"
```

### <a name="display-registered-databases"></a>顯示已註冊的資料庫

#### <a name="usage"></a>使用量

```cmd
SqlDataSyncAgentCommand.exe -action displayregistereddatabases
```

#### <a name="example"></a>範例

```cmd
SqlDataSyncAgentCommand.exe -action "displayregistereddatabases"
```

### <a name="submit-the-agent-key"></a>提交代理程式金鑰

#### <a name="usage"></a>使用量

```cmd
Usage: SqlDataSyncAgentCommand.exe -action submitagentkey -agentkey [agent key]  -username [user name] -password [password]
```

#### <a name="example"></a>範例

```cmd
SqlDataSyncAgentCommand.exe -action submitagentkey -agentkey [agent key generated from portal, PowerShell, or API] -username [user name to sync metadata database] -password [user name to sync metadata database]
```

### <a name="register-a-database"></a>註冊資料庫

#### <a name="usage"></a>使用量

```cmd
SqlDataSyncAgentCommand.exe -action registerdatabase -servername [on-premisesdatabase server name] -databasename [on-premisesdatabase name]  -username [domain\\username] -password [password] -authentication [sql or windows] -encryption [true or false]
```

#### <a name="examples"></a>範例

```cmd
SqlDataSyncAgentCommand.exe -action "registerdatabase" -serverName localhost -databaseName testdb -authentication sql -username <user name> -password <password> -encryption true

SqlDataSyncAgentCommand.exe -action "registerdatabase" -serverName localhost -databaseName testdb -authentication windows -encryption true

```

### <a name="unregister-a-database"></a>將資料庫取消註冊

當您使用此命令來取消註冊資料庫時，它將會完全取消佈建資料庫。 如果資料庫參與其他同步群組，這項作業會中斷其他同步群組。

#### <a name="usage"></a>使用量

```cmd
SqlDataSyncAgentCommand.exe -action unregisterdatabase -servername [on-premisesdatabase server name] -databasename [on-premisesdatabase name]
```

#### <a name="example"></a>範例

```cmd
SqlDataSyncAgentCommand.exe -action "unregisterdatabase" -serverName localhost -databaseName testdb
```

### <a name="update-credentials"></a>更新認證

#### <a name="usage"></a>使用量

```cmd
SqlDataSyncAgentCommand.exe -action updatecredential -servername [on-premisesdatabase server name] -databasename [on-premisesdatabase name]  -username [domain\\username] -password [password] -authentication [sql or windows] -encryption [true or false]
```

#### <a name="examples"></a>範例

```cmd
SqlDataSyncAgentCommand.exe -action "updatecredential" -serverName localhost -databaseName testdb -authentication sql -username <user name> -password <password> -encryption true

SqlDataSyncAgentCommand.exe -action "updatecredential" -serverName localhost -databaseName testdb -authentication windows -encryption true
```

## <a name="next-steps"></a>後續步驟

如需有關「SQL 資料同步」的詳細資訊，請參閱下列文章：

-   概觀 - [使用 Azure SQL 資料同步，跨多個雲端和內部部署資料庫同步資料](sql-database-sync-data.md)
-   設定資料同步
    - 在入口網站中 - [教學課程：設定 SQL 資料同步以同步處理 Azure SQL Database 與內部部署 SQL Server 之間的資料](sql-database-get-started-sql-data-sync.md)
    - 透過 PowerShell
        -  [使用 PowerShell 在多個 Azure SQL 資料庫之間同步](scripts/sql-database-sync-data-between-sql-databases.md)
        -  [使用 PowerShell 設定「資料同步」在內部部署的 Azure SQL Database 和 SQL Server 之間進行同步處理](scripts/sql-database-sync-data-between-azure-onprem.md)
-   最佳做法 - [Azure SQL 資料同步最佳做法](sql-database-best-practices-data-sync.md)
-   監視 - [使用 Azure 監視器記錄監視 SQL 資料同步](sql-database-sync-monitor-oms.md)
-   疑難排解 - [為 Azure SQL 資料同步的問題進行疑難排解](sql-database-troubleshoot-data-sync.md)
-   更新同步結構描述
    -   使用 Transact-SQL - [在 Azure SQL 資料同步中自動執行結構描述變更複寫](sql-database-update-sync-schema.md)
    -   使用 PowerShell - [使用 PowerShell 更新現有同步群組中的同步結構描述](scripts/sql-database-sync-update-schema.md)
