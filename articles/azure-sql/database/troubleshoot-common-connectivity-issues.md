---
title: 解決暫時性錯誤
description: 瞭解如何在連線到 Azure SQL Database、Azure SQL 受控執行個體和 Azure Synapse Analytics 時，疑難排解、診斷及防止 SQL 連線錯誤或暫時性錯誤。
keywords: sql 連接, 連接字串, 連接問題, 暫時性錯誤, 連接錯誤
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: troubleshooting
author: dalechen
ms.author: ninarn
ms.reviewer: sstein, vanto
ms.date: 01/14/2020
ms.openlocfilehash: f8c94e36a1a6d1f675e9d6a7dde456dbf6eb8897
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/28/2020
ms.locfileid: "92791353"
---
# <a name="troubleshoot-transient-connection-errors-in-sql-database-and-sql-managed-instance"></a>針對 SQL Database 和 SQL 受控執行個體中的暫時性連接錯誤進行疑難排解

[!INCLUDE[appliesto-sqldb-sqlmi-asa](../includes/appliesto-sqldb-sqlmi-asa.md)]

本文說明如何防止、疑難排解、診斷和減緩用戶端應用程式在與 Azure SQL Database、Azure SQL 受控執行個體和 Azure Synapse Analytics 互動時遇到的連接錯誤和暫時性錯誤。 了解如何設定重試邏輯、建置連接字串和調整其他連接設定。

<a id="i-transient-faults" name="i-transient-faults"></a>

## <a name="transient-errors-transient-faults"></a>暫時性錯誤 (暫時性故障)

暫時性錯誤 (也稱為暫時性故障) 具有很快就會自行解決的根本原因。 當 Azure 系統快速地將硬體資源轉移到負載平衡更好的各種工作負載時，偶爾會發生暫時性錯誤。 其中大部分重新設定事件會在不到 60 秒內完成。 在此重新設定時間範圍期間，您可能會在 SQL Database 中連接到資料庫時發生問題。 連接到您資料庫的應用程式應該建立成預期這些暫時性錯誤。 若要處理這些錯誤，請在其程式碼中實作重試邏輯，而不是對使用者呈現為應用程式錯誤。

如果用戶端程式使用 ADO.NET，系統會擲回 **SqlException** ，告知您的程式發生暫時性錯誤。

<a id="connection-versus-command" name="connection-versus-command"></a>

### <a name="connection-vs-command"></a>連線與命令

請重試 SQL Database 和 SQL 受控執行個體連接，或根據下列各項重新建立：

- **嘗試連線期間發生暫時性錯誤**

在延遲數秒後重試連線。

- **SQL Database 和 SQL 受控執行個體查詢命令期間發生暫時性錯誤**

請勿立即重試命令。 而是在延遲之後，建立全新的連線。 然後重試此命令。

<a id="j-retry-logic-transient-faults" name="j-retry-logic-transient-faults"></a>

## <a name="retry-logic-for-transient-errors"></a>暫時性錯誤的重試邏輯

用戶端程式若包含重試邏輯，在偶爾遇到暫時性錯誤時就會更可靠。 當您的程式透過協力廠商中介軟體與您在 SQL Database 中的資料庫進行通訊時，請詢問廠商中介軟體是否包含暫時性錯誤的重試邏輯。

<a id="principles-for-retry" name="principles-for-retry"></a>

### <a name="principles-for-retry"></a>重試原則

- 如果是暫時性錯誤，請重試開啟連線。
- 請勿直接重試因為暫時性錯誤而失敗的 SQL Database 或 SQL 受控執行個體 `SELECT` 語句。 您應建立全新的連線，然後重試 `SELECT`。
- 當 SQL Database 或 SQL 受控執行個體 `UPDATE` 語句因為暫時性錯誤而失敗時，請先建立全新的連接，再重試更新。 重試邏輯必須確保整個資料庫交易完成，或整個交易已復原。

