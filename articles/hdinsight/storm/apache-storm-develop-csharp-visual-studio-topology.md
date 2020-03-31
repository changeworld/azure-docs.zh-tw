---
title: 採用 Visual Studio 和 C# 的 Apache Storm 拓撲 - Azure HDInsight
description: 了解如何使用 C# 建立 Storm 拓撲。 使用 Visual Studio 的 Hadoop 工具在視覺化工作室中創建字數拓撲。
ROBOTS: NOINDEX
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 12/31/2019
ms.openlocfilehash: 1903c2faab865152d1f3666f3c9dadd745058b56
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "75612286"
---
# <a name="develop-c-topologies-for-apache-storm-by-using-the-data-lake-tools-for-visual-studio"></a>使用 Data Lake Tools for Visual Studio 開發 Apache Storm 的 C# 拓撲

了解如何使用 Azure Data Lake (Apache Hadoop) Tools for Visual Studio 來建立 C# Apache Storm 拓撲。 這份文件逐步解說如何在 Visual Studio 中建立 Storm 專案，在本機測試該專案，並將它部署至 Azure HDInsight 叢集上的 Apache Storm。

您也會學習如何建立使用 C# 和 Java 元件的混合式拓撲。

C# 拓撲使用 .NET 4.5，並使用 Mono 在 HDInsight 群集上運行。 有關潛在不相容的資訊，請參閱[單一相容性](https://www.mono-project.com/docs/about-mono/compatibility/)。 要使用 C# 拓撲，必須將專案使用的`Microsoft.SCP.Net.SDK`NuGet 包更新為版本 0.10.0.6 或更高版本。 套件版本也必須符合 HDInsight 上安裝的 Storm 主要版本。

| HDInsight 版本 | Apache Storm 版本 | SCP.NET 版本 | 預設 Mono 版本 |
|:-----------------:|:-------------:|:---------------:|:--------------------:|
| 3.4 | 0.10.0.x | 0.10.0.x | 3.2.8 |
| 3.5 | 1.0.2.x | 1.0.0.x | 4.2.1 |
| 3.6 | 1.1.0.x | 1.0.0.x | 4.2.8 |

## <a name="prerequisite"></a>必要條件

HDInsight 上的 Apache Storm 叢集。 請參閱[使用 Azure 入口網站建立 Apache Hadoop 叢集](../hdinsight-hadoop-create-linux-clusters-portal.md)，然後選取 [Storm]**** 作為 [叢集類型]****。

## <a name="install-visual-studio"></a>安裝 Visual Studio

