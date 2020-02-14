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
ms.sourcegitcommit: b07964632879a077b10f988aa33fa3907cbaaf0e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/13/2020
ms.locfileid: "77186869"
---
# <a name="scp-programming-guide-for-apache-storm-in-azure-hdinsight"></a>Azure HDInsight 中 Apache Storm 的 SCP 程式設計指南

SCP 是建立即時、可靠、一致和高效能資料處理應用程式的平臺。 它是以[Apache Storm](https://storm.incubator.apache.org/)為基礎，它是由開放原始碼軟體團體所設計的串流處理系統。 Nathan Marz 建立了風暴。 這是由 Twitter 發佈為開放原始碼。 風暴會使用[Apache ZooKeeper](https://zookeeper.apache.org/)，這是另一個可啟用高度可靠分散式協調和狀態管理的 Apache 專案。

SCP 專案不僅會在 Windows 上移植，還會在 windows 環境中針對專案新增的擴充功能和自訂。 這些擴充功能包括 .NET 開發人員經驗和 .NET 程式庫。 自訂包含以 Windows 為基礎的部署。

透過擴充功能和自訂功能，您不需要將開放原始碼軟體專案派生。 您可以使用以「風暴」為基礎的衍生環境。

## <a name="processing-model"></a>處理模型

SCP 中的資料模擬成連續的 Tuple 串流。 一般來說，元組：

1. 流入佇列。
1. 會由在風暴拓撲內裝載的商務邏輯來挑選和轉換。
1. 將其輸出輸送為另一個 SCP 系統的元組，或認可至存放區，例如分散式檔案系統和資料庫（例如 SQL Server）。

![佇列的圖表，可將資料摘要至處理中，然後再將資料存放區饋送](./media/apache-storm-scp-programming-guide/queue-feeding-data-to-processing-to-data-store.png)

在 [風暴] 中，應用程式拓撲會定義計算圖形。 拓撲中的每個節點都包含處理邏輯。 節點之間的連結表示資料流程。

將輸入資料插入拓撲中的節點稱為_spout_。 您可以使用它們來排序資料。 輸入資料可能來自于檔案記錄、交易式資料庫或系統效能計數器等來源。

具有輸入和輸出資料流程的節點稱為「_螺栓_」。 它們會執行實際的資料篩選、選取和匯總。

SCP 支援最佳的工作、最少一次，而且只需要一次資料處理。

在分散式資料流程處理應用程式中，資料處理期間可能會發生錯誤。 這類錯誤包括網路中斷、電腦故障或您的程式碼發生錯誤。 「至少一次」處理可確保所有資料至少處理一次，方法是在發生錯誤時自動重新執行相同的資料。

至少一次的處理是簡單且可靠的，而且它適合許多應用程式。 但當應用程式需要精確的計數時，至少一次的處理就不足，因為在應用程式拓撲中可能會重新執行相同的資料。 在這種情況下，只要一次處理就可以確保結果正確，即使資料重新執行並處理多次也一樣。

SCP 可讓 .NET 開發人員在搭配使用 JAVA 虛擬機器（JVM）時，建立即時資料處理應用程式。 JVM 和 .NET 會透過 TCP 本機通訊端進行通訊。 每個 spout/螺栓都是 .NET/JAVA 進程配對，其中的使用者邏輯在 .NET 進程中以外掛程式的形式執行。

若要在 SCP 之上建立資料處理應用程式，請遵循下列步驟：

1. 設計並執行 spout，以從佇列中提取資料。
1. 設計和執行會處理輸入資料的螺栓，並將其儲存至外部存放區，例如資料庫。
1. 設計拓撲，然後提交並執行它。

拓撲會定義頂點和在其間流動的資料。 SCP 會採用拓朴規格，並將它部署在一種在一個邏輯節點上執行的「風暴」叢集上。 「風暴工作排程器」負責容錯移轉和調整。

本文使用一些簡單的範例來逐步解說如何使用 SCP 來建立資料處理應用程式。

## <a name="scp-plug-in-interface"></a>SCP 外掛程式介面

SCP 外掛程式是獨立應用程式。 它們可以在開發期間于 Visual Studio 內執行，並在生產環境部署後插入至風暴管線。

撰寫 SCP 外掛程式與撰寫任何其他 Windows 主控台應用程式相同。 SCP.NET 平臺會宣告一些適用于 spout/螺栓的介面。 您的外掛程式程式碼會執行這些介面。 這項設計的主要目的是讓您專注于商務邏輯，同時讓 SCP.NET 平臺處理其他事項。

您的外掛程式程式碼會執行下列其中一個介面。 哪個介面取決於拓撲是交易式或非交易式，以及元件是 spout 或螺栓。

* **ISCPSpout**
* **ISCPBolt**
* **ISCPTxSpout**
* **ISCPBatchBolt**

### <a name="iscpplugin"></a>ISCPPlugin

**ISCPPlugin**是許多外掛程式的通用介面。目前，它是虛擬介面。

```csharp
public interface ISCPPlugin
{
}
```

### <a name="iscpspout"></a>ISCPSpout

**ISCPSpout**是非交易式 spout 的介面。

```csharp
public interface ISCPSpout : ISCPPlugin
{
    void NextTuple(Dictionary<string, Object> parms);
    void Ack(long seqId, Dictionary<string, Object> parms); 
    void Fail(long seqId, Dictionary<string, Object> parms);
}
```

呼叫**NextTuple**時，您的C#程式碼可能會發出一或多個元組。 如果沒有要發出的東西，這個方法應該會傳回，而不發出任何內容。

**NextTuple**、 **Ack**和**Fail**方法全都是在C#進程的單一執行緒中，以緊密迴圈來呼叫。 當沒有要發出的元組時，讓**NextTuple**睡眠一小段時間，例如10毫秒。 此睡眠有助於避免浪費 CPU 可用性。

只有當規格檔案啟用通知機制時，才會呼叫**Ack**和**Fail**方法。 *SeqId*參數會識別已認可或已失敗的元組。 如果在非交易式拓撲中啟用通知，則應在 spout 中使用下列**發出**函數：

```csharp
public abstract void Emit(string streamId, List<object> values, long seqId);
```

如果非交易式拓撲不支援認可， **Ack**和**Fail**可以保留為空白函式。

這些函數中的*parms*輸入參數會指定空的字典，並保留供日後使用。

### <a name="iscpbolt"></a>ISCPBolt

**ISCPBolt**是非交易式螺栓的介面。

```csharp
public interface ISCPBolt : ISCPPlugin
{
void Execute(SCPTuple tuple);
}
```

當有新的元組可用時，會呼叫**Execute**函式來處理它。

### <a name="iscptxspout"></a>ISCPTxSpout

**ISCPTxSpout**是交易式 spout 的介面。

```csharp
public interface ISCPTxSpout : ISCPPlugin
{
    void NextTx(out long seqId, Dictionary<string, Object> parms);  
    void Ack(long seqId, Dictionary<string, Object> parms);         
    void Fail(long seqId, Dictionary<string, Object> parms);        
}
```

就像它們的非交易式對應專案一樣， **NextTx**、 **Ack**和**Fail**都是在C#進程的單一執行緒中以緊密迴圈來呼叫。 當沒有要發出的元組時，讓**NextTx**睡眠一小段時間，例如10毫秒。 此睡眠有助於避免浪費 CPU 可用性。

呼叫**NextTx**來啟動新的交易時， *seqId*輸出參數會識別交易。 交易也會用於**Ack**中並**失敗**。 您的**NextTx**方法可以發出資料給 JAVA 端。 資料會儲存在 ZooKeeper 中以支援重播。 因為 ZooKeeper 的容量有限，所以您的程式碼應該只發出中繼資料，而非交易式 spout 中的大量資料。

因為「風暴」會自動重新執行失敗的交易，通常不會呼叫「**失敗**」。 但是，如果 SCP 可以檢查交易式 spout 所發出的中繼資料，當中繼資料無效時，就可能呼叫**失敗**。

這些函數中的*parms*輸入參數會指定空的字典，並保留供日後使用。

### <a name="iscpbatchbolt"></a>ISCPBatchBolt

**ISCPBatchBolt**是交易式螺栓的介面。

```csharp
public interface ISCPBatchBolt : ISCPPlugin
{
    void Execute(SCPTuple tuple);
    void FinishBatch(Dictionary<string, Object> parms);  
}
```

當新的元組抵達螺栓時，會呼叫**Execute**方法。 當此交易結束時，會呼叫**FinishBatch**方法。 *Parms*輸入參數保留供未來使用。

若為交易式拓撲， **StormTxAttempt**是重要的類別。 它有兩個成員： **tx 識別碼**和**AttemptId**。 **Tx 識別碼**成員會識別特定的交易。 如果交易失敗且已重新執行，可能會多次嘗試。

SCP.NET 會建立新的**ISCPBatchBolt**物件來處理每個**StormTxAttempt**物件，就像在 JAVA 中的風暴一樣。 這種設計的目的是支援平行交易處理。 交易嘗試完成後，會終結對應的**ISCPBatchBolt**物件並進行垃圾收集。

## <a name="object-model"></a>物件模型

SCP.NET 也提供一組簡單的關鍵物件供開發人員在程式設計中使用。 物件為**CoNtext**、 **StateStore**和**SCPRuntime**。 本節將討論這些功能。

### <a name="context"></a>內容

**內容**物件提供應用程式的執行環境。 **ISCPSpout**、 **ISCPBolt**、 **ISCPTxSpout**或**ISCPBatchBolt**的每個**ISCPPlugin**實例都有對應的**內容**實例。 **CoNtext**提供的功能分成兩個部分：

* 靜態部分，在整個C#程式中都有提供
* 動態部分，僅適用于特定的**內容**實例

### <a name="static-part"></a>靜態部分

```csharp
public static ILogger Logger = null;
public static SCPPluginType pluginType;
public static Config Config { get; set; }
public static TopologyContext TopologyContext { get; set; }  
```

**記錄器**物件是為了記錄目的而提供。

**PluginType**物件表示C#進程的外掛程式類型。 如果處理常式是在沒有 JAVA 的本機測試模式下執行，則外掛程式類型為**SCP_NET_LOCAL**。

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

**Config**屬性會從 JAVA 端取得設定參數，以在C#外掛程式初始化時傳遞它們。 **Config**參數分成兩個部分： **stormConf**和**pluginConf**。

```csharp
public Dictionary<string, Object> stormConf { get; set; }  
public Dictionary<string, Object> pluginConf { get; set; }  
```

**StormConf**元件是以「風暴」定義的參數，而**PLUGINCONF**部分是 SCP 所定義的參數。 以下是範例：

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

**TopologyCoNtext**類型會取得拓撲內容。 最適用于多個平行元件。 以下是範例：

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

### <a name="dynamic-part"></a>動態部分

下列介面是與特定的**內容**實例相關，而此實例是由 SCP.NET 平臺所建立並傳遞至您的程式碼：

```csharp
// Declare the Output and Input Stream Schemas

public void DeclareComponentSchema(ComponentStreamSchema schema);

// Emit tuple to default stream.
public abstract void Emit(List<object> values);

// Emit tuple to the specific stream.
public abstract void Emit(string streamId, List<object> values);  
```

對於支援認可的非交易式 spout，會提供下列方法：

```csharp
// for nontransactional spout that supports ack
public abstract void Emit(string streamId, List<object> values, long seqId);  
```

支援認可的非交易式螺栓應該明確地呼叫**Ack** ，或使用其收到的元組來**失敗**。 發出新的元組時，螺栓也必須指定元組的錨點。 提供下列方法：

```csharp
public abstract void Emit(string streamId, IEnumerable<SCPTuple> anchors, List<object> values);
public abstract void Ack(SCPTuple tuple);
public abstract void Fail(SCPTuple tuple);
```

### <a name="statestore"></a>StateStore

**StateStore**物件提供中繼資料服務、單純序列產生，以及無等待的協調。 您可以在**StateStore**上建立更高層級的分散式平行存取抽象概念。 這些抽象概念包括分散式鎖定、分散式佇列、屏障和交易服務。

SCP 應用程式可以使用**狀態**物件來序列化[Apache ZooKeeper](https://zookeeper.apache.org/)中的資訊。 這種功能對於交易式拓撲而言特別有用。 如果交易式 spout 停止回應並重新啟動，則**狀態**可以從 ZooKeeper 取得必要的資訊，然後重新開機管線。

**StateStore**物件具有下列主要方法：

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

**狀態**物件具有下列主要方法：

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

當**simpleMode**設定為**True**時， **Commit**方法會在 ZooKeeper 中刪除對應的 ZNode。 否則，方法會刪除目前的 ZNode，並在認可的\_路徑中加入新的節點。

### <a name="scpruntime"></a>SCPRuntime

**SCPRuntime**類別提供下列兩種方法：

```csharp
public static void Initialize();

public static void LaunchPlugin(newSCPPlugin createDelegate);  
```

**Initialize**方法會初始化 SCP 執行時間環境。 在此方法中， C#進程會連接到 JAVA 端，以取得設定參數和拓撲內容。

**LaunchPlugin**方法會啟動訊息處理迴圈。 在此迴圈中， C#外掛程式會從 JAVA 端接收訊息。 這些訊息包含元組和控制信號。 外掛程式會接著處理訊息，可能是藉由呼叫您的程式碼所提供的介面方法。

**LaunchPlugin**的輸入參數是委派。 方法可以傳回可執行**ISCPSpout**、 **ISCPBolt**、 **ISCPTxSpout**或**ISCPBatchBolt**介面的物件。

```csharp
public delegate ISCPPlugin newSCPPlugin(Context ctx, Dictionary<string, Object> parms);
```

針對**ISCPBatchBolt**，您可以從*Parms*參數取得**StormTxAttempt**物件，並使用它來判斷嘗試是否為重新執行的嘗試。 檢查重新執行嘗試通常是在認可的螺栓上完成。 本文稍後的 HelloWorldTx 範例會示範這項檢查。

SCP 外掛程式通常可以在兩種模式下執行：本機測試模式和一般模式。

#### <a name="local-test-mode"></a>本機測試模式

在此模式中，程式C#代碼中的 SCP 外掛程式會在開發階段 Visual Studio 內執行。 您可以在此模式中使用**ILocalCoNtext**介面。 介面會提供方法，將發出的元組序列化為本機檔案，並將它們讀回 RAM。

```csharp
public interface ILocalContext
{
    List<SCPTuple> RecvFromMsgQueue();
    void WriteMsgQueueToFile(string filepath, bool append = false);  
    void ReadFromFileToMsgQueue(string filepath);
}
```

#### <a name="regular-mode"></a>一般模式

在此模式中，「風暴 JAVA」進程會執行 SCP 外掛程式。範例如下：

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

## <a name="topology-specification-language"></a>拓撲規格語言

SCP 拓撲規格是用來描述和設定 SCP 拓撲的特定領域語言（DSL）。 它是以[風暴的 CLOJURE DSL](https://storm.incubator.apache.org/documentation/Clojure-DSL.html)為基礎，並由 SCP 擴充。

您可以將拓撲規格直接提交至風暴叢集，以透過**runSpec**命令執行。

SCP.NET 已新增下列函數來定義交易式拓撲：

| 新增函式 | 參數 | 描述 |
| --- | --- | --- |
| **tx-topolopy** |*拓撲-名稱*<br />*spout-對應*<br />*螺栓對應* |使用拓撲名稱、spout 定義對應和螺栓定義對應來定義交易式拓撲。 |
| **scp-tx-spout** |*exec-名稱*<br />*引數*<br />*fields* |定義交易式 spout。 函式會執行*exec 名稱*所指定的應用程式，並使用*args*。<br /><br />*Fields*參數會指定 spout 的輸出欄位。 |
| **scp-tx-batch-bolt** |*exec-名稱*<br />*引數*<br />*fields* |定義交易式批次螺栓。 函式會執行*exec 名稱*所指定的應用程式，並使用*args。*<br /><br />*Fields*參數會指定螺栓的輸出欄位。 |
| **scp-tx-commit-bolt** |*exec-名稱*<br />*引數*<br />*fields* |定義交易式認可螺栓。 函式會執行*exec 名稱*所指定的應用程式，並使用*args*。<br /><br />*Fields*參數會指定螺栓的輸出欄位。 |
| **nontx-拓朴** |*拓撲-名稱*<br />*spout-對應*<br />*螺栓對應* |使用拓撲名稱、spout 定義對應和螺栓定義對應來定義非交易式拓撲。 |
| **scp-spout** |*exec-名稱*<br />*引數*<br />*fields*<br />*parameters* |定義非交易式 spout。 函式會執行*exec 名稱*所指定的應用程式，並使用*args*。<br /><br />*Fields*參數會指定 spout 的輸出欄位。<br /><br />*Parameters*參數是選擇性的。 使用它來指定像是「非交易式. 已啟用」的參數。 |
| **scp-bolt** |*exec-名稱*<br />*引數*<br />*fields*<br />*parameters* |定義非交易式螺栓。 函式會執行*exec 名稱*所指定的應用程式，並使用*args*。<br /><br />*Fields*參數會指定螺栓的輸出欄位<br /><br />*Parameters*參數是選擇性的。 使用它來指定像是「非交易式. 已啟用」的參數。 |

SCP.NET 會定義下列關鍵字：

| 關鍵字 | 描述 |
| --- | --- |
| **:name** |拓撲名稱 |
| **:topology** |使用上表中的函數和內建函數的拓撲 |
| **:p** |每個 spout 或螺栓的平行處理原則提示 |
| **:config** |是否要設定參數或更新現有參數 |
| **:schema** |資料流程的架構 |

SCP.NET 也會定義這些常用的參數：

| 參數 | 描述 |
| --- | --- |
| "plugin.name" |C#外掛程式的 .exe 檔案名 |
| "外掛程式. args" |外掛程式引數 |
| 「輸出架構」 |輸出架構 |
| 「非交易式 ack。已啟用」 |是否啟用非交易式拓撲的確認 |

**RunSpec**命令會與位一起部署。 以下是命令使用方式：

```csharp
.\bin\runSpec.cmd
usage: runSpec [spec-file target-dir [resource-dir] [-cp classpath]]
ex: runSpec examples\HelloWorld\HelloWorld.spec specs examples\HelloWorld\Target
```

*Resource-dir*參數是選擇性的。 當您想要插入C#應用程式時，請指定它。 指定的目錄包含應用程式、相依性和設定。

*classpath* 參數也是選擇性。 如果規格檔案包含 JAVA spout 或螺栓，它會指定 JAVA 路徑。

## <a name="miscellaneous-features"></a>其他功能

### <a name="input-and-output-schema-declarations"></a>輸入和輸出架構宣告

您C#的處理常式可以發出元組。 若要這樣做，平臺會將元組序列化為**byte []** 物件，並將物件傳送至 JAVA 端。 然後，將這些元組傳輸至目標。

在下游元件中C# ，進程會從 JAVA 端接收元組，並將它們轉換成平臺的原始類型。 平臺會隱藏所有這些作業。

若要支援序列化和還原序列化，您的程式碼必須宣告輸入和輸出的架構。 架構會定義為字典。 資料流程識別碼是字典索引鍵。 金鑰值是資料行的類型。 元件可以宣告多個資料流程。

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

下列函式會新增至**內容**物件：

```csharp
public void DeclareComponentSchema(ComponentStreamSchema schema)
```

開發人員必須確定發出的元組遵守針對資料流程定義的架構。 否則，系統會擲回執行時間例外狀況。

### <a name="multistream-support"></a>Multistream 支援

SCP 可讓您的程式碼在同一時間發出或接收多個不同的資料流程。 **內容**物件會將這種支援反映為**發出**方法的選擇性資料流程識別碼參數。

已新增 SCP.NET**內容**物件中的兩個方法。 它們會發出一或多個元組給特定的資料流程。 *StreamId*參數是一個字串。 在程式C#代碼和拓撲定義規格中，其值必須相同。

```csharp
/* Emit tuple to the specific stream. */
public abstract void Emit(string streamId, List<object> values);

/* for nontransactional spout only */
public abstract void Emit(string streamId, List<object> values, long seqId);
```

發出至不存在的資料流程會導致執行時間例外狀況。

### <a name="fields-grouping"></a>欄位分組

在 [SCP.NET] 中，內建欄位群組中的分組無法正常運作。 在 JAVA proxy 端，所有欄位的資料類型實際上是**byte []** 。 [欄位] 群組會使用**byte []** 物件的雜湊碼來進行群組。 雜湊碼是此物件在 RAM 中的位址。 因此，共用相同內容但不是相同位址的多位元組物件，群組會是錯誤的。

SCP.NET 會加入自訂的群組方法，而且它會使用**byte []** 物件的內容來進行群組。 在規格檔案中，語法如下列範例所示：

```csharp
(bolt-spec
    {
        "spout_test" (scp-field-group :non-tx [0,1])
    }
    …
)
```

在上述規格檔案中：

* `scp-field-group` 指定群組是 SCP 所實作為自訂欄位群組。
* `:tx` 或 `:non-tx` 指定拓撲是否為交易式。 您需要此資訊，因為交易式和非交易式拓撲之間的起始索引不同。
* `[0,1]` 指定以零開頭之欄位識別碼的雜湊集。

### <a name="hybrid-topology"></a>混合式拓撲

原生風暴代碼是以 JAVA 撰寫。 SCP.NET 增強了風暴，讓您撰寫C#程式碼來處理您的商務邏輯。 不過，SCP.NET 也支援混合式拓撲，其中不僅C#包含 spout/螺栓，同時還包含 JAVA spout/螺栓。

### <a name="specify-java-spoutbolt-in-a-specification-file"></a>指定規格檔案中的 JAVA spout/閃電

您可以在規格檔案中使用**scp-spout**和**scp** ，以指定 JAVA spout 和螺栓。 以下是範例：

```csharp
(spout-spec 
  (microsoft.scp.example.HybridTopology.Generator.)
  :p 1)
```

這裡 `microsoft.scp.example.HybridTopology.Generator` 是 JAVA spout 類別的名稱。

### <a name="specify-the-java-classpath-in-a-runspec-command"></a>在 runSpec 命令中指定 JAVA 路徑

如果您想要提交包含 JAVA spout 或螺栓的拓撲，請先編譯它們以產生 JAR 檔案。 然後，指定當您提交拓撲時，包含 JAR 檔案的 java 路徑類型。 以下是範例：

```csharp
bin\runSpec.cmd examples\HybridTopology\HybridTopology.spec specs examples\HybridTopology\net\Target -cp examples\HybridTopology\java\target\*
```

在這裡，`examples\HybridTopology\java\target\` 是包含 JAVA spout/螺栓 JAR 檔案的資料夾。

### <a name="serialization-and-deserialization-between-java-and-c"></a>JAVA 和之間的序列化和還原序列化C#

SCP 元件包含 JAVA 端和C#端。 若要與原生 JAVA spout/螺栓互動，必須在 JAVA 端和C#端之間進行序列化和還原序列化，如下圖所示：

![將 JAVA 元件傳送到 SCP 元件，然後傳送至不同的 JAVA 元件的圖表](./media/apache-storm-scp-programming-guide/java-compent-sending-to-scp-component-sending-to-java-component.png)

#### <a name="serialization-in-the-java-side-and-deserialization-in-the-c-side"></a>JAVA 端的序列化和端的C#還原序列化

首先，在 JAVA 端提供序列化的預設實作為，並在C#兩側進行還原序列化。

在規格檔案中指定 JAVA 端的序列化方法。

```csharp
(scp-bolt
    {
        "plugin.name" "HybridTopology.exe"
        "plugin.args" ["displayer"]
        "output.schema" {}
        "customized.java.serializer" ["microsoft.scp.storm.multilang.CustomizedInteropJSONSerializer"]
    })
```

在您C#的程式C#代碼中指定端的還原序列化方法。

```csharp
Dictionary<string, List<Type>> inputSchema = new Dictionary<string, List<Type>>();
inputSchema.Add("default", new List<Type>() { typeof(Person) });
this.ctx.DeclareComponentSchema(new ComponentStreamSchema(inputSchema, null));
this.ctx.DeclareCustomizedDeserializer(new CustomizedInteropJSONDeserializer());
```  

如果資料類型不太複雜，此預設的執行應該會處理大部分的情況。 以下是您可以插入自己的執行的情況：

* 您的資料類型對預設的執行而言太複雜。
* 預設執行的效能不符合您的需求。

JAVA 端的序列化介面定義如下：

```csharp
public interface ICustomizedInteropJavaSerializer {
    public void prepare(String[] args);
    public List<ByteBuffer> serialize(List<Object> objectList);
}
```

這C#一端的還原序列化介面定義如下：

```csharp
public interface ICustomizedInteropCSharpDeserializer
{
    List<Object> Deserialize(List<byte[]> dataList, List<Type> targetTypes);
}
```

#### <a name="serialization-in-the-c-side-and-deserialization-in-the-java-side"></a>在C#端序列化並在 JAVA 端還原序列化

在您C#的程式C#代碼中指定端的序列化方法。

```csharp
this.ctx.DeclareCustomizedSerializer(new CustomizedInteropJSONSerializer()); 
```

在規格檔案中指定 JAVA 端的還原序列化方法。

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

在這裡，`"microsoft.scp.storm.multilang.CustomizedInteropJSONDeserializer"` 是還原序列化的名稱，而 `"microsoft.scp.example.HybridTopology.Person"` 則是資料還原序列化的目標類別。

您也可以插入自己的C#序列化程式和 JAVA 還原序列化程式。

此C#程式碼是序列化程式的介面：

```csharp
public interface ICustomizedInteropCSharpSerializer
{
    List<byte[]> Serialize(List<object> dataList);
}
```

這段程式碼是 JAVA 還原序列化程式的介面：

```csharp
public interface ICustomizedInteropJavaDeserializer {
    public void prepare(String[] targetClassNames);
    public List<Object> Deserialize(List<ByteBuffer> dataList);
}
```

## <a name="scp-host-mode"></a>SCP 主機模式

在此模式中，您可以將程式碼編譯為 DLL，並使用 SCP 所提供的 Scphost.exe 來提交拓撲。 規格檔案看起來會像下面這段程式碼：

```csharp
(scp-spout
  {
    "plugin.name" "SCPHost.exe"
    "plugin.args" ["HelloWorld.dll" "Scp.App.HelloWorld.Generator" "Get"]
    "output.schema" {"default" ["sentence"]}
  })
```

在這裡，`"plugin.name"` 會指定為 `"SCPHost.exe"`（由 SCP SDK 提供）。 Scphost.exe 會依下列順序接受三個參數：

1. DLL 名稱，在此範例中 `"HelloWorld.dll"`。
1. 類別名稱，在此範例中 `"Scp.App.HelloWorld.Generator"`。
1. 公用靜態方法的名稱，可叫用以取得**ISCPPlugin**的實例。

在 [主機模式] 中，將您的程式碼編譯為 DLL，以供 SCP 平台叫用。 由於平臺可以接著完全控制整個處理邏輯，因此建議您在 SCP 主機模式下提交拓撲。 這麼做可簡化開發體驗。 它也可為您提供更大的彈性，以及更好的回溯相容性。

## <a name="scp-programming-examples"></a>SCP 程式設計範例

### <a name="helloworld"></a>HelloWorld

下列簡單的 HelloWorld 範例顯示 SCP.NET 的感受。 它會使用非交易式拓撲搭配名為產生**器**的**spout，以及兩個名為**分割和**計數器**的螺栓。 產生器**spout 會**隨機產生句子，併發出這些句子給**分隔器**。 **分隔器**螺栓會將句子分割成單字，並將這些單字發出到**計數器**的螺栓。 **計數器**的螺栓會使用字典來記錄每個單字的出現次數。

這個範例有兩個規格檔案： HelloWorld. spec 和 HelloWorld\_Enableack.spec。此C#程式碼可從 JAVA 端取得 `pluginConf` 物件，以找出是否已啟用確認。

```csharp
/* demo how to get pluginConf info */
if (Context.Config.pluginConf.ContainsKey(Constants.NONTRANSACTIONAL_ENABLE_ACK))
{
    enableAck = (bool)(Context.Config.pluginConf[Constants.NONTRANSACTIONAL_ENABLE_ACK]);
}
Context.Logger.Info("enableAck: {0}", enableAck);
```

如果已在 spout 中啟用通知，則字典會快取尚未認可的元組。 如果呼叫 `Fail`，會重新執行失敗的元組。

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

下列 HelloWorldTx 範例示範如何執行交易式拓撲。 此範例有一個稱為產生**器的 spout、一個**稱為「**部分計數**」的批次螺栓，以及一個稱為「**計數-總和**」的認可螺栓。 此範例也有三個現有的文字檔： DataSource0 .txt、DataSource1 .txt 和 DataSource2 .txt。

在每個交易中，產生**器 spout 會**隨機從現有的三個檔案中選取兩個檔案，並將兩個檔案名發出至**部分計數**的螺栓。 **部分計數**的螺栓：

1. 從接收的元組取得檔案名。
1. 開啟對應的檔案。
1. 計算檔案中的字數。
1. 發出字數統計到**計數總和**螺栓。

**count-sum** bolt 將計算總數。

若要剛好達到一次的語義， **count 總和**commit 螺栓必須判斷它是否為重新執行的交易。 在此範例中，它具有下列靜態成員變數：

```csharp
public static long lastCommittedTxId = -1; 
```

建立**ISCPBatchBolt**實例時，它會從輸入參數取得 `txAttempt` 物件的值。

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

當呼叫 `FinishBatch` 時，如果不是重新執行的交易，`lastCommittedTxId` 就會更新。

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

此拓撲包含 JAVA spout 和C#螺栓。 它會使用 SCP 平臺所提供的預設序列化和還原序列化執行。 如需規格檔案的詳細資料，請參閱 <\\Examples hybridtopology 資料夾範例中的 Examples hybridtopology 檔案。 另請參閱查看 submittopology.bat 瞭解，以瞭解如何指定 JAVA 類路徑。

### <a name="scphostdemo"></a>SCPHostDemo

這個範例基本上與 HelloWorld 相同。 唯一的差別在於您的程式碼會編譯為 DLL，並使用 Scphost.exe 來提交拓撲。 如需更詳細的說明，請參閱 SCP 主機模式一節。

## <a name="next-steps"></a>後續步驟

如需使用 SCP 建立之 Apache Storm 拓撲的範例，請參閱下列文章：

* [使用 Visual Studio 開發 Apache Storm on HDInsight 的 C# 拓撲](apache-storm-develop-csharp-visual-studio-topology.md)
* [使用 HDInsight 上的 Apache Storm 處理 Azure 事件中樞的事件](apache-storm-develop-csharp-event-hub-topology.md)
* [使用 HDInsight 上的 Apache Storm 處理事件中樞的車輛感應器資料](https://github.com/hdinsight/hdinsight-storm-examples/tree/master/IotExample)
* [從 Azure 事件中樞解壓縮、轉換和載入（ETL）至 Apache HBase](https://github.com/hdinsight/hdinsight-storm-examples/blob/master/RealTimeETLExample)