### <a name="other-considerations-for-retry"></a>其他重試考量

- 在下班後自動啟動並在早上前完成的批次程式，可以進行長時間間隔的重試。
- 使用者介面程式應該說明了人類將在長時間等候後放棄的傾向。 此方案不得每隔幾秒鐘重試，因為該原則可能讓系統充斥要求。

### <a name="interval-increase-between-retries"></a>增加重試之間的間隔

我們建議您在您第一次重試前等待 5 秒鐘。 在少於 5 秒的延遲後重試，雲端服務會有超過負荷的風險。 對於後續每次重試，延遲應以指數方式成長，最大值為 60 秒。

如需使用 ADO.NET 的用戶端封鎖期間的討論，請參閱 [連接共用 (ADO.NET) ](/dotnet/framework/data/adonet/sql-server-connection-pooling)。

您也可以設定在程式自行終止之前的重試次數上限。

### <a name="code-samples-with-retry-logic"></a>具有重試邏輯的程式碼範例

使用重試邏輯的程式碼範例位於：

- [使用 ADO.NET 將彈性地連線至 Azure SQL][step-4-connect-resiliently-to-sql-with-ado-net-a78n]
- [使用 PHP 將彈性地連線至 Azure SQL][step-4-connect-resiliently-to-sql-with-php-p42h]

<a id="k-test-retry-logic" name="k-test-retry-logic"></a>

### <a name="test-your-retry-logic"></a>測試您的重試邏輯

若要測試您的重試邏輯，您必須在程式仍在執行時模擬或產生可以更正的錯誤。

#### <a name="test-by-disconnecting-from-the-network"></a>中斷與網路連接以進行測試

您可以測試重試邏輯的方法，就是在程式執行時中斷用戶端電腦與網路的連接。 錯誤是：

- **SqlException.Number** = 11001
- 訊息：「未知的主機」

在第一次重試的過程中，您可以將用戶端電腦重新連線到網路，然後嘗試連接。

若要使這項測試可行，請先中斷電腦的網路連線，再啟動您的程式。 然後，您的程式會辨識一個執行階段參數，以便程式：

- 暫時將 11001 加入至其錯誤清單，視為暫時性。
- 如往常般嘗試其第一個連接。
- 在攔截到錯誤之後，請從清單中移除 11001。
- 顯示一則訊息，告訴使用者將電腦連到網路。
- 使用 **Console.ReadLine** 方法或含 [確定] 按鈕的對話方塊，暫停進一步執行。 使用者在電腦連上網路之後按下 Enter 鍵。
- 重新嘗試連接，預期成功。

#### <a name="test-by-misspelling-the-user-name-when-connecting"></a>在連接時使用拼寫錯誤的使用者名稱進行測試

在第一次連接嘗試之前，您的程式可以故意拼錯使用者名稱。 錯誤是：

- **SqlException.Number** = 18456
- 錯誤將為：「使用者 'WRONG_MyUserName' 登入失敗。」

第一次重試時，您的程式可以更正拼字錯誤，然後嘗試連線。

若要使這項測試可行，您的程式會辨識一個執行階段參數，以便程式：

- 暫時將 18456 加入至其錯誤清單，視為暫時性。
- 故意將 'WRONG_' 加入至使用者名稱。
- 在攔截到錯誤之後，請從清單中移除 18456。
- 從使用者名稱中移除 'WRONG_'。
- 重新嘗試連接，預期成功。

<a id="net-sqlconnection-parameters-for-connection-retry" name="net-sqlconnection-parameters-for-connection-retry"></a>

## <a name="net-sqlconnection-parameters-for-connection-retry"></a>進行連線重試的.NET SqlConnection 參數

如果您的用戶端程式使用 .NET Framework 類別 **SqlClient** 連接到 SQL Database 中的資料庫，請使用 .net 4.6.1 或更新版本 (或 .net Core) ，讓您可以使用其連接重試功能。 如需此功能的詳細資訊，請參閱 [SqlConnection. ConnectionString 屬性](/dotnet/api/system.data.sqlclient.sqlconnection.connectionstring?view=netframework-4.8&preserve-view=true)。

