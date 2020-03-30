---
title: 使用 Python 元件的阿帕奇風暴 - Azure HDInsight
description: 瞭解如何在 Azure HDInsight 中使用 Python 元件創建 Apache Storm 拓撲
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive,hdiseo17may2017
ms.date: 12/16/2019
ms.openlocfilehash: 20e4827b1a86bff338646ef71f0dd732255c09c9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "77460019"
---
# <a name="develop-apache-storm-topologies-using-python-on-hdinsight"></a>在 HDInsight 上使用 Python 開發 Apache Storm 拓撲

了解如何建立使用 Python 元件的 [Apache Storm](https://storm.apache.org/) \(英文\) 拓撲。 Apache Storm 支援多種語言，甚至可讓您將數種語言的元件結合成一個拓撲。 [Flux](https://storm.apache.org/releases/current/flux.html) \(英文\) 架構 (隨 Storm 0.10.0 一起引進) 可讓您輕鬆建立使用 Python 元件的解決方案。

> [!IMPORTANT]  
> 本文件中的資訊已使用 Storm on HDInsight 3.6 進行測試。

## <a name="prerequisites"></a>Prerequisites

* HDInsight 上的 Apache Storm 叢集。 請參閱[使用 Azure 入口網站建立 Apache Hadoop 叢集](../hdinsight-hadoop-create-linux-clusters-portal.md)，然後選取 [Storm]**** 作為 [叢集類型]****。

* 本地風暴開發環境（可選）。 只有當您想要在本機執行拓撲時，才需要本機 Storm 環境。 如需詳細資訊，請參閱[設定開發環境](https://storm.apache.org/releases/current/Setting-up-development-environment.html)。

* [Python 2.7 或更高](https://www.python.org/downloads/)。

* [JAVA 開發人員工具組 （JDK） 版本 8](https://aka.ms/azure-jdks).

* 根據 Apache 正確[安裝](https://maven.apache.org/install.html)的 [Apache Maven](https://maven.apache.org/download.cgi)。  Maven 是適用於 Java 專案的專案建置系統。

## <a name="storm-multi-language-support"></a>Storm 多語言支援

Apache Storm 專門用來搭配以任何程式設計語言撰寫的元件。 這些元件必須了解如何使用 Storm 的 Thrift 定義。 在 Python 中，Apache Storm 專案隨附一個模組，可讓您輕鬆地與 Strom 互動。 您可以在 中找到此模組。 [https://github.com/apache/storm/blob/master/storm-multilang/python/src/main/resources/resources/storm.py](https://github.com/apache/storm/blob/master/storm-multilang/python/src/main/resources/resources/storm.py)

Storm 是在 Java 虛擬機器 (JVM) 上執行的 Java 程序。 以其他語言撰寫的元件會以子流程執行。 Storm 會使用透過 stdin/stdout 傳送的 JSON 訊息，與這些子流程進行通訊。 如需各元件之間通訊的詳細資訊，請參閱 [多語言通訊協定](https://storm.apache.org/releases/current/Multilang-protocol.html) 文件。

## <a name="python-with-the-flux-framework"></a>採用 Flux 架構的 Python

Flux 架構可讓您獨立於元件之外定義 Storm 拓撲。 Flux 架構會使用 YAML 來定義 Storm 拓撲。 下列文字是如何在 YAML 文件中參考 Python 元件的範例：

```yaml
# Spout definitions
spouts:
  - id: "sentence-spout"
    className: "org.apache.storm.flux.wrappers.spouts.FluxShellSpout"
    constructorArgs:
      # Command line
      - ["python", "sentencespout.py"]
      # Output field(s)
      - ["sentence"]
    # parallelism hint
    parallelism: 1
```

類別 `FluxShellSpout` 用來啟動實作 Spout 的 `sentencespout.py` 指令碼。

Flux 要求 Python 指令碼位於拓撲所在之 jar 檔案內的 `/resources` 目錄。 因此，這個範例會將 Python 指令碼儲存在 `/multilang/resources` 目錄。 `pom.xml` 使用下列 XML 來包含此檔案：

```xml
<!-- include the Python components -->
<resource>
    <directory>${basedir}/multilang</directory>
    <filtering>false</filtering>
</resource>
```

如前所述，有一個`storm.py`檔實現了 Storm 的節儉定義。 建置專案時，Flux 架構會自動包含 `storm.py`，因此您不必擔心要包含它。

## <a name="build-the-project"></a>建置專案

1. 從[https://github.com/Azure-Samples/hdinsight-python-storm-wordcount](https://github.com/Azure-Samples/hdinsight-python-storm-wordcount)下載專案。

1. 打開命令提示符並導航到專案根： `hdinsight-python-storm-wordcount-master`。 輸入下列命令：

    ```cmd
    mvn clean compile package
    ```

    此命令會建立 `target/WordCount-1.0-SNAPSHOT.jar` 檔案，其中包含已編譯的拓撲。

## <a name="run-the-storm-topology-on-hdinsight"></a>在 HDInsight 上執行 Storm 拓撲

1. 使用[ssh 命令](../hdinsight-hadoop-linux-use-ssh-unix.md)將`WordCount-1.0-SNAPSHOT.jar`檔案複製到 HDInsight 群集上的 Storm。 通過將 CLUSTERNAME 替換為群集的名稱來編輯下面的命令，然後輸入以下命令：

    ```cmd
    scp target/WordCount-1.0-SNAPSHOT.jar sshuser@CLUSTERNAME-ssh.azurehdinsight.net:
    ```

1. 上傳檔案後，使用 SSH 連線至叢集：

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. 從 SSH 工作階段中，使用下列命令啟動叢集上的拓撲：

    ```bash
    storm jar WordCount-1.0-SNAPSHOT.jar org.apache.storm.flux.Flux -r -R /topology.yaml
    ```

    Storm 拓撲啟動之後會一直執行到停止為止。

1. 使用 Storm UI 查看群集上的拓撲。 Storm UI 位於 `https://CLUSTERNAME.azurehdinsight.net/stormui`。 將 `CLUSTERNAME` 取代為您的叢集名稱。

1. 停止風暴拓撲。 使用以下命令停止群集上的拓撲：

    ```bash
    storm kill wordcount
    ```

    或者，您可以使用 Storm UI。 在拓撲的**拓撲操作**下，選擇 **"終止**"。

## <a name="run-the-topology-locally"></a>在本機測試拓撲

若要在本機執行拓撲，請使用下列命令：

```bash
storm jar WordCount-1.0-SNAPSHOT.jar org.apache.storm.flux.Flux -l -R /topology.yaml
```

> [!NOTE]  
> 此命令需要本機 Storm 開發環境。 如需詳細資訊，請參閱[設定開發環境](https://storm.apache.org/releases/current/Setting-up-development-environment.html)。

拓撲啟動之後，就會將類似下列文字的資訊發出至本機主控台︰

```output
24302 [Thread-25-sentence-spout-executor[4 4]] INFO  o.a.s.s.ShellSpout - ShellLog pid:2436, name:sentence-spout Emiting the cow jumped over the moon
24302 [Thread-30] INFO  o.a.s.t.ShellBolt - ShellLog pid:2438, name:splitter-bolt Emitting the
24302 [Thread-28] INFO  o.a.s.t.ShellBolt - ShellLog pid:2437, name:counter-bolt Emitting years:160
24302 [Thread-17-log-executor[3 3]] INFO  o.a.s.f.w.b.LogInfoBolt - {word=the, count=599}
24303 [Thread-17-log-executor[3 3]] INFO  o.a.s.f.w.b.LogInfoBolt - {word=seven, count=302}
24303 [Thread-17-log-executor[3 3]] INFO  o.a.s.f.w.b.LogInfoBolt - {word=dwarfs, count=143}
24303 [Thread-25-sentence-spout-executor[4 4]] INFO  o.a.s.s.ShellSpout - ShellLog pid:2436, name:sentence-spout Emiting the cow jumped over the moon
24303 [Thread-30] INFO  o.a.s.t.ShellBolt - ShellLog pid:2438, name:splitter-bolt Emitting cow
24303 [Thread-17-log-executor[3 3]] INFO  o.a.s.f.w.b.LogInfoBolt - {word=four, count=160}
```

若要停止拓撲，請使用 __Ctrl+C__。

## <a name="next-steps"></a>後續步驟

有關將 Python 與 HDInsight 一起使用的其他方法，請參閱以下文檔：[如何在 Apache Pig 和 Apache Hive 中使用 Python 使用者定義函數 （UDF）。](../hadoop/python-udf-hdinsight.md)