您可以使用[Visual Studio](https://visualstudio.microsoft.com/downloads/)使用 SCP.NET 開發 C# 拓撲。 此處的說明使用 Visual Studio 2019，但您也可以使用早期版本的 Visual Studio。

## <a name="install-data-lake-tools-for-visual-studio"></a>安裝 Data Lake Tools for Visual Studio

若要安裝 Data Lake Tools for Visual Studio，請遵循[開始使用 Data Lake Tools for Visual Studio](../hadoop/apache-hadoop-visual-studio-tools-get-started.md#install-data-lake-tools-for-visual-studio) 中的步驟。

## <a name="install-java"></a>安裝 Java

當您從 Visual Studio 提交 Storm 拓撲時，SCP.NET 會產生包含拓撲及相依性的 ZIP 檔案。 系統會使用 Java 來建立這些 ZIP 檔案，因為它所使用的格式和以 Linux 為基礎的叢集更具相容性。

1. 在您的開發環境上安裝 Java Developer Kit (JDK) 7 或更新版本。 您可以從 [Oracle](https://openjdk.java.net/) \(英文\) 取得 Oracle JDK。 您也可以使用[其他 Java 發佈](/java/azure/jdk/) \(英文\)。

2. 將`JAVA_HOME`環境變數設置為包含 JAVA 的目錄。

3. 將`PATH`環境變數設置為包括`%JAVA_HOME%\bin`目錄。

您可以構建並運行以下 C# 主控台應用程式，以驗證 JAVA 和 JDK 是否正確安裝：

```csharp
using System;
using System.IO;
namespace ConsoleApplication2
{
   class Program
   {
       static void Main(string[] args)
       {
           string javaHome = Environment.GetEnvironmentVariable("JAVA_HOME");
           if (!string.IsNullOrEmpty(javaHome))
           {
               string jarExe = Path.Combine(javaHome + @"\bin", "jar.exe");
               if (File.Exists(jarExe))
               {
                   Console.WriteLine("JAVA Is Installed properly");
                    return;
               }
               else
               {
                   Console.WriteLine("A valid JAVA JDK is not found. Looks like JRE is installed instead of JDK.");
               }
           }
           else
           {
             Console.WriteLine("A valid JAVA JDK is not found. JAVA_HOME environment variable is not set.");
           }
       }  
   }
}
```

## <a name="apache-storm-templates"></a>Apache Storm 範本

Data Lake Tools for Visual Studio 提供下列範本：

| 專案類型 | 示範 |
| --- | --- |
| Storm 應用程式 |空白 Storm 拓撲專案。 |
| Storm Azure SQL 寫入器範例 |如何寫入至 Azure SQL Database。 |
| Storm Azure Cosmos DB 讀取器範例 |如何從 Azure Cosmos DB 讀取。 |
| Storm Azure Cosmos DB 寫入器範例 |如何寫入至 Azure Cosmos DB。 |
| Storm EventHub 讀取器範例 |如何從 Azure 事件中樞讀取。 |
| Storm EventHub 寫入器範例 |如何寫入至 Azure 事件中樞。 |
| Storm HBase 讀取器範例 |如何從 HDInsight 叢集上的 HBase 讀取。 |
| Storm HBase 寫入器範例 |如何寫入至 HDInsight 叢集上的 HBase。 |
| Storm 混合式範例 |如何使用 Java 元件。 |
| Storm 範例 |基本的字數統計拓撲。 |

> [!WARNING]  
> 並非所有範本都能與以 Linux 為基礎的 HDInsight 搭配運作。 範本所使用的 NuGet 套件可能無法與 Mono 相容。 要識別潛在問題，請參閱[單聲道相容性](https://www.mono-project.com/docs/about-mono/compatibility/)並使用[.NET 可攜性分析器](../hdinsight-hadoop-migrate-dotnet-to-linux.md#automated-portability-analysis)。

在這份文件的步驟中，您會使用基本 Storm 應用程式專案類型來建立拓撲。

### <a name="apache-hbase-templates"></a>阿帕奇 HBase 範本

HBase 讀取器和寫入器範本會使用 HBase REST API (而不是 HBase Java API) 來與 HDInsight 叢集上的 HBase 通訊。

### <a name="eventhub-templates"></a>事件中心範本

> [!IMPORTANT]  
> 包含在 EventHub Reader 讀取器範本中，以 Java 為基礎的 EventHub Spout 元件可能無法與 Storm on HDInsight 3.5 版或更新版本搭配使用。 此元件的更新版本可在 [GitHub](https://github.com/hdinsight/hdinsight-storm-examples/tree/master/HDI3.5/lib) \(英文\) 取得。

如需使用此元件和搭配 Storm on HDInsight 3.5 使用的範例拓撲，請參閱 [GitHub](https://github.com/Azure-Samples/hdinsight-dotnet-java-storm-eventhub) \(英文\)。

## <a name="create-a-c-topology"></a>建立 C# 拓撲

要在視覺化工作室中創建 C# 拓撲專案，請：

1. 開啟 Visual Studio。

1. 在 [開始]**** 視窗中，選取 [建立新專案]****。

1. 在 **"創建新專案**"視窗中，滾動到 並選取 **"風暴應用程式"，** 然後選擇 **"下一步**"。

1. 在 **"配置新專案**"視窗中，輸入*WordCount***的專案名稱**，轉到或為專案創建**位置**目錄路徑，然後選擇"**創建**"。

    ![風暴應用程式，配置新專案對話方塊，視覺化工作室](./media/apache-storm-develop-csharp-visual-studio-topology/apache-storm-new-project.png)

創建專案後，應具有以下檔：

* *Program.cs*： 專案的拓撲定義。 預設會建立含有一個 Spout 和一個 Bolt 的預設拓撲。

* *Spout.cs*：發出亂數的範例 Spout。

* *Bolt.cs*：保留 Spout 所發出數字之計數的範例 Bolt。

當您建立專案時，NuGet 會下載最新的 [SCP.NET 套件](https://www.nuget.org/packages/Microsoft.SCP.Net.SDK/) \(英文\)。

### <a name="implement-the-spout"></a>實作 Spout

接下來，添加噴口的代碼，該代碼用於從外部源讀取拓撲中的資料。 此 spout 會將句子隨機發出至拓撲。

1. 開啟 *Spout.cs*。 Spout 的主要元件如下：

   * `NextTuple`：當噴口允許發出新的元結時，由風暴調用。

   * `Ack`（僅限事務拓撲）：處理拓撲中其他元件為從噴口發送的元組啟動的確認。 認可 Tuple 可讓 Spout 知道下游元件已順利處理 Tuple。

   * `Fail`（僅限事務拓撲）：處理處理拓撲中其他元件的失敗元件的控制碼組。 實現方法`Fail`允許您重新發出元組，以便可以再次處理它。

2. 將`Spout`類的內容替換為以下文本：

    ```csharp
    private Context ctx;
    private Random r = new Random();
    string[] sentences = new string[] {
        "the cow jumped over the moon",
        "an apple a day keeps the doctor away",
        "four score and seven years ago",
        "snow white and the seven dwarfs",
        "i am at two with nature"
    };

    public Spout(Context ctx)
    {
        // Set the instance context
        this.ctx = ctx;

        Context.Logger.Info("Generator constructor called");

        // Declare Output schema
        Dictionary<string, List<Type>> outputSchema = new Dictionary<string, List<Type>>();
        // The schema for the default output stream is
        // a tuple that contains a string field
        outputSchema.Add("default", new List<Type>() { typeof(string) });
        this.ctx.DeclareComponentSchema(new ComponentStreamSchema(null, outputSchema));
    }

    // Get an instance of the spout
    public static Spout Get(Context ctx, Dictionary<string, Object> parms)
    {
        return new Spout(ctx);
    }

    public void NextTuple(Dictionary<string, Object> parms)
    {
        Context.Logger.Info("NextTuple enter");
        // The sentence to be emitted
        string sentence;

        // Get a random sentence
        sentence = sentences[r.Next(0, sentences.Length - 1)];
        Context.Logger.Info("Emit: {0}", sentence);
        // Emit it
        this.ctx.Emit(new Values(sentence));

        Context.Logger.Info("NextTuple exit");
    }

    public void Ack(long seqId, Dictionary<string, Object> parms)
    {
        // Only used for transactional topologies
    }

    public void Fail(long seqId, Dictionary<string, Object> parms)
    {
        // Only used for transactional topologies
    }
    ```

### <a name="implement-the-bolts"></a>實作 Bolt

現在，在此示例中創建兩個 Storm 螺栓：

1. 刪除專案中的現有 *Bolt.cs* 檔。

2. 在 **"解決方案資源管理器"** 中，按右鍵專案，**Add** > 然後選擇"**添加新項**"。 從清單中，選取 [Storm Bolt]****，並輸入 *Splitter.cs* 做為名稱。 在新檔的代碼中，將命名空間名稱更改為`WordCount`。 然後重複此過程以創建名為*Counter.cs*的第二個螺栓。

   * *Splitter.cs：* 實現一個螺栓，將句子分成單個單詞，併發出新的單詞流。

   * *Counter.cs：* 實現一個計算每個單詞的螺栓，併發出一個新的單詞流和每個單詞的計數。

     > [!NOTE]  
     > 這些 Bolt 會讀取和寫入資料流，但是您也可以使用 Bolt 與資料庫或服務等來源進行通訊。

3. 開啟 *Splitter.cs*。 預設情況下，它只有一個方法： `Execute`. 當`Execute`螺栓接收處理元組時，將調用該方法。 此時，您可以讀取和處理內送 Tuple，以及發出輸出 Tuple。

4. 以下列程式碼取代 `Splitter` 類別的內容：

    ```csharp
    private Context ctx;

    // Constructor
    public Splitter(Context ctx)
    {
        Context.Logger.Info("Splitter constructor called");
        this.ctx = ctx;

        // Declare Input and Output schemas
        Dictionary<string, List<Type>> inputSchema = new Dictionary<string, List<Type>>();
        // Input contains a tuple with a string field (the sentence)
        inputSchema.Add("default", new List<Type>() { typeof(string) });
        Dictionary<string, List<Type>> outputSchema = new Dictionary<string, List<Type>>();
        // Outbound contains a tuple with a string field (the word)
        outputSchema.Add("default", new List<Type>() { typeof(string) });
        this.ctx.DeclareComponentSchema(new ComponentStreamSchema(inputSchema, outputSchema));
    }

    // Get a new instance of the bolt
    public static Splitter Get(Context ctx, Dictionary<string, Object> parms)
    {
        return new Splitter(ctx);
    }

    // Called when a new tuple is available
    public void Execute(SCPTuple tuple)
    {
        Context.Logger.Info("Execute enter");

        // Get the sentence from the tuple
        string sentence = tuple.GetString(0);
        // Split at space characters
        foreach (string word in sentence.Split(' '))
        {
            Context.Logger.Info("Emit: {0}", word);
            //Emit each word
            this.ctx.Emit(new Values(word));
        }

        Context.Logger.Info("Execute exit");
    }
    ```

5. 開啟 *Counter.cs*，並將類別內容取代為下列代碼：

    ```csharp
    private Context ctx;

    // Dictionary for holding words and counts
    private Dictionary<string, int> counts = new Dictionary<string, int>();

    // Constructor
    public Counter(Context ctx)
    {
        Context.Logger.Info("Counter constructor called");
        // Set instance context
        this.ctx = ctx;

        // Declare Input and Output schemas
        Dictionary<string, List<Type>> inputSchema = new Dictionary<string, List<Type>>();
        // A tuple containing a string field - the word
        inputSchema.Add("default", new List<Type>() { typeof(string) });

        Dictionary<string, List<Type>> outputSchema = new Dictionary<string, List<Type>>();
        // A tuple containing a string and integer field - the word and the word count
        outputSchema.Add("default", new List<Type>() { typeof(string), typeof(int) });
        this.ctx.DeclareComponentSchema(new ComponentStreamSchema(inputSchema, outputSchema));
    }

    // Get a new instance
    public static Counter Get(Context ctx, Dictionary<string, Object> parms)
    {
        return new Counter(ctx);
    }

    // Called when a new tuple is available
    public void Execute(SCPTuple tuple)
    {
        Context.Logger.Info("Execute enter");

        // Get the word from the tuple
        string word = tuple.GetString(0);
        // Do we already have an entry for the word in the dictionary?
        // If no, create one with a count of 0
        int count = counts.ContainsKey(word) ? counts[word] : 0;
        // Increment the count
        count++;
        // Update the count in the dictionary
        counts[word] = count;

        Context.Logger.Info("Emit: {0}, count: {1}", word, count);
        // Emit the word and count information
        this.ctx.Emit(Constants.DEFAULT_STREAM_ID, new List<SCPTuple> { tuple }, new Values(word, count));
        Context.Logger.Info("Execute exit");
    }
    ```

### <a name="define-the-topology"></a>定義拓撲

Spout 和 Bolt 是以圖形方式排列，用以定義資料在元件之間的流動方式。 此拓撲的圖形如下：

![噴口和螺栓元件排列圖，風暴拓撲](./media/apache-storm-develop-csharp-visual-studio-topology/word-count-topology1.png)

出嘴發出分配給拆分器螺栓實例的句子。 Splitter Bolt 會將句子分成多個單字，並將這些單字分散到 Counter Bolt。

由於計數器實例在本地保存單詞計數，因此您希望確保特定單詞流向同一計數器螺栓實例。 每個執行個體會保持追蹤特定文字。 分割器 Bolt 會維持無狀態，因為分割器的哪個執行個體收到哪個句子並不重要。

打開*Program.cs*。 重要的方法是`GetTopologyBuilder`，用於定義提交到 Storm 的拓撲。 將 以下內容`GetTopologyBuilder`的內容替換為以下代碼，以實現前面描述的拓撲：

```csharp
// Create a new topology named 'WordCount'
TopologyBuilder topologyBuilder = new TopologyBuilder(
    "WordCount" + DateTime.Now.ToString("yyyyMMddHHmmss"));

// Add the spout to the topology.
// Name the component 'sentences'
// Name the field that is emitted as 'sentence'
topologyBuilder.SetSpout(
    "sentences",
    Spout.Get,
    new Dictionary<string, List<string>>()
    {
        {Constants.DEFAULT_STREAM_ID, new List<string>(){"sentence"}}
    },
    1);
// Add the splitter bolt to the topology.
// Name the component 'splitter'
// Name the field that is emitted 'word'
// Use suffleGrouping to distribute incoming tuples
//   from the 'sentences' spout across instances
//   of the splitter
topologyBuilder.SetBolt(
    "splitter",
    Splitter.Get,
    new Dictionary<string, List<string>>()
    {
        {Constants.DEFAULT_STREAM_ID, new List<string>(){"word"}}
    },
    1).shuffleGrouping("sentences");

// Add the counter bolt to the topology.
// Name the component 'counter'
// Name the fields that are emitted 'word' and 'count'
// Use fieldsGrouping to ensure that tuples are routed
//   to counter instances based on the contents of field
//   position 0 (the word). This could also have been
//   List<string>(){"word"}.
//   This ensures that the word 'jumped', for example, will always
//   go to the same instance
topologyBuilder.SetBolt(
    "counter",
    Counter.Get,
    new Dictionary<string, List<string>>()
    {
        {Constants.DEFAULT_STREAM_ID, new List<string>(){"word", "count"}}
    },
    1).fieldsGrouping("splitter", new List<int>() { 0 });

// Add topology config
topologyBuilder.SetTopologyConfig(new Dictionary<string, string>()
{
    {"topology.kryo.register","[\"[B\"]"}
});

return topologyBuilder;
```

## <a name="submit-the-topology"></a>提交拓撲

現在，您已準備好將拓撲提交到 HDInsight 群集。

1. 導航到**視圖** > **伺服器資源管理器**。

1. 按右鍵**Azure**，選擇"**連接到 Microsoft Azure 訂閱..."，** 然後完成登錄過程。

1. 在**解決方案資源管理器**中，按右鍵專案，然後選擇**在 HDInsight 上提交到風暴**。

1. 在 **"提交拓撲"** 對話方塊中，在 **"風暴群集**"下拉清單下，在 HDInsight 群集上選擇"風暴"，然後選擇"**提交**"。 您可以通過查看 **"輸出"** 窗格來檢查提交是否成功。

    成功提交拓撲後，應顯示群集的 **"風暴拓撲視圖"** 視窗。 從清單中選擇**WordCount**拓撲以查看有關正在運行的拓撲的資訊。

    ![風暴拓撲視圖視窗、HDInsight 群集、視覺工作室](./media/apache-storm-develop-csharp-visual-studio-topology/storm-topology-view.png)

    > [!NOTE]  
    > 您也可以從 [伺服器總管]**** 檢視 [Storm 拓撲]****。 展開**Azure** > **HDInsight，** 按右鍵 HDInsight 群集上的風暴，然後選擇 **"查看風暴拓撲**"。

    要查看有關拓撲中元件的資訊，請在關係圖中選擇一個元件。

1. 在 **"拓撲摘要"** 部分中，選擇 **"終止**"以停止拓撲。

    > [!NOTE]  
    > 除非停用 Storm 拓撲或刪除叢集，否則 Storm 拓撲會繼續執行。

## <a name="transactional-topology"></a>交易式拓撲

先前的拓撲為非交易式。 拓撲中的元件不實現重播消息的功能。 針對交易式拓撲範例，請建立專案，然後選取 [Storm 範例]**** 做為專案類型。

交易式拓撲會實作下列項目來支援重新執行資料：

* **中繼資料緩存**：spout 必須存儲有關所發出資料的中繼資料，以便在發生故障時可以再次檢索和發出資料。 此範例所發出的資料太少，因此為了重新執行，每個 Tuple 的原始資料都會儲存在字典中。

* **Ack**：拓撲中的每個 Bolt 都可以呼叫 `this.ctx.Ack(tuple)` 來認可它已順利處理 Tuple。 當所有螺栓都確認元組時，`Ack`將調用噴口的方法。 `Ack` 方法可讓 Spout 移除快取以重新執行的資料。

* **Fail**：每個 Bolt 都可以呼叫 `this.ctx.Fail(tuple)`，以指出 Tuple 的處理失敗。 這項失敗會傳播至 Spout 的 `Fail` 方法，在其中，可以使用快取的中繼資料來重新執行 Tuple。

* **序列識別碼**：發出 Tuple 時，可以指定唯一的序列識別碼。 此值標識用於重播 （`Ack`和`Fail`） 處理的元組。 例如，**在發出資料時，Storm 示例**專案中的分值使用以下方法調用：

  `this.ctx.Emit(Constants.DEFAULT_STREAM_ID, new Values(sentence), lastSeqId);`

  此代碼發出一個元組，其中包含預設流的句子，其中包含`lastSeqId`序列 ID 值。 在此示例中，`lastSeqId`對於發射的每個元組，都會遞增。

如 [Storm 範例]**** 專案中所示，在執行階段可以根據組態來設定元件是否為交易式。

## <a name="hybrid-topology-with-c-and-java"></a>採用 C# 和 Java 的混合式拓撲

您也可以使用 Data Lake Tools for Visual Studio 來建立混合式拓撲，其中有些元件是 C#，有些則是 Java。

針對混合式拓撲範例，請建立專案，然後選取 [Storm 混合式範例]****。 此範例類型將示範下列概念︰

* **JAVA 出口**和**C# 螺栓** `HybridTopology_javaSpout_csharpBolt` ：在類中定義。

  在`HybridTopologyTx_javaSpout_csharpBolt`類中定義事務版本。

* **C# 出口**和**JAVA 螺栓** `HybridTopology_csharpSpout_javaBolt` ：在類中定義。

  在`HybridTopologyTx_csharpSpout_javaBolt`類中定義事務版本。

  > [!NOTE]  
  > 這個版本也會示範如何使用文字檔中的 Clojure 程式碼做為 Java 元件。

若要切換為提交專案時所使用的拓撲，請在提交至叢集之前，將 `[Active(true)]` 陳述式移至您要使用的拓撲即可。

> [!NOTE]  
> 在 *JavaDependency* 資料夾中，所需的所有 Java 檔案都會提供為此專案的一部分。

創建和提交混合拓撲時請考慮以下事項：

* 用於`JavaComponentConstructor`為出樣或螺栓創建 JAVA 類的實例。

* 用於`microsoft.scp.storm.multilang.CustomizedInteropJSONSerializer`將資料序列化到或從 JAVA 物件到 JSON 的 JAVA 元件。

* 提交拓撲至伺服器時，您必須使用 [其他組態]**** 選項指定 [Java 檔案路徑]****。 指定的路徑應該是包含 JAVA 類的 JAR 檔的目錄。

### <a name="azure-event-hubs"></a>Azure 事件中心

SCP.NET 0.9.4.203 版引進了專用於事件中樞 Spout (從事件中樞讀取的 Java Spout) 的新類別和方法。 創建使用事件中心噴口的拓撲（例如，使用**Storm 事件Hub 閱讀器示例**範本）時，請使用以下 API：

* `EventHubSpoutConfig`類：創建包含分出元件配置的物件。

* `TopologyBuilder.SetEventHubSpout`方法：將事件中心分出元件添加到拓撲。

> [!NOTE]  
> 您仍必須使用`CustomizedInteropJSONSerializer`序列化由出樣器生成的資料。

## <a name="use-configurationmanager"></a>使用 ConfigurationManager

不要使用**組態管理員**從螺栓和出樣元件中檢索配置值。 這麼做會導致 Null 指標例外狀況。 相反，將專案的配置作為拓撲上下文中的鍵和值對傳遞到 Storm 拓撲中。 仰賴組態值的每個元件則必須在初始化期間從內容擷取這些組態值。

下列程式碼示範如何擷取這些值︰

```csharp
public class MyComponent : ISCPBolt
{
    // To hold configuration information loaded from context
    Configuration configuration;
    ...
    public MyComponent(Context ctx, Dictionary<string, Object> parms)
    {
        // Save a copy of the context for this component instance
        this.ctx = ctx;
        // If it exists, load the configuration for the component
        if(parms.ContainsKey(Constants.USER_CONFIG))
        {
            this.configuration = parms[Constants.USER_CONFIG] 
                as System.Configuration.Configuration;
        }
        // Retrieve the value of "Foo" from configuration
        var foo = this.configuration.AppSettings.Settings["Foo"].Value;
    }
    ...
}
```

如果您使用 `Get` 方法傳回元件的執行個體，您必須確定它會將 `Context` 和 `Dictionary<string, Object>` 參數同時傳遞至建構函式。 下列範例是會正確傳遞這些值的基本 `Get` 方法︰

```csharp
public static MyComponent Get(Context ctx, Dictionary<string, Object> parms)
{
    return new MyComponent(ctx, parms);
}
```

## <a name="how-to-update-scpnet"></a>如何更新 SCP.NET

最新版 SCP.NET 支援透過 NuGet 進行封裝升級。 當新的更新可用時，您會收到升級通知。 若要手動檢查升級，請遵循下列步驟：

1. 在 [方案總管]**** 中，以滑鼠右鍵按一下專案，然後選取 [管理 NuGet 套件]****。

2. 從封裝管理員，選取 [更新] ****。 如果SCP.NET支援包的更新可用，則列出它。 選擇"**更新**"包，然後在"**預覽更改"** 對話方塊中選擇 **"確定**"以安裝它。

> [!IMPORTANT]  
> 如果您的專案是利用未使用 NuGet 的舊版 SCP.NET 建立，您必須執行下列步驟更新為新的版本：
>
> 1. 在 [方案總管]**** 中，以滑鼠右鍵按一下專案，然後選取 [管理 NuGet 套件]****。
> 2. 使用 **"搜索"** 欄位，搜索專案，然後`Microsoft.SCP.Net.SDK`添加到專案中。

## <a name="troubleshoot-common-issues-with-topologies"></a>針對拓撲常見問題進行疑難排解

### <a name="null-pointer-exceptions"></a>Null 指標例外狀況

當您使用帶有基於 Linux 的 HDInsight 群集的 C# 拓撲時，使用**組態管理員**在運行時讀取配置設置的螺栓和分出元件可能會返回空指標異常。

專案的設定會以拓撲內容中金鑰和值組的形式傳遞至 Storm 拓撲。 可以從在初始化元件時傳遞給元件的字典物件中檢索它。

有關詳細資訊，請參閱本文檔的["使用組態管理員](#use-configurationmanager)"部分。

### <a name="systemtypeloadexception"></a>System.TypeLoadException

當您使用具有基於 Linux 的 HDInsight 群集的 C# 拓撲時，可能會遇到以下錯誤：

`System.TypeLoadException: Failure has occurred while loading a type.`

當您使用與 Mono 支援的 .NET 版本不相容的二進位檔案時，將發生此錯誤。

對於以 Linux 為基礎的 HDInsight 叢集，請確定專案使用針對 .NET 4.5 編譯的二進位檔。

### <a name="test-a-topology-locally"></a>在本機測試拓撲

儘管將拓撲部署到群集很容易，但在某些情況下，您可能需要在本地測試拓撲。 使用以下步驟在開發環境中本地運行和測試本文中的示例拓撲。

> [!WARNING]  
> 本機測試只適用於僅限 C# 的基本拓撲。 您不得將本機測試使用於混合式拓撲或使用多個資料流的拓撲。

1. 在**方案總管**中，於專案上按一下滑鼠右鍵，然後選取 [屬性]****。 在專案屬性中。 然後將**輸出類型**更改為**主控台應用程式**。

   ![HDInsight 風暴應用、專案屬性、輸出類型](./media/apache-storm-develop-csharp-visual-studio-topology/hdi-output-type-window.png)

   > [!NOTE]
   > 請記得先將 [輸出類型]**** 變更回 [類別庫]****，再將拓撲部署至叢集。

1. 在 [方案總管]**** 中，以滑鼠右鍵按一下專案，然後選取 [新增]**** > [新項目]****。 選擇**類**，然後輸入*LocalTest.cs*作為類名稱。 最後，選擇 **"添加**"。

1. 打開*LocalTest.cs*，並在頂部添加`using`以下語句：

    ```csharp
    using Microsoft.SCP;
    ```

1. 使用以下代碼作為類的內容`LocalTest`：

    ```csharp
    // Drives the topology components
    public void RunTestCase()
    {
        // An empty dictionary for use when creating components
        Dictionary<string, Object> emptyDictionary = new Dictionary<string, object>();

        #region Test the spout
        {
            Console.WriteLine("Starting spout");
            // LocalContext is a local-mode context that can be used to initialize
            // components in the development environment.
            LocalContext spoutCtx = LocalContext.Get();
            // Get a new instance of the spout, using the local context
            Spout sentences = Spout.Get(spoutCtx, emptyDictionary);

            // Emit 10 tuples
            for (int i = 0; i < 10; i++)
            {
                sentences.NextTuple(emptyDictionary);
            }
            // Use LocalContext to persist the data stream to file
            spoutCtx.WriteMsgQueueToFile("sentences.txt");
            Console.WriteLine("Spout finished");
        }
        #endregion

        #region Test the splitter bolt
        {
            Console.WriteLine("Starting splitter bolt");
            // LocalContext is a local-mode context that can be used to initialize
            // components in the development environment.
            LocalContext splitterCtx = LocalContext.Get();
            // Get a new instance of the bolt
            Splitter splitter = Splitter.Get(splitterCtx, emptyDictionary);

            // Set the data stream to the data created by the spout
            splitterCtx.ReadFromFileToMsgQueue("sentences.txt");
            // Get a batch of tuples from the stream
            List<SCPTuple> batch = splitterCtx.RecvFromMsgQueue();
            // Process each tuple in the batch
            foreach (SCPTuple tuple in batch)
            {
                splitter.Execute(tuple);
            }
            // Use LocalContext to persist the data stream to file
            splitterCtx.WriteMsgQueueToFile("splitter.txt");
            Console.WriteLine("Splitter bolt finished");
        }
        #endregion

        #region Test the counter bolt
        {
            Console.WriteLine("Starting counter bolt");
            // LocalContext is a local-mode context that can be used to initialize
            // components in the development environment.
            LocalContext counterCtx = LocalContext.Get();
            // Get a new instance of the bolt
            Counter counter = Counter.Get(counterCtx, emptyDictionary);

            // Set the data stream to the data created by splitter bolt
            counterCtx.ReadFromFileToMsgQueue("splitter.txt");
            // Get a batch of tuples from the stream
            List<SCPTuple> batch = counterCtx.RecvFromMsgQueue();
            // Process each tuple in the batch
            foreach (SCPTuple tuple in batch)
            {
                counter.Execute(tuple);
            }
            // Use LocalContext to persist the data stream to file
            counterCtx.WriteMsgQueueToFile("counter.txt");
            Console.WriteLine("Counter bolt finished");
        }
        #endregion
    }
    ```

    請用一些時間閱讀程式碼註解。 此代碼用於`LocalContext`在開發環境中運行元件。 它將元件之間的資料流程持久化到本地磁碟機上的文字檔。

1. 打開*Program.cs*，並將以下代碼添加到`Main`方法：

    ```csharp
    Console.WriteLine("Starting tests");
    System.Environment.SetEnvironmentVariable("microsoft.scp.logPrefix", "WordCount-LocalTest");
    // Initialize the runtime
    SCPRuntime.Initialize();

    //If we are not running under the local context, throw an error
    if (Context.pluginType != SCPPluginType.SCP_NET_LOCAL)
    {
        throw new Exception(string.Format("unexpected pluginType: {0}", Context.pluginType));
    }
    // Create test instance
    LocalTest tests = new LocalTest();
    // Run tests
    tests.RunTestCase();
    Console.WriteLine("Tests finished");
    Console.ReadKey();
    ```

1. 保存更改，然後選擇**F5**或選擇**調試** > **啟動調試**以啟動專案。 應該會出現主控台視窗，並記錄測試進行的狀態。 出現`Tests finished`時，選擇任何鍵關閉視窗。

1. 使用 [Windows 檔案總管]**** 找出包含您專案的目錄。 （例如 *：C：\\\\\<使用者your_user_name>\\源\\存儲庫\\WordCount\\WordCount*.然後在此目錄中，打開*Bin，* 然後選擇*調試*。 您應該看到測試回合時生成的文字檔：*句子.txt、**計數器.txt*和*splitter.txt*。 開啟每個文字檔，並檢查資料。

   > [!NOTE]  
   > 字串資料會在這些檔案中保存為十進位值的陣列。 例如，`[[97,103,111]]`在**splitter.txt**檔中表示*單詞之前*。

> [!NOTE]  
> 在部署到 HDInsight 群集上的 Storm 之前，請確保將**專案類型**重新設置為專案屬性中的**類庫**。

### <a name="log-information"></a>記錄資訊

您可以使用 `Context.Logger`，輕鬆地記錄拓撲元件中的資訊。 例如，下列命令會建立一個參考性記錄項目：

`Context.Logger.Info("Component started");`

您可以從 **[Hadoop 服務記錄]** \(位於**伺服器總管中**) 檢視記錄的資訊。 展開 Storm on HDInsight 叢集的項目，然後展開 [Hadoop 服務記錄]****。 最後，選取要檢視的記錄檔。

> [!NOTE]  
> 記錄會儲存在您叢集所使用的 Azure 儲存體帳戶中。 若要在 Visual Studio 中檢視記錄，您必須登入至擁有儲存體帳戶的 Azure 訂用帳戶。

### <a name="view-error-information"></a>檢視錯誤資訊

若要檢視執行中拓撲中所發生的錯誤，請使用下列步驟：

1. 從**伺服器資源管理器**中，按右鍵 HDInsight 群集上的風暴，然後選擇 **"查看風暴拓撲**"。

   針對 **Spout** 和 **Bolt**，[最後一個錯誤]**** 資料行會含有最後一個錯誤的詳細資訊。

2. 為列出的錯誤元件選擇**噴口 ID**或**螺栓 ID。** 詳細資訊頁在頁面底部的 **"錯誤"** 部分中顯示其他錯誤資訊。

3. 要獲取更多資訊，請從頁面的 **"執行器"** 部分選擇 **"埠**"，以查看最後幾分鐘的 Storm 輔助角色日誌。

### <a name="errors-submitting-topologies"></a>提交拓撲的錯誤

如果遇到向 HDInsight 提交拓撲的錯誤，可以找到處理 HDInsight 群集上拓撲提交的伺服器端元件的日誌。 要下載這些日誌，請使用命令列中的以下命令：

```cmd
scp sshuser@clustername-ssh.azurehdinsight.net:/var/log/hdinsight-scpwebapi/hdinsight-scpwebapi.out .
```

以叢集的 SSH 使用者帳戶取代 *sshuser*。 將*群集名稱*替換為 HDInsight 群集的名稱。 如需搭配 HDInsight 使用 `scp` 和 `ssh` 的詳細資訊，請參閱[搭配 HDInsight 使用 SSH](../hdinsight-hadoop-linux-use-ssh-unix.md)。

提交失敗有多種原因：

* JDK 未安裝或不在路徑中。
* 提交中不包括所需的 JAVA 依賴項。
* 依賴項不相容。
* 拓撲名稱重複。

如果*hdinsight-scpwebapi.out*日誌檔包含 ，`FileNotFoundException`則異常可能由以下條件引起：

* JDK 不在開發環境中的路徑中。 確認已在開發環境中安裝 JDK，且 `%JAVA_HOME%/bin` 在路徑中。
* 缺少 JAVA 依賴項。 請確保在提交中包含任何必需的 .jar 檔。

## <a name="next-steps"></a>後續步驟

如需從事件中樞處理資料的範例，請參閱[利用 Storm on HDInsight 處理 Azure 事件中樞的事件](apache-storm-develop-csharp-event-hub-topology.md)。

如需將資料流的資料分成多個資料流的 C# 拓撲範例，請參閱 [C# Storm 範例](https://github.com/Blackmist/csharp-storm-example)。

若要探索建立 C# 拓撲的詳細資訊，請參閱 [GitHub](https://github.com/hdinsight/hdinsight-storm-examples/blob/master/SCPNet-GettingStarted.md) \(英文\)。

如需更多使用 HDInsight 的方式，或更多 Storm on HDInsight 範例，請參閱下列文件：

**Microsoft SCP.NET**

* [Azure HDInsight 中阿帕奇風暴的 SCP 程式設計指南](apache-storm-scp-programming-guide.md)

**Apache Storm on HDInsight**

* [部署和管理 HDInsight 上的 Apache Storm 拓撲](apache-storm-deploy-monitor-topology-linux.md)
* [Azure HDInsight 中的範例 Apache Storm 拓撲](apache-storm-example-topology.md)

**Apache Hadoop on HDInsight**

* [Azure HDInsight 上的 Apache Hive 和 HiveQL 是什麼？](../hadoop/hdinsight-use-hive.md)
* [搭配 MapReduce 與 HDInsight 上的 Apache Hadoop](../hadoop/hdinsight-use-mapreduce.md)

**Apache HBase on HDInsight**

* [使用 Azure HDInsight 中的 Apache HBase](../hbase/apache-hbase-tutorial-get-started-linux.md)