<!--
2015-11-30, FwLink 393996 points to dn632678.aspx, which links to a downloadable .docx related to SqlClient and SQL Server 2014.
-->

當您為 [SqlConnection](/dotnet/api/system.data.sqlclient.sqlconnection.connectionstring) 物件建立 **連接字串** 時，請調整下列參數的值：

- **ConnectRetryCount** ： &nbsp; &nbsp; 預設值為1。 範圍是 0 到 255。
- **ConnectRetryInterval** ： &nbsp; &nbsp; 預設值為10秒。 範圍是 1 到 60。
- **連接逾時** ： &nbsp; &nbsp; 預設值為15秒。 範圍是 0 到 2147483647。

具體來說，您選擇的值應該會讓下列等式成立：連線逾時 = ConnectRetryCount * ConnectionRetryInterval

例如，如果計數等於 3，且間隔等於 10 秒，則僅只 29 秒的逾時無法讓系統有足夠的時間進行第三次及最後一次的連線重試：29 < 3 * 10。

<a id="connection-versus-command" name="connection-versus-command"></a>

## <a name="connection-vs-command"></a>連線與命令

**ConnectRetryCount** 和 **ConnectRetryInterval** 參數讓您的 **SqlConnection** 物件可重試連接作業，而不需告知或中斷您的程式，例如將控制權傳回您的程式。 可能會在以下情況中發生重試：

- SqlConnection. 開啟方法呼叫
- SqlConnection.Exe刻意的方法呼叫

有一些微妙的差異。 若在執行「查詢」 時發生暫時性錯誤，您的 **SqlConnection** 物件並不會重試連線作業。 而且絕對不會重試查詢。 不過，在傳送您的查詢以供執行之前， **SqlConnection** 會先快速檢查連接。 如果快速檢查偵測到連接問題， **SqlConnection** 會重試連接作業。 如果重試成功，就會傳送您的查詢以供執行。

### <a name="should-connectretrycount-be-combined-with-application-retry-logic"></a>是否應該將 ConnectRetryCount 與應用程式重試邏輯結合

假設您的應用程式有健全的自訂重試邏輯。 它可能會重試連線作業 4 次。 如果您將 **ConnectRetryInterval** 和 **ConnectRetryCount** =3 加入到連接字串，您會將重試次數增加為 4 * 3 = 12 次重試。 您可能不會想要這麼多的重試次數。

<a id="a-connection-connection-string" name="a-connection-connection-string"></a>

## <a name="connections-to-your-database-in-sql-database"></a>在 SQL Database 中連接到您的資料庫

<a id="c-connection-string" name="c-connection-string"></a>

### <a name="connection-connection-string"></a>連接：連接字串

