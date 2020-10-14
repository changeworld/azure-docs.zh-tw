---
title: '跨雲端資料庫的分散式交易 (preview) '
description: 使用 Azure SQL Database 和 Azure SQL 受控執行個體的彈性資料庫交易總覽。
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 03/12/2019
ms.openlocfilehash: 369f79a436d76e6a1bf1a1ce64f7754f25a5abc5
ms.sourcegitcommit: 1b47921ae4298e7992c856b82cb8263470e9e6f9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/14/2020
ms.locfileid: "92058041"
---
# <a name="distributed-transactions-across-cloud-databases-preview"></a>跨雲端資料庫的分散式交易 (preview) 
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

Azure SQL Database 和 Azure SQL 受控執行個體的彈性資料庫交易可讓您執行跨越數個資料庫的交易。 彈性資料庫交易適用于使用 ADO.NET 的 .NET 應用程式，並與使用「 [系統交易](https://msdn.microsoft.com/library/system.transactions.aspx) 」類別的熟悉程式設計經驗整合。 如要取得程式庫，請參閱 [.NET Framework 4.6.1 (Web 安裝程式)](https://www.microsoft.com/download/details.aspx?id=49981)。
此外，也可以在 [transact-sql](https://docs.microsoft.com/sql/t-sql/language-elements/begin-distributed-transaction-transact-sql)中使用受控執行個體分散式交易。

在內部部署中，這類案例通常需要執行 (MSDTC) 的 Microsoft Distributed Transaction Coordinator。 由於 MSDTC 不適用於 Azure 中的平臺即服務應用程式，因此協調分散式交易的能力現在已直接整合到 SQL Database 或受控執行個體。 應用程式可以連接到任何資料庫來啟動分散式交易，而且其中一個資料庫或伺服器會以透明的方式協調分散式交易，如下圖所示。

本檔中的「分散式交易」和「彈性資料庫交易」都被視為同義字，並且會交替使用。

  ![Azure SQL Database 的分散式交易 - 使用彈性資料庫交易 ][1]

## <a name="common-scenarios"></a>常見的案例

彈性資料庫交易可讓應用程式對儲存在數個不同資料庫中的資料進行不可部分完成的變更。 預覽版著重於 C# 和 .NET 的用戶端開發經驗。 伺服器端體驗 (以預存程式撰寫的程式碼或使用 [transact-sql](https://docs.microsoft.com/sql/t-sql/language-elements/begin-distributed-transaction-transact-sql)) 的伺服器端腳本，僅適用于受控執行個體。
> [!IMPORTANT]
> 在預覽中，目前不支援在 Azure SQL Database 和 Azure SQL 受控執行個體之間執行彈性資料庫交易。 彈性資料庫交易只能跨一組 SQL 資料庫或一組受控實例。

彈性資料庫交易以下列案例為目標：

* Azure 中的多資料庫應用程式：在此案例中，資料會在 SQL Database 或受控執行個體中的多個資料庫之間垂直分割，讓不同類型的資料位於不同的資料庫上。 某些作業需要變更資料，而這些資料會保存在兩個或多個資料庫中。 應用程式使用彈性資料庫交易來協調資料庫之間的變更，確保不可部分完成性。
* Azure 中的分區化資料庫應用程式：在此案例中，資料層會使用 [彈性資料庫用戶端程式庫](elastic-database-client-library.md) 或自我分區化，在 SQL Database 或受控執行個體的多個資料庫之間水準分割資料。 一個顯著的使用案例是在分區化多租用戶應用程式中，當變更牽涉多個租用戶時，需要執行不可部分完成的變更。 例如，從一個租用戶轉移到另一個租用戶，而兩者位於不同的資料庫。 第二個案例是更精細的分區化來容納大型租使用者的容量需求，而這通常表示某些不可部分完成的作業需要在多個用於相同租使用者的資料庫之間延展。 第三種案例是以不可部分完成的更新來參考資料庫之間複寫的資料。 現在可以利用預覽版，跨多個資料庫協調這幾方面不可部分完成的交易式作業。
  彈性資料庫交易會使用兩個階段認可，以確保跨資料庫的交易不可部分完成。 它很適合用於在單一交易中一次牽涉到少於100資料庫的交易。 這些限制並不會強制執行，但在超過這些限制的情況下，可能會有彈性資料庫交易的效能和成功率會受到影響。

## <a name="installation-and-migration"></a>安裝和移轉

彈性資料庫交易的功能是透過 .NET 程式庫 System.Data.dll 和 System.Transactions.dll 的更新提供。 DLL 確保必要時使用兩階段交易認可，以確保不可部分完成性。 若要使用彈性資料庫交易來開始開發應用程式，請安裝 [.NET Framework 4.6.1](https://www.microsoft.com/download/details.aspx?id=49981) 或更新版本。 在舊版 .NET Framework 上執行時，交易無法升級為分散式交易，將會引發例外狀況。

安裝之後，您就可以使用 System. transaction 中的分散式交易 Api 與 SQL Database 和受控執行個體的連接。 如果您有現有的 MSDTC 應用程式使用這些 Api，請在安裝 4.6.1 Framework 之後，重建 .NET 4.6 的現有應用程式。 如果您的專案是以 .NET 4.6 為目標，則會自動使用新 Framework 版本的更新 Dll 和分散式交易 API 呼叫搭配 SQL Database 的連接，或受控執行個體現在會成功。

請記住，彈性資料庫交易不需要安裝 MSDTC。 相反地，彈性資料庫交易是由和服務內的直接管理。 這可大幅簡化雲端案例，因為 MSDTC 的部署不需要搭配 SQL Database 或受控執行個體來使用分散式交易。 第 4 節更詳細地說明如何將彈性資料庫交易和必要的 .NET Framework 連同您的雲端應用程式一起部署到 Azure。

## <a name="net-installation-for-azure-cloud-services"></a>Azure 雲端服務的 .NET 安裝

Azure 會提供數個供應項目，以裝載 .NET 應用程式。 如需不同供應項目的比較，請參閱 [Azure App Service、雲端服務與虛擬機器之比較](/azure/architecture/guide/technology-choices/compute-decision-tree)。 如果供應項目的客體 OS 小於彈性交易所需的 .NET 4.6.1，則您必須將客體 OS 升級至 4.6.1。

針對 Azure App Service，目前不支援對虛擬作業系統進行升級。 對於 Azure 虛擬機器，只要登入 VM，並執行最新的 .NET Framework 的安裝程式即可。 對於 Azure 雲端服務，您需要將新版 .NET 的安裝包含在您部署的啟動工作中。 [在雲端服務角色上安裝 .NET](../../cloud-services/cloud-services-dotnet-install-dotnet.md)中說明概念和步驟。  

請注意，相較於 .NET 4.6 的安裝程式，.NET 4.6.1 的安裝程式在 Azure 雲端服務上進行啟動程序期間，可能需要更多的暫存儲存空間。 為了確保能夠順利安裝，您必須在 ServiceDefinition.csdef 檔案中，於啟動工作的 LocalResources 區段和環境設定中，增加 Azure 雲端服務的暫存儲存體，如以下範例所示：

```xml
    <LocalResources>
    ...
        <LocalStorage name="TEMP" sizeInMB="5000" cleanOnRoleRecycle="false" />
        <LocalStorage name="TMP" sizeInMB="5000" cleanOnRoleRecycle="false" />
    </LocalResources>
    <Startup>
        <Task commandLine="install.cmd" executionContext="elevated" taskType="simple">
            <Environment>
        ...
                <Variable name="TEMP">
                    <RoleInstanceValue xpath="/RoleEnvironment/CurrentInstance/LocalResources/LocalResource[@name='TEMP']/@path" />
                </Variable>
                <Variable name="TMP">
                    <RoleInstanceValue xpath="/RoleEnvironment/CurrentInstance/LocalResources/LocalResource[@name='TMP']/@path" />
                </Variable>
            </Environment>
        </Task>
    </Startup>
```

## <a name="net-development-experience"></a>.NET 開發經驗

### <a name="multi-database-applications"></a>多重資料庫應用程式

下列範例程式碼使用熟悉的 .NET System.Transactions 程式設計經驗。 TransactionScope 類別會在 .NET 中建立環境交易  (「環境交易」是目前線程中的「環境交易」。 ) 在 TransactionScope 內開啟的所有連接都會參與交易。 如果有不同的資料庫參與，交易會自動提升為分散式交易。 設定完成範圍來指出認可，即可控制交易的結果。

```csharp
    using (var scope = new TransactionScope())
    {
        using (var conn1 = new SqlConnection(connStrDb1))
        {
            conn1.Open();
            SqlCommand cmd1 = conn1.CreateCommand();
            cmd1.CommandText = string.Format("insert into T1 values(1)");
            cmd1.ExecuteNonQuery();
        }

        using (var conn2 = new SqlConnection(connStrDb2))
        {
            conn2.Open();
            var cmd2 = conn2.CreateCommand();
            cmd2.CommandText = string.Format("insert into T2 values(2)");
            cmd2.ExecuteNonQuery();
        }

        scope.Complete();
    }
```

### <a name="sharded-database-applications"></a>分區化資料庫應用程式

SQL Database 和受控執行個體的彈性資料庫交易也支援協調分散式交易，其中您可以使用彈性資料庫用戶端程式庫的 OpenConnectionForKey 方法，來開啟相應放大資料層的連接。 假設變更跨數個不同分區化索引鍵值，而您需要保證交易一致性。 連接到裝載不同分區化索引鍵值的分區時，由 OpenConnectionForKey 代理連接。 在一般情況下可連接到不同分區，以確保交易保證需要分散式交易。
下列程式碼範例說明此方法。 其中假設使用一個稱為 shardmap 的變數，代表來自彈性資料庫用戶端程式庫的分區對應：

```csharp
    using (var scope = new TransactionScope())
    {
        using (var conn1 = shardmap.OpenConnectionForKey(tenantId1, credentialsStr))
        {
            SqlCommand cmd1 = conn1.CreateCommand();
            cmd1.CommandText = string.Format("insert into T1 values(1)");
            cmd1.ExecuteNonQuery();
        }

        using (var conn2 = shardmap.OpenConnectionForKey(tenantId2, credentialsStr))
        {
            var cmd2 = conn2.CreateCommand();
            cmd2.CommandText = string.Format("insert into T1 values(2)");
            cmd2.ExecuteNonQuery();
        }

        scope.Complete();
    }
```

## <a name="transact-sql-development-experience"></a>Transact-sql 開發經驗

使用 Transact-sql 的伺服器端分散式交易僅適用于 Azure SQL 受控執行個體。 分散式交易只能在屬於相同 [伺服器信任群組](https://aka.ms/mitrusted-groups)的受控實例之間執行。 在此案例中，受控實例需要使用 [連結的伺服器](https://docs.microsoft.com/sql/relational-databases/linked-servers/create-linked-servers-sql-server-database-engine#TsqlProcedure) 彼此參考。

下列的 Transact-sql 程式碼範例會使用 [BEGIN DISTRIBUTED transaction](https://docs.microsoft.com/sql/t-sql/language-elements/begin-distributed-transaction-transact-sql) 來啟動分散式交易。

```Transact-SQL

    -- Configure the Linked Server
    -- Add one Azure SQL Managed Instance as Linked Server
    EXEC sp_addlinkedserver
        @server='RemoteServer', -- Linked server name
        @srvproduct='',
        @provider='sqlncli', -- SQL Server Native Client
        @datasrc='managed-instance-server.46e7afd5bc81.database.windows.net' -- Managed Instance endpoint

    -- Add credentials and options to this Linked Server
    EXEC sp_addlinkedsrvlogin
        @rmtsrvname = 'RemoteServer', -- Linked server name
        @useself = 'false',
        @rmtuser = '<login_name>',         -- login
        @rmtpassword = '<secure_password>' -- password

    USE AdventureWorks2012;
    GO
    SET XACT_ABORT ON;
    GO
    BEGIN DISTRIBUTED TRANSACTION;
    -- Delete candidate from local instance.
    DELETE AdventureWorks2012.HumanResources.JobCandidate
        WHERE JobCandidateID = 13;
    -- Delete candidate from remote instance.
    DELETE RemoteServer.AdventureWorks2012.HumanResources.JobCandidate
        WHERE JobCandidateID = 13;
    COMMIT TRANSACTION;
    GO
```

## <a name="combining-net-and-transact-sql-development-experience"></a>結合 .NET 和 Transact-sql 開發經驗

使用 System.object 類別的 .NET 應用程式可以結合 TransactionScope 類別與 Transact-sql 語句 BEGIN DISTRIBUTED TRANSACTION。 在 TransactionScope 內，執行 BEGIN DITRIBUTED TRANSACTION 的內部交易會明確地升級為分散式交易。 此外，當第二個 SqlConnecton 在 TransactionScope 中開啟時，它會隱含地升級為分散式交易。 一旦啟動分散式交易之後，所有後續的交易都會要求（不論是來自 .NET 還是 Transact-sql）都會聯結父分散式交易。 因此，BEGIN 語句所起始的所有嵌套交易範圍最後都會出現在相同的交易中，而且 COMMIT/ROLLBACK 語句會對整體結果產生下列影響：
 * COMMIT 語句對 BEGIN 語句所起始的交易範圍不會有任何影響，也就是在完成之前，將不會認可任何結果 ( 在 TransactionScope 物件上叫用 # A1 方法。 如果 TransactionScope 物件在完成之前終結，則在範圍內完成的所有變更都會回復。
 * ROLLBACK 語句會讓整個 TransactionScope 回復。 在 TransactionScope 內登錄新交易的任何嘗試都會在之後失敗，並嘗試在 TransactionScope 物件上叫用 Complete ( # A1。

以下是使用 Transact-sql 將交易明確升級為分散式交易的範例。

```csharp
    using (TransactionScope s = new TransactionScope())
    {
        using (SqlConnection conn = new SqlConnection(DB0_ConnectionString)
        {
            conn.Open();
        
            // Transaction is here promoted to distributed by BEGIN statement
            //
            Helper.ExecuteNonQueryOnOpenConnection(conn, "BEGIN DISTRIBUTED TRAN");
            // ...
        }
     
        using (SqlConnection conn2 = new SqlConnection(DB1_ConnectionString)
        {
            conn2.Open();
            // ...
        }
        
        s.Complete();
    }
```

下列範例顯示在 TransactionScope 內啟動第二個 SqlConnecton 之後，隱含升級為分散式交易的交易。

```csharp
    using (TransactionScope s = new TransactionScope())
    {
        using (SqlConnection conn = new SqlConnection(DB0_ConnectionString)
        {
            conn.Open();
            // ...
        }
        
        using (SqlConnection conn = new SqlConnection(DB1_ConnectionString)
        {
            // Because this is second SqlConnection within TransactionScope transaction is here implicitly promoted distributed.
            //
            conn.Open(); 
            Helper.ExecuteNonQueryOnOpenConnection(conn, "BEGIN DISTRIBUTED TRAN");
            Helper.ExecuteNonQueryOnOpenConnection(conn, lsQuery);
            // ...
        }
        
        s.Complete();
    }
```

## <a name="transactions-across-multiple-servers-for-azure-sql-database"></a>跨多部伺服器的交易以進行 Azure SQL Database

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]
> [!IMPORTANT]
> Azure SQL Database 仍然支援 PowerShell Azure Resource Manager 模組，但所有未來的開發都是針對 Az.Sql 模組。 如需這些 Cmdlet，請參閱 [AzureRM.Sql](https://docs.microsoft.com/powershell/module/AzureRM.Sql/) \(英文\)。 Az 模組和 AzureRm 模組中命令的引數本質上完全相同。

Azure SQL Database 中的不同伺服器之間支援彈性資料庫交易。 當交易跨越伺服器界限時，參與的伺服器必須先進入相互通訊關聯性。 一旦建立通訊關聯性之後，任一部伺服器中的任何資料庫都可以和另一部伺服器中的資料庫一起參與彈性交易。 當交易跨越兩部以上的伺服器時，就必須對任何一對伺服器進行通訊關聯性。

使用下列 PowerShell Cmdlet 管理跨伺服器的通訊關聯性，以進行彈性資料庫交易：

* **AzSqlServerCommunicationLink**：使用此 Cmdlet 在 Azure SQL Database 中的兩部伺服器之間建立新的通訊關聯性。 關聯性是對稱的，這表示這兩部伺服器都可以起始與其他伺服器的交易。
* **AzSqlServerCommunicationLink**：使用此 Cmdlet 來取出現有的通訊關聯性及其屬性。
* **AzSqlServerCommunicationLink**：使用此 Cmdlet 移除現有的通訊關聯性。

## <a name="transactions-across-multiple-servers-for-azure-sql-managed-instance"></a>適用于 Azure SQL 受控執行個體的多部伺服器之間的交易

Azure SQL 受控執行個體中的不同伺服器皆支援分散式交易。 當交易跨越受控執行個體界限時，參與的實例必須先進入相互安全性和通訊關聯性。 這是藉由建立 [伺服器信任群組](https://aka.ms/mitrusted-groups)來完成，這可以在 Azure 入口網站上完成。 如果受管理的實例不在相同的虛擬網路上，則必須設定 [虛擬網路對等互連](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview) ，且網路安全性群組的輸入和輸出規則必須允許所有參與的虛擬網路上的埠5024和11000-12000。

  ![Azure 入口網站上的伺服器信任群組][3]

下圖顯示具有受控實例的伺服器信任群組，可使用 .NET 或 Transact-sql 執行分散式交易。

  ![使用彈性交易與 Azure SQL 受控執行個體的分散式交易][2]

## <a name="monitoring-transaction-status"></a>監視交易狀態

使用動態管理檢視 (Dmv) 來監視進行中的彈性資料庫交易的狀態和進度。 與交易相關的所有 Dmv 都與 SQL Database 和受控執行個體中的分散式交易有關。 您可以在這裡找到對應的 DMV 清單： [交易相關的動態管理檢視和函數 (Transact-SQL)](https://msdn.microsoft.com/library/ms178621.aspx)。

這些 DMV 特別有用：

* **sys.dm\_tran\_active\_transactions**：列出目前使用中的交易及其狀態。 UOW (工作單位) 資料行可以識別屬於相同分散式交易的不同子交易。 相同分散式交易內的所有交易具有相同的 UOW 值。 如需詳細資訊，請參閱 [DMV 檔](https://msdn.microsoft.com/library/ms174302.aspx)集。
* **sys.dm\_tran\_database\_transactions**：提供交易的其他相關資訊，例如交易在記錄檔中的位置。 如需詳細資訊，請參閱 [DMV 檔](https://msdn.microsoft.com/library/ms186957.aspx)集。
* **sys.dm\_tran\_locks**：提供目前進行中交易所持有的鎖定相關資訊。 如需詳細資訊，請參閱 [DMV 檔](https://msdn.microsoft.com/library/ms190345.aspx)集。

## <a name="limitations"></a>限制

下列限制目前適用于 SQL Database 中的彈性資料庫交易：

* 只支援 SQL Database 中的資料庫之間的交易。 SQL Database 之外的其他 [X/OPEN XA](https://en.wikipedia.org/wiki/X/Open_XA) 資源提供者和資料庫無法參與彈性資料庫交易。 這表示彈性資料庫交易無法在內部部署 SQL Server 和 Azure SQL Database 之間延展。 對於內部部署的分散式交易，請繼續使用 MSDTC。
* 僅支援來自 .NET 應用程式的用戶端協調交易。 目前已規劃 T-SQL 的伺服器端支援，例如 BEGIN DISTRIBUTED TRANSACTION，但尚未推出。
* 不支援跨 WCF 服務的交易。 例如，您有執行交易的 WCF 服務方法。 納入交易範圍內的呼叫將會失敗，因為 [System.ServiceModel.ProtocolException](https://msdn.microsoft.com/library/system.servicemodel.protocolexception)。

下列限制目前適用于受控執行個體中的分散式交易：

* 只支援受控執行個體中的資料庫之間的交易。 其他 [X/OPEN XA](https://en.wikipedia.org/wiki/X/Open_XA) 資源提供者和 Azure SQL 之外的資料庫受控執行個體不能參與分散式交易。 這表示分散式交易無法在內部部署 SQL Server 和 Azure SQL 受控執行個體之間延展。 對於內部部署的分散式交易，請繼續使用 MSDTC。
* 不支援跨 WCF 服務的交易。 例如，您有執行交易的 WCF 服務方法。 納入交易範圍內的呼叫將會失敗，因為 [System.ServiceModel.ProtocolException](https://msdn.microsoft.com/library/system.servicemodel.protocolexception)。
* Azure SQL 受控執行個體必須是 [伺服器信任群組](https://aka.ms/mitrusted-groups) 的一部分，才能參與分散式交易。
* [伺服器信任群組](https://aka.ms/mitrusted-groups)的限制會影響分散式交易。
* 參與分散式交易的受控實例必須具有私人端點的連線， (使用私人 IP 位址從部署) 的虛擬網路，然後必須使用私人 Fqdn 進行相互參考。 用戶端應用程式可以使用私人端點上的分散式交易。 此外，在 Transact-sql 會利用參考私人端點的連結伺服器時，用戶端應用程式也可以使用公用端點上的分散式交易。 下圖將說明這項限制。
  ![私人端點連線能力限制][4]
## <a name="next-steps"></a>後續步驟

* 如有疑問，請洽詢 [Microsoft 問&SQL Database 的問題頁面](https://docs.microsoft.com/answers/topics/azure-sql-database.html)。
* 針對功能要求，請將其新增至 [SQL Database 意見反應論壇](https://feedback.azure.com/forums/217321-sql-database/) 或 [受控執行個體論壇](https://feedback.azure.com/forums/915676-sql-managed-instance)。



<!--Image references-->
[1]: ./media/elastic-transactions-overview/distributed-transactions.png
[2]: ./media/elastic-transactions-overview/sql-mi-distributed-transactions.png
[3]: ./media/elastic-transactions-overview/server-trust-groups-azure-portal.png
[4]: ./media/elastic-transactions-overview/managed-instance-distributed-transactions-private-endpoint-limitations.png
 
