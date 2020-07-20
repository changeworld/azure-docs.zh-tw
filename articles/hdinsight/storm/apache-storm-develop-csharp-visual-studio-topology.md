---
title: 採用 Visual Studio 和 C# 的 Apache Storm 拓撲 - Azure HDInsight
description: 了解如何使用 C# 建立 Storm 拓撲。 使用適用于 Visual Studio 的 Hadoop 工具，在 Visual Studio 中建立字數統計拓撲。
ROBOTS: NOINDEX
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: how-to
ms.date: 12/31/2019
ms.openlocfilehash: 3645b6752a49a0cf2544d170ac55a77cc8ae5e40
ms.sourcegitcommit: 124f7f699b6a43314e63af0101cd788db995d1cb
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/08/2020
ms.locfileid: "86082007"
---
# <a name="develop-c-topologies-for-apache-storm-by-using-the-data-lake-tools-for-visual-studio"></a>使用 Data Lake Tools for Visual Studio 開發 Apache Storm 的 C# 拓撲

了解如何使用 Azure Data Lake (Apache Hadoop) Tools for Visual Studio 來建立 C# Apache Storm 拓撲。 這份文件逐步解說如何在 Visual Studio 中建立 Storm 專案，在本機測試該專案，並將它部署至 Azure HDInsight 叢集上的 Apache Storm。

您也會學習如何建立使用 C# 和 Java 元件的混合式拓撲。

C # 拓撲使用 .NET 4.5，並使用 Mono 在 HDInsight 叢集上執行。 如需潛在不相容的資訊，請參閱[Mono 相容性](https://www.mono-project.com/docs/about-mono/compatibility/)。 若要使用 c # 拓撲，您必須將 `Microsoft.SCP.Net.SDK` 專案所使用的 NuGet 套件更新為為0.10.0.6 版或更新版本。 套件版本也必須符合 HDInsight 上安裝的 Storm 主要版本。

| HDInsight 版本 | Apache Storm 版本 | SCP.NET 版本 | 預設 Mono 版本 |
|:-----------------:|:-------------:|:---------------:|:--------------------:|
| 3.4 | 0.10.0.x | 0.10.0.x | 3.2.8 |
| 3.5 | 1.0.2.x | 1.0.0.x | 4.2.1 |
| 3.6 | 1.1.0.x | 1.0.0.x | 4.2.8 |

## <a name="prerequisite"></a>必要條件

HDInsight 上的 Apache Storm 叢集。 請參閱[使用 Azure 入口網站建立 Apache Hadoop 叢集](../hdinsight-hadoop-create-linux-clusters-portal.md)，然後選取 [Storm]**** 作為 [叢集類型]****。

## <a name="install-visual-studio"></a>安裝 Visual Studio