連接到資料庫所需的連接字串，與用來連接到 SQL Server 的字串稍有不同。 您可以從 [Azure 入口網站](https://portal.azure.com/)複製資料庫的連接字串。

[!INCLUDE [sql-database-include-connection-string-20-portalshots](../../../includes/sql-database-include-connection-string-20-portalshots.md)]

<a id="b-connection-ip-address" name="b-connection-ip-address"></a>

### <a name="connection-ip-address"></a>連接：IP 位址

您必須將 SQL Database 設定為接受來自裝載用戶端程式之電腦的 IP 位址的通訊。 若要設定此組態，請透過 [Azure 入口網站](https://portal.azure.com/)編輯防火牆設定。

如果您忘了設定 IP 位址，您的程式會失敗並出現一個好用的錯誤訊息，陳述必要的 IP 位址。

[!INCLUDE [sql-database-include-ip-address-22-portal](../../../includes/sql-database-include-ip-address-22-v12portal.md)]

如需詳細資訊，請參閱 [SQL Database 中的設定防火牆設定](firewall-configure.md)。
<a id="c-connection-ports" name="c-connection-ports"></a>

### <a name="connection-ports"></a>連接：連接埠

通常，您只需要在裝載用戶端程式的電腦上確定已開啟連接埠 1433 進行輸出通訊。

例如，當用戶端程式裝載在 Windows 電腦上時，您可在主機上使用 Windows 防火牆來開啟通訊埠 1433。

1. 開啟 [控制台]。
2. 選取 **所有主控台專案**  >  **Windows 防火牆**  >  **Advanced Settings**  >  **輸出規則**  >  **動作**  >  的 **新規則** 。

如果您的用戶端程式裝載在 Azure 虛擬機器 (VM) 上，請閱讀[適用於 ADO.NET 4.5 和 SQL Database 的 1433 以外的連接埠](adonet-v12-develop-direct-route-ports.md)。

如需有關在您的資料庫中設定埠和 IP 位址的背景資訊，請參閱 [Azure SQL Database 防火牆](firewall-configure.md)。

<a id="d-connection-ado-net-4-5" name="d-connection-ado-net-4-5"></a>

### <a name="connection-adonet-462-or-later"></a>連線：ADO.NET 4.6.2 或更新版本

如果您的程式使用 **System.Data.SqlClient.SqlConnection** 之類的 ADO.NET 類別來連線到 SQL Database，建議您使用 .NET Framework 4.6.2 版或更新版本。

#### <a name="starting-with-adonet-462"></a>開頭是 ADO.NET 4.6.2

- 針對 Azure SQL 立即重試的連線開啟嘗試，藉此改善啟用雲端的應用程式效能。

#### <a name="starting-with-adonet-461"></a>開頭是 ADO.NET 4.6.1

- 對於 SQL Database，使用 **SqlConnection.Open** 方法來開啟連線時，可靠性更高。 針對連線逾時期間內的特定錯誤， **Open** 方法現在包含最佳重試機制來因應暫時性錯誤。
- 支援連線集區，包括其提供給您的程式的連線物件是否能運作的有效驗證。

當您使用連線集區中的連線物件時，建議您的程式若未立即使用連線，則暫時將它關閉。 重新開啟連線並不會耗費很多資源，但是會建立新的連線。

如果您使用 ADO.NET 4.0 或更舊版本，建議您升級到最新的 ADO.NET。 從 2018 年 8 月起，您可以[下載 ADO.NET 4.6.2](https://blogs.msdn.microsoft.com/dotnet/20../../announcing-the-net-framework-4-7-2/)。

<a id="e-diagnostics-test-utilities-connect" name="e-diagnostics-test-utilities-connect"></a>

## <a name="diagnostics"></a>診斷

<a id="d-test-whether-utilities-can-connect" name="d-test-whether-utilities-can-connect"></a>

### <a name="diagnostics-test-whether-utilities-can-connect"></a>診斷：測試公用程式是否可以連接

如果您的程式無法在 SQL Database 中連線到您的資料庫，您可以使用一個診斷選項來嘗試與公用程式連接。 在理想的情況下，此公用程式會使用您的程式使用的同一程式庫進行連線。

您可以在任何 Windows 電腦上，嘗試這些公用程式：

- SQL Server Management Studio (SSMS.exe)，其使用 ADO.NET 進行連線
- `sqlcmd.exe`，使用 [ODBC](/sql/connect/odbc/microsoft-odbc-driver-for-sql-server) 進行連線

在您的程式連線之後，請測試簡短的 SQL SELECT 查詢是否能運作。

<a id="f-diagnostics-check-open-ports" name="f-diagnostics-check-open-ports"></a>

### <a name="diagnostics-check-the-open-ports"></a>診斷：檢查開啟的連接埠

如果您懷疑連線嘗試因為連接埠問題而失敗，您可以在報告連接埠組態的電腦上執行公用程式。

下列公用程式在 Linux 上可能很有用：

- `netstat -nap`
- `nmap -sS -O 127.0.0.1`：將範例值變更為您的 IP 位址。

在 Windows 上，[PortQry.exe](https://www.microsoft.com/download/details.aspx?id=17148) 公用程式可能很有用。 以下是在 SQL Database 的資料庫上查詢埠狀況，並在膝上型電腦上執行的範例執行：

```cmd
[C:\Users\johndoe\]
>> portqry.exe -n johndoesvr9.database.windows.net -p tcp -e 1433

Querying target system called: johndoesvr9.database.windows.net

Attempting to resolve name to IP address...
Name resolved to 23.100.117.95

querying...
TCP port 1433 (ms-sql-s service): LISTENING

[C:\Users\johndoe\]
>>
```

<a id="g-diagnostics-log-your-errors" name="g-diagnostics-log-your-errors"></a>

### <a name="diagnostics-log-your-errors"></a>診斷：記錄您的錯誤

有時診斷間歇問題的最好方式，就是數天或數週偵測一般模式。

您的用戶端可以記錄其遇到的所有錯誤來協助診斷。 您可以使記錄項目與 SQL Database 本身內部記錄的錯誤資料相互關聯。

Enterprise Library 6 (EntLib60) 提供 .NET 受控類別來協助記錄。 如需詳細資訊，請參閱 [5 - 輕而易舉：使用記錄應用程式區塊](/previous-versions/msp-n-p/dn440731(v=pandp.60))。

<a id="h-diagnostics-examine-logs-errors" name="h-diagnostics-examine-logs-errors"></a>

### <a name="diagnostics-examine-system-logs-for-errors"></a>診斷：檢查系統記錄找出錯誤

以下是一些可查詢錯誤記錄和其他資訊的 Transact-SQL SELECT 陳述式。

| 記錄查詢 | 描述 |
|:--- |:--- |
| `SELECT e.*`<br/>`FROM sys.event_log AS e`<br/>`WHERE e.database_name = 'myDbName'`<br/>`AND e.event_category = 'connectivity'`<br/>`AND 2 >= DateDiff`<br/>&nbsp;&nbsp;`(hour, e.end_time, GetUtcDate())`<br/>`ORDER BY e.event_category,`<br/>&nbsp;&nbsp;`e.event_type, e.end_time;` |[Sys.event_log](/sql/relational-databases/system-catalog-views/sys-event-log-azure-sql-database) 檢視可提供個別事件的資訊，包括會導致暫時性錯誤或連線失敗的某些事件。<br/><br/>在理想的情況下，您可以讓 **start_time** 或 **end_time** 值與用戶端程式發生問題時的相關資訊相互關聯。<br/><br/>您必須連線到 master 資料庫來執行此查詢。 |
| `SELECT c.*`<br/>`FROM sys.database_connection_stats AS c`<br/>`WHERE c.database_name = 'myDbName'`<br/>`AND 24 >= DateDiff`<br/>&nbsp;&nbsp;`(hour, c.end_time, GetUtcDate())`<br/>`ORDER BY c.end_time;` |[Sys.database_connection_stats](/sql/relational-databases/system-catalog-views/sys-database-connection-stats-azure-sql-database)視圖會提供事件種類的匯總計數，以供其他診斷之用。<br/><br/>您必須連線到 master 資料庫來執行此查詢。 |

<a id="d-search-for-problem-events-in-the-sql-database-log" name="d-search-for-problem-events-in-the-sql-database-log"></a>

### <a name="diagnostics-search-for-problem-events-in-the-sql-database-log"></a>診斷：在 SQL Database 記錄中搜尋問題事件

您可以在 SQL Database 記錄中搜尋有關問題事件的項目。 在 *master* 資料庫中嘗試下列 Transact-SQL SELECT 陳述式：

```sql
SELECT
   object_name
  ,CAST(f.event_data as XML).value
      ('(/event/@timestamp)[1]', 'datetime2')                      AS [timestamp]
  ,CAST(f.event_data as XML).value
      ('(/event/data[@name="error"]/value)[1]', 'int')             AS [error]
  ,CAST(f.event_data as XML).value
      ('(/event/data[@name="state"]/value)[1]', 'int')             AS [state]
  ,CAST(f.event_data as XML).value
      ('(/event/data[@name="is_success"]/value)[1]', 'bit')        AS [is_success]
  ,CAST(f.event_data as XML).value
      ('(/event/data[@name="database_name"]/value)[1]', 'sysname') AS [database_name]
FROM
  sys.fn_xe_telemetry_blob_target_read_file('el', null, null, null) AS f
WHERE
  object_name != 'login_event'  -- Login events are numerous.
  and
  '2015-06-21' < CAST(f.event_data as XML).value
        ('(/event/@timestamp)[1]', 'datetime2')
ORDER BY
  [timestamp] DESC
;
```

#### <a name="a-few-returned-rows-from-sysfn_xe_telemetry_blob_target_read_file"></a>數個從 sys.fn_xe_telemetry_blob_target_read_file 傳回的資料列

下列範例顯示傳回的資料列可能的樣子。 顯示的 null 值通常在其他資料列不是 null。

```
object_name                   timestamp                    error  state  is_success  database_name

database_xml_deadlock_report  2015-10-16 20:28:01.0090000  NULL   NULL   NULL        AdventureWorks
```

<a id="l-enterprise-library-6" name="l-enterprise-library-6"></a>

## <a name="enterprise-library-6"></a>Enterprise Library 6

Enterprise Library 6 (EntLib60) 是 .NET 類別的架構，可協助您執行穩固的雲端服務用戶端，其中一個是 SQL Database 的。 若要找出 EntLib60 所能協助之每個領域的專用主題，請參閱 [Enterprise Library 6 - 2013 年 4 月](/previous-versions/msp-n-p/dn169621(v=pandp.10))。

在 EntLib60 可以協助的一個領域中用於處理暫時性錯誤的重試邏輯。 如需詳細資訊，請參閱 [4 - 堅持是所有成功的秘方：使用暫時性錯誤處理應用程式區塊](/previous-versions/msp-n-p/dn440719(v=pandp.60))。

> [!NOTE]
> EntLib60 的原始程式碼可從[下載中心](https://go.microsoft.com/fwlink/p/?LinkID=290898)公開下載。 Microsoft 沒有計劃進一步更新或維護 EntLib 的功能。

<a id="entlib60-classes-for-transient-errors-and-retry" name="entlib60-classes-for-transient-errors-and-retry"></a>

### <a name="entlib60-classes-for-transient-errors-and-retry"></a>用於暫時性錯誤和重試的 EntLib60 類別

下列 EntLib60 類別特別有助於重試邏輯。 這些類別全都位於命名空間 **Microsoft.Practices.EnterpriseLibrary.TransientFaultHandling** 之下。

在命名空間中， **>microsoft.practices.enterpriselibrary.transientfaulthandling. >microsoft.practices.enterpriselibrary.transientfaulthandling** ：

- **RetryPolicy** 類別
  - **ExecuteAction** 方法
- **ExponentialBackoff** 類別
- **SqlDatabaseTransientErrorDetectionStrategy** 類別
- **ReliableSqlConnection** 類別
  - **ExecuteCommand** 方法

在命名空間 **Microsoft.Practices.EnterpriseLibrary.TransientFaultHandling.TestSupport** 中：

- **AlwaysTransientErrorDetectionStrategy** 類別
- **NeverTransientErrorDetectionStrategy** 類別

以下是 EntLib60 相關資訊的一些連結：

- 免費書籍下載：[Microsoft Enterprise Library 開發人員指南第 2 版](https://www.microsoft.com/download/details.aspx?id=41145)。
- 最佳作法： [重試一般指引](/azure/architecture/best-practices/transient-faults) 深入探討重試邏輯。
- NuGet 下載：[Enterprise Library - 暫時性錯誤處理應用程式區塊 6.0](https://www.nuget.org/packages/EnterpriseLibrary.TransientFaultHandling/)。

<a id="entlib60-the-logging-block" name="entlib60-the-logging-block"></a>

### <a name="entlib60-the-logging-block"></a>EntLib60：記錄區塊

- 記錄區塊是高度彈性且可設定的解決方案，您可用於：
  - 建立記錄訊息，並儲存在各種不同的位置中。
  - 分類與篩選訊息。
  - 收集有助於偵錯和追蹤的內容資訊，以及用於稽核和一般記錄需求的內容資訊。
- 記錄區塊可彙總來自記錄目的地的記錄功能，使應用程式程式碼能夠一致，而不必理會目標記錄存放區的的位置和類型。

如需詳細資訊，請參閱 [5 - 輕而易舉：使用記錄應用程式區塊](/previous-versions/msp-n-p/dn440731(v=pandp.60))。

<a id="entlib60-istransient-method-source-code" name="entlib60-istransient-method-source-code"></a>

### <a name="entlib60-istransient-method-source-code"></a>EntLib60 IsTransient 方法的原始程式碼

接下來， **IsTransient** 方法的 C# 原始程式碼來自 **SqlDatabaseTransientErrorDetectionStrategy** 類別。 原始程式碼會釐清哪些錯誤會被視為暫時性並值得重試 (從 2013 年 4 月起)。

```csharp
public bool IsTransient(Exception ex)
{
  if (ex != null)
  {
    SqlException sqlException;
    if ((sqlException = ex as SqlException) != null)
    {
      // Enumerate through all errors found in the exception.
      foreach (SqlError err in sqlException.Errors)
      {
        switch (err.Number)
        {
            // SQL Error Code: 40501
            // The service is currently busy. Retry the request after 10 seconds.
            // Code: (reason code to be decoded).
          case ThrottlingCondition.ThrottlingErrorNumber:
            // Decode the reason code from the error message to
            // determine the grounds for throttling.
            var condition = ThrottlingCondition.FromError(err);

            // Attach the decoded values as additional attributes to
            // the original SQL exception.
            sqlException.Data[condition.ThrottlingMode.GetType().Name] =
              condition.ThrottlingMode.ToString();
            sqlException.Data[condition.GetType().Name] = condition;

            return true;

          case 10928:
          case 10929:
          case 10053:
          case 10054:
          case 10060:
          case 40197:
          case 40540:
          case 40613:
          case 40143:
          case 233:
          case 64:
            // DBNETLIB Error Code: 20
            // The instance of SQL Server you attempted to connect to
            // does not support encryption.
          case (int)ProcessNetLibErrorCode.EncryptionNotSupported:
            return true;
        }
      }
    }
    else if (ex is TimeoutException)
    {
      return true;
    }
    else
    {
      EntityException entityException;
      if ((entityException = ex as EntityException) != null)
      {
        return this.IsTransient(entityException.InnerException);
      }
    }
  }

  return false;
}
```

## <a name="next-steps"></a>後續步驟

- [SQL Database 和 SQL Server 的連線庫](connect-query-content-reference-guide.md#libraries)
- [連接共用 (ADO.NET) ](/dotnet/framework/data/adonet/sql-server-connection-pooling)
- [Retrying 是 Apache 2.0 授權的一般用途重試程式庫 (以 Python 撰寫)](https://pypi.python.org/pypi/retrying)，可簡化可對任何案例新增重試行為的工作。

<!-- Link references. -->

[step-4-connect-resiliently-to-sql-with-ado-net-a78n]: /sql/connect/ado-net/step-4-connect-resiliently-sql-ado-net

[step-4-connect-resiliently-to-sql-with-php-p42h]: /sql/connect/php/step-4-connect-resiliently-to-sql-with-php