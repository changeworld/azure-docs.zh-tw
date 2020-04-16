---
title: 搭配 HDInsight 的 Hadoop 上的 MapReduce 使用 C# - Azure
description: 了解如何搭配 Azure HDInsight 的 Apache Hadoop 使用 C# 以建立 MapReduce 解決方案。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 04/15/2020
ms.openlocfilehash: 7a299ce16f6e9c7292cebf198c9c3077f8e05fcb
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/16/2020
ms.locfileid: "81417599"
---
# <a name="use-c-with-mapreduce-streaming-on-apache-hadoop-in-hdinsight"></a>搭配 HDInsight 的 Apache Hadoop 上的 MapReduce 串流使用 C#

了解如何使用 C# 在 HDInsight 上建立 MapReduce 方案。

Apache Hadoop 串流允許您使用文稿或可執行檔案執行 MapReduce 作業。 此處,.NET用於實現字數解決方案的映射器和減少器。

## <a name="net-on-hdinsight"></a>HDInsight 上的 .NET

HDInsight 叢集使用[單https://mono-project.com)聲道 (](https://mono-project.com)運行 .NET 應用程式)。 4.2.1 版的 Mono 隨附於 3.6 版的 HDInsight。 有關 HDInsight 附帶的 Mono 版本的詳細資訊,請參閱[Apache Hadoop 元件,這些元件可提供不同的 HDInsight 版本](../hdinsight-component-versioning.md#apache-hadoop-components-available-with-different-hdinsight-versions)。

如需 Mono 與 .NET Framework 版本之相容性的詳細資訊，請參閱 [Mono 相容性](https://www.mono-project.com/docs/about-mono/compatibility/) \(英文\)。

## <a name="how-hadoop-streaming-works"></a>Hadoop 串流的運作方式

本文件中使用於串流的基本程序如下︰

1. Hadoop 將資料傳遞給 STDIN 上的映射器(本例中的*映射器.exe)。*
2. 對應工具會處理資料，然後發出以 Tab 分隔的機碼值組到 STDOUT。
3. 該輸出由 Hadoop 讀取,然後傳遞到 STDIN 上的減速器(本例中*減量器.exe)。*
4. 歸納器會讀取以 Tab 分隔的機碼值組、處理資料，然後在 STDOUT 發出以 Tab 分隔的機碼值組格式結果。
5. Hadoop 會讀取輸出，接著輸出會寫入至輸出目錄。

如需有關串流的詳細資訊，請參閱 [Hadoop 串流](https://hadoop.apache.org/docs/r2.7.1/hadoop-streaming/HadoopStreaming.html) \(英文\)。

## <a name="prerequisites"></a>Prerequisites

* Visual Studio。

* 熟悉如何撰寫和建置以 .NET Framework 4.5 為目標的 C# 程式碼。

* 將 .exe 檔案上傳至叢集的方式。 本文件中的步驟使用 Data Lake Tools for Visual Studio 將檔案上傳至叢集的主要儲存體。

* 如果使用 PowerShell,則需要[Az 模組](https://docs.microsoft.com/powershell/azure/overview)。

* HDInsight 上的 Apache Hadoop 叢集。 請參閱[在 Linux 上開始使用 HDInsight。](../hadoop/apache-hadoop-linux-tutorial-get-started.md)

* 您叢集主要儲存體的 [URI 配置](../hdinsight-hadoop-linux-information.md#URI-and-scheme)。 此方案適用於`wasb://`Azure 儲存、Azure`abfs://`數據湖`adl://`儲存第 2 代或 Azure 數據湖儲存第 1 代。 如果針對 Azure 儲存體或 Data Lake Storage Gen2 已啟用安全傳輸，則 URI 分別會是 `wasbs://` 或 `abfss://`。另請參閱[安全傳輸](../../storage/common/storage-require-secure-transfer.md)。

## <a name="create-the-mapper"></a>建立對應工具

在可視化工作室中,創建名為*映射器*的新 .NET 框架控制台應用程式。 針對此應用程式使用下列程式碼：

```csharp
using System;
using System.Text.RegularExpressions;

namespace mapper
{
    class Program
    {
        static void Main(string[] args)
        {
            string line;
            //Hadoop passes data to the mapper on STDIN
            while((line = Console.ReadLine()) != null)
            {
                // We only want words, so strip out punctuation, numbers, etc.
                var onlyText = Regex.Replace(line, @"\.|;|:|,|[0-9]|'", "");
                // Split at whitespace.
                var words = Regex.Matches(onlyText, @"[\w]+");
                // Loop over the words
                foreach(var word in words)
                {
                    //Emit tab-delimited key/value pairs.
                    //In this case, a word and a count of 1.
                    Console.WriteLine("{0}\t1",word);
                }
            }
        }
    }
}
```

創建應用程式後,請生成它以在專案目錄中生成 */bin/除錯/mapper.exe*檔。

## <a name="create-the-reducer"></a>建立歸納器

在 Visual Studio 中,創建名為*縮減器*的新 .NET 框架主控台應用程式。 針對此應用程式使用下列程式碼：

```csharp
using System;
using System.Collections.Generic;

namespace reducer
{
    class Program
    {
        static void Main(string[] args)
        {
            //Dictionary for holding a count of words
            Dictionary<string, int> words = new Dictionary<string, int>();

            string line;
            //Read from STDIN
            while ((line = Console.ReadLine()) != null)
            {
                // Data from Hadoop is tab-delimited key/value pairs
                var sArr = line.Split('\t');
                // Get the word
                string word = sArr[0];
                // Get the count
                int count = Convert.ToInt32(sArr[1]);

                //Do we already have a count for the word?
                if(words.ContainsKey(word))
                {
                    //If so, increment the count
                    words[word] += count;
                } else
                {
                    //Add the key to the collection
                    words.Add(word, count);
                }
            }
            //Finally, emit each word and count
            foreach (var word in words)
            {
                //Emit tab-delimited key/value pairs.
                //In this case, a word and a count of 1.
                Console.WriteLine("{0}\t{1}", word.Key, word.Value);
            }
        }
    }
}
```

創建應用程式後,請生成它以在專案目錄中生成 */bin/除錯/縮減器.exe*檔。

## <a name="upload-to-storage"></a>上傳至儲存體

接下來,您需要將*映射器*和*縮減器*應用程式上載到 HDInsight 儲存。

1. 在可視化工作室中,選擇 **「查看** > **伺服器資源管理器**」。。

1. 右鍵按一下**Azure**,選擇「**連接到 Microsoft Azure 訂閱...",** 然後完成登錄過程。

1. 展開您要部署此應用程式的 HDInsight 叢集。 就會列出含有文字 **(預設儲存體帳戶)** 的項目。

   ![儲存帳戶、HDInsight 群集、伺服器資源管理員、可視化工作室](./media/apache-hadoop-dotnet-csharp-mapreduce-streaming/hdinsight-storage-account.png)

   * 如果可以展開 **(預設儲存帳戶)** 條目,則使用**Azure 存儲帳戶**作為群集的預設存儲。 要檢視叢集預設儲存上的檔案,請展開條目,然後按兩下 **(預設容器)。**

   * 如果無法展開 **(預設儲存帳戶)** 條目,則使用**Azure 數據湖儲存**作為群集的預設存儲。 若要檢視叢集之預設儲存體上的檔案，請按兩下 [(預設儲存體帳戶)]**** 項目。

1. 若要上傳 .exe 檔案，請使用下列其中一種方法：

    * 如果您使用的是 Azure**儲存帳戶**,請選擇 **「上傳 Blob」** 圖示。

        ![高清洞察上傳圖示為地圖,視覺工作室](./media/apache-hadoop-dotnet-csharp-mapreduce-streaming/hdinsight-upload-icon.png)

        在 **「上載新檔案**」對話框中,在 **「檔名稱**」下,選擇 **「瀏覽**」。 在 **"上傳 Blob"** 對話方塊中,轉到*地圖器*專案的*bin_除錯*資料夾,然後選擇*mapper.exe*檔。 最後,選擇 **「打開****」,然後確定**以完成上傳。

    * 對於**Azure 資料湖儲存**,右鍵按一下檔案清單中的空區域,然後選擇「**上傳**」。 最後,選擇*映射器.exe*檔,然後選擇 **「打開**」。

    *mapper.exe* 上傳完成後，請針對 *reducer.exe* 檔案重複上傳程序。

## <a name="run-a-job-using-an-ssh-session"></a>執行工作︰使用 SSH 工作階段

以下過程介紹如何使用 SSH 工作階段執行 MapReduce 作業:

1. 使用[ssh 命令](../hdinsight-hadoop-linux-use-ssh-unix.md)連接到群集。 以將 CLUSTERNAME 取代為群組的名稱來編輯下面的命令,然後輸入以下指令:

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. 使用下列其中一個命令來啟動 MapReduce 作業：

   * 如果預設儲存**是 Azure 儲存**:

        ```bash
        yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-streaming.jar \
            -files wasbs:///mapper.exe,wasbs:///reducer.exe \
            -mapper mapper.exe \
            -reducer reducer.exe \
            -input /example/data/gutenberg/davinci.txt \
            -output /example/wordcountout
        ```

    * 如果預設儲存**資料儲存第一代**:

        ```bash
        yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-streaming.jar \
            -files adl:///mapper.exe,adl:///reducer.exe \
            -mapper mapper.exe \
            -reducer reducer.exe \
            -input /example/data/gutenberg/davinci.txt \
            -output /example/wordcountout
        ```

   * 如果預設儲存**為資料儲存儲存第 2 代**:

        ```bash
        yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-streaming.jar \
            -files abfs:///mapper.exe,abfs:///reducer.exe \
            -mapper mapper.exe \
            -reducer reducer.exe \
            -input /example/data/gutenberg/davinci.txt \
            -output /example/wordcountout
        ```

   下面的清單描述了每個參數與選項表示的內容:

   |參數 | 描述 |
   |---|---|
   |hadoop-streaming.jar|指定包含流式處理 MapReduce 功能的 jar 檔。|
   |-檔案|為此作業指定*mapper.exe*和*減量器.exe*檔。 每個`wasbs:///``adl:///`檔`abfs:///`之前的 、或協定聲明是群集預設存儲根目錄的路徑。|
   |-對應器|指定實現映射器的檔。|
   |-減速器|指定實現減分器的檔。|
   |-輸入|指定輸入數據。|
   |-輸出|指定輸出目錄。|

1. MapReduce 工作完成後,使用以下指令檢視結果:

   ```bash
   hdfs dfs -text /example/wordcountout/part-00000
   ```

   下列文字是此命令所傳回資料的範例︰

   ```output
   you     1128
   young   38
   younger 1
   youngest        1
   your    338
   yours   4
   yourself        34
   yourselves      3
   youth   17
   ```

## <a name="run-a-job-using-powershell"></a>執行作業：使用 PowerShell

使用下列 PowerShell 指令碼來執行 MapReduce 作業並下載結果。

[!code-powershell[main](../../../powershell_scripts/hdinsight/use-csharp-mapreduce/use-csharp-mapreduce.ps1?range=5-87)]

此指令碼會提示您輸入叢集登入帳戶名稱和密碼，以及 HDInsight 叢集名稱。 作業完成後,輸出將下載到名為*輸出.txt*的檔。 以下文字是 `output.txt` 檔案中的資料範例：

```output
you     1128
young   38
younger 1
youngest        1
your    338
yours   4
yourself        34
yourselves      3
youth   17
```

## <a name="next-steps"></a>後續步驟

有關使用 MapReduce 與 HDInsight 的詳細資訊,請參閱[在 HDInsight 上使用 Apache Hadoop 中的地圖縮減](hdinsight-use-mapreduce.md)。

如需有關使用 C# 搭配 Hive 和 Pig 的詳細資訊，請參閱[使用 C# 使用者定義函式搭配 Apache Hive 和 Apache Pig](apache-hadoop-hive-pig-udf-dotnet-csharp.md)。

如需搭配 HDInsight 上的 Storm 使用 C# 的詳細資訊，請參閱[開發適用於 Apache Storm on HDInsight 的 C# 拓撲](../storm/apache-storm-develop-csharp-visual-studio-topology.md)。
