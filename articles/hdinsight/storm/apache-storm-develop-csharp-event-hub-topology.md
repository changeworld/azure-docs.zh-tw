---
title: 在事件中樞內使用 Storm 來處理事件 - Azure HDInsight
description: 了解如何利用在 Visual Studio 中使用 HDInsight Tools for Visual Studio 所建立之 C# Storm 拓撲來處理 Azure 事件中樞的資料。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: how-to
ms.date: 01/14/2020
ms.custom: devx-track-csharp
ms.openlocfilehash: cb1c2d8daa74d1224ad07ef7a2fb5a74f4773338
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/27/2020
ms.locfileid: "89000306"
---
# <a name="process-events-from-azure-event-hubs-with-apache-storm-on-hdinsight-c"></a>使用 HDInsight 上的 Apache Storm 處理 Azure 事件中樞的事件 (C#)

了解如何從 HDInsight 上的 [Apache Storm](https://storm.apache.org/) 使用 Azure 事件中樞。 本文件使用 C# Storm 拓撲從事件中樞讀取和寫入資料

> [!NOTE]  
> 如需本專案的 Java 版本，請參閱[使用 HDInsight 上的 Apache Storm 處理 Azure 事件中樞的事件 (Java)](https://github.com/Azure-Samples/hdinsight-java-storm-eventhub)。

## <a name="scpnet"></a>SCP.NET

本文件中的步驟使用 SCP.NET，其為 NuGet 套件，可方便您建立 C# 拓撲和元件來與 Storm on HDInsight 搭配使用。

HDInsight 3.4 和更高版本使用單聲道來執行 C# 拓撲。 本文件中使用的範例會使用 HDInsight 3.6。 如果您打算針對 HDInsight 來建立您自己的 .NET 解決方案，請參閱 [Mono 相容性](https://www.mono-project.com/docs/about-mono/compatibility/)文件，以了解可能不相容之處。

### <a name="cluster-versioning"></a>叢集版本控制

用於專案的 Microsoft.SCP.Net.SDK NuGet 套件必須符合安裝在 HDInsight 上的 Storm 主要版本。 HDInsight 3.5 版及 3.6 版使用 Storm 1.x 版，因此您必須搭配使用 SCP.NET 1.0.x.x 版與這些叢集。

C# 拓撲也必須以 .NET 4.5 為目標。

## <a name="how-to-work-with-event-hubs"></a>如何使用事件中樞

Microsoft 提供一組可用來從 Storm 拓撲與事件中樞通訊的 Java 元件。 您可以在中找到包含這些元件的 HDInsight 3.6 相容版本的 JAVA 封存 (JAR) 檔案 [https://github.com/hdinsight/mvn-repo/raw/master/org/apache/storm/storm-eventhubs/1.1.0.1/storm-eventhubs-1.1.0.1.jar](https://github.com/hdinsight/mvn-repo/raw/master/org/apache/storm/storm-eventhubs/1.1.0.1/storm-eventhubs-1.1.0.1.jar) 。

> [!IMPORTANT]  
> 雖然元件是以 Java 所撰寫，您可以輕鬆地從 C# 拓撲使用它們。

在此範例中會使用下列元件：

* __EventHubSpout__：從事件中樞讀取資料。
* __EventHubBolt__：將資料寫入事件中樞。
* __EventHubSpoutConfig__：用來設定 EventHubSpout。
* __EventHubBoltConfig__：用來設定 EventHubBolt。

### <a name="example-spout-usage"></a>範例 Spout 使用方式

SCP.NET 會提供將 EventHubSpout 新增至您拓撲的方法。 這些方法比使用泛型方法新增 Java 元件更容易新增 Spout。 下列範例示範如何使用 SCP.NET 所提供的 __SetEventHubSpout__ 和 **EventHubSpoutConfig** 方法建立 Spout：

```csharp
 topologyBuilder.SetEventHubSpout(
    "EventHubSpout",
    new EventHubSpoutConfig(
        ConfigurationManager.AppSettings["EventHubSharedAccessKeyName"],
        ConfigurationManager.AppSettings["EventHubSharedAccessKey"],
        ConfigurationManager.AppSettings["EventHubNamespace"],
        ConfigurationManager.AppSettings["EventHubEntityPath"],
        eventHubPartitions),
    eventHubPartitions);
```

先前的範例會建立名為 __EventHubSpout__的新 spout 元件，並將它設定為與事件中樞通訊。 元件的平行處理原則提示設定為事件中樞的資料分割數目。 此設定可讓 Storm 針對每個資料分割建立元件執行個體。

### <a name="example-bolt-usage"></a>範例 Bolt 使用方式

使用 **JAVAComponmentConstructor** 方法來建立螺栓的實例。 下列範例示範如何建立和設定 **EventHubBolt**的新實例：

```csharp
// Java construcvtor for the Event Hub Bolt
JavaComponentConstructor constructor = JavaComponentConstructor.CreateFromClojureExpr(
    String.Format(@"(org.apache.storm.eventhubs.bolt.EventHubBolt. (org.apache.storm.eventhubs.bolt.EventHubBoltConfig. " +
        @"""{0}"" ""{1}"" ""{2}"" ""{3}"" ""{4}"" {5}))",
        ConfigurationManager.AppSettings["EventHubPolicyName"],
        ConfigurationManager.AppSettings["EventHubPolicyKey"],
        ConfigurationManager.AppSettings["EventHubNamespace"],
        "servicebus.windows.net",
        ConfigurationManager.AppSettings["EventHubName"],
        "true"));

// Set the bolt to subscribe to data from the spout
topologyBuilder.SetJavaBolt(
    "eventhubbolt",
    constructor,
    partitionCount)
        .shuffleGrouping("Spout");
```

> [!NOTE]  
> 這個範例會使用以字串傳遞的 Clojure 運算式，而不是如 Spout 範例一樣使用 **JavaComponentConstructor** 來建立 **EventHubBoltConfig**。 兩種方法均可。 使用覺得較適合您的方法。

## <a name="download-the-completed-project"></a>下載完成的專案

您可以從 [GitHub](https://github.com/Azure-Samples/hdinsight-dotnet-java-storm-eventhub)下載本文中所建立之專案的完整版本。 不過，您仍然必須遵循本文中的步驟來提供設定設定。

### <a name="prerequisites"></a>先決條件

* HDInsight 上的 Apache Storm 叢集。 請參閱[使用 Azure 入口網站建立 Apache Hadoop 叢集](../hdinsight-hadoop-create-linux-clusters-portal.md)，然後選取 [Storm]**** 作為 [叢集類型]****。

* [Azure 事件中樞](../../event-hubs/event-hubs-create.md)。

* [Azure .NET SDK](https://azure.microsoft.com/downloads/)。

* [適用于 Visual Studio 的 HDInsight 工具](../hadoop/apache-hadoop-visual-studio-tools-get-started.md)。

* 開發環境有 Java JDK 1.8 或更新版本。 JDK 下載檔可從 [Oracle](https://aka.ms/azure-jdks) 取得。

  * **JAVA_HOME** 環境變數必須指向包含 Java 的目錄。
  * **% JAVA_HOME%/bin**目錄必須在路徑中。

## <a name="download-the-event-hubs-components"></a>下載事件中樞元件

從下載事件中樞 spout 和螺栓元件 [https://github.com/hdinsight/mvn-repo/raw/master/org/apache/storm/storm-eventhubs/1.1.0.1/storm-eventhubs-1.1.0.1.jar](https://github.com/hdinsight/mvn-repo/raw/master/org/apache/storm/storm-eventhubs/1.1.0.1/storm-eventhubs-1.1.0.1.jar) 。

請建立名為 `eventhubspout` 的目錄，並將檔案儲存到該目錄。

## <a name="configure-event-hubs"></a>設定事件中樞

事件中樞是此範例的資料來源。 請使用[開始使用事件中樞](../../event-hubs/event-hubs-create.md)的＜建立事件中樞＞一節中的資訊。

1. 在建立事件中樞之後，檢視 Azure 入口網站中的 [事件中樞]**** 設定，然後選取 [共用存取原則]****。 選取 [ **+ 新增** ] 來建立下列原則：

   | 名稱 | 權限 |
   | --- | --- |
   | 寫入器 |Send |
   | 讀取器 |接聽 |

    ![[共用存取原則] 視窗的螢幕擷取畫面](./media/apache-storm-develop-csharp-event-hub-topology/share-access-policies.png)

2. 選取 [讀取器]**** 和 [寫入器]**** 原則。 複製並儲存這兩個原則的主索引鍵值，因為稍後將使用這些值。

## <a name="configure-the-eventhubwriter"></a>設定 EventHubWriter

1. 如果您尚未安裝最新版的 HDInsight tools for Visual Studio，請參閱 [開始使用 hdinsight tools for Visual Studio](../hadoop/apache-hadoop-visual-studio-tools-get-started.md)。

2. 從 [eventhub-storm-hybrid](https://github.com/Azure-Samples/hdinsight-dotnet-java-storm-eventhub) 下載方案。

3. 開啟 **EventHubExample .sln**。 在 **EventHubWriter** 專案中，開啟 **App.config** 檔案。 使用事件中樞內您稍早設定的資訊填入下列索引鍵的值：

   | Key | 值 |
   | --- | --- |
   | EventHubPolicyName |寫入器 (如果您為具有*傳送*權限的原則使用了不同名稱，請改用該名稱。) |
   | EventHubPolicyKey |寫入器原則的索引鍵。 |
   | EventHubNamespace |包含事件中樞的命名空間。 |
   | EventHubName |事件中樞名稱。 |
   | EventHubPartitionCount |事件中樞內的資料分割數目。 |

4. 儲存並關閉 **App.config** 檔案。

## <a name="configure-the-eventhubreader"></a>設定 EventHubReader

1. 開啟 **EventHubReader** 專案。

2. 開啟 **EventHubReader** 的 **App.config** 檔案。 使用事件中樞內您稍早設定的資訊填入下列索引鍵的值：

   | Key | 值 |
   | --- | --- |
   | EventHubPolicyName |讀取器 (如果您為具有*接聽*權限的原則使用了不同名稱，請改用該名稱。) |
   | EventHubPolicyKey |讀取器原則的索引鍵。 |
   | EventHubNamespace |包含事件中樞的命名空間。 |
   | EventHubName |事件中樞名稱。 |
   | EventHubPartitionCount |事件中樞內的資料分割數目。 |

3. 儲存並關閉 **App.config** 檔案。

## <a name="deploy-the-topologies"></a>部署拓撲

1. 在 **方案總管**中，以滑鼠右鍵按一下 **EventHubReader** 專案，然後選取 [ **在 HDInsight 上提交到風暴**]。

    ![[方案總管] 的螢幕擷取畫面，已反白顯示 [提交到 Storm on HDInsight]](./media/apache-storm-develop-csharp-event-hub-topology/submit-to-apache-storm.png)

2. 在 [提交拓撲]**** 對話方塊中，選取您的 [Storm 叢集]****。 展開 [其他設定]****，依序選取 [Java 檔案路徑]****、[...]****，然後選取包含您稍早下載之 JAR 檔的目錄。 最後，按一下 [提交]****。

    ![[提交拓撲] 對話方塊的螢幕擷取畫面](./media/apache-storm-develop-csharp-event-hub-topology/submit-storm-topology.png)

3. 提交拓撲之後，[Storm 拓撲檢視器]**** 便會隨即出現。 若要檢視拓撲的相關資訊，請選取左窗格中的 **EventHubReader** 拓撲。

    ![Storm 拓撲檢視器的螢幕擷取畫面](./media/apache-storm-develop-csharp-event-hub-topology/storm-topology-viewer.png)

4. 在 **方案總管**中，以滑鼠右鍵按一下 **EventHubWriter** 專案，然後選取 [ **在 HDInsight 上提交到風暴**]。

5. 在 [提交拓撲]**** 對話方塊中，選取您的 [Storm 叢集]****。 展開 [ **其他**設定]，選取 [ **JAVA 檔案路徑**]，選取 [ **...**]，然後選取包含您稍早下載之 JAR 檔案的目錄。 最後，按一下 [提交]****。

6. 提交拓撲之後，請重新整理 [Storm 拓撲檢視器] **** 中的拓撲清單，以確認兩個拓撲皆在叢集上執行。

7. 在 [Storm 拓撲檢視器]**** 中，選取 [EventHubReader]**** 拓撲。

8. 若要開啟螺栓的元件摘要，請按兩下圖表中的 **>logbolt** 元件。

9. 在 [執行程式]**** 區段中，選取 [連接埠]**** 資料行內的其中一個連結。 這會顯示該元件記錄的資訊。 所記錄的資訊類似下列文字︰

    ```output
    2017-03-02 14:51:29.255 m.s.p.TaskHost [INFO] Received C# STDOUT: 2017-03-02 14:51:29,255 [1] INFO  EventHubReader_LogBolt [(null)] - Received data: {"deviceValue":1830978598,"deviceId":"8566ccbc-034d-45db-883d-d8a31f34068e"}
    2017-03-02 14:51:29.283 m.s.p.TaskHost [INFO] Received C# STDOUT: 2017-03-02 14:51:29,283 [1] INFO  EventHubReader_LogBolt [(null)] - Received data: {"deviceValue":1756413275,"deviceId":"647a5eff-823d-482f-a8b4-b95b35ae570b"}
    2017-03-02 14:51:29.313 m.s.p.TaskHost [INFO] Received C# STDOUT: 2017-03-02 14:51:29,312 [1] INFO  EventHubReader_LogBolt [(null)] - Received data: {"deviceValue":1108478910,"deviceId":"206a68fa-8264-4d61-9100-bfdb68ee8f0a"}
    ```

## <a name="stop-the-topologies"></a>停止拓撲

若要停止拓撲，請在 [Storm 拓撲檢視器]**** 中選取每個拓撲，然後選取 [終止]****。

![Storm 拓撲檢視器的螢幕擷取畫面，已反白顯示 [終止] 按鈕](./media/apache-storm-develop-csharp-event-hub-topology/kill-storm-topology1.png)

## <a name="delete-your-cluster"></a>刪除叢集

[!INCLUDE [delete-cluster-warning](../../../includes/hdinsight-delete-cluster-warning.md)]

## <a name="next-steps"></a>後續步驟

在本檔中，您已瞭解如何從 c # 拓撲使用 JAVA 事件中樞 spout 和螺栓來處理 Azure 事件中樞中的資料。 若要深入了解如何建立 C# 拓撲，請參閱下列內容：

* [使用 Visual Studio 開發 Apache Storm on HDInsight 的 C# 拓撲](apache-storm-develop-csharp-visual-studio-topology.md)
* [SCP 程式設計指南](apache-storm-scp-programming-guide.md)
* [Apache Storm on HDInsight 的範例拓撲](apache-storm-example-topology.md)
