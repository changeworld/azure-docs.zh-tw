---
title: Azure HDInsight 中的 Storm 適用的 SCP.NET 程式設計指南
description: 了解如何使用 SCP.NET 建立以 .NET 為基礎的 Storm 拓撲，用於在 Azure HDInsight 中執行的 Storm。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 01/13/2020
ms.openlocfilehash: ddf69a75a39911293277a4a4189cf4e79256e09d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "77186869"
---
# <a name="scp-programming-guide-for-apache-storm-in-azure-hdinsight"></a>Azure HDInsight 中阿帕奇風暴的 SCP 程式設計指南

SCP 是構建即時、可靠、一致和高效能資料處理應用程式的平臺。 它建立在[Apache風暴](https://storm.incubator.apache.org/)之上，這是一個由開源軟體社區設計的流處理系統。 南森·馬茲創造了風暴 它被推特作為開放源碼出版。 風暴使用[阿帕奇動物園，](https://zookeeper.apache.org/)這是另一個Apache專案，使高度可靠的分散式協調和狀態管理。

SCP 專案不僅在 Windows 上移植了 Storm，而且還移植了 Windows 環境的專案添加擴展和自訂。 擴展包括 .NET 開發人員體驗和 .NET 庫。 該自訂項包括基於 Windows 的部署。

使用擴展和自訂，您無需分叉開源軟體專案。 您可以使用構建在 Storm 之上的派生環境。

## <a name="processing-model"></a>處理模型

SCP 中的資料模擬成連續的 Tuple 串流。 通常，元：

1. 流入佇列。
1. 由在 Storm 拓撲中託管的業務邏輯拾取和轉換。
1. 要麼將其輸出作為元/數傳送到另一個 SCP 系統，要麼被提交到分散式檔案系統和 SQL Server 等存儲中。

![佇列將資料摘要到處理的關係圖，該圖反過來又為數據存儲供電](./media/apache-storm-scp-programming-guide/queue-feeding-data-to-processing-to-data-store.png)

在 Storm 中，應用程式拓撲定義計算圖。 拓撲中的每個節點都包含處理邏輯。 節點之間的連結指示資料流程。

將輸入資料注入拓撲的節點稱為_spout。_ 您可以使用它們對資料進行排序。 輸入資料可能來自檔日誌、事務資料庫或系統效能計數器等源。

同時具有輸入和輸出資料流程的節點稱為_螺栓_。 它們執行實際的資料篩選、選擇和聚合。

SCP 支援盡力而為，至少一次，一次資料處理。

在分散式流處理應用程式中，資料處理期間可能會出現錯誤。 此類錯誤包括網路中斷、電腦故障或代碼中的錯誤。 至少一次處理通過在發生錯誤時自動重播相同的資料來確保至少處理一次所有資料。

至少一次處理簡單可靠，並且適用于許多應用。 但是，當應用程式需要精確計數時，至少一次處理是不夠的，因為相同的資料可以在應用程式拓撲中重播。 在這種情況下，即使多次重播和處理資料，也可確保結果正確。

SCP 允許 .NET 開發人員在使用 JAVA 虛擬機器 （JVM） 時創建即時資料處理應用程式。 JVM 和 .NET 通過 TCP 本地通訊端進行通信。 每個分出/螺栓都是 .NET/JAVA 進程對，其中使用者邏輯作為外掛程式在 .NET 進程中運行。

要在 SCP 之上構建資料處理應用程式，請按照以下步驟操作：

1. 設計和實現樣出，以便從佇列中提取資料。
1. 設計和實現處理輸入資料的螺栓並將其保存到外部存儲（如資料庫）
1. 設計拓撲，然後提交並運行它。

拓撲定義頂點和它們之間流動的資料。 SCP 採用拓撲規範並將其部署到 Storm 群集上，其中每個頂點在一個邏輯節點上運行。 Storm 任務計畫程式負責容錯移轉和縮放。

本文使用一些簡單示例來演練如何使用 SCP 構建資料處理應用程式。

## <a name="scp-plug-in-interface"></a>SCP 外掛程式介面

SCP 外掛程式是獨立應用程式。 它們可以在開發期間在 Visual Studio 中運行，並在生產部署後插入 Storm 管道。

編寫 SCP 外掛程式與編寫任何其他 Windows 主控台應用程式相同。 SCP.NET平臺聲明一些介面用於噴口/螺栓。 您的外掛程式代碼實現這些介面。 此設計的主要目的是讓您專注于業務邏輯，同時讓SCP.NET平臺處理其他事情。

您的外掛程式代碼實現了以下介面之一。 哪個介面取決於拓撲是事務性的還是非事務性的，以及元件是噴口還是螺栓。

* **ISCPSpout**
* **ISCPBolt**
* **ISCPTxSpout**
* **ISCPBatchBolt**

### <a name="iscpplugin"></a>ISCPPlugin

**ISCPPlugin**是許多外掛程式的常見介面，目前，它是一個虛擬的介面。

```csharp
public interface ISCPPlugin
{
}
```

### <a name="iscpspout"></a>ISCPSpout

**ISCPSpout**是非事務性噴口的介面。

```csharp
public interface ISCPSpout : ISCPPlugin
{
    void NextTuple(Dictionary<string, Object> parms);
    void Ack(long seqId, Dictionary<string, Object> parms); 
    void Fail(long seqId, Dictionary<string, Object> parms);
}
```

當調用**NextTuple**時，您的 C# 代碼可能會發出一個或多個元組。 如果沒有什麼可排放的，則此方法應該返回而不發出任何內容。

**NextTuple、Ack**和**Fail**方法都在 C# 進程的單一執行緒中的緊密迴圈中調用。 **Ack** 當沒有要發出的元組時，讓**NextTuple**睡眠短時間（如 10 毫秒）。 此睡眠有助於避免浪費 CPU 可用性。

僅當規範檔啟用確認機制時，才調用**Ack**和**Fail**方法。 *seqId*參數標識已確認或已失敗的元組。 如果在非事務性拓撲中啟用了確認，則應在出點中使用以下**Emit**函數：

```csharp
public abstract void Emit(string streamId, List<object> values, long seqId);
```

如果非事務拓撲不支援確認，**則 Ack**和**Fail**可以保留為空函數。

這些函數中的*parms*輸入參數指定一個空字典，並保留以供將來使用。

### <a name="iscpbolt"></a>ISCPBolt

**ISCPBolt**是非事務性螺栓的介面。

```csharp
public interface ISCPBolt : ISCPPlugin
{
void Execute(SCPTuple tuple);
}
```

當新的元組可用時，將調用**Execute**函數來處理它。

### <a name="iscptxspout"></a>ISCPTxSpout

**ISCPTxSpout**是事務噴口的介面。

```csharp
public interface ISCPTxSpout : ISCPPlugin
{
    void NextTx(out long seqId, Dictionary<string, Object> parms);  
    void Ack(long seqId, Dictionary<string, Object> parms);         
    void Fail(long seqId, Dictionary<string, Object> parms);        
}
```

就像他們的非事務性對應項一樣 **，NextTx、Ack**和**Fail**都在 C# 進程的單一執行緒中的緊密迴圈中調用。 **Ack** 當沒有要發出的元數時，讓**NextTx**睡眠短時間（如 10 毫秒）。 此睡眠有助於避免浪費 CPU 可用性。

當**NextTx**調用以啟動新事務時 *，seqId*輸出參數標識事務。 該事務也用於**Ack**和**失敗**。 **NextTx**方法可以向 JAVA 端發出資料。 資料會儲存在 ZooKeeper 中以支援重播。 由於 ZooKeeper 的容量有限，因此代碼應僅發出中繼資料，而不應在事務噴口中發出批量資料。

由於 Storm 會自動重播失敗的交易，因此通常不會調用**失敗**。 但是，如果 SCP 可以檢查事務噴口發出的中繼資料，則可以在中繼資料無效時調用**Fail。**

這些函數中的*parms*輸入參數指定一個空字典，並保留以供將來使用。

### <a name="iscpbatchbolt"></a>ISCPBatchBolt

**ISCPBatchBolt**是事務螺栓的介面。

```csharp
public interface ISCPBatchBolt : ISCPPlugin
{
    void Execute(SCPTuple tuple);
    void FinishBatch(Dictionary<string, Object> parms);  
}
```

當新元組到達螺栓時，將調用**Execute**方法。 此事務結束時調用**FinishBatch**方法。 *parms*輸入參數保留供將來使用。

對於事務拓撲 **，StormTx嘗試**是一個重要的類。 它有兩個成員 **：TxId**和**嘗試 Id**。 **TxId**成員標識特定事務。 如果事務失敗並重播，可能會嘗試多次。

SCP.NET創建新的**ISCPBatchBolt**物件來處理每個**StormTx嘗試**物件，就像 Storm 在 JAVA 中所做的一樣。 此設計的目的是支援並行交易處理。 事務嘗試完成後，將銷毀相應的**ISCPBatchBolt**物件並收集垃圾。

## <a name="object-model"></a>物件模型

SCP.NET 也提供一組簡單的關鍵物件供開發人員在程式設計中使用。 物件是**上下文**、**狀態存儲**和**SCPRuntime**。 本節將討論它們。

### <a name="context"></a>Context

**上下文**物件向應用程式提供正在運行的環境。 ISCPSpout、ISCPBolt、ISCPTxSpout 或**ISCPTxSpout** **ISCPBatchBolt**的每個**ISCPPlugin**實例都有相應的**上下文**實例。 **ISCPSpout** **ISCPBolt** **上下文**提供的功能分為以下兩部分：

* 靜態部件，可在整個 C# 流程中提供
* 動態部分，僅適用于特定**上下文**實例

### <a name="static-part"></a>靜態零件

```csharp
public static ILogger Logger = null;
public static SCPPluginType pluginType;
public static Config Config { get; set; }
public static TopologyContext TopologyContext { get; set; }  
```

**記錄器**物件是為日誌記錄目的提供的。

**外掛程式類型**物件指示 C# 進程的外掛程式類型。 如果進程在本地測試模式下運行，而不使用 JAVA，則外掛程式類型**將SCP_NET_LOCAL**。

```csharp
public enum SCPPluginType 
{
    SCP_NET_LOCAL = 0,
    SCP_NET_SPOUT = 1,
    SCP_NET_BOLT = 2,
    SCP_NET_TX_SPOUT = 3,
    SCP_NET_BATCH_BOLT = 4  
    }
```

**Config**屬性從 JAVA 端獲取配置參數，在 C# 外掛程式初始化時傳遞這些參數。 **配置**參數分為兩部分：**風暴Conf**和**外掛程式Conf。**

```csharp
public Dictionary<string, Object> stormConf { get; set; }  
public Dictionary<string, Object> pluginConf { get; set; }  
```

**風暴Conf**部分是由 Storm 定義的參數，**外掛程式Conf**部分是 SCP 定義的參數。 以下是範例：

```csharp
public class Constants
{
    … …

    // constant string for pluginConf
    public static readonly String NONTRANSACTIONAL_ENABLE_ACK = "nontransactional.ack.enabled";  

    // constant string for stormConf
    public static readonly String STORM_ZOOKEEPER_SERVERS = "storm.zookeeper.servers";
    public static readonly String STORM_ZOOKEEPER_PORT = "storm.zookeeper.port";
}
```

**拓撲上下文**類型獲取拓撲上下文。 它對於多個並行元件最有用。 以下是範例：

```csharp
//demo how to get TopologyContext info
if (Context.pluginType != SCPPluginType.SCP_NET_LOCAL)
{
    Context.Logger.Info("TopologyContext info:");
    TopologyContext topologyContext = Context.TopologyContext;
    Context.Logger.Info("taskId: {0}", topologyContext.GetThisTaskId());
    taskIndex = topologyContext.GetThisTaskIndex();
    Context.Logger.Info("taskIndex: {0}", taskIndex);
    string componentId = topologyContext.GetThisComponentId();
    Context.Logger.Info("componentId: {0}", componentId);
    List<int> componentTasks = topologyContext.GetComponentTasks(componentId);  
    Context.Logger.Info("taskNum: {0}", componentTasks.Count);
}
```

### <a name="dynamic-part"></a>動態零件

以下介面與特定**上下文**實例相關，該實例由SCP.NET平臺創建並傳遞給代碼：

```csharp
// Declare the Output and Input Stream Schemas

public void DeclareComponentSchema(ComponentStreamSchema schema);

// Emit tuple to default stream.
public abstract void Emit(List<object> values);

// Emit tuple to the specific stream.
public abstract void Emit(string streamId, List<object> values);  
```

對於支援確認的非事務式噴口，提供了以下方法：

```csharp
// for nontransactional spout that supports ack
public abstract void Emit(string streamId, List<object> values, long seqId);  
```

支援確認的非事務性螺栓應顯式調用**Ack**或**失敗**，並與其收到的元組進行連接。 發射新元組時，螺栓還必須指定元組錨。 提供了以下方法：

```csharp
public abstract void Emit(string streamId, IEnumerable<SCPTuple> anchors, List<object> values);
public abstract void Ack(SCPTuple tuple);
public abstract void Fail(SCPTuple tuple);
```

### <a name="statestore"></a>StateStore

**StateStore**物件提供中繼資料服務、單調序列生成和無等待協調。 您可以在**StateStore**上構建更高級別的分散式併發抽象。 這些抽象包括分散式鎖、分散式佇列、障礙和事務服務。

SCP 應用程式可以使用**狀態**物件對[Apache ZooKeeper](https://zookeeper.apache.org/)中的資訊進行序列化。 此功能對於事務拓撲尤其有價值。 如果事務噴口停止回應並重新啟動，**狀態**可以從 ZooKeeper 檢索必要的資訊並重新啟動管道。

**StateStore**物件具有以下主要方法：

```csharp
/// <summary>
/// Static method to retrieve a state store of the given path and connStr 
/// </summary>
/// <param name="storePath">StateStore path</param>
/// <param name="connStr">StateStore address</param>
/// <returns>Instance of StateStore</returns>
public static StateStore Get(string storePath, string connStr);

/// <summary>
/// Create a new state object in this state store instance
/// </summary>
/// <returns>State from StateStore</returns>
public State Create();

/// <summary>
/// Retrieve all states that were previously uncommitted, excluding all exited states
/// </summary>
/// <returns>Uncommitted states</returns>
public IEnumerable<State> GetUnCommitted();

/// <summary>
/// Get all the states in the StateStore
/// </summary>
/// <returns>All the states</returns>
public IEnumerable<State> States();

/// <summary>
/// Get state or registry object
/// </summary>
/// <param name="info">Registry name (registry only)</param>
/// <typeparam name="T">Type, registry or state</typeparam>
/// <returns>Return registry or state</returns>
public T Get<T>(string info = null);

/// <summary>
/// List all the committed states
/// </summary>
/// <returns>Registries containing the committed state </returns>
public IEnumerable<Registry> Committed();

/// <summary>
/// List all the exited states in the StateStore
/// </summary>
/// <returns>Registries containing the exited states</returns>
public IEnumerable<Registry> Aborted();

/// <summary>
/// Retrieve an existing state object from this state store instance 
/// </summary>
/// <returns>State from StateStore</returns>
/// <typeparam name="T">stateId, id of the State</typeparam>
public State GetState(long stateId)
```

**狀態**物件具有以下主要方法：

```csharp
/// <summary>
/// Set the status of the state object to commit
/// </summary>
public void Commit(bool simpleMode = true);

/// <summary>
/// Set the status of the state object to exit
/// </summary>
public void Abort();

/// <summary>
/// Put an attribute value under the given key
/// </summary>
/// <param name="key">Key</param>
/// <param name="attribute">State attribute</param>
    public void PutAttribute<T>(string key, T attribute);

/// <summary>
/// Get the attribute value associated with the given key
/// </summary>
/// <param name="key">Key</param>
/// <returns>State attribute</returns>
    public T GetAttribute<T>(string key);
```

當**simpleMode**設置為**true**時，**提交**方法將刪除 ZooKeeper 中相應的 ZNode。 否則，該方法將刪除當前 ZNode 並在"已提交路徑"\_中添加新節點。

### <a name="scpruntime"></a>SCPRuntime

**SCPRuntime**類提供以下兩種方法：

```csharp
public static void Initialize();

public static void LaunchPlugin(newSCPPlugin createDelegate);  
```

**初始化**方法初始化 SCP 運行時環境。 在此方法中，C# 進程連接到 JAVA 端以獲取配置參數和拓撲上下文。

**LaunchPlugin**方法啟動消息處理迴圈。 在此迴圈中，C# 外掛程式接收來自 JAVA 端的消息。 這些消息包括元數和控制信號。 然後，外掛程式處理消息，可能通過調用代碼提供的介面方法。

**LaunchPlugin**的輸入參數是委託。 該方法可以返回實現 ISCPSpout、ISCPBolt、ISCPTxSpout 或**ISCPBatchBolt**介面的物件。 **ISCPSpout** **ISCPBolt** **ISCPTxSpout**

```csharp
public delegate ISCPPlugin newSCPPlugin(Context ctx, Dictionary<string, Object> parms);
```

對於**ISCPBatchBolt，** 可以從*parms*參數獲取**StormTxAit**物件，並用它來判斷嘗試是否是重播的嘗試。 重播嘗試的檢查通常在提交螺栓處完成。 本文後面的 HelloWorldTx 示例演示了此檢查。

SCP 外掛程式通常可以在兩種模式下運行：本地測試模式和常規模式。

#### <a name="local-test-mode"></a>本地測試模式

在此模式下，C# 代碼中的 SCP 外掛程式在開發階段在 Visual Studio 內運行。 在此模式下可以使用**ILocalCoNtext**介面。 該介面提供了將發出的元數序列化到本地檔並將其讀回 RAM 的方法。

```csharp
public interface ILocalContext
{
    List<SCPTuple> RecvFromMsgQueue();
    void WriteMsgQueueToFile(string filepath, bool append = false);  
    void ReadFromFileToMsgQueue(string filepath);
}
```

#### <a name="regular-mode"></a>常規模式

在此模式下，Storm JAVA 進程運行 SCP 外掛程式。下面是一個示例：

```csharp
namespace Scp.App.HelloWorld
{
public class Generator : ISCPSpout
{
    … …
    public static Generator Get(Context ctx, Dictionary<string, Object> parms)
    {
    return new Generator(ctx);
    }
}

class HelloWorld
{
    static void Main(string[] args)
    {
    /* Setting the environment variable here can change the log file name */
    System.Environment.SetEnvironmentVariable("microsoft.scp.logPrefix", "HelloWorld");

    SCPRuntime.Initialize();
    SCPRuntime.LaunchPlugin(new newSCPPlugin(Generator.Get));
    }
}
}
```

## <a name="topology-specification-language"></a>拓撲規範語言

SCP 拓撲規範是一種特定于域的語言 （DSL），用於描述和配置 SCP 拓撲。 它基於[風暴的Clojure DSL，](https://storm.incubator.apache.org/documentation/Clojure-DSL.html)由SCP擴展。

您可以直接將拓撲規範提交到 Storm 群集，以便通過**runSpec**命令執行。

SCP.NET添加了以下函數來定義事務拓撲：

| 新增函式 | 參數 | 描述 |
| --- | --- | --- |
| **tx-topolopy** |*topology-name*<br />*spout-map*<br />*bolt-map* |使用拓撲名稱、樣條線定義映射和螺栓定義映射定義事務拓撲。 |
| **scp-tx-spout** |*exec-name*<br />*阿格斯*<br />*欄位* |定義事務噴口。 該函數運行由*exec-name*指定的應用程式，並使用*args*。<br /><br />*欄位*參數指定出花口的輸出欄位。 |
| **scp-tx-batch-bolt** |*exec-name*<br />*阿格斯*<br />*欄位* |定義事務批次處理螺栓。 該函數運行由*exec-name*指定的應用程式並使用*args。*<br /><br />*欄位*參數指定螺栓的輸出欄位。 |
| **scp-tx-commit-bolt** |*exec-name*<br />*阿格斯*<br />*欄位* |定義事務提交螺栓。 該函數運行由*exec-name*指定的應用程式，並使用*args*。<br /><br />*欄位*參數指定螺栓的輸出欄位。 |
| **非tx拓撲** |*topology-name*<br />*spout-map*<br />*bolt-map* |使用拓撲名稱、噴口定義映射和螺栓定義映射定義非事務性拓撲。 |
| **scp-spout** |*exec-name*<br />*阿格斯*<br />*欄位*<br />*參數* |定義非事務性噴口。 該函數運行由*exec-name*指定的應用程式，並使用*args*。<br /><br />*欄位*參數指定出花口的輸出欄位。<br /><br />*參數參數*是可選的。 使用它指定參數，如"啟用非事務性.ack"。 |
| **scp-bolt** |*exec-name*<br />*阿格斯*<br />*欄位*<br />*參數* |定義非事務性螺栓。 該函數運行由*exec-name*指定的應用程式，並使用*args*。<br /><br />*欄位*參數指定螺栓的輸出欄位<br /><br />*參數參數*是可選的。 使用它指定參數，如"啟用非事務性.ack"。 |

SCP.NET定義以下關鍵字：

| 關鍵字 | 描述 |
| --- | --- |
| **：名稱** |拓撲名稱 |
| **：拓撲** |使用上表中的函數和內建函數的拓撲 |
| **:p** |每個出口或螺栓的並行提示 |
| **：配置** |是配置參數還是更新現有參數 |
| **：schema** |流的架構 |

SCP.NET還定義了這些常用參數：

| 參數 | 描述 |
| --- | --- |
| "plugin.name" |C# 外掛程式的 .exe 檔案名 |
| "plugin.args" |外掛程式參數 |
| "output.schema" |輸出架構 |
| "nontransactional.ack.enabled" |是否為非事務性拓撲啟用確認 |

**runSpec**命令與位一起部署。 下面是命令用法：

```csharp
.\bin\runSpec.cmd
usage: runSpec [spec-file target-dir [resource-dir] [-cp classpath]]
ex: runSpec examples\HelloWorld\HelloWorld.spec specs examples\HelloWorld\Target
```

*資源-dir*參數是可選的。 在要插入 C# 應用程式時指定它。 指定的目錄包含應用程式、依賴項和配置。

*classpath* 參數也是選擇性。 如果規範檔包含 JAVA 出點或螺栓，則指定 JAVA 類路徑。

## <a name="miscellaneous-features"></a>雜項功能

### <a name="input-and-output-schema-declarations"></a>輸入和輸出架構聲明

您的 C# 進程可以發出 tup。 為此，平臺將元數序列化為**位元組*** 物件，並將物件傳輸到 JAVA 端。 風暴然後將這些元數轉移到目標。

在下游元件中，C# 進程從 JAVA 端接收元組並將其轉換為平臺的原始類型。 所有這些操作都由平臺隱藏。

為了支援序列化和反序列化，代碼需要聲明輸入和輸出的架構。 架構定義為字典。 流 ID 是字典金鑰。 鍵值是列的類型。 元件可以聲明多個流。

```csharp
public class ComponentStreamSchema
{
    public Dictionary<string, List<Type>> InputStreamSchema { get; set; }
    public Dictionary<string, List<Type>> OutputStreamSchema { get; set; }
    public ComponentStreamSchema(Dictionary<string, List<Type>> input, Dictionary<string, List<Type>> output)
    {
        InputStreamSchema = input;
        OutputStreamSchema = output;
    }
}
```

以下函數將添加到**上下文**物件：

```csharp
public void DeclareComponentSchema(ComponentStreamSchema schema)
```

開發人員必須確保發出的元數符合為流定義的架構。 否則，系統將引發運行時異常。

### <a name="multistream-support"></a>多流支援

SCP 允許您的代碼同時向多個不同的流發出或接收。 **上下文**物件將此支援作為**Emit**方法的可選流 ID 參數反映。

添加了SCP.NET**上下文**物件中的兩種方法。 它們向特定流發出一個或多個元數。 *streamId*參數是一個字串。 其值在 C# 代碼和拓撲定義規範中必須相同。

```csharp
/* Emit tuple to the specific stream. */
public abstract void Emit(string streamId, List<object> values);

/* for nontransactional spout only */
public abstract void Emit(string streamId, List<object> values, long seqId);
```

向不存在的流發出會導致運行時異常。

### <a name="fields-grouping"></a>欄位分組

Storm 中的內置欄位分組在SCP.NET中無法正常工作。 在 JAVA 代理端，所有欄位的資料類型實際上是**位元組***。 欄位分組使用**位元組*** 物件的雜湊代碼執行分組。 雜湊代碼是 RAM 中此物件的位址。 因此，對於共用相同內容但不相同位址的多位元組物件的分組是錯誤的。

SCP.NET添加自訂分組方法，它使用**位元組*** 物件的內容執行分組。 在規範檔中，語法如下所示：

```csharp
(bolt-spec
    {
        "spout_test" (scp-field-group :non-tx [0,1])
    }
    …
)
```

在前面的規範檔中：

* `scp-field-group`指定分組是由 SCP 實現的自訂欄位分組。
* `:tx`或`:non-tx`指定拓撲是事務性的。 您需要此資訊，因為起始索引在事務拓撲和非事務拓撲之間是不同的。
* `[0,1]`指定以零開頭的雜湊欄位指示集。

### <a name="hybrid-topology"></a>混合式拓撲

本機風暴代碼用 JAVA 編寫。 SCP.NET增強了 Storm，允許您編寫 C# 代碼來處理業務邏輯。 但是SCP.NET也支援混合拓撲，它不僅包含 C# 噴口/螺栓，還包含 JAVA 噴口/螺栓。

### <a name="specify-java-spoutbolt-in-a-specification-file"></a>在規範檔中指定 JAVA 出點/螺栓

您可以在規範檔中使用**scp-spout**和**scp-bolt**來指定 JAVA 樣嘴和螺栓。 以下是範例：

```csharp
(spout-spec 
  (microsoft.scp.example.HybridTopology.Generator.)
  :p 1)
```

下面是`microsoft.scp.example.HybridTopology.Generator`JAVA 出口類的名稱。

### <a name="specify-the-java-classpath-in-a-runspec-command"></a>在 runSpec 命令中指定 JAVA 類路徑

如果要提交包含 JAVA 出口或螺栓的拓撲，首先編譯它們以生成 JAR 檔。 然後指定提交拓撲時包含 JAR 檔的 java 類路徑。 以下是範例：

```csharp
bin\runSpec.cmd examples\HybridTopology\HybridTopology.spec specs examples\HybridTopology\net\Target -cp examples\HybridTopology\java\target\*
```

`examples\HybridTopology\java\target\`此處是包含 JAVA 出樣/螺栓 JAR 檔的資料夾。

### <a name="serialization-and-deserialization-between-java-and-c"></a>JAVA 和 C 之間的序列化和反序列化#

SCP 元件包括 JAVA 端和 C# 端。 要與本機 JAVA 語音/螺栓進行交互，必須在 JAVA 端和 C# 端之間進行序列化和反序列化，如下圖所示：

![將 JAVA 元件發送到 SCP 元件的關係圖，然後發送到其他 JAVA 元件](./media/apache-storm-scp-programming-guide/java-compent-sending-to-scp-component-sending-to-java-component.png)

#### <a name="serialization-in-the-java-side-and-deserialization-in-the-c-side"></a>JAVA 端的序列化和 C# 端的序列化

首先提供 JAVA 端序列化和 C# 端反序列化的預設實現。

在規範檔中指定 JAVA 端的序列化方法。

```csharp
(scp-bolt
    {
        "plugin.name" "HybridTopology.exe"
        "plugin.args" ["displayer"]
        "output.schema" {}
        "customized.java.serializer" ["microsoft.scp.storm.multilang.CustomizedInteropJSONSerializer"]
    })
```

在 C# 代碼中指定 C# 端的反序列化方法。

```csharp
Dictionary<string, List<Type>> inputSchema = new Dictionary<string, List<Type>>();
inputSchema.Add("default", new List<Type>() { typeof(Person) });
this.ctx.DeclareComponentSchema(new ComponentStreamSchema(inputSchema, null));
this.ctx.DeclareCustomizedDeserializer(new CustomizedInteropJSONDeserializer());
```  

如果資料類型不太複雜，則此預設實現應處理大多數情況。 以下是您可以插入自己的實現的情況：

* 對於預設實現來說，資料類型太複雜了。
* 預設實現的性能不符合您的要求。

JAVA 端的序列化介面定義為：

```csharp
public interface ICustomizedInteropJavaSerializer {
    public void prepare(String[] args);
    public List<ByteBuffer> serialize(List<Object> objectList);
}
```

C# 端中的反序列化介面定義為：

```csharp
public interface ICustomizedInteropCSharpDeserializer
{
    List<Object> Deserialize(List<byte[]> dataList, List<Type> targetTypes);
}
```

#### <a name="serialization-in-the-c-side-and-deserialization-in-the-java-side"></a>C# 端的序列化和 JAVA 端的反序列化

在 C# 代碼中指定 C# 端的序列化方法。

```csharp
this.ctx.DeclareCustomizedSerializer(new CustomizedInteropJSONSerializer()); 
```

在規範檔中指定 JAVA 端的反序列化方法。

```csharp
(scp-spout
   {
     "plugin.name" "HybridTopology.exe"
     "plugin.args" ["generator"]
     "output.schema" {"default" ["person"]}
     "customized.java.deserializer" ["microsoft.scp.storm.multilang.CustomizedInteropJSONDeserializer" "microsoft.scp.example.HybridTopology.Person"]
   }
)
```

此處`"microsoft.scp.storm.multilang.CustomizedInteropJSONDeserializer"`是反序列化器的名稱，是`"microsoft.scp.example.HybridTopology.Person"`資料反序列化的目標類。

您還可以插入自己的 C# 序列化器和 JAVA 反序列化器的實現。

此代碼是 C# 序列化器的介面：

```csharp
public interface ICustomizedInteropCSharpSerializer
{
    List<byte[]> Serialize(List<object> dataList);
}
```

此代碼是 JAVA 反序列化器的介面：

```csharp
public interface ICustomizedInteropJavaDeserializer {
    public void prepare(String[] targetClassNames);
    public List<Object> Deserialize(List<ByteBuffer> dataList);
}
```

## <a name="scp-host-mode"></a>SCP 主機模式

在此模式下，您可以將代碼編譯為 DLL，並使用 SCP 提供的 SCPHost.exe 提交拓撲。 規範檔如下所示：

```csharp
(scp-spout
  {
    "plugin.name" "SCPHost.exe"
    "plugin.args" ["HelloWorld.dll" "Scp.App.HelloWorld.Generator" "Get"]
    "output.schema" {"default" ["sentence"]}
  })
```

此處`"plugin.name"`，指定為`"SCPHost.exe"`SCP SDK 提供的 。 SCPHost.exe 接受以下順序的三個參數：

1. DLL 名稱，`"HelloWorld.dll"`在此示例中為。
1. 類名稱，`"Scp.App.HelloWorld.Generator"`在此示例中。
1. 公共靜態方法的名稱，可以調用它來獲取**ISCPPlugin**的實例。

在主機模式下，將代碼編譯為 SCP 平台叫用的 DLL。 由於平臺可以完全控制整個處理邏輯，因此我們建議您在 SCP 主機模式下提交拓撲。 這樣做簡化了開發體驗。 它還為您帶來更大的靈活性和更好的向後相容性，適用于以後的版本。

## <a name="scp-programming-examples"></a>SCP 程式設計示例

### <a name="helloworld"></a>HelloWorld

下面的簡單 HelloWorld 示例顯示了SCP.NET的味道。 它使用非事務性拓撲，其噴口稱為**產生器**，兩個螺栓稱為**拆分器**和**計數器**。 **產生器**噴口隨機生成句子，並將這些句子發出給**拆分器**。 **分體**螺栓將句子分成單詞，並將這些單詞發出到**計數器**螺栓上。 **計數器**螺栓使用字典記錄每個單詞的匹配項。

此示例有兩個規範檔：HelloWorld.spec 和\_HelloWorld 啟用Ack.spec。C# 代碼可以通過從 JAVA 端獲取`pluginConf`物件來瞭解是否啟用了確認。

```csharp
/* demo how to get pluginConf info */
if (Context.Config.pluginConf.ContainsKey(Constants.NONTRANSACTIONAL_ENABLE_ACK))
{
    enableAck = (bool)(Context.Config.pluginConf[Constants.NONTRANSACTIONAL_ENABLE_ACK]);
}
Context.Logger.Info("enableAck: {0}", enableAck);
```

如果在 spout 中啟用了確認，則字典將緩存尚未確認的元數。 如果`Fail`調用，則重播失敗元組。

```csharp
public void Fail(long seqId, Dictionary<string, Object> parms)
{
    Context.Logger.Info("Fail, seqId: {0}", seqId);
    if (cachedTuples.ContainsKey(seqId))
    {
        /* get the cached tuple */
        string sentence = cachedTuples[seqId];

        /* replay the failed tuple */
        Context.Logger.Info("Re-Emit: {0}, seqId: {1}", sentence, seqId);
        this.ctx.Emit(Constants.DEFAULT_STREAM_ID, new Values(sentence), seqId);
    }
    else
    {
        Context.Logger.Warn("Fail(), can't find cached tuple for seqId {0}!", seqId);
    }
}
```

### <a name="helloworldtx"></a>HelloWorldTx

下面的 HelloWorldTx 示例演示如何實現事務拓撲。 該示例有一個稱為**產生器**的噴口，一個稱為**部分計數的**批次處理螺栓，以及一個稱為**計數和的**提交螺栓。 該示例還有三個現有的文字檔：DataSource0.txt、DataSource1.txt 和 DataSource2.txt。

在每個事務中，**產生器**噴口從現有三個檔中隨機播放兩個檔，並將兩個檔案名發射到**部分計數**螺栓。 **部分計數**螺栓：

1. 從接收的元組獲取檔案名。
1. 打開相應的檔。
1. 計算檔中的單詞數。
1. 將單詞計數發出到**計數和**螺栓。

**count-sum** bolt 將計算總數。

要精確實現一次語義，**計數和**提交螺栓需要判斷它是否是重播事務。 在此示例中，它有以下靜態成員變數：

```csharp
public static long lastCommittedTxId = -1; 
```

創建**ISCPBatchBolt**實例時，它將從輸入參數中獲取`txAttempt`物件的值。

```csharp
public static CountSum Get(Context ctx, Dictionary<string, Object> parms)
{
    /* for transactional topology, we can get txAttempt from the input parms */
    if (parms.ContainsKey(Constants.STORM_TX_ATTEMPT))
    {
        StormTxAttempt txAttempt = (StormTxAttempt)parms[Constants.STORM_TX_ATTEMPT];
        return new CountSum(ctx, txAttempt);
    }
    else
    {
        throw new Exception("null txAttempt");
    }
}
```

調用`FinishBatch`時，`lastCommittedTxId`如果它不是重播的事務，則更新該事務。

```csharp
public void FinishBatch(Dictionary<string, Object> parms)
{
    /* judge whether it is a replayed transaction */
    bool replay = (this.txAttempt.TxId <= lastCommittedTxId);

    if (!replay)
    {
        /* If it is not replayed, update the totalCount and lastCommittedTxId value */
        totalCount = totalCount + this.count;
        lastCommittedTxId = this.txAttempt.TxId;
    }
    … …
}
```

### <a name="hybridtopology"></a>HybridTopology

此拓撲包含 JAVA 噴口和 C# 螺栓。 它使用 SCP 平臺提供的預設序列化和反序列化實現。 有關規範檔的詳細資訊，請參閱示例\\"混合拓撲.spec"中的檔混合拓撲。 有關如何指定 JAVA 類路徑，請參閱提交拓撲.bat。

### <a name="scphostdemo"></a>SCPHostDemo

此示例本質上與 HelloWorld 相同。 唯一的區別是，您的代碼編譯為 DLL，拓撲是使用 SCPHost.exe 提交的。 有關更詳細的說明，請參閱 SCP 主機模式部分。

## <a name="next-steps"></a>後續步驟

有關使用 SCP 創建的 Apache Storm 拓撲的示例，請參閱以下文章：

* [使用 Visual Studio 開發 Apache Storm on HDInsight 的 C# 拓撲](apache-storm-develop-csharp-visual-studio-topology.md)
* [使用 HDInsight 上的 Apache Storm 處理 Azure 事件中樞的事件](apache-storm-develop-csharp-event-hub-topology.md)
* [使用 Apache Storm on HDInsight 處理事件中樞的車輛感應器資料](https://github.com/hdinsight/hdinsight-storm-examples/tree/master/IotExample)
* [從 Azure 事件中心擷取、轉換和下載 （ETL） 到 Apache HBase](https://github.com/hdinsight/hdinsight-storm-examples/blob/master/RealTimeETLExample)
