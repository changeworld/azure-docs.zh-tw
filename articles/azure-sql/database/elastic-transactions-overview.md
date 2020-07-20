---
title: 跨雲端資料庫的分散式交易
description: 使用 Azure SQL Database 彈性資料庫交易的總覽。
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
ms.openlocfilehash: 5c94234644fcefb70a40ba0b2c21e6e205be0e65
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "85829409"
---
# <a name="distributed-transactions-across-cloud-databases"></a>跨雲端資料庫的分散式交易
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Azure SQL Database 的彈性資料庫交易可讓您在 SQL Database 中執行跨越多個資料庫的交易。 適用于 SQL Database 的彈性資料庫交易適用于使用 ADO .NET 的 .NET 應用程式，並與使用[system.object](https://msdn.microsoft.com/library/system.transactions.aspx)類別的熟悉程式設計經驗整合。 如要取得程式庫，請參閱 [.NET Framework 4.6.1 (Web 安裝程式)](https://www.microsoft.com/download/details.aspx?id=49981)。

在內部部署環境中，這種情況通常需要執行 Microsoft 分散式交易協調器（MSDTC）。 因為 MSDTC 無法用於 Azure 中的平臺即服務應用程式，所以協調分散式交易的功能現在已直接整合到 SQL Database 中。 應用程式可以連接到 SQL Database 中的任何資料庫來啟動分散式交易，其中一個資料庫會以透明的方式協調分散式交易，如下圖所示。

  ![Azure SQL Database 的分散式交易 - 使用彈性資料庫交易 ][1]

## <a name="common-scenarios"></a>常見案例

SQL Database 的彈性資料庫交易可讓應用程式對儲存在 SQL Database 中數個不同資料庫中的資料進行不可部分完成的變更。 預覽版著重於 C# 和 .NET 的用戶端開發經驗。 未來預計加入使用 T-SQL 的伺服器端經驗。  
彈性資料庫交易的目標為下列案例：

* Azure 中的多資料庫應用程式：在此案例中，資料會以垂直方式分割在中的數個資料庫，SQL Database，讓不同種類的資料位於不同的資料庫上。 某些作業需要變更資料，這會保留在兩個或多個資料庫中。 應用程式使用彈性資料庫交易來協調資料庫之間的變更，確保不可部分完成性。
* Azure 中的分區化資料庫應用程式：在此案例中，資料層會使用[彈性資料庫用戶端程式庫](elastic-database-client-library.md)或自我分區化，在 SQL Database 中，以水準方式將資料分割到多個資料庫。 一個顯著的使用案例是在分區化多租用戶應用程式中，當變更牽涉多個租用戶時，需要執行不可部分完成的變更。 例如，從一個租用戶轉移到另一個租用戶，而兩者位於不同的資料庫。 第二個案例是精細的分區化，以因應大型租使用者的容量需求，而這通常表示某些不可部分完成的作業需要在用於相同租使用者的數個資料庫之間進行延展。 第三種案例是以不可部分完成的更新來參考資料庫之間複寫的資料。 現在可以利用預覽版，跨多個資料庫協調這幾方面不可部分完成的交易式作業。
  彈性資料庫交易使用兩階段認可，確保跨資料庫的交易不可部分完成性。 這適合用於在單一交易中一次涉及少於100個資料庫的交易。 這些限制不會強制執行，但會預期彈性資料庫交易的效能和成功率會在超過這些限制時受到影響。

## <a name="installation-and-migration"></a>安裝和移轉

SQL Database 中彈性資料庫交易的功能是透過 .NET 程式庫 System.Data.dll 和 System.Transactions.dll 的更新提供。 DLL 確保必要時使用兩階段交易認可，以確保不可部分完成性。 若要使用彈性資料庫交易來開始開發應用程式，請安裝 [.NET Framework 4.6.1](https://www.microsoft.com/download/details.aspx?id=49981) 或更新版本。 在舊版 .NET Framework 上執行時，交易無法升級為分散式交易，將會引發例外狀況。

安裝之後，您可以在系統中使用分散式交易 Api，並將 SQL Database 的連接。 如果您有使用這些 API 的現有 MSDTC 應用程式，只要在安裝 4.6.1 Framework 之後，為 .NET 4.6 重建現有的應用程式即可。 如果您的專案是以 .NET 4.6 為目標，它們會自動使用新架構版本中更新的 Dll，並將分散式交易 API 呼叫與 SQL Database 的連接結合，現在會成功。

請記住，彈性資料庫交易不需要安裝 MSDTC。 相反地，彈性資料庫交易是由在 SQL Database 中直接管理。 這會大幅簡化雲端案例，因為 MSDTC 部署並不需要搭配 SQL Database 使用分散式交易。 第 4 節更詳細地說明如何將彈性資料庫交易和必要的 .NET Framework 連同您的雲端應用程式一起部署到 Azure。

## <a name="development-experience"></a>開發經驗

### <a name="multi-database-applications"></a>多重資料庫應用程式

下列範例程式碼使用熟悉的 .NET System.Transactions 程式設計經驗。 TransactionScope 類別會在 .NET 中建立環境交易 （「環境交易」是位於目前線程中的一個）。在 TransactionScope 內開啟的所有連接都會參與交易。 如果有不同的資料庫參與，交易會自動提升為分散式交易。 設定完成範圍來指出認可，即可控制交易的結果。

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

SQL Database 的彈性資料庫交易也支援協調分散式交易，您可以在其中使用彈性資料庫用戶端程式庫的 OpenConnectionForKey 方法，針對相應放大的資料層開啟連線。 假設變更跨數個不同分區化索引鍵值，而您需要保證交易一致性。 連接到裝載不同分區化索引鍵值的分區時，由 OpenConnectionForKey 代理連接。 在一般情況下可連接到不同分區，以確保交易保證需要分散式交易。
下列程式碼範例說明此方法。 其中假設使用一個稱為 shardmap 的變數，代表來自彈性資料庫用戶端程式庫的分區對應：

```csharp
    using (var scope = new TransactionScope())
    {
        using (var conn1 = shardmap.OpenConnectionForKey(tenantId1, credentialsStr))
        {
            conn1.Open();
            SqlCommand cmd1 = conn1.CreateCommand();
            cmd1.CommandText = string.Format("insert into T1 values(1)");
            cmd1.ExecuteNonQuery();
        }

        using (var conn2 = shardmap.OpenConnectionForKey(tenantId2, credentialsStr))
        {
            conn2.Open();
            var cmd2 = conn2.CreateCommand();
            cmd2.CommandText = string.Format("insert into T1 values(2)");
            cmd2.ExecuteNonQuery();
        }

        scope.Complete();
    }
```

## <a name="net-installation-for-azure-cloud-services"></a>Azure 雲端服務的 .NET 安裝

Azure 會提供數個供應項目，以裝載 .NET 應用程式。 如需不同供應項目的比較，請參閱 [Azure App Service、雲端服務與虛擬機器之比較](/azure/architecture/guide/technology-choices/compute-decision-tree)。 如果供應項目的客體 OS 小於彈性交易所需的 .NET 4.6.1，則您必須將客體 OS 升級至 4.6.1。

針對 Azure App Service，目前不支援升級至客體作業系統。 對於 Azure 虛擬機器，只要登入 VM，並執行最新的 .NET Framework 的安裝程式即可。 對於 Azure 雲端服務，您需要將新版 .NET 的安裝包含在您部署的啟動工作中。 [在雲端服務角色上安裝 .NET](../../cloud-services/cloud-services-dotnet-install-dotnet.md)中說明概念和步驟。  

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

## <a name="transactions-across-multiple-servers"></a>跨多部伺服器的交易

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]
> [!IMPORTANT]
> Azure SQL Database 仍然支援 PowerShell Azure Resource Manager 模組，但所有未來的開發都是針對 Az .Sql 模組。 如需這些 Cmdlet，請參閱[AzureRM](https://docs.microsoft.com/powershell/module/AzureRM.Sql/)。 Az 模組和 AzureRm 模組中命令的引數本質上完全相同。

在 Azure SQL Database 中，跨不同伺服器支援彈性資料庫交易。 當交易跨越伺服器界限時，參與的伺服器首先必須進入相互通訊關聯性。 一旦建立通訊關聯性之後，任一部伺服器中的任何資料庫都可以和另一部伺服器中的資料庫一起參與彈性交易。 當交易跨越兩部以上的伺服器時，就必須為任何一對伺服器進行通訊關聯性。

使用下列 PowerShell Cmdlet 管理跨伺服器的通訊關聯性，以進行彈性資料庫交易：

* **AzSqlServerCommunicationLink**：使用這個 Cmdlet 在 Azure SQL Database 中的兩部伺服器之間建立新的通訊關聯性。 關聯性是對稱的，這表示這兩部伺服器都可以起始與其他伺服器的交易。
* **AzSqlServerCommunicationLink**：使用這個 Cmdlet 來取出現有的通訊關聯性及其屬性。
* **AzSqlServerCommunicationLink**：使用這個 Cmdlet 移除現有的通訊關聯性。

## <a name="monitoring-transaction-status"></a>監視交易狀態

在 SQL Database 中使用動態管理檢視（Dmv）來監視進行中的彈性資料庫交易的狀態和進度。 與交易相關的所有 Dmv 都與 SQL Database 中的分散式交易相關。 您可以在這裡找到對應的 DMV 清單： [交易相關的動態管理檢視和函數 (Transact-SQL)](https://msdn.microsoft.com/library/ms178621.aspx)。

這些 DMV 特別有用：

* **sys.dm\_tran\_active\_transactions**：列出目前使用中的交易及其狀態。 UOW (工作單位) 資料行可以識別屬於相同分散式交易的不同子交易。 相同分散式交易內的所有交易具有相同的 UOW 值。 如需詳細資訊，請參閱 [DMV 文件](https://msdn.microsoft.com/library/ms174302.aspx) \(機器翻譯\)。
* **sys.dm\_tran\_database\_transactions**：提供交易的其他相關資訊，例如交易在記錄檔中的位置。 如需詳細資訊，請參閱 [DMV 文件](https://msdn.microsoft.com/library/ms186957.aspx) \(機器翻譯\)。
* **sys.dm\_tran\_locks**：提供目前進行中交易所持有的鎖定相關資訊。 如需詳細資訊，請參閱 [DMV 文件](https://msdn.microsoft.com/library/ms190345.aspx) \(機器翻譯\)。

## <a name="limitations"></a>限制

下列限制目前適用于 SQL Database 中的彈性資料庫交易：

* 僅支援 SQL Database 中跨資料庫的交易。 其他[X/OPEN XA](https://en.wikipedia.org/wiki/X/Open_XA)資源提供者和 SQL Database 以外的資料庫無法參與彈性資料庫交易。 這表示彈性資料庫交易無法跨內部部署 SQL Server 和 Azure SQL Database 延展。 對於內部部署的分散式交易，請繼續使用 MSDTC。
* 僅支援來自 .NET 應用程式的用戶端協調交易。 目前已規劃 T-SQL 的伺服器端支援，例如 BEGIN DISTRIBUTED TRANSACTION，但尚未推出。
* 不支援跨 WCF 服務的交易。 例如，您有執行交易的 WCF 服務方法。 納入交易範圍內的呼叫將會失敗，因為 [System.ServiceModel.ProtocolException](https://msdn.microsoft.com/library/system.servicemodel.protocolexception)。

## <a name="next-steps"></a>後續步驟

如有任何問題，請到我們的[Microsoft Q&SQL Database 的問題頁面](https://docs.microsoft.com/answers/topics/azure-sql-database.html)。 對於功能要求，請將其新增至[SQL Database 意見反應論壇](https://feedback.azure.com/forums/217321-sql-database/)。

<!--Image references-->
[1]: ./media/elastic-transactions-overview/distributed-transactions.png
 