您可以使用[Visual Studio](https://visualstudio.microsoft.com/downloads/)，透過 SCP.NET 開發 c # 拓撲。 此處的指示使用 Visual Studio 2019，但您也可以使用舊版的 Visual Studio。

## <a name="install-data-lake-tools-for-visual-studio"></a>安裝 Data Lake Tools for Visual Studio

若要安裝 Data Lake Tools for Visual Studio，請遵循[開始使用 Data Lake Tools for Visual Studio](../hadoop/apache-hadoop-visual-studio-tools-get-started.md#install-data-lake-tools-for-visual-studio) 中的步驟。

## <a name="install-java"></a>安裝 Java

當您從 Visual Studio 提交 Storm 拓撲時，SCP.NET 會產生包含拓撲及相依性的 ZIP 檔案。 系統會使用 Java 來建立這些 ZIP 檔案，因為它所使用的格式和以 Linux 為基礎的叢集更具相容性。

1. 在您的開發環境上安裝 Java Developer Kit (JDK) 7 或更新版本。 您可以從 [Oracle](https://openjdk.java.net/) \(英文\) 取得 Oracle JDK。 您也可以使用[其他 Java 發佈](/java/azure/jdk/) \(英文\)。

2. 將 `JAVA_HOME` 環境變數設定為包含 JAVA 的目錄。

3. 設定 `PATH` 環境變數以包含 `%JAVA_HOME%\bin` 目錄。

您可以建立並執行下列 c # 主控台應用程式，以確認已正確安裝 JAVA 和 JDK：

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
> 並非所有範本都能與以 Linux 為基礎的 HDInsight 搭配運作。 範本所使用的 NuGet 套件可能無法與 Mono 相容。 若要找出潛在的問題，請參閱[Mono 相容性](https://www.mono-project.com/docs/about-mono/compatibility/)和使用[.net 可攜性分析器](../hdinsight-hadoop-migrate-dotnet-to-linux.md#automated-portability-analysis)。

在這份文件的步驟中，您會使用基本 Storm 應用程式專案類型來建立拓撲。

### <a name="apache-hbase-templates"></a>Apache HBase 範本

HBase 讀取器和寫入器範本會使用 HBase REST API (而不是 HBase Java API) 來與 HDInsight 叢集上的 HBase 通訊。

### <a name="eventhub-templates"></a>EventHub 範本

> [!IMPORTANT]  
> 包含在 EventHub Reader 讀取器範本中，以 Java 為基礎的 EventHub Spout 元件可能無法與 Storm on HDInsight 3.5 版或更新版本搭配使用。 此元件的更新版本可在 [GitHub](https://github.com/hdinsight/hdinsight-storm-examples/tree/master/HDI3.5/lib) \(英文\) 取得。

如需使用此元件和搭配 Storm on HDInsight 3.5 使用的範例拓撲，請參閱 [GitHub](https://github.com/Azure-Samples/hdinsight-dotnet-java-storm-eventhub) \(英文\)。

## <a name="create-a-c-topology"></a>建立 C# 拓撲

若要在 Visual Studio 中建立 c # 拓撲專案：

1. 開啟 Visual Studio。

1. 在 [開始] 視窗中，選取 [建立新專案]。

1. 在 [**建立新專案**] 視窗中，依序流覽並挑選 [**風暴應用程式**]，然後選取 **[下一步]**。

1. 在 [**設定您的新專案**] 視窗中，輸入*WordCount*的**專案名稱**，移至或建立專案的**位置**目錄路徑，然後選取 [**建立**]。

    ![風暴應用程式，設定您的 [新增專案] 對話方塊，Visual Studio](./media/apache-storm-develop-csharp-visual-studio-topology/apache-storm-new-project.png)

建立專案之後，您應該會有下列檔案：

* *Program.cs*：專案的拓撲定義。 預設會建立含有一個 Spout 和一個 Bolt 的預設拓撲。

* *Spout.cs*：發出亂數的範例 Spout。

* *Bolt.cs*：保留 Spout 所發出數字之計數的範例 Bolt。

當您建立專案時，NuGet 會下載最新的 [SCP.NET 套件](https://www.nuget.org/packages/Microsoft.SCP.Net.SDK/) \(英文\)。

### <a name="implement-the-spout"></a>實作 Spout

接下來，新增 spout 的程式碼，用來從外部來源讀取拓撲中的資料。 此 spout 會將句子隨機發出至拓撲。

1. 開啟 *Spout.cs*。 Spout 的主要元件如下：

   * `NextTuple`：允許 spout 發出新的元組時，由風暴呼叫。

   * `Ack`（僅限交易式拓撲）：針對從 spout 傳送的元組，處理拓撲中其他元件所啟動的通知。 認可 Tuple 可讓 Spout 知道下游元件已順利處理 Tuple。

   * `Fail`（僅限交易式拓撲）：處理無法處理拓撲中其他元件的元組。 執行 `Fail` 方法可讓您重新發出元組，以便再次處理。

2. 將類別的內容取代為 `Spout` 下列文字：

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

現在，在此範例中建立兩個風暴螺栓：

1. 刪除專案中的現有 *Bolt.cs* 檔。

2. 在**方案總管**中，以滑鼠右鍵按一下專案，然後選取 [**加入**  >  **新專案**]。 從清單中，選取 [Storm Bolt]****，並輸入 *Splitter.cs* 做為名稱。 在新檔案的程式碼中，將命名空間名稱變更為 `WordCount` 。 然後重複此程式，以建立名為*Counter.cs*的第二個螺栓。

   * *Splitter.cs*：執行將句子分割成個別單字，併發出新的單字資料流程的螺栓。

   * *Counter.cs*：執行會計算每個字組的螺栓，併發出新的單字資料流程和每個單字的計數。

     > [!NOTE]  
     > 這些 Bolt 會讀取和寫入資料流，但是您也可以使用 Bolt 與資料庫或服務等來源進行通訊。

3. 開啟 *Splitter.cs*。 根據預設，它只有一個方法： `Execute` 。 `Execute`當螺栓收到要處理的元組時，會呼叫方法。 此時，您可以讀取和處理內送 Tuple，以及發出輸出 Tuple。

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

![Spout 和螺栓元件排列圖表，暴拓朴](./media/apache-storm-develop-csharp-visual-studio-topology/word-count-topology1.png)

Spout 會發出散發給分隔器螺栓實例的句子。 Splitter Bolt 會將句子分成多個單字，並將這些單字分散到 Counter Bolt。

因為計數器實例會在本機保留字數統計，所以您想要確保特定單字流向相同的計數器螺栓實例。 每個執行個體會保持追蹤特定文字。 分割器 Bolt 會維持無狀態，因為分割器的哪個執行個體收到哪個句子並不重要。

開啟*Program.cs*。 重要的方法是 `GetTopologyBuilder` ，用來定義提交至風暴的拓撲。 將的內容取代為 `GetTopologyBuilder` 下列程式碼，以執行先前所述的拓撲：

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

您現在已準備好將拓撲提交至您的 HDInsight 叢集。

1. 流覽至**View**  >  **伺服器總管**。

1. 以滑鼠右鍵按一下 [ **Azure**]，選取 **[連線至 Microsoft Azure 訂用帳戶 ...]**，然後完成登入程式。

1. 在**方案總管**中，以滑鼠右鍵按一下專案，然後選擇 [**在 HDInsight 上提交至風暴**]。

1. 在 [**提交拓撲**] 對話方塊的 [**風暴**叢集] 下拉式清單中，選擇您在 HDInsight 叢集上的 [風暴]，然後選取 [**提交**]。 您可以藉由查看 [**輸出**] 窗格來檢查提交是否成功。

    成功提交拓撲時，應該會出現叢集的 [**風暴拓撲] 視圖**視窗。 從清單中選擇 [ **WordCount**拓撲]，以查看執行中拓撲的相關資訊。

    ![風暴拓朴視圖視窗，HDInsight 叢集，Visual Studio](./media/apache-storm-develop-csharp-visual-studio-topology/storm-topology-view.png)

    > [!NOTE]  
    > 您也可以從 [伺服器總管]**** 檢視 [Storm 拓撲]****。 展開 [ **Azure**  >  **HDInsight**]，以滑鼠右鍵按一下 HDInsight 叢集上的 [風暴]，然後選擇 [ **View 暴拓朴**]。

    若要查看拓撲中元件的相關資訊，請選取圖表中的元件。

1. 在 [**拓撲摘要**] 區段中，選取 [**終止**] 以停止拓撲。

    > [!NOTE]  
    > 除非停用 Storm 拓撲或刪除叢集，否則 Storm 拓撲會繼續執行。

## <a name="transactional-topology"></a>交易式拓撲

先前的拓撲為非交易式。 拓撲中的元件不會實作為重新執行訊息的功能。 針對交易式拓撲範例，請建立專案，然後選取 [Storm 範例]**** 做為專案類型。

交易式拓撲會實作下列項目來支援重新執行資料：

* **中繼資料**快取： spout 必須儲存所發出資料的中繼資料，以便在發生失敗時，能夠抓取和發出資料。 此範例所發出的資料太少，因此為了重新執行，每個 Tuple 的原始資料都會儲存在字典中。

* **Ack**：拓撲中的每個 Bolt 都可以呼叫 `this.ctx.Ack(tuple)` 來認可它已順利處理 Tuple。 當所有螺栓都已認可元組時， `Ack` 就會叫用 spout 的方法。 `Ack` 方法可讓 Spout 移除快取以重新執行的資料。

* **Fail**：每個 Bolt 都可以呼叫 `this.ctx.Fail(tuple)`，以指出 Tuple 的處理失敗。 這項失敗會傳播至 Spout 的 `Fail` 方法，在其中，可以使用快取的中繼資料來重新執行 Tuple。

* **序列識別碼**：發出 Tuple 時，可以指定唯一的序列識別碼。 這個值會識別重新執行（ `Ack` 和）處理的元組 `Fail` 。 例如，當發出資料時，[**風暴範例**] 專案中的 spout 會使用下列方法呼叫：

  `this.ctx.Emit(Constants.DEFAULT_STREAM_ID, new Values(sentence), lastSeqId);`

  此程式碼會將包含句子的元組，發出至預設資料流程，並在中包含順序識別碼值 `lastSeqId` 。 在此範例中， `lastSeqId` 會針對每個發出的元組遞增。

如 [Storm 範例]**** 專案中所示，在執行階段可以根據組態來設定元件是否為交易式。

## <a name="hybrid-topology-with-c-and-java"></a>採用 C# 和 Java 的混合式拓撲

您也可以使用 Data Lake Tools for Visual Studio 來建立混合式拓撲，其中有些元件是 C#，有些則是 Java。

針對混合式拓撲範例，請建立專案，然後選取 [Storm 混合式範例]****。 此範例類型將示範下列概念︰

* **JAVA spout**和**c # 閃電**：在類別中定義 `HybridTopology_javaSpout_csharpBolt` 。

  交易式版本定義于類別中 `HybridTopologyTx_javaSpout_csharpBolt` 。

* **C # spout**和**JAVA 螺栓**：在類別中定義 `HybridTopology_csharpSpout_javaBolt` 。

  交易式版本定義于類別中 `HybridTopologyTx_csharpSpout_javaBolt` 。

  > [!NOTE]  
  > 這個版本也會示範如何使用文字檔中的 Clojure 程式碼做為 Java 元件。

若要切換為提交專案時所使用的拓撲，請在提交至叢集之前，將 `[Active(true)]` 陳述式移至您要使用的拓撲即可。

> [!NOTE]  
> 在 *JavaDependency* 資料夾中，所需的所有 Java 檔案都會提供為此專案的一部分。

當您建立和提交混合式拓撲時，請考慮下列事項：

* 使用 `JavaComponentConstructor` 來為 spout 或螺栓建立 JAVA 類別的實例。

* 用 `microsoft.scp.storm.multilang.CustomizedInteropJSONSerializer` 來將 java 元件的資料傳入或傳出至 JSON。

* 提交拓撲至伺服器時，您必須使用 [其他組態]**** 選項指定 [Java 檔案路徑]****。 指定的路徑應該是具有 JAR 檔案的目錄，其中包含您的 JAVA 類別。

### <a name="azure-event-hubs"></a>Azure 事件中心

SCP.NET 0.9.4.203 版引進了專用於事件中樞 Spout (從事件中樞讀取的 Java Spout) 的新類別和方法。 當您建立使用事件中樞 spout 的拓撲時（例如，使用「**風暴 EventHub 讀取器」範例**範本），請使用下列 api：

* `EventHubSpoutConfig`class：建立物件，其中包含 spout 元件的設定。

* `TopologyBuilder.SetEventHubSpout`方法：將事件中樞 spout 元件新增至拓撲。

> [!NOTE]  
> 您仍然必須使用 `CustomizedInteropJSONSerializer` 來序列化 spout 所產生的資料。

## <a name="use-configurationmanager"></a>使用 ConfigurationManager

請勿使用**ConfigurationManager**來取出來自螺栓和 spout 元件的設定值。 這麼做會導致 Null 指標例外狀況。 相反地，請將專案的設定傳遞至風暴拓撲，做為拓撲內容中的索引鍵和值組。 仰賴組態值的每個元件則必須在初始化期間從內容擷取這些組態值。

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

2. 從封裝管理員，選取 [更新] ****。 如果有可用的 SCP.NET 支援封裝更新，則會列出。 選取套件的 [**更新**]，然後在 [**預覽變更**] 對話方塊中，選取 **[確定]** 以進行安裝。

> [!IMPORTANT]  
> 如果您的專案是利用未使用 NuGet 的舊版 SCP.NET 建立，您必須執行下列步驟更新為新的版本：
>
> 1. 在 [方案總管]**** 中，以滑鼠右鍵按一下專案，然後選取 [管理 NuGet 套件]****。
> 2. 使用 [**搜尋**] 欄位，搜尋，然後將加入 `Microsoft.SCP.Net.SDK` 至專案。

## <a name="troubleshoot-common-issues-with-topologies"></a>針對拓撲常見問題進行疑難排解

### <a name="null-pointer-exceptions"></a>Null 指標例外狀況

當您使用 c # 拓撲搭配以 Linux 為基礎的 HDInsight 叢集時，在執行時間使用**ConfigurationManager**來讀取設定值的螺栓和 spout 元件可能會傳回 null 指標例外狀況。

專案的設定會以拓撲內容中金鑰和值組的形式傳遞至 Storm 拓撲。 它可以在初始化時，從傳遞至元件的字典物件中抓取。

如需詳細資訊，請參閱本檔的[Use ConfigurationManager](#use-configurationmanager)一節。

### <a name="systemtypeloadexception"></a>System.TypeLoadException

當您搭配使用 c # 拓撲與以 Linux 為基礎的 HDInsight 叢集時，可能會遇到下列錯誤：

`System.TypeLoadException: Failure has occurred while loading a type.`

當您使用的二進位檔與 Mono 支援的 .NET 版本不相容時，就會發生此錯誤。

對於以 Linux 為基礎的 HDInsight 叢集，請確定專案使用針對 .NET 4.5 編譯的二進位檔。

### <a name="test-a-topology-locally"></a>在本機測試拓撲

雖然很容易就能將拓撲部署至叢集，但在某些情況下，您可能需要在本機測試拓撲。 使用下列步驟，在您的開發環境中，于本機執行並測試本文中的範例拓撲。

> [!WARNING]  
> 本機測試只適用於僅限 C# 的基本拓撲。 您不得將本機測試使用於混合式拓撲或使用多個資料流的拓撲。

1. 在**方案總管**中，於專案上按一下滑鼠右鍵，然後選取 [屬性]****。 在專案屬性中。 然後將 [**輸出類型**] 變更為 [**主控台應用程式**]。

   ![HDInsight 風暴應用程式，專案屬性，輸出類型](./media/apache-storm-develop-csharp-visual-studio-topology/hdi-output-type-window.png)

   > [!NOTE]
   > 請記得先將 [輸出類型]**** 變更回 [類別庫]****，再將拓撲部署至叢集。

1. 在 [方案總管]**** 中，以滑鼠右鍵按一下專案，然後選取 [新增]**** > [新項目]****。 選取 [**類別**]，然後輸入*LocalTest.cs*做為類別名稱。 最後，選取 [**新增**]。

1. 開啟*LocalTest.cs*，並 `using` 在頂端新增下列語句：

    ```csharp
    using Microsoft.SCP;
    ```

1. 使用下列程式碼做為類別的內容 `LocalTest` ：

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

    請用一些時間閱讀程式碼註解。 這段程式碼會使用在 `LocalContext` 開發環境中執行元件。 它會將元件之間的資料流程保存至本機磁片磁碟機上的文字檔。

1. 開啟*Program.cs*，並將下列程式碼新增至 `Main` 方法：

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

1. 儲存變更，然後選取**F5**或選擇 [ **Debug**] [  >  **開始調試**] 來啟動專案。 應該會出現主控台視窗，並記錄測試進行的狀態。 `Tests finished`出現時，選取 [任何] 鍵以關閉視窗。

1. 使用 [Windows 檔案總管]**** 找出包含您專案的目錄。 （例如： *C： \\ Users \\ \<your_user_name> \\ source \\ 存放庫 \\ WordCount \\ WordCount*）。然後在此目錄中，開啟 [ *Bin*]，然後選取 [ *Debug*]。 您應該會看到測試執行時所產生的文字檔： *sentences.txt*、 *counter.txt*和*splitter.txt*。 開啟每個文字檔，並檢查資料。

   > [!NOTE]  
   > 字串資料會在這些檔案中保存為十進位值的陣列。 例如， `[[97,103,111]]` 在**splitter.txt**檔案中代表一詞*前*。

> [!NOTE]  
> 請務必先將**專案類型**設定回專案屬性中的**類別庫**，然後再部署到 HDInsight 叢集上的「風暴」。

### <a name="log-information"></a>記錄資訊

您可以使用 `Context.Logger`，輕鬆地記錄拓撲元件中的資訊。 例如，下列命令會建立一個參考性記錄項目：

`Context.Logger.Info("Component started");`

您可以從 **[Hadoop 服務記錄]** \(位於**伺服器總管中**) 檢視記錄的資訊。 展開 Storm on HDInsight 叢集的項目，然後展開 [Hadoop 服務記錄]****。 最後，選取要檢視的記錄檔。

> [!NOTE]  
> 記錄會儲存在您叢集所使用的 Azure 儲存體帳戶中。 若要在 Visual Studio 中檢視記錄，您必須登入至擁有儲存體帳戶的 Azure 訂用帳戶。

### <a name="view-error-information"></a>檢視錯誤資訊

若要檢視執行中拓撲中所發生的錯誤，請使用下列步驟：

1. 在**伺服器總管**中，以滑鼠右鍵按一下 HDInsight 叢集上的 [風暴]，然後選取 [ **View 風暴拓朴**]。

   針對 **Spout** 和 **Bolt**，[最後一個錯誤]**** 資料行會含有最後一個錯誤的詳細資訊。

2. 為已列出錯誤的元件選取 [ **SPOUT 識別碼**] 或 [**螺栓識別碼**]。 [詳細資料] 頁面會在頁面底部的 [**錯誤**] 區段中顯示其他錯誤資訊。

3. 若要取得詳細資訊，請從頁面的 **[執行**程式] 區段中選取**埠**，以查看過去幾分鐘的風暴背景工作記錄檔。

### <a name="errors-submitting-topologies"></a>提交拓撲的錯誤

如果您在提交拓撲至 HDInsight 時遇到錯誤，您可以尋找在 HDInsight 叢集上處理拓撲提交之伺服器端元件的記錄檔。 若要下載這些記錄，請從命令列使用下列命令：

```cmd
scp sshuser@clustername-ssh.azurehdinsight.net:/var/log/hdinsight-scpwebapi/hdinsight-scpwebapi.out .
```

以叢集的 SSH 使用者帳戶取代 *sshuser*。 以 HDInsight 叢集的名稱取代*clustername* 。 如需搭配 HDInsight 使用 `scp` 和 `ssh` 的詳細資訊，請參閱[搭配 HDInsight 使用 SSH](../hdinsight-hadoop-linux-use-ssh-unix.md)。

提交失敗有多種原因：

* JDK 未安裝，或不在路徑中。
* 提交中不包含必要的 JAVA 相依性。
* 相依性不相容。
* 拓撲名稱重複。

如果*hdinsight-scpwebapi*記錄檔包含 `FileNotFoundException` ，例外狀況可能是由下列情況所造成：

* JDK 不在開發環境的路徑中。 確認已在開發環境中安裝 JDK，且 `%JAVA_HOME%/bin` 在路徑中。
* 您缺少 JAVA 相依性。 請確定您在提交過程中包含任何必要的 .jar 檔案。

## <a name="next-steps"></a>後續步驟

如需從事件中樞處理資料的範例，請參閱[利用 Storm on HDInsight 處理 Azure 事件中樞的事件](apache-storm-develop-csharp-event-hub-topology.md)。

如需將資料流的資料分成多個資料流的 C# 拓撲範例，請參閱 [C# Storm 範例](https://github.com/Blackmist/csharp-storm-example)。

若要探索建立 C# 拓撲的詳細資訊，請參閱 [GitHub](https://github.com/hdinsight/hdinsight-storm-examples/blob/master/SCPNet-GettingStarted.md) \(英文\)。

如需更多使用 HDInsight 的方式，或更多 Storm on HDInsight 範例，請參閱下列文件：

**Microsoft SCP.NET**

* [Azure HDInsight 中 Apache Storm 的 SCP 程式設計指南](apache-storm-scp-programming-guide.md)

**Apache Storm on HDInsight**

* [部署和管理 HDInsight 上的 Apache Storm 拓撲](apache-storm-deploy-monitor-topology-linux.md)
* [Azure HDInsight 中的範例 Apache Storm 拓撲](apache-storm-example-topology.md)

**Apache Hadoop on HDInsight**

* [Azure HDInsight 上的 Apache Hive 和 HiveQL 是什麼？](../hadoop/hdinsight-use-hive.md)
* [搭配 MapReduce 與 HDInsight 上的 Apache Hadoop](../hadoop/hdinsight-use-mapreduce.md)

**Apache HBase on HDInsight**

* [使用 Azure HDInsight 中的 Apache HBase](../hbase/apache-hbase-tutorial-get-started-linux.md)
